---
title: Helyszíni VMware virtuális gépek áttelepítése az Azure-ba az ügynök nélküli Azure Migrate kiszolgáló áttelepítésével | Microsoft Docs
description: Ismerteti, hogyan végezhető el a helyszíni VMware virtuális gépek Azure-ba történő áttelepítése és ügynök nélküli migrálása az Azure Migrate használatával.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/08/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 7fba9cbbaa15359e7e4dd95e66645dd5f4022431
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68640771"
---
# <a name="migrate-vmware-vms-to-azure-agentless"></a>VMware virtuális gépek migrálása az Azure-ba (ügynök nélkül)

Ez a cikk bemutatja, hogyan telepítheti át a helyszíni VMware virtuális gépeket az Azure-ba a Azure Migrate Server áttelepítési eszközzel az ügynök nélküli áttelepítés használatával.

A [Azure Migrate](migrate-services-overview.md) egy központi központot biztosít a helyszíni alkalmazások és munkaterhelések, valamint az AWS/GCP virtuálisgép-példányok felderítésének, értékelésének és áttelepítésének nyomon követéséhez az Azure-ban. Az elosztó Azure Migrate eszközöket biztosít az értékeléshez és az áttelepítéshez, valamint a harmadik féltől származó független szoftvergyártók (ISV) ajánlatokhoz.

Ez az oktatóanyag egy sorozat harmadik része, amely bemutatja, hogyan lehet felmérni és áttelepíteni a VMware virtuális gépeket az Azure-ba Azure Migrate Server Assessment és Migration használatával. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Virtuális gépek előkészítése áttelepítésre.
> * Adja hozzá az Azure áttelepítési kiszolgáló áttelepítési eszközét.
> * Fedezze fel az áttelepíteni kívánt virtuális gépeket.
> * Indítsa el a virtuális gépek replikálását.
> * Futtasson egy teszt-áttelepítést, és győződjön meg róla, hogy minden a várt módon működik-e.
> * Futtasson egy teljes körű virtuálisgép-áttelepítést.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="migration-methods"></a>Áttelepítési módszerek

A VMware virtuális gépeket áttelepítheti az Azure-ba az Azure Migrate Server áttelepítési eszköz használatával. Ez az eszköz néhány lehetőséget kínál a VMware virtuális gépek áttelepítésére:

- Áttelepítés ügynök nélküli replikálással. Telepítse át a virtuális gépeket anélkül, hogy bármit telepítenie kellene rájuk.
- Áttelepítés ügynökkel a replikáláshoz. Telepítsen egy ügynököt a virtuális gépre a replikáláshoz.

Annak eldöntéséhez, hogy ügynök nélkül vagy ügynökön alapuló áttelepítést szeretne-e használni, tekintse át a következő cikkeket:

- Megtudhatja, hogyan működik az ügynök nélküli áttelepítés, és [Hogyan](server-migrate-overview.md) [hasonlítható össze](server-migrate-overview.md#compare-migration-methods)az áttelepítési módszerek.
- Ha az ügynök-alapú módszert szeretné használni, [olvassa el ezt a cikket](tutorial-migrate-vmware-agent.md) .

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

1. [Ismerje](migrate-architecture.md) meg a VMware áttelepítési architektúrát.
2. [A sorozat első oktatóanyagának végrehajtásával](tutorial-prepare-vmware.md) állítsa be az Azure-t és a VMware-et az áttelepítéshez. Ebben az oktatóanyagban a következőket kell tennie:
    - [Készítse elő az Azure](tutorial-prepare-vmware.md#prepare-azure) -t áttelepítésre.
    - [Készítse elő a helyszíni környezetet](tutorial-prepare-vmware.md#prepare-for-agentless-vmware-migration) az áttelepítéshez.
    
3. Javasoljuk, hogy az Azure-ba való Migrálás előtt próbálja meg a VMware virtuális gépek felmérését Azure Migrate Server Assessment használatával. Az értékelés beállításához [hajtsa végre a második oktatóanyagot](tutorial-assess-vmware.md) ebben a sorozatban. Ha nem szeretné felmérni a virtuális gépeket, kihagyhatja ezt az oktatóanyagot. Bár javasoljuk, hogy próbálja ki az értékelést, de az áttelepítés megkezdése előtt nem kell értékelést futtatnia.



## <a name="add-the-azure-migrate-server-migration-tool"></a>A Azure Migrate Server áttelepítési eszköz hozzáadása

Ha nem követte a VMware virtuális gépek felmérésére szolgáló második oktatóanyagot, [kövesse ezeket az utasításokat](how-to-add-tool-first-time.md) Azure Migrate projekt beállításához, majd válassza a Azure Migrate Server áttelepítési eszközt. 

Ha követte a második oktatóanyagot, és már beállított egy Azure Migrate projektet, adja hozzá a Azure Migrate Server áttelepítési eszközt a következő módon:

1. A Azure Migrate projektben kattintson az **Áttekintés**elemre. 
2. A **felderítési, felmérési és áttelepítési kiszolgálók**területen kattintson a **kiszolgálók felmérése és áttelepítése**elemre.

     ![Kiszolgálók értékelése és migrálása](./media/tutorial-migrate-vmware/assess-migrate.png)

3. Az **áttelepítési eszközök**területen válassza **a kattintson ide az áttelepítési eszköz hozzáadásához, amikor készen áll az**áttelepítésre.

    ![Eszköz kiválasztása](./media/tutorial-migrate-vmware/select-migration-tool.png)

4. Az eszközök listában válassza **a Azure Migrate: Kiszolgáló áttelepítése** > **eszköz hozzáadása**

    ![Kiszolgáló áttelepítési eszköze](./media/tutorial-migrate-vmware/server-migration-tool.png)

## <a name="set-up-the-azure-migrate-appliance"></a>A Azure Migrate berendezés beállítása

Azure Migrate kiszolgáló áttelepítése egy könnyű VMware VM-készüléket futtat. A készülék virtuálisgép-felderítést végez, és a virtuális gépek metaadatait és teljesítményadatait elküldi Azure Migrate kiszolgáló áttelepítésére. Ugyanezt a készüléket a Azure Migrate Server Assessment eszköz is használja.

Ha követte a VMware virtuális gépek felmérésére szolgáló második oktatóanyagot, az oktatóanyag során már beállította a készüléket. Ha nem követi ezt az oktatóanyagot, most be kell állítania a készüléket. Ehhez tegye a következőket: 

- Töltse le a petesejtek sablon fájlját, és importálja vCenter Serverba.
- Hozza létre a készüléket, és győződjön meg róla, hogy tud kapcsolódni Azure Migrate Server Assessmenthez. 
- Konfigurálja a készüléket első alkalommal, és regisztrálja a Azure Migrate projekttel.

A berendezés beállításához kövesse a [cikk](how-to-set-up-appliance-vmware.md) utasításait.


## <a name="prepare-vms-for-migration"></a>Virtuális gépek előkészítése áttelepítésre

Azure Migrate megköveteli, hogy a virtuális gépek áttelepíthetők legyenek az Azure-ba.

- Egyes operációs rendszerek esetében a Azure Migrate automatikusan végrehajtja ezeket a módosításokat. [További információ](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements)
- Ha olyan virtuális gépet telepít át, amely nem rendelkezik ezekkel az operációs rendszerekkel, kövesse az utasításokat a virtuális gép előkészítéséhez.
- Az áttelepítés megkezdése előtt fontos, hogy elvégezze ezeket a módosításokat. Ha a módosítás előtt áttelepíti a virtuális gépet, előfordulhat, hogy a virtuális gép nem indul el az Azure-ban.
- A helyszíni virtuális gépeken végrehajtott konfigurációs módosításokat az Azure-ba replikálja a virtuális gép replikálásának engedélyezése után. A módosítások replikálásának ellenőrzéséhez győződjön meg arról, hogy az áttelepített helyreállítási pont későbbi, mint amikor a konfiguráció a helyszínen történt.


### <a name="prepare-windows-server-vms"></a>Windows Server rendszerű virtuális gépek előkészítése

**Művelet** | **Részletek** | **Utasításokat**
--- | --- | ---
Győződjön meg arról, hogy az Azure virtuális gépen futó Windows-kötetek ugyanazt a meghajtóbetűjel-hozzárendelést használják, mint a helyszíni virtuális gép. | Konfigurálja a TÁROLÓHÁLÓZATI szabályzatot online állapotba. | 1. Jelentkezzen be a virtuális gépre egy rendszergazdai fiókkal, és nyisson meg egy parancssori ablakot.<br/> 2. Írja be a **DiskPart** parancsot a DiskPart segédprogram futtatásához.<br/> 3. **San-házirend típusa = OnlineAll**<br/> 4. Írja be a Kilépés parancsot a DiskPart elhagyásához, majd zárja be a parancssort.
Azure soros hozzáférési konzol engedélyezése az Azure-beli virtuális géphez | Ez segít a hibaelhárításban. Nem kell újraindítani a virtuális gépet. Az Azure-beli virtuális gép a lemezkép használatával fog elindulni, és ez egyenértékű az új virtuális gép újraindításával. | Az engedélyezéshez kövesse az [alábbi utasításokat](https://docs.microsoft.com/azure/virtual-machines/windows/serial-console) .
A Hyper-V vendég integrációjának telepítése | Ha Windows Server 2003 rendszert futtató gépeket telepít át, telepítse a Hyper-V vendég integrációs szolgáltatásait a VM operációs rendszerbe. | [További információk](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#install-or-update-integration-services).
Távoli asztal | Engedélyezze a Távoli asztalt a virtuális gépen, és győződjön meg arról, hogy a Windows tűzfal nem blokkolja a hálózati profilok Távoli asztal elérését. | [További információk](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access).

### <a name="prepare-linux-vms"></a>Linux rendszerű virtuális gépek előkészítése

**Művelet** | **Részletek** 
--- | --- | ---
A Hyper-V Linux Integration Services telepítése | Alapértelmezés szerint a Linux-disztribúciók legtöbb új verziója szerepel.
A Linux init-rendszerkép újraépítése a szükséges Hyper-V-illesztőprogramok tárolására | Ez biztosítja, hogy a virtuális gép az Azure-ban induljon el, és csak bizonyos disztribúciókban szükséges.
Az Azure soros konzol naplózásának engedélyezése | Ez segít a hibaelhárításban. Nem kell újraindítani a virtuális gépet. Az Azure-beli virtuális gép a lemezkép használatával fog elindulni, és ez egyenértékű az új virtuális gép újraindításával.<br/> Az engedélyezéshez kövesse az [alábbi utasításokat](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console) .
Eszköz-hozzárendelési fájl frissítése | Frissítse az eszköz nevét a mennyiségi társítások számára, hogy állandó eszköz-azonosítókat használjon.
Fstab-bejegyzések frissítése | Frissítse a bejegyzéseket az állandó kötet-azonosítók használatára.
Udev-szabály eltávolítása | Távolítson el minden olyan udev-szabályt, amely Mac-címeken alapuló kapcsolati neveket foglal le, stb.
Hálózati adapterek frissítése | A hálózati adapterek frissítése a DHCP-alapú IP-cím fogadására.
SSH engedélyezése | Győződjön meg arról, hogy az SSH engedélyezve van, és az sshd szolgáltatás automatikusan elindul az újraindításkor.<br/> Győződjön meg arról, hogy az operációs rendszer tűzfala nem blokkolja a bejövő SSH-kapcsolatok kéréseit.

[Kövesse ezt a cikket](https://docs.microsoft.com/azure/virtual-machines/linux/create-upload-generic) , amely a Linux RENDSZERű virtuális gépek Azure-ban való futtatásának lépéseit ismerteti, valamint néhány népszerű Linux-disztribúcióra vonatkozó utasításokat is tartalmaz.  


## <a name="replicate-vms"></a>Virtuális gépek replikálása

A felderítés befejeződése után megkezdheti a VMware virtuális gépek replikálását az Azure-ba.

1. A Azure Migrate projekt > **kiszolgálók** **Azure Migrate: Kiszolgáló áttelepítése**, kattintson a **replikálás**gombra.

    ![Virtuális gépek replikálása](./media/tutorial-migrate-vmware/select-replicate.png)

2. A **replikálás**során > a **forrás beállításait** > **a gépek virtualizálják?** területen válassza **az igen, a VMware vSphere**lehetőséget.
3. A helyszíni **készülék**területen válassza ki **a > által**beállított Azure Migrate-berendezés nevét. 

    ![Forrás beállításai](./media/tutorial-migrate-vmware/source-settings.png)

    - Ez a lépés azt feltételezi, hogy már beállított egy berendezést az oktatóanyag befejezése után.
    - Ha még nem állított be készüléket, kövesse az [ebben a cikkben](how-to-set-up-appliance-vmware.md)szereplő utasításokat.

4. A **virtuális gépek**területen válassza ki a replikálni kívánt gépeket.
    - Ha a virtuális gépek értékelését futtatja, a kiértékelési eredmények alapján a virtuálisgép-méretezési és a lemez típusa (prémium/standard) javaslatok is alkalmazhatók. Ehhez az áttelepítési **Beállítások importálása Azure Migrate értékelésből?** területen válassza az **Igen** lehetőséget.
    - Ha nem futtatott értékelést, vagy nem szeretné használni az értékelési beállításokat, válassza a **nincs** lehetőséget.
    - Ha az értékelés használatát választotta, válassza ki a virtuálisgép-csoportot és az értékelés nevét.

    ![Értékelés kiválasztása](./media/tutorial-migrate-vmware/select-assessment.png)

5. A **Virtual Machines**szolgáltatásban szükség szerint keresse meg a virtuális gépeket, és tekintse meg az áttelepíteni kívánt virtuális gépeket. Ezután kattintson **a Tovább gombra: Cél beállításai**

    ![Virtuális gépek kiválasztása](./media/tutorial-migrate-vmware/select-vms.png)

6. A **cél beállításai**területen válassza ki azt az előfizetést és célcsoportot, amelyre az áttelepítést végezni szeretné, majd adja meg azt az erőforráscsoportot, amelyben az Azure-beli virtuális gépek a Migrálás után lesznek tárolva. **Virtual Network**válassza ki azt az Azure VNet/alhálózatot, amelyhez az Azure-beli virtuális gépek csatlakozni fognak az áttelepítés után.
7. **Azure Hybrid Benefit**:

    - Válassza a **nem** lehetőséget, ha nem kívánja alkalmazni a Azure Hybrid Benefit. Ezután kattintson a **Next** (Tovább) gombra.
    - Válassza az **Igen** lehetőséget, ha olyan Windows Server-gépekkel rendelkezik, amelyek aktív frissítési garanciával vagy Windows Server-előfizetésekkel rendelkeznek, és az áttelepíteni kívánt gépekre alkalmazni kívánja a kedvezményt. Ezután kattintson a **Next** (Tovább) gombra.

    ![Cél beállításai](./media/tutorial-migrate-vmware/target-settings.png)

8. A **számításban**tekintse át a virtuális gép nevét, a méretet, az operációsrendszer-lemez típusát és a rendelkezésre állási készletet. A virtuális gépeknek meg kell felelniük az [Azure-követelményeknek](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements).

    - **Virtuális gép mérete**: Ha értékelési javaslatokat használ, a virtuális gép mérete legördülő lista az ajánlott méretet fogja tartalmazni. Ellenkező esetben Azure Migrate az Azure-előfizetés legközelebbi egyezése alapján kell megkeresnie egy méretet. Alternatív megoldásként válasszon egy manuális méretet az Azure-beli **virtuális gép méretében**. 
    - **Operációsrendszer-lemez**: A virtuális gép operációsrendszer-(rendszerindító) lemezének megadása. Az operációsrendszer-lemez az operációs rendszer rendszerbetöltőjét és telepítőjét tartalmazó lemez. 
    - **Rendelkezésre állási csoport**: Ha a virtuális gépnek az áttelepítés után Azure-beli rendelkezésre állási csoportba kell tartoznia, adja meg a készletet. A készletnek az áttelepítéshez megadott cél erőforráscsoporthoz kell lennie.

    ![VIRTUÁLIS gépek számítási beállításai](./media/tutorial-migrate-vmware/compute-settings.png)

9. A **lemezek**területen adja meg, hogy a virtuálisgép-lemezeket az Azure-ba kell-e replikálni, majd válassza ki a lemez típusát (standard SSD/HDD vagy prémium szintű Managed Disks) az Azure-ban. Ezután kattintson a **Next** (Tovább) gombra.
    - A lemezeket kizárhatja a replikációból.
    - Ha kizárja a lemezeket, a Migrálás után nem jelennek meg az Azure-beli virtuális gépen. 

    ![Lemezek](./media/tutorial-migrate-vmware/disks.png)

10. A **replikáció áttekintése és elindítása**területen tekintse át a beállításokat, majd kattintson a **replikálás** gombra a kiszolgálók kezdeti replikálásának elindításához.

> [!NOTE]
> A replikálási beállításokat bármikor frissítheti a replikálás megkezdése előtt, a**replikálási gépek** **kezelése** > területen. A beállítások nem módosíthatók a replikáció elindítása után.

### <a name="provisioning-for-the-first-time"></a>Kiépítés első alkalommal

Ha ez az első olyan virtuális gép, amelyet a Azure Migrate projektben replikál, Azure Migrate a kiszolgáló áttelepítése automatikusan kiosztja ezeket az erőforrásokat a projekttel azonos erőforráscsoporthoz.

- **Service Bus**: Azure Migrate kiszolgáló áttelepítése a Service Bus használatával küld replikálási előkészítési üzeneteket a berendezésnek.
- **Átjáró Storage**-fiókja: A kiszolgáló áttelepítése az átjáró Storage-fiók használatával tárolja a replikált virtuális gépekre vonatkozó állapotinformációkat.
- **Log Storage-fiók**: A Azure Migrate készülék feltölti a virtuális gépek replikációs naplóit a log Storage-fiókba. Azure Migrate a replikációs adatokat a replika felügyelt lemezekre alkalmazza.
- **Key Vault**: A Azure Migrate készülék a kulcstartó használatával kezeli a Service Bus kapcsolati karakterláncait, valamint a replikáció során használt Storage-fiókok hozzáférési kulcsait. Be kell állítania azokat az engedélyeket, amelyeket a kulcstartónak el kell érnie ahhoz, hogy előkészítse a Storage-fiókot. [Tekintse át ezeket az engedélyeket](tutorial-prepare-vmware.md#assign-role-assignment-permissions).   


## <a name="track-and-monitor"></a>Nyomon követés és figyelés


- Ha rákattint a **replikálás** indítása a replikálási feladatokhoz lehetőségre. 
- Ha a replikáció elindítása feladatainak végrehajtása sikeresen befejeződött, a gépek megkezdik a kezdeti replikálást az Azure-ba.
- A kezdeti replikálás során létrejön egy virtuális gép pillanatképe. A pillanatképből származó lemezes adatok replikálódnak a replika felügyelt lemezekre az Azure-ban.
- A kezdeti replikálás befejeződése után a különbözeti replikáció megkezdődik. A helyszíni lemezek növekményes módosításait a rendszer rendszeres időközönként replikálja az Azure-beli replika-lemezekre.

A feladatok állapotát a portál értesítéseiben követheti nyomon.

A replikálási állapot figyeléséhez kattintson a **kiszolgálók** **replikálásához Azure Migrate: Kiszolgáló áttelepítése**.
![Replikáció figyelése](./media/tutorial-migrate-vmware/replicating-servers.png)




## <a name="run-a-test-migration"></a>Migrálási teszt futtatása


A különbözeti replikáció megkezdése előtt futtasson egy teszt-áttelepítést a virtuális gépek számára, mielőtt az Azure-ba történő teljes áttelepítést futtatná. Azt javasoljuk, hogy az áttelepítés előtt legalább egyszer végezze el ezt az egyes gépeken.

- Az áttelepítési teszt futtatása ellenőrzi, hogy az áttelepítés a várt módon fog-e működni, anélkül, hogy ez hatással lenne a helyszíni gépekre, amelyek továbbra is működőképesek maradnak. 
- A tesztelési áttelepítés szimulálja az áttelepítést egy Azure-beli virtuális gép replikálási adataival történő létrehozásával (általában az Azure-előfizetése nem üzemi VNet való áttelepítéssel).
- A replikált Azure-beli virtuális gép használatával ellenőrizheti az áttelepítést, elvégezheti az alkalmazások tesztelését, és bármilyen problémát megtehet a teljes áttelepítés előtt.

Végezzen el egy teszt-áttelepítést a következőképpen:


1. Az **áttelepítési célok** > **kiszolgálói** > **Azure Migrate: Kiszolgáló áttelepítése**, kattintson az áttelepített **kiszolgálók tesztelése**elemre.

     ![Végrehajtott migrálási tesztű kiszolgálók](./media/tutorial-migrate-vmware/test-migrated-servers.png)

2. Kattintson a jobb gombbal a tesztelni kívánt virtuális gépre, majd kattintson az **áttelepítés tesztelése**elemre.

    ![Migrálás tesztelése](./media/tutorial-migrate-vmware/test-migrate.png)

3. Az **áttelepítés tesztelése**lapon válassza ki azt az Azure-VNet, amelyben az Azure-beli virtuális gép az áttelepítés után lesz elhelyezve. Javasoljuk, hogy használjon nem üzemi VNet.
4. Elindul a **teszt** áttelepítési feladata. Figyelje a feladatot a portál értesítéseiben.
5. Az áttelepítés befejeződése után tekintse meg az áttelepített Azure-beli virtuális gépet a Azure Portal Virtual Machinesban. A számítógépnév utótag **-teszt**.
6. A teszt után kattintson a jobb gombbal az Azure-beli virtuális gépre a **replikáló gépek**területen, majd kattintson a teszt áttelepítésének **tisztítása**elemre.

    ![Migrálás törlése](./media/tutorial-migrate-vmware/clean-up.png)


## <a name="migrate-vms"></a>Virtuális gépek áttelepítése

Miután ellenőrizte, hogy a teszt áttelepítése a várt módon működik-e, áttelepítheti a helyszíni gépeket.

1. A Azure Migrate Project > **Servers** > **Azure Migrate: Kiszolgáló áttelepítése**, kattintson a **kiszolgálók replikálása**elemre.

    ![Kiszolgálók replikálása](./media/tutorial-migrate-vmware/replicate-servers.png)

2. A **gépek replikálásához**kattintson a jobb gombbal a virtuális gépre> Migrálás elemre.
3. Az **áttelepítés** > során**állítsa le a virtuális gépeket, és végezze el az adatvesztés nélküli tervezett**áttelepítést, válassza az **Igen** > **OK**lehetőséget.
    - Alapértelmezés szerint Azure Migrate leállítja a helyszíni virtuális gépet, és igény szerinti replikálást futtat a legutóbbi replikáció óta bekövetkezett virtuális gépek változásainak szinkronizálásához. Ez biztosítja az adatvesztést.
    - Ha nem szeretné leállítani a virtuális gépet, válassza a **nem** lehetőséget.
4. Egy áttelepítési feladatot indít a virtuális gép számára. A feladatot nyomon követheti az Azure-értesítésekben.
5. A művelet befejezése után megtekintheti és kezelheti a virtuális gépet a **Virtual Machines** lapon.

## <a name="complete-the-migration"></a>A migrálás befejezése

1. Az áttelepítés befejezése után kattintson a jobb gombbal a virtuális gépre > az **áttelepítés leállítása**elemre. Ez leállítja a helyszíni gép replikálását, és megtisztítja a virtuális gép replikációs állapotával kapcsolatos információkat.
2. Telepítse az Azure-beli VM [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) -vagy [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) -ügynököt az áttelepített gépekre.
3. Hajtson végre minden áttelepítés utáni módosítást az alkalmazáson (például adatbázis-kapcsolati sztringek frissítése és webes kiszolgálók konfigurálása).
4. Végezze el a végső alkalmazás- és áttelepítés-elfogadás teszteket az Azure-on jelenleg futó alkalmazásoknál.
5. Az áttelepített Azure virtuálisgép-példány felé irányuló forgalom kivágása.
6. Távolítsa el a helyszíni virtuális gépeket a helyi virtuálisgép-készletéből.
7. Távolítsa el a helyszíni virtuális gépeket helyi biztonsági mentésekből.
8. Frissítse minden belső dokumentációját az Azure virtuális gépek új helyével és IP-címével. 

## <a name="post-migration-best-practices"></a>Az áttelepítés utáni ajánlott eljárások

- A nagyobb rugalmasság érdekében:
    - Biztonságba helyezheti az adatokat, ha biztonsági másolatot készít az Azure virtuális gépekről az Azure Backup szolgáltatással. [További információk](../backup/quick-backup-vm-portal.md).
    - Biztosíthatja a számítási feladatok folyamatos futtatását és rendelkezésre állását, ha az Azure virtuális gépeket egy másodlagos régióba replikálja a Site Recovery használatával. [További információk](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- A biztonság fokozása érdekében:
    - Zárolja és korlátozza a bejövő adatforgalom elérését [Azure Security Center – igény](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)szerinti felügyelettel.
    - Korlátozza a forgalmat felügyeleti végpontokra [hálózati biztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/security-overview) használatával.
    - Az [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview) üzembe helyezésével biztonságba helyezheti a lemezeket, és megóvhatja az adatokat a lopási kísérletektől és a jogosulatlan hozzáféréstől.
    - Látogasson el a [az Azure Security Center](https://azure.microsoft.com/services/security-center/) webhelyére, és tudjon meg többet az [IaaS-erőforrások biztosításáról](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/).
- Figyelési és felügyeleti eszközök:
-  Fontolja meg az [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview) üzembe helyezését az erőforrás-használat és a költségek figyeléséhez.


## <a name="next-steps"></a>További lépések

Vizsgálja meg a [felhőalapú migrációs utat](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate) az Azure Cloud bevezetési keretrendszerében.
