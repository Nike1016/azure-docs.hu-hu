---
title: ClaimsProviders – az Azure Active Directory B2C |} A Microsoft Docs
description: Adja meg az egyéni szabályzat ClaimsProvider elem Azure Active Directory B2C-t.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 8d2570af6abb34a87ac4c69dd63408c8ec2e8005
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66511522"
---
# <a name="claimsproviders"></a>ClaimsProviders

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Jogcím-szolgáltatóktól tartalmaz [technikai profilok](technicalprofiles.md). Minden jogcím-szolgáltatói rendelkeznie kell egy vagy több technikai profilok –, amelyek meghatározzák a végpontok és a jogcímszolgáltató folytatott kommunikációhoz szükséges protokollok. Jogcím-szolgáltatóktól rendelkezhet több technikai profil. Több technikai profil például definiálható, mert a jogcímszolgáltató támogatja a több protokoll, a különböző képességekkel rendelkező különböző végpontok, vagy más jogcímeket másik megbízhatósági szintű kiadásai. Elképzelhető, hogy egy felhasználói interakciósorozatban szereplő, de nem a másik bizalmas jogcímeket kiadni elfogadható.

```XML
<ClaimsProviders>
  <ClaimsProvider>
    <Domain>Domain name</Domain>
    <DisplayName>Display name</DisplayName>
    <TechnicalProfiles>
      </TechnicalProfile>
        ...
      </TechnicalProfile>
        ...
    </TechnicalProfiles>
  </ClaimsProvider>
  ...
</ClaimsProviders>
```

A **ClaimsProviders** elem tartalmazza a következő elemet:

| Elem | Előfordulás | Leírás |
| ------- | ----------- | ----------- |
| ClaimsProvider | 1:n | Egy akkreditált jogcímszolgáltatótól, amely a különböző felhasználói utak is javítható. |

## <a name="claimsprovider"></a>ClaimsProvider

A **ClaimsProvider** elem tartalmazza gyermekeleme a következők közül:

| Elem | Előfordulás | Leírás |
| ------- | ---------- | ----------- |
| Domain | 0:1 | A jogcím-szolgáltató a tartomány nevét tartalmazó karakterlánc. Például ha a jogcím-szolgáltatói is tartalmaz, a Facebook technikai profilban, a tartománynév Facebook.com weboldalt. Ez a tartománynév a jogcímszolgáltató bírálni a technikai profil által meghatározott összes technikai profil szolgál. A tartomány nevét is lehet hivatkozni egy **domain_hint**. További információkért lásd: a **bejelentkezési átirányítása egy közösségi szolgáltató** szakaszában [állítsa be a közvetlen bejelentkezés az Azure Active Directory B2C használatával](direct-signin.md). |
| displayName | 0:1 | A jogcímszolgáltatótól, a felhasználók számára megjelenített nevét tartalmazó karakterlánc. |
| [TechnicalProfiles](technicalprofiles.md) | 0:1 | Az igényt szolgáltatója által támogatott technikai profilok |

**ClaimsProvider** rendezi, hogyan kapcsolódik egymáshoz a technikai profil a jogcímszolgáltató. Az alábbi példa bemutatja az Azure Active Directory jogcímszolgáltató és az Azure Active Directory technikai profilok:

```XML
<ClaimsProvider>
  <DisplayName>Azure Active Directory</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AAD-Common">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId-NoError">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
      ...
    </TechnicalProfile>
      ...
    <TechnicalProfile Id="AAD-UserWritePasswordUsingObjectId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserWriteProfileUsingObjectId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingObjectId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserWritePhoneNumberUsingObjectId">
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

Az alábbi példa bemutatja a Facebook jogcímszolgáltatótól, az a **Facebook-OAUTH** technikai profil.

```XML
<ClaimsProvider>
  <Domain>facebook.com</Domain>
  <DisplayName>Facebook</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Facebook-OAUTH">
      <DisplayName>Facebook</DisplayName>
      <Protocol Name="OAuth2" />
        ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```
