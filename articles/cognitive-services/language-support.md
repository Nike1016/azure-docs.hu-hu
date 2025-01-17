---
title: Nyelvi támogatás
titleSuffix: Azure Cognitive Services
description: Az Azure Cognitive Services lehetővé teszi alkalmazások összeállítását, amelyek lásd, hallgassa meg, beszéljen a és a felhasználók megismerése. Ezek a szolgáltatások között több mint három tucat nyelvet támogat, így a felhasználók természetes módon kommunikálnak az alkalmazással.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: article
ms.date: 06/16/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: cd31215f578062548c67f8d14e724dc49f73b21c
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2019
ms.locfileid: "68775004"
---
# <a name="natural-language-support-for-azure-cognitive-services"></a>Természetes nyelvi támogatás az Azure Cognitive Services

Az Azure Cognitive Services lehetővé teszi alkalmazások összeállítását, amelyek lásd, hallgassa meg, beszéljen a és a felhasználók megismerése. Ezek a szolgáltatások között több mint három tucat nyelvet támogat, így a felhasználók természetes módon kommunikálnak az alkalmazással.

Ez a cikk két szakaszra van osztva. Az első Azure Cognitive Services különböző gyakran támogatott core nyelvek listája. A második szakasz az minden további nyelvi támogatás összpontosít. Nyelvi rendelkezésre állása ország/régió és piaci eltérőek lehetnek.

## <a name="core-languages"></a>Core nyelvek

Core nyelvek között az Azure Cognitive Services támogatottak:

* kínai
* Angol
* francia
* német
* olasz
* japán
* Koreai ¹
* portugál
* spanyol

> [!NOTE]
> ¹ LUIS és Video Indexer: A beszédfelismerési szöveg nem támogatott.

## <a name="additional-language-availability-by-service"></a>További nyelvi szolgáltatás rendelkezésre állása

Ezek a táblák jelölje ki a nyelvet rendelkezésre állási szolgáltatási kategóriánként; kizárt Core nyelveket. Az alábbi hivatkozások segítségével további nyelvi támogatás és ország/régió és piaci rendelkezésre állását.

### <a name="vision"></a>Látás

| | arab | bolgár | katalán | horvát | cseh | dán | holland | észt | finn | görög | hindi | magyar | izlandi | indonéz | lett | litván | maláj | norvég | lengyel | román | orosz | szerb | szlovák | szlovén | svéd | tamil | thai | török | ukrán | vietnami |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| [Computer Vision: OCR](https://docs.microsoft.com/azure/cognitive-services/computer-vision/faq) | :heavy_check_mark: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | : heavy_minus_sign: | :heavy_check_mark: | :heavy_check_mark: | : heavy_minus_sign: | :heavy_check_mark: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | : heavy_minus_sign: | :heavy_check_mark: | : heavy_minus_sign: | : heavy_minus_sign: | :heavy_check_mark: | : heavy_minus_sign: | : heavy_minus_sign: |
| [Video Indexer: Beszéd – szöveg](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview) | :heavy_check_mark: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | :heavy_check_mark: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: |
| [Video Indexer: Beszéd fordítása](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | : heavy_minus_sign: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |

### <a name="speech"></a>Beszéd

| | arab | bolgár | katalán | horvát | cseh | dán | holland | észt | finn | görög | hindi | magyar | izlandi | indonéz | lett | litván | maláj | norvég | lengyel | román | orosz | szerb | szlovák | szlovén | svéd | tamil | thai | török | ukrán | vietnami |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| [Egyéni beszédfelismerés](https://docs.microsoft.com/azure/cognitive-services/custom-speech-service/customspeech-how-to-topics/cognitive-services-custom-speech-change-locale) | :heavy_check_mark: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | :heavy_check_mark: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | :heavy_check_mark: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | :heavy_check_mark: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | :heavy_check_mark: | : heavy_minus_sign: | : heavy_minus_sign: |
| [Beszédfelismerési szolgáltatás: Beszéd – szöveg](https://docs.microsoft.com/azure/cognitive-services/speech-service/supported-languages#speech-to-text) | :heavy_check_mark: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | :heavy_check_mark: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | :heavy_check_mark: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | :heavy_check_mark: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: |
| [Beszédfelismerési szolgáltatás: szöveg-, – beszédfelismerés](https://docs.microsoft.com/azure/cognitive-services/speech-service/supported-languages#text-to-speech) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | : heavy_minus_sign: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | : heavy_minus_sign: | :heavy_check_mark: | : heavy_minus_sign: | : heavy_minus_sign: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | : heavy_minus_sign: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | : heavy_minus_sign: | :heavy_check_mark: |
| [Beszédfelismerési szolgáltatás: Beszéd fordítása](https://docs.microsoft.com/azure/cognitive-services/speech-service/supported-languages#speech-translation) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | : heavy_minus_sign: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |

### <a name="language"></a>Nyelv

| | arab | bolgár | katalán | horvát | cseh | dán | holland | észt | finn | görög | hindi | magyar | izlandi | indonéz | lett | litván | maláj | norvég | lengyel | román | orosz | szerb | szlovák | szlovén | svéd | tamil | thai | török | ukrán | vietnami |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| [Bing helyesírás-ellenőrzés](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/bing-spell-check-supported-languages) | :heavy_check_mark: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | :heavy_check_mark: | :heavy_check_mark: | : heavy_minus_sign: | :heavy_check_mark: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | :heavy_check_mark: | :heavy_check_mark: | : heavy_minus_sign: | :heavy_check_mark: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | :heavy_check_mark: | : heavy_minus_sign: | : heavy_minus_sign: | :heavy_check_mark: | : heavy_minus_sign: | : heavy_minus_sign: |
| [Language Understanding (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/luis-supported-languages) | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | :heavy_check_mark: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | :heavy_check_mark: | : heavy_minus_sign: | : heavy_minus_sign: |
| [QnA Maker](https://docs.microsoft.com/azure/cognitive-services/qnamaker/overview/languages-supported) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Translator Text](https://docs.microsoft.com/azure/cognitive-services/translator/languages) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Szövegelemzés](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages) | :heavy_check_mark: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | : heavy_minus_sign: | :heavy_check_mark: | :heavy_check_mark: | : heavy_minus_sign: | :heavy_check_mark: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | :heavy_check_mark: | :heavy_check_mark: | : heavy_minus_sign: | :heavy_check_mark: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | :heavy_check_mark: | : heavy_minus_sign: | : heavy_minus_sign: | :heavy_check_mark: | : heavy_minus_sign: | : heavy_minus_sign: |

### <a name="search"></a>Keresés

| | arab | bolgár | katalán | horvát | cseh | dán | holland | észt | finn | görög | hindi | magyar | izlandi | indonéz | lett | litván | maláj | norvég | lengyel | román | orosz | szerb | szlovák | szlovén | svéd | tamil | thai | török | ukrán | vietnami |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| [Bing Web Search](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/supported-countries-markets) | :heavy_check_mark: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | :heavy_check_mark: | :heavy_check_mark: | : heavy_minus_sign: | :heavy_check_mark: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | :heavy_check_mark: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | :heavy_check_mark: | :heavy_check_mark: | : heavy_minus_sign: | :heavy_check_mark: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | :heavy_check_mark: | : heavy_minus_sign: | : heavy_minus_sign: | :heavy_check_mark: | : heavy_minus_sign: | : heavy_minus_sign: |
| [Bing Image Search](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/supported-countries-markets) | :heavy_check_mark: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | :heavy_check_mark: | :heavy_check_mark: | : heavy_minus_sign: | :heavy_check_mark: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | :heavy_check_mark: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | :heavy_check_mark: | :heavy_check_mark: | : heavy_minus_sign: | :heavy_check_mark: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | :heavy_check_mark: | : heavy_minus_sign: | : heavy_minus_sign: | :heavy_check_mark: | : heavy_minus_sign: | : heavy_minus_sign: |
| [Bing News Search](https://docs.microsoft.com/azure/cognitive-services/bing-news-search/supported-countries-markets) | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | :heavy_check_mark: | :heavy_check_mark: | : heavy_minus_sign: | :heavy_check_mark: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | :heavy_check_mark: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | :heavy_check_mark: | : heavy_minus_sign: | : heavy_minus_sign: | :heavy_check_mark: | : heavy_minus_sign: | : heavy_minus_sign: |
| [Bing Autosuggest](https://docs.microsoft.com/azure/cognitive-services/Bing-Autosuggest/bing-autosuggest-supported-languages) | :heavy_check_mark: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | :heavy_check_mark: | :heavy_check_mark: | : heavy_minus_sign: | :heavy_check_mark: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | :heavy_check_mark: | :heavy_check_mark: | : heavy_minus_sign: | :heavy_check_mark: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | :heavy_check_mark: | : heavy_minus_sign: | : heavy_minus_sign: | :heavy_check_mark: | : heavy_minus_sign: | : heavy_minus_sign: |
| [Bing Visual Search](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/supported-countries-markets) | :heavy_check_mark: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | :heavy_check_mark: | :heavy_check_mark: | : heavy_minus_sign: | :heavy_check_mark: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | :heavy_check_mark: | : heavy_minus_sign: | :heavy_check_mark: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | :heavy_check_mark: | : heavy_minus_sign: | : heavy_minus_sign: | :heavy_check_mark: | : heavy_minus_sign: | : heavy_minus_sign: |
| [Bing Custom Search](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/supported-countries-markets) | :heavy_check_mark: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | :heavy_check_mark: | :heavy_check_mark: | : heavy_minus_sign: | :heavy_check_mark: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | :heavy_check_mark: | :heavy_check_mark: | : heavy_minus_sign: | :heavy_check_mark: | : heavy_minus_sign: | : heavy_minus_sign: | : heavy_minus_sign: | :heavy_check_mark: | : heavy_minus_sign: | : heavy_minus_sign: | :heavy_check_mark: | : heavy_minus_sign: | : heavy_minus_sign: |

### <a name="decision"></a>Döntés

| | arab | bolgár | katalán | horvát | cseh | dán | holland | észt | finn | görög | hindi | magyar | izlandi | indonéz | lett | litván | maláj | norvég | lengyel | román | orosz | szerb | szlovák | szlovén | svéd | tamil | thai | török | ukrán | vietnami |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| [Content Moderator: Szöveg szűrése](https://docs.microsoft.com/azure/cognitive-services/content-moderator/text-moderation-api-languages) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |

A következő Cognitive Services a Language agnosztikus, és nem rendelkezik a nyelvtől függően korlátozásokkal.

* [Személyre szabás (előzetes verzió)](https://docs.microsoft.com/azure/cognitive-services/personalizer/)
* [Anomália detektor (előzetes verzió)](https://docs.microsoft.com/azure/cognitive-services/anomaly-detector/overview)


## <a name="see-also"></a>Lásd még

* [Mi a Cognitive Services?](welcome.md)
* [Fiók létrehozása](cognitive-services-apis-create-account.md)
