---
title: Támogatott erőforrástípusok a Azure Resource Healthon keresztül | Microsoft Docs
description: Támogatott erőforrástípusok az Azure Resource Health használatával
author: stephbaron
ms.author: stbaron
ms.topic: conceptual
ms.service: service-health
ms.date: 01/29/2019
ms.openlocfilehash: 1692e8acda88a51173eb0eabcd90c377b431a3c3
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/12/2019
ms.locfileid: "68955034"
---
# <a name="resource-types-and-health-checks-in-azure-resource-health"></a>Erőforrástípusok és állapot-ellenőrzések az Azure Resource Health-ben
Az alábbi lista az erőforrás-állapoton keresztül végrehajtott összes ellenőrzés teljes listáját tartalmazza.

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers
|Végrehajtott ellenőrzések|
|---|
|<ul><li>A kiszolgáló működik és fut?</li><li>Elfogyott a kiszolgáló memóriája?</li><li>Elindul a kiszolgáló?</li><li>A kiszolgáló helyreállítása?</li></ul>|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Működik az API Management szolgáltatás?</li></ul>|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Működik a Batch-fiókja?</li><li>Túllépte a készlet kvótáját ehhez a Batch-fiókhoz?</li></ul>|

## <a name="microsoftcacheredisredis"></a>Microsoft.CacheRedis/Redis
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Az összes gyorsítótár-csomópont fel van-e telepítve és fut?</li><li>Elérheti a gyorsítótárat az adatközponton belülről?</li><li>Elérte a gyorsítótárban a kapcsolatok maximális számát?</li><li> A gyorsítótár kimerítette a rendelkezésre álló memóriát? </li><li>A gyorsítótár nagy számú laphibát tapasztal?</li><li>A gyorsítótár nagy terhelés alatt van?</li></ul>|

## <a name="microsoftcdnprofile"></a>Microsoft.CDN/profile
|Végrehajtott ellenőrzések|
|---|
|<ul> <li>Elérhető-e a kiegészítő portál a CDN konfigurációs műveleteihez?</li><li>Folyamatban van a CDN-végpontokkal kapcsolatos kézbesítési probléma?</li><li>Módosíthatják a felhasználók a CDN-erőforrásaik konfigurációját?</li><li>A konfigurációs változások a várt sebességgel lesznek propagálva?</li><li>A felhasználók kezelhetik a CDN-konfigurációt a Azure Portal, a PowerShell vagy az API használatával?</li> </ul>|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.classiccompute/virtualmachines
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Működik a gazdagép kiszolgálója?</li><li>Befejeződött a gazda operációs rendszer indítása?</li><li>A virtuális gép tárolója kiépítve és felépítve van?</li><li>Van hálózati kapcsolat a gazdagép és a Storage-fiók között?</li><li>Befejeződött a vendég operációs rendszer indítása?</li><li>Folyamatban van a tervezett karbantartás?</li></ul>|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.cognitiveservices/accounts
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Elérheti a fiókot az adatközponton belülről?</li><li>Elérhető a Cognitive Services erőforrás-szolgáltató?</li><li>Elérhető-e a kognitív szolgáltatás a megfelelő régióban?</li><li>Elvégezhető az olvasási műveletek végrehajtása az erőforrás-metaadatokat tároló Storage-fiókon?</li><li>Elérte az API-hívási kvótát?</li><li>Elérte az API-hívás olvasási korlátját?</li></ul>|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.compute/virtualmachines
|Végrehajtott ellenőrzések|
|---|
|<ul><li>A virtuális gépet üzemeltető kiszolgáló működik?</li><li>Befejeződött a gazda operációs rendszer indítása?</li><li>A virtuális gép tárolója kiépítve és felépítve van?</li><li>Van hálózati kapcsolat a gazdagép és a Storage-fiók között?</li><li>Befejeződött a vendég operációs rendszer indítása?</li><li>Folyamatban van a tervezett karbantartás?</li></ul>|

## <a name="microsoftdatafactoryfactories"></a>Microsoft. DataFactory/gyárak
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Történtek a folyamat futási hibái?</li><li>A fürt a Data Factory kifogástalan állapotú?</li></ul>|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.datalakeanalytics/accounts
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Tapasztaltak-e a felhasználók a Data Lake Analytics feladatok elküldését vagy listázását?</li><li>A Data Lake Analytics feladatok a rendszerhibák miatt nem hajthatók végre?</li></ul>|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft. Data Lake Store/fiókok
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Problémák léptek fel a felhasználók számára az adatfeltöltés Data Lake Store?</li><li>Problémák léptek fel a felhasználóktól a Data Lake Store adatok letöltésekor?</li></ul>|

## <a name="microsoftdatamigrationservices"></a>Microsoft. datamigration/szolgáltatások
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Nem sikerült kiépíteni az adatbázis-áttelepítési szolgáltatást?</li><li>Az adatbázis-áttelepítési szolgáltatás inaktivitás vagy felhasználói kérelem miatt leállt?</li></ul>|

## <a name="microsoftdatashareaccounts"></a>Microsoft. DataShare/fiókok
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Az adatmegosztási fiók fel van-e telepítve és fut?</li><li>Elérhető-e az adatmegosztást tároló fürt?</li></ul>|

## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers
|Végrehajtott ellenőrzések|
|---|
|<ul><li>A kiszolgáló nem érhető el karbantartás miatt?</li><li>Nem érhető el a kiszolgáló az újrakonfigurálás miatt?</li></ul>|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers
|Végrehajtott ellenőrzések|
|---|
|<ul><li>A kiszolgáló nem érhető el karbantartás miatt?</li><li>Nem érhető el a kiszolgáló az újrakonfigurálás miatt?</li></ul>|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers
|Végrehajtott ellenőrzések|
|---|
|<ul><li>A kiszolgáló nem érhető el karbantartás miatt?</li><li>Nem érhető el a kiszolgáló az újrakonfigurálás miatt?</li></ul>|

## <a name="microsoftdevicesiothubs"></a>Microsoft.devices/iothubs
|Végrehajtott ellenőrzések|
|---|
|<ul><li>A IoT hub működik?</li></ul>|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.documentdb/databaseAccounts
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Van-e olyan adatbázis-vagy gyűjtési kérelem, amely egy Azure Cosmos DB szolgáltatás nem rendelkezésre állása miatt nem szolgált?</li><li>Van-e olyan dokumentumra vonatkozó kérelem, amely egy Azure Cosmos DB szolgáltatás nem rendelkezésre állása miatt nem szolgált?</li></ul>|

## <a name="microsofteventhubnamespaces"></a>Microsoft. eventhub/névterek
|Végrehajtott ellenőrzések|
|---|
|<ul><li>A Event Hubs névtér a felhasználó által generált hibákat észlelt?</li><li>Jelenleg folyamatban van a Event Hubs névtér frissítése?</li></ul>|

## <a name="microsofthdinsightclusters"></a>Microsoft.hdinsight/clusters
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Elérhetők-e a Core Services a HDInsight-fürtön?</li><li>A HDInsight-fürt hozzáfér a kulcshoz a BYOK-titkosításhoz a nyugalmi állapotban?</li></ul>|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Az Azure kulcstartó-platformmal kapcsolatos problémák miatt nem sikerül a Key vaultra irányuló kérés?</li><li>A Key Vault felé irányuló kérések szabályozva lettek, mivel az ügyfél túl sok kérést igényel?</li></ul>|

## <a name="microsoftmachinelearningwebservices"></a>Microsoft.MachineLearning/webServices
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Fut a webszolgáltatás?</li></ul>|

## <a name="microsoftmediamediaservices"></a>Microsoft. Media/Mediaservices
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Működik az adathordozó szolgáltatás?</li></ul>|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.network/applicationgateways
|Végrehajtott ellenőrzések|
|---|
|<ul><li>A Application Gateway teljesítménye romlik?</li><li>Elérhető a Application Gateway?</li></ul>|

## <a name="microsoftnetworkconnections"></a>Microsoft.network/connections
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Csatlakozik a VPN-alagút?</li><li>Vannak konfigurációs ütközések a kapcsolatban?</li><li>Megfelelően vannak-e konfigurálva az előmegosztott kulcsok?</li><li>Elérhető a helyszíni VPN-eszköz?</li><li>Eltérnek az IPSec/IKE biztonsági házirend?</li><li>A S2S VPN-kapcsolat megfelelően van kiépítve vagy hibás állapotban van?</li><li>Megfelelően van-e kiépítve vagy hibás állapotban a VNET-VNET-kapcsolatok?</li></ul>|

## <a name="microsoftnetworkexpressreoutecircuits"></a>Microsoft.network/expressreoutecircuits
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Kifogástalan a ExpressRoute áramkör?</li></ul>|

## <a name="microsoftnetworkfrontdoors"></a>Microsoft. Network/frontdoors
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Az állapotjelző háttérrendszer a hibákkal válaszol az állapot-mintavételi problémákra?</li><li>A konfigurációs változások késleltetve vannak?</li></ul>|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft. Network/LoadBalancers
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Elérhetőek-e a terheléselosztási végpontok?</li></ul>|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.network/virtualNetworkGateways
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Elérhető a VPN-átjáró az internetről?</li><li>A VPN Gateway készenléti állapotban van?</li><li>Fut a VPN-szolgáltatás az átjárón?</li></ul>|

## <a name="microsoftnotificationhubsnamespace"></a>Microsoft.NotificationHubs/namespace
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Futtathatók-e olyan futásidejű műveletek, mint a regisztráció, a telepítés vagy a Küldés a névtérben?</li></ul>|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft. operationalinsights/munkaterületek
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Vannak-e késések a munkaterületen?</li></ul>|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/Capacities
|Végrehajtott ellenőrzések|
|---|
|<ul><li>A kapacitás-erőforrás felépítése és futtatása folyamatban van?</li><li>Működik az összes számítási feladat?</li></ul>|

## <a name="microsoftpowerbiworkspacecollections"></a>Microsoft.PowerBI/workspaceCollections
|Végrehajtott ellenőrzések|
|---|
|<ul><li>A gazdagép operációs rendszere működik?</li><li>Elérhető a workspaceCollection az adatközponton kívülről?</li><li>Elérhető a Power BI erőforrás-szolgáltató?</li><li>Elérhető a Power BI szolgáltatás a megfelelő régióban?</li></ul>|

## <a name="microsoftsearchsearchservices"></a>Microsoft.search/searchServices
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Végezhető diagnosztikai műveletek a fürtön?</li></ul>|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Az ügyfelek a felhasználó által generált Service Bus hibákat észleltek?</li><li>A felhasználók a Service Bus névtér frissítése miatt átmeneti hibákat tapasztalnak?</li></ul>|

## <a name="microsoftsqlmanagedinstancesdatabases"></a>Microsoft. SQL/managedInstances/adatbázisok
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Az adatbázis működik és fut?</li></ul>|

## <a name="microsoftsqlserverdatabases"></a>Microsoft. SQL/Server/adatbázisok
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Van-e bejelentkezés az adatbázisba?</li></ul>|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Nem sikerül beolvasni az adatok beolvasását a Storage-fiókból az Azure Storage platformmal kapcsolatos problémák miatt?</li><li>Az Azure Storage platformmal kapcsolatos problémák miatt nem sikerül az adatbevitel a Storage-fiókba?</li><li>Az a tárolási fürt, ahol a Storage-fiók nem érhető el?</li></ul>|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Az összes gazdagép, ahol a feladatot végrehajtják és futtatják?</li><li>Nem sikerült elindítani a feladatot?</li><li>Folyamatban van a futtatókörnyezet frissítése?</li><li>A feladatoknak a várt állapotban kell lenniük (például az ügyfél által futtatott vagy leállított)?</li><li>Észlelte a feladatot a memórián kívüli kivételek?</li><li>Van-e folyamatban ütemezett számítási frissítés?</li><li>Elérhető a végrehajtás-kezelő (vezérlési terv)?</li></ul>|

## <a name="microsoftwebserverfarms"></a>Microsoft.web/serverFarms
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Működik a gazdagép kiszolgálója?</li><li>Internet Information Services fut?</li><li>Fut a terheléselosztó?</li><li>Elérhető a App Service-csomag az adatközponton belülről?</li><li>Elérhető-e a kiszolgálófarm tároló fiók a helyek tartalmában?</li></ul>|

## <a name="microsoftwebsites"></a>Microsoft.web/sites
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Működik a gazdagép kiszolgálója?</li><li>Fut az Internet Information Server?</li><li>Fut a terheléselosztó?</li><li>Elérheti a webalkalmazást az adatközponton belülről?</li><li>Elérhető-e a webhely tartalmát üzemeltető Storage-fiók?</li></ul>|

## <a name="next-steps"></a>További lépések
-  További információ: [Bevezetés a Azure Service Health irányítópultra](service-health-overview.md) és [a Azure Resource Health](resource-health-overview.md) bevezetésére. 
-  [Gyakori kérdések a Azure Resource Health](resource-health-faq.md)
- Riasztások beállítása, hogy értesítést kapjon az állapottal kapcsolatos problémákról. További információ: riasztások [konfigurálása a szolgáltatás állapotára vonatkozó eseményekhez](../azure-monitor/platform/alerts-activity-log-service-notifications.md). 
