---
title: Hozzáférési token átadása egy egyéni szabályzaton keresztül az alkalmazáshoz Azure Active Directory B2C
description: Megtudhatja, hogyan adhat hozzáférési jogkivonatot a OAuth 2.0-s identitás-szolgáltatók számára jogcímként egy egyéni szabályzattal az alkalmazásához Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: b6795af0829a288c36cad5b848fed50a99dc1bfc
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69510131"
---
# <a name="pass-an-access-token-through-a-custom-policy-to-your-application-in-azure-active-directory-b2c"></a>Hozzáférési token átadása egy egyéni szabályzaton keresztül az alkalmazáshoz Azure Active Directory B2C

A Azure Active Directory B2C (Azure AD B2C) [Egyéni szabályzata](active-directory-b2c-get-started-custom.md) lehetővé teszi az alkalmazás felhasználói számára, hogy regisztráljon vagy jelentkezzen be egy identitás-szolgáltatóval. Ha ez történik, Azure AD B2C [hozzáférési](active-directory-b2c-reference-tokens.md) jogkivonatot kap az identitás-szolgáltatótól. Azure AD B2C a token használatával kéri le a felhasználó adatait. A jogcímek és a kimeneti jogcímek egyéni szabályzatba való felvételével a jogkivonatot átadja a Azure AD B2Cban regisztrált alkalmazásoknak.

Azure AD B2C támogatja a [OAuth 2,0](active-directory-b2c-reference-oauth-code.md) és az [OpenID Connect](active-directory-b2c-reference-oidc.md) Identity Providers hozzáférési jogkivonatának átadását. Az összes többi Identity Provider esetében a rendszer üresen adja vissza a jogcímet.

## <a name="prerequisites"></a>Előfeltételek

* Az egyéni házirend egy OAuth 2,0 vagy OpenID Connect Identity szolgáltatóval van konfigurálva.

## <a name="add-the-claim-elements"></a>Jogcím-elemek hozzáadása

1. Nyissa meg a *TrustframeworkExtensions. XML* fájlt, és adja hozzá a következő **claimType** elemet `identityProviderAccessToken` egy azonosítóval a **ClaimsSchema** elemhez:

    ```XML
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="identityProviderAccessToken">
          <DisplayName>Identity Provider Access Token</DisplayName>
          <DataType>string</DataType>
          <AdminHelpText>Stores the access token of the identity provider.</AdminHelpText>
        </ClaimType>
        ...
      </ClaimsSchema>
    </BuildingBlocks>
    ```

2. Adja hozzá a **OutputClaim** elemet a **kivonatjogcím** elemhez minden olyan OAuth 2,0-identitáshoz, amelyhez hozzá szeretné adni a hozzáférési jogkivonatot. A következő példa a Facebook technikai profiljához hozzáadott elemet mutatja be:

    ```XML
    <ClaimsProvider>
      <DisplayName>Facebook</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Facebook-OAUTH">
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="identityProviderAccessToken" PartnerClaimType="{oauth2:access_token}" />
          </OutputClaims>
          ...
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

3. Mentse a *TrustframeworkExtensions. XML* fájlt.
4. Nyissa meg a függő entitás házirend-fájlját, például *SignUpOrSignIn. XML*fájlt, és adja hozzá a **OutputClaim** elemet a **kivonatjogcím**:

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <TechnicalProfile Id="PolicyProfile">
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="identityProviderAccessToken" PartnerClaimType="idp_access_token"/>
        </OutputClaims>
        ...
      </TechnicalProfile>
    </RelyingParty>
    ```

5. Mentse a házirend-fájlt.

## <a name="test-your-policy"></a>A szabályzat tesztelése

Az alkalmazások Azure ad B2C-ben történő tesztelésekor hasznos lehet, hogy a Azure ad B2C token visszaadja `https://jwt.ms` a jogcímeket, hogy áttekintse a benne lévő jogcímeket.

### <a name="upload-the-files"></a>A fájlok feltöltése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Győződjön meg arról, hogy az Azure AD B2C bérlőjét tartalmazó könyvtárat használja, majd a felső menüben kattintson a **könyvtár + előfizetés** szűrőre, és válassza ki a bérlőt tartalmazó könyvtárat.
3. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
4. Válassza az **identitási élmény keretrendszert**.
5. Az egyéni házirendek lapon kattintson a **házirend feltöltése**elemre.
6. **Ha létezik**, válassza a házirend felülírása lehetőséget, majd keresse meg és válassza ki a *TrustframeworkExtensions. XML* fájlt.
7. Válassza a **Feltöltés** lehetőséget.
8. Ismételje meg az 5 – 7. lépést a függő entitás fájljánál (például *SignUpOrSignIn. XML*).

### <a name="run-the-policy"></a>Szabályzat futtatása

1. Nyissa meg a módosított szabályzatot. Például: *B2C_1A_signup_signin*.
2. **Alkalmazás**esetén válassza ki a korábban regisztrált alkalmazást. Az alábbi példában szereplő token megjelenítéséhez a **Válasz URL-címének** meg `https://jwt.ms`kell jelennie.
3. Válassza a **Futtatás most**lehetőséget.

    Az alábbi példához hasonlónak kell megjelennie:

    ![Dekódolású token a jwt.ms-ben a idp_access_token Block kiemelve](./media/idp-pass-through-custom/idp-pass-through-custom-token.PNG)

## <a name="next-steps"></a>További lépések

További információ a tokenekről: [Azure Active Directory B2C jogkivonat-hivatkozás](active-directory-b2c-reference-tokens.md).
