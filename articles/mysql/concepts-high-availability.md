---
title: Az Azure Database for MySQL magas rendelkezésre állású fogalmak
description: Ez a témakör nyújt információkat a magas rendelkezésre állás, ha az Azure Database for MySQL használatával
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 055727695bfa1ce8a6bb160a7e071c2a161afb3b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60837829"
---
# <a name="high-availability-concepts-in-azure-database-for-mysql"></a>Az Azure Database for MySQL magas rendelkezésre állású fogalmak
Az Azure Database for MySQL-szolgáltatás garantált magas szintű rendelkezésre állást biztosít. A pénzügyi felelősséggel vállalt garantált szolgáltatási szinttel (SLA) 99,99 %-os általános elérhetővé tételtől. Gyakorlatilag alkalmazások egyike sem állásidő a szolgáltatás használata esetén.

## <a name="high-availability"></a>Magas rendelkezésre állás
A magas rendelkezésre ÁLLÁS modell feladatátvételt a beépített mechanizmusok alapján egy csomópont-szintű megszakadása esetén. Egy csomópont-szintű megszakítás akkor fordulhat elő, hardverhiba miatt, vagy a szolgáltatások üzembe helyezéséhez.

Mindig egy Azure Database for MySQL-adatbázis-kiszolgáló végrehajtott módosítások történnek, egy tranzakció környezetében. A változásokat elszámolni szinkron módon történik az Azure storage-ban Ha a tranzakció véglegesítése. Ha egy csomópont-szintű megszakítás történik, az adatbázis-kiszolgáló automatikusan létrehoz egy új csomópont, és adattárolás csatolja az új csomópont. Minden aktív kapcsolat a rendszer elveti és megszakít tranzakciók nem kerülnek.

## <a name="application-retry-logic-is-essential"></a>Alkalmazás újrapróbálkozási logikát elengedhetetlen.
Fontos, hogy a MySQL-adatbázis-alkalmazások észlelése, és ismételje meg a beépített kapcsolatok eldobott, és nem sikerült a tranzakció. Az alkalmazás újrapróbálkozik, ha az alkalmazás által létesített kapcsolatban a rendszer transzparens módon átirányítja az újonnan létrehozott példány, amely átveszi a hibás szolgáltatáspéldányt a.

Belsőleg az Azure-ban, az átjáró segítségével átirányítja a kapcsolatot az új példány. Alapján megszakadásának a feladatátvétel teljes folyamat általában több tíz másodpercet vesz igénybe. Az átirányítási belsőleg kezeli az átjárót, mert a külső kapcsolati karakterlánc ugyanaz marad, az ügyfélalkalmazások számára.

## <a name="scaling-up-or-down"></a>Felfelé és lefelé skálázás
A magas rendelkezésre ÁLLÁSÚ modell, Azure Database for MySQL-méretezve, felfelé vagy lefelé hasonlóan egy új példány a megadott méretű jön létre. A meglévő adatok tárolási leválasztása az eredeti példányban, és az új példány csatlakozik.

A skálázási művelet során az adatbázis-kapcsolatok romlását történik. Az ügyfélalkalmazások le vannak választva, és nyissa meg a nem véglegesített tranzakciót megszakították. Az ügyfélalkalmazás újrapróbálja a kapcsolódást, vagy egy új kapcsolatot, miután az átjáró irányítja a kapcsolat az újonnan méretű példánnyal. 

## <a name="next-steps"></a>További lépések
- Ismerje meg [átmeneti kapcsolati hibák kezelése](concepts-connectivity.md)
- Ismerje meg, hogyan [olvasható replikákat az adatok replikálása](howto-read-replicas-portal.md)
