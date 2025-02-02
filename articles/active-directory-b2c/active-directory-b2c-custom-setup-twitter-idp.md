---
title: Twitter-fiókkal való bejelentkezés beállítása egyéni szabályzatok használatával Azure Active Directory B2Cban | Microsoft Docs
description: Twitter-fiókkal való bejelentkezés beállítása egyéni szabályzatok használatával Azure Active Directory B2Cban.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 557d25c4921c9906be75bce03c326903e63432de
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68464796"
---
# <a name="set-up-sign-in-with-a-twitter-account-by-using-custom-policies-in-azure-active-directory-b2c"></a>Twitter-fiókkal való bejelentkezés beállítása egyéni szabályzatok használatával Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk bemutatja, hogyan engedélyezheti a bejelentkezést egy Twitter-fiók felhasználói számára [Egyéni szabályzatok](active-directory-b2c-overview-custom.md) használatával Azure Active Directory (Azure ad) B2C-ben.

## <a name="prerequisites"></a>Előfeltételek

- Hajtsa végre a következő témakörben ismertetett lépéseket: Ismerkedés az [Egyéni szabályzatokkal Azure Active Directory B2Cban](active-directory-b2c-get-started-custom.md).
- Ha még nem rendelkezik Twitter-fiókkal, hozzon létre egyet a [Twitter regisztrációs oldalon](https://twitter.com/signup).

## <a name="create-an-application"></a>Alkalmazás létrehozása

Ha a Twittert identitás-szolgáltatóként szeretné használni Azure AD B2Cban, létre kell hoznia egy Twitter-alkalmazást.

1. Jelentkezzen be a [Twitter-fejlesztők](https://developer.twitter.com/en/apps) webhelyére a Twitter-fiókja hitelesítő adataival.
2. Válassza **az alkalmazás létrehozása**lehetőséget.
3. Adja meg az alkalmazás **nevét** és **leírását**.
4. A **webhely URL**-címe `https://your-tenant.b2clogin.com`mezőbe írja be a értéket. Cserélje `your-tenant` le a helyére a bérlő nevét. Például: https://contosob2c.b2clogin.com.
5. A **visszahívás URL**-címéhez `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/your-policy-Id/oauth1/authresp`írja be a következőt:. Cserélje `your-tenant` le a nevet a bérlő neve és `your-policy-Id` a szabályzat azonosítójának helyére. Például: `b2c_1A_signup_signin_twitter`. A bérlő nevének megadásakor az összes kisbetűt kell használnia, még akkor is, ha a bérlőt nagybetűvel definiálták Azure AD B2C.
6. A lap alján olvassa el és fogadja el a feltételeket, majd válassza a **Létrehozás**lehetőséget.
7. Az **alkalmazás részletei** lapon válassza a **Szerkesztés > a részletek szerkesztése**lehetőséget, jelölje be a **bejelentkezés engedélyezése**a Twitteren jelölőnégyzetet, majd kattintson a **Mentés**gombra.
8. Válassza a **kulcsok és** jogkivonatok lehetőséget, és jegyezze fel a **fogyasztói API-kulcsot** és a **fogyasztói API titkos kulcsának** értékeit, amelyeket később szeretne használni.

## <a name="create-a-policy-key"></a>Házirend-kulcs létrehozása

A Azure AD B2C bérlőben korábban rögzített titkos kulcsot kell tárolnia.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja. Válassza ki a **címtár és előfizetés szűrőt** a felső menüben, és válassza ki a bérlőt tartalmazó könyvtárat.
3. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
4. Az Áttekintés lapon válassza az **identitási élmény keretrendszert**.
5. Válassza a **szabályzat kulcsok** lehetőséget, majd kattintson a **Hozzáadás**gombra.
6. A **Beállítások**területen válassza `Manual`a lehetőséget.
7. Adja meg a szabályzat kulcsának **nevét** . Például: `TwitterSecret`. A rendszer `B2C_1A_` automatikusan hozzáadja az előtagot a kulcs nevéhez.
8. A **Secret (titkos kulcs**) mezőben adja meg a korábban rögzített ügyfél-titkot.
9. A **kulcshasználat**beállításnál válassza `Encryption`a elemet.
10. Kattintson a **Create** (Létrehozás) gombra.

## <a name="add-a-claims-provider"></a>Jogcím-szolgáltató hozzáadása

Ha azt szeretné, hogy a felhasználók Twitter-fiókkal jelentkezzenek be, meg kell adnia a fiókot jogcím-szolgáltatóként, amely Azure AD B2C tud kommunikálni egy végponton keresztül. A végpont olyan jogcímeket biztosít, amelyeket a Azure AD B2C használ annak ellenőrzéséhez, hogy egy adott felhasználó hitelesítve van-e.

A Twitter-fiókot jogcím-szolgáltatóként is meghatározhatja, ha hozzáadja azt a **ClaimsProviders** elemhez a szabályzat bővítmény fájljában.

1. Nyissa meg a *TrustFrameworkExtensions. xml fájlt*.
2. Keresse meg a **ClaimsProviders** elemet. Ha nem létezik, adja hozzá a gyökérelem elemhez.
3. Vegyen fel egy új **ClaimsProvider** a következőképpen:

    ```xml
    <ClaimsProvider>
      <Domain>twitter.com</Domain>
      <DisplayName>Twitter</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Twitter-OAUTH1">
          <DisplayName>Twitter</DisplayName>
          <Protocol Name="OAuth1" />
          <Metadata>
            <Item Key="ProviderName">Twitter</Item>
            <Item Key="authorization_endpoint">https://api.twitter.com/oauth/authenticate</Item>
            <Item Key="access_token_endpoint">https://api.twitter.com/oauth/access_token</Item>
            <Item Key="request_token_endpoint">https://api.twitter.com/oauth/request_token</Item>
            <Item Key="ClaimsEndpoint">https://api.twitter.com/1.1/account/verify_credentials.json?include_email=true</Item>
            <Item Key="ClaimsResponseFormat">json</Item>
            <Item Key="client_id">Your Twitter application consumer key</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_TwitterSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="user_id" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="screen_name" />
            <OutputClaim ClaimTypeReferenceId="email" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="twitter.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. Cserélje le a **client_id** értékét a korábban rögzített fogyasztói kulcsra.
5. Mentse a fájlt.

### <a name="upload-the-extension-file-for-verification"></a>A bővítmény fájljának feltöltése ellenőrzéshez

Most úgy konfigurálta a szabályzatot, hogy Azure AD B2C tudja, hogyan kommunikálhat a Twitter-fiókjával. Próbálja megismételni a szabályzat kiterjesztési fájljának feltöltését, hogy megbizonyosodjon róla, hogy eddig nincs probléma.

1. A Azure AD B2C-bérlő **Egyéni házirendek** lapján válassza a **házirend feltöltése**lehetőséget.
2. **Ha létezik, engedélyezze a házirend felülírását**, majd keresse meg és válassza ki a *TrustFrameworkExtensions. XML* fájlt.
3. Kattintson a **Feltöltés** gombra.

## <a name="register-the-claims-provider"></a>A jogcím-szolgáltató regisztrálása

Ezen a ponton az identitás-szolgáltató beállítása megtörtént, de a regisztrációs vagy bejelentkezési képernyőkön nem érhető el. A szolgáltatás elérhetővé tételéhez hozzon létre egy meglévő sablon felhasználói útvonalát, majd módosítsa úgy, hogy a Twitter-identitás szolgáltatója is legyen.

1. Nyissa meg a *TrustFrameworkBase. XML* fájlt az alapszintű csomagból.
2. A **UserJourney** elem `Id="SignUpOrSignIn"`teljes tartalmának megkeresése és másolása.
3. Nyissa meg a *TrustFrameworkExtensions. xml fájlt* , és keresse meg a **UserJourneys** elemet. Ha az elem nem létezik, vegyen fel egyet.
4. Illessze be a **UserJourney** elem teljes tartalmát, amelyet a **UserJourneys** elem gyermekeiként másolt.
5. Nevezze át a felhasználói út AZONOSÍTÓját. Például: `SignUpSignInTwitter`.

### <a name="display-the-button"></a>A gomb megjelenítése

A **ClaimsProviderSelection** elem hasonló a bejelentkezési vagy bejelentkezési képernyőn lévő Identity Provider gombhoz. Ha hozzáad egy **ClaimsProviderSelection** elemet egy Twitter-fiókhoz, egy új gomb jelenik meg, amikor a felhasználó az oldalon landol.

1. Keresse meg  az Ön által létrehozott `Order="1"` felhasználói útra kiterjedő OrchestrationStep elemet.
2. A **ClaimsProviderSelects**területen adja hozzá a következő elemet. Állítsa a **TargetClaimsExchangeId** értékét egy megfelelő értékre, például `TwitterExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Gomb csatolása egy művelethez

Most, hogy van egy gomb a helyén, össze kell kapcsolni egy művelettel. A művelet, ebben az esetben a Azure AD B2C, hogy egy Twitter-fiókkal kommunikáljon a jogkivonatok fogadásához.

1. Keresse meg  a felhasználói útra `Order="2"` kiterjedő OrchestrationStep.
2. Adja hozzá a következő **ClaimsExchange** elemet, és győződjön meg arról, hogy ugyanazt az értéket használja a **TargetClaimsExchangeId**használt azonosítóhoz:

    ```XML
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAUTH1" />
    ```

    Frissítse a **TechnicalProfileReferenceId** értékét a korábban létrehozott műszaki profil azonosítójával. Például: `Twitter-OAUTH1`.

3. Mentse a *TrustFrameworkExtensions. XML* fájlt, és töltse fel újra az ellenőrzéshez.

## <a name="create-an-azure-ad-b2c-application"></a>Azure AD B2C-alkalmazás létrehozása

Az Azure AD B2c-vel folytatott kommunikáció egy, a bérlőben létrehozott alkalmazáson keresztül történik. Ez a szakasz azokat a választható lépéseket sorolja fel, amelyekkel elvégezheti a tesztelési alkalmazások létrehozását, ha még nem tette meg.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja. Válassza ki a **címtár és előfizetés szűrőt** a felső menüben, és válassza ki a bérlőt tartalmazó könyvtárat.
3. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
4. Válassza az **alkalmazások**lehetőséget, majd válassza a **Hozzáadás**lehetőséget.
5. Adja meg az alkalmazás nevét, például *testapp1*.
6. A **Web App/web API**esetében válassza `Yes`a elemet, majd `https://jwt.ms` adja meg a **Válasz URL-címét**.
7. Kattintson a **Create** (Létrehozás) gombra.

## <a name="update-and-test-the-relying-party-file"></a>A függő entitás fájljának frissítése és tesztelése

Frissítse a függő entitás (RP) fájlját, amely kezdeményezi a létrehozott felhasználói utat.

1. Készítsen másolatot a *SignUpOrSignIn. XML fájlról* a munkakönyvtárában, és nevezze át. Nevezze át például a *SignUpSignInTwitter. XML fájlba*.
2. Nyissa meg az új fájlt, és frissítse a **PolicyId** attribútum értékét a **TrustFrameworkPolicy** egyedi értékkel. Például: `SignUpSignInTwitter`.
3. Frissítse a **PublicPolicyUri** értékét a szabályzat URI azonosítójának értékével. Például:`http://contoso.com/B2C_1A_signup_signin_twitter`
4. Frissítse a **ReferenceId** attribútum értékét a **DefaultUserJourney** -ben, hogy az megfeleljen a létrehozott új felhasználói út azonosítójának (SignUpSignTwitter).
5. Mentse a módosításokat, töltse fel a fájlt, majd válassza ki az új szabályzatot a listában.
6. Győződjön meg arról, hogy a létrehozott Azure AD B2C alkalmazás ki van választva az **alkalmazás kiválasztása** mezőben, majd tesztelje a **Futtatás most**lehetőségre kattintva.
