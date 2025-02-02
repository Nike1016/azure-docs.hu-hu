---
title: Több-bérlős alkalmazás Azure SQL Database példa – Wingtip SaaS | Microsoft Docs
description: Ismerkedjen meg a Azure SQL Databaset használó minta több-bérlős alkalmazással, a Wingtip SaaS-példa használatával
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/24/2018
ms.openlocfilehash: 16f4bb946af4720a327a8755c6bf9187f3b71ba6
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68570347"
---
# <a name="introduction-to-a-multitenant-saas-app-that-uses-the-database-per-tenant-pattern-with-sql-database"></a>Bevezetés egy több-bérlős SaaS-alkalmazásba, amely az adatbázis-/bérlői mintát használja SQL Database

A Wingtip SaaS-alkalmazás egy példa több-bérlős alkalmazás. Az alkalmazás az adatbázis/bérlői SaaS-alkalmazás mintáját használja több bérlő kiszolgálására. Az alkalmazás a Azure SQL Database funkcióit mutatja be, amelyek lehetővé teszik az SaaS-forgatókönyvek használatát több SaaS-kialakítás és-felügyeleti minta használatával. A gyors üzembe helyezéshez a Wingtip SaaS-alkalmazás kevesebb, mint öt percet vesz igénybe.

A [WingtipTicketsSaaS-DbPerTenant GitHub-](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) tárházban az alkalmazás forráskódja és a felügyeleti parancsfájlok érhetők el. Mielőtt elkezdené, tekintse meg a Wingtip-jegyek kezelési parancsfájljainak letöltéséhez és feloldásához szükséges lépéseket ismertető [általános útmutatást](saas-tenancy-wingtip-app-guidance-tips.md) .

## <a name="application-architecture"></a>Alkalmazásarchitektúra

A Wingtip SaaS-alkalmazás az adatbázis/bérlői modellt használja. Rugalmas SQL-készleteket használ a hatékonyság maximalizálása érdekében. Ha bérlőket szeretne kiépíteni az adatközpontba, a rendszer katalógus-adatbázist használ. A Core Wingtip SaaS-alkalmazás három minta Bérlővel rendelkező készletet és a katalógus-adatbázist használja. A katalógus és a bérlői kiszolgálók a DNS-aliasokkal lettek kiépítve. Ezek az aliasok a Wingtip alkalmazás által használt aktív erőforrásokra mutató hivatkozások fenntartására szolgálnak. Ezek az aliasok úgy frissülnek, hogy a vész-helyreállítási oktatóanyagokban a helyreállítási erőforrásokra mutassanak. A Wingtip SaaS-oktatóanyagok nagy része a kezdeti üzembe helyezéshez a bővítmények eredményeit eredményezi. A bővítmények, például az analitikai adatbázisok és az adatbázison belüli séma-kezelés bevezetése.


![Wingtip SaaS-architektúra](media/saas-dbpertenant-wingtip-app-overview/app-architecture.png)


Az oktatóanyagok és az alkalmazással való munka során az SaaS-mintázatokra koncentrálhat, amelyek az adatcsomaggal kapcsolatosak. Más szóval koncentráljon az adatcsomagra, és ne elemezze magát az alkalmazást. Ezen SaaS-minták megvalósításának megértése kulcsfontosságú ezeknek a mintázatoknak az alkalmazásokban való megvalósításához. Vegye figyelembe az adott üzleti követelmények szükséges módosításait is.

## <a name="sql-database-wingtip-saas-tutorials"></a>SQL Database Wingtip SaaS-oktatóanyagok

Az alkalmazás üzembe helyezése után vizsgálja meg a következő, a kezdeti üzembe helyezésre épülő oktatóanyagokat. Ezek az oktatóanyagok olyan általános SaaS-mintákat mutatnak, amelyek kihasználják SQL Database, Azure SQL Data Warehouse és más Azure-szolgáltatások beépített funkcióit. Az oktatóanyagok tartalmazzák a PowerShell-szkripteket, részletes magyarázatokkal. A magyarázattal megtalálhatók az alkalmazások azonos SaaS-felügyeleti mintáinak megismerése és megvalósítása.


| Oktatóanyag | Leírás |
|:--|:--|
| [Útmutató és tippek az SQL Database több-bérlős SaaS-alkalmazáshoz – példa](saas-tenancy-wingtip-app-guidance-tips.md) | Töltse le és futtassa a PowerShell-parancsfájlokat az alkalmazás részeinek előkészítéséhez. |
|[A Wingtip SaaS-alkalmazás üzembe helyezése és megismerése](saas-dbpertenant-get-started-deploy.md)|  Üzembe helyezheti és felfedezheti az Wingtip SaaS-alkalmazást az Azure-előfizetésével. |
|[Bérlők kiépítése és katalogizálása](saas-dbpertenant-provision-and-catalog.md)| Megtudhatja, hogyan kapcsolódik az alkalmazás a bérlőhöz egy katalógus-adatbázis használatával, valamint arról, hogy a katalógus hogyan képezi le a bérlőket az adataiknak. |
|[Teljesítmény figyelése és kezelése](saas-dbpertenant-performance-monitoring.md)| Megtudhatja, hogyan használhatja a SQL Database figyelési funkcióit, és riasztásokat állíthat be, ha túllépi a teljesítménnyel kapcsolatos küszöbértékeket. |
|[Figyelő Azure Monitor naplókkal](saas-dbpertenant-log-analytics.md) | Megtudhatja, hogyan használhat [Azure monitor naplókat](../log-analytics/log-analytics-overview.md) nagy mennyiségű erőforrás több készleten történő figyeléséhez. |
|[Egyetlen bérlő visszaállítása](saas-dbpertenant-restore-single-tenant.md)| Megtudhatja, hogyan állíthatja vissza a bérlői adatbázist egy korábbi időpontra. Azt is megtudhatja, hogyan lehet visszaállítani egy párhuzamos adatbázisra, amely a meglévő bérlői adatbázist online állapotba hagyja. |
|[Bérlői adatbázis sémájának kezelése](saas-tenancy-schema-management.md)| Megtudhatja, hogyan frissítheti a sémát, és hogyan frissítheti a hivatkozási információkat az összes bérlői adatbázisban. |
|[Több-bérlős elosztott lekérdezések futtatása](saas-tenancy-cross-tenant-reporting.md) | Hozzon létre egy ad hoc elemzési adatbázist, és futtasson valós idejű elosztott lekérdezéseket az összes bérlő között.  |
|[Elemzés futtatása a kinyert bérlői adatbázison](saas-tenancy-tenant-analytics.md) | A bérlői adatok kinyerése egy elemzési adatbázisba vagy adattárházba offline elemzési lekérdezésekhez. |


## <a name="next-steps"></a>További lépések

- [Általános útmutatás és tippek a Wingtip tickets SaaS-alkalmazás üzembe helyezéséhez és használatához – példa](saas-tenancy-wingtip-app-guidance-tips.md)
- [A Wingtip SaaS-alkalmazás üzembe helyezése](saas-dbpertenant-get-started-deploy.md)
