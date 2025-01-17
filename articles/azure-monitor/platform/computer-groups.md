---
title: Számítógépcsoportok a Azure Monitor log lekérdezésekben | Microsoft Docs
description: A Azure Monitorban lévő számítógépcsoportok lehetővé teszik a naplózási lekérdezések hatókörét a számítógépek adott csoportjára.  Ez a cikk azokat a különböző módszereket ismerteti, amelyekkel számítógépcsoportok hozhatók létre, és hogyan használhatók a naplók a naplózási lekérdezésekben.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: a28b9e8a-6761-4ead-aa61-c8451ca90125
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/05/2019
ms.author: bwren
ms.openlocfilehash: ae423b6fb141cab4038e65ba85c6067f1c23aee0
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68320679"
---
# <a name="computer-groups-in-azure-monitor-log-queries"></a>Számítógépcsoportok a Azure Monitor log lekérdezésekben
A Azure Monitorban lévő számítógépcsoportok lehetővé teszik a [naplózási lekérdezések](../log-query/log-query-overview.md) hatókörét a számítógépek adott csoportjára.  Minden csoport fel van töltve, vagy az Ön által meghatározott lekérdezés segítségével számítógépek vagy csoportok különböző forrásokból származó importálásával.  Ha a csoport egy napló lekérdezésében szerepel, az eredmények a csoport számítógépeinek megfelelő rekordokra korlátozódnak.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="creating-a-computer-group"></a>Számítógépcsoport létrehozása
Az alábbi táblázatban felsorolt módszerek bármelyikével létrehozhat egy számítógépcsoportot a Azure Monitorban.  Részletek az egyes módszerek az alábbi szakaszokban találhatók. 

| Módszer | Leírás |
|:--- |:--- |
| Napló lekérdezése |Hozzon létre egy olyan log-lekérdezést, amely a számítógépek listáját adja vissza. |
| Log Search API |A log Search API használatával programozott módon hozhat létre számítógépcsoportot a naplófájlok eredményei alapján. |
| Active Directory |A csoport tagjainak automatikus vizsgálata minden olyan ügynök számítógépén, amely egy Active Directory tartomány tagja, és minden egyes biztonsági csoport számára létrehoz egy csoportot Azure Monitor. (Csak Windows-gépek esetén)|
| Configuration Manager | Gyűjtemények importálása a System Center Configuration Managerból, és mindegyikhez hozzon létre egy csoportot a Azure Monitorban. |
| A Windows Server Update Services |Automatikusan vizsgálja meg a WSUS-kiszolgálókat vagy-ügyfeleket a célcsoportok célzásához, és hozzon létre egy csoportot Azure Monitorban. |

### <a name="log-query"></a>Napló lekérdezése
A napló lekérdezésből létrehozott számítógépcsoportok tartalmazzák az Ön által meghatározott lekérdezés által visszaadott összes számítógépet.  Ez a lekérdezés futtatása minden alkalommal, amikor a számítógép (csoport) használja úgy, hogy a csoport létrehozása óta módosítások is megjelenik.  

Minden lekérdezés egy számítógép (csoport) is használhat, de egy különálló számítógépek csoportja használatával kell visszaadnia `distinct Computer`.  Az alábbiakban egy tipikus példát tartalmazó lekérdezés látható, amelyet számítógép-csoportként használhat.

    Heartbeat | where Computer contains "srv" | distinct Computer

Az alábbi eljárás segítségével számítógépcsoport létrehozása egy Naplókeresés, az Azure Portalon.

1. A Azure Portal **Azure monitor** menüjében kattintson a **naplók** elemre.
1. Hozzon létre és futtasson egy lekérdezést, amely visszaadja a csoportba felhasználandó számítógépeket.
1. Kattintson a képernyő felső részén található **Mentés** gombra.
1. Módosítsa a **Mentés másként** **funkciót** , és válassza a **lekérdezés mentése számítógépcsoportként**lehetőséget.
1. Adja meg a táblázatban leírt számítógépcsoport minden tulajdonságának értékeit, majd kattintson a **Mentés**gombra.

A következő táblázat ismerteti, amelyek meghatározzák egy számítógép (csoport) tulajdonságait.

| Tulajdonság | Description |
|:---|:---|
| Name (Név)   | A portálon megjelenítendő lekérdezés neve. |
| Függvényalias | A számítógépcsoport-lekérdezés azonosítására használt egyedi nevet. |
| Category       | A lekérdezéseknek a portálon való rendszerezésének kategóriája. |


### <a name="active-directory"></a>Active Directory
Ha a Azure Monitor Active Directory csoporttagság importálására konfigurálja, akkor a a Log Analytics ügynökkel rendelkező Windows tartományhoz csatlakozó számítógépek csoportjának tagságát elemzi.  A rendszer létrehoz egy számítógépcsoportot Azure Monitor a Active Directory minden egyes biztonsági csoportjához, és mindegyik Windows-számítógép hozzá lesz adva a azon biztonsági csoportokhoz tartozó számítógép-csoportokhoz, amelyek tagjai a csoportnak.  A csoporttagság 4 óránként folyamatosan frissítjük.  

> [!NOTE]
> Az importált Active Directory csoportok csak Windows rendszerű gépeket tartalmaznak.

A Azure Monitor konfigurálható úgy, hogy Active Directory biztonsági csoportokat importáljon a Azure Portal Log Analytics munkaterületének **speciális beállításaiból** .  Válassza ki **számítógépcsoportok**, **Active Directory**, majd **importálás az Active Directory-csoporttagságok számítógépekről**.  Nincs szükség további konfigurációra.

![A számítógépcsoportok Active Directoryból](media/computer-groups/configure-activedirectory.png)

Ha csoportok lettek importálva, a menü mutatja az a csoport tagjai észlelt számítógépek számát és a csoport importálva számát.  Kattintson a visszaadandó alábbi hivatkozások egyikét a **ComputerGroup** ezeket az adatokat rögzíti.

### <a name="windows-server-update-service"></a>Windows Server Update Service
Ha a WSUS-csoporttagságok importálását Azure Monitor konfigurálja, az elemzi a Log Analytics ügynökkel rendelkező számítógépek célcsoport-kezelési csoportjának tagságát.  Ha ügyféloldali célzást használ, a Azure Monitorhoz csatlakoztatott és a WSUS-célcsoportok részét képező bármely számítógép rendelkezik a csoporttagság importálásával Azure Monitor. Ha kiszolgálóoldali célzást használ, akkor a Log Analytics ügynököt a WSUS-kiszolgálóra kell telepíteni ahhoz, hogy a csoporttagság adatai Azure Monitorba legyenek importálva.  A csoporttagság 4 óránként folyamatosan frissítjük. 

A Azure Monitor konfigurálható úgy, hogy a Azure Portal Log Analytics munkaterületének **speciális beállításaiból** importálja a WSUS-csoportokat.  Válassza ki **számítógépcsoportok**, **WSUS**, majd **importálási WSUS-csoporttagságok**.  Nincs szükség további konfigurációra.

![Számítógépcsoportokat a WSUS-nak](media/computer-groups/configure-wsus.png)

Ha csoportok lettek importálva, a menü mutatja az a csoport tagjai észlelt számítógépek számát és a csoport importálva számát.  Kattintson a visszaadandó alábbi hivatkozások egyikét a **ComputerGroup** ezeket az adatokat rögzíti.

### <a name="system-center-configuration-manager"></a>System Center Configuration Manager
Ha a Azure Monitor Configuration Manager gyűjteményi tagságok importálására konfigurálja, akkor minden gyűjteményhez létrehoz egy számítógépcsoportot.  A gyűjtemény tagsági információ 3 óránként rendszer olvassa be a számítógépcsoportok naprakészen tartása. 

Configuration Manager gyűjtemények importálása előtt csatlakoznia kell [a Configuration Managerhoz a Azure monitorhoz](collect-sccm.md).  

![Számítógépcsoportok az SCCM-ből](media/computer-groups/configure-sccm.png)

Gyűjtemények lettek importálva, ha a menüben a csoport tagjai észlelt számítógépek számát és a csoport importálva számát sorolja fel.  Kattintson a visszaadandó alábbi hivatkozások egyikét a **ComputerGroup** ezeket az adatokat rögzíti.

## <a name="managing-computer-groups"></a>Számítógépcsoportok kezelése
Megtekintheti a naplózási lekérdezésből létrehozott számítógép-csoportokat, illetve a naplóbeli keresési API-t a Azure Portal Log Analytics munkaterületének **speciális beállításai** között.  Válassza ki **számítógépcsoportok** , majd **mentett csoportok**.  

Kattintson a **x** a a **eltávolítása** törölni a számítógép (csoport) oszlopban.  Kattintson a **tagok megtekintése** ikon egy csoportra, amely visszaadja a tagok a csoport Naplókeresés futtatásához.  Egy számítógép (csoport) nem módosítható, de ehelyett kell törölje és hozza létre újra a módosított beállításokkal.

![Mentett számítógépcsoportok](media/computer-groups/configure-saved.png)


## <a name="using-a-computer-group-in-a-log-query"></a>Számítógépcsoport használata egy naplózási lekérdezésben
A lekérdezésekben egy, az alias függvényként való kezelésével létrehozott számítógépcsoportot használ, jellemzően a következő szintaxissal:

  `Table | where Computer in (ComputerGroup)`

Ha például a következő segítségével mycomputergroup nevű számítógép csoport lévő updateSummary típusú rekordok csak számítógépek visszaadása.
 
  `UpdateSummary | where Computer in (mycomputergroup)`


Az importált számítógép- és az érintett számítógépekre vannak tárolva a **ComputerGroup** tábla.  Például a következő lekérdezés termékazonosítóhoz azon számítógépek listáját a tartományi számítógépek csoport az Active Directoryból. 

  `ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer`

A következő lekérdezést a tartományi számítógépek adna vissza updateSummary típusú rekordok csak a számítógépek számára.

  ```
  let ADComputers = ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer;
  UpdateSummary | where Computer in (ADComputers)
  ```




## <a name="computer-group-records"></a>Számítógép biztonságicsoport-rekordok
Egy rekord jön létre minden egyes létrehozott Active Directory vagy a WSUS számítógépcsoport-tagság a Log Analytics-munkaterületen.  Ezeket a rekordokat rendelkezik olyan típusú **ComputerGroup** , és a tulajdonságait az alábbi táblázatban.  A rendszer nem hoz létre rekordokat a számítógép-csoportokhoz a naplózási lekérdezések alapján.

| Tulajdonság | Leírás |
|:--- |:--- |
| `Type` |*ComputerGroup* |
| `SourceSystem` |*SourceSystem* |
| `Computer` |A számítógép neve. |
| `Group` |A csoport nevét. |
| `GroupFullName` |Teljes elérési útja a csoporthoz, többek között a forrás- és adatforrás neve. |
| `GroupSource` |Forrás-csoporthoz való gyűjtése történt. <br><br>ActiveDirectory<br>WSUS<br>WSUSClientTargeting |
| `GroupSourceName` |A forrás, az összegyűjtött a csoport nevére.  Az Active Directory Ez a tartománynév. |
| `ManagementGroupName` |A felügyeleti csoport neve SCOM-ügynökök esetén.  Más ügynökök esetén ez AOI -\<munkaterület azonosítója\> |
| `TimeGenerated` |Dátum és időpont a számítógép (csoport) létrehozott vagy frissített. |

## <a name="next-steps"></a>További lépések
* Ismerje meg [lekérdezések naplózását](../log-query/log-query-overview.md) az adatforrások és megoldások gyűjtött adatok elemzéséhez.  

