---
title: Azure CLI-példaszkript – Feliratkozás Blob Storage-fiókra | Microsoft Docs
description: Azure CLI-példaszkript – Feliratkozás Blob Storage-fiókra
services: event-grid
documentationcenter: na
author: tfitzmac
ms.service: event-grid
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2018
ms.author: tomfitz
ms.openlocfilehash: f352542f72226358cd700359eb5aac16e1aa8ad5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60461603"
---
# <a name="subscribe-to-events-for-a-blob-storage-account-with-azure-cli"></a>Feliratkozás egy Blob Storage-fiók eseményeire az Azure CLI-vel

Ez a szkript létrehoz egy Event Grid-előfizetést egy Blob Storage-fiók eseményeihez. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli[main](../../../cli_scripts/event-grid/subscribe-to-blob-storage/subscribe-to-blob-storage.sh "Subscribe to Blob storage")]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsot használja az esemény-előfizetés létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az eventgrid event-subscription create](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create) | Event Grid-előfizetés létrehozása. |
| [az eventgrid event-subscription create](/cli/azure/ext/eventgrid/eventgrid/event-subscription#ext-eventgrid-az-eventgrid-event-subscription-create) – bővítmény verziója | Event Grid-előfizetés létrehozása. |

## <a name="next-steps"></a>További lépések

* Az előfizetések lekérdezéséről lásd: [Event Grid-előfizetések lekérdezése](../query-event-subscriptions.md).
* Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).