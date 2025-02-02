---
title: Azure Kubernetes hálózati szabályzatok | Microsoft Docs
description: További tudnivalók a Kubernetes hálózati házirendjeiről a Kubernetes-fürt biztonságossá tételéhez.
services: virtual-network
documentationcenter: na
author: aanandr
manager: NarayanAnnamalai
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 9/25/2018
ms.author: aanandr
ms.custom: ''
ms.openlocfilehash: ff6fd45e0a68a3e93e4c62eb31a566a6dffa2344
ms.sourcegitcommit: 5604661655840c428045eb837fb8704dca811da0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2019
ms.locfileid: "68494950"
---
# <a name="azure-kubernetes-network-policies-overview"></a>Az Azure Kubernetes hálózati házirendjeinek áttekintése

A hálózati házirendek a hüvelyek számára biztosítanak olyan mikro-szegmentálást, mint a hálózati biztonsági csoportok (NSG-EK) a virtuális gépek számára. Az Azure hálózati házirend implementációja támogatja a szabványos Kubernetes hálózati házirend-specifikációt. A címkék használatával kiválaszthatja a hüvelyek egy csoportját, és meghatározhatja a bejövő és kimenő forgalomra vonatkozó szabályok listáját, amely meghatározza, hogy milyen típusú forgalmat engedélyeznek a hüvelyek. További információ a Kubernetes hálózati házirendjeiről a [Kubernetes dokumentációjában](https://kubernetes.io/docs/concepts/services-networking/network-policies/).

![A Kubernetes hálózati házirendjeinek áttekintése](./media/kubernetes-network-policies/kubernetes-network-policies-overview.png)

Az Azure hálózati házirendjei együtt működnek az Azure CNI, amely VNet-integrációt biztosít a tárolók számára. Jelenleg csak Linux-csomópontokon támogatott. A implementálja a Linux IP-táblázat szabályait a megadott házirendek alapján, hogy kikényszerítse a forgalom szűrését.

## <a name="planning-security-for-your-kubernetes-cluster"></a>A Kubernetes-fürt biztonságának megtervezése
A fürt biztonságának megvalósításakor használjon hálózati biztonsági csoportokat (NSG-ket) az észak-déli forgalom szűrésére, azaz a fürt alhálózatára beérkező és onnan kilépő forgalomra, valamint Kubernetes hálózati házirendeket használ a kelet-nyugati forgalomhoz, azaz a hüvelyek közötti adatforgalomhoz a fürt.

## <a name="using-azure-kubernetes-network-policies"></a>Az Azure Kubernetes hálózati házirendjeinek használata
Az Azure hálózati házirendjei a következő módokon használhatók a hüvelyek mikro-szegmentálásának biztosítására.

### <a name="acs-engine"></a>ACS-motor
Az ACS-Engine egy olyan eszköz, amely Azure Resource Manager sablont hoz létre egy Kubernetes-fürt Azure-beli üzembe helyezéséhez. A fürtkonfiguráció egy JSON-fájlban van meghatározva, amelyet a sablon létrehozásakor a rendszer továbbít az eszköznek. Ha többet szeretne megtudni a fürt támogatott beállításainak teljes listájáról és azok leírásáról, tekintse meg a Microsoft Azure Container Service Engine-cluster definition című témakört.

Ha az ACS-Engine használatával telepített fürtökön szeretné engedélyezni a házirendeket, akkor a fürt definíciós fájljában válassza a networkPolicy beállítás értékét az "Azure" értékre.

#### <a name="example-configuration"></a>Konfigurációs példa

Az alábbi JSON-példa egy új virtuális hálózatot és alhálózatot hoz létre, és üzembe helyez egy Kubernetes-fürtöt az Azure CNI. Javasoljuk, hogy a JSON-fájl szerkesztéséhez használja a "Jegyzettömb" kifejezést. 
```json
{
  "apiVersion": "vlabs",
  "properties": {
    "orchestratorProfile": {
      "orchestratorType": "Kubernetes",
      “kubernetesConfig”: {
         "networkPolicy": "azure"
       }
    },
    "masterProfile": {
      "count": 1,
      "dnsPrefix": "<specify a cluster name>",
      "vmSize": "Standard_D2s_v3"
    },
    "agentPoolProfiles": [
      {
        "name": "agentpool",
        "count": 2,
        "vmSize": "Standard_D2s_v3",
        "availabilityProfile": "AvailabilitySet"
      }
    ],
   "linuxProfile": {
      "adminUsername": "<specify admin username>",
      "ssh": {
        "publicKeys": [
          {
            "keyData": "<cut and paste your ssh key here>"
          }
        ]
      }
    },
    "servicePrincipalProfile": {
      "clientId": "<enter the client ID of your service principal here >",
      "secret": "<enter the password of your service principal here>"
    }
  }
}

```
### <a name="creating-your-own-kubernetes-cluster-in-azure"></a>Saját Kubernetes-fürt létrehozása az Azure-ban
A megvalósítás használatával hálózati házirendeket biztosíthat a hüvelyek számára a saját maga által üzembe helyezett Kubernetes-fürtökön anélkül, hogy olyan eszközöket kellene használnia, mint például az ACS-motor. Ebben az esetben először telepítenie kell a CNI beépülő modult, és engedélyeznie kell azt a fürt összes virtuális gépén. Részletes információ: [A beépülő modul üzembe helyezése saját kezűleg üzembe helyezett Kubernetes-fürthöz](deploy-container-networking.md#deploy-plug-in-for-a-kubernetes-cluster).

Miután telepítette a fürtöt, futtassa a `kubectl` következő parancsot az Azure hálózati házirend *daemonset elemet* a fürtre való letöltéséhez és alkalmazásához.

  ```
  kubectl apply -f https://raw.githubusercontent.com/Azure/acs-engine/master/parts/k8s/addons/kubernetesmasteraddons-azure-npm-daemonset.yaml

  ```
A megoldás szintén nyílt forráskódú, és a kód elérhető az [Azure Container Networking adattárában](https://github.com/Azure/azure-container-networking/tree/master/npm).



## <a name="next-steps"></a>További lépések
- További tudnivalók az [Azure Kubernetes szolgáltatásról](../aks/intro-kubernetes.md).
-  A [tároló hálózatkezelésének](container-networking-overview.md)megismerése.
- [Telepítse a beépülő](deploy-container-networking.md) modult a Kubernetes-fürtök vagy a Docker-tárolók számára.
