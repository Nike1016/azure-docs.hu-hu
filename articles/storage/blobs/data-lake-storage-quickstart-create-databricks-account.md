---
title: 'Gyors útmutató: Azure Data Lake Storage Gen2 adatai elemzése a Azure Databricks használatával | Microsoft Docs'
description: Megtudhatja, hogyan futtathat Spark-feladatokat Azure Databrickson a Azure Portal és egy Azure Data Lake Storage Gen2 Storage-fiók használatával.
author: normesta
ms.author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: quickstart
ms.date: 02/15/2019
ms.reviewer: jeking
ms.openlocfilehash: a1e7ee4f81f2b40b804ee69c8366ca87c377e6ac
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68855499"
---
# <a name="quickstart-analyze-data-in-azure-data-lake-storage-gen2-by-using-azure-databricks"></a>Gyors útmutató: Azure Data Lake Storage Gen2i adatelemzés Azure Databricks használatával

Ebből a rövid útmutatóból megtudhatja, hogyan futtathat egy Apache Spark feladatot a Azure Databricks használatával, hogy elvégezze az elemzést olyan Storage-fiókban tárolt adatokon, amelyek Azure Data Lake Storage Gen2 engedélyezve vannak.

A Spark-feladatok részeként elemezheti a Radio Channel-előfizetési adatokat, így a demográfiai adatok alapján betekintést nyerhet az ingyenes/fizetős használatba.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* Hozzon létre egy Data Lake Gen2 Storage-fiókot. Lásd [: gyors útmutató: Azure Data Lake Storage Gen2 Storage-fiók létrehozása](data-lake-storage-quickstart-create-account.md)

  Illessze be a Storage-fiók nevét egy szövegfájlba. Hamarosan szüksége lesz rá.

* Egyszerű szolgáltatásnév létrehozása. További [információ: A portál használatával létrehozhat egy Azure AD-alkalmazást és egy egyszerű szolgáltatásnevet, amely hozzáférhet](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)az erőforrásokhoz.

  A cikk lépéseinek elvégzése során néhány konkrét dolgot is el kell végeznie.

  :heavy_check_mark: Ha végrehajtja az [alkalmazás szerepkörhöz rendelése](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) szakaszának lépéseit, akkor ügyeljen arra, hogy hozzárendelje a **tárolási blob adatközreműködői** szerepkört az egyszerű szolgáltatáshoz.

  > [!IMPORTANT]
  > Ügyeljen arra, hogy a szerepkört a Data Lake Storage Gen2 Storage-fiók hatókörében rendelje hozzá. Hozzárendelhet egy szerepkört a szülő erőforráscsoporthoz vagy az előfizetéshez, de az engedélyekkel kapcsolatos hibákat addig kapja, amíg a szerepkör-hozzárendelések el nem terjednek a Storage-fiókba.

  :heavy_check_mark: A cikk beléptetési [értékek](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) beolvasása szakaszában szereplő lépések végrehajtásakor illessze be a bérlői azonosítót, az alkalmazás azonosítóját és a jelszó értékeit egy szövegfájlba. Ezekre hamarosan szüksége lesz.

## <a name="create-an-azure-databricks-workspace"></a>Azure Databricks-munkaterület létrehozása

Ebben a szakaszban egy Azure Databricks-munkaterületet fog létrehozni az Azure Portal használatával.

1. Az Azure Portalon válassza az **Erőforrás létrehozása** > **Elemzés** > **Azure Databricks** elemet.

    ![Databricks az Azure Portalon](./media/data-lake-storage-quickstart-create-databricks-account/azure-databricks-on-portal.png "Databricks az Azure Portalon")

2. Az **Azure Databricks szolgáltatás** pontban adja meg az értékeket Databricks-munkaterület létrehozásához.

    ![Azure Databricks-munkaterület létrehozása](./media/data-lake-storage-quickstart-create-databricks-account/create-databricks-workspace.png "Azure Databricks-munkaterület létrehozása")

    Adja meg a következő értékeket:

    |Tulajdonság  |Leírás  |
    |---------|---------|
    |**Munkaterület neve**     | Adja meg a Databricks-munkaterület nevét.        |
    |**Előfizetés**     | Válassza ki a legördülő menüből a saját Azure-előfizetését.        |
    |**Erőforráscsoport**     | Adja meg, hogy új erőforráscsoportot kíván-e létrehozni, vagy egy meglévőt szeretne használni. Az erőforráscsoport egy tároló, amely Azure-megoldásokhoz kapcsolódó erőforrásokat tárol. További információért olvassa el az [Azure-erőforráscsoportok áttekintését](../../azure-resource-manager/resource-group-overview.md). |
    |**Hely**     | Válassza az **USA 2. nyugati régióját**. Nyugodtan választhat egy másik nyilvános régiót is igény szerint.        |
    |**Tarifacsomag**     |  Válassza a **Standard** vagy a **Prémium** előfizetést. További információkért a csomagokkal kapcsolatban tekintse meg a [Databricks díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/databricks/).       |

3. A fiók létrehozása eltarthat néhány percig. A művelet állapotának figyeléséhez tekintse meg a felső folyamatjelző sávot.

4. Válassza a **Rögzítés az irányítópulton**, majd a **Létrehozás** lehetőséget.

## <a name="create-a-spark-cluster-in-databricks"></a>Spark-fürt létrehozása a Databricks használatával

1. Az Azure Portalon lépjen a létrehozott Databricks-munkaterülethez, majd válassza a **Munkaterület indítása** elemet.

2. A rendszer átirányítja az Azure Databricks portáljára. A portálon válassza az **Új** > **Fürt** elemet.

    ![Databricks az Azure-on](./media/data-lake-storage-quickstart-create-databricks-account/databricks-on-azure.png "Databricks az Azure-on")

3. Az **Új fürt** lapon adja meg a fürt létrehozásához szükséges értékeket.

    ![Databricks Spark-fürt létrehozása az Azure-on](./media/data-lake-storage-quickstart-create-databricks-account/create-databricks-spark-cluster.png "Databricks Spark-fürt létrehozása az Azure-on")

    Fogadja el az összes alapértelmezett értéket, kivéve a következőket:

    * Adjon egy nevet a fürtnek.
    * Hozzon létre egy **5,1** futtatókörnyezettel rendelkező fürtöt.
    * Mindenképpen jelölje be a **Leállítás 120 percnyi tétlenség után** jelölőnégyzetet. Adja meg az időtartamot (percben), amelynek elteltével le kell állítani a fürtöt, amennyiben az használaton kívül van.

4. Válassza a **Fürt létrehozása** lehetőséget. Ha a fürt már fut, notebookokat csatlakoztathat hozzá, illetve Spark-feladatokat futtathat.

További információt a fürtök létrehozásáról a [Spark-fürtök az Azure Databricks használatával történő létrehozását](https://docs.azuredatabricks.net/user-guide/clusters/create.html) ismertető szakaszban talál.

## <a name="create-storage-account-file-system"></a>Tárfiók fájlrendszerének létrehozása

Ebben a szakaszban létrehoz egy jegyzetfüzetet az Azure Databricks-munkaterületen, majd kódrészleteket futtat a tárfiók konfigurálásához.

1. Az [Azure Portalon](https://portal.azure.com) lépjen a létrehozott Azure Databricks-munkaterülethez, majd válassza a **Munkaterület indítása** elemet.

2. A bal oldali panelen válassza a **Munkaterület** elemet. A **Munkaterület** legördülő menüből válassza a **Létrehozás** > **Jegyzetfüzet** lehetőséget.

    ![Notebook létrehozása a Databricksben](./media/data-lake-storage-quickstart-create-databricks-account/databricks-create-notebook.png "Notebook létrehozása a Databricksben")

3. A **Jegyzetfüzet létrehozása** párbeszédpanelen adja meg a jegyzetfüzet nevét. Válassza a **Scala** nyelvet, majd válassza ki a korábban létrehozott Spark-fürtöt.

    ![Notebook létrehozása a Databricksben](./media/data-lake-storage-quickstart-create-databricks-account/databricks-notebook-details.png "Notebook létrehozása a Databricksben")

    Kattintson a **Létrehozás** gombra.

4. Másolja és illessze be az alábbi kódrészletet az első cellába, de még ne futtassa ezt a kódot.

   ```scala
   spark.conf.set("fs.azure.account.auth.type.<storage-account-name>.dfs.core.windows.net", "OAuth")
   spark.conf.set("fs.azure.account.oauth.provider.type.<storage-account-name>.dfs.core.windows.net", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
   spark.conf.set("fs.azure.account.oauth2.client.id.<storage-account-name>.dfs.core.windows.net", "<appID>")
   spark.conf.set("fs.azure.account.oauth2.client.secret.<storage-account-name>.dfs.core.windows.net", "<password>")
   spark.conf.set("fs.azure.account.oauth2.client.endpoint.<storage-account-name>.dfs.core.windows.net", "https://login.microsoftonline.com/<tenant-id>/oauth2/token")
   spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "true")
   dbutils.fs.ls("abfss://<file-system-name>@<storage-account-name>.dfs.core.windows.net/")
   spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "false")

   ```

    > [!NOTE]
    > Ez a kódrészlet közvetlenül hozzáfér a Data Lake Gen2-végponthoz a OAuth használatával, de más módokon is csatlakozhat a Databricks-munkaterülethez a Data Lake Storage Gen2-fiókhoz. Például csatlakoztathatja a fájlrendszert a OAuth használatával, vagy közvetlen hozzáférést használhat megosztott kulccsal. <br>A módszerek példáinak megtekintéséhez tekintse meg a [Azure Data Lake Storage Gen2](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html) cikket a Azure Databricks webhelyén.

5. A kód blokkban cserélje le a `storage-account-name` `password`, `appID`,, és `tenant-id` helyőrző értékeit a kódban az egyszerű szolgáltatásnév létrehozásakor összegyűjtött értékekre. Állítsa a `file-system-name` helyőrző értékét arra a névre, amelyet a fájlrendszerhez szeretne adni.

    > [!NOTE]
    > Éles környezetben érdemes megfontolni a hitelesítési kulcs tárolását Azure Databricks-ben. Ezután adjon hozzá egy megkeresési kulcsot a kódjához a hitelesítési kulcs helyett. A rövid útmutató elvégzése után tekintse meg a Azure Databricks webhelyén található [Azure Data Lake Storage Gen2](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html) cikket, ahol megtekintheti a megközelítés példáit.

6. Nyomja le a **SHIFT + ENTER** billentyűkombinációt a kód futtatásához ebben a blokkban.

## <a name="ingest-sample-data"></a>Mintaadatok betöltése

Mielőtt ehhez a szakaszhoz hozzáfogna, a következő előfeltételeknek kell eleget tennie:

Írja be az alábbi kódot egy jegyzetfüzetcellába:

    %sh wget -P /tmp https://raw.githubusercontent.com/Azure/usql/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json

A cellában nyomja le a **SHIFT + ENTER** billentyűkombinációt a kód futtatásához.

Most egy új cellában az alábbi kód megadásával írja be a következő kódot, és cserélje le a zárójelben megjelenő értékeket a korábban használt értékekkel:

    dbutils.fs.cp("file:///tmp/small_radio_json.json", "abfss://<file-system>@<account-name>.dfs.core.windows.net/")

A cellában nyomja le a **SHIFT + ENTER** billentyűkombinációt a kód futtatásához.

## <a name="run-a-spark-sql-job"></a>Spark SQL-feladat futtatása

A következő feladatok végrehajtásával futtathat Spark SQL-feladatot az adatokon.

1. SQL-utasítás futtatásával hozzon létre egy ideiglenes táblát a JSON-mintaadatfájl, a **small_radio_json.json** adataiból. Az alábbi kódrészletben cserélje le a helyőrzőket a fájlrendszer és a tárfiók nevére. A korábban létrehozott jegyzetfüzet segítségével illessze be a kódrészletet a jegyzetfüzet egyik új kódcellájába, majd nyomja le a SHIFT + ENTER billentyűkombinációt.

    ```sql
    %sql
    DROP TABLE IF EXISTS radio_sample_data;
    CREATE TABLE radio_sample_data
    USING json
    OPTIONS (
     path  "abfss://<file-system-name>@<storage-account-name>.dfs.core.windows.net/small_radio_json.json"
    )
    ```

    A parancs sikeres végrehajtása után a JSON-fájl összes adata táblaként jelenik meg a Databricks-fürtben.

    Az `%sql` nyelvi varázsparancs segítségével SQL-kódot futtathat egy másik típusba tartozó notebookról is. További információért olvassa el a [többféle nyelv notebookokban történő használatát](https://docs.azuredatabricks.net/user-guide/notebooks/index.html#mixing-languages-in-a-notebook) ismertető cikket.

2. A futtatott lekérdezés jobb megértéséhez vessünk egy pillantást a JSON-mintafájl adatairól készült pillanatfelvételre. Illessze be a következő kódtöredéket a kódcellába, majd nyomja le a **SHIFT + ENTER** billentyűparancsot.

    ```sql
    %sql
    SELECT * from radio_sample_data
    ```

3. Így egy, az alábbi képernyőképhez hasonló táblázatos kimenet jelenik meg (csak egyes oszlopok láthatók):

    ![JSON-mintaadatok](./media/data-lake-storage-quickstart-create-databricks-account/databricks-sample-csv-data.png "JSON-mintaadatok")

    Egyebek között a mintaadatok rögzítik a rádiós csatorna közönségét (oszlopnév, nem), valamint azt, hogyaz előfizetésük ingyenes vagy fizetős-e (oszlopnév, **szint**).

4. A következőkben vizuálisan jelenítjük meg ezeket az adatokat annak megfelelően, hogy az egyes nemek szerint hány felhasználó rendelkezik ingyenes fiókkal, illetve hányan fizetnek az előfizetésért. A táblázatos kimenet alján kattintson az **Oszlopdiagram** ikonra, majd az **Ábrázolási beállítások** elemre.

    ![Oszlopdiagram létrehozása](./media/data-lake-storage-quickstart-create-databricks-account/create-plots-databricks-notebook.png "Oszlopdiagram létrehozása")

5. A **Ábrázolás testreszabása** lapon húzza az értékeket a megfelelő helyre a képernyőképen látható módon.

    ![Oszlopdiagram testreszabása](./media/data-lake-storage-quickstart-create-databricks-account/databricks-notebook-customize-plot.png "Oszlopdiagram testreszabása")

    - A **Kulcsok** mezőben adja meg a **gender** értéket.
    - Az **Adatsorozat-csoportok** mezőben adja meg a **level** értéket.
    - Az **Értékek** mezőben adja meg a **level** értéket.
    - Az **Összesítés** mezőben adja meg a **COUNT** értéket.

6. Kattintson az **Alkalmaz** gombra.

7. A kimenetben a következő képernyőképen látható vizuális megjelenítés jelenik meg:

     ![Oszlopdiagram testreszabása](./media/data-lake-storage-quickstart-create-databricks-account/databricks-sql-query-output-bar-chart.png "Oszlopdiagram testreszabása")

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült ezzel a cikkel, leállíthatja a fürtöt. Az Azure Databricks-munkaterületen kattintson a **Fürtök** elemre, majd keresse meg a leállítani kívánt fürtöt. Vigye az egérmutatót a **Műveletek** oszlopban a három pont fölé, és kattintson a **Leállítás** ikonra.

![Databricks-fürt leállítása](./media/data-lake-storage-quickstart-create-databricks-account/terminate-databricks-cluster.png "Databricks-fürt leállítása")

Ha nem állítja be manuálisan a fürtöt, az automatikusan leáll, ha a fürt létrehozásakor bejelölte a **megszakítás perc inaktivitás után \_ \_**  jelölőnégyzetet. Ha bejelöli ezt a lehetőséget, a fürt automatikusan leáll, ha a megadott ideig inaktív volt.

## <a name="next-steps"></a>További lépések

Ennek a cikknek a segítségével létrehozott egy Spark-fürtöt az Azure Databricksben, illetve futtatott egy Spark-feladatot a Data Lake Storage Gen2-kompatibilis tárfiók adatainak felhasználásával. A [Spark-adatforrások](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html) áttekintésével azt is megismerheti, hogyan importálhat adatokat más adatforrásokból az Azure Databricksbe. Folytassa a következő cikkel annak megismeréséhez, hogyan végezhet ETL-műveletet (adatok kinyerését, átalakítását és betöltését) az Azure Databricks használatával.

> [!div class="nextstepaction"]
>[Adatok kinyerése, átalakítása és betöltése az Azure Databricks használatával](../../azure-databricks/databricks-extract-load-sql-data-warehouse.md)
