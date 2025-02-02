---
title: 'Sikertelen kapcsolatcsoport - ExpressRoute visszaállítása: PowerShell: Azure | Microsoft Docs'
description: Ez a cikk segít a sikertelen állapotú ExpressRoute-kapcsolatcsoport alaphelyzetbe állítása.
services: expressroute
author: anzaman
ms.service: expressroute
ms.topic: article
ms.date: 11/28/2018
ms.author: anzaman
ms.custom: seodec18
ms.openlocfilehash: f6fd8e5f0e5730e55fa5bbe13f74be796dd6a4cb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66115838"
---
# <a name="reset-a-failed-expressroute-circuit"></a>Sikertelen az ExpressRoute-kapcsolatcsoport alaphelyzetbe állítása

Egy ExpressRoute-kapcsolatcsoport művelet sikeres, ha a kapcsolatcsoport "sikertelen" állapotba előfordulhat, hogy lépjen. Ez a cikk segít sikertelen Azure ExpressRoute-kapcsolatcsoport alaphelyzetbe állítása.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="reset-a-circuit"></a>Kapcsolatcsoport alaphelyzetbe állítása

1. Telepítse az Azure Resource Manager PowerShell-parancsmagjainak legújabb verzióját. További információk: [Az Azure PowerShell telepítése és konfigurálása](/powershell/azure/install-az-ps).

2. Nyissa meg emelt szintű jogosultságokkal a PowerShell konzolt, és csatlakozzon a fiókjához. A következő példa segít a kapcsolódásban:

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
3. Ha több Azure-előfizetéssel is rendelkezik, ellenőrizze a fiók előfizetéseit.

   ```azurepowershell-interactive
   Get-AzSubscription
   ```
4. Válassza ki a használni kívánt előfizetést.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
   ```
5. Futtassa a következő parancsokat, amelyek állapota sikertelen kapcsolatcsoport alaphelyzetbe állítása:

   ```azurepowershell-interactive
   $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

A kapcsolatcsoport most kifogástalan állapotban kell lennie. Nyisson meg egy támogatási jegyet a [a Microsoft támogatási](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , ha a kapcsolatcsoport még mindig sikertelen állapotban van.

## <a name="next-steps"></a>További lépések

Nyisson meg egy támogatási jegyet a [a Microsoft támogatási](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) Ha továbbra is problémákat tapasztal.
