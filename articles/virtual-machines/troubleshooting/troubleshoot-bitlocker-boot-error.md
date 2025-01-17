---
title: BitLocker rendszerindítási hibák elhárítása Azure-beli virtuális gépen | Microsoft Docs
description: Ismerje meg, hogyan lehet elhárítani a BitLocker rendszerindítási hibáit egy Azure-beli virtuális gépen
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: v-jesits
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/25/2019
ms.author: genli
ms.openlocfilehash: 27a675982711f8d8f0b36ea0cc2600de45e97a6e
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68348455"
---
# <a name="bitlocker-boot-errors-on-an-azure-vm"></a>BitLocker rendszerindítási hibák egy Azure-beli virtuális gépen

 Ez a cikk azokat a BitLocker-hibákat ismerteti, amelyek akkor jelentkezhetnek, ha a Windows rendszerű virtuális gépet (VM) elindítja Microsoft Azureban.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="symptom"></a>Jelenség

 Egy Windows rendszerű virtuális gép nem indul el. Amikor bejelöli a képernyőképeket a rendszerindítási [diagnosztika](../windows/boot-diagnostics.md) ablakban, a következő hibaüzenetek egyike jelenik meg:

- Csatlakoztassa a BitLocker-kulcsot tartalmazó USB-illesztőprogramot

- Ki van zárva! Adja meg a helyreállítási kulcsot a kezdéshez (billentyűzetkiosztás: NEKÜNK) a helytelen bejelentkezési adatok túl sokszor lettek megadva, ezért a számítógép zárolva van az adatvédelem védelme érdekében. A helyreállítási kulcs lekéréséhez ugorjon https://windows.microsoft.com/recoverykeyfaq egy másik számítógép vagy mobil eszközről. Ha szüksége van rá, a kulcs azonosítója XXXXXXX. Vagy alaphelyzetbe állíthatja a számítógépet.

- Adja meg a meghajtó zárolásának feloldásához szükséges jelszót [] a beíráskor nyomja le az INSERT billentyűt a jelszó megjelenítéséhez.
- Adja meg a helyreállítási kulcs betöltését egy USB-eszközről.

## <a name="cause"></a>Ok

Ez a probléma akkor fordulhat elő, ha a virtuális gép nem találta meg a BitLocker helyreállítási kulcs (BEK) fájlját a titkosított lemez visszafejtéséhez.

## <a name="solution"></a>Megoldás

A probléma megoldásához állítsa le és szabadítsa fel a virtuális gépet, majd indítsa újra. Ez a művelet arra kényszeríti a virtuális gépet, hogy lekérje a BEK-fájlt a Azure Key Vaultból, majd helyezze a titkosított lemezre. 

Ha ez a módszer nem oldja meg a problémát, kövesse az alábbi lépéseket a BEK-fájl manuális visszaállításához:

1. Készítsen pillanatképet az érintett virtuális gép rendszerlemezéről biztonsági másolatként. További információkért lásd: [lemez pillanatképének elkészítése](../windows/snapshot-copy-managed-disk.md).
2. [A rendszer lemez csatolása egy helyreállítási virtuális Géphez](troubleshoot-recovery-disks-portal-windows.md). A 7. lépésben a [manage-bde](https://docs.microsoft.com/windows-server/administration/windows-commands/manage-bde) parancs futtatásához a **BitLocker meghajtótitkosítás** funkciót engedélyeznie kell a helyreállítási virtuális gépen.

    Felügyelt lemez csatlakoztatásakor előfordulhat, hogy a "titkosítási beállításokat tartalmaz, ezért nem használható adatlemezként" hibaüzenet jelenik meg. Ebben az esetben futtassa a következő parancsfájlt a lemez csatlakoztatásához:

    ```Powershell
    $rgName = "myResourceGroup"
    $osDiskName = "ProblemOsDisk"

    New-AzDiskUpdateConfig -EncryptionSettingsEnabled $false |Update-AzDisk -diskName $osDiskName -ResourceGroupName $rgName

    $recoveryVMName = "myRecoveryVM" 
    $recoveryVMRG = "RecoveryVMRG" 
    $OSDisk = Get-AzDisk -ResourceGroupName $rgName -DiskName $osDiskName;

    $vm = get-AzVM -ResourceGroupName $recoveryVMRG -Name $recoveryVMName 

    Add-AzVMDataDisk -VM $vm -Name $osDiskName -ManagedDiskId $osDisk.Id -Caching None -Lun 3 -CreateOption Attach 

    Update-AzVM -VM $vm -ResourceGroupName $recoveryVMRG
    ```
     Nem csatolhat felügyelt lemezt egy blob-lemezképből visszaállított virtuális géphez.

3. Ha a lemez csatlakoztatva van, hozzon egy távoli asztali kapcsolattal a helyreállítási virtuális gépet, hogy néhány Azure PowerShell szkriptet futtasson. Győződjön meg arról, hogy a helyreállítási virtuális gépen a [Azure PowerShell legújabb verziója](https://docs.microsoft.com/powershell/azure/overview) van telepítve.

4. Nyisson meg egy emelt szintű Azure PowerShell-munkamenetet (Futtatás rendszergazdaként). A következő parancsok futtatásával jelentkezzen be az Azure-előfizetésbe:

    ```Powershell
    Add-AzAccount -SubscriptionID [SubscriptionID]
    ```

5. Futtassa a következő szkriptet a BEK-fájl nevének a megadásához:

    ```powershell
    $vmName = "myVM"
    $vault = "myKeyVault"
    Get-AzKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq $vmName) -and ($_.ContentType -match 'BEK')} `
            | Sort-Object -Property Created `
            | ft  Created, `
                @{Label="Content Type";Expression={$_.ContentType}}, `
                @{Label ="Volume"; Expression = {$_.Tags.VolumeLetter}}, `
                @{Label ="DiskEncryptionKeyFileName"; Expression = {$_.Tags.DiskEncryptionKeyFileName}}
    ```

    Az alábbiakban a kimenet mintája látható. Keresse meg a csatolt lemez BEK-fájljának nevét. Ebben az esetben feltételezzük, hogy a csatlakoztatott lemez meghajtóbetűjele F, a BEK-fájl pedig EF7B2F5A-50C6-4637-9F13-7F599C12F85C. BEK.

    ```
    Created             Content Type Volume DiskEncryptionKeyFileName               
    -------             ------------ ------ -------------------------               
    4/5/2018 7:14:59 PM Wrapped BEK  C:\    B4B3E070-836C-4AF5-AC5B-66F6FDE6A971.BEK
    4/7/2018 7:21:16 PM Wrapped BEK  F:\    EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    4/7/2018 7:26:23 PM Wrapped BEK  G:\    70148178-6FAE-41EC-A05B-3431E6252539.BEK
    4/7/2018 7:26:26 PM Wrapped BEK  H:\    5745719F-4886-4940-9B51-C98AFABE5305.BEK
    ```

    Ha két duplikált kötet jelenik meg, akkor az újabb időbélyeggel rendelkező kötet a jelenlegi BEK-fájl, amelyet a helyreállítási virtuális gép használ.

    Ha a **tartalomtípus** értéke becsomagolt **BEK**, lépjen a [Key encryption Key (KEK) forgatókönyvekhez](#key-encryption-key-scenario).

    Most, hogy már rendelkezik a meghajtóhoz tartozó BEK-fájl nevével, létre kell hoznia a titkos fájl nevét. BEK-fájl a meghajtó zárolásának feloldásához.

6.  Töltse le a BEK-fájlt a helyreállítási lemezre. A következő minta menti a BEK-fájlt a C:\BEK mappába. Győződjön meg arról, `C:\BEK\` hogy az elérési út létezik a parancsfájlok futtatása előtt.

    ```powershell
    $vault = "myKeyVault"
    $bek = " EF7B2F5A-50C6-4637-9F13-7F599C12F85C"
    $keyVaultSecret = Get-AzKeyVaultSecret -VaultName $vault -Name $bek
    $bekSecretBase64 = $keyVaultSecret.SecretValueText
    $bekFileBytes = [Convert]::FromBase64String($bekSecretbase64)
    $path = "C:\BEK\DiskEncryptionKeyFileName.BEK"
    [System.IO.File]::WriteAllBytes($path,$bekFileBytes)
    ```

7.  A csatolt lemez a BEK-fájllal való feloldásához futtassa a következő parancsot.

    ```powershell
    manage-bde -unlock F: -RecoveryKey "C:\BEK\EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    ```
    Ebben a példában a csatlakoztatott operációsrendszer-lemez a meghajtó F. Ellenőrizze, hogy a megfelelő meghajtóbetűjelet használja-e. 

    - Ha a lemez sikeresen fel lett oldva a BEK kulcs használatával. fontolóra vesszük a BitLocker-probléma megoldását. 

    - Ha a BEK-kulcs használata nem oldja fel a lemez zárolását, a védelem felfüggesztésével átmenetileg kikapcsolhatja a BitLockert a következő parancs futtatásával.
    
        ```powershell
        manage-bde -protectors -disable F: -rc 0
        ```      
    - Ha a virtuális gépet a dytem-lemez használatával szeretné újraépíteni, akkor teljes mértékben vissza kell fejtenie a meghajtót. Ehhez futtassa a következő parancsot:

        ```powershell
        manage-bde -off F:
        ```
8.  Válassza le a lemezt a helyreállítási virtuális gépről, majd csatlakoztassa újra a lemezt az érintett virtuális géphez rendszerlemezként. További információ: Windows rendszerű [virtuális gép hibáinak elhárítása az operációsrendszer-lemez egy helyreállítási virtuális géphez való csatolásával](troubleshoot-recovery-disks-windows.md).

### <a name="key-encryption-key-scenario"></a>Kulcs titkosítási kulcsának forgatókönyve

A kulcs titkosítási kulcsainak forgatókönyvéhez kövesse az alábbi lépéseket:

1. Győződjön meg arról, hogy a bejelentkezett felhasználói fiók "nincs becsomagolt" engedélyre van szüksége a felhasználó Key Vault hozzáférési házirendjében **| Kulcs engedélyei | Titkosítási műveletek | Kulcs**kicsomagolása.
2. Mentse a következő parancsfájlokat a-be. PS1-fájl:

    ```powershell
    #Set the Parameters for the script
    param (
            [Parameter(Mandatory=$true)]
            [string] 
            $keyVaultName,
            [Parameter(Mandatory=$true)]
            [string] 
            $kekName,
            [Parameter(Mandatory=$true)]
            [string]
            $secretName,
            [Parameter(Mandatory=$true)]
            [string]
            $bekFilePath,
            [Parameter(Mandatory=$true)]
            [string] 
            $adTenant
            )
    # Load ADAL Assemblies. The following script assumes that the Azure PowerShell version you installed is 1.0.0. 
    $adal = "${env:ProgramFiles}\WindowsPowerShell\Modules\Az.Accounts\1.0.0\PreloadAssemblies\Microsoft.IdentityModel.Clients.ActiveDirectory.dll"
    $adalforms = "${env:ProgramFiles}\WindowsPowerShell\Modules\Az.Accounts\1.0.0\PreloadAssemblies\Microsoft.IdentityModel.Clients.ActiveDirectory.Platform.dll"
    [System.Reflection.Assembly]::LoadFrom($adal)
    [System.Reflection.Assembly]::LoadFrom($adalforms)

    # Set well-known client ID for AzurePowerShell
    $clientId = "1950a258-227b-4e31-a9cf-717495945fc2" 
    # Set redirect URI for Azure PowerShell
    $redirectUri = "urn:ietf:wg:oauth:2.0:oob"
    # Set Resource URI to Azure Service Management API
    $resourceAppIdURI = "https://vault.azure.net"
    # Set Authority to Azure AD Tenant
    $authority = "https://login.windows.net/$adtenant"
    # Create Authentication Context tied to Azure AD Tenant
    $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
    # Acquire token
    $authResult = $authContext.AcquireTokenAsync($resourceAppIdURI, $clientId, $redirectUri, $platformParameters).result
    # Generate auth header 
    $authHeader = $authResult.CreateAuthorizationHeader()
    # Set HTTP request headers to include Authorization header
    $headers = @{'x-ms-version'='2014-08-01';"Authorization" = $authHeader}

    ########################################################################################################################
    # 1. Retrieve wrapped BEK
    # 2. Make KeyVault REST API call to unwrap the BEK
    # 3. Convert the Base64Url string returned by KeyVault unwrap to Base64 string 
    # 4. Convert Base64 string to bytes and write to the BEK file
    ########################################################################################################################

    #Get wrapped BEK and place it in JSON object to send to KeyVault REST API
    $keyVaultSecret = Get-AzureKeyVaultSecret -VaultName $keyVaultName -Name $secretName
    $wrappedBekSecretBase64 = $keyVaultSecret.SecretValueText
    $jsonObject = @"
    {
    "alg": "RSA-OAEP",
    "value" : "$wrappedBekSecretBase64"
    }
    "@

    #Get KEK Url
    $kekUrl = (Get-AzureKeyVaultKey -VaultName $keyVaultName -Name $kekName).Key.Kid;
    $unwrapKeyRequestUrl = $kekUrl+ "/unwrapkey?api-version=2015-06-01";

    #Call KeyVault REST API to Unwrap 
    $result = Invoke-RestMethod -Method POST -Uri $unwrapKeyRequestUrl -Headers $headers -Body $jsonObject -ContentType "application/json" -Debug

    #Convert Base64Url string returned by KeyVault unwrap to Base64 string
    $base64UrlBek = $result.value;
    $base64Bek = $base64UrlBek.Replace('-', '+');
    $base64Bek = $base64Bek.Replace('_', '/');
    if($base64Bek.Length %4 -eq 2)
    {
        $base64Bek+= '==';
    }
    elseif($base64Bek.Length %4 -eq 3)
    {
        $base64Bek+= '=';
    }

    #Convert base64 string to bytes and write to BEK file
    $bekFileBytes = [System.Convert]::FromBase64String($base64Bek);
    [System.IO.File]::WriteAllBytes($bekFilePath,$bekFileBytes)
    ```
3. Állítsa be a paramétereket. A szkript feldolgozza a KEK titkát a BEK-kulcs létrehozásához, majd a helyreállítási virtuális gép helyi mappájába menti azt.

4. A szkript elkezdése után a következő kimenet jelenik meg:

        GAC    Version        Location                                                                              
        ---    -------        --------                                                                              
        False  v4.0.30319     C:\Program Files\WindowsPowerShell\Modules\Az.Accounts\...
        False  v4.0.30319     C:\Program Files\WindowsPowerShell\Modules\Az.Accounts\...

    A szkript befejezésekor a következő kimenet jelenik meg:

        VERBOSE: POST https://myvault.vault.azure.net/keys/rondomkey/<KEY-ID>/unwrapkey?api-
        version=2015-06-01 with -1-byte payload
        VERBOSE: received 360-byte response of content type application/json; charset=utf-8


5. A csatolt lemez a BEK-fájllal való feloldásához futtassa a következő parancsot:

    ```powershell
    manage-bde -unlock F: -RecoveryKey "C:\BEK\EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    ```
    Ebben a példában a csatlakoztatott operációsrendszer-lemez a meghajtó F. Ellenőrizze, hogy a megfelelő meghajtóbetűjelet használja-e. 

    - Ha a lemez sikeresen fel lett oldva a BEK kulcs használatával. fontolóra vesszük a BitLocker-probléma megoldását. 

    - Ha a BEK-kulcs használata nem oldja fel a lemez zárolását, a védelem felfüggesztésével átmenetileg kikapcsolhatja a BitLockert a következő parancs futtatásával.
    
        ```powershell
        manage-bde -protectors -disable F: -rc 0
        ```      
    - Ha a virtuális gépet a dytem-lemez használatával szeretné újraépíteni, akkor teljes mértékben vissza kell fejtenie a meghajtót. Ehhez futtassa a következő parancsot:

        ```powershell
        manage-bde -off F:
        ```

6. Válassza le a lemezt a helyreállítási virtuális gépről, majd csatlakoztassa újra a lemezt az érintett virtuális géphez rendszerlemezként. További információ: Windows rendszerű [virtuális gép hibáinak elhárítása az operációsrendszer-lemez egy helyreállítási virtuális géphez való csatolásával](troubleshoot-recovery-disks-windows.md).
