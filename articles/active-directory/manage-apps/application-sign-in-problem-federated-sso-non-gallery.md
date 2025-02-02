---
title: Problémák a nem gyűjteményes összevont egyszeri bejelentkezés alkalmazásba való bejelentkezéskor | Microsoft Docs
description: Útmutató az Azure AD-vel az SAML-alapú összevont egyszeri bejelentkezéshez konfigurált alkalmazásba való bejelentkezéskor felmerülő problémákhoz
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: d26f52db394877cff13bf4b0b476a9603c1ddba8
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/22/2019
ms.locfileid: "68381392"
---
# <a name="problems-signing-in-to-a-non-gallery-application-configured-for-federated-single-sign-on"></a>Problémák az összevont egyszeri bejelentkezéshez konfigurált, nem Gallery-alkalmazásba való bejelentkezéskor

Az alábbi bejelentkezési problémák elhárításához javasoljuk, hogy kövesse az alábbi javaslatokat, hogy jobb legyen a diagnosztika, és automatizálja a megoldás lépéseit:

- Telepítse a [saját alkalmazások biztonságos böngésző bővítményét](access-panel-extension-problem-installing.md) , hogy Azure Active Directory (Azure ad) jobb diagnosztikai és megoldási funkciókat nyújtson a Azure Portal tesztelési felületének használatakor.
- Reprodukálja a hibát a Azure Portal alkalmazás konfigurációja lapján található tesztelési élmény használatával. További információ az [SAML-alapú egyszeri bejelentkezési alkalmazások hibakereséséről](../develop/howto-v1-debug-saml-sso-issues.md)

## <a name="application-not-found-in-directory"></a>Az alkalmazás nem található a címtárban

*AADSTS70001 hiba: Az azonosítóval `https://contoso.com` rendelkező alkalmazás nem található a címtárban*.

**Lehetséges ok**

Az SAML-kérelemben az alkalmazásból az Azure AD-be küldött kiállítói attribútum nem egyezik az alkalmazásban az Azure AD-ben konfigurált azonosító értékkel.

**Felbontás**

Győződjön meg arról `Issuer` , hogy az SAML-kérelemben szereplő attribútum megegyezik az Azure ad-ben konfigurált azonosító értékkel. Ha a Azure Portal a saját alkalmazások biztonságos böngésző bővítménnyel használja a [tesztelési élményt](../develop/howto-v1-debug-saml-sso-issues.md) , nem kell manuálisan végrehajtania ezeket a lépéseket.

1. Nyissa meg a [**Azure Portal**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként** vagy **társ-** rendszergazdaként.

2. Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3. Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4. kattintson a **vállalati alkalmazások** lehetőségre a bal oldali navigációs menü Azure Active Directory.

5. Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

   * Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Az alkalmazások.**

6. Válassza ki azt az alkalmazást, amelyhez az egyszeri bejelentkezést konfigurálni kívánja.

7. Az alkalmazás betöltése után kattintson az **egyszeri bejelentkezésre** az alkalmazás bal oldali navigációs menüjében.

8. Miután az alkalmazás betöltött, nyissa meg az **SAML-alapkonfiguráció** szakaszt. Ellenőrizze, hogy az azonosító szövegmezőben szereplő érték megegyezik-e a hibában megjelenő azonosító értékével.

## <a name="the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application"></a>A válasz címe nem egyezik meg az alkalmazáshoz konfigurált Válaszcím-címekkel. 

*AADSTS50011 hiba: A válasz címe `https://contoso.com` nem egyezik az alkalmazáshoz konfigurált Válaszcím-címekkel.* 

**Lehetséges ok** 

Az SAML-kérelem AssertionConsumerServiceURL értéke nem felel meg az Azure AD-ben konfigurált válasz URL-értéknek vagy mintának. Az SAML-kérelemben szereplő AssertionConsumerServiceURL érték a hibaüzenetben látható URL-cím. 

**Felbontás** 

Győződjön meg arról `Issuer` , hogy az SAML-kérelemben szereplő attribútum megegyezik az Azure ad-ben konfigurált azonosító értékkel. Ha a Azure Portal a saját alkalmazások biztonságos böngésző bővítménnyel használja a [tesztelési élményt](../develop/howto-v1-debug-saml-sso-issues.md) , nem kell manuálisan végrehajtania ezeket a lépéseket.
 
1. Nyissa meg a [**Azure Portal**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként** vagy **társ-** rendszergazdaként. 

2. Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva. 

3. Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet. 

4. kattintson a **vállalati alkalmazások** lehetőségre a bal oldali navigációs menü Azure Active Directory. 

5. Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez. 

   * Ha nem látja a használni kívánt alkalmazást, használja a **minden alkalmazás lista** tetején található **szűrő** vezérlőelemet, és állítsa a **show (megjelenítés** ) lehetőséget az **összes alkalmazásra.**
  
6. Válassza ki az egyszeri bejelentkezést konfigurálni kívánt alkalmazást

7. Az alkalmazás betöltése után kattintson az **egyszeri bejelentkezésre** az alkalmazás bal oldali navigációs menüjében.

8. Miután az alkalmazás betöltött, nyissa meg az **SAML-alapkonfiguráció** szakaszt. Ellenőrizze vagy frissítse a válasz URL-címe szövegmezőben szereplő értéket, `AssertionConsumerServiceURL` hogy az megfeleljen az SAML-kérelemben szereplő értéknek.    
    
Miután frissítette a válasz URL-értékét az Azure AD-ben, és megfelel az alkalmazás által az SAML-kérelemben küldött értéknek, be kell tudnia jelentkezni az alkalmazásba.

## <a name="user-not-assigned-a-role"></a>A felhasználó nincs szerepkörhöz rendelve

*AADSTS50105 hiba: A bejelentkezett felhasználó `brian\@contoso.com` nincs hozzárendelve az alkalmazás egyik szerepköréhez sem*

**Lehetséges ok**

A felhasználó nem kapott hozzáférést az alkalmazáshoz az Azure AD-ben.

**Felbontás**

Egy vagy több felhasználó közvetlenül egy alkalmazáshoz való hozzárendeléséhez kövesse az alábbi lépéseket. Ha a Azure Portal a saját alkalmazások biztonságos böngésző bővítménnyel használja a [tesztelési élményt](../develop/howto-v1-debug-saml-sso-issues.md) , nem kell manuálisan végrehajtania ezeket a lépéseket.

1. Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2. Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3. Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4. kattintson a **vállalati alkalmazások** lehetőségre a bal oldali navigációs menü Azure Active Directory.

5. Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

   * Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Az alkalmazások.**

6. Válassza ki a kívánt rendelje hozzá egy felhasználót a listáról az alkalmazást.

7. Az alkalmazás betöltése után kattintson a **felhasználók és csoportok** elemre az alkalmazás bal oldali navigációs menüjében.

8. Kattintson a **Hozzáadás** a gombot a **felhasználók és csoportok** listán, megnyílik a **hozzárendelés hozzáadása** ablaktáblán.

9. Kattintson a **felhasználók és csoportok** a választó a **hozzárendelés hozzáadása** ablaktáblán.

10. Írja be a **teljes név** vagy **e-mail-cím** Önt érdeklő való hozzárendelése a felhasználó a **Keresés név vagy e-mail cím alapján** keresőmezőbe.

11. A kurzort a **felhasználói** megjelenítéséhez a listában egy **jelölőnégyzet**. Jelölje be a jelölőnégyzetet, a felhasználó profilfényképének vagy adja hozzá a felhasználót az embléma mellett a **kijelölt** listája.

12. **Választható** Ha **egynél több felhasználót**szeretne felvenni, írjon be egy másik **teljes nevet** vagy **e-mail-címet** a **Keresés név vagy e-mail-cím** Keresés mezőbe, és kattintson a jelölőnégyzetre a felhasználó a **kiválasztott** listához való hozzáadásához.

13. Amikor elkészült, válassza a felhasználók, kattintson a **kiválasztása** gombra kattintva vegye fel a listára a felhasználók és csoportok hozzá kell rendelni az alkalmazást.

14. **Választható lehetőség:** kattintson a **Szerepkörválasztás** -választójában jelenítse a **hozzárendelés hozzáadása** hozzárendelése a kiválasztott felhasználói szerepkör kiválasztása panel.

15. Kattintson a **hozzárendelése** gombra kattintva rendelje hozzá az alkalmazás a kijelölt felhasználók számára.

Után rövid idő alatt a kiválasztott felhasználók tudják elindítani ezeket az alkalmazásokat a megoldás leírása szakaszban leírt módszerek használatával.

## <a name="not-a-valid-saml-request"></a>Nem érvényes SAML-kérelem

*AADSTS75005 hiba: A kérelem nem érvényes egy saml2 protokoll-üzenet.*

**Lehetséges ok**

Az Azure AD nem támogatja az alkalmazás egyszeri bejelentkezésre vonatkozó SAML-kérelmét. Néhány gyakori probléma:

-   Az SAML-kérelemből hiányzó kötelező mezők

-   SAML-kérelem kódolási metódusa

**Felbontás**

1.  SAML-kérelem rögzítése. kövesse az [SAML-alapú egyszeri bejelentkezés az Azure ad](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging) -alkalmazásokban való hibakeresését ismertető oktatóanyagot, amelyből megtudhatja, hogyan rögzítheti az SAML-kérelmet.

2.  Vegye fel a kapcsolatot az alkalmazás gyártójával és megosztásával:

    -   SAML-kérelem

    -   [Az Azure AD egyszeri bejelentkezési SAML protokolljának követelményei](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)

Az alkalmazás gyártójának ellenőriznie kell, hogy az Azure AD SAML-implementációja támogatja-e az egyszeri bejelentkezést.

## <a name="misconfigured-application"></a>Hibásan konfigurált alkalmazás

*AADSTS650056 hiba: Helytelenül konfigurált alkalmazás. Ezt a következők egyike okozhatja: Az ügyfél nem sorolt fel semmilyen engedélyt a HRE Graph számára a kért engedélyekben az ügyfél alkalmazás-regisztrációjában. Vagy a rendszergazda nem járult hozzá a bérlőhöz. Vagy ellenőrizze az alkalmazás azonosítóját a kérelemben annak ellenőrzéséhez, hogy az megfelel-e a konfigurált ügyfélalkalmazás-azonosítónak. Kérje meg a rendszergazdát, hogy javítsa ki a konfigurációt vagy a beleegyezik a bérlő nevében.* .

**Lehetséges ok**

Az SAML-kérelemben az alkalmazásból az Azure ad-be elküldett attribútumnemegyezikazalkalmazáshozazAzuread-benkonfiguráltazonosítóértékkel.`Issuer`

**Felbontás**

Győződjön meg arról `Issuer` , hogy az SAML-kérelemben szereplő attribútum megegyezik az Azure ad-ben konfigurált azonosító értékkel. Ha a Azure Portal a saját alkalmazások biztonságos böngésző bővítménnyel használja a [tesztelési élményt](../develop/howto-v1-debug-saml-sso-issues.md) , nem kell manuálisan végrehajtania a következő lépéseket:

1.  Nyissa meg a [**Azure Portal**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként** vagy **társ-** rendszergazdaként.

1.  Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** lehetőség kiválasztásával.

1.  Írja be a **"Azure Active Directory"** kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.

1.  Válassza ki a **vállalati alkalmazásokat** a Azure Active Directory bal oldali navigációs menüjéből.

1.  A **minden alkalmazás** elemre kattintva megtekintheti az összes alkalmazás listáját.

    Ha nem látja a használni kívánt alkalmazást, használja a **minden alkalmazás lista** tetején található **szűrő** vezérlőelemet, és állítsa a **show (megjelenítés** ) lehetőséget az **összes alkalmazásra**.

1.  Válassza ki az egyszeri bejelentkezéshez konfigurálni kívánt alkalmazást.

1.  Miután az alkalmazás betöltött, nyissa meg az **SAML-alapkonfiguráció** szakaszt. Ellenőrizze, hogy az azonosító szövegmezőben szereplő érték megegyezik-e a hibában megjelenő azonosító értékével.

## <a name="certificate-or-key-not-configured"></a>Nincs konfigurálva a tanúsítvány vagy a kulcs

AADSTS50003 hiba: Nincs beállítva aláíró kulcs.

**Lehetséges ok**

Az Application objektum sérült, és az Azure AD nem ismeri fel az alkalmazáshoz konfigurált tanúsítványt.

**Felbontás**

Új tanúsítvány törléséhez és létrehozásához kövesse az alábbi lépéseket:

1. Nyissa meg a [**Azure Portal**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként** vagy **társ-** rendszergazdaként.

2. Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3. Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4. kattintson a **vállalati alkalmazások** lehetőségre a bal oldali navigációs menü Azure Active Directory.

5. Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

   * Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Az alkalmazások.**

6. Válassza ki azt az alkalmazást, amelyhez az egyszeri bejelentkezést konfigurálni kívánja.

7. Az alkalmazás betöltése után kattintson az **egyszeri bejelentkezésre** az alkalmazás bal oldali navigációs menüjében.

8. kattintson az **új tanúsítvány létrehozása** elemre az **SAML aláíró tanúsítvány** szakaszban.

9. Válassza ki a lejárati dátumot. Ezután kattintson a **Mentés gombra.**

10. Ellenőrizze, **hogy az új tanúsítvány aktív** -e az aktív tanúsítvány felülbírálásához. Ezután kattintson a panel tetején a **Mentés** gombra, és fogadja el a helyettesítő tanúsítvány aktiválását.

11. Az **SAML aláíró tanúsítvány** szakaszban kattintson az **Eltávolítás** gombra a fel nem **használt** tanúsítvány eltávolításához.

## <a name="saml-request-not-present-in-the-request"></a>Az SAML-kérelem nem szerepel a kérelemben

*AADSTS750054 hiba: A SAMLRequest vagy a SAMLResponse szolgáltatásnak lekérdezési karakterlánc paraméterként kell szerepelnie a HTTP-kérelemben az SAML-átirányítási kötéshez.*

**Lehetséges ok**

Az Azure AD nem tudta azonosítani az SAML-kérelmet a HTTP-kérelem URL-paraméterei között. Ez akkor fordulhat elő, ha az alkalmazás nem használ HTTP-átirányítási kötést az SAML-kérelem Azure AD-be való küldésekor.

**Felbontás**

Az alkalmazásnak HTTP-átirányítási kötés használatával kell elküldenie a Location fejlécbe kódolt SAML-kérelmet. Ennek megvalósításáról további információt [az SAML protokoll specifikációját tartalmazó dokumentumban](https://docs.oasis-open.org/security/saml/v2.0/saml-bindings-2.0-os.pdf) talál.

## <a name="azure-ad-is-sending-the-token-to-an-incorrect-endpoint"></a>Az Azure AD nem megfelelő végpontnak küldi a jogkivonatot.

**Lehetséges ok**

Az egyszeri bejelentkezés során, ha a bejelentkezési kérés nem tartalmaz explicit válasz URL-címet (a felhasználói szolgáltatás URL-címét), akkor az Azure AD kiválasztja az adott alkalmazáshoz konfigurált összes hivatkozhat URL-címet. Annak ellenére, hogy az alkalmazás explicit válasz URL-címmel van konfigurálva, a felhasználó átirányítható https://127.0.0.1:444. 

Amikor az alkalmazás hozzá lett adva nem katalógusbeli alkalmazásként, az Azure Active Directory ezt a válasz-URL-címet alapértelmezett értékként hozta létre. Ez a viselkedés azóta megváltozott, és az Azure Active Directory már nem adja hozzá ezt az URL-címet alapértelmezés szerint. 

**Felbontás**

Törölje az alkalmazáshoz konfigurált nem használt válasz URL-címeket.

1.  Nyissa meg a [**Azure Portal**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként** vagy **társ-** rendszergazdaként.

2.  Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** lehetőség kiválasztásával.

3.  Írja be a **"Azure Active Directory"** kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.

4.  Válassza ki a **vállalati alkalmazásokat** a Azure Active Directory bal oldali navigációs menüjéből.

5.  A **minden alkalmazás** elemre kattintva megtekintheti az összes alkalmazás listáját.

    Ha nem látja a használni kívánt alkalmazást, használja a **minden alkalmazás lista** tetején található **szűrő** vezérlőelemet, és állítsa a **show (megjelenítés** ) lehetőséget az **összes alkalmazásra**.

6.  Válassza ki az egyszeri bejelentkezéshez konfigurálni kívánt alkalmazást.

7.  Miután az alkalmazás betöltött, nyissa meg az **SAML-alapkonfiguráció** szakaszt. A **Válasz URL-címe (a fogyasztói szolgáltatás URL-címe)** területen törölje a rendszer által létrehozott nem használt vagy alapértelmezett válasz URL-címeket. Például: `https://127.0.0.1:444/applications/default.aspx`.



## <a name="problem-when-customizing-the-saml-claims-sent-to-an-application"></a>Probléma az alkalmazásnak eljuttatott SAML-jogcímek testreszabásakor

Ha meg szeretné tudni, hogyan szabhatja testre az alkalmazásnak eljuttatott SAML-attribútumok jogcímeit, tekintse meg a következő témakört: [jogcím-hozzárendelés Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping)

## <a name="next-steps"></a>További lépések
[Az Azure AD egyszeri bejelentkezési SAML protokolljának követelményei](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)
