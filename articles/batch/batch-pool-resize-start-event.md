---
title: Azure Batch készlet átméretezése indítási esemény | Microsoft Docs
description: A Batch-készlet átméretezési indítási eseményének hivatkozása.
services: batch
author: laurenhughes
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: c7ef6bb9550b7398a10f5b57e6009d896256666b
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68323179"
---
# <a name="pool-resize-start-event"></a>Készlet átméretezésének indítása esemény

 Ezt az eseményt a rendszer a készlet átméretezésének megkezdése után bocsátja ki. Mivel a készlet átméretezése egy aszinkron esemény, várható, hogy a készlet átméretezése az átméretezési művelet befejezését követően kivezethető teljes eseményt.

 A következő példa egy készlet átméretezési indítási eseményének törzsét mutatja be, ha egy készlet 0 és 2 csomópont között átméretezi a manuális átméretezést.

```
{
    "poolId": "myPool1",
    "nodeDeallocationOption": "invalid",
    "currentDedicated": 0,
    "targetDedicated": 2,
    "enableAutoScale": false,
    "isAutoPool": false
}
```

|Elem|Type|Megjegyzések|
|-------------|----------|-----------|
|poolId|Karakterlánc|A készlet azonosítója.|
|nodeDeallocationOption|Sztring|Megadja, hogy a rendszer mikor távolítsa el a csomópontokat a készletből, ha a készlet mérete csökken.<br /><br /> Lehetséges értékek a következők:<br /><br /> **újravárólista** – leállítja a futó feladatokat, és újravárólistára helyezi őket. A feladatok akkor futnak újra, amikor a feladat engedélyezve van. A csomópontokat a feladatok leállítása után távolítsa el.<br /><br /> **megszakítás** – futó feladatok leállítása. A feladatok nem futnak újra. A csomópontokat a feladatok leállítása után távolítsa el.<br /><br /> **taskcompletion** – a jelenleg futó feladatok befejezésének engedélyezése. A várakozás közben nem ütemezhet új feladatokat. Csomópontok eltávolítása, ha az összes feladat befejeződött.<br /><br /> **Retaineddata** – lehetővé teszi a jelenleg futó feladatok befejezését, majd várjon, amíg az összes feladat adatmegőrzési időszaka lejár. A várakozás közben nem ütemezhet új feladatokat. Csomópontok eltávolítása, ha az összes tevékenység megőrzési időszaka lejárt.<br /><br /> Az alapértelmezett érték az újraüzenetsor.<br /><br /> Ha a készlet mérete növekszik, az érték érvénytelenre van állítva .|
|currentDedicated|Int32|A készlethez jelenleg hozzárendelt számítási csomópontok száma.|
|targetDedicated|Int32|A készlethez igényelt számítási csomópontok száma.|
|enableAutoScale|Bool|Meghatározza, hogy a készlet mérete automatikusan igazodik-e az idő múlásával.|
|isAutoPool|Bool|Azt határozza meg, hogy a készlet a feladatok autopool mechanizmusán keresztül lett-e létrehozva.|
