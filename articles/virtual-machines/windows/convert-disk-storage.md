---
title: Azure Managed Disks Storage konvertálása standard csomagról prémium vagy prémium szintre | Microsoft Docs
description: Az Azure Managed Disks standard szintűről prémiumra vagy prémium szintűre való konvertálása Azure PowerShell használatával.
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: fa6b005be91f47f5976dace7fd1e76f6ea7e0b29
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68698842"
---
# <a name="update-the-storage-type-of-a-managed-disk"></a>Felügyelt lemez tárolási típusának frissítése

Az Azure Managed Disks négyféle lemezből áll: Azure Ultra SSD-k (előzetes verzió), prémium SSD, standard SSD és standard HDD. A teljesítmény igényei alapján válthat a három GA-lemez típusa (prémium SSD, standard SSD és standard HDD) között. Még nem tud váltani egy ultra SSD-ről, és telepítenie kell egy újat.

A nem felügyelt lemezek esetében ez a funkció nem támogatott. A nem felügyelt lemezeket azonban könnyedén átalakíthatja [egy felügyelt lemezre](convert-unmanaged-to-managed-disks.md) , hogy képes legyen váltani a lemezek típusai között.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

* Mivel a konverzióhoz szükség van a virtuális gép (VM) újraindítására, a lemezes tárolás áttelepítését egy már létező karbantartási időszakban kell ütemeznie.
* Ha a lemez nem felügyelt, először [alakítsa át felügyelt lemezre](convert-unmanaged-to-managed-disks.md) , hogy át lehessen váltani a tárolási lehetőségek között.

## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>Egy virtuális gép összes felügyelt lemezének átváltása a prémium és a standard szint között

Ebből a példából megtudhatja, hogyan alakíthatja át a virtuális gép összes lemezét a standard és a Premium Storage-ból, illetve prémiumról standard szintű tárterületre. A prémium szintű felügyelt lemezek használatához a virtuális gépnek olyan [](sizes.md) virtuálisgép-méretet kell használnia, amely támogatja a Premium Storage-ot. Ez a példa a Premium Storage-t támogató méretre is vált:

```azurepowershell-interactive
# Name of the resource group that contains the VM
$rgName = 'yourResourceGroup'

# Name of the your virtual machine
$vmName = 'yourVM'

# Choose between Standard_LRS and Premium_LRS based on your scenario
$storageType = 'Premium_LRS'

# Premium capable size
# Required only if converting storage from Standard to Premium
$size = 'Standard_DS2_v2'

# Stop and deallocate the VM before changing the size
Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force

$vm = Get-AzVM -Name $vmName -resourceGroupName $rgName

# Change the VM size to a size that supports Premium storage
# Skip this step if converting storage from Premium to Standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Get all disks in the resource group of the VM
$vmDisks = Get-AzDisk -ResourceGroupName $rgName 

# For disks that belong to the selected VM, convert to Premium storage
foreach ($disk in $vmDisks)
{
    if ($disk.ManagedBy -eq $vm.Id)
    {
        $diskUpdateConfig = New-AzDiskUpdateConfig –AccountType $storageType
        Update-AzDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
        -DiskName $disk.Name
    }
}

Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>Egyéni felügyelt lemezek váltása a standard és a prémium között

A fejlesztési és tesztelési feladatok esetében érdemes lehet a standard és a prémium szintű lemezek kombinációját használni a költségek csökkentése érdekében. Dönthet úgy is, hogy csak a jobb teljesítményt igénylő lemezeket frissíti. Ebből a példából megtudhatja, hogyan alakíthat át egyetlen virtuálisgép-lemezt a standard csomagból a Premium Storage-ba vagy a prémiumról standard Storage-ba A prémium szintű felügyelt lemezek használatához a virtuális gépnek olyan [](sizes.md) virtuálisgép-méretet kell használnia, amely támogatja a Premium Storage-ot. Ez a példa azt is bemutatja, hogyan válthat a Premium Storage-t támogató méretre:

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS and Premium_LRS based on your scenario
$storageType = 'Premium_LRS'
# Premium capable size 
$size = 'Standard_DS2_v2'

$disk = Get-AzDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Change the VM size to a size that supports Premium storage
# Skip this step if converting storage from Premium to Standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Update the storage type
$diskUpdateConfig = New-AzDiskUpdateConfig -AccountType $storageType -DiskSizeGB $disk.DiskSizeGB
Update-AzDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="convert-managed-disks-from-standard-to-premium-in-the-azure-portal"></a>Felügyelt lemezek konvertálása standardról prémiumra a Azure Portal

Kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki a virtuális gépet a portálon a **virtuális gépek** listájából.
3. Ha a virtuális gép nem áll le, válassza a **Leállítás** lehetőséget a virtuális gép **áttekintése** panel tetején, és várjon, amíg a virtuális gép leáll.
3. A virtuális gép ablaktábláján válassza a menü **lemezek** elemét.
4. Válassza ki az átalakítani kívánt lemezt.
5. Válassza a **konfiguráció** lehetőséget a menüből.
6. Módosítsa a **fiók típusát** **standard HDDról** **prémium SSDra**.
7. Kattintson a **Save (Mentés**) gombra, és zárjuk be a lemez panelt.

A lemez típusának átalakítása azonnali. Az átalakítás után újraindíthatja a virtuális gépet.

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>Felügyelt lemezek váltása standard HDD és standard SSD között 

Ebből a példából megtudhatja, hogyan alakíthat át egyetlen virtuálisgép-lemezt standard HDDról standard SSD vagy standard SSDról standard HDDra:

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS and StandardSSD_LRS based on your scenario
$storageType = 'StandardSSD_LRS'

$disk = Get-AzDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Update the storage type
$diskUpdateConfig = New-AzDiskUpdateConfig -AccountType $storageType -DiskSizeGB $disk.DiskSizeGB
Update-AzDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="next-steps"></a>További lépések

Készítse el a virtuális gép írásvédett másolatát egy [Pillanatkép](snapshot-copy-managed-disk.md)használatával.
