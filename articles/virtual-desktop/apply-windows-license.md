---
title: Windows-licenc alkalmazása a munkamenet-gazdagép virtuális gépei számára – Azure
description: Leírja, hogyan kell alkalmazni a Windows-licencet a Windows rendszerű virtuális gépekhez.
services: virtual-desktop
author: ChristianMontoya
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/14/2019
ms.author: chrimo
ms.openlocfilehash: af8542ccc8fad8d833d8329999ded2f5a52b3d03
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69564198"
---
# <a name="apply-windows-license-to-session-host-virtual-machines"></a>Windows-licenc alkalmazása a munkamenet-gazdagép virtuális gépei számára

Azok a felhasználók, akik megfelelően licenccel rendelkeznek a Windows rendszerű virtuális asztali munkaterhelések futtatásához, jogosultak arra, hogy Windows-licencet alkalmazzanak a munkamenet-gazdagépi virtuális gépekre, és más licencek kifizetése nélkül További információ: a [Windows virtuális asztal díjszabása](https://azure.microsoft.com/pricing/details/virtual-desktop/).

## <a name="ways-to-use-your-windows-virtual-desktop-license"></a>A Windows rendszerű virtuális asztali licenc használatának módjai
A Windows rendszerű virtuális asztali licencelés lehetővé teszi, hogy licencet alkalmazzon bármely olyan Windows-vagy Windows Server rendszerű virtuális gépre, amely a gazdagépen munkamenet-gazdagépként van regisztrálva, és felhasználói kapcsolatokat fogad. Ez a licenc nem vonatkozik azokra a virtuális gépekre, amelyek fájlmegosztási kiszolgálóként, tartományvezérlőként és hasonló módon futnak.

A Windows rendszerű virtuális asztali licenc több módon is használható:
- A gazdagépek és a munkamenet-gazdagépek virtuális gépei az [Azure Marketplace-ajánlat](./create-host-pools-azure-marketplace.md)használatával hozhatók létre. Az ily módon létrehozott virtuális gépek automatikusan a licenccel lettek alkalmazva.
- A [GitHub Azure Resource Manager sablonnal](./create-host-pools-arm-template.md)létrehozhat egy gazdagépet és a munkamenet-gazda virtuális gépeket. Az ily módon létrehozott virtuális gépek automatikusan a licenccel lettek alkalmazva.
- Egy meglévő munkamenet-gazda virtuális gépre is alkalmazhat licencet. Ehhez kövesse a [gazdagép létrehozása a PowerShell](./create-host-pools-powershell.md) használatával című témakör utasításait a gazdagépek és a társított virtuális gépek létrehozásához, majd térjen vissza ehhez a cikkhez, és Ismerje meg, hogyan alkalmazhatja a licencet.

## <a name="apply-a-windows-license-to-a-session-host-vm"></a>Windows-licenc alkalmazása egy munkamenet-gazda virtuális gépre
Győződjön meg arról, hogy [telepítette és konfigurálta a legújabb Azure PowerShell](/powershell/azure/overview). Futtassa a következő PowerShell-parancsmagot a Windows-licenc alkalmazásához:

```powershell
$vm = Get-AzVM -ResourceGroup <resourceGroupName> -Name <vmName>
$vm.LicenseType = "Windows_Client"
Update-AzVM -ResourceGroupName <resourceGroupName> -VM $vm
```

## <a name="verify-your-session-host-vm-is-utilizing-the-licensing-benefit"></a>Ellenőrizze, hogy a munkamenet-gazda virtuális gép használja-e a licencelési kedvezményt
A virtuális gép üzembe helyezése után futtassa ezt a parancsmagot, és ellenőrizze a licenc típusát:
```powershell
Get-AzVM -ResourceGroup <resourceGroupName> -Name <vmName>
```

Az alkalmazott Windows-licenccel rendelkező munkamenet-gazda virtuális gép az alábbihoz hasonló módon fog megjelenni:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

Az alkalmazott Windows-licenccel nem rendelkező virtuális gépek a következőhöz hasonló módon jelennek meg:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

A következő parancsmag futtatásával tekintheti meg az Azure-előfizetésében alkalmazott Windows-licenccel rendelkező munkamenet-gazda virtuális gépek listáját:

```powershell
$vms = Get-AzVM
$vms | ?{$_.LicenseType -like "Windows_Client"} | select ResourceGroupName, Name, LicenseType
```