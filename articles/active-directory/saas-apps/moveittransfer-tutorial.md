---
title: 'Oktatóanyag: MOVEit átvitele – az Azure AD-integráció az Azure Active Directory-integráció |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés MOVEit átvitele – az Azure AD-integrációs és az Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8ff7102d-be73-4888-ae81-d8e3d01dd534
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/25/2019
ms.author: jeedes
ms.openlocfilehash: 76f1f0d99b47b0a57f97f4fcdf721a78a77ba9b4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67096899"
---
# <a name="tutorial-azure-active-directory-integration-with-moveit-transfer---azure-ad-integration"></a>Oktatóanyag: MOVEit átvitele – az Azure AD-integráció az Azure Active Directory-integráció

Ebben az oktatóanyagban elsajátíthatja, hogyan integrálhatja MOVEit átvitele – az Azure AD-integráció az Azure Active Directoryval (Azure AD).
MOVEit átvitele – az Azure AD-integráció az Azure AD integrálása nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá MOVEit átvitele – az Azure AD-integráció az Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezve MOVEit átvitele – az Azure AD-integrációs (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

MOVEit átvitele – az Azure AD-integráció, az Azure AD-integráció konfigurálása a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* MOVEit átviteli - integráció az Azure AD egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Támogatja a MOVEit átvitele – az Azure AD-integrációs **SP** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-moveit-transfer---azure-ad-integration-from-the-gallery"></a>Hozzáadás MOVEit átvitele – az Azure AD-integráció a katalógusból

MOVEit átvitele – az Azure AD integrálása az Azure AD-integráció konfigurálásához hozzá kell MOVEit átvitele – az Azure AD-integrációs a katalógusból a listához, kezelt SaaS-alkalmazások.

**A következő lépésekkel adja hozzá a MOVEit átviteli - katalógusból, az Azure AD-integráció:**

1. Az a **[az Azure portal](https://portal.azure.com)** , kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **MOVEit átvitele – az Azure AD-integrációs**, jelölje be **MOVEit átvitele – az Azure AD-integrációs** eredmény panelen kattintson a **Hozzáadás** gombra kattintva adhat hozzá a az alkalmazás.

     ![MOVEit átvitele – az eredménylistában az Azure AD-integráció](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az MOVEit átviteli - nevű tesztfelhasználó alapján az Azure AD-integrációs **Britta Simon**.
Az egyszeri bejelentkezés a munkahelyi Azure AD-felhasználót és a kapcsolódó felhasználó az MOVEit átviteli - hivatkozás kapcsolata az Azure AD-integrációs kell létrehozni.

Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés MOVEit átvitele – az Azure AD-integráció, kell hajtsa végre a következő építőelemeket:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[MOVEit átvitel – egyszeri bejelentkezés az Azure AD-integráció konfigurálása](#configure-moveit-transfer---azure-ad-integration-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[MOVEit átvitele – az Azure AD-integrációs tesztfelhasználó létrehozása](#create-moveit-transfer---azure-ad-integration-test-user)**  – van egy megfelelője a Britta Simon MOVEit átvitele – az Azure AD-integráció, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

MOVEit átvitele – az Azure AD-integráció, az Azure AD egyszeri bejelentkezés konfigurálásához hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), a a **MOVEit átvitele – az Azure AD-integrációs** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszt, ha rendelkezik **szolgáltató metaadatait tartalmazó fájl**, hajtsa végre az alábbi lépéseket:

    a. Kattintson a **metaadatfájl feltöltése**.

    ![Metaadatfájl feltöltése](common/upload-metadata.png)

    b. Kattintson a **mappa embléma** válassza ki a metaadat-fájlt, és kattintson a **feltöltése**.

    ![metaadatfájl kiválasztása](common/browse-upload-metadata.png)

    c. A metaadatait tartalmazó fájl sikeresen feltöltve, miután a **azonosító** és **válasz URL-cím** érték lekérdezi az automatikusan **alapszintű SAML-konfigurációja** szakaszban:

    ![MOVEit átviteli - tartomány és URL-címeket egyetlen bejelentkezési adatait az Azure AD-integráció](common/sp-identifier-reply.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-cím:  `https://contoso.com`

    > [!NOTE]
    > A **bejelentkezési URL-** értéke nem valódi. Frissítse az értéket a tényleges bejelentkezési URL-CÍMÉT. Kapcsolattartó [MOVEit átviteli - ügyfelek támogatása az Azure AD-integrációs](https://community.ipswitch.com/s/support) csapat a gépkulcsengedélyek értékének. Letöltheti a **Service Provider metaadatait tartalmazó fájl** származó a **szolgáltató metaadatok URL-címe** amely később a kifejtett a **konfigurálása MOVEit Transfer – egyetlen Azure AD-integráció Bejelentkezés** az oktatóanyag szakaszában. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

4. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **összevonási metaadatainak XML**  a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

6. Az a **MOVEit átvitele – az Azure AD-integráció beállítása** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-moveit-transfer---azure-ad-integration-single-sign-on"></a>MOVEit átvitel – egyszeri bejelentkezés az Azure AD-integráció konfigurálása

1. Jelentkezzen be rendszergazdaként a MOVEit átviteli bérlő.

2. A bal oldali navigációs panelén kattintson **beállítások**.

    ![Beállítások szakaszban az alkalmazás oldalán](./media/moveittransfer-tutorial/tutorial_moveittransfer_000.png)

3. Kattintson a **egyszeri bejelentkezés** hivatkozás, amely alatt található **biztonsági házirendek -> felhasználói hitelesítés**.

    ![Biztonsági szabályzatok az alkalmazás oldalán](./media/moveittransfer-tutorial/tutorial_moveittransfer_001.png)

4. A metaadatok URL-címe hivatkozásra a metaadat-dokumentum letöltése.

    ![Service Provider metaadatok URL-címe](./media/moveittransfer-tutorial/tutorial_moveittransfer_002.png)
    
   * Győződjön meg arról **entityID** megegyezik **azonosító** a a **alapszintű SAML-konfigurációja** szakaszban.
   * Győződjön meg arról **AssertionConsumerService** hely URL-címe megegyezik **válasz URL-cím** a a **alapszintű SAML-konfigurációja** szakaszban.
    
     ![Egyszeri bejelentkezés az alkalmazás ügyféloldali konfigurálása](./media/moveittransfer-tutorial/tutorial_moveittransfer_007.png)

5. Kattintson a **identitásszolgáltató hozzáadása** gombra kattintva adhat hozzá egy új összevont identitásszolgáltatót.

    ![Add Identity Provider](./media/moveittransfer-tutorial/tutorial_moveittransfer_003.png)

6. Kattintson a **Tallózás...**  kiválasztásához a metaadatait tartalmazó fájl, amely az Azure Portalról letöltött, majd kattintson a **identitásszolgáltató hozzáadása** feltölteni a letöltött fájlt.

    ![SAML Identity Provider](./media/moveittransfer-tutorial/tutorial_moveittransfer_004.png)

7. Jelölje be "**Igen**" as **engedélyezve** a a **összevont identitás szolgáltató beállításainak szerkesztése...**  lapot, és kattintson **mentése**.

    ![Federated Identity Provider Settings](./media/moveittransfer-tutorial/tutorial_moveittransfer_005.png)

8. Az a **összevont identitás szolgáltató felhasználói beállítások szerkesztése** lapon, hajtsa végre a következő műveleteket:
    
    ![Edit Federated Identity Provider Settings](./media/moveittransfer-tutorial/tutorial_moveittransfer_006.png)
    
    a. Válassza ki **SAML NameID** , **bejelentkezési név**.
    
    b. Válassza ki **más** , **teljes fájlvisszaállítási név** és a a **attribútumnév** szövegmezőben, hogy az érték put: `http://schemas.microsoft.com/identity/claims/displayname`.
    
    c. Válassza ki **más** , **E-mail** és a a **attribútumnév** szövegmezőben, hogy az érték put: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.
    
    d. Válassza ki **Igen** , **frissítsen-fiók automatikus létrehozása**.
    
    e. Kattintson a **mentése** gombra.

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

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés MOVEit átvitele – az Azure AD-integrációs Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **MOVEit átvitele – az Azure AD-integrációs**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **MOVEit átvitele – az Azure AD-integrációs**.

    ![MOVEit átvitele – az Azure AD-integrációs hivatkozásra az alkalmazások listáját](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-moveit-transfer---azure-ad-integration-test-user"></a>MOVEit átvitele – az Azure AD-integrációs tesztfelhasználó létrehozása

Ez a szakasz célja MOVEit átvitele – az Azure AD-integrációs Britta Simon nevű felhasználó létrehozásához. MOVEit átvitele – az Azure AD-integrációs támogatja a just-in-time-kiépítés, amelyhez engedélyezte. Nincs meg ebben a szakaszban a művelet elem. Új felhasználó jön létre az MOVEit átvitel – Ha még nem létezik az Azure AD-integrációs elérésére tett kísérlet során.

>[!NOTE]
>Hozzon létre egy felhasználót manuálisan kell, ha kapcsolódni kell a [MOVEit átvitele – az Azure AD-integrációs ügyfél ügyfélszolgálathoz](https://community.ipswitch.com/s/support).

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

MOVEit átvitele – az Azure AD-integrációs csempére kattintva a hozzáférési panelen, meg kell hogy automatikusan jelentkezzenek be MOVEit átruházásával –, amelynek beállítása egyszeri bejelentkezés az Azure AD-integrációs. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

