---
title: Biztonsági javaslatok a Azure Security Centerban | Microsoft Docs
description: Ez a dokumentum részletesen ismerteti, hogyan segíti az Azure Security Center az Azure-erőforrások védelmében és a biztonsági szabályzatoknak való megfelelésben.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 86c50c9f-eb6b-4d97-acb3-6d599c06133e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/29/2019
ms.author: v-mohabe
ms.openlocfilehash: 229b8949facae34a809c0789154a3b56264ee2c5
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779020"
---
# <a name="security-recommendations-in-azure-security-center"></a>Biztonsági javaslatok az Azure Security Centerben 
Ez a témakör azt ismerteti, hogyan lehet megtekinteni és értelmezni a Azure Security Centerban található javaslatokat az Azure-erőforrások védelmének elősegítése érdekében.

> [!NOTE]
> Ez a dokumentum egy üzembe helyezést szemléltető példa segítségével mutatja be a szolgáltatást.  Ez a dokumentum nem az útmutató lépésről lépésre.
>

## <a name="what-are-security-recommendations"></a>Mik azok a biztonsági javaslatok?

A javaslatok olyan műveletek, amelyekkel biztonságossá teheti az erőforrásokat.

Az esetleges biztonsági rések azonosítása érdekében rendszeresen Security Center az Azure-erőforrások biztonsági állapotának elemzését. Ezután javaslatokat tesz a eltávolításához.

Az egyes javaslatok a következőket biztosítják:

- A javaslat rövid leírása.
- A javaslat végrehajtásához szükséges szervizelési lépések. <!-- In some cases, one-click remediation is available. -->
- Mely erőforrásokra van szükség a javasolt művelet végrehajtásához.
- A **biztonságos pontszám hatása**, ami azt az összeget befolyásolja, amelyet a biztonságos pontszám felvesz, ha végrehajtja ezt a javaslatot.

## Javaslatok figyelése<a name="monitor-recommendations"></a>

Security Center elemzi az erőforrások biztonsági állapotát, hogy azonosítsa a lehetséges biztonsági réseket. A **javaslatok** csempéje az **áttekintés** területen a Security Center által azonosított javaslatok teljes számát jeleníti meg.

![A Security Center áttekintése](./media/security-center-recommendations/asc-overview.png)

1. Kattintson a **javaslatok csempére** az **Áttekintés**területen. Megnyílik a **javaslatok** listája.

      ![Javaslatok megtekintése](./media/security-center-recommendations/view-recommendations.png)

    Lehetőség van a javaslatok szűrésére. A javaslatok szűréséhez válassza a **szűrés** lehetőséget a **javaslatok** panelen. Megnyílik a **szűrő** panel, és kiválasztja a megtekinteni kívánt súlyossági és állapot-értékeket.

   * **JAVASLATOK**: A javaslat.
   * A **BIZTONSÁGOS PONTSZÁM HATÁSA**: A Security Center által generált pontszám a biztonsági javaslatokkal, valamint a speciális algoritmusok alkalmazásával határozható meg, hogy az egyes javaslatok mennyire fontosak. További információ: [biztonságos pontszámok kiszámítása](security-center-secure-score.md#secure-score-calculation).
   * **ERŐFORRÁS**: Felsorolja azokat az erőforrásokat, amelyekre ez a javaslat vonatkozik.
   * **ÁLLAPOTJELZŐ SÁVOK**:  Az adott javaslat súlyosságát írja le:
       * **Magas (piros)** : A biztonsági rés egy értelmes erőforrással (például egy alkalmazással, egy virtuális géppel vagy egy hálózati biztonsági csoporttal) van, és figyelmet igényel.
       * **Közepes (narancssárga)** : Egy biztonsági rés létezik, és nem kritikus fontosságú vagy további lépések szükségesek az eltávolításához vagy egy folyamat befejezéséhez.
       * **Alacsony (kék)** : Létezik egy olyan biztonsági rés, amelynek meg kell oldania, de nem igényel azonnali beavatkozást. (Alapértelmezés szerint az alacsony javaslatok nem jelennek meg, de ha szeretné megtekinteni, az alacsony javaslatokat is szűrheti.) 
       * **Kifogástalan (zöld)** :
       * **Nem érhető el (szürke)** :

1. Az egyes javaslatok részleteinek megtekintéséhez kattintson a javaslatra.

    ![Javaslat részletei](./media/security-center-recommendations/recommendation-details.png)

>[!NOTE] 
> Lásd: [klasszikus és Resource Manager-](../azure-classic-rm.md) alapú üzemi modellek az Azure-erőforrásokhoz.
 
## <a name="next-steps"></a>További lépések

Ebben a dokumentumban a Security Center biztonsági javaslataira került sor. További információ a javaslatok megoldásáról:

* [Javaslatok szervizelése](security-center-remediate-recommendations.md) – Ismerje meg, hogyan konfigurálhatja az Azure-előfizetések és-erőforráscsoportok biztonsági szabályzatait.
