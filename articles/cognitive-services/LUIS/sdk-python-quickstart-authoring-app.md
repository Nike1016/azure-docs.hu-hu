---
title: 'Gyors útmutató: Language Understanding (LUIS) a Pythonhoz készült ügyféloldali kódtár készítése'
titleSuffix: Azure Cognitive Services
description: Ismerkedés a Pythonhoz készült Language Understanding (LUIS) ügyféloldali kódtáraval. Az alábbi lépéseket követve telepítheti a csomagot, és kipróbálhatja az alapszintű feladatokhoz tartozó példa kódját.  Language Understanding (LUIS) lehetővé teszi, hogy egyéni gépi tanulási intelligenciát alkalmazzon egy felhasználó társalgási, természetes nyelvi szövegére az általános jelentés megbecslése érdekében, és a vonatkozó, részletes információk kiolvasását.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 08/07/2019
ms.author: diberry
ms.openlocfilehash: f5756c479d47e905e0110c9919cf4b5be4e75099
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932131"
---
# <a name="quickstart-language-understanding-luis-authoring-client-library-for-python"></a>Gyors útmutató: Language Understanding (LUIS) a Pythonhoz készült ügyféloldali kódtár készítése

Ismerkedés a Pythonhoz készült Language Understanding (LUIS) létrehozásával. Az alábbi lépéseket követve telepítheti a csomagot, és kipróbálhatja az alapszintű feladatokhoz tartozó példa kódját.  Language Understanding (LUIS) lehetővé teszi, hogy egyéni gépi tanulási intelligenciát alkalmazzon egy felhasználó társalgási, természetes nyelvi szövegére az általános jelentés megbecslése érdekében, és a vonatkozó, részletes információk kiolvasását. 

Használja a Pythonhoz készült Language Understanding (LUIS) a következőhöz:

* Hozzon létre egy alkalmazást.
* Leképezések, entitások és példa hosszúságú kimondott szöveg hozzáadása.
* Funkciók hozzáadása, például egy kifejezési lista.
* Alkalmazás betanítása és közzététele.

[A dokumentáció](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/index?view=azure-python) | [könyvtár forráskódjának](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-luis/azure/cognitiveservices/language/luis) | [szerzői csomagja (PyPI)](https://pypi.org/project/azure-cognitiveservices-language-luis/) | [mintái](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/application_quickstart.py)

## <a name="prerequisites"></a>Előfeltételek

* Language Understanding (LUIS) portál fiókja: [Hozzon létre egyet ingyen](https://www.luis.ai).
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>Beállítás

### <a name="get-your-language-understanding-luis-authoring-key"></a>A Language Understanding (LUIS) szerzői kulcsának beszerzése

Szerezze be a [szerzői kulcsot](luis-how-to-account-settings.md), és [hozzon létre környezeti változót](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) a `LUIS_AUTHORING_KEY` kulcs, a named és a környezeti változó számára a `LUIS_REGION`kulcs régiójában.

### <a name="install-the-python-library-for-luis"></a>A Python-könyvtár telepítése a LUIS számára

Az alkalmazás könyvtárában telepítse a Pythonhoz készült Language Understanding (LUIS) authoring ügyféloldali kódtárat a következő paranccsal:

```console
pip install azure-cognitiveservices-language-luis
```

## <a name="object-model"></a>Objektummodell

A Language Understanding (LUIS) authoring Client egy [LUISAuthoringClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-python) objektum, amely a szerzői kulcsot tartalmazó Azure-ba hitelesíti.

Az ügyfél létrehozása után ezt az ügyfelet használhatja a következő funkciók eléréséhez, többek között:

* Alkalmazások – [Létrehozás](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#add-application-create-object--custom-headers-none--raw-false----operation-config-), [Törlés](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#delete-app-id--force-false--custom-headers-none--raw-false----operation-config-), [Közzététel](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#publish-app-id--version-id-none--is-staging-false--custom-headers-none--raw-false----operation-config-)
* Példa hosszúságú kimondott szöveg – [Hozzáadás köteg](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations?view=azure-python#batch-app-id--version-id--example-label-object-array--custom-headers-none--raw-false----operation-config-)szerint, [Törlés azonosító alapján](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations?view=azure-python#delete-app-id--version-id--example-id--custom-headers-none--raw-false----operation-config-) 
* Funkciók – [kifejezések listája](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.featuresoperations?view=azure-python#add-phrase-list-app-id--version-id--phraselist-create-object--custom-headers-none--raw-false----operation-config-) 
* Modell – [leképezések](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python#add-intent-app-id--version-id--name-none--custom-headers-none--raw-false----operation-config-) és entitások kezelése
* Minta – [minták](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.patternoperations?view=azure-python#add-pattern-app-id--version-id--pattern-none--intent-none--custom-headers-none--raw-false----operation-config-) kezelése
* Betanítás [](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#train-version-app-id--version-id--custom-headers-none--raw-false----operation-config-) az alkalmazáshoz és a lekérdezés a betanítási [állapothoz](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#get-status-app-id--version-id--custom-headers-none--raw-false----operation-config-)
* [Verziók](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.versionsoperations?view=azure-python) – kezelés klónozással, exportálással és törléssel


## <a name="code-examples"></a>Példák a kódokra

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következőket a Pythonhoz készült Language Understanding (LUIS) authoring ügyféloldali kódtár használatával:

* [Alkalmazás létrehozása](#create-a-luis-app)
* [Entitások hozzáadása](#create-entities-for-the-app)
* [Leképezések hozzáadása](#create-intent-for-the-app)
* [Példa hosszúságú kimondott szöveg hozzáadása](#add-example-utterance-to-intent)
* [Az alkalmazás betanítása](#train-the-app)
* [Az alkalmazás közzététele](#publish-a-language-understanding-app)

## <a name="create-a-new-python-application"></a>Új Python-alkalmazás létrehozása

Hozzon létre egy új Python-alkalmazást az előnyben részesített szerkesztőben vagy az IDE-ben. Ezután importálja a következő kódtárakat. 

[!code-python[Create a new Python application in your preferred editor or IDE.](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=Dependencies)]

Hozzon létre változókat az erőforrás Azure-végpontjának és-kulcsának létrehozásához. Ha a környezeti változót az alkalmazás elindítása után hozta létre, akkor a változó eléréséhez be kell állítania és újra meg kell nyitnia a szerkesztőt, az IDE-t vagy a shellt.

[!code-python[Create variables for your resource's Azure endpoint and key.](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=AuthorizationVariables)]

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Hozzon létre egy [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) objektumot a kulccsal, és használja a végpontján egy [LUISAuthoringClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-python) objektum létrehozásához.

[!code-python[Create LUIS client object](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=Client)]

## <a name="create-a-luis-app"></a>A LUIS-alkalmazás létrehozása

1. Hozzon létre egy LUIS-alkalmazást, amely tartalmazza a természetes nyelvi feldolgozó (NLP) modellt, amely a szándékok, az entitások és a példa hosszúságú kimondott szöveg. 

1. Hozzon létre egy [AppsOperation](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python) -objektum [hozzáadási](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#add-application-create-object--custom-headers-none--raw-false----operation-config-) metódusát az alkalmazás létrehozásához. A név és a nyelvi kultúra kötelező tulajdonságai. 

    [!code-python[Create LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=createApp)]


## <a name="create-intent-for-the-app"></a>Szándék létrehozása az alkalmazáshoz
A LUIS-alkalmazás modelljében lévő elsődleges objektum a szándék. A szándék összehangolja a felhasználói Kimondás szándékait tartalmazócsoporttal. Előfordulhat, hogy egy felhasználó felteheti a kérdést, vagy egy olyan utasítást, amely egy bot (vagy más ügyfélalkalmazás) által megadott _kívánt_ választ keres. Ilyenek például a repülőjáratok foglalása, az időjárás megkérdezése egy adott célállomáson, és az ügyfélszolgálat elérhetőségi adatai.   

Használja a [modellt. adja hozzá az _intent](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python#add-intent-app-id--version-id--name-none--custom-headers-none--raw-false----operation-config-) metódust az egyedi szándék nevével, majd adja át az alkalmazás azonosítóját, a verziószámát és az új leképezés nevét. 

[!code-python[Create LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=addIntents)]

## <a name="create-entities-for-the-app"></a>Entitások létrehozása az alkalmazáshoz

Habár az entitások nem kötelezőek, a legtöbb alkalmazásban megtalálhatók. Az entitás kinyeri az adatokat a felhasználótól, és a felhasználó szándékának fullfil szükséges. Az [előre elkészített](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python#add-prebuilt-app-id--version-id--prebuilt-extractor-names--custom-headers-none--raw-false----operation-config-) és az egyéni entitások több típusa is van, amelyek mindegyike saját Adatátalakítási objektum-(DTO-) modellel rendelkezik.  Az alkalmazásba felvenni kívánt közös előre összeépített entitások közé tartozik a [Number](luis-reference-prebuilt-number.md), a [datetimeV2](luis-reference-prebuilt-datetimev2.md), a [geographyV2](luis-reference-prebuilt-geographyv2.md)és a [sorszám](luis-reference-prebuilt-ordinal.md). 

Ez a **add_entities** metódus egy `Location` egyszerű entitást hozott létre két szerepkörrel, egy `Class` egyszerű `Flight` entitással, egy összetett entitással és több előre elkészített entitás hozzáadásával.

Fontos tudni, hogy az entitások nincsenek megjelölve szándékkal megjelölve. Ezek általában számos szándékra vonatkoznak. Csak például a felhasználó hosszúságú kimondott szöveg van megjelölve egy adott, egyetlen szándékkal.

Az entitások létrehozási módszerei a [ModelOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python) osztály részét képezik. Minden entitás típusa saját Adatátalakítási objektum (DTO) modellt tartalmaz. 

[!code-python[Create LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=addEntities)]

## <a name="add-example-utterance-to-intent"></a>Példaként való Kimondás hozzáadása a szándékhoz

Ha meg szeretné határozni a teljes szándékot, és kinyeri az entitásokat, az alkalmazásnak példákat kell hosszúságú kimondott szöveg. A példákban egy adott, egyetlen szándékot kell megcélozni, és az összes egyéni entitást meg kell jelölni. Az előre elkészített entitásokat nem kell megjelölni. 

Adja hozzá például a hosszúságú kimondott szöveg egy [ExampleLabelObject](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.models.examplelabelobject?view=azure-python) -objektumok listájának létrehozásával, amely minden egyes példa kiírásának egy objektumát tartalmazza. Mindegyik példa minden entitást megjelöl az entitás neve és az entitás értéke név/érték párokkal rendelkező szótárával. Az entitás értékének pontosan úgy kell lennie, ahogy a példa szövegében megjelenik. 

Példákat hívhat meg [. a Batch](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations?view=azure-python#batch-app-id--version-id--example-label-object-array--custom-headers-none--raw-false----operation-config-) az alkalmazás azonosítóját, a verziószámot és a példák listáját tartalmazza. A hívás az eredmények listájával válaszol. Ellenőriznie kell az egyes példák eredményeit, hogy biztosan hozzá lehessen adni a modellhez. 

[!code-python[Add example utterances via a batch](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=addUtterances)]
    
## <a name="train-the-app"></a>Az alkalmazás betanítása

A modell létrehozása után a LUIS alkalmazást a modell ezen verziójára kell képezni. A betanított modell használható egy [tárolóban](luis-container-howto.md), vagy [közzétehető](luis-how-to-publish-app.md) az átmeneti vagy a termék tárolóhelyeken. 

A [Train. train_version](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#train-version-app-id--version-id--custom-headers-none--raw-false----operation-config-) metódusnak meg kell felelnie az alkalmazás azonosítójának és a verzió azonosítójának. 

Nagyon kis modell, például ez a rövid útmutató mutatja, nagyon gyorsan betanítja. Üzemi szintű alkalmazások esetén az alkalmazásnak be kell vonnia a [get_status](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#get-status-app-id--version-id--custom-headers-none--raw-false----operation-config-) metódus lekérdezési hívását annak meghatározására, hogy mikor vagy mikor sikerült a képzés. A válasz az egyes objektumokhoz külön állapotú [ModelTrainingInfo](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.models.modeltraininginfo?view=azure-python) objektumok listája. Az összes objektumnak sikeresnek kell lennie ahhoz, hogy a képzés befejezze.

[!code-python[Train LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=train)]

## <a name="publish-a-language-understanding-app"></a>Language Understanding alkalmazás közzététele

Tegye közzé a LUIS alkalmazást az [app. publish](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#publish-app-id--version-id-none--is-staging-false--custom-headers-none--raw-false----operation-config-) metódus használatával. Ez közzéteszi a jelenlegi betanított verziót a végponton megadott tárolóhelyre. Az ügyfélalkalmazás ezt a végpontot használja arra, hogy felhasználói hosszúságú kimondott szöveg küldjön a szándékok és az entitások kinyerésének előrejelzésére.

[!code-python[Publish LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=publish)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást `python` a gyors üzembe helyezési fájlban található paranccsal.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha Cognitive Services-előfizetést szeretne törölni, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek.

* [Portál](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#clean-up-resources)
* [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli#clean-up-resources) 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
>[LUIS-alkalmazás létrehozása a felhasználói szándékok meghatározásához](luis-quickstart-intents-only.md)

* [Mi a Language Understanding (LUIS) API?](what-is-luis.md)
* [Újdonságok](whats-new.md)
* [Szándékok](luis-concept-intent.md), entitások és [példa hosszúságú kimondott szöveg](luis-concept-utterance.md)és [előre összeépített entitások](luis-reference-prebuilt-entities.md) [](luis-concept-entity-types.md)
* A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/application_quickstart.py)található.
