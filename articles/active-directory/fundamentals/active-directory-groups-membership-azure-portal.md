---
title: Adja hozzá, vagy egy másik csoport – Azure Active Directory csoport eltávolítása |} A Microsoft Docs
description: Útmutatás hozzáadni vagy eltávolítani egy csoportot az Azure Active Directory használatával egy másik csoportot.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: 896a8b2c96dc10afbc3f179aed5a8497a8948ca9
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561926"
---
# <a name="add-or-remove-a-group-from-another-group-using-azure-active-directory"></a>Adja hozzá, vagy távolítsa el egy csoport egy másik csoportot az Azure Active Directoryval
Ez a cikk segít hozzáadhat és eltávolíthat egy csoportot az Azure Active Directory használatával egy másik csoportot.

>[!Note]
>A szülőcsoport törölni próbál, ha [frissíteni vagy törölni a csoportot és annak tagjait](active-directory-groups-delete-group.md).

## <a name="add-a-group-to-another-group"></a>Adjon hozzá egy másik csoportot
Hozzáadhat egy meglévő biztonsági csoport egy másik meglévő biztonsági csoport (más néven beágyazott csoportok), a tag létrehozása (alcsoport) csoport és a egy szülőcsoportot. A tagcsoportnak örökli az attribútumokat és a tulajdonságokat a szülőcsoporthoz, így konfigurációs időt takarít meg.

>[!Important]
>Jelenleg nem támogatottak:<ul><li>Csoportok hozzáadása a helyszíni Active Directory szinkronizált csoportokhoz.</li><li>Biztonsági csoportok hozzáadása az Office 365-csoportokhoz.</li><li>Office 365-csoportok hozzáadása biztonsági csoportokhoz vagy más Office 365-csoportokhoz.</li><li>Alkalmazások kiosztása beágyazott csoportokhoz.</li><li>Licencek alkalmazása beágyazott csoportokra.</li></ul>

### <a name="to-add-a-group-as-a-member-of-another-group"></a>Egy másik csoport tagjaként csoport hozzáadása

1. A címtár eléréséhez globális rendszergazdai fiókkal jelentkezzen be az [Azure portálra](https://portal.azure.com).

2. Válassza ki **Azure Active Directory**, majd válassza ki **csoportok**.

3. Az a **csoportok – összes csoport** lapon keresse meg és válassza ki a csoportot, amely egy másik csoport tagjává válik. Ebben a gyakorlatban használjuk a **mobileszköz-kezelési szabályzat – Nyugat-India** csoport.

    >[!Note]
    >Adhat hozzá a csoporthoz, amelynek csak egyetlen csoport egyszerre. Ezenkívül a **csoport kijelölése** box szűri a megjelenített megfelelt a bejegyzés egy felhasználó vagy az eszköz nevének bármelyik részét. Azonban a helyettesítő karakterek nem támogatottak.

    ![Kijelölt csoportok – minden csoportok lap az MDM-szabályzat – Nyugat-csoport](media/active-directory-groups-membership-azure-portal/group-all-groups-screen.png)

4. Az a **mobileszköz-kezelési szabályzat – Nyugat - csoporttagságok** lapon jelölje be **csoporttagságok**, jelölje be **Hozzáadás**, keresse meg a csoport tagja, és válassza a acsoportba **Válassza ki**. Ebben a gyakorlatban használjuk a **mobileszköz-kezelési szabályzat – az összes szervezeti** csoport.

    A **mobileszköz-kezelési szabályzat – Nyugat-India** csoport már tagja a **mobileszköz-kezelési szabályzat – az összes szervezeti** csoport, a tulajdonságok és a mobileszköz-kezelési házirend - az összes szervezeti csoport konfiguráció örökli.

    ![Hozzon létre egy csoport tagságát csoport egy másik csoporthoz való hozzáadásával](media/active-directory-groups-membership-azure-portal/add-group-membership.png)

5. Tekintse át a **mobileszköz-kezelési szabályzat – Nyugat - csoporttagságok** oldalon tekintheti meg a csoport- és tagobjektumok kapcsolatban.

    ![Mobileszköz-kezelési szabályzat – Nyugat - csoport tagságát-oldalról a szülőcsoport](media/active-directory-groups-membership-azure-portal/group-membership-blade.png)

6. A csoport- és tagobjektumok kapcsolatban részletesebb megjelenítéséhez, válassza ki a csoport nevét (**mobileszköz-kezelési szabályzat – az összes szervezeti**), és tekintse meg a **mobileszköz-kezelési szabályzat – Nyugat-India** részletei lapon.

    ![Csoport tagsági oldaláról is a tagja, és a csoport részletei](media/active-directory-groups-membership-azure-portal/group-membership-review.png)

## <a name="remove-a-group-from-another-group"></a>Csoport eltávolítása csoportból
Eltávolíthatja a meglévő biztonsági csoportot egy másik biztonsági csoportból. Azonban a csoport eltávolítása is eltávolítja a örökölt attribútumok és a tulajdonságok a tagjai.

### <a name="to-remove-a-member-group-from-another-group"></a>Egy tag csoport eltávolítása egy másik csoportot
1. Az a **csoportok – összes csoport** lapon keresse meg és válassza ki azt a csoportot, amely tagja egy másik csoportra, el kell távolítani. Ebben a gyakorlatban újra használjuk a **mobileszköz-kezelési szabályzat – Nyugat-India** csoport.

2. Az a **mobileszköz-kezelési szabályzat – Nyugat-India – áttekintés** lapon jelölje be **csoporttagságok**.

    ![Mobileszköz-kezelési szabályzat – Nyugat-India – áttekintés oldalra](media/active-directory-groups-membership-azure-portal/group-membership-overview.png)

3. Válassza ki a **mobileszköz-kezelési szabályzat – az összes szervezeti** csoportra a **mobileszköz-kezelési szabályzat – Nyugat - csoporttagságok** oldalra, és kattintson **eltávolítása** származó a **mobileszköz-kezelési szabályzat – Nyugat-India** részletei lapon.

    ![Csoport tagsági oldaláról is a tagja, és a csoport részletei](media/active-directory-groups-membership-azure-portal/group-membership-remove.png)


## <a name="additional-information"></a>További információ
E cikkekben további információk találhatók az Azure Active Directoryval kapcsolatban.

- [Csoportok és tagok megtekintése](active-directory-groups-view-azure-portal.md)

- [Hozzon létre egy alapszintű csoportot, és tagokat vehet fel](active-directory-groups-create-azure-portal.md)

- [Tagok hozzáadása vagy eltávolítása a csoportból](active-directory-groups-members-azure-portal.md)

- [A csoport beállításainak szerkesztése](active-directory-groups-settings-azure-portal.md)

- [Csoport használata SaaS-alkalmazásokhoz való hozzáférés kezelésére](../users-groups-roles/groups-saasapps.md)

- [Forgatókönyvek, korlátait és ismert problémák csoportok használata kezelheti az Azure Active Directory licencelése](../users-groups-roles/licensing-group-advanced.md#limitations-and-known-issues)
