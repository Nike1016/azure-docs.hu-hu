---
title: Application Insights API egyéni eseményekhez és mérőszámokhoz | Microsoft Docs
description: A használat és a problémák diagnosztizálásához helyezzen be néhány sornyi kódot az eszközön, az asztali alkalmazásban, a weblapon vagy a szolgáltatásban.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 80400495-c67b-4468-a92e-abf49793a54d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: mbullwin
ms.openlocfilehash: 9bedb74f4e882ac6e4206ee7fef676c94dc2422d
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68717449"
---
# <a name="application-insights-api-for-custom-events-and-metrics"></a>Application Insights API egyéni eseményekhez és mérőszámokhoz

Szúrjon be néhány sornyi kódot az alkalmazásban, hogy megtudja, mit csinálnak a felhasználók, vagy segíthetnek a problémák diagnosztizálásában. Telemetria az eszköz-és asztali alkalmazásokból, a webes ügyfelekből és a webkiszolgálókról is küldhet. Az [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) Core telemetria API használatával egyéni eseményeket és mérőszámokat küldhet, valamint a szabványos telemetria saját verzióit. Ez az API ugyanazt az API-t használja, mint a standard Application Insights adatgyűjtők.

> [!NOTE]
> `TrackMetric()`már nem az előnyben részesített módszer az egyéni metrikák küldésére a .NET-alapú alkalmazásokhoz. A [2,60-](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/CHANGELOG.md#version-260-beta3) es verzióban a .net SDK Application Insights Beta 3. új metódus [`TelemetryClient.GetMetric()`](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.getmetric?view=azure-dotnet) lett bevezetve. A Application Insights .NET SDK [2,72](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.getmetric?view=azure-dotnet) -es verziójától kezdve ez a funkció már a stabil kiadás részét képezi.

## <a name="api-summary"></a>API összefoglaló

Az alapszintű API minden platformon egységes, többek között a `GetMetric`(csak .net-es) változatokon kívül.

| Módszer | Alkalmazási cél |
| --- | --- |
| [`TrackPageView`](#page-views) |Lapok, képernyők, pengék vagy űrlapok. |
| [`TrackEvent`](#trackevent) |Felhasználói műveletek és egyéb események. A felhasználói viselkedés nyomon követésére vagy a teljesítmény figyelésére szolgál. |
| [`GetMetric`](#getmetric) |Nulla és többdimenziós metrikák, központilag konfigurált összesítés, C# csak. |
| [`TrackMetric`](#trackmetric) |A teljesítmény mérése, például a várólista hossza nem adott eseményekhez kapcsolódik. |
| [`TrackException`](#trackexception) |Diagnosztikai kivételek naplózása. Nyomon követheti a más eseményekhez kapcsolódó eseményeket, és megvizsgálhatja a verem nyomkövetéseit. |
| [`TrackRequest`](#trackrequest) |A kiszolgálói kérelmek gyakoriságának és időtartamának naplózása a teljesítmény elemzéséhez. |
| [`TrackTrace`](#tracktrace) |Diagnosztikai naplók üzenetei. A harmadik féltől származó naplókat is rögzítheti. |
| [`TrackDependency`](#trackdependency) |A külső összetevőkre irányuló hívások időtartamának és gyakoriságának naplózása, amelytől az alkalmazás függ. |

A telemetria-hívások többségéhez [tulajdonságokat és mérőszámokat is csatolhat](#properties) .

## <a name="prep"></a>Kezdés előtt

Ha még nincs hivatkozása Application Insights SDK-ra:

* Adja hozzá a Application Insights SDK-t a projekthez:

  * [ASP.NET project](../../azure-monitor/app/asp-net.md)
  * [ASP.NET Core projekt](../../azure-monitor/app/asp-net-core.md)
  * [Java project](../../azure-monitor/app/java-get-started.md)
  * [Node. js-projekt](../../azure-monitor/app/nodejs.md)
  * [JavaScript az egyes weblapokon](../../azure-monitor/app/javascript.md) 
* Az eszköz vagy a webkiszolgáló kódjába illessze be a következőt:

    *C#:* `using Microsoft.ApplicationInsights;`

    *Visual Basic:* `Imports Microsoft.ApplicationInsights`

    *Java:* `import com.microsoft.applicationinsights.TelemetryClient;`

    *Node. js:* `var applicationInsights = require("applicationinsights");`

## <a name="get-a-telemetryclient-instance"></a>TelemetryClient-példány beolvasása

Példány beszerzése `TelemetryClient` (kivéve a weblapokon található JavaScriptben):

*C#*

```csharp
private TelemetryClient telemetry = new TelemetryClient();
```

*Visual Basic*

```vb
Private Dim telemetry As New TelemetryClient
```

*Java*

```java
private TelemetryClient telemetry = new TelemetryClient();
``` 

*Node.js*

```javascript
var telemetry = applicationInsights.defaultClient;
```

A TelemetryClient szál-biztonságos.

A ASP.NET és a Java-projektek esetében a bejövő HTTP-kérelmeket a rendszer automatikusan rögzíti. Előfordulhat, hogy a TelemetryClient további példányait is létre szeretné hozni az alkalmazás más moduljában. Előfordulhat például, hogy egy TelemetryClient-példánya van a middleware-osztályban az üzleti logikával kapcsolatos események jelentéséhez. A gép azonosításához olyan tulajdonságokat is beállíthat, mint például a UserId és a DeviceId. Ez az információ a példány által küldött összes eseményhez csatolva van. 

*C#*

```csharp
TelemetryClient.Context.User.Id = "...";
TelemetryClient.Context.Device.Id = "...";
```

*Java*

```java
telemetry.getContext().getUser().setId("...");
telemetry.getContext().getDevice().setId("...");
```

A Node. js-projektekben `new applicationInsights.TelemetryClient(instrumentationKey?)` létrehozhat egy új példányt, de ez csak olyan forgatókönyvek esetén javasolt, amelyek különálló konfigurációt `defaultClient`igényelnek.

## <a name="trackevent"></a>TrackEvent

Application Insights egy *egyéni esemény* egy olyan adatpont, amelyet [Metrikaböngésző](../../azure-monitor/app/metrics-explorer.md) megadhat összesített számként, valamint a [diagnosztikai keresésben](../../azure-monitor/app/diagnostic-search.md) egyéni előfordulásként. (Nem kapcsolódik az MVC-hoz vagy más keretrendszer "eseményeihez".)

A `TrackEvent` különböző események számlálásához helyezzen be hívásokat a kódban. A felhasználók milyen gyakran választanak ki egy adott funkciót, milyen gyakran érnek el konkrét célokat, vagy hogy milyen gyakran használják bizonyos típusú hibákat.

Például egy játék alkalmazásban küldjön egy eseményt, amikor egy felhasználó megnyeri a játékot:

*JavaScript*

```javascript
appInsights.trackEvent("WinGame");
```

*C#*

```csharp
telemetry.TrackEvent("WinGame");
```

*Visual Basic*

```vb
telemetry.TrackEvent("WinGame")
```

*Java*

```java
telemetry.trackEvent("WinGame");
```

*Node.js*

```javascript
telemetry.trackEvent({name: "WinGame"});
```

### <a name="custom-events-in-analytics"></a>Egyéni események az Analyticsben

A telemetria `customEvents` [Application Insights Analytics](analytics.md)táblázatában érhető el. Az egyes sorok az alkalmazásban `trackEvent(..)` meghívást jelentenek.

Ha a [mintavételezés](../../azure-monitor/app/sampling.md) folyamatban van, a ItemCount tulajdonság 1-nél nagyobb értéket jelenít meg. Például a itemCount = = 10 érték azt jelenti, hogy a trackEvent () 10 hívása a mintavételezési folyamat csak az egyiket továbbítja. Az egyéni események helyes számának megszerzéséhez ezért kódokat kell használnia, például `customEvents | summarize sum(itemCount)`:.

## <a name="getmetric"></a>GetMetric

### <a name="examples"></a>Példák

*C#*

```csharp
namespace User.Namespace.Example01
{
    using System;
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;

    /// <summary>
    /// Most simple cases are one-liners.
    /// This is all possible without even importing an additional namespace.
    /// </summary>

    public class Sample01
    {
        /// <summary />
        public static void Exec()
        {
            // *** SENDING METRICS ***

            // Recall how you send custom telemetry with Application Insights in other cases, e.g. Events.
            // The following will result in an EventTelemetry object to be sent to the cloud right away.
            TelemetryClient client = new TelemetryClient();
            client.TrackEvent("SomethingInterestingHappened");

            // Metrics work very similar. However, the value is not sent right away.
            // It is aggregated with other values for the same metric, and the resulting summary (aka "aggregate" is sent automatically every minute.
            // To mark this difference, we use a pattern that is similar, but different from the established TrackXxx(..) pattern that sends telemetry right away:

            client.GetMetric("CowsSold").TrackValue(42);

            // *** MULTI-DIMENSIONAL METRICS ***

            // The above example shows a zero-dimensional metric.
            // Metrics can also be multi-dimensional.
            // In the initial version we are supporting up to 2 dimensions, and we will add support for more in the future as needed.
            // Here is an example for a one-dimensional metric:

            Metric animalsSold = client.GetMetric("AnimalsSold", "Species");

            animalsSold.TrackValue(42, "Pigs");
            animalsSold.TrackValue(24, "Horses");

            // The values for Pigs and Horses will be aggregated separately from each other and will result in two distinct aggregates.
            // You can control the maximum number of number data series per metric (and thus your resource usage and cost).
            // The default limits are no more than 1000 total data series per metric, and no more than 100 different values per dimension.
            // We discuss elsewhere how to change them.
            // We use a common .NET pattern: TryXxx(..) to make sure that the limits are observed.
            // If the limits are already reached, Metric.TrackValue(..) will return False and the value will not be tracked. Otherwise it will return True.
            // This is particularly useful if the data for a metric originates from user input, e.g. a file:

            Tuple<int, string> countAndSpecies = ReadSpeciesFromUserInput();
            int count = countAndSpecies.Item1;
            string species = countAndSpecies.Item2;

            if (!animalsSold.TrackValue(count, species))

            {
                client.TrackTrace($"Data series or dimension cap was reached for metric {animalsSold.Identifier.MetricId}.", SeverityLevel.Error);
            }

            // You can inspect a metric object to reason about its current state. For example:
            int currentNumberOfSpecies = animalsSold.GetDimensionValues(1).Count;
        }

        private static void ResetDataStructure()
        {
            // Do stuff
        }

        private static Tuple<int, string> ReadSpeciesFromUserInput()
        {
            return Tuple.Create(18, "Cows");
        }

        private static int AddItemsToDataStructure()
        {
            // Do stuff
            return 5;
        }
    }
}
```

## <a name="trackmetric"></a>TrackMetric

> [!NOTE]
> A Microsoft. ApplicationInsights. TelemetryClient. TrackMetric nem az előnyben részesített módszer a metrikák küldéséhez. A metrikákat mindig előre összesíteni kell a küldés előtt. Az egyik GetMetric (..) túlterheli az SDK-ra vonatkozó előzetes összesítési képességek eléréséhez szükséges metrikai objektumot. Ha saját előzetes összesítési logikát alkalmaz, akkor a TrackMetric () metódussal küldheti el az eredményül kapott összesítéseket. Ha az alkalmazás egy külön telemetria-elem küldését igényli minden alkalommal az idő összesítése nélkül, akkor valószínű, hogy az Event telemetria használati esete van; Lásd: TelemetryClient. TrackEvent (Microsoft. ApplicationInsights. DataContracts. EventTelemetry).

A Application Insights olyan metrikákat diagramokat is használhat, amelyek nincsenek az adott eseményekhez csatolva. Előfordulhat például, hogy rendszeres időközönként figyelheti a várólista hosszát. A metrikák esetében az egyes mérések kevésbé érdeklik, mint a változatok és a trendek, így a statisztikai diagramok hasznosak.

A metrikák Application Insightsre való küldéséhez használhatja az `TrackMetric(..)` API-t. Kétféle módon küldhet mérőszámot:

* Egyetlen érték. Minden alkalommal, amikor mérést végez az alkalmazásban, a megfelelő értéket kell elküldenie Application Insights. Tegyük fel például, hogy rendelkezik egy mérőszámmal, amely egy tárolóban lévő elemek számát mutatja. Egy adott időszakban először három elemet helyez el a tárolóba, majd két elemet távolít el. Ennek megfelelően kétszer is meghívja `TrackMetric` a következőt: az érték `3` első átadása, majd az érték `-2`. Application Insights mindkét értéket tárolja az Ön nevében.

* Összesítési. A metrikák használatakor az egyes mérések ritkán fordulnak elő. Ehelyett fontos összefoglalni, hogy mi történt egy adott időszakban. Egy ilyen összefoglaló neve _Összesítés_. A fenti példában az adott időszak `1` összesített metrikai összege és a metrika `2`értékeinek száma. Az összesítési módszer használatakor csak egyszer kell meghívnia `TrackMetric` az egyes időszakokat, és el kell küldenie az összesített értékeket. Ez az ajánlott módszer, mivel jelentősen csökkentheti a költségek és a teljesítmény terhelését azáltal, hogy kevesebb adatpontot küld Application Insightsra, miközben továbbra is összegyűjti az összes releváns információt.

### <a name="examples"></a>Példák

#### <a name="single-values"></a>Egyetlen érték

Egyetlen metrikai érték küldése:

*JavaScript*

 ```javascript
appInsights.trackMetric("queueLength", 42.0);
 ```

*C#*

```csharp
var sample = new MetricTelemetry();
sample.Name = "metric name";
sample.Value = 42.3;
telemetryClient.TrackMetric(sample);
```

*Java*

```java
telemetry.trackMetric("queueLength", 42.0);
```

*Node.js*

 ```javascript
telemetry.trackMetric({name: "queueLength", value: 42.0});
 ```

### <a name="custom-metrics-in-analytics"></a>Egyéni metrikák az Analyticsben

A telemetria `customMetrics` [Application Insights Analytics](analytics.md)táblázatában érhető el. Az egyes sorok az alkalmazásban `trackMetric(..)` meghívást jelentenek.

* `valueSum`– Ez a mérések összege. A Mean értékének megszerzéséhez ossza el `valueCount`a következőt:.
* `valueCount`– Az ebben `trackMetric(..)` a hívásban összesített mérések száma.

## <a name="page-views"></a>Lapmegtekintések

Egy eszköz vagy weblap alkalmazásban az oldal nézet telemetria alapértelmezés szerint a rendszer minden egyes képernyő vagy lap betöltésekor elküldi. Ezt azonban módosíthatja, ha további vagy eltérő időpontokban szeretné nyomon követni a lapok nézeteit. Ha például egy alkalmazásban lapokat vagy lapokat jelenít meg, érdemes nyomon követni egy oldalt, amikor a felhasználó új panelt nyit meg.

A felhasználói és munkamenet-adatküldés tulajdonságokként, valamint a lapok nézeteivel együtt történik, így a felhasználói és a munkamenet-diagramok életben lesznek, ha az oldal nézet telemetria.

### <a name="custom-page-views"></a>Egyéni lapok nézetei

*JavaScript*

```javascript
appInsights.trackPageView("tab1");
```

*C#*

```csharp
telemetry.TrackPageView("GameReviewPage");
```

*Visual Basic*

```vb
telemetry.TrackPageView("GameReviewPage")
```

*Java*

```java
telemetry.trackPageView("GameReviewPage");
```

Ha több lap is található a különböző HTML-lapokon, megadhatja az URL-címet is:

```javascript
appInsights.trackPageView("tab1", "http://fabrikam.com/page1.htm");
```

### <a name="timing-page-views"></a>Időzítési lapok nézetei

Alapértelmezés szerint a rendszer az **oldal nézet** betöltési idejét jeleníti meg, amikor a böngésző elküldi a kérést, amíg a böngésző lapja betöltési eseménye meg nem történik.

Ehelyett a következők közül választhat:

* Explicit időtartam beállítása a [trackPageView](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#trackpageview) -hívásban: `appInsights.trackPageView("tab1", null, null, null, durationInMilliseconds);`.
* Használja az oldal nézet időzítési `startTrackPage` hívásait és `stopTrackPage`.

*JavaScript*

```javascript
// To start timing a page:
appInsights.startTrackPage("Page1");

...

// To stop timing and log the page:
appInsights.stopTrackPage("Page1", url, properties, measurements);
```

Az első paraméterként használt név a Start és a stop hívásokat társítja. Alapértelmezés szerint az aktuális oldal nevét adja meg.

Az eredményül kapott oldal betöltési időtartama a Metrikaböngésző a kezdő és a leállítási hívások közötti intervallumból származik. Ez a tényleges időintervallum.

### <a name="page-telemetry-in-analytics"></a>Az Analytics telemetria lapja

Az [Analytics](analytics.md) két táblájában a böngésző műveleteiből származó adatok jelennek meg:

* A `pageViews` tábla az URL-címmel és a lap címével kapcsolatos információkat tartalmaz.
* A `browserTimings` tábla az ügyfél teljesítményével kapcsolatos információkat tartalmaz, például a bejövő adat feldolgozásához szükséges időt.

Annak megállapítása, hogy a böngésző mennyi ideig tart a különböző lapok feldolgozása:

```kusto
browserTimings
| summarize avg(networkDuration), avg(processingDuration), avg(totalDuration) by name
```

A különböző böngészők népszerűek észlelése:

```kusto
pageViews
| summarize count() by client_Browser
```

A lapok nézeteinek AJAX-hívásokhoz való társításához kapcsolódjon függőségekkel:

```kusto
pageViews
| join (dependencies) on operation_Id 
```

## <a name="trackrequest"></a>TrackRequest

A Server SDK a TrackRequest használatával naplózza a HTTP-kérelmeket.

Azt is meghívhatja, ha olyan kontextusban szeretné szimulálni a kérelmeket, amelyben nem fut a webszolgáltatási modul.

Azonban a kérelem küldésének ajánlott módja a telemetria, ahol a kérelem <a href="#operation-context">műveleti környezetként</a>működik.

## <a name="operation-context"></a>Műveleti környezet

A telemetria elemek összekapcsolhatók a műveleti környezettel való társítással. A standard szintű kérelem-követési modul ezt a kivételeket és a HTTP-kérelem feldolgozása közben elküldött egyéb eseményeket is végrehajtja. A [Search](../../azure-monitor/app/diagnostic-search.md) és az [Analytics szolgáltatásban](analytics.md)a művelet azonosítójának használatával könnyedén megtalálhatja a kérelemhez társított eseményeket.

A korrelációval kapcsolatos további részletekért tekintse meg a [telemetria korrelációját Application Insightsban](../../azure-monitor/app/correlation.md) .

A telemetria manuális nyomon követéséhez a legegyszerűbb módszer a telemetria korrelációjának biztosítása a következő minta használatával:

*C#*

```csharp
// Establish an operation context and associated telemetry item:
using (var operation = telemetryClient.StartOperation<RequestTelemetry>("operationName"))
{
    // Telemetry sent in here will use the same operation ID.
    ...
    telemetryClient.TrackTrace(...); // or other Track* calls
    ...

    // Set properties of containing telemetry item--for example:
    operation.Telemetry.ResponseCode = "200";

    // Optional: explicitly send telemetry item:
    telemetryClient.StopOperation(operation);

} // When operation is disposed, telemetry item is sent.
```

A műveleti környezet beállításával együtt egy `StartOperation` telemetria-elem jön létre a megadott típushoz. A művelet eldobásakor a telemetria-tételt küldi el, vagy ha explicit módon meghívja `StopOperation`. Ha telemetria- `RequestTelemetry` típusként használja, annak időtartama az Indítás és a Leállítás közötti időzített időközre van állítva.

A művelet hatókörén belül jelentett telemetria a művelet "Children" részévé válnak. A műveleti környezetek beágyazva lehetnek.

A Search (keresés) területen a **kapcsolódó elemek** listájának létrehozásához a műveleti környezet használható:

![Kapcsolódó elemek](./media/api-custom-events-metrics/21.png)

Az egyéni műveletek nyomon követésével kapcsolatos további információkért lásd: [Egyéni műveletek nyomon követése Application Insights .net SDK-val](../../azure-monitor/app/custom-operations-tracking.md) .

### <a name="requests-in-analytics"></a>Az elemzési kérelmek

[Application Insights elemzésekben](analytics.md)a kérelmek megjelennek a `requests` táblázatban.

Ha a [mintavételezés](../../azure-monitor/app/sampling.md) folyamatban van, a ItemCount tulajdonság 1-nél nagyobb értéket fog megjeleníteni. Például a itemCount = = 10 érték azt jelenti, hogy a trackRequest () 10 hívása a mintavételezési folyamat csak az egyiket továbbítja. A kérelmek helyes számának és az átlagos időtartamnak a kérelmek nevei szerint szegmentált eléréséhez használja a következő kódot:

```kusto
requests
| summarize count = sum(itemCount), avgduration = avg(duration) by name
```

## <a name="trackexception"></a>TrackException

Kivételek küldése Application Insightsre:

* A probléma gyakoriságának megjelölése, hogy megszámolja [őket](../../azure-monitor/app/metrics-explorer.md).
* Az [egyes előfordulások vizsgálatához](../../azure-monitor/app/diagnostic-search.md).

A jelentések tartalmazzák a verem nyomkövetését.

*C#*

```csharp
try
{
    ...
}
catch (Exception ex)
{
    telemetry.TrackException(ex);
}
```

*Java*

```java
try {
    ...
} catch (Exception ex) {
    telemetry.trackException(ex);
}
```

*JavaScript*

```javascript
try
{
    ...
}
catch (ex)
{
    appInsights.trackException(ex);
}
```

*Node.js*

```javascript
try
{
    ...
}
catch (ex)
{
    telemetry.trackException({exception: ex});
}
```

Az SDK-k sok kivételt kapnak automatikusan, így nem mindig explicit módon kell meghívnia a TrackException.

* ASP.NET: [Kód írása a kivételek](../../azure-monitor/app/asp-net-exceptions.md)elvégzéséhez.
* Java EE: [A kivételek automatikusan](../../azure-monitor/app/java-get-started.md#exceptions-and-request-failures)bekerülnek.
* JavaScript A kivételek automatikusan bekerülnek. Ha le szeretné tiltani az automatikus gyűjtést, adjon hozzá egy sort a weblapokon beszúrt kódrészlethez:

```javascript
({
    instrumentationKey: "your key",
    disableExceptionTracking: true
})
```

### <a name="exceptions-in-analytics"></a>Kivételek az Analyticsben

[Application Insights elemzésekben](analytics.md)a kivételek megjelennek a `exceptions` táblázatban.

Ha a [mintavétel](../../azure-monitor/app/sampling.md) folyamatban van, a `itemCount` tulajdonság 1-nél nagyobb értéket jelenít meg. Például a itemCount = = 10 érték azt jelenti, hogy a trackException () 10 hívása a mintavételezési folyamat csak az egyiket továbbítja. A kivételek típusával szegmentált kivételek helyes számának beszerzéséhez használja a következő kódot:

```kusto
exceptions
| summarize sum(itemCount) by type
```

A fontos stack-információk nagy része már ki van választva különálló változóknak, de a `details` szerkezet kihúzásával további információkat érhet el. Mivel ez a struktúra dinamikus, az eredményt a várt típusra kell átadnia. Példa:

```kusto
exceptions
| extend method2 = tostring(details[0].parsedStack[1].method)
```

Ha a kivételeket a kapcsolódó kérésekkel társítja, használjon illesztést:

```kusto
exceptions
| join (requests) on operation_Id
```

## <a name="tracktrace"></a>TrackTrace

A TrackTrace segítségével diagnosztizálhatja a problémákat, ha a "morzsa Trail" üzenetet küld Application Insights. Elküldheti a diagnosztikai adatgyűjtés részleteit, és megvizsgálhatja azokat a [diagnosztikai keresésben](../../azure-monitor/app/diagnostic-search.md).

A .NET [](../../azure-monitor/app/asp-net-trace-logs.md) -naplózási adapterek ezt az API-t használják harmadik féltől származó naplók küldésére a portálon.

A Java [standard szintű naplózók (például a Log4J](../../azure-monitor/app/java-trace-logs.md) ) esetében a Logback Application Insights Log4J vagy Logback-hozzáfűzéset használ, hogy harmadik féltől származó naplókat küldjön a portálra.

*C#*

```csharp
telemetry.TrackTrace(message, SeverityLevel.Warning, properties);
```

*Java*

```java
telemetry.trackTrace(message, SeverityLevel.Warning, properties);
```

*Node.js*

```javascript
telemetry.trackTrace({
    message: message,
    severity: applicationInsights.Contracts.SeverityLevel.Warning,
    properties: properties
});
```

*Ügyfél/böngésző oldali JavaScript*

```javascript
trackTrace(message: string, properties?: {[string]:string}, severityLevel?: AI.SeverityLevel)
```

Naplózhat egy diagnosztikai eseményt, például beírhat vagy elhagyhat egy metódust.

 Paraméter | Leírás
---|---
`message` | Diagnosztikai adatként. Sokkal hosszabb lehet, mint a név.
`properties` | Karakterlánc leképezése sztringre: A portálon a [kivételek szűréséhez](https://azure.microsoft.com/documentation/articles/app-insights-api-custom-events-metrics/#properties) használt további információk. Az alapértelmezett érték üres.
`severityLevel` | Támogatott értékek: [SeverityLevel. TS](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/JavaScript/JavaScriptSDK.Interfaces/Contracts/Generated/SeverityLevel.ts)

Megkeresheti az üzenet tartalmát, de (a tulajdonság értékeitől eltérően) nem szűrheti.

A méretkorlát `message` értéke sokkal nagyobb, mint a tulajdonságok korlátja.
A TrackTrace előnye, hogy viszonylag hosszú adatmennyiséget helyezhet el az üzenetben. Például elvégezheti az adatposták küldését.  

Emellett súlyossági szintet is hozzáadhat az üzenethez. És hasonlóan más telemetria is, hozzáadhat tulajdonságokat is a különböző nyomkövetési csoportok szűréséhez vagy kereséséhez. Példa:

*C#*

```csharp
var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
telemetry.TrackTrace("Slow database response",
                SeverityLevel.Warning,
                new Dictionary<string,string> { {"database", db.ID} });
```

*Java*

```java
Map<String, Integer> properties = new HashMap<>();
properties.put("Database", db.ID);
telemetry.trackTrace("Slow Database response", SeverityLevel.Warning, properties);
```

A [Search](../../azure-monitor/app/diagnostic-search.md)szolgáltatásban egyszerűen kiszűrheti az adott adatbázishoz kapcsolódó adott súlyossági szint összes üzenetét.

### <a name="traces-in-analytics"></a>Nyomkövetés az Analyticsben

[Application Insights elemzésekben](analytics.md)a TrackTrace megjelenő hívások megjelennek a `traces` táblázatban.

Ha a [mintavételezés](../../azure-monitor/app/sampling.md) folyamatban van, a ItemCount tulajdonság 1-nél nagyobb értéket jelenít meg. Például a ItemCount = = 10 azt jelenti, hogy a 10 `trackTrace()`meghívása esetén a mintavételi folyamat csak az egyiket továbbítja. A nyomkövetési hívások helyes számának megszerzéséhez használja a következő kódot `traces | summarize sum(itemCount)`:.

## <a name="trackdependency"></a>TrackDependency

A TrackDependency hívásával követheti a külső kódrészletre irányuló hívások válaszideje és sikerességi arányát. Az eredmények a portál függőségi diagramjaiban jelennek meg.

*C#*

```csharp
var success = false;
var startTime = DateTime.UtcNow;
var timer = System.Diagnostics.Stopwatch.StartNew();
try
{
    success = dependency.Call();
}
catch(Exception ex) 
{
    success = false;
    telemetry.TrackException(ex);
    throw new Exception("Operation went wrong", ex);
}
finally
{
    timer.Stop();
    telemetry.TrackDependency("DependencyType", "myDependency", "myCall", startTime, timer.Elapsed, success);
}
```

*Java*

```java
boolean success = false;
long startTime = System.currentTimeMillis();
try {
    success = dependency.call();
}
finally {
    long endTime = System.currentTimeMillis();
    long delta = endTime - startTime;
    RemoteDependencyTelemetry dependencyTelemetry = new RemoteDependencyTelemetry("My Dependency", "myCall", delta, success);
    telemetry.setTimeStamp(startTime);
    telemetry.trackDependency(dependencyTelemetry);
}
```

*JavaScript*

```javascript
var success = false;
var startTime = new Date().getTime();
try
{
    success = dependency.Call();
}
finally
{
    var elapsed = new Date() - startTime;
    telemetry.trackDependency({
        dependencyTypeName: "myDependency",
        name: "myCall",
        duration: elapsed,
        success: success
    });
}
```

Ne feledje, hogy a kiszolgálói SDK [](../../azure-monitor/app/asp-net-dependencies.md) -k olyan függőségi modult tartalmaznak, amely felkeresi és nyomon követi bizonyos függőségi hívásokat automatikusan – például az adatbázisok és a REST API-k számára. Telepítenie kell egy ügynököt a kiszolgálóra a modul működésének érdekében. 

A javában bizonyos függőségi hívásokat automatikusan nyomon követhetik a [Java Agent](../../azure-monitor/app/java-agent.md)használatával.

Ezt a hívást akkor használja, ha nyomon szeretné követni, hogy az automatikus követés ne kapjon hívásokat, vagy ha nem szeretné telepíteni az ügynököt.

A standard függőség-követési modul kikapcsolásához szerkessze a [ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) fájlt, és törölje `DependencyCollector.DependencyTrackingTelemetryModule`a hivatkozást a következőre:. C# A Java-ban ne telepítse a Java-ügynököt, ha nem szeretné automatikusan gyűjteni a standard függőségeket.

### <a name="dependencies-in-analytics"></a>Az elemzés függőségei

[Application Insights elemzésekben](analytics.md)a trackDependency-hívások megjelennek a `dependencies` táblázatban.

Ha a [mintavételezés](../../azure-monitor/app/sampling.md) folyamatban van, a ItemCount tulajdonság 1-nél nagyobb értéket jelenít meg. Például a itemCount = = 10 érték azt jelenti, hogy a trackDependency () 10 hívása a mintavételezési folyamat csak az egyiket továbbítja. A cél összetevő által szegmentált függőségek helyes számának beszerzéséhez használja a következő kódot:

```kusto
dependencies
| summarize sum(itemCount) by target
```

A függőségeknek a kapcsolódó kérésekkel való hozzárendeléséhez használjon illesztést:

```kusto
dependencies
| join (requests) on operation_Id
```

## <a name="flushing-data"></a>Az adatkiürítés

Az SDK általában rögzített időközönként küld adatokat (általában 30 mp), vagy ha a puffer megtelt (általában 500 elem). Bizonyos esetekben azonban érdemes lehet kiüríteni a puffert, például ha az SDK-t olyan alkalmazásban használja, amely le van állítva.

*C#*

 ```csharp
telemetry.Flush();
// Allow some time for flushing before shutdown.
System.Threading.Thread.Sleep(5000);
```

*Java*

```java
telemetry.flush();
//Allow some time for flushing before shutting down
Thread.sleep(5000);
```

*Node.js*

```javascript
telemetry.flush();
```

A [kiszolgáló telemetria](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/)-csatornája aszinkron módon működik.

Ideális esetben a flush () metódust kell használni az alkalmazás leállítási tevékenységében.

## <a name="authenticated-users"></a>Hitelesített felhasználók

Egy webalkalmazásban a felhasználók a cookie-k által azonosított (alapértelmezés szerint). Előfordulhat, hogy egy felhasználó többször is megszámolható, ha egy másik gépről vagy böngészőből fér hozzá az alkalmazáshoz, vagy ha cookie-kat töröl.

Ha a felhasználók bejelentkeznek az alkalmazásba, pontosabban megadhatja a hitelesített felhasználói azonosító beállítását a böngésző kódjában:

*JavaScript*

```javascript
// Called when my app has identified the user.
function Authenticated(signInId) {
    var validatedId = signInId.replace(/[,;=| ]+/g, "_");
    appInsights.setAuthenticatedUserContext(validatedId);
    ...
}
```

Egy ASP.NET web MVC-alkalmazásban, például:

*Razor*

```cshtml
@if (Request.IsAuthenticated)
{
    <script>
        appInsights.setAuthenticatedUserContext("@User.Identity.Name
            .Replace("\\", "\\\\")"
            .replace(/[,;=| ]+/g, "_"));
    </script>
}
```

Nem szükséges a felhasználó tényleges bejelentkezési nevének használata. Csak olyan AZONOSÍTÓnak kell lennie, amely egyedi az adott felhasználó számára. Nem tartalmazhat szóközt vagy a karaktereket `,;=|`.

A felhasználói azonosító a munkamenet-cookie-ban is be van állítva, és a kiszolgálónak is el lesz küldve. Ha a Server SDK telepítve van, a rendszer az ügyfél és a kiszolgáló telemetria környezeti tulajdonságainak részeként elküldi a hitelesített felhasználói azonosítót. Ezután szűrheti és keresheti meg a keresést.

Ha az alkalmazás a felhasználókat fiókokba csoportosítja, akkor a fiók azonosítóját is átadhatja (ugyanazzal a karakteres korlátozásokkal).

```javascript
appInsights.setAuthenticatedUserContext(validatedId, accountId);
```

[Metrikaböngésző](../../azure-monitor/app/metrics-explorer.md)létrehozhat egy olyan diagramot, amely **felhasználói, hitelesített**és **felhasználói fiókokat**számol fel.

A megadott felhasználóneveket és fiókokat tartalmazó ügyfél-adatpontokat is megkeresheti. [](../../azure-monitor/app/diagnostic-search.md)

## <a name="properties"></a>Az adatai szűrése, keresése és szegmentálása tulajdonságok használatával

Az eseményekhez tulajdonságok és mérések is csatolhatók (valamint mérőszámok, oldalletöltések, kivételek és egyéb telemetria adatok).

A *Tulajdonságok* olyan karakterlánc-értékek, amelyek segítségével szűrheti a telemetria a használati jelentésekben. Ha például az alkalmazás több játékot is biztosít, a játék nevét csatolhatja az egyes eseményekhez, így láthatja, hogy mely játékok népszerűbbek.

A karakterlánc hossza legfeljebb 8192 lehet. (Ha nagy adattömböket szeretne küldeni, használja a TrackTrace üzenet paraméterét.)

A metrikák olyan numerikus értékek, amelyek grafikus formában jeleníthetők meg. Például érdemes megtekinteni, hogy van-e fokozatos növekedés a játékosok által elért pontszámok között. A gráfok az eseménnyel ellátott tulajdonságok szerint szegmentálva lehetnek, így különálló vagy halmozott gráfokat is letölthet a különböző játékok számára.

Ahhoz, hogy a metrika helyesen jelenjen meg, a nullánál nagyobbnak vagy azzal egyenlőnek kell lennie.

Bizonyos korlátozások vonatkoznak a használható [Tulajdonságok, tulajdonságértékek és mérőszámok számára](#limits) .

*JavaScript*

```javascript
appInsights.trackEvent
    ("WinGame",
        // String properties:
        {Game: currentGame.name, Difficulty: currentGame.difficulty},
        // Numeric metrics:
        {Score: currentGame.score, Opponents: currentGame.opponentCount}
        );

appInsights.trackPageView
    ("page name", "http://fabrikam.com/pageurl.html",
        // String properties:
        {Game: currentGame.name, Difficulty: currentGame.difficulty},
        // Numeric metrics:
        {Score: currentGame.score, Opponents: currentGame.opponentCount}
        );
```

*C#*

```csharp
// Set up some properties and metrics:
var properties = new Dictionary <string, string>
    {{"game", currentGame.Name}, {"difficulty", currentGame.Difficulty}};
var metrics = new Dictionary <string, double>
    {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

// Send the event:
telemetry.TrackEvent("WinGame", properties, metrics);
```

*Node.js*

```javascript
// Set up some properties and metrics:
var properties = {"game": currentGame.Name, "difficulty": currentGame.Difficulty};
var metrics = {"Score": currentGame.Score, "Opponents": currentGame.OpponentCount};

// Send the event:
telemetry.trackEvent({name: "WinGame", properties: properties, measurements: metrics});
```

*Visual Basic*

```vb
' Set up some properties:
Dim properties = New Dictionary (Of String, String)
properties.Add("game", currentGame.Name)
properties.Add("difficulty", currentGame.Difficulty)

Dim metrics = New Dictionary (Of String, Double)
metrics.Add("Score", currentGame.Score)
metrics.Add("Opponents", currentGame.OpponentCount)

' Send the event:
telemetry.TrackEvent("WinGame", properties, metrics)
```

*Java*

```java
Map<String, String> properties = new HashMap<String, String>();
properties.put("game", currentGame.getName());
properties.put("difficulty", currentGame.getDifficulty());

Map<String, Double> metrics = new HashMap<String, Double>();
metrics.put("Score", currentGame.getScore());
metrics.put("Opponents", currentGame.getOpponentCount());

telemetry.trackEvent("WinGame", properties, metrics);
```

> [!NOTE]
> Ügyeljen arra, hogy ne naplózza a személyazonosításra alkalmas adatokat a tulajdonságok között.
>
>

### <a name="alternative-way-to-set-properties-and-metrics"></a>Tulajdonságok és metrikák beállításának alternatív módja

Ha kényelmesebb, egy különálló objektumban gyűjthet egy esemény paramétereit:

```csharp
var event = new EventTelemetry();

event.Name = "WinGame";
event.Metrics["processingTime"] = stopwatch.Elapsed.TotalMilliseconds;
event.Properties["game"] = currentGame.Name;
event.Properties["difficulty"] = currentGame.Difficulty;
event.Metrics["Score"] = currentGame.Score;
event.Metrics["Opponents"] = currentGame.Opponents.Length;

telemetry.TrackEvent(event);
```

> [!WARNING]
> Ne használja ugyanazt a telemetria-példányt (`event` ebben a példában) a Track * () többszöri hívásához. Ez a telemetria helytelen konfigurációval való elküldése miatt fordulhat elő.
>
>

### <a name="custom-measurements-and-properties-in-analytics"></a>Egyéni mérések és tulajdonságok az elemzésekben

Az [elemzésben](analytics.md)az egyéni metrikák és tulajdonságok az egyes `customMeasurements` telemetria `customDimensions` -rekordok és attribútumaik szerint jelennek meg.

Ha például hozzáadta a "Game" nevű tulajdonságot a kérelem telemetria, ez a lekérdezés a "játék" különböző értékeinek előfordulásait számolja, és megjeleníti az egyéni metrika "pontszáma" átlagát:

```kusto
requests
| summarize sum(itemCount), avg(todouble(customMeasurements.score)) by tostring(customDimensions.game)
```

Figyelje meg, hogy:

* Ha Kinyer egy értéket a customDimensions vagy a customMeasurements JSON-ból, dinamikus típussal rendelkezik, és ezért el kell `tostring` azt `todouble`tennie.
* A [mintavétel](../../azure-monitor/app/sampling.md)lehetőségének figyelembevételéhez a következőt kell használnia `sum(itemCount)` `count()`:.

## <a name="timed"></a>Időzítési események

Néha diagramot szeretne készíteni, hogy mennyi ideig tart egy művelet végrehajtása. Előfordulhat például, hogy meg szeretné tudni, hogy a felhasználók milyen hosszú időt vesznek igénybe a játékokban. Ehhez a mérési paramétert használhatja.

*C#*

```csharp
var stopwatch = System.Diagnostics.Stopwatch.StartNew();

// ... perform the timed action ...

stopwatch.Stop();

var metrics = new Dictionary <string, double>
    {{"processingTime", stopwatch.Elapsed.TotalMilliseconds}};

// Set up some properties:
var properties = new Dictionary <string, string>
    {{"signalSource", currentSignalSource.Name}};

// Send the event:
telemetry.TrackEvent("SignalProcessed", properties, metrics);
```

*Java*

```java
long startTime = System.currentTimeMillis();

// Perform timed action

long endTime = System.currentTimeMillis();
Map<String, Double> metrics = new HashMap<>();
metrics.put("ProcessingTime", (double)endTime-startTime);

// Setup some properties
Map<String, String> properties = new HashMap<>();
properties.put("signalSource", currentSignalSource.getName());

// Send the event
telemetry.trackEvent("SignalProcessed", properties, metrics);
```

## <a name="defaults"></a>Egyéni telemetria alapértelmezett tulajdonságai

Ha az Ön által írt egyéni események esetében meg szeretné határozni az alapértelmezett tulajdonságértékeket, beállíthatja őket egy TelemetryClient-példányban. Ezek az ügyféltől érkező összes telemetria-elemhez csatlakoznak.

*C#*

```csharp
using Microsoft.ApplicationInsights.DataContracts;

var gameTelemetry = new TelemetryClient();
gameTelemetry.Context.Properties["Game"] = currentGame.Name;
// Now all telemetry will automatically be sent with the context property:
gameTelemetry.TrackEvent("WinGame");
```

*Visual Basic*

```vb
Dim gameTelemetry = New TelemetryClient()
gameTelemetry.Context.Properties("Game") = currentGame.Name
' Now all telemetry will automatically be sent with the context property:
gameTelemetry.TrackEvent("WinGame")
```

*Java*

```java
import com.microsoft.applicationinsights.TelemetryClient;
import com.microsoft.applicationinsights.TelemetryContext;
...


TelemetryClient gameTelemetry = new TelemetryClient();
TelemetryContext context = gameTelemetry.getContext();
context.getProperties().put("Game", currentGame.Name);

gameTelemetry.TrackEvent("WinGame");
```

*Node.js*

```javascript
var gameTelemetry = new applicationInsights.TelemetryClient();
gameTelemetry.commonProperties["Game"] = currentGame.Name;

gameTelemetry.TrackEvent({name: "WinGame"});
```

Az egyes telemetria-hívások felülbírálják a tulajdonságok szótárában lévő alapértelmezett értékeket.

*JavaScript-alapú webes ügyfelek*esetén használja a JavaScript telemetria inicializáló.

*Ha tulajdonságokat szeretne hozzáadni az összes telemetria*, beleértve a standard gyűjtemények moduljaiból származó adatokkal is, [ `ITelemetryInitializer`implementálja ](../../azure-monitor/app/api-filtering-sampling.md#add-properties).

## <a name="sampling-filtering-and-processing-telemetry"></a>Telemetria mintavételezése, szűrése és feldolgozása

Az SDK-ból való elküldése előtt kódot írhat a telemetria feldolgozásához. A feldolgozás a szabványos telemetria-modulokból, például a HTTP-kérelmek gyűjtésével és a függőségek gyűjtésével elküldett adatokra vonatkozik.

[Adja hozzá](../../azure-monitor/app/api-filtering-sampling.md#add-properties) a telemetria tulajdonságokat a `ITelemetryInitializer`megvalósításhoz. Felveheti például a más tulajdonságok alapján számított verziószámokat vagy értékeket.

A [szűrés](../../azure-monitor/app/api-filtering-sampling.md#filtering) módosíthatja vagy elvetheti a TELEMETRIA az SDK-ból való `ITelemetryProcessor`elküldésük előtt. Ön határozza meg, hogy mi történjen az elküldésen vagy elvetve, de a metrikák hatásait kell figyelembe vennie. Az elemek elvetésének módjától függően elveszítheti a kapcsolódó elemek közötti váltás lehetőségét.

A [mintavétel](../../azure-monitor/app/api-filtering-sampling.md) egy csomagolt megoldás, amely csökkenti az alkalmazásból a portálra továbbított adatok mennyiségét. Ez nem befolyásolja a megjelenített metrikákat. Ez azonban nem befolyásolja a problémák diagnosztizálását a kapcsolódó elemek, például a kivételek, a kérelmek és az oldalletöltések közötti navigálás során.

[További információk](../../azure-monitor/app/api-filtering-sampling.md).

## <a name="disabling-telemetry"></a>Telemetria letiltása

A telemetria gyűjtésének és továbbításának *dinamikus leállítása és elindítása* :

*C#*

```csharp
using  Microsoft.ApplicationInsights.Extensibility;

TelemetryConfiguration.Active.DisableTelemetry = true;
```

*Java*

```java
telemetry.getConfiguration().setTrackingDisabled(true);
```

A *kiválasztott standard gyűjtők letiltása*– például TELJESÍTMÉNYSZÁMLÁLÓK, HTTP-kérelmek vagy függőségek – a [ApplicationInsights. config fájlban](../../azure-monitor/app/configuration-with-applicationinsights-config.md)lévő megfelelő sorok törlése vagy megjegyzése. Ezt például akkor teheti meg, ha a saját TrackRequest-adatait szeretné elküldeni.

*Node.js*

```javascript
telemetry.config.disableAppInsights = true;
```

A *kiválasztott standard gyűjtők*letiltásához – például a teljesítményszámlálók, a HTTP-kérelmek vagy a függőségek – inicializáláskor, a lánc konfigurációs módszereinek beállítása az SDK inicializálási kódjához:

```javascript
applicationInsights.setup()
    .setAutoCollectRequests(false)
    .setAutoCollectPerformance(false)
    .setAutoCollectExceptions(false)
    .setAutoCollectDependencies(false)
    .setAutoCollectConsole(false)
    .start();
```

Ha az inicializálás után le szeretné tiltani ezeket a gyűjtőket, használja a következő konfigurációs objektumot:`applicationInsights.Configuration.setAutoCollectRequests(false)`

## <a name="debug"></a>Fejlesztői mód

A hibakeresés során hasznos lehet a telemetria gyorsítása a folyamaton keresztül, hogy az eredmények azonnal megjelenjenek. Emellett további üzeneteket is megadhat, amelyek segítségével nyomon követheti a telemetria kapcsolatos problémákat. Kapcsolja ki az éles környezetben, mert lelassíthatja az alkalmazást.

*C#*

```csharp
TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = true;
```

*Visual Basic*

```vb
TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = True
```

*Node.js*

A Node. js esetében engedélyezheti a fejlesztői üzemmódot, ha engedélyezi a `setInternalLogging` belső naplózást a és a 0 értékre `maxBatchSize` , ami azt eredményezi, hogy a telemetria a gyűjtést követően azonnal elküldjék.

```js
applicationInsights.setup("ikey")
  .setInternalLogging(true, true)
  .start()
applicationInsights.defaultClient.config.maxBatchSize = 0;
```

## <a name="ikey"></a>A kialakítási kulcs beállítása a kiválasztott egyéni telemetria

*C#*

```csharp
var telemetry = new TelemetryClient();
telemetry.InstrumentationKey = "---my key---";
// ...
```

## <a name="dynamic-ikey"></a>Dynamic Instrumentation-kulcs

A fejlesztési, tesztelési és éles környezetek telemetria elkerülése érdekében [létrehozhat külön Application Insights erőforrásokat](../../azure-monitor/app/create-new-resource.md ) , és módosíthatja a kulcsokat a környezettől függően.

Ahelyett, hogy a rendszerállapot-kulcsot beolvassa a konfigurációs fájlból, beállíthatja azt a kódban. Állítsa be a kulcsot egy inicializálási metódusban, például global.aspx.cs egy ASP.NET-szolgáltatásban:

*C#*

```csharp
protected void Application_Start()
{
    Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey =
        // - for example -
        WebConfigurationManager.Settings["ikey"];
    ...
}
```

*JavaScript*

```javascript
appInsights.config.instrumentationKey = myKey;
```

A weblapokon érdemes lehet azt beállítani a webkiszolgáló állapotáról, nem pedig a szó szerinti kódolást a szkriptben. Például egy ASP.NET-alkalmazásban generált weblapon:

*JavaScript a Borotvában*

```cshtml
<script type="text/javascript">
// Standard Application Insights webpage script:
var appInsights = window.appInsights || function(config){ ...
// Modify this part:
}({instrumentationKey:  
    // Generate from server property:
    @Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey;
}) // ...
```

```java
    String instrumentationKey = "00000000-0000-0000-0000-000000000000";

    if (instrumentationKey != null)
    {
        TelemetryConfiguration.getActive().setInstrumentationKey(instrumentationKey);
    }
```

## <a name="telemetrycontext"></a>TelemetryContext

A TelemetryClient rendelkezik egy környezeti tulajdonsággal, amely az összes telemetria-adattal együtt elküldett értékeket tartalmazza. Ezeket általában a standard telemetria modulok határozzák meg, de saját maguk is megadhatók. Példa:

```csharp
telemetry.Context.Operation.Name = "MyOperationName";
```

Ha saját maga állítja be ezeket az értékeket, érdemes lehet eltávolítani a megfelelő sort a [ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md)fájlból, hogy az értékek és a standard értékek ne legyenek zavarosak.

* **Összetevő**: Az alkalmazás és annak verziója.
* **Eszköz**: Annak az eszköznek az adatait, amelyen az alkalmazás fut. (A Web Apps szolgáltatásban ez az a kiszolgáló vagy ügyfél-eszköz, amelyről a telemetria küldték.)
* **InstrumentationKey**: A Application Insights erőforrás az Azure-ban, ahol a telemetria megjelenik. Ez általában a ApplicationInsights. config fájlból lett kiválasztva.
* **Hely**: Az eszköz földrajzi helye.
* **Művelet**: A web Appsben a jelenlegi HTTP-kérelem. Más típusú alkalmazások esetében beállíthatja, hogy az események csoportosítva legyenek.
  * **AZONOSÍTÓ**: Olyan generált érték, amely különböző eseményeket kapcsol össze, így ha bármely eseményt megvizsgál a diagnosztikai keresésben, megtalálhatja a kapcsolódó elemeket.
  * **Név**: Egy azonosító, amely általában a HTTP-kérelem URL-címe.
  * **SyntheticSource**: Ha nem null értékű vagy üres, egy sztring, amely azt jelzi, hogy a kérelem forrása robotként vagy webes tesztként lett azonosítva. Alapértelmezés szerint a rendszer kizárja a számításokat a Metrikaböngészőban.
* **Tulajdonságok**: Az összes telemetria-adattal továbbított tulajdonságok Az egyes Track * hívásokban felülbírálható.
* **Munkamenet**: A felhasználó munkamenete. Az azonosító egy generált értékre van állítva, amely akkor változik, ha a felhasználó egy ideig nem aktív.
* **Felhasználó**: Felhasználói információk.

## <a name="limits"></a>Korlátok

[!INCLUDE [application-insights-limits](../../../includes/application-insights-limits.md)]

Az adatsebességi korlát elérésének elkerüléséhez [](../../azure-monitor/app/sampling.md)használja a mintavételezést.

Az adatok megőrzési időtartamának megállapításához tekintse meg az adatmegőrzést [és](../../azure-monitor/app/data-retention-privacy.md)az adatvédelmet.

## <a name="reference-docs"></a>Segédanyagok

* [ASP.NET-hivatkozás](https://msdn.microsoft.com/library/dn817570.aspx)
* [Java-referencia](http://dl.windowsazure.com/applicationinsights/javadoc/)
* [JavaScript-hivatkozás](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)
* [Android SDK](https://github.com/Microsoft/ApplicationInsights-Android)
* [iOS SDK](https://github.com/Microsoft/ApplicationInsights-iOS)

## <a name="sdk-code"></a>SDK-kód

* [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [ASP.NET 5](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [Windows Server-csomagok](https://github.com/Microsoft/applicationInsights-dotnet-server)
* [Java SDK](https://github.com/Microsoft/ApplicationInsights-Java)
* [Node.js SDK](https://github.com/Microsoft/ApplicationInsights-Node.js)
* [JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)
* [Minden platform](https://github.com/Microsoft?utf8=%E2%9C%93&query=applicationInsights)

## <a name="questions"></a>Kérdések

* *Milyen kivételeket okozhatnak a Track_ () hívásai?*

    Nincs. A try-catch záradékokban nem kell becsomagolni őket. Ha az SDK problémákba ütközik, a hibakeresési konzol kimenetében üzeneteket fog naplózni, és – ha az üzenetek – a diagnosztikai keresés során jelentkeznek.
* *Van REST API az adatok a portálról való lekéréséhez?*

    Igen, az [adatelérési API](https://dev.applicationinsights.io/). Az adatok kinyerésének egyéb módjai közé tartozik az [elemzésből való exportálás Power bi](../../azure-monitor/app/export-power-bi.md ) és a [folyamatos exportálás](../../azure-monitor/app/export-telemetry.md).

## <a name="next"></a>Következő lépések

* [Események és naplók keresése](../../azure-monitor/app/diagnostic-search.md)
* [Hibaelhárítás](../../azure-monitor/app/troubleshoot-faq.md)
