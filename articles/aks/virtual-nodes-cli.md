---
title: Virtuális csomópontok létrehozása az Azure CLI használatával az Azure Kubernetes Servicesben (ak)
description: Ismerje meg, hogyan használhatja az Azure CLI-t olyan Azure Kubernetes Services-(ak-) fürt létrehozásához, amely virtuális csomópontokat használ a hüvelyek futtatásához.
services: container-service
author: mlearned
ms.topic: conceptual
ms.service: container-service
ms.date: 05/06/2019
ms.author: mlearned
ms.openlocfilehash: a6acdd6255278123ff13a8597cadd2a386536bd4
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/12/2019
ms.locfileid: "67613785"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-using-the-azure-cli"></a>Azure Kubernetes Services (ak) fürt létrehozása és konfigurálása virtuális csomópontok használatára az Azure CLI használatával

Az alkalmazások számítási feladatainak gyors méretezéséhez egy Azure Kubernetes-szolgáltatásbeli (ak-beli) fürtben virtuális csomópontokat használhat. A virtuális csomópontokkal gyorsan kiépítheti a hüvelyeket, és a végrehajtásuk ideje alatt csak másodpercenként kell fizetnie. A további hüvelyek futtatásához nem kell várnia, hogy a Kubernetes-fürt autoskálázása virtuálisgép-számítási csomópontokat helyezzen üzembe. A virtuális csomópontok csak Linux-hüvelyek és-csomópontok esetén támogatottak.

Ez a cikk bemutatja, hogyan hozhatja létre és konfigurálhatja a virtuális hálózati erőforrásokat és az AK-fürtöt, majd engedélyezheti a virtuális csomópontokat.

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

Ha a szolgáltató *NotRegistered*-ként jelenik meg, regisztrálja a szolgáltatót az az [Provider Register][az-provider-register] paranccsal az alábbi példában látható módon:

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

## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell indítása

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta.

A Cloud Shell megnyitásához válassza a **kipróbálás** elemet a kód jobb felső sarkában. A Cloud Shellt egy külön böngészőlapon is elindíthatja a [https://shell.azure.com/bash](https://shell.azure.com/bash) cím megnyitásával. A **Másolás** kiválasztásával másolja és illessze be a kódrészleteket a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz.

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez a cikkhez az Azure CLI 2.0.49 vagy újabb verziójára van szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az Azure-erőforráscsoport olyan logikai csoport, amelyben az Azure-erőforrások üzembe helyezése és kezelése zajlik. Hozzon létre egy erőforráscsoportot az [az group create][az-group-create] paranccsal. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot a *westus* helyen.

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Hozzon létre egy virtuális hálózatot az az [Network vnet Create][az-network-vnet-create] paranccsal. A következő példában létrehozunk egy *myVnet* nevű virtuális hálózatot a *10.0.0.0/8*, valamint egy *myAKSSubnet*nevű alhálózatot. Ennek az alhálózatnak az alapértelmezett *10.240.0.0/16*előtagja:

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 10.240.0.0/16
```

Most hozzon létre egy további alhálózatot a virtuális csomópontok számára az az [Network vnet subnet Create][az-network-vnet-subnet-create] paranccsal. A következő példa létrehoz egy *myVirtualNodeSubnet* nevű alhálózatot a *10.241.0.0/16*előtaggal.

```azurecli-interactive
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name myVirtualNodeSubnet \
    --address-prefixes 10.241.0.0/16
```

## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

Ahhoz, hogy egy AKS-fürt kommunikálhasson más Azure-erőforrásokkal, Azure Active Directory-szolgáltatásnevet kell használnia. A szolgáltatásnév automatikusan létrehozható az Azure CLI-vel vagy a portállal, vagy létrehozhat egyet előre, és hozzárendelhet további engedélyeket.

Hozzon létre egy szolgáltatásnevet az [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] paranccsal. A `--skip-assignment` paraméter korlátozza a további engedélyek hozzárendelését.

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

A kimenet a következő példához hasonló:

```
{
  "appId": "bef76eb3-d743-4a97-9534-03e9388811fc",
  "displayName": "azure-cli-2018-11-21-18-42-00",
  "name": "http://azure-cli-2018-11-21-18-42-00",
  "password": "1d257915-8714-4ce7-a7fb-0e5a5411df7f",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

Jegyezze fel az *appID* és a *password* értékét. A következő lépésekben szükség lesz ezekre az értékekre.

## <a name="assign-permissions-to-the-virtual-network"></a>Engedélyek kiosztása a virtuális hálózathoz

Annak engedélyezéséhez, hogy a fürt használhassa és felügyelje a virtuális hálózatot, meg kell adnia a megfelelő jogosultságokat a hálózati erőforrások használatához.

Először szerezze be a virtuális hálózati erőforrás AZONOSÍTÓját az [az Network vnet show][az-network-vnet-show]paranccsal:

```azurecli-interactive
az network vnet show --resource-group myResourceGroup --name myVnet --query id -o tsv
```

Ahhoz, hogy a virtuális hálózat használatára megfelelő hozzáférést biztosítson az AK-fürt számára, hozzon létre egy szerepkör-hozzárendelést az az [role hozzárendelés Create][az-role-assignment-create] paranccsal. Cserélje le az `<appId`> és `<vnetId>` helyőrzőket az előző két lépésben beszerzett értékekre.

```azurecli-interactive
az role assignment create --assignee <appId> --scope <vnetId> --role Contributor
```

## <a name="create-an-aks-cluster"></a>AKS-fürt létrehozása

Egy AK-fürtöt az előző lépésben létrehozott AK alhálózatba helyez üzembe. Az alhálózat AZONOSÍTÓjának lekérése az [az Network vnet subnet show][az-network-vnet-subnet-show]paranccsal:

```azurecli-interactive
az network vnet subnet show --resource-group myResourceGroup --vnet-name myVnet --name myAKSSubnet --query id -o tsv
```

Az az [AK Create][az-aks-create] paranccsal hozzon létre egy AK-fürtöt. A következő példa egy *myAKSCluster* nevű fürtöt hoz létre egy csomóponttal. Cserélje `<subnetId>` le az azonosítót az előző lépésben beszerzett azonosítóra, `<appId>` `<password>` majd a 

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --network-plugin azure \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id <subnetId> \
    --service-principal <appId> \
    --client-secret <password>
```

Néhány perc múlva befejeződik a parancs végrehajtása, és visszaadja a fürttel kapcsolatos adatokat JSON formátumban.

## <a name="enable-virtual-nodes-addon"></a>A virtuális csomópontok addon engedélyezése

A virtuális csomópontok engedélyezéséhez használja az az [AK Enable-addons][az-aks-enable-addons] parancsot. A következő példa a *myVirtualNodeSubnet* nevű alhálózatot használja az előző lépésben:

```azurecli-interactive
az aks enable-addons \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --addons virtual-node \
    --subnet-name myVirtualNodeSubnet
```

## <a name="connect-to-the-cluster"></a>Csatlakozás a fürthöz

A Kubernetes `kubectl` -fürthöz való kapcsolódás konfigurálásához használja az az az [AK Get-hitelesítőadats][az-aks-get-credentials] parancsot. Ebben a lépésben a rendszer hitelesítő adatokat tölt le, és konfigurálja a Kubernetes parancssori felületét azok használatára.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

A fürthöz való csatlakozás ellenőrzéséhez használja a [kubectl get][kubectl-get] parancsot a fürtcsomópontok listájának lekéréséhez.

```console
kubectl get nodes
```

A következő példa kimenete az egyetlen virtuálisgép-csomópontot mutatja, majd a Linux, *Virtual-Node-ACI-Linux*virtuális csomópontot:

```
$ kubectl get nodes

NAME                          STATUS    ROLES     AGE       VERSION
virtual-node-aci-linux        Ready     agent     28m       v1.11.2
aks-agentpool-14693408-0      Ready     agent     32m       v1.11.2
```

## <a name="deploy-a-sample-app"></a>Minta alkalmazás üzembe helyezése

Hozzon létre egy `virtual-node.yaml` nevű fájlt, és másolja a következő YAML. A tárolónak a csomóponton való megadásához meg kell adni a [nodeSelector][node-selector] és a [tolerancia][toleration] értéket.

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

```console
kubectl apply -f virtual-node.yaml
```

A [kubectl Get hüvely][kubectl-get] parancs használatával adja meg `-o wide` az argumentumot a hüvelyek és az ütemezett csomópontok listájának kimenetéhez. `aci-helloworld` Figyelje`virtual-node-aci-linux` meg, hogy a pod ütemezve van a csomóponton.

```
$ kubectl get pods -o wide

NAME                            READY     STATUS    RESTARTS   AGE       IP           NODE
aci-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

A pod a virtuális csomópontokkal való használatra delegált Azure virtuális hálózati alhálózatból származó belső IP-címet kap.

> [!NOTE]
> Ha Azure Container Registryban tárolt rendszerképeket használ, [konfigurálja és használja a Kubernetes titkos kulcsát][acr-aks-secrets]. A virtuális csomópontok jelenlegi korlátozása az, hogy nem használhatja az integrált Azure AD szolgáltatás egyszerű hitelesítését. Ha nem használ titkos kódot, a virtuális csomópontokon ütemezett hüvelyek nem indulnak el, és `HTTP response status code 400 error code "InaccessibleImage"`nem jelentik a hibát.

## <a name="test-the-virtual-node-pod"></a>A virtuális csomópont-Pod tesztelése

A virtuális csomóponton futó Pod teszteléséhez keresse meg a bemutató alkalmazást egy webes ügyféllel. Mivel a pod belső IP-cím van hozzárendelve, gyorsan tesztelheti ezt a kapcsolatot egy másik Pod-on az AK-fürtön. Hozzon létre egy teszt Pod-t, és csatoljon hozzá egy terminál-munkamenetet:

```console
kubectl run -it --rm virtual-node-test --image=debian
```

Telepítés `curl` a pod használatával `apt-get`:

```console
apt-get update && apt-get install -y curl
```

Most nyissa meg a pod-t `curl` *http://10.241.0.4* a használatával, például:. Az előző `kubectl get pods` parancsban megjelenő saját belső IP-cím megadása:

```console
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

## <a name="remove-virtual-nodes"></a>Virtuális csomópontok eltávolítása

Ha már nem szeretné használni a virtuális csomópontokat, letilthatja azokat az az [AK disable-addons][az aks disable-addons] parancs használatával. 

Először törölje a virtuális csomóponton futó HelloWorld Pod-t:

```azurecli-interactive
kubectl delete -f virtual-node.yaml
```

A következő példa parancs letiltja a linuxos virtuális csomópontokat:

```azurecli-interactive
az aks disable-addons --resource-group myResourceGroup --name myAKSCluster --addons virtual-node
```

Most távolítsa el a virtuális hálózati erőforrásokat és az erőforráscsoportot:

```azurecli-interactive
# Change the name of your resource group, cluster and network resources as needed
RES_GROUP=myResourceGroup
AKS_CLUSTER=myAKScluster
AKS_VNET=myVnet
AKS_SUBNET=myVirtualNodeSubnet

# Get AKS node resource group
NODE_RES_GROUP=$(az aks show --resource-group $RES_GROUP --name $AKS_CLUSTER --query nodeResourceGroup --output tsv)

# Get network profile ID
NETWORK_PROFILE_ID=$(az network profile list --resource-group $NODE_RES_GROUP --query [0].id --output tsv)

# Delete the network profile
az network profile delete --id $NETWORK_PROFILE_ID -y

# Get the service association link (SAL) ID
SAL_ID=$(az network vnet subnet show --resource-group $RES_GROUP --vnet-name $AKS_VNET --name $AKS_SUBNET --query id --output tsv)/providers/Microsoft.ContainerInstance/serviceAssociationLinks/default

# Delete the default SAL ID for the subnet
az resource delete --ids $SAL_ID --api-version 2018-07-01

# Delete the subnet delegation to Azure Container Instances
az network vnet subnet update --resource-group $RES_GROUP --vnet-name $AKS_VNET --name $AKS_SUBNET --remove delegations 0
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben egy Pod-t ütemeztek a virtuális csomóponton, és egy privát, belső IP-címet rendeltek hozzá. Ehelyett hozzon létre egy szolgáltatás központi telepítését, és irányítsa a forgalmat a pod-ra egy terheléselosztó vagy egy bejövő vezérlő használatával. További információkért lásd: alapszintű [bejövő vezérlő létrehozása az AK-ban][aks-basic-ingress].

A virtuális csomópontok gyakran az AK-ban lévő skálázási megoldás egyik összetevője. A megoldások méretezésével kapcsolatos további információkért tekintse meg a következő cikkeket:

- [A Kubernetes vízszintes Pod automéretező használata][aks-hpa]
- [A Kubernetes-fürt automéretező használata][aks-cluster-autoscaler]
- [Tekintse meg a virtuális csomópontok méretezési mintáját][virtual-node-autoscale]
- [További információ a virtuális Kubelet nyílt forráskódú könyvtáráról][virtual-kubelet-repo]

<!-- LINKS - external -->
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[aks-github]: https://github.com/azure/aks/issues
[virtual-node-autoscale]: https://github.com/Azure-Samples/virtual-node-autoscale
[virtual-kubelet-repo]: https://github.com/virtual-kubelet/virtual-kubelet

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-create]: /cli/azure/group#az-group-create
[az-network-vnet-create]: /cli/azure/network/vnet#az-network-vnet-create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-network-vnet-show]: /cli/azure/network/vnet#az-network-vnet-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-show
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-enable-addons]: /cli/azure/aks#az-aks-enable-addons
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az aks disable-addons]: /cli/azure/aks#az-aks-disable-addons
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: autoscaler.md
[aks-basic-ingress]: ingress-basic.md
[az-provider-list]: /cli/azure/provider#az-provider-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[acr-aks-secrets]: ../container-registry/container-registry-auth-aks.md#access-with-kubernetes-secret
