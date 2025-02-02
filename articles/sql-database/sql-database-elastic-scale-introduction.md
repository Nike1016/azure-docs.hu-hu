---
title: Horizontális felskálázás Azure SQL Databasesal | Microsoft Docs
description: A szolgáltatott szoftver (SaaS) fejlesztők könnyedén hozhatnak létre rugalmas, méretezhető adatbázisokat a felhőben ezen eszközök használatával
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: e5ae56b2050243831f10863bbb4184a9e89f5911
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568401"
---
# <a name="scaling-out-with-azure-sql-database"></a>Scaling out with Azure SQL Database (Horizontális felskálázás az Azure SQL Database segítségével)
Az Azure SQL Database-adatbázisok egyszerűen felméretezhetők a **Elastic Database** eszközök használatával. Ezek az eszközök és szolgáltatások lehetővé teszik a **Azure SQL Database** adatbázis-erőforrásainak használatát a tranzakciós számítási feladatokhoz és különösen a szolgáltatott szoftverekhez (SaaS) készült megoldások létrehozásához. Elastic Database szolgáltatások a következőkből állnak:

* [Elastic Database ügyféloldali kódtár](sql-database-elastic-database-client-library.md): Az ügyféloldali kódtár egy olyan szolgáltatás, amely lehetővé teszi a szilánkokra osztott adatbázisok létrehozását és karbantartását.  Lásd: [Elastic Database-eszközök első lépései](sql-database-elastic-scale-get-started.md).
* [Elastic Database felosztási-egyesítési eszköz](sql-database-elastic-scale-overview-split-and-merge.md): áthelyezi az adatátvitelt a felosztott adatbázisok között. Ez az eszköz akkor hasznos, ha egy több-bérlős adatbázisból egy egybérlős adatbázisba helyezi át az adatáthelyezést (vagy fordítva). Lásd: [rugalmas adatbázis felosztása – egyesítési eszköz oktatóanyaga](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
* [Rugalmas adatbázis-feladatok](elastic-jobs-overview.md): Feladatok használata nagy számú Azure SQL-adatbázis kezeléséhez. Egyszerűen végezhet olyan adminisztratív műveleteket, mint a séma módosítása, a hitelesítő adatok kezelése, a hivatkozási adatok frissítései, a teljesítményadatok gyűjtése vagy a bérlő (ügyfél) telemetria-gyűjtemény a feladatok használatával.
* [Elastic Database lekérdezés](sql-database-elastic-query-overview.md) (előzetes verzió): Lehetővé teszi, hogy olyan Transact-SQL-lekérdezést futtasson, amely több adatbázisra is kiterjed. Ez lehetővé teszi, hogy a rendszer olyan jelentéskészítő eszközöket létesítsen, mint például az Excel, a Power BI, a tabló stb.).
* [Rugalmas tranzakciók](sql-database-elastic-transactions-overview.md): Ez a funkció lehetővé teszi, hogy olyan tranzakciókat futtasson, amelyek a Azure SQL Database több adatbázisára is kiterjednek. A rugalmas adatbázis-tranzakciók az ADO .NET-et használó .NET-alkalmazások számára érhetők el, és az ismerős programozási felülettel integrálhatók a [System. Transaction osztályok](https://msdn.microsoft.com/library/system.transactions.aspx)használatával.

Az alábbi ábra egy olyan architektúrát mutat be, amely tartalmazza az adatbázisok gyűjteményéhez kapcsolódó **Elastic Database funkciókat** .

Ebben a grafikában az adatbázis színei a sémákat jelölik. Azonos színnel rendelkező adatbázisok ugyanazt a sémát használják.

1. Az Azure **SQL Database-adatbázisok** horizontális Felskálázási architektúra használatával futnak az Azure-ban.
2. A **Elastic Database ügyféloldali kódtár** használatával kezelhetők a szegmensek.
3. Az adatbázisok egy részhalmaza **rugalmas készletbe**kerül. (Lásd: [Mi az a készlet?](sql-database-elastic-pool.md)).
4. Az **Elastic Database feladatok** ütemezett vagy ad hoc T-SQL-parancsfájlokat futtatnak az összes adatbázison.
5. A **felosztott egyesítés eszköz** az adatok egyik szegmensből egy másikba való áthelyezésére szolgál.
6. A **Elastic Database lekérdezés** lehetővé teszi egy olyan lekérdezés írását, amely az összes adatbázisra kiterjed a szegmens készletében.
7. A **rugalmas tranzakciók** lehetővé teszik, hogy több adatbázisra kiterjedő tranzakciókat futtasson. 

![Rugalmasadatbázis-eszközök][1]

## <a name="why-use-the-tools"></a>Miért érdemes használni az eszközöket?
A rugalmasság és a méretezés a felhőalapú alkalmazásokhoz egyszerű a virtuális gépek és a blob Storage számára – egyszerűen hozzáadhat vagy kivonja az egységeket, vagy növelheti a teljesítményt. Azonban továbbra is kihívást jelent az állapot-nyilvántartó adatfeldolgozáshoz a kapcsolódó adatbázisokban. A következő helyzetekben felmerülő kihívások:

* A számítási feladathoz tartozó kapcsolati adatbázis részének növekvő és csökkenő kapacitása.
* Olyan elérési pontok kezelése, amelyek felmerülhetnek az adathalmazok (például a foglalt végfelhasználók) számára.

A fentiekhez hasonló forgatókönyvek a nagy léptékű adatbázis-kiszolgálók befektetésével foglalkoznak, amelyek támogatják az alkalmazást. Ez a beállítás azonban korlátozott a felhőben, ahol az összes feldolgozás az előre definiált termék hardverén történik. Ehelyett az adatelosztást és a feldolgozást számos azonos módon strukturált adatbázison (a "horizontális felskálázás" néven ismert kibővített mintázat) a hagyományos horizontális Felskálázási megközelítések helyett a Cost és a rugalmasság szempontjából is.

## <a name="horizontal-and-vertical-scaling"></a>Horizontális és vertikális skálázás
Az alábbi ábra a skálázás horizontális és vertikális dimenzióit mutatja be, amelyek a rugalmas adatbázisok méretezésének alapvető módjai.

![Vízszintes és vertikális felskálázás][2]

A horizontális skálázás az adatbázisok hozzáadását vagy eltávolítását jelenti a kapacitás vagy az általános teljesítmény módosításához, más néven "horizontális felskálázás". A horizontális skálázás megvalósításának egyik gyakori módja, hogy az adategységek az azonos strukturált adatbázisok gyűjteménye között particionálva legyenek.  

A vertikális skálázás egy különálló adatbázis számítási méretének növelésére vagy csökkentésére utal, más néven "felskálázás".

A legtöbb felhőalapú adatbázis-alkalmazás a két stratégia kombinációját használja. Előfordulhat például, hogy egy szoftver, mint egy szolgáltatásalkalmazás horizontális skálázást használ az új végfelhasználók és vertikális skálázás kiépítéséhez, hogy az egyes végfelhasználói adatbázisok a munkaterhelésnek megfelelően növelje vagy csökkentik az erőforrásokat.

* A horizontális skálázás a [Elastic Database ügyféloldali kódtár](sql-database-elastic-database-client-library.md)használatával felügyelhető.
* A vertikális skálázás Azure PowerShell parancsmagok segítségével módosítható a szolgáltatási szint módosításához, vagy egy rugalmas készletbe helyezi az adatbázisokat.

## <a name="sharding"></a>Sharding
A horizontális skálázás olyan technika, amellyel nagy mennyiségű, egymástól független adatbázisba tartozó strukturált adategység terjeszthető ki. Ez különösen népszerű a felhőalapú fejlesztők számára a végfelhasználók és a vállalkozások számára szolgáltatott szoftver-(SAAS-) ajánlatok létrehozásához. Ezeket a végfelhasználókat gyakran "bérlőknek" nevezzük. A horizontális skálázás tetszőleges számú okból szükséges lehet:  

* A teljes adatmennyiség túl nagy ahhoz, hogy az egyes adatbázisok korlátain belül elfér
* A teljes munkaterhelés tranzakciós sebessége meghaladja az egyes adatbázisok képességeit
* A bérlők fizikai elkülönítést igényelhetnek egymástól, ezért külön adatbázisokra van szükség az egyes bérlők számára
* Előfordulhat, hogy az adatbázisok különböző részeinek a megfelelőség, a teljesítmény vagy a geopolitikai okokból különböző földrajzi területeken kell lenniük.

Más helyzetekben, például az elosztott eszközök adatainak betöltéséhez a horizontális felskálázással lehet kitölteni egy időben szervezett adatbázisok készletét. Egy külön adatbázis például minden nap vagy hét számára kiválasztható. Ebben az esetben a horizontális Felskálázási kulcs lehet egy egész szám, amely a dátumot jelöli (a szétválasztott táblák összes sorában szerepel), és a dátumtartomány adatait lekérő lekérdezéseket az alkalmazásnak kell átirányítani az adott tartományra kiterjedő adatbázisok részhalmazára.

A horizontális skálázás akkor működik a legjobban, ha egy alkalmazás minden tranzakciója egy horizontális Felskálázási kulcs egyetlen értékére korlátozódik. Ez biztosítja, hogy az összes tranzakció egy adott adatbázishoz legyen helyi.

## <a name="multi-tenant-and-single-tenant"></a>Több-bérlős és egybérlős
Egyes alkalmazások a legegyszerűbb megközelítést használják az egyes bérlők külön adatbázisának létrehozásához. Ez a megközelítés az **egyetlen bérlői** horizontális Felskálázási minta, amely elkülönítést, biztonsági mentési/visszaállítási képességet és erőforrás-méretezést biztosít a bérlő részletessége érdekében. Az egybérlős horizontális felskálázással minden adatbázis egy adott bérlői azonosító értékkel (vagy az ügyfél kulcsának értékével) van társítva, de a kulcsnak nem kell mindig az adataiban lennie. Az alkalmazás feladata, hogy az egyes kérelmeket a megfelelő adatbázishoz irányítsa, és az ügyféloldali kódtár egyszerűsítse ezt.

![Egyetlen bérlő és több-bérlő][4]

Más forgatókönyvek esetén több bérlőt is becsomagolhat adatbázisokba, és nem kell külön adatbázisba elkülöníteni őket. Ez a minta egy tipikus, **több-bérlős** horizontális Felskálázási minta, és az is előfordulhat, hogy egy alkalmazás nagy számú kis bérlőt kezel. A több-bérlős szegmensekben az adatbázistábla sorai úgy vannak kialakítva, hogy a bérlői azonosítót vagy a horizontális kulcsot azonosító kulcsot használják. Ismét az alkalmazási szinten a bérlői kérésnek a megfelelő adatbázishoz való irányítása felelős, és ezt a rugalmas adatbázis ügyféloldali könyvtára is támogatja. Emellett a soros szintű biztonság használatával szűrheti, hogy az egyes bérlők milyen sorokhoz férhetnek hozzá – részletekért lásd: [több-bérlős alkalmazások rugalmas adatbázis-eszközökkel és a sorok szintjének biztonsága](sql-database-elastic-tools-multi-tenant-row-level-security.md). A több-bérlős horizontális Felskálázási minta segítségével szükség lehet az adatbázisok adatbázisok közötti újraterjesztésére. A rugalmas készleteket használó SaaS-alkalmazások szerkezeti kialakításainak alaposabb megismeréséhez olvassa el a [Tervminták több-bérlős SaaS-alkalmazásokhoz Azure SQL Database esetén](sql-database-design-patterns-multi-tenancy-saas-applications.md) című részt.

### <a name="move-data-from-multiple-to-single-tenancy-databases"></a>Adatok áthelyezése többről egybérlős adatbázisokba
SaaS-alkalmazások létrehozásakor jellemző, hogy a leendő ügyfelek számára a szoftver próbaverzióját kínálja. Ebben az esetben költséghatékony, ha több-bérlős adatbázist használ az adatkezeléshez. Ha azonban az ügyfél válik elérhetővé, akkor egy egybérlős adatbázis jobb, mivel jobb teljesítményt nyújt. Ha az ügyfél a próbaidőszak alatt hozta létre az adatait, a [Split-Merge eszközzel](sql-database-elastic-scale-overview-split-and-merge.md) helyezheti át a több-bérlő adatait az új egybérlős adatbázisba.

## <a name="next-steps"></a>További lépések
Az ügyféloldali függvénytárat bemutató minta alkalmazáshoz lásd: Ismerkedés [a Elastic Database eszközökkel](sql-database-elastic-scale-get-started.md).

Ha a meglévő adatbázisokat az eszközök használatára szeretné átalakítani, tekintse meg a [meglévő adatbázisok](sql-database-elastic-convert-to-use-elastic-tools.md)áttelepítésének felskálázását ismertető témakört.

A rugalmas készletre vonatkozó részletek megtekintéséhez tekintse meg a [rugalmas készlet árának és teljesítményével kapcsolatos szempontokat](sql-database-elastic-pool.md), vagy hozzon létre egy [rugalmas](sql-database-elastic-pool-manage-portal.md)készletekkel rendelkező új készletet.  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-introduction/tools.png
[2]:./media/sql-database-elastic-scale-introduction/h_versus_vert.png
[3]:./media/sql-database-elastic-scale-introduction/overview.png
[4]:./media/sql-database-elastic-scale-introduction/single_v_multi_tenant.png

