---
title: Kibővíthető Azure Analysis Services | Microsoft Docs
description: Azure Analysis Services-kiszolgálók replikálása felskálázással
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 29188013b75dbefbaf80f3c59360f203ae5b5a82
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2019
ms.locfileid: "68736755"
---
# <a name="azure-analysis-services-scale-out"></a>Az Azure Analysis Services horizontális felskálázása

A kibővített szolgáltatással az ügyfelek lekérdezései több lekérdezési *replika* között terjeszthetők a *lekérdezési készletekben*, így csökkentve a válaszadási időt a nagy lekérdezési számítási feladatok során. Elkülönítheti a feldolgozást a lekérdezési készletből is, így az ügyfelek lekérdezéseit nem érinti hátrányosan a feldolgozási műveletek. A felskálázás konfigurálható Azure Portal vagy a Analysis Services REST API használatával.

A standard szintű díjszabású kiszolgálók esetében a kibővített szolgáltatás elérhető. A lekérdezési replikák számlázása a kiszolgálóval megegyező sebességgel történik. Minden lekérdezési replika a-kiszolgálóval megegyező régióban jön létre. A beállítható lekérdezési replikák számát a kiszolgáló régiója korlátozza. További információ: [a rendelkezésre állás régiónként](analysis-services-overview.md#availability-by-region). A vertikális felskálázás nem növelheti a kiszolgáló rendelkezésre álló memóriájának mennyiségét. A memória növeléséhez frissítenie kell a csomagot. 

## <a name="why-scale-out"></a>Miért érdemes kibővíteni?

Egy tipikus kiszolgálói környezetben az egyik kiszolgáló a feldolgozó kiszolgálóként és a lekérdezési kiszolgálóként is szolgál. Ha a kiszolgálón lévő modellektől érkező ügyfél-lekérdezések száma meghaladja a kiszolgáló csomagjának lekérdezés-feldolgozási egységeit (QPU), vagy a modell feldolgozása a nagy lekérdezési számítási feladatokkal megegyező időben történik, a teljesítmény csökkenhet. 

A kibővített szolgáltatással legfeljebb hét további lekérdezési replika erőforrással hozhat létre egy lekérdezési készletet (ez nyolc összeg, beleértve az *elsődleges* kiszolgálót is). A lekérdezési készletben lévő replikák számát a kritikus időpontokban a QPU igényeinek megfelelően méretezheti, és bármikor elkülönítheti a feldolgozó kiszolgálót a lekérdezési készletből. 

A lekérdezési készletekben lévő lekérdezések replikáinak számától függetlenül a feldolgozási feladatok nem oszlanak meg a lekérdezési replikák között. Az elsődleges kiszolgáló a feldolgozó kiszolgálóként szolgál. A lekérdezési replikák csak az elsődleges kiszolgáló és a lekérdezési készlet minden másodpéldánya között szinkronizált modell-adatbázisok lekérdezéseit szolgálják. 

Horizontális felskálázás esetén akár öt percet is igénybe vehet, amíg az új lekérdezési replikák Növekményesen fel lesznek véve a lekérdezési készletbe. Ha az összes új lekérdezési replikát felhasználják, az új ügyfélkapcsolatok terheléselosztása a lekérdezési készlet erőforrásai között történik. A meglévő ügyfélkapcsolatok nem változnak attól az erőforrástól, amelyhez jelenleg csatlakoznak. A (z) skálázásakor a lekérdezési készlet erőforrásaiból eltávolított meglévő ügyfélkapcsolatok le lesznek szakítva. Az ügyfelek újra csatlakozhatnak egy hátralévő lekérdezési készlet erőforráshoz.

## <a name="how-it-works"></a>Működés

A skálázás első beállításakor a *rendszer automatikusan* szinkronizálja az elsődleges kiszolgálón a modell adatbázisait új replikákkal egy új lekérdezési készletben. Az automatikus szinkronizálás csak egyszer fordul elő. Az automatikus szinkronizálás során az elsődleges kiszolgáló adatfájljait (a blob Storage-ban tárolt adatok titkosítása) egy második helyre másolják, amely a blob Storage-ban is titkosítva van. A lekérdezési készletben lévő replikák ezután hidratálva lesznek a második készletből származó adatokkal. 

Míg az automatikus szinkronizálás csak akkor történik meg, ha első alkalommal kibővít egy kiszolgálót, manuálisan is elvégezheti a szinkronizálást. A szinkronizálási funkció biztosítja, hogy a lekérdezési készletben lévő replikák megegyezzenek az elsődleges kiszolgálóval. Az elsődleges kiszolgálón végzett feldolgozási (frissítési) modellek esetében a feldolgozási műveletek befejeződése *után* szinkronizálást kell végrehajtani. Ez a szinkronizálás a blob Storage-ban lévő elsődleges kiszolgáló fájljainak frissített adatait másolja a második fájlba. A lekérdezési készletben lévő replikákat a rendszer a blob Storage-ban lévő fájlok második készletének frissített adatainak megfelelően hidratálja. 

Ha egy későbbi kibővített műveletet hajt végre, például a két és öt közötti replikák számának növelését, az új replikák a blob Storage-ban lévő fájlok második készletében lévő adatokkal vannak hidratálva. Nincs szinkronizálás. Ha a horizontális felskálázás után elvégezte a szinkronizálást, a lekérdezési készletben lévő új replikák kétszer is hidratálva lesznek – redundáns folyadékpótlás. Egy későbbi kibővített művelet végrehajtásakor fontos szem előtt tartani a következőket:

* Végezzen szinkronizálást *a kibővítő művelet előtt* a hozzáadott replikák redundáns hidratációjának elkerüléséhez. Az egyidejű szinkronizálási és kibővíthető műveletek nem engedélyezettek.

* A feldolgozási *és a* kibővíthető műveletek automatizálásakor fontos, hogy először dolgozza fel az elsődleges kiszolgálón lévő adatok feldolgozását, majd végezzen szinkronizálást, majd végezze el a kibővített műveletet. Ez a folyamat minimális hatást biztosít a QPU és a memória erőforrásaira.

* A szinkronizálás akkor is engedélyezett, ha nincsenek replikák a lekérdezési készletben. Ha az elsődleges kiszolgálón lévő feldolgozási műveletből egy vagy több replikára horizontális felskálázást végez, először hajtsa végre a szinkronizálást a lekérdezési készletben lévő replikák nélkül, majd a horizontális felskálázást. A horizontális felskálázás előtt a szinkronizálás elkerüli az újonnan hozzáadott replikák redundáns hidratációját.

* Ha az elsődleges kiszolgálóról törli a modell-adatbázist, a rendszer nem törli automatikusan a lekérdezési készlet replikái közül. A szinkronizálási műveletet a [Sync-AzAnalysisServicesInstance PowerShell-](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance) paranccsal kell végrehajtania, amely eltávolítja az adott adatbázishoz tartozó fájlt/t a replika megosztott blob Storage-helyéről, majd törli a modell-adatbázist a replikák a lekérdezési készletben. Annak megállapításához, hogy létezik-e a modell adatbázisa a lekérdezési készlet replikái között, de nem az elsődleges kiszolgálón, ügyeljen arra, hogy a **feldolgozó kiszolgáló elkülönítése** a lekérdezési készletből beállítás értéke **Igen**. Ezután a SSMS használatával kapcsolódjon az elsődleges kiszolgálóhoz a `:rw` minősítővel, és ellenőrizze, hogy létezik-e az adatbázis. Ezután kapcsolódjon a lekérdezési készlet replikái számára úgy, hogy `:rw` a minősítő nélkül csatlakozik az adatbázishoz. Ha az adatbázis létezik a lekérdezési készlet replikái között, de az elsődleges kiszolgálón nem, futtassa a szinkronizálási műveletet.   

* Ha az elsődleges kiszolgálón átnevez egy adatbázist, további lépésekre van szükség annak biztosításához, hogy az adatbázis megfelelően legyen szinkronizálva a replikákkal. Az Átnevezés után hajtson végre szinkronizálást a [Sync-AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance) paranccsal, és adja meg `-Database` a paramétert a régi adatbázis nevével. Ez a szinkronizálás eltávolítja az adatbázist és a fájlokat a régi névvel bármely replikáról. Ezután hajtson végre egy másik szinkronizálást a `-Database` paraméter megadásával az új adatbázis nevével. A második szinkronizálás az újonnan elnevezett adatbázist átmásolja a fájlok második készletére, és hidratálja a replikákat. Ezek a szinkronizálások nem hajthatók végre a portálon a modell szinkronizálása parancs használatával.

### <a name="separate-processing-from-query-pool"></a>Külön feldolgozás a lekérdezési készletből

A feldolgozási és a lekérdezési műveletek maximális teljesítményéhez választhatja a feldolgozó kiszolgáló elkülönítését a lekérdezési készletből is. Ha elválasztja, az új ügyfélkapcsolatok csak a lekérdezési készletben lévő lekérdezési replikák esetében lesznek hozzárendelve. Ha a feldolgozási műveletek csak rövid időt vesznek igénybe, dönthet úgy, hogy a feldolgozó kiszolgálót csak a feldolgozási és szinkronizálási műveletek elvégzéséhez szükséges ideig választja el, majd visszaadja a lekérdezési készletbe. Ha a feldolgozási kiszolgálót a lekérdezési készletből választja, vagy visszaadja a lekérdezési készletbe, a művelet befejezéséhez akár öt percet is igénybe vehet.

## <a name="monitor-qpu-usage"></a>QPU-használat figyelése

Annak megállapításához, hogy szükség van-e a kiszolgáló méretezésére, figyelje a kiszolgálót Azure Portal a metrikák használatával. Ha a QPU rendszeresen lefoglalja, az azt jelenti, hogy a modellekhez tartozó lekérdezések száma meghaladja a csomag QPU-korlátját. A lekérdezési készlet feladatok várólistájának hossza metrika akkor is megnő, ha a lekérdezési szál várólistáján lévő lekérdezések száma meghaladja az elérhető QPU. 

Egy másik jó mérőszámot kell figyelni a ServerResourceType átlagos QPU. Ez a metrika az elsődleges kiszolgáló átlagos QPU hasonlítja össze a lekérdezési készlettel. 

![Kibővíthető mérőszámok lekérdezése](media/analysis-services-scale-out/aas-scale-out-monitor.png)

### <a name="to-configure-qpu-by-serverresourcetype"></a>A QPU konfigurálása a ServerResourceType szerint
1. A mérőszámok sorában kattintson a **metrika hozzáadása**lehetőségre. 
2. Az **erőforrás**területen válassza ki a kiszolgálót, majda metrikai névtérben válassza ki **Analysis Services standard mérőszámok**elemet, majd a **metrika**területen válassza a **QPU**lehetőséget, majd az **Összesítés**területen válassza az **AVG**elemet. 
3. Kattintson a **felosztás alkalmazása**lehetőségre. 
4. Az **értékek**területen válassza a **ServerResourceType**lehetőséget.  

További tudnivalókért lásd: [A kiszolgáló metrikáinak monitorozása](analysis-services-monitor.md).

## <a name="configure-scale-out"></a>Horizontális felskálázás konfigurálása

### <a name="in-azure-portal"></a>Azure Portal

1. A portálon kattintson akibővítés elemre. A csúszka segítségével válassza ki a lekérdezési replika kiszolgálók számát. A kiválasztott replikák száma a meglévő kiszolgálón kívül történik.  

2. A **feldolgozó kiszolgáló leválasztása a lekérdezési készletből**beállításnál válassza az Igen lehetőséget, ha ki szeretné zárni a feldolgozó kiszolgálót a lekérdezési kiszolgálókról. Az [](#connections) alapértelmezett kapcsolati karakterláncot (nem `:rw`) használó ügyfélkapcsolatokat a rendszer átirányítja a lekérdezési készlet replikái között. 

   ![Felskálázási csúszka](media/analysis-services-scale-out/aas-scale-out-slider.png)

3. A **Save (Mentés** ) gombra kattintva kiépítheti az új lekérdezés-replika kiszolgálókat. 

Amikor első alkalommal konfigurálja a kibővített kiszolgálót, a rendszer automatikusan szinkronizálja az elsődleges kiszolgáló modelljeit a lekérdezési készletben lévő replikákkal. Az automatikus szinkronizálás csak egyszer történik meg, amikor először konfigurálja a méretezést egy vagy több replikára. Az ugyanazon a kiszolgálón lévő replikák számának későbbi módosításai *nem indítanak el másik automatikus szinkronizálást*. Az automatikus szinkronizálás még akkor sem történik meg, ha a kiszolgáló nulla replikára van állítva, majd ismét kibővíthető tetszőleges számú replikára. 

## <a name="synchronize"></a>Szinkronizálni 

A szinkronizálási műveleteket manuálisan vagy a REST API használatával kell elvégezni.

### <a name="in-azure-portal"></a>Azure Portal

Az **áttekintés** > modell > a **modell szinkronizálása**című témakört.

![Felskálázási csúszka](media/analysis-services-scale-out/aas-scale-out-sync.png)

### <a name="rest-api"></a>REST API

Használja a **szinkronizálási** műveletet.

#### <a name="synchronize-a-model"></a>Modell szinkronizálása   

`POST https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

#### <a name="get-sync-status"></a>Szinkronizálási állapot beolvasása  

`GET https://<region>.asazure.windows.net/servers/<servername>/models/<modelname>/sync`

Visszatérési állapotkódok:


|Kód  |Leírás  |
|---------|---------|
|-1     |  Érvénytelen       |
|0     | Replikáció folyamatban        |
|1     |  Rehidratálása       |
|2     |   Befejeződött       |
|3     |   Meghiúsult      |
|4     |    Véglegesítés     |
|||


### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A PowerShell használata előtt [telepítse vagy frissítse a legújabb Azure PowerShell modult](/powershell/azure/install-az-ps). 

A szinkronizálás futtatásához használja a [Sync-AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance).

A lekérdezési replikák számának beállításához használja a [set-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver). Válassza a választható `-ReadonlyReplicaCount` paramétert.

A feldolgozó kiszolgáló a lekérdezési készletből való elkülönítéséhez használja a [set-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver). Válassza ki a `-DefaultConnectionMode` használni `Readonly`kívánt választható paramétert.

További információ: [egyszerű szolgáltatásnév használata az az. AnalysisServices modullal](analysis-services-service-principal.md#azmodule).

## <a name="connections"></a>Kapcsolatok

A kiszolgáló áttekintő oldalán két kiszolgálónév található. Ha még nem konfigurálta a kibővített kiszolgálót a kiszolgálókon, mindkét kiszolgálónév ugyanúgy működik. A kibővített kiszolgáló beállítása után a kapcsolódási típustól függően meg kell adnia a megfelelő kiszolgálónevet. 

A végfelhasználói ügyfélkapcsolatok, például az Power BI Desktop, az Excel és az egyéni alkalmazások esetében használja a **kiszolgálónév nevet**. 

A PowerShell, az Azure Function apps és az AMO SSMS, SSDT és kapcsolatok karakterláncai esetében használja a **felügyeleti kiszolgáló nevét**. A felügyeleti kiszolgáló neve egy speciális `:rw` (írható-olvasható) minősítőt tartalmaz. Az összes feldolgozási művelet az (elsődleges) felügyeleti kiszolgálón történik.

![Kiszolgálók nevei](media/analysis-services-scale-out/aas-scale-out-name.png)

## <a name="scale-up-scale-down-vs-scale-out"></a>Vertikális felskálázás, vertikális leskálázás és Kiterjesztés

Az árképzési szintet egy több replikával rendelkező kiszolgálón is módosíthatja. Ugyanez az árképzési csomag az összes replikára vonatkozik. A skálázási művelet először az összes replikát leállítja, majd az új díjszabási szinten hozza létre az összes replikát.

## <a name="troubleshoot"></a>Hibaelhárítás

**Probléma:** A felhasználók hibaüzenete **nem találja\<a (z) > kiszolgáló nevét a (z) "ReadOnly" csatlakoztatási módban.**

**Megoldás** Amikor kiválasztja a **feldolgozó kiszolgáló elkülönítése a lekérdezési készletből** lehetőséget, a rendszer az alapértelmezett kapcsolati karakterláncot ( `:rw`nem) használó ügyfélkapcsolatokat a lekérdezési készlet replikáinak átirányítja. Ha a lekérdezési készlet replikái még nincsenek online állapotban, mert a szinkronizálás még nem fejeződött be, az átirányított ügyfélkapcsolatok sikertelenek lehetnek. A sikertelen kapcsolatok megelőzése érdekében a lekérdezési készletben legalább két kiszolgálónak kell lennie a szinkronizálás végrehajtásához. Minden kiszolgáló külön van szinkronizálva, míg mások online állapotban maradnak. Ha úgy dönt, hogy nem rendelkezik a feldolgozási kiszolgálóval a lekérdezési készletben a feldolgozás során, dönthet úgy, hogy a készletből eltávolítja a feldolgozást, majd a feldolgozás befejezése után visszaadja azt a készletbe, de a szinkronizálás előtt. A szinkronizálási állapot figyeléséhez használja a memória és a QPU metrikáját.



## <a name="related-information"></a>Kapcsolódó információk

[Kiszolgáló metrikáinak figyelése](analysis-services-monitor.md)   
[Azure Analysis Services kezelése](analysis-services-manage.md) 
