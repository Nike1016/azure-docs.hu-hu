---
title: fájl belefoglalása
description: fájl belefoglalása
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 05/01/2019
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: feec6a695ad867d26d32904d020648b029f9da35
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67179209"
---
## <a name="create-a-media-services-account"></a>Media Services-fiók létrehozása

Először létre kell hoznia egy Media Services-fiókot. Ez a szakasz bemutatja, mi van szüksége a fiók létrehozása az Azure CLI használatával.

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az erőforráscsoport létrehozásához használja az alábbi parancsot. Az Azure-erőforráscsoport egy olyan logikai tároló, amelyben a rendszer üzembe helyezi és kezeli az erőforrásokat (például az Azure Media Services-fiókokat és a kapcsolódó Storage-fiókokat).

Helyettesítheti `amsResourceGroup` a értékkel.

```azurecli
az group create --name amsResourceGroup --location westus2
```

### <a name="create-a-storage-account"></a>Tárfiók létrehozása

A Media Services-fiók létrehozásakor meg kell adnia egy Azure Storage-fiókhoz tartozó erőforrás nevét. A rendszer a Media Services-fiókhoz csatolja a megadott Storage-fiókot. További információ a tárfiókok használatáról a Media Servicesben: [Storage-fiókok](../articles/media-services/latest/storage-account-concept.md).

Egy **elsődleges** és tetszőleges számú **másodlagos** Storage-fiókot társíthat a Media Services-fiókhoz. A Media Services támogatja az **Általános célú v2-** (GPv2-) és az **Általános célú v1-** (GPv1-) fiókokat is. A blobfiókok nem megengedettek **elsődleges** tárfiókként. Ha szeretne többet megtudni a Storage-fiókokkal kapcsolatban, tekintse meg [Az Azure Storage-fiók beállításai](../articles/storage/common/storage-account-options.md) című témakört. 

Ebben a példában létrehozunk egy általános célú v2, a standard szintű LRS-fiókra. Ha azt szeretné, kísérletezhet a storage-fiókok, `--sku Standard_LRS`. Azonban éles környezetben a Termékváltozat kiválasztásakor érdemes, `--sku Standard_RAGRS`, amely biztosítja az üzletmenet folytonosságának földrajzi replikáció. További információkért lásd: [tárfiókok](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest).
 
Az alábbi parancs egy Storage-fiókot hoz létre, amelyet a rendszer a Media Services-fiókhoz fog társítani. Az alábbi szkriptben a `storageaccountforams` helyére helyettesítheti be az Ön által megadott értéket. `amsResourceGroup` az erőforráscsoport az előző lépésben megadott értékét meg kell egyeznie. A tárfiók nevének hossza kisebb, mint 24 kell rendelkeznie.

```azurecli
az storage account create --name storageaccountforams \  
  --kind StorageV2 \
  --sku Standard_LRS \
  -l westus2 \
  -g amsResourceGroup
```

### <a name="create-a-media-services-account"></a>Media Services-fiók létrehozása

Az alábbi Azure CLI-parancs egy új Media Services-fiókot hoz létre. A következő értékeket kell kicserélnie: `amsaccount`, `storageaccountforams` (meg kell egyeznie a tárfiók esetében megadott értékkel) és `amsResourceGroup` (meg kell egyeznie az erőforráscsoport esetében megadott értékkel).

```azurecli
az ams account create --name amsaccount \
  -l westus2 \
  -g amsResourceGroup --storage-account storageaccountforams
```
