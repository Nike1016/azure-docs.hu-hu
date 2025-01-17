---
title: Rövid útmutató – Azure Analysis Services-kiszolgáló létrehozása az Azure Portal használatával | Microsoft Docs
description: Útmutatás egy Azure-beli Analysis Services-kiszolgálópéldány létrehozásához.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: quickstart
ms.date: 07/02/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: aae5b87e49b6d64e4c9ee854b65821d65969e66b
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2019
ms.locfileid: "67536808"
---
# <a name="quickstart-create-a-server---portal"></a>Gyors útmutató: Kiszolgáló létrehozása – Portal

Ez a rövid útmutató bemutatja, hogyan hozhat létre erőforrást egy Azure Analysis Services-kiszolgálóhoz Azure-előfizetésében a portál használatával.

## <a name="prerequisites"></a>Előfeltételek 

* **Azure-előfizetés**: Látogasson el [Azure ingyenes próbaverziója](https://azure.microsoft.com/offers/ms-azr-0044p/) hozhat létre fiókot.
* **Azure Active Directory**: Az előfizetés Azure Active Directory-bérlő társítva kell lennie. Emellett be kell jelentkeznie az Azure-ba az adott Azure Active Directoryban található fiókkal. További információ: [Hitelesítés és felhasználói engedélyek](analysis-services-manage-users.md).

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra 

[Jelentkezzen be a portálra](https://portal.azure.com)


## <a name="create-a-server"></a>A kiszolgáló létrehozása

1. Kattintson a **+ erőforrás létrehozása** > **Analytics** > **Analysis Services**.

    ![Portál](./media/analysis-services-create-server/aas-create-server-portal.png)

2. Az **Analysis Services** területen töltse ki a kötelező mezőket, majd válassza a **Létrehozás** lehetőséget.
   
   * **Kiszolgálónév**: Írjon be egy egyedi nevet a kiszolgáló hivatkozni. A kiszolgáló nevét kell kezdődniük egy kisbetűt és 3 és 128 kisbetűk és számok között. Nem állhat kizárólag szóközökből, és speciális karakterek nem engedélyezettek.
   * **Előfizetés**: Válassza ki az előfizetést, a kiszolgáló társítva lesz.
   * **Erőforráscsoport**: Hozzon létre egy új erőforráscsoportot, vagy válasszon ki egy már rendelkezik. Az erőforráscsoportok segítségével Azure-erőforrások gyűjteményét kezelheti. További információ: [erőforráscsoportok](../azure-resource-manager/resource-group-overview.md).
   * **Hely**: Az Azure-adatközpont helyét a kiszolgáló üzemelteti. A legnagyobb felhasználói bázisához legközelebb eső helyet válassza ki.
   * **A tarifacsomag**: Válasszon tarifacsomagot. Ha Ön éppen tesztel, és ezért a mintamodell-adatbázist kívánja telepíteni, válassza ki az ingyenes **D1** szintet. További tudnivalókat az [Azure Analysis Services díjszabását](https://azure.microsoft.com/pricing/details/analysis-services/) ismertető témakörben talál. 
   * **Rendszergazdai**: Alapértelmezés szerint ez a fiók van bejelentkezve a következővel lesz. Másik fiókot az Azure Active Directoryból választhat.
   * **Biztonsági mentési tároló beállítása**: Választható. Ha már rendelkezik [tárfiókkal](../storage/common/storage-introduction.md), megadhatja azt a modelladatbázisok biztonsági másolatainak alapértelmezett tárolójaként. Később is megadhatja a [biztonsági mentés és visszaállítás](analysis-services-backup.md) beállításait.
   * **Tárkulcs lejárata**: Választható. Adjon meg egy tárkulcslejárati időszakot.

A kiszolgáló létrehozása általában nem egészen egy percet vesz igénybe. Ha a **Hozzáadás a Portalhoz** lehetőséget választotta, a portáljára lépve megtekintheti az új kiszolgálót. Az **Összes szolgáltatás** > **Analysis Services** területre lépve is ellenőrizheti, hogy készen áll-e a kiszolgáló. Kiszolgálók támogatja a táblázatos modelleket az 1200-as vagy magasabb kompatibilitási szinteken. Modell kompatibilitási szint van megadva, az SSDT vagy az ssms-ben.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje a kiszolgálót. Nyissa meg a kiszolgáló **Áttekintés** lapját, majd kattintson a **Törlés** elemre. 

 ![Felesleges tartalmak törlése](./media/analysis-services-create-server/aas-create-server-cleanup.png)


## <a name="next-steps"></a>További lépések
Ebből a rövid útmutatóból megtudta, hogyan hozhat létre kiszolgálót az Azure-előfizetésében. Most, hogy már rendelkezik kiszolgálóval, biztonságosabbá teheti azt egy (nem kötelező) kiszolgálótűzfal konfigurálásával. Hozzáadhat a kiszolgálóhoz egy alapszintű minta adatmodellt is közvetlenül a portálról. A mintamodell azért hasznos, mert segít megismerni a modell adatbázis-szerepkörök konfigurálását és tesztelni az ügyfélkapcsolatokat. További tudnivalókért folytassa a mintamodell hozzáadását ismertető oktatóanyaggal.

> [!div class="nextstepaction"]
> [Rövid útmutató: Kiszolgálótűzfal - portál konfigurálása](analysis-services-qs-firewall.md)   
> [!div class="nextstepaction"]
> [Oktatóanyag: Mintamodell hozzáadása a kiszolgálóhoz](analysis-services-create-sample-model.md)
