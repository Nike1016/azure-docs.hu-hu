---
title: Előre elkészített entitások a Language Understandinghoz
titleSuffix: Azure Cognitive Services
description: LUIS az általános típusú adatok, például a dátumok, időpontok, számok, mértékek és pénznem FELISMERVE előre összeállított entitások egy készletét tartalmazza. Előre összeállított entitások támogatása a LUIS-alkalmazás kulturális környezete eltérő.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: diberry
ms.openlocfilehash: 3f36680477b29158ae4a4214dfef6ea36342342c
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932638"
---
# <a name="prebuilt-entities-to-recognize-common-data-types"></a>Előre összeállított entitások felismerésére általános adattípusok

LUIS az általános típusú adatok, például a dátumok, időpontok, számok, mértékek és pénznem FELISMERVE előre összeállított entitások egy készletét tartalmazza. 

## <a name="add-a-prebuilt-entity"></a>Egy előre létrehozott entitás hozzáadása

1. Nyissa meg az alkalmazás nevére kattintva **saját alkalmazások** lapon, és kattintson a **entitások** a bal oldalon. 

1. Az a **entitások** kattintson **előre összeállított entitás hozzáadása**.

1. A **előre összeállított entitások hozzáadása** párbeszédpanelen jelölje ki a datetimeV2 előre összeállított entitások. 

    ![Előre összeállított entitások párbeszédpanel hozzáadása](./media/luis-use-prebuilt-entity/add-prebuilt-entity-dialog.png)

1. Válassza a **Done** (Kész) lehetőséget.

## <a name="publish-the-app"></a>Az alkalmazás közzététele

Előre összeállított entitások értékének megtekintéséhez a legegyszerűbb módja az, hogy a közzétett végpontról lekérdezés. 

1. A felső eszköztáron válassza **közzététel**. Közzététele **éles**. 

1. Ha a zöld, sikeres értesítés jelenik meg, válassza ki a **tekintse meg a végpontok listáját** hivatkozásra kattintva megtekintheti a végpontok.

1. Válasszon végpontot. Egy új böngészőlapon nyílik meg, hogy a végpont. Ne zárja be a böngészőlapot, és továbbra is a **teszt** szakaszban.

## <a name="test"></a>Tesztelés
Az entitás hozzáadása után nem kell az alkalmazás betanításához. 

Teszt új célja a végponton által hozzáadott értéket a **q** paraméter. Használja az alábbi táblázat a javasolt utterances **q**:

|Tesztelje az utterance (kifejezés)|Entitás érték|
|--|:--|
|Könyv holnap repülőjegyet|2018-10-19|
|a találkozó március 3-án megszakítása|A LUIS a legutóbbi március 3 korábban visszaadott (2018-03-03) és március 3 a jövőben (a 2019-03-03), mert az utterance (kifejezés) nem adott meg egy év.|
|10-kor értekezlet ütemezése|10:00:00|

## <a name="marking-entities-containing-a-prebuilt-entity-token"></a>Olyan előre összeállított entitások jogkivonatot tartalmazó entitások jelölés
 Ha rendelkezik a szöveget, például `HH-1234`, amelyek jelölhetnek egy egyéni entitást szeretne _és_ rendelkezik [előre összeállított szám](luis-reference-prebuilt-number.md) ad hozzá a modellt, nem lehet megjelölni az egyéni entitáshoz, a LUIS-portálon. Ez az API-val jelölheti meg. 

 Jelölje meg a jogkivonatot, ahol egy része már meg van jelölve az előre összeállított entitások, az ilyen típusú távolítsa el az előre összeállított entitások a LUIS-alkalmazásból. Nem kell az alkalmazás betanításához. Ezután jelölje meg a jogkivonatot a saját egyéni entitást. Ezután adja hozzá az előre összeállított entitások vissza a LUIS-alkalmazás.

 Egy másik példaként tekintse meg az osztály beállításai listáját: `I want first year spanish, second year calculus, and fourth year english lit.`Ha a Luis-alkalmazáshoz az előépítés sorszáma `second`van hozzáadva `first`, a, és `fourth` a már sorszámmal van megjelölve. Ha szeretné rögzíteni azt a sorszámot, és az osztályt, összetett entitás létrehozása, és tegye azokat az előre összeállított sorszámnál és az egyéni entitás osztály neveként.

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Előre összeállított entitások bemutatása](./luis-reference-prebuilt-entities.md)
