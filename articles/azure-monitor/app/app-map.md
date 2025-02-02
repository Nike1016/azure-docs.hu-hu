---
title: Alkalmazás-hozzárendelés az Azure Application Insightsban | Microsoft Docs
description: Összetett alkalmazás-topológiák figyelése az alkalmazás-hozzárendeléssel
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 3bf37fe9-70d7-4229-98d6-4f624d256c36
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/15/2019
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: 73cf6fd1c20f2e4208d1f7c28a756f28a2fad839
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2019
ms.locfileid: "68302576"
---
# <a name="application-map-triage-distributed-applications"></a>Alkalmazás-hozzárendelés: Elosztott alkalmazások osztályozása

Az alkalmazás-hozzárendeléssel a teljesítmény szűk keresztmetszetek vagy meghibásodási pontok fordulnak elő az elosztott alkalmazás összes összetevőjénél. A Térkép minden csomópontja egy alkalmazás-összetevőt vagy annak függőségeit jelöli. és az állapot KPI és a riasztások állapota. A bármely összetevőtől kezdve a részletesebb diagnosztika, például a Application Insights események elemre kattintva végezhető el. Ha az alkalmazás az Azure-szolgáltatásokat használja, akkor az Azure Diagnostics szolgáltatásra is kattinthat, például SQL Database Advisor javaslatokat.

## <a name="what-is-a-component"></a>Mi az összetevő?

Az összetevők egymástól függetlenül telepíthetők a Distributed/-Services-alkalmazás részeként. A fejlesztőknek és az operatív csapatoknak kód szintű láthatósággal vagy az alkalmazás-összetevők által generált telemetria való hozzáféréssel kell rendelkezniük. 

* Az összetevők különböznek a "megfigyelt" külső függőségek, például az sqltól, a EventHub stb., amelyek nem férhetnek hozzá a csapatához vagy szervezetéhez (kód vagy telemetria).
* Az összetevők tetszőleges számú kiszolgáló/szerepkör/tároló példányon futnak.
* Az összetevők különálló Application Insights rendszerállapot-kulcsok (még akkor is, ha az előfizetések eltérőek) vagy a különböző szerepkörök, amelyek egyetlen Application Insights kialakítási kulcsnak vannak kialakítva. Az előnézet-leképezési élmény az összetevőket mutatja, függetlenül attól, hogy milyen módon vannak beállítva.

## <a name="composite-application-map"></a>Összetett alkalmazás-hozzárendelés

Az alkalmazás teljes topológiája a kapcsolódó alkalmazás-összetevők több szintjén is megtekinthető. Az összetevők lehetnek különbözőek Application Insights erőforrásai, vagy egy adott erőforrás különböző szerepkörei. Az alkalmazás-hozzárendelés a kiszolgálók között a telepített Application Insights SDK-val végrehajtott HTTP-függőségi hívásokkal megkeresi az összetevőket. 

Ez a élmény az összetevők fokozatos felderítésével kezdődik. Amikor először tölti be az alkalmazás-hozzárendelést, a rendszer elindítja a lekérdezések egy halmazát, hogy felderítse az ehhez az összetevőhöz kapcsolódó összetevőket. A bal felső sarokban található gomb a felderített alkalmazásban lévő összetevők számával fog frissülni. 

Ha a "Térkép-összetevők frissítése" gombra kattint, a Térkép frissül az addig felderített összes összetevővel. Az alkalmazás összetettségétől függően ez egy percet is igénybe vehet.

Ha az összes összetevő szerepkör egyetlen Application Insights erőforráson belül van, akkor ez a felderítési lépés nem szükséges. Az ilyen alkalmazások kezdeti terhelése az összes összetevőjét tartalmazni fogja.

![Alkalmazás-Térkép képernyőképe](media/app-map/app-map-001.png)

Ennek a tapasztalatnak az egyik fő célja, hogy képes legyen a több száz összetevővel rendelkező összetett topológiák megjelenítésére.

Kattintson bármelyik összetevőre a kapcsolódó információk megjelenítéséhez, és lépjen az adott összetevő teljesítményére és meghibásodására vonatkozó osztályozási felületre.

![Flyout](media/app-map/application-map-002.png)

### <a name="investigate-failures"></a>Hibák vizsgálata

Válassza a **hibák vizsgálata** lehetőséget a hibák ablaktábla elindításához.

![Képernyőkép a hibák kivizsgálásáról gomb](media/app-map/investigate-failures.png)

![Képernyőfelvétel a hibákról](media/app-map/failures.png)

### <a name="investigate-performance"></a>Teljesítmény vizsgálata

A teljesítménnyel kapcsolatos problémák elhárításához válassza a **teljesítmény vizsgálata**lehetőséget.

![A teljesítmény vizsgálata gomb képernyőképe](media/app-map/investigate-performance.png)

![A teljesítmény élményét bemutató képernyőkép](media/app-map/performance.png)

### <a name="go-to-details"></a>Ugrás a részletekre

Válassza az **Ugrás** a részletekhez lehetőséget a végpontok közötti tranzakciós élmény megismeréséhez, amely a hívási verem szintjéhez nyújt nézeteket.

![Képernyőkép a részletekről gomb](media/app-map/go-to-details.png)

![Képernyőkép a végpontok közötti tranzakció részleteiről](media/app-map/end-to-end-transaction.png)

### <a name="view-in-analytics"></a>Megtekintés az elemzésekben

Az alkalmazások adatai lekérdezéséhez és kivizsgálásához kattintson **a megtekintés az elemzésekben**lehetőségre.

![Képernyőkép a nézetről az Analytics gombon](media/app-map/view-in-analytics.png)

![Képernyőkép az elemzési élményről](media/app-map/analytics.png)

### <a name="alerts"></a>Riasztások

Az aktív riasztások és a riasztások indítását kiváltó alapul szolgáló szabályok megtekintéséhez válassza a **riasztások**lehetőséget.

![A riasztások gomb képernyőképe](media/app-map/alerts.png)

![Képernyőkép az elemzési élményről](media/app-map/alerts-view.png)

## <a name="set-cloud-role-name"></a>Felhőbeli szerepkör nevének megadása

Az alkalmazás-hozzárendelés a **Felhőbeli szerepkör neve** tulajdonságot használja a térképen található összetevők azonosítására. A Application Insights SDK automatikusan hozzáadja a Felhőbeli szerepkör neve tulajdonságot az összetevők által kibocsátott telemetria. Az SDK például hozzáadja a webhely nevét vagy a szolgáltatási szerepkör nevét a Felhőbeli szerepkör neve tulajdonsághoz. Vannak azonban olyan esetek, amikor érdemes lehet felülbírálni az alapértelmezett értéket. A felhő szerepkör nevének felülbírálásához és az alkalmazás-hozzárendelésben megjelenített elemek módosításához:

### <a name="netnet-core"></a>.NET/.NET Core

**Az alábbi módon írhat egyéni TelemetryInitializer.**

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace CustomInitializer.Telemetry
{
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
            {
                //set custom role name here
                telemetry.Context.Cloud.RoleName = "Custom RoleName";
                telemetry.Context.Cloud.RoleInstance = "Custom RoleInstance";
            }
        }
    }
}
```

**ASP.NET alkalmazások: Inicializálás betöltése az aktív TelemetryConfiguration**

A ApplicationInsights. config fájlban:

```xml
    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="CustomInitializer.Telemetry.MyTelemetryInitializer, CustomInitializer"/>
        ...
      </TelemetryInitializers>
    </ApplicationInsights>
```

A webalkalmazások ASP.NET alternatív módszere az inicializáló létrehozása kódban, például a Global.aspx.cs-ben:

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
    }
```

> [!NOTE]
> Az inicializálás `ApplicationInsights.config` a vagy a használatával `TelemetryConfiguration.Active` való hozzáadása ASP.net Core alkalmazások esetében nem érvényes. 

**ASP.NET Core alkalmazások: Inicializáló betöltése a TelemetryConfiguration**

[ASP.net Core](asp-net-core.md#adding-telemetryinitializers) alkalmazások esetében az új `TelemetryInitializer` hozzáadását a függőségi injektálási tárolóba való hozzáadásával végezheti el, az alább látható módon. Ez az `ConfigureServices` `Startup.cs` osztály metódusában történik.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

### <a name="nodejs"></a>Node.js

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY').start();
appInsights.defaultClient.context.tags["ai.cloud.role"] = "your role name";
appInsights.defaultClient.context.tags["ai.cloud.roleInstance"] = "your role instance";
```

### <a name="alternate-method-for-nodejs"></a>Alternatív módszer a Node. js-hez

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY').start();

appInsights.defaultClient.addTelemetryProcessor(envelope => {
    envelope.tags["ai.cloud.role"] = "your role name";
    envelope.tags["ai.cloud.roleInstance"] = "your role instance"
});
```

### <a name="java"></a>Java

Ha a Spring Boott a Application Insights Spring boot Starter használatával használja, az egyetlen szükséges módosítás az alkalmazás egyéni nevének beállítása az Application. properties fájlban.

`spring.application.name=<name-of-app>`

A Spring boot Starter automatikusan hozzárendeli a Felhőbeli szerepkör nevét a spring.application.name tulajdonsághoz megadott értékhez.

A Java-korrelációval kapcsolatos további információkért és a felhőalapú szerepkör nevének a nem SpringBoot alkalmazásokhoz való konfigurálásáról a korrelációs [szakaszban](https://docs.microsoft.com/azure/application-insights/application-insights-correlation#role-name) olvashat.

### <a name="clientbrowser-side-javascript"></a>Ügyfél/böngésző oldali JavaScript

```javascript
appInsights.queue.push(() => {
appInsights.addTelemetryInitializer((envelope) => {
  envelope.tags["ai.cloud.role"] = "your role name";
  envelope.tags["ai.cloud.roleInstance"] = "your role instance";
});
});
```

### <a name="understanding-cloud-role-name-within-the-context-of-the-application-map"></a>A Felhőbeli szerepkör nevének megértése az alkalmazás-hozzárendelés kontextusában

A **Felhőbeli szerepkör nevének**megismerése érdekében hasznos lehet megtekinteni egy olyan alkalmazás-hozzárendelést, amely több felhőalapú szerepkör-névvel rendelkezik:

![Alkalmazás-Térkép képernyőképe](media/app-map/cloud-rolename.png)

Az alkalmazás-hozzárendelés a zöld mezőkben található nevek felett a Felhőbeli szerepkörök neve értékek az adott elosztott alkalmazás különböző szempontjaihoz. Így ehhez az alkalmazáshoz a szerepkörei a következők `Authentication`: `acmefrontend`, `Inventory Management`,, `Payment Processing Worker Role`a. 

Ebben az alkalmazásban a Felhőbeli szerepkörök nevei egy másik egyedi Application Insights erőforrást is jelentenek a saját kialakítási kulcsaik használatával. Mivel ennek az alkalmazásnak a tulajdonosa a négy különböző Application Insights erőforráshoz fér hozzá, az Application Map képes összekeverni az alapul szolgáló kapcsolatok térképét.

A [hivatalos definíciók](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/39a5ef23d834777eefdd72149de705a016eb06b0/Schema/PublicSchema/ContextTagKeys.bond#L93)esetében:

```
   [Description("Name of the role the application is a part of. Maps directly to the role name in azure.")]
    [MaxStringLength("256")]
    705: string      CloudRole = "ai.cloud.role";
    
    [Description("Name of the instance where the application is running. Computer name for on-premises, instance name for Azure.")]
    [MaxStringLength("256")]
    715: string      CloudRoleInstance = "ai.cloud.roleInstance";
```

A **Felhőbeli szerepkör-példány** hasznos lehet olyan helyzetekben, ahol a Felhőbeli **szerepkör neve** azt jelzi, hogy a probléma valahol a webes kezelőfelületen fut, de előfordulhat, hogy a webes kezelőfelület több elosztott terhelésű kiszolgálón fut, így Kusto-lekérdezések révén mélyebben végezheti el a részletezést, és megtudhatja, hogy a probléma hatással van-e az összes webes előtér-kiszolgálóra/példányra, vagy csak egy rendkívül fontos.

Egy olyan forgatókönyv, amelyben érdemes lehet felülbírálni a Felhőbeli szerepkör példányának értékét, ha az alkalmazás egy olyan tárolóban fut, ahol az egyes kiszolgálók nem feltétlenül elegendő információval szolgálnak az adott probléma megtalálásához.

További információ arról, [hogyan bírálható felül a Felhőbeli szerepkör Name tulajdonsága a telemetria inicializálók használatával: tulajdonságok hozzáadása: ITelemetryInitializer](api-filtering-sampling.md#add-properties-itelemetryinitializer).

## <a name="troubleshooting"></a>Hibaelhárítás

Ha nem tudja, hogy az alkalmazás-hozzárendelés a várt módon működjön, próbálja meg a következő lépéseket:

### <a name="general"></a>Általános

1. Győződjön meg róla, hogy hivatalosan támogatott SDK-t használ. Előfordulhat, hogy a nem támogatott/közösségi SDK-k nem támogatják a korrelációt.

    A támogatott SDK-k listáját ebben a [cikkben](https://docs.microsoft.com/azure/application-insights/app-insights-platforms) találja.

2. Frissítse az összes összetevőt a legújabb SDK-verzióra.

3. Ha Azure Functionst használ C#, frissítsen a [functions v2](https://docs.microsoft.com/azure/azure-functions/functions-versions)-re.

4. Ellenőrizze, hogy a [Felhőbeli szerepkör neve](#set-cloud-role-name) helyesen van-e konfigurálva.

5. Ha valamelyik függőség hiányzik, ellenőrizze, hogy az [automatikusan gyűjtött függőségek](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies) listájában szerepel-e. Ha nem, manuálisan úgy is nyomon követheti egy [függőségek nyomon követése hívással](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency).

### <a name="too-many-nodes-on-the-map"></a>Túl sok csomópont van a térképen

Az alkalmazás-hozzárendelés egy alkalmazás-csomópontot hoz létre a kérelem telemetria található minden egyes egyedi felhőalapú szerepkör nevéhez, valamint egy függőségi csomópontot a típus, a cél és a Felhőbeli szerepkör minden egyedi kombinációja számára a függőségi telemetria. Ha a telemetria több mint 10 000 csomópont található, az Application Map nem fogja tudni beolvasni az összes csomópontot és hivatkozást, így a Térkép hiányos lesz. Ha ez történik, egy figyelmeztető üzenet jelenik meg a Térkép megtekintésekor.

Emellett az alkalmazás-hozzárendelés legfeljebb 1000 külön nem csoportosított csomópontot támogat. Az alkalmazás-hozzárendelés csökkenti a vizualizációk összetettségét olyan függőségek csoportosításával, amelyek ugyanolyan típusú és hívókkal rendelkeznek, de ha a telemetria túl sok egyedi Felhőbeli szerepkör-névvel vagy túl sok függőségi típussal rendelkezik, a csoportosítás nem lesz elegendő, és a Térkép nem fog működni. renderelési.

A probléma megoldásához módosítania kell a kialakítást, hogy megfelelően állítsa be a Felhőbeli szerepkör nevét, a függőség típusát és a függőségi cél mezőket.

* A függőségi célnak a függőség logikai nevét kell képviselnie. Sok esetben ez egyenértékű a függőség kiszolgálójának vagy erőforrásának nevével. A HTTP-függőségek esetében például az állomásnévre van beállítva. Nem tartalmazhat olyan egyedi azonosítókat vagy paramétereket, amelyek az egyik kérelemből a másikba változnak.

* A függőségi típusnak a függőség logikai típusát kell képviselnie. Például a HTTP, az SQL vagy az Azure Blob jellemző függőségi típusok. Nem tartalmazhat egyedi azonosítókat.

* A Felhőbeli szerepkör nevét a [fenti szakasz](https://docs.microsoft.com/azure/azure-monitor/app/app-map#set-cloud-role-name)ismerteti.

## <a name="portal-feedback"></a>Portál visszajelzése

A visszajelzések megadásához használja a visszajelzés lehetőséget.

![MapLink – 1 rendszerkép](./media/app-map/14-updated.png)

## <a name="next-steps"></a>További lépések

* [A korreláció ismertetése](https://docs.microsoft.com/azure/application-insights/application-insights-correlation)
