---
title: Reguláris kifejezésű entitás – LUIS
titleSuffix: Azure Cognitive Services
description: Konzisztensen formázott adatok kinyerése kimondott szövegből a Reguláris kifejezés entitással.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 06/03/2019
ms.author: diberry
ms.openlocfilehash: 958d880829e9524389a25f029415786231644c04
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563598"
---
# <a name="tutorial-get-well-formatted-data-from-the-utterance"></a>Oktatóanyag: Jól formázott adatok beolvasása a teljes területről
Ebben az oktatóanyagban hozzon létre egy alkalmazást a konzisztens formátumú adatok kinyeréséhez a **reguláris kifejezés** entitás használatával.

**Ebben az oktatóanyagban az alábbiakkal fog megismerkedni:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Új alkalmazás létrehozása 
> * Szándék hozzáadása
> * Reguláriskifejezés-entitás hozzáadása 
> * Betanítás
> * Közzététel
> * Szándék és entitások lekérése a végpontról

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="regular-expression-entities"></a>Reguláris kifejezések entitásai

Ez az alkalmazás a reguláris kifejezés entitást használja a jól formázott emberi erőforrások (HR) formátumú számok kikapcsolására. Bár a kimondott szöveg szándékát mindig gépi tanulás határozza meg, ez az entitástípus nem gép által tanult. 

**Példák kimondott szövegekre:**

|Példák kimondott szövegekre|
|--|
|Hol található a HRF-123456?|
|Ki szerzője HRF-123234?|
|A HRF-456098 francia nyelven van közzétéve?|
|HRF-456098|
|HRF-456098 dátum?|
 
A reguláris kifejezés megfelelő választás az ilyen típusú adatok esetén, ha

* az adatok helyesen formázottak.


## <a name="create-a-new-app"></a>Új alkalmazás létrehozása

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-intent-for-finding-form"></a>Szándék létrehozása az űrlap kereséséhez

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Válassza a **Create new intent** (Új szándék létrehozása) lehetőséget. 

1. Az előugró párbeszédpanelen írja be a `FindForm` karakterláncot, majd válassza a **Kész** elemet. 

    ![Képernyőkép a Create new intent (Új szándék létrehozása) párbeszédpanelről, a keresőmezőben a Utilities (Segédprogramok) karaktersorral](./media/luis-quickstart-intents-regex-entity/create-new-intent-ddl.png)

1. Adjon hozzá kimondott példaszövegeket a szándékhoz.

    |Példák kimondott szövegekre|
    |--|
    |Mi a hrf-123456 URL-címe?|
    |Hol van a hrf-345678?|
    |Mikor frissült a hrf-456098?|
    |Frissítette Kovács János múlt héten a hrf-234639 űrlapot?|
    |Hány HRF-345123-verzió létezik?|
    |Kinek kell jóváhagynia a hrf-123456 űrlapot?|
    |Hány személynek kell jóváhagynia a hrf-345678 űrlapot?|
    |A hrf-234123 dátuma?|
    |A hrf-546234 szerzője?|
    |A hrf-456234 címe?|

    [![Képernyőkép a cél oldalról az új hosszúságú kimondott szöveg kiemelve](./media/luis-quickstart-intents-regex-entity/findform-intent.png)](./media/luis-quickstart-intents-regex-entity/findform-intent.png#lightbox)

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]  

## <a name="use-the-regular-expression-entity-for-well-formatted-data"></a>A reguláris kifejezés entitás használata jól formázott adathoz
A reguláriskifejezés-entitás, amelyet meg kell feleltetni az űrlapszámnak: `hrf-[0-9]{6}`. Ez a reguláriskifejezés-entitás megfelelteti egymásnak a `hrf-` karaktereket, de figyelmen kívül hagyja, hogy kis- vagy nagybetűvel szerepelnek-e, illetve a kulturális változatokat. Megfelelteti egymásnak a számjegyeket 0-tól 9-ig, pontosan 6 számjegyet.

A HRF a következőt jelöli: `human resources form`.

A LUIS elvégzi a kimondott szöveg lexikális elemzését, ha az hozzá van adva egy szándékhoz. Ezen kimondott szövegek lexikális elemzése szóközöket ad hozzá a kötőjel előtt és után, `Where is HRF - 123456?` A reguláris kifejezést a rendszer nyers formájában alkalmazza a kimondott szövegre, még a lexikális elemzés előtt. Mivel még a _nyers_ űrlapra van alkalmazva, a reguláris kifejezésnek nem kell foglalkoznia a szóhatárokkal. 

A következő lépésekkel hozzon létre egy reguláriskifejezés-entitást, amely elmagyarázza a LUIS-nak, mi az a HRF-számformátum:

1. Válassza az **Entities** (Entitások) elemet a bal oldali ablaktáblán.

1. Az Entities (Entitások) lapon válassza a **Create new entity** (Új entitás létrehozása) gombot. 

1. A felugró párbeszédpanelen az új entitásnak adja a `HRF-number` nevet, válassza a **RegEx** entitástípust, a **Regex** értéke legyen `hrf-[0-9]{6}`, majd válassza a **Done** (Kész) lehetőséget.

    ![Képernyőkép az új entitás tulajdonságainak beállításakor felugró párbeszédpanelről](./media/luis-quickstart-intents-regex-entity/create-regex-entity.png)

1. A bal oldali menüben válassza az **Intents** (Szándékok) elemet, majd a **FindForm** szándékot kiválasztva megtekintheti a kimondott szövegekben a megcímkézett reguláris kifejezést. 

    [![Képernyőkép a Label (Címke) kimondott szövegről létező entitás és regex mintával](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png)](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png#lightbox)

    Mivel az entitás nem egy géppel megtanult entitás, az entitás a hosszúságú kimondott szöveg lesz alkalmazva, és a LUIS webhelyen jelenik meg, amint létrejött.

## <a name="add-example-utterances-to-the-none-intent"></a>Példa hosszúságú kimondott szöveg hozzáadása a none szándékhoz 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-before-testing-or-publishing"></a>Az alkalmazás betanítása tesztelés vagy közzététel előtt

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>Az alkalmazás közzététele a végpontról történő lekérdezéshez

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Cél-és entitás-előrejelzés beolvasása a végpontról

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Nyissa meg a címet az URL-cím végére, és adja meg a következő értéket:

    `When were HRF-123456 and hrf-234567 published in the last year?`

    Az utolsó lekérdezésisztring-paraméter `q`, a kimondott szöveg pedig a **query**. A kimondott szöveg nem egyezik meg egyik címkézett kimondott szöveggel sem, ezért tesztnek pont megfelelő, és `FindForm` szándékot kell visszaadnia `HRF-123456` és `hrf-234567` űrlapszámmal.

    ```json
    {
      "query": "When were HRF-123456 and hrf-234567 published in the last year?",
      "topScoringIntent": {
        "intent": "FindForm",
        "score": 0.9988884
      },
      "intents": [
        {
          "intent": "FindForm",
          "score": 0.9988884
        },
        {
          "intent": "None",
          "score": 0.00204812363
        }
      ],
      "entities": [
        {
          "entity": "hrf-123456",
          "type": "HRF-number",
          "startIndex": 10,
          "endIndex": 19
        },
        {
          "entity": "hrf-234567",
          "type": "HRF-number",
          "startIndex": 25,
          "endIndex": 34
        }
      ]
    }
    ```

    Ha reguláriskifejezés-entitást használ, a LUIS kinyeri a megnevezett adatokat, ami programozás szempontjából nagyobb segítséget nyújt a JSON-választ fogadó ügyfélalkalmazásnak.


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Kapcsolódó információk

* [Reguláris kifejezéssel](luis-concept-entity-types.md#regular-expression-entity) rendelkező entitások fogalmai
* [Betanítás](luis-how-to-train.md)
* [Közzétételi útmutató](luis-how-to-publish-app.md)
* [Tesztelés a LUIS portálon](luis-interactive-test.md)

## <a name="next-steps"></a>További lépések
Ez az oktatóanyag létrehozott egy új szándékot, példaszándékokat adott hozzá, majd létrehozott egy reguláriskifejezés-entitást a helyesen formázott adatok kimondott szövegből történő kinyerésének céljából. Az alkalmazás betanítása és közzététele után egy végpontlekérdezés azonosította a szándékot, és visszaadta a kinyert adatokat.

> [!div class="nextstepaction"]
> [Tudnivalók a listaentitásról](luis-quickstart-intent-and-list-entity.md)

