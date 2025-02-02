---
title: Az Azure Table Storage és az Azure Cosmos DB Table API használata a Ruby nyelvvel
description: Az Azure Table Storage vagy az Azure Cosmos DB Table API használatával strukturált adatok tárolhatók a felhőben.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: ruby
ms.topic: sample
ms.date: 04/05/2018
author: wmengmsft
ms.author: wmeng
ms.reviewer: sngun
ms.openlocfilehash: 3603455674485a505a7dbc969554a881947940ae
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62130534"
---
# <a name="how-to-use-azure-table-storage-and-the-azure-cosmos-db-table-api-with-ruby"></a>Az Azure Table Storage és az Azure Cosmos DB Table API használata a Ruby nyelvvel
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

## <a name="overview"></a>Áttekintés
Ez az útmutató bemutatja, hogyan hajthat végre gyakori forgatókönyveket az Azure Table szolgáltatás és az Azure Cosmos DB Table API használatával. A kódminták Ruby nyelven íródtak, és az [Azure Storage Table Ruby programnyelvhez készült ügyféloldali kódtárát](https://github.com/azure/azure-storage-ruby/tree/master/table) használják. Az ismertetett forgatókönyvek a **táblák létrehozásával és törlésével, valamint a táblaentitások beszúrásával és lekérdezésével** foglalkoznak.

## <a name="create-an-azure-service-account"></a>Azure-szolgáltatásfiók létrehozása
[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Azure-tárfiók létrehozása
[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

### <a name="create-an-azure-cosmos-db-account"></a>Azure Cosmos DB-fiók létrehozása
[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="add-access-to-storage-or-azure-cosmos-db"></a>Hozzáférés hozzáadása egy Storage- vagy Azure Cosmos DB-fiókhoz
Az Azure Storage vagy az Azure Cosmos DB használatához töltse le és használja a Ruby Azure-csomagot, amelyben a Table REST-szolgáltatásokkal kommunikáló egyszerűsített kódtárak találhatók.

### <a name="use-rubygems-to-obtain-the-package"></a>A csomag beszerzése a RubyGems használatával
1. Használjon egy parancssori felületet, mint például a **PowerShell** (Windows), a **Terminal** (Mac), vagy a **Bash** (Unix).
2. Gépelje be a parancssorba a **gem install azure-storage-table** parancsot a Gem és a függőségek telepítéséhez.

### <a name="import-the-package"></a>A csomag importálása
Egy szabadon választott szövegszerkesztővel adja hozzá a következőket a Ruby fájl elejéhez, azon a helyen, ahol a Storage-et használni kívánja:

```ruby
require "azure/storage/table"
```

## <a name="add-an-azure-storage-connection"></a>Azure Storage-kapcsolat hozzáadása
Az Azure Storage-modul az **AZURE_STORAGE_ACCOUNT** és **AZURE_STORAGE_ACCESS_KEY** környezeti változókat olvassa az Azure Storage-fiókhoz való csatlakozáshoz szükséges információkért. Ha ezek a környezeti változók nincsenek beállítva, meg kell adnia a fiókadatokat a **Azure::Storage::Table::TableService** használatát megelőzően a következő kóddal:

```ruby
Azure.config.storage_account_name = "<your Azure Storage account>"
Azure.config.storage_access_key = "<your Azure Storage access key>"
```

Ezeket az értékeket a következőképp kérheti le egy klasszikus vagy Resource Manager-tárfiókból az Azure Portalon:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Lépjen a használni kívánt tárfiókra.
3. A jobb oldali Beállítások panelen kattintson az **Hozzáférési kulcsok** lehetőségre.
4. A megjelenő Hozzáférési kulcsok panelen láthatja az 1. és a 2. hozzáférési kulcsot. Ezek bármelyikét használhatja.
5. Kattintson a másolás gombra, hogy a kulcsot a vágólapra másolja.

## <a name="add-an-azure-cosmos-db-connection"></a>Azure Cosmos DB-kapcsolat hozzáadása
Az Azure Cosmos DB-hez való csatlakozáshoz másolja a kapcsolati sztringet az Azure Portalról, és hozzon létre egy **Client** objektumot a másolt kapcsolati sztringgel: A **Client** objektumot továbbadhatja, amikor létrehozza a **TableService** objektumot:

```ruby
common_client = Azure::Storage::Common::Client.create(storage_account_name:'myaccount', storage_access_key:'mykey', storage_table_host:'mycosmosdb_endpoint')
table_client = Azure::Storage::Table::TableService.new(client: common_client)
```

## <a name="create-a-table"></a>Tábla létrehozása
Az **Azure::Storage::Table::TableService** objektum lehetővé teszi a táblákkal és entitásokkal való műveletvégzést. Egy tábla létrehozásához használja a **create_table()** metódust. Az alábbi példa létrehoz egy táblát vagy kinyomtatja a hibát, ha van.

```ruby
azure_table_service = Azure::Storage::Table::TableService.new
begin
    azure_table_service.create_table("testtable")
rescue
    puts $!
end
```

## <a name="add-an-entity-to-a-table"></a>Entitás hozzáadása a táblához
Entitás hozzáadásához először hozzon létre egy kivonatobjektumot, amely meghatározza az entitástulajdonságokat. Vegye figyelembe, hogy minden entitáshoz meg kell adnia egy **PartitionKey** és egy **RowKey** tulajdonságot. Ezek az entitások egyedi azonosítói, és az értékeik sokkal gyorsabban lekérdezhetők, mint a többi tulajdonság. Az Azure Storage a **PartitionKey** tulajdonságot használja a táblaentitások automatikus elosztására számos tárolási csomópont között. Az ugyanazzal a **PartitionKey** tulajdonsággal rendelkező entitások tárolása ugyanazon a csomóponton történik. A **RowKey** tulajdonság az entitás egyedi azonosítója azon a partíción belül, amelyikbe az entitás tartozik.

```ruby
entity = { "content" => "test entity",
    :PartitionKey => "test-partition-key", :RowKey => "1" }
azure_table_service.insert_entity("testtable", entity)
```

## <a name="update-an-entity"></a>Entitás frissítése
Több metódus is rendelkezésre áll a meglévő entitások frissítéséhez:

* **update_entity():** Cserélje ki egy létező entitásba frissítéséhez.
* **merge_entity():** Frissíti egy meglévő entitásra úgy, hogy új tulajdonságértékeket egyesítjük a már létező entitásba.
* **insert_or_merge_entity():** Frissíti egy meglévő entitásra, lecserélésével. Ha még nincsen entitás, beszúr egy újat:
* **insert_or_replace_entity():** Frissíti egy meglévő entitásra úgy, hogy új tulajdonságértékeket egyesítjük a már létező entitásba. Ha még nincsen entitás, beszúr egy újat.

Az alábbi példa azt mutatja be, hogyan frissítheti az entitásokat az **update_entity()** használatával:

```ruby
entity = { "content" => "test entity with updated content",
    :PartitionKey => "test-partition-key", :RowKey => "1" }
azure_table_service.update_entity("testtable", entity)
```

Ha az **update_entity()** és a **merge_entity()** használatakor a frissítendő entitás nem létezik, akkor a frissítési művelet sikertelen lesz. Ezért, ha tárolni szeretne egy entitást függetlenül attól, hogy már létezik-e, használja az **insert_or_replace_entity()** vagy **insert_or_merge_entity()** metódusokat.

## <a name="work-with-groups-of-entities"></a>Entitáscsoportok használata
Annak biztosításához, hogy a kiszolgáló elvégezze a kérés elemi feldolgozását, néha érdemes több műveletet egyszerre, egy kötegben elküldeni. Ehhez először hozzon létre egy **Batch**-objektumot, majd használja az **execute_batch()** metódust a **TableService**-en. Az alábbi példa két entitás egy kötegben való elküldését mutatja be (az entitások RowKey tulajdonságának értéke 2 és 3). Vegye figyelembe, hogy ez csak az azonos PartitionKey tulajdonsággal rendelkező entitások esetén működik.

```ruby
azure_table_service = Azure::TableService.new
batch = Azure::Storage::Table::Batch.new("testtable",
    "test-partition-key") do
    insert "2", { "content" => "new content 2" }
    insert "3", { "content" => "new content 3" }
end
results = azure_table_service.execute_batch(batch)
```

## <a name="query-for-an-entity"></a>Entitás lekérdezése
Egy táblában szereplő entitás lekérdezéséhez használja a **get_entity** metódust a tábla nevének, valamint a **PartitionKey** és **RowKey** tulajdonságok átadásával.

```ruby
result = azure_table_service.get_entity("testtable", "test-partition-key",
    "1")
```

## <a name="query-a-set-of-entities"></a>Több entitás lekérdezése
Egy táblában szereplő több entitás lekérdezéséhez hozzon létre egy lekérdezési kivonatobjektumot, majd használja a **query_entities()** metódust. A következő példa bemutatja, hogyan kérhető le az összes olyan entitás, amelynek ugyanaz a **PartitionKey** tulajdonsága:

```ruby
query = { :filter => "PartitionKey eq 'test-partition-key'" }
result, token = azure_table_service.query_entities("testtable", query)
```

> [!NOTE]
> Ha az eredménykészlet túl nagy ahhoz, hogy egyetlen lekérdezés vissza tudja adni, a további oldalak lekéréséhez a lekérdezés egy folytatási tokent ad vissza.
>
>

## <a name="query-a-subset-of-entity-properties"></a>Az entitástulajdonságok egy részének lekérdezése
Egy táblalekérdezéssel egy entitásnak csak bizonyos tulajdonságait is lekérdezheti. Ez a „leképezésnek” hívott technika csökkenti a sávszélesség felhasználását, és javítja a lekérdezési teljesítményt, főleg a nagy entitások esetében. Használja a select záradékot, és adja át azon tulajdonságok nevét, amelyeket továbbítani kíván az ügyfélnek.

```ruby
query = { :filter => "PartitionKey eq 'test-partition-key'",
    :select => ["content"] }
result, token = azure_table_service.query_entities("testtable", query)
```

## <a name="delete-an-entity"></a>Entitás törlése
Egy entitás törléséhez használja a **delete_entity()** metódust. Adja át az entitást tartalmazó tábla nevét, valamint az entitás PartitionKey és RowKey tulajdonságait.

```ruby
azure_table_service.delete_entity("testtable", "test-partition-key", "1")
```

## <a name="delete-a-table"></a>Tábla törlése
Egy entitás törléséhez használja a **delete_table()** metódust, majd adja át a törölni kívánt tábla nevét.

```ruby
azure_table_service.delete_table("testtable")
```

## <a name="next-steps"></a>További lépések

* A [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) egy ingyenes, önálló alkalmazás, amelynek segítségével vizuálisan dolgozhat Azure Storage-adatokkal Windows, macOS és Linux rendszereken.
* [Ruby fejlesztői központ](https://azure.microsoft.com/develop/ruby/)
* [A Microsoft Azure Storage Table ügyféloldali kódtára a Rubyhoz](https://github.com/azure/azure-storage-ruby/tree/master/table) 

