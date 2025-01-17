---
title: Az Azure Event Grid használata a feltöltött képek átméretezésének automatizálására | Microsoft Docs
description: Az Azure Event Grid a blobok Azure Storage-ba való feltöltésekor aktiválódhat. A segítségével Azure Storage-ba feltöltött képfájlokat küldhet más szolgáltatásoknak, például az Azure Functions szolgáltatásnak átméretezés vagy egyéb javítás céljából.
services: event-grid, functions
author: spelluru
manager: jpconnoc
editor: ''
ms.service: event-grid
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/29/2019
ms.author: spelluru
ms.custom: mvc
ms.openlocfilehash: 652fe182663d37c389658c8fe3b172826168e51f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60462827"
---
# <a name="tutorial-automate-resizing-uploaded-images-using-event-grid"></a>Oktatóanyag: Feltöltött képek átméretezésének automatizálása az Event Grid használatával

Az [Azure Event Grid](overview.md) egy felhőalapú eseménykezelési szolgáltatás. Az Event Grid lehetővé teszi, hogy előfizetéseket hozzon létre az Azure-szolgáltatások vagy külső erőforrások által létrehozott eseményekhez.  

Ez az oktatóanyag a Storage oktatóanyag-sorozat második része. Kibővíti az [előző Storage oktatóanyagot][previous-tutorial], és hozzáadja a kiszolgáló nélküli automatikus miniatűr-létrehozást az Azure Event Grid és az Azure Functions használatával. Az Event Grid lehetővé teszi az [Azure Functions](../azure-functions/functions-overview.md) számára, hogy reagáljon az [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) eseményeire, és létrehozza a feltöltött képek miniatűrjeit. Az esemény-előfizetések a Blob Storage-esemény létrehozásakor jönnek létre. Amikor egy blob bekerül egy adott Blob Storage-tárolóba, a rendszer meghívja egy függvény végpontját. Az Event Gridből a függvénykötésnek továbbadott adatokat a rendszer arra használja, hogy hozzáférjen a blobhoz, és létrehozza a miniatűrt.

Az Azure CLI és az Azure Portal segítségével hozzáadja az átméretezési funkciót egy meglévő képfeltöltő alkalmazáshoz.

# <a name="nettabdotnet"></a>[\.NET](#tab/dotnet)

![Közzétett webes alkalmazás a böngészőben](./media/resize-images-on-storage-blob-upload-event/tutorial-completed.png)

# <a name="nodejs-v2-sdktabnodejs"></a>[Node.js V2 SDK](#tab/nodejs)

![Közzétett webes alkalmazás a böngészőben](./media/resize-images-on-storage-blob-upload-event/upload-app-nodejs-thumb.png)

# <a name="nodejs-v10-sdktabnodejsv10"></a>[Node.js V10 SDK](#tab/nodejsv10)

![Közzétett webes alkalmazás a böngészőben](./media/resize-images-on-storage-blob-upload-event/upload-app-nodejs-thumb.png)

---

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Általános célú Azure Storage-fiók létrehozása
> * Kiszolgáló nélküli programkód üzembe helyezése az Azure Functions használatával
> * Blob Storage esemény-előfizetés létrehozása az Event Gridben

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az oktatóanyag elvégzéséhez:

El kell végeznie az előző Blob storage oktatóanyagot: [A felhőben az Azure Storage képadatok feltöltése][previous-tutorial].

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Ha korábban még nem regisztrálta az Event Grid erőforrás-szolgáltatót az előfizetésében, mindenképp regisztrálja.

```azurecli-interactive
az provider register --namespace Microsoft.EventGrid
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.0.14-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli). 

Ha nem a Cloud Shellt használja, először be kell jelentkeznie a(z) `az login` használatával.

## <a name="create-an-azure-storage-account"></a>Azure Storage-fiók létrehozása

Az Azure Functions szolgáltatásnak egy általános célú tárfiókra van szüksége. Mellett a Blob storage-fiókban az előző oktatóanyagban létrehozott egy különálló, általános célú tárfiók a használatával hozható létre az erőforráscsoportot a [az tárfiók létrehozása](/cli/azure/storage/account) parancsot. A tárfiókok neve 3–24 karakter hosszúságú lehet, és csak számokból és kisbetűkből állhat. 

1. Állítsa be egy változót, amely az előző oktatóanyagban létrehozott erőforráscsoport nevét tárolja. 

    ```azurecli-interactive
    resourceGroupName=myResourceGroup
    ```
2. Állítson be egy változót az Azure Functions igénylő új tárfiók nevére. 

    ```azurecli-interactive
    functionstorage=<name of the storage account to be used by the function>
    ```
3. A storage-fiókját az Azure-függvény létrehozása. 

    ```azurecli-interactive
    az storage account create --name $functionstorage --location southeastasia \
    --resource-group $resourceGroupName --sku Standard_LRS --kind storage
    ```

## <a name="create-a-function-app"></a>Függvényalkalmazás létrehozása  

Rendelkeznie kell egy függvényalkalmazással a függvény végrehajtásának biztosításához. A függvényalkalmazás szolgáltat környezetet a függvénykód kiszolgáló nélküli végrehajtásához. Hozzon létre egy függvényalkalmazást az [az functionapp create](/cli/azure/functionapp) parancs használatával. 

A következő parancsot adja meg a saját egyedi függvényalkalmazás-nevét. A függvényalkalmazás nevét a rendszer a függvényalkalmazás alapértelmezett DNS-tartományának részeként használja, ezért egyedinek kell lennie az Azure összes alkalmazásában. 

1. Adjon meg egy nevet, a függvényalkalmazás, létre kell hozni. 

    ```azurecli-interactive
    functionapp=<name of the function app>
    ```
2. Az Azure-függvény létrehozása. 

    ```azurecli-interactive
    az functionapp create --name $functionapp --storage-account $functionstorage \
    --resource-group $resourceGroupName --consumption-plan-location southeastasia
    ```

Most konfigurálnia kell a függvényalkalmazást, hogy csatlakozzon az [előző oktatóanyagban][previous-tutorial] létrehozott Blob Storage-fiókhoz.

## <a name="configure-the-function-app"></a>A függvényalkalmazás konfigurálása

A függvénynek szüksége van a hitelesítő adatokat a Blob storage-fiók, amelyre kerülnek, a függvény használatával az alkalmazás beállításait a [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings) parancsot.

# <a name="nettabdotnet"></a>[\.NET](#tab/dotnet)

```azurecli-interactive
blobStorageAccount=<name of the Blob storage account you created in the previous tutorial>
storageConnectionString=$(az storage account show-connection-string --resource-group $resourceGroupName \
--name $blobStorageAccount --query connectionString --output tsv)

az functionapp config appsettings set --name $functionapp --resource-group $resourceGroupName \
--settings AzureWebJobsStorage=$storageConnectionString THUMBNAIL_CONTAINER_NAME=thumbnails \
THUMBNAIL_WIDTH=100 FUNCTIONS_EXTENSION_VERSION=~2
```

# <a name="nodejs-v2-sdktabnodejs"></a>[Node.js V2 SDK](#tab/nodejs)

```azurecli-interactive
blobStorageAccount=<name of the Blob storage account you created in the previous tutorial>

storageConnectionString=$(az storage account show-connection-string --resource-group $resourceGroupName \
--name $blobStorageAccount --query connectionString --output tsv)

az functionapp config appsettings set --name $functionapp --resource-group $resourceGroupName \
--settings AZURE_STORAGE_CONNECTION_STRING=$storageConnectionString \
THUMBNAIL_WIDTH=100 FUNCTIONS_EXTENSION_VERSION=~2
```

# <a name="nodejs-v10-sdktabnodejsv10"></a>[Node.js V10 SDK](#tab/nodejsv10)

```azurecli-interactive
blobStorageAccount=<name of the Blob storage account you created in the previous tutorial>

blobStorageAccountKey=$(az storage account keys list -g $resourceGroupName \
-n $blobStorageAccount --query [0].value --output tsv)

storageConnectionString=$(az storage account show-connection-string --resource-group $resourceGroupName \
--name $blobStorageAccount --query connectionString --output tsv)

az functionapp config appsettings set --name $functionapp --resource-group $resourceGroupName \
--settings FUNCTIONS_EXTENSION_VERSION=~2 BLOB_CONTAINER_NAME=thumbnails \
AZURE_STORAGE_ACCOUNT_NAME=$blobStorageAccount \
AZURE_STORAGE_ACCOUNT_ACCESS_KEY=$blobStorageAccountKey \
AZURE_STORAGE_CONNECTION_STRING=$storageConnectionString
```

---

A `FUNCTIONS_EXTENSION_VERSION=~2` beállítással a függvényalkalmazás az Azure Functions futtatókörnyezet 2.x-es verzióján fut.

Most már üzembe helyezhet egy függvénykód-projektet a függvényalkalmazásban.

## <a name="deploy-the-function-code"></a>A függvénykód üzembe helyezése 

# <a name="nettabdotnet"></a>[\.NET](#tab/dotnet)

A minta C# átméretezési funkció érhető el a [GitHub](https://github.com/Azure-Samples/function-image-upload-resize). Ez a kód projekt telepítése a függvényalkalmazás használatával a [az functionapp deployment source config](/cli/azure/functionapp/deployment/source) parancsot. 

```azurecli-interactive
az functionapp deployment source config --name $functionapp --resource-group $resourceGroupName --branch master --manual-integration --repo-url https://github.com/Azure-Samples/function-image-upload-resize
```

# <a name="nodejs-v2-sdktabnodejs"></a>[Node.js V2 SDK](#tab/nodejs)

A minta Node.js-átméretezési függvény elérhető a [GitHubon](https://github.com/Azure-Samples/storage-blob-resize-function-node). Helyezze üzembe ezt a Functions-kódprojektet az [az functionapp deployment source config](/cli/azure/functionapp/deployment/source) paranccsal.

```azurecli-interactive
az functionapp deployment source config --name $functionapp \
--resource-group $resourceGroupName --branch master --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-resize-function-node
```

# <a name="nodejs-v10-sdktabnodejsv10"></a>[Node.js V10 SDK](#tab/nodejsv10)

A minta Node.js-átméretezési függvény elérhető a [GitHubon](https://github.com/Azure-Samples/storage-blob-resize-function-node-v10). Helyezze üzembe ezt a Functions-kódprojektet az [az functionapp deployment source config](/cli/azure/functionapp/deployment/source) paranccsal.

```azurecli-interactive
az functionapp deployment source config --name $functionapp \
--resource-group $resourceGroupName --branch master --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-resize-function-node-v10
```
---

A kép átméretezése függvényt az Event Grid szolgáltatásból küldött HTTP-kérések indítják el. Egy esemény-előfizetés létrehozásával utasítja az Event Gridet, hogy ezeket az értesítéseket a függvénye URL-címére szeretné kapni. Ebben az oktatóanyagban blobok által létrehozott eseményekre iratkozik fel.

Az Event Grid-értesítésből a függvénynek átadott adatok között megtalálható a blob URL-címe. Ezt az URL-címet ezután az eseményindító átadja a bemeneti kötésnek a feltöltött kép Blob Storage-ból való lekéréséhez. A függvény létrehoz egy miniatűrt, és a Blob Storage egyik külön tárolójába írja az eredményül kapott streamet. 

A projekt az `EventGridTrigger` típusú eseményindítót használja. Az általános HTTP-eseményindítók helyett az Event Grid eseményindító használata ajánlott. Az Event Grid automatikusan érvényesíti az Event Grid függvény eseményindítóit. Általános HTTP-eseményindítók esetén meg kell valósítani az [érvényesítési választ](security-authentication.md).

# <a name="nettabdotnet"></a>[\.NET](#tab/dotnet)

A függvénnyel kapcsolatos további tudnivalókért tekintse meg a [function.json és run.csx fájlt](https://github.com/Azure-Samples/function-image-upload-resize/tree/master/ImageFunctions).

# <a name="nodejs-v2-sdktabnodejs"></a>[Node.js V2 SDK](#tab/nodejs)

A függvénnyel kapcsolatos további tudnivalókért lásd: a [function.json és az index.js fájlt](https://github.com/Azure-Samples/storage-blob-resize-function-node/tree/master/Thumbnail).

# <a name="nodejs-v10-sdktabnodejsv10"></a>[Node.js V10 SDK](#tab/nodejsv10)

A függvénnyel kapcsolatos további tudnivalókért lásd: a [function.json és az index.js fájlt](https://github.com/Azure-Samples/storage-blob-resize-function-node-v10/tree/master/Thumbnail).

---

A függvény projektkódját a rendszer közvetlenül a nyilvános mintaadattárból helyezi üzembe. További információk az Azure Functions üzembe helyezési lehetőségeiről: [Azure Functions – folyamatos üzembe helyezés](../azure-functions/functions-continuous-deployment.md).

## <a name="create-an-event-subscription"></a>Esemény-előfizetés létrehozása

Az esemény-előfizetés jelzi, hogy melyik szolgáltató eseményeit kívánja elküldeni egy adott végpontnak. Ebben az esetben a függvény közzéteszi a végpontot. Az alábbi lépésekkel hozzon létre egy esemény-előfizetést, amely értesítéseket küld a függvényének az Azure Portalon: 

1. Az a [az Azure portal](https://portal.azure.com)válassza **minden szolgáltatás** a bal oldali menüben, és válassza ki a **Függvényalkalmazások**. 

    ![Keresse meg a Függvényalkalmazásokat az Azure Portalon](./media/resize-images-on-storage-blob-upload-event/portal-find-functions.png)

2. Bontsa ki a függvényalkalmazást, válassza ki a **miniatűr** függvényt, és válassza ki **hozzáadása Event Grid-előfizetés**.

    ![Keresse meg a Függvényalkalmazásokat az Azure Portalon](./media/resize-images-on-storage-blob-upload-event/add-event-subscription.png)

3. Használja a táblázatban megadott esemény-előfizetési beállításokat.
    
    ![Esemény-előfizetés létrehozása a függvényből az Azure Portalon](./media/resize-images-on-storage-blob-upload-event/event-subscription-create.png)

    | Beállítás      | Ajánlott érték  | Leírás                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Name (Név)** | imageresizersub | Az új esemény-előfizetés azonosítóneve. | 
    | **Témakörtípus** |  Tárfiókok | Válassza ki a Storage-fiók eseményszolgáltatóját. | 
    | **Előfizetés** | Az Azure-előfizetése | Alapértelmezés szerint az aktuális Azure-előfizetés van kiválasztva.   |
    | **Erőforráscsoport** | myResourceGroup | Válassza a **Meglévő használata** elemet, majd válassza ki az oktatóanyagban használt erőforráscsoportot.  |
    | **Erőforrás** |  Saját Blob Storage-fiók |  Válassza ki a létrehozott Blob Storage-fiókot. |
    | **Eseménytípusok** | Létrehozott blob | Törölje a jelölést az összes típus mellől a **Létrehozott blob** kivételével. A rendszer csak a `Microsoft.Storage.BlobCreated` eseménytípusokat adja át a függvénynek.| 
    | **Előfizető típusa** |  automatikusan létrehozott |  Előre meg van határozva webhookként. |
    | **Előfizető végpontja** | automatikusan létrehozott | Használja a rendszer által létrehozott végpont URL-címét. | 
4. Váltson a **szűrő** lapra, és hajtsa végre a következőket:     
    1. Válassza ki **szűrésének engedélyezése tulajdonos** lehetőséget.
    2. A **tulajdonos kezdődik**, írja be a következő értéket: **/blobServices/alapértelmezett/tárolók/képek/BLOB/** .

        ![Adja meg az esemény-előfizetés szűrő](./media/resize-images-on-storage-blob-upload-event/event-subscription-filter.png) 
2. Válassza ki **létrehozás** az esemény-előfizetés hozzáadásához. Ez létrehoz egy esemény-előfizetést, amely `Thumbnail` működni, amikor egy blob bekerül a `images` tároló. A függvény átméretezi a képeket, és hozzáadja őket a `thumbnails` tároló.

Most, hogy konfigurálta a háttérszolgáltatásokat, tesztelni fogja a képátméretezési funkciót a minta-webalkalmazásban. 

## <a name="test-the-sample-app"></a>A mintaalkalmazás tesztelése

A képátméretezés webalkalmazásban való teszteléséhez nyissa meg a közzétett alkalmazás URL-címét. A webalkalmazás alapértelmezett URL-címe `https://<web_app>.azurewebsites.net`.

# <a name="nettabdotnet"></a>[\.NET](#tab/dotnet)

Kattintson a **Fényképek feltöltése** területre egy fájl kiválasztásához és feltöltéséhez. Fényképet is behúzhat a területre. 

Figyelje meg, hogy miután a feltöltött kép eltűnik, a feltöltött kép másolata megjelenik a **Létrehozott miniatűrök** nevű forgó képválasztón. A függvény átméretezte a képet, hozzáadta a *miniatűrök* tárolóhoz, a webes ügyfél pedig letöltötte a képet.

![Közzétett webes alkalmazás a böngészőben](./media/resize-images-on-storage-blob-upload-event/tutorial-completed.png)

# <a name="nodejs-v2-sdktabnodejs"></a>[Node.js V2 SDK](#tab/nodejs)

Kattintson a **fájl kiválasztása** jelöljön ki egy fájlt, majd kattintson a **kép feltöltése**. Ha a feltöltés sikeres volt, egy sikeres lapra navigál. Kattintson a hivatkozásra kattintva térjen vissza a kezdőlapon. A feltöltött kép másolata megjelenik a **létrehozott miniatűrök** területen. (Ha a kép nem jelenik meg először, próbálja meg újból az oldal.) A függvény átméretezte a képet, hozzáadta a *miniatűrök* tárolóhoz, a webes ügyfél pedig letöltötte a képet.

![Közzétett webes alkalmazás a böngészőben](./media/resize-images-on-storage-blob-upload-event/upload-app-nodejs-thumb.png)

# <a name="nodejs-v10-sdktabnodejsv10"></a>[Node.js V10 SDK](#tab/nodejsv10)

Kattintson a **fájl kiválasztása** jelöljön ki egy fájlt, majd kattintson a **kép feltöltése**. Ha a feltöltés sikeres volt, egy sikeres lapra navigál. Kattintson a hivatkozásra kattintva térjen vissza a kezdőlapon. A feltöltött kép másolata megjelenik a **létrehozott miniatűrök** területen. (Ha a kép nem jelenik meg először, próbálja meg újból az oldal.) A függvény átméretezte a képet, hozzáadta a *miniatűrök* tárolóhoz, a webes ügyfél pedig letöltötte a képet.

![Közzétett webes alkalmazás a böngészőben](./media/resize-images-on-storage-blob-upload-event/upload-app-nodejs-thumb.png)

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Általános célú Azure Storage-fiók létrehozása
> * Kiszolgáló nélküli programkód üzembe helyezése az Azure Functions használatával
> * Blob Storage esemény-előfizetés létrehozása az Event Gridben

Lépjen a Storage oktatóanyag-sorozat harmadik részére, amelyből megtudhatja, hogyan férhet hozzá biztonságosan a tárfiókhoz.

> [!div class="nextstepaction"]
> [Biztonságos hozzáférés egy alkalmazás adataihoz a felhőben](../storage/blobs/storage-secure-access-application.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

+ Az Event Griddel kapcsolatos további információkért lásd: [Az Azure Event Grid bemutatása](overview.md). 
+ Ha meg szeretne próbálkozni egy másik oktatóanyaggal, amely az Azure Functions szolgáltatással foglalkozik: [Az Azure Logic Apps szolgáltatással integrálható függvények létrehozása](../azure-functions/functions-twitter-email.md). 

[previous-tutorial]: ../storage/blobs/storage-upload-process-images.md
