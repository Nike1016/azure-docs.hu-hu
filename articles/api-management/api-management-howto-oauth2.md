---
title: Fejlesztői fiókok engedélyezése a OAuth 2,0 használatával az Azure API Managementban | Microsoft Docs
description: Megtudhatja, hogyan engedélyezheti a felhasználóknak a OAuth 2,0-et a API Managementban.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2018
ms.author: apimpm
ms.openlocfilehash: b7b003c588d7b079823bb046676a1226828fcae2
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249861"
---
# <a name="how-to-authorize-developer-accounts-using-oauth-20-in-azure-api-management"></a>Fejlesztői fiókok engedélyezése az Azure API Management OAuth 2,0 használatával

Számos API támogatja az [OAuth 2,0](https://oauth.net/2/) -et az API biztonságossá tételéhez, és gondoskodjon arról, hogy csak az érvényes felhasználók férjenek hozzá, és csak azokhoz az erőforrásokhoz férhessenek hozzá, amelyekre jogosultak. Az Azure API Management interaktív fejlesztői konzoljának az ilyen API-kkal való használatához a szolgáltatás lehetővé teszi, hogy konfigurálja a szolgáltatási példányt úgy, hogy az OAuth 2,0-kompatibilis API-val működjön.

## <a name="prerequisites"> </a>Előfeltételek

Ez az útmutató bemutatja, hogyan konfigurálhatja a API Management Service-példányt a OAuth 2,0-engedélyezés használatára a fejlesztői fiókokhoz, de nem jeleníti meg a OAuth 2,0-szolgáltató konfigurálását. Az egyes OAuth 2,0-szolgáltatók konfigurációja eltér egymástól, bár a lépések hasonlóak, és a OAuth 2,0 konfigurálásához használt szükséges információk megegyeznek a API Management Service-példányban. Ez a témakör példákat mutat be a Azure Active Directory OAuth 2,0-szolgáltatóként való használatára.

> [!NOTE]
> A OAuth 2,0 Azure Active Directory használatával történő konfigurálásával kapcsolatos további információkért tekintse meg a [WebApp-GraphAPI-DotNet][WebApp-GraphAPI-DotNet] mintát.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="step1"> </a>OAuth 2,0 engedélyezési kiszolgáló konfigurálása API Management

> [!NOTE]
> Ha még nem hozott létre API Management Service-példányt, tekintse meg [a API Management-szolgáltatás példányának létrehozása][Create an API Management service instance]című témakört.

1. Kattintson a bal oldali menü OAuth 2,0 fülére, majd kattintson a **+ Hozzáadás**elemre.

    ![OAuth 2,0 menü](./media/api-management-howto-oauth2/oauth-01.png)

2. Adjon meg egy nevet és egy opcionális leírást a **név** és **Leírás** mezőkben.

    > [!NOTE]
    > Ezek a mezők a OAuth 2,0 engedélyezési kiszolgáló azonosítására szolgálnak a jelenlegi API Management szolgáltatási példányon belül, és azok értékei nem a OAuth 2,0-kiszolgálóról származnak.

3. Adja meg az **ügyfél-regisztrációs oldal URL-címét**. Ezen a lapon a felhasználók létrehozhatják és kezelhetik a fiókjaikat, és a OAuth 2,0-szolgáltatótól függően változhatnak. Az **ügyfél-regisztrációs oldal URL-címe** arra a lapra mutat, amellyel a felhasználók létrehozhatják és konfigurálhatják a saját fiókjaikat a OAuth 2,0-szolgáltatók számára, amelyek támogatják a fiókok felhasználói felügyeletét. Néhány szervezet nem konfigurálja és nem használja ezt a funkciót, még akkor sem, ha a OAuth 2,0-szolgáltató támogatja azt. Ha a OAuth 2,0-szolgáltató nem rendelkezik konfigurált fiókok felhasználói felügyeletével, adjon meg egy helyőrző URL-címet, például a vállalat URL-címét, vagy egy URL `https://placeholder.contoso.com`-címet, például:.

    ![OAuth 2,0 új kiszolgáló](./media/api-management-howto-oauth2/oauth-02.png)

4. Az űrlap következő szakasza tartalmazza az **engedélyezési engedélyek típusát**, az **engedélyezési végpont URL-címét**és az **engedélyezési kérelem módszerének** beállításait.

    Adja meg az **engedélyezési típusokat** a kívánt típusok ellenőrzésével. Az **engedélyezési kód** alapértelmezés szerint meg van adva.

    Adja meg az **engedélyezési végpont URL-címét**. Azure Active Directory esetében ez az URL-cím a következő URL-címhez hasonlóan fog `<tenant_id>` megjelenni, ahol az az Azure ad-bérlő azonosítójának helyébe lép.

    `https://login.microsoftonline.com/<tenant_id>/oauth2/authorize`

    Az **engedélyezési kérelem módszere** határozza meg, hogyan történik az engedélyezési kérelem küldése a OAuth 2,0-kiszolgálóra. Alapértelmezés szerint a **Get** beállítás van kiválasztva.

5. Ezután meg kell adni a **jogkivonat-végpont URL-címét**, az **ügyfél-hitelesítési módszereket**, a **hozzáférési token küldési metódusát** és az **alapértelmezett hatókört** .

    ![OAuth 2,0 új kiszolgáló](./media/api-management-howto-oauth2/oauth-03.png)

    Egy Azure Active Directory OAuth 2,0-kiszolgáló esetén a **jogkivonat-végpont URL-címének** formátuma a `<TenantID>` következő lesz, ahol `yourapp.onmicrosoft.com`a formátuma:.

    `https://login.microsoftonline.com/<TenantID>/oauth2/token`

    Az **ügyfél-hitelesítési módszerek** alapértelmezett beállítása az alapszintű, a **hozzáférési jogkivonat-küldési módszer** pedig az **engedélyezési fejléc**. Ezek az értékek az űrlap ezen szakaszán, az **alapértelmezett hatókörrel**együtt konfigurálhatók.

6. Az **ügyfél hitelesítő adatai** szakasz tartalmazza az **ügyfél-azonosítót** és az **ügyfél titkát**, amelyet a rendszer a OAuth 2,0-kiszolgáló létrehozása és konfigurálása során kapott. Az **ügyfél-azonosító** és az **ügyfél titkos kulcsának** megadása után létrejön az **engedélyezési kód** **redirect_uri** . Ez az URI a válasz URL-címének konfigurálására szolgál a OAuth 2,0-kiszolgáló konfigurációjában.

    ![OAuth 2,0 új kiszolgáló](./media/api-management-howto-oauth2/oauth-04.png)

    Ha a **hitelesítési engedélyezési típusok** erőforrás- **tulajdonosi jelszóként**vannak beállítva, az **erőforrás-tulajdonos jelszava hitelesítő adatai** szakasz a hitelesítő adatok megadására szolgál. Ellenkező esetben hagyja üresen.

    Miután az űrlap elkészült, kattintson a **Létrehozás** gombra a API Management OAuth 2,0 engedélyezési kiszolgáló konfigurációjának mentéséhez. A kiszolgáló konfigurációjának mentése után az API-k a következő szakaszban látható módon konfigurálhatók a konfiguráció használatára.

## <a name="step2"> </a>OAuth 2,0 felhasználói hitelesítés használatára szolgáló API konfigurálása

1. Kattintson a bal oldali **API Management** menüjében az **API** -k elemre.

    ![OAuth 2,0 API-k](./media/api-management-howto-oauth2/oauth-05.png)

2. Kattintson a kívánt API nevére, majd a **Beállítások**elemre. Görgessen a **Biztonság** szakaszhoz, majd jelölje be a **OAuth 2,0**jelölőnégyzetet.

    ![OAuth 2,0 beállítások](./media/api-management-howto-oauth2/oauth-06.png)

3. Válassza ki a kívánt **engedélyezési kiszolgálót** a legördülő listából, majd kattintson a **Mentés**gombra.

    ![OAuth 2,0 beállítások](./media/api-management-howto-oauth2/oauth-07.png)

## <a name="step3"> </a>A OAuth 2,0 felhasználói hitelesítés tesztelése a fejlesztői portálon

Miután konfigurálta a OAuth 2,0 engedélyezési kiszolgálót, és konfigurálta az API-t a kiszolgáló használatára, tesztelheti azt a fejlesztői portálon, és meghívja az API-t.  Kattintson a **fejlesztői portál** elemre az Azure API Management példányának **Áttekintés** oldalának felső menüjében.

![Fejlesztői portál][api-management-developer-portal-menu]

Kattintson az **API** -k elemre a felső menüben, majd válassza az **echo API**elemet.

![Echo API][api-management-apis-echo-api]

> [!NOTE]
> Ha csak egy API van konfigurálva, vagy csak egy API látható a fiókja számára, és rákattint az API-k elemre, az közvetlenül az API-hoz tartozó művelethez fogja vinni.

Válassza az **erőforrás** lekérése műveletet, kattintson a **konzol megnyitása**lehetőségre, majd válassza a legördülő listából az **engedélyezési kód** lehetőséget.

![Konzol megnyitása][api-management-open-console]

Ha az **engedélyezési kód** van kiválasztva, megjelenik egy előugró ablak, amely a OAuth 2,0 szolgáltató bejelentkezési formáját jeleníti meg. Ebben a példában a bejelentkezési űrlapot Azure Active Directory biztosítjuk.

> [!NOTE]
> Ha letiltotta az előugró ablakokat, a rendszer kérni fogja, hogy engedélyezze őket a böngészőben. Miután engedélyezte őket, válassza újra az **engedélyezési kódot** , és megjelenik a bejelentkezési űrlap.

![Bejelentkezés][api-management-oauth2-signin]

Miután bejelentkezett, a kérések **fejlécei** egy `Authorization : Bearer` fejléctel lesznek feltöltve, amely engedélyezi a kérést.

![Kérelem fejlécének tokenje][api-management-request-header-token]

Ezen a ponton beállíthatja a fennmaradó paraméterek kívánt értékeit, és elküldheti a kérést.

## <a name="next-steps"></a>További lépések

További információ a OAuth 2,0 és a API Management használatáról: a következő videó és kísérő [cikk](api-management-howto-protect-backend-with-aad.md).

[api-management-oauth2-signin]: ./media/api-management-howto-oauth2/api-management-oauth2-signin.png
[api-management-request-header-token]: ./media/api-management-howto-oauth2/api-management-request-header-token.png
[api-management-developer-portal-menu]: ./media/api-management-howto-oauth2/api-management-developer-portal-menu.png
[api-management-open-console]: ./media/api-management-howto-oauth2/api-management-open-console.png
[api-management-apis-echo-api]: ./media/api-management-howto-oauth2/api-management-apis-echo-api.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[https://oauth.net/2/]: https://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

