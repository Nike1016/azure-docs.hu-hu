---
ms.openlocfilehash: 52dfbfca5f79a7f92848ea39eddc00aa10f05ff1
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178835"
---
## <a name="locate-a-cloud-spatial-anchor"></a>Keresse meg a felhő térbeli forráshorgony

Keresse meg a térbeli korábban feltöltött felhőbeli horgony magától egyik az elsődleges oka az Azure térbeli horgonyok kódtár használatával. Térbeli felhőbeli horgonyok megkereséséhez kell ismernie a azonosítókról. Jegyzetobjektum azonosítók is tárolhatók, az alkalmazás háttér-szolgáltatásban, és elérhető-e az összes eszközre, hogy megfelelően hitelesíteni tudják magukat. Lásd a példát [oktatóanyag: Térbeli horgonyok megosztása eszközök](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/).

Hozza létre egy `AnchorLocateCriteria` objektumazonosító, állítsa be az azonosítók keres, és hívja a `CreateWatcher` metódust a munkamenet megadásával a `AnchorLocateCriteria`.
