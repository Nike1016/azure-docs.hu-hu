---
title: Azure CLI-példaszkript – Windows Server 2016 virtuális gép létrehozása hálózati terheléselosztással | Microsoft Docs
description: Azure CLI-példaszkript – Windows Server 2016 virtuális gép létrehozása hálózati terheléselosztással
services: virtual-machines-windows
documentationcenter: virtual-machines
author: rickstercdn
manager: gwallace
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/23/2017
ms.author: rclaus
ms.openlocfilehash: b1cbb48bc6dcfcaf20d4331b93ba61e57979692c
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67703253"
---
# <a name="load-balance-traffic-between-highly-available-virtual-machines"></a>Magas rendelkezésre állású virtuális gépek közötti forgalom terheléselosztása

A példaszkript minden összetevőt létrehoz, amelyre szükség van több, magas rendelkezésre állásra és terheléselosztásra konfigurált Ubuntu rendszerű virtuális gép futtatásához. A szkript futtatása után három virtuális géppel rendelkezik majd, amelyek egy Azure-beli rendelkezésre állási csoporthoz vannak csatlakoztatva, és egy Azure Load Balanceren keresztül érhetők el.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-nlb/create-windows-vm-nlb.sh "Quick Create VM")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

Az alábbi paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja egy erőforráscsoport, egy virtuális gép, egy rendelkezésre állási csoport, egy terheléselosztó és minden kapcsolódó erőforrás létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet) | Létrehoz egy Azure-beli virtuális hálózatot és alhálózatot. |
| [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip) | Létrehoz egy nyilvános IP-címet egy statikus IP-címmel és egy hozzárendelt DNS-névvel. |
| [az network lb create](https://docs.microsoft.com/cli/azure/network/lb) | Létrehoz egy Azure-beli hálózati terheléselosztót (NLB). |
| [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe) | Létrehoz egy NLB-vizsgálatot. Az NLB-vizsgálat az NLB-készletbe tartozó egyes virtuális gépeket monitorozza. Ha valamelyik virtuális gép elérhetetlenné válik, a terheléselosztó nem irányít rá forgalmat. |
| [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule) | Létrehoz egy NLB-szabályt. Ebben a példában egy, a 80-es portra vonatkozó szabály jön létre. A hálózati terheléselosztóra érkező HTTP-forgalom az NLB-csoportba tartozó egyik virtuális gép 80-as portjára lesz irányítva. |
| [az network lb inbound-nat-rule create](https://docs.microsoft.com/cli/azure/network/lb/inbound-nat-rule) | Létrehoz egy NLB hálózati címfordítási (Network Address Translation, NAT) szabályt.  A NAT-szabályok az NLB egyik portját hozzárendelik egy virtuális gép valamelyik portjához. Ebben a példában egy NAT-szabályt hozunk létre az SSH-forgalom az NLB-csoportba tartozó egyes virtuális gépekre történő irányítására.  |
| [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg) | Létrehoz egy hálózati biztonsági csoportot (NSG), amely biztonsági határként szolgál az internet és a virtuális gép között. |
| [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule) | Létrehoz egy NSG-szabályt a befelé irányuló forgalom engedélyezésére. Ebben a példában a 22-es portot nyitjuk meg az SSH-forgalom számára. |
| [az network nic create](https://docs.microsoft.com/cli/azure/network/nic) | Létrehoz egy virtuális hálózati kártyát, és csatlakoztatja a virtuális hálózathoz, az alhálózathoz és az NSG-hez. |
| [az vm availability-set create](https://docs.microsoft.com/cli/azure/network/lb/rule) | Létrehoz egy rendelkezésre állási csoportot. A rendelkezésre állási csoportok az alkalmazások rendelkezésre állását biztosítják a virtuális gépek fizikai erőforrások közötti elosztásával, hogy az esetlegesen fellépő hibák ne érintsék a teljes készletet. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm/availability-set) | Létrehozza a virtuális gépet, és csatlakoztatja a hálózati kártyához, a virtuális hálózathoz, az alhálózathoz és az NSG-hez. A parancs megadja továbbá a használandó virtuálisgép-rendszerképet és a rendszergazdai jelszavakat.  |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).

További virtuális gép CLI-példaszkripteket az [Azure Windows virtuális gépekre vonatkozó dokumentációban](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) találhat.
