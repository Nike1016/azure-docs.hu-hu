---
title: Létrehozása és kezelése a tűzfalszabályok az Azure Database for postgresql-hez - Azure CLI használatával egyetlen kiszolgáló
description: Ez a cikk ismerteti, hogyan hozhat létre, és az Azure Database-tűzfalszabályok kezelése a PostgreSQL - hez-Azure CLI parancssori használatával egyetlen kiszolgáló.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 03b1c215994e4089ad0aed4eac3868b05c564c4c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65067537"
---
# <a name="create-and-manage-firewall-rules-in-azure-database-for-postgresql---single-server-using-azure-cli"></a>Létrehozása és kezelése a tűzfalszabályok az Azure Database for postgresql-hez - Azure CLI használatával egyetlen kiszolgáló
Kiszolgálószintű tűzfalszabályok segítségével hozzáférésének kezelése az Azure Database for PostgreSQL-kiszolgáló egy adott IP-cím vagy IP-címtartományt. Kényelmes megoldás az Azure parancssori felület parancsait használva, létrehozhat, frissítése, törlése, a listában, és kezelheti a kiszolgálót a tűzfalszabályok megjelenítése. Azure Database for PostgreSQL-tűzfalszabályok áttekintése, lásd: [, Azure Database for PostgreSQL-kiszolgáló tűzfalszabályainak](concepts-firewall-rules.md).

Virtuális hálózat (VNet) szabályok is használható a kiszolgálóhoz való hozzáférés biztonsága érdekében. Tudjon meg többet [létrehozása és kezelése a virtuális hálózati szolgáltatás végpontjai és az Azure CLI használatával szabályok](howto-manage-vnet-using-cli.md).

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató lépéseinek, az alábbiak szükségesek:
- Telepítés [Azure CLI-vel](/cli/azure/install-azure-cli) parancssori segédprogram, vagy használja az Azure Cloud Shellt a böngészőben.
- Egy [, Azure Database for PostgreSQL-kiszolgáló és adatbázis](quickstart-create-server-database-azure-cli.md).

## <a name="configure-firewall-rules-for-azure-database-for-postgresql"></a>Tűzfalszabályok konfigurálása az Azure Database for postgresql-hez
A [az postgres server firewall-rule](/cli/azure/postgres/server/firewall-rule) parancsok tűzfalszabályok konfigurálásához használt.

## <a name="list-firewall-rules"></a>Tűzfal-szabályok listája 
A meglévő kiszolgáló tűzfalszabályait felsorolásához futtassa az [az postgres server firewall-rule list](/cli/azure/postgres/server/firewall-rule) parancsot.
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
A kimenet a tűzfalszabályok sorolja fel, ha bármely, alapértelmezés szerint JSON formátumban. Előfordulhat, hogy használja a kapcsolót `--output table` a kimenetként, olvashatóbb táblázatos formában.
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-firewall-rule"></a>Tűzfalszabály létrehozása
Hozzon létre egy új tűzfalszabályt a kiszolgálón, futtassa a [az postgres server firewall-rule létrehozása](/cli/azure/postgres/server/firewall-rule) parancsot. 

```
To allow access to a singular IP address, provide the same address in the `--start-ip-address` and `--end-ip-address`, as in this example, replacing the IP shown here with your specific IP.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name AllowSingleIpAddress --start-ip-address 13.83.152.1 --end-ip-address 13.83.152.1
```
Ahhoz, hogy az alkalmazások az Azure IP-címekről az Azure Database for PostgreSQL-kiszolgálóhoz csatlakozni, adja meg az IP-cím 0.0.0.0 kezdő IP-és záró IP-cím, ebben a példában látható módon.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name AllowAllAzureIps --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Ez a beállítás konfigurálja a tűzfalat arra, hogy engedélyezzen minden, az Azure felől érkező kapcsolatot, beleértve a más ügyfelek előfizetéseiből érkező kapcsolatokat is. Ezen beállítás kiválasztásakor győződjön meg arról, hogy a bejelentkezési és felhasználói engedélyei a hozzáféréseket az arra jogosult felhasználókra korlátozzák.
> 

Követően sikeres a parancs kimenete listázza a létrehozott, alapértelmezés szerint JSON formátumban tűzfalszabály részletei. Ha hiba történik, a kimenet egy hibaüzenetet jelenít meg helyette.

## <a name="update-firewall-rule"></a>Tűzfalszabályok frissítése 
Az a kiszolgálóra történő meglévő tűzfalszabály módosítása [az postgres server firewall-rule update](/cli/azure/postgres/server/firewall-rule) parancsot. Adja meg a nevét a meglévő tűzfalszabály kimenetként, és a kezdő IP-cím és a záró IP attribútumok frissítése.
```azurecli-interactive
az postgres server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.0
```
Követően sikeres a parancs kimenete listázza a frissítése után alapértelmezés szerint JSON formátumban a tűzfalszabály részletei. Ha hiba történik, a kimenet egy hibaüzenetet jelenít meg helyette.
> [!NOTE]
> Ha nem létezik a tűzfalszabályt, a frissítés paranccsal létrejön.

## <a name="show-firewall-rule-details"></a>Tűzfal szabály részleteinek megjelenítéséhez.
Meglévő kiszolgálószintű tűzfalszabály részleteit jeleníti meg a futó [az postgres server firewall-rule show](/cli/azure/postgres/server/firewall-rule) parancsot.
```azurecli-interactive
az postgres server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange
```
Követően sikeres a parancs kimenete listázza a megadott, alapértelmezés szerint JSON formátumban tűzfalszabály részletei. Ha hiba történik, a kimenet egy hibaüzenetet jelenít meg helyette.

## <a name="delete-firewall-rule"></a>Tűzfalszabály törlése
Visszavonni a hozzáférést egy IP-címtartományt a kiszolgálóhoz, meglévő tűzfalszabály törlése úgy, hogy végrehajtja a [az postgres server firewall-rule törlése](/cli/azure/postgres/server/firewall-rule) parancsot. Adja meg a meglévő tűzfalszabály nevét.
```azurecli-interactive
az postgres server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange
```
Sikeres, Befejezés esetén nem semmilyen kimenet. Hiba esetén a hibaüzenet szövege adja vissza.

## <a name="next-steps"></a>További lépések
- Ehhez hasonlóan használhatja egy webböngészőben [hozzon létre és kezelhető az Azure Database for PostgreSQL tűzfalszabályok az Azure portal használatával](howto-manage-firewall-using-portal.md).
- Ismerje meg jobban az kapcsolatos [, Azure Database for PostgreSQL-kiszolgáló tűzfalszabályainak](concepts-firewall-rules.md).
- További való biztonságos hozzáférést a kiszolgáló által [létrehozása és kezelése a virtuális hálózati szolgáltatás végpontjai és az Azure CLI használatával szabályok](howto-manage-vnet-using-cli.md).
- Segítségre van szüksége az Azure Database for PostgreSQL-kiszolgálóhoz csatlakozik, lásd: [adatkapcsolattárak az Azure Database for PostgreSQL](concepts-connection-libraries.md).
