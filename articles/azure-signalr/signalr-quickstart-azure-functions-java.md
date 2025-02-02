---
title: Az Azure SignalR Service kiszolgáló nélküli rövid útmutató – Java
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre csevegőszobát az Azure SignalR szolgáltatás és az Azure Functions használatával.
author: sffamily
ms.service: signalr
ms.devlang: java
ms.topic: quickstart
ms.date: 03/04/2019
ms.author: zhshang
ms.openlocfilehash: 9e4e64b99a69e523547bae04146c7460d08bc1df
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60775863"
---
# <a name="quickstart-create-a-chat-room-with-azure-functions-and-signalr-service-using-java"></a>Gyors útmutató: Csevegőszoba létrehozása az Azure Functions és a SignalR Service Java használatával

Az Azure SignalR szolgáltatás használatával egyszerűen adhat hozzá valós idejű funkciókat az alkalmazásához. Az Azure Functions egy kiszolgáló nélküli platform, amellyel infrastruktúra kezelése nélkül futtathat kódokat. Ennek a rövid útmutatónak a segítségével megtanulhatja, hogyan készíthet kiszolgáló nélküli, valós idejű csevegőalkalmazást a SignalR szolgáltatás és a Functions használatával.

## <a name="prerequisites"></a>Előfeltételek

Ez a rövid útmutató macOS, Windows vagy Linux rendszeren is futtatható.

Ellenőrizze, hogy van-e telepítve valamilyen kódszerkesztő, például a [Visual Studio Code](https://code.visualstudio.com/).

Telepítse az [Azure Functions Core Tools (v2)](https://github.com/Azure/azure-functions-core-tools#installing) eszközkészletet az Azure-függvényalkalmazások helyi futtatásához.

> [!NOTE]
> A SignalR Service kötéseket Java-környezetben, győződjön meg arról, hogy 2.4.419 verzióját használja, vagy újabb verzióját az Azure Functions Core Tools (gazdagép verziója 2.0.12332) használatára.

A bővítmények telepítéséhez az Azure Functions Core Tools jelenleg igényli, hogy a [.NET Core SDK](https://www.microsoft.com/net/download) telepítve legyen. A JavaScript-alapú Azure-függvényalkalmazások létrehozásához azonban nem szükséges a .NET ismerete.

Ha függvényalkalmazást szeretne létrehozni a Java használatával, akkor a számítógépre a következőket kell telepíteni:

* A [Java Developer Kit](https://www.azul.com/downloads/zulu/) 8-as verziója.
* Az [Apache Maven](https://maven.apache.org) 3.0-s vagy újabb verziója.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure Portalra a <https://portal.azure.com/> webhelyen az Azure-fiókjával.

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

[!INCLUDE [Clone application](includes/signalr-quickstart-clone-application.md)]

## <a name="configure-and-run-the-azure-function-app"></a>Az Azure-függvényalkalmazás konfigurálása és futtatása

1. A böngészőben, amelyben meg van nyitva az Azure Portal, a portál tetején levő keresőmezőben a példány nevére való kereséssel ellenőrizze, hogy a korábban üzembe helyezett SignalR-szolgáltatáspéldány sikeresen létrejött-e. A megnyitáshoz válassza ki a példányt.

    ![A SignalR-szolgáltatáspéldány megkeresése](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

1. Válassza a **Kulcsok** elemet a SignalR-szolgáltatáspéldány kapcsolati sztringjeinek megtekintéséhez.

1. Válassza ki és másolja a vágólapra az elsődleges kapcsolati sztring értékét.

    ![SignalR szolgáltatás létrehozása](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-keys.png)

1. A Kódszerkesztő, nyissa meg a *a java/src/Csevegés* a klónozott adattár-mappában.

1. Nevezze át a *local.settings.sample.json* fájlt *local.settings.json* névre.

1. A **local.settings.json** fájlban illessze be a kapcsolati sztringet az **AzureSignalRConnectionString** beállítás értékéhez. Mentse a fájlt.

1. A fő fájlt, amely tartalmazza a funkciók vannak *src/chat/java/src/main/java/com/function/Functions.java*:

    - **negotiate** – A *SignalRConnectionInfo* bemeneti kötést használja érvényes kapcsolatadatok létrehozásához és visszaküldéséhez.
    - **SendMessage** – Csevegési üzenet fogadása a kérelem törzsében szereplő, és használja a *SignalR* kimeneti, kötelező, az üzenet összes szórási csatlakoztatva az ügyfélalkalmazások számára.

1. A terminálban ellenőrizze, hogy az a *a java/src/Csevegés* mappát. A függvényalkalmazás létrehozása.

    ```bash
    mvn clean package
    ```

1. Futtassa helyben a függvényalkalmazást.

    ```bash
    mvn azure-functions:run
    ```

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott, és futtatta a Maven használatával valós idejű kiszolgáló nélküli alkalmazás. Ezután megtudhatja, hogyan hozhat létre Java az Azure Functions előzmények.

> [!div class="nextstepaction"]
> [Az első függvény létrehozása a Java és Maven](../azure-functions/functions-create-first-java-maven.md)