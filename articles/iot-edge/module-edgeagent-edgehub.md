---
title: EdgeAgent és EdgeHub kívánt tulajdonságok referencia – Azure IoT Edge |} A Microsoft Docs
description: Tekintse át az adott tulajdonságok és azok értékei a edgeAgent és edgeHub ikermodulokkal
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: e8a8170023c8f529894522e27a4c6231325089af
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190980"
---
# <a name="properties-of-the-iot-edge-agent-and-iot-edge-hub-module-twins"></a>Az IoT Edge-ügynök és az IoT Edge hubot ikermodulokkal tulajdonságai

Az IoT Edge-ügynök és az IoT Edge hubot az IoT Edge-futtatókörnyezet alkotó két modult. Minden modul hajt végre milyen feladatokat kapcsolatos további információkért lásd: [megismerheti az Azure IoT Edge-futtatókörnyezet és az architektúrára](iot-edge-runtime.md). 

Ez a cikk a kívánt tulajdonságok és a futtatókörnyezet ikermodulokkal jelentett tulajdonságait. A modulok IoT Edge-eszközökön üzembe helyezése további információkért lásd: [megtudhatja, hogyan helyezhet üzembe modulokat, és ezekkel létesíthetnek útvonalat az IoT Edge](module-composition.md).

Egy ikermodul tartalmazza: 

* **Kívánt tulajdonságok**. A megoldás háttérrendszere beállíthatja a kívánt tulajdonságait, és a modul is olvashatja őket. A modul is fogadhat értesítéseket a módosításokat a kívánt tulajdonságait. Kívánt tulajdonságok használhatók együtt jelentett tulajdonságok szinkronizálása a modulkonfiguráció vagy feltételeket.

* **Jelentett tulajdonságokként**. A modul be, a jelentett tulajdonságokat, és olvashatja és lekérdezheti, ha a megoldás háttérrendszeréhez. Jelentett tulajdonságok és kívánt tulajdonságok szinkronizálása a modulkonfiguráció vagy feltételeket szolgálnak. 

## <a name="edgeagent-desired-properties"></a>EdgeAgent desired tulajdonságai

Az IoT Edge-ügynök ikermodulja nevezzük `$edgeAgent` és a egy eszközön, és az IoT Hub futó IoT Edge-ügynök közötti kommunikáció koordinálását. A kívánt tulajdonságok vannak beállítva, a manifest nasazení alkalmazása az adott eszközön egy egyetlen eszközök vagy ipari méretekben központi telepítésének részeként esetén. 

| Tulajdonság | Leírás | Szükséges |
| -------- | ----------- | -------- |
| sémaverzióval | "1.0" kell lennie | Igen |
| Runtime.Type | Kell lennie a "docker" | Igen |
| runtime.settings.minDockerVersion | Állítsa be a manifest nasazení által megkövetelt minimális Docker verzióra | Igen |
| runtime.settings.loggingOptions | Az IoT Edge-ügynök tároló a naplózási beállításokat tartalmazó sztringesített JSON. [Docker naplózási lehetőségek](https://docs.docker.com/engine/admin/logging/overview/) | Nem |
| runtime.settings.registryCredentials<br>. {registryId} .username | A tároló-beállításjegyzék felhasználóneve. Az Azure Container Registry esetében a felhasználónév általában a beállításjegyzék nevét.<br><br> A tárolójegyzék hitelesítő adatainak szükségesek, amelyek nem nyilvános modul képeket. | Nem |
| runtime.settings.registryCredentials<br>. {registryId} .password | A tárolóregisztrációs adatbázis jelszava. | Nem |
| runtime.settings.registryCredentials<br>. {registryId} .address | A tárolóregisztrációs adatbázis címe. Az Azure Container Registry esetében a címe általában *{beállításjegyzék neve}.azurecr.IO/Azure-vote-Front:V1*. | Nem |  
| systemModules.edgeAgent.type | Kell lennie a "docker" | Igen |
| systemModules.edgeAgent.settings.image | Az IoT Edge-ügynök a kép URI azonosítója. Jelenleg nem sikerül frissíteni saját magát az IoT Edge-ügynök. | Igen |
| systemModules.edgeAgent.settings<br>.createOptions | Az IoT Edge-ügynök tároló létrehozását, a beállításokat tartalmazó sztringesített JSON. [Docker-létrehozási beállítások](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Nem |
| systemModules.edgeAgent.configuration.id | Az üzemelő példány, amelyre ez a modul telepítve azonosítója. | Az IoT Hub állítja ezt a tulajdonságot, alkalmazásakor a jegyzékfájl egy üzemelő példány használatával. Nem része egy manifest nasazení. |
| systemModules.edgeHub.type | Kell lennie a "docker" | Igen |
| systemModules.edgeHub.status | "Futnia kell" | Igen |
| systemModules.edgeHub.restartPolicy | Kell lennie a "mindig" | Igen |
| systemModules.edgeHub.settings.image | Az URI-ját az IoT Edge hubot képe. | Igen |
| systemModules.edgeHub.settings<br>.createOptions | Az IoT Edge hubot tároló létrehozását, a beállításokat tartalmazó sztringesített JSON. [Docker-létrehozási beállítások](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Nem |
| systemModules.edgeHub.configuration.id | Az üzemelő példány, amelyre ez a modul telepítve azonosítója. | Az IoT Hub állítja ezt a tulajdonságot, alkalmazásakor a jegyzékfájl egy üzemelő példány használatával. Nem része egy manifest nasazení. |
| modules.{moduleId}.version | Egy felhasználó által megadott karakterlánc, amely a modul verzióját. | Igen |
| modulok. {moduleId} .type | Kell lennie a "docker" | Igen |
| modulok. {moduleId} .status | {"fut" \| "leállított"} | Igen |
| modulok. {moduleId} .restartPolicy | {"soha" \| "hiba" \| "a – nem megfelelő állapotú" \| "mindig"} | Igen |
| modules.{moduleId}.settings.image | A modul kép URI Azonosítóját. | Igen |
| modules.{moduleId}.settings.createOptions | A modul tároló létrehozását, a beállításokat tartalmazó sztringesített JSON. [Docker-létrehozási beállítások](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Nem |
| modules.{moduleId}.configuration.id | Az üzemelő példány, amelyre ez a modul telepítve azonosítója. | Az IoT Hub állítja ezt a tulajdonságot, alkalmazásakor a jegyzékfájl egy üzemelő példány használatával. Nem része egy manifest nasazení. |

## <a name="edgeagent-reported-properties"></a>EdgeAgent jelentett tulajdonságok

Az IoT Edge-ügynök jelentett tulajdonságok három fő információt tartalmaznak:

1. Utolsó észlelés kívánt tulajdonságok; az alkalmazás állapota
2. A modulok jelenleg fut az eszközön, az IoT Edge-ügynök; által jelentett módon állapota és
3. Jelenleg fut az eszköz kívánt tulajdonságait másolatát.

Ezen információk, az aktuális kívánt tulajdonságait másolatát utolsó részének hasznos állapítható meg, hogy az eszköz rendelkezik a alkalmazni a legújabb kívánt tulajdonságok, vagy még fut egy előző manifest nasazení.

> [!NOTE]
> Az IoT Edge-ügynök a jelentett tulajdonságok hasznosak lehetnek, a lekérdezhetők a [IoT Hub lekérdezési nyelv](../iot-hub/iot-hub-devguide-query-language.md) vizsgálhatja a nagy mennyiségű központi telepítések állapotát. Az IoT Edge-ügynök tulajdonságainak használata az állapot további információkért lásd: [ismertetése IoT Edge-telepítések egyetlen eszközök vagy ipari méretekben](module-deployment-monitoring.md).

A következő táblázat nem tartalmazza a kívánt tulajdonságok, másolja az információkat.

| Tulajdonság | Leírás |
| -------- | ----------- |
| lastDesiredVersion | Egész szám lehet a kívánt tulajdonságokkal dolgozza fel az IoT Edge-ügynök verziója az utolsó hivatkozik. |
| lastDesiredStatus.code | Ezzel az állapotkóddal hivatkozik az IoT Edge-ügynök által látott utolsó kívánt tulajdonságait. Megengedett értékek: `200` Sikeres, `400` érvénytelen konfigurációt `412` érvénytelen verziójú, `417` a kívánt tulajdonságokkal is üres, `500` sikertelen |
| lastDesiredStatus.description | Az állapot leírása |
| deviceHealth | `healthy` Ha az összes modul futásidejű állapotát `running` vagy `stopped`, `unhealthy` egyéb |
| configurationHealth.{deploymentId}.health | `healthy` Ha a központi telepítés {deploymentId} által beállított összes modul futási állapotát `running` vagy `stopped`, `unhealthy` egyéb |
| runtime.platform.OS | Jelentéskészítés az eszközön futó operációs rendszer |
| Runtime.platform.Architecture | Az architektúra a processzor Reporting az eszközön |
| systemModules.edgeAgent.runtimeStatus | IoT Edge-ügynök a jelzett állapot: {"fut" \| "nem kifogástalan"} |
| systemModules.edgeAgent.statusDescription | Az IoT Edge-ügynök a jelzett állapot szöveges leírása. |
| systemModules.edgeHub.runtimeStatus | IoT Edge hubot állapota: {"fut" \| "leállított" \| "sikertelen" \| "leállítási" \| "nem kifogástalan"} |
| systemModules.edgeHub.statusDescription | Az IoT Edge hub, ha a nem kifogástalan állapotát szöveges leírása. |
| systemModules.edgeHub.exitCode | Az IoT Edge hubot tároló által jelentett a kilépési kód kilép, ha a tároló |
| systemModules.edgeHub.startTimeUtc | Time when IoT Edge hub was last started |
| systemModules.edgeHub.lastExitTimeUtc | Time when IoT Edge hub last exited |
| systemModules.edgeHub.lastRestartTimeUtc | Time when IoT Edge hub was last restarted |
| systemModules.edgeHub.restartCount | Ez a modul újra lett indítva, az újraindítási házirend részeként hányszor. |
| modules.{moduleId}.runtimeStatus | A modul állapota: {"fut" \| "leállított" \| "sikertelen" \| "leállítási" \| "nem kifogástalan"} |
| modules.{moduleId}.statusDescription | Szöveges leírása a modult, ha a nem kifogástalan állapotát. |
| modulok. {moduleId} .exitCode | A modul tároló által jelentett a kilépési kód kilép, ha a tároló |
| modules.{moduleId}.startTimeUtc | Ha a modul legutóbbi Indítás ideje |
| modules.{moduleId}.lastExitTimeUtc | Ha a modul utolsó kilépett idő |
| modules.{moduleId}.lastRestartTimeUtc | Ha a modul utolsó újraindítása idő |
| modules.{moduleId}.restartCount | Ez a modul újra lett indítva, az újraindítási házirend részeként hányszor. |

## <a name="edgehub-desired-properties"></a>EdgeHub desired tulajdonságai

Az IoT Edge hub az ikermodul nevezzük `$edgeHub` és koordinálja a kommunikációt az IoT Edge hub fut egy eszközön, és az IoT Hub között. A kívánt tulajdonságok vannak beállítva, a manifest nasazení alkalmazása az adott eszközön egy egyetlen eszközök vagy ipari méretekben központi telepítésének részeként esetén. 

| Tulajdonság | Leírás | Manifest nasazení szükséges |
| -------- | ----------- | -------- |
| sémaverzióval | "1.0" kell lennie | Igen |
| útvonalak. {routeName} | A string representing an IoT Edge hub route. További információkért lásd: [útvonalak deklarálja](module-composition.md#declare-routes). | A `routes` elem létezik, de üres is lehet. |
| storeAndForwardConfiguration.timeToLiveSecs | Az időtartam másodpercben, hogy az IoT Edge hub tartja Ha bontotta a útválasztási végpontok, hogy üzeneteket az IoT Hub és a egy helyi modul. Az érték lehet bármilyen pozitív egész szám lehet. | Igen |

## <a name="edgehub-reported-properties"></a>EdgeHub jelentett tulajdonságok

| Tulajdonság | Leírás |
| -------- | ----------- |
| lastDesiredVersion | Egész szám lehet a kívánt tulajdonságok, az IoT Edge hub által feldolgozott verzióját az utolsó hivatkozik. |
| lastDesiredStatus.code | Az IoT Edge-központ által látott utolsó kívánt tulajdonságok hivatkozó állapotkód. Megengedett értékek: `200` Sikeres, `400` érvénytelen konfigurációt `500` sikertelen |
| lastDesiredStatus.description | Az állapot szöveges leírása. |
| ügyfelek számára. {eszköz vagy moduleId} .status | A kapcsolat állapota az eszköz vagy a modul. Lehetséges értékei {"csatlakoztatva" \| "leválasztott"}. Csak a modul identitások leválasztott állapotban is lehetnek. Alsóbb rétegbeli eszközök csatlakoztatása az IoT Edge hubot jelennek meg, csak a csatlakozáskor. |
| ügyfelek számára. {eszköz vagy moduleId} .lastConnectTime | Utolsó időpont, amikor az eszköz vagy a csatlakoztatott. |
| ügyfelek számára. {eszköz vagy moduleId} .lastDisconnectTime | Utolsó időpont, amikor az eszköz vagy leválasztott modul. |

## <a name="next-steps"></a>További lépések

Ezek a tulajdonságok használatával hozhatja létre a központi telepítési jegyzékek kapcsolatban lásd: [megismerheti, hogyan IoT Edge-modulok használják, konfigurálhatók, és újra felhasználható](module-composition.md).
