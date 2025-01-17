---
title: Lekérdezés bővítése keresztül a keresési index – Azure Search szinonimáiról
description: Hozzon létre egy szinonimatérképet, bontsa ki az Azure Search-index a keresési lekérdezés hatókörét. Hatókör van szélesíteni adnia egyenértékű kifejezések foglalandó listáját.
author: brjohnstmsft
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 05/02/2019
manager: jlembicz
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: 99abcc70a81622e4efbe85722d457bd1846b6e15
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485216"
---
# <a name="synonyms-in-azure-search"></a>Az Azure Search szolgáltatásban a szinonimák

A keresőmotorok szinonimák társítsa egyenértékű kifejezéseket, amely implicit módon bontsa ki a lekérdezés hatókörét a felhasználónak nem kell ténylegesen adja meg az előfizetési időszak nélkül. Például adja meg a kifejezés "kutya" és a szinonima társítások "canine" és "ételadagot", "kutya" tartalmazó dokumentumok, "canine" vagy "ételadagot" csökken a lekérdezés hatókörén belül.

Az Azure Search szolgáltatásban a szinonima bővítése lekérdezések során történik. Szolgáltatás, amely nincs meglévő műveletek megszakadását szinonimatérképet is hozzáadhat. Hozzáadhat egy **synonymMaps** anélkül, hogy kellene építenie az indexet egy mező definition tulajdonságát.

## <a name="create-synonyms"></a>A szinonimák létrehozása

Nem szinonimák létrehozására szolgáló portál támogatott, de a REST API vagy .NET SDK-t is használhatja. REST – első lépések, azt javasoljuk [a postmannel](search-get-started-postman.md) és az API-kérések kialakításában: [Hozzon létre Szinonimatérképet](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map). A C# fejlesztők, megkezdheti az [szinonimák hozzáadása az Azure-keresés- C# ](search-synonyms-tutorial-sdk.md).

Igény szerint ha használ [ügyfél által felügyelt kulcsokat](search-security-manage-encryption-keys.md) Szolgáltatásoldali titkosítás inaktív, a alkalmazhat protection által a szinonimatérkép tartalmát.

## <a name="use-synonyms"></a>A szinonimák használata

Az Azure Search szolgáltatásban a szinonima támogatási szinonimatérképet, amely meghatározza, és töltse fel a szolgáltatás alapul. Ezeket a térképeket (például indexek vagy adatforrások) egy független erőforrást alkotnak, és használhatja a keresési szolgáltatásban lévő bármely indexben bármely kereshető mezőjében.

Szinonima vannak leképezve, és az indexek karbantartása egymástól függetlenül. Meghatározása egy szinonimatérképet, és töltse fel azt a szolgáltatást, a egy mezőben szinonimát szolgáltatás nevű új tulajdonsággal hozzáadásával engedélyezheti **synonymMaps** a mező-definícióban. Létrehozása, frissítése és törlése egy szinonimatérképet, mindig egy teljes-dokumentum műveletét, ami azt jelenti, hogy Ön nem létrehozása, frissítése vagy törlése a szinonimatérkép részeit növekményes. Még egy-egy bejegyzésnek frissítése szükséges egy töltse be újra.

A szinonimák beépítése a keresőalkalmazás két lépésből áll:

1.  Adjon hozzá egy szinonimatérképet a keresőszolgáltatáshoz az alábbi API-kon keresztül.  

2.  A szinonimatérkép indexdefinícióban használandó kereshető mező konfigurálása.

### <a name="synonymmaps-resource-apis"></a>SynonymMaps erőforrás API-k

#### <a name="add-or-update-a-synonym-map-under-your-service-using-post-or-put"></a>Adja hozzá, vagy frissíteni a szolgáltatás alatt egy szinonimatérképet, használatával KÖZZÉTENNI, vagy HELYEZZE.

Szinonimatérképet vannak töltve a szolgáltatásba, POST és PUT-n keresztül. Minden szabály az új sor karakter ("\n") kell elválasztani. Legfeljebb 5000 szabályok száma egy ingyenes szolgáltatás a szinonimatérkép és 10 000 szabályok az összes többi termékváltozat határozhatja meg. Minden szabály legfeljebb 20 bővülésből rendelkezhet.

Szinonimatérképet az alábbiakban ismertetett Apache Solr formátumúnak kell lennie. Ha rendelkezik egy meglévő szinonimát szótár más formátumban, és közvetlenül használni szeretne, vegye fel velünk a [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

Létrehozhat egy új szinonimatérképet HTTP POST, használja az alábbi példában látható módon:

    POST https://[servicename].search.windows.net/synonymmaps?api-version=2019-05-06
    api-key: [admin key]

    {
       "name":"mysynonymmap",
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }

Másik lehetőségként a PUT, és adja meg a szinonimát leképezés nevét az URI-t. Ha nem létezik a szinonimatérkép, létrejön.

    PUT https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2019-05-06
    api-key: [admin key]

    {
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }

##### <a name="apache-solr-synonym-format"></a>Az Apache Solr szinonimát formátum

A Solr formátum egyenértékű és explicit szinonimaleképezéseket támogatja. Leképezési szabályok kövessék a nyílt forráskódú szinonimát szűrőspecifikáció elérje az Apache Solr, jelen dokumentumban ismertetett: [SynonymFilter](https://cwiki.apache.org/confluence/display/solr/Filter+Descriptions#FilterDescriptions-SynonymFilter). Alul látható egy példa szabály egyenértékű szinonimák.
```
USA, United States, United States of America
```

A fenti keresési lekérdezést a szabály az "USA" bővített "USA" vagy "Egyesült Államok" vagy "Az Amerikai Egyesült Államok".

Explicit leképezés nyíl helyén "= >". Megadása esetén egy keresési lekérdezést, amely megfelel a bal oldalon az előfizetési időszak sorozatát "= >" váltja fel a lehetőségeket, a jobb oldalon. Adja meg az alábbi szabály, keresési lekérdezéseket "Washington", "Wash." vagy a "WA" az összes felülíródik a "WA". Explicit leképezés csak a megadott irányba vonatkozik, és nem írja újra a lekérdezést a "Washington" a "WA" Ebben az esetben.
```
Washington, Wash., WA => WA
```

#### <a name="list-synonym-maps-under-your-service"></a>Lista szinonimát leképezi a szolgáltatás alatt.

    GET https://[servicename].search.windows.net/synonymmaps?api-version=2019-05-06
    api-key: [admin key]

#### <a name="get-a-synonym-map-under-your-service"></a>A szolgáltatás alatt egy szinonimatérképet beolvasása.

    GET https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2019-05-06
    api-key: [admin key]

#### <a name="delete-a-synonyms-map-under-your-service"></a>A szolgáltatás alatt a szinonimák leképezés törlése.

    DELETE https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2019-05-06
    api-key: [admin key]

### <a name="configure-a-searchable-field-to-use-the-synonym-map-in-the-index-definition"></a>A szinonimatérkép indexdefinícióban használandó kereshető mező konfigurálása.

Új mező tulajdonság **synonymMaps** egy szinonimatérképet egy kereshető mező használandó megadásához használható. Szinonimatérképet szolgáltatási szint erőforrások és a szolgáltatásban az index bármely mező alapján lehet rá hivatkozni.

    POST https://[servicename].search.windows.net/indexes?api-version=2019-05-06
    api-key: [admin key]

    {
       "name":"myindex",
       "fields":[
          {
             "name":"id",
             "type":"Edm.String",
             "key":true
          },
          {
             "name":"name",
             "type":"Edm.String",
             "searchable":true,
             "analyzer":"en.lucene",
             "synonymMaps":[
                "mysynonymmap"
             ]
          },
          {
             "name":"name_jp",
             "type":"Edm.String",
             "searchable":true,
             "analyzer":"ja.microsoft",
             "synonymMaps":[
                "japanesesynonymmap"
             ]
          }
       ]
    }

**synonymMaps** kereshető mezőket "Edm.String" vagy "Collection(Edm.String)" típusú adható meg.

> [!NOTE]
> Legfeljebb egy szinonima mezőnként leképezése. Ha több szinonimatérképet használni kívánt, vegye fel velünk a [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

## <a name="impact-of-synonyms-on-other-search-features"></a>A keresési funkciókat a szinonimák hatása

A szinonimák szolgáltatást a szinonimákat a vagy művelettel az eredeti lekérdezés újraírja. Ebből kifolyólag a találatok kiemelése és pontozási profilok kezeli az eredeti kifejezés és szinonimák egyenértékűként.

Szinonima funkció keresési lekérdezéseket vonatkozik, és nem vonatkozik a szűrők vagy metszettel. Hasonlóképpen javaslatok alapuló csak az eredeti kifejezés; Szinonima egyezik a válaszban nem jelennek meg.

Szinonima bővülésből nem vonatkoznak a keresőkifejezést helyettesítő; előtag, az intelligens, és a reguláris kifejezés feltételei nem kibontva.

Ha kell tennie, amelyekre vonatkozik a szinonima bővítése és helyettesítő, reguláris kifejezéssel vagy az intelligens keresés egyetlen lekérdezést, kombinálhatja a lekérdezéseket, a OR szintaxis használatával. Például úgy, hogy a szinonimák helyettesítő karaktereket is tartalmazó egyszerű lekérdezési szintaxis, az előfizetési időszak lenne `<query> | <query>*`.

## <a name="tips-for-building-a-synonym-map"></a>Tippek a szinonimatérkép létrehozásához

- Egy rövid, jól megtervezett szinonimatérképet hatékonyabb, mint az lehetséges egyezések teljesnek. A túl nagy vagy összetett szótárak elemzése és a Lekérdezések késése hatással, ha a lekérdezés kibővíti a sok szinonimák hosszabb időt vesz igénybe. Ahelyett, hogy milyen feltételek használhatók találat, a tényleges használati keresztül kap egy [keresési forgalom elemzése – jelentés](search-traffic-analytics.md).

- Egy előzetes és érvényesítési gyakorolja engedélyezése, majd ez a jelentés pontosan határozza meg, hogy melyik feltételeit fog szinonimát egyezést kihasználhatják és majd továbbra is használhatja azt, hogy a szinonimatérkép az előállító jobb eredmény érvényesítése. Az előre meghatározott jelentésben a csempék "leggyakoribb keresési lekérdezések" és "eredmény nélküli keresési lekérdezések" felkínálja a szükséges információkat.

- Létrehozhat több szinonimatérképet a search-alkalmazás (például úgy, hogy ha az alkalmazás támogatja a többnyelvű ügyfélkörét language). Jelenleg egy mező csak egyikét használhatja őket. A mező synonymMaps tulajdonság bármikor frissítheti.

## <a name="next-steps"></a>További lépések

- Ha egy meglévő index (nem éles) fejlesztői környezetben, kísérletezhet egy kis szótár meg, hogy a szinonimák hozzáadásával hogyan változik a keresési funkciót, beleértve a hatása a pontozási profilok, a találatok kiemelése és javaslatok.

- [Forgalmi elemzések keresése engedélyezése](search-traffic-analytics.md) és az előre megadott jelentést a Power BI segítségével megtudhatja, mely kifejezések a legtöbb, és melyeket dokumentumot ad vissza. Ezen elemzési elemzőmodellek, vizsgálja felül a szótár tartalmazza, amely az indexben található dokumentumok kell lennie feloldása rendszer lekérdezések a szinonimák.
