---
title: Kiszolgáló nélküli Azure SQL Database (előzetes verzió) | Microsoft Docs
description: Ez a cikk az új kiszolgáló nélküli számítási szintet ismerteti, és összehasonlítja a meglévő kiépített számítási szintjével.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: sstein, carlrab
ms.date: 07/05/2019
ms.openlocfilehash: 67e877609eec98e7100b34ab477dbab7c5577772
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69515282"
---
# <a name="azure-sql-database-serverless-preview"></a>Kiszolgáló nélküli Azure SQL Database (előzetes verzió)

Azure SQL Database kiszolgáló nélküli (előzetes verzió) az önálló adatbázisok számítási szintje, amely automatikusan méretezi a számítási feladatok igényét és számláit a másodpercenként felhasznált számítások mennyiségétől függően. A kiszolgáló nélküli számítási rétegek automatikusan szüneteltetik az adatbázisokat az inaktív időszakok során, amikor csak a tárterületet számlázzák, és automatikusan folytatják az adatbázisokat, amikor a tevékenység visszatér.

## <a name="serverless-compute-tier"></a>Kiszolgáló nélküli számítási szint

Az önálló adatbázisok kiszolgáló nélküli számítási rétegét egy számítási automatikus skálázási tartomány és egy automatikus szüneteltetési késleltetés jellemzi.  Ezeknek a paramétereknek a konfigurációja az adatbázis teljesítményének és számítási költségeit formálja.

![kiszolgáló nélküli számlázás](./media/sql-database-serverless/serverless-billing.png)

### <a name="performance-configuration"></a>Teljesítménykonfiguráció

- A **minimális virtuális mag** és a **maximális virtuális mag** olyan konfigurálható paraméterek, amelyek meghatározzák az adatbázis számára elérhető számítási kapacitás tartományát. A memória és az i/o-korlátok arányosak a megadott virtuális mag-tartománnyal.  
- Az automatikus **szüneteltetési késleltetés** egy konfigurálható paraméter, amely meghatározza azt az időtartamot, ameddig az adatbázisnak inaktívnak kell lennie, mielőtt a rendszer automatikusan szünetelteti az időt. A rendszer automatikusan folytatja az adatbázist, ha a következő bejelentkezés vagy más tevékenység történik.  Másik lehetőségként az autoszüneteltetés is letiltható.

### <a name="cost"></a>Költség

- A kiszolgáló nélküli adatbázisok díja a számítási és a tárolási díjak összegzése.
- Ha a számítási használat a minimális és a maximális korlát között van, a számítási díj a felhasznált virtuális mag és memória alapján történik.
- Ha a számítási használat a minimálisan konfigurált határértékek alatt van, a számítási díj a minimális virtuális mag és a minimálisan konfigurált memória alapján történik.
- Ha az adatbázis szüneteltetve van, a számítási költség nulla, és csak a tárolási költségek merülnek fel.
- A tárolási költségeket ugyanúgy határozzák meg, mint a kiépített számítási szinten.

További részletekért tekintse meg [](sql-database-serverless.md#billing)a számlázást ismertető témakört.

## <a name="scenarios"></a>Forgatókönyvek

A kiszolgáló nélküli, időszakos, kiszámíthatatlan használati mintákkal rendelkező önálló adatbázisokhoz optimalizált ár-teljesítmény, amely némi késést biztosít a számítási felmelegszik a tétlen használati időszakok után. Ezzel szemben a kiépített számítási szint az önálló adatbázisokra vagy a rugalmas készletekben található több adatbázisra optimalizált, magasabb átlagos használattal, amely nem biztosít semmilyen késleltetést a számítási felmelegítőben.

### <a name="scenarios-well-suited-for-serverless-compute"></a>A kiszolgáló nélküli számítási feladatokhoz jól illeszkedő forgatókönyvek

- Az egyidejű, előre nem látható használati mintákkal rendelkező önálló adatbázisok időbeli inaktivitási és alacsonyabb átlagos számítási kihasználtsággal rendelkeznek.
- A kiépített számítási rétegben lévő önálló adatbázisok, amelyek gyakran átméretezhetők, és az ügyfelek, akik szívesebben delegálják a szolgáltatásnak a számítási átméretezést.
- Az új, használati előzmények nélküli önálló adatbázisok, amelyekben a számítási méretezés nehéz, vagy a SQL Database-ben való üzembe helyezés előtt nem lehet megbecsülni.

### <a name="scenarios-well-suited-for-provisioned-compute"></a>A kiépített számítási feladatokhoz jól illeszkedő forgatókönyvek

- Az önálló adatbázisok esetében több rendszeres, kiszámítható használati minta és nagyobb átlagos számítási kihasználtság áll az idő múlásával.
- Azokat az adatbázisokat, amelyek nem tudják elviselni a teljesítménybeli kompromisszumokat, így gyakrabban fordulnak elő a memóriából, vagy késleltetik a szüneteltetett állapotból való automatikus folytatást.
- Több adatbázis időszakos, előre nem látható használati mintákkal, amelyek rugalmas készletekbe egyesíthetők a jobb ár-teljesítmény optimalizálása érdekében.

## <a name="comparison-with-provisioned-compute-tier"></a>Összehasonlítás a kiépített számítási szintjével

A következő táblázat összefoglalja a kiszolgáló nélküli számítási rétegek és a kiépített számítási szintek közötti különbségeket:

| | **Kiszolgáló nélküli számítás** | **Kiépített számítás** |
|:---|:---|:---|
|**Adatbázis-használati minta**| Időszakos, előre jelezhető használat kisebb átlagos számítási használattal az idő múlásával. |  A rendszeres használati minták nagyobb átlagos számítási kihasználtságot és rugalmas készleteket használó több adatbázist használnak.|
| **Teljesítmény-felügyeleti tevékenység** |Alacsonyabb|Jobb|
|**Számítási skálázás**|Automatikus|Manuális|
|**Számítási rugalmasság**|Alacsonyabb az inaktív időszakok után|Azonnali|
|**Számlázási részletesség**|Másodpercenként|/ óra|

## <a name="purchasing-model-and-service-tier"></a>Modell-és szolgáltatási szintek vásárlása

A kiszolgáló nélküli SQL Database jelenleg csak a virtuális mag beszerzési modell 5. generációs hardverének általános célú szintje támogatja.

## <a name="autoscaling"></a>Automatikus skálázás

### <a name="scaling-responsiveness"></a>Rugalmas méretezés

Általánosságban elmondható, hogy a kiszolgáló nélküli adatbázisok egy olyan gépen futnak, amely elegendő kapacitással rendelkezik ahhoz, hogy a maximális virtuális mag értékkel beállított korlátokon belül bármilyen mennyiségű számítási igény megszakítható legyen. Esetenként a terheléselosztás automatikusan történik, ha a gép néhány percen belül nem képes kielégíteni az erőforrás-igényt. Ha például az erőforrás igénye 4 virtuális mag, de csak 2 virtuális mag érhető el, akkor a 4 virtuális mag megadása előtt akár néhány percet is igénybe vehet. Az adatbázis online állapotban marad a terheléselosztás során, kivéve, ha a kapcsolat eldobásakor a művelet végén rövid ideig tart.

### <a name="memory-management"></a>Memória kezelése

A kiszolgáló nélküli adatbázisok memóriáját gyakrabban, mint a kiépített számítási adatbázisokhoz. Ez a viselkedés fontos a kiszolgáló nélküli költségek szabályozására, és hatással lehet a teljesítményre.

#### <a name="cache-reclamation"></a>Gyorsítótár-visszanyerés

A kiépített számítási adatbázisoktól eltérően az SQL-gyorsítótárból származó memóriát a rendszer a kiszolgáló nélküli adatbázisból állítja vissza, amikor a CPU vagy a gyorsítótár kihasználtsága alacsony.

- A gyorsítótár kihasználtsága akkor minősül alacsonynak, ha a legutóbb használt gyorsítótár-bejegyzések teljes mérete egy adott időtartam alatt egy küszöbérték alá esik.
- A gyorsítótár-újraindításkor a cél gyorsítótárának mérete fokozatosan csökken az előző méret töredékéért, és a visszaigénylés csak akkor folytatódik, ha a használat alacsony marad.
- A gyorsítótár-visszanyeréskor a kizárni kívánt gyorsítótár-bejegyzések kiválasztására szolgáló házirend ugyanaz a kiválasztási házirend, mint a kiépített számítási adatbázisok esetében, ha a memória nyomása magas.
- A gyorsítótár mérete soha nem csökken a minimális memória-korlát alatt, amelyet konfigurálhat a percben megadott minimális virtuális mag.

A kiszolgáló nélküli és a kiépített számítási adatbázisokban a gyorsítótár bejegyzései kizárható, ha az összes rendelkezésre álló memória használatban van.

#### <a name="cache-hydration"></a>Gyorsítótár-hidratáció

Az SQL-gyorsítótár növekszik, ahogy az adatok beolvasása a lemezről ugyanúgy történik, mint a kiépített adatbázisokkal azonos sebességgel. Ha az adatbázis foglalt, akkor a gyorsítótár mérete a maximális memória korlátja miatt nem lehet nagyobb.

## <a name="autopausing-and-autoresuming"></a>Az autoszüneteltetés és az autofolytatás

### <a name="autopausing"></a>Autoszüneteltetés

Az automatikus szüneteltetés akkor aktiválódik, ha az alábbi feltételek mindegyike teljesül az automatikus szüneteltetés késleltetésének időtartama alatt:

- Munkamenetek száma = 0
- CPU = 0 a felhasználói munkaterheléshez, amely a felhasználói készletben fut

Ha szükséges, a rendszer lehetőséget biztosít az autoszüneteltetés letiltására.

A következő szolgáltatások nem támogatják az autoszüneteltetést.  Azaz ha a következő funkciók bármelyikét használják, az adatbázis az adatbázis tétlenségének időtartamától függetlenül online marad:

- Geo-replikáció (aktív geo-replikáció és automatikus feladatátvételi csoportok).
- A biztonsági másolatok hosszú távú megőrzése (LTR).
- Az SQL-adatszinkronizálás során használt szinkronizálási adatbázis.

Az autoszüneteltetés átmenetileg megakadályozható néhány olyan szolgáltatás telepítése során, amelyekhez az adatbázisnak online állapotra van szüksége.  Ilyen esetekben a szolgáltatás frissítésének befejeződése után ismét engedélyezve lesz az autoszüneteltetés.

### <a name="autoresuming"></a>Autofolytatás

Az autofolytatás a következő esetekben aktiválódik, ha az alábbi feltételek bármelyike teljesül:

|Funkció|Trigger újraindítása|
|---|---|
|Hitelesítés és engedélyezés|Bejelentkezés|
|Fenyegetések észlelése|A veszélyforrások észlelési beállításainak engedélyezése/letiltása az adatbázis vagy a kiszolgáló szintjén.<br>A veszélyforrások észlelési beállításainak módosítása az adatbázis vagy a kiszolgáló szintjén.|
|Adatfelderítés és besorolás|Érzékenységi Címkék hozzáadása, módosítása, törlése vagy megtekintése|
|Naplózás|Naplózási rekordok megtekintése.<br>Naplózási házirend frissítése vagy megtekintése.|
|Adatmaszkolás|Az adatmaszkolási szabályok hozzáadása, módosítása, törlése vagy megtekintése|
|Transzparens adattitkosítás|Transzparens adattitkosítás állapotának vagy állapotának megtekintése|
|Lekérdezés (teljesítmény) adattár|A lekérdezési tároló beállításainak módosítása vagy megtekintése; automatikus hangolás|
|Autotuning|Automatikus finomhangolási javaslatok alkalmazása és ellenőrzése, például automatikus indexelés|
|Adatbázis másolása|Adatbázis létrehozása másolásként.<br>Exportálás BACPAC-fájlba.|
|SQL-adatszinkronizálás|Az olyan központi és tag adatbázisok közötti szinkronizálás, amelyek konfigurálható ütemterv szerint futnak, vagy manuálisan végeznek műveleteket|
|Bizonyos adatbázis-metaadatok módosítása|Új adatbázis-Címkék hozzáadása.<br>A maximális virtuális mag, a minimális virtuális mag vagy az automatikus szüneteltetés késleltetésének módosítása.|
|SQL Server Management Studio (SSMS)|A SSMS 18-as és újabb lekérdezési ablakának megnyitása a kiszolgáló bármely adatbázisa esetében folytatja az automatikusan szüneteltetett adatbázisokat ugyanazon a kiszolgálón. Ez a viselkedés nem fordul elő, ha a SSMS-verzió 17.9.1 az IntelliSense kikapcsolásával használja.|

Az automatikusan folytatott művelet az egyes szolgáltatási frissítések központi telepítése során is aktiválódik, amelyekhez az adatbázisnak online állapotra van szüksége.

### <a name="connectivity"></a>Kapcsolat

Ha egy kiszolgáló nélküli adatbázis szüneteltetve van, akkor az első bejelentkezés folytatja az adatbázist, és egy hibaüzenetet ad vissza, amely azt jelzi, hogy az adatbázis nem érhető el a 40613-as hibakódú kóddal. Az adatbázis újraindítása után a bejelentkezést újra meg kell próbálni a kapcsolat létrehozásához. A kapcsolódási újrapróbálkozási logikával rendelkező adatbázis-ügyfeleket nem szükséges módosítani.

### <a name="latency"></a>Késés

Az automatikus folytatáshoz és az automatikus szüneteltetéshez a kiszolgáló nélküli adatbázisok késése általában 1 percet vesz igénybe az automatikus folytatáshoz, és 1-10 percet az automatikus szüneteltetéshez.

## <a name="onboarding-into-serverless-compute-tier"></a>A kiszolgáló nélküli számítási szintek bevezetése

Ha egy új adatbázist hoz létre, vagy egy meglévő adatbázist kiszolgáló nélküli számítási rétegbe helyez át, a következő két lépéssel megegyező mintát kell létrehoznia egy új adatbázis létrehozásához a kiépített számítási szinten.

1. Adja meg a szolgáltatási cél nevét. A szolgáltatási cél a szolgáltatási szintet, a hardverek létrehozását és a maximális virtuális mag írja elő. A következő táblázat a szolgáltatási cél beállításait mutatja be:

   |Szolgáltatási cél neve|Szolgáltatási szint|Hardver létrehozása|Virtuális magok max. száma|
   |---|---|---|---|
   |GP_S_Gen5_1|Általános rendeltetés|Gen5|1|
   |GP_S_Gen5_2|Általános rendeltetés|Gen5|2|
   |GP_S_Gen5_4|Általános rendeltetés|Gen5|4|

2. Szükség esetén megadhatja a minimális virtuális mag és az automatikus szüneteltetés késleltetését is, ha módosítani szeretné az alapértelmezett értékeket. A következő táblázat a paraméterek elérhető értékeit tartalmazza.

   |Paraméter|Értékek megválasztása|Alapértelmezett érték|
   |---|---|---|---|
   |Virtuális magok min. száma|{0,5, 1, 2, 4} nem haladhatja meg a maximális virtuális mag|0,5 virtuális mag|
   |Automatikus szüneteltetés késleltetése|Minimum: 60 perc (1 óra)<br>Maximum: 10080 perc (7 nap)<br>Lépésekben 60 perc<br>Automatikus szüneteltetés letiltása:-1|60 perc|

> [!NOTE]
> A T-SQL használatával egy meglévő adatbázist kiszolgáló nélkülire helyezhet át, vagy megváltoztathatja a számítási méretet, de a Azure Portal vagy a PowerShell segítségével végezhető el.

### <a name="create-new-database-in-serverless-compute-tier"></a>Új adatbázis létrehozása kiszolgáló nélküli számítási szinten 

#### <a name="use-azure-portal"></a>Az Azure Portal használata

Lásd [: gyors útmutató: Hozzon létre egyetlen adatbázist Azure SQL Database a Azure Portal](sql-database-single-database-get-started.md)használatával.

#### <a name="use-powershell"></a>A PowerShell használata

Az alábbi példa egy új adatbázist hoz létre a kiszolgáló nélküli számítási szinten.  Ez a példa explicit módon megadja a minimális virtuális mag, a maximális virtuális mag és az automatikus szüneteltetés késleltetését.

```powershell
New-AzSqlDatabase `
  -ResourceGroupName $resourceGroupName `
  -ServerName $serverName `
  -DatabaseName $databaseName `
  -ComputeModel Serverless `
  -Edition GeneralPurpose `
  -ComputeGeneration Gen5 `
  -MinVcore 0.5 `
  -MaxVcore 2 `
  -AutoPauseDelayInMinutes 720
```

### <a name="move-database-from-provisioned-compute-tier-into-serverless-compute-tier"></a>Adatbázis áthelyezése a kiépített számítási rétegekből kiszolgáló nélküli számítási szintjére

#### <a name="use-powershell"></a>A PowerShell használata

Az alábbi példa egy adatbázist helyez át a kiépített számítási szintről a kiszolgáló nélküli számítási szintbe. Ez a példa explicit módon megadja a minimális virtuális mag, a maximális virtuális mag és az automatikus szüneteltetés késleltetését.

```powershell
Set-AzSqlDatabase
  -ResourceGroupName $resourceGroupName `
  -ServerName $serverName `
  -DatabaseName $databaseName `
  -Edition GeneralPurpose `
  -ComputeModel Serverless `
  -ComputeGeneration Gen5 `
  -MinVcore 1 `
  -MaxVcore 4 `
  -AutoPauseDelayInMinutes 1440
```

### <a name="move-database-from-serverless-compute-tier-into-provisioned-compute-tier"></a>Adatbázis áthelyezése kiszolgáló nélküli számítási szintjéről kiépített számítási szinten

A kiszolgáló nélküli adatbázisok a kiépített számítási rétegekbe helyezhetők, ugyanúgy, mint a kiépített számítási adatbázisok kiszolgáló nélküli számítási szintjére való áthelyezése.

## <a name="modifying-serverless-configuration"></a>Kiszolgáló nélküli konfiguráció módosítása

### <a name="maximum-vcores"></a>Virtuális magok maximális száma

#### <a name="use-powershell"></a>A PowerShell használata

A maximális virtuális mag módosítását a PowerShell [set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) parancsával végezheti el a `MaxVcore` argumentum használatával.

### <a name="minimum-vcores"></a>Virtuális magok minimális száma

#### <a name="use-powershell"></a>A PowerShell használata

A minimális virtuális mag módosítását a PowerShell [set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) parancsával végezheti el az `MinVcore` argumentum használatával.

### <a name="autopause-delay"></a>Automatikus szüneteltetés késleltetése

#### <a name="use-powershell"></a>A PowerShell használata

Az automatikus szüneteltetés késleltetésének módosítása a PowerShell [set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) parancsával történik a `AutoPauseDelayInMinutes` argumentum használatával.

## <a name="monitoring"></a>Figyelés

### <a name="resources-used-and-billed"></a>Felhasznált erőforrások és számlázás

A kiszolgáló nélküli adatbázisok erőforrásai az alkalmazáscsomag, az SQL-példány és a felhasználói erőforráskészlet-entitások beágyazásával vannak beágyazva.

#### <a name="app-package"></a>Alkalmazáscsomag

Az alkalmazáscsomag egy adatbázis külső erőforrás-kezelési határa, függetlenül attól, hogy az adatbázis kiszolgáló nélküli vagy kiépített számítási szinten van-e. Az alkalmazáscsomag tartalmazza azokat az SQL-példányokat és külső szolgáltatásokat, amelyek közösen tartalmazzák az adatbázis által SQL Database-ben használt összes felhasználói és rendszererőforrást. Külső szolgáltatások például az R és a teljes szöveges keresés. Az SQL-példány általában a teljes erőforrás-kihasználtságot uralja az alkalmazáscsomag között.

#### <a name="user-resource-pool"></a>Felhasználói erőforráskészlet

A felhasználói erőforráskészlet egy adatbázis belső erőforrás-kezelési határa, függetlenül attól, hogy az adatbázis kiszolgáló nélküli vagy kiépített számítási szinten van-e. A felhasználói erőforráskészlet hatóköre CPU és IO a DDL-lekérdezések által generált felhasználói számítási feladatokhoz, például LÉTREHOZÁSi és MÓDOSÍTÁSi, valamint DML-lekérdezések, például SELECT, INSERT, UPDATE és DELETE. Ezek a lekérdezések általában a kihasználtság legjelentősebb hányadát jelentik az alkalmazáscsomag keretében.

### <a name="metrics"></a>Mérőszámok

A kiszolgáló nélküli adatbázisok alkalmazáscsomag és felhasználói készlete erőforrás-használatának figyelésére szolgáló mérőszámok az alábbi táblázatban láthatók:

|Entitás|Metrika|Leírás|Mértékegységek|
|---|---|---|---|
|Alkalmazáscsomag|app_cpu_percent|Az alkalmazás által az alkalmazáshoz engedélyezett maximális virtuális mag képest használt virtuális mag százalékos aránya.|Százalék|
|Alkalmazáscsomag|app_cpu_billed|A jelentési időszak során az alkalmazás számára számlázott számítási mennyiség. Az ebben az időszakban fizetett összeg a metrika terméke és a virtuális mag egység ára. <br><br>A metrika értékeit a rendszer a felhasznált CPU és a másodpercenként felhasznált memória maximális számának időbeli összesítésével határozza meg. Ha a felhasznált mennyiség kevesebb, mint a minimum virtuális mag és a minimális memória által beállított minimális mennyiség, akkor a kiosztott minimális összegért kell fizetnie. Ha a CPU-t számlázási célokra szeretné összehasonlítani a memóriával, a memória a virtuális mag-egységekbe van normalizálva azáltal, hogy a memória mennyiségét GB-ban, virtuális mag 3 GB-onként átméretezni.|Virtuális mag másodpercben|
|Alkalmazáscsomag|app_memory_percent|Az alkalmazás által az alkalmazás számára engedélyezett maximális memóriához képest használt memória százalékos aránya.|Százalék|
|Felhasználói készlet|cpu_percent|A felhasználói munkaterhelés által a felhasználói munkaterhelés számára engedélyezett maximális virtuális mag képest felhasznált virtuális mag százalékos aránya.|Százalék|
|Felhasználói készlet|data_IO_percent|A felhasználói munkaterhelés által a felhasználói munkaterhelés számára engedélyezett maximális IOPS képest IOPS adatok százalékos aránya.|Százalék|
|Felhasználói készlet|log_IO_percent|A felhasználói munkaterhelés által használt log MB/s százalékos aránya a felhasználói munkaterhelés számára engedélyezett maximális log MB/s értékhez képest.|Százalék|
|Felhasználói készlet|workers_percent|A felhasználói munkaterhelés által a felhasználói munkaterhelés számára engedélyezett maximális munkaterheléshez képest használt munkavégzők százalékos aránya.|Százalék|
|Felhasználói készlet|sessions_percent|A felhasználói munkaterhelés által használt munkamenetek százalékos aránya a felhasználói munkaterhelés számára engedélyezett maximális munkamenetekhez képest.|Százalék|

### <a name="pause-and-resume-status"></a>Szüneteltetés és folytatás állapota

A Azure Portal az adatbázis állapota megjelenik a kiszolgáló áttekintés ablaktábláján, amely felsorolja a benne lévő adatbázisokat. Az adatbázis állapota az adatbázis áttekintés paneljén is megjelenik.

A következő PowerShell-paranccsal kérdezheti le egy adatbázis szüneteltetési és folytatási állapotát:

```powershell
Get-AzSqlDatabase `
  -ResourceGroupName $resourcegroupname `
  -ServerName $servername `
  -DatabaseName $databasename `
  | Select -ExpandProperty "Status"
```

## <a name="resource-limits"></a>Erőforráskorlátok

Az erőforrás-korlátokat lásd: [kiszolgáló nélküli számítási szintek](sql-database-vCore-resource-limits-single-databases.md#general-purpose-service-tier-for-serverless-compute).

## <a name="billing"></a>Számlázás

A számlázott számítások mennyisége a felhasznált CPU és a másodpercenként felhasznált memória maximális mennyisége. Ha a felhasznált CPU mennyisége és a felhasznált memória kevesebb, mint az egyesek számára kiépített minimális mennyiség, akkor a kiosztott mennyiség számlázásra kerül. Ha a CPU-t számlázási célokra szeretné összehasonlítani a memóriával, a memória a virtuális mag-egységekbe van normalizálva azáltal, hogy a memória mennyiségét GB-ban, virtuális mag 3 GB-onként átméretezni.

- **Számlázott erőforrás**: CPU és memória
- **Számlázott összeg**: virtuális mag egység ára * Max (min virtuális mag, virtuális mag felhasznált, minimális memória gb * 1/3, memória GB használatban * 1/3) 
- **Számlázási gyakoriság**: Másodpercenként

A virtuális mag egységenkénti díja a virtuális mag/másodpercben. Egy adott régióban az adott egység áraira vonatkozó [Azure SQL Database díjszabási oldalán](https://azure.microsoft.com/pricing/details/sql-database/single/) talál további információt.

A számlázott számítások mennyiségét a következő metrika teszi elérhetővé:

- **Metrika**: App_cpu_billed (virtuális mag másodperc)
- **Definíció**: Max (min. virtuális mag, felhasznált virtuális mag, minimális memória gb * 1/3, memória GB használatban * 1/3)
- **Jelentéskészítés gyakorisága**: Percenként

Ezt a mennyiséget másodpercenként számítjuk ki, és 1 percenként összesítjük.

Vegyünk egy 1 perces virtuális mag és 4 maximális virtuális mag konfigurált kiszolgáló nélküli adatbázist.  Ez körülbelül 3 GB-os memória-és 12 GB-os maximális memória-értéknek felel meg.  Tegyük fel, hogy az automatikus szüneteltetés késleltetése 6 óra, az adatbázis-munkaterhelés pedig aktív a 24 órás időszak első 2 órájában, ellenkező esetben inaktív.    

Ebben az esetben az adatbázist a számítás és a tárolás után számítjuk fel az első 8 órában.  Annak ellenére, hogy az adatbázis inaktív a második óra után, a rendszer továbbra is kiszámlázza a számítást a következő 6 órában az adatbázis online állapotában kiosztott minimális számítási kapacitás alapján.  A rendszer csak a 24 órás időszak hátralévő részében számlázza a tárterületet, amíg az adatbázis szüneteltetve van.

Pontosabban, a példában szereplő számítási számla kiszámítása a következőképpen történik:

|Időtartam|másodpercenként használt virtuális mag|GB használt másodpercenként|Számítási dimenzió számlázása|Virtuális mag másodpercben elszámolt időtartam|
|---|---|---|---|---|
|0:00-1:00|4|9|használt virtuális mag|4 vCores * 3600 seconds = 14400 vCore seconds|
|1:00-2:00|1|12|Felhasznált memória|12 GB * 1/3 * 3600 seconds = 14400 vCore seconds|
|2:00-8:00|0|0|Minimális memória kiépítve|3 GB * 1/3 * 21600 seconds = 21600 vCore seconds|
|8:00-24:00|0|0|A felfüggesztett számítási díj nem számítható fel|0 virtuális mag másodperc|
|24 órán át számlázott virtuális mag-másodpercek összesen||||50400 virtuális mag másodperc|

Tegyük fel, hogy a számítási egység ára $0.000073/virtuális mag/Second.  Ezt követően a 24 órás időszakra kiszámított számítás a számítási egység árának és a virtuális mag másodpercben mért kiszámlázásának szorzata: $0.000073/virtuális mag/Second * 50400 virtuális mag másodperc = $3,68

## <a name="available-regions"></a>Elérhető régiók

A kiszolgáló nélküli számítási csomag világszerte elérhető, kivéve a következő régiókat: Közép-Ausztrália, Kelet-Kína, Észak-Kína, Dél-Franciaország, Közép-Németország, Kelet-Németország, Nyugat-India, Dél-Korea, Dél-Afrika, Egyesült Királyság északi régiója, Egyesült Királyság déli régiója, Egyesült Királyság nyugati régiója és az USA nyugati középső régiója.

## <a name="next-steps"></a>További lépések

- Első lépésként tekintse [meg a gyors útmutató: Hozzon létre egyetlen adatbázist Azure SQL Database a Azure Portal](sql-database-single-database-get-started.md)használatával.
- Az erőforrások korlátaival kapcsolatban lásd: [kiszolgáló nélküli számítási keret erőforrás-korlátai](sql-database-vCore-resource-limits-single-databases.md#general-purpose-service-tier-for-serverless-compute).
