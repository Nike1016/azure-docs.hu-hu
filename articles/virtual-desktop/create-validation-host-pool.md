---
title: Windows rendszerű virtuális asztali előzetes verziójú gazdagép-készlet létrehozása a szolgáltatás frissítéseinek ellenőrzéséhez – Azure
description: Ellenőrzési címkészlet létrehozása a szolgáltatások frissítéseinek figyeléséhez, mielőtt az éles környezetbe helyezné a frissítéseket.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 05/08/2019
ms.author: helohr
ms.openlocfilehash: 732192e481f88c0cd3c78e9dac43970eb5be72e6
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840516"
---
# <a name="tutorial-create-a-host-pool-to-validate-service-updates"></a>Oktatóanyag: Gazdagépcsoport létrehozása a szolgáltatásfrissítések érvényesítéséhez

A gazdagép-készletek egy vagy több azonos virtuális gép gyűjteményei a Windows rendszerű virtuális asztali előzetes verziójú bérlői környezetekben. Mielőtt üzembe helyezi a gazdagép-készleteket az éles környezetben, javasoljuk, hogy hozzon létre egy érvényesítési gazdagépet. A rendszer először alkalmazza a frissítéseket az érvényesítési gazdagépekre, így a szolgáltatás frissítéseinek figyelésével megfigyelheti őket az éles környezetbe. Érvényesítési címkészlet nélkül előfordulhat, hogy nem deríti fel azokat a módosításokat, amelyek hibákat vezetnek be, ami az éles környezetben lévő felhasználók számára állásidőt eredményezhet.

Annak biztosítása érdekében, hogy az alkalmazások a legújabb frissítésekkel működjenek, az ellenőrzési gazdagép-készletnek a lehető leghasonlónak kell lennie az éles környezetben lévő gazdagépekhez. A felhasználóknak az üzemi gazdagép-készlethez hasonlóan gyakran kell csatlakozniuk az ellenőrzési gazdagéphez. Ha automatizált tesztelést végez a gazdagép-készleten, az automatikus tesztelést is tartalmaznia kell az ellenőrzési gazdagépen.

Az érvényesítési gazdagépen lévő hibák hibakereséséhez használhatja a [diagnosztikai szolgáltatást](diagnostics-role-service.md) vagy a [Windows virtuális asztali hibaelhárítási cikkeit](https://docs.microsoft.com/Azure/virtual-desktop/troubleshoot-set-up-overview).

>[!NOTE]
> Javasoljuk, hogy az összes jövőbeli frissítés teszteléséhez hagyja meg az ellenőrző gazdagép készletét.

Mielőtt elkezdené, [töltse le és importálja a Windows rendszerű virtuális asztali PowerShell](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview)-modult, ha még nem tette meg.

## <a name="create-your-host-pool"></a>A gazda készlet létrehozása

A következő cikkek utasításait követve hozhat létre egy gazdagépet:
- [Oktatóanyag: Állomáslista létrehozása az Azure Marketplace-szel](create-host-pools-azure-marketplace.md)
- [Gazdagép létrehozása Azure Resource Manager sablonnal](create-host-pools-arm-template.md)
- [Gazdagép létrehozása a PowerShell-lel](create-host-pools-powershell.md)

## <a name="define-your-host-pool-as-a-validation-host-pool"></a>A gazdagép-készlet definiálása érvényesítési gazdagépként

Futtassa az alábbi PowerShell-parancsmagokat az új címkészlet érvényesítési gazdagépként való definiálásához. Cserélje le az idézőjelek értékeit a munkamenetéhez tartozó értékekre:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
Set-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool" -ValidationEnv $true
```

A következő PowerShell-parancsmag futtatásával ellenőrizze, hogy az érvényesítési tulajdonság be van-e állítva. Cserélje le az idézőjelek értékeit a munkamenetéhez tartozó értékekre.

```powershell
Get-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool"
```

A parancsmag eredményeinek ehhez a kimenethez hasonlóan kell kinéznie:

```
    TenantName          : contoso 
    TenantGroupName     : Default Tenant Group
    HostPoolName        : contosoHostPool
    FriendlyName        :
    Description         :
    Persistent          : False 
    CustomRdpProperty   : use multimon:i:0;
    MaxSessionLimit     : 10
    LoadBalancerType    : BreadthFirst
    ValidationEnv       : True
    Ring                :
```

## <a name="update-schedule"></a>Frissítési ütemterv

Az előzetes verzióban a szolgáltatás frissítései körülbelül havi ütemben történnek. Ha jelentős problémák merülnek fel, a kritikus frissítések gyakoribb ütemben lesznek elérhetők.

## <a name="next-steps"></a>További lépések

Most, hogy létrehozott egy érvényesítési alkalmazáskészletet, megtudhatja, hogyan telepíthet és csatlakozhat egy felügyeleti eszközhöz a Microsoft virtuális asztali erőforrásainak kezeléséhez.

> [!div class="nextstepaction"]
> [Felügyeleti eszköz üzembe helyezése – oktatóanyag](./manage-resources-using-ui.md)
