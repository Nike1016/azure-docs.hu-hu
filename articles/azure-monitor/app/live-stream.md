---
title: Élő metrikastream egyéni metrikákkal és diagnosztika az Azure Application Insightsban | Microsoft Docs
description: Valós időben figyelheti a webalkalmazást egyéni metrikákkal, és diagnosztizálhatja a hibákat, nyomokat és eseményeket élő hírcsatornával.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 1f471176-38f3-40b3-bc6d-3f47d0cbaaa2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/22/2019
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: f8203cade1d2e34a9852e945df03dc2fddc1fbe5
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359416"
---
# <a name="live-metrics-stream-monitor--diagnose-with-1-second-latency"></a>Élő metrikastream: 1 másodperces késéssel figyelje & diagnosztizálása

Az éles környezetben futó webalkalmazások dobogós szívének mintavétele a [Application Insights](../../azure-monitor/app/app-insights-overview.md)élő metrikastream használatával. A metrikák és teljesítményszámlálók kiválasztásával valós időben figyelheti a szolgáltatást, és nem zavarja a szolgáltatását. A sikertelen kérelmek és kivételek alapján ellenőrizze a verem nyomkövetéseit. A [Profilerrel](../../azure-monitor/app/profiler.md)együtt a [Snapshot Debugger](../../azure-monitor/app/snapshot-debugger.md). A Élő metrikastream egy hatékony és nem invazív diagnosztikai eszközt biztosít az élő webhelyhez.

A Élő metrikastream a következőket teheti:

* A teljesítmény és a hibák számának figyelésével ellenőrizze, hogy megjelent-e a javítás.
* Tekintse meg a teszt betöltésének hatását, és az élő problémák diagnosztizálását. 
* A megtekinteni kívánt metrikák kiválasztásával és szűrésével kiválaszthatja az egyes tesztelési munkameneteket, vagy kiszűrheti az ismert problémákat.
* Kivételek beolvasása, ahogy történnek.
* Próbálja ki a szűrőket a legfontosabb KPI-k megkereséséhez.
* Figyelje meg a Windows teljesítményszámláló élő működését.
* Könnyedén azonosíthatja a problémákat okozó kiszolgálót, és az összes KPI/Live-hírcsatornát szűrheti csak erre a kiszolgálóra.

[![Élő metrikastream videó](./media/live-stream/youtube.png)](https://www.youtube.com/watch?v=zqfHf1Oi5PY)

Az élő metrikák jelenleg ASP.NET, ASP.NET Core, Azure Functions, Java és Node. js-alkalmazások esetén támogatottak.

## <a name="get-started"></a>Bevezetés

1. Ha még nem [telepítette Application Insights](../../azure-monitor/azure-monitor-app-hub.md) a webalkalmazásban, tegye meg most.
2. A standard Application Insights csomagokon kívül a [Microsoft. ApplicationInsights. PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) csomagok is szükségesek az élő metrikák adatfolyamának engedélyezéséhez.
3. Frissítsen a Application Insights csomag **legújabb verziójára** . A Visual Studióban kattintson a jobb gombbal a projektre, és válassza a **Nuget-csomagok kezelése**lehetőséget. Nyissa meg a **frissítések** lapot, és válassza ki az összes Microsoft. ApplicationInsights. * csomagot.

    Helyezze ismét üzembe alkalmazását.

3. A [Azure Portal](https://portal.azure.com)nyissa meg az alkalmazás Application Insights erőforrását, majd nyissa meg élő stream.

4. Ha kényes adatokat, például a szűrőket használó ügyfelek nevét használja, [gondoskodjon a vezérlési csatorna védelméről](#secure-the-control-channel) .

### <a name="nodejs"></a>Node.js

Ha a Node. js használatával szeretne élő metrikákat használni, frissítenie kell az SDK 1,30-es vagy újabb verziójára. Alapértelmezés szerint az élő metrikák le vannak tiltva a Node. js SDK-ban. Ha engedélyezni szeretné az élő metrikák hozzáadását `setSendLiveMetrics(true)` a [konfigurációs módszerekhez](https://github.com/Microsoft/ApplicationInsights-node.js#configuration) , az SDK inicializálásakor.

### <a name="no-data-check-your-server-firewall"></a>Nincs adat? A kiszolgáló tűzfalának keresése

Győződjön meg arról, hogy a [kimenő portok élő metrikastream](../../azure-monitor/app/ip-addresses.md#outgoing-ports) a kiszolgálók tűzfalán vannak megnyitva. 

## <a name="how-does-live-metrics-stream-differ-from-metrics-explorer-and-analytics"></a>Miben különbözik az Élő metrikastream a Metrikaböngésző és az elemzéstől?

| |Élő stream | Metrikaböngésző és elemzés |
|---|---|---|
|Késés|Egy másodpercen belül megjelenített adatértékek|Percek alatt összesítve|
|Nincs megőrzés|Az adatmegőrzési idő a diagramon marad, és a rendszer elveti|[90 napig megőrzött adat](../../azure-monitor/app/data-retention-privacy.md#how-long-is-the-data-kept)|
|Igény szerinti|Az adatok továbbítása az élő metrikák megnyitásakor történik|Ha az SDK telepítve és engedélyezve van, az adatküldés történik|
|Free|Élő stream-adatszolgáltatásért nem számítunk fel díjat|A [díjszabás](../../azure-monitor/app/pricing.md) hatálya alá tartozik
|Mintavételezés|Minden kiválasztott metrika és számláló továbbítva van. A hibák és a verem nyomkövetési mintája. A TelemetryProcessors nincsenek alkalmazva.|Az események [mintavétele](../../azure-monitor/app/api-filtering-sampling.md) megtörténhet|
|Vezérlési csatorna|A szűrő vezérlő jeleit a rendszer elküldi az SDK-nak. Javasoljuk, hogy gondoskodjon a csatorna biztonságáról.|Egyirányú kommunikáció a portálon|

## <a name="select-and-filter-your-metrics"></a>Metrikák kiválasztása és szűrése

(ASP.NET, ASP.NET Core és Azure Functions (v2) érhető el.)

Megfigyelheti az egyéni KPI-ket, ha tetszőleges szűrőket alkalmaz bármilyen Application Insights telemetria a portálon. Kattintson arra a szűrő vezérlőelemre, amely akkor jelenik meg, ha az egérmutatót valamelyik diagramon átadja. A következő diagram egy egyéni kérések számának KPI-jét ábrázolja az URL-címek és az időtartam attribútumainak szűrésével. Érvényesítse a szűrőket a stream Preview szakaszával, amely a telemetria élő hírcsatornáit jeleníti meg, amelyek megfelelnek az adott időpontban megadott feltételeknek. 

![Egyéni kérelem KPI](./media/live-stream/live-stream-filteredMetric.png)

A darabszámtól eltérő értékeket is megfigyelheti. A beállítások a stream típusától függenek, amely bármilyen Application Insights telemetria lehet: kérelmek, függőségek, kivételek, Nyomkövetések, események vagy mérőszámok. A saját [Egyéni mérőszáma](../../azure-monitor/app/api-custom-events-metrics.md#properties)lehet:

![Érték beállításai](./media/live-stream/live-stream-valueoptions.png)

Application Insights telemetria kívül a Windows-teljesítményszámlálók figyelését is megfigyelheti, ha kijelöli az stream beállításai közül, és megadja a teljesítményszámláló nevét.

Az élő metrikák összesítése két ponton történik: helyileg az egyes kiszolgálókon, majd az összes kiszolgálón. Az alapértelmezett érték a megfelelő legördülő menüben található egyéb beállítások lehetőség kiválasztásával módosítható.

## <a name="sample-telemetry-custom-live-diagnostic-events"></a>Minta telemetria: Egyéni élő diagnosztikai események
Alapértelmezés szerint az események élő hírcsatornája a sikertelen kérelmek és függőségi hívások, kivételek, események és Nyomkövetések mintáit jeleníti meg. Kattintson a szűrő ikonra az alkalmazott feltételek bármely időpontban történő megtekintéséhez. 

![Alapértelmezett élő csatorna](./media/live-stream/live-stream-eventsdefault.png)

Mint a metrikák esetében, tetszőleges feltételek bármelyikét megadhatja a Application Insights telemetria-típusaihoz. Ebben a példában az adott kérelmekkel kapcsolatos hibák, Nyomkövetések és események vannak kiválasztva. Az összes kivételt és függőségi hibát is kiválasztjuk.

![Egyéni élő hírcsatorna](./media/live-stream/live-stream-events.png)

Megjegyzés: Jelenleg a kivételt jelző üzenetekre vonatkozó feltételek esetén használja a legkülső kivételként szolgáló üzenetet. Az előző példában a belső kivételű üzenettel való jóindulatú kivétel kiszűréséhez (a "< –" elválasztó karakterrel) "az ügyfél leválasztva". "hiba történt a kérelem tartalmának olvasása" feltételt nem tartalmazó üzenet használata.

Az élő hírcsatornában található elemek részleteinek megtekintéséhez kattintson rá. A hírcsatornát szüneteltetheti, ha a **szüneteltetés** gombra kattint, vagy egyszerűen lefelé görget, vagy rákattint egy elemre. Az élő hírcsatornák a felülre görgetés után is folytatódnak, vagy a felfüggesztéskor gyűjtött elemek számlálóra kattintanak.

![Mintául szolgáló élő hibák](./media/live-stream/live-metrics-eventdetail.png)

## <a name="filter-by-server-instance"></a>Szűrés kiszolgálópéldány szerint

Ha egy adott kiszolgálói szerepkör-példányt szeretne figyelni, akkor a kiszolgáló alapján szűrhet.

![Mintául szolgáló élő hibák](./media/live-stream/live-stream-filter.png)

## <a name="sdk-requirements"></a>SDK-követelmények
Az egyéni Élő metrikastream a [Application INSIGHTS SDK for web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/)2.4.0-Beta2 vagy újabb verziójával érhető el. Ne felejtse el kiválasztani az "előzetes kiadás" lehetőséget a NuGet Package managerből.

## <a name="secure-the-control-channel"></a>A vezérlő csatorna biztonságossá tétele
Az egyéni szűrők megadott feltételeit a rendszer visszaküldi a Application Insights SDK élő metrikák összetevőjére. A szűrők potenciálisan bizalmas adatokat is tartalmazhatnak, például customerIDs. A csatornát a kialakítási kulcs mellett titkos API-kulccsal is biztonságossá teheti.
### <a name="create-an-api-key"></a>API-kulcs létrehozása

![API-kulcs létrehozása](./media/live-stream/live-metrics-apikeycreate.png)

### <a name="add-api-key-to-configuration"></a>API-kulcs hozzáadása a konfigurációhoz

### <a name="classic-aspnet"></a>Klasszikus ASP.NET

A applicationinsights. config fájlban adja hozzá a AuthenticationApiKey a QuickPulseTelemetryModule:
``` XML

<Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <AuthenticationApiKey>YOUR-API-KEY-HERE</AuthenticationApiKey>
</Add>

```
Vagy a kódban adja meg a QuickPulseTelemetryModule:

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
using Microsoft.ApplicationInsights.Extensibility;

             TelemetryConfiguration configuration = new TelemetryConfiguration();
            configuration.InstrumentationKey = "YOUR-IKEY-HERE";

            QuickPulseTelemetryProcessor processor = null;

            configuration.TelemetryProcessorChainBuilder
                .Use((next) =>
                {
                    processor = new QuickPulseTelemetryProcessor(next);
                    return processor;
                })
                        .Build();

            var QuickPulse = new QuickPulseTelemetryModule()
            {

                AuthenticationApiKey = "YOUR-API-KEY"
            };
            QuickPulse.Initialize(configuration);
            QuickPulse.RegisterTelemetryProcessor(processor);
            foreach (var telemetryProcessor in configuration.TelemetryProcessors)
                {
                if (telemetryProcessor is ITelemetryModule telemetryModule)
                    {
                    telemetryModule.Initialize(configuration);
                    }
                }

```

### <a name="azure-function-apps"></a>Azure Function-alkalmazások

Az Azure Function apps (v2) esetében, ha a csatornát egy API-kulccsal védi, környezeti változóval végezhető el. 

Hozzon létre egy API-kulcsot a Application Insights erőforrásból, és lépjen a függvényalkalmazás **alkalmazás beállításaihoz** . Válassza az **új beállítás hozzáadása** lehetőséget, és adjon `APPINSIGHTS_QUICKPULSEAUTHAPIKEY` meg egy nevet és egy olyan értéket, amely megfelel az API-kulcsnak.

### <a name="aspnet-core-requires-application-insights-aspnet-core-sdk-230-beta-or-greater"></a>ASP.NET Core (Application Insights ASP.NET Core SDK 2.3.0-Beta vagy újabb verzió szükséges)

Módosítsa a startup.cs-fájlt a következőképpen:

Első Hozzáadás

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
```

Ezután a ConfigureServices metódus Hozzáadás:

```csharp
services.ConfigureTelemetryModule<QuickPulseTelemetryModule> ((module, o) => module.AuthenticationApiKey = "YOUR-API-KEY-HERE");
```

Ha azonban felismeri és megbízik az összes csatlakoztatott kiszolgálón, a hitelesített csatorna nélkül is kipróbálhatja az egyéni szűrőket. Ez a beállítás hat hónapig elérhető. Ezt a felülbírálást minden új munkamenet esetében meg kell adni, vagy ha egy új kiszolgáló online állapotba kerül.

![Élő metrikák hitelesítési beállításai](./media/live-stream/live-stream-auth.png)

>[!NOTE]
>Javasoljuk, hogy a hitelesített csatornát úgy állítsa be, hogy a szűrési feltételeknek megfelelő bizalmas információk (például a Vevőkód) megadása előtt.
>

## <a name="troubleshooting"></a>Hibaelhárítás

Nincs adat? Ha az alkalmazás védett hálózaton van: Élő metrikastream eltérő IP-címeket használ, mint a többi Application Insights telemetria. Győződjön meg arról, hogy az [IP-címek](../../azure-monitor/app/ip-addresses.md) meg vannak nyitva a tűzfalon.



## <a name="next-steps"></a>További lépések
* [Használat figyelése Application Insights](../../azure-monitor/app/usage-overview.md)
* [A diagnosztikai keresés használata](../../azure-monitor/app/diagnostic-search.md)
* [Profilkészítő](../../azure-monitor/app/profiler.md)
* [Pillanatkép-hibakereső](../../azure-monitor/app/snapshot-debugger.md)
