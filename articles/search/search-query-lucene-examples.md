---
title: Lucene-lekérdezési példák – Azure Search
description: A Lucene lekérdezési szintaxisa a fuzzy kereséshez, a közelségi kereséshez, a kifejezés fokozásához, a reguláris kifejezéses kereséshez és a helyettesítő karakteres keresésekhez egy Azure Search szolgáltatásban.
author: HeidiSteen
manager: cgronlun
tags: Lucene query analyzer syntax
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 53a1f09fcc9897f4def565a9119ad97ca365cae3
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68882754"
---
# <a name="query-examples-using-full-lucene-search-syntax-advanced-queries-in-azure-search"></a>Példák lekérdezésére a "teljes" Lucene keresési szintaxis használatával (Azure Search speciális lekérdezései)

Azure Search-lekérdezések létrehozásakor lecserélheti az alapértelmezett [egyszerű lekérdezés](query-simple-syntax.md) -elemzőt a több expanzív [Lucene lekérdezés-elemzővel](query-lucene-syntax.md) a speciális és speciális lekérdezési definíciók kidolgozásához Azure Search. 

A Lucene-elemző összetett lekérdezési szerkezeteket támogat, például a mezők hatókörű lekérdezéseket, a fuzzy és az előtag helyettesítő karakteres keresését, a közelségi keresést, a kifejezés növelését és a reguláris kifejezések keresését. További feldolgozási követelmények is elérhetők, ezért érdemes lehet valamivel több végrehajtási időt várnia. Ebben a cikkben áttekintheti a teljes szintaxis használatakor elérhető lekérdezési műveleteket bemutató példákat.

> [!Note]
> A teljes Lucene lekérdezési szintaxison keresztül engedélyezett speciális lekérdezési szerkezetek közül sok nem kerül [szöveg-elemzésre](search-lucene-query-architecture.md#stage-2-lexical-analysis), ami meglepő lehet, ha az eredmény vagy a morfológiai elemzéshez. A lexikális analízis csak teljes feltételekkel (lekérdezés vagy kifejezéses lekérdezés) hajtható végre. Hiányos kifejezésekkel rendelkező lekérdezések (előtag-lekérdezés, helyettesítő karakteres lekérdezés, regex lekérdezés, fuzzy lekérdezés) közvetlenül a lekérdezési fában lesznek hozzáadva, az elemzési szakasz megkerülésével. Az egyetlen, hiányos lekérdezési feltételeken végrehajtott átalakítás lowercasing. 
>

## <a name="formulate-requests-in-postman"></a>Kérelmek összeállítása a Poster-ban

Az alábbi példákban a [New York OpenData Initiative City](https://opendata.cityofnewyork.us/) által biztosított adatkészletek alapján elérhető feladatokat tartalmazó NYC-feladatok keresési indexét használjuk. Ezek az adathalmazok nem tekintendők aktuálisnak vagy teljesnek. Az index a Microsoft által biztosított sandbox-szolgáltatáson alapul, ami azt jelenti, hogy nincs szükség Azure-előfizetésre vagy Azure Search a lekérdezések kipróbálásához.

A GET-ben a HTTP-kérelem kiadásához szükséges Poster vagy azzal egyenértékű eszközre van szükség. További információ: [Ismerkedés a REST](search-get-started-postman.md)-ügyfelekkel.

### <a name="set-the-request-header"></a>A kérelem fejlécének beállítása

1. A kérelem fejlécében állítsa be a **Content-Type értéket** a következőre `application/json`:.

2. Adjon hozzá egy **API-kulcsot**, és állítsa be a következő sztringre: `252044BE3886FE4A8E3BAA4F595114BB`. Ez egy lekérdezési kulcs a NYC-feladatok indexét futtató sandbox Search szolgáltatáshoz.

A kérelem fejlécének megadását követően újra felhasználhatja azt a jelen cikk összes lekérdezéséhez, csak a **Search =** sztringet felcserélve. 

  ![Postman-kérelem fejléce](media/search-query-lucene-examples/postman-header.png)

### <a name="set-the-request-url"></a>A kérelem URL-címének beállítása

A kérelem egy GET parancs, amely a Azure Search végpontot és a keresési karakterláncot tartalmazó URL-címmel párosul.

  ![Postman-kérelem fejléce](media/search-query-lucene-examples/postman-basic-url-request-elements.png)

Az URL-összeállítás a következő elemekből áll:

+ **`https://azs-playground.search.windows.net/`** a a Azure Search fejlesztői csapat által karbantartott sandbox-keresési szolgáltatás. 
+ **`indexes/nycjobs/`** a New York Jobs index a szolgáltatás indexek gyűjteményében. A kéréshez a szolgáltatás nevét és indexét is meg kell adni.
+ **`docs`** az összes kereshető tartalmat tartalmazó dokumentumok gyűjteménye. A kérelem fejlécében megadott lekérdezési API-kulcs csak olyan olvasási műveleteken működik, amelyek a dokumentumok gyűjteményét célozzák meg.
+ **`api-version=2019-05-06`** Beállítja az API-verziót, amely minden kérelem esetében kötelező paraméter.
+ **`search=*`** a lekérdezési karakterlánc, amely a kezdeti lekérdezésben null értékű, és az első 50 eredményt adja vissza (alapértelmezés szerint).

## <a name="send-your-first-query"></a>Az első lekérdezés elküldése

Ellenőrzési lépésként illessze be a következő kérelmet a GET mezőbe, és kattintson a **Küldés**gombra. Az eredményeket a rendszer részletes JSON-dokumentumként adja vissza. A rendszer a teljes dokumentumot adja vissza, ami lehetővé teszi az összes mező és az összes érték megtekintését.

Illessze be ezt az URL-címet egy REST-ügyfélbe érvényesítési lépésként, és tekintse meg a dokumentum szerkezetét.

  ```http
  https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search=*
  ```

A lekérdezési karakterlánc **`search=*`** () nem meghatározott keresés, amely egyenértékű null vagy üres kereséssel. Ez a legegyszerűbb keresési lehetőség.

Opcionálisan hozzáadhatja **`$count=true`** az URL-címet a keresési feltételeknek megfelelő dokumentumok számának visszaadásához. Üres keresési sztring esetén ez az indexben található összes dokumentum (körülbelül 2800 a NYC-feladatok esetében).

## <a name="how-to-invoke-full-lucene-parsing"></a>A teljes Lucene-elemzés meghívása

Adja hozzá a **queryType = Full** parancsot a teljes lekérdezési szintaxis meghívásához, felülbírálva az alapértelmezett egyszerű lekérdezési szintaxist. 

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&search=*
```

A cikkben szereplő összes példa megadja a **queryType = teljes** keresési paramétert, amely azt jelzi, hogy a teljes szintaxist a Lucene lekérdezés-elemzője kezeli. 

## <a name="example-1-query-scoped-to-a-list-of-fields"></a>1\. példa: Lekérdezés hatóköre a mezők listájára

Ez az első példa nem Lucene, de az első alapvető lekérdezési koncepció bevezetéséhez vezetünk: a mező hatóköre. Ez a példa a teljes lekérdezést és a választ csak néhány konkrét mezőre vonatkozik. Az olvasható JSON-válaszok szerkezetének ismerete fontos, ha az eszköz Poster vagy Search Explorer. 

A rövidség kedvéért a lekérdezés csak a *business_title* mezőt célozza meg, és csak az üzleti címeket adja vissza. A **searchFields** paraméter korlátozza a lekérdezés végrehajtását csak a business_title mezőre, és **válassza ki** , hogy mely mezők szerepeljenek a válaszban.

### <a name="partial-query-string"></a>Részleges lekérdezési karakterlánc

```http
&search=*&searchFields=business_title&$select=business_title
```

Itt ugyanaz a lekérdezés, amelyben több mező található a vesszővel tagolt listában.

```http
search=*&searchFields=business_title, posting_type&$select=business_title, posting_type
```

A vesszők utáni szóközök nem kötelezőek.

> [!Tip]
> Ha az alkalmazás kódjából REST API használ, ne felejtsen el URL-kódolású paramétereket `$select` ( `searchFields`például és).

### <a name="full-url"></a>Teljes URL-cím

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&search=*&searchFields=business_title&$select=business_title
```

A lekérdezésre adott válasznak az alábbi képernyőképhez hasonlóan kell kinéznie.

  ![Poster-minta válasz](media/search-query-lucene-examples/postman-sample-results.png)

Lehetséges, hogy észrevette a keresési pontszámot a válaszban. 1 egységes pontszám akkor fordul elő, ha nincs rangsor, vagy mert a keresés nem teljes szöveges keresés, vagy nem lett alkalmazva. A feltétel nélküli null kereséshez a sorok tetszőleges sorrendben jönnek vissza. A tényleges keresési feltételek befoglalásakor a keresési pontszámok jelentős értékekre lesznek kialakítva.

## <a name="example-2-fielded-search"></a>2\. példa Mező szerinti keresés

A teljes Lucene szintaxisa támogatja az egyes keresési kifejezések adott mezőhöz való hatókörét. Ez a példa olyan üzleti címeket keres, amelyek a legfelső szintűek, de nem a Junior kifejezéssel rendelkeznek.

### <a name="partial-query-string"></a>Részleges lekérdezési karakterlánc

```http
$select=business_title&search=business_title:(senior NOT junior)
```

Itt ugyanaz a lekérdezés több mezővel.

```http
$select=business_title, posting_type&search=business_title:(senior NOT junior) AND posting_type:external
```

### <a name="full-url"></a>Teljes URL-cím

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&$select=business_title&search=business_title:(senior NOT junior)
```

  ![Poster-minta válasz](media/search-query-lucene-examples/intrafieldfilter.png)

A **mezőnév: searchExpression** szintaxissal megadhat egy mezőhöz tartozó keresési műveletet, ahol a keresési kifejezés lehet egyetlen szó vagy kifejezés, vagy egy összetettebb kifejezés zárójelben, opcionálisan logikai operátorokkal. Néhány példa a következőkre:

- `business_title:(senior NOT junior)`
- `state:("New York" OR "New Jersey")`
- `business_title:(senior NOT junior) AND posting_type:external`

Ügyeljen arra, hogy az idézőjelek között több karakterláncot is helyezzen el, ha azt szeretné, hogy mindkét sztring egyetlen entitásként legyen kiértékelve, mivel ebben az esetben `state` két különböző helyet keres a mezőben. Győződjön meg arról is, hogy az operátor olyan tőkésített, ahogyan a nem és a és a is látható.

A **mezőnév: searchExpression** mezőben megadott mezőnek kereshető mezőnek kell lennie. További részletekért lásd: [create index (Azure Search Service REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index) .

> [!NOTE]
> A fenti példában nem volt szükség a `searchFields` paraméter használatára, mert a lekérdezés egyes részeihez explicit módon meg van adva a mező neve. Azonban továbbra is használhatja a `searchFields` paramétert, ha olyan lekérdezést szeretne futtatni, amelyben bizonyos részek hatóköre egy adott mezőre vonatkozik, a REST pedig több mezőre is vonatkozhat. Például a lekérdezés `search=business_title:(senior NOT junior) AND external&searchFields=posting_type` csak `business_title` a mezőhöz `senior NOT junior` igazodik, míg a "külső" értékkel egyezik a `posting_type` mezővel. A Mezőnév mezőben megadott mezőnév **: a searchExpression** mindig elsőbbséget élvez a `searchFields` paraméterrel szemben, ezért ebben a példában nem kell belefoglalni `business_title` a `searchFields` paraméterbe.

## <a name="example-3-fuzzy-search"></a>3\. példa: Fuzzy keresés

A teljes Lucene szintaxisa támogatja a zavaros keresést, és a hasonló szerkezettel rendelkező feltételek egyeztetését is. A zavaros kereséshez fűzze hozzá a tilde `~` szimbólumot egyetlen szó végén egy opcionális paraméterrel, egy 0 és 2 közötti értéket, amely megadja a szerkesztési távolságot. `blue~` Például`blue~1` a kék, a blues és a ragasztó is visszatérhet.

### <a name="partial-query-string"></a>Részleges lekérdezési karakterlánc

```http
searchFields=business_title&$select=business_title&search=business_title:asosiate~
```

A kifejezések nem támogatottak közvetlenül, de egy kifejezés összetevő részeinél megadható egy homályos egyezés.

```http
searchFields=business_title&$select=business_title&search=business_title:asosiate~ AND comm~ 
```


### <a name="full-url"></a>Teljes URL-cím

Ez a lekérdezés a "hozzárendelés" kifejezéssel rendelkező feladatokat keresi meg (szándékosan hibásan írt):

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:asosiate~
```
  ![Fuzzy keresési válasz](media/search-query-lucene-examples/fuzzysearch.png)


> [!Note]
> A zavaros lekérdezések [elemzése](search-lucene-query-architecture.md#stage-2-lexical-analysis)nem történik meg. Hiányos kifejezésekkel rendelkező lekérdezések (előtag-lekérdezés, helyettesítő karakteres lekérdezés, regex lekérdezés, fuzzy lekérdezés) közvetlenül a lekérdezési fában lesznek hozzáadva, az elemzési szakasz megkerülésével. Az egyetlen, hiányos lekérdezési feltételeken végrehajtott átalakítás lowercasing.
>

## <a name="example-4-proximity-search"></a>4\. példa: Közelség keresése
A közelségi keresések megkeresik a dokumentumokban egymáshoz közeli kifejezéseket. Szúrjon be egy "~" szimbólumot egy kifejezés végén, majd a közelségi határt létrehozó szavak számát. Például a "Hotel Airport" ~ 5 megkeresi a (z) és a repülőtér használati feltételeit egy dokumentumban lévő, egymástól eltérő 5 szóból.

### <a name="partial-query-string"></a>Részleges lekérdezési karakterlánc

```http
searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~1
```

### <a name="full-url"></a>Teljes URL-cím

Ebben a lekérdezésben a "vezető elemző" kifejezéssel rendelkező feladatok esetében, ha az elválasztó karakter nem több, mint egy szó:

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~1
```
  ![Közelségi lekérdezés](media/search-query-lucene-examples/proximity-before.png)

Próbálkozzon újra a "vezető elemző" kifejezés közötti szavak eltávolításával. Figyelje meg, hogy a lekérdezés 8 dokumentumot ad vissza, az előző lekérdezés esetében pedig 10.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~0
```

## <a name="example-5-term-boosting"></a>5\. példa: A kifejezés fokozása
A kifejezés növelése arra utal, hogy a dokumentum rangsorolása magasabb, ha a megnövelt kifejezést tartalmazza, a kifejezést nem tartalmazó dokumentumokhoz képest. Egy kifejezés növeléséhez használja a "^" karaktert, a Symbol értéket a keresett kifejezés végén (a szám). 

### <a name="full-urls"></a>Teljes URL-címek

Ebben az "előtt" lekérdezésben keressen feladatokat a *számítógép-elemző* kifejezéssel, és figyelje meg, hogy a számítógépek és az *elemzők*között nincs találat, de a *számítógép* -feladatok még a találatok elején vannak.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:computer%20analyst
```
  ![Kiemelési idő](media/search-query-lucene-examples/termboostingbefore.png)

Az "After" lekérdezésben ismételje meg a keresést, ez az idő fokozza az eredményeket az *elemző* kifejezéssel a *számítógép* kifejezése alatt, ha a két szó nem létezik. 

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:computer%20analyst%5e2
```
A fenti lekérdezés `search=business_title:computer analyst^2`egy olvasható verziója. Egy működőképes lekérdezés `^2` esetén a kódolása `%5E2`a következőként történik:, ami nehezebben látható.

  ![Kiemelési időszak](media/search-query-lucene-examples/termboostingafter.png)

A kifejezés fokozása különbözik az adott pontozási profilokban lévő pontozási profiloktól, és nem adott feltételekkel javítja bizonyos mezőket. A következő példa segít illusztrálni a különbségeket.

Vegyünk egy pontozási profilt, amely fellendíti a egyezéseket egy adott mezőben , például a műfajt a musicstoreindex példában. A kifejezés fokozása felhasználható a másoknál nagyobb keresési kifejezések további növelésére. Például a "Rock ^ 2 Electronic" olyan dokumentumokat fog növelni, amelyekben a kifejezés a **műfaj** mezőben magasabb, mint az index más kereshető mezőinél is szerepel. Továbbá a "rock" keresési kifejezést tartalmazó dokumentumok nagyobbak lesznek, mint a "elektronikus" keresési kifejezés, amely a "KRADEB" kifejezés eredményét eredményezi (2).

A faktor szintjének beállításakor minél magasabb a növelési tényező, annál nagyobb a kifejezés, mint a többi keresési kifejezéshez képest. Alapértelmezés szerint a Boost faktor 1. Bár a Boost faktornak pozitívnak kell lennie, kevesebb, mint 1 (például 0,2).


## <a name="example-6-regex"></a>6\. példa: Regex

A reguláris kifejezéses keresés a "/" perjelek közötti, a [regexp osztályban](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html)dokumentált tartalom alapján keres egyezést.

### <a name="partial-query-string"></a>Részleges lekérdezési karakterlánc

```http
searchFields=business_title&$select=business_title&search=business_title:/(Sen|Jun)ior/
```

### <a name="full-url"></a>Teljes URL-cím

Ebben a lekérdezésben keressen olyan feladatokat, amelyek a Senior vagy a Junior kifejezéssel rendelkeznek: `search=business_title:/(Sen|Jun)ior/`.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:/(Sen|Jun)ior/
```

  ![Regex-lekérdezés](media/search-query-lucene-examples/regex.png)

> [!Note]
> A regex-lekérdezések [elemzése](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis)nem történik meg. Az egyetlen, hiányos lekérdezési feltételeken végrehajtott átalakítás lowercasing.
>

## <a name="example-7-wildcard-search"></a>7\. példa: Helyettesítő karakteres keresés
Az általánosan felismerhető szintaxist használhatja több (\*) vagy szimpla (?) karakteres helyettesítő karakteres kereséshez. Vegye figyelembe, hogy a Lucene-lekérdezés elemzője egyetlen kifejezéssel támogatja a szimbólumok használatát, nem pedig egy kifejezést.

### <a name="partial-query-string"></a>Részleges lekérdezési karakterlánc

```http
searchFields=business_title&$select=business_title&search=business_title:prog*
```

### <a name="full-url"></a>Teljes URL-cím

Ebben a lekérdezésben keressen olyan feladatokat, amelyek tartalmazzák a "PROG" előtagot, amely magában foglalja az üzleti címeket a programozási és programozói feltételekkel. Nem használhat * vagy? a szimbólum a keresés első karaktere.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:prog*
```
  ![Helyettesítő karakteres lekérdezés](media/search-query-lucene-examples/wildcard.png)

> [!Note]
> A helyettesítő karakteres lekérdezések [elemzése](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis)nem történik meg. Az egyetlen, hiányos lekérdezési feltételeken végrehajtott átalakítás lowercasing.
>

## <a name="next-steps"></a>További lépések
Próbálja megadni a Lucene lekérdezés-elemzőjét a kódban. A következő hivatkozások ismertetik, hogyan állíthat be keresési lekérdezéseket a .NET-hez és a REST APIhoz. A hivatkozások az alapértelmezett egyszerű szintaxist használják, így a **queryType**megadásához alkalmaznia kell a cikkből megtanult tudnivalókat.

* [A Azure Search index lekérdezése a .NET SDK használatával](search-query-dotnet.md)
* [Azure Search index lekérdezése a REST API használatával](search-create-index-rest-api.md)

A szintaxissal, a lekérdezési architektúrával és a példákkal kapcsolatban a következő hivatkozásokban találhat további tudnivalókat:

+ [Példák egyszerű szintaxisú lekérdezésekre](search-query-simple-examples.md)
+ [A teljes szöveges keresés működése Azure Search](search-lucene-query-architecture.md)
+ [Egyszerű lekérdezési szintaxis](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Teljes Lucene lekérdezési szintaxis](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)