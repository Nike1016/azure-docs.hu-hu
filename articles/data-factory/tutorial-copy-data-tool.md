---
title: Adatok másolása az Azure Adatok másolása eszközével | Microsoft Docs
description: Létrehozhat egy Azure-beli adat-előállítót, majd az Adatok másolása eszközzel másolhat adatokat egy Azure Blob Storage-ból egy SQL Database-be.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 09/11/2018
ms.author: jingwang
ms.openlocfilehash: a99f319c25613f811b48923c734b77b135b677bc
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68827949"
---
# <a name="copy-data-from-azure-blob-storage-to-a-sql-database-by-using-the-copy-data-tool"></a>Adatok másolása az Azure Blob Storage-ból egy SQL Database-be az Adatok másolása eszközzel
> [!div class="op_single_selector" title1="Válassza ki a használni kívánt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Aktuális verzió](tutorial-copy-data-tool.md)

Ebben az oktatóanyagban egy adat-előállítót hoz létre az Azure Portal használatával. Ezután a Adatok másolása eszközzel létrehozhat egy folyamatot, amely az Azure Blob Storage-ból másol egy SQL Database-adatbázisba.

> [!NOTE]
> Ha még csak ismerkedik az Azure Data Factory használatával, olvassa el [az Azure Data Factory használatának első lépéseit](introduction.md) ismertető cikket.

Az oktatóanyagban az alábbi lépéseket fogja végrehajtani:
> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Folyamat létrehozása az Adatok másolása eszközzel.
> * A folyamat és a tevékenységek futásának monitorozása

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**: Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* **Azure Storage-fiók**: A blob Storage-t használja _forrás_ adattárként. Ha még nem rendelkezik Azure Storage-fiókkal, a szükséges utasításokat a [Storage-fiók létrehozását](../storage/common/storage-quickstart-create-account.md) ismertető cikkben találja.
* **Azure SQL Database**: SQL-adatbázis használata fogadó adattárként. Ha még nem rendelkezik SQL-adatbázissal, a szükséges utasításokat az [SQL-adatbázis létrehozását](../sql-database/sql-database-get-started-portal.md) ismertető cikkben találja.

### <a name="create-a-blob-and-a-sql-table"></a>Blob és SQL-tábla létrehozása

Készítse elő a Blob Storage-ot és az SQL-adatbázist az oktatóanyaghoz a következő lépésekkel.

#### <a name="create-a-source-blob"></a>Forrás blob létrehozása

1. Indítsa el a **Jegyzettömböt**. Másolja be a következő szöveget, és mentse a lemezen egy **inputEmp.txt** nevű fájlba:

    ```
    John|Doe
    Jane|Doe
    ```

1. Hozzon létre egy **adfv2tutorial** nevű tárolót és töltse fel az inputEmp.txt fájlt a tárolóba. A feladatok végrehajtásához használhatja a Azure Portal vagy különféle eszközöket, például a [Azure Storage Explorer](https://storageexplorer.com/) .

#### <a name="create-a-sink-sql-table"></a>Fogadó SQL-tábla létrehozása

1. A következő SQL-szkripttel hozza létre a **dbo.emp** nevű táblát az SQL-adatbázisban:

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

2. Engedélyezze az SQL Server elérését az Azure-szolgáltatások számára. Ellenőrizze, hogy az **Azure-szolgáltatásokhoz való hozzáférés engedélyezése** beállítás engedélyezve van-e az SQL-adatbázist futtató kiszolgálón. Ezzel a beállítással engedélyezheti, hogy a Data Factory adatokat írjon az adatbázispéldányra. A beállítás ellenőrzéséhez és bekapcsolásához nyissa meg az Azure SQL Server > áttekintés > **a**kiszolgáló tűzfalának beállítása > az **Azure-szolgáltatásokhoz való hozzáférés engedélyezése** lehetőséget.

## <a name="create-a-data-factory"></a>data factory létrehozása

1. A bal oldali menüben válassza az **erőforrás** > létrehozása**elemzési** > **Data Factory**:
    
    ![Új adat-előállító létrehozása](./media/doc-common-process/new-azure-data-factory-menu.png)
1. Az **Új adat-előállító** lap **Név** mezőjében adja meg az **ADFTutorialDataFactory** értéket.

    Az adat-előállító nevének _globálisan egyedinek_ kell lennie. A következő hibaüzenet jelenhet meg:
    
    ![Új adat-előállító hibaüzenete](./media/doc-common-process/name-not-available-error.png)

    Ha a név értékével kapcsolatos hibaüzenet kap, adjon meg másik nevet az adat-előállítóhoz. Például: _**sajátneve**_ **ADFTutorialDataFactory**. A Data Factory-összetevők elnevezési szabályait a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.
1. Válassza ki az Azure-**előfizetést** az új adat-előállító létrehozásához.
1. **Erőforráscsoport:** hajtsa végre a következő lépések egyikét:
    
    a. Kattintson a **Meglévő használata** elemre, majd a legördülő listából válasszon egy meglévő erőforráscsoportot.

    b. Kattintson az **Új létrehozása** elemre, és adja meg az erőforráscsoport nevét.
    
    Az erőforráscsoportokkal kapcsolatos információkért tekintse meg az [Erőforráscsoportok használata az Azure-erőforrások kezeléséhez](../azure-resource-manager/resource-group-overview.md) ismertető cikket.

1. A **Verzió** résznél válassza a **V2** értéket.
1. A **Hely** alatt válassza ki az adat-előállító helyét. A legördülő listán csak a támogatott helyek jelennek meg. Az adat-előállítók által használt adattárak (például Azure Storage és SQL Database) és számítási erőforrások (például Azure HDInsight) más helyeken/régiókban is lehetnek.
1. Kattintson a **Létrehozás** gombra.

1. A létrehozás befejezése után a **Data Factory** kezdőlapja jelenik meg.
    
    ![Data factory kezdőlap](./media/doc-common-process/data-factory-home-page.png)
1. Az Azure Data Factory felhasználói felületének (UI) külön lapon történő megnyitásához kattintson a **Tartalomkészítés és monitorozás** csempére.

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Folyamat létrehozása az Adatok másolása eszközzel

1. Az Adatok másolása eszköz elindításához az **első lépéseket** ismertető oldalon kattintson az **Adatok másolása** csempére.

    ![Az Adatok másolása eszköz csempéje](./media/doc-common-process/get-started-page.png)
1. A **Tulajdonságok** lap **Feladat neve** részénél adja meg a következőt: **CopyFromBlobToSqlPipeline**. Ezután kattintson a **Tovább** gombra. A Data Factory felhasználói felülete létrehoz egy folyamatot a megadott feladatnéven.

1. A **Forrásadattár** oldalon hajtsa végre az alábbi lépéseket:

    a. Kapcsolat hozzáadásához kattintson a **+ Új kapcsolat létrehozása** lehetőségre

    b. Válassza ki az **Azure Blob Storage** elemet a katalógusból, majd válassza a **Folytatás**lehetőséget.

    c. Az **Új társított szolgáltatás** lapon válassza ki tárfiókját a **Tárfiók neve** listából, és kattintson a **Befejezés** gombra.

    d. Forrásnak válassza ki az újonnan létrehozott társított szolgáltatást, majd kattintson a **Tovább** gombra.

    ![Forráshoz társított szolgáltatás kiválasztása](./media/tutorial-copy-data-tool/select-source-linked-service.png)

1. **A bemeneti fájl vagy mappa kiválasztása** lapon kövesse az alábbi lépéseket:
    
    a. A **Tallózás** elemre kattintva lépjen az **adfv2tutorial/input** mappához, válassza az **inputEmp.txt** fájlt, majd kattintson a **Kiválasztás** lehetőségre.

    b. A következő lépéshez kattintson a **Tovább** gombra.

1. A **Fájlformátum beállításai** lapon látható, hogy az eszköz automatikusan észleli az oszlopok és sorok határolókaraktereit. Kattintson a **Tovább** gombra. Ezen a lapon emellett megtekintheti az adatok előnézetét, valamint a bemeneti adatok sémáját is.

    ![A Fájlformátum beállításai](./media/tutorial-copy-data-tool/file-format-settings-page.png)
1. A **Céladattár** oldalon hajtsa végre a következő lépéseket:

    a. Kapcsolat hozzáadásához kattintson a **+ Új kapcsolat létrehozása** lehetőségre

    b. Válassza ki **Azure SQL Database** a katalógusból, majd válassza a **Folytatás**lehetőséget.

    c. Az **Új társított szolgáltatás** lapon válassza ki a legördülő listából a kiszolgáló és az adatbázis nevét, adja meg a felhasználónevet és a jelszót, majd kattintson a **Befejezés** gombra.

    ![Azure SQL-adatbázis konfigurálása](./media/tutorial-copy-data-tool/config-azure-sql-db.png)

    d. Válassza ki fogadóként az újonnan létrehozott társított szolgáltatást, majd kattintson a **Tovább** gombra.

    ![Fogadó társított szolgáltatás kiválasztása](./media/tutorial-copy-data-tool/select-sink-linked-service.png)

1. A **Tábla hozzárendelése** oldalon válassza ki a **[dbo].[emp]** táblát, majd kattintson a **Tovább** gombra.

1. A **Séma hozzárendelése** oldalon figyelje meg, hogy a bemeneti fájl első és második oszlopa az **emp** tábla **FirstName** és **LastName** oszlopában képeződik le. Kattintson a **Tovább** gombra.

    ![Séma hozzárendelése oldal](./media/tutorial-copy-data-tool/schema-mapping.png)
1. A **Beállítások** lapon kattintson a **Tovább** gombra.
1. Az **Összefoglalás** lapon tekintse át a beállításokat, majd kattintson a **Tovább** gombra.
1. A folyamat (feladat) figyeléséhez az **Üzembe helyezés** lapon kattintson a **Monitorozás** elemre.
1. Figyelje meg, hogy a bal oldalon található **Figyelés** lap automatikusan ki lesz választva. A tevékenységfuttatási részletek megtekintéséhez és a folyamat ismételt futtatásához használható hivatkozások a **Műveletek** oszlopban találhatók. A lista frissítéséhez kattintson a **Frissítés** gombra.

1. A folyamat futtatásához kapcsolódó tevékenységfuttatások megtekintéséhez kattintson a **Tevékenységfuttatások megtekintése** hivatkozásra a **Műveletek** oszlopban. A másolási művelet részleteinek megtekintéséhez válassza a **Műveletek** oszlop **Részletek** hivatkozását (szemüveg ikon). Ha vissza szeretne térni a folyamat futási nézetéhez, válassza a felső **folyamat futtatási** hivatkozását. A nézet frissítéséhez válassza a **Frissítés** parancsot.

    ![Tevékenységfuttatások monitorozása](./media/tutorial-copy-data-tool/activity-monitoring.png)


1. Ellenőrizze, hogy a rendszer beszúrta-e az adatokat az SQL-adatbázis **emp** táblájába.


1. A szerkesztő módra való váltáshoz kattintson a bal oldalon található **Létrehozás** fülre. A szerkesztővel frissítheti a társított szolgáltatásokat, az adatkészleteket és az eszközzel létrehozott folyamatokat. Az entitások Data Factory felhasználói felületen való szerkesztéséről [a jelen oktatóanyag Azure Portal-verziójában](tutorial-copy-data-portal.md) talál további információt.

## <a name="next-steps"></a>További lépések
A példában szereplő folyamat adatokat másol a Blob Storage-ból egy SQL Database-be. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Folyamat létrehozása az Adatok másolása eszközzel.
> * A folyamat és a tevékenységek futásának monitorozása

A következő oktatóanyagra lépve megismerheti az adatok helyszíni rendszerből felhőre való másolásának folyamatát:

>[!div class="nextstepaction"]
>[Adatok másolása a helyszínről a felhőbe](tutorial-hybrid-copy-data-tool.md)
