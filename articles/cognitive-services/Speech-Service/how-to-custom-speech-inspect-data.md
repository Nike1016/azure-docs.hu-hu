---
title: Az Custom Speech-Speech szolgáltatás adatminőségének vizsgálata
titleSuffix: Azure Cognitive Services
description: A Custom Speech olyan eszközöket biztosít, amelyekkel vizuálisan ellenőrizheti a modell felismerési minőségét a hangadatoknak a megfelelő felismerési eredménnyel való összehasonlításával. A Custom Speech-portálon lejátszhatja a feltöltött hangot, és megállapíthatja, hogy a megadott felismerési eredmény helyes-e.  Ez az eszköz lehetővé teszi az alapszintű beszéd – szöveg modell vagy egy betanított egyéni modell minőségének gyors vizsgálatát anélkül, hogy bármilyen hangadatokat kellene átírnia.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: b58f9c17995128091b5c4badd228356dbacc6ae9
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562844"
---
# <a name="inspect-custom-speech-data"></a>Custom Speech-adatellenőrzés

> [!NOTE]
> Ez az oldal azt feltételezi, hogy elolvasta a [Custom Speechi tesztelési adatokat](how-to-custom-speech-test-data.md) , és feltöltött egy adatkészletet a vizsgálathoz.

A Custom Speech olyan eszközöket biztosít, amelyekkel vizuálisan ellenőrizheti a modell felismerési minőségét a hangadatoknak a megfelelő felismerési eredménnyel való összehasonlításával. A Custom Speech-portálon lejátszhatja a feltöltött hangot, és megállapíthatja, hogy a megadott felismerési eredmény helyes-e. Ezzel az eszközzel gyorsan megvizsgálhatja a Microsoft alapvető beszédfelismerési modelljét vagy egy betanított egyéni modellt, anélkül, hogy hangadatokat kellene átírnia.

Ebből a dokumentumból megtudhatja, hogyan vizsgálhatja meg a modell minőségét a korábban feltöltött betanítási adatmennyiség használatával.

Ezen az oldalon megtudhatja, hogyan vizsgálhatja meg a Microsoft alapvető beszédfelismerési modelljét és/vagy a már betanított egyéni modellt. A teszteléshez az **adatlapra feltöltött** adatlapokat fogja használni.

## <a name="create-a-test"></a>Teszt létrehozása

Teszt létrehozásához kövesse az alábbi utasításokat:

1. Navigáljon a **beszéd-szöveg > Custom Speech > teszteléshez**.
2. Kattintson a **teszt hozzáadása**gombra.
3. Válassza a **minőség vizsgálata (csak hangalapú adatok)** lehetőséget. Adja meg a teszt nevét, leírását, és válassza ki a hangkészletet.
4. Válasszon legfeljebb két modellt, amelyeket szeretne tesztelni.
5. Kattintson a **Create** (Létrehozás) gombra.

A tesztek sikeres létrehozása után összehasonlíthatja a modelleket egymás mellett.

## <a name="side-by-side-model-comparisons"></a>Párhuzamos modell-összehasonlítások

Ha a teszt állapota *sikeres*, kattintson a teszt elem nevére a teszt részleteinek megtekintéséhez. Ez a részletes lap felsorolja az adatkészlet összes hosszúságú kimondott szöveg, amely a két modell felismerési eredményét jelzi a beküldött adatkészlet átírása mellett.

Az egymás melletti összehasonlítások megvizsgálása érdekében különböző típusú hibákat válthat ki, beleértve a beszúrást, a törlést és a helyettesítést is. Ha az egyes oszlopokban figyeli a hangot, és összehasonlítja a felismerés eredményét (megjelenítve az emberi címkével ellátott átírást és a két beszéd – szöveg modell eredményét), eldöntheti, hogy melyik modell megfelel az igényeinek, és hogy hol szükségesek a tökéletesítések.

A minőségi tesztelés vizsgálata hasznos annak ellenőrzéséhez, hogy egy beszédfelismerési végpont minősége elég-e az alkalmazáshoz.  Az átmásolt hangra vonatkozó objektív pontosság érdekében kövesse a [pontosság](how-to-custom-speech-evaluate-data.md)kiértékelése című részben található utasításokat.

## <a name="next-steps"></a>További lépések

* [Az adatai kiértékelése](how-to-custom-speech-evaluate-data.md)
* [A modell betanítása](how-to-custom-speech-train-model.md)
* [A modell üzembe helyezése](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>További források

* [Custom Speech tesztelési célú adatfeldolgozása](how-to-custom-speech-test-data.md)
