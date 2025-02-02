---
title: Rövid útmutató az Azure-fájlmegosztásoknak az Azure PowerShell-lel történő felügyeletéhez
description: Ebből a rövid útmutatóból megtudhatja, hogyan felügyelheti az Azure-fájlmegosztásokat az Azure PowerShell-lel.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 10/26/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 802ad497f95a43665665d7e7dbd06c9081eba74a
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699501"
---
# <a name="quickstart-create-and-manage-an-azure-file-share-with-azure-powershell"></a>Gyors útmutató: Azure-fájlmegosztás létrehozása és kezelése Azure PowerShell 
Ez az útmutató az [Azure-fájlmegosztások](storage-files-introduction.md) PowerShell-lel való használatának alapvető lépéseit mutatja be. Az Azure-fájlmegosztások nem különböznek más fájlmegosztásoktól, a tárolásuk azonban a felhőben történik, és az Azure platform nyújt számukra támogatást. Az Azure-fájlmegosztások támogatják az iparági szabvány SMB protokollt, és lehetővé teszik a több gép, alkalmazás és példány közötti fájlmegosztást. 

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha helyileg szeretné telepíteni és használni a PowerShellt, az útmutatóhoz az Azure PowerShell-modul az 0,7-es vagy újabb verziójára lesz szükség. A jelenleg futtatott Azure PowerShell-modul verziójának kiderítéséhez futtassa a következő parancsot: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor a `Login-AzAccount` parancsot is futtatnia kell az Azure-fiókba való bejelentkezéshez.

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot
Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Ha még nem rendelkezik Azure-erőforráscsoport használatával, létrehozhat egy újat a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) parancsmaggal. 

A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az USA keleti régiójában:

```azurepowershell-interactive
New-AzResourceGroup `
    -Name myResourceGroup `
    -Location EastUS
```

## <a name="create-a-storage-account"></a>Tárfiók létrehozása
A tárfiókok az Azure-fájlmegosztások vagy más tárolási erőforrások, például blobok vagy üzenetsorok üzembe helyezéséhez használható tárolók közös készletei. Egy tárfiók korlátlan számú megosztást tartalmazhat, egy megosztás pedig korlátlan számú fájl tárolására használható, egészen a tárfiók kapacitásának korlátjáig.

Ez a példa egy Storage-fiókot hoz létre a [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) parancsmag használatával. A Storage-fiók neve *mystorageaccount\<Random Number >* , és az adott Storage-fiókra mutató hivatkozás a **$storageAcct**változóban van tárolva. A tárfiókok névnek egyedinek kell lenniük, ezért a `Get-Random` paranccsal fűzzön számot a névhez. 

```azurepowershell-interactive 
$storageAcct = New-AzStorageAccount `
                  -ResourceGroupName "myResourceGroup" `
                  -Name "mystorageacct$(Get-Random)" `
                  -Location eastus `
                  -SkuName Standard_LRS 
```

## <a name="create-an-azure-file-share"></a>Azure-fájlmegosztás létrehozása
Most létrehozhatja az első Azure-fájlmegosztását. A [New-AzStorageShare](/powershell/module/az.storage/New-AzStorageShare) parancsmag használatával létrehozhat fájlmegosztást. Ez a példa egy `myshare` nevű fájlmegosztást hoz létre.

```azurepowershell-interactive
New-AzStorageShare `
   -Name myshare `
   -Context $storageAcct.Context
```

A fájlmegosztások neve csak kisbetűket, számokat és kötőjeleket tartalmazhat, de nem kezdődhet kötőjellel. A fájlmegosztások és fájlok elnevezésére vonatkozó információkért lásd: [Naming and Referencing Shares, Directories, Files, and Metadata](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata) (Megosztások, könyvtárak, fájlok és metaadatok elnevezése és hivatkozása).

## <a name="use-your-azure-file-share"></a>Az Azure-fájlmegosztás használata
Az Azure Files két módszert biztosít a fájloknak és mappáknak az Azure-fájlmegosztásban való használatához: az iparági szabvány [Server Message Block (SMB) protokollt](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) és a [Fájl REST protokollt](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api). 

A fájlmegosztás SMB-vel való csatlakoztatásához tekintse meg a használt operációs rendszernek megfelelő dokumentumot az alábbiak közül:
- [Windows](storage-how-to-use-files-windows.md)
- [Linux](storage-how-to-use-files-linux.md)
- [macOS](storage-how-to-use-files-mac.md)

### <a name="using-an-azure-file-share-with-the-file-rest-protocol"></a>Azure-fájlmegosztások használata a Fájl REST protokollal 
Közvetlenül a file REST protokoll használatával dolgozhat közvetlenül (például a REST HTTP-hívások saját kezűleg), de a fájl REST protokoll használatának leggyakoribb módja az Azure PowerShell modul, az [Azure CLI](storage-how-to-use-files-cli.md)vagy egy Azure Storage SDK használata Adjon meg egy szép burkolót a fájl REST protokoll köré a választott Scripting/programozási nyelven.  

Az Azure-fájlmegosztást a legtöbb esetben az SMB protokollon keresztül fogja használni, mivel ez lehetővé teszi a mások által is vélhetően használt meglévő alkalmazások és eszközök használatát. A Fájl REST API használata azonban számos előnnyel jár az SMB-vel szemben, például a következő esetekben:

- A fájlmegosztást a PowerShell Cloud Shellben böngészi (amely nem tud fájlmegosztásokat csatlakoztatni az SMB-n keresztül).
- Olyan ügyfélről kell szkriptet vagy alkalmazást futtatnia, amely nem tud SMB-megosztásokat csatlakoztatni, például a helyszíni ügyfelekről, amelyekhez nincs feloldva a 445-ös port.
- Ki szeretné használni a kiszolgáló nélküli erőforrások, például az [Azure Functions](../../azure-functions/functions-overview.md) előnyeit. 

Az alábbi példák bemutatják, hogyan kezelheti az Azure-fájlmegosztást a fájl REST protokollal a Azure PowerShell modul használatával. 

#### <a name="create-directory"></a>Címtár létrehozása
Ha a *myDirectory* nevű új könyvtárat szeretné létrehozni az Azure-fájlmegosztás gyökerében, használja a [New-AzStorageDirectory](/powershell/module/az.storage/New-AzStorageDirectory) parancsmagot.

```azurepowershell-interactive
New-AzStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName "myshare" `
   -Path "myDirectory"
```

#### <a name="upload-a-file"></a>Fájl feltöltése
A fájlok a [set-AzStorageFileContent](/powershell/module/az.storage/Set-AzStorageFileContent) parancsmag használatával történő feltöltésének bemutatásához először létre kell hoznia egy fájlt a PowerShell-Cloud Shell a feltöltéshez. 

Ez a példa az aktuális dátumot és időt helyezi az ideiglenes meghajtón lévő új fájlba, majd feltölti a fájlt a fájlmegosztásra.

```azurepowershell-interactive
# this expression will put the current date and time into a new file on your scratch drive
Get-Date | Out-File -FilePath "C:\Users\ContainerAdministrator\CloudDrive\SampleUpload.txt" -Force

# this expression will upload that newly created file to your Azure file share
Set-AzStorageFileContent `
   -Context $storageAcct.Context `
   -ShareName "myshare" `
   -Source "C:\Users\ContainerAdministrator\CloudDrive\SampleUpload.txt" `
   -Path "myDirectory\SampleUpload.txt"
```   

Ha helyileg futtatja a PowerShellt, helyettesítse be a `C:\Users\ContainerAdministrator\CloudDrive\` elérési utat a gép egy meglévő elérési útjával.

A fájl feltöltése után a [Get-AzStorageFile](/powershell/module/Az.Storage/Get-AzStorageFile) parancsmaggal ellenőrizze, hogy a fájl fel lett-e töltve az Azure-fájlmegosztást. 

```azurepowershell-interactive
Get-AzStorageFile -Context $storageAcct.Context -ShareName "myshare" -Path "myDirectory" 
```

#### <a name="download-a-file"></a>Fájl letöltése
A [Get-AzStorageFileContent](/powershell/module/az.storage/Get-AzStorageFilecontent) parancsmag használatával letöltheti az imént feltöltött fájl másolatát a Cloud shell.

```azurepowershell-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists because you've run this example before.
Remove-Item `
     -Path "C:\Users\ContainerAdministrator\CloudDrive\SampleDownload.txt" `
     -Force `
     -ErrorAction SilentlyContinue

Get-AzStorageFileContent `
    -Context $storageAcct.Context `
    -ShareName "myshare" `
    -Path "myDirectory\SampleUpload.txt" ` 
    -Destination "C:\Users\ContainerAdministrator\CloudDrive\SampleDownload.txt"
```

A fájl letöltése után a `Get-ChildItem` parancsmaggal tekintheti meg, hogy a fájl le lett-e töltve a PowerShell Cloud Shell ideiglenes meghajtójára.

```azurepowershell-interactive
Get-ChildItem -Path "C:\Users\ContainerAdministrator\CloudDrive"
``` 

#### <a name="copy-files"></a>Fájlok másolása
A fájlok egy fájlmegosztásról egy másikra, vagy Azure Blob Storage-tárolóról/-tárolóra való másolása gyakori feladat. A funkció bemutatásához létrehozhat egy új megosztást, és az imént feltöltött fájlt átmásolhatja erre az új megosztásra a [Start-AzStorageFileCopy](/powershell/module/az.storage/Start-AzStorageFileCopy) parancsmag használatával. 

```azurepowershell-interactive
New-AzStorageShare `
    -Name "myshare2" `
    -Context $storageAcct.Context
  
New-AzStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName "myshare2" `
   -Path "myDirectory2"

Start-AzStorageFileCopy `
    -Context $storageAcct.Context `
    -SrcShareName "myshare" `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestShareName "myshare2" `
    -DestFilePath "myDirectory2\SampleCopy.txt" `
    -DestContext $storageAcct.Context
```

Ha most listázza az új megosztásban lévő fájlokat, meg kell jelennie a fájl másolatának.

```azurepowershell-interactive
Get-AzStorageFile -Context $storageAcct.Context -ShareName "myshare2" -Path "myDirectory2" 
```

Habár a `Start-AzStorageFileCopy` parancsmag kényelmes az Azure-fájlmegosztás és az Azure Blob Storage-tárolók közötti alkalmi fájlokra való váltáshoz, javasoljuk, hogy a nagyobb áthelyezéshez AzCopy (az áthelyezett fájlok száma vagy mérete tekintetében). További információ: [AzCopy segédprogram Windows rendszeren](../common/storage-use-azcopy.md) és [AzCopy segédprogram Linux rendszeren](../common/storage-use-azcopy-linux.md). Az AzCopy parancsmagnak helyileg kell telepítve lennie – a Cloud Shellen nem érhető el. 

## <a name="create-and-manage-share-snapshots"></a>Megosztási pillanatképek létrehozása és felügyelete
Az Azure-fájlmegosztással végezhető egyik további hasznos feladat a megosztási pillanatképek létrehozása. A pillanatképek megőrzik az Azure-fájlmegosztások adott időpontban látható állapotát. A megosztási pillanatképek hasonlóak az esetleg már ismert operációsrendszer-technológiákhoz, például a következőkhöz:
- [Kötet árnyékmásolata szolgáltatás (VSS)](https://docs.microsoft.com/windows/desktop/VSS/volume-shadow-copy-service-portal) Windows fájlrendszerekhez (például NTFS és ReFS)
- [Logikaikötet-kezelő (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) pillanatképek Linux rendszerekhez
- [Apple fájlrendszer (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) pillanatképek macOS rendszerhez. 
 Megoszthat megosztási pillanatképet a megosztáshoz a ( `Snapshot` z) metódus használatával a fájlmegosztás PowerShell-objektumán, amely a [Get-AzStorageShare](/powershell/module/az.storage/Get-AzStorageShare) parancsmaggal kérhető le. 

```azurepowershell-interactive
$share = Get-AzStorageShare -Context $storageAcct.Context -Name "myshare"
$snapshot = $share.Snapshot()
```

### <a name="browse-share-snapshots"></a>Böngészés a megosztási pillanatképekben
A pillanatkép referenciáját (`$snapshot`) a `Get-AzStorageFile` parancsmag `-Share` paraméterébe illesztve böngészhet a megosztási pillanatkép tartalmában.

```azurepowershell-interactive
Get-AzStorageFile -Share $snapshot
```

### <a name="list-share-snapshots"></a>Megosztási pillanatképek felsorolása
A következő paranccsal tekintheti meg a megosztáshoz készített pillanatképek listáját.

```azurepowershell-interactive
Get-AzStorageShare -Context $storageAcct.Context | Where-Object { $_.Name -eq "myshare" -and $_.IsSnapshot -eq $true }
```

### <a name="restore-from-a-share-snapshot"></a>Visszaállítás megosztási pillanatképből
A fájlokat a korábban már használt `Start-AzStorageFileCopy` paranccsal állíthatja vissza. A rövid útmutató során először töröljük a korábban feltöltött `SampleUpload.txt` fájlt, hogy visszaállíthassuk a pillanatképből.

```azurepowershell-interactive
# Delete SampleUpload.txt
Remove-AzStorageFile `
    -Context $storageAcct.Context `
    -ShareName "myshare" `
    -Path "myDirectory\SampleUpload.txt"
 # Restore SampleUpload.txt from the share snapshot
Start-AzStorageFileCopy `
    -SrcShare $snapshot `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestContext $storageAcct.Context `
    -DestShareName "myshare" `
    -DestFilePath "myDirectory\SampleUpload.txt"
```

### <a name="delete-a-share-snapshot"></a>Megosztási pillanatkép törlése
A Remove [-AzStorageShare](/powershell/module/az.storage/Remove-AzStorageShare) parancsmag használatával törölheti a megosztási pillanatképet a `$snapshot` `-Share` paraméterre mutató hivatkozást tartalmazó változóval.

```azurepowershell-interactive
Remove-AzStorageShare -Share $snapshot
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha elkészült, használhatja a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) parancsmagot az erőforráscsoport és az összes kapcsolódó erőforrás eltávolításához. 

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

Egyesével is eltávolíthatja az erőforrásokat:

- A gyors útmutatóhoz létrehozott Azure-fájlmegosztások eltávolítása.

    ```azurepowershell-interactive
    Get-AzStorageShare -Context $storageAcct.Context | Where-Object { $_.IsSnapshot -eq $false } | ForEach-Object { 
        Remove-AzStorageShare -Context $storageAcct.Context -Name $_.Name
    }
    ```

- Magának a tárfióknak az eltávolítása (ez implicit módon eltávolítja a létrehozott Azure-fájlmegosztásokat, valamint az esetlegesen létrehozott egyéb tárolási erőforrásokat, például az Azure Blob Storage-tárolókat).

    ```azurepowershell-interactive
    Remove-AzStorageAccount -ResourceGroupName $storageAcct.ResourceGroupName -Name $storageAcct.StorageAccountName
    ```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Mi az Azure Files?](storage-files-introduction.md)
