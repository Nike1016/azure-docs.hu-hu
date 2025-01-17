---
title: 'Gyors útmutató: Apache Hive-lekérdezések végrehajtása az Azure HDInsight – Apache Zeppelin'
description: Ez a rövid útmutatóban megismerheti, hogyan futtathat Apache Hive-lekérdezések a Apache Zeppelin használatával.
keywords: hdinsight, hadoop, hive, LLAP interaktív lekérdezés
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: quickstart
ms.date: 05/06/2019
ms.author: hrasheed
ms.openlocfilehash: 1642c64b0b14c2e290aad689399b59d896660a28
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67056696"
---
# <a name="quickstart-execute-apache-hive-queries-in-azure-hdinsight-with-apache-zeppelin"></a>Gyors útmutató: Apache Hive-lekérdezések végrehajtása az Azure HDInsight az Apache Zeppelin

Ez a rövid útmutatóban megismerheti, hogyan futtathat Apache Zeppelin használatával [Apache Hive](https://hive.apache.org/) lekérdezések az Azure HDInsight. HDInsight interaktív lekérdezési fürtökhöz tartalmaznak [Apache Zeppelin](https://zeppelin.apache.org/) notebookok, amelyek segítségével futtassa az interaktív Hive-lekérdezések.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Egy HDInsight interaktív lekérdezési fürtöt. Lásd: [fürt létrehozása](../hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster) hozhat létre HDInsight-fürtöt.  Ügyeljen arra, hogy válassza ki a **interaktív lekérdezés** fürt típusa.

## <a name="create-an-apache-zeppelin-note"></a>Hozzon létre egy Apache Zeppelin Megjegyzés

1. Cserélje le `CLUSTERNAME` a következő URL-címet a fürt nevével `https://CLUSTERNAME.azurehdinsight.net/zeppelin`. Ezután írja be az URL-CÍMÉT egy webböngészőben.

2. Adja meg a fürt bejelentkezési felhasználónevét és jelszavát. A Zeppelin lapon hozzon létre egy új megjegyzés, vagy nyissa meg a meglévő megjegyzéseket. **HiveSample** bizonyos Hive-mintalekérdezések tartalmazza.  

    ![HDInsight interaktív lekérdezés zeppelin](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin.png)

3. Válassza ki **létrehozása új feljegyzés**.

4. Az a **létrehozása új feljegyzés** párbeszédpanelen adja meg vagy válassza ki a következő értékeket:

    - Megjegyzés: név: Adja meg a jegyzet nevét.
    - Alapértelmezett interpretu: Válassza ki **jdbc** a legördülő listából.

5. Válassza ki **jegyzet létrehozása**.

6. A kód szakaszban írja be a következő Hive-lekérdezés, és nyomja le az **Shift + Enter**:

    ```hive
    %jdbc(hive)
    show tables
    ```

    ![HDInsight interaktív lekérdezés zeppelin lekérdezés futtatása](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin-query.png)

    A **%jdbc(hive)** első sorában utasítás arra utasítja a notebook használata a Hive JDBC értelmezője számára készült.

    A lekérdezés egy Hive-tábla neve visszaküldik **hivesampletable**.

    Az alábbiakban két további Hive-lekérdezések, amelyek vonatkozóan futtathat le **hivesampletable**:

    ```hive
    %jdbc(hive)
    select * from hivesampletable limit 10

    %jdbc(hive)
    select ${group_name}, count(*) as total_count
    from hivesampletable
    group by ${group_name=market,market|deviceplatform|devicemake}
    limit ${total_count=10}
    ```

    Mint a hagyományos Hive, a lekérdezés eredményeinek térjen vissza kell gyorsabban.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Miután végzett a gyors üzembe helyezéssel, érdemes törölni a fürtöt. A HDInsight az Azure Storage szolgáltatásban tárolja az adatokat, így biztonságosan törölhet olyan fürtöket, amelyek nincsenek használatban. Ráadásul a HDInsight-fürtök akkor is díjkötelesek, amikor éppen nincsenek használatban. Mivel a fürt költsége a sokszorosa a tároló költségeinek, gazdaságossági szempontból is ésszerű törölni a használaton kívüli fürtöket.

Törölje a fürtöt, tekintse meg a [törlése egy HDInsight-fürtön a böngészőben, a PowerShell vagy az Azure CLI](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>További lépések

Ebből a gyors útmutatóból megtudhatta, hogyan Apache Zeppelin használata Azure HDInsight az Apache Hive-lekérdezések futtatásához. További információ a Hive-lekérdezések, a következő cikk bemutatja, hogyan hajthat végre lekérdezéseket a Visual Studio használatával.

> [!div class="nextstepaction"]
> [Csatlakozás az Azure HDInsight és a Data Lake Tools for Visual Studio használatával, az Apache Hive-lekérdezések futtatása](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)
