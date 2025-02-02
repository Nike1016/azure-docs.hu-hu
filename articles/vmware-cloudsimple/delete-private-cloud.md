---
title: Azure VMware-megoldás törlése a CloudSimple privát felhővel
description: Útmutató CloudSimple privát felhő törléséhez.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8a47968ec252f628da8a1a36570fb06eb4bb10bf
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68886948"
---
# <a name="delete-a-cloudsimple-private-cloud"></a>CloudSimple privát felhő törlése

A CloudSimple rugalmasságot biztosít a privát felhő törléséhez.  A privát felhő egy vagy több vSphere-fürtből áll. Minden fürthöz 3 – 16 csomópont tartozhat. Ha töröl egy privát felhőt, az összes fürt törölve lesz. 

## <a name="before-you-begin"></a>Előkészületek

A privát felhő törlése törli a teljes privát felhőt.  A rendszer törli a privát felhő összes összetevőjét.  Ha meg szeretné őrizni az összes adatmennyiséget, győződjön meg róla, hogy biztonsági másolatot készített a helyszíni tárolóba vagy az Azure Storage szolgáltatásba. 

A privát felhő összetevői a következők:

* CloudSimple-csomópontok
* Virtual machines (Virtuális gépek)
* VLAN-ok/alhálózatok
* A privát felhőben tárolt összes felhasználói érték
* Az összes tűzfalszabály melléklete egy VLAN/alhálózat számára


## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="delete-a-private-cloud"></a>Privát felhő törlése 

1. [Nyissa meg a CloudSimple portált](access-cloudsimple-portal.md).

2. Nyissa meg az **erőforrások** lapot.

3. Kattintson a törölni kívánt privát felhőre

4. Az összefoglalás lapon kattintson a **Törlés**gombra.

    ![Privát felhő törlése](media/delete-private-cloud.png)

5. A jóváhagyás lapon adja meg a saját felhő nevét, majd kattintson a **Törlés**gombra. 

    ![Privát felhő törlése – megerősítés](media/delete-private-cloud-confirm.png)


A privát felhő törlésre van megjelölve.  A törlési folyamat három óra elteltével indul el, és törli a privát felhőt.

> [!CAUTION]
> A csomópontokat törölni kell a saját felhő törlése után.  A csomópontok mérése folytatódni fog, amíg a csomópontok törlődnek az előfizetésből.


## <a name="next-steps"></a>További lépések

* [Csomópontok törlése](delete-nodes.md)
