---
title: Az Azure Security Center alkalmazásszolgáltatások védelme |} A Microsoft Docs
description: Ez a cikk segít első lépései az App Services az Azure Security Center védelme.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: e8518710-fcf9-44a8-ae4b-8200dfcded1a
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/27/2019
ms.author: v-mohabe
ms.openlocfilehash: 6a6b5b6e247bd7c105286b86257a6ae11c1d1c60
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2019
ms.locfileid: "67551858"
---
# <a name="protect-app-service-with-azure-security-center"></a>App Service-ben az Azure Security Center védelme
Ez a cikk segít az Azure Security Center segítségével történő figyeléséhez és védelméhez az alkalmazások futtatása az App Service.

Az App Service segítségével hozhat létre és üzemeltethet webalkalmazásokat az Ön által választott programozási nyelven infrastruktúra kezelése nélkül is. Az App Service automatikus méretezést és magas rendelkezésre állást kínál, mind Windows és Linux rendszerű, valamint automatikus telepítéseket a GitHub-, Azure DevOps, vagy minden Git-tárházat támogatja. 

Webes alkalmazások biztonsági réseinek gyakran sérülékenységeket, mert az egy közös és dinamikus felületet szinte minden szervezetnek az interneten. Az App Service futó alkalmazások kérelmek halad át több átjáró üzembe helyezett Azure-adatközpontokban világszerte, a megfelelő alkalmazás az egyes kérelmek útválasztás felelős. 

Az Azure Security Center-értékelés és javaslatok futtathatja a próbakörnyezetbe, a virtuális gép vagy igény szerinti példányok lefordítja az App Service-ben futó alkalmazások. Használata révén, amely az Azure rendelkezik felhőszolgáltatóként látható-e, a Security Center elemzi az App Service-ben belső naplók webes alkalmazások gyakori támadásoktól gyakran több cél futó figyelni.

A Security Center az az App Service-alkalmazások ellen indított támadások azonosítása és újonnan felbukkanó támadások koncentrálhat, miközben a támadók a felderítési fázis, a biztonsági rések azonosításához több webhely vizsgálatát az üzemeltetett Azure-ban a felhő a méretezési csoport használja. A Security Center elemzési és gépi tanulási modellek használatával lehetővé teszi ügyfeleink számára, hogy az alkalmazásaikat, HTTP Protokollon keresztül vagy a felügyeleti metódusok összes illesztő terjed ki. Ezen felül egy belső szolgáltatásként az Azure-ban a Security Center van is egy egyedi helyzetben, hogy a mögöttes számítási csomópontok kiterjedő a paas-hez a gazdaszintű biztonsági elemzési ajánlat, engedélyezése webes alkalmazásokhoz, amelyek korábban elleni támadások észlelésére a Security Center már ki.

## <a name="prerequisites"></a>Előfeltételek

Az App Service monitorozásához és védelméhez egy, a dedikált gépekkel társított App Service-csomaggal kell rendelkeznie. A csomagok a következők: alapszintű, standard, prémium, izolált és Linux. Az Azure Security Center nem támogatja az ingyenes, közös és használatalapú csomagokat. További információkért lásd: [App Service-csomagok](https://azure.microsoft.com/pricing/details/app-service/plans/).

## <a name="security-center-protection"></a>A Security Center védelme

Az Azure Security Center védi a Virtuálisgép-példány fut, amelyben az App Service és a felügyeleti felület. Kérelmek és válaszok küldött és az App Service-ben futó alkalmazásokat is figyeli.

A Security Center natív módon integrálva van az App Service-ben, így nem kell az üzembe helyezési és bevezetési – az integráció teljesen transzparens módon működik.



## <a name="enabling-monitoring-and-protection-of-app-service"></a>Figyelés és az App Service protection engedélyezése

1. Az Azure-ban válassza ki a Security Center.
2. Lépjen a **díjszabási & beállítások** , és válasszon egy előfizetést.
3. A **tarifacsomag**, a a **App Service-ben** sor, a tervet az Váltás **engedélyezve**.

![App service be-vagy kikapcsolása](./media/security-center-app-services/app-services-toggle.png)

>[!NOTE]
> Az erőforrás-mennyiség felsorolt példányok az App Service-ben az árképzési szint panel megnyitásakor jelenleg aktív megfelelő példányainak számát jelöli. Ez a szám a skálázási beállítás alapján változhat, mert díjkötelesek példányainak számát ennek megfelelően módosítható.

Tiltsa le a figyelés és javaslatok az App Service, ismételje meg a folyamat és a be-vagy kikapcsolása a **App Service-ben** tervezi, hogy **letiltott**.



## <a name="see-also"></a>Lásd még
Ebben a cikkben megismerkedhetett az Azure Security Center figyelési funkcióinak használatával. Az Azure Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági szabályzatok beállítása az Azure Security Center](tutorial-security-policy.md): Megtudhatja, hogyan konfigurálhat biztonsági beállításokat az Azure Security Centerben.
* [Kezelése és válaszadás a biztonsági riasztásokra az Azure Security Center](security-center-managing-and-responding-alerts.md): A biztonsági riasztások kezelésének és a riasztásokra való válaszadás módját ismertető útmutató.
* [App services](security-center-virtual-machine-protection.md#app-services):  Az állapot-összefoglalók az App service-környezetek listájának megtekintéséhez.
* [Partneri megoldások monitorozása az Azure Security Center](security-center-partner-solutions.md): A partnermegoldások állapotának figyelését ismertető útmutató.
* [Az Azure Security Center – gyakori kérdések](security-center-faq.md): Gyakori kérdések a szolgáltatás használatával kapcsolatban.
* [Az Azure Security Blog](https://blogs.msdn.com/b/azuresecurity/): Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.
