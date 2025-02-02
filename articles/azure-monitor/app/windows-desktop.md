---
title: Windowsos asztali alkalmazások használatának és teljesítményének figyelése
description: A windowsos asztali alkalmazások használatát és teljesítményét az Application Insights segítségével elemezheti.
services: application-insights
documentationcenter: windows
author: mrbullwinkle
manager: carmonm
ms.assetid: 19040746-3315-47e7-8c60-4b3000d2ddc4
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 08/09/2019
ms.author: mbullwin
ms.openlocfilehash: ed6df8b4724dbb297a0c64fd869d3377545a7595
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932337"
---
# <a name="monitoring-usage-and-performance-in-classic-windows-desktop-apps"></a>Klasszikus windowsos asztali alkalmazások használatának és teljesítményének figyelése

A helyszínen, az Azure-ban és más felhőben üzemeltetett alkalmazások is kihasználhatják az Application Insights előnyeit. Az egyetlen korlát, hogy [engedélyezni kell a kommunikációt](../../azure-monitor/app/ip-addresses.md) az Application Insights szolgáltatással. Az univerzális Windows-platformra (UWP) épülő alkalmazásokhoz a [Visual Studio App Center](../../azure-monitor/learn/mobile-center-quickstart.md) használatát javasoljuk.

## <a name="to-send-telemetry-to-application-insights-from-a-classic-windows-application"></a>Klasszikus Windows-alkalmazásból származó telemetriai adatok küldése az Application Insightsba
1. [Hozzon létre egy Application Insights-erőforrást](../../azure-monitor/app/create-new-resource.md ) az [Azure Portalon](https://portal.azure.com). Az alkalmazás típusánál válassza az ASP.NET-alkalmazás lehetőséget.
2. Végezze el a kialakítási kulcs másolását. A kulcs az imént létrehozott új erőforrás Alapvető szolgáltatások legördülő menüjében található. 
3. A Visual Studióban szerkessze az alkalmazási projekt NuGet-csomagjait, és vegye fel a Microsoft.ApplicationInsights.WindowsServer csomagot. (Vagy válassza a Microsoft.ApplicationInsights csomagot, ha csak az API-ra van szüksége a normál telemetriai adatgyűjtő modulok nélkül.)
4. Állítsa be a kialakítási kulcsot a kódban:
   
    `TelemetryConfiguration.Active.InstrumentationKey = "`*az Ön kulcsa*`";`
   
    vagy az ApplicationInsights.config fájlban (ha telepítette valamelyik normál telemetriai csomagot):
   
    `<InstrumentationKey>`*az Ön kulcsa*`</InstrumentationKey>` 
   
    Az ApplicationInsights.config használatakor győződjön meg arról, hogy annak tulajdonságait a következőre állította a Megoldáskezelőben: **Build Action = Content, Copy to Output Directory = Copy**.
5. [Az API-val](../../azure-monitor/app/api-custom-events-metrics.md) telemetriai adatokat küldhet.
6. Futtassa az alkalmazást, és tekintse meg a telemetria a Azure Portalban létrehozott erőforrásban.

## <a name="telemetry"></a>Mintakód
```csharp
using Microsoft.ApplicationInsights;

    public partial class Form1 : Form
    {
        private TelemetryClient tc = new TelemetryClient();
        ...
        private void Form1_Load(object sender, EventArgs e)
        {
            // Alternative to setting ikey in config file:
            tc.InstrumentationKey = "key copied from portal";

            // Set session data:
            tc.Context.Session.Id = Guid.NewGuid().ToString();
            tc.Context.Device.OperatingSystem = Environment.OSVersion.ToString();

            // Log a page view:
            tc.TrackPageView("Form1");
            ...
        }

        protected override void OnClosing(System.ComponentModel.CancelEventArgs e)
        {
            e.Cancel = true;

            if (tc != null)
            {
                tc.Flush(); // only for desktop apps

                // Allow time for flushing:
                System.Threading.Thread.Sleep(1000);
            }
            base.OnClosing(e);
        }

```

## <a name="next-steps"></a>További lépések
* [Irányítópult létrehozása](../../azure-monitor/app/overview-dashboard.md)
* [Diagnosztikai keresés](../../azure-monitor/app/diagnostic-search.md)
* [Metrikák böngészése](../../azure-monitor/app/metrics-explorer.md)
* [Analytics-lekérdezések](../../azure-monitor/app/analytics.md)

