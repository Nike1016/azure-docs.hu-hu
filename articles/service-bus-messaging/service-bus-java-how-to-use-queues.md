---
title: Azure Service Bus Queues használata Javával
description: Ismerje meg, hogyan használhatók a Service Bus-üzenetsorok az Azure-ban. A Java nyelven írt példák.
services: service-bus-messaging
documentationcenter: java
author: axisc
manager: timlt
editor: spelluru
ms.assetid: f701439c-553e-402c-94a7-64400f997d59
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 04/10/2019
ms.author: aschhab
ms.custom: seo-java-july2019
ms.openlocfilehash: 4075ceae6c40a6be8ee2b9f6580a57fafb96e17e
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68663428"
---
# <a name="how-to-use-service-bus-queues-with-java"></a>A Service Bus Queues használata Javával
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]
Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre Java-alkalmazásokat a Service Bus üzenetsor üzeneteinek üzenetküldéséhez és fogadásához. 

> [!NOTE]
> A GitHubon található Java-mintákat az [Azure-Service-Bus adattárban](https://github.com/Azure/azure-service-bus/tree/master/samples/Java)találja.

## <a name="prerequisites"></a>Előfeltételek
1. Azure-előfizetés. Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. Aktiválhatja [MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) -előfizetői előnyeit, vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Ha nem rendelkezik várólistával, hogy működjön a szolgáltatással, a várólista létrehozásához kövesse az [Azure Portal használata Service Bus üzenetsor létrehozásához](service-bus-quickstart-portal.md) című cikket.
    1. Olvassa el Service Bus **várólisták**gyors **áttekintését** . 
    2. Hozzon létreegy Service Bus névteret. 
    3. A **kapcsolatok karakterláncának**beolvasása.
    4. Hozzon létre egy Service Bus **üzenetsor**.
3. Telepítse [a Javához készült Azure SDK][Azure SDK for Java]-t. 


## <a name="configure-your-application-to-use-service-bus"></a>Az alkalmazás konfigurálása Service Bus használatára
A minta létrehozása előtt győződjön meg arról, hogy telepítette a Javához készült [Azure SDK][Azure SDK for Java] -t. Ha az Eclipse-t használja, akkor telepítheti a Javához készült Azure SDK-t tartalmazó [Azure Toolkit for Eclipse][Azure Toolkit for Eclipse] . Ezután hozzáadhatja a **javához Microsoft Azure kódtárakat** a projekthez:

![Képernyőfelvétel a könyvtárak lapról, ahol könyvtárakat adhat hozzá](./media/service-bus-java-how-to-use-queues/eclipselibs.png)

Adja hozzá a `import` következő utasításokat a Java-fájl elejéhez:

```java
// Include the following imports to use Service Bus APIs
import com.google.gson.reflect.TypeToken;
import com.microsoft.azure.servicebus.*;
import com.microsoft.azure.servicebus.primitives.ConnectionStringBuilder;
import com.google.gson.Gson;

import static java.nio.charset.StandardCharsets.*;

import java.time.Duration;
import java.util.*;
import java.util.concurrent.*;

import org.apache.commons.cli.*;

```

## <a name="send-messages-to-a-queue"></a>Üzenetek küldése egy üzenetsorba
Ha üzeneteket szeretne küldeni egy Service Bus üzenetsor számára, az alkalmazás egy **QueueClient** objektumot hoz létre, és aszinkron módon küldi az üzeneteket. A következő kód bemutatja, hogyan küldhet üzenetet a portálon keresztül létrehozott üzenetsor számára.

```java
public void run() throws Exception {
    // Create a QueueClient instance and then asynchronously send messages.
    // Close the sender once the send operation is complete.
    QueueClient sendClient = new QueueClient(new ConnectionStringBuilder(ConnectionString, QueueName), ReceiveMode.PEEKLOCK);
    this.sendMessageAsync(sendClient).thenRunAsync(() -> sendClient.closeAsync());

    sendClient.close();
}

    CompletableFuture<Void> sendMessagesAsync(QueueClient sendClient) {
        List<HashMap<String, String>> data =
                GSON.fromJson(
                        "[" +
                                "{'name' = 'Einstein', 'firstName' = 'Albert'}," +
                                "{'name' = 'Heisenberg', 'firstName' = 'Werner'}," +
                                "{'name' = 'Curie', 'firstName' = 'Marie'}," +
                                "{'name' = 'Hawking', 'firstName' = 'Steven'}," +
                                "{'name' = 'Newton', 'firstName' = 'Isaac'}," +
                                "{'name' = 'Bohr', 'firstName' = 'Niels'}," +
                                "{'name' = 'Faraday', 'firstName' = 'Michael'}," +
                                "{'name' = 'Galilei', 'firstName' = 'Galileo'}," +
                                "{'name' = 'Kepler', 'firstName' = 'Johannes'}," +
                                "{'name' = 'Kopernikus', 'firstName' = 'Nikolaus'}" +
                                "]",
                        new TypeToken<List<HashMap<String, String>>>() {}.getType());

        List<CompletableFuture> tasks = new ArrayList<>();
        for (int i = 0; i < data.size(); i++) {
            final String messageId = Integer.toString(i);
            Message message = new Message(GSON.toJson(data.get(i), Map.class).getBytes(UTF_8));
            message.setContentType("application/json");
            message.setLabel("Scientist");
            message.setMessageId(messageId);
            message.setTimeToLive(Duration.ofMinutes(2));
            System.out.printf("\nMessage sending: Id = %s", message.getMessageId());
            tasks.add(
                    sendClient.sendAsync(message).thenRunAsync(() -> {
                        System.out.printf("\n\tMessage acknowledged: Id = %s", message.getMessageId());
                    }));
        }
        return CompletableFuture.allOf(tasks.toArray(new CompletableFuture<?>[tasks.size()]));
    }

```

Service Bus várólistákból küldött és fogadott üzenetek az [üzenet](/java/api/com.microsoft.azure.servicebus.message?view=azure-java-stable) osztály példányai. Az üzenet objektumai szabványos tulajdonságokkal rendelkeznek (például felirat és TimeToLive), az egyéni alkalmazásspecifikus tulajdonságok tárolására szolgáló szótár, valamint egy tetszőleges alkalmazásadatok törzse. Egy alkalmazás beállíthatja az üzenet törzsét úgy, hogy bármilyen szerializálható objektumot továbbít az üzenet konstruktorának, és a megfelelő szerializáló lesz használva az objektum szerializálásához. Azt is megteheti, hogy Java-t is biztosít **. IO. InputStream** objektum.


A Service Bus-üzenetsorok a [Standard csomagban](service-bus-premium-messaging.md) legfeljebb 256 KB, a [Prémium csomagban](service-bus-premium-messaging.md) legfeljebb 1 MB méretű üzeneteket támogatnak. A szabványos és az egyéni alkalmazástulajdonságokat tartalmazó fejléc mérete legfeljebb 64 KB lehet. Az üzenetsorban tárolt üzenetek száma korlátlan, az üzenetsor által tárolt üzenetek teljes mérete azonban korlátozva van. Az üzenetsor ezen méretét a létrehozáskor kell meghatározni, és a felső korlátja 5 GB.

## <a name="receive-messages-from-a-queue"></a>Üzenetek fogadása egy várólistából
Az üzenetek várólistából való fogadásának elsődleges módja egy **ServiceBusContract** objektum használata. A fogadott üzenetek két különböző módban működhetnek: **ReceiveAndDelete** és **PeekLock**.

A **ReceiveAndDelete** mód használatakor a fogadás egy egylépéses művelet – vagyis amikor a Service Bus olvasási kérést kap egy várólistában lévő üzenethez, az üzenetet felhasználja, és visszaadja az alkalmazásnak. A **ReceiveAndDelete** mód (amely az alapértelmezett mód) a legegyszerűbb modell, és a legjobban olyan helyzetekben működik, amikor egy alkalmazás meghibásodás esetén nem dolgozza fel az üzenetet. Ennek megértéséhez képzeljen el egy forgatókönyvet, amelyben a fogyasztó kiad egy fogadási kérést, majd összeomlik a feldolgozása előtt.
Mivel Service Bus az üzenetet felhasználva jelölte meg, akkor az alkalmazás újraindításakor és az üzenetek újbóli használatának megkezdése után a rendszer kihagyta az összeomlás előtt felhasznált üzenetet.

**PeekLock** módban a fogadás kétlépéses művelet lesz, ami lehetővé teszi olyan alkalmazások támogatását, amelyek nem tudják elviselni a hiányzó üzeneteket. Amikor a Service Bus fogad egy kérést, megkeresi és zárolja a következő feldolgozandó üzenetet, hogy más fogyasztók ne tudják fogadni, majd visszaadja az alkalmazásnak. Miután az alkalmazás befejezte az üzenet feldolgozását (vagy megbízhatóként tárolja azt a későbbi feldolgozáshoz), befejezi a fogadási folyamat második szakaszát a **delete** utasítás meghívásával a kapott üzeneten. Ha Service Bus látja a **törlési** hívást, a rendszer felhasználja az üzenetet, és eltávolítja azt a várólistából.

Az alábbi példa bemutatja, hogyan fogadhatók és dolgozhatók fel az üzenetek a **PeekLock** mód használatával (nem az alapértelmezett mód). Az alábbi példa egy végtelen hurkot tartalmaz, és az üzeneteket a következő módon `TestQueue`dolgozza fel:

```java
    public void run() throws Exception {
        // Create a QueueClient instance for receiving using the connection string builder
        // We set the receive mode to "PeekLock", meaning the message is delivered
        // under a lock and must be acknowledged ("completed") to be removed from the queue
        QueueClient receiveClient = new QueueClient(new ConnectionStringBuilder(ConnectionString, QueueName), ReceiveMode.PEEKLOCK);
        this.registerReceiver(receiveClient);

        // shut down receiver to close the receive loop
        receiveClient.close();
    }
    void registerReceiver(QueueClient queueClient) throws Exception {
        // register the RegisterMessageHandler callback
        queueClient.registerMessageHandler(new IMessageHandler() {
        // callback invoked when the message handler loop has obtained a message
            public CompletableFuture<Void> onMessageAsync(IMessage message) {
            // receives message is passed to callback
                if (message.getLabel() != null &&
                    message.getContentType() != null &&
                    message.getLabel().contentEquals("Scientist") &&
                    message.getContentType().contentEquals("application/json")) {

                        byte[] body = message.getBody();
                        Map scientist = GSON.fromJson(new String(body, UTF_8), Map.class);

                        System.out.printf(
                            "\n\t\t\t\tMessage received: \n\t\t\t\t\t\tMessageId = %s, \n\t\t\t\t\t\tSequenceNumber = %s, \n\t\t\t\t\t\tEnqueuedTimeUtc = %s," +
                            "\n\t\t\t\t\t\tExpiresAtUtc = %s, \n\t\t\t\t\t\tContentType = \"%s\",  \n\t\t\t\t\t\tContent: [ firstName = %s, name = %s ]\n",
                            message.getMessageId(),
                            message.getSequenceNumber(),
                            message.getEnqueuedTimeUtc(),
                            message.getExpiresAtUtc(),
                            message.getContentType(),
                            scientist != null ? scientist.get("firstName") : "",
                            scientist != null ? scientist.get("name") : "");
                    }
                    return CompletableFuture.completedFuture(null);
                }

                // callback invoked when the message handler has an exception to report
                public void notifyException(Throwable throwable, ExceptionPhase exceptionPhase) {
                    System.out.printf(exceptionPhase + "-" + throwable.getMessage());
                }
        },
        // 1 concurrent call, messages are auto-completed, auto-renew duration
        new MessageHandlerOptions(1, true, Duration.ofMinutes(1)));
    }

```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Az alkalmazás-összeomlások és nem olvasható üzenetek kezelése
A Service Bus olyan funkciókat biztosít, amelyekkel zökkenőmentesen helyreállíthatja az alkalmazás hibáit vagy az üzenetek feldolgozásának nehézségeit. Ha egy fogadó alkalmazás valamilyen okból nem tudja feldolgozni az üzenetet, akkor az a kapott üzenet **unlockMessage** metódusát hívja meg (a **deleteMessage** metódus helyett). Ennek hatására a Service Bus feloldja az üzenet zárolását az üzenetsoron belül, és lehetővé teszi az ugyanazon vagy egy másik fogyasztó alkalmazás általi ismételt fogadását.

A várólistán lévő üzenethez is van egy időkorlát társítva, és ha az alkalmazás nem tudja feldolgozni az üzenetet a zárolási időkorlát lejárta előtt (például ha az alkalmazás összeomlik), akkor a Service Bus automatikusan feloldja az üzenet zárolását, és lehetővé teszi ismét elérhető.

Abban az esetben, ha az alkalmazás az üzenet feldolgozását követően összeomlik, de az **deleteMessage** -kérelem kiadása előtt, akkor a rendszer az üzenetet az újraindításkor újra továbbítja az alkalmazásnak. Ezt a módszert gyakran *Legalább egyszeri feldolgozásnak* nevezik. Ez azt jelenti, hogy minden üzenet legalább egyszer fel lesz dolgozva, de bizonyos helyzetekben előfordulhat ugyanazon üzenet újbóli kézbesítése. Ha a forgatókönyvben nem lehetségesek a duplikált üzenetek, akkor az alkalmazásfejlesztőnek további logikát kell az alkalmazásba építenie az üzenetek ismételt kézbesítésének kezeléséhez. Ez gyakran az üzenet **getMessageId** metódusával érhető el, amely állandó marad a kézbesítési kísérletek között.

> [!NOTE]
> [Service Bus Explorerrel](https://github.com/paolosalvatori/ServiceBusExplorer/)kezelheti Service Bus erőforrásait. A Service Bus Explorer lehetővé teszi a felhasználók számára, hogy egy Service Bus névtérhez kapcsolódjanak, és egyszerű módon felügyelhetik az üzenetkezelési entitásokat. Az eszköz olyan speciális funkciókat biztosít, mint az importálási/exportálási funkció, illetve a témakör, a várólisták, az előfizetések, a Relay-szolgáltatások, az értesítési központok és az események hubok. 

## <a name="next-steps"></a>További lépések
Most, hogy megismerte Service Bus várólisták alapjait, további információt a [várólisták, témakörök és][Queues, topics, and subscriptions] előfizetések című témakörben talál.

További információ: [Java fejlesztői központ](https://azure.microsoft.com/develop/java/).

[Azure SDK for Java]: https://docs.microsoft.com/java/api/overview/azure/
[Azure Toolkit for Eclipse]: https://docs.microsoft.com/java/azure/eclipse/azure-toolkit-for-eclipse
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
