---
title: Cél, Node. js – LUIS
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban elérhető nyilvános LUIS-alkalmazással határozza meg egy felhasználó szándékát egy beszélgetés szövegéből. Node.js nyelven küldje el szövegként a felhasználó szándékát a nyilvános alkalmazás HTTP-előrejelzési végpontjára.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: 084d717c1001604a7fb8ed60518777f956dec8b9
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563800"
---
# <a name="quickstart-get-intent-using-nodejs"></a>Gyors útmutató: Szándék beszerzése a Node. js használatával

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

## <a name="prerequisites"></a>Előfeltételek

* [Node.js](https://nodejs.org/) programozási nyelv 
* [Visual Studio Code](https://code.visualstudio.com/)
* A df67dcdb-c37d-46af-88e1-8b97951ca1c2 azonosítójú nyilvános alkalmazás


> [!NOTE] 
> A teljes Node. js-megoldás az [ **Azure-Samples GitHub-** tárházból](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/analyze-text/node)érhető el.

## <a name="get-luis-key"></a>LUIS-kulcs lekérése

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>Szándék lekérése böngészővel

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent-programmatically"></a>Szándék lekérése programozott módon

A Node.js használatával hozzáférhet ugyanazokhoz az eredményekhez, amelyeket a böngészőablakban látott az előző lépésben.

1. Másolja ki az alábbi kódrészletet:

   [!code-nodejs[Console app code that calls a LUIS endpoint for Node.js](~/samples-luis/documentation-samples/quickstarts/analyze-text/node/call-endpoint.js)]

2. Hozzon létre egy `.env` fájlt a következő szöveggel, vagy állítsa be ezeket a változókat a rendszerkörnyezetben:

    ```CMD
    LUIS_APP_ID=df67dcdb-c37d-46af-88e1-8b97951ca1c2
    LUIS_ENDPOINT_KEY=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
    ```

3. Állítsa be a `LUIS_ENDPOINT_KEY` környezeti változót a kulcsára.

4. Telepítse a függőségeket a következő parancs parancssori futtatásával: `npm install`.

5. Futtassa a kódot a következővel: `npm start`. A futtatás ugyanazokat az értékeket jeleníti meg, amelyet korábban a böngészőablakban látott.

## <a name="luis-keys"></a>LUIS-kulcsok

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Törölje a Node.js fájlt.

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Beszédmódok hozzáadása](luis-get-started-node-add-utterance.md)
