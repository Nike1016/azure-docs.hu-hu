---
title: Harmadik féltől származó alkalmazások telepítése Azure HDInsight
description: Megismerheti, hogyan telepíthet külső gyártótól származó Hadoop-alkalmazásokat az Azure HDInsighton.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: hrasheed
ms.openlocfilehash: 38b37b45964dd724b18f22a478be0d85a7036ce7
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946966"
---
# <a name="install-third-party-apache-hadoop-applications-on-azure-hdinsight"></a>Az Azure HDInsight külső Apache Hadoop-alkalmazások telepítése

Ismerje meg, hogyan telepítheti egy harmadik fél [Apache Hadoop](https://hadoop.apache.org/) alkalmazás az Azure HDInsight a. A saját alkalmazások telepítéséről az [Egyéni HDInsight-alkalmazások telepítése](hdinsight-apps-install-custom-applications.md) című cikk tartalmaz útmutatást.

Egy HDInsight-alkalmazás olyan alkalmazás, amely a felhasználók telepíthetik az HDInsight-fürtön. Ezek az alkalmazások lehetnek a Microsoft, független szoftvergyártók (ISV-k) vagy a felhasználók fejlesztései.  

Az alábbi listában a közzétett alkalmazást mutatja be:

|Alkalmazás |Fürt típusa (i) | Leírás |
|---|---|---|
|[AtScale Intelligence platform](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/atscale.atscale) |Hadoop |A AtScale bekapcsolja a HDInsight-fürtöt egy kibővített OLAP-kiszolgálóra, amely lehetővé teszi, hogy több milliárd sornyi adatsort interaktív módon tudjon lekérdezni a már ismert BI-eszközökkel, a saját és a szeretettel – a Microsoft Excel, a Power BI, a tabló szoftver és a QlikView használatával. |
|[CDAP a HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/cask.cdap-for-hdinsight) |HBase |A CDAP az első olyan egységesített integrációs big data platform, amely felgyorsítja a Hadoop értékét, és lehetővé teszi az informatikai szolgáltatások számára az önkiszolgáló adattárolást. A nyílt forráskód és a bővíthető CDAP eltávolítja az innováció akadályait. Követelmények: 4 régiós csomópont, min D3 v2. |
|[Datameer](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/datameer.datameer) |Hadoop |A Datameer önkiszolgáló méretezhető platformja az adatok előkészítésére, feltárására és szabályozására, valamint az összetett többforrású adatok értékes, üzleti használatra kész információkba való bekapcsolására, gyorsabb és intelligensebb elemzéseket tesz lehetővé nagyvállalati szinten. |
|[Dataiku DSS a HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/dataiku.dss-on-hdi) |Hadoop, Spark |Dataiku DSS egy nagyvállalati adatelemzési platformon, amely lehetővé teszi az adatszakértők és az adatelemzők számára, hogy hatékonyabban tervezzék meg és futtassák az új adattermékeket és-szolgáltatásokat, a nyers adatelemzést pedig hatásos előrejelzésekként |
|[WANdisco Fusion HDI-alkalmazás](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/wandisco.fusion-hdi-app) |Hadoop, Spark, HBase, Storm, Kafka |Az adatok konzisztens elosztása egy elosztott környezetben nagy mennyiségű adatműveleti kérdés. A WANdisco Fusion egy nagyvállalati szintű szoftver, amely a strukturálatlan adatkonzisztencia bármely környezetbe való bekapcsolásával oldja meg ezt a problémát. |
|[H2O SparklingWater a HDInsight-hez](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/h2o-ai.h2o-sparklingwater) |Spark |A H2O pezsgős víz a következő elosztott algoritmusokat támogatja: GLM, naiv Bayes, elosztott véletlenszerű erdő, gradiens-növelő gép, mély neurális hálózatok, mély tanulás, K-means, PCA, általánosított, alacsony rangú modellek, anomáliák észlelése, autokódolók. |
|[Striim a valós idejű adatintegrációról a HDInsight-be](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/striim.hdinsightintegration) |Hadoop, HBase, Storm, Spark, Kafka |A Striim ("Stream") egy végpontok közötti streaming adatintegrációs és intelligencia platform, amely lehetővé teszi a különböző adatfolyamok folyamatos betöltését, feldolgozását és elemzését. |
|[Jumbune Enterprise – gyorsuló BigData-elemzés](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/impetus-infotech-india-pvt-ltd.impetus_jumbune) |Hadoop, Spark |A Jumbune magas szinten segíti a vállalatok számára az 1. Felgyorsult a TEZ, a MapReduce & a Spark motoron alapuló struktúra, a Java, a Scala számítási feladatok teljesítménye. 2. Proaktív Hadoop-fürt figyelése, 3. Az adatminőség-kezelés létrehozása az elosztott fájlrendszerben. |
|[Kyligence Enterprise](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/kyligence.kyligence) |Hadoop, HBase, Spark |Az Apache Kylin által működtetett Kyligence Enterprise lehetővé teszi a BI-t Big-alapú adatvédelemben. A Hadoop vállalati OLAP-motorja, a Kyligence Enterprise az iparági szabványoknak megfelelő adattárházat és BI-módszertant is lehetővé teszi az üzleti elemzők számára a Hadoop-beli építészek számára. |
|[Starburst Presto az Azure HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/starburst.starburst-presto) |Hadoop |A Presto egy gyors és skálázható elosztott SQL-lekérdezési motor. Tárolási és számítási szétválasztása lett tervezve, Presto tökéletes megoldás az adatok az Azure Data Lake Storage, Azure Blob Storage, SQL és NoSQL-adatbázisok és más adatforrásokhoz. |
|[StreamSets-adatgyűjtő a HDInsight-felhőhöz](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/streamsets.streamsets-data-collector-hdinsight) |Hadoop, HBase, Spark, Kafka |A StreamSets adatgyűjtő egy egyszerű, nagy teljesítményű motor, amely valós időben továbbítja az adatstreameket. Az adatstreamek adatait az adatgyűjtő használatával irányíthatja és dolgozza fel. Egy 30 napos próbaverziós licenccel rendelkezik. |
|[Trifacta Wrangler vállalat](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/trifacta.tr01) |Hadoop, Spark, HBase |A Trifacta Wrangler Enterprise for HDInsight minden adatskála esetében támogatja a nagyvállalati szintű adatmennyiség-huzavona. A Trifacta Azure-on való futtatásának költsége a Trifacta-előfizetés költségeinek és a virtuális gépek Azure-infrastruktúra-költségeinek a kombinációja. |
|[Az egységesített adatplatform](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/unifi-software.unifi-data-catalog) |Hadoop,HBase,Storm,Spark |Az egységesített adatplatform az önkiszolgáló Adateszközök zökkenőmentesen integrált csomagja, amely lehetővé teszi az üzleti felhasználó számára a növekményes bevételt, a költségek csökkentését és a működési komplexitást eredményező adatkihívások kezelését. |
|[Unraveldata APM](https://azuremarketplace.microsoft.com/marketplace/apps/unravel-data.unravel-app) |Spark |A HDInsight Spark-fürthöz tartozó adatalkalmazás felbomlik. |
|[A vízvonal MESTERSÉGESen vezérelt Data Catalog](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/waterline_data.waterline_data) |Spark |Az adatközpont-katalógusok, rendszerezések és az azokra vonatkozó információk a mesterséges intelligenciával az üzleti feltételekkel való automatikus címkézés érdekében. Üzleti literate katalógus vízvonallal a része a kritikus fontosságú, sikeres önkiszolgáló elemzési, megfelelőségi és szabályozási és informatikai kezdeményezések előrehaladását. |

A cikkben szereplő utasítások az Azure Portalon alapulnak. Az Azure Resource Manager-sablonok exportálása a portálról vagy a Resource Manager-sablon másolatának beszerzése szállítóktól származó, és a sablon üzembe helyezése az Azure PowerShell és a klasszikus Azure CLI használatával.  Lásd: [Apache Hadoop-fürtök létrehozása a Resource Manager-sablonok használatával HDInsight](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

## <a name="prerequisites"></a>Előfeltételek
Ha egy meglévő HDInsight-fürtre kíván HDInsight-alkalmazásokat telepíteni, HDInsight-fürttel kell rendelkeznie. A fürt létrehozását lásd: [Fürtök létrehozása](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster). HDInsight-alkalmazásokat HDInsight-fürt létrehozása közben is telepíthet.

## <a name="install-applications-to-existing-clusters"></a>Alkalmazások telepítése a meglévő fürtökre
Az alábbi eljárásból megtudhatja, hogyan telepíthet HDInsight-alkalmazásokat egy meglévő HDInsight-fürtbe.

**Egy HDInsight-alkalmazás telepítése**

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldali menüben navigáljon az **összes szolgáltatás** > **Analytics** > **HDInsight**-fürthöz.
3. Válasszon ki egy HDInsight-fürtöt a listából.  Ha még nincs ilyen fürtje, hozzon létre egyet most.  Lásd: [Fürtök létrehozása](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).
4. A **Beállítások** kategóriában válassza az **alkalmazások**lehetőséget. A fő ablakban megtekintheti a telepített alkalmazások listáját. 
   
    ![HDInsight-alkalmazások menü a portálon](./media/hdinsight-apps-install-applications/hdinsight-apps-portal-menu.png)
5. Válassza a **+ Hozzáadás** lehetőséget a menüből. Megtekintheti az elérhető alkalmazások listáját.  Ha a **+ Hozzáadás** szürkén jelenik meg, az azt jelenti, hogy a HDInsight-fürt ezen verziójához nem tartoznak alkalmazások.
   
    ![HDInsight-alkalmazások, elérhető alkalmazások](./media/hdinsight-apps-install-applications/hdinsight-apps-list.png)
6. Válasszon ki egyet az elérhető alkalmazások közül, majd kövesse az utasításokat a jogi feltételek elfogadásához.

A telepítési állapotot a portál értesítései között tekintheti meg (a portál tetején található harang ikonra kattintva). Az alkalmazás telepítése után az alkalmazás megjelenik a telepített alkalmazások listájában.

## <a name="install-applications-during-cluster-creation"></a>Alkalmazások telepítése fürtlétrehozás során
HDInsight-alkalmazásokat fürt létrehozása közben is telepíthet. A HDInsight-alkalmazásokat azt követően telepíti a rendszer, hogy a fürt létrejött, és futó állapotba került. Ha a Azure Portal használatával kívánja telepíteni az alkalmazásokat a fürt létrehozása során, az alapértelmezett **gyors létrehozás** lehetőség helyett az **Egyéni** lehetőséget kell használnia.

## <a name="list-installed-hdinsight-apps-and-properties"></a>Telepített HDInsight-alkalmazások és az alkalmazástulajdonságok listázása
A portál megjeleníti a fürthöz tartozó telepített HDInsight-alkalmazásokat, valamint az egyes telepített alkalmazásokhoz tartozó tulajdonságokat.

**HDInsight-alkalmazások listázása és az alkalmazástulajdonságok megjelenítése**

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldali menüben navigáljon az **összes szolgáltatás** > **Analytics** > **HDInsight**-fürthöz.
3. Válasszon ki egy HDInsight-fürtöt a listából.
4. A **Beállítások** kategóriában válassza az **alkalmazások**lehetőséget. A fő ablakban megtekintheti a telepített alkalmazások listáját. 
   
    ![HDInsight-alkalmazások, telepített alkalmazások](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps-with-apps.png)
5. Válassza ki az egyik telepített alkalmazást a tulajdonság megjelenítéséhez. A tulajdonság tartalmazza:

    |Tulajdonság | Leírás |
    |---|---|
    |App neve |Az alkalmazás neve. |
    |Állapot |Az alkalmazás állapota. |
    |Webpage |A peremhálózati csomóponton üzembe helyezett webalkalmazás URL-címe. A hitelesítő adatok ugyanazok, mint amelyeket a fürthöz beállított HTTP-felhasználónál használt. |
    |SSH-végpont |Az SSH használatával csatlakozhat a peremhálózati csomóponthoz. Az SSH hitelesítő adatai ugyanazok, mint amelyeket a fürthöz beállított SSH-felhasználónál használt. További információk: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md). |
    |Leírás | Az alkalmazás leírása. |

6. Egy alkalmazás törléséhez kattintson a jobb gombbal az alkalmazást, és kattintson **törlése** a helyi menüből.

## <a name="connect-to-the-edge-node"></a>Csatlakozás az élcsomóponthoz
Az élcsomóponthoz HTTP és SSH segítségével csatlakozhat. A végpont adatait megtalálja a [portálon](#list-installed-hdinsight-apps-and-properties). További információk: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

A HTTP végpont hitelesítő adatai ugyanazok, mint amelyeket a HDInsight-fürthöz beállított HTTP-felhasználónál használt; az SSH-végpont hitelesítő adatai ugyanazok, mint amelyeket a HDInsight-fürthöz beállított SSH-felhasználónál használt.

## <a name="troubleshoot"></a>Hibaelhárítás
Lásd: [A telepítési folyamat hibaelhárítása](hdinsight-apps-install-custom-applications.md#troubleshoot-the-installation).

## <a name="next-steps"></a>További lépések
* [Egyéni HDInsight-alkalmazások telepítése](hdinsight-apps-install-custom-applications.md): útmutató HDInsight közzé nem tett HDInsight-alkalmazás üzembe helyezése.
* [HDInsight-alkalmazások közzététele](hdinsight-apps-publish-applications.md): Ismerje meg, hogyan teheti közzé egyéni HDInsight-alkalmazásait az Azure Marketplace-en.
* [MSDN HDInsight-alkalmazás](https://msdn.microsoft.com/library/mt706515.aspx)telepítése: Megtudhatja, hogyan definiálhat HDInsight-alkalmazásokat.
* [Customize Linux-based HDInsight clusters using Script Action](hdinsight-hadoop-customize-cluster-linux.md) (Linux-alapú HDInsight-fürtök testreszabása parancsfájlműveletek segítségével): megtudhatja, hogyan telepíthet további alkalmazásokat parancsfájlműveletek használatával.
* [Linux-alapú Apache Hadoop-fürtök létrehozása a Resource Manager-sablonok használatával HDInsight](hdinsight-hadoop-create-linux-clusters-arm-templates.md): ismerje meg, hogyan hívhat meg Resource Manager-sablonokat HDInsight-fürtök létrehozásához.
* [Use empty edge nodes in HDInsight](hdinsight-apps-use-edge-node.md) (Üres élcsomópontok használata a HDInsightban): a cikk bemutatja, hogyan lehet üres élcsomópontot használni egy HDInsight-fürt elérésére, HDInsight-alkalmazások tesztelésére és HDInsight-alkalmazások üzemeltetésére.

