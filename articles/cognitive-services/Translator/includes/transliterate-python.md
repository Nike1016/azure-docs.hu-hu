---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: c047d03f27325ffa133c62ef3fe945f330139656
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68968586"
---
## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz a következőkre van szükség:

* Python 2.7.x vagy 3.x
* Egy Azure-előfizetői azonosító a Translator Text szolgáltatáshoz

## <a name="create-a-project-and-import-required-modules"></a>Projekt létrehozása és a szükséges modulok importálása

Hozzon létre egy új projektet a kedvenc ide vagy szerkesztőjével, vagy egy új mappát, amely `transliterate-text.py` egy nevű fájlt használ az asztalon. Ezután másolja a kódrészletet a projektbe vagy fájlba:

```python
# -*- coding: utf-8 -*-
import os
import requests
import uuid
import json
```

> [!NOTE]
> Ha még nem használta ezeket a modulokat, a program futtatása előtt telepítenie kell őket. A csomagok telepítéséhez futtassa a következő parancsot: `pip install requests uuid`.

Az első megjegyzés arra utasítja a Python-értelmezőt, hogy UTF-8 kódolást használjon. A rendszer importálja azokat a modulokat, amelyek az előfizetői azonosító egy környezeti változóból való beolvasásához, a HTTP-kérelem felépítéséhez, egy egyedi azonosító létrehozásához, illetve a Translator Text API által visszaadott JSON-válasz kezeléséhez szükségesek.

## <a name="set-the-subscription-key-base-url-and-path"></a>Az előfizetői azonosító, az alap URL-cím és az elérési út beállítása

Ez a minta megpróbálja beolvasni a Translator Text-előfizetői azonosítót a `TRANSLATOR_TEXT_KEY` környezeti változóból. Ha még nem ismeri a környezeti változókat, beállíthatja a `subscriptionKey` sztringet, és megjegyzéssé teheti a feltételes utasítást.

Másolja a projektbe a következő kódot:

```python
# Checks to see if the Translator Text subscription key is available
# as an environment variable. If you are setting your subscription key as a
# string, then comment these lines out.
if 'TRANSLATOR_TEXT_KEY' in os.environ:
    subscriptionKey = os.environ['TRANSLATOR_TEXT_KEY']
else:
    print('Environment variable for TRANSLATOR_TEXT_KEY is not set.')
    exit()
# If you want to set your subscription key as a string, uncomment the line
# below and add your subscription key.
#subscriptionKey = 'put_your_key_here'
```

A Translator Text globális végpont beállítása `base_url`. A `path` tulajdonság a `transliterate` útvonalat állítja be, és meghatározza, hogy a 3-as API-verziót szeretnénk használni.

A beviteli nyelv, valamint a bemeneti és a kimeneti szkriptek a `params` értékeként állíthatók be. Ebben a példában egy japán nyelvű szövegből hozunk létre latin betűs átiratot.

>[!NOTE]
> További információ a végpontokról, az útvonalakról és a kérelmek paraméteréről [: Translator Text API 3,0: Átbetűzés](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-transliterate).

```python
base_url = 'https://api.cognitive.microsofttranslator.com'
path = '/transliterate?api-version=3.0'
params = '&language=ja&fromScript=jpan&toScript=latn'
constructed_url = base_url + path + params
```

## <a name="add-headers"></a>Fejlécek hozzáadása

A kérelmek hitelesítésének legegyszerűbb módja az, hogy átadja az előfizetői azonosítót `Ocp-Apim-Subscription-Key` fejlécként, amit ebben a példában alkalmazunk. Alternatív megoldásként hozzáférési jogkivonatra cserélheti az előfizetői azonosítóját, és átadhatja a hozzáférési jogkivonatot is `Authorization` fejlécként a kérelem ellenőrzése céljából. További információért lásd: [Hitelesítés](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

Másolja a projektbe a következő kódrészletet:

```python
headers = {
    'Ocp-Apim-Subscription-Key': subscriptionKey,
    'Content-type': 'application/json',
    'X-ClientTraceId': str(uuid.uuid4())
}
```

Ha Cognitive Services több szolgáltatásra kiterjedő előfizetést használ, akkor a kérés paramétereinek `Ocp-Apim-Subscription-Region` is szerepelnie kell. [További információ a többszolgáltatásos előfizetés hitelesítéséről](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="create-a-request-to-transliterate-text"></a>Kérelem létrehozása szöveg átírásához

Határozza meg az átírni kívánt sztringet (vagy sztringeket):

```python
# Transliterate "good afternoon" from source Japanese.
# Note: You can pass more than one object in body.
body = [{
    'text': 'こんにちは'
}]
```

A következő lépésben egy POST-kérelmet hozunk létre a `requests` modullal. Ez három argumentumot fogad: az összefűzött URL-címet, a kérelemfejléceket és a kérelem törzsét:

```python
request = requests.post(constructed_url, headers=headers, json=body)
response = request.json()
```

## <a name="print-the-response"></a>A válasz megjelenítése

Az utolsó lépés az eredmények kiírása. Ez a kódrészlet szebbé teszi az eredményeket a kulcsok rendezésével, a behúzás beállításával és az elem- és kulcselválasztók meghatározásával.

```python
print(json.dumps(response, sort_keys=True, indent=4,
                 ensure_ascii=False, separators=(',', ': ')))
```

## <a name="put-it-all-together"></a>Az alkalmazás összeállítása

Ezzel összeállított egy egyszerű programot, amely meghívja a Translator Text API-t, és visszaad egy JSON-választ. Most itt az ideje, hogy futtassa a programot:

```console
python transliterate-text.py
```

Ha szeretné összevetni a saját kódját a miénkkel, a teljes mintakódot megtekintheti a [GitHubon](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Python).

## <a name="sample-response"></a>Mintaválasz

```json
[
    {
        "script": "latn",
        "text": "konnichiwa"
    }
]
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem változtatható módon adta meg az előfizetői azonosítót a programban, a rövid útmutató befejezése után mindenképpen távolítsa el az előfizetői azonosítót.

## <a name="next-steps"></a>További lépések

Tekintse meg az API-referenciát, amely mindent megtudhat a Translator Text API.

> [!div class="nextstepaction"]
> [API-leírások](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
