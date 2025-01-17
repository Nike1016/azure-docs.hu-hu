---
title: Dinamikus adatmaszkolás Azure SQL Database | Microsoft docs
description: SQL Database a dinamikus adatmaszkolás korlátozza a bizalmas adatokkal való érintkezést azáltal, hogy maszkolást végez a nem Kiemelt felhasználók számára
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: ronitr
ms.author: ronitr
ms.reviewer: vanto
ms.date: 03/04/2019
ms.openlocfilehash: 366b9437aab134985c73611fa8b46c6fbd3d309c
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568762"
---
# <a name="sql-database-dynamic-data-masking"></a>Dinamikus adatmaszkolás SQL Database

SQL Database dinamikus adatmaszkolás korlátozza a bizalmas adatokkal való érintkezést azáltal, hogy a nem Kiemelt felhasználók számára maszkolást végez. 

A dinamikus adatmaszkolás azzal segít megelőzni a bizalmas adatokhoz való jogosulatlan hozzáférést, hogy az ügyfél által meghatározhatóvá teszi az alkalmazásrétegre gyakorolt minimális következményekkel felfedhető bizalmas adatok menyiségét. Ez a szabályzatalapú biztonsági funkció elrejti a bizalmas adatokat egy kijelölt adatbázismezőkön végrehajtott lekérdezés eredményhalmazában, miközben az adatbázis adatait nem módosítja.

Előfordulhat például, hogy a Call Center egyik szolgáltatása a hívókat a hitelkártyaszám több számjegye alapján azonosítja, de ezeket az adatelemeket nem szabad teljes mértékben kitenni a szolgáltatás képviselőjének. A maszkolási szabályok határozzák meg, hogy a rendszer az összes, de az utolsó négy számjegyet elrejti a bármely lekérdezés eredmény-készletében. Egy másik példaként egy megfelelő adatmaszkot is meghatározhat a személyazonosításra alkalmas adatok (személyes adatok) adatainak védelme érdekében, így a fejlesztők a megfelelőségi rendeletek megszegése nélkül is lekérhetik a termelési környezeteket a hibaelhárítási célból.

## <a name="sql-database-dynamic-data-masking-basics"></a>A dinamikus adatmaszkolás alapjai SQL Database

A dinamikus adatmaszkolási szabályzatot a Azure Portal a dinamikus adatmaszkolási művelet kiválasztásával a SQL Database konfiguráció paneljén vagy a beállítások panelen.

### <a name="dynamic-data-masking-permissions"></a>Dinamikus adatmaszkolási engedélyek

A dinamikus adatmaszkolás konfigurálható a Azure SQL Database-rendszergazda, a kiszolgálói rendszergazda vagy az [SQL Security Manager](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager) szerepköreivel.

### <a name="dynamic-data-masking-policy"></a>Dinamikus adatmaszkolási szabályzat

* A **maszkolásból kizárt SQL-felhasználók** – olyan SQL-felhasználók vagy HRE-identitások összessége, amelyek az SQL-lekérdezés eredményeiben nem maszkolt adatokkal rendelkeznek. A rendszergazdai jogosultságokkal rendelkező felhasználók mindig ki vannak zárva a maszkolásból, és az eredeti adatok maszk nélkül jelennek meg.
* **Maszkolási szabályok** – a kijelölt mezőket és a használni kívánt maszkolási függvényt meghatározó szabályok halmaza. A kijelölt mezők az adatbázis-séma neve, a tábla neve és az oszlop neve alapján definiálhatók.
* **Maszkolási függvények** – olyan metódusok összessége, amelyek különböző forgatókönyvek esetében szabályozzák az adatok kitettségét.

| Maszkolási függvény | Maszkolási logika |
| --- | --- |
| **Alapértelmezett** |**Teljes maszkolás a kijelölt mezők adattípusainak megfelelően**<br/><br/>• Az XXXX vagy kevesebb XS használata, ha a mező mérete kisebb, mint 4 karakter a sztring adattípusok esetében (NCHAR, ntext, nvarchar).<br/>• Nulla értéket használhat a numerikus adattípusokhoz (bigint, bit, decimális, int, Money, numerikus, smallint, túlcsordulási, tinyint, float, Real).<br/>• Az 01-01-1900-as dátum/idő adattípusok (dátum, datetime2, datetime, DateTimeOffset, idő adattípusúra, Time) használata.<br/>• Az SQL Variant esetében az aktuális típus alapértelmezett értéke lesz használatban.<br/>• Az XML esetében a \<rendszer a dokumentum maszkolását/> használja.<br/>• Üres értéket használhat a speciális adattípusokhoz (timestamp-tábla, hierarchyid, GUID, Binary, képek, varbinary térbeli típusok). |
| **Bankkártya** |**Maszkolási módszer, amely a kijelölt mezők utolsó négy számjegyét teszi elérhetővé** , és egy konstans karakterláncot hoz létre egy hitelkártya formájában.<br/><br/>XXXX-XXXX-XXXX-1234 |
| **E-mail** |**Maszkolási módszer, amely az első betűt teszi elérhetővé, és lecseréli a tartományt a xxx.com** egy állandó karakterlánc-előtaggal e-mail-cím formájában.<br/><br/>aXX@XXXX.com |
| **Véletlenszerű szám** |**Maszkolási módszer, amely** a kiválasztott határok és a tényleges adattípusok alapján véletlenszerűen generált számot hoz létre. Ha a kijelölt határok egyenlőek, akkor a maszkolási függvény állandó szám.<br/><br/>![Navigációs ablaktábla](./media/sql-database-dynamic-data-masking-get-started/1_DDM_Random_number.png) |
| **Egyéni szöveg** |**Maszkolási módszer, amely az első és az utolsó karaktert teszi elérhetővé** , és egy egyéni kitöltési karakterláncot helyez el a közepén. Ha az eredeti sztring rövidebb a megjelenő előtagnál és utótagnál, csak a kitöltés karakterláncot használja a rendszer. <br/>előtag [kitöltés] utótag<br/><br/>![Navigációs ablaktábla](./media/sql-database-dynamic-data-masking-get-started/2_DDM_Custom_text.png) |

<a name="Anchor1"></a>

### <a name="recommended-fields-to-mask"></a>Ajánlott maszkolandó mezők

A DDM-javaslatok motorja az adatbázis egyes mezőit potenciálisan bizalmas mezőként adja meg, ami valószínűleg a maszkoláshoz is jó választás lehet. A portál dinamikus adatmaszkolás paneljén látni fogja az adatbázis ajánlott oszlopait. Mindössze annyit kell tennie, hogy a **maszk hozzáadása** egy vagy több oszlophoz lehetőséget választja, majd a **Mentés** gombra kattint a mezőkhöz tartozó maszk alkalmazásához.

## <a name="set-up-dynamic-data-masking-for-your-database-using-powershell-cmdlets"></a>Dinamikus adatmaszkolás beállítása az adatbázishoz PowerShell-parancsmagok használatával

Lásd: [Azure SQL Database parancsmagok](https://docs.microsoft.com/powershell/module/az.sql).

## <a name="set-up-dynamic-data-masking-for-your-database-using-rest-api"></a>Dinamikus adatmaszkolás beállítása az adatbázishoz REST API használatával

Tekintse [meg Azure SQL Database műveleteit](https://docs.microsoft.com/rest/api/sql/).
