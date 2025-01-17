---
title: Használati feltételek – Azure Active Directory |} A Microsoft Docs
description: Ismerkedés az Azure Active Directory használati feltételeket a szükséges információkkal az alkalmazottak vagy a Vendégek, mielőtt hozzáférhetne használatával.
services: active-directory
ms.service: active-directory
ms.subservice: compliance
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: f891642761a2f692158efbd9111ff96444c4269d
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476190"
---
# <a name="azure-active-directory-terms-of-use"></a>Az Azure Active Directory – használati feltételek

Az Azure AD használati feltételek, amelyek a szervezetek használhatják a szükséges információkkal a végfelhasználók számára egyszerű módszert kínál. Ez a bemutató gondoskodik arról, hogy a felhasználók megkapják a jogi vagy megfelelőségi követelményekre vonatkozó nyilatkozatokat. Ez a cikk azt ismerteti, hogyan kezdheti el a használati feltételeket tartalmazó fájl.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview-videos"></a>Áttekintő videók

Az alábbi videó a használati feltételeket tartalmazó fájl gyors áttekintést nyújt.

>[!VIDEO https://www.youtube.com/embed/tj-LK0abNao]

További videók lásd:
- [Az Azure Active Directoryban használati feltételek központi telepítése](https://www.youtube.com/embed/N4vgqHO2tgY)
- [Hogyan vezethet be használati feltételeket az Azure Active Directoryban](https://www.youtube.com/embed/t_hA4y9luCY)

## <a name="what-can-i-do-with-terms-of-use"></a>Mire használhatom a használati feltételeket?

Az Azure AD használati feltételek a következő képességekkel rendelkezik:

- Az alkalmazottak vagy a vendégfelhasználókat arra, hogy fogadja el a használati feltételeket, mielőtt hozzáférhetne van szükség.
- Az alkalmazottak vagy a vendégfelhasználókat arra, hogy fogadja el a használati feltételeket minden eszközön, mielőtt hozzáférhetne van szükség.
- Az alkalmazottak vagy a vendégfelhasználókat arra, hogy fogadja el a használati feltételeket, ismétlődő ütemezés szerint szükséges.
- Az alkalmazottak vagy a vendégfelhasználókat arra, hogy fogadja el a használati feltételeket, mielőtt a biztonsági adatok regisztrálása az Azure multi-factor Authenticationre (MFA) van szükség.
- Az alkalmazottaknak, fogadja el a használati feltételeket, mielőtt a biztonsági adatok regisztrálása az Azure AD önkiszolgáló jelszó-visszaállítás (SSPR).
- A szervezet minden tagjára vonatkozó általános használati szerepelnek.
- Specifikus használati feltételek a felhasználói attribútumok (például alapján található orvosok kontra nővérek, belföldi kontra nemzetközi alkalmazottak [dinamikus csoportjai](../users-groups-roles/groups-dynamic-membership.md)).
- Specifikus használati feltételek jelenthet, nagy üzleti hatás alkalmazások, mint például a Salesforce elérése közben.
- Jelen használati feltételek különböző nyelveken.
- Lista, aki, vagy még nem elfogadott a használati feltételeket.
- Segít az adatvédelmi előírások teljesítése.
- Feltételek használata tevékenység megfelelőségi és naplózási naplóját jeleníti meg.
- Létrehozásához és kezeléséhez használja a feltételek [Microsoft Graph API-k](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/agreement) (jelenleg előzetes verzióban érhető el).

## <a name="prerequisites"></a>Előfeltételek

Használ, és az Azure AD használati feltételek konfigurálása, kell rendelkeznie:

- Prémium szintű Azure AD P1, P2, EMS E3 vagy EMS E5 előfizetés.
   - Ha még nem rendelkezik ezen előfizetések egyikével sem, [beszerezhet Prémium szintű Azure AD előfizetést](../fundamentals/active-directory-get-started-premium.md), vagy [engedélyezheti a Prémium szintű Azure AD előfizetés próbaverzióját](https://azure.microsoft.com/trial/get-started-active-directory/).
- A következő rendszergazda fiókok egyike a konfigurálni kívánt könyvtárhoz:
   - Globális rendszergazda
   - Biztonsági rendszergazda
   - Feltételes hozzáférésű rendszergazda

## <a name="terms-of-use-document"></a>A használati feltételek dokumentuma

Az Azure AD használati feltételek PDF formátumot használja a tartalmak. A PDF-fájl bármilyen tartalmat jelenthet, akár meglévő szerződéseket is, így begyűjtheti a végfelhasználói megállapodásokat a felhasználók bejelentkezésekor. Felhasználók a mobileszközök támogatására, a PDF-dokumentumban ajánlott betűméret az 24 pont.

## <a name="add-terms-of-use"></a>Használati feltételek hozzáadása

A használati feltételek dokumentuma véglegesítése után a következő eljárás használatával adja hozzá.

1. Jelentkezzen be az Azure-ban, egy globális rendszergazdai, biztonsági rendszergazdának vagy feltételes hozzáférésű rendszergazda.
1. Lépjen a **Használati feltételekre** a következőn: [https://aka.ms/catou](https://aka.ms/catou).

   ![Feltételes hozzáférés – feltételek használata panel](./media/terms-of-use/tou-blade.png)

1. Kattintson az **Új feltételek** lehetőségre.

   ![Új kifejezés használata ablaktábla adja meg a használati beállítások használata](./media/terms-of-use/new-tou.png)

1. Az a **neve** mezőbe írjon be egy nevet a használati feltételeket, amely használható az Azure Portalon.
1. Az a **megjelenítendő név** mezőbe írja be a címet látják a felhasználók bejelentkezéskor.
1. A **használati feltételek dokumentuma**, keresse meg a véglegesített használati feltételek PDF-fájlt, és válassza ki azt.
1. A használati feltételek dokumentuma nyelvének kiválasztása. A nyelvi beállítás lehetővé teszi, hogy több használati feltételt töltsön fel, különböző nyelveken. A használati feltételek végfelhasználó által látott verziója a böngésző beállításaitól függ.
1. A végfelhasználóknak megtekinteni a használati feltételeket, mielőtt elfogadhatnák őket, állítsa **bontsa ki a használati feltételeket, hogy a felhasználók** való **a**.
1. A végfelhasználók számára, hogy fogadja el a használati feltételeket minden eszközön érnek el a szükséges, állítsa be **kérése a felhasználóktól minden eszközön jóváhagyást** való **a**. További információkért lásd: [eszközönkénti használati feltételeit](#per-device-terms-of-use).
1. Ha szeretne érvényessége lejár a használati feltételek jóváhagyásai ütemezés szerint, állítsa **címtárbérlőhöz lejár** való **a**. Érték beállítása a esetén két további beállítások jelennek meg.

   ![Jóváhagyási beállítások állítsa be a kezdő dátum, gyakorisága és időtartama lejár](./media/terms-of-use/expire-consents.png)

1. Használja a **től elévülési** és **gyakorisága** beállításainak megadásával határozza meg a használati feltételeket az ütemezés használata lejárhat. Az alábbi táblázatban néhány példa beállítások láthatók:

   | Lejárat kezdete | Gyakoriság | Eredmény |
   | --- | --- | --- |
   | A mai napig  | Havi | Kezdve felhasználók minden hónapban majd elfogadást, és kell fogadja el a használati feltételeket. |
   | Jövőbeli dátumot  | Havi | Kezdve felhasználóknak el kell fogadniuk a használati feltételeket. A jövőbeli dátumot esetén a hozzájárulásokat le fog járni, és ezután a felhasználók hatókörébe, havonta kell.  |

   Például, ha a lejárati dátumot, a kezdési **jan. 1** és gyakoriságának **havi**, hogyan lejárhat két olyan felhasználó esetében fordulhat elő, a következő:

   | Felhasználó | Először fogadja el a dátum | Először lejárati dátuma | A második lejárati dátuma | Harmadik lejárati dátuma |
   | --- | --- | --- | --- | --- |
   | Alice | Január 1 | Feb. 1-től | Március 1 | Diagramhalmazban 1 |
   | Bob | Január 15- | Feb. 1-től | Március 1 | Diagramhalmazban 1 |

1. Használja a **időtartam, mielőtt újra-elfogadása szükséges (nap)** beállítást adja meg, hány nap elteltével a felhasználó a használati feltételeket kell hatókörébe. Ez lehetővé teszi a felhasználók a saját ütemezés számára. Ha például azt az időtartamot **30** nap, hogyan lejárhat két olyan felhasználó esetében fordulhat elő, a következő:

   | Felhasználó | Először fogadja el a dátum | Először lejárati dátuma | A második lejárati dátuma | Harmadik lejárati dátuma |
   | --- | --- | --- | --- | --- |
   | Alice | Január 1 | Január 31-ig | Március 2 | Diagramhalmazban 1 |
   | Bob | Január 15- | Február 14 | Március 16 | Diagramhalmazban 15 |

   Lehet használni a **címtárbérlőhöz lejár** és **időtartam, mielőtt újra-elfogadása szükséges (nap)** beállítások együtt, de általában olyan, az egyiket használja.

1. A **feltételes hozzáférési**, használja a **sablonnal feltételes hozzáférési szabályzat kényszerítése** listát használva jelölje ki a sablon kényszeríteni a használati feltételeket.

   ![Feltételes hozzáférés legördülő listából válasszon ki egy házirendsablont a](./media/terms-of-use/conditional-access-templates.png)

   | Sablon | Leírás |
   | --- | --- |
   | **Az összes vendégek felhőalkalmazások elérése** | Feltételes hozzáférési szabályzat jön létre minden Vendég és az összes felhőalapú alkalmazásokba. Ez a szabályzat milyen hatással van az Azure Portalon. Miután ez elkészült, akkor szükség lehet kijelentkezés és bejelentkezés. |
   | **Az összes felhasználó számára a felhőalkalmazások elérése** | Feltételes hozzáférési szabályzat jön létre minden felhasználó és az összes felhőalapú alkalmazásokba. Ez a szabályzat milyen hatással van az Azure Portalon. Ennek létrehozása után kijelentkezés és bejelentkezés szükséges fogjuk. |
   | **Egyéni szabályzat** | Válassza ki a felhasználók, csoportok és alkalmazások, amelyek a használati feltételek érvényesek. |
   | **Feltételes hozzáférési szabályzat létrehozása később** | A használati feltételek az engedélyezési vezérlői listában megjelennek a feltételes hozzáférési szabályzat létrehozásakor. |

   >[!IMPORTANT]
   >Feltételes hozzáférési szabályzat vezérlői (beleértve a használati feltételeket) nem támogatja a kényszerítési szolgáltatásfiókok. Azt javasoljuk, hogy zárjon ki minden szolgáltatásfiókot a feltételes hozzáférési szabályzatot.

    Egyéni feltételes hozzáférési szabályzatok engedélyezése részletes használati feltételek, egy adott felhőalapú alkalmazások vagy felhasználók csoportja. További információkért lásd: [a rövid útmutató: Felhőbeli alkalmazások elérése előtt el kell fogadni a használati feltételeket tartalmazó fájl szükséges](require-tou.md).

1. Kattintson a **Create** (Létrehozás) gombra.

   Ha egy egyéni feltételes hozzáférési sablont választott, majd egy új képernyő jelenik meg, amely lehetővé teszi, hogy az egyéni feltételes hozzáférési szabályzat létrehozása.

   ![Ha úgy döntött, hogy az egyéni feltételes hozzáférési szabályzat sablon új feltételes hozzáférési panel](./media/terms-of-use/custom-policy.png)

   Meg kell jelennie az új használati feltételek.

   ![Új használati szereplő panel használata a használati feltételek](./media/terms-of-use/create-tou.png)

## <a name="view-report-of-who-has-accepted-and-declined"></a>Jelentés megtekintése, akik elfogadó és elutasító

A használati feltételek panel megjeleníti a feltételeket elfogadó, illetve elutasító felhasználók számát. Ezeket, és akik elfogadott/nem fogadta el a teljes idejére is. a használati feltételeket tárolja.

1. Jelentkezzen be az Azure-ba, és lépjen a **Használati feltételekre** a [https://aka.ms/catou](https://aka.ms/catou) címen.

   ![Használja panel felhasználó show száma ajánlati feltételek elfogadó és elutasító](./media/terms-of-use/view-tou.png)

1. A használati feltételeket, kattintson a számokat **elfogadva** vagy **elutasítva** a felhasználók aktuális állapotának megtekintéséhez.

   ![A használati feltételek címtárbérlőhöz panel, amelyek fogadták el a felhasználók listázása](./media/terms-of-use/accepted-tou.png)

1. Az egyes felhasználók számára az előzmények megtekintéséhez kattintson a három pontra ( **...** ), majd **előzményeinek megtekintése**.

   ![Egy felhasználó előzmények helyi menü megjelenítése](./media/terms-of-use/view-history-menu.png)

   A korábbi panelen, megjelenik az összes a fogad, csökken, illetve lejárhat.

   ![Előzményei panel felsorolja az előzmények megtekintése fogad, csökken, és a egy felhasználó lejárhat](./media/terms-of-use/view-history-pane.png)

## <a name="view-azure-ad-audit-logs"></a>Naplók megtekintése az Azure AD

Ha meg szeretné tekinteni a további tevékenység, az Azure AD használati feltételek tartalmazza az auditnaplók. Minden egyes felhasználói beleegyezés elindít egy eseményt az auditnaplókban tárolt a **30 napig**. Ezeket a naplókat megtekintheti a portálon, vagy letöltheti egy .csv fájlban.

Ismerkedés az Azure AD-naplók, kövesse az alábbi eljárást:

1. Jelentkezzen be az Azure-ba, és lépjen a **Használati feltételekre** a [https://aka.ms/catou](https://aka.ms/catou) címen.
1. Válassza ki a használati feltételeket.
1. Kattintson a **Naplók megtekintése** elemre.

   ![Panel használata a nézet feltételeit audit naplók opció kiemelésével](./media/terms-of-use/audit-tou.png)

1. Az Azure AD naplókat megjelenítő képernyőjén naplózása, szűrheti az adatokat a megadott listákat az adott naplózási információkra cél használatával.

   A **Letöltés** elemre kattintva az információkat helyi felhasználás céljából egy .csv fájlba is mentheti.

   ![Az Azure AD naplók ajánlati dátuma, a cél-házirend kezdeményezte, és a tevékenység képernyő](./media/terms-of-use/audit-logs-tou.png)

   Ha egy napló gombra kattint, további tevékenység részletei ablaktábla jelenik meg.

   ![Tevékenység részletei naplók, tevékenység állapota, által kezdeményezett tevékenységek, megjelenítése a szabályzatok célzásához](./media/terms-of-use/audit-log-activity-details.png)

## <a name="what-terms-of-use-looks-like-for-users"></a>Milyen használati feltételek tűnik a felhasználók számára

A használati feltételeket tartalmazó fájl létrehozása és kényszerítése, ha felhasználók, akik érintett bejelentkezés során a következő képernyő jelenik meg.

![Példa használati feltételei jelenik meg, amikor egy felhasználó bejelentkezik](./media/terms-of-use/user-tou.png)

A felhasználók a használati feltételek megtekintése és, ha szükséges, használja a gombok, nagyíthat és.

![Nagyítás gombokkal felhasználási feltételek megtekintése](./media/terms-of-use/zoom-buttons.png)

A következő képernyőn látható használati feltételeket tartalmazó fájl hogyan néz ki a mobileszközökön.

![Példa használati feltételei jelenik meg, amikor egy felhasználó bejelentkezik a mobileszközökön](./media/terms-of-use/mobile-tou.png)

A felhasználóknak csak kell fogadja el a használati feltételeket egyszer, és nem láthatják a használati feltételeket újra a későbbi bejelentkezések.

### <a name="how-users-can-review-their-terms-of-use"></a>Hogyan felhasználók tekintheti át a használati feltételeket

A felhasználók áttekinthetik és tekintse meg a használati feltételeket, amelyek az általuk elfogadott, a következő eljárás használatával.

1. Jelentkezzen be itt: [https://myapps.microsoft.com](https://myapps.microsoft.com).
1. A jobb felső sarokban, kattintson a nevére, és válassza **profil**.

   ![A felhasználói panelen nyissa meg a MyApps webhelyen](./media/terms-of-use/tou14.png)

1. A Profi lapon kattintson a **Használati feltételek áttekintése** elemre.

   ![Bemutató kapcsolat használata a felülvizsgálati használati felhasználó profillapján](./media/terms-of-use/tou13a.png)

1. Itt áttekintheti az elfogadott használati feltételeket.

## <a name="edit-terms-of-use-details"></a>Használati feltételek részleteinek szerkesztése

Szerkesztheti a használati feltételeket tartalmazó fájl bizonyos részleteit, de nem módosíthatja egy meglévő dokumentumot. Az alábbi eljárás ismerteti, hogyan lehet az adatok szerkesztéséhez.

1. Jelentkezzen be az Azure-ba, és lépjen a **Használati feltételekre** a [https://aka.ms/catou](https://aka.ms/catou) címen.
1. Válassza ki a szerkeszteni kívánt használati feltételeket.
1. Kattintson a **feltételeket tartalmazó fájl szerkesztése**.
1. A használati ablaktábla Szerkesztés feltételeit módosítsa a nevet, megjelenítendő nevet vagy értékek felhasználóknak legyen kötelező.

   Ha más beállítás, amelyet szeretne módosítani, például PDF-dokumentum, kérése a felhasználóktól minden eszközön hozzájárulás, hozzájárulást, hamarosan lejár időtartama reacceptance, vagy a feltételes hozzáférési szabályzat, előtt létre kell hoznia egy új használati feltételek.

   ![Ablaktábla megjelenítése név használata feltételeket tartalmazó fájl szerkesztése, és bontsa ki a beállítások](./media/terms-of-use/edit-tou.png)

1. Kattintson a **mentése** a módosítások mentéséhez.

   A módosítások mentése után a felhasználók nem kell ezeket a szerkesztéseket hatókörébe.

## <a name="add-a-terms-of-use-language"></a>A használati feltételek használata nyelv hozzáadása

Az alábbi eljárás ismerteti, hogyan lehet a használati feltételek használata nyelv hozzáadása.

1. Jelentkezzen be az Azure-ba, és lépjen a **Használati feltételekre** a [https://aka.ms/catou](https://aka.ms/catou) címen.
1. Válassza ki a szerkeszteni kívánt használati feltételeket.
1. A részleteket tartalmazó ablaktáblán kattintson a **nyelvek** fülre.

   ![Használati feltételeinek kiválasztott és nyelvek lap megjelenítése a részletek panelen](./media/terms-of-use/languages-tou.png)

1. Kattintson a **nyelv hozzáadása**.
1. A honosított PDF-fájl feltöltése a Hozzáadás használati használata nyelvi ablaktáblán, és válassza ki a nyelvet.

   ![Kifejezések használata nyelvi ablaktábla honosított PDF-fájlok feltöltése beállításokkal hozzáadása](./media/terms-of-use/language-add-tou.png)

1. Kattintson a **Hozzáadás** a nyelv hozzáadása.

## <a name="per-device-terms-of-use"></a>Az eszközszintű használati feltételeket

A **kérése a felhasználóktól minden eszközön jóváhagyást** beállítás lehetővé teszi, hogy a végfelhasználók számára, hogy fogadja el a használati feltételeket minden eszközön érnek el a szükséges. A felhasználó köteles csatlakoztassák az eszközüket az Azure ad-ben. Amikor az eszköz tartományhoz csatlakozik, az eszköz azonosítója segítségével kényszerítése a használati feltételeket minden eszközön.

Íme a támogatott platformokról és a szoftverek listáját.

> [!div class="mx-tableFixed"]
> |  | iOS | Android | Windows 10 | Egyéb |
> | --- | --- | --- | --- | --- |
> | **Natív alkalmazás** | Igen | Igen | Igen |  |
> | **Microsoft Edge** | Igen | Igen | Igen |  |
> | **Az Internet Explorer** | Igen | Igen | Igen |  |
> | **Chrome (kiterjesztésű)** | Igen | Igen | Igen |  |

Az eszközönkénti használati feltételeket tartalmazó fájl a következő korlátozások vonatkoznak:

- Egy eszköz csak egyetlen bérlő lehet csatlakoztatni.
- A felhasználónak jogosultnak kell lennie csatlakoztassák az eszközüket.
- Az Intune-regisztráció alkalmazás nem támogatott.
- Az Azure AD B2B-felhasználók nem támogatottak.

Ha a felhasználó eszköze nincs tartományhoz csatlakoztatva, egy üzenet csatlakoztassák az eszközüket a kapja meg. Szerzett függ a szoftver és platform lesz.

### <a name="join-a-windows-10-device"></a>Windows 10-es eszköz csatlakoztatása

Ha a felhasználó nem használja a Windows 10-es és a Microsoft Edge, a következőhöz hasonló üzenetet kapja [csatlakoztassák az eszközüket](../user-help/user-help-join-device-on-network.md#to-join-an-already-configured-windows-10-device).

![Windows 10-es és a Microsoft Edge - üzenetet, amely az eszköz regisztrálva kell lennie](./media/terms-of-use/per-device-win10-edge.png)

Ha Chrome, kérni fogja telepíteni a [Windows 10-es fiókok bővítmény](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji).

### <a name="browsers"></a>Böngészők

Ha egy felhasználó által nem támogatott böngészőt használ, azokat egy másik böngészőben használandó adnia.

![Regisztrálni kell az eszközt, de a böngésző nem támogatott jelző üzenet](./media/terms-of-use/per-device-browser-unsupported.png)

## <a name="delete-terms-of-use"></a>Használati feltételek törlése

Korábbi használati feltételeket az alábbi eljárással törölheti.

1. Jelentkezzen be az Azure-ba, és lépjen a **Használati feltételekre** a [https://aka.ms/catou](https://aka.ms/catou) címen.
1. Válassza ki az eltávolítani kívánt használati feltételeket.
1. Kattintson a **Feltételek törlése** lehetőségre.
1. A megjelenő üzenetben, amely a törlés megerősítését kéri, kattintson az **Igen** lehetőségre.

   ![Használati feltételeket tartalmazó fájl törlése a megerősítést kérő üzenet](./media/terms-of-use/delete-tou.png)

   Már megtekintheti a használati feltételeket.

## <a name="deleted-users-and-active-terms-of-use"></a>Törölt felhasználók és aktív használati feltételeket

Alapértelmezés szerint a törölt felhasználók 30 napig még törölt állapotban megmaradnak az Azure AD-ban, amely idő alatt szükség esetén egy rendszergazda visszaállíthatja őket. A felhasználók 30 nap után véglegesen törlődnek. Ezenkívül egy globális rendszergazda az Azure Active Directory portálon explicit módon [véglegesen törölhet egy közelmúltban törölt felhasználót](../fundamentals/active-directory-users-restore.md) az időszak lejárta előtt. Egy felhasználó véglegesen törölve lett, a későbbi adatait, hogy a felhasználó törlődni fog a aktív használati feltételeit. A törölt felhasználókkal kapcsolatos naplózási információk az auditnaplóban maradnak.

## <a name="policy-changes"></a>A szabályzatok változásai

Feltételes hozzáférési szabályzatok azonnal hatályba lépnek. Ha ez történik, a rendszergazda "Szomorú felhők" vagy "A tokenekkel kapcsolatos problémák az Azure AD" indul el. A rendszergazda kell jelentkezzen ki, és jelentkezzen be újra annak érdekében, hogy megfelelhessen az új szabályzatnak.

> [!IMPORTANT]
> Az érintett felhasználóknak újra be kell jelentkezniük, hogy megfelelhessenek az új szabályzatnak, ha:
>
> - egy feltételes hozzáférési szabályzata engedélyezve van a használati feltételek
> - létrehoztak újabb használati feltételeket

## <a name="b2b-guests-preview"></a>B2B-vendégek (előzetes verzió)

A legtöbb szervezet tartalmaznak egy folyamat a felhasználók számára, hogy engedélyt adjanak a cég használati feltételei és adatvédelmi nyilatkozatok. De hogyan is kényszeríti az azonos hozzájárulást az Azure AD-vállalatok (B2B), amikor hozzáadják a SharePoint vagy a Teams vendégek? Feltételes hozzáférési és használati feltételeket tartalmazó fájl segítségével, akkor olyan szabályzatot alkalmazhatnak közvetlenül a B2B-vendég felhasználók felé. A meghívó érvényesítési folyamat során a felhasználó a használati feltételek egyike. Ez a támogatás jelenleg előzetes verzióban érhető el.

Használati feltételek csak jelenik meg, ha a felhasználó egy vendégfiókba rendelkezik az Azure ad-ben. SharePoint online-ban jelenleg rendelkezik olyan [alkalmi külső megosztási címzett felülettel](/sharepoint/what-s-new-in-sharing-in-targeted-release) megosztani egy dokumentumot vagy egy mappát, amely nem igényel a felhasználót, hogy a Vendég fiókkal rendelkezik. Ebben az esetben a használati feltételeket tartalmazó fájl nem jelenik meg.

![Felhasználók és csoportok panelen – például lapon az összes vendég felhasználók beállítás be van jelölve](./media/terms-of-use/b2b-guests.png)

## <a name="support-for-cloud-apps-preview"></a>Felhőbeli alkalmazások (előzetes verzió) támogatása

Másik felhőalapú alkalmazások, például az Azure Information Protection és a Microsoft Intune használati feltételeit is használható. Ez a támogatás jelenleg előzetes verzióban érhető el.

### <a name="azure-information-protection"></a>Azure Information Protection

Az Azure Information Protection alkalmazásra vonatkozó feltételes hozzáférési házirend konfigurálása és a használati feltételek kérése, amikor egy felhasználó hozzáfér egy védett dokumentumot. Ezzel elindítja a használati feltételeket, mielőtt a felhasználó első alkalommal fér hozzá a védett dokumentum.

![Felhőbeli alkalmazások panelről a Microsoft Azure Information Protection alkalmazás kiválasztva](./media/terms-of-use/cloud-app-info-protection.png)

### <a name="microsoft-intune-enrollment"></a>A Microsoft Intune-regisztráció

Feltételes hozzáférési szabályzatot konfigurálni ahhoz a Microsoft Intune regisztrációs alkalmazást, és a használati feltételeket, mielőtt az eszköz Intune-beli regisztrációját megkövetelése. További információkért lásd: az olvasási [kiválasztani a feltételek megoldást kínál a szervezet blogbejegyzés](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409).

![Felhőbeli alkalmazások panelről a Microsoft Intune alkalmazás kiválasztva](./media/terms-of-use/cloud-app-intune.png)

> [!NOTE]
> Az Intune-regisztráció alkalmazás nem támogatott a [eszközönkénti használati feltételeit](#per-device-terms-of-use).

## <a name="frequently-asked-questions"></a>Gyakori kérdések

**K: Hogyan ellenőrizhetem amikor / felhasználó a használati feltételek elfogadása?**<br />
V: A panel használata a feltételeket, kattintson a számra **elfogadva**. Is megtekintése vagy keressen a elfogadás tevékenység az Azure AD-ben a naplók. További információkért tekintse meg a megtekintése, akik elfogadó és elutasító jelentés és [naplók megtekintése az Azure AD](#view-azure-ad-audit-logs).

**K: Mennyi ideig őrzi meg tárolt adatokat?**<br />
V: A felhasználó-adatokra is használja a jelentés és aki elfogadott/nem fogadta el a használati feltételeket élettartama tárolt feltételeit. Az Azure AD naplózási naplót 30 napig tárol.

**K: Miért látok a feltételeit, és az Azure AD használati jelentés címtárbérlőhöz különböző számú naplók?**<br />
V: A használati jelentés használata a használati feltételeket, miközben a naplót 30 napig tárol az Azure AD naplózási élettartama nem történik. A használati jelentés használatát is, csak a felhasználók aktuális jóváhagyási állapotot jeleníti meg. Például ha egy felhasználó elutasítja, és majd elfogadja a használati jelentés használata csak akkor jelenik meg, hogy a felhasználó fogadja el. Ha szeretne látni a korábbi, használhatja az Azure AD auditnaplóinak.

**K: Tudom szerkeszteni a részletek a használati feltételeket, ha nem írja elő a felhasználóknak újra el kell fogadniuk?**<br />
V: Nem, ha egy rendszergazda szerkeszt a részletek a használati feltételek (nevet, megjelenítendő nevet, felhasználóknak legyen kötelező vagy nyelv hozzáadása), nem szükséges a felhasználók számára az új feltételeket hatókörébe.

**K: Frissíti egy meglévő használati feltételek dokumentuma is?**<br />
V: Jelenleg nem lehet frissíteni egy meglévő használati feltételek dokumentuma. Ha módosítani szeretné a használati feltételek dokumentuma, akkor új feltételek használata-példány létrehozása.

**K: Ha a hivatkozások a használati feltételek PDF dokumentuma, a végfelhasználók fogja tudni azokra kattint?**<br />
V: A PDF-fájl jelenik meg alapértelmezés szerint, JPEG, így nem kattintható hivatkozások állnak. Felhasználók rendelkeznek a kívánt **megtekintéssel? Kattintson ide a**, amely a PDF-fájl rendereli natív módon, a hivatkozások támogatottak.

**K: Használati feltételek több nyelv támogatása is?**<br />
V: Igen. Jelenleg nincsenek 108 több különböző nyelvet a rendszergazda konfigurálhatja egy egyetlen használati feltételeit. A rendszergazdák több PDF-dokumentumok feltöltése, és ezeket a dokumentumokat a megfelelő nyelvű (akár 108) címkét. Amikor a végfelhasználók jelentkeznek be, azt tekintse meg a böngésző nyelvi beállításait, és megjeleníti az egyező dokumentumok. Ha nem szerepel, hogy az alapértelmezett dokumentum, amely az első dokumentum feltöltött jelennek meg.

**K: Ha van a használati feltételek?**<br />
V: A használati feltételeket a bejelentkezési élmény során aktiválódik.

**K: Mely alkalmazásokhoz használhatom a használati feltételeket?**<br />
V: Feltételes hozzáférési szabályzatot a modern hitelesítést használó vállalati alkalmazásokhoz hozhat létre. Bővebb információ: [vállalati alkalmazások](./../manage-apps/view-applications-portal.md).

**K: Egy adott felhasználó vagy alkalmazás is hozzáadhatok több használati feltételeket?**<br />
V: Igen, ha több feltételes hozzáférési házirendek adott csoportokhoz vagy alkalmazásokhoz. Ha egy felhasználó több használati hatókörébe esik, el kell fogadniuk egy használati feltételek egyszerre.

**K: Mi történik, ha a felhasználó elutasítja a használati feltételeket?**<br />
V: A felhasználó hozzáférése az alkalmazáshoz való hozzáférés le van tiltva. Jelentkezzen be újra, és fogadja el a feltételeket ahhoz, hogy a hozzáférést a felhasználónak kell.

**K: Az korábban elfogadott használati feltételek unaccept is?**<br />
V: Is [felülvizsgálati korábban elfogadott használati feltételeket tartalmazó fájl](#how-users-can-review-their-terms-of-use), de jelenleg nincs unaccept lehetővé.

**K: Mi történik, ha az Intune használati feltételeket is használata?**<br />
V: Ha konfigurálta is az Azure AD használati feltételek és [Intune használati feltételek](/intune/terms-and-conditions-create), a felhasználó köteles fogadnia mindkettőt. További információkért lásd: a [kiválasztani a feltételek megoldást kínál a szervezet blogbejegyzés](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409).

**K: Milyen végpontokat nem használható szolgáltatási feltételeit használja a hitelesítéshez?**<br />
V: Használati feltételek használja az alábbi végpontok a hitelesítéshez: https://tokenprovider.termsofuse.identitygovernance.azure.com és https://account.activedirectory.windowsazure.com. Ha a szervezete egy regisztrációs URL-címek engedélyezési listája, szüksége lesz a adja hozzá ezeket a végpontokat az engedélyezési listához, és az Azure ad-ben végpontjainak jelentkezzen be.

## <a name="next-steps"></a>További lépések

- [Rövid útmutató: Felhőbeli alkalmazások elérése előtt el kell fogadni a használati feltételeket tartalmazó fájl megkövetelése](require-tou.md)
- [Ajánlott eljárások az Azure Active Directory feltételes hozzáférés](best-practices.md)
