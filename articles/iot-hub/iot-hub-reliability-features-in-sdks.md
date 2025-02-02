---
title: A kapcsolat és a megbízható üzenetkezelés kezelése az Azure IoT Hub Device SDK-k használatával
description: Ismerje meg, hogyan javíthatja az eszköz kapcsolatait és üzenetkezelését az Azure IoT Hub Device SDK-k használatakor
services: iot-hub
author: robinsh
ms.author: robinsh
ms.date: 07/07/2018
ms.topic: article
ms.service: iot-hub
ms.openlocfilehash: e881dffbd1f286047ffcff226eb3dede7a138a0c
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68884342"
---
# <a name="manage-connectivity-and-reliable-messaging-by-using-azure-iot-hub-device-sdks"></a>A kapcsolat és a megbízható üzenetkezelés kezelése az Azure IoT Hub Device SDK-k használatával

Ez a cikk magas szintű útmutatást nyújt a rugalmasabb eszköz-alkalmazások tervezéséhez. Bemutatja, hogyan veheti igénybe az Azure IoT Device SDK-k kapcsolati és megbízható üzenetkezelési funkcióit. Az útmutató célja, hogy segítséget nyújtson a következő forgatókönyvek kezelésében:

* Eldobott hálózati kapcsolatok javítása

* Váltás a különböző hálózati kapcsolatok között

* Újracsatlakoztatás a szolgáltatás átmeneti csatlakozási hibái miatt

A megvalósítás részletei a nyelvtől függően változhatnak. További információt az API dokumentációjában vagy az adott SDK-ban talál:

* [C/Python/iOS SDK](https://github.com/azure/azure-iot-sdk-c)

* [.NET SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/retrypolicy.md)

* [Java SDK](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)

* [Node SDK](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)

## <a name="designing-for-resiliency"></a>Rugalmasságot szem előtt tartó tervezés

A IoT-eszközök gyakran nem folytonos vagy instabil hálózati kapcsolatokon (például GSM vagy Satellite) alapulnak. Hibák léphetnek fel, ha az eszközök a szolgáltatás időszakos rendelkezésre állása és az infrastruktúra-vagy átmeneti hibák miatt működnek a felhőalapú szolgáltatásokkal. Egy eszközön futó alkalmazásnak kezelnie kell a kapcsolatok és az újrakapcsolódás mechanizmusait, valamint az üzenetek küldéséhez és fogadásához szükséges újrapróbálkozási logikát. Emellett az újrapróbálkozási stratégia követelményei nagy mértékben függenek az eszköz IoT forgatókönyvével, kontextusával, képességeivel.

Az Azure IoT Hub Device SDK-k célja, hogy egyszerűbbé váljon a felhőből az eszközre és az eszközről a felhőbe irányuló csatlakozás és a velük folytatott kommunikáció. Ezek az SDK-k robusztus módszert biztosítanak az Azure-IoT Hubhoz való kapcsolódásra, valamint az üzenetek küldésére és fogadására szolgáló átfogó lehetőségek létrehozására. A fejlesztők módosíthatják a meglévő implementációt is, hogy egy adott forgatókönyvhöz egy jobb újrapróbálkozási stratégiát szabjanak testre.

A kapcsolódást és a megbízható üzenetkezelést támogató SDK-funkciók az alábbi fejezetekben találhatók.

## <a name="connection-and-retry"></a>Kapcsolatok és újrapróbálkozás

Ez a szakasz áttekintést nyújt a kapcsolatok kezelésekor elérhető újrakapcsolódási és újrapróbálkozási mintákról. Részletes útmutatást nyújt a különböző újrapróbálkozási szabályzatok használatához az eszköz alkalmazásaiban, és felsorolja a kapcsolódó API-kat az eszköz SDK-kon.

### <a name="error-patterns"></a>Hiba mintázatai

A kapcsolatfelvételi hibák több szinten is megtörténhetnek:

* Hálózati hibák: leválasztott szoftvercsatorna-és névfeloldási hibák

* Protokoll szintű hibák HTTP-, AMQP-és MQTT-átvitel esetén: leválasztott hivatkozások vagy lejárt munkamenetek

* Helyi hibákból eredő alkalmazáshiba-hibák: érvénytelen hitelesítő adatok vagy szolgáltatási viselkedés (például a kvótát vagy a szabályozást túllépve)

Az eszköz SDK-k mindhárom szinten észlelnek hibákat. Az operációs rendszersel kapcsolatos hibákat és hardveres hibákat az eszköz SDK-k nem észlelik és kezelik. Az SDK-kialakítás a Azure Architecture Center [átmeneti hibák kezelésére](/azure/architecture/best-practices/transient-faults#general-guidelines) vonatkozó útmutatásán alapul.

### <a name="retry-patterns"></a>Újrapróbálkozási minták

A következő lépések leírják az újrapróbálkozási folyamatot a kapcsolódási hibák észlelésekor:

1. Az SDK észleli a hibát és a kapcsolódó hibát a hálózatban, a protokollban vagy az alkalmazásban.

2. Az SDK a hibakód használatával határozza meg a hiba típusát, és döntse el, hogy szükség van-e az újrapróbálkozásra.

3. Ha az SDK nem **helyreállítható hibát**azonosít, a rendszer leállítja az olyan műveleteket, mint a kapcsolatok, a Küldés és a fogadás. Az SDK értesíti a felhasználót. A helyreállíthatatlan hibákra például egy hitelesítési hiba és egy hibás végponti hiba tartozik.

4. Ha az SDK helyreállítható **hibát**azonosít, a megadott újrapróbálkozási házirendnek megfelelően újrapróbálkozik, amíg el nem telik a meghatározott időtúllépés.  Vegye figyelembe, hogy az SDK alapértelmezés szerint exponenciális visszalépést alkalmaz az **Jitter** újrapróbálkozási házirendjével.
5. Ha a megadott időkorlát lejár, az SDK nem próbálkozik a csatlakozással vagy a küldéssel. Értesíti a felhasználót.

6. Az SDK lehetővé teszi, hogy a felhasználó visszahívást csatoljon a kapcsolódási állapot változásainak fogadásához.

Az SDK-k három újrapróbálkozási szabályzatot biztosítanak:

* **Exponenciális visszaállítás a vibrálás**után: Ez az alapértelmezett újrapróbálkozási szabályzat általában agresszív, és az idő múlásával lelassul, amíg el nem éri a maximális késleltetést. A terv a [Azure Architecture Center](https://docs.microsoft.com/azure/architecture/best-practices/retry-service-specific)újrapróbálkozási útmutatásán alapul. 

* **Egyéni újrapróbálkozás**: Egyes SDK-nyelveken létrehozhat egy egyéni újrapróbálkozási szabályzatot, amely alkalmasabb a forgatókönyvhöz, majd beilleszti azt a RetryPolicy. Az egyéni újrapróbálkozás nem érhető el a C SDK-ban.

* **Nincs újrapróbálkozás**: Megadhatja az újrapróbálkozási szabályzatot "nincs újrapróbálkozás" értékre, ami letiltja az újrapróbálkozási logikát. Az SDK egyszer csatlakozni próbál, és egyszer üzenetet küld, feltéve, hogy a kapcsolat létrejött. Ezt a házirendet jellemzően a sávszélességgel vagy a költségeket érintő helyzetekben használják. Ha ezt a lehetőséget választja, a küldésre képtelen üzenetek elvesznek, és nem állíthatók helyre.

### <a name="retry-policy-apis"></a>Újrapróbálkozási házirend API-k

   | SDK | SetRetryPolicy metódus | Szabályzatok implementálása | Implementálási segédlet |
   |-----|----------------------|--|--|
   |  C/Python/iOS  | [IOTHUB_CLIENT_RESULT IoTHubClient_SetRetryPolicy](https://github.com/Azure/azure-iot-sdk-c/blob/2018-05-04/iothub_client/inc/iothub_client.h#L188)        | **Alapértelmezett**: [IOTHUB_CLIENT_RETRY_EXPONENTIAL_BACKOFF](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)<BR>**Egyéni:** elérhető [retryPolicy](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies) használata<BR>**Nincs újrapróbálkozás:** [IOTHUB_CLIENT_RETRY_NONE](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)  | [C/Python/iOS implementáció](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#)  |
   | Java| [SetRetryPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.deviceclientconfig.setretrypolicy?view=azure-java-stable)        | **Alapértelmezett**: [ExponentialBackoffWithJitter osztály](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)<BR>**Egyéni:** [RetryPolicy felület](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/RetryPolicy.java) implementálása<BR>**Nincs újrapróbálkozás:** [Nem újrapróbálkozási osztály](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)  | [Java-implementáció](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md) |
   | .NET| [DeviceClient.SetRetryPolicy](/dotnet/api/microsoft.azure.devices.client.deviceclient.setretrypolicy?view=azure-dotnet) | **Alapértelmezett**: [ExponentialBackoff osztály](/dotnet/api/microsoft.azure.devices.client.exponentialbackoff?view=azure-dotnet)<BR>**Egyéni:** [IRetryPolicy felület](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.iretrypolicy?view=azure-dotnet) implementálása<BR>**Nincs újrapróbálkozás:** [Nem újrapróbálkozási osztály](/dotnet/api/microsoft.azure.devices.client.noretry?view=azure-dotnet) | [C#végrehajtása](https://github.com/Azure/azure-iot-sdk-csharp) | |
   | Csomópont| [setRetryPolicy](/javascript/api/azure-iot-device/client?view=azure-iot-typescript-latest) | **Alapértelmezett**: [ExponentialBackoffWithJitter osztály](/javascript/api/azure-iot-common/exponentialbackoffwithjitter?view=azure-iot-typescript-latest)<BR>**Egyéni:** [RetryPolicy felület](/javascript/api/azure-iot-common/retrypolicy?view=azure-iot-typescript-latest) implementálása<BR>**Nincs újrapróbálkozás:** [Nem újrapróbálkozási osztály](/javascript/api/azure-iot-common/noretry?view=azure-iot-typescript-latest) | [Csomópont implementációja](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them) |

A következő mintakód szemlélteti ezt a folyamatot:

#### <a name="net-implementation-guidance"></a>.NET-implementációs útmutató

Az alábbi mintakód bemutatja, hogyan határozható meg és állítható be az alapértelmezett újrapróbálkozási szabályzat:

   ```csharp
   // define/set default retry policy
   IRetryPolicy retryPolicy = new ExponentialBackoff(int.MaxValue, TimeSpan.FromMilliseconds(100), TimeSpan.FromSeconds(10), TimeSpan.FromMilliseconds(100));
   SetRetryPolicy(retryPolicy);
   ```

A magas CPU-használat elkerülése érdekében az újrapróbálkozások szabályozva lesznek, ha a kód azonnal meghiúsul. Például, ha nincs hálózat vagy útvonal a célhelyre. A következő újrapróbálkozás végrehajtásának minimális ideje 1 másodperc.

Ha a szolgáltatás szabályozási hibával válaszol, az újrapróbálkozási szabályzat eltérő, és nyilvános API-n keresztül nem módosítható:

   ```csharp
   // throttled retry policy
   IRetryPolicy retryPolicy = new ExponentialBackoff(RetryCount, TimeSpan.FromSeconds(10), 
     TimeSpan.FromSeconds(60), TimeSpan.FromSeconds(5)); SetRetryPolicy(retryPolicy);
   ```

Az újrapróbálkozási mechanizmus `DefaultOperationTimeoutInMilliseconds`a következő után leáll, amely jelenleg 4 percre van beállítva.

#### <a name="other-languages-implementation-guidance"></a>Más nyelvek megvalósítási útmutatója

Más nyelveken használható kódok esetében tekintse át a következő implementációs dokumentumokat. Az adattár olyan mintákat tartalmaz, amelyek bemutatják az újrapróbálkozási házirend API-k használatát.

* [C/Python/iOS SDK](https://github.com/azure/azure-iot-sdk-c)

* [.NET SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/retrypolicy.md)

* [Java SDK](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)

* [Node SDK](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)

## <a name="next-steps"></a>További lépések

* [Eszköz- és szolgáltatásoldali SDK-k használata](./iot-hub-devguide-sdks.md)

* [A C IoT eszközoldali SDK-jának használata](./iot-hub-device-sdk-c-intro.md)

* [Fejlesztés korlátozott eszközökhöz](./iot-hub-devguide-develop-for-constrained-devices.md)

* [Fejlesztés mobileszközökhöz](./iot-hub-how-to-develop-for-mobile-devices.md)

* [Eszköz leválasztásának megoldása](iot-hub-troubleshoot-connectivity.md)
