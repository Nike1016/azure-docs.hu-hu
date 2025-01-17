---
title: Dokumentum feltöltése – egyéni fordító
titleSuffix: Azure Cognitive Services
description: A dokumentum feltöltése funkcióval párhuzamos dokumentumot tölthet fel a képzések számára. A párhuzamos dokumentumok olyan dokumentumok, amelyekben az egyik a másik fordítása. A pár egyik dokumentuma mondatokat tartalmaz a forrás nyelvén, a másik dokumentum pedig ezeket a mondatokat a célként megadott nyelvre lefordítva tartalmazza.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: f2bd6103c27d455265ee967554fb27513f78a472
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595645"
---
# <a name="upload-a-document"></a>Dokumentum feltöltése

Az [Egyéni fordítóban](https://portal.customtranslator.azure.ai)párhuzamos dokumentumok tölthetők fel a fordítási modellek betanításához. A [párhuzamos dokumentumok](what-are-parallel-documents.md) olyan dokumentumok, amelyekben az egyik a másik fordítása. A pár egyik dokumentuma mondatokat tartalmaz a forrás nyelvén, a másik dokumentum pedig ezeket a mondatokat a célként megadott nyelvre lefordítva tartalmazza.

A dokumentumok feltöltése előtt tekintse át a [dokumentum formátumait és az elnevezési konvenció útmutatását, és](document-formats-naming-convention.md) győződjön meg arról, hogy a fájlformátum támogatott az egyéni fordítóban.

## <a name="how-to-upload-document"></a>Hogyan tölthetők fel a dokumentumok?

A [Custom Translator](https://portal.customtranslator.azure.ai) Portalon kattintson a dokumentumok fülre a dokumentumok lapon.

![Dokumentum feltöltési hivatkozása](media/how-to/how-to-upload-1.png)


1.  Kattintson a fájlok feltöltése gombra a dokumentumok lapon.

    ![Dokumentum feltöltése lap](media/how-to/how-to-upload-2.png)

2.  A párbeszédpanelen adja meg a következő információkat:

    a.  Dokumentum típusa:

    -  Betanítás: A rendszer ezeket a dokumentumokat fogja használni a betanítási készlethez.
    -  Tuning A rendszer ezeket a dokumentumokat fogja használni a hangolási készlethez.
    -  Vizsgálat A rendszer ezeket a dokumentumokat fogja használni a tesztelési készlethez.
    -  Szótár kifejezése: A rendszer ezeket a dokumentumokat fogja használni a szótár kifejezéséhez.
    -  Mondat szótár: A rendszer ezeket a dokumentumokat fogja használni a mondat szótárához

    b.  Nyelvi pár

    c.  Felülbírálási dokumentum, ha létezik: Jelölje be ezt a jelölőnégyzetet, ha felül szeretné írni az azonos nevű meglévő dokumentumokat.

    d.  Adja meg a megfelelő szakaszt párhuzamos vagy kombinált adattípushoz.

    -  Párhuzamos adatértékek:
        -  Forrásfájl: Válassza ki a forrás nyelvi fájl elemet a helyi számítógépről.
        -  Célfájl: Válassza ki a célként megadott nyelvi fájlt a helyi számítógépről.
        -  Dokumentum neve: Csak akkor használható, ha párhuzamos fájlokat tölt fel.

    - Kombinált adatkészletek:
        -  Kombinált fájl: Válassza ki a kombinált fájlt a helyi számítógépről. A kombinált fájl a forrás-és a cél nyelvi mondatokkal is rendelkezik. Az [elnevezési konvenció](document-formats-naming-convention.md) a kombinált fájlok esetében fontos.

    e.  Kattintson a feltöltés

    ![Dokumentum feltöltése párbeszédpanel](media/how-to/how-to-upload-dialog.png)

3.  Ezen a ponton dolgozunk fel dokumentumokat, és megpróbáljuk kinyerni a mondatokat. A "feltöltési folyamat megtekintése" lehetőségre kattintva megtekintheti a dokumentumok állapotát a folyamat során.

    ![Dokumentum-feldolgozás feltöltése párbeszédpanel](media/how-to/how-to-upload-processing-dialog.png)

4.  Ezen a lapon látható az állapot, valamint a feltöltésen belüli egyes fájlok hibái. A korábbi feltöltési állapotot bármikor megtekintheti, ha a "feltöltési előzmények" lapra kattint.

    ![Dokumentum előzményeinek feltöltése párbeszédpanel](media/how-to/how-to-upload-document-history.png)


## <a name="view-upload-history"></a>Feltöltési előzmények megtekintése

A feltöltési Előzmények lapon megtekintheti az összes dokumentum feltöltésének előzményeit, például a dokumentum típusát, a nyelvi párokat, a feltöltési állapotot stb.

1. Az [Egyéni Translator](https://portal.customtranslator.azure.ai) portálon kattintson az Előzmények lap feltöltés elemére az előzmények megtekintéséhez.

    ![Feltöltési Előzmények lap](media/how-to/how-to-upload-history-1.png)

2. Ezen a lapon látható az összes korábbi feltöltés állapota. A legújabbtól a legkorábbiig terjedő feltöltéseket jeleníti meg. Minden feltöltésnél megjelenik a dokumentum neve, a feltöltés állapota, a feltöltés dátuma, a feltöltött fájlok száma, a feltöltött fájl típusa és a fájl nyelvi párja.

    ![Feltöltési Előzmények lap](media/how-to/how-to-document-history-2.png)

3. Kattintson a feltöltési előzmények rekordra. A feltöltési előzmények részletei lapon megtekintheti a fájl feltöltése, feltöltött állapotának részeként feltöltött fájlokat, a fájl nyelvét és a hibaüzenetet (ha bármilyen hiba van a feltöltés során).

## <a name="next-steps"></a>További lépések

- A kinyert mondatok listájának áttekintéséhez használja a [dokumentum részletei lapot](how-to-view-document-details.md) .
- [Modell](how-to-train-model.md)betanítása.
