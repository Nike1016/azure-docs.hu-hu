---
title: Az Azure Állapotmonitor v2 áttekintése | Microsoft Docs
description: A Állapotmonitor v2 áttekintése. Webhelyek teljesítményének figyelése a webhely újbóli üzembe helyezése nélkül. Együttműködik a helyszínen, a virtuális gépeken vagy az Azure-on üzemeltetett ASP.NET Web Apps szolgáltatásokkal.
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: 0264cf3a972c35edb3ad6dc600ca39bdaa076dfd
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68333685"
---
# <a name="status-monitor-v2"></a>Állapotmonitor v2

Állapotmonitor v2 a [PowerShell-Galéria](https://www.powershellgallery.com/packages/Az.ApplicationMonitor)közzétett PowerShell-modul.
Lecseréli [Állapotmonitor](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now).
A modul az IIS szolgáltatásban üzemeltetett .NET-webalkalmazások kódolását teszi lehetővé.
A rendszer elküldje a telemetria a Azure Portalba, ahol [nyomon](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) követheti az alkalmazást.

## <a name="powershell-gallery"></a>PowerShell-galéria

Állapotmonitor v2 itt található: https://www.powershellgallery.com/packages/Az.ApplicationMonitor.

![PowerShell-galéria](https://img.shields.io/powershellgallery/v/Az.ApplicationMonitor.svg?color=Blue&label=Current%20Version&logo=PowerShell&style=for-the-badge)


## <a name="instructions"></a>Útmutatás
- Tekintse meg az [első lépéseket ismertető útmutatót](status-monitor-v2-get-started.md) , amelyből megtudhatja, hogyan kezdheti el a rövid kód mintáit.
- Tekintse meg az első lépések [részletes leírását](status-monitor-v2-detailed-instructions.md) .

## <a name="powershell-api-reference"></a>PowerShell API-referenciák
- [Disable-ApplicationInsightsMonitoring](status-monitor-v2-api-disable-monitoring.md)
- [InstrumentationEngine letiltása](status-monitor-v2-api-disable-instrumentation-engine.md)
- [Enable-ApplicationInsightsMonitoring](status-monitor-v2-api-enable-monitoring.md)
- [InstrumentationEngine engedélyezése](status-monitor-v2-api-enable-instrumentation-engine.md)
- [Get-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-get-config.md)
- [Get-ApplicationInsightsMonitoringStatus](status-monitor-v2-api-get-status.md)
- [Set-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-set-config.md)
- [Start – ApplicationInsightsMonitoringTrace](status-monitor-v2-api-start-trace.md)

## <a name="troubleshooting"></a>Hibaelhárítás
- [Hibaelhárítás](status-monitor-v2-troubleshoot.md)
- [Ismert problémák](status-monitor-v2-troubleshoot.md#known-issues)


## <a name="faq"></a>GYIK

- Támogatja a Állapotmonitor v2 a proxy telepítéseit?

  *Igen*. A Állapotmonitor v2 letöltése több módon is lehetséges. Ha a számítógépnek van internet-hozzáférése, akkor paraméterek használatával `-Proxy` bejelentkezhet a PowerShell-galériaba.
Manuálisan is letöltheti a modult, és telepítheti a számítógépre, vagy közvetlenül is használhatja azt.
Ezeket a beállításokat a [részletes utasítások](status-monitor-v2-detailed-instructions.md)ismertetik.
  
- Hogyan ellenőrizze, hogy az engedélyezés sikeres volt-e?

  - A [Get-ApplicationInsightsMonitoringStatus](status-monitor-v2-api-get-status.md) parancsmag használatával ellenőrizheti, hogy az engedélyezés sikeres volt-e.
  - Javasoljuk, hogy [élő metrikák](https://docs.microsoft.com/azure/azure-monitor/app/live-stream) használatával gyorsan megállapítsa, hogy az alkalmazás telemetria küld-e.

  - A [log Analytics](../log-query/get-started-portal.md) használatával is listázhatja az összes jelenleg telemetria küldő felhőalapú szerepkört:
      ```Kusto
      union * | summarize count() by cloud_RoleName, cloud_RoleInstance
      ```

## <a name="next-steps"></a>További lépések

A telemetriai adatok megtekintése:

* [Ismerje](../../azure-monitor/app/metrics-explorer.md) meg a mérőszámokat a teljesítmény és a használat figyeléséhez.
* [Események és naplók keresése](../../azure-monitor/app/diagnostic-search.md) a problémák diagnosztizálásához.
* További speciális lekérdezésekhez [használja](../../azure-monitor/app/analytics.md) az elemzést.
* [Irányítópultok létrehozása](../../azure-monitor/app/overview-dashboard.md).

További telemetriai funkciók hozzáadása:

* [Létrehozhat webes teszteket](monitor-web-app-availability.md) annak biztosításához, hogy a hely élőben maradjon.
* [Vegyen fel webes ügyfél-telemetria](../../azure-monitor/app/javascript.md) a kivételek megjelenítéséhez a weboldali kódból és a nyomkövetési hívások engedélyezéséhez.
* [Adja hozzá a Application INSIGHTS SDK-t a kódhoz](../../azure-monitor/app/asp-net.md) , hogy nyomkövetési és naplózási hívásokat helyezzen el.

