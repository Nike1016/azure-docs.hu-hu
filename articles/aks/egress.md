---
title: Statikus IP-cím a kimenő forgalomhoz az Azure Kubernetes szolgáltatásban (ak)
description: Megtudhatja, hogyan hozhat létre és használhat statikus nyilvános IP-címet a kimenő forgalomhoz egy Azure Kubernetes Service-(ak-) fürtben
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 03/04/2019
ms.author: mlearned
ms.openlocfilehash: 67471d688e64244067a7537bc87c379da4a69c03
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68696362"
---
# <a name="use-a-static-public-ip-address-for-egress-traffic-in-azure-kubernetes-service-aks"></a>Statikus nyilvános IP-cím használata a kimenő forgalomhoz az Azure Kubernetes szolgáltatásban (ak)

Alapértelmezés szerint a rendszer véletlenszerűen rendeli hozzá a kimenő IP-címet egy Azure Kubernetes-szolgáltatás (ak) fürtjéből. Ez a konfiguráció nem ideális, ha meg kell határoznia egy IP-címet a külső szolgáltatásokhoz való hozzáféréshez, például:. Ehelyett előfordulhat, hogy hozzá kell rendelnie egy statikus IP-címet, amely engedélyezhető a szolgáltatás eléréséhez.

Ebből a cikkből megtudhatja, hogyan hozhat létre és használhat egy statikus nyilvános IP-címet egy AK-fürt kimenő forgalmának használatával.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy rendelkezik egy meglévő AK-fürttel. Ha AK-fürtre van szüksége, tekintse meg az AK gyors üzembe helyezését [Az Azure CLI használatával][aks-quickstart-cli] vagy [a Azure Portal használatával][aks-quickstart-portal].

Szüksége lesz az Azure CLI 2.0.59 vagy újabb verziójára is, valamint a telepítésre és konfigurálásra. A `az --version` verzió megkereséséhez futtassa a parancsot. Ha telepíteni vagy frissíteni szeretne, tekintse meg az [Azure CLI telepítését][install-azure-cli]ismertető témakört.

## <a name="egress-traffic-overview"></a>Kimenő forgalom áttekintése

Az AK-fürtök kimenő forgalma [Azure Load Balancer konvenciókat][outbound-connections]követi. Az első Kubernetes-szolgáltatás `LoadBalancer` létrehozása előtt egy AK-fürt ügynök-csomópontjai nem tartoznak egyetlen Azure Load Balancer-készletbe sem. Ebben a konfigurációban a csomópontok nem rendelkeznek példány-szintű nyilvános IP-címmel. Az Azure a kimenő forgalmat olyan nyilvános forrású IP-címekre fordítja le, amely nem konfigurálható vagy determinisztikus.

Ha létrejön egy Kubernetes-szolgáltatás `LoadBalancer` , az ügynök csomópontjai hozzáadódnak egy Azure Load Balancer készlethez. A kimenő forgalom esetében az Azure a terheléselosztó által konfigurált első nyilvános IP-címet fordítja le. Ez a nyilvános IP-cím csak az adott erőforrás élettartama esetén érvényes. Ha törli a Kubernetes terheléselosztó szolgáltatást, a rendszer a társított terheléselosztó és az IP-cím is törlődik. Ha egy adott IP-címet szeretne hozzárendelni, vagy IP-címet kíván megőrizni az újratelepített Kubernetes-szolgáltatásokhoz, létrehozhat és használhat statikus nyilvános IP-címet.

## <a name="create-a-static-public-ip"></a>Statikus nyilvános IP-cím létrehozása

Kérje le az erőforráscsoport nevét az az [az AK show][az-aks-show] paranccsal, és adja `--query nodeResourceGroup` hozzá a lekérdezési paramétert. A következő példa lekéri a csomópont-erőforráscsoportot az AK-fürt neve *myAKSCluster* az erőforráscsoport neve *myResourceGroup*:

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Most hozzon létre egy statikus nyilvános IP-címet az az [Network Public IP Create][az-network-public-ip-create] paranccsal. Adja meg az előző parancsban beszerzett csomópont-erőforráscsoport nevét, majd az IP-cím erőforrás nevét, például *myAKSPublicIP*:

```azurecli-interactive
az network public-ip create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name myAKSPublicIP \
    --allocation-method static
```

Az IP-cím jelenik meg, ahogy az a következő tömörített példában látható:

```json
{
  "publicIp": {
    "dnsSettings": null,
    "etag": "W/\"6b6fb15c-5281-4f64-b332-8f68f46e1358\"",
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/Microsoft.Network/publicIPAddresses/myAKSPublicIP",
    "idleTimeoutInMinutes": 4,
    "ipAddress": "40.121.183.52",
    [..]
  }
```

Később lekérheti a nyilvános IP-címet az az [Network Public-IP List][az-network-public-ip-list] paranccsal. Adja meg a csomópont-erőforráscsoport nevét, majd az *IP* -cím lekérdezését az alábbi példában látható módon:

```azurecli-interactive
$ az network public-ip list --resource-group MC_myResourceGroup_myAKSCluster_eastus --query [0].ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-with-the-static-ip"></a>Szolgáltatás létrehozása a statikus IP-címmel

Egy statikus nyilvános IP-címmel rendelkező szolgáltatás létrehozásához adja hozzá a `loadBalancerIP` tulajdonságot és a statikus nyilvános IP-cím értékét a YAML jegyzékfájlhoz. Hozzon létre egy `egress-service.yaml` nevű fájlt, és másolja a következő YAML. Adja meg az előző lépésben létrehozott saját nyilvános IP-címet.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-egress
spec:
  loadBalancerIP: 40.121.183.52
  type: LoadBalancer
  ports:
  - port: 80
```

Hozza létre a szolgáltatást és az üzembe `kubectl apply` helyezést a paranccsal.

```console
kubectl apply -f egress-service.yaml
```

Ez a szolgáltatás egy új előtér-IP-címet konfigurál a Azure Load Balancer. Ha más IP-címek nincsenek konfigurálva, akkor az **összes** kimenő forgalomnak most ezt a címet kell használnia. Ha több cím van konfigurálva a Azure Load Balanceron, a kimenő forgalom a terheléselosztó első IP-címét használja.

## <a name="verify-egress-address"></a>Kimenő forgalom ellenőrzésének ellenőrzése

Annak ellenőrzéséhez, hogy a statikus nyilvános IP-cím használatban van-e, használhatja a DNS-alapú `checkip.dyndns.org`keresés szolgáltatást, például:.

Alapszintű *Debian* Pod elindítása és csatlakoztatása:

```console
kubectl run -it --rm aks-ip --image=debian --generator=run-pod/v1
```

Webhelynek a tárolóból való eléréséhez használja `apt-get` a t a tárolóba való telepítéshez. `curl`

```console
apt-get update && apt-get install curl -y
```

Most a curl használatával férhet hozzá a *checkip.dyndns.org* webhelyhez. Megjelenik a kimenő forgalom IP-címe, ahogy az az alábbi példában is látható. Ez az IP-cím megegyezik a terheléselosztó szolgáltatáshoz létrehozott és definiált statikus nyilvános IP-címmel:

```console
$ curl -s checkip.dyndns.org

<html><head><title>Current IP Check</title></head><body>Current IP Address: 40.121.183.52</body></html>
```

## <a name="next-steps"></a>További lépések

Ha el szeretné kerülni a Azure Load Balancer több nyilvános IP-cím fenntartását, használhat egy bejövő vezérlőt is. A beáramlási vezérlők további előnyöket biztosítanak, például az SSL/TLS-megszakítást, az URI-újraírások támogatását és az SSL/TLS-titkosítást. További információkért lásd: alapszintű [bejövő vezérlő létrehozása az AK-ban][ingress-aks-cluster].

<!-- LINKS - internal -->
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[az-aks-show]: /cli/azure/aks#az-aks-show
[azure-cli-install]: /cli/azure/install-azure-cli
[ingress-aks-cluster]: ./ingress-basic.md
[outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md#scenarios
[public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
