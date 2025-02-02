---
title: Egyéni engedély megadása az alkalmazások regisztrációjának korlátozásához – Azure Active Directory | Microsoft Docs
description: Rendeljen hozzá egy egyéni szerepkört, amely nem korlátozott alkalmazás-regisztrációkat biztosít az Azure AD-Active Directoryban.
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f883741577de22f66cd7a9bfaf733aa3c59b879b
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707681"
---
# <a name="quickstart-grant-permission-to-create-unlimited-app-registrations"></a>Gyors útmutató: Korlátlan számú alkalmazás regisztrációjának létrehozása engedély megadása

Ebben a rövid útmutatóban egy egyéni szerepkört hoz létre, amely lehetővé teszi korlátlan számú alkalmazás regisztrációjának létrehozását, majd ezt a szerepkört hozzárendelheti egy felhasználóhoz. A hozzárendelt felhasználó ezután használhatja az Azure AD-portált, az Azure AD PowerShellt, az Azure AD Graph API vagy Microsoft Graph API-t az alkalmazások regisztrációjának létrehozásához. A beépített alkalmazás-fejlesztői szerepkörtől eltérően ez az egyéni szerepkör korlátlan számú alkalmazás-regisztráció létrehozását teszi lehetővé. Az alkalmazás fejlesztői szerepköre biztosítja a képességet, de a létrehozott objektumok teljes száma a 250-ra van korlátozva, hogy ne verjék [a teljes címtárra kiterjedő objektum kvótáját](directory-service-limits-restrictions.md).

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisite"></a>Előfeltétel

Az Azure AD egyéni szerepköreinek létrehozásához és hozzárendeléséhez szükséges legalacsonyabb jogosultsági szintű szerepkör a Kiemelt szerepkörű rendszergazda.

## <a name="create-a-new-custom-role-using-the-azure-ad-portal"></a>Új egyéni szerepkör létrehozása az Azure AD-portál használatával

1. Jelentkezzen be az [Azure ad felügyeleti](https://aad.portal.azure.com) központba a Kiemelt szerepkörű rendszergazda vagy a globális rendszergazdai engedélyekkel az Azure ad-szervezetben.
1. Válassza a **Azure Active Directory**lehetőséget, válassza a **szerepkörök és rendszergazdák**lehetőséget, majd válassza az **új egyéni szerepkör**lehetőséget.

    ![Szerepkörök létrehozása vagy szerkesztése a szerepkörök és rendszergazdák lapról](./media/roles-create-custom/new-custom-role.png)

1. Az **alapvető beállítások** lapon adja meg az "alkalmazás regisztrációs létrehozója" kifejezést a szerepkör nevéhez, és "korlátlan számú alkalmazás-regisztrációt hozhat létre" a szerepkör leírásához, majd kattintson a **tovább**gombra.

    ![adja meg az egyéni szerepkör nevét és leírását az alapok lapon](./media/roles-quickstart-app-registration-limits/basics-tab.png)

1. Az **engedélyek** lapon adja meg a "Microsoft. Directory/alkalmazások/létrehozás" kifejezést a keresőmezőbe, majd jelölje be a kívánt engedélyek melletti jelölőnégyzeteket, majd kattintson a **tovább**gombra.

    ![Egyéni szerepkör engedélyeinek kiválasztása az engedélyek lapon](./media/roles-quickstart-app-registration-limits/permissions-tab.png)

1. A **felülvizsgálat + létrehozás** lapon tekintse át az engedélyeket, és válassza a **Létrehozás**lehetőséget.

### <a name="assign-the-role-to-a-user-using-the-azure-ad-portal"></a>A szerepkör kiosztása egy felhasználónak az Azure AD-portál használatával

1. Jelentkezzen be az [Azure ad felügyeleti](https://aad.portal.azure.com) központba a Kiemelt szerepkörű rendszergazda vagy a globális rendszergazdai engedélyekkel az Azure ad-szervezetben.
1. Válassza a **Azure Active Directory** , majd a **szerepkörök és rendszergazdák**lehetőséget.
1. Válassza ki az alkalmazás regisztrációjának létrehozója szerepkört, és válassza a **hozzárendelés hozzáadása**elemet.
1. Válassza ki a kívánt felhasználót, és kattintson a **kiválasztás** gombra a felhasználó szerepkörhöz való hozzáadásához.

Kész! Ebben a rövid útmutatóban sikeresen létrehozott egy egyéni szerepkört, amely korlátlan számú alkalmazás regisztrációját hozza létre, majd hozzárendeli a szerepkört egy felhasználóhoz.

> [!TIP]
> Ha az Azure AD-portál használatával szeretné hozzárendelni a szerepkört egy alkalmazáshoz, adja meg az alkalmazás nevét a hozzárendelési oldal keresőmező mezőjében. Az alkalmazások alapértelmezés szerint nem jelennek meg a listában, de a keresési eredmények között lesznek visszaadva.

### <a name="app-registration-permissions"></a>Alkalmazás-regisztrációs engedélyek

Két engedély áll rendelkezésre, amelyek lehetővé teszi az alkalmazás-regisztrációk létrehozását, amelyek mindegyike különböző viselkedést biztosít.

- Microsoft. Directory/alkalmazások/createAsOwner: Az engedély kiosztása azt eredményezi, hogy a létrehozó létrehozta a létrehozott alkalmazás regisztrációjának első tulajdonosát, és a létrehozott alkalmazás regisztrálása a létrehozó 250 létrehozott objektumok kvótájának megfelelően fog megjelenni.
- Microsoft. Directory/applicationPolicies/Create: Az engedély kiosztása azt eredményezi, hogy a létrehozó nem lesz hozzáadva a létrehozott alkalmazás regisztrációjának első tulajdonosaként, és a létrehozott alkalmazás regisztrálása nem fog megjelenni a létrehozó 250 létrehozott objektum-kvótájában. Ezt az engedélyt körültekintően kell használni, mivel semmi nem akadályozza meg, hogy az alkalmazás regisztrációkat hozzon létre, amíg meg nem találja a címtár-szintű kvótát. Ha mindkét engedély hozzá van rendelve, ez az engedély elsőbbséget élvez.

## <a name="create-a-custom-role-using-azure-ad-powershell"></a>Egyéni szerepkör létrehozása az Azure AD PowerShell-lel

Hozzon létre egy új szerepkört a következő PowerShell-parancsfájl használatával:

``` PowerShell
# Basic role information
$description = "Application Registration Creator"
$displayName = "Can create an unlimited number of application registrations."
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/createAsOwner"
)
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customRole = New-AzureAdRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-custom-role-using-azure-ad-powershell"></a>Egyéni szerepkör kiosztása az Azure AD PowerShell használatával

#### <a name="prepare-powershell"></a>A PowerShell előkészítése

Először telepítse az Azure AD PowerShell-modult a [PowerShell-Galéria](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17). Ezután importálja az Azure AD PowerShell előzetes verzióját a következő parancs használatával:

```powershell
import-module azureadpreview
```

Annak ellenőrzéséhez, hogy a modul készen áll-e a használatra, a következő parancs által visszaadott verziónak kell megfelelnie az itt felsoroltak közül:

```powershell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azureadpreview               {Add-AzureADAdministrati...}
```

#### <a name="assign-the-custom-role"></a>Az egyéni szerepkör kiosztása

Rendelje hozzá a szerepkört az alábbi PowerShell-parancsfájl használatával:

``` PowerShell
# Basic role information
$description = "Application Registration Creator"
$displayName = "Can create an unlimited number of application registrations."
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/create"
)
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customRole = New-AzureAdRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="create-a-custom-role-using-microsoft-graph-api"></a>Egyéni szerepkör létrehozása Microsoft Graph API használatával

HTTP-kérelem az egyéni szerepkör létrehozásához.

POST

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions
```

Body

```HTTP
{
    "description":"Can create an unlimited number of application registrations.",
    "displayName":"Application Registration Creator",
    "isEnabled":true,
    "rolePermissions":
    [
        {
            "resourceActions":
            {
                "allowedResourceActions":
                [
                    "microsoft.directory/applications/create"
                ]
            },
            "condition":null
        }
    ],
    "templateId":"<PROVIDE NEW GUID HERE>",
    "version":"1"
}
```

### <a name="assign-the-custom-role-using-microsoft-graph-api"></a>Egyéni szerepkör kiosztása Microsoft Graph API használatával

A szerepkör-hozzárendelés egyesíti a rendszerbiztonsági tag AZONOSÍTÓját (amely lehet egy felhasználó vagy egy egyszerű szolgáltatásnév), egy szerepkör-definíciós (szerepkör-) azonosító és egy Azure AD-erőforrás hatóköre.

Egyéni szerepkör hozzárendelésére szolgáló HTTP-kérelem.

POST

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
```

Body

``` HTTP
{
    "principalId":"<PROVIDE OBJECTID OF USER TO ASSIGN HERE>",
    "roleDefinitionId":"<PROVIDE OBJECTID OF ROLE DEFINITION HERE>",
    "resourceScopes":["/"]
}
```

## <a name="next-steps"></a>További lépések

- Nyugodtan ossza meg velünk az [Azure ad rendszergazdai szerepkörökkel](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)foglalkozó fórumát.
- A szerepkörökkel és a rendszergazdai szerepkör-hozzárendeléssel kapcsolatos további információkért lásd: [rendszergazdai szerepkörök hozzárendelése](directory-assign-admin-roles.md).
- Az alapértelmezett felhasználói engedélyek összehasonlítását lásd: a [vendég és a tag alapértelmezett felhasználói engedélyeinek összehasonlítása](../fundamentals/users-default-permissions.md).
