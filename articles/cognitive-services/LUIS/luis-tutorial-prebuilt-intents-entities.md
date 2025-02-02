---
title: Előre összeépített leképezések és entitások – LUIS
titleSuffix: Azure Cognitive Services
description: Ebben az oktatóanyagban előre elkészített leképezéseket és entitásokat ad hozzá egy alkalmazáshoz, amellyel gyorsan elsajátíthatja a szándék előrejelzését és az kinyerést. A kimondott szövegeket nem szükséges megcímkézni előre összeállított entitásokkal. Az entitást a rendszer automatikusan észleli.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 07/16/2018
ms.author: diberry
ms.openlocfilehash: 8c3fa2a68018e0eb45f37b54b16b6e7797cfa7c4
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560045"
---
# <a name="tutorial-identify-common-intents-and-entities"></a>Oktatóanyag: Közös szándékok és entitások azonosítása

Ebben az oktatóanyagban előre elkészített leképezéseket és entitásokat ad hozzá egy emberi erőforrásokkal foglalkozó oktatóanyag-alkalmazáshoz, amellyel gyorsan elsajátíthatja a szándék előrejelzését és az kinyerést. Nem kell megjelölnie egy hosszúságú kimondott szöveg az előre elkészített entitásokkal, mert a rendszer automatikusan észleli az entitást.

Az előre elkészített modellek (tartományok, leképezések és entitások) segítenek a modell gyors létrehozásában.

**Ebben az oktatóanyagban az alábbiakkal fog megismerkedni:**

> [!div class="checklist"]
> * Új alkalmazás létrehozása
> * Előre összeállított szándékok hozzáadása 
> * Előre összeállított entitások hozzáadása 
> * Betanítás 
> * Közzététel 
> * Szándék és entitások lekérése a végpontról

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="create-a-new-app"></a>Új alkalmazás létrehozása

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]


## <a name="add-prebuilt-intents-to-help-with-common-user-intentions"></a>Előre összeépített leképezések hozzáadása a gyakori felhasználói szándékok segítésére

A LUIS számos előre összeállított szándékot biztosít, amelyek segítenek a gyakori felhasználói szándékokban.  

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Válassza az **Add prebuilt domain intent** (Előre összeállított tartományi szándék hozzáadása) lehetőséget. 

1. Keressen a `Utilities` kifejezésre. 

    [![Képernyőkép az előre elkészített leképezési párbeszédpanelről a keresőmező segédprogramjaival](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png)](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png#lightbox)

1. Válassza ki a következő szándékokat, majd a **Done** (Kész) lehetőséget: 

   * Utilities.Cancel
   * Utilities.Confirm
   * Utilities.Help
   * Utilities.StartOver
   * Utilities.Stop

     Ezek a szándékok segítenek megállapítani, hogy hol, a beszélgetésben, a felhasználó, és mit kérnek. Ezek a leképezések az entitások használatát is tartalmazzák. Ezek az entitások automatikusan hozzáadódnak a LUIS-alkalmazáshoz: number, sorszám és Utilities. DirectionalReference. 


## <a name="add-prebuilt-entities-to-help-with-common-data-type-extraction"></a>Előre összeépített entitások hozzáadása a közös adattípusok kinyerésének megkönnyítéséhez

A LUIS számos előre összeállított entitást biztosít a gyakori adatok kinyeréséhez. 

1. Válassza az **Entities** (Entitások) elemet a bal oldali navigációs menüben.

1. Válassza az **Add prebuilt entity** (Előre összeállított entitás hozzáadása) gombot.

1. Válassza ki a következő entitásokat az előre elkészített entitások listájából, majd válassza a **kész**lehetőséget:

   * **[PersonName](luis-reference-prebuilt-person.md)** 
   * **[GeographyV2](luis-reference-prebuilt-geographyV2.md)**

     ![Képernyőkép: számválasztó az előre összeállított entitások párbeszédpanelen](./media/luis-tutorial-prebuilt-intents-and-entities/select-prebuilt-entities.png)

     Ezek az entitások segítséget nyújtanak a név és a hely felismerésének hozzáadásához az ügyfélalkalmazás számára.

## <a name="add-example-utterances-to-the-none-intent"></a>Példa hosszúságú kimondott szöveg hozzáadása a none szándékhoz 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Az alkalmazás betanítása, hogy tesztelni lehessen a szándék változásait 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Tegye közzé az alkalmazást, hogy a betanított modell lekérdezhető legyen a végpontról.

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Cél-és entitás-előrejelzés beolvasása a végpontról

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

1. Lépjen az URL-cím végéhez a böngésző címsorában, és írja be a következőt: `I want to cancel my trip to Seattle to see Bob Smith`. Az utolsó lekérdezésisztring-paraméter a `q`, a kimondott szöveg pedig **query**. 

    ```json
    {
      "query": "I want to cancel my trip to Seattle to see Bob Smith",
      "topScoringIntent": {
        "intent": "Utilities.Cancel",
        "score": 0.807676256
      },
      "intents": [
        {
          "intent": "Utilities.Cancel",
          "score": 0.807676256
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.0487322025
        },
        {
          "intent": "Utilities.Help",
          "score": 0.0208660364
        },
        {
          "intent": "None",
          "score": 0.008789532
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.006929268
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.00136293867
        }
      ],
      "entities": [
        {
          "entity": "seattle",
          "type": "builtin.geographyV2.city",
          "startIndex": 28,
          "endIndex": 34
        },
        {
          "entity": "bob smith",
          "type": "builtin.personName",
          "startIndex": 43,
          "endIndex": 51
        }
      ]
    }
    ```

    Az eredmény előre jelezte a segédprogramokat. az 80%-os megbízhatósággal megszakítja a szándékot, és kibontotta a város és a személy nevét. 


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Kapcsolódó információk

További információ az előre elkészített modellekről:

* [Előre elkészített tartományok](luis-reference-prebuilt-domains.md): ezek olyan általános tartományok, amelyek csökkentik a teljes Luis-alkalmazások készítését
* Előre elkészített leképezések: ezek a közös tartományok egyedi céljait jelentik. A teljes tartomány hozzáadása helyett a leképezéseket külön is hozzáadhatja.
* [Előre elkészített entitások](luis-prebuilt-entities.md): ezek a leggyakoribb adattípusok a legtöbb Luis-alkalmazás esetében hasznosak.

További információ a LUIS-alkalmazás használatáról:

* [Betanítás](luis-how-to-train.md)
* [Közzétételi útmutató](luis-how-to-publish-app.md)
* [Tesztelés a LUIS portálon](luis-interactive-test.md)

## <a name="next-steps"></a>További lépések

Az előre összeállított szándékok és entitások hozzáadásával az ügyfélalkalmazás képes észlelni a gyakori felhasználói szándékokat és kinyerni a gyakori adattípusokat.  

> [!div class="nextstepaction"]
> [Reguláriskifejezés-entitás hozzáadása az alkalmazáshoz](luis-quickstart-intents-regex-entity.md)

