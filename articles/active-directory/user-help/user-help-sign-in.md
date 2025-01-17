---
title: Bejelentkezés kétlépéses ellenőrzéssel vagy biztonsági információkkal – Azure Active Directory | Microsoft Docs
description: Ismerje meg, hogyan jelentkezhet be a különböző Identity ellenőrzési módszerekkel a biztonsági adatokban.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: librown
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: lizross
ms.custom: user-help
ms.collection: M365-identity-device-management
ms.openlocfilehash: b20ea1131ceda0527ed35d1a1082d05f25da6bac
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/22/2019
ms.locfileid: "68382299"
---
# <a name="sign-in-using-two-step-verification-or-security-info"></a>Bejelentkezés kétlépéses ellenőrzés vagy biztonsági információ használatával

A kétlépéses ellenőrzés vagy a biztonsági adatok beállítása után a megadott hitelesítési módszer használatával bejelentkezhet a fiókjába.

> [!Note]
> Ha továbbra is a kétlépéses ellenőrzési funkciót használja, a hitelesítési módszereket úgy kell beállítania, hogy a [saját fiók beállítása](multi-factor-authentication-end-user-first-time.md) kétlépéses ellenőrzésre című cikk utasításait követve.
>
> Ha a rendszergazda bekapcsolta a biztonsági adatokkal kapcsolatos felhasználói élményt, a következő lépésekre bontott cikkekkel kell beállítania a hitelesítési módszereket:<ul><li>[Biztonsági adatok beállítása hitelesítési alkalmazás használatára](security-info-setup-auth-app.md)</li><li>[A biztonsági adatok szöveges üzenetben történő hitelesítésének beállítása](security-info-setup-text-msg.md)</li><li>[Biztonsági adatok beállítása telefonhívás használatára](security-info-setup-phone-number.md)</li><li>[Biztonsági adatok beállítása biztonsági kulcs használatára](security-info-setup-security-key.md)</li></ul>

## <a name="sign-in-using-an-authenticator-app-notification-on-your-mobile-device"></a>Bejelentkezés a mobileszköz hitelesítő alkalmazásával kapcsolatos értesítés használatával

1. Jelentkezzen be a fiókjába a felhasználónevével és jelszavával.

2. Válassza **a** jóváhagyás lehetőséget a mobileszköz számára küldött jóváhagyási értesítésből.

## <a name="sign-in-using-an-authenticator-app-code-on-your-mobile-device"></a>Bejelentkezés a mobileszközön lévő hitelesítő alkalmazás kódjával

1. Jelentkezzen be a fiókjába a felhasználónevével és jelszavával.

2. Nyissa meg a hitelesítő alkalmazást, és írja be a fiókja véletlenszerűen generált kódját a **kód megadása** mezőbe.

## <a name="sign-in-using-your-phone-number"></a>Bejelentkezés telefonszám használatával

1. Jelentkezzen be a fiókjába a felhasználónevével és jelszavával.

2. Válaszoljon a telefonjára, és kövesse az utasításokat.

## <a name="sign-in-using-a-text-message"></a>Bejelentkezés szöveges üzenettel

1. Jelentkezzen be a fiókjába a felhasználónevével és jelszavával.

2. Nyissa meg a szöveges üzenetet, és írja be a kódot a szöveges üzenetből a **kód megadása** mezőbe.

## <a name="sign-in-using-a-security-key-at-the-lock-screen"></a>Bejelentkezés biztonsági kulccsal a zárolási képernyőn

1. A biztonsági kulcs regisztrálását követően válassza ki a biztonsági kulcs rendszerképét a Windows 10 zárolási képernyőjén.

2. Szúrja be a biztonsági kulcsot az eszköz USB-portjába, és jelentkezzen be a Windowsba a biztonsági kulcs PIN-kódjának használatával.

    ![Biztonsági kulcs bejelentkezés a Windows 10 zárolási képernyőjén](./media/security-info/security-info-windows-10-lock-screen-security-key.png)

## <a name="sign-in-using-a-security-key-and-the-microsoft-edge-browser"></a>Bejelentkezés biztonsági kulccsal és a Microsoft Edge böngésző használatával

1. A biztonsági kulcs regisztrálását követően nyissa meg a Microsoft Edge böngészőt.

2. Amikor a rendszer felszólítja a bejelentkezésre, szúrja be a biztonsági kulcsot az eszköz USB-portjába, és jelentkezzen be a Windowsba a biztonsági kulcs PIN-kódjának használatával.

    ![Biztonsági kulcs bejelentkezés a Microsoft Edge böngésző használatával](./media/security-info/security-info-edge-security-key.png)

    >[!NOTE]
    >További információ a Microsoft Authenticator alkalmazás használatával történő bejelentkezésről: a [Microsoft Authenticator alkalmazás használatával jelentkezzen](user-help-auth-app-sign-in.md)be a fiókjaiba.

## <a name="sign-in-using-another-verification-method"></a>Bejelentkezés másik ellenőrzési módszer használatával

Ha valamilyen okból nem tudja használni az elsődleges bejelentkezési módszerét, egy másik korábban beállított ellenőrzési módszert is használhat.

1. Normál bejelentkezéssel jelentkezzen be a fiókjába, majd a kétlépéses **ellenőrzés** lapon válassza a **bejelentkezés másik módon** hivatkozását.

    ![Bejelentkezés ellenőrzési módszerének módosítása](media/security-info/two-factor-auth-signin-another-way.png)

    >[!Note]
    >Ha nem látja a **Bejelentkezés más módon** hivatkozást, az azt jelenti, hogy nem állított be semmilyen más ellenőrzési módszert, és hogy kapcsolatba kell lépnie a rendszergazdával a fiókjába való bejelentkezéshez. Miután a rendszergazda segítséget nyújt a bejelentkezéshez, győződjön meg róla, hogy további ellenőrzési módszereket ad hozzá. Az ellenőrzési módszerek hozzáadásával kapcsolatos további információkért tekintse [meg a beállítások kezelése](multi-factor-authentication-end-user-manage-settings.md) kétlépéses ellenőrzéshez című cikket.
    >
    >Ha a **Bejelentkezés egy másik módon** hivatkozás jelenik meg, de még mindig nem látja a többi ellenőrzési módszert, forduljon a rendszergazdához, és kérjen segítséget a fiókjához való bejelentkezéshez.

2. Válassza ki az alternatív ellenőrzési módszert, és folytassa a kétlépéses ellenőrzési folyamattal.

3. Miután visszatért a fiókjába, frissítheti az ellenőrzési módszereket (ha szükséges). A módszerek hozzáadásával vagy módosításával kapcsolatos további információkért tekintse meg a [beállítások kezelése](multi-factor-authentication-end-user-manage-settings.md) kétlépéses ellenőrzéshez című cikket.

## <a name="next-steps"></a>További lépések

- A biztonsági adatok [(előzetes verzió) – áttekintés című](user-help-security-info-overview.md) cikkben talál további információt.

- A kétlépéses [ellenőrzés áttekintése című](user-help-two-step-verification-overview.md) cikkben megismerheti a kétlépéses ellenőrzést.

- Ha elvesztette vagy elfelejtette a jelszavát, állítsa alaphelyzetbe a jelszót a [jelszó](https://passwordreset.microsoftonline.com/) -visszaállítási portálról

- Hibaelhárítási tippek és Súgó a bejelentkezési problémákhoz a [nem tud bejelentkezni a Microsoft-fiók](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) cikkbe.
