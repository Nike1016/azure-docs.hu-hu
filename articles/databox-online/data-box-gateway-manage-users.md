---
title: Azure Data Box Gateway – Felhasználók kezelése | Microsoft Docs
description: Ez a cikk bemutatja, hogyan történik az Azure Data Box Gateway-felhasználók kezelése az Azure Portalon.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: 9ee5eec5a5c17e443fe71f0093f09c96cf81ece0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60754787"
---
# <a name="use-the-azure-portal-to-manage-users-on-your-azure-data-box-gateway"></a>Azure Data Box Gateway-felhasználók kezelése az Azure Portalon 

Ez a cikk bemutatja, hogyan történik a felhasználók kezelése az Azure Data Box Gatewayben. Az Azure Data Box Gateway az Azure Portal használatával vagy a helyi webes felhasználói felületről felügyelhető. Az Azure Portalon felhasználókat vehet fel, módosíthat vagy törölhet.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Felhasználó hozzáadása
> * Felhasználó módosítása
> * Felhasználó törlése 

## <a name="about-users"></a>Információk a felhasználókról

A felhasználók csak olvasási vagy teljes körű jogosultsággal rendelkezhetnek. Ahogy a név is mutatja, a csak olvasási jogosultsággal rendelkező felhasználók kizárólag megtekinteni tudják a megosztott adatokat. A teljes körű jogosultsággal rendelkező felhasználók olvasni és írni is tudják a megosztott adatokat, valamint módosíthatják és törölhetik is azokat. 

 - **Teljes körű jogosultsággal rendelkező felhasználó** – Teljes körű hozzáféréssel rendelkező helyi felhasználó. 
 - **Csak olvasási jogosultsággal rendelkező felhasználó** – Csak olvasási hozzáféréssel rendelkező helyi felhasználó. Ezek a felhasználók csak olvasási műveleteket megengedő megosztásokhoz vannak társítva.

A felhasználói engedélyek első alkalommal a megosztás létrehozása során, a felhasználó létrehozásakor lesznek meghatározva. A felhasználóhoz társított engedélyek ezt követően a Fájlkezelőben módosíthatók. 


## <a name="add-a-user"></a>Felhasználó hozzáadása

Felhasználó hozzáadásához hajtsa végre az alábbi lépéseket az Azure Portalon.

1. Az Azure Portalon keresse meg a Data Box Gateway-erőforrást, majd lépjen az **Áttekintés** területre. A parancssávon kattintson a **+ Felhasználó hozzáadása** gombra.

    ![Kattintson a Felhasználó hozzáadása elemre](media/data-box-gateway-manage-users/add-user-1.png)

2. Adja meg a hozzáadni kívánt felhasználó felhasználónevét és jelszavát. Erősítse meg a jelszót, majd kattintson a **Hozzáadás** gombra.

    ![Kattintson a Felhasználó hozzáadása elemre](media/data-box-gateway-manage-users/add-user-2.png)

    > [!IMPORTANT] 
    > Ezeket a felhasználókat a rendszer számára vannak fenntartva, és nem használható: Rendszergazda, EdgeUser, EdgeSupport, HcsSetupUser, WDAGUtilityAccount, CLIUSR, DefaultAccount, Vendég.  

3. A rendszer a felhasználó létrehozásának megkezdéséről és befejezéséről is értesítést küld. A felhasználó létrehozása után kattintson a parancssáv **Frissítés** elemére a felhasználók listájának frissítéséhez.


## <a name="modify-user"></a>Felhasználó módosítása

A felhasználó létrehozását követően lehetőség van a hozzá tartozó jelszó módosítására. Jelölje ki a felhasználót a listában, majd kattintson rá. Adja meg és erősítse meg az új jelszót. Mentse a módosításokat.
 
![Felhasználó módosítása](media/data-box-gateway-manage-users/modify-user-1.png)


## <a name="delete-a-user"></a>Felhasználó törlése

Felhasználó törléséhez hajtsa végre a következő lépéseket az Azure Portalon.

1. Jelölje ki a felhasználót a listában, kattintson rá, majd kattintson a **Törlés** gombra.  

   ![Felhasználó törlése](media/data-box-gateway-manage-users/delete-user-1.png)

2. A rendszer kérésére erősítse meg a törlést. 

   ![Felhasználó törlése](media/data-box-gateway-manage-users/delete-user-2.png)

A törlés végrehajtása után annak megfelelően frissül a felhasználók listája.

![Felhasználó törlése](media/data-box-gateway-manage-users/delete-user-3.png)


## <a name="next-steps"></a>További lépések

- További tudnivalókat a [sávszélesség-kezeléssel foglalkozó részben](data-box-gateway-manage-bandwidth-schedules.md) talál.
