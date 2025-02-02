---
title: 'Oktatóanyag: Azure Active Directory integráció a Uberflip-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Uberflip között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 754b1f5b-6694-4fd6-9e1e-9fad769c64db
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 8760606c981f494b38d4eb8ac1b2cd50ceb8582c
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68852111"
---
# <a name="tutorial-azure-active-directory-integration-with-uberflip"></a>Oktatóanyag: Azure Active Directory integráció a Uberflip

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Uberflip a Azure Active Directory (Azure AD) szolgáltatással.

A Uberflip és az Azure AD integrálásával a következő előnyöket nyújtja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a Uberflip.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Uberflip (egyszeri bejelentkezés) az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti: a Azure Portal.

Az Azure AD-vel való szolgáltatott szoftverek (SaaS) alkalmazással kapcsolatos részletekért tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció Uberflip való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.
* Egyszeri bejelentkezést használó Uberflip-előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

A Uberflip a következő funkciókat támogatja:

* Az SP által kezdeményezett és IDENTITÁSSZOLGÁLTATÓ-alapú egyszeri bejelentkezés (SSO).
* Igény szerinti felhasználó üzembe helyezése.

## <a name="add-uberflip-from-the-azure-marketplace"></a>Uberflip hozzáadása az Azure Marketplace-ről

A Uberflip Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Uberflip az Azure Marketplace-ből a felügyelt SaaS-alkalmazások listájához:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A bal oldali panelen válassza az **Azure Active Directory** lehetőséget.

   ![Az Azure Active Directory lehetőség](common/select-azuread.png)

1. Lépjen a **vállalati alkalmazások**elemre, majd válassza a **minden alkalmazás**lehetőséget.

   ![A vállalati alkalmazások panel](common/enterprise-applications.png)

1. Új alkalmazás hozzáadásához válassza az **+ új alkalmazás** elemet a panel tetején.

   ![Az új alkalmazás lehetőség](common/add-new-app.png)

1. A keresőmezőbe írja be a **Uberflip**kifejezést. A keresési eredmények között válassza a **Uberflip**lehetőséget, majd válassza a **Hozzáadás** lehetőséget az alkalmazás hozzáadásához.

   ![Uberflip az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezést az Uberflip-mel konfigurálja és teszteli a **B Simon**nevű teszt felhasználó alapján. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolatot egy Azure AD-felhasználó és egy kapcsolódó felhasználó között a Uberflip-ben.

Az Azure AD egyszeri bejelentkezés Uberflip való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezés konfigurálásával](#configure-azure-ad-single-sign-on)** engedélyezheti a felhasználók számára a funkció használatát.
1. **[Konfigurálja az Uberflip egyszeri bejelentkezést](#configure-uberflip-single-sign-on)** az alkalmazás oldalának egyszeri bejelentkezési beállításainak konfigurálásához.
1. **[Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user)** az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** , hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[Hozzon létre egy Uberflip-teszt felhasználót](#create-an-uberflip-test-user)** , hogy egy b. Simon nevű felhasználó legyen a Uberflip, aki a b. Simon nevű Azure ad-felhasználóhoz van társítva.
1. **[Tesztelje az egyszeri bejelentkezést](#test-single-sign-on)** annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés Uberflip való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/) **Uberflip** alkalmazás-integráció lapján válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési lehetőség konfigurálása](common/select-sso.png)

1. Az egyszeri bejelentkezési **módszer kiválasztása** ablaktáblán válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

1. Az **egyszeri bejelentkezés az SAML-vel** panelen való beállítása lapon válassza a **Szerkesztés** (a ceruza ikon) lehetőséget az alapszintű **SAML-konfiguráció** panel megnyitásához.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az alapszintű **SAML-konfiguráció** ablaktáblán hajtsa végre a következő lépések egyikét attól függően, hogy melyik SSO-módot szeretné konfigurálni:

   * Az alkalmazás IDENTITÁSSZOLGÁLTATÓ egyszeri bejelentkezéses módban való konfigurálásához a **Válasz URL-címe (a fogyasztói szolgáltatás URL-címe)** mezőben adjon meg egy URL-címet a következő minta használatával:

     `https://app.uberflip.com/sso/saml2/<IDPID>/<ACCOUNTID>`

     ![Uberflip tartomány és URL-címek egyszeri bejelentkezési adatai](common/both-replyurl.png)

     > [!NOTE]
     > Ez az érték nem valós. Frissítse ezt az értéket a tényleges válasz URL-címével. A tényleges érték beszerzéséhez lépjen kapcsolatba a [Uberflip támogatási csapatával](mailto:support@uberflip.com). A Azure Portal alapszintű **SAML-konfiguráció** paneljén látható mintázatokat is megtekintheti.

   * Az alkalmazás SP-ben kezdeményezett egyszeri bejelentkezéses módban való konfigurálásához válassza a **további URL-címek beállítása**lehetőséget, majd a **bejelentkezési URL-cím** mezőbe írja be a következő URL-címet:

     `https://app.uberflip.com/users/login`

     ![Uberflip tartomány és URL-címek egyszeri bejelentkezési adatai](common/both-signonurl.png)

1. Az **egyszeri bejelentkezés SAML-** panelen való beállításához az **SAML aláíró tanúsítvány** szakaszban válassza a **Letöltés** lehetőséget, hogy letöltse az **összevonási metaadatok XML-** fájlját a megadott beállításokból, és mentse a számítógépre.

   ![Az összevonási metaadatok XML-letöltési lehetősége](common/metadataxml.png)

1. A **Uberflip beállítása** panelen másolja a szükséges URL-címet vagy URL-címeket:

   * **Bejelentkezési URL-cím**
   * **Azure AD-azonosító**
   * **Kijelentkezési URL-cím**

   ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="configure-uberflip-single-sign-on"></a>Uberflip egyszeri bejelentkezés konfigurálása

Az egyszeri bejelentkezés az Uberflip oldalon való konfigurálásához el kell küldenie a letöltött összevonási metaadatok XML-jét és a Azure Portal megfelelő másolt URL-címeket a [Uberflip támogató csapatnak](mailto:support@uberflip.com). A Uberflip-csoport gondoskodik arról, hogy az SAML SSO-kapcsolatok mindkét oldalon megfelelően legyenek beállítva.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban egy B. Simon nevű teszt felhasználót hoz létre a Azure Portal.

1. A Azure Portal a bal oldali ablaktáblán válassza a **Azure Active Directory** > **felhasználók** > **minden felhasználó**lehetőséget.

    ![A felhasználók és a "minden felhasználó" lehetőség](common/users.png)

1. A képernyő felső részén válassza az **+ új felhasználó**lehetőséget.

    ![Új felhasználói beállítás](common/new-user.png)

1. A **felhasználó** ablaktáblán hajtsa végre a következő lépéseket:

    ![A felhasználó panel](common/user-properties.png)

    1. A név mezőbe írja be a **BSimon** **nevet** .
  
    1. A **Felhasználónév** mezőbe írja be a **BSimon\@\<yourcompanydomain > értéket.\< bővítmény >** . Például **BSimon\@contoso.com**.

    1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.

    1. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi a B. Simon számára az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a Uberflip.

1. A Azure Portal válassza a **vállalati alkalmazások** > **minden alkalmazás** > **Uberflip**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

1. Az alkalmazások listában válassza a **Uberflip**lehetőséget.

    ![Uberflip az alkalmazások listájában](common/all-applications.png)

1. A bal oldali ablaktábla **kezelés**területén válassza a **felhasználók és csoportok**lehetőséget.

    ![A "felhasználók és csoportok" lehetőség](common/users-groups-blade.png)

1. Válassza a **+ felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** panelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

1. A **felhasználók és csoportok** panelen válassza a **B Simon** elemet a **felhasználók** listában, majd válassza a **Kiválasztás elemet** a panel alján.

1. Ha az SAML-állításban a szerepkör értékét várja, akkor a **szerepkör kiválasztása** panelen válassza ki a megfelelő szerepkört a listáról a felhasználó számára. A panel alján válassza a **kiválasztás**lehetőséget.

1. A **hozzárendelés hozzáadása** panelen válassza a **hozzárendelés**lehetőséget.

### <a name="create-an-uberflip-test-user"></a>Uberflip-teszt felhasználó létrehozása

A rendszer most létrehoz egy B. Simon nevű felhasználót a Uberflip-ben. A felhasználó létrehozásához semmit nem kell tennie. A Uberflip támogatja az igény szerinti felhasználói üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Ha egy B. Simon nevű felhasználó már nem létezik a Uberflip-ben, akkor a hitelesítés után létrejön egy újat.

> [!NOTE]
> Ha manuálisan kell létrehoznia egy felhasználót, lépjen kapcsolatba a [Uberflip támogatási csapatával](mailto:support@uberflip.com).

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a saját alkalmazások portál használatával.

Amikor kiválasztja a **Uberflip** lehetőséget a saját alkalmazások portálján, automatikusan be kell jelentkeznie a Uberflip-előfizetésbe, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a saját alkalmazások portálján: [alkalmazások elérése és használata a saját alkalmazások portálon](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

* [Az SaaS-alkalmazások Azure Active Directory-vel való integrálására szolgáló oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

* [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
