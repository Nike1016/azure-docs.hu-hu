---
title: Egy eszköz alkalmazás Node.js-ben az IoT Hub eszköz adatfolyamok (előzetes verzió) keresztül kommunikáljon |} A Microsoft Docs
description: Ebben a rövid útmutatóban fog futtatni a Node.js Szolgáltatásoldali alkalmazásokat, melyekkel egy IoT-eszközzel rendelkező eszköz adatfolyam-n keresztül.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 74a9e5e6be326bf9cef3dc6d26594b80491deb3e
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446040"
---
# <a name="quickstart-communicate-to-a-device-application-in-nodejs-via-iot-hub-device-streams-preview"></a>Gyors útmutató: Egy eszköz alkalmazás Node.js-ben az IoT Hub eszköz adatfolyamok (előzetes verzió) használatával való kommunikáció során

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

A Microsoft Azure IoT Hub jelenleg támogatja az eszköz adatfolyamok, mint egy [előzetes verziójú funkció](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Az IoT Hub eszköz Streamek](./iot-hub-device-streams-overview.md) szolgáltatás és eszköz alkalmazások biztonságos és tűzfalbarát módon kommunikálnak. A nyilvános előzetes verzióban Node.js SDK csak támogatja eszköz Streamek Szolgáltatásoldali. Ez a rövid útmutató ennek eredményeképpen csak a Szolgáltatásoldali alkalmazás futtatásához útmutatást terjed ki. Kísérő eszközoldali alkalmazás futtassa az alábbi rövid útmutatókat közül:

* [Eszközalkalmazások a C IoT Hub eszköz Streamek keresztül való kommunikáció során](./quickstart-device-streams-echo-c.md)

* [Az eszköz Apps kommunikációhoz C# keresztül az IoT Hub eszköz Streamek](./quickstart-device-streams-echo-csharp.md).

Ebben a rövid útmutatóban a Szolgáltatásoldali Node.js-alkalmazás a következő funkciókkal rendelkezik:

* Létrehoz egy eszköz stream IoT-eszközökön.

* Beolvassa a bemenetet a parancssorból, és elküldi az eszköz alkalmazásról, így vissza echo azt.

Bemutatjuk, hogy a kód egy eszköz stream, valamint hogyan használható a adatokat küldeni és fogadni a kezdeményezés folyamatán.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Előzetes verziójának eszköz Streamek jelenleg csak a az IoT-központok létrehozni a következő régiókban támogatott:

*  **USA középső RÉGIÓJA**

*  **USA középső RÉGIÓJA – EUAP**

Ebben a rövid útmutatóban a Szolgáltatásoldali alkalmazás futtatásához szüksége Node.js v10.x.x vagy annál újabb verzió a fejlesztői gépére.

Node.js letöltheti a több platformon [Nodejs.org](https://nodejs.org).

A Node.js aktuális verzióját a következő paranccsal ellenőrizheti a fejlesztői gépen:

```
node --version
```

Futtassa a következő parancsot a Microsoft Azure IoT-bővítmény hozzáadása a Cloud Shell-példány Azure CLI-hez. Az IOT-bővítmény hozzáadása Azure CLI-vel az IoT Hub, IoT Edge és IoT Device Provisioning Service (DPS) parancsok.

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Ha még nem tette meg, töltse le a Node.js-mintaprojektet a https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip címről, és csomagolja ki a ZIP-archívumot.

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

Ha elvégezte az előző [a rövid útmutató: Telemetria küldése egy eszközről IoT hubra](quickstart-send-telemetry-node.md), kihagyhatja ezt a lépést.

[!INCLUDE [iot-hub-include-create-hub-device-streams](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Eszköz regisztrálása

Ha elvégezte az előző [a rövid útmutató: Telemetria küldése egy eszközről IoT hubra](quickstart-send-telemetry-node.md), kihagyhatja ezt a lépést.

Az eszköznek regisztrálva kell lennie az IoT Hubbal, hogy csatlakozhasson hozzá. Ebben a rövid útmutatóban az Azure Cloud Shell használatával regisztrál egy szimulált eszközt.

1. Futtassa a következő parancsot az Azure Cloud Shellben, hozza létre az eszközidentitást.

   **YourIoTHubName**: Alább a helyőrzőt cserélje le az IoT hub számára is választott nevét.

   **Sajáteszköz**: Ez az eszköz a megadott név. Használjon Sajáteszköz látható módon. Ha úgy dönt, hogy eszközének egy másik nevet választ, akkor az egész cikkben azt a nevet kell használnia, és a mintaalkalmazások futtatása előtt frissítenie kell bennük az eszköznevet.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

2. Szüksége van egy *szolgáltatáskapcsolati sztringre* is azért, hogy a háttéralkalmazás csatlakozhasson az IoT Hubhoz, és üzeneteket kérhessen le. Az alábbi parancs lekéri az IoT Hub szolgáltatáskapcsolati sztringjét:

    **YourIoTHubName**: Alább a helyőrzőt cserélje le az IoT hub számára is választott nevét.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name YourIoTHubName
    ```

    Jegyezze fel, a visszaadott érték, amely a következőhöz hasonló:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-device-and-service-via-device-streams"></a>Eszköz és szolgáltatás keresztül eszköz adatfolyamok közötti kommunikáció

Ebben a szakaszban az eszközoldali alkalmazás, mind a Szolgáltatásoldali alkalmazás futtatásához, és a kettő közötti kommunikációhoz.

### <a name="run-the-device-side-application"></a>Az eszközoldali alkalmazás futtatása

Ahogy korábban említettük, IoT Hub Node.js SDK-t csak támogatja az eszköz Streamek Szolgáltatásoldali. Eszközoldali alkalmazáshoz használja a hozzájuk tartozó eszköz program ezeket a rövid útmutatók egyikében:

   * [Eszközalkalmazások a C IoT Hub eszköz Streamek keresztül való kommunikáció során](./quickstart-device-streams-echo-c.md)

   * [Az eszköz Apps kommunikációhoz C# keresztül az IoT Hub eszköz Streamek](./quickstart-device-streams-echo-csharp.md)

Győződjön meg róla, az eszközoldali alkalmazás fut, a folytatás előtt a következő lépéssel.

### <a name="run-the-service-side-application"></a>A Szolgáltatásoldali alkalmazás futtatása

Feltéve, hogy az eszköz ügyféloldali alkalmazás fut, a node.js-ben a Szolgáltatásoldali alkalmazás futtatásához az alábbi lépésekkel:

* Környezeti változókként adja meg a szolgáltatás hitelesítő adatai és Eszközazonosító.
 
   ```
   # In Linux
   export IOTHUB_CONNECTION_STRING="<provide_your_service_connection_string>"
   export STREAMING_TARGET_DEVICE="MyDevice"

   # In Windows
   SET IOTHUB_CONNECTION_STRING=<provide_your_service_connection_string>
   SET STREAMING_TARGET_DEVICE=MyDevice
   ```
  
   Változás `MyDevice` , az eszköz azonosítója, az eszköz számára is választott.

* Navigáljon a `Quickstarts/device-streams-service` a kicsomagolt a mappa projektre, és futtassa a mintát a csomópontot.

   ```
   cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service
    
   # Install the preview service SDK, and other dependencies
   npm install azure-iothub@streams-preview
   npm install

   node echo.js
   ```

Az utolsó lépés végén a Szolgáltatásoldali program egy streamet, az eszközre, és lesz létrejöttét követően megkezdődik a szolgáltatás egy karakterláncpuffert küldenie a streamet. Ebben a példában a Szolgáltatásoldali program egyszerűen beolvassa a `stdin` meg a terminált, és elküldi az eszköz, amely lesz majd echo azt vissza. Ez azt mutatja be a sikeres kétirányú kommunikációt a két alkalmazás között.

![Szolgáltatásoldali konzolkimenet](./media/quickstart-device-streams-echo-nodejs/service-console-output.png)

Billentyű lenyomásával majd megszüntetheti a programot meg újra.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid, rendelkezik állítsa be az IoT hub, regisztrált egy eszközt, létrehozott egy eszköz stream eszköz és szolgáltatás oldalán az alkalmazások között és a stream használt oda-vissza az alkalmazások közötti adatküldéshez.

További információ az eszköz adatfolyamok az alábbi hivatkozásokat:

> [!div class="nextstepaction"]
> [Eszköz Streamek áttekintése](./iot-hub-device-streams-overview.md) 