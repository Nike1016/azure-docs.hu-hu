---
title: Az Azure Cosmos DB Gremlin-támogatás
description: Tudnivalók az Apache TinkerPop Gremlin nyelvéről. Megismerheti, milyen funkciók és lépések érhetők el az Azure Cosmos DB-ben.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 06/24/2019
ms.author: lbosq
ms.openlocfilehash: db263c1c7f0a8b87b315c5aa6da31336229c9643
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67502728"
---
# <a name="azure-cosmos-db-gremlin-graph-support"></a>A Gremlin-gráfok Azure Cosmos DB általi támogatása
Az Azure Cosmos DB támogatja a [Apache Tinkerpop](https://tinkerpop.apache.org) gráf bejárása nyelv, más néven [Gremlin](https://tinkerpop.apache.org/docs/3.3.2/reference/#graph-traversal-steps). A Gremlin nyelv segítségével létrehozhat gráfentitásokat (csúcspontokat és éleket), módosíthatja ezen entitások tulajdonságait, végrehajthat lekérdezéseket és bejárásokat, és törölhet entitásokat. 

Ebben a cikkben azt adja meg a gyors bemutató Gremlin és a Gremlin-funkciók, a Gremlin API által támogatott számbavétele.

## <a name="compatible-client-libraries"></a>Kompatibilis ügyfélkódtárak

Az alábbi táblázat az Azure Cosmos DB-n használható népszerű Gremlin-illesztőprogramokat foglalja össze:

| Letöltés | source | Első lépések | Támogatott összekötő-verzió |
| --- | --- | --- | --- |
| [.NET](https://tinkerpop.apache.org/docs/3.3.1/reference/#gremlin-DotNet) | [Gremlin.NET on GitHub](https://github.com/apache/tinkerpop/tree/master/gremlin-dotnet) | [Gráf létrehozása a .NET használatával](create-graph-dotnet.md) | 3.4.0-RC2 |
| [Java](https://mvnrepository.com/artifact/com.tinkerpop.gremlin/gremlin-java) | [Gremlin JavaDoc](https://tinkerpop.apache.org/javadocs/current/full/) | [Gráf létrehozása a Java használatával](create-graph-java.md) | 3.2.0+ |
| [Node.js](https://www.npmjs.com/package/gremlin) | [Gremlin-JavaScript a GitHubon](https://github.com/jbmusso/gremlin-javascript) | [Gráf létrehozása a Node.js használatával](create-graph-nodejs.md) | 3.3.4+ |
| [Python](https://tinkerpop.apache.org/docs/3.3.1/reference/#gremlin-python) | [Gremlin-Python a GitHubon](https://github.com/apache/tinkerpop/tree/master/gremlin-python) | [Gráf létrehozása a Python használatával](create-graph-python.md) | 3.2.7 |
| [PHP](https://packagist.org/packages/brightzone/gremlin-php) | [Gremlin-PHP a GitHubon](https://github.com/PommeVerte/gremlin-php) | [Gráf létrehozása a PHP használatával](create-graph-php.md) | 3.1.0 |
| [Gremlin-konzol](https://tinkerpop.apache.org/downloads.html) | [TinkerPop dokumentumok](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console) |  [Gráf létrehozása a Gremlin-konzol használatával](create-graph-gremlin-console.md) | 3.2.0 + |

## <a name="supported-graph-objects"></a>Graph-objektumok támogatott
A TinkerPop egy olyan szabvány, amely számos különböző gráftechnológiára kiterjed. Ebből adódóan szabványos kifejezésekkel írja le, hogy az egyes gráfszolgáltatók milyen funkciókat nyújtanak. Az Azure Cosmos DB egy állandó, magas egyidejűségű, írható gráfadatbázis, amely egyszerre több kiszolgálóra vagy fürtre is particionálható. 

Az alábbi táblázat a TinkerPop azon funkcióit sorolja fel, amelyeket az Azure Cosmos DB megvalósít: 

| Kategória | Azure Cosmos DB-megvalósítás |  Megjegyzések | 
| --- | --- | --- |
| Gráffunkciók | Állandóságot és egyidejű hozzáférést biztosít. A tranzakciók támogatására lett tervezve. | A számítógépes módszerek a Spark-összekötőn keresztül valósíthatók meg. |
| Változófunkciók | A logikai, egész számú, bájt-, dupla, lebegőpontos, hosszú és sztringértékeket támogatja. | Támogatja az egyszerű típusokat, és kompatibilis az összetett típusokkal adatmodellek révén. |
| Csúcspontfunkciók | A következőket támogatja: RemoveVertices, MetaProperties, AddVertices, MultiProperties, StringIds, UserSuppliedIds, AddProperty, RemoveProperty.  | Lehetővé teszi csúcspontok létrehozását, módosítását és törlését. |
| Csúcsponttulajdonság-funkciók | StringIds, UserSuppliedIds, AddProperty, RemoveProperty, BooleanValues, ByteValues, DoubleValues, FloatValues, IntegerValues, LongValues, StringValues | Lehetővé teszi csúcsponttulajdonságok létrehozását, módosítását és törlését. |
| Élfunkciók | AddEdges, RemoveEdges, StringIds, UserSuppliedIds, AddProperty, RemoveProperty | Lehetővé teszi élek létrehozását, módosítását és törlését. |
| Éltulajdonság-funkciók | Tulajdonságok, BooleanValues, ByteValues, DoubleValues, FloatValues, IntegerValues, LongValues, StringValues | Lehetővé teszi éltulajdonságok létrehozását, módosítását és törlését. |

## <a name="gremlin-wire-format-graphson"></a>Gremlin formátumot: GraphSON

Az Azure Cosmos DB a [GraphSON formátum](https://tinkerpop.apache.org/docs/3.3.2/reference/#graphson-reader-writer) használatával adja vissza a Gremlin-műveletek eredményeit. A GraphSON a Gremlin szabványos formátuma a csúcspontok, élek és tulajdonságok (egy- és többértékű tulajdonságok) jelöléséhez a JSON használatával. 

Az alábbi kódrészletben például az Azure Cosmos DB-ből *az ügyfél számára visszaadott* csúcspont GraphSON-jelölése látható. 

```json
  {
    "id": "a7111ba7-0ea1-43c9-b6b2-efc5e3aea4c0",
    "label": "person",
    "type": "vertex",
    "outE": {
      "knows": [
        {
          "id": "3ee53a60-c561-4c5e-9a9f-9c7924bc9aef",
          "inV": "04779300-1c8e-489d-9493-50fd1325a658"
        },
        {
          "id": "21984248-ee9e-43a8-a7f6-30642bc14609",
          "inV": "a8e3e741-2ef7-4c01-b7c8-199f8e43e3bc"
        }
      ]
    },
    "properties": {
      "firstName": [
        {
          "value": "Thomas"
        }
      ],
      "lastName": [
        {
          "value": "Andersen"
        }
      ],
      "age": [
        {
          "value": 45
        }
      ]
    }
  }
```

Csúcspontok a GraphSON által használt tulajdonságok az alábbiakban tekintheti át:

| Tulajdonság | Leírás | 
| --- | --- | --- |
| `id` | A csúcspont azonosítója. Egyedinek kell lennie (együttesen a következő értékkel: `_partition` ha van ilyen). Ha a nem érték van megadva, azt lesz automatikusan adni egy GUID | 
| `label` | A csúcspont címkéje. Ez a tulajdonság az entitástípus leírására szolgál. |
| `type` | A használatával megkülönböztethetők a csúcspontok a nem gráfdokumentumoktól. |
| `properties` | A csúcsponthoz tartozó, felhasználó által megadott tulajdonságok összessége. Minden tulajdonságnak több értéke is lehet. |
| `_partition` | A csúcspont partíciókulcsa. Használt [graph-particionálás](graph-partitioning.md). |
| `outE` | Ez a tulajdonság a csúcspont élek ki listáját tartalmazza. A csúcspontok szomszédsági adatainak tárolása lehetővé teszi a bejárások gyors végrehajtását. Az élek a címkéik alapján vannak csoportosítva. |

Az él pedig a következő információkat tartalmazza, ezzel segítve a gráf többi részéhez való navigációt.

| Tulajdonság | Leírás |
| --- | --- |
| `id` | Az él azonosítója. Egyedinek kell lennie (együttesen a következő értékkel: `_partition` ha van ilyen) |
| `label` | Az él címkéje. Ezt a tulajdonságot nem kötelező megadni, és a kapcsolat típusának leírására használható. |
| `inV` | Ez a tulajdonság a csúcsot versenyképességét listáját tartalmazza. Az élek szomszédsági adatainak tárolása lehetővé teszi a bejárások gyors végrehajtását. A csúcspontok a címkéik alapján vannak csoportosítva. |
| `properties` | Az élhez tartozó, felhasználó által megadott tulajdonságok összessége. Minden tulajdonságnak több értéke is lehet. |

Az egyes tulajdonságok több értéket is tárolhatnak egy tömbben. 

| Tulajdonság | Leírás |
| --- | --- |
| `value` | A tulajdonság értéke.

## <a name="gremlin-steps"></a>Gremlin-lépések
Most pedig tekintsük át az Azure Cosmos DB által támogatott Gremlin-lépéseket. A Gremlin teljes körű ismertetését a [TinkerPop referenciaanyaga](https://tinkerpop.apache.org/docs/3.3.2/reference) tartalmazza.

| lépés | Leírás | TinkerPop 3.2-dokumentáció |
| --- | --- | --- |
| `addE` | Hozzáad egy élt két csúcspont között. | [addE lépés](https://tinkerpop.apache.org/docs/3.3.2/reference/#addedge-step) |
| `addV` | Hozzáad egy csúcspontot a gráfhoz. | [addV lépés](https://tinkerpop.apache.org/docs/3.3.2/reference/#addvertex-step) |
| `and` | Biztosítja, hogy minden bejárás visszaadjon egy értéket. | [and lépés](https://tinkerpop.apache.org/docs/3.3.2/reference/#and-step) |
| `as` | Egy lépésmodulátor, amely egy változót rendel a lépés kimenetéhez. | [as lépés](https://tinkerpop.apache.org/docs/3.3.2/reference/#as-step) |
| `by` | A `group` és az `order` lépéssel használt lépésmodulátor. | [by lépés](https://tinkerpop.apache.org/docs/3.3.2/reference/#by-step) |
| `coalesce` | Visszaadja az első olyan bejárást, amely értéket ad vissza. | [coalesce lépés](https://tinkerpop.apache.org/docs/3.3.2/reference/#coalesce-step) |
| `constant` | Egy állandó értéket ad vissza. A `coalesce` lépéssel használható.| [constant lépés](https://tinkerpop.apache.org/docs/3.3.2/reference/#constant-step) |
| `count` | Visszaadja a darabszámot a bejárásból. | [count lépés](https://tinkerpop.apache.org/docs/3.3.2/reference/#count-step) |
| `dedup` | Visszaadja az értékeket, eltávolítva az ismétlődéseket. | [dedup lépés](https://tinkerpop.apache.org/docs/3.3.2/reference/#dedup-step) |
| `drop` | Elveti az értékeket (csúcspont/él). | [drop lépés](https://tinkerpop.apache.org/docs/3.3.2/reference/#drop-step) |
| `executionProfile` | Hozza létre a végrehajtott Gremlin-lépés által létrehozott összes művelet leírása | [executionProfile. lépés](graph-execution-profile.md) |
| `fold` | Korlátként funkcionál, amely kiszámítja az eredmények összesítését.| [fold lépés](https://tinkerpop.apache.org/docs/3.3.2/reference/#fold-step) |
| `group` | Csoportosítja az értékeket a megadott címkék alapján.| [group lépés](https://tinkerpop.apache.org/docs/3.3.2/reference/#group-step) |
| `has` | Tulajdonságok, csúcspontok és élek szűrésére szolgál. A következő változatokat támogatja: `hasLabel`, `hasId`, `hasNot` és `has`. | [has lépés](https://tinkerpop.apache.org/docs/3.3.2/reference/#has-step) |
| `inject` | Értékeket szúr be egy streambe.| [inject lépés](https://tinkerpop.apache.org/docs/3.3.2/reference/#inject-step) |
| `is` | Szűrés végrehajtására használható egy logikai kifejezés használatával. | [is lépés](https://tinkerpop.apache.org/docs/3.3.2/reference/#is-step) |
| `limit` | A bejárásban található elemek számának korlátozására szolgál.| [limit lépés](https://tinkerpop.apache.org/docs/3.3.2/reference/#limit-step) |
| `local` | Helyileg becsomagolja egy bejárás egy szakaszát, egy segédlekérdezéshez hasonlóan. | [local lépés](https://tinkerpop.apache.org/docs/3.3.2/reference/#local-step) |
| `not` | Egy szűrő eltávolítására szolgál. | [not lépés](https://tinkerpop.apache.org/docs/3.3.2/reference/#not-step) |
| `optional` | A megadott bejárás eredményét adja vissza, ha az ad eredményt, ha nem, akkor a hívó elemet adja vissza. | [optional lépés](https://tinkerpop.apache.org/docs/3.3.2/reference/#optional-step) |
| `or` | Biztosítja, hogy legalább az egyik bejárás visszaadjon egy értéket. | [or lépés](https://tinkerpop.apache.org/docs/3.3.2/reference/#or-step) |
| `order` | A megadott rendezési sorrendben adja vissza az eredményeket. | [order lépés](https://tinkerpop.apache.org/docs/3.3.2/reference/#order-step) |
| `path` | Visszaadja a bejárás teljes útvonalát. | [path lépés](https://tinkerpop.apache.org/docs/3.3.2/reference/#path-step) |
| `project` | Leképezésként jeleníti meg a tulajdonságokat. | [project lépés](https://tinkerpop.apache.org/docs/3.3.2/reference/#project-step) |
| `properties` | Visszaadja a megadott címkék tulajdonságait. | [properties lépés](https://tinkerpop.apache.org/docs/3.3.2/reference/#_properties_step) |
| `range` | A megadott értéktartományra szűr.| [range lépés](https://tinkerpop.apache.org/docs/3.3.2/reference/#range-step) |
| `repeat` | Megismétli a lépést a megadott számú alkalommal. Ismétlődések beállítására szolgál. | [repeat lépés](https://tinkerpop.apache.org/docs/3.3.2/reference/#repeat-step) |
| `sample` | Mintát vesz a bejárás eredményeiből. | [sample lépés](https://tinkerpop.apache.org/docs/3.3.2/reference/#sample-step) |
| `select` | Megjeleníti a bejárás eredményeit. |  [select lépés](https://tinkerpop.apache.org/docs/3.3.2/reference/#select-step) |
| `store` | Nem blokkoló összesítéseket hajt végre a bejárásból. | [store lépés](https://tinkerpop.apache.org/docs/3.3.2/reference/#store-step) |
| `TextP.startingWith(string)` | Függvény szűrési karakterlánc. Ez a függvény egy predikátumát szolgál a `has()` lépésben megadott karakterlánc elején tulajdonságot megfelelően | [TextP predikátumok](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.endingWith(string)` |  Függvény szűrési karakterlánc. Ez a függvény egy predikátumát szolgál a `has()` megfelelően a befejezési egy adott karakterláncot tulajdonságot. lépés | [TextP predikátumok](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.containing(string)` | Függvény szűrési karakterlánc. Ez a függvény egy predikátumát szolgál a `has()` lépés megfelelően tulajdonsággal rendelkező tartalmát egy adott karakterlánccal. | [TextP predikátumok](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.notStartingWith(string)` | Függvény szűrési karakterlánc. Ez a függvény egy predikátumát szolgál a `has()` olyan tulajdonságot, amely egy adott karakterlánccal nem indul el megfelelően. lépés | [TextP predikátumok](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.notEndingWith(string)` | Függvény szűrési karakterlánc. Ez a függvény egy predikátumát szolgál a `has()` megfelelő olyan tulajdonságot, amely egy megadott karakterlánc nem végződhet. lépés | [TextP predikátumok](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.notContaining(string)` | Függvény szűrési karakterlánc. Ez a függvény egy predikátumát szolgál a `has()` olyan tulajdonságot, amely egy megadott karakterlánc nem tartalmaz megfelelő lépés | [TextP predikátumok](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `tree` | Egy fában összesíti a csúcspontból induló útvonalakat. | [tree lépés](https://tinkerpop.apache.org/docs/3.3.2/reference/#tree-step) |
| `unfold` | Visszaalakít egy iterátort egy lépésként.| [unfold lépés](https://tinkerpop.apache.org/docs/3.3.2/reference/#unfold-step) |
| `union` | Egyesíti több bejárás eredményeit.| [union lépés](https://tinkerpop.apache.org/docs/3.3.2/reference/#union-step) |
| `V` | A csúcspontok és élek közötti bejárásokhoz szükséges lépéseket foglalja magában: `V`, `E`, `out`, `in`, `both`, `outE`, `inE`, `bothE`, `outV`, `inV`, `bothV` és `otherV`. | [vertex lépések](https://tinkerpop.apache.org/docs/3.3.2/reference/#vertex-steps) |
| `where` | A bejárás eredményeinek szűrésére szolgál. A következő operátorokat támogatja: `eq`, `neq`, `lt`, `lte`, `gt`, `gte`, `between`.  | [where lépés](https://tinkerpop.apache.org/docs/3.3.2/reference/#where-step) |

Az Azure Cosmos DB által biztosított, írásra optimalizált motor alapértelmezés szerint támogatja a csúcspontokon és éleken belüli összes tulajdonság automatikus indexelését. Ezért a szűrővel rendelkező lekérdezéseket, a tartománylekérdezéseket, a rendezéseket és a tulajdonságösszesítések mindegyikét a rendszer közvetlenül az indexből dolgozza fel a hatékony kiszolgálás érdekében. Az indexelésnek az Azure Cosmos DB-ben való működésével kapcsolatban a [sémafüggetlen indexelésről](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) szóló tanulmányunkban tekinthet meg további információt.

## <a name="next-steps"></a>További lépések
* Bevezetés egy gráfalkalmazás létrehozásába [az SDK-k használatával](create-graph-dotnet.md) 
* További információk a [gráfok támogatásáról](graph-introduction.md) az Azure Cosmos DB-ben
