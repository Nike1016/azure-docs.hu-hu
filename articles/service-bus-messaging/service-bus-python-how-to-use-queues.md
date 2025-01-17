---
title: A Azure Service Bus Queues használata a Python használatával | Microsoft Docs
description: Ismerje meg, hogyan használhatók a Azure Service Bus Queues a Pythonból.
services: service-bus-messaging
documentationcenter: python
author: axisc
manager: timlt
editor: spelluru
ms.assetid: b95ee5cd-3b31-459c-a7f3-cf8bcf77858b
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 04/10/2019
ms.author: aschhab
ms.openlocfilehash: fa3aedf138564fedafe555adfbaf6c56efc1813e
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360838"
---
# <a name="how-to-use-service-bus-queues-with-python"></a>Service Bus Queues használata a Python használatával

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre Python-alkalmazásokat egy Service Bus üzenetsor üzeneteinek küldéséhez és fogadásához. 

## <a name="prerequisites"></a>Előfeltételek
1. Azure-előfizetés. Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. Aktiválhatja [MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) -előfizetői előnyeit, vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. [Service Bus üzenetsor létrehozásához kövesse az Azure Portal használata](service-bus-quickstart-portal.md) című témakör lépéseit.
    1. Olvassa el Service Bus **várólisták**gyors **áttekintését** . 
    2. Hozzon létre egy Service Bus névteret. 
    3. A **kapcsolatok karakterláncának**beolvasása. 

        > [!NOTE]
        > Az oktatóanyagban a  Python használatával hozzon létre egy várólistát a Service Bus névtérben. 
1. Telepítse a Pythont vagy a [python Azure Service Bus csomagot][Python Azure Service Bus package], lásd a [Python telepítési útmutatóját](../python-how-to-install.md). A Python SDK Service Bus teljes dokumentációja [itt](/python/api/overview/azure/servicebus?view=azure-python)található.

## <a name="create-a-queue"></a>Üzenetsor létrehozása
A **ServiceBusClient** objektum lehetővé teszi a várólistákkal való munkavégzést. Adja hozzá a következő kódot bármely olyan Python-fájlhoz, amelyben programozott módon szeretné elérni Service Bus:

```python
from azure.servicebus import ServiceBusClient
```

A következő kód létrehoz egy **ServiceBusClient** objektumot. Cserélje `<CONNECTION STRING>` le a karakterláncot a servicebus ConnectionString kifejezésre.

```python
sb_client = ServiceBusClient.from_connection_string('<CONNECTION STRING>')
```

Az SAS-kulcs nevének és értékének értékei a [Azure Portal][Azure portal] kapcsolódási adatai között, vagy a Visual Studio **Tulajdonságok** paneljén, a Service Bus névtér kiválasztásakor a Server Explorerben (az előző szakaszban látható módon) találhatók.

```python
sb_client.create_queue("taskqueue")
```

A `create_queue` metódus további beállításokat is támogat, amelyek segítségével felülbírálhatja az alapértelmezett várólista-beállításokat, például az üzenet élettartamát (TTL) vagy a várólista maximális méretét. A következő példa az üzenetsor maximális méretét 5 GB-ra állítja, az élettartam értéke pedig 1 perc:

```python
sb_client.create_queue("taskqueue", max_size_in_megabytes=5120,
                       default_message_time_to_live=datetime.timedelta(minutes=1))
```

További információ: [Azure Service Bus Python-dokumentáció](/python/api/overview/azure/servicebus?view=azure-python).

## <a name="send-messages-to-a-queue"></a>Üzenetek küldése egy üzenetsorba
Ha üzenetet szeretne küldeni egy Service Bus üzenetsor számára, az alkalmazás meghívja `send` a metódust `ServiceBusClient` az objektumon.

Az alábbi példa bemutatja, hogyan küldhet tesztüzenet üzenetet a nevű `taskqueue` várólistára a következő paranccsal: `send_queue_message`

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient
queue_client = QueueClient.from_connection_string(
    "<CONNECTION STRING>", "<QUEUE NAME>")

# Send a test message to the queue
msg = Message(b'Test Message')
queue_client.send(msg)
```

A Service Bus-üzenetsorok a [Standard csomagban](service-bus-premium-messaging.md) legfeljebb 256 KB, a [Prémium csomagban](service-bus-premium-messaging.md) legfeljebb 1 MB méretű üzeneteket támogatnak. A szabványos és az egyéni alkalmazástulajdonságokat tartalmazó fejléc mérete legfeljebb 64 KB lehet. Az üzenetsorban tárolt üzenetek száma korlátlan, az üzenetsor által tárolt üzenetek teljes mérete azonban korlátozva van. Az üzenetsor ezen méretét a létrehozáskor kell meghatározni, és a felső korlátja 5 GB. További információ a kvótákkal kapcsolatban: [Service Bus kvóták][Service Bus quotas].

További információ: [Azure Service Bus Python-dokumentáció](/python/api/overview/azure/servicebus?view=azure-python).

## <a name="receive-messages-from-a-queue"></a>Üzenetek fogadása egy várólistából
Az üzenetek egy várólistából érkeznek az `get_receiver` `ServiceBusService` objektum metódusának használatával:

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient
queue_client = QueueClient.from_connection_string(
    "<CONNECTION STRING>", "<QUEUE NAME>")

# Receive the message from the queue
with queue_client.get_receiver() as queue_receiver:
    messages = queue_receiver.fetch_next(timeout=3)
    for message in messages:
        print(message)
        message.complete()
```

További információ: [Azure Service Bus Python-dokumentáció](/python/api/overview/azure/servicebus?view=azure-python).


Az üzenetek törlődnek a várólistából, mert a paraméter `peek_lock` **hamis**értékre van állítva. A paraméter `peek_lock` **true**értékre állításával elolvashatja (betekintést) és zárolhatja az üzenetet anélkül, hogy törölné azt a sorból.

A fogadási művelet részeként az üzenet olvasásának és törlésének viselkedése a legegyszerűbb modell, és a legjobban olyan helyzetekben működik, amikor egy alkalmazás meghibásodás esetén nem dolgozza fel az üzenetet. Ennek megértéséhez képzeljen el egy forgatókönyvet, amelyben a fogyasztó kiad egy fogadási kérést, majd összeomlik a feldolgozása előtt. Mivel a Service Bus a rendszer felhasználja az üzenetet, amikor az alkalmazás újraindul, és újrakezdi az üzenetek felhasználását, az összeomlás előtt kimaradt az üzenetből.

Ha a `peek_lock` paraméter értéke TRUE ( **igaz**), a fogadás kétlépéses művelet lesz, ami lehetővé teszi az olyan alkalmazások támogatását, amelyek nem tudják elviselni a hiányzó üzeneteket. Amikor a Service Bus fogad egy kérést, megkeresi és zárolja a következő feldolgozandó üzenetet, hogy más fogyasztók ne tudják fogadni, majd visszaadja az alkalmazásnak. Miután az alkalmazás befejezte az üzenet feldolgozását (vagy megbízhatóként tárolja azt a későbbi feldolgozáshoz), a fogadási folyamat második szakaszát a **delete** metódus meghívásával végzi el az **üzenet** objektumon. A **delete** metódus felhasználja az üzenetet, és eltávolítja azt a várólistából.

```python
msg.delete()
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Az alkalmazás-összeomlások és nem olvasható üzenetek kezelése
A Service Bus olyan funkciókat biztosít, amelyekkel zökkenőmentesen helyreállíthatja az alkalmazás hibáit vagy az üzenetek feldolgozásának nehézségeit. Ha egy fogadó alkalmazás valamilyen okból nem tudja feldolgozni az üzenetet, akkor meghívhatja a **feloldási** metódust az **üzenet** objektumon. Ez azt eredményezi, hogy Service Bus a várólistán belüli üzenet zárolását, és elérhetővé teszi azt újra, akár egyazon alkalmazás, akár egy másik alkalmazás.

A várólistán lévő üzenethez is van egy időkorlát társítva, és ha az alkalmazás nem tudja feldolgozni az üzenetet a zárolási időkorlát lejárta előtt (például ha az alkalmazás összeomlik), akkor a Service Bus automatikusan feloldja az üzenetet, és elvégzi ismét elérhető.

Abban az esetben, ha az alkalmazás az üzenet feldolgozását követően összeomlik, de a **delete** metódus meghívása előtt, az üzenetet a rendszer az újraindításkor visszaküldi az alkalmazásnak. Ezt gyakran **legalább egyszer**kell meghívni, azaz minden üzenet feldolgozása legalább egyszer történik, de bizonyos helyzetekben előfordulhat, hogy ugyanazt az üzenetet újra lehet küldeni. Ha a forgatókönyvben nem lehetségesek a duplikált üzenetek, akkor az alkalmazásfejlesztőnek további logikát kell az alkalmazásba építenie az üzenetek ismételt kézbesítésének kezeléséhez. Ez gyakran az üzenet **MessageId** tulajdonságával érhető el, amely állandó marad a kézbesítési kísérletek során.

> [!NOTE]
> [Service Bus Explorerrel](https://github.com/paolosalvatori/ServiceBusExplorer/)kezelheti Service Bus erőforrásait. A Service Bus Explorer lehetővé teszi a felhasználók számára, hogy egy Service Bus névtérhez kapcsolódjanak, és egyszerű módon felügyelhetik az üzenetkezelési entitásokat. Az eszköz olyan speciális funkciókat biztosít, mint az importálási/exportálási funkció, illetve a témakör, a várólisták, az előfizetések, a Relay-szolgáltatások, az értesítési központok és az események hubok. 

## <a name="next-steps"></a>További lépések
Most, hogy megismerte Service Bus várólisták alapjait, további információért tekintse meg ezeket a cikkeket.

* [Queues, topics, and subscriptions][Queues, topics, and subscriptions] (Üzenetsorok, témakörök és előfizetések)

[Azure portal]: https://portal.azure.com
[Python Azure Service Bus package]: https://pypi.python.org/pypi/azure-servicebus  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Service Bus quotas]: service-bus-quotas.md

