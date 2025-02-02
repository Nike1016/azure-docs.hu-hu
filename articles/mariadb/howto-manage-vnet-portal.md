---
title: Azure Database for MariaDB VNet-szolgáltatási végpontok és szabályok létrehozása és kezelése a Azure Portal használatával | Microsoft Docs
description: Azure Database for MariaDB VNet-szolgáltatási végpontok és szabályok létrehozása és kezelése a Azure Portal használatával
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 02/26/2019
ms.openlocfilehash: 392428c0498458f49c439a0553d581a302c0c242
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68610096"
---
# <a name="create-and-manage-azure-database-for-mariadb-vnet-service-endpoints-and-vnet-rules-by-using-the-azure-portal"></a>Azure Database for MariaDB VNet-szolgáltatási végpontok és VNet-szabályok létrehozása és kezelése a Azure Portal használatával

Virtual Network (VNet) szolgáltatási végpontok és szabályok kiterjesztik a Virtual Network privát címterület-területét a Azure Database for MariaDB-kiszolgálóra. Az Azure Database for MariaDB VNet szolgáltatásbeli végpontok, például a korlátozások áttekintését lásd: [Azure Database for MariaDB Server VNet Service](concepts-data-access-security-vnet.md)-végpontok. A VNet szolgáltatási végpontok a Azure Database for MariaDB összes támogatott régiójában elérhetők.

> [!NOTE]
> Virtuális hálózati Szolgáltatásvégpontok támogatása csak az általános célú és memóriahasználatra optimalizált kiszolgálók esetében érhető el.

## <a name="create-a-vnet-rule-and-enable-service-endpoints"></a>VNet-szabály létrehozása és szolgáltatási végpontok engedélyezése

1. A MariaDB-kiszolgáló lapon, a beállítások fejléc alatt kattintson a **kapcsolatbiztonsági** elemre a Azure Database for MariaDB kapcsolódási biztonsági paneljének megnyitásához.

2. Győződjön meg arról, hogy az Azure-szolgáltatások hozzáférésének engedélyezése beállítás **ki van kapcsolva**.

> [!Important]
> Ha be értékre állítja be, az Azure MariaDB adatbázis-kiszolgáló bármely alhálózatról fogadja a kommunikációt. Ha a vezérlőt a be értékre állítja, előfordulhat, hogy a biztonsági szempontból túlságosan nagy a hozzáférés. A Microsoft Azure Virtual Network szolgáltatási végpont funkciója, amely a Azure Database for MariaDB virtuális hálózati szabály funkciójával együttműködve csökkenti a biztonsági felületét.

3. Ezután kattintson a **+ meglévő virtuális hálózat hozzáadása**lehetőségre. Ha nem rendelkezik meglévő VNet, kattintson az **+ új virtuális hálózat létrehozása** lehetőségre egy létrehozásához. Lásd [: gyors útmutató: Virtuális hálózat létrehozása a Azure Portal használatával](../virtual-network/quick-create-portal.md)

   ![Azure Portal – kattintson a kapcsolatbiztonsági lehetőségre](./media/howto-manage-vnet-portal/1-connection-security.png)

4. Adja meg a VNet szabály nevét, válassza ki az előfizetést, a virtuális hálózatot és az alhálózatot, majd kattintson az **Engedélyezés**gombra. Ez automatikusan engedélyezi a VNet szolgáltatási végpontok használatát az alhálózaton a **Microsoft. SQL** szolgáltatás címkével.

   ![Azure Portal – VNet konfigurálása](./media/howto-manage-vnet-portal/2-configure-vnet.png)

   A fióknak rendelkeznie kell a virtuális hálózat és a szolgáltatás végpontjának létrehozásához szükséges engedélyekkel.

   A szolgáltatási végpontok egymástól függetlenül konfigurálhatók virtuális hálózatokon a virtuális hálózathoz írási hozzáféréssel rendelkező felhasználóktól.
    
   Az Azure-szolgáltatási erőforrások VNet való biztonságossá tételéhez a felhasználónak engedéllyel kell rendelkeznie a "Microsoft. Network/virtualNetworks/Subnets/joinViaServiceEndpoint/" jogosultsággal a hozzáadott alhálózatokhoz. Ez az engedély alapértelmezés szerint bele van foglalva a beépített szolgáltatás-rendszergazdai szerepkörökbe, és egyéni szerepkörök létrehozásával módosítható.
    
   További információk a [beépített szerepkörökről](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) és a bizonyos engedélyek [egyéni szerepkörökhöz](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles) való hozzárendeléséről.
    
   A virtuális hálózatok és az Azure-szolgáltatási erőforrások tartozhatnak egyazon előfizetéshez, vagy különböző előfizetésekhez is. Ha a VNet és az Azure-szolgáltatási erőforrások különböző előfizetésekben találhatók, akkor az erőforrásoknak ugyanahhoz a Active Directory (AD) bérlőhöz kell tartozniuk. Győződjön meg arról, hogy mindkét előfizetés regisztrálva van a **Microsoft. SQL** erőforrás-szolgáltatónál. További információ: [Resource-Manager-regisztráció][resource-manager-portal]

   > [!IMPORTANT]
   > Javasoljuk, hogy a szolgáltatási végpontok konfigurálása előtt olvassa el ezt a cikket a szolgáltatás-végponti konfigurációkkal és szempontokkal kapcsolatban. **Virtual Network szolgáltatási végpont:** A [Virtual Network szolgáltatási végpont](../virtual-network/virtual-network-service-endpoints-overview.md) olyan alhálózat, amelynek tulajdonságértékek egy vagy több formális Azure-szolgáltatástípus nevét tartalmazzák. A VNet Services-végpontok a **Microsoft. SQL**nevű szolgáltatástípus-nevet használják, amely az SQL Database nevű Azure-szolgáltatásra hivatkozik. Ez a szolgáltatási címke a Azure SQL Databasera, a Azure Database for MariaDBra, a PostgreSQL-re és a MySQL szolgáltatásokra is vonatkozik. Fontos megjegyezni, hogy amikor a **Microsoft. SQL** szolgáltatás címkéjét egy VNet szolgáltatási végpontra alkalmazza, a szolgáltatás végponti forgalmát konfigurálja az összes Azure Database-szolgáltatáshoz, beleértve a Azure SQL Database, a Azure Database for PostgreSQL, az Azure Database for MariaDB és Azure Database for MySQL-kiszolgálókat az alhálózaton.
   > 

5. Ha engedélyezve van, kattintson **az OK** gombra, és látni fogja, hogy a VNet szolgáltatás-végpontok engedélyezve vannak a VNet szabállyal együtt.

   ![VNet szolgáltatás-végpontok engedélyezve és VNet szabály létrehozva](./media/howto-manage-vnet-portal/3-vnet-service-endpoints-enabled-vnet-rule-created.png)

## <a name="next-steps"></a>További lépések
- További információ az [SSL konfigurálásáról Azure Database for MariaDB](howto-configure-ssl.md)
- Ehhez hasonlóan parancsfájlt is [engedélyezhet a VNet szolgáltatás végpontjai számára, és létrehozhat egy VNet-szabályt az Azure CLI-vel való Azure Database for MariaDBhoz](howto-manage-vnet-cli.md).

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/resource-manager-supported-services.md