---
title: A biztonsági adatok (előzetes verzió) beállítása telefonhívások használatához – Azure Active Directory | Microsoft Docs
description: A biztonsági adatok beállítása az identitás telefonos hívásokkal való ellenőrzéséhez.
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
ms.openlocfilehash: fbac4f364dedc28f7d5f3e100481a56bde70b4d3
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/22/2019
ms.locfileid: "68382924"
---
# <a name="set-up-security-info-preview-to-use-phone-calls"></a>A biztonsági adatok (előzetes verzió) beállítása telefonhívások használatához

A kétfaktoros ellenőrzési és jelszó-visszaállítási módszerek hozzáadásához kövesse az alábbi lépéseket. Az első beállítás után visszatérhet a **biztonsági** információ lapra a biztonsági adatok hozzáadásához, frissítéséhez vagy törléséhez.

Ha a rendszer a munkahelyi vagy iskolai fiókjába való bejelentkezés után azonnal beállítja ezt a beállítását, tekintse meg a [biztonsági adatok beállítása a bejelentkezési oldalról](security-info-setup-signin.md) című cikk részletes lépéseit.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

> [!Note]
> A biztonsági adatok nem támogatják a telefonos bővítmények használatát. Ha a megfelelő formátumot (+ 1 4255551234X12345) adja hozzá, a bővítmények el lesznek távolítva a hívás elhelyezése előtt.
>
> Ha nem jelenik meg a telefonos lehetőség, akkor előfordulhat, hogy a szervezet nem teszi lehetővé telefonszámok használatát az ellenőrzéshez. Ebben az esetben másik módszert kell választania, vagy további segítségért forduljon a rendszergazdához.

## <a name="set-up-phone-calls-from-the-security-info-page"></a>Telefonhívások beállítása a biztonsági adatok lapról

A szervezet beállításaitól függően előfordulhat, hogy telefonos hívásokat is használhat a biztonsági adatok egyik módjaként.

>[!Note]
>Ha telefonhívás helyett SMS-üzenetet szeretne kapni, kövesse a [biztonsági információ beállítása a szöveges üzenetküldéshez](security-info-setup-text-msg.md) című cikk lépéseit.

### <a name="to-set-up-phone-calls"></a>Telefonhívások beállítása

1. Jelentkezzen be a munkahelyi vagy iskolai fiókjába, majd lépjen a https://myprofile.microsoft.com/ lapra.

    ![Saját profil oldal, kiemelt biztonsági információs hivatkozások megjelenítése](media/security-info/securityinfo-myprofile-page.png)

2. Válassza a bal oldali navigációs ablaktábla **biztonsági adatok** elemét, vagy a biztonsági **információ** blokkban található hivatkozásra, majd válassza a **metódus hozzáadása** elemet a **biztonsági adatok** lapon.

    ![Biztonsági adatok lap Kiemelt hozzáadási módszer lehetőséggel](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. A **metódus hozzáadása** lapon válassza a **telefon** lehetőséget a legördülő listából, majd kattintson a **Hozzáadás**gombra.

    ![Hozzáadás a Method Box-hoz, a kiválasztott telefonnal](media/security-info/securityinfo-myprofile-addphonetext.png)

4. A **telefon** lapon írja be a mobileszköz telefonszámát, válassza a Hívjon meg lehetőséget, majd kattintson a **tovább**gombra.

    ![Telefonszám hozzáadása és telefonhívások kiválasztása](media/security-info/securityinfo-myprofile-phonecall-addnumber.png)

5. Válaszolja meg az ellenőrző telefonhívást, amelyet a megadott telefonszámra továbbít, és kövesse az utasításokat.

    A lap úgy változik, hogy megmutassa a sikert.

    ![Sikeres értesítés, a telefonszám csatlakoztatása, a telefonhívások fogadásának lehetősége és a fiók](media/security-info/securityinfo-myprofile-phonetext-success.png)

    A biztonsági adatok frissülnek, és telefonhívások segítségével ellenőrizheti az identitást, ha kétlépéses ellenőrzést vagy jelszó-visszaállítást használ. Ha telefonhívást szeretne készíteni az alapértelmezett módszerről, tekintse meg a jelen cikk [alapértelmezett biztonsági adatok módosítása módszerét](#change-your-default-security-info-method) ismertető szakaszát.

## <a name="delete-phone-calls-from-your-security-info-methods"></a>Telefonhívások törlése a biztonsági adatok módszereiből

Ha a továbbiakban nem szeretné használni a telefonhívásokat biztonsági információként szolgáló módszerként, akkor a **biztonsági adatok** lapról távolíthatja el.

>[!Important]
>Ha a telefonhívásokat tévedésből törli, a visszavonás nem vonható vissza. A metódust újra fel kell vennie a jelen cikk [telefonhívások beállítása](#set-up-phone-calls-from-the-security-info-page) című részében ismertetett lépéseket követve.

### <a name="to-delete-phone-calls"></a>Telefonhívások törlése

1. A **biztonsági adatok** lapon válassza a **Törlés** hivatkozást a **telefonos** beállítás mellett.

    ![Hivatkozás a telefonos módszer törlésére a biztonsági adatokból](media/security-info/securityinfo-myprofile-phonetext-delete.png)

2. A **telefonszám** törléséhez válassza az **Igen** lehetőséget a megerősítő mezőben. A telefonszám törlése után a rendszer eltávolítja a biztonsági adatokból, és eltűnik a **biztonsági adatok** lapról. Ha a **telefon** az alapértelmezett módszer, akkor az alapértelmezett érték egy másik elérhető metódusra változik.

## <a name="change-your-default-security-info-method"></a>Az alapértelmezett biztonsági adatok módszerének módosítása

Ha azt szeretné, hogy a telefonhívások alapértelmezett módszerként legyenek használatban a munkahelyi vagy iskolai fiókba való bejelentkezéskor a kétfaktoros ellenőrzés vagy a jelszó-átállítási kérelmek esetében, a **biztonsági adatok** lapon állíthatja be.

### <a name="to-change-your-default-security-info-method"></a>Az alapértelmezett biztonsági adatok módszerének módosítása

1. A **biztonsági adatok** lapon válassza az **alapértelmezett bejelentkezési módszer** adatainak melletti **módosítás** hivatkozást.

    ![Az alapértelmezett bejelentkezési módszer hivatkozásának módosítása](media/security-info/securityinfo-myprofile-phonetext-defaultchange.png)

2. Válassza a **telefonhívás ( *_your_phone_number_* )** lehetőséget az elérhető módszerek legördülő listájában, majd válassza a Confirm ( **megerősítés**) lehetőséget.

    ![Válassza ki a metódust az alapértelmezett bejelentkezéshez](media/security-info/securityinfo-myprofile-phonecall-changeddefault.png)

    A bejelentkezéshez használt alapértelmezett módszer a **telefonos híváshoz ( *_your_phone_number_* )** .

## <a name="additional-security-info-methods"></a>További biztonsági információs módszerek

További beállításokkal is megtudhatja, hogy a szervezet hogyan forduljon a személyazonosságának ellenőrzéséhez a you're alapján. A lehetőségek a következők:

- **Hitelesítő alkalmazás.** Töltse le és használjon egy hitelesítő alkalmazást a jóváhagyási értesítés vagy a véletlenszerűen generált jóváhagyási kód beszerzéséhez a kétlépéses ellenőrzéshez vagy a jelszó-visszaállításhoz. A Microsoft Authenticator alkalmazás beállításával és használatával kapcsolatos részletes utasításokért lásd: [biztonsági adatok beállítása hitelesítő alkalmazás használatára](security-info-setup-auth-app.md).

- **Mobileszköz szövege.** Adja meg a mobileszköz számát, és szerezzen be egy, a kétlépéses ellenőrzéshez vagy a jelszó-visszaállításhoz használni kívánt kódot. Az identitás szöveges üzenettel (SMS) való ellenőrzésével kapcsolatos részletes utasításokért lásd: [biztonsági információk beállítása szöveges üzenetküldés (SMS) használatára](security-info-setup-text-msg.md).

- **Biztonsági kulcs.** Regisztrálja Microsoft-kompatibilis biztonsági kulcsát, és használja PIN-kóddal a kétlépéses ellenőrzéshez vagy a jelszó alaphelyzetbe állításához. Ha részletes útmutatást szeretne arról, hogyan ellenőrizheti személyazonosságát egy biztonsági kulccsal, tekintse meg a biztonsági [adatok beállítása biztonsági kulcs használatára](security-info-setup-security-key.md)című témakört.

- **E-mail-cím.** Adja meg a munkahelyi vagy iskolai e-mail-címét, hogy e-mailt kapjon a jelszó alaphelyzetbe állításához. Ez a beállítás kétlépéses ellenőrzés esetén nem érhető el. Az e-mailek beállításával kapcsolatos részletes útmutatásért lásd: [biztonsági adatok beállítása e-mailek használatára](security-info-setup-email.md).

- **Biztonsági kérdések.** Válaszoljon a szervezete rendszergazdája által létrehozott biztonsági kérdésekre. Ez a beállítás csak a jelszó-visszaállításhoz érhető el, és nem a kétlépéses ellenőrzéshez. A biztonsági kérdések beállításával kapcsolatos részletes utasításokért tekintse meg a biztonsági [kérdések beállítása a biztonsági kérdésekre](security-info-setup-questions.md) című cikket.

    >[!Note]
    >Ha a lehetőségek némelyike hiányzik, valószínűleg azért, mert a szervezet nem engedélyezi ezeket a metódusokat. Ebben az esetben ki kell választania egy elérhető módszert, vagy kapcsolatba kell lépnie a rendszergazdával további segítségért.

## <a name="next-steps"></a>További lépések

- Ha elvesztette vagy elfelejtette a jelszavát, állítsa vissza a jelszót a [jelszó](https://passwordreset.microsoftonline.com/) -visszaállítási portálról, vagy kövesse a [munkahelyi vagy iskolai jelszó](user-help-reset-password.md) alaphelyzetbe állítása című cikk lépéseit.

- Hibaelhárítási tippek és Súgó a bejelentkezési problémákhoz a [nem tud bejelentkezni a Microsoft-fiók](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) cikkbe.
