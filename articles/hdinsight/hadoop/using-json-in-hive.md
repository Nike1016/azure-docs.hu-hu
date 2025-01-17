---
title: Elemezheti és az Azure HDInsight az Apache Hive JSON-dokumentumok feldolgozása
description: Ismerje meg, hogyan használja a JSON-dokumentumok és elemezheti az Azure HDInsight az Apache Hive segítségével.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 5ec766cea2135f7c00df032ad0df4ada033d6293
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67461991"
---
# <a name="process-and-analyze-json-documents-by-using-apache-hive-in-azure-hdinsight"></a>JSON-dokumentumok elemzését az Azure HDInsight az Apache Hive használatával

Megtudhatja, hogyan dolgozza fel, és a JavaScript Object Notation (JSON) fájlok elemezhet az Apache Hive, az Azure HDInsight. Ez a cikk a következő JSON-dokumentum használja:

```json
{
  "StudentId": "trgfg-5454-fdfdg-4346",
  "Grade": 7,
  "StudentDetails": [
    {
      "FirstName": "Peggy",
      "LastName": "Williams",
      "YearJoined": 2012
    }
  ],
  "StudentClassCollection": [
    {
      "ClassId": "89084343",
      "ClassParticipation": "Satisfied",
      "ClassParticipationRank": "High",
      "Score": 93,
      "PerformedActivity": false
    },
    {
      "ClassId": "78547522",
      "ClassParticipation": "NotSatisfied",
      "ClassParticipationRank": "None",
      "Score": 74,
      "PerformedActivity": false
    },
    {
      "ClassId": "78675563",
      "ClassParticipation": "Satisfied",
      "ClassParticipationRank": "Low",
      "Score": 83,
      "PerformedActivity": true
    }
  ]
}
```

A fájl található `wasb://processjson@hditutorialdata.blob.core.windows.net/`. Az Azure Blob storage használata a HDInsight további információkért lásd: [használható HDFS-kompatibilis Azure Blob storage a HDInsight Apache Hadoop-keretrendszerrel](../hdinsight-hadoop-use-blob-storage.md). A fürt alapértelmezett tárolóba másolhatja a fájlt.

Ez a cikk az Apache Hive konzolt használja. Nyissa meg a Hive konzolt kapcsolatos utasításokért lásd: [használata az Apache Ambari az Apache hadooppal a HDInsight Hive-nézet](apache-hadoop-use-hive-ambari-view.md).

## <a name="flatten-json-documents"></a>JSON-dokumentumok egybesimítására
A következő szakaszban felsorolt módszerek szükséges, hogy a JSON-dokumentum tevődik össze egyetlen sor. Tehát simítja egybe a JSON-dokumentum karakterlánccá. Ha a JSON-dokumentumok már lett simítva, ezt a lépést kihagyhatja, és egyenesen eljuthat a következő szakasz a JSON-adatok elemzése. A JSON-dokumentumok egybesimítására, futtassa a következő parancsfájlt:

```sql
DROP TABLE IF EXISTS StudentsRaw;
CREATE EXTERNAL TABLE StudentsRaw (textcol string) STORED AS TEXTFILE LOCATION "wasb://processjson@hditutorialdata.blob.core.windows.net/";

DROP TABLE IF EXISTS StudentsOneLine;
CREATE EXTERNAL TABLE StudentsOneLine
(
  json_body string
)
STORED AS TEXTFILE LOCATION '/json/students';

INSERT OVERWRITE TABLE StudentsOneLine
SELECT CONCAT_WS(' ',COLLECT_LIST(textcol)) AS singlelineJSON
      FROM (SELECT INPUT__FILE__NAME,BLOCK__OFFSET__INSIDE__FILE, textcol FROM StudentsRaw DISTRIBUTE BY INPUT__FILE__NAME SORT BY BLOCK__OFFSET__INSIDE__FILE) x
      GROUP BY INPUT__FILE__NAME;

SELECT * FROM StudentsOneLine
```

A nyers JSON-fájl `wasb://processjson@hditutorialdata.blob.core.windows.net/`. A **StudentsRaw** a nyers JSON-dokumentum, amely nem lett simítva, Hive-tábla pontokat.

A **StudentsOneLine** Hive-tábla tárolja az adatokat a HDInsight alapértelmezett fájlrendszer alatt a **/json/tanulók/** elérési útja.

A **BESZÚRÁSA** utasítás tölti fel a **StudentOneLine** egybesimított JSON-adatokat tartalmazó táblát.

A **kiválasztása** utasítás csak egy sort adja vissza.

A kimenet az itt látható a **kiválasztása** utasítást:

![Az egybesimítás a JSON-dokumentum](./media/using-json-in-hive/flatten.png)

## <a name="analyze-json-documents-in-hive"></a>JSON-dokumentumok Hive elemzése
Hive a JSON-dokumentumok lekérdezések futtatására használható három különböző mechanizmusokat biztosít, vagy írhat saját:

* Használja a get_json_object felhasználói függvény (UDF).
* Az UDF json_tuple használja.
* Az egyéni szerializáló/deszerializáló (SerDe) használja.
* Írhat saját UDF Python vagy más nyelv használatával. Saját Python-kód futtatása Hive-val kapcsolatos további információkért lásd: [az Apache Hive- és Apache Pig Python felhasználói függvények] [hdinsight-python].

### <a name="use-the-getjsonobject-udf"></a>Használja a get_json_object UDF-ben
Hive biztosít egy beépített UDF nevű [get_json_object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) , amely JSON lekérdezése során futásidejű hajthat végre. Ez a módszer két argumentumot – a táblázat neve és a metódus nevét, amely rendelkezik a egybesimított JSON-dokumentum és a JSON-mezőt, amely elemezni kell. Nézzük meg, például hogy az UDF működését.

A következő lekérdezést ad vissza, az utónév és Vezetéknév minden tanuló számára:

```sql
SELECT
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.FirstName'),
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.LastName')
FROM StudentsOneLine;
```

Ez a lekérdezés futtatásakor a konzolablakban a kimenet itt látható:

![get_json_object UDF](./media/using-json-in-hive/getjsonobject.png)

Korlátozottak a get_json_object UDF-ben:

* A lekérdezés minden egyes mezőjéhez használatához a lekérdezés reparsing, a teljesítményt befolyásolja.
* **ELSŐ\_JSON_OBJECT()** a tömb karakterláncként adja vissza. A tömb átalakítása a Hive-tömb, cserélje le a szögletes zárójelek reguláris kifejezések használatával kell "[" és "]", és ezután is kell beolvasni a tömb split hívja.

Ezért a Hive wiki json_tuple használatát javasolja.  

### <a name="use-the-jsontuple-udf"></a>Használja a json_tuple UDF-ben
Egy másik UDF Hive által biztosított nevezzük [json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple), melyik teljesít jobban, mint [get_ json _object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object). Ez a metódus szükséges kulcsokat és a egy JSON-karakterlánc, és adja vissza egy rekord érték egy függvény használatával. A következő lekérdezést a tanulói Azonosítóját és a besorolási a JSON-dokumentum adja vissza:

```sql
SELECT q1.StudentId, q1.Grade
FROM StudentsOneLine jt
LATERAL VIEW JSON_TUPLE(jt.json_body, 'StudentId', 'Grade') q1
  AS StudentId, Grade;
```

Ez a szkript a Hive-konzol kimenetét:

![json_tuple UDF](./media/using-json-in-hive/jsontuple.png)

Az UDF-ben használt json_tuple a [nézet oldalirányú](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) struktúra, amely lehetővé teszi a json szintaxist\_virtuális tábla létrehozása a UDT függvény az eredeti tábla minden egyes sorára való alkalmazásával a rekordot. A ismételt használata miatt túl nehézkessé válhat összetett JSONs **OLDALNÉZET**. Ezenkívül **JSON_TUPLE** beágyazott JSONs nem tudja kezelni.

### <a name="use-a-custom-serde"></a>Egy egyéni SerDe használata
SerDe a legjobb választás beágyazott JSON-dokumentumok elemzését. Lehetővé teszi, hogy a JSON-séma határozza meg, és ezután használhatja a sémát a dokumentumok elemzése. Útmutatásért lásd: [egy egyéni JSON-SerDe használata a Microsoft Azure HDInsight](https://web.archive.org/web/20190217104719/https://blogs.msdn.microsoft.com/bigdatasupport/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight/).

## <a name="summary"></a>Összefoglalás
Végezetül JSON operátor az Ön által választott Hive típusa a forgatókönyvtől függ. Ha egy egyszerű JSON-dokumentum, és keresse ki csak egy mezőt, válassza ki a Hive-UDF get_json_object használja. Ha egynél több kulcs keressük meg, majd használhatja json_tuple. Ha rendelkezik egy beágyazott dokumentumot, akkor a JSON-SerDe kell használnia.

## <a name="next-steps"></a>További lépések

Kapcsolódó cikkek lásd:

* [Az Apache Hive és a HiveQL használata a HDInsight az Apache Hadoop Apache log4j mintafájl elemzéséhez](../hdinsight-use-hive.md)
* [Repülőjáratok késési adatainak elemzése a HDInsight interaktív lekérdezés használatával](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)
* [Twitter-adatok elemzése a HDInsight az Apache Hive használatával](../hdinsight-analyze-twitter-data-linux.md)
