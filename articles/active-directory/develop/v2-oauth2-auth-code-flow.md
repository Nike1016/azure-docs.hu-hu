---
title: Microsoft Identity platform és OAuth engedélyezési kód flow | Azure
description: Webalkalmazások készítése a OAuth 2,0 hitelesítési protokoll Microsoft Identity platform-implementációjának használatával.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: ae1d7d86-7098-468c-aa32-20df0a10ee3d
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: abdf60d92f51fdb34f36599aa7f4019b2adb8475
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68852235"
---
# <a name="microsoft-identity-platform-and-oauth-20-authorization-code-flow"></a>Microsoft Identity platform és OAuth 2,0 engedélyezési kód folyamatábrája

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

A OAuth 2,0 engedélyezési kód megadása az eszközre telepített alkalmazásokban használható a védett erőforrásokhoz, például a webes API-khoz való hozzáféréshez. A OAuth 2,0 Microsoft Identity platform-implementációjának használatával bejelentkezhet a mobil-és asztali alkalmazásokhoz való bejelentkezéshez és API-hoz. Ez az útmutató nyelvtől független, és leírja, hogyan küldhet és fogadhat HTTP-üzeneteket az [Azure nyílt forráskódú hitelesítési kódtárak](reference-v2-libraries.md)használata nélkül.

> [!NOTE]
> Nem minden Azure Active Directory-forgatókönyvet támogat a Microsoft Identity platform végpontja & funkciókat. Annak megállapításához, hogy érdemes-e a Microsoft Identity platform-végpontot használni, olvassa el a [Microsoft Identity platform korlátozásait](active-directory-v2-limitations.md)ismertetőt.

Az OAuth 2,0 engedélyezési kód folyamatát a [OAuth 2,0 specifikáció 4,1](https://tools.ietf.org/html/rfc6749). szakasza ismerteti. A hitelesítés és az engedélyezés az alkalmazások különböző típusai, például a [Web Apps](v2-app-types.md#web-apps) és a [natív módon telepített alkalmazások](v2-app-types.md#mobile-and-native-apps)esetében használatos. A folyamat lehetővé teszi, hogy az alkalmazások biztonságosan szerezzenek be olyan access_tokens, amelyek segítségével hozzáférhetnek a Microsoft Identity platform végpontja által védett erőforrásokhoz.

## <a name="protocol-diagram"></a>Protokoll diagramja

Magas szinten a natív/mobil alkalmazások teljes hitelesítési folyamata a következőképpen néz ki:

![OAuth-hitelesítési kód folyamatábrája](./media/v2-oauth2-auth-code-flow/convergence-scenarios-native.svg)

## <a name="request-an-authorization-code"></a>Engedélyezési kód kérése

Az engedélyezési kód folyamata azzal kezdődik, hogy az ügyfél átirányítja `/authorize` a felhasználót a végpontra. Ebben a kérelemben az ügyfél a felhasználótól beszerzett engedélyeket jelzi:

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&state=12345
```

> [!TIP]
> A kérelem végrehajtásához kattintson az alábbi hivatkozásra. A bejelentkezést követően a böngészőt át `https://localhost/myapp/` `code` kell irányítani a címsorába.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fuser.read&state=12345" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

| Paraméter    | Required/optional | Leírás |
|--------------|-------------|--------------|
| `tenant`    | szükséges    | A kérelem elérési útjának értékehasználhatóannakszabályozására,hogykijelentkezhetbeazalkalmazásba.`{tenant}` Az engedélyezett értékek `common` `organizations` :,,ésbérlőiazonosítók`consumers`. További részletek: [protokoll alapjai](active-directory-v2-protocols.md#endpoints).  |
| `client_id`   | szükséges    | Az alkalmazáshoz hozzárendelt [Azure Portal – Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) felhasználói felület **(ügyfél) azonosítója** .  |
| `response_type` | szükséges    | Tartalmaznia `code` kell az engedélyezési kód folyamatát.       |
| `redirect_uri`  | szükséges | Az alkalmazás redirect_uri, ahol a hitelesítési válaszokat el lehet juttatni és fogadni tudja az alkalmazás. Pontosan egyeznie kell a portálon regisztrált redirect_uris, kivéve, ha az URL-címet kódolni kell. A natív & Mobile apps esetében az alapértelmezett értéket `https://login.microsoftonline.com/common/oauth2/nativeclient`kell használnia.   |
| `scope`  | szükséges    | Egy szóközzel tagolt lista, [](v2-permissions-and-consent.md) melyben a felhasználónak jóvá kell hagynia a hatókört.  `/authorize` A kérelemhez több erőforrást is képes kiszolgálni, így az alkalmazás több, a hívni kívánt webes API-hoz kap beleegyezik. |
| `response_mode`   | ajánlott | Meghatározza azt a módszert, amelyet az eredményül kapott jogkivonat az alkalmazásba való visszaküldéséhez kell használni. A következők egyike lehet:<br/><br/>- `query`<br/>- `fragment`<br/>- `form_post`<br/><br/>`query`a kódot lekérdezési karakterlánc paraméterként adja meg az átirányítási URI-n. Ha az implicit folyamat használatával kér azonosító jogkivonatot, az `query` [OpenID specifikációban](https://openid.net/specs/oauth-v2-multiple-response-types-1_0.html#Combinations)megadott módon nem használható. Ha csak a kódot kéri, használhatja `query` `fragment`a, vagy `form_post`a-t is. `form_post`végrehajt egy BEJEGYZÉST, amely tartalmazza a kódot az átirányítási URI-nak. További információ: [OpenID Connect protokoll](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code).  |
| `state`                 | ajánlott | A kérelemben szereplő érték, amelyet a rendszer a jogkivonat-válaszban is visszaad. Bármely kívánt tartalom sztringje lehet. A véletlenszerűen generált egyedi érték általában a [helyek közötti kérelmek hamisításának megelőzésére](https://tools.ietf.org/html/rfc6749#section-10.12)szolgál. Az érték az alkalmazásban lévő felhasználó állapotára vonatkozó adatokat is kódolhatja, mielőtt a hitelesítési kérelem bekövetkezett volna, például az oldal vagy a megtekintés. |
| `prompt`  | választható    | Megadja a szükséges felhasználói beavatkozás típusát. Ebben az esetben `login` `none`az egyetlen érvényes érték a, a és `consent`a.<br/><br/>- `prompt=login`kényszeríti a felhasználót, hogy adja meg hitelesítő adatait a kérésen, és zárja be az egyszeri bejelentkezést.<br/>- `prompt=none`Ez ellentétes – biztosítja, hogy a felhasználó semmilyen interaktív kérdés nélkül is megjelenik. Ha a kérést nem lehet csendes úton végrehajtani az egyszeri bejelentkezésen keresztül, a Microsoft Identity platform-végpont `interaction_required` hibát ad vissza.<br/>- `prompt=consent`a a felhasználó bejelentkezése után elindítja a OAuth jóváhagyása párbeszédpanelt, amely arra kéri a felhasználót, hogy adjon engedélyt az alkalmazásnak. |
| `login_hint`  | választható    | A használatával előre kitöltheti a felhasználó bejelentkezési oldalának username/e-mail címe mezőjét, ha már ismeri a felhasználónevét. Az alkalmazások gyakran ezt a paramétert fogják használni az ismételt hitelesítés során, miután már kibontotta a felhasználónevet egy korábbi `preferred_username` bejelentkezésből a jogcím használatával.   |
| `domain_hint`  | választható    | A `consumers` (vagy `organizations`) egyike lehet.<br/><br/>Ha a szolgáltatás belefoglalja ezt a funkciót, a rendszer kihagyja az e-mailes felderítési folyamatot, amelyet a felhasználó a bejelentkezési oldalon áthalad, ami valamivel egyszerűbb felhasználói élményt nyújt. Az alkalmazások gyakran ezt a paramétert fogják használni az ismételt hitelesítés során, ha `tid` kinyeri az alkalmazást egy korábbi bejelentkezésből. Ha a `tid` jogcím `9188040d-6c67-4c5b-b112-36a304b66dad`értéke, használja `domain_hint=consumers`a () értéket. Egyéb esetben használja `domain_hint=organizations`a t.  |
| `code_challenge_method` | választható    | A `code_verifier` `code_challenge` paraméter kódolásához használt metódus. A következő értékek egyike lehet:<br/><br/>- `plain` <br/>- `S256`<br/><br/>Ha a szolgáltatás ki `code_challenge` van zárva, a rendszer azt feltételezi, hogy egyszerű szöveg, ha `code_challenge` szerepel benne. A Microsoft Identity platform mind `plain` a `S256`, mind a-t támogatja. További információ: [PKCE RFC](https://tools.ietf.org/html/rfc7636). |
| `code_challenge`  | választható | Az engedélyezési kód támogatásának biztosítására szolgál a Code Exchange (PKCE) egy natív ügyféltől származó igazoló kulcsával. Kötelező, `code_challenge_method` ha szerepel benne. További információ: [PKCE RFC](https://tools.ietf.org/html/rfc7636). |

Ekkor a rendszer megkéri a felhasználót, hogy adja meg a hitelesítő adatait, és fejezze be a hitelesítést. A Microsoft Identity platform végpontja azt is biztosítja, hogy a felhasználó beleegyezett a `scope` lekérdezési paraméterben megadott engedélyekkel. Ha a felhasználó nem járult hozzá ezen engedélyek bármelyikéhez, a felhasználó beleegyezést kér a szükséges engedélyekkel. Az [engedélyek, a beleegyezett és a több-bérlős alkalmazások részletei itt](v2-permissions-and-consent.md)érhetők el.

Miután a felhasználó hitelesíti és beleegyezik, a Microsoft Identity platform végpontja az adott alkalmazásra `redirect_uri`adott választ ad vissza a `response_mode` paraméterben megadott metódus használatával.

#### <a name="successful-response"></a>Sikeres válasz

Sikeres válasz `response_mode=query` a következőképpen:

```
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=12345
```

| Paraméter | Leírás  |
|-----------|--------------|
| `code` | Az alkalmazás által kért authorization_code. Az alkalmazás az engedélyezési kóddal kérhet hozzáférési jogkivonatot a célként megadott erőforráshoz. A Authorization_codes rövid életűek, jellemzően körülbelül 10 perc múlva lejárnak. |
| `state` | Ha a kérelemben szerepel egy State paraméter, akkor a válaszban ugyanazt az értéket kell megjelennie. Az alkalmazásnak ellenőriznie kell, hogy a kérelemben és a válaszban szereplő állapot értékek azonosak-e. |

#### <a name="error-response"></a>Hiba válasza

A `redirect_uri` rendszer a hibaüzeneteket is elküldheti, hogy az alkalmazás megfelelően tudja kezelni őket:

```
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Paraméter | Leírás  |
|----------|------------------|
| `error`  | Hibakód-karakterlánc, amely a felmerülő hibák típusának besorolására használható, és felhasználható a hibákra való reagálásra. |
| `error_description` | Egy adott hibaüzenet, amely segítséget nyújt a fejlesztőknek a hitelesítési hiba kiváltó okának azonosításában. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Az engedélyezési végpont hibáinak kódjai

Az alábbi táblázat azokat a hibakódokat ismerteti, amelyeket a rendszer a hiba `error` válaszának paraméterében adhat vissza.

| Hibakód  | Leírás    | Ügyfél művelete   |
|-------------|----------------|-----------------|
| `invalid_request` | Protokollhiba, például hiányzó kötelező paraméter. | Javítsa ki és küldje el újra a kérelmet. Ez a kezdeti tesztelés során általában felmerülő fejlesztési hiba. |
| `unauthorized_client` | Az ügyfélalkalmazás nem jogosult engedélyezési kód igénylésére. | Ez a hiba általában akkor fordul elő, ha az ügyfélalkalmazás nincs regisztrálva az Azure AD-ben, vagy nem kerül be a felhasználó Azure AD-bérlőbe. Az alkalmazás arra kéri a felhasználót, hogy telepítse az alkalmazást, és hozzáadja azt az Azure AD-hez. |
| `access_denied`  | Erőforrás-tulajdonos által megtagadott jóváhagyás  | Az ügyfélalkalmazás értesíti a felhasználót, hogy csak akkor tud továbblépni, ha a felhasználó beleegyezett. |
| `unsupported_response_type` | Az engedélyezési kiszolgáló nem támogatja a válasz típusát a kérelemben. | Javítsa ki és küldje el újra a kérelmet. Ez a kezdeti tesztelés során általában felmerülő fejlesztési hiba.  |
| `server_error`  | A kiszolgáló váratlan hibát észlelt.| Próbálja megismételni a kérelmet. Ezek a hibák ideiglenes feltételekből származhatnak. Előfordulhat, hogy az ügyfélalkalmazás elmagyarázza a felhasználónak, hogy a válasza ideiglenes hibára késleltetve van. |
| `temporarily_unavailable`   | A kiszolgáló átmenetileg túl elfoglalt a kérelem kezeléséhez. | Próbálja megismételni a kérelmet. Előfordulhat, hogy az ügyfélalkalmazás megmagyarázza a felhasználót, hogy a válasza egy ideiglenes feltétel miatt késleltetve van. |
| `invalid_resource`  | A célként megadott erőforrás érvénytelen, mert nem létezik, az Azure AD nem találja, vagy helytelenül van konfigurálva. | Ez a hiba azt jelzi, hogy az erőforrás (ha létezik) nem lett konfigurálva a bérlőben. Az alkalmazás arra kéri a felhasználót, hogy telepítse az alkalmazást, és hozzáadja azt az Azure AD-hez. |
| `login_required` | Túl sok vagy nem található felhasználó | Az ügyfél csendes hitelesítést (`prompt=none`) kért, de egyetlen felhasználó nem található. Ez azt jelentheti, hogy több felhasználó is aktív a munkamenetben, vagy nincsenek felhasználók. Ez a kiválasztott bérlőt veszi figyelembe (például ha két Azure ad-fiók aktív és egy Microsoft-fiók `consumers` van kiválasztva, a csendes hitelesítés működni fog). |
| `interaction_required` | A kérés felhasználói beavatkozást igényel. | További hitelesítési lépés vagy beleegyező engedély szükséges. Próbálkozzon újra a kérelem `prompt=none`nélkül. |

## <a name="request-an-access-token"></a>Hozzáférési jogkivonat igénylése

Most, hogy beszerzett egy authorization_code, és engedélyt kapott a felhasználónak, beválthatja `code` `access_token` az-t a kívánt erőforráshoz. Ehhez egy `POST` kérést kell küldenie a `/token` végpontnak:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```

> [!TIP]
> Próbálja meg végrehajtani a kérelmet postán! (Ne felejtse el lecserélni a `code`következőt:) [ ![próbálja meg futtatni ezt a kérelmet postán](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

| Paraméter  | Required/optional | Leírás     |
|------------|-------------------|----------------|
| `tenant`   | szükséges   | A kérelem elérési útjának értékehasználhatóannakszabályozására,hogykijelentkezhetbeazalkalmazásba.`{tenant}` Az engedélyezett értékek `common` `organizations` :,,ésbérlőiazonosítók`consumers`. További részletek: [protokoll alapjai](active-directory-v2-protocols.md#endpoints).  |
| `client_id` | szükséges  | Az alkalmazáshoz hozzárendelt [Azure Portal – Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) lap alkalmazás-(ügyfél-) azonosítója. |
| `grant_type` | szükséges   | Az engedélyezési `authorization_code` kód folyamatábrájának kell lennie.   |
| `scope`      | szükséges   | A hatókörök szóközzel tagolt listája. Az ebben a láb-ban kért hatóköröknek egyenértékűnek kell lenniük az első lábon kért hatókörökkel vagy azok egy részhalmazával. A hatóköröknek egyetlen erőforrásból kell származnia, a OIDC hatókörökkel együtt`profile`( `openid`, `email`,). A hatókörök részletesebb ismertetését az [engedélyek,](v2-permissions-and-consent.md)a beleegyezések és a hatókörök című témakörben találja. |
| `code`          | szükséges  | A folyamat első szakaszában beszerzett authorization_code. |
| `redirect_uri`  | szükséges  | A authorization_code beolvasására használt redirect_uri érték. |
| `client_secret` | webalkalmazásokhoz szükséges | Az alkalmazás regisztrációs portálján létrehozott alkalmazás-titkos kód az alkalmazáshoz. Az alkalmazás titkos kulcsát nem szabad natív alkalmazásban használni, mert a client_secrets nem lehet megbízhatóan tárolni az eszközökön. A webalkalmazásokhoz és a webes API-khoz szükséges, amelyekkel biztonságosan tárolhatók a client_secret a kiszolgálóoldali oldalon.  Az ügyfél titkos kódjának URL-kódolással kell rendelkeznie a küldés előtt.  |
| `code_verifier` | választható  | A authorization_code beszerzéséhez használt code_verifier. Kötelező, ha a PKCE az engedélyezési kód Grant kérelmében használták. További információ: [PKCE RFC](https://tools.ietf.org/html/rfc7636). |

### <a name="successful-response"></a>Sikeres válasz

A jogkivonat sikeres válasza A következőképpen fog kinézni:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fuser.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```

| Paraméter     | Leírás   |
|---------------|------------------------------|
| `access_token`  | A kért hozzáférési jogkivonat. Az alkalmazás használhatja ezt a tokent a biztonságos erőforráshoz, például egy webes API-hoz való hitelesítéshez.  |
| `token_type`    | Megadja a jogkivonat típusának értékét. Az Azure AD által támogatott egyetlen típus a tulajdonos |
| `expires_in`    | A hozzáférési jogkivonat érvényességi ideje (másodpercben). |
| `scope`         | Azok a hatókörök, amelyekre a access_token érvényes. |
| `refresh_token` | Egy OAuth 2,0 frissítési token. Az alkalmazás használhatja ezt a tokent további hozzáférési jogkivonatok beszerzésére az aktuális hozzáférési jogkivonat lejárta után. A Refresh_tokens hosszú élettartamú, és az erőforrásokhoz való hozzáférés hosszabb ideig való megőrzésére is használható. A hozzáférési tokenek frissítésével kapcsolatos további részletekért tekintse meg az [alábbi szakaszt](#refresh-the-access-token). <br> **Megjegyzés:** Csak akkor van `offline_access` megadva, ha a hatókört kérték. |
| `id_token`      | Egy JSON Web Token (JWT). Az alkalmazás képes dekódolni a token szegmenseit, hogy adatokat kérjen a bejelentkezett felhasználótól. Az alkalmazás gyorsítótárazhatja az értékeket, és megjelenítheti őket, de nem hivatkozhat rájuk az engedélyezési vagy biztonsági határokra. A id_tokens kapcsolatos további információkért lásd: [`id_token reference`](id-tokens.md). <br> **Megjegyzés:** Csak akkor van `openid` megadva, ha a hatókört kérték. |

### <a name="error-response"></a>Hiba válasza

A következőhöz hasonló hibaüzenetek jelennek meg:

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Paraméter         | Leírás    |
|-------------------|----------------|
| `error`       | Hibakód-karakterlánc, amely a felmerülő hibák típusának besorolására használható, és felhasználható a hibákra való reagálásra. |
| `error_description` | Egy adott hibaüzenet, amely segítséget nyújt a fejlesztőknek a hitelesítési hiba kiváltó okának azonosításában. |
| `error_codes` | Az STS-specifikus hibakódok listája, amelyek segíthetnek a diagnosztikaben.  |
| `timestamp`   | Az az idő, amikor a hiba bekövetkezett. |
| `trace_id`    | Az a kérelem egyedi azonosítója, amely segíthet a diagnosztikaben. |
| `correlation_id` | A kérelem egyedi azonosítója, amely segíthet az összetevők diagnosztizálásában. |

### <a name="error-codes-for-token-endpoint-errors"></a>Hibakódok jogkivonat-végponti hibákhoz

| Hibakód         | Leírás        | Ügyfél művelete    |
|--------------------|--------------------|------------------|
| `invalid_request`  | Protokollhiba, például hiányzó kötelező paraméter. | A kérelem javítása és újraküldése   |
| `invalid_grant`    | Az engedélyezési kód vagy a PKCE-ellenőrző érvénytelen vagy lejárt. | Próbálkozzon egy új kéréssel a `/authorize` végponton, és ellenőrizze, hogy a code_verifier paraméter helyes-e.  |
| `unauthorized_client` | A hitelesített ügyfél nem jogosult az engedélyezési támogatás típusának használatára. | Ez általában akkor fordul elő, ha az ügyfélalkalmazás nincs regisztrálva az Azure AD-ben, vagy nem kerül be a felhasználó Azure AD-bérlőbe. Az alkalmazás arra kéri a felhasználót, hogy telepítse az alkalmazást, és hozzáadja azt az Azure AD-hez. |
| `invalid_client` | Nem sikerült az ügyfél-hitelesítés.  | Az ügyfél hitelesítő adatai nem érvényesek. A javításhoz az alkalmazás rendszergazdája frissíti a hitelesítő adatokat.   |
| `unsupported_grant_type` | Az engedélyezési kiszolgáló nem támogatja az engedélyezési engedély típusát. | Módosítsa a kérelemben szereplő Grant típust. Ez a típusú hiba csak a fejlesztés során fordul elő, és a rendszer a kezdeti tesztelés során észleli. |
| `invalid_resource` | A célként megadott erőforrás érvénytelen, mert nem létezik, az Azure AD nem találja, vagy helytelenül van konfigurálva. | Ez azt jelzi, hogy az erőforrás (ha létezik) nem lett konfigurálva a bérlőben. Az alkalmazás arra kéri a felhasználót, hogy telepítse az alkalmazást, és hozzáadja azt az Azure AD-hez.  |
| `interaction_required` | A kérés felhasználói beavatkozást igényel. Például további hitelesítési lépésre van szükség. | Próbálja megismételni a kérelmet ugyanazzal az erőforrással.  |
| `temporarily_unavailable` | A kiszolgáló átmenetileg túl elfoglalt a kérelem kezeléséhez. | Próbálja megismételni a kérelmet. Előfordulhat, hogy az ügyfélalkalmazás megmagyarázza a felhasználót, hogy a válasza egy ideiglenes feltétel miatt késleltetve van. |

## <a name="use-the-access-token"></a>Hozzáférési jogkivonat használata

Most `access_token`, hogy sikeresen megszerezte a-t, használhatja a jogkivonatot a webes API-khoz a kérésekben a `Authorization` fejlécbe való belefoglalásával:

> [!TIP]
> A kérelem végrehajtása postán! (Először cserélje `Authorization` le a fejlécet) [ ![futtassa ezt a kérelmet a Poster-ben](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-access-token"></a>A hozzáférési jogkivonat frissítése

A Access_tokens rövid életűek, és a lejáratuk után frissítenie kell őket, hogy továbbra is hozzáférhessenek az erőforrásokhoz. Ezt úgy teheti `POST` meg `refresh_token` , hogy elküld egy másik `/token` kérést a végpontnak, ezúttal `code`megadja a helyett a következőt:.  A frissítési jogkivonatok érvényesek minden olyan engedélyhez, amelyet az ügyfél már kapott, ezért a kérelemre `scope=mail.read` kiadott frissítési jogkivonat használható új hozzáférési `scope=api://contoso.com/api/UseResource`jogkivonat igénylésére a alkalmazáshoz.  

A frissítési tokenek nem rendelkeznek megadott élettartammal. A frissítési tokenek élettartama általában viszonylag hosszú. Bizonyos esetekben azonban a frissítési tokenek lejárnak, visszavonásra kerülnek, vagy hiányoznak a megfelelő jogosultságok a kívánt művelethez. Az alkalmazásnak megfelelően kell elvárnia és kezelnie [a jogkivonat-kiállítási végpont által visszaadott hibákat](#error-codes-for-token-endpoint-errors) . 

Bár a frissítési tokenek nem vonhatók vissza, amikor új hozzáférési jogkivonatok beszerzésére használják, el kell vetni a régi frissítési tokent. A [OAuth 2,0 spec](https://tools.ietf.org/html/rfc6749#section-6) a következőket mondja: "Az engedélyezési kiszolgáló új frissítési tokent ADHAT ki, amely esetben az ügyfélnek el kell vetnie a régi frissítési jogkivonatot, és le kell cserélnie az új frissítési jogkivonattal. Az engedélyezési kiszolgáló visszavonhatja a régi frissítési tokent, miután kiadott egy új frissítési jogkivonatot az ügyfélnek. "  

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&grant_type=refresh_token
&client_secret=JqQX2PNo9bpM0uEihUPzyrh      // NOTE: Only required for web apps
```

> [!TIP]
> Próbálja meg végrehajtani a kérelmet postán! (Ne felejtse el lecserélni a `refresh_token`következőt:) [ ![próbálja meg futtatni ezt a kérelmet postán](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)
> 

| Paraméter     |                | Leírás        |
|---------------|----------------|--------------------|
| `tenant`        | szükséges     | A kérelem elérési útjának értékehasználhatóannakszabályozására,hogykijelentkezhetbeazalkalmazásba.`{tenant}` Az engedélyezett értékek `common` `organizations` :,,ésbérlőiazonosítók`consumers`. További részletek: [protokoll alapjai](active-directory-v2-protocols.md#endpoints).   |
| `client_id`     | szükséges    | Az alkalmazáshoz hozzárendelt [Azure Portal – Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) felhasználói felület **(ügyfél) azonosítója** . |
| `grant_type`    | szükséges    | `refresh_token` Az engedélyezési kód folyamatának ezen szakaszához kell tartoznia. |
| `scope`         | szükséges    | A hatókörök szóközzel tagolt listája. Az ebben a végtagban kért hatóköröknek meg kell egyeznie az eredeti authorization_code-kérelemben kért hatókörökkel vagy azok egy részhalmazával. Ha a kérelemben megadott hatókörök több erőforrás-kiszolgálóra is kiterjednek, akkor a Microsoft Identity platform végpontja az első hatókörben megadott erőforráshoz tartozó jogkivonatot ad vissza. A hatókörök részletesebb ismertetését az [engedélyek,](v2-permissions-and-consent.md)a beleegyezések és a hatókörök című témakörben találja. |
| `refresh_token` | szükséges    | A folyamat második szakaszában beszerzett refresh_token. |
| `client_secret` | webalkalmazásokhoz szükséges | Az alkalmazás regisztrációs portálján létrehozott alkalmazás-titkos kód az alkalmazáshoz. Nem használható natív alkalmazásban, mert a client_secrets nem lehet megbízhatóan tárolni az eszközökön. A webalkalmazásokhoz és a webes API-khoz szükséges, amelyekkel biztonságosan tárolhatók a client_secret a kiszolgálóoldali oldalon. |

#### <a name="successful-response"></a>Sikeres válasz

A jogkivonat sikeres válasza A következőképpen fog kinézni:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fuser.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```
| Paraméter     | Leírás         |
|---------------|-------------------------------------------------------------|
| `access_token`  | A kért hozzáférési jogkivonat. Az alkalmazás használhatja ezt a tokent a biztonságos erőforráshoz, például egy webes API-hoz való hitelesítéshez. |
| `token_type`    | Megadja a jogkivonat típusának értékét. Az Azure AD által támogatott egyetlen típus a tulajdonos |
| `expires_in`    | A hozzáférési jogkivonat érvényességi ideje (másodpercben).   |
| `scope`         | Azok a hatókörök, amelyekre a access_token érvényes.    |
| `refresh_token` | Új OAuth 2,0 frissítési jogkivonat. A régi frissítési tokent cserélje le erre az újonnan beszerzett frissítési jogkivonatra annak biztosításához, hogy a frissítési tokenek a lehető leghosszabb ideig érvényesek maradjanak. <br> **Megjegyzés:** Csak akkor van `offline_access` megadva, ha a hatókört kérték.|
| `id_token`      | Aláíratlan JSON Web Token (JWT). Az alkalmazás képes dekódolni a token szegmenseit, hogy adatokat kérjen a bejelentkezett felhasználótól. Az alkalmazás gyorsítótárazhatja az értékeket, és megjelenítheti őket, de nem hivatkozhat rájuk az engedélyezési vagy biztonsági határokra. A id_tokens kapcsolatos további információkért lásd: [`id_token reference`](id-tokens.md). <br> **Megjegyzés:** Csak akkor van `openid` megadva, ha a hatókört kérték. |

#### <a name="error-response"></a>Hiba válasza

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Paraméter         | Leírás                                                                                        |
|-------------------|----------------------------------------------------------------------------------------------------|
| `error`           | Hibakód-karakterlánc, amely a felmerülő hibák típusának besorolására használható, és felhasználható a hibákra való reagálásra. |
| `error_description` | Egy adott hibaüzenet, amely segítséget nyújt a fejlesztőknek a hitelesítési hiba kiváltó okának azonosításában.           |
| `error_codes` |Az STS-specifikus hibakódok listája, amelyek segíthetnek a diagnosztikaben. |
| `timestamp` | Az az idő, amikor a hiba bekövetkezett. |
| `trace_id` | Az a kérelem egyedi azonosítója, amely segíthet a diagnosztikaben. |
| `correlation_id` | A kérelem egyedi azonosítója, amely segíthet az összetevők diagnosztizálásában. |

A hibakódok és az ajánlott ügyfél-művelet leírását a jogkivonat- [végponti hibák hibakódja](#error-codes-for-token-endpoint-errors)című témakörben tekintheti meg.
