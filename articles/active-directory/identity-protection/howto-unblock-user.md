---
title: Felhasználók blokkolásának feloldása Azure Active Directory Identity Protection használatával | Microsoft Docs
description: Megtudhatja, hogyan tilthatja le a Azure Active Directory Identity Protection szabályzat által blokkolt felhasználókat.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: c28e30b8e44b6888cdb7416b9c7b563b955a68ce
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335378"
---
# <a name="how-to-unblock-users"></a>kézikönyv: Felhasználók tiltásának feloldása

A Azure Active Directory Identity Protection segítségével házirendeket állíthat be a felhasználók blokkolására, ha a konfigurált feltételek teljesülnek. A letiltott felhasználók általában kapcsolatba lépnek az ügyfélszolgálattal a blokkolás feloldása érdekében. Ez a cikk a letiltott felhasználók blokkolásának feloldásához szükséges lépéseket ismerteti.

## <a name="determine-the-reason-for-blocking"></a>A blokkolás okának meghatározása

A felhasználó blokkolásának feloldásának első lépéseként meg kell határoznia, hogy milyen típusú házirendet blokkolt a felhasználó, mert a következő lépések attól függően változnak.
A Azure Active Directory Identity Protection használatával a felhasználókat a bejelentkezési kockázati házirend vagy egy felhasználói kockázati házirend blokkolhatja.

A bejelentkezési kísérlet során a felhasználónak megjelenő házirend típusa letilthatja a felhasználót a párbeszédpanelen megjelenő fejlécből:

| Szabályzat | Felhasználó párbeszédpanel |
| --- | --- |
| Bejelentkezési kockázat |![Tiltott bejelentkezés](./media/howto-unblock-user/02.png) |
| Felhasználói kockázat |![Letiltott fiók](./media/howto-unblock-user/104.png) |

A által blokkolt felhasználó:

* A bejelentkezési kockázati szabályzatot gyanús bejelentkezésnek is nevezzük
* A felhasználói kockázati házirendeket a kockázatnak kitett fiókoknak is nevezik

## <a name="unblocking-suspicious-sign-ins"></a>Gyanús bejelentkezések blokkolásának feloldása

A gyanús bejelentkezés feloldásához a következő lehetőségek közül választhat:

1. **Bejelentkezés ismerős helyről vagy eszközről** – a tiltott gyanús bejelentkezések gyakori oka az ismeretlen helyekről vagy eszközökről érkező bejelentkezési kísérletek. A felhasználók gyorsan meghatározhatják, hogy ez a blokkolás oka-e, ha egy ismerős helyről vagy eszközről próbálnak bejelentkezni.
2. **Kizárás a szabályzatból** – ha úgy gondolja, hogy a bejelentkezési szabályzat jelenlegi konfigurációja problémákat okoz bizonyos felhasználók számára, kizárhatja a felhasználókat. További információ: [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).
3. **Házirend letiltása** – ha úgy gondolja, hogy a házirend-konfiguráció az összes felhasználónál problémát okoz, letilthatja a szabályzatot. További információ: [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

## <a name="unblocking-accounts-at-risk"></a>A veszélyeztetett fiókok blokkolásának feloldása

A veszélyeztetett fiókok blokkolásának feloldásához a következő lehetőségek állnak rendelkezésre:

1. **Jelszó** alaphelyzetbe állítása – alaphelyzetbe állíthatja a felhasználó jelszavát. 
2. Az **összes kockázati esemény bezárása** – a felhasználói kockázati házirend letilt egy felhasználót, ha elérte a hozzáférés-blokkoló beállításhoz beállított felhasználói kockázati szintet. Csökkentheti a felhasználók kockázati szintjét a jelentett kockázati események manuális lezárásával. 
3. **Kizárás a szabályzatból** – ha úgy gondolja, hogy a bejelentkezési szabályzat jelenlegi konfigurációja problémákat okoz bizonyos felhasználók számára, kizárhatja a felhasználókat. További információ: [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).
4. **Házirend letiltása** – ha úgy gondolja, hogy a házirend-konfiguráció az összes felhasználónál problémát okoz, letilthatja a szabályzatot. További információ: [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

## <a name="next-steps"></a>További lépések
 
Szeretne többet megtudni a Azure AD Identity Protectionról? Tekintse meg [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).
