---
title: Oktatóanyag – a Azure Container Instances tároló rendszerképének előkészítése
description: Azure Container Instances oktatóanyag 1. rész – az alkalmazás előkészítése egy tároló-rendszerképbe az üzembe helyezéshez Azure Container Instances
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: tutorial
ms.date: 03/21/2018
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 719237f63d387cf56ab7947f8f168e0aa4351376
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68325569"
---
# <a name="tutorial-create-a-container-image-for-deployment-to-azure-container-instances"></a>Oktatóanyag: Tároló rendszerképének létrehozása a Azure Container Instances való üzembe helyezéshez

Az Azure Container Instances lehetővé teszi Docker-tárolók üzembe helyezését az Azure-infrastruktúrában anélkül, hogy ehhez virtuális gépeket kellene üzembe helyeznie vagy magasabb szintű szolgáltatást kellene alkalmaznia. Ebben az oktatóanyagban egy kisméretű Node.js-webalkalmazást csomagolunk egy, az Azure Container Instances használatával futtatható tárolórendszerképbe.

A cikk, amely a sorozat első része, a következő lépésekből áll:

> [!div class="checklist"]
> * Alkalmazás forráskódjának klónozása a GitHubról
> * Tárolórendszerkép létrehozása az alkalmazás forrásából
> * A rendszerkép tesztelése helyi Docker-környezetben

Az oktatóanyag második és harmadik részében feltöltjük a rendszerképet az Azure Container Registrybe, majd üzembe helyezzük az Azure Container Instancesben.

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [container-instances-tutorial-prerequisites](../../includes/container-instances-tutorial-prerequisites.md)]

## <a name="get-application-code"></a>Az alkalmazáskód letöltése

Az oktatóanyagban szereplő minta alkalmazás egy [Node. js][nodejs]-ben létrehozott egyszerű webalkalmazás. Az alkalmazás egy statikus HTML-oldalt szolgál ki, és a következő képernyőképen láthatóakhoz hasonló:

![Az oktatóanyag alkalmazása böngészőben megjelenítve][aci-tutorial-app]

A Git használatával klónozza a mintaalkalmazás adattárát:

```bash
git clone https://github.com/Azure-Samples/aci-helloworld.git
```

Közvetlenül a GitHubról is [letöltheti a zip][aci-helloworld-zip] -archívumot.

## <a name="build-the-container-image"></a>Építse fel a tárolórendszerképet

A mintaalkalmazásban szereplő Dockerfile bemutatja a tároló összeállításának menetét. Ez egy, az [alpesi Linuxon][alpine-linux]alapuló, [hivatalos Node. js][docker-hub-nodeimage] -rendszerképből indul, amely egy kis eloszlás, amely jól használható a tárolókkal való használatra. Ezután bemásolja az alkalmazásfájlokat a tárolóba, telepíti a függőségeket a Node Package Managerrel, végül pedig elindítja az alkalmazást.

```Dockerfile
FROM node:8.9.3-alpine
RUN mkdir -p /usr/src/app
COPY ./app/ /usr/src/app/
WORKDIR /usr/src/app
RUN npm install
CMD node /usr/src/app/index.js
```

A [Docker Build][docker-build] parancs használatával hozza létre a tároló rendszerképét, és címkézze fel *ACI-tutorial-app*néven:

```bash
docker build ./aci-helloworld -t aci-tutorial-app
```

A Docker [Build][docker-build] parancs kimenete az alábbihoz hasonló (az olvashatóság érdekében csonkítva):

```console
$ docker build ./aci-helloworld -t aci-tutorial-app
Sending build context to Docker daemon  119.3kB
Step 1/6 : FROM node:8.9.3-alpine
8.9.3-alpine: Pulling from library/node
88286f41530e: Pull complete
84f3a4bf8410: Pull complete
d0d9b2214720: Pull complete
Digest: sha256:c73277ccc763752b42bb2400d1aaecb4e3d32e3a9dbedd0e49885c71bea07354
Status: Downloaded newer image for node:8.9.3-alpine
 ---> 90f5ee24bee2
...
Step 6/6 : CMD node /usr/src/app/index.js
 ---> Running in f4a1ea099eec
 ---> 6edad76d09e9
Removing intermediate container f4a1ea099eec
Successfully built 6edad76d09e9
Successfully tagged aci-tutorial-app:latest
```

A beépített [][docker-images] lemezkép megjelenítéséhez használja a Docker images parancsot:

```bash
docker images
```

Az újonnan létrehozott rendszerképnek meg kell jelennie a listán:

```console
$ docker images
REPOSITORY          TAG       IMAGE ID        CREATED           SIZE
aci-tutorial-app    latest    5c745774dfa9    39 seconds ago    68.1 MB
```

## <a name="run-the-container-locally"></a>Futtassa helyileg a tárolót

Mielőtt üzembe helyezi a tárolót Azure Container Instancesre [][docker-run] , a Docker futtatásával helyileg futtathatja és ellenőrizheti, hogy működik-e. A `-d` kapcsolóval a tároló a háttérben működtethető, míg a `-p` kapcsolóval leképezheti a számítógép egy tetszőleges portját a tároló 80-as portjára.

```bash
docker run -d -p 8080:80 aci-tutorial-app
```

Ha a parancs sikeres volt, a `docker run` parancs kimenete megjeleníti a futó tároló azonosítóját:

```console
$ docker run -d -p 8080:80 aci-tutorial-app
a2e3e4435db58ab0c664ce521854c2e1a1bda88c9cf2fcff46aedf48df86cccf
```

Ezután lépjen a `http://localhost:8080` címre a böngészőben, hogy ellenőrizze, a tároló fut-e. A következőhöz hasonló webhely jelenik meg:

![Az alkalmazás helyileg történő futtatása a böngészőben][aci-tutorial-app-local]

## <a name="next-steps"></a>További lépések

Az oktatóanyagban egy, az Azure Container Instancesben üzembe helyezhető tárolórendszerképet hozott létre, és ellenőrizte, hogy helyben fut-e. Eddig a következőket hajtotta végre:

> [!div class="checklist"]
> * Az alkalmazás forrásának klónozása a GitHubról
> * Tárolórendszerkép létrehozása az alkalmazás forrásából
> * A tároló helyi tesztelése

Folytassa a sorozat következő oktatóanyagával, amelyben a tárolórendszerkép az Azure Container Registryben való tárolását ismerheti meg:

> [!div class="nextstepaction"]
> [Rendszerkép leküldése az Azure Container Registrybe](container-instances-tutorial-prepare-acr.md)

<!--- IMAGES --->
[aci-tutorial-app]:./media/container-instances-quickstart/aci-app-browser.png
[aci-tutorial-app-local]: ./media/container-instances-tutorial-prepare-app/aci-app-browser-local.png

<!-- LINKS - External -->
[aci-helloworld-zip]: https://github.com/Azure-Samples/aci-helloworld/archive/master.zip
[alpine-linux]: https://alpinelinux.org/
[docker-build]: https://docs.docker.com/engine/reference/commandline/build/
[docker-get-started]: https://docs.docker.com/get-started/
[docker-hub-nodeimage]: https://store.docker.com/images/node
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/
[nodejs]: https://nodejs.org

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
