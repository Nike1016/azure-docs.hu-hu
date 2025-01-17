---
title: Standard – Verizon Azure CDN-profil át Verizon Premium |} A Microsoft Docs
description: Ismerje meg a profil áttelepítés Standard – Verizon a Verizon prémium szintű részleteit.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2018
ms.author: magattus
ms.custom: ''
ms.openlocfilehash: c11155223220c075c28769a19875691783cce3bd
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593625"
---
# <a name="migrate-an-azure-cdn-profile-from-standard-verizon-to-premium-verizon"></a>Standard – Verizon Azure CDN-profil át prémium – Verizon

Amikor létrehoz egy Azure Content Delivery Network (CDN) profilt a végpontok kezeléséhez, az Azure CDN kínál négy különböző termékekhez tartozó közül választhat. A különböző termékek és az elérhető funkciókkal kapcsolatos további információkért lásd: [hasonlítsa össze az Azure CDN termékszolgáltatásai](cdn-features.md).

Ha már létrehozott egy **Azure CDN Standard a Verizontól** profilját, és kezelheti a CDN-végpontokat, használja azt, hogy a frissítési lehetőség van egy **verizon Azure CDN Premium** profil. Amikor frissít, a CDN-végpontokat, és az adatok megmaradnak. 

> [!IMPORTANT]
> Miután már frissített egy **verizon Azure CDN Premium** profil, akkor nem később alakíthatja vissza a egy **Azure CDN Standard a Verizontól** profil.
> 

Frissítése az **Azure CDN Standard verizon** profil, a kapcsolattartási [Support](https://azure.microsoft.com/support/options/).

## <a name="profile-comparison"></a>Profil összehasonlítása
**Az Azure CDN Premiumhoz a Verizontól** profilok a következő különbségeket kell **Azure CDN Standard verizon** profilok:
- Az egyes Azure CDN szolgáltatások, például a [tömörítési](cdn-improve-performance.md), [gyorsítótár-szabályokkal](cdn-caching-rules.md), és [geoszűrési](cdn-restrict-access-by-country.md), nem használhatja az Azure CDN-felület, a Verizon portálon keresztül kell használnia a **Kezelés** gombra.
- API: Ellentétben a Standard – Verizon, nem használható az API-t ezek a Funkciók, amelyekhez hozzáférnek a Premium Verizon portálról. Az API segítségével azonban más közös szolgáltatások, például a végpont létrehozása/törlése, a gyorsítótárazott eszközök végleges törlése, betöltése és a egy egyéni tartomány engedélyezése vagy letiltása.
- Díjszabás: Prémium – Verizon adatátvitelt, mint a Standard – Verizon rendelkezik a különböző árképzési struktúráját. További információkért lásd: [Content Delivery Network díjszabása](https://azure.microsoft.com/pricing/details/cdn/).

**Az Azure CDN Premiumhoz a Verizontól** profilok a következő további szolgáltatások rendelkezik:
- [Jogkivonat-hitelesítés](cdn-token-auth.md): Lehetővé teszi a felhasználóknak le és használható jogkivonatot beolvasni a biztonságos erőforrásokhoz.
- [Szabálymotor](cdn-rules-engine.md): Lehetővé teszi, hogy testre szabhatja a HTTP-kérések kezelésének módja.
- Fejlett analitikai eszközöket:
   - [Részletes HTTP-elemzés](cdn-advanced-http-reports.md)
   - [Peremhálózati Teljesítményelemzés](cdn-edge-performance.md)
   - [Valós idejű elemzés](cdn-real-time-alerts.md)


## <a name="next-steps"></a>További lépések
A rules engine kapcsolatos további információkért lásd: [Azure CDN szabálymotor-referencia](cdn-rules-engine-reference.md).

