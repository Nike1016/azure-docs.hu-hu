---
title: Tippek a Hadoop Linux-alapú HDInsight való használatához – Azure
description: A Linux-alapú HDInsight-(Hadoop-) fürtöket az Azure-felhőben futó, ismerős Linux-környezetekben implementálhatja.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: 1904ab07a188e4e877a4fb2f2b7682d923c08fb2
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68441996"
---
# <a name="information-about-using-hdinsight-on-linux"></a>Információk a HDInsight Linuxon való használatáról

Az Azure HDInsight-fürtök az Azure-felhőben futó, ismerős Linux-környezetekben Apache Hadoop biztosítanak. A legtöbb esetben pontosan ugyanúgy kell működnie, mint bármely más Hadoop-Linux-telepítés. Ez a dokumentum olyan konkrét különbségeket hív meg, amelyekkel tisztában kell lennie.

## <a name="prerequisites"></a>Előfeltételek

A jelen dokumentumban ismertetett lépések többsége a következő segédprogramokat használja, amelyeket esetleg telepíteni kell a rendszerre.

* [curl](https://curl.haxx.se/) – a web-alapú szolgáltatásokkal való kommunikációra szolgál.
* **jQ**, parancssori JSON-processzor.  Lásd [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/):.
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) – az Azure-szolgáltatások távoli felügyeletére szolgál.
* **Egy SSH-ügyfél**. További információ: [Kapcsolódás HDInsight (Apache Hadoop) SSH használatával](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="users"></a>Felhasználók

Ha nincs [tartományhoz csatlakoztatva](./domain-joined/hdinsight-security-overview.md), a HDInsight **egyetlen felhasználói** rendszernek kell tekinteni. Egyetlen SSH-felhasználói fiók jön létre a fürttel, rendszergazdai szintű engedélyekkel. További SSH-fiókok is létrehozhatók, de rendszergazdai hozzáféréssel is rendelkeznek a fürthöz.

A tartományhoz csatlakoztatott HDInsight több felhasználót és részletesebb jogosultságokat és szerepkör-beállításokat is támogat. További információ: tartományhoz [csatlakoztatott HDInsight-fürtök kezelése](./domain-joined/apache-domain-joined-manage.md).

## <a name="domain-names"></a>Tartománynevek

Az internetről `CLUSTERNAME.azurehdinsight.net` a fürthöz való csatlakozáskor használandó teljes tartománynév (FQDN) vagy `CLUSTERNAME-ssh.azurehdinsight.net` (csak ssh esetén).

Belsőleg a fürt minden csomópontja rendelkezik egy, a fürt konfigurálása során hozzárendelt névvel. A fürt nevének megkereséséhez tekintse  meg a gazdagépek lapot a Ambari webes felhasználói felületén. A következő paranccsal is visszaállíthatja a gazdagépek listáját a Ambari REST API:

    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts" | jq '.items[].Hosts.host_name'

Cserélje le a `CLUSTERNAME` elemet a fürt nevére. Ha a rendszer kéri, adja meg a rendszergazdai fiók jelszavát. Ez a parancs egy JSON-dokumentumot ad vissza, amely tartalmazza a fürtben lévő gazdagépek listáját. [](https://stedolan.github.io/jq/) a`host_name` jQ az egyes gazdagépek elem értékének kinyerésére szolgál.

Ha meg kell találnia egy adott szolgáltatáshoz tartozó csomópont nevét, lekérdezheti az adott összetevőhöz tartozó Ambari. Ha például a HDFS neve csomópont gazdagépeit szeretné megkeresni, használja a következő parancsot:

    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'

Ez a parancs egy JSON-dokumentumot ad vissza, amely leírja [](https://stedolan.github.io/jq/) a szolgáltatást, majd a jQ `host_name` csak a gazdagépek értékét kéri le.

## <a name="remote-access-to-services"></a>Távoli hozzáférés a szolgáltatásokhoz

* **Ambari (web)**  - https://CLUSTERNAME.azurehdinsight.net

    A hitelesítéshez használja a Fürtfelügyelő felhasználóját és jelszavát, majd jelentkezzen be a Ambari-be.

    A hitelesítés egyszerű szöveges – a HTTPS használatával mindig biztosítható a kapcsolat biztonsága.

    > [!IMPORTANT]  
    > A Ambari-hozzáférési csomópontokon keresztül elérhető webes felület egy belső tartománynév használatával. A belső tartománynevek nem nyilvánosan elérhetők az interneten keresztül. Előfordulhat, hogy a "kiszolgáló nem található" hibaüzenet jelenik meg, amikor bizonyos funkciókhoz próbál hozzáférni az interneten keresztül.
    >
    > A Ambari webes felhasználói felületének teljes funkcionalitásához használjon SSH-alagutat a webes forgalom a fürt fő csomópontjára való kiosztásához. Lásd: [az SSH Tunneling használata az Apache Ambari webes felhasználói felület, a erőforráskezelő, a JobHistory, a NameNode, a Oozie és más webes felület eléréséhez](hdinsight-linux-ambari-ssh-tunnel.md)

* **Ambari (REST)**  - https://CLUSTERNAME.azurehdinsight.net/ambari

    > [!NOTE]  
    > A hitelesítést a Fürtfelügyelő felhasználójának és jelszavának használatával végezze el.
    >
    > A hitelesítés egyszerű szöveges – a HTTPS használatával mindig biztosítható a kapcsolat biztonsága.

* **Webhcaten (Templeton)**  - https://CLUSTERNAME.azurehdinsight.net/templeton

    > [!NOTE]  
    > A hitelesítést a Fürtfelügyelő felhasználójának és jelszavának használatával végezze el.
    >
    > A hitelesítés egyszerű szöveges – a HTTPS használatával mindig biztosítható a kapcsolat biztonsága.

* **SSH** -CLUSTERNAME-SSH.azurehdinsight.net a 22-es vagy 23-as porton. A 22-es port az elsődleges átjárócsomóponthoz való kapcsolódásra szolgál, míg a 23 a másodlagoshoz való csatlakozáshoz használatos. A fő csomópontokkal kapcsolatos további információkért lásd: [Apache Hadoop-fürtök rendelkezésre állása és megbízhatósága a HDInsight-ben](hdinsight-high-availability-linux.md).

    > [!NOTE]  
    > Az ügyfélgépről csak SSH-n keresztül férhet hozzá a fürt fő csomópontjaihoz. A csatlakozás után a munkavégző csomópontokat a átjárócsomóponthoz SSH használatával érheti el.

További információ: [Apache Hadoop Services által használt portok a HDInsight](hdinsight-hadoop-port-settings-for-services.md) -dokumentumban.

## <a name="file-locations"></a>Fájlok helye

A Hadoop kapcsolatos fájlok a fürtcsomópontokon `/usr/hdp`találhatók. Ez a könyvtár a következő alkönyvtárakat tartalmazza:

* **2.6.5.3006-29**: A címtár neve a HDInsight által használt Hortonworks-adatplatform verziója. A fürtön lévő szám különbözhet az itt felsoroltak számától.
* **jelenlegi**: Ez a könyvtár a **2.6.5.3006-29** könyvtár alkönyvtáraira mutató hivatkozásokat tartalmaz. Ez a könyvtár létezik, így nem kell megjegyeznünk a verziószámot.

Például az adatfájlok és a JAR-fájlok a Hadoop `/example` elosztott fájlrendszer `/HdiSamples`címen és címen találhatók.

## <a name="hdfs-azure-storage-and-data-lake-storage"></a>HDFS, Azure Storage és Data Lake Storage

A legtöbb Hadoop-eloszlásban az adattárolást a HDFS tárolja, amelyet a fürtön lévő gépek helyi tárterülete támogat. A helyi tárterület költséges lehet egy felhőalapú megoldáshoz, ahol a számítási erőforrások óradíja óránként vagy percenként történik.

A HDInsight használatakor az adatfájlok skálázható és rugalmas módon tárolódnak a felhőben az Azure Blob Storage és opcionálisan Azure Data Lake Storage használatával. Ezek a szolgáltatások a következő előnyöket nyújtják:

* Olcsó hosszú távú tárolás.
* A külső szolgáltatásokból, például webhelyekről, fájlfeltöltés/letöltési segédprogramokból, különböző nyelvi SDK-kből és webböngészőkből való kisegítés.
* Nagyméretű fájlok kapacitása és nagyméretű méretezhető tárolás.

További információ: a blobok és a [Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/) [ismertetése](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) .

Az Azure Storage vagy a Data Lake Storage használatakor nem kell semmit HDInsight az adatok eléréséhez. Például a következő parancs felsorolja a `/example/data` mappában található fájlokat, függetlenül attól, hogy az Azure Storage-ban vagy a Data Lake Storageon van-e tárolva:

    hdfs dfs -ls /example/data

A HDInsight-ben az adattárolási erőforrások (Azure Blob Storage és Azure Data Lake Storage) le vannak választva a számítási erőforrásokból. Ezért létrehozhat HDInsight-fürtöket a számítások elvégzéséhez, és később törölheti a fürtöt a munka befejezésekor, miközben az adatfájlok biztonságban maradnak a Felhőbeli tárolásban, amennyiben szükséges.


### <a name="URI-and-scheme"></a>URI és séma

Egyes parancsok esetében előfordulhat, hogy az URI részeként meg kell adnia a sémát egy fájl elérésekor. Például a Storm-HDFS összetevő megköveteli a séma megadását. Ha nem alapértelmezett tárolót használ (a tároló "további" tárolóként van hozzáadva a fürthöz), mindig a sémát kell használnia az URI részeként.

Az __Azure Storage__használatakor használja a következő URI-sémák egyikét:

* `wasb:///`: Az alapértelmezett tárterületet titkosítatlan kommunikációval érheti el.

* `wasbs:///`: Az alapértelmezett tároló elérése titkosított kommunikáció használatával.  A wasbs séma csak a HDInsight 3,6-es verziójától támogatott.

* `wasb://<container-name>@<account-name>.blob.core.windows.net/`: Nem alapértelmezett Storage-fiókkal való kommunikációhoz használatos. Ha például egy további Storage-fiókkal vagy egy nyilvánosan elérhető Storage-fiókban tárolt adatokhoz fér hozzá.

__Azure Data Lake Storage Gen2__használatakor használja a következő URI-sémák egyikét:

* `abfs:///`: Az alapértelmezett tárterületet titkosítatlan kommunikációval érheti el.

* `abfss:///`: Az alapértelmezett tároló elérése titkosított kommunikáció használatával.  A abfss séma csak a HDInsight 3,6-es verziójától támogatott.

* `abfs://<container-name>@<account-name>.dfs.core.windows.net/`: Nem alapértelmezett Storage-fiókkal való kommunikációhoz használatos. Ha például egy további Storage-fiókkal vagy egy nyilvánosan elérhető Storage-fiókban tárolt adatokhoz fér hozzá.

__Azure Data Lake Storage Gen1__használatakor használja a következő URI-sémák egyikét:

* `adl:///`: A fürthöz tartozó alapértelmezett Data Lake Storage elérése.

* `adl://<storage-name>.azuredatalakestore.net/`: Nem alapértelmezett Data Lake Storagesal való kommunikációhoz használatos. A HDInsight-fürt gyökérkönyvtárán kívüli adatelérésre is használható.

> [!IMPORTANT]  
> Ha a HDInsight alapértelmezett tárolóként Data Lake Storage használ, meg kell adnia egy elérési utat a tárolón belül, amelyet a HDInsight-tároló gyökeréhez kell használni. Az alapértelmezett elérési `/clusters/<cluster-name>/`út:.
>
> Az adateléréshez `adl:///` vagy az-hoz való hozzáféréshez csak a fürt `/clusters/<cluster-name>/`gyökerében (például) tárolt adataihoz férhet hozzá. `/` Ha a tárolóban bárhol szeretné elérni az adatelérést, használja a `adl://<storage-name>.azuredatalakestore.net/` következő formátumot:.

### <a name="what-storage-is-the-cluster-using"></a>Milyen tárterületet használ a fürt

A Ambari a fürt alapértelmezett tárolási konfigurációjának beolvasására használható. Használja az alábbi parancsot a HDFS konfigurációs adatainak a curl használatával történő lekéréséhez, és szűrje a [jQ](https://stedolan.github.io/jq/)használatával:

```bash
curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
```

> [!NOTE]  
> Ez a parancs visszaadja a kiszolgáló (`service_config_version=1`) által alkalmazott első konfigurációt, amely tartalmazza ezt az információt. Előfordulhat, hogy az összes konfigurációs verziót fel kell sorolnia, hogy megtalálja a legújabbat.

Ez a parancs a következő URI azonosítóhoz hasonló értéket ad vissza:

* `wasb://<container-name>@<account-name>.blob.core.windows.net`Azure Storage-fiók használata esetén.

    A fiók neve az Azure Storage-fiók neve. A tároló neve a fürt tárolójának gyökerét szolgáló blob-tároló.

* `adl://home`Azure Data Lake Storage használata esetén. A Data Lake Storage nevének beszerzéséhez használja a következő REST-hívást:

     ```bash
    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'
    ```

    Ez a parancs a következő állomásnevet adja vissza `<data-lake-store-account-name>.azuredatalakestore.net`:.

    A következő REST-hívással kérheti le az áruházban lévő könyvtárat, amely a HDInsight gyökerét használja:

    ```bash
    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'
    ```

    Ez a parancs a következő elérési úthoz hasonló elérési `/clusters/<hdinsight-cluster-name>/`utat ad vissza:.

A tárolási adatokat a Azure Portal használatával is megkeresheti a következő lépésekkel:

1. A [Azure Portal](https://portal.azure.com/)válassza ki a HDInsight-fürtöt.

2. A **Tulajdonságok** szakaszban válassza a **Storage-fiókok**lehetőséget. Megjelenik a fürt tárolási adatai.

### <a name="how-do-i-access-files-from-outside-hdinsight"></a>Hogyan a HDInsight kívülről származó fájlok elérését

A HDInsight-fürtön kívül különböző módokon férhet hozzá az adatokhoz. Az alábbiakban néhány olyan segédprogramot és SDK-t találhat, amelyek segítségével dolgozhat az adataival:

Ha az __Azure Storage__-t használja, tekintse meg a következő hivatkozásokat az adatai eléréséhez:

* [Azure CLI](https://docs.microsoft.com/cli/azure/install-az-cli2): Parancssori felületi parancsok az Azure-ban való használathoz. A telepítését követően használja a `az storage` parancsot a tárolás vagy `az storage blob` a blob-specifikus parancsok használatához.
* [blobxfer.py](https://github.com/Azure/blobxfer): Python-szkript a Blobok Azure Storage-beli használatáról.
* Különböző SDK-k:

    * [Java](https://github.com/Azure/azure-sdk-for-java)
    * [Node.js](https://github.com/Azure/azure-sdk-for-node)
    * [PHP](https://github.com/Azure/azure-sdk-for-php)
    * [Python](https://github.com/Azure/azure-sdk-for-python)
    * [Ruby](https://github.com/Azure/azure-sdk-for-ruby)
    * [.NET](https://github.com/Azure/azure-sdk-for-net)
    * [Tárolási REST API](https://msdn.microsoft.com/library/azure/dd135733.aspx)

__Azure Data Lake Storage__használata esetén tekintse meg az alábbi hivatkozásokat az adatai eléréséhez:

* [Webböngésző](../data-lake-store/data-lake-store-get-started-portal.md)
* [PowerShell](../data-lake-store/data-lake-store-get-started-powershell.md)
* [Azure CLI](../data-lake-store/data-lake-store-get-started-cli-2.0.md)
* [WebHDFS REST API](../data-lake-store/data-lake-store-get-started-rest-api.md)
* [A Data Lake Tools for Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504)
* [.NET](../data-lake-store/data-lake-store-get-started-net-sdk.md)
* [Java](../data-lake-store/data-lake-store-get-started-java-sdk.md)
* [Python](../data-lake-store/data-lake-store-get-started-python.md)

## <a name="scaling"></a>A fürt skálázása

A fürt skálázási funkciója lehetővé teszi a fürt által használt adatcsomópontok számának dinamikus módosítását. Skálázási műveleteket hajthat végre, miközben más feladatok vagy folyamatok futnak a fürtön.  Lásd még: [HDInsight-fürtök méretezése](./hdinsight-scaling-best-practices.md)

A különböző típusú fürtöket a következőképpen befolyásolja a méretezés:

* **Hadoop**: A fürtben lévő csomópontok számának skálázásakor a fürt egyes szolgáltatásai újraindulnak. A skálázási műveletek a skálázási művelet befejezésekor a feladatátvételt vagy függőben lévő feladatokat is okozhatnak. A feladatok újbóli elküldése a művelet befejeződése után.
* **HBase**: A skálázási művelet befejezése után néhány percen belül automatikusan kiegyensúlyozzák a regionális kiszolgálókat. A regionális kiszolgálók manuális elosztásához kövesse az alábbi lépéseket:

    1. Kapcsolódjon a HDInsight-fürthöz az SSH használatával. További információ: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

    2. A HBase rendszerhéj elindításához használja a következőt:

            hbase shell

    3. A HBase rendszerhéj betöltését követően a következő paranccsal végezheti el a regionális kiszolgálók manuális elosztását:

            balancer

* **Storm**: A futó Storm-topológiákat a skálázási művelet elvégzése után újra el kell osztania. A terheléselosztás lehetővé teszi a topológia számára a párhuzamossági beállítások újramódosítását a fürtben lévő csomópontok új száma alapján. A futó topológiák újraelosztásához használja az alábbi lehetőségek egyikét:

    * **SSH**: Kapcsolódjon a kiszolgálóhoz, és a következő parancs használatával egyenlítse ki a topológiát:

            storm rebalance TOPOLOGYNAME

        Paramétereket is megadhat a topológia által eredetileg biztosított párhuzamossági javaslatok felülbírálásához. Például `storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10` újrakonfigurálja a topológiát 5 munkavégző folyamatra, 3 végrehajtót a kék kiöntő összetevőhöz, és 10 végrehajtót a sárga-bolt összetevőhöz.

    * **Storm felhasználói felület**: A következő lépésekkel kiegyensúlyozhatja a topológiát a Storm felhasználói felületének használatával.

        1. Nyissa meg `https://CLUSTERNAME.azurehdinsight.net/stormui` a webböngészőben `CLUSTERNAME` , ahol a a Storm-fürt neve. Ha a rendszer kéri, adja meg a fürt létrehozásakor megadott HDInsight-Fürtfelügyelő (rendszergazda) nevét és jelszavát.
        2. Válassza ki a megismételni kívánt topológiát, majd kattintson az **újraelosztás** gombra. Adja meg az újraelosztási művelet végrehajtása előtti késleltetést.

* **Kafka**: A skálázási műveletek után újra kell osztania a partíciós replikákat. További információ: az [adatok magas rendelkezésre állása Apache Kafka HDInsight](./kafka/apache-kafka-high-availability.md) -dokumentummal.

A HDInsight-fürt méretezésével kapcsolatos részletes információkért lásd:

* [Apache Hadoop-fürtök kezelése a HDInsight-ben a Azure Portal használatával](hdinsight-administer-use-portal-linux.md#scale-clusters)
* [Apache Hadoop-fürtök kezelése a HDInsight az Azure CLI használatával](hdinsight-administer-use-command-line.md#scale-clusters)

## <a name="how-do-i-install-hue-or-other-hadoop-component"></a>Hogyan telepíteni a Hue (vagy más Hadoop összetevőt)?

A HDInsight egy felügyelt szolgáltatás. Ha az Azure problémát észlel a fürttel kapcsolatban, akkor előfordulhat, hogy törli a meghibásodott csomópontot, és létrehoz egy csomópontot, amely lecseréli azt. Ha manuálisan telepíti a műveleteket a fürtön, a rendszer nem őrzi meg őket a művelet bekövetkeztekor. Ehelyett használja a [HDInsight-parancsfájlok műveleteit](hdinsight-hadoop-customize-cluster-linux.md). A következő módosításokat végezheti el egy parancsfájl-művelettel:

* Szolgáltatás vagy webhely telepítése és konfigurálása.
* Telepítsen és konfiguráljon olyan összetevőt, amely a fürt több csomópontján is konfigurációs módosításokat igényel.

A parancsfájl-műveletek bash-parancsfájlok. A parancsfájlok a fürt létrehozása során futnak, és további összetevők telepítésére és konfigurálására szolgálnak. Példa parancsfájlok a következő összetevők telepítéséhez:

* [Apache Giraph](hdinsight-hadoop-giraph-install-linux.md)

Az egyéni parancsfájlművelet-fejlesztéssel kapcsolatos további információkért lásd: [Script Action development with HDInsight](hdinsight-hadoop-script-actions-linux.md) (Parancsfájlműveletek fejlesztése a HDInsighttal).

### <a name="jar-files"></a>JAR-fájlok

Egyes Hadoop-technológiák olyan önálló jar-fájlokban találhatók, amelyek a MapReduce-feladatok részeként vagy a Pig vagy a kaptáron belül használt függvényeket tartalmazzák. Gyakran nem igényelnek telepítést, és közvetlenül a létrehozás után fel lehet tölteni a fürtbe. Ha azt szeretné, hogy az összetevő megmaradjon a fürt újraképalkotás terén, a jar-fájlt a fürt alapértelmezett tárolójában (WASB vagy ADL) is tárolhatja.

Ha például az [Apache DataFu](https://datafu.incubator.apache.org/)legújabb verzióját szeretné használni, letöltheti a projektet tartalmazó jar-t, és feltöltheti azt a HDInsight-fürtbe. Ezután kövesse a DataFu dokumentációját a használatáról a Pig vagy a kaptár használatával.

> [!IMPORTANT]  
> Az önálló jar-fájlokat tartalmazó összetevők a HDInsight-mel vannak ellátva, de nem szerepelnek az elérési úton. Ha egy adott összetevőt keres, akkor a következő paranccsal keresheti meg a fürtön:
>
> ```find / -name *componentname*.jar 2>/dev/null```
>
> Ez a parancs visszaadja az összes egyező jar-fájl elérési útját.

Ha egy összetevő más verzióját szeretné használni, töltse fel a szükséges verziót, és használja azt a feladatokban.

> [!IMPORTANT]
> A HDInsight-fürthöz biztosított összetevők teljes mértékben támogatottak, és Microsoft ügyfélszolgálata segít elkülöníteni és elhárítani ezeket az összetevőket érintő problémákat.
>
> Az egyéni összetevők kereskedelmileg ésszerű támogatást kapnak a probléma további megoldásához. Ez a probléma megoldásához vezethet, vagy megkérdezheti, hogy a nyílt forráskódú technológiákhoz elérhető csatornákat szeretne-e felvenni. Például számos közösségi webhely használható, például: [MSDN-fórum a HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight)- [https://stackoverflow.com](https://stackoverflow.com)hoz. Emellett az Apache [https://apache.org](https://apache.org)-projektek is rendelkeznek projekt-webhelyekkel, például: [Hadoop](https://hadoop.apache.org/), [Spark](https://spark.apache.org/).

## <a name="next-steps"></a>További lépések

* [HDInsight-fürtök kezelése az Apache Ambari REST API használatával](./hdinsight-hadoop-manage-ambari-rest-api.md)
* [Apache Hive használata a HDInsight](hadoop/hdinsight-use-hive.md)
* [Az Apache Pig és a HDInsight használata](hadoop/hdinsight-use-pig.md)
* [MapReduce-feladatok használata a HDInsightban](hadoop/hdinsight-use-mapreduce.md)
