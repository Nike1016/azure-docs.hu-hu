---
title: Érzelmek elemzése az Azure Text Analytics REST API használatával Cognitive Services
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan derítheti fel a véleményét a Text Analytics REST API használatával.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 07/30/2019
ms.author: aahi
ms.openlocfilehash: 19654a4902ae64e5de63ffc93a8d143cc518e254
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68697734"
---
# <a name="example-detect-sentiment-with-text-analytics"></a>Példa: Érzelmek észlelése Text Analytics

Az [Azure HANGULATELEMZÉS API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) kiértékeli a szövegbevitelt, és az egyes dokumentumokhoz tartozó hangulati pontszámot adja vissza. A pontszámok 0 (negatív) és 1 (pozitív) közé esnek.

Ez a funkció akkor hasznos, ha a közösségi média, a vevő értékelések és a vitafórumokon kell felismeri a pozitív és negatív hangulatokat. A tartalmat Ön kapja meg. A szolgáltatás a modelleket és a betanítási adattípusokat is megadja.

A Hangulatelemzés API jelenleg az angol, a német, a spanyol és a francia nyelveket támogatja. Más nyelvek előzetes verzióban érhetők el. További információk: [Támogatott nyelvek](../text-analytics-supported-languages.md).

> [!TIP]
> Az Azure Text Analytics API egy Linux-alapú Docker-tároló képet is biztosít az érzelmek elemzéséhez, így [a Text Analytics tárolót](text-analytics-how-to-install-containers.md) az adatokhoz közelebb is telepítheti és futtathatja.

## <a name="concepts"></a>Alapelvek

A Text Analytics gépi tanulási osztályozó algoritmussal generálja a 0 és 1 közötti hangulatpontszámot. Az 1-hez közeli értékek pozitív, míg a 0-hoz közeliek negatív hangulatot jelölnek. A modellt nagy mennyiségű hangulattársításos szöveggel előre betanították. Jelenleg nem lehet saját betanítási adatait megadnia. A modell a módszerek kombinációját használja a szöveges elemzés során. A módszerek közé tartoznak a szövegek feldolgozása, a beszédfelismerési elemzés, a Word elhelyezése és a Word-társítások. Az algoritmussal kapcsolatos további információk: [A Text Analytics bemutatása](https://blogs.technet.microsoft.com/machinelearning/2015/04/08/introducing-text-analytics-in-the-azure-ml-marketplace/).

A hangulatelemzés a teljes dokumentum történik, szemben azzal, amikor a szöveg egy konkrét eleméhez gyűjtjük ki a hangulatot. A gyakorlatban van olyan tendencia, hogy a pontozás pontossága javuljon, ha a dokumentumok egy vagy két mondatot tartalmaznak, nem pedig egy nagy blokkot. Az objektivitás megfelelőségvizsgálati fázis során a modell meghatározza, hogy a dokumentum egészében objektív-e vagy hangulatot tartalmaz. Egy olyan dokumentum, amely többnyire objektív, nem halad az észlelési fázisra, ami egy 0,50 pontszámot eredményez, és nincs szükség további feldolgozásra. A folyamat során folytatott dokumentumok esetében a következő fázis a 0,50-es vagy újabb pontszámot generálja. A pontszám a dokumentumban észlelt érzelmek szintjétől függ.

## <a name="preparation"></a>Előkészítés

Az érzelmek elemzése nagyobb minőségi eredményt eredményez, ha kisebb mennyiségű szöveget ad hozzá. Ez a ellentétes a kulcsszókereséssel, amely nagyobb mennyiségű szöveg esetén teljesít jobban. A legjobb eredmény elérése érdekében célszerű a bemenetet ennek megfelelően átszervezni.

A következő formátumú JSON-dokumentumok szükségesek: AZONOSÍTÓ, szöveg és nyelv.

A dokumentum méretének 5 120 karakternél rövidebbnek kell lennie a dokumentumban. Egy gyűjteményhez legfeljebb 1 000 elem (azonosító) tartozhat. A kollekció elküldése a kérelem törzsében történik. Az alábbi minta egy példa arra, hogy milyen tartalmakat küldhet az érzelmek elemzéséhez:

```json
    {
        "documents": [
            {
                "language": "en",
                "id": "1",
                "text": "We love this trail and make the trip every year. The views are breathtaking and well worth the hike!"
            },
            {
                "language": "en",
                "id": "2",
                "text": "Poorly marked trails! I thought we were goners. Worst hike ever."
            },
            {
                "language": "en",
                "id": "3",
                "text": "Everyone in my family liked the trail but thought it was too challenging for the less athletic among us. Not necessarily recommended for small children."
            },
            {
                "language": "en",
                "id": "4",
                "text": "It was foggy so we missed the spectacular views, but the trail was ok. Worth checking out if you are in the area."
            },
            {
                "language": "en",
                "id": "5",
                "text": "This is my favorite trail. It has beautiful views and many places to stop and rest"
            }
        ]
    }
```

## <a name="step-1-structure-the-request"></a>1\. lépés: A kérelem szerkezete

A kérelem meghatározásával kapcsolatos további információkért lásd [a Text Analytics API](text-analytics-how-to-call-api.md)meghívása című témakört. A következő pontokat a kényelem kedvéért itt megismételjük:

+ Hozzon létre egy POST-kérelmet. A kérelem API-dokumentációjának áttekintéséhez tekintse meg a [HANGULATELEMZÉS API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9)-t.

+ Állítsa be a HTTP-végpontot az Text Analytics Azure-beli erőforrás vagy egy példányos [text Analytics tároló](text-analytics-how-to-install-containers.md)használatával. Tartalmaznia kell az `/sentiment` erőforrást: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/sentiment`.

+ Állítsa be a kérelem fejlécét, hogy tartalmazza a Text Analytics műveletekhez tartozó [hozzáférési kulcsot](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) .

+ A kérelem törzsében adja meg az elemzéshez előkészített JSON-dokumentum kollekciót.

> [!Tip]
> Használja [](text-analytics-how-to-call-api.md) a Poster szolgáltatást, vagy nyissa meg a [dokumentáció](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) **API-tesztelési konzolját** a kérelem felépítéséhez és a szolgáltatáshoz való közzétételéhez.

## <a name="step-2-post-the-request"></a>2\. lépés: A kérelem közzététele

Az elemzés a kérelem megkapásakor történik meg. További információ a másodpercenként elküldhető kérelmek méretéről és számáról: az adatkorlátozások szakasz [](../overview.md#data-limits) az áttekintésben.

Ne felejtse, hogy a szolgáltatás állapot nélküli. A fiókban nem tárol semmilyen adatot. Az eredményeket azonnal visszaadja a válaszban.


## <a name="step-3-view-the-results"></a>3\. lépés: Eredmények megtekintése

Az a hangulat-elemző a szöveget túlnyomórészt pozitívként vagy negatívként osztályozza. 0 és 1 közötti pontszámot rendel hozzá. A 0,5 közeli értékek semlegesek vagy határozatlanok. A 0,5-ös pontszám semlegességet jelez. Ha egy sztringet nem lehet elemezni az érzelmekkel kapcsolatban, vagy nincs hangulata, a pontszám mindig 0,5 pontosan. Ha például egy spanyol nyelvű szöveget ad meg angol nyelvi kóddal, a pontszám 0,5 lesz.

A kimenetet visszaadása azonnali. Az eredményeket egy olyan alkalmazásba is továbbíthatja, amely fogadja a JSON-t, vagy mentse a kimenetet egy fájlba a helyi rendszeren. Ezután importálja a kimenetet egy olyan alkalmazásba, amelyet az adatrendezéshez, kereséshez és kezeléshez használhat.

A következő példa a dokumentum-gyűjtemény válaszát mutatja be ebben a cikkben:

```json
    {
        "documents": [
            {
                "score": 0.9999237060546875,
                "id": "1"
            },
            {
                "score": 0.0000540316104888916,
                "id": "2"
            },
            {
                "score": 0.99990355968475342,
                "id": "3"
            },
            {
                "score": 0.980544924736023,
                "id": "4"
            },
            {
                "score": 0.99996328353881836,
                "id": "5"
            }
        ],
        "errors": []
    }
```

## <a name="sentiment-analysis-v3-public-preview"></a>Hangulatelemzés v3 nyilvános előzetes verzió

A [Hangulatelemzés következő verziója](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-preview/operations/56f30ceeeda5650db055a3c9) már elérhető a nyilvános előzetes verzióban. Jelentős mértékben javítja az API szövegének és pontozásának pontosságát és részletességét.

> [!NOTE]
> * Az Hangulatelemzés v3 kérelem formátuma [](../overview.md#data-limits) és adatkorlátja megegyezik az előző verzióval.
> * Most Hangulatelemzés v3:
>    * Jelenleg csak az angol nyelvet támogatja.
>    * A következő régiókban érhető el: `Central US`, `Central Canada`és `East Asia`.

|Funkció |Leírás  |
|---------|---------|
|Javított pontosság     | Az előző verziókhoz képest jelentősen javult a pozitív, semleges, negatív és vegyes hangulat szöveges dokumentumokban való észlelése.           |
|A dokumentum és a mondatok szintjének értékelésének pontszáma     | A hangulatot egy dokumentumon és annak egyes mondatain belül is észleli. Ha a dokumentum több mondatból áll, akkor minden mondathoz külön hangulatpontszám lesz rendelve.         |
|A hangulat kategóriája és pontszáma     | Az API mostantól az érzelmi pontszám mellett visszaadja a szöveg hangulati kategóriáit. A kategóriák a `positive`következők `negative` `neutral`:,, `mixed`és.       |
| Továbbfejlesztett kimenet | Az érzelmek elemzése mostantól a teljes szöveges dokumentumra és az egyéni mondatokra vonatkozó információkat is visszaadja. |

### <a name="sentiment-labeling"></a>Érzelmek címkézése

A Hangulatelemzés v3 a pontszámokat és címkéket a mondatok és a dokumentumok szintjén adhatja vissza. A pontszámok és a `positive`címkék `negative`a következők `neutral`:, és. A dokumentum szintjén a `mixed` "hangulat" címkét is vissza lehet adni. A dokumentum hangulatát a mondatok pontszámának összesítésével határozzuk meg.

| Mondat hangulata                                                        | Visszaadott dokumentum címkéje |
|---------------------------------------------------------------------------|----------------|
| Legalább egy pozitív mondat és a mondatok többi része semleges. | `positive`     |
| Legalább egy negatív mondat és a mondatok többi része semleges.  | `negative`     |
| Legalább egy negatív mondatot és legalább egy pozitív mondatot.         | `mixed`        |
| Minden mondat semleges.                                                 | `neutral`      |

### <a name="sentiment-analysis-v3-example-request"></a>Példa Hangulatelemzés v3-kérelemre

A következő JSON egy példa a Hangulatelemzés új verziójára tett kérelemre. A kérelem formázása megegyezik az előző verzióval:

```json
    {
        "documents": [
        {
            "language": "en",
            "id": "1",
            "text": "Hello world. This is some input text that I love."
        },
        {
            "language": "en",
            "id": "2",
            "text": "It's incredibly sunny outside! I'm so happy."
        }
        ],
    }
```

### <a name="sentiment-analysis-v3-example-response"></a>Példa Hangulatelemzés v3-es válaszra

Míg a kérelem formátuma megegyezik az előző verzióval, a válasz formátuma megváltozott. A következő JSON az API új verziójának válasza:

```json
    {
        "documents": [
            {
                "id": "1",
                "sentiment": "positive",
                "documentScores": {
                    "positive": 0.98570585250854492,
                    "neutral": 0.0001625834556762,
                    "negative": 0.0141316400840878
                },
                "sentences": [
                    {
                        "sentiment": "neutral",
                        "sentenceScores": {
                            "positive": 0.0785155147314072,
                            "neutral": 0.89702343940734863,
                            "negative": 0.0244610067456961
                        },
                        "offset": 0,
                        "length": 12
                    },
                    {
                        "sentiment": "positive",
                        "sentenceScores": {
                            "positive": 0.98570585250854492,
                            "neutral": 0.0001625834556762,
                            "negative": 0.0141316400840878
                        },
                        "offset": 13,
                        "length": 36
                    }
                ]
            },
            {
                "id": "2",
                "sentiment": "positive",
                "documentScores": {
                    "positive": 0.89198976755142212,
                    "neutral": 0.103382371366024,
                    "negative": 0.0046278294175863
                },
                "sentences": [
                    {
                        "sentiment": "positive",
                        "sentenceScores": {
                            "positive": 0.78401315212249756,
                            "neutral": 0.2067587077617645,
                            "negative": 0.0092281140387058
                        },
                        "offset": 0,
                        "length": 30
                    },
                    {
                        "sentiment": "positive",
                        "sentenceScores": {
                            "positive": 0.99996638298034668,
                            "neutral": 0.0000060341349126,
                            "negative": 0.0000275444017461
                        },
                        "offset": 31,
                        "length": 13
                    }
                ]
            }
        ],
        "errors": []
    }
```

### <a name="example-c-code"></a>Példa C# kódja

Megtalálhatja a githubon Hangulatelemzés ezen verzióját meghívó C# alkalmazást. [](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/dotnet/Language/SentimentV3.cs)

## <a name="summary"></a>Összegzés

Ebben a cikkben az Text Analytics az Azure Cognitive Services-ban való használatával megtanulta az érzelmek elemzéséhez szükséges fogalmakat és munkafolyamatokat. Összegezve:

+ A [HANGULATELEMZÉS API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) a kiválasztott nyelvekhez érhető el.
+ A kérelem törzsében található JSON-dokumentumok közé tartozik az azonosító, a szöveg és a nyelvi kód.
+ A post kérelem `/sentiment` a végponthoz egy személyre szabott [hozzáférési kulccsal és egy](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) , az előfizetéséhez érvényes végpontot használ.
+ A válasz kimenete, amely az egyes dokumentumok AZONOSÍTÓinak hangulati pontszámát tartalmazza, továbbítható bármely olyan alkalmazásnak, amely elfogadja a JSON-t. Az alkalmazások közé tartoznak például az Excel és a Power BI, hogy csak néhányat említsünk.

## <a name="see-also"></a>Lásd még

 [Text Analytics áttekintése](../overview.md) [Gyakori kérdések (GYIK)](../text-analytics-resource-faq.md)</br>
 [Text Analytics termékoldala](//go.microsoft.com/fwlink/?LinkID=759712)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Kulcsszavak kinyerése](text-analytics-how-to-keyword-extraction.md)
