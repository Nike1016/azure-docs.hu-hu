---
title: Azure Active Directory fejlesztők számára (v 1.0) – áttekintés
description: Ez a cikk áttekintést nyújt a Microsoft munkahelyi és iskolai fiókjainak a Azure Active Directory v 1.0 végpont és platform használatával történő aláírásáról.
services: active-directory
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 5c872c89-ef04-4f4c-98de-bc0c7460c7c2
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/24/2018
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 91f406198418571ec0d2c4d488909d3baaa494b1
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68834668"
---
# <a name="azure-active-directory-for-developers-v10-overview"></a>Azure Active Directory fejlesztők számára (v 1.0) – áttekintés

Az Azure Active Directory (Azure AD) egy olyan felhőalapú identitásszolgáltatás, amely lehetővé teszi a fejlesztők számára az olyan alkalmazások létrehozását, amelyek biztosítják a munkahelyi vagy iskolai Microsoft-fiókkal rendelkező felhasználók biztonságos bejelentkeztetését. Az Azure AD egyaránt támogatja egybérlős, üzletági (LOB) alkalmazások, illetve a több-bérlős alkalmazások fejlesztését. Az alapszintű bejelentkezés mellett az Azure AD lehetővé teszi az alkalmazások számára, hogy Microsoft API-kat (pl. [Microsoft Graph](https://docs.microsoft.com/graph/overview)) és az Azure AD platformon alapuló egyéni API-kat egyaránt hívhassanak. Ebben a dokumentációban megtekintheti, hogyan történik az Azure AD-támogatás hozzáadása az alkalmazásához iparági szabvány szerinti protokollok (pl. OAuth2.0 és OpenID Connect) használatával.

> [!NOTE]
> Az oldal tartalmának nagy része a 1.0-s végpontra és platformra koncentrál, amely kizárólag a Microsoft munkahelyi vagy iskolai fiókjait támogatja. Ha felhasználói vagy személyes Microsoft-fiókokat szeretne bejelentkezni, tekintse meg a [v 2.0-végpont és-platform](v2-overview.md)információit. A v 2.0 végpontja olyan alkalmazások egységes fejlesztői felületét kínálja, amelyek az összes Microsoft-identitást szeretnék bejelentkezni.

| | |
| --- | --- |
|[Hitelesítési alapok](authentication-scenarios.md) | Bevezetés az Azure AD-vel történő hitelesítés használatába. |
|[Alkalmazástípusok](app-types.md) | Áttekintés az Azure AD által támogatott hitelesítési forgatókönyvekről. |
| | |

## <a name="get-started"></a>Bevezetés

A 1.0-s verzióban elérhető gyors útmutatók és oktatóanyagok végigvezetik az alkalmazások az előnyben részesített platformon való létrehozásán az Azure AD Authentication Library (ADAL) SDK használatával. Első lépésként tekintse meg a [Microsoft Identity platform (Azure Active Directory for Developers)](index.yml) **1.0** -s és 1.0-s **verziójának oktatóanyagait** .

## <a name="how-to-guides"></a>Útmutatók

Az Azure AD leggyakoribb feladatainak részletes információit és bemutatóit lásd a **v 1.0 útmutatók** című részben.

## <a name="reference-topics"></a>Referencia-témakörök

A következő cikkekben az API-król, a protokollüzenetekről és az Azure AD által használt kifejezésekről találhat részletes információkat.

|                                                                                   | |
| ----------------------------------------------------------------------------------| --- |
| [Hitelesítési tárak (ADAL)](active-directory-authentication-libraries.md)   | Áttekintés az Azure AD által támogatott kódtárakról és SDK-król. |
| [Kódminták](sample-v1-code.md)                                  | Az Azure AD összes kódmintáját tartalmazó lista. |
| [Szószedet](developer-glossary.md)                                      | A jelen dokumentációban használt fogalmak terminológiája és meghatározásai. |
|  |  |


[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
