---
title: A hírcsatorna-feldolgozó processzor indítási idejének konfigurálása – Azure Cosmos DB
description: Megtudhatja, hogyan konfigurálhatja a módosítási hírcsatorna processzorát egy adott dátummal és időponttal való olvasás megkezdéséhez
author: ealsur
ms.service: cosmos-db
ms.topic: sample
ms.date: 08/13/2019
ms.author: maquaran
ms.openlocfilehash: 7db14f6406223486ea0efb80d3d1cfcd0351f414
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69037245"
---
# <a name="how-to-configure-the-change-feed-processor-start-time"></a>A hírcsatorna-processzor módosításának kezdési idejének konfigurálása

Ez a cikk azt ismerteti, hogyan konfigurálhatja a [változási hírcsatorna processzorát](./change-feed-processor.md) , hogy egy adott dátummal és időponttal kezdjen el olvasni.

## <a name="default-behavior"></a>Alapértelmezett viselkedés

Amikor az első alkalommal elindul a Change feed-processzor, a megkezdi a bérletek tárolójának inicializálását, és elindítja a [feldolgozási életciklusát](./change-feed-processor.md#processing-life-cycle). A rendszer nem észleli a tárolóban a változási csatorna processzorának első inicializálásakor történt módosításokat.

## <a name="reading-from-a-previous-date-and-time"></a>Olvasás egy korábbi dátumból és időpontból

A módosítási hírcsatorna processzora inicializálható úgy, hogy egy **adott dátummal és**időponttal kezdődő módosításokat olvasson be, az a `DateTime` példányának `WithStartTime` átadásával a Builder bővítménnyel:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=TimeInitialization)]

A módosítási hírcsatorna processzora az adott dátumra és időpontra lesz inicializálva, és megkezdi az azt követő módosítások olvasását.

## <a name="reading-from-the-beginning"></a>Olvasás az elejétől

Más forgatókönyvekben, például az adatáttelepítés során vagy egy tároló teljes előzményeinek elemzéséhez a **tároló élettartamának elejéről**kell olvasni a változási csatornát. Ehhez használhatja `WithStartTime` a Builder bővítményt, de a továbbítást `DateTime.MinValue.ToUniversalTime()`, amely a minimális `DateTime` érték UTC szerinti megjelenítését eredményezi, például:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartFromBeginningInitialization)]

A módosítási hírcsatorna processzora inicializálva lesz, és megkezdi a módosítások olvasását a tároló élettartamának elejétől kezdve.

> [!NOTE]
> Ezek a testreszabási beállítások csak akkor működnek, ha beállítja a változási csatorna processzorának kiindulási időpontját. Ha a bérletek tárolója első alkalommal lett inicializálva, a módosításnak nincs hatása.

> [!NOTE]
> Ha egy adott időpontot ad meg, csak a tárolóban aktuálisan létező elemek módosításai lesznek beolvasva. Ha egy elem törölve lett, a változási csatornán lévő előzmények is el lettek távolítva.

## <a name="additional-resources"></a>További források

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [Használati minták a GitHubon](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/usage/changefeed)
* [További minták a GitHubon](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>További lépések

Folytassa további információ a változáscsatorna az alábbi cikkeket:

* [A hírcsatorna változásának áttekintése](change-feed.md)
* [A módosítási csatorna olvasási módjai](read-change-feed.md)
* [A csatorna módosítása](change-feed-processor.md)
