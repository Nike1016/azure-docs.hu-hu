---
title: A Microsoft Authenticator alkalmazás áttekintése – Azure Active Directory | Microsoft Docs
description: Ismerkedjen meg a Microsoft Authenticator alkalmazással, beleértve a mi is, hogyan működik, és milyen információkat tartalmaz a tartalom ezen szakasza.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: overview
ms.date: 01/24/2019
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: d3766436851cb2f31fa7bb070402191e03ad9ad1
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/22/2019
ms.locfileid: "68382335"
---
# <a name="what-is-the-microsoft-authenticator-app"></a>Mi a Microsoft Authenticator alkalmazás?

>[!Important]
>Ezek az anyagok felhasználók számára készültek. Amennyiben Ön rendszergazda, az Azure Active Directory- (Azure AD-) környezet beállításával és kezelésével kapcsolatosan további információt az [Azure Active Directory dokumentációjában](https://docs.microsoft.com/azure/active-directory) talál.

A Microsoft Authenticator alkalmazás segít bejelentkezni a fiókjába, ha kétfaktoros ellenőrzést használ. A kétfaktoros ellenőrzés segítségével biztonságosabban férhet hozzá a fiókokhoz, különösen a bizalmas adatok megtekintésekor. Mivel a jelszavak elfelejthető, ellopott vagy sérült, a kétfaktoros ellenőrzés egy további biztonsági lépés, amely segít a fiók védelmében azáltal, hogy nehezebbé teszi a más személyek számára a betörést.

A Microsoft Authenticator alkalmazást többféleképpen is használhatja, többek között:

- Válaszoljon a hitelesítésre, miután bejelentkezett felhasználónevével és jelszavával.

- Bejelentkezés jelszó megadása nélkül, a felhasználónévvel, a hitelesítő alkalmazással és a mobileszközön, az ujjlenyomattal, a Face vagy a PIN-kóddal.

- A hitelesítő alkalmazásokat támogató egyéb fiókok számára.

> [!Important]
> A Microsoft Authenticator alkalmazás minden olyan fiókkal működik, amely kétfaktoros ellenőrzést használ, és támogatja az idő-alapú egyszeri jelszó (TOTP) szabványait.
>
> Előfordulhat, hogy a szervezete hitelesítő alkalmazást használ a bejelentkezéshez, és hozzáfér a szervezeti adataihoz és dokumentumaihoz. Bár a Felhasználónév megjelenhet az alkalmazásban, a fiók valójában nem úgy van beállítva, hogy ellenőrzési módszerként működjön, amíg el nem végzi a regisztrációs folyamatot. További információkért lásd: [munkahelyi vagy iskolai fiók hozzáadása](user-help-auth-app-add-work-school-account.md).
> 
> Ha problémába ütközik a fiókjába való bejelentkezéssel, akkor a következő témakörben talál segítséget: [Ha nem tud bejelentkezni](https://support.microsoft.com/help/12429) a Microsoft-fiókba. További információ arról, hogy mi a teendő, ha ["a Microsoft-fiók nem létezik"](https://support.microsoft.com/help/13811) üzenet jelenik meg, amikor megpróbál bejelentkezni a Microsoft-fiókba.

## <a name="terminology"></a>Terminológia

| Kifejezés|Leírás|
| ----|-----------|
| Kétfaktoros ellenőrzés | Ellenőrzési folyamat, amely megköveteli, hogy csak két ellenőrző adatot használjon, például egy jelszót és egy PIN-kódot. A Microsoft Authenticator alkalmazás a standard Kéttényezős ellenőrzést és a jelszó nélküli bejelentkezést is támogatja. |
| Multi-Factor Authentication (MFA) | Az összes kétfaktoros ellenőrzés többtényezős hitelesítés, amely megköveteli, hogy *legalább* két, a szervezet igényeinek megfelelő ellenőrző adatot használjon. |
| Microsoft-fiók (más néven, MSA) | Saját személyes fiókjait hozhat létre, hogy hozzáférjen a Microsoft-termékekhez és a felhőalapú szolgáltatásokhoz, például az Outlookhoz, a OneDrive, az Xbox LIVE-hoz vagy az Office 365-hoz. A Microsoft-fiók létrehozása és a Microsoft fogyasztói identitás-fiókrendszer a Microsoft által futtatott tárolja. |
| Munkahelyi vagy iskolai fiók | A szervezet létrehozza munkahelyi vagy iskolai fiókját (például alain@contoso.com), hogy lehetővé tegye a belső és potenciálisan korlátozott erőforrások, például a Microsoft Azure, a Windows Intune és az Office 365 elérését. |
| Ellenőrzőkód | A hitelesítő alkalmazásban az egyes hozzáadott fiókok alatt megjelenő hat számjegyű kód. Az ellenőrző kód 30 másodpercenként változik, ami megakadályozza, hogy valaki többször is felhasználja a kódot. Ezt az egyszeri jelszót (OTP) is nevezik. |

## <a name="how-two-factor-verification-works-with-the-app"></a>A kétfaktoros ellenőrzés működése az alkalmazással

A két tényező ellenőrzése a Microsoft Authenticator alkalmazással működik a következő módokon:

- **Értesítési.** Írja be a felhasználónevét és jelszavát a munkahelyi vagy iskolai fiókjába vagy a személyes Microsoft-fiókba bejelentkezett eszközre, majd a Microsoft Authenticator alkalmazás értesítést küld, amely arra kéri, hogy **hagyja jóvá**a bejelentkezést. Ha felismeri a bejelentkezési kísérletet, válassza a **jóváhagyás** lehetőséget. Ellenkező esetben válassza a **Megtagadás**lehetőséget. Ha a **Megtagadás**lehetőséget választja, akkor a kérést csalárdként is megjelölheti.

- **Ellenőrző kód.** Írja be a felhasználónevét és jelszavát a munkahelyi vagy iskolai fiókjába vagy a személyes Microsoft-fiókba bejelentkezett eszközre, majd másolja a társított ellenőrző kódot a Microsoft Authenticator alkalmazás **fiókok** képernyőjéről. Az ellenőrző kód egyszeri jelszavas (OTP) hitelesítésként is ismert.

- **Jelszó nélküli bejelentkezés.** Írja be a felhasználónevét arra az eszközre, amelyet be szeretne jelentkezni a munkahelyi vagy iskolai fiókjába vagy a személyes Microsoft-fiókba, majd a mobileszköz használatával ellenőrizze, hogy az Ön ujjlenyomatát, arcát vagy PIN-kódját használja-e. Ennél a módszernél nem kell megadnia a jelszavát.

### <a name="whether-to-use-your-devices-biometric-capabilities"></a>Azt határozza meg, hogy az eszköz biometrikus képességeit használja-e

Ha PIN-kódot használ a hitelesítési folyamat elvégzéséhez, beállíthatja a Microsoft Authenticator alkalmazást úgy, hogy ehelyett az eszköz ujjlenyomatát vagy Arcfelismerés (biometrikus) képességeit használja. Ezt beállíthatja úgy, hogy a hitelesítő alkalmazás első használatakor ellenőrizze a fiókját. Ehhez válassza a PIN-kód helyett az eszköz biometrikus funkcióinak azonosítására szolgáló lehetőséget.

## <a name="who-decides-if-you-use-this-feature"></a>Ki dönti el, hogy használja-e ezt a funkciót?

A fiók típusától függően a szervezet dönthet úgy, hogy kétfaktoros ellenőrzést kell használnia, vagy Ön is dönthet úgy, hogy eldönti.

- **Munkahelyi vagy iskolai fiókjával.** Ha munkahelyi vagy iskolai fiókot használ (például alain@contoso.com:), akkor akár a szervezet számára is, akár kétfaktoros ellenőrzést kell használnia, az adott ellenőrzési módszerekkel együtt. A munkahelyi vagy iskolai fióknak a Microsoft Authenticator alkalmazáshoz való hozzáadásával kapcsolatos további információkért lásd: [munkahelyi vagy iskolai fiókok hozzáadása](user-help-auth-app-add-work-school-account.md).

- **Személyes Microsoft-fiók.** Dönthet úgy, hogy a személyes Microsoft-fiókjaihoz (például alain@outlook.com:) kétfaktoros ellenőrzést állít be. A személyes Microsoft-fiók hozzáadásával kapcsolatos további információkért tekintse [meg a személyes fiókjainak hozzáadása](user-help-auth-app-add-personal-ms-account.md)című témakört.

- **Nem Microsoft-fiók.** Beállíthatja, hogy a nem Microsoft-fiókokhoz (például alain@gmail.com) Kéttényezős ellenőrzést állítson be. Előfordulhat, hogy a nem Microsoft-fiókok nem használják a kétfaktoros hitelesítést, de a **biztonsági** vagy **bejelentkezési** beállításokon belül meg kell keresnie a szolgáltatást. A Microsoft Authenticator alkalmazás minden olyan fiókkal működik, amely támogatja az TOTP szabványokat. A nem Microsoft-fiókok hozzáadásával kapcsolatos további információkért lásd [a nem Microsoft-fiókok hozzáadását](user-help-auth-app-add-non-ms-account.md)ismertető témakört.

## <a name="in-this-section"></a>Ebben a szakaszban

| Cikk | Leírás |
| ------ | ------------ |
| [Az alkalmazás letöltése és telepítése](user-help-auth-app-download-install.md) | Leírja, hogy hol és hogyan lehet beolvasni és telepíteni a Microsoft Authenticator alkalmazást az Android és az iOS rendszerű eszközökön. |
| [Munkahelyi vagy iskolai fiókjainak hozzáadása](user-help-auth-app-add-work-school-account.md) | Ismerteti, hogyan adhat hozzá különböző munkahelyi vagy iskolai és személyes fiókokat a Microsoft Authenticator alkalmazáshoz. |
| [Személyes fiókok hozzáadása](user-help-auth-app-add-personal-ms-account.md) | Ismerteti, hogyan adhat hozzá személyes Microsoft-fiókokat a Microsoft Authenticator alkalmazáshoz. |
| [A nem Microsoft-fiókok hozzáadása](user-help-auth-app-add-non-ms-account.md) | Ismerteti, hogyan adhat hozzá nem Microsoft-fiókokat a Microsoft Authenticator alkalmazáshoz. |
| [Fiókok manuális hozzáadása](user-help-auth-app-add-account-manual.md) | Ismerteti, hogyan lehet manuálisan hozzáadni a fiókokat a Microsoft Authenticator alkalmazáshoz, ha nem tudja beolvasni a megadott QR-kódot. |
| [Bejelentkezés az alkalmazás használatával](user-help-auth-app-sign-in.md) | Leírja, hogyan jelentkezhet be a különböző fiókjaiba az Microsoft Authenticator alkalmazás használatával.|
| [A fiók hitelesítő adatainak biztonsági mentése és helyreállítása](user-help-auth-app-backup-recovery.md) | Információkat biztosít a fiók hitelesítő adatainak biztonsági mentéséhez és visszaállításához a Microsoft Authenticator alkalmazás használatával. |
| [A Microsoft Authenticator alkalmazással kapcsolatos gyakori kérdések](user-help-auth-app-faq.md) | Az alkalmazással kapcsolatos gyakori kérdésekre ad választ. |
