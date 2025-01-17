---
title: Az adatbetöltés ajánlott eljárásai – Azure SQL Data Warehouse | Microsoft Docs
description: Az adatok Azure SQL Data Warehouse-ba való betöltése – javaslatok és teljesítményoptimalizálás
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: load-data
ms.date: 08/08/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seoapril2019
ms.openlocfilehash: a1433139695eb59fa3fd721852fae3181b8f892b
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68882479"
---
# <a name="best-practices-for-loading-data-into-azure-sql-data-warehouse"></a>Az adatok Azure SQL Data Warehouse-ba való betöltésének ajánlott eljárásai

Az adatok Azure SQL Data Warehouse-ba való betöltése – javaslatok és teljesítményoptimalizálás

## <a name="preparing-data-in-azure-storage"></a>Adatok előkészítése az Azure Storage-ban

A késés minimalizálása érdekében helyezze egymás mellé a tárolási réteget és az adattárházat.

Az adatok ORC fájlformátumba való exportálásakor Java memóriahiány-hibák jelentkezhetnek, ha a szövegoszlopok túl nagyok. Ezt a korlátozást úgy küszöbölheti ki, ha az oszlopok csak egy részhalmazát exportálja.

A PolyBase nem képes 1 000 000 bájtnál több adatot tartalmazó sorok betöltésére. Az Azure Blob Storage-ba vagy az Azure Data Lake Store-ba helyezett szöveges fájlok nem tartalmazhatnak 1 000 000 bájtnál több adatot. Ez a bájtkorlátozás a táblasémától függetlenül érvényes.

Minden fájlformátum eltérő teljesítményjellemzővel rendelkezik. A leggyorsabb betöltés érdekében használjon tömörített, tagolt szövegfájlokat. Az UTF-8 és UTF-16 formátum teljesítménye között minimális a különbség.

A nagy tömörített fájlokat ossza fel kisebb tömörített fájlokra.

## <a name="running-loads-with-enough-compute"></a>Betöltések futtatása elegendő számítási teljesítménnyel

A leggyorsabb betöltési sebesség érdekében egyszerre egy betöltési feladatot futtasson. Ha ez nem lehetséges, egyszerre a lehető legkevesebb betöltést futtassa. Ha nagy betöltési feladatra számít, érdemes felskálázni az adattárházat a betöltés előtt.

A betöltések megfelelő számítási erőforrásokkal való futtatásához hozzon létre betöltések futtatására kijelölt felhasználókat. Az egyes betöltést végző felhasználókat rendelje hozzá egy adott erőforrásosztályhoz. Betöltés futtatásához jelentkezzen be az egyik betöltési felhasználóként, majd futtassa a betöltést. A betöltés a felhasználó erőforrásosztályával fut.  Ez a módszer egyszerűbb, mint a felhasználó erőforrásosztályának módosításával próbálkozni, hogy az megfeleljen az aktuális erőforrásosztály-igénynek.

### <a name="example-of-creating-a-loading-user"></a>Példa egy betöltést végző felhasználó létrehozására

Ez a példa létrehoz egy betöltést végző felhasználót a staticrc20 erőforrásosztályhoz. Ennek első lépése a **főkiszolgálóhoz való csatlakozás** és egy bejelentkezés létrehozása.

```sql
   -- Connect to master
   CREATE LOGIN LoaderRC20 WITH PASSWORD = 'a123STRONGpassword!';
```

Kapcsolódjon az adattárházhoz, majd hozzon létre egy felhasználót. A következő kód azt feltételezi, hogy a mySampleDataWarehouse nevű adatbázishoz kapcsolódik. A kód azt mutatja, hogyan lehet létrehozni egy LoaderRC20 nevű felhasználót, illetve hogyan lehet vezérlői jogosultságot adni számára egy adatbázishoz. Ezután a kód felveszi a felhasználót a staticrc20 adatbázis-szerepkör tagjaként.  

```sql
   -- Connect to the database
   CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
   GRANT CONTROL ON DATABASE::[mySampleDataWarehouse] to LoaderRC20;
   EXEC sp_addrolemember 'staticrc20', 'LoaderRC20';
```

Ha a Staticrc20 erőforrásosztályhoz erőforrás-osztályaihoz erőforrásokkal rendelkező terhelést szeretne futtatni, jelentkezzen be LoaderRC20 néven, és futtassa a terhelést.

A betöltéseket inkább statikus, mint dinamikus erőforrásosztályokkal futtassa. A statikus erőforrás-osztályok használata ugyanazokat az erőforrásokat garantálja, függetlenül [](what-is-a-data-warehouse-unit-dwu-cdwu.md)az adattárház-egységektől. Ha dinamikus erőforrásosztályt használ, az erőforrások a szolgáltatásszinttől függően változhatnak. Dinamikus osztályok esetében egy alacsonyabb szolgáltatási szint azt jelenti, hogy feltehetően nagyobb erőforrásosztályt kell használnia a betöltést végző felhasználóhoz.

## <a name="allowing-multiple-users-to-load"></a>Betöltés engedélyezése több felhasználó számára

Gyakran van szükség több olyan felhasználóra, akik adatokat töltenek egy adattárházba. A [(Transact-SQL) CREATE TABLE](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) betöltéséhez az ADATBÁZISnak vezérlési engedélyekkel kell rendelkeznie.  A CONTROL engedély az összes séma vezérlését biztosítja. Előfordulhat, hogy nem szeretné, hogy minden betöltést végző felhasználó vezérelési jogot kapjon az összes sémához. Az engedélyek korlátozására használja a DENY CONTROL utasítást.

Vegyünk például két adatbázissémát: schema_A az A részleghez, és schema_B a B részleghez. Legyen user_A és user_B két PolyBase-betöltést végző adatbázis-felhasználó az A, illetve a B részlegen. Mindkét felhasználó kapott adatbázisszintű CONTROL jogosultságokat. Az A és B séma létrehozói zárolják a sémáikat a DENY utasítás segítségével:

```sql
   DENY CONTROL ON SCHEMA :: schema_A TO user_B;
   DENY CONTROL ON SCHEMA :: schema_B TO user_A;
```

User_A és user_B számára mostantól nem lesz hozzáférhető a másik részleg sémája.

## <a name="loading-to-a-staging-table"></a>Betöltés előkészítési táblába

Az adattárház táblájába való adatáthelyezés leggyorsabb betöltési sebességének eléréséhez töltse be az adatokat egy előkészítési táblába.  Határozza meg az előkészítési táblát halomként, és használjon ciklikus időszeletelést a terjesztési beállításhoz. 

Vegye figyelembe, hogy a betöltés általában két lépésből álló folyamat, amely során először az előkészítési táblába tölti be, majd beszúrja az adatokat egy éles adattárháztáblába. Ha az éles tábla kivonatoló terjesztést használ, a betöltés és a beszúrás teljes ideje gyorsabb lehet, ha meghatároz egy előkészítési táblát a kivonatoló terjesztéssel. Az előkészítési táblába való betöltés több időt vesz igénybe, de a sorok az éles táblába való beszúrásának második lépése nem jár a disztribúciók közötti adatmozgatással.

## <a name="loading-to-a-columnstore-index"></a>Betöltés oszlopcentrikus indexbe

Az oszlopcentrikus indexek sok memóriát igényelnek az adatok jó minőségű sorcsoportokba való tömörítéséhez. A legjobb tömörítési és indexelési hatékonyság érdekében az oszlopcentrikus indexnek a maximális 1 048 576 sort kell tömörítenie az egyes sorcsoportokba. Ha korlátozott a rendelkezésre álló memória mennyisége, előfordulhat, hogy az oszlopcentrikus index nem éri el a maximális tömörítési sebességet. Ez hatással van a lekérdezés teljesítményére. A témakör részletes bemutatása: [Oszloptár memóriájának optimalizálása](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

- Annak érdekben, hogy elég memória álljon a betöltést végző felhasználók rendelkezésére a maximális tömörítési sebesség eléréséhez, használjon olyan betöltést végző felhasználókat, akik közepes vagy nagy erőforrásosztály tagjai. 
- Töltsön be elég sort az új sorcsoportok teljes feltöltéséhez. Kötegelt betöltés során minden 1 048 576. sor teljes sorcsoportként közvetlenül az oszloptárba van tömörítve. A 102 400 sornál kisebb betöltések a deltatárba küldik a sorokat, ahol a sorok B-fában vannak tárolva. Ha kevesebb sort tölt be, előfordulhat, hogy mind a deltatárba kerül, és a rendszer nem tömöríti azokat azonnal oszloptár formátumba.

## <a name="increase-batch-size-when-using-sqlbulkcopy-api-or-bcp"></a>A Batch méretének növeléséhez a SQLBulkCopy API vagy a BCP használata esetén
Ahogy korábban említettük, a következővel való betöltés a legmagasabb átviteli sebességet biztosítja a SQL Data Warehouse. Ha a SQLBulkCopy API-t (vagy a BCP-t) nem lehet betöltésre használni, érdemes megfontolni a Batch méretének növelését a jobb teljesítmény érdekében. 

## <a name="handling-loading-failures"></a>Betöltési hibák kezelése

Egy külső táblát használó betöltés meghiúsulhat a következő hibával: *„A lekérdezés megszakadt – a rendszer elérte a felső visszautasítási küszöbértéket külső forrásból való beolvasás során”* . Ez az üzenet azt jelzi, hogy a külső adatok szabálytalan rekordokat tartalmaznak. Az adatrekord akkor számít „szabálytalannak”, ha az oszlopok adattípusai és száma nem felel meg a külső tábla definícióinak, vagy ha az adatok nem felelnek meg a megadott külső fájlformátumnak. 

A szabálytalan rekordok kijavításához győződjön meg arról, hogy a külső tábla- és fájlformátum-definíciók helyesek, és hogy a külső adatok megfelelnek ezeknek a definícióknak. Amennyiben a külső adatrekordok egy részhalmaza szabálytalan, dönthet úgy, hogy nem tart igényt ezekre a rekordokra a lekérdezéseihez. Ehhez használja a CREATE EXTERNAL TABLE visszautasítási lehetőségeit.

## <a name="inserting-data-into-a-production-table"></a>Adatok beszúrása az éles táblába

A kis táblák [INSERT utasítással](/sql/t-sql/statements/insert-transact-sql) végzett egyszeri feltöltése vagy akár egy keresés rendszeres újratöltése is megfelelő lehet, ha egy, a következőhöz hasonló utasítást használ: `INSERT INTO MyLookup VALUES (1, 'Type 1')`.  Az egyszeres beszúrásoknál azonban hatékonyabb egy kötegelt betöltés végrehajtása. 

Ha több ezer egyszeres beszúrást hajt végre egy nap, kötegelje a beszúrásokat, hogy kötegelve tölthesse be őket.  Fejlesszen folyamatokat, amelyek az egyszeres beszúrásokat egy fájlhoz fűzik, majd hozzon létre egy másik folyamatot, amely időszakosan betölti a fájlt.

## <a name="creating-statistics-after-the-load"></a>Statisztika létrehozása a betöltés után

A lekérdezési teljesítmény javításához fontos létrehozni statisztikákat a táblák összes oszlopához az első betöltés után, illetve az adatok minden lényeges módosítását követően.  Ezt manuálisan is megteheti, vagy engedélyezheti az [automatikus létrehozási statisztikát](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics#automatic-creation-of-statistic).

A statisztika részletes ismertetése: [Statisztika](sql-data-warehouse-tables-statistics.md). Az alábbi példa bemutatja, hogyan hozhat létre manuálisan statisztikákat a Customer_Speed tábla öt oszlopán.

```sql
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## <a name="rotate-storage-keys"></a>Tárkulcsok rotálása

Biztonsági szempontból érdemes rendszeresen módosítani a Blob Storage hozzáférési kulcsát. A Blob Storage-fiókhoz két tárkulcs tartozik, amely lehetővé teszi a kulcsok közötti váltást.

Az Azure Storage-fiók kulcsainak rotálása:

Adja ki az [ALTER DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/alter-database-scoped-credential-transact-sql) parancsot minden olyan tárfiókhoz, amelynek módosult a kulcsa.

Példa:

Létrejön az eredeti kulcs

```sql
CREATE DATABASE SCOPED CREDENTIAL my_credential WITH IDENTITY = 'my_identity', SECRET = 'key1'
```

A kulcs rotálása az 1. kulcsból a 2. kulcsba

```sql
ALTER DATABASE SCOPED CREDENTIAL my_credential WITH IDENTITY = 'my_identity', SECRET = 'key2' 
```

A mögöttes külső adatforrásokban nem kell más módosítást elvégezni.

## <a name="next-steps"></a>További lépések

- További információ a PolyBase-ről és a kinyerési, betöltési és átalakítási (ELT) folyamat megtervezéséről: [ELT tervezése SQL Data Warehouse-hoz](design-elt-data-loading.md).
- Betöltési oktatóanyag: [Adatok betöltése az Azure Blob Storage-ból az Azure SQL Data Warehouse-ba a PolyBase használatával](load-data-from-azure-blob-storage-using-polybase.md).
- Az adatbetöltések monitorozása: [A számítási feladat monitorozása DMV-kkel](sql-data-warehouse-manage-monitor.md).
