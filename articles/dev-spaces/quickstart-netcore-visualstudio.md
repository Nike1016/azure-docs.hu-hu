---
title: Hibakeresés és iteráció a Visual Studióval és a .NET Core-val az AK-val az Azure dev Spaces használatával
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.author: zarhoads
ms.date: 03/22/2019
ms.topic: quickstart
description: Gyors Kubernetes-fejlesztés tárolókkal és mikroszolgáltatásokkal az Azure-ban
keywords: Docker, Kubernetes, Azure, AK, Azure Kubernetes szolgáltatás, tárolók, Helm, Service Mesh, szolgáltatás háló útválasztás, kubectl, k8s
manager: gwallace
ms.custom: vs-azure
ms.workload: azure-vs
ms.openlocfilehash: ece47c86fba6bc975a4146f596fa001014352a4f
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68725846"
---
# <a name="quickstart-debug-and-iterate-with-visual-studio-and-net-core-on-kubernetes-with-azure-dev-spaces"></a>Gyors útmutató: Hibakeresés és iteráció a Visual Studióval és a .NET Core-val a Kubernetes az Azure dev Spaces használatával

Ebből az útmutatóból a következőket tudhatja meg:

- Az Azure Dev Spaces beállítása Managed Kubernetes-fürttel az Azure-ban.
- Iteratív kódfejlesztés tárolókban a Visual Studio használatával.
- A fürtön futó hibakeresési kód a Visual Studio használatával.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Ha még nincs fiókja, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free).
- Visual Studio 2019 Windows rendszeren, amelyen telepítve van az Azure fejlesztői munkaterhelése. A Windowshoz készült Visual Studio 2017 a webes fejlesztési munkaterhelés és a [Visual Studio Tools for Kubernetes](https://aka.ms/get-vsk8stools) telepítve is használható. Ha nincs telepítve a Visual Studio, töltse le [itt](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Azure Kubernetes Service-fürt létrehozása

A [támogatott régiókban][supported-regions]létre kell hoznia egy AK-fürtöt. Fürt létrehozása:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com)
1. Válassza *az + erőforrás létrehozása > Kubernetes szolgáltatás*lehetőséget. 
1. Adja megaz előfizetést, az _erőforráscsoportot_, a _Kubernetes-fürt nevét_, a régiót, a _Kubernetes-verziót_és a _DNS-név előtagját_.

    ![AK létrehozása a Azure Portalban](media/get-started-netcore-visualstudio/create-aks-portal.png)

1. Kattintson az *Áttekintés + létrehozás* elemre.
1. Kattintson a *Create* (Létrehozás) gombra.

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Az Azure dev-helyek engedélyezése az AK-fürtön

Navigáljon az AK-fürthöz a Azure Portalban, és kattintson a *dev Spaces*elemre. Módosítsa a *fejlesztői tárhelyek engedélyezése* *beállítást igen* értékre, és kattintson a *Mentés*gombra.

![Fejlesztői szóközök engedélyezése a Azure Portalban](media/get-started-netcore-visualstudio/enable-dev-spaces-portal.png)

## <a name="create-a-new-aspnet-web-app"></a>Új ASP.NET-Webalkalmazás létrehozása

1. Nyissa meg a Visual Studiót.
1. Új projekt létrehozása.
1. Válassza ki *ASP.net Core* webalkalmazást, és nevezze el a projekt webfelületét.
1. Kattintson az *OK* gombra.
1. Ha a rendszer kéri, válassza a *webalkalmazás (Model-View-Controller)* elemet a sablonhoz.
1. Válassza a *.net Core* és a *ASP.net Core 2,0* elemet a felső részen.
1. Kattintson az *OK* gombra.

## <a name="connect-your-project-to-your-dev-space"></a>A projekt összekötése a fejlesztői területtel

A projektben válassza az **Azure dev Spaces** lehetőséget az indítási beállítások legördülő listából az alább látható módon.

![](media/get-started-netcore-visualstudio/LaunchSettings.png)

Az Azure dev Spaces párbeszédpanelen válassza ki az előfizetését és az *Azure Kubernetes*-fürtöt. Hagyja üresen a *helyet* az *alapértelmezett* értékre, és engedélyezze a *nyilvánosan elérhető* jelölőnégyzetet. Kattintson az *OK* gombra.

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog.png)

Ez a folyamat egy nyilvánosan elérhető URL-címmel telepíti a szolgáltatást az *alapértelmezett* fejlesztői tárhelyre. Ha egy olyan fürtöt választ, amely nincs az Azure Dev Spaceshez konfigurálva, a rendszer egy üzenetben rákérdez, hogy szeretné-e konfigurálni. Kattintson az *OK* gombra.

![](media/get-started-netcore-visualstudio/Add-Azure-Dev-Spaces-Resource.png)

Az *alapértelmezett* fejlesztői területen futó szolgáltatás nyilvános URL-címe a *kimeneti* ablakban jelenik meg:

```cmd
Starting warmup for project 'webfrontend'.
Waiting for namespace to be provisioned.
Using dev space 'default' with target 'MyAKS'
...
Successfully built 1234567890ab
Successfully tagged webfrontend:devspaces-11122233344455566
Built container image in 39s
Waiting for container...
36s

Service 'webfrontend' port 'http' is available at http://webfrontend.1234567890abcdef1234.eus.azds.io/
Service 'webfrontend' port 80 (http) is available at http://localhost:62266
Completed warmup for project 'webfrontend' in 125 seconds.
```

A fenti példában a nyilvános URL-cím http://webfrontend.1234567890abcdef1234.eus.azds.io/:. Navigáljon a szolgáltatás nyilvános URL-címére, és lépjen kapcsolatba a fejlesztői térben futó szolgáltatással.

Előfordulhat, hogy a folyamat letiltotta a szolgáltatáshoz való nyilvános hozzáférést. A nyilvános hozzáférés engedélyezéséhez frissítheti a [bejövő *értékeket a Values. YAML*][ingress-update].

## <a name="update-code"></a>Kód frissítése

Ha a Visual Studio továbbra is csatlakozik a fejlesztői területhez, kattintson a Leállítás gombra. A 20. sor `Controllers/HomeController.cs` módosítása a következőre:
    
```csharp
ViewData["Message"] = "Your application description page in Azure.";
```

Mentse a módosításokat, és indítsa el a szolgáltatást az **Azure dev Spaces** használatával az indítási beállítások legördülő menüből. Nyissa meg a szolgáltatás nyilvános URL-címét egy böngészőben, és kattintson *a névjegy*elemre. Figyelje meg, hogy a frissített üzenet jelenik meg.

Az új Container-lemezképek újraépítése és újratelepítése helyett az Azure dev Spaces fokozatosan újrafordítja a kódot a meglévő tárolóban, így gyorsabb szerkesztési/hibakeresési hurkot biztosít.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Töréspontok beállítása és használata hibakereséshez

Ha a Visual Studio továbbra is csatlakozik a fejlesztői területhez, kattintson a Leállítás gombra. Nyissa meg `Controllers/HomeController.cs` , majd kattintson a 20. sorban a kurzor mozgatásához. Ha a töréspontot az *F9 billentyűre* szeretné beállítani, vagy kattintson a *hibakeresés* lehetőségre, majd a *töréspontot*. Ha hibakeresési módban szeretné elindítani a szolgáltatást a fejlesztői térben, nyomja meg az *F5 billentyűt* , vagy kattintson a *hibakeresés elemre* , majd *indítsa el*a hibakeresést.

Nyissa meg a szolgáltatást egy böngészőben, és figyelje meg, hogy nem jelenik meg üzenet. Térjen vissza a Visual studióhoz, és figyelje meg, hogy a 20. sor ki van emelve. A beállított töréspont szüneteltette a szolgáltatást a 20. sorban. A szolgáltatás folytatásához nyomja meg az *F5 billentyűt* , vagy kattintson a *hibakeresés* gombra, és *folytassa*a művelettel. Térjen vissza a böngészőhöz, és figyelje meg, hogy az üzenet most megjelenik.

A szolgáltatás Kubernetes való futtatásakor a hibakeresőhöz csatolva teljes hozzáférése van a hibakeresési adatokhoz, például a hívási verem, a helyi változók és a kivételek adataihoz.

Távolítsa el a töréspontot úgy, hogy a kurzort a 20-as és az `Controllers/HomeController.cs` *F9 billentyű*megnyomásával helyezi el.

## <a name="clean-up-your-azure-resources"></a>Azure-erőforrások karbantartása

Navigáljon az erőforráscsoporthoz a Azure Portalon, majd kattintson az *erőforráscsoport törlése*elemre. Azt is megteheti, hogy az az [AK delete](/cli/azure/aks#az-aks-delete) parancsot használja:

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Több tároló használata és fejlesztés csapatban](multi-service-netcore-visualstudio.md)

[ingress-update]: how-dev-spaces-works.md#how-running-your-code-is-configured
[supported-regions]: about.md#supported-regions-and-configurations
