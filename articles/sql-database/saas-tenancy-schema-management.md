---
title: Azure SQL Database séma kezelése egyetlen bérlős alkalmazásban | Microsoft Docs
description: Több bérlő sémájának kezelése egyetlen bérlős alkalmazásban, amely Azure SQL Databaset használ
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: billgib
ms.date: 09/19/2018
ms.openlocfilehash: 7b238044fd3795ae2f49c2fa21367e6499a65672
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68570120"
---
# <a name="manage-schema-in-a-saas-application-using-the-database-per-tenant-pattern-with-azure-sql-database"></a>A sémák kezelése SaaS-alkalmazásokban az adatbázis-bérlői minta használatával Azure SQL Database
 
Az adatbázis-alkalmazások fejlődése során az adatbázis-séma vagy a hivatkozási adatváltozások elkerülhetetlenül szükségesek.  Az adatbázis-karbantartási feladatok rendszeres időközönként is szükségesek. Az adatbázist a bérlői mintákon használó alkalmazások kezeléséhez szükséges, hogy ezeket a módosításokat vagy karbantartási feladatokat a bérlői adatbázisok flottáján belül alkalmazza.

Ez az oktatóanyag két forgatókönyvet mutat be – a hivatkozási adatok frissítéseinek üzembe helyezése az összes bérlő számára, valamint a hivatkozási adatokkal rendelkező tábla indexének újraépítése. A [rugalmas feladatok](elastic-jobs-overview.md) funkció használatával végrehajthatja ezeket a műveleteket az összes bérlői adatbázison és az új bérlői adatbázisok létrehozásához használt sablon-adatbázison.

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> 
> * Feladatok ügynökének létrehozása
> * A T-SQL-feladatok futtatásának oka az összes bérlői adatbázisban
> * Az összes bérlői adatbázisban lévő hivatkozási érték frissítése
> * Index létrehozása a táblához az összes bérlői adatbázisban


Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:

* A Wingtip jegyek SaaS-adatbázisa egy bérlői alkalmazáson van üzembe helyezve. Ha kevesebb, mint öt perc alatt kíván üzembe helyezni, tekintse meg [a Wingtip tickets SaaS-adatbázis üzembe helyezése és megismerése bérlői alkalmazásokban](saas-dbpertenant-get-started-deploy.md)
* Az Azure PowerShell telepítve van. A részletekért lásd: [Ismerkedés az Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* Telepítve van az SQL Server Management Studio (SSMS) legújabb verziója. [Az SSMS letöltése és telepítése](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)

> [!NOTE]
> Ez az oktatóanyag egy korlátozott előzetes verzióban (rugalmas adatbázis-feladatok) található SQL Database szolgáltatás funkcióit használja. Ha ezt az oktatóanyagot szeretné elvégezni, adja meg az előfizetés- SaaSFeedback@microsoft.com azonosítóját a subject = rugalmas feladatok előzetes verziójával. Miután megkapta a megerősítést az előfizetésének engedélyezéséről, [töltse le és telepítse a legújabb kiadás előtti feladatok parancsmagjait](https://github.com/jaredmoo/azure-powershell/releases). Ez az előzetes verzió korlátozott, ezért SaaSFeedback@microsoft.com vegye fel a kapcsolatot a kapcsolódó kérdésekkel vagy támogatással.

## <a name="introduction-to-saas-schema-management-patterns"></a>Az SaaS-séma felügyeleti mintáinak bemutatása

Az adatbázis/bérlői minta elkülöníti a bérlői adatmennyiséget, de növeli a felügyelni és karbantartani kívánt adatbázisok számát. A [rugalmas feladatok](elastic-jobs-overview.md) megkönnyítik az SQL-adatbázisok felügyeletét és kezelését. A feladatok lehetővé teszik, hogy biztonságosan és megbízhatóan futtasson feladatokat (T-SQL-szkripteket) az adatbázisok egy csoportjára. A feladatok üzembe helyezhetik a sémát és a gyakori hivatkozási adatváltozásokat az alkalmazás összes bérlői adatbázisában. A rugalmas feladatok az új bérlők létrehozásához használt *sablon* -adatbázis fenntartására is használhatók, így biztosítva, hogy mindig a legújabb séma-és hivatkozási adatok legyenek.

![képernyő](media/saas-tenancy-schema-management/schema-management-dpt.png)


## <a name="elastic-jobs-limited-preview"></a>Az Elastic Jobs korlátozott előzetes verziója

A rugalmas feladatok új verziója már a Azure SQL Database integrált szolgáltatása. Ez Elastic Jobs-nak ez az új verziója jelenleg korlátozott előzetes verzió. Ez a korlátozott előzetes verzió jelenleg a PowerShell használatával teszi lehetővé a feladatok ügynökének létrehozását és a T-SQL-T a feladatok létrehozásához és kezeléséhez.

> [!NOTE]
> Ez az oktatóanyag egy korlátozott előzetes verzióban (rugalmas adatbázis-feladatok) található SQL Database szolgáltatás funkcióit használja. Ha ezt az oktatóanyagot szeretné elvégezni, adja meg az előfizetés- SaaSFeedback@microsoft.com azonosítóját a subject = rugalmas feladatok előzetes verziójával. Miután megkapta a megerősítést az előfizetésének engedélyezéséről, [töltse le és telepítse a legújabb kiadás előtti feladatok parancsmagjait](https://github.com/jaredmoo/azure-powershell/releases). Ez az előzetes verzió korlátozott, ezért SaaSFeedback@microsoft.com vegye fel a kapcsolatot a kapcsolódó kérdésekkel vagy támogatással.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>A Wingtip tickets SaaS-adatbázis beszerzése bérlői alkalmazás parancsfájljai alapján

Az alkalmazás forráskódja és a felügyeleti szkriptek a [WingtipTicketsSaaS-DbPerTenant GitHub-](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) tárházban érhetők el. Tekintse meg az [általános útmutatót](saas-tenancy-wingtip-app-guidance-tips.md) a Wingtip tickets SaaS-parancsfájlok letöltésének és feloldásának lépéseihez.

## <a name="create-a-job-agent-database-and-new-job-agent"></a>A Job Agent-adatbázis és az új feladatkártya létrehozása

Ehhez az oktatóanyaghoz a PowerShell használatával kell létrehoznia a feladatokhoz tartozó ügynököt és a hozzá tartozó feladatkártya-adatbázist. A feladatok ügynökének adatbázisában a feladatütemezés, a feladatok állapota és az előzmények szerepelnek. A feladat-ügynök és az adatbázis létrehozása után azonnal létrehozhatja és figyelheti a feladatokat.

1. **A POWERSHELL ISE-ben**nyissa meg a... Learning-\\modulok séma\\-felügyeleti*demo-SchemaManagement. ps1.* \\
1. A szkriptek futtatásához nyomja le az **F5** billentyűt.

A *demo-SchemaManagement. ps1* parancsfájl meghívja a *Deploy-SchemaManagement. ps1* parancsfájlt egy *osagent* nevű SQL-adatbázis létrehozásához a Catalog kiszolgálón. Ezután létrehozza a feladatot, amely az adatbázis paraméterként való használatával jön létre.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Feladat létrehozása új referenciaadatok bevezetéséhez az összes bérlőn

A Wingtip tickets alkalmazásban a bérlői adatbázisok a támogatott helyszíneket tartalmazzák. Minden helyszín egy adott típusú helyszín, amely meghatározza a üzemeltethető események típusát, és meghatározza az alkalmazásban használt háttérképet. Ahhoz, hogy az alkalmazás támogassa az új típusú eseményeket, ezeket a hivatkozási adattípusokat frissíteni kell, és új helyszíneket kell hozzáadni.  Ebben a gyakorlatban egy frissítést helyez üzembe az összes bérlői adatbázison két további hely típusának hozzáadásához: *Motorkerékpár-verseny* és az *úszó klub*.

Először tekintse át az egyes bérlői adatbázisokban található helyszín típusait. Kapcsolódjon SQL Server Management Studio (SSMS) egyik bérlői adatbázisához, és vizsgálja meg a VenueTypes táblát.  Ezt a táblázatot a Azure Portal lekérdezés-szerkesztőjében is lekérdezheti, amely az adatbázis lapról érhető el. 

1. Nyissa meg a SSMS, és kapcsolódjon a bérlői kiszolgálóhoz: *tenants1-DPT-&lt;&gt;user. database.Windows.net*
1. Annak megerősítéséhez, hogy a *motorkerékpár-verseny* és az *úszás Club* jelenleg **nem** szerepel, keresse meg a _contosoconcerthall_ -adatbázist a *tenants1&gt; -DPT-&lt;User* kiszolgálón, és kérdezze le a  *VenueTypes* tábla.

Most hozzon létre egy feladatot, amely frissíti a *VenueTypes* táblát az összes bérlői adatbázisban az új helyszín típusának hozzáadásához.

Új feladat létrehozásához a feladat ügynökének létrehozásakor a _jobagent_ -adatbázisban létrehozott feladatok rendszertárolt eljárásait kell használnia.

1. A SSMS-ben kapcsolódjon a Catalog kiszolgálóhoz: *Catalog-DPT&lt;-&gt;user. database.Windows.net* Server 
1. A SSMS-ben nyissa meg a fájlt... Tanulási modulok\\sémájának\\kezelése DeployReferenceData. SQL \\
1. Módosítsa az utasítást: SET @wtpUser = &lt;User&gt; és a Wingtip tickets SaaS-adatbázis üzembe helyezése során használt felhasználói érték helyettesítése a bérlői alkalmazásokban
1. Győződjön meg arról, hogy csatlakozik a _jobagent_ -adatbázishoz, és nyomja le az **F5** billentyűt a szkript futtatásához.

Figyelje meg a következő elemeket a *DeployReferenceData. SQL* parancsfájlban:
* **az\_SPAdd\_Target\_Group** létrehozza a célcsoport nevét DemoServerGroup.
* **az\_SPAdd\_Target\_Grouptag\_** a célként megadott adatbázisok meghatározására szolgál.  Először adja hozzá a _tenants1-&lt;DPT&gt; -User_ kiszolgálót.  A kiszolgáló célként való hozzáadásával az adott kiszolgálón lévő adatbázisok a feladatok végrehajtásának időpontjában fognak szerepelni a feladatokban. Ezután a _basetenantdb_ -adatbázis és a *adhocreporting* -adatbázis (amelyet egy későbbi oktatóanyagban használ) hozzá lesz adva célként.
* **az\_SPAdd\_Job** létrehoz egy _hivatkozási adatok központi telepítés_nevű feladatot.
* **az\_SPAdd\_jobstep** létrehozza a T-SQL-parancs szövegét tartalmazó feladatot, amely frissíti a VenueTypes.
* A szkript fennmaradó nézetei megjelenítik, hogy léteznek-e az objektumok, és figyelik a feladat-végrehajtást. Ezekkel a lekérdezésekkel áttekintheti az állapot értékét az **életciklus** oszlopban annak megállapításához, hogy a feladatok befejeződtek-e az összes célként megadott adatbázison.

A parancsfájl befejezését követően ellenőrizheti, hogy frissültek-e a hivatkozási adathalmazok.  A SSMS-ben keresse meg a *contosoconcerthall* adatbázist a *tenants1-DPT-&lt;User&gt;*  kiszolgálón, és kérdezze le a *VenueTypes* táblát.  Győződjön meg arról, hogy a *motorkerékpár-verseny* és az *úszó klub* már jelen **van** .


## <a name="create-a-job-to-manage-the-reference-table-index"></a>Feladat létrehozása a referenciatábla indexének kezeléséhez

Ez a gyakorlat egy feladatot használ az index újraépítéséhez a hivatkozási tábla elsődleges kulcsán.  Ez egy tipikus adatbázis-karbantartási művelet, amely nagy mennyiségű információ betöltése után is elvégezhető.

Hozzon létre egy feladatot ugyanannak a feladatnak a „system” által tárolt eljárásait használva.

1. Nyissa meg a SSMS, és kapcsolódjon a _Catalog&gt;-DPT-&lt;user. database.Windows.net_ kiszolgálóhoz
1. Nyissa meg a fájlt _... Tanulásimodulok\\sémájánakkezelése\\Sémakezelés.SQL \\_
1. Kattintson a jobb gombbal, válassza a kapcsolat lehetőséget, és kapcsolódjon a _Catalog&gt;-DPT-&lt;user. database.Windows.net_ kiszolgálóhoz, ha még nincs csatlakoztatva
1. Győződjön meg arról, hogy csatlakozik a _jobagent_ -adatbázishoz, és nyomja le az **F5** billentyűt a szkript futtatásához.

Figyelje meg a következő elemeket a _Sémakezelés. SQL_ parancsfájlban:
* **az\_SPAdd\_Job** létrehoz egy "online reindex PK\_\_VenueTyp\_\_265E44FD7FD4C885" nevű új feladatot.
* **az\_SPAdd\_jobstep** létrehozza a T-SQL-parancs szövegét tartalmazó feladatot, amely frissíti az indexet
* A parancsfájl-figyelő feladatok végrehajtásának hátralévő nézetei. Ezekkel a lekérdezésekkel áttekintheti az állapot értékét az **életciklus** oszlopban annak megállapításához, hogy a feladatok sikeresen befejeződtek-e az összes célcsoport-tagon.



## <a name="next-steps"></a>További lépések

Ennek az oktatóanyagnak a segítségével megtanulta a következőket:

> [!div class="checklist"]
> 
> * Feladat-ügynök létrehozása a T-SQL-feladatok több adatbázis között való futtatásához
> * Az összes bérlői adatbázisban lévő hivatkozási érték frissítése
> * Index létrehozása a táblához az összes bérlői adatbázisban

Ezután próbálja ki az [ad hoc jelentéskészítési oktatóanyagot](saas-tenancy-cross-tenant-reporting.md) , és vizsgálja meg a bérlői adatbázisok közötti elosztott lekérdezések futtatását.


## <a name="additional-resources"></a>További források

* [További oktatóanyagok, amelyek az Wingtip tickets SaaS-adatbázisra épülnek a bérlői alkalmazások üzembe helyezése után](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Kiterjesztett felhőalapú adatbázisok kezelése](elastic-jobs-overview.md)