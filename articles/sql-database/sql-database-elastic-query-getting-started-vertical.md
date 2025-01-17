---
title: Ismerkedés az adatbázisok közötti lekérdezésekkel (vertikális particionálás) | Microsoft Docs
description: rugalmas adatbázis-lekérdezés használata vertikálisan particionált adatbázisokkal
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: a6a87f90586dc4392dc1304a83349bc386590ee4
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568593"
---
# <a name="get-started-with-cross-database-queries-vertical-partitioning-preview"></a>Ismerkedés az adatbázisok közötti lekérdezésekkel (vertikális particionálás) (előzetes verzió)

A rugalmas adatbázis-lekérdezés (előzetes verzió) Azure SQL Database lehetővé teszi, hogy olyan T-SQL-lekérdezéseket futtasson, amelyek több adatbázisra is kiterjednek egyetlen kapcsolódási pont használatával. Ez a cikk a [vertikálisan particionált adatbázisokra](sql-database-elastic-query-vertical-partitioning.md)vonatkozik.  

Ha elkészült, megtudhatja, hogyan konfigurálhat és használhat egy Azure SQL Database a több kapcsolódó adatbázisra kiterjedő lekérdezések végrehajtásához.

További információ a rugalmas adatbázis lekérdezési funkciójával kapcsolatban: [Azure SQL Database rugalmas adatbázis lekérdezésének áttekintése](sql-database-elastic-query-overview.md).

## <a name="prerequisites"></a>Előfeltételek

MINDEN külső ADATFORRÁS-engedély megadása kötelező. Ez az engedély az ALTER DATABASE engedély részét képezi. Az alapul szolgáló adatforrásra való hivatkozáshoz minden külső ADATFORRÁS engedélyére van szükség.

## <a name="create-the-sample-databases"></a>A minta-adatbázisok létrehozása

Első lépésként hozzon létre két adatbázist , ügyfelet és **rendelést**ugyanabban vagy a különböző SQL Database-kiszolgálókon.

Hajtsa végre a következő lekérdezéseket a **Orders** adatbázisban a **OrderInformation** tábla létrehozásához és a mintaadatok beviteléhez.

    CREATE TABLE [dbo].[OrderInformation](
        [OrderID] [int] NOT NULL,
        [CustomerID] [int] NOT NULL
        )
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (123, 1)
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (149, 2)
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (857, 2)
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (321, 1)
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (564, 8)

Ezután hajtsa végre a következő lekérdezést a Customers adatbázisban a **CustomerInformation** tábla létrehozásához és a mintaadatok beviteléhez.

    CREATE TABLE [dbo].[CustomerInformation](
        [CustomerID] [int] NOT NULL,
        [CustomerName] [varchar](50) NULL,
        [Company] [varchar](50) NULL
        CONSTRAINT [CustID] PRIMARY KEY CLUSTERED ([CustomerID] ASC)
    )
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (1, 'Jack', 'ABC')
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (2, 'Steve', 'XYZ')
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (3, 'Lylla', 'MNO')

## <a name="create-database-objects"></a>Adatbázis-objektumok létrehozása

### <a name="database-scoped-master-key-and-credentials"></a>Adatbázis-hatókörű főkulcs és hitelesítő adatok

1. Nyissa meg SQL Server Management Studio vagy SQL Server Data Tools a Visual Studióban.
2. Kapcsolódjon az Orders adatbázishoz, és hajtsa végre a következő T-SQL-parancsokat:

        CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<master_key_password>';
        CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred
        WITH IDENTITY = '<username>',
        SECRET = '<password>';  

    A "username" és a "password" értéknek kell lennie az ügyfelek adatbázisba való bejelentkezéshez használt felhasználónévnek és jelszónak.
    A rugalmas lekérdezésekkel Azure Active Directory használatával történő hitelesítés jelenleg nem támogatott.

### <a name="external-data-sources"></a>Külső adatforrások

Külső adatforrás létrehozásához hajtsa végre a következő parancsot az Orders adatbázisban:

    CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH
        (TYPE = RDBMS,
        LOCATION = '<server_name>.database.windows.net',
        DATABASE_NAME = 'Customers',
        CREDENTIAL = ElasticDBQueryCred,
    ) ;

### <a name="external-tables"></a>Külső táblák

Hozzon létre egy külső táblát a orders (megrendelések) adatbázisban, amely megfelel a CustomerInformation tábla definíciójának:

    CREATE EXTERNAL TABLE [dbo].[CustomerInformation]
    ( [CustomerID] [int] NOT NULL,
      [CustomerName] [varchar](50) NOT NULL,
      [Company] [varchar](50) NOT NULL)
    WITH
    ( DATA_SOURCE = MyElasticDBQueryDataSrc)

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>Minta rugalmas adatbázis-lekérdezés végrehajtása T-SQL-lekérdezés

Miután meghatározta a külső adatforrást és a külső táblákat, mostantól a T-SQL használatával is lekérdezheti a külső táblákat. A lekérdezés végrehajtása a megrendelések adatbázisán:

    SELECT OrderInformation.CustomerID, OrderInformation.OrderId, CustomerInformation.CustomerName, CustomerInformation.Company
    FROM OrderInformation
    INNER JOIN CustomerInformation
    ON CustomerInformation.CustomerID = OrderInformation.CustomerID

## <a name="cost"></a>Költség

A rugalmas adatbázis lekérdezési funkciója jelenleg a Azure SQL Database költségeit tartalmazza.  

A díjszabással kapcsolatos információkért tekintse meg a [SQL Database díjszabását](https://azure.microsoft.com/pricing/details/sql-database).

## <a name="next-steps"></a>További lépések

* A rugalmas lekérdezés áttekintését lásd: [rugalmas lekérdezés áttekintése](sql-database-elastic-query-overview.md).
* A függőlegesen particionált információk szintaxisát és mintáit lásd [](sql-database-elastic-query-vertical-partitioning.md) : függőlegesen particionált adatlekérdezés
* A horizontális particionálással (skálázással) kapcsolatos oktatóanyagért lásd: az [első lépések a rugalmas lekérdezéssel a horizontális particionáláshoz](sql-database-elastic-query-getting-started.md).
* A horizontálisan particionált információk szintaxisát és mintáit lásd [](sql-database-elastic-query-horizontal-partitioning.md) : vízszintesen particionált adatlekérdezés
* Lásd [:\_az \_SP távoli futtatása](https://msdn.microsoft.com/library/mt703714) olyan tárolt eljáráshoz, amely Transact-SQL-utasítást hajt végre egyetlen távoli Azure SQL Database vagy egy horizontális particionálási sémában szegmensként szolgáló adatbázis-készleten.
