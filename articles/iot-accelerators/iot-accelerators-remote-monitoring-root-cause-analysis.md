---
title: Riasztás kiváltó okainak elemzése – Azure | Microsoft Docs
description: Ebben az oktatóanyagban a riasztások kiváltó okainak az Azure Time Series Insights használatával végzett elemzését sajátíthatja el.
author: aditidugar
ms.author: adugar
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/20/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 70d29359d4a4bcf9f5badbbf0c553d7bed88a02b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61444619"
---
# <a name="tutorial-conduct-a-root-cause-analysis-on-an-alert"></a>Oktatóanyag: Riasztás kiváltó okainak elemzése

Ez az oktatóanyag a távoli monitorozási megoldásgyorsító riasztások kiváló okainak elemzésére való használatát ismerteti. A távoli monitorozási megoldás irányítópultján láthatja, ha egy riasztás aktiválva lett, majd az Azure Time Series Insights Explorerrel kivizsgálhatja annak kiváltó okát.

Az oktatóanyag két szimulált, teherautóban működő eszközt használ, amelyek helyre, magasságra, sebességre és rakomány-hőmérsékletre vonatkozó telemetriát küldenek. A teherautókat egy Contoso nevű cég kezeli, és kapcsolódnak a távoli monitorozási megoldásgyorsítóhoz. A Contoso üzemeltetési munkatársaként tisztáznia kell, hogy miért rögzített a teherautók egyike (delivery-truck-02) alacsony hőmérsékletet jelző riasztást.

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

>[!div class="checklist"]
> * Az irányítópulton található eszközök szűrése
> * Valós idejű telemetriai adatok megtekintése
> * Adatok vizsgálata a Time Series Insights Explorerben
> * A kiváltó okok elemzése
> * Új szabály létrehozása az eredmények alapján

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

## <a name="choose-the-devices-to-display"></a>A megjelenítendő eszközök kiválasztása

Az **Irányítópult** oldalon megjelenítendő csatlakoztatott eszközök kiválasztásához használjon szűrőket. Ha kizárólag a **Teherautó** típusú eszközöket szeretné megjeleníteni, válassza a beépített **Teherautók** szűrőt a legördülő szűrőlistában:

[![Teherautók keresése szűréssel az irányítópulton](./media/iot-accelerators-remote-monitoring-root-cause-analysis/filter-trucks-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/filter-trucks-expanded.png#lightbox)

Amikor alkalmazza a szűrőt, csak azokat az eszközöket a szűrési feltételeknek megfelelő jelennek meg a térképen, és a telemetriai adatok panelen a **irányítópult**. Láthatja, hogy a megoldásgyorsítóhoz két teherautó csatlakozik, beleértve a **truck-02** teherautót is.

## <a name="view-real-time-telemetry"></a>Valós idejű telemetriai adatok megtekintése

A megoldásgyorsító valós idejű telemetriai adatokat ábrázol az **Irányítópult** lapon található diagramon. A diagram alapértelmezés szerint az időben változó magassági telemetriai adatokat mutatja:

[![Teherautó-magasság telemetriai diagram](./media/iot-accelerators-remote-monitoring-root-cause-analysis/trucks-moving-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/trucks-moving-expanded.png#lightbox)

A teherautók hőmérsékleti telemetriájának megtekintéséhez kattintson **Telemetria panel** **Hőmérséklet** elemére. Láthatja, hogyan változott a két teherautó hőmérséklete az elmúlt 15 perc során. A riasztási panelen az is látható, hogy a delivery-truck-02 esetén alacsony hőmérsékleti riasztás lett aktiválva.

[![Távoli figyelési irányítópult alacsony hőmérsékleti riasztással](./media/iot-accelerators-remote-monitoring-root-cause-analysis/low-temp-alert-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/low-temp-alert-expanded.png#lightbox)

## <a name="explore-the-data"></a>Az adatok vizsgálata

Az alacsony hőmérsékleti riasztás okának megállapításához nyissa meg a teherautó telemetriai adatait a Time Series Insights Explorerben. Kattintson az irányítópulton lévő **Vizsgálat a Time Series Insightsban** hivatkozások bármelyikére:

[![Távoli figyelési irányítópult a TSI-hivatkozások kiemelésével](./media/iot-accelerators-remote-monitoring-root-cause-analysis/explore-tsi-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/explore-tsi-expanded.png#lightbox)

Amikor az Explorer megnyílik, megjelenik az eszközök teljes listája:

[![TSI Explorer kezdő nézet](./media/iot-accelerators-remote-monitoring-root-cause-analysis/initial-tsi-view-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/initial-tsi-view-expanded.png#lightbox)

Az eszközök szűrése beírásával **szállítójárműhöz** a Szűrő mezőbe, és válassza a **hőmérséklet** , a **mérték** a bal oldali panelen:

[![TSI Explorer – teherautó hőmérséklete](./media/iot-accelerators-remote-monitoring-root-cause-analysis/filter-tsi-temp-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/filter-tsi-temp-expanded.png#lightbox)

A távoli figyelési irányítópult a látott ugyanabban a nézetben láthatja. Ezenkívül mostantól nagyítás is közelebb a az időkeretet, amely a riasztás aktiválódott belül:

[![TSI Explorer – zoom](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-zoom-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-zoom-expanded.png#lightbox)

Hozzáadhat a teherautóktól származó további telemetria-streameket is. Kattintson a **Hozzáadás** gomb bal felső sarokban. Ekkor új panel nyílik meg:

[![TSI Explorer új panellel](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-pane-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-pane-expanded.png#lightbox)

Az új panelen módosítsa az új címke nevét az **Eszközök** névre, hogy megegyezzen az előzővel. Válassza ki **magasság** , a **mérték** és **iothub-kapcsolat-eszközazonosító** , a **Split által** , tengerszint telemetriát hozzáadandó érték a nézet:

[![TSI Explorer a hőmérséklettel és a magassággal](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-altitude-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-altitude-expanded.png#lightbox)

## <a name="diagnose-the-alert"></a>A riasztás diagnosztizálása

Ha megnézi a streameket, az aktuális nézet, láthatja, hogy a két látható a magasság profilok különböznek. Az is látható, hogy a **delivery-truck-02** esetében a hőmérséklet-csökkenés akkor következik be, amikor a teherautó nagy magasságot ér el. Az eredmény meglepő, hiszen a két teherautó számára ugyanaz az útvonal volt kijelölve.

Sejtését, hogy a teherautók más útvonalon haladtak, úgy igazolhatja, hogy újabb panelt vesz fel az oldalpanelre a **Hozzáadás** gomb használatával. Az új panelen módosítsa az új címke nevét az **Eszközök** névre, hogy megegyezzen az előzővel. A földrajzi hosszúsági telemetriát úgy veheti fel ebbe a nézetbe, hogy **Mértékként** a **földrajzi hosszúságot**, a **Felosztás** szempontjaként pedig az **iothub-connection-device-id** értéket választja. A **földrajzi hosszúsági** stream alapján megállapítható, hogy a teherautók különböző útvonalon haladtak:

[![TSI Explorer a hőmérséklettel, a magassággal és a földrajzi hosszúsággal](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-longitude-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-longitude-expanded.png#lightbox)

## <a name="create-a-new-rule"></a>Új szabály létrehozása

Teherautó útvonalak optimalizált általában előzetes, miközben kiderülhet, hogy forgalmi mintázatait, időjárás és más előre nem látható események is késésekhez vezethet, és hagyja meg az elmúlt percben útválasztási döntéseket teherautó illesztőprogramokat a legjobb ítéletét alapján. Azonban mivel a hőmérsékletet, a vehicle belüli eszközök a kritikus fontosságú, érdemes további szabály létrehozása a távoli figyelési megoldásban. Ez a szabály annak érdekében, hogy figyelmeztetést kap, ha egy 1 perces időszakban az átlagos magasság túllépik 350 feet:

[![Távoli figyelési szabályok lapja a magasságra vonatkozó szabály beállításával](./media/iot-accelerators-remote-monitoring-root-cause-analysis/new-rule-altitude-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/new-rule-altitude-expanded.png#lightbox)

A szabályok létrehozását és módosítását az [Eszközökkel kapcsolatos problémák észlelése](iot-accelerators-remote-monitoring-automate.md) című előző oktatóanyagban sajátíthatja el.

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag azt mutatta be, hogyan használható a Time Series Insights Explorer a távoli monitorozási megoldásgyorsítóval riasztások kiváltó okainak elemzésére. A következő oktatóanyagból azt tudhatja meg, hogyan használhatja a megoldásgyorsítót a csatlakoztatott eszközök problémáinak azonosítására és javítására.

> [!div class="nextstepaction"]
> [Eszközriasztások használata a monitorozási megoldáshoz csatlakoztatott eszközök problémáinak azonosítására és javítására](iot-accelerators-remote-monitoring-maintain.md)
