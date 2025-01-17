---
title: A PIM - Azure Active Directory Azure-erőforrások szerepköreihez tartozó tevékenység és a naplózási előzmények megtekintése |} A Microsoft Docs
description: Megtekintheti a tevékenységeket, és a naplózási előzmények az Azure AD Privileged Identity Management (PIM) az Azure-erőforrások szerepköreihez tartozó.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84bd491d992ed15df288d9226b58bfe832e0692a
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476496"
---
# <a name="view-activity-and-audit-history-for-azure-resource-roles-in-pim"></a>A PIM az Azure-erőforrások szerepköreihez tartozó tevékenység és a naplózási előzmények megtekintése

Az Azure Active Directory (Azure AD) Privileged Identity Management (PIM), megtekintheti tevékenység, aktiválás és az Azure-erőforrások szerepkörök naplózási előzményei a szervezeten belül. Ez magában foglalja az előfizetések, erőforráscsoportok és még akkor is, virtuális gépeket. Az Azure Portalon, amely kihasználja az Azure szerepköralapú hozzáférés-vezérlés (RBAC) bármely erőforrás kihasználhatja a biztonsági és életciklus felügyeleti képességek az PIM-ben.

## <a name="view-activity-and-activations"></a>Tevékenységek megtekintése és az aktiválások

Milyen műveletek egy adott felhasználó tartott a különböző erőforrások megtekintéséhez, megtekintheti az Azure-erőforrás tevékenység, amely egy adott aktiválási időszak van társítva.

1. Nyissa meg **az Azure AD Privileged Identity Management**.

1. Kattintson a **Azure-erőforrások**.

1. Kattintson a tevékenység és az aktiválások megtekinteni kívánt erőforrásra.

1. Kattintson a **szerepkörök** vagy **tagok**.

1. Kattintson a felhasználó.

    A felhasználói műveletek az Azure-erőforrások grafikus nézetének dátum szerint láthatja. Azt is bemutatja a legutóbbi szerepkör-aktiválások, hogy ugyanazon időszakra.

    ![Erőforrás tevékenység összegzése és a szerepkör-aktiválások rendelkező felhasználói adatok](media/azure-pim-resource-rbac/rbac-user-details.png)

1. Kattintson egy adott szerepkör aktiválásának megjelenítéséhez a részletek és a megfelelő Azure-erőforrás tevékenység, amely történt, miközben a felhasználó volt aktív.

    ![Kiválasztott szerepkör-aktiválás és dátum szerint jelenik meg a tevékenység részletei](media/azure-pim-resource-rbac/rbac-user-resource-activity.png)

## <a name="export-role-assignments-with-children"></a>Szerepkör-hozzárendeléseit a gyermekkel rendelkező kifejezések

Megfelelőségi követelményt rendelkezhet, ahol meg kell adnia szerepkör-hozzárendelések teljes listáját az auditorok. A PIM lehetővé teszi egy adott erőforráshoz, amely tartalmazza az összes gyermek-erőforrás szerepkör-hozzárendelések lekérdezés szerepkör-hozzárendelést. Korábban a rendszergazdák teljes listáját az előfizetéshez tartozó szerepkör-hozzárendelések nehéz volt, és kellett arra, hogy minden erőforrás szerepkör-hozzárendeléseit. A PIM használata, lekérdezheti, ha az összes aktív és a jogosult szerepkör-hozzárendelések egy előfizetésben, beleértve az összes erőforráscsoportra és erőforrások szerepkör-hozzárendeléseit.

1. Nyissa meg **az Azure AD Privileged Identity Management**.

1. Kattintson a **Azure-erőforrások**.

1. Kattintson a kívánt szerepkör-hozzárendeléseit, például az előfizetés erőforrás.

1. Kattintson a **tagok**.

1. Kattintson a **exportálása** az exportálási tagsági panel megnyitásához.

    ![Tagság ablaktábla exportálni az összes tag exportálása](media/azure-pim-resource-rbac/export-membership.png)

1. Kattintson a **exportálja az összes tag** exportálhatja egy CSV-fájlban szereplő összes szerepkör-hozzárendelések.

    ![Exportált CSV fil az Excel, a szerepkör-hozzárendelések](media/azure-pim-resource-rbac/export-csv.png)

## <a name="view-resource-audit-history"></a>Erőforrás-naplózási előzmények megtekintése

Erőforrás naplózása megtekintheti az összes szerepkör tevékenység egy erőforrás.

1. Nyissa meg **az Azure AD Privileged Identity Management**.

1. Kattintson a **Azure-erőforrások**.

1. Kattintson a naplózási előzmények a megtekinteni kívánt erőforrásra.

1. Kattintson a **erőforrás naplózása**.

1. Szűrés egy előre definiált dátum- vagy egyéni tartomány használatával előzményeit.

    ![Naplózási erőforráslista szűrőkkel](media/azure-pim-resource-rbac/rbac-resource-audit.png)

1. A **naplózás típusa**válassza **Activate (hozzárendelt + aktiválva)** .

    ![Aktiválás naplózási típus szerint szűrt erőforrások naplózási listája](media/azure-pim-resource-rbac/rbac-audit-activity.png)

1. A **művelet**, kattintson a **(tevékenységet)** a felhasználó számára a felhasználói tevékenység részletei az Azure-erőforrások számára.

    ![Felhasználói tevékenység részleteit egy adott művelet](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

## <a name="view-my-audit"></a>Saját naplózás megtekintése

Saját naplózás lehetővé teszi a személyes szerepkör tevékenység megtekintéséhez.

1. Nyissa meg **az Azure AD Privileged Identity Management**.

1. Kattintson a **Azure-erőforrások**.

1. Kattintson a naplózási előzmények a megtekinteni kívánt erőforrásra.

1. Kattintson a **saját naplózás**.

1. Szűrés egy előre definiált dátum- vagy egyéni tartomány használatával előzményeit.

    ![A jelenlegi felhasználó listáját naplózása](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="next-steps"></a>További lépések

- [Rendelje hozzá a PIM az Azure-erőforrásszerepkörök](pim-resource-roles-assign-roles.md)
- [Hagyja jóvá vagy utasítsa a PIM az Azure-erőforrások szerepköreihez tartozó kérelmek](pim-resource-roles-approval-workflow.md)
- [Az Azure AD-szerepköröket a PIM naplózási előzmények megtekintése](pim-how-to-use-audit-log.md)
