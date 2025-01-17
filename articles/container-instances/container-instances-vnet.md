---
title: Tároló-példányok üzembe helyezése Azure-beli virtuális hálózatban
description: Megtudhatja, hogyan helyezhet üzembe tároló csoportokat egy új vagy meglévő Azure-beli virtuális hálózaton.
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 07/11/2019
ms.author: danlep
ms.openlocfilehash: ad7f93bb3934ca01b7f45c0bd4b5cc8be81ea54b
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325523"
---
# <a name="deploy-container-instances-into-an-azure-virtual-network"></a>Tároló-példányok üzembe helyezése Azure-beli virtuális hálózatban

Az [azure Virtual Network](../virtual-network/virtual-networks-overview.md) biztonságos, privát hálózatkezelést biztosít az Azure-hoz és a helyszíni erőforrásokhoz. Ha tároló-csoportokat telepít egy Azure-beli virtuális hálózatba, a tárolók biztonságosan kommunikálhatnak a virtuális hálózat többi erőforrásával.

Az Azure-beli virtuális hálózatba üzembe helyezett tároló-csoportok a következőkhöz hasonló forgatókönyveket tesznek lehetővé:

* Azonos alhálózaton található tároló csoportok közötti közvetlen kommunikáció
* [Feladat-alapú munkaterhelés-](container-instances-restart-policy.md) kimenet küldése a tároló példányaiból a virtuális hálózatban található adatbázisba
* Tároló-példányok tartalmának beolvasása a virtuális hálózat [szolgáltatásbeli végpontján](../virtual-network/virtual-network-service-endpoints-overview.md)
* A virtuális hálózatban található virtuális gépekkel való kommunikáció
* Helyszíni erőforrásokkal való kommunikáció a [VPN-átjárón](../vpn-gateway/vpn-gateway-about-vpngateways.md) vagy [ExpressRoute](../expressroute/expressroute-introduction.md) keresztül

> [!IMPORTANT]
> Ez a funkció jelenleg előzetes verzióban érhető el, és bizonyos [korlátozások érvényesek](#preview-limitations). Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe][terms-of-use]. A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.

## <a name="virtual-network-deployment-limitations"></a>A virtuális hálózat központi telepítésére vonatkozó korlátozások

Bizonyos korlátozások akkor lépnek érvénybe, ha a tároló-csoportokat virtuális hálózatra telepíti.

* A tároló-csoportok alhálózatra történő telepítéséhez az alhálózat nem tartalmazhat más típusú erőforrásokat. Távolítsa el a meglévő alhálózatból az összes meglévő erőforrást a tároló-csoportok üzembe helyezése előtt, vagy hozzon létre egy új alhálózatot.
* Nem használhat felügyelt [identitást](container-instances-managed-identity.md) egy virtuális hálózatra központilag telepített tároló csoportba.
* Az érintett további hálózati erőforrások miatt a tároló-csoportok virtuális hálózatra történő telepítése általában valamivel lassabb, mint a standard Container-példányok üzembe helyezése.

## <a name="preview-limitations"></a>Előzetes verzió korlátozásai

Habár ez a funkció előzetes verzióban érhető el, a következő korlátozások érvényesek a tárolók virtuális hálózatra történő telepítésekor. 

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

A tárolók erőforrásainak korlátai eltérőek lehetnek a nem hálózati tároló példányainak korlátaiban ezekben a régiókban. Ez a funkció jelenleg csak a Linux-tárolókat támogatja. A Windows-támogatás tervezett.

### <a name="unsupported-networking-scenarios"></a>Nem támogatott hálózatkezelési forgatókönyvek 

* **Azure Load Balancer** – a hálózati tárolóban található tároló példányai előtt egy Azure Load Balancer elhelyezése nem támogatott
* **Virtuális hálózati** társítás – nem lehet társ olyan virtuális hálózat, amely egy másik virtuális hálózat Azure Container instances delegált alhálózatot tartalmaz
* **Útválasztási táblák** – a felhasználó által megadott útvonalak nem állíthatók be a Azure Container instances delegált alhálózaton
* **Hálózati biztonsági csoportok** – a nsg egy Azure Container instances delegált alhálózatra alkalmazott kimenő biztonsági szabályok jelenleg nincsenek kényszerítve 
* A virtuális hálózatra telepített **nyilvános IP-cím vagy DNS** -tároló csoportok jelenleg nem támogatják a tárolók közvetlen közzétételét nyilvános IP-címmel vagy teljes tartománynévvel.
* **Belső névfeloldás** – a virtuális hálózatban lévő Azure-erőforrások névfeloldása belső Azure DNS használatával nem támogatott

A **hálózati erőforrás törléséhez** [további lépések](#delete-network-resources) szükségesek, miután telepítette a tároló-csoportokat a virtuális hálózatra.

## <a name="required-network-resources"></a>Szükséges hálózati erőforrások

A tároló-csoportok virtuális hálózathoz való telepítéséhez három Azure Virtual Network erőforrás szükséges: maga a [virtuális hálózat](#virtual-network) , a virtuális hálózaton belüli [delegált alhálózat](#subnet-delegated) és egy [hálózati profil](#network-profile). 

### <a name="virtual-network"></a>Virtuális hálózat

A virtuális hálózat határozza meg azt a címtartományt, amelyben egy vagy több alhálózatot hoz létre. Ezután üzembe helyezheti az Azure-erőforrásokat (például a Container groups-t) a virtuális hálózat alhálózatai között.

### <a name="subnet-delegated"></a>Alhálózat (delegált)

Alhálózatok, külön címterek használható a virtuális hálózat szegmentáljon az Azure-erőforrások bennük. Létrehoz egy vagy több alhálózatot egy virtuális hálózaton belül.

A tárolók csoportjaihoz használt alhálózat csak tároló csoportokat tartalmazhat. Amikor először telepít egy tároló csoportot egy alhálózatra, az Azure delegálja az alhálózatot Azure Container Instances. A delegálás után az alhálózat csak a tároló csoportok esetében használható. Ha a tárolói csoportokon kívül más erőforrásokat is telepít egy delegált alhálózatra, a művelet sikertelen lesz.

### <a name="network-profile"></a>Hálózati profil

A hálózati profil az Azure-erőforrások hálózati konfigurációs sablonja. Megadja az erőforrás bizonyos hálózati tulajdonságait, például azt az alhálózatot, amelybe központilag telepíteni kívánja. Amikor először az az [Container Create][az-container-create] paranccsal telepít egy tároló csoportot egy alhálózatra (és így egy virtuális hálózatra), az Azure létrehoz egy hálózati profilt. Ezt a hálózati profilt ezután a jövőbeli központi telepítések számára is használhatja az alhálózathoz. 

Ha Resource Manager-sablont, YAML-fájlt vagy programozott módszert szeretne használni egy alhálózatra, meg kell adnia egy hálózati profil teljes Resource Manager erőforrás-AZONOSÍTÓját. Használhat korábban [az az Container Create][az-container-create]paranccsal létrehozott profilt, vagy létrehozhat egy profilt egy Resource Manager-sablonnal (lásd a [sablon példáját](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet) és a [hivatkozást](https://docs.microsoft.com/azure/templates/microsoft.network/networkprofiles)). Egy korábban létrehozott profil AZONOSÍTÓjának lekéréséhez használja az az [Network profil List][az-network-profile-list] parancsot. 

A következő ábrán számos tároló csoport lett telepítve egy Azure Container Instances-ra delegált alhálózatra. Miután telepített egy tároló csoportot egy alhálózatra, további tároló csoportokat is telepíthet rá, ha ugyanazt a hálózati profilt megadja.

![Virtuális hálózaton belüli tároló-csoportok][aci-vnet-01]

## <a name="deployment-scenarios"></a>Üzembe helyezési forgatókönyvek

Az [az Container Create][az-container-create] paranccsal telepíthet tároló csoportokat egy új virtuális hálózatra, és engedélyezheti az Azure számára a szükséges hálózati erőforrások létrehozását, vagy üzembe helyezését egy meglévő virtuális hálózaton. 

### <a name="new-virtual-network"></a>Új virtuális hálózat

Ha új virtuális hálózatra kíván üzembe helyezni, és az Azure-hoz automatikusan hozza létre a hálózati erőforrásokat, akkor a következőt kell megadnia az az [Container Create][az-container-create]:

* Virtuális hálózat neve
* CIDR formátumú virtuális hálózati címek előtagja
* Alhálózat neve
* Alhálózat CIDR formátuma

A virtuális hálózat és az alhálózati címek előtagjai határozzák meg a virtuális hálózat és az alhálózat címterület-területét. Ezek az értékek az osztály nélküli tartományok közötti útválasztási (CIDR) jelöléssel jelennek meg, `10.0.0.0/16`például:. További információ az alhálózatok használatáról: [virtuális hálózati alhálózat hozzáadása, módosítása vagy törlése](../virtual-network/virtual-network-manage-subnet.md).

Miután telepítette az első tároló csoportját ezzel a módszerrel, a virtuális hálózat és az alhálózatok nevének megadásával, vagy az Azure által automatikusan létrehozott hálózati profil megadásával ugyanarra az alhálózatra is telepíthető. Mivel az Azure delegálja az alhálózatot Azure Container Instancesre, *csak* a tároló csoportokat telepítheti az alhálózatra.

### <a name="existing-virtual-network"></a>Meglévő virtuális hálózat

Tároló csoport üzembe helyezése meglévő virtuális hálózaton:

1. Hozzon létre egy alhálózatot a meglévő virtuális hálózaton belül, vagy Ürítse ki az *összes* többi erőforrás meglévő alhálózatát.
1. Helyezzen üzembe egy tároló csoportot az [az Container Create][az-container-create] paranccsal, és határozza meg a következők egyikét:
   * Virtuális hálózat neve és alhálózatának neve
   * Virtuális hálózati erőforrás-azonosító és alhálózati erőforrás-azonosító, amely lehetővé teszi, hogy egy másik erőforráscsoporthoz tartozó virtuális hálózatot használjon.
   * A hálózati profil neve vagy azonosítója, amelyet az [az Network profil List][az-network-profile-list] paranccsal szerezhet be

Miután üzembe helyezte az első tároló-csoportot egy meglévő alhálózaton, az Azure delegálja az alhálózatot Azure Container Instances. Ezen az alhálózaton nem helyezhetők el más erőforrások, mint a tárolók csoportjai.

## <a name="deployment-examples"></a>Üzembe helyezési példák

Az alábbi szakaszok azt ismertetik, hogyan helyezhetők üzembe tároló csoportok egy virtuális hálózaton az Azure CLI-vel. A parancs példák a **bash** -rendszerhéjra vannak formázva. Ha egy másik rendszerhéjt, például a PowerShellt vagy a parancssort részesíti előnyben, módosítsa a sor folytatási karaktereit ennek megfelelően.

### <a name="deploy-to-a-new-virtual-network"></a>Üzembe helyezés új virtuális hálózaton

Először telepítsen egy tároló csoportot, és határozza meg az új virtuális hálózat és alhálózat paramétereit. Ha megadja ezeket a paramétereket, az Azure létrehozza a virtuális hálózatot és az alhálózatot, delegálja az alhálózatot az Azure Container instances szolgáltatásba, és létrehoz egy hálózati profilt is. Miután létrehozta ezeket az erőforrásokat, a rendszer üzembe helyezi a tároló csoportot az alhálózaton.

Futtassa az alábbi az [Container Create][az-container-create] parancsot, amely az új virtuális hálózat és alhálózat beállításait adja meg. Meg kell adnia egy olyan erőforráscsoport nevét, amely egy virtuális hálózatban lévő tároló csoportokat [támogató](#preview-limitations) régióban lett létrehozva. Ez a parancs üzembe helyezi a nyilvános Microsoft [ACI-HelloWorld][aci-helloworld] tárolót, amely egy statikus weboldalt kiszolgáló kis Node. js webkiszolgálót futtat. A következő szakaszban egy második tároló csoportot fog telepíteni ugyanahhoz az alhálózathoz, és tesztelni a két tároló példány közötti kommunikációt.

```azurecli
az container create \
    --name appcontainer \
    --resource-group myResourceGroup \
    --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --vnet aci-vnet \
    --vnet-address-prefix 10.0.0.0/16 \
    --subnet aci-subnet \
    --subnet-address-prefix 10.0.0.0/24
```

Ha ezzel a módszerrel telepít egy új virtuális hálózatot, az üzembe helyezés eltarthat néhány percig, amíg a hálózati erőforrások létrejöttek. A kezdeti üzembe helyezést követően a további tároló csoportok telepítése is gyorsabban elvégezhető.

### <a name="deploy-to-existing-virtual-network"></a>Üzembe helyezés meglévő virtuális hálózaton

Most, hogy telepített egy tároló csoportot egy új virtuális hálózatra, helyezzen üzembe egy második tároló csoportot ugyanahhoz az alhálózathoz, és ellenőrizze a két tároló példány közötti kommunikációt.

Először szerezze be az elsőként telepített Container-csoport IP-címét, a *appcontainer*:

```azurecli
az container show --resource-group myResourceGroup --name appcontainer --query ipAddress.ip --output tsv
```

A kimenetnek a saját alhálózat tároló csoportjának IP-címét kell megjelenítenie:

```console
$ az container show --resource-group myResourceGroup --name appcontainer --query ipAddress.ip --output tsv
10.0.0.4
```

Most állítsa `CONTAINER_GROUP_IP` be a `az container show` paranccsal lekért IP-címet, és hajtsa végre a következő `az container create` parancsot. Ez a második tároló, a *commchecker*egy alpesi Linux-alapú rendszerképet futtat, `wget` és az első tároló csoport privát alhálózatának IP-címén hajtja végre.

```azurecli
CONTAINER_GROUP_IP=<container-group-IP-here>

az container create \
    --resource-group myResourceGroup \
    --name commchecker \
    --image alpine:3.5 \
    --command-line "wget $CONTAINER_GROUP_IP" \
    --restart-policy never \
    --vnet aci-vnet \
    --subnet aci-subnet
```

A második tároló üzembe helyezésének befejezése után húzza le a naplókat, hogy láthassa az általa végrehajtott `wget` parancs kimenetét:

```azurecli
az container logs --resource-group myResourceGroup --name commchecker
```

Ha a második tároló sikeresen kommunikált az elsővel, a kimenetnek a következőhöz hasonlónak kell lennie:

```console
$ az container logs --resource-group myResourceGroup --name commchecker
Connecting to 10.0.0.4 (10.0.0.4:80)
index.html           100% |*******************************|  1663   0:00:00 ETA
```

A napló kimenetének azt kell `wget` megjelennie, hogy képes volt csatlakozni a fájlhoz, és letölti az indexfájl az első tárolóból a saját magánhálózati IP-címének használatával a helyi alhálózaton. A két tároló csoport közötti hálózati forgalom a virtuális hálózaton belül marad.

### <a name="deploy-to-existing-virtual-network---yaml"></a>Üzembe helyezés meglévő virtuális hálózatban – YAML

YAML-fájl használatával is üzembe helyezhet egy tároló csoportot egy meglévő virtuális hálózaton. Ha egy virtuális hálózatban lévő alhálózatra kíván üzembe helyezni, több további tulajdonságot is meg kell adnia a YAML:

* `ipAddress`: A tároló csoport IP-címének beállításai.
  * `ports`: A megnyitni kívánt portok, ha vannak ilyenek.
  * `protocol`: A megnyitott port protokollja (TCP vagy UDP).
* `networkProfile`: Megadja a hálózati beállításokat, például a virtuális hálózatot és az alhálózatot egy Azure-erőforráshoz.
  * `id`: A teljes erőforrás-kezelő erőforrás-azonosítója `networkProfile`.

Ahhoz, hogy YAML-fájllal telepítsen egy tároló csoportot egy virtuális hálózatra, először le kell kérnie a hálózati profil AZONOSÍTÓját. Hajtsa végre az az [Network Profile List][az-network-profile-list] parancsot, és adja meg a virtuális hálózatot és a delegált alhálózatot tartalmazó erőforráscsoport nevét.

``` azurecli
az network profile list --resource-group myResourceGroup --query [0].id --output tsv
```

A parancs kimenete a hálózati profil teljes erőforrás-AZONOSÍTÓját jeleníti meg:

```console
$ az network profile list --resource-group myResourceGroup --query [0].id --output tsv
/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-aci-subnet
```

A hálózati profil azonosítója után másolja a következő YAML egy új, *vnet-Deploy-ACI. YAML*nevű fájlba. A `networkProfile`alatt cserélje le `id` az imént beolvasott azonosítójú értéket, majd mentse a fájlt. Ez a YAML létrehoz egy *appcontaineryaml* nevű tároló csoportot a virtuális hálózatában.

```YAML
apiVersion: '2018-09-01'
location: westus
name: appcontaineryaml
properties:
  containers:
  - name: appcontaineryaml
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      ports:
      - port: 80
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  ipAddress:
    type: Private
    ports:
    - protocol: tcp
      port: '80'
  networkProfile:
    id: /subscriptions/<Subscription ID>/resourceGroups/container/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-subnet
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Telepítse a tároló csoportot az az [Container Create][az-container-create] paranccsal, és adja meg a `--file` paraméter YAML-fájljának nevét:

```azurecli
az container create --resource-group myResourceGroup --file vnet-deploy-aci.yaml
```

Az üzembe helyezés befejezése után futtassa az az [Container show][az-container-show] parancsot az állapot megjelenítéséhez:

```console
$ az container show --resource-group myResourceGroup --name appcontaineryaml --output table
Name              ResourceGroup    Status    Image                                       IP:ports     Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  ------------------------------------------  -----------  ---------  ---------------  --------  ----------
appcontaineryaml  myResourceGroup  Running   mcr.microsoft.com/azuredocs/aci-helloworld  10.0.0.5:80  Private    1.0 core/1.5 gb  Linux     westus
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

### <a name="delete-container-instances"></a>Tároló példányainak törlése

Ha elkészült a létrehozott tároló-példányokkal, törölje azokat a következő parancsokkal:

```azurecli
az container delete --resource-group myResourceGroup --name appcontainer -y
az container delete --resource-group myResourceGroup --name commchecker -y
az container delete --resource-group myResourceGroup --name appcontaineryaml -y
```

### <a name="delete-network-resources"></a>Hálózati erőforrások törlése

Ennek a funkciónak a kezdeti előzetes verziójához több további parancs szükséges a korábban létrehozott hálózati erőforrások törléséhez. Ha a cikk előző részében szereplő, a virtuális hálózat és az alhálózat létrehozásához használt példás parancsokat használta, akkor a következő parancsfájllal törölheti a hálózati erőforrásokat.

A parancsfájl végrehajtása előtt állítsa a `RES_GROUP` változót a törölni kívánt virtuális hálózatot és alhálózatot tartalmazó erőforráscsoport nevére. Frissítse a virtuális hálózat nevét, ha nem a `aci-vnet` korábban javasolt nevet használta. A parancsfájl a bash-rendszerhéjhoz van formázva. Ha inkább egy másik rendszerhéjt, például a PowerShellt vagy a parancssort részesíti előnyben, akkor ennek megfelelően módosítania kell a változó hozzárendelés és a hozzáférési jogosultságokat.

> [!WARNING]
> Ez a szkript törli az erőforrásokat! Törli a virtuális hálózatot és a benne található összes alhálózatot. Győződjön meg arról, hogy a parancsfájl futtatása előtt már nincs szüksége a virtuális hálózatban lévő *összes* erőforrásra, beleértve a benne található alhálózatokat is. A törlés után **ezek az erőforrások nem állíthatók helyre**.

```azurecli
# Replace <my-resource-group> with the name of your resource group
RES_GROUP=<my-resource-group>

# Get network profile ID
NETWORK_PROFILE_ID=$(az network profile list --resource-group $RES_GROUP --query [0].id --output tsv)

# Delete the network profile
az network profile delete --id $NETWORK_PROFILE_ID -y

# Delete virtual network
az network vnet delete --resource-group $RES_GROUP --name aci-vnet
```

## <a name="next-steps"></a>További lépések

Ha új virtuális hálózatot, alhálózatot, hálózati profilt és tároló csoportot szeretne üzembe helyezni Resource Manager-sablonnal, [tekintse meg az Azure Container Group](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
)létrehozása a VNet használatával című témakört.

Ebben a cikkben több virtuális hálózati erőforrást és szolgáltatást is tárgyaltak, de röviden. Az Azure Virtual Network dokumentációja részletesen ismerteti ezeket a témaköröket:

* [Virtuális hálózat](../virtual-network/manage-virtual-network.md)
* [Alhálózat](../virtual-network/virtual-network-manage-subnet.md)
* [Szolgáltatásvégpontok](../virtual-network/virtual-network-service-endpoints-overview.md)
* [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md)
* [ExpressRoute](../expressroute/expressroute-introduction.md)

<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-vnet/aci-vnet-01.png

<!-- LINKS - External -->
[aci-helloworld]: https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-network-vnet-create]: /cli/azure/network/vnet#az-network-vnet-create
[az-network-profile-list]: /cli/azure/network/profile#az-network-profile-list
