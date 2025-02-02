---
title: SQL Server virtuális gép regisztrálása az Azure-ban az SQL VM erőforrás-szolgáltatóval | Microsoft Docs
description: A kezelhetőség javítása érdekében regisztrálja SQL Server VM az SQL VM erőforrás-szolgáltatóval.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/24/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 552caf0f09dcfa291981ef73152cf4febfc4a840
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68882376"
---
# <a name="register-a-sql-server-virtual-machine-in-azure-with-the-sql-vm-resource-provider"></a>SQL Server virtuális gép regisztrálása az Azure-ban az SQL VM erőforrás-szolgáltatóval

Ez a cikk azt ismerteti, hogyan regisztrálhatók a SQL Server virtuális gépek (VM) az Azure-ban az SQL VM erőforrás-szolgáltatóval. 

SQL Server VM Azure Marketplace-rendszerkép üzembe helyezése a Azure Portal használatával automatikusan regisztrálja az SQL Server VM az erőforrás-szolgáltatóval. Ha úgy dönt, hogy egy Azure-beli virtuális gépen telepíti SQL Server önálló telepítését ahelyett, hogy kiválaszt egy rendszerképet az Azure Marketplace-ről, vagy ha egy egyéni virtuális merevlemezről SQL Server használatával telepít egy Azure-beli virtuális gépet, akkor regisztrálnia kell a SQL Server VM a következő erőforrás-szolgáltatóval: :

- **Megfelelőség**: A Microsoft-termékek használati feltételeinek megfelelően a felhasználóknak meg kell tudniuk adni a Microsoftnak, ha a [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)használják. Ehhez regisztrálnia kell az SQL VM erőforrás-szolgáltatóval. 

- A **szolgáltatás előnyei**: A SQL Server VM erőforrás-szolgáltatóval való regisztrálása feloldja az [automatizált javításokat](virtual-machines-windows-sql-automated-patching.md), az [automatizált biztonsági mentést](virtual-machines-windows-sql-automated-backup-v2.md), valamint a figyelési és kezelhetőségi képességeket. Emellett feloldja a [licencelési](virtual-machines-windows-sql-ahb.md) és a [kiadási](virtual-machines-windows-sql-change-edition.md) rugalmasságot is. Korábban ezek a funkciók csak az Azure Marketplace-en SQL Server VM lemezképek számára voltak elérhetők.

Az SQL-alapú virtuális gép erőforrás-szolgáltatójának használatához regisztrálnia kell az SQL VM erőforrás-szolgáltatót az előfizetésében is. Ezt a Azure Portal, az Azure CLI vagy a PowerShell használatával végezheti el. 

## <a name="prerequisites"></a>Előfeltételek

A SQL Server VM erőforrás-szolgáltatóval való regisztrálásához a következőkre lesz szüksége: 

- Egy [Azure-előfizetés](https://azure.microsoft.com/free/).
- Egy [SQL Server VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision). 
- Az [Azure CLI](/cli/azure/install-azure-cli) és a [PowerShell](/powershell/azure/new-azureps-module-az). 


## <a name="register-with-sql-vm-resource-provider"></a>Regisztrálás az SQL VM erőforrás-szolgáltatóval
Ha a [SQL Server IaaS-ügynök bővítmény](virtual-machines-windows-sql-server-agent-extension.md) nincs telepítve a virtuális gépen, akkor az egyszerűsített SQL Management mód megadásával regisztrálhat az SQL VM erőforrás-szolgáltatóval. Az egyszerűsített SQL Management mode-ban az SQL VM erőforrás-szolgáltatója [egyszerűsített módban](virtual-machines-windows-sql-server-agent-extension.md#install-in-lightweight-mode) telepíti az SQL IaaS bővítményt, és ellenőrzi SQL Server példány metaadatait; Ez nem fog újraindulni SQL Server szolgáltatás. Meg kell adnia SQL Server licenc típusát, ha az SQL VM erőforrás-szolgáltatót "TB" vagy "AHUB" néven regisztrálja.

Az SQL VM erőforrás-szolgáltató [egyszerű módban](virtual-machines-windows-sql-server-agent-extension.md#install-in-lightweight-mode) való regisztrálása biztosítja a megfelelőséget, és lehetővé teszi a rugalmas licencelést, valamint a helyi SQL Server kiadási frissítéseket. A feladatátvevő fürtök példányai és a többpéldányos központi telepítések csak kis-és nagyvállalati módban regisztrálhatók az SQL VM erőforrás-szolgáltatóval. A Azure Portal található útmutatást követve [teljes módba](virtual-machines-windows-sql-server-agent-extension.md#install-in-full-mode) frissíthet, és engedélyezheti az átfogó kezelhetőségi funkciót SQL Server újraindítással bármikor. 


# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
A következő kódrészlettel regisztrálhat az SQL VM erőforrás-szolgáltatóval, ha a SQL Server IaaS bővítmény már telepítve van a virtuális gépen. Meg kell adnia, hogy milyen típusú SQL Server-licencet szeretne regisztrálni az SQL VM erőforrás-szolgáltatónál: vagy utólagos elszámolású (`PAYG`) vagy Azure Hybrid Benefit (`AHUB`). 

Regisztrálja a SQL Server VM a következő PowerShell-kódrészlet használatával:

  ```powershell-interactive
     # Get the existing compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     # Register SQL VM with 'Lightweight' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='AHUB';sqlManagement='LightWeight'}  
  
  ```

# <a name="az-clitabbash"></a>[AZ PARANCSSORI FELÜLET](#tab/bash)

Fizetős kiadásokhoz (Enterprise vagy standard):

  ```azurecli-interactive
  # Register Enterprise or Standard self-installed VM in Lightweight mode

  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type AHUB 

  ```

Ingyenes kiadásokhoz (fejlesztői, web vagy Express):

  ```azurecli-interactive
  # Register Developer, Web, or Express self-installed VM in Lightweight mode

  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type PAYG 
  ```
---

Ha az SQL IaaS bővítmény már telepítve van a virtuális gépen, akkor az SQL VM erőforrás-szolgáltatóhoz való regisztrálás egyszerűen létrehoz egy Microsoft. SqlVirtualMachine/SqlVirtualMachines típusú metaadat-erőforrást. Az alábbi kódrészlettel regisztrálhat az SQL VM erőforrás-szolgáltatóval, ha az SQL IaaS bővítmény már telepítve van a virtuális gépen. Meg kell adnia SQL Server licenc típusát, ha az SQL VM erőforrás-szolgáltatót "TB" vagy "AHUB" néven regisztrálja.

  ```powershell-interactive
  # Get the existing  Compute VM
   $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
   # Register with SQL VM resource provider
   New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
      -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
      -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='AHUB'}
  ```


## <a name="register-sql-server-2008-or-2008-r2-on-windows-server-2008-vms"></a>Regisztrálja SQL Server 2008 vagy 2008 R2 rendszert Windows Server 2008 rendszerű virtuális gépeken

A Windows Server 2008 rendszerre telepített SQL Server 2008 és 2008 R2 az SQL VM erőforrás-szolgáltatón keresztül regisztrálható az [ügynök nélküli](virtual-machines-windows-sql-server-agent-extension.md) módban. Ez a beállítás biztosítja a megfelelőséget, és lehetővé teszi a SQL Server VM figyelését a Azure Portal korlátozott funkcionalitással.

Az alábbi táblázat a regisztráció során megadott paraméterek elfogadható értékeit részletezi:

| Paraméter | Elfogadható értékek                                 |
| :------------------| :--------------------------------------- |
| **sqlLicenseType** | `AHUB` vagy `PAYG`                    |
| **sqlImageOffer**  | `SQL2008-WS2008` vagy `SQL2008R2-WS2008`|
| &nbsp;             | &nbsp;                                   |


Ha regisztrálni szeretné a SQL Server 2008-es vagy 2008 R2-példányt a Windows Server 2008-példányon, használja a következő PowerShell-vagy az CLI-kódrészletet:  

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
  ```powershell-interactive
     # Get the existing compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
    New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
      -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
      -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='AHUB'; `
       sqlManagement='NoAgent';sqlImageSku='Standard';sqlImageOffer='SQL2008R2-WS2008'}
  ```

# <a name="az-clitabbash"></a>[AZ PARANCSSORI FELÜLET](#tab/bash)

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type AHUB --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008-WS2008R2
 ```

---

## <a name="verify-registration-status"></a>Regisztrációs állapot ellenőrzése
A Azure Portal, az Azure CLI vagy a PowerShell használatával ellenőrizheti, hogy a SQL Server VM már regisztrálva van-e az SQL VM erőforrás-szolgáltatóban. 

### <a name="azure-portal"></a>Azure Portal 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 
1. Lépjen a [SQL Server virtuális gépekre](virtual-machines-windows-sql-manage-portal.md).
1. Válassza ki a SQL Server VM a listából. Ha a SQL Server VM nem szerepel a listán, valószínűleg nincs regisztrálva az SQL VM erőforrás-szolgáltatónál. 
1. Tekintse meg az **állapot**alatt lévő értéket. Ha az állapot **sikeres**, akkor a SQL Server VM sikeresen regisztrálva lett az SQL VM erőforrás-szolgáltatónál. 

![SQL RP-regisztrációval rendelkező állapot ellenőrzése](media/virtual-machines-windows-sql-register-with-rp/verify-registration-status.png)

### <a name="command-line"></a>Parancssor

Ellenőrizze az aktuális SQL Server VM regisztrációs állapotot az az CLI vagy a PowerShell használatával. `ProvisioningState`azt mutatja `Succeeded` , hogy a regisztráció sikeres volt-e. 

# <a name="az-clitabbash"></a>[AZ PARANCSSORI FELÜLET](#tab/bash)


  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
 ```


# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

  ```powershell-interactive
  Get-AzResource -ResourceName <vm_name> -ResourceGroupName <resource_group> `
  -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines
  ```

---

A hiba azt jelzi, hogy a SQL Server VM nincs regisztrálva az erőforrás-szolgáltatónál. 

## <a name="register-the-sql-vm-resource-provider-with-a-subscription"></a>Az SQL VM erőforrás-szolgáltató regisztrálása előfizetéssel 

Ha regisztrálni szeretné a SQL Server VM az SQL VM erőforrás-szolgáltatóval, regisztrálnia kell az erőforrás-szolgáltatót az előfizetésében. Ezt a Azure Portal, az Azure CLI vagy a PowerShell használatával teheti meg.

### <a name="azure-portal"></a>Azure Portal

1. Nyissa meg a Azure Portalt, és válassza a **minden szolgáltatás**lehetőséget. 
1. Lépjen az előfizetések elemre, és válassza ki a kamat előfizetését.  
1. Az előfizetések lapon lépjen az **erőforrás-szolgáltatók**elemre. 
1. Adja meg az SQL-t a szűrőben az SQL-hez kapcsolódó erőforrás-szolgáltatók létrehozásához. 
1. Válassza a **Microsoft. SqlVirtualMachine** -szolgáltató **regisztrálása**, újbóli **regisztrálása**vagy **regisztrációjának** törlése lehetőséget a kívánt művelettől függően. 

![A szolgáltató módosítása](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)


### <a name="command-line"></a>Parancssor

Regisztrálja az SQL-alapú virtuális gép erőforrás-szolgáltatóját az Azure-előfizetéséhez az az CLI vagy a PowerShell használatával. 

# <a name="az-clitabbash"></a>[AZ PARANCSSORI FELÜLET](#tab/bash)
A következő kódrészlet regisztrálja az SQL VM erőforrás-szolgáltatót az Azure-előfizetésében. 

```azurecli-interactive
# Register the new SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

```powershell-interactive
# Register the new SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```
---

## <a name="remarks"></a>Megjegyzések

- Az SQL VM erőforrás-szolgáltató csak a Azure Resource Manager használatával telepített SQL Server virtuális gépeket támogatja. SQL Server a klasszikus modellen keresztül üzembe helyezett virtuális gépek nem támogatottak. 
- Az SQL VM erőforrás-szolgáltató csak a nyilvános felhőbe telepített SQL Server virtuális gépeket támogatja. A privát vagy kormányzati felhőbe való központi telepítések nem támogatottak. 
 

## <a name="frequently-asked-questions"></a>Gyakori kérdések 

**Regisztrálhatom a SQL Server VM az Azure Marketplace-en egy SQL Server rendszerképből kiépítve?**

Nem. A Microsoft automatikusan regisztrálja az Azure Marketplace-en SQL Server rendszerképből kiépített virtuális gépeket. Az SQL VM erőforrás-szolgáltatóval való regisztrálás csak akkor szükséges, ha a virtuális gép *nem* lett kiépítve az Azure Marketplace SQL Server rendszerképeiből, és SQL Server önálló telepítése történt.

**Az SQL VM-erőforrásszolgáltató minden ügyfél számára elérhető?** 

Igen. Az ügyfeleknek regisztrálniuk kell a SQL Server virtuális gépeket az SQL VM erőforrás-szolgáltatóval, ha nem használnak SQL Server rendszerképet az Azure Marketplace-ről, hanem önállóan telepített SQL Server, vagy ha az egyéni VHD-t hozták. Az összes típusú előfizetés (Direct, Nagyvállalati Szerződés és Cloud Solution Provider) tulajdonában lévő virtuális gépek regisztrálhatnak az SQL VM erőforrás-szolgáltatóval.

**Regisztrálni kell az SQL VM erőforrás-szolgáltatónál, ha a SQL Server VM már telepítve van a SQL Server IaaS bővítmény?**

Ha a SQL Server VM öntelepített, és nem az Azure Marketplace-en lévő SQL Server lemezképből lett kiépítve, akkor is regisztrálnia kell az SQL VM erőforrás-szolgáltatóval, ha telepítette a SQL Server IaaS bővítményt. Az SQL virtuális gép erőforrás-szolgáltatójának regisztrálása egy új, Microsoft. SqlVirtualMachines típusú erőforrást hoz létre. A SQL Server IaaS bővítmény telepítése nem hozza létre az erőforrást.

**Mi az alapértelmezett felügyeleti mód az SQL VM erőforrás-szolgáltatóval való regisztráláskor?**

Az SQL VM erőforrás-szolgáltatóval való regisztráláskor az alapértelmezett felügyeleti mód *megtelt*. Ha a SQL Server Management tulajdonság nincs beállítva az SQL VM erőforrás-szolgáltatóval való regisztráláskor, a mód teljes kezelhetőségként lesz beállítva. A virtuális gépre telepített SQL Server IaaS bővítmény az SQL VM erőforrás-szolgáltató teljes körű kezelhetőségi módban való regisztrálásának előfeltétele.

**Milyen előfeltételeket kell regisztrálni az SQL VM erőforrás-szolgáltatónál?**

Az SQL VM erőforrás-szolgáltatóhoz nem szükséges előfeltétel, hogy egyszerűsített módban vagy nem ügynök módban regisztráljanak. Az SQL VM erőforrás-szolgáltató teljes módban való regisztrálásának előfeltétele, hogy a SQL Server IaaS bővítmény telepítve legyen a virtuális gépen.

**Regisztrálhatok az SQL VM erőforrás-szolgáltatóval, ha nincs telepítve a SQL Server IaaS bővítmény a virtuális gépre?**

Igen, ha nem rendelkezik a virtuális gépre telepített SQL Server IaaS bővítménnyel, akkor az egyszerűsített felügyeleti módban regisztrálhatja az SQL VM erőforrás-szolgáltatót. A könnyű mód esetében az SQL VM erőforrás-szolgáltató egy konzol alkalmazást fogja használni a SQL Server példány verziójának és kiadásának ellenőrzéséhez. 

Az SQL VM erőforrás-szolgáltatóval való regisztráláskor az alapértelmezett SQL-kezelési mód megtelt. Ha az SQL-alapú virtuális gép erőforrás-szolgáltatójának regisztrálásakor nincs beállítva az SQL Management tulajdonság, a mód teljes Kezelhetőségként lesz beállítva. Ha a virtuális gépen telepítve van az SQL IaaS bővítmény, a teljes körű kezelhetőségi módban regisztrálnia kell az SQL VM erőforrás-szolgáltatót.

**Regisztrálja az SQL VM erőforrás-szolgáltatót az ügynök telepítése a virtuális gépre?**

Nem. Az SQL VM erőforrás-szolgáltatóval való regisztrálás csak új metaadat-erőforrást fog létrehozni. Nem telepíti az ügynököt a virtuális gépre.

A SQL Server IaaS bővítmény csak a teljes kezelhetőség engedélyezéséhez szükséges. Ha a kezelhetőségi módot könnyűről teljesre szeretné frissíteni, telepítse a SQL Server IaaS bővítményt, és újraindítja SQL Server.

**Regisztrálja a SQL Server VM erőforrás-szolgáltató újraindítási SQL Server a virtuális gépen?**

Nem. Az SQL VM erőforrás-szolgáltatóval való regisztrálás csak új metaadat-erőforrást fog létrehozni. Nem fog újraindulni SQL Server a virtuális gépen. 

SQL Server újraindítása csak a SQL Server IaaS bővítmény telepítéséhez szükséges. A teljes kezelhetőség engedélyezéséhez a SQL Server IaaS bővítményre van szükség. Ha a kezelhetőségi módot könnyűről teljesre szeretné frissíteni, telepítse a SQL Server IaaS bővítményt, és újraindítja SQL Server.

**Mi a különbség a könnyű és a nem ügynök felügyeleti módok között az SQL VM erőforrás-szolgáltatóval való regisztráláskor?** 

A nem ügynök felügyeleti mód csak SQL Server 2008 és SQL Server 2008 R2 rendszer esetén érhető el a Windows Server 2008-on. Ezen verziók esetében ez az egyetlen elérhető felügyeleti mód. A SQL Server összes többi verziója esetében a két elérhető kezelhetőségi mód könnyű és teljes. 

A nem ügynök üzemmódhoz az ügyfélnek meg kell adni SQL Server Version és Edition tulajdonságokat. A könnyű mód lekérdezi a virtuális gépet a SQL Server példány verziójának és kiadásának megkereséséhez.

**Az Azure CLI használatával regisztrálhatok az SQL VM-erőforrás-szolgáltatót könnyű vagy nem ügynök módban?**

Nem. A felügyeleti mód tulajdonság csak akkor érhető el, ha a Azure PowerShell használatával regisztrálja az SQL virtuális gép erőforrás-szolgáltatóját. Az Azure CLI nem támogatja a SQL Server kezelhetőségi tulajdonság beállítását. Mindig az SQL VM erőforrás-szolgáltatót regisztrálja az alapértelmezett módban, teljes kezelhetőséggel.

**Regisztrálhatok az SQL VM erőforrás-szolgáltatóval a SQL Server licenc típusának megadása nélkül?**

Nem. Az SQL Server licenc típusa nem választható tulajdonság az SQL VM erőforrás-szolgáltatóval való regisztráláskor. Az Azure CLI-vel és a PowerShell-lel az összes kezelhetőségi módban (nem ügynök, Lightweight és teljes) az SQL VM erőforrás-szolgáltatóhoz való regisztráláskor be kell állítania a SQL Server Azure Hybrid Benefit licencfeltételeket.

**Frissíthetem a SQL Server IaaS-bővítményt ügynök nélküli módból teljes módba?**

Nem. A kezelhetőségi mód teljes vagy egyszerű frissítése nem érhető el ügynök módban. Ez a Windows Server 2008 technikai korlátozása.

**Frissíthetem a SQL Server IaaS-bővítményt az egyszerűsített módból a teljes módba?**

Igen. A kezelhetőségi mód lightweightről teljesre történő frissítése a PowerShell vagy a Azure Portal használatával támogatott. SQL Server újraindítását igényli.

**Visszaválthatom a SQL Server IaaS-bővítményt a teljes módból a nem ügynök vagy az egyszerűsített felügyeleti módra?**

Nem. A SQL Server IaaS-bővítmény kezelhetőségi módjának lefokozása nem támogatott. A kezelhetőségi mód nem állítható vissza a teljes módból a könnyű vagy nem ügynök módba, és nem lehet leértékelni az egyszerűsített módból az ügynök módba. 

Ha módosítani szeretné a kezelhetőségi módot a teljes kezelhetőséggel, távolítsa el a SQL Server IaaS bővítményt. Ezután dobja el a Microsoft. SqlVirtualMachine erőforrást, és regisztrálja újra a SQL Server VM az SQL VM erőforrás-szolgáltatóval.

**Regisztrálhatok az SQL VM erőforrás-szolgáltatóval a Azure Portal?**

Nem. Az SQL VM erőforrás-szolgáltatóval való regisztráció nem érhető el a Azure Portal. Az Azure CLI vagy a PowerShell használatával az SQL VM erőforrás-szolgáltató teljes körű kezelhetőségi módban való regisztrálása támogatott. Ha az SQL VM erőforrás-szolgáltatót egyszerű vagy nem ügynöki kezelhetőségi módban regisztrálja, csak Azure PowerShell API-k támogatják.

**Regisztrálhatok egy virtuális gépet az SQL VM erőforrás-szolgáltatóval SQL Server telepítése előtt?**

Nem. Egy virtuális gépnek legalább egy SQL Server példányával sikeresen regisztrálnia kell az SQL VM erőforrás-szolgáltatóval. Ha a virtuális gépen nincs SQL Server példány, az új Microsoft. SqlVirtualMachine erőforrás sikertelen állapotba kerül.

**Regisztrálhatok egy virtuális gépet az SQL VM erőforrás-szolgáltatóval, ha több SQL Server példány is van?**

Igen. Az SQL virtuális gép erőforrás-szolgáltatója csak egy SQL Server példányt fog regisztrálni. Az SQL virtuális gép erőforrás-szolgáltatója több példány esetén regisztrálja az alapértelmezett SQL Server példányt. Ha nincs alapértelmezett példány, akkor a rendszer csak az egyszerűsített módban történő regisztrációt támogatja. A lightweightről teljes kezelhetőségi üzemmódra való frissítéshez vagy az alapértelmezett SQL Server példánynak léteznie kell, vagy a virtuális gépnek csak egy névvel ellátott SQL Server példánynak kell lennie.

**Regisztrálhatok egy SQL Server feladatátvevő fürt példányát az SQL VM erőforrás-szolgáltatóval?**

Igen. Az Azure-beli virtuális gépeken SQL Server feladatátvevő fürt példányai egyszerűsített módban regisztrálhatók az SQL VM erőforrás-szolgáltatóban. SQL Server feladatátvevő fürt példányai azonban nem frissíthetők teljes kezelhetőségi módba.

**Regisztrálhatom a virtuális gépet az SQL VM erőforrás-szolgáltatóval, ha az Always On rendelkezésre állási csoport konfigurálva van?**

Igen. Az SQL VM erőforrás-szolgáltatóval nem lehet SQL Server példányt regisztrálni egy Azure-beli virtuális gépen, ha részt vesz egy always on rendelkezésre állási csoport konfigurációjában.

## <a name="next-steps"></a>További lépések

További információkért tekintse át a következő cikkeket: 

* [Windows rendszerű virtuális gépek SQL Server áttekintése](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows rendszerű virtuális gépen SQL Server gyakori kérdések](virtual-machines-windows-sql-server-iaas-faq.md)
* [A Windows rendszerű virtuális gépek SQL Server díjszabási útmutatója](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows rendszerű virtuális gépen SQL Server kibocsátási megjegyzései](virtual-machines-windows-sql-server-iaas-release-notes.md)
