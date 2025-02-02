---
title: Az Azure AD PIM - Azure Active Directory-szerepkörök hozzárendelése |} A Microsoft Docs
description: Útmutató az Azure AD szerepkörök hozzárendelése az Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.collection: M365-identity-device-management
ms.openlocfilehash: e1760d0e0bd356a05d84c07eda005e0526da5d13
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476520"
---
# <a name="assign-azure-ad-roles-in-pim"></a>Az Azure AD PIM-szerepkörök hozzárendelése

Az Azure Active Directory (Azure AD), a globális rendszergazdák kezdeményezhetik **állandó** az Azure AD-rendszergazdai szerepkör-hozzárendeléseket. A szerepkör-hozzárendelések hozható létre a [az Azure portal](../users-groups-roles/directory-assign-admin-roles.md) vagy [PowerShell-parancsok](/powershell/module/azuread#directory_roles).

Az Azure AD Privileged Identity Management (PIM) szolgáltatást is lehetővé teszi a kiemelt szerepkörű rendszergazdákhoz, hogy az állandó rendszergazdai szerepkör-hozzárendeléseket. Ezenkívül a kiemelt szerepkörű rendszergazdák felhasználókat tehet **jogosult** az Azure AD rendszergazdai szerepkörökhöz. A jogosult rendszergazda aktiválhatja a szerepkört, amikor szükségük van rá, majd rájuk vonatkozó engedélyek lejárnak, amint kész is van.

## <a name="make-a-user-eligible-for-a-role"></a>Győződjön meg arról, a felhasználó jogosult szerepkör

Kövesse az alábbi lépéseket, hogy egy felhasználó jogosult az Azure AD-rendszergazdai szerepkörhöz.

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com/) egy felhasználóval, amely tagja a [kiemelt szerepkörű rendszergazda](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) szerepkör.

    Egy másik rendszergazdai hozzáférést a PIM kezelése kapcsolatos információkért lásd: [hozzáférés biztosítása más rendszergazdák számára, miközben a PIM kezelése](pim-how-to-give-access-to-pim.md).

1. Nyissa meg **az Azure AD Privileged Identity Management**.

    Ha a PIM még nem indult el az Azure Portalon még, ugorjon [használatához a PIM](pim-getting-started.md).

1. Kattintson a **Azure AD-szerepkörök**.

1. Kattintson a **szerepkörök** vagy **tagok**.

    ![Kiemelt szerepkörök és a tagok menüpontok az Azure AD-szerepkörök](./media/pim-how-to-add-role-to-user/pim-directory-roles.png)

1. Kattintson a **tag hozzáadása** felügyelt tagok hozzáadása párbeszédpanel megnyitásához.

1. Kattintson a **Szerepkörválasztás**, kattintson a kezelése, és kattintson a kívánt szerepkört **kiválasztása**.

    ![Válassza ki az Azure AD-szerepköröket szerepkör ablaktábla](./media/pim-how-to-add-role-to-user/pim-select-a-role.png)

1. Kattintson a **tagok kiválasztása**, válassza ki a felhasználókat, rendelje hozzá a szerepkörhöz, és kattintson a kívánt **kiválasztása**.

    ![A tagok panel, amelyen kiválaszthatja a felhasználó kiválasztása](./media/pim-how-to-add-role-to-user/pim-select-members.png)

1. Felügyelt tagok kattintson a Hozzáadás **OK** a felhasználó hozzáadása a szerepkörhöz.

1. A szerepkörök listájában kattintson a szerepkörrel, csak a tagok listájának megtekintéséhez.

     Ha a szerepkör van hozzárendelve, a kiválasztott felhasználó megjelenik a tagok listája, **jogosult** a szerepkörhöz.

    ![Szerepkör tagjai szerepelnek együtt az aktiválási állapota](./media/pim-how-to-add-role-to-user/pim-directory-role-eligible.png)

1. Most, hogy a felhasználó nem jogosult a szerepkörhöz, tudassa vele, hogy azokat, az aktiválást található utasítások szerint [aktiválja a saját Azure AD-szerepköröket a PIM](pim-how-to-activate-role.md).

    Jogosult rendszergazdák rendszer megkéri, hogy regisztrálja az Azure multi-factor Authentication (MFA) az aktiválás során. Ha a felhasználó MFA-kiszolgáló nem tudja regisztrálni, vagy egy Microsoft-fiók használata (általában @outlook.com), végre kell hajtania az összes szerepkörökhöz állandó.

## <a name="make-a-role-assignment-permanent"></a>Állandóvá tétel szerepkör-hozzárendelés

Alapértelmezés szerint az új felhasználók jogosultak csak az Azure AD-rendszergazdai szerepkörhöz. Ha azt szeretné, hogy a szerepkör-hozzárendelés véglegessé, kövesse az alábbi lépéseket.

1. Nyissa meg **az Azure AD Privileged Identity Management**.

1. Kattintson a **Azure AD-szerepkörök**.

1. Kattintson a **tagok**.

    ![Az Azure AD-szerepkörök – tagok listája ábrázoló szerepkör és aktiválási állapota](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Kattintson egy **jogosult** állandóvá kívánt szerepkört.

1. Kattintson a **további** majd **márka perm**.

    ![Nyissa meg a panel, amely egy olyan szerepkörhöz a további menüpontok egy felhasználó listázása](./media/pim-how-to-add-role-to-user/pim-make-perm.png)

    A szerepkör most már szerepel az **állandó**.

    ![Tagok listája, amely mostantól állandó szerepkör és az aktiválási állapot megjelenítése](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members-permanent.png)

## <a name="remove-a-user-from-a-role"></a>Felhasználó eltávolítása a szerepkörből

Felhasználók eltávolítása a szerepkör-hozzárendeléseket, de győződjön meg arról, hogy mindig van legalább egy felhasználót, aki egy állandó globális rendszergazda. Ha nem biztos abban, hogy mely felhasználók továbbra is szükséges a szerepkör-hozzárendeléseket, [a szerepkörhöz tartozó hozzáférési felülvizsgálat indítása](pim-how-to-start-security-review.md).

Kövesse az alábbi lépéseket egy adott felhasználó eltávolítása Azure AD-rendszergazdai szerepkörhöz.

1. Nyissa meg **az Azure AD Privileged Identity Management**.

1. Kattintson a **Azure AD-szerepkörök**.

1. Kattintson a **tagok**.

    ![Az Azure AD-szerepkörök – tagok listája ábrázoló szerepkör és az aktiválási állapot](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Kattintson az eltávolítani kívánt szerepkör-hozzárendelés.

1. Kattintson a **további** majd **eltávolítása**.

    ![Nyissa meg a panelen egy felhasználót, hogy egy állandó szerepkör a további menü Beállítások listázása](./media/pim-how-to-add-role-to-user/pim-remove-role.png)

1. Az üzenet, amely arra kéri, hogy erősítse meg, kattintson a **Igen**.

    ![Üzenet rákérdez, tag eltávolítása szerepkörből](./media/pim-how-to-add-role-to-user/pim-remove-role-confirm.png)

    A szerepkör-hozzárendelés eltávolítása.

## <a name="authorization-error-when-assigning-roles"></a>Engedélyezési hiba a szerepkörök hozzárendelése

Ha nemrég engedélyezte a PIM-előfizetéssel, és hitelesítési hibaüzenetet kap meg, hogy egy felhasználó jogosult az Azure AD-Rendszergazda szerepkörhöz, mivel az MS-PIM szolgáltatás típusú rendszerbiztonsági taghoz még nem rendelkezik megfelelő jogosultsággal lehet. Az MS-PIM szolgáltatás típusú rendszerbiztonsági taghoz kell rendelkeznie a [felhasználói hozzáférés rendszergazdája](../../role-based-access-control/built-in-roles.md#user-access-administrator) szerepkör más szerepkörök hozzárendelése. Várnia, amíg az MS-PIM hozzá van rendelve a felhasználói hozzáférés rendszergazdájának szerepköre, rendelheti hozzá kézzel.

Kövesse az alábbi lépéseket a felhasználói hozzáférés rendszergazdája szerepkör hozzárendelése az előfizetéshez tartozó egyszerű MS-PIM szolgáltatás.

1. Jelentkezzen be az Azure Portalra globális rendszergazdaként.

1. Válasszon **minden szolgáltatás** , majd **előfizetések**.

1. Válassza ki az előfizetését.

1. Válassza a **Hozzáférés-vezérlés (IAM)** elemet.

1. Válasszon **szerepkör-hozzárendelések** szerepkör-hozzárendelések az előfizetések szintjén aktuális listájának megtekintéséhez.

   ![Egy előfizetés hozzáférés-vezérlés (IAM) paneljén](./media/pim-how-to-add-role-to-user/ms-pim-access-control.png)

1. Ellenőrizze, hogy a **MS-PIM** egyszerű szolgáltatás a **felhasználói hozzáférés rendszergazdája** szerepkör.

1. Ha nem, válassza a **szerepkör-hozzárendelés hozzáadása** megnyitásához a **szerepkör-hozzárendelés hozzáadása** ablaktáblán.

1. Az a **szerepkör** legördülő listában válassza a **felhasználói hozzáférés rendszergazdája** szerepkör.

1. Az a **kiválasztása** listában keresse meg és válassza ki a **MS-PIM** egyszerű szolgáltatást.

   ![Szerepkör-hozzárendelési ablaktáblán vehet - MS-PIM egyszerű szolgáltatás engedélyeit](./media/pim-how-to-add-role-to-user/ms-pim-add-permissions.png)

1. Válasszon **mentése** a szerepkör hozzárendelése.

   Néhány pillanat múlva a MS-PIM egyszerű szolgáltatást a felhasználói hozzáférés rendszergazdájának szerepköre az előfizetések szintjén van hozzárendelve.

   ![Hozzáférés-vezérlés (IAM) paneljéről, amelyen látható a felhasználói hozzáférés rendszergazdája szerepkör-hozzárendelést a PIM MS](./media/pim-how-to-add-role-to-user/ms-pim-user-access-administrator.png)


## <a name="next-steps"></a>További lépések

- [A PIM az Azure AD-rendszergazdai szerepkör-beállítások konfigurálása](pim-how-to-change-default-settings.md)
- [Rendelje hozzá a PIM az Azure-erőforrásszerepkörök](pim-resource-roles-assign-roles.md)
