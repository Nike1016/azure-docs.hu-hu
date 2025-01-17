---
title: Összetett entitások oktatóanyaga – LUIS
titleSuffix: Azure Cognitive Services
description: Adjon hozzá egy összetett entitást szeretné a különböző típusú kinyert adatok egyetlen tartalmazó entitásba. A kötegelés az adatokat, az ügyfélalkalmazás is könnyen kinyerheti az különböző adattípusok kapcsolódó adatokat.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 698635b3f216c556e1e36a033703b8786a028e38
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946133"
---
# <a name="tutorial-group-and-extract-related-data"></a>Oktatóanyag: Kapcsolódó adatok csoportosítása és kinyerése
Ebben az oktatóanyagban egy összetett entitás szeretné a kinyert adatokkal történő tartalmazó egyetlen entitás hozzáadása. A kötegelés az adatokat, az ügyfélalkalmazás is könnyen kinyerheti az különböző adattípusok kapcsolódó adatokat.

Az összetett entitás az a célja, hogy egy szülőentitás kategória kapcsolódó entitások csoportosíthatja. Az információk létezik külön entitásokként összetett létrehozása előtt. 

Az összetett entitás ideális ehhez az adattípushoz, mert az adatok:

* Kapcsolódnak egymáshoz. 
* Többféle típusú entitás használja.
* Csoportosítását és feldolgozását az ügyfélalkalmazásoknak egy információegységként kell végezniük.

**Ebben az oktatóanyagban az alábbiakkal fog megismerkedni:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Alkalmazás importálása – példa
> * Szándék létrehozása
> * Összetett entitás hozzáadása 
> * Betanítás
> * Közzététel
> * Szándék és entitások lekérése a végpontról

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Alkalmazás importálása – példa

1.  Töltse le és mentse az [alkalmazás JSON](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/build-app/tutorial_list.json) -fájlját az entitás listázása oktatóanyagból.

2. Importálja a JSON-t egy új alkalmazásba.

3. A **Manage** (Kezelés) szakasz **Versions** (Verziók) lapján klónozza a verziót, és adja neki a `composite` nevet. A klónozás nagyszerű mód, hogy kísérletezhessen a különböző LUIS-funkciókkal anélkül, hogy az az eredeti verzióra hatással lenne. Mivel a verzió neve az URL-útvonal részét képezi, a név nem tartalmazhat olyan karaktert, amely URL-címben nem érvényes.

## <a name="composite-entity"></a>Összetett entitást

Ebben az alkalmazásban a részleg neve a **Department** List entitásban van definiálva, és szinonimákat tartalmaz. 

A **TransferEmployeeToDepartment** célja, hogy egy alkalmazott hosszúságú kimondott szöveg egy új részlegbe. 

Példa erre a szándékra a következő hosszúságú kimondott szöveg:

|Példák kimondott szövegekre|
|--|
|John W. Smith áthelyezése a nyilvántartási részlegbe|
|Jill Jones átvitele az R & D-re|
 
Az áthelyezési kérelemnek tartalmaznia kell a részleg nevét és az alkalmazott nevét. 

## <a name="add-the-personname-prebuilt-entity-to-help-with-common-data-type-extraction"></a>Adja hozzá a PersonName előre összeépített entitást, amely segítséget nyújt a közös adattípusok kinyeréséhez

A LUIS számos előre összeállított entitást biztosít a gyakori adatok kinyeréséhez. 

1. Válassza a **Létrehozás** lehetőséget a felső navigációs menüben, majd válassza az **entitások** lehetőséget a bal oldali navigációs menüből.

1. Válassza a **Manage prebuilt entity** (Előre összeállított entitás kezelése) gombot.

1. Válassza a **[PersonName](luis-reference-prebuilt-person.md)** lehetőséget az előre elkészített entitások listájából, majd válassza a **kész**lehetőséget.

    ![Képernyőkép: számválasztó az előre összeállított entitások párbeszédpanelen](./media/luis-tutorial-composite-entity/add-personname-prebuilt-entity.png)

    Ez az entitás segít felvenni a nevek felismerését az ügyfélalkalmazás számára.

## <a name="create-composite-entity-from-example-utterances"></a>Összetett entitás létrehozása példa hosszúságú kimondott szöveg

1. A bal oldali menüben válassza az **Intents** (Szándékok) lehetőséget.

1. Válassza a **TransferEmployeeToDepartment** lehetőséget a cél listából.

1. A részletek `John Jackson`területen válassza ki a personName entitást, majd válassza a **becsomagolás az összetett entitásban** lehetőséget az előugró menüben a következő részletek megjelenítéséhez. `place John Jackson in engineering` 

    ![Képernyőkép a wrap Composite kiválasztásáról a legördülő párbeszédpanelen](./media/luis-tutorial-composite-entity/hr-create-composite-entity-1.png)

1. Azonnal válassza ki az utolsó entitás `engineering` az utterance (kifejezés) található. Zöld sáv megjelenítése a kiválasztott szavakat jelző egy összetett entitás alapján. Az előugró menüben adja meg az összetett név `TransferEmployeeInfo` majd válassza ki az Entert. 

    ![Képernyőkép az összetett név beírásáról a legördülő párbeszédpanelen](./media/luis-tutorial-composite-entity/hr-create-composite-entity-2.png)

1. **Milyen típusú entitást kíván létrehozni?** az összes szükséges mező a következő listában szerepel: `personName` és. `Department` Válassza a **Done** (Kész) lehetőséget. Figyelje meg, hogy az előre összeállított entitás (personName) hozzá lett adva az összetett entitáshoz. Ha egy előre összeállított entitások jelennek meg a kezdő és záró jogkivonatok összetett entitás között lehetnek, az összetett entitás ezeket előre összeállított entitások kell tartalmaznia. Ha az előre összeállított entitások nem szerepelnek, az összetett entitás nem jelzett megfelelően, de az egyes elemeket.

    ![Képernyőkép az összetett név beírásáról a legördülő párbeszédpanelen](./media/luis-tutorial-composite-entity/hr-create-composite-entity-3.png)

## <a name="label-example-utterances-with-composite-entity"></a>Az összetett entitás címke példa kimondott szöveg

1. Valamennyi példa utterance (kifejezés) válassza ki a bal szélső entitást, amely az összetett kell lennie. Válassza ki **Zabalit do összetett entitást**.

1. Válassza ki az összetett entitás utolsó szavát, majd az előugró menüből válassza a **TransferEmployeeInfo** lehetőséget. 

1. Ellenőrizze, hogy a célt az összes utterances vannak ellátva, az összetett entitáshoz. 

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Az alkalmazás betanítása, hogy tesztelni lehessen a szándék változásait 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Tegye közzé az alkalmazást, hogy a betanított modell lekérdezhető legyen a végpontról.

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Cél-és entitás-előrejelzés beolvasása a végpontról 

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Lépjen az URL-cím végéhez, és írja be a következőt: `Move Jill Jones to DevOps`. Az utolsó lekérdezési karakterlánc paraméter `q`, az utterance (kifejezés) lekérdezést. 

    Mivel ez a teszt ellenőrzése az összetett ki kell olvasni megfelelően, egy tesztet vagy tartalmazhat egy meglévő minta utterance (kifejezés) vagy egy új utterance (kifejezés). Ellenőrzi, hogy a gyermekentitások tartalmazza az összetett entitásban.

    ```json
    {
      "query": "Move Jill Jones to DevOps",
      "topScoringIntent": {
        "intent": "TransferEmployeeToDepartment",
        "score": 0.9882747
      },
      "intents": [
        {
          "intent": "TransferEmployeeToDepartment",
          "score": 0.9882747
        },
        {
          "intent": "None",
          "score": 0.00925369747
        }
      ],
      "entities": [
        {
          "entity": "jill jones",
          "type": "builtin.personName",
          "startIndex": 5,
          "endIndex": 14
        },
        {
          "entity": "devops",
          "type": "Department",
          "startIndex": 19,
          "endIndex": 24,
          "resolution": {
            "values": [
              "Development Operations"
            ]
          }
        },
        {
          "entity": "jill jones to devops",
          "type": "TransferEmployeeInfo",
          "startIndex": 5,
          "endIndex": 24,
          "score": 0.9607566
        }
      ],
      "compositeEntities": [
        {
          "parentType": "TransferEmployeeInfo",
          "value": "jill jones to devops",
          "children": [
            {
              "type": "builtin.personName",
              "value": "jill jones"
            },
            {
              "type": "Department",
              "value": "devops"
            }
          ]
        }
      ]
    }
    ```

   Az utterance (kifejezés) egy összetett entitások tömböt ad vissza. Minden entitás egy típusa és értéke van megadva. Minden gyermek entitásnak pontossággal megkereséséhez használja a megfelelő elem található entitások tömbben típusa és értéke összetett tömb elemének kombinációja.  

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Kapcsolódó információk

* [Entitások listázása – oktatóanyag](luis-quickstart-intents-only.md)
* [Összetett entitás](luis-concept-entity-types.md) fogalmi információi
* [Betanítás](luis-how-to-train.md)
* [Közzétételi útmutató](luis-how-to-publish-app.md)
* [Tesztelés a LUIS portálon](luis-interactive-test.md)


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott egy összetett entitást, hogy magába foglalja a meglévő entitásokat. Ez lehetővé teszi, hogy az ügyfélalkalmazás található kapcsolódó adatok csoportja folytatja a beszélgetés különböző adattípusokat. Egy ügyfélalkalmazás az emberi erőforrások alkalmazás megkérheti, mely napokon és időpontban az áthelyezés kell kezdődik és ér véget. Azt is megteheti, hogy az áthelyezés más logisztikai adatait, például egy fizikai telefont. 

> [!div class="nextstepaction"] 
> [Egy kifejezés listát egy egyszerű entitás hozzáadása](luis-quickstart-primary-and-secondary-data.md)  
