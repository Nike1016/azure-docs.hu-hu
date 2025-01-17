---
title: Fejlesztés és az Azure functions helyi futtatása |} A Microsoft Docs
description: Ismerje meg, hogyan kódolása és tesztelése a helyi számítógépen az Azure functions, az Azure Functions futtatása előtt.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: abb807262d976419a0c7700046bd8ad58322fc90
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537080"
---
# <a name="code-and-test-azure-functions-locally"></a>Kódolás és tesztelés az Azure Functions helyi

Bár a fejlesztés és tesztelés az Azure Functions tudja a [Azure Portal], sok fejlesztő előnyben részesítése egy helyi fejlesztési környezetet biztosít. Függvények megkönnyíti a kedvenc code szerkesztőt és fejlesztési eszközöket segítségével létrehozhat, és tesztelési függvényeket, a helyi számítógépen. A helyi függvények élő Azure-szolgáltatások is csatlakozhat, és Ön is a helyi számítógépen a teljes Functions futtatókörnyezete használatával hibakeresése.

## <a name="local-development-environments"></a>Helyi fejlesztési környezetek

Amelyben a helyi számítógépen fejleszt funkciók módja függ az [nyelvi](supported-languages.md) és eszközbeállításai. Az alábbi táblázatban a környezet támogatja a helyi fejlesztési:

|Környezet                              |Languages         |Leírás|
|-----------------------------------------|------------|---|
|[Visual Studio Code](functions-develop-vs-code.md)| [C#(osztálytár) ](functions-dotnet-class-library.md), [ C# szkript (.csx)](functions-reference-csharp.md), [JavaScript](functions-reference-node.md), [PowerShell](functions-create-first-function-powershell.md), [Python](functions-reference-python.md) | A [VS Code-bővítmény az Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) funkciók támogatása a VS Code hozzáadja. A Core Tools szükséges. Fejlesztési támogatja a Linux, MacOS és Windows, a verzió használatakor 2.x verzióját az Core Tools. További tudnivalókért lásd: [Visual Studio Code használatával az első függvény létrehozása](functions-create-first-function-vs-code.md). |
| [Parancssorba vagy terminálba](functions-run-local.md) | [C#(osztálytár) ](functions-dotnet-class-library.md), [ C# szkript (.csx)](functions-reference-csharp.md), [JavaScript](functions-reference-node.md), [PowerShell](functions-reference-powershell.md), [Python](functions-reference-python.md) | [Az Azure Functions Core Tools] biztosít a core-futtatókörnyezet és a sablonok funkciók, amelyek lehetővé teszik a helyi fejlesztési létrehozásához. Verzió 2.x verziója támogatja a fejlesztési Linux, MacOS és Windows. Minden környezet a helyi Functions futtatókörnyezetének Core Tools támaszkodnak. |
| [Visual Studio 2019](functions-develop-vs.md) | [C# (osztálytár)](functions-dotnet-class-library.md) | Az Azure Functions tools szerepelnek a **Azure-fejlesztési** számítási [Visual Studio 2019](https://www.visualstudio.com/vs/) és újabb verziók. Lehetővé teszi egy osztálytár függvényei összeállításához és a .dll fájl közzététele az Azure-bA. Magában foglalja a Core Tools való helyi tesztelést. További tudnivalókért lásd: [fejlesztése az Azure Functions Visual Studio használatával](functions-develop-vs.md). |
| [Maven](functions-create-first-java-maven.md) (különböző) | [Java](functions-reference-java.md) | Integrálható a Core Tools fejlesztés Java-funkciók engedélyezéséhez. Verzió 2.x verziója támogatja a fejlesztési Linux, MacOS és Windows. További tudnivalókért lásd: [az első függvény létrehozása a Java és Maven](functions-create-first-java-maven.md). Emellett támogatja a fejlesztési [Eclipse](functions-create-maven-eclipse.md) és [az IntelliJ IDEA](functions-create-maven-intellij.md) |

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

Ez a helyi fejlesztési környezet egyes lehetővé teszi a függvény létrehozása az alkalmazás projektek és a függvények előre definiált sablonok használatával hozzon létre új funkciók. A Core Tools egyes használja, hogy a tesztelése, és a valós Functions futtatókörnyezete ellen a függvények hibakeresése a saját számítógépére, ugyanúgy, mint bármely más alkalmazást. Is közzéteheti, függvényalkalmazás projektjét bármelyik ilyen környezetben az Azure-bA.  

## <a name="next-steps"></a>További lépések

+ További információ a helyi fejlesztési lefordított C# függvények használatával a Visual Studio 2019, lásd: [fejlesztése az Azure Functions Visual Studio használatával](functions-develop-vs.md).
+ A funkciókat a VS Code használata Mac, Linux vagy Windows-számítógépen a helyi fejlesztési kapcsolatos további információkért tekintse meg a [VS Code dokumentáció az Azure Functions](https://code.visualstudio.com/tutorials/functions-extension/getting-started).
+ A parancssorba vagy terminálba funkciókat fejlesztésével kapcsolatos további tudnivalókért lásd: [együttműködik az Azure Functions Core Tools](functions-run-local.md).

<!-- LINKS -->

[Az Azure Functions Core Tools]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure Portal]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
