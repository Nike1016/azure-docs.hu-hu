---
title: 'Oktatóanyag: Azure Active Directory integráció a ServiceChannel-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és ServiceChannel között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c3546eab-96b5-489b-a309-b895eb428053
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 120dbefb6885489155a4b86fae429223766a06bc
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976091"
---
# <a name="tutorial-integrate-servicechannel-with-azure-active-directory"></a>Oktatóanyag: A ServiceChannel integrálása Azure Active Directory

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a ServiceChannel a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az ServiceChannel-t az Azure AD-vel, a következőket teheti:

* A ServiceChannel-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a ServiceChannel az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* ServiceChannel egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A ServiceChannel támogatja a **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést
* A ServiceChannel **csak időben támogatja a** felhasználók kiépítési folyamatát

## <a name="adding-servicechannel-from-the-gallery"></a>ServiceChannel hozzáadása a gyűjteményből

A ServiceChannel Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a ServiceChannel a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **ServiceChannel** kifejezést a keresőmezőbe.
1. Válassza ki a **ServiceChannel** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-single-sign-on-for-servicechannel"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a ServiceChannel

Konfigurálja és tesztelje az Azure AD SSO-t a ServiceChannel a **B. Simon**nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a ServiceChannel-ben.

Az Azure AD SSO és a ServiceChannel konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
2. **[SERVICECHANNEL SSO konfigurálása](#configure-servicechannel-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre ServiceChannel-teszt felhasználót](#create-servicechannel-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-ServiceChannel rendelkezik.
3. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **ServiceChannel** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az alapszintű **SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az alapszintű **SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

      a. Az **azonosító** szövegmezőbe írja be az értéket a következőként:`http://adfs.<domain>.com/adfs/service/trust`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://<customer domain>.servicechannel.com/saml/acs`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosító és válasz URL-címmel. Itt javasoljuk, hogy a karakterlánc egyedi értékét használja az azonosítóban. Az értékek lekéréséhez forduljon a [ServiceChannel](https://servicechannel.zendesk.com/hc/en-us) ügyfélszolgálati csapatához. Az Azure Portal alapszintű **SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. A ServiceChannel-alkalmazás meghatározott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőfelvételen az alapértelmezett attribútumok listája látható, ahol a **NameIdentifier** a **User. userPrincipalName**leképezéssel van leképezve. A ServiceChannel alkalmazás a **NameIdentifier** a **User. mail**használatával rendeli hozzá, ezért az attribútum-hozzárendelést úgy kell módosítania, hogy rákattint a **Szerkesztés** ikonra, és megváltoztatja az attribútum-hozzárendelést.

    A jogcímekre vonatkozó [](https://servicechannel.zendesk.com/hc/articles/217514326-Azure-AD-Configuration-Example) további útmutatásért tekintse meg a ServiceChannel útmutatót.

    ![image](common/edit-attribute.png)

    > [!NOTE]
    > Ez a [hivatkozás](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management) azt ismerteti, hogyan konfigurálható a **SZEREPKÖR** az Azure ad-ben.

6. A fentieken kívül, ha azt tervezi, hogy csak időben engedélyezi a felhasználók üzembe helyezését, akkor a következő jogcímeket kell hozzáadnia az alább látható módon. A **szerepkör** -jogcímet le kell képezni a User **. assignedroles** , amely a felhasználó szerepkörét tartalmazza. A **felhasználó attribútumai** párbeszédpanel **felhasználói** jogcímek szakaszában a következő lépésekkel adja hozzá az SAML-jogkivonat attribútumát az alábbi táblázatban látható módon:

    | Name (Név)   |  Forrás attribútum |
    | ------ | --- |
    | Role   | User. assignedroles |

    a. Kattintson az **új jogcím hozzáadása** elemre a **felhasználói jogcímek kezelése** párbeszédpanel megnyitásához.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. A **név** szövegmezőbe írja be az adott sorhoz megjelenített attribútum nevét.

    c. Hagyja üresen a **névteret** .

    d. Válassza a forrás **attribútumként**lehetőséget.

    e. A **forrás attribútum** listáról írja be az adott sorhoz megjelenő attribútum értékét.

    f. Kattintson a **Ok**

    g. Kattintson a **Save** (Mentés) gombra.

4. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

6. A **ServiceChannel beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a ServiceChannel.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **ServiceChannel**lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-servicechannel-sso"></a>ServiceChannel SSO konfigurálása

Ha az egyszeri bejelentkezést szeretné konfigurálni a **ServiceChannel** oldalon, el kell küldenie a letöltött **tanúsítványt (Base64)** és a megfelelő másolt url-címeket a Azure Portalról a [ServiceChannel támogatási csapatához](https://servicechannel.zendesk.com/hc/). Akkor állítsa ezt a beállítást, hogy a SAML SSO-kapcsolat megfelelően állítsa be mindkét oldalon.

### <a name="create-servicechannel-test-user"></a>ServiceChannel-tesztelési felhasználó létrehozása

Az alkalmazás a felhasználó üzembe helyezését támogatja, és a hitelesítéssel rendelkező felhasználók automatikusan jönnek létre az alkalmazásban. A teljes jogú felhasználó kiépítés esetén forduljon a [ServiceChannel támogatási csapatához](https://servicechannel.zendesk.com/hc/).

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a ServiceChannel csempére kattint, automatikusan be kell jelentkeznie arra a ServiceChannel, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

