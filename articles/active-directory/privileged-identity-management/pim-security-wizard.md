---
title: Az Azure AD szerepkörök biztonsági varázsló a PIM - Azure Active Directory |} A Microsoft Docs
description: A biztonsági varázsló, amely a konvertálás állandó emelt szintű Azure AD szerepkör-hozzárendelések jogosult az Azure AD Privileged Identity Management (PIM) használatával történő használatával írja le.
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
ms.custom: pim ; H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa4fd850ac2116dc7f353eea87845501fff020bb
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476225"
---
# <a name="azure-ad-roles-security-wizard-in-pim"></a>Az Azure AD szerepkörök biztonsági varázsló az PIM-ben

Ha Ön az első, aki futtatása az Azure Active Directory (Azure AD) Privileged Identity Management (PIM) a szervezet számára, megjelenik egy varázsló segítségével. A varázsló segítségével megismerheti a biztonsági kockázatokat az emelt szintű identitások és a PIM használata a kockázatok csökkentése érdekében. Nem kell módosításokat a varázsló a meglévő szerepkör-hozzárendelés, ha később szeretne.

## <a name="wizard-overview"></a>Varázsló – áttekintés

A szervezet elindul, miközben a PIM használatával, mielőtt állandó-e az összes szerepkör-hozzárendelések: a felhasználók mindig vannak ezek a szerepkörök akkor is, ha jelenleg nem szükségesek a jogosultságait. A varázsló az első lépés bemutatja, magas szintű jogosultságokat igénylő szerepkörök listáját, és hogy hány felhasználó van éppen ezeket a szerepköröket. Hogy részletesebben is megtekintheti a felhasználók olvashat bővebben, ha egy adott szerepkörhöz vagy nem több ismeri.

A varázsló második lépése lehetővé teszi a rendszergazda szerepkör-hozzárendelések módosítása.  

> [!WARNING]
> Fontos, hogy legalább egy globális rendszergazda, és több kiemelt szerepkörű rendszergazda egy olyan szervezeti fiókkal (nem Microsoft-fiókkal). Ha csak egy kiemelt szerepkörű rendszergazda, a szervezet nem lesz képes a PIM kezelése, ha a fiókot törölték.
> Emellett folyamatosan állandó, ha egy felhasználó Microsoft-fiókkal (fiókkal jelentkezzen be a Microsoft szolgáltatásaihoz, például a Skype-hoz és az Outlook.com-ot használnak) szerepkör-hozzárendelések. Ha azt tervezi, többtényezős hitelesítés megkövetelése az aktiváláshoz ahhoz a szerepkörhöz tartozó, hogy a felhasználó lesz zárolva.

## <a name="run-the-wizard"></a>A varázsló futtatása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Nyissa meg **az Azure AD Privileged Identity Management**.

1. Kattintson a **Azure AD-szerepkörök** majd **varázsló**.

    ![Azure AD-szerepkörök – a 3 lépésben futtassa a varázslót megjelenítő varázslólap](./media/pim-security-wizard/wizard-start.png)

1. Kattintson a **1 felderítési kiemelt szerepköröket**.

1. Tekintse át a megtekintheti, hogy mely felhasználók, állandó és jogosult kiemelt szerepkörök listáját.

    ![Fedezze fel a kiemelt szerepkörök - szerepkör panelen állandó és jogosult tagok megjelenítése](./media/pim-security-wizard/discover-privileged-roles-users.png)

1. Kattintson a **tovább** válassza ki a kívánt jogosult tagok.

    ![A tagok átalakítása jogosult lapon jelölje be a tagok számára szeretné tenni a jogosult szerepkörök a beállításokkal](./media/pim-security-wizard/convert-members-eligible.png)

1. Miután kiválasztotta a tagok, kattintson a **tovább**.

    ![Tekintse át a módosításokat oldaláról, tagok lemezszáma állandó szerepkör-hozzárendelésekkel](./media/pim-security-wizard/review-changes.png)

1. Kattintson a **OK** az állandó hozzárendelés átalakítása jogosult.

    Az átalakítás befejeződése után megjelenik egy értesítés.

    ![Értesítés, hogy egy állapota](./media/pim-security-wizard/notification-completion.png)

Ha szeretne további kiemelt szerepkörök hozzárendeléseit jogosulttá alakításához, ismét futtathatja a varázslót. Ha szeretné használni a PIM felület helyett a varázsló, [hozzárendelése az Azure AD-szerepköröket a PIM](pim-how-to-add-role-to-user.md).

## <a name="next-steps"></a>További lépések

- [Az Azure AD PIM-szerepkörök hozzárendelése](pim-how-to-add-role-to-user.md)
- [Hozzáférés biztosítása más rendszergazdák számára, miközben a PIM kezelése](pim-how-to-give-access-to-pim.md)
