---
title: Módosítás, betanítási C# alkalmazás,-Luis
titleSuffix: Azure Cognitive Services
description: Ebben a rövid C#-útmutatóban kimondott példaszövegeket ad egy otthonautomatizálási alkalmazáshoz, és betanítja az alkalmazást.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: 55251a553921e947e7c6522677e51e4eb1d3fa5b
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619950"
---
# <a name="quickstart-change-model-using-c"></a>Gyors útmutató: Modell módosítása a következő használatávalC#

[!INCLUDE [Quickstart introduction for change model](../../../includes/cognitive-services-luis-qs-change-model-intro-para.md)]

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [Quickstart prerequisites for changing model](../../../includes/cognitive-services-luis-qs-change-model-prereq.md)]
* Legújabb [**Visual Studio Community kiadás**](https://www.visualstudio.com/downloads/).
* Telepített C# programozási nyelv.
* [JsonFormatterPlus](https://www.nuget.org/packages/JsonFormatterPlus) és [CommandLine](https://www.nuget.org/packages/CommandLineParser/) NuGet-csomagok

[!INCLUDE [Code is available in Azure-Samples GitHub repo](../../../includes/cognitive-services-luis-qs-change-model-luis-repo-note.md)]

## <a name="example-utterances-json-file"></a>Kimondott példaszövegek JSON-fájlja

[!INCLUDE [Quickstart explanation of example utterance JSON file](../../../includes/cognitive-services-luis-qs-change-model-json-ex-utt.md)]

## <a name="create-quickstart-code"></a>A rövid útmutató kódjának létrehozása 

A Visual Studióban hozzon létre egy új **klasszikus Windows asztali konzolt** a .NET-keretrendszer használatával. Adja meg a `ConsoleApp1`projekt nevét.

![Visual Studio-projekt típusa](./media/luis-quickstart-cs-add-utterance/vs-project-type.png)

### <a name="add-the-systemweb-dependency"></a>A System.Web függőség hozzáadása

A Visual Studio-projektnek szüksége van a következőre: **System.Web**. A Megoldáskezelő kattintson a jobb gombbal a **hivatkozások** elemre, és válassza a **hivatkozás hozzáadása** elemet a szerelvények szakaszban.

![A System.Web hivatkozás hozzáadása](./media/luis-quickstart-cs-add-utterance/system.web.png)

### <a name="add-other-dependencies"></a>Más függőségek hozzáadása

A Visual Studio-projektnek szüksége van a következőkre: **JsonFormatterPlus** és **CommandLineParser**. A Solution Explorer (Megoldáskezelő) ablakban kattintson a jobb gombbal a **References** (Hivatkozások) elemre, és válassza a **Manage NuGet Packages...** (NuGet-csomagok kezelése...) lehetőséget. Tallózással keresse meg és adja hozzá a két csomagot. 

![Külső függőségek hozzáadása](./media/luis-quickstart-cs-add-utterance/add-dependencies.png)


### <a name="write-the-c-code"></a>A C#-kód megírása
A **Program.cs** fájlnak a következőkből kell állnia:

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace ConsoleApp1
{
    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

Frissítse a függőségeket, hogy a következők legyenek:

   [!code-csharp[Add the dependencies](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=1-11 "Add the dependencies")]


Adja hozzá a LUIS-azonosítókat és -sztringeket a **Program** osztályhoz.

   [!code-csharp[Add the LUIS IDs and strings](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=19-30&dedent=8 "Add the LUIS IDs and strings")]

Adjon egy osztályt a **Program** osztályhoz a parancssori paraméterek kezelése céljából.

   [!code-csharp[Add class to manage command line parameters.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=32-46 "Add class to manage command-line parameters.")]

Adja hozzá a GET kérelemmetódust a **Program** osztályhoz.

   [!code-csharp[Add the GET request.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=49-59 "Add the GET request.")]


Adja hozzá a POST kérelemmetódust a **Program** osztályhoz. 

   [!code-csharp[Add the POST request.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=60-76 "Add the POST request.")]

Adjon hozzá kimondott példaszövegeket a fájlmetódusból a **Program** osztályhoz.

   [!code-csharp[Add example utterances from file.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=77-86 "Add example utterances from file.")]

Miután alkalmazta a módosításokat a modellre, tanítsa be a modellt. Adja hozzá a metódust a **Program** osztályhoz.

   [!code-csharp[After the changes are applied to the model, train the model.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=87-96 "After the changes are applied to the model, train the model.")]

Előfordulhat, hogy a képzés nem fejeződik be azonnal. Az állapot ellenőrzésével győződhet meg arról, hogy a képzés befejeződött. Adja hozzá a metódust a **Program** osztályhoz.

   [!code-csharp[Training may not complete immediately, check status to verify training is complete.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=97-103 "Training may not complete immediately, check status to verify training is complete.")]

Adja hozzá a fő kódot a parancssori argumentumok kezeléséhez. Adja hozzá a metódust a **Program** osztályhoz.

   [!code-csharp[To manage command line arguments, add the main code.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=104-137 "To manage command-line arguments, add the main code.")]

### <a name="copy-utterancesjson-to-output-directory"></a>Az utterances.json másolása a kimeneti könyvtárba

A megoldáskezelő adja hozzá a `utterances.json` parancsot a jobb gombbal a megoldáskezelő projekt nevére, majd válassza a **Hozzáadás**, majd a **meglévő elem**lehetőséget. Válassza ki `utterances.json` a fájlt. Ezzel hozzáadja a fájlt a projekthez. Ezután fel kell venni a kimeneti könyvtárba. Kattintson a jobb gombbal `utterances.json` a elemre, és válassza a **Tulajdonságok**lehetőséget. A tulajdonságok ablakban jelölje meg a `Content` **Build Action** (Létrehozási művelet) elemét és a `Copy Always` **Copy to Output Directory** (Másolás a kimeneti könyvtárba) elemét.  

![A JSON-fájl megjelölése tartalomként](./media/luis-quickstart-cs-add-utterance/content-properties.png)

## <a name="build-code"></a>Kód buildelése

Buildelje a kódot a Visual Studióban. 

## <a name="run-code"></a>Kód futtatása

A projekt /bin/Debug könyvtárában futtassa az alkalmazást egy parancssorból. 

```console
ConsoleApp1.exe --add utterances.json --train --status
```

A parancssor megjeleníti a kimondott szövegek hozzáadása API meghívásának eredményét. 

[!INCLUDE [Quickstart response from API calls](../../../includes/cognitive-services-luis-qs-change-model-json-results.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Miután végzett a rövid útmutatóval, távolítsa el a rövid útmutatóban létrehozott összes fájlt. 

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"] 
> [LUIS-alkalmazások felépítése programozott módon](luis-tutorial-node-import-utterances-csv.md) 
