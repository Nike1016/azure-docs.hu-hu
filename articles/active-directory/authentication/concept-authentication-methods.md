---
title: Hitelesítési módszerek – Azure Active Directory
description: Az Azure AD-ben elérhető hitelesítési módszerek az MFA és a SSPR esetében
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry, michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: d18c74e908f6d5c4c3d2eefb0518add380fa1324
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69561316"
---
# <a name="what-are-authentication-methods"></a>Mik azok a hitelesítési módszerek?

Rendszergazdaként válassza az Azure multi-Factor Authentication és az önkiszolgáló jelszó-visszaállítás (SSPR) hitelesítési módszerei lehetőséget, ha a felhasználóknak több hitelesítési módszert kell regisztrálniuk. Ha egy felhasználó hitelesítési módszere nem érhető el, dönthet úgy, hogy egy másik módszerrel végez hitelesítést.

A rendszergazdák meghatározhatják a szabályzatban, hogy mely hitelesítési módszerek érhetők el a SSPR és az MFA felhasználói számára. Előfordulhat, hogy egyes hitelesítési módszerek nem állnak rendelkezésre az összes szolgáltatás számára. A szabályzatok konfigurálásával kapcsolatos további információkért tekintse meg a cikk az [önkiszolgáló jelszó-visszaállítás sikeres](howto-sspr-deployment.md) kiszámítását és [a felhőalapú Azure multi-Factor Authentication tervezését](howto-mfa-getstarted.md) ismertető cikket.

A Microsoft nagymértékben javasolja a rendszergazdáknak, hogy a minimálisan szükséges számú hitelesítési módszer közül többet válasszanak, ha nem férnek hozzájuk.

|Hitelesítési módszer|Használat|
| --- | --- |
| Windows 10 | MFA és SSPR |
| Biztonsági kérdések | Csak SSPR |
| E-mail-cím | Csak SSPR |
| Microsoft Authenticator alkalmazás | MFA és SSPR |
| A hardver-token ESKÜje | Az MFA és a SSPR nyilvános előzetes verziója |
| SMS | MFA és SSPR |
| Hanghívás | MFA és SSPR |
| Alkalmazásjelszavak | MFA csak bizonyos esetekben |

![A bejelentkezési képernyőn használt hitelesítési módszerek](media/concept-authentication-methods/overview-login.png)

|     |
| --- |
| Az MFA és a SSPR-hez tartozó hardver-tokenek a Azure Active Directory nyilvános előzetes verziójának funkciói. További információ az előzetes verziókról: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="password"></a>Windows 10

Az Azure AD-jelszó hitelesítési módszernek minősül. Ez az egyetlen olyan metódus, amely **nem tiltható le**.

## <a name="security-questions"></a>Biztonsági kérdések

A biztonsági kérdések **csak az Azure ad** önkiszolgáló jelszó-visszaállítási szolgáltatásában érhetők el a nem rendszergazdai fiókokra.

Ha biztonsági kérdéseket használ, azt javasoljuk, hogy más módszerekkel együtt használja őket. A biztonsági kérdések kevésbé biztonságosak, mint más metódusok, mert egyes felhasználók tudják, hogy a válaszokat egy másik felhasználó kérdéseire.

> [!NOTE]
> A biztonsági kérdések tárolása a címtárban található felhasználói objektumon történik, és a felhasználók csak a regisztráció során válaszolnak. A rendszergazda nem tudja beolvasni vagy módosítani a felhasználó kérdéseit és válaszait.
>

### <a name="predefined-questions"></a>Előre meghatározott kérdések

* Melyik városban ismerkedett meg az első házastársával/párjával?
* Melyik településen találkoztak a szülei?
* Melyik városban lakik a legközelebbi rokona?
* Az édesapja melyik városban született?
* Melyik városban volt az első munkahelye?
* Melyik városban született az édesanyja?
* Hol töltötte a 2000. év újévét?
* Mi a vezetékneve és az utóneve a kedvenc középiskolai tanárának?
* Milyen felsőoktatási intézménybe jelentkezett, de nem vették fel?
* Hogy hívták a helyet, ahol az első lakodalmát tartották?
* Mi az apja második utóneve?
* Mi a kedvenc étele?
* Anyai nagyanyja vezeték- és keresztneve?
* Mi az édesanyja második utóneve?
* Mi a legrégebbi testvére születésnapjának hónapja és éve? (például 1985. november)
* Mi a második utóneve a legidősebb testvérének?
* Mi a vezetékneve és az utóneve az apai nagyapjának?
* Mi a második utóneve a legfiatalabb testvérének?
* Melyik iskolában végezte el a hatodik osztályt?
* Mi volt a vezetékneve és az utóneve a legjobb gyerekkori barátjának?
* Mi volt a vezetékneve és az utóneve az első szerelmének?
* Mi volt a kedvenc általános iskolai tanárának vezetékneve?
* Mi volt az első autója vagy motorkerékpárja márkája és típusa?
* Hogy hívták az első iskoláját?
* Mi a neve annak a kórháznak, ahol született?
* Milyen utcában lakott gyermekkorában?
* Hogy hívják a gyermekkori példaképét?
* Kedvenc plüssállatának neve?
* Mi volt az első háziállatának neve?
* Mi volt a beceneve gyerekkorában?
* Mi volt a kedvenc sportja a középiskolában?
* Mi volt az első munkaköre?
* Mi volt a gyerekkori telefonszámának utolsó négy számjegye?
* Gyerekkorában mi szeretett volna lenni, ha majd felnő?
* Ki a leghíresebb ember, akivel életében találkozott?

Az előre definiált biztonsági kérdések mindegyike le van fordítva és honosítva van az Office 365 teljes készletében, a felhasználó böngésző területi beállítása alapján.

### <a name="custom-security-questions"></a>Egyéni biztonsági kérdések

Az egyéni biztonsági kérdések nincsenek honosítva. Minden egyéni kérdés a rendszergazda felhasználói felületen megadott nyelven jelenik meg, még akkor is, ha a felhasználó böngésző területi beállítása eltér. Ha honosított kérdésekre van szüksége, használja az előre meghatározott kérdéseket.

Az egyéni biztonsági kérdések maximális hossza 200 karakter.

### <a name="security-question-requirements"></a>Biztonsági kérdésekkel kapcsolatos követelmények

* A minimális válasz karakteres korlát három karakterből állhat.
* A maximális válasz karakteres korlátja 40 karakter.
* A felhasználók több alkalommal nem tudnak válaszolni ugyanarra a kérdésre.
* A felhasználók nem tudnak ugyanazt a választ adni több kérdéshez.
* Bármely karakterkészlet használható a kérdések és válaszok meghatározására, beleértve a Unicode karaktereket is.
* A megadott kérdések számának nagyobbnak vagy egyenlőnek kell lennie a regisztráláshoz szükséges kérdések számával.

## <a name="email-address"></a>E-mail-cím

Az e-mail **-cím csak az Azure ad önkiszolgáló jelszó-visszaállítási szolgáltatásában**érhető el.

A Microsoft olyan e-mail-fiók használatát javasolja, amely nem igényli a felhasználó Azure AD-jelszavának elérését.

## <a name="microsoft-authenticator-app"></a>Microsoft Authenticator alkalmazás

A Microsoft Authenticator alkalmazás további biztonsági szintet biztosít az Azure AD munkahelyi vagy iskolai fiókjához vagy a Microsoft-fiókhoz.

A Microsoft Authenticator alkalmazás [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594) és [Windows Phone](https://go.microsoft.com/fwlink/?Linkid=825071) rendszereken érhető el.

> [!NOTE]
> A felhasználók nem regisztrálhatják a mobil alkalmazásaikat az önkiszolgáló jelszó-visszaállításhoz való regisztráláskor. Ehelyett a felhasználók a biztonsági adatok regisztrációjának [https://aka.ms/mfasetup](https://aka.ms/mfasetup) [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo)előzetes verziójában regisztrálhatják a Mobile alkalmazást.
>

### <a name="notification-through-mobile-app"></a>Értesítés mobilalkalmazáson keresztül

A Microsoft Authenticator alkalmazás segít megakadályozni a fiókok jogosulatlan elérését, és letilthatja a csalárd tranzakciókat, ha értesítéseket küld az okostelefonra vagy a táblaszámítógépre. A felhasználók megtekinthetik az értesítést, és ha ez jogos, válassza az ellenőrzés lehetőséget. Ellenkező esetben a Megtagadás lehetőséget is kiválaszthatja.

> [!WARNING]
> Az önkiszolgáló jelszó-visszaállításhoz, ha csak egy módszer szükséges az alaphelyzetbe állításhoz, az ellenőrzési kód az egyetlen lehetőség a felhasználók számára a **legmagasabb szintű biztonság biztosításához**.
>
> Ha két módszer szükséges, a felhasználók visszaállíthatják az értesítési **vagy** ellenőrzési kódokat az egyéb engedélyezett módszerek kiegészítéseként.
>

Ha engedélyezi mindkét értesítés használatát a Mobile App és az ellenőrző kód használatával a Mobile apps szolgáltatásban, a Microsoft Authenticator alkalmazást az értesítéseket használó felhasználók értesítést és kódot is használhatnak az identitásuk ellenőrzéséhez.

> [!NOTE]
> Ha a szervezete Kínában dolgozik vagy Kínába utazik, az **Android** -eszközökön a **Mobile App metóduson keresztül küldött értesítés** nem működik az adott országban. Ezeket a felhasználókat alternatív módszereket kell elérhetővé tenni.

### <a name="verification-code-from-mobile-app"></a>Mobilalkalmazás ellenőrzőkódja

A Microsoft Authenticator alkalmazás vagy más külső féltől származó alkalmazások szoftver-tokenként használhatók eskü-ellenőrző kód létrehozásához. A Felhasználónév és a jelszó megadása után adja meg az alkalmazás által a bejelentkezési képernyőn megadott kódot. Az ellenőrző kód a hitelesítés második formáját biztosítja.

> [!WARNING]
> Az önkiszolgáló jelszó-visszaállításhoz, ha csak egy módszerre van szükség az ellenőrző kód alaphelyzetbe állításához, az egyetlen lehetőség a felhasználók számára a **legmagasabb szintű biztonság biztosításához**.
>

Előfordulhat, hogy a felhasználók legfeljebb öt olyan hardver-tokent vagy hitelesítő alkalmazást (például a Microsoft Authenticator alkalmazást, amelyet a használatra konfiguráltak) kombinálnak.

## <a name="oath-hardware-tokens-public-preview"></a>A hardver-tokenek ESKÜje (nyilvános előzetes verzió)

Az eskü egy nyílt szabvány, amely meghatározza, hogy az egyszeri jelszavas (OTP) kódok hogyan jönnek létre. Az Azure AD támogatja az eskü-TOTP SHA-1 tokenek használatát a 30 másodperces vagy a 60-Second fajta esetében. Ezek a jogkivonatok a választott gyártótól származnak. A titkos kulcsok legfeljebb 128 karakterből állhatnak, amelyek nem kompatibilisek az összes jogkivonattal. A titkos kulcsokat kódolni kell a Base32-ben.

![ESKÜ-tokenek feltöltése az MFA-kiszolgálói eskü-tokenek panelre](media/concept-authentication-methods/oath-tokens-azure-ad.png)

A rendszer a nyilvános előzetes verzió részeként támogatja a hardveres tokenek használatát. További információ az előzetes verziókról: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

A jogkivonatok beszerzése után a rendszer az alábbi példában látható módon, vesszővel tagolt (CSV) fájlformátumban kell feltöltenie őket, beleértve az UPN-t, a sorozatszámot, a titkos kulcsot, az időintervallumot, a gyártót és a modellt.

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,1234567890abcdef1234567890abcdef,60,Contoso,HardwareKey
```

> [!NOTE]
> Ügyeljen rá, hogy a fejlécsort a fent látható módon adja meg a CSV-fájlban.

Miután megfelelően formázott CSV-fájlként, a rendszergazda bejelentkezhet a Azure Portalba, és megkeresheti **Azure Active Directory**, **MFA-kiszolgálót**, **eskü**-tokeneket, és FELTÖLTheti az eredményül kapott CSV-fájlt.

A CSV-fájl méretétől függően a folyamat eltarthat néhány percig. Kattintson a **frissítés** gombra az aktuális állapot lekéréséhez. Ha a fájlban hibák merülnek fel, lehetősége lesz letölteni egy CSV-fájlt, amely felsorolja a feloldható hibákat.

A hibák elhárítása után a rendszergazda ezután aktiválhatja az egyes kulcsokat, ha a jogkivonat **aktiválása** elemre kattint, és beírja a tokenen megjelenített egyszeri jelszavas azonosítót.

Előfordulhat, hogy a felhasználók legfeljebb öt olyan hardver-tokent vagy hitelesítő alkalmazást (például a Microsoft Authenticator alkalmazást, amelyet a használatra konfiguráltak) kombinálnak.

## <a name="phone-options"></a>Telefonbeállítások

### <a name="mobile-phone"></a>Mobiltelefon

A mobil telefonok esetében két lehetőség érhető el.

Ha a felhasználó nem szeretné, hogy a mobil telefonszáma megjelenjen a címtárban, de továbbra is szeretné használni azt a jelszó-visszaállításhoz, a rendszergazdáknak nem szabad azt a címtárban feltölteniük. A felhasználók a [jelszó-visszaállítási regisztrációs portálon](https://aka.ms/ssprsetup)tölthetik fel a **hitelesítési telefonos** attribútumot. A rendszergazdák láthatják ezeket az információkat a felhasználó profiljában, de máshol nem jelennek meg.

A megfelelő működéshez a telefonszámoknak a *+ országhívószám telefonszám*formátumban kell lenniük, például: + 1 4255551234.

> [!NOTE]
> Az országkód és a telefonszám között szóköz szükséges.
>
> A jelszó-visszaállítás nem támogatja a telefonos bővítményeket. A rendszer a hívás elhelyezése előtt is eltávolítja a bővítményeket a + 1 4255551234X12345 formátumban.

#### <a name="text-message"></a>Szöveges üzenet

A rendszer SMS-t továbbít az ellenőrző kódot tartalmazó mobil telefonszámra. A folytatáshoz adja meg a bejelentkezési felületen megadott ellenőrző kódot.

#### <a name="phone-call"></a>Telefonhívás

Az Ön által megadott telefonszámra automatikusan hanghívás történik. Válaszolja meg a hívást, majd a hitelesítéshez nyomja meg a # gombot a telefon billentyűzetén.

> [!IMPORTANT]
> Az 2019-as naptól kezdve a telefonhívási lehetőségek nem lesznek elérhetők az MFA és a SSPR felhasználók számára ingyenes/próbaverziós Azure AD-bérlők esetében. Ez a változás nem érinti az SMS-üzeneteket. A telefonos hívás továbbra is elérhető lesz a fizetős Azure AD-bérlők felhasználói számára. Ez a változás csak az ingyenes/próbaverziós Azure AD-bérlőket befolyásolja.

### <a name="office-phone"></a>Irodai telefon

Az Ön által megadott telefonszámra automatikusan hanghívás történik. Válaszolja meg a hívást, majd a hitelesítéshez nyomja meg a telefon billentyűzetén a # gombot.

A megfelelő működéshez a telefonszámoknak a *+ országhívószám telefonszám*formátumban kell lenniük, például: + 1 4255551234.

Az Office Phone-attribútumot a rendszergazda felügyeli.

> [!IMPORTANT]
> Az 2019-as naptól kezdve a telefonhívási lehetőségek nem lesznek elérhetők az MFA és a SSPR felhasználók számára ingyenes/próbaverziós Azure AD-bérlők esetében. Ez a változás nem érinti az SMS-üzeneteket. A telefonos hívás továbbra is elérhető lesz a fizetős Azure AD-bérlők felhasználói számára. Ez a változás csak az ingyenes/próbaverziós Azure AD-bérlőket befolyásolja.

> [!NOTE]
> Az országkód és a telefonszám között szóköz szükséges.
>
> A jelszó-visszaállítás nem támogatja a telefonos bővítményeket. A rendszer a hívás elhelyezése előtt is eltávolítja a bővítményeket a + 1 4255551234X12345 formátumban.

### <a name="troubleshooting-phone-options"></a>Telefonos beállítások hibaelhárítása

A hitelesítési módszerekkel kapcsolatos gyakori problémák telefonszám használatával:

* Blokkolt hívó azonosítója egyetlen eszközön
   * Eszköz hibáinak megoldása
* Helytelen telefonszám, helytelen országkód, otthoni telefonszám és munkahelyi telefonszám
   * Felhasználói objektum és konfigurált hitelesítési módszerek hibakeresése. Győződjön meg arról, hogy a megfelelő telefonszámok regisztrálva vannak.
* Helytelen PIN-kód van megadva
   * Erősítse meg, hogy a felhasználó használta az Azure MFA-kiszolgálón regisztrált megfelelő PIN-kódot.
* Hívás továbbítása a hangpostára
   * Győződjön meg arról, hogy a felhasználó bekapcsolta a telefont, és a szolgáltatás elérhető a saját területén, vagy használjon alternatív módszert.
* A felhasználó blokkolva van
   * A rendszergazda feloldja a felhasználó blokkolását a Azure Portalban.
* Az SMS nincs előfizetve az eszközön
   * A felhasználó módosíthatja a metódusokat, vagy aktiválhatja az SMS-t az eszközön.
* Hibás távközlési szolgáltatók (nem észlelhetők telefonos bemenetek, hiányzó DTMF-hangok, letiltott hívóazonosító több eszközön, vagy a több eszközön letiltott SMS-ek)
   * A Microsoft több távközlési szolgáltatót használ a telefonhívások és SMS-üzenetek továbbítására a hitelesítéshez. Ha a fenti problémák bármelyikét látja, akkor a felhasználó 5 percen belül legalább 5 alkalommal megpróbálta használni a metódust, és a felhasználó információi elérhetők a Microsoft ügyfélszolgálatával való kapcsolatfelvételkor.

## <a name="app-passwords"></a>Alkalmazásjelszavak

Bizonyos böngészőn kívüli alkalmazások nem támogatják a többtényezős hitelesítést, ha a felhasználó számára engedélyezve van a többtényezős hitelesítés, és a nem böngészőbeli alkalmazások használatát kísérli meg, nem tudnak hitelesíteni. Az alkalmazás jelszava lehetővé teszi, hogy a felhasználók továbbra is hitelesítsék magukat

Ha a többtényezős hitelesítést a feltételes hozzáférési szabályzatok és nem a felhasználónkénti MFA alapján kényszeríti ki, nem hozhat létre alkalmazás-jelszavakat. A feltételes hozzáférési házirendeket használó alkalmazások hozzáférés-vezérléséhez nem szükségesek az alkalmazás jelszavai.

Ha a szervezete az Azure AD-vel történő egyszeri bejelentkezéshez készült, és az Azure MFA-t fogja használni, vegye figyelembe a következő adatokat:

* Az alkalmazás jelszavait az Azure AD ellenőrzi, így megkerüli az összevonást. Az összevonás csak az alkalmazás jelszavának beállításakor használatos. Összevont (SSO) felhasználók esetén a rendszer a jelszavakat a szervezeti AZONOSÍTÓban tárolja. Ha a felhasználó elhagyja a vállalatot, az információnak a szervezet AZONOSÍTÓját kell használnia az rSync használatával. A fiókok letiltása/törlése akár három órát is igénybe vehet, ami késlelteti az alkalmazások jelszavainak letiltását/törlését az Azure AD-ben.
* Az alkalmazásjelszó nem tartja be a helyszíni ügyfél hozzáférés-vezérlési beállításait.
* Nem érhető el helyszíni hitelesítési naplózási/naplózási képesség az alkalmazások jelszavainak megadásához.
* Bizonyos speciális építészeti kialakításokhoz szükség lehet a szervezeti felhasználónevek és jelszavak és az alkalmazások jelszavának együttes használatára, ha kétlépéses ellenőrzést használ az ügyfelekkel a hitelesítés helyétől függően. A helyszíni infrastruktúrával hitelesítő ügyfelek esetében szervezeti felhasználónevet és jelszót kell használnia. Az Azure AD-vel hitelesítő ügyfelek esetében használja az alkalmazás jelszavát.
* Alapértelmezés szerint a felhasználók nem hozhatnak létre alkalmazás-jelszavakat. Ha lehetővé szeretné tenni a felhasználóknak az alkalmazás jelszavának létrehozását, jelölje be a **felhasználók számára az alkalmazás jelszavának engedélyezése lehetőséget** a szolgáltatás beállításai területen lévő nem böngészőalapú alkalmazásokba való bejelentkezéshez.

## <a name="next-steps"></a>További lépések

[Önkiszolgáló jelszó-visszaállítás engedélyezése a szervezet számára](quickstart-sspr.md)

[Az Azure multi-Factor Authentication engedélyezése a szervezet számára](howto-mfa-getstarted.md)

[Kombinált regisztráció engedélyezése a bérlőben](howto-registration-mfa-sspr-combined.md)

[A végfelhasználói hitelesítési módszer konfigurációs dokumentációja](https://aka.ms/securityinfoguide)
