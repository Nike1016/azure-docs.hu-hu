---
title: A OAuth 2,0 engedélyezési kód folyamatának megismerése az Azure AD-ben
description: Ez a cikk azt ismerteti, hogyan használhatók a HTTP-üzenetek a bérlőn található webalkalmazásokhoz és webes API-khoz való hozzáférés engedélyezéséhez Azure Active Directory és OAuth 2,0 használatával.
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/05/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 719939b393b01938a4d4faa41a5dca163b2a8949
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68834698"
---
# <a name="authorize-access-to-azure-active-directory-web-applications-using-the-oauth-20-code-grant-flow"></a>Hozzáférés engedélyezése Azure Active Directory webes alkalmazásokhoz az OAuth 2.0 kódengedélyezési folyamat használatával

Azure Active Directory (Azure AD) a OAuth 2,0 használatával engedélyezi az Azure AD-bérlőben található webalkalmazásokhoz és webes API-khoz való hozzáférés engedélyezését. Ez az útmutató a nyelvtől független, és leírja, hogyan küldhet és fogadhat HTTP-üzeneteket a [nyílt forráskódú kódtárak](active-directory-authentication-libraries.md)használata nélkül.

Az OAuth 2,0 engedélyezési kód folyamatát a [OAuth 2,0 specifikáció 4,1](https://tools.ietf.org/html/rfc6749#section-4.1). szakasza ismerteti. A hitelesítés és engedélyezés a legtöbb alkalmazás-típusban történik, beleértve a webalkalmazásokat és a natív módon telepített alkalmazásokat is.

[!INCLUDE [active-directory-protocols-getting-started](../../../includes/active-directory-protocols-getting-started.md)]

## <a name="oauth-20-authorization-flow"></a>OAuth 2,0 engedélyezési folyamat

Magas szinten az alkalmazás teljes engedélyezési folyamata a következőhöz hasonlóan néz ki:

![OAuth-hitelesítési kód folyamatábrája](./media/v1-protocols-oauth-code/active-directory-oauth-code-flow-native-app.png)

## <a name="request-an-authorization-code"></a>Engedélyezési kód kérése

Az engedélyezési kód folyamata azzal kezdődik, hogy az ügyfél átirányítja `/authorize` a felhasználót a végpontra. Ebben a kérelemben az ügyfél jelzi a felhasználótól beszerzett engedélyeket. A bérlőhöz tartozó OAuth 2,0 engedélyezési végpontot úgy érheti el, ha kijelöli **Alkalmazásregisztrációk > végpontot** a Azure Portal.

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%3A12345
&response_mode=query
&resource=https%3A%2F%2Fservice.contoso.com%2F
&state=12345
```

| Paraméter |  | Leírás |
| --- | --- | --- |
| tenant |szükséges |A kérelem elérési útjának értékehasználhatóannakszabályozására,hogykijelentkezhetbeazalkalmazásba.`{tenant}` Az engedélyezett értékek a bérlői azonosítók, például `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` `common` vagy `contoso.onmicrosoft.com` a bérlői független tokenek esetében. |
| client_id |szükséges |Az alkalmazáshoz hozzárendelt, az Azure AD-vel regisztrált alkalmazás azonosítója. Ez az Azure Portalon található. Kattintson **Azure Active Directory** a szolgáltatások oldalsávban, majd a **Alkalmazásregisztrációk**elemre, és válassza ki az alkalmazást. |
| response_type |szükséges |Tartalmaznia `code` kell az engedélyezési kód folyamatát. |
| redirect_uri |ajánlott |Az alkalmazás redirect_uri, ahol a hitelesítési válaszokat el lehet juttatni és fogadni tudja az alkalmazás. Pontosan egyeznie kell a portálon regisztrált redirect_uris, kivéve, ha az URL-címet kódolni kell. A natív & Mobile apps esetében az alapértelmezett értéket `urn:ietf:wg:oauth:2.0:oob`kell használnia. |
| response_mode |választható |Meghatározza azt a módszert, amelyet az eredményül kapott jogkivonat az alkalmazásba való visszaküldéséhez kell használni. `query`Lehet, `fragment`, vagy `form_post`. `query`a kódot lekérdezési karakterlánc paraméterként adja meg az átirányítási URI-n. Ha az implicit folyamat használatával kér azonosító jogkivonatot, az `query` [OpenID specifikációban](https://openid.net/specs/oauth-v2-multiple-response-types-1_0.html#Combinations)megadott módon nem használható. Ha csak a kódot kéri, használhatja `query` `fragment`a, vagy `form_post`a-t is. `form_post`végrehajt egy BEJEGYZÉST, amely tartalmazza a kódot az átirányítási URI-nak. Az alapértelmezett `query` érték egy programkód folyamata.  |
| state |ajánlott |A kérelemben szereplő, a jogkivonat-válaszban visszaadott érték. A véletlenszerűen generált egyedi érték általában a [helyek közötti kérelmek hamisításának megelőzésére](https://tools.ietf.org/html/rfc6749#section-10.12)szolgál. Az állapot az alkalmazásban a felhasználó állapotára vonatkozó információk kódolására is használatos, mielőtt a hitelesítési kérelem bekövetkezett volna, például az oldal vagy a megtekintés. |
| resource | ajánlott |A célként szolgáló webes API (biztonságos erőforrás) alkalmazás-azonosító URI-ja. Az alkalmazás-azonosító URI azonosítójának megkereséséhez az Azure Portalon kattintson a **Azure Active Directory**elemre, majd az **alkalmazás regisztrációja**elemre, nyissa meg az alkalmazás **beállításait** tartalmazó lapot, majd kattintson a **Tulajdonságok**elemre. Egy külső erőforrás is lehet, például `https://graph.microsoft.com`:. Erre az engedélyezési vagy a jogkivonat-kérelmek egyikében van szükség. Annak biztosítása érdekében, hogy kevesebb hitelesítési kérdés kerüljön az engedélyezési kérelembe annak biztosításához, hogy a felhasználó elfogadja a jóváhagyást. |
| scope | **figyelmen kívül hagyja** | A v1 Azure AD-alkalmazások esetében a hatóköröket statikusan kell konfigurálni az Azure Portalon az alkalmazások **beállításai**, a **szükséges engedélyek**területen. |
| gyors |választható |Adja meg a szükséges felhasználói interakció típusát.<p> Érvényes értékek a következők: <p> *Bejelentkezés*: A felhasználónak meg kell adnia az ismételt hitelesítést. <p> *select_account*: A rendszer megkéri a felhasználót, hogy válasszon egy fiókot, és szakítsa meg az egyszeri bejelentkezést. A felhasználó kiválaszthat egy meglévő bejelentkezett fiókot, megadhatja a megjegyzett fiók hitelesítő adatait, vagy választhat, hogy egy másik fiókot is használ-e. <p> *beleegyezik*: A felhasználó beleegyezik, de frissíteni kell. A felhasználónak meg kell adnia a hozzájárulásukat. <p> *admin_consent*: A rendszergazdának meg kell adnia a hozzájárulásukat a szervezet összes felhasználója nevében |
| login_hint |választható |A használatával előre kitöltheti a felhasználó bejelentkezési oldalának username/e-mail címe mezőjét, ha már ismeri a felhasználónevét. Az alkalmazások gyakran használják ezt a paramétert az újrahitelesítés során, miután már kibontotta a felhasználónevet egy `preferred_username` korábbi bejelentkezésből a jogcím használatával. |
| domain_hint |választható |A felhasználó által a bejelentkezéshez használt bérlőről vagy tartományról nyújt be egy tippet. A domain_hint értéke a bérlő regisztrált tartománya. Ha a bérlőt egy helyszíni címtárba összevonták, a HRE átirányítja a megadott bérlői összevonási kiszolgálóra. |
| code_challenge_method | ajánlott    | A `code_verifier` `code_challenge` paraméter kódolásához használt metódus. A `plain` (vagy `S256`) egyike lehet. Ha a szolgáltatás ki `code_challenge` van zárva, a rendszer azt feltételezi, hogy egyszerű szöveg, ha `code_challenge` szerepel benne. Az Azure HRE 1.0-s `plain` verziójában a és a `S256`is támogatott. További információ: [PKCE RFC](https://tools.ietf.org/html/rfc7636). |
| code_challenge        | ajánlott    | Az engedélyezési kód támogatásának biztosítására szolgál a Code Exchange (PKCE) számára a natív vagy nyilvános ügyfélről. Kötelező, `code_challenge_method` ha szerepel benne. További információ: [PKCE RFC](https://tools.ietf.org/html/rfc7636). |

> [!NOTE]
> Ha a felhasználó egy szervezet tagja, akkor a szervezet rendszergazdája beleegyezik vagy elutasíthatja a felhasználó nevében, vagy engedélyezheti a felhasználónak az engedélyezését. A felhasználó csak akkor kapja meg a hozzájárulásukat, ha a rendszergazda engedélyezi azt.
>
>

Ezen a ponton a felhasználónak meg kell adnia a hitelesítő adatait, és hozzá kell járulnia az alkalmazás által az Azure Portalon kért engedélyekhez. Miután a felhasználó elvégezte a hitelesítést, és megadta a hozzájárulást, az Azure ad `redirect_uri` választ küld az alkalmazásnak a kérelemben szereplő címen a kóddal.

### <a name="successful-response"></a>Sikeres válasz
A sikeres válasz így néz ki:

```
GET  HTTP/1.1 302 Found
Location: http://localhost:12345/?code= AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF&state=D79E5777-702E-4260-9A62-37F75FF22CCE
```

| Paraméter | Leírás |
| --- | --- |
| admin_consent |Az érték TRUE (igaz), ha egy rendszergazda beleegyezett egy hozzájárulási kérelemre vonatkozó felszólításba. |
| code |Az alkalmazás által kért engedélyezési kód. Az alkalmazás az engedélyezési kóddal kérhet hozzáférési jogkivonatot a célként megadott erőforráshoz. |
| session_state |Egy egyedi érték, amely az aktuális felhasználói munkamenetet azonosítja. Ez az érték egy GUID azonosító, de a vizsgálat nélkül átadott átlátszatlan értékként kell kezelni. |
| state |Ha a kérelemben szerepel egy State paraméter, akkor a válaszban ugyanazt az értéket kell megjelennie. Célszerű az alkalmazásnak meggyőződnie arról, hogy a kérelemben és a válaszban lévő állapot értékek megegyeznek a válasz használata előtt. Ez segít felderíteni a [helyek közötti kérelmek hamisításának (CSRF)](https://tools.ietf.org/html/rfc6749#section-10.12) elleni támadásait az ügyfélen. |

### <a name="error-response"></a>Hiba válasza
A rendszer a `redirect_uri` hibaüzeneteket is elküldheti, hogy az alkalmazás megfelelően tudja kezelni őket.

```
GET http://localhost:12345/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Paraméter | Leírás |
| --- | --- |
| hiba |A [OAuth 2,0 engedélyezési keretrendszer](https://tools.ietf.org/html/rfc6749)5,2. szakasza határozza meg a hibakód értékét. A következő táblázat azokat a hibakódokat ismerteti, amelyeket az Azure AD ad vissza. |
| error_description |A hiba részletesebb leírása. Ez az üzenet nem a végfelhasználók számára készült. |
| state |Az állapot értéke egy véletlenszerűen generált, nem újrafelhasznált érték, amelyet a rendszer a kérelemben küld, és a válaszban visszaadja, hogy megakadályozza a helyek közötti kérelmek hamisításának (CSRF) támadásait. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Az engedélyezési végpont hibáinak kódjai
Az alábbi táblázat azokat a hibakódokat ismerteti, amelyeket a rendszer a hiba `error` válaszának paraméterében adhat vissza.

| Hibakód | Leírás | Ügyfél művelete |
| --- | --- | --- |
| invalid_request |Protokollhiba, például hiányzó kötelező paraméter. |Javítsa ki és küldje el újra a kérelmet. Ez egy fejlesztési hiba, és általában a kezdeti tesztelés során történik. |
| unauthorized_client |Az ügyfélalkalmazás nem jogosult engedélyezési kód igénylésére. |Ez általában akkor fordul elő, ha az ügyfélalkalmazás nincs regisztrálva az Azure AD-ben, vagy nem kerül be a felhasználó Azure AD-bérlőbe. Az alkalmazás arra kéri a felhasználót, hogy telepítse az alkalmazást, és hozzáadja azt az Azure AD-hez. |
| access_denied |Erőforrás-tulajdonos által megtagadott jóváhagyás |Az ügyfélalkalmazás értesítheti a felhasználót, hogy nem tud továbblépni, kivéve, ha a felhasználó beleegyezett. |
| unsupported_response_type |Az engedélyezési kiszolgáló nem támogatja a válasz típusát a kérelemben. |Javítsa ki és küldje el újra a kérelmet. Ez egy fejlesztési hiba, és általában a kezdeti tesztelés során történik. |
| server_error |A kiszolgáló váratlan hibát észlelt. |Próbálja megismételni a kérelmet. Ezek a hibák ideiglenes feltételekből származhatnak. Előfordulhat, hogy az ügyfélalkalmazás egy ideiglenes hiba miatt késlelteti a felhasználót, hogy a válasza késik. |
| temporarily_unavailable |A kiszolgáló átmenetileg túl elfoglalt a kérelem kezeléséhez. |Próbálja megismételni a kérelmet. Előfordulhat, hogy az ügyfélalkalmazás megmagyarázza a felhasználót, hogy a válasza egy ideiglenes feltétel miatt késik. |
| invalid_resource |A célként megadott erőforrás érvénytelen, mert nem létezik, az Azure AD nem találja, vagy helytelenül van konfigurálva. |Ez azt jelzi, hogy az erőforrás (ha létezik) nem lett konfigurálva a bérlőben. Az alkalmazás arra kéri a felhasználót, hogy telepítse az alkalmazást, és hozzáadja azt az Azure AD-hez. |

## <a name="use-the-authorization-code-to-request-an-access-token"></a>Hozzáférési jogkivonat igénylése az engedélyezési kóddal
Most, hogy beszerzett egy engedélyezési kódot, és engedélyt kapott a felhasználó, a hozzáférési token kódját beválthatja a kívánt erőforrásba egy post-kérelem küldésével a `/token` végpontra:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded
grant_type=authorization_code
&client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA
&redirect_uri=https%3A%2F%2Flocalhost%3A12345
&resource=https%3A%2F%2Fservice.contoso.com%2F
&client_secret=p@ssw0rd

//NOTE: client_secret only required for web apps
```

| Paraméter |  | Leírás |
| --- | --- | --- |
| tenant |szükséges |A kérelem elérési útjának értékehasználhatóannakszabályozására,hogykijelentkezhetbeazalkalmazásba.`{tenant}` Az engedélyezett értékek a bérlői azonosítók, például `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` `common` vagy `contoso.onmicrosoft.com` a bérlői független tokenek esetében. |
| client_id |szükséges |Az alkalmazáshoz hozzárendelt, az Azure AD-vel regisztrált alkalmazás azonosítója. Ezt a Azure Portalban találja. Az alkalmazás-azonosító az alkalmazás regisztrációjának beállításaiban jelenik meg. |
| grant_type |szükséges |Az engedélyezési `authorization_code` kód folyamatábrájának kell lennie. |
| code |szükséges |Az `authorization_code` előző szakaszban beszerzett |
| redirect_uri |szükséges | Az `redirect_uri`ügyfélalkalmazás regisztrálva van. |
| client_secret |a webalkalmazásokhoz szükséges, a nyilvános ügyfelek számára nem engedélyezett |A **kulcsok**területen az alkalmazáshoz az Azure Portalon létrehozott alkalmazás titka. Nem használható natív alkalmazásban (nyilvános ügyfél), mert a client_secrets nem lehet megbízhatóan tárolni az eszközökön. A webalkalmazások és webes API-k (az összes bizalmas ügyfél) számára szükséges, amelyekkel `client_secret` biztonságosan tárolhatók a kiszolgáló oldalán. A client_secret a küldés előtt az URL-kódolásnak kell lennie. |
| resource | ajánlott |A célként szolgáló webes API (biztonságos erőforrás) alkalmazás-azonosító URI-ja. Az alkalmazás-azonosító URI azonosítójának megkereséséhez az Azure Portalon kattintson a **Azure Active Directory**elemre, majd az **alkalmazás regisztrációja**elemre, nyissa meg az alkalmazás **beállításait** tartalmazó lapot, majd kattintson a **Tulajdonságok**elemre. Egy külső erőforrás is lehet, például `https://graph.microsoft.com`:. Erre az engedélyezési vagy a jogkivonat-kérelmek egyikében van szükség. Annak biztosítása érdekében, hogy kevesebb hitelesítési kérdés kerüljön az engedélyezési kérelembe annak biztosításához, hogy a felhasználó elfogadja a jóváhagyást. Ha az engedélyezési kérelemben és a jogkivonat-kérelemben is szerepel, akkor az erőforrás paramétereinek egyezniük kell. | 
| code_verifier | választható | A authorization_code beszerzéséhez használt code_verifier. Kötelező, ha a PKCE az engedélyezési kód Grant kérelmében használták. További információ: [PKCE RFC](https://tools.ietf.org/html/rfc7636)   |

Az alkalmazás-azonosító URI azonosítójának megkereséséhez az Azure Portalon kattintson a **Azure Active Directory**elemre, majd az **alkalmazás regisztrációja**elemre, nyissa meg az alkalmazás **beállításait** tartalmazó lapot, majd kattintson a **Tulajdonságok**elemre.

### <a name="successful-response"></a>Sikeres válasz
Az Azure AD egy [hozzáférési](access-tokens.md) jogkivonatot ad vissza sikeres válasz esetén. Az ügyfélalkalmazás és a hozzájuk kapcsolódó késések hálózati hívásának minimalizálásához az ügyfélalkalmazás a OAuth 2,0 válaszban megadott jogkivonat-élettartamhoz tartozó hozzáférési jogkivonatokat gyorsítótárazza. A jogkivonat élettartamának meghatározásához használja a vagy `expires_in` `expires_on` a paraméter értékét.

Ha egy webes API-erőforrás `invalid_token` hibakódot ad vissza, akkor ez arra utalhat, hogy az erőforrás azt állapította meg, hogy a jogkivonat lejárt. Ha az ügyfél és az erőforrás órajelének időpontja eltérő (azaz "időeltérés"), akkor előfordulhat, hogy az erőforrás a jogkivonatot az ügyfél-gyorsítótárból való törlés előtt megtekinti. Ha ez történik, törölje a tokent a gyorsítótárból, még akkor is, ha még a számított élettartamán belül van.

A sikeres válasz így néz ki:

```
{
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ",
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1388444763",
  "resource": "https://service.contoso.com/",
  "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4rTfgV29ghDOHRc2B-C_hHeJaJICqjZ3mY2b_YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfcUl4VBbiSHZyd1NVZG5QTIOcbObu3qnLutbpadZGAxqjIbMkQ2bQS09fTrjMBtDE3D6kSMIodpCecoANon9b0LATkpitimVCrl-NyfN3oyG4ZCWu18M9-vEou4Sq-1oMDzExgAf61noxzkNiaTecM-Ve5cq6wHqYQjfV9DOz4lbceuYCAA",
  "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
  "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83ZmU4MTQ0Ny1kYTU3LTQzODUtYmVjYi02ZGU1N2YyMTQ3N2UvIiwiaWF0IjoxMzg4NDQwODYzLCJuYmYiOjEzODg0NDA4NjMsImV4cCI6MTM4ODQ0NDc2MywidmVyIjoiMS4wIiwidGlkIjoiN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlIiwib2lkIjoiNjgzODlhZTItNjJmYS00YjE4LTkxZmUtNTNkZDEwOWQ3NGY1IiwidXBuIjoiZnJhbmttQGNvbnRvc28uY29tIiwidW5pcXVlX25hbWUiOiJmcmFua21AY29udG9zby5jb20iLCJzdWIiOiJKV3ZZZENXUGhobHBTMVpzZjd5WVV4U2hVd3RVbTV5elBtd18talgzZkhZIiwiZmFtaWx5X25hbWUiOiJNaWxsZXIiLCJnaXZlbl9uYW1lIjoiRnJhbmsifQ."
}

```

| Paraméter | Leírás |
| --- | --- |
| access_token |A kért [hozzáférési jogkivonat](access-tokens.md) aláírt JSON web token (JWT). Az alkalmazás használhatja ezt a tokent a biztonságos erőforráshoz, például egy webes API-hoz való hitelesítéshez. |
| token_type |Megadja a jogkivonat típusának értékét. Az Azure AD által támogatott egyetlen típus a tulajdonos. A tulajdonosi jogkivonatokkal kapcsolatos további információkért lásd [: OAuth 2.0 engedélyezési keretrendszer: Tulajdonosi jogkivonat használata (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt) |
| expires_in |A hozzáférési jogkivonat érvényességi ideje (másodpercben). |
| expires_on |A hozzáférési jogkivonat lejáratának időpontja. A dátum az 1970-01-01T0:0: 0Z UTC számú másodperc, a lejárati időpontig. Ez az érték a gyorsítótárazott tokenek élettartamának meghatározására szolgál. |
| resource |A webes API alkalmazás-azonosító URI-ja (biztonságos erőforrás). |
| scope |Az ügyfélalkalmazás számára megadott megszemélyesítési engedélyek. Az alapértelmezett engedély `user_impersonation`:. A biztonságos erőforrás tulajdonosa további értékeket is regisztrálhat az Azure AD-ben. |
| refresh_token |Egy OAuth 2,0 frissítési token. Az alkalmazás az aktuális hozzáférési jogkivonat lejárta után további hozzáférési jogkivonatok beszerzésére használhatja ezt a tokent. A frissítési tokenek hosszú életűek, és az erőforrásokhoz való hozzáférés hosszabb ideig is használhatók. |
| id_token |Egy [azonosító](id-tokens.md)jogkivonatot jelölő aláíratlan JSON web token (JWT). Az alkalmazás képes base64Url dekódolni a token szegmenseit, hogy információkat kérjen a bejelentkezett felhasználóval kapcsolatban. Az alkalmazás gyorsítótárazhatja az értékeket, és megjelenítheti őket, de nem hivatkozhat rájuk az engedélyezési vagy biztonsági határokra. |

A JSON webes jogkivonatokkal kapcsolatos további információkért tekintse meg a [JWT IETF draft specifikációját](https://go.microsoft.com/fwlink/?LinkId=392344).   További információ `id_tokens`: [v 1.0 OpenID Connect flow](v1-protocols-openid-connect-code.md).

### <a name="error-response"></a>Hiba válasza
A jogkivonat-kiállítási végpont hibái HTTP-hibakódok, mert az ügyfél közvetlenül hívja meg a jogkivonat-kiállítási végpontot. A HTTP-állapotkód mellett az Azure AD jogkivonat-kiállítási végpont is egy JSON-dokumentumot ad vissza, amely a hibát leíró objektumokat tartalmazza.

A példaként kapott hiba a következőhöz hasonló:

```
{
  "error": "invalid_grant",
  "error_description": "AADSTS70002: Error validating credentials. AADSTS70008: The provided authorization code or refresh token is expired. Send a new interactive authorization request for this user and resource.\r\nTrace ID: 3939d04c-d7ba-42bf-9cb7-1e5854cdce9e\r\nCorrelation ID: a8125194-2dc8-4078-90ba-7b6592a7f231\r\nTimestamp: 2016-04-11 18:00:12Z",
  "error_codes": [
    70002,
    70008
  ],
  "timestamp": "2016-04-11 18:00:12Z",
  "trace_id": "3939d04c-d7ba-42bf-9cb7-1e5854cdce9e",
  "correlation_id": "a8125194-2dc8-4078-90ba-7b6592a7f231"
}
```
| Paraméter | Leírás |
| --- | --- |
| hiba |Hibakód-karakterlánc, amely a felmerülő hibák típusának besorolására használható, és felhasználható a hibákra való reagálásra. |
| error_description |Egy adott hibaüzenet, amely segítséget nyújt a fejlesztőknek a hitelesítési hiba kiváltó okának azonosításában. |
| error_codes |Az STS-specifikus hibakódok listája, amelyek segíthetnek a diagnosztikaben. |
| timestamp |Az az idő, amikor a hiba bekövetkezett. |
| trace_id |Az a kérelem egyedi azonosítója, amely segíthet a diagnosztikaben. |
| correlation_id |A kérelem egyedi azonosítója, amely segíthet az összetevők diagnosztizálásában. |

#### <a name="http-status-codes"></a>HTTP-állapotkódok
A következő táblázat felsorolja azokat a HTTP-állapotkódok listáját, amelyeket a jogkivonat-kiállítási végpont visszaad. Bizonyos esetekben a hibakód elegendő a válasz leírásához, de ha hibákat észlel, elemezni kell a csatolt JSON-dokumentumot, és meg kell vizsgálnia a hibakódját.

| HTTP-kód | Leírás |
| --- | --- |
| 400 |Alapértelmezett HTTP-kód. A legtöbb esetben használatos, és általában egy helytelenül formázott kérelem okozza. Javítsa ki és küldje el újra a kérelmet. |
| 401 |A hitelesítés sikertelen volt. Például a kérelemből hiányzik a client_secret paraméter. |
| 403 |A hitelesítés sikertelen. Például a felhasználónak nincs engedélye az erőforrás elérésére. |
| 500 |Belső hiba történt a szolgáltatásban. Próbálja megismételni a kérelmet. |

#### <a name="error-codes-for-token-endpoint-errors"></a>Hibakódok jogkivonat-végponti hibákhoz
| Hibakód | Leírás | Ügyfél művelete |
| --- | --- | --- |
| invalid_request |Protokollhiba, például hiányzó kötelező paraméter. |A kérelem javítása és újraküldése |
| invalid_grant |Az engedélyezési kód érvénytelen vagy lejárt. |Új kérelem kipróbálása a `/authorize` végpontra |
| unauthorized_client |A hitelesített ügyfél nem jogosult az engedélyezési támogatás típusának használatára. |Ez általában akkor fordul elő, ha az ügyfélalkalmazás nincs regisztrálva az Azure AD-ben, vagy nem kerül be a felhasználó Azure AD-bérlőbe. Az alkalmazás arra kéri a felhasználót, hogy telepítse az alkalmazást, és hozzáadja azt az Azure AD-hez. |
| invalid_client |Nem sikerült az ügyfél-hitelesítés. |Az ügyfél hitelesítő adatai nem érvényesek. A javításhoz az alkalmazás rendszergazdája frissíti a hitelesítő adatokat. |
| unsupported_grant_type |Az engedélyezési kiszolgáló nem támogatja az engedélyezési engedély típusát. |Módosítsa a kérelemben szereplő Grant típust. Ez a típusú hiba csak a fejlesztés során fordul elő, és a rendszer a kezdeti tesztelés során észleli. |
| invalid_resource |A célként megadott erőforrás érvénytelen, mert nem létezik, az Azure AD nem találja, vagy helytelenül van konfigurálva. |Ez azt jelzi, hogy az erőforrás (ha létezik) nem lett konfigurálva a bérlőben. Az alkalmazás arra kéri a felhasználót, hogy telepítse az alkalmazást, és hozzáadja azt az Azure AD-hez. |
| interaction_required |A kérés felhasználói beavatkozást igényel. Például további hitelesítési lépésre van szükség. | Nem interaktív kérelem helyett próbálkozzon újra egy interaktív engedélyezési kéréssel ugyanarra az erőforrásra. |
| temporarily_unavailable |A kiszolgáló átmenetileg túl elfoglalt a kérelem kezeléséhez. |Próbálja megismételni a kérelmet. Előfordulhat, hogy az ügyfélalkalmazás megmagyarázza a felhasználót, hogy a válasza egy ideiglenes feltétel miatt késik. |

## <a name="use-the-access-token-to-access-the-resource"></a>Az erőforrás eléréséhez használja a hozzáférési jogkivonatot.
Most `access_token`, hogy sikeresen megszerezte a-t, használhatja a jogkivonatot a webes API-khoz a kérelmekben `Authorization` , a fejlécben szereplővel együtt. Az [RFC 6750](https://www.rfc-editor.org/rfc/rfc6750.txt) specifikáció ismerteti, hogyan használhatók a tulajdonosi jogkivonatok a http-kérelmekben a védett erőforrások eléréséhez.

### <a name="sample-request"></a>Mintakérelem
```
GET /data HTTP/1.1
Host: service.contoso.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ
```

### <a name="error-response"></a>Hiba válasza
Az RFC 6750-t implementáló védett erőforrások HTTP-állapotkódot adnak ki. Ha a kérelem nem tartalmazza a hitelesítő adatokat, vagy hiányzik belőle a jogkivonat, a válasz `WWW-Authenticate` fejlécet tartalmaz. Ha egy kérelem meghiúsul, az erőforrás-kiszolgáló a HTTP-állapotkódot és egy hibakódot válaszol.

Az alábbi példa egy sikertelen választ mutat be, ha az ügyfél kérelme nem tartalmazza a tulajdonosi jogkivonatot:

```
HTTP/1.1 401 Unauthorized
WWW-Authenticate: Bearer authorization_uri="https://login.microsoftonline.com/contoso.com/oauth2/authorize",  error="invalid_token",  error_description="The access token is missing.",
```

#### <a name="error-parameters"></a>Hiba paraméterei
| Paraméter | Leírás |
| --- | --- |
| authorization_uri |Az engedélyezési kiszolgáló URI-ja (fizikai végpontja). Ezt az értéket keresési kulcsként is használja a rendszer, ha további információt szeretne kapni a kiszolgálóról egy felderítési végpontról. <p><p> Az ügyfélnek ellenőriznie kell, hogy az engedélyezési kiszolgáló megbízható-e. Ha az erőforrást az Azure ad védi, elegendő annak ellenőrzése, hogy az URL-cím kezdődik https://login.microsoftonline.com -e, vagy egy másik, az Azure ad által támogatott állomásnév. A bérlő-specifikus erőforrásoknak mindig egy bérlőre vonatkozó engedélyezési URI-t kell visszaadniuk. |
| hiba |A [OAuth 2,0 engedélyezési keretrendszer](https://tools.ietf.org/html/rfc6749)5,2. szakasza határozza meg a hibakód értékét. |
| error_description |A hiba részletesebb leírása. Ez az üzenet nem a végfelhasználók számára készült. |
| resource_id |Az erőforrás egyedi azonosítóját adja vissza. Az ügyfélalkalmazás ezt az azonosítót használhatja a `resource` paraméter értékeként, amikor jogkivonatot kér az erőforráshoz. <p><p> Fontos, hogy az ügyfélalkalmazás ellenőrizze ezt az értéket, ellenkező esetben előfordulhat, hogy egy rosszindulatú szolgáltatás megemelt jogosultsági **szintű** támadást tud kiváltani <p><p> A támadás megelőzésére javasolt stratégia annak ellenőrzése, hogy az `resource_id` megfelel-e a webes API URL-címének, amelyhez hozzáfér. Ha https://service.contoso.com/data például a hozzáférése folyamatban van, a `resource_id` lehet htttps://Service.contoso.com/. Az ügyfélalkalmazásnak el kell utasítania egy olyant `resource_id` , amely nem az alap URL-címmel kezdődik, kivéve, ha az azonosító ellenőrzésének megbízható alternatív módja van. |

#### <a name="bearer-scheme-error-codes"></a>Tulajdonosi séma hibakódai
Az RFC 6750 specifikációja a következő hibákat definiálja a WWW-Authenticate fejlécet és a tulajdonosi sémát a válaszban használó erőforrásokhoz.

| HTTP-állapotkód | Hibakód | Leírás | Ügyfél művelete |
| --- | --- | --- | --- |
| 400 |invalid_request |A kérés nem megfelelően formázott. Előfordulhat például, hogy hiányzik egy paraméter, vagy kétszer ugyanazt a paramétert használja. |Javítsa ki a hibát, majd próbálja megismételni a kérelmet. Ezt a típusú hibát csak a fejlesztés során, a kezdeti tesztelés során kell észlelni. |
| 401 |invalid_token |A hozzáférési jogkivonat hiányzik, érvénytelen vagy vissza lett vonva. A error_description paraméter értéke további részleteket tartalmaz. |Igényeljen új jogkivonatot az engedélyezési kiszolgálóról. Ha az új jogkivonat meghiúsul, váratlan hiba történt. Küldjön egy hibaüzenetet a felhasználónak, és próbálkozzon újra a véletlenszerű késések után. |
| 403 |insufficient_scope |A hozzáférési jogkivonat nem tartalmazza az erőforrás eléréséhez szükséges megszemélyesítési engedélyeket. |Új engedélyezési kérelem küldése az engedélyezési végpontnak. Ha a válasz tartalmazza a hatókör-paramétert, használja a kérelemben szereplő hatókör értéket az erőforráshoz. |
| 403 |insufficient_access |A jogkivonat tárgya nem rendelkezik az erőforrás eléréséhez szükséges engedélyekkel. |Kérje meg a felhasználót, hogy használjon másik fiókot, vagy kérjen engedélyeket a megadott erőforrásnak. |

## <a name="refreshing-the-access-tokens"></a>Hozzáférési tokenek frissítése

A hozzáférési jogkivonatok rövid életűek, és a lejáratuk után frissíteni kell az erőforrások elérésének folytatásához. Frissítheti `access_token` a-t egy másik `POST` kérelem küldésével a `/token` végpontra, `code`de `refresh_token` ezúttal a helyett.  A frissítési jogkivonatok érvényesek minden olyan erőforrásra, amelyhez az ügyfélnek már hozzáférése van, így a kérelemre `resource=https://graph.microsoft.com` kiadott frissítési jogkivonat felhasználható új hozzáférési `resource=https://contoso.com/api`jogkivonat igénylésére. 

A frissítési tokenek nem rendelkeznek megadott élettartammal. A frissítési tokenek élettartama általában viszonylag hosszú. Bizonyos esetekben azonban a frissítési tokenek lejárnak, visszavonásra kerülnek, vagy hiányoznak a megfelelő jogosultságok a kívánt művelethez. Az alkalmazásnak megfelelően kell elvárnia és kezelnie a jogkivonat-kiállítási végpont által visszaadott hibákat.

Ha a frissítési jogkivonat hibája miatt választ kap, elveti az aktuális frissítési jogkivonatot, és új engedélyezési kódot vagy hozzáférési jogkivonatot kér. Különösen, ha frissítési tokent használ az engedélyezési kód megadásakor, ha a vagy `interaction_required` `invalid_grant` a hibakódot választ kap, elveti a frissítési tokent, és új engedélyezési kódot kér.

Egy példa a bérlőre **jellemző** végpontra (a **közös** végpontot is használhatja) egy új hozzáférési jogkivonat lekéréséhez, amely a frissítési token használatával a következőképpen néz ki:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&grant_type=refresh_token
&resource=https%3A%2F%2Fservice.contoso.com%2F
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```

### <a name="successful-response"></a>Sikeres válasz
A jogkivonat sikeres válasza A következőképpen fog kinézni:

```
{
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1460404526",
  "resource": "https://service.contoso.com/",
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ",
  "refresh_token": "AwABAAAAv YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfcUl4VBbiSHZyd1NVZG5QTIOcbObu3qnLutbpadZGAxqjIbMkQ2bQS09fTrjMBtDE3D6kSMIodpCecoANon9b0LATkpitimVCrl PM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4rTfgV29ghDOHRc2B-C_hHeJaJICqjZ3mY2b_YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfmVCrl-NyfN3oyG4ZCWu18M9-vEou4Sq-1oMDzExgAf61noxzkNiaTecM-Ve5cq6wHqYQjfV9DOz4lbceuYCAA"
}
```
| Paraméter | Leírás |
| --- | --- |
| token_type |A jogkivonat típusa. Az egyetlen támogatott érték a **tulajdonos**. |
| expires_in |A token hátralévő élettartama másodpercben. Egy átlagos érték 3600 (egy óra). |
| expires_on |A jogkivonat lejárati dátuma és időpontja. A dátum az 1970-01-01T0:0: 0Z UTC számú másodperc, a lejárati időpontig. |
| resource |Meghatározza azt a biztonságos erőforrást, amelyet a hozzáférési jogkivonat használhat. |
| scope |A natív ügyfélalkalmazás számára megadott megszemélyesítési engedélyek. Az alapértelmezett engedély a **user_impersonation**. A célként megadott erőforrás tulajdonosa az Azure AD-ben is regisztrálhat alternatív értékeket. |
| access_token |A kért új hozzáférési jogkivonat. |
| refresh_token |Egy új OAuth 2,0 refresh_token, amely új hozzáférési jogkivonatok igénylésére használható, ha a válaszban szereplő egy lejárati idő lejár. |

### <a name="error-response"></a>Hiba válasza
A példaként kapott hiba a következőhöz hasonló:

```
{
  "error": "invalid_resource",
  "error_description": "AADSTS50001: The application named https://foo.microsoft.com/mail.read was not found in the tenant named 295e01fc-0c56-4ac3-ac57-5d0ed568f872. This can happen if the application has not been installed by the administrator of the tenant or consented to by any user in the tenant. You might have sent your authentication request to the wrong tenant.\r\nTrace ID: ef1f89f6-a14f-49de-9868-61bd4072f0a9\r\nCorrelation ID: b6908274-2c58-4e91-aea9-1f6b9c99347c\r\nTimestamp: 2016-04-11 18:59:01Z",
  "error_codes": [
    50001
  ],
  "timestamp": "2016-04-11 18:59:01Z",
  "trace_id": "ef1f89f6-a14f-49de-9868-61bd4072f0a9",
  "correlation_id": "b6908274-2c58-4e91-aea9-1f6b9c99347c"
}
```

| Paraméter | Leírás |
| --- | --- |
| hiba |Hibakód-karakterlánc, amely a felmerülő hibák típusának besorolására használható, és felhasználható a hibákra való reagálásra. |
| error_description |Egy adott hibaüzenet, amely segítséget nyújt a fejlesztőknek a hitelesítési hiba kiváltó okának azonosításában. |
| error_codes |Az STS-specifikus hibakódok listája, amelyek segíthetnek a diagnosztikaben. |
| timestamp |Az az idő, amikor a hiba bekövetkezett. |
| trace_id |Az a kérelem egyedi azonosítója, amely segíthet a diagnosztikaben. |
| correlation_id |A kérelem egyedi azonosítója, amely segíthet az összetevők diagnosztizálásában. |

A hibakódok és az ajánlott ügyfél-művelet leírását a jogkivonat- [végponti hibák hibakódja](#error-codes-for-token-endpoint-errors)című témakörben tekintheti meg.
