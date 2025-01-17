---
title: Avere vFXT – Azure-támogatás engedélyezése
description: Az Azure-ban Avere vFXT származó feltöltések támogatás engedélyezése
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: fe096b2e2a75cc89e3ce5ef905d8e4c347cc153a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60409838"
---
# <a name="enable-support-uploads"></a>Támogatási feltöltések engedélyezése

Az Azure-hoz a Avere vFXT automatikusan feltöltheti a fürttel kapcsolatos támogatási adatok. Ezek lehetővé teszik a támogatási személyzet feltöltések adja meg az ajánlott ügyfélszolgálat.

## <a name="steps-to-enable-uploads"></a>További lépések elvégzésével feltöltések

Kövesse az alábbi lépéseket a Avere Vezérlőpult támogatás aktiválásához. (Olvasás [a vFXT fürt eléréséhez](avere-vfxt-cluster-gui.md) megismeréséhez nyissa meg a Vezérlőpultot Avere.)

1. Keresse meg a **beállítások** a felső fülön.
1. Kattintson a **támogatási** a bal oldali hivatkozásra, és fogadja el az adatvédelmi nyilatkozatát.

   ![Képernyőfelvétel: a Avere Vezérlőpulton, és a megerősítés gombra az előugró ablakban fogadja el az adatvédelmi szabályzatot](media/avere-vfxt-privacy-policy.png)

1. Kattintson a háromszögre balra **Customer Info** a szakasz kibontásához.
1. Kattintson a **Revalidate feltöltési információk** gombra.
1. Állítsa be a fürt támogatási nevét **egyedi fürtnév** – ellenőrizze, hogy a fürt a támogató személyzet egyedileg azonosítja.
1. Jelölje be a **statisztikák figyelése**, **általános információkat feltöltése**, és **összeomlási adatokat feltölteni**.
1. Kattintson a **Submit** (Küldés) gombra.

   ![Képernyőkép, amely tartalmazza a customer info szakaszban támogatási beállítások oldal befejeződött](media/avere-vfxt-support-info.png)

1. Kattintson a háromszögre balra **biztonságos proaktív támogatási (Szervizcsomagok)** a szakasz kibontásához.
1. Jelölje be a **Szervizcsomagok hivatkozás engedélyezése**.
1. Kattintson a **Submit** (Küldés) gombra.

   ![Befejezett proaktív támogatja biztonságos szakaszban támogatási beállítások oldalon tartalmazó képernyőképe](media/avere-vfxt-support-sps.png)

## <a name="next-steps"></a>További lépések

Ha kell hozzáadnia a helyszíni vagy felhőalapú adattároló rendszer a fürthöz, kövesse a meglévő [konfigurálta a tárterületet](avere-vfxt-add-storage.md). 

Ha készen áll a fürt, olvassa el az ügyfelek csatolása [a Avere vFXT fürt csatlakoztatási](avere-vfxt-mount-clients.md).