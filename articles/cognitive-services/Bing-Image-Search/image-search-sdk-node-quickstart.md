---
title: 'Gyors útmutató: Képek keresése – Bing Image Search SDK a Node. js-hez'
titleSuffix: Azure Cognitive Services
description: Ebből a rövid útmutatóból megtudhatja, hogyan hajthatja végre első képkeresését a Bing Image Search SDK használatával, amely az API burkolójaként szolgál, és ugyanazokkal a funkciókkal rendelkezik. Ez az egyszerű Node.js-alkalmazás elküld egy képkeresési lekérdezést, elemzi a JSON-választ, és megjeleníti az első visszaadott kép URL-címét.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 03/04/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 24f6de759f233b82cb19a21401d3886a80bd746f
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423893"
---
# <a name="quickstart-search-for-images-with-the-bing-image-search-sdk-for-nodejs"></a>Gyors útmutató: Képek keresése a Node. js-hez készült Bing Image Search SDK-val

Ebből a rövid útmutatóból megtudhatja, hogyan hajthatja végre első képkeresését a Bing Image Search SDK használatával, amely az API burkolójaként szolgál, és ugyanazokkal a funkciókkal rendelkezik. Ez az egyszerű JavaScript-alkalmazás elküld egy képkeresési lekérdezést, elemzi a JSON-választ, és megjeleníti az első visszaadott kép URL-címét.

A minta forráskódja további hibakezeléssel és megjegyzésekkel együtt elérhető a [GitHubon](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/imageSearch.js).

## <a name="prerequisites"></a>Előfeltételek
A [Cognitive Services hozzáférési kulcsát](https://azure.microsoft.com/try/cognitive-services/) a **Keresés** területen kérheti le.  Lásd még: [a Cognitive Services díjszabás – keresési Bing-API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).  Lásd még: [a Cognitive Services díjszabás – keresési Bing-API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

* A [Node.js-hez készült Cognitive Services Image Search SDK](https://www.npmjs.com/package/azure-cognitiveservices-imagesearch)
    * Végezze el a telepítést az `npm install azure-cognitiveservices-imagesearch` paranccsal.
* A [Node.js Azure Rest](https://www.npmjs.com/package/ms-rest-azure) modul
    * Végezze el a telepítést az `npm install ms-rest-azure` paranccsal.

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. Hozzon létre egy új JavaScript-fájlt a kedvenc IDE-környezetében vagy szerkesztőjében, és állítsa be a szigorúságot, a https-t és az egyéb követelményeket.

    ```javascript
    'use strict';
    const ImageSearchAPIClient = require('azure-cognitiveservices-imagesearch');
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    ```

2. A projekt fő metódusában hozzon létre változókat érvényes előfizetési kulcsához, a Bing által visszaadandó képtalálatokhoz és egy keresési kifejezéshez. Ezután példányosítsa a képkeresési ügyfelet a kulcs használatával.

    ```javascript
    //replace this value with your valid subscription key.
    let serviceKey = "ENTER YOUR KEY HERE";

    //the search term for the request
    let searchTerm = "canadian rockies";

    //instantiate the image search client 
    let credentials = new CognitiveServicesCredentials(serviceKey);
    let imageSearchApiClient = new ImageSearchAPIClient(credentials);

    ```

## <a name="create-an-asynchronous-helper-function"></a>Aszinkron segítő függvény létrehozása

1. Hozzon létre egy függvényt, amely aszinkron módon hívja meg az ügyfelet, és visszaadja a Bing Image Search szolgáltatás válaszát.  
    ```javascript
    //a helper function to perform an async call to the Bing Image Search API
    const sendQuery = async () => {
        return await imageSearchApiClient.imagesOperations.search(searchTerm);
    };
    ```
   ## <a name="send-a-query-and-handle-the-response"></a>Lekérdezés küldése és a válasz kezelése

1. Hívja meg a segítő függvényt, és kezelje annak `promise` elemét a válaszban visszaadott képtalálatok elemzéséhez.

    Ha a válasz tartalmaz keresési eredményeket, tárolja az első találatot, és jelenítse meg annak részleteit, például a miniatűr URL-címét és az eredeti URL-címet a visszaadott képek teljes számával együtt.  
    ```javascript
    sendQuery().then(imageResults => {
        if (imageResults == null) {
        console.log("No image results were found.");
        }
        else {
            console.log(`Total number of images returned: ${imageResults.value.length}`);
            let firstImageResult = imageResults.value[0];
            //display the details for the first image result. After running the application,
            //you can copy the resulting URLs from the console into your browser to view the image.
            console.log(`Total number of images found: ${imageResults.value.length}`);
            console.log(`Copy these URLs to view the first image returned:`);
            console.log(`First image thumbnail url: ${firstImageResult.thumbnailUrl}`);
            console.log(`First image content url: ${firstImageResult.contentUrl}`);
        }
      })
      .catch(err => console.error(err))
    ```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyoldalas alkalmazás-oktatóanyag a Bing Image Search használatához](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app)

## <a name="see-also"></a>Lásd még

* [Mi a Bing Image Search?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Online interaktív bemutató kipróbálása](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Ingyenes Cognitive Services hozzáférési kulcs beszerzése](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)
* [Node.js-minták az Azure Cognitive Services SDK-hoz](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
* [Az Azure Cognitive Services dokumentációja](https://docs.microsoft.com/azure/cognitive-services)
* [Bing Image Search API – referencia](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
