---
title: Vagy a Service Fabric-fürt skálázása |} A Microsoft Docs
description: Az egyes csomópontok és virtuálisgép-méretezési csoportokra vonatkozó automatikus méretezési szabályok beállításával az igény szerint méretezheti a Service Fabric-fürtöt az igényeknek megfelelően. Adja hozzá, vagy távolíthat el csomópontokat a Service Fabric-fürt
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: aeb76f63-7303-4753-9c64-46146340b83d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/12/2019
ms.author: atsenthi
ms.openlocfilehash: b1b3c0e6440212474bf356d4204c0dd91c1491fa
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599894"
---
# <a name="scale-a-cluster-in-or-out"></a>Fürt horizontális fel- és leskálázása

> [!WARNING]
> A méretezés előtt olvassa el ezt a szakaszt

Az alkalmazás terhelését szándékos tervezést igényel, szinte mindig hosszabb ideig tart, mint egy órát éles környezethez és igényel, hogy jobban megismerhesse a számítási feladatok és üzleti környezet; forrás méretezési számítási erőforrásokat valójában soha nem végzett előtt ezt a tevékenységet, ha ajánlott első lépésként kódmintáiban [tervezési megfontolások a Service Fabric-fürt kapacitása](service-fabric-cluster-capacity.md), ez a dokumentum további része a folytatás előtt. Ez a javaslat nem kívánt LiveSite problémák elkerülése érdekében, és emellett ajánlott sikerült tesztelni a műveletek végrehajtásához egy nem éles környezetben ellen dönt. Bármikor lehet [éles problémák jelentése és az Azure-ban fizetős támogatási kérelem](service-fabric-support.md#report-production-issues-or-request-paid-support-for-azure). Hajtsa végre ezeket a műveleteket, amelyek rendelkeznek a megfelelő környezet számára lefoglalt mérnökök Ez a cikk azt ismerteti, a méretezési műveletek, de kell döntse el, és a használati esetekhez; megfelelő mely műveletek megismeréséhez a méretezési csoport (Processzor és a Storage, memória), hogy milyen erőforrásokat például milyen irányba (függőleges vagy vízszintes), és milyen műveleteket (erőforrás-sablon telepítési Portal, PowerShell vagy a parancssori felületen).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scale-a-service-fabric-cluster-in-or-out-using-auto-scale-rules-or-manually"></a>Méretezheti a Service Fabric-fürt a- vagy leskálázása automatikus skálázási szabályok használatával vagy manuálisan
Virtuálisgép-méretezési csoportok olyan számítási Azure-erőforrások üzembe helyezése és kezelése a virtuális gépek csoportként gyűjtemény használható. Meghatározott Service Fabric-fürtök minden csomóponttípusa egy külön virtuálisgép-méretezési csoportban, van beállítva. Mindegyik csomóponttípus majd leskálázható vagy el egymástól függetlenül, más-más részhalmazához nyitott portokkal rendelkezik, és eltérő kapacitásmetrikái. További információ a [Service Fabric Node types](service-fabric-cluster-nodetypes.md) dokumentumban olvasható. Mivel a fürtben lévő Service Fabric csomópontok virtuálisgép-méretezési csoportokból állnak a háttérben, be kell állítania az automatikus skálázási szabályokat minden egyes csomópont típus/virtuálisgép-méretezési csoport számára.

> [!NOTE]
> Az előfizetés hozzáadása az új virtuális gépeket, a fürtöt alkotó elég mag kell rendelkeznie. Nem történik modell ellenőrzés jelenleg, így üzembe helyezési idő hibák, ha bármelyik kvótát az újrapróbálkozásig. Egyetlen csomóponttípus; is nem haladhatja meg egyszerűen 100 csomópont csoportonként. Adja hozzá a VMSS eléréséhez a célzott méretezési csoport, és nincs automatikus méretezést is szükség lehet automagically VMSS a hozzáadása. VMSS a helyszíni ad hozzá egy élő fürthöz egy feladat, és általában ez azt eredményezi a felhasználók a megfelelő csomóponttípusok kiosztott; létrehozáskor az új fürtök kiépítése [fürtkapacitás tervezése](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) ennek megfelelően. 
> 
> 

## <a name="choose-the-node-typevirtual-machine-scale-set-to-scale"></a>Csomópont típusa/virtuális gép méretezése méretezési kiválasztása
Jelenleg nem adhat meg az automatikus skálázási szabályok virtuálisgép-méretezési csoportok a portál segítségével Service Fabric-fürt létrehozása, ezért várjuk a csomóponttípusok a listában, majd adja hozzá automatikus skálázási szabályok azokat használhatja az Azure Powershellt (1.0 +).

A fürtöt alkotó virtuálisgép-méretezési listájának lekéréséhez futtassa a következő parancsmagokat:

```powershell
Get-AzResource -ResourceGroupName <RGname> -ResourceType Microsoft.Compute/VirtualMachineScaleSets

Get-AzVmss -ResourceGroupName <RGname> -VMScaleSetName <virtual machine scale set name>
```

## <a name="set-auto-scale-rules-for-the-node-typevirtual-machine-scale-set"></a>Automatikus méretezési szabályok beállítása a csomópont típusa/virtuálisgép-méretezési csoport számára
Ha a fürt több csomópont-típussal rendelkezik, akkor ismételje meg ezt minden olyan csomópont-típus/virtuálisgép-méretezési csoport esetében, amelyet szeretne méretezni (be vagy ki). Vegye figyelembe a szükséges csomópontok számát, mielőtt beállítja az automatikus skálázást. Az elsődleges csomóponttípushoz megadott minimálisan szükséges csomópontszámot a kiválasztott megbízhatósági szint határozza meg. Tudjon meg többet [megbízhatósági szintek](service-fabric-cluster-capacity.md).

> [!NOTE]
> Az elsődleges csomópont leskálázást kisebb, mint a minimális számú ügyeljen, írja be a fürt nem stabil, vagy leállásához azt. Az alkalmazások és a rendszer szolgáltatásokra az adatvesztést eredményezhet.
> 
> 

Az automatikus méretezési funkció jelenleg nem határozzák meg, hogy az alkalmazások a előfordulhat, hogy a Service Fabric jelentéskészítés terhelés. Így az automatikus skálázás kap tisztán határozzák meg a teljesítményszámlálók, amelyek mindegyike a virtuális gép által kibocsátott vannak jelenleg méretezési csoport példányaihoz.  

A [virtuálisgép-méretezési csoportokhoz tartozó automatikus méretezés beállításához](../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md)kövesse az alábbi utasításokat.

> [!NOTE]
> A leskálázási forgatókönyvekben, ha a csomópont típusa nem [][durability] rendelkezik az arany vagy ezüst tartóssági szinttel, a [Remove-ServiceFabricNodeState parancsmagot](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate) a megfelelő csomópont nevével kell meghívni. A bronz tartósság esetében nem ajánlott egyszerre egynél több csomópontot lekicsinyíteni.
> 
> 

## <a name="manually-add-vms-to-a-node-typevirtual-machine-scale-set"></a>Virtuális gépek manuális felvétele a csomópont típusú/virtuálisgép-méretezési csoportba

A horizontális felskálázás során további virtuálisgép-példányokat ad a méretezési csoporthoz. Ezek a példányok lesznek a Service Fabric által használt csomópontok. A Service Fabric tudja, ha a méretezési csoport új példányokkal bővül (felskálázással), és automatikusan ennek megfelelően jár el. 

> [!NOTE]
> A virtuális gépek hozzáadása időt vesz igénybe, ezért ne várja meg, hogy a hozzáadások azonnaliek legyenek. Ezért érdemes előre megtervezni a kapacitást, hogy a rendszer több mint 10 percet engedélyezzen, mielőtt a virtuális gépek kapacitása elérhető legyen a replikák/szolgáltatási példányok elhelyezéséhez.
> 

### <a name="add-vms-using-a-template"></a>Virtuális gépek hozzáadása sablon használatával
A virtuális gépek számának módosításához kövesse a gyors üzembe helyezési [sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) katalógusának mintáját/utasításait. 

### <a name="add-vms-using-powershell-or-cli-commands"></a>Virtuális gépek hozzáadása a PowerShell vagy a parancssori felület parancsaival
A következő kód név alapján megkeres egy méretezési csoportot, és 1-gyel növeli a **kapacitását**.

```powershell
$scaleset = Get-AzVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity += 1

Update-AzVmss -ResourceGroupName $scaleset.ResourceGroupName -VMScaleSetName $scaleset.Name -VirtualMachineScaleSet $scaleset
```

Ez a kód 6-ra állítja a kapacitást.

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 6
```

## <a name="manually-remove-vms-from-a-node-typevirtual-machine-scale-set"></a>Virtuális gépek manuális eltávolítása csomópont típusú vagy virtuálisgép-méretezési csoportból
Ha egy csomópont típusú méretezést végez, távolítsa el a virtuálisgép-példányokat a méretezési csoportból. Ha a csomópont típusa bronz tartóssági szint, Service Fabric nem ismeri, hogy mi történt, és azt jelenti, hogy egy csomópont eltűnt. A Service Fabric ekkor a fürt hibás állapotát jelzi. A helytelen állapot megakadályozása érdekében explicit módon el kell távolítania a csomópontot a fürtből, és el kell távolítania a csomópont állapotát.

A Service Fabric rendszerszolgáltatások a fürt elsődleges csomópont-típusa alatt futnak. Az elsődleges csomópont típusának skálázásakor soha ne méretezze át a példányok számát a megbízhatósági szinteknél kisebb [](service-fabric-cluster-capacity.md) értékre. 
 
Az állapotalapú szolgáltatások szüksége lesz egy bizonyos számú csomópont mindig ki lehet a rendelkezésre állás fenntartása, és a szolgáltatás állapotának megőrzése. Jelenleg a nagyon minimális szüksége lesz a csomópontok számát a partíció szolgáltatás a cél replika set száma egyenlő.

### <a name="remove-the-service-fabric-node"></a>A Service Fabric-csomópont eltávolítása

A csomópont-állapot manuális eltávolításának lépései csak a *bronz* tartóssági szintű csomópont-típusokra vonatkoznak.  Az *ezüst* és az *arany* tartóssági szinten ezeket a lépéseket a platform automatikusan végrehajtja. További információ a tartósságról: [Service Fabric a fürt kapacitásának][durability]megtervezése.

Annak érdekében, hogy a fürt csomópontjainak megoszlása egyenletes legyen a frissítési és tartalék tartományokban, illetve ezáltal egyenletesen legyen a kihasználtságuk, elsőként a legutóbb létrehozott csomópontot kell eltávolítani. Más szóval a csomópontokat létrehozásuk fordított sorrendjében kell eltávolítani. A legutóbb létrehozott csomópont rendelkezik a legnagyobb `virtual machine scale set InstanceId` tulajdonságértékkel. Az alábbi kódpéldák a legutóbb létrehozott csomópontot adják vissza.

```powershell
Get-ServiceFabricNode | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending | Select-Object -First 1
```

```azurecli
sfctl node list --query "sort_by(items[*], &name)[-1]"
```

A Service Fabric-fürtnek értesülnie kell róla, hogy ez a csomópont el lesz távolítva. Három lépést kell végrehajtania:

1. Tiltsa le a csomópontot, hogy ezentúl ne replikálja az adatokat.  
PowerShell: `Disable-ServiceFabricNode`  
sfctl: `sfctl node disable`

2. Állítsa le a csomópontot, hogy a Service Fabric-futtatókörnyezet szabályszerűen álljon le, és az alkalmazás kapjon egy megszakítási kérelmet.  
PowerShell: `Start-ServiceFabricNodeTransition -Stop`  
sfctl: `sfctl node transition --node-transition-type Stop`

2. Távolítsa el a csomópontot a fürtről.  
PowerShell: `Remove-ServiceFabricNodeState`  
sfctl: `sfctl node remove-state`

A fenti három lépés alkalmazása után a csomópont eltávolítható a méretezési csoportból. Ha a bronzon kívül bármilyen tartóssági [][durability]szintet használ, ezeket a lépéseket a méretezési csoport példányának eltávolításakor végezze el.

A következő kódrészlet lekérdezi az utolsó létrehozott csomópontot, majd letiltja, leállítja, és eltávolítja a fürtről.

```powershell
#### After you've connected.....
# Get the node that was created last
$node = Get-ServiceFabricNode | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending | Select-Object -First 1

# Node details for the disable/stop process
$nodename = $node.NodeName
$nodeid = $node.NodeInstanceId

$loopTimeout = 10

# Run disable logic
Disable-ServiceFabricNode -NodeName $nodename -Intent RemoveNode -TimeoutSec 300 -Force

$state = Get-ServiceFabricNode | Where-Object NodeName -eq $nodename | Select-Object -ExpandProperty NodeStatus

while (($state -ne [System.Fabric.Query.NodeStatus]::Disabled) -and ($loopTimeout -ne 0))
{
    Start-Sleep 5
    $loopTimeout -= 1
    $state = Get-ServiceFabricNode | Where-Object NodeName -eq $nodename | Select-Object -ExpandProperty NodeStatus
    Write-Host "Checking state... $state found"
}

# Exit if the node was unable to be disabled
if ($state -ne [System.Fabric.Query.NodeStatus]::Disabled)
{
    Write-Error "Disable failed with state $state"
}
else
{
    # Stop node
    $stopid = New-Guid
    Start-ServiceFabricNodeTransition -Stop -OperationId $stopid -NodeName $nodename -NodeInstanceId $nodeid -StopDurationInSeconds 300

    $state = (Get-ServiceFabricNodeTransitionProgress -OperationId $stopid).State
    $loopTimeout = 10

    # Watch the transaction
    while (($state -eq [System.Fabric.TestCommandProgressState]::Running) -and ($loopTimeout -ne 0))
    {
        Start-Sleep 5
        $state = (Get-ServiceFabricNodeTransitionProgress -OperationId $stopid).State
        Write-Host "Checking state... $state found"
    }

    if ($state -ne [System.Fabric.TestCommandProgressState]::Completed)
    {
        Write-Error "Stop transaction failed with $state"
    }
    else
    {
        # Remove the node from the cluster
        Remove-ServiceFabricNodeState -NodeName $nodename -TimeoutSec 300 -Force
    }
}
```

Az alábbi **sfctl** kódban a következő parancs kéri le a legutóbb létrehozott csomópont **node-name** értékét: `sfctl node list --query "sort_by(items[*], &name)[-1].name"`

```azurecli
# Inform the node that it is going to be removed
sfctl node disable --node-name _nt1vm_5 --deactivation-intent 4 -t 300

# Stop the node using a random guid as our operation id
sfctl node transition --node-instance-id 131541348482680775 --node-name _nt1vm_5 --node-transition-type Stop --operation-id c17bb4c5-9f6c-4eef-950f-3d03e1fef6fc --stop-duration-in-seconds 14400 -t 300

# Remove the node from the cluster
sfctl node remove-state --node-name _nt1vm_5
```

> [!TIP]
> A következő **sfctl**-lekérdezésekkel ellenőrizheti az egyes lépések állapotát
>
> **Inaktiválási állapot ellenőrzése**
> `sfctl node list --query "sort_by(items[*], &name)[-1].nodeDeactivationInfo"`
>
> **Leállítási állapot ellenőrzése**
> `sfctl node list --query "sort_by(items[*], &name)[-1].isStopped"`
>

### <a name="scale-in-the-scale-set"></a>A méretezési csoport horizontális leskálázása

Most, hogy a Service Fabric-csomópont el lett távolítva a fürtből, a virtuálisgép-méretezési csoport leskálázható. Az alábbi példa 1-gyel csökkenti a méretezési csoport kapacitását.

```powershell
$scaleset = Get-AzVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity -= 1

Update-AzVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm -VirtualMachineScaleSet $scaleset
```

Ez a kód a kapacitást 5-re állítja.

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 5
```

## <a name="behaviors-you-may-observe-in-service-fabric-explorer"></a>Azt is megfigyelheti a Service Fabric Explorerben viselkedések
Amikor a fürt vertikális a Service Fabric Explorert a csomópontok (virtuális gép méretezési csoport példányaihoz) a fürt részét képező fogja tartalmazni.  Horizontális a fürt működik, megjelenik a nem megfelelő állapot jelenik meg, ha meghívja a eltávolított csomópontra/Virtuálisgép-példány [Remove-ServiceFabricNodeState cmd](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate) csomópontok megfelelő névvel.   

Itt van ez a viselkedés a magyarázata.

A Service Fabric Explorert a felsorolt csomópontokat tartalmazza milyen a Service Fabric-rendszerszolgáltatások tükre (FM kifejezetten) a fürt kellett/rendelkezik csomópontok ismer. Állítsa a virtuálisgép-méretezési csoport horizontális, a virtuális gép törölve lett, de FM rendszerszolgáltatás fenyegetésként észlel továbbra is, hogy a csomópont (amely a virtuális gép, amely törölve lett lett leképezve) fog érkezni. Ezért a Service Fabric Explorer továbbra is megjeleníti a csomóponton (bár az állapot vagy hiba ismeretlen is lehet).

Annak érdekében, hogy, hogy amikor eltávolít egy virtuális Gépet eltávolít egy csomópontot, két lehetősége van:

1. Válassza ki a fürthöz, így Ön az infrastruktúra integrációs csomóponttípus, arany és ezüst tartóssági szintet. Amely majd automatikusan eltávolítja a csomópontot a rendszerállapot-szolgáltatások (FM) a horizontális.
Tekintse meg [részleteit itt tartóssági szint](service-fabric-cluster-capacity.md)

2. Miután a Virtuálisgép-példány mérete, meg kell hívnia a [Remove-ServiceFabricNodeState parancsmag](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate).

> [!NOTE]
> Service Fabric-fürtök bizonyos mennyiségű csomópontokat folyamatosan rendelkezésre állását és állapot - nevezik "a kvórum fenntartása." megőrzése érdekében megkövetelte a szükséges Így is a fürtben lévő összes gép leállítása, kivéve, ha először végrehajtása után általában nem biztonságos egy [teljes biztonsági mentést az állapot](service-fabric-reliable-services-backup-restore.md).
> 
> 

## <a name="next-steps"></a>További lépések
Olvassa el a következő fürtkapacitás tervezése, a fürt frissítése, és a szolgáltatások particionálása is ismerteti:

* [A fürt kapacitásának megtervezése](service-fabric-cluster-capacity.md)
* [Fürt frissítése](service-fabric-cluster-upgrade.md)
* [Állapotalapú szolgáltatások partíció a maximális méret](service-fabric-concepts-partitioning.md)

<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-up-down/BrowseServiceFabricClusterResource.png
[ClusterResources]: ./media/service-fabric-cluster-scale-up-down/ClusterResources.png

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
