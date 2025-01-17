---
title: Adja hozzá munkahelyi vagy iskolai fiókját a Microsoft Authenticator app-Azure Active Directoryhoz | Microsoft Docs
description: Munkahelyi vagy iskolai fiók hozzáadása a Microsoft Authenticator alkalmazáshoz kétfaktoros ellenőrzéshez.
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
ms.openlocfilehash: 3be2ee662a061cdcb6acc58e47eda5feda3b9eee
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68880799"
---
# <a name="add-your-work-or-school-account"></a>Munkahelyi vagy iskolai fiók hozzáadása

Ha a szervezet kétfaktoros ellenőrzést használ, beállíthatja a munkahelyi vagy iskolai fiókját, hogy az Microsoft Authenticator alkalmazást az egyik ellenőrzési módszerként használja.

>[!Important]
>A fiók hozzáadása előtt le kell töltenie és telepítenie kell a Microsoft Authenticator alkalmazást. Ha még nem tette meg, kövesse az [alkalmazás letöltése és telepítése](user-help-auth-app-download-install.md) című cikk lépéseit.

## <a name="add-your-work-or-school-account"></a>Munkahelyi vagy iskolai fiók hozzáadása

1. A számítógépen lépjen a [további biztonsági ellenőrzés](https://aka.ms/mfasetup) lapra.

    >[!Note]
    >Ha nem látja a **további biztonsági ellenőrzés** oldalt, lehetséges, hogy a rendszergazda bekapcsolta a biztonsági adatok (előzetes verzió) szolgáltatást. Ha ez a helyzet, kövesse a [biztonsági adatok beállítása a hitelesítő alkalmazás használatára](security-info-setup-auth-app.md) című szakasz utasításait. Ha ez nem így van, segítségért forduljon a szervezet ügyfélszolgálatához. További információ a biztonsági adatokról: [biztonsági adatok (előzetes verzió) – áttekintés](user-help-security-info-overview.md).

2. Jelölje be a hitelesítő **alkalmazás**melletti jelölőnégyzetet, majd válassza a **Konfigurálás**lehetőséget.

    Megjelenik a **Mobile App konfigurálása** oldal.

    ![A QR-kódot biztosító képernyő](./media/user-help-auth-app-download-install/auth-app-barcode.png)

3. Nyissa meg a Microsoft Authenticator alkalmazást, válassza a **fiók hozzáadása** lehetőséget a **Testreszabás és vezérlés** ikonban a jobb felső sarokban, majd válassza a **munkahelyi vagy iskolai fiók**lehetőséget.

    >[!Note]
    >Ha első alkalommal állítja be a Microsoft Authenticator alkalmazást, megkérdezheti, hogy engedélyezi-e az alkalmazásnak a kamera (iOS) elérését, vagy hogy az alkalmazás képeket készítsen, és videót rögzítsen (Android). Az **Engedélyezés lehetőséget** kell választania, hogy a hitelesítő alkalmazás hozzáférhessen a kamerához, hogy a következő lépésben képet készítsen a QR-kódból. Ha nem engedélyezi a kamerát, akkor továbbra is beállíthatja a hitelesítő alkalmazást, de a kód adatait manuálisan kell hozzáadnia. A kód manuális hozzáadásával kapcsolatos további információkért lásd: [fiók manuális hozzáadása az alkalmazáshoz](user-help-auth-app-add-account-manual.md).

4. Az eszköz kamerájának használatával beszkennelheti a QR-kódot a **mobil alkalmazás konfigurálása** képernyőn a számítógépen, majd válassza a **kész**lehetőséget.

    >[!Note]
    >Ha a kamera nem tudja rögzíteni a QR-kódot, manuálisan is felveheti a fiók adatait a Microsoft Authenticator alkalmazásba kétfaktoros ellenőrzéshez. További információt és útmutatást a [fiók manuális hozzáadása](user-help-auth-app-add-account-manual.md)című témakörben talál.

5. Tekintse át az alkalmazás **fiókok** képernyőjét az eszközön, és győződjön meg róla, hogy a fiókja megfelelő, és hogy van-e hat számjegyű ellenőrző kód. A további biztonság érdekében az ellenőrző kód 30 másodpercenként változik, ami megakadályozza, hogy valaki többször is felhasználja a kódot.

    ![Fiókok képernyő](./media/user-help-auth-app-download-install/auth-app-accounts.png)

## <a name="next-steps"></a>További lépések

- Miután hozzáadta a fiókokat az alkalmazáshoz, bejelentkezhet a hitelesítő alkalmazás használatával az eszközön. További információ: [Bejelentkezés az alkalmazás használatával](user-help-auth-app-sign-in.md).

- Az iOS rendszerű eszközökön biztonsági mentést készíthet a fiók hitelesítő adatairól és a kapcsolódó Alkalmazásbeállítások, például a fiókok sorrendjéről a felhőhöz. További információ: [biztonsági mentés és helyreállítás Microsoft Authenticator alkalmazással](user-help-auth-app-backup-recovery.md).
