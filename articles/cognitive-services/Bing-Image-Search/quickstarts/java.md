---
title: 'Gyors útmutató: A Java használata a Bing Image Search meghívásához REST API'
titleSuffix: Azure Cognitive Services
description: Ezzel a rövid útmutatóval képkeresési kérelmeket küldhet a Bing Image Search REST API Javával, és JSON-válaszokat kaphat.
services: cognitive-services
documentationcenter: ''
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 07/22/2019
ms.author: aahi
ms.custom: seodec2018, seo-java-july2019
ms.openlocfilehash: a4a8535aa204cbad76d4da4c466c0f4aba99db9a
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489657"
---
# <a name="quickstart-search-for-images-using-the-bing-image-search-rest-api-and-java"></a>Gyors útmutató: Képek keresése a Bing Image Search REST API és a Java használatával

Ezzel a rövid útmutatóval megkezdheti a keresési kérések küldését a Bing Image Search API. Ez a Java-alkalmazás keresési lekérdezést küld az API-nak, és megjeleníti az eredményekben szereplő első rendszerkép URL-címét. Habár ez az alkalmazás Java nyelven íródott, az API egy REST-alapú webszolgáltatás, amely kompatibilis a legtöbb programozási nyelvvel.

A minta forráskódja további hibakezeléssel és megjegyzésekkel együtt elérhető a [GitHubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingImageSearchv7Quickstart.java).

## <a name="prerequisites"></a>Előfeltételek

* A [Java fejlesztői készlet (JDK)](https://aka.ms/azure-jdks)

* A [Gson-kódtár](https://github.com/google/gson)

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Projekt létrehozása és inicializálása

1. Hozzon létre egy új Java-projektet a kedvenc IDE-környezetében vagy szerkesztőjében, és importálja az alábbi kódtárakat.

    ```java
    import java.net.*;
    import java.util.*;
    import java.io.*;
    import javax.net.ssl.HttpsURLConnection;
    import com.google.gson.Gson;
    import com.google.gson.GsonBuilder;
    import com.google.gson.JsonObject;
    import com.google.gson.JsonParser;
    ```

2. Hozza létre az API-végpont, az előfizetési kulcsot és a kerőkifejezést.

    ```java
    static String subscriptionKey = "enter key here";
    static String host = "https://api.cognitive.microsoft.com";
    static String path = "/bing/v7.0/images/search";
    static String searchTerm = "tropical ocean";
    ```

## <a name="construct-the-search-request-and-query"></a>A keresési kérelem és a lekérdezés összeállítása

1. Az előző lépés változóit használva formázza a keresési URL-címet az API-kérelemhez. A keresési kifejezésnek URL-kódolással kell rendelkeznie, mielőtt hozzáfűzi a kéréshez.

    ```java
    // construct the search request URL (in the form of endpoint + query string)
    URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchQuery, "UTF-8"));
    HttpsURLConnection connection = (HttpsURLConnection)url.openConnection();
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);
    ```

## <a name="receive-and-process-the-json-response"></a>A JSON-válasz fogadása és feldolgozása

1. A Bing Image Search API-tól érkező JSON-válasz fogadása és az eredményobjektum összeállítása.

    ```java
    // receive JSON body
    InputStream stream = connection.getInputStream();
    String response = new Scanner(stream).useDelimiter("\\A").next();
    // construct result object for return
    SearchResults results = new SearchResults(new HashMap<String, String>(), response);
    ```
2. Válassza külön a Bing-specifikus HTTP-fejléceket a JSON-törzstől
    ```java
    // extract Bing-related HTTP headers
    Map<String, List<String>> headers = connection.getHeaderFields();
    for (String header : headers.keySet()) {
        if (header == null) continue;      // may have null key
        if (header.startsWith("BingAPIs-") || header.startsWith("X-MSEdge-")) {
            results.relevantHeaders.put(header, headers.get(header).get(0));
        }
    }
    ```

3. Zárja le az adatfolyamot, és elemezze a választ. Szerezze meg a visszaadott keresési eredmények teljes számát és az első képeredmény miniatűrképének URL-címét.

    ```java
    stream.close();
    JsonParser parser = new JsonParser();
    JsonObject json = parser.parse(result.jsonResponse).getAsJsonObject();
    //get the first image result from the JSON object, along with the total
    //number of images returned by the Bing Image Search API.
    String total = json.get("totalEstimatedMatches").getAsString();
    JsonArray results = json.getAsJsonArray("value");
    JsonObject first_result = (JsonObject)results.get(0);
    String resultURL = first_result.get("thumbnailUrl").getAsString();
    ```

## <a name="example-json-response"></a>Példa JSON-válaszra

A Bing Image Search API válaszai JSON formátumban érkeznek vissza. A mintaválasz egyetlen eredményre van csonkolva.

```json
{
"_type":"Images",
"instrumentation":{
    "_type":"ResponseInstrumentation"
},
"readLink":"images\/search?q=tropical ocean",
"webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=tropical ocean&FORM=OIIARP",
"totalEstimatedMatches":842,
"nextOffset":47,
"value":[
    {
        "webSearchUrl":"https:\/\/www.bing.com\/images\/search?view=detailv2&FORM=OIIRPO&q=tropical+ocean&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&simid=608027248313960152",
        "name":"My Life in the Ocean | The greatest WordPress.com site in ...",
        "thumbnailUrl":"https:\/\/tse3.mm.bing.net\/th?id=OIP.fmwSKKmKpmZtJiBDps1kLAHaEo&pid=Api",
        "datePublished":"2017-11-03T08:51:00.0000000Z",
        "contentUrl":"https:\/\/mylifeintheocean.files.wordpress.com\/2012\/11\/tropical-ocean-wallpaper-1920x12003.jpg",
        "hostPageUrl":"https:\/\/mylifeintheocean.wordpress.com\/",
        "contentSize":"897388 B",
        "encodingFormat":"jpeg",
        "hostPageDisplayUrl":"https:\/\/mylifeintheocean.wordpress.com",
        "width":1920,
        "height":1200,
        "thumbnail":{
        "width":474,
        "height":296
        },
        "imageInsightsToken":"ccid_fmwSKKmK*mid_8607ACDACB243BDEA7E1EF78127DA931E680E3A5*simid_608027248313960152*thid_OIP.fmwSKKmKpmZtJiBDps1kLAHaEo",
        "insightsMetadata":{
        "recipeSourcesCount":0,
        "bestRepresentativeQuery":{
            "text":"Tropical Beaches Desktop Wallpaper",
            "displayText":"Tropical Beaches Desktop Wallpaper",
            "webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=Tropical+Beaches+Desktop+Wallpaper&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&FORM=IDBQDM"
        },
        "pagesIncludingCount":115,
        "availableSizesCount":44
        },
        "imageId":"8607ACDACB243BDEA7E1EF78127DA931E680E3A5",
        "accentColor":"0050B2"
    }]
}
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyoldalas alkalmazás-oktatóanyag a Bing Image Search használatához](../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Lásd még

* [Mi a Bing Image Search?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Online interaktív bemutató kipróbálása](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/) 
* [](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) A Bing Search API-k díjszabása. 
* [Ingyenes Cognitive Services hozzáférési kulcs beszerzése](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
* [Az Azure Cognitive Services dokumentációja](https://docs.microsoft.com/azure/cognitive-services)
* [Bing Image Search API – referencia](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
