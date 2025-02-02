---
title: 'Gyors útmutató: Hajtsa végre a hírkeresési, az a Bing News Search SDK for node.js használatával'
titleSuffix: Azure Cognitive Services
description: Ez a rövid útmutató segítségével hírkeresés a Bing News Search SDK használata a node.js-ben, és a válasz feldolgozása.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 06/18/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: c71d76e93eb1a121163d40fe61dcd5a8546d63f8
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67203343"
---
# <a name="quickstart-perform-a-news-search-with-the-bing-news-search-sdk-for-nodejs"></a>Gyors útmutató: Hajtsa végre a hírkeresési Bing News Search SDK-val a node.js-ben

Ez a rövid útmutató segítségével kezdeni a keresést hírkeresés a Bing News Search SDK-val, a node.js-ben. Míg a Bing News Search REST API-val kompatibilis szinte bármelyik programozási nyelvével, az SDK biztosít egy egyszerű módja annak, hogy a szolgáltatás integrálása az alkalmazásokba. Ez a minta forráskódja találhatók [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/newsSearch.js).

## <a name="prerequisites"></a>Előfeltételek

* [Node.js](https://nodejs.org/en/)

Beállítása egy konzolalkalmazást, a Bing News Search SDK használatával:
1. Futtatás `npm install ms-rest-azure` a fejlesztési környezetben.
2. Futtatás `npm install azure-cognitiveservices-newssearch` a fejlesztési környezetben.


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. Hozza létre a `CognitiveServicesCredentials` egy példányát. Hozzon létre változókat az előfizetési kulcs és a egy keresési kifejezést.

    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let search_term = 'Winter Olympics'
    ```

2. Hozza létre az ügyfél:
    
    ```javascript
    const NewsSearchAPIClient = require('azure-cognitiveservices-newssearch');
    let client = new NewsSearchAPIClient(credentials);
    ```

## <a name="send-a-search-query"></a>Keresési lekérdezés küldése

1. Az ügyfél használja a keresést a lekérdezés kifejezés ebben az esetben "Es téli Olimpián" segítségével:
    
    ```javascript
    client.newsOperations.search(search_term).then((result) => {
        console.log(result.value);
    }).catch((err) => {
        throw err;
    });
    ```

A kód `result.value`-elemeket jelenít meg a konzolon bármilyen szövegelemzés nélkül. Az eredmények – ha egyáltalán van találat egy kategóriában – a következőket fogják tartalmazni:

- `_type: 'NewsArticle'`
- `_type: 'WebPage'`
- `_type: 'VideoObject'`
- `_type: 'ImageObject'`

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyoldalas webalkalmazás létrehozása](tutorial-bing-news-search-single-page-app.md)
