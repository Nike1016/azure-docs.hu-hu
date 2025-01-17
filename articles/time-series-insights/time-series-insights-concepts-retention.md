---
title: Az adatmegőrzés ismertetése a Azure Time Series Insights-környezetben | Microsoft Docs
description: Ez a cikk két olyan beállítást ismertet, amelyek az adatmegőrzést vezérlik Azure Time Series Insights-környezetben.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 5388b157ebea78a69355eb745492910f260be3ad
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68823642"
---
# <a name="understand-data-retention-in-azure-time-series-insights"></a>Az adatmegőrzés ismertetése Azure Time Series Insights

Ez a cikk két olyan beállítást ismertet, amelyek hatással vannak az adatok megőrzésére a Azure Time Series Insights-környezetben.

## <a name="video"></a>Videó

### <a name="the-following-video-summarizes-time-series-insights-data-retention-and-how-to-plan-for-itbr"></a>Az alábbi videó összefoglalja Time Series Insights adatmegőrzést, és azt, hogyan tervezze meg.</br>

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

Az Azure idősorozat-környezetek mindegyike egy olyan beállítással rendelkezik, amely az **adatok megőrzési idejét**vezérli. Az érték 1 – 400 nap. Az adatok törlődnek a környezet tárolási kapacitása vagy a megőrzési időtartam alapján, attól függően, hogy melyik következik be először.

Emellett az Azure idősorozat-környezete túllépte a **tárolási korlátot** . A bejövő és a kiürítési viselkedést vezérli, ha elérik a környezetek maximális kapacitását. A konfigurálása során két viselkedés közül választhat:

- **Régi adattörlés** alapértelmezett  
- **Bejövő forgalom szüneteltetése**

> [!NOTE]
> Új környezet létrehozásakor alapértelmezés szerint a megőrzés a **régi adatok törlésére**van konfigurálva. Ez a beállítás a Time Series Insights-környezet **configure (Konfigurálás** ) lapjának a Azure Portal használatával történő létrehozása után igény szerint állítható be.

A megőrzési viselkedés váltásával kapcsolatos információkért tekintse át [az adatmegőrzés konfigurálása Time Series Insightsban](time-series-insights-how-to-configure-retention.md)című témakört.

Az adatmegőrzési viselkedés összehasonlítása:

## <a name="purge-old-data"></a>Régi adatok végleges törlése

- Ez a viselkedés Time Series Insights környezetek alapértelmezett viselkedése.  
- Ez a viselkedés akkor javasolt, ha a felhasználók mindig látják a legutóbbi adatTime Series Insights-környezetben lévő összes *információt* .
- Ez a viselkedés kiüríti az adatmennyiséget, ha a környezet korlátait (a megőrzési időt, a méretet vagy a darabszámot) eléri. Alapértelmezés szerint a megőrzés értéke 30 nap.
- A legrégebbi betöltött adatot a rendszer először megtisztítja (FIFO-megközelítés).

### <a name="example-one"></a>Példa egy

Vegye figyelembe, hogy az adatmegőrzési viselkedést biztosító környezet **továbbra is beáramlik és törli a régi adatokból**:

Az **adatok megőrzési ideje** 400 napra van állítva. A **kapacitás** értéke S1 egység, amely 30 GB teljes kapacitást tartalmaz.   Tegyük fel, hogy a bejövő adat átlagosan 500 MB-ra halmozódik. Ez a környezet a bejövő adatmennyiség miatt csak 60 napos adatot tud megőrizni, mivel a maximális kapacitás 60 nap. A bejövő adatmennyiség a következőképpen halmozódik: 500 MB minden nap x 60 nap = 30 GB.

A 61st napján a környezet a legfrissebb adatforrásokat jeleníti meg, de a legrégebbi, 60 napnál régebbi adat kiürítését. A kiürítés lehetővé teszi az új adatfolyam-továbbítást a alkalmazásban, így továbbra is megvizsgálhatja az új adatátvitelt. Ha a felhasználó továbbra is meg szeretné őrizni az adatmennyiséget, a további egységek hozzáadásával növelheti a környezet méretét, vagy kevesebb adat leküldését teszi lehetővé.  

### <a name="example-two"></a>Példa kettőre

Vegye figyelembe, hogy a környezet is konfigurálta az adatmegőrzési viselkedést **, és a régi adatok kiürítését folytatja**. Ebben a példában az **adatmegőrzési idő** a 180 napos alacsonyabb értékre van állítva. A **kapacitás** értéke S1 egység, amely 30 GB teljes kapacitást tartalmaz. Ahhoz, hogy a teljes 180 napig tárolja az adattárolást, a napi bejövő forgalom napi szinten nem haladhatja meg az 0,166 GB-ot (166 MB).  

Ha a környezet napi beléptetési aránya meghaladja a napi 0,166 GB-ot, az adat nem tárolható 180 nap alatt, mivel egyes adatmennyiségek törlődnek. Egy foglalt időkereten belül ugyanezt a környezetet érdemes figyelembe venni. Tegyük fel, hogy a környezet beléptetési aránya a napi átlagos 0,189 GB-ra nő. Ebben a foglalt időkeretben körülbelül 158 nap adat marad meg (30 GB/0.189 = 158,73 nap). Ez az idő kevesebb, mint a kívánt adatmegőrzési időszak.

## <a name="pause-ingress"></a>Bejövő forgalom szüneteltetése

- A **bejövő** forgalom felfüggesztése beállítás úgy van kialakítva, hogy az adatok ne legyenek kiürítve, ha a méret és a szám a megőrzési időtartam előtt eléri a korlátot.  
- A felfüggesztések szüneteltetése további időt biztosít a felhasználók számára, hogy a megőrzési idő megszegése miatt megnövelje a környezete kapacitását, mielőtt az adatok törlődnek.
- Segít az adatvesztés elleni védelemben, de létrehozhat egy lehetőséget a legutóbbi adatok elvesztésére, ha a bejövő forgalom szünetel az eseményforrás megőrzési időtartamán túl.
- A környezet maximális kapacitásának elérésekor azonban a környezet szünetelteti az adatbevitelt, amíg a következő további műveletek nem történnek:

   - A környezet maximális kapacitásának növelésével további méretezési egységeket adhat hozzá a [Time Series Insights-környezet skálázása](time-series-insights-how-to-scale-your-environment.md)című témakörben leírtak szerint.
   - A rendszer elérte az adatmegőrzési időszakot, és az adatok törlődnek, így a környezet a maximális kapacitás alatt marad.

### <a name="example-three"></a>Harmadik példa

Vegye fontolóra egy olyan környezet megőrzési viselkedését, amely a **bejövő**forgalom szüneteltetésére van konfigurálva. Ebben a példában az adatmegőrzési **időszak** 60 napra van konfigurálva. A **kapacitás** értéke az S1 három (3) egysége. Tegyük fel, hogy a környezet minden nap 2 GB-os adatforgalommal rendelkezik. Ebben a környezetben a bejövő forgalom szünetel a maximális kapacitás elérésekor.

Ebben az időben a környezet ugyanazt az adatkészletet jeleníti meg, amíg a bejövő állapot folytatódik, vagy amíg be nem fejeződik a **bejövő** forgalom engedélyezése (ami a régebbi adatokat törli, hogy helyet szabadítson fel az új adatokat).

A bejövő forgalom folytatásakor:

- Az adatfolyamatok az eseményforrás által fogadott sorrendben
- Az események az időbélyegük alapján vannak indexelve, kivéve, ha túllépte az adott eseményforrás adatmegőrzési szabályzatait. További információ az eseményforrás-megőrzési konfigurációról: [Event HUBS GYIK](../event-hubs/event-hubs-faq.md)

> [!IMPORTANT]
> Riasztásokat kell beállítania, hogy értesítést kapjon a bejövő forgalom szüneteltetésének elkerüléséhez. Az adatvesztés lehetséges, mivel az alapértelmezett megőrzési idő 1 nap az Azure-eseményeknél. Ezért a bejövő forgalom felfüggesztése után valószínűleg elveszíti a legutóbbi adatvesztést, kivéve, ha további műveletet végez. Az adatvesztés elkerülése érdekében növelje a kapacitást, vagy váltson viselkedésre a **régi adatmennyiség törléséhez** .

Az érintett Event Hubsban érdemes lehet módosítani az üzenet- **megőrzési** tulajdonságot, hogy az adatvesztés minimálisra csökkenjen, ha az Time Series Insights.

[![Event hub-üzenetek megőrzése.](media/time-series-insights-contepts-retention/event-hub-retention.png)](media/time-series-insights-contepts-retention/event-hub-retention.png#lightbox)

Ha az eseményforrás (`timeStampPropertyName`) egyik tulajdonság sincs konfigurálva, Time Series Insights alapértelmezett érték az Event hub-ban az X tengelyként való érkezés időbélyege. Ha `timeStampPropertyName` úgy van konfigurálva, hogy valami más legyen, a környezet az események `timeStampPropertyName` elemzésekor az adatcsomagban konfigurált konfigurációt keresi.

Ha a környezetet úgy kell méretezni, hogy a további kapacitást is kielégítse, vagy növelje a megőrzési időt, tekintse meg a [Time Series Insights környezet skálázását](time-series-insights-how-to-scale-your-environment.md) ismertető témakört.  

## <a name="next-steps"></a>További lépések

- Az adatmegőrzési beállítások konfigurálásával és módosításával kapcsolatos információkért tekintse át [az adatmegőrzés konfigurálása Time Series Insightsban](time-series-insights-how-to-configure-retention.md)című témakört.
