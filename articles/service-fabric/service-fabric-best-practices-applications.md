---
title: Az Azure Service Fabric Application design ajánlott eljárásai | Microsoft Docs
description: Ajánlott eljárások Service Fabric alkalmazások fejlesztéséhez.
services: service-fabric
documentationcenter: .net
author: markfussell
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/18/2019
ms.author: mfussell
ms.openlocfilehash: 06af1f4326e3f6a6dcb53c8710a126f43e2d2f6a
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875095"
---
# <a name="azure-service-fabric-application-design-best-practices"></a>Azure Service Fabric alkalmazás-tervezési ajánlott eljárások

Ez a cikk gyakorlati útmutatást nyújt az alkalmazások és szolgáltatások Azure Service Fabric történő létrehozásához.
 
## <a name="get-familiar-with-service-fabric"></a>Ismerkedjen meg Service Fabric
* Olvassa el [, hogy mit szeretne megtudni Service Fabric?](service-fabric-content-roadmap.md) cikkről.
* További információ a [Service Fabric alkalmazási forgatókönyvekről](service-fabric-application-scenarios.md).
* A programozási modell lehetőségeinek megismeréséhez olvassa el [Service Fabric programozási modell áttekintését](service-fabric-choose-framework.md).



## <a name="application-design-guidance"></a>Az alkalmazás tervezési útmutatója
Ismerkedjen meg Service Fabric alkalmazások [általános architektúrával](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric) és a [kialakítási szempontokkal](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#design-considerations).

### <a name="choose-an-api-gateway"></a>API-átjáró kiválasztása
Használjon olyan API Gateway szolgáltatást, amely a háttérrendszer-szolgáltatásokkal kommunikál. A leggyakrabban használt API Gateway-szolgáltatások a következők:

- [A Service Fabric integrált](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-deploy-api-management) [Azure API Management](https://docs.microsoft.com/azure/service-fabric/service-fabric-api-management-overview).
- [Azure IoT hub](https://docs.microsoft.com/azure/iot-hub/) vagy [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/), amely az Event hub-partíciók [ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/ServiceFabricProcessor) való olvasáshoz használatos.
- [Træfik fordított proxy](https://blogs.msdn.microsoft.com/azureservicefabric/2018/04/05/intelligent-routing-on-service-fabric-with-traefik/)az [Azure Service Fabric Provider](https://docs.traefik.io/configuration/backends/servicefabric/)használatával.
- [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/).

   > [!NOTE] 
   > Az Azure Application Gateway nincs közvetlenül integrálva a Service Fabric. Az Azure API Management általában az előnyben részesített választás.
- Saját, egyéni fejlesztésű [ASP.net Core](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-aspnetcore) webalkalmazás-átjáró.

### <a name="stateless-services"></a>Állapot nélküli szolgáltatások
Azt javasoljuk, hogy az állapot nélküli szolgáltatások létrehozásával mindig az Azure-adatbázisban, a Azure Cosmos DB-ban vagy az Azure Storage-ban tárolja az állapotot [Reliable Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction) és tárolással. A legtöbb fejlesztő számára a külsőleg kihelyezett állapot a megszokott módszer. Ez a megközelítés lehetővé teszi, hogy kihasználhassa a lekérdezési képességek előnyeit az áruházban.  

### <a name="when-to-use-stateful-services"></a>Mikor kell használni az állapot-nyilvántartó szolgáltatásokat
Vegye fontolóra az állapot-nyilvántartó szolgáltatásokat abban az esetben, ha alacsony késésre van szüksége, és az adatoknak a számításhoz közel kell tartaniuk. Néhány példa a IoT Digital Twin-eszközökre, a játék állapotára, a munkamenet-állapotra, az adatbázis gyorsítótárazási adataira, valamint a hosszan futó munkafolyamatokra, hogy nyomon követhesse a más szolgáltatásokhoz intézett hívásokat.

Döntse el az adatmegőrzési idő keretét:

- **Gyorsítótárazott**adathalmazok. A gyorsítótárazást akkor használja, ha a külső tárolók késése probléma. Használjon állapot-nyilvántartó szolgáltatást saját adatgyorsítótárként, vagy vegye fontolóra a [nyílt forráskódú SoCreate Service Fabric elosztott gyorsítótár](https://github.com/SoCreate/service-fabric-distributed-cache)használatát. Ebben az esetben nem kell foglalkoznia a gyorsítótárban lévő összes adat elvesztésével.
- **Időkorlátos adat**. Ebben az esetben a késéshez a számítási időszakhoz közel kell tartania az adatmennyiséget, de az adatok elvesznek a *katasztrófában*. Például sok IoT-megoldásban az adatoknak a számításokhoz kell megfelelniük, például az elmúlt néhány nap átlagos hőmérsékletének kiszámításakor, de ha ezek az adatok elvesznek, akkor a rögzített adatpontok nem fontosak. Emellett ebben a forgatókönyvben általában nem érdekli az egyes adatpontok biztonsági mentése. Csak a külső tárolóba rendszeresen írt számított átlagos értékeket kell biztonsági másolatot készíteni.  
- **Hosszú távú adatfeldolgozás**. A megbízható gyűjtemények folyamatosan tárolják az adataikat. Ebben az esetben azonban [elő kell készítenie a](https://docs.microsoft.com/azure/service-fabric/service-fabric-disaster-recovery)vész-helyreállítást, beleértve a fürtök [rendszeres biztonsági mentési házirendjeinek konfigurálását](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-configure-periodic-backup) is. Ezzel a beállítással megadhatja, hogy mi történjen, ha a fürt egy vészhelyzetben megsemmisül, ahol létre kell hoznia egy új fürtöt, és hogyan kell telepíteni az új alkalmazás-példányokat és a helyreállítást a legújabb biztonsági mentésből.

Költségek megtakarítása és a rendelkezésre állás javítása:
- Az állapot-nyilvántartó szolgáltatások segítségével csökkentheti a költségeket, mivel nem számít fel adathozzáférési és tranzakciós költségeket a távoli áruházból, és mivel nem kell más szolgáltatást használnia, például az Azure cache-t a Redis.
- Az állapot-nyilvántartó szolgáltatások használata elsősorban a tároláshoz, és nem a számítási feladatokhoz, hanem nem ajánlott. Tekintse át az állapot-nyilvántartó szolgáltatásokat az olcsó helyi tárolással.
- Más szolgáltatások függőségeinek eltávolításával javíthatja a szolgáltatás rendelkezésre állását. Ha az állapotot a fürttel együtt szeretné kezelni, elkülöníti Önt más szolgáltatás leállása vagy késési problémái miatt.

## <a name="how-to-work-with-reliable-services"></a>A Reliable Services használata
Service Fabric Reliable Services segítségével egyszerűen hozhat létre állapot nélküli és állapot-nyilvántartó szolgáltatásokat. További információ: [Bevezetés](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction)a Reliable Servicesba.
- Mindig tartsa tiszteletben a lemondási `RunAsync()` jogkivonatot az állapot nélküli és állapot- `ChangeRole()` nyilvántartó szolgáltatások esetében, valamint az állapot-nyilvántartó szolgáltatások módszerét. [](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-lifecycle#stateful-service-primary-swaps) Ha nem, Service Fabric nem tudja, hogy a szolgáltatás lezárható-e. Ha például nem tartja tiszteletben a lemondási tokent, sokkal több alkalmazás-frissítési idő merülhet fel.
-   A [kommunikációs figyelők](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication) megnyitása és lezárása időben, és a lemondási jogkivonatok tiszteletben tartása.
-   Soha ne keverje aszinkron kóddal a szinkronizálási kódot. Például ne használja `.GetAwaiter().GetResult()` az aszinkron hívásokat. A hívási veremben használjon aszinkron *módon* .

## <a name="how-to-work-with-reliable-actors"></a>A Reliable Actors használata
A Service Fabric Reliable Actors lehetővé teszi az állapot-nyilvántartó, virtuális szereplők egyszerű létrehozását. További információ: [Bevezetés](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction)a Reliable Actorsba.

- Komolyan fontolóra veheti a pub/sub üzenetküldést a szereplők között az alkalmazás méretezése érdekében. A szolgáltatást biztosító eszközök közé tartozik a [nyílt forráskódú SoCreate Service Fabric pub/sub](https://service-fabric-pub-sub.socreate.it/) és [Azure Service Bus](https://docs.microsoft.com/azure/service-bus/).
- A Actor állapotának lehető [](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices)legrészletesebbnek kell lennie.
- A [színész életciklusának](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices)kezelése. Ha nem fogja használni őket, törölje a szereplőket. A szükségtelen szereplők törlése különösen fontos, ha a [felejtő szolgáltatót](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#state-persistence-and-replication)használja, mert az összes állapotot a memóriában tárolja a rendszer.
- Az egymásra [épülő Egyidejűség](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction#concurrency)miatt a szereplők a legjobban független objektumokként használatosak. Ne hozzon létre több résztvevőből álló, szinkron metódusú hívásokat (amelyek mindegyike valószínűleg külön hálózati hívás lesz), vagy hozzon létre körkörös színészi kéréseket. Ezek jelentős hatással lesznek a teljesítményre és a skálázásra.
- Ne keverje aszinkron kóddal a szinkronizálási kódot. A teljesítménnyel kapcsolatos problémák elkerülése érdekében következetesen használja az aszinkron módon.
- Ne tegyen hosszan futó hívásokat a szereplőkkel. A hosszan futó hívások letiltják az ugyanahhoz a szereplőhöz tartozó más hívásokat a turn-based Egyidejűség miatt.
- Ha más szolgáltatásokkal kommunikál [Service Fabric a távoli](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-remoting) eljáráshívás `ServiceProxyFactory`használatával, és létrehozza a-t, hozza létre a gyárat a [Actor-Service](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-using) szinten, és *ne* a színész szintjén.


## <a name="application-diagnostics"></a>Application Diagnostics
Legyen alapos az [alkalmazások naplózásának](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-app) a szolgáltatási hívásokban való hozzáadásával kapcsolatban. Segít diagnosztizálni azokat a forgatókönyveket, amelyekben a szolgáltatások meghívja egymást. Ha például A "B" hívása C hívást hív meg, a hívás bárhol meghiúsulhat. Ha nem rendelkezik elegendő naplózással, a rendszer nehezen diagnosztizálja a hibákat. Ha a szolgáltatások a hívási kötetek miatt túl sokat jelentkeznek, ne felejtse el legalább a hibák és a figyelmeztetések naplózását.

## <a name="iot-and-messaging-applications"></a>IoT és üzenetkezelési alkalmazások
Amikor az [azure IoT hub](https://docs.microsoft.com/azure/iot-hub/) vagy az [Azure Event Hubsról](https://docs.microsoft.com/azure/event-hubs/)olvas üzeneteket, használja a [ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/ServiceFabricProcessor). A ServiceFabricProcessor integrálható Service Fabric Reliable Services, hogy megőrizze az Event hub-partíciók olvasásának állapotát, és az új üzeneteket leküldi a szolgáltatásoknak a `IEventProcessor::ProcessEventsAsync()` metódus segítségével.


## <a name="design-guidance-on-azure"></a>Tervezési útmutató az Azure-ban
* Látogasson el az [Azure architektúra](https://docs.microsoft.com/azure/architecture/microservices/) központba, ahol tervezési útmutatást talál az Azure-beli [szolgáltatások létrehozásához](https://docs.microsoft.com/azure/architecture/microservices/).

* Tekintse meg az [Azure for Gaming használatának első lépéseit](https://docs.microsoft.com/gaming/azure/) ismertető útmutatót a [Service Fabric in Gaming Servicesben](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf)való használatáról.
