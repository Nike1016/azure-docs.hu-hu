---
title: Azure-beli virtuális gépek migrálása a Managed Disksba | Microsoft Docs
description: Telepítse át a nem felügyelt lemezekkel létrehozott Azure-beli virtuális gépeket a Storage-fiókokban a Managed Disks használatához.
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 7487d53195b45664b094ccc3a8418bd0c700e052
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68693498"
---
# <a name="migrate-azure-vms-to-managed-disks-in-azure"></a>Azure-beli virtuális gépek migrálása az Azure-ba való Managed Disks

Az Azure Managed Disks leegyszerűsíti a tárolási felügyeletet, hiszen nem kell külön kezelnie a Storage-fiókokat.  A meglévő Azure-beli virtuális gépeket is áttelepítheti Managed Disks a rendelkezésre állási csoportba tartozó virtuális gépek jobb megbízhatóságának kihasználása érdekében. Biztosítja, hogy a rendelkezésre állási csoportokban lévő különböző virtuális gépek lemezei elég elszigeteltek legyenek egymástól, hogy elkerülje az egyes meghibásodási pontokat. A szolgáltatás automatikusan áthelyezi a különböző virtuális gépek lemezeit egy rendelkezésre állási csoportba a különböző tárolási skálázási egységekben (bélyegzők), ami korlátozza a hardveres és a szoftver meghibásodása miatti egyetlen tárolási skálázási egység meghibásodásának hatását.
Az igények alapján négyféle tárolási lehetőség közül választhat. A rendelkezésre álló lemezek típusairól a [lemez típusának kiválasztása](disks-types.md) című cikkből tájékozódhat.

## <a name="migration-scenarios"></a>Áttelepítési forgatókönyvek

A következő helyzetekben telepítheti át Managed Disksre:

|Forgatókönyv  |Cikk  |
|---------|---------|
|Különálló virtuális gépek és rendelkezésreállási csoportban lévő virtuális gépek konvertálása felügyelt lemezekké     |[Virtuális gépek konvertálása felügyelt lemezek használatára](convert-unmanaged-to-managed-disks.md)         |
|Egyetlen virtuális gép konvertálása klasszikusról Resource Managerre felügyelt lemezeken     |[Virtuális gép létrehozása klasszikus VHD-ből](create-vm-specialized-portal.md)         |
|A vNet lévő összes virtuális gép konvertálása a Klasszikusból a Resource Managerbe a felügyelt lemezeken     |[IaaS-erőforrások migrálása a Klasszikusból a Resource Managerbe](migration-classic-resource-manager-ps.md) , majd [a virtuális gép konvertálása](convert-unmanaged-to-managed-disks.md) nem felügyelt lemezekről a felügyelt lemezekre         |
|Szabványos nem felügyelt lemezekkel rendelkező virtuális gépek frissítése felügyelt prémium szintű lemezekkel     | Először [alakítsa át a Windows rendszerű virtuális gépeket](convert-unmanaged-to-managed-disks.md)a nem felügyelt lemezekről a felügyelt lemezekre. Ezután [frissítse a felügyelt lemez tárolási típusát](convert-disk-storage.md).         |

## <a name="next-steps"></a>További lépések

- További információ a [Managed Disks](managed-disks-overview.md)
- Tekintse át a [Managed Disks díjszabását](https://azure.microsoft.com/pricing/details/managed-disks/).
