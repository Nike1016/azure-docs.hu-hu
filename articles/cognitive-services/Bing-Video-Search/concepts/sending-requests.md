---
title: Keresési kérelmek küldése a Bing Video Search APInak
titleSuffix: Azure Cognitive Services
description: További információ a keresési lekérdezések küldéséről a Bing Video Search API.
services: cognitive-services
author: aahi
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: aahill
ms.openlocfilehash: ba2036c58ae54cc665623e63129ecea6c530b27d
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68878988"
---
# <a name="sending-search-requests-to-the-bing-video-search-api"></a>Keresési kérelmek küldése a Bing Video Search APInak

Ez a cikk ismerteti a Bing Video Search API küldött kérések paramétereit és attribútumait, valamint a visszaadott JSON-válasz objektumot. 

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="suggest-search-terms-with-the-bing-autosuggest-api"></a>Keresési kifejezések ajánlása a Bing Autosuggest API

Ha biztosít egy olyan keresőmezőt, ahol a felhasználók megadhatják a keresőkifejezést, a [Bing Autosuggest API](../../bing-autosuggest/get-suggested-search-terms.md) használatával kényelmesebbé teheti a felhasználói élményt. Az API javasolt lekérdezési sztringeket ad vissza a részleges keresőkifejezések alapján, miközben a felhasználó gépel.

Miután a felhasználó beírja a keresési kifejezést, URL-kódolással kódolja a [q](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#query) lekérdezési paraméter beállítása előtt. Ha például a felhasználó a *sailing dinghies* (kis vitorlások) kifejezésre keres, állítsa a `q` beállítást `sailing+dinghies` vagy `sailing%20dinghies` értékre.

## <a name="sending-a-request"></a>Kérelem küldése

A videókeresés eredményeinek lekéréséhez egy GET kérelmet kell küldeni a következő végpontra:  
  
```
https://api.cognitive.microsoft.com/bing/v7.0/videos/search
```
   
A kérelemnek a HTTPS protokollt kell használnia.

Javasoljuk, hogy minden kérelem egy kiszolgálóról induljon. Az azonosítónak egy ügyfélalkalmazás részeként való terjesztése több lehetőséget ad arra, hogy rosszindulatú külső felek hozzáférjenek az azonosítóhoz. A kiszolgálókon kezdeményezett hívások egyetlen frissítési pontot is biztosítanak az API jövőbeli verzióihoz.

  
A kérelemnek tartalmaznia kell a [q](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#query) lekérdezési paramétert, amely a felhasználó keresési kifejezését adja meg. Nem kötelező, de a kérelemnek érdemes tartalmaznia egy [mkt](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#mkt) lekérdezési paramétert is, amely azonosítja a piacot, ahonnan eredményeket szeretnénk kapni. Az opcionális lekérdezési paraméterek (például `pricing`) listáját lásd a [lekérdezési paramétereket](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#query-parameters) ismertető cikkben. Minden lekérdezési paraméter értékének URL-kódolásúnak kell lennie.  
  
A kérelemnek tartalmaznia kell az [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#subscriptionkey) fejlécet. Nem kötelező, de javasolt a következő fejlécek megadása is:  
  
-   [User-Agent](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#useragent)  
-   [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#clientid)  
-   [X-Search-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#clientip)  
-   [X-Search-Location](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#location)  

Az ügyfél IP-címe és helye fontos a helyfüggő tartalmak visszaadása szempontjából.  

Az összes kérelem- és válaszfejléc listáját lásd a [Fejlécek](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#headers) cikkben.

## <a name="example-search-request"></a>Példa keresési kérelemre

Az alábbiakban egy olyan keresési kérelem látható, amely az összes javasolt lekérdezési paramétert és fejlécet tartalmazza. Ha első alkalommal hívja meg bármelyik Bing API-t, ne használja az ügyfél-azonosító fejlécét. Csak akkor használja az ügyfél-azonosítót, ha korábban már meghívott egy Bing API-t, és visszakapott egy ügyfél-azonosítót a felhasználó és az eszköz kombinációjához. 
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

## <a name="example-json-response"></a>Példa JSON-válaszra

Az alábbiakban az előző kérelemre adott válasz látható. A példában a Bing-specifikus válaszfejlécek is láthatók.

[!INCLUDE [cognitive-services-bing-url-note](../../../../includes/cognitive-services-bing-url-note.md)]

```json
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D5694...",
    "totalEstimatedMatches" : 1000,
    "value" : [
        {
            "name" : "How to sail - What to Wear for Dinghy Sailing",
            "description" : "An informative video on what to wear when...",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D56...",
            "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OVP.DYWCvh...",
            "datePublished" : "2014-03-04T11:51:53",
            "publisher" : [
                {
                    "name" : "Fabrikam"
                }
            ],
            "creator" : {
                "name" : "Marcus Appel"
            },
            "contentUrl" : "https:\/\/www.fabrikam.com\/watch?v=vzmPjHZ--g",
            "hostPageUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D56944...",
            "encodingFormat" : "h264",
            "hostPageDisplayUrl" : "https:\/\/www.fabrikam.com\/watch?v=vzmPjBZ--g",
            "width" : 1280,
            "height" : 720,
            "duration" : "PT2M47S",
            "motionThumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?id=OM.Y6...",
            "embedHtml" : "<iframe width=\"1280\" height=\"720\" src=\"https:...><\/iframe>",
            "allowHttpsEmbed" : true,
            "viewCount" : 8743,
            "thumbnail" : {
                "width" : 300,
                "height" : 168
            },
            "videoId" : "6DB795E11A6E3CBAAD636DB795E11E3CBAAD63",
            "allowMobileEmbed" : true,
            "isSuperfresh" : false
        },
        . . .
    ],
    "nextOffset" : 0,
    "pivotSuggestions" : [
        {
            "pivot" : "sailing",
            "suggestions" : []
        },
        {
            "pivot" : "dinghies",
            "suggestions" : [
                {
                    "text" : "Sailing Cruising",
                    "displayText" : "Cruising",
                    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF754...",
                    "searchLink" : "https:\/\/api.cognitive.microsoft.com...",
                    "thumbnail" : {
                        "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?q=Sailing..."
                    }
                },
                . . .
            ]
        }
    ]
}
```

## <a name="next-steps"></a>További lépések

Próbálja ki az API-t. Lépjen a [Video Search API-tesztkonzolhoz](https://dev.cognitive.microsoft.com/docs/services/56b43f3ccf5ff8098cef3809/operations/58113fe5e31dac0a1ce6b0a8). 

A válaszobjektumok feldolgozásával kapcsolatban lásd: [Videók keresése az interneten](../search-the-web.md).

A videókhoz kapcsolódó információk, például a kapcsolódó keresések beszerzéséről bővebben a [videóelemzésekkel](../video-insights.md) foglalkozó részben olvashat.  
  
A közösségi oldalakon népszerű videókról bővebben a [népszerű videókkal](../trending-videos.md) foglalkozó részben olvashat.  
