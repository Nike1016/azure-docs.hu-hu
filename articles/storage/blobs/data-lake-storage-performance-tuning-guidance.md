---
title: Azure Data Lake Storage Gen2 teljesítmény-hangolási irányelvek | Microsoft Docs
description: Teljesítmény-finomhangolási irányelvek Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: b134842303bebdf10efdf388057c8ad7b3be61be
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68855569"
---
# <a name="tuning-azure-data-lake-storage-gen2-for-performance"></a>A teljesítmény finomhangolása Azure Data Lake Storage Gen2

A Azure Data Lake Storage Gen2 nagy átviteli sebességet biztosít az I/O-intenzív elemzésekhez és az adatáthelyezéshez.  Data Lake Storage Gen2 az összes rendelkezésre álló átviteli sebesség – a másodpercenként olvasható vagy írható adatok mennyisége – fontos a legjobb teljesítmény érdekében.  Ez úgy érhető el, hogy a lehető legtöbb olvasási és írási műveletet végrehajtja.

![Data Lake Storage Gen2 teljesítmény](./media/data-lake-storage-performance-tuning-guidance/throughput.png)

A Data Lake Storage Gen2 méretezheti, hogy az összes elemzési forgatókönyv esetében elérhető legyen a szükséges átviteli sebesség. Alapértelmezés szerint egy Data Lake Storage Gen2 fiók automatikusan elegendő átviteli sebességet biztosít, hogy megfeleljen a használati esetek széles kategóriájának. Azokban az esetekben, amikor az ügyfelek az alapértelmezett korláton futnak, a Data Lake Storage Gen2 fiók úgy konfigurálható, hogy az [Azure támogatási szolgálatával](https://azure.microsoft.com/support/faq/)további átviteli sebességet biztosítson.

## <a name="data-ingestion"></a>Adatfeldolgozás

Amikor adatfeldolgozást végez egy forrásoldali rendszerből a Data Lake Storage Gen2ba, fontos figyelembe venni, hogy a forrás hardver, a forrás hálózati hardver és a Data Lake Storage Gen2 hálózati kapcsolata a szűk keresztmetszet.  

![Data Lake Storage Gen2 teljesítmény](./media/data-lake-storage-performance-tuning-guidance/bottleneck.png)

Fontos, hogy az adatáthelyezést ne befolyásolja ezek a tényezők.

### <a name="source-hardware"></a>Forrás hardver

Függetlenül attól, hogy a helyszíni gépeket vagy virtuális gépeket használja az Azure-ban, gondosan válassza ki a megfelelő hardvert. A forrásoldali lemezes hardverek esetében inkább az SSD-ket használja a HDD-k számára, és gyorsabb orsókkal válassza a lemez hardverét. A forrásoldali hálózati hardverek esetében a lehető leggyorsabb hálózati adaptereket használhatja.  Az Azure-ban olyan Azure D14 virtuális gépeket ajánlunk, amelyek megfelelő teljesítményű lemezzel és hálózati hardverrel rendelkeznek.

### <a name="network-connectivity-to-data-lake-storage-gen2"></a>Data Lake Storage Gen2 hálózati kapcsolat

A forrásadatok és a Data Lake Storage Gen2 közötti hálózati kapcsolat esetenként szűk keresztmetszetet jelenthet. Ha a forrásadatok helyszíniek, érdemes lehet dedikált hivatkozást használni az [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) . Ha a forrásadatok az Azure-ban találhatók, akkor a teljesítmény akkor a legjobb, ha az adat ugyanabban az Azure-régióban található, mint a Data Lake Storage Gen2 fiók.

### <a name="configure-data-ingestion-tools-for-maximum-parallelization"></a>Adatfeldolgozási eszközök konfigurálása a maximális párhuzamos

Miután megoldotta a forrás hardver és a hálózati kapcsolat szűk keresztmetszetét, készen áll a betöltési eszközök konfigurálására. A következő táblázat összefoglalja a számos népszerű betöltési eszköz főbb beállításait, és részletes teljesítmény-finomhangolási cikkeket biztosít számukra.  Ha többet szeretne megtudni a forgatókönyvhöz használt eszközről, tekintse meg ezt a [cikket](data-lake-storage-data-scenarios.md).

| Eszköz               | Beállítások     | További részletek                                                                 |
|--------------------|------------------------------------------------------|------------------------------|
| DistCp            | -m (Mapper)   | [Hivatkozás](data-lake-storage-use-distcp.md#performance-considerations-while-using-distcp)                             |
| Azure Data Factory| parallelCopies    | [Hivatkozás](../../data-factory/copy-activity-performance.md)                          |
| Sqoop           | fs.azure.block.size, -m (mapper)    |   [Hivatkozás](https://blogs.msdn.microsoft.com/bigdatasupport/2015/02/17/sqoop-job-performance-tuning-in-hdinsight-hadoop/)        |

## <a name="structure-your-data-set"></a>Az adathalmaz szerkezete

Ha az adat tárolása Data Lake Storage Gen2 történik, a fájl mérete, a fájlok száma és a mappa szerkezete hatással van a teljesítményre.  A következő szakasz az ezekben a területeken ajánlott eljárásokat ismerteti.  

### <a name="file-size"></a>Fájlméret

Az elemzési motorok (például a HDInsight és a Azure Data Lake Analytics) általában Egyfájlos terheléssel rendelkeznek. Ha az adatait sok kis fájlként tárolja, ez negatívan befolyásolhatja a teljesítményt. Általánosságban megszervezheti az adatait nagyobb méretű fájlokba a jobb teljesítmény érdekében (a 256 MB-tól a 100 GB méretig). Előfordulhat, hogy egyes motorok és alkalmazások nem képesek hatékonyan feldolgozni a 100 GB nagyobb méretű fájlokat.

Időnként az adatfolyamatok korlátozott mértékben szabályozzák a sok kis fájlból álló nyers adatmennyiséget. Azt javasoljuk, hogy egy "főzési" folyamattal rendelkezzen, amely nagyobb fájlokat generál az alárendelt alkalmazásokhoz.

### <a name="organizing-time-series-data-in-folders"></a>Idősorozat-adattárolás mappákban történő rendszerezése

A struktúra számítási feladataihoz az idősoros adatok partíciójának metszése segíthet bizonyos lekérdezésekben, hogy csak az adatok egy részhalmaza legyen olvasható, amely javítja a teljesítményt.    

Azok a folyamatok, amelyek idősoros adatot töltenek be, gyakran a fájlokhoz és mappákhoz nagyon strukturált elnevezéssel helyezik el a fájljaikat. Az alábbiakban egy gyakori példa látható, hogy a dátum szerint strukturált adatok:

    \DataSet\YYYY\MM\DD\datafile_YYYY_MM_DD.tsv

Figyelje meg, hogy a DateTime információ a mappák és a fájlnév között is megjelenik.

A dátum és idő esetében az alábbi gyakori minta

    \DataSet\YYYY\MM\DD\HH\mm\datafile_YYYY_MM_DD_HH_mm.tsv

A mappa és a fájl szervezetének megválasztásakor a nagyobb fájlméretet és az egyes mappákban lévő fájlok ésszerű számú fájljának optimalizálását is érdemes megtenni.

## <a name="optimizing-io-intensive-jobs-on-hadoop-and-spark-workloads-on-hdinsight"></a>I/O-igényes feladatok optimalizálása a Hadoop-és Spark-munkaterheléseken a HDInsight-on

A feladatok a következő három kategóriába sorolhatók:

* **CPU-igényes.**  Ezeknek a feladatoknak hosszú számítási ideje van a minimális I/O-időpontokban.  Ilyenek például a gépi tanulás és a természetes nyelvi feldolgozási feladatok.  
* **Memória-igényes.**  Ezek a feladatok sok memóriát használnak.  Ilyenek például a PageRank és a valós idejű elemzési feladatok.  
* **Nagy I/O-igényű.**  Ezek a feladatok a legtöbb időt töltik az I/O-műveletek során.  Gyakori példa egy olyan másolási művelet, amely csak olvasási és írási műveleteket végez.  A többi példa olyan adat-előkészítési feladatokat is tartalmaz, amelyek nagy mennyiségű adatot olvasnak be, végeznek adatátalakítást, majd az adatok visszaírásával visszakerülnek az áruházba.  

Az alábbi útmutatás csak az I/O-igényes feladatokra vonatkozik.

## <a name="general-considerations"></a>Általános megfontolások

Egy olyan feladattal rendelkezhet, amely akár 100 MB-ot is képes beolvasni vagy írni egyetlen műveletben, de a méret puffere is veszélyeztetheti a teljesítményt.
A teljesítmény optimalizálása érdekében Próbáljon meg egy I/O-műveletet megőrizni a 4MB nál és a 16 MB között.

### <a name="general-considerations-for-an-hdinsight-cluster"></a>HDInsight-fürt általános szempontjai

* **HDInsight-verziók.** A legjobb teljesítmény érdekében használja a HDInsight legújabb kiadását.
* **Régiók.** Helyezze a Data Lake Storage Gen2 fiókot a HDInsight-fürttel megegyező régióban.  

An méretű HDInsight-fürt két fő csomópontból és néhány feldolgozói csomópontból áll. Minden munkavégző csomópont adott számú magot és memóriát biztosít, amelyet a virtuális gép típusa határoz meg.  A feladatok futtatásakor a fonal a tárolók létrehozásához rendelkezésre álló memória és magok kiosztására szolgáló erőforrás-tárgyaló.  Minden tároló futtatja a feladat végrehajtásához szükséges feladatokat.  A tárolók párhuzamosan futnak a feladatok gyors feldolgozásához. Ezért a teljesítmény javítása a lehető legtöbb párhuzamos tároló futtatásával történik.

Egy HDInsight-fürtben három réteg található, amelyek úgy állíthatók be, hogy növeljük a tárolók számát és az összes elérhető átviteli sebességet.  

* **Fizikai réteg**
* **FONAL réteg**
* **Munkaterhelés réteg**

### <a name="physical-layer"></a>Fizikai réteg

**Futtasson több csomóponttal és/vagy nagyobb méretű virtuális géppel rendelkező fürtöt.**  Egy nagyobb fürt lehetővé teszi, hogy több FONALas tárolót futtasson az alábbi képen látható módon.

![Data Lake Storage Gen2 teljesítmény](./media/data-lake-storage-performance-tuning-guidance/VM.png)

**Használjon nagyobb hálózati sávszélességű virtuális gépeket.**  A hálózati sávszélesség mennyisége szűk keresztmetszetet jelenthet, ha a Data Lake Storage Gen2 átviteli sebességnél kevesebb hálózati sávszélesség van.  A különböző virtuális gépek eltérő hálózati sávszélességet fognak tartalmazni.  Válasszon olyan virtuálisgép-típust, amely a legnagyobb lehetséges hálózati sávszélességgel rendelkezik.

### <a name="yarn-layer"></a>FONAL réteg

**Használjon kisebb FONALas tárolókat.**  Csökkentse az egyes FONALak tárolóinak méretét, hogy több tárolót hozzon létre azonos mennyiségű erőforrással.

![Data Lake Storage Gen2 teljesítmény](./media/data-lake-storage-performance-tuning-guidance/small-containers.png)

A számítási feladattól függően mindig szükség van egy minimálisan szükséges szál-tároló méretre. Ha túl kis tárolót választ, a feladatok memórián kívüli problémákba fognak futni. Általában a FONALas tárolók nem lehetnek 1 GB-nál kisebbek. Gyakori, hogy a 3GB FONALas tárolók láthatók. Bizonyos munkaterhelések esetén nagyobb méretű FONALas tárolók szükségesek.  

**Növelje a magok számát a szálak tárolóján.**  Növelje az egyes tárolók számára lefoglalt magok számát, hogy növelje az egyes tárolókban futó párhuzamos feladatok számát.  Ez olyan alkalmazások esetében működik, mint például a Spark, amelyek tárolón több feladatot futtatnak.  Az olyan alkalmazások esetében, mint például a kaptár, amely az egyes tárolókban egyetlen szálat futtat, jobb, ha több tárolóval rendelkezik, mint a tárolók száma.

### <a name="workload-layer"></a>Munkaterhelés réteg

**Az összes rendelkezésre álló tároló használata.**  Állítsa be, hogy a tevékenységek száma egyenlő legyen, vagy nagyobb legyen, mint a rendelkezésre álló tárolók száma, hogy minden erőforrás kihasználható legyen.

![Data Lake Storage Gen2 teljesítmény](./media/data-lake-storage-performance-tuning-guidance/use-containers.png)

**A sikertelen feladatok költségesek.** Ha az egyes feladatok nagy mennyiségű adattal dolgoznak fel, akkor a feladat meghibásodása költséges újrapróbálkozást eredményez.  Ezért jobb, ha több feladatot hoz létre, amelyek mindegyike kis mennyiségű adat feldolgozását dolgozza fel.

A fenti általános irányelvek mellett minden alkalmazás különböző paraméterekkel rendelkezik az adott alkalmazás finomhangolásához. Az alábbi táblázat néhány paramétert és hivatkozást tartalmaz, amelyekkel megkezdheti az egyes alkalmazások teljesítményének finomhangolását.

| Számítási feladat | A feladatok beállítására szolgáló paraméter |
|----------|------------------------|
| [Spark on HDInsight](data-lake-storage-performance-tuning-spark.md) | <ul><li>Num-executors</li><li>Végrehajtó – memória</li><li>Végrehajtó – magok</li></ul> |
| [Struktúra a HDInsight](data-lake-storage-performance-tuning-hive.md) | <ul><li>hive.tez.container.size</li></ul> |
| [MapReduce a HDInsight](data-lake-storage-performance-tuning-mapreduce.md) | <ul><li>MapReduce. map. Memory</li><li>MapReduce. job. Maps</li><li>MapReduce. csökkentse a memóriát</li><li>MapReduce. job. csökkenti</li></ul> |
| [Storm on HDInsight](data-lake-storage-performance-tuning-storm.md)| <ul><li>Munkavégző folyamatok száma</li><li>Kiöntő végrehajtó példányainak száma</li><li>A bolt végrehajtó példányainak száma </li><li>Kiöntő feladatok száma</li><li>Bolti feladatok száma</li></ul>|

## <a name="see-also"></a>Lásd még
* [A Azure Data Lake Storage Gen2 áttekintése](data-lake-storage-introduction.md)
