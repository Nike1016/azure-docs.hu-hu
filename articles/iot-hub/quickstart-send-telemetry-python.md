---
title: Rövid útmutató – Telemetria küldése az Azure IoT Hubra (Python) | Microsoft Docs
description: Ebben a rövid útmutatóban egy Python-mintaalkalmazást fog futtatni szimulált telemetria egy IoT Hubra való küldéséhez, és egy segédprogrammal telemetriát olvas az IoT Hubról.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: quickstart
ms.custom: mvc
ms.date: 02/28/2019
ms.openlocfilehash: c92b019e15c6a9ee5b2d38e240ae4f9891621f72
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360195"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-python"></a>Gyors útmutató: Telemetria küldése egy eszközről egy IoT-hubhoz, és olvasása háttérbeli alkalmazással (Python)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

Az IoT Hub olyan Azure-szolgáltatás, amely lehetővé teszi nagy mennyiségű telemetria betöltését egy IoT-eszközről a felhőbe tárolás vagy feldolgozás céljából. Ebben a rövid útmutatóban telemetriát küld egy szimulálteszköz-alkalmazástól az IoT Hubon keresztül egy háttéralkalmazásba feldolgozásra.

A gyors útmutató egy előre megírt Python-alkalmazást használ a telemetria küldésére, és egy parancssori felületi segédprogrammal olvassa be a telemetriát a hubról. Mielőtt futtatja ezt a két alkalmazást, hozzon létre egy IoT Hubot, és regisztráljon egy eszközt a hubon.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az ebben a rövid útmutatóban futtatott alkalmazás a Python használatával íródik. Jelenleg a Pythonhoz készült Microsoft Azure IoT SDK-k csak a Python adott verzióit támogatják az egyes platformokhoz. További információ: [PYTHON SDK – readme](https://github.com/Azure/azure-iot-sdk-python#important-installation-notes---dealing-with-importerror-issues).

Ez a rövid útmutató azt feltételezi, hogy Windowsos fejlesztői gépet használ. Windows rendszereken csak a [Python 3.6. x verzió](https://www.python.org/downloads/release/python-368/) támogatott. Válassza az Ön által használt rendszer architektúrájának megfelelő Python-telepítőt. Ha a rendszer CPU-architektúrája 32 bites, töltse le az x86-os telepítőt; az 64 bites architektúrához töltse le az x86-64 telepítőt. Továbbá győződjön meg arról, hogy a [Visual Studio 2019 C++ ](https://support.microsoft.com/en-us/help/2977003/the-latest-supported-visual-c-downloads) -hoz készült Microsoft vizualizációs terjeszthető csomag telepítve van az architektúrához (x86 vagy x64).

A Python más platformokra is letölthető a [Python.org](https://www.python.org/downloads/)webhelyről.

A Python aktuális verzióját a következő parancsok egyikével ellenőrizheti a fejlesztői gépen:

```python
python - -version
```

```python
python3 - -version
```

A következő parancs futtatásával adja hozzá az Azure CLI-hez készült Microsoft Azure IoT-bővítményt a Cloud Shell-példányhoz. Az IOT bővítmény a IoT Hub, IoT Edge és IoT Device kiépítési szolgáltatás (DPS) adott parancsait hozzáadja az Azure CLI-hez.

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Töltse le a Python-mintaprojektet a https://github.com/Azure-Samples/azure-iot-samples-python/archive/master.zip címről, és bontsa ki a ZIP-archívumot.

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Eszköz regisztrálása

Az eszköznek regisztrálva kell lennie az IoT Hubbal, hogy csatlakozhasson hozzá. Ebben a rövid útmutatóban az Azure Cloud Shell használatával regisztrál egy szimulált eszközt.

1. Futtassa az alábbi parancsot a Azure Cloud Shell az eszköz identitásának létrehozásához.

    **YourIoTHubName**: Az alábbi helyőrzőt cserélje le az IoT hub számára kiválasztott névre.

    **MyPythonDevice**: Ez a regisztrált eszköz nevét adja meg. A MyPythonDevice nevet használja a bemutatott módon. Ha úgy dönt, hogy eszközének egy másik nevet választ, akkor az egész cikkben azt a nevet kell használnia, és a mintaalkalmazások futtatása előtt frissítenie kell bennük az eszköznevet.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyPythonDevice
    ```

1. Futtassa az alábbi parancsokat a Azure Cloud Shellban a regisztrált eszközhöz tartozó _eszköz-kapcsolódási karakterlánc_ beszerzéséhez:

    **YourIoTHubName**: Az alábbi helyőrzőt cserélje le az IoT hub számára kiválasztott névre.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyPythonDevice --output table
    ```

    Jegyezze fel az eszköz kapcsolati sztringjét, amely a következőképpen néz ki:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Ezt az értéket használni fogja a rövid útmutató későbbi részében.

## <a name="send-simulated-telemetry"></a>Szimulált telemetria küldése

A szimulálteszköz-alkalmazás egy az IoT Hubon található eszközspecifikus végponthoz csatlakozik, és hőmérséklettel és páratartalommal kapcsolatos szimulált telemetriát küld.

1. Egy helyi terminálablakban keresse meg a Python-mintaprojekt gyökérmappáját. Ezután lépjen az **iot-hub\Quickstarts\simulated-device** mappába.

1. Nyissa meg a **SimulatedDevice.py** fájlt egy tetszőleges szövegszerkesztőben.

    Cserélje le a `CONNECTION_STRING` változó értékét az eszköz korábban lejegyzett kapcsolati sztringjére. Ezután mentse a **SimulatedDevice.py** fájl módosításait.

1. Futtassa az alábbi parancsokat a helyi terminálablakban a szimulálteszköz-alkalmazáshoz szükséges kódtárak telepítéséhez:

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

1. Futtassa az alábbi parancsokat a helyi terminálablakban a szimulálteszköz-alkalmazás futtatásához:

    ```cmd/sh
    python SimulatedDevice.py
    ```

    A következő képernyőképen az a kimenet látható, amikor a szimulálteszköz-alkalmazás telemetriát küld az IoT Hubnak:

    ![A szimulált eszköz futtatása](media/quickstart-send-telemetry-python/SimulatedDevice.png)
    
### <a name="to-avoid-the-import-iothubclient-error"></a>Az importálási iothub_client hibájának elkerülése
Az Azure IoT SDK for Python jelenlegi verziója a [C SDK](https://github.com/azure/azure-iot-sdk-c)-ra épülő burkoló. A szolgáltatás a [Boost](https://www.boost.org/) Library használatával jön létre. Emiatt számos jelentős korlátozást is tartalmaz. További részletek [itt](https://github.com/Azure/azure-iot-sdk-python#important-installation-notes---dealing-with-importerror-issues) találhatók

1. Győződjön meg arról, hogy a [Python](https://github.com/Azure/azure-iot-sdk-python#important-installation-notes---dealing-with-importerror-issues)megfelelő verziója van. Vegye figyelembe, hogy csak bizonyos verziók jól működnek ehhez a mintához. 
2. Győződjön meg arról, hogy rendelkezik a C++ [Microsoft Visual C++ Studio 2019](https://support.microsoft.com/en-us/help/2977003/the-latest-supported-visual-c-downloads)-es verziójának megfelelő verziójával. (A legújabbat javasoljuk).
3. Ellenőrizze, hogy telepítette-e a iothub `pip install azure-iothub-device-client`-ügyfelet:.

## <a name="read-the-telemetry-from-your-hub"></a>Telemetria olvasása a Hubról

Az IoT Hub CLI-bővítmény csatlakozhat a szolgáltatásoldali **Események** végponthoz az IoT Hubon. A bővítmény fogadja az eszközről a felhőbe irányuló üzeneteket, amelyeket a rendszer a szimulált eszközről küld. Az IoT Hub-háttéralkalmazások általában a felhőben futnak, hogy fogadják és feldolgozzák az eszközről a felhőbe küldött üzeneteket.

Futtassa a következő parancsokat az Azure Cloud Shellben úgy, hogy a `YourIoTHubName` helyére az IoT Hub neve kerüljön:

```azurecli-interactive
az iot hub monitor-events --hub-name YourIoTHubName --device-id MyPythonDevice 
```

A következő képernyőképen az a kimenet látható, amikor a bővítmény fogadja a szimulált eszköz által az IoT Hubnak küldött telemetriát:

![A háttéralkalmazás futtatása](media/quickstart-send-telemetry-python/ReadDeviceToCloud.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban beállított egy IoT Hubot, regisztrált egy eszközt, szimulált telemetriát küldött a Hubra egy Python-alkalmazással, és beolvasta a telemetriát a Hubról egy egyszerű háttéralkalmazással.

Ha meg szeretné tudni, hogyan vezérelheti a szimulált eszközt egy háttéralkalmazáson keresztül, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Rövid útmutató: IoT hubhoz csatlakoztatott eszköz vezérlése](quickstart-control-device-python.md)
