---
title: Az Azure CLI Példaszkript – Azure Cache létrehozása a redis |} A Microsoft Docs
description: Az Azure CLI Példaszkript – Azure Cache létrehozása a redis
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
tags: azure-service-management
ms.assetid: afd7f6e0-9297-4c98-a95e-597be939cef7
ms.service: cache
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: yegu
ms.openlocfilehash: f4900242c9b6e5eecd5364d74bfcd8e2bc4efb2b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60607732"
---
# <a name="create-an-azure-cache-for-redis"></a>A Redis Azure Cache létrehozása

Ebben a forgatókönyvben, megtudhatja, hogyan hozhat létre egy Azure Cache redis.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli[main](../../../cli_scripts/redis-cache/create-cache/create-cache.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja egy erőforráscsoport és a egy Azure Cache létrehozása a Redis. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az a redis létrehozása](https://docs.microsoft.com/cli/azure/redis) | Az Azure gyorsítótár létrehozása a Redis-példányt. |


## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).

További Azure Cache a Redis CLI-példaszkripteket megtalálható a [Azure Cache Redis dokumentációt](../cli-samples.md).
