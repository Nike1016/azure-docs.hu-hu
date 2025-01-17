---
title: Tervezési útmutató a replikált táblákhoz – Azure SQL Data Warehouse | Microsoft Docs
description: Javaslatok a replikált táblák tervezéséhez a Azure SQL Data Warehouse sémában. 
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 03/19/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: c622edc6c3a37b2bc71323cf0e2c155f7aec6e33
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479316"
---
# <a name="design-guidance-for-using-replicated-tables-in-azure-sql-data-warehouse"></a>Tervezési útmutató a replikált táblák használatához Azure SQL Data Warehouse
Ez a cikk a SQL Data Warehouse séma replikált tábláinak kialakítására vonatkozó ajánlásokat ismerteti. Ezekkel az ajánlásokkal javíthatja a lekérdezési teljesítményt azáltal, hogy csökkenti az adatáthelyezést és a lekérdezés bonyolultságát.

> [!VIDEO https://www.youtube.com/embed/1VS_F37GI9U]

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy ismeri a SQL Data Warehouse adatelosztási és adatáthelyezési fogalmait.  További információt az [architektúrával](massively-parallel-processing-mpp-architecture.md) foglalkozó cikkben talál. 

A tábla kialakításának részeként a lehető legnagyobb mértékben megismerheti az adatait és az adatlekérdezés módját.  Vegyük például a következő kérdéseket:

- Mekkora a táblázat?   
- Milyen gyakran frissül a tábla?   
- Van-e az adattárházban egy tény-és dimenziós táblázat?   

## <a name="what-is-a-replicated-table"></a>Mi az a replikált tábla?
A replikált táblák minden számítási csomóponton elérhetők a tábla teljes másolatával. A táblázatok replikálásával megszűnik az adatok átvitele a számítási csomópontok között a csatlakozás vagy összesítés előtt. Mivel a táblának több példánya van, a replikált táblák akkor működnek a legjobban, ha a tábla mérete kisebb, mint 2 GB.  2 GB nem rögzített korlát.  Ha az adatmennyiség statikus, és nem változik, nagyobb táblákat is replikálhat.

Az alábbi ábrán egy replikált tábla látható, amely az egyes számítási csomópontokon elérhető. A SQL Data Warehouse-ben a replikált tábla minden számítási csomóponton teljes mértékben másolódik egy terjesztési adatbázisba. 

![Replikált tábla](media/guidance-for-using-replicated-tables/replicated-table.png "Replikált tábla")  

A replikált táblák jól működnek a Star-sémák dimenziós tábláiban. A dimenzió táblák általában a dimenzió táblától eltérő eloszlású táblákhoz vannak csatlakoztatva.  A méretek általában olyan méretűek, amelyek lehetővé teszik a több példány tárolását és karbantartását. A dimenziók a lassan megjelenő leíró adatokat tárolják, például az ügyfél nevét és a lakcímét, valamint a termék részleteit. Az adatmennyiség lassan változó jellege a replikált tábla kevesebb karbantartásához vezet. 

Egy replikált tábla használata:

- A lemezen lévő táblázat mérete kevesebb, mint 2 GB, a sorok számától függetlenül. A tábla méretének megkereséséhez használhatja a [DBCC PDW_SHOWSPACEUSED](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql) parancsot: `DBCC PDW_SHOWSPACEUSED('ReplTableCandidate')`. 
- A tábla olyan illesztésekben használatos, amelyek egyébként adatáthelyezést igényelnek. Ha olyan táblákat szeretne csatlakoztatni, amelyek nem ugyanazon az oszlopon vannak elosztva, például egy kivonattal elosztott táblán egy ciklikus multiplexelés táblázatba, a lekérdezés befejezéséhez adatáthelyezés szükséges.  Ha a táblák egyike kicsi, vegye fontolóra a replikált táblát. A legtöbb esetben javasolt a replikált táblák használata a ciklikus multiplexelés helyett. Az adatáthelyezési műveletek lekérdezési tervekben való megtekintéséhez használja a [sys. DM _pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql).  A BroadcastMoveOperation a tipikus adatáthelyezési művelet, amely egy replikált tábla használatával törölhető.  
 
A replikált táblák nem eredményezik a legjobb lekérdezési teljesítményt, ha:

- A tábla gyakori INSERT, Update és DELETE műveletekkel rendelkezik. Ezek az adatmanipulációs nyelvi (DML-) műveletek a replikált tábla újraépítését igénylik. A gyakran előforduló Újraépítés lassabb teljesítményt eredményezhet.
- Az adattárház méretezése gyakran megtörténik. Az adatraktár skálázása megváltoztatja a számítási csomópontok számát, amelyek a replikált tábla újraépítésekor keletkeznek.
- A tábla nagy számú oszlopot tartalmaz, de az adatműveletek általában csak kis számú oszlopot férnek hozzá. Ebben az esetben a teljes tábla replikálása helyett hatékonyabb lehet a tábla terjesztése, majd létre kell hozni egy indexet a gyakran használt oszlopokon. Ha egy lekérdezés adatáthelyezést igényel, a SQL Data Warehouse csak a kért oszlopokra helyezi át az adatátvitelt. 

## <a name="use-replicated-tables-with-simple-query-predicates"></a>A replikált táblák használata egyszerű lekérdezési predikátumokkal
Mielőtt megkezdené egy tábla terjesztését vagy replikálását, gondolja át, milyen típusú lekérdezéseket szeretne futtatni a táblán. Ha lehetséges,

- Replikált táblákat használhat egyszerű lekérdezési predikátumokkal, például egyenlőséggel vagy egyenlőtlenséggel rendelkező lekérdezéseknél.
- Elosztott táblák használata összetett lekérdezési predikátumokkal rendelkező lekérdezésekhez, például hasonló vagy nem hasonló.

A CPU-igényes lekérdezések akkor működnek a legjobban, ha a munka az összes számítási csomóponton el van osztva. A számításokat egy táblázat minden egyes sorára futtató lekérdezések például jobb teljesítményt biztosítanak az elosztott táblákon, mint a replikált táblák. Mivel a rendszer minden számítási csomóponton teljes egészében tárolja a replikált táblát, a rendszer egy, a replikált táblával kapcsolatos CPU-igényes lekérdezést futtat minden számítási csomóponton a teljes táblán. Az extra számítás lelassíthatja a lekérdezések teljesítményét.

Ez a lekérdezés például egy összetett predikátummal rendelkezik.  Gyorsabban fut, ha az adatforgalom replikált tábla helyett elosztott táblázatban található. Ebben a példában az adatgyűjtés ciklikusan terjeszthető lehet.

```sql

SELECT EnglishProductName 
FROM DimProduct 
WHERE EnglishDescription LIKE '%frame%comfortable%'

```

## <a name="convert-existing-round-robin-tables-to-replicated-tables"></a>Meglévő ciklikus időszeletelési táblázatok átalakítása replikált táblákra
Ha már van ciklikusan megnyitható táblái, javasoljuk, hogy konvertálja azokat replikált táblákra, ha azok megfelelnek a jelen cikkben leírt feltételeknek. A replikált táblák javítják a ciklikusan megjelenő táblák teljesítményét, mivel nem szükségesek az adatáthelyezéshez.  A Round-Robin tábla mindig adatáthelyezést igényel az illesztésekhez. 

Ez a példa a [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) használatával módosítja a DimSalesTerritory táblát egy replikált táblára. Ez a példa attól függetlenül működik, hogy a DimSalesTerritory kivonat-elosztott vagy ciklikus multiplexelés.

```sql
CREATE TABLE [dbo].[DimSalesTerritory_REPLICATE]   
WITH   
  (   
    CLUSTERED COLUMNSTORE INDEX,  
    DISTRIBUTION = REPLICATE  
  )  
AS SELECT * FROM [dbo].[DimSalesTerritory]
OPTION  (LABEL  = 'CTAS : DimSalesTerritory_REPLICATE') 

-- Switch table names
RENAME OBJECT [dbo].[DimSalesTerritory] to [DimSalesTerritory_old];
RENAME OBJECT [dbo].[DimSalesTerritory_REPLICATE] TO [DimSalesTerritory];

DROP TABLE [dbo].[DimSalesTerritory_old];
```  

### <a name="query-performance-example-for-round-robin-versus-replicated"></a>Lekérdezési teljesítmény – példa a ciklikus multiplexelés és a replikált 

A replikált táblákhoz nincs szükség adatáthelyezésre az illesztésekhez, mert a teljes tábla már megtalálható az egyes számítási csomópontokon. Ha a dimenziós táblák ciklikusan elosztottak, az illesztések teljes mértékben átmásolják a dimenzió táblát az egyes számítási csomópontokra. Az adatáthelyezéshez a lekérdezési terv egy BroadcastMoveOperation nevű műveletet tartalmaz. Ez a típusú adatáthelyezési művelet lelassítja a lekérdezési teljesítményt, és a replikált táblák használatával kiesik. A lekérdezési terv lépéseinek megtekintéséhez használja a [sys. DM _pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql) Rendszerkatalógus nézetét. 

A AdventureWorks séma következő lekérdezésében például a `FactInternetSales` tábla kivonat-eloszlású. A `DimDate` és`DimSalesTerritory` a táblák kisebb dimenzió táblák. Ez a lekérdezés a 2004-as pénzügyi év teljes értékesítéseit adja vissza Észak-Amerika:

```sql
SELECT [TotalSalesAmount] = SUM(SalesAmount)
FROM dbo.FactInternetSales s
INNER JOIN dbo.DimDate d
  ON d.DateKey = s.OrderDateKey
INNER JOIN dbo.DimSalesTerritory t
  ON t.SalesTerritoryKey = s.SalesTerritoryKey
WHERE d.FiscalYear = 2004
  AND t.SalesTerritoryGroup = 'North America'
```
Újra létre lett hozva `DimDate` , `DimSalesTerritory` és ciklikus időszeletelésű táblázatként. Ennek eredményeképpen a lekérdezés a következő lekérdezési tervet mutatta, amelynek több szórásos mozgatási művelete van: 
 
![Ciklikus multiplexelés – lekérdezési terv](media/design-guidance-for-replicated-tables/round-robin-tables-query-plan.jpg) 

A rendszer újból `DimDate` létrehozta `DimSalesTerritory` a és a replikált táblákat, majd újra futtatta a lekérdezést. Az eredményül kapott lekérdezési terv sokkal rövidebb, és nem rendelkezik szórási lépésekkel.

![Replikált lekérdezési terv](media/design-guidance-for-replicated-tables/replicated-tables-query-plan.jpg) 


## <a name="performance-considerations-for-modifying-replicated-tables"></a>A replikált táblák módosításának teljesítményével kapcsolatos megfontolások
SQL Data Warehouse egy replikált táblát valósít meg a tábla főverziójának megtartásával. Minden számítási csomóponton átmásolja a fő verziót egy terjesztési adatbázisba. Változás esetén SQL Data Warehouse először frissíti a főtáblát. Ezután újraépíti a táblákat az egyes számítási csomópontokon. A replikált tábla újraépítése magában foglalja a tábla másolását az egyes számítási csomópontokra, majd az indexek összeállítását.  Egy DW400 replikált táblájának például 5 másolata van az adatmennyiségről.  Egy fő másolat és egy teljes másolat az egyes számítási csomópontokon.  A rendszer minden adatforrást a terjesztési adatbázisokban tárol. SQL Data Warehouse ezt a modellt használja a gyorsabb adatmódosítási utasítások és a rugalmas skálázási műveletek támogatásához. 

Az Újraépítés a következő után szükséges:
- Az adatfeltöltés vagy módosítás
- Az adatraktár más szintre van méretezve
- A tábla definíciója frissítve

Az Újraépítés a következő után nem szükséges:
- Szüneteltetési művelet
- Folytatási művelet

Az Újraépítés nem történik meg közvetlenül az adatmódosítás után. Ehelyett az Újraépítés akkor aktiválódik, amikor a lekérdezés első alkalommal kiválasztja a táblázatot.  Az a lekérdezés, amely azonnal elindította az újraépítési beolvasásokat a tábla fő verziójából, miközben az adatok aszinkron módon másolódnak át az egyes számítási csomópontokra. Az Adatmásolás befejezéséig a további lekérdezések továbbra is a tábla fő verzióját használják.  Ha bármilyen tevékenység történik a replikált táblán, amely egy másik újraépítést kényszerít, az adatmásolat érvénytelenné válik, és a következő SELECT utasítás ismét átmásolja az adatok másolását. 

### <a name="use-indexes-conservatively"></a>Az indexek konzervatív használata
A szabványos indexelési eljárások a replikált táblákra vonatkoznak. SQL Data Warehouse az Újraépítés részeként újraépíti az egyes replikált táblák indexeit. Csak az indexek használata, ha a teljesítmény meghaladja az indexek újjáépítésének költségeit.  
 
### <a name="batch-data-loads"></a>Batch-adatterhelések
Ha replikált táblákba tölti be az adatgyűjtést, próbálja meg az Újraépítés minimalizálását a kötegelt betöltések együtt. A SELECT utasítások futtatása előtt hajtsa végre az összes kötegelt terhelést.

Például a terhelési minta négy forrásból tölti be az adatait, és négy újraépítést hív meg. 

- Betöltés az 1. forrásból.
- Válassza az utasítás Újraépítés 1 lehetőséget.
- Betöltés a 2. forrásból.
- A SELECT utasítás elindítja a 2. újraépítést.
- Betöltés a 3. forrásból.
- A SELECT utasítás elindítja a 3. Újraépítés elemet.
- Betöltés a 4. forrásból.
- Válassza az utasítás Újraépítés 4 lehetőséget.

Például a terhelési minta négy forrásból tölti be az adatait, de csak egy újraépítést hív meg.

- Betöltés az 1. forrásból.
- Betöltés a 2. forrásból.
- Betöltés a 3. forrásból.
- Betöltés a 4. forrásból.
- Válassza az utasítás eseményindítók Újraépítés lehetőséget.


### <a name="rebuild-a-replicated-table-after-a-batch-load"></a>Replikált tábla újraépítése batch-betöltés után
A konzisztens lekérdezés-végrehajtási idők biztosítása érdekében érdemes lehet a replikált táblákat a Batch betöltését követően kényszeríteni. Ellenkező esetben az első lekérdezés továbbra is az adatáthelyezést használja a lekérdezés befejezéséhez. 

Ez a lekérdezés a [sys. PDW _replicated_table_cache_state](/sql/relational-databases/system-catalog-views/sys-pdw-replicated-table-cache-state-transact-sql) DMV használatával sorolja fel a módosított, de nem létrehozott replikált táblákat.

```sql 
SELECT [ReplicatedTable] = t.[name]
  FROM sys.tables t  
  JOIN sys.pdw_replicated_table_cache_state c  
    ON c.object_id = t.object_id 
  JOIN sys.pdw_table_distribution_properties p 
    ON p.object_id = t.object_id 
  WHERE c.[state] = 'NotReady'
    AND p.[distribution_policy_desc] = 'REPLICATE'
```
 
Az Újraépítés elindításához futtassa a következő utasítást az előző kimenetben lévő összes táblán. 

```sql
SELECT TOP 1 * FROM [ReplicatedTable]
``` 
 
## <a name="next-steps"></a>További lépések 
Replikált tábla létrehozásához használja az alábbi utasítások egyikét:

- [CREATE TABLE (Azure SQL Data Warehouse)](/sql/t-sql/statements/create-table-azure-sql-data-warehouse)
- [CREATE TABLE a SELECT (Azure SQL Data Warehouse)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse)

Az elosztott táblák áttekintését lásd: [elosztott táblák](sql-data-warehouse-tables-distribute.md).



