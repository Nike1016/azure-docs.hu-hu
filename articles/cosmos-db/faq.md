---
title: Az Azure Cosmos DB API kapcsolatos gyakori kérdések
description: Válaszok az Azure Cosmos DB egy globálisan elosztott, többmodelles adatbázis-szolgáltatás – gyakori kérdések. Ismerje meg a kapacitás, a teljesítményszintek és a méretezés.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 5cbbd507352471f5e093ef018de4b0c849aad442
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/15/2019
ms.locfileid: "68000798"
---
# <a name="frequently-asked-questions-about-different-apis-in-azure-cosmos-db"></a>Az Azure Cosmos DB API kapcsolatos gyakori kérdések

### <a name="what-are-the-typical-use-cases-for-azure-cosmos-db"></a>Melyek a tipikus használati esetek, az Azure Cosmos DB?

Azure Cosmos DB a megfelelő választás az olyan új webes, mobil-, játék-, és fontos, ahol automatikus méretezés, a kiszámítható teljesítmény, a gyors ezredmásodperces válaszidők sorrendje, illetve a épülő lekérdezés képessége sémamentes adatokra IoT-alkalmazásokat. Az Azure Cosmos DB adatmodelljeinek gyors fejlesztési lehetőségeket és az alkalmazás folyamatos ismétlését. Felhasználó által létrehozott tartalom és adatokat kezelő alkalmazások [Azure Cosmos DB gyakori alkalmazási helyzetei](use-cases.md).

### <a name="how-does-azure-cosmos-db-offer-predictable-performance"></a>Hogyan kínál a Azure Cosmos DB kiszámítható teljesítményt?

A [kérelemegység](request-units.md) (RU) az Azure Cosmos DB-ben a teljesítmény mértékegysége. A 1RU átviteli sebessége megfelel egy 1 KB-os dokumentum lekérésének átviteli sebességének. Olvasási, írási, SQL-lekérdezések és a tárolt eljárás-végrehajtás, beleértve az Azure Cosmos DB-ben minden művelet értéke determinisztikus RU a művelet végrehajtásához szükséges teljesítmény alapján. Szem előtt tartva CPU, IO, és a memória és azok minden adatátviteli teljesítmény, helyett alkalmazásteljesítményre RU egyetlen mérték.

Az egyes Azure Cosmos-tárolók kiosztott átviteli sebességgel konfigurálhatók a másodpercenkénti adatátviteli sebesség alapján. Bármilyen méret esetén az alkalmazások benchmark egyes kérelmeket Kérelemegység-értékük, és kezelje az összes kérelem összes kérelemegységének tároló üzembe helyezése. Vertikális felskálázás is, vagy a tároló átviteli sebességet az alkalmazás változásával igényeinek megfelelően méretezhető. Ha további információt szeretne a kérelmekkel kapcsolatos egységekről, és segítségre van szüksége a tároló igényeinek meghatározásához, próbálkozzon az [átviteli sebesség kalkulátorával](https://www.documentdb.com/capacityplanner).

### <a name="how-does-azure-cosmos-db-support-various-data-models-such-as-keyvalue-columnar-document-and-graph"></a>Hogyan támogatja az Azure Cosmos DB különböző adatmodelleket, például a kulcs/érték, Oszlopalapú, dokumentum és graph?

Kulcs/érték (tábla), Oszlopalapú, dokumentum és modellek natív módon támogatja a ARS (atomokból, rekordokat és feladatütemezések) miatt a grafikon adatainak tervezése az Azure Cosmos DB épül. Atom, a rekordokat és a feladatütemezések egyszerűen leképezve és előre jelzett különböző adatmodelleket. A modellekhez tartozó API-k mostantól elérhetők (SQL-, MongoDB-, tábla-és Gremlin), és a további adatmodellekhez kapcsolódó egyebek a jövőben lesznek elérhetők.

Az Azure Cosmos DB egy séma sémafüggetlen indexelés összetevő, amely képes automatikusan indexeli az azt betöltő anélkül, hogy bármiféle séma vagy másodlagos indexek a fejlesztőtől származó összes adatot tartalmaz. A motor logikai index elrendezések (fordított, Oszlopalapú, fa), amelyek a tárolási elrendezés az index és a Lekérdezésfeldolgozás alrendszerek szétválaszthatók készletét támaszkodik. A cosmos DB lehetővé teszi az átviteli protokollokat és API-k támogatása kibővíthető módon és kéréseivé átalakítani azokat hatékonyan a core data model (1) és a logikai index elrendezések (2) így egyedi módon képes a natív módon támogató egynél több adatmodellt is tartalmaz.

### <a name="is-azure-cosmos-db-hipaa-compliant"></a>Az Azure Cosmos DB a HIPAA szabványnak megfelelő?

Igen, az Azure Cosmos DB a HIPAA-szabványnak megfelelő. A HIPAA az egyéni és beazonosítható egészségügyi adatok használatára, nyilvánosságra hozatalára és védelmére vonatkozó követelményeket állapítja meg. További információkért lásd: [Microsoft Adatvédelmi központ](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA).

### <a name="what-are-the-storage-limits-of-azure-cosmos-db"></a>Mik az Azure Cosmos DB tárolási korlátai?

Egy tárolót az Azure Cosmos DB tárolható adatok teljes mennyisége nincs korlátozva van.

### <a name="what-are-the-throughput-limits-of-azure-cosmos-db"></a>Mik az Azure Cosmos DB átviteli sebességének korlátai?

Átviteli sebesség egy tárolót az Azure Cosmos DB által támogatott teljes mennyisége nincs korlátozva van. A fő cél, hogy a munkaterhelés nagyjából egyenletes elosztása megfelelően nagy számú partíciós kulcsok között.

### <a name="are-direct-and-gateway-connectivity-modes-encrypted"></a>Közvetlen és az átjáró kapcsolódási módjai titkosított?

Igen mindkét mód mindig teljes mértékben titkosított.

### <a name="how-much-does-azure-cosmos-db-cost"></a>Azure Cosmos DB mennyibe?

Részletekért tekintse meg a [díjszabásáról az Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) lapot. Az Azure Cosmos DB-használati díjak üzembe helyezett tárolók, a tárolók is online órák száma számát határozza meg, és az egyes tárolók a kiosztott átviteli sebesség.

### <a name="is-a-free-account-available"></a>Van egy ingyenes fiókot?

Igen, akkor regisztráljon egy időben korlátozott fiók ingyenesen, kötelezettségvállalás nélkül. Ha szeretne regisztrálni, keresse fel a [próbálja ki ingyenesen az Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) vagy további információ: a [próbálja ki az Azure Cosmos DB – gyakori kérdések](#try-cosmos-db).

Ha most ismerkedik az Azure-ba, hogy regisztráljon egy [ingyenes Azure-fiók](https://azure.microsoft.com/free/), amely biztosít 30 napot és az összes Azure-szolgáltatás kreditet. Ha a Visual Studio-előfizetéssel rendelkezik, hogy is jogosult-e [ingyenes Azure-kreditek](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) bármely Azure-szolgáltatások használatára.

Is használhatja a [Azure Cosmos DB Emulatort](local-emulator.md) fejlesztéséhez és teszteléséhez az alkalmazás helyi ingyenes, az Azure-előfizetés létrehozása nélkül. Amikor már elégedett az alkalmazás működésével az Azure Cosmos DB Emulatorban, átválthat az Azure Cosmos DB-fiók használatára a felhőben.

### <a name="how-can-i-get-additional-help-with-azure-cosmos-db"></a>Hogyan kaphatok további segítséget az Azure Cosmos DB?

Technikai kérdés, az alábbi két kérdés egyik közzététele, és válaszoljon a fórumok:

* [MSDN-fórum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurecosmosdb)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cosmosdb). A stack Overflow a legjobb a programozási kérdéseit. Ellenőrizze, hogy a kérdés [témához illeszkedő](https://stackoverflow.com/help/on-topic) és [adja meg a legtöbb adatot lehet, így a kérdést, egyértelmű és megválaszolható](https://stackoverflow.com/help/how-to-ask).

Javasolhat új funkciókat, hozzon létre egy új kérelmet [User voice](https://feedback.azure.com/forums/263030-azure-cosmos-db).

Ha a fiókjával van probléma, nyújtson be [támogatási kérelmet](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) az Azure Portalon.

További kérdések küldheti el a csapatának [ askcosmosdb@microsoft.com ](mailto:askcosmosdb@microsoft.com); ugyanakkor ez a technikai támogatási címe nem.

## <a id="try-cosmos-db"></a>Próbálja ki az Azure Cosmos DB-előfizetések

Most már élvezheti egy időben korlátozott Azure Cosmos DB nyújtotta előfizetés, díjfizetés és elköteleződés ingyenes nélkül. Próbálja ki az Azure Cosmos DB-előfizetéshez tartozó regisztráció, lépjen a [próbálja ki ingyenesen az Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/). Ebben az előfizetésben nem azonos azzal a [Azure ingyenes próbaverziója](https://azure.microsoft.com/free/), és használható együtt az Azure ingyenes próbaverziójára, és a egy Azure-előfizetéssel.

Próbálja meg az Azure Cosmos DB előfizetések jelennek meg az Azure portal mellett más előfizetésekkel társított felhasználói azonosítóját.

A következő feltételek lesznek érvényesek, próbálja ki az Azure Cosmos DB-előfizetések:

* Egy, az SQL, a Gremlin API és a Table accounts-előfizetések által [kiosztott tároló](./set-throughput.md#set-throughput-on-a-container) .
* Akár három, a MongoDB-fiókokra vonatkozó előfizetéshez tartozó [kiosztott gyűjtemény](./set-throughput.md#set-throughput-on-a-container) .
* Egy [átviteli sebesség](./set-throughput.md#set-throughput-on-a-database) kiépített adatbázisa előfizetéssel. Az átviteli sebesség kiépített adatbázisai tetszőleges számú tárolót tartalmazhatnak.
* 10 GB-os tárolási kapacitás.
* A globális replikáció a következő Azure- [régiókban](https://azure.microsoft.com/regions/)érhető el: USA középső régiója, Észak-Európa és Délkelet-Ázsia
* A maximális átviteli sebesség 5 K RU/s a tároló szintjén kiépítve.
* Az adatbázis szintjén kiépített 20 K/s maximális átviteli sebesség.
* Az előfizetések 30 nap elteltével lejárnak, és legfeljebb 31 napra bővíthetők.
* Próbálja ki az Azure Cosmos DB-fiókok; nem hozható létre Azure-támogatási jegyek azonban támogatást is magukban a meglévő támogatási csomagokról-előfizetőknek.

## <a name="set-up-azure-cosmos-db"></a>Állítsa be az Azure Cosmos DB-hez

### <a name="how-do-i-sign-up-for-azure-cosmos-db"></a>Hogyan tudok regisztrálni az Azure Cosmos DB?

Az Azure Cosmos DB az Azure Portalon érhető el. Először regisztráljon egy Azure-előfizetést. A regisztráció után hozzáadhat egy Azure Cosmos DB fiókot az Azure-előfizetéséhez.

### <a name="what-is-a-master-key"></a>Mi a főkulcs?

A főkulcs egy biztonsági jogkivonat, amellyel egy fiók összes erőforrása elérhető. A főkulccsal rendelkező egyének olvasási és írási hozzáférés az adatbázis-fiókban lévő összes erőforrást. Körültekintően járjon el főkulcsok terjesztésekor. Az elsődleges főkulcs és a másodlagos főkulcs a [Azure Portal][azure-portal] **kulcsok** paneljén érhető el. A kulcsokkal kapcsolatos további információkért lásd: [Hozzáférési kulcsok megtekintése, másolása és újragenerálása](manage-with-cli.md#list-account-keys).

### <a name="what-are-the-regions-that-preferredlocations-can-be-set-to"></a>Mik azok a régiók, amelyek a PreferredLocations értékre lehet beállítani?

A preferredlocations listában érték beállítható bármely Azure-régiót, amelyben Cosmos DB érhető el. Az elérhető régiók listáját lásd: [Azure-régiók](https://azure.microsoft.com/regions/).

### <a name="is-there-anything-i-should-be-aware-of-when-distributing-data-across-the-world-via-the-azure-datacenters"></a>Van-e, hogy ügyelnie, ha az adatok eloszthatók a Azure-adatközpontok keresztül a világon?

Az Azure Cosmos DB az összes Azure-régióban a megadott megtalálható a [Azure-régiók](https://azure.microsoft.com/regions/) lapot. Mivel a core-szolgáltatás, minden új adatközpont rendelkezik egy Azure Cosmos DB jelenlétét.

Állíthat be egy régiót, ne feledje, hogy az Azure Cosmos DB betartja és a government szuverén felhőkben. Azt jelenti Ha létrehoz egy fiókot a egy [szuverén régió](https://azure.microsoft.com/global-infrastructure/), nem lehet replikálni, amely kívül [szuverén régió](https://azure.microsoft.com/global-infrastructure/). Hasonlóképpen egy külső partner szuverén helyekre történő replikáció nem engedélyezhető.

### <a name="is-it-possible-to-switch-from-container-level-throughput-provisioning-to-database-level-throughput-provisioning-or-vice-versa"></a>Van lehetőség az átállás a tároló szint átviteli adatbázis-szintű teljesítmény kiépítés folyamatban? Vagy fordítva

Tároló és az adatbázis átviteli szintű üzembe helyezésének egymástól különálló ajánlatok, és mindkét közötti váltáskor szükséges áttelepítés forrás adatait a célhelyre. Ami azt jelenti, hogy hozzon létre egy új adatbázist vagy egy új gyűjteményt, és ezután telepítse át az adatok segítségével kell [tömeges végrehajtó könyvtár](bulk-executor-overview.md) vagy [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md).

### <a name="does-azure-cosmosdb-support-time-series-analysis"></a>Azure cosmos DB támogatja az idősoros elemzés?

Igen Azure cosmos DB támogatja az idősoros elemzés, a következő egy minta [sorozat Időminta](https://github.com/Azure/azure-cosmosdb-dotnet/tree/master/samples/Patterns). Ez a példa bemutatja, hogyan használható az idősorozat-adatok a build összesített nézetek csatorna módosítása. Ez a megközelítés a spark streaming vagy egy másik stream adatfeldolgozó használatával kiterjesztheti.

## <a name="sql-api"></a>SQL API

### <a name="how-do-i-start-developing-against-the-sql-api"></a>Hogyan kezdhetem meg az SQL API fejlesztését?

Először, jelentkezzen Azure-előfizetésre. Regisztrációt követően az Azure-előfizetéssel, hozzáadhat egy SQL API-tárolót az Azure-előfizetéshez. Az Azure Cosmos DB-fiók hozzáadására vonatkozó utasításokért lásd: [hozzon létre egy Azure Cosmos DB-adatbázisfiók](create-sql-api-dotnet.md#create-account).

[SDK-k](sql-api-sdk-dotnet.md) a .NET, Python, Node.js, JavaScript és Java esetében érhetők el. A fejlesztők is használhatja a [RESTful HTTP API-k](/rest/api/cosmos-db/) kommunikál a különböző platformok és nyelvek az Azure Cosmos DB erőforrásokat.

### <a name="can-i-access-some-ready-made-samples-to-get-a-head-start"></a>Hozzáférhet a bizonyos előre elkészített mintát üzembe?

Az SQL API-minták [.NET](sql-api-dotnet-samples.md), [Java](https://github.com/Azure/azure-documentdb-java), [Node.js](sql-api-nodejs-samples.md), és [Python](sql-api-python-samples.md) SDK a Githubon érhetők el.

### <a name="does-the-sql-api-database-support-schema-free-data"></a>Az SQL API-adatbázis támogatja a sémamentes adatokat?

Igen, az SQL API lehetővé teszi, hogy sémadefiníciók vagy mutatók nélkül tetszőleges JSON-dokumentumokat tárolhat alkalmazásokat. Adatok azonnal lekérdezhetők a Azure Cosmos DB SQL-lekérdezési felületén keresztül.

### <a name="does-the-sql-api-support-acid-transactions"></a>Az SQL API támogatja az ACID-tranzakciókat?

Igen, az SQL API támogatja a JavaScript-tárolt eljárásokkal és eseményindítókkal kifejezett dokumentumok közötti tranzakciókat. Tranzakciók hatóköre a tárolók belül egyetlen partícióra és ACID szemantikákkal, "mindent vagy semmit," végrehajtani más párhuzamosan kódtól vagy felhasználói kérelmektől elkülönítve. Ha a kivételek jelentkeznek a JavaScript alkalmazáskód kiszolgálóoldali végrehajtási, a teljes tranzakció vissza lesz állítva. 

### <a name="what-is-a-container"></a>Mi az a tároló?

Egy tároló olyan dokumentumokat és a kapcsolódó JavaScript alkalmazáslogikát. Egy tároló-e egy számlázható entitás, ahol a [költség](performance-levels.md) az átviteli sebesség határozza meg, és a storage használt. A tárolók is kiterjedhet, egy vagy több partíció vagy a kiszolgálók és gyakorlatilag korlátlan mennyiségű tárterület vagy átviteli sebesség kezelésére méretezhetők.

* Az SQL API és a Cosmos DB MongoDB-fiókjainak API-ját egy tároló képezi le egy gyűjteményhez.
* Cassandra- és Table API-fiókok esetében a tároló képez le egy táblát.
* Gremlin API-fiókok esetében egy tároló képez le egy grafikont.

Tárolók is rendelkezésre állnak az Azure Cosmos DB számlázási egységei. Minden tároló számlázása óránként, a kiosztott átviteli sebesség alapján és a felhasznált tárterület. További információkért lásd: [Azure Cosmos DB szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/cosmos-db/).

### <a name="how-do-i-create-a-database"></a>Hogyan hozható létre adatbázis?

Az adatbázist is létrehozhat a [az Azure portal](https://portal.azure.com)leírtak szerint [gyűjtemény hozzáadása](create-sql-api-java.md#add-a-container), egy, a [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md), vagy a [REST API-k](/rest/api/cosmos-db/).

### <a name="how-do-i-set-up-users-and-permissions"></a>Hogyan állíthatom be a felhasználókat és engedélyeket?

Egyikének használatával hozhat létre felhasználókat és engedélyeket a [Cosmos DB API SDK-kkal](sql-api-sdk-dotnet.md) vagy a [REST API-k](/rest/api/cosmos-db/).

### <a name="does-the-sql-api-support-sql"></a>Támogatja az SQL API-t-SQL?

Az SQL API-fiókok által támogatott SQL lekérdező nyelve az SQL Server által támogatott lekérdezési funkcionalitás továbbfejlesztett alkészlete. Az Azure Cosmos DB SQL-lekérdezési nyelvet biztosít a részletes hierarchikus és relációs operátorokat és bővíthetőséget a JavaScript-alapú, felhasználó által definiált függvények (UDF-EK). A JSON-szintaxis lehetővé teszi, hogy a JSON-dokumentumok modellezési olyan címkézett csomópontokhoz, amelyek az Azure Cosmos DB automatikus indexelési technikák és az Azure Cosmos DB SQL lekérdezési dialektusa egyaránt által használt. További információ az SQL-nyelvtan használatáról: [SQL][query] -lekérdezési cikk.

### <a name="does-the-sql-api-support-sql-aggregation-functions"></a>Az SQL API támogatja az SQL összesítő függvények?

Az SQL API támogatja a közel valós idejű aggregációs aggregátumfüggvények keresztül bármilyen méretben `COUNT`, `MIN`, `MAX`, `AVG`, és `SUM` az SQL-szintaxis használatával. További információkért lásd: [összesítő függvény](sql-query-aggregates.md).

### <a name="how-does-the-sql-api-provide-concurrency"></a>Hogyan nyújt az SQL API az egyidejűséget?

Az SQL API támogatja a HTTP entitáscímkék vagy ETag-EK keresztül hozzáférések optimista vezérlését (OCC). Minden SQL API-erőforrás rendelkezik egy ETag, és minden alkalommal, amikor a dokumentum frissül az ETag címke értéke a kiszolgálón. Az ETag fejrészt és a jelenlegi érték szerepel parancsválasz-üzeneteket. Döntse el, hogy egy erőforrás frissíteni kell a kiszolgáló ETag használható az If-Match fejlécet. If-Match értéke alapján kell ellenőrizni az ETag-érték. Ha az ETag-érték megfelel a kiszolgáló ETag-érték, az erőforrás frissítése. Ha az ETag címke már nem aktuális, a kiszolgáló elutasítja a műveletet egy "HTTP 412 sikertelen előfeltétel" válaszkóddal. Az ügyfél ezután refetches az erőforrás beszerezni az erőforrás jelenlegi ETag-érték. Emellett ETag segítségével az If-None-Match fejléc határozza meg, hogy szükség van-e egy refetch erőforrás.

Használjon optimista egyidejűséget, a .NET-ben, használja a [AccessCondition](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accesscondition.aspx) osztály. A .NET-minta: [Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs) a DocumentManagement minta a Githubon.

### <a name="how-do-i-perform-transactions-in-the-sql-api"></a>Hogyan végezhetők tranzakciók az SQL API-ban?

Az SQL API támogatja a nyelvintegrált tranzakciókat JavaScript-tárolt eljárásokkal és eseményindítókkal keresztül. Az összes művelet pillanatkép-elkülönítés lesznek végrehajtva. Ha egy egypartíciós gyűjtemény, a végrehajtás a gyűjtemény hatókörét. Ha a gyűjtemény particionált, a végrehajtás a gyűjteményen belül azonos partíciós kulccsal értékkel dokumentumok hatókörét. A tranzakció kezdetekor pillanatkép készül a dokumentumverziókról (ETag-ek), és csak akkor lesznek véglegesítve, ha a parancsfájl sikeres. Ha a JavaScript hibát jelez, a tranzakció vissza lesz állítva. További információkért lásd: [kiszolgálóoldali JavaScript programozás az Azure Cosmos DB](stored-procedures-triggers-udfs.md).

### <a name="how-can-i-bulk-insert-documents-into-cosmos-db"></a>Hogyan lehet I tömeges beszúrási dokumentumok az Cosmos DB-be?

Akkor is tömeges beszúrási dokumentumokat az Azure Cosmos DB-be a következő módszerek valamelyikével:

* A tömeges végrehajtó eszközt, a leírtak szerint [Using tömeges végrehajtó .NET-kódtár](bulk-executor-dot-net.md) és [tömeges végrehajtó Java-kódtár használata](bulk-executor-java.md)
* Az adatáttelepítés eszközzel leírtak szerint [az Azure Cosmos DB adatbázis-áttelepítési eszköz](import-data.md).
* Tárolt eljárások, leírtak szerint [kiszolgálóoldali JavaScript programozás az Azure Cosmos DB](stored-procedures-triggers-udfs.md).

### <a name="does-the-sql-api-support-resource-link-caching"></a>Nem, az SQL API támogatási erőforrás-hivatkozások gyorsítótárazását?

Igen, mivel az Azure Cosmos DB egy RESTful szolgáltatás, erőforrás-hivatkozások nem módosíthatók, és ezáltal gyorsítótárazhatók. Az SQL API-ügyfelek adja meg egy "If-None-Match" fejlécet bármilyen erőforrás-szerű dokumentum vagy gyűjtemény olvasása és frissítse a helyi példányok, miután a kiszolgálóverzió módosult.

### <a name="is-a-local-instance-of-sql-api-available"></a>Az SQL API helyi példány érhető el?

Igen. A [Azure Cosmos DB Emulatort](local-emulator.md) egy élethű emulációs a Cosmos DB szolgáltatást biztosít. Támogatja a Funkciók, amelyek azonos Azure Cosmos DB-hez, például létrehozása és lekérdezése a JSON-dokumentumok, kiépítés és a gyűjtemények méretezéséről és végrehajtása tárolt eljárásokkal és eseményindítókkal. Fejlesztés és tesztelik az alkalmazásokat az Azure Cosmos DB Emulator használatával, és telepítse őket a az Azure globális méretű, módosítsa a kapcsolati végpont az Azure Cosmos DB egyetlen konfigurációjának.

### <a name="why-are-long-floating-point-values-in-a-document-rounded-when-viewed-from-data-explorer-in-the-portal"></a>Miért vannak kerekítve, ha a portálon az adatkezelőben megtekintett dokumentum mennyi ideig lebegőpontos értékeket.

Ez a korlátozás a JavaScript. A JavaScript dupla pontosságú lebegőpontos formátumú számokat használ az IEEE 754-ben megadott értékekkel, és biztonságosan képes tárolni a számokat – (2<sup>53</sup> – 1) és 2<sup>53</sup>-1 (például 9007199254740991).

### <a name="where-are-permissions-allowed-in-the-object-hierarchy"></a>Ha engedélyezettek a engedélyek az objektum hierarchiában?

A descendants (például dokumentumok, a mellékletek) és a tároló szintű engedélyek ResourceTokens használatával történő létrehozásának engedélyezett. Ez azt jelenti, hogy hozzon létre egy engedéllyel, amikor az adatbázis próbálkozik, vagy a fiók szintjén jelenleg nem engedélyezett.

## <a name="azure-cosmos-dbs-api-for-mongodb"></a>MongoDB-hez készült Azure Cosmos DB API

### <a name="what-is-the-azure-cosmos-dbs-api-for-mongodb"></a>Mi a Azure Cosmos DB API-MongoDB?

A Azure Cosmos DB API-MongoDB egy olyan huzal-protokoll kompatibilitási réteg, amely lehetővé teszi az alkalmazások számára, hogy a meglévő, Közösség által támogatott SDK-k és illesztőprogramok használatával könnyedén és transzparens módon kommunikáljanak a natív Azure Cosmos DB MongoDB. A fejlesztők mostantól a meglévő MongoDB eszközlánccal és képességeiket is használhatják olyan alkalmazások készítéséhez, amelyek kihasználják a Azure Cosmos DB. A fejlesztők a Azure Cosmos DB egyedi képességeivel részesülnek, amelyek globális elosztást biztosítanak a több főkiszolgálós replikációval, az automatikus indexeléssel, a biztonsági mentési karbantartással, a pénzügyi támogatással rendelkező szolgáltatói szerződésekkel (SLA) stb.

### <a name="how-do-i-connect-to-my-database"></a>Hogyan csatlakozni az adatbázishoz?

A MongoDB Azure Cosmos DB API-jával a Cosmos-adatbázishoz való kapcsolódás leggyorsabb módja a [Azure Portal](https://portal.azure.com). Jelentkezzen be a fiókjába, és a bal oldali navigációs menüben kattintson **gyors üzembe helyezés**. A gyors üzembe helyezés a legjobb módszer az adatbázishoz való kapcsolódáshoz szükséges kódrészletek beszerzésére.

Az Azure Cosmos DB szigorú biztonsági követelmények és előírások érvénybe lépteti. Az Azure Cosmos DB-fiókokhoz hitelesítés és biztonságos SSL-kommunikációra van szükség, ezért ügyeljen arra, hogy TLSv1.2.

További információ: a [Cosmos-adatbázishoz való kapcsolódás Azure Cosmos db API-MongoDB](connect-mongodb-account.md).

### <a name="are-there-additional-error-codes-that-i-need-to-deal-with-while-using-azure-cosmos-dbs-api-for-mongodb"></a>Vannak-e további hibakódok, amelyekre szükség van a Azure Cosmos DB API-MongoDB való használatához?

A Common MongoDB-hibakódokkal együtt a Azure Cosmos DB API-ját a MongoDB saját specifikus hibakódokkal rendelkezik:

| Hiba               | Kód  | Leírás  | Megoldás  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | Felhasznált kérelemegységek teljes száma nagyobb, mint a gyűjteményben kiosztott kérelemegység díjaival, és megfelelően szabályozva lett. | Az átviteli sebességet rendelve egy tárolót vagy tárolók egy készletét az Azure portal vagy az újrapróbálkozás újra megfontolni. |
| ExceededMemoryLimit | 16501 | A művelet több-bérlős szolgáltatás, az ügyfél memória kiosztása feletti csökkent. | Csökkentse a szigorúbb lekérdezési feltételek használatával művelet hatókörének, vagy forduljon az ügyfélszolgálathoz a a [az Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). <br><br>Példa: <em> &nbsp; db.&nbsp;getCollection (' Users '). aggregált ([ &nbsp; &nbsp;<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$match: {név: "Andy"}}, <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$sort: {Age:-1}}<br>&nbsp;&nbsp;&nbsp;&nbsp;])</em>) |

### <a name="is-the-simba-driver-for-mongodb-supported-for-use-with-azure-cosmos-dbs-api-for-mongodb"></a>Támogatott-e a MongoDB-illesztőprogram a Azure Cosmos DB API-MongoDB való használatra?

Igen, használhatja a Simba Mongo ODBC-illesztőjét Azure Cosmos DB API-val a MongoDB

## <a id="table"></a>Table API

### <a name="how-can-i-use-the-table-api-offering"></a>Hogyan használhatom a Table API-ajánlat?

A Azure Cosmos DB Table API a Azure Portalban érhető [][azure-portal]el. Először, jelentkezzen Azure-előfizetésre. Miután bejelentkezett a, az egy Azure Cosmos DB Table API-fiók hozzáadása az Azure-előfizetéshez, és adja hozzá a táblák a fiókjához.

A támogatott nyelvek és a kapcsolódó használatának első lépései a találja a [bemutatása az Azure Cosmos DB Table API](table-introduction.md).

### <a name="do-i-need-a-new-sdk-to-use-the-table-api"></a>Szükségem van egy új SDK-t a Table API-val?

Nem, a meglévő storage SDK-k továbbra is működnie kell. Ajánlott azonban, hogy mindig kap a legújabb SDK-k és a legtöbb esetben a legjobb támogatás kiváló teljesítmény. Az elérhető nyelvek listáját lásd a [bemutatása az Azure Cosmos DB Table API](table-introduction.md).

### <a name="where-is-table-api-not-identical-with-azure-table-storage-behavior"></a>Ha nem Table API az Azure Table storage működése azonos?

Van néhány viselkedésbeli különbségeket, hogy az Azure Table storage-ból érkező táblázatok létrehozása az Azure Cosmos DB Table API az kívánó felhasználóknak érdemes figyelembe:

* Az Azure Cosmos DB Table API-tartalékkapacitást modellt használ a garantált teljesítmény biztosítása érdekében, de ez azt jelenti, hogy egy havidíja fedezi a kapacitást, amint az a tábla jön létre, akkor is, ha a kapacitás nincs használatban. Az Azure Table storage egy csak azért fizet, használt kapacitás. Ez segít annak magyarázata, hogy miért kínálnak Table API 10 ms és 15 ms 99 százalékon olvasási SLA-t, az Azure Table storage biztosít a 10 másodperces szolgáltatásszint-szerződéssel. De ennek következtében a Table API-táblákkal, minden kéréshez, a költség pénzt azokat bármely, a szolgáltatási kéréseinek kezeléséhez rendelkezésre álló kapacitás biztosítása nélkül is üres táblák által kínált Azure Cosmos DB.
* A Table API által visszaadott lekérdezési eredményeket nem rendezi a rendszer a partíciós kulcs/sor kulcs sorrendjében, mivel azok az Azure Table Storage-ban találhatók.
* Sorkulcsok csak legfeljebb 255 bájt lehet
* Kötegek legfeljebb 2 MB
* A CORS jelenleg nem támogatott.
* Az Azure Table Storage-beli táblanév nem különbözteti meg a kis-és nagybetűket, Azure Cosmos DB Table API
* Egyes Azure Cosmos DB belső formátumok kódolási információk, például bináris mezőket, jelenleg nem annyira hatékony, például előfordulhat, hogy az egyik. Ezért az adatok mérete váratlan korlátozások léphetnek föl. Például jelenleg egyik nem használható a teljes egy tábla entitás Meg a bináris adatok tárolására, mert a kódolás növeli az adatok mérete.
* Az entitás "ID" tulajdonságának neve jelenleg nem támogatott
* TableQuery TakeCount nem korlátozódik az 1000

A REST API tekintetében számos végpontok és lekérdezési lehetőségeket, amelyek az Azure Cosmos DB Table API által nem támogatott:

| REST-metódus | REST-végpont/lekérdezési beállítás | Dokumentum URL-címek | Magyarázat |
| ------------| ------------- | ---------- | ----------- |
| GET, PUT | /? restype =service@comp= tulajdonságai| [Állítsa be a Table Service tulajdonságok](https://docs.microsoft.com/rest/api/storageservices/set-table-service-properties) és [Table Service tulajdonságainak lekérése](https://docs.microsoft.com/rest/api/storageservices/get-table-service-properties) | Ez a végpont segítségével állítsa be a CORS-szabályok, storage analytics konfigurációs és naplózási beállítások. CORS jelenleg nem támogatott, és az analytics és a naplózás az Azure Cosmos DB, mint az Azure Storage-táblák másképp kezeli |
| BEÁLLÍTÁSOK | /\<tábla – erőforrás neve > | [Üzem előtti repülési CORS tábla kérelem](https://docs.microsoft.com/rest/api/storageservices/preflight-table-request) | Ez az Azure Cosmos DB jelenleg nem támogatja a CORS részét képezi. |
| GET | /? restype =service@comp= stats | [Táblastatisztika szolgáltatás beolvasása](https://docs.microsoft.com/rest/api/storageservices/get-table-service-stats) | Milyen gyorsan adat áll replikálás alatt, elsődleges és másodlagos adatbázisok közötti információkat tartalmaz. A replikáció része írások, ez nem szükséges Cosmos DB-ben. |
| GET, PUT | /mytable? comp = acl | [ACL-tábla](https://docs.microsoft.com/rest/api/storageservices/get-table-acl) és [tábla ACL beállítása](https://docs.microsoft.com/rest/api/storageservices/set-table-acl) | Ez lekérdezi és beállítja a tárolt hozzáférési szabályzatok kezelése közös hozzáférésű Jogosultságkódok (SAS) használt. Bár a SAS támogatott, a rendszer másként állítja be és kezeli őket. |

Emellett az Azure Cosmos DB Table API támogatja a JSON-formátumban, nem ATOM csak.

Bár az Azure Cosmos DB támogatja a közös hozzáférésű Jogosultságkódok (SAS) bizonyos szabályzatok, ezt nem támogatja, kifejezetten azok kapcsolódó felügyeleti műveleteket, mint a jogot arra, hogy új táblákat hozhat létre.

A .NET SDK-hoz készült különösen vannak osztályok és módszerek, amely Azure Cosmos DB jelenleg nem támogatja.

| Osztály | A metódus nem támogatott |
|-------|-------- |
| CloudTableClient | \*ServiceProperties* |
|                  | \*ServiceStats* |
| CloudTable | SetPermissions* |
|            | GetPermissions * |
| TableServiceContext | * (Ez az osztály elavult) |
| TableServiceEntity | " " |
| TableServiceExtensions | " " |
| TableServiceQuery | " " |

Ha ezek a különbségek bármelyike a projekt probléma, forduljon a [ askcosmosdb@microsoft.com ](mailto:askcosmosdb@microsoft.com) és tudassa velünk.

### <a name="how-do-i-provide-feedback-about-the-sdk-or-bugs"></a>Hogyan küldhetek visszajelzést az SDK-t vagy a hibák?

Az alábbi módokon is ossza meg velünk véleményét:

* [Felhasználói visszajelzési webhelyen](https://feedback.azure.com/forums/263030-azure-cosmos-db)
* [MSDN-fórum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurecosmosdb)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cosmosdb). A stack Overflow a legjobb a programozási kérdéseit. Ellenőrizze, hogy a kérdés [témához illeszkedő](https://stackoverflow.com/help/on-topic) és [adja meg a legtöbb adatot lehet, így a kérdést, egyértelmű és megválaszolható](https://stackoverflow.com/help/how-to-ask).

### <a name="what-is-the-connection-string-that-i-need-to-use-to-connect-to-the-table-api"></a>Mi az a kapcsolati karakterláncot, amely a Table API csatlakozni kell?

A kapcsolati karakterláncot a következő:

```
DefaultEndpointsProtocol=https;AccountName=<AccountNamefromCosmos DB;AccountKey=<FromKeysPaneofCosmosDB>;TableEndpoint=https://<AccountName>.table.cosmosdb.azure.com
```

A kapcsolati karakterlánc kaphat az Azure Portal kapcsolati karakterlánc oldaláról.

### <a name="how-do-i-override-the-config-settings-for-the-request-options-in-the-net-sdk-for-the-table-api"></a>Hogyan bírálhatom felül az kérésbeállításokat for a Table API .NET SDK-ban konfigurációs beállításait?

Egyes beállítások Túlterheléseivel metódus és más kezeli, az app.config az appSettings szakaszt az ügyfélalkalmazásban található keresztül. Konfigurációs beállításaival kapcsolatos további információkért lásd: [Azure Cosmos DB-képességek](tutorial-develop-table-dotnet.md).

### <a name="are-there-any-changes-for-customers-who-are-using-the-existing-azure-table-storage-sdks"></a>Vannak-e módosítások a már meglévő Azure Table storage SDK-kat használó ügyfelek számára?

Nincs. Nem változtak meglévő vagy új, a meglévő Azure Table storage SDK-kat használó ügyfelek számára.

### <a name="how-do-i-view-table-data-thats-stored-in-azure-cosmos-db-for-use-with-the-table-api"></a>Hogyan tekinthetem meg az Azure Cosmos DB Table API-val használható tárolt táblaadatok?

Az Azure portal segítségével keresse meg az adatokat. A Table API-kód vagy az eszközök a következő választ említett is használhatja.

### <a name="which-tools-work-with-the-table-api"></a>Mely eszközök működnek a Table API-val?

Használhatja a [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

Eszközök is igénybe vehet egy kapcsolati karakterláncot a megadott formátumban korábban az új Table API is támogatja. A megadott tábla eszközök listáját az [Azure Storage-ügyféleszközök](../storage/common/storage-explorers.md) lapot.

### <a name="is-the-concurrency-on-operations-controlled"></a>Az egyidejűség az ellenőrzött műveletek?

Igen, az optimista egyidejűség biztosítunk az ETag mechanizmus segítségével.

### <a name="is-the-odata-query-model-supported-for-entities"></a>Az entitások támogatott OData lekérdezési modellje?

Igen, a Table API támogatja az OData-lekérdezés és a LINQ-lekérdezésekre.

### <a name="can-i-connect-to-azure-table-storage-and-azure-cosmos-db-table-api-side-by-side-in-the-same-application"></a>Csatlakozhatok az Azure Table Storage és Azure Cosmos DB Table API egymás mellett ugyanabban az alkalmazásban?

Igen, csatlakoztathatja a CloudTableClient, mindegyik a saját URI-n keresztül a kapcsolati karakterlánc mutató két külön példányának létrehozásával.

### <a name="how-do-i-migrate-an-existing-azure-table-storage-application-to-this-offering"></a>Hogyan migrálhatok ezt az ajánlatot egy meglévő Azure Table storage alkalmazást?

[Az AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) és a [Azure Cosmos DB adatáttelepítési eszköz](import-data.md) azok egyaránt támogatott.

### <a name="how-is-expansion-of-the-storage-size-done-for-this-service-if-for-example-i-start-with-n-gb-of-data-and-my-data-will-grow-to-1-tb-over-time"></a>Hogyan történik a tároló mérete, ha például a indítható el, ez a szolgáltatás készen bővítése *n* adatok és a saját adatok GB-os idővel növekszik 1 TB-os?

Az Azure Cosmos DB célja a horizontális skálázás segítségével korlátlan tárolási megoldás biztosítása. A szolgáltatás figyelheti és hatékonyan növelheti a tárhely.

### <a name="how-do-i-monitor-the-table-api-offering"></a>Hogyan figyelhetem a Table API-ajánlat?

A Table API használható **metrikák** panelen a kérelmek és a storage használatának monitorozása.

### <a name="how-do-i-calculate-the-throughput-i-require"></a>Hogyan számítja ki a az átviteli sebességet, követelhetem?

A kapacitás estimator a műveletek által megkövetelt TableThroughput kiszámításához használható. További információkért lásd: [becslés Kérelemegységek és az adattárolásra](https://www.documentdb.com/capacityplanner). Általánosságban elmondható hogy az entitás JSON-fájlként, és adja meg a számokat az operations.

### <a name="can-i-use-the-table-api-sdk-locally-with-the-emulator"></a>Használható a Table API SDK helyi emulátorral?

Jelenleg nem.

### <a name="can-my-existing-application-work-with-the-table-api"></a>Meglévő alkalmazás dolgozhat a Table API-t?

Igen, az azonos API támogatja.

### <a name="do-i-need-to-migrate-my-existing-azure-table-storage-applications-to-the-sdk-if-i-dont-want-to-use-the-table-api-features"></a>Van szükségem az áttelepítést a meglévő Azure Table storage alkalmazásaimat az SDK-t, ha nem szeretnék a Table API-funkciók?

Nem, hozzon létre, és használja a meglévő Azure Table storage eszközök bármiféle megszakítás nélkül. Azonban ha nem használja a Table API, meg nem részesülhetnek az Automatikus indexelés, a további konzisztencia lehetőséget vagy a globális terjesztés.

### <a name="how-do-i-add-replication-of-the-data-in-the-table-api-across-more-than-one-region-of-azure"></a>Hogyan adhatok hozzá az adatok replikálása a Table API-ban több régióban az Azure között?

Az Azure Cosmos DB Portallal [globális replikációs beállítások](tutorial-global-distribution-sql-api.md#portal) régiók, amelyek alkalmasak az alkalmazás hozzáadásához. A globálisan elosztott alkalmazások fejlesztése, hozzá kell az alkalmazás a PreferredLocation adatokkal állítsa be a helyi régiónak, amelyek biztosítják az alacsony olvasási késés.

### <a name="how-do-i-change-the-primary-write-region-for-the-account-in-the-table-api"></a>Hogyan módosíthatom a Table API-ban a fiók az elsődleges írási régiót?

Az Azure Cosmos DB globális replikációt portál panel használatával hozzáadta a régiót, és ezután átadja a feladatokat a szükséges régió. Útmutatásért lásd: [fejlesztés a többrégiós Azure Cosmos DB-fiókok](high-availability.md).

### <a name="how-do-i-configure-my-preferred-read-regions-for-low-latency-when-i-distribute-my-data"></a>Hogyan konfigurálhatom az alacsony késés érdekében saját előnyben részesített olvasási régiók I adataimat terjesztésekor?

Annak érdekében, olvassa el a helyi helyről, használja a PreferredLocation kulcsot az app.config fájlban. Meglévő alkalmazások a Table API hibát jelez, ha LocationMode van beállítva. Távolítsa el a adott kódot, mert a tábla API szerzi be ezt az információt az app.config fájlból. 

### <a name="how-should-i-think-about-consistency-levels-in-the-table-api"></a>Hogyan kell konzisztenciaszintek a Table API a gondolja?

Az Azure Cosmos DB jól indoklással és hátrányokkal konzisztencia, a rendelkezésre állás és a késést biztosít. Az Azure Cosmos DB öt konzisztenciaszintet kínál a Table API-fejlesztők számára, így válassza ki a megfelelő konzisztenciájú modellt, a tábla szintjén, és győződjön meg arról, az egyes kérelmek, az adatok lekérdezése közben. Amikor egy ügyfél csatlakozik, azt a konzisztenciaszint adhatja meg. Módosíthatja a szintet Túlterheléseivel consistencyLevel argumentuma keresztül.

A Table API-t biztosít, közel valós idejű olvassa be a "saját írási műveletek"olvasása kötött-frissesség konzisztencia alapértelmezés szerint. További információkért lásd: [konzisztenciaszintek](consistency-levels.md).

Alapértelmezés szerint az Azure Table storage biztosítja a konzisztenciát egy adott régión belül és a végleges konzisztencia a másodlagos helyeken.

### <a name="does-azure-cosmos-db-table-api-offer-more-consistency-levels-than-azure-table-storage"></a>Kínál az Azure Cosmos DB Table API az Azure Table storage-nál több konzisztenciaszintek?

Igen, kihasználhatják az Azure Cosmos DB elosztott jellege kapcsolatos információkért lásd: [konzisztenciaszintek](consistency-levels.md). A konzisztenciaszintek előírt garanciákat, mert a magabiztosan használhatja őket.

### <a name="when-global-distribution-is-enabled-how-long-does-it-take-to-replicate-the-data"></a>Ha engedélyezve van a globális terjesztés, mennyi ideig tart, replikálja az adatokat?

Az Azure Cosmos DB az adatokat a helyi régióban tartósan véglegesítések, és leküldéses értesítések az adatok közvetlenül más régiókhoz csupán néhány ezredmásodperc. A fájlreplikációs szolgáltatás csak az üzenetváltási időt (RTT) az adatközpontok függ. Ha többet szeretne megtudni a Azure Cosmos db globális terjesztési képességeiről, tekintse [meg a Azure Cosmos db: Globálisan elosztott adatbázis-szolgáltatás az](distribute-data-globally.md)Azure-ban.

### <a name="can-the-read-request-consistency-level-be-changed"></a>Módosíthatja az olvasási kérést konzisztenciaszint?

Az Azure Cosmos DB a konzisztenciaszint beállíthatja a tároló szintjén (a leírásban). A .NET SDK használatával módosíthatja a szintet TableConsistencyLevel kulcsot az app.config fájlban az érték megadásával. A lehetséges értékek a következők: Erős, kötött elavulás, munkamenet, konzisztens előtag és végleges. További információkért lásd: [adatok aprólékosan beállítható konzisztenciaszintek az Azure Cosmos DB](consistency-levels.md). A kulcs lényege, hogy nem állítható be a kérelem konzisztencia szint több, mint a beállítást a táblára vonatkozóan:. Például a konzisztenciaszint a táblához nem beállítása végleges, és a kérés konzisztenciaszint erős.

### <a name="how-does-the-table-api-handle-failover-if-a-region-goes-down"></a>Hogyan a Table API kezeli feladatátvételi Ha leáll egy régióban?

A Table API a az Azure Cosmos DB globálisan elosztott platformot használ. Győződjön meg arról, hogy az alkalmazás működését adatközpont-leállás, engedélyezni kell legalább egy további régióban, a fiók az Azure Cosmos DB portál [fejlesztés a többrégiós Azure Cosmos DB-fiókok](high-availability.md). A portál használatával beállíthatja az a régió prioritásának [fejlesztés a többrégiós Azure Cosmos DB-fiókok](high-availability.md).

A fiók, és szabályozhatja, ahol azt átveheti azáltal, hogy a feladatátvétel prioritási tetszőleges számú régiót adhat hozzá. Az adatbázis használatára, meg kell adnia a-alkalmazást is túl. Ha így tesz, az ügyfelek nem szoftverkarbantartás. A [legújabb .NET ügyféloldali SDK-val](table-sdk-dotnet.md) automatikus helykezelési van, de a más SDK-k nem. Észlelését, azt a régiót, amelyben nem működik, és automatikusan átadja a feladatokat az új régióban.

### <a name="is-the-table-api-enabled-for-backups"></a>A Table API engedélyezve van a biztonsági mentéseket?

Igen, a Table API-t használja a biztonsági másolatokat az Azure Cosmos DB platform. Biztonsági mentés automatikusan történik. További információkért lásd: [Online biztonsági mentés és visszaállítás az Azure Cosmos DB](online-backup-and-restore.md).

### <a name="does-the-table-api-index-all-attributes-of-an-entity-by-default"></a>A Table API index egy entitás összes attribútumának alapértelmezés szerint?

Igen, egy entitás összes attribútumának indexelt alapértelmezés szerint. További információkért lásd [: Azure Cosmos db: Indexelési](index-policy.md)házirendek.

### <a name="does-this-mean-i-dont-have-to-create-more-than-one-index-to-satisfy-the-queries"></a>Nem, nem kell létrehoznunk jelent ez több, mint a lekérdezések kielégítéséhez több index?

Igen, az Azure Cosmos DB Table API biztosít az összes attribútum bármely séma definíció nélküli automatikus indexelés. Ezt az automatizálást a fejlesztők számára, hogy az alkalmazás helyett az index létrehozásának és felügyeletének fókusz szabadít fel. További információkért lásd [: Azure Cosmos db: Indexelési](index-policy.md)házirendek.

### <a name="can-i-change-the-indexing-policy"></a>Módosíthatja az indexelési házirendet?

Igen, módosíthatja az indexelési házirendet azáltal, hogy az index definícióját. Megfelelően kódolása és a beállítások karaktert kell.

A nem – .NET SDK-k, az indexelési házirendet csak állítható be a portálon, a **adatkezelő**, keresse meg az adott táblában szeretné módosítani, és folytassa a a **méretezés és beállítások**-> indexelési házirend a kívánt módosítást, majd **mentése**.

A .NET SDK-ból, így el lehet az app.config fájlban:

```JSON
{
  "indexingMode": "consistent",
  "automatic": true,
  "includedPaths": [
    {
      "path": "/somepath",
      "indexes": [
        {
          "kind": "Range",
          "dataType": "Number",
          "precision": -1
        },
        {
          "kind": "Range",
          "dataType": "String",
          "precision": -1
        }
      ]
    }
  ],
  "excludedPaths":
[
 {
      "path": "/anotherpath"
 }
]
}
```

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-sorting-aggregates-hierarchy-and-other-functionality-will-you-be-adding-these-capabilities-to-the-table-api"></a>Az Azure Cosmos DB platform úgy tűnik, hogy rengeteg funkciók, például a rendezést, az összesítések, hierarchia és egyéb funkciókat. Ön hozzáadni a ezeket a képességeket a Table API-hoz?

A tábla API ugyanazokat a lekérdezési funkciókat, mint az Azure Table storage biztosít. Az Azure Cosmos DB a rendezést, az összesítéseket, a földrajzi lekérdezéseket, a hierarchiát és számos különféle beépített funkciót is támogat. További információkért lásd: [SQL-lekérdezések](how-to-sql-query.md).

### <a name="when-should-i-change-tablethroughput-for-the-table-api"></a>Mikor kell módosítani a tábla API TableThroughput?

TableThroughput akkor kell megváltoztatnia, ha érvényes a következő feltételek valamelyike:

* Egy kinyerési, átalakítási és betöltési (ETL) az adatok hajt végre, vagy rövid időn belül nagy mennyiségű adatot feltölteni kívánt.
* További átviteli sebesség a tárolóból vagy egy készletből, a tárolók, a háttéralkalmazás van szüksége. Például láthatja, hogy a használt átviteli sebesség több, mint a kiosztott átviteli sebesség, és Ön éppen első szabályozott. További információkért lásd: [teljesítmény beállítása az Azure Cosmos DB-tárolók](set-throughput.md).

### <a name="can-i-scale-up-or-scale-down-the-throughput-of-my-table-api-table"></a>Vertikális felskálázás vagy leskálázás átviteli sebességének Table API táblázat?

Igen, az Azure Cosmos DB-portál scale panel használatával méretezheti az átviteli sebességet. További információkért lásd: [teljesítmény beállítása](set-throughput.md).

### <a name="is-a-default-tablethroughput-set-for-newly-provisioned-tables"></a>Az alapértelmezett TableThroughput állítsa be az újonnan létrehozott táblák?

Igen, ha nem bírálja felül a TableThroughput app.config keresztül, és a egy előre létrehozott tároló ne használja az Azure Cosmos DB, a szolgáltatás létrehoz egy táblát az átviteli sebesség 400.

### <a name="is-there-any-change-of-pricing-for-existing-customers-of-the-azure-table-storage-service"></a>Van-e bármilyen módosítás a meglévő ügyfelei számára az Azure Table storage szolgáltatás díjszabása?

Nincs. Nem történik változás ár meglévő Azure Table storage-ügyfelek számára.

### <a name="how-is-the-price-calculated-for-the-table-api"></a>Hogyan számítják az ár a Table API-hoz?

A díj attól függ, hogy a lefoglalt TableThroughput.

### <a name="how-do-i-handle-any-rate-limiting-on-the-tables-in-table-api-offering"></a>Hogyan kezelhetem a bármely sebességkorlátozást, a táblák a Table API-ajánlat?

Ha a kérések aránya meghaladja a mögöttes tároló kiosztott átviteli kapacitásának vagy egy tárolók, hibaüzenetet kap, és az SDK-t a hívás újrapróbálkozik az újrapróbálkozási szabályzat alkalmazásával.

### <a name="why-do-i-need-to-choose-a-throughput-apart-from-partitionkey-and-rowkey-to-take-advantage-of-the-table-api-offering-of-azure-cosmos-db"></a>Miért kell választania a PartitionKey és rowkey tulajdonságok esetén kihasználásához az Azure Cosmos DB Table API elérhető átviteli sebesség?

Ha nem ad meg az app.config fájlban, vagy a portálon keresztül egy Azure Cosmos DB egy alapértelmezett átviteli sebességet a tároló állítja be.

Az Azure Cosmos DB garanciákat nyújt a teljesítmény és a késés, a felső korlátja a műveletet. Ez garantálja a motor kényszerítheti a cégirányítási műveletek a bérlő esetén lehetséges. TableThroughput beállítás biztosítja, hogy kap a garantált átviteli sebességgel és késéssel, mert a platform fenntartja a kapacitást, és garantálja a működési sikeres.

Az átviteli sebesség meghatározása révén rugalmasan módosíthatja, hogy az alkalmazás a szezonalitás kihasználhatják, az átviteli sebesség igényeinek és költségeit.

### <a name="azure-table-storage-has-been-inexpensive-for-me-because-i-pay-only-to-store-the-data-and-i-rarely-query-the-azure-cosmos-db-table-api-offering-seems-to-be-charging-me-even-though-i-havent-performed-a-single-transaction-or-stored-anything-can-you-explain"></a>Az Azure Table storage számomra, költségkímélő lett, mert csak szeretné tárolni az adatokat, és csak ritkán lekérdezést kell fizetni. Az Azure Cosmos DB Table API-ajánlat úgy tűnik, hogy lehet díjszabási me annak ellenére, hogy még nem végzett egyetlen tranzakció vagy tárolt semmit. Melyek?

Az Azure Cosmos DB egy globálisan elosztott, SLA-alapú rendszer rendelkezésre állását, a késés és az átviteli sebesség garanciákkal tervezték. Fenntartott átviteli sebesség az Azure Cosmos DB, amikor biztosított, ellentétben más rendszerek az átviteli sebességet. Az Azure Cosmos DB további képességeket biztosít, amelyek ügyfelek kért, például a másodlagos indexek és globális disztribúciót.

### <a name="i-never-get-a-quota-full-notification-indicating-that-a-partition-is-full-when-i-ingest-data-into-azure-table-storage-with-the-table-api-i-do-get-this-message-is-this-offering-limiting-me-and-forcing-me-to-change-my-existing-application"></a>Soha nem jelenik meg a teljes kvóta"értesítés (azt jelzi, hogy egy partíción teljes) Ha szeretnék betölteni az adatokat az Azure Table storage. A Table API-val Ez az üzenet jelenik meg. Ez kínál me korlátozásával, és módosíthatja a meglévő alkalmazás me kényszerítése?

Az Azure Cosmos DB amely korlátlan számú skálázási, a késés, átviteli sebesség, rendelkezésre állás és konzisztencia garanciákkal biztosító SLA-alapú rendszer. Garantált prémium szintű teljesítmény biztosítása érdekében győződjön meg arról, hogy az adatok mérete és az index kezelhető és méretezhető. A 10 GB-os korlátot, az entitások vagy partíciókulcson elemek száma, hogy győződjön meg arról, hogy remek keresési és a lekérdezési teljesítmény kínálunk. Győződjön meg arról, hogy az alkalmazás jól, akár az Azure Storage, skálázható, akkor javasoljuk, hogy Ön *nem* az egy partícióban kapcsolatos összes információ tárolása és lekérdezése, gyakori elérésű partíciót létrehozni.

### <a name="so-partitionkey-and-rowkey-are-still-required-with-the-table-api"></a>Ezért PartitionKey és rowkey tulajdonságok esetén azok továbbra is szükséges a Table API-val?

Igen. Mivel a Table API felületének az Azure Table storage SDK-t a hasonló, a partíciókulcs biztosít egy hatékony módszer az adatok elosztására. A sorkulcs pedig a partíción belül egyedi. A sorkulcs megtalálhatónak kell lennie, és nem lehet null értékű, mint a standard szintű SDK-t. RowKey hossza 255 bájt, és a PartitionKey legfeljebb 1 KB-os.

### <a name="what-are-the-error-messages-for-the-table-api"></a>Mik azok a hibaüzeneteket, a Table API-hoz?

Az Azure Table storage és az Azure Cosmos DB Table API az azonos SDK-kat használja, így a hibák leggyakrabban azonos lesz.

### <a name="why-do-i-get-throttled-when-i-try-to-create-lot-of-tables-one-after-another-in-the-table-api"></a>Miért do I leszabályozza jelenik meg létrehozása táblák rengeteg egymás után a Table API-ban?

Az Azure Cosmos DB amely késés, átviteli sebesség, rendelkezésre állás és konzisztencia megvalósulásának biztosító SLA-alapú rendszer. Mivel a szolgáltatás egy üzembe helyezett rendszert, ezek a követelmények biztosításához erőforrások fenntartja magának. A táblák létrehozásának gyors aránya észlelt és szabályozott. Azt javasoljuk, hogy, tekintse meg a táblák létrehozásának sebessége alacsonyabb, kevesebb mint 5 percenként. Ne feledje, hogy a tábla API-t egy üzembe helyezett rendszer. A pillanatban, annak üzembe helyezésekor megkezdheti akkor kell fizetnie.

## <a name="gremlin-api"></a>Gremlin API

### <a name="for-cnet-development-should-i-use-the-microsoftazuregraphs-package-or-gremlinnet"></a>A C# / .NET-fejlesztéshez, érdemes használni a Microsoft.Azure.Graphs csomag vagy a Gremlin.NET?

Az Azure Cosmos DB Gremlin API a nyílt forráskódú illesztőprogramokat használja, a szolgáltatás fő összekötőkként. Ezért az ajánlott lehetőség [illesztőprogramok, Apache Tinkerpop által támogatott](https://tinkerpop.apache.org/).

### <a name="how-are-rus-charged-when-running-queries-on-a-graph-database"></a>Hogyan számlázzuk a RU/s a gráfadatbázisok lekérdezések futtatásakor?

Minden gráfsémákkal, csúcsokat és éleket, a háttérben JSON-dokumentumok formájában jelennek meg. Egy Gremlin lekérdezési módosíthatja egy vagy több graph-objektumok egyszerre, mivel a hozzá társított költségek közvetlenül kapcsolódik az objektumok, a lekérdezés által feldolgozott élek. Ez az eljárást minden más API-t használó Azure Cosmos DB. További információkért lásd: [Azure Cosmos DB-ben a kérelemegység](request-units.md).

A Kérelemegység díj alapja az bejárási munkakészletének adatokat, és nem az eredményt. Például ha egy lekérdezést a célja, hogy egyetlen csúcs eredményeképpen beszerzése, de egynél több más objektumot gyermekelemeinek von kell, majd a költségek alapul fog tartani a egy eredményt csúcspont számítási graph-objektumon.

### <a name="whats-the-maximum-scale-that-a-graph-database-can-have-in-azure-cosmos-db-gremlin-api"></a>Mi az a maximális skála, amelyeken egy gráfadatbázist az Azure Cosmos DB Gremlin API-hoz?

Az Azure Cosmos DB teszi [horizontális particionálást](partition-data.md) tárolási és átviteli követelményei automatikusan cím növekedéséhez. Egy adott gyűjteményhez társított partíciók száma egy számítási feladat maximális adatátviteli és tárolási kapacitása határozza meg. Azonban a Gremlin API-gyűjtemény van egy meghatározott készletének a szabályokat, hogy a megfelelő teljesítmény biztosítása érdekében, ipari méretekben. További információ a particionálás és ajánlott eljárások: [az Azure Cosmos DB particionálási](partition-data.md) cikk.

### <a name="how-can-i-protect-against-injection-attacks-using-gremlin-drivers"></a>Hogyan tudja megvédeni Gremlin-illesztőprogramokkal injektálási támadások ellen?

A legtöbb natív Apache Tinkerpop Gremlin-illesztőprogram lehetővé teszi, hogy a lekérdezés végrehajtásához paraméterek szótárát adja meg. Ez a példa bemutatja, hogyan a [Gremlin.Net](https://tinkerpop.apache.org/docs/3.2.7/reference/#gremlin-DotNet) és a [Gremlin-Javascript](https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started/blob/master/app.js).

### <a name="why-am-i-getting-the-gremlin-query-compilation-error-unable-to-find-any-method-error"></a>Miért kapok "Gremlin Query fordítási hibát: Nem található "hiba" metódus?

Az Azure Cosmos DB Gremlin API valósítja meg a Gremlin támadási meghatározott funkciók egy részét. Támogatott lépések és további információkat lásd: [Gremlin-támogatás](gremlin-support.md) cikk.

A legjobb megoldás, hogy a szükséges Gremlin-lépések újraírási a támogatott funkciókkal, mivel az Azure Cosmos DB által támogatott összes alapvető fontosságú gremlinnel kapcsolatos lépések.

### <a name="why-am-i-getting-the-websocketexception-the-server-returned-status-code-200-when-status-code-101-was-expected-error"></a>Miért kapok "WebSocketException: A kiszolgáló a (z) "200" állapotkódot adta vissza, amikor a rendszer "101" állapotkódot várt: "hiba?

Ez a hiba valószínűleg akkor fordul elő, ha a nem megfelelő végpont van használatban. A végpont, amely állít elő, ez a hiba van a következő mintának:

`https:// YOUR_DATABASE_ACCOUNT.documents.azure.com:443/`

Ez az a graph-adatbázis a dokumentumok végpont.  A megfelelő végpontra való használatához a Gremlin-végpont, amely rendelkezik a következő formátumban:

`https://YOUR_DATABASE_ACCOUNT.gremlin.cosmosdb.azure.com:443/`

### <a name="why-am-i-getting-the-requestrateistoolarge-error"></a>Miért jelenik meg a "RequestRateIsTooLarge" hiba?

Ez a hiba, az azt jelenti, hogy a lefoglalt kérelemegység / másodperc nem elegendő a lekérdezés kiszolgálása érdekében. Ez a hiba általában látható, amely lekéri az összes csúcsot lekérdezés futtatásakor:

```
// Query example:
g.V()
```

Ez a lekérdezés megkísérli a gráf összes csúcspontok lekérése. Tehát ez a lekérdezés költsége lesz legalább az azonos számú kérelemegység tekintetében csúcspontok. Oldja meg ezt a lekérdezést az RU/s-beállítást kell állítani.

### <a name="why-do-my-gremlin-driver-connections-get-dropped-eventually"></a>Miért hajtsa végre a Gremlin-illesztőprogram kapcsolatok lekérése eldobott idővel?

A Gremlin kapcsolat WebSocket kapcsolaton keresztül. Bár a WebSocket-kapcsolatok nem rendelkezik egy adott időpont Live, az Azure Cosmos DB Gremlin API megszűnik tétlen kapcsolatok 30 perc inaktivitás után.

### <a name="why-cant-i-use-fluent-api-calls-in-the-native-gremlin-drivers"></a>Miért nem használható a natív Gremlin-illesztőprogramok fluent API-hívások?

Fluent API-hívások még az Azure Cosmos DB Gremlin API által nem támogatott. Fluent API-hívások bytecode támogatása, amely az Azure Cosmos DB Gremlin API jelenleg nem támogatja más néven egy belső formázási szolgáltatás szükséges. Ugyanebből az okból miatt a legújabb Gremlin-JavaScript-illesztőprogramot is jelenleg nem támogatott.

### <a name="how-can-i-evaluate-the-efficiency-of-my-gremlin-queries"></a>Hogyan tudom értékelni a Gremlin-lekérdezések hatékonyságának?

A **executionProfile()** előzetes lépés segítségével adja meg a lekérdezés-végrehajtási terv elemzése. Ebben a lépésben leírtak szerint a következő példában bármely Gremlin-lekérdezés végére hozzá kell adnia:

**Lekérdezési példa**

```
g.V('mary').out('knows').executionProfile()
```

**Példa a kimenetre**

```json
[
  {
    "gremlin": "g.V('mary').out('knows').executionProfile()",
    "totalTime": 8,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 3,
        "annotations": {
          "percentTime": 37.5
        },
        "counts": {
          "resultCount": 1
        }
      },
      {
        "name": "GetEdges",
        "time": 5,
        "annotations": {
          "percentTime": 62.5
        },
        "counts": {
          "resultCount": 0
        },
        "storeOps": [
          {
            "count": 0,
            "size": 0,
            "time": 0.6
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 0
        }
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 0
        }
      }
    ]
  }
]
```

A fenti profil kimenete azt mutatja, hogy mennyi idő telt el a csúcspont-objektumok, a peremhálózat-objektumok és a munkahalmaz méretének beszerzése során. Ez a standard szintű költség mérések az Azure Cosmos DB-lekérdezések kapcsolódik.

## <a id="cassandra"></a> Cassandra API

### <a name="what-is-the-protocol-version-supported-by-azure-cosmso-db-cassandra-api-is-there-a-plan-to-support-other-protocols"></a>Mi az Azure Cosmso DB Cassandra API által támogatott protokoll-verzió? Egyéb protokollok támogatása csomag van?

Az Azure Cosmos DB Apache Cassandra API ma CQL 4-es verziója támogatja. Ha más protokollt támogatásával kapcsolatos visszajelzést szeretne küldeni, ossza meg velünk keresztül [felhasználói visszajelzések](https://feedback.azure.com/forums/263030-azure-cosmos-db) , vagy küldjön egy e-mailek [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com).

### <a name="why-is-choosing-a-throughput-for-a-table-a-requirement"></a>Miért megválasztása a tábla egy átviteli követelmény?

Az Azure Cosmos DB alapértelmezett átviteli sebességet beállítja a tárolóhoz rendeltek, ahol létrehozhatja az a tábla - portálon vagy a CQL.
Az Azure Cosmos DB garanciákat nyújt a teljesítmény és a késés, a felső korlátja a műveletet. Ez garantálja a motor kényszerítheti a cégirányítási műveletek a bérlő esetén lehetséges. Beállítás átviteli biztosítja, hogy kap a garantált átviteli sebességgel és késéssel, mert a platform fenntartja a kapacitást, és garantálja a művelet sikeres.
Rugalmasan módosíthatja az átviteli sebességet, hogy az alkalmazás a szezonalitás előnyös és költségeit.

Az átviteli sebesség koncepció ismertetése a [Azure Cosmos DB-ben a kérelemegység](request-units.md) cikk. Egy táblát az átviteli sebesség egyenlően a mögöttes fizikai partíciók között.

### <a name="what-is-the-default-rus-of-table-when-created-through-cql-what-if-i-need-to-change-it"></a>Mi az az alapértelmezett RU/s tábla létrehozásakor CQL keresztül? Mi történik, ha szükséges, módosítsa?

Az Azure Cosmos DB kérelemegység / másodperc (RU/s) használja, amelyek biztosítják az átviteli sebesség a pénznem. CQL keresztül létrehozott táblák rendelkezik 400 RU. Módosíthatja a Kérelemegység a portálról.

CQL

```
CREATE TABLE keyspaceName.tablename (user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=1200
```

.NET

```csharp
int provisionedThroughput = 400;
var simpleStatement = new SimpleStatement($"CREATE TABLE {keyspaceName}.{tableName} (user_id int PRIMARY KEY, lastname text)");
var outgoingPayload = new Dictionary<string, byte[]>();
outgoingPayload["cosmosdb_provisioned_throughput"] = Encoding.UTF8.GetBytes(provisionedThroughput.ToString());
simpleStatement.SetOutgoingPayload(outgoingPayload);
```

### <a name="what-happens-when-throughput-is-used-up"></a>Mi történik a átviteli sebesség használatakor?

Az Azure Cosmos DB garanciákat nyújt a teljesítmény és a késés, a felső korlátja a műveletet. Ez garantálja a motor kényszerítheti a cégirányítási műveletek a bérlő esetén lehetséges. Ez akkor lehetséges, az átviteli sebességet, amely biztosítja, hogy kap a garantált átviteli sebességgel és késéssel, mert a platform fenntartja a kapacitást, és garantálja a művelet sikeres beállítás alapján.
Ez a kapacitás felszámolunk kap túlterhelt üzenet, miszerint a kapacitás fel lett megadva.
0x1001 túlterhelt: nem lehet feldolgozni a kérelmet, mert a "Túl sok kérelmet adott". Ebben a pillanatban elengedhetetlen, hogy milyen műveleteket, és a mennyiségi okozza a problémát. Felhasznált kapacitás történik a portálon keresztül a kiosztott kapacitás metrikákkal kapcsolatos ötlet kérheti le. Ezután győződjön meg arról, szinte felhasznált kapacitás egyaránt összes alapul szolgáló partíciók között. Ha egy partíció által felhasznált átviteli a legtöbb, rendelkezik torzulása számítási feladat.

Metrikák érhetők el, amelyek bemutatják, átviteli sebesség használatáról órában, napban, és a egy hét nap, több partícióra kiterjedő vagy Összesítés. További információkért lásd: [megfigyelési és a hibakeresést az Azure Cosmos DB metrikákkal](use-metrics.md).

Diagnosztikai naplók mutatjuk be a [diagnosztikai naplózás az Azure Cosmos DB](logging.md) cikk.

### <a name="does-the-primary-key-map-to-the-partition-key-concept-of-azure-cosmos-db"></a>Az elsődleges kulcs térkép az Azure Cosmos DB a partíciós kulcs fogalma nem?

Igen, a partíciókulcs megfelelő helyen helyezi el az entitás szolgál. Az Azure Cosmos DB szolgál, hogy egy fizikai partíciónak jobb logikai partíció található. A particionálási fogalma jól ismertetése a [particionálási és horizontális az Azure Cosmos DB](partition-data.md) cikk. Az alapvető hajtsa végre a megfelelő nyelvet a lenti listában itt látható, hogy a logikai partíció nem haladnak át a 10 GB-os korlát még ma.

### <a name="what-happens-when-i-get-a-quota-full-notification-indicating-that-a-partition-is-full"></a>Mi történik, ha a teljes kvóta jelenik meg:"értesítés arról, hogy a partíció nem teljes?

Az Azure Cosmos DB egy rendszer SLA-alapú, amely korlátlan számú skálázási késés, átviteli sebesség, rendelkezésre állás és konzisztencia vonatkozó garanciákat biztosít. A korlátlan tárolási kívül fő fogalmat, a particionálás használata az adatok horizontális skálázás alapul. A particionálási fogalma jól ismertetése a [particionálási és horizontális az Azure Cosmos DB](partition-data.md) cikk.

Az entitások vagy meg kell felelnie logikai partíciónként elemek számának 10 GB-os korlát. Győződjön meg arról, hogy az alkalmazás jól skálázható, javasoljuk, hogy Ön *nem* az egy partícióban kapcsolatos összes információ tárolása és lekérdezése, gyakori elérésű partíciót létrehozni. Ez a hiba csak bármikor visszatérhet, ha az adatok torzítja van: egy partíciókulcsot az adatok rengeteg rendelkezik, (több mint 10&nbsp;GB). A storage-portál használata az adatok eloszlása találja. Ez a hiba elhárításához módja hozza létre újból a táblát, és válassza a részletes elsődleges (partíciókulcs), amely lehetővé teszi az adatok jobb elosztása.

### <a name="is-it-possible-to-use-cassandra-api-as-key-value-store-with-millions-or-billions-of-individual-partition-keys"></a>A Cassandra API-kulcs-érték tárolóként használható a több millió vagy egyéni partíciókulcsok milliárd?

Az Azure Cosmos DB korlátlan adatot tárolhat a storage horizontális felskálázásával. Ez az az átviteli sebesség független. Tárolására és beolvasására, kulcs/érték megadásával a megfelelő elsődleges/partíciókulcs Igen Cassandra API csak mindig használhatja. Ezek a egyedi kulcsok saját logikai partíció beolvasása, és problémamentesen fizikai partíciók felett található.

### <a name="is-it-possible-to-create-more-than-one-table-with-apache-cassandra-api-of-azure-cosmos-db"></a>Az egynél több táblában létrehozható az Apache Cassandra API-t az Azure Cosmos DB?

Igen, akkor lehet az Apache Cassandra API-val egynél több tábla létrehozásához. Ezeket a táblákat és tárolásért egység számít.

### <a name="is-it-possible-to-create-more-than-one-table-in-succession"></a>Az egymás után több tábla létrehozására?

Az Azure Cosmos DB rendszer erőforrás szabályozott adatokat és a vezérlési sík tevékenységekhez. Tárolók, például gyűjtemények, táblák olyan futásidejű entitások, amelyek a megadott átviteli kapacitás kiépítése. Egymás után gyorsan tárolók létrehozása nem várt tevékenységet, és szabályozott. Ha eldobási/táblák létrehozása azonnal teszteket, próbálja meg a lemezterület-azokat.

### <a name="what-is-maximum-number-of-tables-that-can-be-created"></a>Mit jelent a létrehozott táblák maximális száma?

Nem fizikai, a táblák száma korlátozott, küldjön egy e-mailt [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com) Ha nagy számú táblák (ahol keresztül haladnak állandó teljes mérete 10 TB-nyi adatot), amely kell létrehozni a szokásos 10s vagy 100s.

### <a name="what-is-the-maximum--of-keyspace-that-we-can-create"></a>Mi az, hogy hozhat létre kulcstér maximális száma?

Ezek nincs fizikai korlátlan számú keyspaces metaadat-tárolók, küldjön egy e-mailt [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com) Ha valamilyen okból keyspaces nagy számú rendelkezik.

### <a name="is-it-possible-to-bring-in-lot-of-data-after-starting-from-normal-table"></a>Az is nagy mennyiségű adatot betöltheti a normál táblából elindítása után?

A tárolási kapacitás automatikusan kezeli, és leküldte a további adatok minél jobban növekszik. Így magabiztosan importálhatja adatmennyiség kezelése és a csomópontok, és több üzembe helyezése nélkül.

### <a name="is-it-possible-to-supply-yaml-file-settings-to-configure-apache-casssandra-api-of-azure-cosmos-db-behavior"></a>Az Apache Casssandra API az Azure Cosmos DB-viselkedés yaml fájl beállításait adja meg a lehetséges?

Az Apache Cassandra API-t az Azure Cosmos DB platform szolgáltatása. Szolgáltatói kompatibilitási protokoll biztosítja a műveletek végrehajtása. Elrejti azonnal felügyeleti, monitorozási és konfigurációs bonyolultságát. Fejlesztői/felhasználóként nem kell aggódnia a rendelkezésre állási, törlésjelzőit, kulcsfontosságú gyorsítótár, sor gyorsítótár, virágzó szűrő és számos egyéb beállítást. Az Azure Cosmos DB Apache Cassandra API megoldásai lehetővé, hogy és olvasási teljesítményt megkövetelése nélkül konfigurálással és felügyelettel járó többletterhelést.

### <a name="will-apache-cassandra-api-for-azure-cosmos-db-support-node-additioncluster-statusnode-status-commands"></a>Az Azure Cosmos DB Apache Cassandra API támogatja csomópont hozzáadása/fürt állapotának/csomópont állapota parancsokat?

Az Apache Cassandra API olyan platformszolgáltatás, amely lehetővé teszi a kapacitástervezés, az átviteli sebesség és a tárhely rugalmassági igényeinek válaszol egy pofonegyszerű. Az Azure Cosmos DB, kioszthatja az átviteli sebességet, szükséges. Majd méretezhető felfelé és tetszőleges számú alkalommal napon keresztül, nem kell bajlódnunk csomópontok hozzáadásával és eltávolításával, vagy kezelnie azokat. Ez azt jelenti, hogy nem kell a csomópontot, a fürtkezelő túl használja.

### <a name="what-happens-with-respect-to-various-config-settings-for-keyspace-creation-like-simplenetwork"></a>Mi történik, különböző konfigurációs beállítások kulcstér létrehozására például egyszerű/hálózati vonatkozóan?

Azure Cosmos DB biztosítja a rendelkezésre állás és a közel valós idejű okokból kulcsrakész globális disztribúciót. Nem kell telepítő replikák vagy egyebek. Minden írás mindig tartósan a kvórumot minden olyan régióban, ahol a teljesítményre vonatkozó garanciákat ír.

### <a name="what-happens-with-respect-to-various-settings-for-table-metadata-like-bloom-filter-caching-read-repair-change-gcgrace-compression-memtableflushperiod-and-more"></a>Olvassa el vonatkozóan különböző beállításait tábla metaadatainak virágzó szűrőt, például a gyorsítótárazás, mi történik, javítás módosítása, gc_grace, tömörítés memtable_flush_period és egyéb?

Az Azure Cosmos DB olvasási/írási műveleteket és kell foglalkoznia a konfigurációs beállításait, és véletlenül kezelésére szolgáló őket átviteli teljesítményt nyújt.

### <a name="is-time-to-live-ttl-supported-for-cassandra-tables"></a>Cassandra-táblák esetén támogatott idő – Élettartam (TTL)?

Igen, TTL támogatott.

### <a name="is-it-possible-to-monitor-node-status-replica-status-gc-and-os-parameters-earlier-with-various-tools-what-needs-to-be-monitored-now"></a>Ennyi az egész lehet figyelő csomópont állapota, a replika állapota, a globális katalógus és a különböző eszközök révén korábban operációs rendszer paraméterek? Mit kell figyelni most?

Az Azure Cosmos DB egy platformszolgáltatás, amely segít a hatékonyság növeléséhez, és nem kell foglalkoznia kezeléséhez és monitorozásához infrastruktúra. Egyszerűen, amely elérhető a portál mérőszámai keresése, ha, szabályozva van első és növeléséhez vagy csökkentéséhez, hogy az átviteli teljesítmény gondoskodik.
A figyelő [SLA-k](monitor-accounts.md).
Használat [metrikák](use-metrics.md) használata [diagnosztikai naplók](logging.md).

### <a name="which-client-sdks-can-work-with-apache-cassandra-api-of-azure-cosmos-db"></a>Melyik ügyfél SDK-k is használható az Apache Cassandra API-t az Azure Cosmos DB?

Az Apache Cassandra SDK az CQLv3-t használó ügyféloldali illesztőprogramjait használták az ügyfélalkalmazások számára. Ha rendelkezik más illesztőprogramokat használja, vagy ha felmerült problémákat, az e-mailek küldésére [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com).

### <a name="is-composite-partition-key-supported"></a>Összetett partíciókulcs támogatott?

Igen, rendszeres szintaxis használatával összetett partíciós kulcs létrehozása.

### <a name="can-i-use-sstableloader-for-data-loading"></a>Használhatom a sstableloader az betöltéshez?

Nem, a sstableloader nem támogatott.

### <a name="can-an-on-premises-apache-cassandra-cluster-be-paired-with-azure-cosmos-dbs-cassandra-api"></a>Párosítható a helyszíni Apache Cassandra-fürt a Azure Cosmos DB Cassandra APIával?

A jelenlegi Azure Cosmos DB egy optimalizált megoldás műveletek nélkül terhelés a felhőalapú környezet rendelkezik. Üzenet küldése a párosítást követelik meg, ha [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com) az az eset leírását. Arra törekszünk, hogy a helyszíni/eltérő Cloud Cassandra-fürtöt a Cosomos adatbázis Cassandra APIához párosítsa.

### <a name="does-cassandra-api-provide-full-backups"></a>Biztosítja a Cassandra API a teljes biztonsági mentés?

Az Azure Cosmos DB biztosítja a két ingyenes teljes mentésekre négy óra időköz még ma az összes API-k között. Ez biztosítja, hogy nem kell állítsa be a biztonsági mentési ütemezést és egyebek.
Ha azt szeretné, módosíthatja a megőrzési és gyakoriságát, küldjön egy e-mailek [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com) vagy egy támogatási esetet előléptetése. Információ a biztonsági mentési funkció megtalálható a [automatikus online biztonsági mentés és visszaállítás az Azure Cosmos DB](online-backup-and-restore.md) cikk.

### <a name="how-does-the-cassandra-api-account-handle-failover-if-a-region-goes-down"></a>Hogyan a Cassandra API-fiókot kezeli feladatátvételi Ha leáll egy régióban?

Az Azure Cosmos DB Cassandra API telephelyeken az Azure Cosmos DB globálisan elosztott platformról. Győződjön meg arról, hogy az alkalmazás működését adatközpont-leállás, engedélyezni kell legalább egy további régióban, a fiók az Azure Cosmos DB portál [fejlesztés a többrégiós Azure Cosmos DB-fiókok](high-availability.md). A portál használatával beállíthatja az a régió prioritásának [fejlesztés a többrégiós Azure Cosmos DB-fiókok](high-availability.md).

A fiók, és szabályozhatja, ahol azt átveheti azáltal, hogy a feladatátvétel prioritási tetszőleges számú régiót adhat hozzá. Az adatbázis használatára, meg kell adnia a-alkalmazást is túl. Ha így tesz, az ügyfelek nem szoftverkarbantartás.

### <a name="does-the-apache-cassandra-api-index-all-attributes-of-an-entity-by-default"></a>Az Apache Cassandra API index egy entitás összes attribútumának alapértelmezés szerint?

Cassandra API azt tervezi, hogy támogatja a másodlagos indexelést, hogy bizonyos attribútumokon a szelektív indexet hozzon létre. 


### <a name="can-i-use-the-new-cassandra-api-sdk-locally-with-the-emulator"></a>Használható az új Cassandra API SDK-t helyi emulátorral?

Igen, ez támogatott.

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-change-feed-and-other-functionality-will-these-capabilities-be-added-to-the-cassandra-api"></a>Úgy tűnik, hogy a platform számos képességgel rendelkezik, például a hírcsatornák és egyéb funkciók módosítására. Azure Cosmos DB Ezek a képességek hozzáadódik a Cassandra API-t?

Az Apache Cassandra API Apache Cassandra azonos CQL funkciókat biztosít. Tervezzük megvalósítható-képességek a jövőbeli támogatási megvizsgáljuk.

### <a name="feature-x-of-regular-cassandra-api-isnt-working-as-today-where-can-the-feedback-be-provided"></a>Rendszeres Cassandra API x funkciója nem működik, még ma, ahol is a visszajelzést adni?

Visszajelzés küldése keresztül [felhasználói visszajelzések](https://feedback.azure.com/forums/263030-azure-cosmos-db).

[azure-portal]: https://portal.azure.com
[query]: sql-api-sql-query.md
