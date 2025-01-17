---
title: Mi a Privileged Identity Management? – Az azure Active Directory |} A Microsoft Docs
description: Az Azure AD Privileged Identity Management (PIM) áttekintést nyújt.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: pim
ms.topic: overview
ms.date: 04/09/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0678dc42d8c22c8dcc20137cf70f81a1eb4f61c
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66473686"
---
# <a name="what-is-azure-ad-privileged-identity-management"></a>Mi az az Azure AD Privileged Identity Management?

Az Azure Active Directory (Azure AD) Privileged Identity Management (PIM) egy szolgáltatása, amely lehetővé teszi, hogy a kezelése, szabályozása és figyelése a szervezet fontos erőforrásokhoz való hozzáférés. Ebbe beletartozik az Azure AD, az Azure-erőforrások és más online Microsoft-szolgáltatások, például az Office 365 vagy a Microsoft Intune erőforrásaihoz való hozzáférés.

## <a name="why-should-i-use-pim"></a>Miért érdemes használni a PIM?

Szervezetek számára szeretne információkat vagy erőforrások, biztonságos, mert, amely csökkenti az esélyét, hogy egy adott hozzáférhetne rosszindulatú aktor, vagy egy jogosult felhasználó véletlenül érintő bizalmas erőforrás hozzáféréssel rendelkező személyek számának csökkentése érdekében. Azonban a felhasználóknak ettől függetlenül el kell végezniük bizonyos emelt szintű műveleteket az Azure AD-, Azure-, Office 365- vagy SaaS-alkalmazásokban. Szervezetek számára adhat a felhasználóknak az emelt szintű hozzáférés az Azure-erőforrások és az Azure AD-igény (szerinti JIT). A felügyeletet, mivel foglalkoznak az ezek a felhasználók saját rendszergazdai jogosultságokkal az szükség van. A PIM segít mérsékelni a túlzott mértékben használta, a szükségtelen kockázatának, vagy helytelen a hozzáférési jogosultságokat.

## <a name="what-can-i-do-with-pim"></a>Mire használhatom a PIM Használatát?

A PIM lényegében segítségével kezelheti a ki, mit, mikor, hol és miért érdemes az erőforrásra, amely az Ön számára. Íme néhány a PIM főbb jellemzői:

- Adja meg **just-in-time** emelt szintű hozzáférés az Azure AD és az Azure-erőforrások
- Rendelje hozzá **időhöz kötött** kezdő és záró dátuma erőforrások elérése
- Szükséges **jóváhagyási** emelt szintű szerepkörök aktiválása
- Kényszerítése **a multi-factor authentication** bármely szerepkör aktiválása
- Használat **indoklás** tudni, miért felhasználók aktiválása
- Első **értesítések** kiemelt szerepkörök aktiválásakor
- Magatartási **hozzáférési felülvizsgálatokkal** annak biztosítása érdekében a felhasználók továbbra is szükséges szerepkörök
- Töltse le **naplózási előzmények** a belső vagy külső naplózás

## <a name="terminology"></a>Terminológia

A PIM és a hozzá tartozó dokumentáció jobb megértéséhez, tekintse át az alábbi feltételek.

| Kifejezés vagy fogalom | Szerepkör-hozzárendelés kategória | Leírás |
| --- | --- | --- |
| Jogosult | Típus | Szerepkör-hozzárendelés, amelyhez a felhasználót a szerepkör használatához egy vagy több művelet végrehajtásához szükséges. Ha egy felhasználó jogosult szerepkör lett végrehajtva, ez azt jelenti, ha kiemelt feladatait van szükségük a szerepkör aktiválásához. Nincs megadott valaki egy állandó jogosult szerepkör-hozzárendelés és hozzáférési nincs különbség. Az egyetlen különbség, hogy néhány felhasználó nem kell olyan folyamatosan. |
| aktív | Típus | Szerepkör-hozzárendelés, amelyek nem igényelnek a felhasználót, hogy a szerepkör használatához bármely művelet elvégzésére. A felhasználók hozzárendelve aktívként a szerepkörhöz rendelt jogosultság. |
| aktiválás |  | Folyamat használatához egy szerepkör, amely a felhasználó nem jogosult egy vagy több műveletet hajt végre. Műveletek tartalmazhat, egy üzleti indoklásának megadása, vagy jóváhagyási kérése a kijelölt jóváhagyókat többtényezős hitelesítés (MFA)-ellenőrzés elvégzése. |
| Hozzárendelve | Állapot | Aktív szerepkör-hozzárendeléssel rendelkező felhasználóként. |
| Aktiválva | Állapot | Egy jogosult szerepkör-hozzárendelés rendelkező felhasználóként végrehajtani a műveleteket a szerepkör aktiválását, és most már aktív.  Aktiválta, miután a felhasználó használhatja az előre konfigurált időszak-az-ideje előtt újra aktiválni kell azokat a szerepkör. |
| állandó jogosult | Időtartam | Ahol a felhasználó mindig a szerepkör aktiválását jogosult szerepkör-hozzárendelés. |
| állandó aktív | Időtartam | Szerepkör-hozzárendelés ahol a felhasználó bármikor használhatja a szerepkör bármilyen művelet végrehajtása nélkül. |
| jogosult lejár | Időtartam | Ahol a felhasználó abban az esetben a szerepkör aktiválását a megadott kezdő és záró dátum belül jogosult szerepkör-hozzárendelés. |
| aktív lejár | Időtartam | Szerepkör-hozzárendelés ahol a felhasználó használhatja a szerepkör belül megadott kezdő és záró dátum bármilyen művelet végrehajtása nélkül. |
| – igény (szerinti JIT) hozzáférési |  | A modell, amelyben a felhasználók megkapják a rendszerjogosultságú feladatait ideiglenes engedélyek, amely megakadályozza, hogy a rosszindulatú vagy jogosulatlan felhasználók hozzáférjenek a engedélyekkel rendelkezik lejárta után. Hozzáférés csak akkor, ha a felhasználók szüksége van rá. |
| legkisebb jogosultság hozzáférés elve |  | A feladatok elvégzéséhez jogosultak, amelyben minden felhasználó csak a minimális jogosultságokkal rendelkező megadott ajánlott biztonsági eljárás szükséges. Ez az eljárás minimálisra csökkentik a globális rendszergazdák, és helyette használja az adott rendszergazdai szerepköröket bizonyos forgatókönyvek esetén. |

## <a name="what-does-pim-look-like"></a>Milyen kinéznie PIM?

Miután beállította a PIM, látni fogja **feladatok**, **kezelés**, és **tevékenység** a bal oldali navigációs menü Beállítások. A rendszergazdák kezelése közötti fogja választani **Azure AD-szerepkörök** és **Azure-erőforrás** szerepköröket. Ha úgy dönt, a szerepkörök kezeléséhez típusát, tekintse meg hasonló beállítási lehetőségek tartoznak az adott szerepkör típusa.

![Képernyőkép a PIM az Azure Portalon](./media/pim-configure/pim-overview.png)

## <a name="who-can-do-what-in-pim"></a>Ki teheti Mi a PIM?

Ha Ön az első, aki a PIM használatát tervezi, akkor automatikusan megkapja a [biztonsági rendszergazda](../users-groups-roles/directory-assign-admin-roles.md#security-administrator) és [kiemelt szerepkörű rendszergazda](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) szerepkörökhöz a címtárban.

Az Azure AD-szerepkörök csak egy felhasználót, aki a kiemelt szerepkörű rendszergazda szerepkör kezelheti más rendszergazdák a PIM-hozzárendeléseit. Is [hozzáférés biztosítása más rendszergazdák számára, miközben a PIM kezelése](pim-how-to-give-access-to-pim.md). Globális rendszergazdák, biztonsági rendszergazdák és biztonsági olvasók PIM az Azure AD szerepkör-hozzárendelések is megtekintheti.

Azure-erőforrások szerepköreihez, csak egy előfizetés-rendszergazda, az erőforrás tulajdonosa vagy a felhasználói hozzáférés rendszergazdája erőforrás kezelhetik a más rendszergazdák a PIM-hozzárendeléseit. Felhasználók, akik a kiemelt szerepkörű rendszergazdák, biztonsági rendszergazdák és a biztonsági olvasók alapértelmezés szerint nem rendelkeznek hozzáféréssel a PIM az Azure-erőforrás szerepkör-hozzárendelések megtekintéséhez.

## <a name="scenarios"></a>Forgatókönyvek

A PIM használatát támogatja a következő esetekben:

**Kiemelt szerepkörű rendszergazdaként a következőkre jogosult:**

- Jóváhagyás engedélyezése speciális szerepkörökhöz
- Kérelmek jóváhagyására jogosult felhasználók és/vagy csoportok meghatározása
- Speciális szerepkörökhöz tartozó kérelmek és jóváhagyások előzményeinek megtekintése

**Jóváhagyóként a következőket teheti:**

- Függőben lévő jóváhagyások (kérelmek) megtekintése
- Szerepkör-kiterjesztésre irányuló kérelmek jóváhagyása vagy visszautasítása (egyszerre egy és/vagy több)
- Indoklás fűzése a jóváhagyáshoz/elutasításhoz 

**Jogosult szerepkör felhasználóként a következő műveletek végezhetők el:**

- Jóváhagyást igénylő szerepkör aktiválásának kérelme
- Az aktiválási kérelem állapotának megtekintése
- Az aktiválás jóváhagyása után feladatok végrehajtása az Azure AD-ben

## <a name="license-requirements"></a>Licenckövetelmények

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

Felhasználók licenceinek kapcsolatos információkért lásd: [Licenckövetelményekért a PIM használatát tervezi](subscription-requirements.md).

## <a name="next-steps"></a>További lépések

- [Licenckövetelmények vonatkoznak, miközben a PIM használata](subscription-requirements.md)
- [Emelt szintű hozzáférés biztosítása Azure AD hibrid- és felhőkörnyezetekhez](../users-groups-roles/directory-admin-roles-secure.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)
- [A PIM telepítése](pim-deployment-plan.md)
