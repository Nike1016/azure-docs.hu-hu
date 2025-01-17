---
title: Naplók a Azure Monitorban | Microsoft Docs
description: Ismerteti a Azure Monitor naplóit, amelyeket a rendszer a figyelési adatelemzések speciális elemzésekor használ.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: f6eaeb7c4a4d28fcf11ec9acda14629a79d00791
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68814332"
---
# <a name="logs-in-azure-monitor"></a>Naplók az Azure Monitorban

> [!NOTE]
> Az Azure Monitor által összegyűjtött összes adat a két alapvető típus, a metrikák és a naplók egyikére illeszkedik. Ez a cikk a naplókat ismerteti. A metrikák részletes ismertetését és a [Azure monitor által gyűjtött adatok figyelését](data-platform.md) a kettő összehasonlításával kapcsolatban a [Azure monitor metrikái](data-platform-metrics.md) című témakörben talál.

A Azure Monitorban lévő naplók különösen hasznosak a különböző forrásokból származó adatok összetett elemzésének elvégzéséhez. Ez a cikk azt ismerteti, hogyan épülnek fel a naplók a Azure Monitorba, mit tehet az adattal, és azonosíthatja azokat a különböző adatforrásokat, amelyek a naplókban tárolnak adattárakat.

> [!NOTE]
> Fontos különbséget tenni Azure Monitor naplók és a naplózási adatforrások között az Azure-ban. Az Azure-beli előfizetési szintű események például egy, a [](activity-logs-overview.md) Azure monitor menüből megtekinthető tevékenységi naplóba íródnak. A legtöbb erőforrás operatív adatokat ír egy olyan [diagnosztikai naplóba](diagnostic-logs-overview.md) , amelyet továbbíthat különböző helyszíneknek. Azure Monitor a naplók egy olyan naplózási adatplatform, amely a tevékenységek naplóit és a diagnosztikai naplókat más figyelési adatokkal együtt gyűjti, és a teljes erőforrás-készlet mély elemzését teszi lehetővé.

## <a name="what-are-azure-monitor-logs"></a>Mik azok a Azure Monitor naplók?

A Azure Monitorban lévő naplók különböző típusú adatokat tartalmaznak, amelyek különböző tulajdonságokkal rendelkeznek az egyes típusokhoz. A naplók tartalmazhatnak numerikus értékeket, például Azure Monitor metrikákat, de általában részletes leírásokkal ellátott szöveges adatokat tartalmaznak. Az adatok a szerkezetük változásakor eltérnek a mérőszám adataitól, és gyakran nem gyűjtik azokat rendszeres időközönként. A telemetria, például az eseményeket és a nyomkövetéseket a teljesítményadatok mellett Azure Monitor naplók tárolják, hogy az összes elemzésre lehessen összevonható legyen.

A naplózási bejegyzések közös típusa egy olyan esemény, amelyet szórványosan gyűjtenek. Az eseményeket egy alkalmazás vagy szolgáltatás hozza létre, és általában elegendő információt tartalmaz a teljes környezet biztosításához. Például az esemény azt jelzi, hogy egy adott erőforrás létrejött, és módosíthatók, megnövekedett forgalmának indított új gazdagépre, vagy hiba történt az alkalmazások.

 Mivel az adatok formátumát változhat, alkalmazások segítségével hozhat létre egyéni naplók szükségük van a struktúrát. A metrikai adatok a naplókban is tárolhatók, így a trendek és más adatelemzések esetében más figyelési adatokkal kombinálhatók.


## <a name="what-can-you-do-with-azure-monitor-logs"></a>Mire használhatók Azure Monitor naplók?
A következő táblázat felsorolja azokat a különböző módszereket, amelyekkel Azure Monitor naplókat használhat.


|  |  |
|:---|:---|
| Elemzés | A Azure Portal [log Analytics](../log-query/get-started-portal.md) használatával írhat napló- [lekérdezéseket](../log-query/log-query-overview.md) , és interaktív módon elemezheti a naplózási adataikat a hatékony adatkezelő Analysis Engine használatával.<br>A Azure Portal [Application Insights Analytics konzoljának](../app/analytics.md) használatával írhat napló-lekérdezéseket, és interaktív módon elemezheti a naplófájlok adatait a Application Insightsból. |
| Megjelenítés | A lekérdezés eredményei táblázatként vagy diagramként jelennek meg egy [Azure](../../azure-portal/azure-portal-dashboards.md)-irányítópulton.<br>Hozzon [](../app/usage-workbooks.md) létre egy munkafüzetet, amely kombinálható több adatkészlettel egy interaktív jelentésben. <br>A lekérdezés eredményeinek exportálására [Power BI](powerbi.md) különböző Vizualizációk használata és megosztása Azure-on kívüli felhasználókkal.<br>Egy lekérdezés eredményeinek exportálása a [Grafana](grafana-plugin.md) , hogy kihasználja az irányítópultot, és kombinálja más adatforrásokkal.|
| Riasztás | Konfigurálja egy [riasztási szabály](alerts-log.md) , amely elküld egy értesítést vagy veszi [művelet automatikus](action-groups.md) mikor a a lekérdezés eredménye megfelel-e egy adott eredményt.<br>Metrikai [riasztási szabály](alerts-metric-logs.md) konfigurálása bizonyos naplózott adatnaplókhoz mérőszámként kibontva. |
| Lekérés | A naplók lekérdezési eredményeinek elérése egy parancssorból az [Azure CLI](/cli/azure/ext/log-analytics/monitor/log-analytics)használatával.<br>A log lekérdezés eredményeinek elérése a parancssorból [PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights)-parancsmagok használatával.<br>Hozzáférés a naplók lekérdezési eredményeihez egy egyéni alkalmazásból [REST API](https://dev.loganalytics.io/)használatával. |
| Exportálás | Hozzon létre egy munkafolyamatot a naplófájlok beolvasásához, és másolja azt egy külső helyre [Logic apps](~/articles/logic-apps/index.yml)használatával. |


## <a name="how-is-data-in-azure-monitor-logs-structured"></a>Hogyan épülnek fel az Azure Monitor naplókba tartozó adathalmazok?
Azure Monitor naplók által gyűjtött adatokat egy [log Analytics](../platform/design-logs-deployment.md)munkaterületen tárolja a rendszer. Mindegyik munkaterület több táblát tartalmaz, amelyek mindegyike egy adott forrásból származó adatokat tárol. Míg az összes táblázat [közös tulajdonságokat](log-standard-properties.md)tartalmaz, mindegyiknek egyedi tulajdonságai vannak, az általa tárolt adattípustól függően. Az új munkaterületek szabványos táblái lesznek, és a különböző figyelési megoldások és más, a munkaterületre írt szolgáltatások is több táblát adnak hozzá.

A Application Insights adatok naplózása ugyanazokat a Log Analytics motort használja, mint a munkaterületek, de az egyes figyelt alkalmazások esetében külön tárolja őket. Minden alkalmazás rendelkezik egy szabványos táblázatokkal, amelyek az olyan adattárolási kérelmeket tartalmazzák, mint az alkalmazások, a kivételek és a lapok nézetei.

A naplók lekérdezése Log Analytics munkaterületről vagy egy Application Insights alkalmazásból származó adatokkal történik. Több erőforrást használó [lekérdezéssel](../log-query/cross-workspace-query.md) elemezheti az alkalmazásadatok és más naplózási adatforrásokat, illetve létrehozhat lekérdezéseket, több munkaterületet vagy alkalmazást is beleértve.

![Munkaterületek](media/data-platform-logs/workspaces.png)

## <a name="log-queries"></a>Naplólekérdezések
Azure Monitor naplókban lévő adatok a [Kusto lekérdezési nyelvével](../log-query/get-started-queries.md)írt [napló lekérdezéssel](../log-query/log-query-overview.md) kérhetők le, amely lehetővé teszi az összegyűjtött adatok gyors lekérését, összesítését és elemzését. A [log Analytics](../log-query/portals.md) használatával írhat és tesztelheti a napló lekérdezéseit a Azure Portalban. Lehetővé teszi, hogy interaktív módon működjön együtt az eredményekkel, vagy rögzítse őket egy irányítópulton, és megtekintse őket más vizualizációkkal.

![Log Analytics](media/data-platform-logs/log-analytics.png)

Application Insights adatok elemzéséhez nyissa meg [a Application Insights log Analyticsét](../app/analytics.md) .

![Application Insights Analitika](media/data-platform-logs/app-insights-analytics.png)

A naplózási adataikat a [log Analytics API](https://dev.loganalytics.io/documentation/overview) és a [Application Insights REST API](https://dev.applicationinsights.io/documentation/overview)használatával is lekérheti.


## <a name="sources-of-azure-monitor-logs"></a>Azure Monitor naplók forrásai
A Azure Monitor különböző forrásokból származó naplózási adatokat gyűjthet az Azure-ban és a helyszíni erőforrásokon belül is. A következő táblázatok felsorolják azokat a különböző adatforrásokat, amelyek különböző erőforrásokból állnak, amelyek az adatok Azure Monitor naplókba való írását írják le. Mindegyiknek van egy hivatkozása, amely a szükséges konfigurációk részleteit ismerteti.

### <a name="azure-tenant-and-subscription"></a>Azure-bérlő és-előfizetés

| Data | Leírás |
|:---|:---|
| Naplók Azure Active Directory | Az egyes könyvtárak diagnosztikai beállításain keresztül konfigurálhatók. Lásd: [Az Azure ad-naplók integrálása Azure monitor naplókkal](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md).  |
| Tevékenységnaplók | Alapértelmezés szerint külön tárolva, és közel valós idejű riasztásokhoz is használható. Telepítse a tevékenység log Analytics-megoldást Log Analytics munkaterületre való íráshoz. Lásd: [Az Azure-beli tevékenységek naplóinak összegyűjtése és elemzése log Analytics](activity-log-collect.md). |

### <a name="azure-resources"></a>Azure-erőforrások

| Data | Leírás |
|:---|:---|
| Erőforrás-diagnosztika | Diagnosztikai beállítások megadása a diagnosztikai adatokba való íráshoz, beleértve a Log Analytics munkaterülethez tartozó mérőszámokat is. Lásd: [stream Azure diagnosztikai naplók log Analytics](diagnostic-logs-stream-log-store.md). |
| Monitorozási megoldások | A figyelési megoldások a begyűjtött adatokat az Log Analytics munkaterületre írják. A megoldások listájának megtekintéséhez tekintse meg az [Azure-beli felügyeleti megoldások](../insights/solutions-inventory.md) adatgyűjtési részleteit. A megoldások telepítésével és használatával kapcsolatos részletekért tekintse meg [Azure monitor figyelési megoldásait](../insights/solutions.md) . |
| Mérőszámok | Platform metrikákat küldhet Azure Monitor erőforrásokhoz egy Log Analytics munkaterületre, hogy hosszabb ideig őrizze meg a naplózási adatokat, és összetett elemzéseket végezzen más adattípusokkal a [Kusto lekérdezési nyelv](/azure/kusto/query/)használatával. Lásd: [stream Azure diagnosztikai naplók log Analytics](diagnostic-logs-stream-log-store.md). |
| Azure-os táblaalapú tárolás | Gyűjtsön adatokat az Azure Storage-ból, ahol egyes Azure-erőforrások figyelik a figyelési adatokat. Lásd: az [Azure Blob Storage használata az IIS-hez és az Azure Table Storage-hoz a log Analyticsokkal](azure-storage-iis-table.md)kapcsolatos eseményekhez. |

### <a name="virtual-machines"></a>Virtuális gépek

| Data | Leírás |
|:---|:---|
|  Ügynökök adatforrásai | A [Windows](agent-windows.md) -és [Linux](../learn/quick-collect-linux-computer.md) -ügynököktől gyűjtött adatforrások közé tartoznak az események, a Teljesítményadatok és az egyéni naplók. Az adatforrások listájának és a konfiguráció részleteinek megtekintéséhez tekintse meg [Azure monitor ügynök](data-sources.md) adatforrásait. |
| Monitorozási megoldások | A figyelési megoldások az ügynököktől az Log Analytics munkaterületre gyűjtött adatokat írják. A megoldások listájának megtekintéséhez tekintse meg az [Azure-beli felügyeleti megoldások](../insights/solutions-inventory.md) adatgyűjtési részleteit. A megoldások telepítésével és használatával kapcsolatos részletekért tekintse meg [Azure monitor figyelési megoldásait](../insights/solutions.md) . |
| System Center Operations Manager | Operations Manager felügyeleti csoport összekapcsolása, hogy az események és teljesítményadatok a helyszíni ügynökökből a naplókba Azure Monitor legyenek összegyűjtve. Ennek a konfigurációnak a részleteiért lásd: [Operations Manager Összekapcsolása log Analytics](om-agents.md) . |


### <a name="applications"></a>Alkalmazások

| Data | Leírás |
|:---|:---|
| Kérések és kivételek | Az alkalmazásra vonatkozó kérelmekkel és kivételekkel kapcsolatos részletes információk a _kérelmek_, _oldalmegtekintések_és _kivételek_ táblákban találhatók. A [külső összetevőkre](../app/asp-net-dependencies.md) irányuló hívások a _függőségek_ táblázatban találhatók. |
| Használat és teljesítmény | Az alkalmazás teljesítménye a _kérelmek_, a _BrowserTimings_ és a _performanceCounters_ táblákban érhető el. Az [Egyéni metrikák](../app/api-custom-events-metrics.md#trackevent) adatai a _customMetrics_ táblában vannak.|
| Nyomkövetési adat | Az [elosztott nyomkövetés](../app/distributed-tracing.md) eredményeit a nyomkövetési táblázat tárolja. |
| Rendelkezésre állási tesztek | A [rendelkezésre állási tesztek](../app/monitor-web-app-availability.md) összegző adatait a _availabilityResults_ táblában tárolja a rendszer. A tesztek részletes adatait külön tárolóban tárolják, és a Azure Portal Application Insights érhetők el. |

### <a name="insights"></a>Insights

| Data | Leírás |
|:---|:---|
| Tárolókhoz készült Azure Monitor | [A tárolók Azure monitor](../insights/container-insights-overview.md)által gyűjtött leltározási és teljesítményadatokat. A táblák listájának megtekintéséhez lásd a [Container-adatok gyűjtésének részleteit](../insights/container-insights-log-search.md#container-records) . |
| Virtuális gépekhez készült Azure Monitor | A [Azure monitor for VMS](../insights/vminsights-overview.md)által összegyűjtött leképezési és teljesítményadatokat. Az adatok lekérdezésével kapcsolatos részletekért lásd: [naplók lekérdezése Azure monitor for VMsról](../insights/vminsights-log-search.md) . |

### <a name="custom"></a>Egyéni 

| Data | Leírás |
|:---|:---|
| REST API | Adatok írása egy Log Analytics munkaterületre bármely REST-ügyfélről. További részletekért lásd: [naplóbejegyzések küldése Azure monitor a http-adatgyűjtő API-val](data-collector-api.md) .
| Logic App | A Logic app-munkafolyamatokból az **Azure log Analytics adatgyűjtő** művelettel bármilyen adattípust írhat log Analytics munkaterületre. |

### <a name="security"></a>Biztonság

| Data | Leírás |
|:---|:---|
| Azure Security Center | A [Azure Security Center](/azure/security-center/) egy log Analytics munkaterületen tárolja az általa gyűjtött adatokat, ahol más naplózási adatokkal is elemezhető. A munkaterület-konfigurációval kapcsolatos részletekért tekintse meg a [Azure Security Center](../../security-center/security-center-enable-data-collection.md) adatgyűjtést ismertető témakört. |
| Azure Sentinel | Az [Azure Sentinel](/azure/sentinel/) adatforrásokból származó adatok log Analytics munkaterületre való tárolását tárolja. Lásd: [az](/azure/sentinel/connect-data-sources)adatforrások összekötése.  |


## <a name="next-steps"></a>További lépések

- További információ a [Azure monitor](data-platform.md)adatplatformról.
- [A Azure monitor metrikáinak](data-platform-metrics.md)megismerése.
- További információ a [monitorozási adatok elérhető](data-sources.md) a különböző erőforrásokat az Azure-ban.
