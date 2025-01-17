---
title: Az Azure rbac-RÓL és a REST API – Azure erőforrások elérésének kezelése |} A Microsoft Docs
description: Ismerje meg, hogyan kezelheti a felhasználók, csoportok és szerepköralapú hozzáférés-vezérlés (RBAC) és a REST API-t használó alkalmazások Azure-erőforrásokhoz való hozzáférését. Ez tartalmazza a hozzáférés felsorolásának, a hozzáférés adásának és a hozzáférés eltávolításának módját.
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
editor: ''
ms.assetid: 1f90228a-7aac-4ea7-ad82-b57d222ab128
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 3602e4ca83e828270ebef56c688670b896ca58a4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66472731"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-the-rest-api"></a>Rbac-RÓL és a REST API használatával Azure-erőforrásokhoz való hozzáférés kezelése

[Szerepköralapú hozzáférés-vezérlés (RBAC)](overview.md) van az Azure-erőforrásokhoz való hozzáférés kezelése ugyanúgy. Ez a cikk bemutatja, hogyan kezelheti a felhasználók, csoportok és alkalmazások rbac-RÓL és a REST API használatával hozzáférését.

## <a name="list-access"></a>Hozzáférések felsorolása

Az RBAC lista hozzáférés listázása a szerepkör-hozzárendeléseket. Szerepkör-hozzárendelések felsorolásához használja következők egyikét a [szerepkör-hozzárendelések – lista](/rest/api/authorization/roleassignments/list) REST API-k. Az eredmények pontosításához használjon, megadhatja a hatókör és a egy nem kötelező szűrő.

1. Indítsa el a következő kérelmet:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter={filter}
    ```

1. Cserélje le az URI-belül *{hatókör}* a hatókörben, amelynek meg szeretné a szerepkör-hozzárendelések lista.

    | Scope | Típus |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Előfizetés |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Erőforráscsoport |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resource |
    
       
     > [!NOTE]
     > A fenti példában Microsoft.web a az erőforrás-szolgáltató használt amelyre vonatkozik az App service-példány. Hasonlóképpen más erőforrás-szolgáltató használhat és hozhat létre a hatókör URI. Annak megismerése érdekében, hogy több tekintse meg [Azure erőforrás-szolgáltatókat és típusaikat](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services) és támogatott [Azure RM erőforrás-szolgáltatói műveletek](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations).  
     
1. Cserélje le *{szűrő}* azzal a feltétellel, hogy a szerepkör-hozzárendelés listájának szűrése a alkalmazni szeretné.

    | Szűrés | Leírás |
    | --- | --- |
    | `$filter=atScope()` | Szerepkör-hozzárendeléseit csak a megadott hatókörön, a nem többek között a szerepkör-hozzárendelést subscopes sorolja fel. |
    | `$filter=principalId%20eq%20'{objectId}'` | Megjeleníti egy adott felhasználó, csoport vagy egyszerű szolgáltatás szerepkör-hozzárendeléseit. |
    | `$filter=assignedTo('{objectId}')` | Megjeleníti egy megadott felhasználó vagy szolgáltatás szerepkör-hozzárendeléseit. Ha a felhasználó tagja egy csoportnak, amely rendelkezik a szerepkör-hozzárendelés, a szerepkör-hozzárendelés is szerepel. Ez a szűrő abban tranzitív csoportok, ami azt jelenti, hogy ha a felhasználó tagja egy csoportnak, és a csoport tagja egy másik csoportra, amely rendelkezik a szerepkör-hozzárendelés, a szerepkör-hozzárendelés is szerepel. Ez a szűrő csak a felhasználó vagy szolgáltatásnév objektumazonosító fogad el. Egy csoport objektumazonosító nem adhatók át. |

## <a name="grant-access"></a>Hozzáférés biztosítása

Az RBAC-ben a hozzáférés biztosítása egy szerepkör-hozzárendelés létrehozásával történik. Szerepkör-hozzárendelés létrehozásához használja a [szerepkör-hozzárendelések – hozzon létre](/rest/api/authorization/roleassignments/create) – REST API-t, és adja meg a rendszerbiztonsági tag, a szerepkör-definíció és a hatókör. Az API meghívásához, hozzáféréssel kell rendelkeznie a `Microsoft.Authorization/roleAssignments/write` műveletet. A beépített szerepkörök, csak [tulajdonosa](built-in-roles.md#owner) és [felhasználói hozzáférés rendszergazdája](built-in-roles.md#user-access-administrator) , amelyekhez hozzáférést ezt a műveletet.

1. Használja a [szerepkör-definíciók - lista](/rest/api/authorization/roledefinitions/list) vagy a REST API-t [beépített szerepkörök](built-in-roles.md) beolvasni a hozzárendelni kívánt szerepkör-definíció azonosítóját.

1. Egy GUID eszköz segítségével hozza létre a szerepkör-hozzárendelési azonosító esetében használt egyedi azonosító. Az azonosító formátuma: `00000000-0000-0000-0000-000000000000`

1. Indítsa el a következő kérés és a szervezet:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

    ```json
    {
      "properties": {
        "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}",
        "principalId": "{principalId}"
      }
    }
    ```
    
1. Cserélje le az URI-t, belül *{hatókör}* a hatókörben, a szerepkör-hozzárendelésre vonatkozó.

    | Scope | Típus |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Előfizetés |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Erőforráscsoport |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resource |

1. Cserélje le *{roleAssignmentName}* a szerepkör-hozzárendelés GUID azonosítóját.

1. Cserélje le a kérelem törzsében lévő *{subscriptionId}* az előfizetés-azonosítóval.

1. Cserélje le *{roleDefinitionId}* a szerepkör-definíció azonosítóval.

1. Cserélje le *{principalId}* az egyszerű szolgáltatás a szerepkörhöz rendelt, felhasználó vagy csoport objektumazonosítóját.

## <a name="remove-access"></a>Hozzáférés eltávolítása

Az RBAC-ben hozzáférés eltávolításához egy szerepkör-hozzárendelést kell eltávolítania. Szerepkör-hozzárendelés eltávolításához használja a [szerepkör-hozzárendelések – Törlés](/rest/api/authorization/roleassignments/delete) REST API-t. Az API meghívásához, hozzáféréssel kell rendelkeznie a `Microsoft.Authorization/roleAssignments/delete` műveletet. A beépített szerepkörök, csak [tulajdonosa](built-in-roles.md#owner) és [felhasználói hozzáférés rendszergazdája](built-in-roles.md#user-access-administrator) , amelyekhez hozzáférést ezt a műveletet.

1. A szerepkör-hozzárendelés azonosítója (GUID) beolvasása. Ez az azonosító adja vissza, amikor először hoz létre a szerepkör-hozzárendelést vagy a szerepkör-hozzárendeléseket listázásával beszerezheti azt.

1. Indítsa el a következő kérelmet:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

1. Belül az URI-t, cserélje le a *{hatókör}* a hatókörben, a szerepkör-hozzárendelés eltávolításához.

    | Scope | Típus |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Előfizetés |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Erőforráscsoport |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resource |

1. Cserélje le *{roleAssignmentName}* a szerepkör-hozzárendelés GUID azonosítóját.

## <a name="next-steps"></a>További lépések

- [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure Manager REST API-val](../azure-resource-manager/resource-group-template-deploy-rest.md)
- [Azure REST API-referencia](/rest/api/azure/)
- [Az Azure-erőforrásokhoz a REST API-val egyéni szerepkörök létrehozása](custom-roles-rest.md)
