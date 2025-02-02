---
title: A Power bi-ban – Azure HDInsight az Apache Hive-adatok megjelenítése
description: Ismerje meg, hogyan dolgozza fel az Azure HDInsight Hive-adatok vizualizálása a Microsoft Power BI használatával.
keywords: a hdinsight hadoop-, hive, interaktív lekérdezés, interaktív hive, LLAP, odbc
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: hrasheed
ms.openlocfilehash: 69353968f6b38f0d16b68c58b9b00c3e6d45850b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446877"
---
# <a name="visualize-apache-hive-data-with-microsoft-power-bi-using-odbc-in-azure-hdinsight"></a>A Microsoft Power BI ODBC segítségével az Azure HDInsight az Apache Hive-adatok megjelenítése

Ismerje meg, hogyan csatlakozhat Azure HDInsight ODBC segítségével a Microsoft Power BI Desktop és az Apache Hive-adatok megjelenítése.

>[!IMPORTANT]
> Kihasználhatja a Hive ODBC-illesztőprogram importálása a Power BI Desktop általános ODBC-összekötő használatával. Azonban nem ajánlott a Hive-lekérdezési motor nem interaktív természeténél BI számítási feladatokhoz. [HDInsight interaktív lekérdezés összekötője](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md) és [HDInsight Spark-összekötő](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect) a teljesítményük jobb döntéseket.

Ebből a cikkből származó adatokat betölteni egy `hivesampletable` Hive táblát a Power bi-bA. A Hive-tábla tartalmaz néhány mobiltelefon-használati adatokat. Ezután a használati adatok a világ térképen jeleníti meg:

![HDInsight Power bi-ban a térkép jelentésbe](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-visualization.png)

Az információk az új [interaktív lekérdezés](../interactive-query/apache-interactive-query-get-started.md) fürt típusa. HDInsight interaktív lekérdezés közvetlen lekérdezés használatával csatlakozhat, olvassa el a [adatok interaktív lekérdezéses Hive jelenítheti meg a közvetlen lekérdezés használatával Azure HDInsight a Microsoft Power BI](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).

## <a name="prerequisites"></a>Előfeltételek

Mielőtt végrehajtaná ezt a cikket, a következőkkel kell rendelkeznie:

* **HDInsight-fürt**. A fürt vagy a Hive használatával egy HDInsight-fürtöt, vagy egy újonnan kiadott interaktív lekérdezési fürt lehet. Fürtök létrehozása, lásd: [fürt létrehozása](apache-hadoop-linux-tutorial-get-started.md#create-cluster).

* **[A Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)** . Letöltheti a másolatot a [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=45331).

## <a name="create-hive-odbc-data-source"></a>Hive ODBC-adatforrás létrehozása

Lásd: [létrehozása Hive ODBC-adatforrás](apache-hadoop-connect-excel-hive-odbc-driver.md#create-apache-hive-odbc-data-source).

## <a name="load-data-from-hdinsight"></a>A HDInsight adatok betöltése

A hivesampletable Hive-tábla összes HDInsight-fürtöket tartalmaz.

1. Indítsa el a Power BI Desktopban.

2. A felső menüben keresse meg **kezdőlap** > **adatok lekérése** > **több...** .

    ![Nyissa meg HDInsight Power BI-adatok](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-open-odbc.png)

3. Az a **adatok lekérése** párbeszédpanelen válassza **más** válassza a bal oldali **ODBC** , jobb oldalról, majd **Connect** alján.

4. Az a **származó ODBC** párbeszédpanelen válassza ki az adatokat a forrás nevét a legördülő listából az utolsó szakaszban létrehozott, és válassza ki **OK**.

5. Az a **kezelő** párbeszédpanelen bontsa ki a **ODBC > HIVE-> alapértelmezett**, jelölje be **hivesampletable**, majd válassza ki **terhelés**.

6. Az a **ODBC-illesztő** párbeszédablakban válassza **alapértelmezett vagy egyéni**, majd **Connect**.

## <a name="visualize-data"></a>Adatok vizualizációja

Továbbra is az előző eljárást.

1. A megjelenítések ablaktáblán válassza ki a **térkép**.  Egy földgömb ikon.

    ![HDInsight Power bi-ban a jelentés személyre szabható.](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-customize.png)
2. Az a **mezők** ablaktáblán válassza előbb **ország** és **devicemake**. Láthatja, hogy a térképen ábrázolt adatok.
3. Bontsa ki a térképet.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan HDInsight használata a Power BI adatok vizualizálásához.  További tudnivalókért tekintse meg a következő cikkeket:

* [Az Apache a Zeppelin használata Azure HDInsight az Apache Hive-lekérdezések futtatásához](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Excel csatlakoztatása a Microsoft Hive ODBC illesztőprogram segítségével a HDInsight](./apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Excel Power Query használatával csatlakozni az Apache Hadoop](apache-hadoop-connect-excel-power-query.md).
* [Csatlakozás az Azure HDInsight és a Data Lake Tools for Visual Studio használatával, az Apache Hive-lekérdezések futtatása](apache-hadoop-visual-studio-tools-get-started.md).
* [Az Azure HDInsight-eszköz használata a Visual Studio Code](../hdinsight-for-vscode.md).
* [Upload Data to HDInsight](./../hdinsight-upload-data.md).
