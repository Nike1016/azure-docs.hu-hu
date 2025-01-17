---
title: Biztonsági adatok (előzetes verzió) beállítása az e-mail-cím használatára – Azure Active Directory | Microsoft Docs
description: A biztonsági adatok beállítása a személyazonosságának ellenőrzéséhez az e-mail-cím használatával.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec93574a8da051b5cf77a37af1030d0c22dfc4a9
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/22/2019
ms.locfileid: "68382895"
---
# <a name="set-up-security-info-preview-to-use-your-email-address"></a>A biztonsági adatok (előzetes verzió) beállítása az e-mail-cím használatára

A jelszó-visszaállítási módszer hozzáadásához kövesse az alábbi lépéseket. Az első beállítás után visszatérhet a **biztonsági** információ lapra a biztonsági adatok hozzáadásához, frissítéséhez vagy törléséhez.

A jelszó-visszaállítási módszer beállítása után a kétfaktoros ellenőrzési módszert is be kell állítania a [hitelesítő alkalmazás](security-info-setup-auth-app.md), a [szöveges üzenetküldés](security-info-setup-text-msg.md)vagy egy [telefonhívás](security-info-setup-phone-number.md)használatával.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

## <a name="set-up-your-email-address-from-the-security-info-page"></a>Az e-mail-cím beállítása a biztonsági adatok lapról

A szervezet beállításaitól függően előfordulhat, hogy az e-mail-címét a biztonsági adatok egyik módszerének megfelelően fogja tudni használni.

>[!Note]
>Javasoljuk, hogy olyan e-mail-címet használjon, amely nem igényli a hálózati jelszó elérését. Ha nem látja az e-mail-címet, lehetséges, hogy a szervezet nem teszi lehetővé e-mailek használatát az ellenőrzéshez. Ebben az esetben másik módszert kell választania, vagy további segítségért forduljon a rendszergazdához.

### <a name="to-set-up-your-email-address"></a>Az e-mail-cím beállítása

1. Jelentkezzen be a munkahelyi vagy iskolai fiókjába, majd lépjen a https://myprofile.microsoft.com/ lapra.

    ![Saját profil oldal, kiemelt biztonsági információs hivatkozások megjelenítése](media/security-info/securityinfo-myprofile-page.png)

2. Válassza a bal oldali navigációs ablaktábla **biztonsági adatok** elemét, vagy a biztonsági **információ** blokkban található hivatkozásra, majd válassza a **metódus hozzáadása** elemet a **biztonsági adatok** lapon.

    ![Biztonsági adatok lap Kiemelt hozzáadási módszer lehetőséggel](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. A **metódus hozzáadása** lapon válassza az **e-mail** lehetőséget a legördülő listából, majd kattintson a **Hozzáadás**gombra.

    ![Hozzáadási mód mező, e-mailben kiválasztva](media/security-info/securityinfo-myprofile-addemail.png)

4. Az **e-mail** lapon írja be az e-mail-címét (például alain@gmail.com:), majd kattintson a **tovább**gombra.

    ![Telefonszám hozzáadása és telefonhívások kiválasztása](media/security-info/securityinfo-myprofile-emailaddress.png)

    >[!Important]
    >Ez az e-mail-cím nem lehet a munkahelyi vagy iskolai e-mail-címe.

5. Írja be a megadott e-mail-címre küldött kódot, majd kattintson a **tovább**gombra.

    ![Telefonszám hozzáadása és szöveges üzenetek kiválasztása](media/security-info/securityinfo-myprofile-emailcode.png)

    A biztonsági adatai frissülnek, és az e-mail-címe alapján ellenőrizheti az identitását a jelszó-visszaállítás használatakor.

## <a name="delete-your-email-address-from-your-security-info-methods"></a>Az e-mail-cím törlése a biztonsági adatok módszereiből

Ha már nem szeretné használni az e-mail-címet biztonsági információként szolgáló módszerként, akkor a **biztonsági adatok** lapról távolíthatja el.

>[!Important]
>Ha tévedésből törli az e-mail-címét, a visszavonás nem vonható vissza. A metódust újra fel kell vennie a jelen cikk [e-mail-cím beállítása](#set-up-your-email-address-from-the-security-info-page) című szakaszának lépéseit követve.

### <a name="to-delete-your-email-address"></a>E-mail-cím törlése

1. A **biztonsági adatok** lapon válassza a **Törlés** hivatkozást az **e-mail** beállítás mellett.

    ![Hivatkozás a telefonos módszer törlésére a biztonsági adatokból](media/security-info/securityinfo-myprofile-emaildelete.png)

2. Az **e-mail-** fiók törléséhez válassza az **Igen** lehetőséget a megerősítő mezőben. Az e-mail fiók törlése után a rendszer eltávolítja a biztonsági adatokat, és eltűnik a **biztonsági adatok** lapról.

## <a name="additional-security-info-methods"></a>További biztonsági információs módszerek

További beállításokkal is megtudhatja, hogy a szervezet hogyan forduljon a személyazonosságának ellenőrzéséhez a you're alapján. A lehetőségek a következők:

- **Hitelesítő alkalmazás.** Töltse le és használjon egy hitelesítő alkalmazást a jóváhagyási értesítés vagy a véletlenszerűen generált jóváhagyási kód beszerzéséhez a kétlépéses ellenőrzéshez vagy a jelszó-visszaállításhoz. A Microsoft Authenticator alkalmazás beállításával és használatával kapcsolatos részletes utasításokért lásd: [biztonsági adatok beállítása hitelesítő alkalmazás használatára](security-info-setup-auth-app.md).

- **Mobileszköz szövege.** Adja meg a mobileszköz számát, és szerezzen be egy, a kétlépéses ellenőrzéshez vagy a jelszó-visszaállításhoz használni kívánt kódot. Az identitás szöveges üzenettel (SMS) való ellenőrzésével kapcsolatos részletes utasításokért lásd: [biztonsági információk beállítása szöveges üzenetküldés (SMS) használatára](security-info-setup-text-msg.md).

- **Mobileszköz vagy munkahelyi telefonhívás.** Adja meg a mobileszköz számát, és telefonhívást kap a kétlépéses ellenőrzéshez vagy a jelszó alaphelyzetbe állításához. Az identitás telefonszámmal történő ellenőrzésével kapcsolatos részletes útmutatásért lásd: [biztonsági információk beállítása telefonhívások használatához](security-info-setup-phone-number.md).

- **Biztonsági kulcs.** Regisztrálja Microsoft-kompatibilis biztonsági kulcsát, és használja PIN-kóddal a kétlépéses ellenőrzéshez vagy a jelszó alaphelyzetbe állításához. Ha részletes útmutatást szeretne arról, hogyan ellenőrizheti személyazonosságát egy biztonsági kulccsal, tekintse meg a biztonsági [adatok beállítása biztonsági kulcs használatára](security-info-setup-security-key.md)című témakört.

- **Biztonsági kérdések.** Válaszoljon a szervezete rendszergazdája által létrehozott biztonsági kérdésekre. Ez a beállítás csak a jelszó-visszaállításhoz érhető el, és nem a kétlépéses ellenőrzéshez. A biztonsági kérdések beállításával kapcsolatos részletes utasításokért tekintse meg a biztonsági [kérdések beállítása a biztonsági kérdésekre](security-info-setup-questions.md) című cikket.

    >[!Note]
    >Ha a lehetőségek némelyike hiányzik, valószínűleg azért, mert a szervezet nem engedélyezi ezeket a metódusokat. Ebben az esetben ki kell választania egy elérhető módszert, vagy kapcsolatba kell lépnie a rendszergazdával további segítségért.

## <a name="next-steps"></a>További lépések

- Ha elvesztette vagy elfelejtette a jelszavát, állítsa vissza a jelszót a [jelszó](https://passwordreset.microsoftonline.com/) -visszaállítási portálról, vagy kövesse a [munkahelyi vagy iskolai jelszó](user-help-reset-password.md) alaphelyzetbe állítása című cikk lépéseit.

- Hibaelhárítási tippek és Súgó a bejelentkezési problémákhoz a [nem tud bejelentkezni a Microsoft-fiók](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) cikkbe.
