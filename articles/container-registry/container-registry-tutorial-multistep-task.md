---
title: Oktatóanyag – több lépésből álló tároló feladatai – Azure Container Registry feladatok
description: Ebből az oktatóanyagból megtudhatja, hogyan konfigurálhat egy Azure Container Registry feladatot úgy, hogy automatikusan indítson el egy többlépéses munkafolyamatot a tároló lemezképek létrehozásához, futtatásához és leküldéséhez a felhőben, amikor a forráskódot egy git-tárházba véglegesíti.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: tutorial
ms.date: 05/09/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: c78c2c8279972108aee12b9b386175d0f27b7fee
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68310413"
---
# <a name="tutorial-run-a-multi-step-container-workflow-in-the-cloud-when-you-commit-source-code"></a>Oktatóanyag: Többlépéses tároló munkafolyamat futtatása a felhőben a forráskód elküldésekor

Egy [gyors](container-registry-tutorial-quick-task.md)feladaton kívül az ACR-feladatok támogatják a többlépéses, többtárolós alapú munkafolyamatokat, amelyek automatikusan elindíthatják a forráskódnak a git-tárházba való beléptetését. 

Ebből az oktatóanyagból megtudhatja, hogyan használhatja például a YAML-fájlokat olyan többlépéses feladatok definiálásához, amelyek egy vagy több tároló-lemezképet állítanak be egy beállításjegyzékbe a forráskód elküldésekor. Ha olyan feladatot szeretne létrehozni, amely csak egyetlen rendszerkép-összeállítást hajt végre a kód véglegesítve, tekintse [meg az oktatóanyagot: A tároló rendszerképének automatizálása a forráskód](container-registry-tutorial-build-task.md)elkészítésekor. Az ACR-feladatok áttekintését lásd: [az operációs rendszer és a keretrendszer javításának automatizálása az ACR](container-registry-tasks-overview.md)-feladatokkal,

Ebben az oktatóanyagban:

> [!div class="checklist"]
> * Többlépéses feladat definiálása YAML-fájl használatával
> * Feladat létrehozása
> * Hitelesítő adatok hozzáadása a feladathoz egy másik beállításjegyzékhez való hozzáférés engedélyezéséhez
> * A feladat tesztelése
> * Tevékenységek állapotának megtekintése
> * A feladat aktiválása kódvéglegesítéssel

Ez az oktatóanyag feltételezi, hogy elvégezte az [előző oktatóanyag](container-registry-tutorial-quick-task.md) lépéseit. Ha még nem tette meg, hajtsa végre az előző oktatóanyag [Előfeltételek](container-registry-tutorial-quick-task.md#prerequisites) szakaszának lépéseit, mielőtt továbblépne.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha helyileg szeretné használni az Azure CLI-t, az Azure CLI **2.0.62** vagy újabb verzióját kell telepítenie, és be kell jelentkeznie az [az login][az-login]paranccsal. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepítenie vagy frissítenie kell a CLI-t, tekintse meg az [Azure CLI telepítését][azure-cli]ismertető témakört.

[!INCLUDE [container-registry-task-tutorial-prereq.md](../../includes/container-registry-task-tutorial-prereq.md)]

## <a name="create-a-multi-step-task"></a>Több lépésből álló feladat létrehozása

Most, hogy végrehajtotta azokat a lépéseket, amelyek szükségesek ahhoz, hogy az ACR-feladatok beolvassák a véglegesítési állapotot, és webhookokat hozzon létre egy adattárban, hozzon létre egy többlépéses feladatot, amely elindítja a tároló-rendszerkép létrehozását, futtatását és

### <a name="yaml-file"></a>YAML-fájl

Egy [YAML-fájlban](container-registry-tasks-reference-yaml.md)a többlépéses feladatok lépéseit adhatja meg. Az oktatóanyaghoz tartozó első példa többlépéses feladat a fájlban `taskmulti.yaml`van definiálva, amely az Ön által klónozott GitHub-tárház gyökerében található:

```yml
version: v1.0.0
steps:
# Build target image
- build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} -f Dockerfile .
# Run image 
- cmd: -t {{.Run.Registry}}/hello-world:{{.Run.ID}}
  id: test
  detach: true
  ports: ["8080:80"]
- cmd: docker stop test
# Push image
- push:
  - {{.Run.Registry}}/hello-world:{{.Run.ID}}
```

Ez a többlépéses feladat a következő műveleteket végzi el:

1. Egy `build` lépés futtatásával létrehoz egy rendszerképet a munkakönyvtár Docker. A rendszerkép célja a `Run.Registry`, a beállításjegyzék, amelyben a feladat fut, és egy egyedi ACR-feladat futtatási azonosítójával van megjelölve. 
1. Futtat egy `cmd` lépést a rendszerkép egy ideiglenes tárolóban való futtatásához. Ez a példa egy hosszan futó tárolót indít a háttérben, és visszaadja a tároló AZONOSÍTÓját, majd leállítja a tárolót. A valós forgatókönyvekben a futó tároló teszteléséhez szükséges lépéseket is megteheti, hogy megfelelően fusson.
1. Egy `push` lépésben leküldi a futtatási beállításjegyzékbe épített rendszerképet.

### <a name="task-command"></a>Feladat parancs

Először lássa el ezeket a rendszerhéj-környezeti változókat a környezetnek megfelelő értékekkel. Ez a lépés nem feltétlenül szükséges, de némileg könnyebbé teszi az oktatóanyagban lévő többsoros Azure CLI-parancsok végrehajtását. Ha nem tölti fel ezeket a környezeti változókat, manuálisan kell lecserélnie az egyes értékeket, bárhol is megjelenjenek a példában szereplő parancsokban.

```azurecli-interactive
ACR_NAME=<registry-name>        # The name of your Azure container registry
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the previous section
```

Most hozza létre a feladatot a következő az [ACR Task Create][az-acr-task-create] parancs végrehajtásával:

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name example1 \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --branch master \
    --file taskmulti.yaml \
    --git-access-token $GIT_PAT
```

Ez a feladat azt adja meg, hogy a rendszer minden alkalommal véglegesíti a *főágat* az `--context`által megadott adattárban, az ACR-feladatok a több lépésből álló feladatot futtatják az adott ág kódjában. A tárház gyökerében megadott `--file` YAML-fájl határozza meg a lépéseket. 

A sikeres az [ACR Task Create][az-acr-task-create] parancs kimenete az alábbihoz hasonló:

```console
{
  "agentConfiguration": {
    "cpu": 2
  },
  "creationDate": "2019-05-03T03:14:31.763887+00:00",
  "credentials": null,
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myregistry/providers/Microsoft.ContainerRegistry/registries/myregistry/tasks/taskmulti",
  "location": "westus",
  "name": "example1",
  "platform": {
    "architecture": "amd64",
    "os": "linux",
    "variant": null
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "status": "Enabled",
  "step": {
    "baseImageDependencies": null,
    "contextAccessToken": null,
    "contextPath": "https://github.com/gituser/acr-build-helloworld-node.git",
    "taskFilePath": "taskmulti.yaml",
    "type": "FileTask",
    "values": [],
    "valuesFilePath": null
  },
  "tags": null,
  "timeout": 3600,
  "trigger": {
    "baseImageTrigger": {
      "baseImageTriggerType": "Runtime",
      "name": "defaultBaseimageTriggerName",
      "status": "Enabled"
    },
    "sourceTriggers": [
      {
        "name": "defaultSourceTriggerName",
        "sourceRepository": {
          "branch": "master",
          "repositoryUrl": "https://github.com/gituser/acr-build-helloworld-node.git",
          "sourceControlAuthProperties": null,
          "sourceControlType": "Github"
        },
        "sourceTriggerEvents": [
          "commit"
        ],
        "status": "Enabled"
      }
    ]
  },
  "type": "Microsoft.ContainerRegistry/registries/tasks"
}
```

## <a name="test-the-multi-step-workflow"></a>A többlépéses munkafolyamat tesztelése

A többlépéses feladat teszteléséhez indítsa el manuálisan az az [ACR Task Run][az-acr-task-run] parancs futtatásával:

```azurecli-interactive
az acr task run --registry $ACR_NAME --name example1
```

Alapértelmezés szerint az `az acr task run` a naplókimenetet a konzolra streameli a parancs végrehajtásakor. A kimenet a feladatok lépéseinek futtatási folyamatát mutatja. Az alábbi kimenet a legfontosabb lépések megjelenítésére van kitömörítve.

```console
Queued a run with ID: cf19
Waiting for an agent...
2019/05/03 03:03:31 Downloading source code...
2019/05/03 03:03:33 Finished downloading source code
2019/05/03 03:03:33 Using acb_vol_cfe6bd55-3076-4215-8091-6a81aec3d1b1 as the home volume
2019/05/03 03:03:33 Creating Docker network: acb_default_network, driver: 'bridge'
2019/05/03 03:03:34 Successfully set up Docker network: acb_default_network
2019/05/03 03:03:34 Setting up Docker configuration...
2019/05/03 03:03:34 Successfully set up Docker configuration
2019/05/03 03:03:34 Logging in to registry: myregistry.azurecr.io
2019/05/03 03:03:35 Successfully logged into myregistry.azurecr.io
2019/05/03 03:03:35 Executing step ID: acb_step_0. Working directory: '', Network: 'acb_default_network'
2019/05/03 03:03:35 Scanning for dependencies...
2019/05/03 03:03:36 Successfully scanned dependencies
2019/05/03 03:03:36 Launching container with name: acb_step_0
Sending build context to Docker daemon  24.06kB

[...]

Successfully built f669bfd170af
Successfully tagged myregistry.azurecr.io/hello-world:cf19
2019/05/03 03:03:43 Successfully executed container: acb_step_0
2019/05/03 03:03:43 Executing step ID: acb_step_1. Working directory: '', Network: 'acb_default_network'
2019/05/03 03:03:43 Launching container with name: acb_step_1
279b1cb6e092b64c8517c5506fcb45494cd5a0bd10a6beca3ba97f25c5d940cd
2019/05/03 03:03:44 Successfully executed container: acb_step_1
2019/05/03 03:03:44 Executing step ID: acb_step_2. Working directory: '', Network: 'acb_default_network'
2019/05/03 03:03:44 Pushing image: myregistry.azurecr.io/hello-world:cf19, attempt 1

[...]

2019/05/03 03:03:46 Successfully pushed image: myregistry.azurecr.io/hello-world:cf19
2019/05/03 03:03:46 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 7.425169)
2019/05/03 03:03:46 Populating digests for step ID: acb_step_0...
2019/05/03 03:03:47 Successfully populated digests for step ID: acb_step_0
2019/05/03 03:03:47 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 0.827129)
2019/05/03 03:03:47 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 2.112113)
2019/05/03 03:03:47 The following dependencies were found:
2019/05/03 03:03:47
- image:
    registry: myregistry.azurecr.io
    repository: hello-world
    tag: cf19
    digest: sha256:6b981a8ca8596e840228c974c929db05c0727d8630465de536be74104693467a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 1a3065388a0238e52865db1c8f3e97492a43444c

Run ID: cf19 was successful after 18s
```

## <a name="trigger-a-build-with-a-commit"></a>Összeállítás kiváltása véglegesítéssel

A feladat manuális futtatással való tesztelését követően aktiválja automatikusan a feladatot a forráskód módosításával.

Először is győződjön meg arról, hogy az [adattár][sample-repo]helyi klónját tartalmazó könyvtárban van:

```azurecli-interactive
cd acr-build-helloworld-node
```

Ezután az alábbi parancsok futtatásával hozzon létre, véglegesítsen és küldjön le egy új fájlt a GitHub-adattár adott elágazásába:

```azurecli-interactive
echo "Hello World!" > hello.txt
git add hello.txt
git commit -m "Testing ACR Tasks"
git push origin master
```

Előfordulhat, hogy a rendszer a GitHub hitelesítő adatok megadását kéri a `git push` parancs végrehajtásakor. Adja meg GitHub-felhasználónevét, valamint a jelszóként korábban létrehozott személyes hozzáférési jogkivonatot (PAT).

```console
$ git push origin master
Username for 'https://github.com': <github-username>
Password for 'https://githubuser@github.com': <personal-access-token>
```

Miután elküldte az adattárházba a véglegesített feladatot, az ACR által létrehozott webhook elindítja a feladatait, és elindít Azure Container Registry. Megjeleníti az aktuálisan futó feladat naplóit az összeállítási folyamat ellenőrzéséhez és monitorozásához:

```azurecli-interactive
az acr task logs --registry $ACR_NAME
```

A kimenet az alábbihoz hasonló, és az aktuálisan (vagy legutóbb) futtatott feladatot mutatja:

```console
$ az acr task logs --registry $ACR_NAME
Showing logs of the last created run.
Run ID: cf1d

[...]

Run ID: cf1d was successful after 37s
```

## <a name="list-builds"></a>Összeállítások listázása

Ha szeretné megtekinteni a feladat által futtatott ACR-feladatokat a beállításjegyzékben, futtassa az az [ACR Task List-Run][az-acr-task-list-runs] parancsot:

```azurecli-interactive
az acr task list-runs --registry $ACR_NAME --output table
```

A parancs kimenete az alábbihoz hasonlóan néz ki. Megjelennek az ACR Tasks által végrehajtott futtatások, és a „Git Commit” (Gitbeli véglegesítés) jelölés jelenik meg a legutóbbi feladat TRIGGER oszlopában:

```console
$ az acr task list-runs --registry $ACR_NAME --output table

RUN ID    TASK       PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  ---------  ----------  ---------  ---------  --------------------  ----------
cf1d      example1   linux       Succeeded  Commit     2019-05-03T04:16:44Z  00:00:37
cf1c      example1   linux       Succeeded  Commit     2019-05-03T04:16:44Z  00:00:39
cf1b      example1   linux       Succeeded  Manual     2019-05-03T03:10:30Z  00:00:31
cf1a      example1   linux       Succeeded  Commit     2019-05-03T03:09:32Z  00:00:31
cf19      example1   linux       Succeeded  Manual     2019-05-03T03:03:30Z  00:00:21
```

## <a name="create-a-multi-registry-multi-step-task"></a>Több beállításjegyzékbeli többlépéses feladat létrehozása

Az ACR-feladatok alapértelmezés szerint jogosultak a lemezképek leküldésére vagy lekérésére a feladatot futtató beállításjegyzékből. Előfordulhat, hogy olyan többlépéses feladatot szeretne futtatni, amely egy vagy több beállításjegyzéket céloz meg a futtatási beállításjegyzék mellett. Előfordulhat például, hogy egy beállításjegyzékben lemezképeket kell létrehoznia, és az éles rendszer által elérhető második beállításjegyzékben más címkékkel rendelkező képeket kell tárolnia. Ebből a példából megtudhatja, hogyan hozhat létre egy ilyen feladatot, és hogyan adhat meg hitelesítő adatokat egy másik beállításjegyzékhez.

Ha még nem rendelkezik második beállításjegyzékgel, hozzon létre egyet ehhez a példához. Ha szüksége van egy beállításjegyzékre, tekintse meg az [előző oktatóanyagot](container-registry-tutorial-quick-task.md)vagy [a gyors útmutatót: Hozzon létre egy tároló-beállításjegyzéket](container-registry-get-started-azure-cli.md)az Azure CLI használatával.

A feladat létrehozásához szüksége lesz a beállításjegyzék bejelentkezési kiszolgálójának nevére, amely az űrlap *mycontainerregistrydate.azurecr.IO* (az összes kisbetűs). Ebben a példában a második beállításjegyzéket használja az összeállítási dátummal címkézett lemezképek tárolásához.

### <a name="yaml-file"></a>YAML-fájl

Az oktatóanyaghoz tartozó második példa többlépéses feladata a fájlban `taskmulti-multiregistry.yaml`van definiálva, amely a klónozott GitHub-tárház gyökerében található:

```yml
version: v1.0.0
steps:
# Build target images
- build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} -f Dockerfile .
- build: -t {{.Values.regDate}}/hello-world:{{.Run.Date}} -f Dockerfile .
# Run image 
- cmd: -t {{.Run.Registry}}/hello-world:{{.Run.ID}}
  id: test
  detach: true
  ports: ["8080:80"]
- cmd: docker stop test
# Push images
- push:
  - {{.Run.Registry}}/hello-world:{{.Run.ID}}
  - {{.Values.regDate}}/hello-world:{{.Run.Date}}
```

Ez a többlépéses feladat a következő műveleteket végzi el:

1. A következő `build` két lépés futtatásával készít lemezképeket a munkakönyvtár Docker:
    * Az első célja `Run.Registry`a, a beállításjegyzék, amelyben a feladat fut, és az ACR-feladatok futtatási azonosítójával van megjelölve. 
    * A második az értékkel `regDate`azonosított beállításjegyzéket célozza meg, amelyet a feladat létrehozásakor, illetve az átadott `az acr task create`külső `values.yaml` fájlon keresztül adhat meg. Ez a rendszerkép a futtatási dátummal van megjelölve.
1. Futtat egy `cmd` lépést az egyik létrehozott tároló futtatásához. Ez a példa egy hosszan futó tárolót indít a háttérben, és visszaadja a tároló AZONOSÍTÓját, majd leállítja a tárolót. Egy valós forgatókönyvben a futó tároló tesztelésével ellenőrizheti, hogy megfelelően fut-e.
1. Egy `push` lépésben leküldi a létrehozott rendszerképeket, az elsőt a futtatási beállításjegyzékbe, a másodikat a által `regDate`azonosított beállításjegyzékbe.

### <a name="task-command"></a>Feladat parancs

A korábban definiált rendszerhéj-környezeti változók használatával hozza létre a feladatot a következő az [ACR Task Create][az-acr-task-create] parancs végrehajtásával. Helyettesítse be a *mycontainerregistrydate*beállításjegyzékének nevét.

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name example2 \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --branch master \
    --file taskmulti-image.yaml \
    --git-access-token $GIT_PAT \
    --set regDate=mycontainerregistrydate.azurecr.io
```

### <a name="add-task-credential"></a>Feladat hitelesítő adatainak hozzáadása

Ha a rendszerképeket az értékkel `regDate`azonosított beállításjegyzékbe szeretné leküldeni, használja az az [ACR Task hitelesítőadat Add][az-acr-task-credential-add] paranccsal, és adja hozzá a beállításjegyzékhez tartozó bejelentkezési hitelesítő adatokat a feladathoz.

Ebben a példában azt javasoljuk, hogy hozzon létre egy [egyszerű szolgáltatást](container-registry-auth-service-principal.md) a *AcrPush* szerepkörhöz tartozó beállításjegyzékhez való hozzáféréssel. Az egyszerű szolgáltatás létrehozásához tekintse meg ezt az [Azure CLI](https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-create/service-principal-create.sh)-szkriptet.

Adja át az egyszerű szolgáltatásnév alkalmazás-azonosítóját és jelszavát `az acr task credential add` a következő parancsban:

```azurecli-interactive
az acr task credential add --name example2 \
    --registry $ACR_NAME \
    --login-server mycontainerregistrydate.azurecr.io \
    --username <service-principal-application-id> \
    --password <service-principal-password>
```

A CLI a hozzáadott beállításjegyzékbeli bejelentkezési kiszolgáló nevét adja vissza.

### <a name="test-the-multi-step-workflow"></a>A többlépéses munkafolyamat tesztelése

Ahogy az előző példában is, a többlépéses feladat teszteléséhez indítsa el manuálisan az az [ACR Task Run][az-acr-task-run] parancs futtatásával. Ha a feladatot véglegesíteni szeretné a git-tárházban, tekintse meg a [Build](#trigger-a-build-with-a-commit)létrehozása a commit című szakaszt.

```azurecli-interactive
az acr task run --registry $ACR_NAME --name example2
```

Alapértelmezés szerint az `az acr task run` a naplókimenetet a konzolra streameli a parancs végrehajtásakor. Ahogy korábban is, a kimenet a feladatok lépéseinek futtatási folyamatát mutatja. A kimenet a legfontosabb lépések megjelenítéséhez van tömörítve

Kimenet:

```console
Queued a run with ID: cf1g
Waiting for an agent...
2019/05/03 04:33:39 Downloading source code...
2019/05/03 04:33:41 Finished downloading source code
2019/05/03 04:33:42 Using acb_vol_4569b017-29fe-42bd-83b2-25c45a8ac807 as the home volume
2019/05/03 04:33:42 Creating Docker network: acb_default_network, driver: 'bridge'
2019/05/03 04:33:43 Successfully set up Docker network: acb_default_network
2019/05/03 04:33:43 Setting up Docker configuration...
2019/05/03 04:33:44 Successfully set up Docker configuration
2019/05/03 04:33:44 Logging in to registry: mycontainerregistry.azurecr.io
2019/05/03 04:33:45 Successfully logged into mycontainerregistry.azurecr.io
2019/05/03 04:33:45 Logging in to registry: mycontainerregistrydate.azurecr.io
2019/05/03 04:33:47 Successfully logged into mycontainerregistrydate.azurecr.io
2019/05/03 04:33:47 Executing step ID: acb_step_0. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:33:47 Scanning for dependencies...
2019/05/03 04:33:47 Successfully scanned dependencies
2019/05/03 04:33:47 Launching container with name: acb_step_0
Sending build context to Docker daemon  25.09kB

[...]

Successfully tagged mycontainerregistry.azurecr.io/hello-world:cf1g
2019/05/03 04:33:55 Successfully executed container: acb_step_0
2019/05/03 04:33:55 Executing step ID: acb_step_1. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:33:55 Scanning for dependencies...
2019/05/03 04:33:56 Successfully scanned dependencies
2019/05/03 04:33:56 Launching container with name: acb_step_1
Sending build context to Docker daemon  25.09kB

[...]

Successfully tagged mycontainerregistrydate.azurecr.io/hello-world:20190503-043342z
2019/05/03 04:33:57 Successfully executed container: acb_step_1
2019/05/03 04:33:57 Executing step ID: acb_step_2. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:33:57 Launching container with name: acb_step_2
721437ff674051b6be63cbcd2fa8eb085eacbf38d7d632f1a079320133182101
2019/05/03 04:33:58 Successfully executed container: acb_step_2
2019/05/03 04:33:58 Executing step ID: acb_step_3. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:33:58 Launching container with name: acb_step_3
test
2019/05/03 04:34:09 Successfully executed container: acb_step_3
2019/05/03 04:34:09 Executing step ID: acb_step_4. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:34:09 Pushing image: mycontainerregistry.azurecr.io/hello-world:cf1g, attempt 1
The push refers to repository [mycontainerregistry.azurecr.io/hello-world]

[...]

2019/05/03 04:34:12 Successfully pushed image: mycontainerregistry.azurecr.io/hello-world:cf1g
2019/05/03 04:34:12 Pushing image: mycontainerregistrydate.azurecr.io/hello-world:20190503-043342z, attempt 1
The push refers to repository [mycontainerregistrydate.azurecr.io/hello-world]

[...]

2019/05/03 04:34:19 Successfully pushed image: mycontainerregistrydate.azurecr.io/hello-world:20190503-043342z
2019/05/03 04:34:19 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 8.125744)
2019/05/03 04:34:19 Populating digests for step ID: acb_step_0...
2019/05/03 04:34:21 Successfully populated digests for step ID: acb_step_0
2019/05/03 04:34:21 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 2.009281)
2019/05/03 04:34:21 Populating digests for step ID: acb_step_1...
2019/05/03 04:34:23 Successfully populated digests for step ID: acb_step_1
2019/05/03 04:34:23 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 0.795440)
2019/05/03 04:34:23 Step ID: acb_step_3 marked as successful (elapsed time in seconds: 11.446775)
2019/05/03 04:34:23 Step ID: acb_step_4 marked as successful (elapsed time in seconds: 9.734973)
2019/05/03 04:34:23 The following dependencies were found:
2019/05/03 04:34:23
- image:
    registry: mycontainerregistry.azurecr.io
    repository: hello-world
    tag: cf1g
    digest: sha256:75354e9edb995e8661438bad9913deed87a185fddd0193811f916d684b71a5d2
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 9d9023473c46a5e2c315681b11eb4552ef0faccc
- image:
    registry: mycontainerregistrydate.azurecr.io
    repository: hello-world
    tag: 20190503-043342z
    digest: sha256:75354e9edb995e8661438bad9913deed87a185fddd0193811f916d684b71a5d2
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 9d9023473c46a5e2c315681b11eb4552ef0faccc

Run ID: cf1g was successful after 46s
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan hozhat létre több lépésből álló, többtárolós alapú feladatokat, amelyek automatikusan aktiválódnak a forráskódnak a git-tárházba való beléptetése során. A többlépéses feladatok speciális funkciói, többek között a párhuzamos és a függő lépések végrehajtásával kapcsolatban lásd az [ACR-feladatok YAML](container-registry-tasks-reference-yaml.md)-referenciáját. Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan hozhat létre olyan feladatokat, amelyek a tárolórendszerképek alapként szolgáló rendszerképeinek frissítésekor aktiválnak összeállításokat.

> [!div class="nextstepaction"]
> [Összeállítások automatizálása alapként szolgáló rendszerképek frissítésekor](container-registry-tutorial-base-image-update.md)

<!-- LINKS - External -->
[sample-repo]: https://github.com/Azure-Samples/acr-build-helloworld-node

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add    
[az-login]: /cli/azure/reference-index#az-login

<!-- IMAGES -->
[build-task-01-new-token]: ./media/container-registry-tutorial-build-tasks/build-task-01-new-token.png
[build-task-02-generated-token]: ./media/container-registry-tutorial-build-tasks/build-task-02-generated-token.png
