---
title: Tárolt eljárások, eseményindítók és felhasználó által definiált függvények hívása Azure Cosmos DB SDK-k használatával
description: Megtudhatja, hogyan regisztrálhat és hívhat meg tárolt eljárásokat, eseményindítókat és felhasználó által definiált függvényeket a Azure Cosmos DB SDK-k használatával
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/21/2019
ms.author: mjbrown
ms.openlocfilehash: e1215441b45e1fdba8d74c9149415f0da5c4c44a
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68360377"
---
# <a name="how-to-register-and-use-stored-procedures-triggers-and-user-defined-functions-in-azure-cosmos-db"></a>Tárolt eljárások, eseményindítók és felhasználó által definiált függvények regisztrálása és használata Azure Cosmos DB

A Azure Cosmos DBban található SQL API támogatja a JavaScriptben írt tárolt eljárások, eseményindítók és felhasználó által definiált függvények (UDF) regisztrálását és meghívását. Használhatja az SQL API [.net](sql-api-sdk-dotnet.md), [.net Core](sql-api-sdk-dotnet-core.md), [Java](sql-api-sdk-java.md), [JavaScript](sql-api-sdk-node.md), [Node. js](sql-api-sdk-node.md)vagy [Python](sql-api-sdk-python.md) SDK-kat a tárolt eljárások regisztrálásához és meghívásához. Miután meghatározta egy vagy több tárolt eljárást, eseményindítót és felhasználó által definiált függvényt, betöltheti és megtekintheti őket a [Azure Portal](https://portal.azure.com/) adatkezelő használatával.

## <a id="stored-procedures"></a>Tárolt eljárások futtatása

A tárolt eljárások JavaScript használatával íródnak. Létrehozhatják, frissíthetik, olvashatják, lekérhetik és törölhetik az Azure Cosmos-tárolóban lévő elemeket. A tárolt eljárások Azure Cosmos DBban való írásával kapcsolatos további információkért lásd: [tárolt eljárások írása Azure Cosmos db](how-to-write-stored-procedures-triggers-udfs.md#stored-procedures) cikkben.

Az alábbi példák bemutatják, hogyan regisztrálhat és hívhat meg egy tárolt eljárást a Azure Cosmos DB SDK-k használatával. A tárolt eljárás forrásaként való mentésekor `spCreateToDoItem.js`tekintse meg a [dokumentum létrehozása](how-to-write-stored-procedures-triggers-udfs.md#create-an-item) című témakört.

> [!NOTE]
> Particionált tárolók esetén a tárolt eljárás végrehajtásakor meg kell adni egy partíciós kulcs értékét a kérés beállításai között. A tárolt eljárásokat a rendszer mindig a partíciós kulcsra szűkíti. A másik partíciós kulcs értékkel rendelkező elemek nem lesznek láthatók a tárolt eljárásban. Ez is a triggerekre is vonatkozik.

### <a name="stored-procedures---net-sdk"></a>Tárolt eljárások – .NET SDK

Az alábbi példa bemutatja, hogyan regisztrálhat egy tárolt eljárást a .NET SDK használatával:

```csharp
string storedProcedureId = "spCreateToDoItem";
StoredProcedure newStoredProcedure = new StoredProcedure
   {
       Id = storedProcedureId,
       Body = File.ReadAllText($@"..\js\{storedProcedureId}.js")
   };
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
var response = await client.CreateStoredProcedureAsync(containerUri, newStoredProcedure);
StoredProcedure createdStoredProcedure = response.Resource;
```

A következő kód bemutatja, hogyan hívhat meg egy tárolt eljárást a .NET SDK használatával:

```csharp
dynamic newItem = new
{
    category = "Personal",
    name = "Groceries",
    description = "Pick up strawberries",
    isComplete = false
};

Uri uri = UriFactory.CreateStoredProcedureUri("myDatabase", "myContainer", "spCreateToDoItem");
RequestOptions options = new RequestOptions { PartitionKey = new PartitionKey("Personal") };
var result = await client.ExecuteStoredProcedureAsync<string>(uri, options, newItem);
var id = result.Response;
```

### <a name="stored-procedures---java-sdk"></a>Tárolt eljárások – Java SDK

Az alábbi példa bemutatja, hogyan regisztrálhat egy tárolt eljárást a Java SDK használatával:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
StoredProcedure newStoredProcedure = new StoredProcedure(
    "{" +
        "  'id':'spCreateToDoItem'," +
        "  'body':" + new String(Files.readAllBytes(Paths.get("..\\js\\spCreateToDoItem.js"))) +
    "}");
//toBlocking() blocks the thread until the operation is complete and is used only for demo.  
StoredProcedure createdStoredProcedure = asyncClient.createStoredProcedure(containerLink, newStoredProcedure, null)
    .toBlocking().single().getResource();
```

A következő kód bemutatja, hogyan hívhat meg egy tárolt eljárást a Java SDK használatával:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String sprocLink = String.format("%s/sprocs/%s", containerLink, "spCreateToDoItem");
final CountDownLatch successfulCompletionLatch = new CountDownLatch(1);

class ToDoItem {
    public String category;
    public String name;
    public String description;
    public boolean isComplete;
}

ToDoItem newItem = new ToDoItem();
newItem.category = "Personal";
newItem.name = "Groceries";
newItem.description = "Pick up strawberries";
newItem.isComplete = false;

RequestOptions requestOptions = new RequestOptions();
requestOptions.setPartitionKey(new PartitionKey("Personal"));

Object[] storedProcedureArgs = new Object[] { newItem };
asyncClient.executeStoredProcedure(sprocLink, requestOptions, storedProcedureArgs)
    .subscribe(storedProcedureResponse -> {
        String storedProcResultAsString = storedProcedureResponse.getResponseAsString();
        successfulCompletionLatch.countDown();
        System.out.println(storedProcedureResponse.getActivityId());
    }, error -> {
        System.err.println("an error occurred while executing the stored procedure: actual cause: "
                + error.getMessage());
    });

successfulCompletionLatch.await();
```

### <a name="stored-procedures---javascript-sdk"></a>Tárolt eljárások – JavaScript SDK

Az alábbi példa bemutatja, hogyan regisztrálhat egy tárolt eljárást a JavaScript SDK használatával

```javascript
const container = client.database("myDatabase").container("myContainer");
const sprocId = "spCreateToDoItem";
await container.storedProcedures.create({
    id: sprocId,
    body: require(`../js/${sprocId}`)
});
```

A következő kód bemutatja, hogyan hívhat meg egy tárolt eljárást a JavaScript SDK használatával:

```javascript
const newItem = [{
    category: "Personal",
    name: "Groceries",
    description: "Pick up strawberries",
    isComplete: false
}];
const container = client.database("myDatabase").container("myContainer");
const sprocId = "spCreateToDoItem";
const {body: result} = await container.storedProcedure(sprocId).execute(newItem, {partitionKey: newItem[0].category});
```

### <a name="stored-procedures---python-sdk"></a>Tárolt eljárások – Python SDK

Az alábbi példa bemutatja, hogyan regisztrálhat egy tárolt eljárást a Python SDK használatával

```python
with open('../js/spCreateToDoItem.js') as file:
    file_contents = file.read()
container_link = 'dbs/myDatabase/colls/myContainer'
sproc_definition = {
    'id': 'spCreateToDoItem',
    'serverScript': file_contents,
}
sproc = client.CreateStoredProcedure(container_link, sproc_definition)
```

A következő kód bemutatja, hogyan hívhat meg egy tárolt eljárást a Python SDK használatával

```python
sproc_link = 'dbs/myDatabase/colls/myContainer/sprocs/spCreateToDoItem'
new_item = [{
    'category':'Personal',
    'name':'Groceries',
    'description':'Pick up strawberries',
    'isComplete': False
}]
client.ExecuteStoredProcedure(sproc_link, new_item, {'partitionKey': 'Personal'}
```

## <a id="pre-triggers"></a>Az eseményindítók futtatása

Az alábbi példák bemutatják, hogyan regisztrálhat és hívhat meg egy előindítást az Azure Cosmos DB SDK-k használatával. Tekintse meg az [Indítás előtti példát](how-to-write-stored-procedures-triggers-udfs.md#pre-triggers) , mivel az előtrigger forrásaként a rendszer menti `trgPreValidateToDoItemTimestamp.js`a következőt:.

A végrehajtásakor az eseményindítók átadása a RequestOptions objektumba az eseményindító nevének `PreTriggerInclude` megadásával, majd a lista objektumban való átadásával történik.

> [!NOTE]
> Annak ellenére, hogy az trigger neve listaként van átadva, a művelet végrehajtása csak egy triggert tud végrehajtani.

### <a name="pre-triggers---net-sdk"></a>Előzetes eseményindítók – .NET SDK

A következő kód bemutatja, hogyan regisztrálhat egy pre-triggert a .NET SDK használatával:

```csharp
string triggerId = "trgPreValidateToDoItemTimestamp";
Trigger trigger = new Trigger
{
    Id =  triggerId,
    Body = File.ReadAllText($@"..\js\{triggerId}.js"),
    TriggerOperation = TriggerOperation.Create,
    TriggerType = TriggerType.Pre
};
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.CreateTriggerAsync(containerUri, trigger);
```

A következő kód bemutatja, hogyan hívhat meg egy előzetes triggert a .NET SDK használatával:

```csharp
dynamic newItem = new
{
    category = "Personal",
    name = "Groceries",
    description = "Pick up strawberries",
    isComplete = false
};

Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
RequestOptions requestOptions = new RequestOptions { PreTriggerInclude = new List<string> { "trgPreValidateToDoItemTimestamp" } };
await client.CreateDocumentAsync(containerUri, newItem, requestOptions);
```

### <a name="pre-triggers---java-sdk"></a>Előzetes eseményindítók – Java SDK

A következő kód bemutatja, hogyan regisztrálhat egy pre-triggert a Java SDK használatával:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String triggerId = "trgPreValidateToDoItemTimestamp";
Trigger trigger = new Trigger();
trigger.setId(triggerId);
trigger.setBody(new String(Files.readAllBytes(Paths.get(String.format("..\\js\\%s.js", triggerId)));
trigger.setTriggerOperation(TriggerOperation.Create);
trigger.setTriggerType(TriggerType.Pre);
//toBlocking() blocks the thread until the operation is complete and is used only for demo. 
Trigger createdTrigger = asyncClient.createTrigger(containerLink, trigger, new RequestOptions()).toBlocking().single().getResource();
```

A következő kód bemutatja, hogyan hívhat meg egy pre-triggert a Java SDK használatával:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
    Document item = new Document("{ "
            + "\"category\": \"Personal\", "
            + "\"name\": \"Groceries\", "
            + "\"description\": \"Pick up strawberries\", "
            + "\"isComplete\": false, "
            + "}"
            );
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPreTriggerInclude(Arrays.asList("trgPreValidateToDoItemTimestamp"));
//toBlocking() blocks the thread until the operation is complete and is used only for demo. 
asyncClient.createDocument(containerLink, item, requestOptions, false).toBlocking();
```

### <a name="pre-triggers---javascript-sdk"></a>Előzetes eseményindítók – JavaScript SDK

A következő kód bemutatja, hogyan regisztrálhat egy pre-triggert a JavaScript SDK használatával:

```javascript
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPreValidateToDoItemTimestamp";
await container.triggers.create({
    id: triggerId,
    body: require(`../js/${triggerId}`),
    triggerOperation: "create",
    triggerType: "pre"
});
```

A következő kód bemutatja, hogyan hívhat meg egy előzetes triggert a JavaScript SDK használatával:

```javascript
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPreValidateToDoItemTimestamp";
await container.items.create({
    category: "Personal",
    name = "Groceries",
    description = "Pick up strawberries",
    isComplete = false
}, {preTriggerInclude: [triggerId]});
```

### <a name="pre-triggers---python-sdk"></a>Előzetes eseményindítók – Python SDK

A következő kód bemutatja, hogyan regisztrálhat egy előzetes triggert a Python SDK használatával:

```python
with open('../js/trgPreValidateToDoItemTimestamp.js') as file:
    file_contents = file.read()
container_link = 'dbs/myDatabase/colls/myContainer'
trigger_definition = {
    'id': 'trgPreValidateToDoItemTimestamp',
    'serverScript': file_contents,
    'triggerType': documents.TriggerType.Pre,
    'triggerOperation': documents.TriggerOperation.Create
}
trigger = client.CreateTrigger(container_link, trigger_definition)
```

A következő kód bemutatja, hogyan hívhat meg egy előzetes triggert a Python SDK használatával:

```python
container_link = 'dbs/myDatabase/colls/myContainer'
item = {'category': 'Personal', 'name': 'Groceries',
        'description': 'Pick up strawberries', 'isComplete': False}
client.CreateItem(container_link, item, {
                  'preTriggerInclude': 'trgPreValidateToDoItemTimestamp'})
```

## <a id="post-triggers"></a>Az eseményindítók futtatása

Az alábbi példák bemutatják, hogyan regisztrálhat egy post-triggert a Azure Cosmos DB SDK-k használatával. Tekintse meg a [trigger utáni példát](how-to-write-stored-procedures-triggers-udfs.md#post-triggers) , mivel a rendszer a post-trigger forrását menti `trgPostUpdateMetadata.js`.

### <a name="post-triggers---net-sdk"></a>Eseményindítók után – .NET SDK

A következő kód bemutatja, hogyan regisztrálhat egy triggert a .NET SDK használatával:

```csharp
string triggerId = "trgPostUpdateMetadata";
Trigger trigger = new Trigger
{
    Id = triggerId,
    Body = File.ReadAllText($@"..\js\{triggerId}.js"),
    TriggerOperation = TriggerOperation.Create,
    TriggerType = TriggerType.Post
};
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.CreateTriggerAsync(containerUri, trigger);
```

A következő kód bemutatja, hogyan hívhat meg egy triggert a .NET SDK használatával:

```csharp
var newItem = { 
    name: "artist_profile_1023",
    artist: "The Band",
    albums: ["Hellujah", "Rotators", "Spinning Top"]
};

RequestOptions options = new RequestOptions { PostTriggerInclude = new List<string> { "trgPostUpdateMetadata" } };
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.createDocumentAsync(containerUri, newItem, options);
```

### <a name="post-triggers---java-sdk"></a>Triggerek utáni – Java SDK

A következő kód bemutatja, hogyan regisztrálhat egy post-triggert a Java SDK használatával:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String triggerId = "trgPostUpdateMetadata";
Trigger trigger = new Trigger();
trigger.setId(triggerId);
trigger.setBody(new String(Files.readAllBytes(Paths.get(String.format("..\\js\\%s.js", triggerId)))));
trigger.setTriggerOperation(TriggerOperation.Create);
trigger.setTriggerType(TriggerType.Post);
Trigger createdTrigger = asyncClient.createTrigger(containerLink, trigger, new RequestOptions()).toBlocking().single().getResource();
```

A következő kód bemutatja, hogyan hívhat meg egy triggert a Java SDK használatával:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
Document item = new Document(String.format("{ "
    + "\"name\": \"artist_profile_1023\", "
    + "\"artist\": \"The Band\", "
    + "\"albums\": [\"Hellujah\", \"Rotators\", \"Spinning Top\"]"
    + "}"
));
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPostTriggerInclude(Arrays.asList("trgPostUpdateMetadata"));
//toBlocking() blocks the thread until the operation is complete, and is used only for demo.
asyncClient.createDocument(containerLink, item, requestOptions, false).toBlocking();
```

### <a name="post-triggers---javascript-sdk"></a>Triggerek – JavaScript SDK

A következő kód bemutatja, hogyan regisztrálhat egy triggert a JavaScript SDK használatával:

```javascript
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPostUpdateMetadata";
await container.triggers.create({
    id: triggerId,
    body: require(`../js/${triggerId}`),
    triggerOperation: "create",
    triggerType: "post"
});
```

A következő kód bemutatja, hogyan hívhat meg egy triggert a JavaScript SDK használatával:

```javascript
const item = {
    name: "artist_profile_1023",
    artist: "The Band",
    albums: ["Hellujah", "Rotators", "Spinning Top"]
};
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPostUpdateMetadata";
await container.items.create(item, {postTriggerInclude: [triggerId]});
```

### <a name="post-triggers---python-sdk"></a>Triggerek utáni – Python SDK

A következő kód bemutatja, hogyan regisztrálhat egy triggert a Python SDK használatával:

```python
with open('../js/trgPostUpdateMetadata.js') as file:
    file_contents = file.read()
container_link = 'dbs/myDatabase/colls/myContainer'
trigger_definition = {
    'id': 'trgPostUpdateMetadata',
    'serverScript': file_contents,
    'triggerType': documents.TriggerType.Post,
    'triggerOperation': documents.TriggerOperation.Create
}
trigger = client.CreateTrigger(container_link, trigger_definition)
```

A következő kód bemutatja, hogyan hívhat meg egy triggert a Python SDK használatával:

```python
container_link = 'dbs/myDatabase/colls/myContainer'
item = {'name': 'artist_profile_1023', 'artist': 'The Band',
        'albums': ['Hellujah', 'Rotators', 'Spinning Top']}
client.CreateItem(container_link, item, {
                  'postTriggerInclude': 'trgPostUpdateMetadata'})
```

## <a id="udfs"></a>Felhasználó által definiált függvények használata

Az alábbi példák bemutatják, hogyan regisztrálhat egy felhasználó által definiált függvényt a Azure Cosmos DB SDK-k használatával. Tekintse meg ezt a [felhasználó által definiált függvényt, például](how-to-write-stored-procedures-triggers-udfs.md#udfs) az utólagos trigger `udfTax.js`forrását.

### <a name="user-defined-functions---net-sdk"></a>Felhasználó által definiált függvények – .NET SDK

A következő kód bemutatja, hogyan regisztrálhat egy felhasználó által definiált függvényt a .NET SDK használatával:

```csharp
string udfId = "Tax";
var udfTax = new UserDefinedFunction
{
    Id = udfId,
    Body = File.ReadAllText($@"..\js\{udfId}.js"),
};

Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.CreateUserDefinedFunctionAsync(containerUri, udfTax);

```

A következő kód bemutatja, hogyan hívhat meg egy felhasználó által definiált függvényt a .NET SDK használatával:

```csharp
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
var results = client.CreateDocumentQuery<dynamic>(containerUri, "SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000"));

foreach (var result in results)
{
    //iterate over results
}
```

### <a name="user-defined-functions---java-sdk"></a>Felhasználó által definiált függvények – Java SDK

A következő kód bemutatja, hogyan regisztrálhat egy felhasználó által definiált függvényt a Java SDK használatával:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String udfId = "Tax";
UserDefinedFunction udf = new UserDefinedFunction();
udf.setId(udfId);
udf.setBody(new String(Files.readAllBytes(Paths.get(String.format("..\\js\\%s.js", udfId)))));
//toBlocking() blocks the thread until the operation is complete and is used only for demo.
UserDefinedFunction createdUDF = client.createUserDefinedFunction(containerLink, udf, new RequestOptions()).toBlocking().single().getResource();
```

A következő kód bemutatja, hogyan hívhat meg egy felhasználó által definiált függvényt a Java SDK használatával:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
Observable<FeedResponse<Document>> queryObservable = client.queryDocuments(containerLink, "SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000", new FeedOptions());
final CountDownLatch completionLatch = new CountDownLatch(1);
queryObservable.subscribe(
        queryResultPage -> {
            System.out.println("Got a page of query result with " +
                    queryResultPage.getResults().size());
        },
        // terminal error signal
        e -> {
            e.printStackTrace();
            completionLatch.countDown();
        },

        // terminal completion signal
        () -> {
            completionLatch.countDown();
        });
completionLatch.await();
```

### <a name="user-defined-functions---javascript-sdk"></a>Felhasználó által definiált függvények – JavaScript SDK

A következő kód bemutatja, hogyan regisztrálhat egy felhasználó által definiált függvényt a JavaScript SDK használatával:

```javascript
const container = client.database("myDatabase").container("myContainer");
const udfId = "Tax";
await container.userDefinedFunctions.create({
    id: udfId,
    body: require(`../js/${udfId}`)
```

A következő kód bemutatja, hogyan hívhat meg egy felhasználó által definiált függvényt a JavaScript SDK használatával:

```javascript
const container = client.database("myDatabase").container("myContainer");
const sql = "SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000";
const {result} = await container.items.query(sql).toArray();
```

### <a name="user-defined-functions---python-sdk"></a>Felhasználó által definiált függvények – Python SDK

A következő kód bemutatja, hogyan regisztrálhat egy felhasználó által definiált függvényt a Python SDK használatával:

```python
with open('../js/udfTax.js') as file:
    file_contents = file.read()
container_link = 'dbs/myDatabase/colls/myContainer'
udf_definition = {
    'id': 'Tax',
    'serverScript': file_contents,
}
udf = client.CreateUserDefinedFunction(container_link, udf_definition)
```

A következő kód bemutatja, hogyan hívhat meg egy felhasználó által definiált függvényt a Python SDK használatával:

```python
container_link = 'dbs/myDatabase/colls/myContainer'
results = list(client.QueryItems(
    container_link, 'SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000'))
```

## <a name="next-steps"></a>További lépések

További fogalmak és útmutató: tárolt eljárások, eseményindítók és felhasználó által definiált függvények írása vagy használata Azure Cosmos DBban:

- [Azure Cosmos DB tárolt eljárások, eseményindítók és felhasználó által definiált függvények használata Azure Cosmos DB](stored-procedures-triggers-udfs.md)
- [A JavaScript nyelvi integrált lekérdezési API használata a Azure Cosmos DBban](javascript-query-api.md)
- [Tárolt eljárások, eseményindítók és felhasználó által definiált függvények írása Azure Cosmos DB](how-to-write-stored-procedures-triggers-udfs.md)
- [Tárolt eljárások és eseményindítók írása a JavaScript lekérdezési API használatával Azure Cosmos DB](how-to-write-javascript-query-api.md)
