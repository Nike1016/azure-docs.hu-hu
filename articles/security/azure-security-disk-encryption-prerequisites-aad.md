---
title: Az Azure Disk Encryption az Azure AD alkalmazás előfeltételeknek (előző kiadás)
description: Ez a cikk ismerteti a Microsoft Azure Disk Encryption használatával IaaS-beli virtuális gépek előfeltételei.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: da3cb81eaeaa3b1609ffe67b2aea92a69b6bb47a
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68726250"
---
# <a name="azure-disk-encryption-prerequisites-previous-release"></a>Az Azure Disk Encryption előfeltétel (előző kiadás)

**Az Azure Disk Encryption új kiadása így nem egy Azure AD-alkalmazás paramétert, amelyek biztosítják a virtuális gép lemez-titkosítás engedélyezéséhez. Az új kiadással, már nem kell az Azure AD hitelesítő adatok titkosítás engedélyezése lépés során. Valamennyi új virtuális gépeket titkosítani kell az Azure AD alkalmazás paramétereit az új kiadás használata nélkül. Ahhoz, hogy az új kiadással VM lemeztitkosítás utasítások megtekintése: [az Azure Disk Encryption előfeltétel](azure-security-disk-encryption-prerequisites.md). Az Azure AD-alkalmazás paraméterekkel már titkosított virtuális gépek továbbra is támogatottak, és továbbra is fenn kell tartani az AAD-szintaxissal.**

 Ez a cikk az Azure Disk Encryption titkosítási előfeltétel, biztosítani kell, mielőtt használhatná az Azure Disk Encryption igénylő elemeket ismerteti. Általános Előfeltételek, valamint az Azure Disk Encryption szolgáltatással integrált [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/) és a hitelesítés kezeléséhez a titkosítási kulcsokat a key vault az Azure AD-alkalmazást használ. Előfordulhat, hogy is használni kívánt [Azure PowerShell-lel](/powershell/azure/overview) vagy a [Azure CLI-vel](/cli/azure/) beállítását, és állítsa be a Key Vault és az Azure AD-alkalmazás.

A támogatott forgatókönyveket, amelyek a aktorcsoportot tárgyalt számára az Azure IaaS virtuális gépekhez az Azure Disk Encryption engedélyezése előtt a [Azure Disk Encryption áttekintése](azure-security-disk-encryption-overview.md) című cikket, ellenőrizze, hogy rendelkezik az előfeltételek teljesülnek. 

> [!WARNING]
> - Bizonyos ajánlások növelheti az adatok, hálózati vagy számítási erőforrás-használat, ami további licencek vagy előfizetések költségeit. Érvényes aktív Azure-előfizetést hozhat létre erőforrásokat az Azure-ban támogatott régiókban kell rendelkeznie.
> - Ha korábban már használta [Azure Disk Encryption az Azure ad](azure-security-disk-encryption-prerequisites-aad.md) -alkalmazással a virtuális gép titkosításához, akkor továbbra is ezt a beállítást kell használnia a virtuális gép titkosításához. Ezen a titkosított virtuális gépen nem használható [Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md) , mivel ez nem támogatott forgatókönyv, ami azt jelenti, hogy a titkosított virtuális gép esetében a HRE alkalmazásból való átállás még nem támogatott. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

### <a name="windows"></a>Windows

- Windows-ügyfél: Windows 8 és újabb verziók.
- Windows Server: Windows Server 2008 R2 és újabb verziók.  
 
> [!NOTE] 
> A Windows Server 2008 R2 megköveteli, hogy a .NET-keretrendszer 4,5 legyen telepítve a titkosításhoz; telepítse azt Windows Update a Windows Server 2008 R2 x64-alapú rendszerek ([KB2901983](https://www.catalog.update.microsoft.com/Search.aspx?q=KB2901983)) választható frissítés Microsoft .net keretrendszere 4.5.2-es verziójával.  
>  
> A Windows Server 2012 R2 Core és a Windows Server 2016 Core megköveteli, hogy a bdehdcfg összetevőt a virtuális gépre telepítse a titkosításhoz.

### <a name="linux"></a>Linux 

A Azure Disk Encryption az [Azure által támogatott Linux-disztribúciók](../virtual-machines/linux/endorsed-distros.md)egy részhalmazán támogatott, amely az összes Linux-kiszolgáló lehetséges disztribúciójának részhalmaza.

![A Azure Disk Encryptiont támogató Linux Server-disztribúciók Venn ábrája](./media/azure-security-disk-encryption-faq/ade-supported-distros.png)

Az Azure által nem támogatott Linux Server-disztribúciók nem támogatják Azure Disk Encryptiont, és a támogatottak közül csak a következő disztribúciók és verziók támogatják a Azure Disk Encryption:

| Linux-disztribúció | Verzió | A kötettípus titkosítás támogatott|
| --- | --- |--- |
| Ubuntu | 18,04| Operációsrendszer- és lemez |
| Ubuntu | 16.04| Operációsrendszer- és lemez |
| Ubuntu | 14.04.5</br>[Az Azure-ban beállított kernel 4,15-es vagy újabb verzióra frissült](azure-security-disk-encryption-tsg.md#bkmk_Ubuntu14) | Operációsrendszer- és lemez |
| RHEL | 7,6 | Operációs rendszer és az adatlemez (lásd az alábbi megjegyzést) |
| RHEL | 7.5 | Operációs rendszer és az adatlemez (lásd az alábbi megjegyzést) |
| RHEL | 7.4 | Operációs rendszer és az adatlemez (lásd az alábbi megjegyzést) |
| RHEL | 7.3 | Operációs rendszer és az adatlemez (lásd az alábbi megjegyzést) |
| RHEL | 7.2 | Operációs rendszer és az adatlemez (lásd az alábbi megjegyzést) |
| RHEL | 6.8 | Adatlemez (lásd az alábbi megjegyzést) |
| RHEL | 6.7 | Adatlemez (lásd az alábbi megjegyzést) |
| CentOS | 7,6 | Operációsrendszer- és lemez |
| CentOS | 7.5 | Operációsrendszer- és lemez |
| CentOS | 7.4 | Operációsrendszer- és lemez |
| CentOS | 7.3 | Operációsrendszer- és lemez |
| CentOS | 7.2n | Operációsrendszer- és lemez |
| CentOS | 6.8 | Adatlemez |
| openSUSE | 42,3 | Adatlemez |
| SLES | 12-SP4 | Adatlemez |
| SLES | 12-SP3 | Adatlemez |

> [!NOTE]
> Az új ADE-implementáció a RHEL operációs rendszer és az adatlemez RHEL7-elszámolású lemezképekhez való használata esetén támogatott. Az ADE jelenleg nem támogatott a RHEL saját előfizetéses (BYOS) lemezképek esetében. További információ: [Azure Disk Encryption for Linux](azure-security-disk-encryption-linux.md) .

- Az Azure Disk Encryption megköveteli, hogy a kulcstartó és a virtuális gépek található az ugyanazon Azure-régióban és az előfizetés. Az erőforrások konfigurálását külön régiókban okoz a az Azure Disk Encryption engedélyezésével.

#### <a name="additional-prerequisites-for-linux-iaas-vms"></a>A Linux IaaS virtuális gépekre vonatkozó további előfeltételek 

- Azure Disk Encryption megköveteli, hogy a dm-crypt és a VFAT modulok jelen legyenek a rendszeren. A VFAT eltávolítása vagy letiltása az alapértelmezett rendszerképből megakadályozza, hogy a rendszer beolvassa a kulcs kötetét, és beszerezze a lemezek zárolásának feloldásához szükséges kulcsot a későbbi újraindítások során. A VFAT modul rendszerből való eltávolítására szolgáló rendszer-megerősítési lépések nem kompatibilisek a Azure Disk Encryptionokkal. 
- Titkosítás engedélyezése előtt titkosítani az adatlemezek kell /etc/fstab megfelelően szerepel. Ez a bejegyzés állandó blokk eszköz nevét a "/ dev/sdX" formátumban nevek nem támaszkodnak társítani kell ugyanazon a lemezen újraindítások, különösen akkor, ha a rendszer titkosítást alkalmaz eszközként használja. A viselkedésről további részleteket a következő témakörben talál: [A Linux rendszerű virtuális gép eszköz nevének módosításainak megoldása](../virtual-machines/linux/troubleshoot-device-names-problems.md)
- Ellenőrizze, hogy a /etc/fstab beállításokat a csatlakoztatáshoz szükséges megfelelően van beállítva. Ezeket a beállításokat, a mount - parancs futtatása vagy a virtuális gépet, és aktiválja a újracsatlakoztatást ezzel a módszerrel. Miután ez befejeződött, ellenőrizze, ellenőrizze, hogy a meghajtó is csatlakoztatva van a lsblk parancs kimenete. 
  - Ha a /etc/fstab fájl megfelelően titkosítás engedélyezése előtt nem csatlakoztatja a meghajtót, az Azure Disk Encryption nem lehet megfelelően csatlakoztatásához.
  - Az Azure Disk Encryption folyamat kerül át a csatlakoztatási adatokat /etc/fstab és a saját konfigurációs fájlba a titkosítási folyamat részeként. Nem lehet terekbe megtekintéséhez a data encryption meghajtó után a /etc/fstab hiányzó bejegyzés befejeződött.
  - A titkosítás megkezdése előtt mindenképpen állítsa le az összes olyan szolgáltatást és folyamatot, amely a csatlakoztatott adatlemezekre írhat, és tiltsa le őket, hogy újraindítás után ne induljon el automatikusan. Ezek megtarthatják a fájlok megnyitását ezeken a partíciókon, így megakadályozva a titkosítási eljárás újracsatlakoztatását, ami hibát okoz a titkosításban. 
  - Az újraindítást követően, az Azure Disk Encryption folyamat csatlakoztathatja az újonnan titkosított lemezek időt vesz igénybe. Ezek nem érhető el azonnal a számítógép újraindítása után. A folyamat elindításához, zárolásának feloldásához, és csatlakoztassa a titkosított meghajtók legyenek elérhetők más folyamatokkal való hozzáférés előtt időt kell. Ez a folyamat a rendszer jellegétől függően a rendszer újraindítása után több mint egy percet is igénybe vehet.

Parancsok, amelyek segítségével csatlakoztathatja az adatlemezeket és a szükséges/etc/fstab-bejegyzéseket létrehozni egy példát találhat [244-248 a parancsfájl vonalak](https://github.com/ejarvi/ade-cli-getting-started/blob/master/validate.sh#L244-L248). 

## <a name="bkmk_GPO"></a> Hálózat és a csoportházirend

**Ahhoz, hogy az Azure Disk Encryption funkciót, a régebbi AAD használatával paraméter szintaxisa az IaaS virtuális gépek hálózati végpont a következő konfigurációs követelményeknek kell megfelelnie:** 
  - Szeretne csatlakozni a key vault jogkivonat beszerzéséhez, IaaS virtuális Gépen kell lennie csatlakozni egy Azure Active Directory végpontján \[login.microsoftonline.com\].
  - A titkosítási kulcsok a key vault ír, az IaaS virtuális gép tud csatlakozni a key vault-végpontot kell lennie.
  - Az IaaS virtuális gépek csatlakozni egy Azure storage-végpont, amelyen az Azure-bővítményt adattárat és a egy Azure storage-fiókot, amelyen a VHD-fájlok képesnek kell lennie.
  -  Ha a biztonsági házirend korlátozza az Internet-hozzáférést az Azure virtuális gépekről, oldja meg az előző URI-t, és konfigurálja egy adott szabályt, amely engedélyezi a kimenő kapcsolat az IP-címekről. További információkért lásd: [Azure Key Vault tűzfal mögötti](../key-vault/key-vault-access-behind-firewall.md).
  - A Windows Ha a TLS 1.0 explicit módon le van tiltva, és a .NET-verziója nem lett frissítve a 4.6-os vagy újabb, a következő beállításjegyzék-módosítás lehetővé teszi az újabb TLS-verzió kiválasztásához ADE:
    
        [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
        "SystemDefaultTlsVersions"=dword:00000001
        "SchUseStrongCrypto"=dword:00000001

        [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
        "SystemDefaultTlsVersions"=dword:00000001
        "SchUseStrongCrypto"=dword:00000001` 
     

 


**Csoportházirend:**
 - Az Azure Disk Encryption megoldás a BitLocker külső kulcsvédő Windows IaaS virtuális gépekhez használja. Tartományhoz csatlakozó virtuális gépek esetén nem leküldéses bármely csoportházirendek, amelyeket a TPM-védőt. "A BitLocker engedélyezése a kompatibilis TPM nélküli" a csoportházirenddel kapcsolatos információkért lásd: [a BitLocker csoportházirend-hivatkozás](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1).

-  A tartományhoz csatlakoztatott, egyéni csoportházirendtel rendelkező virtuális gépekre vonatkozó BitLocker-házirendnek a következő beállítást kell tartalmaznia: [Konfigurálja a BitLocker helyreállítási adatok felhasználói tárolóját – > engedélyezze a 256 bites helyreállítási kulcsot](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). A Azure Disk Encryption sikertelen lesz, ha a BitLocker egyéni csoportházirend-beállításai nem kompatibilisek. Gépeken, amelyek nem rendelkeznek a megfelelő házirend-beállítást, az új szabályzat alkalmazásának frissítése (a gpupdate.exe/Force) az új szabályzat kényszerítése és indítsa újra lehet szükség.  


## <a name="bkmk_PSH"></a> Azure PowerShell-lel
[Az Azure PowerShell](/powershell/azure/overview) használó parancsmagok készletét nyújtja a [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) modell az Azure-erőforrások kezeléséhez. Használhatja a böngészőjében az [Azure Cloud Shell](../cloud-shell/overview.md), vagy használhatja bármely PowerShell-munkamenetben az alábbi utasítások segítségével a helyi gépen telepítheti. Ha már helyileg telepítette, győződjön meg arról, hogy a Azure PowerShell legújabb verzióját használja a Azure Disk Encryption konfigurálásához.

### <a name="install-azure-powershell-for-use-on-your-local-machine-optional"></a>Azure PowerShell telepítése (nem kötelező) a helyi gépen használatra:  
1. [Azure PowerShell telepítése és konfigurálása](/powershell/azure/install-az-ps). 

2. Telepítse a [Azure Active Directory PowerShell-modul](/powershell/azure/active-directory/install-adv2#installing-the-azure-ad-module). 

     ```powershell
     Install-Module AzureAD
     ```

3. A modulok telepített verzióinak ellenőrzése.
      ```powershell
      Get-Module Az -ListAvailable | Select-Object -Property Name,Version,Path
      Get-Module AzureAD -ListAvailable | Select-Object -Property Name,Version,Path
      ```
4. Jelentkezzen be az Azure-ba a [Kapcsolódás-AzAccount](/powershell/module/az.accounts/connect-azaccount) parancsmag használatával.
     
     ```powershell
     Connect-AzAccount
     # For specific instances of Azure, use the -Environment parameter.
     Connect-AzAccount –Environment (Get-AzEnvironment –Name AzureUSGovernment)
    
     <# If you have multiple subscriptions and want to specify a specific one, 
     get your subscription list with Get-AzSubscription and 
     specify it with Set-AzContext.  #>
     Get-AzSubscription
     Set-AzContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"
     ```

5.  Az Azure AD Connect [Connect-AzureAD](/powershell/module/azuread/connect-azuread).
     
     ```powershell
     Connect-AzureAD
     ```

6. Felülvizsgálat [Ismerkedés az Azure PowerShell-lel](/powershell/azure/get-started-azureps) és [AzureAD](/powershell/module/azuread), ha szükséges.

## <a name="bkmk_CLI"></a> Az Azure CLI

A [Azure CLI 2.0](/cli/azure) Azure-erőforrások kezelésére szolgáló parancssori eszköz. A parancssori felület célja rugalmasan kérdezhet le adatokat, nem blokkoló hosszú ideig futó műveleteket támogatja, és ellenőrizze, hogy megkönnyítse a parancsprogramok használatát. Használhatja a böngészőjében az [Azure Cloud Shell-lel](../cloud-shell/overview.md), vagy telepítheti a helyi gépen, és használhatja bármely PowerShell-munkamenetben.

1. [Azure CLI telepítése](/cli/azure/install-azure-cli) használatra (nem kötelező) a helyi gépen:

2. A telepített verzió ellenőrzéséhez.
     
     ```azurecli-interactive
     az --version
     ``` 

3. Jelentkezzen be Azure-bA [az bejelentkezési](/cli/azure/authenticate-azure-cli).
     
     ```azurecli
     az login
     
     # If you would like to select a tenant, use: 
     az login --tenant "<tenant>"

     # If you have multiple subscriptions, get your subscription list with az account list and specify with az account set.
     az account list
     az account set --subscription "<subscription name or ID>"
     ```

4. Felülvizsgálat [Azure CLI 2.0 használatának első lépései](/cli/azure/get-started-with-azure-cli) szükség esetén. 


## <a name="prerequisite-workflow-for-key-vault-and-the-azure-ad-app"></a>A Key Vault és az Azure AD-alkalmazás előfeltétel munkafolyamat

Ha már ismeri az Azure Disk Encryption Key Vault és az Azure AD szükséges előfeltételeket, használhatja a [az Azure Disk Encryption előfeltétel PowerShell-parancsprogram](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ). További információ az előfeltételek parancsfájl használatával kapcsolatban: [Azure Disk Encryption függelék](azure-security-disk-encryption-appendix.md#bkmk_prereq-script). 

1. Kulcstartó létrehozása. 
2. Állítsa be az Azure AD-alkalmazást és egy szolgáltatásnevet.
3. Az Azure AD-alkalmazás számára a kulcstartó hozzáférési házirendjének beállítása.
4. Set a key vault speciális hozzáférési szabályzatok.
 
## <a name="bkmk_KeyVault"></a> Kulcstartó létrehozása 
Az Azure Disk Encryption integrálva van [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) segítségével vezérelheti és felügyelheti a lemeztitkosítási kulcsokat és titkos kulcsokat a key vault-előfizetés. Hozzon létre egy kulcstartót, vagy használjon egy meglévőt az Azure Disk Encryption. Kulcstartók kapcsolatos további információkért lásd: [első lépései az Azure Key Vault](../key-vault/key-vault-get-started.md) és [kulcstartó védelme](../key-vault/key-vault-secure-your-key-vault.md). Resource Manager-sablonnal, az Azure PowerShell vagy az Azure CLI használatával hozzon létre egy kulcstartót. 


>[!WARNING]
>Annak érdekében, hogy a titkosítási titkos kulcsok nem adatbázisközi regionális határokat, az Azure Disk Encryption a Key Vault és a virtuális gépek közös elhelyezése ugyanabban a régióban van szüksége. Létrehozhat és használhat egy Key Vaultot, amely ugyanabban a régióban, mint a virtuális gép titkosítását. 


### <a name="bkmk_KVPSH"></a> Key vault létrehozása a PowerShell-lel

A [New-AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault) parancsmag használatával Azure PowerShell kulcstartót hozhat létre. További parancsmagok a Key Vaulthoz: [az.](/powershell/module/az.keyvault/)kulcstartó. 

1. Ha szükséges, [csatlakozhat az Azure-előfizetés](azure-security-disk-encryption-appendix.md#bkmk_ConnectPSH). 
2. Szükség esetén hozzon létre egy új erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup).  Az adatközpont helyeinek listázásához használja a [Get-AzLocation](/powershell/module/az.resources/get-azlocation). 
     
     ```azurepowershell-interactive
     # Get-AzLocation 
     New-AzResourceGroup –Name 'MyKeyVaultResourceGroup' –Location 'East US'
     ```

3. Új kulcstartó létrehozása a [New-AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault) használatával
    
      ```azurepowershell-interactive
     New-AzKeyVault -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -Location 'East US'
     ```

4. Megjegyzés: a **tároló neve**, **erőforráscsoport-nevet**, **erőforrás-azonosító**, **tároló URI-ja**, és a **objektumazonosító** amely visszaadja a későbbi használatra a lemezek titkosításakor. 


### <a name="bkmk_KVCLI"></a> Kulcstartó létrehozása az Azure CLI-vel
A key vaulttal és az Azure CLI használatával kezelheti a [az keyvault](/cli/azure/keyvault#commands) parancsokat. Hozzon létre egy kulcstartót, használja a [az keyvault létrehozása](/cli/azure/keyvault#az-keyvault-create).

1. Ha szükséges, [csatlakozhat az Azure-előfizetés](azure-security-disk-encryption-appendix.md#bkmk_ConnectCLI).
2. Hozzon létre egy új erőforráscsoportot, ha szükséges, a [az csoport létrehozása](/cli/azure/group#az-group-create). Helyek listázása, használja [az fiók list-locations](/cli/azure/account#az-account-list) 
     
     ```azurecli-interactive
     # To list locations: az account list-locations --output table
     az group create -n "MyKeyVaultResourceGroup" -l "East US"
     ```

3. Hozzon létre egy új kulcstartót [az keyvault létrehozása](/cli/azure/keyvault#az-keyvault-create).
    
     ```azurecli-interactive
     az keyvault create --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --location "East US"
     ```

4. Megjegyzés: a **a tároló neve** (név), **erőforráscsoport-név**, **erőforrás-azonosító** (azonosító) **Vault URI**, és a **Objektumazonosító** , amely visszaadja a használati később. 

### <a name="bkmk_KVRM"></a> Key vault létrehozása a Resource Manager-sablonnal

Key vault használatával is létrehozhat a [Resource Manager-sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

1. Kattintson az Azure gyorsindítási sablon **üzembe helyezés az Azure**.
2. Válassza ki az előfizetés, erőforráscsoport, erőforráscsoport helyét, a Key Vault neve, objektumazonosító:, jogi feltételek és szerződés, és kattintson a **beszerzési**. 


## <a name="bkmk_ADapp"></a> Állítsa be az Azure AD-alkalmazás és szolgáltatás egyszerű 
Engedélyezni kell az Azure-ban futó virtuális gépek titkosítási van szüksége, amikor az Azure Disk Encryption állít elő, és ír a titkosítási kulcsok a key vaultban. Az Azure AD-hitelesítés kezelése a titkosítási kulcsok a key vaultban lévő igényel. Hozzon létre egy Azure AD-alkalmazást erre a célra. Hitelesítési célra használhatja vagy titkos kulcs-alapú ügyfélhitelesítés vagy [az Azure AD ügyféltanúsítvány-alapú ügyfél-hitelesítés](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md).


### <a name="bkmk_ADappPSH"></a> Állítsa be az Azure AD-alkalmazás és szolgáltatás egyszerű, az Azure PowerShell használatával 
Hajtsa végre a következő parancsokat, lekérése és használata a [Azure AD PowerShell-modul](/powershell/azure/active-directory/install-adv2). 

1. Ha szükséges, [csatlakozhat az Azure-előfizetés](azure-security-disk-encryption-appendix.md#bkmk_ConnectPSH).
2. Azure AD-alkalmazás létrehozásához használja a [New-AzADApplication PowerShell-](/powershell/module/az.resources/new-azadapplication) parancsmagot. MyApplicationHomePage és a MyApplicationUri lehet a kívánt értékeket.

     ```azurepowershell
     $aadClientSecret = "My AAD client secret"
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force
     $azureAdApplication = New-AzADApplication -DisplayName "My Application Display Name" -HomePage "https://MyApplicationHomePage" -IdentifierUris "https://MyApplicationUri" -Password $aadClientSecretSec
     $servicePrincipal = New-AzADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId
     ```

3. A $azureAdApplication.ApplicationId az Azure AD ClientID, a $aadClientSecret pedig a titkos ügyfélkulcsot használó, később az Azure Disk Encryption engedélyezése. Megfelelő védelme érdekében az Azure AD ügyfél titkos kulcsát. Futó `$azureAdApplication.ApplicationId` jelennek meg, az ApplicationID.


### <a name="bkmk_ADappCLI"></a> Állítsa be az Azure AD-alkalmazás és szolgáltatás egyszerű, az Azure CLI-vel

A szolgáltatásnevek Azure CLI használatával kezelheti a [az ad sp](/cli/azure/ad/sp) parancsokat. További információ: Azure- [szolgáltatásnév létrehozása](/cli/azure/create-an-azure-service-principal-azure-cli).

1. Ha szükséges, [csatlakozhat az Azure-előfizetés](azure-security-disk-encryption-appendix.md#bkmk_ConnectCLI).
2. Hozzon létre egy új egyszerű szolgáltatást.
     
     ```azurecli-interactive
     az ad sp create-for-rbac --name "ServicePrincipalName" --password "My-AAD-client-secret" --skip-assignment 
     ```
3.  Az appId, az adott vissza az Azure AD ClientID használatban van más parancsok. Emellett akkor is az egyszerű Szolgáltatásnevet fogja használni az keyvault set-házirendet. A jelszó, a titkos ügyfélkulcsot, amely az Azure Disk Encryption engedélyezése később használjon. Megfelelő védelme érdekében az Azure AD ügyfél titkos kulcsát.
 
### <a name="bkmk_ADappRM"></a> Állítsa be az Azure AD-alkalmazás és szolgáltatás egyszerű, ha az Azure Portalon
Lépéssorral a [egy Azure Active Directory-alkalmazás és -erőforrások eléréséhez szolgáltatásnév létrehozása a portálon](../active-directory/develop/howto-create-service-principal-portal.md) hozhat létre az Azure AD-alkalmazást. Az alábbiakban az egyes lépések léphet közvetlenül a következő cikkszakaszt befejezéséhez. 

1. [Szükséges engedélyek ellenőrzése](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)
2. [Az Azure Active Directory-alkalmazás létrehozása](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) 
     - Használhat bármilyen nevet és bejelentkezés URL-CÍMÉT szeretné az alkalmazás létrehozásakor.
3. [Az Alkalmazásazonosító és hitelesítési kulcs](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in). 
     - A hitelesítési kulcs az ügyfél titka, és a set-AzVMDiskEncryptionExtension AadClientSecret használatos. 
        - A hitelesítési kulcs szolgál hitelesítő adatokat, az alkalmazás által jelentkezzen be Azure ad-ben. Az Azure Portalon a titkos kulcsok nevezzük, de nem rendelkezik kapcsolattal a kulcstartókhoz. A titkos kód megfelelően biztonságos. 
     - Az alkalmazás AZONOSÍTÓját később a set-AzVMDiskEncryptionExtension AadClientId fogja használni a set-AzKeyVaultAccessPolicy ServicePrincipalName. 

## <a name="bkmk_KVAP"></a> A kulcstartó hozzáférési szabályzatot az Azure AD-alkalmazás beállítása
Titkosítási titkos kulcsok egy megadott Key Vault ír, az Azure Disk Encryption kell rendelkeznie az ügyfél-Azonosítót és az ügyfél titkos kulcsát az Azure Active Directory-alkalmazás titkos kulcsok a Key Vault írási engedélyekkel rendelkező. 

> [!NOTE]
> Azure Disk Encryption megköveteli az alábbi hozzáférési házirendek konfigurálását az Azure AD-ügyfélalkalmazás számára: _WrapKey_ és engedélyek _beállítása_ .

### <a name="bkmk_KVAPPSH"></a> A kulcstartó hozzáférési szabályzat az Azure AD-alkalmazás az Azure PowerShell használatával
Az Azure AD-alkalmazást a és a tárolóban lévő titkos hozzáférési jogokkal kell. A [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) parancsmag használatával engedélyeket adhat az alkalmazásnak az ügyfél-azonosítóval (amely az alkalmazás regisztrálásakor jött létre), a _– ServicePrincipalName_ paraméter értékeként. További tudnivalókért tekintse meg a következő blogbejegyzésben: [Azure Key Vault - lépésről lépésre](https://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx). 

1. Ha szükséges, [csatlakozhat az Azure-előfizetés](azure-security-disk-encryption-appendix.md#bkmk_ConnectPSH).
2. Állítsa be a kulcstartó hozzáférési szabályzatot az AD-alkalmazás, a PowerShell használatával.

     ```azurepowershell
     $keyVaultName = 'MySecureVault'
     $aadClientID = 'MyAadAppClientID'
     $KVRGname = 'MyKeyVaultResourceGroup'
     Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname
     ```

### <a name="bkmk_KVAPCLI"></a> A kulcstartó hozzáférési szabályzat az Azure AD-alkalmazás az Azure CLI-vel
Használat [az keyvault set-policy](/cli/azure/keyvault#az-keyvault-set-policy) hozzáférési házirend beállítása. További információkért lásd: [kezelése a Key Vault parancssori felület 2.0-val](../key-vault/key-vault-manage-with-cli2.md#authorizing-an-application-to-use-a-key-or-secret).

1. Ha szükséges, [csatlakozhat az Azure-előfizetés](azure-security-disk-encryption-appendix.md#bkmk_ConnectCLI).
2. Adja meg az egyszerű szolgáltatás titkos kódok és Sortörés az Azure CLI-vel elérésével létrehozott kulcsokat a következő paranccsal:
 
     ```azurecli-interactive
     az keyvault set-policy --name "MySecureVault" --spn "<spn created with CLI/the Azure AD ClientID>" --key-permissions wrapKey --secret-permissions set
     ```

### <a name="bkmk_KVAPRM"></a> A kulcstartó hozzáférési szabályzat az Azure AD-alkalmazás a portállal

1. Nyissa meg az erőforráscsoport a key vaultban.
2. Válassza ki a key vaultban, ugorjon a **hozzáférési szabályzatok**, majd kattintson a **új hozzáadása**.
3. A **válassza egyszerű**, keresse meg a létrehozott Azure AD-alkalmazást, és válassza ki azt. 
4. A **Kulcsengedélyek**, ellenőrizze **Wrap Key** alatt **titkosítási műveletek**.
5. A **titkos kód engedélyei**, ellenőrizze **beállítása** alatt **titkos műveletek**.
6. Kattintson a **OK** a hozzáférési szabályzat mentéséhez. 

![Az Azure Key Vault titkosítási-műveletek - Wrap Key](./media/azure-security-disk-encryption/keyvault-portal-fig3.png)

![Az Azure Key Vault titkos kulcsából engedélyek – beállítása](./media/azure-security-disk-encryption/keyvault-portal-fig3b.png)

## <a name="bkmk_KVper"></a> Speciális hozzáférési szabályzatok beállítása a key vault
Az Azure platform a titkosítási kulcsok vagy titkos kódok, hogy elérhetők legyenek a rendszerindítást, és visszafejti a köteteket a virtuális géphez a key vaultban lévő hozzá kell férnie. A key vault vagy a központi telepítések lemez-titkosítás engedélyezése sikertelen lesz.  

### <a name="bkmk_KVperPSH"></a> Speciális hozzáférési szabályzatok az Azure PowerShell használatával a key vault beállítása
 A Key Vault PowerShell-parancsmagjának [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) használatával engedélyezheti a lemez titkosítását.

  - **Key Vault engedélyezése a lemezes titkosításhoz:** Az Azure Disk Encryption EnabledForDiskEncryption szükséges.
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDiskEncryption
     ```

  - **Key Vault telepítésének engedélyezése, ha szükséges:** Engedélyezi a Microsoft. számítási erőforrás-szolgáltató számára, hogy a kulcstartóból beolvassa a titkos kulcsokat, amikor ez a kulcstartó az erőforrás-létrehozásban hivatkozik, például virtuális gép létrehozásakor.

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDeployment
     ```

  - **Szükség esetén Key Vault engedélyezése a sablonok telepítéséhez:** Lehetővé teszi a Azure Resource Manager számára, hogy a kulcstartóból beolvassa a titkos kulcsokat, ha a kulcstároló a sablon központi telepítésében van hivatkozva.

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForTemplateDeployment
     ```

### <a name="bkmk_KVperCLI"></a> Set a key vault speciális hozzáférési szabályzatok az Azure CLI használatával
Használat [az keyvault update](/cli/azure/keyvault#az-keyvault-update) lemez titkosítása a key vault engedélyezése. 

 - **Key Vault engedélyezése a lemezes titkosításhoz:** Engedélyezve van a-Disk-Encryption szükséges. 

     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **Key Vault telepítésének engedélyezése, ha szükséges:** Annak engedélyezése, Virtual Machines a titkos kulcsként tárolt tanúsítványok beolvasása a tárból.
     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **Szükség esetén Key Vault engedélyezése a sablonok telepítéséhez:** Annak engedélyezése, hogy a Resource Manager beolvassa a titkos kulcsokat a tárból.
     ```azurecli-interactive  
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-template-deployment "true"
     ```


### <a name="bkmk_KVperrm"></a> Speciális hozzáférési szabályzatok az Azure Portalon a key vault beállítása

1. Válassza ki a keyvault, ugorjon a **hozzáférési szabályzatok**, és **kattintson ide a speciális hozzáférési szabályzatok megjelenítéséhez**.
2. Jelölje be az **engedélyezze a hozzáférést az Azure Disk Encryption kötettitkosítást**.
3. Válassza ki **engedélyezze a hozzáférést az Azure Virtual Machines üzembe helyezési** és/vagy **hozzáférés engedélyezése az Azure Resource Manager-sablon üzembe helyezéshez**, ha szükséges. 
4. Kattintson a **Save** (Mentés) gombra.

![Az Azure key vault speciális hozzáférési szabályzatok](./media/azure-security-disk-encryption/keyvault-portal-fig4.png)


## <a name="bkmk_KEK"></a> Állítsa be a kulcsalapú titkosítás kulcsa (nem kötelező)
Egy további titkosítási kulcsok biztonsági szintet szeretne kulcstitkosítási kulcs-(KEK) használatára, ha egy KEK hozzáadása a key vaultban. Az [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) parancsmaggal hozzon létre egy kulcs titkosítási kulcsot a Key vaultban. A helyszíni kulcskezelő HSM is importálhat egy KEK. További információkért lásd: [Key Vault-dokumentáció](../key-vault/key-vault-hsm-protected-keys.md). Amikor egy kulcsalapú titkosítás kulcsa van megadva, az Azure Disk Encryption a kulcs segítségével burkolhatja a titkosítási titkos kulcsait a Key Vault írása előtt. 

* Kulcsok létrehozásakor használjon RSA-kulcs típusát. Azure Disk Encryption még nem támogatja az elliptikus görbe kulcsait.

* A key vault titkos kulcsából, és KEK URL-címek verziószámmal kell lennie. Az Azure ezt a korlátozást, versioning, érvénybe lépteti. Érvényes titkos kulcsot, és KEK URL-címeket tekintse meg az alábbi példák:

  * Példa: egy érvényes titkos URL-címe:   *https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Példa: egy érvényes KEK URL-cím:   *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* Az Azure Disk Encryption részeként a key vault titkos kódok és KEK URL-címek megadásához használt portszámok nem támogatja. Nem támogatott, és támogatott a key vault URL-címek példákért lásd az alábbi példákat:

  * Érvénytelen a key vault URL-címe  *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Elfogadható a key vault URL-címe:   *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="bkmk_KEKPSH"></a> Állítsa be a kulcsalapú titkosítás kulcsa az Azure PowerShell használatával 
A PowerShell-parancsfájl használatával, mielőtt azokat a lépéseket mutatják a parancsfájl az Azure Disk Encryption Előfeltételek tisztában kell lennie. A példaszkript környezetében előfordulhat, hogy szükség módosításokra. Ez a szkript létrehoz az összes az Azure Disk Encryption előfeltétel, és meglévő IaaS virtuális gép, a lemez titkosítási kulcs alkalmazásburkoló kulcstitkosítási kulcs használatával titkosítja. 

 ```powershell
 # Step 1: Create a new resource group and key vault in the same location.
     # Fill in 'MyLocation', 'MyKeyVaultResourceGroup', and 'MySecureVault' with your values.
     # Use Get-AzLocation to get available locations and use the DisplayName.
     # To use an existing resource group, comment out the line for New-AzResourceGroup
     
     $Loc = 'MyLocation';
     $KVRGname = 'MyKeyVaultResourceGroup';
     $KeyVaultName = 'MySecureVault'; 
     New-AzResourceGroup –Name  $KVRGname –Location $Loc;
     New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc;
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName  $KVRGname;
     $KeyVaultResourceId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName  $KVRGname).ResourceId;
     $diskEncryptionKeyVaultUrl = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName  $KVRGname).VaultUri;
     
 # Step 2: Create the AD application and service principal.
     # Fill in 'MyAADClientSecret', "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
     # MyApplicationHomePage and the MyApplicationUri can be any values you wish.
     
     $aadClientSecret =  'MyAADClientSecret';
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force;
     $azureAdApplication = New-AzADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -Password $aadClientSecretSec
     $servicePrincipal = New-AzADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId;
     $aadClientID = $azureAdApplication.ApplicationId;
     
 #Step 3: Enable the vault for disk encryption and set the access policy for the Azure AD application.
     
     Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption;
     Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName  $KVRGname;
     
 #Step 4: Create a new key in the key vault with the Add-AzKeyVaultKey cmdlet.
     # Fill in 'MyKeyEncryptionKey' with your value.
     
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName -Destination 'Software';
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     
 #Step 5: Encrypt the disks of an existing IaaS VM
     # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values. 
     
     $VMName = 'MySecureVM';
      $VMRGName = 'MyVirtualMachineResourceGroup';
     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;
```

## <a name="bkmk_Cert"></a> Tanúsítványalapú hitelesítés (nem kötelező)
Ha Tanúsítványalapú hitelesítés használatára, töltsön fel a kulcstartóba, és telepítheti az ügyfelet. A PowerShell-parancsfájl használatával, mielőtt azokat a lépéseket mutatják a parancsfájl az Azure Disk Encryption Előfeltételek tisztában kell lennie. A példaszkript környezetében előfordulhat, hogy szükség módosításokra.

     
 ```powershell

 # Fill in "MyKeyVaultResourceGroup", "MySecureVault", and 'MyLocation' ('My location' only if needed)

   $KVRGname = 'MyKeyVaultResourceGroup'
   $KeyVaultName= 'MySecureVault'

   # Create a key vault and set enabledForDiskEncryption property on it. 
   # Comment out the next three lines if you already have an existing key vault enabled for encryption. No need to set 'My location' in this case.

   $Loc = 'MyLocation'
   New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption

   #Setting some variables with the key vault information 
   $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname
   $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
   $KeyVaultResourceId = $KeyVault.ResourceId

   # Create the Azure AD application and associate the certificate with it. 
   # Fill in "C:\certificates\mycert.pfx", "Password", "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
   # MyApplicationHomePage and the MyApplicationUri can be any values you wish

   $CertPath = "C:\certificates\mycert.pfx"
   $CertPassword = "Password"
   $Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
   $CertValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

   $AzureAdApplication = New-AzADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -CertValue $CertValue 
   $ServicePrincipal = New-AzADServicePrincipal -ApplicationId $AzureAdApplication.ApplicationId

   $AADClientID = $AzureAdApplication.ApplicationId
   $aadClientCertThumbprint= $cert.Thumbprint

   Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname
   
   # Upload the pfx file to the key vault. 
   # Fill in "MyAADCert".  

   $KeyVaultSecretName = "MyAADCert"
   $FileContentBytes = get-content $CertPath -Encoding Byte
   $FileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
           $JSONObject = @"
           { 
               "data" : "$filecontentencoded", 
               "dataType" : "pfx", 
               "password" : "$CertPassword" 
           } 
"@

   $JSONObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
   $JSONEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

   #Set the secret and set the key vault policy for -EnabledForDeployment

   $Secret = ConvertTo-SecureString -String $JSONEncoded -AsPlainText -Force
   Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName -SecretValue $Secret
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDeployment

   # Deploy the certificate to the VM
   # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values.

   $VMName = 'MySecureVM'
   $VMRGName = 'MyVirtualMachineResourceGroup'
   $CertUrl = (Get-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName).Id
   $SourceVaultId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGName).ResourceId
   $VM = Get-AzVM -ResourceGroupName $VMRGName -Name $VMName 
   $VM = Add-AzVMSecret -VM $VM -SourceVaultId $SourceVaultId -CertificateStore "My" -CertificateUrl $CertUrl
   Update-AzVM -VM $VM -ResourceGroupName $VMRGName 

   #Enable encryption on the VM using Azure AD client ID and the client certificate thumbprint

   Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId
 ```

## <a name="bkmk_CertKEK"></a> Tanúsítványalapú hitelesítés és a egy kek-KEL (nem kötelező)

Ha szeretné a Tanúsítványalapú hitelesítés használatára, és a egy KEK-titkosítási kulcs becsomagolása, használhatja az alábbi parancsfájlok példaként. A PowerShell-parancsfájl használatával, mielőtt az összes korábbi Azure Disk Encryption előfeltétel azokat a lépéseket mutatják a szkriptben tisztában kell lennie. A példaszkript környezetében előfordulhat, hogy szükség módosításokra.

> [!IMPORTANT]
> Az Azure AD-alapú hitelesítés jelenleg nem támogatott Linuxos virtuális gépeken.



     
 ```powershell
# Fill in 'MyKeyVaultResourceGroup', 'MySecureVault', and 'MyLocation' (if needed)

   $KVRGname = 'MyKeyVaultResourceGroup'
   $KeyVaultName= 'MySecureVault'

   # Create a key vault and set enabledForDiskEncryption property on it. 
   # Comment out the next three lines if you already have an existing key vault enabled for encryption.

   $Loc = 'MyLocation'
   New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption

   # Create the Azure AD application and associate the certificate with it.  
   # Fill in "C:\certificates\mycert.pfx", "Password", "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
   # MyApplicationHomePage and the MyApplicationUri can be any values you wish

   $CertPath = "C:\certificates\mycert.pfx"
   $CertPassword = "Password"
   $Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
   $CertValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

   $AzureAdApplication = New-AzADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -CertValue $CertValue 
   $ServicePrincipal = New-AzADServicePrincipal -ApplicationId $AzureAdApplication.ApplicationId

   $AADClientID = $AzureAdApplication.ApplicationId
   $aadClientCertThumbprint= $cert.Thumbprint

   ## Give access for setting secrets and wraping keys
   Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname

   # Upload the pfx file to the key vault. 
   # Fill in "MyAADCert". 

   $KeyVaultSecretName = "MyAADCert"
   $FileContentBytes = get-content $CertPath -Encoding Byte
   $FileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
           $JSONObject = @"
           { 
               "data" : "$filecontentencoded", 
               "dataType" : "pfx", 
               "password" : "$CertPassword" 
           } 
"@

   $JSONObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
   $JSONEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

   #Set the secret and set the key vault policy for deployment

   $Secret = ConvertTo-SecureString -String $JSONEncoded -AsPlainText -Force
   Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName -SecretValue $Secret
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDeployment

   #Setting some variables with the key vault information and generating a KEK 
   # FIll in 'KEKName'
   
   $KEKName ='KEKName'
   $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname
   $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
   $KeyVaultResourceId = $KeyVault.ResourceId
   $KEK = Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $KEKName -Destination "Software"
   $KeyEncryptionKeyUrl = $KEK.Key.kid



   # Deploy the certificate to the VM
   # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values.

   $VMName = 'MySecureVM';
   $VMRGName = 'MyVirtualMachineResourceGroup';
   $CertUrl = (Get-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName).Id
   $SourceVaultId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGName).ResourceId
   $VM = Get-AzVM -ResourceGroupName $VMRGName -Name $VMName 
   $VM = Add-AzVMSecret -VM $VM -SourceVaultId $SourceVaultId -CertificateStore "My" -CertificateUrl $CertUrl
   Update-AzVM -VM $VM -ResourceGroupName $VMRGName

   #Enable encryption on the VM using Azure AD client ID and the client certificate thumbprint

   Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId
```

 
## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [A Windows Azure Disk Encryption engedélyezése](azure-security-disk-encryption-windows-aad.md)

> [!div class="nextstepaction"]
> [Linuxhoz készült Azure Disk Encryption engedélyezése](azure-security-disk-encryption-linux-aad.md)
