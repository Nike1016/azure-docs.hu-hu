---
title: Azure ServiceFabric diagnosztika és monitorozás | Microsoft Docs
description: Ez a cikk a Service Fabric megbízható ServiceRemoting futtatókörnyezet (például az általa kibocsátott teljesítményszámlálók) Teljesítményfigyelő funkcióit ismerteti.
services: service-fabric
documentationcenter: .net
author: suchiagicha
manager: chackdan
editor: suchiagicha
ms.assetid: 1c229923-670a-4634-ad59-468ff781ad18
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2017
ms.author: chackdan
ms.openlocfilehash: 4e9aa2bbb99cac2ffc2b57ccb9299bf4ee7a729e
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876263"
---
# <a name="diagnostics-and-performance-monitoring-for-reliable-service-remoting"></a>Diagnosztika és Teljesítményfigyelés a megbízható szolgáltatás táveléréséhez
A megbízható ServiceRemoting futtatókörnyezet [teljesítményszámlálókat](https://msdn.microsoft.com/library/system.diagnostics.performancecounter.aspx)bocsát ki. Ezek betekintést nyújtanak a ServiceRemoting működéséhez, és segítenek a hibaelhárításban és a teljesítmény-figyelésben.


## <a name="performance-counters"></a>Teljesítményszámlálók
A megbízható ServiceRemoting-futtatókörnyezet a következő teljesítményszámláló-kategóriákat határozza meg:

| Category | Leírás |
| --- | --- |
| Service Fabric szolgáltatás |Az Azure Service Fabric szolgáltatás táveléréséhez kapcsolódó számlálók, például a kérelem feldolgozásához átlagosan szükséges idő |
| Service Fabric szolgáltatási módszer |Service Fabric távelérési szolgáltatás által megvalósított módszerekre jellemző számlálók, például a szolgáltatási módszer meghívásának gyakorisága |

Az előző kategóriák mindegyike egy vagy több számlálóval rendelkezik.

A Windows [Teljesítményfigyelő](https://technet.microsoft.com/library/cc749249.aspx) alkalmazás, amely alapértelmezés szerint elérhető a Windows operációs rendszerben, a teljesítményszámláló-adatok gyűjtésére és megtekintésére használható. [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md) egy másik lehetőség a teljesítményszámláló-adatok gyűjtésére és az Azure-táblákba való feltöltésére.

### <a name="performance-counter-instance-names"></a>Teljesítményszámláló-példányok nevei
A nagy mennyiségű ServiceRemoting-szolgáltatással vagy partícióval rendelkező fürtök nagy számú teljesítményszámláló-példánnyal rendelkeznek. A teljesítményszámláló-példányok nevei segíthetnek azonosítani a teljesítményszámláló-példányhoz társított adott partíciót és szolgáltatási módszert (ha van ilyen).

#### <a name="service-fabric-service-category"></a>Service Fabric szolgáltatáskategória
A kategória `Service Fabric Service`esetében a számláló példányainak neve a következő formátumú:

`ServiceFabricPartitionID_ServiceReplicaOrInstanceId_ServiceRuntimeInternalID`

A *ServiceFabricPartitionID* annak a Service Fabric partíció-azonosítónak a karakterlánc-ábrázolása, amelyhez a teljesítményszámláló-példány társítva van. A partíció-azonosító egy GUID, és a karakterlánc-ábrázolás a [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) "D" formátumú metódussal jön létre.

A *ServiceReplicaOrInstanceId* az a Service Fabric replika/példány-azonosító karakterlánc-ábrázolása, amelyhez a teljesítményszámláló-példány társítva van.

A *ServiceRuntimeInternalID* egy 64 bites egész szám karakterlánc-ábrázolása, amelyet a háló szolgáltatás futtatókörnyezete hoz létre belső használatra. Ez a teljesítményszámláló-példány neve része, amely biztosítja az egyediségét, és a teljesítményszámláló-példányok más neveivel való ütközés elkerülését. A felhasználók nem próbálják értelmezni a teljesítményszámláló-példány nevének ezen részét.

A következőkben egy példa látható egy számláló példányának nevére a `Service Fabric Service` kategóriához tartozó számlálóhoz:

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046_5008379932`

Az előző példában `2740af29-78aa-44bc-a20b-7e60fb783264` a Service Fabric partíció-azonosító karakterlánc-ábrázolása a replika/ `635650083799324046` InstanceId `5008379932` karakterlánc-ábrázolása, amely a futásidejű belső használatra generált 64 bites azonosító.

#### <a name="service-fabric-service-method-category"></a>Service Fabric szolgáltatási módszer kategóriája
A kategória `Service Fabric Service Method`esetében a számláló példányainak neve a következő formátumú:

`MethodName_ServiceRuntimeMethodId_ServiceFabricPartitionID_ServiceReplicaOrInstanceId_ServiceRuntimeInternalID`

A *MethodName* annak a szolgáltatási módszernek a neve, amelyhez a teljesítményszámláló-példány társítva van. A metódus nevének formátuma a háló szolgáltatás futtatókörnyezetének néhány logikája alapján van meghatározva, amely kiegyensúlyozza a név olvashatóságát a Windowson a teljesítményszámláló-példányok neveinek maximális hosszára vonatkozó korlátozásokkal.

A *ServiceRuntimeMethodId* egy 32 bites egész szám karakterlánc-ábrázolása, amelyet a háló szolgáltatás futtatókörnyezete hoz létre belső használatra. Ez a teljesítményszámláló-példány neve része, amely biztosítja az egyediségét, és a teljesítményszámláló-példányok más neveivel való ütközés elkerülését. A felhasználók nem próbálják értelmezni a teljesítményszámláló-példány nevének ezen részét.

A *ServiceFabricPartitionID* annak a Service Fabric partíció-azonosítónak a karakterlánc-ábrázolása, amelyhez a teljesítményszámláló-példány társítva van. A partíció-azonosító egy GUID, és a karakterlánc-ábrázolás a [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) "D" formátumú metódussal jön létre.

A *ServiceReplicaOrInstanceId* az a Service Fabric replika/példány-azonosító karakterlánc-ábrázolása, amelyhez a teljesítményszámláló-példány társítva van.

A *ServiceRuntimeInternalID* egy 64 bites egész szám karakterlánc-ábrázolása, amelyet a háló szolgáltatás futtatókörnyezete hoz létre belső használatra. Ez a teljesítményszámláló-példány neve része, amely biztosítja az egyediségét, és a teljesítményszámláló-példányok más neveivel való ütközés elkerülését. A felhasználók nem próbálják értelmezni a teljesítményszámláló-példány nevének ezen részét.

A következőkben egy példa látható egy számláló példányának nevére a `Service Fabric Service Method` kategóriához tartozó számlálóhoz:

`ivoicemailboxservice.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486_5008380`

Az előző példában `ivoicemailboxservice.leavemessageasync` a metódus `2` neve a `89383d32-e57e-4a9b-a6ad-57c6792aa521` futtatókörnyezet belső használatára generált 32 bites azonosító, a Service Fabric partíció-azonosító`635650083804480486` karakterlánc-ábrázolása, a a Service Fabric-replika/-példány `5008380` azonosítója, valamint a futásidejű belső használatra generált 64 bites azonosító.

## <a name="list-of-performance-counters"></a>Teljesítményszámlálók listája
### <a name="service-method-performance-counters"></a>Szolgáltatási módszer teljesítményszámlálói

A megbízható szolgáltatási futtatókörnyezet a szolgáltatási módszerek végrehajtásával kapcsolatos következő teljesítményszámlálók közzétételét teszi közzé.

| Kategória neve | Számláló neve | Leírás |
| --- | --- | --- |
| Service Fabric szolgáltatási módszer |Hívás/mp |A szolgáltatás metódusának meghívásakor meghívott idő másodpercenkénti száma |
| Service Fabric szolgáltatási módszer |Hívás átlagos száma ezredmásodpercben |A szolgáltatás metódusának végrehajtásához szükséges idő ezredmásodpercben |
| Service Fabric szolgáltatási módszer |Kivételek száma másodpercenként |Az a szám, ahányszor a szolgáltatás metódusa kivételt okozott másodpercenként |

### <a name="service-request-processing-performance-counters"></a>Szolgáltatási kérelmek feldolgozási teljesítményszámlálói
Ha egy ügyfél Service proxy-objektumon keresztül hív meg egy metódust, akkor a rendszer a hálózaton keresztül küldi el a távelérési szolgáltatásnak küldött kérési üzenetet. A szolgáltatás feldolgozza a kérelem üzenetét, és visszaküldi a választ az ügyfélnek. A megbízható ServiceRemoting-futtatókörnyezet a következő teljesítményszámlálók közzétételét teszi közzé a szolgáltatási kérelmek feldolgozásával kapcsolatban.

| Kategória neve | Számláló neve | Leírás |
| --- | --- | --- |
| Service Fabric szolgáltatás |függőben lévő kérelmek száma |A szolgáltatásban feldolgozott kérelmek száma |
| Service Fabric szolgáltatás |Kérelmek átlagos száma ezredmásodpercben |A szolgáltatás által a kérelem feldolgozásához szükséges idő (ezredmásodpercben) |
| Service Fabric szolgáltatás |Kérelem deszerializálásának átlagos ezredmásodperce |A szolgáltatási kérelem üzenetének deszerializálásához szükséges idő (ezredmásodpercben), amikor a szolgáltatás megkapja a szolgáltatást |
| Service Fabric szolgáltatás |Válasz szerializálásának átlagos ezredmásodperce |Igénybe vett idő (ezredmásodpercben) a szolgáltatás válaszüzenetének szerializálásához, mielőtt a rendszer elküldi a választ az ügyfélnek |

## <a name="next-steps"></a>További lépések
* [Mintakód](https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0)
* [EventSource-szolgáltatók a Perfview eszköz-ben](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)
