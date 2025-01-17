---
title: Azure Storage-metrikák a Azure Monitorban | Microsoft Docs
description: Ismerkedjen meg a Azure Monitor által kínált új mérőszámokkal.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 09/05/2017
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: e31ad78e24f329eb46cd85ba4a5962442a216779
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68844829"
---
# <a name="azure-storage-metrics-in-azure-monitor"></a>Azure Storage-metrikák az Azure Monitorban

Az Azure Storage mérőszámai segítségével elemezheti a használati trendeket, a nyomkövetési kérelmeket és a Storage-fiókkal kapcsolatos problémákat.

A Azure Monitor egységes felhasználói felületet biztosít a különböző Azure-szolgáltatások figyelésére. További információ: [Azure monitor](../../monitoring-and-diagnostics/monitoring-overview.md). Az Azure Storage Azure Monitor a metrikus adatoknak a Azure Monitor platformba való küldésével integrálható.

## <a name="access-metrics"></a>Access-metrikák

Az Azure Monitor hozzáférés metrikák több módot is biztosít. A [Azure Portal](https://portal.azure.com), a Azure monitor API-k (REST és .net) és az Analysis Solutions, például a Event Hubs használatával férhet hozzájuk. További információ: [Azure monitor mérőszámok](../../monitoring-and-diagnostics/monitoring-overview-metrics.md).

A metrikák alapértelmezés szerint engedélyezve vannak, és az elmúlt 93 nap adatait is elérheti. Ha szeretne egy hosszabb ideig megőrizni az adatokat, úgy archiválhatók metrikák adatai egy Azure Storage-fiókhoz. Ennek a konfigurációja a [diagnosztikai beállítások](../../azure-monitor/platform/diagnostic-logs-overview.md) az Azure monitorban.

### <a name="access-metrics-in-the-azure-portal"></a>Hozzáférési metrikák a Azure Portal

A metrikák a Azure Portalban is megfigyelhetők. Az alábbi példa azt szemlélteti, hogyan lehet megtekinteni a tranzakciókat a fiók szintjén.

![képernyőfelvétel a metrikák eléréséről a Azure Portal](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal.png)

A dimenziókat támogató mérőszámok esetében a mérőszámot a kívánt dimenzió értékkel szűrheti. Az alábbi példa azt szemlélteti, hogyan lehet megtekinteni egy adott művelethez tartozó tranzakciókat a fiók szintjén az **API-név** dimenzió értékének kiválasztásával.

![képernyőfelvétel a Azure Portal dimenzióval rendelkező metrikák eléréséről](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal-with-dimension.png)

### <a name="access-metrics-with-the-rest-api"></a>Metrikák elérése a REST API

A Azure Monitor [REST API-kat](/rest/api/monitor/) biztosít a metrika definíciójának és értékeinek olvasásához. Ez a szakasz bemutatja, hogyan olvashatja el a tárolási metrikákat. Az erőforrás-azonosító az összes REST API-ban használatos. További információért olvassa el a Storage-szolgáltatások erőforrás-AZONOSÍTÓjának ismertetése című témakört.

Az alábbi példa bemutatja, hogyan használható a [ArmClient](https://github.com/projectkudu/ARMClient) a parancssorban a REST API tesztelésének egyszerűsítése érdekében.

#### <a name="list-account-level-metric-definition-with-the-rest-api"></a>A fiók szintű metrika definíciójának listázása a REST API

Az alábbi példa bemutatja, hogyan listázhatja a metrika definícióját a fiók szintjén:

```
# Login to Azure and enter your credentials when prompted.
> armclient login

> armclient GET /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metricdefinitions?api-version=2018-01-01

```

Ha szeretné kilistázni a blob, a tábla, a fájl vagy a várólista metrikájának definícióit, az API-val különböző erőforrás-azonosítókat kell megadnia az egyes szolgáltatásokhoz.

A válasz JSON formátumban tartalmazza a metrika definícióját:

```Json
{
  "value": [
    {
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metricdefinitions/UsedCapacity",
      "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}",
      "category": "Capacity",
      "name": {
        "value": "UsedCapacity",
        "localizedValue": "Used capacity"
      },
      "isDimensionRequired": false,
      "unit": "Bytes",
      "primaryAggregationType": "Average",
      "metricAvailabilities": [
        {
          "timeGrain": "PT1M",
          "retention": "P30D"
        },
        {
          "timeGrain": "PT1H",
          "retention": "P30D"
        }
      ]
    },
    ... next metric definition
  ]
}

```

#### <a name="read-account-level-metric-values-with-the-rest-api"></a>A fiók szintű metrika értékeinek olvasása a REST API

Az alábbi példa bemutatja, hogyan olvashatók be a metrikus adatok a fiók szintjén:

```
> armclient GET "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metrics?metricnames=Availability&api-version=2018-01-01&aggregation=Average&interval=PT1H"

```

Ha a fenti példában a blob, a tábla, a fájl vagy a várólista metrikai értékeit szeretné olvasni, különböző erőforrás-azonosítókat kell megadnia minden szolgáltatáshoz az API-val.

A következő válasz JSON formátumú mérőszám-értékeket tartalmaz:

```Json
{
  "cost": 0,
  "timespan": "2017-09-07T17:27:41Z/2017-09-07T18:27:41Z",
  "interval": "PT1H",
  "value": [
    {
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/Microsoft.Insights/metrics/Availability",
      "type": "Microsoft.Insights/metrics",
      "name": {
        "value": "Availability",
        "localizedValue": "Availability"
      },
      "unit": "Percent",
      "timeseries": [
        {
          "metadatavalues": [],
          "data": [
            {
              "timeStamp": "2017-09-07T17:27:00Z",
              "average": 100.0
            }
          ]
        }
      ]
    }
  ]
}

```

### <a name="access-metrics-with-the-net-sdk"></a>Metrikák elérése a .NET SDK-val

Azure Monitor [.net SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) -t biztosít a metrika definíciójának és értékeinek olvasásához. A [mintakód](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) azt mutatja be, hogyan használható az SDK különböző paraméterekkel. A tárolási metrikák `0.18.0-preview` használatához vagy újabb verziójának kell lennie. Az erőforrás-azonosító a .NET SDK-ban használatos. További információért olvassa el a Storage-szolgáltatások erőforrás-AZONOSÍTÓjának ismertetése című témakört.

Az alábbi példa bemutatja, hogyan használható a Azure Monitor .NET SDK a tárolási metrikák olvasásához.

#### <a name="list-account-level-metric-definition-with-the-net-sdk"></a>A fiók szintű metrika definíciójának listázása a .NET SDK-val

Az alábbi példa bemutatja, hogyan listázhatja a metrika definícióját a fiók szintjén:

```csharp
    public static async Task ListStorageMetricDefinition()
    {
        // Resource ID for storage account
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}";
        var subscriptionId = "{SubscriptionID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "{TenantID}";
        var applicationId = "{ApplicationID}";
        var accessKey = "{AccessKey}";

        // Using metrics in Azure Monitor is currently free. However, if you use additional solutions ingesting metrics data, you may be billed by these solutions. For example, you are billed by Azure Storage if you archive metrics data to an Azure Storage account. Or you are billed by Operation Management Suite (OMS) if you stream metrics data to OMS for advanced analysis.
        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;
        IEnumerable<MetricDefinition> metricDefinitions = await readOnlyClient.MetricDefinitions.ListAsync(resourceUri: resourceId, cancellationToken: new CancellationToken());

        foreach (var metricDefinition in metricDefinitions)
        {
            //Enumrate metric definition:
            //    Id
            //    ResourceId
            //    Name
            //    Unit
            //    MetricAvailabilities
            //    PrimaryAggregationType
            //    Dimensions
            //    IsDimensionRequired
        }
    }

```

Ha szeretné kilistázni a blob, a tábla, a fájl vagy a várólista metrikájának definícióit, az API-val különböző erőforrás-azonosítókat kell megadnia az egyes szolgáltatásokhoz.

#### <a name="read-metric-values-with-the-net-sdk"></a>Metrikus értékek olvasása a .NET SDK-val

Az alábbi példa azt szemlélteti, hogyan `UsedCapacity` lehet az adatolvasást a fiók szintjén:

```csharp
    public static async Task ReadStorageMetricValue()
    {
        // Resource ID for storage account
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}";
        var subscriptionId = "{SubscriptionID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "{TenantID}";
        var applicationId = "{ApplicationID}";
        var accessKey = "{AccessKey}";

        MonitorClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToUniversalTime().ToString("o");
        string endDate = DateTime.Now.ToUniversalTime().ToString("o");
        string timeSpan = startDate + "/" + endDate;

        Response = await readOnlyClient.Metrics.ListAsync(
            resourceUri: resourceId,
            timespan: timeSpan,
            interval: System.TimeSpan.FromHours(1),
            metricnames: "UsedCapacity",

            aggregation: "Average",
            resultType: ResultType.Data,
            cancellationToken: CancellationToken.None);

        foreach (var metric in Response.Value)
        {
            //Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

Ha a fenti példában a blob, a tábla, a fájl vagy a várólista metrikai értékeit szeretné olvasni, különböző erőforrás-azonosítókat kell megadnia minden szolgáltatáshoz az API-val.

#### <a name="read-multi-dimensional-metric-values-with-the-net-sdk"></a>Többdimenziós metrika értékeinek olvasása a .NET SDK-val

Többdimenziós mérőszámok esetén meg kell határoznia a metaadat-szűrőt, ha adott dimenzió értékén szeretné beolvasni a metrikai adatokat.

Az alábbi példa bemutatja, hogyan olvashatja el a metrikus adatokat a mérőszámot támogató többdimenziós:

```csharp
    public static async Task ReadStorageMetricValueTest()
    {
        // Resource ID for blob storage
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default";
        var subscriptionId = "{SubscriptionID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "{TenantID}";
        var applicationId = "{ApplicationID}";
        var accessKey = "{AccessKey}";

        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToUniversalTime().ToString("o");
        string endDate = DateTime.Now.ToUniversalTime().ToString("o");
        string timeSpan = startDate + "/" + endDate;
        // It's applicable to define meta data filter when a metric support dimension
        // More conditions can be added with the 'or' and 'and' operators, example: BlobType eq 'BlockBlob' or BlobType eq 'PageBlob'
        ODataQuery<MetadataValue> odataFilterMetrics = new ODataQuery<MetadataValue>(
            string.Format("BlobType eq '{0}'", "BlockBlob"));

        Response = readOnlyClient.Metrics.List(
                        resourceUri: resourceId,
                        timespan: timeSpan,
                        interval: System.TimeSpan.FromHours(1),
                        metricnames: "BlobCapacity",
                        odataQuery: odataFilterMetrics,
                        aggregation: "Average",
                        resultType: ResultType.Data);

        foreach (var metric in Response.Value)
        {
            //Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

## <a name="understanding-resource-id-for-services-in-azure-storage"></a>Az Azure Storage-szolgáltatások erőforrás-AZONOSÍTÓjának ismertetése

Az erőforrás-azonosító az erőforrás egyedi azonosítója az Azure-ban. Ha a Azure Monitor REST API a metrikák definícióinak és értékeinek olvasására használja, akkor a használni kívánt erőforrás erőforrás-AZONOSÍTÓját kell használnia. Az erőforrás-azonosító sablon a következő formátumot követi:

```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

A Storage a Storage-fiók szintjén és a szolgáltatási szinten is biztosít metrikákat Azure Monitor. Például lekérheti a blob Storage mérőszámait. Minden szint saját erőforrás-AZONOSÍTÓval rendelkezik, amely csak az adott szint metrikáinak lekérésére szolgál.

### <a name="resource-id-for-a-storage-account"></a>Storage-fiók erőforrás-azonosítója

A következő ábrán egy Storage-fiók erőforrás-AZONOSÍTÓjának megadására szolgáló formátum látható.

```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}
```

### <a name="resource-id-for-the-storage-services"></a>A tárolási szolgáltatások erőforrás-azonosítója

A következő ábrán az egyes tárolási szolgáltatások erőforrás-AZONOSÍTÓjának megadására szolgáló formátum látható.

* Erőforrás-azonosító Blob service
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default
```
* Erőforrás-azonosító Table service
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/tableServices/default
```
* Erőforrás-azonosító Queue szolgáltatás
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/queueServices/default
```
* Fájlszolgáltatások erőforrás-azonosítója
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/fileServices/default
```

### <a name="resource-id-in-azure-monitor-rest-api"></a>Erőforrás-azonosító Azure Monitor REST API

Az alábbi ábrán az Azure Monitor REST API meghívásakor használt minta látható.

```
GET {resourceId}/providers/microsoft.insights/metrics?{parameters}
```

## <a name="capacity-metrics"></a>Kapacitás metrikái
A kapacitás metrikáinak értékeit minden órában Azure Monitor elküldjük. Az értékek naponta frissülnek. Az időkeret határozza meg azt az időintervallumot, ameddig a metrikák értékei bemutatva lesznek. Az összes kapacitás metrikájának támogatott időszelete egy óra (PT1H).

Az Azure Storage a következő kapacitási mérőszámokat biztosítja Azure Monitorban.

### <a name="account-level"></a>Fiók szintje

| Metrika neve | Leírás |
| ------------------- | ----------------- |
| UsedCapacity | A Storage-fiók által használt tárterület mennyisége. Standard szintű tárfiókok esetében ez a blob, a tábla, a fájl és a várólista által használt kapacitás összege. Prémium szintű és Blob Storage-fiókok esetében a BlobCapacity értékével azonos. <br/><br/> Egység Bájt <br/> Összesítés típusa: Average <br/> Érték példa: 1024 |

### <a name="blob-storage"></a>Blob Storage

| Metrika neve | Leírás |
| ------------------- | ----------------- |
| BlobCapacity | A Storage-fiókban használt BLOB Storage teljes száma. <br/><br/> Egység Bájt <br/> Összesítés típusa: Average <br/> Érték példa: 1024 <br/> Méretek: **BlobType**és **BlobTier** ([definíció](#metrics-dimensions)) |
| BlobCount    | A Storage-fiókban tárolt blob-objektumok száma. <br/><br/> Egység Count <br/> Összesítés típusa: Average <br/> Érték példa: 1024 <br/> Méretek: **BlobType**és **BlobTier** ([definíció](#metrics-dimensions)) |
| ContainerCount    | A Storage-fiókban lévő tárolók száma. <br/><br/> Egység Count <br/> Összesítés típusa: Average <br/> Érték példa: 1024 |
| IndexCapacity     | A ADLS Gen2 hierarchikus index által használt tárterület mennyisége <br/><br/> Egység Bájt <br/> Összesítés típusa: Average <br/> Érték példa: 1024 |

### <a name="table-storage"></a>Táblatároló

| Metrika neve | Leírás |
| ------------------- | ----------------- |
| TableCapacity | A Storage-fiók által használt Table Storage mennyisége. <br/><br/> Egység Bájt <br/> Összesítés típusa: Average <br/> Érték példa: 1024 |
| TableCount   | A Storage-fiókban lévő táblák száma. <br/><br/> Egység Count <br/> Összesítés típusa: Average <br/> Érték példa: 1024 |
| TableEntityCount | A Storage-fiókban lévő tábla entitások száma. <br/><br/> Egység Count <br/> Összesítés típusa: Average <br/> Érték példa: 1024 |

### <a name="queue-storage"></a>Queue Storage

| Metrika neve | Leírás |
| ------------------- | ----------------- |
| QueueCapacity | A Storage-fiók által használt üzenetsor-tároló mennyisége. <br/><br/> Egység Bájt <br/> Összesítés típusa: Average <br/> Érték példa: 1024 |
| QueueCount   | A Storage-fiókban lévő várólisták száma. <br/><br/> Egység Count <br/> Összesítés típusa: Average <br/> Érték példa: 1024 |
| QueueMessageCount | A nem lejárt üzenetsor-üzenetek száma a Storage-fiókban. <br/><br/>Egység Count <br/> Összesítés típusa: Average <br/> Érték példa: 1024 |

### <a name="file-storage"></a>File Storage

| Metrika neve | Leírás |
| ------------------- | ----------------- |
| FileCapacity | A Storage-fiók által használt file Storage mennyisége. <br/><br/> Egység Bájt <br/> Összesítés típusa: Average <br/> Érték példa: 1024 |
| FileCount   | A Storage-fiókban található fájlok száma. <br/><br/> Egység Count <br/> Összesítés típusa: Average <br/> Érték példa: 1024 |
| FileShareCount | A Storage-fiókban lévő fájlmegosztás száma. <br/><br/> Egység Count <br/> Összesítés típusa: Average <br/> Érték példa: 1024 |

## <a name="transaction-metrics"></a>Tranzakciós metrikák

A tranzakciós metrikák az Azure Storage-ból Azure Monitorba történő minden kérelemre kibocsátva. Abban az esetben, ha nincs tevékenység a Storage-fiókban, az adott időszakban nem lesz adat a tranzakciós metrikákban. Minden tranzakciós metrika a fiók és a szolgáltatás szintjén (blob Storage, Table Storage, Azure Files és üzenetsor-tárolás) is elérhető. Az időgabona meghatározza a metrikai értékek bemutatásának időintervallumát. Az összes tranzakciós metrika támogatott időgabona-PT1H és PT1M.

Az Azure Storage a következő tranzakciós mérőszámokat biztosítja Azure Monitorban.

| Metrika neve | Leírás |
| ------------------- | ----------------- |
| Tranzakciók | Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és sikertelen kérések, valamint a hibára futott kérések számát tartalmazza. <br/><br/> Egység Count <br/> Összesítés típusa: Összes <br/> Alkalmazható méretek: ResponseType, GeoType, ApiName és hitelesítés ([definíció](#metrics-dimensions))<br/> Érték példa: 1024 |
| Bejövő forgalom | A bejövő adatok mennyisége. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja. <br/><br/> Egység Bájt <br/> Összesítés típusa: Összes <br/> Alkalmazható méretek: GeoType, ApiName és hitelesítés ([definíció](#metrics-dimensions)) <br/> Érték példa: 1024 |
| Kimenő forgalom | A kimenő adatok mennyisége. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat. <br/><br/> Egység Bájt <br/> Összesítés típusa: Összes <br/> Alkalmazható méretek: GeoType, ApiName és hitelesítés ([definíció](#metrics-dimensions)) <br/> Érték példa: 1024 |
| SuccessServerLatency | Az Azure Storage által sikeresen feldolgozott kérések átlagos feldolgozási ideje. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést. <br/><br/> Egység Ezredmásodperc <br/> Összesítés típusa: Average <br/> Alkalmazható méretek: GeoType, ApiName és hitelesítés ([definíció](#metrics-dimensions)) <br/> Érték példa: 1024 |
| SuccessE2ELatency | A tárolási szolgáltatás vagy a megadott API-művelet számára elküldött sikeres kérések végpontok közötti késésének átlaga. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt. <br/><br/> Egység Ezredmásodperc <br/> Összesítés típusa: Average <br/> Alkalmazható méretek: GeoType, ApiName és hitelesítés ([definíció](#metrics-dimensions)) <br/> Érték példa: 1024 |
| Rendelkezésre állás | A társzolgáltatás vagy a megadott API-művelet rendelkezésre állási százaléka. A Rendelkezésre állás az összes számlázható kérelem értékének és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. Minden nem várt hiba a társzolgáltatás vagy a megadott API-művelet romlik a rendelkezésre állás eredményez. <br/><br/> Egység Percent <br/> Összesítés típusa: Average <br/> Alkalmazható méretek: GeoType, ApiName és hitelesítés ([definíció](#metrics-dimensions)) <br/> Érték példa: 99,99 |

## <a name="metrics-dimensions"></a>Metrikák dimenziók

Az Azure Storage a Azure Monitor metrikáinak következő dimenzióit támogatja.

| Dimenzió neve | Leírás |
| ------------------- | ----------------- |
| **BlobType** | A blob típusa csak a blob-metrikák esetében. A támogatott értékek a következők: **BlockBlob**, **PageBlob**és **Azure Data Lake Storage**. A hozzáfűző blobot a BlockBlob tartalmazza. |
| **BlobTier** | Az Azure Storage különböző hozzáférési szinteket kínál, amelyek lehetővé teszik a blob-objektumok-adattárolást a legköltséghatékonyabb módon. További információ az [Azure Storage blob](../blobs/storage-blob-storage-tiers.md)-szintjéről. A támogatott értékek a következők: <br/> <li>**Gyors**: Gyors elérési szint</li> <li>**Cool**: Hűvös szint</li> <li>**Archiválás**: Archiválási szint</li> <li>**Prémium**: Prémium szint a blokkos blobhoz</li> <li>**P4/P6/P10/P15/P20/P30/P40/P50/P60**: A prémium szintű lap blob-típusai</li> <li>**Standard**: A standard oldal blobjának típusa</li> <li>Nem **Lépcsőzetes**: Az általános célú v1-es Storage-fiókhoz tartozó rétegek típusa</li> |
| **GeoType** | Az elsődleges vagy a másodlagos fürtből származó tranzakció. Az elérhető értékek közé tartozik az **elsődleges** és a **másodlagos**. Az olvasási hozzáférés földrajzi redundáns tárolási (RA-GRS) szolgáltatásra vonatkozik, amikor objektumokat olvas a másodlagos bérlőről. |
| **ResponseType** | Tranzakció válaszának típusa Az elérhető értékek közé a következők tartoznak: <br/><br/> <li>**ServerOtherError**: Minden egyéb kiszolgálóoldali hiba, kivéve az ismertetett hibákat </li> <li>**ServerBusyError**: Hitelesített kérés, amely HTTP 503-as állapotkódot adott vissza. </li> <li>**ServerTimeoutError**: Hitelesített kérés, amely túllépte az időkorlátot, és HTTP 500-as állapotkódot adott vissza. Az időtúllépés egy kiszolgálóhiba miatt lépett fel. </li> <li>**AuthorizationError**: Hitelesített kérés, amely jogosulatlan adathozzáférés vagy egy engedélyezési hiba miatt hiúsult meg. </li> <li>**NetworkError**: Hitelesített kérés, amely hálózati hibák miatt hiúsult meg. Leggyakrabban akkor fordul elő, ha egy ügyfél idő előtt, az időkorlát letelte előtt zár be egy kapcsolatot. </li> <li>**ClientThrottlingError**: Ügyféloldali szabályozási hiba. </li> <li>**ClientTimeoutError**: Hitelesített kérés, amely túllépte az időkorlátot, és HTTP 500-as állapotkódot adott vissza. Ha az ügyfél hálózati időkorlátja vagy a kérés időkorlátja a tárolási szolgáltatás által várt értéknél alacsonyabbra van állítva, akkor ez egy várt időtúllépés. Máskülönben a rendszer ServerTimeoutError hibát jelent. </li> <li>**ClientOtherError**: Minden egyéb ügyféloldali hiba, kivéve az ismertetett hibákat. </li> <li>**Success**: Sikeres kérelem</li> <li> **SuccessWithThrottling**: Sikeres kérés, ha az SMB-ügyfél az első kísérlet (ek) során leszabályozza a szabályozást, de az újrapróbálkozások után sikeres lesz.</li> |
| **ApiName** | A művelet neve. Példa: <br/> <li>**CreateContainer**</li> <li>**DeleteBlob**</li> <li>**GetBlob**</li> Az összes művelet neve: [Document](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages). |
| **Hitelesítés** | A tranzakciókban használt hitelesítési típus. Az elérhető értékek közé a következők tartoznak: <br/> <li>**AccountKey**: A tranzakciót a Storage-fiók kulcsa hitelesíti.</li> <li>**SAS**: A tranzakció megosztott hozzáférési aláírásokkal van hitelesítve.</li> <li>**OAuth**: A tranzakció OAuth hozzáférési jogkivonatokkal van hitelesítve.</li> <li>**Névtelen**: A tranzakciót a rendszer névtelenül kéri. Nem tartalmaz elővizsgálati kérelmeket.</li> <li>**AnonymousPreflight**: A tranzakció elővizsgálati kérelem.</li> |

A dimenziókat támogató metrikák esetében meg kell adnia a dimenzió értékét a megfelelő mérőszámok értékeinek megtekintéséhez. Ha például a sikeres válaszok **tranzakció** értékét tekinti át, akkor a **ResponseType** dimenziót a **sikerrel**kell szűrnie. Vagy ha megtekinti a blokk **BlobCount** értékét, akkor a **BlobType** -dimenziót a **BlockBlob**-mel kell szűrnie.

## <a name="service-continuity-of-legacy-metrics"></a>Az örökölt metrikák szolgáltatás folytonossága

Az örökölt metrikák párhuzamosan érhetők el Azure Monitor felügyelt metrikákkal. A támogatás addig tart, amíg az Azure Storage nem ér véget a szolgáltatásnak az örökölt mérőszámokon.

## <a name="faq"></a>GYIK

**Támogatja az új metrikák a klasszikus Storage-fiókot?**

Nem, a Azure Monitorban szereplő új metrikák csak Azure Resource Manager Storage-fiókokat támogatják. Ha metrikákat szeretne használni a Storage-fiókokon, át kell telepítenie Azure Resource Manager Storage-fiókba. Lásd: [áttelepítés Azure Resource Managerra](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview).

**Támogatja az Azure Storage a Managed Disks és a nem felügyelt lemezek metrikáit?**

Nem, az Azure-beli számítási szolgáltatás támogatja a lemezek mérőszámait. További részletekért tekintse meg a [cikket](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/) .

**Hogyan lehet új metrikákkal feltérképezni és áttelepíteni a klasszikus metrikákat?**

Részletes leképezést talál a klasszikus metrikák és az [Azure Storage](./storage-metrics-migration.md)-metrikák áttelepítése során felhasználható új metrikák között.

## <a name="next-steps"></a>További lépések

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
