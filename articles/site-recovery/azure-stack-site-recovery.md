---
title: Azure Stack virtuális gépek replikálása az Azure-ba Azure Site Recovery használatával | Microsoft Docs
description: Ismerje meg, hogyan állíthatja be a vész-helyreállítást az Azure-ba Azure Stack virtuális gépekre a Azure Site Recovery szolgáltatással.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.topic: conceptual
ms.service: site-recovery
ms.date: 08/05/2019
ms.author: raynew
ms.openlocfilehash: 1932221e18241d8a2d921f61375019f969e61912
ms.sourcegitcommit: f7998db5e6ba35cbf2a133174027dc8ccf8ce957
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/05/2019
ms.locfileid: "68782677"
---
# <a name="replicate-azure-stack-vms-to-azure"></a>Az Azure Stack-alapú virtuális gépek replikálása az Azure-bA

Ez a cikk bemutatja, hogyan állíthatja be a vész-helyreállítási Azure Stack virtuális gépeket az Azure-ba a [Azure site Recovery szolgáltatás](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview)használatával.

Site Recovery az üzletmenet-folytonossági és a vész-helyreállítási (BCDR) stratégiához járul hozzá. A szolgáltatás biztosítja, hogy a virtuális gépek számítási feladatai a vártnál továbbra is rendelkezésre álljanak, és váratlan kimaradások történnek.

- Site Recovery összehangolja és kezeli a virtuális gépek replikálását az Azure Storage-ba.
- Ha áramkimaradás fordul elő az elsődleges helyen, akkor Site Recovery az Azure-ba történő feladatátvételhez.
- A feladatátvétel során az Azure-beli virtuális gépek a tárolt virtuálisgép-adatokból jönnek létre, és a felhasználók továbbra is hozzáférhetnek az Azure-beli virtuális gépeken futó munkaterhelésekhez.
- Ha minden újra működik, az Azure-beli virtuális gépeket visszaállíthatja az elsődleges helyre, és megkezdheti az Azure Storage-ba való replikálást.


Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * **1. lépés: Készítse elő az Azure stack**virtuális gépeket a replikáláshoz. Győződjön meg arról, hogy a virtuális gépek megfelelnek Site Recovery követelményeinek, és előkészíti a Site Recovery mobilitási szolgáltatás telepítését. Ez a szolgáltatás minden replikálni kívánt virtuális gépen telepítve van.
> * **2. lépés: Recovery Services-tároló beállítása**. Állítson be egy tárolót a Site Recoveryhoz, és adja meg, hogy mit szeretne replikálni. Site Recovery összetevőket és műveleteket a tárolóban kell konfigurálni és felügyelni.
> * **3. lépés: Állítsa be a forrás replikációs környezetet**. Site Recovery konfigurációs kiszolgáló beállítása. A konfigurációs kiszolgáló egyetlen Azure Stack virtuális gép, amely a Site Recovery által igényelt összes összetevőt futtatja. A konfigurációs kiszolgáló beállítása után regisztrálja a tárolóban.
> * **4. lépés: Állítsa be a cél replikációs környezetet**. Válassza ki az Azure-fiókját és a használni kívánt Azure Storage-fiókot és-hálózatot. A replikáció során a rendszer átmásolja a virtuális gépeket az Azure Storage szolgáltatásba. A feladatátvételt követően az Azure-beli virtuális gépek csatlakoznak a megadott hálózathoz.
> * **5. lépés: Replikáció**engedélyezése. Konfigurálja a replikációs beállításokat, és engedélyezze a replikálást a virtuális gépek számára. A mobilitási szolgáltatás telepítése egy virtuális gépre történik, ha a replikáció engedélyezve van. Site Recovery végrehajtja a virtuális gép kezdeti replikálását, majd megkezdi a folyamatban lévő replikációt.
> * **6. lépés: Vész-helyreállítási részletezés**futtatása: A replikálást követően a részletezés futtatásával ellenőrizheti, hogy a feladatátvétel a várt módon fog-e működni. A részletezés elindításához futtasson egy feladatátvételi tesztet Site Recovery. A feladatátvételi teszt nem érinti az éles környezetet.

Ezekkel a lépésekkel teljes körű feladatátvételt futtathat az Azure-ban, és amikor szükséges.

## <a name="architecture"></a>Architektúra

![Architektúra](./media/azure-stack-site-recovery/architecture.png)

**Location** | **Összetevő** |**Részletek**
--- | --- | ---
**Konfigurációs kiszolgáló** | Egyetlen Azure Stack virtuális gépen fut. | Az egyes előfizetésekben beállíthatja a konfigurációs kiszolgáló virtuális gépet. Ez a virtuális gép a következő Site Recovery összetevőket futtatja:<br/><br/> -Konfigurációs kiszolgáló: Koordinálja a helyszíni és az Azure közötti kommunikációt, és felügyeli az adatreplikációt. -Process Server: Replikációs átjáróként üzemel. Replikációs adatfogadást fogad, optimalizálja a gyorsítótárazást, a tömörítést és a titkosítást; és elküldi az Azure Storage-nak.<br/><br/> Ha a replikálni kívánt virtuális gépek túllépik az alább megadott korlátokat, beállíthat egy külön önálló folyamat-kiszolgálót. [További információk](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-process-server-scale).
**Mobilitási szolgáltatás** | Minden olyan virtuális gépen telepítve van, amelyet replikálni szeretne. | A cikk lépéseiben előkészítünk egy fiókot, hogy a mobilitási szolgáltatás automatikusan telepítve legyen egy virtuális gépen, ha a replikáció engedélyezve van. Ha nem szeretné automatikusan telepíteni a szolgáltatást, számos más módszer is használható. [További információk](https://docs.microsoft.com/azure/site-recovery/vmware-azure-install-mobility-service).
**Azure** | Az Azure-ban szüksége van egy Recovery Services-tárolóra, egy Storage-fiókra és egy virtuális hálózatra. |  A replikált fájlokat a rendszer a Storage-fiókban tárolja. Feladatátvétel esetén az Azure-beli virtuális gépek hozzáadódnak az Azure-hálózathoz. 


A replikáció a következőképpen működik:

1. A tárolóban adja meg a replikálás forrását és célját, állítsa be a konfigurációs kiszolgálót, hozzon létre egy replikációs házirendet, és engedélyezze a replikálást.
2. A mobilitási szolgáltatás telepítve van a gépen (ha leküldéses telepítést használt), és a gépek a replikációs házirendnek megfelelően megkezdik a replikálást.
3. A kiszolgáló adatainak kezdeti másolatát az Azure Storage-ba replikálja a rendszer.
4. A kezdeti replikálás befejeződése után a változásokat az Azure-ba replikálva megkezdődik. A gépek nyomon követett módosításait a rendszer egy .hrl fájlban tárolja.
5. A konfigurációs kiszolgáló a replikálást az Azure-ban (HTTPS 443 kimenő port) hangolja össze.
6. A Process Server adatokat fogad a forrásoldali gépekről, optimalizálja és titkosítja, majd elküldi az Azure Storage-nak (443-es port).
7. A replikált gépek kommunikálnak a konfigurációs kiszolgálóval (HTTPS 443 bejövő port, replikálási felügyelet céljából. A gépek replikációs adatküldést küldenek a folyamat kiszolgálójára (a HTTPS 9443 bejövő portja módosítható).
8. Az adatforgalmat a rendszer az interneten keresztül az Azure Storage nyilvános végpontjaira replikálja. Másik lehetőségként használhatja az Azure ExpressRoute nyilvános társítását is. Az adatforgalmat helyszíni helyek és az Azure között helyek közötti VPN-en keresztül nem lehet replikálni.

## <a name="prerequisites"></a>Előfeltételek

Ezt a forgatókönyvet kell beállítania.

**Követelmény** | **Részletek**
--- | ---
**Azure-előfizetési fiók** | Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).
**Azure-fiók engedélyei** | Az Ön által használt Azure-fióknak a következő engedélyekkel kell rendelkeznie:<br/><br/> -Recovery Service-tároló létrehozása<br/><br/> -Hozzon létre egy virtuális gépet a forgatókönyvhöz használt erőforráscsoport és virtuális hálózat között.<br/><br/> – Írás a megadott Storage-fiókba<br/><br/> Vegye figyelembe:<br/><br/> -Fiók létrehozásakor Ön az előfizetés rendszergazdája, és az összes művelet elvégezhető.<br/><br/> – Ha meglévő előfizetést használ, és Ön nem a rendszergazda, akkor a rendszergazdával kell dolgoznia a tulajdonosi vagy közreműködői engedélyek hozzárendeléséhez.<br/><br/> – Ha részletesebb engedélyekre van szüksége, tekintse át [ezt a cikket](https://docs.microsoft.com/azure/site-recovery/site-recovery-role-based-linked-access-control). 
**Azure Stack virtuális gép** | Szüksége van egy Azure Stack virtuális gépre a bérlői előfizetésben, amely Site Recovery konfigurációs kiszolgálóként lesz telepítve. 


### <a name="prerequisites-for-the-configuration-server"></a>A konfigurációs kiszolgáló előfeltételei

[!INCLUDE [site-recovery-config-server-reqs-physical](../../includes/site-recovery-config-server-reqs-physical.md)]


 
## <a name="step-1-prepare-azure-stack-vms"></a>1\. lépés: Azure Stack virtuális gépek előkészítése

### <a name="verify-the-operating-system"></a>Az operációs rendszer ellenőrzése

Győződjön meg arról, hogy a virtuális gépek a táblázatban összegzett operációs rendszerek egyikét futtatják.


**Operációs rendszer** | **Részletek**
--- | ---
**64 bites Windows** | Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 (SP1)
**CentOS** | 5,2 – 5,11, 6,1, 6,9, 7,0 és 7,3
**Ubuntu** | 14,04 LTS-kiszolgáló, 16,04 LTS-kiszolgáló. [Támogatott kernelek](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#ubuntu-kernel-versions) áttekintése

### <a name="prepare-for-mobility-service-installation"></a>Felkészülés a mobilitási szolgáltatás telepítésére

Minden replikálni kívánt virtuális gépnek telepítve kell lennie a mobilitási szolgáltatásnak. Ahhoz, hogy a Process Server automatikusan telepítse a szolgáltatást a virtuális gépen, amikor a replikáció engedélyezve van, ellenőrizze a virtuális gép beállításait.

#### <a name="windows-machines"></a>Windows rendszerű gépek

- Hálózati kapcsolatra van szüksége a virtuális gép között, amelyen engedélyezni szeretné a replikálást, és a Process Servert futtató számítógép (alapértelmezés szerint ez a konfigurációs kiszolgáló virtuális gép).
- Olyan fiókra van szüksége, amely rendszergazdai jogosultságokkal rendelkezik (tartományi vagy helyi) azon a gépen, amelyhez engedélyezi a replikációt.
    - Ezt a fiókot a Site Recovery beállításakor kell megadnia. Ezt követően a folyamat-kiszolgáló ezt a fiókot használja a mobilitási szolgáltatás telepítéséhez, ha a replikáció engedélyezve van.
    - Ezt a fiókot csak Site Recovery fogja használni a leküldéses telepítéshez és a mobilitási szolgáltatás frissítéséhez.
    - Ha nem tartományi fiókot használ, le kell tiltania a távoli felhasználói hozzáférés-vezérlést a virtuális gépen:
        - A beállításjegyzékben hozzon létre DWORD Value **LocalAccountTokenFilterPolicy** a HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System. alatt
        - Állítsa az értéket 1-re.
        - Ehhez írja be a következőt a parancssorba: **Reg add HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System/V LocalAccountTokenFilterPolicy/T REG_DWORD/d 1**.
- A replikálni kívánt virtuális gépen a Windows tűzfalon engedélyezze a fájl-és nyomtatómegosztás, valamint a WMI használatát.
    - Ehhez futtassa a **WF. msc fájlt** a Windows tűzfal konzoljának megnyitásához. Kattintson a jobb gombbal a **Bejövő szabályok** > **új szabály**elemre. Válassza az **előre definiált**lehetőséget, majd válassza a **fájl-és nyomtatómegosztás** elemet a listából. Fejezze be a varázslót, és jelölje be a kapcsolódás> befejezésének engedélyezése jelölőnégyzetet.
    - Tartományi számítógépek esetén ezt a csoportházirend-objektum használatával végezheti el.

    
#### <a name="linux-machines"></a>Linux rendszerű gépek

- Gondoskodjon róla, hogy legyen hálózati kapcsolat a Linux-számítógép és a folyamatkiszolgáló közt.
- Azon a gépen, amelyre engedélyezi a replikációt, szüksége van egy fiókra, amely a forrás Linux-kiszolgáló legfelső szintű felhasználója:
    - Ezt a fiókot a Site Recovery beállításakor kell megadnia. Ezt követően a folyamat-kiszolgáló ezt a fiókot használja a mobilitási szolgáltatás telepítéséhez, ha a replikáció engedélyezve van.
    - Ezt a fiókot csak Site Recovery fogja használni a leküldéses telepítéshez és a mobilitási szolgáltatás frissítéséhez.
- Ellenőrizze, hogy a forrás Linux-kiszolgálón található /etc/hosts fájl tartalmaz-e olyan bejegyzéseket, amelyek a helyi gazdanevet az összes hálózati adapterhez társított IP-címekké képezik le.
- Telepítse a legfrissebb openssh, openssh-server és openssl csomagokat a replikálni kívánt számítógépen.
- Ügyeljen arra, hogy a Secure Shell (SSH) engedélyezve legyen, és a 22-es porton fusson.
- Engedélyezze az SFTP alrendszer és a jelszó hitelesítését az sshd_config fájlban:
    1. Ehhez jelentkezzen be root-ként.
    2. Keresse meg a **PasswordAuthentication**kezdődő sort a/etc/ssh/sshd_config fájlban. Állítsa vissza a sort, és módosítsa az értéket **yes** értékre.
    3. Keresse meg a sort, amelynek a kezdete **Subsystem**, és állítsa vissza.

        ![Linuxos mobilitási szolgáltatás](./media/azure-stack-site-recovery/linux-mobility.png)

    4. Indítsa újra az sshd szolgáltatást.


### <a name="note-the-vm-private-ip-address"></a>Jegyezze fel a virtuális gép magánhálózati IP-címét

Keresse meg az IP-címet az összes replikálni kívánt gépen:

1. A Azure Stack-portálon kattintson a virtuális gépre.
2. Az **erőforrás** menüben kattintson a **hálózati adapterek**elemre.
3. Jegyezze fel a magánhálózati IP-címet.

    ![Magánhálózati IP-cím](./media/azure-stack-site-recovery/private-ip.png)


## <a name="step-2-create-a-vault-and-select-a-replication-goal"></a>2\. lépés: Tároló létrehozása és a replikációs cél kiválasztása

1. A Azure Portal válassza az **erőforrás** > létrehozása**felügyeleti eszközök** > **biztonsági mentése és site Recovery**lehetőséget.
2. A **Név** mezőben adjon meg egy, a tárolót azonosító rövid nevet. 
3. Az **erőforráscsoport**területen hozzon létre vagy válasszon ki egy erőforráscsoportot. **ContosoRG**használunk.
4. A **hely**mezőben adja meg az Azure-régiót. a **Nyugat-Európa** beállítást használjuk.
5. Ha szeretne gyorsan hozzáférni az új tárolóhoz az irányítópultról, válassza a **Rögzítés az irányítópulton** > **Létrehozás** elemet.

   ![Új tároló létrehozása](./media/azure-stack-site-recovery/new-vault-settings.png)

   Az új tároló megjelenik az **Irányítópult** > **Minden erőforrás** menüben, illetve a központi **Recovery Services-tárolók** lapon.

### <a name="select-a-replication-goal"></a>Replikációs cél kiválasztása

1. **Recovery Services** -tárolóban > adja meg a tár nevét. **ContosoVMVault**használunk.
2. Az **Első lépések** területen válassza ki a Site Recovery elemet. Ezután válassza **Az infrastruktúra előkészítése** lehetőséget.
3. A **Védelmi cél** > **Hol találhatók a gépek?** területen válassza a **Helyszíni** lehetőséget.
4. A **Hová szeretné replikálni a gépeket?** területen válassza **Az Azure-ba** lehetőséget.
5. A-ben **a gépek virtualizáltak**, válassza a **nem virtualizált/egyéb**lehetőséget. Ezután kattintson az **OK** gombra.

    ![Védelmi cél](./media/azure-stack-site-recovery/protection-goal.png)

## <a name="step-3-set-up-the-source-environment"></a>3\. lépés: A forráskörnyezet beállítása

Állítsa be a konfigurációs kiszolgáló számítógépét, regisztrálja a tárolóban, és fedezze fel a replikálni kívánt gépeket.

1. Kattintson **Az infrastruktúra előkészítése** > **Forrás** lehetőségre.
2. A **Forrás előkészítése** ablakban kattintson a **+Konfigurációs kiszolgáló** gombra.

    ![A forrás beállítása](./media/azure-stack-site-recovery/plus-config-srv.png)

3. A **kiszolgáló hozzáadása**területen győződjön meg arról, hogy a **konfigurációs kiszolgáló** megjelenik a **kiszolgáló típusa mezőben**.
5. Töltse le a Site Recovery egyesített telepítési telepítőfájlt.
6. Töltse le a tárolóregisztrációs kulcsot. Az egyesített telepítő futtatásakor szüksége lesz a regisztrációs kulcsra. A kulcs a generálásától számított öt napig érvényes.

    ![A forrás beállítása](./media/azure-stack-site-recovery/set-source2.png)


### <a name="run-azure-site-recovery-unified-setup"></a>Azure Site Recovery egyesített telepítő futtatása

A konfigurációs kiszolgáló telepítéséhez és regisztrálásához végezzen RDP-kapcsolattal a konfigurációs kiszolgálóhoz használni kívánt virtuális gépet, és futtassa az egyesített telepítőt.

Mielőtt elkezdené, győződjön meg róla, hogy az óra szinkronizálva van [egy](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service) időkiszolgálóval a virtuális gépen a Kezdés előtt. A telepítés sikertelen lesz, ha az idő több mint öt percet vesz igénybe a helyi idő alatt.

Most telepítse a konfigurációs kiszolgálót:

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> A konfigurációs kiszolgáló a parancssorból is telepíthető. [További információk](physical-manage-configuration-server.md#install-from-the-command-line).
> 
> Akár 15 vagy még több percbe is beletelhet, amíg a fiók neve megjelenik a portálon. Az azonnali frissítéshez válassza a **Konfigurációs kiszolgálók** > ***kiszolgáló neve*** > **Kiszolgáló frissítése** elemet.

## <a name="step-4-set-up-the-target-environment"></a>4\. lépés: A célkörnyezet beállítása

Válassza ki és ellenőrizze a célerőforrásokat.

1. Az **infrastruktúra** > előkészítése**cél**területen válassza ki a használni kívánt Azure-előfizetést.
2. A cél telepítési modell meghatározása.
3. A Site Recovery ellenőrzi, hogy rendelkezik-e legalább egy kompatibilis Azure-tárfiókkal és -hálózattal. Ha nem találja őket, létre kell hoznia legalább egy Storage-fiókot és egy virtuális hálózatot a varázsló befejezéséhez.


## <a name="step-5-enable-replication"></a>5\. lépés: Replikáció engedélyezése

### <a name="create-a-replication-policy"></a>Replikációs házirend létrehozása

1. Kattintson az **infrastruktúra** > előkészítése**replikációs beállítások**elemre.
2. A **Replikációs szabályzat létrehozása** beállításnál adja meg a szabályzat nevét.
3. Az **RPO küszöbértéke** beállításnál adja meg a helyreállítási időkorlátot (RPO).
    - A replikált adathelyreállítási pontok a beállított időponttal összhangban jönnek létre.
    - Ez a beállítás nincs hatással a replikációra, ami folyamatosan működik. Csak akkor ad ki riasztást, ha a küszöbértéket a rendszer nem létrehozta helyreállítási pont nélkül éri el.
4. A **helyreállítási pont megőrzése**területen határozza meg, hogy az egyes helyreállítási pontok meddig legyenek tárolva. A replikált virtuális gépek a megadott időtartomány bármely pontjára visszaállíthatók.
5. Az alkalmazással **konzisztens Pillanatképek gyakorisága**mezőben határozza meg, hogy milyen gyakran hozza létre az alkalmazások konzisztens pillanatképeit.

    - Az alkalmazás-konzisztens pillanatkép a virtuális gépen belüli alkalmazásadatok időponthoz tartozó pillanatképe.
    - Kötet árnyékmásolata szolgáltatás (VSS) biztosítja, hogy a virtuális gépen lévő alkalmazások konzisztens állapotban legyenek a pillanatkép készítésekor.
6. A szabályzat létrehozásához válassza az **OK** gombot.


### <a name="confirm-deployment-planning"></a>Az üzembe helyezés megtervezésének megerősítése

Most kihagyhatja ezt a lépést. A **központi telepítés tervezése** legördülő listában kattintson az **Igen,** elvégeztem.



### <a name="enable-replication"></a>Replikáció engedélyezése

Győződjön meg arról, hogy végrehajtotta az 1. [lépés összes feladatát: Számítógép](#step-1-prepare-azure-stack-vms)előkészítése. Ezután engedélyezze a replikálást a következőképpen:

1. Válassza az **Alkalmazás replikálása** > **Forrás** elemet.
2. A **Forrás** mezőben válassza ki a konfigurációs kiszolgálót.
3. A **gép típusa**területen válassza a **fizikai gépek**lehetőséget.
4. Válassza ki a folyamatkiszolgálót (a konfigurációs kiszolgálót). Ezután kattintson az **OK** gombra.
5. A **cél**mezőben válassza ki azt az előfizetést és erőforráscsoportot, amelyben a virtuális gépeket a feladatátvételt követően létre szeretné hozni. Válassza ki a feladatátvételi virtuális gépekhez használni kívánt telepítési modellt.
6. Válassza ki azt az Azure Storage-fiókot, amelyben tárolni kívánja a replikált fájlokat.
7. Válassza ki azt az Azure-hálózatot és alhálózatot, amelyhez a feladatátvétel után létrejövő Azure-beli virtuális gépek csatlakoznak.
8. Ha a megadott hálózati beállításokat az összes védelemre kijelölt gépre szeretné alkalmazni, válassza a **Beállítás most a kijelölt gépekhez** lehetőséget. Válassza a **Konfigurálás később** lehetőséget, ha az Azure-hálózatot külön szeretné kiválasztani az egyes gépekhez.
9. A **fizikai gépek**területen kattintson a **+ fizikai gép**elemre. Adja meg a replikálni kívánt gépek nevét, IP-címét és operációsrendszer-típusát.

    - Használja a gép belső IP-címét.
    - Ha a nyilvános IP-címet adta meg, előfordulhat, hogy a replikáció nem a várt módon működik.

10. A **Tulajdonságok** > **konfigurálása tulajdonságok**területen válassza ki azt a fiókot, amellyel a Process Server automatikusan telepíti a mobilitási szolgáltatást a gépen.
11. A replikációs **Beállítások** > **replikációs beállítások konfigurálása**területen győződjön meg arról, hogy a megfelelő replikációs házirend van kiválasztva.
12. Kattintson a **Replikáció engedélyezése** elemre.
13. A **védelem engedélyezése** feladat előrehaladásának nyomon követése a **Beállítások** > **feladatok** > **site Recovery feladatok**részen. A **Védelem véglegesítése** feladat befejeződését követően a gép készen áll a feladatátvételre.

> [!NOTE]
> Amikor engedélyezi a replikációt egy adott virtuális gépen, a Site Recovery telepíti a mobilitási szolgáltatást.
> 
> 15 vagy több percet is igénybe vehet, hogy a módosítások életbe lépjenek és megjelenjenek a portálon.
> 
> A hozzáadott virtuális gépek monitorozásához ellenőrizze a virtuális gépek legutolsó felderítésének időpontját a **Konfigurációs kiszolgálók** > **Legutóbbi kapcsolat** területen. Ha nem szeretné megvárni az ütemezett felderítést a virtuális gépek hozzáadásához, emelje ki a konfigurációs kiszolgálót (ne válassza ki), majd válassza a **Frissítés** elemet.


## <a name="step-6-run-a-disaster-recovery-drill"></a>6\. lépés: Vészhelyreállítási próba végrehajtása

Futtasson egy feladatátvételi tesztet az Azure-ba, és ellenőrizze, hogy minden a várt módon működik-e. Ez a feladatátvétel nem befolyásolja az éles környezetet.

### <a name="verify-machine-properties"></a>Számítógép tulajdonságainak ellenőrzése

A feladatátvételi teszt futtatása előtt ellenőrizze a gép tulajdonságait, és győződjön meg arról, hogy azok megfelelnek az [Azure követelményeinek](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements). A tulajdonságokat az alábbiak szerint tekintheti meg és módosíthatja:

1. A **Védett elemek** területen kattintson a **Replikált elemek** > VM lehetőségre.
2. A **Replikált elemek** ablaktáblában szerepel a virtuális gép információinak összegzése, állapota és a legújabb elérhető helyreállítási pontok. Kattintson a **Tulajdonságok** lehetőségre a további részletek megtekintéséhez.
3. A **számítás és hálózat**területen módosítsa a beállításokat igény szerint.

    - Módosíthatja az Azure-beli virtuális gép nevét, az erőforráscsoportot, a célként megadott méretet, a [rendelkezésre állási](../virtual-machines/windows/tutorial-availability-sets.md)csoportot és a felügyelt lemez beállításait.
    - Megtekintheti és módosíthatja a hálózati beállításokat is. Ezek közé tartozik a hálózat/alhálózat, amelyhez az Azure-beli virtuális gép csatlakoztatva van a feladatátvétel után, valamint a virtuális géphez hozzárendelt IP-cím.
1. A **lemezek**területen tekintse meg a virtuális gép operációs rendszerével és adatlemezével kapcsolatos információkat.
   

### <a name="run-a-test-failover"></a>Feladatátvételi teszt futtatása

Feladatátvételi teszt futtatásakor a következő történik:

1. A rendszer lefuttatja az előfeltételek ellenőrzését, hogy a feladatátvételhez szükséges feltételek biztosan teljesüljenek.
2. A feladatátvétel a megadott helyreállítási pont használatával dolgozza fel az adatfeldolgozást:
    - **Legutóbb feldolgozott**: A gép feladatátvételt hajt végre a Site Recovery által feldolgozott legutóbbi helyreállítási pontra. Megjelenik az időbélyeg. Ezzel a beállítással a rendszer nem tölt időt az adatok feldolgozásával, így a helyreállítási időre vonatkozó célkitűzés (RTO) alacsony.
    - **Legújabb alkalmazás – konzisztens**: A számítógép feladatátvételt hajt végre a legújabb alkalmazás-konzisztens helyreállítási pontra.
    - **Egyéni**: Válassza ki a feladatátvételhez használt helyreállítási pontot.

3. Az Azure-beli virtuális gépek a feldolgozott adatkezelési szolgáltatással hozhatók létre.
4. A feladatátvételi teszt képes automatikusan törölni a részletezés során létrehozott Azure-beli virtuális gépeket.

Futtasson egy feladatátvételi tesztet a virtuális gépen a következőképpen:

1. A **Beállítások** > **Replikált elemek** területen kattintson a virtuális gép > **+Feladatátvételi teszt** ikonra.
2. Ebben az útmutatóban a **legújabb feldolgozott** helyreállítási pontot fogjuk használni. 
3. A **feladatátvételi teszt**területen válassza ki a cél Azure-hálózatot.
4. A feladatátvételi művelet elindításához kattintson az **OK** gombra.
5. A folyamat nyomon követéséhez kattintson a virtuális gépre, és nyissa meg a tulajdonságait. Vagy kattintson a **feladatátvételi teszt** feladat a *tár neve* > **Beállítások** > **feladatok** >**site Recovery feladatok**lehetőségre.
6. A feladatátvétel befejezését követően az Azure-beli virtuális gép replikája megjelenik az Azure Portal > **Virtuális gépek** területen. Győződjön meg arról, hogy a virtuális gép a megfelelő méretű, a megfelelő hálózathoz csatlakozik, és fut.
7. Így már tudnia kell csatlakozni a replikált virtuális géphez az Azure-ban. [További információk](https://docs.microsoft.com/azure/site-recovery/site-recovery-test-failover-to-azure#prepare-to-connect-to-azure-vms-after-failover).
8. A feladatátvételi teszt során létrehozott Azure-beli virtuális gépek törléséhez kattintson a **Feladatátvételi teszt eltávolítása** elemre a virtuális gépen. A **jegyzetek**területen mentse a feladatátvételi teszttel kapcsolatos megfigyeléseket.

## <a name="fail-over-and-fail-back"></a>Feladatátadás és feladatátvétel

Miután beállította a replikálást, és lefuttatott egy részletezést, hogy minden megfelelően működik, a gépeket szükség szerint feladatátvételt hajthat végre az Azure-ban.

A feladatátvétel futtatása előtt, ha a feladatátvétel után csatlakozni szeretne a géphez az Azure-ban, a Kezdés előtt [elő kell készítenie](https://docs.microsoft.com/azure/site-recovery/site-recovery-test-failover-to-azure#prepare-to-connect-to-azure-vms-after-failover) a csatlakozást.

Ezután futtassa a feladatátvételt az alábbiak szerint:


1. A **Beállítások** > **replikált elemek**területen kattintson a gép > **feladatátvétel**elemre.
2. Válassza ki a használni kívánt helyreállítási pontot.
3. A **feladatátvételi teszt**területen válassza ki a cél Azure-hálózatot.
4. Válassza a **Gép leállítása a feladatátvétel megkezdése előtt** lehetőséget. Ezzel a beállítással Site Recovery a feladatátvétel elindítása előtt megkísérli leállítani a forrás gépet. A feladatátvétel azonban akkor is folytatódik, ha a Leállítás sikertelen. 
5. A feladatátvételi művelet elindításához kattintson az **OK** gombra. A feladatátvételi folyamatot a **Feladatok** lapon követheti nyomon.
6. A feladatátvétel befejezését követően az Azure-beli virtuális gép replikája megjelenik az Azure Portal > **Virtuális gépek** területen. Ha a feladatátvételt követően előkészítette a csatlakozást, győződjön meg arról, hogy a virtuális gép a megfelelő méretű, a megfelelő hálózathoz csatlakozik, és fut.
7. A virtuális gép ellenőrzése után kattintson a **véglegesítés** gombra a feladatátvétel befejezéséhez. Ezzel törli az összes rendelkezésre álló helyreállítási pontot.

> [!WARNING]
> Ne szakítsa meg a folyamatban lévő feladatátvételt: A feladatátvétel indítása előtt a virtuális gép replikációja leáll. Ha megszakítja a folyamatban lévő feladatátvételt, az leáll, a virtuális gép replikációja azonban nem folytatódik.


### <a name="fail-back-to-azure-stack"></a>Feladat-visszavétel Azure Stack

Ha az elsődleges hely újra működik, az Azure-ból Azure Stackra is visszatérhet. Ehhez le kell töltenie az Azure virtuális gép virtuális merevlemezét, és fel kell töltenie Azure Stackba.

1. Állítsa le az Azure-beli virtuális gépet, hogy a VHD letölthető legyen. 
2. A virtuális merevlemez letöltésének megkezdéséhez telepítse a [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).
3. Navigáljon a virtuális gépre az Azure Portalon (a virtuális gép nevének használatával).
4. A **lemezek**területen kattintson a lemez nevére, és Gyűjtse össze a beállításokat.

    - A tesztben https://502055westcentralus.blob.core.windows.net/wahv9b8d2ceb284fb59287/copied-3676553984.vhd használt VHD URI-azonosító például lebontható a virtuális merevlemez letöltéséhez használt következő bemeneti paraméterek beszerzéséhez.
        - Tárfiók: 502055westcentralus
        - Tároló: wahv9b8d2ceb284fb59287
        - VHD neve: copied-3676553984. vhd

5. Most Azure Storage Explorer használatával töltse le a VHD-t.
6. Töltse fel a virtuális merevlemezt Azure Stack a [következő lépésekkel](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-manage-vm-disks#use-powershell-to-add-multiple-unmanaged-disks-to-a-vm).
7. A meglévő virtuális gépen vagy az új virtuális gépen csatolja a feltöltött virtuális merevlemezeket.
8. Győződjön meg arról, hogy az operációsrendszer-lemez helyes, és indítsa el a virtuális gépet.


Ebben a fázisban a feladat-visszavétel befejeződött.


## <a name="conclusion"></a>Összegzés

Ebben a cikkben Azure Stack virtuális gépeket replikált az Azure-ba. A replikáció a helyén egy vész-helyreállítási részletezést futtatott, amely gondoskodik arról, hogy az Azure-beli feladatátvétel az elvárásoknak megfelelően működik. A cikk a teljes feladatátvétel Azure-ba való futtatásának lépéseit is tartalmazza, és visszakerül a Azure Stackra.

## <a name="next-steps"></a>További lépések

A feladat-visszavételt követően újra védetté teheti a virtuális gépet, és megkezdheti az Azure-ba való replikálást. ehhez ismételje meg a jelen cikkben ismertetett lépéseket.

