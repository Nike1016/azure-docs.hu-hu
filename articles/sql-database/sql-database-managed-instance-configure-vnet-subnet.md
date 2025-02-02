---
title: Meglévő virtuális hálózat konfigurálása Azure SQL Database felügyelt példányhoz | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan lehet konfigurálni egy meglévő virtuális hálózatot és alhálózatot, ahol Azure SQL Database felügyelt példányt telepíthet.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 01/15/2019
ms.openlocfilehash: 509a08705d87e8f789e26ebbcec295a31f99f416
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567632"
---
# <a name="configure-an-existing-virtual-network-for-azure-sql-database-managed-instance"></a>Meglévő virtuális hálózat konfigurálása Azure SQL Database felügyelt példányhoz

Azure SQL Database felügyelt példányt egy Azure-beli [virtuális hálózaton](../virtual-network/virtual-networks-overview.md) belül kell telepíteni, és csak a felügyelt példányok számára dedikált alhálózatot kell üzembe helyezni. A meglévő virtuális hálózatot és alhálózatot akkor használhatja, ha a [felügyelt példány virtuális hálózati követelményeinek](sql-database-managed-instance-connectivity-architecture.md#network-requirements)megfelelően van konfigurálva.

Ha a következő esetek valamelyike vonatkozik Önre, a jelen cikkben ismertetett parancsfájllal ellenőrizheti és módosíthatja a hálózatot:

- Olyan új alhálózattal rendelkezik, amely még nincs konfigurálva.
- Nem biztos benne, hogy az alhálózat igazodik a követelményekhez [](sql-database-managed-instance-connectivity-architecture.md#network-requirements).
- Azt szeretné, hogy az alhálózat továbbra is megfeleljen a [hálózati követelményeknek](sql-database-managed-instance-connectivity-architecture.md#network-requirements) , miután végzett a módosításokat.

> [!Note]
> Felügyelt példányok csak a Azure Resource Manager üzemi modellel létrehozott virtuális hálózatokon hozhatók létre. A klasszikus üzemi modellel létrehozott Azure-beli virtuális hálózatok nem támogatottak. Az alhálózat méretének kiszámításához kövesse a [felügyelt példányok alhálózat méretének meghatározása](sql-database-managed-instance-determine-size-vnet-subnet.md) című cikkben található irányelveket. Az alhálózatot nem lehet átméretezni az erőforrások belső telepítése után.
>
> Felügyelt példány létrehozása után a felügyelt példány vagy VNet másik erőforráscsoporthoz vagy előfizetésbe való áthelyezése nem támogatott.

## <a name="validate-and-modify-an-existing-virtual-network"></a>Meglévő virtuális hálózat érvényesítése és módosítása

Ha felügyelt példányt szeretne létrehozni egy meglévő alhálózaton belül, a következő PowerShell-parancsfájlt javasoljuk az alhálózat előkészítéséhez:

```powershell
$scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/prepare-subnet'

$parameters = @{
    subscriptionId = '<subscriptionId>'
    resourceGroupName = '<resourceGroupName>'
    virtualNetworkName = '<virtualNetworkName>'
    subnetName = '<subnetName>'
    }

Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/prepareSubnet.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters
```

A parancsfájl három lépésben készíti elő az alhálózatot:

1. Érvényesít Ellenőrzi a kiválasztott virtuális hálózatot és alhálózatot a felügyelt példányok hálózati követelményeihez.
2. Ellenőrizze Ez a felhasználó olyan módosításokat jelenít meg, amelyeket az alhálózat felügyelt példányok telepítéséhez való előkészítéséhez kell végezni. Emellett beleegyezik.
3. Előkészítése Megfelelően konfigurálja a virtuális hálózatot és az alhálózatot.

## <a name="next-steps"></a>További lépések

- Tekintse át a [Mi az a felügyelt példány?](sql-database-managed-instance.md)című témakört.
- A virtuális hálózatok létrehozásával, felügyelt példány létrehozásával és adatbázis biztonsági másolatból való visszaállításával kapcsolatos oktatóanyagért lásd: [Azure SQL Database felügyelt példány létrehozása](sql-database-managed-instance-get-started.md).
- DNS-problémák esetén tekintse meg az [Egyéni DNS konfigurálása](sql-database-managed-instance-custom-dns.md)című témakört.
