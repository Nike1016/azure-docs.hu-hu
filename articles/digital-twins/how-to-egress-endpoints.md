---
title: Kimenő és végpontok az Azure Digital Twinsban | Microsoft Docs
description: Útmutató a végpontok Azure digitális ikrekkel való létrehozásához.
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: alinast
ms.openlocfilehash: 3c33992ce3c130d6c06e0709a9c4ddcab4fff159
ms.sourcegitcommit: fe50db9c686d14eec75819f52a8e8d30d8ea725b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/14/2019
ms.locfileid: "69013943"
---
# <a name="egress-and-endpoints"></a>Kimenő és végpontok

Az Azure Digital Twins-végpontok egy üzenetet vagy egy esemény-közvetítőt jelentenek a felhasználó Azure-előfizetésén belül. Az események és üzenetek elküldhetők az Azure Event Hubs, Azure Event Grid és Azure Service Bus témakörökbe.

Az eseményeket a rendszer az előre meghatározott útválasztási beállítások szerint irányítja a végpontokhoz. A felhasználók határozzák meg, hogy az egyes végpontok milyen *eseménytípus* fogadására jogosultak.

Ha többet szeretne megtudni az eseményekről, az útválasztásról és az eseményekről, tekintse meg az [útválasztási eseményeket és üzeneteket az Azure digitális Twins](./concepts-events-routing.md)szolgáltatásban.

## <a name="events"></a>Events

Az eseményeket IoT-objektumok (például eszközök és érzékelők) küldik el az Azure-üzenet és az esemény-közvetítők általi feldolgozáshoz. Az eseményeket a következő Azure Event Grid az [esemény sémájának hivatkozása](../event-grid/event-schema.md)határozza meg.

```JSON
{
  "id": "00000000-0000-0000-0000-000000000000",
  "subject": "ExtendedPropertyKey",
  "data": {
    "SpacesToNotify": [
      "3a16d146-ca39-49ee-b803-17a18a12ba36"
    ],
    "Id": "00000000-0000-0000-0000-000000000000",
      "Type": "ExtendedPropertyKey",
    "AccessType": "Create"
  },
  "eventType": "TopologyOperation",
  "eventTime": "2018-04-17T17:41:54.9400177Z",
  "dataVersion": "1",
  "metadataVersion": "1",
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Attribútum | Type | Leírás |
| --- | --- | --- |
| id | Karakterlánc | Az esemény egyedi azonosítója. |
| subject | Karakterlánc | Közzétevő által megadott elérési út az esemény tárgya számára. |
| data | object | Az erőforrás-szolgáltatóhoz tartozó esemény-adatértékek. |
| eventType | Karakterlánc | Az eseményforrás egyik regisztrált eseménytípus. |
| eventTime | Karakterlánc | Az esemény a szolgáltató UTC-ideje alapján történő létrehozásakor. |
| dataVersion | Karakterlánc | Az adatobjektum sémaverziója. A közzétevő határozza meg a séma verzióját. |
| metadataVersion | Karakterlánc | Az esemény-metaadatok sémaverziója. Event Grid a legfelső szintű tulajdonságok sémáját határozza meg. Event Grid megadja ezt az értéket. |
| topic | Karakterlánc | Az eseményforrás teljes erőforrás-elérési útja. Ez a mező nem írható. Event Grid megadja ezt az értéket. |

További információ a Event Grid esemény sémával kapcsolatban:

- Tekintse át a [Azure Event Grid esemény sémájának referenciáját](../event-grid/event-schema.md).
- Olvassa el az [Azure EventGrid Node. js SDK EventGridEvent](https://docs.microsoft.com/javascript/api/azure-eventgrid/eventgridevent?view=azure-node-latest)-referenciáját.

## <a name="event-types"></a>Események típusai

Az események típusai osztályozzák az esemény természetét, és a **EventType** mezőben vannak beállítva. Az elérhető eseménytípus a következő lista szerint adható meg:

- TopologyOperation
- UdfCustom
- SensorChange
- SpaceChange
- DeviceMessage

Az egyes eseménytípus esemény-formátumait a következő alszakaszokban részletesebben ismertetjük.

### <a name="topologyoperation"></a>TopologyOperation

A **TopologyOperation** a diagram módosításaira vonatkozik. A **tulajdonos** tulajdonság határozza meg az érintett objektum típusát. Az eseményt a következő típusú objektumok válthatják ki:

- Eszköz
- DeviceBlobMetadata
- DeviceExtendedProperty
- ExtendedPropertyKey
- ExtendedType
- KeyStore
- Jelentés
- Definíciós
- Érzékelő
- SensorBlobMetadata
- SensorExtendedProperty
- Szóköz
- SpaceBlobMetadata
- SpaceExtendedProperty
- SpaceResource
- SpaceRoleAssignment
- Rendszer
- Felhasználó
- UserBlobMetadata
- UserExtendedProperty

#### <a name="example"></a>Példa

```JSON
{
  "id": "00000000-0000-0000-0000-000000000000",
  "subject": "ExtendedPropertyKey",
  "data": {
    "SpacesToNotify": [
      "3a16d146-ca39-49ee-b803-17a18a12ba36"
    ],
    "Id": "00000000-0000-0000-0000-000000000000",
      "Type": "ExtendedPropertyKey",
    "AccessType": "Create"
  },
  "eventType": "TopologyOperation",
  "eventTime": "2018-04-17T17:41:54.9400177Z",
  "dataVersion": "1",
  "metadataVersion": "1",
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Value | Csere erre |
| --- | --- |
| YOUR_TOPIC_NAME | A testreszabott témakör neve |

### <a name="udfcustom"></a>UdfCustom

A **UdfCustom** egy felhasználó által definiált függvény (UDF) által eljuttatott esemény.
  
> [!IMPORTANT]  
> Ezt az eseményt explicit módon el kell juttatni az UDF-ből.

#### <a name="example"></a>Példa

```JSON
{
  "id": "568fd394-380b-46fa-925a-ebb96f658cce",
  "subject": "UdfCustom",
  "data": {
    "TopologyObjectId": "7c799bfc-1bff-4b9e-b15a-669933969d20",
    "ResourceType": "Space",
    "Payload": "\"Room is not available or air quality is poor\"",
    "CorrelationId": "568fd394-380b-46fa-925a-ebb96f658cce"
  },
  "eventType": "UdfCustom",
  "eventTime": "2018-10-02T06:50:15.198Z",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Value | Csere erre |
| --- | --- |
| YOUR_TOPIC_NAME | A testreszabott témakör neve |

### <a name="sensorchange"></a>SensorChange

A **SensorChange** az érzékelők állapotának frissítése a telemetria változásai alapján.

#### <a name="example"></a>Példa

```JSON
{
  "id": "60bf5336-2929-45b4-bb4c-b45699dfe95f",
  "subject": "SensorChange",
  "data": {
    "Type": "Classic",
    "DataType": "Motion",
    "Id": "60bf5336-2929-45b4-bb4c-b45699dfe95f",
    "Value": "False",
    "PreviousValue": "True",
    "EventTimestamp": "2018-04-17T17:46:15.4964262Z",
    "MessageType": "sensor",
    "Properties": {
      "ms-client-request-id": "c9e576b7-5eea-4f61-8617-92a57add5179",
      "ms-activity-id": "ct22YwXEGJ5u.605.0"
    }
  },
  "eventType": "SensorChange",
  "eventTime": "2018-04-17T17:46:18.5452993Z",
  "dataVersion": "1",
  "metadataVersion": "1",
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Value | Csere erre |
| --- | --- |
| YOUR_TOPIC_NAME | A testreszabott témakör neve |

### <a name="spacechange"></a>SpaceChange

A **SpaceChange** a telemetria változásai alapján frissíti a terület állapotát.

#### <a name="example"></a>Példa

```JSON
{
  "id": "42522e10-b1aa-42ff-a5e7-7181788ffc4b",
  "subject": "SpaceChange",
  "data": {
    "Type": null,
    "DataType": "AvailableAndFresh",
    "Id": "7c799bfc-1bff-4b9e-b15a-669933969d20",
    "Value": "Room is not available or air quality is poor",
    "PreviousValue": null,
    "RawData": null,
    "transactionId": null,
    "EventTimestamp": null,
    "MessageType": null,
    "Properties": null,
    "CorrelationId": "42522e10-b1aa-42ff-a5e7-7181788ffc4b"
  },
  "eventType": "SpaceChange",
  "eventTime": "2018-10-02T06:50:20.128Z",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Value | Csere erre |
| --- | --- |
| YOUR_TOPIC_NAME | A testreszabott témakör neve |

### <a name="devicemessage"></a>DeviceMessage

A **DeviceMessage**használatával olyan **EventHub** -kapcsolatokat adhat meg, amelyekhez a nyers telemetria események, valamint az Azure digitális ikrek is használhatók.

> [!NOTE]
> - A **DeviceMessage** csak a **EventHub**kombinálható. A **DeviceMessage** nem egyesíthető más típusú eseményekkel.
> - A **EventHub** vagy a **DeviceMessage**típusú kombinációhoz csak egy végpontot adhat meg.

## <a name="configure-endpoints"></a>Végpontok konfigurálása

A végpontok kezelése a végpontok API-n keresztül történik.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Az alábbi példák bemutatják, hogyan konfigurálhatja a támogatott végpontokat.

>[!IMPORTANT]
> Körültekintően figyeljen a **eventTypes** attribútumra. Meghatározza, hogy a végpont milyen típusú eseményeket kezel, és így határozza meg az útválasztást.

Hitelesített HTTP POST-kérelem a következőket illetően:

```plaintext
YOUR_MANAGEMENT_API_URL/endpoints
```

- Útvonal Service Bus **SensorChange**, **SpaceChange**és **TopologyOperation**típusú eseményekhez:

  ```JSON
  {
    "type": "ServiceBus",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_PRIMARY_KEY",
    "secondaryConnectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SECONDARY_KEY",
    "path": "YOUR_TOPIC_NAME"
  }
  ```

    | Value | Csere erre |
    | --- | --- |
    | YOUR_NAMESPACE | A végpont névtere |
    | YOUR_PRIMARY_KEY | A hitelesítéshez használt elsődleges kapcsolódási sztring |
    | YOUR_SECONDARY_KEY | A hitelesítéshez használt másodlagos kapcsolódási sztring |
    | YOUR_TOPIC_NAME | A testreszabott témakör neve |

- Útvonal Event Grid **SensorChange**, **SpaceChange**és **TopologyOperation**típusú eseményekhez:

  ```JSON
  {
    "type": "EventGrid",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "YOUR_PRIMARY_KEY",
    "secondaryConnectionString": "YOUR_SECONDARY_KEY",
    "path": "YOUR_TOPIC_NAME.westus-1.eventgrid.azure.net"
  }
  ```

    | Value | Csere erre |
    | --- | --- |
    | YOUR_PRIMARY_KEY | A hitelesítéshez használt elsődleges kapcsolódási sztring|
    | YOUR_SECONDARY_KEY | A hitelesítéshez használt másodlagos kapcsolódási sztring |
    | YOUR_TOPIC_NAME | A testreszabott témakör neve |

- Útvonal Event Hubs **SensorChange**, **SpaceChange**és **TopologyOperation**típusú eseményekhez:

  ```JSON
  {
    "type": "EventHub",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_PRIMARY_KEY",
    "secondaryConnectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SECONDARY_KEY",
    "path": "YOUR_EVENT_HUB_NAME"
  }
  ```

    | Value | Csere erre |
    | --- | --- |
    | YOUR_NAMESPACE | A végpont névtere |
    | YOUR_PRIMARY_KEY | A hitelesítéshez használt elsődleges kapcsolódási sztring |
    | YOUR_SECONDARY_KEY | A hitelesítéshez használt másodlagos kapcsolódási sztring |
    | YOUR_EVENT_HUB_NAME | Az Event hub neve |

- Útvonal Event Hubs eseménytípus **DeviceMessage**. A `EntityPath` **ConnectionString** -ben való felvétel kötelező:

  ```JSON
  {
    "type": "EventHub",
    "eventTypes": [
      "DeviceMessage"
    ],
    "connectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_PRIMARY_KEY;EntityPath=YOUR_EVENT_HUB_NAME",
    "secondaryConnectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SECONDARY_KEY;EntityPath=YOUR_EVENT_HUB_NAME",
    "path": "YOUR_EVENT_HUB_NAME"
  }
  ```

    | Value | Csere erre |
    | --- | --- |
    | YOUR_NAMESPACE | A végpont névtere |
    | YOUR_PRIMARY_KEY | A hitelesítéshez használt elsődleges kapcsolódási sztring |
    | YOUR_SECONDARY_KEY | A hitelesítéshez használt másodlagos kapcsolódási sztring |
    | YOUR_EVENT_HUB_NAME | Az Event hub neve |

> [!NOTE]  
> Új végpont létrehozásakor akár 5 – 10 percet is igénybe vehet, hogy megkezdje az események fogadását a végponton.

## <a name="primary-and-secondary-connection-keys"></a>Elsődleges és másodlagos kapcsolatok kulcsai

Ha egy elsődleges kapcsolódási kulcs jogosulatlanul válik, a rendszer automatikusan megpróbálkozik a másodlagos kapcsolódási kulccsal. Ez biztosítja a biztonsági mentést, és lehetővé teszi, hogy a végpontok API-n keresztül szabályosan hitelesítse és frissítse az elsődleges kulcsot.

Ha az elsődleges és a másodlagos kapcsolódási kulcs is nem engedélyezett, a rendszer akár 30 percen belül exponenciális visszalépési várakozási időt is megad. Az események eldobása minden egyes kiváltott várakozási idő alatt megtörténik.

Ha a rendszer biztonsági mentési állapotban van, a kapcsolati kulcsok a végpontok API-n keresztül történő frissítése akár 30 percet is igénybe vehet.

## <a name="unreachable-endpoints"></a>Nem elérhető végpontok

Ha egy végpont elérhetetlenné válik, a rendszer akár 30 percen belül exponenciális visszatartási várakozási időt is elérhet. Az események eldobása minden egyes kiváltott várakozási idő alatt megtörténik.

## <a name="next-steps"></a>További lépések

- Ismerje meg [, hogyan használható az Azure Digital Twins hencegés](how-to-use-swagger.md).

- További információ az [útválasztási eseményekről és üzenetekről](concepts-events-routing.md) az Azure digitális Twins szolgáltatásban.
