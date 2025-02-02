---
title: Üzembe helyezése és kezelése az Azure Time Series – előzetes |} A Microsoft Docs
description: Megértse, hogyan építhet ki és felügyelhet az Azure Time Series Insights előzetes verziója.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 06/26/2019
ms.custom: seodec18
ms.openlocfilehash: f626ce2e009a18afcb4d04b7caa6850ea58c7483
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446812"
---
# <a name="provision-and-manage-azure-time-series-insights-preview"></a>Üzembe helyezése és kezelése az Azure Time Series Insights előzetes verziója

Ez a cikk bemutatja, hogyan hozhat létre és kezelése az Azure Time Series Insights – előzetes környezet használatával a [az Azure portal](https://portal.azure.com/).

## <a name="overview"></a>Áttekintés

Az Azure Time Series Insights előzetes környezetekben használatalapú fizetés (Használatalapú) környezetben.

Amikor üzembe helyezi az Azure Time Series Insights – előzetes környezet, hozzon létre két Azure-erőforrások:

* Az Azure Time Series Insights – előzetes környezet  
* Azure Storage-általános célú v1 fiók
  
Ismerje meg, [a környezet tervezésével](./time-series-insights-update-plan.md).

>[!IMPORTANT]
> Az előzetes verzióra, ellenőrizze, hogy használja az Azure Storage általános célú v1 (GPv1-) fiókkal.

Szükség esetén az egyes Azure Time Series Insights – előzetes környezetekben is társíthat eseményforrás. További információkért olvassa el [adja hozzá a hub eseményforrást](./time-series-insights-how-to-add-an-event-source-eventhub.md) és [egy IoT hub-forrás hozzáadása](./time-series-insights-how-to-add-an-event-source-iothub.md). E lépés során egy időbélyeg ID tulajdonsága és a egy egyedi felhasználói csoport biztosítanak. Ez biztosítja, hogy a környezet férhet hozzá a megfelelő események.

Kiépítés befejezése után módosíthatja a hozzáférési házirendek és más környezeti attribútumok saját üzleti követelményeinek megfelelően.

## <a name="create-the-environment"></a>A környezet létrehozása

Az alábbi lépések bemutatják, hogyan hozhat létre az Azure Time Series Insights – előzetes környezet:

1. Válassza ki a **PAYG** gomb alatt a **Termékváltozat** menü. Adja meg a környezet nevét, és válassza ki, melyik előfizetést csoporthoz, és melyik erőforráscsoport. Ezután válassza ki a környezetet üzemeltetni, a támogatott helyét.

   [![Hozzon létre egy Azure Time Series Insights-példányt.](media/v2-update-manage/manage-three.png)](media/v2-update-manage/manage-three.png#lightbox)

1. Adjon meg egy Idősorozat-azonosító.

    >[!NOTE]
    > * A Time Series-azonosító, kis-és nagybetűket, és nem módosítható. (Ez nem módosítható beállítása után.)
    > * Time Series azonosítók legfeljebb három kulcsot is lehet.
    > * A Time Series ID kiválasztásával kapcsolatos további információkért olvassa el [válassza ki a Time Series ID](./time-series-insights-update-how-to-id.md).

1. Az Azure storage-fiók létrehozása a tárfiók nevének kiválasztásával, és a egy replikációs választott kijelölése. Ez így automatikusan létrehoz egy Azure Storage-általános célú v1 fiók. Az Azure Time Series Insights – előzetes környezet, amely a korábban kiválasztott ugyanabban a régióban jön.

    [![A példány az Azure storage-fiók létrehozása](media/v2-update-manage/manage-five.png)](media/v2-update-manage/manage-five.png#lightbox)

1. Szükség esetén az eseményforrás is hozzáadhat.

   * Time Series Insights támogatja [Azure IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md) és [Azure Event Hubs](./time-series-insights-how-to-add-an-event-source-eventhub.md) közül. Bár hozzáadhat csak egyetlen eseményforrás környezet létrehozáskor történik, egy másik eseményforrás később is hozzáadhat. Érdemes létrehozni egy egyedi felhasználói csoport, győződjön meg arról, hogy láthatók-e az összes esemény az Azure Time Series Insights – előzetes-példányhoz. Válasszon egy meglévő fogyasztói csoportot, vagy hozzon létre egy új fogyasztói csoportot, az eseményforrás hozzáadása során.

   * Válassza a megfelelő időbélyeg-tulajdonság is. Alapértelmezés szerint az Azure Time Series Insights használja az üzenet sorba helyezésekor minden egyes esemény forrását.

     > [!TIP]
     > Az üzenet sorba helyezésekor nem feltétlenül a legjobb konfigurált beállítást, a batch-esemény vagy előzményadatokat forgatókönyvek feltöltés. Ellenőrizze, hogy ellenőrizze a döntést, vagy használjon egy időbélyeg-tulajdonság az ezekben az esetekben.

     [![Esemény (forrás) lapot](media/v2-update-manage/manage-two.png)](media/v2-update-manage/manage-two.png#lightbox)

1. Győződjön meg arról, hogy a környezet van kiépítve a kívánt beállításokat.

    [![Felülvizsgálat + Létrehozás lap](media/v2-update-manage/manage-three.png)](media/v2-update-manage/manage-three.png#lightbox)

## <a name="manage-the-environment"></a>A környezet kezelése

Az Azure Time Series Insights – előzetes környezet az Azure portal használatával kezelheti. Az alábbiakban a jelentősebb eltérések kezelését egy Használatalapú Azure Time Series Insights előzetes verziója környezet helyett egy S1 vagy S2 környezetben, az Azure portal használatával:

* Az Azure Portalon **áttekintése** panel nem változott az Azure Time Series Insightsban, kivéve az alábbi módokon:
  * Kapacitás törlődnek, mert a fogalom esetében nem releváns PAYG környezetekben.
  * A Time Series azonosító tulajdonsággal bővült. Meghatározza, hogy az adatok particionálásáról.
  * Referencia-adatkészletekhez el lesznek távolítva.
  * A megjelenő URL-CÍMÉT arra utasítja, hogy a [Azure Time Series Insights – előzetes explorer](./time-series-insights-update-explorer.md).
  * Az Azure storage-fiók neve szerepel a listán.

* Az Azure Portalon **konfigurálása** panel az Azure Time Series Insights – előzetes eltávolították, mivel Használatalapú környezetek nem konfigurálható.

* Az Azure Portalon **referenciaadatok** panel az Azure Time Series Insights – előzetes eltávolították, mert referenciaadatok nem PAYG környezet egyik összetevője.

[![Time Series Insights előzetes verziójú környezet az Azure Portalon](media/v2-update-manage/manage-four.png)](media/v2-update-manage/manage-four.png#lightbox)

## <a name="next-steps"></a>További lépések

- Olvasási [a környezet megtervezése](./time-series-insights-update-plan.md).

- Ismerje meg, hogyan [adja hozzá a hub eseményforrást](./time-series-insights-how-to-add-an-event-source-eventhub.md).

- Konfigurálása [egy IoT hub forrás](./time-series-insights-how-to-add-an-event-source-iothub.md).