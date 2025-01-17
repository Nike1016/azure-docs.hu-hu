---
title: Az elhelyezési csoportok használatának előzetes verziója Linux rendszerű virtuális gépekhez | Microsoft Docs
description: További információ az Azure-beli linuxos virtuális gépekhez elérhető közelségi csoportok létrehozásáról és használatáról.
services: virtual-machines-linux
author: cynthn
manager: jeconnoc
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/01/2019
ms.author: cynthn
ms.openlocfilehash: a3009c9863d8322e3fe34bd99d64b93f0aa3d858
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385728"
---
# <a name="preview-deploy-vms-to-proximity-placement-groups-using-azure-cli"></a>Előzetes verzió: Virtuális gépek üzembe helyezése a közelségi csoportokba az Azure CLI használatával

Ha a lehető legközelebb szeretné lekérni a virtuális gépeket, a lehető legkevesebb késést [](co-location.md#preview-proximity-placement-groups)kell megvalósítania, egy közelségi elhelyezési csoporton belül kell telepítenie.

A közelségi elhelyezési csoport olyan logikai csoport, amely biztosítja, hogy az Azure számítási erőforrásai fizikailag közel legyenek egymáshoz. A közelségi csoportok olyan munkaterhelések esetén hasznosak, ahol az alacsony késés követelmény.

> [!IMPORTANT]
> A közelségi elhelyezési csoportok jelenleg nyilvános előzetes verzióban érhetők el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> A közelségi elhelyezési csoportok ezekben a régiókban nem érhetők el az előzetes verzió alatt: **Kelet-japán**, **Kelet-Ausztrália** és **Közép-India**.

## <a name="create-the-proximity-placement-group"></a>A közelségi elhelyezési csoport létrehozása
Hozzon létre egy közelségi elhelyezési csoportot a használatával [`az ppg create`](/cli/azure/ppg#az-ppg-create). 

```azurecli-interactive
az group create --name myPPGGroup --location westus
az ppg create \
   -n myPPG \
   -g myPPGGroup \
   -l westus \
   -t standard 
```

## <a name="list-proximity-placement-groups"></a>Proximity elhelyezési csoportok listázása

Az [az PPG List](/cli/azure/ppg#az-ppg-list)paranccsal listázhatja az összes közelségi elhelyezési csoportot.

```azurecli-interactive
az ppg list -o table
```

## <a name="create-a-vm"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális gépet a közelségi elhelyezési csoporton belül az [új az VM](/cli/azure/vm#az-vm-create)használatával.
```azurecli-interactive
az vm create \
   -n myVM \
   -g myPPGGroup \
   --image UbuntuLTS \
   --ppg myPPG  \
   --generate-ssh-keys \
   --size Standard_D1_v2  \
   -l westus
```

A virtuális gépet az [az PPG show](/cli/azure/ppg#az-ppg-show)paranccsal tekintheti meg a közelségi csoportban.

```azurecli-interactive
az ppg show --name myppg --resource-group myppggroup --query "virtualMachines"
```

## <a name="availability-sets"></a>Rendelkezésre állási csoportok
A közelségi elhelyezési csoportban is létrehozhat egy rendelkezésre állási csoportot. Használja ugyanazt `--ppg` a paramétert az [az VM rendelkezésre állása-set Create](/cli/azure/vm/availability-set#az-vm-availability-set-create) paranccsal, és hozzon létre egy rendelkezésre állási csoportot, és a rendelkezésre állási csoportban lévő összes virtuális gép is ugyanabban a közelségi elhelyezési csoportban lesz létrehozva.

## <a name="scale-sets"></a>Méretezési csoportok

Létrehozhat egy méretezési csoportot is a közelségi elhelyezési csoportban. Használja ugyanazt `--ppg` a paramétert az az [vmss Create](/cli/azure/vmss?view=azure-cli-latest#az-vmss-create) paranccsal egy méretezési csoport létrehozásához, és az összes példány ugyanabban a közelségi elhelyezési csoportban lesz létrehozva.

## <a name="next-steps"></a>További lépések

További információ az [Azure CLI](/cli/azure/ppg) -parancsokról a Proximity elhelyezési csoportokhoz.
