---
title: HDInsight – az Azure Machine Learning szolgáltatások a fürt kezelése
description: Ismerje meg, hogyan kezelheti egy Machine Learning-szolgáltatások-fürtöt az Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/19/2019
ms.openlocfilehash: fa838f371607f3c0b0f76f81d6755c842a5901f7
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448961"
---
# <a name="manage-ml-services-cluster-on-azure-hdinsight"></a>Machine Learning-szolgáltatások az Azure HDInsight-fürt kezelése

Ebből a cikkből megismerheti, hogyan kezelheti a Machine Learning Services meglévő fürt az Azure HDInsight végrehajtani a feladatokat, mint a több egyidejű felhasználók hozzáadása, egy gépi Tanulási szolgáltatások fürthöz való távoli csatlakozást számítási környezet módosítása stb.

## <a name="prerequisites"></a>Előfeltételek

* Egy Machine Learning-szolgáltatások HDInsight fürt. Lásd: [Apache Hadoop-fürtök létrehozása az Azure portal használatával](../hdinsight-hadoop-create-linux-clusters-portal.md) válassza **Machine Learning-szolgáltatások** a **fürt típusa**.


* A Secure Shell (SSH) ügyfél: Egy SSH-ügyféllel távolról csatlakozhat a HDInsight-fürthöz, és közvetlenül a fürtön futtasson parancsokat szolgál. További információkért lásd: [az SSH használata a HDInsight.](../hdinsight-hadoop-linux-use-ssh-unix.md).


## <a name="enable-multiple-concurrent-users"></a>Több párhuzamos felhasználó engedélyezése

A Machine Learning-szolgáltatások HDInsight-fürt több párhuzamos felhasználó számára az élcsomóponthoz, amelyen az RStudio közösségi verziója fut. további felhasználók hozzáadásával engedélyezheti. HDInsight-fürt létrehozásakor két felhasználót kell megadnia, egy HTTP-felhasználót és egy SSH-felhasználót:

![1\. párhuzamos felhasználó](./media/r-server-hdinsight-manage/concurrent-users-1.png)

- **Fürt bejelentkezési felhasználóneve**: HTTP-felhasználó a létrehozott HDInsight-fürtöket védő HDInsight-átjárón át történő hitelesítéshez. A HTTP-felhasználó használatos az Apache Ambari felhasználói felületén, Apache Hadoop YARN felhasználói felületén, valamint más Felhasználóifelület-összetevőknek eléréséhez.
- **Secure Shell- (SSH-) felhasználónév**: SSH-felhasználó, aki a fürtöt biztonságos felületen keresztül éri el. Ez a felhasználó a Linux rendszerben az összes főcsomópont, munkavégző csomópont és élcsomópont felhasználója. Így Secure Shellt használhat a távoli fürt bármely csomópontjának elérésére.

Az a Machine Learning-szolgáltatások HDInsight-fürt használt R Studio Server Community verziója a mechanizmus során csak Linux-felhasználónevet és jelszót fogad el. Nem támogatja a jogkivonatok átadását. Ezért amikor megpróbálja elérni az R Studio először egy Machine Learning-szolgáltatások-fürtön, kell kétszer bejelentkezni.

- Először jelentkezzen be a HTTP felhasználói hitelesítő adatokkal a HDInsight-átjárón. 

- Az SSH-felhasználói hitelesítő adatok használatával az Rstudióba való bejelentkezéshez.
  
Jelenleg HDInsight-fürt üzembe helyezésekor csak egy SSH felhasználói fiókot lehet létrehozni. Így a Machine Learning-szolgáltatások HDInsight-fürt eléréséhez több felhasználó engedélyezéshez kell hozhat létre további felhasználókat a Linux rendszerben.

A fürt határcsomópontjához az RStudio fut, mert több lépésből áll itt:

1. A meglévő SSH-felhasználó használata az élcsomópontra való bejelentkezéshez
2. További Linux-felhasználók hozzáadása az élcsomópontban
3. Az RStudio Community verziójának használata a létrehozott felhasználóval

### <a name="step-1-use-the-created-ssh-user-to-sign-in-to-the-edge-node"></a>1\. lépés: A létrehozott SSH-felhasználó használata az élcsomópontra való bejelentkezéshez

Kövesse az utasításokat, [HDInsight (az Apache Hadoop) SSH-val csatlakozhat](../hdinsight-hadoop-linux-use-ssh-unix.md) az élcsomópont eléréséhez. A Machine Learning-szolgáltatások HDInsight-fürt az élcsomópont címe a következő `CLUSTERNAME-ed-ssh.azurehdinsight.net`.

### <a name="step-2-add-more-linux-users-in-edge-node"></a>2\. lépés: További Linux-felhasználók hozzáadása az élcsomópontban

Felhasználó élcsomóponthoz adásához hajtsa végre a következő parancsokat:

    # Add a user 
    sudo useradd <yournewusername> -m

    # Set password for the new user
    sudo passwd <yournewusername>

Az alábbi képernyőfelvételen a kimenetek.

![3\. párhuzamos felhasználó](./media/r-server-hdinsight-manage/concurrent-users-2.png)

Amikor a rendszer "aktuális Kerberos-jelszót:", csak nyomja meg az **Enter** figyelmen kívül hagyja azt. A `useradd` parancs `-m` kapcsolója jelzi, hogy a rendszer létrehoz egy kezdőmappát a felhasználó számára, amely szükséges az RStudio Community verziójához.

### <a name="step-3-use-rstudio-community-version-with-the-user-created"></a>3\. lépés: Az RStudio Community verziójának használata a létrehozott felhasználóval

Az RStudio eléréséhez https://CLUSTERNAME.azurehdinsight.net/rstudio/. Ha első alkalommal a fürt létrehozása után naplózás, adja meg a fürt rendszergazdai hitelesítő adataival követ a létrehozott SSH-felhasználónál használt. Ha ez nem az első bejelentkezés, csak adja meg a hitelesítő adatokat a létrehozott SSH-felhasználót.

Is bejelentkezhet az eredeti hitelesítő adatokkal (alapértelmezés szerint a *sshuser*) egyidejűleg egy másik böngészőablakból.

Figyelje meg azt is, hogy az újonnan felvett felhasználók nem rendelkeznek gyökérjogosultságokkal a Linux rendszerben, de ugyanolyan hozzáférésük van a távoli HDFS- és WASB-tárolón az összes fájlhoz.

## <a name="connect-remotely-to-microsoft-ml-services"></a>Távoli csatlakozás a Microsoft Machine Learning-szolgáltatások

Állíthat be hozzáférési a HDInsight Spark számítási környezetet a Machine Learning-ügyfél egy távoli példányát futtató asztali számítógépeken. Ehhez meg kell adnia a beállításokat (hdfsShareDir, shareDir, sshUsername, sshHostname, sshSwitches és sshProfileScript) Ha az rxspark számítási környezetet az asztalon: Példa:

    myNameNode <- "default"
    myPort <- 0

    mySshHostname  <- '<clustername>-ed-ssh.azurehdinsight.net'  # HDI secure shell hostname
    mySshUsername  <- '<sshuser>'# HDI SSH username
    mySshSwitches  <- '-i /cygdrive/c/Data/R/davec'   # HDI SSH private key

    myhdfsShareDir <- paste("/user/RevoShare", mySshUsername, sep="/")
    myShareDir <- paste("/var/RevoShare" , mySshUsername, sep="/")

    mySparkCluster <- RxSpark(
      hdfsShareDir = myhdfsShareDir,
      shareDir     = myShareDir,
      sshUsername  = mySshUsername,
      sshHostname  = mySshHostname,
      sshSwitches  = mySshSwitches,
      sshProfileScript = '/etc/profile',
      nameNode     = myNameNode,
      port         = myPort,
      consoleOutput= TRUE
    )

További információkért tekintse meg a "Használatával a Microsoft Machine Learning kiszolgáló, az Apache Hadoop-ügyfelének" szakaszában [RevoScaleR egy Apache Spark számítási környezet használata](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-spark#more-spark-scenarios)

## <a name="use-a-compute-context"></a>Számítási környezet használata

A számítási környezetekkel vezérelheti, hogy a számítás helyben történik-e az élcsomóponton, vagy elosztottan a HDInsight-fürtben lévő csomópontok között.  Az RStudio Server számítási környezetek beállítása egy példa: [egy Machine Learning-szolgáltatások az RStudio Server használata Azure HDInsight-fürtön R-szkriptek végrehajtása](machine-learning-services-quickstart-job-rstudio.md).

## <a name="distribute-r-code-to-multiple-nodes"></a>R-kód elosztása több csomópontra

HDInsight ML szolgáltatásokkal, meglévő R-kódokat és futtatása a fürtben több csomóponton használatával `rxExec`. Ez a függvény akkor hasznos, amikor paraméteres frissítést vagy szimulációkat végez. A következő kód az `rxExec` használatának példája:

    rxExec( function() {Sys.info()["nodename"]}, timesToRun = 4 )

Ha a Spark környezet továbbra is használja, ez a parancs visszaadja azon munkavégző csomópontok csomópontnév értékét, amely a kód `(Sys.info()["nodename"])` futtatta. Ha például egy négy csomópontból álló fürtön várhatóan az alábbi kódrészlethez hasonló kimenetet kaphat:

    $rxElem1
        nodename
    "wn3-mymlser"

    $rxElem2
        nodename
    "wn0-mymlser"

    $rxElem3
        nodename
    "wn3-mymlser"

    $rxElem4
        nodename
    "wn3-mymlser"

## <a name="access-data-in-apache-hive-and-parquet"></a>Hozzáférés az adatokhoz az Apache Hive és a parquet eszközökben

Machine Learning-szolgáltatások HDInsight a Spark számítási környezet ScaleR-függvényei általi lehetővé teszi az adatok közvetlen elérését a Hive és a parquet eszközökben való használatra. Ezek a képességek az RxHiveData és RxParquetData nevű új ScaleR adatforrás-függvényeken keresztül érhetők el, amelyek a Spark SQL-en keresztül töltenek adatokat közvetlenül a Spark DataFrame-be a ScaleR által végzett elemzéshez.

A következő kód tartalmazza az új függvények használatának néhány mintakódját:

    #Create a Spark compute context:
    myHadoopCluster <- rxSparkConnect(reset = TRUE)

    #Retrieve some sample data from Hive and run a model:
    hiveData <- RxHiveData("select * from hivesampletable",
                     colInfo = list(devicemake = list(type = "factor")))
    rxGetInfo(hiveData, getVarInfo = TRUE)

    rxLinMod(querydwelltime ~ devicemake, data=hiveData)

    #Retrieve some sample data from Parquet and run a model:
    rxHadoopMakeDir('/share')
    rxHadoopCopyFromLocal(file.path(rxGetOption('sampleDataDir'), 'claimsParquet/'), '/share/')
    pqData <- RxParquetData('/share/claimsParquet',
                     colInfo = list(
                age    = list(type = "factor"),
               car.age = list(type = "factor"),
                  type = list(type = "factor")
             ) )
    rxGetInfo(pqData, getVarInfo = TRUE)

    rxNaiveBayes(type ~ age + cost, data = pqData)

    #Check on Spark data objects, cleanup, and close the Spark session:
    lsObj <- rxSparkListData() # two data objs are cached
    lsObj
    rxSparkRemoveData(lsObj)
    rxSparkListData() # it should show empty list
    rxSparkDisconnect(myHadoopCluster)


További információ az ezen új függvények használatával a Machine Learning-szolgáltatások használatán keresztül online súgójában találhat a `?RxHivedata` és `?RxParquetData` parancsokat.  

## <a name="install-additional-r-packages-on-the-cluster"></a>További R-csomagok telepíthetők a fürtön

### <a name="to-install-r-packages-on-the-edge-node"></a>R-csomagok telepítése az élcsomópontra

Ha szeretne további R-csomagok telepítése az élcsomópontra, `install.packages()` közvetlenül belül az R-konzolról, egyszer csatlakozik az élcsomóponthoz ssh-n keresztül. 

### <a name="to-install-r-packages-on-the-worker-node"></a>R-csomagok telepítéséhez a feldolgozó csomóponton

A fürt munkavégző csomópontjaira R csomagokat telepítenie, Szkriptműveletet kell használnia. A szkriptműveletek olyan Bash-szkriptek, amelyekkel konfigurációs módosítások végezhetők a HDInsight-fürtön, vagy további szoftverek, például további R-csomagok telepíthetők. 

> [!IMPORTANT]  
> A további R csomagok szkriptműveletekkel végzett telepítése csak a fürt létrehozása után használható. Használja ezt az eljárást a fürt létrehozásakor, ahogy a parancsfájl támaszkodik a Machine Learning-szolgáltatások teljesen konfigurált.

1. Kövesse a lépéseket [fürtök testreszabása Szkriptműveletek használatával](../hdinsight-hadoop-customize-cluster-linux.md).

3. A **Szkriptművelet**, adja meg a következő információkat:

   * A **parancsprogram típusa**válassza **egyéni**.

   * A **neve**, adja meg a parancsfájlművelet nevét.

     * A **Bash parancsfájl URI azonosítója**, adja meg `https://mrsactionscripts.blob.core.windows.net/rpackages-v01/InstallRPackages.sh`. Ez az a szkript, amely további R csomagokat telepít a munkavégző csomópont

   * Jelölje be a jelölőnégyzetet, csak a **feldolgozó**.

   * **Paraméterek**: Az R-csomagok telepíthetők. Például: `bitops stringr arules`

   * A jelölőnégyzet bejelölésével **parancsfájlműveletet**.  

   > [!NOTE]
   > 1. Alapértelmezés szerint az összes R-csomagok települnek, a gépi tanulás Server telepített verziója megfelel a Microsoft MRAN tárház egy pillanatképből. Ha a csomagok újabb verzióját szeretné telepíteni, akkor van némi inkompatibilitási kockázat. Ez a fajta telepítés azonban lehetséges, ha a `useCRAN` parancsot adja meg a csomaglista első elemeként, például: `useCRAN bitops, stringr, arules`.  
   > 2. Néhány R csomag további Linux rendszerű könyvtárakat igényel. Az egyszerűség kedvéért a HDInsight Machine Learning-szolgáltatások előre telepítve áll rendelkezésre a függőségekkel, szükség szerint a felső 100 legnépszerűbb R-csomagokat. Ha azonban a telepített R csomag(ok) további könyvtárakat telepítését igényli(k), akkor le kell töltenie az itt használt alapvető szkriptet, és lépéseket kell hozzáadnia a rendszerkönyvtárak telepítéséhez. Ezután fel kell töltenie a módosított szkriptet egy nyilvános blob-tárolóba az Azure Storage-ben, és a módosított szkripttel kell telepítenie a csomagokat.
   >    A szkriptműveletek fejlesztésével kapcsolatos további információért lásd: [Szkriptművelet fejlesztése](../hdinsight-hadoop-script-actions-linux.md).  
   >
   >

   ![Szkriptműveletek hozzáadása](./media/r-server-hdinsight-manage/submitscriptaction.png)

4. Válassza a **Létrehozás** lehetőséget a szkript futtatásához. A szkript befejezése után az R csomagok elérhetők az összes munkavégző csomóponton.

## <a name="next-steps"></a>További lépések

* [Az ML-szolgáltatások fürtjének üzembe helyezése a HDInsighton](r-server-operationalize.md)
* [Számítási környezeti beállítások a Machine Learning szolgáltatás HDInsight-fürt](r-server-compute-contexts.md)
* [Azure Storage-lehetőségek az ML-szolgáltatások HDInsighton belüli fürtjéhez](r-server-storage.md)
