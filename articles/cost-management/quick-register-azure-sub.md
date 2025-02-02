---
title: Azure-előfizetés regisztrálása a Cloudynben | Microsoft Docs
description: Ez a rövid útmutató részletesen ismerteti a Cloudyn próbaverziójára szóló előfizetés létrehozásához és a Cloudyn-portálra való bejelentkezéshez szükséges folyamatot.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/20/2019
ms.topic: quickstart
ms.custom: seodec18
ms.service: cost-management
manager: benshy
ms.openlocfilehash: 6d5282a326af37e5653f21795438ba8965ae7fcc
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2019
ms.locfileid: "65967197"
---
# <a name="register-an-individual-azure-subscription-and-view-cost-data"></a>Egyéni Azure-előfizetés regisztrálása és a költségadatok megtekintése

Az Azure-előfizetésével regisztrálhat a Cloudynbe. A regisztráció hozzáférést biztosít a Cloudyn portálhoz. Ez a rövid útmutató részletesen ismerteti a Cloudyn próbaverziójára szóló előfizetés létrehozásához és a Cloudyn-portálra való bejelentkezéshez szükséges folyamatot. Azt is bemutatja, hogyan tekintheti meg azonnal a költségadatokat.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

- Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="register-with-cloudyn"></a>Regisztráció a Cloudynben

1. Az Azure Portalon kattintson a **Költségkezelés + Számlázás** elemre a szolgáltatások listáján.
2. Az **Áttekintés** területen kattintson a **Cloudyn** elemre.  
    ![A Cloudyn lap jelenik meg, az Azure Portalon](./media/quick-register-azure-sub/cost-mgt-billing-service.png)
3. A **Cost Management** oldalon kattintson a **Go to Cloudyn** (Ugrás a Cloudynre) elemre a Cloudyn regisztrációs oldalának új ablakban való megnyitásához.
4. A Cloudyn portálon a próbaverzióra való regisztrációhoz írja be a vállalata nevét, majd válassza az **Azure Individual Subscription Owner** (Egyéni Azure-előfizetés tulajdonosa) lehetőségre, és kattintson a **Next** (Tovább) gombra. A fiókneve és a bérlőazonosító automatikusan rákerül az űrlapra.  
    ![Regisztrálás a próbaverzióra lap, adja meg a regisztrációs adatait](./media/quick-register-azure-sub/trial-reg-ind.png)
5. Válassza ki az előfizetéséhez kapcsolódó **Offer ID - Name** (Ajánlatazonosító – Név) elemet. Ha nem biztos abban, milyen díjazonosító tartozik az előfizetéséhez, nézzen meg egy Azure-számlát, és keresse meg rajta az **ajánlatazonosítót**.
6. Fogadja el a használati feltételeket, majd ellenőrizze az adatokat, és kattintson a **Next** (Tovább) gombra.
7. A **Gather additional data** (További adatok gyűjtése) oldalon kattintson a **Next** (Tovább) gombra. Ezzel felhatalmazza fel a Cloudynt az Azure-erőforrásadatok gyűjtésére. A gyűjtött adatok az előfizetéseire vonatkozó használati, teljesítmény-, elszámolási és címkeadatokból állnak.  
    ![Ha engedélyezi, hogy a Cloudyn további adatok lap összegyűjtése](./media/quick-register-azure-sub/gather-additional.png)
8. A böngészőjében megnyílik a Cloudyn bejelentkezési oldala. Jelentkezzen be az Azure-előfizetése hitelesítő adataival.
9. A **Go to Cloudyn** (Ugrás a Cloudynre) elemre kattintva nyissa meg a Cloudyn portált, ahol az **Accounts Management** (Fiókok kezelése) oldalon látnia kell az Azure-előfizetés fiókjának adatait.  
    ![Fiókok kezelése lap Azure-előfizetés adatainak megjelenítése](./media/quick-register-azure-sub/accounts-mgt.png)

Oktatóvideó az Azure-előfizetés regisztrációjáról: [A címtárbeli GUID és a díjazonosító megkeresése a Cloudynben való használathoz](https://youtu.be/PaRjnyaNGMI).

[!INCLUDE [cost-management-create-account-view-data](../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban az Azure-előfizetése adatait használta arra, hogy regisztráljon a Cloudyn szolgáltatásra. A Cloudyn portálra is bejelentkezett, és elkezdte a költségadatok áttekintését. Ha bővebb információra van szüksége a Cloudynről, lépjen tovább a Cloudyn oktatóanyagára.

> [!div class="nextstepaction"]
> [A használat és a költségek áttekintése](./tutorial-review-usage.md)
