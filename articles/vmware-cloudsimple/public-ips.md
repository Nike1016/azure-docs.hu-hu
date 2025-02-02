---
title: Azure VMware-megoldás CloudSimple – nyilvános IP-címek lefoglalása
description: Ismerteti, hogyan lehet nyilvános IP-címeket lefoglalni a virtuális gépek számára a saját felhőalapú környezetben
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 12a6af73dc0abc9598184f6c83e9d7652973a99e
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69544447"
---
# <a name="allocate-public-ip-addresses-for-private-cloud-environment"></a>Nyilvános IP-címek lefoglalása a saját felhőalapú környezethez

Nyissa meg a nyilvános IP-címek lapot a hálózat lapon a saját felhőalapú környezetében lévő virtuális gépek nyilvános IP-címeinek lefoglalásához.

1. [Nyissa meg a CloudSimple](monitor-activity.md) -portált, és válassza a **hálózat** lehetőséget az oldalsó menüben.
2. Válassza a **nyilvános IP**-címek lehetőséget.
3. Kattintson az **új nyilvános IP-cím**elemre.

    ![Nyilvános IP-címek lap](media/public-ips-page.png)

4. Adja meg az IP-cím bejegyzését azonosító nevet.
5. Tartsa meg az alapértelmezett helyet.
6. Ha szükséges, használja a csúszkát az Üresjárati időkorlát módosításához.
7. Adja meg azt a helyi IP-címet, amelyhez nyilvános IP-címet szeretne hozzárendelni.
8. Adjon meg egy társított DNS-nevet.
9. Kattintson a **Submit** (Küldés) gombra.

![Nyilvános IP-címek lefoglalása](media/network-public-ip-allocate.png)

A nyilvános IP-cím lefoglalásának feladata megkezdődik. A feladat állapotát a **tevékenység > feladatok** oldalon tekintheti meg. A foglalás befejezésekor az új bejegyzés megjelenik a nyilvános IP-címek lapon.
