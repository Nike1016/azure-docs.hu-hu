---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 69055937cb8f5e94a9e3c179b507fa064e2fa65c
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68968558"
---
## <a name="prerequisites"></a>Előfeltételek

* C#7,1 vagy újabb
* [.NET SDK](https://www.microsoft.com/net/learn/dotnet/hello-world-tutorial)
* [Json.NET NuGet csomag](https://www.nuget.org/packages/Newtonsoft.Json/)
* [A Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download), vagy kedvenc szövegszerkesztőjével
* Egy Azure-előfizetői azonosító a Translator Text szolgáltatáshoz

## <a name="create-a-net-core-project"></a>Egy .NET Core-projekt létrehozása

Nyisson meg egy új parancssort (vagy a terminál-munkamenetben), és futtassa a következő parancsokat:

```console
dotnet new console -o sentences-sample
cd sentences-sample
```

Az első parancs két dolgot eredményez. Egy új .NET-konzolalkalmazást hoz létre, és létrehoz egy könyvtárat nevű `sentences-sample`. A második parancs módosítja a projekt számára a könyvtárba.

Ezután telepítenie kell a Json.Net. A projekt címtárában futtassa a következőt:

```console
dotnet add package Newtonsoft.Json --version 11.0.2
```

## <a name="select-the-c-language-version"></a>Válassza ki a C# nyelvi változatát

Ez a rövid útmutatóhoz C# 7.1-es vagy újabb verziója. Néhány módon módosítsa a C# verzió a projekthez. Ebben az útmutatóban bemutatjuk, hogyan módosíthatja a `sentences-sample.csproj` fájlt. Az összes rendelkezésre álló beállításokat, például a Visual Studióban, a nyelv módosítását lásd: [válassza ki a C# beállított nyelvi verzióhoz](https://docs.microsoft.com/dotnet/csharp/language-reference/configure-language-version).

Nyissa meg a projektet, majd nyissa meg a `sentences-sample.csproj`. Győződjön meg arról, hogy `LangVersion` 7.1-es vagy újabb verziójára van beállítva. Ha nincs a tulajdonságcsoport a beállított nyelvi verzióhoz, adja hozzá ezeket a sorokat:

```xml
<PropertyGroup>
   <LangVersion>7.1</LangVersion>
</PropertyGroup>
```

## <a name="add-required-namespaces-to-your-project"></a>Adja hozzá a projekthez szükséges névterek

A `dotnet new console` parancsot, amely futtatta korábban létrehozott egy projektet, beleértve a `Program.cs`. Ez a fájl meg, ahová az alkalmazás kódjában fog. Nyissa meg `Program.cs`, és cserélje le a meglévő using utasítások. Ezek az utasítások győződjön meg arról, hogy a minta-alkalmazás létrehozásához és futtatásához szükséges összes típusú hozzáférést.

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.Threading.Tasks;
// Install Newtonsoft.Json with NuGet
using Newtonsoft.Json;
```

## <a name="create-classes-for-the-json-response"></a>Osztályok létrehozása a JSON-válaszhoz

Ezután létrehozunk egy olyan osztályt, amelyet a Translator Text API által visszaadott JSON-válasz deszerializálása során használunk.

```csharp
/// <summary>
/// The C# classes that represents the JSON returned by the Translator Text API.
/// </summary>
public class BreakSentenceResult
{
    public int[] SentLen { get; set; }
    public DetectedLanguage DetectedLanguage { get; set; }
}

public class DetectedLanguage
{
    public string Language { get; set; }
    public float Score { get; set; }
}
```

## <a name="create-a-function-to-determine-sentence-length"></a>Függvény létrehozása a mondat hosszának meghatározásához

Hozzon `Program` létre egy nevű `BreakSentence()`függvényt a osztályon belül. Ez a függvény négy argumentumot `subscriptionKey`vesz `host`igénybe: `inputText` `route`,, és.

```csharp
static public async Task BreakSentenceRequest(string subscriptionKey, string host, string route, string inputText)
{
  /*
   * The code for your call to the translation service will be added to this
   * function in the next few sections.
   */
}
```

## <a name="serialize-the-break-sentence-request"></a>A tördelési mondat kérésének szerializálása

Ezután létre kell hoznia és szerializálnia kell a szöveget tartalmazó JSON-objektumot. Ne feledje, hogy több objektumot is át tud adni a `body` tömbben.

```csharp
object[] body = new object[] { new { Text = inputText } };
var requestBody = JsonConvert.SerializeObject(body);
```

## <a name="instantiate-the-client-and-make-a-request"></a>Az ügyfél példányának létrehozása és kérelem elkészítése

Ezek a sorok a `HttpClient` és a `HttpRequestMessage`következőket hozzanak létre:

```csharp
using (var client = new HttpClient())
using (var request = new HttpRequestMessage())
{
  // In the next few sections you'll add code to construct the request.
}
```

## <a name="construct-the-request-and-print-the-response"></a>A kérelem kiépítése és a válasz nyomtatása

A a `HttpRequestMessage` következőkön belül fog megjelenni:

* A HTTP-metódus deklarálása
* A kérelem URI-ja felépítése
* A kérelem törzsének beszúrása (szerializált JSON-objektum)
* Kötelező fejlécek hozzáadása
* Aszinkron kérelem létrehozása
* A válasz megjelenítése

Adja hozzá ezt a kódot `HttpRequestMessage`a következőhöz:

```csharp
// Build the request.
// Set the method to POST
request.Method = HttpMethod.Post;
// Construct the URI and add headers.
request.RequestUri = new Uri(host + route);
request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

// Send the request and get response.
HttpResponseMessage response = await client.SendAsync(request).ConfigureAwait(false);
// Read response as a string.
string result = await response.Content.ReadAsStringAsync();
// Deserialize the response using the classes created earlier.
BreakSentenceResult[] deserializedOutput = JsonConvert.DeserializeObject<BreakSentenceResult[]>(result);
foreach (BreakSentenceResult o in deserializedOutput)
{
    Console.WriteLine("The detected language is '{0}'. Confidence is: {1}.", o.DetectedLanguage.Language, o.DetectedLanguage.Score);
    Console.WriteLine("The first sentence length is: {0}", o.SentLen[0]);
}
```

Ha Cognitive Services több szolgáltatásra kiterjedő előfizetést használ, akkor a kérés paramétereinek `Ocp-Apim-Subscription-Region` is szerepelnie kell. [További információ a többszolgáltatásos előfizetés hitelesítéséről](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="put-it-all-together"></a>Az alkalmazás összeállítása

Az utolsó lépés a `BreakSentenceRequest()` `Main` függvény hívása. Keresse `static void Main(string[] args)` meg és cserélje le a következő kódra:

```csharp
static async Task Main(string[] args)
{
    // This is our main function.
    // Output languages are defined in the route.
    // For a complete list of options, see API reference.
    string subscriptionKey = "YOUR_TRANSLATOR_TEXT_KEY_GOES_HERE";
    string host = "https://api.cognitive.microsofttranslator.com";
    string route = "/breaksentence?api-version=3.0";
    // Feel free to use any string.
    string breakSentenceText = @"How are you doing today? The weather is pretty pleasant. Have you been to the movies lately?";
    await BreakSentenceRequest(subscriptionKey, host, route, breakSentenceText);
}
```

Megfigyelheti, hogy `Main`a-ben `subscriptionKey`deklarálja `host` `route`,, és a kiértékelni `breakSentenceText`kívánt szöveget.

## <a name="run-the-sample-app"></a>Mintaalkalmazás futtatása

Ekkor készen áll a minta alkalmazás futtatására. A parancssor (vagy a terminál-munkamenetben) lépjen a projektkönyvtárba, majd futtassa:

```console
dotnet run
```

## <a name="sample-response"></a>Mintaválasz

A minta futtatása után a következőnek kell megjelennie a terminálon:

```bash
The detected language is \'en\'. Confidence is: 1.
The first sentence length is: 25
```

Ez az üzenet a nyers JSON-ből készült, amely így fog kinézni:

```json
[
    {
        "detectedLanguage":
        {
            "language":"en",
            "score":1.0
        },
        "sentLen":[25,32,35]
    }
]
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ellenőrizze, hogy a bizalmas adatok eltávolítása a mintaalkalmazás forráskódja, például a előfizetési kulcsok.

## <a name="next-steps"></a>További lépések

Tekintse meg az API-referenciát, amely mindent megtudhat a Translator Text API.

> [!div class="nextstepaction"]
> [API-leírások](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
