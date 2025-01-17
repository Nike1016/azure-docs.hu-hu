---
title: Személyes Microsoft-fiókjainak hozzáadása a Microsoft Authenticator app-Azure Active Directoryhoz | Microsoft Docs
description: Személyes Microsoft-fiókjainak hozzáadása (például Outlook.com vagy Xbox LIVE) a Microsoft Authenticator alkalmazáshoz kétfaktoros ellenőrzés esetén.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: lizross
ms.reviewer: olhaun
ms.collection: M365-identity-device-management
ms.openlocfilehash: 502407bbf1cdb47fc8105ed0220babcd9f292faa
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/22/2019
ms.locfileid: "68382693"
---
# <a name="add-your-personal-microsoft-accounts"></a>Személyes Microsoft-fiókok hozzáadása

Adja hozzá a személyes Microsoft-fiókjait, például a Outlook.com és az Xbox LIVE-t a Microsoft Authenticator alkalmazáshoz mind a standard Kéttényezős ellenőrzési folyamat, mind a jelszó nélküli telefonos bejelentkezési módszer használatával.

- **Standard kétfaktoros ellenőrzési módszer.** Írja be a felhasználónevét és jelszavát arra az eszközre, amelyhez be van jelentkezve, majd válassza ki, hogy a Microsoft Authenticator alkalmazás küldjön-e értesítést, vagy ha szeretné átmásolni a  társított ellenőrző kódot a Microsoft fiókjainak képernyőjéről. Hitelesítő alkalmazás.

- **Jelszó nélküli bejelentkezési módszer.** Írja be a felhasználónevét az eszközre, amelyet be szeretne jelentkezni a személyes Microsoft-fiókba, majd a mobileszköz használatával ellenőrizze, hogy az ujjlenyomatát, arcát vagy PIN-kódját használja-e. Ennél a módszernél nem kell megadnia a jelszavát.

>[!Important]
>A fiók hozzáadása előtt le kell töltenie és telepítenie kell a Microsoft Authenticator alkalmazást. Ha még nem tette meg, kövesse az [alkalmazás letöltése és telepítése](user-help-auth-app-download-install.md) című cikk lépéseit.

## <a name="add-your-personal-microsoft-account"></a>Személyes Microsoft-fiók hozzáadása

Személyes Microsoft-fiók a kétfaktoros ellenőrzés bekapcsolásával, majd a fióknak az alkalmazáshoz való hozzáadásával adhat hozzá.

>[!Note]
>Ha úgy tervezi, hogy a személyes Microsoft-fiók jelszó nélküli telefonos bejelentkezést használ, nem kell bekapcsolnia a kétfaktoros ellenőrzést. A fiók biztonsága érdekében azonban javasoljuk, hogy kapcsolja be a kétfaktoros ellenőrzést.

### <a name="turn-on-two-factor-verification"></a>Kétfaktoros ellenőrzés bekapcsolása

1. A számítógépen lépjen a [Biztonság alapjai](https://account.microsoft.com/security) lapra, és jelentkezzen be a személyes Microsoft-fiók használatával. Például: alain@outlook.com.

2. A **Biztonság alapjai** lap alján válassza a **további biztonsági beállítások** hivatkozást.

    ![Biztonsági alapismeretek oldal a "további biztonsági beállítások" hivatkozás kiemelve](./media/user-help-auth-app-add-personal-ms-account/more-security-options-link.png)

3. Lépjen a kétlépéses **ellenőrzés** szakaszra, és válassza **a funkció**bekapcsolását. Kikapcsolhatja azt is, ha már nem szeretné használni a személyes fiókjával.

### <a name="add-your-microsoft-account-to-the-app"></a>Microsoft-fiók hozzáadása az alkalmazáshoz

1. Nyissa meg a Microsoft Authenticator alkalmazást a mobileszközön.

2. Válassza a **fiók hozzáadása** lehetőséget a **Testreszabás és vezérlés** ikon jobb felső sarkában.

    ![Fiókok lap, a Testreszabás és vezérlés ikon kiemelve](./media/user-help-auth-app-add-personal-ms-account/customize-and-control-icon.png)

3. A **fiók hozzáadása** lapon válassza a **személyes fiók**lehetőséget.

4. Jelentkezzen be a személyes fiókjába a megfelelő e-mail-cím ( alain@outlook.compéldául) használatával, majd válassza a **tovább**lehetőséget.

    >[!Note]
    >Ha nincs személyes Microsoft-fiók, itt hozhat létre egyet.

5. Adja meg a jelszót, majd válassza a **Bejelentkezés**lehetőséget.

    A rendszer hozzáadja a személyes fiókját a Microsoft Authenticator alkalmazáshoz.

## <a name="next-steps"></a>További lépések

- Miután hozzáadta a fiókokat az alkalmazáshoz, bejelentkezhet a hitelesítő alkalmazás használatával az eszközön. További információ: [Bejelentkezés az alkalmazás használatával](user-help-auth-app-sign-in.md).

- Ha nem sikerül beszereznie a személyes Microsoft-fiók ellenőrzési kódját, tekintse meg az **ellenőrző** kód problémáinak elhárítása című szakaszt a [Microsoft-fiók biztonsági adatok & ellenőrző kódok](https://support.microsoft.com/en-us/help/12428/microsoft-account-security-info-verification-codes) című cikkben.

- Az iOS rendszerű eszközökön biztonsági mentést készíthet a fiók hitelesítő adatairól és a kapcsolódó Alkalmazásbeállítások, például a fiókok sorrendjéről a felhőhöz. További információ: [biztonsági mentés és helyreállítás Microsoft Authenticator alkalmazással](user-help-auth-app-backup-recovery.md).
