---
title: Deklarálja a modulok és útvonalak az üzembe helyezés jegyzékek – Azure IoT Edge |} A Microsoft Docs
description: Ismerje meg, hogyan egy manifest nasazení kijelenti, mely modulok üzembe helyezéséhez, hogyan kell őket telepíteni, és közöttük üzenet útvonalak létrehozása.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 05/28/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: f275cca664733f19d3f3c5b52d168ffad01cadad
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68839609"
---
# <a name="learn-how-to-deploy-modules-and-establish-routes-in-iot-edge"></a>Ismerje meg, hogyan helyezhet üzembe modulokat, és ezekkel létesíthetnek útvonalat IoT Edge-ben

Minden IoT Edge-eszközön fut, legalább két modul: $edgeAgent és $edgeHub, amely az IoT Edge-futtatókörnyezet részét képezik. IoT Edge eszköz több további modult is futtathat tetszőleges számú folyamat esetén. Az üzembe helyezési jegyzék segítségével adja meg, hogy az eszköz mely modulokat telepítse, és hogyan konfigurálhatja őket a közös munkához. 

A *manifest nasazení* leíró JSON-dokumentumok:

* A **IoT Edge Agent** modul Twin, amely három összetevőt tartalmaz. 
  * Az eszközön futó egyes modulok tárolójának képe.
  * A modul rendszerképeit tartalmazó privát tároló-nyilvántartók eléréséhez szükséges hitelesítő adatok.
  * Útmutató az egyes modulok létrehozásához és kezeléséhez.
* A **IoT Edge hubot** tartalmaz, hogyan flow az üzeneteket a modulok között, és végül az IoT Hub ikermodul.
* Szükség esetén a kívánt tulajdonságok, minden további ikermodulokkal.

Minden IoT Edge-eszközök egy manifest nasazení kell konfigurálni. Egy újonnan telepített IoT Edge-futtatókörnyezet egy hibakódot, amíg nem egy érvényes jegyzékfájl konfigurált jelentések. 

Az Azure IoT Edge-oktatóanyagok és a egy manifest nasazení haladjon végig a varázsló az Azure IoT Edge-portálon létre. A manifest nasazení programozott módon, REST vagy az IoT Hub szolgáltatási SDK segítségével is alkalmazhat. További információkért lásd: [megismerheti az IoT Edge-telepítések](module-deployment-monitoring.md).

## <a name="create-a-deployment-manifest"></a>Hozzon létre egy manifest nasazení

Magas szinten a manifest nasazení az ikermodulokkal, amelyeken a kívánt tulajdonságok listája. Egy manifest nasazení közli az IoT Edge-eszköz (vagy eszközök egy csoportját) melyik modulokat telepíteni és konfigurálni őket. Üzembe helyezés jegyzékek közé tartozik a *kívánt tulajdonságok* egyes ikermodul. IoT Edge-eszközök jelentéseket küldhetnek vissza a *jelentett tulajdonságokként* az egyes modulok. 

Két modult kell minden manifest nasazení: `$edgeAgent`, és `$edgeHub`. Ezeket a modulokat az IoT Edge-futtatókörnyezet, amely felügyeli az IoT Edge-eszköz és a rajta futó modulok részét képezik. Ezek a modulok kapcsolatos további információkért lásd: [megismerheti az IoT Edge-futtatókörnyezet és az architektúrára](iot-edge-runtime.md).

A két futásidejű modulok mellett legfeljebb 20-modulok saját futtatását az IoT Edge-eszköz adhat hozzá. 

A csak az IoT Edge-futtatókörnyezet (edgeAgent és edgeHub) tartalmazó manifest nasazení je platná.

Üzembe helyezés jegyzékek Ez a struktúra kövesse:

```json
{
    "modulesContent": {
        "$edgeAgent": { // required
            "properties.desired": {
                // desired properties of the Edge agent
                // includes the image URIs of all modules
                // includes container registry credentials
            }
        },
        "$edgeHub": { //required
            "properties.desired": {
                // desired properties of the Edge hub
                // includes the routing information between modules, and to IoT Hub
            }
        },
        "module1": {  // optional
            "properties.desired": {
                // desired properties of module1
            }
        },
        "module2": {  // optional
            "properties.desired": {
                // desired properties of module2
            }
        },
        ...
    }
}
```

## <a name="configure-modules"></a>Modulok konfigurálása

Adja meg, hogyan az IoT Edge-futtatókörnyezet telepíti a központi telepítésben a modulokat. Az IoT Edge-ügynök nem a futásidejű összetevő, amely kezeli a telepítés, a frissítések és az IoT Edge-eszköz vonatkozó állapotjelentéseket. Ezért a $edgeAgent ikermodul igényel a konfigurációs és felügyeleti információk összes modult. Ez az információ magában foglalja a IoT Edge-ügynök konfigurációs paramétereit is. 

A megadható vagy kötelező tulajdonságok teljes listájáért tekintse meg [a IoT Edge ügynök és a IoT Edge hub tulajdonságait](module-edgeagent-edgehub.md).

Ez a struktúra hajtsa végre a $edgeAgent tulajdonságai:

```json
"$edgeAgent": {
    "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
            "settings":{
                "registryCredentials":{ // give the edge agent access to container images that aren't public
                    }
                }
            }
        },
        "systemModules": {
            "edgeAgent": {
                // configuration and management details
            },
            "edgeHub": {
                // configuration and management details
            }
        },
        "modules": {
            "module1": { // optional
                // configuration and management details
            },
            "module2": { // optional
                // configuration and management details
            }
        }
    }
},
```

## <a name="declare-routes"></a>Útvonalak deklarálása

Az IoT Edge hubot modulok, az IoT Hub és bármely levéleszközök közötti kommunikációt kezeli. Ezért a $edgeHub ikermodul nevű kívánt tulajdonságot tartalmaz *útvonalak* , kijelenti, hogy hogyan továbbított üzenetek egy telepítésben található. Több útvonal ugyanazon környezetben is rendelkezhet.

Útvonalak vannak deklarálva a **$edgeHub** kívánt tulajdonságot a következő szintaxissal:

```json
"$edgeHub": {
    "properties.desired": {
        "routes": {
            "route1": "FROM <source> WHERE <condition> INTO <sink>",
            "route2": "FROM <source> WHERE <condition> INTO <sink>"
        },
    }
}
```

Minden útvonal van szüksége, a forrás és a egy fogadó, de a feltétel egy választható elem használatával szűrik az üzeneteket. 


### <a name="source"></a>Forrás

A forrás Megadja, hogy honnan származnak az üzeneteket. A IoT Edge üzeneteket küldhet a modulokból vagy a levelekből származó eszközökről. 

A IoT SDK-k használatával a modulok adott kimeneti várólistákat deklarálnak az üzeneteik számára a ModuleClient osztály használatával. A kimeneti várólisták nem szükségesek, de hasznosak lehetnek több útvonal kezeléséhez. A Leaf-eszközök a IoT SDK-k DeviceClient-osztályával ugyanúgy küldhetnek üzeneteket IoT Edge átjáró-eszközökre, mint az üzenetek küldését IoT Hub. További információ: az [Azure IoT hub SDK-k megismerése és használata](../iot-hub/iot-hub-devguide-sdks.md).

A source tulajdonság a következő értékek egyike lehet:

| Forrás | Leírás |
| ------ | ----------- |
| `/*` | Az összes eszköz – felhő üzeneteket vagy ikereszköz fájlmódosítási értesítések bármely modul vagy a levél eszközről |
| `/twinChangeNotifications` | Minden modul vagy a levél eszközről érkező ikereszköz módosítása (jelentett Tulajdonságok) |
| `/messages/*` | Bármely, a modul által egy vagy több kimeneten vagy egy levélen keresztül küldött eszközről a felhőbe irányuló üzenet |
| `/messages/modules/*` | Bármely eszköz-felhő által küldött üzenet egy modul át néhány vagy nincs kimenet |
| `/messages/modules/<moduleId>/*` | Bármilyen eszközről a felhőbe által küldött üzenet egy adott modul át néhány vagy nincs kimenet |
| `/messages/modules/<moduleId>/outputs/*` | Bármilyen eszközről a felhőbe által küldött üzenet egy adott modul néhány kimenetet keresztül |
| `/messages/modules/<moduleId>/outputs/<output>` | Bármely eszköz-felhő által küldött üzenet egy adott kimeneti keresztül ugyan adott modulok |

### <a name="condition"></a>Állapot
A feltétel nem kötelező útvonal határozza meg. Ha az összes üzenetet át szeretné adni a forrásról a fogadónak, csak hagyja ki a **Where** záradékot teljesen. Vagy használhatja a [IoT Hub lekérdezési nyelv](../iot-hub/iot-hub-devguide-routing-query-syntax.md) szűrése az egyes üzenetek vagy üzenettípust, amely megfelel a feltételnek. IoT Edge-útvonalakat nem támogatják a szűrési üzenetek ikereszköz – címkék és tulajdonságok alapján. 

Az, hogy az IoT Edge moduljai közötti üzenetek ugyanaz, mint az, hogy az eszközök és az Azure IoT Hub közötti üzenetek vannak formázva. Összes üzenet formázott JSON-fájlként, és rendelkezik **systemProperties**, **appProperties**, és **törzs** paramétereket. 

Lekérdezések körül bármely, a három paraméter a következő szintaxissal hozhat létre: 

* A Rendszertulajdonságok: `$<propertyName>` vagy `{$<propertyName>}`
* Alkalmazás tulajdonságai: `<propertyName>`
* Törzs tulajdonságai: `$body.<propertyName>` 

Az üzenet tulajdonságainak lekérdezések létrehozásával kapcsolatos további példákért lásd [útvonalak lekérdezési kifejezések eszköz – felhő üzenetek](../iot-hub/iot-hub-devguide-routing-query-syntax.md).

Adott IoT Edge-ben például akkor, ha levél eszközről átjáróeszköz érkezett üzenetek szűrését. Modulok származó üzenetek közé tartozik a rendszer tulajdonsággal **connectionModuleId**. Ezért üzenetek továbbítását a levél eszközökről közvetlenül az IoT hubhoz, használja a következő útvonal kizárása a modul üzeneteket:

```query
FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO $upstream
```

### <a name="sink"></a>Sink (Fogadó)
A fogadó határozza meg, ahol az üzenetek érkeznek. Csak a modulok és az IoT Hub fogadhat üzeneteket. Üzenetek más eszközök például nem irányítható. Nem tartoznak a fogadó tulajdonságban helyettesítő beállítások. 

A fogadó tulajdonság a következő értékek egyike lehet:

| Sink (Fogadó) | Leírás |
| ---- | ----------- |
| `$upstream` | Az üzenet elküldéséhez az IoT hubhoz |
| `BrokeredEndpoint("/modules/<moduleId>/inputs/<input>")` | Az üzenet küldése egy adott modul egy meghatározott bevitel |

IoT Edge: legalább egyszeri garanciákat nyújt. Az IoT Edge hub helyileg tárolja az üzeneteket, ha egy útvonal nem tudja kézbesíteni az üzenetet a fogadónak. Ha például az IoT Edge hub nem tud csatlakozni a IoT Hubhoz, vagy a célként megadott modul nincs csatlakoztatva.

IoT Edge hub a `storeAndForwardConfiguration.timeToLiveSecs` [IoT Edge hub kívánt tulajdonságainak](module-edgeagent-edgehub.md)tulajdonságában megadott időpontig tárolja az üzeneteket.

## <a name="define-or-update-desired-properties"></a>Adja meg, vagy az eszköz kívánt tulajdonságainak frissítése 

Manifest nasazení megadja az egyes modulok IoT Edge-eszközökön üzembe helyezett kívánt tulajdonságokat. Manifest nasazení a kívánt tulajdonságok írja felül a jelenleg a az ikermodul kívánt tulajdonságokat.

Ha nem adja meg a moduliker kívánt tulajdonságainak manifest nasazení, az IoT Hub az ikermodul semmilyen módon nem módosíthatja. Ehelyett a kívánt tulajdonságokat programozott módon is beállíthatja.

Ugyanazt a mechanizmust, amelyek lehetővé teszik, hogy módosítsa az ikereszközök ikermodulokkal módosítására szolgálnak. További információkért lásd: a [modul ikereszköz – fejlesztői útmutató](../iot-hub/iot-hub-devguide-module-twins.md).   

## <a name="deployment-manifest-example"></a>Jegyzékfájl központi telepítés példája

Az alábbi példa bemutatja, milyen nézhet ki egy érvényes telepítési jegyzékfájl dokumentum.

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
          "type": "docker",
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {
              "ContosoRegistry": {
                "username": "myacr",
                "password": "<password>",
                "address": "myacr.azurecr.io"
              }
            }
          }
        },
        "systemModules": {
          "edgeAgent": {
            "type": "docker",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
              "createOptions": ""
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
              "createOptions": ""
            }
          }
        },
        "modules": {
          "SimulatedTemperatureSensor": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
              "createOptions": "{}"
            }
          },
          "filtermodule": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "myacr.azurecr.io/filtermodule:latest",
              "createOptions": "{}"
            }
          }
        }
      }
    },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {
          "sensorToFilter": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
          "filterToIoTHub": "FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream"
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 10
        }
      }
    }
  }
}
```

## <a name="next-steps"></a>További lépések

* A $edgeAgent és az $edgeHubben felvehető vagy befoglalható tulajdonságok teljes listáját az [IoT Edge ügynök és IoT Edge hub tulajdonságai](module-edgeagent-edgehub.md)részben találja.

* Most, hogy megismerte, hogyan IoT Edge-modulok használata esetén [megismerhesse a követelményeket és az eszközök IoT Edge-modulok](module-development.md).
