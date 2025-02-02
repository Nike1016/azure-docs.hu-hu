---
title: Az Azure Multi-Factor Authentication próbaverziójának engedélyezése
description: Az oktatóanyagban az Azure AD Multi-Factor Authenticationt engedélyezzük a felhasználók egy tesztcsoportja számára.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: a229eef3d49964e71becf066d67f60b18da6fc18
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67113246"
---
# <a name="tutorial-complete-an-azure-multi-factor-authentication-pilot-roll-out"></a>Oktatóanyag: Végezze el az Azure multi-factor Authentication kísérleti bevezetés

Ebben az oktatóanyagban vezeti végig, ha jelentkezik be, amely lehetővé teszi, hogy az Azure multi-factor Authentication (az Azure MFA) feltételes hozzáférési szabályzat konfigurálása az Azure Portalra. A szabályzat bevezetése és tesztelése tesztfelhasználók egy adott csoportján történik. Üzembe helyezés az Azure MFA, feltételes hozzáférés használatával a vállalatok és a rendszergazdák a hagyományos kényszerített metódus képest jelentős rugalmasságot biztosít.

> [!div class="checklist"]
> * Az Azure Multi-Factor Authentication engedélyezése
> * Az Azure Multi-Factor Authentication tesztelése

## <a name="prerequisites"></a>Előfeltételek

* Egy működő Azure AD-bérlő, legalább próbaverziós licenccel.
* Egy globális rendszergazdai jogosultsággal rendelkező fiók.
* Egy nem rendszergazdai tesztfelhasználó ismeri a tesztelésre, ha egy felhasználó létrehozásához szükséges jelszóval, tekintse meg a cikket [a rövid útmutató: Új felhasználók hozzáadása az Azure Active Directory](../add-users-azure-active-directory.md).
* Egy tesztcsoport a teszteléshez, amelynek a nem rendszergazdai szintű felhasználó a tagja. Ha létre kell hoznia a csoportot, tekintse meg a [csoport létrehozását és a tagok hozzáadását az Azure Active Directoryban](../active-directory-groups-create-azure-portal.md) ismertető cikket.

## <a name="enable-azure-multi-factor-authentication"></a>Az Azure Multi-Factor Authentication engedélyezése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) egy globális rendszergazdai fiókkal.
1. Keresse meg a **az Azure Active Directory**, **feltételes hozzáférés**
1. Válassza az **Új szabályzat** lehetőséget.
1. Adja az **MFA teszt** nevet a szabályzatnak.
1. A **Felhasználók és csoportok** területen válassza a **Felhasználók és csoportok kiválasztása** választógombot.
    * Válassza ki a cikk előfeltételeket ismertető szakaszában létrehozott tesztcsoportot.
    * Kattintson a **Kész** gombra.
1. A **Felhőalkalmazások** területen válassza az **Alkalmazások kiválasztása** választógombot.
    * Az Azure Portal felhőalkalmazása a **Microsoft Azure Management** alkalmazás.
    * Kattintson a **Kiválasztás** gombra.
    * Kattintson a **Kész** gombra.
1. Hagyja ki a **Feltételek** szakaszt.
1. A **Hozzáférés** területen győződjön meg róla, hogy a **Hozzáférés biztosítása** választógomb van kiválasztva.
    * Jelölje be a **Többtényezős hitelesítés megkövetelése** jelölőnégyzetet.
    * Kattintson a **Kiválasztás** gombra.
1. Hagyja ki a **Munkamenet** szakaszt.
1. Állítsa a **Házirend engedélyezése** kapcsolót **Be** állásba.
1. Kattintson a **Létrehozás** gombra.

## <a name="test-azure-multi-factor-authentication"></a>Az Azure Multi-Factor Authentication tesztelése

Hogy igazolja, hogy működik-e a feltételes hozzáférési szabályzatot, akkor tesztelje a olyan erőforrások, amelyek elvégzéséhez nem szükséges az MFA, és ezután az Azure Portalra, amely többtényezős Hitelesítést követel meg.

1. Nyisson meg egy új böngészőablakot InPrivate vagy inkognitó módban, és lépjen a következő oldalra: [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com).
   * Jelentkezzen be a cikk előfeltételeket ismertető szakaszában létrehozott tesztfelhasználóval. Ekkor a rendszer nem igényel többtényezős hitelesítést.
   * Zárja be a böngészőablakot.
2. Nyisson meg egy új böngészőablakot InPrivate vagy inkognitó módban, és lépjen a következő oldalra: [https://portal.azure.com](https://portal.azure.com).
   * Jelentkezzen be a cikk előfeltételeket ismertető szakaszában létrehozott tesztfelhasználóval. Most regisztrálnia kell az Azure Multi-Factor Authenticationre, és használnia kell azt.
   * Zárja be a böngészőablakot.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha az oktatóanyag keretében konfigurált funkciót a továbbiakban már nem szeretné használni, végezze el a következő módosításokat.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Keresse meg a **az Azure Active Directory**, **feltételes hozzáférési**.
1. Válassza ki a feltételes hozzáférési szabályzatot hozott létre.
1. Kattintson a **Törlés** gombra.

## <a name="next-steps"></a>További lépések

Az oktatóanyagban engedélyezte az Azure Multi-Factor Authenticationt. A következő oktatóanyagból megtudhatja, hogyan integrálható az Azure Identity Protection az új jelszó önkiszolgáló kérésével és a többtényezős hitelesítéssel.

> [!div class="nextstepaction"]
> [Kockázat értékelése bejelentkezéskor](tutorial-risk-based-sspr-mfa.md)
