---
title: 'Visual Interface példa #6: Besorolás a repülési késések előrejelzésére'
titleSuffix: Azure Machine Learning service
description: Ebből a cikkből megtudhatja, hogyan hozhat létre egy gépi tanulási modellt a repülési késések előrejelzésére a fogd és vidd vizualizáció felület és az egyéni R-kód használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: peterlu
ms.date: 07/02/2019
ms.openlocfilehash: 4c0a990ae3f45fc7b08c157f180d8ecf805c24e6
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990025"
---
# <a name="sample-6---classification-predict-flight-delays-using-r"></a>6\. példa – besorolás: Repülési késések előrejelzése R használatával

Ez a kísérlet a korábbi repülési és időjárási adatszolgáltatásokat használja annak előrejelzésére, hogy egy ütemezett utasszállító járat 15 percnél hosszabb ideig késleltetve lesz-e.

Ez a probléma besorolási problémaként is megközelíthető, két osztály előrejelzése – késleltetett vagy időben. Osztályozó létrehozásához a modell nagy számú példát használ a történelmi repülési adatokból.

Itt látható a végső kísérleti gráf:

[![A kísérlet gráfja](media/ui-sample-classification-predict-flight-delay/experiment-graph.png)](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Válassza a 6. minta kísérlet **Megnyitás** gombját:

    ![A kísérlet megnyitása](media/ui-sample-classification-predict-flight-delay/open-sample6.png)

## <a name="get-the-data"></a>Az adatok lekérése

Ez a kísérlet a **repülési késések** adatkészletét használja. Az Egyesült államokbeli TranStats-adatgyűjtés részét képezi. Közlekedési Minisztérium. Az adatkészlet az áprilistól október 2013-ig terjedő repülési késleltetési adatokat tartalmaz. Mielőtt feltölti az adattárat a vizualizációs felületre, az a következőképpen lett feldolgozva:

* Szűrve, hogy tartalmazza a 70 legforgalmasabb repülőteret a kontinentális Egyesült Államok.
* A megszakított járatok esetében több mint 15 perc késleltetéssel lett ellátva.
* Kiszűrt fordított járatok.
* Kiválasztott 14 oszlop.

A repülési adatokat a rendszer az **időjárási adatkészlet** használatával egészíti ki. Az időjárási adatok a NOAA-től származó, óránkénti, szárazföldön alapuló időjárási megfigyeléseket tartalmaznak, és a repülőtéri meteorológiai állomások észrevételeit jelölik, amelyek a 2013. április 1-jétől érvényes időszakra vonatkoznak. Az Azure ML vizualizációs kezelőfelületre való feltöltés előtt a következő módon lett feldolgozva:

* A meteorológiai állomás azonosítói a megfelelő repülőtéri azonosítóra vannak leképezve.
* Az 70-es legforgalmasabb repülőtérhez nem társított meteorológiai állomások el lettek távolítva.
* A Date oszlop különálló oszlopokra van bontva: Év, hónap és nap.
* Kiválasztott 26 oszlop.

## <a name="pre-process-the-data"></a>Az adatfeldolgozás előkezelése

Az adatkészletek általában valamilyen előzetes feldolgozást igényelnek, mielőtt elemezni lehetne őket.

![adatfeldolgozás](media/ui-sample-classification-predict-flight-delay/data-process.png)

### <a name="flight-data"></a>Repülési adatcsatorna

A **Carrier**, a **OriginAirportID**és a **DestAirportID** oszlopok egész számként lesznek mentve. Azonban kategorikus attribútumok, a **metaadatok szerkesztése** modullal alakítsa át azokat a kategorikusba.

![metaadatok szerkesztése](media/ui-sample-classification-predict-flight-delay/edit-metadata.png)

Ezután az adatkészlet **kijelölése oszlopban válassza** ki az adatkészletek oszlopait, amelyekkel lehetséges a szivárgások célja: **DepDelay**, **DepDel15**, **ArrDelay**,megszakítva, **év**. 

Ha a repülési rekordokat az óránkénti időjárási adatokkal szeretné csatlakoztatni, használja az ütemezett indulási időt az illesztési kulcsok egyikének megfelelően. Az illesztés végrehajtásához a CSRDepTime oszlopot a legközelebbi órára kell kerekíteni, amelyet az **R-parancsfájl végrehajtása modul hajt** végre. 

### <a name="weather-data"></a>Időjárási adatszolgáltatások

A hiányzó értékek nagy hányadát tartalmazó oszlopok ki vannak zárva a **Project Columns** modul használatával. Ezek az oszlopok az összes karakterlánc értékű oszlopot tartalmazzák: **ValueForWindCharacter**, **WetBulbFarenheit**, **WetBulbCelsius**, **PressureTendency**, **PressureChange**, **SeaLevelPressure**és **StationPressure**.

Ekkor a rendszer a hiányzó oszlopokra alkalmazza a **tiszta hiányzó** adatmodult, hogy eltávolítsa a hiányzó adatsorokat.

Az időjárási megfigyelési idők a legközelebbi teljes órára vannak kerekítve. Az ütemezett repülési időpontok és az időjárási megfigyelések időpontját az ellenkező irányba kerekítjük, így biztosítva, hogy a modell csak a repülési idő előtti időjárási időt használja. 

Mivel az időjárási adatok helyi időben kerülnek jelentésre, az időzóna-különbségek az ütemezett indulási idő és az időjárási megfigyelési idő kivonásával vannak elszámolva. Ezek a műveletek az **R szkript végrehajtása** modul használatával hajthatók végre.

### <a name="joining-datasets"></a>Adatkészletek csatlakoztatása

A repülési rekordok a beléptetési **adatok** modul használatával a Flight (**OriginAirportID**) által használt időjárási adatokkal vannak csatlakoztatva.

 ![Csatlakozás a repüléshez és az időjáráshoz forrás szerint](media/ui-sample-classification-predict-flight-delay/join-origin.png)


A repülési rekordok időjárási adatokkal vannak összekapcsolva a repülési cél (**DestAirportID**) használatával.

 ![Csatlakozás a repüléshez és az időjáráshoz célhoz](media/ui-sample-classification-predict-flight-delay/join-destination.png)

### <a name="preparing-training-and-test-samples"></a>Képzések és tesztelési minták előkészítése

Az **adatok felosztása** modul a betanítási és a tesztelési feladatokra vonatkozó októberi rekordokra osztja fel az adatokat.

 ![A képzési és tesztelési célú megosztások](media/ui-sample-classification-predict-flight-delay/split.png)

Az év, hónap és timezone oszlop törlődik a betanítási adatkészletből az Oszlopok kiválasztása modul használatával.

## <a name="define-features"></a>Funkciók definiálása

A gépi tanulásban a funkciók a érdeklik egyes, mérhető tulajdonságai. Erős funkciók megkeresése kísérletezést és tartományi ismereteket igényel. Bizonyos jellemzők ugyanis hasznosabbak a cél előrejelzéséhez, mint mások. Emellett előfordulhat, hogy egyes funkciók erős korrelációt mutatnak más funkciókkal, és nem vesznek fel új adatokat a modellbe. Ezek a funkciók eltávolíthatók.

Modell létrehozásához használhatja az összes elérhető funkciót, vagy kiválaszthatja a szolgáltatások egy részhalmazát.

## <a name="choose-and-apply-a-learning-algorithm"></a>Tanulási algoritmus kiválasztása és alkalmazása

Hozzon létre egy modellt a kétosztályos logisztikai regressziós modullal, és tanítsa be azt a betanítási adatkészleten. 

A betanítási **modell** modul eredménye egy betanított besorolási modell, amely az előrejelzések készítéséhez használható új minták kiértékelésére. A test (teszt) beállítással pontokat hozhat létre a betanított modellből. Ezután használja a **modell** kiértékelése modult a modellek minőségének elemzéséhez és összehasonlításához.

A kísérlet futtatása után megtekintheti a **pontszám modell** modul kimenetét. ehhez kattintson a kimeneti portra, és válassza a **Megjelenítés**lehetőséget. A kimenet tartalmazza a pontszámmal ellátható címkéket és a címkék valószínűségét.

Végül az eredmények minőségének teszteléséhez adja hozzá a **modell** kiértékelése modult a kísérlet vászonhoz, és a bal oldali bemeneti portot a pontszám modell modul kimenetéhez kapcsolja. Futtassa a kísérletet, és tekintse meg a **modell** kiértékelése modul kimenetét a kimeneti portra kattintva, és válassza a **Megjelenítés**lehetőséget.

## <a name="evaluate"></a>Értékelés
A logisztikai regressziós modell 0,631 AUC rendelkezik a tesztelési készleten.

 ![értékelés](media/ui-sample-classification-predict-flight-delay/evaluate.png)

## <a name="next-steps"></a>További lépések

Ismerje meg a vizuális felületen elérhető egyéb mintákat:

- [1. példa – regresszió: Autó árának előrejelzése](ui-sample-regression-predict-automobile-price-basic.md)
- [2. minta – regresszió: Algoritmusok összehasonlítása az autó árának előrejelzéséhez](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [3. példa – besorolás: Hitelkockázat előrejelzése](ui-sample-classification-predict-credit-risk-basic.md)
- [4. minta – besorolás: Hitelkockázat (Cost szenzitív)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [5. példa – besorolás: Forgalom előrejelzése](ui-sample-classification-predict-churn.md)
