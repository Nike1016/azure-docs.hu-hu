---
title: Azure Monitor függőség virtuálisgép-bővítmény a Windowshoz | Microsoft Docs
description: Telepítse a Azure Monitor függőségi ügynököt a Windows rendszerű virtuális gépen a virtuálisgép-bővítmény használatával.
services: virtual-machines-windows
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/29/2019
ms.author: magoedte
ms.openlocfilehash: 5913e33bc29541adfa1599ad5413ad3702635740
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2019
ms.locfileid: "68775433"
---
# <a name="azure-monitor-dependency-virtual-machine-extension-for-windows"></a>A Windows-függőség virtuálisgép-bővítményének Azure Monitor

Virtuális gépek térkép funkció az Azure Monitor az adatok lekérése a Microsoft Dependency agent. Az Azure-beli virtuális gép függőségi ügynökének a Windows rendszerhez készült virtuálisgép-bővítményét a Microsoft közzétette és támogatja. A bővítmény telepíti a függőségi ügynököt az Azure Virtual Machines szolgáltatásban. Ez a dokumentum részletesen ismerteti a Windows rendszerhez készült Azure-beli virtuális gép függőségi ügynökének támogatott platformokat, konfigurációkat és telepítési lehetőségeit.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="operating-system"></a>Operációs rendszer

A Windows rendszerhez készült Azure-beli virtuálisgép-függőségi ügynök bővítmény a Azure Monitor for VMs telepítési cikk [támogatott operációs rendszerek](../../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) szakaszában felsorolt támogatott operációs rendszereken is futtatható.

## <a name="extension-schema"></a>Bővítményséma

A következő JSON az Azure-beli virtuális gép függőségi ügynökének sémáját mutatja egy Azure Windows rendszerű virtuális gépen.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Azure VM. Supported Windows Server versions:  2008 R2 and above (x64)."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

### <a name="property-values"></a>Tulajdonságok értékei

| Name (Név) | Érték/példa |
| ---- | ---- |
| apiVersion | 2015-01-01 |
| publisher | Microsoft.Azure.Monitoring.DependencyAgent |
| type | DependencyAgentWindows |
| typeHandlerVersion | 9,5 |

## <a name="template-deployment"></a>Sablonalapú telepítés

Az Azure virtuálisgép-bővítményeket Azure Resource Manager-sablonokkal is üzembe helyezheti. Az Azure Resource Manager sablon előző szakaszában részletes JSON-sémát használva futtathatja az Azure-beli virtuális gép függőségi ügynökének bővítményét egy Azure Resource Manager-sablon központi telepítése során.

A virtuálisgép-bővítmények JSON-je beágyazható a virtuális gép erőforrásaiba. Azt is megteheti, hogy egy Resource Manager JSON-sablon gyökerére vagy legfelső szintjére helyezi. A JSON elhelyezése hatással van az erőforrás nevének és típusának értékére. További információkért lásd: [állítsa be a nevét és típusát gyermekerőforrásait](../../azure-resource-manager/child-resource-name-type.md).

Az alábbi példa azt feltételezi, hogy a függőségi ügynök bővítménye a virtuális gép erőforrásán belül van beágyazva. A bővítmény erőforrásának beágyazásakor a rendszer a JSON- `"resources": []` t a virtuális gép objektumában helyezi el.


```json
{
    "type": "extensions",
    "name": "DAExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

Ha a bővítmény JSON-fájlját a sablon gyökerében helyezi el, az erőforrás neve a szülő virtuális gépre mutató hivatkozást tartalmaz. A típus a beágyazott konfigurációt tükrözi.

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "<parentVmResource>/DAExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

## <a name="powershell-deployment"></a>PowerShell-telepítés

A `Set-AzVMExtension` parancs használatával telepítheti a függőségi ügynök virtuálisgép-bővítményét egy meglévő virtuális gépre. A parancs futtatása előtt a nyilvános és a privát konfigurációkat egy PowerShell-kivonatoló táblában kell tárolni.

```powershell

Set-AzVMExtension -ExtensionName "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ExtensionType "DependencyAgentWindows" `
    -TypeHandlerVersion 9.5 `
    -Location WestUS 
```

## <a name="troubleshoot-and-support"></a>Hibaelhárítás és támogatás

### <a name="troubleshoot"></a>Hibaelhárítás

A bővítmények állapotával kapcsolatos adatok a Azure Portal és az Azure PowerShell modul használatával kérhetők le. Egy adott virtuális gép bővítményeinek telepítési állapotának megtekintéséhez futtassa a következő parancsot a Azure PowerShell modul használatával:

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

A bővítmény-végrehajtás kimenete a következő könyvtárban található fájlokra van naplózva:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Monitoring.DependencyAgent\
```

### <a name="support"></a>Támogatás

Ha ebben a cikkben bármikor további segítségre van szüksége, forduljon az Azure-szakértőket a a [MSDN Azure-ban és a Stack Overflow-fórumok](https://azure.microsoft.com/support/forums/). Vagy egy Azure-támogatási incidenst is betölthet. Nyissa meg az [Azure támogatási](https://azure.microsoft.com/support/options/) webhelyét, és válassza a **támogatás kérése**lehetőséget. További információ az Azure-támogatás használatáról: [Microsoft Azure támogatással kapcsolatos gyakori kérdések](https://azure.microsoft.com/support/faq/).
