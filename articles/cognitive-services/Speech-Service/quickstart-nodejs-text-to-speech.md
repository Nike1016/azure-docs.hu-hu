---
title: 'Gyors útmutató: Szöveg-beszéd átalakítás, Node. js-Speech szolgáltatás konvertálása'
titleSuffix: Azure Cognitive Services
description: Ebből a rövid útmutatóból megtudhatja, hogyan alakíthatja át a szöveg – beszéd szöveget a Node. js és a szöveg – beszéd REST API használatával. A jelen útmutatóban szereplő minta szöveg van felépítve, beszéd összefoglaló Markup Language (SSML). Ez lehetővé teszi, hogy válassza ki a beszédfelismerési és nyelvi beszédfelismerési válasz.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 72be99ec666bb9e04ffca6e14ab4fcafa889ae68
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68553936"
---
# <a name="quickstart-convert-text-to-speech-using-nodejs"></a>Gyors útmutató: Szöveg-beszéd átalakítás a Node. js használatával

Ebből a rövid útmutatóból megtudhatja, hogyan alakíthatja át a szöveg – beszéd szöveget a Node. js és a szöveg – beszéd REST API használatával. Ebben az útmutatóban a kérelem törzsében van strukturálva, [Speech összefoglaló Markup Language (SSML)](speech-synthesis-markup.md), amely lehetővé teszi, hogy a válasz nyelv és válassza ki.

Ehhez a rövid útmutatóhoz egy Speech Services-erőforrással rendelkező [Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) -fiókra van szükség. Ha nincs fiókja, használhatja az ingyenes [próbaidőszakot](get-started.md) egy előfizetői azonosító beszerzéséhez.

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz a következőkre van szükség:

* [Node 8.12.x vagy újabb](https://nodejs.org/en/)
* [A Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download), vagy kedvenc szövegszerkesztőjével
* Egy Azure-előfizetési kulcs a Speech Serviceshez. [Szerezze be ingyen!](get-started.md)

## <a name="create-a-project-and-require-dependencies"></a>Projekt létrehozása és függőségek megkövetelése

Hozzon létre egy új Node. js-projektet a kedvenc IDE vagy szerkesztő használatával. Ezután másolja a következő kódrészletet egy `tts.js` nevű fájlba a projektjében.

```javascript
// Requires request and request-promise for HTTP requests
// e.g. npm install request request-promise
const rp = require('request-promise');
// Requires fs to write synthesized speech to a file
const fs = require('fs');
// Requires readline-sync to read command line inputs
const readline = require('readline-sync');
// Requires xmlbuilder to build the SSML body
const xmlbuilder = require('xmlbuilder');
```

> [!NOTE]
> Ha még nem használta ezeket a modulokat, a program futtatása előtt telepítenie kell őket. A csomagok telepítéséhez futtassa a következő parancsot: `npm install request request-promise xmlbuilder readline-sync`.

## <a name="get-an-access-token"></a>Hozzáférési jogkivonat lekérése

A szöveg-hang transzformációs REST API egy hozzáférési jogkivonatot a hitelesítéshez szükséges. A hozzáférési jogkivonatot kapjon egy exchange szükség. Ez a függvény a Speech Services előfizetési kulcsát egy hozzáférési jogkivonat számára `issueToken` a végpont használatával cseréli le.

Ez a példa feltételezi, hogy a Speech Services-előfizetése az USA nyugati régiójában található. Ha egy másik régiót használ, módosítsa a `uri`. A teljes listát lásd: [régiók](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

Másolja a projektbe a következő kódot:

```javascript
// Gets an access token.
function getAccessToken(subscriptionKey) {
    let options = {
        method: 'POST',
        uri: 'https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken',
        headers: {
            'Ocp-Apim-Subscription-Key': subscriptionKey
        }
    }
    return rp(options);
}
```

> [!NOTE]
> A hitelesítéssel kapcsolatos további információkért lásd: [hitelesítés hozzáférési](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token)jogkivonattal.

A következő szakaszban létrehozjuk a függvényt a szöveg-beszéd API meghívásához és a szintetizált beszéd válasz mentéséhez.

## <a name="make-a-request-and-save-the-response"></a>Kérés és a válasz mentése

Itt hozza létre a kérést a szöveg-beszéd API-hoz, és mentse a beszédfelismerési választ. Ez a példa feltételezi, hogy az USA nyugati RÉGIÓJA végpont használata. Ha az erőforrás egy másik régióba regisztrálva van, ellenőrizze, hogy frissíti a `uri`. További információ: [Speech Services-régiók](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Következő lépésként hozzá kell a kéréshez szükséges fejlécek. Győződjön meg arról, hogy frissítenie `User-Agent` az erőforrás (az Azure Portalon található), és a készlet nevére `X-Microsoft-OutputFormat` , az előnyben részesített hangkimeneti. Kimeneti formátumok teljes listáját lásd: [hang kimenete](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

Ezután hozhat létre a kérelem törzsében Speech összefoglaló Markup Language (SSML) használatával. Ez a példa a struktúrát határozza meg, és használja a `text` adjon meg a korábban létrehozott.

>[!NOTE]
> Ebben a példában a `JessaRUS` hangtípusú. A Microsoft teljes listáját adott beszédhangot/nyelvek, lásd: [nyelvi támogatás](language-support.md).
> Ha érdekli a saját márkáját egy egyedi, könnyen felismerhető névre hangalapú létrehozása, [létrehozása egyéni hangtípust](how-to-customize-voice-font.md).

Végül paritásadatok egy kérelmet a szolgáltatáshoz. Ha a kérelem sikeres, és a rendszer egy 200 állapotkódot ad vissza, a beszédfelismerési válasz a következő `TTSOutput.wav`lesz:.

```javascript
// Make sure to update User-Agent with the name of your resource.
// You can also change the voice and output formats. See:
// https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#text-to-speech
function textToSpeech(accessToken, text) {
    // Create the SSML request.
    let xml_body = xmlbuilder.create('speak')
        .att('version', '1.0')
        .att('xml:lang', 'en-us')
        .ele('voice')
        .att('xml:lang', 'en-us')
        .att('name', 'Microsoft Server Speech Text to Speech Voice (en-US, Guy24KRUS)')
        .txt(text)
        .end();
    // Convert the XML into a string to send in the TTS request.
    let body = xml_body.toString();

    let options = {
        method: 'POST',
        baseUrl: 'https://westus.tts.speech.microsoft.com/',
        url: 'cognitiveservices/v1',
        headers: {
            'Authorization': 'Bearer ' + accessToken,
            'cache-control': 'no-cache',
            'User-Agent': 'YOUR_RESOURCE_NAME',
            'X-Microsoft-OutputFormat': 'riff-24khz-16bit-mono-pcm',
            'Content-Type': 'application/ssml+xml'
        },
        body: body
    }

    let request = rp(options)
        .on('response', (response) => {
            if (response.statusCode === 200) {
                request.pipe(fs.createWriteStream('TTSOutput.wav'));
                console.log('\nYour file is ready.\n')
            }
        });
    return request;
}
```

## <a name="put-it-all-together"></a>Az alkalmazás összeállítása

Már majdnem kész. Az utolsó lépés egy aszinkron függvény létrehozása. Ez a függvény beolvassa az előfizetési kulcsot egy környezeti változóból, megkéri a szöveget, lekérdezi a jogkivonatot, megvárja a kérés befejeződését, majd átalakítja a szöveget a beszédre, és mentse a hangot. wav néven.

Ha nem ismeri a környezeti változókat, vagy inkább egy karakterláncként szeretné tesztelni az előfizetési kulcs hardcoded, `process.env.SPEECH_SERVICE_KEY` cserélje le az előfizetési kulcsot karakterláncként.

```javascript
// Use async and await to get the token before attempting
// to convert text to speech.
async function main() {
    // Reads subscription key from env variable.
    // You can replace this with a string containing your subscription key. If
    // you prefer not to read from an env variable.
    // e.g. const subscriptionKey = "your_key_here";
    const subscriptionKey = process.env.SPEECH_SERVICE_KEY;
    if (!subscriptionKey) {
        throw new Error('Environment variable for your subscription key is not set.')
    };
    // Prompts the user to input text.
    const text = readline.question('What would you like to convert to speech? ');

    try {
        const accessToken = await getAccessToken(subscriptionKey);
        await textToSpeech(accessToken, text);
    } catch (err) {
        console.log(`Something went wrong: ${err}`);
    }
}

main()
```

## <a name="run-the-sample-app"></a>Mintaalkalmazás futtatása

Ennyi az egész, készen áll a szöveg-hang transzformációs mintaalkalmazás futtatásához. A parancssor (vagy a terminál-munkamenetben) lépjen a projektkönyvtárba, majd futtassa:

```console
node tts.js
```

Amikor a rendszer kéri, írjon be bármilyen kívánt szöveg-hang transzformációs konvertálása. Ha ez sikeres, a beszéd fájl található a projektmappába. A kedvenc media player használatával lejátszani.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ellenőrizze, hogy a bizalmas adatok eltávolítása a mintaalkalmazás forráskódja, például a előfizetési kulcsok.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A Node. js-minták megismerése a GitHubon](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NodeJS)

## <a name="see-also"></a>Lásd még

* [Szöveget beszéddé átalakító API-referencia](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Egyéni hangtípust létrehozása](how-to-customize-voice-font.md)
* [Hozzon létre egy egyéni beszédfelismerési rekord hangalapú minták](record-custom-voice-samples.md)
