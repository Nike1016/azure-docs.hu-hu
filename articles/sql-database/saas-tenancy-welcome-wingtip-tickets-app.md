---
title: Üdvözli a wingtips app-Azure SQL Database | Microsoft Docs
description: Ismerje meg az adatbázis-bérleti modelleket, valamint a wingtips SaaS-alkalmazással kapcsolatos tudnivalókat a felhőalapú környezet Azure SQL Database.
keywords: sql database-oktatóanyag
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: billgib
ms.date: 01/25/2019
ms.openlocfilehash: ca534d3ffcfd1d4caeb9cf755934a846fafae26e
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68570107"
---
# <a name="the-wingtip-tickets-saas-application"></a>A Wingtip tickets SaaS-alkalmazás

Ugyanezek a *Wingtip jegyek* a SaaS-alkalmazások mindhárom mintában implementálva vannak. Az alkalmazás egy egyszerű esemény listázása és az SaaS-alkalmazások beszerzése kis helyszínekre – színházak, klubok stb. Minden helyszín az alkalmazás bérlője, és saját adatai vannak: a helyszín részletei, az események listája, az ügyfelek, a jegyek rendelése stb.  Az alkalmazás a felügyeleti parancsfájlokkal és oktatóanyagokkal együtt egy végpontok közötti SaaS-forgatókönyvet mutat be. Ez magában foglalja a bérlők kiépítése, a teljesítmény, a sémakezelő és a több-bérlős jelentéskészítés és az elemzések felügyeletét és kezelését.

## <a name="three-saas-application-and-tenancy-patterns"></a>Három SaaS-alkalmazás és bérleti minta

Az alkalmazás három verziója érhető el; mindegyik megvizsgál egy másik adatbázis-bérleti mintát Azure SQL Databaseon.  Az első egy önálló alkalmazást használ bérlőn a saját adatbázisával. A második egy több-bérlős alkalmazást használ bérlői adatbázissal. A harmadik minta több-bérlős alkalmazást használ a több-bérlős adatbázisok használatával.

![Három bérleti minta][image-three-tenancy-patterns]

 Mindegyik minta tartalmazza az alkalmazás kódját, valamint a felügyeleti parancsfájlokat és oktatóanyagokat, amelyek számos tervezési és felügyeleti mintát vizsgálnak meg.  Mindegyik minta kevesebb, mint öt perc alatt üzembe helyezhető.  Mind a három telepíthető párhuzamosan, így összehasonlíthatja a tervezés és a felügyelet különbségeit.

## <a name="standalone-application-per-tenant-pattern"></a>Önálló alkalmazás/bérlői minta

Az önálló alkalmazás/bérlői minta egyetlen bérlői alkalmazást használ az egyes bérlők adatbázisával. Az egyes bérlők alkalmazásait, beleértve az adatbázisát is, külön Azure-erőforráscsoporthoz helyezik üzembe. Az erőforráscsoport a szolgáltató előfizetésében vagy a bérlő előfizetésében is üzembe helyezhető, és a szolgáltató a bérlő nevében felügyelhető. Az önálló alkalmazás/bérlői minta biztosítja a bérlők legnagyobb elkülönítését, de általában a legdrágább, mivel nincs lehetőség több bérlő közötti erőforrások megosztására.  Ez a minta jól használható olyan alkalmazásokhoz, amelyek összetettebbek lehetnek, és amelyek kisebb számú bérlőhöz vannak telepítve.  Önálló telepítések esetén az alkalmazás minden bérlő számára könnyebben testreszabható, mint más mintákban.  

Tekintse meg [][docs-tutorials-for-wingtip-sa] az oktatóanyagokat és a kódot a GitHub [. ../Microsoft/WingtipTicketsSaaS-StandaloneApp][github-code-for-wingtip-sa].

## <a name="database-per-tenant-pattern"></a>Adatbázis/bérlői minta

Az adatbázis/bérlői minta a bérlői elkülönítéssel érintett szolgáltatók esetében érvényes, és olyan központi szolgáltatást szeretne futtatni, amely lehetővé teszi a megosztott erőforrások költséghatékony használatát. Létrejön egy adatbázis az egyes helyszínekhez vagy bérlőhöz, és az összes adatbázis központilag felügyelt. Az adatbázisok rugalmas készletekbe helyezhetők, így költséghatékony és könnyen kezelhető a teljesítmény, ami kihasználja a bérlők előre nem látható számítási feladatait. A katalógus-adatbázis a bérlők és az adatbázisaik közötti leképezést tárolja. Ezt a leképezést a [Elastic Database ügyféloldali függvénytárának](sql-database-elastic-database-client-library.md)a szegmensek közötti leképezés-kezelési funkcióival felügyeli, amely hatékony ügyfélkapcsolat-kezelést biztosít az alkalmazás számára.

Tekintse meg [][docs-tutorials-for-wingtip-dpt] az oktatóanyagokat és a kódot a GitHub [. ../Microsoft/WingtipTicketsSaaS-DbPerTenant][github-code-for-wingtip-dpt].

## <a name="sharded-multi-tenant-database-pattern"></a>Több-bérlős, több-bérlős adatbázis mintája

A több-bérlős adatbázisok a bérlők alacsonyabb díjait kereső szolgáltatók esetében érvényesek, és a bérlők elkülönítésének csökkenése is megfelelő. Ez a minta lehetővé teszi a nagy számú bérlő egyetlen adatbázisba való becsomagolását, a bérlői költségeket. Szinte végtelen skála lehetséges a bérlők több adatbázisba való szétosztásával. A katalógus-adatbázis bérlőket rendel az adatbázisokhoz.  

Ez a minta lehetővé teszi a *hibrid* modell használatát is, amelyben optimalizálhatja a költségeket több Bérlővel egy adatbázisban, vagy optimalizálhatja az elkülönítést egyetlen Bérlővel a saját adatbázisában. A választást bérlői bérlői alapon lehet elvégezni, ha a bérlő kiépítve vagy később van, és nincs hatással az alkalmazásra.  Ez a modell hatékonyan használható, ha a bérlők csoportjait különbözőképpen kell kezelni. Az alacsony díjas bérlők például megosztott adatbázisokhoz rendelhetők, míg a prémium szintű bérlők a saját adatbázisaihoz rendelhetők. 

Tekintse meg [][docs-tutorials-for-wingtip-mt] az oktatóanyagokat és a kódot a GitHub [. ../Microsoft/WingtipTicketsSaaS-MultiTenantDb][github-code-for-wingtip-mt].

## <a name="next-steps"></a>További lépések

#### <a name="conceptual-descriptions"></a>Fogalmi leírások

- Az alkalmazás bérleti mintáinak részletesebb ismertetése a [több-bérlős SaaS-adatbázisok bérleti mintái][saas-tenancy-app-design-patterns-md] című részében olvasható.

#### <a name="tutorials-and-code"></a>Oktatóanyagok és kód

- Önálló alkalmazás/bérlő:
    - [Oktatóanyagok önálló alkalmazáshoz][docs-tutorials-for-wingtip-sa].
    - [Kód önálló alkalmazáshoz a githubon][github-code-for-wingtip-sa].

- Adatbázis/bérlő:
    - [Oktatóanyagok a bérlői adatbázisokhoz][docs-tutorials-for-wingtip-dpt].
    - [Kód a bérlői adatbázishoz a githubon][github-code-for-wingtip-dpt].

- Több-bérlős szegmens:
    - [Oktatóanyagok a több-bérlős szegmensekhez][docs-tutorials-for-wingtip-mt].
    - [A több-bérlős szilánkok kódja a githubon][github-code-for-wingtip-mt].



<!-- Image references. -->

[image-three-tenancy-patterns]: media/saas-tenancy-welcome-wingtip-tickets-app/three-tenancy-patterns.png "Három bérleti minta."

<!-- Docs.ms.com references. -->

[saas-tenancy-app-design-patterns-md]: saas-tenancy-app-design-patterns.md

<!-- WWWeb http references. -->

[docs-tutorials-for-wingtip-sa]: https://aka.ms/wingtipticketssaas-sa
[github-code-for-wingtip-sa]: https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp

[docs-tutorials-for-wingtip-dpt]: https://aka.ms/wingtipticketssaas-dpt
[github-code-for-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant

[docs-tutorials-for-wingtip-mt]: https://aka.ms/wingtipticketssaas-mt
[github-code-for-wingtip-mt]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb

