---
title: Az Azure Service Fabric Eseményelemzés az Azure Monitor-naplók |} A Microsoft Docs
description: További információ megjelenítése, és a monitorozást és diagnosztikát az Azure Service Fabric-fürtök események az Azure Monitor használatával naplózza.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/21/2019
ms.author: srrengar
ms.openlocfilehash: ba4923edbc59f0e6650fda1a71e1c4f79b884cf2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60393494"
---
# <a name="event-analysis-and-visualization-with-azure-monitor-logs"></a>Esemény elemzése és képi megjelenítése a Azure Monitor naplóira
 Az Azure Monitor naplóira gyűjti és elemzi az alkalmazások és szolgáltatások a felhőben üzemeltetett származó telemetriai adatok, és segítséget nyújtanak a rendelkezésre állás és teljesítmény maximalizálása elemzésére szolgáló eszközöket biztosít. Ez a cikk ismerteti, hogyan lekérdezéseket futtathat az Azure Monitor-naplók elemzése és hibaelhárítása, mi történik a fürtön. Az alábbi gyakori kérdések foglalkozik:

* Hogyan háríthatom el a health-események?
* Honnan tudhatom meg, amikor egy csomópont leáll?
* Honnan tudhatom, hogy ha saját alkalmazásszolgáltatások elindítani vagy leállítani?

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="overview-of-the-log-analytics-workspace"></a>A Log Analytics-munkaterület áttekintése

>[!NOTE] 
>Diagnosztikai tárfiók alapértelmezés szerint engedélyezve van a fürt létrehozásakor, miközben továbbra is be kell állítania a Log Analytics-munkaterületet a diagnosztikai tárfiók olvasni.

Az Azure Monitor a felügyelt erőforrások, például egy Azure storage-táblába, vagy az ügynök összegyűjti az adatokat naplózza, és egy központi tárházban megőrzi azt. Az adatok ezután lehet, elemzés, a riasztás és a Vizualizáció használatos, vagy további exportálását. Az Azure Monitor támogatja az események, teljesítményadatok vagy bármely más egyéni adatokat naplózza. Tekintse meg [eseményeket a diagnosztikai bővítmény konfigurálásának lépései](service-fabric-diagnostics-event-aggregation-wad.md) és [olvasni a storage-ban az események Log Analytics-munkaterület létrehozásához szükséges lépéseket](service-fabric-diagnostics-oms-setup.md) , hogy az Azure Monitor adatok beérkeznek naplók.

Az Azure Monitor-naplók által adatok fogadását követően az Azure rendelkezik több *figyelési megoldások* , amelyek előre összeállított megoldások vagy figyelheti a bejövő adatokat, testre szabva, hogy számos forgatókönyv operatív irányítópultokat. Ezek közé tartozik egy *Service Fabric-elemzés* megoldás és a egy *tárolók* megoldást, amely két dolgokat azok, diagnosztika és figyelés a Service Fabric-fürtök használatakor. Ez a cikk ismerteti, hogyan használható a Service Fabric-elemzés megoldást, amely a munkaterület jön létre.

## <a name="access-the-service-fabric-analytics-solution"></a>Hozzáférés a Service Fabric-elemzés megoldás

Az a [az Azure Portal](https://portal.azure.com), nyissa meg az erőforráscsoport, amelyben létrehozta a Service Fabric-elemzés megoldás.

Válassza ki az erőforrást **ServiceFabric\<nameOfOMSWorkspace\>** .

A `Summary`, csempék formájában grafikon, látni fogja az egyes, a megoldások engedélyezve van, amelyek közül az egyik a Service fabric. Kattintson a **Service Fabric** graph, hogy a Service Fabric-elemzés megoldás.

![A Service Fabric-megoldás](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_summary.PNG)

Az alábbi képen látható a Service Fabric-elemzés megoldás kezdőlapján. A kezdőlap nyújt a pillanatkép, hogy mi történik a fürtön.

![A Service Fabric-megoldás](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_solution.PNG)

 Ha engedélyezte a diagnosztikai fürt létrehozásakor, láthatja az események 

* [A Service Fabric-fürthöz kapcsolódó események](service-fabric-diagnostics-event-generation-operational.md)
* [A Reliable Actors programozási modell események](service-fabric-reliable-actors-diagnostics.md)
* [A Reliable Services programozási modell események](service-fabric-reliable-services-diagnostics.md)

>[!NOTE]
>A beépített Service Fabric-események, valamint részletesebb rendszeresemények által gyűjtött [a diagnosztikai bővítmény, a konfiguráció frissítésével](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations).

## <a name="view-service-fabric-events-including-actions-on-nodes"></a>Nézet Service Fabric-események, beleértve a csomópontokon műveletek

A Service Fabric-elemzés oldalon kattintson a diagramon az **Service Fabric-események**.

![Service Fabric megoldást műveleti csatorna](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events_selection.png)

Kattintson a **lista** az események megtekintése a listában. Miután itt látni fogja a rendszer minden olyan események, összegyűjtött alkalmazásproblémát. Referenciaként az alábbiak a **WADServiceFabricSystemEventsTable** az Azure Storage-fiókot, és ehhez hasonlóan a megbízható szolgáltatások és az actors esemény ezután megjelenik megfelelő táblák származnak.
    
![Lekérdezés műveleti csatorna](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events.png)

Másik lehetőségként kattintson a Nagyító ikonra a bal oldali és a Kusto-lekérdezési nyelv használatával találja, amit keres. Ha például a fürtben található csomópontok végzett összes művelet megkereséséhez használhatja a következő lekérdezést. Az alábbiakban használt eseményazonosítók találhatók a [műveleti csatorna események referencia](service-fabric-diagnostics-event-generation-operational.md).

```kusto
ServiceFabricOperationalEvent
| where EventId < 25627 and EventId > 25619 
```

A számos további mezők, például a csomópontok (számítógép) a rendszer szolgáltatás (TaskName) lekérdezheti.

## <a name="view-service-fabric-reliable-service-and-actor-events"></a>Nézet Service Fabric Reliable Services és az Aktor esemény

A Service Fabric-elemzés oldalon kattintson a diagram a **Reliable Services**.

![A Service Fabric megoldást a Reliable Services](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_services_events_selection.png)

Kattintson a **lista** az események megtekintése a listában. Itt láthatja a reliable services eseményei. A különböző események láthatja, ha a szolgáltatás runasync elkezdődött és befejeződött, amely általában akkor fordul elő a telepítésekkel és frissítésekkel. 

![A Reliable Services lekérdezés](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_service_events.png)

Reliable actors-események hasonló módon is megtekinthetők. A reliable actors részletesebb események beállításához módosítani szeretné a `scheduledTransferKeywordFilter` a Config a diagnosztikai bővítmény (lásd alább). Ezek a következők az értékeket a részletek a [a reliable actors-események referencia](service-fabric-reliable-actors-diagnostics.md#keywords).

```json
"EtwEventSourceProviderConfiguration": [
                {
                    "provider": "Microsoft-ServiceFabric-Actors",
                    "scheduledTransferKeywordFilter": "1",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableActorEventTable"
                    }
                },
```

A Kusto-lekérdezési nyelve, hatékony. Egy másik értékes lekérdezés futtatása, hogy ismerje meg, melyik csomópontokon létrehozó a legtöbb esemény. A lekérdezés az alábbi képernyőképen a Service Fabric műveleti események összesíteni az adott szolgáltatás és a csomópont jeleníti meg.

![Lekérdezés események száma csomópontonként](media/service-fabric-diagnostics-event-analysis-oms/oms_kusto_query.png)

## <a name="next-steps"></a>További lépések

* Ahhoz, hogy az infrastruktúra figyelő például teljesítményszámlálók, látogasson el [hozzáadása a Log Analytics-ügynököket](service-fabric-diagnostics-oms-agent.md). Az ügynök összegyűjti a teljesítményszámlálók, és hozzáadja őket a meglévő munkaterülethez.
* A helyszíni fürtök esetén az Azure Monitor naplóira, amelyek segítségével adatokat küldeni a naplókat az Azure Monitor átjárót (http-továbbítás Proxy) nyújt. Tudjon meg többet arról, hogy a [internetelérés nélküli számítógépek csatlakoztatása a Log Analytics-átjáró használata az Azure Monitor naplóira](../azure-monitor/platform/gateway.md).
* Konfigurálása [automatizált riasztások](../log-analytics/log-analytics-alerts.md) , ezzel elősegítve az észlelési és a diagnosztikát.
* Ismerkedjen meg a [naplókeresési és lekérdezési](../log-analytics/log-analytics-log-searches.md) funkciók az Azure Monitor naplóira részeként érhető el.
* Részletesebb ismertetőt az Azure Monitor naplóira, és mit kínál, a olvasási [Mi az Azure Monitor naplóira?](../operations-management-suite/operations-management-suite-overview.md).
