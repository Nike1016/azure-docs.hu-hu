---
title: 'Gyors útmutató: Az Azure REST API és a cURL egy olyan rendszerképre az arcok észlelése'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid, használatával az Azure Face REST API a curl használatával arcok észlelése a képet.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: 68cdd147977294954051735d70307305aa5dc0cb
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603327"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-rest-api-and-curl"></a>Gyors útmutató: Arcfelismerés a képet, a Face REST API és a cURL használatával

Ebben a rövid, használatával az Azure Face REST API a curl használatával emberi arcok észlelése a képet.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt. 

## <a name="prerequisites"></a>Előfeltételek

- A Face API előfizetési kulcs. Megjelenik a származó ingyenes próba-előfizetését kulcsok [próbálja meg a Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Másik lehetőségként kövesse a [Cognitive Services-fiók létrehozása](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) a Face API szolgáltatás és a kulcs beszerzése.

## <a name="write-the-command"></a>A parancs írása
 
A Face API-t és a face attribútum adatokat kérhet le a képet fogja használni a következőkhöz hasonló parancsot. Először másolja be a kódot egy szövegszerkesztőbe&mdash;kell módosítania a parancs bizonyos részeit, mielőtt is futtatható legyen.

```shell
curl -H "Ocp-Apim-Subscription-Key: <Subscription Key>" "https://westcentralus.api.cognitive.microsoft.com/face/v1.0/detect?returnFaceId=true&returnFaceLandmarks=false&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise" -H "Content-Type: application/json" --data-ascii "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/c/c3/RH_Louise_Lillian_Gish.jpg\"}"
```

### <a name="subscription-key"></a>Előfizetői azonosító
Cserélje le `<Subscription Key>` az Arcfelismerés érvényes előfizetési kulccsal végzett.

### <a name="face-endpoint-url"></a>Face végpont URL-címe

Az URL-cím `https://westcentralus.api.cognitive.microsoft.com/face/v1.0/detect` azt jelzi, hogy a lekérdezés Azure Face végpontot. Előfordulhat, hogy módosítania az URL-címet, a régiót, amelyben felel meg az előfizetési kulcs megfelelő első része. Tekintse meg a [Face API-dokumentumok](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) minden régióban végpontok listáját.

### <a name="url-query-string"></a>URL-cím lekérdezési karakterlánc

A Face végponti URL-cím lekérdezési karakterláncában Megadja, hogy melyik face attribútumok lekéréséhez. Előfordulhat, hogy módosítani kívánja ezt a karakterláncot, a felhasználás céljának függően.

```
?returnFaceId=true&returnFaceLandmarks=false&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise
```

### <a name="image-source-url"></a>Forrás URL-címe
A forrás URL-cím azt jelzi, hogy bemeneteként használni kívánt lemezkép. Módosíthatja ezt a képet elemzési mutasson.

```
https://upload.wikimedia.org/wikipedia/commons/c/c3/RH_Louise_Lillian_Gish.jpg
``` 

## <a name="run-the-command"></a>Futtassa a

Amikor végzett a módosításokat, nyisson meg egy parancssort, és adja meg az új parancsot. Megtekintheti az arcfelismerési adatokat JSON-adatok a konzolablakban jelenik meg. Példa:

```json
[
  {
    "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f",
    "faceRectangle": {
      "top": 131,
      "left": 177,
      "width": 162,
      "height": 162
    },
    "faceAttributes": {
      "smile": 0,
      "headPose": {
        "pitch": 0,
        "roll": 0.1,
        "yaw": -32.9
      },
      "gender": "female",
      "age": 22.9,
      "facialHair": {
        "moustache": 0,
        "beard": 0,
        "sideburns": 0
      },
      "glasses": "NoGlasses",
      "emotion": {
        "anger": 0,
        "contempt": 0,
        "disgust": 0,
        "fear": 0,
        "happiness": 0,
        "neutral": 0.986,
        "sadness": 0.009,
        "surprise": 0.005
      },
      "blur": {
        "blurLevel": "low",
        "value": 0.06
      },
      "exposure": {
        "exposureLevel": "goodExposure",
        "value": 0.67
      },
      "noise": {
        "noiseLevel": "low",
        "value": 0
      },
      "makeup": {
        "eyeMakeup": true,
        "lipMakeup": true
      },
      "accessories": [],
      "occlusion": {
        "foreheadOccluded": false,
        "eyeOccluded": false,
        "mouthOccluded": false
      },
      "hair": {
        "bald": 0,
        "invisible": false,
        "hairColor": [
          {
            "color": "brown",
            "confidence": 1
          },
          {
            "color": "black",
            "confidence": 0.87
          },
          {
            "color": "other",
            "confidence": 0.51
          },
          {
            "color": "blond",
            "confidence": 0.08
          },
          {
            "color": "red",
            "confidence": 0.08
          },
          {
            "color": "gray",
            "confidence": 0.02
          }
        ]
      }
    }
  }
]
```

## <a name="next-steps"></a>További lépések

Ez a rövid útmutatóban okkal készítette el a cURL-parancsot, amely meghívja ezt az Azure Face API a képet arcok észlelése és attribútumaik. Ezután megkezdheti a Face API dokumentációja további.

> [!div class="nextstepaction"]
> [Face API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
