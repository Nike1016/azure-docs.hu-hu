---
title: Nyelvi támogatás – Computer Vision
titleSuffix: Azure Cognitive Services
description: Computer Vision szolgáltatások által támogatott természetes nyelvek listája.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.openlocfilehash: 516d21bc69bbc20f924a3bdf39eda7245fc08a28
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68882972"
---
# <a name="language-support-for-computer-vision"></a>A Computer Vision nyelvi támogatása

A Computer Vision egyes funkciói több nyelvet támogatnak; az itt nem említett szolgáltatások csak az angol nyelvet támogatják.

## <a name="text-recognition"></a>Szövegfelismerés

A Computer Vision számos nyelven képes felismerni a szöveget. Az [OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) API különböző nyelveket támogat, míg az [olvasási](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) API és a [szövegfelismerés](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) API csak az angol nyelvet támogatja. További információ a funkcióról és az egyes API-k előnyeiről: a [nyomtatott és a kézírásos szöveg felismerése](concept-recognizing-text.md) .

Az OCR automatikusan észleli a bemeneti anyag nyelvét, így nincs szükség a nyelvi kód megadására az API-hívásban. A nyelvi kódokat azonban mindig a JSON-válaszban lévő `"language"` csomópont értékeként adja vissza a rendszer.

|Nyelv| Nyelvkód | OCR API |
|:-----|:----:|:-----:|
|arab | `ar`|✔ |
|kínai (egyszerűsített) | `zh-Hans`|✔ |
|kínai (hagyományos) | `zh-Hant`|✔ |
|cseh | `cs` |✔ |
|dán | `da` |✔ |
|holland | `nl` |✔ |
|Angol | `en` |✔ |
|finn | `fi` |✔ |
|francia | `fr` |✔ |
|német | `de` |✔ |
|görög | `el` |✔ |
|magyar | `hu` |✔ |
|olasz | `it` |✔ |
|japán | `ja` |✔ |
|koreai | `ko` |✔ |
|norvég | `nb` |✔ |
|lengyel | `pl` |✔ |
|portugál | `pt` |✔ |
|román | `ro` |✔ |
|orosz | `ru` |✔ |
|szerb (cirill betűs) | `sr-Cyrl` |✔ |
|szerb (latin betűs) | `sr-Latn` |✔ |
|szlovák | `sk` |✔ |
|spanyol | `es` |✔ |
|svéd | `sw` |✔ |
|török | `tr` |✔ |

## <a name="image-analysis"></a>Rendszerkép elemzése

Az [elemzés-rendszerkép](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API egyes műveletei más nyelveket is visszaadhatnak a `language` lekérdezési paraméterrel megadottak szerint. A többi művelet angol nyelven adja vissza az eredményeket, függetlenül attól, hogy milyen nyelven van megadva, mások pedig kivételt képeznek a nem támogatott nyelvek esetében. A műveletek a és `visualFeatures` `details` a lekérdezési paraméterekkel vannak megadva. az [áttekintést](home.md) a képelemzéssel elvégezhető műveletek listájáról tekintheti meg.

|Nyelv | Nyelvkód | Categories | Tags | Leírás | Felnőtt tartalom | Márkák | Szín | Arcok | Képtípus | Objektumok | Hírességek | Arcrész |
|:---|:---:|:----:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|kínai | `zh`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Angol | `en`   | ✔ | ✔| ✔|✔|✔|✔|✔|✔|✔|✔|✔|
|japán | `ja`   | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|portugál | `pt` | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|spanyol | `es`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|

## <a name="next-steps"></a>További lépések

Ismerkedjen meg az útmutatóban említett Computer Vision-funkciókkal.

* [Helyi rendszerkép (REST) elemzése](./quickstarts/csharp-analyze.md)
* [Nyomtatott szöveg kinyerése (REST)](./quickstarts/csharp-print-text.md)