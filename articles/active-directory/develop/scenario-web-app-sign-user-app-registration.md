---
title: Webes alkalmazás, amely képes bejelentkeztetni a felhasználókat (alkalmazásregisztráció) – a Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre egy webalkalmazást, amely képes bejelentkeztetni a felhasználókat (alkalmazásregisztráció)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0ae638f8cbef29c5d167a3ab59188169cbd934ef
ms.sourcegitcommit: 6e6813f8e5fa1f6f4661a640a49dc4c864f8a6cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2019
ms.locfileid: "67150227"
---
# <a name="web-app-that-signs-in-users---app-registration"></a>Webes alkalmazás, amely képes bejelentkeztetni a felhasználókat – alkalmazás regisztrálása

Jelen lap bemutatja egy webalkalmazás-alkalmazás regisztrációs adatait, hogy jelentkezik be felhasználókat.

Az alkalmazás regisztrálásához használhatja:

- A [webes alkalmazások gyors útmutatók](#register-an-app-using-the-quickstarts) -mellett egy alkalmazás létrehozásának első nagyszerűen, az Azure Portalon a rövid útmutatók gombot tartalmaz **számomra a módosítás végrehajtása**. Ez a gomb segítségével állítsa be a tulajdonságokat kell, még ha egy meglévő alkalmazást. Alkalmazkodjon az értékeket a saját eset ezeket a tulajdonságokat kell. A webes API URL-címe az alkalmazás, valószínűleg történik, különbözik a javasolt alapértelmezett beállítás, amely szintén hatással van az URI-t a kijelentkezési.
- Az Azure Portalon [manuálisan regisztrálhatja alkalmazását](#register-an-app-using-azure-portal)
- PowerShell és a parancssori eszközök

## <a name="register-an-app-using-the-quickstarts"></a>A rövid útmutatók használatával alkalmazás regisztrálása

Ha ez a hivatkozás ugrik, hozhat létre rendszer-indításkori webes alkalmazásainak létrehozását:

- [ASP.NET Core](https://aka.ms/aspnetcore2-1-aad-quickstart-v2)
- [ASP.NET](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs)

### <a name="register-an-app-using-azure-portal"></a>Az Azure portal használatával alkalmazás regisztrálása

> [!NOTE]
> a portál használatához eltér attól függően, ha az alkalmazás fut, a Microsoft Azure nyilvános felhőjében vagy a nemzeti és szuverén felhő. További információkért lásd: [országos felhők](./authentication-national-cloud.md#app-registration-endpoints)

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com). Azt is megteheti jelentkezzen be a országos felhőben tetszőleges Azure-portálon.
1. Ha a fiók lehetőséget biztosít több bérlő hozzáférést, a jobb felső sarokban válassza ki a fiókját, és állítsa be a portál munkamenet a kívánt Azure ad-bérlőben.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatásra, és válassza ki **alkalmazásregisztrációk** > **új regisztrációs**.
1. Amikor megjelenik az **Alkalmazás regisztrálása** lap, adja meg az alkalmazás regisztrációs adatait:
   1. Válassza ki az alkalmazás a támogatott fióktípus esetében (lásd: [támogatott fióktípusok](./v2-supported-account-types.md))
   1. A **Név** szakaszban adja meg az alkalmazás felhasználói számára megjelenített, jelentéssel bíró alkalmazásnevet (például `AspNetCore-WebApp`).
   1. A **átirányítási URI-t**adja hozzá az alkalmazás és az URI-t a cél, amely elfogadja a sikeres hitelesítés után jogkivonatválaszok adja vissza. Például: `https://localhost:44321/`.  Kattintson a **Register** (Regisztrálás) elemre.
1. Válassza ki a **hitelesítési** menüben, majd adja hozzá a következő információkat:
   1. A **válasz URL-cím**, adjon hozzá `https://localhost:44321/signin-oidc`.
   1. Az a **speciális beállítások** szakaszában **kijelentkezési URL-címe** való `https://localhost:44321/signout-oidc`.
   1. A **típusú Implicit engedélyezés**, ellenőrizze **azonosító-jogkivonatokat**.
   1. Kattintson a **Mentés** gombra.

### <a name="register-an-app-using-powershell"></a>PowerShell-lel alkalmazás regisztrálása

> [!NOTE]
> Jelenleg az Azure AD PowerShell csak hoz létre alkalmazást a következő támogatott fióktípusok:
>
> - MyOrg (fiókok csak a szervezeti könyvtárban található)
> - AnyOrg (az összes szervezeti directory-fiókok).
>
> Ha azt szeretné, hogy bejelentkezik felhasználók alkalmazás létrehozása a személyes Microsoft-Accounts (például a Skype, XBox, Outlook.com), először létrehozhat egy több-bérlős alkalmazás (fióktípus esetében támogatott fiókok = bármely szervezeti könyvtárban), majd módosítsa a `signInAudience` tulajdonság az alkalmazásjegyzékben, az Azure Portalról. Ez a lépés a részletek kifejtett [1.3](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-3-AnyOrgOrPersonal#step-1-register-the-sample-with-your-azure-ad-tenant) az ASP.NET Core-oktatóanyag (és a web Apps-alkalmazások bármilyen nyelven is általánosítva).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Alkalmazás kód konfigurációját](scenario-web-app-sign-user-app-configuration.md)
