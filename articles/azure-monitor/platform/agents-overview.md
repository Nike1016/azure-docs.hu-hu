---
title: Az Azure monitorozási ügynökök |} A Microsoft Docs
description: Ez a cikk részletes áttekintést nyújt az elérhető Azure-ügynökök mely támogatás Azure-ban vagy a hibrid környezetben futtatott virtuális gépek figyelése.
services: azure-monitor
documentationcenter: azure-monitor
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: magoedte
ms.openlocfilehash: 12eea032c37c8d737ae004d622b72536195c4444
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65977570"
---
# <a name="overview-of-the-azure-monitoring-agents"></a>Az Azure monitorozási ügynökök 
A Microsoft Azure több módszert is biztosít a különböző típusú adatok gyűjtésére a Microsoft Windows és az Azure, a helyi adatközpontban vagy egyéb felhőszolgáltatók üzemeltetett Linux rendszerű virtuális gépek. Három különböző típusa érhető el a virtuális gép figyelése ügynökök a következők:

* Az Azure Diagnostics bővítmény
* Log Analytics-ügynököket a Linux és Windows
* Függőségi ügynök

Ez a cikk ahhoz, hogy melyik támogatni fogja az IT service management vagy általános a figyelési követelmények meghatározása őket, és azok képességeinek közötti különbségeket mutatja be.  

## <a name="azure-diagnostic-extension"></a>Az Azure diagnosztikai bővítmény
A [Azure Diagnostics bővítmény](../../azure-monitor/platform/diagnostics-extension-overview.md) (más néven a Windows Azure diagnosztikai (WAD) vagy a Linux Azure diagnosztikai (LAD) kiterjesztéssel), amely megadva az Azure Cloud Services általánosan elérhető a 2010-vált, mivel diagnosztikai adatok egyszerű gyűjteménye biztosít a számítási Azure-erőforrások, például egy virtuális Gépet, és továbbra is fennáll az Azure storage-ügynök van. Miután a tároló úgy dönt, hogy az egyik számos elérhető eszköz, például megtekintheti [a Visual Studio Server Explorer](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage) és [Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md).

Ha szeretné, gyűjtése:

* Előre definiált operációs rendszer teljesítményszámlálók és az eseménynaplókat, vagy amelyek gyűjtésére is megadhat. 
* Összes kérelem és/vagy egy IIS-webkiszolgálón a sikertelen kérelmek
* .NET-alkalmazás nyomkövetési kimeneti naplók
* Esemény-nyomkövetés Windows (ETW)-események 
* Alkalmazásnapló-események gyűjtése a syslog  
* összeomlási memóriaképek, 

Az Azure Diagnostics-ügynök használata, ha azt szeretné, hogy:

* Archív tárolási szint, naplók és mérőszámok az Azure storage
* Monitorozási adatok integrálása a harmadik felektől származó eszközök. Ezek az eszközök különféle eszközök, például a tárfiók továbbított lekérdezés használata [az Event Hubs](../../event-hubs/event-hubs-about.md), vagy a lekérdezés a [Azure Monitoring REST API](../../azure-monitor/platform/rest-api-walkthrough.md)
* Adatfeltöltés az Azure Monitor mérőszám-diagramok létrehozása az Azure Portalon, vagy hozzon létre, közel valós idejű [metrikákhoz kapcsolódó riasztások](../../azure-monitor/platform/alerts-metric-overview.md). 
* Virtuálisgép-méretezési készletek automatikus és a klasszikus Felhőszolgáltatások vendég operációs rendszer metrikák alapján.
* Vizsgálja meg a virtuális gépek rendszerindítási problémáinak [a rendszerindítási diagnosztika](../../virtual-machines/troubleshooting/boot-diagnostics.md).
* Megismerheti, hogyan az alkalmazások hajt végre, és proaktív módon azonosítja az őket érintő problémákat [Application Insights](../../azure-monitor/overview.md).
* Metrikák importálhatja, és a Cloud Services, a klasszikus virtuális gépeket, gyűjtött adatok az Azure Monitor konfigurálása és a egy Azure storage-fiókban tárolt Service Fabric csomópontjaival.

## <a name="log-analytics-agent"></a>Log Analytics-ügynököket
Speciális monitorozás kell több mint gyűjtenek, metrikák és naplók egy részét, a Log Analytics-ügynököket (is hivatkoznak, mint a Microsoft Monitoring Agent (MMA)) Windows és Linux rendszerhez készült szükség. A Log Analytics-ügynököket kifejlesztett átfogó felügyeletét nyújtja a helyszíni fizikai és virtuális gépek, a System Center Operations Manager által felügyelt számítógépek, és más felhőkben futó virtuális gépeket. A Windows és Linux-ügynökök kapcsolódni az Azure Monitor gyűjtéséhez monitorozási megoldás-alapú adatok és is egyéni azokat az adatforrásokat, konfigurálhat egy Log Analytics-munkaterületen.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

A Log Analytics-ügynököket kell használni, ha meg szeretné:

* Többféle forrásból, mind az Azure, a más felhőszolgáltatók és a helyszíni erőforrásokhoz történő adatgyűjtést. 
* Az Azure Monitor, például a figyelési megoldások valamelyikével [-beli virtuális gépek az Azure Monitor](../insights/vminsights-overview.md), [-tárolókhoz az Azure Monitor](../insights/container-insights-overview.md)stb.  
* Használjon egy más az Azure management szolgáltatás például [az Azure Security Center](../../security-center/security-center-intro.md), [Azure Automation](../../automation/automation-intro.md)stb.

Korábban, a több Azure-szolgáltatások kötegelve is a *Operations Management Suite*, és ennek eredményeképpen a Log Analytics-ügynököket közösen használja többek között az Azure Security Center és Azure Automation szolgáltatásokat.  Ez magában foglalja a kínálnak, továbbítása az Azure-beli virtuális életciklusuk átfogó felügyeleti funkciók teljes készletét.  Néhány példa ennek a következők:

* [Az Azure Automation Update management](../../automation/automation-update-management.md) operációsrendszer-frissítések.
* [Azure Automation Desired State Configuration](../../automation/automation-dsc-overview.md) konzisztens konfiguráció-állapot karbantartásához.
* Konfigurációs változások követése a [Azure Automation Change Tracking and Inventory](../../automation/change-tracking.md).
* Azure-szolgáltatások például [Application Insights](https://docs.microsoft.com/azure/application-insights/) és [az Azure Security Center](https://docs.microsoft.com/azure/security-center/), amelyek natív módon tárolja az adatokat közvetlenül a Log Analytics.  

## <a name="dependency-agent"></a>Függőségi ügynök
A függőségi ügynök fejlesztette ki a Service Map megoldást, amely eredetileg nem célja a Microsoft részeként. [A Service Map](../insights/service-map.md) és [-beli virtuális gépek az Azure Monitor](../insights/vminsights-overview.md) használatához a függőségi ügynököt Windows és Linux rendszerű virtuális gépek és a virtuális futó folyamatok detektált adatok gyűjtése a Log Analytics-ügynök integrálható gép és a külső folyamatok függőségeit. Ez tárolja ezeket az adatokat a Log Analytics-munkaterületen, és megjeleníti a felderített összekapcsolt összetevőket.

Ezeket az ügynököket a virtuális gép figyelése valamilyen kombinációját szükség lehet. Az ügynökök is telepíthető egymás mellett az Azure-bővítmény, azonban a Linux, a Log Analytics-ügynököket *kell* telepíthető telepítési ellenkező esetben az első sikertelen lesz. 

## <a name="next-steps"></a>További lépések

- Lásd: [áttekintése a Log Analytics-ügynököket](../../azure-monitor/platform/log-analytics-agent.md) követelmények és támogatott módszerek telepítheti az ügynököt a gépek Azure-ban üzemeltetett, az adatközpontban vagy egyéb felhőalapú környezetben.

