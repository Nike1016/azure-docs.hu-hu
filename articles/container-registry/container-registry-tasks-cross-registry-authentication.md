---
title: Több beállításjegyzékbeli hitelesítés egy Azure Container Registry feladatban
description: Felügyelt identitás engedélyezése Azure Container Registry (ACR) feladatban az Azure-erőforrások számára, hogy a feladat hozzáférhessen egy másik privát tároló beállításjegyzékéhez.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 07/12/2019
ms.author: danlep
ms.openlocfilehash: 07fa7f3df5274ae88c93deac75093ead3f32f036
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69509096"
---
# <a name="cross-registry-authentication-in-an-acr-task-using-an-azure-managed-identity"></a>Több beállításjegyzékbeli hitelesítés egy ACR-feladatban egy Azure által felügyelt identitás használatával 

Egy [ACR](container-registry-tasks-overview.md)-feladatban engedélyezheti az [Azure-erőforrások felügyelt identitását](container-registry-tasks-authentication-managed-identity.md). A feladat használhatja az identitást más Azure-erőforrások elérésére, anélkül, hogy hitelesítő adatokat kellene megadnia vagy kezelnie. 

Ebből a cikkből megtudhatja, hogyan engedélyezheti a felügyelt identitásokat egy olyan feladatban, amely a feladat futtatásához használttól eltérő beállításjegyzékből kér le egy rendszerképet.

Az Azure-erőforrások létrehozásához ehhez a cikkhez az Azure CLI 2.0.68 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli].

## <a name="scenario-overview"></a>Forgatókönyv áttekintése

A példában szereplő feladat egy másik Azure Container registryből származó alaprendszerkép lekérése egy alkalmazás rendszerképének elkészítéséhez és leküldéséhez. Az alaprendszerkép lekéréséhez konfigurálja a feladatot egy felügyelt identitással, és rendelje hozzá a megfelelő engedélyeket. 

Ez a példa egy felhasználó által hozzárendelt vagy rendszer által hozzárendelt felügyelt identitást használó lépéseket mutatja be. Az Ön által választott identitás a szervezet igényeitől függ.

A valós forgatókönyvekben a szervezetek az összes fejlesztői csapat által az alkalmazásaikat felépítő alaplemezképek készletét is tarthatják. Ezeket az alaplemezképeket egy vállalati beállításjegyzék tárolja, és minden fejlesztői csapat csak lekéréses jogokkal rendelkezik. 

## <a name="prerequisites"></a>Előfeltételek

Ehhez a cikkhez két Azure Container-nyilvántartóra van szükség:

* Az első beállításjegyzék használatával ACR-feladatokat hozhat létre és futtathat. Ebben a cikkben a beállításjegyzék neve *myregistry*. 
* A második beállításjegyzékben található egy rendszerkép létrehozására szolgáló feladathoz használt alaprendszerkép. Ebben a cikkben a második beállításjegyzék neve *mybaseregistry*. 

Cserélje le a változót a saját beállításjegyzékbeli nevére a későbbi lépésekben.

Ha még nem rendelkezik a szükséges Azure Container-nyilvántartásokkal, tekintse meg a gyors útmutató [: Hozzon létre egy privát tároló-beállításjegyzéket](container-registry-get-started-azure-cli.md)az Azure CLI használatával. A lemezképeket még nem kell leküldeni a beállításjegyzékbe.

## <a name="prepare-base-registry"></a>Alapszintű beállításjegyzék előkészítése

Először hozzon létre egy munkakönyvtárat, majd hozzon létre egy Docker nevű fájlt az alábbi tartalommal. Ez az egyszerű példa egy Node. js-alapú alaprendszerképet hoz létre egy nyilvános rendszerképből a Docker hub-ban.
    
```bash
echo FROM node:9-alpine > Dockerfile
```
Az aktuális könyvtárban futtassa az az [ACR Build][az-acr-build] parancsot az alaprendszerkép kiépítéséhez és az alap beállításjegyzékbe való leküldéséhez. A gyakorlatban a szervezet egy másik csapata vagy folyamata is megtarthatja az alap beállításjegyzéket.
    
```azurecli
az acr build --image baseimages/node:9-alpine --registry mybaseregistry --file Dockerfile .
```

## <a name="define-task-steps-in-yaml-file"></a>Feladat lépéseinek meghatározása a YAML fájlban

A példa többlépéses [](container-registry-tasks-multi-step.md) feladatának lépései egy [YAML-fájlban](container-registry-tasks-reference-yaml.md)vannak meghatározva. Hozzon létre egy `helloworldtask.yaml` nevű fájlt a helyi munkakönyvtárban, és illessze be a következő tartalmakat. Frissítse az értékét `REGISTRY_NAME` a Build lépésben az alapszintű beállításjegyzék kiszolgálójának nevével.

```yml
version: v1.0.0
steps:
# Replace mybaseregistry with the name of your registry containing the base image
  - build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}}  https://github.com/Azure-Samples/acr-build-helloworld-node.git -f Dockerfile-app --build-arg REGISTRY_NAME=mybaseregistry.azurecr.io
  - push: ["{{.Run.Registry}}/hello-world:{{.Run.ID}}"]
```

A Build lépés az `Dockerfile-app` [Azure-Samples/ACR-Build-HelloWorld-Node](https://github.com/Azure-Samples/acr-build-helloworld-node.git) adattár fájlját használja egy rendszerkép létrehozásához. A `--build-arg` az alapszintű rendszerkép lekérésére hivatkozik az alapadatbázisra. A sikeres létrehozást követően a rendszer leküldi a rendszerképet a feladat futtatásához használt beállításjegyzékbe.

## <a name="option-1-create-task-with-user-assigned-identity"></a>1\. lehetőség: Feladat létrehozása felhasználó által hozzárendelt identitással

Az ebben a szakaszban szereplő lépések egy feladatot hoznak létre, és engedélyezik a felhasználó által hozzárendelt identitást. Ha inkább a rendszer által hozzárendelt identitást szeretné engedélyezni, tekintse [meg a 2. lehetőséget: Feladat létrehozása rendszer által hozzárendelt identitással](#option-2-create-task-with-system-assigned-identity). 

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="create-task"></a>Feladat létrehozása

Hozza létre a feladat *helloworldtask* a következő az [ACR Task Create][az-acr-task-create] parancs végrehajtásával. A feladat forráskód-környezet nélkül fut, és a parancs a munkakönyvtárban `helloworldtask.yaml` található fájlra hivatkozik. A `--assign-identity` paraméter átadja a felhasználó által hozzárendelt identitás erőforrás-azonosítóját. 

```azurecli
az acr task create \
  --registry myregistry \
  --name helloworldtask \
  --context /dev/null \
  --file helloworldtask.yaml \
  --assign-identity $resourceID
```

[!INCLUDE [container-registry-tasks-user-id-properties](../../includes/container-registry-tasks-user-id-properties.md)]

## <a name="option-2-create-task-with-system-assigned-identity"></a>2\. lehetőség: Feladat létrehozása rendszer által hozzárendelt identitással

Az ebben a szakaszban szereplő lépések egy feladatot hoznak létre, és engedélyezik a rendszer által hozzárendelt identitást. Ha inkább egy felhasználó által hozzárendelt identitást szeretne engedélyezni, tekintse [meg az 1. lehetőséget: Feladat létrehozása felhasználó által hozzárendelt identitással](#option-1-create-task-with-user-assigned-identity). 

### <a name="create-task"></a>Feladat létrehozása

Hozza létre a feladat *helloworldtask* a következő az [ACR Task Create][az-acr-task-create] parancs végrehajtásával. A feladat forráskód-környezet nélkül fut, és a parancs a munkakönyvtárban `helloworldtask.yaml` található fájlra hivatkozik. Az `--assign-identity` érték nélküli paraméter lehetővé teszi a rendszer által hozzárendelt identitást a feladatban. 

```azurecli
az acr task create \
  --registry myregistry \
  --name helloworldtask \
  --context /dev/null \
  --file helloworldtask.yaml \
  --assign-identity 
```
[!INCLUDE [container-registry-tasks-system-id-properties](../../includes/container-registry-tasks-system-id-properties.md)]

## <a name="give-identity-pull-permissions-to-the-base-registry"></a>Identitás-lekérési engedélyek megadása az alap beállításjegyzékhez

Ebben a szakaszban adja meg a felügyelt identitás engedélyeit az alapszintű beállításjegyzékből való lekéréshez, *mybaseregistry*.

Használja az az [ACR show][az-acr-show] parancsot az alapszintű beállításjegyzék erőforrás-azonosítójának lekéréséhez és egy változóban való tárolásához:

```azurecli
baseregID=$(az acr show --name mybaseregistry --query id --output tsv)
```

Az az [szerepkör-hozzárendelés létrehozási][az-role-assignment-create] parancs használatával rendelje hozzá `acrpull` a szerepkört az alap beállításjegyzékhez. Ez a szerepkör csak a lemezképek beállításjegyzékből való lekéréséhez rendelkezik jogosultságokkal.

```azurecli
az role assignment create --assignee $principalID --scope $baseregID --role acrpull
```

## <a name="add-target-registry-credentials-to-task"></a>Cél beállításjegyzékbeli hitelesítő adatok hozzáadása a feladathoz

Most használja az az [ACR Task hitelesítőadat Add][az-acr-task-credential-add] paranccsal, hogy hozzáadja az identitás hitelesítő adatait a feladathoz, hogy az képes legyen hitelesíteni az alap beállításjegyzékével. Futtassa a parancsot a feladatban engedélyezett felügyelt identitás típusának megfelelően. Ha engedélyezte a felhasználó által hozzárendelt identitást `--use-identity` , adja át az identitás ügyfél-azonosítóját. Ha engedélyezve van egy rendszerhez rendelt identitás, pass `--use-identity [system]`.

```azurecli
# Add credentials for user-assigned identity to the task
az acr task credential add \
  --name helloworldtask \
  --registry myregistry \
  --login-server mybaseregistry.azurecr.io \
  --use-identity $clientID

# Add credentials for system-assigned identity to the task
az acr task credential add \
  --name helloworldtask \
  --registry myregistry \
  --login-server mybaseregistry.azurecr.io \
  --use-identity [system]
```

## <a name="manually-run-the-task"></a>A feladat manuális futtatása

Annak ellenőrzéséhez, hogy a felügyelt identitást engedélyező feladat sikeresen fut-e, manuálisan aktiválja a feladatot az az [ACR Task Run][az-acr-task-run] paranccsal. 

```azurecli
az acr task run \
  --name helloworldtask \
  --registry myregistry
```

Ha a feladat sikeresen fut, a kimenet a következőhöz hasonló:

```
Queued a run with ID: cf10
Waiting for an agent...
2019/06/14 22:47:32 Using acb_vol_dbfbe232-fd76-4ca3-bd4a-687e84cb4ce2 as the home volume
2019/06/14 22:47:39 Creating Docker network: acb_default_network, driver: 'bridge'
2019/06/14 22:47:40 Successfully set up Docker network: acb_default_network
2019/06/14 22:47:40 Setting up Docker configuration...
2019/06/14 22:47:41 Successfully set up Docker configuration
2019/06/14 22:47:41 Logging in to registry: myregistry.azurecr.io
2019/06/14 22:47:42 Successfully logged into myregistry.azurecr.io
2019/06/14 22:47:42 Logging in to registry: mybaseregistry.azurecr.io
2019/06/14 22:47:43 Successfully logged into mybaseregistry.azurecr.io
2019/06/14 22:47:43 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/14 22:47:43 Scanning for dependencies...
2019/06/14 22:47:45 Successfully scanned dependencies
2019/06/14 22:47:45 Launching container with name: acb_step_0
Sending build context to Docker daemon   25.6kB
Step 1/6 : ARG REGISTRY_NAME
Step 2/6 : FROM ${REGISTRY_NAME}/baseimages/node:9-alpine
9-alpine: Pulling from baseimages/node
[...]
Successfully built 41b49a112663
Successfully tagged myregistry.azurecr.io/hello-world:cf10
2019/06/14 22:47:56 Successfully executed container: acb_step_0
2019/06/14 22:47:56 Executing step ID: acb_step_1. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/14 22:47:56 Pushing image: myregistry.azurecr.io/hello-world:cf10, attempt 1
The push refers to repository [myregistry.azurecr.io/hello-world]
[...]
2019/06/14 22:48:00 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 2.517011)
2019/06/14 22:48:00 The following dependencies were found:
2019/06/14 22:48:00
- image:
    registry: myregistry.azurecr.io
    repository: hello-world
    tag: cf10
    digest: sha256:611cf6e3ae3cb99b23fadcd89fa144e18aa1b1c9171ad4a0da4b62b31b4e38d1
  runtime-dependency:
    registry: mybaseregistry.azurecr.io
    repository: baseimages/node
    tag: 9-alpine
    digest: sha256:e8e92cffd464fce3be9a3eefd1b65dc9cbe2484da31c11e813a4effc6105c00f
  git:
    git-head-revision: 0f988779c97fe0bfc7f2f74b88531617f4421643

Run ID: cf10 was successful after 32s
```

Futtassa az az [ACR repository show-Tags][az-acr-repository-show-tags] parancsot annak ellenőrzéséhez, hogy a rendszerkép létrehozva lett-e, és sikeresen leküldve a *myregistry*:

```azurecli
az acr repository show-tags --name myregistry --repository hello-world --output tsv
```

Példa a kimenetre:

```console
cf10
```

## <a name="next-steps"></a>További lépések

* További információ a [felügyelt identitások ACR-feladatokban való engedélyezéséről](container-registry-tasks-authentication-managed-identity.md).
* Lásd az [ACR-feladatok YAML](container-registry-tasks-reference-yaml.md) -referenciáját

<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[az-group-create]: /cli/azure/group?#az-group-create
