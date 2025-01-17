---
title: Linux-csomópontok frissítése és újraindítása az Azure Kubernetes Service (kured) szolgáltatásban (ak)
description: Ismerje meg, hogyan frissítheti a Linux-csomópontokat, és hogyan végezheti el automatikusan a kured az Azure Kubernetes szolgáltatásban (ak)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 02/28/2019
ms.author: mlearned
ms.openlocfilehash: 580d1316c2bfc6514a148ed6fba78a8e77bd880e
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "67614905"
---
# <a name="apply-security-and-kernel-updates-to-linux-nodes-in-azure-kubernetes-service-aks"></a>Biztonsági és kernel-frissítések alkalmazása Linux-csomópontokra az Azure Kubernetes szolgáltatásban (ak)

A fürtök védelme érdekében a rendszer automatikusan alkalmazza a biztonsági frissítéseket az AK-ban található Linux-csomópontokra. Ezek a frissítések operációsrendszer-biztonsági javításokat vagy kernel-frissítéseket tartalmaznak. Ezeknek a frissítéseknek a végrehajtásához a csomópont újraindítása szükséges a folyamat befejezéséhez. Az AK nem újraindítja automatikusan ezeket a Linux-csomópontokat a frissítési folyamat befejezéséhez.

A Windows Server-csomópontok (jelenleg előzetes verzióban) naprakészen tartása egy kicsit más. A Windows Server-csomópontok nem kapnak napi frissítést. Ehelyett olyan AK-frissítést fog végrehajtani, amely új csomópontokat helyez üzembe a legújabb alapszintű ablak-kiszolgáló lemezképével és javításával. A Windows Server-csomópontokat használó AK-fürtök esetében lásd: [csomópont-készlet frissítése az AK-ban][nodepool-upgrade].

Ez a cikk bemutatja, hogyan használhatja a nyílt forráskódú [kured (KUbernetes reboot Daemon)][kured] az újraindítást igénylő linuxos csomópontok megtekintésére, majd automatikusan kezeli a futó hüvelyek és a csomópont-újraindítási folyamat átütemezését.

> [!NOTE]
> `Kured`a egy nyílt forráskódú projekt a Weaveworks. Ennek a projektnek az AK-ban való támogatását a legjobb megoldási szinten biztosítjuk. További támogatás a #weave-Community Slack Channel-ben érhető el.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy rendelkezik egy meglévő AK-fürttel. Ha AK-fürtre van szüksége, tekintse meg az AK gyors üzembe helyezését [Az Azure CLI használatával][aks-quickstart-cli] vagy [a Azure Portal használatával][aks-quickstart-portal].

Szüksége lesz az Azure CLI 2.0.59 vagy újabb verziójára is, valamint a telepítésre és konfigurálásra. A `az --version` verzió megkereséséhez futtassa a parancsot. Ha telepíteni vagy frissíteni szeretne, tekintse meg az [Azure CLI telepítését][install-azure-cli]ismertető témakört.

## <a name="understand-the-aks-node-update-experience"></a>Az AK-csomópont frissítési felületének megismerése

Egy AK-fürtben a Kubernetes-csomópontok Azure-beli virtuális gépekként futnak. Ezek a Linux-alapú virtuális gépek Ubuntu-rendszerképet használnak, és az operációs rendszer úgy van konfigurálva, hogy minden este automatikusan keressen frissítéseket. Ha biztonsági vagy kernel-frissítések érhetők el, a rendszer automatikusan letölti és telepíti őket.

![AK-csomópont frissítési és újraindítási folyamata a kured](media/node-updates-kured/node-reboot-process.png)

Bizonyos biztonsági frissítések, például a kernel frissítései a folyamat véglegesítéséhez a csomópont újraindítását igénylik. Egy olyan Linux-csomópont, amely újraindítást igényel, létrehoz egy */var/Run/reboot-Required*nevű fájlt. Ez az újraindítási folyamat nem automatikusan történik.

Saját munkafolyamatokat és folyamatokat használhat a csomópont-újraindítások kezeléséhez, vagy `kured` a folyamat előkészítéséhez. A-ben egy olyan daemonset elemet van telepítve, amely a fürt minden Linux-csomópontján egy Pod-t futtat. [][DaemonSet] `kured` Ezek a hüvelyek a Daemonset elemet figyelik a */var/Run/reboot-Required* -fájl létezését, majd kezdeményeznek egy folyamatot a csomópontok újraindításához.

### <a name="node-upgrades"></a>Csomópontok frissítése

Az AK-ban egy további folyamat van, amely lehetővé teszi a fürt frissítését. A frissítés általában a Kubernetes egy újabb verziójára kerül, és nem csak a csomópontok biztonsági frissítéseire vonatkozik. Az ak-frissítés a következő műveleteket hajtja végre:

* Egy új csomópont van telepítve a legújabb biztonsági frissítésekkel és Kubernetes-verzióval.
* A régi csomópontok le vannak szigetelve és ki vannak ürítve.
* A hüvelyek ütemezése az új csomóponton történik.
* A régi csomópont törölve lett.

A frissítési esemény során nem maradhat ugyanazon a Kubernetes-verzión. Meg kell adnia a Kubernetes újabb verzióját. A Kubernetes legújabb verziójára való frissítéshez frissítheti az [AK][aks-upgrade]-fürtöt.

## <a name="deploy-kured-in-an-aks-cluster"></a>Kured üzembe helyezése AK-fürtben

A `kured` daemonset elemet üzembe helyezéséhez alkalmazza a következő minta YAML-jegyzéket a GitHub-projekt oldaláról. Ez a jegyzékfájl létrehoz egy szerepkör-és fürt-szerepkört, kötéseket és egy szolgáltatásfiókot, majd központilag `kured` telepíti a daemonset elemet-et, amely az 1,9-es vagy újabb AK-fürtöket támogatja.

```console
kubectl apply -f https://github.com/weaveworks/kured/releases/download/1.2.0/kured-1.2.0-dockerhub.yaml
```

További paramétereket `kured`is konfigurálhat, például a Prometheus vagy a Slack integrációját. További információ a további konfigurációs paraméterekről: [kured telepítési docs][kured-install].

## <a name="update-cluster-nodes"></a>Fürtcsomópontok frissítése

Alapértelmezés szerint az AK-ban található Linux-csomópontok minden este frissítéseket keresnek. Ha nem szeretné megvárni, manuálisan is végrehajthat egy frissítést, hogy ellenőrizze, `kured` megfelelően fut-e. Először hajtsa végre az [SSH-t az egyik AK-csomóponton][aks-ssh]. Ha már rendelkezik SSH-kapcsolatban a Linux-csomóponttal, keressen frissítéseket, és alkalmazza őket a következőképpen:

```console
sudo apt-get update && sudo apt-get upgrade -y
```

Ha olyan frissítések lettek alkalmazva, amelyek a csomópont újraindítását igénylik, a rendszer egy fájlt ír a */var/Run/reboot-Required*. `Kured`ellenőrzi azokat a csomópontokat, amelyek alapértelmezés szerint 60 percenként újraindítást igényelnek.

## <a name="monitor-and-review-reboot-process"></a>Az újraindítási folyamat figyelése és áttekintése

Ha a Daemonset elemet egyik replikája azt észlelte, hogy a csomópontok újraindítására van szükség, a Kubernetes API-n keresztül egy zárolás kerül a csomópontra. Ez a zárolás megakadályozza, hogy további hüvelyek legyenek ütemezve a csomóponton. A zárolás azt is jelzi, hogy egyszerre csak egy csomópontot kell újraindítani. Ha a csomópont ki van kapcsolva, a futó hüvelyek el lesznek ürítve a csomópontból, és a csomópont újraindul.

A csomópontok állapotának figyeléséhez használja a [kubectl Get Nodes][kubectl-get-nodes] parancsot. A következő példa kimenete egy *SchedulingDisabled* állapotú csomópontot mutat be, amely a csomópont előkészíti az újraindítási folyamatot:

```
NAME                       STATUS                     ROLES     AGE       VERSION
aks-nodepool1-28993262-0   Ready,SchedulingDisabled   agent     1h        v1.11.7
```

A frissítési folyamat befejeződése után megtekintheti a csomópontok állapotát a [kubectl Get Nodes][kubectl-get-nodes] paranccsal a `--output wide` (z) paraméterrel. Ez a kiegészítő kimenet a mögöttes csomópontok *kernel-verziójában* megjelenő különbségeket is megjeleníti, ahogy az a következő példában látható. Az *AK-nodepool1-28993262-0* egy korábbi lépésben lett frissítve, és a *4.15.0-1039-Azure kernel-* verziót mutatja. A nem frissített Node *-nodepool1-28993262-1* csomópont a *4.15.0-1037-Azure*kernel-verziót jeleníti meg.

```
NAME                       STATUS    ROLES     AGE       VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-28993262-0   Ready     agent     1h        v1.11.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS   4.15.0-1039-azure   docker://3.0.4
aks-nodepool1-28993262-1   Ready     agent     1h        v1.11.7   10.240.0.5    <none>        Ubuntu 16.04.6 LTS   4.15.0-1037-azure   docker://3.0.4
```

## <a name="next-steps"></a>További lépések

Ez a cikk részletesen ismerteti `kured` , hogyan használható a Linux-csomópontok automatikus újraindítása a biztonsági frissítési folyamat részeként. A Kubernetes legújabb verziójára való frissítéshez frissítheti az [AK][aks-upgrade]-fürtöt.

A Windows Server-csomópontokat használó AK-fürtök esetében lásd: [csomópont-készlet frissítése az AK-ban][nodepool-upgrade].

<!-- LINKS - external -->
[kured]: https://github.com/weaveworks/kured
[kured-install]: https://github.com/weaveworks/kured#installation
[kubectl-get-nodes]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[DaemonSet]: concepts-clusters-workloads.md#statefulsets-and-daemonsets
[aks-ssh]: ssh.md
[aks-upgrade]: upgrade-cluster.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
