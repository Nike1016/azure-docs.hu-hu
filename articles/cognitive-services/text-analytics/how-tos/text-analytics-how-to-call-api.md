---
title: Szövegelemzési API hívása
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan hívhatja meg a Text Analytics REST API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: aahi
ms.openlocfilehash: 14d3864f654dac42566441b3729de0cf88482295
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68697867"
---
# <a name="how-to-call-the-text-analytics-rest-api"></a>A Text Analytics meghívása REST API

A **text Analytics API** hívások http post/Get hívásokat végeznek, amelyek bármilyen nyelven megadhatók. Ebben a cikkben a REST és a [Poster](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop) használatával mutatjuk be a főbb fogalmakat.

Minden kérelemnek tartalmaznia kell a hozzáférési kulcsot és egy HTTP-végpontot. A végpont meghatározza a regisztráció során kiválasztott régiót, a szolgáltatás URL-címét és a kérelemben használt erőforrást: `sentiment` `languages`, `keyphrases`,, és `entities`. 

Ne felejtse el, hogy Text Analytics állapot nélküli, ezért nincsenek felügyelhető adategységek. A rendszer feltölti és elemzi a szöveget, és azonnal visszaadja az eredményeket a hívó alkalmazásnak.

> [!Tip]
> Ha szeretné megtekinteni, hogyan működik az API, POST kéréseket küldhet a beépített **API**-tesztelési konzolról, amely bármely [API-doc-lapon](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6)elérhető. Nincs beállítva beállítás, és az egyetlen követelmény, hogy a kérelembe beillesszen egy hozzáférési kulcsot és a JSON-dokumentumokat. 

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

<a name="json-schema"></a>

## <a name="json-schema-definition"></a>JSON-séma definíciója

A bemenetnek JSON-nek kell lennie nyers strukturálatlan szövegben. Az XML nem támogatott. A séma egyszerű, amely az alábbi listában leírt elemekből áll. 

Jelenleg minden Text Analytics művelethez ugyanazokat a dokumentumokat lehet elküldeni: a véleményeket, a kulcsfontosságú kifejezéseket, a nyelvi észlelést és az entitások azonosítását. (A séma várhatóan változó lesz az egyes elemzésekhez a jövőben.)

| Elem | Érvényes értékek | Kötelező? | Használat |
|---------|--------------|-----------|-------|
|`id` |Az adattípus karakterlánc, de a gyakorlatban a dokumentumok azonosítói általában egész számnak számítanak. | Kötelező | A rendszer az Ön által megadott azonosítókat használja a kimenet felépítéséhez. A kérelemben szereplő minden egyes AZONOSÍTÓhoz a nyelvi kódok, a legfontosabb kifejezések és a hangulati pontszámok jönnek létre.|
|`text` | Strukturálatlan nyers szöveg, legfeljebb 5 120 karakter hosszú lehet. | Kötelező | A nyelvfelismerés a szöveg bármilyen nyelven kifejezhető. Az érzelmek elemzéséhez, a fő kifejezés kinyeréséhez és az entitás azonosításához a szövegnek [támogatott nyelven](../text-analytics-supported-languages.md)kell lennie. |
|`language` | 2 karakteres [ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) -kód [támogatott nyelvhez](../text-analytics-supported-languages.md) | Változó | Az érzelmek elemzéséhez, a kulcsfontosságú kifejezés kinyeréséhez és az entitások összekapcsolásához szükséges. a nyelvfelismerés nem kötelező. Nincs hiba, ha kizárják, de az elemzés a nélkül meggyengül. A nyelvi kódnak meg kell egyeznie `text` az Ön által megadott értékkel. |

A korlátokkal kapcsolatos további információkért tekintse meg a [text Analytics áttekintés > az adatkorlátok](../overview.md#data-limits)című témakört. 

## <a name="set-up-a-request-in-postman"></a>Kérelem beállítása a Poster-ban

A szolgáltatás legfeljebb 1 MB méretű kérést fogad el. Ha a Poster-t (vagy egy másik webes API-teszt eszközt) használja, állítsa be a végpontot, hogy tartalmazza a használni kívánt erőforrást, és adja meg a hozzáférési kulcsot a kérelem fejlécében. Minden művelethez hozzá kell fűzni a megfelelő erőforrást a végponthoz. 

1. A Poster-ben:

   + A kérelem típusaként válassza a **post** lehetőséget.
   + Illessze be a portál lapról másolt végpontot.
   + Egy erőforrás hozzáfűzése.

   Az erőforrás-végpontok a következők lehetnek (a régió változhat):

   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/sentiment`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/keyPhrases`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/languages`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/entities`

2. A három kérelem fejlécének beállítása:

   + `Ocp-Apim-Subscription-Key`: az Azure Portaltól kapott hozzáférési kulcs.
   + `Content-Type`: alkalmazás/JSON.
   + `Accept`: alkalmazás/JSON.

   A kérelemnek a következő képernyőképhez hasonlóan kell kinéznie, feltéve, hogy **/keyPhrases** -erőforrást használ.

   ![Képernyőkép kérése a végpontról és a fejlécekről](../media/postman-request-keyphrase-1.png)

4. Kattintson a **törzs** elemre, és válassza a **RAW** formátumot a formátumhoz.

   ![Képernyőkép kérése a törzs beállításaival](../media/postman-request-body-raw.png)

5. Illesszen be néhány JSON-dokumentumot olyan formátumban, amely érvényes a kívánt elemzéshez. Egy adott elemzéssel kapcsolatos további információkért tekintse meg az alábbi témaköröket:

  + [Nyelvfelismerés](text-analytics-how-to-language-detection.md)  
  + [Fő kifejezés kibontása](text-analytics-how-to-keyword-extraction.md)  
  + [Hangulat elemzése](text-analytics-how-to-sentiment-analysis.md)  
  + [Entitások felismerése](text-analytics-how-to-entity-linking.md)  


6. A kérelem elküldéséhez kattintson a **Küldés** gombra. Az áttekinthető kérelmek számával kapcsolatos információkért tekintse meg az adatkorlátozások szakaszt az Áttekintés lapon. [](../overview.md#data-limits)

   A Poster esetében a válasz a következő ablakban, egyetlen JSON-dokumentumként jelenik meg, a kérelemben megadott összes dokumentum-AZONOSÍTÓhoz tartozó elemmel.

## <a name="see-also"></a>Lásd még 

 [A Text Analytics áttekintése](../overview.md)  
 [Gyakori kérdések (GYIK)](../text-analytics-resource-faq.md)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Nyelv felismerése](text-analytics-how-to-language-detection.md)
