---
title: Rövid útmutató – lekérdezés küldése a Bing helyi üzleti keresési API-nak a Pythonban
titleSuffix: Azure Cognitive Services
description: Ebből a cikkből megkezdheti a Bing helyi üzleti keresési API használatát a Pythonban.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: quickstart
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: c6da3c9a169f3b6e5885499d3a7bc5347902782e
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423325"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-in-python"></a>Gyors útmutató: Lekérdezés küldése a Bing helyi üzleti keresési API-nak a Pythonban

Ezzel a rövid útmutatóval megkezdheti a kérések küldését a Bing local Business Search API számára, amely egy Azure kognitív szolgáltatás. Habár ez az egyszerű alkalmazás Pythonban íródott, az API egy REST-alapú webszolgáltatás, amely kompatibilis a HTTP-kérelmeket és a JSON-elemzést lehetővé tevő programozási nyelvekkel.

Ez a példában szereplő alkalmazás a keresési lekérdezés `hotel in Bellevue`API-ból érkező helyi válaszüzeneteket kéri le.

## <a name="prerequisites"></a>Előfeltételek

* [Python](https://www.python.org/) 2. x vagy 3. x
 
A Bing API-kkal [Cognitive Services API](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) -fiókkal kell rendelkeznie. Az [ingyenes próbaverzió](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) elegendő ehhez a rövid útmutatóhoz. Használja az ingyenes próbaverzió által biztosított hozzáférési kulcsot.  Lásd még: [a Cognitive Services díjszabás – keresési Bing-API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="run-the-complete-application"></a>A teljes alkalmazás futtatása

A következő kód honosított eredményeket kap. Implementálására a következő lépésekben kerül sor:
1. Deklarálja a változókat a végpont gazdagép és útvonal szerinti megadásához.
2. A lekérdezési paramétert kell megadni. 
3. Adja meg a kérelmet létrehozó keresési függvényt, és adja meg a OCP-APIM-Subscription-Key fejlécet.
4. Állítsa be a OCP-APIM-Subscription-Key fejlécet. 
5. Hozza meg a kapcsolatokat, és küldje el a kérést.
6. Megjeleníti a JSON-eredményeket.

Az útmutatóban használt teljes kód a következő:

```
import http.client, urllib.parse
import json

# Replace the subscriptionKey string value with your valid subscription key.
subscriptionKey = 'YOUR-SUBSCRIPTION-KEY'

host = 'api.cognitive.microsoft.com/bing'
path = '/v7.0/localbusinesses/search'

query = 'restaurant in Bellevue'

params = '?q=' + urllib.parse.quote (query) + '&mkt=en-us'

def get_local():
    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
    conn = http.client.HTTPSConnection (host)
    conn.request ("GET", path + params, None, headers)
    response = conn.getresponse ()
    return response.read ()

result = get_local()
print (json.dumps(json.loads(result), indent=4))

```

## <a name="next-steps"></a>További lépések
- [Helyi üzleti keresés – Java rövid útmutató](local-search-java-quickstart.md)
- [Helyi üzleti keresés C# – rövid útmutató](local-quickstart.md)
- [Helyi üzleti keresési csomópont rövid útmutatója](local-search-node-quickstart.md)
