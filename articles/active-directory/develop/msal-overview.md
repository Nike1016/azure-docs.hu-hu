---
title: Tudnivalók a Microsoft Authentication Library (MSAL) szolgáltatásról | Azure
description: A Microsoft Authentication Library (MSAL) lehetővé teszi az alkalmazások fejlesztői számára a jogkivonatok beszerzését a biztonságos webes API-k meghívásához. Ezek a webes API-k lehetnek a Microsoft Graph, más Microsoft API-k, harmadik féltől származó webes API-k vagy a saját webes API-jai. A MSAL több alkalmazás-architektúrát és platformot is támogat.
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/25/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: d686fad8471064f127e75f10f42f34350c764837
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69532543"
---
# <a name="overview-of-microsoft-authentication-library-msal"></a>A Microsoft Authentication Library (MSAL) áttekintése
A Microsoft Authentication Library (MSAL) lehetővé teszi a [](developer-glossary.md#security-token) fejlesztők számára a tokenek beszerzését a Microsoft Identity platform-végpontból a biztonságos webes API-k eléréséhez. Ezek a webes API-k lehetnek a Microsoft Graph, más Microsoft API-k, harmadik féltől származó webes API-k vagy a saját webes API-jai. A MSAL olyan .NET-, JavaScript-, Android-és iOS-eszközökhöz érhető el, amelyek számos különböző alkalmazás-architektúrát és platformot támogatnak.

A MSAL számos lehetőséget kínál a jogkivonatok lekérésére, egy egységes API-val számos platformon. A MSAL használata a következő előnyöket biztosítja:

* Nem kell közvetlenül használni a OAuth-kódtárakat vagy-kódokat az alkalmazás protokollján.
* Egy felhasználó nevében vagy egy alkalmazás nevében szerzi be a jogkivonatokat (ha alkalmazható a platformra).
* Fenntart egy jogkivonat-gyorsítótárat, és frissíti a jogkivonatokat, amikor közel vannak a lejárathoz. Nem kell kezelnie a token lejáratát a sajátján.
* Segítséget nyújt annak megadásához, hogy az alkalmazás milyen célközönséget jelentkezzen be (szervezete, több szervezethez, munkahelyi és iskolai és személyes Microsoft-fiók, közösségi identitás a Azure AD B2C, a szuverén és az országos felhőkben).
* Segítséget nyújt az alkalmazás konfigurációs fájlokból való beállításához.
* Segítséget nyújt az alkalmazás hibakereséséhez, így végrehajtható kivételeket, naplózást és telemetria tehet közzé.

## <a name="application-types-and-scenarios"></a>Alkalmazások típusai és forgatókönyvei
A MSAL használatával számos alkalmazásból szerezhet be tokent: webalkalmazások, webes API-k, egylapos alkalmazások (JavaScript), mobil és natív alkalmazások, valamint démonok és kiszolgálóoldali alkalmazások. 

A MSAL számos alkalmazási helyzetben használható, többek között az alábbiakat is beleértve:

* [Egyoldalas alkalmazások (JavaScript)](scenario-spa-overview.md) 
* [Webalkalmazás-aláírás a felhasználókban](scenario-web-app-sign-user-overview.md)
* [Webalkalmazás-aláírás a felhasználó nevében, és webes API meghívása a felhasználó nevében](scenario-web-app-call-api-overview.md)
* [Webes API-k védelme, hogy csak a hitelesített felhasználók férhessenek hozzá.](scenario-protected-web-api-overview.md)
* [Webes API, amely egy másik alsóbb rétegbeli webes API-t hív meg a bejelentkezett felhasználó nevében](scenario-web-api-call-api-overview.md)
* [A bejelentkezett felhasználó nevében webes API-t hívó asztali alkalmazás](scenario-desktop-overview.md)
* A [mobil alkalmazás a webes API-t hívja interaktív módon bejelentkezett felhasználó nevében](scenario-mobile-overview.md).
* [Desktop/Service Daemon-alkalmazás a webes API-t saját nevében hívja meg](scenario-daemon-overview.md)

## <a name="languages-and-frameworks"></a>Nyelvek és keretrendszerek

| Erőforrástár | Támogatott platformok és keretrendszerek|
| --- | --- | 
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/>[MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)| .NET-keretrendszer, .NET Core, Xamarin Android, Xamarin iOS, Univerzális Windows-platform|
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/>[MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)| JavaScript/írógéppel keretrendszerek, mint például a AngularJS, az ember. js vagy a Durante. js|
| ![MSAL Android rendszerhez](media/sample-v2-code/logo_Android.png) <br/>[MSAL for Android (előzetes verzió)](https://github.com/AzureAD/microsoft-authentication-library-for-android)|Android|
| ![MSAL iOS rendszerhez](media/sample-v2-code/logo_iOS.png) <br/>[MSAL. Objective-C (előzetes verzió)](https://github.com/AzureAD/microsoft-authentication-library-for-objc)|iOS|
| ![MSAL4J](media/sample-v2-code/logo_java.png) <br/>[MSAL4J előzetes verzió](https://github.com/AzureAD/microsoft-authentication-library-for-java)|Java|

## <a name="differences-between-adal-and-msal"></a>ADAL és MSAL közötti különbségek
Active Directory-hitelesítési tár (ADAL) integrálható az Azure AD for Developers (v 1.0) végponttal, ahol a MSAL a Microsoft Identity platform (v 2.0) végponttal integrálódik. A v 1.0 végpont támogatja a munkahelyi fiókokat, a személyes fiókokat azonban nem. A 2.0-s végpont a személyes Microsoft-fiókok és-munkahelyi fiókok egységesítése egyetlen hitelesítési rendszerbe. Emellett a MSAL-mel is beszerezhetők a Azure AD B2C hitelesítései.

További információért olvassa el az áttelepítés a [MSAL.net-ről a ADAL.net](msal-net-migration.md) -ről és a [MSAL. js-re való Migrálás a ADAL. js-ből](msal-compare-msal-js-and-adal-js.md)című témakört.

            