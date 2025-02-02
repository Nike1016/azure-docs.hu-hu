---
title: 'Oktatóanyag: Kinyerési, átalakítási, betöltési (ETL) műveletek végrehajtása az Azure HDInsight Apache Hive használatával'
description: Ebből az oktatóanyagból megtudhatja, hogyan nyerheti ki az adatokat egy nyers CSV-adatkészletből, hogyan alakíthatja át Apache Hive használatával az Azure HDInsight, majd az átalakított adatokat Azure SQL Databaseba tölti be a Sqoop használatával.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 344dddb4e16f23ae40028c090c499d210adb8837
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68855459"
---
# <a name="tutorial-extract-transform-and-load-data-by-using-apache-hive-on-azure-hdinsight"></a>Oktatóanyag: Adatok kinyerése, átalakítása és betöltése az Azure HDInsight Apache Hive használatával

Ebben az oktatóanyagban egy ETL-műveletet hajt végre: adatok kinyerése, átalakítása és betöltése. Létrehoz egy nyers CSV-adatfájlt, importálja egy Azure HDInsight-fürtbe, átalakítja Apache Hive, és betölti egy Azure SQL Database-be az Apache Sqoop.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az adatok kinyerése és feltöltése egy HDInsight-fürtbe.
> * Az adatátalakítás Apache Hive használatával.
> * Az Azure SQL Database-be az Sqoop használatával tölthető be az adatai.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* **A HDInsight konfigurált Azure Data Lake Storage Gen2 Storage-fiók**

    Lásd: [Azure Data Lake Storage Gen2 használata az Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2)-fürtökkel.

* **Linux-alapú Hadoop-fürt a HDInsight-on**

    Lásd [: gyors útmutató: Ismerkedjen meg Apache Hadoop és Apache Hive az Azure HDInsight az Azure Portal](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-linux-create-cluster-get-started-portal)használatával.

* **Azure SQL Database**: Egy Azure SQL-adatbázist használ céladattárként. Ha még nem rendelkezik SQL-adatbázissal, olvassa el az [Azure SQL-adatbázis az Azure Portalon történő létrehozását](../../sql-database/sql-database-get-started.md) ismertető cikket.

* **Azure CLI**: Ha még nem telepítette az Azure CLI-t, tekintse meg [Az Azure CLI telepítését](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)ismertető témakört.

* **Egy Secure Shell-(SSH-) ügyfél**: További információ: [Kapcsolódás a HDInsight (Hadoop) az SSH használatával](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="download-the-flight-data"></a>A repülőjárat-adatok letöltése

1. Tallózással keresse meg a [kutatási és innovatív technológiákkal kapcsolatos adminisztrációt, az Bureau of közlekedési statisztikáit](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time).

2. Az oldalon válassza ki a következő értékeket:

   | Name (Név) | Érték |
   | --- | --- |
   | Filter Year (Szűrési év) |2013 |
   | Filter Period (Szűrési időszak) |January |
   | Mezők |Év, FlightDate, Reporting_Airline, IATA_CODE_Reporting_Airline, Flight_Number_Reporting_Airline, OriginAirportID, forrás, OriginCityName, OriginState, DestAirportID, elpusz, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay. |
   
   Az összes többi mező jelölését törölje.

3. Válassza a **Download** (Letöltés) lehetőséget. Egy .zip fájlt kap, amely a kiválasztott adatmezőket tartalmazza.

## <a name="extract-and-upload-the-data"></a>Adatok kinyerése és feltöltése

Ebben a szakaszban az adatok feltöltése a HDInsight-fürtbe, majd az adatok másolása a Data Lake Storage Gen2-fiókba.

1. Nyisson meg egy parancssort, és a következő biztonságos másolási (SCP-) parancs használatával töltse fel a. zip fájlt a HDInsight-fürt fő csomópontjára:

   ```bash
   scp <file-name>.zip <ssh-user-name>@<cluster-name>-ssh.azurehdinsight.net:<file-name.zip>
   ```

   * Cserélje le `<file-name>` a helyőrzőt a. zip fájl nevére.
   * Cserélje le `<ssh-user-name>` a helyőrzőt a HDInsight-fürt SSH-azonosítójával.
   * Cserélje le `<cluster-name>` a helyőrzőt a HDInsight-fürt nevére.

   Ha az SSH-bejelentkezést egy jelszóval hitelesíti, a rendszer bekéri a jelszót.

   Nyilvános kulcs használatakor lehetséges, hogy az `-i` paramétert kell használnia, és meg kell adnia a megfelelő titkos kulcs elérési útját. Például: `scp -i ~/.ssh/id_rsa <file_name>.zip <user-name>@<cluster-name>-ssh.azurehdinsight.net:`.

2. Ha a feltöltés befejeződött, csatlakozzon a fürthöz az SSH-val. A parancssorban adja meg a következő parancsot:

   ```bash
   ssh <ssh-user-name>@<cluster-name>-ssh.azurehdinsight.net
   ```

3. Használja az alábbi parancsot a .zip fájl kicsomagolásához:

   ```bash
   unzip <file-name>.zip
   ```

   A parancs kibont egy **. csv** fájlt.

4. A Data Lake Storage Gen2 fájlrendszer létrehozásához használja a következő parancsot.

   ```bash
   hadoop fs -D "fs.azure.createRemoteFileSystemDuringInitialization=true" -ls abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/
   ```

   Cserélje le `<file-system-name>` a helyőrzőt arra a névre, amelyet a fájlrendszerben szeretne megadni.

   Cserélje le `<storage-account-name>` a helyőrzőt a Storage-fiók nevére.

5. A következő parancs használatával hozzon létre egy könyvtárat.

   ```bash
   hdfs dfs -mkdir -p abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/data
   ```

6. Az alábbi parancs használatával másolja a *. csv* fájlt a könyvtárba:

   ```bash
   hdfs dfs -put "<file-name>.csv" abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/data/
   ```

   Ha a fájlnév szóközöket vagy speciális karaktereket tartalmaz, használja az idézőjeleket a fájlnév köré.

## <a name="transform-the-data"></a>Az adatok átalakítása

Ebben a szakaszban a Beeline használatával futtat egy Apache Hive feladatot.

A Apache Hive feladatsor részeként importálja az adatait a. csv-fájlból egy **késleltetés**nevű Apache Hive táblába.

1. A HDInsight-fürthöz már meglévő SSH-parancssorból a következő paranccsal hozhat létre és szerkeszthet egy **flightdelays. HQL**nevű új fájlt:

   ```bash
   nano flightdelays.hql
   ```

2. Módosítsa a következő szöveget úgy, hogy `<file-system-name>` lecseréli a és `<storage-account-name>` a helyőrzőket a fájlrendszer és a Storage-fiók nevével. Ezután másolja és illessze be a szöveget a nano Console-ba a SHIFT billentyű lenyomásával és a jobb egérgombos kattintás gomb használatával.

    ```hiveql
    DROP TABLE delays_raw;
    -- Creates an external table over the csv file
    CREATE EXTERNAL TABLE delays_raw (
        YEAR string,
        FL_DATE string,
        UNIQUE_CARRIER string,
        CARRIER string,
        FL_NUM string,
        ORIGIN_AIRPORT_ID string,
        ORIGIN string,
        ORIGIN_CITY_NAME string,
        ORIGIN_CITY_NAME_TEMP string,
        ORIGIN_STATE_ABR string,
        DEST_AIRPORT_ID string,
        DEST string,
        DEST_CITY_NAME string,
        DEST_CITY_NAME_TEMP string,
        DEST_STATE_ABR string,
        DEP_DELAY_NEW float,
        ARR_DELAY_NEW float,
        CARRIER_DELAY float,
        WEATHER_DELAY float,
        NAS_DELAY float,
        SECURITY_DELAY float,
        LATE_AIRCRAFT_DELAY float)
    -- The following lines describe the format and location of the file
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE
    LOCATION 'abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/data';

    -- Drop the delays table if it exists
    DROP TABLE delays;
    -- Create the delays table and populate it with data
    -- pulled in from the CSV file (via the external table defined previously)
    CREATE TABLE delays
    LOCATION 'abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/processed'
    AS
    SELECT YEAR AS year,
        FL_DATE AS flight_date,
        substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier,
        substring(CARRIER, 2, length(CARRIER) -1) AS carrier,
        substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num,
        ORIGIN_AIRPORT_ID AS origin_airport_id,
        substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code,
        substring(ORIGIN_CITY_NAME, 2) AS origin_city_name,
        substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr,
        DEST_AIRPORT_ID AS dest_airport_id,
        substring(DEST, 2, length(DEST) -1) AS dest_airport_code,
        substring(DEST_CITY_NAME,2) AS dest_city_name,
        substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr,
        DEP_DELAY_NEW AS dep_delay_new,
        ARR_DELAY_NEW AS arr_delay_new,
        CARRIER_DELAY AS carrier_delay,
        WEATHER_DELAY AS weather_delay,
        NAS_DELAY AS nas_delay,
        SECURITY_DELAY AS security_delay,
        LATE_AIRCRAFT_DELAY AS late_aircraft_delay
    FROM delays_raw;
    ```

3. Mentse a fájlt a CTRL + X billentyűkombináció használatával, majd írja `Y` be a következőt:.

4. Indítsa el a Hive-ot, és futtassa a **flightdelays.hql** fájlt az alábbi paranccsal:

   ```bash
   beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f flightdelays.hql
   ```

5. Miután a __flightdelays.hql__ szkript lefutott, a következő paranccsal nyisson meg egy interaktív Beeline-munkamenetet:

   ```bash
   beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
   ```

6. Amikor a `jdbc:hive2://localhost:10001/>` parancssor megjelenik, a következő lekérdezéssel nyerhet ki adatokat az importált repülőjárat-késési adatokból:

    ```hiveql
    INSERT OVERWRITE DIRECTORY '/tutorials/flightdelays/output'
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    SELECT regexp_replace(origin_city_name, '''', ''),
        avg(weather_delay)
    FROM delays
    WHERE weather_delay IS NOT NULL
    GROUP BY origin_city_name;
    ```

   Ez a lekérdezés lekéri azon városok listáját, ahol időjárás miatti késések történtek, valamint a késések átlagos idejét, és menti ezeket az adatokat a következő helyen: `abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/output`. Később a Sqoop erről a helyről olvassa be az adatokat, amelyeket exportál az Azure SQL Database-be.

7. A Beeline-ból való kilépéshez írja be a parancssorba a `!quit` parancsot.

## <a name="create-a-sql-database-table"></a>SQL Database-tábla létrehozása

Ehhez a művelethez szüksége lesz az SQL-adatbázis kiszolgálójának nevére. A kiszolgáló nevének megkereséséhez hajtsa végre a következő lépéseket.

1. Nyissa meg az [Azure Portal](https://portal.azure.com).

2. Válassza az **SQL-adatbázisok**lehetőséget.

3. Szűrje a használni kívánt adatbázis nevét. A kiszolgáló neve a **Kiszolgáló neve** oszlopban látható.

4. Szűrje a használni kívánt adatbázis nevét. A kiszolgáló neve a **Kiszolgáló neve** oszlopban látható.

    ![Az Azure SQL-kiszolgáló részleteinek lekérése](./media/data-lake-storage-tutorial-extract-transform-load-hive/get-azure-sql-server-details.png "Az Azure SQL-kiszolgáló részleteinek lekérése")

    Számos módon csatlakozhat az SQL Database-hez, majd hozhat létre egy táblát. A következő lépések során a [FreeTDS](https://www.freetds.org/) eszközt használjuk a HDInsight-fürtről.

5. A FreeTDS telepítéséhez használja a következő parancsot egy, a fürthöz csatlakozó SSH-kapcsolaton:

   ```bash
   sudo apt-get --assume-yes install freetds-dev freetds-bin
   ```

6. A telepítés befejezése után a következő parancs használatával csatlakozhat a SQL Database-kiszolgálóhoz.

   ```bash
   TDSVER=8.0 tsql -H '<server-name>.database.windows.net' -U '<admin-login>' -p 1433 -D '<database-name>'
    ```
   * Cserélje le `<server-name>` a helyőrzőt a SQL Database-kiszolgáló nevére.

   * Cserélje le `<admin-login>` a helyőrzőt SQL Database rendszergazdai bejelentkezési azonosítójával.

   * Cserélje le `<database-name>` a helyőrzőt az adatbázis nevére.

   Amikor a rendszer kéri, adja meg a SQL Database rendszergazdai bejelentkezéshez használt jelszót.

   A kimenet a következő szöveghez fog hasonlítani:

   ```
   locale is "en_US.UTF-8"
   locale charset is "UTF-8"
   using default charset "UTF-8"
   Default database being set to sqooptest
   1>
   ```

7. A `1>` parancssorba írja be a következő utasításokat:

   ```hiveql
   CREATE TABLE [dbo].[delays](
   [OriginCityName] [nvarchar](50) NOT NULL,
   [WeatherDelay] float,
   CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED
   ([OriginCityName] ASC))
   GO
   ```

8. A `GO` utasítás megadásakor a rendszer kiértékeli az előző utasításokat.

   A lekérdezés egy késések nevűtáblát hoz létre, amely fürtözött indexszel rendelkezik.

9. A következő lekérdezés használatával ellenőrizheti, hogy a tábla létrejött-e:

   ```hiveql
   SELECT * FROM information_schema.tables
   GO
   ```

   A kimenet az alábbi szöveghez hasonló:

   ```
   TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
   databaseName       dbo             delays        BASE TABLE
   ```

10. A tsql eszközből való kilépéshez írja be az `exit` kifejezést az `1>` parancssorba.

## <a name="export-and-load-the-data"></a>Az adatexportálás és-betöltés

Az előző részekben a helyről `abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/output`másolta az átalakított adatterületet. Ebben a szakaszban a Sqoop használatával exportálja az adatait `abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/output` az Azure SQL Database-ben létrehozott táblába.

1. A következő paranccsal ellenőrizze, hogy a Sqoop látja-e az SQL-adatbázist:

   ```bash
   sqoop list-databases --connect jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433 --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD>
   ```

   A parancs az adatbázisok listáját adja vissza, beleértve azt az adatbázist, amelyben létrehozta a késések táblát.

2. A következő paranccsal exportálhatja az adatait a **hivesampletable** táblából a **késések** táblájába:

   ```bash
   sqoop export --connect 'jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433;database=<DATABASE_NAME>' --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD> --table 'delays' --export-dir 'abfs://<file-system-name>@.dfs.core.windows.net/tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1
   ```

   A Sqoop a **késések** táblát tartalmazó adatbázishoz csatlakozik, és a `/tutorials/flightdelays/output` címtárból exportálja az adatait a **késések** táblájába.

3. A `sqoop` parancs befejeződése után a TSQL segédprogrammal csatlakozhat az adatbázishoz:

   ```bash
   TDSVER=8.0 tsql -H <SERVER_NAME>.database.windows.net -U <ADMIN_LOGIN> -P <ADMIN_PASSWORD> -p 1433 -D <DATABASE_NAME>
   ```

4. Az alábbi utasítások segítségével ellenőrizheti, hogy az adatokat a **késések** táblába exportálta-e:

   ```sql
   SELECT * FROM delays
   GO
   ```

   A táblában látnia kell az adatok listáját. A tábla a városok nevét és az egyes városokhoz tartozó átlagos késések idejét tartalmazza.

5. A `exit` TSQL segédprogramból való kilépéshez írja be a parancsot.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az oktatóanyagban használt összes erőforrás már létezik. Nincs szükség karbantartásra.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a HDInsight található adatkezelési lehetőségekről, tekintse meg a következő cikket:

> [!div class="nextstepaction"]
> [Az Azure Data Lake Storage Gen2 használata Azure HDInsight-fürtökkel](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
