---
title: A helyszíni HDFS-tárolóból az Azure Storage-ba történő Migrálás Azure Data Box használata
description: Adatok migrálása helyszíni HDFS-tárolóból az Azure Storage-ba
author: normesta
ms.service: storage
ms.date: 06/11/2019
ms.author: normesta
ms.topic: conceptual
ms.subservice: data-lake-storage-gen2
ms.reviewer: jamesbak
ms.openlocfilehash: ff23b27b73918734e10a481cbe9b1f77519b8764
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68847255"
---
# <a name="use-azure-data-box-to-migrate-data-from-an-on-premises-hdfs-store-to-azure-storage"></a>A helyszíni HDFS-tárolóból az Azure Storage-ba történő Migrálás Azure Data Box használata

Az adatok áttelepíthetők a Hadoop-fürt helyszíni HDFS az Azure Storage-ba (blob Storage vagy Data Lake Storage Gen2) egy Data Box eszköz használatával. 80 TB-os Data Box vagy 770-TB Data Box Heavy közül választhat.

Ez a cikk a következő feladatok elvégzését segíti elő:

> [!div class="checklist"]
> * Készítse elő az adatáttelepítés előkészítését.
> * Másolja az adatait egy Data Box vagy egy Data Box Heavy eszközre.
> * Az eszköz újbóli szállítása a Microsoftnak.
> * Helyezze át az adataikat Data Lake Storage Gen2ba.

## <a name="prerequisites"></a>Előfeltételek

Az áttelepítés befejezéséhez ezeket a dolgokat kell végrehajtania.

* Két Storage-fiók; az egyik, hogy engedélyezve van egy hierarchikus névtér, és egy nem.

* A forrásadatokat tartalmazó helyszíni Hadoop-fürt.

* Egy [Azure Data Box eszköz](https://azure.microsoft.com/services/storage/databox/).

  * [Rendeljen Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-ordered) vagy [Data Box Heavy](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-ordered). Az eszköz megrendelése közben ne felejtsen el olyan Storage-fiókot választani, amelyen nincs engedélyezve a hierarchikus névterek. Ennek az az oka, hogy Data Box-eszközök még nem támogatják a közvetlen betöltést Azure Data Lake Storage Gen2ba. Egy Storage-fiókba kell másolnia, majd második másolatot kell készítenie a ADLS Gen2-fiókba. Ehhez az alábbi lépésekben talál útmutatást.

  * Csatlakoztassa a [Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up) vagy [Data Box Heavy](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-set-up) a helyszíni hálózathoz.

Ha készen áll, kezdjük.

## <a name="copy-your-data-to-a-data-box-device"></a>Az adatai másolása egy Data Box eszközre

Ha az adatai egyetlen Data Box eszközhöz illeszkednek, akkor az adatait a Data Box eszközre kell másolni. 

Ha az adatok mérete meghaladja a Data Box eszköz kapacitását, akkor a [választható eljárással több Data Box eszközön is feloszthatja az](#appendix-split-data-across-multiple-data-box-devices) adatmennyiséget, majd végrehajthatja ezt a lépést. 

Ha a helyszíni HDFS-tárolóból egy Data Box eszközre kívánja másolni az adatait, állítson be néhány dolgot, majd használja a [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) eszközt.

Kövesse az alábbi lépéseket az adatok másolásához a blob/objektum tároló REST API-kon keresztül a Data Box eszközre. Az REST API felületen az eszköz HDFS-tárolóként jelenik meg a fürtben.

1. Az adatok REST használatával történő másolása előtt azonosítsa azokat a biztonsági és kapcsolati primitíveket, amelyek a Data Box vagy Data Box Heavy REST-felületéhez csatlakoznak. Jelentkezzen be Data Box helyi webes felhasználói felületére, és lépjen a **Kapcsolódás és másolás** lapra. Az eszközhöz tartozó Azure Storage-fiókhoz a **hozzáférési beállítások**területen keresse meg és válassza a **Rest**lehetőséget.

    !["Kapcsolat és másolás" oldal](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connect-rest.png)

2. A hozzáférés a Storage-fiókhoz és az adatok feltöltése párbeszédpanelen másolja a **blob Service** -végpontot és a **Storage-fiók kulcsát**. A blob Service-végpontból hagyja ki `https://` a és a záró perjelet.

    Ebben az esetben a végpont a következő: `https://mystorageaccount.blob.mydataboxno.microsoftdatabox.com/`. A használni kívánt URI Host része a következők egyike: `mystorageaccount.blob.mydataboxno.microsoftdatabox.com`. Példa: [Kapcsolódás a REST-hez http-](/azure/databox/data-box-deploy-copy-data-via-rest)kapcsolaton keresztül. 

     !["Hozzáférés a Storage-fiókhoz és adatok feltöltése" párbeszédpanel](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connection-string-http.png)

3. Adja hozzá a végpontot és a Data Box vagy Data Box Heavy csomópont IP `/etc/hosts` -címét az egyes csomópontokon.

    ```    
    10.128.5.42  mystorageaccount.blob.mydataboxno.microsoftdatabox.com
    ```

    Ha más DNS-mechanizmust használ, győződjön meg arról, hogy a Data Box végpont feloldható.

4. Állítsa a rendszerhéj változót `azjars` a `hadoop-azure` és `azure-storage` JAR fájlok helyére. Ezek a fájlok a Hadoop telepítési könyvtárában találhatók.

    Annak megállapításához, hogy ezek a fájlok léteznek-e, `ls -l $<hadoop_install_dir>/share/hadoop/tools/lib/ | grep azure`használja a következő parancsot:. Cserélje le `<hadoop_install_dir>` a helyőrzőt annak a könyvtárnak az elérési útjára, amelyen a Hadoop telepítette. Ügyeljen arra, hogy teljesen minősített elérési utakat használjon.

    Példák:

    `azjars=$hadoop_install_dir/share/hadoop/tools/lib/hadoop-azure-2.6.0-cdh5.14.0.jar` `azjars=$azjars,$hadoop_install_dir/share/hadoop/tools/lib/microsoft-windowsazure-storage-sdk-0.6.0.jar`

5. Hozza létre az adatmásoláshoz használni kívánt Storage-tárolót. A parancs részeként meg kell adnia egy célhely könyvtárat is. Ez lehet egy dummy cél könyvtára ezen a ponton.

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -mkdir -p  wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * Cserélje le `<blob_service_endpoint>` a helyőrzőt a blob Service-végpont nevére.

    * Cserélje le `<account_key>` a helyőrzőt a fiókja hozzáférési kulcsára.

    * Cserélje le `<container-name>` a helyőrzőt a tároló nevére.

    * Cserélje le `<destination_directory>` a helyőrzőt annak a könyvtárnak a nevére, amelyre az adatait másolni kívánja.

6. A LIST parancs futtatásával győződjön meg arról, hogy a tároló és a könyvtár létrejött.

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -ls -R  wasb://<container_name>@<blob_service_endpoint>/
    ```

   * Cserélje le `<blob_service_endpoint>` a helyőrzőt a blob Service-végpont nevére.

   * Cserélje le `<account_key>` a helyőrzőt a fiókja hozzáférési kulcsára.

   * Cserélje le `<container-name>` a helyőrzőt a tároló nevére.

7. Adatok másolása a Hadoop-HDFS Data Box blob Storage-ba a korábban létrehozott tárolóba. Ha a másolandó címtár nem található, a parancs automatikusan létrehozza azt.

    ```
    hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint<>=<account_key> \
    -filters <exclusion_filelist_file> \
    [-f filelist_file | /<source_directory> \
           wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * Cserélje le `<blob_service_endpoint>` a helyőrzőt a blob Service-végpont nevére.

    * Cserélje le `<account_key>` a helyőrzőt a fiókja hozzáférési kulcsára.

    * Cserélje le `<container-name>` a helyőrzőt a tároló nevére.

    * Cserélje le `<exlusion_filelist_file>` a helyőrzőt annak a fájlnak a nevére, amely a fájl-kizárások listáját tartalmazza.

    * Cserélje le `<source_directory>` a helyőrzőt annak a könyvtárnak a nevére, amely a másolni kívánt adatmennyiséget tartalmazza.

    * Cserélje le `<destination_directory>` a helyőrzőt annak a könyvtárnak a nevére, amelyre az adatait másolni kívánja.

    Ez `-libjars` a beállítás a `hadoop-azure*.jar` és `azure-storage*.jar` afüggőfájlokelérhetővétételéreszolgál.`distcp` Előfordulhat, hogy néhány fürt esetében ez már előfordulhat.

    Az alábbi példa azt szemlélteti, `distcp` hogy a parancs hogyan használható az adatmásoláshoz.

    ```
     hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.mystorageaccount.blob.mydataboxno.microsoftdatabox.com=myaccountkey \
    -filter ./exclusions.lst -f /tmp/copylist1 -m 4 \
    /data/testfiles \
    wasb://hdfscontainer@mystorageaccount.blob.mydataboxno.microsoftdatabox.com/data
    ```
  
    A másolás sebességének javítása:

    * Próbálja meg módosítani a leképezések számát. (A fenti példa a `m` = 4 mappers szolgáltatást használja.)

    * Próbálkozzon egyszerre több `distcp` párhuzamos futtatással.

    * Ne feledje, hogy a nagyméretű fájlok jobb teljesítményt biztosítanak a kisebb fájloknál.

## <a name="ship-the-data-box-to-microsoft"></a>A Data Box elszállítása a Microsoftnak

Az alábbi lépéseket követve előkészítheti és szállíthatja a Data Box eszközt a Microsoftnak.

1. Először [szállításra való előkészítés a Data Box vagy a Data Box Heavy](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-rest).

2. Az eszköz előkészítésének befejezése után töltse le az ANYAGJEGYZÉK-fájlokat. Ezeket az ANYAGJEGYZÉK-vagy MANIFEST-fájlokat később fogja használni az Azure-ba feltöltött adatkezelés ellenőrzéséhez.

3. Állítsa le az eszközt, és távolítsa el a kábeleket.

4. Pickupok beosztása a UPS-szel.

    * Data Box eszközökhöz tekintse [meg a Data Box szállítása](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up)című témakört.

    * Data Box Heavy eszközökhöz tekintse [meg a Data Box Heavy szállítása](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-picked-up)című témakört.

5. Miután a Microsoft megkapja az eszközét, az az adatközpont-hálózathoz csatlakozik, és a rendszer feltölti az adatait a megadott Storage-fiókba (a hierarchikus névterek letiltásával) az eszköz sorrendjének elhelyezésekor. Ellenőrizze azokat az ANYAGJEGYZÉK-fájlokat, amelyeket az összes adattal feltölt az Azure-ba. Ezután áthelyezheti ezeket az adatData Lake Storage Gen2 Storage-fiókba.

## <a name="move-the-data-into-azure-data-lake-storage-gen2"></a>Az Azure Data Lake Storage Gen2ba helyezi át az adatátvitelt

Már rendelkezik az Azure Storage-fiókjában tárolt adataival. Most átmásolja az adatait a Azure Data Lake Storage-fiókjába, és hozzáférési engedélyeket fog alkalmazni a fájlokhoz és könyvtárakhoz.

> [!NOTE]
> Erre a lépésre akkor van szükség, ha az adattárat Azure Data Lake Storage Gen2 használja. Ha csak egy blob Storage-fiókot használ hierarchikus névtér nélküli adattárként, akkor kihagyhatja ezt a szakaszt.

### <a name="copy-data-to-the-azure-data-lake-storage-gen-2-account"></a>Adatmásolás a Azure Data Lake Storage Gen 2 fiókba

Az Adatmásolás Azure Data Factory használatával vagy az Azure-alapú Hadoop-fürt használatával végezhető el.

* A Azure Data Factory használatához lásd: [Azure Data Factory az adatáthelyezéshez ADLS Gen2](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2). Győződjön meg arról, hogy az **Azure Blob Storage** forrásként van megadva.

* Az Azure-alapú Hadoop-fürt használatához futtassa ezt a DistCp-parancsot:

    ```bash
    hadoop distcp -Dfs.azure.account.key.<source_account>.dfs.windows.net=<source_account_key> abfs://<source_container> @<source_account>.dfs.windows.net/<source_path> abfs://<dest_container>@<dest_account>.dfs.windows.net/<dest_path>
    ```

    * Cserélje le `<source_account>` a `<dest_account>` és a helyőrzőket a forrás és a cél Storage-fiókok nevére.

    * Cserélje le `<source_container>` a `<dest_container>` és a helyőrzőket a forrás és a cél tárolók nevére.

    * Cserélje le `<source_path>` a `<dest_path>` és a helyőrzőket a forrás és a cél könyvtár elérési útjaira.

    * Cserélje le `<source_account_key>` a helyőrzőt az adathalmazt tartalmazó Storage-fiók elérési kulcsára.

    Ez a parancs a Storage-fiókból származó adatokat és metaadatokat a Data Lake Storage Gen2 Storage-fiókba másolja.

### <a name="create-a-service-principal-for-your-azure-data-lake-storage-gen2-account"></a>Egyszerű szolgáltatásnév létrehozása a Azure Data Lake Storage Gen2-fiókhoz

Egyszerű szolgáltatásnév létrehozásához tekintse [meg a How to: A portál használatával létrehozhat egy Azure AD-alkalmazást és egy egyszerű szolgáltatásnevet, amely hozzáférhet](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)az erőforrásokhoz.

* Ha végrehajtja az [alkalmazás szerepkörhöz rendelése](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) szakaszának lépéseit, akkor ügyeljen arra, hogy hozzárendelje a **tárolási blob adatközreműködői** szerepkört az egyszerű szolgáltatáshoz.

* A cikk, az alkalmazás-azonosító és az ügyfél titkos értékeinek szövegfájlba való beolvasásához szükséges [értékek](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) beolvasása című szakasz lépéseinek végrehajtásakor. Ezekre hamarosan szüksége lesz.

### <a name="generate-a-list-of-copied-files-with-their-permissions"></a>A másolt fájlok listájának létrehozása engedélyekkel

Futtassa a következő parancsot a helyszíni Hadoop-fürtből:

```bash

sudo -u hdfs ./copy-acls.sh -s /{hdfs_path} > ./filelist.json
```

Ez a parancs a másolt fájlok listáját hozza létre az engedélyeik alapján.

> [!NOTE]
> A HDFS lévő fájlok számától függően ez a parancs hosszú időt is igénybe vehet.

### <a name="generate-a-list-of-identities-and-map-them-to-azure-active-directory-add-identities"></a>Identitások listájának létrehozása és a Azure Active Directory (Hozzáadás) identitások leképezése

1. Töltse le `copy-acls.py` a szkriptet. Tekintse [meg a Súgó letöltése segítő parancsfájlokat, és állítsa be az Edge-csomópontot](#download-helper-scripts) a cikk futtatásához című szakaszát.

2. Futtassa ezt a parancsot az egyedi identitások listájának létrehozásához.

   ```bash
   
   ./copy-acls.py -s ./filelist.json -i ./id_map.json -g
   ```

   Ez a szkript létrehoz egy nevű `id_map.json` fájlt, amely tartalmazza azokat az identitásokat, amelyeket hozzá kell rendelni a Hozzáadás-alapú identitásokhoz.

3. Nyissa `id_map.json` meg a fájlt egy szövegszerkesztőben.

4. Minden olyan JSON-objektum esetében, amely megjelenik a fájlban, `target` frissítse a megfelelő leképezett identitású HRE (UPN) vagy ObjectId (OID) attribútumot. Ha elkészült, mentse a fájlt. Ezt a fájlt a következő lépésben kell megadnia.

### <a name="apply-permissions-to-copied-files-and-apply-identity-mappings"></a>Engedélyek alkalmazása a másolt fájlokra és az identitás-hozzárendelések alkalmazása

Futtassa ezt a parancsot a Data Lake Storage Gen2-fiókba másolt adathozzáférésre vonatkozó engedélyek alkalmazásához:

```bash
./copy-acls.py -s ./filelist.json -i ./id_map.json  -A <storage-account-name> -C <container-name> --dest-spn-id <application-id>  --dest-spn-secret <client-secret>
```

* Cserélje le `<storage-account-name>` a helyőrzőt a Storage-fiók nevére.

* Cserélje le `<container-name>` a helyőrzőt a tároló nevére.

* Cserélje le `<application-id>` a `<client-secret>` és a helyőrzőket az alkalmazás-azonosítóra és az ügyfél titkos kódjára, amelyet az egyszerű szolgáltatás létrehozásakor gyűjtött.

## <a name="appendix-split-data-across-multiple-data-box-devices"></a>Függelék Az adatfelosztás több Data Box eszköz között

Mielőtt áthelyezi az adatait egy Data Box eszközre, le kell töltenie néhány segítő parancsfájlt, meg kell győződnie arról, hogy az adatai úgy vannak rendszerezve, hogy illeszkedjenek egy Data Box eszközre, és kizárják a szükségtelen fájlokat.

<a id="download-helper-scripts" />

### <a name="download-helper-scripts-and-set-up-your-edge-node-to-run-them"></a>Segítő parancsfájlok letöltése és az Edge-csomópont beállítása a futtatásához

1. Futtassa a következő parancsot a helyszíni Hadoop-fürt Edge vagy Head csomópontján:

   ```bash
   
   git clone https://github.com/jamesbak/databox-adls-loader.git
   cd databox-adls-loader
   ```

   Ez a parancs a GitHub-tárházat klónozott, amely a segítő parancsfájlokat tartalmazza.

2. Győződjön meg arról, hogy a [jQ](https://stedolan.github.io/jq/) -csomag telepítve van a helyi számítógépen.

   ```bash
   
   sudo apt-get install jq
   ```

3. Telepítse a [](http://docs.python-requests.org/en/master/) Python-csomag kéréseit.

   ```bash
   
   pip install requests
   ```

4. Állítsa be a végrehajtási engedélyeket a szükséges parancsfájlokra.

   ```bash
   
   chmod +x *.py *.sh

   ```

### <a name="ensure-that-your-data-is-organized-to-fit-onto-a-data-box-device"></a>Győződjön meg arról, hogy az adatai úgy vannak rendszerezve, hogy illeszkedjenek Data Box eszközre

Ha az adatméret meghaladja az egyetlen Data Box eszköz méretét, akkor a fájlokat a több Data Box eszközön tárolható csoportokba is feloszthatja.

Ha az adatai nem haladják meg a Singing Data Box eszköz méretét, folytassa a következő szakasszal.

1. Emelt szintű engedélyekkel futtassa `generate-file-list` az előző szakasz útmutatását követve a letöltött szkriptet.

   A parancs paramétereinek leírása:

   ```
   sudo -u hdfs ./generate-file-list.py [-h] [-s DATABOX_SIZE] [-b FILELIST_BASENAME]
                    [-f LOG_CONFIG] [-l LOG_FILE]
                    [-v {DEBUG,INFO,WARNING,ERROR}]
                    path

   where:
   positional arguments:
   path                  The base HDFS path to process.

   optional arguments:
   -h, --help            show this help message and exit
   -s DATABOX_SIZE, --databox-size DATABOX_SIZE
                        The size of each Data Box in bytes.
   -b FILELIST_BASENAME, --filelist-basename FILELIST_BASENAME
                        The base name for the output filelists. Lists will be
                        named basename1, basename2, ... .
   -f LOG_CONFIG, --log-config LOG_CONFIG
                        The name of a configuration file for logging.
   -l LOG_FILE, --log-file LOG_FILE
                        Name of file to have log output written to (default is
                        stdout/stderr)
   -v {DEBUG,INFO,WARNING,ERROR}, --log-level {DEBUG,INFO,WARNING,ERROR}
                        Level of log information to output. Default is 'INFO'.
   ```

2. Másolja a generált fájlok listáját a HDFS, hogy elérhetők legyenek a [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) -feladatokhoz.

   ```
   hadoop fs -copyFromLocal {filelist_pattern} /[hdfs directory]
   ```

### <a name="exclude-unnecessary-files"></a>Szükségtelen fájlok kizárása

Néhány könyvtárat ki kell zárnia a DisCp feladatokból. Kizárhatja például azokat a címtárakat, amelyek olyan állapotinformációkat tartalmaznak, amelyek a fürtöt futtatják.

Hozzon létre egy fájlt azon a helyszíni Hadoop-fürtön, ahol el szeretné indítani a DistCp-feladatot. a kizárni kívánt könyvtárak listáját meghatározó fájl létrehozása.

Például:

```
.*ranger/audit.*
.*/hbase/data/WALs.*
```

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan működik a Data Lake Storage Gen2 HDInsight-fürtökkel. Lásd: [Azure Data Lake Storage Gen2 használata az Azure HDInsight](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md)-fürtökkel.
