---
title: Támogatott fiókok az alkalmazásokban (célközönség) – Microsoft Identity platform
description: A célközönségek és az alkalmazások által támogatott fióktípus fogalmi dokumentációja
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad3cd612200dc33fe37036146b00f52073403ecc
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68852043"
---
# <a name="supported-account-types"></a>Támogatott fióktípusok

Ez a cikk azt ismerteti, hogy az alkalmazások milyen típusú fiókokat (más néven célközönségeket) támogatnak.

<!-- This section can be in an include for many of the scenarios (SPA, Web App signing-in users, protecting a Web API, Desktop (depending on the flows), Mobile -->

## <a name="supported-accounts-types-in-microsoft-identity-platform-applications"></a>Támogatott fiókok típusai a Microsoft Identity platform alkalmazásaiban

A Microsoft Azure nyilvános felhőben a legtöbb típusú alkalmazás bejelentkezhet a felhasználók bármelyik hallgatóságával:

- Üzletági (LOB) alkalmazások írásakor a saját szervezetében lévő felhasználókat is bejelentkezhet. Egy ilyen alkalmazást néha **egyetlen bérlőnek**nevezünk.
- Ha Ön ISV-t használ, írhat olyan alkalmazást, amely bejelentkezik a felhasználók számára:

  - Bármely szervezeten belül. Ilyen alkalmazás neve **több-bérlős** webalkalmazás. Néha azt is elolvashatja, hogy a felhasználók munkahelyi vagy iskolai fiókjaikat használják.
  - Munkahelyi vagy iskolai vagy személyes Microsoft-fiók.
  - Csak személyes Microsoft-fiók.
    > [!NOTE]
    > A Microsoft Identity platform jelenleg csak a személyes Microsoft-fiókokat támogatja a **munkahelyi vagy iskolai vagy személyes Microsoft-fiókok**regisztrálásával, majd az alkalmazás kódjában az Azure ad-szolgáltató megadásával korlátozza a bejelentkezést. az alkalmazás létrehozásakor, például `https://login.onmicrosoftonline.com/consumers`:.

- Ha üzleti alkalmazást kíván beírni a fogyasztók alkalmazásba, akkor a Azure AD B2C használatával is bejelentkezhet a felhasználók közösségi identitásával.

## <a name="certain-authentication-flows-dont-support-all-the-account-types"></a>Bizonyos hitelesítési folyamatok nem támogatják az összes számlatípust

Bizonyos típusú fiókok nem használhatók bizonyos hitelesítési folyamatokkal. Például asztali, UWP-alkalmazásokban vagy Daemon-alkalmazásokban:

- Daemon-alkalmazásokat csak Azure Active Directory szervezetekkel lehet használni. Nincs értelme a Microsoft személyes fiókjainak kezeléséhez szükséges Daemon-alkalmazások használatát (a rendszergazda beleegyezik, soha nem lesz megadva).  
- Az integrált Windows-hitelesítési folyamatot csak munkahelyi vagy iskolai fiókkal (a szervezet vagy bármely szervezet) használhatja. Az integrált Windows-hitelesítés tartományi fiókokkal is működik, és megköveteli, hogy a gépek tartományhoz legyenek csatlakoztatva, vagy az Azure AD csatlakoztatva legyen. Ez a folyamat nem értelmezi a személyes Microsoft-fiókokat.
- Az [erőforrás-tulajdonos jelszavának megadása](./v2-oauth-ropc.md) (username/Password) nem használható személyes Microsoft-fiókokkal. A személyes Microsoft-fiókokhoz azonban a felhasználó beleegyezik, hogy az egyes bejelentkezési munkamenetekben hozzáférjen a személyes erőforrásokhoz. Ezért ez a viselkedés nem kompatibilis a nem interaktív folyamatokkal.
- Az eszköz kódjának folyamata még nem működik személyes Microsoft-fiókokkal.

## <a name="supported-account-types-in-national-clouds"></a>Az országos felhőkben támogatott fióktípus

 Az alkalmazások az [országos felhőkben](authentication-national-cloud.md)is bejelentkezhetnek a felhasználókba. A Microsoft személyes fiókjai azonban ezekben a felhőkben nem támogatottak (a felhők definíciója szerint). Ezért csökkenthető a támogatott fióktípus, ezek a felhők, a szervezet (egyetlen bérlő) vagy bármely szervezet (több-bérlős alkalmazások).

## <a name="next-steps"></a>További lépések

- További információ a [Azure Active Directory-](./single-and-multi-tenant-apps.md) beli bérletről
- További információ az [országos felhőkről](./authentication-national-cloud.md)