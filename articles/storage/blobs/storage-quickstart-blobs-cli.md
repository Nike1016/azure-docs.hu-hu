---
title: Azure rövid útmutató – Blob létrehozása objektumtárban Azure CLI használatával | Microsoft Docs
description: Ebben a rövid útmutatóban Azure CLI használatával kezelheti az objektumtárat (blobtárat). Majd a parancssori felület segítségével feltölt egy blobot az Azure Storage-ba, letölt egy blobot, és kilistázza a tárolóban lévő blobokat.
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 11/14/2018
ms.author: tamram
ms.reviewer: seguler
ms.openlocfilehash: 6a0aef9b2fc7a99183ebd6991691245731e00200
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565956"
---
# <a name="quickstart-upload-download-and-list-blobs-using-the-azure-cli"></a>Gyors útmutató: Blobok feltöltése, letöltése és listázása az Azure CLI használatával

Az Azure CLI az Azure parancssori felülete, amely Azure-erőforrások kezelésére szolgál. Használhatja a böngészőjében az Azure Cloud Shell-lel. Vagy telepítheti macOS, Linux és Windows rendszeren, és futtathatja a parancssorból. Ebből a rövid útmutatóból megtudhatja, hogyan lehet az Azure CLI használatával adatokat fel- és letölteni az Azure Blob Storage-be.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a CLI helyi telepítését és használatát választja, akkor ehhez a gyorsútmutatóhoz az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. Futtassa az `az --version` parancsot a verzió meghatározásához. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket.

[!INCLUDE [storage-quickstart-tutorial-intro-include-cli](../../../includes/storage-quickstart-tutorial-intro-include-cli.md)]

## <a name="create-a-container"></a>Tároló létrehozása

A blobok minden esetben egy tárolóba lesznek feltöltve. A blobok csoportjait hasonló módon rendszerezheti, mint a fájlokat a számítógép mappáiban.

Hozzon létre blobok tárolására alkalmas tárolót az [az storage container create](/cli/azure/storage/container) parancs segítségével.

```azurecli-interactive
az storage container create --name mystoragecontainer
```

## <a name="upload-a-blob"></a>Blob feltöltése

A Blob Storage támogatja a blokkblobokat, a hozzáfűző blobokat és a lapblobokat. A blobtárolókban tárolt fájlok a legtöbb esetben blokkblobként vannak tárolva. A hozzáfűző blobokat akkor használjuk, ha meglévő blobokhoz adatokat szeretnénk hozzáadni a meglévő tartalmak módosítása nélkül (például naplózáshoz). A lapblobok az IaaS virtuális gépek VHD fájljait támogatják.

Először hozza létre a blobba feltölteni kívánt fájlt.
Az Azure Cloud Shell használata esetén a fájl létrehozásához alkalmazza a következőt: `vi helloworld`, amikor a fájl megnyílik, nyomja le az **Insert** billentyűt, írja be a „Hello world” szöveget, majd nyomja le az **Esc** billentyűt, írja be a `:x` parancsot, és nyomja le az **Enter** billentyűt.

Ebben a példában egy blobot töltünk fel a legutóbbi lépésben, az [az storage blob upload](/cli/azure/storage/blob) paranccsal létrehozott tárolóba.

```azurecli-interactive
az storage blob upload \
    --container-name mystoragecontainer \
    --name blobName \
    --file ~/path/to/local/file
```

Ha az imént leírt módon hozott létre fájlt az Azure Cloud Shellben, használhatja inkább ezt a CLI-parancsot (itt ugyan nem kellett útvonalat megadni, mivel a fájl az alapkönyvtárban lett létrehozva, normális esetben azonban meg kellene adni az útvonalat):

```azurecli-interactive
az storage blob upload \
    --container-name mystoragecontainer \
    --name helloworld \
    --file helloworld
```

Ez az eljárás létrehozza a blobot, ha az még nem létezett, és felülírja, ha már igen. Mielőtt továbblépne, töltsön fel annyi fájlt, amennyit csak szeretne.

Ha egyszerre több fájlt szeretne feltölteni, használhatja az [az storage blob upload-batch](/cli/azure/storage/blob) parancsot.

## <a name="list-the-blobs-in-a-container"></a>A tárolóban lévő blobok listázása

Listázza ki a tárolóban található blobokat az [az storage blob list](/cli/azure/storage/blob) paranccsal.

```azurecli-interactive
az storage blob list \
    --container-name mystoragecontainer \
    --output table
```

## <a name="download-a-blob"></a>Blob letöltése

Az [az storage blob download](/cli/azure/storage/blob) paranccsal letöltheti a korábban feltöltött blobot.

```azurecli-interactive
az storage blob download \
    --container-name mystoragecontainer \
    --name blobName \
    --file ~/destination/path/for/file
```

## <a name="data-transfer-with-azcopy"></a>Adatátvitel az AzCopy használatával

Az [AzCopy](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) segédprogram az Azure Storage esetében egy további lehetőséget kínál az adatok nagy teljesítményű, parancsfájlalapú átvitelére. Az AzCopy segítségével blob, fájl és tábla típusú tárolókból és tárolókba vihet át adatokat.

Egy példaként íme a *myfile.txt* nevű fájl a *mystoragecontainer* tárolóba való feltöltésére szolgáló AzCopy-parancs.

```bash
azcopy \
    --source /mnt/myfiles \
    --destination https://mystorageaccount.blob.core.windows.net/mystoragecontainer \
    --dest-key <storage-account-access-key> \
    --include "myfile.txt"
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az erőforráscsoportjában lévő egyik erőforrásra sem (beleértve a jelen rövid útmutatóban létrehozott tárfiókot is), törölje az erőforráscsoportot az [az group delete](/cli/azure/group) paranccsal.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>További lépések

Ennek a rövid útmutatónak a segítségével elsajátította a fájlok a helyi lemez és az Azure Blob Storage valamely tárolója közötti átvitelét. Ha bővebb információra van szüksége a blobok Azure Storage-beli használatával kapcsolatban, lépjen tovább az Azure Blob Storage használatáról szóló oktatóanyagra.

> [!div class="nextstepaction"]
> [Útmutató: BLOB Storage-műveletek az Azure CLI-vel](storage-how-to-use-blobs-cli.md)
