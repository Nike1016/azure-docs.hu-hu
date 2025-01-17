---
title: Egyéni válasz konfigurálása a webalkalmazási tűzfalhoz az Azure-beli bejárati ajtón
description: Megtudhatja, hogyan konfigurálhat egyéni hibakódot és üzenetet, ha a webalkalmazási tűzfal (WAF) egy kérést blokkol.
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/21/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: 657dc3a43302d16bc403d790bf2c34c2d147dd6c
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2019
ms.locfileid: "67846375"
---
# <a name="configure-a-custom-response-for-azure-web-application-firewall"></a>Egyéni válasz konfigurálása az Azure webalkalmazási tűzfalhoz

Alapértelmezés szerint, ha az Azure-webalkalmazási tűzfal (WAF) és az Azure bejárati ajtó blokkolja a kérelmet egy egyeztetett szabály miatt, egy 403 állapotkódot ad vissza, amely **a kérésben blokkolt** üzenet. Ez a cikk azt ismerteti, hogyan konfigurálható egy egyéni válasz állapotkódot és válaszüzenetet, ha a WAF letiltotta a kérelmet.

## <a name="set-up-your-powershell-environment"></a>A PowerShell-környezet beállítása
Az Azure PowerShell olyan parancsmagok készletét kínálja, amelyek az [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) modellt használják az Azure-erőforrások kezeléséhez. 

Az [Azure PowerShellt](https://docs.microsoft.com/powershell/azure/overview) telepítheti a helyi számítógépen és bármely PowerShell-munkamenetben használhatja. Kövesse az oldalon megjelenő utasításokat, és jelentkezzen be az Azure-beli hitelesítő adataival, és telepítse az az PowerShell-modult.

### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Kapcsolódás az Azure-hoz interaktív párbeszédablak a bejelentkezéshez
```
Connect-AzAccount
Install-Module -Name Az
```
Győződjön meg arról, hogy a PowerShellGet aktuális verziója telepítve van. Futtassa az alábbi parancsot, majd nyissa meg a PowerShellt újból.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 
### <a name="install-azfrontdoor-module"></a>Telepítés az. FrontDoor modul 

```
Install-Module -Name Az.FrontDoor
```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az Azure-ban kapcsolódó erőforrásokat oszt ki egy erőforráscsoporthoz. Ebben a példában egy erőforráscsoportot hoz létre a [New-AzResourceGroup](/powershell/module/Az.resources/new-Azresourcegroup)használatával.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupWAF
```

## <a name="create-a-new-waf-policy-with-custom-response"></a>Új WAF-házirend létrehozása egyéni választal 

Az alábbi példa egy új WAF szabályzat létrehozását mutatja be, amely az egyéni válasz állapotkód 405-ra van beállítva, és az üzenet, **amelyet Ön blokkol.** a [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy)használata.

```azurepowershell
# WAF policy setting
New-AzFrontDoorWafPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-EnabledState enabled `
-Mode Detection `
-CustomBlockResponseStatusCode 405 `
-CustomBlockResponseBody "<html><head><title>You are blocked.</title></head><body></body></html>"
```

Módosítsa egy meglévő WAF-házirend egyéni válasz kódját vagy válasz törzsének beállításait az [Update-AzFrontDoorFireWallPolicy](/powershell/module/az.frontdoor/Update-AzFrontDoorWafPolicy)használatával.

```azurepowershell
# modify WAF response code
Update-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-EnabledState enabled `
-Mode Detection `
-CustomBlockResponseStatusCode 403
```

```azurepowershell
# modify WAF response body
Update-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-CustomBlockResponseBody "<html><head><title> Forbidden</title></head><body></body></html>"
```

## <a name="next-steps"></a>További lépések
- További információ a [](front-door-overview.md) bejárati ajtóról