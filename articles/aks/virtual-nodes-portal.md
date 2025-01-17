---
title: Virtuális csomópontok létrehozása a portál használatával az Azure Kubernetes Servicesben (ak)
description: Megtudhatja, hogyan használhatja a Azure Portalt olyan Azure Kubernetes Services-(ak-) fürt létrehozásához, amely virtuális csomópontokat használ a hüvelyek futtatásához.
services: container-service
author: mlearned
ms.topic: conceptual
ms.service: container-service
ms.date: 05/06/2019
ms.author: mlearned
ms.openlocfilehash: 8752d888e24e7135d488be6d1b377070a30fe4eb
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/12/2019
ms.locfileid: "67613843"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-in-the-azure-portal"></a>Azure Kubernetes Services (ak) fürt létrehozása és konfigurálása virtuális csomópontok használatára a Azure Portalban

A számítási feladatok gyors üzembe helyezéséhez egy Azure Kubernetes-szolgáltatásbeli (ak-beli) fürtben virtuális csomópontokat használhat. A virtuális csomópontokkal gyorsan kiépítheti a hüvelyeket, és a végrehajtásuk ideje alatt csak másodpercenként kell fizetnie. Skálázási helyzetben nem kell megvárnia, hogy a Kubernetes-fürt automatikus méretezése virtuálisgép-számítási csomópontokat helyezzen üzembe a további hüvelyek futtatásához. A virtuális csomópontok csak Linux-hüvelyek és-csomópontok esetén támogatottak.

Ebből a cikkből megtudhatja, hogyan hozhat létre és konfigurálhat virtuális hálózati erőforrásokat és egy AK-fürtöt az engedélyezett virtuális csomópontokkal.

## <a name="before-you-begin"></a>Előkészületek

A virtuális csomópontok lehetővé teszik a hálózati kommunikációt az ACI-ban és az AK-fürtben futó hüvelyek között. A kommunikáció biztosításához létre kell hozni egy virtuális hálózati alhálózatot, és hozzá kell rendelni a delegált engedélyeket. A virtuális csomópontok csak a *speciális* hálózatkezelés használatával létrehozott AK-fürtökkel működnek. Alapértelmezés szerint az AK-fürtök alapszintű hálózatkezeléssel jönnek létre. Ebből a cikkből megtudhatja, hogyan hozhat létre virtuális hálózatot és alhálózatokat, majd helyezzen üzembe egy speciális hálózatkezelést használó AK-fürtöt.

Ha korábban még nem használta az ACI-t, regisztrálja a szolgáltatót az előfizetésében. Az ACI-szolgáltató regisztrációjának állapotát az az [Provider List][az-provider-list] parancs használatával tekintheti meg, ahogy az az alábbi példában is látható:

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

A *Microsoft. ContainerInstance* szolgáltatónak regisztráltkéntkell jelentenie, ahogy az alábbi példában is látható:

```
Namespace                    RegistrationState
---------------------------  -------------------
Microsoft.ContainerInstance  Registered
```

Ha a szolgáltató *NotRegistered*-ként jelenik meg, regisztrálja a szolgáltatót az [az Provider Register] [az-Provider-Register] paranccsal az alábbi példában látható módon:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

## <a name="regional-availability"></a>Régiónkénti rendelkezésre állás

A virtuális csomópontok központi telepítése a következő régiókat támogatja:

* Kelet-Ausztrália (australiaeast)
* USA középső régiója (CentralUS)
* USA keleti régiója (eastus)
* USA 2. keleti régiója (eastus2)
* Kelet-Japán (japaneast)
* Észak-Európa (northeurope)
* Délkelet-Ázsia (southeastasia)
* USA nyugati középső régiója (westcentralus)
* Nyugat-Európa (westeurope)
* USA nyugati régiója (westus)
* USA 2. nyugati régiója (westus2)

## <a name="known-limitations"></a>Ismert korlátozások
A virtuális csomópontok funkciói nagy mértékben függenek az ACI funkciójának. A virtuális csomópontok esetében a következő forgatókönyvek még nem támogatottak

* Egyszerű szolgáltatásnév használata az ACR-lemezképek lekéréséhez. [Megkerülő megoldás](https://github.com/virtual-kubelet/virtual-kubelet/blob/master/providers/azure/README.md#Private-registry) a [Kubernetes-titkok](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-line) használata
* [Virtual Network korlátozásokat](../container-instances/container-instances-vnet.md) , beleértve a VNet-társítást, a Kubernetes hálózati házirendeket és az internetre irányuló kimenő adatforgalmat hálózati biztonsági csoportokkal.
* Init-tárolók
* [Gazdagép-aliasok](https://kubernetes.io/docs/concepts/services-networking/add-entries-to-pod-etc-hosts-with-host-aliases/)
* [](../container-instances/container-instances-exec.md#restrictions) A exec argumentumai az ACI-ban
* A [Daemonsets](concepts-clusters-workloads.md#statefulsets-and-daemonsets) nem helyezi üzembe a hüvelyeket a virtuális csomóponton.
* A virtuális csomópontok mellett a [Windows Server-csomópontok (jelenleg előzetes](windows-container-cli.md) verzióban) nem támogatottak. A virtuális csomópontok segítségével ütemezhet Windows Server-tárolókat anélkül, hogy szükség lenne a Windows Server-csomópontokra egy AK-fürtben.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="create-an-aks-cluster"></a>AKS-fürt létrehozása

Az Azure Portal bal felső sarkában válassza az **Erőforrás létrehozása** > **Kubernetes Service** lehetőséget.

Az **alapvető** beállítások lapon adja meg a következő beállításokat:

- *PROJEKT RÉSZLETEI*: Válasszon ki egy Azure-előfizetést, majd válasszon ki vagy hozzon létre egy Azure-erőforráscsoportot, például *myResourceGroup*. Adja meg a **Kubernetes-fürt nevét**, például *myAKSCluster*.
- *FÜRT RÉSZLETEI*: Válassza ki a régiót, a Kubernetes-verziót és a DNS-név előtagját az AK-fürthöz.
- *ELSŐDLEGES CSOMÓPONT-KÉSZLET*: Válasszon VM-méretet az AK-csomópontok számára. A virtuálisgép-méret az AKS-fürt telepítését követően **nem** módosítható.
     - Adja meg a fürtre telepítendő csomópontok számát. Ehhez a cikkhez állítsa a **csomópontok száma** *1*értékre. A csomópontok száma a fürt telepítése után is **módosítható**.

Kattintson **a Tovább gombra: Méretezés**.

A **skála** lapon válassza az *engedélyezve* lehetőséget a **virtuális csomópontok**alatt.

![AK-fürt létrehozása és a virtuális csomópontok engedélyezése](media/virtual-nodes-portal/enable-virtual-nodes.png)

Alapértelmezés szerint létrejön egy Azure Active Directory egyszerű szolgáltatásnév. Ez az egyszerű szolgáltatás a fürtök közötti kommunikációhoz és más Azure-szolgáltatásokkal való integrációhoz használatos.

A fürt speciális hálózatkezelésre is konfigurálva van. A virtuális csomópontok saját Azure-beli virtuális hálózati alhálózatának használatára vannak konfigurálva. Ez az alhálózat delegált engedélyekkel rendelkezik az Azure-erőforrások az AK-fürthöz való összekapcsolásához. Ha még nem rendelkezik delegált alhálózattal, a Azure Portal létrehozza és konfigurálja az Azure-beli virtuális hálózatot és az alhálózatot a virtuális csomópontokkal való használatra.

Válassza az **Áttekintés + létrehozás** lehetőséget. Az ellenőrzés befejezése után válassza a **Létrehozás**lehetőséget.

Az AKS-fürt létrehozása és a használatra való előkészítése néhány percet vesz igénybe.

## <a name="connect-to-the-cluster"></a>Csatlakozás a fürthöz

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Kubernetes-fürtök kezeléséhez használja a [kubectl][kubectl] eszközt, a Kubernetes parancssori ügyfelét. A `kubectl` ügyfél előzetesen már telepítve van az Azure Cloud Shellben.

A Cloud Shell megnyitásához válassza a **kipróbálás** elemet a kód jobb felső sarkában. A Cloud Shellt egy külön böngészőlapon is elindíthatja a [https://shell.azure.com/bash](https://shell.azure.com/bash) cím megnyitásával. A **Másolás** kiválasztásával másolja és illessze be a kódrészleteket a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz.

A Kubernetes-fürthöz való kapcsolódáshoz használja az az `kubectl` [AK Get-hitelesítőadats][az-aks-get-credentials] parancsot. A következő példa lekéri a *myResourceGroup* erőforrásban lévő *myAKSCluster* fürtnév hitelesítő adatait:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

A fürthöz való csatlakozás ellenőrzéséhez használja a [kubectl get][kubectl-get] parancsot a fürtcsomópontok listájának lekéréséhez.

```azurecli-interactive
kubectl get nodes
```

A következő példa kimenete az egyetlen virtuálisgép-csomópontot mutatja, majd a Linux, *Virtual-Node-ACI-Linux*virtuális csomópontot:

```
$ kubectl get nodes

NAME                           STATUS    ROLES     AGE       VERSION
virtual-node-aci-linux         Ready     agent     28m       v1.11.2
aks-agentpool-14693408-0       Ready     agent     32m       v1.11.2
```

## <a name="deploy-a-sample-app"></a>Minta alkalmazás üzembe helyezése

A Azure Cloud Shell hozzon létre egy nevű `virtual-node.yaml` fájlt, és másolja a következő YAML. A tárolónak a csomóponton való megadásához meg kell adni a [nodeSelector][node-selector] és a [tolerancia][toleration] értéket. Ezek a beállítások lehetővé teszik, hogy a pod legyen ütemezve a virtuális csomóponton, és ellenőrizze, hogy a szolgáltatás sikeresen engedélyezve van-e.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aci-helloworld
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aci-helloworld
  template:
    metadata:
      labels:
        app: aci-helloworld
    spec:
      containers:
      - name: aci-helloworld
        image: microsoft/aci-helloworld
        ports:
        - containerPort: 80
      nodeSelector:
        kubernetes.io/role: agent
        beta.kubernetes.io/os: linux
        type: virtual-kubelet
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Exists
      - key: azure.com/aci
        effect: NoSchedule
```

Futtassa az alkalmazást az [kubectl Apply][kubectl-apply] paranccsal.

```azurecli-interactive
kubectl apply -f virtual-node.yaml
```

A [kubectl Get hüvely][kubectl-get] parancs használatával adja meg `-o wide` az argumentumot a hüvelyek és az ütemezett csomópontok listájának kimenetéhez. `virtual-node-helloworld` Figyelje`virtual-node-linux` meg, hogy a pod ütemezve van a csomóponton.

```
$ kubectl get pods -o wide

NAME                                     READY     STATUS    RESTARTS   AGE       IP           NODE
virtual-node-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

A pod a virtuális csomópontokkal való használatra delegált Azure virtuális hálózati alhálózatból származó belső IP-címet kap.

> [!NOTE]
> Ha Azure Container Registryban tárolt rendszerképeket használ, [konfigurálja és használja a Kubernetes titkos kulcsát][acr-aks-secrets]. A virtuális csomópontok jelenlegi korlátozása az, hogy nem használhatja az integrált Azure AD szolgáltatás egyszerű hitelesítését. Ha nem használ titkos kódot, a virtuális csomópontokon ütemezett hüvelyek nem indulnak el, és `HTTP response status code 400 error code "InaccessibleImage"`nem jelentik a hibát.

## <a name="test-the-virtual-node-pod"></a>A virtuális csomópont-Pod tesztelése

A virtuális csomóponton futó Pod teszteléséhez keresse meg a bemutató alkalmazást egy webes ügyféllel. Mivel a pod belső IP-cím van hozzárendelve, gyorsan tesztelheti ezt a kapcsolatot egy másik Pod-on az AK-fürtön. Hozzon létre egy teszt Pod-t, és csatoljon hozzá egy terminál-munkamenetet:

```azurecli-interactive
kubectl run -it --rm virtual-node-test --image=debian
```

Telepítés `curl` a pod használatával `apt-get`:

```azurecli-interactive
apt-get update && apt-get install -y curl
```

Most nyissa meg a pod-t `curl` *http://10.241.0.4* a használatával, például:. Az előző `kubectl get pods` parancsban megjelenő saját belső IP-cím megadása:

```azurecli-interactive
curl -L http://10.241.0.4
```

Megjelenik a bemutató alkalmazás, ahogy az a következő tömörített példában látható:

```
$ curl -L 10.241.0.4

<html>
<head>
  <title>Welcome to Azure Container Instances!</title>
</head>
[...]
```

Zárjuk be a terminál-munkamenetet a test `exit`Pod-be a használatával. Ha a munkamenet véget ér, a rendszer törli a pod-t.

## <a name="next-steps"></a>További lépések

Ebben a cikkben egy Pod-t ütemeztek a virtuális csomóponton, és egy privát, belső IP-címet rendeltek hozzá. Ehelyett hozzon létre egy szolgáltatás központi telepítését, és irányítsa a forgalmat a pod-ra egy terheléselosztó vagy egy bejövő vezérlő használatával. További információkért lásd: alapszintű [bejövő vezérlő létrehozása az AK-ban][aks-basic-ingress].

A virtuális csomópontok egy méretezési megoldás egyik összetevője az AK-ban. A megoldások méretezésével kapcsolatos további információkért tekintse meg a következő cikkeket:

- [A Kubernetes vízszintes Pod automéretező használata][aks-hpa]
- [A Kubernetes-fürt automéretező használata][aks-cluster-autoscaler]
- [Tekintse meg a virtuális csomópontok méretezési mintáját][virtual-node-autoscale]
- [További információ a virtuális Kubelet nyílt forráskódú könyvtáráról][virtual-kubelet-repo]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[aks-github]: https://github.com/azure/aks/issues]
[virtual-node-autoscale]: https://github.com/Azure-Samples/virtual-node-autoscale
[virtual-kubelet-repo]: https://github.com/virtual-kubelet/virtual-kubelet

<!-- LINKS - internal -->
[aks-network]: ./networking-overview.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: cluster-autoscaler.md
[aks-basic-ingress]: ingress-basic.md
[acr-aks-secrets]: ../container-registry/container-registry-auth-aks.md#access-with-kubernetes-secret
[az-provider-list]: /cli/azure/provider#az-provider-list