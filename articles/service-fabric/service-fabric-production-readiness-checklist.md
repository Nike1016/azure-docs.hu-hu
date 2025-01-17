---
title: Azure Service Fabric üzemi készültségi ellenőrzőlista | Microsoft Docs
description: Az ajánlott eljárások követésével megkezdheti Service Fabric alkalmazás és a fürt gyártását.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chakdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/05/2019
ms.author: atsenthi
ms.openlocfilehash: 9e86f7306ee70bee2e084b967867e2a9be5b66e1
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599353"
---
# <a name="production-readiness-checklist"></a>Termelési készenlét ellenőrzőlistája

Készen áll az alkalmazás és a fürt a termelési forgalom elvégzésére? Az alkalmazás és a fürt futtatása és tesztelése nem feltétlenül jelenti azt, hogy készen áll az éles környezetben való működésre. Az alkalmazás és a fürt zökkenőmentesen működik az alábbi ellenőrzőlista használatával. Erősen ajánlott az összes ilyen elemet kijelölni. Természetesen dönthet úgy is, hogy alternatív megoldásokat használ egy adott sorra (például saját diagnosztikai keretrendszerek).


## <a name="prerequisites-for-production"></a>Az éles környezet előfeltételei
1. Azure Service Fabric – ajánlott eljárások: [Alkalmazások tervezése](./service-fabric-best-practices-applications.md), [Biztonság](./service-fabric-best-practices-security.md), [hálózatkezelés](./service-fabric-best-practices-networking.md), [kapacitás megtervezése és skálázás](./service-fabric-best-practices-capacity-scaling.md), [infrastruktúra-kód](./service-fabric-best-practices-infrastructure-as-code.md), [monitorozás és diagnosztika](./service-fabric-best-practices-monitoring.md). 
1. A Reliable Actors biztonsági konfigurációjának implementálása a Actors programozási modell használata esetén
1. A több mint 20 magot vagy 10 csomópontot tartalmazó fürtök esetében hozzon létre egy dedikált elsődleges csomópont-típust a rendszerszolgáltatások számára. [Elhelyezési](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) megkötések hozzáadása a rendszerszolgáltatások elsődleges csomópont-típusának lefoglalásához.
1. Használjon D2v2 vagy magasabb SKU-t az elsődleges csomópont típusához. Ajánlott olyan SKU-t választani, amelynek legalább 50 GB-os merevlemez-kapacitása van.
1. Az üzemi fürtöknek [biztonságosnak](service-fabric-cluster-security.md)kell lenniük. Egy biztonságos fürt beállításával kapcsolatos példát ebben a [fürtözött sablonban](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/7-VM-Windows-3-NodeTypes-Secure-NSG)talál. Használjon köznapi neveket a tanúsítványokhoz, és ne használjon önaláírt tanúsítványokat.
1. [Erőforrás-korlátozásokat adhat hozzá a tárolók és szolgáltatások](service-fabric-resource-governance.md)számára, hogy ne használják a csomópont-erőforrások 75%-át. 
1. Ismerje meg és állítsa be a [tartóssági szintet](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster). Állapot-nyilvántartó számítási feladatokat futtató csomópont-típusok esetén ezüst vagy magasabb tartóssági szint ajánlott. Az elsődleges csomópont típusának az ezüst vagy a magasabb értéknek kell lennie.
1. Ismerje meg és válassza ki a csomópont típusának [megbízhatósági szintjét](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) . Az ezüst vagy a nagyobb megbízhatóság ajánlott.
1. A számítási feladatok terhelésének és méretezésének tesztelésével azonosíthatja a fürt [kapacitásának követelményeit](service-fabric-cluster-capacity.md) . 
1. A szolgáltatásait és alkalmazásait figyelik, és az alkalmazások naplóit a rendszer riasztással hozza létre és tárolja. Lásd például: [naplózás hozzáadása a Service Fabric alkalmazáshoz](service-fabric-how-to-diagnostics-log.md) és a [tárolók figyelése Azure monitor naplókkal](service-fabric-diagnostics-oms-containers.md).
1. A fürt figyelése riasztásokkal történik (például [Azure monitor naplók](service-fabric-diagnostics-event-analysis-oms.md)esetében). 
1. Az alapul szolgáló virtuálisgép-méretezési csoport infrastruktúrájának figyelése riasztásokkal történik (például [Azure monitor naplókkal](service-fabric-diagnostics-oms-agent.md).
1. A fürtben mindig van [elsődleges és másodlagos tanúsítvány](service-fabric-cluster-security-update-certs-azure.md) (így nem lesz kizárva).
1. Külön fürtöket kezelhet a fejlesztéshez, az előkészítéshez és a gyártáshoz. 
1. Az [alkalmazások frissítéseit](service-fabric-application-upgrade.md) és a [fürtök frissítéseit](service-fabric-tutorial-upgrade-cluster.md) először a fejlesztési és előkészítési fürtök tesztelik. 
1. Kapcsolja ki az automatikus frissítéseket az üzemi fürtökben, és kapcsolja be fejlesztési és előkészítési fürtök esetén (szükség esetén a visszaállítást). 
1. Hozzon létre egy helyreállítási időkorlátot (RPO) a szolgáltatáshoz, és állítson be egy vész- [helyreállítási folyamatot](service-fabric-disaster-recovery.md) , és tesztelje.
1. Tervezze [](service-fabric-cluster-scaling.md) meg manuálisan vagy programozott módon a fürt méretezését.
1. Tervezze [](service-fabric-patch-orchestration-application.md) meg a fürtcsomópontok javítását. 
1. Hozzon létre egy CI/CD-folyamatot, hogy a legújabb módosítások folyamatosan legyenek tesztelve. Például az [Azure DevOps](service-fabric-tutorial-deploy-app-with-cicd-vsts.md) vagy a [Jenkins](service-fabric-cicd-your-linux-applications-with-jenkins.md) használatával
1. Tesztelje a fejlesztési & átmeneti fürtöket a Load és a [fault Analysis Service szolgáltatásban](service-fabric-testability-overview.md) , és indukálja a szabályozott [káoszt](service-fabric-controlled-chaos.md). 
1. Tervezze [](service-fabric-concepts-scalability.md) meg az alkalmazások méretezését. 


Ha a Service Fabric Reliable Services vagy Reliable Actors programozási modellt használja, a következő elemeket kell kijelölnie:
1. Frissítse az alkalmazásokat a `RunAsync` helyi fejlesztés során annak ellenőrzéséhez, hogy a szolgáltatási kód tiszteletben tartja-e a lemondási tokent a metódusban, és zárja be az egyéni kommunikációs figyelőket.
1. A megbízható gyűjtemények használata esetén Kerülje a [gyakori buktatókat](service-fabric-work-with-reliable-collections.md) .
1. Figyelje a .NET CLR memória-teljesítményszámlálók terhelési tesztek futtatásakor, és ellenőrizze, hogy magas-e a szemetet vagy a Runaway heap növekedését.
1. [Reliable Services és Reliable Actors](service-fabric-reliable-services-backup-restore.md) offline biztonsági mentésének fenntartása és a visszaállítási folyamat tesztelése.
1. Az elsődleges NodeType virtuálisgép-példányok számának ideális esetben meg kell egyeznie a fürtök megbízhatósági szintjéhez szükséges minimális értékkel. a minimális szintet meghaladó feltételek a következők: átmenetileg, ha az elsődleges NodeTypes virtuálisgép-méretezési készlet SKU-jának vertikális skálázása.

## <a name="optional-best-practices"></a>Választható ajánlott eljárások

Míg a fenti lista az előfeltétel, hogy az éles környezetbe lépjen, a következő elemeket is figyelembe kell venni:
1. Csatlakoztassa a [Service Fabric Health modelt](service-fabric-health-introduction.md) a beépített egészségügyi Értékelés és jelentéskészítés kibővítéséhez.
1. Helyezzen üzembe egy egyéni watchdogot, amely figyeli az alkalmazást, és betölti az [erőforrás-kiegyensúlyozás](service-fabric-cluster-resource-manager-balancing.md) [terhelését](service-fabric-cluster-resource-manager-metrics.md) . 


## <a name="next-steps"></a>További lépések
* [Service Fabric Windows-fürt üzembe helyezése](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* [Service Fabric Linux-fürt üzembe helyezése](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* További információk a Service Fabric [alkalmazásainak élettartamáról](service-fabric-application-lifecycle.md).
