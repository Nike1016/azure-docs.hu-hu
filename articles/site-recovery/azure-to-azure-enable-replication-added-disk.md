---
title: Replikáció engedélyezése egy Azure Site Recovery által replikált Azure-beli virtuális géphez hozzáadott lemez számára | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan engedélyezhető a replikáció egy olyan Azure-beli virtuális géphez hozzáadott lemez számára, amely Azure Site Recovery
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: asgang
ms.openlocfilehash: 068464b8a3919d833418c8f3916ccf5c54835c6f
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934549"
---
# <a name="enable-replication-for-a-disk-added-to-an-azure-vm"></a>Azure-beli virtuális géphez hozzáadott lemez replikálásának engedélyezése


Ez a cikk azt ismerteti, hogyan engedélyezhető a replikáció olyan Azure-beli virtuális géphez hozzáadott adatlemezek esetében, amelyek már engedélyezve lettek egy másik Azure-régióba való vész-helyreállításra, [Azure site Recovery](site-recovery-overview.md)használatával.

A virtuális géphez hozzáadott lemezek replikálásának engedélyezése a felügyelt lemezekkel rendelkező Azure-beli virtuális gépek esetében támogatott.

Amikor új lemezt ad hozzá egy másik Azure-régióba replikált Azure-beli virtuális géphez, a következők történnek:

-   A virtuális gép replikálási állapota figyelmeztetést jelenít meg, és a portálon egy Megjegyzés tájékoztatja arról, hogy egy vagy több lemez is elérhető a védelemhez.
-   Ha engedélyezi a hozzáadott lemezek védelmét, a figyelmeztetés a lemez kezdeti replikációja után eltűnik.
-   Ha úgy dönt, hogy nem engedélyezi a lemez replikálását, kiválaszthatja, hogy elhagyhatja a figyelmeztetést.

![Új lemez hozzáadva](./media/azure-to-azure-enable-replication-added-disk/newdisk.png)



## <a name="before-you-start"></a>Előkészületek

Ez a cikk azt feltételezi, hogy már beállította a vész-helyreállítást arra a virtuális gépre vonatkozóan, amelyhez hozzáadja a lemezt. Ha még nem tette meg, kövesse az [Azure-ról az Azure vész-helyreállítási oktatóanyagát](azure-to-azure-tutorial-enable-replication.md). 

## <a name="enable-replication-for-an-added-disk"></a>Replikáció engedélyezése a hozzáadott lemezen 

Egy hozzáadott lemez replikálásának engedélyezéséhez tegye a következőket:

1. A tárolóban > **replikált elemek**területen kattintson arra a virtuális gépre, amelyhez hozzáadta a lemezt.
2. Kattintson a **lemezek**elemre, majd válassza ki azt az adatlemezt, amelynek engedélyezni szeretné a replikálását (ezek a lemezek **nem védett** állapottal rendelkeznek).
3.  A **lemez részletei**területen kattintson a **replikáció engedélyezése**elemre.

    ![A további lemez replikálásának engedélyezése](./media/azure-to-azure-enable-replication-added-disk/enabled-added.png)

A replikációs feladatok engedélyezése és a kezdeti replikálás befejezése után a rendszer eltávolítja a lemezre vonatkozó probléma replikálási állapotára vonatkozó figyelmeztetést.



## <a name="next-steps"></a>További lépések

[További](site-recovery-test-failover-to-azure.md) információ a feladatátvételi teszt futtatásáról.
