---
title: Mi a Bing Spell Check API?
titleSuffix: Azure Cognitive Services
description: Ismerje meg a Bing Spell Check API, amely a gépi tanulást és a statisztikai gépi fordítást használja a környezetfüggő helyesírás-ellenőrzéshez.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: overview
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: 996db9690d19cc40c3963922d4edb3b59469752b
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68514802"
---
# <a name="what-is-the-bing-spell-check-api"></a>Mi a Bing Spell Check API?

A Bing Spell Check API lehetővé teszi a szöveg kontextusbeli nyelvtanának és helyesírás-ellenőrzésének elvégzését. Habár a legtöbb helyesírás-ellenőrző szótár alapú szabálykészlet alapján használható, a Bing helyesírás-ellenőrzője a gépi tanulást és a statisztikai gépi fordítást használja a pontos és a kontextusbeli javítások biztosításához. 

## <a name="features"></a>Szolgáltatások


|  |  |
|---------|---------|
|Több helyesírás-ellenőrzési mód     | Több helyesírás-ellenőrzési mód lehetővé teszi a nyelvtani és/vagy helyesírási hibák javítását. |
|A szleng és az informális nyelvi felismerés     | A szövegben használt általános kifejezések és informális kifejezések felismerése.         |
|Megkülönböztetés a hasonló szavak között     | Megtalálhatja a helyes használatot a szavak között, amelyek hasonlóak, de különböznek a jelentésekben (például "lásd" és "Sea")        |
|Márka, cím és népszerű használati támogatás     | Új márkák, címek és más népszerű kifejezések felismerése |

## <a name="workflow"></a>Munkafolyamat

A Bing Spell Check API könnyen hívható bármely programozási nyelvről, amely HTTP-kéréseket tesz elérhetővé, és értelmezi a JSON-válaszokat. A szolgáltatás a REST API vagy a Bing Spell Check SDK-k használatával érhető el. 

1. Hozzon létre egy [Cognitive Services API-fiókot](../cognitive-services-apis-create-account.md), amely hozzáféréssel rendelkezik a Bing Search API-khoz. Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egy ingyenes fiókot. 
2. Kérelem küldése a Bing Web Search APInak.
3. A JSON-válasz elemzése

## <a name="next-steps"></a>További lépések

Először is próbálja ki a Bing Spell Check Search API [interaktív bemutatóját](https://azure.microsoft.com/services/cognitive-services/spell-check/) , és tekintse meg, hogyan ellenőrizhetők a különböző szövegek.

Amikor kész meghívni az API-t, hozzon létre egy [Cognitive Services API-fiókot](../../cognitive-services/cognitive-services-apis-create-account.md). Ha nem rendelkezik Azure-előfizetéssel, ingyenesen [létrehozhat egyet](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).
