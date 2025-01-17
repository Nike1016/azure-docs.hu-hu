---
title: A fürt létrehozása nem sikerült, mert nem áll rendelkezésre elegendő tartalék tartomány a régióban az Azure HDInsight
description: A fürt létrehozása nem sikerült, mert nem áll rendelkezésre elegendő tartalék tartomány a régióban az Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/09/2019
ms.openlocfilehash: 945bd6aff5bba96f8b72570442d3b1dcd035cb62
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2019
ms.locfileid: "68947707"
---
# <a name="scenario-cluster-creation-failed-due-to-not-sufficient-fault-domains-in-region-in-azure-hdinsight"></a>Forgatókönyv: `not sufficient fault domains in region` A fürt létrehozása az Azure HDInsight miatt nem sikerült

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

A rendszer a következőhöz `not sufficient fault domains in region` hasonló hibaüzenetet kap, amikor Apache Kafka fürtöt próbál létrehozni.

## <a name="cause"></a>Ok

A tartalék tartomány az alapul szolgáló hardver logikai csoportosítása egy Azure-adatközpontban. Mindegyik tartalék tartomány közös áramforrással és hálózati kapcsolóval rendelkezik. A HDInsight-fürtön belül a csomópontokat implementáló virtuális gépek és felügyelt lemezek ezek között a tartalék tartományok között vannak elosztva. Ez az architektúra csökkenti a fizikai hardverhibák lehetséges hatását.

Mindegyik Azure-régió meghatározott számú tartalék tartománnyal rendelkezik. A tartományok listáját és a bennük található tartalék tartományok számát a [rendelkezésre állási csoportokról](../../virtual-machines/windows/manage-availability.md)szóló dokumentációban találja.

A HDInsight-ben a Kafka-fürtöket legalább három tartalék tartománnyal rendelkező régióban kell kiépíteni.

## <a name="resolution"></a>Megoldás:

Ha a fürtöt létrehozni kívánó régió nem rendelkezik megfelelő tartalék tartományokkal, akkor a fürt kiépítés engedélyezéséhez akkor is, ha nincs három tartalék tartomány.

## <a name="next-steps"></a>További lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Kapcsolódjon [@AzureSupport](https://twitter.com/azuresupport) a-a hivatalos Microsoft Azure fiókhoz a felhasználói élmény javítása érdekében. Az Azure-Közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a Azure Portaltól [](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
