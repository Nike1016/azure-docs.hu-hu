---
title: A Batch használata az alkalmazások teljesítményének javítására Azure SQL Database
description: A témakörből megtudhatja, hogy az adatbázis-műveletek feldolgozása nagy mértékben javítja a Azure SQL Database alkalmazások sebességét és méretezhetőségét. Habár ezek a Batch-eljárások bármilyen SQL Server-adatbázishoz működnek, a cikk középpontjában az Azure-ban olvashat.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: genemi
ms.date: 01/25/2019
ms.openlocfilehash: 3d18f5b77d08a55bd06656a72cbc02c040b6f127
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566250"
---
# <a name="how-to-use-batching-to-improve-sql-database-application-performance"></a>A Batch használata az alkalmazások teljesítményének javítására SQL Database

A Azure SQL Database kötegelt műveletek jelentősen javítják az alkalmazások teljesítményét és méretezhetőségét. Az előnyök megismerése érdekében a cikk első része néhány olyan vizsgálati eredményt ismertet, amelyek összehasonlítják a szekvenciális és a kötegelt kérelmeket egy SQL Database. A cikk hátralévő része azokat a technikákat, forgatókönyveket és szempontokat mutatja be, amelyek segítségével az Azure-alkalmazásokban sikeresen használhatja a kötegelt feldolgozást.

## <a name="why-is-batching-important-for-sql-database"></a>Miért fontos a kötegelt feldolgozás a SQL Database

A távoli szolgáltatásokra irányuló hívások egy jól ismert stratégia a teljesítmény és a méretezhetőség növeléséhez. A távoli szolgáltatásokkal kapcsolatos interakciók rögzített feldolgozási költségekkel rendelkeznek, például a szerializálással, a hálózati átvitelsel és a deszerializálással kapcsolatban. A sok különböző tranzakció egyetlen kötegbe való csomagolása csökkentheti ezeket a költségeket.

Ebben a dokumentumban különböző SQL Database kötegelt stratégiákat és forgatókönyveket szeretnénk megvizsgálni. Habár ezek a stratégiák a SQL Servert használó helyszíni alkalmazásokhoz is fontosak, számos oka lehet annak, hogy kiemelje a SQL Database a kötegelt feldolgozás használatát:

* A SQL Database elérése potenciálisan nagyobb hálózati késéssel jár, különösen abban az esetben, ha ugyanazon a Microsoft Azure adatközponton kívülről fér hozzá a SQL Databasehoz.
* A SQL Database több-bérlős jellemzői azt jelentik, hogy az adatelérési réteg hatékonysága összefügg az adatbázis teljes skálázhatóságával. A SQL Databasenak meg kell akadályoznia, hogy egyetlen bérlő/felhasználó az adatbázis-erőforrásokat kisajátítsa más bérlők kárára. Az előre meghatározott kvótákat meghaladó használatra válaszul SQL Database csökkentheti az átviteli sebességet, vagy reagálhat a szabályozás alóli kivételekre. A hatékonyság, például a kötegelt feldolgozás, lehetővé teszi, hogy több munkát SQL Database a korlátok elérése előtt. 
* A kötegelt feldolgozás olyan architektúrák esetében is érvényes, amelyek több adatbázist használnak (horizontális skálázás). Az egyes adatbázis-egységekkel való interakció hatékonysága továbbra is kulcsfontosságú tényező a teljes skálázhatóságban. 

A SQL Database használatának egyik előnye, hogy nem kell kezelnie az adatbázist futtató kiszolgálókat. Ez a felügyelt infrastruktúra azonban azt is jelenti, hogy az adatbázis-optimalizálásokkal kapcsolatban másképp kell gondolkodnia. Már nem tekintheti meg az adatbázis hardverének vagy hálózati infrastruktúrájának fejlesztését. Microsoft Azure szabályozza ezeket a környezeteket. A legfontosabb lehetőség, hogy az alkalmazás hogyan kommunikál a SQL Databaseokkal. A kötegelt feldolgozás az optimalizálások egyike. 

A tanulmány első része a SQL Databaset használó .NET-alkalmazásokra vonatkozó különböző kötegelt eljárásokat vizsgálja. Az utolsó két szakasz a batching irányelveit és forgatókönyveit tárgyalja.

## <a name="batching-strategies"></a>Kötegelt stratégiák

### <a name="note-about-timing-results-in-this-article"></a>Megjegyzés a cikk időzítési eredményeiről

> [!NOTE]
> Az eredmények nem teljesítménytesztek, hanem a **relatív teljesítmény**megjelenítésére szolgálnak. Az időzítések átlagosan legalább 10 teszt futtatásán alapulnak. A műveletek egy üres táblába szúrnak be. Ezeket a teszteket előzetesen Megmértük, és nem feltétlenül felelnek meg a V12-es adatbázisban az új [DTU szolgáltatási rétegekkel](sql-database-service-tiers-dtu.md) vagy [virtuális mag](sql-database-service-tiers-vcore.md)-szolgáltatásokkal megtapasztalható átviteli sebességnek. A batching technika relatív előnye hasonló lehet.

### <a name="transactions"></a>Tranzakciók

Furcsának tűnik, hogy megkezdi a kötegelt feldolgozás felülvizsgálatát a tranzakciók megvitatásával. Az ügyféloldali tranzakciók használata azonban finomabb kiszolgálóoldali kötegelt feldolgozást eredményez, amely javítja a teljesítményt. És a tranzakciókat csak néhány sornyi kóddal lehet felvenni, így gyors módszert biztosítanak a szekvenciális műveletek teljesítményének javítására.

Vegye figyelembe a C# következő kódot, amely egy egyszerű tábla INSERT és Update műveleteit tartalmazza.

```csharp
List<string> dbOperations = new List<string>();
dbOperations.Add("update MyTable set mytext = 'updated text' where id = 1");
dbOperations.Add("update MyTable set mytext = 'updated text' where id = 2");
dbOperations.Add("update MyTable set mytext = 'updated text' where id = 3");
dbOperations.Add("insert MyTable values ('new value',1)");
dbOperations.Add("insert MyTable values ('new value',2)");
dbOperations.Add("insert MyTable values ('new value',3)");
```
A következő ADO.NET-kód szekvenciálisan hajtja végre ezeket a műveleteket.

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    conn.Open();

    foreach(string commandString in dbOperations)
    {
        SqlCommand cmd = new SqlCommand(commandString, conn);
        cmd.ExecuteNonQuery();
    }
}
```

A kód optimalizálásának legjobb módja a hívások ügyféloldali kötegelt feldolgozásának megvalósítása. A kód teljesítményének növelését azonban egyszerűen becsomagolhatja egy tranzakcióban lévő hívások sorrendjébe. Itt ugyanaz a kód, amely tranzakciót használ.

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    conn.Open();
    SqlTransaction transaction = conn.BeginTransaction();

    foreach (string commandString in dbOperations)
    {
        SqlCommand cmd = new SqlCommand(commandString, conn, transaction);
        cmd.ExecuteNonQuery();
    }

    transaction.Commit();
}
```

A tranzakciók ténylegesen használatban vannak mindkét példában. Az első példában minden egyes hívás egy implicit tranzakció. A második példában egy explicit tranzakció az összes hívást lezárja. Az [írási tranzakciós naplóhoz](https://msdn.microsoft.com/library/ms186259.aspx)tartozó dokumentáció alapján a rendszer a tranzakció véglegesíte után naplózza a rekordokat a lemezre. Így azáltal, hogy több hívást is megadhat egy tranzakcióban, a tranzakciós naplóba való írás késleltetheti a tranzakció véglegesítését. Érvényben van, ha engedélyezi a kötegelt feldolgozást az írásokhoz a kiszolgáló tranzakciónaplójában.

Az alábbi táblázat néhány alkalmi tesztelési eredményt mutat be. A tesztek azonos szekvenciális lapkákat hajtottak végre tranzakciókkal és anélkül. További perspektívához az első tesztek távolról, egy laptopról a Microsoft Azure-adatbázisba futottak. A második teszt egy olyan felhőalapú szolgáltatásból és adatbázisból futott, amely mindkettő ugyanazon Microsoft Azure adatközpontban (USA nyugati régiójában) található. A következő táblázat az időtartamot mutatja be ezredmásodpercben, és tranzakció nélkül.

**A helyszínen az Azure-** ba:

| Műveletek | Nincs tranzakció (MS) | Tranzakció (MS) |
| --- | --- | --- |
| 1 |130 |402 |
| 10 |1208 |1226 |
| 100 |12662 |10395 |
| 1000 |128852 |102917 |

**Azure-ról Azure-ra (azonos adatközpont)** :

| Műveletek | Nincs tranzakció (MS) | Tranzakció (MS) |
| --- | --- | --- |
| 1 |21 |26 |
| 10 |220 |56 |
| 100 |2145 |341 |
| 1000 |21479 |2756 |

> [!NOTE]
> Az eredmények nem teljesítménymutatók. Lásd a [jelen cikk időzítési eredményekkel kapcsolatos megjegyzéseit](#note-about-timing-results-in-this-article).

A korábbi tesztek eredményei alapján a tranzakciók egyetlen műveletének becsomagolása ténylegesen csökkenti a teljesítményt. Az egyetlen tranzakción belüli műveletek számának növelésével azonban a teljesítmény növelése nagyobb lesz. A teljesítménybeli különbség akkor is észrevehető, ha az Microsoft Azure adatközponton belül minden művelet bekövetkezik. A SQL Database az Microsoft Azure adatközponton kívülről való használatának nagyobb késése a tranzakciók használatának teljesítménybeli nyereségét is felhasználja.

Bár a tranzakciók használata növelheti a teljesítményt, továbbra is megfigyelheti a [tranzakciók és a kapcsolatok ajánlott eljárásait](https://msdn.microsoft.com/library/ms187484.aspx). Tartsa a tranzakciót a lehető legrövidebb időn belül, és zárja be az adatbázis-kapcsolatokat a munka befejeződése után. Az előző példában szereplő using utasítás biztosítja, hogy a rendszer lezárja a kapcsolódást, amikor a következő kódrészlet befejeződik.

Az előző példa azt mutatja be, hogy egy helyi tranzakciót is hozzáadhat a két sorral rendelkező ADO.NET-kódokhoz. A tranzakciók gyors módszert kínálnak a szekvenciális beszúrási, frissítési és törlési műveletet végző kód teljesítményének javítására. A leggyorsabb teljesítmény érdekében azonban érdemes lehet a kódot tovább módosítani, hogy kihasználhassa az ügyféloldali kötegeket, például a táblázat értékű paramétereket.

További információ a ADO.NET-beli tranzakciókról: [helyi tranzakciók a ADO.net-ben](https://docs.microsoft.com/dotnet/framework/data/adonet/local-transactions).

### <a name="table-valued-parameters"></a>Tábla értékű paraméterek

A tábla értékű paraméterek a felhasználó által definiált táblázat típusú paramétereket támogatják a Transact-SQL-utasításokban, tárolt eljárásokban és függvényekben. Ez az ügyféloldali batch-eljárás lehetővé teszi több sornyi adat küldését a tábla értékű paraméteren belül. Ha táblázat értékű paramétereket szeretne használni, először adjon meg egy táblát. A következő Transact-SQL-utasítás egy **MyTableType**nevű táblát hoz létre.

```sql
    CREATE TYPE MyTableType AS TABLE 
    ( mytext TEXT,
      num INT );
```

A Code-ban létrehoz egy **DataTable** -t a tábla típusának pontos neveivel és típusaival. Adja át ezt a **DataTable** paramétert egy szöveges lekérdezésben vagy a tárolt eljárási hívásban. A következő példa ezt a technikát mutatja be:

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    connection.Open();

    DataTable table = new DataTable();
    // Add columns and rows. The following is a simple example.
    table.Columns.Add("mytext", typeof(string));
    table.Columns.Add("num", typeof(int));
    for (var i = 0; i < 10; i++)
    {
        table.Rows.Add(DateTime.Now.ToString(), DateTime.Now.Millisecond);
    }

    SqlCommand cmd = new SqlCommand(
        "INSERT INTO MyTable(mytext, num) SELECT mytext, num FROM @TestTvp",
        connection);

    cmd.Parameters.Add(
        new SqlParameter()
        {
            ParameterName = "@TestTvp",
            SqlDbType = SqlDbType.Structured,
            TypeName = "MyTableType",
            Value = table,
        });

    cmd.ExecuteNonQuery();
}
```

Az előző példában a **SqlCommand** objektum sorokat szúr be egy tábla értékű paraméterből,  **\@a TestTvp**. A korábban létrehozott **DataTable** objektum hozzá van rendelve ehhez a paraméterhez a **SqlCommand. Parameters. Add** metódussal. A beszúrások az egyik hívásban történő kötegelt feldolgozása jelentősen növeli a teljesítményt a szekvenciális lapkákon.

Az előző példa további javítása érdekében használjon egy tárolt eljárást szöveges parancs helyett. A következő Transact-SQL parancs egy tárolt eljárást hoz létre, amely a **SimpleTestTableType** tábla értékű paramétert veszi fel.

```sql
CREATE PROCEDURE [dbo].[sp_InsertRows] 
@TestTvp as MyTableType READONLY
AS
BEGIN
INSERT INTO MyTable(mytext, num) 
SELECT mytext, num FROM @TestTvp
END
GO
```

Ezután módosítsa a **SqlCommand** objektum deklarációját az előző kód példájában a következőre.

```csharp
SqlCommand cmd = new SqlCommand("sp_InsertRows", connection);
cmd.CommandType = CommandType.StoredProcedure;
```

A legtöbb esetben a tábla értékű paraméterek egyenértékű vagy jobb teljesítményt biztosítanak, mint a többi batch-módszernél. A tábla értékű paraméterek gyakran előnyben részesítettek, mivel a többi lehetőségnél rugalmasabbak. Például más technikák, például az SQL tömeges másolás csak új sorok beszúrását teszik lehetővé. A tábla értékű paraméterekkel azonban a tárolt eljárásban a logika segítségével meghatározhatja, hogy mely sorok frissülnek, és melyek a lapkák. A tábla típusa úgy is módosítható, hogy "művelet" oszlopot tartalmazzon, amely jelzi, hogy a megadott sort be kell-e szúrni, frissíteni vagy törölni kell.

A következő táblázat a tábla értékű paraméterek ezredmásodpercben történő használatára ad hoc tesztelési eredményeket mutat be.

| Műveletek | Helyszíni – Azure (MS) | Azure-beli azonos adatközpont (MS) |
| --- | --- | --- |
| 1 |124 |32 |
| 10 |131 |25 |
| 100 |338 |51 |
| 1000 |2615 |382 |
| 10000 |23830 |3586 |

> [!NOTE]
> Az eredmények nem teljesítménymutatók. Lásd a [jelen cikk időzítési eredményekkel kapcsolatos megjegyzéseit](#note-about-timing-results-in-this-article).
> 
> 

A kötegből származó teljesítménybeli nyereség azonnal nyilvánvalóvá válik. Az előző szekvenciális tesztben az 1000-es művelet az adatközponton kívül 129 másodperc volt, az adatközponton pedig 21 másodperc volt. A táblázat értékű paraméterekkel azonban a 1000-es műveletek csak 2,6 másodpercig tartanak az adatközponton kívül az adatközpontban, az 0,4 másodpercen belül.

A tábla értékű paraméterekkel kapcsolatos további információkért lásd: [táblázat értékű paraméterek](https://msdn.microsoft.com/library/bb510489.aspx).

### <a name="sql-bulk-copy"></a>SQL tömeges másolás

Az SQL tömeges másolás egy másik módszer, amellyel nagy mennyiségű adattal lehet beszúrni a céladatbázisbe. A .NET-alkalmazások használhatják a **SqlBulkCopy** osztályt a tömeges beszúrási műveletek végrehajtásához. A **SqlBulkCopy** hasonló a parancssori eszközhöz, a **BCP. exe fájlhoz**vagy a Transact-SQL-utasításhoz, **bulk INSERT**. A következő mintakód azt mutatja be, hogyan lehet a forrás **DataTable**, Table, és a Destination táblába másolni a sorokat a SQL Server sajáttábla.

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    connection.Open();

    using (SqlBulkCopy bulkCopy = new SqlBulkCopy(connection))
    {
        bulkCopy.DestinationTableName = "MyTable";
        bulkCopy.ColumnMappings.Add("mytext", "mytext");
        bulkCopy.ColumnMappings.Add("num", "num");
        bulkCopy.WriteToServer(table);
    }
}
```

Vannak olyan esetek, amikor a tömeges másolás előnyben részesített a tábla értékű paramétereknél. Tekintse meg a tábla értékű paraméterek összehasonlító táblázatát, illetve a BULK INSERT műveleteket a [táblázat értékű paraméterek](https://msdn.microsoft.com/library/bb510489.aspx)című cikkben.

A következő ad hoc tesztelési eredmények a **SqlBulkCopy** -ben történő kötegelt feldolgozás teljesítményét mutatják be ezredmásodpercben.

| Műveletek | Helyszíni – Azure (MS) | Azure-beli azonos adatközpont (MS) |
| --- | --- | --- |
| 1 |433 |57 |
| 10 |441 |32 |
| 100 |636 |53 |
| 1000 |2535 |341 |
| 10000 |21605 |2737 |

> [!NOTE]
> Az eredmények nem teljesítménymutatók. Lásd a [jelen cikk időzítési eredményekkel kapcsolatos megjegyzéseit](#note-about-timing-results-in-this-article).
> 
> 

Kisebb méretű kötegekben a tábla értékkel rendelkező paraméterek használata a **SqlBulkCopy** osztályt is elvégezte. A **SqlBulkCopy** azonban 12-31%-kal gyorsabb, mint a táblázat értékű paraméterek a 1 000 és 10 000 sorokhoz. A tábla értékű paraméterekhez hasonlóan a **SqlBulkCopy** is jó választás a kötegelt beszúrásokhoz, különösen a nem kötegelt műveletek teljesítményéhez képest.

A ADO.NET tömeges másolásával kapcsolatos további információkért lásd: [tömeges másolási műveletek a SQL Serverban](https://msdn.microsoft.com/library/7ek5da1a.aspx).

### <a name="multiple-row-parameterized-insert-statements"></a>Többsoros paraméteres INSERT utasítások

Kisméretű kötegek esetében egy másik alternatíva egy nagy paraméteres BESZÚRÁSi utasítás létrehozása, amely több sort szúr be. A következő kódrészlet szemlélteti ezt a technikát.

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    connection.Open();

    string insertCommand = "INSERT INTO [MyTable] ( mytext, num ) " +
        "VALUES (@p1, @p2), (@p3, @p4), (@p5, @p6), (@p7, @p8), (@p9, @p10)";

    SqlCommand cmd = new SqlCommand(insertCommand, connection);

    for (int i = 1; i <= 10; i += 2)
    {
        cmd.Parameters.Add(new SqlParameter("@p" + i.ToString(), "test"));
        cmd.Parameters.Add(new SqlParameter("@p" + (i+1).ToString(), i));
    }

    cmd.ExecuteNonQuery();
}
```

Ez a példa az alapvető koncepció megjelenítését szemlélteti. Egy reálisabb forgatókönyv esetén a szükséges entitások hurkot kell létrehozni a lekérdezési karakterlánc és a parancs paramétereinek egyidejű létrehozásához. Összesen 2100 lekérdezési paraméterre van korlátozva, így ez korlátozza az ily módon feldolgozható sorok teljes számát.

A következő ad hoc teszt eredményei az ilyen típusú INSERT utasítás teljesítményét mutatják be ezredmásodpercben.

| Műveletek | Tábla értékű paraméterek (MS) | Egyszeres utasítás beszúrása (MS) |
| --- | --- | --- |
| 1 |32 |20 |
| 10 |30 |25 |
| 100 |33 |51 |

> [!NOTE]
> Az eredmények nem teljesítménymutatók. Lásd a [jelen cikk időzítési eredményekkel kapcsolatos megjegyzéseit](#note-about-timing-results-in-this-article).
> 
> 

Ez a megközelítés valamivel gyorsabb lehet a 100-nál kisebb kötegek esetében. Bár a fejlesztés kicsi, ez a technika egy másik lehetőség, amely jól működik az adott alkalmazási forgatókönyvben.

### <a name="dataadapter"></a>DataAdapter

A **DataAdapter** osztály lehetővé teszi az **adatkészlet** -objektumok módosítását, majd a módosítások beszúrási, frissítési és törlési műveletként való elküldését. Ha a **DataAdapter** ilyen módon használja, fontos megjegyezni, hogy minden egyes művelethez külön hívásokat kell végezni. A teljesítmény javításához használja a **UpdateBatchSize** tulajdonságot azon műveletek számához, amelyeket egyszerre kell kötegbe állítani. További információ: batch- [műveletek végrehajtása a DataAdapters használatával](https://msdn.microsoft.com/library/aadf8fk2.aspx).

### <a name="entity-framework"></a>Entitás-keretrendszer

A Entity Framework jelenleg nem támogatja a kötegelt feldolgozást. A Közösség különböző fejlesztői megpróbálták megmutatni a megkerülő megoldásokat, például felülbírálják a **SaveChanges** metódust. A megoldások azonban általában összetettek és testreszabhatók az alkalmazás és az adatmodell számára. A Entity Framework Codeplex projektnek jelenleg van vitafóruma a szolgáltatásra vonatkozó kérelemben. A vitafórum megtekintéséhez tekintse meg a [design Meeting megjegyzéseit – 2012. augusztus 2](https://entityframework.codeplex.com/wikipage?title=Design%20Meeting%20Notes%20-%20August%202%2c%202012).

### <a name="xml"></a>XML

A teljesség kedvéért úgy érezzük, hogy fontos, hogy az XML-ről kötegelt stratégiaként beszéljen. Az XML használatának azonban nincs előnye más módszerekkel és számos hátránysal szemben. A módszer hasonló a tábla értékű paraméterekhez, de egy XML-fájlt vagy karakterláncot ad át egy tárolt eljárásnak a felhasználó által definiált tábla helyett. A tárolt eljárás elemzi a tárolt eljárás utasításait.

Ennek a megközelítésnek több hátránya is van:

* Az XML használata nehézkes és hibás lehet.
* Az XML-adatbázis elemzése CPU-igényes lehet.
* A legtöbb esetben ez a metódus lassabb a tábla értékű paramétereknél.

Ezen okok miatt a Batch-lekérdezésekhez használt XML használata nem ajánlott.

## <a name="batching-considerations"></a>A kötegelt feldolgozás szempontjai

A következő szakaszokban további útmutatást talál a Batch-alkalmazások SQL Database alkalmazásokban való használatához.

### <a name="tradeoffs"></a>Kompromisszumokat

Az architektúrától függően a kötegelt feldolgozás a teljesítmény és a rugalmasság közötti kompromisszumot is magában foglalhatja. Vegyük például azt a forgatókönyvet, amelyben a szerepkör váratlanul leáll. Ha elveszít egy adatsort, a hatás kisebb, mint a nem beküldött sorok nagy kötegének elvesztése. Nagyobb a kockázata, ha a sorok pufferelése előtt elküldi őket az adatbázisba egy adott időablakban.

A kompromisszum miatt értékelje ki a kötegelt műveletek típusát. A Batch-t agresszíven (nagyobb kötegekben és hosszabb időkeretekben), a kevésbé kritikus adatmennyiséggel.

### <a name="batch-size"></a>Köteg mérete

A tesztek során általában nem volt előnye a nagyméretű kötegek kisebb adattömbökbe való betörésének. Valójában ez az albontás gyakran lassabb teljesítményt eredményezett, mint egyetlen nagy köteg elküldése. Vegyünk például egy olyan forgatókönyvet, amelyben 1000 sort kíván beszúrni. A következő táblázat azt mutatja be, hogy mennyi ideig tart a táblázat értékű paraméterek használata az 1000-es sorok beszúrásához kisebb kötegekre osztva.

| Köteg mérete | Ismétlések | Tábla értékű paraméterek (MS) |
| --- | --- | --- |
| 1000 |1 |347 |
| 500 |2 |355 |
| 100 |10 |465 |
| 50 |20 |630 |

> [!NOTE]
> Az eredmények nem teljesítménymutatók. Lásd a [jelen cikk időzítési eredményekkel kapcsolatos megjegyzéseit](#note-about-timing-results-in-this-article).
> 
> 

Láthatja, hogy az 1000-es sorok legjobb teljesítménye egyszerre küldi el őket. Más tesztekben (itt nem látható) az 10000-es sorok kötegének kibontása a 5000-es két kötegbe. A tesztek táblázatos sémája azonban viszonylag egyszerű, ezért az adott adatokra és a Batch-méretekre vonatkozó teszteket kell végrehajtania az eredmények ellenőrzéséhez.

Egy másik megfontolandó tényező, hogy ha az összes köteg túl nagy lesz, SQL Database lehet, hogy a köteget véglegesíti és elutasítja. A legjobb eredmény érdekében tesztelje az adott forgatókönyvet, és állapítsa meg, hogy van-e ideális batch-méret. A Batch-méret konfigurálható futásidőben, hogy a gyors beállításokat a teljesítmény vagy a hibák alapján engedélyezze.

Végezetül egyensúlyt kell kiosztani a köteg méretével a kötegelt feldolgozáshoz kapcsolódó kockázatokkal. Ha átmeneti hibák történnek, vagy ha a szerepkör meghibásodik, vegye figyelembe a művelet újrapróbálkozásának vagy a kötegben lévő adat elvesztésének következményeit.

### <a name="parallel-processing"></a>Párhuzamos feldolgozás

Mi a teendő, ha a Batch méretének csökkentését választotta, de több szálat használt a munka végrehajtásához? A tesztek azt mutatták, hogy több kisebb többszálú köteg általában rosszabb, mint egyetlen nagyobb köteg. A következő teszt megpróbál 1000 sort beszúrni egy vagy több párhuzamos kötegbe. Ez a teszt azt mutatja be, hogy az egyidejű kötegek milyen mértékben csökkentik a teljesítményt.

| Köteg mérete [iterációk] | Két szál (MS) | Négy szál (MS) | Hat szál (MS) |
| --- | --- | --- | --- |
| 1000 [1] |277 |315 |266 |
| 500 [2] |548 |278 |256 |
| 250 [4] |405 |329 |265 |
| 100 [10] |488 |439 |391 |

> [!NOTE]
> Az eredmények nem teljesítménymutatók. Lásd a [jelen cikk időzítési eredményekkel kapcsolatos megjegyzéseit](#note-about-timing-results-in-this-article).
> 
> 

A teljesítmény romlásának számos lehetséges oka lehet a párhuzamosságok miatt:

* Több egyidejű hálózati hívás van egy helyett.
* Az egyetlen táblán végrehajtott műveletek több műveletet is okozhatnak.
* A többszálas működéssel kapcsolatos általános költségek vannak.
* Több kapcsolat megnyitásának költsége meghaladja a párhuzamos feldolgozás előnyeit.

Ha különböző táblákat vagy adatbázisokat céloz meg, lehetséges, hogy a stratégia bizonyos teljesítménybeli nyereséggel rendelkezik. Az adatbázisok horizontális felskálázása és a szövetségek forgatókönyve lenne erre a megközelítésre. A horizontális skálázás több adatbázist használ, és az egyes adatbázisokhoz különböző adatútvonalakat irányít. Ha az egyes kis kötegek egy másik adatbázisba kerülnek, akkor a párhuzamos műveletek végrehajtása hatékonyabb lehet. A teljesítménybeli nyereség azonban nem elég jelentős ahhoz, hogy a megoldásban az adatbázisok horizontális felskálázására vonatkozó döntés alapját használja.

Bizonyos kialakításokban a kisebb kötegek párhuzamos végrehajtása a betöltés alatt álló rendszerekben a kérések jobb átviteli sebességét eredményezheti. Ebben az esetben annak ellenére, hogy az egyetlen nagyobb köteg feldolgozását is meggyorsítja, a több köteg párhuzamos feldolgozása hatékonyabb lehet.

Ha párhuzamos végrehajtást használ, érdemes megfontolnia a munkaszálak maximális számának szabályozását. A kisebb szám nem okozhatja a versenyt és a gyorsabb végrehajtási időt. Vegye figyelembe a további terhelést is, amelyet ez a hely a céladatbázis számára a kapcsolatokban és a tranzakciókban is tárol.

### <a name="related-performance-factors"></a>Kapcsolódó teljesítménnyel kapcsolatos tényezők

Az adatbázis teljesítményére jellemző általános útmutató a kötegelt feldolgozást is befolyásolja. A beszúrási teljesítmény például kisebb a nagyméretű elsődleges kulccsal vagy sok nem fürtözött indextel rendelkező tábláknál.

Ha a tábla értékű paraméterek tárolt eljárást használnak, az eljárás elején használhatja a **szám megadására vonatkozó** parancsot is. Ez az utasítás letiltja az eljárásban érintett sorok számának visszaadását. A tesztek során azonban a **set** szám használata nincs hatással vagy csökkent a teljesítmény. A teszt tárolt eljárás egyszerű volt egyetlen **Insert** paranccsal a tábla értékű paraméterből. Lehetséges, hogy összetettebb tárolt eljárások is kihasználhatják ezt az utasítást. Azonban ne feltegyük, hogy a tárolt eljáráshoz tartozó **set UNcount** elem hozzáadásával automatikusan javul a teljesítmény. A hatás megismeréséhez tesztelje a tárolt eljárást a **set uncount** utasítással és anélkül.

## <a name="batching-scenarios"></a>Batch-forgatókönyvek

A következő szakaszok azt ismertetik, hogyan használhatók a tábla értékű paraméterek három alkalmazási helyzetben. Az első forgatókönyv bemutatja, hogyan működhet együtt a pufferelés és a kötegelt feldolgozás. A második forgatókönyv javítja a teljesítményt a Master-detail műveleteknek egyetlen tárolt eljárási hívásban történő végrehajtásával. Az utolsó forgatókönyv azt mutatja be, hogyan használhatók tábla értékű paraméterek egy "UPSERT" műveletben.

### <a name="buffering"></a>Pufferelés

Bár vannak olyan forgatókönyvek, amelyek egyértelműen jelöltek a kötegelt feldolgozáshoz, számos forgatókönyv van, amely kihasználhatja a kötegelt feldolgozást a késleltetett feldolgozással. A késleltetett feldolgozás azonban nagyobb kockázatot is jelent, ha váratlan meghibásodás esetén az adat elvész. Fontos megérteni ezt a kockázatot, és figyelembe venni a következményeket.

Vegyünk például egy webalkalmazást, amely nyomon követi az egyes felhasználók navigációs előzményeit. Az alkalmazás minden egyes lapon meghívást készíthet a felhasználó oldal nézetének rögzítéséhez. A nagyobb teljesítmény és méretezhetőség azonban a felhasználók navigációs tevékenységeinek pufferelésével, majd az adatok kötegekben történő elküldésével is megvalósítható. Az adatbázis frissítését elvégezheti az eltelt idő és/vagy a pufferméret méretének elindításával. Egy szabály például megadhatja, hogy a köteg 20 másodperc után legyen feldolgozva, vagy ha a puffer eléri a 1000 elemet.

A következő kód a [reaktív bővítmények-Rx](https://msdn.microsoft.com/data/gg577609) használatával dolgozza fel a figyelési osztály által kiváltott pufferelt eseményeket. A puffer kitöltése vagy időtúllépés elérésekor a rendszer a felhasználói adatköteget egy tábla értékű paraméterrel küldi el az adatbázisba.

A következő NavHistoryData osztály a felhasználói navigáció részleteit modellezi. Alapszintű információkat tartalmaz, például a felhasználói azonosítót, az URL-címet, valamint a hozzáférési időt.

```csharp
public class NavHistoryData
{
    public NavHistoryData(int userId, string url, DateTime accessTime)
    { UserId = userId; URL = url; AccessTime = accessTime; }
    public int UserId { get; set; }
    public string URL { get; set; }
    public DateTime AccessTime { get; set; }
}
```

A NavHistoryDataMonitor osztály feladata a felhasználó navigációs adatának adatbázisba való pufferelése. Egy RecordUserNavigationEntry metódust tartalmaz, amely egy **OnAdded** -esemény emelésével válaszol. A következő kód az RX-t használó konstruktor logikát mutatja be az esemény alapján megfigyelhető gyűjtemény létrehozásához. Ezután Előfizet erre a megfigyelhető gyűjteményre a puffer metódussal. A túlterhelés azt adja meg, hogy a puffert 20 másodpercenként vagy 1000-es bejegyzésben kell elküldeni.

```csharp
public NavHistoryDataMonitor()
{
    var observableData =
        Observable.FromEventPattern<NavHistoryDataEventArgs>(this, "OnAdded");

    observableData.Buffer(TimeSpan.FromSeconds(20), 1000).Subscribe(Handler);
}
```

A kezelő az összes pufferelt elemet egy táblázat értékű típusra konvertálja, majd átadja ezt a típust egy tárolt eljárásnak, amely feldolgozza a köteget. A következő kód a NavHistoryDataEventArgs és a NavHistoryDataMonitor osztály teljes definícióját mutatja.

```csharp
public class NavHistoryDataEventArgs : System.EventArgs
{
    public NavHistoryDataEventArgs(NavHistoryData data) { Data = data; }
    public NavHistoryData Data { get; set; }
}

public class NavHistoryDataMonitor
{
    public event EventHandler<NavHistoryDataEventArgs> OnAdded;

    public NavHistoryDataMonitor()
    {
        var observableData =
            Observable.FromEventPattern<NavHistoryDataEventArgs>(this, "OnAdded");

        observableData.Buffer(TimeSpan.FromSeconds(20), 1000).Subscribe(Handler);
    }
```

A kezelő az összes pufferelt elemet egy táblázat értékű típusra konvertálja, majd átadja ezt a típust egy tárolt eljárásnak, amely feldolgozza a köteget. A következő kód a NavHistoryDataEventArgs és a NavHistoryDataMonitor osztály teljes definícióját mutatja.

```csharp
    public class NavHistoryDataEventArgs : System.EventArgs
    {
        if (OnAdded != null)
            OnAdded(this, new NavHistoryDataEventArgs(data));
    }

    protected void Handler(IList<EventPattern<NavHistoryDataEventArgs>> items)
    {
        DataTable navHistoryBatch = new DataTable("NavigationHistoryBatch");
        navHistoryBatch.Columns.Add("UserId", typeof(int));
        navHistoryBatch.Columns.Add("URL", typeof(string));
        navHistoryBatch.Columns.Add("AccessTime", typeof(DateTime));
        foreach (EventPattern<NavHistoryDataEventArgs> item in items)
        {
            NavHistoryData data = item.EventArgs.Data;
            navHistoryBatch.Rows.Add(data.UserId, data.URL, data.AccessTime);
        }

        using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
        {
            connection.Open();

            SqlCommand cmd = new SqlCommand("sp_RecordUserNavigation", connection);
            cmd.CommandType = CommandType.StoredProcedure;

            cmd.Parameters.Add(
                new SqlParameter()
                {
                    ParameterName = "@NavHistoryBatch",
                    SqlDbType = SqlDbType.Structured,
                    TypeName = "NavigationHistoryTableType",
                    Value = navHistoryBatch,
                });

            cmd.ExecuteNonQuery();
        }
    }
}
```

A pufferelési osztály használatához az alkalmazás létrehoz egy statikus NavHistoryDataMonitor objektumot. Minden alkalommal, amikor egy felhasználó hozzáfér egy oldalhoz, az alkalmazás meghívja a NavHistoryDataMonitor. RecordUserNavigationEntry metódust. A pufferelési logika arra irányul, hogy a bejegyzéseket a kötegekben lévő adatbázisba küldje el.

### <a name="master-detail"></a>Fő részletek

A tábla értékű paraméterek egyszerű BESZÚRÁSi forgatókönyvekhez hasznosak. Azonban nagyobb kihívást jelenthet a kötegelt beszúrások, amelyek egynél több táblát érintenek. A "Master/detail" forgatókönyv jó példa. A fő tábla azonosítja az elsődleges entitást. Egy vagy több részletes táblázat az entitással kapcsolatos további adatokat tárolja. Ebben a forgatókönyvben a idegenkulcs-kapcsolatok kikényszerítik a részletek egy egyedi fő entitáshoz való viszonyát. Vegye fontolóra a PurchaseOrder-tábla és a hozzá tartozó OrderDetail-táblázat egyszerűsített verzióját. A következő Transact-SQL létrehozza a PurchaseOrder táblát négy oszloppal: Rendeléskód, rendelve, Vevőkód és status.

```sql
CREATE TABLE [dbo].[PurchaseOrder](
[OrderID] [int] IDENTITY(1,1) NOT NULL,
[OrderDate] [datetime] NOT NULL,
[CustomerID] [int] NOT NULL,
[Status] [nvarchar](50) NOT NULL,
CONSTRAINT [PrimaryKey_PurchaseOrder] 
PRIMARY KEY CLUSTERED ( [OrderID] ASC ))
```

Minden megrendelés egy vagy több terméket tartalmaz. Ezek az adatok a PurchaseOrderDetail táblában vannak rögzítve. A következő Transact-SQL létrehozza a PurchaseOrderDetail táblát öt oszloppal: Rendeléskód, OrderDetailID, termékkód, egységár és OrderQty.

```sql
CREATE TABLE [dbo].[PurchaseOrderDetail](
[OrderID] [int] NOT NULL,
[OrderDetailID] [int] IDENTITY(1,1) NOT NULL,
[ProductID] [int] NOT NULL,
[UnitPrice] [money] NULL,
[OrderQty] [smallint] NULL,
CONSTRAINT [PrimaryKey_PurchaseOrderDetail] PRIMARY KEY CLUSTERED 
( [OrderID] ASC, [OrderDetailID] ASC ))
```

A PurchaseOrderDetail táblában lévő Rendeléskód oszlopnak egy rendelésre kell hivatkoznia a PurchaseOrder táblából. A külső kulcsok következő definíciója kényszeríti ezt a korlátozást.

```sql
ALTER TABLE [dbo].[PurchaseOrderDetail]  WITH CHECK ADD 
CONSTRAINT [FK_OrderID_PurchaseOrder] FOREIGN KEY([OrderID])
REFERENCES [dbo].[PurchaseOrder] ([OrderID])
```

A tábla értékű paraméterek használatához egy felhasználó által definiált tábla típusúnak kell lennie minden céltábla esetében.

```sql
CREATE TYPE PurchaseOrderTableType AS TABLE 
( OrderID INT,
    OrderDate DATETIME,
    CustomerID INT,
    Status NVARCHAR(50) );
GO

CREATE TYPE PurchaseOrderDetailTableType AS TABLE 
( OrderID INT,
    ProductID INT,
    UnitPrice MONEY,
    OrderQty SMALLINT );
GO
```

Ezután Definiáljon egy tárolt eljárást, amely ezen típusok táblázatait fogadja el. Ez az eljárás lehetővé teszi az alkalmazás számára, hogy egy hívásban helyileg batch-készletet rendeljen a rendelésekhez és a megrendelési adatokhoz. A következő Transact-SQL biztosítja a teljes tárolt eljárás deklarációját ehhez a beszerzési rendeléshez.

```sql
CREATE PROCEDURE sp_InsertOrdersBatch (
@orders as PurchaseOrderTableType READONLY,
@details as PurchaseOrderDetailTableType READONLY )
AS
SET NOCOUNT ON;

-- Table that connects the order identifiers in the @orders
-- table with the actual order identifiers in the PurchaseOrder table
DECLARE @IdentityLink AS TABLE ( 
SubmittedKey int, 
ActualKey int, 
RowNumber int identity(1,1)
);

-- Add new orders to the PurchaseOrder table, storing the actual
-- order identifiers in the @IdentityLink table   
INSERT INTO PurchaseOrder ([OrderDate], [CustomerID], [Status])
OUTPUT inserted.OrderID INTO @IdentityLink (ActualKey)
SELECT [OrderDate], [CustomerID], [Status] FROM @orders ORDER BY OrderID;

-- Match the passed-in order identifiers with the actual identifiers
-- and complete the @IdentityLink table for use with inserting the details
WITH OrderedRows As (
SELECT OrderID, ROW_NUMBER () OVER (ORDER BY OrderID) As RowNumber 
FROM @orders
)
UPDATE @IdentityLink SET SubmittedKey = M.OrderID
FROM @IdentityLink L JOIN OrderedRows M ON L.RowNumber = M.RowNumber;

-- Insert the order details into the PurchaseOrderDetail table, 
-- using the actual order identifiers of the master table, PurchaseOrder
INSERT INTO PurchaseOrderDetail (
[OrderID],
[ProductID],
[UnitPrice],
[OrderQty] )
SELECT L.ActualKey, D.ProductID, D.UnitPrice, D.OrderQty
FROM @details D
JOIN @IdentityLink L ON L.SubmittedKey = D.OrderID;
GO
```

Ebben a példában a helyileg definiált @IdentityLink táblázat az újonnan beszúrt sorok tényleges Rendeléskód értékeit tárolja. Ezek a sorrend-azonosítók eltérnek a és @orders @details a tábla értékű paraméterekben lévő ideiglenes Rendeléskód értékektől. Emiatt a @IdentityLink tábla ezután összekapcsolja a Rendeléskód értékeket @orders a paraméterből a PurchaseOrder tábla új sorainak valódi Rendeléskód értékeivel. A lépés elvégzése @IdentityLink után a tábla megkönnyítheti a megrendelési adatok beszúrását a Foreign Key korlátozásnak eleget tevő tényleges Rendeléskód értékkel.

Ez a tárolt eljárás kód vagy más Transact-SQL-hívásokból is használható. Tekintse meg a jelen dokumentum táblázat értékű paraméterek szakaszát egy példaként. A következő Transact-SQL azt mutatja be, hogyan hívhatja meg a sp_InsertOrdersBatch.

```sql
declare @orders as PurchaseOrderTableType
declare @details as PurchaseOrderDetailTableType

INSERT @orders 
([OrderID], [OrderDate], [CustomerID], [Status])
VALUES(1, '1/1/2013', 1125, 'Complete'),
(2, '1/13/2013', 348, 'Processing'),
(3, '1/12/2013', 2504, 'Shipped')

INSERT @details
([OrderID], [ProductID], [UnitPrice], [OrderQty])
VALUES(1, 10, $11.50, 1),
(1, 12, $1.58, 1),
(2, 23, $2.57, 2),
(3, 4, $10.00, 1)

exec sp_InsertOrdersBatch @orders, @details
```

Ez a megoldás lehetővé teszi, hogy az egyes kötegek olyan Rendeléskód értékek halmazát használják, amelyek 1-től kezdődnek. Ezek az ideiglenes Rendeléskód értékek írják le a kötegben lévő kapcsolatokat, a tényleges Rendeléskód értékeket azonban a beszúrási művelet időpontjában határozzák meg. Az előző példában szereplő azonos utasítások többször is futtathatók, és egyedi megrendeléseket hozhatnak az adatbázisban. Emiatt érdemes lehet olyan programkódot vagy adatbázis-logikát hozzáadni, amely meggátolja az ismétlődő megrendeléseket a Batch-eljárás használatakor.

Ez a példa azt mutatja be, hogy még összetettebbek az adatbázis-műveletek, például a Master-detail műveletek, tábla értékű paraméterek használatával.

### <a name="upsert"></a>UPSERT

Egy másik batch-forgatókönyv magában foglalja a meglévő sorok párhuzamos frissítését és az új sorok beszúrását. Ezt a műveletet más néven "UPSERT" (Update + INSERT) műveletnek nevezzük. Ahelyett, hogy külön hívásokat kellene beszúrni és frissíteni, a MERGE utasítás a legmegfelelőbb ehhez a feladathoz. A MERGE utasítás mindkét beszúrási és frissítési műveletet egyetlen hívásban is végrehajthatja.

A táblázat értékű paraméterek a MERGE utasítással használhatók a frissítések és a beszúrások végrehajtásához. Vegyünk például egy egyszerűsített Employee táblát, amely a következő oszlopokat tartalmazza: Alkalmazottkód, FirstName, LastName, SocialSecurityNumber:

```sql
CREATE TABLE [dbo].[Employee](
[EmployeeID] [int] IDENTITY(1,1) NOT NULL,
[FirstName] [nvarchar](50) NOT NULL,
[LastName] [nvarchar](50) NOT NULL,
[SocialSecurityNumber] [nvarchar](50) NOT NULL,
CONSTRAINT [PrimaryKey_Employee] PRIMARY KEY CLUSTERED 
([EmployeeID] ASC ))
```

Ebben a példában azt a tényt használhatja, hogy a SocialSecurityNumber egyedi, így több alkalmazott EGYESÍTÉSe is elvégezhető. Először hozza létre a felhasználó által definiált tábla típusát:

```sql
CREATE TYPE EmployeeTableType AS TABLE 
( Employee_ID INT,
    FirstName NVARCHAR(50),
    LastName NVARCHAR(50),
    SocialSecurityNumber NVARCHAR(50) );
GO
```

Ezután hozzon létre egy tárolt eljárást, vagy írjon be egy olyan kódot, amely a MERGE utasítás használatával hajtja végre a frissítést és a beszúrást. A következő példa a Merge utasítást használja egy tábla értékű paraméter @employees(EmployeeTableType típusú). A @employees tábla tartalma itt nem jelenik meg.

```sql
MERGE Employee AS target
USING (SELECT [FirstName], [LastName], [SocialSecurityNumber] FROM @employees) 
AS source ([FirstName], [LastName], [SocialSecurityNumber])
ON (target.[SocialSecurityNumber] = source.[SocialSecurityNumber])
WHEN MATCHED THEN 
UPDATE SET
target.FirstName = source.FirstName, 
target.LastName = source.LastName
WHEN NOT MATCHED THEN
    INSERT ([FirstName], [LastName], [SocialSecurityNumber])
    VALUES (source.[FirstName], source.[LastName], source.[SocialSecurityNumber]);
```

További információkért tekintse meg a MERGE utasítás dokumentációját és példáit. Bár ugyanez a feladat több lépésből álló tárolt eljárásban is elvégezhető különálló INSERT és UPDATE műveletekkel, a MERGE utasítás hatékonyabb. Az adatbázis kódja olyan Transact-SQL-hívásokat is létrehozhat, amelyek közvetlenül a MERGE utasítást használják anélkül, hogy két adatbázis-hívást kellene beszúrni és frissíteni.

## <a name="recommendation-summary"></a>Javaslat összefoglalása

Az alábbi lista összefoglalja a cikkben tárgyalt kötegelt javaslatokat:

* A pufferelés és a kötegelt feldolgozás használatával növelheti SQL Database alkalmazások teljesítményét és méretezhetőségét.
* A kötegelt, a pufferelési és a rugalmassági kompromisszumok megismerése. A szerepkör meghibásodása során az üzleti szempontból kritikus fontosságú adat feldolgozatlan kötegének elvesztésével járó kockázat meghaladhatja a kötegelt feldolgozás teljesítménybeli előnyeit.
* A késés csökkentése érdekében próbálja megtartani az összes hívást az adatbázisba egyetlen adatközponton belül.
* Ha egyetlen batching technikát választ, a tábla értékű paraméterek a legjobb teljesítményt és rugalmasságot nyújtanak.
* A leggyorsabb beszúrási teljesítmény érdekében kövesse az alábbi általános irányelveket, de tesztelje a forgatókönyvet:
  * < 100 sor esetében egyetlen paraméteres BESZÚRÁSi parancsot használjon.
  * < 1000 sor esetén használjon táblázat értékű paramétereket.
  * > = 1000 sor esetén használja a SqlBulkCopy.
* A frissítési és törlési műveleteknél olyan tábla értékű paramétereket használjon tárolt eljárási logikával, amelyek meghatározzák a megfelelő műveletet a Table paraméter minden sorában.
* A Batch méretével kapcsolatos irányelvek:
  * Használja az alkalmazások és az üzleti igényeknek leginkább megfelelő méretű kötegeket.
  * A nagy kötegek teljesítménybeli nyereségét az ideiglenes vagy katasztrofális hibák kockázatával egyenlítheti ki. Mi a következménye annak, hogy újrapróbálkozik a kötegben lévő adatvesztéssel? 
  * Tesztelje a legnagyobb méretű köteget annak ellenőrzéséhez, hogy SQL Database nem utasítja el.
  * Hozzon létre olyan konfigurációs beállításokat, amelyek vezérlik a kötegelt feldolgozást, például a köteg méretét vagy a pufferelés időpontját. Ezek a beállítások rugalmasságot biztosítanak. A működés közbeni viselkedést a felhőalapú szolgáltatás újbóli üzembe helyezése nélkül módosíthatja az éles környezetben.
* Kerülje az egyetlen táblán működő kötegek párhuzamos végrehajtását egyetlen adatbázisban. Ha úgy dönt, hogy egyetlen köteget oszt szét több munkavégző szál között, futtasson teszteket a szálak ideális számának megállapításához. Egy meghatározatlan küszöbérték után több szál csökkenti a teljesítményt, és nem növeli.
* További forgatókönyvek esetén érdemes lehet a méret és az idő pufferelését figyelembe venni a Batch-feladatok végrehajtásához.

## <a name="next-steps"></a>További lépések

Ez a cikk arra összpontosít, hogy miként javítható az alkalmazások teljesítménye és méretezhetősége az adatbázis-létrehozással kapcsolatos tervezési és kódolási technikákban. Ez azonban csak egy tényező a teljes stratégiában. A teljesítmény és a méretezhetőség javítása érdekében tekintse meg a következő témakört: [Azure SQL Database teljesítményre vonatkozó útmutató az önálló adatbázisokhoz](sql-database-performance-guidance.md) , valamint a [rugalmas készlet árának és teljesítményének szempontjait](sql-database-elastic-pool-guidance.md).

