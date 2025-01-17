---
title: 'Oktatóanyag: Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az SAP NetWeaver szolgáltatással | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és az SAP NetWeaver között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 1b9e59e3-e7ae-4e74-b16c-8c1a7ccfdef3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b002a9d5385d6cee3f22da7a1ddcf1f0864311ec
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68989042"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-netweaver"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az SAP NetWeaver-vel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az SAP NetWeavert Azure Active Directory (Azure AD) használatával. Az SAP NetWeaver és az Azure AD integrálásával a következőket teheti:

* Az SAP NetWeaver-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek az SAP NetWeaverba az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* SAP NetWeaver egyszeri bejelentkezés (SSO) engedélyezve előfizetés.
* SAP NetWeaver V 7.20 szükséges atleast

## <a name="scenario-description"></a>Forgatókönyv leírása

Az SAP NetWeaver az **SAML** (**SP által kezdeményezett SSO**) és a **OAuth**egyaránt támogatja. Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben. 

> [!NOTE]
> Konfigurálja az alkalmazást az SAML-ban vagy a OAuth-ben a szervezeti követelményeknek megfelelően. 

## <a name="adding-sap-netweaver-from-the-gallery"></a>SAP NetWeaver hozzáadása a katalógusból

Az SAP NetWeaver Azure AD-be való integrálásának konfigurálásához hozzá kell adnia az SAP NetWeaver-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a keresőmezőbe az **SAP NetWeaver** kifejezést.
1. Válassza az **SAP NetWeaver** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-netweaver"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése az SAP NetWeaver szolgáltatáshoz

Konfigurálja és tesztelje az Azure AD SSO-t az SAP NetWeaver használatával egy **B. Simon**nevű teszt felhasználó segítségével. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és az SAP NetWeaver kapcsolódó felhasználója között.

Az Azure AD SSO SAP NetWeaver-vel való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure ad SSO](#configure-azure-ad-sso)** -t, hogy a felhasználók használhatják ezt a funkciót.
    1. **[Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user)** az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** , hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[Konfigurálja az SAP NetWeaver](#configure-sap-netweaver-using-saml)** -t az SAML használatával az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalán.
    1. **[Hozzon létre egy SAP NetWeaver-teszt felhasználót](#create-sap-netweaver-test-user)** , hogy az az SAP NetWeaver-ban található B. Simon párja legyen, amely a felhasználó Azure ad-képviseletéhez van társítva.
1. Ellenőrizze az **[SSO](#test-sso)** -t annak ellenőrzéséhez, hogy a konfiguráció működik-e.
1. A **[OAUTH SAP NetWeaver konfigurálása](#configure-sap-netweaver-for-oauth)** a OAuth beállításainak konfigurálásához az alkalmazás oldalán.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés az SAP NetWeaver használatával történő konfigurálásához hajtsa végre a következő lépéseket:

1. Nyisson meg egy új böngészőablakot, és jelentkezzen be az SAP NetWeaver vállalati webhelyre rendszergazdaként

1. Győződjön meg arról, hogy a **http** -és **https** -szolgáltatások aktívak, és a megfelelő portok a **SMICM** T-Code-ban vannak hozzárendelve.

1. Jelentkezzen be az SAP System (T01) üzleti ügyfelére, ahol az SSO szükséges, és aktiválja a HTTP biztonsági munkamenetek felügyeletét.

    a. Ugrás a tranzakciós kód **SICF_SESSIONS**. Megjeleníti az aktuális értékekkel rendelkező összes releváns profil paramétert. A következőhöz hasonlóak:
    ```
    login/create_sso2_ticket = 2
    login/accept_sso2_ticket = 1
    login/ticketcache_entries_max = 1000
    login/ticketcache_off = 0  login/ticket_only_by_https = 0 
    icf/set_HTTPonly_flag_on_cookies = 3
    icf/user_recheck = 0  http/security_session_timeout = 1800
    http/security_context_cache_size = 2500
    rdisp/plugin_auto_logout = 1800
    rdisp/autothtime = 60
    ```
    >[!NOTE]
    > Módosítsa a fenti paramétereket a szervezet követelményeinek megfelelően, a fenti paramétereket csak jelzésként adja meg.

    b. Ha szükséges, módosítsa a paramétereket az SAP rendszer példány/alapértelmezett profiljában, majd indítsa újra az SAP rendszert.

    c. A HTTP biztonsági munkamenet engedélyezéséhez kattintson duplán a megfelelő ügyfélre.

    ![A tanúsítvány letöltési hivatkozás](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_profileparameter.png)

    d. Aktiválja az alábbi SICF-szolgáltatásokat:
    ```
    /sap/public/bc/sec/saml2
    /sap/public/bc/sec/cdc_ext_service
    /sap/bc/webdynpro/sap/saml2
    /sap/bc/webdynpro/sap/sec_diag_tool (This is only to enable / disable trace)
    ```
1. Nyissa meg az SAP System [T01/122] üzleti ügyfelének tranzakciós kód **egy saml2** . Megnyílik egy felhasználói felület egy böngészőben. Ebben a példában a 122-as SAP üzleti ügyfélként feltételezzük.

    ![A tanúsítvány letöltési hivatkozás](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_sapbusinessclient.png)

1. Adja meg felhasználónevét és jelszavát a felhasználói felületen való belépéshez, majd kattintson a **Szerkesztés**gombra.

    ![A tanúsítvány letöltési hivatkozás](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_userpwd.png)

1. Cserélje le a **szolgáltató nevét** a `http://T01122` T01122 elemre, majd kattintson a **Save (Mentés**) gombra.

    > [!NOTE]
    > Alapértelmezés szerint a szolgáltató neve `<sid><client>` formátumként `<protocol>://<name>`, de az Azure ad a nevét a következő formátumban adja meg:. a szolgáltató nevének fenntartása `https://<sid><client>` úgy történik, hogy több SAP NetWeaver ABAP-motor legyen konfigurálva az Azure ad-ben.

    ![A tanúsítvány letöltési hivatkozás](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_providername.png)

1. **Szolgáltatói metaadatok generálása**: – Ha elkészült a **helyi szolgáltató** és a **megbízható szolgáltatók** beállításainak konfigurálása az SAML 2,0 felhasználói felületen, a következő lépés a szolgáltató metaadat-fájljának létrehozása (amely tartalmazza az SAP összes beállítását, hitelesítési környezetét és egyéb konfigurációját. A fájl létrehozása után fel kell töltenie ezt az Azure AD-ben.

    ![A tanúsítvány letöltési hivatkozás](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_generatesp.png)

    a. Lépjen a **helyi szolgáltató lapra**.

    b. Kattintson a **metaadatok**elemre.

    c. Mentse a generált **metaadatokat tartalmazó XML-fájlt** a számítógépén, és töltse fel az **SAML alapszintű konfigurációs** szakaszba az **azonosító** és a **Válasz URL** -címének automatikus feltöltéséhez Azure Portalban.

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **SAP NetWeaver** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az alapszintű **SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az alapszintű **SAML-konfiguráció** szakaszban, ha az alkalmazást **identitásszolgáltató** kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépést:

    a. Kattintson a **metaadatok feltöltése** gombra a **szolgáltatói metaadat fájljának**feltöltéséhez, amelyet korábban kapott.

    b. Kattintson a **mappa embléma** válassza ki a metaadat-fájlt, és kattintson a **feltöltése**.

    c. A metaadat-fájl feltöltése után az **azonosító** és a **Válasz URL-** értékei automatikusan feltöltve lesznek az **alapszintű SAML-konfigurációs** szakasz szövegmezőben az alábbi ábrán látható módon:

    d. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://<your company instance of SAP NetWeaver>`

    > [!NOTE]
    > Láttuk, hogy néhány ügyfelünk hibát jelzett a példányhoz konfigurált helytelen válasz URL-cím miatt. Ha ilyen hibaüzenetet kap, a következő PowerShell-parancsfájl használatával megadhatja a példány helyes válaszának URL-címét.
    > ```
    > Set-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId -ReplyUrls "<Your Correct Reply URL(s)>"
    > ``` 
    > Az ServicePrincipal objektum AZONOSÍTÓját elsőként kell beállítani, vagy itt is átadhatja.

1. Az SAP NetWeaver alkalmazás meghatározott formátumban várja az SAML-kijelentéseket, így egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható. Kattintson a **Szerkesztés** ikonra a felhasználói attribútumok párbeszédpanel megnyitásához.

    ![image](common/edit-attribute.png)

1. A **felhasználó attribútumai** párbeszédpanel **felhasználói** jogcímek szakaszában konfigurálja az SAML-jogkivonat attribútumot a fenti képen látható módon, és hajtsa végre a következő lépéseket:

    a. Kattintson a **Szerkesztés ikonra** a **felhasználói jogcímek kezelése** párbeszédpanel megnyitásához.

    ![image](./media/sapnetweaver-tutorial/nameidattribute.png)

    ![image](./media/sapnetweaver-tutorial/nameidattribute1.png)

    b. Az **átalakítás** listából válassza a **ExtractMailPrefix ()** elemet.

    c. Az **1. paraméter** listából válassza a **User. userprinicipalname**elemet.

    d. Kattintson a **Save** (Mentés) gombra.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML** -fájlját, és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

   ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

1. Az **SAP NetWeaver beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények alapján.

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát az SAP NetWeaver elérésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza az **SAP NetWeaver**elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-sap-netweaver-using-saml"></a>Az SAP NetWeaver konfigurálása az SAML használatával

1. Jelentkezzen be az SAP System szolgáltatásba, és lépjen a tranzakciós kód egy SAML2. Ekkor megnyílik az SAML konfigurációs képernyővel rendelkező új böngészőablak.

2. A megbízható identitás-szolgáltató (Azure AD) végpontjának konfigurálásához nyissa meg a **megbízható szolgáltatók** lapot.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_samlconfig.png)

3. Kattintson a **Hozzáadás** gombra, és válassza a **metaadatok feltöltése** elemet a helyi menüből.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_uploadmetadata.png)

4. Töltse fel a metaadat-fájlt, amelyet a Azure Portal letöltött.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_metadatafile.png)

5. A következő képernyőn írja be az alias nevét. Például aadsts, majd a folytatáshoz kattintson a **tovább** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_aliasname.png)

6. Győződjön meg arról, hogy a **kivonatoló algoritmusnak** **SHA-256** értékűnek kell lennie, és nem kell módosítania, majd nyomja meg a **tovább**gombot.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_identityprovider.png)

7. **Egyszeri bejelentkezési végpontokon**használja a **http post** elemet, majd a folytatáshoz kattintson a **tovább** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect.png)

8. Az **egyszeri** kijelentkezési végpontokon válassza a **HTTPRedirect** lehetőséget, majd a folytatáshoz kattintson a **tovább** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect1.png)

9. Az összetevő- **végpontokon**kattintson a Tovább gombra a folytatáshoz.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_artifactendpoint.png)

10. A **hitelesítési követelmények**területen kattintson a **Befejezés**gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_authentication.png)

11. Ugrás a lapon a **megbízható szolgáltató** > **identitás** -összevonása elemre (a képernyő aljáról). Kattintson a **Szerkesztés** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_trustedprovider.png)

12. Kattintson a **Hozzáadás** elemre az **identitás-összevonás** lapon (alsó ablak).

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_addidentityprovider.png)

13. Az előugró ablakban válassza a nem **meghatározott** lehetőséget a **támogatott NameID-formátumok** közül, majd kattintson az OK gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameid.png)

14. Vegye figyelembe, hogy a **felhasználói azonosító forrása** és a **felhasználói azonosító megfeleltetési módjának** értéke határozza meg az SAP-felhasználó és az Azure ad-jogcím közötti kapcsolatot.  

    #### <a name="scenario-sap-user-to-azure-ad-user-mapping"></a>Forgatókönyv: SAP-felhasználó és az Azure AD felhasználói leképezése.

    a. NameID részletei – képernyőfelvétel az SAP-ból.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/nameiddetails.png)

    b. Képernyőkép az Azure AD-ben szükséges jogcímek megemlítéséről.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/claimsaad1.png)

    #### <a name="scenario-select-sap-user-id-based-on-configured-email-address-in-su01-in-this-case-email-id-should-be-configured-in-su01-for-each-user-who-requires-sso"></a>Forgatókönyv: Az SAP felhasználói azonosító kiválasztása a konfigurált e-mail-cím alapján a SU01-ben. Ebben az esetben az e-mail-azonosítót a su01-ben kell konfigurálni minden olyan felhasználó esetében, aki egyszeri bejelentkezést igényel.

    a.  NameID részletei – képernyőfelvétel az SAP-ból.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameiddetails1.png)

    b. képernyőkép az Azure AD-ben szükséges jogcímek megemlítéséről.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/claimsaad2.png)

15. Kattintson a **Mentés** elemre, majd az **Engedélyezés** lehetőségre az Identitáskezelés engedélyezéséhez.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/configuration1.png)

16. Ha a rendszer kéri, kattintson **az OK** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/configuration2.png)

    ### <a name="create-sap-netweaver-test-user"></a>SAP NetWeaver-teszt felhasználó létrehozása

    Ebben a szakaszban egy B. Simon nevű felhasználót hoz létre az SAP NetWeaver-ben. Vegye fel a kapcsolatot a házon belüli SAP szakértői csapatával, vagy működjön együtt a szervezet SAP-partnerével, és vegye fel a felhasználókat az SAP NetWeaver platformon.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

1. Miután aktiválta az Azure AD-szolgáltatót, próbáljon meg hozzáférni az alábbi URL-címhez az egyszeri bejelentkezéshez (a Felhasználónév & jelszava nem jelenik meg)

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    (vagy) használja az alábbi URL-címet

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    > [!NOTE]
    > Cserélje le a sapurl-t a tényleges SAP állomásnévvel.

2. A fenti URL-címnek az alább említett képernyőre kell mutatnia. Ha elérheti az alábbi oldalt, az Azure AD SSO telepítése sikeresen befejeződött.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/testingsso.png)

3. Ha a Felhasználónév & a jelszó kérése, akkor a probléma diagnosztizálásához engedélyezze a nyomkövetést az alábbi URL-cím használatával

    `https://<sapurl>/sap/bc/webdynpro/sap/sec_diag_tool?sap-client=122&sap-language=EN#`

## <a name="configure-sap-netweaver-for-oauth"></a>Az OAuth SAP NetWeaver konfigurálása

1. Az SAP által dokumentált folyamat a következő helyen érhető el: [NetWeaver Gateway szolgáltatás engedélyezése és OAuth 2,0 hatókör létrehozása](https://wiki.scn.sap.com/wiki/display/Security/NetWeaver+Gateway+Service+Enabling+and+OAuth+2.0+Scope+Creation)

2. Nyissa meg a SPRO, és keresse meg az **aktiválási és karbantartási szolgáltatásokat**.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/oauth01.png)

3. Ebben a példában szeretnénk csatlakozni a OData szolgáltatáshoz: `DAAG_MNGGRP` a OAuth és az Azure ad SSO használatával. A műszaki szolgáltatás neve keresse meg a szolgáltatást `DAAG_MNGGRP` , és aktiválja, ha még nem aktív, már ( `green` keresse meg az állapotot az ICF-csomópontok lapon). Győződjön meg arról, hogy a rendszeralias (a csatlakoztatott háttérrendszer, ahol a szolgáltatás ténylegesen fut) helyes.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/oauth02.png)

    * Ezután kattintson a felső gomb sávján a nyomógomb **OAuth** elemre, és rendelje hozzá `scope` a (alapértelmezett név megtartása az elérhetőként) lehetőséget.

4. Példánkban a hatókört `DAAG_MNGGRP_001`a szolgáltatás neve alapján hozza létre a rendszer automatikusan egy szám hozzáadásával. A `/IWFND/R_OAUTH_SCOPES` jelentés használatával módosíthatja a hatókör nevét, vagy manuálisan is létrehozhatja azt.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/oauth03.png)

    > [!NOTE]
    > Üzenet `soft state status is not supported` – figyelmen kívül hagyható, mert nincs probléma. További részletekért tekintse [](https://help.sap.com/doc/saphelp_nw74/7.4.16/1e/c60c33be784846aad62716b4a1df39/content.htm?no_cache=true) meg a következőt:

### <a name="create-a-service-user-for-the-oauth-20-client"></a>Szolgáltatás felhasználójának létrehozása a OAuth 2,0-ügyfél számára

1. A OAuth2 a használatával szerezheti be a végfelhasználó hozzáférési jogkivonatát az nevében. `service ID` Fontos korlátozás a OAuth-kialakítással `OAuth 2.0 Client ID` : a-nek meg `username` kell egyeznie a OAuth 2,0 ügyfél által a hozzáférési jogkivonat kérésekor való bejelentkezéskor használt értékkel. Ezért a példánkban az ÜGYFÉL1 nevű OAuth 2,0-ügyfelet fogjuk regisztrálni, és az előfeltétel, hogy az azonos nevű (ÜGYFÉL1) felhasználónak léteznie kell az SAP-rendszeren, és hogy a felhasználó az említett alkalmazás által használt konfigurációt fogja használni. 

2. OAuth-ügyfél regisztrálásakor a `SAML Bearer Grant type`.

    >[!NOTE]
    >További részletekért tekintse meg a OAuth 2,0 ügyfél regisztrációját az SAML-tulajdonos [](https://wiki.scn.sap.com/wiki/display/Security/OAuth+2.0+Client+Registration+for+the+SAML+Bearer+Grant+Type) engedélyezési típusához.

3. tcod: SU01/hozza létre az ÜGYFÉL1 `System type` felhasználót, és rendelje hozzá a jelszót, mentse el a hitelesítő adatokat az API-programozó számára, akinek a felhasználónevével kell elírnia a felhasználónevet. Nincs hozzárendelve profil vagy szerepkör.

### <a name="register-the-new-oauth-20-client-id-with-the-creation-wizard"></a>Regisztrálja az új OAuth 2,0 ügyfél-azonosítót a létrehozás varázslóval

1. Új **OAuth 2,0 ügyfél** -indítási tranzakció **SOAUTH2**regisztrálása. A tranzakció áttekintést nyújt a már regisztrált OAuth 2,0-ügyfelekről. Válassza a **Létrehozás** lehetőséget, hogy elindítsa a varázslót az új OAuth-ügyfél nevű CLIENT1in ebben a példában.

2. Ugrás a T-Code-ra: **SOAUTH2** , és adja meg a leírást, majd kattintson a **tovább**gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/oauth04.png)

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/oauth05.png)

3. Válassza ki a már hozzáadott **egy saml2 identitásszolgáltató – Azure ad** elemet a legördülő listából, és mentse.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/oauth06.png)

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/oauth07.png)

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/oauth08.png)

4. A korábban létrehozott hatókör hozzáadásához kattintson a **Hozzáadás** a hatókör-hozzárendelés alatt lehetőségre:`DAAG_MNGGRP_001`

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/oauth09.png)

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/oauth10.png)

5. Kattintson a **Befejezés**gombra.

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Az SAP NetWeaver kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)