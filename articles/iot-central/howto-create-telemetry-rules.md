---
title: Hozzon létre, és az Azure IoT Central alkalmazáshoz a telemetriai adatok szabályok kezelése |} A Microsoft Docs
description: Az Azure IoT-központ-telemetria szabályok lehetővé teszik az eszközök, közel valós időben figyelheti és automatikusan követve indíthatók el műveletek, például egy e-mailt küldhet a szabály aktiválásakor.
author: ankitgupta
ms.author: ankitgup
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 8684301b83e01989c745b63848995142cb766188
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67052969"
---
# <a name="create-a-telemetry-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>Telemetria szabály létrehozása és az Azure IoT központi alkalmazás-értesítések beállítása

*Ez a cikk operátorokra, fejlesztőkre és rendszergazdákra vonatkozik.*

Azure IoT Central használatával távolról figyeli a csatlakoztatott eszközök. Az Azure IoT Central szabályok lehetővé teszik az eszközök, közel valós időben figyelheti és automatikusan hajthatók végre műveletek, például e-mail küldése vagy Microsoft Flow-trigger. Mindössze pár kattintással megadhatja a feltételt, amelynek az adatok figyelésére, és konfigurálja a megfelelő műveletet. Ez a cikk bemutatja, hogyan hozhat létre szabályokat figyelése az eszköz által küldött telemetriát.

Eszközök telemetriai mérési segítségével numerikus adatokat küldjön az eszközről. Telemetria szabály aktivál, hogy a kiválasztott eszköz telemetriai átlép egy küszöbértéket.

## <a name="create-a-telemetry-rule"></a>Telemetria szabály létrehozása

Hozzon létre egy telemetriai szabályt, az eszköz sablon legalább egy telemetriai mérési meghatározott kell rendelkeznie. Ez a példa hőmérséklettel és páratartalommal kapcsolatos telemetriai adatokat küld hűtött Eladóautomata eszközt használja. A szabály az eszköz által jelentett hőmérséklet figyeli, és a egy e-mailt küld, ha ezt túllépik 80 fok.

1. Használatával a **eszközsablonok** lapon, keresse meg az eszköz sablon, amelynek a szabályt ad hozzá.

1. Szabályok még nem hozta létre, ha a következő képernyő jelenik meg:

    ![Még nincsenek szabályai](media/howto-create-telemetry-rules/rules_landing_page1.png)

1. Az a **szabályok** lapon jelölje be **+ új szabály** , milyen típusú szabályokat hozhat létre.

1. Válassza ki **Telemetriai** hozhat létre egy szabályt, amely az eszköz telemetria figyelése.

    ![Szabály típusa](media/howto-create-telemetry-rules/rule_types1.png)

1. Adjon meg egy nevet, amely segít azonosítani a szabály az eszköz sablonban.

1. Váltsa át a szabály minden olyan eszközre, ezzel a sablonnal létrehozott azonnal engedélyezéséhez **engedélyezése a szabály a sablon összes eszköz**.

   ![Szabály részletei](media/howto-create-telemetry-rules/rule_detail1.png)

    Eszköz sablon alapján az eszközök automatikusan alkalmazza a szabályt.

### <a name="configure-the-rule-conditions"></a>A szabály feltételeinek konfigurálása

A feltétel a feltételeknek, a szabály által figyelt határozza meg.

1. Válassza ki **+** melletti **feltételek** új feltétel hozzáadása.

1. Válassza ki a figyelni kívánt telemetriát a **mérési** legördülő listából.

1. Következő lépésként válassza ki **összesítési**, **operátor**, és adjon meg egy **küszöbérték** érték.
   - Összesítés nem kötelező. Nélkül összesítés, a szabály aktiválásakor telemetriai adatpontok, amely megfelel a feltételnek. Például ha a szabályt úgy eseményindító, ha hőmérséklete meghaladja a 80-as és a szabály aktiválásakor szinte azonnal mikor jelentett hőmérséklet > 80-as.
   - Ha például az átlagos, minimális és maximális összesítő függvényben, Count van kiválasztva, majd a felhasználónak meg kell adnia egy **összesített időtartomány** keresztül, amelyet a feltétel ki kell értékelni kell. Például ha pedig "5 perc" időszak és a szabály keres átlaghőmérséklet 80-as, a szabály akkor aktiválódik, ha az átlaghőmérséklet legalább 5 percnek a 80-as fölött van. fent. A szabály a kiértékelés gyakorisága pedig ugyanaz, mint a **összesített időtartomány**, ami azt jelenti, hogy ebben a példában a szabály kiértékelése történik az 5 percenként egyszer.

     ![Állapot](media/howto-create-telemetry-rules/aggregate_condition_filled_out1.png)

     >[!NOTE]
     >Egynél több telemetria mérték alapján is hozzáadhatók **feltétel**. Ha több feltétel van megadva, minden feltételeknek teljesülniük kell elindítani a szabályhoz. Minden egyes bizonyságául lekérdezi csatlakozott egy "És" záradék által implicit módon. Összesítés használatakor minden mérték összesíteni kell.

### <a name="configure-actions"></a>Művelet konfigurálása

Ez a szakasz bemutatja, hogyan állíthatja be a műveleteket, ha a szabály lesz elindítva. Műveletek beolvasása aktiválódnak, ha az a szabályban megadott feltételek kiértékelése igaz értékre.

1. Válassza ki a **+** melletti **műveletek**. Itt láthatja az elérhető műveletek listáját.  

    ![Művelet hozzáadása](media/howto-create-telemetry-rules/add_action1.png)

1. Válassza ki a **E-mail** művelet, adjon meg egy érvényes e-mail-címmel a **való** mezőben, és ügyeljen arra, hogy a szabály aktiválásakor jelennek meg az e-mail törzsét adja meg.

    > [!NOTE]
    > Csak hozzá az alkalmazáshoz, és legalább egyszer bejelentkezett felhasználók kapnak e-mailt. Tudjon meg többet [felhasználókezelés](howto-administer.md) az Azure IoT Central.

   ![Művelet konfigurálása](media/howto-create-telemetry-rules/configure_action1.png)

1. A szabály mentéséhez válasszon **mentése**. A szabály élesíti néhány percen belül, és elindítja a telemetriát küld az alkalmazás figyelését. Ha a feltételt a szabályban megadott feltétele teljesül, a szabály elindítja a beállított e-mail-művelet.

A szabály például a Microsoft Flow és a webhookok más műveleteket is hozzáadhat. Szabályonként legfeljebb 5 műveleteket is hozzáadhat.

- [Microsoft Flow művelet](howto-add-microsoft-flow.md) elindít egy munkafolyamatot a Microsoft Flow, amikor egy szabály akkor lesz kiváltva 
- [Webhook művelettel](howto-create-webhooks.md) más szolgáltatások értesíti, amikor egy szabály akkor lesz kiváltva

## <a name="parameterize-the-rule"></a>A szabály paraméterezése

Szabályok is nyer az egyes értékeket **eszköztulajdonságok** paraméterekként. Paraméterek használatával hasznos forgatókönyvekben, ahol a telemetria küszöbértékek eltérőek lehetnek a különböző eszközökön. A szabály létrehozásakor válassza ki egy adott eszköztulajdonság, amely a küszöbértéket, megadja például **maximális ideális küszöbérték**, rögzített érték, például a 80 fok megadása helyett. Ha végrehajtja a szabályt, értékre van állítva, az eszköz tulajdonságban megegyezik az eszköz telemetriai adatokat.

Paraméterek használata hatékony módszert szabályok kezelése / eszköz sablon számának csökkentése érdekében.

Műveletek használatával is konfigurálhatók **Eszköztulajdonság** paraméterként. Ha egy tulajdonságot egy e-mail-cím van tárolva, akkor meghatározása során is használható a **való** cím.

## <a name="delete-a-rule"></a>Szabály törlése

Ha már nincs szüksége egy szabályt, törölje azt a szabály megnyitásával, majd válassza a **törlése**. A szabály törlése eltávolítja az eszköz sablon és a kapcsolódó eszközöket.

## <a name="enable-or-disable-a-rule-for-a-device-template"></a>Engedélyezi vagy letiltja az eszköz sablon szabály

Keresse meg az eszközt, és válassza ki az engedélyezni vagy letiltani kívánt szabályt. Váltás a **engedélyezése a szabály az összes eszközt, ez a sablon** gombra a szabály az eszköz sablon társított összes eszköz letiltása és engedélyezése a szabályban.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Engedélyezi vagy letiltja az eszköz szabály

Keresse meg az eszközt, és válassza ki az engedélyezni vagy letiltani kívánt szabályt. Váltás a **engedélyezése a szabály az eszköz** gombra kattintva engedélyezheti vagy letilthatja a szabály az eszközön.

## <a name="next-steps"></a>További lépések

Most, hogy megtanulhatta, hogyan hozhat létre szabályokat az Azure IoT Central alkalmazáshoz, az alábbiakban néhány következő lépés:

- [A szabályok a Microsoft Flow művelet hozzáadása](howto-add-microsoft-flow.md)
- [A szabályokban Webhook művelet hozzáadása](howto-create-webhooks.md)
- [Több művelet futtatható egy vagy több szabályt a csoporthoz](howto-use-action-groups.md)
- [Az eszközök kezelése](howto-manage-devices.md)
