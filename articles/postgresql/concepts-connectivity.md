---
title: Átmeneti kapcsolati hibákat kezelése az Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Ismerje meg, hogy átmeneti kapcsolati hibáinak kezelése az Azure Database for PostgreSQL – egyetlen kiszolgáló.
keywords: postgresql-kapcsolat, kapcsolati karakterlánc, kapcsolódási problémák, átmeneti hiba, kapcsolódási hiba
author: jan-eng
ms.author: janeng
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: ea90de612dcfb2559b29fbffce8306278beb45b9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65073515"
---
# <a name="handling-transient-connectivity-errors-for-azure-database-for-postgresql---single-server"></a>Átmeneti kapcsolati hibák kezelése az Azure Database for PostgreSQL – egyetlen kiszolgáló

Ez a cikk ismerteti a PostgreSQL-hez készült Azure-adatbázishoz csatlakozó átmeneti hibák kezelésére.

## <a name="transient-errors"></a>Átmeneti hibák

Átmeneti hiba, más néven egy átmeneti hiba a hibát, amely magától megoldódik. Ezek a hibák leggyakrabban az adatbázis-kiszolgálóhoz, az eldobott kapcsolatként jegyzékfájl. Emellett új kapcsolatokat a kiszolgáló nem lehet megnyitni. Átmeneti hibák fordulhat elő, például ha a hardver- vagy hálózati hiba történik. A másik ok lehet egy PaaS szolgáltatás, amely a vezetjük be egy új verziója. A legtöbb ezeket az eseményeket a rendszer által automatikusan üzemeltetésbiztonságának a kisebb, mint 60 másodperc. A felhőbeli alkalmazások tervezésére és fejlesztésére vonatkozó ajánlott eljárás az átmeneti hibák várható. Tegyük fel, akkor fordulhat elő, valamelyik összetevő a tetszőleges időpontban, és rendelkezik a megfelelő logikai az ilyen helyzetek kezelése érdekében.

## <a name="handling-transient-errors"></a>Átmeneti hibák kezelése

Használja az újrapróbálkozási logika átmeneti hibák kell kezelni. Olyan helyzetekben is figyelembe kell venni:

* Hiba történik, ha a kapcsolat megnyitása
* Tétlen kapcsolatok a kiszolgálói oldalon eseményértesítése eldobva. Amikor megpróbál egy parancs nem hajtható végre
* Jelenleg éppen futó parancs aktív kapcsolat megszakad.

Az első és második eset nagyon viszonylag egyszerű olyan kezelésére. Próbálja meg újra a kapcsolat megnyitásához. A sikeres legyen, az átmeneti hiba lett problémák elhárításáról a rendszer. Az Azure Database for postgresql-hez ismét használhatja. Javasoljuk, hogy a vár, mielőtt újra próbálkozna a kapcsolatot. Biztonsági, ha a kezdeti újrapróbálkozások sikertelenek. Ezzel a módszerrel a rendszer a hiba lépne fel, hogy elérhető összes erőforrás használhatja. Egy jó minta kövesse a következő:

* Várjon, amíg az első újrapróbálkozás előtt 5 másodperc.
* Minden egyes következő újra, a növekedés Várakozás exponenciálisan növekszik, legfeljebb 60 másodperc.
* Ekkor az alkalmazás figyelembe veszi a művelet sikertelen volt az újrapróbálkozások maximális számának beállítása.

Ha nem sikerül egy aktív tranzakció-kapcsolattal, nehezebb megfelelően kezeli a helyreállítás. Két eset létezik: Ha a tranzakció volt az írásvédett jellegűek, biztonságos, nyissa meg újra a kapcsolatot, és próbálja meg újra a tranzakciót. Ha azonban a tranzakció is az adatbázisba írt, ha meg kell határoznia, ha a tranzakció vissza lett állítva, vagy ha a feladat végül sikerült előtt a átmeneti hiba történt. Ebben az esetben előfordulhat, hogy nem csupán kapott a véglegesítési nyugtázása az adatbázis-kiszolgáló.

Ennek egyik módja, hogy az ügyfélen, az újrapróbálkozások kezelésére használt egyedi azonosító létrehozása. Ezt az egyedi Azonosítót a tranzakció részeként adja meg a kiszolgálón, és tárolja az oszlopok egy egyedi korlátozás. Így biztonságosan próbálja újra a tranzakciót. Akkor lesz sikeres, ha az előző tranzakció vissza lett állítva, és a generált ügyfél egyedi azonosítója még nem létezik a rendszerben. Nem fog működni, ha egyedi azonosítója korábban tárolta, mert a korábbi tranzakció sikeresen befejeződött, amely azt jelzi, hogy egy duplikált kulcs megsértése.

Amikor a program az Azure Database for postgresql-hez külső közbenső keresztül kommunikál, kérje meg a gyártó, hogy tartalmaz-e a közbenső szoftver újrapróbálkozási logika átmeneti hibák esetén.

Ellenőrizze, hogy tesztelje, újrapróbálkozási logika. Például próbálja meg a kód végrehajtását közben be-vagy leskálázás számítási erőforrásait, Azure Database for PostgreSQL-kiszolgáló. Az alkalmazás kezelje a rövid idejű leállást gond nélkül ez a művelet során észlelt.

## <a name="next-steps"></a>További lépések

* [Az Azure Database for PostgreSQL-hez való csatlakozás hibáinak elhárítása](howto-troubleshoot-common-connection-issues.md)
