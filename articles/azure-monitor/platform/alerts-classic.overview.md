---
title: A Microsoft Azure és az Azure Monitor klasszikus riasztások áttekintése
description: Klasszikus riasztások elavulttá válnak. Riasztások figyelése az Azure erőforrás-metrikák, naplók vagy eseményeket, és értesítést a megadott feltétel teljesülése esetén lehetővé teszik.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/19/2018
ms.author: robb
ms.openlocfilehash: 96183e22f0aeafc681a782e7d3d8bd29a6ec9617
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65914705"
---
# <a name="what-are-classic-alerts-in-microsoft-azure"></a>Mik azok a Microsoft Azure klasszikus riasztások?

> [!NOTE]
> Ez a cikk ismerteti, hogyan hozhat létre a régebbi klasszikus metrikariasztásokat. Az Azure Monitor most már támogatja a [újabb közel valós idejű metrikákhoz kapcsolódó riasztások és a egy riasztások új kezelőfelülete](../../azure-monitor/platform/alerts-overview.md). Klasszikus riasztások [án megszűnik](https://docs.microsoft.com/azure/azure-monitor/platform/monitoring-classic-retirement).  
>

Riasztások lehetővé teszik a feltételek konfigurálása adatokon, és értesüljön róla, ha a feltétel egyezik a legújabb figyelési adatok.

## <a name="old-and-new-alerting-capabilities"></a>Régi és Új riasztási képességek

Az Azure Monitor, az Application Insights, a Log Analytics és a Service Health kellett külön elmúlt riasztási funkciókat. Az Azure túlóra, továbbfejlesztett, és a felhasználói felület és a különböző módszerek riasztások kombinált. A konszolidáció még folyamatban van.

Csak a klasszikus riasztások felhasználói képernyő az Azure Portalon a klasszikus riasztások is megtekintheti. Az ezen a képernyőn megjelenik a **klasszikus riasztások megtekintése** a riasztások képernyőn megjelenő gomb. 

 ![Riasztási lehetőségeket az Azure Portalon](media/alerts-classic.overview/monitor-alert-screen2.png)

Az új riasztások felhasználói felületet a klasszikus riasztások élmény felett az alábbi előnyökkel jár:
-   **Értesítési rendszer jobb** – minden újabb riasztás nevesített csoportok vagy értesítési és műveleteket, amelyeket több riasztás felhasználható Műveletcsoportok használata. Ne használjon Műveletcsoportok klasszikus metrikariasztásokat és a korábbi Log Analytics-riasztásokkal.
-   **A szerzői műveletekhez egyesített** – az összes riasztás létrehozása metrikákhoz, naplók és a tevékenység naplózása az Azure Monitor, Log Analytics között, és az Application Insights jelenleg egy helyen.
-   **Nézet aktivált Azure-portálon a Log Analytics riasztásait** – most már is lásd aktivált a Log Analytics-riasztások az előfizetésében. Ezek korábban egy külön portálon is.
-   **Aktivált riasztások és a riasztási szabályok szétválasztása** - riasztási szabályok (a riasztást kiváltó feltétel-definíció), és aktivált riasztások (példányát, a riasztási szabály robbantás) különbözteti meg, így a működési és a konfiguráció nézet el egymástól.
-   **Jobb munkafolyamat** – az új riasztások szerzői felhasználói élménnyel kapcsolatos útmutatókat a felhasználó mentén egy riasztási szabályt, ami lehetővé teszi az egyszerűbb, Fedezze fel a megfelelő tudnivaló a riasztások konfigurálásának folyamatán.
-   **Riasztások összevonása intelligens** és **figyelmeztetési állapotra állítja az** -újabb riasztások automatikus csoportosítási funkcióval együtt a túlterhelési felhasználói felületének csökkentése érdekében a hasonló riasztások megjelenítése tartalmazza. 

Az újabb metrikákhoz kapcsolódó riasztások szabályozhatják a klasszikus metrikariasztásokat a következő előnyökkel jár:
-   **Továbbfejlesztett késés**: Újabb metrikákhoz kapcsolódó riasztások akár percenkénti futtathatja. Régebbi metrikákhoz kapcsolódó riasztások mindig futtatja, 5 perces gyakorisággal. Újabb riasztások rendelkezik, növelje az értesítési vagy (3-5 perc) műveletet a probléma előfordulását kisebb késleltetés. Régebbi riasztások a típusától függően 5-15 perc.  Naplóriasztások általában 10, 15 perces késleltetés, hogy a naplók szükséges idő miatt van, de újabb feldolgozási módszerek idő csökkentheti. 
-   **Többdimenziós metrikák támogatása**: Azt a többdimenziós metrikák hogy felmérhesse a mérőszám egy érdekes szegmens riasztja Önt.
-   **Metrikai feltétel több felügyeleti**: Gazdagabb riasztási szabályok határozhatja meg. Az újabb riasztások a metrikák maximális, minimális, átlagos és teljes értékek figyelésére is alkalmas.
-   **Több metrika figyelése kombinált**: (Jelenleg legfeljebb két mérőszám) egyetlen szabállyal több metrikát követheti nyomon. Egy riasztás akkor aktiválódik, ha mindkét metrikák megsértik a megfelelő mértékben a küszöbértékeket a megadott időszakban.
-   **Értesítési rendszer jobb**: Minden újabb riasztás használata [Műveletcsoportok](../../azure-monitor/platform/action-groups.md), amely nevesített csoportok vagy értesítési és műveleteket, amelyeket több riasztás felhasználható.  Ne használjon Műveletcsoportok klasszikus metrikariasztásokat és a korábbi Log Analytics-riasztásokkal. 
-   **Naplók, metrikák** (nyilvános előzetes verzió): Átnézi a Log Analytics naplóadatokat is lehet ki kell olvasni, és az Azure Monitor-metrikák konvertálva, és majd kapni a hasonlóan más metrikákkal. Lásd: [riasztások (klasszikus)](alerts-classic.overview.md) a klasszikus riasztások jellemző terminológiát. 


## <a name="classic-alerts-on-azure-monitor-data"></a>Klasszikus riasztások az Azure Monitor-adatok
Klasszikus riasztások érhető el – metrikákhoz kapcsolódó riasztások és a tevékenységnapló-riasztások két típusa van.

* **Klasszikus metrikariasztásokat** – egy megadott metrika értékét átlép egy küszöbértéket, amelyekhez hozzárendeli ezt a riasztást aktivál. A riasztás egy értesítést állít elő, amikor áthaladnak a küszöbérték és a riasztási feltétel nem teljesül. Ezen a ponton a riasztás "Aktiválva" tekinthető. Egy másik értesítést állít elő a "Megoldott" – vagyis amikor újra áthaladnak a küszöbértéket, és a rendszer már nem teljesül.

* **Klasszikus tevékenységnapló-riasztások** -streamelési naplóriasztás, amely elindítja a tevékenységnapló esemény tétel, amely megfelel a szűrési feltételeknek. Ezek a riasztások rendelkezik csak egy állapota "Aktív". A riasztási motor egyszerűen alkalmazza a szűrési feltételeket a bármilyen új eseményre. Nem található a régebbi bejegyzések keres. Ezek a riasztások értesíti a felhasználót, egy új Service Health-incidens esetén, vagy amikor egy felhasználó vagy alkalmazás egy műveletet végez az előfizetésében, például "törlése a virtuális gép."

A diagnosztikai naplóadatokat Azure monitoron keresztül érhető el továbbíthatják az adatokat a Log analyticsbe (korábbi nevén OMS), és a Log Analytics-lekérdezés riasztás használni. Log Analytics most használja a [módszer új riasztás](../../azure-monitor/platform/alerts-overview.md) 

A következő ábra összefoglalja az Azure Monitor és, illesztésekhez, hogyan lehet az adatok minden riasztás adatforrásokat.

![A riasztások ismertetése](media/alerts-classic.overview/Alerts_Overview_Resource_v5.png)

## <a name="taxonomy-of-alerts-classic"></a>Riasztások (klasszikus) a besorolás
Az Azure klasszikus riasztások és azok funkcióit használja az alábbi kifejezéseket:
* **Riasztási** -definíciójának feltételek (egy vagy több szabály vagy feltételek), amely akkor aktiválódik, amikor teljesül.
* **Aktív** -állapota egy klasszikus riasztás által megadott feltételek teljesülése esetén.
* **Megoldott** -az állapot, ha klasszikus riasztás által megadott feltételek korábban teljesítettnek után már nem teljesül.
* **Értesítési** – alapján egy klasszikus riasztás aktív egyre minden végrehajtott műveletet.
* **A művelet** – egy adott hívás küldött értesítést (például e-mail-cím vagy egy webhook URL-könyvelési), a fogadó. Értesítések általában is indíthat több művelet.

## <a name="how-do-i-receive-a-notification-from-an-azure-monitor-classic-alert"></a>Hogyan kapják meg az értesítést a klasszikus Azure Monitor riasztást?
Hagyományosan az Azure-riasztások a különböző szolgáltatások használt saját beépített értesítésekkel módszerek. 

Az Azure Monitor létrehozott csoportosítási újrafelhasználható értesítést nevű *Műveletcsoportok*. Műveletcsoportok adjon meg egy értesítési fogadók. Bármikor riasztás aktiválva van, amely hivatkozik a műveletcsoport, az összes fogadók, értesítést kap. Műveletcsoportok engedélyezése újra felhasználhatja a fogadók (például a telefonos mérnök lista) csoportja számos riasztási objektumokhoz. Műveletcsoportok egy webhook URL-CÍMÉT, e-mail-címeket, az SMS-számok és számos más művelet mellett úgy támogathatja az értesítéseket.  További információkért lásd: [Műveletcsoportok](../../azure-monitor/platform/action-groups.md). 

Régebbi klasszikus tevékenységnapló-riasztások Műveletcsoportok használata.

Azonban a régebbi metrikákhoz kapcsolódó riasztások nem Műveletcsoportok használata. Ehelyett a következő műveleteket lehet konfigurálni: 
- E-mail értesítéseket küldhet a szolgáltatás-rendszergazda, társrendszergazdák, vagy további e-mail-címek.
- Egy webhookot, amely lehetővé teszi további automation műveletek elindításához hívja meg.

Webhookok lehetővé teszi, hogy automation és a szervizeléshez, például használatával:
- Azure Automation-runbook
- Azure Function
- Azure Logic App
- Egy külső szolgáltatás

## <a name="next-steps"></a>További lépések
Riasztási szabályok, és konfigurálja őket az adatainak beolvasása:

* Tudjon meg többet [metrikák](data-platform.md)
* Konfigurálása [klasszikus metrika riasztások az Azure Portalon keresztül](alerts-classic-portal.md)
* Konfigurálása [metrika riasztások klasszikus PowerShell](alerts-classic-portal.md)
* Konfigurálása [klasszikus metrika riasztások parancssori felület (CLI)](alerts-classic-portal.md)
* Konfigurálása [klasszikus metrika riasztások az Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)
* Tudjon meg többet [tevékenységnapló](activity-logs-overview.md)
* Konfigurálása [tevékenységnapló-riasztások Azure-portálon](activity-log-alerts.md)
* Konfigurálása [Resource Manageren keresztül tevékenységnapló-riasztások](alerts-activity-log.md)
* Tekintse át a [tevékenység log riasztási webhook sémáról](activity-log-alerts-webhook.md)
* Tudjon meg többet [Műveletcsoportok](action-groups.md)
* Konfigurálása [újabb riasztások](alerts-metric.md)
