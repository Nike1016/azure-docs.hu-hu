---
title: Azure-beli virtuális gépek vészhelyreállítási próbájának végrehajtása egy másodlagos Azure-régióba az Azure Site Recovery szolgáltatás használatával
description: Ismerje meg, hogyan hajthatja végre az Azure-beli virtuális gépek vészhelyreállítási próbáját egy másodlagos Azure-régióba az Azure IaaS virtuális gépekhez az Azure Site Recovery szolgáltatás használatával.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 08/05/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: a4f2b2ff1c42d18626fb2be27438b2f499ee9368
ms.sourcegitcommit: f7998db5e6ba35cbf2a133174027dc8ccf8ce957
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/05/2019
ms.locfileid: "68782630"
---
# <a name="run-a-disaster-recovery-drill-for-azure-vms-to-a-secondary-azure-region"></a>Azure-beli virtuális gépek vészhelyreállítási próbájának végrehajtása egy másodlagos Azure-régióba

Az [Azure Site Recovery](site-recovery-overview.md) szolgáltatás működőképes és elérhető állapotban tartja az üzleti alkalmazásokat a tervezett és nem tervezett leállások idején, így segít a vállalatoknak az üzletmenet-folytonossági és vészhelyreállítási (BCDR) stratégia megvalósításában. A Site Recovery felügyeli és koordinálja a helyszíni gépek és az Azure-beli virtuális gépek vészhelyreállítását, beleértve a replikálást, a feladatátvételt és a helyreállítást.

Ez az oktatóanyag azt ismerteti, hogy hogyan hajthat végre vészhelyreállítási próbát egy Azure-beli virtuális gép esetében az egyik Azure-régióból a másikba egy feladatátvételi teszt segítségével. A próba adatveszteség és állásidő nélkül ellenőrzi a replikációs stratégiáját, és nincs hatással az éles környezetre. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az előfeltételek ellenőrzése
> * Feladatátvételi teszt futtatása egyetlen virtuális gép esetén

> [!NOTE]
> Ennek az oktatóanyagnak a célja, hogy végigvezesse a felhasználót a vészhelyreállítási próba folyamatán a lehető legkevesebb lépésben. Ha részletesebben szeretné megismerni a vészhelyreállítási próba végrehajtásával kapcsolatos különféle szempontokat, beleértve a hálózatkezelést, az automatizálást és a hibaelhárítást, tekintse meg az Azure-beli virtuális gépekre vonatkozó útmutatók között található dokumentációt.

## <a name="prerequisites"></a>Előfeltételek

- Javasoljuk, hogy a feladatátvételi teszt futtatása előtt ellenőrizze a virtuális gép tulajdonságait, hogy biztosan minden a várt módon működjön.  Lépjen a virtuális gép tulajdonságaihoz a **Replikált elemek** területen. Az **Alapvető erőforrások** panel megjeleníti a gépek beállításaira és állapotára vonatkozó információkat.
- **Javasoljuk, hogy a feladatátvételi teszthez a replikáció engedélyezésekor beállított alapértelmezett hálózat helyett használjon egy külön Azure-beli virtuálisgép-hálózatot**.


## <a name="run-a-test-failover"></a>Feladatátvételi teszt futtatása

1. A **Beállítások** > **Replikált elemek** területen kattintson a virtuális gépre, majd a **+Feladatátvételi teszt** ikonra.

2. A **Feladatátvételi teszt** területen válasszon ki egy helyreállítási pontot a feladatátvétel végrehajtásához:

   - **Legutóbb feldolgozott**: A virtuális gép feladatátvétele a Site Recovery szolgáltatás által feldolgozott legutóbbi helyreállítási pontra meghiúsul. Megjelenik az időbélyeg. Ezzel a beállítással a rendszer nem tölt időt az adatok feldolgozásával, így a helyreállítási időre vonatkozó célkitűzés (RTO) alacsony.
   - **Legújabb alkalmazás – konzisztens**: Ez a beállítás az összes virtuális gépet átadja a legújabb, alkalmazás-konzisztens helyreállítási pontra. Megjelenik az időbélyeg.
   - **Egyéni**: Válassza ki a kívánt helyreállítási pontot.

3. Válassza ki, hogy az Azure-beli virtuális gépek mely másodlagos régióban található cél Azure-beli virtuális hálózathoz csatlakozzanak majd a feladatátvételt követően.

4. A feladatátvétel indításához kattintson az **OK** gombra. A folyamat előrehaladásának megtekintéséhez a virtuális gépre kattintva megnyithatja a tulajdonságait. Vagy kattintson a **Feladatátvételi teszt** feladatra a tároló neve > **Beállítások** > **Feladatok** > **Site Recovery-feladatok** menüpontban.
5. A feladatátvétel befejezését követően az Azure-beli virtuális gép replikája megjelenik az Azure Portal > **Virtuális gépek** területen. Győződjön meg arról, hogy a virtuális gép fut, megfelelő a méretezése és a megfelelő hálózathoz csatlakozik.
6. A feladatátvételi teszt során létrehozott virtuális gépek törléséhez kattintson **Feladatátvételi teszt eltávolítása** elemre a replikált elemen vagy a helyreállítási terven. A **Jegyzetek** területen jegyezheti fel és mentheti a feladatátvételi teszttel kapcsolatos megfigyeléseket.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Éles feladatátvétel futtatása](azure-to-azure-tutorial-failover-failback.md)
