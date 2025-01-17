---
title: Fájlok kötetének dinamikus létrehozása több hüvely számára az Azure Kubernetes szolgáltatásban (ak)
description: Megtudhatja, hogyan hozhat létre dinamikusan állandó kötetet Azure Files használatával több párhuzamos hüvelyrel való használatra az Azure Kubernetes szolgáltatásban (ak)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 07/08/2019
ms.author: mlearned
ms.openlocfilehash: 580363973afd918351931edfb187a1a8d38d6985
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2019
ms.locfileid: "67665974"
---
# <a name="dynamically-create-and-use-a-persistent-volume-with-azure-files-in-azure-kubernetes-service-aks"></a>Állandó kötet létrehozása és használata Azure Files az Azure Kubernetes szolgáltatásban (ak)

Az állandó kötet a Kubernetes hüvelyekkel való használatra kiépített tárterületet jelöli. Egy állandó kötetet egy vagy több hüvely is használhat, és dinamikusan vagy statikusan kiépíthető. Ha több hüvelynek egyidejű hozzáférésre van szüksége ugyanahhoz a tárolási kötethez, akkor a Azure Files használatával csatlakozhat a [Server Message Block (SMB) protokollal][smb-overview]. Ebből a cikkből megtudhatja, hogyan hozhat létre dinamikusan egy Azure Files-megosztást egy Azure Kubernetes-szolgáltatási (ak-) fürtön több hüvely használatával.

A Kubernetes-kötetekkel kapcsolatos további információkért lásd: az [AK-beli alkalmazások tárolási beállításai][concepts-storage].

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy rendelkezik egy meglévő AK-fürttel. Ha AK-fürtre van szüksége, tekintse meg az AK gyors üzembe helyezését [Az Azure CLI használatával][aks-quickstart-cli] vagy [a Azure Portal használatával][aks-quickstart-portal].

Szüksége lesz az Azure CLI 2.0.59 vagy újabb verziójára is, valamint a telepítésre és konfigurálásra. A `az --version` verzió megkereséséhez futtassa a parancsot. Ha telepíteni vagy frissíteni szeretne, tekintse meg az [Azure CLI telepítését][install-azure-cli]ismertető témakört.

## <a name="create-a-storage-class"></a>Tárolási osztály létrehozása

A tárolási osztály segítségével határozható meg az Azure-fájlmegosztás létrehozása. A rendszer automatikusan létrehoz egy Storage-fiókot a [csomópont-erőforráscsoport][node-resource-group] számára, hogy az Azure-fájlmegosztás tárolására használja a Storage osztályt. Válasszon a következő [Azure Storage][storage-skus] -redundancia *skuName*:

* *Standard_LRS* – standard, helyileg redundáns tárolás (LRS)
* *Standard_GRS* – szabványos geo-redundáns tárolás (GRS)
* *Standard_RAGRS* – szabványos olvasási hozzáférésű geo-redundáns tárolás (ra-GRS)

> [!NOTE]
> Azure Files támogatja a Premium Storage-t a Kubernetes 1,13-es vagy újabb verzióját futtató AK-fürtökben.

A Azure Files Kubernetes tárolási osztályaival kapcsolatos további információkért lásd: [Kubernetes tárolási][kubernetes-storage-classes]osztályok.

Hozzon létre egy `azure-file-sc.yaml` nevű fájlt, és másolja a következő példában szereplő jegyzékfájlba. További információ a *mountOptions*: [csatlakoztatási beállítások][mount-options] szakasz.

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
parameters:
  skuName: Standard_LRS
```

Hozza létre a tárolási osztályt a [kubectl Apply][kubectl-apply] paranccsal:

```console
kubectl apply -f azure-file-sc.yaml
```

## <a name="create-a-cluster-role-and-binding"></a>Fürt szerepkörének és kötésének létrehozása

Az AK-fürtök Kubernetes szerepköralapú hozzáférés-vezérlést (RBAC) használnak az elvégezhető műveletek korlátozására. A *szerepkörök* határozzák meg a megadható engedélyeket, a *kötések* pedig a kívánt felhasználókra vonatkoznak. Ezek a hozzárendelések egy adott névtérre vagy a teljes fürtre is alkalmazhatók. További információ: RBAC- [hitelesítés használata][kubernetes-rbac].

Annak engedélyezéséhez, hogy az Azure platform létrehozza a szükséges tárolási erőforrásokat, hozzon létre egy *ClusterRole* és egy *ClusterRoleBinding*. Hozzon létre egy `azure-pvc-roles.yaml` nevű fájlt, és másolja a következő YAML:

```yaml
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: system:azure-cloud-provider
rules:
- apiGroups: ['']
  resources: ['secrets']
  verbs:     ['get','create']
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: system:azure-cloud-provider
roleRef:
  kind: ClusterRole
  apiGroup: rbac.authorization.k8s.io
  name: system:azure-cloud-provider
subjects:
- kind: ServiceAccount
  name: persistent-volume-binder
  namespace: kube-system
```

Rendelje hozzá az engedélyeket a [kubectl Apply][kubectl-apply] paranccsal:

```console
kubectl apply -f azure-pvc-roles.yaml
```

## <a name="create-a-persistent-volume-claim"></a>Állandó kötet jogcímek létrehozása

Az állandó mennyiségi jogcím (PVC) a tárolási osztály objektum használatával dinamikusan kiépít egy Azure-fájlmegosztást. A következő YAML a *ReadWriteMany* -hozzáféréssel rendelkező állandó mennyiségi jogcím ( *5 GB* méretű) létrehozásához használható. A hozzáférési módokkal kapcsolatos további információkért tekintse meg a [Kubernetes állandó kötet][access-modes] dokumentációját.

Most hozzon létre egy `azure-file-pvc.yaml` nevű fájlt, és másolja a következő YAML. Győződjön meg arról, hogy a *storageClassName* megegyezik az utolsó lépésben létrehozott tárolási osztállyal:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azurefile
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: azurefile
  resources:
    requests:
      storage: 5Gi
```

Hozza létre az állandó kötet jogcímet a [kubectl Apply][kubectl-apply] paranccsal:

```console
kubectl apply -f azure-file-pvc.yaml
```

Ha elkészült, a rendszer létrehozza a fájlmegosztást. Létrejön egy titkos Kubernetes is, amely tartalmazza a kapcsolatok adatait és a hitelesítő adatokat. A PVC állapotának megtekintéséhez használja a [kubectl Get][kubectl-get] parancsot:

```console
$ kubectl get pvc azurefile

NAME        STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
azurefile   Bound     pvc-8436e62e-a0d9-11e5-8521-5a8664dc0477   5Gi        RWX            azurefile      5m
```

## <a name="use-the-persistent-volume"></a>Az állandó kötet használata

A következő YAML létrehoz egy Pod-t, amely az állandó mennyiségi jogcímek *azurefile* használatával csatlakoztatja az Azure-fájlmegosztást a */mnt/Azure* elérési úton. A Windows Server-tárolók esetében (jelenleg előzetes verzióban) a Windows PATH Convention, például a *'d: "* *mountPath* használatával adható meg.

Hozzon létre egy `azure-pvc-files.yaml`nevű fájlt, és másolja a következő YAML. Győződjön meg arról, hogy a *claimName* megegyezik az utolsó lépésben létrehozott PVC-vel.

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: nginx:1.15.5
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
    - mountPath: "/mnt/azure"
      name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azurefile
```

Hozza létre a pod-t a [kubectl Apply][kubectl-apply] paranccsal.

```console
kubectl apply -f azure-pvc-files.yaml
```

Most már rendelkezik egy futó Pod Azure Files-megosztással, amely a */mnt/Azure* könyvtárban van csatlakoztatva. Ez a konfiguráció megtekinthető a pod-on keresztül `kubectl describe pod mypod`történő vizsgálatakor. A következő összefoglalt példa kimenet a tárolóban csatlakoztatott kötetet mutatja:

```
Containers:
  mypod:
    Container ID:   docker://053bc9c0df72232d755aa040bfba8b533fa696b123876108dec400e364d2523e
    Image:          nginx:1.15.5
    Image ID:       docker-pullable://nginx@sha256:d85914d547a6c92faa39ce7058bd7529baacab7e0cd4255442b04577c4d1f424
    State:          Running
      Started:      Fri, 01 Mar 2019 23:56:16 +0000
    Ready:          True
    Mounts:
      /mnt/azure from volume (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-8rv4z (ro)
[...]
Volumes:
  volume:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  azurefile
    ReadOnly:   false
[...]
```

## <a name="mount-options"></a>Csatlakoztatási beállítások

Az alapértelmezett *fileMode* és *DirMode* értékek a Kubernetes-verziók között különböznek a következő táblázatban leírtak szerint.

| version | value |
| ---- | ---- |
| v1.6.x, v1.7.x | 0777 |
| v1.8.0-v1.8.5 | 0700 |
| v 1.8.6 vagy újabb | 0755 |
| v1.9.0 | 0700 |
| v 1.9.1 vagy újabb | 0755 |

Ha 1.8.5 vagy nagyobb verziójú fürtöt használ, és egy tárolási osztállyal dinamikusan létrehozza az állandó kötetet, a csatlakoztatási beállítások megadhatók a tárolási osztály objektumban. A következő példa a *0777*-es készletet állítja be:

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
parameters:
  skuName: Standard_LRS
```

Ha 1.8.0-1.8.4-alapú fürtöt használ, a *runAsUser* értékkel megadható egy biztonsági környezet, amely a *0*értékre van állítva. A pod biztonsági környezettel kapcsolatos további információkért lásd: [biztonsági környezet konfigurálása][kubernetes-security-context].

## <a name="next-steps"></a>További lépések

A kapcsolódó ajánlott eljárásokért lásd: [ajánlott eljárások a tároláshoz és a biztonsági mentéshez az AK-ban][operator-best-practices-storage].

További információ az állandó kötetek Kubernetes Azure Files használatával.

> [!div class="nextstepaction"]
> [Kubernetes beépülő modul a Azure Fileshoz][kubernetes-files]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/#azure-file
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[pv-static]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#static
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-list]: /cli/azure/group#az-group-list
[az-resource-show]: /cli/azure/aks#az-aks-show
[az-storage-account-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-key-list]: /cli/azure/storage/account/keys#az-storage-account-keys-list
[az-storage-share-create]: /cli/azure/storage/share#az-storage-share-create
[mount-options]: #mount-options
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[storage-skus]: ../storage/common/storage-redundancy.md
[kubernetes-rbac]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
[node-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks