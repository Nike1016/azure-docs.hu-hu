---
title: Hozzáférés az Azure AD Graph API-hoz egy Windows VM rendszer által hozzárendelt felügyelt identitásával
description: Az oktatóanyag azt ismerteti, hogyan lehet hozzáférni az Azure AD Graph API-hoz egy Windows rendszerű virtuális gép rendszer által hozzárendelt felügyelt identitásával.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/20/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60938f26c27b9f94046b1be8e3d0cb6b247017c9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60307794"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-ad-graph-api"></a>Oktatóanyag: Hozzáférés az Azure AD Graph API-hoz egy Windows VM rendszer által hozzárendelt felügyelt identitásával

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice.md)]

Az oktatóanyag bemutatja, hogyan érheti el az Azure AD Graph API beolvasni a csoporttagságok alapértelmezett felügyelt identitás Windows virtuális gép (VM) használatával. Az Azure-erőforrások felügyelt identitásainak kezelését automatikusan az Azure végzi, és lehetővé teszi a hitelesítést az Azure AD-hitelesítést támogató szolgáltatásokban anélkül, hogy be kellene szúrnia a hitelesítő adatokat a kódba.  Ebben az oktatóanyagban a virtuális gép identitásának Azure AD-csoportbeli tagságát kérdezi le. A csoportadatokra gyakran szükség van, például engedélyezési döntések során. A virtuális gép felügyelt identitása a háttérben **szolgáltatásnévként** jelenik meg az Azure AD-ben. Mielőtt elvégzi a csoportlekérdezést, adja hozzá a virtuális gép identitását jelölő szolgáltatásnevet egy Azure AD-beli csoporthoz. Ezt megteheti az Azure PowerShell, az Azure AD PowerShell vagy az Azure CLI segítségével is.

> [!div class="checklist"]
> * Csatlakozás az Azure AD szolgáltatáshoz
> * Virtuális gép identitásának hozzáadása egy csoporthoz az Azure AD-ben 
> * Hozzáférés biztosítása az Azure AD Graph-hoz a virtuális gép identitása számára 
> * Hozzáférési jogkivonat lekérése a VM identitásával, majd az Azure AD Graph meghívása a használatával

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

- Annak érdekében, hogy hozzáférést tudjon biztosítani az Azure AD Graph-hoz a virtuális gép identitása számára, a fiókjához hozzá kell rendelnie a **globális rendszergazda** szerepkört az Azure AD-ben.
- Telepítse a legújabb [az Azure AD PowerShell](/powershell/azure/active-directory/install-adv2) Ha még nem tette. 

## <a name="connect-to-azure-ad"></a>Csatlakozás az Azure AD szolgáltatáshoz

Ahhoz, hogy hozzá tudja rendelni a virtuális gépet egy csoporthoz, illetve engedélyezni tudja a csoporttagságok lekérését a virtuális gép számára, csatlakoznia kell az Azure AD-hez. Connect-AzureAD parancsmagot használhatja közvetlenül, vagy a TenantId paraméter az abban az esetben, ha több bérlő.

```powershell
Connect-AzureAD
```
VAGY
```powershell
Connect-AzureAD -TenantId "Object Id of the tenant"
```

## <a name="add-your-vm-identity-to-a-group-in-azure-ad"></a>Virtuális gép identitásának hozzáadása egy csoporthoz az Azure AD-ben

Amikor engedélyezte a rendszer által hozzárendelt felügyelt identitást a Windows rendszerű virtuális gépen, az létrehozott egy szolgáltatásnevet az Azure AD-ben.  Most adja hozzá a virtuális gépet egy csoporthoz.  Az alábbi példa létrehoz egy új csoportot Azure AD-ben, és hozzáadja a virtuális gép szolgáltatásnevét a csoporthoz:

```powershell
New-AzureADGroup -DisplayName "myGroup" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
$AzureADGroup = Get-AzureADGroup -Filter "displayName eq 'myGroup'"
$ManagedIdentitiesServicePrincipal = Get-AzureADServicePrincipal -Filter "displayName eq 'myVM'"
Add-AzureADGroupMember -ObjectId $AzureADGroup.ObjectID -RefObjectId $ManagedIdentitiesServicePrincipal.ObjectId
```
## <a name="grant-your-vm-access-to-the-azure-ad-graph-api"></a>Hozzáférés biztosítása az Azure AD Graph API-hoz a virtuális gép számára

Az Azure-erőforrások felügyelt identitásainak segítségével a kód hozzáférési jogkivonatokat kérhet le az olyan erőforrások felé történő hitelesítéshez, amelyek támogatják az Azure AD-hitelesítést. A Microsoft Azure AD Graph API támogatja az Azure AD-hitelesítést. Ebben a lépésben hozzáférést biztosít az Azure AD Graph-hoz a virtuális gép identitásának szolgáltatásneve számára, hogy az le tudja kérdezni a csoporttagságokat. A szolgáltatásnevek az **alkalmazásengedélyeken** keresztül kapnak hozzáférést a Microsoft vagy az Azure AD Graph-hoz. A biztosítandó alkalmazásengedély típusa attól az entitástól függ, amelyet el szeretne érni a Microsoft vagy az Azure AD Graph-ban.

Ebben az oktatóanyagban a ```Directory.Read.All``` alkalmazásengedély használatával teszi lehetővé a virtuális gép identitása számára a csoporttagságok lekérdezését. Az engedély megadásához szüksége lesz egy olyan felhasználói fiókra, amely globális rendszergazdai szerepkörrel rendelkezik az Azure AD-ben. Az alkalmazásengedély megadásához általában az Azure Portalon kell megkeresi az alkalmazás regisztrációját, és ott hozzáadni az engedélyt. Az Azure-erőforrások felügyelt identitásai azonban nem alkalmazásobjektumokat regisztrálnak az Azure AD-ben, hanem csak szolgáltatásneveket. Az alkalmazásengedély regisztrálása az Azure AD PowerShell parancssori eszközével történik. 

Azure AD Graph:
- Egyszerű szolgáltatás alkalmazásazonosítója (használja az alkalmazásengedély megadása): 00000002-0000-0000-c000-000000000000
- Erőforrás-azonosító (hozzáférési jogkivonat az Azure-erőforrások felügyelt identitásaiból történő lekérésekor): https://graph.windows.net
- Engedély hatókör-hivatkozást: [Az Azure AD Graph-engedélyek referencia](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)

### <a name="grant-application-permissions-using-azure-ad-powershell"></a>Alkalmazásengedélyek biztosítása az Azure AD PowerShell használatával

A lehetőség használatához szüksége lesz Azure AD PowerShellre. Ha nincs telepítve, a folytatás előtt [töltse le a legújabb verziót](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2).

1. Nyisson meg egy PowerShell-ablakot, és csatlakozzon az Azure AD-hez:

   ```powershell
   Connect-AzureAD
   ```
   Egy adott Azure Active Directory csatlakozni, használja a _TenantId_ paramétert a következők szerint:

   ```powershell
   Connect-AzureAD -TenantId "Object Id of the tenant"
   ```

   
2. Futtassa az alábbi PowerShell-parancsokat, hogy hozzárendelje a ``Directory.Read.All`` alkalmazásengedélyt ahhoz a szolgáltatásnévhez, amely a virtuális gép identitását jelöli.

   ```powershell
   $ManagedIdentitiesServicePrincipal = Get-AzureADServicePrincipal -Filter "displayName eq 'myVM'"
   $GraphAppId = "00000002-0000-0000-c000-000000000000"
   $GraphServicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$GraphAppId'"
   $PermissionName = "Directory.Read.All"
   $AppRole = $GraphServicePrincipal.AppRoles | Where-Object {$_.Value -eq $PermissionName -and $_.AllowedMemberTypes -contains "Application"}
   New-AzureAdServiceAppRoleAssignment -ObjectId $ManagedIdentitiesServicePrincipal.ObjectId -PrincipalId $ManagedIdentitiesServicePrincipal.ObjectId -ResourceId $GraphServicePrincipal.ObjectId -Id $AppRole.Id
   ``` 

   Az utolsó parancs kimenetének, amely visszaadja a hozzárendelés azonosítóját, a következőképpen kell kinéznie:
        
   `ObjectId`:`gzR5KyLAiUOTiqFhNeWZWBtK7ZKqNJxAiWYXYVHlgMs`

   `ResourceDisplayName`:`Windows Azure Active Directory`

   `PrincipalDisplayName`:`myVM` 

   Ha a `New-AzureAdServiceAppRoleAssignment` hívása `bad request, one or more properties are invalid` hibával meghiúsul, akkor lehetséges, hogy az alkalmazásengedély már hozzá lett rendelve a virtuális gép identitásának szolgáltatásnevéhez. Az alábbi PowerShell-parancsokkal ellenőrizheti, hogy létezik-e már az alkalmazásengedély a virtuális gép identitása és az Azure AD Graph között:

   ```powershell
   $ManagedIdentitiesServicePrincipal = Get-AzureADServicePrincipal -Filter "displayName eq '<VM-NAME>'"
   $GraphAppId = "00000002-0000-0000-c000-000000000000"
   $GraphServicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$GraphAppId'"
   $PermissionName = "Directory.Read.All"
   $AppRole = $GraphServicePrincipal.AppRoles | Where-Object {$_.Value -eq $PermissionName -and $_.AllowedMemberTypes -contains "Application"}
   Get-AzureADServiceAppRoleAssignment -ObjectId $GraphServicePrincipal.ObjectId | Where-Object {$_.Id -eq $AppRole.Id -and $_.PrincipalId -eq $ManagedIdentitiesServicePrincipal.ObjectId}
   ```

   Az alábbi PowerShell-parancsokkal listázhatja azokat az alkalmazásengedélyeket, amelyek biztosítva lettek az Azure AD Graph számára:

   ```powershell
   $GraphAppId = "00000002-0000-0000-c000-000000000000"
   $GraphServicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$GraphAppId'"
   Get-AzureADServiceAppRoleAssignment -ObjectId $GraphServicePrincipal.ObjectId
   ``` 

   Az alábbi PowerShell-parancsokkal eltávolíthatja azokat az alkalmazásengedélyeket, amelyek biztosítva lettek az Azure AD Graph-ban a virtuális gép identitása számára:

   ```powershell
   $ManagedIdentitiesServicePrincipal = Get-AzureADServicePrincipal -Filter "displayName eq '<VM-NAME>'"
   $GraphAppId = "00000002-0000-0000-c000-000000000000"
   $GraphServicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$GraphAppId'"
   $PermissionName = "Directory.Read.All"
   $AppRole = $GraphServicePrincipal.AppRoles | Where-Object {$_.Value -eq $PermissionName -and $_.AllowedMemberTypes -contains "Application"}   
   $ServiceAppRoleAssignment = Get-AzureADServiceAppRoleAssignment -ObjectId $GraphServicePrincipal.ObjectId | Where-Object {$_.Id -eq $AppRole.Id -and $_.PrincipalId -eq $ManagedIdentitiesServicePrincipal.ObjectId}
   Remove-AzureADServiceAppRoleAssignment -AppRoleAssignmentId $ServiceAppRoleAssignment.ObjectId -ObjectId $ManagedIdentitiesServicePrincipal.ObjectId
   ```
 
## <a name="get-an-access-token-using-the-vms-identity-to-call-azure-ad-graph"></a>Hozzáférési jogkivonat beszerzése a virtuális gép identitásával az Azure AD Graph meghívásához 

Ha a virtuális gép rendszer által hozzárendelt felügyelt identitását az Azure AD Graph-ban való hitelesítéshez szeretné használni, a kéréseket a virtuális gépről kell indítania.

1. A portálon lépjen a **Virtuális gépek** lapra, lépjen a Windows VM-hez, és az **Áttekintés** panelen kattintson a **Csatlakozás** elemre.  
2. Írja be a Windows rendszerű virtuális gép létrehozásakor használt **felhasználónevet** és **jelszót**.
3. Most, hogy létrehozott egy távoli asztali kapcsolatot a virtuális géppel, nyissa meg a PowerShellt a távoli munkamenetben.  
4. A PowerShell Invoke-WebRequest parancsával küldjön kérést a helyi Azure-erőforrások felügyeltidentitási végpontjára, hogy lekérjen egy hozzáférési jogkivonatot az Azure AD Graph-hoz.

   ```powershell
   $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://graph.windows.net' -Method GET -Headers @{Metadata="true"}
   ```

   Konvertálja a választ JSON-objektumból PowerShell-objektummá.

   ```powershell
   $content = $response.Content | ConvertFrom-Json
   ```

   Nyerje ki a hozzáférési jogkivonatot a válaszból.

   ```powershell
   $AccessToken = $content.access_token
   ```

5. A virtuális gép identitása szolgáltatásnevéhez tartozó objektumazonosítót használva (ezt az értéket lekérheti az előző lépésekben deklarált változóból: ``$ManagedIdentitiesServicePrincipal.ObjectId``) kérdezze le az Azure AD Graph API-t a csoporttagságok lekéréséhez. Cserélje le `<OBJECT ID>` az előző lépésben objektumazonosítójú és <`ACCESS-TOKEN>` a korábban kapott hozzáférési jogkivonattal:

   ```powershell
   Invoke-WebRequest 'https://graph.windows.net/<Tenant ID>/servicePrincipals/<VM Object ID>/getMemberGroups?api-version=1.6' -Method POST -Body '{"securityEnabledOnly":"false"}' -Headers @{Authorization="Bearer $AccessToken"} -ContentType "application/json"
   ```
   
   A válasz tartalmazza a csoportnak azt az `Object ID` elemét, amelyet hozzáadott a virtuális gép szolgáltatásnevéhez az előző lépésekben.

   Válasz:

   ```powershell   
   Content : {"odata.metadata":"https://graph.windows.net/<Tenant ID>/$metadata#Collection(Edm.String)","value":["<ObjectID of VM's group membership>"]}
   ```

## <a name="next-steps"></a>További lépések

Az oktatóanyag bemutatta, hogyan érhető el Azure AD Graph a windowsos virtuális gép (VM) rendszer által hozzárendelt felügyelt identitásával.  További információ az AD Graph szolgáltatásról:

>[!div class="nextstepaction"]
>[Azure AD Graph](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api)
