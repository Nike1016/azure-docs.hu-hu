---
title: Az Azure IoT Hub üzenetek útválasztásának megismerése | Microsoft Docs
description: Fejlesztői útmutató – az üzenet-útválasztás használata az eszközről a felhőbe irányuló üzenetek küldéséhez. A telemetria és a nem telemetria adatok küldésére vonatkozó információkat tartalmaz.
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: asrastog
ms.openlocfilehash: d2d4d39cc7b330794094745851856365ef54b42f
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68828197"
---
# <a name="use-iot-hub-message-routing-to-send-device-to-cloud-messages-to-different-endpoints"></a>Eszközről a felhőbe irányuló üzenetek küldése különböző végpontokra IoT Hub üzenet-útválasztás használatával

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Az üzenet-útválasztás lehetővé teszi, hogy automatizált, méretezhető és megbízható módon küldjön üzeneteket az eszközeiről a Cloud Services szolgáltatásba. Az üzenet-útválasztás a következőhöz használható: 

* Az **eszköz telemetria üzeneteinek** , valamint az események, azaz az eszközök életciklusa eseményeinek és az eszközök kettős változási eseményeinek küldése a beépített végpontra és az egyéni végpontokra. További információ az [útválasztási végpontokról](#routing-endpoints).

* **Adatszűrés a különböző végpontokra való átirányításuk előtt** , Rich lekérdezések alkalmazásával. Az üzenet-útválasztás lehetővé teszi, hogy lekérdezze az üzenet tulajdonságait és az üzenet törzsét, valamint az eszköz Twin címkéit és az eszköz Twin tulajdonságait. További információ az [üzenet-útválasztásban található lekérdezések](iot-hub-devguide-routing-query-syntax.md)használatáról.

IoT Hub írási hozzáféréssel kell rendelkeznie ezekhez a szolgáltatási végpontokhoz az üzenet-útválasztás működéséhez. Ha a végpontokat a Azure Portalon keresztül konfigurálja, a rendszer hozzáadja a szükséges engedélyeket. Győződjön meg róla, hogy konfigurálja a szolgáltatásokat a várt átviteli sebesség támogatásához. Előfordulhat, hogy a IoT-megoldás első beállításakor figyelnie kell a további végpontokat, és el kell végeznie a szükséges módosításokat a tényleges terheléshez.

A IoT Hub a protokollok közötti együttműködés [általános formátumát](iot-hub-devguide-messages-construct.md) határozza meg az összes eszközről a felhőbe irányuló üzenetkezeléshez. Ha egy üzenet több olyan útvonalnak felel meg, amely ugyanarra a végpontra mutat, IoT Hub csak egyszer továbbítja az üzenetet erre a végpontra. Ezért nincs szükség a deduplikálás konfigurálására a Service Bus-várólistán vagy a témakörben. A particionált várólistákban a partíciós affinitás garantálja az üzenetek rendezését. Ebből az oktatóanyagból megtudhatja, hogyan konfigurálhatja az [üzenet](tutorial-routing.md)-útválasztást.

## <a name="routing-endpoints"></a>Útválasztási végpontok

Az IoT hub alapértelmezett beépített végpontja (**üzenetek/események**), amely kompatibilis a Event Hubsokkal. Létrehozhat [Egyéni végpontokat](iot-hub-devguide-endpoints.md#custom-endpoints) az üzenetek átirányításához az előfizetésben lévő egyéb szolgáltatások összekapcsolásával a IoT hub. A IoT Hub jelenleg a következő szolgáltatásokat támogatja egyéni végpontként:

### <a name="built-in-endpoint"></a>Beépített végpont

Az eszközről a felhőbe irányuló üzenetek fogadásához a beépített végpontról (**üzenetek/események**) a standard [Event Hubs Integration és SDK](iot-hub-devguide-messages-read-builtin.md) -k használhatók. Az útvonal létrehozása után az adatforgalom a beépített végpontra áramlik, kivéve, ha egy útvonal jön létre a végponthoz.

### <a name="azure-blob-storage"></a>Azure Blob Storage

IoT Hub támogatja az Azure Blob Storageba való adatírást az [Apache Avro](https://avro.apache.org/) formátumban, valamint JSON formátumban. A JSON formátum kódolásának lehetősége általánosan elérhető minden olyan régióban, ahol IoT Hub elérhető. Az alapértelmezett érték a AVRO. A kódolás formátuma csak akkor állítható be, ha a blob Storage-végpont konfigurálva van. Egy meglévő végpont formátuma nem szerkeszthető. JSON-kódolás használatakor a contentType JSON-ra és contentEncoding-ra kell beállítania UTF-8 értékre az üzenetrendszer [tulajdonságaiban](iot-hub-devguide-routing-query-syntax.md#system-properties). Ha a beállítás nincs megadva, akkor a IoT Hub a 64 kódolású formátumban fogja írni az üzeneteket. A kódolási formátumot kiválaszthatja a IoT Hub létrehozás vagy frissítés REST API, konkrétan a [RoutingStorageContainerProperties](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate#routingstoragecontainerproperties), a Azure Portal, az [Azure CLI](https://docs.microsoft.com/cli/azure/iot/hub/routing-endpoint?view=azure-cli-latest)vagy az [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.iothub/add-aziothubroutingendpoint?view=azps-1.3.0)használatával. Az alábbi ábrán látható, hogyan választható ki a kódolás formátuma a Azure Portalban.

![BLOB Storage-végpont kódolása](./media/iot-hub-devguide-messages-d2c/blobencoding.png)

A IoT Hub támogatja az útválasztási üzeneteket ADLS Gen2 fiókoknak is, amelyek a blob Storage-ra épülő [hierarchikus névtereket](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace)használó Storage-fiókok. Ez a funkció nyilvános előzetes verzióban érhető el, és az USA 2. nyugati régiójában és az USA nyugati középső régiójában található új ADLS Gen2 fiókok számára elérhető. Ezt a képességet hamarosan minden Felhőbeli régióra kivezetjük.

IoT Hub a kötegek üzeneteit, és az adatot egy blobba írja, amikor a köteg elér egy adott méretet, vagy egy adott időtartam eltelt. IoT Hub alapértelmezett értéke a következő fájl elnevezési konvenció:

```
{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}
```

Bármilyen fájl elnevezési konvenciót használhat, azonban az összes felsorolt tokent kell használnia. A IoT Hub egy üres blobba ír, ha nincs írási adatként.

A blob Storage-hoz való útválasztás esetén javasoljuk a Blobok bekapcsolását és az azokhoz való iterációt, hogy minden tárolót beolvasson a partíciós feltételezések elkészítése nélkül. A partíció tartománya esetleg változhat a [Microsoft által kezdeményezett feladatátvétel](iot-hub-ha-dr.md#microsoft-initiated-failover) vagy IoT hub [manuális feladatátvétel](iot-hub-ha-dr.md#manual-failover-preview)során. A Blobok listájának enumerálásához használhatja a Blobok listázása [API](https://docs.microsoft.com/rest/api/storageservices/list-blobs) -t. Tekintse át a következő mintát útmutatásként.

   ```csharp
        public void ListBlobsInContainer(string containerName, string iothub)
        {
            var storageAccount = CloudStorageAccount.Parse(this.blobConnectionString);
            var cloudBlobContainer = storageAccount.CreateCloudBlobClient().GetContainerReference(containerName);
            if (cloudBlobContainer.Exists())
            {
                var results = cloudBlobContainer.ListBlobs(prefix: $"{iothub}/");
                foreach (IListBlobItem item in results)
                {
                    Console.WriteLine(item.Uri);
                }
            }
        }
   ```

### <a name="service-bus-queues-and-service-bus-topics"></a>Service Bus várólisták és Service Bus témakörök

Service Bus várólisták és a IoT Hub végpontként használt témakörök nem rendelkezhetnek engedélyezett munkamenetekkel vagy **duplikált észleléssel** . Ha bármelyik beállítás engedélyezve van, a végpont nem **érhető el** a Azure Portalban.

### <a name="event-hubs"></a>Event Hubs

A beépített Event Hubs kompatibilis végponton kívül az adatok átirányítása Event Hubs típusú egyéni végpontokra is elvégezhető. 

## <a name="reading-data-that-has-been-routed"></a>Az átirányított adatolvasás

Ezt az oktatóanyagot követve egy útvonalat [](tutorial-routing.md)is beállíthat.

Az alábbi oktatóanyagok segítségével megtudhatja, hogyan olvashatja el a végpontok üzeneteit.

* Olvasás a [beépített végpontról](quickstart-send-telemetry-node.md)

* Olvasás a [blob Storage](../storage/blobs/storage-blob-event-quickstart.md) -ból

* Olvasás [Event Hubsról](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

* Olvasás [Service Bus várólistákból](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)

* Olvasás [Service Bus témakörökből](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions)

## <a name="fallback-route"></a>Tartalék útvonal

A tartalék útvonal minden olyan üzenetet elküld, amely nem felel meg a lekérdezési feltételeknek a meglévő útvonalakon a beépített Event Hubs (**üzenetek/események**) számára, amely kompatibilis a [Event Hubsokkal](/azure/event-hubs/). Ha az üzenet-útválasztás be van kapcsolva, engedélyezheti a tartalék útvonal funkciót. Az útvonal létrehozása után az adatforgalom a beépített végpontra áramlik, hacsak nem jön létre útvonal a végponthoz. Ha nincs elérhető útvonal a beépített végponthoz, és a tartalék útvonal engedélyezve van, csak az útvonalakon nem egyező üzeneteket küld a rendszer a beépített végpontnak. Továbbá, ha az összes meglévő útvonal törölve van, a tartalék útvonalnak engedélyezve kell lennie az összes, a beépített végponton tárolt érték fogadásához.

Engedélyezheti vagy letilthatja a tartalék útvonalat a Azure Portal-> üzenet-útválasztás panelen. A [FallbackRouteProperties](/rest/api/iothub/iothubresource/createorupdate#fallbackrouteproperties) Azure Resource Manager is használhat egyéni végpontot a tartalék útvonalhoz.

## <a name="non-telemetry-events"></a>Nem telemetria események

Az eszközök telemetria kívül az üzenet-útválasztás is lehetővé teszi az eszköz kettős módosítási eseményének és az eszköz életciklusa eseményeinek küldését. Ha például egy útvonal úgy jön létre, hogy az **eszköz kettős változási eseményre**van beállítva, akkor IoT hub üzeneteket küld a végpontnak, amely tartalmazza az eszköz kettős változását. Hasonlóképpen, ha egy útvonal az **eszköz életciklusára**beállított adatforrással jön létre, IoT hub üzenetet küld, amely jelzi, hogy az eszköz törölve lett vagy létrejött. 

A IoT Hub a Azure Event Grid-nal [is integrálva van](iot-hub-event-grid.md) az eszköz eseményeinek közzétételéhez, hogy támogassa a valós idejű integrációkat és a munkafolyamatok automatizálását ezen események alapján. Tekintse meg az [üzenet-útválasztás és a Event Grid közötti](iot-hub-event-grid-routing-comparison.md) fő különbségeket, amelyekből megtudhatja, melyik a legmegfelelőbb a forgatókönyvhöz.

## <a name="testing-routes"></a>Útvonalak tesztelése

Új útvonal létrehozásakor vagy egy meglévő útvonal szerkesztésekor az útvonal lekérdezését egy minta üzenettel kell tesztelni. Az egyes útvonalak tesztelését vagy az összes útvonal tesztelését egyszerre hajthatja végre, és a rendszer nem irányítja át az üzeneteket a végpontokhoz a teszt során. A Azure Portal, a Azure Resource Manager, a Azure PowerShell és az Azure parancssori felület használható teszteléshez. Az eredmények segítenek megállapítani, hogy a minta üzenet megfelel-e a lekérdezésnek, az üzenet nem felelt meg a lekérdezésnek, vagy a teszt nem sikerült, mert a minta üzenet vagy a lekérdezés szintaxisa helytelen. További információ: az [útvonal tesztelése](/rest/api/iothub/iothubresource/testroute) és az [összes útvonal tesztelése](/rest/api/iothub/iothubresource/testallroutes).

## <a name="latency"></a>Késés

Ha a beépített végpontok használatával irányítja az eszközről a felhőbe irányuló telemetria üzeneteket, az első útvonal létrehozása után kis mértékben megnő a végpontok közötti késés.

A legtöbb esetben a késés átlagos növekedése kevesebb, mint 500 MS. A késést a következő útválasztással figyelheti **: üzenetek/események** vagy **D2C. endpoints. látencia. beépített. events** IoT hub metrika. Az egyik útvonal létrehozása vagy törlése az első után nem befolyásolja a végpontok közötti késést.

## <a name="monitoring-and-troubleshooting"></a>Figyelés és hibaelhárítás

IoT Hub az útválasztáshoz és a végpontokhoz kapcsolódó metrikákat biztosít, hogy áttekintést nyújtson a hub állapotáról és az elküldött üzenetekről. Több mérőszámból is egyesítheti az információkat, így azonosíthatja a problémák alapvető okát. Használja például a metrika- **útválasztást: telemetria-üzenetek eldobott** vagy **D2C. telemetria. kimenő. eldobott** üzenet, amely meghatározza, hogy az egyes útvonalakon és a tartalék útvonalon lévő lekérdezések nem felelnek meg a letiltott üzenetek számának. [IoT hub mérőszámok](iot-hub-metrics.md) felsorolja az összes olyan metrikát, amely alapértelmezés szerint engedélyezve van a IoT hub számára.

A végpontok állapotának beolvasásához használja a [](iot-hub-devguide-endpoints.md#custom-endpoints) REST API a [végpont állapota](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) lehetőséget. Azt javasoljuk, hogy az útválasztási üzenet késéséhez kapcsolódó [IoT hub metrikák](iot-hub-metrics.md) használatával azonosítsa és hibakeresési hibákat, ha a végpont állapota meghalt vagy nem megfelelő. A végpont típusa Event Hubs esetében például figyelheti a **D2C. endpoints. késés. eventHubs**. A nem kifogástalan állapotú végpont állapota akkor frissül, ha a IoT Hub végül konzisztens állapotba került.

Az Azure Monitor [diagnosztikai beállításokban](../iot-hub/iot-hub-monitor-resource-health.md)található diagnosztikai naplók használatával nyomon követheti az útválasztási lekérdezések és a végpontok állapotának kiértékelése során felmerülő hibákat IoT hub, például ha egy végpont meghalt. Ezeket a diagnosztikai naplókat Azure Monitor naplókba, Event Hubsba vagy az Azure Storage-ba is elküldhetik egyéni feldolgozásra.

## <a name="next-steps"></a>További lépések

* Az üzenetküldési útvonalak létrehozásával kapcsolatos információkért lásd: [IoT hub eszközről a felhőbe irányuló üzenetek feldolgozása útvonalak használatával](tutorial-routing.md).

* [Az eszközről a felhőbe irányuló üzenetek küldése](quickstart-send-telemetry-node.md)

* Az eszközről a felhőbe irányuló üzenetek küldéséhez használható SDK-k információit az [Azure IoT SDK](iot-hub-devguide-sdks.md)-k című részben tekintheti meg.
