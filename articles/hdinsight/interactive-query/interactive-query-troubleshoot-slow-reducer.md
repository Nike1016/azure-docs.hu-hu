---
title: A redukáló lassú az Azure HDInsight
description: Az Azure HDInsight lassú az esetleges adattorzítás
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/30/2019
ms.openlocfilehash: 5fe1b2c720bbc55e8e245f9592755e046e0b04a3
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68968435"
---
# <a name="scenario-reducer-is-slow-in-azure-hdinsight"></a>Forgatókönyv: A redukáló lassú az Azure HDInsight

Ez a cikk az interaktív lekérdezési összetevők Azure HDInsight-fürtökben való használatakor felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Egy lekérdezés futtatásakor, `insert into table1 partition(a,b) select a,b,c from table2` például a lekérdezési terv elindít egy több szűkítőt, de az egyes partíciók adatainak egyetlen szűkítővel kell rendelkezniük. Ez azt eredményezi, hogy a lekérdezés lassú, mint a legnagyobb partíció-csökkentő által elkészített idő.

## <a name="cause"></a>Ok

Nyissa meg a Beeline értéket, és `hive.optimize.sort.dynamic.partition`ellenőrizze a készlet értékét. [](../hadoop/apache-hadoop-use-hive-beeline.md)

A változó értékét igaz/hamis értékre kell állítani az adattermészet alapján.

Ha a bemeneti táblában lévő partíciók kevesebbek (kevesebb mint 10), így a kimeneti partíciók száma, és a változó `true`értéke, ez az adatok globális rendezését és írását eredményezi a partíciók egy-egy csökkentőjának használatával. Még akkor is, ha a rendelkezésre álló szűkítők száma nagyobb, az Adateltérés miatt néhány szűkítőt is el lehet érni, és a maximális párhuzamosság nem érhető el. Ha a értékre `false`módosul, több szűkítő is kezelhet egyetlen partíciót, és több kisebb fájlt is kiírja a rendszer, ami gyorsabb beszúrást eredményez. Ez a további lekérdezésekre is hatással lehet, azonban kisebb fájlok jelenléte miatt.

A érték `true` azt indokolja, hogy a partíciók száma nagyobb legyen, és az adatmennyiség ne legyen elferdítve. Ebben az esetben a térképi fázis eredményét úgy kell kiírni, hogy az egyes partíciókat egyetlen redukáló kezelje, ami a későbbi lekérdezési teljesítményt eredményezi.

## <a name="resolution"></a>Megoldás:

1. Próbálja megismételni az adatparticionálást több partícióra való normalizálás érdekében.

1. Ha #1 nem lehetséges, állítsa a konfiguráció értékét false értékre a Beeline-munkamenetben, majd próbálja megismételni a lekérdezést. `set hive.optimize.sort.dynamic.partition=false`. Az érték false-ra állítása a fürt szintjén nem ajánlott. Az értéke `true` optimális, és szükség szerint a paramétert az adatmennyiség és a lekérdezés jellege alapján állítja be.

## <a name="next-steps"></a>További lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Csatlakozás az Azure-Közösség a megfelelő erőforrásokhoz való csatlakoztatásával – a hivatalos Microsoft Azure fiókkal – a felhasználói élmény javítása érdekében: válaszok, támogatás és szakértők. [@AzureSupport](https://twitter.com/azuresupport)

* Ha további segítségre van szüksége, támogatási kérést küldhet a Azure Portaltól [](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
