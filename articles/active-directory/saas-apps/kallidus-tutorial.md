---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Kallidus |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Kallidus között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 5a562a8d-0c2a-442e-87b5-db7e74aef0d6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/05/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 734df138bc58deb0c96916ba5178c84a63f3c53a
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712026"
---
# <a name="tutorial-integrate-kallidus-with-azure-active-directory"></a>Oktatóanyag: Kallidus integrálása az Azure Active Directoryval

Ebben az oktatóanyagban elsajátíthatja a Kallidus integrálása az Azure Active Directory (Azure AD) lesz. Kallidus integrálása az Azure ad-vel, akkor a következőket teheti:

* Szabályozza, ki férhet hozzá Kallidus Azure AD-ben.
* Engedélyezze a felhasználóknak, hogy lehet automatikusan bejelentkezve Kallidus az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további információkért lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Előfeltételek

Első lépésként szüksége van a következő elemek:

* Az Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, hozzájuthat egy [ingyenes fiókot](https://azure.microsoft.com/free/).
* Kallidus egyszeri bejelentkezés (SSO) engedélyezve van az előfizetésben.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD SSO-t egy tesztkörnyezetben. Támogatja a Kallidus **Identitásszolgáltató** által kezdeményezett egyszeri bejelentkezés.

## <a name="adding-kallidus-from-the-gallery"></a>Kallidus hozzáadása a katalógusból

Az Azure AD integrálása a Kallidus konfigurálásához hozzá kell Kallidus a katalógusból a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs ablaktáblán válassza ki a **Azure Active Directory** szolgáltatás.
1. Navigáljon a **vállalati alkalmazások** majd **minden alkalmazás**.
1. Új alkalmazás hozzáadásához válassza **új alkalmazás**.
1. Az a **Hozzáadás a katalógusból** területén írja be a **Kallidus** kifejezést a keresőmezőbe.
1. Válassza ki **Kallidus** az eredmények panelen, és vegye fel az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőn.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Konfigurálás és tesztelés az Azure AD SSO nevű tesztfelhasználó használata Kallidus **B.Simon**. Az SSO működjön kell Kallidus az Azure AD-felhasználót és a kapcsolódó felhasználó közötti hivatkozás kapcsolatot hozhat létre.

Az Azure AD SSO Kallidus tesztelése és konfigurálása, hajtsa végre a következő építőelemeket:

1. **[Az Azure AD SSO konfigurálása](#configure-azure-ad-sso)**  ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Konfigurálja a Kallidus](#configure-kallidus)**  alkalmazás oldalán az egyszeri bejelentkezési beállításainak konfigurálására.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  az Azure AD egyszeri bejelentkezés az B.Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  B.Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre Kallidus tesztfelhasználót](#create-kallidus-test-user)**  van egy megfelelője a B.Simon Kallidus, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-sso)**  ellenőrzése, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD egyszeri bejelentkezés engedélyezése az Azure Portalon.

1. Az a [az Azure portal](https://portal.azure.com/), a a **Kallidus** alkalmazás integráció lapon keresse meg a **kezelése** szakaszt, és válassza **egyszeri bejelentkezési**.
1. Az a **egyszeri bejelentkezési módszer** lapra, jelölje be **SAML**.
1. Az a **állítsa be egyszeri bejelentkezést az SAML** lap, kattintson a Szerkesztés/toll ikonra a **alapszintű SAML-konfigurációja** beállításait módosíthatja.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre a következő lépést:

    Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím:  `https://login.kallidus-suite.com/core/<ID>/Acs`

    > [!NOTE]
    > Az érték nem valódi. Frissítse az értéket a tényleges válasz URL-cím. Kapcsolattartó [Kallidus ügyfél-támogatási csapatának](https://kallidus.zendesk.com) a gépkulcsengedélyek értékének. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

1. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson a Másolás gombra, hogy **alkalmazás összevonási metaadatainak URL-címe** és mentse a számítógép.

   ![A tanúsítvány letöltési hivatkozás](common/copy-metadataurl.png)

### <a name="configure-kallidus"></a>Kallidus konfigurálása

Az egyszeri bejelentkezés konfigurálása **Kallidus** oldalon kell küldenie a **alkalmazás összevonási metaadatainak URL-címe** való [Kallidus támogatási csoportjának](https://kallidus.zendesk.com). Akkor állítsa ezt a beállítást, hogy a SAML SSO-kapcsolat megfelelően állítsa be mindkét oldalon.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban az Azure Portalon B.Simon nevű tesztfelhasználó fog létrehozni.

1. Az Azure Portal bal oldali panelén válassza **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.
1. Válassza ki **új felhasználó** a képernyő tetején.
1. Az a **felhasználói** tulajdonságok, kövesse az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. Az a **felhasználónév** mezőbe írja be a username@companydomain.extension. Például: `B.Simon@contoso.com`.
   1. Válassza ki a **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.
   1. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban B.Simon által biztosított hozzáférés Kallidus Azure egyszeri bejelentkezés használatához engedélyeznie kell.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.
1. Az alkalmazások listájában jelölje ki a **Kallidus**.
1. Az alkalmazás áttekintése lapon keresse meg a **kezelés** szakaszt, és válassza **felhasználók és csoportok**.

   ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

1. Válassza ki **felhasználó hozzáadása**, majd **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. Az a **felhasználók és csoportok** párbeszédablakban válassza **B.Simon** a felhasználók listájából, majd kattintson a **kiválasztása** gombra a képernyő alján.
1. Ha a SAML helyességi feltétel, a szerepkör értéket vár a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználóhoz a listából, és kattintson a **kiválasztása** gombra a képernyő alján.
1. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-kallidus-test-user"></a>Kallidus tesztfelhasználó létrehozása

Ebben a szakaszban egy felhasználói Britta Simon nevű Kallidus hoz létre. Együttműködve [Kallidus támogatási csoportjának](https://kallidus.zendesk.com) a felhasználók hozzáadása az Kallidus platformon. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt aktiválva.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

A Kallidus csempe kiválasztásakor a hozzáférési panelen, kell lennie automatikusan bejelentkezett a Kallidus, amelynek beállítása egyszeri Bejelentkezést. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
