---
title: Az Azure dev Spaces használata a Windows-tárolók használatához
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/25/2019
ms.topic: conceptual
description: Ismerje meg, hogyan futtathat Azure dev-helyeket meglévő fürtön Windows-tárolókkal
keywords: Azure dev Spaces, dev Spaces, Docker, Kubernetes, Azure, AK, Azure Kubernetes szolgáltatás, tárolók, Windows-tárolók
ms.openlocfilehash: 2110636b331f0cf4e74c77f41726ead5bf80a64f
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2019
ms.locfileid: "68501519"
---
# <a name="use-azure-dev-spaces-to-interact-with-windows-containers"></a>Az Azure dev Spaces használata a Windows-tárolók használatához

Az Azure dev Spaces szolgáltatást az új és a meglévő Kubernetes-névtereken is engedélyezheti. Az Azure dev Spaces szolgáltatás a Linux-tárolókban futó eszközökön fut. Ezek a szolgáltatások a Windows-tárolókban futó alkalmazásokat is kezelhetik ugyanabban a névtérben. Ebből a cikkből megtudhatja, hogyan használhatja a dev Spaces szolgáltatást a meglévő Windows-tárolókkal rendelkező névtérben lévő szolgáltatások futtatására.

## <a name="set-up-your-cluster"></a>A fürt beállítása

Ez a cikk azt feltételezi, hogy már rendelkezik egy, a Linux-és Windows-csomópont-készlettel rendelkező fürttel. Ha Linux-és Windows-csomópont-készletekkel rendelkező fürtöt kell létrehoznia, kövesse az [itt][windows-container-cli]található utasításokat.

Kapcsolódjon a fürthöz a [kubectl][kubectl]és a Kubernetes parancssori ügyfél használatával. A Kubernetes `kubectl` -fürthöz való kapcsolódás konfigurálásához használja az az az [AK Get-hitelesítőadats][az-aks-get-credentials] parancsot. Ez a parancs letölti a hitelesítő adatokat, és konfigurálja a Kubernetes CLI-t a használatára.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

A fürthöz való csatlakozás ellenőrzéséhez használja a [kubectl get][kubectl-get] parancsot a fürtcsomópontok listájának lekéréséhez.

```azurecli-interactive
kubectl get nodes
```

A következő példában a kimenet egy Windows-és Linux-csomóponttal rendelkező fürtöt mutat be. A folytatás előtt győződjön meg arról, hogy az állapot *készen áll* az egyes csomópontokra.

```console
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.14.1
aksnpwin987654                      Ready    agent   108s   v1.14.1
```

Alkalmazzon [][using-taints] egy Taint a Windows-csomópontjaira. A Windows-csomópontok megromlása megakadályozza, hogy a fejlesztői helyek a Linux-tárolókat ütemezve futtassák a Windows-csomópontokon. Az alábbi parancs-példában szereplő parancs egy szennyező elemet alkalmaz a *aksnpwin987654* Windows-csomópontra az előző példából.

```azurecli-interactive
kubectl taint node aksnpwin987654 sku=win-node:NoSchedule
```

## <a name="run-your-windows-service"></a>Windows-szolgáltatás futtatása

Futtassa a Windows-szolgáltatást az AK-fürtön, és ellenőrizze, hogy *futó* állapotban van-e. Ez a cikk egy [minta alkalmazást][sample-application] használ a fürtön futó Windows-és Linux-szolgáltatások bemutatására.

A minta alkalmazás klónozása a githubról, majd `dev-spaces/samples/existingWindowsBackend/mywebapi-windows` a címtárba való belépés:

```console
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/existingWindowsBackend/mywebapi-windows
```

A minta alkalmazás [Helm][helm-installed] használatával futtatja a Windows-szolgáltatást a fürtön. Telepítse a sisakot a fürtön, és adja meg a megfelelő engedélyeket:

```console
helm init --wait
kubectl create serviceaccount --namespace kube-system tiller
kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller
kubectl patch deploy --namespace kube-system tiller-deploy -p '{"spec":{"template":{"spec":{"serviceAccount":"tiller"}}}}'
``` 

Navigáljon a `charts` címtárhoz, és futtassa a Windows-szolgáltatást:

```console
cd charts/
helm install . --namespace dev
```

A fenti parancs a Helm használatával futtatja a Windows-szolgáltatást a *fejlesztői* névtérben. Ha nem rendelkezik *dev*nevű névtérrel, a rendszer létrehozza.

A `kubectl get pods` parancs használatával ellenőrizheti, hogy a Windows-szolgáltatás fut-e a fürtben. 

```console
$ kubectl get pods --namespace dev --watch
NAME                     READY   STATUS              RESTARTS   AGE
myapi-4b9667d123-1a2b3   0/1     ContainerCreating   0          47s
...
myapi-4b9667d123-1a2b3   1/1     Running             0          98s
```

## <a name="enable-azure-dev-spaces"></a>Az Azure dev Spaces használatának engedélyezése

Engedélyezze a fejlesztői helyeket a Windows-szolgáltatás futtatásához használt névtérben. A következő parancs engedélyezi a dev Spaces használatát a *fejlesztői* névtérben:

```console
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster --space dev --yes
```

## <a name="update-your-windows-service-for-dev-spaces"></a>Windows-szolgáltatás frissítése a fejlesztői tárhelyekhez

Ha egy már futó tárolóval rendelkező meglévő névtérben engedélyezi a dev Spaces szolgáltatást, a dev Spaces szolgáltatás alapértelmezés szerint megkísérli a névtérben futó új tárolók kiválasztását. A dev Spaces emellett a névtérben már futó, szolgáltatáshoz létrehozott új tárolókat is megpróbálja kipróbálni. Ha meg szeretné akadályozni, hogy a fejlesztői területek a névtérben futó tárolót használjanak, adja hozzá a `deployment.yaml` *No-proxy* fejlécet a következőhöz:.

Hozzáadás `azds.io/no-proxy: "true"` a`existingWindowsBackend/mywebapi-windows/charts/templates/deployment.yaml` fájlhoz:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  ...
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    ...
  template:
    metadata:
      labels:
        app.kubernetes.io/name: {{ include "mywebapi.name" . }}
        app.kubernetes.io/instance: {{ .Release.Name }}
        azds.io/no-proxy: "true"
```

A `helm list` használatával listázhatja a Windows-szolgáltatás központi telepítését:

```cmd
$ helm list
NAME            REVISION    UPDATED                     STATUS      CHART           APP VERSION NAMESPACE
gilded-jackal   1           Wed Jul 24 15:45:59 2019    DEPLOYED    mywebapi-0.1.0  1.0         dev  
```

A fenti példában az üzembe helyezés neve *aranyozott-Sakál*. Frissítse Windows-szolgáltatását az új konfigurációval `helm upgrade`a használatával:

```cmd
$ helm upgrade gilded-jackal . --namespace dev
Release "gilded-jackal" has been upgraded.
```

A `deployment.yaml`frissítése óta a fejlesztői tárhelyek nem fogják kipróbálni és kiépíteni a szolgáltatást.

## <a name="run-your-linux-application-with-azure-dev-spaces"></a>Linux-alkalmazás futtatása az Azure dev Spaces-szel

Navigáljon a `webfrontend` címtárhoz, és `azds prep` a `azds up` és a parancsok használatával futtassa a Linux-alkalmazást a fürtön.

```console
cd ../../webfrontend-linux/
azds prep --public
azds up
```

A `azds prep --public` parancs létrehozza a Helm-diagramot és a Dockerfiles az alkalmazáshoz. A `azds up` parancs futtatja a szolgáltatást a névtérben.

```console
$ azds up
Using dev space 'dev' with target 'myAKSCluster'
Synchronizing files...4s
Installing Helm chart...11s
Waiting for container image build...6s
Building container image...
Step 1/12 : FROM mcr.microsoft.com/dotnet/core/sdk:2.2
...
Step 12/12 : ENTRYPOINT ["/bin/bash", "/entrypoint.sh"]
Built container image in 36s
Waiting for container...2s
Service 'webfrontend' port 'http' is available at http://dev.webfrontend.abcdef0123.eus.azds.io/
Service 'webfrontend' port 80 (http) is available via port forwarding at http://localhost:57648
```

A szolgáltatás futtatásához nyissa meg a nyilvános URL-címet, amely a azds up parancs kimenetében jelenik meg. Ebben a példában a nyilvános URL-cím `http://dev.webfrontend.abcdef0123.eus.azds.io/`a következő:. Navigáljon a szolgáltatáshoz egy böngészőben, és kattintson a felül található *Névjegy* elemre. Ellenőrizze, hogy megjelenik-e a *mywebapi* szolgáltatás azon üzenete, amely a tároló által használt Windows-verziót tartalmazza.

![Minta alkalmazás, amely a Windows-verziót mutatja a mywebapi](../media/run-dev-spaces-windows-containers/sample-app.png)

## <a name="next-steps"></a>További lépések

Ismerje meg, hogy az Azure dev Spaces hogyan segíti az összetettebb alkalmazások fejlesztését több tárolóban, és hogyan egyszerűsítheti az együttműködésen alapuló fejlesztést, ha a kód különböző verzióival vagy ágaival dolgozik a különböző helyeken.

> [!div class="nextstepaction"]
> [Csoportmunka az Azure fejlesztői Spaces szolgáltatásban][team-development-qs]

[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-installed]: https://github.com/helm/helm/blob/master/docs/install.md
[sample-application]: https://github.com/Azure/dev-spaces/tree/master/samples/existingWindowsBackend
[team-development-qs]: ../quickstart-team-development.md

[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[team-development]: ../team-development-netcore.md
[using-taints]: ../../aks/use-multiple-node-pools.md#schedule-pods-using-taints-and-tolerations
[windows-container-cli]: ../../aks/windows-container-cli.md
