---
title: Azure File Sync üzembe helyezése | Microsoft Docs
description: Megtudhatja, hogyan helyezheti üzembe a Azure File Synct az elejétől a végéig.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/19/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: de0eb685e212b59705d8d659cbe9627338697e9d
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854525"
---
# <a name="deploy-azure-file-sync"></a>Az Azure File Sync üzembe helyezése
A Azure File Sync segítségével központilag kezelheti a szervezete fájlmegosztást Azure Filesban, miközben megőrizheti a helyszíni fájlkiszolgáló rugalmasságát, teljesítményét és kompatibilitását. Azure File Sync átalakítja a Windows Servert az Azure-fájlmegosztás gyors gyorsítótárba. A Windows Serveren elérhető bármely protokoll használatával helyileg férhet hozzá az adataihoz, beleértve az SMB-t, az NFS-t és a FTPS is. Tetszőleges számú gyorsítótárral rendelkezhet a világ minden tájáról.

Javasoljuk, hogy olvassa el a [Azure Files központi telepítésének](storage-files-planning.md) megtervezését és a [Azure file Sync központi telepítés](storage-sync-files-planning.md) megtervezését, mielőtt elvégezte a jelen cikkben ismertetett lépéseket.

## <a name="prerequisites"></a>Előfeltételek
* Egy Azure-fájlmegosztás abban a régióban, amelyet telepíteni kíván Azure File Sync. További információkért lásd:
    - A [régió rendelkezésre állása](storage-sync-files-planning.md#region-availability) Azure file Sync számára.
    - [Hozzon létre egy fájlmegosztást](storage-how-to-create-file-share.md) a fájlmegosztás létrehozásának lépésenkénti leírásához.
* A Windows Server vagy a Windows Server-fürt legalább egy támogatott példánya Azure File Sync-vel való szinkronizálásra. A Windows Server támogatott verzióival kapcsolatos további információkért lásd: együttműködés [a Windows Serverrel](storage-sync-files-planning.md#azure-file-sync-system-requirements-and-interoperability).
* Az az PowerShell-modul a PowerShell 5,1 vagy a PowerShell 6 + használatával is használható. Használhatja az az PowerShell-modult a Azure File Synchoz bármely támogatott rendszeren, beleértve a nem Windows rendszerű rendszereket is, azonban a kiszolgáló regisztrációs parancsmagját mindig futtatni kell a regisztrálni kívánt Windows Server-példányon (ez közvetlenül vagy a PowerShellen keresztül végezhető el). távoli eljáráshívás). Windows Server 2012 R2 rendszeren ellenőrizheti, hogy legalább a PowerShell 5,1-et futtatja-e. a $PSVersionTable objektum PSVersion tulajdonságának értékét tekinti meg: \*

    ```powershell
    $PSVersionTable.PSVersion
    ```

    Ha a PSVersion értéke kisebb, mint 5,1. , a Windows Server 2012 R2 legtöbb friss telepítésének esetében pedig könnyedén frissítheti a [Windows Management Framework (WMF) 5,1](https://www.microsoft.com/download/details.aspx?id=54616)letöltésével és telepítésével. \* A Windows Server 2012 R2-hoz letölthető és telepíthető megfelelő csomag a **Win 8.1 andw2k12r2-kb\*\*\*\*\*\*\*-x64. msu**. 

    A PowerShell 6 + bármilyen támogatott rendszerrel használható, és a [GitHub-oldalán](https://github.com/PowerShell/PowerShell#get-powershell)is letölthető. 

    > [!Important]  
    > Ha azt tervezi, hogy a kiszolgáló regisztrációjának felhasználói felületét használja, és nem közvetlenül a PowerShellből regisztrálja, akkor a PowerShell 5,1-et kell használnia.

* Ha a PowerShell 5,1 használatát választotta, győződjön meg arról, hogy legalább .NET 4.7.2 van telepítve. További információ a [.NET-keretrendszer verzióiról és függőségeiről](https://docs.microsoft.com/dotnet/framework/migration-guide/versions-and-dependencies) a rendszeren.

    > [!Important]  
    > Ha a .net 4.7.2 + rendszert a Windows Server Core verzióra telepíti, a `quiet` és `norestart` a jelzőket kell telepítenie, vagy a telepítés sikertelen lesz. Ha például a .NET 4,8-et telepíti, a parancs a következőhöz hasonlóan fog kinézni:
    > ```PowerShell
    > Start-Process -FilePath "ndp48-x86-x64-allos-enu.exe" -ArgumentList "/q /norestart" -Wait
    > ```

* Az az PowerShell-modul, amely a következő útmutatás szerint telepíthető: [Azure PowerShell telepítése és konfigurálása](https://docs.microsoft.com/powershell/azure/install-Az-ps).
     
    > [!Note]  
    > Az az. StorageSync modul mostantól automatikusan települ az az PowerShell modul telepítésekor.

## <a name="prepare-windows-server-to-use-with-azure-file-sync"></a>A Windows Server előkészítése az Azure File Sync használatára
Az **Internet Explorer fokozott biztonsági beállításainak**letiltásával minden olyan kiszolgáló esetében, amelyet Azure file Synchoz kíván használni, beleértve a feladatátvevő fürt minden egyes kiszolgálói csomópontját is. Erre csak a kiszolgáló kezdeti regisztrációja esetén van szükség. A kiszolgáló regisztrációja után újra engedélyezheti.

# <a name="portaltabazure-portal"></a>[Portál](#tab/azure-portal)
> [!Note]  
> Ezt a lépést kihagyhatja, ha a Windows Server Core-on telepíti Azure File Sync.

1. Nyissa meg a Kiszolgálókezelő alkalmazást.
2. Kattintson a **helyi kiszolgáló**elemre:  
    !["Helyi kiszolgáló" a Kiszolgálókezelő felhasználói felületének bal oldalán](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-1.PNG)
3. A **Tulajdonságok** lapon, válassza ki az **Internet Explorer fokozott biztonsági beállításai** hivatkozást.  
    ![A Kiszolgálókezelő felhasználói felületének "IE fokozott biztonsági beállításai" panelje](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-2.PNG)
4. Az **Internet Explorer fokozott biztonsági beállításai** párbeszédpanelen válassza ki a **ki** lehetőséget a **rendszergazdák** és a **felhasználók**számára:  
    ![Az Internet Explorer fokozott biztonsági beállításainak pop-ablaka kiválasztva](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-3.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Az Internet Explorer fokozott biztonsági beállításainak letiltásához futtassa a következőt egy emelt szintű PowerShell-munkamenetből:

```powershell
$installType = (Get-ItemProperty "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\").InstallationType

# This step is not required for Server Core
if ($installType -ne "Server Core") {
    # Disable Internet Explorer Enhanced Security Configuration 
    # for Administrators
    Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A7-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0 -Force
    
    # Disable Internet Explorer Enhanced Security Configuration 
    # for Users
    Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A8-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0 -Force
    
    # Force Internet Explorer closed, if open. This is required to fully apply the setting.
    # Save any work you have open in the IE browser. This will not affect other browsers,
    # including Microsoft Edge.
    Stop-Process -Name iexplore -ErrorAction SilentlyContinue
}
``` 

---

## <a name="deploy-the-storage-sync-service"></a>A Társzinkronizálási szolgáltatás üzembe helyezése 
A Azure File Sync üzembe helyezése a **Storage Sync szolgáltatás** erőforrásának a kiválasztott előfizetéshez tartozó erőforráscsoporthoz való elhelyezésével kezdődik. Javasoljuk, hogy a lehető legtöbbet kiépítse igény szerint. Létre fog hozni egy megbízhatósági kapcsolatot a kiszolgálók és az erőforrás között, és a kiszolgáló csak egy Storage Sync szolgáltatásban regisztrálható. Ezért javasoljuk, hogy több tárolási szinkronizálási szolgáltatást telepítsen a kiszolgálók különböző csoportjaihoz. Ne feledje, hogy a különböző Storage Sync Services-kiszolgálók nem tudnak szinkronizálni egymással.

> [!Note]
> A Storage Sync szolgáltatás örökli a hozzáférési engedélyeket az előfizetésből és az erőforráscsoporthoz telepített erőforrás-csoportból. Javasoljuk, hogy alaposan vizsgálja meg, ki férhet hozzá. Az írási hozzáféréssel rendelkező entitások megkezdhetik a Storage Sync szolgáltatásban regisztrált kiszolgálók új fájljainak szinkronizálását, és az adatok a számukra elérhető Azure Storage-ba való áthaladását okozzák.

# <a name="portaltabazure-portal"></a>[Portál](#tab/azure-portal)
A Storage Sync szolgáltatás üzembe helyezéséhez lépjen a [Azure Portal](https://portal.azure.com/), kattintson az *erőforrás létrehozása* elemre, majd keresse meg Azure file Sync. A keresési eredmények között válassza a **Azure file Sync**lehetőséget, majd a **Létrehozás** elemre kattintva nyissa meg a **Storage Sync telepítése** lapot.

A megnyíló panelen adja meg a következőket:

- **Név**: A Társzinkronizálási szolgáltatás egyedi neve (előfizetésenként).
- **Előfizetés**: Az előfizetés, amelyben létre kívánja hozni a Storage Sync szolgáltatást. A szervezet konfigurációs stratégiájától függően előfordulhat, hogy egy vagy több előfizetéshez fér hozzá. Az Azure-előfizetés az egyes felhőalapú szolgáltatások (például a Azure Files) számlázásának legalapvetőbb tárolója.
- **Erőforráscsoport**: Az erőforráscsoportok az Azure-erőforrások, például egy Storage-fiók vagy egy Storage Sync szolgáltatás logikai csoportja. Létrehozhat egy új erőforráscsoportot, vagy használhat meglévő erőforráscsoportot Azure File Synchoz. (Az erőforráscsoportok tárolóként való használatát javasoljuk, hogy a szervezet számára logikailag elkülönítse az erőforrásokat, például a HR-erőforrások vagy egy adott projekt erőforrásainak csoportosítását.)
- **Hely**: Az a régió, amelyben a Azure File Sync központilag telepíteni kívánja. Ebben a listában csak a támogatott régiók érhetők el.

Ha elkészült, válassza a **Létrehozás** lehetőséget a Storage Sync szolgáltatás telepítéséhez.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Cserélje le **< Az_Region >** , **< RG_Name >** , és **< my_storage_sync_service >** a saját értékeire, majd használja a következő cmds a Storage Sync szolgáltatás létrehozásához és üzembe helyezéséhez:

```powershell
$hostType = (Get-Host).Name

if ($installType -eq "Server Core" -or $hostType -eq "ServerRemoteHost") {
    Connect-AzAccount -UseDeviceAuthentication
}
else {
    Connect-AzAccount
}

# this variable holds the Azure region you want to deploy 
# Azure File Sync into
$region = '<Az_Region>'

# Check to ensure Azure File Sync is available in the selected Azure
# region.
$regions = @()
Get-AzLocation | ForEach-Object { 
    if ($_.Providers -contains "Microsoft.StorageSync") { 
        $regions += $_.Location 
    } 
}

if ($regions -notcontains $region) {
    throw [System.Exception]::new("Azure File Sync is either not available in the selected Azure Region or the region is mistyped.")
}

# the resource group to deploy the Storage Sync Service into
$resourceGroup = '<RG_Name>'

# Check to ensure resource group exists and create it if doesn't
$resourceGroups = @()
Get-AzResourceGroup | ForEach-Object { 
    $resourceGroups += $_.ResourceGroupName 
}

if ($resourceGroups -notcontains $resourceGroup) {
    New-AzResourceGroup -Name $resourceGroup -Location $region
}

$storageSyncName = "<my_storage_sync_service>"
$storageSync = New-AzStorageSyncService -ResourceGroupName $resourceGroup -Name $storageSyncName -Location $region
```

---

## <a name="install-the-azure-file-sync-agent"></a>Az Azure File Sync-ügynök telepítése
Az Azure File Sync ügynök egy letölthető csomag, amely lehetővé teszi a Windows Server szinkronizálását Azure-fájlmegosztással. 

# <a name="portaltabazure-portal"></a>[Portál](#tab/azure-portal)
Az ügynököt a [Microsoft letöltőközpontból](https://go.microsoft.com/fwlink/?linkid=858257)töltheti le. Ha a letöltés befejeződött, kattintson duplán az MSI-csomagra a Azure File Sync-ügynök telepítésének elindításához.

> [!Important]  
> Ha a Azure File Sync feladatátvevő fürttel kívánja használni, akkor a Azure File Sync ügynököt a fürt minden csomópontjára telepíteni kell. A fürt minden csomópontjának regisztrálva kell lennie a Azure File Sync használatához.

Javasoljuk, hogy tegye a következőket:
- A hibaelhárítás és a kiszolgáló karbantartásának leegyszerűsítése érdekében hagyja meg az alapértelmezett telepítési útvonalat (C:\Program Files\Azure\StorageSyncAgent).
- Microsoft Update engedélyezése a Azure File Sync naprakészen tartása érdekében. A Azure File Sync ügynök összes frissítése, beleértve a szolgáltatás frissítéseit és gyorsjavításait is, Microsoft Update. Javasoljuk, hogy a legújabb frissítést a Azure File Sync telepítéséhez telepítse. További információ: [Azure file Sync frissítési szabályzat](storage-sync-files-planning.md#azure-file-sync-agent-update-policy).

Ha a Azure File Sync-ügynök telepítése befejeződött, a kiszolgáló regisztrációs FELÜLETe automatikusan megnyílik. A regisztrálás előtt rendelkeznie kell egy Storage Sync szolgáltatással; Tekintse meg a Storage Sync szolgáltatás létrehozásának következő szakaszát.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
A következő PowerShell-kód futtatásával töltse le az operációs rendszer Azure File Sync ügynökének megfelelő verzióját, és telepítse azt a rendszeren.

> [!Important]  
> Ha a Azure File Sync feladatátvevő fürttel kívánja használni, akkor a Azure File Sync ügynököt a fürt minden csomópontjára telepíteni kell. A fürt minden egyes csomópontjának regisztrálnia kell a Azure File Syncsal való együttműködéshez.

```powershell
# Gather the OS version
$osver = [System.Environment]::OSVersion.Version

# Download the appropriate version of the Azure File Sync agent for your OS.
if ($osver.Equals([System.Version]::new(10, 0, 17763, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2019 `
        -OutFile "StorageSyncAgent.msi" 
} elseif ($osver.Equals([System.Version]::new(10, 0, 14393, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2016 `
        -OutFile "StorageSyncAgent.msi" 
} elseif ($osver.Equals([System.Version]::new(6, 3, 9600, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2012R2 `
        -OutFile "StorageSyncAgent.msi" 
} else {
    throw [System.PlatformNotSupportedException]::new("Azure File Sync is only supported on Windows Server 2012 R2, Windows Server 2016, and Windows Server 2019")
}

# Install the MSI. Start-Process is used to PowerShell blocks until the operation is complete.
# Note that the installer currently forces all PowerShell sessions closed - this is a known issue.
Start-Process -FilePath "StorageSyncAgent.msi" -ArgumentList "/quiet" -Wait

# Note that this cmdlet will need to be run in a new session based on the above comment.
# You may remove the temp folder containing the MSI and the EXE installer
Remove-Item -Path ".\StorageSyncAgent.msi" -Recurse -Force
```

---

## <a name="register-windows-server-with-storage-sync-service"></a>Windows-kiszolgáló regisztrálása a Társzinkronizálási szolgáltatással
A Windows Server regisztrálásával a Társzinkronizálási szolgáltatásra megbízhatósági kapcsolatot hoz létre a kiszolgáló (vagy fürt) és a Társzinkronizálási szolgáltatás között. A kiszolgáló csak egy Társzinkronizálási szolgáltatásra regisztrálható és az ugyanahhoz a Társzinkronizálási szolgáltatáshoz társított kiszolgálókkal és Azure-fájlmegosztásokkal képes szinkronizálni.

> [!Note]
> A kiszolgáló regisztrálása az Azure-beli hitelesítő adataival megbízhatósági kapcsolatot hoz létre a Storage Sync szolgáltatás és a Windows Server között, azonban ezt követően a kiszolgáló létrehozza és felhasználja a saját identitását, amely mindaddig érvényes, amíg a kiszolgáló regisztrálva marad, és a az aktuális közös hozzáférésű aláírási jogkivonat (Storage SAS) érvényes. A kiszolgáló regisztrációjának törlése után nem lehet új SAS-jogkivonatot kiállítani a kiszolgálónak, így a kiszolgáló nem férhet hozzá az Azure-fájlmegosztás eléréséhez, és megszüntetheti a szinkronizálást.

# <a name="portaltabazure-portal"></a>[Portál](#tab/azure-portal)
A kiszolgáló regisztrációjának felhasználói felületének automatikusan meg kell nyílnia a Azure File Sync ügynök telepítése után. Ha nem, akkor manuálisan is megnyithatja a fájl helye: C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe. Amikor megnyílik a kiszolgáló regisztrációjának felhasználói felülete, a kezdéshez válassza a **Bejelentkezés** lehetőséget.

A bejelentkezést követően a rendszer a következő információk megadását kéri:

![A kiszolgáló regisztrációs felhasználói felület képernyőképe](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- **Azure-előfizetés**: A Storage Sync szolgáltatást tartalmazó előfizetés (lásd: [a Storage Sync szolgáltatás üzembe helyezése](#deploy-the-storage-sync-service)). 
- **Erőforráscsoport**: A Storage Sync szolgáltatást tartalmazó erőforráscsoport.
- **Storage Sync szolgáltatás**: Annak a Storage Sync szolgáltatásnak a neve, amellyel regisztrálni kíván.

Miután kiválasztotta a megfelelő adatokat, válassza a **regisztráció** lehetőséget a kiszolgáló regisztrációjának befejezéséhez. A regisztrációs folyamat részeként a rendszer újabb bejelentkezésre kéri fel.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
$registeredServer = Register-AzStorageSyncServer -ParentObject $storageSync
```

---

## <a name="create-a-sync-group-and-a-cloud-endpoint"></a>Szinkronizálási csoport és felhő végpont létrehozása
A szinkronizálási csoport határozza meg fájlok egy halmazára a szinkronizálási topológiát. A szinkronizálási csoporton belüli végpontokat a rendszer szinkronban tartja egymással. A szinkronizálási csoportnak tartalmaznia kell egy felhőbeli végpontot, amely Azure-fájlmegosztást és egy vagy több kiszolgáló végpontot jelöl. A kiszolgálói végpont egy regisztrált kiszolgáló elérési útját jelöli. A kiszolgálók több szinkronizálási csoportban is rendelkezhetnek kiszolgálói végpontokkal. Annyi szinkronizálási csoportot hozhat létre, amennyi szükséges a kívánt szinkronizálási topológia megfelelő leírásához.

A Felhőbeli végpont egy Azure-fájlmegosztás mutatója. Minden kiszolgálói végpont szinkronizálva lesz egy Felhőbeli végponttal, így a felhő végpontja a középpontba kerül. Az Azure-fájlmegosztás tárolási fiókjának ugyanabban a régióban kell lennie, mint a Storage Sync szolgáltatásnak. Az Azure-fájlmegosztás teljes egészében szinkronizálva lesz, egyetlen kivétellel: Egy NTFS-kötet rejtett "rendszerkötet-információ" mappájához hasonló speciális mappa lesz kiépítve. Ennek a könyvtárnak a neve ". SystemShareInformation". Fontos szinkronizálási metaadatokat tartalmaz, amelyek nem szinkronizálhatók más végpontokkal. Ne használja vagy törölje!

> [!Important]  
> A szinkronizálási csoportban bármilyen Felhőbeli végpontot vagy kiszolgálói végpontot módosíthat, és a fájlok szinkronizálva vannak a szinkronizálási csoport többi végpontján. Ha közvetlenül módosítja a Felhőbeli végpontot (Azure-fájlmegosztás), a módosításokat először egy Azure File Sync változás-észlelési feladatokkal kell felderíteni. A változás-észlelési feladatok csak 24 óránként egyszer indíthatók el egy Felhőbeli végponton. További információ: [Azure Files gyakori kérdések](storage-files-faq.md#afs-change-detection).

# <a name="portaltabazure-portal"></a>[Portál](#tab/azure-portal)
Egy szinkronizálási csoport létrehozásához nyissa meg a [Azure Portal](https://portal.azure.com/)a Storage Sync Service-t, majd válassza a **+ szinkronizálás csoport**:

![Új szinkronizálási csoport létrehozása az Azure portálon](media/storage-sync-files-deployment-guide/create-sync-group-1.png)

A megnyíló panelen adja meg a következő információkat a szinkronizálási csoport létrehozásához egy felhőbeli végponttal:

- **Szinkronizálási csoport neve**: A létrehozandó szinkronizálási csoport neve. A Társzinkronizálási szolgáltatáson belül egyedinek kell lennie a névnek, de lehet bármilyen Ön számára logikus név.
- **Előfizetés**: Az előfizetés, amelyben üzembe helyezte a Storage Sync szolgáltatást a [Storage Sync szolgáltatás üzembe helyezése](#deploy-the-storage-sync-service)során.
- **Storage-fiók**: Ha a **Storage-fiók kiválasztása**lehetőséget választja, egy másik panel jelenik meg, ahol kiválaszthatja azt a Storage-fiókot, amelyen a szinkronizálni kívánt Azure-fájlmegosztás található.
- **Azure-fájlmegosztás**: A szinkronizálni kívánt Azure-fájlmegosztás neve.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
A szinkronizálási csoport létrehozásához hajtsa végre a következő PowerShellt. Ne felejtse `<my-sync-group>` el lecserélni a szinkronizálási csoport kívánt nevére.

```powershell
$syncGroupName = "<my-sync-group>"
$syncGroup = New-AzStorageSyncGroup -ParentObject $storageSync -Name $syncGroupName
```

A szinkronizálási csoport sikeres létrehozása után létrehozhatja a Felhőbeli végpontot. Ügyeljen arra, hogy `<my-storage-account>` a `<my-file-share>` és a értékét a várt értékekkel helyettesítse.

```powershell
# Get or create a storage account with desired name
$storageAccountName = "<my-storage-account>"
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroup | Where-Object {
    $_.StorageAccountName -eq $storageAccountName
}

if ($storageAccount -eq $null) {
    $storageAccount = New-AzStorageAccount `
        -Name $storageAccountName `
        -ResourceGroupName $resourceGroup `
        -Location $region `
        -SkuName Standard_LRS `
        -Kind StorageV2 `
        -EnableHttpsTrafficOnly:$true
}

# Get or create an Azure file share within the desired storage account
$fileShareName = "<my-file-share>"
$fileShare = Get-AzStorageShare -Context $storageAccount.Context | Where-Object {
    $_.Name -eq $fileShareName -and $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    $fileShare = New-AzStorageShare -Context $storageAccount.Context -Name $fileShareName
}

# Create the cloud endpoint
New-AzStorageSyncCloudEndpoint `
    -Name $fileShare.Name `
    -ParentObject $syncGroup `
    -StorageAccountResourceId $storageAccount.Id `
    -AzureFileShareName $fileShare.Name
```

---

## <a name="create-a-server-endpoint"></a>Kiszolgálói végpont létrehozása
A kiszolgálói végpont a regisztrált kiszolgálón egy konkrét helyet jelöl, például egy mappát egy kiszolgálói köteten. A kiszolgálói végpontnak egy regisztrált kiszolgálón (nem pedig csatlakoztatott megosztáson) található elérési útnak kell lennie, és a felhőalapú rétegek használatához az elérési útnak nem rendszerköteten kell lennie. A hálózati csatolású tároló (NAS) nem támogatott.

# <a name="portaltabazure-portal"></a>[Portál](#tab/azure-portal)
Kiszolgálói végpont hozzáadásához nyissa meg az újonnan létrehozott szinkronizálási csoportot, majd válassza a **kiszolgáló-végpont hozzáadása**elemet.

![Új kiszolgálói végpontok hozzáadása a szinkronizálási csoport panelen](media/storage-sync-files-deployment-guide/create-sync-group-2.png)

A **Kiszolgálói végpont felvétele** panelen adja meg a következő információkat a kiszolgálói végpont létrehozásához:

- **Regisztrált kiszolgáló**: Annak a kiszolgálónak vagy fürtnek a neve, amelyben létre kívánja hozni a kiszolgálói végpontot.
- **Elérési út**: A szinkronizálási csoport részeként szinkronizálandó Windows Server-elérési út.
- **Felhőbeli rétegek**: A felhőalapú rétegek engedélyezésére vagy letiltására szolgáló kapcsoló. A felhő-előállítók esetében a ritkán használt vagy a hozzáfért fájlok Azure Files.
- **Szabad terület**a köteten: A lefoglalni kívánt szabad terület a kiszolgálói végpontot tartalmazó köteten. Ha például a kötet szabad területe 50%-ra van állítva egy olyan köteten, amely egyetlen kiszolgálói végponttal rendelkezik, az adatmennyiség nagyjából fele lesz a Azure Files. Függetlenül attól, hogy engedélyezve van-e a felhőalapú rétegek használata, az Azure-fájlmegosztás mindig a szinkronizálási csoportban lévő összes adattal rendelkezik.

A kiszolgálói végpont hozzáadásához válassza a **Létrehozás**lehetőséget. A fájlok most már szinkronban vannak az Azure-fájlmegosztás és a Windows Server között. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
A kiszolgálói végpont létrehozásához hajtsa végre a következő PowerShell-parancsokat, és a `<your-server-endpoint-path>` kívánt `<your-volume-free-space>` értékekkel cserélje le a és a értéket.

```powershell
$serverEndpointPath = "<your-server-endpoint-path>"
$cloudTieringDesired = $true
$volumeFreeSpacePercentage = <your-volume-free-space>

if ($cloudTieringDesired) {
    # Ensure endpoint path is not the system volume
    $directoryRoot = [System.IO.Directory]::GetDirectoryRoot($serverEndpointPath)
    $osVolume = "$($env:SystemDrive)\"
    if ($directoryRoot -eq $osVolume) {
        throw [System.Exception]::new("Cloud tiering cannot be enabled on the system volume")
    }

    # Create server endpoint
    New-AzStorageSyncServerEndpoint `
        -Name $registeredServer.FriendlyName `
        -SyncGroup $syncGroup `
        -ServerResourceId $registeredServer.ResourceId `
        -ServerLocalPath $serverEndpointPath `
        -CloudTiering `
        -VolumeFreeSpacePercent $volumeFreeSpacePercentage
} else {
    # Create server endpoint
    New-AzStorageSyncServerEndpoint `
        -Name $registeredServer.FriendlyName `
        -SyncGroup $syncGroup `
        -ServerResourceId $registeredServer.ResourceId `
        -ServerLocalPath $serverEndpointPath 
}
```

---

## <a name="configure-firewall-and-virtual-network-settings"></a>A tűzfal és a virtuális hálózat beállításainak konfigurálása

### <a name="portal"></a>Portál
Ha úgy szeretné konfigurálni az Azure-fájlok szinkronizálását, hogy a tűzfal és a virtuális hálózat beállításait is működjön, tegye a következőket:

1. A Azure Portal navigáljon a védeni kívánt Storage-fiókhoz.
1. Válassza a **tűzfalak és virtuális hálózatok** gombot a bal oldali menüben.
1. Válassza a **kiválasztott hálózatok** lehetőséget **a hozzáférés engedélyezése lehetőségnél**.
1. Győződjön meg arról, hogy a kiszolgálók IP-címe vagy virtuális hálózata szerepel a megfelelő szakaszban.
1. Győződjön meg arról, hogy a **megbízható Microsoft-szolgáltatások hozzáférhetnek ehhez a Storage-fiókhoz** jelölőnégyzet be van jelölve.
1. A beállítások mentéséhez kattintson a **Mentés** gombra.

![A tűzfal és a virtuális hálózat beállításainak konfigurálása az Azure file Sync szolgáltatással való együttműködéshez](media/storage-sync-files-deployment-guide/firewall-and-vnet.png)

## <a name="onboarding-with-azure-file-sync"></a>Előkészítés Azure File Sync
A javasolt lépések a Azure File Sync beléptetése az elsőre nulla állásidővel, miközben a teljes fájl-hűség és a hozzáférés-vezérlési lista (ACL) megőrzése a következő:
 
1. Helyezzen üzembe egy Storage Sync szolgáltatást.
2. Hozzon létre egy szinkronizálási csoportot.
3. Telepítse Azure File Sync-ügynököt a kiszolgálóra a teljes adatkészlettel.
4. Regisztrálja a kiszolgálót, és hozzon létre egy kiszolgálói végpontot a megosztáson. 
5. A szinkronizálás az Azure-fájlmegosztás (Felhőbeli végpont) teljes feltöltését teszi lehetővé.  
6. A kezdeti feltöltés befejezése után telepítse Azure File Sync ügynököt a többi kiszolgálóra.
7. Hozzon létre új fájlmegosztást a többi kiszolgálón.
8. Szükség esetén hozzon létre kiszolgálói végpontokat az új fájlmegosztás és a felhőalapú adatmegosztási szabályzat alapján. (Ehhez a lépéshez további tárterület szükséges a kezdeti beállításhoz.)
9. Lehetővé teszi Azure File Sync ügynöknek a tényleges adatátvitel nélkül a teljes névtér gyors visszaállítását. A névtér teljes szinkronizálása után a szinkronizálási motor kitölti a helyi lemezterületet a kiszolgálói végponthoz tartozó felhőalapú rétegű házirend alapján. 
10. Győződjön meg arról, hogy a szinkronizálás befejeződik, és szükség szerint tesztelje a topológiát. 
11. Átirányíthatja a felhasználókat és az alkalmazásokat az új megosztásra.
12. Lehetősége van a kiszolgálókon lévő duplikált megosztások törlésére is.
 
Ha nem rendelkezik további tárterülettel a bevezetéshez, és szeretné csatolni a meglévő megosztásokhoz, az Azure Files-megosztásokban lévő adatmagot is megadhatja. Ez a megközelítés akkor javasolt, ha és csak akkor, ha az állásidőt el tudja fogadni, és a kezdeti bevezetési folyamat során egyáltalán nem módosítja a kiszolgálói megosztások adatváltozását. 
 
1. A bevezetési folyamat során győződjön meg arról, hogy a kiszolgálók egyikén sem változhatnak az adatmennyiség.
2. Az Azure-fájlmegosztás előállítása a kiszolgálói adatokkal az SMB protokollon keresztül bármely adatátviteli eszközzel, például Robocopy, közvetlen SMB-másolás. Mivel a AzCopy nem tölt fel adatfeltöltést az SMB-en keresztül, ezért nem használható az előzetes előkészítéshez.
3. Hozzon létre Azure File Sync topológiát a meglévő megosztásokra mutató kívánt kiszolgálói végpontokkal.
4. Az összes végponton engedélyezze a szinkronizálás befejezési egyeztetési folyamatát. 
5. Az egyeztetés befejeződése után megnyithatja a megosztásokat a változásokhoz.
 
Jelenleg a kivetés előtti megközelítés néhány korlátozással rendelkezik – 
- A fájlok teljes hűsége nem marad meg. A fájlok például elveszítik az ACL-eket és az időbélyegeket.
- Az adatváltozások a kiszolgálón a szinkronizálási topológia teljes körű és futása előtt ütközéseket okozhatnak a kiszolgálói végpontokon.  
- A Felhőbeli végpont létrehozása után Azure File Sync futtat egy folyamatot a felhőben lévő fájlok észleléséhez a kezdeti szinkronizálás megkezdése előtt. A folyamat befejezéséhez szükséges idő a különböző tényezőktől, például a hálózati sebességtől, a rendelkezésre álló sávszélességtől és a fájlok és mappák számától függ. Az előzetes kiadásban felmerülő durva becslés esetében az észlelési folyamat körülbelül 10 fájlt/s-t futtat.  Ezért még akkor is, ha az előkészítési folyamat gyorsan fut, a teljes körűen futó rendszerek teljes ideje jelentősen meghaladhatja a felhőben tárolt adatmennyiséget.

## <a name="migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync"></a>Elosztott fájlrendszer replikációs szolgáltatása (DFS-R) telepítésének áttelepítése Azure File Syncre
DFS-R központi telepítésének áttelepítése Azure File Syncre:

1. Hozzon létre egy szinkronizálási csoportot, amely a cserélni kívánt DFS-R topológiát jelöli.
2. Indítsa el az áttelepíteni kívánt DFS-R topológiában található összes adattal rendelkező kiszolgálót. Telepítse a Azure File Synct az adott kiszolgálón.
3. Regisztrálja a kiszolgálót, és hozzon létre egy kiszolgálói végpontot az áttelepítendő első kiszolgáló számára. Ne engedélyezze a Felhőbeli rétegek engedélyezését.
4. Lehetővé teszi az összes adatszinkronizálást az Azure-fájlmegosztás (Felhőbeli végpont) számára.
5. Telepítse és regisztrálja a Azure File Sync-ügynököt a többi DFS-R-kiszolgálón.
6. Tiltsa le a DFS-R-t. 
7. Hozzon létre egy kiszolgálói végpontot az egyes DFS-R-kiszolgálókon. Ne engedélyezze a Felhőbeli rétegek engedélyezését.
8. Győződjön meg arról, hogy a szinkronizálás befejeződik, és szükség szerint tesztelje a topológiát.
9. A DFS-R kivonása.
10. A felhő-rétegek mostantól a kívánt kiszolgálói végponton is engedélyezhetők.

További információ: [Azure file Sync együttműködés elosztott fájlrendszer (DFS)](storage-sync-files-planning.md#distributed-file-system-dfs)szolgáltatással.

## <a name="next-steps"></a>További lépések
- [Azure File Sync kiszolgáló-végpont hozzáadása vagy eltávolítása](storage-sync-files-server-endpoint.md)
- [Kiszolgáló regisztrálása vagy törlése Azure File Sync](storage-sync-files-server-registration.md)
- [Az Azure File Sync monitorozása](storage-sync-files-monitoring.md)
