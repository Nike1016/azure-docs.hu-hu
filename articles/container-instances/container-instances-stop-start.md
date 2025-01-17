---
title: Tárolók manuális leállítása vagy elindítása Azure Container Instances
description: Megtudhatja, hogyan állíthatja be manuálisan a tárolók csoportját a Azure Container Instancesban.
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 04/15/2019
ms.author: danlep
ms.openlocfilehash: c7d46ad8d935e28b5a24e48c85ac2464b55b2669
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325644"
---
# <a name="manually-stop-or-start-containers-in-azure-container-instances"></a>Tárolók manuális leállítása vagy elindítása Azure Container Instances

A tároló-csoport újraindítási [házirend](container-instances-restart-policy.md) beállítása határozza meg, hogy a tároló példányai alapértelmezés szerint hogyan kezdődnek vagy álljanak le. Az alapértelmezett beállítás felülbírálható egy tároló csoport manuális leállításával vagy indításával.

## <a name="stop"></a>Leállítás

Állítson le manuálisan egy futó tároló csoportot – például az az [Container stop][az-container-stop] paranccsal vagy a Azure Portal használatával. Bizonyos tároló-munkaterhelések esetében előfordulhat, hogy egy hosszú ideig futó tároló-csoportot kell leállítania a költségek megtakarítása érdekében meghatározott időszak után. 

*Ha egy tároló csoport leállított állapotba kerül, a leállítja és újrahasznosítja a csoportban lévő összes tárolót. Nem őrzi meg a tároló állapotát.*

A tárolók újrahasznosítása esetén az [erőforrások](container-instances-container-groups.md#resource-allocation) fel lesznek terhelve, és a tároló csoport számlázása leáll.

A Leállítás műveletnek nincs hatása, ha a tároló csoport már le van állítva (vagy sikeres vagy sikertelen állapotban van). Például egy olyan tároló-csoport, amelyen a Run-Once tároló feladatainak futtatása sikeres volt, sikeresen leállt a sikeres állapotba. Az adott állapotban lévő csoport leállítására tett kísérletek nem változtatják meg az állapotot. 

## <a name="start"></a>Start

Ha egy tároló csoport leáll – vagy azért, mert a tárolók le vannak zárva, vagy manuálisan leállította a csoportot –, elindíthatja a tárolókat. Például az az [Container Start][az-container-start] paranccsal vagy Azure Portal használatával manuálisan indíthatja el a csoportba tartozó tárolókat. Ha egy tárolóhoz tartozó tároló képe frissül, egy új rendszerképet kell meghúzni. 

A tároló-csoport elindítása egy új központi telepítést indít el ugyanazzal a tároló-konfigurációval. Ezzel a művelettel gyorsan újra felhasználhatja a várt módon működő, ismert tárolók csoportjainak konfigurációját. Nem kell létrehoznia új tároló csoportot, hogy ugyanazt a számítási feladatot futtassa.

Ez a művelet elindítja a tároló csoport összes tárolóját. A csoportban nem indítható el egy adott tároló.

A tároló csoport manuális elindítása vagy újraindítása után a tároló csoport a beállított újraindítási szabályzatnak megfelelően fut.
  
## <a name="restart"></a>Újraindítás

A tárolók csoportjának újraindításához futtathatja a rendszert, például az az [Container restart][az-container-restart] parancs használatával. Ez a művelet újraindítja a tároló csoportban lévő összes tárolót. Ha egy tárolóhoz tartozó tároló képe frissül, egy új rendszerképet kell meghúzni. 

A tároló-csoport újraindítása akkor hasznos, ha az üzembe helyezéssel kapcsolatos problémát szeretné elhárítani. Ha például egy ideiglenes erőforrás-korlátozás megakadályozza, hogy a tárolók sikeresen fussanak, a csoport újraindítása megoldhatja a problémát.

Ez a művelet újraindítja a tárolók csoportjának összes tárolóját. A csoportban lévő adott tároló nem indítható újra.

A tároló csoport manuális újraindítása után a tároló csoport a beállított újraindítási szabályzatnak megfelelően fut.

## <a name="next-steps"></a>További lépések

További információ a Azure Container Instances újraindítási [házirend](container-instances-restart-policy.md) -beállításairól.

A meglévő konfigurációval rendelkező tárolók manuális leállítása és elindítása mellett a futó tárolók csoportjának [beállításait](container-instances-update.md) is frissítheti.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-restart]: /cli/azure/container?view=azure-cli-latest#az-container-restart
[az-container-start]: /cli/azure/container?view=azure-cli-latest#az-container-start
[az-container-stop]: /cli/azure/container?view=azure-cli-latest#az-container-stop
