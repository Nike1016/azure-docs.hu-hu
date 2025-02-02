---
title: 'Oktatóanyag: Azure Active Directory integráció a meta Networks-összekötővel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a meta Networks Connector között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4ae5f30d-113b-4261-b474-47ffbac08bf7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: jeedes
ms.openlocfilehash: 5ef49cb2e245e4af7b5d7f7d250194761cf0b375
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68826353"
---
# <a name="tutorial-azure-active-directory-integration-with-meta-networks-connector"></a>Oktatóanyag: Azure Active Directory integráció a meta Networks-összekötővel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a meta Networks Connectort Azure Active Directory (Azure AD) használatával.
A meta Networks-összekötő az Azure AD-vel való integrálásához a következő előnyöket nyújtja:

* A meta Networks-összekötőhöz hozzáféréssel rendelkező Azure AD-t vezérelheti.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a meta Networks-Összekötőbe (egyszeri bejelentkezés) az Azure AD-fiókjával.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció a meta Networks-összekötővel való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* Meta Networks Connector egyszeri bejelentkezésre alkalmas előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A meta Networks Connector támogatja az **SP** és a **identitásszolgáltató** által kezdeményezett SSO-t
 
* A meta Networks Connector **csak időben támogatja a** felhasználók üzembe helyezését

## <a name="adding-meta-networks-connector-from-the-gallery"></a>Meta Networks-összekötő hozzáadása a katalógusból

A meta Networks Connector Azure AD-integrációjának konfigurálásához hozzá kell adnia a meta Networks Connectort a katalógusból a felügyelt SaaS-alkalmazások listájához.

**A meta Networks-összekötő a katalógusból való hozzáadásához hajtsa végre a következő lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)** , kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **meta Networks Connector**kifejezést, válassza a **meta Networks Connector** elemet az eredmények panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Meta Networks-összekötő az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezést a meta Networks összekötővel konfigurálja és teszteli a **Britta Simon**nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a meta Networks Connector kapcsolódó felhasználója közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés meta Networks-összekötővel történő konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. A **[meta Networks Connector egyszeri bejelentkezésének konfigurálása](#configure-meta-networks-connector-single-sign-on)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre meta Networks Connector tesztelési felhasználót](#create-meta-networks-connector-test-user)** – hogy a meta Networks-összekötőn a Britta Simon partnere legyen, amely a felhasználó Azure ad-képviseletéhez van társítva.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés meta Networks-összekötővel történő konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/)a **meta Networks Connector** alkalmazás-integráció lapon válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az alapszintű **SAML-konfiguráció** szakaszban, ha az alkalmazást **identitásszolgáltató** kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépéseket:

    ![Meta Networks Connector tartomány és URL-címek egyszeri bejelentkezési adatai](common/idp-intiated.png)

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://login.nsof.io/v1/<ORGANIZATION-SHORT-NAME>/saml/metadata`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://login.nsof.io/v1/<ORGANIZATION-SHORT-NAME>/sso/saml`

5. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    ![Meta Networks Connector tartomány és URL-címek egyszeri bejelentkezési adatai](common/both-advanced-urls.png)

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://<ORGANIZATION-SHORT-NAME>.metanetworks.com/login`

    b. A **továbbítási állapot** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://<ORGANIZATION-SHORT-NAME>.metanetworks.com/#/`

    > [!NOTE]
    > Ezek az értékek nem valósak. Az oktatóanyag későbbi részében a tényleges azonosító, a válasz URL-cím és a bejelentkezési URL-cím alapján frissítse ezeket az értékeket.

6. A meta Networks Connector alkalmazás meghatározott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható. Kattintson a **Szerkesztés** ikonra a **felhasználói attribútumok** párbeszédpanel megnyitásához.

    ![image](common/edit-attribute.png)
    
7. A fentieken kívül a meta Networks Connector alkalmazás néhány további attribútumot vár az SAML-válaszba való visszatéréshez. A **felhasználó attribútumai** párbeszédpanel **felhasználói** jogcímek szakaszában a következő lépésekkel adja hozzá az SAML-jogkivonat attribútumát az alábbi táblázatban látható módon:
    
    | Name (Név) | Forrásattribútum | Névtér|
    | ---------------| --------------- | -------- |
    | FirstName | User. givenName | |
    | LastName | felhasználó. vezetéknév | |
    | EmailAddress| user.mail| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | name | user.userprincipalname| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | telefon | User. telephoneNumber | |

    a. Kattintson az **új jogcím hozzáadása** elemre a **felhasználói jogcímek kezelése** párbeszédpanel megnyitásához.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. A **név** szövegmezőbe írja be az adott sorhoz megjelenített attribútum nevét.

    c. Hagyja üresen a **névteret** .

    d. Válassza a forrás **attribútumként**lehetőséget.

    e. A **forrás attribútum** listáról írja be az adott sorhoz megjelenő attribútum értékét.

    f. Kattintson a **Ok**

    g. Kattintson a **Save** (Mentés) gombra.

8. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

9. A **meta Networks Connector beállítása** szakaszban másolja ki a megfelelő URL-címet (ka) t a követelmény szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-meta-networks-connector-single-sign-on"></a>A meta Networks Connector egyszeri bejelentkezésének konfigurálása

1. Nyisson meg egy új fület a böngészőben, és jelentkezzen be a meta Network Connector rendszergazdai fiókjába.
    
    > [!NOTE]
    > A meta Networks Connector egy biztonságos rendszer. Így a portálhoz való hozzáférés előtt hozzá kell adnia a nyilvános IP-címét az engedélyezési listához az oldalon. A nyilvános IP-cím lekéréséhez kövesse az [itt](https://whatismyipaddress.com/)megadott hivatkozást. Küldje el az IP-címet a [meta Networks Connector ügyfél-támogatási csapatának](mailto:support@metanetworks.com) , hogy az IP-címet hozzáadja az engedélyezési listához.
    
2. Nyissa meg a rendszergazdát, és válassza a **Beállítások**lehetőséget.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/metanetworksconnector-tutorial/configure3.png)
    
3. Győződjön meg arról, hogy a **naplózási internetes forgalom** és a **kényszerített VPN MFA** értéke off.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/metanetworksconnector-tutorial/configure1.png)
    
4. Nyissa meg a rendszergazdát, és válassza az **SAML**lehetőséget.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/metanetworksconnector-tutorial/configure4.png)
    
5. Hajtsa végre a következő lépéseket a **részletek** lapon:
    
    ![Egyszeri bejelentkezés konfigurálása](./media/metanetworksconnector-tutorial/configure2.png)
    
    a. Másolja az **SSO URL-címet** , és illessze be a **bejelentkezési URL** -szövegmezőbe a **meta Networks Connector tartomány és URL** -cím szakaszban.
    
    b. Másolja a **címzett URL-címét** , és illessze be a **Válasz URL-** szövegmezőbe a **meta Networks Connector tartomány és URL** -cím szakaszban.
    
    c. Másolja a **célközönség URI-ját (SP-entitás azonosítója)** , és illessze be a **meta Networks Connector tartomány és URL-cím** szakasz **azonosító (entitás azonosítója)** szövegmezőbe.
    
    d. Az SAML engedélyezése
    
6. Az **általános** lapon végezze el a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/metanetworksconnector-tutorial/configure5.png)

    a. Az **identitás-szolgáltató egyszeri bejelentkezési URL**-címében illessze be azt a **bejelentkezési URL** -címet, amelyet a Azure Portal másolt.

    b. Az **Identity Provider kiállítójában**illessze be a Azure Portalból másolt **Azure ad-azonosító** értékét.

    c. Nyissa meg a letöltött tanúsítványt Azure Portal a Jegyzettömbben, illessze be az **X. 509 tanúsítvány** szövegmezőbe.

    d. Az igényszerinti kiépítés engedélyezése.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára 

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. Az a **neve** írja be a következőt **BrittaSimon**.
  
    b. A **Felhasználónév** mezőbe írja be a **brittasimon\@yourcompanydomain. Extension nevet.**  
    Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést a meta Networks Connector elérésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza a **meta Networks Connector**elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **meta Networks Connector**elemet.

    ![A meta Networks Connector hivatkozása az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-meta-networks-connector-test-user"></a>Meta Networks Connector tesztelési felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre a meta Networks Connectorban. A meta Networks Connector az igény szerinti üzembe helyezést is támogatja, ami alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha egy felhasználó még nem létezik a meta Networks-összekötőben, a rendszer létrehoz egy újat, amikor megpróbál hozzáférni a meta Networks-összekötőhöz.

>[!Note]
>Ha manuálisan kell létrehoznia egy felhasználót, forduljon a [meta Networks Connector ügyfél-támogatási csapatához](mailto:support@metanetworks.com).

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a meta Networks Connector csempére kattint, automatikusan be kell jelentkeznie a meta Networks-Összekötőbe, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

