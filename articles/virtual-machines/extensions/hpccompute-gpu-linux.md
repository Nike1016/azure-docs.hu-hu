---
title: Illesztőprogram NVIDIA GPU - bővítmény Azure-beli Linuxos virtuális gépek |} A Microsoft Docs
description: A Microsoft Azure-bővítmény telepítse az NVIDIA GPU-illesztőprogramokat N-sorozatú linuxos virtuális gépek számítási.
services: virtual-machines-linux
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/11/2019
ms.author: roiyz
ms.openlocfilehash: c15948fd9e9acc1e1efeb536939002f179402d5a
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706703"
---
# <a name="nvidia-gpu-driver-extension-for-linux"></a>NVIDIA GPU illesztőprogramjának-bővítmény linuxhoz

## <a name="overview"></a>Áttekintés

Ez a bővítmény Linux N-sorozat virtuális gépei NVIDIA GPU-illesztők telepítése. A Virtuálisgép-család függően a bővítmény telepítését CUDA vagy GRID illesztőprogramok. NVIDIA telepítésekor illesztőprogramokat a bővítmény használatával Ön elfogadja, és elfogadja a használati a [NVIDIA végfelhasználói licencszerződés](https://go.microsoft.com/fwlink/?linkid=874330). A telepítés során a virtuális gép újraindulhat az illesztőprogramot a telepítés végrehajtásához.

Utasítások a manuális telepítés, az illesztőprogramok és aktuális verziók [Itt](
https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup).
Egy bővítmény is érhető el az NVIDIA GPU-illesztők telepítése [Windows N-sorozat virtuális gépei](hpccompute-gpu-windows.md).

## <a name="prerequisites"></a>Előfeltételek

### <a name="operating-system"></a>Operációs rendszer

Ez a bővítmény a következő operációs rendszer disztribúciókhoz függően az adott operációsrendszer-verzió támogatja az illesztőprogramokat támogatja.

| Disztribúció | Version |
|---|---|
| Linux: Ubuntu | 16.04 LTS, 18.04 LTS |
| Linux: Red Hat Enterprise Linux | 7.3, 7.4, 7.5, 7.6 |
| Linux: CentOS | 7.3, 7.4, 7.5, 7.6 |

### <a name="internet-connectivity"></a>Internetkapcsolat

A Microsoft Azure-bővítmény esetében az NVIDIA GPU-illesztők szükséges, hogy a cél virtuális gép csatlakozik az internethez, és rendelkezik hozzáféréssel.

## <a name="extension-schema"></a>Bővítményséma

A következő JSON a bővítmény sémáját jeleníti meg.

```json
{
  "name": "<myExtensionName>",
  "type": "extensions",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <myVM>)]"
  ],
  "properties": {
    "publisher": "Microsoft.HpcCompute",
    "type": "NvidiaGpuDriverLinux",
    "typeHandlerVersion": "1.2",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="properties"></a>properties

| Name (Név) | Érték és példa | Adattípus |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.HpcCompute | sztring |
| type | NvidiaGpuDriverLinux | Karakterlánc |
| typeHandlerVersion | 1.2 | int |

### <a name="settings"></a>Beállítások

Minden beállítás nem kötelező. Az alapértelmezett viselkedést, hogy a kernel nem frissíthető, ha nem illesztőprogram telepítéséhez szükséges, telepítse a legújabb támogatott illesztőprogram és a CUDA toolkit (ha létezik).

| Name (Név) | Leírás | Alapértelmezett érték | Érvényes értékek | Adattípus |
| ---- | ---- | ---- | ---- | ---- |
| updateOS | Frissítse a kernel akkor is, ha az illesztőprogram telepítéséhez nem szükséges | false | true, false | boolean |
| driverVersion | NV: RÁCS illesztőprogram verziója<br> NC/ND: CUDA-eszközkészlet verzió. A legújabb illesztőprogramokat a kiválasztott CUDA automatikusan települnek. | legújabb | GRID: "430.30", "418.70", "410.92", "410.71", "390.75", "390.57", "390.42"<br> CUDA: "10.0.130", "9.2.88", "9.1.85" | sztring |
| installCUDA | Telepítse a CUDA eszközkészlet. Csak akkor érvényes, a hálózati vezérlő/ND sorozatú virtuális gépek. | true | true, false | boolean |


## <a name="deployment"></a>Környezet


### <a name="azure-resource-manager-template"></a>Azure Resource Manager-sablon 

Az Azure Virtuálisgép-bővítmények is üzembe helyezhetők az Azure Resource Manager-sablonok. Sablonok ideálisak, egy vagy több üzembe helyezés utáni konfigurálásra igénylő virtuális gépek üzembe helyezésekor.

Virtuálisgép-bővítmények JSON konfigurációjának a virtuális gép típusú erőforrást belülre, vagy elhelyezve, a legfelső szintű vagy a legfelső szintű Resource Managerből származó JSON-sablon. A JSON konfigurációs elhelyezését hatással van az erőforrás nevét, és írja be az értékét. További információkért lásd: [állítsa be a nevét és típusát gyermekerőforrásait](../../azure-resource-manager/resource-manager-template-child-resource.md). 

Az alábbi példa azt feltételezi, hogy a bővítményt a virtuális gép típusú erőforrást van beágyazva. A bővítmény erőforrás beágyazása, ha a JSON az kerül a `"resources": []` objektum a virtuális gép.

```json
{
  "name": "myExtensionName",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', myVM)]"
  ],
  "properties": {
    "publisher": "Microsoft.HpcCompute",
    "type": "NvidiaGpuDriverLinux",
    "typeHandlerVersion": "1.2",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="powershell"></a>PowerShell

```powershell
Set-AzVMExtension
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Location "southcentralus" `
    -Publisher "Microsoft.HpcCompute" `
    -ExtensionName "NvidiaGpuDriverLinux" `
    -ExtensionType "NvidiaGpuDriverLinux" `
    -TypeHandlerVersion 1.2 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Azure CLI

Az alábbi példa a fenti Azure Resource Manager és a PowerShell-példák tükrözi, és is hozzáadja az egyéni beállítások nem alapértelmezett illesztőprogram telepítéséhez például. Pontosabban frissíti az operációs rendszer kernelén, és egy adott CUDA eszközkészlet verzió illesztőprogram telepíti.

```azurecli
az vm extension set `
  --resource-group myResourceGroup `
  --vm-name myVM `
  --name NvidiaGpuDriverLinux `
  --publisher Microsoft.HpcCompute `
  --version 1.2 `
  --settings '{ `
    "updateOS": true, `
    "driverVersion": "9.1.85", `
  }'
```

## <a name="troubleshoot-and-support"></a>Hibaelhárítás és támogatás

### <a name="troubleshoot"></a>Hibaelhárítás

Bővítmény központi telepítések állapotát lehet adatokat beolvasni az Azure Portalról, és az Azure PowerShell-lel és az Azure CLI használatával. Adott Virtuálisgép-bővítmények központi telepítési állapotának megtekintéséhez futtassa a következő parancsot.

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Bővítmény végrehajtás kimenetének a rendszer naplózza a következő fájlt:

```bash
/var/log/azure/nvidia-vmext-status
```

### <a name="exit-codes"></a>Kilépési kódok

| Kilépési kód | Jelentés | A művelet lehetséges |
| :---: | --- | --- |
| 0 | A művelet sikeres |
| 1 | A bővítmény helytelen használat | Végrehajtási kimeneti naplóban |
| 10 | A Hyper-V és az Azure nem érhető el, illetve telepített Linux integrációs szolgáltatások | Ellenőrzés kimenetét lspci |
| 11 | NVIDIA GPU nem található a virtuális gép mérete | Használja a [támogatja a virtuális gép mérete és az operációs rendszer](../linux/n-series-driver-setup.md) |
| 12 | Kép az ajánlat nem támogatott |
| 13 | Virtuálisgép-méret nem támogatott | Használja az N sorozatú virtuális gépek üzembe helyezése |
| 14 | A művelet sikertelen | Végrehajtási kimeneti naplóban |


### <a name="support"></a>Támogatás

Ha ebben a cikkben bármikor további segítségre van szüksége, forduljon az Azure-szakértőket a a [MSDN Azure-ban és a Stack Overflow-fórumok](https://azure.microsoft.com/support/community/). Másik lehetőségként a egy Azure-támogatási esemény is fájl. Nyissa meg a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) , és válassza ki a Get-támogatást. Azure-támogatási használatával kapcsolatos információkért olvassa el a [Microsoft Azure-támogatás – gyakori kérdések](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>További lépések
Bővítmények kapcsolatos további információkért lásd: [virtuális gépi bővítmények és szolgáltatások Linux](features-linux.md).

N-sorozatú virtuális gépekkel kapcsolatos további információkért lásd: [GPU-optimalizált virtuális gépek méretei](../linux/sizes-gpu.md).
