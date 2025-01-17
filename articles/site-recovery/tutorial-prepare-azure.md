---
title: Az Azure előkészítése helyszíni gépek vészhelyreállításához az Azure Site Recoveryvel | Microsoft Docs
description: Ismerje meg, hogyan készítheti elő az Azure-t a helyszíni gépek vészhelyreállításához az Azure Site Recovery használatával.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 87216317a965e85d83aede468163f43b1716c45a
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68827236"
---
# <a name="prepare-azure-resources-for-disaster-recovery-of-on-premises-machines"></a>Azure-erőforrások előkészítése helyszíni gépek vészhelyreállításához

Ez a cikk az Azure-erőforrások és-összetevők előkészítését ismerteti, így a helyszíni VMware virtuális gépek, Hyper-V virtuális gépek vagy Windows/Linux rendszerű fizikai kiszolgálók vész-helyreállítását az Azure-ba állíthatja be a [Azure site Recovery](site-recovery-overview.md) szolgáltatás használatával.

Ez az oktatóanyag az első rész abban a sorozatban, amely bemutatja, hogyan állíthat be vészhelyreállítást helyszíni virtuális gépekhez. 


Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Győződjön meg arról, hogy az Azure-fiók replikációs engedélyekkel rendelkezik.
> * Recovery Services-tároló létrehozása. A tároló virtuális gépek és más replikációs összetevők metaadatait és konfigurációs adatait tárolja.
> * Azure-beli virtuális hálózat (VNet) beállítása. Ha az Azure-beli virtuális gépek a feladatátvételt követően jönnek létre, azok ehhez a hálózathoz csatlakoznak.

> [!NOTE]
> Az oktatóanyagok a forgatókönyvek legegyszerűbb telepítési útvonalát mutatják be. Ahol lehet, az alapértelmezett beállításokat használják, és nem mutatják be az összes lehetséges beállítást és útvonalat. Részletes utasításokért tekintse át a Site Recovery tartalomjegyzékének útmutató című cikkét.

## <a name="before-you-start"></a>Előkészületek

- Tekintse át a [VMware](vmware-azure-architecture.md), a [Hyper-V](hyper-v-azure-architecture.md)és a [fizikai kiszolgáló](physical-azure-architecture.md) architektúráját a vész-helyreállításhoz.
- Gyakori kérdések a [VMware](vmware-azure-common-questions.md) -ről és a [Hyper-V-](hyper-v-azure-common-questions.md) ről

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) a virtuális gép létrehozásának megkezdése előtt. Ezután jelentkezzen be a [Azure Portalba](https://portal.azure.com).


## <a name="verify-account-permissions"></a>Fiókengedélyek ellenőrzése

Ha most hozta létre az ingyenes Azure-fiókját, akkor Ön az előfizetés rendszergazdája, és rendelkezik a szükséges engedélyekkel. Ha nem Ön az előfizetés rendszergazdája, akkor a rendszergazdával együttműködve rendelje hozzá a fiókhoz a szükséges engedélyeket. Egy új virtuális gép replikálásának engedélyezéséhez a következő engedélyekkel kell rendelkeznie:

- Virtuális gépek létrehozása a kiválasztott erőforráscsoportban.
- Virtuális gépek létrehozása a kiválasztott virtuális hálózaton.
- Írás egy Azure Storage-fiókba.
- Írás egy Azure-beli felügyelt lemezre.

A feladatok végrehajtásához az Ön fiókjának rendelkeznie kell a Virtuális gépek közreműködője beépített szerepkörrel. Emellett a fiókhoz hozzá kell rendelni a Site Recovery-közreműködő beépített szerepkört is, a Site Recovery-műveletek tárolókban való kezeléséhez.


## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

1. A Azure Portal kattintson az **+ erőforrás létrehozása**lehetőségre, és keressen rá a piactéren a **helyreállításhoz**.
2. Kattintson a **biztonsági mentés és a site Recovery**elemre, majd a biztonsági mentés és a site Recovery lapon kattintson a **Létrehozás**gombra. 
1. A **Recovery Services** > tároló**neve**mezőben adjon meg egy rövid nevet a tároló azonosításához. Ehhez az oktatóanyag-sorozathoz a **ContosoVMVault** nevet használjuk.
2. Az **erőforráscsoport**területen válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy újat. Ebben az oktatóanyagban a **contosoRG**-ot használjuk.
3. A **hely**mezőben válassza ki azt a régiót, amelyben a tárolót el szeretné helyezni. a **Nyugat-Európa** beállítást használjuk.
4. Ha szeretne gyorsan hozzáférni az új tárolóhoz az irányítópultról, válassza a **Rögzítés az irányítópulton** > **Létrehozás** elemet.

   ![Új tároló létrehozása](./media/tutorial-prepare-azure/new-vault-settings.png)

   Az új tároló megjelenik az **Irányítópult** > **Minden erőforrás** menüben, illetve a központi **Recovery Services-tárolók** lapon.

## <a name="set-up-an-azure-network"></a>Azure-hálózat beállítása

A helyszíni gépek replikálódnak az Azure Managed Disks szolgáltatásba. Feladatátvétel esetén az Azure-beli virtuális gépek ezekből a felügyelt lemezekről jönnek létre, és az ebben az eljárásban megadott Azure-hálózathoz csatlakoznak.

1. Az [Azure Portalon](https://portal.azure.com) válassza az **Erőforrás létrehozása** > **Hálózatkezelés** > **Virtuális hálózat** lehetőséget.
2. Válassza ki a **Resource Managert** a telepítési modellként.
3. A **Név** mezőben adjon meg egy hálózatnevet. A névnek egyedinek kell lennie az Azure-erőforráscsoporton belül. Ebben az oktatóanyagban a **ContosoASRnet** nevet használjuk.
4. Adja meg, melyik erőforráscsoportban hozza létre a hálózatot. A **contosoRG** meglévő erőforráscsoportot használjuk.
5. A **címtartomány**mezőben adja meg a hálózat tartományát. A **10.1.0.0/24**-et használja, és nem használ alhálózatot.
6. Az **Előfizetés** mezőben válassza ki azt az előfizetést, amelyben létre kívánja hozni a hálózatot.
7. A **hely**mezőben válassza ki ugyanazt a régiót, mint amelyben a Recovery Services-tárolót létrehozták. Az oktatóanyagban ez **Nyugat-Európa**. A hálózatnak ugyanabban a régióban kell lennie, mint a tárolónak.
8. Meghagyjuk az alapszintű DDoS Protection alapértelmezett beállításait hálózati szolgáltatásvégpont nélkül.
9. Kattintson a **Create** (Létrehozás) gombra.

   ![Virtuális hálózat létrehozása](media/tutorial-prepare-azure/create-network.png)

A virtuális hálózat néhány másodperc alatt létrejön. A létrehozást követően megjelenik az Azure Portal irányítópultján.




## <a name="next-steps"></a>További lépések

- A VMware vész-helyreállítás esetén [készítse elő a helyszíni VMware-infrastruktúrát](tutorial-prepare-on-premises-vmware.md).
- A Hyper-V vész-helyreállítás esetén [készítse elő a helyszíni Hyper-v-kiszolgálókat](hyper-v-prepare-on-premises-tutorial.md).
- A fizikai kiszolgáló vész-helyreállításához [állítsa be a konfigurációs kiszolgálót és a forrás-környezetet](physical-azure-disaster-recovery.md)
- Az Azure Networks [ismertetése](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).
- [További információ a](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) felügyelt lemezekről.
