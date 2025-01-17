---
title: Azure IoT Hub és Event Grid | Microsoft Docs
description: A Azure Event Grid használatával a folyamatokat a IoT Hubban végrehajtott műveletek alapján aktiválhatja.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.openlocfilehash: 0f8942d92958ee8add9645239cc5664a4a96bb33
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69533355"
---
# <a name="react-to-iot-hub-events-by-using-event-grid-to-trigger-actions"></a>IoT Hub eseményekre való reagálás Event Grid használatával a műveletek elindításához

Az Azure IoT Hub integrálható Azure Event Gridekkel, így értesítéseket küldhet más szolgáltatásoknak, és alsóbb rétegbeli folyamatokat indíthat. Konfigurálja üzleti alkalmazásait IoT Hub események figyelésére, hogy megbízható, skálázható és biztonságos módon reagáljon a kritikus eseményekre. Létrehozhat például egy adatbázist frissítő alkalmazást, létrehoz egy munkahelyi jegyet, és minden alkalommal elküld egy e-mail-értesítést, amikor új IoT-eszköz regisztrálva van az IoT hub-ban.

A [Azure Event Grid](../event-grid/overview.md) egy teljes körűen felügyelt esemény-útválasztási szolgáltatás, amely egy közzétételi és előfizetési modellt használ. Event Grid beépített támogatást biztosít az Azure-szolgáltatásokhoz, [](../azure-functions/functions-overview.md) például a Azure Functionshoz és a [Azure Logic Appshoz](../logic-apps/logic-apps-what-are-logic-apps.md), és az események riasztásait a nem Azure-szolgáltatásokhoz webhookok használatával lehet kézbesíteni. A Event Grid által támogatott eseménykezelők teljes listájáért tekintse [meg a Azure Event Grid bemutatása](../event-grid/overview.md)című témakört.

![Azure Event Grid architektúra](./media/iot-hub-event-grid/event-grid-functional-model.png)

## <a name="regional-availability"></a>Régiónkénti rendelkezésre állás

A Event Grid integráció a Event Grid által támogatott régiókban található IoT hubok számára érhető el. Az eszközök összes eseménye, kivéve az telemetria események általánosan elérhetők. Az telemetria-esemény nyilvános előzetes verzióban érhető el, és minden régióban elérhető az USA keleti régiója, az USA nyugati régiója, Nyugat-Európa, [Azure Government](/azure/azure-government/documentation-government-welcome), az [Azure China 21Vianet](/azure/china)és az [Azure Germany](https://azure.microsoft.com/global-infrastructure/germany/)kivételével. A régiók legújabb listáját a [Azure Event Grid bemutatása](../event-grid/overview.md)című témakörben tekintheti meg.

## <a name="event-types"></a>Események típusai

IoT Hub közzéteszi a következő eseménytípus-típusokat:

| Esemény típusa | Leírás |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Közzétett, ha egy eszköz regisztrálva van egy IoT-hubhoz. |
| Microsoft.Devices.DeviceDeleted | Közzétételre kerül, ha egy eszközt törölnek egy IoT-hubhoz. |
| Microsoft.Devices.DeviceConnected | Akkor jelenik meg, amikor egy eszköz IoT-hubhoz csatlakozik. |
| Microsoft.Devices.DeviceDisconnected | Akkor jelenik meg, ha egy eszköz le van választva egy IoT hubhoz. |
| Microsoft.Devices.DeviceTelemetry | Közzétételre kerül, amikor egy eszköz telemetria üzenetet küld egy IoT-hubhoz |

A Azure Portal vagy az Azure CLI használatával konfigurálhatja, hogy mely eseményeket kell közzétenni az egyes IoT-központokból. Például próbálja ki az oktatóanyagot az [Azure IoT hub eseményekkel kapcsolatos e-mail-értesítések küldéséhez Logic Apps használatával](../event-grid/publish-iot-hub-events-to-logic-apps.md).

## <a name="event-schema"></a>Eseményséma

IoT Hub események tartalmazzák az eszköz életciklusában bekövetkező változásokra való reagáláshoz szükséges összes információt. IoT Hub eseményt a **Microsoft. Devices**EventType tulajdonságának ellenőrzésével azonosíthatja. További információ az Event Grid esemény tulajdonságainak használatáról: [Event Grid Event Schema](../event-grid/event-schema.md).

### <a name="device-connected-schema"></a>Csatlakoztatott eszköz sémája

Az alábbi példa egy csatlakoztatott eszköz sémáját mutatja be:

```json
[{  
  "id": "f6bbf8f4-d365-520d-a878-17bf7238abd8",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceConnected",
  "eventTime": "2018-06-02T19:17:44.4383997Z",
  "data": {
      "deviceConnectionStateEventInfo": {
        "sequenceNumber":
          "000000000000000001D4132452F67CE200000002000000000000000000000001"
      },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice",
    "moduleId" : "DeviceModuleID",
  }, 
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="device-telemetry-schema"></a>Eszköz telemetria sémája

Az eszköz telemetria érvényes JSON-formátumúnak kell lennie, és a contentType JSON-ra, a contentEncoding pedig UTF-8 értékre kell állítani az üzenetrendszer [tulajdonságaiban](iot-hub-devguide-routing-query-syntax.md#system-properties). Ha a beállítás nincs megadva, akkor a IoT Hub a 64 kódolású formátumban fogja írni az üzeneteket.

Az eszközök telemetria az Event Grid közzétételük előtt bővítheti, ha a végpontot Event Gridként kiválasztja. További információ: üzenet- [gazdagítás áttekintése](iot-hub-message-enrichments-overview.md).

Az alábbi példa egy eszköz telemetria-eseményének sémáját mutatja be:

```json
[{  
  "id": "9af86784-8d40-fe2g-8b2a-bab65e106785",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceTelemetry",
  "eventTime": "2019-01-07T20:58:30.48Z",
  "data": {
      "body": {
          "Weather": {
              "Temperature": 900
            },
            "Location": "USA"
        },
        "properties": {
            "Status": "Active"
        },
        "systemProperties": {
          "iothub-content-type": "application/json",
          "iothub-content-encoding": "utf-8",
          "iothub-connection-device-id": "d1",
          "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
          "iothub-connection-auth-generation-id": "123455432199234570",
          "iothub-enqueuedtime": "2019-01-07T20:58:30.48Z",
          "iothub-message-source": "Telemetry"
        }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="device-created-schema"></a>Eszköz létrehozva séma

Az alábbi példa egy eszköz létrehozott esemény sémáját mutatja be:

```json
[{
  "id": "56afc886-767b-d359-d59e-0da7877166b2",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceCreated",
  "eventTime": "2018-01-02T19:17:44.4383997Z",
  "data": {
    "twin": {
      "deviceId": "LogicAppTestDevice",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag":"null",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00",
      "connectionState": "Disconnected",
      "lastActivityTime": "0001-01-01T00:00:00",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "version": 2,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        },
        "reported": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        }
      }
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

Az egyes tulajdonságok részletes ismertetését lásd: [Azure Event Grid Event Schema for IoT hub](../event-grid/event-schema-iot-hub.md).

## <a name="filter-events"></a>Események szűrése

IoT Hub esemény-előfizetések szűrhetik az eseményeket az esemény típusa, az adattartalom és a tárgy alapján, amely az eszköz neve.

Event Grid lehetővé [](../event-grid/event-filtering.md) teszi az események, a témák és az adattartalom szűrését. A Event Grid előfizetés létrehozásakor választhat, hogy előfizet a kiválasztott IoT-eseményekre. A Event Grid-munkafolyamatban található tulajdonosi szűrők a (előtag) és **az** (utótag) egyezések alapján **kezdődnek** . A szűrő egy `AND` operátort használ, így az előtagot és utótagot is megegyező tárgyú események az előfizető számára lesznek továbbítva.

A IoT-események tárgya a formátumot használja:

```json
devices/{deviceId}
```

A Event Grid az egyes események attribútumain is lehetővé teszi a szűrést, beleértve az adattartalmat is. Így kiválaszthatja, hogy milyen események érkeznek a telemetria üzenet alapján. A példák megtekintéséhez tekintse meg a [speciális szűrést](../event-grid/event-filtering.md#advanced-filtering) ismertető témakört. A telemetria-üzenet törzsének szűréséhez a contentType a JSON és a contentEncoding értékre kell állítani az üzenetrendszer [tulajdonságai](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#system-properties)között.

A nem telemetria események, például a DeviceConnected, a DeviceDisconnected, a DeviceCreated és a DeviceDeleted esetében az előfizetés létrehozásakor az Event Grid szűrés használható. A telemetria-események esetében a Event Grid szűrésén kívül a felhasználók az üzenet-útválasztási lekérdezésen keresztül is szűrhetik az eszközön az ikreket, az üzenet tulajdonságait és a törzsét. Egy alapértelmezett [útvonalat](iot-hub-devguide-messages-d2c.md) hozunk létre IoT hub, az eszköz telemetria Event Grid-előfizetése alapján. Ez az egyetlen útvonal képes kezelni az összes Event Grid-előfizetését. Az üzenetek telemetria az adatküldés előtt frissítheti az [útválasztási lekérdezést](iot-hub-devguide-routing-query-syntax.md). Vegye figyelembe, hogy az útválasztási lekérdezés csak akkor alkalmazható az üzenet törzsére, ha a törzs JSON. A contentType a JSON és a contentEncoding értékre kell állítania az üzenetrendszer [tulajdonságai](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#system-properties)között az UTF-8 értékre.

## <a name="limitations-for-device-connected-and-device-disconnected-events"></a>Az eszköz csatlakoztatott és leválasztott eseményeinek korlátai

Az eszköz csatlakoztatott és leválasztott eseményeinek fogadásához meg kell nyitnia az eszköz D2C vagy C2D mutató hivatkozását. Ha az eszköz MQTT protokollt használ, IoT Hub megnyitva marad a C2D hivatkozás. A AMQP a C2D hivatkozást a [fogadási ASZINKRON API](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync?view=azure-dotnet)meghívásával nyithatja meg.

A D2C hivatkozás meg van nyitva, ha telemetria küld. Ha az eszköz kapcsolata vibrál, ami azt jelenti, hogy az eszköz gyakran csatlakozik, és leválasztja a kapcsolatot, a rendszer nem küldi el minden egyes kapcsolati állapotot, de közzéteszi azt a kapcsolati állapotot, amelynek a pillanatképét percenként hozza létre. IoT Hub kimaradás esetén a leállás után azonnal közzé fogjuk tenni az eszköz kapcsolati állapotát. Ha az eszköz bontja a kapcsolatot a leállás során, az eszköz leválasztott eseménye 10 percen belül közzétételre kerül.

## <a name="tips-for-consuming-events"></a>Tippek az események fogyasztásához

Az IoT Hub eseményeket kezelő alkalmazásoknak a következő ajánlott eljárásokat kell követniük:

* Több előfizetés is konfigurálható az események ugyanahhoz az eseménykezelőhöz való továbbítására, ezért ne feltételezzük, hogy az események egy adott forrásból származnak. Mindig ellenőrizze az üzenet témakörét, és győződjön meg arról, hogy az a várt IoT hub-ról származik.

* Ne Tételezzük fel, hogy minden kapott esemény a várt típus. Az üzenet feldolgozása előtt mindig keresse meg a eventType.

* Az üzenetek megérkeznek a sorrendbe, vagy késés után is. A ETAG mező használatával megtudhatja, hogy az objektumokkal kapcsolatos adatok naprakészek-e az eszköz által létrehozott vagy az eszköz által törölt események esetében.

## <a name="next-steps"></a>További lépések

* [Próbálja ki a IoT Hub Events oktatóanyagot](../event-grid/publish-iot-hub-events-to-logic-apps.md)

* [Ismerje meg, hogyan rendezheti az eszközhöz csatlakoztatott és nem csatlakoztatott eseményeket](iot-hub-how-to-order-connection-state-events.md)

* [További információ a Event Grid](../event-grid/overview.md)

* [Az Útválasztás IoT Hub események és üzenetek közötti különbségek összehasonlítása](iot-hub-event-grid-routing-comparison.md)

* [Ismerje meg, hogyan használhatók a IoT telemetria-események a IoT térbeli elemzések megvalósításához Azure Maps használatával (és IoT Hub üzenet-útválasztással)](../azure-maps/tutorial-iot-hub-maps.md#filter-events-using-iot-hub-message-routing)
