---
title: DatetimeV2-előkészített entitások – LUIS
titleSuffix: Azure Cognitive Services
description: Ez a cikk datetimeV2 rendelkezik előre összeállított entitások információ a Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: c4e83ed15c2b15ccb3339ff775b08c8d2dab4c32
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932522"
---
# <a name="datetimev2-prebuilt-entity-for-a-luis-app"></a>DatetimeV2 előre összeépített entitása egy LUIS-alkalmazáshoz

A **datetimeV2** előre összeállított entitások kibontja a dátum és idő értékét. Ezek az értékek ügyfélprogramok használhat szabványos formában oldható fel. Amikor az utterance (kifejezés) dátum és idő, amely még nem fejeződött be, a LUIS tartalmaz _múltbeli és a jövőbeli értékek_ a végpont-válaszban. Az entitás már be van tanítva, mert nem kell való az alkalmazás leképezések datetimeV2 tartalmazó példa beszédmódok hozzáadása. 

## <a name="types-of-datetimev2"></a>DatetimeV2 típusai
A DatetimeV2 a felismerők [– text GitHub-](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-DateTime.yaml) adattárból kezelhető

## <a name="example-json"></a>Példa JSON-ban 
Az alábbi példa JSON-válasz egy `datetimeV2` altípusa entitás `datetime`. Más típusú datetimeV2 entitások példákért lásd [datetimeV2 az altípus](#subtypes-of-datetimev2)</a>.

```json
"entities": [
  {
    "entity": "8am on may 2nd 2019",
    "type": "builtin.datetimeV2.datetime",
    "startIndex": 0,
    "endIndex": 18,
    "resolution": {
      "values": [
        {
          "timex": "2019-05-02T08",
          "type": "datetime",
          "value": "2019-05-02 08:00:00"
        }
      ]
    }
  }
]
  ```

## <a name="json-property-descriptions"></a>JSON-tulajdonság leírása

|Tulajdonság neve |Typ vlastnosti és leírás|
|---|---|
|Entitás|**karakterlánc** – az utterance (kifejezés), dátum, idő, dátumtartomány vagy időtartomány típusú kinyert szöveget.|
|type|**karakterlánc** - egy, a [datetimeV2 az altípus](#subtypes-of-datetimev2)
|startIndex|**int** – az indexet a az utterance (kifejezés), amellyel az entitás kezdődik.|
|endIndex|**int** – az index az utterance (kifejezés), az entitás ér véget.|
|feloldás|Rendelkezik egy `values` tömb, amely rendelkezik egy, kettő vagy négy [feloldási értékét](#values-of-resolution).|
|vége|Egy idő vagy a dátumtartományt, felhasználónévként záróértéke `value`. Csak akkor használható, ha `type` van `daterange`, `timerange`, vagy `datetimerange`|

## <a name="subtypes-of-datetimev2"></a>A datetimeV2 altípus

A **datetimeV2** előre összeállított entitások az alábbi altípusainak, és a példák az egyes, az alábbi táblázat tartalmazza:
* `date`
* `time`
* `daterange`
* `timerange`
* `datetimerange`
* `duration`
* `set`

## <a name="values-of-resolution"></a>Feloldási értékét
* A tömb egy elemmel rendelkezik, ha a dátum vagy idő az utterance (kifejezés) teljesen megadott és egyértelmű.
* A tömb két elemet tartalmaz, nem egyértelmű datetimeV2 érték esetén. Félreérthetőség tartalmazza az adott évig, idő vagy időtartomány hiánya. Lásd: [nem egyértelmű dátumok](#ambiguous-dates) példákat. Ha az idő nem egyértelmű a de. vagy fog sérülni, mindkét értékek szerepelnek.
* A tömb négy elemmel rendelkezik, ha az utterance (kifejezés) félreérthetőség két olyan elemet tartalmaz. Ez magában foglalja, amelyek:
  * Dátum vagy dátumtartomány, amely nem egyértelmű helyrendszerszerepkörökre év
  * Egy idő- vagy nem egyértelmű reggel esetleges szabályozási hiányosságok elhárítását időtartomány or P.M. Ha például 3. április 3:00.

Egyes elemeinek a `values` tömb előfordulhat, hogy a következő mezőket: 

|Tulajdonság neve|Tulajdonságleírás|
|--|--|
|Timex|idő, dátum vagy dátumtartomány TIMEX formátuma a következő kifejezett a [ISO 8601 szabványnak](https://en.wikipedia.org/wiki/ISO_8601) és TimeML jazyce jegyzet TIMEX3 attribútumait. A jegyzet leírt a [TIMEX irányelvek](http://www.timeml.org/tempeval2/tempeval2-trial/guidelines/timex3guidelines-072009.pdf).|
|type|Az `datetime`altípus, amely a következő elemek egyike lehet: `date` `time`, `daterange`,, `timerange`, `datetimerange`,, `duration`, `set`.|
|value|**Nem kötelező.** Egy óó (idő) yyyy:MM:dd óó (datetime) formátumban yyyy:MM:dd (dátum), dátum és idő-objektumában. Ha `type` van `duration`, hány másodpercig (időtartam) értéke <br/> Csak akkor használható, ha `type` van `datetime` vagy `date`, `time`, vagy a "időtartama.|

## <a name="valid-date-values"></a>Érvényes dátumértéket

A **datetimeV2** dátumok között a következő tartományokra támogatja:

| Min | Max |
|----------|-------------|
| 1900. január 1-től.   | 2099. December 31-én. |

## <a name="ambiguous-dates"></a>Nem egyértelmű dátumok

Ha a dátuma a múltban vagy későbbi, a LUIS biztosít mindkét értéket. Ilyen például, az utterance (kifejezés), amely tartalmazza a hónap és év nélkül.  

Ha például adott "Május 2." utterance (kifejezés):
* Ha a mai dátum 2017 május 3., akkor a LUIS értékekként nyújt "2017-05-02" és "2018-05-02". 
* Ha a mai dátum 2017 május 1-től, LUIS biztosít mind a "2016-05-02", mind a "2017-05-02" értéket.

Az alábbi példa bemutatja a feloldás "május 2" entitás. Ez a megoldás feltételezi, hogy a mai dátum 2017 május 2. és 2018 május 1. között egy dátumot.
A mezők `X` a a `timex` mező a dátum részei, amely nincs explicit módon megadott ellenőrzőpontokon az utterance (kifejezés).

```json
  "entities": [
    {
      "entity": "may 2nd",
      "type": "builtin.datetimeV2.date",
      "startIndex": 0,
      "endIndex": 6,
      "resolution": {
        "values": [
          {
            "timex": "XXXX-05-02",
            "type": "date",
            "value": "2019-05-02"
          },
          {
            "timex": "XXXX-05-02",
            "type": "date",
            "value": "2020-05-02"
          }
        ]
      }
    }
  ]
```

## <a name="date-range-resolution-examples-for-numeric-date"></a>Dátum tartomány feloldási példák a numerikus dátuma

A `datetimeV2` entitás kibontja a dátum és idő tartományokat. A `start` és `end` mezőben adja meg a kezdő és a tartomány végéig. Az utterance (kifejezés) "Május 2. május 5-én a", a LUIS biztosít **daterange** is az aktuális évre, és a következő évre értékeit. Az a `timex` mező, a `XXXX` értékek azt jelzik, a kétértelműséget, az év. `P3D` azt jelzi, az adott időszakban három napig tart.

```json
"entities": [
    {
      "entity": "may 2nd to may 5th",
      "type": "builtin.datetimeV2.daterange",
      "startIndex": 0,
      "endIndex": 17,
      "resolution": {
        "values": [
          {
            "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
            "type": "daterange",
            "start": "2019-05-02",
            "end": "2019-05-05"
          }
        ]
      }
    }
  ]
```

## <a name="date-range-resolution-examples-for-day-of-week"></a>Dátum tartomány feloldási példák a hét napja

Az alábbi példa bemutatja, hogyan használja a LUIS **datetimeV2** feloldani az utterance (kifejezés) "Csütörtök, Tuesday". Ebben a példában az aktuális dátum későbbi, június 19-én. A LUIS tartalmaz **daterange** mindkét elé, és hajtsa végre az aktuális dátumot dátumtartományok értékeket.

```json
  "entities": [
    {
      "entity": "tuesday to thursday",
      "type": "builtin.datetimeV2.daterange",
      "startIndex": 0,
      "endIndex": 19,
      "resolution": {
        "values": [
          {
            "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
            "type": "daterange",
            "start": "2019-04-30",
            "end": "2019-05-02"
          }
        ]
      }
    }
  ]
```
## <a name="ambiguous-time"></a>Nem egyértelmű idő
Az értékek tömb van ideje két elemet, ha a az idő, vagy az időtartomány nem egyértelmű. Ha egy nem egyértelmű ideje, mind a reggel rendelkeznie és a délután időpontok.

## <a name="time-range-resolution-example"></a>Idő tartomány feloldási példa

Az alábbi példa bemutatja, hogyan használja a LUIS **datetimeV2** feloldani az utterance (kifejezés), amely rendelkezik egy időtartományt.

```json
  "entities": [
    {
      "entity": "6pm to 7pm",
      "type": "builtin.datetimeV2.timerange",
      "startIndex": 0,
      "endIndex": 9,
      "resolution": {
        "values": [
          {
            "timex": "(T18,T19,PT1H)",
            "type": "timerange",
            "start": "18:00:00",
            "end": "19:00:00"
          }
        ]
      }
    }
  ]
```

## <a name="preview-api-version-3x"></a>Előzetes verziójú API 3. x

DatetimeV2 JSON-válasz módosult az API v3-ben. 

Változások az API v2-ből:
* `datetimeV2.timex.type`a tulajdonságot a rendszer már nem adja vissza, `datetimev2.type`mert a szülő szinten adja vissza. 
* A `datetimeV2.timex` tulajdonság át `datetimeV2.value`lett nevezve a következőre:.

A Kimondás `8am on may 2nd 2017`érdekében a DatetimeV2 v3 verziója a következő:

```JSON
{
    "query": "8am on may 2nd 2017",
    "prediction": {
        "normalizedQuery": "8am on may 2nd 2017",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.6826963
            }
        },
        "entities": {
            "datetimeV2": [
                {
                    "type": "datetime",
                    "values": [
                        {
                            "timex": "2017-05-02T08",
                            "value": "2017-05-02 08:00:00"
                        }
                    ]
                }
            ]
        }
    }
}
```

A következő JSON `verbose` a paraméter `false`értéke:

```json
{
    "query": "8am on may 2nd 2017",
    "prediction": {
        "normalizedQuery": "8am on may 2nd 2017",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.6826963
            }
        },
        "entities": {
            "datetimeV2": [
                {
                    "type": "datetime",
                    "values": [
                        {
                            "timex": "2017-05-02T08",
                            "value": "2017-05-02 08:00:00"
                        }
                    ]
                }
            ],
            "$instance": {
                "datetimeV2": [
                    {
                        "type": "builtin.datetimeV2.datetime",
                        "text": "8am on may 2nd 2017",
                        "startIndex": 0,
                        "length": 19,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    }
}
```

## <a name="deprecated-prebuilt-datetime"></a>Elavult előre összeállított dátum és idő

A `datetime` előre összeállított entitások hozzárendelésénél által **datetimeV2**. 

Cserélje le a `datetime` a `datetimeV2` a LUIS alkalmazás a következő lépéseket:

1. Nyissa meg a **entitások** ablaktábláján a LUIS webes felületén. 
2. Törölje a **datetime** előre összeállított entitások.
3. Kattintson a **előre összeállított entitások hozzáadása**
4. Válassza ki **datetimeV2** kattintson **mentése**.

## <a name="next-steps"></a>További lépések

További információ a [dimenzió](luis-reference-prebuilt-dimension.md), [e-mail](luis-reference-prebuilt-email.md) entitásokat, és [szám](luis-reference-prebuilt-number.md). 

