---
title: Teljesítménynövelés az Apache Spark-alapú számítási feladatokat az Azure HDInsight i/o-Cache (előzetes verzió)
description: További tudnivalók az Azure HDInsight i/o-gyorsítótár és hogyan használható az Apache Spark a teljesítmény javítása.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.topic: conceptual
ms.date: 10/15/2018
ms.openlocfilehash: b77e7e9d5a68439e7f336ecb26e91031d80a7606
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64695208"
---
# <a name="improve-performance-of-apache-spark-workloads-using-azure-hdinsight-io-cache-preview"></a>Teljesítménynövelés az Apache Spark-alapú számítási feladatokat az Azure HDInsight i/o-Cache (előzetes verzió)

I/o Cache egy gyorsítótárazási szolgáltatás az Azure HDInsight, amely növeli a teljesítményt az Apache Spark-feladatok használata. I/o-gyorsítótár is képes együttműködni [Apache TEZ](https://tez.apache.org/) és [Apache Hive](https://hive.apache.org/) számítási feladatok, amelyek futtatni a [Apache Spark](https://spark.apache.org/) fürtök. I/o-gyorsítótár-RubiX nevű nyílt forráskódú gyorsítótárazási összetevő használja. RubiX egy helyi gyorsítótárat a big data analytics motorok, amely felhőalapú tárolórendszerek származó adatok eléréséhez való használatra. RubiX egyedi osztályneveket használjon a rendszerek, gyorsítótárazás, mert használja Solid-State meghajtók (SSD-kkel) ahelyett, hogy a Foglalás működési memória célokra gyorsítótárazáshoz. Az i/o-gyorsítótár-szolgáltatás elindul, és RubiX metaadat-kiszolgálók kezelése a fürt összes munkavégző csomóponton. Továbbá konfigurálja a fürt összes szolgáltatás transzparens RubiX gyorsítótár használata.

A legtöbb SSD-k sávszélesség másodpercenként 1-nél több GByte adja meg. A sávszélesség, az operációs rendszer memórián belüli gyorsítótár, szoftverszolgáltatáson nyújt elegendő sávszélesség, a big data számítási feldolgozó motorokkal, például az Apache Spark betölteni. A működési memória maradt elérhető az Apache Spark az erősen memória-függő feladatok, például shuffles feldolgozásához. Kizárólagos használatára, a memória működési lehetővé, hogy az Apache Spark optimális erőforrás-használat elérése érdekében.  

>[!Note]  
>I/o-gyorsítótár jelenleg használ RubiX gyorsítótárazási összetevőként, de ez megváltozhatnak a jövőben a szolgáltatás verzióit. Gyorsítótár i/o-adapterek használata, és a függőségek nem kell közvetlenül RubiX végrehajtásáról.

## <a name="benefits-of-azure-hdinsight-io-cache"></a>I/o-gyorsítótár az Azure HDInsight előnyei

A teljesítmény növelése i/o-gyorsítótár használatával biztosít a feladatokat, olvassa el az adatok Azure Blob Storage-ból.

A Spark-feladatok megtekintéséhez teljesítménynövekedést i/o-gyorsítótár használatakor semmilyen módosítást nem kell. I/o-gyorsítótár le van tiltva, ha a Spark-kódot kellene adatolvasási távolról az Azure Blob Storage-ból: `spark.read.load('wasbs:///myfolder/data.parquet').count()`. I/o-gyorsítótár aktiválódik, amikor az ugyanazon kódsort hatására a gyorsítótárazott olvasási i/o-gyorsítótár keresztül. A következő olvasási az adatok van olvasni a helyileg az SSD. HDInsight-fürtön a munkavégző csomópontok egérporttal rendelkeznek, helyileg csatlakoztatott, dedikált SSD meghajtókra. HDInsight i/o-gyorsítótár ezek helyi SSD-k felhasználja a gyorsítótárazáshoz, amely a legalacsonyabb szintű késést biztosít, és a lehető legnagyobbra növeli a sávszélesség.

## <a name="getting-started"></a>Első lépések

Az Azure HDInsight i/o-gyorsítótár az előzetes verzióban érhető el az alapértelmezett inaktiválása. Az Azure HDInsight 3.6-os + Spark-fürtökön, Apache Spark 2.3 futtatásához, amelyek i/o-gyorsítótár érhető el.  Az aktiválás i/o-gyorsítótár, tegye a következőket:

1. Válassza ki a HDInsight-fürtön az [az Azure Portalon](https://portal.azure.com).

1. Az a **áttekintése** (megnyitott alapértelmezés szerint a fürt kiválasztásakor) oldalon válassza ki, **Ambari kezdőlap** alatt **fürt irányítópultjai**.

1. Válassza ki a **i/o-gyorsítótár** szolgáltatást, a bal oldalon.

1. Válassza ki **műveletek** és **aktiválása**.

    ![Az Ambari az i/o-gyorsítótár-szolgáltatás engedélyezése](./media/apache-spark-improve-performance-iocache/ambariui-enable-iocache.png "az Ambari az i/o-gyorsítótár-szolgáltatás engedélyezése")

1. Erősítse meg a fürt minden érintett szolgáltatást újra kell indítani.

>[!NOTE]  
> Annak ellenére, hogy a folyamatjelző mutatja aktivált, i/o-gyorsítótár valójában nincs engedélyezve az érintett szolgáltatások újraindításáig.

## <a name="troubleshooting"></a>Hibaelhárítás
  
Futó Spark feladatok i/o-gyorsítótár engedélyezése után terület lemezhibák kaphat. Ezek a hibák akkor fordul elő, mert a Spark is használja a helyi lemezes tárhelyért adattároláshoz újbóli felosztás műveletek során. Spark elfogy, előfordulhat, hogy a SSD-lemezterületet az i/o-gyorsítótár engedélyezve van, és a Spark-tároló terület csökken. A teljes SSD-lemezterületet fele az i/o-gyorsítótár alapértelmezett által felhasznált lemezterület mennyiségét. A lemezterület-használat i/o-gyorsítótár nem konfigurálható az Ambari. Ha lemezhibák adhatja meg, csökkentse a felhasznált i/o-gyorsítótárhoz SSD terület mennyiségét, és indítsa újra a szolgáltatást. Az i/o-gyorsítótár beállított lemezterület módosításához tegye a következőket:

1. Az Apache Ambari, válassza a **HDFS** szolgáltatást, a bal oldalon.

1. Válassza ki a **Configs** és **speciális** lapokon.

    ![Szerkesztés speciális konfigurációs HDFS](./media/apache-spark-improve-performance-iocache/ambariui-hdfs-service-configs-advanced.png "HDFS speciális konfiguráció szerkesztése")

1. Görgessen lefelé, és bontsa ki a **egyéni hely** területen.

1. Keresse meg a tulajdonság **hadoop.cache.data.fullness.percentage**.

1. Módosítsa az értéket, a mezőben.

    ![I/o-gyorsítótár Fullness százalékos szerkesztése](./media/apache-spark-improve-performance-iocache/ambariui-cache-data-fullness-percentage-property.png "i/o-gyorsítótár Fullness százalékos szerkesztése")

1. Válassza ki **mentése** a jobb felső részén.

1. Válassza ki **indítsa újra a** > **indítsa újra az összes érintett**.

    ![Indítsa újra az összes érintett](./media/apache-spark-improve-performance-iocache/ambariui-restart-all-affected.png "indítsa újra az összes érintett")

1. Válassza ki **Újraindítás megerősítése összes**.

Ha nem működik, i/o-gyorsítótár letiltása.

## <a name="next-steps"></a>További lépések

- További információ az i/o-gyorsítótár, többek között teljesítmény ebben a blogbejegyzésben találhat: [Az Apache Spark-feladatok szerezhet legfeljebb 9 x felgyorsítható a HDInsight i/o-gyorsítótár](https://azure.microsoft.com/blog/apache-spark-speedup-with-hdinsight-io-cache/)
