---
title: Azure-előfizetési korlátok és kvóták
description: Az Azure-előfizetések és-szolgáltatások gyakori korlátainak, kvótáinak és megkötéseinek listáját jeleníti meg. Ez a cikk a korlátok növelésével és a maximális értékekkel kapcsolatos tudnivalókat tartalmazza.
services: multiple
author: rothja
manager: jeffreyg
tags: billing
ms.assetid: 60d848f9-ff26-496e-a5ec-ccf92ad7d125
ms.service: billing
ms.topic: article
ms.date: 05/30/2019
ms.author: byvinyal
ms.openlocfilehash: 7c4c80950e43f374b40085cb4e1c3e026e5f3abd
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68698277"
---
# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Az Azure-előfizetésekre és -szolgáltatásokra vonatkozó korlátozások, kvóták és megkötések
Ez a dokumentum felsorolja a leggyakoribb Microsoft Azure-korlátozásokat, amelyeket más néven kvótának is nevezünk. Ez a dokumentum jelenleg nem vonatkozik az összes Azure-szolgáltatásra. Az idő múlásával a listát kibontjuk és frissítik, hogy több szolgáltatásra is kiterjedjen.

További információ az Azure díjszabásáról: az [Azure díjszabásának áttekintése](https://azure.microsoft.com/pricing/). Itt megbecsülheti a költségeket a [díjszabási számológép](https://azure.microsoft.com/pricing/calculator/)használatával. Megtekintheti az adott szolgáltatás díjszabási adatait tartalmazó oldalt, például a [Windows rendszerű virtuális gépeket](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows). A költségek kezelésére szolgáló tippekért lásd: a [váratlan költségek megelőzése az Azure-számlázással és a költségek kezelésével](billing/billing-getting-started.md).

> [!NOTE]
> Ha az alapértelmezett korlátnál magasabb korlátot vagy kvótát szeretne növelni, [Nyisson meg egy online ügyfélszolgálati kérést díjmentesen](azure-resource-manager/resource-manager-quota-errors.md). A határértékek nem állíthatók fel az alábbi táblázatokban látható maximális határérték fölé. Ha nincs maximális korlát oszlop, az erőforrás nem rendelkezik állítható korlátokkal.
>
> Az [ingyenes próbaverziós](https://azure.microsoft.com/offers/ms-azr-0044p) előfizetések nem jogosultak a korlát vagy a kvóta növelésére. Ha rendelkezik egy [ingyenes próba-előfizetésre](https://azure.microsoft.com/offers/ms-azr-0044p), frissíthet egy [használatalapú](https://azure.microsoft.com/offers/ms-azr-0003p/) előfizetés. További információ: az [Azure ingyenes próbaverziós előfizetésének frissítése](billing/billing-upgrade-azure-subscription.md) utólagos elszámolású előfizetésre, valamint az [ingyenes próbaverziós előfizetés – gyakori kérdések](https://azure.microsoft.com/free/free-account-faq).
>

## <a name="limits-and-azure-resource-manager"></a>Korlátok és Azure Resource Manager
Most már lehetséges több Azure-erőforrást egyetlen Azure-erőforráscsoporthoz egyesíteni. Az erőforráscsoportok használatakor a korlátozásokat a rendszer a globálisan a Azure Resource Managertel rendelkező regionális szinten felügyeli. További információ az Azure-erőforráscsoportok használatáról: [Azure Resource Manager Overview (áttekintés](azure-resource-manager/resource-group-overview.md)).

A következő korlátok listájában az új táblázat a Azure Resource Manager használatakor felmerülő eltéréseket mutatja. Például van egy **előfizetési korlát** tábla és egy előfizetési **korlát – Azure Resource Manager** tábla. Ha a korlát mindkét forgatókönyvre vonatkozik, csak az első táblázatban látható. Hacsak másként nincs jelezve, a korlátok globálisak az összes régióban.

> [!NOTE]
> Az Azure-erőforráscsoportok erőforrásainak kvótái régiónként elérhetők az előfizetés, nem pedig előfizetések esetén, a Service Management kvótái. Vegyük példaként a vCPU-kvótákat. A vCPU támogatásával a kvóta növeléséhez el kell döntenie, hogy hány vCPU kíván használni a régiókban. Ezután egy konkrét kérést fog kérni az Azure-erőforráscsoport kvótáinak vCPU a kívánt mennyiségekhez és régiókhoz. Ha a Nyugat-Európában 30 vCPU kell használnia az alkalmazás futtatásához, akkor külön 30 vCPU-t kér Nyugat-Európában. A vCPU-kvóta nem növekszik más régiókban – csak Nyugat-Európában a 30 vCPU kvóta.
> <!-- -->
> Ennek eredményeképpen döntse el, hogy az Azure-erőforráscsoport kvótái milyen mennyiségű legyenek a számítási feladatokhoz egy adott régióban. Ezt követően adja meg ezt az összeget minden olyan régióban, amelybe telepíteni kívánja. Az aktuális kvóták meghatározott régiókban való meghatározásával kapcsolatos segítségért lásd: [üzembe helyezési problémák elhárítása](resource-manager-common-deployment-errors.md).
>
>

## <a name="service-specific-limits"></a>Szolgáltatás-specifikus korlátok
* [Active Directory](#active-directory-limits)
* [API Management](#api-management-limits)
* [APP SERVICE](#app-service-limits)
* [Application Gateway](#application-gateway-limits)
* [Automatizálás](#automation-limits)
* [Azure Cache for Redis](#azure-cache-for-redis-limits)
* [Azure Cloud Services](#azure-cloud-services-limits)
* [Azure Cognitive Services](#azure-cognitive-services-limits)
* [Azure Cosmos DB](#azure-cosmos-db-limits)
* [Azure Database for MySQL](#azure-database-for-mysql)
* [Azure Database for PostgreSQL](#azure-database-for-postgresql)
* [Azure DNS](#azure-dns-limits)
* [Azure Firewall](#azure-firewall-limits)
* [Azure Functions](#functions-limits)
* [Azure Kubernetes Service](#azure-kubernetes-service-limits)
* [Azure Machine Learning szolgáltatás](#azure-machine-learning-service-limits)
* [Azure Maps](#azure-maps-limits)
* [Azure Monitor](#azure-monitor-limits)
* [Azure Policy](#azure-policy-limits)
* [Azure Search](#azure-search-limits)
* [Azure Signaler szolgáltatás](#azure-signalr-service-limits)
* [Biztonsági mentés](#backup-limits)
* [Batch](#batch-limits)
* [BizTalk Services](#biztalk-services-limits)
* [Tárolópéldányok](#container-instances-limits)
* [Container Registry](#container-registry-limits)
* [Content Delivery Network](#content-delivery-network-limits)
* [Data Factory](#data-factory-limits)
* [Data Lake analitikai szolgáltatás](#data-lake-analytics-limits)
* [Data Lake Store](#data-lake-store-limits)
* [Database Migration Service](#database-migration-service-limits)
* [Event Grid](#event-grid-limits)
* [Event Hubs](#event-hubs-limits)
* [Bejárati ajtó szolgáltatás](#azure-front-door-service-limits)
* [Identity Manager](#identity-manager-limits)
* [IoT Hub](#iot-hub-limits)
* [IoT Hub eszközregisztrációs szolgáltatás](#iot-hub-device-provisioning-service-limits)
* [Key Vault](#key-vault-limits)
* [Médiaszolgáltatások](#media-services-limits)
* [Mobile Services](#mobile-services-limits)
* [Többtényezős hitelesítés](#multi-factor-authentication-limits)
* [Hálózat](#networking-limits)
  * [Application Gateway](#application-gateway-limits)
  * [Azure DNS](#azure-dns-limits)
  * [Azure bejárati ajtó szolgáltatás](#azure-front-door-service-limits)
  * [Azure Firewall](#azure-firewall-limits)
  * [ExpressRoute](#expressroute-limits)
  * [Load Balancer](#load-balancer)
  * [Nyilvános IP-cím](#publicip-address)
  * [Network Watcher](#network-watcher-limits)
  * [Traffic Manager](#traffic-manager-limits)
  * [Virtual Network](#networking-limits)
* [Értesítési központ](#notification-hubs-limits)
* [Erőforráscsoport](#resource-group-limits)
* [Szerepköralapú hozzáférés-vezérlés](#role-based-access-control-limits)
* [Scheduler](#scheduler-limits)
* [Szolgáltatásbusz](#service-bus-limits)
* [Site Recovery](#site-recovery-limits)
* [SQL Database](#sql-database-limits)
* [SQL Data Warehouse](#sql-data-warehouse-limits)
* [Storage](#storage-limits)
* [StorSimple-rendszeren](#storsimple-system-limits)
* [Stream Analytics](#stream-analytics-limits)
* [Előfizetés](#subscription-limits)
* [Virtuális gépek](#virtual-machines-limits)
* [Virtuálisgép-méretezési csoportok](#virtual-machine-scale-sets-limits)

### <a name="subscription-limits"></a>Előfizetés korlátai
#### <a name="subscription-limits---azure-service-management-classic-deployment-model"></a>Előfizetés korlátai – Azure Service Management (klasszikus üzembehelyezési modell)
[!INCLUDE [azure-subscription-limits](../includes/azure-subscription-limits.md)]

#### <a name="subscription-limits---azure-resource-manager"></a>Előfizetési korlátok – Azure Resource Manager
Azure Resource Manager és Azure-erőforráscsoportok használatakor a következő korlátozások érvényesek. A Azure Resource Manager nem módosított korlátok nincsenek felsorolva. Ezeket a korlátokat az előző táblázatban tekintheti meg.

További információ a Resource Manager API olvasási és írási korlátairól: a [Resource Manager-kérelmek szabályozása](resource-manager-request-limits.md).

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="resource-group-limits"></a>Erőforráscsoport korlátai
[!INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]

### <a name="virtual-machines-limits"></a>Virtual Machines korlátok
#### <a name="virtual-machines-limits"></a>Virtual Machines korlátok
[!INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]

#### <a name="virtual-machines-limits---azure-resource-manager"></a>Virtual Machines korlátok – Azure Resource Manager
Azure Resource Manager és Azure-erőforráscsoportok használatakor a következő korlátozások érvényesek. A Azure Resource Manager nem módosított korlátok nincsenek felsorolva. Ezeket a korlátokat az előző táblázatban tekintheti meg.

[!INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

#### <a name="shared-image-gallery-limits"></a>Megosztott képgyűjtemény korlátai

Az erőforrások megosztott képtárakkal való üzembe helyezéséhez korlátok, előfizetések vonatkoznak:
- 100 megosztott képtárak, előfizetések száma régiónként
- 1 000 képdefiníciók, előfizetések régiónként
- 10 000 képverzió, előfizetések száma régiónként

### <a name="virtual-machine-scale-sets-limits"></a>A virtuálisgép-méretezési készletek korlátai
[!INCLUDE [virtual-machine-scale-sets-limits](../includes/azure-virtual-machine-scale-sets-limits.md)]

### <a name="container-instances-limits"></a>Container Instances korlátok
[!INCLUDE [container-instances-limits](../includes/container-instances-limits.md)]

### <a name="container-registry-limits"></a>Container Registry korlátok
Az alábbi táblázat az alapszintű, standard és prémium [szintű szolgáltatási szintek](./container-registry/container-registry-skus.md)funkcióit és korlátait részletezi.

[!INCLUDE [container-registry-limits](../includes/container-registry-limits.md)]

### <a name="azure-kubernetes-service-limits"></a>Az Azure Kubernetes szolgáltatás korlátai
[!INCLUDE [container-service-limits](../includes/container-service-limits.md)]

### <a name="azure-machine-learning-service-limits"></a>Azure Machine Learning szolgáltatási korlátok
Azure Machine Learning számítási kvóták legújabb értékei a [Azure Machine learning kvóta lapon](../articles/machine-learning/service/how-to-manage-quotas.md) találhatók.

### <a name="networking-limits"></a>Hálózatkezelési korlátok
[!INCLUDE [azure-virtual-network-limits](../includes/azure-virtual-network-limits.md)]

#### <a name="expressroute-limits"></a>ExpressRoute korlátok
[!INCLUDE [expressroute-limits](../includes/expressroute-limits.md)]

#### <a name="application-gateway-limits"></a>Application Gateway korlátok

A következő táblázat a v1, v2, standard és WAF SKU-ra vonatkozik, hacsak másként nincs megadva.
[!INCLUDE [application-gateway-limits](../includes/application-gateway-limits.md)]

#### <a name="network-watcher-limits"></a>Network Watcher korlátok
[!INCLUDE [network-watcher-limits](../includes/network-watcher-limits.md)]

#### <a name="traffic-manager-limits"></a>Traffic Manager korlátok
[!INCLUDE [traffic-manager-limits](../includes/traffic-manager-limits.md)]

#### <a name="azure-dns-limits"></a>Azure DNS korlátok
[!INCLUDE [dns-limits](../includes/dns-limits.md)]

#### <a name="azure-firewall-limits"></a>Azure Firewall korlátok
[!INCLUDE [azure-firewall-limits](../includes/firewall-limits.md)]

#### <a name="azure-front-door-service-limits"></a>Azure bejárati ajtó szolgáltatás korlátai
[!INCLUDE [azure-front-door-service-limits](../includes/front-door-limits.md)]

### <a name="storage-limits"></a>Tárolási korlátok
<!--like # storage accts -->
[!INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

A Storage-fiók korlátaival kapcsolatos további információkért lásd: az [Azure Storage skálázhatósági és teljesítménybeli céljai](storage/common/storage-scalability-targets.md).

#### <a name="storage-resource-provider-limits"></a>Tárolási erőforrás-szolgáltatói korlátok

[!INCLUDE [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]

#### <a name="azure-blob-storage-limits"></a>Azure Blob Storage-korlátok
[!INCLUDE [storage-blob-scale-targets](../includes/storage-blob-scale-targets.md)]

#### <a name="azure-files-limits"></a>Azure Files korlátok
Azure Files korlátozásokkal kapcsolatos további információkért lásd: [Azure Files skálázhatósági és teljesítményi célok](storage/files/storage-files-scale-targets.md).

[!INCLUDE [storage-files-scale-targets](../includes/storage-files-scale-targets.md)]

#### <a name="azure-file-sync-limits"></a>Azure File Sync korlátok
[!INCLUDE [storage-sync-files-scale-targets](../includes/storage-sync-files-scale-targets.md)]

#### <a name="azure-queue-storage-limits"></a>Az Azure üzenetsor tárterületének korlátai
[!INCLUDE [storage-queues-scale-targets](../includes/storage-queues-scale-targets.md)]

#### <a name="azure-table-storage-limits"></a>Azure Table Storage-korlátok
[!INCLUDE [storage-tables-scale-targets](../includes/storage-tables-scale-targets.md)]

<!-- conceptual info about disk limits -- applies to unmanaged and managed -->
#### <a name="virtual-machine-disk-limits"></a>A virtuális gép lemezének korlátai
[!INCLUDE [azure-storage-limits-vm-disks](../includes/azure-storage-limits-vm-disks.md)]

További információ: [virtuális gépek méretei](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

#### <a name="managed-virtual-machine-disks"></a>Felügyelt virtuális gépek lemezei

[!INCLUDE [azure-storage-limits-vm-disks-managed](../includes/azure-storage-limits-vm-disks-managed.md)]

#### <a name="unmanaged-virtual-machine-disks"></a>Nem felügyelt virtuálisgép-lemezek

[!INCLUDE [azure-storage-limits-vm-disks-standard](../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../includes/azure-storage-limits-vm-disks-premium.md)]

### <a name="azure-cloud-services-limits"></a>Az Azure Cloud Services korlátai
[!INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]

### <a name="azure-cognitive-services-limits"></a>Az Azure Cognitive Services korlátai
[!INCLUDE [azure-cloud-services-limits](../includes/azure-cognitive-services-limits.md)]

### <a name="app-service-limits"></a>App Service korlátok
A következő App Service korlátok közé tartoznak a Web Apps, a Mobile Apps és a API Apps korlátai.

[!INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

### <a name="functions-limits"></a>Függvények korlátai
[!INCLUDE [functions-limits](../includes/functions-limits.md)]

### <a name="scheduler-limits"></a>Ütemező korlátai
[!INCLUDE [scheduler-limits-table](../includes/scheduler-limits-table.md)]

### <a name="batch-limits"></a>Batch-korlátok
[!INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

### <a name="biztalk-services-limits"></a>BizTalk Services korlátok
Az alábbi táblázat az Azure BizTalk Services korlátozásait mutatja be.

[!INCLUDE [biztalk-services-service-limits](../includes/biztalk-services-service-limits.md)]

### <a name="azure-cosmos-db-limits"></a>Azure Cosmos DB korlátok
Azure Cosmos DB korlátok esetében lásd: [korlátok a Azure Cosmos DBban](cosmos-db/concepts-limits.md).

### <a name="azure-database-for-mysql"></a>Azure Database for MySQL
Azure Database for MySQL korlátokat a [Azure Database for MySQL korlátozásai](mysql/concepts-limits.md)című témakörben talál.

### <a name="azure-database-for-postgresql"></a>Azure Database for PostgreSQL
Azure Database for PostgreSQL korlátokat a [Azure Database for PostgreSQL korlátozásai](postgresql/concepts-limits.md)című témakörben talál.

### <a name="azure-search-limits"></a>Azure Search korlátok
A díjszabási szintek határozzák meg a keresési szolgáltatás kapacitását és korlátait. A rétegek a következők:

* Az **ingyenes** több-bérlős szolgáltatás, amelyet más Azure-előfizetők is megosztanak, kiértékelésre és kisméretű fejlesztési projektekre tervezték.
* Az alapszintű eszközök dedikált számítástechnikai erőforrásokat biztosítanak az éles számítási feladatokhoz kisebb méretekben, és legfeljebb három replikával rendelkeznek a nagy rendelkezésre állású lekérdezési feladatokhoz.
* A **standard**, amely az S1, az S2, az S3 és az S3 nagy sűrűségű, nagyobb üzemi számítási feladatokhoz is tartozik. A standard szinten több szint is létezik, így kiválaszthatja a munkaterhelés-profilhoz legjobban illő erőforrás-konfigurációt.

**Korlát/előfizetés**

[!INCLUDE [azure-search-limits-per-subscription](../includes/azure-search-limits-per-subscription.md)]

**Korlátok száma keresési szolgáltatásban**

[!INCLUDE [azure-search-limits-per-service](../includes/azure-search-limits-per-service.md)]

Ha többet szeretne megtudni a további részletességgel kapcsolatos korlátozásokról, például a dokumentumok méretéről, a másodpercenkénti lekérdezésekről, a kulcsokról, a kérésekről és a válaszokról, tekintse meg [a szolgáltatási korlátokat Azure Search](search/search-limits-quotas-capacity.md).

### <a name="media-services-limits"></a>Media Services korlátok
[!INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

### <a name="content-delivery-network-limits"></a>Content Delivery Network korlátok
[!INCLUDE [cdn-limits](../includes/cdn-limits.md)]

### <a name="mobile-services-limits"></a>Mobile Services korlátok
[!INCLUDE [mobile-services-limits](../includes/mobile-services-limits.md)]

### <a name="azure-monitor-limits"></a>Azure Monitor korlátok

#### <a name="alerts"></a>Riasztások

[!INCLUDE [monitoring-limits](../includes/azure-monitor-limits-alerts.md)]

#### <a name="action-groups"></a>Műveletcsoportok

[!INCLUDE [monitoring-limits](../includes/azure-monitor-limits-action-groups.md)]

#### <a name="log-queries-and-language"></a>Lekérdezések és nyelv naplózása

[!INCLUDE [monitoring-limits](../includes/azure-monitor-limits-log-queries.md)]

#### <a name="log-analytics-workspaces"></a>Log Analytics-munkaterületek

[!INCLUDE [monitoring-limits](../includes/azure-monitor-limits-workspaces.md)]

#### <a name="application-insights"></a>Application Insights

[!INCLUDE [monitoring-limits](../includes/azure-monitor-limits-app-insights.md)]




### <a name="notification-hubs-limits"></a>Notification Hubs korlátok
[!INCLUDE [notification-hub-limits](../includes/notification-hub-limits.md)]

### <a name="event-hubs-limits"></a>Event Hubs korlátok
[!INCLUDE [azure-servicebus-limits](../includes/event-hubs-limits.md)]

### <a name="service-bus-limits"></a>Service Bus korlátok
[!INCLUDE [azure-servicebus-limits](../includes/service-bus-quotas-table.md)]

### <a name="iot-hub-limits"></a>IoT Hub korlátok
[!INCLUDE [azure-iothub-limits](../includes/iot-hub-limits.md)]

### <a name="iot-hub-device-provisioning-service-limits"></a>IoT Hub Device Provisioning Service korlátok
[!INCLUDE [azure-iotdps-limits](../includes/iot-dps-limits.md)]

### <a name="data-factory-limits"></a>Data Factory korlátok
[!INCLUDE [azure-data-factory-limits](../includes/azure-data-factory-limits.md)]

### <a name="data-lake-analytics-limits"></a>Data Lake Analytics korlátok
[!INCLUDE [azure-data-lake-analytics-limits](../includes/azure-data-lake-analytics-limits.md)]

### <a name="data-lake-store-limits"></a>Data Lake Store korlátok
[!INCLUDE [azure-data-lake-store-limits](../includes/azure-data-lake-store-limits.md)]

### <a name="database-migration-service-limits"></a>Database Migration Service korlátok
[!INCLUDE [database-migration-service-limits](../includes/database-migration-service-limits.md)]

### <a name="stream-analytics-limits"></a>Stream Analytics korlátok
[!INCLUDE [stream-analytics-limits-table](../includes/stream-analytics-limits-table.md)]

### <a name="active-directory-limits"></a>Active Directory korlátok
[!INCLUDE [AAD-service-limits](../includes/active-directory-service-limits-include.md)]

### <a name="event-grid-limits"></a>Event Grid korlátok
[!INCLUDE [event-grid-limits](../includes/event-grid-limits.md)]

### <a name="azure-maps-limits"></a>Azure Maps korlátok
[!INCLUDE [maps-limits](../includes/maps-limits.md)]

### <a name="azure-policy-limits"></a>Azure Policy korlátok
[!INCLUDE [policy-limits](../includes/azure-policy-limits.md)]

### <a name="storsimple-system-limits"></a>StorSimple rendszerkorlátai
[!INCLUDE [storsimple-limits-table](../includes/storsimple-limits-table.md)]

### <a name="backup-limits"></a>Biztonsági mentési korlátok
[!INCLUDE [azure-backup-limits](../includes/azure-backup-limits.md)]

### <a name="azure-signalr-service-limits"></a>Az Azure Signaler szolgáltatás korlátai
[!INCLUDE [signalr-service-limits](../includes/signalr-service-limits.md)]

### <a name="site-recovery-limits"></a>A Site Recovery korlátai
[!INCLUDE [site-recovery-limits](../includes/site-recovery-limits.md)]

### <a name="api-management-limits"></a>API Management korlátok
[!INCLUDE [api-management-service-limits](../includes/api-management-service-limits.md)]

### <a name="azure-cache-for-redis-limits"></a>Azure cache a Redis korlátaihoz
[!INCLUDE [redis-cache-service-limits](../includes/redis-cache-service-limits.md)]

### <a name="key-vault-limits"></a>Key Vault korlátok
[!INCLUDE [key-vault-limits](../includes/key-vault-limits.md)]

### <a name="multi-factor-authentication-limits"></a>Multi-Factor Authentication-korlátok
[!INCLUDE [azure-mfa-service-limits](../includes/azure-mfa-service-limits.md)]

### <a name="automation-limits"></a>Automatizálási korlátok
[!INCLUDE [automation-limits](../includes/azure-automation-service-limits.md)]

### <a name="identity-manager-limits"></a>Identity Manager-korlátok
[!INCLUDE [automation-limits](~/includes/managed-identity-limits.md)]

### <a name="role-based-access-control-limits"></a>Szerepköralapú hozzáférés-vezérlési korlátok
[!INCLUDE [role-based-access-control-limits](../includes/role-based-access-control-limits.md)]

### <a name="sql-database-limits"></a>SQL Database korlátok
SQL Database korlátok esetében tekintse meg az [önálló adatbázisok SQL Database erőforrás](sql-database/sql-database-vcore-resource-limits-single-databases.md)-korlátozásokat, [SQL Database a rugalmas készletek és a készletezett adatbázisok](sql-database/sql-database-vcore-resource-limits-elastic-pools.md)erőforrás-korlátait, valamint [SQL Database erőforrás-korlátozásokat a felügyelt példányok számára](sql-database/sql-database-managed-instance-resource-limits.md).

### <a name="sql-data-warehouse-limits"></a>SQL Data Warehouse korlátok
SQL Data Warehouse korlátok esetében lásd: [SQL Data Warehouse erőforrás-korlátok](sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md).

## <a name="see-also"></a>Lásd még
- [Az Azure korlátainak és növekedésének megismerése](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
- [Virtuális gépek és felhőalapú szolgáltatások mérete az Azure-ban](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Az Azure Cloud Services mérete](cloud-services/cloud-services-sizes-specs.md)
