---
title: Modul fejlesztése Windows-eszközökhöz – Azure IoT Edge | Microsoft Docs
description: Ez az oktatóanyag végigvezeti a fejlesztői gép és a felhő erőforrásainak beállításán, hogy IoT Edge modulokat fejlesszen a Windows-eszközökön Windows-tárolók használatával
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/15/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 66fa7c2f61af250e4b63b67f6941bed768bd94c4
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69541930"
---
# <a name="tutorial-develop-iot-edge-modules-for-windows-devices"></a>Oktatóanyag: IoT Edge-modulok létrehozása Windows-eszközökhöz

A Visual Studióval hozhat létre és helyezhet üzembe programkódot a IoT Edge rendszert futtató Windows-eszközökön.

A gyors útmutatóban létrehozott egy IoT Edge eszközt egy Windows rendszerű virtuális géppel, és üzembe helyezett egy előre elkészített modult az Azure piactéren. Ez az oktatóanyag végigvezeti a saját programkódjának IoT Edge eszközön való fejlesztéséhez és üzembe helyezéséhez szükséges lépéseken. Ez az oktatóanyag hasznos előfeltételt nyújt a többi oktatóanyaghoz, amely részletesen ismerteti az egyes programozási nyelveket és az Azure-szolgáltatásokat. 

Ez az oktatóanyag egy  **C# modul Windows-eszközre**történő központi telepítésének példáját használja. Ez a példa azért lett kiválasztva, mert ez a leggyakoribb fejlesztési forgatókönyv. Ha más nyelven fejleszti a fejlesztést, vagy az Azure-szolgáltatások modulként való üzembe helyezését tervezi, akkor ez az oktatóanyag továbbra is hasznos lehet a fejlesztői eszközök megismeréséhez. A fejlesztési fogalmak megismerése után kiválaszthatja az Ön által választott nyelvet vagy Azure-szolgáltatást, és megismerheti a részleteket. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Állítsa be a fejlesztői gépet.
> * Hozzon létre egy új projektet a Visual studióhoz készült IoT Edge Tools használatával.
> * Készítse el a projektet tárolóként, és tárolja egy Azure Container registryben.
> * A kód üzembe helyezése egy IoT Edge eszközön. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="key-concepts"></a>Fő fogalmak

Ez az oktatóanyag végigvezeti egy IoT Edge modul fejlesztésén. Egy *IoT Edge modul*, vagy néha csak a *modul* rövid, egy olyan tároló, amely végrehajtható kódot tartalmaz. IoT Edge eszközön egy vagy több modult is telepíthet. A modulok olyan konkrét feladatokat látnak el, mint például az érzékelőkből származó adatok beolvasása, az adatelemzési vagy adattisztítási műveletek végrehajtása, illetve üzenetek küldése egy IoT hubhoz. További információ: Azure IoT Edge- [modulok megismerése](iot-edge-modules.md).

IoT Edge modulok fejlesztésekor fontos megérteni a fejlesztési gép és a cél IoT Edge eszköz közötti különbséget, ahol a modul végül üzembe lesz helyezve. A modul kódjának tárolására felépített tárolónak meg kell egyeznie a *céleszköz*operációs rendszerével (os). A Windows-tárolók fejlesztéséhez ez a koncepció egyszerűbb, mivel a Windows-tárolók csak Windows operációs rendszereken futnak. Előfordulhat például, hogy a Windows fejlesztői gépe segítségével modulokat készít a linuxos IoT Edge eszközökhöz. Ebben az esetben meg kell győződnie arról, hogy a fejlesztői számítógép Linux-tárolókat futtatott. Ebben az oktatóanyagban ne feledje, hogy a *fejlesztői gép operációs rendszere* és a *tároló operációs rendszer*közötti különbség.

Ez az oktatóanyag a IoT Edge rendszert futtató Windows-eszközöket célozza meg. A Windows IoT Edge-eszközök Windows-tárolókat használnak. Javasoljuk, hogy a Visual Studio használatával fejlesszen a Windows-eszközökön, hogy ez az oktatóanyag milyen módon fog használatba venni. Használhatja a Visual Studio Code-ot is, de a két eszköz között eltérések vannak.

A következő táblázat a **Windows** -tárolók támogatott fejlesztési forgatókönyveit sorolja fel a Visual Studio Code és a Visual Studio alkalmazásban.

|   | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Azure-szolgáltatások** | Azure Functions <br> Azure Stream Analytics |   |
| **Nyelvek** | C#(a hibakeresés nem támogatott) | C <br> C# |
| **További információ** | [Azure IoT Edge a Visual Studio Code-hoz](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) | [Azure IoT Edge Tools for Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools)<br>[Azure IoT Edge Tools for Visual Studio 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) |

## <a name="prerequisites"></a>Előfeltételek

Egy fejlesztői gép:

* Windows 10 1809-es vagy újabb frissítéssel.
* A fejlesztési beállításoktól függően saját számítógépet vagy virtuális gépet is használhat.
* Telepítse a [Git](https://git-scm.com/) szoftvert. 

Egy Azure IoT Edge eszköz a Windows rendszerben:

* Javasoljuk, hogy ne futtasson IoT Edge a fejlesztői gépen, hanem használjon külön eszközt. A fejlesztési gép és a IoT Edge eszköz közötti különbségtétel pontosabban tükrözi a valódi üzembe helyezési forgatókönyvet, és segít a különböző fogalmak közvetlen megtartásában.
* Ha nem érhető el második eszköz, a rövid útmutatóval IoT Edge eszközt hozhat létre az Azure-ban egy [Windows rendszerű virtuális géppel](quickstart.md).

Felhőerőforrások:

* Egy ingyenes vagy standard szintű [IoT hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban. 

## <a name="install-container-engine"></a>Tároló-motor telepítése

IoT Edge modulok tárolóként vannak csomagolva, ezért a tárolók létrehozásához és kezeléséhez szüksége lesz egy tároló motorra a fejlesztői gépen. Azt javasoljuk, hogy a Docker Desktopot a számos funkciójának és a népszerű tároló-motornak a használatával fejlessze. A Windows rendszerű számítógépeken a Docker Desktop segítségével válthat a Linux-tárolók és a Windows-tárolók között, így egyszerűen fejleszthet különböző típusú IoT Edge eszközökhöz tartozó modulokat. 

Telepítse a Docker dokumentációját a fejlesztői gépre való telepítéshez: 

* [A Windows rendszerhez készült Docker Desktop telepítése](https://docs.docker.com/docker-for-windows/install/)

  * A Windows rendszerhez készült Docker Desktop telepítésekor megkérdezi, hogy Linux-vagy Windows-tárolókat kíván-e használni. Ebben az oktatóanyagban használja a **Windows**-tárolókat. További információ: váltás a [Windows és a Linux rendszerű tárolók között](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).


## <a name="set-up-visual-studio-and-tools"></a>A Visual Studio és az eszközök beállítása

A Visual studióhoz készült IoT-bővítmények segítenek IoT Edge modulok fejlesztésében. Ezek a bővítmények a Project sablonjait biztosítják, automatizálják az üzembe helyezési jegyzék létrehozását, és lehetővé teszik IoT Edge-eszközök figyelését és kezelését. Ebben a szakaszban a Visual studiót és a IoT Edge-bővítményt telepíti, majd beállíthatja az Azure-fiókját, hogy az IoT Hub erőforrásokat a Visual studióból kezelhesse. 

Ez az oktatóanyag a Visual Studio 2019 fejlesztési lépéseit tanítja. Ha a Visual Studio 2017-es verzióját (15,7-es vagy újabb verzió) használja, a lépések nagyon hasonlóak. Ha inkább a Visual Studio Code-ot szeretné használni, tekintse meg a [Visual Studio Code használata a Azure IoT Edge moduljainak fejlesztéséhez és hibakereséséhez](how-to-vs-code-develop-module.md)című témakör útmutatását. 

1. Készítse elő a Visual Studio 2019-et a fejlesztői gépen. 

   * Ha még nem rendelkezik a Visual Studióval a fejlesztői gépen, [telepítse a Visual studio 2019](https://docs.microsoft.com/visualstudio/install/install-visual-studio) -et a következő munkaterhelésekkel: 

      * Azure-fejlesztés
      * Asztali fejlesztés aC++
      * .NET Core platformfüggetlen fejlesztés

   * Ha már rendelkezik a Visual Studio 2019-rel a fejlesztői gépen, kövesse a [Visual Studio módosítása](https://docs.microsoft.com/visualstudio/install/modify-visual-studio) a szükséges számítási feladatok hozzáadásához című témakör lépéseit.

2. Töltse le és telepítse a Visual Studio 2019 [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) bővítményét. 

   Ha a Visual Studio 2017-es verzióját (15,7-es vagy újabb verziót) használja, töltse le és telepítse a [Visual studio 2017 Azure IoT Edge eszközeit](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools).

3. A telepítések befejezése után nyissa meg a Visual Studio 2019 alkalmazást, és válassza a **Folytatás kód nélkül**lehetőséget.

4. Válassza a**Cloud Explorer** **megtekintése** > lehetőséget. 

5. Válassza a profil ikont a Cloud Explorerben, és jelentkezzen be az Azure-fiókjába, ha még nincs bejelentkezve. 

6. Miután bejelentkezett, megjelenik az Azure-előfizetések listája. Bontsa ki az IoT hub-előfizetést. 

7. Az előfizetés alatt bontsa ki a **IoT hubok** csomópontot, majd az IoT hubot. Ekkor meg kell jelennie a IoT-eszközök listájának, és az Intéző használatával felügyelheti őket. 

   ![Hozzáférés IoT Hub erőforrásokhoz a Cloud Explorerben](./media/tutorial-develop-for-windows/cloud-explorer-view-hub.png)

[!INCLUDE [iot-edge-create-container-registry](../../includes/iot-edge-create-container-registry.md)]

## <a name="create-a-new-module-project"></a>Új modul-projekt létrehozása

A Azure IoT Edge Tools bővítmény a Visual Studióban az összes támogatott IoT Edge modul nyelvéhez biztosít Project sablonokat. Ezekben a sablonokban minden olyan fájl és kód szerepel, amelyre a IoT Edge teszteléséhez egy munkamodult kell telepítenie, vagy egy kiindulási pontot kell adnia a sablon saját üzleti logikával való testreszabásához. 

1. Válassza a **fájl** > **új** > **projekt... lehetőséget.**

2. Az új projekt ablakban keresse meg **IoT Edge** , és válassza ki a **Azure IoT Edge (Windows amd64)** projektet. Kattintson a **Tovább** gombra. 

   ![Új Azure IoT Edge projekt létrehozása](./media/tutorial-develop-for-windows/new-project.png)

3. Az új projekt konfigurálása ablakban nevezze át a projektet és a megoldást valami olyan leíróra, mint a **CSharpTutorialApp**. A projekt létrehozásához kattintson a **Létrehozás** gombra.

   ![Új Azure IoT Edge projekt konfigurálása](./media/tutorial-develop-for-windows/configure-project.png)

4. A modul hozzáadása ablakban konfigurálja a projektet a következő értékekkel: 

   | Mező | Value |
   | ----- | ----- |
   | Visual Studio-sablon | Válassza **C#** ki a modult. | 
   | Modulnév | Fogadja el az alapértelmezett **IotEdgeModule1**. | 
   | Adattár URL-címe | Egy rendszerképadattár a tárolóregisztrációs adatbázis nevét és a tárolórendszerkép nevét tartalmazza. A tároló képe előre fel van töltve a modul projekt neve értékből. Cserélje le a **localhost:5000** értéket az Azure-beli tárolóregisztrációs adatbázis bejelentkezési kiszolgálójának értékére. A bejelentkezési kiszolgálót a tárolóregisztrációs adatbázis Áttekintés lapján kérheti le az Azure Portalon. <br><br> A rendszerkép utolsó tárháza a \<következőhöz\>hasonló: beállításjegyzék neve. azurecr.IO/iotedgemodule1. |

      ![A projekt konfigurálása a céleszköz, a modul típusa és a tároló-beállításjegyzék számára](./media/tutorial-develop-for-windows/add-module-to-solution.png)

5. A modul létrehozásához válassza a **Hozzáadás** lehetőséget. 

Ha az új projekt betöltődik a Visual Studio ablakába, szánjon egy kis időt a létrehozott fájlok megismerésére: 

* Egy **CSharpTutorialApp**nevű IoT Edge-projekt.
    * A **modulok** mappa mutatókat tartalmaz a projektben szereplő modulokhoz. Ebben az esetben csak IotEdgeModule1 kell lennie. 
    * A **Deployment. template. JSON** fájl egy olyan sablon, amely segítséget nyújt az üzembe helyezési jegyzék létrehozásában. Az *üzembe helyezési jegyzék* egy olyan fájl, amely pontosan meghatározza, hogy mely modulokat kívánja telepíteni az eszközön, hogyan legyenek konfigurálva, és hogyan kommunikálnak egymással és a felhővel. 
* Egy IoT Edge **IotEdgeModule1**nevű modul-projekt.
    * A **program.cs** fájl tartalmazza a Project C# sablonhoz tartozó alapértelmezett modul kódját. Az alapértelmezett modul beírja a forrásból a bemenetet, és átadja a IoT Hubnak. 
    * A **Module. JSON** fájl tartalmazza a modul részleteit, beleértve a teljes rendszerkép tárházát, a képváltozatot, valamint azt, hogy melyik Docker kell használni az egyes támogatott platformokhoz.

### <a name="provide-your-registry-credentials-to-the-iot-edge-agent"></a>Adja meg a beállításjegyzékbeli hitelesítő adatait a IoT Edge-ügynöknek

A IoT Edge futtatókörnyezetnek szüksége van a beállításjegyzékbeli hitelesítő adataira, hogy a tároló lemezképeit a IoT Edge eszközre húzza. Adja hozzá ezeket a hitelesítő adatokat a központi telepítési sablonhoz. 

1. Nyissa meg a **Deployment. template. JSON** fájlt.

2. Keresse meg a **registryCredentials** tulajdonságot a $edgeAgent kívánt tulajdonságban. 

3. Frissítse a tulajdonságot a hitelesítő adataival, a következő formátumban: 

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "<username>",
       "password": "<password>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

4. Mentse a Deployment. template. JSON fájlt. 

### <a name="review-the-sample-code"></a>Tekintse át a mintakódot

A létrehozott megoldási sablon egy IoT Edge modulhoz tartozó mintakód-kódot tartalmaz. Ez a mintakód egyszerűen fogad üzeneteket, majd továbbítja azokat a következőre:. A folyamat funkciója a IoT Edge fontos koncepcióját mutatja be, ami azt szemlélteti, hogyan kommunikálnak a modulok egymással.

Minden modul több *bemeneti* és *kimeneti* várólistával is rendelkezhet a kódban. Az eszközön futó IoT Edge hub az egyik modul kimenetében lévő üzeneteket egy vagy több modul bemenetére irányítja. A bemenetek és kimenetek deklarálása adott nyelve a nyelvek között változik, de a koncepció az összes modul esetében azonos. További információ a modulok közötti útválasztásról: [útvonalak deklarálása](module-composition.md#declare-routes).

A Project C# sablonhoz tartozó mintakód a [ModuleClient osztályt](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet) használja a .net-hez készült IoT hub SDK-ból. 

1. A **program.cs** fájlban keresse meg a **SetInputMessageHandlerAsync** metódust.

2. A [SetInputMessageHandlerAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.setinputmessagehandlerasync?view=azure-dotnet) metódus beállít egy bemeneti várólistát a bejövő üzenetek fogadásához. Tekintse át ezt a metódust, és nézze meg, hogyan inicializál egy **input1**nevű bemeneti várólistát. 

   ![A bemeneti név megkeresése a SetInputMessageHandlserAsync konstruktorban](./media/tutorial-develop-for-windows/declare-input-queue.png)

3. Ezután keresse meg a **SendEventAsync** metódust.

4. A [SendEventAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.sendeventasync?view=azure-dotnet) metódus feldolgozza a fogadott üzeneteket, és beállítja a kimeneti várólistát, hogy átadja őket. Tekintse át ezt a metódust, és tekintse meg, hogy inicializál egy **output1**nevű kimeneti várólistát. 

   ![A kimeneti név megkeresése a SendEventAsync konstruktorban](./media/tutorial-develop-for-windows/declare-output-queue.png)

5. Nyissa meg a **Deployment. template. JSON** fájlt.

6. Keresse meg a $edgeAgent kívánt tulajdonságok **modulok** tulajdonságát. 

   Itt két modulnak kell szerepelnie. Az első a **tempSensor**, amely alapértelmezés szerint az összes sablonban szerepel, hogy szimulált hőmérsékleti értékeket biztosítson, amelyeket a modulok tesztelésére használhat. A második a projekt részeként létrehozott **IotEdgeModule1** -modul.

   Ez a modul-tulajdonság azt deklarálja, hogy mely modulok szerepeljenek az eszközön vagy az eszközökön való üzembe helyezésben. 

7. Keresse meg a $edgeHub kívánt tulajdonságok Routes ( **útvonalak** ) tulajdonságát. 

   A függvények egyike, ha az IoT Edge hub-modul az üzenetek továbbítására szolgál a központi telepítés összes modulja között. Tekintse át az útvonalak tulajdonságban található értékeket. A **IotEdgeModule1ToIoTHub**első útvonala helyettesítő karaktert ( **\*** ) használ a IotEdgeModule1 modulban található bármelyik kimeneti sorból érkező üzenet belefoglalásához. Ezek az üzenetek bekerülnek *$upstreamba*, amely egy fenntartott név, amely IoT hub jelzi. A második útvonal, a **sensorToIotEdgeModule1**, a tempSensor-modultól érkező üzeneteket fogadja, és átirányítja őket a IotEdgeModule1 modul *input1* bemeneti sorába. 

   ![Útvonalak áttekintése a Deployment. template. JSON fájlban](./media/tutorial-develop-for-windows/deployment-routes.png)


## <a name="build-and-push-your-solution"></a>És a megoldás leküldéses

Áttekintette a modul kódját és a telepítési sablont, hogy megismerje a kulcsfontosságú telepítési fogalmakat. Most már készen áll arra, hogy létrehozza a IotEdgeModule1-tároló rendszerképét, és leküldi a tároló-beállításjegyzékbe. A Visual studióhoz készült IoT Tools bővítménnyel ez a lépés az üzembe helyezési jegyzékfájlt is létrehozza a sablonfájl információi és a modul információi alapján a megoldás fájljaiban. 

### <a name="sign-in-to-docker"></a>Bejelentkezés a Docker-be

Adja meg a tároló beállításjegyzékbeli hitelesítő adatait a Docker számára a fejlesztői gépen, így a tároló rendszerképét leküldheti a beállításjegyzékben. 

1. Nyisson meg egy PowerShell-vagy egy parancssort.

2. Jelentkezzen be a Docker-be a beállításjegyzék létrehozása után mentett Azure Container Registry hitelesítő adatokkal. 

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Biztonsági figyelmeztetés jelenhet meg, `--password-stdin`amely a használatát javasolja. Habár az ajánlott eljárás az éles környezetekben javasolt, az oktatóanyag hatókörén kívül esik. További információkért lásd a Docker [bejelentkezési](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) referenciáját.

### <a name="build-and-push"></a>Létrehozás és leküldés

A fejlesztői gép most már rendelkezik hozzáféréssel a tároló beállításjegyzékéhez, és a IoT Edge-eszközök is elérhetők lesznek. Itt az ideje, hogy bekapcsolja a projekt kódját egy tároló képébe. 

1. Kattintson a jobb gombbal a **CSharpTutorialApp** projekt mappájára, és válassza a **IoT Edge modulok kiépítése és leküldése**lehetőséget. 

   ![IoT Edge-modulok létrehozása és leküldése](./media/tutorial-develop-for-windows/build-and-push-modules.png)

   A build és a push parancs három műveletet indít el. Először létrehoz egy új mappát a konfigurációban, amely tartalmazza a teljes telepítési jegyzékfájlt, kiépítve a telepítési sablonban és más megoldási fájlokban található információkat. Másodszor, futtatja `docker build` a tároló rendszerképét a célként megadott architektúra megfelelő Docker alapján. Ezután futtatja `docker push` , hogy leküldi a rendszerkép-tárházat a tároló-beállításjegyzékbe. 

   Ez a folyamat az első alkalommal több percet is igénybe vehet, de gyorsabb a parancsok következő futtatásakor. 

2. Nyissa meg a **Deployment. Windows-amd64. JSON** fájlt az újonnan létrehozott konfigurációs mappában. (Előfordulhat, hogy a konfigurációs mappa nem jelenik meg a Megoldáskezelő a Visual Studióban. Ha ez a helyzet, jelölje be az **összes fájl megjelenítése** ikont a megoldáskezelő tálcán.)

3. Keresse meg a IotEdgeModule1 szakasz **rendszerkép** paraméterét. Figyelje meg, hogy a rendszerkép tartalmazza a teljes rendszerkép tárházát, amely a Module. JSON fájl nevét, verzióját és architektúrájának címkéjét tartalmazza.

4. Nyissa meg a **Module. JSON** fájlt a IotEdgeModule1 mappában. 

5. Módosítsa a modul rendszerképének verziószámát. (A verzió, nem a $schema verziója.) Megnövelheti például a javítás verziószámát a **0.0.2** , mintha egy kis javítást hajtottunk végre a modul kódjában. 

   >[!TIP]
   >A modul verziói lehetővé teszik a verziókövetés használatát, és lehetővé teszik, hogy a frissítések éles környezetben való telepítése előtt tesztelje a módosításokat az eszközök egy kis készletén. Ha nem növeli a modul verzióját a kiépítése és a kitolása előtt, írja felül a tárházat a tároló beállításjegyzékében. 

6. Mentse a módosításokat a Module. JSON fájlba.

7. Kattintson ismét a **CSharpTutorialApp** projekt mappájára, majd válassza újra a **IoT Edge-modulok kiépítése és leküldése** lehetőséget. 

8. Nyissa meg újra az **Deployment. Windows-amd64. JSON** fájlt. Figyelje meg, hogy a build és a leküldéses parancs ismételt futtatásakor nem jött létre új fájl. Ehelyett ugyanez a fájl frissült, hogy tükrözze a módosításokat. A IotEdgeModule1-rendszerkép most a tároló 0.0.2-verziójára mutat. Az üzembe helyezési jegyzékben szereplő változás arról tájékoztatja a IoT Edge eszközt, hogy egy modul új verziója van a lekéréshez. 

9. A build és a leküldéses parancs végrehajtásának további ellenőrzéséhez lépjen a [Azure Portal](https://portal.azure.com) , és keresse meg a tároló beállításjegyzékét. 

10. A tároló beállításjegyzékében válassza a **tárak** , majd a **iotedgemodule1**lehetőséget. Ellenőrizze, hogy a rendszerkép mindkét verzióját leküldte-e a beállításjegyzékbe.

    ![A tároló beállításjegyzékének mindkét rendszerkép-verziójának megtekintése](./media/tutorial-develop-for-windows/view-repository-versions.png)

### <a name="troubleshoot"></a>Hibaelhárítás

Ha hibát tapasztal a modul rendszerképének létrehozásakor és leküldésekor, gyakran a Docker-konfigurációval kell végrehajtania a fejlesztői gépen. A konfiguráció áttekintéséhez használja a következő ellenőrzéseket: 

* Futtatta a `docker login` parancsot a tároló-beállításjegyzékből másolt hitelesítő adatok használatával? Ezek a hitelesítő adatok eltérnek az Azure-ba való bejelentkezéshez használttól. 
* Helyes a tároló-adattár? A tároló beállításjegyzékének megfelelő neve és a megfelelő modul neve? A IotEdgeModule1 mappában Nyissa meg a **Module. JSON** fájlt, és keresse meg a következőt:. A tárház értékének a következőhöz hasonlóan  **\<\>** kell kinéznie: Registry Name. azurecr.IO/iotedgemodule1. 
* Ha a modul **IotEdgeModule1** eltérő nevet használt, az adott név konzisztens a megoldás során?
* A gép ugyanazon típusú tárolókat futtatja, amelyeket Ön épít? Ez az oktatóanyag a Windows IoT Edge eszközökhöz készült, ezért a Visual Studio-fájloknak Windows **-amd64** kiterjesztéssel kell rendelkezniük, és a Docker desktopnak Windows-tárolókat kell futtatnia. 

## <a name="deploy-modules-to-device"></a>Modulok üzembe helyezése az eszközön

Ellenőrizte, hogy a rendszer a tároló beállításjegyzékében tárolja a beépített tároló lemezképeit, ezért itt az ideje, hogy egy eszközre telepítse őket. Ellenőrizze, hogy a IoT Edge eszköz működik-e. 

1. Nyissa meg a Cloud Explorert a Visual Studióban, és bontsa ki az IoT hub részleteit. 

2. Válassza ki a telepíteni kívánt eszköz nevét. A **műveletek** listában válassza a **központi telepítés létrehozása**elemet.

   ![Üzemelő példány létrehozása egyetlen eszközhöz](./media/tutorial-develop-for-windows/create-deployment.png)


3. A Fájlkezelőben navigáljon a projekt konfigurációs mappájához, és válassza ki a **Deployment. Windows-amd64. JSON** fájlt. Ez a fájl gyakran a következő helyen található:`C:\Users\<username>\source\repos\CSharpTutorialApp\CSharpTutorialApp\config\deployment.windows-amd64.json`

   Ne használja a Deployment. template. JSON fájlt, amely nem rendelkezik a teljes modul képének értékeivel. 

4. Bontsa ki a IoT Edge eszközének részleteit a Cloud Explorerben, és tekintse meg az eszközön található modulokat.

5. Az eszköz állapotának frissítéséhez használja a refresh ( **frissítés** ) gombot, és ellenőrizze, hogy a tempSensor és a IotEdgeModule1 modulok telepítve vannak-e az eszközön. 


   ![A IoT Edge eszközön futó modulok megtekintése](./media/tutorial-develop-for-windows/view-running-modules.png)

## <a name="view-messages-from-device"></a>Üzenetek megtekintése az eszközről

A IotEdgeModule1 kód üzeneteket fogad a bemeneti várólistán keresztül, és továbbítja azokat a kimeneti várólistán keresztül. Az üzembe helyezési jegyzék olyan útvonalakat jelentett be, amelyek üzeneteket továbbítanak a tempSensor a IotEdgeModule1-be, majd továbbítottak üzeneteket a IotEdgeModule1-ből a IoT Hubba. A Visual studióhoz készült Azure IoT Edge Tools lehetővé teszi, hogy az egyes eszközökről érkező IoT Hub megtekintse az üzeneteket. 

1. A Visual Studio Cloud Explorerben válassza ki annak a IoT Edge eszköznek a nevét, amelyet központilag telepített. 

2. A **műveletek** menüben válassza a **figyelés beépített esemény végpontjának elindítása**lehetőséget.

3. Tekintse meg a Visual Studio **output (kimenet** ) szakaszát, és tekintse meg az IoT hub-ra érkező üzeneteket. 

   Eltarthat néhány percig, amíg mindkét modul el nem indul. Az IoT Edge futtatókörnyezetnek meg kell kapnia az új üzembe helyezési jegyzékfájlt, le kell kérnie a modul lemezképeit a tároló futtatókörnyezetből, majd el kell indítania az új modulokat. Ha 

   ![A bejövő eszköz Felhőbeli üzeneteinek megtekintése](./media/tutorial-develop-for-windows/view-d2c-messages.png)

## <a name="view-changes-on-device"></a>Az eszköz változásainak megtekintése

Ha szeretné megtekinteni, hogy mi történik az eszközön, használja az ebben a szakaszban található parancsokat az eszközön futó IoT Edge futtatókörnyezet és modulok vizsgálatához. 

Az ebben a szakaszban található parancsok a IoT Edge eszközre, nem pedig a fejlesztői gépre vonatkoznak. Ha virtuális gépet használ a IoT Edge eszközhöz, kapcsolódjon hozzá most. Az Azure-ban nyissa meg a virtuális gép áttekintés lapját, és válassza a **Kapcsolódás** lehetőséget a távoli asztali kapcsolat eléréséhez. Az eszközön nyisson meg egy parancsot vagy egy PowerShell-ablakot a `iotedge` parancsok futtatásához.

* Tekintse meg az eszközre telepített összes modult, és tekintse meg az állapotukat:

   ```cmd
   iotedge list
   ```

   Négy modulnak kell megjelennie: a két IoT Edge futásidejű modul, a tempSensor és a IotEdgeModule1. Mind a négynek szerepelnie kell futtatásként.

* Egy adott modul naplófájljainak vizsgálata:

   ```cmd
   iotedge logs <module name>
   ```

   A IoT Edge modulok megkülönböztetik a kis-és nagybetűket. 

   A tempSensor és a IotEdgeModule1 naplóknak meg kell jeleníteniük a feldolgozás alatt lévő üzeneteket. A edgeAgent modul felelős a többi modul indításához, így a naplók az üzembe helyezési jegyzék megvalósításával kapcsolatos információkat fognak tartalmazni. Ha valamelyik modul nem jelenik meg vagy nem fut, a edgeAgent-naplók valószínűleg a hibákkal fognak rendelkezni. A edgeHub modul felelős a modulok és a IoT Hub közötti kommunikációért. Ha a modulok működése folyamatban van, de az üzenetek nem érkeznek meg az IoT hub-ba, akkor a edgeHub-naplók valószínűleg a hibákkal fognak rendelkezni. 

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban beállíthatja a Visual Studio 2019-et a fejlesztői gépen, és üzembe helyezheti az első IoT Edge modulját. Most, hogy már ismeri az alapfogalmakat, próbáljon hozzá funkciókat hozzáadni egy modulhoz, hogy az áthaladó adatok elemezhetők legyenek. Válassza ki a kívánt nyelvet: 

> [!div class="nextstepaction"] 
> [C](tutorial-c-module-windows.md)
> [C#](tutorial-csharp-module-windows.md)
