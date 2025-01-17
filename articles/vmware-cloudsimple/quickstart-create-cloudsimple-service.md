---
title: Azure VMware-megoldás CloudSimple rövid útmutató – szolgáltatás létrehozása
description: Megtudhatja, hogyan hozhatja létre a CloudSimple szolgáltatást, hogyan építhet ki csomópontokat és foglaljon le csomópontokat
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 13b07b3b50bdb03373275ca9594baa6357e9f66f
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812291"
---
# <a name="quickstart---create-service"></a>Rövid útmutató – szolgáltatás létrehozása

Első lépésként hozza létre az Azure VMware-megoldást a Azure Portal CloudSimple.

## <a name="vmware-solution-by-cloudsimple---service-overview"></a>VMware-megoldás a CloudSimple-szolgáltatás áttekintése

A CloudSimple szolgáltatás lehetővé teszi, hogy a CloudSimple az Azure VMware-megoldást használja.  A szolgáltatás létrehozása lehetővé teszi a csomópontok kiépítését, a csomópontok fenntartását és a privát felhők létrehozását.  Adja hozzá a CloudSimple szolgáltatást minden olyan Azure-régióban, ahol a CloudSimple szolgáltatás elérhető.  A szolgáltatás a CloudSimple által definiált Azure VMware-megoldás peremhálózati hálózatát határozza meg.  Ez a peremhálózati hálózat olyan szolgáltatásokhoz használható, amelyek VPN-, ExpressRoute-és internetkapcsolattal rendelkeznek a privát felhőkhöz.

A CloudSimple szolgáltatás hozzáadásához létre kell hoznia egy átjáró-alhálózatot. Az átjáró-alhálózat a peremhálózati hálózat létrehozásakor használatos, és egy/28 CIDR blokkot igényel. Az átjáró alhálózatának címtartomány egyedinek kell lennie. A helyszíni hálózati címtartomány vagy az Azure-beli virtuális hálózati címtartomány egyikével sem lehet átfedésben.

## <a name="before-you-begin"></a>Előkészületek

Egy/28 CIDR-blokk lefoglalása az átjáró-alhálózathoz.  Az átjáró-alhálózatok CloudSimple szolgáltatáshoz szükségesek, és egyediek ahhoz a régióhoz, amelyben létrehozták. Az átjáró-alhálózat az Edge hálózati szolgáltatások esetében használatos, és egy/28 CIDR blokkot igényel. Az átjáró alhálózatának címtartomány egyedinek kell lennie. Nem lehet átfedésben a CloudSimple-környezettel kommunikáló hálózattal.  A CloudSimple kommunikáló hálózatok a helyszíni hálózatokat és az Azure-beli virtuális hálózatokat is tartalmazzák. 

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="create-the-service"></a>A szolgáltatás létrehozása

1. Válassza az **Összes szolgáltatás** elemet.
2. Keresse meg a **CloudSimple szolgáltatást**.

    ![Search CloudSimple Service](media/create-cloudsimple-service-search.png)

3. Válassza a **CloudSimple Services**elemet.
4. Új szolgáltatás létrehozásához kattintson a **Hozzáadás** gombra.

    ![CloudSimple szolgáltatás hozzáadása](media/create-cloudsimple-service-add.png)

5. Válassza ki azt az előfizetést, amelyben létre szeretné hozni a CloudSimple szolgáltatást.
6. Válassza ki a szolgáltatáshoz tartozó erőforráscsoportot. Új erőforráscsoport hozzáadásához kattintson az **új létrehozása**lehetőségre.
7. A szolgáltatás azonosításához adja meg a nevet.
8. Adja meg a CIDR a Service Gateway számára. Olyan/28 alhálózatot ad meg, amely nem fedi át a helyszíni alhálózatok, az Azure-alhálózatok vagy a tervezett CloudSimple alhálózatok egyikét sem. A CIDR nem módosítható a szolgáltatás létrehozása után.

    ![A CloudSimple szolgáltatás létrehozása](media/create-cloudsimple-service.png)

9. Kattintson az **OK** gombra.

Létrejön a szolgáltatás, és hozzá lesz adva a szolgáltatások listájához.

## <a name="provision-nodes"></a>Csomópontok kiépítése

Ha a CloudSimple saját felhőalapú környezete számára szeretné beállítani az utólagos elszámolású kapacitást, először a Azure Portal csomópontjait kell kiépíteni.

1. Válassza az **Összes szolgáltatás** elemet.
2. CloudSimple- **csomópontok**keresése.

    ![CloudSimple-csomópontok keresése](media/create-cloudsimple-node-search.png)

3. Válassza a **CloudSimple**-csomópontok lehetőséget.
4. Csomópontok létrehozásához kattintson a **Hozzáadás** gombra.

    ![CloudSimple-csomópontok hozzáadása](media/create-cloudsimple-node-add.png)

5. Válassza ki azt az előfizetést, ahol CloudSimple-csomópontokat kíván kiépíteni.
6. Válassza ki a csomópontok erőforrás-csoportját. Új erőforráscsoport hozzáadásához kattintson az **új létrehozása**lehetőségre.
7. Adja meg a csomópontok azonosításához szükséges előtagot.
8. Válassza ki a csomópont-erőforrások helyét.
9. Válassza ki a csomópont-erőforrások tárolására szolgáló dedikált helyet.
10. Válassza ki a csomópont típusát. Kiválaszthatja a [CS28 vagy a CS36 lehetőséget](cloudsimple-node.md). Az utóbbi lehetőség a maximális számítási és memória-kapacitást is tartalmazza.
11. Válassza ki a kiépíteni kívánt csomópontok számát.
12. Válassza a **felülvizsgálat + létrehozás**lehetőséget.
13. Tekintse át a beállításokat. A beállítások módosításához kattintson az **előző**gombra.
14. Kattintson a **Létrehozás** gombra.

## <a name="next-steps"></a>További lépések

* [Saját felhő létrehozása és környezet konfigurálása](quickstart-create-private-cloud.md)
* További információ a [CloudSimple szolgáltatásról](https://docs.azure.cloudsimple.com/cloudsimple-service)
