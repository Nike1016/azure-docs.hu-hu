---
title: A vadászati képességek az Azure Sentinel Preview-ban | Microsoft Docs
description: Ez a cikk az Azure Sentinel-vadászati képességeinek használatát ismerteti.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 6aa9dd27-6506-49c5-8e97-cc1aebecee87
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 5e6ad3c0b415722349dc584434add1031b7c3cb1
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780464"
---
# <a name="hunt-for-threats-with-in-azure-sentinel-preview"></a>A fenyegetések vadászata az Azure Sentinel előzetes verziójával

> [!IMPORTANT]
> Az Azure Sentinel jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ha Ön olyan nyomozó, aki proaktívan szeretné megkeresni a biztonsági fenyegetéseket, az Azure Sentinel hatékony vadászati és lekérdezési eszközöket kínál a szervezet adatforrásaiban felhasználható biztonsági fenyegetések felderítéséhez. A rendszerek és a biztonsági berendezések azonban olyan adathegyeket hoznak, amelyek nehezen elemezhetők és szűrhetők az értelmes események alapján. Ahhoz, hogy a biztonsági elemzők proaktív módon megnézzék a biztonsági alkalmazások által nem észlelt új rendellenességeket, az Azure Sentinel beépített vadászati lekérdezései segítséget nyújtanak a hálózatban már meglévő adataival kapcsolatos problémák megtalálásában. 

Például az egyik beépített lekérdezés szolgáltatja az infrastruktúrán futó leggyakoribb folyamatok adatait – nem szeretne riasztást kapni minden egyes futtatáskor, teljesen ártatlan lehet, de érdemes megtekinteni a lekérdezést időnként, hogy látható-e a th ere semmi szokatlant. 



Az Azure Sentinel-vadászattal az alábbi lehetőségek közül választhat:

- Beépített lekérdezések: Az első lépésekhez a kezdőlapon előre betöltött lekérdezési példákat talál, amelyek az első lépésekhez készültek, és megismerheti a táblákat és a lekérdezési nyelvet. Ezeket a beépített vadászati lekérdezéseket folyamatosan fejlesztjük a Microsoft biztonsági kutatói, új lekérdezések hozzáadásával és a meglévő lekérdezések finomhangolásával, amely lehetővé teszi az új észlelések keresését, és kideríteni, hogy hol kezdjen el vadászni a új támadások kezdete. 

- Hatékony lekérdezési nyelv IntelliSense-vel: Egy olyan lekérdezési nyelvre épül, amely a következő szintre való vadászathoz szükséges rugalmasságot biztosítja.

- Saját könyvjelzők létrehozása: A vadászati folyamat során előfordulhat, hogy a találatok, az irányítópultok vagy a szokatlan vagy gyanús tevékenységek között találkozhatnak. Ha meg szeretné jelölni ezeket az elemeket, hogy később vissza lehessen őket használni, használja a könyvjelző funkciót. A könyvjelzők lehetővé teszik, hogy később mentse az elemeket, hogy egy incidenst hozzon létre a vizsgálathoz. A könyvjelzők használatával kapcsolatos további információkért lásd: [könyvjelzők használata a vadászatban].

- Jegyzetfüzetek használata a vizsgálat automatizálásához: A jegyzetfüzetek olyan lépésről lépésre haladó forgatókönyvek, amelyeket a vizsgálat és a vadászat lépésein hozhat létre.  A jegyzetfüzetek egy újrafelhasználható forgatókönyvben lévő összes vadászati lépést bemutatnak, amelyek megoszthatók másokkal a szervezeten belül. 
- A tárolt adatértékek lekérdezése: Az adattábla a lekérdezéshez érhető el. Lekérdezheti például a folyamat létrehozását, a DNS-eseményeket és számos más eseménytípus lekérdezését.

- A Közösségre mutató hivatkozások: A nagyobb Közösség hatékonyságát kihasználva további lekérdezéseket és adatforrásokat kereshet.
 
## <a name="get-started-hunting"></a>Ismerkedés a vadászattal

1. Az Azure Sentinel-portálon kattintson a **vadászat**gombra.
  ![Az Azure Sentinel vadászatot kezd](media/tutorial-hunting/hunting-start.png)

2. A vadászati oldal megnyitásakor az összes vadászati lekérdezés egyetlen táblában jelenik meg. A táblázat felsorolja a Microsoft biztonsági elemzők csapata által írt összes lekérdezést, valamint a létrehozott vagy módosított további lekérdezéseket. Mindegyik lekérdezés leírja, hogy mire vadászik, és milyen típusú adatfeldolgozást futtat. Ezek a sablonok különböző taktikák szerint vannak csoportosítva – a jobb oldali ikonok kategorizálják a fenyegetés típusát, például a kezdeti hozzáférést, az adatmegőrzést és a kiszűrése. Ezeket a vadászati lekérdezési sablonokat bármely mező alapján szűrheti. Bármilyen lekérdezést menthet a Kedvencek közé. Ha egy lekérdezést a Kedvencek közé ment, a lekérdezés automatikusan lefut, amikor a **vadászati** oldal elérhető. Létrehozhatja saját vadászati lekérdezését vagy klónját, és testre is szabhatja a meglévő vadászati lekérdezési sablont. 
 
2. Kattintson a **lekérdezés futtatása** elemre a vadászati lekérdezés részletei lapon, hogy bármilyen lekérdezést futtasson anélkül, hogy elhagyja a vadászati oldalt.  A egyezések száma a táblában jelenik meg. Tekintse át a vadászati lekérdezések listáját és azok egyezéseit. Tekintse meg, hogy a kill lánc melyik szakaszában van társítva a egyezés.

3. Hajtsa végre az alapul szolgáló lekérdezés gyors áttekintését a lekérdezés részletei panelen, vagy kattintson a **lekérdezési eredmény megtekintése** elemre a lekérdezés megnyitásához log Analyticsban. Az alsó sarokban tekintse át a lekérdezés egyezéseit.

4.  Kattintson a sorra, és válassza a **Könyvjelző hozzáadása** lehetőséget a megvizsgálni kívánt sorok hozzáadásához – ezt megteheti a gyanúsnak tűnő elemekért. 

5. Ezután lépjen vissza a fő **vadászat** oldalra, és kattintson a **könyvjelzők** lapra az összes gyanús tevékenység megtekintéséhez. 

6. Jelöljön ki egy könyvjelzőt, majd kattintson a vizsgálat gombra a vizsgálati élmény megnyitásához. Szűrheti a könyvjelzőket. Ha például egy kampányt vizsgál, létrehozhat egy címkét a kampányhoz, majd a kampány alapján szűrheti az összes könyvjelzőt.

1. Miután felfedezte, hogy melyik vadászati lekérdezés magas értéket biztosít a lehetséges támadásoknak, létrehozhat egyéni észlelési szabályokat is a lekérdezés alapján, és felhasználhatja ezeket az elemzéseket riasztásként a biztonsági incidensek válaszadói számára.

 

## <a name="query-language"></a>Lekérdezés nyelve 

Az Azure Sentinelben való vadászat az Azure Log Analytics lekérdezési nyelvén alapul. A lekérdezési nyelvre és a támogatott operátorokra vonatkozó további információkért lásd a [lekérdezés nyelvi referenciája](https://docs.loganalytics.io/docs/Language-Reference/)című témakört.

## <a name="public-hunting-query-github-repository"></a>Nyilvános vadászati lekérdezés GitHub-tárháza

Tekintse meg a [vadászati lekérdezés tárházát](https://github.com/Azure/Orion). Az ügyfelek által közösen használt példa lekérdezéseket is használhat.

 

## <a name="sample-query"></a>Példa lekérdezésre

Egy tipikus lekérdezés a tábla nevével kezdődik, majd egy, a által \|elválasztott operátorok sorozata.

A fenti példában Kezdje a Table Name SecurityEvent, és szükség szerint adja hozzá a vezetékes elemeket.

1. Definiáljon egy időszűrőt, amely csak az előző hét nap rekordjait vizsgálja meg.

2. Adjon hozzá egy szűrőt a lekérdezésben, hogy csak a 4688-es AZONOSÍTÓJÚ esemény jelenjen meg.

3. Vegyen fel egy szűrőt a parancssori lekérdezésben, hogy csak a cscript. exe példányait tartalmazza.

4. Csak azokat az oszlopokat adja meg, amelyeket szeretne felderíteni, és korlátozza az eredményeket 1000-re, és kattintson a **lekérdezés futtatása**gombra.
5. Kattintson a zöld háromszögre, és futtassa a lekérdezést. Tesztelheti a lekérdezést, és futtathatja a rendellenes viselkedés kereséséhez.

## <a name="useful-operators"></a>Hasznos operátorok

A lekérdezés nyelve hatékony, és számos elérhető operátorral rendelkezik, néhány hasznos operátor itt látható:

**Where** – egy tábla szűrése egy predikátumot teljesítő sorok részhalmazára.

**Összefoglalás** – létrehoz egy táblázatot, amely összesíti a bemeneti tábla tartalmát.

**Csatlakozás** – egyesítse a két tábla sorait úgy, hogy az egyes táblák megadott oszlopainak megfelelő értékeivel létrehoz egy új táblázatot.

**darabszám** – a bemeneti rekordhalmazban lévő rekordok számának visszaadása.

**Top** – visszaadja az első N rekordot a megadott oszlopok szerint rendezve.

**korlátozás** – a megadott számú sorra visszatérhet.

**projekt** – jelölje ki a belefoglalni, átnevezni vagy eldobni kívánt oszlopokat, és szúrjon be új számított oszlopokat.

**kiterjesztés** – számított oszlopok létrehozása és hozzáfűzése az eredményhalmaz számára.

**makeset** – a csoportba foglalt egyedi értékek halmazának dinamikus (JSON) tömbjét adja vissza.

**Find (keresés** ) – olyan sorok megkeresése, amelyek megfelelnek egy predikátumnak egy adott halmazon belül.

## <a name="save-a-query"></a>Lekérdezés mentése

Létrehozhat vagy módosíthat egy lekérdezést, és mentheti saját lekérdezésként, vagy megoszthatja azokat a felhasználókkal, akik ugyanabban a bérlőben vannak.

   ![Lekérdezés mentése](./media/tutorial-hunting/save-query.png)

Hozzon létre egy új vadászati lekérdezést:

1. Kattintson az **Új lekérdezés** elemre, majd válassza a **Mentés**lehetőséget.
2. Töltse ki az összes üres mezőt, és kattintson a **Mentés**gombra.

   ![Új lekérdezés](./media/tutorial-hunting/new-query.png)

Meglévő vadászati lekérdezés klónozása és módosítása:

1. Válassza ki a vadászati lekérdezést a módosítani kívánt táblázatban.
2. Válassza ki a módosítani kívánt lekérdezés sorában található három pontot (...), majd válassza a **klónozási lekérdezés**elemet.

   ![klónozási lekérdezés](./media/tutorial-hunting/clone-query.png)
 

3. Módosítsa a lekérdezést, és válassza a **Létrehozás**lehetőséget.

   ![egyéni lekérdezés](./media/tutorial-hunting/custom-query.png)

## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, hogyan futtathat egy vadászati vizsgálatot az Azure Sentinel használatával. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:


- [Automatikus vadászati kampányok futtatása jegyzetfüzetek használatával](notebooks.md)
- [A könyvjelzők használatával érdekes információkat menthet a vadászat során](bookmarks.md)
