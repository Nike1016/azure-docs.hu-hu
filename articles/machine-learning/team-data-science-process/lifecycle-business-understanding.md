---
title: A csoportos adatelemzési folyamat az üzleti igények felmérése
description: A célok, feladatok és a data-adatelemzési projektek a csoportos adatelemzési folyamat az üzleti ismertetése szakaszának le.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 35d03a52125bd2646f86b96bcffe123d9fab7f64
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60303553"
---
# <a name="the-business-understanding-stage-of-the-team-data-science-process-lifecycle"></a>Az üzleti ismertetése szakaszban a csoportos adatelemzési folyamat életciklusa

Ez a cikk ismerteti a célokat, feladatok és az üzleti ismertetése szakaszban, a csoportos adatelemzési folyamat (TDSP) társított le. Ez a folyamat egy ajánlott életciklussal, amely használatával a data-adatelemzési projektek strukturálása biztosít. Az életciklus a fő szakaszai, projektek általában végrehajtható, iteratív gyakran ismerteti:

   1. **Az üzleti igények felmérése**
   2. **Adatgyűjtés és adatértelmezés**
   3. **Modellezés**
   4. **Üzembe helyezés**
   5. **Felhasználói elfogadás**

A TDSP életciklus ábrázolása a következő: 

![TDSP-életciklus](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Célok
* Adja meg a kulcs változókat, amelyek a modell célokat szolgál, és amelynek kapcsolódó metrikák használt határozza meg a projekt sikerét.
* A releváns adatforrások, amelyek az üzleti férhet hozzá, vagy szerezze be kell azonosítani.

## <a name="how-to-do-it"></a>Megtudhatja, hogyan teheti
Nincsenek az ebben a szakaszban tárgyalt két fő feladat: 

   * **Célok meghatározása**: Működik az ügyfelek és más érdekelt felek és az üzleti problémák azonosításához. Állítson össze, amelyek meghatározzák az üzleti célokat, a data science technikák célzó kérdéseket.
   * **Adatforrások azonosítása**: Keresse meg a vonatkozó adatokat, amely segítséget nyújt a válaszoljon a kérdésekre, amelyek meghatározzák a projekt céljainak.

### <a name="define-objectives"></a>Célok meghatározása
1. Egy központi Ez a lépés célja, hogy azonosítsa a fő üzleti változókat, amelyek előre jelezni kell az elemzés. Ezeket a változókat, nevezzük a *célok modell*, és a hozzájuk társított metrikák használatával határozza meg a projekt sikerét. Az ilyen tárolók két példák értékesítési előrejelzések vagy egy folyamatban a csalárd jellegű megrendelés valószínűségét.

2. A projekt célok meghatározása szerint kéri, és az "éles" kérdések, amelyek a kapcsolódó, meghatározott és egyértelmű finomítása. Adatelemzési módszer az, amely a nevek és számok használ ilyen kérdésre. Általában használhatja adatelemzési és machine learning-öt típusú kérdések megválaszolásához:
 
   * Mekkora vagy hány? (regresszió)
   * Mely kategóriában jelentkezik? (osztályozás)
   * Melyik csoporthoz? (fürtökkel)
   * Az Ez furcsának? (az anomáliadetektálás)
   * Melyik lehetőség kell tenni? (javaslat)

   Határozza meg, amely ezeket a kérdéseket Ön kéri, és hogyan fogadó hatékonyabb üzleti céljaihoz.

3. Adja meg a projektcsapat és a feladataikat a tagok megadásával. Fejlesztés, ismételt futtatásával további információk felderítésére, magas szintű mérföldkő csomagot. 

4. Adja meg a sikerkritériumok metrikái. Például érdemes a vásárlók lemorzsolódásának prognosztizálása eléréséhez. A három hónapos projekt végén pontossága "x" %-os van szüksége. Ezeket az adatokat, és elérhetővé teheti churn ügyfél promóciókkal csökkentése érdekében. A metrikák kell **INTELLIGENS**: 

   * **S**ütemezésből 
   * **M**easurable
   * **A**chievable 
   * **R**elevant 
   * **T**ime kötött 

### <a name="identify-data-sources"></a>Adatforrások azonosítása
Azonosítsa az adatforrásokat, amelyek tartalmazzák az éles kérdésekre adott válaszok ismert példák. Keresse meg a következő adatokat:

* A kérdés vonatkozó adatokat. A cél és a cél kapcsolódó funkciók mértékek van?
* A modell cél pontos felmérését és a szolgáltatások fontos adatokat.

Előfordulhat például, hogy a meglévő rendszerek kell összegyűjtése és oldja meg a problémát, és a projekt célok eléréséhez további adattípus naplózása. Ebben a helyzetben érdemes, keresse meg a külső adatforrásokhoz, vagy frissíteni a rendszerek az új adatokat gyűjteni.

## <a name="artifacts"></a>Összetevők
Ebben a szakaszban az alábbiakban a le:

   * [Bérleti dokumentum](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Charter.md): Standard sablon megtalálható a TDSP projekt struktúrameghatározást. Bérleti Ez egy élő dokumentumot. Frissítse a projekt során a sablon, új felderítések győződjön meg arról, és üzleti követelmények változnak. A kulcs a újrafuttathatja esetén ez a dokumentum részletesen, hozzáadás, a felderítési folyamat során. Tartsa meg az ügyfél, és más érdekelt felek részt vesz a változtatásokat, és világosan a okok miatt a módosítások.  
   * [Adatforrások](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/Data%20Defintion.md#raw-data-sources): A **nyers adatforrások** szakaszában a **adatok definíciók** jelentést, amely megtalálható a TDSP projekt **jelentés** mappa tartalmazza azokat az adatforrásokat. Ez a szakasz meghatározza a nyers adatok az eredeti, illetve a célhelyeket. A későbbi szakaszokra, töltse ki például a szkripteket az adatok áthelyezése az elemzési környezet további részleteket.  
   * [Adatok szótárak](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Data_Dictionaries): Ez a dokumentum ismerteti az adatok, az ügyfél által biztosított. Ezeket a leírásokat a séma (az adattípusokat és az információkat az ellenőrzési szabályok, ha van ilyen) és az entitás-kapcsolat ábrák információkat tartalmaznak, ha elérhető.

## <a name="next-steps"></a>További lépések

Az alábbiakban a TDSP életciklusának minden lépése mutató hivatkozásokat:

   1. [Az üzleti igények felmérése](lifecycle-business-understanding.md)
   2. [Adatgyűjtés és adatértelmezés](lifecycle-data.md)
   3. [Modellezés](lifecycle-modeling.md)
   4. [Üzembe helyezés](lifecycle-deployment.md)
   5. [Felhasználói elfogadás](lifecycle-acceptance.md)

Azt adja meg a teljes, végpontok közötti forgatókönyvek, amelyek bemutatják, bizonyos forgatókönyvek esetén a folyamat összes lépését. A [példa forgatókönyvek](walkthroughs.md) a cikk a forgatókönyvek miniatűr leírások és hivatkozások listáját tartalmazza. A forgatókönyvek bemutatják, hogyan kombinálhatja a felhőbeli, helyszíni eszközöket és szolgáltatásokat, munkafolyamat vagy folyamat, intelligens alkalmazások létrehozására. 
