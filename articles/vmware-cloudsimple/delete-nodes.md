---
title: A VMware-megoldás csomópontjainak törlése a CloudSimple által – Azure
description: Ismerje meg, hogyan törölhet csomópontokat VMWare-ről a CloudSimple-telepítéssel
author: sharaths-cs
ms.author: dikamath
ms.date: 08/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9dbe1751ac77f370991af75ea161247c21700ff2
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68886987"
---
# <a name="delete-nodes-from-vmware-solution-by-cloudsimple---azure"></a>Csomópontok törlése VMware-megoldásból a CloudSimple-Azure használatával

A CloudSimple-csomópontok a létrehozásuk után vannak mérten.  A csomópontok mérésének leállításához törölni kell a csomópontokat.  Törli a Azure Portal nem használt csomópontokat. 

## <a name="before-you-begin"></a>Előkészületek

Csomópontot csak a következő feltételekkel lehet törölni:

* A csomópontokkal létrehozott privát felhő törlődik.  Privát felhő törléséhez tekintse meg a [CloudSimple privát felhőből származó Azure VMware-megoldás törlése](delete-private-cloud.md)című témakört.
* A csomópont el lett távolítva a privát felhőből a privát felhő zsugorodása mellett.  A privát felhő csökkentése érdekében lásd: [Az Azure VMware-megoldás zsugorítása a CloudSimple privát felhővel](shrink-private-cloud.md).


## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="delete-cloudsimple-node"></a>CloudSimple csomópont törlése

1. Válassza az **Összes szolgáltatás** elemet.

2. CloudSimple- **csomópontok**keresése.

   ![CloudSimple-csomópontok keresése](media/create-cloudsimple-node-search.png)

3. Válassza a **CloudSimple**-csomópontok lehetőséget.

4. Válassza ki azokat a csomópontokat, amelyek nem tartoznak a törölni kívánt privát felhőhöz.  A **saját felhő neve** oszlop azt a saját Felhőbeli nevet jeleníti meg, amelyhez a csomópont tartozik.  Ha egy csomópontot nem használ egy privát felhő, az érték üres lesz. 

    ![CloudSimple-csomópontok kiválasztása](media/select-delete-cloudsimple-node.png)

> [!NOTE]
> Csak azokat a csomópontokat lehet törölni, amelyek nem részei a privát felhőnek.

## <a name="next-steps"></a>További lépések

* A [Private Cloud](cloudsimple-private-cloud.md) megismerése
