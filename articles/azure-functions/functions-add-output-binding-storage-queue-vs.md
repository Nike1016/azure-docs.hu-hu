---
title: Függvények összekötése az Azure Storage-ba a Visual Studióval
description: Megtudhatja, hogyan adhat hozzá kimeneti kötést C# az osztály könyvtára funkcióinak Azure Storage-várólistákhoz való összekapcsolásához a Visual Studióval.
author: ggailey777
ms.author: glenga
ms.date: 07/22/2019
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
manager: gwallace
ms.openlocfilehash: 6ef6f4ae86f0f732164603bd8b38dc1bbef95dd9
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68600258"
---
# <a name="connect-functions-to-azure-storage-using-visual-studio"></a>Függvények összekötése az Azure Storage-ba a Visual Studióval

Azure Functions lehetővé teszi a függvények Azure-szolgáltatásokhoz és más erőforrásokhoz való összekapcsolását anélkül, hogy saját integrációs kódot kellene írnia. Ezeket az előre definiált kapcsolatokat *kötéseknek*nevezzük. A kötések lehetővé teszik, hogy a függvény az Azure-szolgáltatásokat és más erőforrásokat használja bemenetként és kimenetként egy függvénynek.

A függvény végrehajtását egyetlen *trigger*indítja el. Az trigger egy speciális típusú bemeneti kötés. Habár egy függvénynek csak egy triggere van, több bemeneti és kimeneti kötés is lehet. További információ: [Azure functions triggerek és kötések fogalmai](functions-triggers-bindings.md).

Ez a cikk bemutatja, hogyan használhatja a Visual studiót az [előző] rövid útmutatóban létrehozott funkció Azure Storage-ba való összekapcsolásához. Az ehhez a függvényhez hozzáadott kimeneti kötés adatokat ír a HTTP-kérelemből egy Azure üzenetsor-tárolási várólistán lévő üzenetbe. 

A legtöbb kötéshez olyan tárolt kapcsolati karakterlánc szükséges, amelyet a függvények a kötött szolgáltatás eléréséhez használnak. A könnyebb kezelhetőség érdekében használja a Function alkalmazással létrehozott Storage-fiókot. A fiókhoz való kapcsolódás már egy nevű `AzureWebJobsStorage`alkalmazás-beállításban van tárolva.  

## <a name="prerequisites"></a>Előfeltételek

A cikk elindítása előtt a következőket kell tennie: 

 - [./Functions-Create-First-Function-vs-code.MD] [a Visual Studio gyors üzembe helyezésének 1. része]. 

- Jelentkezzen be az Azure-előfizetésbe a Visual studióból

## <a name="download-the-function-app-settings"></a>A függvény alkalmazás beállításainak letöltése

Az [előző](functions-create-first-function-vs-code.md)rövid útmutatóban létrehozott egy Function alkalmazást az Azure-ban a szükséges Storage-fiókkal együtt. A fiókhoz tartozó kapcsolatok karakterlánca biztonságosan tárolódik az Azure-beli alkalmazás beállításaiban. Ebben a cikkben egy fiókba írja az üzeneteket egy tárolási várólistába. Ha a funkciót helyileg futtatja, a Storage-fiókhoz való csatlakozáshoz le kell töltenie az Alkalmazásbeállítások a *Local. Settings. JSON* fájlra. 

1. A **Megoldáskezelőben** kattintson a jobb gombbal a projektre, és válassza a **Publish** (Közzététel) lehetőséget. 

1. A **műveletek**területen válassza a **Azure app Service beállítások szerkesztése**lehetőséget. 

    ![Az Alkalmazásbeállítások szerkesztése](media/functions-add-output-binding-storage-queue-vs/edit-app-settings.png)

1. A **AzureWebJobsStorage**alatt másolja a **távoli** karakterlánc értéket a **helyi**értékre, majd kattintson **az OK gombra**. 

A kapcsolat `AzureWebJobsStorage` beállítását használó tárolási kötések mostantól helyileg is csatlakozhatnak a várólista-tárolóhoz.

## <a name="register-binding-extensions"></a>Kötési bővítmények regisztrálása

Mivel a várólista-tároló kimeneti kötését használja, a projekt futtatása előtt telepítenie kell a Storage-kötések bővítményt. A HTTP-és időzítő-eseményindítók kivételével a kötések kiterjesztési csomagként vannak implementálva. 

1. Az **eszközök** menüben válassza a **NuGet** > Package Manager**csomagkezelő konzol**lehetőséget. 

1. A-konzolon futtassa a következő [Install-Package](/nuget/tools/ps-ref-install-package) parancsot a tárolási bővítmények telepítéséhez:

    ```Command
    Install-Package Microsoft.Azure.WebJobs.Extensions.Storage -Version 3.0.6
    ````

Most hozzáadhatja a tárolási kimeneti kötést a projekthez.

## <a name="add-an-output-binding"></a>Kimeneti kötés hozzáadása

[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]

## <a name="add-code-that-uses-the-output-binding"></a>Kimeneti kötést használó kód hozzáadása

A kötés meghatározása után a kötést használhatja `name` a függvény aláírása attribútumként való eléréséhez. Kimeneti kötés használatával nem szükséges az Azure Storage SDK-kód használata hitelesítéshez, üzenetsor-hivatkozás beszerzése vagy az adatírás. A functions futtatókörnyezet és a várólista kimeneti kötése elvégzi ezeket a feladatokat.

[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]

## <a name="run-the-function-locally"></a>Függvény helyi futtatása

[!INCLUDE [functions-run-function-test-local-vs](../../includes/functions-run-function-test-local-vs.md)]

A rendszer létrehoz egy `outqueue` nevű új várólistát a Storage-fiókban a functions futtatókörnyezetben a kimeneti kötés első használatakor. A Cloud Explorer használatával ellenőrizheti, hogy a várólista létrejött-e az új üzenettel együtt.

## <a name="examine-the-output-queue"></a>A kimeneti üzenetsor vizsgálata

1. A Visual Studióban a **nézet** menüben válassza a **Cloud Explorer**lehetőséget.

1. A **Cloud Explorerben**bontsa ki az Azure-előfizetések és a **Storage-fiókok**csomópontot, majd bontsa ki a függvény által használt Storage-fiókot. Ha nem emlékszik a Storage-fiók nevére, ellenőrizze a `AzureWebJobsStorage` *helyi. Settings. JSON* fájlban a kapcsolatok karakterláncának beállítását.  

1. Bontsa ki a **várólisták** csomópontot, majd kattintson duplán a  "Dequeue" nevű várólistára a várólista tartalmának megtekintéséhez a Visual Studióban. 

   Az üzenetsor tartalmazza az üzenetet, amelyet az üzenetsor kimeneti kötése létrehozott a HTTP által aktivált függvény futtatásakor. Ha a függvényt az `name` *Azure*alapértelmezett értékével hívja meg, a várólista-üzenet a *következő függvénynek lesz átadva: Azure*-ban.

    ![Üzenetsor-üzenet látható Azure Storage Explorer](./media/functions-add-output-binding-storage-queue-vs-code/function-queue-storage-output-view-queue.png)

1. Futtassa újra a függvényt, küldjön egy másik kérést, és megjelenik egy új üzenet a várólistában.  

Itt az ideje, hogy újra közzé lehessen tenni a frissített Function alkalmazást az Azure-ban.

## <a name="redeploy-and-verify-the-updated-app"></a>A frissített alkalmazás újbóli üzembe helyezése és ellenőrzése

1. **Megoldáskezelő**kattintson a jobb gombbal a projektre, és válassza a **Közzététel**lehetőséget, majd válassza a **Közzététel** lehetőséget a projekt újbóli közzétételéhez az Azure-ban.

1. Az üzembe helyezés befejezése után újra használhatja a böngészőt az újratelepített függvény teszteléséhez. Ahogy korábban is, fűzze hozzá a `&name=<yourname>` lekérdezési karakterláncot az URL-címhez.

1. Ismét [tekintse meg az üzenetet a Storage-várólistán](#examine-the-output-queue) annak ellenőrzéséhez, hogy a kimeneti kötés ismét létrehoz egy új üzenetet a várólistában.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>További lépések

Frissítette a HTTP által aktivált függvényt az adattárolási várólistába való íráshoz. További információ a függvények fejlesztéséről: [Azure functions fejlesztése a Visual Studióval](functions-develop-vs.md).

Ezután engedélyezze Application Insights figyelését a Function alkalmazáshoz:

> [!div class="nextstepaction"]
> [Application Insights integráció engedélyezése](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure Storage Explorer]: https://storageexplorer.com/
[előző]: functions-create-your-first-function-visual-studio.md
