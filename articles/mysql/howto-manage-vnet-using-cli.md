---
title: Azure Database for MySQL VNet-szolgáltatási végpontok és szabályok létrehozása és kezelése az Azure CLI használatával | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan hozhatók létre és kezelhetők Azure Database for MySQL VNet szolgáltatásbeli végpontok és szabályok az Azure CLI parancssorral.
author: bolzmj
ms.author: mbolz
manager: jhubbard
ms.service: mysql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 10/23/2018
ms.openlocfilehash: d08b99c0a668286d9e9b94f2229915be8ff106f0
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68610407"
---
# <a name="create-and-manage-azure-database-for-mysql-vnet-service-endpoints-using-azure-cli"></a>Azure Database for MySQL VNet-szolgáltatásbeli végpontok létrehozása és kezelése az Azure CLI-vel
A virtuális hálózat (VNet) szolgáltatásvégpontjai és szabályai kiterjesztik a virtuális hálózat magánhálózati címtartományát az Azure Database for MySQL-kiszolgálóra. A kényelmes Azure parancssori felületi (CLI) parancsok használatával létrehozhat, frissíthet, törölhet, listázhat és megjeleníthet VNet szolgáltatási végpontokat és szabályokat a kiszolgáló kezeléséhez. Az Azure Database for MySQL VNet szolgáltatásbeli végpontok, például a korlátozások áttekintését lásd: [Azure Database for MySQL Server VNet Service](concepts-data-access-and-security-vnet.md)-végpontok. A VNet szolgáltatási végpontok a Azure Database for MySQL összes támogatott régiójában elérhetők.

## <a name="prerequisites"></a>Előfeltételek
A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:
- Telepítse [Az Azure CLI](/cli/azure/install-azure-cli) -t, vagy használja a Azure Cloud shellt a böngészőben.
- Egy [, Azure Database for MySQL-kiszolgáló és adatbázis](quickstart-create-mysql-server-database-using-azure-cli.md).

> [!NOTE]
> Virtuális hálózati Szolgáltatásvégpontok támogatása csak az általános célú és memóriahasználatra optimalizált kiszolgálók esetében érhető el.
> VNet esetén, ha a forgalom egy közös VNet-átjárón keresztül áramlik a szolgáltatási végpontokkal, és a partnernek kell lennie, hozzon létre egy ACL/VNet szabályt, amely lehetővé teszi, hogy az Azure Virtual Machines az átjáró VNet hozzáférjenek a Azure Database for MySQL-kiszolgálóhoz.

## <a name="configure-vnet-service-endpoints-for-azure-database-for-mysql"></a>Vnet-szolgáltatási végpontok konfigurálása Azure Database for MySQLhoz
Az az [Network vnet](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest) parancs a virtuális hálózatok konfigurálására szolgál.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure CLI 2.0-s vagy újabb verzióját kell futtatnia. A telepített verziók megtekintéséhez futtassa az `az --version` parancsot. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését]( /cli/azure/install-azure-cli) ismertető cikket. 

A CLI helyi futtatása esetén az [az login](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest) paranccsal be kell jelentkeznie a fiókjába. Jegyezze fel a megfelelő előfizetésnév parancskimenetéből az **id** tulajdonságot.
```azurecli-interactive
az login
```

Ha több előfizetéssel rendelkezik válassza ki a megfelelő előfizetést, amelyre az erőforrást terhelni szeretné. Válassza ki a megadott előfizetés-azonosítót a fiókja alatt az [az account set](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-set) paranccsal. Az előfizetés **az login** kimenetének **id** tulajdonságát illessze be az előfizetés-azonosító helyőrzője helyére.

- A fióknak rendelkeznie kell a virtuális hálózat és a szolgáltatás végpontjának létrehozásához szükséges engedélyekkel.

A szolgáltatási végpontok egymástól függetlenül konfigurálhatók virtuális hálózatokon a virtuális hálózathoz írási hozzáféréssel rendelkező felhasználóktól.

Az Azure-szolgáltatási erőforrások VNet való biztonságossá tételéhez a felhasználónak engedéllyel kell rendelkeznie a "Microsoft. Network/virtualNetworks/Subnets/joinViaServiceEndpoint/" jogosultsággal a hozzáadott alhálózatokhoz. Ez az engedély alapértelmezés szerint bele van foglalva a beépített szolgáltatás-rendszergazdai szerepkörökbe, és egyéni szerepkörök létrehozásával módosítható.

További információk a [beépített szerepkörökről](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) és a bizonyos engedélyek [egyéni szerepkörökhöz](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles) való hozzárendeléséről.

A virtuális hálózatok és az Azure-szolgáltatási erőforrások tartozhatnak egyazon előfizetéshez, vagy különböző előfizetésekhez is. Ha a VNet és az Azure-szolgáltatási erőforrások különböző előfizetésekben találhatók, akkor az erőforrásoknak ugyanahhoz a Active Directory (AD) bérlőhöz kell tartozniuk. Győződjön meg arról, hogy mindkét előfizetés regisztrálva van a **Microsoft. SQL** erőforrás-szolgáltatónál. További információ: [Resource-Manager-regisztráció][resource-manager-portal]

> [!IMPORTANT]
> Javasoljuk, hogy olvassa el ezt a cikket a szolgáltatás-végponti konfigurációkról és a szempontokról, mielőtt futtatná az alábbi parancsfájlt, vagy konfigurálja a szolgáltatási végpontokat. **Virtual Network szolgáltatási végpont:** A [Virtual Network szolgáltatási végpont](../virtual-network/virtual-network-service-endpoints-overview.md) olyan alhálózat, amelynek tulajdonságértékek egy vagy több formális Azure-szolgáltatástípus nevét tartalmazzák. A VNet Services-végpontok a **Microsoft. SQL**nevű szolgáltatástípus-nevet használják, amely az SQL Database nevű Azure-szolgáltatásra hivatkozik. Ez a szolgáltatási címke a Azure SQL Databasere, a Azure Database for PostgreSQL és a MySQL szolgáltatásokra is vonatkozik. Fontos megjegyezni, hogy amikor a **Microsoft. SQL** szolgáltatás címkéjét egy VNet szolgáltatási végpontra alkalmazza, a szolgáltatás végponti forgalmát konfigurálja az összes Azure Database-szolgáltatáshoz, beleértve a Azure SQL Database, a Azure Database for PostgreSQL és az Azure Database for Az alhálózaton található MySQL-kiszolgálók. 
> 

### <a name="sample-script-to-create-an-azure-database-for-mysql-database-create-a-vnet-vnet-service-endpoint-and-secure-the-server-to-the-subnet-with-a-vnet-rule"></a>Parancsfájl Azure Database for MySQL adatbázis létrehozásához, VNet létrehozásához, VNet szolgáltatás végpontjának biztonságossá tételéhez, valamint a kiszolgáló VNet-szabállyal való biztonságossá tétele az alhálózathoz
A példaszkriptben módosítsa a kiemelt sorokat, és adja meg azokban a rendszergazdai felhasználónevét és jelszavát. Cserélje le a `az account set --subscription` parancsban használt SubscriptionID a saját előfizetés-azonosítójával.
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/create-mysql-server.sh?highlight=5,20 "Create an Azure Database for MySQL, VNet, VNet service endpoint, and VNet rule.")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása
A példaszkript futtatása után a következő paranccsal távolítható el az erőforráscsoport és az összes ahhoz kapcsolódó erőforrás.
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/delete-mysql.sh "Delete the resource group.")]

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/resource-manager-supported-services.md

