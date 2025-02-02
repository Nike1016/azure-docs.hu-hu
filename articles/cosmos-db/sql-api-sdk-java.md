---
title: 'Az Azure Cosmos DB: SQL Java API, SDK & resources'
description: Mindent megtudhat a SQL Java API-t és az SDK kiadási dátum, kivezetési dátum és az Azure Cosmos DB SQL Java SDK minden verziója közötti végzett módosításokat.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 03/13/2019
ms.author: sngun
ms.openlocfilehash: 7af43d24b03a4c548925bdca4189fd0524c701a4
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341639"
---
# <a name="azure-cosmos-db-java-sdk-for-sql-api-release-notes-and-resources"></a>Az Azure Cosmos DB Java SDK-t az SQL API-hoz: Kibocsátási megjegyzések és erőforrások
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET-módosítási hírcsatorna](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Aszinkron Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST erőforrás-szolgáltató](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [BulkExecutor – .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [BulkExecutor – Java](sql-api-sdk-bulk-executor-java.md)

Az SQL API-t a Java SDK támogatja a szinkron műveletek. Aszinkron támogatást, használja a [SQL API aszinkron Java SDK](sql-api-sdk-async-java.md). 

| |  |
|---|---|
|**SDK letöltése**|[Maven 3](https://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22com.microsoft.azure%22%20AND%20a%3A%22azure-documentdb%22)|
|**API-dokumentáció**|[Java API dokumentációja](/java/api/com.microsoft.azure.documentdb)|
|**Hozzájárul az SDK-t**|[GitHubon](https://github.com/Azure/azure-documentdb-java/)|
|**Első lépések**|[A Java SDK használatának első lépései](sql-api-java-get-started.md)|
|**Alapú webappokról szóló oktatóanyagunkat**|[Webalkalmazás-fejlesztés az Azure Cosmos DB használatával](sql-api-java-application.md)|
|**Minimális támogatott futtatókörnyezet**|[Java fejlesztői készlet (JDK) 7 +](https://aka.ms/azure-jdks)|

## <a name="release-notes"></a>Kibocsátási megjegyzések

### <a name="a-name213213"></a><a name="2.1.3"/>2.1.3
* Kijavítva a hiba a PartitionKey kivonatoló v2-ben.

### <a name="a-name212212"></a><a name="2.1.2"/>2.1.2
* Az összetett indexek támogatása.
* Kijavítva a hiba az globális végpontja manager frissítés kényszerítése.
* Kijavítva a hiba a upserts előtti feltételekkel közvetlen módban.

### <a name="a-name211211"></a><a name="2.1.1"/>2.1.1
* Kijavítva a hiba átjáró cím gyorsítótárban.

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0
* Többrégiós írási támogatása az közvetlen üzemmódban.
* Támogatás hozzáadva a kezelési IOExceptions ServiceUnavailable kivételeket, a proxy lépett fel.
* Kijavítva a hiba, a végpont felderítési újrapróbálkozási szabályzat.
* Kijavítva a hiba, NULL értékű mutató nem kivételek BaseDatabaseAccountConfigurationProvider a biztosításához.
* Kijavítva a hiba, annak érdekében, hogy QueryIterator nem ad vissza a null értékeket.
* Kijavítva a hiba, annak érdekében, hogy nagy PartitionKey engedélyezett

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0
* Többrégiós írási támogatása az átjáró mód.

### <a name="a-name11641164"></a><a name="1.16.4"/>1.16.4
* Kijavítva a hiba, olvassa el a partíción kulcs egy lekérdezés tartományokat.

### <a name="a-name11631163"></a><a name="1.16.3"/>1.16.3
* Kijavítva a hiba, a beállítás a folytatási token fejléc mérete DirectHttps módban.

### <a name="a-name11621162"></a><a name="1.16.2"/>1.16.2
* Támogatás hozzáadva streamelési feladatátvétele.
* Egyéni metaadatainak támogatása.
* Továbbfejlesztett munkamenet logikai kezelése.
* Kijavítva a hiba, a partíció kulcstartományhoz gyorsítótárban.
* Közvetlen üzemmódban NPE hibája kijavítva.

### <a name="a-name11611161"></a><a name="1.16.1"/>1.16.1
* Támogatás hozzáadva a Unique Index.
* Támogatás hozzáadva a hírcsatorna-beállítások folytatási jogkivonat mérete korlátozza.
* Kijavítva a hiba, a Json-szerializálás (időbélyeg).
* Kijavítva a hiba, a Json-szerializálás (enum).
* Függőség com.fasterxml.jackson.core:jackson databind 2.9.5 frissítve.

### <a name="a-name11601160"></a><a name="1.16.0"/>1.16.0
* Továbbfejlesztett kapcsolatkészlet közvetlen mód.
* Javult az orderby közötti partíciólekérdezés előzetes betöltési javítása.
* Továbbfejlesztett UUID generációja.
* Továbbfejlesztett munkamenet-konzisztencia logikát.
* Támogatás hozzáadva a multipolygon.
* Támogatás hozzáadva a partíciós kulcs tartomány statisztika a gyűjteményhez.
* Kijavítva a hiba, a több régió támogatása.

### <a name="a-name11501150"></a><a name="1.15.0"/>1.15.0
* A Json-szerializálást teljesítményét.
* Az SDK-verziója letölthető Azure Cosmos DB Emulatort elérhető legújabb verziója szükséges https://aka.ms/cosmosdb-emulator.

### <a name="a-name11401140"></a><a name="1.14.0"/>1.14.0
* A Microsoft barátok kódtárak belső módosítása.

### <a name="a-name11301130"></a><a name="1.13.0"/>1.13.0
* Kijavítva az olvasó egypartíciós kulcstartományokkal.
* Javítva lett egy probléma ResourceID rövid névvel rendelkező adatbázis-elemzés, amely hatással van.
* Kijavítva egy probléma oka a partíciós kulcsok kódolása.

### <a name="a-name11201120"></a><a name="1.12.0"/>1.12.0
* A kérelem feldolgozása során partíció elágazást fontos hibajavításokat tartalmaz.
* Kijavítva az erős és BoundedStaleness konzisztenciaszintek.

### <a name="a-name11101110"></a><a name="1.11.0"/>1.11.0
* Egy új konzisztenciaszint támogatása az új ConsistentPrefix nevezik.
* Kijavítva a hiba, munkamenetmód a gyűjtemény olvasási.

### <a name="a-name11001100"></a><a name="1.10.0"/>1.10.0
* Particionált gyűjteménybe, mint az engedélyezett támogatása mint 2500 RU/s alacsony, és 100 RU/s-os léptékben méretezhető.
* Kijavítva a hiba, amely hatására NullRef kivétel néhány lekérdezést a natív szerelvényben.

### <a name="a-name196196"></a><a name="1.9.6"/>1.9.6
* Kijavítva a hiba, a lekérdezési motor konfigurációjában, amelyek átjáró módban lekérdezést kivételek okozhatnak.
* Rögzített néhány hibákat okozhat a dokumentumgyűjtemény létrehozása után azonnal kérelmek "Tulajdonosa erőforrás nem található" kivétel munkamenet-tárolóban.

### <a name="a-name195195"></a><a name="1.9.5"/>1.9.5
* Összesítés lekérdezések (száma, MIN, MAX, SUM és átlagos) támogatása. Lásd: [összesítési támogatási](sql-query-aggregates.md).
* A módosítási hírcsatorna támogatása.
* Támogatás hozzáadva a gyűjtemény kvótákra vonatkozó információk RequestOptions.setPopulateQuotaInfo keresztül.
* Tárolt eljárás parancsfájl naplózást RequestOptions.setScriptLoggingEnabled támogatása.
* Kijavítva a hiba, ahol lekérdezés DirectHttps módban nem válaszol hajt végre, amikor a szabályozási hibák.
* Kijavítva a hiba, a munkamenet-konzisztencia módját.
* Kijavítva a hiba, amely NullReferenceException bizonyos okozhat a HttpContext, ha a kérések aránya túl magas.
* Továbbfejlesztett teljesítmény DirectHttps mód.

### <a name="a-name194194"></a><a name="1.9.4"/>1.9.4
* A hozzáadott egyszerű ügyfél példány-alapú proxy-támogatás ConnectionPolicy.setProxy() API-val.
* A hozzáadott DocumentClient.close() API-t megfelelően leállítási DocumentClient-példányt.
* Továbbfejlesztett lekérdezési teljesítmény közvetlen kapcsolódás módban a lekérdezésterv származó a natív szerelvény helyett az átjáró által.
* Állítsa be a FAIL_ON_UNKNOWN_PROPERTIES = false, hogy a felhasználók nem határozhat meg JsonIgnoreProperties azok pojo-vá.
* A naplózás újratervezhetők SLF4J használatára.
* Rögzített, néhány más hibák konzisztencia-olvasó.

### <a name="a-name193193"></a><a name="1.9.3"/>1.9.3
* Kijavítva a hiba, a kapcsolat-kezelés közvetlen kapcsolódás módban kapcsolat adatszivárgás megelőzése érdekében.
* Kijavítva a hiba a felső lekérdezésben, ahol NullReference kivétel generálhat.
* Továbbfejlesztett teljesítmény a belső gyorsítótárak esetében a hálózati hívások számának csökkentésével.
* Új ügyfélállapotok kódjai, ActivityID és a kérelem URI-t DocumentClientException hibaelhárítást.

### <a name="a-name192192"></a><a name="1.9.2"/>1.9.2
* A kapcsolat kezelése a stabilitást javítva lett egy probléma.

### <a name="a-name191191"></a><a name="1.9.1"/>1.9.1
* Támogatás hozzáadva a BoundedStaleness konzisztencia szintjét.
* CRUD-műveletek esetében a particionált gyűjtemények közvetlen kapcsolódás támogatása.
* Kijavítva a hiba, az SQL-adatbázis lekérdezéséhez.
* Kijavítva a hiba, ahol munkamenet jogkivonata nem megfelelően beállítható munkamenet-gyorsítótárában.

### <a name="a-name190190"></a><a name="1.9.0"/>1.9.0
* Támogatás hozzáadva a párhuzamos lekérdezések partíció közötti.
* Támogatás hozzáadva a particionált gyűjtemények felső/ORDER BY lekérdezések.
* Erős konzisztencia támogatása.
* Támogatás hozzáadva a neve alapján kérelmek közvetlen kapcsolat használata esetén.
* Kijavítva, hogy az összes kérelem újrapróbálkozások között következetes maradjon ActivityId.
* Kijavítva a hiba, a munkamenet-gyorsítótár során újra létre kellene hoznia egy gyűjtemény ugyanazzal a névvel kapcsolatos.
* A hozzáadott sokszög és LineString adattípusok közben adja meg a gyűjtemény indexelési szabályzat a geokerítés-térinformatikai lekérdezéseket.
* Java Doc Java 1.8-as rögzített problémái.

### <a name="a-name181181"></a><a name="1.8.1"/>1.8.1
* Kijavítva a hiba, a gyorsítótár egypartíciós gyűjteményeket, és nem kulcs kérelmek particionálása extra fetch PartitionKeyDefinitionMap.
* Kijavítva a hiba, akkor egy érvénytelen partíciós kulcsérték nem újra.

### <a name="a-name180180"></a><a name="1.8.0"/>1.8.0
* Többrégiós adatbázisfiókhoz támogatása.
* A szabályozott kérelmeinek száma szabhatja testre a maximális újrapróbálkozási kísérletek és a várakozási idő maximális újrapróbálkozási beállításokkal automatikus újrapróbálkozási támogatása.  RetryOptions és ConnectionPolicy.getRetryOptions() témakörben talál.
* Elavult IPartitionResolver alapú particionálási egyéni kódot. A particionált gyűjtemények használata magasabb tárolási és átviteli sebességet.

### <a name="a-name171171"></a><a name="1.7.1"/>1.7.1
* A hozzáadott újrapróbálkozási házirend támogatása a sebességkorlátozás.  

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
* Dokumentumok élő (TTL) támogatási hozzáadott ideje.

### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0
* Megvalósított [particionált gyűjtemények](partition-data.md) és [felhasználó által definiált teljesítményszintek](performance-levels.md).

### <a name="a-name151151"></a><a name="1.5.1"/>1.5.1
* Kijavítva a hiba a HashPartitionResolver kivonatértékeket más SDK-k konzisztens bájtsorrendű létrehozni.

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0
* Tartomány & ujjlenyomat hozzáadása, amelyek segítik a horizontális skálázás alkalmazásokat, hogy több partíción feloldók particionálásához.

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0
* Upsert megvalósításához. Új upsertXXX módszerek Upsert funkció támogatása érdekében adott hozzá.
* Azonosító-alapú útválasztás megvalósítása. Nincs nyilvános API-módosítás, belső összes módosítást.

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0
* Ahhoz, hogy a verziószám más SDK-k ciklustól kihagyva kiadás

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Támogatja a térinformatikai Index
* Ellenőrzi az összes erőforrás ID tulajdonsága. Az erőforrások azonosítóit nem tartalmazhat?, /, #, \, karaktereket vagy záró szóközzel.
* ResourceResponse ad hozzá új fejléc "index átalakítási folyamat".

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Indexelési házirend V2 valósít meg

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* GA SDK

## <a name="release-and-retirement-dates"></a>Kiadás és kivezetési dátuma
A Microsoft legalább értesítést küldenek **12 hónapig** kivonása egy SDK-t kiegyenlítse az a és újabb támogatott verzióra váltás előtt.

Új szolgáltatások és funkciók és optimalizálási lehetőségek csak hozzá az aktuális SDK-hoz, ezért javasoljuk, hogy mindig a legújabb SDK verzióra frissít leghamarabb lehető.

Cosmos DB-hez a kivont SDK használatával bármilyen kérelmet a rendszer elutasítja a szolgáltatás által.

> [!WARNING]
> Az összes verzió **1.x** az SQL SDK for Java-jén kivonjuk a forgalomból **2020 május 30-as**.
> 
>

> [!WARNING]
> Összes verzióját az SQL SDK a Javához készült verzió előtt **1.0.0-s** án visszavontuk **2016. február 29-én**.
> 
> 

<br/>

| Verzió | Kiadás dátuma | Visszavonás dátuma |
| --- | --- | --- |
| [2.1.3](#2.1.3) |2018. március 13. |--- |
| [2.1.2](#2.1.2) |2018. március 09. |--- |
| [2.1.1](#2.1.1) |2018. december 13. |--- |
| [2.1.0](#2.1.0) |2018. november 20. |--- |
| [2.0.0](#2.0.0) |2018. Szeptembertől 21. |--- |
| [1.16.4](#1.16.4) |2018. Szeptembertől 10. |2020. május 30. |
| [1.16.3](#1.16.3) |2018. Szeptembertől 09. |2020. május 30. |
| [1.16.2](#1.16.2) |2018. június 29. |2020. május 30. |
| [1.16.1](#1.16.1) |2018. május 16. |2020. május 30. |
| [1.16.0](#1.16.0) |2018. március 15. |2020. május 30. |
| [1.15.0](#1.15.0) |2017. november 14. |2020. május 30. |
| [1.14.0](#1.14.0) |2017. október 28. |2020. május 30. |
| [1.13.0](#1.13.0) |2017. augusztus 25-én |2020. május 30. |
| [1.12.0](#1.12.0) |2017. július 11. |2020. május 30. |
| [1.11.0](#1.11.0) |2017. május 10. |2020. május 30. |
| [1.10.0](#1.10.0) |2017. március 11. |2020. május 30. |
| [1.9.6](#1.9.6) |2017. február 21. |2020. május 30. |
| [1.9.5](#1.9.5) |2017. január 31-ig. |2020. május 30. |
| [1.9.4](#1.9.4) |2016. november 24-én |2020. május 30. |
| [1.9.3](#1.9.3) |2016. október 30. |2020. május 30. |
| [1.9.2](#1.9.2) |2016. október 28. |2020. május 30. |
| [1.9.1](#1.9.1) |2016. október 26. |2020. május 30. |
| [1.9.0](#1.9.0) |2016. október 03. |2020. május 30. |
| [1.8.1](#1.8.1) |2016. június 30. |2020. május 30. |
| [1.8.0](#1.8.0) |2016. június 14-én |2020. május 30. |
| [1.7.1](#1.7.1) |2016. április 30. |2020. május 30. |
| [1.7.0](#1.7.0) |2016. április 27-én |2020. május 30. |
| [1.6.0](#1.6.0) |2016. március 29-én |2020. május 30. |
| [1.5.1](#1.5.1) |2015. december 31-ig. |2020. május 30. |
| [1.5.0](#1.5.0) |2015. december 04. |2020. május 30. |
| [1.4.0](#1.4.0) |2015. október 05 |2020. május 30. |
| [1.3.0](#1.3.0) |2015. október 05 |2020. május 30. |
| [1.2.0](#1.2.0) |2015. augusztus 05 |2020. május 30. |
| [1.1.0](#1.1.0) |2015. július 09. |2020. május 30. |
| 1.0.1 |2015. május 12. |2020. május 30. |
| [1.0.0](#1.0.0) |2015. április 07. |2020. május 30. |
| 0.9.5-prelease |2015. március 09. |2016. február 29-én |
| 0.9.4-prelease |2015. február 17. |2016. február 29-én |
| 0.9.3-prelease |2015. január 13. |2016. február 29-én |
| 0.9.2-prelease |2014. december 19-én |2016. február 29-én |
| 0.9.1-prelease |2014. december 19-én |2016. február 29-én |
| 0.9.0-prelease |2014. december 10. |2016. február 29-én |

## <a name="faq"></a>GYIK
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Lásd még
Cosmos DB kapcsolatos további információkért lásd: [a Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) szolgáltatás lapján.

