---
title: Az Azure IoT Hub állapotának figyelése |} A Microsoft Docs
description: Az Azure Monitor és Azure Resource Health segítségével figyelheti az IoT Hub és a problémák gyorsan diagnosztizálása
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/27/2019
ms.author: kgremban
ms.openlocfilehash: 6dea1add1e329cfc894068732898a856a69c9b4c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66166207"
---
# <a name="monitor-the-health-of-azure-iot-hub-and-diagnose-problems-quickly"></a>Azure IoT Hub állapotának monitorozásához és a problémák gyorsan diagnosztizálása

Azure IoT Hub alkalmazó vállalatok erőforrásaikat a megbízható teljesítmény várható. Segítséget egy közeli nézze meg a műveletek a kezelése, az IoT Hub teljesen integrálva van a [Azure Monitor](../azure-monitor/index.yml) és [az Azure Resource Health](../service-health/resource-health-overview.md). A két szolgáltatás működik biztosítani számukra, így az IoT-megoldásait, mentése és kifogástalan állapotban fut szükséges adatokat.

Az Azure Monitor egyetlen adatforrás a figyelés és naplózás az Azure-szolgáltatásokhoz. Elküldheti a diagnosztikai naplók, amely létrehozza az Azure Monitor az Azure Monitor naplók, az Event Hubs vagy Azure Storage egyéni feldolgozáshoz. Az Azure Monitor-metrikák és diagnosztikai beállításait az erőforrások betekintést nyújtanak. Olvassa ebből a cikkből megtudhatja, hogyan [használata az Azure Monitor](#use-azure-monitor) az IoT hubbal. 

> [!IMPORTANT]
> A diagnosztikai naplók az Azure Monitor használatával az IoT Hub-szolgáltatás által kibocsátott események a rendszer nem garantált, hogy a megbízható és rendezett. Néhány esemény esetleg elvész, vagy -i üzemen kívüli. Diagnosztikai naplók is nincsenek szinkronban kell lennie a valós idejű, és jelentkezzen be a kívánt rendeltetési események több percig is eltarthat.

Az Azure Resource Health segítségével diagnosztizálhatja és a támogatás igénylésében, ha egy Azure-beli probléma kihat az erőforrásaira. Egy irányítópult aktuális és korábbi állapotát az egyes IoT-központok biztosít. Folytassa a szakasz alján, ebből a cikkből megtudhatja, hogyan [használata az Azure Resource Health](#use-azure-resource-health) az IoT hubbal. 

IoT Hub is biztosít a saját mérőszámok, amelyek segítségével az IoT-erőforrások állapotának ismertetése. További tudnivalókért lásd: [megismerheti az IoT Hub-metrikák](iot-hub-metrics.md).

## <a name="use-azure-monitor"></a>Az Azure Monitor használata

Az Azure Monitor biztosít a diagnosztikai adatokat az Azure-erőforrásokhoz, ami azt jelenti, hogy figyelemmel kísérheti a műveleteket, az IoT hub között kerül sor.

Az Azure Monitor diagnosztikai beállításait lecseréli az IoT Hub-műveletek monitorozása. Ha jelenleg használja a műveletek figyelése, át kell telepítenie a munkafolyamatokat. További információkért lásd: [a műveletek figyelése a diagnosztikai beállítások](iot-hub-migrate-to-diagnostics-settings.md).

Az adott mérőszámok és eseményeket figyeli az Azure Monitor kapcsolatos további információkért lásd: [az Azure monitorban támogatott mérőszámok](../azure-monitor/platform/metrics-supported.md) és [támogatott szolgáltatások, sémákat és kategóriák az Azure diagnosztikai naplók](../azure-monitor/platform/diagnostic-logs-schema.md).

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="understand-the-logs"></a>A naplók értelmezése

Az Azure Monitor nyomon követi az IoT Hub előforduló különféle műveletek. Az egyes kategóriákhoz hogyan készül jelentés adott kategóriába tartozó eseményeket definiáló séma.

#### <a name="connections"></a>Kapcsolatok

A kapcsolatok kategóriában nyomon követi eszköz csatlakoztatása, és események leválasztása az IoT hubra, valamint a hibák. Ez a kategória jogosulatlan kapcsolódási kísérletek azonosításával és vagy a riasztás az eszköz kapcsolata megszakad, ha hasznos.

> [!NOTE]
> Az eszközök megbízható kapcsolat állapotának ellenőrzése [eszköz szívverés](iot-hub-devguide-identity-registry.md#device-heartbeat).

```json
{
   "records":
   [
        {
            "time": " UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "deviceConnect",
            "category": "Connections",
            "level": "Information",
            "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"maskedIpAddress\":\"<maskedIpAddress>\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="cloud-to-device-commands"></a>Felhőből az eszközre irányuló parancsok

A felhőből az eszközre irányuló parancsok kategória előforduló hibák az IoT hubra a felhőből az eszközre irányuló üzenetek folyamat kapcsolódó követi nyomon. Ez a kategória tartalmazza a felmerülő hibák:

* Felhőből az eszközre irányuló üzenetek küldése (például a jogosulatlan feladótól hibák)
* (Például a szállítási darabszám túllépve hibák), a felhőből az eszközre irányuló üzenetek fogadása és
* A visszajelzést a felhőből az eszközre irányuló üzenet (például a visszajelzések hibák lejárt).

Ez a kategória nem hibák észlelését, ha a felhőből az eszközre irányuló üzenet sikeresen kézbesítve, de nem megfelelően kezeli a rendszer az eszköz által.

```json
{
    "records":
    [
        {
            "time": " UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "messageExpired",
            "category": "C2DCommands",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"deviceId\":\"<deviceId>\",\"messageId\":\"<messageId>\",\"messageSizeInBytes\":\"<messageSize>\",\"protocol\":\"Amqp\",\"deliveryAcknowledgement\":\"<None, NegativeOnly, PositiveOnly, Full>\",\"deliveryCount\":\"0\",\"expiryTime\":\"<timestamp>\",\"timeInSystem\":\"<timeInSystem>\",\"ttl\":<ttl>, \"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\", \"maskedIpAddress\": \"<maskedIpAddress>\", \"statusCode\": \"4XX\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="device-identity-operations"></a>Eszközművelet identitás

Eszközkategória identitás műveletek nyomon követi, létrehozásához, frissítéséhez vagy törléséhez egy bejegyzés az IoT hub eszközidentitás-jegyzékben lévő megkísérlésekor előforduló hibákat. Ebbe a kategóriába követési hasznos forgatókönyvek kiépítéshez.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "get",
            "category": "DeviceIdentityOperations",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"maskedIpAddress\":\"<maskedIpAddress>\",\"deviceId\":\"<deviceId>\", \"statusCode\":\"4XX\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="routes"></a>Útvonalak

Az üzenet-útválasztási kategória üzenet útvonal értékelése és a végpont-állapotot az IoT Hub által érzékelt során felmerülő hibák nyomon követi. Ez a kategória események például tartalmazza:

* Szabály eredménye "nem definiált",
* Az IoT Hub, a kézbesítetlen levelek, jelöli meg a végpont vagy
* A végpont kapott esetleges hibákat. 

Ebbe a kategóriába nem tartalmazza a konkrét hibákat maguk az üzenetek (például a szabályozási hibák eszköz), amely az "eszköz telemetriai" kategóriában szerepelnek.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "endpointUnhealthy",
            "category": "Routes",
            "level": "Error",
            "properties": "{\"deviceId\": \"<deviceId>\",\"endpointName\":\"<endpointName>\",\"messageId\":<messageId>,\"details\":\"<errorDetails>\",\"routeName\": \"<routeName>\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="device-telemetry"></a>Eszköztelemetria

Az eszközkategória telemetriai előforduló hibák az IoT hubra kapcsolódnak, a telemetriai adatok folyamatot követi nyomon. Ez a kategória tartalmazza (például a szabályozás) telemetriai események küldése során előforduló hibákat, valamint telemetrikus eseményeket (például illetéktelen olvasó) fogadását. Ez a kategória nem tényleges magán az eszközön futó által okozott hibákat.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "ingress",
            "category": "DeviceTelemetry",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"deviceId\":\"<deviceId>\",\"batching\":\"0\",\"messageSizeInBytes\":\"<messageSizeInBytes>\",\"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\",\"partitionId\":\"1\"}", 
            "location": "Resource location"
        }
    ]
}
```

#### <a name="file-upload-operations"></a>Fájlfeltöltési műveletek

A fájl feltöltése kategória nyomon követi az IoT hubra és a fájlfeltöltési funkciókhoz kapcsolódó hibák. Ez a kategória tartalmazza:

* Az SAS URI-t, például amikor egy eszköz értesíti a hub egy befejezett feltöltésről, mielőtt lejár az előforduló hibákat.

* Nem sikerült az eszköz által jelentett feltöltések.

* Ha egy fájl nem található a tároló az IoT Hub értesítési üzenet létrehozása során előforduló hibákat.

Ez a kategória nem tényleges jelentkező hibák közvetlenül az eszköz egy fájlt tölt Storage.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "ingress",
            "category": "FileUploadOperations",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"blobUri\":\"http//bloburi.com\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="cloud-to-device-twin-operations"></a>Felhőalapú ikereszköz műveletek

A felhő ikereszköz műveletkategória események szolgáltatás által az ikereszközök követi nyomon. Ezek a műveletek belefoglalhat get ikereszköz, frissítése vagy cserélje le a címkéket, és a frissítése vagy cserélje le a kívánt tulajdonságok.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "read",
            "category": "C2DTwinOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="device-to-cloud-twin-operations"></a>Eszközről a felhőbe – az ikereszköz-műveletek

Az eszközről a felhőbe – az ikereszköz műveletkategória eszköz által kezdeményezett események az ikereszközök követi nyomon. Ezek a műveletek közé tartozik a get-twin, jelentett tulajdonságok frissítésére, és feliratkozhat a kívánt tulajdonságok.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "update",
            "category": "D2CTwinOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authenticationType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="twin-queries"></a>Ikereszköz-lekérdezések

Az ikereszköz-lekérdezéseket kategória, amely a felhőben kezdeményezett ikereszközök lekérdezésekre vonatkozó kérelmek kapcsolatos jelentések.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "query",
            "category": "TwinQueries",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"query\":\"<twin query>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\",\"pageSize\":\"<pageSize>\", \"continuation\":\"<true, false>\", \"resultSize\":\"<resultSize>\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="jobs-operations"></a>Feladatműveletek

A feladatok műveletkategória feladatkérések ikereszközök frissítéséhez, vagy több eszközön közvetlen metódusok meghívása kapcsolatos jelentések. Ezek a kérelmek kezdeményezett a felhőben.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "jobCompleted",
            "category": "JobsOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"jobId\":\"<jobId>\", \"sdkVersion\": \"<sdkVersion>\",\"messageSize\": <messageSize>,\"filter\":\"DeviceId IN ['1414ded9-b445-414d-89b9-e48e8c6285d5']\",\"startTimeUtc\":\"Wednesday, September 13, 2017\",\"duration\":\"0\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="direct-methods"></a>Közvetlen metódusok

A közvetlen metódusok kategória nyomon követi a kérés-válasz interakciók egyes eszközöknek. Ezek a kérelmek kezdeményezett a felhőben.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "send",
            "category": "DirectMethods",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":<messageSize>, \"RequestSize\": 1, \"ResponseSize\": 1, \"sdkVersion\": \"2017-07-11\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="distributed-tracing-preview"></a>Elosztott nyomkövetési (előzetes verzió)

Kategória elosztott nyomkövetést korrelációs azonosítók, amelyek a nyomkövetési környezet fejléc üzenetek követi nyomon. Ezek a naplók teljes engedélyezéséhez ügyféloldali kódot frissíteni kell a következő [elemzés és diagnosztizálhatja a IoT alkalmazások – teljes körű az IoT Hub elosztott nyomkövetést (előzetes verzió)](iot-hub-distributed-tracing.md).

Vegye figyelembe, hogy `correlationId` megfelel-e a [W3C nyomkövetési környezet](https://github.com/w3c/trace-context) javaslatot, ha tartalmaz egy `trace-id` , valamint egy `span-id`.

##### <a name="iot-hub-d2c-device-to-cloud-logs"></a>IoT Hub D2C (device-to-cloud) logs

Az IoT Hub tartalmaz tulajdonságokat érvényes nyomkövetési üzenet érkezik a IoT Hub ebbe a naplófájlba rögzíti.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubD2C",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-0144d2590aacd909-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Receive message success",
            "durationMs": "",
            "properties": "{\"messageSize\": 1, \"deviceId\":\"<deviceId>\", \"callerLocalTimeUtc\": : \"2017-02-22T03:27:28.633Z\", \"calleeLocalTimeUtc\": \"2017-02-22T03:27:28.687Z\"}",
            "location": "Resource location"
        }
    ]
}
```

Itt `durationMs` rendszer nem számítja ki, az IoT Hub órája nem lehet az eszköz órája szinkronban, és így egy időtartamának kiszámítása félrevezető lehet. Javasoljuk, hogy az időbélyegek használatával logika a `properties` adatforgalmi csúcsokhoz rögzítheti az eszközről a felhőbe késés szakaszban.

| Tulajdonság | Típus | Leírás |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **messageSize** | Egész szám | A mérete (bájt) eszköz – felhő üzenetek |
| **deviceId** | ASCII 7 bites alfanumerikus karakterekből álló karakterlánc | Az eszköz identitásának |
| **callerLocalTimeUtc** | UTC-időbélyeg | Az üzenet jelentése szerint az eszköz helyi órája létrehozásának idejét |
| **calleeLocalTimeUtc** | UTC-időbélyeg | Az átjáró az IoT Hub, IoT Hub szolgáltatás oldali óra által jelentett üzenet érkezés időpontja |

##### <a name="iot-hub-ingress-logs"></a>Az IoT Hub belépési naplók

Az IoT Hub ebbe a naplófájlba rögzíti, amikor érvényes nyomkövetési tulajdonságait tartalmazó üzenetet ír a belső vagy beépített eseményközpontba.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubIngress",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Ingress message success",
            "durationMs": "10",
            "properties": "{\"isRoutingEnabled\": \"true\", \"parentSpanId\":\"0144d2590aacd909\"}",
            "location": "Resource location"
        }
    ]
}
```

Az a `properties` szakaszban Ez a napló üzenet bejövő kapcsolatos további információkat tartalmaz.

| Tulajdonság | Típus | Leírás |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **isRoutingEnabled** | String | IGAZ vagy hamis, azt jelzi, hogy üzenet-útválasztása engedélyezve van-e az IoT hubban |
| **parentSpanId** | String | A [span-id](https://w3c.github.io/trace-context/#parent-id) a szülő üzenet, amely ebben az esetben lenne a D2C üzenet nyomkövetés |

##### <a name="iot-hub-egress-logs"></a>Az IoT Hub kimenő forgalmi naplók

IoT Hub rekordok naplózása Ez mikor [útválasztási](iot-hub-devguide-messages-d2c.md) engedélyezve van, és az üzenet egy [végpont](iot-hub-devguide-endpoints.md). Útválasztás nem engedélyezett, ha az IoT Hub ezt a naplót nem rögzíti.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubEgress",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-98ac3578922acd26-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Egress message success",
            "durationMs": "10",
            "properties": "{\"endpointType\": \"EventHub\", \"endpointName\": \"myEventHub\", \"parentSpanId\":\"349810a9bbd28730\"}",
            "location": "Resource location"
        }
    ]
}
```

Az a `properties` szakaszban Ez a napló üzenet bejövő kapcsolatos további információkat tartalmaz.

| Tulajdonság | Típus | Leírás |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **endpointName** | String | Az útválasztási végpont neve |
| **EndpointType** | String | Az útválasztási végpont típusa |
| **parentSpanId** | String | A [span-id](https://w3c.github.io/trace-context/#parent-id) a szülő üzenet, amely ebben az esetben lenne az IoT Hub bejövő üzenet nyomkövetési |

### <a name="read-logs-from-azure-event-hubs"></a>Az Azure Event Hubs naplóinak olvasása

Miután beállította a diagnosztikai beállításokon keresztül eseménynaplózás, olvassa el a naplókat, hogy azok az információk alapján műveleteket végezheti el alkalmazásokat hozhat létre. A mintakód beolvassa a naplók egy adott eseményközpontból:

```csharp
class Program
{ 
    static string connectionString = "{your AMS eventhub endpoint connection string}";
    static string monitoringEndpointName = "{your AMS event hub endpoint name}";
    static EventHubClient eventHubClient;
    //This is the Diagnostic Settings schema
    class AzureMonitorDiagnosticLog
    {
        string time { get; set; }
        string resourceId { get; set; }
        string operationName { get; set; }
        string category { get; set; }
        string level { get; set; }
        string resultType { get; set; }
        string resultDescription { get; set; }
        string durationMs { get; set; }
        string callerIpAddress { get; set; }
        string correlationId { get; set; }
        string identity { get; set; }
        string location { get; set; }
        Dictionary<string, string> properties { get; set; }
    };

    static void Main(string[] args)
    {
        Console.WriteLine("Monitoring. Press Enter key to exit.\n");
        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, monitoringEndpointName);
        var d2cPartitions = eventHubClient.GetRuntimeInformationAsync().PartitionIds;
        CancellationTokenSource cts = new CancellationTokenSource();
        var tasks = new List<Task>();
        foreach (string partition in d2cPartitions)
        {
            tasks.Add(ReceiveMessagesFromDeviceAsync(partition, cts.Token));
        }
        Console.ReadLine();
        Console.WriteLine("Exiting...");
        cts.Cancel();
        Task.WaitAll(tasks.ToArray());
    }

    private static async Task ReceiveMessagesFromDeviceAsync(string partition, CancellationToken ct)
    {
        var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.UtcNow);
        while (true)
        {
            if (ct.IsCancellationRequested)
            {
                await eventHubReceiver.CloseAsync();
                break;
            }
            EventData eventData = await eventHubReceiver.ReceiveAsync(new TimeSpan(0,0,10));
            if (eventData != null)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                Console.WriteLine("Message received. Partition: {0} Data: '{1}'", partition, data);
                var deserializer = new JavaScriptSerializer();
                //deserialize json data to azure monitor object
                AzureMonitorDiagnosticLog message = new JavaScriptSerializer().Deserialize<AzureMonitorDiagnosticLog>(result);
            }
        }
    }
}
```

## <a name="use-azure-resource-health"></a>Az Azure Resource Health eszközt használni

Az Azure Resource Health segítségével figyelheti az IoT hub működik-e. Emellett megismerjük, akár regionális kimaradás az IoT hub állapotának negatív hatással van. Szeretné megtudni, részletes adatait az Azure IoT Hub állapotát, akkor javasoljuk, hogy Ön [használata az Azure Monitor](#use-azure-monitor).

Az Azure IoT Hub egy regionális szinten állapotát jelzi. Regionális kimaradás hatással van az IoT hubhoz, ha az állapot mutatja **ismeretlen**. További tudnivalókért lásd: [erőforrástípusok és állapot-ellenőrzések a az Azure resource health segítségével elérhető](../service-health/resource-health-checks-resource-types.md).

Az IoT hub állapotának ellenőrzéséhez kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Navigáljon a **Service Health** > **a Resource health**.

3. A legördülő mezőben válassza ki az előfizetését, majd válassza ki **az IoT Hub** erőforrás típusaként.

Egészségügyi adatok értelmezése kapcsolatos további információkért lásd: [az Azure resource health áttekintése](../service-health/resource-health-overview.md).

## <a name="next-steps"></a>További lépések

* [Megismerheti az IoT Hub-metrikák](iot-hub-metrics.md)
* [IoT távoli figyelés és értesítések az Azure Logic Apps csatlakoztatása az IoT hub és a postaláda](iot-hub-monitoring-notifications-with-azure-logic-apps.md)