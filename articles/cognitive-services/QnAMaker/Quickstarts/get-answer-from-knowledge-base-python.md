---
title: 'Gyors útmutató: Válasz az Tudásbázisból – REST, Python – QnA Maker'
titleSuffix: Azure Cognitive Services
description: Ez a Python REST-alapú rövid útmutató végigvezeti egy adott Tudásbázisból származó válasz beszerzésének lépésein.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 05/24/2019
ms.author: diberry
ms.openlocfilehash: 71f2cc3ef028d66be7049cf85003d51e6b4baef3
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559799"
---
# <a name="get-answers-to-a-question-from-a-knowledge-base-with-python"></a>Válaszok a Tudásbázisban feltett kérdésekre a Python használatával

Ez a rövid útmutató végigvezeti a közzétett QnA Maker Tudásbázisból származó válasz programozott módon történő beszerzésének lépésein. A Tudásbázis az adatforrásokból, például [](../Concepts/data-sources-supported.md) a GYIK-ből származó kérdéseket és válaszokat tartalmaz. A rendszer elküldi a [kérdést](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) a QnA Maker szolgáltatásnak. A [Válasz](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties) tartalmazza a legfontosabb előre jelzett választ. 

## <a name="prerequisites"></a>Előfeltételek

* [Python 3,6 vagy újabb](https://www.python.org/downloads/)
* [Visual Studio Code](https://code.visualstudio.com/)
* Rendelkeznie kell [QnA Maker-szolgáltatással](../How-To/set-up-qnamaker-service-azure.md) is. A kulcs lekéréséhez válassza ki **kulcsok** alatt **erőforrás-kezelés** az Azure irányítópultján a QnA Maker erőforrás. 
* **Közzétételi** oldal beállításai Ha nem rendelkezik közzétett tudásbázissal, hozzon létre egy üres tudásbázist, majd importáljon egy tudásbázist a **Beállítások** lapon, majd tegye közzé. [Ezt az](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv)alapszintű tudásbázist töltheti le és használhatja. 

    A közzétételi oldal beállításai közé tartozik az útvonal értéke, a gazdagép értéke és a EndpointKey érték. 

    ![Közzétételi beállítások](../media/qnamaker-quickstart-get-answer/publish-settings.png)

A rövid útmutató kódja [https://github.com/Azure-Samples/cognitive-services-qnamaker-python](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/tree/master/documentation-samples/quickstarts/get-answer) a tárházban található. 

## <a name="create-a-python-file"></a>Python-fájl létrehozása

Nyissa meg a VSCode, és hozzon létre egy új fájlt `get-answer-3x.py`.

## <a name="add-the-required-dependencies"></a>A szükséges függőségek hozzáadása

A `get-answer-3x.py` fájl tetején adja hozzá a szükséges függőségeket a projekthez:

[!code-python[Add the required dependencies](~/samples-qnamaker-python/documentation-samples/quickstarts/get-answer/get-answer-3x.py?range=1-2 "Add the required dependencies")]

A gazdagép és az útvonal eltér a **közzétételi** lapon megjelenő. Ennek az az oka, hogy a Python-tár nem engedélyez semmilyen útválasztást a gazdagépen. A **közzétételi** lapon megjelenő útválasztás a gazdagép részeként át lett helyezve az útvonalra.

## <a name="add-the-required-constants"></a>A szükséges konstansok hozzáadása

Adja hozzá a szükséges állandókat a QnA Maker eléréséhez. Ezek az értékek a **közzétételi** lapon jelennek meg, miután közzétette a tudásbázist. 

[!code-python[Add the required constants](~/samples-qnamaker-python/documentation-samples/quickstarts/get-answer/get-answer-3x.py?range=5-25 "Add the required constants")]

## <a name="add-a-post-request-to-send-question-and-get-an-answer"></a>POST-kérelem hozzáadása a kérdés elküldéséhez és a válasz beszerzéséhez

A következő kód egy HTTPS-kérést küld a QnA Maker APInak, hogy elküldje a kérdést a Tudásbázisnak, és fogadja a választ:

[!code-python[Add a POST request to send question to knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/get-answer/get-answer-3x.py?range=27-48 "Add a POST request to send question to knowledge base")]

A `Authorization` fejléc értéke tartalmazza a karakterláncot `EndpointKey`. 

## <a name="run-the-program"></a>A program futtatása

Futtassa a programot a parancssorból. A kérelem automatikusan elküldi a QnA Maker API, majd a konzolablakban nyomtatási.

Futtassa a fájlt:

```bash
python get-answer-3x.py
```

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)] 

További információ a [kérelemről](../how-to/metadata-generateanswer-usage.md#generateanswer-request) és a [válaszról](../how-to/metadata-generateanswer-usage.md#generateanswer-response).

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API-referencia](https://go.microsoft.com/fwlink/?linkid=2092179)
