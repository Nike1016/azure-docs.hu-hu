---
title: Irányok megjelenítése Azure Mapskal | Microsoft Docs
description: Útmutató a térképen két hely között a Azure Maps web SDK-val való megjelenítéséhez.
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: cf997d4ae120f3e9309892b112f9954bde97bc76
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976491"
---
# <a name="show-directions-from-a-to-b"></a>Útvonal megjelenítése A-ból B-be

Ebből a cikkből megtudhatja, hogyan hajthat végre egy útvonal-kérést, és hogyan jelenítheti meg az útvonalat a térképen.

Ezt kétféleképpen teheti meg. Az első módszer az [Azure Maps Route API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) lekérdezése egy szolgáltatási modulon keresztül. A második módszer az [API](https://fetch.spec.whatwg.org/) beolvasása, hogy keresési kérést végezzen a [Azure Maps Route API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)-hoz. Az alábbiakban mindkét módszert ismertetjük.

## <a name="query-the-route-via-service-module"></a>Az útvonal lekérdezése a Service Module használatával

<iframe height='500' scrolling='no' title='Irányok megjelenítése a-tól B-re egy térképen (szolgáltatási modul)' src='//codepen.io/azuremaps/embed/RBZbep/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollat a <a href='https://codepen.io/azuremaps/pen/RBZbep/'>-tól B-be a térképeken (szolgáltatási modul)</a> a<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'>CodePen</a>-on Azure Maps ().
</iframe>

A fenti kódban a kód első blokkja létrehozza a Térkép objektumot, és beállítja a hitelesítési mechanizmust a hozzáférési jogkivonat kihasználása érdekében. Ehhez útmutatást a [Térkép létrehozása](./map-create.md) című témakörben találhat.

A kód második blokkja létrehoz egy `TokenCredential` -t a hozzáférési jogkivonattal Azure Maps HTTP-kérések hitelesítéséhez. Ezután továbbítja a `TokenCredential` -t `atlas.service.MapsURL.newPipeline()` , és létrehoz egy [folyamat](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) -példányt. A `routeURL` a Azure Maps [Route](https://docs.microsoft.com/rest/api/maps/route) műveletekhez tartozó URL-címet jelöli.

A kód harmadik blokkja létrehoz egy [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) objektumot a térképhez, és hozzáadja azt.

A kód negyedik blokkja a kezdő és végponti objektumokat hozza létre, és hozzáadja őket az adatforrás-objektumhoz. [](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest)

A vonal a LineString egyik [funkciója](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) . A [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) az adatforrásban sorokként burkolt vonalakat jeleníti meg a térképen. [](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) A kód negyedik blokkja létrehoz egy sor réteget a térképhez, és hozzáadja azt. Lásd: egy sor rétegének tulajdonságai a következő helyen: [LinestringLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest).

A [szimbólum-réteg](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) szöveggel vagy ikonokkal jeleníti meg a pont-alapú adatforrásokat az adatforrásban szimbólumként a térképen. [](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) A kód ötödik blokkja egy szimbólum réteget hoz létre, és hozzáadja a térképhez.

A kód hatodik blokkja lekérdezi a Azure Maps útválasztási szolgáltatást, amely a [szolgáltatás modul](how-to-use-services-module.md)részét képezi. A RouteURL [calculateRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-iot-typescript-latest#methods) metódusa a kezdő-és végpontok közötti útvonal lekérésére szolgál. Ezután a válaszból kinyert egy GeoJSON-gyűjteményt `geojson.getFeatures()` , amely a metódussal lett kibontva, és a rendszer hozzáadja az adatforráshoz. Ezután a választ útvonalként jeleníti meg a térképen. További információ a vonal a térképhez való hozzáadásáról: [vonal hozzáadása a térképhez](map-add-line-layer.md).

A kód utolsó blokkja a Térkép [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) tulajdonságával állítja be a leképezés határait.

Az útvonal-lekérdezés, az adatforrás, a szimbólum és a vonal rétegek, valamint a kamera határai a Térkép [esemény](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) -figyelőn belül jönnek létre és állíthatók be, így biztosítható, hogy az eredmények a Térkép teljes betöltése után megjelenjenek.

## <a name="query-the-route-via-fetch-api"></a>Az útvonal lekérdezése a fetch API használatával

<iframe height='500' scrolling='no' title='Az A és B közötti útvonalak megjelenítése térképen' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollat a (z) Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) alapján a <a href='https://codepen.io'>CodePen</a>-on található, a <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>-tól B-ig mutató utasításokat</a> .
</iframe>

A fenti kódban a kód első blokkja létrehozza a Térkép objektumot, és beállítja a hitelesítési mechanizmust a hozzáférési jogkivonat kihasználása érdekében. Ehhez útmutatást a [Térkép létrehozása](./map-create.md) című témakörben találhat.

A kód második blokkja létrehoz és hozzáadja a térképhez egy [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) objektumot.

A harmadik kódrészlet létrehozza a kezdő és a célhelyet az útvonalhoz, és hozzáadja azokat az adatforráshoz. A [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)használatával kapcsolatos utasításokért tekintse [meg a térképen a PIN-kód hozzáadása](map-add-pin.md) című témakört.

A [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) az adatforrásban sorokként burkolt vonalakat jeleníti meg a térképen. [](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) A kód negyedik blokkja létrehoz egy sor réteget a térképhez, és hozzáadja azt. Lásd: egy sor rétegének tulajdonságai a következő helyen: [LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest).

A [szimbólum-réteg](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) szöveggel vagy ikonokkal jeleníti meg a pont-alapú adatforrásokat az adatforrásban szimbólumként a térképen. [](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) A kód ötödik blokkja egy szimbólum réteget hoz létre, és hozzáadja a térképhez. Tekintse meg a szimbólumok rétegének tulajdonságait a következő helyen: [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest).

A következő kódrészlet létrehozza `SouthWest` és `NorthEast` rámutat a kezdő és a célhelyről, és beállítja a Térkép határait a Térkép [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) tulajdonságának használatával.

A kód utolsó blokkja a beolvasás [API](https://fetch.spec.whatwg.org/) -t használja a [Azure Maps Route API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)-ra irányuló keresési kérések elvégzéséhez. A rendszer elemzi a választ. Ha a válasz sikeres volt, a szélességi és a hosszúsági adatokat a rendszer egy sor létrehozásához használja a pontok összekapcsolásával. Ekkor a rendszer hozzáadja az adatforráshoz a sor-adatforrást, hogy az útvonalat a térképen jelenítse meg. Útmutatásért lásd: [vonal hozzáadása a térképen](map-add-line-layer.md) .

Az útvonal-lekérdezés, az adatforrás, a szimbólum és a vonal rétegek, valamint a kamera határai a Térkép [esemény](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) -figyelőn belül jönnek létre és állíthatók be, így biztosítható, hogy az eredmények a Térkép teljes betöltése után megjelenjenek.

## <a name="next-steps"></a>További lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [Térkép](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

A következő cikkekben talál részletes példákat:

> [!div class="nextstepaction"]
> [Forgalom megjelenítése a térképen](./map-show-traffic.md)

> [!div class="nextstepaction"]
> [Interakció a Térkép-egér eseményeivel](./map-events.md)
