---
title: Kibővített felhőalapú adatbázisok közötti jelentéskészítés | Microsoft Docs
description: rugalmas lekérdezések beállítása horizontális partíciókon
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
ms.date: 01/03/2019
ms.openlocfilehash: 1416cbdc29d355e2ed83737140b46306de734127
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568576"
---
# <a name="reporting-across-scaled-out-cloud-databases-preview"></a>A kibővített felhőalapú adatbázisok (előzetes verzió) közötti jelentéskészítés

![Lekérdezés több szegmens között][1]

A többszintű adatbázisok egy horizontálisan elosztott adatszinten osztják el a sorokat. A séma minden résztvevő adatbázison azonos, más néven horizontális particionálás. A rugalmas lekérdezés használatával olyan jelentéseket hozhat létre, amelyek egy többszegmensű adatbázisban lévő összes adatbázisra kiterjednek.

Első lépésként tekintse meg a kibővített [felhőalapú adatbázisok jelentéskészítését](sql-database-elastic-query-getting-started.md)ismertető témakört.

A nem többrétegű adatbázisok esetében lásd: [lekérdezés a Felhőbeli adatbázisok között különböző sémákkal](sql-database-elastic-query-vertical-partitioning.md).

## <a name="prerequisites"></a>Előfeltételek

* Hozzon létre egy szegmenses térképet a rugalmas adatbázis-ügyféloldali kódtár használatával. Lásd: a szegmenses [Térkép kezelése](sql-database-elastic-scale-shard-map-management.md). Vagy használja a minta alkalmazást a [rugalmas adatbázis-eszközök használatának első lépéseiben](sql-database-elastic-scale-get-started.md).
* Másik megoldásként tekintse [meg a meglévő adatbázisok áttelepítését a felskálázásra szolgáló adatbázisokra](sql-database-elastic-convert-to-use-elastic-tools.md)című témakört.
* A felhasználónak rendelkeznie kell a külső adatforrásra vonatkozó engedély megváltoztatásával. Ez az engedély az ALTER DATABASE engedély részét képezi.
* Az alapul szolgáló adatforrásra való hivatkozáshoz minden külső ADATFORRÁS engedélyére van szükség.

## <a name="overview"></a>Áttekintés

Ezek az utasítások a rugalmas lekérdezési adatbázisban létrehozzák a szilánkokra bontott adatcsomag metaadat-ábrázolását.

1. [FŐKULCS LÉTREHOZÁSA](https://msdn.microsoft.com/library/ms174382.aspx)
2. [ADATBÁZIS-HATÓKÖRŰ HITELESÍTŐ ADAT LÉTREHOZÁSA](https://msdn.microsoft.com/library/mt270260.aspx)
3. [KÜLSŐ ADATFORRÁS LÉTREHOZÁSA](https://msdn.microsoft.com/library/dn935022.aspx)
4. [KÜLSŐ TÁBLA LÉTREHOZÁSA](https://msdn.microsoft.com/library/dn935021.aspx)

## <a name="11-create-database-scoped-master-key-and-credentials"></a>1,1 adatbázis-hatókörű főkulcs és hitelesítő adatok létrehozása

A rugalmas lekérdezés a hitelesítő adatokat használja a távoli adatbázisokhoz való kapcsolódáshoz.  

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'password';
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
    SECRET = '<password>'
    [;]

> [!NOTE]
> Győződjön meg arról, *hogy\<a\>"username"* nem tartalmaz *"\@servername"* utótagot.

## <a name="12-create-external-data-sources"></a>1,2 külső adatforrások létrehozása

Szintaxis

    <External_Data_Source> ::=
    CREATE EXTERNAL DATA SOURCE <data_source_name> WITH
            (TYPE = SHARD_MAP_MANAGER,
                       LOCATION = '<fully_qualified_server_name>',
            DATABASE_NAME = ‘<shardmap_database_name>',
            CREDENTIAL = <credential_name>,
            SHARD_MAP_NAME = ‘<shardmapname>’
                   ) [;]

### <a name="example"></a>Példa

    CREATE EXTERNAL DATA SOURCE MyExtSrc
    WITH
    (
        TYPE=SHARD_MAP_MANAGER,
        LOCATION='myserver.database.windows.net',
        DATABASE_NAME='ShardMapDatabase',
        CREDENTIAL= SMMUser,
        SHARD_MAP_NAME='ShardMap'
    );

Az aktuális külső adatforrások listájának beolvasása:

    select * from sys.external_data_sources;

A külső adatforrás a szegmens térképre hivatkozik. A rugalmas lekérdezés ezután a külső adatforrást és a mögöttes szegmenses térképet használja az adatrétegben részt vevő adatbázisok enumerálásához.
Ugyanazokat a hitelesítő adatokat használja a rendszer, hogy beolvassa a szegmensek térképét, és egy rugalmas lekérdezés feldolgozása során hozzáférjen a szegmensekhez tartozó adatokhoz.

## <a name="13-create-external-tables"></a>1,3 külső táblák létrehozása

Szintaxis  

    CREATE EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name  
        ( { <column_definition> } [ ,...n ])
        { WITH ( <sharded_external_table_options> ) }
    ) [;]  

    <sharded_external_table_options> ::=
      DATA_SOURCE = <External_Data_Source>,
      [ SCHEMA_NAME = N'nonescaped_schema_name',]
      [ OBJECT_NAME = N'nonescaped_object_name',]
      DISTRIBUTION = SHARDED(<sharding_column_name>) | REPLICATED |ROUND_ROBIN

**Példa**

    CREATE EXTERNAL TABLE [dbo].[order_line](
         [ol_o_id] int NOT NULL,
         [ol_d_id] tinyint NOT NULL,
         [ol_w_id] int NOT NULL,
         [ol_number] tinyint NOT NULL,
         [ol_i_id] int NOT NULL,
         [ol_delivery_d] datetime NOT NULL,
         [ol_amount] smallmoney NOT NULL,
         [ol_supply_w_id] int NOT NULL,
         [ol_quantity] smallint NOT NULL,
         [ol_dist_info] char(24) NOT NULL
    )

    WITH
    (
        DATA_SOURCE = MyExtSrc,
         SCHEMA_NAME = 'orders',
         OBJECT_NAME = 'order_details',
        DISTRIBUTION=SHARDED(ol_w_id)
    );

A külső táblák listájának beolvasása az aktuális adatbázisból:

    SELECT * from sys.external_tables;

Külső táblák eldobása:

    DROP EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name[;]

### <a name="remarks"></a>Megjegyzések

Az adatforrás\_-záradék meghatározza a külső táblához használt külső adatforrást (egy szegmenses leképezést).  

A séma\_neve és objektumnév\_záradéka a külső tábla definícióját egy másik sémában lévő táblára képezi le. Ha nincs megadva, a rendszer feltételezi, hogy a távoli objektum sémája "dbo" lesz, és a neve megegyezik a definiált külső táblanév nevével. Ez akkor hasznos, ha a távoli tábla neve már szerepel az adatbázisban, amelyben létre kívánja hozni a külső táblát. Tegyük fel például, hogy egy külső táblázatot szeretne meghatározni, amely összesített nézetet jelenít meg a katalógus nézeteiből vagy a DMV a kibővíthető adatszinten. Mivel a katalógus-nézetek és a DMV már helyileg léteznek, a külső tábla definíciójának neve nem használható. Ehelyett használjon másik nevet, és használja a katalógus nézet vagy a DMV nevét a séma\_neve és/vagy objektumnév\_záradékban. (Lásd az alábbi példát.)

A DISTRIBUTion záradék meghatározza az ehhez a táblához használt adateloszlást. A lekérdezési processzor a terjesztési záradékban található információkat használja a leghatékonyabb lekérdezési csomagok létrehozásához.

1. A szilánkos érték azt jelenti, hogy az adategységek horizontálisan vannak particionálva az adatbázisok között. Az Adatterjesztés particionálási kulcsa a **< sharding_column_name >** paraméter.
2. A replikált érték azt jelenti, hogy a tábla azonos példányszámban szerepel az egyes adatbázisokon. Az Ön felelőssége, hogy a replikák azonosak legyenek az adatbázisok között.
3. **A\_ciklikus multiplexelés** azt jelenti, hogy a tábla vízszintesen particionálva van egy alkalmazás-függő terjesztési módszer használatával.

**Adatcsomag**-referenciák: A külső tábla DDL egy külső adatforrásra hivatkozik. A külső adatforrás egy szegmenses térképet határoz meg, amely a külső táblát tartalmazza az adatrétegben található összes adatbázis megkereséséhez szükséges információkkal.

### <a name="security-considerations"></a>Biztonsági szempontok

A külső táblához hozzáféréssel rendelkező felhasználók automatikusan hozzáférhetnek a mögöttes távoli táblákhoz a külső adatforrás definíciójában megadott hitelesítő adatok alatt. A külső adatforrás hitelesítő adataival Kerülje a jogosultságok nem kívánt kiterjesztését. Egy külső tábla esetében adja meg az engedélyezés vagy a visszavonás lehetőséget, ugyanúgy, mintha egy normál tábla lenne.  

A külső adatforrás és a külső táblák meghatározása után mostantól teljes T-SQL-T használhat a külső táblákon.

## <a name="example-querying-horizontal-partitioned-databases"></a>Példa: vízszintes particionált adatbázisok lekérdezése

A következő lekérdezés háromféleképpen csatlakozik a raktárak, a megrendelések és a rendelési sorok között, és több összesítést és egy szelektív szűrőt használ. Feltételezi, hogy (1) horizontális particionálási (skálázási) és (2), hogy a raktárak, a megrendelések és a rendelési sorok a Warehouse ID oszlop szerint vannak elválasztva, és hogy a rugalmas lekérdezés közösen megkeresheti a szegmensek összekapcsolását, és feldolgozhatja a lekérdezés költséges részét a párhuzamos.

```sql
    select  
         w_id as warehouse,
         o_c_id as customer,
         count(*) as cnt_orderline,
         max(ol_quantity) as max_quantity,
         avg(ol_amount) as avg_amount,
         min(ol_delivery_d) as min_deliv_date
    from warehouse
    join orders
    on w_id = o_w_id
    join order_line
    on o_id = ol_o_id and o_w_id = ol_w_id
    where w_id > 100 and w_id < 200
    group by w_id, o_c_id
```

## <a name="stored-procedure-for-remote-t-sql-execution-spexecuteremote"></a>Tárolt eljárás távoli T-SQL-végrehajtáshoz:\_SP execute_remote

A rugalmas lekérdezés egy tárolt eljárást is bevezet, amely közvetlen hozzáférést biztosít a szegmensekhez. A tárolt eljárás neve [\_SP Execute \_Remote](https://msdn.microsoft.com/library/mt703714) , és használható távoli tárolt eljárások vagy T-SQL-kód végrehajtásához a távoli adatbázisokon. A következő paramétereket veszi figyelembe:

* Adatforrás neve (nvarchar): A RDBMS típusú külső adatforrás neve.
* Lekérdezés (nvarchar): Az egyes szegmenseken végrehajtandó T-SQL-lekérdezés.
* Paraméter deklarációja (nvarchar) – nem kötelező: A lekérdezési paraméterben (például Sp_executesql) használt paraméterek adattípus-definícióit tartalmazó karakterlánc.
* Paraméter értékének listája – nem kötelező: A paraméterek értékeinek vesszővel tagolt listája (például Sp_executesql).

Az SP\_Execute\_Remote a Meghívási paraméterekben megadott külső adatforrást használja az adott T-SQL-utasítás távoli adatbázisokon való végrehajtásához. A külső adatforrás hitelesítő adatait használja a shardmap-kezelő adatbázisához és a távoli adatbázisokhoz való kapcsolódáshoz.  

Példa:

```sql
    EXEC sp_execute_remote
        N'MyExtSrc',
        N'select count(w_id) as foo from warehouse'
```

## <a name="connectivity-for-tools"></a>Eszközökhöz való kapcsolódás

Az alkalmazáshoz, a BI-és az adatintegrációs eszközökhöz a külső táblázatos definíciókkal összekapcsolva rendszeres SQL Server kapcsolati karakterláncokat használhat. Győződjön meg arról, hogy a SQL Server támogatott adatforrásként az eszköz számára. Ezután hivatkozzon a rugalmas lekérdezési adatbázisra, mint az eszközhöz csatlakoztatott bármely más SQL Server adatbázisra, és használjon külső táblákat az eszközről vagy alkalmazásból, mintha helyi táblák lennének.

## <a name="best-practices"></a>Ajánlott eljárások

* Győződjön meg arról, hogy a rugalmas lekérdezési végpont adatbázisa hozzáférést kapott a shardmap adatbázishoz és az összes szegmenshez az SQL DB-tűzfalakon keresztül.  
* Érvényesítse vagy kényszerítse a külső tábla által definiált adatterjesztést. Ha a tényleges adateloszlás eltér a tábla definíciójában megadott eloszlástól, előfordulhat, hogy a lekérdezések nem várt eredményeket eredményeznek.
* A rugalmas lekérdezés jelenleg nem hajtja végre a szegmensek eltávolítását, ha az predikátumok a horizontális Felskálázási kulcs során lehetővé teszik, hogy biztonságosan kizárjon bizonyos szegmenseket a feldolgozásból.
* A rugalmas lekérdezés a legjobb megoldás, ha a számítások többsége a szegmenseken végezhető el. A legjobb lekérdezési teljesítményt általában a szelektív szűrési predikátumokkal lehet kiértékelni, amelyek kiértékelése a szegmenseken vagy az összekapcsolások a particionálási kulcsokon keresztül történik, amelyek az összes szegmensen elvégezhető partícióra igazított módon hajthatók végre. Előfordulhat, hogy más lekérdezési minták nagy mennyiségű adat betöltését végzik a szegmensek és a fő csomópont között, és nem megfelelően

## <a name="next-steps"></a>További lépések

* A rugalmas lekérdezés áttekintését lásd: [rugalmas lekérdezés áttekintése](sql-database-elastic-query-overview.md).
* A vertikális particionálással kapcsolatos oktatóanyagért lásd: [Bevezetés az adatbázisok közötti lekérdezéssel (vertikális particionálás)](sql-database-elastic-query-getting-started-vertical.md).
* A függőlegesen particionált információk szintaxisát és mintáit lásd [](sql-database-elastic-query-vertical-partitioning.md) : függőlegesen particionált adatlekérdezés
* A horizontális particionálással (skálázással) kapcsolatos oktatóanyagért lásd: az [első lépések a rugalmas lekérdezéssel a horizontális particionáláshoz](sql-database-elastic-query-getting-started.md).
* Lásd [:\_az \_SP távoli futtatása](https://msdn.microsoft.com/library/mt703714) olyan tárolt eljáráshoz, amely Transact-SQL-utasítást hajt végre egyetlen távoli Azure SQL Database vagy egy horizontális particionálási sémában szegmensként szolgáló adatbázis-készleten.

<!--Image references-->
[1]: ./media/sql-database-elastic-query-horizontal-partitioning/horizontalpartitioning.png
<!--anchors-->
