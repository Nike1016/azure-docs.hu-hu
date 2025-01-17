---
title: 'Azure Backup: Azure Backup védett munkaterhelések figyelése'
description: Azure Backup munkaterhelések figyelése Azure Portal használatával
author: pvrk
manager: shivamg
keywords: Azure Backup; Riasztások
ms.service: backup
ms.topic: conceptual
ms.date: 03/05/2019
ms.author: pullabhk
ms.assetid: 86ebeb03-f5fa-4794-8a5f-aa5cbbf68a81
ms.openlocfilehash: b41b32943aa0113a7653c8d2eb74fd04afb2e080
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465834"
---
# <a name="monitoring-azure-backup-workloads"></a>Azure Backup munkaterhelések figyelése

Azure Backup több biztonsági mentési megoldást biztosít a biztonsági mentési követelmények és az infrastruktúra-topológia (helyszíni vagy Azure) alapján. Minden biztonsági mentési felhasználónak vagy rendszergazdának látnia kell, hogy mi történik az összes megoldásban, és a várhatóan fontos helyzetekben kapjon értesítést. Ez a cikk a Azure Backup szolgáltatás által biztosított figyelési és értesítési képességeket részletezi.

## <a name="backup-jobs-in-recovery-services-vault"></a>Biztonsági mentési feladatok a Recovery Services-tárolóban

A Azure Backup a Azure Backup által védett munkaterhelések számára beépített figyelési és riasztási képességeket biztosít. A Recovery Services-tároló beállításaiban a **figyelés** szakasz beépített feladatokat és riasztásokat tartalmaz.

![RS-tár beépített figyelése](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltmonitoring.png)

A feladatok akkor jönnek létre, ha olyan műveleteket végeznek, mint például a biztonsági mentés, a biztonsági mentés, a visszaállítás, a biztonsági mentés törlése és így tovább.

Az alábbi Azure Backup-megoldásokból származó feladatok itt láthatók:

  - Azure-beli virtuális gép biztonsági mentése
  - Azure-fájlok biztonsági mentése
  - Azure munkaterhelés biztonsági mentés, például SQL
  - Azure Backup ügynök (MAB)

A System Center Data Protection Manager (SC-DPM), Microsoft Azure Backup Server (MABS) feladatok nem jelennek meg.

> [!NOTE]
> Az Azure-beli virtuális gépeken belüli SQL-alapú biztonsági mentések nagy számú biztonsági mentési feladattal rendelkeznek. A naplók biztonsági mentései például 15 percenként is futtathatók. Ezért az adatbázis-munkaterhelések esetében csak a felhasználó által aktivált műveletek jelennek meg. Az ütemezett biztonsági mentési műveletek nem jelennek meg.

## <a name="backup-alerts-in-recovery-services-vault"></a>Biztonsági mentési riasztások Recovery Services-tárolóban

A riasztások elsődlegesen olyan helyzetek, amikor a felhasználók értesítést kapnak, hogy el tudják végezni a megfelelő lépéseket. A **biztonsági mentési riasztások** szakasz a Azure Backup szolgáltatás által generált riasztásokat jeleníti meg. Ezeket a riasztásokat a szolgáltatás definiálja, és a felhasználó nem tud egyéni riasztást létrehozni.

### <a name="alert-scenarios"></a>Riasztási forgatókönyvek
A szolgáltatás a következő forgatókönyveket figyelmeztethető forgatókönyvként határozza meg.

  - Biztonsági mentési/visszaállítási hibák
  - A biztonsági mentés sikeresen befejeződött az Azure Backup ügynök (MAB) esetében, de figyelmeztetésekkel
  - A védelem leállítása és a védelem leállítása az adattörlési szolgáltatással

### <a name="exceptions-when-an-alert-is-not-raised"></a>Kivételek, ha a riasztás nem merült fel
A riasztások nem jelennek meg a hibák miatt, ezért a következő kivételek állnak fenn:

  - A felhasználó explicit módon megszakította a futó feladatot.
  - A feladatot nem sikerült végrehajtani, mert folyamatban van egy másik biztonsági mentési művelet (semmi köze itt, mert csak meg kell várni, amíg az előző feladatnak befejeződik)
  - A virtuális gép biztonsági mentési feladata meghiúsul, mert a biztonsági másolattal rendelkező Azure-beli virtuális gép már nem létezik

A fenti kivételek úgy lettek kialakítva, hogy a műveletek eredménye (elsősorban a felhasználó által aktivált) azonnal megjelenik a portálon/PS/CLI-ügyfeleken. Ezért a felhasználó azonnal tisztában van, és nem igényel értesítést.

### <a name="alerts-from-the-following-azure-backup-solutions-are-shown-here"></a>Az alábbi Azure Backup-megoldások riasztásai itt láthatók:

  - Azure-beli virtuális gép biztonsági mentései
  - Azure File biztonsági mentései
  - Azure számítási feladatok biztonsági mentései, például SQL
  - Azure Backup ügynök (MAB)

> [!NOTE]
> A System Center Data Protection Manager (SC-DPM), Microsoft Azure Backup Server (MABS) riasztásai itt nem jelennek meg.

### <a name="alert-types"></a>Riasztástípusok
A riasztás súlyossága alapján a riasztásokat háromféle típusban lehet meghatározni:

  - **Kritikus**: Elvileg a biztonsági mentési vagy helyreállítási hibák (ütemezett vagy felhasználó által aktivált) a riasztások generálásához vezethetnek, és kritikus riasztásként, valamint olyan romboló műveletekkel is megjelenhetnek, mint például a biztonsági mentés törlése.
  - **Figyelmeztetés**: Ha a biztonsági mentési művelet sikeres, de néhány figyelmeztetéssel rendelkezik, a rendszer figyelmeztető riasztásként sorolja fel őket.
  - **Tájékoztatás**: A mai naptól kezdve a Azure Backup szolgáltatás nem hoz létre tájékoztató riasztást.

## <a name="notification-for-backup-alerts"></a>Értesítés a biztonsági mentési riasztásokról

> [!NOTE]
> Az értesítések konfigurálása csak az Azure Portalon végezhető el. A PS/CLI/REST API/Azure Resource Manager sablon támogatása nem támogatott.

A riasztások kiemelése után a rendszer értesíti a felhasználókat. A Azure Backup e-mailben egy beépített értesítési mechanizmust biztosít. Megadhat egyéni e-mail-címeket vagy terjesztési listát, amelyekről értesítést kaphat, ha riasztás jön létre. Azt is megadhatja, hogy az egyes riasztások értesítést kapjanak-e, vagy egy óránkénti kivonatban csoportosítsa őket, majd értesítést kap.

![RS-tároló beépített e-mail-értesítése](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltnotification.png)

Ha az értesítés konfigurálva van, egy üdvözlő vagy bevezető e-mailt fog kapni. Ezzel erősíti meg, hogy Azure Backup e-maileket küldhet ezekre a címekre riasztás esetén.<br>

Ha a gyakoriság egy óránkénti kivonatoló értékre lett beállítva, és egy órán belül megoldották a riasztást, akkor nem lesz része a közelgő óránkénti kivonatnak.

> [!NOTE]
>
> * Ha olyan roncsolásos műveletet végez, mint például a **védelem leállítása a törlési adatokkal** , akkor a rendszer riasztást küld, és e-mailt küld az előfizetés-tulajdonosoknak, a rendszergazdáknak és a társ-rendszergazdáknak akkor is, ha az értesítések nincsenek konfigurálva a helyreállítási tárolóhoz.
> * A sikeres feladatok értesítésének konfigurálásához használja a [log Analytics](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-workspace).

## <a name="next-steps"></a>További lépések

[Azure Backup-munkaterhelések figyelése Azure Monitor használatával](backup-azure-monitoring-use-azuremonitor.md)
