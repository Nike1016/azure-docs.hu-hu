---
title: Az Azure Blob Storage csatlakoztatása fájlrendszerként Linux rendszeren | Microsoft Docs
description: Azure Blob Storage-tároló csatlakoztatása a Linux-alapú BIZTOSÍTÉKokkal
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 2/1/2019
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: 88002999baacf38b4afd40b574686457c48546e4
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68845024"
---
# <a name="how-to-mount-blob-storage-as-a-file-system-with-blobfuse"></a>BLOB Storage csatlakoztatása fájlrendszerként a blobfuse-mel

## <a name="overview"></a>Áttekintés
A [Blobfuse](https://github.com/Azure/azure-storage-fuse) egy virtuális fájlrendszer-illesztőprogram az Azure Blob Storage szolgáltatáshoz. A Blobfuse lehetővé teszi a meglévő blokk blob-adatai elérését a Storage-fiókban a Linux fájlrendszer használatával. A Blobfuse a virtuális könyvtár sémáját a Forward-Slash "/" karakterrel használja határolójelként.  

Ebből az útmutatóból megtudhatja, hogyan használhatja a blobfuse, és hogyan csatlakoztathat blob Storage-tárolót Linux rendszeren, és hogyan férhet hozzá az adatbázisokhoz. Ha többet szeretne megtudni a blobfuse, olvassa el a részleteket a [blobfuse](https://github.com/Azure/azure-storage-fuse)-tárházban.

> [!WARNING]
> A Blobfuse nem garantálja a 100%-os POSIX-megfelelőséget, mivel egyszerűen a kérelmeket a [blob REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api)-khoz fordítja. Például az átnevezési műveletek a POSIX-ben atomiak, de nem a blobfuse.
> A natív fájlrendszer és a blobfuse közötti különbségek teljes listájáért látogasson el [a blobfuse-forráskód adattárára](https://github.com/azure/azure-storage-fuse).
> 

## <a name="install-blobfuse-on-linux"></a>A blobfuse telepítése Linux rendszeren
A Blobfuse bináris fájljai a [Linux](https://docs.microsoft.com/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software) for Ubuntu és a RHEL disztribúciók Microsoft-szoftverek tárházában érhetők el. A blobfuse az adott disztribúcióra való telepítéséhez konfigurálja az egyik tárházat a listából. A forráskód bináris fájljait az [Azure Storage telepítési lépéseit](https://github.com/Azure/azure-storage-fuse/wiki/1.-Installation#option-2---build-from-source) követve is létrehozhatja, ha nincs elérhető bináris fájl a terjesztéshez.

A Blobfuse támogatja az Ubuntu 14,04, 16,04 és 18,04 rendszereken való telepítést. Futtassa ezt a parancsot, és győződjön meg arról, hogy telepítve van-e a telepített verziók egyike:
```
lsb_release -a
```

### <a name="configure-the-microsoft-package-repository"></a>A Microsoft Package adattár konfigurálása
A [Linux-csomagok tárházának](https://docs.microsoft.com/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software)konfigurálása a Microsoft-termékekhez.

Példa egy vállalati Linux 6 disztribúcióra:
```bash
sudo rpm -Uvh https://packages.microsoft.com/config/rhel/6/packages-microsoft-prod.rpm
```

Hasonlóképpen módosítsa az URL-címet `.../rhel/7/...` úgy, hogy az a nagyvállalati Linux 7 disztribúcióra mutasson.

Egy másik példa Ubuntu 14,04-disztribúcióra:
```bash
wget https://packages.microsoft.com/config/ubuntu/14.04/packages-microsoft-prod.deb
sudo dpkg -i packages-microsoft-prod.deb
sudo apt-get update
```

Hasonlóképpen módosítsa az URL-címet `.../ubuntu/16.04/...` a `.../ubuntu/18.04/...` vagy a értékre egy másik Ubuntu-verzióra való hivatkozáshoz.

### <a name="install-blobfuse"></a>A blobfuse telepítése

Ubuntu/Debian disztribúcióban:
```bash
sudo apt-get install blobfuse
```

Vállalati Linux-disztribúcióban:
```bash    
sudo yum install blobfuse
```

## <a name="prepare-for-mounting"></a>Felkészülés a csatlakoztatásra
A Blobfuse natív teljesítményt nyújt azáltal, hogy ideiglenes elérési utat igényel a fájlrendszerben a megnyitott fájlok puffereléséhez és gyorsítótárazásához. Ehhez az ideiglenes elérési úthoz válassza a leginkább megfelelő lemezt, vagy használjon egy Ramdisk-t a legjobb teljesítmény érdekében. 

> [!NOTE]
> A Blobfuse az összes megnyitott fájl tartalmát az ideiglenes elérési úton tárolja. Ügyeljen arra, hogy elegendő lemezterület álljon rendelkezésre az összes megnyitott fájl számára. 
> 

### <a name="optional-use-a-ramdisk-for-the-temporary-path"></a>Választható A Ramdisk használata az ideiglenes elérési úthoz
Az alábbi példa egy 16 GB-os Ramdisk-t és egy blobfuse könyvtárat hoz létre. Válassza ki az igényeinek megfelelő méretet. Ez a Ramdisk lehetővé teszi, hogy a blobfuse legfeljebb 16 GB méretű fájlokat nyisson meg. 
```bash
sudo mount -t tmpfs -o size=16g tmpfs /mnt/ramdisk
sudo mkdir /mnt/ramdisk/blobfusetmp
sudo chown <youruser> /mnt/ramdisk/blobfusetmp
```

### <a name="use-an-ssd-as-a-temporary-path"></a>SSD használata ideiglenes útvonalként
Az Azure-ban a virtuális gépeken elérhető ideiglenes lemezeket (SSD-ket) használhatja a blobfuse alacsony késleltetésű pufferének biztosításához. Az Ubuntu-disztribúciókban ez az ideiglenes lemez a "/mnt"-ra van csatlakoztatva. A Red Hat és a CentOS disztribúciókban a lemez a "/mnt/Resource/"-ra van csatlakoztatva.

Győződjön meg arról, hogy a felhasználó rendelkezik az ideiglenes elérési úttal:
```bash
sudo mkdir /mnt/resource/blobfusetmp -p
sudo chown <youruser> /mnt/resource/blobfusetmp
```

### <a name="configure-your-storage-account-credentials"></a>A Storage-fiók hitelesítő adatainak konfigurálása
A Blobfuse használatához a hitelesítő adatokat a következő formátumban kell szövegfájlba menteni: 

```
accountName myaccount
accountKey storageaccesskey
containerName mycontainer
```
A `accountName` a Storage-fiók előtagja – nem a teljes URL-cím.

Fájl létrehozása a következő használatával:

```
touch ~/fuse_connection.cfg
```

A fájl létrehozása és szerkesztése után ügyeljen arra, hogy korlátozza a hozzáférést, hogy más felhasználók ne tudják elolvasni.
```bash
chmod 600 fuse_connection.cfg
```

> [!NOTE]
> Ha a Windowson létrehozta a konfigurációs fájlt, futtassa a parancsot `dos2unix` a fájl megtisztításához és a UNIX formátumba való átalakításához. 
>

### <a name="create-an-empty-directory-for-mounting"></a>Üres könyvtár létrehozása a csatlakoztatáshoz
```bash
mkdir ~/mycontainer
```

## <a name="mount"></a>Csatlakoztatás

> [!NOTE]
> A csatlakoztatási lehetőségek teljes listáját [a blobfuse](https://github.com/Azure/azure-storage-fuse#mount-options)-tárházban találja.  
> 

A blobfuse csatlakoztatásához futtassa a következő parancsot a felhasználóval. Ez a parancs a "/Path/to/fuse_connection.cfg" helyen megadott tárolót csatlakoztatja a (z) "/mycontainer" helyre.

```bash
sudo blobfuse ~/mycontainer --tmp-path=/mnt/resource/blobfusetmp  --config-file=/path/to/fuse_connection.cfg -o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120
```

Most már hozzáférhet a blokk blobokhoz a normál fájlrendszerű API-kon keresztül. Az a felhasználó, aki csatlakoztatja a könyvtárat, alapértelmezés szerint az egyetlen olyan személy, aki hozzáférést biztosít a hozzáféréshez. Ha engedélyezni szeretné az összes felhasználó hozzáférését, a kapcsolón ```-o allow_other```keresztül is csatlakoztathatja. 

```bash
cd ~/mycontainer
mkdir test
echo "hello world" > test/blob.txt
```

## <a name="next-steps"></a>További lépések

* [Blobfuse kezdőlapja](https://github.com/Azure/azure-storage-fuse#blobfuse)
* [Blobfuse-problémák jelentése](https://github.com/Azure/azure-storage-fuse/issues) 

