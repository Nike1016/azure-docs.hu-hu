---
title: 'VS kód: Adatkapcsolat és-lekérdezés Azure SQL Databaseban | Microsoft Docs'
description: Ebből a cikkből megtudhatja, hogyan csatlakozhat az SQL Database-hez az Azure-ban a Visual Studio Code segítségével. Ezután futtasson Transact-SQL (T-SQL) utasításokat az adatok lekérdezéséhez és szerkesztéséhez.
keywords: csatlakozás SQL Database-adatbázishoz
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: d8f12e699c17787d897a7f5ed23eccdbf3659921
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569139"
---
# <a name="quickstart-use-visual-studio-code-to-connect-and-query-an-azure-sql-database"></a>Gyors útmutató: A Visual Studio Code használata egy Azure SQL Databasehoz való kapcsolódáshoz és lekérdezéshez

[A Visual Studio Code](https://code.visualstudio.com/docs) van egy grafikus Kódszerkesztő Linux, macOS és Windows. Támogatja a bővítményeket, beleértve a [mssql bővítményt](https://aka.ms/mssql-marketplace) a Microsoft SQL Server, az Azure SQL Database és az SQL Data Warehouse lekérdezéséhez. Ebben a rövid útmutatóban a Visual Studio Code használatával csatlakozik egy Azure SQL Database-adatbázishoz, majd Transact-SQL-utasítások futtatásával kérdezheti le, szúrhatja be, frissítheti és törölheti az adatokat.

## <a name="prerequisites"></a>Előfeltételek

- Azure SQL-adatbázis. Az alábbi rövid útmutatók segítségével hozhat létre és konfigurálhat egy adatbázist Azure SQL Databaseban:

  || Önálló adatbázis | Felügyelt példány |
  |:--- |:--- |:---|
  | Hozzon létre| [Portál](sql-database-single-database-get-started.md) | [Portál](sql-database-managed-instance-get-started.md) |
  || [Parancssori felület](scripts/sql-database-create-and-configure-database-cli.md) | [Parancssori felület](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Konfigurálás | [Kiszolgálói szintű IP-tűzfalszabály](sql-database-server-level-firewall-rule.md)| [Kapcsolódás virtuális gépről](sql-database-managed-instance-configure-vm.md)|
  |||[Kapcsolódás a webhelyről](sql-database-managed-instance-configure-p2s.md)
  |Adatok betöltése|Adventure Works betöltve|[Széles körű globális importőrök visszaállítása](sql-database-managed-instance-get-started-restore.md)
  |||Adventure Works visszaállítása vagy importálása a [BACPAC](sql-database-import.md) -fájlból a [githubról](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > A cikkben található parancsfájlok az Adventure Works-adatbázis használatára íródnak. Felügyelt példány esetén importálnia kell az Adventure Works-adatbázist egy példány-adatbázisba, vagy módosítania kell a jelen cikkben szereplő parancsfájlokat a Wide World Importálós adatbázis használatára.

## <a name="install-visual-studio-code"></a>A Visual Studio Code telepítése

Győződjön meg arról, hogy telepítette a legújabb [Visual Studio Code](https://code.visualstudio.com/Download) és betölti a [mssql bővítményt](https://aka.ms/mssql-marketplace). Az mssql bővítmény telepítésével kapcsolatos útmutatásért lásd: [VS Code telepítését](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-vscode#install-and-start-visual-studio-code) és [a Visual Studio Code mssql ](https://marketplace.visualstudio.com/items?itemName=ms-mssql.mssql).

## <a name="configure-visual-studio-code"></a>A Visual Studio Code konfigurálása

### <a name="mac-os"></a>**Mac OS**

MacOS esetén telepítenie kell az OpenSSL-t, amely az MSSQL-bővítmény által használt .NET Core előfeltétele. Nyissa meg a terminált, és adja meg az alábbi parancsokat a **brew** és az **OpenSSL** telepítéséhez.

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew update
brew install openssl
mkdir -p /usr/local/lib
ln -s /usr/local/opt/openssl/lib/libcrypto.1.0.0.dylib /usr/local/lib/
ln -s /usr/local/opt/openssl/lib/libssl.1.0.0.dylib /usr/local/lib/
```

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**

Nincs szükség különleges konfigurációra.

### <a name="windows"></a>**Windows**

Nincs szükség különleges konfigurációra.

## <a name="get-sql-server-connection-information"></a>SQL Server-kapcsolatok adatainak beolvasása

Az Azure SQL Database-adatbázishoz való kapcsolódáshoz szükséges kapcsolati adatok beolvasása. A közelgő eljárásokhoz szüksége lesz a teljes kiszolgálónévre vagy az állomásnévre, az adatbázis nevére és a bejelentkezési adatokra.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Navigáljon az **SQL-adatbázisok** vagy az **SQL-felügyelt példányok** lapra.

3. Az **Áttekintés** lapon tekintse át a teljes kiszolgálónevet a **kiszolgáló neve** mellett egyetlen adatbázishoz vagy a felügyelt példányhoz tartozó **gazdagép** melletti teljes kiszolgálónévhez. A kiszolgálónév vagy az állomásnév másolásához vigye a kurzort a fölé, és válassza a **Másolás** ikont.

## <a name="set-language-mode-to-sql"></a>A nyelvmód SQL értékre állítása

A Visual Studio Code-ban a nyelvmód beállítása **SQL** mssql-parancsok és a T-SQL IntelliSense engedélyezéséhez.

1. Nyisson meg egy új Visual Studio Code-ablakot.

2. Nyomja le a **CTRL**+**N**billentyűkombinációt. Megnyílik egy új egyszerű szöveges fájlt.

3. Válassza ki **egyszerű szöveges** az állapotsor jobb alsó sarkában található.

4. Az a **nyelvmód kiválasztása** legördülő menüben, amely megnyílik, válassza ki **SQL**.

## <a name="connect-to-your-database"></a>Csatlakozás az adatbázishoz

A Visual Studio Code segítségével kapcsolatot hozhat létre az Azure SQL Database-kiszolgálóval.

> [!IMPORTANT]
> A folytatás előtt győződjön meg arról, hogy a kiszolgáló és a bejelentkezési adatok készen állnak. Ha elkezdi beírni a csatlakozási profil információit, ha módosítja a fókuszt a Visual Studio Code-ból, akkor indítsa újra a profil létrehozásához.

1. A Visual Studio Code-ban nyomja le a **Ctrl + Shift + P** (vagy **F1**) a Parancskatalógus megnyitásához.

2. Válassza az **MS SQL: kapcsolat** lehetőséget, és válassza az **ENTER billentyűt**.

3. Válassza ki **kapcsolati profil létrehozása**.

4. Kövesse az utasításokat követve adja meg az új profil kapcsolati tulajdonságait. Az egyes értékek megadása után a folytatáshoz válassza az **ENTER billentyűt** .

   | Tulajdonság       | Ajánlott érték | Leírás |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Kiszolgálónév** | A teljes kiszolgálónév | Hasonló: **mynewserver20170313.database.windows.net**. |
   | **Adatbázis neve** | mySampleDatabase | Az adatbázis való csatlakozáshoz. |
   | **Hitelesítés** | SQL-bejelentkezés| Ez az oktatóanyag az SQL-hitelesítést használ. |
   | **Felhasználónév** | Felhasználónév | A kiszolgálói rendszergazdai fiókkal, a kiszolgáló létrehozásához használt felhasználónév. |
   | **Jelszó (SQL-bejelentkezés)** | Jelszó | A kiszolgáló létrehozásához használt, a kiszolgálói rendszergazdai fiók jelszava. |
   | **Menti a jelszót?** | Igen vagy Nem | Válassza ki **Igen** Ha nem szeretné, hogy minden alkalommal, amikor adja meg a jelszót. |
   | **Adja meg a profil kívánt nevét** | Egy profilnév, például **mySampleProfile** | A mentett profil felgyorsítja a csatlakozást a későbbi bejelentkezések során. |

   Ha ez sikeres, megjelenik egy értesítés, a profil létrehozásáról és arról tájékoztatja.

## <a name="query-data"></a>Adatok lekérdezése

Futtassa a következő [Select](https://msdn.microsoft.com/library/ms189499.aspx) Transact-SQL-utasítást az első 20 termék kategóriánkénti lekérdezéséhez.

1. A szerkesztő ablakban illessze be a következő SQL-lekérdezést.

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

2. A **CTRL**+ `Product` `ProductCategory` billentyűt+lenyomva futtassa a lekérdezést, és jelenítse meg az eredményeket a és a táblákból.

    ![2 táblákból származó adatok beolvasására irányuló lekérdezés](./media/sql-database-connect-query-vscode/query.png)

## <a name="insert-data"></a>Adat beszúrása

Futtassa az alábbi [Insert](https://msdn.microsoft.com/library/ms174335.aspx) Transact-SQL-utasítást egy új termék `SalesLT.Product` táblába való felvételéhez.

1. Ehhez cserélje le az előző lekérdezést.

   ```sql
   INSERT INTO [SalesLT].[Product]
        ( [Name]
        , [ProductNumber]
        , [Color]
        , [ProductCategoryID]
        , [StandardCost]
        , [ListPrice]
        , [SellStartDate]
        )
     VALUES
        ('myNewProduct'
        ,123456789
        ,'NewColor'
        ,1
         ,100
         ,100
         ,GETDATE() );
   ```

2. Nyomja meg **Ctrl**+**Shift**+**E** az új sor beszúrásához a `Product` tábla.

## <a name="update-data"></a>Adatok frissítése

Futtassa a következő [frissítési](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL-utasítást a hozzáadott termék frissítéséhez.

1. Cserélje le az előző lekérdezés erre:

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Nyomja meg **Ctrl**+**Shift**+**E** egy megadott sorának frissítéséhez a `Product` tábla.

## <a name="delete-data"></a>Adat törlése

Futtassa az alábbi [delete](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql) Transact-SQL utasítást az új termék eltávolításához.

1. Cserélje le az előző lekérdezés erre:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Nyomja meg **Ctrl**+**Shift**+**E** egy megadott sorának törléséhez a `Product` tábla.

## <a name="next-steps"></a>További lépések

- A SQL Server Management Studio használatával történő kapcsolódáshoz és lekérdezéshez lásd: gyors útmutató [: SQL Server Management Studio használatával csatlakozhat egy Azure SQL Databasehoz, és lekérdezheti azokat](sql-database-connect-query-ssms.md).
- A Azure Portal használatával történő kapcsolódáshoz és lekérdezéshez [lásd: gyors útmutató: A Azure Portal SQL-lekérdezés szerkesztőjével csatlakozhat, és lekérdezheti](sql-database-connect-query-portal.md)azokat.
- Az MSDN magazin Visual Studio Code használatáról szóló cikkéhez lásd az [Adatbázis IDE létrehozása az MSSQL bővítménnyel blogbejegyzést](https://msdn.microsoft.com/magazine/mt809115).
