---
title: Azure Stream Analytics feladatok állapota
description: Ez a cikk a Stream Analytics feladatok négy különböző állapotát ismerteti; futó, leállított, csökkentett teljesítményű és sikertelen.
services: stream-analytics
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: c533463ff544dc315142f7fb95c34c67933f9614
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444251"
---
# <a name="azure-stream-analytics-job-states"></a>Azure Stream Analytics feladatok állapota

Egy Stream Analytics feladatnak a négy állam egyikében lehet egy adott időpontban: futó, leállított, csökkentett teljesítményű vagy sikertelen. A feladatok állapotát a Azure Portal Stream Analytics feladatok Áttekintés oldalán találja. 

| Állapot | Leírás | Javasolt műveletek |
| --- | --- | --- |
| **Fut** | A feladatnak a megadott bemeneti forrásokból érkező, az Azure-beli olvasási eseményeken kell futnia, és fel kell dolgoznia azokat, és az eredményeket a konfigurált kimeneti mosdóba kell írni | Az ajánlott eljárás a feladatok teljesítményének nyomon követése a [fő mérőszámok](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor)figyelésével. |
| **Megállt** | A rendszer leállítja a feladatot, és nem dolgozza fel az eseményeket. | NA | 
| **Leromlott** | Előfordulhat, hogy a bemeneti és kimeneti kapcsolatokban időnként problémák léptek fel. Ezeket a hibákat átmeneti hibáknak nevezzük, amelyekkel a feladatok csökkentett teljesítményű állapotba kerülhetnek. Stream Analytics azonnal megkísérli visszaállítani az ilyen hibákat, és visszatér egy futó állapotba (néhány percen belül). Ezek a hibák hálózati problémák, más Azure-erőforrások rendelkezésre állása, deszerializálási hibák stb. miatt fordulnak elő. A feladatok teljesítményének romlása befolyásolhatja, ha a feladatnak csökkentett állapotban van.| Az átmeneti hibák okának megismeréséhez tekintse meg a [diagnosztikai vagy a tevékenység naplóit](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs#debugging-using-activity-logs) . Olyan esetekben, mint például a deszerializálási hibák, javasolt a javítási művelet végrehajtása annak biztosítása érdekében, hogy az események ne legyenek helytelenek. Ha a feladatokban továbbra is eléri az erőforrás-kihasználtsági korlátot, próbálja meg megnövelni a integrálással számát vagy a [feladatot](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization). Más esetekben, amikor nem végez semmilyen műveletet, Stream Analytics megkísérli a helyreállítást egy *futó* állapotba. <br> A küszöbértékek [késleltetésének](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor) mérőszáma segítségével megtudhatja, hogy ezek az átmeneti hibák hatással vannak-e a feladatok teljesítményére.|
| **Sikertelen** | A feladatainak kritikus hibába ütközött, ami sikertelen állapotot eredményezett. Az események nincsenek beolvasva és feldolgozva. A futásidejű hibák gyakori oka, hogy a feladatok sikertelen állapotban vannak. | A [riasztásokat konfigurálhatja](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#set-up-alerts-in-the-azure-portal) úgy, hogy értesítést kapjon, ha a feladatot sikertelen állapotba kerül. <br> <br>A hibakereséshez [tevékenység-és diagnosztikai naplókat](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs#debugging-using-activity-logs) használhat a kiváltó ok azonosításához és a probléma megoldásához.|

## <a name="next-steps"></a>További lépések
* [Riasztások beállítása Azure Stream Analytics feladatokhoz](stream-analytics-set-up-alerts.md)
* [Stream Analytics elérhető metrikák](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-monitoring#metrics-available-for-stream-analytics)
* [Hibakeresés a tevékenység-és diagnosztikai naplók használatával](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)
