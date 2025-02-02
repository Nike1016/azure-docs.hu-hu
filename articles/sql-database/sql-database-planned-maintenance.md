---
title: Az Azure karbantartási eseményeinek tervezése – Azure SQL Database | Microsoft Docs
description: Megtudhatja, hogyan készítheti elő a tervezett karbantartási eseményeket a Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: carlrab
ms.date: 01/30/2019
ms.openlocfilehash: 1bb88d6f74ab4b93e226fe8630f07f0a96f4ba47
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567048"
---
# <a name="planning-for-azure-maintenance-events-in-azure-sql-database"></a>Azure-karbantartási események tervezése Azure SQL Database

Ismerje meg, hogyan készítheti elő a tervezett karbantartási eseményeket az Azure SQL Database-ben.

## <a name="what-is-a-planned-maintenance-event"></a>Mi a tervezett karbantartási esemény?

Az Azure SQL DB minden adatbázis esetében az adatbázis-replikák kvórumát tárolja, ahol az egyik replika az elsődleges. Az elsődleges replika minden esetben online karbantartásnak kell lennie, és legalább egy másodlagos replikának kifogástalannak kell lennie. A tervezett karbantartás során az adatbázis Kvórumának tagjai egy időben offline állapotba kerülnek, azzal a szándékkal, hogy az ügyfél leállásának hiányában egyetlen válasz elsődleges replika és legalább egy másodlagos replika online állapotban van. Ha az elsődleges replikát offline állapotba kell helyezni, egy újrakonfigurálási/feladatátvételi folyamat fog történni, amelyben egy másodlagos replika lesz az új elsődleges.  

## <a name="what-to-expect-during-a-planned-maintenance-event"></a>Mire számíthat egy tervezett karbantartási esemény során

Az újrakonfigurálások/feladatátvételek általában 30 másodpercen belül befejeződik – az átlag 8 másodperc. Ha már csatlakoztatva van, az alkalmazásnak újra kapcsolódnia kell az adatbázisa új elsődleges másodpéldányához. Ha új kapcsolatra van kísérlet, miközben az adatbázis újrakonfigurálást végez az új elsődleges replika online állapotba lépését megelőzően, akkor a 40613-es hiba jelenik meg (az adatbázis nem érhető el): A (z) {servername} kiszolgáló {databasename} adatbázisa jelenleg nem érhető el. Próbálja megismételni a kapcsolatokat később. " Ha az adatbázis hosszú ideig futó lekérdezéssel rendelkezik, akkor a lekérdezés az újrakonfigurálás során megszakad, és újra kell indítani.

## <a name="retry-logic"></a>Újrapróbálkozási logika

A felhőalapú adatbázis-szolgáltatáshoz csatlakozó összes ügyfél-éles alkalmazásnak robusztus kapcsolati [](sql-database-connectivity-issues.md#retry-logic-for-transient-errors)újrapróbálkozási logikát kell létrehoznia. Ez segít enyhíteni ezeket a helyzeteket, és általában a végfelhasználók számára transzparens módon végezze el a hibákat.

## <a name="frequency"></a>Gyakoriság

Átlagosan 1,7 tervezett karbantartási esemény történik havonta.

## <a name="resource-health"></a>Resource Health

Ha az SQL-adatbázis bejelentkezési hibákat észlel, tekintse meg az aktuális állapot [Azure Portal](https://portal.azure.com) [Resource Health](../service-health/resource-health-overview.md#get-started) ablakát. Az állapot előzményei szakasz az egyes események állásidői okát tartalmazza (ha elérhető).


## <a name="next-steps"></a>További lépések

- További információ a [](sql-database-resource-health.md) SQL Database Resource Healthről
- Az újrapróbálkozási logikával kapcsolatos további információkért lásd az [átmeneti hibákra vonatkozó](sql-database-connectivity-issues.md#retry-logic-for-transient-errors) újrapróbálkozási logikát.
