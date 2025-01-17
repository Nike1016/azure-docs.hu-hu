---
title: Az Azure Service Fabric eseménylista |} A Microsoft Docs
description: Fürtök figyelése az Azure Service Fabric által biztosított események átfogó listája.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/25/2019
ms.author: srrengar
ms.openlocfilehash: cde0464985f756132c60453c4e79ffefd4a1dd2c
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2019
ms.locfileid: "65788598"
---
# <a name="list-of-service-fabric-events"></a>A Service Fabric-események listája 

A Service Fabric megjelenít egy elsődleges Fürtesemények tájékoztatja, mint a fürt állapotának [Service Fabric-események](service-fabric-diagnostics-events.md). Ezek a csomópontok és a fürt Service Fabric által végrehajtott műveletek vagy a fürt tulajdonosa vagy operátor által végzett kezelési döntést alapulnak. Ezek az események hozzáférhető konfigurálása a különféle módokon, például a konfigurálás [Azure Monitor naplózza a fürttel](service-fabric-diagnostics-oms-setup.md), vagy a lekérdezése a [EventStore](service-fabric-diagnostics-eventstore.md). Windows-gépeken ezeket az eseményeket az Eseménynapló - be vannak adatkéréseket így láthatja a Service Fabric-események az eseménynaplóban. 

Az alábbiakban néhány ezeket az eseményeket jellemzői
* Minden egyes esemény egy adott entitás a fürt van kötve. példa: alkalmazás, szolgáltatás, csomópontot, replika.
* Minden egyes esemény egy általános mezők készletét tartalmazza: EventInstanceId EventName és kategória.
* Minden esemény lehessen vonni az eseményt az entitást, melyhez hozzá van rendelve mezőket tartalmaz. Például a ApplicationCreated esemény kellene mezőket, amelyek azonosítják a létrehozott alkalmazás nevét.
* Események struktúrája úgy, hogy azok használhatók fel az eszközök ehhez számos további elemzés. Ezenkívül az esemény részleteinek vonatkozó hosszú karakterlánc helyett külön tulajdonságok vannak meghatározva. 
* Események által írt különböző alrendszereket, a Service Fabricben az alábbi Source(Task) azonosítja. További információ érhető el a alrendszereket [Service Fabric-architektúra](service-fabric-architecture.md) és [a Service Fabric technikai áttekintése](service-fabric-technical-overview.md).

Itt látható ezen entitás szerint vannak rendezve a Service Fabric-események listája.

## <a name="cluster-events"></a>Fürtesemények

**A fürt frissítési események**

További információt a fürtfrissítések [Itt](service-fabric-cluster-upgrade-windows-server.md).

| EventId | Name (Név) | Category | Leírás |Forrás (feladat) | Szint | 
| --- | --- | --- | --- | --- | --- | 
| 29627 | ClusterUpgradeStarted | Frissítés | Fürt frissítése megkezdődött. | CM | Tájékoztató |
| 29628 | ClusterUpgradeCompleted | Frissítés | Fürt frissítése befejeződött | CM | Tájékoztató | 
| 29629 | ClusterUpgradeRollbackStarted | Frissítés | Fürt frissítése megkezdődött a visszaállítás  | CM | Figyelmeztetés | 
| 29630 | ClusterUpgradeRollbackCompleted | Frissítés | Fürt frissítése befejeződött, visszaállítása | CM | Figyelmeztetés | 
| 29631 | ClusterUpgradeDomainCompleted | Frissítés | A frissítési tartomány véget ért, a frissítés során a fürt frissítése | CM | Tájékoztató | 

## <a name="node-events"></a>Csomópont-események

**Csomópont életciklussal kapcsolatos események** 

| EventId | Name (Név) | Category | Leírás |Forrás (feladat) | Szint |
| --- | --- | ---| --- | --- | --- | 
| 18602 | NodeDeactivateCompleted | StateTransition | A csomópont inaktiválása befejeződött | FM | Tájékoztató | 
| 18603 | NodeUp | StateTransition | A fürt észlelt egy csomópont van elindítva | FM | Tájékoztató | 
| 18604 | NodeDown | StateTransition | A fürt észlelt egy csomópont le lett állítva. Egy csomópont újraindítását során látni fogja a egy követett NodeUp esemény NodeDown esemény |  FM | Hiba | 
| 18605 | NodeAddedToCluster | StateTransition |  Új csomóponttal bővült a fürt és a Service Fabric ezt a csomópontot alkalmazásokat helyezhet üzembe. | FM | Tájékoztató | 
| 18606 | NodeRemovedFromCluster | StateTransition |  A csomópont el lett távolítva a fürtből. A Service Fabric már nem az ebben a csomópontban az alkalmazások központi telepítése | FM | Tájékoztató | 
| 18607 | NodeDeactivateStarted | StateTransition |  A csomópont inaktiválása megkezdődött | FM | Tájékoztató | 
| 25621 | NodeOpenSucceeded | StateTransition |  Egy csomópont sikeresen elindult | FabricNode | Tájékoztató | 
| 25622 | NodeOpenFailed | StateTransition |  Egy csomópont nem sikerült elindítani, és csatlakozzon a kör | FabricNode | Hiba | 
| 25624 | NodeClosed | StateTransition |  A csomópont leállítása sikeresen megtörtént | FabricNode | Tájékoztató | 
| 25626 | NodeAborted | StateTransition |  Egy csomópont ungracefully le lett állítva | FabricNode | Hiba | 

## <a name="application-events"></a>Alkalmazásesemények

**Alkalmazások életciklus-események**

| EventId | Name (Név) | Category | Leírás |Forrás (feladat) | Szint | 
| --- | --- | --- | --- | --- | --- | 
| 29620 | ApplicationCreated | Életciklus | Új alkalmazás létrehozása | CM | Tájékoztató | 
| 29625 | ApplicationDeleted | Életciklus | Egy meglévő alkalmazás törölve lett | CM | Tájékoztató | 
| 23083 | ApplicationProcessExited | Életciklus | Az alkalmazáson belül a folyamat kilépett. | Hosting | Tájékoztató | 

**Frissítési alkalmazásesemények**

További részleteket az alkalmazásfrissítések található [Itt](service-fabric-application-upgrade.md).

| EventId | Name (Név) | Category | Leírás |Forrás (feladat) | Szint | 
| --- | --- | ---| --- | --- | --- | 
| 29621 | ApplicationUpgradeStarted | Frissítés | Egy alkalmazás frissítése megkezdődött. | CM | Tájékoztató | 
| 29622 | ApplicationUpgradeCompleted | Frissítés | Egy alkalmazás frissítése befejeződött | CM | Tájékoztató | 
| 29623 | ApplicationUpgradeRollbackStarted | Frissítés | Egy alkalmazás frissítése megkezdődött a visszaállítás |CM | Figyelmeztetés | 
| 29624 | ApplicationUpgradeRollbackCompleted | Frissítés | Egy alkalmazás frissítése befejeződött, visszaállítása | CM | Figyelmeztetés | 
| 29626 | ApplicationUpgradeDomainCompleted | Frissítés | A frissítési tartomány véget ért a frissítés során egy alkalmazás frissítése | CM | Tájékoztató | 

## <a name="service-events"></a>Szolgáltatás-események

**Service-életciklusesemények**

| EventId | Name (Név) | Category | Leírás |Forrás (feladat) | Szint | 
| --- | --- | ---| --- | --- | --- |
| 18657 | ServiceCreated | Életciklus | Egy új szolgáltatás létrehozása | FM | Tájékoztató | 
| 18658 | ServiceDeleted | Életciklus | Egy meglévő szolgáltatás törlése | FM | Tájékoztató | 

## <a name="partition-events"></a>Partíció események

**Partíció áthelyezési események**

| EventId | Name (Név) | Category | Leírás |Forrás (feladat) | Szint | 
| --- | --- | ---| --- | --- | --- |
| 18940 | PartitionReconfigured | Életciklus | A partíció újrakonfigurálása befejeződött | RA | Tájékoztató | 

## <a name="replica-events"></a>Replika események

**Replika életciklussal kapcsolatos események**

| EventId | Name (Név) | Category | Leírás |Forrás (feladat) | Szint |
| --- | --- | ---| --- | --- | --- |
| 61701 | ReliableDictionaryOpened | Életciklus | Megbízható szótárban nyitotta meg | DistributedDictionary | Tájékoztató |
| 61702 | ReliableDictionaryClosed | Életciklus | Megbízható szótárban be van zárva. | DistributedDictionary | Tájékoztató |
| 61703 | ReliableDictionaryCheckpointRecovered | Életciklus | Megbízható szótárban helyreállt az ellenőrzőpont | DistributedDictionary | Tájékoztató |
| 61704 | ReliableDictionaryCheckpointFilesSent | Életciklus | Replika megbízható szótárban ellenőrzőpontfájlok küldött | DistributedDictionary | Tájékoztató |
| 61705 | ReliableDictionaryCheckpointFilesReceived | Életciklus | Replika megbízható szótárban ellenőrzőpontfájlok kapott. | DistributedDictionary | Tájékoztató |
| 61963 | ReliableQueueOpened | Életciklus | Megbízható várólista nyitotta meg | DistributedQueue | Tájékoztató |
| 61964 | ReliableQueueClosed | Életciklus | Megbízható várólista be van zárva. | DistributedQueue | Tájékoztató |
| 61965 | ReliableQueueCheckpointRecovered | Életciklus | Megbízható várólista helyreállt az ellenőrzőpont | DistributedQueue | Tájékoztató |
| 61966 | ReliableQueueCheckpointFilesSent | Életciklus | Replika megbízható várólista ellenőrzőpontfájlok küldött | DistributedQueue | Tájékoztató |
| 63647 | ReliableQueueCheckpointFilesReceived | Életciklus | Replika megbízható várólista ellenőrzőpontfájlok kapott. | DistributedQueue | Tájékoztató |
| 63648 | ReliableConcurrentQueueOpened | Életciklus | Megbízható egyidejű várólista nyitotta meg | ReliableConcurrentQueue | Tájékoztató |
| 63649 | ReliableConcurrentQueueClosed | Életciklus | Megbízható egyidejű várólista be van zárva. | ReliableConcurrentQueue | Tájékoztató |
| 63650 | ReliableConcurrentQueueCheckpointRecovered | Életciklus | Megbízható egyidejű várólista helyreállt az ellenőrzőpont | ReliableConcurrentQueue | Tájékoztató |
| 61687 | TStoreError | Hiba | Megbízható gyűjtemény váratlan hibaüzenetet kapott. | TStore | Hiba |
| 63831 | PrimaryFullCopyInitiated | Életciklus | Elsődleges replika kezdeményezte egy teljes másolatot | TReplicator | Tájékoztató |
| 63832 | PrimaryPartialCopyInitiated | Életciklus | Elsődleges replika kezdeményezte a részleges másolata | TReplicator | Tájékoztató |
| 16831 | BuildIdleReplicaStarted | Életciklus | Elsődleges replika inaktív replika készítése elindult | Replikáció | Tájékoztató |
| 16832 | BuildIdleReplicaCompleted | Életciklus | Elsődleges replika inaktív replika létrehozása befejeződött | Replikáció | Tájékoztató |
| 16833 | BuildIdleReplicaFailed | Életciklus | Elsődleges replika inaktív replika létrehozása nem sikerült | Replikáció | Figyelmeztetés |
| 16834 | PrimaryReplicationQueueFull | Állapot | Elsődleges replika replikációs sor megtelt | Replikáció | Figyelmeztetés |
| 16835 | PrimaryReplicationQueueWarning | Állapot | Elsődleges replika replikációs várólistán van közel teljes | Replikáció | Figyelmeztetés |
| 16836 | PrimaryReplicationQueueWarningMitigated | Állapot | Elsődleges replika replikációs várólistában lévő rendben. | Replikáció | Tájékoztató |
| 16837 | SecondaryReplicationQueueFull | Állapot | Másodlagos replika replikációs sor megtelt | Replikáció | Figyelmeztetés |
| 16838 | SecondaryReplicationQueueWarning | Állapot | Másodlagos replika replikációs várólistán van közel teljes | Replikáció | Figyelmeztetés |
| 16839 | SecondaryReplicationQueueWarningMitigated | Állapot | Másodlagos replika replikációs várólistában lévő rendben. | Replikáció | Tájékoztató |
| 16840 | PrimaryFaultedSlowSecondary | Állapot | Elsődleges replika lassú másodlagos replika hibába ütközött. | Replikáció | Figyelmeztetés |
| 16841 | ReplicatorFaulted | Állapot | Replika hibába ütközött. | Replikáció | Figyelmeztetés |

## <a name="container-events"></a>Tárolóesemények

**Tároló életciklus-események** 

| EventId | Name (Név) | Leírás |Forrás (feladat) | Szint | Version |
| --- | --- | ---| --- | --- | --- |
| 23074 | ContainerActivated | A tároló elindult | Hosting | Tájékoztató | 1 |
| 23075 | ContainerDeactivated | Egy tároló leállt | Hosting | Tájékoztató | 1 |
| 23082 | ContainerExited | Egy tároló kilépett – ellenőrizze a UnexpectedTermination jelző | Hosting | Tájékoztató | 1 |

## <a name="health-reports"></a>Rendszerállapot-jelentések

A [Service Fabric Állapotmodell](service-fabric-health-introduction.md) biztosít egy gazdag, rugalmas és bővíthető állapotának kiértékelését és a jelentéskészítés. A Service Fabric verziója 6.2-től kezdődően állapotadatok kiírt Platform az eseményeket az egészségügyi korábbi rekordjait tartalmazzák. Tartani egészségügyi események mennyisége alacsony, a következő, a Service Fabric-események csak írható:

* Az összes `Error` vagy `Warning` rendszerállapot-jelentések
* `Ok` rendszerállapot-jelentések átmenetek során
* Ha egy `Error` vagy `Warning` állapotesemény lejár. Ez határozza meg, hogy mennyi ideig egy entitás: nem megfelelő állapotú használható

**A fürt jelentés állapotesemények**

| EventId | Name (Név) | Leírás |Forrás (feladat) | Szint | Version |
| --- | --- | --- | --- | --- | --- |
| 54428 | ClusterNewHealthReport | Új fürt egészségügyi jelentés érhető el | HM | Tájékoztató | 1 |
| 54437 | ClusterHealthReportExpired | Meglévő fürt egészségügyi jelentés érvényessége lejárt | HM | Tájékoztató | 1 |

**Csomópont jelentés állapotesemények**

| EventId | Name (Név) | Leírás |Forrás (feladat) | Szint | Version |
| --- | --- | ---| --- | --- | --- |
| 54423 | NodeNewHealthReport | Egy új csomópont jelentés érhető el | HM | Tájékoztató | 1 |
| 54432 | NodeHealthReportExpired | Meglévő csomópont egészségügyi jelentés érvényessége lejárt | HM | Tájékoztató | 1 |

**Alkalmazásesemények egészségügyi jelentés**

| EventId | Name (Név) | Leírás |Forrás (feladat) | Szint | Version |
| --- | --- | ---| --- | --- | --- |
| 54425 | ApplicationNewHealthReport | Egy alkalmazás új jelentés létrehozása. Ez a nem telepített alkalmazásokat. | HM | Tájékoztató | 1 |
| 54426 | DeployedApplicationNewHealthReport | Egy üzembe helyezett alkalmazás új jelentés létrehozása | HM | Tájékoztató | 1 |
| 54427 | DeployedServicePackageNewHealthReport | Egy telepített szolgáltatást új állapotjelentés létrehozása | HM | Tájékoztató | 1 |
| 54434 | ApplicationHealthReportExpired | Meglévő alkalmazás egészségügyi jelentés érvényessége lejárt | HM | Tájékoztató | 1 |
| 54435 | DeployedApplicationHealthReportExpired | Egy meglévő üzembe helyezett alkalmazás egészségügyi jelentés érvényessége lejárt | HM | Tájékoztató | 1 |
| 54436 | DeployedServicePackageHealthReportExpired | Egy meglévő üzemelő szolgáltatás egészségügyi jelentés érvényessége lejárt | HM | Tájékoztató | 1 |

**A Szolgáltatásállapot-események jelentés**

| EventId | Name (Név) | Leírás |Forrás (feladat) | Szint | Version |
| --- | --- | ---| --- | --- | --- |
| 54424 | ServiceNewHealthReport | Service health új jelentés létrehozása | HM | Tájékoztató | 1 |
| 54433 | ServiceHealthReportExpired | Service health meglévő jelentés érvényessége lejárt | HM | Tájékoztató | 1 |

**Partíció jelentés állapotesemények**

| EventId | Name (Név) | Leírás |Forrás (feladat) | Szint | Version |
| --- | --- | ---| --- | --- | --- |
| 54422 | PartitionNewHealthReport | Új partíció egészségügyi jelentés létrehozása | HM | Tájékoztató | 1 |
| 54431 | PartitionHealthReportExpired | Meglévő partíció egészségügyi jelentés érvényessége lejárt | HM | Tájékoztató | 1 |

**Replika jelentés állapotesemények**

| EventId | Name (Név) | Leírás |Forrás (feladat) | Szint | Version |
| --- | --- | ---| --- | --- | --- |
| 54429 | StatefulReplicaNewHealthReport | Egy állapot-nyilvántartó replika egészségügyi jelentés létrehozása | HM | Tájékoztató | 1 |
| 54430 | StatelessInstanceNewHealthReport | Állapot nélküli példány állapotának új jelentés létrehozása | HM | Tájékoztató | 1 |
| 54438 | StatefulReplicaHealthReportExpired | Egy meglévő állapot-nyilvántartó replika egészségügyi jelentés érvényessége lejárt | HM | Tájékoztató | 1 |
| 54439 | StatelessInstanceHealthReportExpired | Egy meglévő állapotmentes példány egészségügyi jelentés érvényessége lejárt | HM | Tájékoztató | 1 |

## <a name="chaos-testing-events"></a>A Chaos tesztelési események 

**A Chaos munkamenet eseményei**

| EventId | Name (Név) | Leírás |Forrás (feladat) | Szint | Version |
| --- | --- | ---| --- | --- | --- |
| 50021 | ChaosStarted | A Chaos tesztelése a munkamenet megkezdése | Testability | Tájékoztató | 1 |
| 50023 | ChaosStopped | A Chaos tesztelési munkamenetet leállt | Testability | Tájékoztató | 1 |

**A Chaos csomópont események**

| EventId | Name (Név) | Leírás |Forrás (feladat) | Szint | Version |
| --- | --- | ---| --- | --- | --- |
| 50033 | ChaosNodeRestartScheduled | Egy csomópont újraindítását a Chaos tesztelés munkamenet részeként van ütemezve. | Testability | Tájékoztató | 1 |
| 50087 | ChaosNodeRestartCompleted | Csomópont újraindítása a Chaos tesztelés munkamenet részeként befejeződött | Testability | Tájékoztató | 1 |

**A Chaos alkalmazásesemények**

| EventId | Name (Név) | Leírás |Forrás (feladat) | Szint | Version |
| --- | --- | ---| --- | --- | --- |
| 50053 | ChaosCodePackageRestartScheduled | Kód csomag újraindításra van ütemezve egy káosz munkamenet tesztelése során | Testability | Tájékoztató | 1 |
| 50101 | ChaosCodePackageRestartCompleted | Kód csomag újraindítás befejeződött, a Chaos munkamenet tesztelése során | Testability | Tájékoztató | 1 |

**A Chaos partíció események**

| EventId | Name (Név) | Leírás |Forrás (feladat) | Szint | Version |
| --- | --- | ---| --- | --- | --- |
| 50069 | ChaosPartitionPrimaryMoveScheduled | Elsődleges partíció van ütemezve egy tesztelési munkamenetet káosz részeként áthelyezése | Testability | Tájékoztató | 1 |
| 50077 | ChaosPartitionSecondaryMoveScheduled | Egy másodlagos partíció van ütemezve egy tesztelési munkamenetet káosz részeként áthelyezése | Testability | Tájékoztató | 1 |
| 65003 | PartitionPrimaryMoveAnalysis | Az elsődleges partíció áthelyezési mélyebb elemzésre érhető el | Testability | Tájékoztató | 1 |

**A Chaos replika események**

| EventId | Name (Név) | Leírás |Forrás (feladat) | Szint | Version |
| --- | --- | ---| --- | --- | --- |
| 50047 | ChaosReplicaRestartScheduled | Replika újraindításra van ütemezve egy tesztelési munkamenetet káosz részeként | Testability | Tájékoztató | 1 |
| 50051 | ChaosReplicaRemovalScheduled | Egy replika eltávolítása a rendszer ütemezte a Chaos tesztelés munkamenet részeként | Testability | Tájékoztató | 1 |
| 50093 | ChaosReplicaRemovalCompleted | A Chaos tesztelés munkamenet részeként befejeződött egy replika eltávolítása | Testability | Tájékoztató | 1 |

## <a name="other-events"></a>Egyéb események

**Korrelációs események**

| EventId | Name (Név) | Leírás |Forrás (feladat) | Szint | Version |
| --- | --- | ---| --- | --- | --- |
| 65011 | CorrelationOperational | Korreláció észlelt | Testability | Tájékoztató | 1 |

## <a name="events-prior-to-version-62"></a>6\.2 verziónál régebbi események

A következő események 6.2 verzió előtt a Service Fabric által biztosított átfogó listája.

| EventId | Name (Név) | Forrás (feladat) | Szint |
| --- | --- | --- | --- |
| 25620 | NodeOpening | FabricNode | Tájékoztató |
| 25621 | NodeOpenedSuccess | FabricNode | Tájékoztató |
| 25622 | NodeOpenedFailed | FabricNode | Tájékoztató |
| 25623 | NodeClosing | FabricNode | Tájékoztató |
| 25624 | NodeClosed | FabricNode | Tájékoztató |
| 25625 | NodeAborting | FabricNode | Tájékoztató |
| 25626 | NodeAborted | FabricNode | Tájékoztató |
| 29627 | ClusterUpgradeStart | CM | Tájékoztató |
| 29628 | ClusterUpgradeComplete | CM | Tájékoztató |
| 29629 | ClusterUpgradeRollback | CM | Tájékoztató |
| 29630 | ClusterUpgradeRollbackComplete | CM | Tájékoztató |
| 29631 | ClusterUpgradeDomainComplete | CM | Tájékoztató |
| 23074 | ContainerActivated | Hosting | Tájékoztató |
| 23075 | ContainerDeactivated | Hosting | Tájékoztató |
| 29620 | ApplicationCreated | CM | Tájékoztató |
| 29621 | ApplicationUpgradeStart | CM | Tájékoztató |
| 29622 | ApplicationUpgradeComplete | CM | Tájékoztató |
| 29623 | ApplicationUpgradeRollback | CM | Tájékoztató |
| 29624 | ApplicationUpgradeRollbackComplete | CM | Tájékoztató |
| 29625 | ApplicationDeleted | CM | Tájékoztató |
| 29626 | ApplicationUpgradeDomainComplete | CM | Tájékoztató |
| 18566 | ServiceCreated | FM | Tájékoztató |
| 18567 | ServiceDeleted | FM | Tájékoztató |

## <a name="next-steps"></a>További lépések

* Áttekintheti a [Service Fabric-diagnosztika](service-fabric-diagnostics-overview.md)
* További információ az EventStore a [a Service Fabric az Eventstore áttekintése](service-fabric-diagnostics-eventstore.md)
* Módosítása a [Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md) konfigurációját, és további naplók összegyűjtése
* [Az Application Insights beállítása](service-fabric-diagnostics-event-analysis-appinsights.md) a műveleti naplók channel megtekintéséhez
