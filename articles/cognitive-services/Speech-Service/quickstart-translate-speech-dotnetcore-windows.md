---
title: 'Gyors útmutató: Beszéd fordítása C# , (.net Core Windows) – beszédfelismerési szolgáltatás'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban egy egyszerű .NET Core-alkalmazást fog létrehozni a felhasználói beszéd rögzítéséhez, a fordítást egy másik nyelvre, és a szöveget a parancssorba írja. Ez az útmutató Windows-felhasználók számára készült.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 81e652e6456b049c2f4fca938d02d1b0724f2ffd
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68609672"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-net-core"></a>Gyors útmutató: Beszéd fordítása a .NET Core-hoz készült Speech SDK-val

A gyors üzembe helyezési útmutató a [szöveg](quickstart-csharp-dotnet-windows.md) és a [beszéd](quickstart-text-to-speech-dotnetcore.md)közötti kommunikációhoz is elérhető.

Ebben a rövid útmutatóban egy egyszerű .NET Core-alkalmazást fog létrehozni, amely rögzíti a felhasználói beszédet a számítógép mikrofonjában, lefordítja a beszédet, és valós időben írja át a lefordított szöveget a parancssorba. Ez az alkalmazás a 64 bites Windows rendszeren fut, és a [SPEECH SDK NuGet csomaggal](https://aka.ms/csspeech/nuget) és a Microsoft Visual Studio 2017-es vagy újabb verziójával készült.

A beszédfelismeréshez elérhető nyelvek teljes listáját a [nyelvi támogatás](language-support.md)című témakörben tekintheti meg.

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz a következőkre van szükség:

* [.NET Core SDK](https://dotnet.microsoft.com/download)
* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) vagy újabb
* Egy Azure-előfizetési kulcs a beszédfelismerési szolgáltatáshoz. [Szerezze be az egyiket ingyenesen](get-started.md).

## <a name="create-a-visual-studio-project"></a>Visual Studio-projekt létrehozása

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-dotnetcore-create-proj.md)]

## <a name="add-sample-code"></a>Mintakód hozzáadása

1. Nyissa meg a `Program.cs` fájlt, és cserélje le a benne lévő teljes kódot a következőre.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/csharp-dotnetcore/helloworld/Program.cs#code)]

1. Ugyanabban a fájlban cserélje le a `YourSubscriptionKey` sztringet az előfizetői azonosítóra.

1. Cserélje le a `YourServiceRegion` sztringet az előfizetéséhez társított [régióra](regions.md) (ez a `westus` régió, ha az ingyenes próbaverzióra regisztrált).

1. Mentse a projekt módosításait.

## <a name="build-and-run-the-app"></a>Az alkalmazás létrehozása és futtatása

1. Hozza létre az alkalmazást. A menüsávon válassza a **Létrehozás** > **Megoldás fordítása** elemet. A kód fordításának hiba nélkül kell végbe mennie.

    ![A Visual Studio képernyőképe, amelyen ki van emelve a Megoldás fordítása lehetőség](media/sdk/qs-csharp-dotnetcore-windows-05-build.png "Sikeres létrehozás")

1. Indítsa el az alkalmazást. A menüsávon válassza a **Hibakeresés** > **Hibakeresés indítása** lehetőséget, vagy nyomja le az **F5** billentyűt.

    ![A Visual Studio képernyőképe, amelyen ki van emelve a Hibakeresés indítása lehetőség](media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "Hibakeresés indítása az alkalmazáson")

1. Megjelenik egy konzolablak, amely arra kéri Önt, hogy mondjon valamit. Mondjon ki egy angol nyelvű kifejezést vagy mondatot. A beszédet a beszédfelismerési szolgáltatás továbbítja, lefordítva és szövegbe átirata, amely ugyanabban az ablakban jelenik meg.

    ![A sikeres fordítás után a konzol kimenetének képernyőképe](media/sdk/qs-translate-csharp-dotnetcore-windows-output.png "Konzol kimenete sikeres fordítás után")


## <a name="next-steps"></a>További lépések

További minták, mint például a hangfájlok beszédének beolvasása és a lefordított szöveg szintetizált beszédként való kiírása, a GitHubon érhetők el.

> [!div class="nextstepaction"]
> [Minták C# feltárása a githubon](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Lásd még

- [Akusztikai modellek testreszabása](how-to-customize-acoustic-models.md)
- [Nyelvi modellek testreszabása](how-to-customize-language-model.md)
