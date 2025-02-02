---
title: 'Oktatóanyag: Azure Active Directory integráció az ADP-vel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és ADP között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 7be5331b-0481-48f7-9d6b-619dfec657e1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/25/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1597a4ca9cac7ba3885e863502f156d4c83aeed1
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68516390"
---
# <a name="tutorial-integrate-adp-with-azure-active-directory"></a>Oktatóanyag: Az ADP integrálása Azure Active Directory

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az ADP-t Azure Active Directory (Azure AD) használatával. Ha az Azure AD-vel integrálja az ADP-t, a következőket teheti:

* Az ADP-hez hozzáférő Azure AD-beli vezérlés.
* Engedélyezze, hogy a felhasználók automatikusan bejelentkezzenek az ADP-be az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* ADP egyszeri bejelentkezéses (SSO) engedélyezett előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* Az ADP támogatja a **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést

## <a name="adding-adp-from-the-gallery"></a>ADP hozzáadása a gyűjteményből

Az ADP Azure AD-be való integrálásának konfigurálásához hozzá kell adnia az ADP-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be az **ADP** kifejezést a keresőmezőbe.
1. Válassza az **ADP** elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Konfigurálja és tesztelje az Azure AD SSO-t az ADP-vel egy **B. Simon**nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot az Azure AD-felhasználó és a kapcsolódó felhasználó között az ADP-ben.

Az Azure AD SSO az ADP-vel való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
2. Az **[ADP SSO konfigurálása](#configure-adp-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
4. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
5. **[Hozzon létre egy ADP-teszt felhasználót](#create-adp-test-user)** – a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon (ADP).
6. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal az **ADP** Application Integration lapon kattintson a **Tulajdonságok fülre** , és hajtsa végre a következő lépéseket: 

    ![Egyszeri bejelentkezési tulajdonságok](./media/adpfederatedsso-tutorial/tutorial_adp_prop.png)

    a. Állítsa az **engedélyezve** beállítást a felhasználók számára a bejelentkező mező értékének **Igen**értékűre állításához.

    b. Másolja a **felhasználói hozzáférési URL-címet** , és illessze be a **bejelentkezési URL-cím konfigurálása szakaszba**, amelyet az oktatóanyag későbbi részében ismertet.

    c. Állítsa az **Igen**értékre a **felhasználó-hozzárendelés kötelező** mező értékét.

    d. A **Visible to Users** mező értékeként a **nem**értéket állítsa be.

1. A [Azure Portal](https://portal.azure.com/)az **ADP** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az alapszintű **SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az alapszintű **SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet:`https://fed.adp.com`

5. Az ADP alkalmazás meghatározott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható. Kattintson a **Szerkesztés** ikonra a felhasználói attribútumok párbeszédpanel megnyitásához. A jogcím neve mindig **PersonImmutableID** lesz, és az az érték, amelynek az **AlkalmazottKód**-vel való leképezése látható.

    Az Azure AD-ről az ADP-re irányuló felhasználó az **AlkalmazottKód** alapján lesz végrehajtva, de az alkalmazás beállításai alapján más értékre is feltérképezhető. Ezért az [ADP támogatási csapatával](https://www.adp.com/contact-us/overview.aspx) először használja a felhasználó helyes azonosítóját, és rendelje hozzá az értéket a **PersonImmutableID** jogcímhez.

    ![image](common/edit-attribute.png)

6. A fentieken kívül az ADP alkalmazás néhány további attribútumot vár az SAML-válaszban. A felhasználó attribútumai párbeszédpanel felhasználói jogcímek szakaszában a következő lépésekkel adja hozzá az SAML-jogkivonat attribútumát az alábbi táblázatban látható módon: 

    | Name (Név) | Forrás attribútum|
    | ---------------| --------- |
    | PersonImmutableID  | User. Alkalmazottkód |

    a. Kattintson az **új jogcím hozzáadása** elemre a **felhasználói jogcímek kezelése** párbeszédpanel megnyitásához.

    b. A **név** szövegmezőbe írja be az adott sorhoz megjelenített attribútum nevét.

    c. Hagyja üresen a **névteret** .

    d. Válassza a forrás **attribútumként**lehetőséget.

    e. A **forrás attribútum** listáról írja be az adott sorhoz megjelenő attribútum értékét.

    f. Kattintson a **Save** (Mentés) gombra.

    > [!NOTE] 
    > Az SAML-állítás konfigurálása előtt kapcsolatba kell lépnie az [ADP támogatási csapatával](https://www.adp.com/contact-us/overview.aspx) , és a bérlőhöz tartozó egyedi felhasználói azonosító attribútum értékét kell kérnie. Erre az értékre szüksége lesz az alkalmazás egyéni jogcímének konfigurálásához. 

4. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML** -fájlját, és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

6. Az **ADP beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="configure-adp-sso"></a>ADP SSO konfigurálása

Az egyszeri bejelentkezés **ADP** -alapú konfigurálásához fel kell töltenie a letöltött **metaadatok XML-fájlját** az [ADP webhelyén](https://adpfedsso.adp.com/public/login/index.fcc).

> [!NOTE]  
> Ez a folyamat néhány napig is eltarthat.

### <a name="configure-your-adp-services-for-federated-access"></a>Az ADP szolgáltatás (ok) konfigurálása összevont hozzáféréshez

>[!Important]
> Az ADP-szolgáltatásokhoz összevont hozzáférést igénylő alkalmazottakat hozzá kell rendelni az ADP szolgáltatás alkalmazáshoz, és ezt követően a felhasználókat újra hozzá kell rendelni az adott ADP szolgáltatáshoz.
Az ADP-képviselő jóváhagyásának kézhezvétele után konfigurálja az ADP szolgáltatás (oka) t, és rendelje hozzá a felhasználókat az adott ADP szolgáltatáshoz való hozzáférés szabályozásához.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be az **ADP** kifejezést a keresőmezőbe.
1. Válassza az **ADP** elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.
1. A Azure Portal az **ADP** Application Integration lapon kattintson a **Tulajdonságok fülre** , és hajtsa végre a következő lépéseket:  

    ![Egyszeri bejelentkezéshez csatolt tulajdonságok](./media/adpfederatedsso-tutorial/tutorial_adp_linkedproperties.png)

    a.  Állítsa az **engedélyezve** beállítást a felhasználók számára a bejelentkező mező értékének **Igen**értékűre állításához.

    b.  Állítsa az **Igen**értékre a **felhasználó-hozzárendelés kötelező** mező értékét.

    c.  Állítsa az **Igen**értékre a **Visible to Users** mező értékét.

1. A [Azure Portal](https://portal.azure.com/)az **ADP** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.

1. Az **egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza a **mód** **csatoltként**lehetőséget. az alkalmazás az **ADP**-vel való összekapcsolásához.

    ![Egyszeri bejelentkezés csatolva](./media/adpfederatedsso-tutorial/tutorial_adp_linked.png)

1. Navigáljon a **bejelentkezési URL-cím konfigurálása** szakaszhoz, hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés kelléke](./media/adpfederatedsso-tutorial/tutorial_adp_linkedsignon.png)

    a. Illessze be a **felhasználói hozzáférési URL-címet**, amelyet a fenti **Tulajdonságok lapról** másolt (a fő ADP-alkalmazásból).
                                                             
    b. A következő 5 alkalmazás támogatja a különböző **továbbító állapotú URL-címeket**. Az adott alkalmazáshoz tartozó megfelelő **továbbítási állapot URL-címét** manuálisan kell hozzáfűzni a **felhasználói hozzáférés URL-címéhez**.
    
    * **ADP munkaerő most**
        
        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?WFN`

    * **ADP munkaerő-kibővített idő**
        
        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?EETDC2`
    
    * **ADP Vantage HCM**
        
        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?ADPVANTAGE`

    * **ADP Enterprise HR**

        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?PORTAL`

    * **MyADP**

        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?REDBOX`

9. **Mentse** a módosításokat.

10. Az ADP-képviselő jóváhagyásának kézhezvétele után kezdjen el a tesztet egy vagy két felhasználóval.

    a. Rendeljen néhány felhasználót az ADP szolgáltatásbeli alkalmazáshoz az összevont hozzáférés teszteléséhez.

    b. A teszt sikeres, amikor a felhasználók hozzáférnek az ADP szolgáltatás alkalmazásához a katalógusban, és hozzáférhetnek az ADP szolgáltatáshoz.
 
11. A sikeres teszt megerősítése után rendelje hozzá az összevont ADP-szolgáltatást az egyes felhasználókhoz vagy felhasználói csoportokhoz, amelyeket az oktatóanyag későbbi részében talál, és adja ki az alkalmazottainak.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést az ADP hozzáférésének megadásával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza az **ADP**elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-adp-test-user"></a>ADP-tesztkörnyezet létrehozása

Ennek a szakasznak a célja, hogy létrehozzon egy B. Simon nevű felhasználót az ADP-ben. A felhasználók az ADP-fiókban való hozzáadásához az [ADP támogatási csapatával](https://www.adp.com/contact-us/overview.aspx) dolgozhat. 

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Amikor a hozzáférési panelen az ADP csempére kattint, automatikusan be kell jelentkeznie az ADP-be, amelyhez be kell állítania az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

