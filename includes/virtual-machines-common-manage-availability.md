---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: f57c2cacca9bb3e4526ec6261b8aa0ff6c18448a
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67179096"
---
## <a name="understand-vm-reboots---maintenance-vs-downtime"></a>A virtuális gépek újraindításának ismertetése – karbantartás és állásidő
Azure-beli virtuális gép szolgáló három forgatókönyv: nem tervezett hardverkarbantartás, váratlan állásidő és tervezett karbantartás.

* **Nem tervezett hardverkarbantartási esemény** akkor fordul elő, ha az Azure platform előre jelzi, hogy egy adott fizikai kiszolgálóhoz tartozó hardver vagy bármely platformösszetevő meg fog hibásodni. Amikor a platform előre jelez egy hibát, kiad egy nem tervezett hardverkarbantartási eseményt az adott hardveren futtatott virtuális gépekre gyakorolt hatás csökkentése érdekében. Azure az [élő áttelepítés](https://docs.microsoft.com/azure/virtual-machines/linux/maintenance-and-updates) technológiáját használja a virtuális gépek áttelepítése a hibás hardverről egy megfelelő állapotú fizikai gépre. Az Élő áttelepítés a virtuális gépet megőrző művelet, amely csak minimális időre állítja le a virtuális gépet. A memória tartalmát, a megnyitott fájlokat és a hálózati kapcsolatokat mind megőrzi, azonban a teljesítmény az esemény előtt és/vagy után csökkenhet. Ha az Élő áttelepítés nem használható, a virtuális gépen váratlan állásidő következik be az alábbiak szerint.


* **Váratlan állásidő** akkor, ha a hardver- vagy a fizikai infrastruktúra, a virtuális gép váratlanul meghibásodik. Ilyenek lehetnek a helyi hálózati hiba, a helyi lemezek meghibásodása vagy egyéb állványszintű meghibásodások. Ha észlel, az Azure platform automatikusan migrálja (kijavítja) a virtuális gép ugyanabban az adatközpontban egy megfelelő állapotú fizikai gépre. A javítási folyamat során a virtuális gép állásideje következik be (újraindítás), valamint bizonyos esetekben elveszhet az ideiglenes meghajtó. A csatlakoztatott rendszer- és adatmeghajtók minden esetben megmaradnak. 

  Virtuális gépek is tapasztalhatnak a szolgáltatáskimaradás vagy a teljes adatközpontot, vagy akár egy teljes régiót érintő vészhelyreállítás nem túl valószínű esetben az állásidő. Ebben az esetben az Azure biztosít a védelmi beállításokat, beleértve a [rendelkezésre állási zónák](../articles/availability-zones/az-overview.md) és [párosított régiók](../articles/best-practices-availability-paired-regions.md#what-are-paired-regions).

* A **tervezett karbantartási események** a Microsoft által a mögöttes Azure platformon végzett időszakos frissítések, amelyek célja a virtuális gépeket futtató platforminfrastruktúra általános megbízhatóságának, teljesítményének és biztonságának növelése. A frissítések a legtöbb esetben semmilyen hatással nincsenek a virtuális gépek vagy a felhőszolgáltatások működésére (lásd: [VM-megőrző karbantartás](https://docs.microsoft.com/azure/virtual-machines/windows/preserving-maintenance)). Bár az Azure platform minden esetben VM-megőrző karbantartást igyekszik végrehajtani, egyes ritka esetekben a frissítések megkövetelik a virtuális gép újraindítását, hogy a szükséges frissítéseket telepíteni lehessen a mögöttes infrastruktúrára. Ilyen esetekben karbantartással és ismételt üzembe helyezéssel kiegészített Azure tervezett karbantartási eljárást hajthat végre, ha a megfelelő időtartományban kezdeményezi a virtuális gépek karbantartását. További információkért lásd: [Virtuális gépek tervezett karbantartása](https://docs.microsoft.com/azure/virtual-machines/windows/planned-maintenance/).


Az ilyen események okozta állásidő hatásainak csökkentése érdekében javasoljuk, hogy kövesse az alábbi bevált gyakorlatokat a virtuális gépek magas rendelkezésre állásának biztosításához:

* [Több virtuális gép rendelkezésre állási csoportba konfigurálása a redundancia biztosítása érdekében]
* [Felügyelt lemezek használata rendelkezésre állási csoporthoz tartozó virtuális gépekkel]
* [Használja a virtuális gép érintő események proaktív módon választ az ütemezett események](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-scheduled-events)
* [Az egyes alkalmazásrétegek külön rendelkezésre állási csoportokba konfigurálása]
* [Terheléselosztók és rendelkezésre állási csoportok együttes alkalmazása]
* [Szolgáltatói adatközpontok meghibásodásai elleni rendelkezésre állási zónák használata]

## <a name="configure-multiple-virtual-machines-in-an-availability-set-for-redundancy"></a>Több virtuális gép rendelkezésre állási csoportba konfigurálása a redundancia biztosítása érdekében
Az alkalmazás redundanciájának garantálása érdekében javasoljuk, hogy a virtuális gépeket legalább kettesével foglalja rendelkezésre állási csoportokba. Ezt a konfigurációt egy adatközponton belül biztosítja, hogy vagy a tervezett vagy nem tervezett karbantartási események esetén legalább egy virtuális gép érhető el és megfeleljen a 99,95 %-os Azure SLA-t. További információkért lásd [a virtuális gépek esetében érvényes SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) részleteit.

> [!IMPORTANT]
> Ha lehet, önmagában ne helyezzen egyetlen különálló virtuális gépet egy rendelkezésre állási csoportba. Ebben a konfigurációban a virtuális gépek nem tesz eleget az SLA-garanciára, és leállhatnak az Azure tervezett karbantartási események, kivéve, ha egyetlen virtuális gép által használt [Azure prémium szintű SSD-k](../articles/virtual-machines/windows/disks-types.md#premium-ssd). Prémium szintű SSD meghajtók használata önálló virtuális gépek az Azure garantált szolgáltatási SZINTJEI érvényesek.

A mögöttes Azure platform a rendelkezésre állási csoportban lévő mindegyik virtuális gépnek kioszt egy **frissítési tartományt** és egy **tartalék tartományt**. Minden eges rendelkezésre állási csoportnak öt, felhasználó által nem konfigurálható frissítési tartományt oszt ki a rendszer alapértelmezés szerint (a Resource Manager-környezetek ezután akár 20 frissítési tartományig növelhetőek), és így kijelöli azokat a virtuális gépeket és mögöttes fizikai hardvereszközöket magukba foglaló csoportokat, amelyek egy időben újraindíthatóak. Ha egy rendelkezésre állási csoportba ötnél több virtuális gép van konfigurálva, a hatodik virtuális gép az elsővel azonos frissítési tartományba kerül, a hetedik a másodikkal és így tovább. A frissítési tartományok újraindítása nem haladhat szekvenciálisan a tervezett karbantartás során, hanem csak egyetlen frissítési tartományt lehet újraindítani egyszerre. Az újraindított frissítési tartománynak 30 perce van, hogy helyreálljon, mielőtt a karbantartás elkezdődik egy másik frissítési tartományon.

A tartalék tartományok azonos tápforrással és hálózati kapcsolóval rendelkező virtuális gépek csoportjai. Alapértelmezés szerint a rendelkezésre állási csoportba konfigurált virtuális gépek legfeljebb három tartalék tartományba vannak elkülönítve a Resource Manager-környezetben (két tartalék tartományba a klasszikus környezetben). Bár a virtuális gépek rendelkezésre állási csoportokba rendezése nem védi az alkalmazást az operációs rendszerre vezethető vagy az alkalmazásspecifikus hibáktól, korlátozza a potenciális hardvermeghibásodások, hálózatkimaradások vagy tápellátás-megszakadások hatását.

<!--Image reference-->
   ![A frissítési és tartalék tartomány konfiguráció elméleti rajza](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="use-managed-disks-for-vms-in-an-availability-set"></a>Felügyelt lemezek használata rendelkezésre állási csoporthoz tartozó virtuális gépekkel
Ha jelenleg a virtuális gépeket nem felügyelt lemezeken használja, határozottan ajánlott [a rendelkezésre állási csoportban lévő virtuális gépeket átalakítása, hogy felügyelt lemezeken fussanak](../articles/virtual-machines/windows/convert-unmanaged-to-managed-disks.md).

A [felügyelt lemezek](../articles/virtual-machines/windows/managed-disks-overview.md) jobb rendelkezésre állást nyújtanak a rendelkezésre állási csoportok számára, mivel biztosítják a rendelkezésre állási csoportban lévő virtuális gépek lemezeinek megfelelő elszigetelését a kritikus hibapontok elkerülése érdekében. Ezt automatikusan helyezi el a lemezek különböző tárolási tartalék tartományok (tárolási fürtöket) és igazodó azokat a virtuális gép tartalék tartomány hajtja végre. Ha egy tárolási tartalék tartomány hardver-vagy szoftverhiba miatt meghiúsul, csak a Virtuálisgép-példány a storage tartalék tartományban lévő lemezek sikertelen lesz.
![A felügyelt lemezek tartalék tartományok között](./media/virtual-machines-common-manage-availability/md-fd-updated.png)

> [!IMPORTANT]
> A felügyelt rendelkezésre állási csoportok tartalék tartományainak száma régiónként eltérő – régiónként kettő vagy három lehet. A régiónkénti mennyiségeket az alábbi táblázat mutatja

[!INCLUDE [managed-disks-common-fault-domain-region-list](managed-disks-common-fault-domain-region-list.md)]

Ha azt tervezi, nem felügyelt lemezekkel rendelkező virtuális gépekhez használja, kövesse az alábbi ajánlott eljárások a tárfiókok, virtuális merevlemezeket (VHD) virtuális gépek tárolására [lapblobokat](https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs#about-page-blobs).

1. **Tárolja az egyazon virtuális géppel társított összes lemezt (operációsrendszer- és adatlemezt) ugyanabban a tárfiókban.**
2. **Tekintse át a Storage-fiókokban lévő nem felügyelt lemezekre vonatkozó [korlátokat](../articles/storage/common/storage-scalability-targets.md),** mielőtt további virtuális merevlemezeket adna hozzá egy tárfiókhoz.
3. **Használjon külön tárfiókot minden egyes virtuális géphez a rendelkezésre állási csoportban.** Ne tárolja az egyazon rendelkezésre állási csoportban lévő virtuális gépeket ugyanabban a Storage-fiókban. Storage-fiókok megosztásához, ha a fenti ajánlott eljárások a különböző rendelkezésre állási csoportokban lévő virtuális gépek számára elfogadható legyen ![nem felügyelt lemezek tartalék tartományok között](./media/virtual-machines-common-manage-availability/umd-updated.png)

## <a name="use-scheduled-events-to-proactively-respond-to-vm-impacting-events"></a>Az ütemezett események segítségével proaktív módon reagálhat érintő események VM

Szolgáltatásra való előfizetéskor [ütemezett események](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-scheduled-events), a virtuális gép értesült a közelgő karbantartásokról események, amelyek hatással lehetnek a virtuális gép. Ha az ütemezett események engedélyezve vannak, a virtuális gép egy minimális időtartama a karbantartási tevékenység végrehajtása előtt van megadva. Például, ami hatással lehet a virtuális gép gazda operációs rendszer frissítéseinek várólistára kerülnek, adja meg a hatást eseményeket, valamint egy időpontot, amikor a karbantartás történik, ha nem tesz. Ütemezés események is várólistára, ha az Azure-hoz közeli hardverhiba, ami hatással lehet a virtuális gép, amely lehetővé teszi, hogy Ön döntheti el, ha a javítás kell végrehajtani. Ügyfelek átadni a feladatokat a másodlagos előtt a karbantartási feladatok végrehajtásához például állapotmentést, az eseményt használja, és így tovább. Miután elvégezte a logikai szabályosan kezeléséhez a karbantartási események, jóváhagyhatja a szálankénti függőben lévő Ütemezett esemény, hogy a platform karbantartási folytatásához.

## <a name="configure-each-application-tier-into-separate-availability-sets"></a>Az egyes alkalmazásrétegek külön rendelkezésre állási csoportokba konfigurálása
Ha a virtuális gépek majdnem azonosak, és ugyanaz a rendeltetésük az alkalmazásban, javasoljuk, hogy konfiguráljon egy rendelkezésre állási csoportot az alkalmazás mindegyik rétegéhez.  Ha két különböző réteget ugyanabba a rendelkezésre állási csoportba helyez, az ugyanabban az alkalmazásrétegben lévő mindegyik virtuális gép egyszerre indítható újra. Ha legalább két virtuális gépet konfigurál az egyes rendelkezésre állási csoportokba minden egyes réteghez, ezzel garantálja, hogy minden egyes rétegben legalább egy virtuális gép elérhető lesz.

Például az IIS-t, Apache-ot és Nginxet futtató alkalmazás előterében lévő mindegyik virtuális gépet elhelyezheti egyetlen rendelkezésre állási csoportban. Ugyanebbe a rendelkezésre állási csoportba mindenképp csak előtérben futó virtuális gépeket helyezzen. Ugyanígy gondoskodjon róla, hogy csak az adatrétegben futó virtuális gépek kerüljenek a nekik dedikált rendelkezésre állási csoportba, például replikált SQL Server-virtuálisgépek vagy MySQL-virtuálisgépek.

<!--Image reference-->
   ![Alkalmazásrétegek](./media/virtual-machines-common-manage-availability/application-tiers.png)

## <a name="combine-a-load-balancer-with-availability-sets"></a>Terheléselosztók és rendelkezésre állási csoportok együttes alkalmazása
Az [Azure Load Balancer](../articles/load-balancer/load-balancer-overview.md) a rendelkezésre állási csoportokkal kombinálva a legmagasabb fokú rugalmasságot biztosítja az alkalmazásoknak. Az Azure Load Balancer több virtuális gép között osztja el a forgalmat. A Standard csomagban elérhető virtuális gépek esetében az Azure Load Balancer a csomag részét képezi. Nem mindegyik virtuális gép csomagja tartalmazza az Azure Load Balancert. A virtuális gépek terheléselosztásáról további információkért lásd a [virtuális gépek terheléselosztását](../articles/virtual-machines/virtual-machines-linux-load-balance.md) ismertető témakört.

Ha a terheléselosztó nem úgy van konfigurálva, hogy a terhelést elossza több virtuális gép között, a tervezett karbantartás hatással lesz az egyetlen forgalomkiszolgáló virtuális gépre, ami kimaradást okoz az alkalmazásrétegben. Ha több egy rétegbe tartozó virtuális gépet helyez egyazon terheléselosztó és rendelkezésre állási csoport alá, a forgalmat mindig legalább egy példány képes lesz kiszolgálni.

## <a name="use-availability-zones-to-protect-from-datacenter-level-failures"></a>Szolgáltatói adatközpontok meghibásodásai elleni rendelkezésre állási zónák használata

[A rendelkezésre állási zónák](../articles/availability-zones/az-overview.md), másik rendelkezésre állási csoportok, bontsa ki a szabályozás rendelkezik az alkalmazások és adatok a virtuális gépek rendelkezésre állásának fenntartása érdekében. A rendelkezésre állási zónák egy Azure-régió fizikailag elkülönített zónáit jelentik. Nincsenek három rendelkezésre állási zónákhoz támogatott Azure-régiónként. Egyes rendelkezésre állási zóna különálló rendelkezik forrás, hálózattal és hűtéssel és logikailag elkülönítve a többi rendelkezésre állási zónák Azure-régióban. A replikált virtuális gépek használata a zónák a megoldások tervezése, védheti alkalmazásait és adatait az egy kínai adatközpont származik. Ha egy zóna integritása sérül, majd a replikált alkalmazások és adatok érhetők el azonnal egy másik zónában. 

![Rendelkezésre állási zónák](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

További információ a központi telepítése egy [Windows](../articles/virtual-machines/windows/create-powershell-availability-zone.md) vagy [Linux](../articles/virtual-machines/linux/create-cli-availability-zone.md) virtuális gép egy rendelkezésre állási zónában.


<!-- Link references -->
[Több virtuális gép rendelkezésre állási csoportba konfigurálása a redundancia biztosítása érdekében]: #configure-multiple-virtual-machines-in-an-availability-set-for-redundancy
[Az egyes alkalmazásrétegek külön rendelkezésre állási csoportokba konfigurálása]: #configure-each-application-tier-into-separate-availability-sets
[Terheléselosztók és rendelkezésre állási csoportok együttes alkalmazása]: #combine-a-load-balancer-with-availability-sets
[Avoid single instance virtual machines in availability sets]: #avoid-single-instance-virtual-machines-in-availability-sets
[Felügyelt lemezek használata rendelkezésre állási csoporthoz tartozó virtuális gépekkel]: #use-managed-disks-for-vms-in-an-availability-set
[Szolgáltatói adatközpontok meghibásodásai elleni rendelkezésre állási zónák használata]: #use-availability-zones-to-protect-from-datacenter-level-failures
