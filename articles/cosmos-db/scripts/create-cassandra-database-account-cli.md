---
title: Az Azure CLI-szkript – létrehozása egy Azure Cosmos DB Cassandra API-fiókot, adatbázist és táblát
description: Azure CLI-példaszkript – Azure Cosmos DB Cassandra API-fiók, -adatbázis és -táblázat létrehozása
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 10/26/2018
ms.reviewer: sngun
ms.openlocfilehash: 89d43d8fc956d747de885f69017ed79f49670858
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "66154741"
---
# <a name="azure-cosmos-db-create-a-cassandra-api-account-using-azure-cli"></a>Az Azure Cosmos DB: Azure CLI-vel a Cassandra API-fiók létrehozása

Ez a CLI-példaszkript létrehoz egy Azure Cosmos DB Cassandra API-fiókot, -adatbázist és -táblázatot.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure parancssori felületének 2.0-s vagy annál újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/cosmosdb/create-cosmosdb-cassandra-account/create-cosmosdb-cassandra-account.sh "Create an Azure Cosmos DB Cassandra API account, database, and table.")]

> [!Note]
> Az Azure CLI közvetlenül nem támogatja Cassandra-táblázat létrehozását. Használja az Azure Portalt vagy a CQL felületet.

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A példaszkript futtatása után a következő paranccsal távolítható el az erőforráscsoport és az összes ahhoz kapcsolódó erőforrás.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | Létrehoz egy Azure Cosmos DB-fiókot. |
| [az cosmosdb database create](/cli/azure/cosmosdb/database#az-cosmosdb-database-create) | Létrehoz egy Azure Cosmos DB-adatbázist. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További Azure Cosmos DB CLI-példaszkripteket az [Azure Cosmos DB CLI dokumentációjában](../cli-samples.md) találhat.
