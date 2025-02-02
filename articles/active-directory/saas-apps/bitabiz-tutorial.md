---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező BitaBIZ |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és BitaBIZ között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1a51e677-c62b-4aee-9c61-56926aaaa899
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7e52b5478bc4b483b793f155eab2f41246552d49
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67106371"
---
# <a name="tutorial-azure-active-directory-integration-with-bitabiz"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező BitaBIZ

Ebben az oktatóanyagban elsajátíthatja, hogyan BitaBIZ integrálása az Azure Active Directory (Azure AD).
BitaBIZ integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá BitaBIZ Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezve BitaBIZ (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

BitaBIZ az Azure AD-integráció konfigurálásához a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* BitaBIZ egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Támogatja a BitaBIZ **SP és IDP** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-bitabiz-from-the-gallery"></a>BitaBIZ hozzáadása a katalógusból

Az Azure AD integrálása a BitaBIZ konfigurálásához hozzá kell BitaBIZ a katalógusból a felügyelt SaaS-alkalmazások listájára.

**BitaBIZ hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)** , kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **BitaBIZ**válassza **BitaBIZ** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Az eredmények listájában BitaBIZ](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az BitaBIZ nevű tesztfelhasználó alapján **Britta Simon**.
Az egyszeri bejelentkezés működjön egy Azure AD-felhasználót és a kapcsolódó felhasználó BitaBIZ hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az BitaBIZ tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[BitaBIZ egyszeri bejelentkezés konfigurálása](#configure-bitabiz-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre BitaBIZ tesztfelhasználót](#create-bitabiz-test-user)**  – egy megfelelője a Britta Simon BitaBIZ, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Szeretné konfigurálni az Azure AD egyszeri bejelentkezés BitaBIZ, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), az a **BitaBIZ** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszra, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód, hajtsa végre a következő lépést:

    ![BitaBIZ tartomány és URL-címeket egyetlen bejelentkezési adatait](common/idp-identifier.png)

    Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-cím:  `https://www.bitabiz.com/<instanceId>`

    > [!NOTE]
    > A fenti URL-cím értéke csak bemutatásához. Frissítse az értéket a tényleges azonosítója, amelynek az ismertetése az oktatóanyag későbbi részében.

5. Kattintson a **további URL-címet beállítani** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![image](common/both-preintegrated-signon.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be az URL-cím:  `https://www.bitabiz.com/dashboard`

6. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **tanúsítvány (Base64)** a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

7. Az a **BitaBIZ beállítása** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Ad Identifier

    c. Kijelentkezési URL

### <a name="configure-bitabiz-single-sign-on"></a>BitaBIZ egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban, a bejelentkezés a BitaBIZ bérlői rendszergazdaként.

2. Kattintson a **telepítő rendszergazda**.

    ![BitaBIZ konfiguráció](./media/bitabiz-tutorial/settings1.png)

3. Kattintson a **Microsoft Integrációk** alatt **érték hozzáadása** szakaszban.

    ![BitaBIZ konfiguráció](./media/bitabiz-tutorial/settings2.png)

4. Görgessen le a szakasz **a Microsoft Azure ad-ben (engedélyezése az egyszeri bejelentkezés)** , és hajtsa végre a következő lépéseket:

    ![BitaBIZ konfiguráció](./media/bitabiz-tutorial/settings3.png)

    a. Másolja az értéket a **Entitásazonosító ("-azonosító" az Azure ad-ben)** szövegmezőbe, és illessze be azt a **azonosítója** a szövegmezőbe a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon . 

    b. Az a **az Azure AD egyszeri bejelentkezési szolgáltatás URL-cím** szövegmezőjébe illessze be **bejelentkezési URL-cím**, az Azure Portalról másolt.

    c. Az a **Azure AD SAML Entitásazonosító** szövegmezőjébe illessze be **Azure Ad-azonosító**, az Azure Portalról másolt.

    d. Nyissa meg a letöltött **Certificate(Base64)** fájlt a Jegyzettömbben, a tartalmát a vágólapra másolja és illessze be azt a **az Azure AD aláíró tanúsítvány (Base64-kódolású)** szövegmezőbe.

    e. Adja hozzá a vállalati e-mail-tartomány, nevezze el a mycompany.com **tartománynév** SSO hozzárendelése a felhasználók a vállalati e-mailben ezt a tartományt a szövegmezőben (nem kötelező).

    f. Megjelölés **egyszeri bejelentkezés engedélyezve** BitaBIZ fiók.

    g. Kattintson a **az Azure AD-konfiguráció mentéséhez** mentéséhez és az egyszeri bejelentkezési konfiguráció aktiválásához.

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

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés BitaBIZ Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **BitaBIZ**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **BitaBIZ**.

    ![Az alkalmazások listáját a BitaBIZ hivatkozásra](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-bitabiz-test-user"></a>BitaBIZ tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók BitaBIZ jelentkezzen be, akkor ki kell építeni BitaBIZ be.  
BitaBIZ, esetén kiépítése a manuális feladat.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be rendszergazdaként a BitaBIZ vállalati webhely.

2. Kattintson a **telepítő rendszergazda**.

    ![BitaBIZ felhasználó hozzáadása](./media/bitabiz-tutorial/settings1.png)

3. Kattintson a **felhasználók hozzáadása** alatt **szervezet** szakaszban.

    ![BitaBIZ felhasználó hozzáadása](./media/bitabiz-tutorial/user1.png)

4. Kattintson a **hozzáadása új alkalmazott**.

    ![BitaBIZ felhasználó hozzáadása](./media/bitabiz-tutorial/user2.png)

5. Az a **hozzáadása új alkalmazott** párbeszédpanel lapon, a következő lépésekkel:

    ![BitaBIZ felhasználó hozzáadása](./media/bitabiz-tutorial/user3.png)

    a. Az a **Utónév** szövegmezőbe írja be az első felhasználóneve Britta például.

    b. Az a **Vezetéknév** szövegmezőbe írja be a Simon például a felhasználó vezetékneve.

    c. Az a **E-mail** szövegmezőbe írja be az e-mail-cím, felhasználó, például Brittasimon@contoso.com.

    d. Válassza ki a dátumot a **alkalmazási dátum**.

    e. Nincsenek egyéb nem kötelező felhasználói attribútumokat, amelyek a felhasználó beállíthatja. Tekintse meg a [alkalmazottak telepítés Doc](https://help.bitabiz.dk/manage-or-set-up-your-account/on-boarding-employees/new-employee) további részletekért.

    f. Kattintson a **mentés alkalmazott**.

    > [!NOTE]
    > Az Azure Active Directory fióktulajdonos kap egy e-mailt, és a egy hivatkozást, mielőtt aktívvá válik, győződjön meg arról, hogy fiókjuk követi.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a BitaBIZ csempére kattint, meg kell lehet automatikusan bejelentkezett a BitaBIZ, amelynek beállítása egyszeri bejelentkezés. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
