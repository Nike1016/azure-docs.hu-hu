---
title: Az Azure Cosmos DBt használó alkalmazás megfelelő konzisztencia-szintjének kiválasztása
description: Az alkalmazás megfelelő konzisztencia-szintjének kiválasztása Azure Cosmos DBban.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.reviewer: sngun
ms.openlocfilehash: 89c81e978c5f3dbbb8fac1ea5e75fc506612308f
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/22/2019
ms.locfileid: "68384906"
---
# <a name="choose-the-right-consistency-level"></a>A megfelelő konzisztenciaszint kiválasztása 

A magas rendelkezésre állásra, kis késésre vagy mindkettőre támaszkodó elosztott adatbázisok esetében az olvasási konzisztencia és a rendelkezésre állás, a késés és az átviteli sebesség közötti alapvető kompromisszumot kell biztosítani. A legtöbb kereskedelmi forgalomban kapható elosztott adatbázis arra kéri a fejlesztőket, hogy válasszon a két szélsőséges konzisztencia-modell közül: *erős* konzisztencia és *végleges* konzisztencia. Azure Cosmos DB lehetővé teszi a fejlesztők számára, hogy az öt jól meghatározott konzisztencia-modell közül választhatnak: *erős*, *kötött*elavulás, *munkamenet*, *konzisztens előtag* és *végleges*. Mindegyik konzisztencia-modell jól definiált, intuitív, és adott valós forgatókönyvekhez használható. Az öt konzisztencia-modell pontos [rendelkezésre állást és teljesítménybeli kompromisszumokat](consistency-levels-tradeoffs.md) biztosít, és átfogó SLA-kat támogat. A következő egyszerű megfontolások segítenek a megfelelő választásban számos gyakori helyzetben.

## <a name="sql-api-and-table-api"></a>SQL API és Table API

Ha az alkalmazás SQL API vagy Table API használatával készült, vegye figyelembe a következő szempontokat:

- Számos valós forgatókönyv esetén a munkamenetek konzisztenciája optimális, és ez az ajánlott lehetőség. További információt az [alkalmazás munkamenet-jogkivonatának kezelése](how-to-manage-consistency.md#utilize-session-tokens)című témakörben talál.

- Ha az alkalmazáshoz erős konzisztencia szükséges, javasoljuk, hogy a megkötött elavult konzisztencia-szintet használja.

- Ha szigorúbb konzisztencia-garanciára van szüksége, mint amennyit a munkamenet konzisztenciája és az írásokhoz tartozó egyszámjegyű ezredmásodperces késések is igényelnek, akkor javasoljuk, hogy a megadott elavult konzisztencia-szintet használja.  

- Ha az alkalmazása végleges konzisztenciát igényel, akkor javasoljuk, hogy konzisztens előtag-konzisztencia-szintet használjon.

- Ha kevésbé szigorú konzisztencia-garanciára van szüksége, mint amennyit a munkamenet konzisztenciája is biztosít, javasoljuk, hogy konzisztens előtag-konzisztencia-szintet használjon.

- Ha a legmagasabb rendelkezésre állásra és a legalacsonyabb késésre van szüksége, használja a végleges konzisztencia-szintet.

- Ha még nagyobb adattartósságra van szüksége a teljesítmény feláldozása nélkül, létrehozhat egyéni konzisztencia-szintet az alkalmazási rétegben. További információ: [útmutató egyéni szinkronizálás megvalósításához](how-to-custom-synchronization.md)az alkalmazásokban.

## <a name="cassandra-mongodb-and-gremlin-apis"></a>Cassandra, MongoDB és Gremlin API-k

- Az Apache Cassandra és a Cosmos DB konzisztencia-szintjei között elérhető "konzisztencia-kezelési szint" leképezése közötti megfeleltetés részleteiért lásd: [konzisztencia-szintek és Cosmos db API](consistency-levels-across-apis.md#cassandra-mapping)-k.

- A MongoDB és a Azure Cosmos DB konzisztenciai szintjeinek "olvasási aggálya" közötti megfeleltetésével kapcsolatos részletekért lásd: a [konzisztencia szintjei és a Cosmos db API](consistency-levels-across-apis.md#mongo-mapping)-k.

## <a name="consistency-guarantees-in-practice"></a>Konzisztencia-garanciák a gyakorlatban

A gyakorlatban gyakran nagyobb konzisztencia-garanciákat érhet el. Az olvasási művelet konzisztencia-garanciái megfelelnek a kért adatbázis-állapot frissességének és rendezésének. Az olvasás-konzisztencia az írási/frissítési műveletek sorrendjéhez és propagálásához van kötve.  

* Ha a konzisztencia szintje **határos elévülésre**van beállítva, Cosmos db garantálja, hogy az ügyfelek mindig egy korábbi írás értékének beolvasását írják le, és az elavultság ablakában lévő késéssel rendelkeznek.

* Ha a konzisztencia szintje **erős**értékre van állítva, az elavultság ablak értéke nulla, és az ügyfelek garantáltan elolvashatják az írási művelet legutóbb véglegesített értékét.

* A fennmaradó három konzisztenciai szint esetében az elavultság ablak nagy mértékben függ a számítási feladatoktól. Ha például nincs írási művelet az adatbázison, akkor a rendszer a **végleges**, a munkamenetet vagy a **konzisztens előtag** -konzisztencia-szintet tartalmazó olvasási műveletet valószínűleg ugyanazt az eredményt fogja eredményezni, mint egy erős konzisztencia-szintű olvasási művelet.

Ha az Azure Cosmos-fiókja nem az erős konzisztencia mellett van konfigurálva, akkor megtudhatja, hogy az ügyfelek erős és konzisztens olvasási feladatokat szereznek a számítási feladatokhoz a Probabilistically határos megkeresésével. Az elavultság (PBS) mérőszáma. Ez a mérőszám a Azure Portalban érhető el. További információért lásd: a [Probabilistically kötött elévülés (PBS) mérőszámának figyelése](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric).

A valószínűséggel határos elavulás azt mutatja, hogy a végleges konzisztencia milyen módon lehetséges. Ez a mérőszám betekintést nyújt abba, hogy milyen gyakran lehet erősebb konzisztencia, mint az Azure Cosmos-fiókjában jelenleg konfigurált konzisztencia-szint. Más szóval az írási és olvasási régiók kombinációjának nagy mértékben konzisztens olvasásának valószínűsége (ezredmásodpercben mérve) látható.

## <a name="next-steps"></a>További lépések

További információk a konzisztencia-szintekről a következő cikkekben olvashatók:

* [Konzisztencia-szint megfeleltetése Cosmos DB API-k között](consistency-levels-across-apis.md)
* [Kompromisszumot kínál a különböző konzisztenciaszintet rendelkezésre állás és teljesítmény](consistency-levels-tradeoffs.md)
* [Az alkalmazás munkamenet-jogkivonatának kezelése](how-to-manage-consistency.md#utilize-session-tokens)
* [Probabilistically-határos elavultság (PBS) metrikájának figyelése](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric)
