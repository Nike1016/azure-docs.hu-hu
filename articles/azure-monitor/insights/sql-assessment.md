---
title: Az Azure monitorral az SQL Server-környezet optimalizálása |} A Microsoft Docs
description: Az Azure monitorral az SQL Health Check megoldás segítségével felmérheti a kockázatait és állapotát, a környezetek rendszeres időközönkénti.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: e297eb57-1718-4cfe-a241-b9e84b2c42ac
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: magoedte
ms.openlocfilehash: 94b23bc29c3c986e6a0cd74e0805b5d47ce35849
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "62120631"
---
# <a name="optimize-your-sql-environment-with-the-sql-server-health-check-solution-in-azure-monitor"></a>Optimalizálhatja a környezetet SQL az SQL Server állapotának ellenőrzése megoldás az Azure monitorban

![Symbol SQL állapotának ellenőrzése](./media/sql-assessment/sql-assessment-symbol.png)

Az SQL Health Check megoldás segítségével felmérheti a kockázatokat és a kiszolgáló-környezetek állapotát rendszeres időközönkénti. Ez a cikk segítséget nyújt a megoldás telepítése, hogy a potenciális problémákat korrekciós műveleteket hajthatja végre.

Ez a megoldás a telepített kiszolgálói infrastruktúrát vonatkozó javaslatok rangsorolt listáját tartalmazza. Az ajánlások szerint vannak kategóriába sorolva hat fókuszterületre vonatkozóan, amely segítségével gyorsan megismerheti a kockázatokat, és korrekciós műveletek.

Javaslatok útmutatóként tudással és ezer vevő a Microsoft mérnökei tapasztalatai alapulnak. Minden javaslat ismerteti, miért számít a probléma előfordulhat, hogy Önnek és a javasolt változások megvalósítása.

Kiválaszthatja, hogy a szervezet számára legfontosabb, és nyomon követheti az előrehaladást kockázati ingyenes és kifogástalan állapotú környezetét fókuszterületek.

Miután hozzáadta a megoldást, és az értékelés befejeződött, összefoglaló adatait fókuszterületek jelenik meg a **SQL állapotának ellenőrzése** a környezetében az infrastruktúra-irányítópulton. A következő szakaszok ismertetik az információk használata az a **SQL állapotának ellenőrzése** irányítópult, amelyen megtekintheti és megfelelő ajánlott műveletek az SQL Server-infrastruktúrához.

![SQL állapotának ellenőrzése csempét ábrázoló kép](./media/sql-assessment/sql-healthcheck-summary-tile.png)

![SQL állapotának ellenőrzése irányítópult képe](./media/sql-assessment/sql-healthcheck-dashboard-01.png)

## <a name="prerequisites"></a>Előfeltételek

* Az SQL Health Check megoldás telepítve minden olyan számítógépen, a Microsoft Monitoring Agent (MMA) telepítve van a .NET-keretrendszer 4 támogatott verziója szükséges.  Az MMA-ügynök System Center 2016 – Operations Manager és az Operations Manager 2012 R2 és az Azure Monitor használják.  
* A megoldás támogatja az SQL Server 2012, 2014 vagy 2016 verzióra.
* Az SQL Health Check megoldás hozzáadása az Azure Portalon az Azure marketplace-ről Log Analytics-munkaterületet.  A megoldás telepítéséhez rendszergazdaként vagy az Azure-előfizetés közreműködőjének kell lennie.

  > [!NOTE]
  > A megoldás hozzáadását követően a AdvisorAssessment.exe fájlt adnak hozzá, ügynökökkel kiszolgálókra. Konfigurációs adatok olvasása és feldolgozása a felhőben az Azure Monitor majd küldött. A fogadott adatokat logikát alkalmaz, és a felhőszolgáltatás-adatait rögzíti.
  >
  >

Hajtsa végre az állapot-ellenőrzés az SQL Server-kiszolgálók ellen, szükségük van egy ügynök és a kapcsolatot az Azure monitornak a következő támogatott módszerek egyikének használatával:

1. Telepítse a [a Microsoft Monitoring Agent (MMA)](../../azure-monitor/platform/agent-windows.md) , ha a kiszolgáló már nem áll a System Center 2016 – Operations Manager vagy Operations Manager 2012 R2.
2. Ha figyelhető a System Center 2016 – Operations Manager vagy Operations Manager 2012 R2 és a felügyeleti csoport nincs integrálva az Azure Monitor szolgáltatással, a kiszolgáló is lehet, többhelyű az adatok gyűjtéséhez és a szolgáltatás továbbítja, és továbbra is a Log Analytics szolgáltatással az Operations Manager által figyelt.  
3. Ellenkező esetben az Operations Manager felügyeleti csoport integrálva van a szolgáltatást, ha hozzá kell a tartományvezérlők, az adatgyűjtés a szolgáltatás a következő szakasz lépéseit [adja hozzá az ügynök által felügyelt számítógépek](../../azure-monitor/platform/om-agents.md#connecting-operations-manager-to-azure-monitor) engedélyezése után a megoldás a munkaterületén.  

Az ügynököt az SQL Server, az Operations Manager felügyeleti csoport jelentéseket gyűjti az adatokat, továbbítja a hozzárendelt felügyeleti kiszolgálónak, és majd küld egy felügyeleti kiszolgálóról közvetlenül az Azure Monitor.  Az adatok nem szerepel, az Operations Manager-adatbázisokról.  

Ha az SQL Server rendszer Operations Manager által figyelt, kell konfigurálása egy Operations Manager futtató fiókot. Lásd: [az Operations Manager futtató fiókok az Azure Monitor](#operations-manager-run-as-accounts-for-log-analytics) alatt további információt.

## <a name="sql-health-check-data-collection-details"></a>Adatok gyűjtése részletek SQL állapotának ellenőrzése
SQL állapotának ellenőrzése, hogy engedélyezte-ügynök használatával a következő forrásokból gyűjt adatokat:

* Windows Management Instrumentation (WMI)
* Beállításjegyzék
* Teljesítményszámlálók
* Az SQL Server dinamikus felügyeleti eredmények megtekintése

Adatokat az SQL Server-kiszolgálón gyűjti, és továbbítja a Log Analytics hét naponta.

## <a name="operations-manager-run-as-accounts-for-log-analytics"></a>Az Operations Manager futtató fiókokat a Log Analytics
A log Analytics az Operations Manager ügynök és a felügyeleti csoport gyűjteni, és adatokat küldeni a Log Analytics szolgáltatás használ. Log Analytics-buildek esetén a számítási feladatokhoz biztosít a felügyeleti csomagok értéknövelt szolgáltatásokat. Minden számítási feladathoz munkaterhelés-specifikus jogosultságokkal eltérő biztonsági környezetben, például a tartományi felhasználói fiók felügyeleti csomagjainak futtatásához szükséges. Meg kell adnia a hitelesítő adatokat az Operations Manager futtató fiók konfigurálásával.

Az alábbi információk segítségével állítsa be az Operations Manager futtató fiókot az SQL állapotának ellenőrzése.

### <a name="set-the-run-as-account-for-sql-health-check"></a>Állítsa be a futtató fiókot az SQL állapotának ellenőrzése
 Ha az SQL Server felügyeleti csomag már használ, ez a futtató konfiguráció kell használnia.

#### <a name="to-configure-the-sql-run-as-account-in-the-operations-console"></a>Az operatív konzol az SQL futtató fiók konfigurálása
> [!NOTE]
> Alapértelmezés szerint a felügyeleti csomag a munkafolyamatok a helyi rendszer fiók biztonsági környezetében fut. Ha használja a Microsoft Monitoring Agent közvetlenül csatlakozik a szolgáltatáshoz helyett közvetlenül jelentéskészítés az Operations Manager felügyeleti csoporthoz, 1 – 5. lépés kihagyható, és futtassa a T-SQL vagy a PowerShell-mintát, adja meg az NT AUTHORITY\SYSTEM, mint a a felhasználó neve.
>
>

1. Az Operations Manager rendszerben nyissa meg az operatív konzolt, és kattintson **felügyeleti**.
2. A **futtató konfiguráció**, kattintson a **profilok**, és nyissa meg a **SQL Assessment futtató profil**.
3. Az a **futtató fiókok** kattintson **Hozzáadás**.
4. Válasszon ki egy Windows futtató fiókot, amely tartalmazza az SQL Serverhez szükséges hitelesítő adatokat, vagy kattintson **új** hozhat létre egyet.

   > [!NOTE]
   > A futtató fiók típusa Windows kell lennie. A futtató fiókot is kell lennie minden üzemeltető SQL Server-példányok Windows-kiszolgáló helyi Rendszergazdák csoport része.
   >
   >
5. Kattintson a **Save** (Mentés) gombra.
6. Módosíthatja, és majd minden egyes SQL Server-példányon adja meg a futtató fiók az állapot-ellenőrzés végrehajtásához szükséges minimális engedélyeket, hajtsa végre a következő T-SQL-minta. Azonban nem szükséges ezt megtenni, ha a futtató fiók már része a sysadmin (rendszergazda) kiszolgálói szerepkör az SQL Server-példányokon.

```
    ---
    -- Replace <UserName> with the actual user name being used as Run As Account.
    USE master

    -- Create login for the user, comment this line if login is already created.
    CREATE LOGIN [<UserName>] FROM WINDOWS

    -- Grant permissions to user.
    GRANT VIEW SERVER STATE TO [<UserName>]
    GRANT VIEW ANY DEFINITION TO [<UserName>]
    GRANT VIEW ANY DATABASE TO [<UserName>]

    -- Add database user for all the databases on SQL Server Instance, this is required for connecting to individual databases.
    -- NOTE: This command must be run anytime new databases are added to SQL Server instances.
    EXEC sp_msforeachdb N'USE [?]; CREATE USER [<UserName>] FOR LOGIN [<UserName>];'

```

#### <a name="to-configure-the-sql-run-as-account-using-windows-powershell"></a>A Windows PowerShell használatával az SQL futtató fiók konfigurálása
Nyisson meg egy PowerShell-ablakot, és futtassa a következő szkriptet az információkkal már frissítése után:

```
    import-module OperationsManager
    New-SCOMManagementGroupConnection "<your management group name>"

    $profile = Get-SCOMRunAsProfile -DisplayName "SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account
```

## <a name="understanding-how-recommendations-are-prioritized"></a>Hogyan kerülnek előrébb a javaslatok megértése
Minden végrehajtott javaslat, amely azonosítja a relatív fontosságát az ajánlás súlyozási értéket kap. Csak a tíz legfontosabb javaslatok láthatók.

### <a name="how-weights-are-calculated"></a>Hogyan számítják ki súlyok
Súlyozás három fő tényezők alapján az összesített értékek:

* A *valószínűségi* , hogy az azonosított probléma problémákat okozhat. Nagyobb a valószínűsége annak használatára, az ajánlás nagyobb általános pontszámot.
* A *hatás* a szervezet azt problémát okozni, ha a probléma. Egy újabb hatás-adatbázisnak felel meg az ajánlás nagyobb általános pontszámot.
* A *erőfeszítés* történő megvalósításához szükséges. A javaslat egy kisebb összesített pontszám magasabb erőfeszítéssel állapotnak felel meg.

Az egyes javaslatok a súlyozás van a teljes elérhető egyes fókuszterület pontszám százalékban kifejezve. Például ha az adott javaslat a biztonság és megfelelőség fókuszterület 5 %-os pontszámot, a javaslat megvalósítása növeli az átfogó biztonsági és megfelelőségi pontszám szerint 5 %.

### <a name="focus-areas"></a>Fókuszterületek
**Biztonsági és megfelelőségi** -e fókuszterület esetleges biztonsági fenyegetések és a problémák, a vállalati szabályzatok és a technikai, jogszabályi és szabályozásoknak való megfelelőség követelményeinek való javaslatait mutatja be.

**Rendelkezésre állás és üzleti folytonosság** -e fókuszterület javaslatait a szolgáltatás rendelkezésre állása, az infrastruktúra és a vállalati védelme rugalmasságát mutatja be.

**Teljesítmény és méretezhetőség** -e fókuszterület látható ajánlásokat a szervezet IT-infrastruktúráját a növeléssel, ellenőrizze, hogy informatikai környezete megfelel az aktuális teljesítménykövetelményeknek, és tud válaszolni az infrastruktúra módosítása szüksége van.

**Frissítés, Migrálás és üzembe helyezés** -e fókuszterület segítséget frissítése, migrálása és üzembe helyezése az SQL Server a meglévő infrastruktúra javaslatait mutatja be.

**Műveletek és a figyelés** -e fókuszterület leegyszerűsítheti az informatikai műveletek, és megelőző jellegű karbantartás végrehajtása és a teljesítmény maximalizálása érdekében javaslatait mutatja be.

**Változás- és konfigurációkezelés** -e fókuszterület segítenek védelmét mindennapos, győződjön meg arról, hogy ne negatívan befolyásolja az infrastruktúrák meg létrehozni a Változáskezelő eljárások, és a nyomon követése és naplózása javaslatait mutatja be. rendszer konfigurációját.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Tárhelyeken minden fókuszterület a 100 %-os pontozása?
Nem feltétlenül. A javaslatok tudással és több ezer ügyfél által a Microsoft mérnökei szerzett tapasztalatok alapulnak. Azonban nem két kiszolgáló infrastruktúrák azonosak, és lehet, hogy több vagy kevesebb releváns konkrét javaslatokért. Például bizonyos biztonsági javaslatok kevésbé fontos, ha a virtuális gépek nem jelennek meg az interneten a lehet. Lehet, hogy egyes rendelkezésre állási javaslatok kevesebb releváns alacsony prioritású alkalmi adatok összegyűjtésére és a reporting services esetén. Érett fontos problémák a kevésbé fontos, hogy egy indítási is lehet. Előfordulhat, hogy szeretné azonosítani, melyik fókuszterületek a munkájukhoz, és keresse meg, hogyan a pontszámokat az idő előrehaladtával változik.

Minden javaslat, hogy miért fontos vonatkozó útmutatást tartalmaz. Ez az útmutató használjon kiértékelését, hogy a javaslat megvalósítása alkalmas, az informatikai szolgáltatások jellegét, és a szervezet igényeit.

## <a name="use-health-check-focus-area-recommendations"></a>Fókusz terület ajánlásokkal állapotának ellenőrzése
-Felmérési megoldást az Azure Monitor használata előtt rendelkeznie kell a telepített megoldás.  Azt követően, javaslatok összegzését az SQL állapotának ellenőrzése csempe használatával megtekintheti a **áttekintése** oldal az Azure Monitor az Azure Portalon.

Az összesített megfelelőségi értékeléseket az infrastruktúrát, és a-feltárás javaslatok megtekintése.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Egy fókuszterület javaslatok megtekintése, és korrekciós műveletek
1. Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.
2. Az Azure Portalon kattintson a bal alsó sarokban található **További szolgáltatások** elemre. Az erőforrások listájába írja be a **Monitorozás** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza a **Monitorozás** lehetőséget.
3. Az a **Insights** szakaszában a menüből válassza **további**.  
4. Az a **áttekintése** lap, kattintson a **SQL állapotának ellenőrzése** csempére.
5. Az a **állapotának ellenőrzése** lapon. tekintse át az összefoglaló adatokat az egyik a fókusz terület paneleket, majd kattintson egy adott fókuszterület javaslatok megtekintése.
6. A bármelyik, fókusz terület megtekintheti a rangsorolt javaslatok arról, hogy a környezetben. Alatt egy javaslatra kattint **érintett objektumok** Miért jön létre a javaslat részleteinek megtekintéséhez.<br><br> ![SQL állapotának ellenőrzése javaslatok képe](./media/sql-assessment/sql-healthcheck-dashboard-02.png)<br>
7. Az ajánlott javítási műveleteket hajthatja végre **javasolt műveletek**. Az elem történnek, ha újabb értékelések, amely ajánlott műveletek származnak, és növeli a megfelelőségi pontszám lesz felvéve. Kijavított elemek jelennek meg **átadott objektumok**.

## <a name="ignore-recommendations"></a>Hagyja figyelmen kívül a javaslatok
Ha javaslatoknál, amelyeket figyelmen kívül szeretne, létrehozhat egy szövegfájlt, amely az Azure Monitor használatával fogja megakadályozása javaslatokat az értékelés eredményeinek parancsot.

### <a name="to-identify-recommendations-that-you-will-ignore"></a>Javaslatok, amelyek figyelmen kívül hagyja majd azonosításához
1. Az Azure Monitor menüjében kattintson **naplók**.
2. A következő lista ajánlásokat, amelyek nem tudták lekérdezés használata a környezetében.

    ```
    SQLAssessmentRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```
    Íme egy Képernyőkép a naplólekérdezés:<br><br> ![nem sikerült javaslatokat](./media/sql-assessment/sql-assess-failed-recommendations.png)<br>

3. Válassza ki a javaslatok, amelyek figyelmen kívül kívánja. Szeretné használni az értékeket a RecommendationId az alábbi eljárás írja le.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Hozhat létre és használhat egy IgnoreRecommendations.txt szövegfájl
1. Hozzon létre egy fájlt IgnoreRecommendations.txt.
2. Illessze be, vagy írjon be minden egyes RecommendationId minden javaslat, amelyet az Azure Monitor figyelmen kívül, külön sorban, majd mentse és zárja be a fájlt.
3. Helyezni a fájlt a következő mappában minden olyan számítógépen, ahová szeretné az Azure Monitor figyelmen kívül hagyja a javaslatokat.
   * A Microsoft Monitoring Agent (közvetlenül vagy Operations Manager keresztül csatlakozik) – a számítógépeken *SystemDrive*: Monitoring Agent\Agent \Program Files\Microsoft
   * Az Operations Manager felügyeleti kiszolgálón – *SystemDrive*: System Center 2012 R2\Operations Manager\Server \Program Files\Microsoft
   * Az Operations Manager 2016 felügyeleti kiszolgálón – *SystemDrive*: System Center 2016\Operations Manager\Server \Program Files\Microsoft

### <a name="to-verify-that-recommendations-are-ignored"></a>Győződjön meg arról, hogy a javaslatok figyelmen kívül hagyja a
1. Után a következő ütemezett értékelési fut le, alapértelmezésben 7 naponta, a megadott ajánlásokat vannak megjelölve az figyelmen kívül hagyva, és nem jelenik meg az értékelés irányítópulton.
2. A következő naplófájl-keresési lekérdezések segítségével a figyelmen kívül hagyott javaslatok listája.

    ```
    SQLAssessmentRecommendation | where RecommendationResult == "Ignored" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```
3. Ha később úgy dönt, hogy szeretné-e figyelmen kívül hagyott javaslatok, IgnoreRecommendations.txt fájlok eltávolítása, vagy távolíthatja el RecommendationIDs őket.

## <a name="sql-health-check-solution-faq"></a>SQL Health Check megoldás – gyakori kérdések
*Milyen gyakran fut a állapot-ellenőrzése?*

* Az ellenőrzés hét naponta fut.

*Van mód konfigurálása, hogy milyen gyakran az ellenőrzés fut?*

* Jelenleg nem.

*Ha egy másik kiszolgáló fel van derítve, miután hozzá van adva az SQL Health Check megoldás, azt ellenőrzött?*

* Igen, ez be van jelölve, majd a hét naponta felderítése után.

*Ha egy kiszolgáló le van szerelve, amikor azt eltávolítjuk az állapot-ellenőrzés?*

* Ha a kiszolgáló nem küldenek adatokat a 3 hét, a rendszer eltávolítja.

*Mi a neve, a folyamat, amely az adatgyűjtés?*

* AdvisorAssessment.exe

*Mennyi ideig tart a gyűjtendő adatokat?*

* A tényleges adatok gyűjtése a kiszolgálón körülbelül 1 órát vesz igénybe. Ez hosszabb időt vehet igénybe, amelyek nagy számú SQL Server-példányok vagy adatbázisok kiszolgálókon.

*Milyen típusú adatokat gyűjt?*

* A következő típusú adatokat is gyűjti:
  * WMI
  * Beállításjegyzék
  * Teljesítményszámlálók
  * Az SQL dinamikus felügyeleti nézetekkel (DMV).

*Van mód a konfigurálását, amikor az adatgyűjtés történik?*

* Jelenleg nem.

*Miért van a futtató fiók konfigurálása?*

* Az SQL Server SQL-lekérdezések kis számú futnak. Ahhoz, hogy futtatni egy futtató fiókot, amely az SQL VIEW SERVER STATE engedélyeket kell használni.  Emellett lekérdezéséhez WMI, helyi rendszergazdai hitelesítő adatok szükségesek.

*Miért jelennek meg a csak az első 10-javaslatok?*

* Helyett így elsöprő teljesnek feladatot, azt javasoljuk, hogy arra összpontosítunk, először azoknak a rangsorolt javaslatok. Oldja meg őket, miután további javaslatokat is elérhetőek lesznek. Ha inkább a részletes listát, megtekintheti a Log Analytics naplóbeli keresés használatával összes javaslatot.

*Van mód figyelmen kívül hagyja a javaslatot?*

* Igen, tekintse meg [figyelmen kívül hagyja a javaslatok](#ignore-recommendations) című fenti szakaszban.

## <a name="next-steps"></a>További lépések
* [Lekérdezések naplózását](../log-query/log-query-overview.md) megtudhatja, hogyan elemezhet SQL állapotának ellenőrzése a részletes adatok és javaslatok.
