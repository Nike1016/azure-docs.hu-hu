---
title: Hozzáadhat vagy törölhet az Azure Active Directory-felhasználók – |} A Microsoft Docs
description: Új felhasználók hozzáadása, vagy törli a meglévő felhasználókat az Azure Active Directoryval kapcsolatos utasításokat.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: a8b436fbdb0d70318e6820d3f59f1e198c639e5a
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561694"
---
# <a name="add-or-delete-users-using-azure-active-directory"></a>Hozzáadhat vagy törölhet a felhasználók Azure Active Directory használatával
Vegyen fel új felhasználókat, vagy törölje a meglévő felhasználókat a Azure Active Directory (Azure AD) szervezetből.

## <a name="add-a-new-user"></a>Új felhasználó hozzáadása
Létrehozhat egy új felhasználót az Azure Active Directory portál használatával.

### <a name="to-add-a-new-user"></a>Új felhasználó hozzáadása
1. Jelentkezzen be a [Azure Portal](https://portal.azure.com/) felhasználói rendszergazdaként a szervezet számára.

2. Válassza ki **Azure Active Directory**válassza **felhasználók**, majd válassza ki **új felhasználó**.

    ![Felhasználók – minden felhasználó oldalon kiemelve az új felhasználóval](media/add-users-azure-active-directory/new-user-all-users-blade.png)

3. Az a **felhasználói** lap, adja meg a szükséges adatokat.

    ![Új felhasználó, a felhasználó oldalon a felhasználói adatok hozzáadása](media/add-users-azure-active-directory/new-user-user-blade.png)

   - **A név (kötelező).** Az első és utolsó az új felhasználó neve. Ha például Anna Parker.

   - **A felhasználónév (kötelező).** Az új felhasználó felhasználóneve. Például: mary@contoso.com.
    
       A felhasználó nevét tartomány része kell használnia a vagy a kezdeti alapértelmezett tartománynévnek, <_saját_tartománynév_>. onmicrosoft.com, vagy egy egyéni tartománynevet, például contoso.com. Egyéni tartománynév létrehozásával kapcsolatos további információkért lásd: [egyéni tartománynév hozzáadása az Azure Active Directoryhoz](add-custom-domain.md).

   - **Profil.** További információ a felhasználó igény szerint adhat hozzá. Felhasználói adatok később is hozzáadhat. Felhasználói adatok hozzáadásával kapcsolatos további információkért lásd: [hozzáadása vagy módosítása a felhasználói profil adatainak](active-directory-users-profile-azure-portal.md).

   - **Csoportok.** Szükség esetén a felhasználót adhat hozzá egy vagy több meglévő csoportot. A felhasználói csoportokhoz egy későbbi időpontban is hozzáadhat. Felhasználói csoportok hozzáadásával kapcsolatos további információkért lásd: [létrehozásához, és tagokat vehet fel](active-directory-groups-create-azure-portal.md).

   - **Címtárbeli szerepkör.** Igény szerint hozzáadhatja a felhasználót egy Azure AD-rendszergazdai szerepkörhöz. A felhasználót hozzárendelheti globális rendszergazdaként, vagy egy vagy több korlátozott rendszergazdai szerepkörrel az Azure AD-ben. További információ a szerepkörök hozzárendelése: [szerepkörök hozzárendelése a felhasználók](active-directory-users-assign-role-azure-portal.md).

4. A megadott automatikusan létrehozott jelszó másolása a **jelszó** mezőbe. Kell megadnia ezt a jelszót a felhasználónak a kezdeti bejelentkezési folyamathoz.

5. Kattintson a **Létrehozás** gombra.

    A felhasználó létrehozása és az Azure AD-bérlőhöz hozzáadni.

## <a name="add-a-new-user-within-a-hybrid-environment"></a>A hibrid környezetben új felhasználó hozzáadása
Ha rendelkezik Azure Active Directory (felhő) és a Windows Server Active Directory (helyszíni) tartalmazó környezet, hozzáadhat új felhasználókat való szinkronizálásával a meglévő felhasználói fiók adatait. Hibrid környezetek és a felhasználók kapcsolatos további információkért lásd: [a helyszíni címtárak integrálása az Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

## <a name="delete-a-user"></a>Felhasználó törlése
Egy meglévő felhasználó Azure Active Directory portálon törölheti.

### <a name="to-delete-a-user"></a>Felhasználó törlése
1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) a szervezet felhasználói rendszergazdai fiókjának használatával.

2. Válassza ki **Azure Active Directory**válassza **felhasználók**, és keressen rá, és válassza ki a felhasználót az Azure AD-bérlőjéből törölni szeretné. Ha például _Mary Parker_.

3. Válassza ki **felhasználó törlése**.

    ![Felhasználók – minden felhasználó oldalon a kiemelt felhasználó törlése](media/add-users-azure-active-directory/delete-user-all-users-blade.png)

    A felhasználó törlődik, és már nem jelenik meg a **felhasználók – minden felhasználó** lapot. A felhasználó láthatók a **törölt felhasználók** lapon a következő 30 napra, és ez idő alatt vissza tudja állítani. A felhasználó visszaállításával kapcsolatos további információkért lásd: [visszaállítása, vagy véglegesen a közelmúltban törölt felhasználó eltávolítása](active-directory-users-restore.md). Ha töröl egy felhasználót, a felhasználó által felhasznált összes licenc elérhetővé válik más felhasználók számára.

    >[!Note]
    >Az identitás, a kapcsolattartási adatok vagy a feladat adatainak a felhasználók számára, akiknek mérvadó forrás a Windows Server Active Directory frissítéséhez a Windows Server Active Directory kell használnia. Miután elvégezte a frissítést, meg kell várnia a következő szinkronizálási ciklus befejezését, mielőtt a módosítások láthatja.

## <a name="next-steps"></a>További lépések

Miután hozzáadta a felhasználókat, a következő alapszintű folyamatok hajthatja végre:

- [Profil adatok hozzáadása vagy módosítása](active-directory-users-profile-azure-portal.md)

- [Szerepkörök hozzárendelése felhasználókhoz](active-directory-users-assign-role-azure-portal.md)

- [Hozzon létre egy alapszintű csoportot, és tagokat vehet fel](active-directory-groups-create-azure-portal.md)

- [Dinamikus csoportok és felhasználók használata](../users-groups-roles/groups-create-rule.md)

Vagy más felhasználói felügyeleti feladatokat, például hajthat végre [vendég felhasználók hozzáadása másik címtárból](../b2b/what-is-b2b.md) vagy [törölt felhasználó visszaállításával](active-directory-users-restore.md). Egyéb elérhető műveletekkel kapcsolatos további információkért lásd: [Azure Active Directory felhasználói felügyeleti dokumentáció](../users-groups-roles/index.yml).
