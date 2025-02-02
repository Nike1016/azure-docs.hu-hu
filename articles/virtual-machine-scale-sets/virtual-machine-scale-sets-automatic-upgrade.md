---
title: Az operációs rendszer rendszerképének automatikus frissítése Azure-beli virtuálisgép-méretezési csoportokkal | Microsoft Docs
description: Ismerje meg, hogyan frissítheti automatikusan az operációsrendszer-rendszerképet a méretezési csoport virtuálisgép-példányain
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: drewm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/16/2019
ms.author: manayar
ms.openlocfilehash: ac754acd61700dc39ebc633da4274c74d8463824
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68884180"
---
# <a name="azure-virtual-machine-scale-set-automatic-os-image-upgrades"></a>Azure virtuálisgép-méretezési csoport automatikus operációsrendszer-rendszerképének frissítése

Az operációsrendszer-lemezképek automatikus verziófrissítésének engedélyezése a méretezési csoporton belül a méretezési csoport összes példányához tartozó operációsrendszer-lemez biztonságos és automatikus frissítésével megkönnyíti a frissítés kezelését.

Az operációs rendszer automatikus frissítése a következő jellemzőkkel rendelkezik:

- A konfigurálást követően a lemezkép-közzétevők által közzétett legújabb operációsrendszer-lemezképet a rendszer automatikusan alkalmazza a méretezési csoportra felhasználói beavatkozás nélkül.
- A példányok kötegeit folyamatosan, a kiadó által közzétett új platform-rendszerkép közzétételekor frissíti.
- Integrálható az Application Health-szondákkal és az [alkalmazás-állapot bővítménnyel](virtual-machine-scale-sets-health-extension.md).
- Minden virtuálisgép-mérethez használható, valamint Windows-és Linux-rendszerképekhez is.
- Bármikor letilthatja az automatikus frissítést (az operációs rendszer frissítései manuálisan is elindíthatók).
- A virtuális gép operációsrendszer-lemezét a rendszer a legújabb rendszerkép-verzióval létrehozott új operációsrendszer-lemezzel helyettesíti. A konfigurált bővítmények és az egyéni adatszkriptek futnak, míg a megőrzött adatlemezek megmaradnak.
- A [bővítmények sorrendbe rendezése](virtual-machine-scale-sets-extension-sequencing.md) támogatott.
- Az operációs rendszer rendszerképének automatikus frissítése bármilyen méretű méretezési csoporton engedélyezhető.

## <a name="how-does-automatic-os-image-upgrade-work"></a>Hogyan működik az operációs rendszer rendszerképének automatikus frissítése?

A frissítés úgy működik, hogy egy virtuális gép operációsrendszer-lemezét egy új, a legújabb rendszerkép-verzió használatával létrehozott lemezzel helyettesíti. Minden konfigurált bővítmény és egyéni adatparancsfájl az operációsrendszer-lemezen fut, míg a megőrzött adatlemezek megmaradnak. Az alkalmazás leállásának csökkentése érdekében a frissítések a kötegekben történnek, és a méretezési csoport több mint 20%-a bármikor frissül. Emellett integrálhat egy Azure Load Balancer alkalmazás állapotának mintavétele vagy az [alkalmazás állapota bővítményt](virtual-machine-scale-sets-health-extension.md)is. Javasoljuk, hogy az alkalmazás szívverését tartalmazza, és ellenőrizze a verziófrissítés sikerességét a frissítési folyamat minden egyes kötegében.

A frissítési folyamat a következőképpen működik:
1. A frissítési folyamat megkezdése előtt a Orchestrator gondoskodni fog arról, hogy a teljes méretezési csoport példányainak legfeljebb 20%-a sérült legyen (bármilyen okból).
2. A frissítési Orchestrator azonosítja a frissítendő virtuálisgép-példányok kötegét, és a kötegek száma összesen legfeljebb 20%-a lehet. 5 vagy kevesebb példánnyal rendelkező kisebb méretezési csoportok esetén a frissítés kötegének mérete egy virtuálisgép-példány.
3. A rendszer lecseréli a virtuálisgép-példányok kiválasztott kötegének operációsrendszer-lemezét a legújabb rendszerképből létrehozott új operációsrendszer-lemezre. A méretezési csoport modelljében megadott bővítmények és konfigurációk a frissített példányra lesznek alkalmazva.
4. A konfigurált alkalmazás-állapottal vagy az alkalmazás állapotával rendelkező méretezési csoportok esetében a frissítés akár 5 percet vár, amíg a példány Kifogástalan állapotba kerül, mielőtt továbblépne a következő köteg frissítésére. Ha egy példány a frissítés után 5 perccel nem állítja helyre az állapotát, akkor alapértelmezés szerint a rendszer a példány előző operációsrendszer-lemezét állítja vissza.
5. A frissítési Orchestrator azt is nyomon követi, hogy a példányok hány százalékát nem Kifogástalan állapotba kell frissíteni a frissítés után. A frissítés leáll, ha a frissített példányok több mint 20%-a nem Kifogástalan állapotba kerül a frissítési folyamat során.
6. A fenti folyamat addig folytatódik, amíg a méretezési csoport összes példánya frissítve nem lett.

A méretezési csoport operációs rendszerének frissítése Orchestrator az összes köteg frissítése előtt ellenőrzi a teljes méretezési csoport állapotát. Egy köteg frissítése közben más párhuzamos tervezett vagy nem tervezett karbantartási tevékenységek lehetnek, amelyek hatással lehetnek a méretezési csoport példányainak állapotára. Ilyen esetekben, ha a méretezési csoport példányainak több mint 20%-a állapota sérült, akkor a méretezési csoport frissítése az aktuális köteg végén leáll.

## <a name="supported-os-images"></a>Támogatott operációsrendszer-lemezképek
Jelenleg csak bizonyos operációsrendszer-platform-lemezképek támogatottak. Az egyéni lemezképek jelenleg nem támogatottak.

A következő SKU-EK jelenleg támogatottak (és a továbbiak rendszeresen bővülnek):

| Kiadó               | Operációs rendszer ajánlata      |  Termékváltozat               |
|-------------------------|---------------|--------------------|
| Canonical               | UbuntuServer  | 16.04-LTS          |
| Canonical               | UbuntuServer  | 18.04-LTS          |
| Rogue Wave (OpenLogic)  | CentOS        | 7.5                |
| CoreOS                  | CoreOS        | Stable             |
| Microsoft Corporation   | WindowsServer | 2012-R2-Datacenter |
| Microsoft Corporation   | WindowsServer | 2016 – Datacenter    |
| Microsoft Corporation   | WindowsServer | 2016-Datacenter-Smalldisk |
| Microsoft Corporation   | WindowsServer | 2016-Datacenter-with-Containers |
| Microsoft Corporation   | WindowsServer | 2019 – Datacenter |
| Microsoft Corporation   | WindowsServer | 2019-Datacenter-Smalldisk |
| Microsoft Corporation   | WindowsServer | 2019-Datacenter-with-Containers |


## <a name="requirements-for-configuring-automatic-os-image-upgrade"></a>Az operációsrendszer-rendszerkép automatikus frissítésének konfigurálására vonatkozó követelmények

- A platform rendszerképének *Version* tulajdonságát a *legújabb*értékre kell állítani.
- A nem Service Fabric méretezési csoportokhoz használjon Application Health-mintavételt vagy az [alkalmazás állapotának kiterjesztését](virtual-machine-scale-sets-health-extension.md) .
- A számítási API 2018-10-01-es vagy újabb verzióját használja.
- Győződjön meg arról, hogy a méretezési csoport modelljében megadott külső erőforrások elérhetők és frissülnek. Ilyenek például a virtuális gépek bővítményének tulajdonságai, a Storage-fiók hasznos adatai, a modellben található titkos kódok, valamint egyebek között az SAS URI-ja.
- A Windows rendszerű virtuális gépeket használó méretezési csoportok esetében, a számítási API 2019-03-01-es verziójától kezdve a *virtualMachineProfile. osProfile. windowsConfiguration. enableAutomaticUpdates* tulajdonságnak *false* értékre kell állítani a méretezési csoport modelljében. definition. A fenti tulajdonság lehetővé teszi a virtuális gépeken belüli verziófrissítéseket, ahol a "Windows Update" az operációsrendszer-lemez cseréje nélkül alkalmazza az operációs rendszer javításait. Ha a méretezési csoporton engedélyezve vannak az operációs rendszer rendszerképének automatikus frissítése, a "Windows Update" nem szükséges további frissítésre.

### <a name="service-fabric-requirements"></a>Service Fabric követelmények

Ha Service Fabric használ, győződjön meg arról, hogy teljesülnek a következő feltételek:
-   Service Fabric [tartóssági szint](../service-fabric/service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) Silver vagy Gold, és nem bronz.
-   A méretezési csoport modelljének definíciójában a Service Fabric-bővítménynek TypeHandlerVersion 1,1 vagy újabb értékűnek kell lennie.
-   A tartóssági szintnek azonosnak kell lennie a Service Fabric-fürtön, és Service Fabric bővítményt a méretezési csoport modelljének definíciójában.

Győződjön meg arról, hogy a tartóssági beállítások nem egyeznek meg a Service Fabric-fürtön, és Service Fabric a bővítményt, mivel az eltérés a frissítési hibákat eredményezi. A tartóssági szintek az [ezen az oldalon](../service-fabric/service-fabric-cluster-capacity.md#changing-durability-levels)vázolt irányelvek szerint módosíthatók.

## <a name="configure-automatic-os-image-upgrade"></a>Az operációsrendszer-rendszerkép automatikus frissítésének konfigurálása
Az operációsrendszer-rendszerkép automatikus frissítésének konfigurálásához győződjön meg arról, hogy a *automaticOSUpgradePolicy. enableAutomaticOSUpgrade* tulajdonság értéke *true (igaz* ) értékre van állítva a méretezési csoport modelljének definíciójában.

### <a name="rest-api"></a>REST API
Az alábbi példa azt ismerteti, hogyan állíthatja be az operációs rendszer frissítéseit egy méretezési csoport modelljére:

```
PUT or PATCH on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version=2018-10-01`
```

```json
{
  "properties": {
    "upgradePolicy": {
      "automaticOSUpgradePolicy": {
        "enableAutomaticOSUpgrade":  true
      }
    }
  }
}
```

### <a name="azure-powershell"></a>Azure PowerShell
Az [Update-AzVmss](/powershell/module/az.compute/update-azvmss) parancsmag használatával tekintse meg a méretezési csoport operációsrendszer-frissítési előzményeit. A következő példa a *myResourceGroup*nevű erőforráscsoport *myScaleSet* nevű méretezési csoportjának automatikus frissítését konfigurálja:

```azurepowershell-interactive
Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -AutomaticOSUpgrade $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Az az [vmss Update](/cli/azure/vmss#az-vmss-update) paranccsal tekintheti meg a méretezési csoport operációsrendszer-frissítési előzményeit. Használja az Azure CLI-2.0.47 vagy újabb verzióját. A következő példa a *myResourceGroup*nevű erőforráscsoport *myScaleSet* nevű méretezési csoportjának automatikus frissítését konfigurálja:

```azurecli-interactive
az vmss update --name myScaleSet --resource-group myResourceGroup --set UpgradePolicy.AutomaticOSUpgradePolicy.EnableAutomaticOSUpgrade=true
```

## <a name="using-application-health-probes"></a>Application Health-mintavételek használata

Az operációs rendszer frissítése során a méretezési csoportokban lévő virtuálisgép-példányok egyszerre egy köteget frissítenek. A frissítés csak akkor folytatható, ha az ügyfélalkalmazás kifogástalan állapotban van a frissített VM-példányokon. Javasoljuk, hogy az alkalmazás egészségügyi jeleket biztosítson a méretezési csoport operációsrendszer-frissítési motorjának. Alapértelmezés szerint az operációs rendszer frissítése során a platform úgy véli, hogy a virtuális gép energiagazdálkodási állapota és a bővítmények kiépítési állapota határozza meg, hogy a virtuálisgép-példány kifogástalan állapotú-e a frissítés után. Egy virtuálisgép-példány operációsrendszer-frissítése során a virtuálisgép-példány operációsrendszer-lemezét a rendszer a legújabb lemezkép-verzió alapján egy új lemezzel helyettesíti. Az operációs rendszer frissítésének befejeződése után a konfigurált bővítmények futnak ezeken a virtuális gépeken. Az alkalmazás csak akkor tekinthető kifogástalannak, ha a példány összes bővítménye sikeresen kiépítve.

A méretezési csoport opcionálisan konfigurálható az Application Health-mintavételekkel, hogy a platform pontos információkat nyújtson az alkalmazás folyamatos állapotáról. Az Application Health-mintavételek olyan egyéni Load Balancer-mintavételek, amelyek állapot-jelzésként használatosak. A méretezési csoport virtuálisgép-példányán futó alkalmazás reagálhat a külső HTTP-vagy TCP-kérelmekre, ami azt jelzi, hogy kifogástalan-e. Az egyéni Load Balancer mintavételek működésével kapcsolatos további információkért lásd: a [Load Balancer](../load-balancer/load-balancer-custom-probe-overview.md)-tesztek megismerése. Service Fabric méretezési csoportok nem támogatják az alkalmazás állapotának mintavételét. A nem Service Fabric méretezési csoportoknak az alkalmazás állapotának vagy az [alkalmazás állapotának kiterjesztésére](virtual-machine-scale-sets-health-extension.md)van Load Balancer szükségük.

Ha a méretezési csoport több elhelyezési csoport használatára van konfigurálva, akkor standard Load Balancert [](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) használó mintavételt kell használnia.

### <a name="configuring-a-custom-load-balancer-probe-as-application-health-probe-on-a-scale-set"></a>Egyéni Load Balancer mintavétel konfigurálása alkalmazás állapotának mintavétele egy méretezési csoporton
Ajánlott eljárásként hozzon létre egy Load Balancer-mintavételt explicit módon a méretezési csoport állapotához. Egy meglévő HTTP-mintavételi vagy TCP-mintavételi végpont is használható, de az állapot-mintavétel a hagyományos terheléselosztó-mintavételtől eltérő viselkedést igényelhet. Például a hagyományos terheléselosztó-mintavétel nem megfelelő állapotba kerülhet, ha a példány terhelése túl magas, de ez nem lenne helyénvaló a példány állapotának meghatározására egy automatikus operációsrendszer-frissítés során. Konfigurálja a mintavételt úgy, hogy a magas szondázás sebessége kevesebb, mint két perc.

A terheléselosztó mintavételét a méretezési csoport *networkProfile* lehet hivatkozni, és a következőkkel társítható belső vagy nyilvános terhelésű terheléselosztó használatával:

```json
"networkProfile": {
  "healthProbe" : {
    "id": "[concat(variables('lbId'), '/probes/', variables('sshProbeName'))]"
  },
  "networkInterfaceConfigurations":
  ...
}
```

> [!NOTE]
> Ha a Service Fabric használatával automatikus operációsrendszer-frissítést használ, az új operációsrendszer-rendszerképet a frissítési tartomány frissíti a tartomány frissítésével, hogy fenntartsa a Service Fabric futó szolgáltatások magas rendelkezésre állását. Ha az operációs rendszer automatikus frissítését szeretné használni Service Fabric a fürtöt úgy kell konfigurálni, hogy az ezüst tartóssági szintet vagy ennél nagyobbat használjon. A Service Fabric-fürtök tartóssági jellemzőivel kapcsolatos további információkért tekintse meg [ezt](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)a dokumentációt.

### <a name="keep-credentials-up-to-date"></a>Hitelesítő adatok naprakészen tartása
Ha a méretezési csoport bármilyen hitelesítő adatot használ a külső erőforrások eléréséhez, például egy virtuálisgép-bővítményt, amely egy SAS-jogkivonat használatára van konfigurálva a Storage-fiókhoz, akkor győződjön meg arról, hogy a hitelesítő adatok frissülnek. Ha a hitelesítő adatok (beleértve a tanúsítványokat és a jogkivonatokat is) elévültek, a frissítés sikertelen lesz, és a virtuális gépek első kötege sikertelen állapotba kerül.

Az ajánlott lépések a virtuális gépek helyreállításához és az automatikus operációs rendszer frissítésének újbóli engedélyezéséhez, ha van erőforrás-hitelesítési hiba:

* A token (vagy más hitelesítő adatok) átadása a bővítmény (ek) ba.
* Győződjön meg arról, hogy a virtuális gépen belül a külső entitásokkal való kommunikációhoz használt hitelesítő adatok naprakészek.
* Frissítse a méretezési csoport modelljében lévő kiterjesztés (eke) t az új jogkivonatokkal.
* Telepítse a frissített méretezési készletet, amely frissíti az összes VM-példányt, beleértve a sikerteleneket is.

## <a name="using-application-health-extension"></a>Az Application Health bővítmény használata
Az alkalmazás állapotának kiterjesztése egy virtuálisgép-méretezési csoport példányain belül történik, és a virtuális gépek állapotáról szóló jelentések a méretezési csoport példányán belül vannak. A bővítményt beállíthatja a mintavételhez egy alkalmazás-végponton, és frissítheti az alkalmazás állapotát az adott példányon. A példány állapotát az Azure ellenőrzi, hogy a példány jogosult-e a frissítési műveletekre.

Mivel a bővítmény a virtuális gépen belülről jelenti az állapotot, a bővítmény olyan helyzetekben használható, ahol az olyan külső mintavételek, mint az alkalmazás-állapotú [](../load-balancer/load-balancer-custom-probe-overview.md)mintavételek (amelyek az egyéni Azure Load Balancer-mintavételeket használják) nem használhatók.

Az alkalmazás-állapot bővítmény a méretezési csoportokra több módon is üzembe helyezhető a [jelen cikkben](virtual-machine-scale-sets-health-extension.md#deploy-the-application-health-extension)található példákban részletezve.

## <a name="get-the-history-of-automatic-os-image-upgrades"></a>Az operációs rendszer rendszerképének automatikus frissítéseinek előzményeinek beolvasása
A méretezési csoporton végrehajtott legújabb verziófrissítések előzményeit a Azure PowerShell, az Azure CLI 2,0 vagy a REST API-k segítségével tekintheti meg. Az elmúlt két hónap során az elmúlt öt operációsrendszer-frissítési kísérlet előzményeit is megszerezheti.

### <a name="rest-api"></a>REST API
A következő példa a [REST API](/rest/api/compute/virtualmachinescalesets/getosupgradehistory) használatával vizsgálja meg az *myScaleSet* nevű méretezési csoport állapotát a *myResourceGroup*nevű erőforráscsoport-csoportban:

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/osUpgradeHistory?api-version=2018-10-01`
```

A GET hívás a következő példa kimenetéhez hasonló tulajdonságokat ad vissza:

```json
{
    "value": [
        {
            "properties": {
        "runningStatus": {
          "code": "RollingForward",
          "startTime": "2018-07-24T17:46:06.1248429+00:00",
          "completedTime": "2018-04-21T12:29:25.0511245+00:00"
        },
        "progress": {
          "successfulInstanceCount": 16,
          "failedInstanceCount": 0,
          "inProgressInstanceCount": 4,
          "pendingInstanceCount": 0
        },
        "startedBy": "Platform",
        "targetImageReference": {
          "publisher": "MicrosoftWindowsServer",
          "offer": "WindowsServer",
          "sku": "2016-Datacenter",
          "version": "2016.127.20180613"
        },
        "rollbackInfo": {
          "successfullyRolledbackInstanceCount": 0,
          "failedRolledbackInstanceCount": 0
        }
      },
      "type": "Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades",
      "location": "westeurope"
    }
  ]
}
```

### <a name="azure-powershell"></a>Azure PowerShell
Használja a [Get-AzVmss](/powershell/module/az.compute/get-azvmss) parancsmagot a méretezési csoport operációsrendszer-frissítési előzményeinek megtekintéséhez. A következő példa részletesen ismerteti, hogyan tekintheti át egy *myScaleSet* nevű méretezési csoport operációsrendszer-frissítési állapotát a *myResourceGroup*nevű erőforráscsoport:

```azurepowershell-interactive
Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -OSUpgradeHistory
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Az az [vmss Get-os-upgrade-History](/cli/azure/vmss#az-vmss-get-os-upgrade-history) paranccsal tekintheti meg a méretezési csoport operációsrendszer-frissítési előzményeit. Használja az Azure CLI-2.0.47 vagy újabb verzióját. A következő példa részletesen ismerteti, hogyan tekintheti át egy *myScaleSet* nevű méretezési csoport operációsrendszer-frissítési állapotát a *myResourceGroup*nevű erőforráscsoport:

```azurecli-interactive
az vmss get-os-upgrade-history --resource-group myResourceGroup --name myScaleSet
```

## <a name="how-to-get-the-latest-version-of-a-platform-os-image"></a>A platform operációsrendszer-lemezkép legújabb verziójának beszerzése

Az alábbi példákkal beszerezheti az operációs rendszer frissítésének támogatott verzióit a támogatott SKU-lemezeken:

### <a name="rest-api"></a>REST API
```
GET on `/subscriptions/subscription_id/providers/Microsoft.Compute/locations/{location}/publishers/{publisherName}/artifacttypes/vmimage/offers/{offer}/skus/{skus}/versions?api-version=2018-10-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
```azurepowershell-interactive
Get-AzVmImage -Location "westus" -PublisherName "Canonical" -Offer "UbuntuServer" -Skus "16.04-LTS"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
```azurecli-interactive
az vm image list --location "westus" --publisher "Canonical" --offer "UbuntuServer" --sku "16.04-LTS" --all
```

## <a name="manually-trigger-os-image-upgrades"></a>OPERÁCIÓSRENDSZER-rendszerkép frissítéseinek manuális elindítása
Ha engedélyezve van az operációs rendszer rendszerképének frissítése a méretezési csoporton, a méretezési csoporton nem szükséges manuálisan elindítani a rendszerkép-frissítéseket. Az operációs rendszer frissítése Orchestrator automatikusan alkalmazza a legújabb elérhető lemezkép-verziót a méretezési csoport példányaira manuális beavatkozás nélkül.

Bizonyos esetekben, amikor nem szeretné megvárni, hogy a Orchestrator a legújabb lemezképet alkalmazza, az alábbi példák használatával manuálisan aktiválhatja az operációsrendszer-lemezképek frissítését.

> [!NOTE]
> Az operációsrendszer-rendszerkép frissítéseinek manuális triggere nem biztosít Automatikus visszaállítási képességeket. Ha egy példány nem állítja helyre az állapotát egy frissítési művelet után, az előző operációsrendszer-lemez nem állítható vissza.

### <a name="rest-api"></a>REST API
Az [operációs rendszer upgrade](/rest/api/compute/virtualmachinescalesetrollingupgrades/startosupgrade) API-hívásával indítson el egy működés közbeni frissítést, hogy az összes virtuálisgép-méretezési csoport példányai a legújabb elérhető platform-lemezkép operációsrendszer-verzióra legyenek áthelyezve. Azokat a példányokat, amelyek már futtatják a legújabb elérhető operációsrendszer-verziót, nem érintettek. Az alábbi példa azt ismerteti, hogyan indíthatja el a működés közbeni operációs rendszer frissítését egy *myScaleSet* nevű méretezési csoporton az *myResourceGroup*nevű erőforráscsoport:

```
POST on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/osRollingUpgrade?api-version=2018-10-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
A [Start-AzVmssRollingOSUpgrade](/powershell/module/az.compute/Start-AzVmssRollingOSUpgrade) parancsmag használatával tekintse meg a méretezési csoport operációsrendszer-frissítési előzményeit. Az alábbi példa azt ismerteti, hogyan indíthatja el a működés közbeni operációs rendszer frissítését egy *myScaleSet* nevű méretezési csoporton az *myResourceGroup*nevű erőforráscsoport:

```azurepowershell-interactive
Start-AzVmssRollingOSUpgrade -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Az az [vmss Rolling-upgrade Start](/cli/azure/vmss/rolling-upgrade#az-vmss-rolling-upgrade-start) paranccsal tekintheti meg a méretezési csoport operációsrendszer-frissítési előzményeit. Használja az Azure CLI-2.0.47 vagy újabb verzióját. Az alábbi példa azt ismerteti, hogyan indíthatja el a működés közbeni operációs rendszer frissítését egy *myScaleSet* nevű méretezési csoporton az *myResourceGroup*nevű erőforráscsoport:

```azurecli-interactive
az vmss rolling-upgrade start --resource-group "myResourceGroup" --name "myScaleSet" --subscription "subscriptionId"
```

## <a name="deploy-with-a-template"></a>Üzembe helyezés sablonnal

A sablonok használatával olyan méretezési csoport helyezhető üzembe, amely a támogatott lemezképek (például [Ubuntu 16,04-LTS](https://github.com/Azure/vm-scale-sets/blob/master/preview/upgrade/autoupdate.json)) esetében automatikusan frissíti az operációs rendszer frissítéseit.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fvm-scale-sets%2Fmaster%2Fpreview%2Fupgrade%2Fautoupdate.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

## <a name="next-steps"></a>További lépések
Ha további példákat szeretne arról, hogyan használhatók az operációs rendszerek automatikus frissítése a méretezési csoportokkal, tekintse át a [GitHub](https://github.com/Azure/vm-scale-sets/tree/master/preview/upgrade)-tárházat.
