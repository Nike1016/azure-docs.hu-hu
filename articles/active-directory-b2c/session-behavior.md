---
title: Munkamenet-viselkedés konfigurálása – Azure Active Directory B2C | Microsoft Docs
description: Munkamenet-viselkedés konfigurálása Azure Active Directory B2Cban.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: b18a7d98654422951773c0a5497f69db93782f51
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849454"
---
# <a name="configure-session-behavior-in-azure-active-directory-b2c"></a>Munkamenet-viselkedés konfigurálása Azure Active Directory B2Cban

Ez a szolgáltatás részletesen szabályozható vezérlést tesz lehetővé a következő [módon](active-directory-b2c-reference-policies.md):

- A Azure AD B2C által felügyelt webalkalmazás-munkamenetek élettartama.
- Az egyszeri bejelentkezés (SSO) viselkedése több alkalmazás és felhasználói folyamat között a Azure AD B2C-bérlőben.

Ezek a beállítások nem érhetők el a felhasználói folyamatok jelszavának alaphelyzetbe állításához.

Azure AD B2C támogatja az [OpenID Connect hitelesítési protokollt](active-directory-b2c-reference-oidc.md) a biztonságos bejelentkezés webalkalmazásokhoz való engedélyezéséhez. A webalkalmazás-munkamenetek kezeléséhez a következő tulajdonságokat használhatja:

## <a name="session-behavior-properties"></a>Munkamenet viselkedési tulajdonságai

- **Webalkalmazás-munkamenet élettartama (perc)** – sikeres hitelesítés után a felhasználó böngészőjében tárolt Azure ad B2C's-munkamenet-cookie élettartama.
    - Alapértelmezett = 1440 perc.
    - Minimum (inkluzív) = 15 perc.
    - Maximum (inkluzív) = 1440 perc.
- **Webalkalmazás-munkamenet** időkorlátja – ha ez a kapcsoló **abszolút**értékre van állítva, a rendszer a webalkalmazás- **munkamenet élettartama (perc)** által megadott időszak elteltével újra hitelesíti a felhasználót. Ha a kapcsoló a **működés közbeni** (alapértelmezett beállítás) értékre van állítva, a felhasználó mindaddig bejelentkezett marad, amíg a felhasználó folyamatosan aktív a webalkalmazásban.
- **Egyszeri bejelentkezés konfigurálása** Ha több alkalmazással és felhasználói folyamattal rendelkezik a B2C-bérlőben, akkor az **egyszeri bejelentkezési konfigurációs** tulajdonsággal kezelheti a felhasználók interakcióit. A tulajdonságot a következő beállítások egyikére állíthatja be:
    - **Bérlő** – ez a beállítás az alapértelmezett érték. Ezzel a beállítással a B2C-bérlőben több alkalmazás és felhasználói folyamat is megoszthatja ugyanazt a felhasználói munkamenetet. Ha például egy felhasználó bejelentkezik egy alkalmazásba, a felhasználó zökkenőmentesen bejelentkezhet egy másik, contoso gyógyszertárba, amikor hozzáfér.
    - **Alkalmazás** – ez a beállítás lehetővé teszi, hogy a felhasználói munkamenetet kizárólag egy alkalmazáshoz, más alkalmazásoktól függetlenül kezelje. Ha például azt szeretné, hogy a felhasználó bejelentkezzen a contoso gyógyszertárba (ugyanazzal a hitelesítő adatokkal), akkor is, ha a felhasználó már be van jelentkezve a contoso Shoppingba, egy másik alkalmazás ugyanazon B2C-bérlőn.
    - **Házirend** – ez a beállítás lehetővé teszi a felhasználói munkamenetek kizárólag felhasználói folyamatokhoz való fenntartását, az azt használó alkalmazásoktól függetlenül. Ha például a felhasználó már bejelentkezett, és elvégezte a többtényezős hitelesítés (MFA) lépését, a felhasználó több alkalmazás nagyobb biztonságú részeihez is hozzáférhet, ha a munkamenet a felhasználói folyamathoz kötődik, nem jár le.
    - Letiltva – ez a beállítás arra kényszeríti a felhasználót, hogy a teljes felhasználói folyamaton keresztül fusson a szabályzat minden egyes végrehajtásán.

A következő használati esetek engedélyezettek a következő tulajdonságok használatával:

- Az iparág biztonsági és megfelelőségi követelményeinek kielégítése a webalkalmazás-munkamenetek megfelelő élettartamának beállításával.
- A hitelesítés kényszerítése egy beállított időszak után a felhasználó interakciójában a webalkalmazás magas biztonsági részével.

## <a name="configure-the-properties"></a>A tulajdonságok konfigurálása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja, majd a felső menüben kattintson a **címtár és előfizetés szűrőre** , és válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőt tartalmazza.
3. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
4. Válassza a **felhasználói folyamatok (szabályzatok)** lehetőséget.
5. Nyissa meg a korábban létrehozott felhasználói folyamatot.
6. Válassza ki **tulajdonságok**.
7. Konfigurálja a webalkalmazás- **munkamenet élettartamát (perc)** , a webalkalmazás- **munkamenet**időkorlátját, az **egyszeri bejelentkezés konfigurációját**, és szükség szerint adja **meg az azonosító tokent a** kijelentkezési kérésekben.

    ![A munkamenet viselkedési tulajdonságának beállításai a Azure Portalban](./media/session-behavior/session-behavior.png)

8. Kattintson a **Save** (Mentés) gombra.
