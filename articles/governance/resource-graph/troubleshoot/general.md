---
title: Gyakori hibák elhárítása
description: Ismerje meg, hogyan lehet elhárítani az Azure-erőforrások Azure Resource Graph használatával történő lekérdezésével kapcsolatos problémákat.
author: DCtheGeek
ms.author: dacoulte
ms.date: 07/24/2019
ms.topic: troubleshooting
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: 511d170f90e8ed34b00a3960d084223ec73d99dd
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68480552"
---
# <a name="troubleshoot-errors-using-azure-resource-graph"></a>Hibák elhárítása az Azure Resource Graph használatával

Az Azure-erőforrások Azure Resource Graph-ban való lekérdezése során hibák léphetnek fel. Ez a cikk az esetlegesen előforduló különböző hibákat és azok megoldását ismerteti.

## <a name="finding-error-details"></a>Hiba részleteinek megállapítása

A legtöbb hiba az Azure Resource Graph-lekérdezés futtatásakor felmerülő probléma eredménye. Ha egy lekérdezés meghiúsul, az SDK részletesen ismerteti a sikertelen lekérdezés részleteit. Ez az információ azt jelzi, hogy a probléma kijavítható, és egy későbbi lekérdezés sikeres lehet.

## <a name="general-errors"></a>Általános hibák

### <a name="toomanysubscription"></a>Forgatókönyv Túl sok előfizetés

#### <a name="issue"></a>Probléma

Azok az ügyfelek, akik több mint 1000 előfizetéshez férnek hozzá, beleértve az [Azure Lighthouse](../../../lighthouse/overview.md)-val való több-bérlős előfizetést, az Azure Resource Graph egyetlen hívásával nem hívhatják le az összes előfizetést.

#### <a name="cause"></a>Ok

Az Azure CLI és a PowerShell csak az első 1000-előfizetést továbbítja az Azure Resource Graph-ba. Az Azure Resource Graph REST API maximális számú előfizetést fogad el a lekérdezés végrehajtásához.

#### <a name="resolution"></a>Megoldás:

Batch-kérelmek a lekérdezéshez az előfizetések egy olyan részhalmazával, amely az 1000-os előfizetési korlát alatt marad. A megoldás az **előfizetés** paramétert használja a PowerShellben.

```azurepowershell-interactive
# Replace this query with your own
$query = 'project type'

# Fetch the full array of subscription IDs
$subscriptions = Get-AzSubscription
$subscriptionIds = $subscriptions.Id

# Create a counter, set the batch size, and prepare a variable for the results
$counter = [PSCustomObject] @{ Value = 0 }
$batchSize = 1000
$response = @()

# Group the subscriptions into batches
$subscriptionsBatch = $subscriptionIds | Group -Property { [math]::Floor($counter.Value++ / $batchSize) }

# Run the query for each batch
foreach ($batch in $subscriptionsBatch){ $response += Search-AzGraph -Query $query -Subscription $batch.Group }

# View the completed results of the query on all subscriptions
$response
```

## <a name="next-steps"></a>További lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

- Választ kaphat az Azure-szakértőktől az [Azure-fórumokon](https://azure.microsoft.com/support/forums/).
- Az [@AzureSupport](https://twitter.com/azuresupport) a Microsoft Azure hivatalos Twitter-fiókja, amelyen keresztül a jobb felhasználói élmény érdekében igyekszünk az Azure-felhasználók közösségét ellátni a megfelelő forrásokkal: válaszokkal, támogatással és szakértői segítséggel.
- Ha további segítségre van szüksége, egy Azure-támogatási incidenst is megadhat. Nyissa meg az [Azure támogatási](https://azure.microsoft.com/support/options/) webhelyét, és válassza a **támogatás kérése**lehetőséget.