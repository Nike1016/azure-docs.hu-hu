---
title: 'Oktatóanyag: A Scout segít az Azure Active Directory-integráció |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés segítségével Scout és az Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0aad9910-0bc1-4394-9f73-267cf39973ab
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3625a6bbf22a2d51546a91e8a5f7c58b7fbb264f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67101174"
---
# <a name="tutorial-azure-active-directory-integration-with-help-scout"></a>Oktatóanyag: A Scout segít az Azure Active Directory-integráció

Ebben az oktatóanyagban elsajátíthatja, hogyan segít Scout integrálása az Azure Active Directory (Azure AD).
Súgó Scout integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá a Scout segít az Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezett a Scout Súgó (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Scout segítségével, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* Súgó Scout egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Súgója támogatja a Scout **SP és IDP** által kezdeményezett egyszeri bejelentkezés
* Súgója támogatja a Scout **igény szerinti** felhasználók átadása

## <a name="adding-help-scout-from-the-gallery"></a>Súgó Scout hozzáadása a katalógusból

Konfigurálja a Scout súgó integrációja az Azure AD-be, szüksége súgó Scout hozzáadása a felügyelt SaaS-alkalmazások listájában a katalógusból.

**Súgó Scout hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)** , kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **súgó Scout**, jelölje be **súgó Scout** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Scout súgó az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés segítségével Scout nevű tesztfelhasználó alapján a **Britta Simon**.
Egyszeri bejelentkezés működjön, az Azure AD-felhasználót és a kapcsolódó felhasználó a Súgó Scout hivatkozás kapcsolata kell hozható létre.

Az Azure AD egyszeri bejelentkezés a Scout súgó tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Súgó Scout egyszeri bejelentkezés konfigurálása](#configure-help-scout-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre súgó Scout tesztfelhasználót](#create-help-scout-test-user)**  - a-megfelelője a Britta Simon szerepel, amely kapcsolódik az Azure AD felhasználói ábrázolása érdekében Scout.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Szeretné konfigurálni az Azure AD egyszeri bejelentkezés segítségével Scout, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), a a **súgó Scout** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszra, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód, hajtsa végre az alábbi lépéseket:

    ![Súgó a Scout tartomány és URL-címeket egyetlen bejelentkezési adatait](common/idp-intiated.png)

    a. **Azonosító** van a **célközönség URI-t (a Service Provider Entitásazonosító)** a Scout súgó kezdődik `urn:`

    b. **Válasz URL-cím** van a **utáni vissza URL-címe (helyességi feltétel fogyasztói szolgáltatás URL-címe)** a Scout súgó kezdődik `https://` 

    > [!NOTE]
    > Az alábbi URL-címek értékei csak bemutatásához. Ezeket az értékeket a tényleges válasz URL-cím és azonosító frissíteni szeretné. Ezeket az értéteket kap a **egyszeri bejelentkezés** hitelesítés szakaszban, az oktatóanyag későbbi részében ismertetett lapján.

5. Kattintson a **további URL-címet beállítani** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![Súgó a Scout tartomány és URL-címeket egyetlen bejelentkezési adatait](common/metadata-upload-additional-signon.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be egy URL-cím: `https://secure.helpscout.net/members/login/`

6. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **tanúsítvány (Base64)** a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

7. Az a **súgó Scout beállítása** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Ad Identifier

    c. Kijelentkezési URL

### <a name="configure-help-scout-single-sign-on"></a>Súgó Scout egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Scout súgó vállalati hely rendszergazdaként.

2. Kattintson a **kezelés** a felső menüben, és válassza ki a **vállalati** a legördülő menüből.

    ![Egyszeri bejelentkezés konfigurálása](./media/helpscout-tutorial/settings1.png)

3. Válassza ki **hitelesítési** a bal oldali navigációs ablaktáblán.

    ![Egyszeri bejelentkezés konfigurálása](./media/helpscout-tutorial/settings2.png)

4. Ezzel továbblép a SAML-beállítások szakaszban, és hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/helpscout-tutorial/settings3.png)

    a. Másolás a **utáni vissza URL-címe (helyességi feltétel fogyasztói szolgáltatás URL-címe)** értékét, és illessze be az értéket a a **válasz URL-cím** mezőt a **alapszintű SAML-konfigurációja** szakaszban az Azure-ban portál.

    b. Másolás a **célközönség URI-t (a Service Provider Entitásazonosító)** értékét, és illessze be az értéket a a **azonosító** mezőt a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

5. Váltógomb **SAML engedélyezése** a, és hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/helpscout-tutorial/settings4.png)

    a. A **egyszeri bejelentkezési URL-cím** szövegmező, illessze be az értéket a **bejelentkezési URL-cím**, az Azure Portalról másolt.

    b. Kattintson a **tanúsítvány feltöltése** feltölteni a **Certificate(Base64)** az Azure-portálról letöltött.

    c. Adja meg a szervezet e-mail tartomány(ok) például: - `contoso.com` a a **E-mail tartományokban** szövegmezőbe. Elkülönítheti a több tartomány vesszővel válasszon el. Bármikor súgó Scout felhasználó vagy rendszergazda, aki az adott tartomány ír a [súgó Scout bejelentkezési oldala](https://secure.helpscout.net/members/login/) identitásszolgáltató hitelesítő adataikkal hitelesítést lesznek átirányítva.

    d. Végül válthat **kényszerített SAML bejelentkezés** Ha azt szeretné, hogy a felhasználók számára csak jelentkezzen be súgó Scout keresztül ezzel a módszerrel. Ha továbbra is szeretné hagyja bejelölve az, hogy jelentkezzen be a Scout súgó hitelesítő adataikat, hagyhatja, kapcsolva. Akkor is, ha engedélyezve van, fióktulajdonos mindig lesz képes a fiókja jelszavát a Scout segítségével jelentkezzen be.

    e. Kattintson a **Save** (Mentés) gombra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőbe írja be **brittasimon\@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Válassza ki **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés érdekében Scout Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **súgó Scout**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **súgó Scout**.

    ![A Scout Súgó hivatkozásra az alkalmazások listáját](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-help-scout-test-user"></a>Súgó Scout tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó a Súgó Scout jön létre. Súgó Scout támogatja a just-in-time-felhasználók létrehozásának, amely alapértelmezés szerint engedélyezve van. Nincs meg ebben a szakaszban a művelet elem. Ha a felhasználó még nem létezik a Scout segítségével, a hitelesítés után egy új jön létre.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Scout súgó csempére kattint, akkor kell lehet automatikusan bejelentkezett, amelynek beállítása egyszeri bejelentkezés segítségével Scout. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)