---
title: Megtudhatja, hogyan védheti meg Azure Cosmos DB az adathozzáférését
description: Ismerje meg a Azure Cosmos DB hozzáférés-vezérlési fogalmait, beleértve a főkulcsokat, a csak olvasható kulcsokat, a felhasználókat és az engedélyeket.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: rimman
ms.openlocfilehash: e321091f2abcff4c385b442ac6474c0592845e2e
ms.sourcegitcommit: 15f7b641a67f3d6cf4fb4b4c11eaee18cf335923
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68602031"
---
# <a name="secure-access-to-data-in-azure-cosmos-db"></a>Biztonságos hozzáférés a Azure Cosmos DB lévő adateléréshez

Ez a cikk áttekintést nyújt a Microsoft Azure Cosmos DBban tárolt adathozzáférés biztonságossá [](https://azure.microsoft.com/services/cosmos-db/)tételéről.

Azure Cosmos DB kétféle kulcsot használ a felhasználók hitelesítéséhez és az adataihoz és erőforrásaihoz való hozzáférés biztosításához. 

|Kulcstípus|További források|
|---|---|
|[Főkulcsok](#master-keys) |Felügyeleti erőforrásokhoz használatos: adatbázis-fiókok, adatbázisok, felhasználók és engedélyek|
|[Erőforrás-tokenek](#resource-tokens)|Alkalmazás-erőforrásokhoz használatos: tárolók, dokumentumok, mellékletek, tárolt eljárások, eseményindítók és UDF|

<a id="master-keys"></a>

## <a name="master-keys"></a>Főkulcsok 

A főkulcsok hozzáférést biztosítanak az adatbázis-fiókhoz tartozó összes felügyeleti erőforráshoz. Főkulcsok:  
- Hozzáférés biztosítása a fiókokhoz, adatbázisokhoz, felhasználókhoz és engedélyekhez. 
- Nem használható a tárolók és a dokumentumok részletes hozzáférésének biztosítására.
- A fiók létrehozása során jönnek létre.
- Bármikor újra létrehozhatók.

Minden fiók két főkulcsból áll: egy elsődleges és egy másodlagos kulcsból. A kettős kulcsok célja, hogy újragenerálja vagy leállítsa a kulcsokat, és folyamatos hozzáférést biztosítson fiókjához és adataihoz. 

A Cosmos DB fiók két főkulcsán kívül két írásvédett kulcs is van. Ezek a csak olvasási jogosultsággal rendelkező kulcsok csak olvasási műveleteket engedélyeznek a fiókon. A csak olvasási jogosultsággal rendelkező kulcsok nem biztosítanak hozzáférést az olvasási engedélyek erőforrásaihoz.

Az elsődleges, másodlagos, írásvédett és írható főkulcsok lekérhető és újragenerálható a Azure Portal használatával. Útmutatásért lásd: [hozzáférési kulcsok megtekintése, másolása és](manage-with-cli.md#regenerate-account-key)újragenerálása.

![Hozzáférés-vezérlés (IAM) a Azure Portal – NoSQL adatbázis biztonságának bemutatása](./media/secure-access-to-data/nosql-database-security-master-key-portal.png)

A főkulcs elforgatásának folyamata egyszerű. Lépjen a Azure Portal a másodlagos kulcs lekéréséhez, majd cserélje le az elsődleges kulcsot a másodlagos kulcsára az alkalmazásban, majd forgassa el az elsődleges kulcsot a Azure Portal.

![Főkulcs elforgatása a Azure Portalban – NoSQL adatbázis biztonságának bemutatása](./media/secure-access-to-data/nosql-database-security-master-key-rotate-workflow.png)

### <a name="code-sample-to-use-a-master-key"></a>Mintakód a főkulcs használatához

Az alábbi mintakód azt szemlélteti, hogyan használható egy Cosmos DB-fiók végpontja és főkulcsa egy DocumentClient létrehozásához és egy adatbázis létrehozásához. 

```csharp
//Read the Azure Cosmos DB endpointUrl and authorization keys from config.
//These values are available from the Azure portal on the Azure Cosmos DB account blade under "Keys".
//NB > Keep these values in a safe and secure location. Together they provide Administrative access to your DocDB account.

private static readonly string endpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
private static readonly SecureString authorizationKey = ToSecureString(ConfigurationManager.AppSettings["AuthorizationKey"]);

client = new DocumentClient(new Uri(endpointUrl), authorizationKey);

// Create Database
Database database = await client.CreateDatabaseAsync(
    new Database
    {
        Id = databaseName
    });
```

<a id="resource-tokens"></a>

## <a name="resource-tokens"></a>Erőforrás-tokenek

Az erőforrás-tokenek hozzáférést biztosítanak az adatbázison belüli alkalmazás-erőforrásokhoz. Erőforrás-tokenek:
- Hozzáférés biztosítása bizonyos tárolók, partíciós kulcsok, dokumentumok, mellékletek, tárolt eljárások, eseményindítók és UDF számára.
- Akkor jön létre, [](#users) amikor egy felhasználó [engedélyt kap egy](#permissions) adott erőforráshoz.
- Akkor jön létre újra, amikor egy engedélyezési erőforrást POST, GET vagy PUT hívás után végeznek el.
- Használjon olyan kivonatoló erőforrás-tokent, amelyet kifejezetten a felhasználóhoz, erőforráshoz és engedélyhez alakítottak ki.
- Az idő egy testreszabható érvényességi időtartammal van kötve. Az alapértelmezett érvényes TimeSpan egy óra. A jogkivonat élettartama azonban explicit módon megadható, legfeljebb öt órára.
- Adjon meg egy biztonságos alternatívát a főkulcs megadásához. 
- Lehetővé teszi az ügyfelek számára a Cosmos DB fiók erőforrásainak olvasását, írását és törlését a megadott engedélyek alapján.

Erőforrás-tokent (Cosmos DB felhasználók és engedélyek létrehozásával) is használhat, ha hozzáférést szeretne biztosítani a Cosmos DB-fiókban lévő erőforrásokhoz egy olyan ügyfél számára, amely nem megbízható a főkulccsal.  

A Cosmos DB erőforrás-tokenek olyan biztonságos alternatívát biztosítanak, amely lehetővé teszi az ügyfelek számára, hogy az Ön által megadott engedélyek alapján beolvassák, írják és töröljék a Cosmos DB-fiókban lévő erőforrásokat, és nincs szükség fő vagy csak olvasható kulcsra.

Íme egy tipikus kialakítási minta, amely alapján az erőforrás-jogkivonatok az ügyfelek számára igényelhetők, hozhatók létre és továbbíthatók:

1. A közepes szintű szolgáltatás úgy van beállítva, hogy kiszolgálja a felhasználók fényképeit használó mobileszköz-alkalmazásokat. 
2. A középső rétegbeli szolgáltatás rendelkezik a Cosmos DB fiók főkulcsával.
3. A Photo alkalmazás telepítve van a végfelhasználói mobileszközökön. 
4. Bejelentkezéskor a Photo alkalmazás létrehozza a felhasználó identitását a középső rétegbeli szolgáltatással. Ez az identitás-létesítési mechanizmus kizárólag az alkalmazásra vonatkozik.
5. Az identitás létrejötte után a középső rétegbeli szolgáltatás engedélyt kér az identitás alapján.
6. A középső rétegbeli szolgáltatás egy erőforrás-tokent küld vissza a telefonos alkalmazásnak.
7. A telefonos alkalmazás továbbra is használhatja az erőforrás-jogkivonatot, hogy közvetlenül hozzáférhessen Cosmos DB erőforrásokhoz az erőforrás-jogkivonat által meghatározott engedélyekkel és az erőforrás-jogkivonat által engedélyezett intervallummal. 
8. Az erőforrás-jogkivonat lejárata után a további kérések 401 jogosulatlan kivételt kapnak.  Ezen a ponton a telefonos alkalmazás újból létrehozza az identitást, és új erőforrás-tokent kér.

    ![Azure Cosmos DB erőforrás-tokenek munkafolyamata](./media/secure-access-to-data/resourcekeyworkflow.png)

Az erőforrás-jogkivonat létrehozását és felügyeletét a natív Cosmos DB ügyféloldali kódtárak kezelik; Ha azonban a REST-t használja, a kérelem/hitelesítés fejléceket kell létrehoznia. További információ a REST-alapú hitelesítési fejlécek létrehozásáról: [Access Control Cosmos db erőforrásokon](https://docs.microsoft.com/rest/api/cosmos-db/access-control-on-cosmosdb-resources) vagy az [SDK](https://github.com/Azure/azure-documentdb-node/blob/master/source/lib/auth.js)-k forráskódján.

Az erőforrás-tokenek létrehozásához vagy közvetítéséhez használt középső rétegű szolgáltatásra például a [ResourceTokenBroker alkalmazásban](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin/UserItems/ResourceTokenBroker/ResourceTokenBroker/Controllers)talál példát.

<a id="users"></a>

## <a name="users"></a>Felhasználók
Cosmos DB felhasználók Cosmos DB-adatbázishoz vannak társítva.  Minden adatbázis nulla vagy több Cosmos DB felhasználót tartalmazhat.  Az alábbi mintakód bemutatja, hogyan hozható létre Cosmos DB felhasználói erőforrás.

```csharp
//Create a user.
User docUser = new User
{
    Id = "mobileuser"
};

docUser = await client.CreateUserAsync(UriFactory.CreateDatabaseUri("db"), docUser);
```

> [!NOTE]
> Minden Cosmos DB felhasználó rendelkezik egy PermissionsLink tulajdonsággal, amely a felhasználóhoz társított [engedélyek](#permissions) listájának lekérésére használható.
> 
> 

<a id="permissions"></a>

## <a name="permissions"></a>Engedélyek
Egy Cosmos DB engedély erőforrás egy Cosmos DB felhasználóhoz van társítva.  Minden felhasználó nulla vagy több Cosmos DB engedélyt is tartalmazhat.  Az engedélyezési erőforrás hozzáférést biztosít egy olyan biztonsági jogkivonathoz, amelyhez a felhasználónak szüksége van egy adott alkalmazás-erőforrás elérésére tett kísérlet során.
Az engedélyezési erőforrások két rendelkezésre álló hozzáférési szintet tartalmazhatnak:

* Összes A felhasználó teljes körű engedéllyel rendelkezik az erőforráson.
* Olvasni A felhasználó csak az erőforrás tartalmát tudja olvasni, de az erőforráson nem hajtható végre írási, frissítési vagy törlési művelet.

> [!NOTE]
> Cosmos DB tárolt eljárások futtatásához a felhasználónak az összes engedéllyel kell rendelkeznie arra a tárolóra, amelyben a tárolt eljárás futni fog.
> 
> 

### <a name="code-sample-to-create-permission"></a>Kód mintája az engedély létrehozásához

Az alábbi mintakód bemutatja, hogyan hozhat létre egy engedélyezési erőforrást, beolvashatja az engedélyezési erőforrás erőforrás-jogkivonatát, és társíthatja az engedélyeket a fent létrehozott [felhasználóhoz](#users) .

```csharp
// Create a permission.
Permission docPermission = new Permission
{
    PermissionMode = PermissionMode.Read,
    ResourceLink = UriFactory.CreateDocumentCollectionUri("db", "collection"),
    Id = "readperm"
};
  
docPermission = await client.CreatePermissionAsync(UriFactory.CreateUserUri("db", "user"), docPermission);
Console.WriteLine(docPermission.Id + " has token of: " + docPermission.Token);
```

Ha megadta a gyűjteményhez tartozó partíciós kulcsot, akkor a gyűjteményre, a dokumentumra és a mellékletek erőforrásaira vonatkozó engedélynek a ResourceLink webhelyen találhatók kívül is tartalmaznia kell a ResourcePartitionKey.

### <a name="code-sample-to-read-permissions-for-user"></a>Kód minta a felhasználó olvasási engedélyeihez

Az adott felhasználóhoz társított összes engedélyezési erőforrás egyszerű beszerzéséhez Cosmos DB minden felhasználói objektumhoz elérhetővé teszi az engedély-hírcsatornát.  A következő kódrészlet bemutatja, hogyan kérhető le a fent létrehozott felhasználóhoz tartozó engedély, hogyan hozható létre egy engedélyezési lista, és hogyan hozható létre új DocumentClient a felhasználó nevében.

```csharp
//Read a permission feed.
FeedResponse<Permission> permFeed = await client.ReadPermissionFeedAsync(
  UriFactory.CreateUserUri("db", "myUser"));
 List<Permission> permList = new List<Permission>();

foreach (Permission perm in permFeed)
{
    permList.Add(perm);
}

DocumentClient userClient = new DocumentClient(new Uri(endpointUrl), permList);
```

## <a name="add-users-and-assign-roles"></a>Felhasználók hozzáadása és szerepkörök társítása

Ha Azure Cosmos DB fiók-olvasóhoz szeretne hozzáférni a felhasználói fiókjához, a következő lépéseket kell elvégeznie a Azure Portalban.

1. Nyissa meg a Azure Portal, és válassza ki Azure Cosmos DB-fiókját.
2. Kattintson a **hozzáférés-vezérlés (iam)** fülre, majd a **+ szerepkör-hozzárendelés hozzáadása**lehetőségre.
3. A **szerepkör-hozzárendelés hozzáadása** panelen, a **szerepkör** mezőben válassza a **Cosmos db fiók-olvasó szerepkör**lehetőséget.
4. A **hozzáférés kiosztása mezőben**válassza az **Azure ad-felhasználó,-csoport vagy-alkalmazás**lehetőséget.
5. Válassza ki a címtárban azt a felhasználót, csoportot vagy alkalmazást, amelyhez hozzáférést szeretne biztosítani.  A címtárban a megjelenítendő név, e-mail-cím vagy objektumazonosító alapján kereshet.
    A kiválasztott felhasználó, csoport vagy alkalmazás megjelenik a kijelölt tagok listájában.
6. Kattintson a **Save** (Mentés) gombra.

Az entitás mostantól képes olvasni Azure Cosmos DB erőforrásait.

## <a name="delete-or-export-user-data"></a>Felhasználói adatértékek törlése vagy exportálása
Azure Cosmos DB lehetővé teszi, hogy az adatbázisban vagy gyűjteményekben található személyes adatait megkeresse, kiválassza, módosítsa és törölje. A Azure Cosmos DB API-kat biztosít a személyes adat megtalálásához és törléséhez, ezért az Ön felelőssége az API-k használata, és a személyes adat törléséhez szükséges logika meghatározása. Az egyes többmodelles API-k (SQL, MongoDB, Gremlin, Cassandra, Table) különböző nyelvi SDK-kat biztosítanak, amelyek a személyes adatkeresésre és törlésre vonatkozó módszereket tartalmaznak. Azt is engedélyezheti, hogy az élettartam [(TTL)](time-to-live.md) szolgáltatás automatikusan töröljön egy adott időszak után az adattörlést anélkül, hogy további költségeket kellene fizetnie.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>További lépések
* Az Cosmos db adatbázis-biztonsággal kapcsolatos további tudnivalókért [lásd: Cosmos db: Adatbázis-](database-security.md)biztonság.
* A Azure Cosmos DB engedélyezési jogkivonatok létrehozásával kapcsolatos további információkért lásd: [Access Control Azure Cosmos db erőforrásokon](https://docs.microsoft.com/rest/api/cosmos-db/access-control-on-cosmosdb-resources).
