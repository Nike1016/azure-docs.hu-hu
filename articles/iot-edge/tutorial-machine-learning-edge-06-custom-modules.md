---
title: Egyéni modulok létrehozása és telepítése – Machine Learning a Azure IoT Edgeon | Microsoft Docs
description: Hozzon létre és helyezzen üzembe IoT Edge modulokat, amelyek a gépi tanulási modell segítségével dolgozzák fel az adatok a Leaf-eszközökről, majd az elemzések elküldésével IoT Hub.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/13/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4d03e5ee5faf39425e1bf927a3c0557b0ad01b82
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840106"
---
# <a name="tutorial-create-and-deploy-custom-iot-edge-modules"></a>Oktatóanyag: Egyéni IoT Edge-modulok létrehozása és üzembe helyezése

> [!NOTE]
> Ez a cikk egy sorozat részét képezi a Azure Machine Learning IoT Edge-on való használatáról szóló oktatóanyaghoz. Ha ezt a cikket közvetlenül megérkezett, javasoljuk, hogy kezdje a sorozat [első cikkével](tutorial-machine-learning-edge-01-intro.md) a legjobb eredmények érdekében.

Ebben a cikkben három IoT Edge-modult hozunk létre, amelyek üzeneteket fogadnak a Leaf-eszközökről, az adatok futtatását a gépi tanulási modellen keresztül, majd az elemzések továbbításával IoT Hub.

IoT Edge hub megkönnyíti a modul kommunikációját. Az IoT Edge hub használata a Message Broker számára egymástól független modulokat tart fenn. Modulok csak adja meg, amelyen a üzenetek és a kimeneteket, amelyhez a üzeneteket írhat elfogadják a bemeneteket kell.

Azt szeretnénk, hogy a IoT Edge eszköz négy dolgot hajtson végre nekünk:

* Adatok fogadása a Leaf-eszközökről
* Az adatküldő eszköz RUL előrejelzése
* Üzenet küldése csak az eszköz RUL IoT Hub (ez a függvény módosítható úgy, hogy csak akkor küldjön adatküldést, ha a RUL egy bizonyos szint alá csökken)
* Mentse a levélben tárolt eszközöket a IoT Edge eszköz helyi fájljába. Ezt az adatfájlt a rendszer rendszeresen feltölti IoT Hubre a gépi tanulási modell képzésének pontosításához. A fájlok feltöltése a folyamatos üzenetküldés helyett költséghatékony.

Ezeknek a feladatoknak a végrehajtásához három egyéni modult használunk:

* **RUL osztályozó:** A turboFanRulClassifier modul, amelyet a betanításban hoztunk létre, [és üzembe helyezünk egy Azure Machine learning modellt](tutorial-machine-learning-edge-04-train-model.md) egy standard Machine learning modul, amely egy "amlInput" nevű bemenetet és egy "amlOutput" nevű kimenetet tesz elérhetővé. A "amlInput" azt várja, hogy a bemenet pontosan ugyanúgy nézzen ki, mint az ACI-alapú webszolgáltatásnak továbbított bemenet. Hasonlóképpen, a "amlOutput" ugyanazokat az adatokkal adja vissza, mint a webszolgáltatás.

* **Avro-író:** Ez a modul fogadja az üzeneteket a "avroModuleInput" bemeneten, és Avro formátumban megőrzi az üzenetet a lemezre a későbbi feltöltéshez IoT Hub.

* **Útválasztó modul:** Az útválasztó modul üzeneteket fogad az alsóbb rétegbeli eszközökről, majd formázza és elküldi az üzeneteket az osztályozó számára. A modul ezután fogadja az üzeneteket az osztályozó és továbbítja az üzenetet a Avro-író modulnak. Végül a modul csak a RUL-előrejelzést küldi el a IoT Hub.

  * Bemenetek
    * **deviceInput**: üzenetek fogadása a Leaf-eszközökről
    * **rulInput:** üzenetek fogadása a "amlOutput"

  * Kimenetek
    * **besorolás:** üzenetek küldése "amlInput"
    * **writeAvro:** üzenetek küldése "avroModuleInput"
    * **toIotHub:** üzeneteket küld $upstream, amelyek továbbítják az üzeneteket a csatlakoztatott IoT hub

Az alábbi ábrán a modulok, a bemenetek, a kimenetek és a IoT Edge hub útvonalak láthatók a teljes megoldáshoz:

![IoT Edge három modul architektúrájának diagramja](media/tutorial-machine-learning-edge-06-custom-modules/modules-diagram.png)

A cikkben ismertetett lépéseket általában egy felhőalapú fejlesztő hajtja végre.

## <a name="create-a-new-iot-edge-solution"></a>Új IoT Edge-megoldás létrehozása

Két Azure Notebooks második végrehajtása során létrehoztunk és közzétettünk egy RUL modellt tartalmazó tároló-rendszerképet. Azure Machine Learning a lemezkép létrehozási folyamatának részeként, amely úgy van felépítve, hogy a lemezkép Azure IoT Edge modulként legyen üzembe helyezhető. Ebben a lépésben létrehozunk egy Azure IoT Edge megoldást az "Azure Machine Learning" modul használatával, és a modult a Azure Notebooks használatával közzétett rendszerképre irányítjuk.

1. Nyisson meg egy távoli asztali munkamenetet a fejlesztői számítógépén.

2. Nyissa meg a **C\\mappát:\\forrás IoTEdgeAndMlSample** a Visual Studio Code-ban.

3. Kattintson a jobb gombbal az Explorer panelre (az üres területre), és válassza az **új IoT Edge megoldást**.

    ![Új IoT Edge megoldás létrehozása](media/tutorial-machine-learning-edge-06-custom-modules/new-edge-solution-command.png)

4. Fogadja el az alapértelmezett megoldás nevét **EdgeSolution**.

5. Válassza a **Azure Machine learning** lehetőséget a modul sablonként.

6. Nevezze el a modul **turbofanRulClassifier**.

7. Válassza ki a Machine learning-munkaterületet.

8. Válassza ki az Azure-jegyzetfüzet futtatásakor létrehozott rendszerképet.

9. Tekintse meg a megoldást, és figyelje meg a létrehozott fájlokat:

   * **deployment.template.json:** Ez a fájl tartalmazza a megoldás egyes moduljainak definícióját. Ebben a fájlban három szakaszt kell figyelnie:

     * **Beállításjegyzékbeli hitelesítő adatok:** Meghatározza a megoldásban használt egyéni tároló-nyilvántartások készletét. Jelenleg a Machine learning-munkaterületről kell tartalmaznia a beállításjegyzéket, amely a Azure Machine Learning rendszerképét tárolja. Tetszőleges számú tároló-bejegyzést használhat, de az egyszerűség kedvéért minden modul esetében ezt a beállításjegyzéket fogjuk használni

       ```json
       "registryCredentials": {
         "<your registry>": {
           "username": "$CONTAINER_REGISTRY_USERNAME_<your registry>",
           "password": "$CONTAINER_REGISTRY_PASSWORD_<your registry>",
           "address": "<your registry>.azurecr.io”
         }
       }
       ```

     * **Modulok** Ez a szakasz a felhasználó által definiált, a megoldáshoz tartozó modulok készletét tartalmazza. Megfigyelheti, hogy ez a szakasz jelenleg két modult tartalmaz: SimulatedTemperatureSensor és turbofanRulClassifier. A SimulatedTemperatureSensor a Visual Studio Code sablon telepítette, de erre a megoldásra nincs szükségünk. A SimulatedTemperatureSensor modul definícióját a modulok szakaszban törölheti. Vegye figyelembe, hogy a turbofanRulClassifier modul definíciója a tároló beállításjegyzékében lévő képre mutat. Ahogy további modulokat adunk a megoldáshoz, megjelennek ebben a szakaszban.

       ```json
       "modules": {
         "SimulatedTemperatureSensor": {
           "version": "1.0",
           "type": "docker",
           "status": "running",
           "restartPolicy": "always",
           "settings": {
             "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
             "createOptions": {}
           }
         },
         "turbofanRulClassifier": {
           "version": "1.0",
           "type": "docker",
           "status": "running",
           "restartPolicy": "always",
           "settings": {
             "image": "<your registry>.azurecr.io/edgemlsample:1",
             "createOptions": {}
           }
         }
       }
       ```

     * **Útvonalak:** ebben az oktatóanyagban egy kicsit is dolgozunk az útvonalakon. Az útvonalak határozzák meg, hogy a modulok hogyan kommunikáljanak egymással. A sablon által definiált két útvonal nem egyezik meg a szükséges útválasztással. Az első útvonal elküldi az összes adatokat az osztályozó kimenetéről a IoT Hub ($upstream). A másik útvonal a SimulatedTemperatureSensor, amelyet most töröltek. Törölje a két alapértelmezett útvonalat.

       ```json
       "$edgeHub": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "routes": {
             "turbofanRulClassifierToIoTHub": "FROM /messages/modules/turbofanRulClassifier/outputs/\* INTO $upstream",
             "sensorToturbofanRulClassifier": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\\"/modules/turbofanRulClassifier/inputs/input1\\")"
           },
           "storeAndForwardConfiguration": {
             "timeToLiveSecs": 7200
           }
         }
       }
       ```

   * **Deployment. debug. template. JSON:** ez a fájl a Deployment. template. JSON hibakeresési verziója. A telepítési. template. JSON fájlban lévő összes változást át kell tükrözni ebbe a fájlba.

   * **. env:** ebben a fájlban kell megadnia a beállításjegyzék eléréséhez szükséges felhasználónevet és jelszót.

      ```env
      CONTAINER_REGISTRY_USERNAME_<your registry name>=<ACR username>
      CONTAINER_REGISTRY_PASSWORD_<your registry name>=<ACR password>
      ```

10. Kattintson a jobb gombbal a központi telepítési. template. JSON fájlra a Visual Studio Code Explorerben, és válassza a **Build IoT Edge megoldást**.

11. Figyelje meg, hogy ez a parancs egy telepítési. amd64. JSON nevű konfigurációs mappát hoz létre. Ez a fájl a megoldás konkrét telepítési sablonja.

## <a name="add-router-module"></a>Útválasztó modul hozzáadása

Ezután hozzáadjuk az útválasztó modult a megoldáshoz. Az útválasztó modul a megoldás számos feladatát kezeli:

* **Üzenetek fogadása a Leaf-eszközökről:** mivel az üzenetek a IoT Edge eszközre érkeznek az alárendelt eszközökről, az útválasztó modul megkapja az üzenetet, és megkezdi az üzenet útválasztásának előkészítését.
* **Üzenetek küldése az RUL osztályozó modulba:** ha egy alsóbb rétegbeli eszközről új üzenet érkezik, az útválasztó modul átalakítja az üzenetet arra a formátumra, amelyet a RUL osztályozó vár. Az útválasztó elküldi az üzenetet az RUL osztályozó RUL-előrejelzésnek. Miután az osztályozó elvégezte az előrejelzést, visszaküldi az üzenetet az útválasztó modulnak.
* **RUL üzenetek küldése a IoT hubnak:** ha az útválasztó üzeneteket fogad az osztályozó, akkor átalakítja az üzenetet, hogy csak a lényeges információkat, az eszköz azonosítóját és a RUL tartalmazza, és a rövidített üzenetet a IoT hubhoz küldje. A további finomítások, amelyeket itt nem készítettünk, csak akkor küldenek üzeneteket a IoT Hubnak, ha a RUL előrejelzése küszöbérték alá esik (például ha a RUL kevesebb, mint 100 ciklus). A szűrés ily módon csökkentené az üzenetek mennyiségét, és csökkenti az IoT hub költségeit.
* **Üzenet küldése a Avro-író modulnak:** az alárendelt eszköz által küldött összes adatok megőrzése érdekében az útválasztó modul elküldi az osztályozó által fogadott teljes üzenetet az Avro-író modulnak, amely megtartja és feltölti az adatokat IoT hub fájlfeltöltés használatával.

> [!NOTE]
> A modul feladatainak leírása a feldolgozás szekvenciálisnak tűnhet, de a folyamat üzenet/esemény alapján történik. Ezért van szükségünk egy összehangoló modulra, például az útválasztó modulra.

### <a name="create-module-and-copy-files"></a>Modul létrehozása és fájlok másolása

1. Kattintson a jobb gombbal a Visual Studio Code modulok mappájára, majd válassza a **IoT Edge modul hozzáadása**lehetőséget.

2. Válassza a  **C# modul**lehetőséget.

3. Nevezze el a modul **turbofanRouter**.

4. Ha a rendszer kéri a Docker-rendszerkép tárházát, használja a Machine learning-munkaterületen a beállításjegyzéket (a beállításjegyzéket a *telepítési. template. JSON* fájl registryCredentials csomópontjában találja meg). Ez az érték a beállításjegyzék teljesen minősített címe,  **\<például a Registry\>. azurecr.IO/turbofanrouter**.

    > [!NOTE]
    > Ebben a cikkben a Azure Machine Learning szolgáltatás munkaterülete által létrehozott Azure Container Registry használjuk, amelyet az osztályozó betanításához és üzembe helyezéséhez használtunk. Ez csupán kényelmi megoldás. Létrehoztunk egy új tároló-beállításjegyzéket, és ott közzétettük a modulokat.

5. Nyisson meg egy új terminál-ablakot a Visual Studio Code-ban (**terminál** **megtekintése** > ), és másolja a fájlokat a modulok könyvtárából.

    ```cmd
    copy c:\source\IoTEdgeAndMlSample\EdgeModules\modules\turbofanRouter\*.cs c:\source\IoTEdgeAndMlSample\EdgeSolution\modules\turbofanRouter\
    ```

6. Amikor a rendszer kéri, hogy írja `y` felül a program.cs `Enter`, nyomja meg a gombot, majd a találatot.

### <a name="build-router-module"></a>Útválasztó modul létrehozása

1. A Visual Studio Code-ban válassza a **terminál** > **alapértelmezett felépítési feladat beállítása**lehetőséget.

2. Kattintson a **Tasks. JSON fájl létrehozása sablonból**elemre.

3. Kattintson a **.net Core**elemre.

4. Ha a Tasks. JSON megnyitja a tartalmat, cserélje le a következőket:

    ```json
    {
      // See https://go.microsoft.com/fwlink/?LinkId=733558
      // for the documentation about the tasks.json format
      "version": "2.0.0",
      "tasks": [
        {
          "label": "build",
          "command": "dotnet",
          "type": "shell",
          "group": {
            "kind": "build",
            "isDefault": true
          },
          "args": [
            "build",
            "${workspaceFolder}/modules/turbofanRouter"
          ],
          "presentation": {
            "reveal": "always"
          },
          "problemMatcher": "$msCompile"
        }
      ]
    }
    ```

5. Mentse és zárjuk be a Tasks. JSON fájlt.

6. Futtasson Build `Ctrl + Shift + B` -t vagy **terminál** > -futtatási felépítési**feladatot**.

### <a name="set-up-module-routes"></a>Modulok útvonalának beállítása

A fentiekben leírtaknak megfelelően a IoT Edge Runtime a *Deployment. template. JSON* fájlban konfigurált útvonalakat használja a lazán kapcsolt modulok közötti kommunikáció kezeléséhez. Ebben a szakaszban bemutatjuk, hogyan állíthatja be az útvonalakat a turbofanRouter modulhoz. Először a bemeneti útvonalakat fogjuk kijelölni, majd a kimeneteket áthelyezni.

#### <a name="inputs"></a>Bemenetek

1. A Program.cs init () metódusában két visszahívást regisztrálunk a modulhoz:

   ```csharp
   await ioTHubModuleClient.SetInputMessageHandlerAsync(EndpointNames.FromLeafDevice, LeafDeviceInputMessageHandler, ioTHubModuleClient);
   await ioTHubModuleClient.SetInputMessageHandlerAsync(EndpointNames.FromClassifier, ClassifierCallbackMessageHandler, ioTHubModuleClient);
   ```

2. Az első visszahívás figyeli az **deviceInput** -fogadónak küldött üzeneteket. A fenti ábrán láthatjuk, hogy bármely levélből származó üzeneteket át szeretnénk irányítani erre a bemenetre. A *Deployment. template. JSON* fájlban adjon hozzá egy útvonalat, amely közli a peremhálózati központtal, hogy átirányítsa a IoT Edge eszköz által fogadott üzeneteket, amelyeket nem a IoT Edge modul küldött a "deviceInput" nevű bemenetre a turbofanRouter modulban:

   ```json
   "leafMessagesToRouter": "FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/deviceInput\")"
   ```

3. Ezután adjon hozzá egy útvonalat az üzenetekhez a rulClassifier modulból a turbofanRouter modulba:

   ```json
   "classifierToRouter": "FROM /messages/modules/turbofanRulClassifier/outputs/amloutput INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/rulInput\")"
   ```

#### <a name="outputs"></a>Kimenetek

Adjon hozzá négy további útvonalat a $edgeHub Route paraméterhez az útválasztó modul kimenetének kezeléséhez.

1. A Program.cs definiálja a SendMessageToClassifier () metódust, amely a modul ügyfelével üzenetet küld a RUL osztályozó számára az útvonal használatával:

   ```json
   "routerToClassifier": "FROM /messages/modules/turbofanRouter/outputs/classOutput INTO BrokeredEndpoint(\"/modules/turbofanRulClassifier/inputs/amlInput\")"
   ```

2. A SendRulMessageToIotHub () a modul-ügyféllel küldi el a IoT Hub az eszközre vonatkozó RUL-adatelérési útvonalat az útvonalon keresztül:

   ```json
   "routerToIoTHub": "FROM /messages/modules/turboFanRouter/outputs/hubOutput INTO $upstream"
   ```

3. A SendMessageToAvroWriter () a modul ügyfelével küldi el az üzenetet a avroFileWriter modulba felvett RUL-adattal.

   ```json
   "routerToAvro": "FROM /messages/modules/turbofanRouter/outputs/avroOutput INTO BrokeredEndpoint(\"/modules/avroFileWriter/inputs/avroModuleInput\")"
   ```

4. A HandleBadMessage () nem küldi el a sikertelen üzeneteket a IoT Hub, ahol később átirányíthatja őket.

   ```json
   "deadLetter": "FROM /messages/modules/turboFanRouter/outputs/deadMessages INTO $upstream"
   ```

A "$edgeHub" csomóponttal együtt vett összes útvonalnak a következő JSON-hez hasonlóan kell kinéznie:

```json
"$edgeHub": {
  "properties.desired": {
    "schemaVersion": "1.0",
    "routes": {
      "leafMessagesToRouter": "FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/deviceInput\")",
      "classifierToRouter": "FROM /messages/modules/turbofanRulClassifier/outputs/amlOutput INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/rulInput\")",
      "routerToClassifier": "FROM /messages/modules/turbofanRouter/outputs/classOutput INTO BrokeredEndpoint(\"/modules/turbofanRulClassifier/inputs/amlInput\")",
      "routerToIoTHub": "FROM /messages/modules/turboFanRouter/outputs/hubOutput INTO $upstream",
      "routerToAvro": "FROM /messages/modules/turbofanRouter/outputs/avroOutput INTO BrokeredEndpoint(\"/modules/avroFileWriter/inputs/avroModuleInput\")",
      "deadLetter": "FROM /messages/modules/turboFanRouter/outputs/deadMessages INTO $upstream"
    },
    "storeAndForwardConfiguration": {
      "timeToLiveSecs": 7200
    }
  }
}
```

> [!NOTE]
> A turbofanRouter modul hozzáadása a következő további útvonalat hozta létre `turbofanRouterToIoTHub": "FROM /messages/modules/turbofanRouter/outputs/* INTO $upstream`:. Távolítsa el ezt az útvonalat, csak a fent felsorolt útvonalakat hagyva a Deployment. template. JSON fájlban.

#### <a name="copy-routes-to-deploymentdebugtemplatejson"></a>Útvonalak másolása az üzembehelyezési. debug. template. JSON fájlba

Utolsó lépésként a fájlok szinkronizálással való megtartásához tükrözze az üzembe helyezés. sablon. JSON fájl módosításait a Deployment. debug. template. JSON fájlban.

## <a name="add-avro-writer-module"></a>Avro-író modul hozzáadása

A Avro-író modul két feladattal rendelkezik a megoldásban, az üzenetek tárolásához és a fájlok feltöltéséhez.

* **Üzenetek tárolása**: Ha a Avro-író modul üzenetet kap, a Avro formátumban írja az üzenetet a helyi fájlrendszerbe. Egy kötési csatlakoztatást használunk, amely egy könyvtárat (ebben az esetben/Data/avrofiles) egy, a modul tárolójában lévő elérési útra csatlakoztat. Ez a csatlakoztatás lehetővé teszi, hogy a modul a helyi elérési útra írjon (/avrofiles), és ezek a fájlok közvetlenül elérhetők legyenek a IoT Edge eszközről.

* **Fájlok feltöltése**: a Avro-író modul az Azure IoT hub file upload szolgáltatást használja a fájlok Azure Storage-fiókba való feltöltéséhez. A fájl sikeres feltöltése után a modul törli a fájlt a lemezről.

### <a name="create-module-and-copy-files"></a>Modul létrehozása és fájlok másolása

1. A parancssorban keresse meg a Python elemet **: Válassza a tolmács**lehetőséget.

1. Válassza ki a C:\\Python37 található tolmácsot.

1. Nyissa meg újra a parancssort, és keresse **meg a terminált: Válassza az alapértelmezett**rendszerhéj lehetőséget.

1. Ha a rendszer kéri, válassza a **parancssor**lehetőséget.

1. Nyisson meg egy új, > terminál**új**terminálját.

1. Kattintson a jobb gombbal a Visual Studio Code modulok mappájára, majd válassza a **IoT Edge modul hozzáadása**lehetőséget.

1. Válassza a **Python Module** (Python-modul) lehetőséget.

1. Nevezze el a "avroFileWriter" modult.

1. Ha a rendszer kéri a Docker-rendszerkép tárházát, használja ugyanazt a beállításjegyzéket, amelyet az útválasztó modul hozzáadásakor használt.

1. Fájlok másolása a minta modulból a megoldásba.

   ```cmd
   copy C:\source\IoTEdgeAndMlSample\EdgeModules\modules\avroFileWriter\*.py C:\source\IoTEdgeAndMlSample\EdgeSolution\modules\avroFileWriter\
   ```

1. Ha a rendszer a Main.py felülírására `y` kéri, írja `Enter`be a következőt:, majd nyomja le.

1. Figyelje meg, hogy a filemanager.py és a schema.py hozzá lettek adva a megoldáshoz, és a main.py frissült.

> [!NOTE]
> Amikor megnyit egy Python-fájlt, a rendszer kérni fogja, hogy telepítse a pylint-t. Az oktatóanyag elvégzéséhez nem kell telepítenie a kitöltést.

### <a name="bind-mount-for-data-files"></a>Kötési csatlakoztatás adatfájlokhoz

Ahogy azt az introban is említettük, az író modul a kötés csatlakoztatásával függ a Avro-fájlok írására az eszköz fájlrendszerében.

#### <a name="add-directory-to-device"></a>Könyvtár hozzáadása az eszközhöz

1. Csatlakozzon az IoT Edge eszköz virtuális géphez SSH használatával.

   ```bash
   ssh -l <user>@IoTEdge-<extension>.<region>.cloudapp.azure.com
   ```

2. Hozza létre azt a könyvtárat, amely a mentett levél üzeneteit tárolja.

   ```bash
   sudo mkdir -p /data/avrofiles
   ```

3. Frissítse a könyvtár engedélyeit, hogy az a tároló által írható legyen.

   ```bash
   sudo chmod ugo+rw /data/avrofiles
   ```

4. Ellenőrizze, hogy a címtárban már van-e írási (w) engedély a felhasználóhoz, a csoporthoz és a tulajdonoshoz.

   ```bash
   ls -la /data
   ```

   ![Avrofiles tartozó címtár-engedélyek](media/tutorial-machine-learning-edge-06-custom-modules/avrofiles-directory-permissions.png)

#### <a name="add-directory-to-the-module"></a>Könyvtár hozzáadása a modulhoz

Ha a könyvtárat hozzá szeretné adni a modul tárolóhoz, akkor a avroFileWriter modulhoz társított Dockerfiles módosítjuk. A modulhoz három Dockerfiles van társítva: Docker. amd64, Docker. amd64. debug és Docker. arm32v7. Ezeket a fájlokat szinkronban kell tartani, ha egy arm32-eszközön szeretne hibakeresést végezni vagy üzembe helyezni. Ehhez a cikkhez csak a Docker. amd64-re koncentrálhat.

1. A fejlesztői gépen nyissa meg a **Docker. amd64** fájlt.

2. Módosítsa a fájlt úgy, hogy az a következő példához hasonlóan néz ki:

   ```dockerfile
   FROM ubuntu:xenial

   WORKDIR /app

   RUN apt-get update && apt-get install -y --no-install-recommends libcurl4-openssl-dev
   python3-pip libboost-python1.58-dev libpython3-dev && rm -rf /var/lib/apt/lists/*

   RUN pip3 install --upgrade pip
   COPY requirements.txt ./
   RUN pip install -r requirements.txt

   COPY . .

   RUN useradd -ms /bin/bash moduleuser
   RUN mkdir /avrofiles && chown moduleuser /avrofiles
   USER moduleuser

   CMD [ "python3", "-u", "./main.py" ]
   ```

   A `mkdir` és`chown` parancsok arra utasítja a Docker-felépítési folyamatot, hogy hozzon létre egy/avrofiles nevű legfelső szintű könyvtárat a rendszerképben, majd a moduleuser az adott könyvtár tulajdonosaként. Fontos, hogy ezek a parancsok bekerülnek a modul felhasználójának a `useradd` paranccsal való hozzáadása után, és mielőtt a környezet átvált a moduleuser (felhasználói moduleuser).

3. Végezze el a megfelelő módosításokat a Docker. amd64. debug és a Docker. arm32v7.

#### <a name="update-the-module-configuration"></a>A modul konfigurációjának frissítése

A kötés létrehozásának utolsó lépése az üzembe helyezés. template. JSON (és a Deployment. debug. template. JSON) fájlok frissítése a kötési adatokkal.

1. Nyissa meg a Deployment. template. JSON fájlt.

2. Módosítsa a avroFileWriter moduljának definícióját úgy, `Binds` hogy hozzáadja azt a paramétert, amely a Container Directory/avrofiles a peremhálózati eszköz helyi könyvtárába mutat. A modul definíciójának meg kell egyeznie a következő példával:

   ```json
   "avroFileWriter": {
     "version": "1.0",
     "type": "docker",
     "status": "running",
     "restartPolicy": "always",
     "settings": {
       "image": "${MODULES.avroFileWriter}",
       "createOptions": {
         "HostConfig": {
           "Binds": [
             "/data/avrofiles:/avrofiles"
           ]
         }
       }
     }
   }
   ```

3. Végezze el a megfelelő módosításokat a Deployment. debug. template. JSON fájlban.

### <a name="bind-mount-for-access-to-configyaml"></a>Kötés csatlakoztatása a config. YAML eléréséhez

Az író modulhoz még egy kötést kell hozzáadni. Ez a kötés lehetővé teszi, hogy a modul hozzáférhessen a IoT Edge eszközön található/etc/iotedge/config.YAML fájl kapcsolati karakterláncának olvasásához. A iothubclientről létrehozásához szükség van a kapcsolódási karakterláncra, hogy meghívjuk a\_blob\_aszinkron feltöltési módszerét, hogy fájlokat töltsenek fel az IoT hub-ba. A kötés hozzáadásának lépései hasonlóak az előző szakaszhoz.

#### <a name="update-directory-permission"></a>Könyvtár frissítése engedély

1. Csatlakozzon az IoT Edge-eszközhöz az SSH használatával.

   ```bash
   ssh -l <user>@IoTEdge-<extension>.<region>.cloudapp.azure.com
   ```

2. Olvasási engedély hozzáadása a config. YAML fájlhoz.

   ```bash
   sudo chmod +r /etc/iotedge/config.yaml
   ```

3. Ellenőrizze, hogy az engedélyek helyesen vannak-e beállítva.

   ```bash
   ls -la /etc/iotedge/
   ```

4. Győződjön meg arról, hogy a config. YAML engedélyei a következők: **r--r**--r--.

#### <a name="add-directory-to-module"></a>Könyvtár hozzáadása a modulhoz

1. A fejlesztői gépen nyissa meg a **Docker. amd64** fájlt.

2. Adjon hozzá további készletet `mkdir` és `chown` parancsokat a fájlhoz, így a következőképpen néz ki:

   ```dockerfile
   FROM ubuntu:xenial

   WORKDIR /app

   RUN apt-get update && apt-get install -y --no-install-recommends libcurl4-openssl-dev
   python3-pip libboost-python1.58-dev libpython3-dev && rm -rf /var/lib/apt/lists/\*

   RUN pip3 install --upgrade pip
   COPY requirements.txt ./
   RUN pip install -r requirements.txt

   COPY . .

   RUN useradd -ms /bin/bash moduleuser
   RUN mkdir /avrofiles && chown moduleuser /avrofiles
   RUN mkdir -p /app/iotconfig && chown moduleuser /app/iotconfig

   USER moduleuser

   CMD "python3", "-u", "./main.py"]
   ```

3. Végezze el a megfelelő módosításokat a Docker. amd64. debug és a Docker. arm32v7.

#### <a name="update-the-module-configuration"></a>A modul konfigurációjának frissítése

1. Nyissa meg a **Deployment. template. JSON** fájlt.

2. Módosítsa a avroFileWriter modul definícióját úgy, hogy hozzáad egy második sort `Binds` a paraméterhez, amely a Container Directoryt (/App/iotconfig) az eszköz helyi könyvtárára (/etc/iotedge) mutat.

   ```json
   "avroFileWriter": {
     "version": "1.0",
     "type": "docker",
     "status": "running",
     "restartPolicy": "always",
     "settings": {
       "image": "${MODULES.avroFileWriter}",
       "createOptions": {
         "HostConfig": {
           "Binds": [
             "/data/avrofiles:/avrofiles",
             "/etc/iotedge:/app/iotconfig"
           ]
         }
       }
     }
   }
   ```

3. Végezze el a megfelelő módosításokat a Deployment. debug. template. JSON fájlban.

## <a name="install-dependencies"></a>Függőségek telepítése

Az író modul két Python-kódtár, fastavro és PyYAML függőségét veszi igénybe. A függőségeket a fejlesztői gépre kell telepíteni, és arra kell utasítani a Docker-Build folyamatát, hogy azok a modul rendszerképében legyenek telepítve.

### <a name="pyyaml"></a>PyYAML

1. A fejlesztői gépen nyissa meg a **követelmények. txt** fájlt, és adja hozzá a PyYAML.

   ```txt
   azure-iothub-device-client~=1.4.3
   pyyaml
   ```

2. Nyissa meg a **Docker. amd64** fájlt, `pip install` és adjon hozzá egy parancsot a setuptools frissítéséhez.

   ```dockerfile
   FROM ubuntu:xenial

   WORKDIR /app

   RUN apt-get update && \
       apt-get install -y --no-install-recommends libcurl4-openssl-dev python3-pip libboost-python1.58-dev libpython3-dev && \
       rm -rf /var/lib/apt/lists/\*

   RUN pip3 install --upgrade pip
   RUN pip install -U pip setuptools
   COPY requirements.txt ./
   RUN pip install -r requirements.txt

   COPY . .

   RUN useradd -ms /bin/bash moduleuser
   RUN mkdir /avrofiles && chown moduleuser /avrofiles
   RUN mkdir -p /app/iotconfig && chown moduleuser /app/iotconfig
   USER moduleuser

   CMD [ "python3", "-u", "./main.py" ]
   ```

3. Végezze el a megfelelő módosításokat a Docker. amd64. debug parancsban. <!--may not be necessary. Add 'if needed'?-->

4. A PyYAML helyileg történő telepítése a terminál megnyitásával a Visual Studio Code-ban és beírásával

   ```cmd
   pip install pyyaml
   ```

### <a name="fastavro"></a>Fastavro

1. A követelmények. txt fájlban adja hozzá a fastavro a PyYAML után.

   ```txt
   azure-iothub-device-client~=1.4.3
   pyyaml
   fastavro
   ```

2. Telepítse a fastavro a fejlesztői gépre a Visual Studio Code Terminal használatával.

   ```cmd
   pip install fastavro
   ```

## <a name="reconfigure-iot-hub"></a>IoT Hub újrakonfigurálása

A IoT Edge eszköz és modulok a rendszerbe való bevezetésével megváltoztak az Elvárásaink arról, hogy milyen információkat küld a központ és milyen célra. Az új realitások kezeléséhez újra kell konfigurálnia az útválasztást a központban.

> [!NOTE]
> A modulok üzembe helyezése előtt konfigurálja újra az elosztót, mert a hub egyes beállításai, különösen a fájlok feltöltése megfelelően be kell állítani a avroFileWriter modul megfelelő futtatását.

### <a name="set-up-route-for-rul-messages-in-iot-hub"></a>RUL-üzenetek útvonalának beállítása IoT Hub

A router és az osztályozó a helyén arra számítunk, hogy a rendszer csak az eszköz AZONOSÍTÓját és az eszköz RUL előrejelzését tartalmazó normál üzeneteket fogad. Azt szeretnénk, hogy a RUL a saját tárolóhelyére irányítsák, ahol az eszközök állapotát figyelemmel kísérheti, jelentéseket és riasztásokat hozhat létre, szükség szerint. Ugyanakkor azt szeretnénk, hogy az eszközön lévő összes olyan eszközt, amelyet még nem csatoltak a IoT Edge eszközhöz, továbbra is közvetlenül egy levélben küldi el a rendszer, hogy továbbra is az aktuális tárolási helyre irányítsa.

#### <a name="create-a-rul-message-route"></a>RUL-üzenet útvonalának létrehozása

1. A Azure Portal navigáljon a IoT Hub.

2. A bal oldali navigációs sávon válassza az **üzenet-útválasztás**lehetőséget.

3. Válassza a **Hozzáadás** lehetőséget.

4. Nevezze el az útvonal **RulMessageRoute**.

5. Válassza a **Hozzáadás** lehetőséget a **végpont** kiválasztása elem mellett, majd válassza a **blob Storage**lehetőséget.

6. A **tárolási végpont hozzáadása** űrlapon nevezze el a végpont **ruldata**.

7. Válassza **a tároló**kiválasztása lehetőséget.

8. Válassza ki az oktatóanyag során használt Storage-fiókot, amelynek neve **\<iotedgeandml egyedi utótag\>** .

9. Válassza ki a **ruldata** tárolót, és kattintson a **kiválasztás**elemre.

10. A tárolási végpont létrehozásához kattintson a **Létrehozás** gombra.

11. Az **útválasztási lekérdezéshez**adja meg a következő lekérdezést:

    ```sql
    IS_DEFINED($body.PredictedRul) AND NOT IS_DEFINED($body.OperationalSetting1)
    ```

12. Bontsa ki a **teszt** szakaszt, majd az **üzenet törzse** szakaszt. Cserélje le az üzenetet a várt üzenetek példáján:

    ```json
    {
      "ConnectionDeviceId": "aaLeafDevice_1",
      "CorrelationId": "b27e97bb-06c5-4553-a064-e9ad59c0fdd3",
      "PredictedRul": 132.62721409309165,
      "CycleTime": 64.0
    }
    ```

13. Válassza a **teszt útvonal**lehetőséget. Ha a teszt sikeres, "az üzenet megfelelt a lekérdezésnek" jelenik meg.

14. Kattintson a **Save** (Mentés) gombra.

#### <a name="update-turbofandevicetostorage-route"></a>TurbofanDeviceToStorage-útvonal frissítése

Nem szeretnénk átirányítani az új előrejelzési adatgyűjtést a régi tárolási helyükre, ezért az útvonal frissítésével megakadályozhatja.

1. Az IoT Hub **üzenet-útválasztás** lapon válassza az **útvonalak** fület.

2. Válassza ki a **turbofanDeviceDataToStorage**, vagy bármilyen nevet, amelyet a kezdeti eszköz adatútvonala számára adott meg.

3. Az útválasztási lekérdezés frissítése a következőre

   ```sql
   IS_DEFINED($body.OperationalSetting1)
   ```

4. Bontsa ki a **teszt** szakaszt, majd az **üzenet törzse** szakaszt. Cserélje le az üzenetet a várt üzenetek példáján:

   ```json
   {
     "Sensor13": 2387.96,
     "OperationalSetting1": -0.0008,
     "Sensor6": 21.61,
     "Sensor11": 47.2,
     "Sensor9": 9061.45,
     "Sensor4": 1397.86,
     "Sensor14": 8140.39,
     "Sensor18": 2388.0,
     "Sensor12": 522.87,
     "Sensor2": 642.42,
     "Sensor17": 391.0,
     "OperationalSetting3": 100.0,
     "Sensor1": 518.67,
     "OperationalSetting2": 0.0002,
     "Sensor20": 39.03,
     "DeviceId": 19.0,
     "Sensor5": 14.62,
     "PredictedRul": 212.00132402791962,
     "Sensor8": 2388.01,
     "Sensor16": 0.03,
     "CycleTime": 42.0,
     "Sensor21": 23.3188,
     "Sensor15": 8.3773,
     "Sensor3": 1580.09,
     "Sensor10": 1.3,
     "Sensor7": 554.57,
     "Sensor19": 100.0
   }
   ```

5. Válassza a **teszt útvonal**lehetőséget. Ha a teszt sikeres, "az üzenet megfelelt a lekérdezésnek" jelenik meg.

6. Kattintson a **Mentés** gombra.

### <a name="configure-file-upload"></a>Fájlfeltöltés konfigurálása

Konfigurálja a IoT Hub fájlfeltöltés funkciót, hogy a fájl-író modul feltöltse a fájlokat a tárolóba.

1. A IoT Hub bal oldali navigátorában válassza a **fájlfeltöltés**lehetőséget.

2. Válassza az **Azure Storage-tároló**elemet.

3. Válassza ki a Storage-fiókját a listából.

4. Válassza ki a **uploadturbofanfiles** tárolót, és kattintson a **kiválasztás**elemre.

5. Kattintson a **Mentés** gombra. A portál értesíti a Mentés befejeződéséről.

> [!Note]
> Nincs bekapcsolva a feltöltési értesítés ebben az oktatóanyagban, de a fájlfeltöltés-értesítések kezelésével kapcsolatos részletekért tekintse meg a [fájl feltöltésére vonatkozó értesítés fogadása](../iot-hub/iot-hub-java-java-file-upload.md#receive-a-file-upload-notification) című témakört.

## <a name="build-publish-and-deploy-modules"></a>Modulok készítése, közzététele és üzembe helyezése

Most, hogy elvégezte a konfigurációs módosításokat, készen állunk a rendszerképek létrehozására és az Azure Container registrybe való közzétételre. A létrehozási folyamat a Deployment. template. JSON fájlt használja annak meghatározására, hogy mely modulokat kell felépíteni. Az egyes modulok beállításai, beleértve a verziót, a Module. JSON fájlban találhatók. A létrehozási folyamat először egy Docker-buildet futtat a Module. JSON fájlban található aktuális konfigurációnak megfelelő Dockerfiles. Ezt követően a rendszer közzéteszi a rendszerképet a beállításjegyzékben a Module. JSON fájlból, amely a Module. JSON fájlban szereplővel egyező verziójú címkével rendelkezik. Végezetül létrehoz egy konfiguráció-specifikus központi telepítési jegyzéket (például: Deploy. amd64. JSON), amelyet a IoT Edge eszközre fogunk telepíteni. A IoT Edge eszköz beolvassa az adatokat a telepítési jegyzékből, és az utasítások alapján letölti a modulokat, konfigurálja az útvonalakat, és beállítja a kívánt tulajdonságokat. Ennek a telepítési módszernek két mellékhatása van:

* **Központi telepítési késés:** mivel a IoT Edge futtatókörnyezetnek fel kell ismernie a kívánt tulajdonságok módosítását az újrakonfigurálás megkezdése előtt, igénybe vehet némi időt a modulok üzembe helyezése után, amíg a futtatókörnyezet fel nem veszi őket, és nem kezdi frissíteni a IoT Edge eszköz.

* **Modulok verziói:** ha egy modul tárolójának új verzióját közzéteszi a tároló-beállításjegyzékben az előző modullal megegyező verziójú címkék használatával, a futtatókörnyezet nem fogja letölteni a modul új verzióját. Összehasonlítja a helyi rendszerkép verziószámát és a kívánt rendszerképet az üzembe helyezési jegyzékből. Ha ezek a verziók egyeznek, a futtatókörnyezet nem hajt végre műveletet. Ezért fontos, hogy minden alkalommal növelje a modul verzióját, amikor új módosításokat kíván üzembe helyezni. Növelje meg a verziószámot úgy, hogy a Module . JSON fájlban lévő **Version** tulajdonságot a módosítani kívánt modulhoz módosítja. Ezután hozza létre és tegye közzé a modult.

    ```json
    {
      "$schema-version": "0.0.1",
      "description": "",
      "image": {
        "repository": "<your registry>.azurecr.io/avrofilewriter",
        "tag": {
          "version": "0.0.1",
          "platforms": {
            "amd64": "./Dockerfile.amd64",
            "amd64.debug": "./Dockerfile.amd64.debug",
            "arm32v7": "./Dockerfile.arm32v7"
          }
        },
        "buildOptions": []
      },
      "language": "python"
    }
    ```

### <a name="build-and-publish"></a>Létrehozás és közzététel

1. A fejlesztői virtuális gépen a Visual Studio Code-ban nyisson meg egy Visual Studio Code Terminal ablakot, és jelentkezzen be a tároló-beállításjegyzékbe.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

1. A Visual Studio Code-ban kattintson a jobb gombbal a Deployment. template. JSON fájlra, és válassza a **IoT Edge megoldás létrehozása és leküldése**lehetőséget.

### <a name="view-modules-in-the-registry"></a>A beállításjegyzék moduljainak megtekintése

A létrehozás sikeres befejezését követően a Azure Portal segítségével áttekintheti a közzétett modulokat.

1. A Azure Portal navigáljon a Azure Machine Learning szolgáltatás munkaterületére, és kattintson a **beállításjegyzékre**mutató hivatkozásra.

    ![A Machine learning szolgáltatás munkaterületén navigáljon a beállításjegyzékbe](media/tutorial-machine-learning-edge-06-custom-modules/follow-registry-link.png)

2. A beállításjegyzék oldali Navigátorban válassza a **tárolók**lehetőséget.

3. Vegye figyelembe, hogy mindkét létrehozott, **avrofilewriter** és **turbofanrouter**-modul adattárakként jelenik meg.

4. Válassza a **turbofanrouter** lehetőséget, és vegye figyelembe, hogy egy 0.0.1-amd64 címkével rendelkező képet adott közzé.

   ![A turbofanrouter első címkézett verziójának megtekintése](media/tutorial-machine-learning-edge-06-custom-modules/tagged-image-turbofanrouter-repo.png)

### <a name="deploy-modules-to-iot-edge-device"></a>Modulok üzembe helyezése IoT Edge eszközön

Beépítettük és konfiguráltuk a megoldásokat a megoldásban, most a modulokat a IoT Edge eszközre fogjuk telepíteni.

1. A Visual Studio Code-ban kattintson a jobb gombbal a **telepítési. amd64. JSON** fájlra a konfigurációs mappában.

2. Válassza **a központi telepítés létrehozása egyetlen eszközhöz**lehetőséget.

3. Válassza ki a IoT Edge eszközt, **aaTurboFanEdgeDevice**.

4. Frissítse az Azure IoT Hub-eszközök panelt a Visual Studio Code Explorerben. Látnia kell, hogy a három új modul telepítve van, de még nem fut.

5. Néhány perc elteltével újra frissül, és a futó modulok megjelennek.

   ![Futó modulok megtekintése a Visual Studio Code-ban](media/tutorial-machine-learning-edge-06-custom-modules/view-running-modules-list.png)

> [!NOTE]
> Több percet is igénybe vehet, amíg a modulok elindulnak, és egy állandó futású állapotba rendezhetők. Ebben az időszakban a modulok indítását és leállását láthatja, amikor megpróbálnak kapcsolatot létesíteni az IoT Edge hub modullal.

## <a name="diagnosing-failures"></a>Hibák diagnosztizálása

Ebben a szakaszban néhány olyan technikát is megosztunk, amely megértette, hogy mi a hiba egy modul vagy modul esetében. A Visual Studio Code-ban lévő állapotból gyakran nem lehet hibát kimutatni.

### <a name="identify-failed-modules"></a>Sikertelen modulok azonosítása

* **Visual Studio code:** Tekintse meg az Azure IoT Hub-eszközök panelt. Ha a legtöbb modul fut állapotban van, de az egyik le van állítva, meg kell vizsgálnia, hogy a leállított modul továbbra is fennáll. Ha az összes modul hosszabb ideig leállított állapotban van, akkor a meghibásodást is jelezheti.

* **Azure Portal:** Ha megkeresi az IoT hub-t a portálon, majd megkeresi az eszköz részletei lapot (IoT Edge alatt az eszköz részletezése), akkor előfordulhat, hogy egy modul hibát jelzett, vagy soha nem jelentett semmit az IoT hubhoz.

### <a name="diagnosing-from-the-device"></a>Diagnosztizálás az eszközről

Ha bejelentkezik a IoT Edge eszközbe, a modulok állapotával kapcsolatban jó információkhoz juthat. Az általunk használt fő mechanizmus a Docker-parancs, amely lehetővé teszi, hogy megvizsgáljuk az eszközön található tárolókat és képeket.

1. Az összes futó tároló listázása. Azt várjuk, hogy minden modulhoz egy tárolót lássunk, amelynek a neve megfelel a modulnak. Emellett a parancs felsorolja a tároló pontos képét is, beleértve a verziót is, így Ön is megtekintheti a vártat. A rendszerképeket úgy is listázhatja, hogy a parancsban az "image" tulajdonságot a "Container" értékre cseréli.

   ```bash
   sudo docker container ls
   ```

2. A tároló naplóinak beolvasása. Ez a parancs kimenete a tárolóban található StdErr és StdOut-ra is vonatkozik. Ez a parancs olyan tárolók esetében működik, amelyek valamilyen okból elkezdődtek, majd elhaltak. Azt is hasznos megérteni, hogy mi történt a edgeAgent vagy a edgeHub-tárolóval.

   ```bash
   sudo docker container logs <container name>
   ```

3. Egy tároló vizsgálata. Ez a parancs rengeteg információt nyújt a rendszerképről. Az Adatszűrés a keresett elemtől függően szűrhető. Ha például meg szeretné tekinteni, hogy a avroFileWriter kötései helyesek-e, használja a következő parancsot:

   ```bash
   sudo docker container inspect -f "{{ json .Mounts }}" avroFileWriter | python -m json.tool
   ```

4. Kapcsolódás egy futó tárolóhoz. Ez a parancs akkor lehet hasznos, ha a tárolót a futtatása közben szeretné megvizsgálni:

   ```bash
   sudo docker exec -it avroFileWriter bash
   ```

## <a name="next-steps"></a>További lépések

Ebben a cikkben egy IoT Edge megoldást hoztunk létre a Visual Studio Code-ban három modullal, egy osztályozó, egy útválasztóval és egy file Writer/Uploader fájllal. Az útvonalakat úgy állítottuk be, hogy a modulok kommunikáljanak egymással a peremhálózati eszközön, módosította a peremhálózati eszköz konfigurációját, és frissítette a Dockerfiles, hogy telepítse a függőségeket, és hozzáadja a kötési csatlakoztatásokat a modulok tárolóhoz. A következő lépésben frissítettük a IoT Hub konfigurációját, hogy az üzeneteket a típus és a fájlfeltöltés kezelésére használjuk. Minden esetben a modulokat a IoT Edge eszközre telepítettük, és gondoskodtak arról, hogy a modulok megfelelően futnak.

További információt a következő lapokon talál:

* [Ismerje meg, hogyan telepíthet modulokat és hogyan hozhat létre útvonalakat IoT Edge](module-composition.md)
* [IoT Hub üzenet-útválasztási lekérdezés szintaxisa](../iot-hub/iot-hub-devguide-routing-query-syntax.md)
* [IoT Hub üzenet-útválasztás: most az üzenet törzsének útválasztása](https://azure.microsoft.com/blog/iot-hub-message-routing-now-with-routing-on-message-body/)
* [Fájlok feltöltése az IoT Hubbal](../iot-hub/iot-hub-devguide-file-upload.md)
* [Fájlok feltöltése az eszközről a felhőbe IoT Hub](../iot-hub/iot-hub-python-python-file-upload.md)

Folytassa a következő cikkel az adatok küldésének megkezdéséhez és a megoldás működés közbeni megtekintéséhez.

> [!div class="nextstepaction"]
> [Az adatküldés transzparens átjárón keresztül](tutorial-machine-learning-edge-07-send-data-to-hub.md)
