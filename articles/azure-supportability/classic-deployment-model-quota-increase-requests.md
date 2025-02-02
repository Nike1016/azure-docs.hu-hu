---
title: Klasszikus Azure üzembe helyezési modell | Microsoft Docs
description: Klasszikus Azure üzembe helyezési modell
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/20/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 70ad44eade871d52591014ee24e645b95c52f1e5
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234859"
---
# <a name="classic-deployment-model"></a>Klasszikus üzemi modell

A klasszikus üzemi modell a régebbi generációs Azure üzembe helyezési mód, amely a virtuális gépekre és a virtuálisgép-méretezési csoportokra vonatkozó globális vCPU-kvóta korlátozását alkalmazza. A klasszikus üzemi modell használata már nem ajánlott, és most már a Resource Manager-modell váltja fel. Ha többet szeretne megtudni a két üzembe helyezési modellről és a Resource Manager előnyeiről, tekintse meg a Resource Manager-alapú üzemi modell lapot. Új előfizetés létrehozásakor a rendszer a vCPU alapértelmezett kvótáját rendeli hozzá. Amikor új virtuális gépet kíván üzembe helyezni a klasszikus üzemi modell használatával, az összes régió új és meglévő vCPU-használatának összege nem haladhatja meg a klasszikus üzemi modellhez jóváhagyott vCPU-kvótát. További információ az [Azure-előfizetések és-szolgáltatások korlátozásait ismertető oldalon](https://aka.ms/quotalimits) található kvótákkal kapcsolatban

A klasszikus üzembe helyezési modellhez a Súgó + támogatás panelen vagy a portálon a használat + kvóta panelen vCPU-korlátot kérhet.

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-the-help--support-blade"></a>Kérelem/virtuálisgép-sorozat vCPU kvótájának növekedése az előfizetés szintjén a **Súgó + támogatás** panelen

Az alábbi útmutatást követve hozzon létre egy támogatási kérést az Azure "Súgó + támogatás" paneljén, amely a Azure Portal érhető el. 

1. Válassza https://portal.azure.com a **Súgó + támogatás**lehetőséget.

   ![Súgó és támogatás](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  Válassza az **Új támogatási kérelem** lehetőséget. 

      ![Új támogatási kérelem](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. A probléma típusa legördülő menüben válassza a **szolgáltatás és előfizetés korlátai (kvóták)** lehetőséget.

   ![Probléma típusa legördülő lista](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Válassza ki az előfizetést, amelynek a kvótáját emelni szeretné.

   ![Előfizetés-hírolvasó kiválasztása](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. Válassza a **számítási-VM (magok-vCPU) előfizetési korlátot** a **kvóta típusa** legördülő menüben. 

   ![Kvóta típusának kiválasztása](./media/resource-manager-core-quotas-request/select-quota-type.png)

6. A **probléma részletei**területen további információkat adhat meg a kérés feldolgozásához a **részletek megadása**lehetőségre kattintva.

   ![Részletek megadása](./media/resource-manager-core-quotas-request/provide-details.png)

7. A **kvóta részletei** panelen válassza a klasszikus lehetőséget, és válasszon egy helyet.

   ![Kvóta részletei – DM](./media/resource-manager-core-quotas-request/quota-details-classic.png)

8. Válassza ki a növekményt igénylő **SKU** -családokat. 

   ![Termékváltozat-család](./media/resource-manager-core-quotas-request/sku-family-classic.png)

9. Adja meg az előfizetéshez hasonló új korlátozásokat. Egy sor eltávolításához törölje az SKU-t a SKU-család legördülő menüből, vagy kattintson az Elvetés "x" ikonra. Miután megadta a kívánt kvótát az egyes SKU-családokhoz, kattintson a Save (Mentés) gombra, **és folytassa** a kvóta részletei panelen a támogatási kérések létrehozásához.

   ![Új korlátok](./media/resource-manager-core-quotas-request/new-limits-classic.png)

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-usages--quota-blade"></a>Kérelem/virtuálisgép-sorozat vCPU kvótájának növekedése az előfizetési szinten a használat és a **kvóta** panelen

Az alábbi útmutatást követve hozzon létre egy támogatási kérést az Azure-ban a Azure Portalben elérhető "használati + kvóta" panelen. 

1. Válassza https://portal.azure.com ki az előfizetések elemet.

   ![Előfizetések](./media/resource-manager-core-quotas-request/subscriptions.png)

2. Válassza ki az előfizetést, amelynek a kvótáját emelni szeretné.

   ![Előfizetés kiválasztása](./media/resource-manager-core-quotas-request/select-subscription.png)

3. **Használat + kvóták** kiválasztása

   ![Használat és kvóták kiválasztása](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. A jobb felső sarokban válassza a **kérelem növekedés**lehetőséget.

   ![Kérések növekedése](./media/resource-manager-core-quotas-request/request-increase.png)

5. Válassza a **számítási-VM (magok-vCPU) előfizetési korlátot** az árajánlat típusaként. 

   ![Űrlap kitöltése](./media/resource-manager-core-quotas-request/select-quota-type.png)
   
6. A **probléma részletei**területen további információkat adhat meg a kérés feldolgozásához a **részletek megadása**lehetőségre kattintva.

   ![Részletek megadása](./media/resource-manager-core-quotas-request/provide-details.png)

7. A **kvóta részletei** panelen válassza a klasszikus lehetőséget, és válasszon egy helyet.

   ![Kvóta részletei – DM](./media/resource-manager-core-quotas-request/quota-details-classic.png)

8. Válassza ki a növekményt igénylő **SKU** -családokat. 

   ![Termékváltozat-család](./media/resource-manager-core-quotas-request/sku-family-classic.png)

9. Adja meg az előfizetéshez hasonló új korlátozásokat. Egy sor eltávolításához törölje az SKU-t a SKU-család legördülő menüből, vagy kattintson az Elvetés "x" ikonra. Miután megadta a kívánt kvótát az egyes SKU-családokhoz, kattintson a Save (Mentés) gombra, **és folytassa** a kvóta részletei panelen a támogatási kérések létrehozásához.

   ![Új korlátok](./media/resource-manager-core-quotas-request/new-limits-classic.png)

