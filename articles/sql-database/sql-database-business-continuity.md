---
title: Felhőalapú üzletmenet-folytonosság – adatbázis-helyreállítás – SQL Database | Microsoft Docs
description: Ismerje meg, hogyan támogatja az Azure SQL Database a felhőalapú üzletmenet-folytonosságot és az adatbázis-helyreállítást, és hogyan segít az üzletmenet szempontjából kritikus fontosságú felhőalapú alkalmazások folyamatos működésének biztosításában.
keywords: üzletmenet-folytonosság,felhőalapú üzletmenet-folytonosság,adatbázis-vészhelyreállítás,adatbázis-helyreállítás
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 06/25/2019
ms.openlocfilehash: e57427fbb7e0d3c67fc4fcbab1a50f14ef8c9501
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569333"
---
# <a name="overview-of-business-continuity-with-azure-sql-database"></a>Az Azure SQL Database üzletmenet-folytonossági funkcióinak áttekintése

Az **üzletmenet folytonossága** Azure SQL Database olyan mechanizmusokra, szabályzatokra és eljárásokra utal, amelyek lehetővé teszik a vállalat számára, hogy a működés közben továbbra is működjenek, különösen a számítástechnikai infrastruktúrában. A legtöbb esetben a Azure SQL Database fogja kezelni a felhőalapú környezetben esetlegesen előforduló zavaró eseményeket, és megtartja az alkalmazásaikat és az üzleti folyamatokat. Vannak azonban olyan zavaró események, amelyeket a SQL Database automatikusan nem tud kezelni, például:

- A felhasználó véletlenül törölt vagy frissített egy sort egy táblában.
- A rosszindulatú támadó sikeresen törölte az adattörlést, vagy elvetette az adatbázist.
- A földrengés áramkimaradást okozott, és átmenetileg letiltotta az adatközpontot.

Az áttekintés az Azure SQL Database az üzletmenet-folytonosság és a vészhelyreállítás biztosítására szolgáló funkcióit írja le. Ismerje meg az adatvesztést okozó, vagy az adatbázis és az alkalmazás nem elérhetővé tételéhez szükséges lehetőségeket, javaslatokat és oktatóanyagokat. Megtudhatja, mi a teendő, ha egy felhasználói vagy alkalmazáshiba hatással van az adatok integritására, egy Azure-régió leáll, vagy az alkalmazás karbantartást igényel.

## <a name="sql-database-features-that-you-can-use-to-provide-business-continuity"></a>Az SQL Database üzletmenet-folytonosságot biztosító funkciói

Adatbázis perspektívájában négy jelentős lehetséges megszakítási forgatókönyv létezik:

- Az adatbázis-csomópontot érintő helyi hardveres vagy szoftveres hibák, például a lemezmeghajtó meghibásodása.
- Az adatsérülést vagy-törlést általában egy alkalmazás hibája vagy emberi hiba okozta. Az ilyen hibák alkalmazásspecifikusek, és általában nem észlelhetők az adatbázis-szolgáltatás.
- Adatközpont-kimaradás, valószínűleg természeti katasztrófa okozta. Ehhez a forgatókönyvhöz az alkalmazások feladatátvételének egy másik adatközpontba való betöltésének bizonyos szintje szükséges.
- Frissítési vagy karbantartási hibák, a tervezett infrastruktúra karbantartása vagy frissítése során felmerülő, nem várt problémák szükségessé tehetik a korábbi adatbázis állapotának gyors visszaállítását.

A helyi hardverek és szoftverek meghibásodásának enyhítése érdekében SQL Database tartalmaz egy [magas rendelkezésre állású architektúrát](sql-database-high-availability.md), amely a hibák automatikus helyreállítását garantálja, akár 99,995%-os rendelkezésre ÁLLÁSi SLA-val.  

Az adatvesztés elleni védelem érdekében SQL Database automatikusan létrehozza a teljes adatbázis biztonsági mentését hetente, a különbözeti adatbázis biztonsági másolatait 12 óránként, a tranzakciónapló biztonsági mentése pedig 5-10 percenként történik. A biztonsági másolatok az RA-GRS tárolóban tárolódnak legalább 7 napig az összes szolgáltatási réteg esetében. Az összes szolgáltatási réteg, kivéve a alapszintű támogatás konfigurálható biztonsági mentés megőrzési időtartamát az időponthoz tartozó visszaállításhoz, legfeljebb 35 napig. 

A SQL Database számos üzletmenet-folytonossági funkciót is biztosít, amelyek segítségével csökkentheti a különböző nem tervezett forgatókönyveket. 

- A [historikus táblák](sql-database-temporal-tables.md) segítségével a sorverziókat bármely időpontra visszaállíthatja.
- A [beépített automatizált biztonsági mentések](sql-database-automated-backups.md) és időpontok [visszaállítása](sql-database-recovery-using-backups.md#point-in-time-restore) lehetővé teszi a teljes adatbázis visszaállítását egy bizonyos időpontra a beállított megőrzési időtartamon belül, legfeljebb 35 napig.
- Visszaállíthat [egy törölt adatbázist](sql-database-recovery-using-backups.md#deleted-database-restore) arra a pontra, amelyen törölve lett, ha a **SQL Database-kiszolgáló nem lett törölve**.
- A [biztonsági másolatok hosszú távú megőrzése](sql-database-long-term-retention.md) lehetővé teszi, hogy a biztonsági mentések akár 10 évig is megmaradjanak.
- Az [aktív geo-replikáció](sql-database-active-geo-replication.md) lehetővé teszi, hogy a rendszer olvasható replikákat hozzon létre, és manuálisan végezze el a feladatátvételt az adatközpont-kimaradás vagy az alkalmazások frissítése esetén bármelyik replikára.
- Az [automatikus feladatátvételi csoport](sql-database-auto-failover-group.md#auto-failover-group-terminology-and-capabilities) lehetővé teszi, hogy az alkalmazás automatikusan helyreállítsa az adatközpont-kimaradás esetén.

## <a name="recover-a-database-within-the-same-azure-region"></a>Adatbázis helyreállítása ugyanazon az Azure-régión belül

Az adatbázis automatikus biztonsági mentései segítségével visszaállíthat egy adatbázist egy korábbi időpontra. Így helyreállíthatja az emberi hibák által okozott adatsérüléseket. Az időpontok közötti visszaállítás lehetővé teszi, hogy egy olyan új adatbázist hozzon létre ugyanabban a kiszolgálón, amely az adatállapotot jelöli a sérült esemény előtt. A legtöbb adatbázis esetében a visszaállítási műveletek kevesebb, mint 12 órát vesznek igénybe. Egy nagyon nagy vagy nagyon aktív adatbázis helyreállítása hosszabb időt is igénybe vehet. A helyreállítási időre vonatkozó további információkért lásd: [adatbázis-helyreállítás ideje](sql-database-recovery-using-backups.md#recovery-time). 

Ha az időponthoz kötött visszaállításhoz (PITR) tartozó maximálisan támogatott biztonsági mentési megőrzési időtartam nem elegendő az alkalmazáshoz, kiterjesztheti azt egy hosszú távú adatmegőrzési (LTR) házirend konfigurálásával az adatbázis (ok) hoz. További információ: a [biztonsági másolatok hosszú távú megőrzése](sql-database-long-term-retention.md).

## <a name="compare-geo-replication-with-failover-groups"></a>Geo-replikáció összehasonlítása a feladatátvételi csoportokkal

Az [automatikus feladatátvételi csoportok](sql-database-auto-failover-group.md#auto-failover-group-terminology-and-capabilities) egyszerűbbé teszik a [geo-replikáció](sql-database-active-geo-replication.md) üzembe helyezését és használatát, valamint a további képességek hozzáadását az alábbi táblázatban leírtak szerint:

|                                              | Georeplikáció | Feladatátvételi csoportok  |
|:---------------------------------------------| :-------------- | :----------------|
| Automatikus feladatátvétel                           |     Nem          |      Igen         |
| Több adatbázis feladatátvétele egyszerre  |     Nem          |      Igen         |
| A kapcsolatok karakterláncának frissítése a feladatátvétel után      |     Igen         |      Nem          |
| Felügyelt példány támogatott                   |     Nem          |      Igen         |
| Ugyanabban a régióban lehet, mint az elsődleges             |     Igen         |      Nem          |
| Több replika                            |     Igen         |      Nem          |
| Olvasási méretezés támogatása                          |     Igen         |      Igen         |
| &nbsp; | &nbsp; | &nbsp; |


## <a name="recover-a-database-to-the-existing-server"></a>Adatbázis helyreállítása a meglévő kiszolgálóra

Bár ritka, mégis előfordulhat, hogy valamelyik Azure-adatközpont leáll. Leállás esetén az üzletmenet esetleg csak néhány percre, de akár több órára is megszakadhat.

- Az egyik lehetőség, hogy megvárja, amíg az adatközpont leállását követően az adatbázis újra elérhető lesz. Ez az olyan alkalmazások esetében működik, amelyek esetében megengedhető, hogy az adatbázis offline állapotú legyen. Például egy fejlesztési projekt vagy egy ingyenes próbaverzió esetében, amelyeken nem kell folyamatosan dolgoznia. Ha egy adatközpont leáll, nem tudja, hogy mennyi ideig tarthat a leállás, így ez a lehetőség csak akkor működik, ha egy ideig nincs szüksége az adatbázisra.
- Egy másik lehetőség, hogy egy adatbázist bármely Azure-régióban lévő kiszolgálón visszaállítson a [geo-redundáns adatbázis-biztonsági mentések](sql-database-recovery-using-backups.md#geo-restore) (Geo-visszaállítás) használatával. A Geo-visszaállítás a forrásként egy geo-redundáns biztonsági mentést használ, amely akkor is használható az adatbázisok helyreállításához, ha az adatbázis vagy az adatközpont egy leállás miatt nem érhető el.
- Végezetül gyorsan helyreállíthatja az adatkimaradást, ha az [aktív geo-replikációval](sql-database-active-geo-replication.md) vagy az adatbázis vagy adatbázisok [automatikus feladatátvételi csoportjából](sql-database-auto-failover-group.md) konfigurálta a Geo-másodlagos szolgáltatást. A technológiák közül választhat, vagy manuális vagy automatikus feladatátvételt is használhat. Míg maga a feladatátvétel csak néhány másodpercet vesz igénybe, a szolgáltatás legalább egy órát vesz igénybe az aktiváláshoz. Erre azért van szükség, hogy a feladatátvételt a leállás skálázása igazolja. Emellett a feladatátvétel az aszinkron replikáció jellegéből kifolyólag kisebb adatvesztést eredményezhet. 

Az üzletmenet-folytonossági terv kidolgozása során meg kell állapítania az alkalmazás a zavaró eseményeket követő teljes helyreállításának maximális elfogadható időkorlátját. Az alkalmazás teljes helyreállításához szükséges idő a helyreállítási időre vonatkozó célkitűzés (RTO). Meg kell ismernie a legutóbbi adatfrissítések (időintervallum) maximális időtartamát is, ha az alkalmazás a nem tervezett zavaró eseményről történő helyreállítás során elveszíti az adatvesztést. A lehetséges adatvesztés a helyreállítási pont célkitűzése (RPO) néven ismert.

A különböző helyreállítási módszerek különböző szintű RPO és RTO biztosítanak. Kiválaszthat egy adott helyreállítási módszert, vagy használhat metódusok kombinációját a teljes alkalmazás-helyreállítás eléréséhez. A következő táblázat összehasonlítja az egyes helyreállítási lehetőségek RPO és RTO. Az automatikus feladatátvételi csoportok egyszerűbbé teszik a Geo-replikáció üzembe helyezését és használatát, és hozzáadja a további képességeket az alábbi táblázatban leírtak szerint.

| Helyreállítási módszer | RTO | Helyreállítási időkorlát |
| --- | --- | --- | 
| Geo-visszaállítás földrajzilag replikált biztonsági másolatokból | 12 óra | 1 óra |
| Automatikus feladatátvételi csoportok | 1 óra | 5 s |
| Manuális adatbázis-feladatátvétel | 30 s | 5 s |

> [!NOTE]
> A *manuális adatbázis-feladatátvétel* arra utal, hogy a nem [tervezett móddal](sql-database-active-geo-replication.md#active-geo-replication-terminology-and-capabilities)egyetlen adatbázis feladatátvételét a földrajzilag replikált másodlagosra hajtja végre.
A jelen cikk korábbi, az automatikus feladatátvétel RTO és RPO részletes ismertetését lásd a következő táblázatban.


Automatikus feladatátvételi csoportok használata, ha az alkalmazás megfelel a következő feltételeknek:

- Az üzletmenet szempontjából kritikus fontosságú.
- Olyan szolgáltatói szerződéssel (SLA) rendelkezik, amely 12 órát vagy több állásidőt nem engedélyez.
- Az állásidő pénzügyi felelősséget eredményezhet.
- Nagy adatváltozások és 1 órányi adatvesztés esetén nem fogadható el elfogadható érték.
- Az aktív georeplikáció többletköltsége alacsonyabb, mint a potenciális pénzbeli kötelezettség és a kapcsolódó üzletvesztés összege.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-protecting-important-DBs-from-regional-disasters-is-easy/player]
>

Az alkalmazás követelményeitől függően dönthet úgy, hogy az adatbázis biztonsági másolatait és az aktív geo-replikáció kombinációját használja. Az önálló adatbázisok és az ezekkel az üzletmenet-folytonossági funkciókkal rendelkező rugalmas készletek kialakításával kapcsolatos szempontokat a következő témakörben tekintheti meg: [Alkalmazások tervezése Felhőbeli](sql-database-designing-cloud-solutions-for-disaster-recovery.md) vész-helyreállítási és [rugalmas készlet vész-helyreállítási stratégiákhoz](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

A következő szakaszokban áttekintheti az adatbázis-biztonsági másolatok vagy az aktív földrajzi replikálás használatával történő helyreállítás lépéseit. A részletes lépések, például a tervezési követelmények, a helyreállítási lépések után, valamint az kimaradás szimulálása a vész-helyreállítási gyakorlat elvégzéséhez című témakörben talál további információt: [SQL Database helyreállítása leállás](sql-database-disaster-recovery.md)miatt.

### <a name="prepare-for-an-outage"></a>Előkészületek leállás esetére

A használt üzletmenet-folytonossági funkciótól függetlenül végre kell hajtania a következőket:

- Azonosítsa és készítse elő a célkiszolgáló, beleértve a kiszolgálói szintű IP-tűzfalszabályok, a bejelentkezések és a főadatbázis szintű engedélyeket.
- Határozza meg, hogyan legyenek átirányítva az ügyfelek és az ügyfélalkalmazások az új kiszolgálóra.
- Dokumentálja az egyéb függőségeket, mint a naplózási beállítások és a riasztások.

Ha nem készít megfelelő előfizetést, a feladatátvétel vagy adatbázis-helyreállítás után online állapotba helyezi az alkalmazásokat, és valószínűleg szükség van a hibaelhárításra a stressz-rossz kombináció esetén is.

### <a name="fail-over-to-a-geo-replicated-secondary-database"></a>Feladatátvétel egy földrajzilag replikált másodlagos adatbázisba

Ha aktív geo-replikációs vagy automatikus feladatátvételi csoportokat használ helyreállítási mechanizmusként, konfigurálhat egy automatikus feladatátvételi házirendet, vagy manuális, nem [tervezett feladatátvételt](sql-database-active-geo-replication-portal.md#initiate-a-failover)is használhat. A művelet megkezdése után a feladatátvétel az új elsődlegesvé válik, és készen áll az új tranzakciók rögzítésére és a lekérdezésekre való reagálásra – a még nem replikált adatok minimális adatvesztése miatt. A feladatátvételi folyamat megtervezésével kapcsolatos információkért lásd: [alkalmazás tervezése a Felhőbeli](sql-database-designing-cloud-solutions-for-disaster-recovery.md)vész-helyreállításhoz.

> [!NOTE]
> Amikor az adatközpont ismét elérhetővé válik, a régi alapértékek automatikusan újra csatlakoznak az új elsődlegeshöz, és másodlagos adatbázisok lesznek. Ha át kell helyeznie az elsődlegest az eredeti régióba, manuálisan kezdeményezheti a tervezett feladatátvételt (feladat-visszavétel).

### <a name="perform-a-geo-restore"></a>Geo-visszaállítás végrehajtása

Ha az automatikus biztonsági mentést a Geo-redundáns tárolóval (alapértelmezés szerint engedélyezve) használja, az adatbázist a [geo-visszaállítás](sql-database-disaster-recovery.md#recover-using-geo-restore)használatával állíthatja helyre. A helyreállítás általában 12 órán belül zajlik – az adatvesztés akár egy óráig is, az utolsó napló biztonsági mentésének és replikálásának időpontjában. A helyreállítás befejezéséig az adatbázis nem tudja rögzíteni a tranzakciókat, illetve nem tud válaszolni a lekérdezésekre. Megjegyzés: a Geo-visszaállítás csak az utolsó elérhető időpontra állítja vissza az adatbázist.

> [!NOTE]
> Ha az adatközpont ismét online állapotba kerül, mielőtt átvált az alkalmazásra a helyreállított adatbázisra, megszakíthatja a helyreállítást.

### <a name="perform-post-failover--recovery-tasks"></a>Feladatátvétel/helyreállítás utáni feladatok végrehajtása

A helyreállítási mechanizmusok végrehajtása után a következő további feladatokat kell végrehajtania a felhasználók és az alkalmazások újbóli üzembe helyezéséhez:

- Irányítsa át az ügyfeleket és az ügyfélalkalmazásokat az új kiszolgálóra és a helyreállított adatbázisra.
- Győződjön meg arról, hogy a megfelelő kiszolgálói szintű IP-tűzfalszabályok vannak érvényben a felhasználók számára a megfelelő szabályok engedélyezéséhez, illetve az [adatbázis-szintű tűzfalak](sql-database-firewall-configure.md#manage-server-level-ip-firewall-rules-using-the-azure-portal) használatával történő kapcsolódáshoz.
- Biztosítsa a megfelelő bejelentkezési adatok és főadatbázis-szintű jogosultságok rendelkezésre állását (vagy használjon [tartalmazott felhasználókat](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable))
- Konfigurálja a naplózást, ha szükséges.
- Konfigurálja a riasztásokat, ha szükséges.

> [!NOTE]
> Ha feladatátvételi csoportot használ, és az írható-olvasható lstener használatával csatlakozik az adatbázisokhoz, akkor az átirányítás a feladatátvételt követően automatikusan és transzparens módon történik az alkalmazásban.

## <a name="upgrade-an-application-with-minimal-downtime"></a>Alkalmazások frissítése minimális állásidővel

Időnként előfordulhat, hogy egy alkalmazást a tervezett karbantartás, például egy alkalmazás frissítése miatt offline állapotba kell helyezni. Az [alkalmazások frissítéseinek kezelése](sql-database-manage-application-rolling-upgrade.md) azt ismerteti, hogyan használható az aktív geo-replikálás a Felhőbeli alkalmazások működés közbeni frissítésének engedélyezéséhez, hogy a frissítés során csökkentse az állásidőt, és ha valamilyen hiba történik, helyreállítási útvonalat adjon meg.

## <a name="next-steps"></a>További lépések

Az önálló adatbázisok és a rugalmas készletek alkalmazás-kialakítási szempontjaival kapcsolatban lásd: [alkalmazás tervezése a Felhőbeli](sql-database-designing-cloud-solutions-for-disaster-recovery.md) vész-helyreállításhoz és a [rugalmas készlet vész-helyreállítási stratégiái](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
