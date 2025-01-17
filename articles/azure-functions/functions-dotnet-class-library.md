---
title: Azure Functions C# fejlesztői dokumentáció
description: Ismerje meg, hogyan fejlesztheti C#Azure functions a használatával.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: azure-függvények, függvények, eseményfeldolgozás, webhookok, dinamikus számítás, kiszolgáló nélküli architektúra
ms.service: azure-functions
ms.devlang: dotnet
ms.topic: reference
ms.date: 09/12/2018
ms.author: glenga
ms.openlocfilehash: 388b389cca7c3e820ea3ccfd37a2a93ccd476b31
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68254638"
---
# <a name="azure-functions-c-developer-reference"></a>Azure Functions C# fejlesztői dokumentáció

<!-- When updating this article, make corresponding changes to any duplicate content in functions-reference-csharp.md -->

Ez a cikk bevezetést mutat be a .NET C# -es kódtárak használatával történő Azure functions fejlesztéséhez.

Azure Functions támogatja C# és C# parancsfájl-programozási nyelveket támogat. Ha [a Azure Portal használatával C# ](functions-create-function-app-portal.md)kapcsolatos útmutatást keres, tekintse [ C# meg a script (. CSX) fejlesztői referenciáját](functions-reference-csharp.md).

Ez a cikk azt feltételezi, hogy már elolvasta a következő cikkeket:

* [Azure Functions fejlesztői útmutató](functions-reference.md)
* [Azure Functions Visual Studio 2019-eszközök](functions-develop-vs.md)

## <a name="functions-class-library-project"></a>Functions Class Library-projekt

A Visual Studióban a **Azure functions** Project sablon egy olyan C# osztály-függvénytár-projektet hoz létre, amely a következő fájlokat tartalmazza:

* a [Host. JSON](functions-host-json.md) – olyan konfigurációs beállításokat tárol, amelyek a projekt összes funkcióját érintik helyileg vagy az Azure-ban való futtatáskor.
* [Local. Settings. JSON](functions-run-local.md#local-settings-file) – a helyileg futtatott Alkalmazásbeállítások és kapcsolódási karakterláncok tárolására szolgál. Ez a fájl titkokat tartalmaz, és nincs közzétéve az Azure-beli Function alkalmazásban. Ehelyett [adja hozzá az alkalmazás beállításait a Function alkalmazáshoz](functions-develop-vs.md#function-app-settings).

A projekt létrehozásakor a következő példához hasonló mappastruktúrát jön létre a Build kimeneti könyvtárában:

```
<framework.version>
 | - bin
 | - MyFirstFunction
 | | - function.json
 | - MySecondFunction
 | | - function.json
 | - host.json
```

Ez a könyvtár az Azure-beli Function alkalmazás üzembe helyezése. A functions futtatókörnyezet [2. x verziójában](functions-versions.md) szükséges kötési bővítmények a [projekthez NuGet csomagokként](./functions-bindings-register.md#vs)lesznek hozzáadva.

> [!IMPORTANT]
> A build folyamat minden függvényhez létrehoz egy *function. JSON* fájlt. A *function. JSON* fájlt nem közvetlenül kell szerkeszteni. Nem módosíthatja a kötési konfigurációt, vagy letilthatja a függvényt a fájl szerkesztésével. A függvények letiltásával kapcsolatos további információkért lásd a [függvények](disable-function.md#functions-2x---c-class-libraries)letiltását ismertető témakört.

## <a name="methods-recognized-as-functions"></a>Függvényekként felismert metódusok

Egy osztály-függvénytárban a függvény egy statikus metódus `FunctionName` a és egy trigger attribútummal, az alábbi példában látható módon:

```csharp
public static class SimpleExample
{
    [FunctionName("QueueTrigger")]
    public static void Run(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
    }
} 
```

Az `FunctionName` attribútum a metódust függvény belépési pontként jelöli. A névnek egyedinek kell lennie a projekten belül, betűvel kell kezdődnie, és csak betűket, `-`számokat, `_`és, legfeljebb 127 karaktert tartalmazhat. A Project templates gyakran létrehoz egy `Run`nevű metódust, de a metódus neve lehet C# bármely érvényes metódus neve.

Az trigger attribútum meghatározza az trigger típusát, és a bemeneti adatokat egy metódus paraméteréhez köti. A példa függvényt egy üzenetsor-üzenet indítja el, és a várólista- `myQueueItem` üzenet a paraméterben megadott metódusnak lesz átadva.

## <a name="method-signature-parameters"></a>Metódus-aláírás paraméterei

A metódus aláírása nem az trigger attribútummal használt paramétereket tartalmazhat. Íme néhány további paraméter, amelyet felvehet:

* A [bemeneti és kimeneti kötések](functions-triggers-bindings.md) úgy vannak megjelölve, hogy az attribútumokkal díszítsék őket.  
* A ( `TraceWriter` z) [](#logging)[](functions-versions.md#creating-1x-apps)vagy (1. x verziójú) paraméter a `ILogger` naplózáshoz.
* Egy `CancellationToken` paraméter a [kecses](#cancellation-tokens)leállításhoz.
* A [kötési kifejezések](./functions-bindings-expressions-patterns.md) paraméterei trigger-metaadatok beszerzéséhez.

A függvények aláírásában szereplő paraméterek sorrendje nem számít. Például az aktiválási paramétereket más kötések előtt vagy után is elhelyezheti, és a naplózó paramétert trigger vagy kötési paraméterek előtt vagy után is elhelyezheti.

### <a name="output-binding-example"></a>Kimeneti kötési példa

A következő példa egy kimeneti várólista-kötés hozzáadásával módosítja az előzőt. A függvény az üzenetsor-üzenetet írja, amely egy másik várólistában lévő új üzenetsor-üzenetre indítja a függvényt.

```csharp
public static class SimpleExampleWithOutput
{
    [FunctionName("CopyQueueMessage")]
    public static void Run(
        [QueueTrigger("myqueue-items-source")] string myQueueItem, 
        [Queue("myqueue-items-destination")] out string myQueueItemCopy,
        ILogger log)
    {
        log.LogInformation($"CopyQueueMessage function processed: {myQueueItem}");
        myQueueItemCopy = myQueueItem;
    }
}
```

A kötési útmutató cikkei (például a[tárolási várólisták](functions-bindings-storage-queue.md)) megmutatják, hogy milyen típusú paramétereket használhat trigger-, bemeneti vagy kimeneti kötési attribútumokkal.

### <a name="binding-expressions-example"></a>Példa kötési kifejezésekre

A következő kód beolvassa az Alkalmazásbeállítások által figyelt várólista nevét, és az üzenetsor- `insertionTime` létrehozási időt a paraméterben kapja meg.

```csharp
public static class BindingExpressionsExample
{
    [FunctionName("LogQueueMessage")]
    public static void Run(
        [QueueTrigger("%queueappsetting%")] string myQueueItem,
        DateTimeOffset insertionTime,
        ILogger log)
    {
        log.LogInformation($"Message content: {myQueueItem}");
        log.LogInformation($"Created at: {insertionTime}");
    }
}
```

## <a name="autogenerated-functionjson"></a>Automatikusan generált function. JSON

A build folyamat létrehoz egy *function. JSON* fájlt a Build mappában található Function mappában. Amint azt korábban említettük, a fájlt nem közvetlenül kell szerkeszteni. Nem módosíthatja a kötési konfigurációt, vagy letilthatja a függvényt a fájl szerkesztésével. 

Ennek a fájlnak a célja, hogy információt szolgáltasson a méretezési vezérlő számára [a](functions-scale.md#how-the-consumption-and-premium-plans-work)használati tervre vonatkozó döntések skálázásához. Emiatt a fájl csak trigger-információkkal, nem bemeneti vagy kimeneti kötésekkel rendelkezik.

A generált *function. JSON* fájl tartalmaz egy `configurationSource` tulajdonságot, amely azt jelzi, hogy a futtatókörnyezet .net-attribútumokat használ a kötésekhez ahelyett, hogy *function. JSON* -konfigurációt használjon. Például:

```json
{
  "generatedBy": "Microsoft.NET.Sdk.Functions-1.0.0.0",
  "configurationSource": "attributes",
  "bindings": [
    {
      "type": "queueTrigger",
      "queueName": "%input-queue-name%",
      "name": "myQueueItem"
    }
  ],
  "disabled": false,
  "scriptFile": "..\\bin\\FunctionApp1.dll",
  "entryPoint": "FunctionApp1.QueueTrigger.Run"
}
```

## <a name="microsoftnetsdkfunctions"></a>Microsoft.NET.Sdk.Functions

A *function. JSON* -fájl létrehozását a NuGet Package [Microsoft\.\.net\.SDK függvények](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions)végzik. 

Ugyanez a csomag a functions futtatókörnyezet 1. x és 2. x verziójára is használható. A cél-keretrendszer az 1. x projekt megkülönböztetése egy 2. x projektből. Az alábbiakban láthatók a *. csproj* -fájlok azon részei, amelyek különböző cél-keretrendszereket `Sdk` és azonos csomagot mutatnak be:

**Függvények 1. x**

```xml
<PropertyGroup>
  <TargetFramework>net461</TargetFramework>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```

**Függvények 2. x**

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.1</TargetFramework>
  <AzureFunctionsVersion>v2</AzureFunctionsVersion>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```

A csomagok `Sdk` függőségei közé az eseményindítók és a kötések tartoznak. Egy 1. x projekt egy 1. x eseményindítóra és kötésre hivatkozik, mivel ezek az eseményindítók és kötések a .NET-keretrendszert célozzák meg, a 2. x eseményindítók és kötések pedig a .NET Core-t célozzák meg.

A `Sdk` csomag a [Newtonsoft. JSON](https://www.nuget.org/packages/Newtonsoft.Json)fájltól, illetve a [WindowsAzure. Storage](https://www.nuget.org/packages/WindowsAzure.Storage)-ban közvetve is függ. Ezek a függőségek gondoskodnak arról, hogy a projekt azokat a csomagokat használja, amelyek a projekt által megcélzott functions futtatókörnyezet-verzióval működnek. Például `Newtonsoft.Json` a .NET-keretrendszer 4.6.1-es `Newtonsoft.Json` verziójának 11-es verziója, de a .NET-keretrendszer 4.6.1-re irányuló functions futtatókörnyezet csak a 9.0.1 kompatibilis. Így a projekt kódjának is a 9.0.1 használatát kell használnia `Newtonsoft.Json` .

`Microsoft.NET.Sdk.Functions` A forráskódja elérhető a GitHub-adattár [Azure\-functions\-vs\-\-Build SDK](https://github.com/Azure/azure-functions-vs-build-sdk)-ban.

## <a name="runtime-version"></a>Futtatókörnyezet verziója

A Visual Studio a [Azure functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) használatával futtatja a functions-projekteket. A Core Tools a functions futtatókörnyezet parancssori felülete.

Ha a NPM használatával telepíti az alapvető eszközöket, amelyek nem érintik a Visual Studio által használt alapvető eszközök verzióját. A functions Runtime 1. x verziójában a Visual Studio a *%USERPROFILE%\AppData\Local\Azure.functions.CLI* alapeszközök verzióját tárolja, és az ott tárolt legújabb verziót használja. A 2. x függvények esetében az alapvető eszközök a **Azure functions és a web Jobs Tools** bővítmény részét képezik. Az 1. x és a 2. x esetében egyaránt megtekintheti, hogy a konzol kimenetében milyen verzió van használatban a functions projekt futtatásakor:

```terminal
[3/1/2018 9:59:53 AM] Starting Host (HostId=contoso2-1518597420, Version=2.0.11353.0, ProcessId=22020, Debug=False, Attempt=0, FunctionsExtensionVersion=)
```

## <a name="supported-types-for-bindings"></a>A kötések támogatott típusai

Minden kötés saját támogatott típusokkal rendelkezik; például egy blob trigger attribútumot lehet alkalmazni egy karakterlánc-paraméterre, egy poco paraméterre, egy `CloudBlockBlob` paraméterre vagy számos más támogatott típusra. A [blob-kötések kötési útmutatója](functions-bindings-storage-blob.md#trigger---usage) felsorolja az összes támogatott paraméter-típust. További információ: triggerek [és kötések](functions-triggers-bindings.md) , valamint az [egyes kötési típusok kötési dokumentációja](functions-triggers-bindings.md#next-steps).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="binding-to-method-return-value"></a>Kötés a metódus visszatérési értékéhez

A metódus visszatérési értékét egy kimeneti kötéshez is használhatja, ha az attribútumot a metódus visszatérési értékére alkalmazza. Példákat az [Eseményindítók és kötések](./functions-bindings-return-value.md)című témakörben talál. 

Csak akkor használja a visszaadott értéket, ha egy sikeres függvény végrehajtása mindig visszatérési értéket ad eredményül a kimeneti kötésnek. Ellenkező esetben használja `ICollector` a `IAsyncCollector`vagy a beállítást, ahogy az a következő szakaszban látható.

## <a name="writing-multiple-output-values"></a>Több kimeneti érték írása

Ha több értéket szeretne írni egy kimeneti kötésbe, vagy ha egy sikeres függvény meghívása nem eredményez semmit a kimeneti kötéshez, használja a vagy [`ICollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) [`IAsyncCollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) a típust. Ezek a típusok olyan írásvédett gyűjtemények, amelyek a metódus befejeződése után a kimeneti kötésbe íródnak.

Ez a példa több üzenetsor-üzenetet ír ugyanabba a `ICollector`várólistába a következő használatával:

```csharp
public static class ICollectorExample
{
    [FunctionName("CopyQueueMessageICollector")]
    public static void Run(
        [QueueTrigger("myqueue-items-source-3")] string myQueueItem,
        [Queue("myqueue-items-destination")] ICollector<string> myDestinationQueue,
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
        myDestinationQueue.Add($"Copy 1: {myQueueItem}");
        myDestinationQueue.Add($"Copy 2: {myQueueItem}");
    }
}
```

## <a name="logging"></a>Naplózás

Ha a kimenetét be szeretné jelentkezni a C#folyamatos átviteli naplókba, vegyen fel egy [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger)típusú argumentumot. Azt javasoljuk, hogy nevezze el `log`, ahogy az alábbi példában is látható:  

```csharp
public static class SimpleExample
{
    [FunctionName("QueueTrigger")]
    public static void Run(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
    }
} 
```

Ne használja `Console.Write` a Azure functions. További információ: a [naplók írása C# ](functions-monitoring.md#write-logs-in-c-functions) a functions szolgáltatásban a **figyelő Azure functions** cikkben.

## <a name="async"></a>Aszinkron

A függvény [aszinkron](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/)működéséhez használja a `async` kulcsszót, és egy `Task` objektumot ad vissza.

```csharp
public static class AsyncExample
{
    [FunctionName("BlobCopy")]
    public static async Task RunAsync(
        [BlobTrigger("sample-images/{blobName}")] Stream blobInput,
        [Blob("sample-images-copies/{blobName}", FileAccess.Write)] Stream blobOutput,
        CancellationToken token,
        ILogger log)
    {
        log.LogInformation($"BlobCopy function processed.");
        await blobInput.CopyToAsync(blobOutput, 4096, token);
    }
}
```

Aszinkron függvényekben `out` nem használhatók paraméterek. Kimeneti kötések esetén használja helyette a [függvény visszatérési értékét](#binding-to-method-return-value) vagy egy [gyűjtő objektumot](#writing-multiple-output-values) .

## <a name="cancellation-tokens"></a>Visszavonási tokenek

A függvények elfogadják a [CancellationToken](/dotnet/api/system.threading.cancellationtoken) paramétert, amely lehetővé teszi, hogy az operációs rendszer értesítse a kódot, ha a függvény hamarosan leáll. Ezzel az értesítéssel meggyőződhet arról, hogy a függvény váratlanul leáll olyan módon, amely inkonzisztens állapotban hagyja az adatvesztést.

Az alábbi példa bemutatja, hogyan ellenőrizhető a közelgő függvények leállítása.

```csharp
public static class CancellationTokenExample
{
    public static void Run(
        [QueueTrigger("inputqueue")] string inputText,
        TextWriter logger,
        CancellationToken token)
    {
        for (int i = 0; i < 100; i++)
        {
            if (token.IsCancellationRequested)
            {
                logger.WriteLine("Function was cancelled at iteration {0}", i);
                break;
            }
            Thread.Sleep(5000);
            logger.WriteLine("Normal processing for queue message={0}", inputText);
        }
    }
}
```

## <a name="environment-variables"></a>Környezeti változók

Környezeti változó vagy Alkalmazásbeállítások értékének beszerzéséhez használja `System.Environment.GetEnvironmentVariable`a (z) értéket a következő példában látható módon:

```csharp
public static class EnvironmentVariablesExample
{
    [FunctionName("GetEnvironmentVariables")]
    public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
    {
        log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
        log.LogInformation(GetEnvironmentVariable("AzureWebJobsStorage"));
        log.LogInformation(GetEnvironmentVariable("WEBSITE_SITE_NAME"));
    }

    public static string GetEnvironmentVariable(string name)
    {
        return name + ": " +
            System.Environment.GetEnvironmentVariable(name, EnvironmentVariableTarget.Process);
    }
}
```

Az Alkalmazásbeállítások a helyi fejlesztésekor és az Azure-ban való futtatáskor is beolvashatók környezeti változókból. Helyi fejlesztés esetén az Alkalmazásbeállítások a `Values` *Local. Settings. JSON* fájlban lévő gyűjteményből származnak. Mindkét környezetben, a helyi és az Azure `GetEnvironmentVariable("<app setting name>")` -ban a megnevezett alkalmazás értékét kérdezi le. Ha például helyileg fut, a "saját hely neve" értéket adja vissza, ha a *Local. Settings. JSON* fájl tartalmaz `{ "Values": { "WEBSITE_SITE_NAME": "My Site Name" } }`.

A [System. Configuration. ConfigurationManager. appSettings](https://docs.microsoft.com/dotnet/api/system.configuration.configurationmanager.appsettings) tulajdonság egy alternatív API az Alkalmazásbeállítások értékének beolvasásához, de azt javasoljuk, hogy `GetEnvironmentVariable` használja az itt látható módon.

## <a name="binding-at-runtime"></a>Kötés futásidőben

A C# -ben és más .net nyelveken a kötelező [kötési mintát](https://en.wikipedia.org/wiki/Imperative_programming) is használhatja, az attribútumok [deklaratív](https://en.wikipedia.org/wiki/Declarative_programming) kötéseivel szemben. A kényszerített kötés akkor hasznos, ha a kötési paramétereket nem a tervezési idő, hanem futásidőben kell kiszámítani. Ezzel a mintával a függvény kódjában a támogatott bemeneti és kimeneti kötésekhez köthető.

A következő módon adjon meg egy kötelező kötést:

- **** Ne tartalmazzon attribútumot a függvény aláírásában a kívánt kényszerített kötésekhez.
- Adjon meg egy bemeneti [`Binder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) paramétert [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs)vagy.
- Az adatkötés C# végrehajtásához használja a következő mintát.

  ```cs
  using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
  {
      ...
  }
  ```

  `BindingTypeAttribute`az a .NET-attribútum, amely meghatározza a kötést, és `T` egy bemeneti vagy kimeneti típus, amelyet az adott kötési típus támogat. `T`nem lehet `out` paraméter típusa ( `out JObject`például). Például a Mobile apps tábla kimeneti kötése [hat kimeneti típust](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22)támogat, de csak a [ICollector\<t >](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) vagy a [IAsyncCollector\<t-> t](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) használhatja a kényszerített kötéssel.

### <a name="single-attribute-example"></a>Példa egyetlen attribútumra

A következő mintakód létrehoz egy [tárolási blob kimeneti kötést](functions-bindings-storage-blob.md#output) a futási időben definiált blob elérési úttal, majd karakterláncot ír a blobba.

```cs
public static class IBinderExample
{
    [FunctionName("CreateBlobUsingBinder")]
    public static void Run(
        [QueueTrigger("myqueue-items-source-4")] string myQueueItem,
        IBinder binder,
        ILogger log)
    {
        log.LogInformation($"CreateBlobUsingBinder function processed: {myQueueItem}");
        using (var writer = binder.Bind<TextWriter>(new BlobAttribute(
                    $"samples-output/{myQueueItem}", FileAccess.Write)))
        {
            writer.Write("Hello World!");
        };
    }
}
```

A [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs) meghatározza a [Storage blob](functions-bindings-storage-blob.md) bemeneti vagy kimeneti kötését, és a [TextWriter](/dotnet/api/system.io.textwriter) egy támogatott kimeneti kötési típus.

### <a name="multiple-attribute-example"></a>Több attribútum – példa

Az előző példában beolvassa a Function alkalmazás fő Storage `AzureWebJobsStorage`-fiókjának (azaz) az alkalmazás beállítását. A Storage-fiókhoz a [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) hozzáadásával és az attribútum tömbbe `BindAsync<T>()`való átadásával adhat meg egyéni alkalmazás-beállítást. Használjon paramétert, nem `IBinder`. `Binder`  Példa:

```cs
public static class IBinderExampleMultipleAttributes
{
    [FunctionName("CreateBlobInDifferentStorageAccount")]
    public async static Task RunAsync(
            [QueueTrigger("myqueue-items-source-binder2")] string myQueueItem,
            Binder binder,
            ILogger log)
    {
        log.LogInformation($"CreateBlobInDifferentStorageAccount function processed: {myQueueItem}");
        var attributes = new Attribute[]
        {
        new BlobAttribute($"samples-output/{myQueueItem}", FileAccess.Write),
        new StorageAccountAttribute("MyStorageAccount")
        };
        using (var writer = await binder.BindAsync<TextWriter>(attributes))
        {
            await writer.WriteAsync("Hello World!!");
        }
    }
}
```

## <a name="triggers-and-bindings"></a>Eseményindítók és kötések 

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További információ az eseményindítók és kötésekről](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [További információ a Azure Functions ajánlott eljárásairól](functions-best-practices.md)
