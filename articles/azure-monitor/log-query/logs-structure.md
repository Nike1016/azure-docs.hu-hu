---
title: Azure Monitor naplók szerkezete | Microsoft Docs
description: A naplózási adatok Azure Monitorból való lekéréséhez naplózási lekérdezés szükséges.  Ez a cikk azt ismerteti, hogyan használhatók az új naplók a Azure Monitorban, és olyan fogalmakat tartalmaz, amelyeket meg kell értenie, mielőtt újat hozna létre.
services: log-analytics
author: bwren
ms.service: log-analytics
ms.topic: conceptual
ms.date: 06/16/2019
ms.author: bwren
ms.openlocfilehash: 6f5ae426018c9e7fa2ac586a2886c8e5e609069b
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68813844"
---
# <a name="structure-of-azure-monitor-logs"></a>Azure Monitor naplók szerkezete
A Azure Monitor hatékony funkciója, hogy gyorsan betekintést nyerjen az adataiba a [log lekérdezés](log-query-overview.md) használatával. Hatékony és hasznos lekérdezések létrehozásához ismernie kell néhány olyan alapfogalmakat, mint például a keresett adatok, valamint a strukturált adatok. Ez a cikk az első lépésekhez szükséges alapvető fogalmakat ismerteti.

## <a name="overview"></a>Áttekintés
Azure Monitor naplókban lévő adatLog Analytics-munkaterületen vagy egy Application Insights alkalmazásban van tárolva. Mindkettő az [Azure adatkezelő](/azure/data-explorer/) , ami azt jelenti, hogy hatékony adatmotort és lekérdezési nyelvet használ.

A munkaterületeken és alkalmazásokban található adatkészletek táblázatokba vannak rendezve, amelyek mindegyike különböző típusú adattípusokat tárol, és saját tulajdonságokkal rendelkezik. A legtöbb [adatforrás](../platform/data-sources.md) egy log Analytics munkaterületen fogja írni a saját tábláiba, míg a Application Insights egy Application Insights alkalmazásban előre meghatározott táblákba ír. A naplók nagy rugalmasságot biztosítanak, így egyszerűen egyesítheti a több táblázat adatait, és akár egy erőforrás-lekérdezést is használhat több munkaterület tábláiból származó adatok összevonásához, illetve a munkaterület és az alkalmazásadatok összekapcsolására szolgáló lekérdezések írásához.

Az alábbi képen a példák olyan adatforrásokra mutatnak, amelyek különböző, a lekérdezésekben használt táblákba írnak.

![Táblák](media/logs-structure/queries-tables.png)

## <a name="log-analytics-workspace"></a>Log Analytics-munkaterület
A Azure Monitor naplók által összegyűjtött összes adatokat, kivéve a Application Insights [log Analytics](../platform/manage-access.md)munkaterületen vannak tárolva. Az adott követelményektől függően egy vagy több munkaterületet is létrehozhat. [](../platform/data-sources.md) Az adatforrások, például a Tevékenységnaplók és a diagnosztikai naplók az Azure-erőforrásokból, a virtuális gépek ügynökei, valamint az elemzésből és a figyelési megoldásokból származó adatok egy vagy több olyan munkaterületre fognak írni, amelyet a bevezetésük részeként konfigurál. Más szolgáltatások, mint például a [Azure Security Center](/azure/security-center/) és az [Azure Sentinel](/azure/sentinel/) is egy log Analytics munkaterületet használ az adatok tárolására, így a naplózási lekérdezésekkel együtt, más forrásokból származó figyelési adatokkal is elemezhetők.

A különböző típusú adattípusok a munkaterület különböző tábláiban vannak tárolva, és mindegyik tábla egyedi tulajdonságokkal rendelkezik. A rendszer a létrehozott munkaterületekhez egy szabványos készletet ad hozzá, és új táblákat ad hozzá a különböző adatforrásokhoz, megoldásokhoz és szolgáltatásokhoz, mint a bevezetésük. Az [adatgyűjtő API](../platform/data-collector-api.md)használatával egyéni táblákat is létrehozhat.

A munkaterületen található táblákat és azok sémáját a munkaterület Log Analytics **séma** lapján böngészheti.

![Munkaterület sémája](media/scope/workspace-schema.png)

A következő lekérdezéssel listázhatja a munkaterületen lévő táblákat és az előző nap folyamán gyűjtött rekordok számát. 

```Kusto
union withsource = table * 
| where TimeGenerated > ago(1d)
| summarize count() by table
| sort by table asc
```
A létrehozott táblák részleteit az egyes adatforrások dokumentációja tartalmazza. Ilyenek például az [ügynök](../platform/agent-data-sources.md)adatforrásaira, a [diagnosztikai naplókra](../platform/diagnostic-logs-schema.md)és a [figyelési megoldásokra](../insights/solutions-inventory.md)vonatkozó cikkek.

### <a name="workspace-permissions"></a>Munkaterület engedélyei
A munkaterület adataihoz való hozzáférés biztosításával kapcsolatos részletekért tekintse meg a [munkaterület engedélyei és hatóköre](../platform/manage-access.md#manage-accounts-and-users) című témakört. A munkaterülethez való hozzáférés biztosítása mellett a [tábla szintű RBAC](../platform/manage-access.md#table-level-rbac)használatával korlátozhatja az egyes táblákhoz való hozzáférést.

## <a name="application-insights-application"></a>Application Insights alkalmazás
Amikor Application Insightsban hoz létre alkalmazást, a rendszer automatikusan létrehoz egy megfelelő alkalmazást Azure Monitor-naplókból. Az adatok gyűjtéséhez nincs szükség konfigurációra, és az alkalmazás automatikusan írási adatokat (például oldalletöltések, kérések és kivételek) fog írni.

A Log Analytics munkaterülettől eltérően egy Application Insights alkalmazásnak van egy rögzített készlete. Más adatforrások nem konfigurálhatók az alkalmazásba való íráshoz, így további táblák nem hozhatók létre. 

| Tábla | Leírás | 
|:---|:---|
| availabilityResults | A rendelkezésre állási tesztek összesített adatai. |
| browserTimings      | Az ügyfél teljesítményére vonatkozó adat, például a bejövő adat feldolgozásához szükséges idő. |
| customEvents        | Az alkalmazás által létrehozott egyéni események. |
| customMetrics       | Az alkalmazás által létrehozott egyéni metrikák. |
| függőségek        | Az alkalmazásból külső összetevőkre irányuló hívások. |
| kivétel          | Az alkalmazás futtatókörnyezete által kiváltott kivételek. |
| Oldalmegtekintések           | Adatok az egyes webhelyekről a böngésző információi között. |
| performanceCounters | Az alkalmazást támogató számítási erőforrások teljesítményének mérése. |
| kérelmek            | Az egyes alkalmazásokra vonatkozó kérelmek részletei.  |
| nyomok              | Az elosztott nyomkövetés eredményei. |

Az alkalmazáshoz tartozó Log Analytics **séma** lapján megtekintheti az egyes táblák sémáját.

![Alkalmazás sémája](media/scope/application-schema.png)

## <a name="standard-properties"></a>Szokásos tulajdonságok
Míg Azure Monitor naplók minden táblája saját sémával rendelkezik, az összes tábla közös tulajdonságokat tartalmaz. További részletekért tekintse [meg a Azure monitor naplóinak szabványos tulajdonságait](../platform/log-standard-properties.md) .

| Log Analytics-munkaterület | Application Insights alkalmazás | Leírás |
|:---|:---|:---|
| TimeGenerated | timestamp  | A rekord létrehozásának dátuma és időpontja. |
| Type          | itemType   | Annak a táblának a neve, amelyből a rekordot beolvasták. |
| _ResourceId   |            | Azon erőforrás egyedi azonosítója, amelyhez a rekord társítva van. |
| _IsBillable   |            | Meghatározza, hogy a betöltött adatmennyiség számlázható-e. |
| _BilledSize   |            | Meghatározza a számlázandó adatmennyiség bájtban kifejezett méretét. |

## <a name="next-steps"></a>További lépések
- Tudnivalók a [naplók keresésének létrehozásához és szerkesztéséhez log Analytics](../log-query/portals.md)használatáról.
- Az új lekérdezési nyelv használatával tekintse [meg a lekérdezések írásához szóló oktatóanyagot](../log-query/get-started-queries.md) .
