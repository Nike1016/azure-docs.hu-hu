---
title: Bevezetés az Azure Table Storage és az Azure Cosmos DB Table API Pythonnal való használatával
description: Az Azure Table Storage vagy az Azure Cosmos DB Table API használatával strukturált adatok tárolhatók a felhőben.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: python
ms.topic: sample
ms.date: 04/05/2018
author: wmengmsft
ms.author: wmeng
ms.reviewer: sngun
ms.openlocfilehash: 0f0acc721fd8888953d80976234b431943985ebf
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68356266"
---
# <a name="get-started-with-azure-table-storage-and-the-azure-cosmos-db-table-api-using-python"></a>Bevezetés az Azure Table Storage és az Azure Cosmos DB Table API Pythonnal való használatával

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

Az Azure Table Storage és az Azure Cosmos DB olyan szolgáltatások, amelyek strukturált NoSQL-adatokat tárolnak a felhőben, séma nélküli kulcs-/attribútumtárat biztosítva. Mivel a Table Storage és az Azure Cosmos DB séma nélküli, az adatokat könnyen az alkalmazás változó igényeihez igazíthatja. A Table Storage és a Table API adataihoz számos alkalmazástípus gyorsan és költséghatékonyan férhet hozzá, a költségei pedig jellemzően alacsonyabbak, mint a hagyományos SQL hasonló mennyiségű adathoz való használata esetében.

A Table Storage vagy az Azure Cosmos DB segítségével olyan rugalmas adatkészleteket tárolhat, mint például webalkalmazások felhasználói adatai, címtárak, eszközadatok és bármilyen egyéb metaadat, amelyre a szolgáltatásnak szüksége van. Egy táblán korlátlan számú entitást tárolhat, és egy tárfiók a kapacitásán belül korlátlan számú táblát tartalmazhat.

### <a name="about-this-sample"></a>A minta ismertetése
Ez a minta az [Azure Cosmos DB Table SDK Pythonhoz](https://pypi.python.org/pypi/azure-cosmosdb-table/) használatát mutatja be gyakori Azure Table Storage-forgatókönyvekben. Az SDK neve azt jelzi, hogy az Azure Cosmos DB-hez készült, azonban az Azure Cosmos DB és az Azure Table Storage szolgáltatással egyaránt használható, mivel az egyes szolgáltatásoknak csak a végpontja egyedi. Ezek a forgatókönyvek az alábbiakat bemutató Python-példákkal jellemezhetők:
* Táblák létrehozása és törlése
* Entitások beszúrása és lekérdezése
* Entitások módosítása

A minta forgatókönyveinek végrehajtása közben érdemes megtekinteni a [Pythonhoz készült Azure Cosmos DB SDK API-referenciáját](https://docs.microsoft.com/python/api/overview/azure/cosmosdb?view=azure-python).

## <a name="prerequisites"></a>Előfeltételek

A minta sikeres teljesítéséhez a következőkre lesz szüksége:

- [Python](https://www.python.org/downloads/) 2.7, 3.3, 3.4, 3.5 vagy 3.6
- [Azure Cosmos DB Table SDK Pythonhoz](https://pypi.python.org/pypi/azure-cosmosdb-table/). Ez az SDK az Azure Table Storage-hoz és az Azure Cosmos DB Table API-hoz is csatlakozik.
- [Azure Storage-fiók](../storage/common/storage-quickstart-create-account.md) vagy [Azure Cosmos DB-fiók](https://azure.microsoft.com/try/cosmosdb/)

## <a name="create-an-azure-service-account"></a>Azure-szolgáltatásfiók létrehozása
[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Azure-tárfiók létrehozása
[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Azure Cosmos DB Table API-fiók létrehozása
[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="install-the-azure-cosmos-db-table-sdk-for-python"></a>Az Azure Cosmos DB Table SDK Pythonhoz telepítése

A Storage-fiók létrehozása után a következő lépés az [Azure Cosmos DB Table SDK Pythonhoz](https://pypi.python.org/pypi/azure-cosmosdb-table/) telepítése. Az SDK telepítésének részleteiért tekintse meg a [README.rst](https://github.com/Azure/azure-cosmosdb-python/blob/master/azure-cosmosdb-table/README.rst) fájlt a Cosmos DB Table SDK Pythonhoz adattárában a GitHubon.

## <a name="import-the-tableservice-and-entity-classes"></a>A TableService és az Entity osztály importálása

Az Azure Table service Pythonban található entitásokkal való együttműködéshez a [TableService][py_TableService] és az [Entity][py_Entity] osztályokat kell használnia. Adja hozzá ezt a kódot a Python-fájl elejéhez mindkét osztály importálásához:

```python
from azure.cosmosdb.table.tableservice import TableService
from azure.cosmosdb.table.models import Entity
```

## <a name="connect-to-azure-table-service"></a>Csatlakozás az Azure Table Service-hez

Az Azure Storage Table Service-hez való csatlakozáshoz hozzon létre egy [TableService][py_TableService] objektumot, és adja át a számára a Storage-fiók nevét és fiókkulcsát. A `myaccount` és a `mykey` értéket cserélje le a fiók nevére és kulcsára.

```python
table_service = TableService(account_name='myaccount', account_key='mykey')
```

## <a name="connect-to-azure-cosmos-db"></a>Csatlakozás az Azure Cosmos DB-hez

Az Azure Cosmos DB-hez való csatlakozáshoz másolja a kapcsolati sztringet az Azure Portalról, és hozzon létre egy [TableService][py_TableService] objektumot a másolt kapcsolati sztringgel:

```python
table_service = TableService(connection_string='DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;TableEndpoint=myendpoint;)
```

## <a name="create-a-table"></a>Tábla létrehozása

Hívja meg a [create_table][py_create_table] a tábla létrehozásához.

```python
table_service.create_table('tasktable')
```

## <a name="add-an-entity-to-a-table"></a>Entitás hozzáadása a táblához

Entitás hozzáadásához először létre kell hoznia az entitást képviselő objektumot, majd át kell adni az objektumot az [TableService. insert_entity metódusnak][py_TableService]. Az Entity (entitás) objektum lehet egy szótár vagy egy [entitás][py_Entity]típusú objektum, amely meghatározza az entitás tulajdonságainak nevét és értékeit. Minden entitásnak tartalmaznia kell a szükséges [PartitionKey és RowKey](#partitionkey-and-rowkey) tulajdonságot, az entitás számára meghatározott egyéb tulajdonságokon kívül.

Ez a példa egy entitást jelképező szótár objektumot hoz létre, majd átadja a [insert_entity][py_insert_entity] metódusnak, hogy hozzáadja a táblához:

```python
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001',
        'description': 'Take out the trash', 'priority': 200}
table_service.insert_entity('tasktable', task)
```

Ez a példa egy [Entity][py_Entity] objektumot hoz létre, majd átadja a [insert_entity][py_insert_entity] metódusnak, hogy hozzáadja a táblához:

```python
task = Entity()
task.PartitionKey = 'tasksSeattle'
task.RowKey = '002'
task.description = 'Wash the car'
task.priority = 100
table_service.insert_entity('tasktable', task)
```

### <a name="partitionkey-and-rowkey"></a>PartitionKey és RowKey

Minden entitás számára meg kell adnia a **PartitionKey** és **RowKey** tulajdonságot. Ezek az entitások egyedi azonosítói, amelyek együttesen az entitások elsődleges kulcsát alkotják. Ezen értékekkel gyorsabban végezhet lekérdezést a többi entitástulajdonság használatánál, mivel csak ezek a tulajdonságok vannak indexelve.

A Table Service a **PartitionKey** tulajdonságot használja a táblaentitások intelligens elosztására a tárolási csomópontok között. Az ugyanazzal a **PartitionKey** tulajdonsággal rendelkező entitások tárolása ugyanazon a csomóponton történik. A **RowKey** tulajdonság az entitás egyedi azonosítója azon a partíción belül, amelyikbe az entitás tartozik.

## <a name="update-an-entity"></a>Entitás frissítése

Az entitások összes tulajdonságának frissítéséhez hívja meg a [update_entity][py_update_entity] metódust. Ez a példa bemutatja, hogyan cserélhető le egy meglévő entitás a frissített verzióra:

```python
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001',
        'description': 'Take out the garbage', 'priority': 250}
table_service.update_entity('tasktable', task)
```

Ha a frissítendő entitás már nem létezik, a frissítési művelet sikertelen lesz. Ha olyan entitást szeretne tárolni, amely létezik vagy sem, használja a [insert_or_replace_entity][py_insert_or_replace_entity]. Az alábbi példában az első hívás lecseréli a meglévő entitást. A második hívás beszúr egy új entitást, mivel nem létezik a megadott PartitionKey és RowKey tulajdonsággal rendelkező entitás a táblában.

```python
# Replace the entity created earlier
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001',
        'description': 'Take out the garbage again', 'priority': 250}
table_service.insert_or_replace_entity('tasktable', task)

# Insert a new entity
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '003',
        'description': 'Buy detergent', 'priority': 300}
table_service.insert_or_replace_entity('tasktable', task)
```

> [!TIP]
> A [update_entity][py_update_entity] metódus lecseréli egy meglévő entitás összes tulajdonságát és értékét, amelyet a tulajdonságok meglévő entitásból való eltávolítására is használhat. A [merge_entity][py_merge_entity] metódussal a meglévő entitásokat új vagy módosított tulajdonságértékek használatával frissítheti anélkül, hogy teljesen lecseréli az entitást.

## <a name="modify-multiple-entities"></a>Több entitás módosítása

Annak biztosításához, hogy a Table Service elvégezze a kérés atomi feldolgozását, egy kötegben egyszerre több műveletet is elküldhet. Először használja a [TableBatch][py_TableBatch] osztályt több művelet egyetlen kötegbe való felvételéhez. Ezután hívja meg a [TableService][py_TableService]. [commit_batch][py_commit_batch] a műveleteknek egy atomi műveletben való elküldéséhez. A kötegelten módosítani kívánt entitásoknak ugyanazon a partíción kell lenniük.

Ez a példa két entitást ad hozzá egy kötegben:

```python
from azure.cosmosdb.table.tablebatch import TableBatch
batch = TableBatch()
task004 = {'PartitionKey': 'tasksSeattle', 'RowKey': '004',
           'description': 'Go grocery shopping', 'priority': 400}
task005 = {'PartitionKey': 'tasksSeattle', 'RowKey': '005',
           'description': 'Clean the bathroom', 'priority': 100}
batch.insert_entity(task004)
batch.insert_entity(task005)
table_service.commit_batch('tasktable', batch)
```

A kötegek a környezetkezelő szintaxisával is használhatók:

```python
task006 = {'PartitionKey': 'tasksSeattle', 'RowKey': '006',
           'description': 'Go grocery shopping', 'priority': 400}
task007 = {'PartitionKey': 'tasksSeattle', 'RowKey': '007',
           'description': 'Clean the bathroom', 'priority': 100}

with table_service.batch('tasktable') as batch:
    batch.insert_entity(task006)
    batch.insert_entity(task007)
```

## <a name="query-for-an-entity"></a>Entitás lekérdezése

Egy táblában lévő entitás lekérdezéséhez adja át a PartitionKey és a RowKey a [TableService][py_TableService]. [get_entity][py_get_entity] metódus.

```python
task = table_service.get_entity('tasktable', 'tasksSeattle', '001')
print(task.description)
print(task.priority)
```

## <a name="query-a-set-of-entities"></a>Több entitás lekérdezése

Több entitást is lekérdezhet, ha megadja egy szűrősztringet a **filter** paraméterrel. Ez a példa a Seattle-ben található összes feladatot megkeresi egy szűrőnek a PartitionKey tulajdonságra való alkalmazásával:

```python
tasks = table_service.query_entities(
    'tasktable', filter="PartitionKey eq 'tasksSeattle'")
for task in tasks:
    print(task.description)
    print(task.priority)
```

## <a name="query-a-subset-of-entity-properties"></a>Az entitástulajdonságok egy részének lekérdezése

Azt is szabályozhatja, hogy az egyes entitások mely tulajdonságait adja vissza egy lekérdezés. Ez a *leképezésnek* hívott technika csökkenti a sávszélesség felhasználását, és javíthatja a lekérdezési teljesítményt, főleg a nagy entitások vagy eredményhalmazok esetén. Használja a **select** paramétert, és adja át azon tulajdonságok nevét, amelyeket vissza szeretne küldeni az ügyélnek.

Az alábbi kódban szereplő lekérdezés csak a táblában található entitások leírását adja vissza.

> [!NOTE]
> Az alábbi kódrészlet csak az Azure Storage esetén használható. A Storage Emulator nem támogatja.

```python
tasks = table_service.query_entities(
    'tasktable', filter="PartitionKey eq 'tasksSeattle'", select='description')
for task in tasks:
    print(task.description)
```

## <a name="delete-an-entity"></a>Entitás törlése

Entitás törlése a **PartitionKey** és a **RowKey** a [delete_entity][py_delete_entity] metódusba való átadásával.

```python
table_service.delete_entity('tasktable', 'tasksSeattle', '001')
```

## <a name="delete-a-table"></a>Tábla törlése

Ha már nincs szüksége egy táblára vagy a benne lévő entitásokra, hívja meg a [delete_table][py_delete_table] metódust a tábla végleges törléséhez az Azure Storage-ból.

```python
table_service.delete_table('tasktable')
```

## <a name="next-steps"></a>További lépések

* [A Table API-val történő fejlesztéshez kapcsolódó gyakori kérdések](https://docs.microsoft.com/azure/cosmos-db/faq)
* [Az Azure Cosmos DB SDK Pythonhoz API-referenciája](https://docs.microsoft.com/python/api/overview/azure/cosmosdb?view=azure-python)
* [Python fejlesztői központ](https://azure.microsoft.com/develop/python/)
* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md): Ingyenes, platformfüggetlen alkalmazás, amellyel vizuálisan dolgozhat Azure Storage-szolgáltatásokkal Windows, macOS és Linux rendszereken.
* [A Python használata a Visual Studióban (Windows)](https://docs.microsoft.com/visualstudio/python/overview-of-python-tools-for-visual-studio)


[py_commit_batch]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_create_table]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_delete_entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_get_entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_insert_entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_insert_or_replace_entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_Entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.models.entity?view=azure-python
[py_merge_entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_update_entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_delete_table]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_TableService]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_TableBatch]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
