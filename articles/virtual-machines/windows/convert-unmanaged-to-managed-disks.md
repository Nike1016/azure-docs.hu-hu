---
title: Windows rendszerű virtuális gép konvertálása nem felügyelt lemezekről felügyelt lemezekre – Azure Managed Disks | Microsoft Docs
description: Windows rendszerű virtuális gép konvertálása nem felügyelt lemezekről a felügyelt lemezekre a PowerShell használatával a Resource Manager-alapú üzemi modellben
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: rogarana
ms.openlocfilehash: d43ad941fe68707bca873fa969fbc27806ba96a5
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68698812"
---
# <a name="convert-a-windows-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Windows rendszerű virtuális gép konvertálása nem felügyelt lemezekről felügyelt lemezekre

Ha már van olyan Windows rendszerű virtuális gép (VM), amely nem felügyelt lemezeket használ, a virtuális gépeket átalakíthatja felügyelt lemezek használatára az [Azure Managed Disks](managed-disks-overview.md) szolgáltatáson keresztül. Ez a folyamat az operációsrendszer-lemezt és a csatlakoztatott adatlemezeket is átalakítja.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Előkészületek


* Tekintse át [a Managed Disksre való áttelepítésre vonatkozó tervet](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks).

* Tekintse át [a Managed Disksre való áttelepítéssel kapcsolatos gyakori kérdéseket](faq-for-disks.md#migrate-to-managed-disks).

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]

* A virtuális gép által az átalakítás előtt használt eredeti virtuális merevlemezeket és a tárfiókot nem törli a rendszer. A futtatásuk továbbra is költségekkel jár. Annak érdekében, hogy ezekért az összetevőkért a továbbiakban ne fizessen, törölje az eredeti virtuálismerevlemez-blobokat, miután meggyőződött arról, hogy az átalakítás befejeződött. Ha meg kell találnia ezeket a nem csatolt lemezeket a törléséhez, tekintse meg a nem [csatolt Azure felügyelt és nem felügyelt lemezek keresését és törlését](find-unattached-disks.md)ismertető cikket.


## <a name="convert-single-instance-vms"></a>Egypéldányos virtuális gépek konvertálása
Ez a szakasz bemutatja, hogyan alakíthatja át a nem felügyelt lemezekről származó egypéldányos Azure-beli virtuális gépeket a felügyelt lemezekre. (Ha a virtuális gépek rendelkezésre állási csoportban találhatók, tekintse meg a következő szakaszt.) 

1. Szabadítsa fel a virtuális gépet a [stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm) parancsmag használatával. A következő példa felszabadítja a nevű virtuális gépet `myVM` a nevű `myResourceGroup`erőforráscsoporthoz: 

   ```azurepowershell-interactive
   $rgName = "myResourceGroup"
   $vmName = "myVM"
   Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
   ```

2. Alakítsa át a virtuális gépet felügyelt lemezekre a [ConvertTo-AzVMManagedDisk](https://docs.microsoft.com/powershell/module/az.compute/convertto-azvmmanageddisk) parancsmag használatával. A következő folyamat átalakítja az előző virtuális gépet, beleértve az operációsrendszer-lemezt és az adatlemezeket, és elindítja a virtuális gépet:

   ```azurepowershell-interactive
   ConvertTo-AzVMManagedDisk -ResourceGroupName $rgName -VMName $vmName
   ```



## <a name="convert-vms-in-an-availability-set"></a>Virtuális gépek konvertálása rendelkezésre állási csoportba

Ha a felügyelt lemezekre átalakítani kívánt virtuális gépek rendelkezésre állási csoportba kerülnek, először át kell alakítania a rendelkezésre állási készletet egy felügyelt rendelkezésre állási csoportba.

1. Alakítsa át a rendelkezésre állási készletet az [Update-AzAvailabilitySet](https://docs.microsoft.com/powershell/module/az.compute/update-azavailabilityset) parancsmag használatával. Az alábbi példa frissíti a nevű erőforráscsoport `myAvailabilitySet` -beli rendelkezésre állási `myResourceGroup`csoportot:

   ```azurepowershell-interactive
   $rgName = 'myResourceGroup'
   $avSetName = 'myAvailabilitySet'

   $avSet = Get-AzAvailabilitySet -ResourceGroupName $rgName -Name $avSetName
   Update-AzAvailabilitySet -AvailabilitySet $avSet -Sku Aligned 
   ```

   Ha az a régió, ahol a rendelkezésre állási csoport található, csak 2 felügyelt tartalék tartománnyal rendelkezik, de a nem felügyelt tartalék tartományok száma 3, ez a parancs a következőhöz hasonló hibaüzenetet jelenít meg: "a megadott tartalék tartomány 3. számának az 1 és 2 közötti tartományba kell esnie." A hiba elhárításához frissítse a tartalék tartományt 2-re, és `Sku` frissítse `Aligned` a következőre:

   ```azurepowershell-interactive
   $avSet.PlatformFaultDomainCount = 2
   Update-AzAvailabilitySet -AvailabilitySet $avSet -Sku Aligned
   ```

2. Szabadítson fel és alakítsa át a virtuális gépeket a rendelkezésre állási csoportba. A következő szkript felszabadítja az egyes virtuális gépeket a [stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm) parancsmag használatával, átalakítja azt a [ConvertTo-AzVMManagedDisk](https://docs.microsoft.com/powershell/module/az.compute/convertto-azvmmanageddisk)használatával, és automatikusan újraindítja az átalakítási folyamaton kívül:

   ```azurepowershell-interactive
   $avSet = Get-AzAvailabilitySet -ResourceGroupName $rgName -Name $avSetName

   foreach($vmInfo in $avSet.VirtualMachinesReferences)
   {
     $vm = Get-AzVM -ResourceGroupName $rgName | Where-Object {$_.Id -eq $vmInfo.id}
     Stop-AzVM -ResourceGroupName $rgName -Name $vm.Name -Force
     ConvertTo-AzVMManagedDisk -ResourceGroupName $rgName -VMName $vm.Name
   }
   ```


## <a name="troubleshooting"></a>Hibaelhárítás

Ha az átalakítás során hiba történt, vagy ha egy virtuális gép egy korábbi konverzióval kapcsolatos probléma miatt sikertelen állapotban van, futtassa újra a `ConvertTo-AzVMManagedDisk` parancsmagot. Egy egyszerű újrapróbálkozás általában feloldja a helyzetet.
A konvertálás előtt győződjön meg arról, hogy az összes virtuálisgép-bővítmény a "kiépítés sikeres" állapotban van, vagy a konverzió sikertelen lesz a 409-es hibakód miatt.

## <a name="convert-using-the-azure-portal"></a>Konvertálás a Azure Portal használatával

A nem felügyelt lemezeket a Azure Portal használatával is konvertálhatja a felügyelt lemezekre.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki a virtuális gépet a portálon lévő virtuális gépek listájából.
3. A virtuális gép paneljén válassza a menü **lemezek** elemét.
4. A **lemezek** panel tetején válassza az **áttelepítés felügyelt lemezekre**lehetőséget.
5. Ha a virtuális gép rendelkezésre állási csoportba esik, a rendszer figyelmeztetést küld a **felügyelt lemezek** átállítása panelre, hogy először a rendelkezésre állási készletet kell konvertálnia. A figyelmeztetésnek tartalmaznia kell egy hivatkozást, amelyre kattintva átalakíthatja a rendelkezésre állási készletet. Ha a rendelkezésre állási csoport át lett konvertálva, vagy ha a virtuális gép nincs rendelkezésre állási csoportba, kattintson az **áttelepítés** elemre a lemezek felügyelt lemezekre való áttelepítési folyamatának elindításához.

A rendszer a Migrálás befejezése után leállítja és újraindítja a virtuális gépet.

## <a name="next-steps"></a>További lépések

[Standard szintű felügyelt lemezek átalakítása prémium szintre](convert-disk-storage.md)

Pillanatképek használatával készítsen egy virtuális gép írásvédett másolatát. [](snapshot-copy-managed-disk.md)

