---
title: Oktatóanyag – mintamodell hozzáadása az Azure Analysis Services-kiszolgáló |} A Microsoft Docs
description: Az oktatóanyagnak ez a leckéje a mintamodellek hozzáadását ismerteti az Azure Analysis Servicesben.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: tutorial
ms.date: 03/13/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 32c46f0a488d775275b3a367aa2913f034569041
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61026281"
---
# <a name="tutorial-add-a-sample-model-from-the-portal"></a>Oktatóanyag: Mintamodell hozzáadása a portálról

Az oktatóanyag elvégzése során egyszerű táblázatos Adventure Works-modelladatbázist adhat hozzá kiszolgálójához. A mintamodell az Adventure Works Internet Sales (1200) minta-adatmodell kiegészített változata. A mintamodellek a modellkezelés, az eszközökhöz és ügyfélalkalmazásokhoz való kapcsolódás és a modelladatok lekérdezése tesztelésekor hasznosak. Ez az oktatóanyag az [Azure Portalt](https://portal.azure.com) és az [SQL Server Management Studiót](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) használja a következőkhöz: 

> [!div class="checklist"]
> * Kiegészített táblázatos adatmodell-minta hozzáadása kiszolgálóhoz 
> * Csatlakozás a modellhez az SSMS használatával

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="before-you-begin"></a>Előkészületek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

- Egy Azure Analysis Services-kiszolgáló. További tudnivalók: [Kiszolgáló létrehozása – Portal](analysis-services-create-server.md).
- Kiszolgáló-rendszergazdai engedélyek
- [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)


## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be a [portálra](https://portal.azure.com/).

## <a name="add-a-sample-model"></a>Mintamodell hozzáadása

1. A kiszolgáló **Áttekintés** lapján kattintson az **Új modell** lehetőségre.

    ![Mintamodell létrehozása](./media/analysis-services-create-sample-model/aas-create-sample-new-model.png)

2. Győződjön meg arról, hogy az **Új modell** > **Adatforrás kiválasztása** alatt a **Mintaadatok** van kijelölve, és kattintson a **Hozzáadás** gombra.

    ![Mintaadatok kijelölése](./media/analysis-services-create-sample-model/aas-create-sample-data.png)

3. Az **Áttekintés** lapon ellenőrizheti, hogy az `adventureworks` mintamodell hozzáadása megtörtént.

    ![Mintaadatok kijelölése](./media/analysis-services-create-sample-model/aas-create-sample-verify.png)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A mintamodell gyorsítótár-memória erőforrásokat használ. Ha a mintamodellt nem használja teszteléshez, akkor ajánlott eltávolítani a kiszolgálóról.

Egy modell a következő lépések alapján törölhető egy kiszolgálóról az SSMS használatával.

1. Az SSMS > **Object Explorerben** kattintson a **Csatlakozás** > **Analysis Services** lehetőségre.

2. A **Csatlakozás kiszolgálóhoz** panelen illessze be a kiszolgáló nevét, majd a **Hitelesítés** módjaként válassza az **Active Directory – univerzális, MFA-támogatással** lehetőséget, adja meg felhasználónevét, végül kattintson a **Csatlakozás** gombra.

    ![Bejelentkezés](./media/analysis-services-create-sample-model/aas-create-sample-cleanup-signin.png)

3. Az **Object Explorerben** kattintson a jobb gombbal az `adventureworks` mintaadatbázisra, majd kattintson a **Törlés** elemre.

    ![Mintaadatbázis törlése](./media/analysis-services-create-sample-model/aas-create-sample-cleanup-delete.png)

## <a name="next-steps"></a>További lépések 

Ebben az oktatóanyagban azt tanulhatta meg, hogyan adhat hozzá egyszerű mintamodellt kiszolgálójához. Most, hogy már rendelkezik modelladatbázissal, csatlakozhat hozzá az SQL Server Management Studióból, és hozzáadhat felhasználói szerepköröket. További tudnivalókat talál a következő oktatóanyagban.

> [!div class="nextstepaction"]
> [Oktatóanyag: Kiszolgáló-rendszergazdai és felhasználói szerepkörök konfigurálása](analysis-services-database-users.md)


