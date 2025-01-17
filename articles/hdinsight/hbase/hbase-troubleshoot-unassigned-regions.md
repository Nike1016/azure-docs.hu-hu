---
title: A régióbeli kiszolgálókkal kapcsolatos problémák az Azure HDInsight
description: A régióbeli kiszolgálókkal kapcsolatos problémák az Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/07/2019
ms.openlocfilehash: e75f2fdd0530b92e8c8405b74c2a364ff9e9e28e
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68935431"
---
# <a name="issues-with-region-servers-in-azure-hdinsight"></a>A régióbeli kiszolgálókkal kapcsolatos problémák az Azure HDInsight

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="scenario-unassigned-regions"></a>Forgatókönyv: Nem hozzárendelt régiók

### <a name="issue"></a>Probléma

A parancs `hbase hbck` futtatásakor a következőhöz hasonló hibaüzenet jelenik meg:

```
multiple regions being unassigned or holes in the chain of regions
```

Az Apache HBase Master felhasználói felületén láthatja, hogy a régiók száma az összes régió-kiszolgáló között kiegyensúlyozatlan lesz.

### <a name="cause"></a>Ok

A lyukak az offline régiók eredményei lehetnek.

### <a name="resolution"></a>Megoldás:

Javítsa ki a hozzárendeléseket. Az alábbi lépések végrehajtásával visszahelyezheti a hozzá nem rendelt régiókat a normál állapotba:

1. Jelentkezzen be a HDInsight HBase-fürtbe SSH használatával.

1. Futtassa `hbase zkcli` a parancsot a Zookeeper-rendszerhéjhoz való kapcsolódáshoz.

1. Futtatás `rmr /hbase/regions-in-transition` vagy`rmr /hbase-unsecure/regions-in-transition` parancs.

1. Lépjen ki a Zookeeper- `exit` rendszerhéjból a paranccsal.

1. Nyissa meg a Ambari felhasználói felületét, és indítsa újra az aktív HBase Master szolgáltatást a Ambari.

1. Futtassa `hbase hbck` újra a parancsot (további beállítások nélkül). Ellenőrizze a kimenetet, és győződjön meg arról, hogy az összes régió hozzá van rendelve.

---

## <a name="scenario-dead-region-servers"></a>Forgatókönyv: Holt régió kiszolgálói

### <a name="issue"></a>Probléma

A régió-kiszolgálók nem indulnak el.

### <a name="cause"></a>Ok

Több felosztó WAL-könyvtár.

1. Aktuális Wals listájának beolvasása: `hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out`.

1. Vizsgálja meg `wals.out` a fájlt. Ha túl sok felosztó könyvtár van (a *-felosztástól kezdve), akkor a régió-kiszolgáló valószínűleg sikertelen lesz a könyvtárak miatt.

### <a name="resolution"></a>Megoldás:

1. A HBase leállítása a Ambari-portálról.

1. Végrehajtás `hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out` a WALs friss listájának lekéréséhez.

1. Helyezze át a *-felosztási könyvtárakat egy ideiglenes mappába `splitWAL`, és törölje a *-felosztási könyvtárakat.

1. Futtassa `hbase zkcli` a parancsot a Zookeeper-rendszerhéjhoz való kapcsolódáshoz.

1. Végrehajtás `rmr /hbase-unsecure/splitWAL`.

1. Indítsa újra a HBase szolgáltatást.

## <a name="next-steps"></a>További lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Kapcsolódjon [@AzureSupport](https://twitter.com/azuresupport) a-a hivatalos Microsoft Azure fiókhoz a felhasználói élmény javítása érdekében. Az Azure-Közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a Azure Portaltól [](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
