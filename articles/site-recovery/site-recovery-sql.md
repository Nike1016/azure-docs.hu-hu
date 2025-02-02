---
title: Vész-helyreállítás beállítása SQL Serverhoz SQL Server és Azure Site Recovery esetén | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan állíthat be SQL Server vész-helyreállítást a SQL Server és a Azure Site Recovery használatával.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/02/2019
ms.author: sutalasi
ms.openlocfilehash: 14fbca6dea735ed1ee13fca20f19379cc2c4d0a9
ms.sourcegitcommit: d060947aae93728169b035fd54beef044dbe9480
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2019
ms.locfileid: "68742326"
---
# <a name="set-up-disaster-recovery-for-sql-server"></a>Vész-helyreállítás beállítása SQL Serverhoz

Ez a cikk azt ismerteti, hogyan lehet védelemmel ellátni az alkalmazások SQL Server hátterét. Ezt a SQL Server üzletmenet-folytonossági és vész-helyreállítási (BCDR) technológiák és a [Azure site Recovery](site-recovery-overview.md)együttes használatával teheti meg.

Mielőtt elkezdené, győződjön meg róla, hogy megértette SQL Server vész-helyreállítási képességeit. Ezek a képességek a következők:

* Feladatátvételi fürtszolgáltatás
* Always On rendelkezésre állási csoportok
* Adatbázis-tükrözés
* Naplóküldés
* Aktív georeplikáció
* Automatikus feladatátvételi csoportok

## <a name="combining-bcdr-technologies-with-site-recovery"></a>BCDR-technológiák kombinálása a Site Recovery

A SQL Server példányok helyreállításához választott BCDR-technológiának a helyreállítási idő célkitűzésének (RTO) és a helyreállítási pont célkitűzésének (RPO) a következő táblázatban leírtaknak megfelelően kell alapulnia. A Site Recovery és a választott technológia feladatátvételi műveletével egyesítheti a teljes alkalmazás helyreállítását.

Üzemelő példány típusa | BCDR technológia | SQL Server várt RTO | SQL Server várt RPO |
--- | --- | --- | ---
SQL Server egy Azure-beli infrastruktúra-(IaaS-) virtuális gépen (VM) vagy a helyszínen.| [Always On rendelkezésre állási csoport](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server?view=sql-server-2017) | A másodlagos replika elsődlegesként való elvégzéséhez szükséges idő. | Mivel a másodlagos replikára történő replikáció aszinkron, némi adatvesztés történik.
SQL Server egy Azure IaaS virtuális gépen vagy a helyszínen.| [Feladatátvételi fürtszolgáltatás (always on ()](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server?view=sql-server-2017) | A csomópontok közötti feladatátvételhez szükséges idő. | Mivel a always on a megosztott tárolót használ, a tárolási példány ugyanazon nézete elérhető a feladatátvételben.
SQL Server egy Azure IaaS virtuális gépen vagy a helyszínen.| [Adatbázis-tükrözés (nagy teljesítményű mód)](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server?view=sql-server-2017) | A szolgáltatás kényszerítéséhez szükséges idő, amely a tükrözött kiszolgálót használja meleg készenléti kiszolgálóként. | A replikálás aszinkron módon történik. Előfordulhat, hogy a tükrözési adatbázis némileg elmarad az elsődleges adatbázis mögött. A késés általában kicsi. Azonban akkor is nagy lehet, ha a rendszerbiztonsági tag vagy a tükrözött kiszolgáló rendszere nagy terhelés alatt áll.<br/><br/>A napló szállítása az adatbázis-tükrözés kiegészítéseként lehet. Ez az aszinkron adatbázis-tükrözés számára kedvező alternatíva.
Az SQL as platform szolgáltatásként (Péter) az Azure-ban.<br/><br/>Ez a központi telepítési típus rugalmas készleteket és Azure SQL Database-kiszolgálókat tartalmaz. | Aktív georeplikáció | a feladatátvétel elindítása után 30 másodperc.<br/><br/>Ha a feladatátvétel aktiválva van valamelyik másodlagos adatbázis esetében, az összes többi formátumú másodlagos zónák automatikusan az új elsődlegeshez lesz kapcsolva. | RPO öt másodperc.<br/><br/>Az aktív geo-replikáció a SQL Server mindig technológiáját használja. Aszinkron módon replikálja a véglegesített tranzakciókat az elsődleges adatbázison egy másodlagos adatbázisba a pillanatkép-elkülönítés használatával.<br/><br/>A másodlagos adatmennyiség garantáltan soha nem rendelkezhet részleges tranzakciókkal.
Az SQL as Pásti aktív geo-replikációval van konfigurálva az Azure-ban.<br/><br/>Ez a központi telepítési típus SQL Database felügyelt példányt, rugalmas készleteket és SQL Database kiszolgálókat tartalmaz. | Automatikus feladatátvételi csoportok | RTO egy óra. | RPO öt másodperc.<br/><br/>Az automatikus feladatátvételi csoportok az aktív geo-replikáción alapuló csoportos szemantikai feladatokat biztosítják. Azonban ugyanazt az aszinkron replikációs mechanizmust használja a rendszer.
SQL Server egy Azure IaaS virtuális gépen vagy a helyszínen.| Replikálás Azure Site Recovery | A RTO jellemzően 15 percnél rövidebb. További információért olvassa el a [site Recovery által biztosított RTO SLA](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/)-t. | Egy óra az alkalmazás konzisztenciája érdekében, és öt perc összeomlási konzisztencia esetén. Ha alacsonyabb RPO keres, használjon más BCDR-technológiákat.

> [!NOTE]
> Néhány fontos szempontot figyelembe kell venni, ha az SQL-munkaterheléseket a Site Recovery segítségével kívánja védetté tenni:
> * Site Recovery az alkalmazás agnosztikus. A Site Recovery a támogatott operációs rendszereken telepített SQL Server bármely verziójának védelmére képes. További információ: a replikált gépek [helyreállításának támogatási mátrixa](vmware-physical-azure-support-matrix.md#replicated-machines) .
> * Az Azure-ban, a Hyper-V-ben, a VMware-ben vagy a fizikai infrastruktúrában üzemelő üzembe helyezéshez Site Recovery is használhat. A jelen cikk végén található útmutatást követve megtudhatja, hogyan biztosítható [SQL Server-fürt](#how-to-help-protect-a-sql-server-cluster) site Recoveryokkal való védelméhez.
> * Győződjön meg arról, hogy a gépen megfigyelt adatváltozási arány [site Recovery korláton](vmware-physical-azure-support-matrix.md#churn-limits)belül van. A változási arányt írási bájt/másodpercben mérjük. A Windows rendszert futtató gépek esetében a változási arányt a Feladatkezelő **teljesítmény** lapjára kattintva tekintheti meg. Figyelje meg az egyes lemezek írási sebességét.
> * Site Recovery támogatja a feladatátvevő fürtök példányainak replikálását Közvetlen tárolóhelyekon. További információ: [közvetlen tárolóhelyek replikáció engedélyezése](azure-to-azure-how-to-enable-replication-s2d-vms.md).

## <a name="disaster-recovery-of-an-application"></a>Alkalmazás vész-helyreállítása

Site Recovery összehangolja a feladatátvételi tesztet és a teljes alkalmazás feladatátvételét a helyreállítási tervek segítségével.

Néhány előfeltétel, hogy a helyreállítási terv teljes mértékben testre szabható legyen, igény szerint. A SQL Server üzembe helyezésének általában Active Directory központi telepítésre van szüksége. Emellett kapcsolódnia kell az alkalmazási szintjéhez.

### <a name="step-1-set-up-active-directory"></a>1\. lépés: Active Directory beállítása

Állítsa be a Active Directory a másodlagos helyreállítási helyen, hogy a SQL Server megfelelően fusson.

* **Kis vállalat**: Kis számú alkalmazás és egyetlen tartományvezérlő a helyszíni helyhez. Ha a teljes helyet szeretné felvenni, használja a Site Recovery replikálást. Ez a szolgáltatás replikálja a tartományvezérlőt a másodlagos adatközpontba vagy az Azure-ba.
* **Közepes és nagy vállalat**: Lehetséges, hogy további tartományvezérlőket kell beállítania.
  - Ha nagy számú alkalmazással rendelkezik, Active Directory erdőben kell lennie, és az alkalmazás vagy a munkaterhelés feladatátvételt szeretne végezni, állítson be egy másik tartományvezérlőt a másodlagos adatközpontban vagy az Azure-ban.
  -  Ha always on rendelkezésre állási csoportokat használ egy távoli helyre történő helyreállításhoz, állítson be egy másik tartományvezérlőt a másodlagos helyen vagy az Azure-ban. Ez a tartományvezérlő a helyreállított SQL Server példányhoz használatos.

A cikkben szereplő utasítások feltételezik, hogy a tartományvezérlő elérhető a másodlagos helyen. További információkért tekintse meg a [Active Directory és site Recovery elleni védelem segítésére](site-recovery-active-directory.md)szolgáló eljárásokat.

### <a name="step-2-ensure-connectivity-with-other-tiers"></a>2\. lépés: Más rétegek kapcsolatának biztosítása

Miután az adatbázis-szintet futtatta a cél Azure-régióban, ellenőrizze, hogy van-e kapcsolat az alkalmazással és a webes rétegekkel. Végezze el a szükséges lépéseket a feladatátvételi teszttel való kapcsolat ellenőrzéséhez.

A következő példákban megismerheti, hogyan tervezhet meg alkalmazásokat a kapcsolódási megfontolásokhoz:

* [Alkalmazás tervezése a Felhőbeli vész-helyreállításhoz](../sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery.md)
* [Rugalmas készlet vész-helyreállítási stratégiái](../sql-database/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)

### <a name="step-3-interoperate-with-always-on-active-geo-replication-and-auto-failover-groups"></a>3\. lépés: Együttműködés a mindig bekapcsolt, aktív geo-replikálási és automatikus feladatátvételi csoportokkal

A BCDR Technologies always on, Active geo-Replication és automatikus feladatátvételi csoportok másodlagos replikái SQL Server futnak a cél Azure-régióban. Az alkalmazás feladatátvételének első lépéseként meg kell adnia a replikát elsődlegesként. Ez a lépés azt feltételezi, hogy már rendelkezik tartományvezérlővel a másodlagosban. Előfordulhat, hogy a lépés nem szükséges, ha automatikus feladatátvételt hajt végre. A webes és az alkalmazási rétegek feladatátvétele csak az adatbázis-feladatátvétel befejeződése után végezhető el.

> [!NOTE]
> Ha az SQL-gépeket a Site Recovery segítségével kívánja védelemmel ellátni, csak létre kell hoznia egy helyreállítási csoportot a gépekről, és hozzá kell adnia a feladatátvételt a helyreállítási tervben.

[Helyreállítási terv létrehozása](site-recovery-create-recovery-plans.md) alkalmazás-és webes szintű virtuális gépekkel. Az alábbi lépések bemutatják, hogyan adhat hozzá feladatátvételt az adatbázis szintjéhez:

1. Importálja a parancsfájlokat az SQL rendelkezésre állási csoport feladatátvételére egy [Resource Manager-alapú virtuális gépen](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAG.ps1) és egy [klasszikus virtuális gépen](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAGClassic.ps1). Importálja a parancsfájlokat a Azure Automation-fiókjába.

    [!["Üzembe helyezés az Azure-ban" embléma képe](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Adja hozzá az ASR-SQL-FailoverAG parancsfájlt a helyreállítási terv első csoportjának előzetes műveletéhez.

1. Az Automation-változó létrehozásához kövesse a parancsfájlban található utasításokat. Ez a változó a rendelkezésre állási csoportok nevét adja meg.

### <a name="step-4-conduct-a-test-failover"></a>4\. lépés: Feladatátvételi teszt végrehajtása

Bizonyos BCDR-technológiák, például az SQL always on nem támogatják natív módon a feladatátvételi tesztet. A következő módszert *csak akkor javasoljuk, ha ilyen technológiákat használ*.

1. [Azure Backup](../backup/backup-azure-arm-vms.md) beállítása azon a virtuális gépen, amelyen a rendelkezésre állási csoport replikája fut az Azure-ban.

1. Mielőtt elindítja a helyreállítási terv feladatátvételi tesztjét, állítsa helyre a virtuális gépet az előző lépésben végrehajtott biztonsági másolatból.

    ![A konfiguráció visszaállítására szolgáló ablak képernyőképe Azure Backup](./media/site-recovery-sql/restore-from-backup.png)

1. [Olyan kvórumot](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/force-a-wsfc-cluster-to-start-without-a-quorum#PowerShellProcedure) kell kényszeríteni a virtuális gépen, amelyet biztonsági másolatból állítottak vissza.

1. Frissítse a figyelő IP-címét, hogy a teszt feladatátvételi hálózaton elérhető Cím legyen.

    ![Képernyőfelvétel a szabályok ablak és az IP-cím tulajdonságai párbeszédpanelről](./media/site-recovery-sql/update-listener-ip.png)

1. A figyelő online állapotba helyezése.

    ![Képernyőkép a Content_AG és az állapotokat megjelenítő ablakról](./media/site-recovery-sql/bring-listener-online.png)

1. Hozzon létre egy Load balancert. Minden rendelkezésre állási csoport figyelője esetében hozzon létre egy IP-címet az előtér-IP-készletből. Adja hozzá a SQL Server VM a háttér-készlethez is.

     ![Az "SQL-AlwaysOn-LB-frontend IP-címkészlet" nevű ablak képernyőképe](./media/site-recovery-sql/create-load-balancer1.png)

    ![Az "SQL-AlwaysOn-LB-backend IP-készlet" nevű ablak képernyőképe](./media/site-recovery-sql/create-load-balancer2.png)

1. A későbbi helyreállítási csoportokban adja hozzá az alkalmazási rétegek feladatátvételét, majd a helyreállítási tervhez tartozó webes szintet.

1. Végezzen feladatátvételi tesztet a helyreállítási tervből az alkalmazás végpontok közötti feladatátvételének teszteléséhez.

## <a name="steps-to-do-a-failover"></a>A feladatátvétel lépései

Miután hozzáadta a szkriptet a 3. lépésben, és érvényesíti azt a 4. lépésben, elvégezheti a 3. lépésben létrehozott helyreállítási terv feladatátvételét.

Az alkalmazás és a webes rétegek feladatátvételi lépéseinek azonosnak kell lenniük a teszt feladatátvételi és feladatátvételi helyreállítási tervekben.

## <a name="how-to-help-protect-a-sql-server-cluster"></a>SQL Server-fürt védelmének segítése

SQL Server Standard Edition vagy SQL Server 2008 R2 rendszerű fürtök esetében javasoljuk, hogy a SQL Server védelméhez Site Recovery replikációt használjon.

### <a name="azure-to-azure-and-on-premises-to-azure"></a>Azure-ról Azure-ra és a helyszínen az Azure-ba

A Site Recovery nem biztosítja a vendég fürt támogatását az Azure-régióba való replikáláskor. A SQL Server Standard Edition szintén nem biztosít alacsony költséghatékonyságú vész-helyreállítási megoldást. Ebben az esetben javasoljuk, hogy az SQL Server-fürtöt az elsődleges helyen található önálló SQL Server példányra, majd a másodlagos helyre állítsa helyre.

1. Konfiguráljon egy további önálló SQL Server példányt az elsődleges Azure-régióban vagy a helyszíni helyen.

1. Konfigurálja úgy a példányt, hogy tükrözze a védelemmel ellátni kívánt adatbázisok tükrözését. A tükrözés konfigurálása magas biztonságú módban.

1. Site Recovery konfigurálása az elsődleges helyen [Azure](azure-to-azure-tutorial-enable-replication.md)-, [Hyper-V](site-recovery-hyper-v-site-to-azure.md)-vagy VMware-alapú [virtuális gépekhez és fizikai kiszolgálókhoz](site-recovery-vmware-to-azure-classic.md).

1. Site Recovery replikáció használatával replikálhatja az új SQL Server példányt a másodlagos helyre. Mivel ez egy magas biztonsági tükrözés, a rendszer szinkronizálja az elsődleges fürttel, de replikálja Site Recovery replikáció használatával.

   ![Az elsődleges hely, a Site Recovery és az Azure közötti kapcsolatot és folyamatot ábrázoló szabványos fürt képe](./media/site-recovery-sql/standalone-cluster-local.png)

### <a name="failback-considerations"></a>Feladat-visszavétel szempontjai

SQL Server Standard fürtök esetén a feladat-visszavétel nem tervezett feladatátvétel után SQL Server biztonsági mentést és visszaállítást igényel. Ez a művelet a tükrözési példányról az eredeti fürtre történik a tükrözés újbóli létrehozásával.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="how-does-sql-server-get-licensed-when-used-with-site-recovery"></a>Hogyan történik a SQL Server licenc beszerzése a Site Recovery használatakor?

SQL Server Site Recovery replikációját a frissítési garancia vész-helyreállítási juttatása szabályozza. Ez a lefedettség az összes Site Recovery-forgatókönyvre vonatkozik: a helyszíni és az Azure-beli vész-helyreállításra, valamint az Azure-beli IaaS vész-helyreállításra. További információ: [Azure site Recovery díjszabása](https://azure.microsoft.com/pricing/details/site-recovery/) .

### <a name="will-site-recovery-support-my-sql-server-version"></a>Támogatni fogja Site Recovery a SQL Server verziót?

Site Recovery az alkalmazás agnosztikus. A Site Recovery a támogatott operációs rendszereken telepített SQL Server bármely verziójának védelmére képes. További információ: a replikált gépek [helyreállításának támogatási mátrixa](vmware-physical-azure-support-matrix.md#replicated-machines) .

## <a name="next-steps"></a>További lépések

* További információ a [site Recovery architektúráról](site-recovery-components.md).
* Az Azure-beli SQL Server a másodlagos Azure-régióban történő helyreállításhoz használható [magas rendelkezésre állási megoldásokról](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions) itt tájékozódhat.
* SQL Database az [üzletmenet folytonosságát](../sql-database/sql-database-business-continuity.md) és a [magas rendelkezésre állási](../sql-database/sql-database-high-availability.md) lehetőségeket a másodlagos Azure-régióban való helyreállításhoz.
* A helyszíni SQL Server gépek esetében további információ az Azure-beli helyreállítás [magas rendelkezésre állási lehetőségeiről](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md#hybrid-it-disaster-recovery-solutions) Virtual machines.
