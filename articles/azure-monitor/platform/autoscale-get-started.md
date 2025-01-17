---
title: Ismerkedés az Azure-beli autoskálázással
description: Ismerje meg, hogyan méretezheti az Azure-ban az erőforrás-webalkalmazást, a Cloud Service-t, a virtuális gépet vagy a virtuálisgép-méretezési készletet
author: rajram
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/07/2017
ms.author: rajram
ms.subservice: autoscale
ms.openlocfilehash: 0535c84a8ee0776c2c35a46d3c7510a2cd615cf6
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/12/2019
ms.locfileid: "60788590"
---
# <a name="get-started-with-autoscale-in-azure"></a>Ismerkedés az Azure-beli autoskálázással
Ez a cikk azt ismerteti, hogyan állíthatja be az erőforráshoz tartozó autoskálázási beállításokat a Microsoft Azure Portalban.

Azure Monitor az autoscale csak [Virtual Machine Scale sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Cloud Services](https://azure.microsoft.com/services/cloud-services/), [app Service-Web Apps](https://azure.microsoft.com/services/app-service/web/)és [API Management szolgáltatásokra](https://docs.microsoft.com/azure/api-management/api-management-key-concepts)vonatkozik.

## <a name="discover-the-autoscale-settings-in-your-subscription"></a>Az előfizetéshez tartozó automatikus skálázási beállítások felderítése
Felderítheti az összes olyan erőforrást, amelyre az automatikus skálázás alkalmazható Azure Monitorban. Kövesse az alábbi lépéseket a lépésenkénti útmutatóhoz:

1. Nyissa meg a [Azure Portal.][1]
1. Kattintson a Azure Monitor ikonra a bal oldali ablaktáblán.
  ![Azure Monitor megnyitása][2]
1. Az **autoskálázás** lehetőségre kattintva megtekintheti az összes olyan erőforrást, amely esetében az autoskálázás alkalmazható, valamint az aktuális autoskálázási állapotot.
  ![Automatikus méretezés észlelése Azure Monitor][3]

A felső szűrő ablaktáblán a lista hatóköre alapján kiválaszthatja az erőforrásokat egy adott erőforráscsoport, egy adott erőforrástípus vagy egy adott erőforrás számára.

Az egyes erőforrásokhoz az aktuális példányszámot és az autoskálázási állapotot fogja megtalálni. Az autoskálázás állapota a következőket teheti:

- **Nincs konfigurálva**: Ennél az erőforrásnál még nem engedélyezte az autoskálázást.
- **Engedélyezve**: Engedélyezte az erőforrás-méretezést.
- Letiltva: Letiltotta az erőforrás-méretezést.

## <a name="create-your-first-autoscale-setting"></a>Az első méretezési beállítás létrehozása

Most ugorjon végig egy egyszerű, lépésenkénti útmutatót, amellyel létrehozhatja az első autoskálázási beállítást.

1. Nyissa meg Azure monitor az autoscale (méretezés) panelt, és válassza ki a méretezni kívánt erőforrást. (A következő lépések egy webalkalmazáshoz társított App Service tervet használnak. Az [első ASP.net-webalkalmazását 5 perc alatt létrehozhatja az Azure][4]-ban.)
1. Vegye figyelembe, hogy a példányok aktuális száma 1. Kattintson az **autoskálázás engedélyezése**lehetőségre.
  ![Méretezési beállítás az új webalkalmazáshoz][5]
1. Adja meg a skálázási beállítás nevét, majd kattintson **a szabály hozzáadása**elemre. Figyelje meg, hogy a méretezési szabály beállításai a jobb oldalon a helyi ablaktáblán nyílnak meg. Alapértelmezés szerint ez a beállítás a példányszám 1 értékre való skálázását adja meg, ha az erőforrás CPU-százaléka meghaladja a 70 százalékot. Hagyja meg az alapértelmezett értékeket, majd kattintson a **Hozzáadás**gombra.
  ![Méretezési beállítás létrehozása egy webalkalmazáshoz][6]
1. Most létrehozta az első skálázási szabályt. Vegye figyelembe, hogy az UX az ajánlott eljárásokat javasolja, és azt állítja be, hogy "a szabálynak legalább egy méretezése van." Ehhez tegye a következőket:

    a. Kattintson **a szabály hozzáadása**elemre.

    b. Az **operátor** beállítása **kisebb, mint**.

    c. Állítsa a küszöbértéket **20**-ra.

    d. Állítsa be a **műveletet** úgy, hogy **csökkentse a darabszámot**.

   Most olyan méretezési beállítással kell rendelkeznie, amely a CPU-használat alapján kibővíti/méretezi a skálázást.
   ![Skálázás CPU alapján][8]
1. Kattintson a **Save** (Mentés) gombra.

Gratulálunk! Most sikeresen létrehozta az első méretezési beállítást a webalkalmazás CPU-használat alapján történő méretezéséhez.

> [!NOTE]
> Ugyanezek a lépések érvényesek a virtuálisgép-méretezési csoport vagy a Cloud Service-szerepkör megkezdésére.

## <a name="other-considerations"></a>Egyéb szempontok
### <a name="scale-based-on-a-schedule"></a>Méretezés ütemterv alapján
A CPU-alapú skálázáson kívül a hét adott napjain különbözőképpen is beállíthatja a méretezést.

1. Kattintson **a skálázási feltétel hozzáadása**elemre.
1. A skálázási mód beállítása és a szabályok megegyeznek az alapértelmezett feltétellel.
1. Válassza az **adott napok** megismétlése az ütemtervhez lehetőséget.
1. Válassza ki a napokat és a kezdő/záró időpontot, ha alkalmazni szeretné a méretezési feltételt.

![Feltétel skálázása ütemterv alapján][9]
### <a name="scale-differently-on-specific-dates"></a>Eltérő méretezés adott dátumokon
A PROCESSZORon alapuló skálázás mellett az adott dátumok esetében különbözőképpen is beállíthatja a méretezést.

1. Kattintson **a skálázási feltétel hozzáadása**elemre.
1. A skálázási mód beállítása és a szabályok megegyeznek az alapértelmezett feltétellel.
1. Válassza az ütemterv **kezdő/záró dátumának megadása** lehetőséget.
1. Válassza ki a kezdő/befejező dátumokat és a kezdő/záró időpontot a skálázási feltétel alkalmazásához.

![Feltétel skálázása dátumok alapján][10]

### <a name="view-the-scale-history-of-your-resource"></a>Az erőforrás skálázási előzményeinek megtekintése
Ha az erőforrás fel-vagy leskálázásra kerül, egy eseményt naplóznak a tevékenység naplójában. Az elmúlt 24 órában megtekintheti az erőforrás-méretezési előzményeket a **futtatási előzmények** lapra való váltással.

![Előzmények][11]

Ha meg szeretné tekinteni a teljes méretezési előzményeket (akár 90 napig), **kattintson ide a további részletek megtekintéséhez**. Megnyílik a tevékenység naplója, amely az erőforrás és a kategória esetében előre be van jelölve.

### <a name="view-the-scale-definition-of-your-resource"></a>Az erőforrás méretezési definíciójának megtekintése
Az autoscale egy Azure Resource Manager erőforrás. Megtekintheti a méretezési definíciót a JSON-ban a **JSON** lapra való váltással.

![Méretezési definíció][12]

Ha szükséges, közvetlenül a JSON-ben végezheti el a módosításokat. Ezek a módosítások a mentés után is megjelennek.

### <a name="disable-autoscale-and-manually-scale-your-instances"></a>Az autoskálázás letiltása és a példányok manuális méretezése
Előfordulhat, hogy a jelenlegi méretezési beállítás letiltására és az erőforrás manuális méretezésére van szükség.

Kattintson a felül lévő **autoskálázás letiltása** gombra.
![Az autoskálázás letiltása][13]

> [!NOTE]
> Ez a beállítás letiltja a konfigurációt. Az autoskálázás újbóli engedélyezése után azonban visszatérhet.

Most már beállíthatja a manuálisan méretezni kívánt példányok számát.

![Manuális skálázás beállítása][14]

Az **autoskálázás engedélyezése** és a **Mentés**lehetőségre kattintva bármikor visszatérhet az autoscale méretezéshez.

## <a name="next-steps"></a>További lépések
- [Műveletnapló-riasztás létrehozása az összes autoskálázási motor műveleteinek figyeléséhez az előfizetésen](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Műveletnapló-riasztás létrehozása az összes sikertelen, az előfizetésen kívüli méretezési és kibővítő művelet figyeléséhez](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

<!--Reference-->
[1]:https://portal.azure.com
[2]: ./media/autoscale-get-started/azure-monitor-launch.png
[3]: ./media/autoscale-get-started/discover-autoscale-azure-monitor.png
[4]: https://docs.microsoft.com/azure/app-service/app-service-web-get-started-dotnet
[5]: ./media/autoscale-get-started/scale-setting-new-web-app.png
[6]: ./media/autoscale-get-started/create-scale-setting-web-app.png
[7]: ./media/autoscale-get-started/scale-in-recommendation.png
[8]: ./media/autoscale-get-started/scale-based-on-cpu.png
[9]: ./media/autoscale-get-started/scale-condition-schedule.png
[10]: ./media/autoscale-get-started/scale-condition-dates.png
[11]: ./media/autoscale-get-started/scale-history.png
[12]: ./media/autoscale-get-started/scale-definition-json.png
[13]: ./media/autoscale-get-started/disable-autoscale.png
[14]: ./media/autoscale-get-started/set-manualscale.png

