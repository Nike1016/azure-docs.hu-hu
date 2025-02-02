---
title: Azure SQL Database memóriában tárolt minta | Microsoft Docs
description: Próbálja ki Azure SQL Database memóriában lévő technológiákat a OLTP és a oszlopcentrikus mintával.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
ms.date: 12/18/2018
ms.openlocfilehash: 5f6f4ce4fc77533a4d893472298ef3a20f153136
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568002"
---
# <a name="in-memory-sample"></a>Memóriában tárolt minta

A Azure SQL Database memóriában lévő technológiái lehetővé teszik az alkalmazás teljesítményének növelését, és csökkentheti az adatbázis költségeit. A Azure SQL Database memóriában lévő technológiák használatával a teljesítménnyel kapcsolatos fejlesztéseket a különböző számítási feladatokkal érheti el.

Ebben a cikkben két olyan mintát fog látni, amelyek szemléltetik a memóriában lévő OLTP használatát, valamint a Azure SQL Database oszlopcentrikus indexeit.

További információkért lásd:
- [Memóriában tárolt OLTP – áttekintés és használati forgatókönyvek](https://msdn.microsoft.com/library/mt774593.aspx) (az ügyfél-esettanulmányokra és a kezdéshez szükséges információkra mutató hivatkozásokat tartalmaz)
- [A memóriában tárolt OLTP dokumentációja](https://msdn.microsoft.com/library/dn133186.aspx)
- [Útmutató a oszlopcentrikus indexekhez](https://msdn.microsoft.com/library/gg492088.aspx)
- Hibrid tranzakciós/analitikus feldolgozás (HTAP), más néven [valós idejű operatív elemzés](https://msdn.microsoft.com/library/dn817827.aspx)

<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

&nbsp;

## <a name="1-install-the-in-memory-oltp-sample"></a>1. A memóriában tárolt OLTP-minta telepítése

A AdventureWorksLT minta adatbázisát a [Azure Portal](https://portal.azure.com/)néhány kattintással létrehozhatja. Ezt követően a szakasz lépései azt ismertetik, hogyan bővíthetők a AdventureWorksLT-adatbázisok a memóriában lévő OLTP-objektumokkal, és milyen teljesítménybeli előnyökkel járnak.

A memóriabeli OLTP egyszerűbb, de vizuálisan vonzó teljesítmény-bemutatója a következő helyen található:

- Kiadás: [memóriabeli OLTP-bemutató-v 1.0](https://github.com/Microsoft/sql-server-samples/releases/tag/in-memory-oltp-demo-v1.0)
- Forráskód: [a memóriában-OLTP-demo-Source-Code](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/in-memory/ticket-reservations)

#### <a name="installation-steps"></a>Telepítési lépések

1. A [Azure Portalban](https://portal.azure.com/)hozzon létre egy prémium vagy üzletileg kritikus adatbázist egy kiszolgálón. Állítsa a **forrást** a AdventureWorksLT mintaadatbázis értékre. Részletes útmutatás: [az első Azure SQL-adatbázis létrehozása](sql-database-single-database-get-started.md).

2. Kapcsolódjon az adatbázishoz SQL Server Management Studio [(SSMS. exe)](https://msdn.microsoft.com/library/mt238290.aspx)használatával.

3. Másolja a [memóriában tárolt OLTP Transact-SQL-szkriptet](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/sql_in-memory_oltp_sample.sql) a vágólapra. A T-SQL-szkript létrehozza a szükséges memóriában lévő objektumokat az 1. lépésben létrehozott AdventureWorksLT-mintaadatbázisban.

4. Illessze be a T-SQL-szkriptet a SSMS-be, majd hajtsa végre a parancsfájlt. A `MEMORY_OPTIMIZED = ON` záradék Create TABLEi utasítások elengedhetetlenek. Példa:


```sql
CREATE TABLE [SalesLT].[SalesOrderHeader_inmem](
    [SalesOrderID] int IDENTITY NOT NULL PRIMARY KEY NONCLUSTERED ...,
    ...
) WITH (MEMORY_OPTIMIZED = ON);
```


#### <a name="error-40536"></a>40536-es hiba


Ha a T-SQL-szkript futtatásakor a 40536-es hiba jelenik meg, futtassa a következő T-SQL-szkriptet annak ellenőrzéséhez, hogy az adatbázis támogatja-e a memóriában:


```sql
SELECT DatabasePropertyEx(DB_Name(), 'IsXTPSupported');
```


A **0** eredmény azt jelenti, hogy a memóriában nem támogatott a memória, és **1** azt jelenti, hogy a rendszer támogatja. A probléma diagnosztizálásához győződjön meg arról, hogy az adatbázis a prémium szintű szolgáltatási szinten van.


#### <a name="about-the-created-memory-optimized-items"></a>A létrehozott memóriára optimalizált elemek ismertetése

**Táblák**: A minta a következő memória-optimalizált táblákat tartalmazza:

- SalesLT.Product_inmem
- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem
- Demo.DemoSalesOrderHeaderSeed
- Demo.DemoSalesOrderDetailSeed


A memóriára optimalizált táblákat a SSMS **Object Explorer** keresztül ellenőrizheti. Kattintson a jobb gombbal**a** **táblák** > **szűrő** > **beállításainak** > szűrése elemre. Az érték 1.


Vagy lekérdezheti a katalógus nézeteit, például a következőket:


```sql
SELECT is_memory_optimized, name, type_desc, durability_desc
    FROM sys.tables
    WHERE is_memory_optimized = 1;
```


**Natív módon lefordított tárolt eljárás**: A SalesLT. USP _InsertSalesOrder_inmem a katalógus nézet lekérdezésével ellenőrizheti:


```sql
SELECT uses_native_compilation, OBJECT_NAME(object_id), definition
    FROM sys.sql_modules
    WHERE uses_native_compilation = 1;
```


&nbsp;

### <a name="run-the-sample-oltp-workload"></a>A minta OLTP számítási feladat futtatása

A következő két *tárolt eljárás* között az egyetlen különbség az, hogy az első eljárás a táblák memóriára optimalizált verzióit használja, míg a második eljárás a normál lemezes táblákat használja:

- SalesLT **.** usp_InsertSalesOrder **_inmem**
- SalesLT **.** usp_InsertSalesOrder **_ondisk**


Ebből a szakaszból megtudhatja, hogyan használhatja a Handy **ostress. exe** segédprogramot úgy, hogy a két tárolt eljárást felhasználja stresszes szinten. Összehasonlíthatja, hogy mennyi ideig tart a két stressz futtatása.


A ostress. exe futtatásakor javasoljuk, hogy a következőhöz tartozó paraméterek értékeit adja át:

- Nagy számú egyidejű kapcsolat futtatása a-N100 használatával.
- A-R500 használatával a kapcsolatok mindegyike több százszor.


Azonban érdemes lehet sokkal kisebb értékekkel kezdeni, mint például a-N10 és a-R50, hogy minden megfelelően működjön.


### <a name="script-for-ostressexe"></a>A ostress. exe parancsfájlja


Ez a szakasz a ostress. exe parancssorba ágyazott T-SQL-parancsfájlt jeleníti meg. A parancsfájl a korábban telepített T-SQL-parancsfájl által létrehozott elemeket használja.


A következő szkript egy példaként szolgáló értékesítési rendelést szúr be öt sorral a következő memóriáraoptimalizált táblákba:

- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem


```sql
DECLARE
    @i int = 0,
    @od SalesLT.SalesOrderDetailType_inmem,
    @SalesOrderID int,
    @DueDate datetime2 = sysdatetime(),
    @CustomerID int = rand() * 8000,
    @BillToAddressID int = rand() * 10000,
    @ShipToAddressID int = rand() * 10000;

INSERT INTO @od
    SELECT OrderQty, ProductID
    FROM Demo.DemoSalesOrderDetailSeed
    WHERE OrderID= cast((rand()*60) as int);

WHILE (@i < 20)
begin;
    EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT,
        @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od;
    SET @i = @i + 1;
end
```


A ostress. exe előző T-SQL-parancsfájljának *_ondisk* -verziójának lecserélése a *_inmem* alsztring mindkét előfordulását helyettesíti a *_ondisk*. Ezek a cserék a táblák és a tárolt eljárások nevét érintik.


### <a name="install-rml-utilities-and-ostress"></a>RML segédprogramok telepítése és`ostress`


Ideális esetben azt tervezi, hogy a ostress. exe fájlt egy Azure-beli virtuális gépen (VM) szeretné futtatni. Hozzon létre egy [Azure-beli virtuális gépet](https://azure.microsoft.com/documentation/services/virtual-machines/) ugyanabban az Azure földrajzi régióban, ahol a AdventureWorksLT-adatbázis található. Ehelyett a ostress. exe fájlt is futtathatja a laptopon.


A virtuális gépen vagy a választott gazdagépen telepítse a Replay Markup Language (RML) segédprogramokat. A segédprogramok közé tartoznak a ostress. exe.

További információkért lásd:
- A ostress. exe vitafórum a [memóriában tárolt OLTP](https://msdn.microsoft.com/library/mt465764.aspx).
- [Mintaadatbázis a memóriában tárolt OLTP](https://msdn.microsoft.com/library/mt465764.aspx).
- A [ostress. exe telepítésének blogja](https://blogs.msdn.com/b/psssql/archive/20../../cumulative-update-2-to-the-rml-utilities-for-microsoft-sql-server-released.aspx).



<!--
dn511655.aspx is for SQL 2014,
[Extensions to AdventureWorks to Demonstrate In-Memory OLTP]
(https://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx)

whereas for SQL 2016+
[Sample Database for In-Memory OLTP]
(https://msdn.microsoft.com/library/mt465764.aspx)
-->



### <a name="run-the-inmem-stress-workload-first"></a>Először futtassa a *_inmem* Stress munkaterhelés-t


A ostress. exe parancssorának futtatásához használhatja a *RML* parancssori ablakát. A közvetlen `ostress` parancssori paraméterek:

- Az 100-es kapcsolatok párhuzamos futtatása (-N100).
- Minden egyes kapcsolatban futtassa a T-SQL-szkriptet 50-szor (-R50).


```
ostress.exe -n100 -r50 -S<servername>.database.windows.net -U<login> -P<password> -d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_inmem, @SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() * 8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand()* 10000; INSERT INTO @od SELECT OrderQty, ProductID FROM Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*60) as int); WHILE (@i < 20) begin; EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od; set @i += 1; end"
```


A fenti ostress. exe parancssor futtatása:


1. Az adatbázis-adattartalom alaphelyzetbe állításához futtassa a következő parancsot a SSMS, és törölje az összes korábbi Futtatás által beszúrt adatokat:

    ``` tsql
    EXECUTE Demo.usp_DemoReset;
    ```

2. Másolja az előző ostress. exe parancssori szöveget a vágólapra.

3. A megfelelő `<placeholders>` valós értékekkel cserélje le a paramétert a-S-U-P-d paraméterekre.

4. Futtassa a szerkesztett parancssort egy RML cmd-ablakban.


#### <a name="result-is-a-duration"></a>Az eredmény egy időtartam


Ha `ostress.exe` befejeződik, a futtatási időtartamot a RML cmd ablakban a kimenet utolsó soraként írja be. Például egy rövidebb teszt futtatása körülbelül 1,5 percig tartott:

`11/12/15 00:35:00.873 [0x000030A8] OSTRESS exiting normally, elapsed time: 00:01:31.867`


#### <a name="reset-edit-for-ondisk-then-rerun"></a>Alaphelyzetbe állítás, szerkesztés a *_ondisk*, majd futtassa újra


Miután elvégezte a *_inmem* futtatásának eredményét, hajtsa végre a következő lépéseket a *_ondisk* futtatásához:


1. Állítsa alaphelyzetbe az adatbázist úgy, hogy az alábbi parancsot futtatja a SSMS az előző Futtatás által beszúrt összes érték törléséhez:
   ```sql
   EXECUTE Demo.usp_DemoReset;
   ```

2. Szerkessze a ostress. exe parancssort az összes *_inmem* az *_ondisk*-vel való lecseréléséhez.

3. Futtassa újra a ostress. exe fájlt a második alkalommal, és rögzítse az időtartam eredményét.

4. Ismét állítsa alaphelyzetbe az adatbázist (a felelősségteljes törléshez, ami nagy mennyiségű tesztelési adattal rendelkezhet).


#### <a name="expected-comparison-results"></a>Várt összehasonlító eredmények

A memórián belüli tesztek azt mutatták, hogy a teljesítmény **kilenc alkalommal** javult ebben az egyszerű munkaterhelésben, és `ostress` egy Azure-beli virtuális gépen fut, amely ugyanabban az Azure-régióban található, mint az adatbázis.

<a id="install_analytics_manuallink" name="install_analytics_manuallink"></a>

&nbsp;

## <a name="2-install-the-in-memory-analytics-sample"></a>2. A memóriában tárolt elemzési minta telepítése


Ebben a szakaszban összehasonlítja az i/o-és statisztikai eredményeket, ha oszlopcentrikus indexet használ egy hagyományos b-Tree indexhez képest.


A OLTP számítási feladatokhoz a valós idejű elemzések esetében általában nem fürtözött oszlopcentrikus index használata ajánlott. Részletekért lásd: a [Oszlopcentrikus indexek](https://msdn.microsoft.com/library/gg492088.aspx)ismertetése.



### <a name="prepare-the-columnstore-analytics-test"></a>A oszlopcentrikus Analytics-teszt előkészítése


1. A mintából hozzon létre egy friss AdventureWorksLT-adatbázist a Azure Portal használatával.
   - Használja ezt a pontos nevet.
   - Válassza ki a prémium szintű szolgáltatási szintet.

2. Másolja a [sql_in-memory_analytics_sample](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/sql_in-memory_analytics_sample.sql) a vágólapra.
   - A T-SQL-szkript létrehozza a szükséges memóriában lévő objektumokat az 1. lépésben létrehozott AdventureWorksLT-mintaadatbázisban.
   - A szkript létrehozza a dimenzió táblát és két egyedkapcsolat táblát. Az egyedkapcsolat-táblák 3 500 000-sorokkal vannak feltöltve.
   - A szkript végrehajtása 15 percet is igénybe vehet.

3. Illessze be a T-SQL-szkriptet a SSMS-be, majd hajtsa végre a parancsfájlt. A **create index** utasításban a **oszlopcentrikus** kulcsszó elengedhetetlen, a következő módon:<br/>`CREATE NONCLUSTERED COLUMNSTORE INDEX ...;`

4. A AdventureWorksLT beállítása a 130 kompatibilitási szintre:<br/>`ALTER DATABASE AdventureworksLT SET compatibility_level = 130;`

    Az 130-as szint nem kapcsolódik közvetlenül a memóriához tartozó szolgáltatásokhoz. Az 130-as szint azonban általában gyorsabb lekérdezési teljesítményt nyújt, mint a 120.


#### <a name="key-tables-and-columnstore-indexes"></a>Legfontosabb táblák és oszlopcentrikus indexek


- dbo. A FactResellerSalesXL_CCI olyan tábla, amely egy fürtözött oszlopcentrikus indextel rendelkezik, amely az *adatszinten speciális* tömörítést tartalmaz.

- dbo. A FactResellerSalesXL_PageCompressed olyan tábla, amely egyenértékű normál fürtözött indexszel rendelkezik, amely csak az *oldal* szintjén tömörítve van.


#### <a name="key-queries-to-compare-the-columnstore-index"></a>A oszlopcentrikus index összehasonlítására szolgáló legfontosabb lekérdezések


[Több T-SQL-lekérdezési típus is futtatható](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/clustered_columnstore_sample_queries.sql) a teljesítmény növelésének megtekintéséhez. A T-SQL-parancsfájl 2. lépésében figyeljen erre a pár lekérdezésre. Csak egy sorban térnek el:


- `FROM FactResellerSalesXL_PageCompressed a`
- `FROM FactResellerSalesXL_CCI a`


A fürtözött oszlopcentrikus index a FactResellerSalesXL\_CCI-táblázatban található.

A következő T-SQL-szkript részletesen kinyomtatja az IO és az idő adatait az egyes táblák lekérdezéséhez.


```sql
/*********************************************************************
Step 2 -- Overview
-- Page Compressed BTree table v/s Columnstore table performance differences
-- Enable actual Query Plan in order to see Plan differences when Executing
*/
-- Ensure Database is in 130 compatibility mode
ALTER DATABASE AdventureworksLT SET compatibility_level = 130
GO

-- Execute a typical query that joins the Fact Table with dimension tables
-- Note this query will run on the Page Compressed table, Note down the time
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO

SELECT c.Year
    ,e.ProductCategoryKey
    ,FirstName + ' ' + LastName AS FullName
    ,count(SalesOrderNumber) AS NumSales
    ,sum(SalesAmount) AS TotalSalesAmt
    ,Avg(SalesAmount) AS AvgSalesAmt
    ,count(DISTINCT SalesOrderNumber) AS NumOrders
    ,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_PageCompressed a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO
SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO


-- This is the same Prior query on a table with a clustered columnstore index CCI
-- The comparison numbers are even more dramatic the larger the table is (this is an 11 million row table only)
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO
SELECT c.Year
    ,e.ProductCategoryKey
    ,FirstName + ' ' + LastName AS FullName
    ,count(SalesOrderNumber) AS NumSales
    ,sum(SalesAmount) AS TotalSalesAmt
    ,Avg(SalesAmount) AS AvgSalesAmt
    ,count(DISTINCT SalesOrderNumber) AS NumOrders
    ,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_CCI a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO

SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO
```

A P2 díjszabási csomaggal rendelkező adatbázisban a lekérdezés teljesítményének növelését a hagyományos indexszel összehasonlítva a fürtözött oszlopcentrikus index használatával lehet számítani. A P15 a oszlopcentrikus index használatával várhatóan körülbelül 57-szor a teljesítmény nyeresége.



## <a name="next-steps"></a>További lépések

- [1. gyors útmutató: Memóriabeli OLTP technológiák a gyorsabb T-SQL teljesítményhez](https://msdn.microsoft.com/library/mt694156.aspx)

- [Memóriában tárolt OLTP használata meglévő Azure SQL-alkalmazásokban](sql-database-in-memory-oltp-migration.md)

- [Memóriában tárolt OLTP](sql-database-in-memory-oltp-monitoring.md) -tárolók figyelése a memóriában lévő OLTP


## <a name="additional-resources"></a>További források

#### <a name="deeper-information"></a>Mélyebb információk

- [Ismerje meg, hogy a kvórum megduplázza a kulcsfontosságú adatbázis számítási feladatait, miközben 70%-kal csökkenti a DTU a memóriában tárolt OLTP SQL Database](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)

- [Memóriában tárolt OLTP Azure SQL Database blogbejegyzésben](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)

- [Tudnivalók a memóriában tárolt OLTP](https://msdn.microsoft.com/library/dn133186.aspx)

- [Tudnivalók a oszlopcentrikus indexekről](https://msdn.microsoft.com/library/gg492088.aspx)

- [Tudnivalók a valós idejű operatív elemzésekről](https://msdn.microsoft.com/library/dn817827.aspx)

- Tekintse meg a [gyakori számítási feladatok mintáit és](https://msdn.microsoft.com/library/dn673538.aspx) az áttelepítési megfontolásokat (amelyek a munkaterhelési mintákat ismertetik, ahol a memóriabeli OLTP gyakran jelentős teljesítmény

#### <a name="application-design"></a>Az alkalmazás kialakítása

- [Memóriában tárolt OLTP (memórián belüli optimalizálás)](https://msdn.microsoft.com/library/dn133186.aspx)

- [Memóriában tárolt OLTP használata meglévő Azure SQL-alkalmazásokban](sql-database-in-memory-oltp-migration.md)

#### <a name="tools"></a>Eszközök

- [Azure Portal](https://portal.azure.com/)

- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)

- [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)
