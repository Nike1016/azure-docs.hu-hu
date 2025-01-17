---
title: SQL Server rendelkezésre állási csoportok – Azure Virtual Machines – áttekintés | Microsoft Docs
description: Ez a cikk SQL Server rendelkezésre állási csoportokat mutatja be az Azure Virtual Machines szolgáltatásban.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 601eebb1-fc2c-4f5b-9c05-0e6ffd0e5334
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/13/2017
ms.author: mikeray
ms.openlocfilehash: 4a65465528ea2ffd8ba7af705d92efbb23a96d9e
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883461"
---
# <a name="introducing-sql-server-always-on-availability-groups-on-azure-virtual-machines"></a>SQL Server always on rendelkezésre állási csoportok bemutatása Azure-beli virtuális gépeken #

Ez a cikk SQL Server rendelkezésre állási csoportokat mutatja be az Azure Virtual Machines-on. 

Az Azure Virtual Machines always on rendelkezésre állási csoportok hasonlók a helyszíni always on rendelkezésre állási csoportokhoz. További információ: Always [on rendelkezésre állási csoportok (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx). 

A diagram az Azure Virtual Machines teljes SQL Server rendelkezésre állási csoportjának részeit mutatja be.

![Rendelkezésreállási csoport](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

Egy rendelkezésre állási csoport fő különbsége az Azure-Virtual Machines az, hogy az Azure-beli virtuális gépekhez [terheléselosztó](../../../load-balancer/load-balancer-overview.md)szükséges. A terheléselosztó tárolja a rendelkezésre állási csoport figyelő IP-címeit. Ha egynél több rendelkezésre állási csoporttal rendelkezik, a csoportoknak figyelőre van szükségük. Egy terheléselosztó több figyelőt is támogat.

Emellett egy Azure IaaS VM vendég feladatátvevő fürtön egyetlen hálózati ADAPTERt (fürtcsomópont) és egyetlen alhálózatot is ajánlunk. Az Azure-hálózatok fizikai redundanciával rendelkeznek, így nincs szükség további hálózati adapterre és alhálózatra az Azure IaaS virtuális gépek vendégfürtjén. Bár a fürtellenőrzési jelentés figyelmeztetést küld, amely szerint a csomópontok csak egyetlen hálózaton érhetők el, ez a figyelmeztetés nyugodtan figyelmen kívül hagyható az Azure IaaS virtuális gépek vendég feladatátvevő fürtjein. 

A redundancia és a magas rendelkezésre állás növeléséhez a SQL Server virtuális gépeknek ugyanahhoz a [rendelkezésre állási csoporthoz](virtual-machines-windows-portal-sql-availability-group-prereq.md#create-availability-sets)vagy különböző [rendelkezésre állási zónákhoz](/azure/availability-zones/az-overview)kell tartoznia. 

|  | Windows Server-verzió | SQL Server verziója | SQL Server Edition | WSFC kvórum konfigurációja | DR több régióval | Több alhálózatot támogató támogatás | Meglévő AD támogatása | DR többzónás azonos régióval | Dist-AG támogatás AD-tartomány nélkül | A dist-AG támogatása fürt nélkül |  
| :------ | :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----|
| [SQL VIRTUÁLIS GÉP PARANCSSORI FELÜLETE](virtual-machines-windows-sql-availability-group-cli.md) | 2016 | 2017 </br>2016   | ENT | Felhőbeli tanúsító | Nem | Igen | Igen | Igen | Nem | Nem |
| [Gyorsindítás sablonok](virtual-machines-windows-sql-availability-group-quickstart-template.md) | 2016 | 2017</br>2016  | ENT | Felhőbeli tanúsító | Nem | Igen | Igen | Igen | Nem | Nem |
| [Portál sablonja](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) | 2016 </br>2012 R2 | 2016</br>2014 | ENT | Fájlmegosztás | Nem | Nem | Nem | Nem | Nem | Nem |
| [Kézi](virtual-machines-windows-portal-sql-availability-group-prereq.md) | Összes | Összes | Összes | Összes | Igen | Igen | Igen | Igen | Igen | Igen |
| &nbsp; | &nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |

Ha készen áll egy SQL Server rendelkezésre állási csoport létrehozására az Azure Virtual Machines-on, tekintse meg ezeket az oktatóanyagokat.

## <a name="manually-with-azure-cli"></a>Manuális az Azure CLI-vel
Ha az Azure CLI-t használja a rendelkezésre állási csoport konfigurálásához és üzembe helyezéséhez, az ajánlott lehetőség, mivel ez az egyszerűség és a üzembe helyezés gyorsasága szempontjából a legjobb. Az Azure CLI-vel a Windows feladatátvevő fürt létrehozása, SQL Server virtuális gépek csatlakoztatása a fürthöz, valamint a figyelő és a belső Load Balancer létrehozása 30 percen belül is elvégezhető. Ez a lehetőség továbbra is a rendelkezésre állási csoport manuális létrehozását igényli, de automatizálja az összes többi szükséges konfigurációs lépést. 

További információ: az Azure [SQL VM CLI használata az Always On rendelkezésre állási csoport konfigurálásához SQL Server Azure-beli virtuális gépen](virtual-machines-windows-sql-availability-group-cli.md). 

## <a name="automatically-with-azure-quickstart-templates"></a>Automatikus az Azure Gyorsindítás sablonjaival
Az Azure rövid útmutató sablonjai az SQL virtuális gép erőforrás-szolgáltatóját használják a Windows feladatátvevő fürt üzembe helyezéséhez, a SQL Server virtuális gépekhez való csatlakozáshoz, a figyelő létrehozásához és a belső Load Balancer konfigurálásához. Ehhez a lehetőséghez továbbra is manuálisan kell létrehozni a rendelkezésre állási csoportot és a belső Load Balancer (ILB), de automatizálja és egyszerűsíti az összes többi szükséges konfigurációs lépést (beleértve a ILB konfigurációját is). 

További információt az Azure-beli virtuális gépeken [SQL Server az Always On rendelkezésre állási csoport konfigurálása az Azure gyorsindítási sablonnal](virtual-machines-windows-sql-availability-group-quickstart-template.md)című témakörben talál.


## <a name="automatically-with-an-azure-portal-template"></a>Automatikus Azure Portal sablonnal

[Az Always On rendelkezésre állási csoport konfigurálása az Azure-beli virtuális gépen automatikusan – Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)


## <a name="manually-in-azure-portal"></a>Manuálisan Azure Portal

Saját maga is létrehozhatja a virtuális gépeket a sablon nélkül. Először hajtsa végre az előfeltételeket, majd hozza létre a rendelkezésre állási csoportot. Tekintse meg a következő témaköröket: 

- [SQL Server always on rendelkezésre állási csoportok előfeltételeinek konfigurálása az Azure-ban Virtual Machines](virtual-machines-windows-portal-sql-availability-group-prereq.md)

- [Always On rendelkezésre állási csoport létrehozása a rendelkezésre állás és a vész-helyreállítás javítása érdekében](virtual-machines-windows-portal-sql-availability-group-tutorial.md)

## <a name="next-steps"></a>További lépések

[SQL Server always on rendelkezésre állási csoport konfigurálása az Azure Virtual Machines különböző régiókban](virtual-machines-windows-portal-sql-availability-group-dr.md)
