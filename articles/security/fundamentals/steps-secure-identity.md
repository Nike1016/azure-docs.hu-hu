---
title: Öt lépés az identitás-infrastruktúra biztonságossá tételéhez Azure Active Directory
description: Ez a dokumentum a rendszergazdák által megvalósított fontos műveletek listáját ismerteti, hogy segítse a szervezet védelmét az Azure AD-funkciók használatával
author: martincoetzer
manager: manmeetb
tags: azuread
ms.service: security
ms.subservice: security-fundamentals
ms.topic: conceptual
ms.workload: identity
ms.date: 06/18/2018
ms.author: martinco
ms.openlocfilehash: 80c1ffd27b0668f19fd87e7eda62e578c861ba64
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934605"
---
# <a name="five-steps-to-securing-your-identity-infrastructure"></a>Az identitás-infrastruktúra biztonságossá tétele öt lépésben

Ha elolvassa ezt a dokumentumot, tisztában van a biztonság fontosságával. Valószínűleg már viseli a felelősséget a szervezet védelme érdekében. Ha meg kell győznie másokat a biztonság fontosságáról, küldje el a [Microsoft biztonsági intelligencia](https://go.microsoft.com/fwlink/p/?linkid=2073747)legújabb jelentését.

Ez a dokumentum segítséget nyújt a Azure Active Directory képességeinek biztonságosabbá tételéhez egy öt lépésből álló ellenőrzőlista használatával, amellyel a szervezetet a számítógépes támadásokkal szemben beoltották.

Ez az ellenőrzőlista segítséget nyújt a kritikus fontosságú ajánlott műveletek gyors üzembe helyezéséhez a szervezet azonnali védelméhez, amely ismerteti a következőket:

* Erősítse meg hitelesítő adatait.
* Csökkentse a támadási felületet.
* A veszélyforrások megválaszolásának automatizálása.
* Növelje a naplózási és figyelési ismereteket.
* Az önsegítő szolgáltatással kiszámítható és teljes körű biztonságot nyújthat a végfelhasználók számára.

> [!NOTE]
> A jelen dokumentumban szereplő javaslatok többsége csak olyan alkalmazásokra vonatkozik, amelyek a Azure Active Directory identitás-szolgáltatóként való használatára vannak konfigurálva. Az alkalmazások egyszeri bejelentkezéshez való konfigurálása biztosítja a hitelesítő adatokra vonatkozó házirendek, a veszélyforrások észlelése, a naplózás, a naplózás és az alkalmazásokhoz hozzáadott egyéb szolgáltatások előnyeit. Az [Azure Active Directoryon keresztüli egyszeri bejelentkezés](../../active-directory/manage-apps/configure-single-sign-on-portal.md) az alapja – az összes javaslat alapja.

Az ebben a dokumentumban ismertetett javaslatok az Azure AD-bérlő identitás-biztonsági konfigurációjának automatizált értékelésével vannak összhangban az [identitás biztonságos pontszámával](../../active-directory/fundamentals/identity-secure-score.md). A szervezetek az Azure AD-portálon az Identitáskezelés biztonságos pontszám lapon megtalálhatják az aktuális biztonsági konfigurációban mutatkozó hézagokat, így biztosítva, hogy az aktuális Microsoft ajánlott biztonsági eljárásokat követik. Az egyes javaslatok a biztonságos pontszám lapon való megvalósítása növeli a pontszámát, és lehetővé teszi az előrehaladás nyomon követését, valamint segít összehasonlítani a megvalósítást más hasonló méretű szervezetekkel vagy iparágával.

![Identitásbiztonsági pontszám](./media/steps-secure-identity/azure-ad-sec-steps0.png)

## <a name="before-you-begin-protect-privileged-accounts-with-mfa"></a>Előkészületek: Magas jogosultsági szintű fiókok biztosítása MFA-val

Mielőtt elkezdené ezt a feladatlistát, győződjön meg róla, hogy az ellenőrzőlista elolvasása közben nem sérül. Először gondoskodnia kell a Kiemelt jogosultságú fiókok biztonságáról.

A Kiemelt fiókok felügyeletét befogadó támadók óriási károkat okozhatnak, ezért fontos, hogy először megvédje ezeket a fiókokat. Az alapszintű [védelem](../../active-directory/conditional-access/baseline-protection.md)használatával engedélyezheti és megkövetelheti az [Azure multi-Factor Authentication](../../active-directory/authentication/multi-factor-authentication.md) (MFA) használatát a szervezet összes rendszergazdájának. Ha még nem implementálta az MFA-t, tegye meg most! Ez fontos.

Minden készlet? Ismerkedjen meg az ellenőrzőlista első lépéseivel.

## <a name="step-1---strengthen-your-credentials"></a>1\. lépés – a hitelesítő adatok megerősítése 

A legtöbb nagyvállalati biztonsági rést egy olyan fiók okozta, amely a jelszó spray, a megsértőség vagy az adathalászat megsértésével van ellátva. További információ ezekről a támadásokról ebben a videóban (45 perc):
> [!VIDEO https://www.youtube.com/embed/uy0j1_t5Hd4]

### <a name="make-sure-your-organization-use-strong-authentication"></a>Győződjön meg arról, hogy a szervezete erős hitelesítést használ

A kitalált jelszavak gyakorisága, az adathalászás, a kártevők ellopása vagy az újbóli használat miatt kritikus fontosságú a jelszó visszaállítása valamilyen erős hitelesítő adat használatával – további információ az [Azure multi-Factor Authentication](../../active-directory/authentication/multi-factor-authentication.md)szolgáltatásról.

### <a name="start-banning-commonly-attacked-passwords-and-turn-off-traditional-complexity-and-expiration-rules"></a>Indítsa el a gyakran megtámadott jelszavak betiltását, és kapcsolja ki a hagyományos összetettséget és a lejárati szabályokat.

Számos szervezet használja a hagyományos összetettséget (speciális karakterek, számok, nagybetűs és kisbetűk megkövetelése) és a jelszó lejárati szabályait. A [Microsoft kutatási](https://aka.ms/passwordguidance) feltette ezeket a házirendeket, mert a felhasználók a könnyebben kitalált jelszavakat választják.

Az Azure AD [dinamikus tiltott jelszava](../../active-directory/authentication/concept-password-ban-bad.md) funkció a jelenlegi támadó viselkedését használja annak megakadályozása érdekében, hogy a felhasználók könnyen kitalált jelszavakat állítsanak be. Ez a funkció mindig a Felhőbeli felhasználók létrehozásakor érhető el, de a hibrid szervezetek számára is elérhető, ha az [Azure ad jelszavas védelmet telepítik a Windows Server Active Directory](../../active-directory/authentication/concept-password-ban-bad-on-premises.md). Az Azure AD jelszavas védelme megakadályozza, hogy a felhasználók kiválasszák ezeket a gyakori jelszavakat, és kiterjeszthetők a megadott egyéni kulcsszavakat tartalmazó jelszavak blokkolására. Megakadályozhatja például, hogy a felhasználók olyan jelszavakat válasszanak, amelyek a vállalat termékeit vagy egy helyi sport csapatot tartalmaznak.

A Microsoft a következő modern jelszóházirend bevezetését javasolja a [NIST-útmutató](https://pages.nist.gov/800-63-3/sp800-63b.html)alapján:

1. A jelszó megkövetelése legalább 8 karakterből állhat. Többé nem feltétlenül jobb, mert a felhasználók kiválaszthatják a prediktív jelszavakat, menthetik a jelszavakat a fájlokban, vagy leírhatják őket.
2. Letilthatja a lejárati szabályokat, amelyek segítségével a felhasználók könnyen kitalálják a jelszavakat, például a **Spring2019!**
3. Tiltsa le a karakter-összeállítási követelményeket, és akadályozza meg, hogy a felhasználók gyakran megtámadott jelszavakat válasszanak, mivel a felhasználók kiszámítható karakteres helyettesítéseket választhatnak a jelszavakban.

A PowerShell használatával [megakadályozhatja, hogy a jelszavak](../../active-directory/authentication/concept-sspr-policy.md) lejárjanak a felhasználók számára, ha közvetlenül az Azure ad-ben hoz létre identitásokat. A hibrid szervezeteknek a [tartományi csoportházirend-beállítások](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/hh994572(v%3dws.10)) vagy a [Windows PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/set-addefaultdomainpasswordpolicy)használatával kell alkalmazniuk ezeket a házirendeket.

### <a name="protect-against-leaked-credentials-and-add-resilience-against-outages"></a>A kiszivárgott hitelesítő adatokkal szembeni védelem és az kimaradások elleni rugalmasság biztosítása

Ha a szervezet hibrid identitási megoldást használ átmenő hitelesítéssel vagy összevonással, akkor a jelszó-kivonatolási szinkronizálást a következő két okból kell engedélyeznie:

* A [kiszivárgott hitelesítő adatokkal rendelkező felhasználók](../../active-directory/reports-monitoring/concept-risk-events.md) az Azure ad-ben figyelmeztetnek a Felhasználónév és a jelszó párokra, amelyek elérhetők a "Dark web"-ben. A rendszer az adathalászat, a kártevők és a jelszavak újbóli felhasználását a későbbiekben megsértő külső webhelyeken végezheti el. A Microsoft megkeresi ezeket a kiszivárgott hitelesítő adatokat, és tájékoztatja Önt a jelentésben, ha megfelelnek a szervezet hitelesítő adatainak, de csak akkor, ha [engedélyezi a jelszó](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)-kivonatok szinkronizálását.
* Helyszíni leállás esetén (például egy ransomware támadás esetében) a [jelszó-kivonatoló szinkronizálás használatával](choose-ad-authn.md)átválthat a Felhőbeli hitelesítés használatára. Ez a biztonsági mentési hitelesítési módszer lehetővé teszi, hogy továbbra is hozzáférjen a hitelesítéshez konfigurált alkalmazások Azure Active Directory, például az Office 365-hez. Ebben az esetben az informatikai részlegnek nem kell személyes e-mail-fiókokat használnia az adatmegosztáshoz, amíg a helyszíni leállás megoldódik.

További információ a [jelszó-kivonatolási szinkronizálás](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md) működéséről.

> [!NOTE]
> Ha engedélyezi a jelszó-kivonatolási szinkronizálást, és az Azure AD tartományi szolgáltatásokat használja, a Kerberos (AES 256) kivonatok és opcionálisan NTLM (RC4, nem Salt) kivonatok is titkosítva lesznek, és szinkronizálva lesznek az Azure AD-vel. 

### <a name="implement-ad-fs-extranet-smart-lockout"></a>AD FS extranetes intelligens zárolás implementálása

Szervezetek, amelyek az [Azure ad Smart zárolási](../../active-directory/authentication/concept-sspr-howitworks.md)szolgáltatásból közvetlenül az Azure ad-be való hitelesítéshez konfigurálják az alkalmazásokat. Ha AD FSt használ a Windows Server 2012R2, implementálja AD FS [extranet zárolásának védelmét](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection). Ha AD FSt használ a Windows Server 2016-ben, az [extranetes intelligens zárolást](https://support.microsoft.com/help/4096478/extranet-smart-lockout-feature-in-windows-server-2016)implementálja. AD FS az intelligens extranetes zárolás védelmet biztosít a találgatásos támadásokkal szemben, amelyek célja AD FS, miközben megakadályozza a felhasználók kizárását Active Directory.

### <a name="take-advantage-of-intrinsically-secure-easier-to-use-credentials"></a>Használja ki a belsőleg biztonságos és könnyebben használható hitelesítő adatokat

A [Windows Hello](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)használatával a jelszavakat erős, kétfaktoros hitelesítéssel helyettesítheti a számítógépeken és mobileszközökön. Ez a hitelesítés olyan új felhasználói hitelesítő adatokból áll, amely biztonságosan kötődik az eszközhöz, és biometrikus vagy PIN-kódot használ.

## <a name="step-2---reduce-your-attack-surface"></a>2\. lépés – a támadási felület csökkentése

A jelszó megtörése miatt a szervezet támadási felületének minimalizálása kritikus fontosságú. A régebbi, kevésbé biztonságos protokollok használatának, a hozzáférési pontok korlátozásának és az erőforrásokhoz való rendszergazdai hozzáférés nagyobb mértékű felügyeletének csökkentése segíthet csökkenteni a támadási felület területét.

### <a name="block-legacy-authentication"></a>Régi hitelesítési folyamat letiltása

A saját örökölt módszereiket használó alkalmazások az Azure AD-vel való hitelesítéshez és a vállalati adatszolgáltatásokhoz való hozzáféréshez egy másik veszélyforrást jelentenek. Az örökölt hitelesítést használó alkalmazások például a következők: POP3, IMAP4 vagy SMTP-ügyfelek. Az örökölt hitelesítési alkalmazások a felhasználó nevében hitelesítik magukat, és megakadályozzák, hogy az Azure AD speciális biztonsági értékeléseket végezzen. Az alternatív, modern hitelesítés csökkenti a biztonsági kockázatokat, mivel ez támogatja a többtényezős hitelesítést és a feltételes hozzáférést. A következő három műveletet javasoljuk:

1. [Az örökölt hitelesítés letiltása, ha AD FS használ](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/access-control-policies-w2k12).
2. [A SharePoint Online és az Exchange Online beállítása modern hitelesítés használatára](../../active-directory/conditional-access/conditional-access-for-exo-and-spo.md).
3. [Feltételes hozzáférési szabályzatok használata az örökölt hitelesítés blokkolására](../../active-directory/conditional-access/conditions.md).

### <a name="block-invalid-authentication-entry-points"></a>Érvénytelen hitelesítési belépési pontok blokkolása

A szabálysértések megsértésének feltételezésével csökkentheti a feltört felhasználói hitelesítő adatok következményeit, amikor azok történnek. A környezetben lévő minden alkalmazás esetében tekintse meg a következő érvényes használati eseteket: mely csoportok, mely hálózatok, mely eszközök és egyéb elemek engedélyezettek –, majd tiltsa le a többiet. Az [Azure ad feltételes hozzáférésével](../../active-directory/conditional-access/overview.md)szabályozhatja, hogy a jogosult felhasználók hogyan férhessenek hozzá az alkalmazásaihoz és erőforrásaihoz az Ön által megadott feltételek alapján.

### <a name="block-end-user-consent"></a>Végfelhasználói engedély letiltása

Alapértelmezés szerint az Azure AD minden felhasználója engedélyezheti, hogy az OAuth 2,0-et használó alkalmazások és a Microsoft Identity [engedélyezési keretrendszer](../../active-directory/develop/consent-framework.md) engedélyei hozzáférjenek a vállalati adatokhoz. Míg a hozzájárulás lehetővé teszi, hogy a felhasználók könnyen beszerezzék a Microsoft 365 és az Azure-ba integrált hasznos alkalmazásokat, a kockázatokat jelenthetik, ha nem használják és nem figyelik körültekintően. Az [összes jövőbeli felhasználói beleegyező művelet](../../active-directory/manage-apps/methods-for-removing-user-access.md) letiltásával csökkentheti a felületi területet, és enyhítheti a kockázatot. Ha a végfelhasználói hozzájárulás le van tiltva, a rendszer továbbra is tiszteletben tartja az előző hozzájárulási támogatást, de az összes jövőbeli hozzájárulási műveletet egy rendszergazdának kell elvégeznie. A funkció letiltása előtt azt javasoljuk, hogy a felhasználók tisztában legyenek azzal, hogyan kérheti le az új alkalmazások rendszergazdai jóváhagyását; Ennek segítségével csökkentheti a felhasználók súrlódását, minimalizálhatja a támogatás mennyiségét, és gondoskodhat arról, hogy a felhasználók ne regisztráljanak a nem Azure AD hitelesítő adatokat használó alkalmazásokra.

### <a name="implement-azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management implementálása

A "jogsértés feltételezése" egy másik következménye annak a valószínűsége, hogy a feltört fiók valószínűleg egy kiemelt szerepkörrel működhet. 

A [Azure ad Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-configure.md) segítséget nyújt a fiókok minimálisra csökkentése érdekében:

* Azonosíthatja és kezelheti a rendszergazdai szerepkörökhöz rendelt felhasználókat.
* Ismerje meg a nem használt vagy túlzott jogosultságú szerepköröket, amelyeket el kell távolítania.
* Szabályokat hozhat létre annak biztosítására, hogy a Kiemelt szerepkörök védelmét a multi-Factor Authentication védi.
* Olyan szabályokat hozhat létre, amelyek biztosítják, hogy a Kiemelt szerepkörök csak elég hosszúak legyenek a kiemelt feladat végrehajtásához.

Engedélyezze az Azure AD PIM-t, majd tekintse meg a rendszergazdai szerepkörökhöz rendelt felhasználókat, és távolítsa el a szerepkörök szükségtelen fiókjait. A többi emelt szintű jogosultsággal rendelkező felhasználó számára az állandóról jogosultra helyezheti át őket. Végül hozza létre a megfelelő szabályzatokat annak biztosításához, hogy mikor van szükségük ahhoz, hogy hozzáférést kapjanak ezekhez a Kiemelt szerepkörökhöz, biztonságosan, a szükséges módosítás-ellenőrzéssel.

A rendszerjogosultságú fiók üzembe helyezésének részeként az ajánlott eljárás [szerint hozzon létre legalább két vészhelyzeti fiókot](../../active-directory/users-groups-roles/directory-admin-roles-secure.md) , hogy biztosan hozzáférjen az Azure ad-hez, ha Ön kizárja magát.

## <a name="step-3---automate-threat-response"></a>3\. lépés – a veszélyforrások megválaszolásának automatizálása

A Azure Active Directory számos képességgel rendelkezik, amelyek automatikusan feltartóztatják a támadásokat, és eltávolítják az észlelés és a válasz közötti késést. Csökkentheti a költségeket és a kockázatokat, ha csökkenti a bűnözők által a környezetbe ágyazható idő csökkentését. Itt láthatja az elvégezhető konkrét lépéseket.

### <a name="implement-user-risk-security-policy-using-azure-ad-identity-protection"></a>A felhasználói kockázatokra vonatkozó biztonsági házirend megvalósítása Azure AD Identity Protection használatával

A felhasználói kockázat azt jelzi, hogy a felhasználó identitása sérült, és a felhasználó identitásával társított [felhasználói kockázati események](../../active-directory/identity-protection/overview.md) alapján számítja ki a rendszer. A felhasználói kockázati házirend egy feltételes hozzáférési szabályzat, amely kiértékeli a kockázati szintet egy adott felhasználó vagy csoport számára. Az alacsony, közepes és magas kockázati szintű szabályok alapján a szabályzat beállítható úgy, hogy blokkolja a hozzáférést, vagy a többtényezős hitelesítés használatával biztonságos jelszó megváltoztatására van szükség. A Microsoft javaslata a nagy kockázatú felhasználók biztonságos jelszavas módosításának megkövetelése.

![Kockázatosként megjelölt felhasználók](./media/steps-secure-identity/azure-ad-sec-steps1.png)

### <a name="implement-sign-in-risk-policy-using-azure-ad-identity-protection"></a>A bejelentkezési kockázati szabályzat megvalósítása Azure AD Identity Protection használatával

A bejelentkezési kockázat annak a valószínűsége, hogy a fiók tulajdonosa nem az identitás használatával próbál bejelentkezni. A [bejelentkezési kockázati szabályzat](../../active-directory/identity-protection/overview.md) egy feltételes hozzáférési szabályzat, amely kiértékeli a kockázati szintet egy adott felhasználó vagy csoport számára. A kockázati szint (magas/közepes/alacsony) alapján a szabályzat beállítható úgy, hogy blokkolja a hozzáférést vagy kényszerítse a többtényezős hitelesítést. Győződjön meg arról, hogy a többtényezős hitelesítést közepes vagy annál nagyobb kockázatú bejelentkezésekre kényszeríti.

![Bejelentkezés névtelen IP-címekről](./media/steps-secure-identity/azure-ad-sec-steps2.png)

## <a name="step-4---increase-your-awareness"></a>4\. lépés – a Hangfelismerés növelése

A biztonsággal kapcsolatos események és a kapcsolódó riasztások naplózása és naplózása hatékony védelmi stratégia alapvető összetevői. A biztonsági naplók és jelentések lehetővé teszik a gyanús tevékenységek elektronikus feljegyzését, valamint a hálózat külső behatolását, illetve a belső támadásokat jelezhet minták észlelését. A naplózás segítségével figyelheti a felhasználói tevékenységeket, dokumentálhatja a megfelelőséget, törvényszéki elemzéseket végezhet, és egyéb műveleteket is végrehajthat. A riasztások a biztonsági események értesítéseit biztosítják.

### <a name="monitor-azure-ad"></a>Az Azure AD figyelése

Microsoft Azure szolgáltatások és szolgáltatások konfigurálható biztonsági naplózási és naplózási lehetőségeket biztosítanak, amelyek segítségével azonosíthatja a biztonsági házirendekben és mechanizmusokban mutatkozó réseket, és orvosolhatja ezeket a hiányosságokat a szabálysértések megelőzése érdekében. [A Azure Active Directory-portálon](../../active-directory/reports-monitoring/concept-audit-logs.md)az [Azure naplózási és naplózási](log-audit.md) szolgáltatásait, valamint a naplózási tevékenység jelentéseit használhatja.

### <a name="monitor-azure-ad-connect-health-in-hybrid-environments"></a>Azure AD Connect Health monitorozása hibrid környezetekben

A [Azure ad Connect Health figyelése AD FS](../../active-directory/hybrid/how-to-connect-health-adfs.md) a lehetséges problémákkal és a AD FS-infrastruktúrával kapcsolatos támadások láthatóságával kapcsolatos további információkat nyújt. Azure AD Connect Health a riasztásokat a részletekkel, a megoldás lépéseivel és a kapcsolódó dokumentációra mutató hivatkozásokkal biztosítja; a hitelesítési forgalommal kapcsolatos számos metrika használati elemzései; Teljesítményfigyelés és jelentések.

![Azure AD Connect Health](./media/steps-secure-identity/azure-ad-sec-steps4.png)

### <a name="monitor-azure-ad-identity-protection-events"></a>Azure AD Identity Protection események figyelése

A [Azure ad Identity Protection](../../active-directory/identity-protection/overview.md) egy értesítési, figyelési és jelentéskészítési eszköz, amellyel észlelhetők a szervezet identitásait érintő lehetséges sebezhetőségek. Ez észleli a kockázati eseményeket, például a kiszivárgott hitelesítő adatokat, a lehetetlen utazást és a fertőzött eszközökről érkező bejelentkezéseket, a névtelen IP-címeket, a gyanús tevékenységhez tartozó IP-címeket és az ismeretlen helyeket. Az értesítési riasztások lehetővé teszik a veszélyeztetett felhasználók és/vagy heti kivonatoló e-mailek fogadását.

Azure AD Identity Protection két fontos jelentést biztosít a napi figyeléshez:
1. A kockázatos bejelentkezési jelentések felszínre kerülnek a felhasználói bejelentkezési tevékenységek, amelyeket meg kell vizsgálnia. Előfordulhat, hogy a legitim tulajdonos nem végezte el a bejelentkezést.
2. A kockázatos felhasználói jelentések olyan felszíni felhasználói fiókokat érintenek, amelyek biztonsága esetleg sérült, például a észlelt hitelesítő adatok, vagy a felhasználó különböző helyekről való bejelentkezett, ami lehetetlenné teszi az utazási eseményt. 

![Kockázatosként megjelölt felhasználók](./media/steps-secure-identity/azure-ad-sec-steps3.png)

### <a name="audit-apps-and-consented-permissions"></a>Az alkalmazások és a beleegyezett engedélyek naplózása

A felhasználók bejelentkezhetnek egy olyan feltört webhelyre vagy alkalmazásba, amely hozzáférést szerez a profiljuk adataihoz és a felhasználói adatokhoz, például az e-mailekhez. Egy rosszindulatú színész használhatja a kapott engedélyekkel, hogy Titkosítsa a postaláda tartalmát, és megszerezze a váltságdíjat a postaláda-információk visszaszerzéséhez. A [rendszergazdáknak át kell tekinteniük és ellenőrizniük kell](https://docs.microsoft.com/office365/securitycompliance/detect-and-remediate-illicit-consent-grants) a felhasználók által megadott engedélyeket.

## <a name="step-5---enable-end-user-self-help"></a>5\. lépés – a végfelhasználói önkiszolgáló Súgó engedélyezése

Amennyire csak lehet, érdemes egyensúlyba állítani a biztonságot a hatékonysággal. Ha hosszú távon is megközelíti az Ön utazását, azzal a gondolkodásmódtal, hogy a biztonságra vonatkozó alapszintű biztonsági beállításokat a munkahelyen, a felhasználók továbbra is körültekintően vehetik igénybe. 

### <a name="implement-self-service-password-reset"></a>Önkiszolgáló jelszó-visszaállítás implementálása

Az Azure önkiszolgáló jelszavának alaphelyzetbe [állítása (SSPR)](../../active-directory/authentication/quickstart-sspr.md) egyszerű módszert kínál a rendszergazdáknak, hogy rendszergazdai beavatkozás nélkül engedélyezzék vagy oldják fel jelszavukat vagy fiókjaikat. A rendszer részletes, követhető jelentést tartalmaz a felhasználók rendszerhozzáféréséről, továbbá értesítőkkel figyelmeztet az illetéktelen használatra vagy visszaélésre. 

### <a name="implement-self-service-group-management"></a>Önkiszolgáló csoport kezelésének megvalósítása

Az Azure AD lehetővé teszi az erőforrásokhoz való hozzáférés kezelését a biztonsági csoportok és az Office 365-csoportok használatával. Ezeket a csoportokat a rendszergazdák felügyelik a csoport tulajdonosai helyett. Ez a [](../../active-directory/users-groups-roles/groups-self-service-management.md)funkció lehetővé teszi, hogy az önkiszolgáló csoportok felügyeletének részeként olyan tulajdonosokat engedélyezzen, akik nem rendeltek rendszergazdai szerepkört a csoportok létrehozásához és kezeléséhez anélkül, hogy a rendszergazdák a kéréseiket kezelni tudják.

### <a name="implement-azure-ad-access-reviews"></a>Azure AD hozzáférési felülvizsgálatok megvalósítása

Az [Azure ad hozzáférési felülvizsgálatok](../../active-directory/governance/access-reviews-overview.md)segítségével kezelheti a csoporttagságok, a vállalati alkalmazásokhoz való hozzáférést és a Kiemelt szerepkör-hozzárendeléseket, így meggyőződhet arról, hogy olyan biztonsági szabványt tart fenn, amely nem biztosít hozzáférést a felhasználóknak hosszabb ideig, amikor nem szükség van rá.

## <a name="summary"></a>Összegzés

A biztonságos identitás-infrastruktúrának számos aspektusa van, de ez az öt lépésből álló ellenőrzőlista segítséget nyújt a biztonságosabb és biztonságos identitás-infrastruktúra gyors megvalósításához:

* Erősítse meg hitelesítő adatait.
* Csökkentse a támadási felületet.
* A veszélyforrások megválaszolásának automatizálása.
* Növelje a naplózási és figyelési ismereteket.
* Az önsegítő szolgáltatással kiszámítható és teljes körű biztonságot nyújthat a végfelhasználók számára.

Nagyra értékeljük, hogy milyen komolyan vesszük a személyazonossági biztonságot, és reméljük, hogy ez a dokumentum hasznos útiterv a szervezete számára biztonságosabb testhelyzethez.

## <a name="next-steps"></a>További lépések
Ha segítségre van szüksége a javaslatok megtervezéséhez és üzembe helyezéséhez, tekintse meg az [Azure ad projekt üzembe helyezési terveit](https://aka.ms/deploymentplans) .
