---
title: Fejlesztés a Kubernetes Azure fejlesztési szóközöket (Visual Studio Code) használatával a .NET Core használatával
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.author: zarhoads
ms.date: 07/08/2019
ms.topic: quickstart
description: Gyors Kubernetes-fejlesztés tárolókkal és mikroszolgáltatásokkal az Azure-ban
keywords: Docker, Kubernetes, Azure, az AKS, az Azure Kubernetes Service, tárolók, Helm, a szolgáltatás háló, a szolgáltatás háló útválasztás, a kubectl, a k8s
manager: gwallace
ms.openlocfilehash: cc41e268678872910113c8e198bdaaac34232458
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706322"
---
# <a name="quickstart-develop-with-net-core-on-kubernetes-using-azure-dev-spaces-visual-studio-code"></a>Gyors útmutató: Fejlesztés a Kubernetes Azure fejlesztési szóközöket (Visual Studio Code) használatával a .NET Core használatával

Ebből az útmutatóból a következőket tudhatja meg:

- Az Azure Dev Spaces beállítása Managed Kubernetes-fürttel az Azure-ban.
- Iteratív fejlesztés kód a Visual Studio Code-tárolókban.
- A Visual Studio Code-ból a fejlesztési tárhely kódok hibakeresése.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Ha még nincs fiókja, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free).
- [A Visual Studio Code telepítése](https://code.visualstudio.com/download).
- A [Azure fejlesztési tárolóhelyek](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) és [ C# ](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) telepítve a Visual Studio Code-bővítmények.
- [Telepített Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Az Azure Kubernetes Service-fürt létrehozása

Az AKS-fürt létrehozásához szükséges egy [támogatott régió][supported-regions]. Az alábbi parancsokkal nevű erőforráscsoport létrehozása *MyResourceGroup* és a egy AKS-fürt nevű *MyAKS*.

```cmd
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --node-vm-size Standard_DS2_v2 --node-count 1 --disable-rbac --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Az AKS-fürt az Azure fejlesztési tárolóhelyek engedélyezése

Használja a `use-dev-spaces` parancs fejlesztési szóközt engedélyezni az AKS-fürt, és kövesse az utasításokat. Az alábbi parancs segítségével fejlesztési tárolóhelyek a *MyAKS* a fürt a *MyResourceGroup* csoportból, és létrehoz egy *alapértelmezett* fejlesztési terület.

```cmd
$ az aks use-dev-spaces -g MyResourceGroup -n MyAKS

'An Azure Dev Spaces Controller' will be created that targets resource 'MyAKS' in resource group 'MyResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'MyAKS' in resource group 'MyResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

## <a name="get-sample-application-code"></a>Minta az alkalmazáskód letöltése

Ebben a cikkben fogja használni a [Azure fejlesztési tárolóhelyek mintaalkalmazás](https://github.com/Azure/dev-spaces) bemutatása, Azure-fejlesztési szóközzel.

Klónozza az alkalmazást a Githubról.

```cmd
git clone https://github.com/Azure/dev-spaces
```

## <a name="prepare-the-sample-application-in-visual-studio-code"></a>A mintaalkalmazás a Visual Studio Code előkészítése

Nyissa meg a Visual Studio Code, kattintson a *fájl* majd *megnyitása...* , keresse meg a *fejlesztési-tárolóhelyek/samples/dotnetcore/első-lépések/webfrontend* könyvtárra, és kattintson *nyílt*.

Most már a *webfrontend* projektben nyissa meg a Visual Studio Code-ban. Az alkalmazás futtatásához a fejlesztési tárhelyre, hozzon létre a Docker és a Helm diagram eszközök, az Azure fejlesztési tárolóhelyek bővítmény használatával a parancs kívánt színére.

A Visual Studio Code-ban a Parancskatalógus megnyitásához kattintson a *nézet* majd *Parancskatalógus*. Kezdje el beírni `Azure Dev Spaces` , majd kattintson a `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`.

![Konfigurációs fájlok előkészítése Azure-fejlesztési tárolóhelyek](./media/common/command-palette.png)

Ha a Visual Studio Code-ot is megkéri, hogy konfigurálja a nyilvános végponthoz, válassza `Yes` engedélyezése egy nyilvános végpontot.

![Select public endpoint](media/common/select-public-endpoint.png)

Ez a parancs előkészíti a projekthez, hogy egy docker-fájl és a Helm-diagram létrehozása az Azure fejlesztési tárolóhelyek futtatásához. Is előállít egy *.vscode* könyvtárat, amely a projekt gyökerében konfigurációs hibakeresés.

## <a name="build-and-run-code-in-kubernetes-from-visual-studio"></a>Létrehozása és kód futtatása a Kubernetesben a Visual Studióból

Kattintson a *Debug* ikonra a bal oldalon, majd a *.NET Core indítása (AZDS)* tetején.

![](media/get-started-netcore/debug-configuration.png)

A parancs épít, és a szolgáltatás fut az Azure fejlesztési tárolóhelyek hibakeresési módban. A *terminálon* ablak alján látható a felépítési művelet kimenetében és URL-címek az Azure fejlesztési tárolóhelyek futó szolgáltatás. A *hibakeresési konzolt* napló kimenetét mutatja be.

> [!Note]
> Ha nem látja az Azure fejlesztési tárolóhelyek parancsok a *Parancskatalógus*, győződjön meg arról, hogy telepítette a [Visual Studio Code-bővítmény az Azure fejlesztési tárolóhelyek](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds). Ellenőrizze azt is nyitotta meg a *fejlesztési-tárolóhelyek/samples/dotnetcore/első-lépések/webfrontend* könyvtárat a Visual Studio Code-ot.

A nyilvános URL-cím megnyitásával fut a szolgáltatás megjelenik.

Kattintson a *Debug* majd *hibakeresés leállításához* a hibakereső leállítása.

## <a name="update-code"></a>Kód frissítése

A szolgáltatás frissített verziójának üzembe helyezéséhez bármilyen fájl frissítése a projekt és futtassa újra a *.NET Core indítása (AZDS)* . Példa:

1. Ha az alkalmazás továbbra is fut, kattintson a *Debug* majd *hibakeresés leállításához* leállításához.
1. Frissítés [a 22-es üzletági `Controllers/HomeController.cs` ](https://github.com/Azure/dev-spaces/blob/master/samples/dotnetcore/getting-started/webfrontend/Controllers/HomeController.cs#L22) való:
    
    ```csharp
    ViewData["Message"] = "Your application description page in Azure.";
    ```

1. Mentse a módosításokat.
1. Ismétlés *.NET Core indítása (AZDS)* .
1. Lépjen a futó szolgáltatás, és kattintson *kapcsolatos*.
1. Figyelje meg a módosításokat.
1. Kattintson a *Debug* majd *hibakeresés leállításához* az alkalmazás leállításához.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Beállítás és hibakeresési töréspontok használatával

Indítsa el a szolgáltatást a hibakeresés módban a *.NET Core indítása (AZDS)* .

Lépjen vissza a *Explorer* nézet kattintva *nézet* majd *Explorer*. Nyissa meg `Controllers/HomeController.cs` majd valahol a 22-es hiba helyezze a kurzort a sor. Állítson be egy töréspontot, nyomja le az *F9* , vagy kattintson *Debug* majd *töréspont*.

Egy böngészőben nyissa meg a szolgáltatást, és figyelje meg, hogy nem jelenik meg. Térjen vissza a Visual Studio Code-ot, és vizsgálja meg, 20 sor van kijelölve. A töréspont beállított sor 20 a szolgáltatás szünetel. A szolgáltatás folytatásához nyomja le az *F5* , vagy kattintson *Debug* majd *Folytatás*. Térjen vissza a böngészőben, és figyelje meg, hogy az üzenet jelenik meg.

Miközben fut a szolgáltatás a Kubernetes egy hibakeresővel, akkor a hibakeresési információkat, például a hívási verem, a helyi változókat és a kivétel adatai teljes hozzáféréssel rendelkezik.

A töréspont eltávolítása ehhez vigye a kurzort a 22-es sor `Controllers/HomeController.cs` és lenyomásával *F9*.

## <a name="update-code-from-visual-studio-code"></a>A Visual Studio Code-ból kódjának frissítése

A szolgáltatás futása hibakeresési módban, a 22-es sor frissítése `Controllers/HomeController.cs`. Példa:

```csharp
ViewData["Message"] = "Your application description page in Azure while debugging!";
```

Mentse a fájlt. Kattintson a *Debug* majd *indítsa újra a hibakeresés* vagy a a *hibakeresési eszköztár*, kattintson a *indítsa újra a hibakeresés* gombra.

![](media/common/debug-action-refresh.png)

Egy böngészőben nyissa meg a szolgáltatást, és figyelje meg, hogy a frissített üzenet jelenik meg.

Újraépítése és a egy új tárolórendszerképet minden alkalommal, amikor a kód módosításokat végzett, az Azure fejlesztési tárolóhelyek növekményes újbóli üzembe helyezés helyett újrafordítja a kódot a Szerkesztés/debug gyorsabb hurkot biztosít a meglévő tárolón belül.

## <a name="clean-up-your-azure-resources"></a>Az Azure-erőforrások törlése

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>További lépések

Ismerje meg, Azure fejlesztési tárolóhelyek segítségével miként több tárolóra kiterjedő összetettebb alkalmazásokat fejleszthet, és egyszerűsítését által biztosított együttműködési környezettel fejlesztési különböző verziói vagy az ágak a kódot a különböző tárolóhelyek segítségével. 

> [!div class="nextstepaction"]
> [Több tároló használata és fejlesztés csapatban](multi-service-netcore.md)


[supported-regions]: about.md#supported-regions-and-configurations