---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Slacktel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Slack között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ffc5e73f-6c38-4bbb-876a-a7dd269d4e1c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/13/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26e15f704fc9604bd18a1f4848e84065fc507314
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69563101"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-slack"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Slacktel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Slackt Azure Active Directory (Azure AD-val). A Slack Azure AD-val való integrálásával a következőket teheti:

* Az Azure AD-ben a Slackhez hozzáférő vezérlő.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Slackbe az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* A Slack egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Slack támogatja az **SP** által KEZDEMÉNYEZett SSO-t
* A Slack **csak időben támogatja a** felhasználók üzembe helyezését
* A Slack támogatja a [felhasználók **automatikus** kiépítési felállítását](https://docs.microsoft.com/en-gb/azure/active-directory/saas-apps/slack-provisioning-tutorial)

## <a name="adding-slack-from-the-gallery"></a>Slack hozzáadása a gyűjteményből

A Slack Azure AD-be való integrálásának konfigurálásához hozzá kell adnia Slack-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban a keresőmezőbe írja be a **Slack** kifejezést.
1. Válassza ki a **Slack** elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-slack"></a>Azure AD-beli egyszeri bejelentkezés konfigurálása és tesztelése a Slackhez

Konfigurálja és tesztelje az Azure AD SSO-t a Slack használatával egy **B. Simon**nevű tesztelési felhasználóval. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Slackben.

Az Azure AD SSO tartalékidővel való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[SLACK SSO konfigurálása](#configure-slack-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Slack-teszt felhasználó létrehozása](#create-slack-test-user)** – a felhasználó Azure ad-képviseletéhez csatolt B. Simon-beli partneri kapcsolattal rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a **Slack** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az alapszintű **SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az alapszintű **SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://<companyname>.slack.com`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet:`https://slack.com`

    > [!NOTE]
    > A bejelentkezési URL-cím értéke nem valós. Frissítse az értéket a tényleges bejelentkezési URL-címmel. Az érték beszerzéséhez vegye fel a kapcsolatot a [Slack-ügyfél támogatási csoportjával](https://slack.com/help/contact) . Az Azure Portal alapszintű **SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. A Slack alkalmazás megadott formátumban várja az SAML-kijelentéseket. Konfigurálja a következő jogcímeket ehhez az alkalmazáshoz. Az attribútumok értékeit az alkalmazás-integráció lapon, a **felhasználói attribútumok** szakaszban kezelheti. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson a **Szerkesztés** gombra a **felhasználói attribútumok** párbeszédpanel megnyitásához.

    ![image](common/edit-attribute.png)

    > [!NOTE]
    > Ha van olyan felhasználó, aki hozzárendelt **e-mail-címe** nem Office 365-licenccel rendelkezik, akkor a **felhasználó. e-mail** -jogcím nem jelenik meg az SAML-jogkivonatban. Ezekben az esetekben azt javasoljuk, hogy a **User. userPrincipalName** használja a User **. mail** attribútum értékeként, hogy az **egyedi azonosítóként** legyen leképezve.

1. A **felhasználó attribútumai** párbeszédpanel **felhasználói** jogcímek szakaszában konfigurálja az SAML-jogkivonat attribútumot a fenti képen látható módon, és hajtsa végre a következő lépéseket:

    | Name (Név) | Forrás attribútum |
    | --- | --- |
    | first_name | User. givenName |
    | last_name | felhasználó. vezetéknév |
    | User. E-mail | user.mail |
    | User. username | user.userprincipalname |

    a. Kattintson az **új jogcím hozzáadása** elemre a **felhasználói jogcímek kezelése** párbeszédpanel megnyitásához.

    b. A **név** szövegmezőbe írja be az adott sorhoz megjelenített attribútum nevét.

    c. Hagyja üresen a **névteret** .

    d. Válassza a forrás **attribútumként**lehetőséget.

    e. A **forrás attribútum** listáról írja be az adott sorhoz megjelenő attribútum értékét.

    f. Kattintson a **Ok**

    g. Kattintson a **Save** (Mentés) gombra.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

1. A **tartalékidő beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza ki **új felhasználó** a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőben adja meg a username@companydomain.extensionnevet. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést a Slack elérésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **tartalékidő**elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-slack-sso"></a>Slack SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Slack vállalati webhelyre rendszergazdaként.

2. Lépjen a **Microsoft Azure ADra** , majd lépjen a **Team Settings**elemre.

     ![Egyszeri bejelentkezés konfigurálása az alkalmazás oldalán](./media/slack-tutorial/tutorial_slack_001.png)

3. A **csapat beállításai** szakaszban kattintson a **hitelesítés** lapra, majd a **beállítások módosítása**parancsra.

    ![Egyszeri bejelentkezés konfigurálása az alkalmazás oldalán](./media/slack-tutorial/tutorial_slack_002.png)

4. Az **SAML hitelesítési beállítások** párbeszédpanelen hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása az alkalmazás oldalán](./media/slack-tutorial/tutorial_slack_003.png)

    a.  Az **SAML 2,0-végpont (http)** szövegmezőbe illessze be a **bejelentkezési URL-cím**értékét, amelyet a Azure Portalból másolt.

    b.  Az **Identity Provider kiállító** szövegmezőben illessze be az **Azure ad-azonosító**értékét, amelyet a Azure Portalból másolt.

    c.  Nyissa meg a letöltött tanúsítványfájl a Jegyzettömbben, másolja a vágólapra a tartalmát, majd illessze be a **nyilvános tanúsítvány** szövegmezőbe.

    d. Konfigurálja a fenti három beállítást a Slack csapatának megfelelően. A beállításokkal kapcsolatos további információkért tekintse meg a **SLACK SSO konfigurációs útmutatóját** itt. `https://get.slack.help/hc/articles/220403548-Guide-to-single-sign-on-with-Slack%60`

    e.  Kattintson a **konfiguráció mentése**gombra.

### <a name="create-slack-test-user"></a>Slack-teszt felhasználó létrehozása

Ennek a szakasznak a célja egy Britta Simon nevű felhasználó létrehozása a Slackben. A Slack az igény szerinti üzembe helyezést is támogatja, ami alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. A Slack elérésére tett kísérlet során új felhasználó jön létre, ha még nem létezik. A Slack a felhasználók automatikus üzembe helyezését is támogatja, további részleteket [itt](slack-provisioning-tutorial.md) talál az automatikus felhasználó-kiépítés konfigurálásával kapcsolatban.

> [!NOTE]
> Ha manuálisan kell létrehoznia egy felhasználót, vegye fel a kapcsolatot a [Slack support csapatával](https://slack.com/help/contact).

> [!NOTE]
> Azure AD Connect a szinkronizációs eszköz, amely képes szinkronizálni a helyszíni Active Directory identitásait az Azure AD-ben, majd ezek a szinkronizált felhasználók is használhatják az alkalmazásokat, mint más Felhőbeli felhasználókat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Slack (tartalékidő) csempére kattint, automatikusan be kell jelentkeznie arra a Slack-re, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [A Slack kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)