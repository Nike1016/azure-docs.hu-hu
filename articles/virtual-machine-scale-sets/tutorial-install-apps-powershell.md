---
title: Oktatóanyag – Alkalmazások telepítése méretezési csoportokban az Azure PowerShell-lel | Microsoft Docs
description: Megismerheti, hogyan telepíthet alkalmazásokat virtuálisgép-méretezési csoportokban az egyéni szkriptbővítmény és az Azure PowerShell használatával
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/08/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 4c6216f3dff3cbf4c8d838810c4dd786f1c34ec1
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2019
ms.locfileid: "66728652"
---
# <a name="tutorial-install-applications-in-virtual-machine-scale-sets-with-azure-powershell"></a>Oktatóanyag: Alkalmazások telepítése virtuálisgép-méretezési csoportok az Azure PowerShell használatával

Ha alkalmazásokat szeretne futtatni egy méretezési csoport virtuálisgép-példányán, először telepítenie kell az alkalmazás összetevőit és szükséges fájljait. Egy korábbi oktatóanyagból megtudhatta, hogyan hozhat létre és használhat egyéni virtuálisgép-rendszerképeket a virtuálisgép-példányok üzembe helyezéséhez. Ez az egyéni rendszerkép tartalmazott manuális alkalmazástelepítéseket és -konfigurációkat. Az egyes virtuálisgép-példányok üzembe helyezése után lehetősége van az alkalmazások méretezési csoportokon történő telepítésének automatizálására, vagy egy a méretezési csoporton már futó alkalmazás frissítésére. Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Alkalmazások automatikus telepítése a méretezési csoporton
> * Az egyéni Azure-szkriptek bővítményének használata
> * Egy méretezési csoporton futtatott alkalmazás frissítése

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="what-is-the-azure-custom-script-extension"></a>Mi az egyéni Azure-szkriptek bővítménye?
Az egyéni szkriptek bővítménye szkripteket tölt le és futtat az Azure-beli virtuális gépeken. A bővítmény az üzembe helyezést követő konfiguráció, szoftvertelepítés, illetve bármely konfigurációs/felügyeleti feladat végrehajtása során hasznos. A szkriptek az Azure Storage-ből vagy a GitHubról tölthetők le, illetve megadhatók az Azure Portalon a bővítmény futásidejében.

Az egyéni szkriptek futtatására szolgáló bővítmény integrálható az Azure Resource Manager-sablonok. Is használható az Azure CLI, Azure PowerShell-lel, az Azure portal vagy a REST API-t. További információ: [Az egyéni szkriptbővítmény áttekintése](../virtual-machines/windows/extensions-customscript.md).

Az egyéni szkriptbővítmény működés közben való megtekintéséhez hozzon létre egy méretezési csoportot, amely telepíti az IIS-webkiszolgálót, és a méretezési csoport virtuálisgép-példányának gazdagépnevét adja vissza. Az egyéni szkriptbővítmény definíciója letölt egy mintaszkriptet a GitHubról, telepíti a szükséges csomagokat, majd kiírja a virtuálisgép-példány gazdagépnevét egy alap HTML-oldalra.


## <a name="create-a-scale-set"></a>Méretezési csoport létrehozása
Most hozzon létre egy virtuálisgép-méretezési csoportot az [New-AzVmss](/powershell/module/az.compute/new-azvmss). A forgalom az egyes virtuális gépek közötti elosztása érdekében a parancs egy terheléselosztót is létrehoz. A load balancer a 80-as porton a forgalom elosztását szabályokat tartalmaz. Azt is lehetővé teszi a távoli asztali forgalmat a 3389-es TCP-porton és a PowerShell távoli eljáráshívást az 5985-ös TCP-port. Amikor a rendszer kéri, a saját rendszergazdai hitelesítő adatokkal, a Virtuálisgép-példányokhoz állíthatja be a méretezési csoportba:

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myScaleSet" `
  -Location "EastUS" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic"
```

A méretezési csoport erőforrásainak és virtuális gépeinek létrehozása és konfigurálása néhány percet vesz igénybe.


## <a name="create-custom-script-extension-definition"></a>Definíció létrehozása az egyéni szkriptek bővítményéhez
Az Azure PowerShell egy kivonattáblában tárolja a letöltendő fájlt és a végrehajtandó parancsot. Az alábbi példa a GitHubról származó mintaszkriptet használja. Először hozza létre ezt a konfigurációs objektumot az alábbiak szerint:

```azurepowershell-interactive
$customConfig = @{
  "fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate-iis.ps1");
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis.ps1"
}
```


Most alkalmazza az egyéni szkriptek bővítménye az [Add-AzVmssExtension](/powershell/module/az.Compute/Add-azVmssExtension). A korábban definiált konfigurációs objektum át lesz adva a bővítménynek. Frissítse és futtassa a bővítményt a Virtuálisgép-példányok [Update-AzVmss](/powershell/module/az.compute/update-azvmss).


```azurepowershell-interactive
# Get information about the scale set
$vmss = Get-AzVmss `
          -ResourceGroupName "myResourceGroup" `
          -VMScaleSetName "myScaleSet"

# Add the Custom Script Extension to install IIS and configure basic website
$vmss = Add-AzVmssExtension `
  -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.9 `
  -Setting $customConfig

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```

A méretezési csoport összes virtuálisgép-példánya a GitHubról tölti le és futtatja a szkriptet. Egy összetettebb példában több alkalmazás-összetevő és fájl is telepíthető. A méretezési csoport vertikális felskálázása esetén az új virtuálisgép-példányok automatikusan alkalmazzák az egyéni szkriptek bővítményének definícióját, és telepítik a szükséges alkalmazást.


## <a name="allow-traffic-to-application"></a>Forgalom engedélyezése az alkalmazáshoz

Ahhoz, hogy az alapszintű webalkalmazás a hozzáférést, hozzon létre egy hálózati biztonsági csoport [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) és [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup). További információkért lásd: [Azure-beli virtuálisgép-méretezési csoportok hálózatkezelése](virtual-machine-scale-sets-networking.md).

```azurepowershell-interactive
# Get information about the scale set
$vmss = Get-AzVmss `
            -ResourceGroupName "myResourceGroup" `
            -VMScaleSetName "myScaleSet"

#Create a rule to allow traffic over port 80
$nsgFrontendRule = New-AzNetworkSecurityRuleConfig `
  -Name myFrontendNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 200 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow

#Create a network security group and associate it with the rule
$nsgFrontend = New-AzNetworkSecurityGroup `
  -ResourceGroupName  "myResourceGroup" `
  -Location EastUS `
  -Name myFrontendNSG `
  -SecurityRules $nsgFrontendRule

$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName  "myResourceGroup" `
  -Name myVnet

$frontendSubnet = $vnet.Subnets[0]

$frontendSubnetConfig = Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name mySubnet `
  -AddressPrefix $frontendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgFrontend

Set-AzVirtualNetwork -VirtualNetwork $vnet

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzVmss `
    -ResourceGroupName "myResourceGroup" `
    -Name "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```



## <a name="test-your-scale-set"></a>Méretezési csoport tesztelése
Szeretné látni működés közben a webkiszolgáló, a terheléselosztó a nyilvános IP-címének lekéréséhez [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress). Az alábbi példa megjeleníti a létrehozott IP-címet a *myResourceGroup* erőforráscsoportot:

```azurepowershell-interactive
Get-AzPublicIpAddress -ResourceGroupName "myResourceGroup" | Select IpAddress
```

Adja meg a terheléselosztó nyilvános IP-címét egy webböngészőben. A terheléselosztó az egyik virtuálisgép-példányra terjeszti a forgalmat, ahogy az a következő példában látható:

![Alapszintű weboldal az IIS-ben](media/tutorial-install-apps-powershell/running-iis.png)

Ne zárja be a böngészőt, hogy megtekinthesse a frissített verziót a következő lépés során.


## <a name="update-app-deployment"></a>Alkalmazástelepítés frissítése
A méretezési csoport életciklusa során előfordulhat, hogy telepítenie kell az alkalmazás frissített verzióját. Az egyéni szkriptek bővítményével hivatkozhat egy frissített telepítési szkriptre, majd alkalmazhatja ismét a bővítményt a méretezési csoporton. Amikor az előző lépésben létrehozta a méretezési csoportot, az `-UpgradePolicyMode` paraméter *Automatic* (Automatikus) értékre volt állítva. Ez a beállítás lehetővé teszi a méretezési csoportban található virtuálisgép-példányok számára az automatikus frissítést és az alkalmazás legfrissebb verziójának alkalmazását.

Hozzon létre egy új konfigurációdefiníciót *customConfigv2* néven. A definíció az alkalmazástelepítési szkript frissített (*v2*) verzióját futtatja:

```azurepowershell-interactive
$customConfigv2 = @{
  "fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate-iis-v2.ps1");
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis-v2.ps1"
}
```

Frissítse az egyéni Szkriptbővítmény konfigurációját a méretezési csoportban lévő Virtuálisgép-példányok. Az alkalmazás frissített verziója a *customConfigv2* definíció használatával alkalmazható:

```azurepowershell-interactive
$vmss = Get-AzVmss `
          -ResourceGroupName "myResourceGroup" `
          -VMScaleSetName "myScaleSet"
 
$vmss.VirtualMachineProfile.ExtensionProfile[0].Extensions[0].Settings = $customConfigv2
 
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```

A méretezési csoport összes virtuálisgép-példánya automatikusan frissül a minta weboldal legfrissebb verziójával. A frissített verzió megtekintéséhez frissítse a webhelyet a böngészőben:

![Frissített weboldal az IIS-ben](media/tutorial-install-apps-powershell/running-iis-updated.png)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
A méretezési csoport és további erőforrások eltávolításához törölje az erőforráscsoportot és az ahhoz tartozó összes erőforrást, az [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup). A `-Force` paraméter megerősíti, hogy további kérdés nélkül szeretné törölni az erőforrásokat. A `-AsJob` paraméter visszaadja a vezérlést a parancssornak, és nem várja meg a művelet befejeztét.

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>További lépések
Ez az oktatóanyag bemutatta, hogy lehet alkalmazásokat automatikusan telepíteni és frissíteni a méretezési csoportokban az Azure PowerShell-lel:

> [!div class="checklist"]
> * Alkalmazások automatikus telepítése a méretezési csoporton
> * Az egyéni Azure-szkriptek bővítményének használata
> * Egy méretezési csoporton futtatott alkalmazás frissítése

A következő oktatóanyag azt mutatja be, hogyan skálázhatja automatikusan a méretezési csoportját.

> [!div class="nextstepaction"]
> [Méretezési csoportok automatikus skálázása](tutorial-autoscale-powershell.md)
