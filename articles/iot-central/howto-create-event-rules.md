---
title: Létrehozása és kezelése az Azure IoT központi alkalmazás eseményszabályok |} A Microsoft Docs
description: Az Azure IoT Central esemény szabályok lehetővé teszik az eszközök, közel valós időben figyelheti és automatikusan követve indíthatók el műveletek, például egy e-mailt küldhet a szabály aktiválásakor.
author: ankitscribbles
ms.author: ankitgup
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 4754e6b571845d286ef22014f87b86fae2f6633d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67053033"
---
# <a name="create-an-event-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>Hozzon létre egy esemény szabályt és az Azure IoT központi alkalmazás-értesítések beállítása

*Ez a cikk operátorokra, fejlesztőkre és rendszergazdákra vonatkozik.*

Azure IoT Central használatával távolról figyeli a csatlakoztatott eszközök. Az Azure IoT Central szabályok lehetővé teszik az eszközök, közel valós időben figyelheti és automatikusan hajthatók végre műveletek, például e-mail küldése vagy Microsoft Flow-trigger. Mindössze pár kattintással megadhatja a feltételt, amelynek az adatok figyelésére, és konfigurálja a megfelelő műveletet. Ez a cikk bemutatja, hogyan hozhat létre szabályokat az eszköz által küldött események figyelésére.

Eszközök esemény mérési segítségével eszköz fontos vagy tájékoztató eseményeket küldeni. Egy esemény szabály aktivál, hogy a kiválasztott eszköz esemény az eszköz által jelentett.

## <a name="create-an-event-rule"></a>Eseményszabály létrehozása

Esemény szabály létrehozása esetén az eszköz sablon legalább egy eseményt mérési definiálva kell rendelkeznie. Ebben a példában egy hűtött Eladóautomata eszköz által jelentett motor hibaesemény ventilátor. A szabály az eseményt, az eszköz által jelentett figyeli, és a egy e-mailt küld, amikor az eseményt jelentett.

1. Használatával a **eszközsablonok** lapon, keresse meg az eszköz sablon, amelynek a szabályt ad hozzá.

1. Szabályok még nem hozta létre, ha a következő képernyő jelenik meg:

    ![Még nincsenek szabályai](media/howto-create-event-rules/rules_landing_page1.png)

1. Az a **szabályok** lapon jelölje be **+ új szabály** , milyen típusú szabályokat hozhat létre.

1. Válassza ki a **esemény** csempére kattintva hozzon létre egy esemény figyelési szabály.

    ![Szabály típusa](media/howto-create-event-rules/rule_types1.png)

1. Adjon meg egy nevet, amely segít azonosítani a szabály az eszköz sablonban.

1. Váltsa át a szabály a sablon alapján létrehozott összes eszköz azonnal engedélyezéséhez **engedélyezése a szabály az összes eszközt, ez a sablon**.

    ![Szabály részletei](media/howto-create-event-rules/rule_detail1.png)

    Eszköz sablon alapján az eszközök automatikusan alkalmazza a szabályt.

### <a name="configure-the-rule-conditions"></a>A szabály feltételeinek konfigurálása

A feltétel a feltételeknek, a szabály által figyelt határozza meg.

1. Válassza ki a **+** melletti **feltételek** új feltétel hozzáadása.

1. Válassza ki azt az eseményt, amely szeretné figyelni a mérési legördülő listából. Ebben a példában **ventilátor Motor hiba** esemény lett kiválasztva.

   ![Állapot](media/howto-create-event-rules/condition_filled_out1.png)

1. Igény szerint is beállíthat **száma** , **összesítési** , és adja meg a megfelelő küszöbértéket.

   - Nélkül összesítés, a szabály aktiválásakor adatpontok esemény, amely megfelel a feltételnek. Például, ha a szabály feltétel az indítási feltétel egy **ventilátor Motor hiba** esemény következik be, akkor a szabály gondoskodik arról, szinte azonnal amikor az eszköz jelenti a kapcsolódó eseményre.
   - Száma aggregátumfüggvényt használja, akkor meg kell adnia egy **küszöbérték** és a egy **összesített időtartomány** keresztül, amelyet a feltétel ki kell értékelni kell. Ebben az esetben az események száma összesített értéket jelenít meg, és csak akkor, ha az összesített száma a küszöbérték megegyezik a szabály aktiválásakor.

     Például ha azt szeretné, a riasztás, ha háromnál több eszköz események 5 percen belül, válassza ki az esemény, és állítsa "count", az aggregate függvényt, operátort, "nagyobb, mint" és "küszöbértéket" 3. Állítsa be a "Összesítési időszak", "5 perc". A szabály akkor aktiválódik, ha több mint három eseménynek az eszköz által küldött 5 percen belül. A szabály a kiértékelés gyakorisága pedig ugyanaz, mint a **összesített időtartomány**, ami azt jelenti, hogy ebben a példában a szabály kiértékelése történik az 5 percenként egyszer.

     ![Feltétel hozzáadása](media/howto-create-event-rules/aggregate_condition_filled_out1.png)

     >[!NOTE]
     >Egynél több esemény mérték alapján is hozzáadhatók **feltétel**. Ha több feltétel van megadva, minden feltételeknek teljesülniük kell elindítani a szabályhoz. Minden egyes feltétel lekérdezi csatlakozott egy "És" záradék által implicit módon. Összesítés használatakor minden mérték összesíteni kell.

### <a name="configure-actions"></a>Művelet konfigurálása

Ez a szakasz bemutatja, hogyan állíthatja be a műveleteket, ha a szabály lesz elindítva. Műveletek beolvasása aktiválódnak, ha az a szabályban megadott feltételek kiértékelése igaz értékre.

1. Válassza ki a **+** melletti **műveletek**. Itt láthatja az elérhető műveletek listáját.

    ![Művelet hozzáadása](media/howto-create-event-rules/add_action1.png)

1. Válassza ki a **E-mail** művelet, adjon meg egy érvényes e-mail-címmel a **való** mezőben, és ügyeljen arra, hogy a szabály aktiválásakor jelennek meg az e-mail törzsét adja meg.

    > [!NOTE]
    > Csak hozzá az alkalmazáshoz, és legalább egyszer bejelentkezett felhasználók kapnak e-mailt. Tudjon meg többet [felhasználókezelés](howto-administer.md) az Azure IoT Central.

   ![Művelet konfigurálása](media/howto-create-event-rules/configure_action1.png)

1. A szabály mentéséhez válasszon **mentése**. A szabály élesíti néhány percen belül, és elindítja az alkalmazásnak küldött események figyelését. Megegyezik a feltételt a szabályban megadott, a szabály eseményindítók a beállított e-mail-művelet.

A szabály például a Microsoft Flow és a webhookok más műveleteket is hozzáadhat. Szabályonként legfeljebb 5 műveleteket is hozzáadhat.

- [Microsoft Flow művelet](howto-add-microsoft-flow.md) elindít egy munkafolyamatot a Microsoft Flow, amikor egy szabály akkor lesz kiváltva 
- [Webhook művelettel](howto-create-webhooks.md) más szolgáltatások értesíti, amikor egy szabály akkor lesz kiváltva

## <a name="parameterize-the-rule"></a>A szabály paraméterezése

Műveletek használatával is konfigurálhatók **Eszköztulajdonság** paraméterként. Ha egy e-mail-cím egy adott eszköztulajdonság van tárolva, akkor meghatározása során is használható a **való** címet.

## <a name="delete-a-rule"></a>Szabály törlése

Ha már nincs szüksége egy szabályt, törölje azt a szabály megnyitásával, majd válassza a **törlése**. A szabály törlése eltávolítja az eszköz sablon és a kapcsolódó eszközöket.

## <a name="enable-or-disable-a-rule-for-a-device-template"></a>Engedélyezi vagy letiltja az eszköz sablon szabály

Keresse meg az eszközt, és válassza ki az engedélyezni vagy letiltani kívánt szabályt. Váltás a **engedélyezése a szabály az összes eszközt, ez a sablon** gombra kattintva engedélyezheti vagy tilthatja le a szabályt, amelyek az eszköz-sablonhoz társított összes eszköz.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Engedélyezi vagy letiltja az eszköz szabály

Keresse meg az eszközt, és válassza ki az engedélyezni vagy letiltani kívánt szabályt. Váltás a **engedélyezése a szabály az eszköz** gombra kattintva engedélyezheti vagy letilthatja a szabály az eszközön.

## <a name="next-steps"></a>További lépések

Most, hogy megtanulhatta, hogyan hozhat létre szabályokat az Azure IoT Central alkalmazáshoz, az alábbiakban néhány következő lépés:

- [A szabályok a Microsoft Flow művelet hozzáadása](howto-add-microsoft-flow.md)
- [A szabályokban Webhook művelet hozzáadása](howto-create-webhooks.md)
- [Több művelet futtatható egy vagy több szabályt a csoporthoz](howto-use-action-groups.md)
- [Az eszközök kezelése](howto-manage-devices.md)
