---
title: Hozzon létre egy Kubernetes-fejlesztési tárolóhelyet a felhőben a .NET Core és a Visual Studio használatával
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.custom: vs-azure
ms.workload: azure-vs
author: zr-msft
ms.author: zarhoads
ms.date: 07/09/2018
ms.topic: tutorial
description: Gyors Kubernetes-fejlesztés tárolókkal és mikroszolgáltatásokkal az Azure-ban
keywords: Docker, Kubernetes, Azure, az AKS, az Azure Kubernetes Service, tárolók, Helm, a szolgáltatás háló, a szolgáltatás háló útválasztás, a kubectl, a k8s
ms.openlocfilehash: 4b37da8d31fc5ac605e59d9aceb456e996df438b
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503059"
---
# <a name="get-started-on-azure-dev-spaces-with-net-core-and-visual-studio"></a>Azure fejlesztői szóközöket a .NET Core és a Visual Studio használatának első lépései

Ebből az útmutatóból a következőket tudhatja meg:

- Az Azure Dev Spaces beállítása Managed Kubernetes-fürttel az Azure-ban.
- Iteratív kódfejlesztés tárolókban a Visual Studio használatával.
- Két külön szolgáltatás egymástól függetlenül történő fejlesztése, és a használt Kubernetes DNS-szolgáltatás észlelésével hívásindítás egy másik szolgáltatásba.
- A kód hatékony fejlesztése és tesztelése, csapatkörnyezetben.

> [!Note]
> **Ha elakad,** bármikor, tekintse meg a [hibaelhárítás](troubleshooting.md) szakaszban.


## <a name="create-a-kubernetes-cluster-enabled-for-azure-dev-spaces"></a>Azure Dev Spaceshez engedélyezett Kubernetes-fürt létrehozása

1. Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.
1. Válassza az **Erőforrás létrehozása** lehetőséget > keressen a **Kubernetes** kifejezésre > válassza a **Kubernetes Service** > **Létrehozás** elemet.

   A következő lépéseket minden egyes címsora alatt a *létre Kubernetes-fürt* alkotnak, és ellenőrizze a kiválasztott [régió támogatja az Azure fejlesztési tárolóhelyek][supported-regions].

   - **A PROJECT DETAILS**: válassza ki az Azure-előfizetés és a egy új vagy meglévő Azure-erőforráscsoportot.
   - **FÜRT ADATAI**: adjon meg egy nevet, régiót, verziót és DNS-névelőtagot az AKS-fürthöz.
   - **MÉRET**: válassza ki a virtuálisgép-méretet az AKS-ügynökcsomópontok számára, és a csomópontok számát. Ha most kezdte el az Azure Dev Spaces használatát, egyetlen csomópont elegendő az összes funkció kipróbálásához. A csomópontok száma bármikor egyszerűen beállítható a fürt telepítése után. Vegye figyelembe, hogy a virtuálisgép-méret az AKS-fürt létrehozását követően nem módosítható. Az AKS-fürt telepítése után azonban egyszerűen létrehozhat egy új, nagyobb virtuális gépekkel rendelkező AKS-fürtöt, majd a Dev Spaces használatával újratelepíthet erre a nagyobb fürtre, ha felskálázásra van szükség.

   ![Kubernetes konfigurációs beállításai](media/common/Kubernetes-Create-Cluster-2.PNG)


   Válassza ki **tovább: Hitelesítési** teljes.

1. Válassza ki a Szerepköralapú hozzáférés-vezérlés (RBAC) kívánt beállítását. Az Azure Dev Spaces engedélyezett és letiltott RBAC esetén is támogatja a fürtöket.

    ![RBAC-beállítás](media/common/k8s-RBAC.PNG)

1. Amikor végzett, válassza az **Áttekintés + létrehozás**, majd a **Létrehozás** lehetőséget.

## <a name="get-the-visual-studio-tools"></a>A Visual Studio eszközeinek beszerzése
Telepítse a legújabb verzióját, [Visual Studio](https://www.visualstudio.com/vs/). A Windows a Visual Studio 2019, telepítenie kell az Azure-fejlesztési számítási feladatot. A Windows a Visual Studio 2017, telepítenie kell az ASP.NET- és fejlesztési számítási feladatot, valamint [Visual Studio Tools for Kubernetes](https://aka.ms/get-azds-visualstudio).

## <a name="create-a-web-app-running-in-a-container"></a>Egy tárolóban futó webalkalmazás létrehozása

Ebben a szakaszban fog egy ASP.NET Core-webalkalmazás létrehozása és futtatása a Kubernetesben tárolóban lévő letöltés.

### <a name="create-an-aspnet-web-app"></a>ASP.NET-webapp létrehozása

A Visual studióban hozzon létre egy új projektet. Jelenleg a projektnek egy **ASP.NET Core-webalkalmazásnak** kell lennie. Adja a projektnek a „**webfrontend**” nevet.

![](media/get-started-netcore-visualstudio/NewProjectDialog1.png)

Válassza ki a **Webalkalmazás (Model-View-Controller)** sablont, és ellenőrizze, hogy a párbeszédpanel tetején a két legördülő listában a **.NET Core** és az **ASP.NET Core 2.0** van-e kijelölve. A projekt létrehozásához kattintson az **OK** gombra.

![](media/get-started-netcore-visualstudio/NewProjectDialog2.png)

### <a name="enable-dev-spaces-for-an-aks-cluster"></a>A DevSpaces engedélyezése egy AKS-fürtön

Az imént létrehozott projektben válassza ki az **Azure Dev Spaces** lehetőséget az indítási beállítások legördülő menüjéből, ahogy az az alábbi képen látható.

![](media/get-started-netcore-visualstudio/LaunchSettings.png)

Az ezt követően megjelenő párbeszédpanelen ellenőrizze, hogy a megfelelő fiókkal van-e bejelentkezve, majd válasszon ki egy meglévő Kubernetes-fürtöt.

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog.PNG)

A **Tér** legördülő menüt egyelőre hagyja a `default` alapértelmezett értéken. Erről a beállításról a későbbiekben kaphat további információt. Jelölje be a **Nyilvánosan hozzáférhető** jelölőnégyzetet, hogy a webalkalmazás nyilvános végpontról is elérhető legyen. Ez a beállítás nem szükséges, de hasznos lehet néhány fogalom bemutatásához az útmutató későbbi szakaszában. De ne aggódjon, mindkét esetben végezhet a webhelyen hibakeresést a Visual Studio segítségével.

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog2.png)

A fürt kiválasztásához vagy létrehozásához kattintson az **OK** gombra.

Ha egy olyan fürtöt választ, amely nincs az Azure Dev Spaceshez konfigurálva, a rendszer egy üzenetben rákérdez, hogy szeretné-e konfigurálni.

![](media/get-started-netcore-visualstudio/Add-Azure-Dev-Spaces-Resource.png)

Válassza az **OK** gombot.

> [!IMPORTANT]
> Az Azure fejlesztési tárolóhelyek konfigurációs folyamat eltávolítja a `azds` névtér a fürtben, ha az már létezik.

 Egy háttérművelet fogja végrehajtani a konfigurálást. Ez eltarthat néhány percig. Ha meg szeretné nézni, hogy tart-e még a folyamat, vigye az egérmutatót az állapotsor bal alsó sarkában lévő **Háttérfolyamatok** ikon fölé, az alábbi ábrán látható módon.

![](media/get-started-netcore-visualstudio/BackgroundTasks.PNG)

> [!Note]
> Amíg a Dev Spaces-tér létrehozása sikeresen le nem zárul, nem tud hibakeresést végezni az alkalmazáson.

### <a name="look-at-the-files-added-to-project"></a>A projekthez adott fájlok ellenőrzése
Amíg a Dev Spaces-tér létrehozására vár, tekintse meg, milyen fájlokat rendelt a projekthez a rendszer, amikor egy Dev Spaces-tér használatát választotta.

Először is láthatja, hogy a rendszer hozzáadott egy `charts` nevű mappát, amelyben előállított egy [Helm-diagramot](https://docs.helm.sh) az alkalmazásához. Ezek a fájlok szükségesek az alkalmazás Dev Spaces-térben való üzembe helyezéséhez.

A rendszer hozzáadott egy `Dockerfile` nevű fájlt is. Ez a fájl olyan információt tartalmaz, amely az alkalmazás szabványos Docker-formátumba való csomagolásához szükséges.

Végül látni fog egy `azds.yaml` nevű fájlt, amely a Dev Spaces-tér által igényelt fejlesztéskori konfigurációt tartalmazza.

![](media/get-started-netcore-visualstudio/ProjectFiles.png)

## <a name="debug-a-container-in-kubernetes"></a>Tároló hibakeresése a Kubernetesben
Amint a Dev Spaces-tér létrehozása sikeresen lezárul, hibakeresést végezhet az alkalmazáson. Állítson be egy töréspontot a kódban, például a `HomeController.cs` fájl 20. sorában, ahol a `Message` változó van beállítva. Nyomja le az **F5** billentyűt a hibakeresés indításához. 

A Visual Studio kommunikál a Dev Spaces-térrel, hogy az hozza létre és helyezze üzembe az alkalmazást, majd nyisson meg egy böngészőt a futó webalkalmazással. Úgy tűnhet, hogy a tároló futtatása helyileg történik, de valójában a Dev Spaces-térben fut az Azure-ban. A localhost cím oka az, hogy az Azure Dev Spaces egy ideiglenes SSH-alagutat hoz létre az AKS-ben futó tárolóhoz.

Kattintson az oldal tetején lévő **About** (Információ) hivatkozásra a töréspont aktiválásához. Ugyanúgy teljes körű hozzáférése van a hibakeresési információkhoz, mintha helyileg futna a kód (pl. hívási verem, helyi változók, kivételek adatai stb.).

## <a name="iteratively-develop-code"></a>Iteratív kódfejlesztés

Az Azure Dev Spaces nem csupán a Kubernetesben futó kódok lekérésére szolgál – a segítségével gyorsan és iteratívan lehet megtekinteni a kódmódosítások életbe lépését a felhőben lévő Kubernetes-környezetben.

### <a name="update-a-content-file"></a>Tartalomfájlok frissítése


1. Keresse meg a `./Views/Home/Index.cshtml` fájlt, és szerkessze a HTML-kódot. Módosítsa például [. sor, amely beolvas 73 `<h2>Application uses</h2>` ](https://github.com/Azure/dev-spaces/blob/master/samples/dotnetcore/getting-started/webfrontend/Views/Home/Index.cshtml#L73) a következőhöz hasonló: 
  
    ```html
    <h2>Hello k8s in Azure!</h2>`
    ```

2. Mentse a fájlt.
3. Lépjen a böngészőre, és frissítse az oldalt. A weboldalnak meg kell jelenítenie a frissített HTML-tartalmat.

Mi történt? A tartalomfájlok (például HTML és CSS) módosításait nem szükséges egy .NET Core webalkalmazásban újrafordítani, így az aktív F5-munkamenet automatikusan szinkronizálja a módosított tartalomfájlokat az Azure-ban futó tárolóba, így azonnal megtekintheti a tartalmak módosításait.

### <a name="update-a-code-file"></a>Kódfájlok frissítése
A kódfájlok frissítése egy kicsit több munkát igényel, mert a .NET Core-alkalmazásnak újra létre kell hoznia és elő kell állítania az alkalmazás frissített bináris fájljait.

1. Állítsa le a hibakeresőt a Visual Studióban.
1. Nyissa meg a `Controllers/HomeController.cs` nevű kódfájlt, és szerkessze az Információ oldalon megjelenő üzenetet: `ViewData["Message"] = "Your application description page.";`
1. Mentse a fájlt.
1. Nyomja le az **F5** billentyűt a hibakeresés újraindításához. 

Ahelyett, hogy a kód minden szerkesztése alkalmával újra létrehozna és üzembe helyezne egy új tárolórendszerképet, ami általában sok időt vesz igénybe, az Azure Dev Spaces növekményesen újrafordítja a kódot a meglévő tárolón belül, hogy gyorsabb szerkesztési/hibakeresési ciklust biztosítson.

Frissítse a webalkalmazást a böngészőben, és lépjen az Információ oldalra. Az egyedi üzenetnek meg kell jelennie a felhasználói felületen.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További tudnivalók több szolgáltatást fejlesztési](multi-service-netcore-visualstudio.md)


[supported-regions]: about.md#supported-regions-and-configurations