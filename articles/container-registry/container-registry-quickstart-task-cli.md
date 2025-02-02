---
title: Rövid útmutató – tároló rendszerképének létrehozása és futtatása Azure Container Registry
description: Gyorsan futtathat feladatokat a Azure Container Registry használatával, és igény szerint, a felhőben készíthet és futtathat egy tároló-rendszerképet.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: quickstart
ms.date: 04/02/2019
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: e5e02d8194f9164a03bb27d932df45d91486c518
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68310635"
---
# <a name="quickstart-build-and-run-a-container-image-using-azure-container-registry-tasks"></a>Gyors útmutató: Tároló-rendszerkép létrehozása és futtatása Azure Container Registry feladatok használatával

Ebben a rövid útmutatóban Azure Container Registry feladatok parancsokkal gyorsan hozhat létre, küldhet le és futtathat egy Docker-tároló rendszerképet az Azure-ban, amely bemutatja, hogyan teheti ki a "belső hurkos" fejlesztési ciklust a felhőbe. Az [ACR-feladatok][container-registry-tasks-overview] a Azure Container Registryon belüli szolgáltatások egyik csomagja, amely segít a tároló-rendszerképek felügyeletében és módosításához a tárolók életciklusa során. 

Ebből a rövid útmutatóból megismerheti az ACR-feladatok fejlettebb funkcióit. Az ACR-feladatok a programkódok véglegesítve vagy az alapképek frissítései alapján automatizálják a képbuildeket, vagy több tárolót tesztelnek párhuzamosan más forgatókönyvek között. 

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot][azure-account] a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

A rövid útmutató teljesítéséhez használhatja az Azure Cloud Shellt vagy az Azure CLI helyileg telepített példányát. Ha helyileg szeretné használni, a 2.0.58 vagy újabb verzió használata javasolt. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Ha még nem rendelkezik tároló-beállításjegyzékkel, először hozzon létre egy erőforráscsoportot az az [Group Create][az-group-create] paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>Tároló-beállításjegyzék létrehozása

Hozzon létre egy tároló-beállításjegyzéket az az [ACR Create][az-acr-create] paranccsal. A beállításjegyzék nevének egyedinek kell lennie az Azure rendszerben, és 5–50 alfanumerikus karaktert kell tartalmaznia. A következő példában a rendszer a *myContainerRegistry008* használja. Ezt cserélje le egy egyedi értékre.

```azurecli-interactive
az acr create --resource-group myResourceGroup --name myContainerRegistry008 --sku Basic
```

Ez a példa egy  alapszintű beállításjegyzéket hoz létre, amely egy költséghatékony megoldás a fejlesztők számára a Azure Container Registry megismeréséhez. A rendelkezésre álló szolgáltatási szintek részletes ismertetését lásd: [Container Registry SKU][container-registry-skus]-i.

## <a name="build-an-image-from-a-dockerfile"></a>Rendszerkép létrehozása Docker

Hozzon létre egy rendszerképet a Azure Container Registry használatával. Először hozzon létre egy munkakönyvtárat, majd hozzon létre egy *Docker* nevű Docker az alábbi tartalommal. Ez egy egyszerű példa arra, hogy Linux-tárolót hozzon létre, de létrehozhatja a saját standard Docker, és képeket készíthet más platformokhoz.

```bash
echo FROM hello-world > Dockerfile
```

A rendszerkép létrehozásához futtassa az az [ACR Build][az-acr-build] parancsot. A létrehozást követően a rendszer leküldi a rendszerképet a beállításjegyzékbe. A következő példa leküldi `sample/hello-world:v1` a rendszerképet. `.` A parancs végén adja meg a Docker helyét, ebben az esetben az aktuális könyvtárat.

```azurecli-interactive
az acr build --image sample/hello-world:v1 --registry myContainerRegistry008 --file Dockerfile . 
```

A sikeres buildek és leküldések kimenete a következőhöz hasonló:

```console
Packing source code into tar to upload...
Uploading archived source code from '/tmp/build_archive_b0bc1e5d361b44f0833xxxx41b78c24e.tar.gz'...
Sending context (1.856 KiB) to registry: mycontainerregistry008...
Queued a build with ID: ca8
Waiting for agent...
2019/03/18 21:56:57 Using acb_vol_4c7ffa31-c862-4be3-xxxx-ab8e615c55c4 as the home volume
2019/03/18 21:56:57 Setting up Docker configuration...
2019/03/18 21:56:58 Successfully set up Docker configuration
2019/03/18 21:56:58 Logging in to registry: mycontainerregistry008.azurecr.io
2019/03/18 21:56:59 Successfully logged into mycontainerregistry008.azurecr.io
2019/03/18 21:56:59 Executing step ID: build. Working directory: '', Network: ''
2019/03/18 21:56:59 Obtaining source code and scanning for dependencies...
2019/03/18 21:57:00 Successfully obtained source code and scanned for dependencies
2019/03/18 21:57:00 Launching container with name: build
Sending build context to Docker daemon  13.82kB
Step 1/1 : FROM hello-world
latest: Pulling from library/hello-world
Digest: sha256:2557e3c07ed1e38f26e389462d03ed943586fxxxx21577a99efb77324b0fe535
Successfully built fce289e99eb9
Successfully tagged mycontainerregistry008.azurecr.io/sample/hello-world:v1
2019/03/18 21:57:01 Successfully executed container: build
2019/03/18 21:57:01 Executing step ID: push. Working directory: '', Network: ''
2019/03/18 21:57:01 Pushing image: mycontainerregistry008.azurecr.io/sample/hello-world:v1, attempt 1
The push refers to repository [mycontainerregistry008.azurecr.io/sample/hello-world]
af0b15c8625b: Preparing
af0b15c8625b: Layer already exists
v1: digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a size: 524
2019/03/18 21:57:03 Successfully pushed image: mycontainerregistry008.azurecr.io/sample/hello-world:v1
2019/03/18 21:57:03 Step ID: build marked as successful (elapsed time in seconds: 2.543040)
2019/03/18 21:57:03 Populating digests for step ID: build...
2019/03/18 21:57:05 Successfully populated digests for step ID: build
2019/03/18 21:57:05 Step ID: push marked as successful (elapsed time in seconds: 1.473581)
2019/03/18 21:57:05 The following dependencies were found:
2019/03/18 21:57:05
- image:
    registry: mycontainerregistry008.azurecr.io
    repository: sample/hello-world
    tag: v1
    digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: v1
    digest: sha256:2557e3c07ed1e38f26e389462d03ed943586f744621577a99efb77324b0fe535
  git: {}

Run ID: ca8 was successful after 10s
```

## <a name="run-the-image"></a>A rendszerkép futtatása

Most gyorsan futtathatja a létrehozott és a beállításjegyzékbe leküldett képet. A tároló-fejlesztési munkafolyamatban ez lehet egy érvényesítési lépés a lemezkép telepítése előtt.

Hozzon létre egy *quickrun. YAML* fájlt egy helyi munkakönyvtárban az alábbi tartalommal egyetlen lépéshez. Helyettesítse be a  *\<acrLoginServer\>* beállításjegyzékének bejelentkezési kiszolgálójának nevét. A bejelentkezési kiszolgáló nevét a következő formátumban   *\<kell megadni: Registry-name\>. azurecr.IO* (mind kisbetűs), például mycontainerregistry008.azurecr.IO. Ez a példa feltételezi, hogy létrehozta és leküldte a `sample/hello-world:v1` rendszerképet az előző szakaszban:

```yml
steps:
  - cmd: <acrLoginServer>/sample/hello-world:v1
```

A `cmd` példában szereplő lépés az alapértelmezett konfigurációban futtatja a tárolót, `cmd` de támogatja `docker run` a további paramétereket, `docker` vagy akár más parancsokat is.

Futtassa a tárolót a következő paranccsal:

```azurecli-interactive
az acr run --registry myContainerRegistry008 --file quickrun.yaml .
```

A kimenet a következőkhöz hasonló:

```console
Packing source code into tar to upload...
Uploading archived source code from '/tmp/run_archive_ebf74da7fcb04683867b129e2ccad5e1.tar.gz'...
Sending context (1.855 KiB) to registry: mycontainerre...
Queued a run with ID: cab
Waiting for an agent...
2019/03/19 19:01:53 Using acb_vol_60e9a538-b466-475f-9565-80c5b93eaa15 as the home volume
2019/03/19 19:01:53 Creating Docker network: acb_default_network, driver: 'bridge'
2019/03/19 19:01:53 Successfully set up Docker network: acb_default_network
2019/03/19 19:01:53 Setting up Docker configuration...
2019/03/19 19:01:54 Successfully set up Docker configuration
2019/03/19 19:01:54 Logging in to registry: mycontainerregistry008.azurecr.io
2019/03/19 19:01:55 Successfully logged into mycontainerregistry008.azurecr.io
2019/03/19 19:01:55 Executing step ID: acb_step_0. Working directory: '', Network: 'acb_default_network'
2019/03/19 19:01:55 Launching container with name: acb_step_0

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/

2019/03/19 19:01:56 Successfully executed container: acb_step_0
2019/03/19 19:01:56 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 0.843801)

Run ID: cab was successful after 6s
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az az [Group delete][az-group-delete] paranccsal eltávolítható az erőforráscsoport, a tároló-beállításjegyzék és az ott tárolt tároló-lemezképek.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban az ACR-feladatok funkcióit használta a Docker-tárolók gyors létrehozására, leküldésére és futtatására az Azure-ban. Folytassa a Azure Container Registry oktatóanyagokkal, és ismerkedjen meg az ACR-feladatok a rendszerkép-buildek és-frissítések automatizálására való használatával.

> [!div class="nextstepaction"]
> [Oktatóanyagok Azure Container Registry][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/
[azure-account]: https://azure.microsoft.com/free/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-tasks-overview]: container-registry-tasks-overview.md
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md
[azure-cli-install]: /cli/azure/install-azure-cli
