---
title: Hozzon létre C# ASP.NET Core-webalkalmazás – az Azure App Service |} A Microsoft Docs
description: Ismerje meg, hogyan futtathat webalkalmazásokat az Azure App Service-ben az alapértelmezett üzembe helyezésével C# ASP.NET Core-webalkalmazást.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 09/05/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 2762a81c9f366ad4a5d0d6d87cf04490fc10b23c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60854523"
---
# <a name="create-an-aspnet-core-web-app-in-azure"></a>ASP.NET Core-webalkalmazás létrehozása az Azure-ban

> [!NOTE]
> Ebben a cikkben egy alkalmazást helyezünk üzembe a Windowson futó App Service-ben. A _Linuxon_ futó App Service-ben való üzembe helyezéssel kapcsolatban lásd: [.NET Core-webalkalmazás létrehozása Linuxon futó App Service-ben](./containers/quickstart-dotnetcore.md). 
>

Az [Azure App Service](overview.md) egy hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatás.  Ez a rövid útmutató bemutatja, hogyan az első ASP.NET Core üzembe helyezése az Azure App Service-webalkalmazás. Ha elkészült, egy erőforráscsoportot, App Service-csomag és a egy üzembe helyezett webalkalmazással rendelkező App Service-alkalmazás lesz.

![](./media/app-service-web-get-started-dotnet/web-app-running-live.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez telepítse a <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017-et</a> az **ASP.NET és webfejlesztési** számítási feladattal.

Ha már telepítette a Visual Studio 2017-et:

- Telepítse a legújabb frissítéseket a Visual Studióban a **Help** > **Check for Updates** (Súgó, Frissítések keresése) lehetőségre kattintva.
- Adja hozzá a számítási feladatokat a **Tools** > **Get Tools and Features** (Eszközök, Eszközök és funkciók beszerzése) elemre kattintva.

## <a name="create-an-aspnet-core-web-app"></a>ASP.NET Core-webalkalmazás létrehozása

Hozzon létre egy projektet a Visual Studióban a **File > New > Project** (Fájl > Új > Projekt) lehetőség kiválasztásával. 

A **New Project** (Új projekt) párbeszédpanelen válassza a **Visual C# > Web > ASP.NET Core Web Application** (Visual C# > Web > ASP.NET Core-webalkalmazás) lehetőséget.

Nevezze el az alkalmazást _myFirstAzureWebApp_ néven, majd kattintson az **OK** gombra.
   
![A New Project (Új projekt) párbeszédpanel](./media/app-service-web-get-started-dotnet/new-project.png)

Bármilyen típusú ASP.NET Core-webalkalmazást üzembe helyezhet az Azure-ban. Ehhez a rövid útmutatóhoz válassza a **Web Application** (Webalkalmazás) sablont, és ügyeljen arra, hogy a hitelesítés beállítása **No Authentication** (Nincs hitelesítés) legyen.
      
Kattintson az **OK** gombra.

![A New ASP.NET Project (Új ASP.NET-projekt) párbeszédpanel](./media/app-service-web-get-started-dotnet/razor-pages-aspnet-dialog.png)

A menüből válassza a **Debug > Start without Debugging** (Hibakeresés > Indítás hibakeresés nélkül) lehetőséget a webalkalmazás helyi futtatásához.

![Az alkalmazás futtatása helyileg](./media/app-service-web-get-started-dotnet/razor-web-app-running-locally.png)

## <a name="launch-the-publish-wizard"></a>A közzétételi varázsló elindítása

A **Solution Explorer** (Megoldáskezelő) lapon kattintson a jobb gombbal a **myFirstAzureWebApp** projektre, és válassza a **Publish** (Közzététel) elemet.

![Közzététel a Megoldáskezelőből](./media/app-service-web-get-started-dotnet/right-click-publish.png)

A közzétételi varázsló automatikusan elindul. Válassza az **App Service** > **Publish** (App Service, Közzététel) lehetőséget a **Create App Service** (App Service létrehozása) párbeszédpanel megnyitásához.

![Közzététel a projekt áttekintő oldaláról](./media/app-service-web-get-started-dotnet/publish-to-app-service.png)

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

A **Create App Service** (App Service létrehozása) párbeszédpanelen kattintson az **Add an account** (Fiók hozzáadása) parancsra, majd jelentkezzen be az Azure-előfizetésébe. Ha már bejelentkezett, válassza ki a kívánt fiókot a legördülő listából.

> [!NOTE]
> Ha már be van jelentkezve, akkor még ne válassza a **Create** (Létrehozás) lehetőséget.
>
   
![Bejelentkezés az Azure-ba](./media/app-service-web-get-started-dotnet/sign-in-azure.png)

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

[!INCLUDE [resource group intro text](../../includes/resource-group.md)]

Az **Erőforráscsoport** mellett válassza az **Új** elemet.

Nevezze el a **myResourceGroup** erőforráscsoportot, majd kattintson az **OK** gombra.

## <a name="create-an-app-service-plan"></a>App Service-csomag létrehozása

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

A **Szolgáltatási csomag** mellett válassza az **Új** elemet. 

A **Szolgáltatási csomag konfigurálása** párbeszédpanelen a képernyőképet követve használja a táblázatban szereplő beállításokat.

![App Service-csomag létrehozása](./media/app-service-web-get-started-dotnet/configure-app-service-plan.png)

| Beállítás | Ajánlott érték | Leírás |
|-|-|-|
|App Service-csomag| myAppServicePlan | Az App Service-csomag neve. |
| Location egység | Nyugat-Európa | Az adatközpont, ahol a webalkalmazást üzemeltetik. |
| Méret | Ingyenes | A [tarifacsomag](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) meghatározza az üzemeltetési funkciókat. |

Kattintson az **OK** gombra.

## <a name="create-and-publish-the-web-app"></a>A webapp létrehozása és közzététele

Az **Alkalmazás neve** mezőben adjon meg egy egyedi nevet az alkalmazás számára (érvényes karakterek: `a-z`, `0-9` és `-`), vagy fogadja el az automatikusan létrehozott egyedi nevet. A webalkalmazás URL-címe `http://<app_name>.azurewebsites.net`, amelyben az `<app_name>` az alkalmazás neve.

A **Create** (Létrehozás) gombra kattintva hozzákezdhet az Azure-erőforrások létrehozásához.

![Az alkalmazás nevének konfigurálása](./media/app-service-web-get-started-dotnet/web-app-name.png)

Miután a varázsló befejeződött, közzéteszi az ASP.NET Core-webalkalmazást az Azure-ban, majd elindítja azt az alapértelmezett böngészőben.

![Közzétett ASP.NET-webapp az Azure-ban](./media/app-service-web-get-started-dotnet/web-app-running-live.png)

A [létrehozási és közzétételi lépésben](#create-and-publish-the-web-app) megadott alkalmazásnév lesz az URL-előtag a következő formátumban: `http://<app_name>.azurewebsites.net`.

Gratulálunk, az ASP.NET Core-webalkalmazás most már elérhető az Azure App Service-ben.

## <a name="update-the-app-and-redeploy"></a>Az alkalmazás frissítése és ismételt üzembe helyezése

A **Solution Explorer** (Megoldáskezelő) felületén nyissa meg a _Pages/Index.cshtml_ fájlt.

Cserélje le a két `<div>` címkét az alábbi kódra:

```HTML
<div class="jumbotron">
    <h1>ASP.NET in Azure!</h1>
    <p class="lead">This is a simple app that we’ve built that demonstrates how to deploy a .NET app to Azure App Service.</p>
</div>
```

Az Azure-beli ismételt üzembe helyezéshez kattintson a jobb gombbal a **myFirstAzureWebApp** projektre a **Solution Explorer** (Megoldáskezelő) lapon, és válassza a **Publish** (Közzététel) elemet.

Az összefoglaló közzétételi oldalon válassza a **Publish** (Közzététel) elemet.
![A Visual Studio összefoglaló közzétételi oldala](./media/app-service-web-get-started-dotnet/publish-summary-page.png)

Miután a közzététel befejeződött, a Visual Studio tallózza a webalkalmazás URL-címét.

![Frissített ASP.NET-webapp az Azure-ban](./media/app-service-web-get-started-dotnet/web-app-running-live-updated.png)

## <a name="manage-the-azure-app"></a>Az Azure-alkalmazás kezelése

Ugorjon az <a href="https://portal.azure.com" target="_blank">Azure Portalra</a>, és felügyelje a létrehozott webalkalmazást.

A bal oldali menüben válassza ki a **App Services**, majd válassza ki az Azure-alkalmazás neve.

![Navigálás a portálon egy Azure-alkalmazáshoz](./media/app-service-web-get-started-dotnet/access-portal.png)

Megtekintheti a webalkalmazás Áttekintés oldalát. Itt elvégezhet olyan alapszintű felügyeleti feladatokat, mint a tallózás, leállítás, elindítás, újraindítás és törlés. 

![Az App Service panel az Azure Portalon](./media/app-service-web-get-started-dotnet/web-app-blade.png)

A bal oldali menü az alkalmazás konfigurálásához biztosít különböző oldalakat. 

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [ASP.NET Core és SQL Database](app-service-web-tutorial-dotnetcore-sqldb.md)
