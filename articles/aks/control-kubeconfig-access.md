---
title: A kubeconfig való hozzáférés korlátozása az Azure Kubernetes szolgáltatásban (ak)
description: Útmutató a Kubernetes konfigurációs fájlhoz (kubeconfig) való hozzáférés vezérléséhez a fürt rendszergazdái és a fürt felhasználói számára
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: mlearned
ms.openlocfilehash: cbc653b86ed83f9d6a7348d39f51dc7cd49c6892
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/12/2019
ms.locfileid: "67615675"
---
# <a name="use-azure-role-based-access-controls-to-define-access-to-the-kubernetes-configuration-file-in-azure-kubernetes-service-aks"></a>Az Azure szerepköralapú hozzáférés-vezérlés használata a Kubernetes konfigurációs fájlhoz való hozzáférés definiálásához az Azure Kubernetes szolgáltatásban (ak)

A Kubernetes-fürtöket az `kubectl` eszköz használatával lehet kezelni. Az Azure CLI-vel egyszerűen lekérheti a hozzáférési hitelesítő adatokat és a konfigurációs információkat, hogy a használatával `kubectl`csatlakozzanak az AK-fürtökhöz. Az Azure szerepköralapú hozzáférés-vezérlés (RBAC) használatával korlátozhatja, hogy ki érheti el az Kubernetes-konfiguráció (*kubeconfig*) adatait, és korlátozhatja a rájuk vonatkozó engedélyeket.

Ebből a cikkből megtudhatja, hogyan rendelhet hozzá olyan RBAC-szerepköröket, amelyek korlátozzák, hogy ki szerezhet be egy AK-fürt konfigurációs adatait.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy rendelkezik egy meglévő AK-fürttel. Ha AK-fürtre van szüksége, tekintse meg az AK gyors üzembe helyezését [Az Azure CLI használatával][aks-quickstart-cli] vagy [a Azure Portal használatával][aks-quickstart-portal].

Ehhez a cikkhez az Azure CLI 2.0.65 vagy újabb verzióját is futtatnia kell. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="available-cluster-roles-permissions"></a>Elérhető fürt szerepköreinek engedélyei

Ha az `kubectl` eszköz használatával használ egy AK-fürtöt, a rendszer egy konfigurációs fájlt használ, amely a fürt kapcsolati adatait határozza meg. Ezt a konfigurációs fájlt általában a *~/.Kube/config*tárolja. Ebben a *kubeconfig* -fájlban több fürt is definiálható. A fürtök közötti váltáshoz [használja a kubectl config use-Context][kubectl-config-use-context] parancsot.

Az az [AK Get-hitelesítőadats][az-aks-get-credentials] parancs lehetővé teszi egy AK-fürt hozzáférési hitelesítő adatainak beszerzését, és egyesíti őket a *kubeconfig* -fájlba. Az Azure szerepköralapú hozzáférés-vezérlés (RBAC) használatával szabályozhatja a hitelesítő adatokhoz való hozzáférést. Ezek az Azure RBAC-szerepkörök lehetővé teszik a *kubeconfig* -fájl beolvasását, valamint a fürtön belüli engedélyek megadását.

A két beépített szerepkör a következők:

* **Azure Kubernetes Service-fürt rendszergazdai szerepköre**  
    * Engedélyezi a hozzáférést a *Microsoft. tárolószolgáltatás/managedClusters/listClusterAdminCredential/Action* API-híváshoz. Ez az API-hívás [felsorolja a fürt rendszergazdai hitelesítő adatait][api-cluster-admin].
    * A *clusterAdmin* szerepkör *kubeconfig* letöltése.
* **Azure Kubernetes Service-fürt felhasználói szerepköre**
    * Engedélyezi a hozzáférést a *Microsoft. tárolószolgáltatás/managedClusters/listClusterUserCredential/Action* API-híváshoz. Ez az API-hívás [a fürt felhasználói hitelesítő adatait listázza][api-cluster-user].
    * A *clusterUser* szerepkör *kubeconfig* letöltése.

Ezeket a RBAC szerepköröket Azure Active Directory (AD) felhasználóra vagy csoportra lehet alkalmazni.

## <a name="assign-role-permissions-to-a-user-or-group"></a>Szerepkör-engedélyek társítása felhasználóhoz vagy csoporthoz

Az elérhető szerepkörök egyikének hozzárendeléséhez le kell kérnie az AK-fürt erőforrás-AZONOSÍTÓját és az Azure AD felhasználói fiók vagy csoport AZONOSÍTÓját. A következő példa parancsai:

* Szerezze be a fürterőforrás-azonosítót az az *myAKSCluster* nevű fürthöz tartozó *myResourceGroup* -erőforráscsoport, az az az [AK show][az-aks-show] parancs használatával. Szükség szerint adja meg a fürt és az erőforráscsoport nevét.
* Az az [Account show][az-account-show] és az [ad User show][az-ad-user-show] PARANCSOKAT használja a felhasználói azonosító lekéréséhez.
* Végül pedig az az [role hozzárendelés Create][az-role-assignment-create] paranccsal rendel hozzá egy szerepkört.

Az alábbi példa az *Azure Kubernetes Service-fürt rendszergazdai szerepkörét* rendeli hozzá egy egyéni felhasználói fiókhoz:

```azurecli-interactive
# Get the resource ID of your AKS cluster
AKS_CLUSTER=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query id -o tsv)

# Get the account credentials for the logged in user
ACCOUNT_UPN=$(az account show --query user.name -o tsv)
ACCOUNT_ID=$(az ad user show --upn-or-object-id $ACCOUNT_UPN --query objectId -o tsv)

# Assign the 'Cluster Admin' role to the user
az role assignment create \
    --assignee $ACCOUNT_ID \
    --scope $AKS_CLUSTER \
    --role "Azure Kubernetes Service Cluster Admin Role"
```

> [!TIP]
> Ha engedélyeket szeretne hozzárendelni egy Azure ad-csoporthoz, frissítse az `--assignee` előző példában látható paramétert a *csoport* objektum-azonosítójával, nem pedig felhasználóként. Egy csoport objektumazonosítóának beszerzéséhez használja az az [ad Group show][az-ad-group-show] parancsot. A következő példa a *appdev*nevű Azure ad-csoport OBJEKTUMAZONOSÍTÓ-azonosítóját kapja:`az ad group show --group appdev --query objectId -o tsv`

Szükség szerint módosíthatja az előző hozzárendelést a *fürt felhasználói szerepköréhez* .

A következő példa kimenete a szerepkör-hozzárendelés sikeres létrehozását mutatja be:

```
{
  "canDelegate": null,
  "id": "/subscriptions/<guid>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/providers/Microsoft.Authorization/roleAssignments/b2712174-5a41-4ecb-82c5-12b8ad43d4fb",
  "name": "b2712174-5a41-4ecb-82c5-12b8ad43d4fb",
  "principalId": "946016dd-9362-4183-b17d-4c416d1f8f61",
  "resourceGroup": "myResourceGroup",
  "roleDefinitionId": "/subscriptions/<guid>/providers/Microsoft.Authorization/roleDefinitions/0ab01a8-8aac-4efd-b8c2-3ee1fb270be8",
  "scope": "/subscriptions/<guid>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="get-and-verify-the-configuration-information"></a>A konfigurációs adatok beolvasása és ellenőrzése

A hozzárendelt RBAC-szerepkörökkel az az [AK Get-hitelesítőadats][az-aks-get-credentials] paranccsal kérheti le a *kubeconfig* -definíciót az AK-fürthöz. A következő példa lekéri a *--Admin* hitelesítő adatokat, amelyek megfelelően működnek, ha a felhasználó megkapta a *fürt rendszergazdai szerepkörét*:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Ezután a [kubectl config View][kubectl-config-view] paranccsal ellenőrizheti, hogy a fürt környezete azt mutatja-e, hogy a rendszergazda konfigurációs adatai lettek alkalmazva:

```
$ kubectl config view

apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://myaksclust-myresourcegroup-19da35-4839be06.hcp.eastus.azmk8s.io:443
  name: myAKSCluster
contexts:
- context:
    cluster: myAKSCluster
    user: clusterAdmin_myResourceGroup_myAKSCluster
  name: myAKSCluster-admin
current-context: myAKSCluster-admin
kind: Config
preferences: {}
users:
- name: clusterAdmin_myResourceGroup_myAKSCluster
  user:
    client-certificate-data: REDACTED
    client-key-data: REDACTED
    token: e9f2f819a4496538b02cefff94e61d35
```

## <a name="remove-role-permissions"></a>Szerepkör-engedélyek eltávolítása

A szerepkör-hozzárendelések eltávolításához használja az az [role hozzárendelés delete][az-role-assignment-delete] parancsot. Az előző parancsokban leírtak szerint határozza meg a fiók AZONOSÍTÓját és a fürterőforrás AZONOSÍTÓját. Ha a szerepkört felhasználó helyett egy csoporthoz rendelte hozzá, akkor a következő `--assignee` paraméterhez nem kell megadnia a megfelelő csoportazonosító-azonosítót:

```azurecli-interactive
az role assignment delete --assignee $ACCOUNT_ID --scope $AKS_CLUSTER
```

## <a name="next-steps"></a>További lépések

Az AK-fürtökhöz való hozzáférés fokozott biztonsága érdekében [integrálja Azure Active Directory hitelesítést][aad-integration].

<!-- LINKS - external -->
[kubectl-config-use-context]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#config
[kubectl-config-view]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#config

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-rbac]: ../role-based-access-control/overview.md
[api-cluster-admin]: /rest/api/aks/managedclusters/listclusteradmincredentials
[api-cluster-user]: /rest/api/aks/managedclusters/listclusterusercredentials
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-account-show]: /cli/azure/account#az-account-show
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-role-assignment-delete]: /cli/azure/role/assignment#az-role-assignment-delete
[aad-integration]: azure-ad-integration.md
[az-ad-group-show]: /cli/azure/ad/group#az-ad-group-show
