---
title: Mi a Bing Entity Search API?
titleSuffix: Azure Cognitive Services
description: A Bing Entity Search API használatával kinyerheti és keresheti meg a keresési lekérdezések entitásait és helyét.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: overview
ms.date: 02/01/2019
ms.author: scottwhi
ms.openlocfilehash: 8f43401296a154ee40e7c214ad63da878129244a
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68424013"
---
# <a name="what-is-bing-entity-search-api"></a>Mi az Bing Entity Search API?

A Bing Entity Search API egy keresési lekérdezést küld a Bingnek, majd entitásokat és helyeket tartalmazó találatokat kap vissza. A helytalálatok lehetnek éttermek, szállodák vagy egyéb helyi vállalkozások. A Bing helyeket ad vissza, ha a lekérdezés a helyi vállalkozás nevét adja meg, vagy egy vállalkozástípus iránt érdeklődik (például „éttermek a közelben”). A Bing visszaadja az entitásokat, ha a lekérdezés jól ismert személyeket, helyeket (turisztikai látványosságokat, államokat, országokat/régiókat stb.) vagy dolgokat ad meg.

|Funkció  |Leírás  |
|---------|---------|
|[Valós idejű keresési javaslatok](concepts/search-for-entities.md#suggest-search-terms-with-the-bing-autosuggest-api)     | Olyan keresési javaslatokat adhat meg, amelyek legördülő listaként jeleníthetők meg a felhasználók típusaként.       | 
| [Alapentitás](concepts/search-for-entities.md#the-bing-entity-search-api-response)  | Több entitás beolvasása több lehetséges jelentéssel rendelkező lekérdezésekhez. |
| [Helyek keresése](concepts/search-for-entities.md#find-places) | Információk keresése és visszaküldése a helyi vállalkozások és entitások számára  |

## <a name="workflow"></a>Munkafolyamat

A Bing Entity Search API egy REST-alapú webszolgáltatás, így könnyen hívható bármely programozási nyelvről, amely HTTP-kéréseket tesz elérhetővé, és értelmezi a JSON-t. A szolgáltatást a REST API vagy az SDK használatával is használhatja.

1. Hozzon létre egy [Cognitive Services API-fiókot](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account), amely hozzáféréssel rendelkezik a Bing Search API-khoz. Ha nem rendelkezik Azure-előfizetéssel, ingyenesen [létrehozhat egyet](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).
2. Kérelem küldése az API-nak érvényes keresési lekérdezéssel.
3. Az API válaszának feldolgozásához elemezze a visszaadott JSON-üzenetet.

## <a name="next-steps"></a>További lépések

* Próbálja ki a Bing Entity Search API [interaktív bemutatóját](https://azure.microsoft.com/services/cognitive-services/bing-entity-search-api/) . 
* Az első kérés gyors megkezdéséhez próbáljon [ki egy rövid](quickstarts/csharp.md)útmutatót.
* A [Bing Entity Search API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference) -hivatkozás szakasza.
* A [Bing használati és megjelenítési követelményei](./use-display-requirements.md) a Bing Search API-k használatával kapott tartalmak és információk elfogadható használatát határozzák meg.
