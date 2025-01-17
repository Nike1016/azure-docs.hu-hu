---
title: 'Gyors útmutató: C#SDK-lekérdezés előrejelzési végpontja – LUIS'
titleSuffix: Azure Cognitive Services
description: Az C# SDK használatával elküldheti a felhasználóknak a Luis-t, és előrejelzést kaphat.
author: diberry
manager: nitinme
ms.service: cognitive-services
services: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: c0b534848232d60929722e2036f69f4b6e670a4a
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563155"
---
# <a name="quickstart-query-prediction-endpoint-with-c-net-sdk"></a>Gyors útmutató: Lekérdezés-előrejelzési C# VÉGPONT .net SDK-val

Használja a [NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/)címen található .net SDK-t, hogy küldjön egy felhasználót Language UNDERSTANDING (Luis), és megkapja a felhasználó szándékának előrejelzését. 

Ez a rövid `turn on the bedroom light`útmutató egy felhasználó teljes körű, például egy nyilvános Language Understanding alkalmazáshoz való küldését küldi el, és megjeleníti az előrejelzést, és `HomeAutomation.TurnOn` megjeleníti a legfelső pontozási szándékot és a megtalált entitást `HomeAutomation.Room` . 

## <a name="prerequisites"></a>Előfeltételek

* [A Visual Studio Community 2017-es kiadása](https://visualstudio.microsoft.com/vs/community/)
* C# programozási nyelv (a VS Community 2017 része)
* A df67dcdb-c37d-46af-88e1-8b97951ca1c2 azonosítójú nyilvános alkalmazás

> [!Note]
> A teljes megoldás a [kognitív szolgáltatások – Language-Understanding](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/sdk-quickstarts/c%23/UsePredictionRuntime) GitHub-tárházban érhető el.

További dokumentációt keres?

 * [Az SDK dokumentációja](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet)


## <a name="get-cognitive-services-or-language-understanding-key"></a>Cognitive Services vagy Language Understanding kulcs beolvasása

Ahhoz, hogy a nyilvános alkalmazást otthoni automatizálásra használhassa, érvényes kulcsra van szüksége a végpontok előrejelzéséhez. Használhatja az Azure CLI-vel létrehozott Cognitive Services kulcsot is, amely számos kognitív szolgáltatás vagy `Language Understanding` kulcs esetében érvényes. 

A következő [Azure CLI-paranccsal hozzon létre egy kognitív szolgáltatási kulcsot](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-create):

```azurecli-interactive
az cognitiveservices account create \
    -n my-cog-serv-resource \
    -g my-cog-serv-resource-group \
    --kind CognitiveServices \
    --sku S0 \
    -l WestEurope \ 
    --yes
```

## <a name="create-net-core-project"></a>.NET Core-projekt létrehozása

Hozzon létre egy .NET Core Console-projektet a Visual Studio Community 2017-ben.

1. Nyissa meg a Visual Studio Community 2017 alkalmazást.
1. Hozzon létre egy új projektet a **vizualizáció C#**  szakaszban, majd válassza a **Console app (.net Core)** lehetőséget.
1. Adja meg a projekt `QueryPrediction`nevét, hagyja változatlanul az alapértelmezett értékeket, majd kattintson **az OK gombra**.
    Ez egy egyszerű projektet hoz létre az **program.cs**nevű elsődleges kóddal.

## <a name="add-sdk-with-nuget"></a>SDK hozzáadása a NuGet

1. A **megoldáskezelő**válassza ki a projektet a **QueryPrediction**nevű fanézetben, majd kattintson a jobb gombbal a elemre. A menüben válassza a **NuGet-csomagok kezelése...** lehetőséget.
1. Válassza a **Tallózás** lehetőséget `Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime`, majd írja be a parancsot. Ha a csomag adatai megjelennek, válassza a **telepítés** lehetőséget a csomag projektbe való telepítéséhez. 
1. Adja hozzá a következő _using_ utasításokat a **program.cs**elejéhez. Ne távolítsa el a meglévő _using_ utasítást `System`a következőhöz:. 

```csharp
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime;
using Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime.Models;
```

## <a name="create-a-new-method-for-the-prediction"></a>Új módszer létrehozása az előrejelzéshez

Hozzon létre egy új `GetPrediction` metódust, hogy elküldje a lekérdezést a lekérdezés előrejelzési végpontjának. A metódus létrehozza és konfigurálja az összes szükséges objektumot `Task` , majd visszaadja a értéket az [`LuisResult`](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.models.luisresult?view=azure-dotnet) előrejelzés eredményeivel. 

```csharp
static async  Task<LuisResult> GetPrediction() {
}
```

## <a name="create-credentials-object"></a>Hitelesítő adatok létrehozása objektum

Adja hozzá a következő kódot a `GetPrediction` metódushoz, és hozza létre az ügyfél hitelesítő adatait a kognitív szolgáltatás kulcsával.

Cserélje `<REPLACE-WITH-YOUR-KEY>` le a-t a kognitív szolgáltatás kulcsának régiójára. A kulcs a [Azure Portal](https://portal.azure.com) az adott erőforrás kulcsai lapján.

```csharp
// Use Language Understanding or Cognitive Services key
// to create authentication credentials
var endpointPredictionkey = "<REPLACE-WITH-YOUR-KEY>";
var credentials = new ApiKeyServiceClientCredentials(endpointPredictionkey);
```

## <a name="create-language-understanding-client"></a>Language Understanding-ügyfél létrehozása

A metódusban az előző kód után adja hozzá a következő kódot az új hitelesítő adatok használatához, és hozzon [`LUISRuntimeClient`](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient.-ctor?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_LUIS_Runtime_LUISRuntimeClient__ctor_Microsoft_Rest_ServiceClientCredentials_System_Net_Http_DelegatingHandler___) létre egy ügyfél-objektumot. `GetPrediction` 

Cserélje `<REPLACE-WITH-YOUR-KEY-REGION>` le a billentyűt a kulcs régiójára `westus`, például:. A kulcsfontosságú régió a [Azure Portal](https://portal.azure.com) az adott erőforráshoz tartozó áttekintés oldalon.

```csharp
// Create Luis client and set endpoint
// region of endpoint must match key's region, for example `westus`
var luisClient = new LUISRuntimeClient(credentials, new System.Net.Http.DelegatingHandler[] { });
luisClient.Endpoint = "https://<REPLACE-WITH-YOUR-KEY-REGION>.api.cognitive.microsoft.com";
```

## <a name="set-query-parameters"></a>Lekérdezési paraméterek beállítása

`GetPrediction` A metódusban az előző kód után adja hozzá a következő kódot a lekérdezési paraméterek beállításához.

```csharp
// public Language Understanding Home Automation app
var appId = "df67dcdb-c37d-46af-88e1-8b97951ca1c2";

// query specific to home automation app
var query = "turn on the bedroom light";

// common settings for remaining parameters
Double? timezoneOffset = null;
var verbose = true;
var staging = false;
var spellCheck = false;
String bingSpellCheckKey = null;
var log = false;
```

## <a name="query-prediction-endpoint"></a>Előrejelzési végpont lekérdezése

`GetPrediction` A metódusban az előző kód után adja hozzá a következő kódot a lekérdezési paraméterek beállításához:

```csharp
// Create prediction client
var prediction = new Prediction(luisClient);

// get prediction
return await prediction.ResolveAsync(appId, query, timezoneOffset, verbose, staging, spellCheck, bingSpellCheckKey, log, CancellationToken.None);
```

## <a name="display-prediction-results"></a>Előrejelzés eredményeinek megjelenítése

Módosítsa a **Main** metódust az új `GetPrediction` metódus meghívásához és az előrejelzés eredményének visszaadásához:

```csharp
static void Main(string[] args)
{

    var luisResult = GetPrediction().Result;

    // Display query
    Console.WriteLine("Query:'{0}'", luisResult.Query);

    // Display most common properties of query result
    Console.WriteLine("Top intent is '{0}' with score {1}", luisResult.TopScoringIntent.Intent,luisResult.TopScoringIntent.Score);

    // Display all entities detected in query utterance
    foreach (var entity in luisResult.Entities)
    {
        Console.WriteLine("{0}:'{1}' begins at position {2} and ends at position {3}", entity.Type, entity.Entity, entity.StartIndex, entity.EndIndex);
    }

    Console.Write("done");

}
```

## <a name="run-the-project"></a>A projekt futtatása

Hozza létre a projektet a Studióban, és futtassa a projektet a lekérdezés kimenetének megtekintéséhez:

```console
Query:'turn on the bedroom light'
Top intent is 'HomeAutomation.TurnOn' with score 0.809439957
HomeAutomation.Room:'bedroom' begins at position 12 and ends at position 18
```

## <a name="next-steps"></a>További lépések

További információ a [.net SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/) -ról és a [.net](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet)-dokumentációról. 

> [!div class="nextstepaction"] 
> [Oktatóanyag: LUIS-alkalmazás létrehozása a felhasználói szándékok meghatározásához](luis-quickstart-intents-only.md) 