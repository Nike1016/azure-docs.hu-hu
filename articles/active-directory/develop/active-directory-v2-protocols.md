---
title: Tudnivalók a Microsoft Identity platform által támogatott engedélyezési protokollokról | Azure
description: Útmutató a OAuth 2,0 és az OpenID Connect protokollokhoz, amelyeket a Microsoft Identity platform végpontja támogat.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 5fb4fa1b-8fc4-438e-b3b0-258d8c145f22
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 446d7178973c1d43d55ff89c429b05c2a10118ba
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68835286"
---
# <a name="microsoft-identity-platform-protocols"></a>Microsoft Identity platform-protokollok

A Microsoft Identity platform végpontja az iparági szabványoknak megfelelő, az OpenID Connect és a OAuth 2,0-as szolgáltatást nyújtó szolgáltatáshoz. Míg a szolgáltatás szabványoknak megfelelő, a protokollok két implementációja között finom különbségek lehetnek. Az itt olvasható információk akkor hasznosak, ha úgy dönt, hogy a kódot közvetlenül a HTTP-kérések küldésével és felügyeletével vagy harmadik féltől származó nyílt forráskódú kódtár használatával írja elő, és nem használja a [nyílt forráskódú kódtárak](reference-v2-libraries.md)egyikét sem.

> [!NOTE]
> A Microsoft Identity platform végpontja nem támogatja az összes Azure AD-forgatókönyvet és-funkciót. Annak megállapításához, hogy érdemes-e a Microsoft Identity platform-végpontot használni, olvassa el a [Microsoft Identity platform korlátozásait](active-directory-v2-limitations.md)ismertetőt.

## <a name="the-basics"></a>Az alapok

Csaknem minden OAuth 2,0 és OpenID Connect-folyamaton belül négy fél vesz részt az Exchange-ben:

![A OAuth 2,0 szerepköröket bemutató diagram](./media/active-directory-v2-flows/protocols-roles.svg)

* Az **engedélyezési kiszolgáló** a Microsoft Identity platform végpontja, amely a felhasználó személyazonosságának biztosítására, az erőforrásokhoz való hozzáférés megadására és visszavonására, valamint a jogkivonatok kiadására szolgál. Az engedélyezési kiszolgáló más néven az identitás-szolgáltató – biztonságosan kezeli a felhasználó információit, hozzáférését, valamint a folyamat résztvevői közötti megbízhatósági kapcsolatait.
* Az **erőforrás tulajdonosa** általában a végfelhasználó. Ez az a fél, amely az adattulajdonost birtokolja, és lehetővé teszi, hogy harmadik felek hozzáférjenek ehhez az adatforráshoz vagy erőforráshoz.
* Az **OAuth-ügyfél** az alkalmazás azonosítója alapján azonosított alkalmazás. A OAuth-ügyfél általában az a fél, akit a végfelhasználó kommunikál, és az engedélyezési kiszolgálótól kér jogkivonatokat. Az ügyfélnek engedélyt kell adni az erőforrás tulajdonos általi eléréséhez.
* Az **erőforrás-kiszolgáló** , ahol az erőforrás vagy az adat található. Megbízik az engedélyezési kiszolgálón, hogy biztonságosan hitelesítse és engedélyezze az OAuth-ügyfelet, és a tulajdonos hozzáférési jogkivonatait használja annak biztosítására, hogy az erőforrásokhoz való hozzáférés megadható legyen.

## <a name="app-registration"></a>Alkalmazás regisztrálása

Minden olyan alkalmazást, amely a személyes és munkahelyi vagy iskolai fiókokat is el kívánja fogadni, regisztrálnia kell a [Azure Portal](https://aka.ms/appregistrations) **Alkalmazásregisztrációk** -felületén, mielőtt aláírja ezeket a felhasználókat a OAuth 2,0 vagy OpenID Connect használatával. Az alkalmazás regisztrációs folyamata összegyűjti és hozzárendel néhány értéket az alkalmazáshoz:

* Egy **alkalmazás-azonosító** , amely egyedileg azonosítja az alkalmazást
* **Átirányítási URI** (nem kötelező), amely a válaszok visszairányítására használható az alkalmazásba
* Néhány más forgatókönyv-specifikus érték.

További részletekért ismerkedjen meg az [alkalmazások regisztrálásának folyamatával](quickstart-register-app.md).

## <a name="endpoints"></a>Végpontok

A regisztrációt követően az alkalmazás a Microsoft Identity platformmal kommunikál, ha kéréseket küld a végpontnak:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
```

Ahol a `{tenant}` a négy különböző érték egyikét hajthatja végre:

| Value | Leírás |
| --- | --- |
| `common` | Lehetővé teszi, hogy a felhasználók személyes Microsoft-fiókokkal és munkahelyi/iskolai fiókkal jelentkezzenek be az Azure AD-be az alkalmazásba. |
| `organizations` | Csak az Azure AD-beli munkahelyi vagy iskolai fiókkal rendelkező felhasználók számára engedélyezi az alkalmazásba való bejelentkezést. |
| `consumers` | Csak személyes Microsoft-fiókkal (MSA) rendelkező felhasználók számára engedélyezi az alkalmazásba való bejelentkezést. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` vagy `contoso.onmicrosoft.com` | Csak a munkahelyi vagy iskolai fiókkal rendelkező felhasználók számára engedélyezi az adott Azure AD-bérlőtől az alkalmazásba való bejelentkezést. Az Azure AD-bérlő vagy a bérlő GUID azonosítójának felhasználóbarát tartományneve is használható. |

Ha többet szeretne megtudni ezekről a végpontokról, válasszon egy adott alkalmazást a [protokollok](#protocols) szakaszban, és kövesse a hivatkozásokat további információért.

> [!TIP]
> Minden, az Azure AD-ban regisztrált alkalmazás használhatja a Microsoft Identity platform-végpontot, még akkor is, ha nem jelentkezik be a személyes fiókokba.  Ily módon áttelepítheti a meglévő alkalmazásokat a Microsoft Identity platformra és [MSAL](reference-v2-libraries.md) az alkalmazás újbóli létrehozása nélkül.  

## <a name="tokens"></a>Tokenek

A OAuth 2,0 és az OpenID Connect Microsoft Identity platform általi megvalósítása a tulajdonosi jogkivonatok széleskörű használatát teszi elérhetővé, beleértve a JWTs-ként jelölt tulajdonosi jogkivonatokat is. A tulajdonosi jogkivonat egy olyan egyszerű biztonsági jogkivonat, amely a "tulajdonos" hozzáférést biztosít egy védett erőforráshoz. Ebben az értelemben a "tulajdonos" bármely olyan fél, aki be tudja mutatni a jogkivonatot. Bár a feleknek először hitelesíteniük kell magukat a Microsoft Identity platformmal a tulajdonosi jogkivonat fogadásához, ha a szükséges lépések nem a jogkivonat biztonságossá tételére szolgálnak az átvitel és a tárolás során, akkor azt egy nem szándékolt fél felhasználhatja és használhatja. Míg egyes biztonsági jogkivonatok beépített mechanizmussal rendelkeznek, amely megakadályozza a jogosulatlan felek használatát, a tulajdonosi jogkivonatok nem rendelkeznek ezzel a mechanizmussal, és egy biztonságos csatornán, például a Transport Layer Security (HTTPS) szolgáltatásban kell őket szállítani. Ha egy tulajdonosi jogkivonatot a rendszer egyértelművé tesz, a rosszindulatú felek a támadók megszerezhetik a jogkivonatot, és jogosulatlan hozzáférésre használhatják a védett erőforrásokhoz. Ugyanezek a biztonsági elvek érvényesek a tulajdonosi jogkivonatok későbbi használatra történő tárolására vagy gyorsítótárazására. Mindig győződjön meg arról, hogy az alkalmazás biztonságos módon továbbítja és tárolja a tulajdonosi jogkivonatokat. A tulajdonosi jogkivonatokkal kapcsolatos további biztonsági megfontolásokat lásd: [RFC 6750, 5. szakasz](https://tools.ietf.org/html/rfc6750).

A Microsoft Identity platform végpontjában használt különböző típusú tokenek további részletei [a Microsoft Identity platform Endpoint token referenciájában](v2-id-and-access-tokens.md)olvashatók.

## <a name="protocols"></a>Protokollok

Ha készen áll arra, hogy lásson néhány példát, ismerkedjen meg az alábbi oktatóanyagok egyikével. Mindegyik egy adott hitelesítési forgatókönyvnek felel meg. Ha segítségre van szüksége annak meghatározásához, hogy melyik a megfelelő folyamat, tekintse meg [a Microsoft Identity platformmal felépíthető alkalmazások típusait](v2-app-types.md).

* [Mobil-és natív alkalmazás létrehozása a OAuth 2,0](v2-oauth2-auth-code-flow.md)
* [Webalkalmazások létrehozása OpenID Connecttel](v2-protocols-oidc.md)
* [Egylapos alkalmazások készítése a OAuth 2,0 implicit folyamattal](v2-oauth2-implicit-grant-flow.md)
* [Démonokat vagy kiszolgálóoldali folyamatokat hozhat létre a OAuth 2,0 ügyfél-hitelesítő adatok folyamatával](v2-oauth2-client-creds-grant-flow.md)
* [Jogkivonatok beszerzése egy webes API-ban a OAuth 2,0-es verziójának használatával](v2-oauth2-on-behalf-of-flow.md)
