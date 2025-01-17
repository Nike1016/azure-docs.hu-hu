---
title: A Linux és Windows rendszerű Azure Data Science Virtual Machine (adatelemző virtuális gép) bemutatása | Microsoft Docs
description: Alapvető elemzési helyzetek és összetevők Windows és Linux rendszerű adatelemző virtuális gépekhez.
keywords: adatelemzési eszközök, adatelemző virtuális gép, eszközök adatelemzéshez, linux adatelemzés
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.assetid: d4f91270-dbd2-4290-ab2b-b7bfad0b2703
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 02/22/2019
ms.author: vijetaj
ms.openlocfilehash: 5816f53115f3ec54cbd9784894a5262b68dd6e95
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565184"
---
# <a name="what-is-azure-data-science-virtual-machine-for-linux-and-windows"></a>Mi a Linux és a Windows rendszerhez készült Azure Data Science Virtual Machine?

Az Adatelemző virtuális gép (DSVM) egy személyre szabott virtuálisgép-lemezkép a Microsoft Azure-felhőben, amelyet kifejezetten adatelemzésre hoztak létre. Számos népszerű adatelemzési és egyéb eszköz található meg rajta előre telepítve és konfigurálva, amelyek jelentősen felgyorsítják az intelligens alkalmazások fejlett elemzésekhez történő összeállítását. Windows Server és Linux rendszeren érhető el. A DSVM Windows-kiadását Server 2016 és Server 2012 rendszeren tesszük elérhetővé. A DSVM linuxos kiadásait Ubuntu 16.04 LTS és CentOS 7.4 rendszerre kínáljuk.

Ez a cikk azt ismerteti, hogy mit tehet a Data Science VM. Ismerteti a virtuális gép használatának néhány főbb forgatókönyvét, valamint a Windows-és Linux-verziók körvonalazza elérhető főbb funkciókat. A cikk emellett útmutatást nyújt a használat megkezdéséhez.


## <a name="what-can-i-do-with-the-data-science-virtual-machine"></a>Mire használhatom az adatelemző virtuális gépet?
A Data Science Virtual Machine (DSVM) rendeltetése az, hogy könnyen használható, előre konfigurált és teljes körűen integrált adatelemzési környezetet biztosítson a legkülönfélébb képzettségű és a legkülönfélébb iparágakban dolgozó adatszakértőknek. Saját hasonló munkakörnyezet kialakítása helyett üzembe helyezhet egy adatelemző virtuális gépet, így napokat vagy akár _heteket_ is megtakaríthat a telepítés, konfigurálás és csomagkezelés folyamatában. A DSVM üzembe helyezése után azonnal munkához láthat adatelemzési projektjén.

Az adatelemző virtuális gépet úgy tervezték és konfigurálták, hogy sokféle alkalmazási helyzetben használható legyen. A környezet követelményeinek megváltozása mellett akár le is méretezheti a környezetet. Az adatelemzési feladatok elvégzéséhez használhatja az előnyben részesített nyelvet is, és más eszközöket is telepíthet, amelyekkel testre szabhatja a rendszer pontos szükségleteit.

## <a name="key-scenarios"></a>Főbb alkalmazási helyzetek
Ez a szakasz néhány alapvető alkalmazási helyzetet javasol, amelyben az adatelemző virtuális gép üzembe helyezhető.

### <a name="preconfigured-analytics-desktop-in-the-cloud"></a>Előre konfigurált felhőbeli elemzési kezelőfelület
Az adatelemző virtuális gép alapszintű konfigurációt kínál azoknak az adatszakértőknek, akik helyszíni számítógépeiket felügyelt felhőbeli kezelőfelületre szeretnék cserélni. Ez az alapszint biztosítja, hogy egy csapat valamennyi adatszakértője egységes beállításokkal ellenőrizhesse a kísérleti eredményeket, és elősegíti az együttműködést. A rendszergazdai terhek csökkentésével csökkenti a költségeket is. Ez a terhek csökkentése a speciális elemzésekhez szükséges különböző szoftvercsomagok kiértékeléséhez, telepítéséhez és karbantartásához szükséges időt takarít meg.

### <a name="data-science-training-and-education"></a>Adatelemzési képzés és oktatás
Az adatelemzési osztályokat bemutató vállalati oktatók és oktatók általában virtuálisgép-rendszerképet biztosítanak. Biztosítják a képet annak biztosításához, hogy a tanulóik konzisztens beállításokkal rendelkezzenek, és hogy a minták kiszámíthatóan működjenek. Az adatelemző virtuális gép igény szerinti környezetet hoz létre egységes beállításokkal, amely megkönnyíti a támogatást és az inkompatibilitási nehézségek leküzdését. Olyan esetekben, amikor gyakran kell környezetet kiépíteni, különösen a rövidebb kurzusokhoz, ez jelentős előnnyel jár.

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>Igény szerinti rugalmas kapacitás nagyszabású projektekhez
Az adatelemzési ötletbörzékhez/versenyekhez és a nagytömegű adatok elemzéséhez és feltárásához bővebb hardverkapacitás szükséges, általában rövid időre. A Data Science VM segítségével gyorsan igény szerint replikálhatja az adatelemzési környezetet, a kibővített kiszolgálókon, amelyek lehetővé teszik a nagy teljesítményű számítási erőforrások futtatásának kísérleteit.

### <a name="custom-compute-power-for-azure-notebooks"></a>Azure Notebooks egyéni számítási teljesítmény

[Azure Notebooks](../../notebooks/azure-notebooks-overview.md) egy ingyenes üzemeltetett szolgáltatás, amely a felhőben Jupyter-jegyzetfüzeteket fejleszt, futtat és oszt meg a telepítés nélkül. Az ingyenes szolgáltatás szintje azonban 4 GB-nyi memóriára és 1 GB-ra van korlátozva. Az összes korlát felszabadításához csatolhat jegyzetfüzet-projektet egy Data Science VM vagy bármely más, Jupyter-kiszolgálót futtató virtuális géphez. Ha Azure Active Directory (például vállalati fiókkal) rendelkező fiókkal jelentkezik be Azure Notebooksba, a jegyzetfüzetek automatikusan megjelenítik az adatelemzési virtuális gépeket a fiókhoz társított előfizetésekben. További információ: [projektek kezelése és konfigurálása – számítási réteg](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier).

### <a name="short-term-experimentation-and-evaluation"></a>Rövidtávú kísérletezés és kiértékelés
Az adatelemző virtuális gép minimális előkészítéssel felhasználható olyan eszközök felmérésére és megismerésére, mint a Microsoft ML Server, az SQL Server, a Visual Studio eszközei, a Jupyter, a deep learning / ML eszközkészletek és a közösségben népszerű új eszközök. Mivel a Data Science VM gyorsan beállítható, más rövid távú használati helyzetekben is alkalmazható. Ilyen forgatókönyvek például a közzétett kísérletek replikálása, bemutatók végrehajtása, az online munkamenetek és a konferencia-oktatóanyagok áttekintése.

### <a name="deep-learning"></a>Deep learning
A Data Science VM-en modelleket taníthat be a grafikus processzorokon (GPU) futtatott mélytanulási algoritmusok segítségével. Az Azure-felhőbeli virtuálisgép-méretezési lehetőségeket kihasználva a DSVM segít a felhőbeli GPU-alapú hardver igény szerinti használatában. Nagy modellek betanításakor vagy ha gyors számításokra van szükség, az operációs rendszerlemez megtartásával lehet áttérni GPU-alapó virtuális gépre.  A DSVM Windows Server 2016-os kiadása az előre telepített GPU-illesztőprogramokat, keretrendszereket és deep learning keretrendszerek GPU-verzióit is tartalmazza. A linuxos kiadáson a GPU-alapú mélytanulás a CentOS-es és ubuntus DSVM-eken is engedélyezve van. Data Science VM Ubuntu, CentOS vagy Windows 2016 kiadását a nem GPU-alapú Azure-beli virtuális gépekre is üzembe helyezheti. Ebben az esetben az összes mély tanulási keretrendszer vissza fog térni a CPU-módra.

## <a name="whats-included-in-the-data-science-vm"></a>Mi tartozik az adatelemző virtuális géphez?
Az adatelemző virtuális gépen sok közkedvelt adatelemzési és deep learning eszköz már telepítve és konfigurálva van. Olyan eszközöket is tartalmaz, amelyek megkönnyítik a különböző Azure adat- és elemzési termékek használatát. Ilyen például a Microsoft ML Server (R, Python) a prediktív modellek összeállításához vagy az SQL Server 2017 a nagyméretű adatkészletek feltárásához. A Data Science VM számos más eszközt tartalmaz a nyílt forráskódú Közösségből és a Microsofttól, valamint a mintakód és a jegyzetfüzetek számára. Az alábbi táblázat elemenként mutatja be és hasonlítja össze az adatelemző virtuális gép windowsos és linuxos kiadásainak fő összetevőit.


| **Eszköz**                                                           | **Windowsos kiadás** | **Linuxos kiadás** |
| :------------------------------------------------------------------ |:-------------------:|:------------------:|
| [Microsoft R Open](https://mran.microsoft.com/open/) előre telepített népszerű csomagokkal   |I                      | I             |
| [Microsoft ML Server (R, Python)](https://docs.microsoft.com/machine-learning-server/) Developer Edition melynek része, <br />  &nbsp;&nbsp;&nbsp;&nbsp;* [RevoScaleR/revoscalepy](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-revoscaler) párhuzamos és elosztott nagyteljesítményű keretrendszer (R és Python)<br />  &nbsp;&nbsp;&nbsp;&nbsp;* [MicrosoftML](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-the-microsoftml-package) - A legkorszerűbb új ML-algoritmusok a Microsofttól <br />  &nbsp;&nbsp;&nbsp;&nbsp;* [R és Python operacionalizálás](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)                                            |I                      | I |
| [Microsoft Office](https://products.office.com/business/office-365-proplus-business-software) Pro-Plus megosztott aktiválással – Excel, Word és PowerPoint   |I                      |N              |
| [Anaconda Python](https://www.continuum.io/) 2.7, 3.5 előre telepített népszerű csomagokkal    |I                      |I              |
| [JuliaPro](https://juliacomputing.com/products/juliapro.html) előre telepített népszerű csomagokkal a Julia nyelvhez                         |I                      |I              |
| Relációs adatbázisok                                                            | [SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition| [PostgreSQL](https://www.postgresql.org/) (CentOS),<br/>[SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition (Ubuntu) |
| Adatbáziseszközök                                                       | * SQL Server Management Studio <br/>* SQL Server Integration Services<br/>* [bcp, sqlcmd](https://docs.microsoft.com/sql/tools/command-prompt-utility-reference-database-engine)<br /> * ODBC/JDBC illesztőprogramok| * [SQuirreL SQL](http://squirrel-sql.sourceforge.net/) (lekérdező eszköz), <br /> * bcp, sqlcmd <br /> * ODBC/JDBC illesztőprogramok|
| Méretezhető adatbázison belüli elemzések SQL Server ML szolgáltatásokkal (R, Python) | I     |N              |
| **[Jupyter Notebook Server](https://jupyter.org/) a következő kernelekkel,**                                  | I     | I |
|     &nbsp;&nbsp;&nbsp;&nbsp;* R | I | I |
|     &nbsp;&nbsp;&nbsp;&nbsp;* Python | I | I |
|     &nbsp;&nbsp;&nbsp;&nbsp;* Julia | I | I |
|     &nbsp;&nbsp;&nbsp;&nbsp;* PySpark | I | I |
|     &nbsp;&nbsp;&nbsp;&nbsp;* [Sparkmagic](https://github.com/jupyter-incubator/sparkmagic) | N | I (csak Ubuntu) |
|     &nbsp;&nbsp;&nbsp;&nbsp;* SparkR     | N | I |
| JupyterHub (többfelhasználós notebook server)| N | I |
| JupyterLab (többfelhasználós notebook server) | N | I (csak Ubuntu) |
| **Fejlesztői eszközök, ide-kódok és kód-szerkesztők**| | |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Visual studio 2019 (Community Edition)](https://www.visualstudio.com/community/) git beépülő modullal, Azure HDInsight (Hadoop), Data Lake, SQL Server Adateszközök, [Node. js](https://github.com/Microsoft/nodejstools), [Python](https://aka.ms/ptvs)és [R Tools for Visual Studio (RTVS)](https://microsoft.github.io/RTVS-docs/) | I | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Visual Studio Code](https://code.visualstudio.com/) | I | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop) | I | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [RStudio Server](https://www.rstudio.com/products/rstudio/#Server) | N | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [PyCharm Community Edition](https://www.jetbrains.com/pycharm/) | N | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Atom](https://atom.io/) | N | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Juno (Julia IDE)](https://junolab.org/)| I | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* Vim és Emacs | I | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* Git és GitBash | I | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* OpenJDK | I | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* .NET-keretrendszer | I | N |
| Power BI Desktop | I | N |
| SDK-k az Azure és a Cortana Intelligence szolgáltatáscsomag eléréséhez | I | I |
| **Adatáthelyezési és felügyeleti eszközök** | | |
| &nbsp;&nbsp;&nbsp;&nbsp;* Azure Storage Explorer | I | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Azure CLI](https://docs.microsoft.com/cli/azure) | I | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* Azure Powershell | I | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Azcopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy) | I | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Blob FUSE illesztőprogram](https://github.com/Azure/azure-storage-fuse) | N | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Adlcopy (Azure Data Lake Storage)](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob) | I | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [DocDB adatáttelepítési eszköz](https://docs.microsoft.com/azure/documentdb/documentdb-import-data) | I | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Microsoft adatkezelés átjáró](https://msdn.microsoft.com/library/dn879362.aspx): Az helyszíni és a felhő közötti adatáthelyezés | I | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* Unix/Linux parancssor eszközök | I | I |
| [Apache Drill](https://drill.apache.org) adatok feltárásához | I | I |
| **Machine Learning eszközök** |||
| &nbsp;&nbsp;&nbsp;&nbsp;* Integráció az [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) szolgáltatással (R, Python) | I | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Xgboost](https://github.com/dmlc/xgboost) | I | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) | I | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Weka](https://www.cs.waikato.ac.nz/ml/weka/) | I | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Rattle](https://togaware.com/rattle/) | I | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [LightGBM](https://github.com/Microsoft/LightGBM) | N | I (csak Ubuntu) |
| &nbsp;&nbsp;&nbsp;&nbsp;* [CatBoost](https://tech.yandex.com/catboost/) | N | I (csak Ubuntu) |
| &nbsp;&nbsp;&nbsp;&nbsp;* [H2O](https://www.h2o.ai/h2o/), [szénsavas víz](https://www.h2o.ai/sparkling-water/) | N | I (csak Ubuntu) |
| **Deep Learning eszközök** <br>Minden eszköz működik GPU-n és CPU-n is |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/) (Windows 2016) | I | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [TensorFlow](https://www.tensorflow.org/) | I (Windows 2016) | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Horovod](https://github.com/uber/horovod) | N | I (Ubuntu) |
| &nbsp;&nbsp;&nbsp;&nbsp;* [MXNet](https://mxnet.io/) | I (Windows 2016) | I|
| &nbsp;&nbsp;&nbsp;&nbsp;* [Caffe & Caffe2](https://github.com/caffe2/caffe2) | N | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Chainer](https://chainer.org/) | N | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Torch](http://torch.ch/) | N | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Theano](https://github.com/Theano/Theano) | N | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Keras](https://keras.io/)| N | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [PyTorch](https://pytorch.org/)| N | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [NVidia Digits](https://github.com/NVIDIA/DIGITS) | N | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [MXNet Model Server](https://github.com/awslabs/mxnet-model-server) | N | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [TensorFlow Serving](https://www.tensorflow.org/serving/) | N | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [TensorRT](https://developer.nvidia.com/tensorrt) | N | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [CUDA, cuDNN, NVIDIA illesztőprogram](https://developer.nvidia.com/cuda-toolkit) | I | I |
| **Big Data Platform (csak Devtest)**|||
| &nbsp;&nbsp;&nbsp;&nbsp;* Helyszíni különálló [Spark](https://spark.apache.org/) | I | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* Local [Hadoop](https://hadoop.apache.org/) (HDFS, YARN) | N | I |

## <a name="get-started"></a>Bevezetés

### <a name="windows-data-science-vm"></a>Windows rendszerű adatelemző virtuális gép
* Windowsos DSVM létrehozásáról és használatáról a [Windows rendszerű adatelemző virtuális gép üzembe helyezése](provision-vm.md) című cikk nyújt bővebb tájékoztatást. Az adatelemzési projektjéhez szükséges különböző tevékenységek windowsos adatelemző virtuális gépen való végrehajtásának módjáról a [Tíz dolog, amit megtehet az adatelemző virtuális géppel](vm-do-ten-things.md) című cikk ír bővebben.

### <a name="linux-data-science-vm"></a>Linux rendszerű adatelemző virtuális gép
* Ubuntus DSVM létrehozásáról és használatáról az [Adatelemző virtuális gép üzembe helyezése Linuxon (Ubuntu)](dsvm-ubuntu-intro.md) című cikk nyújt bővebb tájékoztatást. CentOS-es DSVM létrehozásáról és használatáról az [Linux CentOS-es adatelemző virtuális gép üzembe helyezése az Azure-on](linux-dsvm-intro.md) című cikk nyújt bővebb tájékoztatást.
* A gyakori adatelemzési feladatok végrehajtását CentOS és Ubuntu Linux rendszeren is bemutató útmutatást talál az [Adatelemzés a linuxos adatelemző virtuális gépen](linux-dsvm-walkthrough.md) című cikkben.

## <a name="next-steps"></a>További lépések
[Az Azure R fejlesztői útmutatója](/azure/architecture/data-guide/technology-choices/r-developers-guide)
