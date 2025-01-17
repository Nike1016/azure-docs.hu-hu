---
title: Rbac-RÓL és az Azure PowerShell használatával Azure-erőforrásokhoz való hozzáférés kezelése |} A Microsoft Docs
description: Ismerje meg, hogyan kezelheti a felhasználók, csoportok és alkalmazások szerepköralapú hozzáférés-vezérlés (RBAC) és az Azure PowerShell használatával Azure-erőforrásokhoz való hozzáférését. Ez tartalmazza a hozzáférés felsorolásának, a hozzáférés adásának és a hozzáférés eltávolításának módját.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 9e225dba-9044-4b13-b573-2f30d77925a9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/12/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: deb7864c9f59427d6da9d27ede349c7532bf40d5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67074025"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-azure-powershell"></a>Rbac-RÓL és az Azure PowerShell használatával Azure-erőforrásokhoz való hozzáférés kezelése

[Szerepköralapú hozzáférés-vezérlés (RBAC)](overview.md) van az Azure-erőforrásokhoz való hozzáférés kezelése ugyanúgy. Ez a cikk bemutatja, hogyan kezelheti a felhasználók, csoportok és alkalmazások RBAC és az Azure PowerShell használatával hozzáférését.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Hozzáférés kezelése lesz szüksége a következők egyikét:

* [PowerShell az Azure Cloud Shellben](/azure/cloud-shell/overview)
* [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="list-roles"></a>Szerepkörök felsorolása

### <a name="list-all-available-roles"></a>Elérhető szerepkörök listája

A lista RBAC-szerepkörök, amelyek hozzárendelés érhetők el, és vizsgálja meg a műveleteket, amelyre ezek hozzáférést biztosítani, használja a [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

```azurepowershell
Get-AzRoleDefinition | FT Name, Description
```

```Example
AcrImageSigner                                    acr image signer
AcrQuarantineReader                               acr quarantine data reader
AcrQuarantineWriter                               acr quarantine data writer
API Management Service Contributor                Can manage service and the APIs
API Management Service Operator Role              Can manage service but not the APIs
API Management Service Reader Role                Read-only access to service and APIs
Application Insights Component Contributor        Can manage Application Insights components
Application Insights Snapshot Debugger            Gives user permission to use Application Insights Snapshot Debugge...
Automation Job Operator                           Create and Manage Jobs using Automation Runbooks.
Automation Operator                               Automation Operators are able to start, stop, suspend, and resume ...
...
```

### <a name="list-a-specific-role"></a>Egy adott szerepkör listája

Egy adott szerepkör listájában, használja a [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

```azurepowershell
Get-AzRoleDefinition <role_name>
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor"

Name             : Contributor
Id               : b24988ac-6180-42a0-ab88-20f7382dd24c
IsCustom         : False
Description      : Lets you manage everything except access to resources.
Actions          : {*}
NotActions       : {Microsoft.Authorization/*/Delete, Microsoft.Authorization/*/Write,
                   Microsoft.Authorization/elevateAccess/Action}
DataActions      : {}
NotDataActions   : {}
AssignableScopes : {/}
```

## <a name="list-a-role-definition"></a>A szerepkör-definíciónak listázása

### <a name="list-a-role-definition-in-json-format"></a>Egy szerepkör-definíció JSON formátumban listázása

Egy szerepkör-definíció JSON formátumban listájában, használja a [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

```azurepowershell
Get-AzRoleDefinition <role_name> | ConvertTo-Json
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor" | ConvertTo-Json

{
  "Name": "Contributor",
  "Id": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "IsCustom": false,
  "Description": "Lets you manage everything except access to resources.",
  "Actions": [
    "*"
  ],
  "NotActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action",
    "Microsoft.Blueprint/blueprintAssignments/write",
    "Microsoft.Blueprint/blueprintAssignments/delete"
  ],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

### <a name="list-actions-of-a-role"></a>A szerepkörök listája műveletek

Egy adott szerepkör esetében a műveletek listájában, használja a [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

```azurepowershell
Get-AzRoleDefinition <role_name> | FL Actions, NotActions
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor" | FL Actions, NotActions

Actions    : {*}
NotActions : {Microsoft.Authorization/*/Delete, Microsoft.Authorization/*/Write,
             Microsoft.Authorization/elevateAccess/Action,
             Microsoft.Blueprint/blueprintAssignments/write...}
```

```azurepowershell
(Get-AzRoleDefinition <role_name>).Actions
```

```Example
PS C:\> (Get-AzRoleDefinition "Virtual Machine Contributor").Actions

Microsoft.Authorization/*/read
Microsoft.Compute/availabilitySets/*
Microsoft.Compute/locations/*
Microsoft.Compute/virtualMachines/*
Microsoft.Compute/virtualMachineScaleSets/*
Microsoft.DevTestLab/schedules/*
Microsoft.Insights/alertRules/*
Microsoft.Network/applicationGateways/backendAddressPools/join/action
Microsoft.Network/loadBalancers/backendAddressPools/join/action
...
```

## <a name="list-access"></a>Hozzáférések felsorolása

Az RBAC lista hozzáférés listázása a szerepkör-hozzárendeléseket.

### <a name="list-role-assignments-at-a-specific-scope"></a>Egy adott hatókörnél szerepkör-hozzárendelések listát

A szerepkör-hozzárendeléseit a megadott előfizetés, erőforráscsoport vagy erőforrás megjelenik. Ha például az összes az aktív hozzárendelések erőforráscsoport megtekintéséhez használja [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

```azurepowershell
Get-AzRoleAssignment -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> Get-AzRoleAssignment -ResourceGroupName pharma-sales | FL DisplayName, RoleDefinitionName, Scope

DisplayName        : Alain Charon
RoleDefinitionName : Backup Operator
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales

DisplayName        : Isabella Simonsen
RoleDefinitionName : BizTalk Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales

DisplayName        : Alain Charon
RoleDefinitionName : Virtual Machine Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
```

### <a name="list-role-assignments-for-a-user"></a>Felhasználó szerepkör-hozzárendeléseinek felsorolása

Az adott felhasználóhoz rendelt szerepkörök listájában, használja a [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

```azurepowershell
Get-AzRoleAssignment -SignInName <email_or_userprincipalname>
```

```Example
PS C:\> Get-AzRoleAssignment -SignInName isabella@example.com | FL DisplayName, RoleDefinitionName, Scope

DisplayName        : Isabella Simonsen
RoleDefinitionName : BizTalk Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
```

Használja az adott felhasználóhoz rendelt szerepkörök és a, amelyhez a felhasználó tartozik a csoportokhoz rendelt szerepkörök listáját, [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

```azurepowershell
Get-AzRoleAssignment -SignInName <email_or_userprincipalname> -ExpandPrincipalGroups
```

```Example
Get-AzRoleAssignment -SignInName isabella@example.com -ExpandPrincipalGroups | FL DisplayName, RoleDefinitionName, Scope
```

### <a name="list-role-assignments-for-classic-service-administrator-and-co-administrators"></a>Szerepkör-hozzárendelések lista klasszikus szolgáltatás-rendszergazda és társrendszergazdák

Szerepkör-hozzárendeléseit a klasszikus előfizetés-adminisztrátor és a társadminisztrátorok felsorolásához használja [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

```azurepowershell
Get-AzRoleAssignment -IncludeClassicAdministrators
```

## <a name="grant-access"></a>Hozzáférés biztosítása

Az RBAC-ben a hozzáférés biztosítása egy szerepkör-hozzárendelés létrehozásával történik.

### <a name="search-for-object-ids"></a>Objektumazonosítók keresése

Szerepkör hozzárendelése, azonosítania kell az objektum (felhasználó, csoport vagy alkalmazás) és a hatókör.

Ha nem tudja, az előfizetés-azonosító, Észreveheti, hogy az a **előfizetések** panel az Azure Portalon, vagy Ön használhatja [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription).

Az Azure AD-felhasználó Objektumazonosítójának lekéréséhez használja [Get-AzADUser](/powershell/module/az.resources/get-azaduser).

```azurepowershell
Get-AzADUser -StartsWith <string_in_quotes>
```

Az Azure AD-csoport Objektumazonosítójának lekéréséhez használja [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup).

```azurepowershell
Get-AzADGroup -SearchString <group_name_in_quotes>
```

Egy Azure AD egyszerű szolgáltatás vagy alkalmazás Objektumazonosítójának lekéréséhez használja [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal).

```azurepowershell
Get-AzADServicePrincipal -SearchString <service_name_in_quotes>
```

### <a name="create-a-role-assignment-for-a-user-at-a-resource-group-scope"></a>Egy erőforrás-csoport hatókörben egy felhasználó szerepkör-hozzárendelés létrehozása

Egy felhasználó erőforrás-csoportot a csoporthatókörben, a hozzáférési jogot, használja a [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment).

```azurepowershell
New-AzRoleAssignment -SignInName <email_or_userprincipalname> -RoleDefinitionName <role_name_in_quotes> -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> New-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName pharma-sales


RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/pr
                     oviders/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

### <a name="create-a-role-assignment-using-the-unique-role-id"></a>A szerepkör egyedi azonosítója alapján a szerepkör-hozzárendelés létrehozása

Van néhány alkalommal, amikor a szerepkör nevét változhatnak, például:

- Használja a saját egyéni szerepkört, és úgy dönt, hogy módosítsa a nevet.
- Használ egy előzetes szerepkörnek **(előzetes verzió)** a nevében. Amikor megjelenik a szerepkört, átnevezése a szerepkört.

> [!IMPORTANT]
> Egy szolgáltatásiszint-szerződés nélkül biztosított egy előzetes verzióhoz, és nem javasolt éles számítási feladatok esetében. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Akkor is, ha a szerepkör át lett nevezve, a szerepkör-azonosítója nem változik. Szkriptek vagy az automation használ a szerepkör-hozzárendelések létrehozására, esetén ajánlott használni, a szerepkör egyedi azonosítója a szerepkör neve helyett. Ezért szerepkör át lett nevezve, ha a parancsfájlok valószínűleg több működik.

A szerepkör egyedi azonosítója alapján a szerepkör neve helyett a szerepkör-hozzárendelés létrehozásához használja [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment).

```azurepowershell
New-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionId <role_id> -ResourceGroupName <resource_group_name>
```

Az alábbi példa a [virtuális gépek Közreműködője](built-in-roles.md#virtual-machine-contributor) szerepkör *alain@example.com* felhasználója a *pharma – értékesítés* erőforrás hatóköre. Az egyedi szerepkör-azonosító lekéréséhez használhatja [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) vagy [beépített szerepkörök az Azure-erőforrások](built-in-roles.md).

```Example
PS C:\> New-AzRoleAssignment -ObjectId 44444444-4444-4444-4444-444444444444 -RoleDefinitionId 9980e02c-c2be-4d73-94e8-173b1dc7cf3c -Scope /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

### <a name="create-a-role-assignment-for-a-group-at-a-resource-scope"></a>Szerepkör-hozzárendelés egy csoport létrehozása egy resource hatókörben

Hozzáférés biztosítása a resource hatókörben egy csoporthoz, használja a [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment).

```azurepowershell
New-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name_in_quotes> -ResourceName <resource_name> -ResourceType <resource_type> -ParentResource <parent resource> -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> Get-AzADGroup -SearchString "Pharma"

SecurityEnabled DisplayName         Id                                   Type
--------------- -----------         --                                   ----
           True Pharma Sales Admins aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa Group

PS C:\> New-AzRoleAssignment -ObjectId aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa -RoleDefinitionName "Virtual Machine Contributor" -ResourceName RobertVirtualNetwork -ResourceType Microsoft.Network/virtualNetworks -ResourceGroupName RobertVirtualNetworkResourceGroup

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/RobertVirtualNetwork/providers/Microsoft.Authorizat
                     ion/roleAssignments/bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/RobertVirtualNetwork
DisplayName        : Pharma Sales Admins
SignInName         :
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa
ObjectType         : Group
CanDelegate        : False
```

### <a name="create-a-role-assignment-for-an-application-at-a-subscription-scope"></a>Alkalmazás szerepkör-hozzárendelés létrehozása egy előfizetési hatókörben

Az előfizetések szintjén alkalmazásokhoz való hozzáférés biztosításához használja [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment).

```azurepowershell
New-AzRoleAssignment -ObjectId <application id> -RoleDefinitionName <role_name> -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> New-AzRoleAssignment -ObjectId 77777777-7777-7777-7777-777777777777 -RoleDefinitionName "Reader" -Scope /subscriptions/00000000-0000-0000-0000-000000000000

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/66666666-6666-6666-6666-666666666666
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
DisplayName        : MyApp1
SignInName         :
RoleDefinitionName : Reader
RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
ObjectId           : 77777777-7777-7777-7777-777777777777
ObjectType         : ServicePrincipal
CanDelegate        : False
```

## <a name="remove-access"></a>Hozzáférés eltávolítása

RBAC, a hozzáférést, akkor egy szerepkör-hozzárendelés eltávolítása használatával [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment).

```azurepowershell
Remove-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope <scope_such_as_subscription>
```

```Example
PS C:\> Remove-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName pharma-sales
```

Ha a hiba jelenik meg: "A megadott információk nem feleltethető meg egy szerepkör-hozzárendelés", győződjön meg arról, hogy Ön is megadhatja a `-Scope` vagy `-ResourceGroupName` paramétereket. További információkért lásd: [RBAC hibaelhárítása az Azure-erőforrások](troubleshooting.md#role-assignments-without-a-security-principal).

## <a name="next-steps"></a>További lépések

- [Oktatóanyag: Egy csoport hozzáférést biztosít az Azure-erőforrások RBAC és az Azure PowerShell használatával](tutorial-role-assignments-group-powershell.md)
- [Oktatóanyag: Hozzon létre egy egyéni szerepkört az Azure-erőforrások Azure PowerShell-lel](tutorial-custom-role-powershell.md)
- [Az Azure PowerShell-erőforrások kezelése](../azure-resource-manager/manage-resources-powershell.md)
