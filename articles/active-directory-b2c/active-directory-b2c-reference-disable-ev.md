---
title: Regisztráció az Azure Active Directory B2C fogyasztói során e-mailes ellenőrzés letiltása |} A Microsoft Docs
description: A témakör bemutatja, hogyan lehet során az Azure Active Directory B2C-előfizetés felhasználói e-mailes ellenőrzés letiltása.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 8b50b59b6a1f99787b842923cd6ec77ee6500f0a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66509536"
---
# <a name="disable-email-verification-during-consumer-sign-up-in-azure-active-directory-b2c"></a>Regisztráció az Azure Active Directory B2C fogyasztói során e-mailes ellenőrzés letiltása 
Ha engedélyezve van, a Azure Active Directory (Azure AD) B2C egy e-mail-címet biztosít, és hozzon létre egy helyi fiók alkalmazások regisztráció lehetővé teszi egy fogyasztó. Az Azure AD B2C biztosítja, hogy érvényes e-mail-címek azzal, hogy a felhasználó számára, hogy a regisztrációs folyamat során ellenőrizni őket. Azt is megakadályozza, hogy egy rosszindulatú automatikus folyamat azon alkalmazások hamis fiókok létrehozása.

Egyes alkalmazások fejlesztői inkább a regisztrációs folyamat során az e-mail-ellenőrzés kihagyása és ehelyett a fogyasztók Ellenőrizze később, az e-mail-cím. Ehhez az Azure AD B2C-vel beállítható úgy, hogy e-mailes ellenőrzés letiltása. Ezzel létrehozza az egyenletesebb regisztrációs folyamaton, és rugalmasságot biztosít a fejlesztők a különbséget tenni a felhasználóknak az e-mail-címükkel, ezek a felhasználók, amelyek még nem ellenőrizte.

Alapértelmezés szerint az előfizetés-kezelő felhasználói folyamatok rendelkeznek e-mailes ellenőrzés engedélyezve van. A következő lépések segítségével kikapcsolásához:

1. Kattintson a **felhasználókövetési adatai**.
2. Kattintson a felhasználói folyamat (például "B2C_1_SiUp") való megnyitásához. 
3. Kattintson a **elrendezések lapon**.
4. Kattintson a **helyi fiók regisztrálási oldala**.
5. Kattintson a **E-mail cím** a a **neve** oszlop alatt a **felhasználói attribútumok** szakaszban.
6. A **ellenőrzés szükséges**válassza **nem**.
7. Kattintson a panel tetején lévő **Mentés** elemre. Kész!

> [!NOTE]
> A regisztrációs folyamat az e-mailes ellenőrzés letiltása fogjuk kéretlen információk küldésére vezethet. Ha letiltja az alapértelmezett, ajánlott felvenni a saját ellenőrzési rendszerétől.
> 
>
