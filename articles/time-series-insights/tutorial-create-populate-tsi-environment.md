---
title: 'Oktatóanyag: Azure Time Series Insights-környezet létrehozása | Microsoft Docs'
description: Oktatóanyag, amelyből megtudhatja, hogyan hozhat létre olyan Time Series Insights-környezetet, amely szimulált eszközökről származó adatokkal van feltöltve.
services: time-series-insights
author: ashannon7
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 07/29/2019
ms.author: dpalled
manager: cshankar
ms.custom: seodec18
ms.openlocfilehash: 4c81daa1f55167fa868c69f3bff388dbaa4887cd
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68725740"
---
# <a name="tutorial-create-an-azure-time-series-insights-environment"></a>Oktatóanyag: Azure Time Series Insights-környezet létrehozása

Ez az oktatóanyag végigvezeti egy olyan Azure Time Series Insights-környezet létrehozásának folyamatán, amely a szimulált eszközökről származó adatokkal van feltöltve. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Egy Time Series Insights környezetet hozhat létre.
> * Hozzon létre egy IoT hubot tartalmazó eszköz-szimulációs megoldást.
> * Csatlakoztatja a Time Series Insights környezetet az IoT hubhoz.
> * Futtasson egy eszköz-szimulációt az adatstreamek Time Series Insights-környezetbe való továbbításához.
> * Ellenőrizze a szimulált telemetria-adatgyűjtést.

> [!IMPORTANT]
> Ha még nem rendelkezik ilyennel, regisztráljon egy [ingyenes Azure](https://azure.microsoft.com/free/) -előfizetésre.

## <a name="prerequisites"></a>Előfeltételek

* Az Azure bejelentkezési fiókjának az előfizetés **tulajdonosi** szerepkörének is tagja kell lennie. További információ: [hozzáférés kezelése szerepköralapú hozzáférés-vezérléssel és a Azure Portal használatával](../role-based-access-control/role-assignments-portal.md).

## <a name="review-video"></a>Videó áttekintése

### <a name="learn-how-to-use-an-azure-iot-solution-accelerator-to-generate-data-and-get-started-with-time-series-insights-br"></a>Megtudhatja, hogyan hozhatja meg az Azure IoT-megoldási gyorssegédet az adatlétrehozáshoz és a Time Series Insights első lépéseihez. </br>

> [!VIDEO https://www.youtube.com/embed/6ehNf6AJkFo]

## <a name="overview"></a>Áttekintés

A Time Series Insights-környezet az adatok gyűjtésének és tárolásának helye. A tárolás után a [Azure Time Series Insights Explorer](time-series-quickstart.md) és a [Time Series Insights Query API](/rest/api/time-series-insights/ga-query-api) használható az adatlekérdezéshez és az elemzéshez.

Az Azure IoT Hub az oktatóanyagban az összes eszköz (szimulált vagy fizikai) által használt eseményforrás, amellyel biztonságosan csatlakozhat az Azure-felhőhöz, és továbbíthatja az adatokat.

Ez az oktatóanyag egy [IoT](https://www.azureiotsolutions.com) -megoldási gyorssegédet használ a minta telemetria-alapú adatIoT Hubek létrehozásához és továbbításához.

>[!TIP]
> A [IoT megoldás](https://www.azureiotsolutions.com) -gyorsítók olyan nagyvállalati szintű előre konfigurált megoldásokat biztosítanak, amelyek segítségével felgyorsíthatja az egyéni IoT-megoldások fejlesztését.

## <a name="create-a-device-simulation"></a>Eszközszimuláció létrehozása

Először hozza létre az eszköz-szimulációs megoldást, amely tesztelési adatokat generál a Time Series Insights-környezet feltöltéséhez.

1. Egy különálló ablakban vagy lapon nyissa meg a [azureiotsolutions.com](https://www.azureiotsolutions.com). Jelentkezzen be ugyanazzal az Azure-előfizetési fiókkal, és válassza ki az **eszköz szimulációs** gyorsító.

   [![Az eszköz szimulációs gyorssegédének futtatása](media/tutorial-create-populate-tsi-environment/sa-main.png)](media/tutorial-create-populate-tsi-environment/sa-main.png#lightbox)

1. Adja meg a szükséges paramétereket az **eszköz-szimulációs megoldás létrehozása** lapon.

   Paraméter|Leírás
   ---|---
   **Központi telepítés neve** | Ez az egyedi érték egy új erőforráscsoport létrehozásához használatos. A felsorolt Azure-erőforrások létrejönnek, és hozzá lesznek rendelve az erőforráscsoporthoz.
   **Azure-előfizetés** | Adja meg ugyanazt az előfizetést, amelyet az előző szakaszban Time Series Insights környezet létrehozásához használt.
   **Üzembe helyezési beállítások** | Válassza az **új IoT hub kiépítése** lehetőséget az oktatóanyaghoz tartozó új IoT hub létrehozásához.
   **Azure-beli hely** | Adja meg ugyanazt a régiót, amelyet az előző szakaszban Time Series Insights környezet létrehozásához használt.

   Ha elkészült, válassza a megoldás **létrehozása** lehetőséget a megoldás Azure-erőforrásainak kiépítéséhez. A folyamat elvégzése akár 20 percet is igénybe vehet.

   [![Az eszköz-szimulációs megoldás kiépítése](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution.png#lightbox)

1. A kiépítés befejezése után az új megoldás fölötti szöveg az **üzembe** helyezéstől a **használatra kész**értékre változik.

   >[!IMPORTANT]
   > Ne válassza az **Indítás** még lehetőséget! Tartsa meg ezt a weblapot, mert később vissza fog térni.

   [![Az eszköz-szimulációs megoldás üzembe helyezése befejeződött](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard-ready.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard-ready.png#lightbox)

1. Most vizsgálja meg az újonnan létrehozott erőforrásokat a Azure Portal. Figyelje meg, hogy az **erőforráscsoportok** lapon az utolsó lépésben megadott **megoldás neve** alapján létrehozott egy új erőforráscsoportot. Jegyezze fel az eszköz szimulálásához létrehozott erőforrásokat.

   [![Eszköz-szimulációs erőforrások](media/tutorial-create-populate-tsi-environment/ap-device-sim-solution-resources.png)](media/tutorial-create-populate-tsi-environment/ap-device-sim-solution-resources.png#lightbox)

## <a name="create-an-environment"></a>Környezet létrehozása

Másodszor hozzon létre egy Time Series Insights környezetet az Azure-előfizetésében.

1. Jelentkezzen be az [Azure Portalba](https://portal.azure.com) az Azure-előfizetési fiók használatával. 
1. Válassza az **+ Erőforrás létrehozása** lehetőséget a bal felső sarokban. 
1. Válassza ki a **IOT-** kategóriát, és válassza ki **Time Series Insights**. 

   [![A Time Series Insights-környezet erőforrás kiválasztása](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi.png#lightbox)

1. A **Time Series Insights-környezet** lapon adja meg a szükséges paramétereket.

   Paraméter|Leírás
   ---|---
   **Környezet neve** | Válasszon egyedi nevet a Time Series Insights környezet számára. A neveket a Time Series Insights Explorer és a [lekérdezési API](https://docs.microsoft.com/rest/api/time-series-insights/ga-query)-k használják.
   **Előfizetés** | Az előfizetés az Azure-erőforrások tárolója. Válasszon egy előfizetést a Time Series Insights környezet létrehozásához.
   **Erőforráscsoport** | Az erőforráscsoport az Azure-erőforrások tárolója. Válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy újat a Time Series Insights környezeti erőforráshoz.
   **Location** | Válasszon egy adatközpont-régiót Time Series Insights-környezetéhez. A további késés elkerülése érdekében hozza létre a Time Series Insights környezetet ugyanabban a régióban, mint a többi IoT-erőforrást.
   **Tier** | Válassza ki a szükséges teljesítményt. Válassza az **S1**elemet.
   **Kapacitás** | A kapacitás a kiválasztott SKU-hoz tartozó bejövő forgalom arányára és tárolókapacitására alkalmazott szorzó. A kapacitás a létrehozás után módosítható. Válasszon **1**kapacitást.

   Ha elkészült, válassza a **felülvizsgálat + létrehozás** lehetőséget a következő lépéshez való továbblépéshez.

   [![Time Series Insights környezeti erőforrás létrehozása](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-params.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-params.png#lightbox)

1. Most csatlakoztassuk a Time Series Insights környezetet a megoldás-gyorsító által létrehozott IoT hubhoz. Állítsa be **a hubot** `Select existing`a következőre:. Ezután válassza ki a megoldás-gyorsító által létrehozott IoT hubot **IoT hub nevének**beállításakor.

   [![Az Time Series Insights-környezet összekötése a létrehozott IoT hubhoz](media/tutorial-create-populate-tsi-environment/ap-create-resource-iot-hub.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-iot-hub.png#lightbox)

1. Ellenőrizze az **értesítések** panelt az üzembe helyezés befejezésének figyeléséhez. 

   [![A Time Series Insights környezet telepítése sikerült](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-deployment-succeeded.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-deployment-succeeded.png#lightbox)

## <a name="run-device-simulation"></a>Eszköz szimulációjának futtatása

Most, hogy a központi telepítés és a kezdeti konfiguráció elkészült, töltse ki a Time Series Insights környezetet a [gyorssegéd által létrehozott szimulált eszközökből](#create-a-device-simulation)származó mintaadatok használatával.

Az IoT hub mellett egy Azure App Service webalkalmazás lett létrehozva, amely szimulált eszköz telemetria létrehozására és továbbítására készült.

1. Lépjen vissza a [megoldásgyorsítók irányítópultjára](https://www.azureiotsolutions.com/Accelerators#dashboard). Ha szükséges, jelentkezzen be újra ugyanezen az oktatóanyagban használt Azure-fiók használatával. Most kiválaszthatja az **Indítás** lehetőséget a "Device szimulációs" megoldás alatt.

     [![Megoldás-gyorssegédek irányítópultja](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard.png#lightbox)

1. Az eszköz-szimulációs webalkalmazás megkezdi a webalkalmazás engedélyezését a "bejelentkezés és a profil beolvasása" engedély megadásával. Ez az engedély lehetővé teszi az alkalmazás számára, hogy lekérje az alkalmazás működésének támogatásához szükséges felhasználói profil adatait.

     [![Eszköz-szimulációs webalkalmazások beleegyezett](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png)](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png#lightbox)

1. A **szimulációs beállítások** oldal betöltése után adja meg a szükséges paramétereket.

   Paraméter|Leírás
   ---|---
   **Cél IoT Hub** | Válassza **az előre kiépített IoT hub használata**lehetőséget.
   **Eszközmodell** | Válasszaa Chiller lehetőséget.
   **Eszközök száma**  | Adja `1000` meg az **összeg értéket**.
   **Telemetria gyakorisága** | Adja `10` meg a másodperceket.
   **Szimuláció időtartama** | Válassza **a Befejezés elemet:** , `5` és adja meg a percet.

   Ha elkészült, válassza a **Szimuláció indítása**lehetőséget. A szimuláció összesen 5 percig fut. 10 másodpercenként 1 000 szimulált eszköz adatait hozza létre. 

   [![Eszköz szimulációjának beállítása](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png#lightbox)

1. A szimuláció futtatása közben figyelje meg, hogy az **összes üzenet** és **üzenet másodpercenként** frissül, körülbelül 10 másodpercenként. A szimuláció körülbelül 5 percet vesz igénybe, és visszaadja a **Szimuláció telepítőjét**.

   [![Az eszköz szimulációja fut](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png#lightbox)

## <a name="verify-the-telemetry-data"></a>A telemetriaadatok ellenőrzése

Ebben a végső szakaszban ellenőrzi, hogy a telemetria-adatbázis létrejött és a Time Series Insights-környezetben van-e tárolva. Az adatok ellenőrzéséhez használja a Time Series Insights Explorert, amellyel a telemetriaadatok lekérdezhetők és elemezhetők.

1. Térjen vissza a Time Series Insights környezet erőforrás-csoportjának **Áttekintés** lapjára. Válassza ki a Time Series Insights környezetet.

   [![Time Series Insights környezeti erőforráscsoport és környezet](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png#lightbox)

1. A Time Series Insights környezet **áttekintése** lapon válassza ki a **Time Series Insights Explorer URL-címét** a Time Series Insights Explorer megnyitásához.

   [![Time Series Insights Explorer](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png#lightbox)

1. A Time Series Insights Explorer betölti és hitelesíti magát a Azure Portal-fiók használatával. A kezdeti nézetben a diagram területén láthatja, hogy a Time Series Insights környezet szimulált telemetria-adatokkal lett feltöltve. A szűkebb időtartam szűréséhez válassza a bal felső sarokban lévő legördülő listát. Adjon meg egy időtartományt, amely elég nagy az eszköz szimulációjának időtartamára. Ezután válassza a keresés Nagyítót.

   [![Time Series Insights Explorer időtartomány-szűrő](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png#lightbox)

1. Az időtartomány szűkítése lehetővé teszi, hogy a diagram a IoT hubhoz és a Time Series Insights-környezetbe irányuló adatátvitelek eltérő adatsoraira nagyítson. Azt is figyelje meg, hogy a **folyamatos átvitel teljes** szövege a jobb felső sarokban látható, amely a talált események teljes számát mutatja. A diagram részletességének vezérléséhez az **intervallum mérete** csúszkát is húzhatja.

   [![Time Series Insights Explorer időtartományának szűrt nézete](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png#lightbox)

1. Végül kattintson a bal gombbal egy régióra a tartomány szűréséhez. Ezután kattintson a jobb gombbal, és az **események megismerése** lehetőségre kattintva jelenítse meg az esemény részleteit a táblázatos **események** nézetben.

   [![Time Series Insights Explorer időtartományának szűrt nézete és eseményei](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png#lightbox)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ez az oktatóanyag számos futó Azure-szolgáltatást hoz létre a Time Series Insights-környezet és az eszköz-szimulációs megoldás támogatásához. Ha el szeretné távolítani őket, térjen vissza a Azure Portal.

A Azure Portal bal oldali menüjében:

1. Válassza ki az **erőforráscsoportok** ikont. Ezután válassza ki az Time Series Insights-környezethez létrehozott erőforráscsoportot. Az oldal tetején válassza az **erőforráscsoport törlése**elemet, adja meg az erőforráscsoport nevét, és válassza a **Törlés**lehetőséget.

1. Válassza ki az **erőforráscsoportok** ikont. Ezután válassza ki az eszköz-szimulációs megoldás-gyorsító által létrehozott erőforráscsoportot. Az oldal tetején válassza az **erőforráscsoport törlése**elemet, adja meg az erőforráscsoport nevét, és válassza a **Törlés**lehetőséget.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Egy Time Series Insights környezetet hozhat létre.
> * Hozzon létre egy IoT hubot tartalmazó eszköz-szimulációs megoldást.
> * Csatlakoztatja a Time Series Insights környezetet az IoT hubhoz.
> * Futtasson egy eszköz-szimulációt az adatstreamek Time Series Insights-környezetbe való továbbításához.
> * Ellenőrizze a szimulált telemetria-adatgyűjtést.

Most, hogy már tudja, hogyan hozhat létre saját Time Series Insights környezetet, megtudhatja, hogyan készíthet olyan webalkalmazást, amely egy Time Series Insights-környezetből származó adatokkal rendelkezik:

> [!div class="nextstepaction"]
> [Azure Time Series Insights egyoldalas webalkalmazás létrehozása](tutorial-create-tsi-sample-spa.md)
