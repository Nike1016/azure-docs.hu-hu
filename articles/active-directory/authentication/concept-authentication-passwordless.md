---
title: Jelszó nélküli bejelentkezés Azure Active Directory (előzetes verzió)
description: Jelszó nélküli bejelentkezés az Azure AD-be az FIDO2 biztonsági kulcsaival vagy a Microsoft Authenticator alkalmazással (előzetes verzió)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3ae8f6854241240249cb3b7494872cbbd8fd41e6
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68823767"
---
# <a name="what-is-passwordless"></a>Mi az a jelszó?

A többtényezős hitelesítés (MFA) egy nagyszerű módszer a szervezet biztonságossá tételére, de a felhasználók a további rétegekkel is megjegyezik a jelszavukat. A jelszóval nem rendelkező hitelesítési módszerek sokkal kényelmesebbek, mert a jelszó el lett távolítva, és lecserélve valamire, amit Ön, vagy amit tud.

|   | Valamilyen dolog | Amit Ön vagy ismer |
| --- | --- | --- |
| Jelszó nélküli | Telefon vagy biztonsági kulcs | Biometrikus vagy PIN-kód |

A hitelesítéshez minden szervezetnek eltérő igényeire van szüksége. A Microsoft jelenleg a Windows Hello-et, a Windows rendszerű számítógépeket kínálja. Felvesszük a Microsoft Authenticator alkalmazást és a FIDO2 biztonsági kulcsait a jelszóval nem rendelkező családba.

## <a name="microsoft-authenticator-app"></a>Microsoft Authenticator alkalmazás

Annak engedélyezése, hogy az alkalmazott telefonja jelszavas hitelesítésen alapuló hitelesítési módszer legyen. Előfordulhat, hogy a jelszó mellett már használja a Microsoft Authenticator alkalmazást kényelmes multi-Factor Authentication beállításként. Mostantól azonban jelszó nélkül is elérhető.

![Bejelentkezés a Microsoft Edge-be a Microsoft Authenticator alkalmazással](./media/concept-authentication-passwordless/concept-web-sign-in-microsoft-authenticator-app.png)

Az iOS-vagy Android-telefonokat egy erős, jelszó nélküli hitelesítő adatba helyezi, mivel lehetővé teszi, hogy a felhasználók bármilyen platformra vagy böngészőbe bejelentkezzenek, ha értesítést küldenek a telefonján, és a képernyőn megjelenő számot megadják a telefonján, majd a biometrikus adatokat használják ( érintés vagy szembenézés) vagy PIN-kód a megerősítéshez.

## <a name="fido2-security-keys"></a>FIDO2 biztonsági kulcsok

A FIDO2 biztonsági kulcsai egy nem adattípusra épülő, szabványos jelszavas hitelesítési módszer, amely bármilyen típusú tényezőt tartalmazhat. A gyors identitású online (pont) egy nyílt szabvány a jelszó nélküli hitelesítéshez. Lehetővé teszi, hogy a felhasználók és a szervezetek a standard használatával bejelentkezzenek az erőforrásaik számára Felhasználónév vagy jelszó nélkül, egy külső biztonsági kulccsal vagy egy eszközre épített platform-kulccsal.

A nyilvános előzetes verzióban az alkalmazottak külső biztonsági kulcsok használatával jelentkezhetnek be a Azure Active Directory csatlakoztatott Windows 10 rendszerű gépekre (1809-es vagy újabb verzióra), és egyszeri bejelentkezést kapnak a felhőalapú erőforrásaik számára. A támogatott böngészőkbe is bejelentkezhetnek.

![Bejelentkezés a Microsoft Edge-be egy biztonsági kulccsal](./media/concept-authentication-passwordless/concept-web-sign-in-security-key.png)

Noha a FIDO2 számos kulcsot használ, a Microsoft a FIDO2 CTAP-specifikáció néhány opcionális kiterjesztését igényli a gyártó megvalósításához, így biztosítva a maximális biztonságot és a legjobb élményt.

A biztonsági kulcsnak a következő szolgáltatásokat és bővítményeket **kell** megvalósítania a FIDO2 CTAP-protokollból, hogy a Microsoft-kompatibilis legyen:

| # | Szolgáltatás/bővítmény megbízhatósága | Miért szükséges a funkció vagy a bővítmény? |
| --- | --- | --- |
| 1 | Rezidens kulcs | Ez a funkció lehetővé teszi, hogy a biztonsági kulcs hordozható legyen, ahol a hitelesítő adatokat a biztonsági kulcs tárolja. |
| 2 | Ügyfél PIN-kódja | Ez a funkció lehetővé teszi, hogy a hitelesítő adatait egy második tényezővel megvédje, és azokra a biztonsági kulcsokra vonatkozzon, amelyek nem rendelkeznek felhasználói felülettel. |
| 3 | HMAC – titkos kód | Ez a bővítmény biztosítja, hogy bejelentkezzen az eszközre, amikor az offline vagy a repülőgép üzemmódban van. |
| 4 | Több fiók/RP | Ez a funkció biztosítja, hogy ugyanazt a biztonsági kulcsot használja több szolgáltatáshoz, például a Microsoft-fiókhoz és a Azure Active Directory. |

A következő szolgáltatók olyan FIDO2 biztonsági kulcsokat kínálnak, amelyekről ismert, hogy kompatibilisek legyenek a paswordy felülettel. A Microsoft arra bátorítja az ügyfeleket, hogy értékelik a kulcsok biztonsági tulajdonságait, ha kapcsolatba lép a szállítóval, valamint a következővel:.

| Szolgáltató | Kapcsolattartó |
| --- | --- |
| Yubico | [https://www.yubico.com/support/contact/](https://www.yubico.com/support/contact/) |
| Feitian | [https://www.ftsafe.com/about/Contact_Us](https://www.ftsafe.com/about/Contact_Us) |
| HID FÁJLOK | [https://www.hidglobal.com/contact-us](https://www.hidglobal.com/contact-us) |
| Ensurity | [https://ensurity.com/contact-us.html](https://ensurity.com/contact-us.html) |
| eWBM | [https://www.ewbm.com/page/sub1_5](https://www.ewbm.com/page/sub1_5) |

Ha Ön szállító, és szeretné lekérni az eszközt ezen a listán, forduljon [Fido2Request@Microsoft.com](mailto:Fido2Request@Microsoft.com)a következőhöz:.

A FIDO2 biztonsági kulcsai nagyszerű lehetőséget biztosítanak olyan nagyvállalatok számára, akik nagyon érzékenyek a biztonságra, vagy olyan forgatókönyvekkel vagy alkalmazottakkal rendelkeznek, akik nem hajlandók vagy nem tudják használni a telefont második tényezőként.

## <a name="what-scenarios-work-with-the-preview"></a>Milyen forgatókönyvek működnek az előzetes verzióban?

- A rendszergazdák engedélyezhetik a jelszóval nem rendelkező hitelesítési módszereket a bérlők számára
- A rendszergazdák az összes felhasználót megcélozhatja, vagy kiválaszthatják a bérlőn belüli felhasználókat és csoportokat az egyes módszereknél
- A végfelhasználók a fiók-portálon regisztrálhatják és kezelhetik ezeket a jelszó nélküli hitelesítési módszereket
- A végfelhasználók ezekkel a jelszó nélküli hitelesítési módszerekkel jelentkezhetnek be
   - Microsoft Authenticator alkalmazás: Olyan forgatókönyvekben fog működni, ahol az Azure AD-hitelesítés használatban van, beleértve az összes böngészőt, a Windows 10-es (OOBE) beállítását és az integrált Mobile apps-t bármely operációs rendszeren.
   - Biztonsági kulcsok: A a Windows 10 1809-es vagy újabb verziójának zárolási képernyőjén, illetve a weben támogatott böngészőkben, például a Microsoft Edge-ben fog működni.

## <a name="next-steps"></a>További lépések

[A FIDO2 biztonsági kulcs passwordlesss beállításainak engedélyezése a szervezetben](howto-authentication-passwordless-security-key.md)

[Telefonos jelszó-megadási lehetőségek engedélyezése a szervezetben](howto-authentication-passwordless-phone.md)

### <a name="external-links"></a>Külső hivatkozások

[A-Szövetség](https://fidoalliance.org/)

[FIDO2 CTAP-specifikáció](https://fidoalliance.org/specs/fido-v2.0-id-20180227/fido-client-to-authenticator-protocol-v2.0-id-20180227.html)
