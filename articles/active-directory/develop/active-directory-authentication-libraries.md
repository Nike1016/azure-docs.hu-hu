---
title: Azure Active Directory hitelesítési kódtárak | Microsoft Docs
description: Az Azure AD Authentication Library (ADAL) lehetővé teszi az ügyfélalkalmazások fejlesztői számára, hogy egyszerűen hitelesítsék a felhasználókat a felhőben vagy a helyszíni Active Directory (AD), majd hozzáférési jogkivonatokat szerezzenek be az API-hívások biztonságossá tételéhez.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 2e4fc79a-0285-40be-8c77-65edee408a22
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb1cd062384bf75e6f83380c172f43244e737db0
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68853480"
---
# <a name="azure-active-directory-authentication-libraries"></a>Azure Active Directory hitelesítési kódtárak

A Azure Active Directory Authentication Library (ADAL) 1.0-s verziója lehetővé teszi az alkalmazások fejlesztői számára, hogy hitelesítsék a felhasználókat a felhőben vagy a helyszíni Active Directoryban (AD), valamint jogkivonatokat szerezzenek be az API-hívások biztonságossá tételéhez. A ADAL megkönnyíti a fejlesztők számára a következő funkciókat:

- A hozzáférési jogkivonatokat tároló konfigurálható jogkivonat-gyorsítótár és a jogkivonatok frissítése
- Automatikus jogkivonat-frissítés, ha egy hozzáférési jogkivonat lejár, és rendelkezésre áll egy frissítési jogkivonat.
- Aszinkron metódus-hívások támogatása

> [!NOTE]
> Az Azure AD v 2.0 kódtárait (MSAL) keresi? A [MSAL-függvénytár útmutatójának](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries)kifizetése.
>
>

## <a name="microsoft-supported-client-libraries"></a>Microsoft által támogatott ügyféloldali kódtárak

| Platform | Erőforrástár | Letöltés | Forráskód | Minta | Hivatkozás
| --- | --- | --- | --- | --- | --- |
| .NET-ügyfél, Windows áruház, UWP, Xamarin iOS és Android |ADAL .NET v3 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) |[GitHubon](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet) | [Asztali alkalmazás](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-dotnet) |[Referencia](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory?view=azure-dotnet) |
| .NET-ügyfél, Windows áruház, Windows Phone-telefon 8,1 |ADAL .NET v2 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/2.28.4) |[GitHubon](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/releases/tag/v2.28.4) | [Asztali alkalmazás](https://github.com/AzureADQuickStarts/NativeClient-DotNet/releases/tag/v2.X) | |
| JavaScript |ADAL.js |[GitHubon](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[GitHubon](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[Egyoldalas alkalmazás](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) | |
| iOS, macOS |ADAL |[GitHubon](https://github.com/AzureAD/azure-activedirectory-library-for-objc/releases) |[GitHubon](https://github.com/AzureAD/azure-activedirectory-library-for-objc) |[iOS app](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-ios) | [Referencia](http://cocoadocs.org/docsets/ADAL/2.5.1/)|
| Android |ADAL |[Maven 3](https://search.maven.org/search?q=g:com.microsoft.aad+AND+a:adal&core=gav) |[GitHubon](https://github.com/AzureAD/azure-activedirectory-library-for-android) |[Android-alkalmazás](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-android) | [JavaDocs](https://javadoc.io/doc/com.microsoft.aad/adal/)|
| Node.js |ADAL |[npm](https://www.npmjs.com/package/adal-node) |[GitHubon](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs) | [Node.Js-webalkalmazás](https://github.com/Azure-Samples/active-directory-node-webapp-openidconnect)|[Referencia](https://docs.microsoft.com/javascript/api/adal-node/?view=azure-node-latest) |
| Java |ADAL4J |[Maven 3](https://search.maven.org/#search%7Cga%7C1%7Ca%3Aadal4j%20g%3Acom.microsoft.azure) |[GitHubon](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[Java webalkalmazások](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect) |[Referencia](https://javadoc.io/doc/com.microsoft.azure/adal4j) |
| Python |ADAL |[GitHubon](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[GitHubon](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[Python-webalkalmazás](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi) |[Referencia](https://adal-python.readthedocs.io/) |

## <a name="microsoft-supported-server-libraries"></a>Microsoft által támogatott kiszolgálói kódtárak

| Platform | Erőforrástár | Letöltés | Forráskód | Minta | Hivatkozás
| --- | --- | --- | --- | --- | --- |
| .NET |OWIN a AzureAD|[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[GitHubon](https://github.com/aspnet/AspNetKatana/tree/dev/src/Microsoft.Owin.Security.ActiveDirectory) |[MVC App](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapp-dotnet) | |
| .NET |OWIN a OpenIDConnect |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[GitHubon](https://github.com/aspnet/AspNetKatana/tree/dev/src/Microsoft.Owin.Security.OpenIdConnect) |[Webalkalmazás](https://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet) | |
| .NET |A WS-Federation OWIN |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) |[GitHubon](https://github.com/aspnet/AspNetKatana/tree/dev/src/Microsoft.Owin.Security.WsFederation) |[MVC Web App](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) | |
| .NET |A .NET 4,5 azonosító protokoll bővítményei |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Protocol.Extensions) |[GitHubon](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| .NET |JWT kezelő a .NET 4,5-hez |[NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[GitHubon](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| Node.js |Azure AD Passport |[npm](https://www.npmjs.com/package/passport-azure-ad) |[GitHubon](https://github.com/AzureAD/passport-azure-ad) | [Webes API](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapi-nodejs)| |

## <a name="scenarios"></a>Forgatókönyvek

Íme három gyakori forgatókönyv a ADAL használatához egy távoli erőforráshoz hozzáférő ügyfélen:

### <a name="authenticating-users-of-a-native-client-application-running-on-a-device"></a>Eszközön futó natív ügyfélalkalmazás felhasználóinak hitelesítése

Ebben az esetben a fejlesztőnek olyan mobil ügyfél-vagy asztali alkalmazásra van szüksége, amelynek hozzá kell férnie egy távoli erőforráshoz, például egy webes API-hoz. A webes API nem engedélyezi a névtelen hívásokat, és a nevet hitelesített felhasználó kontextusában kell meghívni. A webes API előre konfigurálva van, hogy megbízzon egy adott Azure AD-bérlő által kiadott hozzáférési jogkivonatokban. Az Azure AD előre konfigurálva van, hogy kiadja az adott erőforráshoz tartozó hozzáférési jogkivonatokat. A webes API-nak az ügyféltől való meghívásához a fejlesztő a ADAL használatával könnyíti meg az Azure AD-vel való hitelesítést. A ADAL használatának legbiztonságosabb módja az, hogy a felhasználói felületet a felhasználói hitelesítő adatok összegyűjtésére használja (ez a böngészőablakban jelenik meg).

A ADAL megkönnyíti a felhasználó hitelesítését, hozzáférési jogkivonat beszerzését és frissítési tokent az Azure AD-ből, majd a hozzáférési jogkivonat használatával meghívja a webes API-t.

Az Azure AD-hitelesítéssel ezt a forgatókönyvet bemutató mintakód: [natív ÜGYFÉLOLDALI WPF-alkalmazás a webes API-hoz](https://github.com/azureadsamples/nativeclient-dotnet).

### <a name="authenticating-a-confidential-client-application-running-on-a-web-server"></a>Webkiszolgálón futó bizalmas ügyfélalkalmazás hitelesítése

Ebben az esetben a fejlesztőnek egy olyan kiszolgálón futó alkalmazásnak kell futnia, amely egy távoli erőforráshoz, például egy webes API-hoz férhet hozzá. A webes API nem engedélyezi a névtelen hívásokat, ezért egy engedélyezett szolgáltatásból kell hívni. A webes API előre konfigurálva van, hogy megbízzon egy adott Azure AD-bérlő által kiadott hozzáférési jogkivonatokban. Az Azure AD előre konfigurálva van, hogy kiadja az adott erőforráshoz tartozó hozzáférési jogkivonatokat az ügyfél hitelesítő adataival (ügyfél-AZONOSÍTÓval és titkos kulccsal). A ADAL megkönnyíti a szolgáltatás hitelesítését az Azure AD-ben, amely a webes API meghívásához használható hozzáférési tokent AD vissza. A ADAL a hozzáférési token élettartamának kezelését is kezeli a gyorsítótárazással, és szükség szerint megújítja azt. A forgatókönyvet bemutató mintakód: [Daemon Console Application to web API](https://github.com/AzureADSamples/Daemon-DotNet).

### <a name="authenticating-a-confidential-client-application-running-on-a-server-on-behalf-of-a-user"></a>Kiszolgálón futó bizalmas ügyfélalkalmazás hitelesítése egy felhasználó nevében

Ebben az esetben a fejlesztőnek olyan kiszolgálón futó webalkalmazása van, amely egy távoli erőforráshoz, például egy webes API-hoz férhet hozzá. A webes API nem engedélyezi a névtelen hívásokat, ezért a hitelesített felhasználó nevében egy engedélyezett szolgáltatásból kell meghívni. A webes API előre konfigurálva van egy adott Azure AD-bérlő által kiadott hozzáférési jogkivonatok megbízhatóságára, és az Azure AD előre konfigurálva van ahhoz, hogy az adott erőforráshoz hozzáférési jogkivonatokat bocsásson ki egy, az ügyfél hitelesítő adatait tartalmazó szolgáltatáshoz. Miután a felhasználó hitelesítése megtörtént a webalkalmazásban, az alkalmazás megkapja az Azure AD-ben a felhasználóhoz tartozó engedélyezési kódot. A webalkalmazás ezután a ADAL használatával beszerezhet egy hozzáférési jogkivonatot és frissítési jogkivonatot a felhasználó nevében az Azure AD-ből az alkalmazáshoz társított engedélyezési kóddal és ügyfél-hitelesítő adatokkal. Ha a webalkalmazás a hozzáférési jogkivonat birtokában van, akkor a jogkivonat lejárata után meghívhatja a webes API-t. Ha a jogkivonat lejár, a webalkalmazás a ADAL használatával új hozzáférési jogkivonatot kérhet le a korábban kapott frissítési jogkivonat használatával. A forgatókönyvet bemutató mintakód esetében tekintse meg a [natív ügyfél webes API-hoz webes API-hoz](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof)című témakört.

## <a name="see-also"></a>Lásd még:

- [A Azure Active Directory fejlesztői útmutatója](v1-overview.md)
- [Az Azure Active Directory hitelesítési forgatókönyvei](authentication-scenarios.md)
- [Azure Active Directory kód mintái](sample-v1-code.md)
