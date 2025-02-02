---
title: Az Azure AD szolgáltatás és szolgáltatás közötti hitelesítés a OAuth 2.0 használatával | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan használhatók a HTTP-üzenetek a szolgáltatás és szolgáltatás hitelesítésének megvalósításához a OAuth 2.0-ügyfél hitelesítő adatainak megadásakor.
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: a7f939d9-532d-4b6d-b6d3-95520207965d
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/08/2017
ms.author: ryanwi
ms.reviewer: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ac618b28fae7410a773012e390dcd6b3a63b966
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68834769"
---
# <a name="service-to-service-calls-using-client-credentials-shared-secret-or-certificate"></a>Szolgáltatások közötti hívások ügyfél-hitelesítő adatokkal (közös titok vagy tanúsítvány)

[!INCLUDE [active-directory-develop-applies-v1](../../../includes/active-directory-develop-applies-v1.md)]

A OAuth 2,0 ügyfél-hitelesítő adatok engedélyezési folyamata engedélyezi egy webszolgáltatás (*bizalmas ügyfél*) számára a saját hitelesítő adatainak használatát a felhasználó megszemélyesítése helyett, a hitelesítéshez egy másik webszolgáltatás hívásakor. Ebben az esetben az ügyfél általában egy közepes szintű webszolgáltatás, egy démon-szolgáltatás vagy egy webhely. A magasabb szintű megbízhatóság érdekében az Azure AD azt is lehetővé teszi, hogy a hívó szolgáltatás hitelesítő adatként használjon tanúsítványokat (közös titok helyett).

## <a name="client-credentials-grant-flow-diagram"></a>Ügyfél-hitelesítő adatok megadása folyamat diagramja
Az alábbi ábra azt mutatja be, hogy az ügyfél hitelesítő adatainak megadása hogyan működik a Azure Active Directory (Azure AD) szolgáltatásban.

![OAuth 2.0-ügyfél hitelesítő adatainak megadása](./media/v1-oauth2-client-creds-grant-flow/active-directory-protocols-oauth-client-credentials-grant-flow.jpg)

1. Az ügyfélalkalmazás hitelesíti magát az Azure AD jogkivonat-kiállítási végponton, és hozzáférési jogkivonatot kér.
2. Az Azure AD-jogkivonat kiállítási végpontja kiadja a hozzáférési jogkivonatot.
3. A hozzáférési jogkivonat a biztonságos erőforráshoz való hitelesítésre szolgál.
4. A biztonságos erőforrásból származó adatok az ügyfélalkalmazás számára kerülnek vissza.

## <a name="register-the-services-in-azure-ad"></a>Szolgáltatások regisztrálása az Azure AD-ben
Regisztrálja mind a hívó szolgáltatást, mind a fogadó szolgáltatást Azure Active Directoryban (Azure AD). Részletes útmutatásért lásd: [alkalmazások integrálása a Azure Active Directorysal](quickstart-v1-integrate-apps-with-azure-ad.md).

## <a name="request-an-access-token"></a>Hozzáférési jogkivonat igénylése
Hozzáférési jogkivonat igényléséhez használjon HTTP-BEJEGYZÉST a bérlő-specifikus Azure AD-végpontra.

```
https://login.microsoftonline.com/<tenant id>/oauth2/token
```

## <a name="service-to-service-access-token-request"></a>Szolgáltatás-szolgáltatás hozzáférési jogkivonat kérése
Két eset attól függően, hogy az ügyfélalkalmazás egy közös titok vagy egy tanúsítvány biztonságossá tételére van-e kiválasztva.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Első eset: Hozzáférési jogkivonat-kérelem közös titokkal
Közös titkos kulcs használata esetén a szolgáltatás-szolgáltatás hozzáférési jogkivonat-kérelem a következő paramétereket tartalmazza:

| Paraméter |  | Leírás |
| --- | --- | --- |
| grant_type |szükséges |Megadja a kért engedélyezési típust. Az ügyfél hitelesítő adatainak megadása esetén az értéknek **client_credentials**kell lennie. |
| client_id |szükséges |Megadja a hívó webszolgáltatás Azure AD-ügyfél-azonosítóját. A hívó alkalmazás ügyfél-AZONOSÍTÓjának megkereséséhez kattintson a [Azure Portal](https://portal.azure.com) **Azure Active Directory**, majd a **Alkalmazásregisztrációk**elemre, majd az alkalmazásra. A client_id az *alkalmazás azonosítója* |
| client_secret |szükséges |Adja meg az Azure AD-ben a hívó webszolgáltatás vagy démon alkalmazáshoz regisztrált kulcsot. Kulcs létrehozásához kattintson a Azure Portal **Azure Active Directory**, majd a **Alkalmazásregisztrációk**elemre, kattintson az alkalmazásra, kattintson a **Beállítások**, majd a **kulcsok**elemre, és adjon hozzá egy kulcsot.  URL – ezt a titkot kódolja a megadásakor. |
| resource |szükséges |Adja meg a fogadó webszolgáltatás alkalmazás-azonosító URI azonosítóját. Az alkalmazás-azonosító URI azonosítójának megkereséséhez kattintson a Azure Portal **Azure Active Directory**, majd a **Alkalmazásregisztrációk**lehetőségre, majd a szolgáltatásalkalmazás elemre, végül a **Beállítások** és **Tulajdonságok**elemre. |

#### <a name="example"></a>Példa
A következő http-bejegyzés egy [hozzáférési](access-tokens.md) jogkivonatot https://service.contoso.com/ kér a webszolgáltatáshoz. Az `client_id` azonosítja azt a webszolgáltatást, amely a hozzáférési jogkivonatot kéri.

```
POST /contoso.com/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&client_id=625bc9f6-3bf6-4b6d-94ba-e97cf07a22de&client_secret=qkDwDJlDfig2IpeuUZYKH1Wb8q1V0ju6sILxQQqhJ+s=&resource=https%3A%2F%2Fservice.contoso.com%2F
```

### <a name="second-case-access-token-request-with-a-certificate"></a>Második eset: Hozzáférési jogkivonat-kérelem tanúsítvánnyal
Egy tanúsítványhoz tartozó szolgáltatás-szolgáltatás hozzáférési jogkivonat-kérelem a következő paramétereket tartalmazza:

| Paraméter |  | Leírás |
| --- | --- | --- |
| grant_type |szükséges |A kért válasz típusát adja meg. Az ügyfél hitelesítő adatainak megadása esetén az értéknek **client_credentials**kell lennie. |
| client_id |szükséges |Megadja a hívó webszolgáltatás Azure AD-ügyfél-azonosítóját. A hívó alkalmazás ügyfél-AZONOSÍTÓjának megkereséséhez kattintson a [Azure Portal](https://portal.azure.com) **Azure Active Directory**, majd a **Alkalmazásregisztrációk**elemre, majd az alkalmazásra. A client_id az *alkalmazás azonosítója* |
| client_assertion_type |szükséges |Az értéknek meg kell felelnie`urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| client_assertion |szükséges | Az alkalmazáshoz hitelesítő adatként regisztrált tanúsítvány létrehozásához és aláírásához szükséges állítás (JSON Web Token). Tudnivalók a [tanúsítvány hitelesítő adatairól](active-directory-certificate-credentials.md) : a tanúsítvány regisztrálásának és az állítás formátumának megismerése.|
| resource | szükséges |Adja meg a fogadó webszolgáltatás alkalmazás-azonosító URI azonosítóját. Az alkalmazás-azonosító URI azonosítójának megkereséséhez kattintson a Azure Portal **Azure Active Directory**, majd a **Alkalmazásregisztrációk**lehetőségre, majd a szolgáltatásalkalmazás elemre, végül a **Beállítások** és **Tulajdonságok**elemre. |

Figyelje meg, hogy a paraméterek majdnem ugyanazok, mint a megosztott titkos kérelem esetében, kivéve, ha a client_secret paramétert két paraméter helyettesíti: a client_assertion_type és a client_assertion.

#### <a name="example"></a>Példa
A következő http post hozzáférési jogkivonatot kér a https://service.contoso.com/ webszolgáltatáshoz egy tanúsítvánnyal. Az `client_id` azonosítja azt a webszolgáltatást, amely a hozzáférési jogkivonatot kéri.

```
POST /<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

resource=https%3A%2F%contoso.onmicrosoft.com%2Ffc7664b4-cdd6-43e1-9365-c2e1c4e1b3bf&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg&grant_type=client_credentials
```

### <a name="service-to-service-access-token-response"></a>A szolgáltatás és a szolgáltatás közötti hozzáférési jogkivonat válasza

A sikeres válasz egy JSON OAuth 2,0 választ tartalmaz, amely a következő paraméterekkel rendelkezik:

| Paraméter | Leírás |
| --- | --- |
| access_token |A kért hozzáférési jogkivonat. A hívó webszolgáltatás ezt a tokent használhatja a fogadó webszolgáltatáshoz való hitelesítéshez. |
| token_type |Megadja a jogkivonat típusának értékét. Az Azure AD által támogatott egyetlen típus a **tulajdonos**. A tulajdonosi jogkivonatokkal kapcsolatos további információkért tekintse [meg a OAuth 2,0 engedélyezési keretrendszert: Tulajdonosi jogkivonat használata (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| expires_in |A hozzáférési jogkivonat érvényességi ideje (másodpercben). |
| expires_on |A hozzáférési jogkivonat lejáratának időpontja. A dátum az 1970-01-01T0:0: 0Z UTC számú másodperc, a lejárati időpontig. Ez az érték a gyorsítótárazott tokenek élettartamának meghatározására szolgál. |
| not_before |Az az idő, ameddig a hozzáférési jogkivonat használhatóvá válik. A dátum a (z) 1970-01-01T0:0: 0Z UTC számú másodpercnek felel meg a jogkivonat érvényességi idejéig.|
| resource |A fogadó webszolgáltatás alkalmazás-azonosító URI-ja. |

#### <a name="example-of-response"></a>Példa a válaszra
A következő példa egy webszolgáltatáshoz való hozzáférési jogkivonatra vonatkozó kérelem sikerességét mutatja be.

```
{
"access_token":"eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0ODI2NywibmJmIjoxMzg4NDQ4MjY3LCJleHAiOjEzODg0NTIxNjcsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsInN1YiI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZS8iLCJhcHBpZCI6ImQxN2QxNWJjLWM1NzYtNDFlNS05MjdmLWRiNWYzMGRkNThmMSIsImFwcGlkYWNyIjoiMSJ9.aqtfJ7G37CpKV901Vm9sGiQhde0WMg6luYJR4wuNR2ffaQsVPPpKirM5rbc6o5CmW1OtmaAIdwDcL6i9ZT9ooIIicSRrjCYMYWHX08ip-tj-uWUihGztI02xKdWiycItpWiHxapQm0a8Ti1CWRjJghORC1B1-fah_yWx6Cjuf4QE8xJcu-ZHX0pVZNPX22PHYV5Km-vPTq2HtIqdboKyZy3Y4y3geOrRIFElZYoqjqSv5q9Jgtj5ERsNQIjefpyxW3EwPtFqMcDm4ebiAEpoEWRN4QYOMxnC9OUBeG9oLA0lTfmhgHLAtvJogJcYFzwngTsVo6HznsvPWy7UP3MINA",
"token_type":"Bearer",
"expires_in":"3599",
"expires_on":"1388452167",
"resource":"https://service.contoso.com/"
}
```

## <a name="see-also"></a>Lásd még
* [OAuth 2,0 az Azure AD-ben](v1-protocols-oauth-code.md)
* [Példa C# ](https://github.com/Azure-Samples/active-directory-dotnet-daemon) a szolgáltatás és a szolgáltatás közötti hívásra egy közös titokkal és egy [minta C# ](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential) szolgáltatással
