---
title: 'Oktatóanyag: Az Azure Active Directory-integráció a Microsoft Azure felhőalapú felügyeleti portállal |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a felhőalapú felügyeleti portálján a Microsoft Azure között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4ea9f47c-25ca-42b0-a878-9e7aa6f34973
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/22/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: cdb6e034005902d478b94a12e2cbe44a00228735
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2019
ms.locfileid: "67551803"
---
# <a name="tutorial-azure-active-directory-integration-with-cloud-management-portal-for-microsoft-azure"></a>Oktatóanyag: Az Azure Active Directory-integráció a Microsoft Azure felhőalapú felügyeleti portállal

Ebben az oktatóanyagban elsajátíthatja, hogyan integrálható a felhőalapú felügyeleti portálján a Microsoft Azure az Azure Active Directory (Azure AD).
A Microsoft Azure felhőalapú felügyeleti portálján integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá a Cloud Management Portal a Microsoft Azure, Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezett felhőalapú felügyeleti portáljára a Microsoft Azure (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Microsoft Azure felhőalapú felügyeleti portállal, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* Felhőalapú felügyeleti portálján a Microsoft Azure egyszeri bejelentkezés az előfizetés engedélyezve van

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* A Microsoft Azure támogatja a felügyeleti portálon felhő **SP** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-cloud-management-portal-for-microsoft-azure-from-the-gallery"></a>A Microsoft Azure felhőalapú felügyeleti portál hozzáadása a katalógusból

Konfigurálása az Azure AD integrálása a Microsoft Azure felhőalapú felügyeleti portálján, hozzá kell a Microsoft Azure felhőalapú felügyeleti portálján a katalógusból a felügyelt SaaS-alkalmazások listájára.

**A Microsoft Azure felhőalapú felügyeleti portál hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)** , kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **felhőalapú felügyeleti portálján a Microsoft Azure**, jelölje be **felhőalapú felügyeleti portálján a Microsoft Azure** eredmény panelen kattintson a **Hozzáadás** gombra kattintva Vegye fel az alkalmazást.

     ![Az eredmények listájában a Microsoft Azure felhőalapú felügyeleti portálján](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés a Felhőbeli felügyeleti portállal nevű tesztfelhasználó alapján a Microsoft Azure **Britta Simon**.
Egyszeri bejelentkezés működjön, az Azure AD-felhasználót és a felhőalapú felügyeleti portálján a Microsoft Azure-hoz kapcsolódó felhasználó hivatkozás kapcsolata kell hozható létre.

Az Azure AD egyszeri bejelentkezés a Microsoft Azure felhőalapú felügyeleti portállal tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Felhőalapú felügyeleti portál konfigurálása a Microsoft Azure egyszeri bejelentkezés](#configure-cloud-management-portal-for-microsoft-azure-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Cloud Management Portal létrehozása a Microsoft Azure tesztfelhasználó](#create-cloud-management-portal-for-microsoft-azure-test-user)**  – felhőalapú felügyeleti portálján, amely kapcsolódik az Azure AD felhasználói ábrázolása a Microsoft Azure-megfelelője a Britta Simon rendelkeznie.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Az Azure AD egyszeri bejelentkezés konfigurálása a Microsoft Azure felhőalapú felügyeleti portállal, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), a a **felhőalapú felügyeleti portálján a Microsoft Azure** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Felhőalapú felügyeleti portálján a Microsoft Azure-tartomány és URL-címek egyetlen bejelentkezési adatait](common/sp-identifier-reply.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-cím:

    | |
    |--|
    | `https://portal.newsignature.com/<instancename>` |   
    | `https://portal.igcm.com/<instancename>` |

    b. Az a **azonosító** mezőbe írja be a következő minta használatával URL-cím:

    | |
    |--|
    | `https://<subdomain>.igcm.com` |
    | `https://<subdomain>.newsignature.com` |

    c. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím:

    | |
    |--|
    | `https://<subdomain>.igcm.com/<instancename>` |
    | `https://<subdomain>.newsignature.com` |
    | `https://<subdomain>.newsignature.com/<instancename>` |

    > [!NOTE]
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL-, azonosítóját és válasz URL-cím. Kapcsolattartó [felhőalapú felügyeleti portálján a Microsoft Azure ügyfél-támogatási csapatának](mailto:jczernuszka@newsignature.com) beolvasni ezeket az értékeket. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

5. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **tanúsítvány (Base64)** a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

6. Az a **beállítása a Microsoft Azure felhőalapú felügyeleti portálján** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Ad Identifier

    c. Kijelentkezési URL

### <a name="configure-cloud-management-portal-for-microsoft-azure-single-sign-on"></a>Cloud Management Portal a Microsoft Azure egyszeri bejelentkezés konfigurálása

Az egyszeri bejelentkezés konfigurálása **felhőalapú felügyeleti portálján a Microsoft Azure** oldalon kell küldenie a letöltött **tanúsítvány (Base64)** és a megfelelő másolt URL-címek az Azure Portalról [ A Microsoft Azure támogatási csapatával a felügyeleti portálon felhő](mailto:jczernuszka@newsignature.com). Akkor állítsa ezt a beállítást, hogy a SAML SSO-kapcsolat megfelelően állítsa be mindkét oldalon.

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

Ebben a szakaszban engedélyezze Britta Simon felhőalapú felügyeleti portálján a Microsoft Azure-hoz való hozzáférés biztosítása az Azure egyszeri bejelentkezés használja.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **felhőalapú felügyeleti portálján a Microsoft Azure**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **felhőalapú felügyeleti portálján a Microsoft Azure**.

    ![A felhőalapú felügyeleti portálján, az alkalmazások listáját a Microsoft Azure-hivatkozás](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-cloud-management-portal-for-microsoft-azure-test-user"></a>Cloud Management Portal létrehozása a Microsoft Azure tesztfelhasználó számára

Ebben a szakaszban egy felhőalapú felügyeleti portálon, a Microsoft Azure nevű Britta Simon felhasználó hoz létre. Együttműködve [felhőalapú felügyeleti portálján a Microsoft Azure támogatási csapatának](mailto:jczernuszka@newsignature.com) a felhasználók hozzáadása a Microsoft Azure platform felhőalapú felügyeleti portálján. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt aktiválva.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha rákattint a felhőalapú felügyeleti portálon, a Microsoft Azure csempe a hozzáférési panelen, meg kell lehet automatikusan bejelentkezett a felhő, amelynek beállítása egyszeri bejelentkezés a Microsoft Azure felügyeleti portálján. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

