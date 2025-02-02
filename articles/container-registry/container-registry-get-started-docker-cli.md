---
title: Docker-rendszerkép leküldése a privát Azure Container registrybe
description: Docker-rendszerképek leküldése és lekérése egy Azure-beli privát tároló beállításjegyzékébe és -jegyzékéből a Docker parancssori felületével
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 01/23/2019
ms.author: danlep
ms.custom: seodec18, H1Hack27Feb2017
ms.openlocfilehash: 6944755619ea5e8e63af04b9b3bca6f7376e29a9
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68309447"
---
# <a name="push-your-first-image-to-a-private-docker-container-registry-using-the-docker-cli"></a>Az első rendszerkép leküldése egy privát Docker-tároló beállításjegyzékébe a Docker parancssori felületével

Az Azure-beli tároló-beállításjegyzékek privát [Docker](https://hub.docker.com)-tárolórendszerképeket tárol és felügyel, hasonlóan ahhoz, ahogyan a [Docker Hub](https://hub.docker.com/) nyilvános Docker-rendszerképeket tárol. A [Docker parancssori felületét](https://docs.docker.com/engine/reference/commandline/cli/) (Docker CLI) használhatja [bejelentkezési](https://docs.docker.com/engine/reference/commandline/login/), leküldéses, [](https://docs.docker.com/engine/reference/commandline/push/)lekéréses és egyéb műveletekhez a tároló-beállításjegyzékben. [](https://docs.docker.com/engine/reference/commandline/pull/)

A következő lépésekben letölt egy hivatalos Nginx- [rendszerképet](https://store.docker.com/images/nginx) a nyilvános Docker hub-beállításjegyzékből, megcímkézi azt a privát Azure Container registryben, leküldheti a beállításjegyzékbe, majd lehívhatja a beállításjegyzékből.

## <a name="prerequisites"></a>Előfeltételek

* **Azure Container Registry** – Létrehozhat egy tároló-beállításjegyzéket Azure-előfizetésében. Használja például a [Azure Portal](container-registry-get-started-portal.md) vagy az [Azure CLI](container-registry-get-started-azure-cli.md)-t.
* **Docker CLI** – a Docker helyileg is telepítve kell lennie. A Docker olyan csomagokat biztosít, amelyekkel egyszerűen konfigurálható a Docker bármely [MacOS][docker-mac]-, [Windows][docker-windows]-vagy [Linux][docker-linux] -rendszeren.

## <a name="log-in-to-a-registry"></a>Bejelentkezés beállításjegyzékbe

A privát tároló beállításjegyzékének [több módja](container-registry-authentication.md) is van a hitelesítéshez. A parancssorban végzett munka esetén ajánlott módszer az Azure CLI-parancs az [ACR login](/cli/azure/acr?view=azure-cli-latest#az-acr-login). Például egy *myregistry*nevű beállításjegyzékbe való bejelentkezéshez:

```azurecli
az acr login --name myregistry
```

A Docker- [Bejelentkezés](https://docs.docker.com/engine/reference/commandline/login/)használatával is bejelentkezhet. Előfordulhat például, hogy egy [egyszerű szolgáltatásnevet rendelt hozzá](container-registry-authentication.md#service-principal) a beállításjegyzékhez egy automatizálási forgatókönyvhöz. A következő parancs futtatásakor interaktív módon adja meg az egyszerű szolgáltatásnév appID (username) és a jelszót, amikor a rendszer kéri. A bejelentkezési hitelesítő adatok kezelésével kapcsolatos ajánlott eljárásokért tekintse meg a [Docker login](https://docs.docker.com/engine/reference/commandline/login/) parancs referenciáját:

```
docker login myregistry.azurecr.io
```

Mindkét parancs a `Login Succeeded` befejezés után visszatér.

> [!TIP]
> Mindig a teljes beállításjegyzékbeli nevet adja meg (az összes kisbetűs) a `docker login` használatakor, és ha képeket címkéz fel a beállításjegyzékbe való leküldéshez. A cikkben szereplő példákban a teljes név *myregistry.azurecr.IO*.

## <a name="pull-the-official-nginx-image"></a>A hivatalos Nginx-rendszerkép lekérése

Először kérje le a nyilvános Nginx-rendszerképet a helyi számítógépre.

```
docker pull nginx
```

## <a name="run-the-container-locally"></a>Futtassa helyileg a tárolót

A következő [Docker](https://docs.docker.com/engine/reference/run/) -futtatási parancs végrehajtásával indítsa el az Nginx-tároló helyi példányát`-it`interaktívan () a 8080-es porton. Az `--rm` argumentum azt adja meg, hogy a tárolót el kell távolítani, amikor leállítja.

```
docker run -it --rm -p 8080:80 nginx
```

`http://localhost:8080` Tallózással megtekintheti az Nginx által a futó tárolóban kiszolgált alapértelmezett weblapot. A következőhöz hasonló oldalnak kell megjelennie:

![Nginx egy helyi számítógépen](./media/container-registry-get-started-docker-cli/nginx.png)

Mivel a tárolót interaktív módon `-it`indította el, az Nginx-kiszolgáló kimenetét a parancssorban láthatja a böngészőben való navigálás után.

A tároló leállításához és eltávolításához `Control`nyomja meg a gombot + `C`.

## <a name="create-an-alias-of-the-image"></a>A rendszerkép aliasának létrehozása

A [Docker címke](https://docs.docker.com/engine/reference/commandline/tag/) használatával hozza létre a rendszerkép aliasát a beállításjegyzék teljes elérési útjával. A példa a(z) `samples` névteret határozza meg, hogy ne legyen zsúfolt a beállításjegyzék gyökere.

```
docker tag nginx myregistry.azurecr.io/samples/nginx
```

A névterekkel való címkézéssel kapcsolatos további információkért tekintse [meg a Azure Container Registry ajánlott eljárásainak](container-registry-best-practices.md) [tárházbeli névterek](container-registry-best-practices.md#repository-namespaces) című szakaszát.

## <a name="push-the-image-to-your-registry"></a>A rendszerkép leküldése a beállításjegyzékbe

Most, hogy megcímkézte a rendszerképet a saját beállításjegyzékének teljes elérési útjával, leküldheti a beállításjegyzékbe a [Docker push](https://docs.docker.com/engine/reference/commandline/push/)használatával:

```
docker push myregistry.azurecr.io/samples/nginx
```

## <a name="pull-the-image-from-your-registry"></a>A rendszerkép lekérése a beállításjegyzékből

A [Docker pull](https://docs.docker.com/engine/reference/commandline/pull/) paranccsal lekérheti a rendszerképet a beállításjegyzékből:

```
docker pull myregistry.azurecr.io/samples/nginx
```

## <a name="start-the-nginx-container"></a>Az Nginx-tároló elindítása

A beállításjegyzékből lekért rendszerkép futtatásához használja a [Docker Run](https://docs.docker.com/engine/reference/run/) parancsot:

```
docker run -it --rm -p 8080:80 myregistry.azurecr.io/samples/nginx
```

`http://localhost:8080` Tallózással keresse meg a futó tárolót.

A tároló leállításához és eltávolításához `Control`nyomja meg a gombot + `C`.

## <a name="remove-the-image-optional"></a>A rendszerkép eltávolítása (nem kötelező)

Ha már nincs szüksége az Nginx-rendszerképre, helyileg törölheti a Docker [RMI](https://docs.docker.com/engine/reference/commandline/rmi/) paranccsal.

```
docker rmi myregistry.azurecr.io/samples/nginx
```

Az Azure Container registryből származó rendszerképek eltávolításához használhatja az Azure CLI-parancsot az [ACR adattár delete](/cli/azure/acr/repository#az-acr-repository-delete)paranccsal. A következő parancs például törli a `samples/nginx:latest` címke által hivatkozott jegyzékfájlt, az egyedi réteget, valamint az összes többi, a jegyzékfájlra hivatkozó címkét.

```azurecli
az acr repository delete --name myregistry --image samples/nginx:latest
```

## <a name="next-steps"></a>További lépések

Most, hogy megismerte az alapokat, készen áll a beállításjegyzék használatának megkezdésére! Például telepítsen lemezképeket a beállításjegyzékből a következőre:

* [Az Azure Kubernetes Service (AKS)](../aks/tutorial-kubernetes-prepare-app.md)
* [Azure Container Instances](../container-instances/container-instances-tutorial-prepare-app.md)
* [Service Fabric](../service-fabric/service-fabric-tutorial-create-container-images.md)

Telepítse a Docker-bővítményt a [Visual Studio Code](https://code.visualstudio.com/docs/azure/docker) -hoz, és az [Azure-fiók](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) bővítményét az Azure-beli tároló-beállításjegyzékkel való együttműködéshez. Lemezképek lekérése és leküldése egy Azure Container registrybe, vagy az ACR-feladatok futtatása a Visual Studio Code-ban.


<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/
