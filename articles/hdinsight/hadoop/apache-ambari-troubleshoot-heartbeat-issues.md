---
title: Az Apache Ambari szívverési problémái az Azure HDInsight
description: Az Apache Ambari szívverésével kapcsolatos problémák különféle okai az Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/02/2019
ms.openlocfilehash: a74e826e9a4725c9b689dc97101b00615771d4d1
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/05/2019
ms.locfileid: "68781399"
---
# <a name="apache-ambari-heartbeat-issues-in-azure-hdinsight"></a>Az Apache Ambari szívverési problémái az Azure HDInsight

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="scenario-high-cpu-utilization"></a>Forgatókönyv: Magas CPU-kihasználtság

### <a name="issue"></a>Probléma

A Ambari-ügynök magas CPU-kihasználtsággal rendelkezik, ami a Ambari felhasználói felületének riasztásait eredményezi, hogy egyes csomópontok esetén a Ambari-ügynök szívverése megszakad.

### <a name="cause"></a>Ok

A különböző ambari hibák miatt előfordulhat, hogy a ambari-ügynöknek magas (100) százalékos CPU-kihasználtsága lehet.

### <a name="resolution"></a>Megoldás:

1. Azonosítsa a ambari-ügynök folyamat-AZONOSÍTÓját (PID):

    ```bash
    ps -ef | grep ambari_agent
    ```

1. Ezután futtassa a következő parancsot a processzorhasználat megjelenítéséhez:

    ```bash
    top -p <ambari-agent-pid>
    ```

1. A probléma megoldásához indítsa újra a ambari-Agent ügynököt:

    ```bash
    service ambari-agent restart
    ```

1. Ha az újraindítás nem működik, öld meg a ambari folyamatot, majd indítsa el:

    ```bash
    kill -9 <ambari-agent-pid>
    service ambari-agent start
    ```

---

## <a name="scenario-ambari-agent-not-started"></a>Forgatókönyv: A Ambari-ügynök nem indult el

### <a name="issue"></a>Probléma

A Ambari-ügynök nem indult el, amely a Ambari felhasználói felületén riasztásokat eredményez, hogy egyes csomópontok esetében a Ambari-ügynök szívverése megszakad.

### <a name="cause"></a>Ok

A riasztások oka a Ambari-ügynök nem fut.

### <a name="resolution"></a>Megoldás:

1. Ambari-ügynök állapotának megerősítése:

    ```bash
    service ambari-agent status
    ```

1. Ellenőrizze, hogy futnak-e a feladatátvételi vezérlő szolgáltatásai:

    ```bash
    ps -ef | grep failover
    ```

    Ha a feladatátvételi vezérlő szolgáltatásai nem futnak, valószínűleg egy probléma okozza, hogy a hdinsight-ügynök nem indítja el a feladatátvételi vezérlőt. A fájlból olvassa be a `/var/log/hdinsight-agent/hdinsight-agent.out` hdinsight-ügynök naplóját.

---

## <a name="next-steps"></a>További lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Csatlakozás az Azure-Közösség a megfelelő erőforrásokhoz való csatlakoztatásával – a hivatalos Microsoft Azure fiókkal – a felhasználói élmény javítása érdekében: válaszok, támogatás és szakértők. [@AzureSupport](https://twitter.com/azuresupport)

* Ha további segítségre van szüksége, támogatási kérést küldhet a Azure Portaltól [](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
