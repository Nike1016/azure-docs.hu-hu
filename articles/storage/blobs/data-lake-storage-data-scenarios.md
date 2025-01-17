---
title: Az Azure Data Lake Storage Gen2t érintő adatforgatókönyvek | Microsoft Docs
description: Megismerheti azokat a különböző forgatókönyveket és eszközöket, amelyek segítségével az adatok betölthetők, feldolgozhatók, letölthetők és megjeleníthetők Data Lake Storage Gen2ban (korábbi nevén Azure Data Lake Store)
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: cafe761d2b566a7bddce503765c11bf9f8e00f2a
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68847455"
---
# <a name="using-azure-data-lake-storage-gen2-for-big-data-requirements"></a>A Azure Data Lake Storage Gen2 használata big data követelményekhez

A big data feldolgozásának négy fő szakasza van:

> [!div class="checklist"]
> * Nagy mennyiségű adatot tölt be egy adattárba valós időben vagy kötegekben
> * Az adatfeldolgozás
> * Az adatletöltés
> * Az adatmegjelenítés

Először hozzon létre egy Storage-fiókot és egy fájlrendszert. Ezután adjon hozzáférést az adathoz. Ennek a cikknek az első néhány része segít ezeknek a feladatoknak a megvalósításában. A többi szakaszban kiemeljük az egyes feldolgozási fázisok beállításait és eszközeit.

## <a name="create-a-data-lake-storage-gen2-account"></a>Data Lake Storage Gen2 fiók létrehozása

A Data Lake Storage Gen2 fiók olyan Storage-fiók, amely hierarchikus névtérrel rendelkezik. 

A létrehozáshoz tekintse [meg a gyors útmutató: Hozzon létre egy Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-account?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)Storage-fiókot.

## <a name="create-a-file-system"></a>Fájlrendszer létrehozása

A *fájlrendszer* a mappák és fájlok tárolója. Legalább az egyiknek szüksége van arra, hogy megkezdje az adatbevitelt a Storage-fiókban.  Az alábbi lista felsorolja azokat az eszközöket, amelyeket a létrehozásához használhat.

|Eszköz | Útmutatás |
|---|--|
|Azure Storage Explorer | [Fájlrendszer létrehozása Storage Explorer használatával](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-explorer#create-a-file-system) |
|AzCopy | [BLOB-tároló vagy fájlmegosztás létrehozása a AzCopyV10 használatával](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10#transfer-files)|
|Hadoop File System (HDFS) parancssori felület (CLI) és HDInsight |[Fájlrendszer létrehozása a HDFS és a HDInsight használatával](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-hdfs-data-lake-storage?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-a-file-system) |
|Kód Azure Databricks jegyzetfüzetben|[Storage-fiók fájlrendszerének (Scala) létrehozása](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-databricks-account?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-storage-account-file-system) <br><br> [Fájlrendszer létrehozása és csatlakoztatása (Python)](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-databricks-spark?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-a-file-system-and-mount-it)|

A legegyszerűbben Storage Explorer vagy AzCopy használatával hozhat létre fájlrendszereket. A HDInsight és a Databricks használatával a fájlrendszerek létrehozásához valamivel több munka szükséges. Ha azonban HDInsight-vagy Databricks-fürtöket kíván használni az adatfeldolgozáshoz, akkor először létre kell hoznia a fürtöket, és a HDFS CLI-vel kell használnia a fájlrendszerek létrehozásához.  

## <a name="grant-access-to-the-data"></a>Hozzáférés biztosítása az adathoz

A megfelelő hozzáférési engedélyek beállítása a fiókhoz és a fiókban lévő adataihoz az adatbevitel megkezdése előtt.

A hozzáférés három módon biztosítható:

* Rendelje hozzá a szerepkörök egyikét egy felhasználóhoz, csoporthoz, felhasználó által felügyelt identitáshoz vagy egyszerű szolgáltatáshoz:

  [Storage blob-Adatolvasó](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader)

  [Storage blob adatközreműködői](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor)

  [Storage blob-adattulajdonos](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)

* Használjon egy közös hozzáférésű aláírás (SAS) tokent.

* Használjon Storage-fiók kulcsát.

Ez a táblázat bemutatja, hogyan biztosíthat hozzáférést az egyes Azure-szolgáltatásokhoz vagy eszközökhöz.

|Eszköz | Hozzáférés biztosítása | Útmutatás |
|---|--|---|
|Storage Explorer| Szerepkör társítása felhasználókhoz és csoportokhoz | [Rendszergazdai és nem rendszergazdai szerepkörök kiosztása a felhasználóknak a Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal) |
|AzCopy| Szerepkör társítása felhasználókhoz és csoportokhoz <br>**or**<br> SAS-token használata| [Rendszergazdai és nem rendszergazdai szerepkörök kiosztása a felhasználóknak a Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)<br><br>[Egyszerű SAS létrehozása az Azure Storage-ból származó fájlok letöltéséhez Azure Storage Explorer használatával](https://blogs.msdn.microsoft.com/jpsanders/2017/10/12/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer/)|
|Apache DistCp | Szerepkör hozzárendelése felhasználóhoz rendelt felügyelt identitáshoz | [HDInsight-fürt létrehozása Data Lake Storage Gen2](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2) |
|Azure Data Factory| Szerepkör hozzárendelése felhasználó által hozzárendelt felügyelt identitáshoz<br>**or**<br> Szerepkör hozzárendelése való kihelyezése<br>**or**<br> A Storage-fiók kulcsainak használata | [Társított szolgáltatás tulajdonságai](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#linked-service-properties) |
|Azure HDInsight| Szerepkör hozzárendelése felhasználóhoz rendelt felügyelt identitáshoz | [HDInsight-fürt létrehozása Data Lake Storage Gen2](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2)|
|Azure Databricks| Szerepkör kiosztása egy egyszerű szolgáltatáshoz | [Útmutató: Az erőforrásokhoz hozzáférő Azure AD-alkalmazás és -szolgáltatásnév létrehozása a portálon](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)|

Az adott fájlokhoz és mappákhoz való hozzáférés biztosításához tekintse meg ezeket a cikkeket.

* [Fájl-és könyvtár-szintű engedélyek beállítása Azure Storage Explorer és Azure Data Lake Storage Gen2 használatával](https://review.docs.microsoft.com/azure/storage/blobs/data-lake-storage-how-to-set-permissions-storage-explorer)

* [Fájlok és könyvtárak hozzáférés-vezérlési listája](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control#access-control-lists-on-files-and-directories)

A biztonság egyéb szempontjainak beállításával kapcsolatos további tudnivalókért tekintse meg a [Azure Data Lake Storage Gen2 biztonsági útmutatót](https://docs.microsoft.com/azure/storage/common/storage-data-lake-storage-security-guide?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="ingest-the-data"></a>Az adatok betöltése

Ez a szakasz kiemeli a különböző adatforrásokat, valamint azokat a különböző módokat, amelyekben az adott adatot betöltheti egy Data Lake Storage Gen2-fiókba.

![Adatbevitel a Data Lake Storage Gen2ba](./media/data-lake-storage-data-scenarios/ingest-data.png "Adatbevitel a Data Lake Storage Gen2ba")

### <a name="ad-hoc-data"></a>Ad hoc adatszolgáltatások

Ez kisebb adathalmazokat jelent, amelyeket egy big data alkalmazás prototípusához használ. Az ad hoc adatmennyiség különböző módokon végezhető el az adatforrástól függően. 

Az alábbi lista azokat az eszközöket tartalmazza, amelyeket az ad hoc adatbevitelhez használhat.

| Adatforrás | A |
| --- | --- |
| Helyi számítógép |[Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)<br><br>[AzCopy eszköz](../common/storage-use-azcopy-v10.md)|
| Azure Storage Blob |[Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md)<br><br>[AzCopy eszköz](../common/storage-use-azcopy-v10.md)<br><br>[A HDInsight-fürtön futó DistCp](data-lake-storage-use-distcp.md)|

### <a name="streamed-data"></a>Továbbított adattartalom

Ez olyan adatforrásokat jelent, amelyeket különböző források, például alkalmazások, eszközök, érzékelők stb. hozhatnak létre. Ezeket az adatmennyiségeket különböző eszközök segítségével Data Lake Sorage Gen2. Ezek az eszközök általában valós időben rögzítik és feldolgozzák az adott eseményt, majd a kötegekben lévő eseményeket a Data Lake Storage Gen2ba írják, hogy tovább lehessen feldolgozni őket.

Az alábbi lista azokat az eszközöket tartalmazza, amelyek segítségével betöltheti a továbbított adatmennyiséget.

|Eszköz | Útmutatás |
|---|--|
|Azure HDInsight Storm | [Írás a HDInsight Apache Storm HDFS Apache Hadoop](https://docs.microsoft.com/azure/hdinsight/storm/apache-storm-write-data-lake-store) |

### <a name="relational-data"></a>Relációs adatok

A kapcsolódó adatbázisok adatait is megtekintheti. Egy adott időszakban a kapcsolati adatbázisok nagy mennyiségű adatot gyűjtenek, amelyek kulcsfontosságú elemzéseket biztosítanak, ha big data folyamaton keresztül dolgozzák fel őket. Az alábbi eszközök használatával helyezheti át ezeket az eszközöket Data Lake Storage Gen2ba.

Az alábbi lista azokat az eszközöket tartalmazza, amelyekkel a rendszer a kapcsolatok adatfeldolgozását is felhasználhatja.

|Eszköz | Útmutatás |
|---|--|
|Azure Data Factory | [Másolási tevékenység az Azure Data Factoryben](https://docs.microsoft.com/azure/data-factory/copy-activity-overview) |

### <a name="web-server-log-data-upload-using-custom-applications"></a>Webkiszolgáló naplójának adatai (egyéni alkalmazásokkal történő feltöltés)

Az ilyen típusú adathalmazokat a rendszer kifejezetten felhívta, mert a webkiszolgáló naplófájljainak elemzése gyakori használati eset big data alkalmazásokhoz, és nagy mennyiségű naplófájlt igényel a Data Lake Storage Gen2ba való feltöltéshez. Az alábbi eszközök bármelyikével írhat saját parancsfájlokat vagy alkalmazásokat az ilyen adatok feltöltéséhez.

Az alábbi lista azokat az eszközöket tartalmazza, amelyek segítségével betöltheti a webkiszolgáló naplófájljait.

|Eszköz | Útmutatás |
|---|--|
|Azure Data Factory | [Másolási tevékenység az Azure Data Factoryben](https://docs.microsoft.com/azure/data-factory/copy-activity-overview)  |

A webkiszolgáló-naplózási adatainak feltöltéséhez, valamint más típusú adatmennyiségek (például a közösségi érzelmek adatainak) feltöltéséhez jó módszer a saját egyéni parancsfájljainak/alkalmazásainak megírása, mivel a rugalmassága lehetővé teszi, hogy az adatfeltöltési összetevőt a következő részeként tartalmazza: nagyobb big data alkalmazása. Bizonyos esetekben ez a kód parancsfájl vagy egyszerű parancssori segédprogram formájában is elvégezhető. Más esetekben a kód a big data feldolgozás üzleti alkalmazásba vagy megoldásba való integrálására használható.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Az Azure HDInsight-fürtökhöz kapcsolódó adatmennyiség

A legtöbb HDInsight-fürt (Hadoop, HBase, Storm) támogatja az adattároló adattárként való Data Lake Storage Gen2. A HDInsight-fürtök az Azure Storage-Blobokból (WASB) származó adatokhoz férnek hozzá. A jobb teljesítmény érdekében a WASB származó adatok másolása a fürthöz társított Data Lake Storage Gen2-fiókba. Az adatmásoláshoz a következő eszközök használhatók.

Az alábbi lista azokat az eszközöket tartalmazza, amelyekkel a HDInsight-fürtökhöz kapcsolódó adatbevitelt végezheti el.

|Eszköz | Útmutatás |
|---|--|
|Apache DistCp | [Az DistCp használata az Azure Storage-blobok és a Azure Data Lake Storage Gen2 közötti adatmásoláshoz](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-distcp) |
|AzCopy eszköz | [Adatok átvitele a AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10) |
|Azure Data Factory | [Adatok másolása Azure Data Lake Storage Gen2ba vagy onnan a Azure Data Factory használatával](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2) |

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>A helyszíni vagy IaaS Hadoop-fürtökben tárolt adatkészletek

Nagy mennyiségű adattal rendelkezhetnek a meglévő Hadoop-fürtökben, helyileg a HDFS-t használó gépeken. Előfordulhat, hogy a Hadoop-fürtök helyszíni környezetben találhatók, vagy egy Azure-beli IaaS-fürtön belül vannak. Előfordulhat, hogy az ilyen jellegű adatmásolásra vonatkozó követelmények egy egyszeri vagy ismétlődő módon Azure Data Lake Storage Gen2. Ennek eléréséhez számos lehetőség közül választhat. Az alábbiakban felsoroljuk az alternatívák listáját, valamint a hozzájuk kapcsolódó kompromisszumokat.

| A módszer | Részletek | Előnyök | Megfontolandó szempontok |
| --- | --- | --- | --- |
| Azure Data Factory (ADF) használatával közvetlenül a Hadoop-fürtökről másolhatja az adatok Azure Data Lake Storage Gen2 |[Az ADF támogatja a HDFS adatforrásként](../../data-factory/connector-hdfs.md) |Az ADF beépített támogatást nyújt a HDFS és az első osztályú végpontok közötti felügyelethez és figyeléshez |A adatkezelés átjárót a helyszíni vagy a IaaS-fürtön kell telepíteni |
| Adatok másolása a Hadoop-ből az Azure Storage-ba a Distcp használatával. Ezután másolja az Azure Storage-ból származó adatok Data Lake Storage Gen2 a megfelelő mechanizmus használatával. |Az adatok az Azure Storage-ból Data Lake Storage Gen2 a következő használatával másolhatók: <ul><li>[Azure Data Factory](../../data-factory/copy-activity-overview.md)</li><li>[AzCopy eszköz](../common/storage-use-azcopy-v10.md)</li><li>[HDInsight-fürtökön futó Apache DistCp](data-lake-storage-use-distcp.md)</li></ul> |Használhatja a nyílt forráskódú eszközöket. |Többlépéses folyamat, amely több technológiát is magában foglal |

### <a name="really-large-datasets"></a>Nagyon nagy adatkészletek

A több terabájtos tartományba tartozó adatkészletek feltöltéséhez a fent ismertetett módszerek használatával időnként lassú és költséges lehet. Ilyen esetekben használhatja az Azure ExpressRoute-t is.  

Az Azure ExpressRoute lehetővé teszi privát kapcsolatok létrehozását az Azure-adatközpontok és a helyszíni infrastruktúra között. Ez megbízható lehetőséget biztosít a nagy mennyiségű adatátvitelhez. További információt az [Azure ExpressRoute dokumentációjában](../../expressroute/expressroute-introduction.md)talál.

## <a name="process-the-data"></a>Az adatfeldolgozás

Ha az adatai elérhetők a Data Lake Storage Gen2ban, a támogatott big data alkalmazásokkal is futtathatja az elemzést. 

![Data Lake Storage Gen2ban lévő adatelemzés](./media/data-lake-storage-data-scenarios/analyze-data.png "Data Lake Storage Gen2ban lévő adatelemzés")

Az alábbi lista azokat az eszközöket tartalmazza, amelyek segítségével adatelemzési feladatokat futtathat az Data Lake Storage Gen2 tárolt adatokon.

|Eszköz | Útmutatás |
|---|--|
|Azure HDInsight | [Az Azure Data Lake Storage Gen2 használata Azure HDInsight-fürtökkel](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2) |
|Azure Databricks | [Azure Data Lake Storage Gen2](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html)<br><br>[Rövid útmutató: Azure Data Lake Storage Gen2i adatelemzés Azure Databricks használatával](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-databricks-account?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br><br>[Oktatóanyag: Adatok kinyerése, átalakítása és betöltése a Azure Databricks használatával](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

## <a name="visualize-the-data"></a>Az adatok vizualizációja

A szolgáltatások együttes használatával a Data Lake Storage Gen2ban tárolt adatvizualizációk is létrehozhatók.

![Az Data Lake Storage Gen2ban lévő adatmegjelenítés](./media/data-lake-storage-data-scenarios/visualize-data.png "Az Data Lake Storage Gen2ban lévő adatmegjelenítés")

* A Azure Data Factory használatával elindíthatja az [adatok áthelyezését Data Lake Storage Gen2ról Azure SQL Data Warehouse](../../data-factory/copy-activity-overview.md)
* Ezt követően integrálhatja a [Power BIt Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-get-started-visualize-with-power-bi.md) segítségével vizuálisan jelenítheti meg az adatmegjelenítést.

## <a name="download-the-data"></a>Az adatgyűjtés letöltése

Előfordulhat, hogy a következő forgatókönyvek esetében is szeretné letölteni vagy áthelyezni az adatok Azure Data Lake Storage Gen2:

* Más adattárakba helyezheti át az adatait a meglévő adatfeldolgozási folyamatokkal. Előfordulhat például, hogy át szeretné helyezni az adatok Data Lake Storage Gen2ról Azure SQL Database vagy helyszíni SQL Serverre.

* Az alkalmazások prototípusának kiépítése során az IDE-környezetekben tárolt adatfeldolgozási folyamatokat a helyi számítógépre töltheti le.

![Kimenő adatok Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/egress-data.png "Kimenő adatok Data Lake Storage Gen2")

Az alábbi lista azokat az eszközöket tartalmazza, amelyekkel adatok tölthetők le a Data Lake Storage Gen2ról.

|Eszköz | Útmutatás |
|---|--|
|Azure Data Factory | [Másolási tevékenység az Azure Data Factoryben](https://docs.microsoft.com/azure/data-factory/copy-activity-overview) |
|Apache DistCp | [Az DistCp használata az Azure Storage-blobok és a Azure Data Lake Storage Gen2 közötti adatmásoláshoz](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-distcp) |
