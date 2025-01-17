---
title: Az oktatóanyag egy Azure-függvényt az Azure IoT Edge - eszköz üzembe helyezése |} A Microsoft Docs
description: Ebben az oktatóanyagban fejlesztése az Azure IoT Edge-modul adatbázisként működik, akkor telepítheti az edge-eszköz.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/25/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 592aac7e66e1f0720a203c24ac9f000951cfe6f6
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68839526"
---
# <a name="tutorial-deploy-azure-functions-as-iot-edge-modules"></a>Oktatóanyag: Azure functions üzembe helyezése IoT Edge modulként

Az Azure Functions használatával olyan kódot helyezhet üzembe, amely közvetlenül az Azure IoT Edge-eszközökön valósítja meg az üzleti logikát. Ez az oktatóanyag végigvezeti az érzékelőktől kapott adatokat szűrő Azure-függvény létrehozásának és a szimulált IoT Edge-eszközön való üzembe helyezésének lépésein. Az Azure IoT Edge üzembe helyezése szimulált eszközön [Windows](quickstart.md) vagy [Linux](quickstart-linux.md) rendszeren című rövid útmutatóban létrehozott szimulált IoT Edge-eszközt fogja használni. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Azure-függvény létrehozása a Visual Studio Code használatával.
> * Docker-rendszerkép létrehozása és közzététele a tárolóregisztrációs adatbázisban a VS Code és a Docker használatával.
> * A modul üzembe helyezése egy tárolóregisztrációs adatbázisból az IoT Edge-eszközön.
> * Szűrt adatok megtekintése.

<center>

![Diagram – oktatóanyag architektúrája, fázis és üzembe helyezés funkció modul](./media/tutorial-deploy-function/functions-architecture.png)
</center>

>[!NOTE]
>Az Azure Function moduljai [nyilvános előzetes verzióban](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) érhetők el az Azure IoT Edge szolgáltatásban. 

Az ebben az oktatóanyagban létrehozott Azure-függvény szűri az eszköze által létrehozott hőmérsékletadatokat. A függvény csak akkor küld felfelé irányuló üzeneteket az Azure IoT Hubra, amikor a hőmérséklet egy megadott küszöbérték felett van. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag megkezdése előtt el kellett volna végeznie az előző oktatóanyagot a fejlesztői környezet létrehozásához a Linux-tárolók fejlesztéséhez: [IoT Edge modulok fejlesztése Linux](tutorial-develop-for-linux.md)-eszközökhöz. Az oktatóanyag elvégzésével a következő előfeltételek szükségesek: 

* Egy ingyenes vagy standard szintű [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban.
* [Azure IoT Edge rendszert futtató Linux-eszköz](quickstart-linux.md)
* Egy tároló-beállításjegyzék, például [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* A [Visual Studio Code](https://code.visualstudio.com/) az [Azure IoT-eszközökkel](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)van konfigurálva.
* A [Docker CE](https://docs.docker.com/install/) Linux-tárolók futtatására van konfigurálva.

Ha IoT Edge-modult szeretne létrehozni a Azure Functionsban, telepítse a következő további előfeltételeket a fejlesztői gépre: 

* [C# bővítmény a Visual Studio Code-hoz (szolgáltató: OmniSharp) ](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).

## <a name="create-a-function-project"></a>Függvényprojekt létrehozása

Az előfeltételekben telepített Visual Studio Code-hoz készült Azure IoT Tools felügyeleti képességeket és egyes kódrészleteket biztosít. Ebben a szakaszban a Visual Studio Code használatával készíthet Azure-függvényt tartalmazó IoT Edge-megoldást. 

### <a name="create-a-new-project"></a>Új projekt létrehozása

Hozzon C# létre egy Function Solution-sablont, amelyet testreszabhat saját kódjával.

1. Nyissa meg a Visual Studio Code-ot a fejlesztői gépen.

2. A **View (Nézet)**  > **Command Palette (Parancskatalógus)** elem kiválasztásával nyissa meg a VS Code parancskatalógusát.

3. A Command paletta írja be és futtassa a parancsot **Azure IoT Edge: Új IoT Edge megoldás**. Kövesse a parancskatalógusban található utasításokat a megoldás létrehozásához.

   | Mező | Érték |
   | ----- | ----- |
   | Select folder (Mappa kiválasztása) | Válassza ki azt a helyet a fejlesztői gépen, ahol a VS Code létre fogja hozni a megoldásfájlokat. |
   | Provide a solution name (Megoldásnév megadása) | Adjon meg egy leíró nevet a megoldáshoz, például **FunctionSolution**, vagy fogadja el az alapértelmezett. |
   | Select module template (Modulsablon kiválasztása) | Válasszon **az Azure Functions - C#** . |
   | Provide a module name (Modulnév megadása) | A modulnak adja a **CSharpFunction** nevet. |
   | Provide Docker image repository for the module (Docker-rendszerkép adattárának megadása a modulhoz) | Egy rendszerképadattár a tárolóregisztrációs adatbázis nevét és a tárolórendszerkép nevét tartalmazza. A tárolórendszerkép előre fel van töltve az előző lépésből. Cserélje le a **localhost:5000** értéket az Azure-beli tárolóregisztrációs adatbázis bejelentkezési kiszolgálójának értékére. A bejelentkezési kiszolgálót a tárolóregisztrációs adatbázis Áttekintés lapján kérheti le az Azure Portalon. Néz ki a végső karakterláncban \<beállításjegyzék neve\>.azurecr.io/CSharpFunction. |

   ![Docker-rendszerkép adattárának megadása](./media/tutorial-deploy-function/repository.png)

### <a name="add-your-registry-credentials"></a>A regisztrációs adatbázis hitelesítő adatainak hozzáadása

A környezeti fájl tárolja a tárolóregisztrációs adatbázis hitelesítő adatait, és megosztja őket az IoT-Edge futtatókörnyezettel. A futtatókörnyezetnek szüksége van ezekre a hitelesítő adatokra a privát rendszerképek letöltéséhez az IoT Edge-eszközre.

1. A VS Code Explorerben nyissa meg a .env fájlt.
2. Adja meg az Azure Container Registryből kimásolt **felhasználónevet** és **jelszót** a megfelelő mezőkben.
3. Mentse el ezt a fájlt.

### <a name="select-your-target-architecture"></a>Válassza ki a cél architektúrát

A Visual Studio Code jelenleg Linux AMD64 és Linux rendszerű ARM32v7-eszközökhöz is fejleszt C modulokat. Ki kell választania, hogy melyik architektúrát célozza meg az egyes megoldásokkal, mivel a tárolót az egyes architektúrák típusainál eltérően építették és futtatják. Az alapértelmezett érték a Linux AMD64. 

1. Nyissa meg a parancssort, **és keressen rá Azure IoT Edge: Állítsa be az Edge-megoldás**alapértelmezett célként megadott platformját, vagy válassza a parancsikon ikont az ablak alján található oldalsó sávban. 

2. A parancs palettáján válassza ki a cél architektúrát a lehetőségek listájából. Ebben az oktatóanyagban egy Ubuntu rendszerű virtuális gépet használunk IoT Edge eszközként, így megtarthatja az alapértelmezett **amd64**-t. 

### <a name="update-the-module-with-custom-code"></a>A modul módosítása egyéni kóddal

Vegyünk fel néhány további kódot, hogy a modul feldolgozza az üzeneteket a peremen, mielőtt továbbítaná őket a IoT Hubba.

1. A Visual Studio Code-ban nyissa meg a **modulok** > **CSharpFunction** > **CSharpFunction.cs**.

1. Cserélje le a tartalmát a **CSharpFunction.cs** fájlt a következő kóddal. Ez a kód megkapja a telemetriai adatokat gyűjthessen környezeti és a gép hőmérséklet, és csak akkor továbbítja az üzenet továbbítása az IoT Hub, ha a gép hőmérséklet egy meghatározott küszöbérték fölött van.

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.IO;
   using System.Text;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Client;
   using Microsoft.Azure.WebJobs;
   using Microsoft.Azure.WebJobs.Extensions.EdgeHub;
   using Microsoft.Azure.WebJobs.Host;
   using Microsoft.Extensions.Logging;
   using Newtonsoft.Json;

   namespace Functions.Samples
   {
       public static class CSharpFunction
       {
           [FunctionName("CSharpFunction")]
           public static async Task FilterMessageAndSendMessage(
               [EdgeHubTrigger("input1")] Message messageReceived,
               [EdgeHub(OutputName = "output1")] IAsyncCollector<Message> output,
               ILogger logger)
           {
               const int temperatureThreshold = 20;
               byte[] messageBytes = messageReceived.GetBytes();
               var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

               if (!string.IsNullOrEmpty(messageString))
               {
                   logger.LogInformation("Info: Received one non-empty message");
                   // Get the body of the message and deserialize it.
                   var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

                   if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
                   {
                       // Send the message to the output as the temperature value is greater than the threshold.
                       var filteredMessage = new Message(messageBytes);
                       // Copy the properties of the original message into the new Message object.
                       foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
                       {filteredMessage.Properties.Add(prop.Key, prop.Value);}
                       // Add a new property to the message to indicate it is an alert.
                       filteredMessage.Properties.Add("MessageType", "Alert");
                       // Send the message.
                       await output.AddAsync(filteredMessage);
                       logger.LogInformation("Info: Received and transferred a message with temperature above the threshold");
                   }
               }
           }
       }
       //Define the expected schema for the body of incoming messages.
       class MessageBody
       {
           public Machine machine {get; set;}
           public Ambient ambient {get; set;}
           public string timeCreated {get; set;}
       }
       class Machine
       {
           public double temperature {get; set;}
           public double pressure {get; set;}
       }
       class Ambient
       {
           public double temperature {get; set;}
           public int humidity {get; set;}
       }
   }
   ```

1. Mentse a fájlt.

## <a name="build-your-iot-edge-solution"></a>Az IoT Edge-megoldás összeállítása

Az előző szakaszban létrehozott egy IoT Edge-megoldást, és hozzáadott egy kódot a **CSharpFunction** függvényhez, amely kiszűri az olyan üzeneteket, ahol a jelentett géphőmérséklet alacsonyabb az elfogadható határértéknél. Most létre kell hoznia a megoldást tárolórendszerképként, és le kell küldenie a tárolóregisztrációs adatbázisba.

Ebben a szakaszban a tároló-beállításjegyzékhez tartozó hitelesítő adatokat a második alkalommal adja meg (az első a IoT Edge-megoldás **. env** fájljában található). ehhez jelentkezzen be helyileg a fejlesztői gépről, hogy a Visual Studio Code képes legyen képeket leküldeni a beállításjegyzék.

1. A VS Code integrált termináljának megnyitásához válassza a **View** (Nézet)  > **Terminal** (Terminál) elemet. 

2. Az integrált terminálban az alábbi paranccsal jelentkezzen be a tárolóregisztrációs adatbázisba. Használja azt a felhasználónevet és bejelentkezési kiszolgálót, amelyet az Azure Container Registry-adatbázisból másolt ki korábban.

    ```csh/sh
    docker login -u <ACR username> <ACR login server>
    ```

    Amikor a rendszer kéri a jelszót, illessze be a jelszót (nem jelenik meg a terminál ablakban) a tároló-beállításjegyzékben, majd nyomja le az **ENTER**billentyűt.

    ```csh/sh
    Password: <paste in the ACR password and press enter>
    Login Succeeded
    ```

3. A VS Code Explorerben kattintson a jobb gombbal a deployment.template.json fájlra, és válassza a **Build and Push IoT Edge solution** (IoT Edge-megoldás összeállítása és leküldése) lehetőséget. 

Amikor a megoldás összeállítására utasítja a Visual Studio Code-ot, az elsőként lekéri az adatokat az üzembehelyezési sablonból, és létrehoz egy deployment.json nevű fájlt egy új **config** nevű mappában. Ezután futtatja a következő két parancsot az integrált terminálon: `docker build` és `docker push`. A két parancs létrehozza a kódot, tárolóba helyezi a függvényeket, majd leküldi a kódot a megoldás inicializálásakor megadott tárolóregisztrációs adatbázisba. 

## <a name="view-your-container-image"></a>A tárolórendszerkép megtekintése

A Visual Studio Code sikert jelző üzenetet küld, miután leküldte a tárolórendszerképet a tárolóregisztrációs adatbázisba. Ha személyesen is ellenőrizni szeretné a műveletet, keresse meg a rendszerképet az adatbázisban. 

1. Az Azure Portalon tallózzon az Azure Container Registry-adatbázisra. 
2. Válassza az **Adattárak** elemet.
3. Ekkor a **csharpfunction** adattár jelenik meg a listában. Válassza ki az adattárat a további részletek megtekintéséhez.
4. A **Címkék** szakaszban megjelenik a **0.0.1-amd64** címke. Ez a címke jelzi a létrehozott rendszerkép verziószámát és platformját. Ezek az értékek a CSharpFunction mappa module.json fájljában találhatók meg. 

## <a name="deploy-and-run-the-solution"></a>A megoldás üzembe helyezése és futtatása

A függvények modulját a gyors útmutatókban bemutatott módon az Azure Portal segítségével helyezheti üzembe az IoT Edge-eszközökön. A modulokat emellett a Visual Studio Code használatával is üzembe helyezheti és monitorozhatja. A következő részekben a VS Code-hoz készült Azure IoT Tools (az előfeltételek részben felsorolt) használható. Ha még nem tette meg, telepítse most a bővítményt. 

1. A VS Code Explorerben bontsa ki az **Azure IoT Hub Devices** (Azure IoT Hub-eszközök) szakaszt. 

2. Kattintson a jobb gombbal az IoT Edge-eszköz nevére, majd válassza a **Create Deployment for single device** (Üzembe helyezés létrehozása egyetlen eszközhöz) parancsot. 

3. Navigáljon a **CSharpFunction** függvényt tartalmazó megoldásmappához. Nyissa meg a konfigurációs mappát, válassza ki a **deployment.json** fájlt, és válassza a **kiválasztása peremhálózati üzembe helyezési Manifest**.

4. Frissítse az **Azure IoT Hub Devices** (Azure IoT Hub-eszközök) szakaszt. Meg kell jelennie az új **CSharpFunction** , amelyen a **SimulatedTemperatureSensor** modul, valamint a **$edgeAgent** és a **$edgeHub**fut. Eltarthat néhány másodpercig jelenik meg az új modulokat. Az IoT Edge-eszköz rendelkezik az új központi telepítési adatok kinyerése az IoT Hub, indítsa el az új tárolókat és jelentést készít az állapot vissza az IoT hubnak. 

   ![Üzembe helyezett modulok megtekintése a VS Code-ban](./media/tutorial-deploy-function/view-modules.png)

## <a name="view-generated-data"></a>A létrejött adatok megtekintése

Az Azure IoT hub futtatásával **megtekintheti az IoT hub-ra érkező összes üzenetet: A beépített esemény-végpont** figyelésének megkezdése a parancs palettáján.

A nézetet szűrheti is, hogy csak a megjelölt eszközről az IoT Hub-központra érkező üzenetek jelenjenek meg. Kattintson a jobb gombbal az eszközre az **Azure IoT hub-eszközök** szakaszban, majd válassza a **figyelés beépített esemény végpontjának elindítása**lehetőséget.

A figyelési üzenetek leállításához futtassa **az Azure IoT hub parancsot: A beépített esemény-végpont** figyelésének leállítása a parancs palettáján. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy a következő ajánlott cikkel folytatja, megtarthatja és újból felhasználhatja a létrehozott erőforrásokat és konfigurációkat. Azt is megteheti, hogy ugyanezt az IoT Edge-eszközt használja teszteszközként. 

Ellenkező esetben a díjak elkerülése érdekében törölheti a jelen cikkben létrehozott helyi konfigurációkat és Azure-erőforrásokat. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy Azure-függvénymodult hozott létre olyan kóddal, amely szűri az IoT Edge-eszköz által létrehozott nyers adatokat. Ha készen áll a saját moduljainak létrehozására, többet is megtudhat a [Visual Studio Code](how-to-vs-code-develop-module.md)-hoz való Azure IoT Edge fejlesztéséről. 

Továbbléphet a következő oktatóanyagokra, és megtudhatja, milyen más módokon alakíthatja üzleti megállapításokká ezeket az adatokat a peremhálózaton az Azure IoT Edge segítségével.

> [!div class="nextstepaction"]
> [Átlagértékek keresése lebegő Azure Stream Analytics-ablakban](tutorial-deploy-stream-analytics.md)
