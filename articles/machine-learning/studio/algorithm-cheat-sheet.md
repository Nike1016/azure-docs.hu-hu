---
title: Gépi tanulási algoritmus-Adatlap
titleSuffix: Azure Machine Learning Studio
description: Nyomtatható machine learning algoritmus-Adatlap segít az Azure Machine Learning Studióban válassza ki a megfelelő algoritmus a prediktív modell.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-ms.author=pakalra, previous-author=pakalra
ms.date: 03/04/2019
ms.openlocfilehash: 51a743e7578ea5bbc2acb9094bbf704a09f3cd6a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60752085"
---
# <a name="machine-learning-algorithm-cheat-sheet-for-azure-machine-learning-studio"></a>Gépi tanulási algoritmus-Adatlap az Azure Machine Learning Studióban

A **Azure Machine Learning Studio algoritmus Adatlap lap** segítségével válassza ki a megfelelő algoritmus a prediktív elemzési modellek.

[Az Azure Machine Learning Studio](https://studio.azureml.net/) rendelkezik egy nagy származó algoritmusok könyvtára a ***regressziós***, ***besorolási***, ***Fürtszolgáltatás***, és  ***anomáliadetektálás*** családhoz. Minden egyes célja egy más típusú machine learning a probléma megoldása érdekében.

## <a name="download-machine-learning-algorithm-cheat-sheet"></a>Letöltés: Gépi tanulási algoritmus-Adatlap

**A Adatlap letöltése: [A Machine Learning algoritmus-Adatlap (11 x 17)](https://download.microsoft.com/download/A/6/1/A613E11E-8F9C-424A-B99D-65344785C288/microsoft-machine-learning-algorithm-cheat-sheet-v7.pdf)**

![A Machine learning algoritmus-Adatlap: Ismerje meg, egy gépi tanulási algoritmus kiválasztása.](./media/algorithm-cheat-sheet/machine-learning-algorithm-cheat-sheet-small_v_0_6-01.png)

Töltse le, és a Machine Learning Studio algoritmus Adatlap lap nyomtatása tabloid méretben kéznél legyen, és kérjen segítséget az algoritmus kiválasztása.

> [!NOTE]
> Használatával a Adatlap döntöttek a megfelelő algoritmus, valamint a különböző típusú gépi tanulási algoritmusok és a használatuk mélyebb hatásának a megbeszélését lásd [algoritmusok kiválasztása a Microsoft Azure Machine Learning Studióban](algorithm-choice.md).

## <a name="notes-and-terminology-definitions-for-the-machine-learning-studio-algorithm-cheat-sheet"></a>Megjegyzések és a Machine Learning Studio algoritmus terminológiai definíciók – Adatlap

* A javaslat érhető el az algoritmus-Adatlap hozzávetőleges szabályok-az-bütykének. Néhány hajlított, és néhány is lehet flagrantly megsértve. Ennek célja, és kiindulási pontot javasolja. Ne féljen head-to-head versenyt között számos algoritmust az adatokon való futtatásához. Nincs egyszerűen nem helyettesíti az egyes algoritmus és a rendszer az adatokat létrehozó ismertetése.

* Minden gépi tanulási algoritmus rendelkezik a saját style vagy *induktív eltérés*. Egy meghatározott problémára vonatkozó számos algoritmust célszerű lehet, és a egy algoritmus lehet, mint a többi jobban megfelel. De nem mindig lehetséges tudja előre, azaz az ideális megoldást. Ilyen esetekben számos algoritmust szerepelnek együtt a Adatlap. Próbáljon meg egy algoritmust, és ha az eredmények egyelőre nem kielégítő, próbálja ki, a többi lehet a megfelelő stratégiát. Íme egy példa a a [Azure AI-katalógusban](https://gallery.azure.ai/) kísérletet, amelyek az ugyanazon adatokra irányuló számos algoritmust megpróbál, és összehasonlítja az eredmények: [Hasonlítsa össze a többcsoportos besorolások: Elismerési betűs](https://gallery.azure.ai/Details/a635502fc98b402a890efe21cec65b92).

* A gépi tanulás három fő kategóriába sorolhatók: **felügyelt tanítással**, **tanítást**, és **megerősítő tanulást**.

  * A **felügyelt tanítással**, az egyes van címkézett, illetve a társított egy kategóriát vagy az egyik fontos értékére.  Egy példa egy kategorikus címke van hozzárendelése "cat" vagy "kutya" kép.  Egy érték felirat egyik példája az olyan kapcsolattal társított eladási ár. A felügyelt tanítás célja, hogy ezeket, mint számos címkézett példák tanulmánya majd tudnak kapcsolatos jövőbeli adatpontok előrejelzéseket. Például új fényképet azonosítja a megfelelő szolgáltatást, vagy más használt autók pontos értékesítési árak hozzárendelése. Ez a machine learning egy népszerű és hasznos típusú. Az Azure Machine Learning Studióban összes felügyelt tanulási algoritmus kivételével [K-közép-Fürtszolgáltatás][k-means-clustering].

  * A **tanítást**, adatpontok nem tartalmaz feliratokat társítva. Ehelyett egy felügyeletlen tanulási algoritmus célja valamilyen módon adatok rendezése, vagy a szerkezetét írja le. Ez azt csoportosítás, csoportosít, a K-közép hasonlóan, vagy különböző módszert is megnézzük az összetett, így egyszerűbb jelenik kereséséhez.

  * A **megerősítő tanulást**, az algoritmus beolvasása művelet kiválasztása az egyes adott válaszként. Általánosan használt megközelítés a robotika, ahol egy adatpont érzékelőinek adott ponton időben lesz, és az algoritmus ki kell választania a robot a következő művelet. Még egy természetes választás olyan IOT-alkalmazásokat. A tanulási algoritmus is fogad ellenszolgáltatás jel egy rövid ideig később, hogyan jó döntés lett megjelölve. Ennek alapján, az algoritmus módosítása igazítani a stratégiáját a legmagasabb ellenszolgáltatás növelhető. Jelenleg nincsenek nincs megerősítő tanulási algoritmus modulok az Azure Machine Learning studióban.

* **Bayes módszerek** statisztikailag független adatpontok feltételezik. Ez azt jelenti, hogy az egyik adatpont unmodeled hozzon predikátumban másokkal, vagyis nem becsülhető. Például ha az adatok rögzíti a hány perc múlva a következő subway vonat érkezik, két mérések napi egymástól függetlenek statisztikailag. Azonban két mérések egy perc közötti nem függetlenek statisztikailag – a egy értéke magas prediktív, a másik értékét.

* **Súlyozott regressziós döntési fa** kihasználja a szolgáltatás között átfedés van, vagy a szolgáltatások közötti interakció. Azt jelenti, hogy, hogy az adott adatpontok, egy szolgáltatás értéke némileg prediktív egy másik érték. Például a napi magas és alacsony hőmérsékletadatokat, a legalacsonyabb hőmérséklet, hogy a nap lehetővé teszi, hogy a nagy egy ésszerű találgatásos. A két funkció található információkat, valamennyire redundáns.

* Adatok besorolása több mint két kategóriába sorolhatók teheti meg egy eredendően több osztály osztályozó használatával vagy csoportjának egyesítésével képzett két osztályú osztályozás be egy **ensemble**. A ensemble módszert használja, nincs külön két osztályú besorolás az egyes osztályok – az adatok mindegyik elválasztja a két kategóriába sorolhatók: "Ez az osztály" és "nem ez az osztály." Ezek a besorolások majd helyes hozzárendelését az adatpont is szavazhat. Ez az a működési elv mögött [egy-multi-osztályú osztályozási műveletet][one-vs-all-multiclass].

* Tegyük fel, több módszer, beleértve a logisztikai regressziós és a Bayes pontozó gépet **lineáris osztály határok**. Ez azt jelenti, hogy azt feltételezik, hogy az osztályok között határok körülbelül egyenes vonalak (vagy az általános esetben hyperplanes). Gyakran ez az adatok, amíg nem tudja után való elhatárolásra próbálta jellemzők, de valami, amely általában is lehet megtanult előre vizualizációjával. Az osztály határok nagyon szabálytalan tekinti meg, ha jól döntési fák, decision jungle algoritmust, támogatja a vektoros gépek vagy a Neurális hálózatokat.

* Neurális hálózatokkal használható kategorikus változók létrehozásával egy **helyőrző a változó** minden egyes kategóriánál, 1 értékre állítaná az esetekben, amikor a kategóriát, ahol nem 0.

## <a name="next-steps"></a>További lépések

* Letölthető infografika, amely ismerteti az algoritmusokkal, és példákat talál, lásd: [letölthető Infografika: Machine learning alapjai algoritmuspéldákkal](basics-infographic-with-algorithm-examples.md).

* A Machine Learning Studióban elérhető összes gépi tanulási algoritmusok, kategória szerint, lásd: [modell inicializálása] [ initialize-model] a Machine Learning Studio algoritmus-és modul segítségével.

* Betűrend szerinti rendezés listájáért algoritmusok és a Machine Learning Studióban modulok, lásd: [betűrend szerinti lista Machine Learning Studio-modulok] [ a-z-list] a Machine Learning Studio algoritmus- és modul segítségével.



<!-- Module References -->
[a-z-list]: /azure/machine-learning/studio-module-reference/a-z-module-list
[initialize-model]: /azure/machine-learning/studio-module-reference/machine-learning-initialize-model
[k-means-clustering]: /azure/machine-learning/studio-module-reference/k-means-clustering
[one-vs-all-multiclass]: /azure/machine-learning/studio-module-reference/one-vs-all-multiclass
