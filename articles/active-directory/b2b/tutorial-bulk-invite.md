---
title: Oktatóanyag meghívása a B2B-együttműködés felhasználók – az Azure Active Directory tömeges |} A Microsoft Docs
description: Ebben az oktatóanyagban megismerheti, hogyan küldhet az Azure AD B2B együttműködés külső felhasználói számára tömeges meghívókat a PowerShell és egy CSV-fájl használatával.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: tutorial
ms.date: 08/14/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: d3bd02afa1fe1aaba6602201f839468a58673c29
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68278003"
---
# <a name="tutorial-bulk-invite-azure-ad-b2b-collaboration-users"></a>Oktatóanyag: Tömeges meghívása az Azure AD B2B együttműködés felhasználók

Ha Azure Active Directory (Azure AD) B2B együttműködéssel dolgozik együtt külső partnerekkel, egyszerre több vendégfelhasználót meghívhat a szervezetébe. Ebben az oktatóanyagban megismerheti, hogyan küldhet külső felhasználók számára tömeges meghívókat a PowerShell használatával. A következőket fogja elvégezni:

> [!div class="checklist"]
> * Felhasználói adatokat tartalmazó, vesszővel tagolt adatfájl (.csv-fájl) előkészítése
> * Meghívók küldése egy PowerShell-szkripttel
> * A felhasználók címtárhoz való hozzáadásának ellenőrzése

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt. 

## <a name="prerequisites"></a>Előfeltételek

### <a name="install-the-latest-azureadpreview-module"></a>A legújabb AzureADPreview modul telepítése
Győződjön meg róla, hogy az Azure AD PowerShell for Graph module (AzureADPreview) legújabb verzióját telepíti. 

Először ellenőrizze, melyik modulokat telepítette. Nyissa meg a Windows PowerShellt rendszergazdaként (Futtatás rendszergazdaként), majd futtassa a következő parancsot:
 
```powershell  
Get-Module -ListAvailable AzureAD*
```

A kimenet alapján tegye a következők egyikét:

- Ha nem kapott eredményt, a következő parancs futtatásával telepítse az AzureADPreview modult:
  
   ```powershell  
   Install-Module AzureADPreview
   ```
- Ha csak az AzureAD modul látható az eredmények között, a következő parancsok futtatásával telepítse az AzureADPreview modult: 

   ```powershell 
   Uninstall-Module AzureAD 
   Install-Module AzureADPreview 
   ```
- Ha csak az AzureADPreview modul látható az eredmények között, azonban kap egy üzenetet, amely szerint elérhető egy újabb verzió, a következő parancsok futtatásával frissítse a modult: 

   ```powershell 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
  ```

Előfordulhat, hogy egy megjelenő üzenet tájékoztatja Önt, hogy a modult nem megbízható tárházból telepíti. Ez akkor fordul elő, ha korábban még nem állította be megbízható tárháznak a PSGallery tárházat. Nyomja meg az **Y-t** a modul telepítéséhez.

### <a name="get-test-email-accounts"></a>E-mail-tesztfiókok létrehozása

Szüksége lesz kettő vagy több e-mail-tesztfiókra, ahova a meghívókat küldheti. A fiókoknak a szervezeten kívüli kell lenniük. Bármilyen típusú fiókot használhat, így közösségi fiókokat is, például gmail.com vagy outlook.com végződésűeket.

## <a name="prepare-the-csv-file"></a>A CSV-fájl előkészítése

A Microsoft Excelben hozzon létre egy CSV-fájlt a meghívott felhasználók neveivel és e-mail-címeivel. Győződjön meg róla, hogy belefoglalta a **Name** és **InvitedUserEmailAddress** oszlopfejléceket. 

Például az alábbi formátumban hozza létre a munkalapot:


![Felhasználói elfogadásra várakozást mutató PowerShell-kimenet](media/tutorial-bulk-invite/AddUsersExcel.png)

Mentse a fájlt a **C:\BulkInvite\Invitations.csv** helyre. 

Ha nem rendelkezik az Excellel, létrehozhatja a CSV-fájlt bármelyik szövegszerkesztőben, például a Jegyzettömbben. Minden értéket vesszővel válasszon el, minden sort pedig új sorba írjon. 

## <a name="sign-in-to-your-tenant"></a>Bejelentkezés a bérlőbe

Futtassa a következő parancsot a bérlőtartományhoz való csatlakozáshoz:

```powershell
Connect-AzureAD -TenantDomain "<Tenant_Domain_Name>"
```
Például: `Connect-AzureAD -TenantDomain "contoso.onmicrosoft.com"`.

Amikor a rendszer erre kéri, adja meg a hitelesítő adatait.

## <a name="send-bulk-invitations"></a>Tömeges meghívók küldése

A meghívók elküldéséhez futtassa a következő PowerShell-szkriptet (ahol a **c:\bulkinvite\invitations.csv** a CSV-fájl útvonala): 

```powershell
$invitations = import-csv c:\bulkinvite\invitations.csv
   
$messageInfo = New-Object Microsoft.Open.MSGraph.Model.InvitedUserMessageInfo
   
$messageInfo.customizedMessageBody = "Hello. You are invited to the Contoso organization."
   
foreach ($email in $invitations) 
   {New-AzureADMSInvitation `
      -InvitedUserEmailAddress $email.InvitedUserEmailAddress `
      -InvitedUserDisplayName $email.Name `
      -InviteRedirectUrl https://myapps.microsoft.com `
      -InvitedUserMessageInfo $messageInfo `
      -SendInvitationMessage $true
   }
```
A szkript meghívót küld az Invitations.csv fájlban található e-mail-címeknek. Minden felhasználó számára a következőhöz hasonló kimenetnek kell megjelennie:

![Felhasználói elfogadásra várakozást mutató PowerShell-kimenet](media/tutorial-bulk-invite/B2BBulkImport.png)

## <a name="verify-users-exist-in-the-directory"></a>A felhasználók a könyvtárban való megjelenésének ellenőrzése

Annak ellenőrzésére, hogy a meghívott felhasználókat hozzáadták-e az Azure AD-hoz, futtassa a következő parancsot:
```powershell
 Get-AzureADUser -Filter "UserType eq 'Guest'"
```
Megtekintheti a meghívott felhasználó szerepel a listában, egy egyszerű felhasználónévvel (UPN) a következő formátumban *emailaddress*EXT #\@*tartomány*. Ha például *lstokes_fabrikam.com#EXT#\@contoso.onmicrosoft.com*, ahol a contoso.onmicrosoft.com a szervezet, ahonnan a Meghívók küldése.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rájuk szükség, törölheti a tesztfelhasználói fiókokat a címtárban. A felhasználói fiók törléséhez a következő parancsot futtassa:

```powershell
 Remove-AzureADUser -ObjectId "<UPN>"
```
Például:`Remove-AzureADUser -ObjectId "lstokes_fabrikam.com#EXT#@contoso.onmicrosoft.com"`


## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban tömeges meghívókat küldött szervezeten kívüli vendégfelhasználóknak. A következő szakaszban megtudhatja, hogyan működik a meghívások érvényesítési folyamata.

> [!div class="nextstepaction"]
> [További információ az Azure AD B2B együttműködés meghívóérvényesítési folyamatáról](redemption-experience.md)

