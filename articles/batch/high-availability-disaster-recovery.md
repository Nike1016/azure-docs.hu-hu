---
title: Magas rendelkezésre állás és vész-helyreállítás – Azure Batch | Microsoft Docs
description: Megtudhatja, hogyan tervezheti meg a Batch-alkalmazást regionális leállás esetén
services: batch
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2019
ms.author: lahugh
ms.openlocfilehash: 3c76a5100e6ac1db067ccdbd582ddf9adba946c1
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68322589"
---
# <a name="design-your-application-for-high-availability"></a>Magas rendelkezésre állású alkalmazás tervezése

Azure Batch regionális szolgáltatás. A Batch az összes Azure-régióban elérhető, de egy batch-fiók létrehozásakor egy régióhoz kell társítani. Ezután a Batch-fiók összes művelete erre a régióra vonatkozik. A készletek és a társított virtuális gépek (VM-EK) például a Batch-fiókkal megegyező régióban jönnek létre.

A Batch szolgáltatást használó alkalmazások tervezésekor figyelembe kell vennie, hogy a Batch ne legyen elérhető egy régióban. Előfordulhat, hogy ritkán fordul elő, amikor a régió egésze, a teljes batch szolgáltatás a régióban, vagy az adott batch-fiókkal kapcsolatos probléma merül fel.

Ha a Batch-t használó alkalmazást vagy megoldást mindig elérhetőnek kell lennie, akkor azt úgy kell megtervezni, hogy egy másik régióba kerüljön feladatátvételre, vagy hogy a számítási feladatok mindig két vagy több régió között legyenek felosztva. Mindkét módszernek legalább két batch-fiókra van szüksége, és minden fiók egy másik régióban található.

## <a name="multiple-batch-accounts-in-multiple-regions"></a>Több batch-fiók több régióban

A különböző régiókban több batch-fiók használata lehetővé teszi, hogy az alkalmazás továbbra is fusson, ha egy másik régióban lévő batch-fiók elérhetetlenné válik. Több fiók használata különösen fontos, ha az alkalmazásnak nagy rendelkezésre állást kell biztosítania.

Bizonyos esetekben előfordulhat, hogy egy alkalmazás úgy van kialakítva, hogy mindig két vagy több régiót használjon. Ha például jelentős mennyiségű kapacitásra van szüksége, több régió használata szükséges lehet egy nagyméretű alkalmazás kezeléséhez vagy a jövőbeli növekedéshez.

## <a name="design-considerations-for-providing-failover"></a>Tervezési szempontok a feladatátvétel biztosításához

A másik régióba való feladatátvételi képesség biztosításának egyik kulcsfontosságú pontja, hogy figyelembe kell venni a megoldás összes összetevőjét. nem elegendő, ha csak egy második batch-fiókkal rendelkezik. A legtöbb batch-alkalmazásban például szükség van egy Azure Storage-fiókra, és a Storage-fióknak és a Batch-fióknak ugyanabban a régióban kell lennie az elfogadható teljesítmény érdekében.

A feladatátvételt biztosító megoldás tervezésekor vegye figyelembe a következő szempontokat:

- Minden szükséges fiók előzetes létrehozása minden régióban, például a Batch-fiók és a Storage-fiók. Gyakran nem számítunk fel díjat a fiókok létrehozásakor, csak akkor, ha a tárolt vagy a fiók használatban van.
- Győződjön meg arról, hogy a kvóták előre vannak beállítva a fiókokon, így a Batch-fiók használatával lefoglalhatja a szükséges magok számát.
- A sablonok és/vagy parancsfájlok segítségével automatizálhatja az alkalmazás központi telepítését egy adott régióban.
- Az alkalmazások bináris fájljainak és az adatok naprakészen tartása minden régióban. Naprakészen tarthatja, hogy a régiót gyorsan online állapotba hozhatja anélkül, hogy várnia kellene a fájlok feltöltésére és telepítésére. Ha például egy, a készlet csomópontjain telepítendő egyéni alkalmazást a Batch-alkalmazáscsomag használatával tárol és hivatkozik, akkor az alkalmazás új verziójának előállításakor fel kell töltenie az egyes batch-fiókokat, és a készlet konfigurációja hivatkozik rá (vagy hozza az új verziót az alapértelmezett verzióra.
- Az alkalmazásban a Batch, a Storage és bármely más szolgáltatás meghívásával egyszerűen átkapcsolhatja az ügyfeleket vagy a terhelést a másik régióba.
- Ajánlott eljárás a feladatátvétel sikerességének biztosítására, hogy a normál működés részeként gyakran váltson át egy másik régióba. Ha például két üzemelő példány külön régióban található, a rendszer minden hónapban átvált a másodlagos régióba.

## <a name="next-steps"></a>További lépések

- További információ a Batch-fiókok [Azure Portal](batch-account-create-portal.md), az [Azure CLI](cli-samples.md), a [POWERSHELL](batch-powershell-cmdlets-get-started.md)vagy a [Batch Management API](batch-management-dotnet.md)használatával történő létrehozásáról.
- Az alapértelmezett kvóták egy batch-fiókhoz vannak társítva; [Ez a cikk](batch-quota-limit.md) az alapértelmezett kvóta-értékeket részletezi, és leírja, hogyan lehet növelni a kvótákat.
