---
title: Az ASP.NET-webalkalmazás monitorozása az Azure Application Insights segítségével | Microsoft Docs
description: Ez a gyors beállítása ASP.NET-WebApp figyelése az Application insights segítségével
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 06/26/2019
ms.service: application-insights
ms.custom: mvc
ms.topic: quickstart
manager: carmonm
ms.openlocfilehash: 6d26cedb0dd836846d7e9f00539fe101c944f478
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442214"
---
# <a name="start-monitoring-your-aspnet-web-application"></a>Az ASP.NET-alkalmazás monitorozásának indítása

Az Azure Application Insights segítségével egyszerűen monitorozhatja webalkalmazása rendelkezésre állását, teljesítményét és használatát.  Emellett egyszerűen azonosíthatja és diagnosztizálhatja az alkalmazás hibáit anélkül, hogy meg kellene várnia, amíg egy felhasználó jelenti azokat.  Az Application Insightsból az alkalmazás teljesítményéről és hatékonyságáról gyűjtött információkkal tájékozott döntéseket hozhat az alkalmazás karbantartásával és továbbfejlesztésével kapcsolatban.

Ez a rövid útmutató bemutatja, hogyan adhatja hozzá az Application Insightst a meglévő ASP.NET-webalkalmazáshoz, és hogyan indíthatja el az élő statisztika monitorozását, amely az alkalmazás elemzésére használható számos módszer egyike. Ha nem rendelkezik ASP.NET-webalkalmazás, egy következő hozhat létre a [hozzon létre egy ASP.NET webes alkalmazás – rövid útmutató](../../app-service/app-service-web-get-started-dotnet-framework.md).

## <a name="prerequisites"></a>Előfeltételek
A gyorsútmutató elvégzéséhez:

- Telepítés [Visual Studio 2019](https://www.visualstudio.com/downloads/) a következő számítási feladatokkal:
    - ASP.NET és webfejlesztés
    - Azure-fejlesztés


Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="enable-application-insights"></a>Az Application Insights engedélyezése

1. Nyissa meg a projekt a Visual Studio 2019.
2. Válassza **Az Application Insights konfigurálása** elemet a Projekt menüben. A Visual Studio hozzáadja az Application Insights SDK-t az alkalmazásához.

    > [!IMPORTANT]
    > Az Application Insights hozzáadásának folyamata ASP.NET-sablontípusonként változik. Az **üres** vagy az **Azure Mobile App** sablon használata esetén válassza a **Projekt** > **Application Insights Telemetria hozzáadása** lehetőséget. Minden más ASP.NET-sablonnal kapcsolatban tekintse át a fenti lépésben található utasításokat. 

3. Válassza a **Get Started** (Első lépések) lehetőséget (a Visual Studio korábbi verzióiban ehelyett a **Start Free** (Kezdje meg ingyen) gomb található).

    ![Application Insights hozzáadása a Visual Studióhoz](./media/quick-monitor-portal/add-application-insights-b.png)

4. Válassza ki előfizetését, és kattintson a **Register** (Regisztráció) gombra.

5. Futtassa az alkalmazást a **Hibakeresés** menü **Hibakeresés indítása** elemének kiválasztásával vagy az F5 billentyű lenyomásával.

## <a name="confirm-app-configuration"></a>Alkalmazáskonfiguráció jóváhagyása

Az Application Insights a futtatás helyétől függetlenül telemetriai adatokat gyűjt az alkalmazásról. Az adatok megjelenítéséhez hajtsa végre az alábbi lépéseket.

1. Az Application Insights megnyitásához kattintson a **View (Nézet)**  -> **Other Windows (Egyéb ablakok)**  -> **Application Insights Search (Application Insights-keresés)** elemre.  Megjelenik az aktuális munkamenetből származó telemetria.<BR><br>![Telemetria a Visual Studióban](./media/quick-monitor-portal/telemetry-in-vs.png)

2. A kérés részleteinek megtekintéséhez kattintson a lista első kérésére (ebben a példában: GET Home/Index). Figyelje meg, hogy az állapotkód és a válaszidő is megtalálható a részletek között, a kérésre vonatkozó más értékes információkkal együtt.<br><br>![Válasz részletei a Visual Studióban](media/quick-monitor-portal/request-details.png)

## <a name="start-monitoring-in-the-azure-portal"></a>Monitorozás indítása az Azure Portalon

Most megnyithatja az Application Insightst az Azure Portalon a futó alkalmazás különböző részleteinek megtekintéséhez.

1. Bontsa ki a **csatlakoztatott szolgáltatás** mappa (felhőbeli és plug ikon) a Megoldáskezelőben kattintson a jobb gombbal a **Application Insights** mappába, és kattintson **Application Insights portál megnyitása** .  Megjelenik számos lehetőség és néhány információ az alkalmazásról.

    ![Alkalmazástérkép](media/quick-monitor-portal/04-overview.png)

2. Kattintson az **Alkalmazástérkép** elemre az alkalmazás-összetevők függőségi viszonyait mutató vizuális elrendezés megjelenítéséhez.  Minden egyes összetevőnél megjelennek a KPI-k, például a terhelés, a teljesítmény, a hibák és a riasztások.

    ![Alkalmazástérkép](media/quick-monitor-portal/05-appmap.png)

3. Kattintson a a **Alkalmazáselemzés** ikon ![Alkalmazástérkép](media/quick-monitor-portal/app-viewinlogs-icon.png) **naplók (Analytics) nézetében** egy alkalmazás-összetevőket. Ez megnyitja **naplók (Analytics)** , amely egy részletes lekérdezési nyelvet biztosít, az Application Insights által gyűjtött adatok elemzéséhez. Esetünkben most egy lekérdezés jön létre, amely a kérések számát egy diagramon jeleníti meg. A további adatok elemzéséhez írhat saját lekérdezéseket is.

    ![Elemzés](media/quick-monitor-portal/6viewanalytics.png)

4. Kattintson a **élő metrikák Stream** vizsgálja meg a bal oldali alatt. Ezzel élő statisztikát jeleníthet meg az alkalmazásról futás közben. Ez a bejövő kérések számával, az adott kérések időtartamával, valamint az esetleges hibákkal kapcsolatos adatokat tartalmaz. Emellett a kritikus teljesítménymutatókat is figyelemmel kísérheti, például a processzor- és memóriahasználatot.

    ![Élő stream](media/quick-monitor-portal/7livemetrics.png)

    Ha készen áll az alkalmazás Azure-on történő üzemeltetésére, most már közzéteheti. Kövesse az [ASP.NET-webalkalmazás létrehozását ismertető rövid útmutató](../../app-service/app-service-web-get-started-dotnet.md#update-the-app-and-redeploy) lépéseit.

5. Ha a Visual Studiót használja az Application Insights-monitorozáshoz, automatikusan hozzáadhatja az ügyféloldali monitorozást. Ha egy alkalmazáshoz manuálisan szeretné hozzáadni az ügyféloldali monitorozást, adja hozzá a következő JavaScript-kódrészletet:

```html
<!-- 
To collect user behavior analytics about your application, 
insert the following script into each page you want to track.
Place this code immediately before the closing </head> tag,
and before any other scripts. Your first data will appear 
automatically in just a few seconds.
-->
<script type="text/javascript">
var appInsights=window.appInsights||function(a){
  function b(a){c[a]=function(){var b=arguments;c.queue.push(function(){c[a].apply(c,b)})}}var c={config:a},d=document,e=window;setTimeout(function(){var b=d.createElement("script");b.src=a.url||"https://az416426.vo.msecnd.net/scripts/a/ai.0.js",d.getElementsByTagName("script")[0].parentNode.appendChild(b)});try{c.cookie=d.cookie}catch(a){}c.queue=[];for(var f=["Event","Exception","Metric","PageView","Trace","Dependency"];f.length;)b("track"+f.pop());if(b("setAuthenticatedUserContext"),b("clearAuthenticatedUserContext"),b("startTrackEvent"),b("stopTrackEvent"),b("startTrackPage"),b("stopTrackPage"),b("flush"),!a.disableExceptionTracking){f="onerror",b("_"+f);var g=e[f];e[f]=function(a,b,d,e,h){var i=g&&g(a,b,d,e,h);return!0!==i&&c["_"+f](a,b,d,e,h),i}}return c
  }({
      instrumentationKey:"<your instrumentation key>"
  });

window.appInsights=appInsights,appInsights.queue&&0===appInsights.queue.length&&appInsights.trackPageView();
</script>
```

További információt a [nyílt forráskódú JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)-ra vonatkozó GitHub-adattárban talál.

## <a name="video"></a>Videó

* Külső részletes videó [konfigurálása az Application Insights .NET-alkalmazás Előzmények](https://www.youtube.com/watch?v=blnGAVgMAfA).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Amikor végzett tesztelése, törölheti az erőforráscsoportot, és az összes kapcsolódó erőforrás. Ezért kövesse az alábbi lépéseket.
1. Az Azure Portal bal oldali menüjében kattintson az **Erőforráscsoportok** lehetőségre, majd kattintson a **myResourceGroup** elemre.
2. Az erőforráscsoport oldalán kattintson a **Törlés** elemre, írja be a **myResourceGroup** szöveget a szövegmezőbe, majd kattintson a **Törlés** gombra.

## <a name="next-steps"></a>További lépések
Ez a rövid útmutatóban engedélyezte az alkalmazás figyelése az Azure Application Insights.  Folytassa a további oktatóanyagokkal, amelyekből megtudhatja, hogyan használhatja a statisztika monitorozására és az alkalmazáshibák észlelésére is.

> [!div class="nextstepaction"]
> [Azure Application Insights-oktatóanyagok](tutorial-runtime-exceptions.md)
