---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 01/27/2019
ms.author: cynthn
ms.openlocfilehash: ac400c86af8236ff5d67b8b6fbf99f6f4b1d36c9
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67179092"
---
A virtuális gépek monitorozásához begyűjthetik, megtekintése és elemzése a diagnosztikai számos lehetőségek előnyeit, és az adatok. Ehhez egyszerűen [figyelési](../articles/azure-monitor/overview.md) a virtuális gép is használhatja az Áttekintés képernyő a virtuális gép az Azure Portalon. Használhat [bővítmények](../articles/virtual-machines/windows/extensions-features.md) diagnosztika konfigurálása további metrikai adatok gyűjtésére a virtuális gépeken. Is használhatja fejlett figyelési lehetőségek, például [Application Insights](../articles/azure-monitor/app/app-insights-overview.md) és [Log Analytics](../articles/azure-monitor/log-query/log-query-overview.md).

## <a name="diagnostics-and-metrics"></a>Diagnosztika és metrikák 

Állítsa be, és figyelheti a gyűjteménye, [diagnosztikai adatok](https://docs.microsoft.com/cli/azure/vm/diagnostics) használatával [metrikák](../articles/monitoring-and-diagnostics/monitoring-overview-metrics.md) az Azure Portalon, az Azure CLI, Azure PowerShell-lel és programozási alkalmazások programozási felületek (API). Megteheti például a következőt:

- **Tekintse át az alapvető metrikák a virtuális gép számára.** Az Azure Portal áttekintés képernyő az alapvető metrikák látható ki CPU-használat, a hálózati forgalom, a lemez bájt, és a lemez műveletek másodpercenkénti száma összesen.

- **A rendszerindítási diagnosztika az adatgyűjtés engedélyezésével, és megtekintheti azokat az Azure portal használatával.** Ha a saját rendszerképét az Azure-ban, vagy valamelyik platform rendszerképét indítja, számos oka lehet, miért lekérdezi a virtuális gép rendszerindításra képtelen állapotba lehet. Könnyen engedélyezheti a rendszerindítási diagnosztika kattintva egy virtuális gép létrehozásakor **engedélyezve** a rendszerindítási diagnosztika a figyelés szakaszban, a Beállítások képernyő.

    Virtuális gépek rendszerindítójaként a rendszerindítási diagnosztikai ügynök rögzíti a rendszerindítás kimeneti és az Azure storage-ban tárolja azokat. Ezek az adatok a virtuális gépek rendszerindítási problémáinak hibaelhárításához használhatóak. A rendszerindítási diagnosztika nem engedélyezettek automatikusan, egy virtuális gép létrehozásakor a parancssori eszközök. Ahhoz, hogy a rendszerindítási diagnosztikát engedélyezni lehessen, tárfiókot kell létrehozni a rendszerindítási naplók tárolásához. Ha engedélyezi a rendszerindítási diagnosztika az Azure Portalon, a storage-fiók automatikusan létrejön az Ön számára.

    Ha nem engedélyezte a rendszerindítási diagnosztika a virtuális gép létrehozásakor, mindig engedélyezheti azt később használatával [Azure CLI-vel](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics), [Azure PowerShell-lel](https://docs.microsoft.com/powershell/module/az.compute/set-azvmbootdiagnostic), vagy egy [Azure Resource Manager-sablon](../articles/virtual-machines/windows/extensions-diagnostics-template.md).

- **A vendég operációs rendszer diagnosztikai adatok gyűjtésének engedélyezéséhez.** Amikor létrehoz egy virtuális Gépet, lehetősége van a vendég operációs rendszer diagnosztika engedélyezése a beállítások képernyőn. Ha engedélyezte a diagnosztikai adatgyűjtés, a [IaaSDiagnostics-bővítmény linuxhoz](../articles/virtual-machines/linux/diagnostic-extension.md) vagy a [Windows IaaSDiagnostics bővítmény](../articles/virtual-machines/windows/ps-extensions-diagnostics.md) adnak hozzá a virtuális gép, amely lehetővé teszi, hogy összegyűjtse a további lemez, a Processzor és memória-adatokat.

    Gyűjtött diagnosztikai adatok használatával konfigurálhatja az automatikus skálázás a virtuális gépek számára. Beállíthatja a naplók az adatok tárolására, és értesíti, amikor a teljesítmény nem teljesen megfelelő riasztások beállítása.

## <a name="alerts"></a>Riasztások

Létrehozhat [riasztások](../articles/azure-monitor/platform/alerts-overview.md) meghatározott teljesítmény-mérőszámok alapján. A problémák, akkor is értesítést szeretne többek között az átlagos processzorhasználat meghalad egy bizonyos küszöbértéket, vagy a rendelkezésre álló szabad lemezterület egy adott érték alá csökken. Riasztások konfigurálható a [az Azure portal](../articles/azure-monitor/platform/alerts-classic-portal.md)révén [Azure PowerShell-lel](../articles/azure-monitor/platform/alerts-classic-portal.md#with-powershell), vagy a [Azure CLI-vel](../articles/azure-monitor/platform/alerts-classic-portal.md#with-azure-cli).

## <a name="azure-service-health"></a>Azure Service Health

[Az Azure Service Health](../articles/service-health/service-health-overview.md) személyre szabott útmutatást és támogatást nyújt az Azure-szolgáltatások Önt érintő problémái, és segít felkészülni a tervezett karbantartások alkalmával kerül. Az Azure Service Health figyelmezteti Önt és munkatársait célzott és rugalmas értesítések útján.

## <a name="azure-resource-health"></a>Azure Resource Health

[Az Azure Resource health](../articles/service-health/resource-health-overview.md) segít diagnosztizálni és a támogatás igénylésében, ha egy Azure-beli probléma kihat az erőforrásaira. Tájékoztatja az erőforrásai aktuális és korábbi állapotáról, és segít a problémák kezelésében. A Resource Health műszaki támogatást nyújt, ha segítségre van szüksége az Azure szolgáltatásait érintő problémákkal kapcsolatban.

## <a name="azure-activity-log"></a>Azure-tevékenységnapló

A [Azure-tevékenységnapló](../articles/azure-monitor/platform/activity-logs-overview.md) van egy előfizetési napló, amely az Azure-ban bekövetkezett események előfizetés-szintű betekintést nyújt. A napló az adatok az Azure Resource Manager frissítéseket a Service Health-események operatív adatok foglal magában. Tevékenységnapló az Azure Portalon a virtuális gép a napló megtekintése gombra.

Néhány, a tevékenységnapló is végezhet a következők:

- Hozzon létre egy [riasztási egy tevékenységnapló eseményéhez](../articles/azure-monitor/platform/activity-logs-overview.md).
- [Az Eseményközpontok felé, Stream](../articles/azure-monitor/platform/activity-logs-stream-event-hubs.md) egy külső szolgáltatás vagy az egyéni elemzési megoldással, például a Power bi támogatunk.
- A Power bi használatával elemezhetők a [Power bi-tartalomcsomag](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).
- [Mentse a tárfiókhoz](../articles/azure-monitor/platform/archive-activity-log.md) archív vagy manuális ellenőrzést. Megadhatja, hogy a megőrzési időtartam (napban) a napló-profillal.

Emellett tevékenységnapló adatainak használatával [Azure PowerShell-lel](https://docs.microsoft.com/powershell/module/azurerm.insights/), a [Azure CLI-vel](https://docs.microsoft.com/cli/azure/monitor), vagy [Monitor REST API-k](https://docs.microsoft.com/rest/api/monitor/).

[Az Azure diagnosztikai naplók](../articles/azure-monitor/platform/diagnostic-logs-overview.md) a virtuális gép által kibocsátott naplókat, a működésével kapcsolatos részletes, gyakori adatokkal szolgálnak. Diagnosztikai naplók különböznek a tevékenységnapló azáltal, hogy betekintést adnak a virtuális gépen végrehajtott műveletekbe.

A diagnosztikai naplók a lehetőségek a következők:

- [Mentse azokat egy storage-fiók](../articles/azure-monitor/platform/archive-diagnostic-logs.md) naplózási vagy manuális ellenőrzést. Megadhatja, hogy a megőrzési időtartam (napban) használata az erőforrás diagnosztikai beállításait.
- [Az Event Hubs Stream őket](../articles/azure-monitor/platform/diagnostic-logs-stream-event-hubs.md) egy külső szolgáltatás vagy az egyéni elemzési megoldással, például a Power bi támogatunk.
- Elemezheti a [Log Analytics](../articles/log-analytics/log-analytics-azure-storage.md).

## <a name="advanced-monitoring"></a>Speciális figyelés

- [Az Azure Monitor](../articles/azure-monitor/overview.md) egy szolgáltatás, amely figyeli a felhőbeli és helyszíni környezeteket a rendelkezésre állás és teljesítmény fenntartása érdekében. Ez gyűjtéséhez, elemzéséhez és a telemetriai adatokat a felhőben működő átfogó megoldást kínál, és a helyszíni környezetekben. Ez a szolgáltatás segít megérteni azt, hogy az alkalmazásai hogyan teljesítenek, valamint proaktív módon azonosítja a működésüket befolyásoló problémákat és azokat az erőforrásokat, amelyektől függenek. Egy bővítmény telepítheti egy [Linux rendszerű virtuális gép](../articles/virtual-machines/linux/extensions-oms.md) vagy egy [Windows virtuális gép](../articles/virtual-machines/windows/extensions-oms.md) , amely a naplóadatokat gyűjthet, és a Log Analytics-munkaterületen tárolja a Log Analytics-ügynököket telepíti.

    A Windows és Linux rendszerű virtuális gépek esetében az ajánlott módszer a naplók gyűjtésére szolgáló van, a Log Analytics-ügynök telepítésével. A Log Analytics-ügynök telepítése a virtuális gép legegyszerűbben az [Virtuálisgép-bővítménnyel](../articles/log-analytics/log-analytics-azure-vm-extension.md). A bővítmény használata leegyszerűsíti a telepítés folyamatát és automatikusan konfigurálja az ügynököt, hogy elküldje az adatokat a megadott Log Analytics-munkaterületre. Az ügynök automatikusan frissül, hogy mindig a legújabb funkciókkal és javításokkal bővüljön.

- [Network Watcher](../articles/network-watcher/network-watcher-monitoring-overview.md) segítségével nyomon követheti a virtuális gép és az összes kapcsolódó erőforrás valamelyikéhez kapcsolódnak a hálózathoz, azok tartoznak. A Network Watcher-ügynök bővítmény telepíthető egy [Linux rendszerű virtuális gép](../articles/virtual-machines/linux/extensions-nwa.md) vagy egy [Windows virtuális gép](../articles/virtual-machines/windows/extensions-nwa.md).

- [A virtuális gépek az Azure Monitor](../articles/azure-monitor/insights/vminsights-overview.md) ipari méretekben, elemzésével, a teljesítmény és a Windows és Linux rendszerű virtuális gépekhez, beleértve azok eltérő folyamatokat és egyéb erőforrások és a külső összekapcsolt függőségek állapotát figyeli az Azure-beli virtuális gépek (VM) dolgozza fel. 

## <a name="next-steps"></a>További lépések
- Lépésről lépésre bemutatjuk a [monitorozása az Azure PowerShell használatával Windows virtuális gép](../articles/virtual-machines/windows/tutorial-monitoring.md) vagy [monitorozása az Azure CLI-vel Linux virtuális gépek](../articles/virtual-machines/linux/tutorial-monitoring.md).
- További információ az ajánlott eljárásokat körül [Monitorozási és diagnosztikai](https://docs.microsoft.com/azure/architecture/best-practices/monitoring).
