---
title: Megkeresi a kérési egység (RU) díját Azure Cosmos DB
description: Megtudhatja, hogyan keresheti meg az Azure Cosmos-tárolón végrehajtott műveletekre vonatkozó kérési egység (RU) díját.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: sample
ms.date: 06/14/2019
ms.author: thweiss
ms.openlocfilehash: 96c36067456a49a5760d6fde488dcb4ad8311a90
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68356459"
---
# <a name="find-the-request-unit-charge-in-azure-cosmos-db"></a>A kérési egység díjszabásának megkeresése Azure Cosmos DB

Ez a cikk bemutatja, hogyan lehet megkeresni a [kérési egység](request-units.md) (ru) használatát a Azure Cosmos db tárolóján végrehajtott bármely művelethez. Jelenleg ezt a felhasználást csak a Azure Portal használatával, vagy az SDK-k egyikén keresztül a Azure Cosmos DBtól visszaküldött válasz vizsgálatával mérhető fel.

## <a name="sql-core-api"></a>SQL (Core) API

Ha az SQL API-t használja, több lehetősége is van arra, hogy megkeresse a művelet RU-felhasználását egy Azure Cosmos-tárolón.

### <a name="use-the-azure-portal"></a>Az Azure Portal használata

Jelenleg a Azure Portal csak az SQL-lekérdezésekhez tartozó kérelmek díját találja.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. [Hozzon létre egy új Azure Cosmos-fiókot](create-sql-api-dotnet.md#create-account) , és adja meg az adatgyűjtést, vagy válasszon olyan meglévő Azure Cosmos-fiókot, amely már tartalmaz információt.

1. Lépjen a **adatkezelő** ablaktáblára, majd válassza ki a használni kívánt tárolót.

1. Válassza az **új SQL-lekérdezés**lehetőséget.

1. Adjon meg egy érvényes lekérdezést, majd válassza a **lekérdezés végrehajtása**lehetőséget.

1. A **lekérdezési statisztikák** lehetőség kiválasztásával jelenítheti meg a tényleges kérelmek díját a végrehajtott kérelemért.

![Képernyőkép az SQL-lekérdezési kérések díjszabásáról a Azure Portal](./media/find-request-unit-charge/portal-sql-query.png)

### <a name="use-the-net-sdk"></a>A .NET SDK használata
### <a name="net-v2-sdk"></a>.Net V2 SDK

A [.net SDK v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) -ből visszaadott objektumok egy `RequestCharge` tulajdonságot tesznek elérhetővé:

```csharp
ResourceResponse<Document> fetchDocumentResponse = await client.ReadDocumentAsync(
    UriFactory.CreateDocumentUri("database", "container", "itemId"),
    new RequestOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    });
var requestCharge = fetchDocumentResponse.RequestCharge;

StoredProcedureResponse<string> storedProcedureCallResponse = await client.ExecuteStoredProcedureAsync<string>(
    UriFactory.CreateStoredProcedureUri("database", "container", "storedProcedureId"),
    new RequestOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    });
requestCharge = storedProcedureCallResponse.RequestCharge;

IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri("database", "container"),
    "SELECT * FROM c",
    new FeedOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    }).AsDocumentQuery();
while (query.HasMoreResults)
{
    FeedResponse<dynamic> queryResponse = await query.ExecuteNextAsync<dynamic>();
    requestCharge = queryResponse.RequestCharge;
}
```

### <a name="net-v3-sdk"></a>.Net V3 SDK

A [.net SDK v3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) által visszaadott objektumok a következő `RequestCharge` tulajdonságot teszik elérhetővé:

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/CustomDocsSampleCode.cs?name=GetRequestCharge)]

További információ: gyors útmutató [: Hozzon létre egy .NET-webalkalmazást egy SQL API-](create-sql-api-dotnet.md)fiók használatával Azure Cosmos DBban.

### <a name="use-the-java-sdk"></a>A Java SDK használata

A [Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) által visszaadott objektumok egy `getRequestCharge()` metódust tesznek elérhetővé:

```java
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPartitionKey(new PartitionKey("partitionKey"));

Observable<ResourceResponse<Document>> readDocumentResponse = client.readDocument(String.format("/dbs/%s/colls/%s/docs/%s", "database", "container", "itemId"), requestOptions);
readDocumentResponse.subscribe(result -> {
    double requestCharge = result.getRequestCharge();
});

Observable<StoredProcedureResponse> storedProcedureResponse = client.executeStoredProcedure(String.format("/dbs/%s/colls/%s/sprocs/%s", "database", "container", "storedProcedureId"), requestOptions, null);
storedProcedureResponse.subscribe(result -> {
    double requestCharge = result.getRequestCharge();
});

FeedOptions feedOptions = new FeedOptions();
feedOptions.setPartitionKey(new PartitionKey("partitionKey"));

Observable<FeedResponse<Document>> feedResponse = client
    .queryDocuments(String.format("/dbs/%s/colls/%s", "database", "container"), "SELECT * FROM c", feedOptions);
feedResponse.forEach(result -> {
    double requestCharge = result.getRequestCharge();
});
```

További információ: gyors útmutató [: Java-alkalmazás létrehozása Azure Cosmos DB SQL API-fiók](create-sql-api-java.md)használatával.

### <a name="use-the-nodejs-sdk"></a>A Node. js SDK használata

A [Node. js SDK](https://www.npmjs.com/package/@azure/cosmos) által visszaadott objektumok olyan `headers` alobjektumot tesznek elérhetővé, amely az alapul szolgáló http API által visszaadott összes fejlécet leképezi. A kérelem díja a `x-ms-request-charge` kulcs alatt érhető el:

```javascript
const item = await client
    .database('database')
    .container('container')
    .item('itemId', 'partitionKey')
    .read();
var requestCharge = item.headers['x-ms-request-charge'];

const storedProcedureResult = await client
    .database('database')
    .container('container')
    .storedProcedure('storedProcedureId')
    .execute({
        partitionKey: 'partitionKey'
    });
requestCharge = storedProcedureResult.headers['x-ms-request-charge'];

const query = client.database('database')
    .container('container')
    .items
    .query('SELECT * FROM c', {
        partitionKey: 'partitionKey'
    });
while (query.hasMoreResults()) {
    var result = await query.executeNext();
    requestCharge = result.headers['x-ms-request-charge'];
}
```

További információ: gyors útmutató [: Node. js-alkalmazás létrehozása Azure Cosmos DB SQL API-fiók](create-sql-api-nodejs.md)használatával. 

### <a name="use-the-python-sdk"></a>A Python SDK használata

A `CosmosClient` [Python SDK](https://pypi.org/project/azure-cosmos/) -ból származó objektum egy olyan `last_response_headers` szótárt tesz elérhetővé, amely leképezi a mögöttes http API által visszaadott összes fejlécet az utolsó művelet végrehajtásához. A kérelem díja a `x-ms-request-charge` kulcs alatt érhető el:

```python
response = client.ReadItem(
    'dbs/database/colls/container/docs/itemId', {'partitionKey': 'partitionKey'})
request_charge = client.last_response_headers['x-ms-request-charge']

response = client.ExecuteStoredProcedure(
    'dbs/database/colls/container/sprocs/storedProcedureId', None, {'partitionKey': 'partitionKey'})
request_charge = client.last_response_headers['x-ms-request-charge']
```

További információ: gyors útmutató [: Python-alkalmazás létrehozása Azure Cosmos DB SQL API-fiók](create-sql-api-python.md)használatával. 

## <a name="azure-cosmos-db-api-for-mongodb"></a>MongoDB-hez készült Azure Cosmos DB API

Az RU díját egy nevű `getLastRequestStatistics`egyéni adatbázis- [parancs](https://docs.mongodb.com/manual/reference/command/) teszi elérhetővé. A parancs egy olyan dokumentumot ad vissza, amely tartalmazza az utolsó végrehajtott művelet nevét, a kérések díját és annak időtartamát. Ha a MongoDB Azure Cosmos DB API-t használja, több lehetőség is van az RU-díj lekérésére.

### <a name="use-the-azure-portal"></a>Az Azure Portal használata

Jelenleg a kérelmek díját a Azure Portal csak a lekérdezésekhez lehet megkeresni.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. [Hozzon létre egy új Azure Cosmos-fiókot](create-mongodb-dotnet.md#create-a-database-account) , és adja meg az adatgyűjtést, vagy válasszon ki egy olyan meglévő fiókot, amely már tartalmaz információt.

1. Lépjen a **adatkezelő** ablaktáblára, majd válassza ki a gyűjteményt, amelyen dolgozni szeretne.

1. Válassza a **New Query** (Új lekérdezés) lehetőséget.

1. Adjon meg egy érvényes lekérdezést, majd válassza a **lekérdezés végrehajtása**lehetőséget.

1. A **lekérdezési statisztikák** lehetőség kiválasztásával jelenítheti meg a tényleges kérelmek díját a végrehajtott kérelemért.

![Képernyőkép a MongoDB-lekérdezési kérelmekért a Azure Portal](./media/find-request-unit-charge/portal-mongodb-query.png)

### <a name="use-the-mongodb-net-driver"></a>A MongoDB .NET-illesztőprogram használata

Ha a [hivatalos MongoDB .net-illesztőprogramot](https://docs.mongodb.com/ecosystem/drivers/csharp/)használja, végrehajthat parancsokat úgy, hogy meghívja a `RunCommand` metódust egy `IMongoDatabase` objektumon. Ehhez a metódushoz az `Command<>` absztrakt osztály megvalósítására van szükség:

```csharp
class GetLastRequestStatisticsCommand : Command<Dictionary<string, object>>
{
    public override RenderedCommand<Dictionary<string, object>> Render(IBsonSerializerRegistry serializerRegistry)
    {
        return new RenderedCommand<Dictionary<string, object>>(new BsonDocument("getLastRequestStatistics", 1), serializerRegistry.GetSerializer<Dictionary<string, object>>());
    }
}

Dictionary<string, object> stats = database.RunCommand(new GetLastRequestStatisticsCommand());
double requestCharge = (double)stats["RequestCharge"];
```

További információ: gyors útmutató [: Hozzon létre egy .NET-webalkalmazást egy Azure Cosmos DB API](create-mongodb-dotnet.md)használatával a MongoDB.

### <a name="use-the-mongodb-java-driver"></a>A MongoDB Java-illesztőprogram használata


Ha a [hivatalos MongoDB Java-illesztőprogramot](https://mongodb.github.io/mongo-java-driver/)használja, parancsokat futtathat a `runCommand` metódus meghívásával egy `MongoDatabase` objektumon:

```java
Document stats = database.runCommand(new Document("getLastRequestStatistics", 1));
Double requestCharge = stats.getDouble("RequestCharge");
```

További információ: gyors útmutató [: Hozzon létre egy webalkalmazást a MongoDB-hez készült Azure Cosmos DB API-](create-mongodb-java.md)val és a Java SDK-val.

### <a name="use-the-mongodb-nodejs-driver"></a>A MongoDB Node. js-illesztőprogram használata

Ha a [hivatalos MongoDB Node. js-illesztőprogramot](https://mongodb.github.io/node-mongodb-native/)használja, parancsokat futtathat a `command` metódus meghívásával egy `db` objektumon:

```javascript
db.command({ getLastRequestStatistics: 1 }, function(err, result) {
    assert.equal(err, null);
    const requestCharge = result['RequestCharge'];
});
```

További információ: gyors útmutató [: Migráljon egy meglévő MongoDB Node. js-webalkalmazást Azure Cosmos DBba](create-mongodb-nodejs.md).

## <a name="cassandra-api"></a>Cassandra API

Ha a Azure Cosmos DB Cassandra API műveleteit hajtja végre, a rendszer az RU-díjat a bejövő adattartalomban adja `RequestCharge`vissza egy nevű mezőként. Több lehetőség is van az RU-díj lekérésére.

### <a name="use-the-net-sdk"></a>A .NET SDK használata

A [.net SDK](https://www.nuget.org/packages/CassandraCSharpDriver/)használatával lekérheti a bejövő hasznos adatokat egy `Info` `RowSet` objektum tulajdonsága alatt:

```csharp
RowSet rowSet = session.Execute("SELECT table_name FROM system_schema.tables;");
double requestCharge = BitConverter.ToDouble(rowSet.Info.IncomingPayload["RequestCharge"].Reverse().ToArray(), 0);
```

További információ: gyors útmutató [: Hozzon létre egy Cassandra-alkalmazást a .NET SDK és](create-cassandra-dotnet.md)a Azure Cosmos db használatával.

### <a name="use-the-java-sdk"></a>A Java SDK használata

A [Java SDK](https://mvnrepository.com/artifact/com.datastax.cassandra/cassandra-driver-core)használatával lekérheti a bejövő hasznos adatokat, ha meghívja a `getExecutionInfo()` metódust egy `ResultSet` objektumon:

```java
ResultSet resultSet = session.execute("SELECT table_name FROM system_schema.tables;");
Double requestCharge = resultSet.getExecutionInfo().getIncomingPayload().get("RequestCharge").getDouble();
```

További információ: gyors útmutató [: Hozzon létre egy Cassandra-alkalmazást a Java SDK és](create-cassandra-java.md)a Azure Cosmos db használatával.

## <a name="gremlin-api"></a>Gremlin API

Ha a Gremlin API-t használja, több lehetőség is rendelkezésre áll, amelyekkel megkeresheti egy művelet RU-felhasználását egy Azure Cosmos-tárolón. 

### <a name="use-drivers-and-sdk"></a>Illesztőprogramok és SDK használata

A Gremlin API által visszaadott fejlécek egyéni állapot-attribútumokra vannak leképezve, amelyek jelenleg a Gremlin .NET és a Java SDK felületén vannak. A kérés díja a `x-ms-request-charge` kulcs alatt érhető el.

### <a name="use-the-net-sdk"></a>A .NET SDK használata

Az [Gremlin.net SDK](https://www.nuget.org/packages/Gremlin.Net/)használatakor az állapot attribútumai az `StatusAttributes` `ResultSet<>` objektum tulajdonsága alatt érhetők el:

```csharp
ResultSet<dynamic> results = client.SubmitAsync<dynamic>("g.V().count()").Result;
double requestCharge = (double)results.StatusAttributes["x-ms-request-charge"];
```

További információ: gyors útmutató [: .NET-keretrendszer vagy Core-alkalmazás létrehozása egy Azure Cosmos DB Gremlin API-fiók](create-graph-dotnet.md)használatával.

### <a name="use-the-java-sdk"></a>A Java SDK használata

Ha a [Gremlin Java SDK](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver)-t használja, az állapot attribútumait az `statusAttributes()` `ResultSet` objektum metódusának meghívásával kérheti le:

```java
ResultSet results = client.submit("g.V().count()");
Double requestCharge = (Double)results.statusAttributes().get().get("x-ms-request-charge");
```

További információ: gyors útmutató [: Hozzon létre egy Graph-adatbázist a Azure Cosmos DBban a](create-graph-java.md)Java SDK használatával.

## <a name="table-api"></a>Tábla API

Jelenleg az egyetlen SDK, amely a Table Operations RU díját adja vissza, a [.NET Standard SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table). Az `TableResult` objektum egy olyan `RequestCharge` tulajdonságot tesz elérhetővé, amelyet az SDK tölt fel, amikor a Azure Cosmos db Table API használja:

```csharp
CloudTable tableReference = client.GetTableReference("table");
TableResult tableResult = tableReference.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
if (tableResult.RequestCharge.HasValue) // would be false when using Azure Storage Tables
{
    double requestCharge = tableResult.RequestCharge.Value;
}
```

További információ: gyors útmutató [: Hozzon létre egy Table API alkalmazást a .NET SDK és a](create-table-dotnet.md)Azure Cosmos db használatával.

## <a name="next-steps"></a>További lépések

Az RU-felhasználás optimalizálásával kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

* [Kérelemegységek és átviteli sebesség az Azure Cosmos DB-ben](request-units.md)
* [A kiosztott átviteli sebesség költségeinek optimalizálása az Azure Cosmos DB-ben](optimize-cost-throughput.md)
* [A lekérdezési díjak optimalizálása Azure Cosmos DB](optimize-cost-queries.md)
* [Kiosztott átviteli sebesség globális skálázása](scaling-throughput.md)
* [Adatforgalom kiépítése a tárolók és adatbázisok számára](set-throughput.md)
* [Adatátviteli kapacitás kiépítése egy tároló számára](how-to-provision-container-throughput.md)
* [A Azure Cosmos DB metrikáinak monitorozása és hibakeresése](use-metrics.md)
