---
title: Rugalmas hozzáférés-vezérlési kezelési stratégia létrehozása – Azure Active Directory
description: Ez a dokumentum útmutatást nyújt azon stratégiákról, amelyeket a szervezetnek el kell fogadnia a rugalmasság biztosításához, hogy a váratlan megszakítások során csökkentse a zárolás kockázatát
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 12/19/2018
ms.author: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: c9be48d8f403d3ddde993ebdcf0142b55e52afce
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779674"
---
# <a name="create-a-resilient-access-control-management-strategy-with-azure-active-directory"></a>Rugalmas hozzáférés-vezérlési felügyeleti stratégia létrehozása Azure Active Directory

>[!NOTE]
> A jelen dokumentumban szereplő információk a Microsoft Corporation aktuális nézetét jelentik a kiadvány napjával kapcsolatban tárgyalt problémákról. Mivel a Microsoftnak válaszolnia kell a piaci feltételek megváltoztatására, nem kell értelmezni a Microsoft részéről vállalt kötelezettséget, és a Microsoft nem tudja garantálni a közzététel időpontja után bemutatott információk pontosságát.

Azok a szervezetek, amelyek egyetlen hozzáférés-vezérlést (például többtényezős hitelesítést (MFA) vagy egyetlen hálózati helyet) használnak az informatikai rendszerek biztonságossá tételéhez, az alkalmazásokhoz és erőforrásokhoz való hozzáférésük nem érhető el, ha az egyetlen hozzáférés-vezérlés elérhetetlenné válik vagy helytelenül van konfigurálva. A természeti katasztrófák például a nagy mennyiségű távközlési infrastruktúra vagy vállalati hálózatok hiányának hiányát okozhatják. Ilyen fennakadás miatt megakadályozható, hogy a végfelhasználók és a rendszergazdák be tudják jelentkezni.

Ez a dokumentum útmutatást nyújt azon stratégiákról, amelyeket a szervezetnek el kell fogadnia a rugalmasság biztosításához, hogy csökkentse a zárolás kockázatát a váratlan megszakítások során a következő esetekben:

 1. A szervezetek növelhetik rugalmasságát, hogy a kockázatcsökkentő stratégiák vagy a készenléti tervek bevezetése **előtt** csökkentse a zárolás kockázatát.
 2. A szervezetek továbbra is hozzáférhetnek **a** megszakadáskor kiválasztott alkalmazásokhoz és erőforrásokhoz azáltal, hogy kockázatcsökkentő stratégiákat és vészhelyzeti terveket alkalmaznak.
 3. A szervezeteknek meg kell győződniük arról, hogy az adatok megmaradnak, például a naplók, **megszakítás után** , és az általuk megvalósított szükséghelyzetek visszaállítása előtt.
 4. Azok a szervezetek, amelyek még nem hajtották végre a megelőzési stratégiákat vagy az alternatív terveket, képesek lehetnek a fennakadások kezelésére szolgáló **vészhelyzeti lehetőségek** megvalósítására.

## <a name="key-guidance"></a>Legfontosabb útmutató

A dokumentumban négy fő elvihető fájl található:

* A rendszergazdai zárolás elkerülése vészhelyzeti hozzáférési fiókok használatával.
* MFA alkalmazása feltételes hozzáféréssel (CA) a felhasználónkénti MFA helyett.
* A felhasználók zárolásának enyhítése több feltételes hozzáférési (CA) vezérlőelem használatával.
* A felhasználók zárolásának enyhítése több hitelesítési módszer vagy ezzel egyenértékű érték kiépítve minden felhasználó számára.

## <a name="before-a-disruption"></a>Megszakítás előtt

A tényleges fennakadások enyhítése csak a szervezet elsődleges fókusza lehet a hozzáférés-vezérlési problémák megoldásához. A mérséklés magában foglalja a tényleges esemény megtervezését és a stratégiák bevezetését annak érdekében, hogy a hozzáférés-vezérlés és a műveletek ne legyenek hatással a megszakadások során.

### <a name="why-do-you-need-resilient-access-control"></a>Miért van szüksége rugalmas hozzáférés-vezérlésre?

 Az identitás az alkalmazásokhoz és erőforrásokhoz hozzáférő felhasználók vezérlő síkja. Az Identity rendszer szabályozza, hogy mely felhasználók és milyen feltételek mellett, például hozzáférés-vezérlési vagy hitelesítési követelmények esetén a felhasználók hozzáférjenek az alkalmazásokhoz. Ha egy vagy több hitelesítési vagy hozzáférés-vezérlési követelmény nem érhető el a felhasználók számára előre nem látható körülmények miatt, akkor a szervezetek az alábbi problémák egyikét vagy mindkettőt tapasztalják:

* **Rendszergazdai zárolás:** A rendszergazdák nem kezelhetik a bérlőt vagy a szolgáltatásokat.
* **Felhasználói zárolás:** A felhasználók nem férhetnek hozzá az alkalmazásokhoz és az erőforrásokhoz.

### <a name="administrator-lockout-contingency"></a>Rendszergazdai zárolás készenléti

A bérlőhöz való rendszergazdai hozzáférés zárolásának feloldásához hozzon létre vészhelyzeti hozzáférési fiókokat. Ezek a vészhelyzeti hozzáférési fiókok – más néven a *break Glass* -fiókok – lehetővé teszik az Azure ad-konfiguráció felügyeletét, amikor a normál jogosultsági szintű fiók hozzáférési eljárásai nem érhetők el. Legalább két vészhelyzeti hozzáférési fiókot kell létrehozni a segélyhívó [fiókra vonatkozó javaslatok]( https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access)követésével.

### <a name="mitigating-user-lockout"></a>Felhasználói zárolás csökkentése

 A felhasználói zárolás kockázatának enyhítése érdekében a feltételes hozzáférési szabályzatok segítségével több vezérlővel is biztosíthatja a felhasználók számára az alkalmazások és erőforrások elérését. Ha a felhasználó számára választ ad például az MFA-ba való bejelentkezésre **vagy** egy felügyelt eszközről való bejelentkezésre **vagy** a vállalati hálózatról való bejelentkezésre, ha az egyik hozzáférési vezérlő nem érhető el, a felhasználó további lehetőségeket is biztosít a munka folytatásához.

#### <a name="microsoft-recommendations"></a>Microsoft-javaslatok

A következő hozzáférés-vezérlést a szervezet meglévő feltételes hozzáférési házirendjeiben foglalja bele:

1. Több hitelesítési módszer is kiépíthető minden olyan felhasználó számára, aki különböző kommunikációs csatornákra támaszkodik, például az Microsoft Authenticator alkalmazás (internetalapú), az eskü token (az eszközön generált) és az SMS (telefonos kapcsolat).
2. A Windows Hello for Business üzembe helyezése Windows 10-es eszközökön az MFA-követelmények közvetlenül az eszköz-bejelentkezésből való kielégítéséhez.
3. Megbízható eszközöket használhat az [Azure ad Hybrid JOIN](https://docs.microsoft.com/azure/active-directory/devices/overview) vagy [Microsoft Intune felügyelt eszközökön](https://docs.microsoft.com/intune/planning-guide)keresztül. A megbízható eszközök növelik a felhasználói élményt, mivel maga a megbízható eszköz is kielégítheti a házirend erős hitelesítési követelményeit, ha a felhasználó nem rendelkezik MFA-kihívással. Az MFA ezt követően új eszköz beléptetéséhez és a nem megbízható eszközökről származó alkalmazások vagy erőforrások eléréséhez szükséges.
4. Az Azure AD Identity Protection kockázatkezelési házirendjeinek használatával megakadályozhatja a hozzáférést, ha a felhasználó vagy a bejelentkezés veszélyben van a rögzített MFA-szabályzatok helyett.

>[!NOTE]
> A kockázatalapú házirendekhez [prémium szintű Azure ad P2](https://azure.microsoft.com/pricing/details/active-directory/) licencek szükségesek.

Az alábbi példa azokat a szabályzatokat ismerteti, amelyeket létre kell hoznia, hogy rugalmas hozzáférés-vezérlést biztosítson a felhasználók számára az alkalmazások és erőforrások eléréséhez. Ebben a példában egy biztonsági csoport **AppUsers** kell lennie azokkal a célcsoportokkal, amelyeknek hozzáférést kíván adni a szolgáltatáshoz, egy **CoreAdmins** nevű csoportot az alapvető rendszergazdákkal, valamint egy **EmergencyAccess** nevű csoportot a sürgősségi hozzáférési fiókokkal.
Ez a példa a kiválasztott felhasználók számára biztosítja a **AppUsers**, hozzáférést biztosít a kiválasztott alkalmazásokhoz, ha azok megbízható eszközről csatlakoznak, vagy erős hitelesítést biztosítanak, például MFA-t. Kizárja a vészhelyzeti fiókokat és az alapvető rendszergazdákat.

**HITELESÍTÉSSZOLGÁLTATÓ-kockázatcsökkentő házirendek készlete:**

* 1\. szabályzat: A célcsoportokon kívüli személyek hozzáférésének letiltása
  * Felhasználók és csoportok: Minden felhasználó belefoglalása. A AppUsers, a CoreAdmins és a EmergencyAccess kizárása
  * Felhőalapú alkalmazások: Minden alkalmazás belefoglalása
  * Feltételek (nincs)
  * Ellenőrzés engedélyezése: Letiltás
* 2\. szabályzat: Az MFA-t vagy a megbízható eszközt igénylő AppUsers való hozzáférés biztosítása.
  * Felhasználók és csoportok: AppUsers belefoglalása. CoreAdmins és EmergencyAccess kizárása
  * Felhőalapú alkalmazások: Minden alkalmazás belefoglalása
  * Feltételek (nincs)
  * Ellenőrzés engedélyezése: Hozzáférés biztosítása, többtényezős hitelesítés megkövetelése, az eszköz megfelelőségének megkövetelése. Több vezérlő esetén: A kiválasztott vezérlők egyikének megkövetelése.

### <a name="contingencies-for-user-lockout"></a>Felhasználói zárolási szükséghelyzetek

Másik lehetőségként a szervezet is létrehozhat készenléti házirendeket. A készenléti szabályzatok létrehozásához meg kell határoznia a kompromisszumok feltételeit az üzletmenet folytonossága, az üzemeltetési költségeket, a pénzügyi költségeket és a biztonsági kockázatokat illetően. Előfordulhat például, hogy a készenléti szabályzatot csak a felhasználók egy részhalmazára, az alkalmazások egy részhalmazára, az ügyfelek egy részhalmazára vagy a helyszínek egy részhalmazára aktiválja. A készenléti szabályzatok a rendszergazdák és a végfelhasználók számára hozzáférést biztosítanak az alkalmazásokhoz és az erőforrásokhoz, a fennakadás során, amikor nincs implementálva a megoldás.
A megszakadás során felmerülő expozíció révén csökkentheti a kockázatát, és a tervezési folyamat kritikus részét képezi. A készenléti terv létrehozásához először határozza meg a szervezet alábbi üzleti követelményeit:

1. A kritikus fontosságú alkalmazások megállapítása az idő előtt: Mik azok az alkalmazások, amelyeknek hozzáférést kell biztosítania, még alacsonyabb kockázati/biztonsági testhelyzet esetén is? Készítse el az alkalmazások listáját, és győződjön meg arról, hogy az érdekelt felek (üzleti, biztonsági, jogi, vezető) egyetértenek abban, hogy ha az összes hozzáférés-vezérlés leáll, az alkalmazások továbbra is futniuk kell. Valószínűleg a következő kategóriákkal fog foglalkozni:
   * **1. kategória – olyan kritikus fontosságú alkalmazások** , amelyek nem érhetők el néhány percen belül, például olyan alkalmazások esetében, amelyek közvetlenül érintik a szervezet bevételét.
   * **2. kategóriába tartozó fontos alkalmazások** , amelyeknek az üzleti tevékenységnek néhány órán belül elérhetőnek kell lennie.
   * **3. kategória alacsony prioritású alkalmazások** , amelyek elérhetik néhány nap megszakadását.
2. Az 1. és a 2. kategóriába tartozó alkalmazások esetében a Microsoft azt javasolja, hogy előre tervezze meg, hogy milyen típusú hozzáférési szintet kíván engedélyezni:
   * Szeretné engedélyezni a teljes hozzáférést vagy a korlátozott munkamenetet, például a letöltések korlátozását?
   * Szeretné engedélyezni az alkalmazás egy részének elérését, de nem a teljes alkalmazást?
   * Engedélyezi az Information Worker-hozzáférést, és letiltja a rendszergazdai hozzáférést a hozzáférés-vezérlés visszaállítása előtt?
3. Ezekhez az alkalmazásokhoz a Microsoft azt is javasolja, hogy tervezze meg, hogy mely hozzáférési utakat kell megnyitnia, és melyeket kell majd lezárulnia:
   * Engedélyezi a böngésző számára, hogy csak a kapcsolat nélküli adatvesztést biztosító, gazdag ügyfeleket mentsen el?
   * Szeretné engedélyezni a hozzáférést csak a vállalati hálózaton belüli felhasználók számára, és hogy a felhasználók ne legyenek letiltva?
   * Engedélyezi bizonyos országok vagy régiók hozzáférését csak a megszakadáskor?
   * Szeretné, hogy a szabályzatok a készenléti házirendekre legyenek, különösen a kritikus fontosságú alkalmazások esetében, ha nem sikerül egy alternatív hozzáférés-vezérlés?

#### <a name="microsoft-recommendations"></a>Microsoft-javaslatok

A készenléti feltételes hozzáférési szabályzat olyan **letiltott szabályzat** , amely kihagyja az Azure MFA, a harmadik féltől származó MFA, a kockázatalapú vagy az eszközökön alapuló vezérlőket. Ezután, amikor a szervezet úgy dönt, hogy aktiválja a készenléti tervet, a rendszergazdák engedélyezhetik a szabályzatot, és letilthatják a normál vezérlőn alapuló házirendeket.

>[!IMPORTANT]
> A felhasználók biztonsági állapotát kényszerítő házirendek letiltása – akár átmenetileg is – csökkenti a biztonsági helyzetét, miközben a készenléti terv teljesül.

* Állítsa be a tartalék szabályzatok készletét, ha az egyik hitelesítőadat-típus vagy egy hozzáférés-vezérlési mechanizmus megszakad az alkalmazásokhoz való hozzáférés. Állítsa be a házirendet letiltott állapotba, amely megköveteli a tartományhoz való csatlakozást vezérlőként, egy olyan aktív házirend biztonsági mentését, amely harmadik féltől származó MFA-szolgáltatót igényel.
* A jelszóval kapcsolatos [útmutatóban](https://aka.ms/passwordguidance) ismertetett eljárások követésével csökkentheti a nem kötelezően megjelenő jelszavak kialakulásának kockázatát, ha az MFA nem szükséges.
* Az [Azure ad önkiszolgáló jelszó-visszaállítás (SSPR)](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr) és az [Azure ad jelszavas védelem](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-ban-bad-on-premises-deploy) üzembe helyezésével gondoskodhat arról, hogy a felhasználók ne használják a közös jelszót és a megtiltani kívánt kifejezéseket.
* Használjon olyan házirendeket, amelyek korlátozzák a hozzáférést az alkalmazásokon belül, ha egy bizonyos hitelesítési szint nem érhető el ahelyett, hogy egyszerűen vissza kellene térnie a teljes hozzáféréshez. Példa:
  * Olyan biztonsági mentési szabályzatot konfigurálhat, amely a korlátozott munkamenet-jogcímet az Exchange és a SharePoint szolgáltatásba küldi
  * Ha a szervezet Microsoft Cloud App Security használ, érdemes lehet visszaesnie egy olyan házirendnek, amely a MCAS-t, majd a MCAS lehetővé teszi a csak olvasási hozzáférést, de a feltöltéseket nem.
* Nevezze el a szabályzatokat, és győződjön meg róla, hogy könnyen megtalálhatja őket a megszakítás során. Adja meg a következő elemeket a szabályzat nevében:
  * A házirend *feliratának száma* .
  * A megjelenítendő szöveg, ez a szabályzat csak vészhelyzetekre vonatkozik. Példa: **ENGEDÉLYEZÉS VÉSZHELYZETBEN**
  * A rendszer a megszakadást alkalmazza. Példa: **Az MFA megszakadásakor**
  * A szabályzatok aktiválásához szükséges *sorszám* .
  * Azok az *alkalmazások* , amelyekre érvényesek.
  * Az alkalmazandó *vezérlők* .
  * A szükséges *feltételek* .
  
A készenléti szabályzatok elnevezési szabványa a következőképpen fog megjelenni: 

```
EMnnn - ENABLE IN EMERGENCY: [Disruption][i/n] - [Apps] - [Controls] [Conditions]
```

A következő példa: **Példa a feltételes hitelesítésszolgáltatói házirendre, amely a kritikus fontosságú együttműködési alkalmazásokhoz való hozzáférést állítja vissza**, ez egy tipikus vállalati készenléti feladat. Ebben az esetben a szervezet általában MFA-t igényel az összes Exchange Online-és SharePoint Online-hozzáféréshez, és ebben az esetben a megszakadás az ügyfél MFA-szolgáltatója (akár az Azure MFA, a helyszíni MFA-szolgáltató, akár a harmadik féltől származó MFA) esetében is. Ez a házirend csökkenti ezt a kimaradást azáltal, hogy a célzott felhasználók csak akkor férnek hozzá ezekhez az alkalmazásokhoz a megbízható Windows-eszközökről, ha a megbízható vállalati hálózatról érik el az alkalmazást. Emellett kizárja a vészhelyzeti fiókokat és az alapvető rendszergazdákat ezektől a korlátozásoktól. A megcélozott felhasználók ezután hozzáférhetnek az Exchange Online-hoz és a SharePoint Online-hoz, míg más felhasználók továbbra sem férhetnek hozzá az alkalmazásokhoz a leállás miatt. Ehhez a példához meg kell nevezni a hálózati hely **CorpNetwork** és egy biztonsági csoportot a **ContingencyAccess** , egy **CoreAdmins** nevű csoportot az alapvető rendszergazdákkal, valamint egy **EmergencyAccess** nevű csoportot a következővel a vészhelyzeti hozzáférési fiókok. A készenléti feltételnek négy házirendre van szüksége a kívánt hozzáférés biztosításához. 

**Példa A-készenléti HITELESÍTÉSSZOLGÁLTATÓI szabályzatokra a kritikus fontosságú együttműködési alkalmazások elérésének visszaállításához:**

* 1\. szabályzat: Tartományhoz csatlakoztatott eszközök megkövetelése az Exchange-hez és a SharePointhoz
  * Név: EM001 – ENGEDÉLYEZÉS VÉSZHELYZETBEN: MFA-megszakítás [1/4] – Exchange SharePoint – hibrid Azure AD-csatlakozás szükséges
  * Felhasználók és csoportok: ContingencyAccess belefoglalása. CoreAdmins és EmergencyAccess kizárása
  * Felhőalapú alkalmazások: Exchange Online és SharePoint Online
  * Feltételek Any
  * Ellenőrzés engedélyezése: Tartományhoz való csatlakozás megkövetelése
  * Állapot: Letiltva
* 2\. szabályzat: A Windowstól eltérő platformok letiltása
  * Név: EM002 – ENGEDÉLYEZÉS VÉSZHELYZETBEN: MFA-megszakítás [2/4] – Exchange SharePoint – hozzáférés tiltása a Windows kivételével
  * Felhasználók és csoportok: Minden felhasználó belefoglalása. CoreAdmins és EmergencyAccess kizárása
  * Felhőalapú alkalmazások: Exchange Online és SharePoint Online
  * Feltételek Az eszköz platformja minden platformot tartalmaz, kizárja a Windowst
  * Ellenőrzés engedélyezése: Letiltás
  * Állapot: Letiltva
* 3\. szabályzat: A CorpNetwork-től eltérő hálózatok blokkolása
  * Név: EM003 – ENGEDÉLYEZÉS VÉSZHELYZETBEN: MFA-megszakítás [3/4] – Exchange SharePoint – hozzáférés tiltása a vállalati hálózaton kívül
  * Felhasználók és csoportok: Minden felhasználó belefoglalása. CoreAdmins és EmergencyAccess kizárása
  * Felhőalapú alkalmazások: Exchange Online és SharePoint Online
  * Feltételek A helyek közé tartozik a hely, a CorpNetwork kizárása
  * Ellenőrzés engedélyezése: Letiltás
  * Állapot: Letiltva
* 4\. szabályzat: EAS explicit letiltása
  * Név: EM004 – ENGEDÉLYEZÉS VÉSZHELYZETBEN: MFA-megszakítás [4/4] – Exchange-Block EAS az összes felhasználó számára
  * Felhasználók és csoportok: Minden felhasználó belefoglalása
  * Felhőalapú alkalmazások: Az Exchange Online-t is beleértve
  * Feltételek Ügyfélalkalmazások: Exchange Active Sync
  * Ellenőrzés engedélyezése: Letiltás
  * Állapot: Letiltva

Aktiválási sorrend:

1. Zárja ki a ContingencyAccess, a CoreAdmins és a EmergencyAccess a meglévő MFA-szabályzatból. A ContingencyAccess felhasználóinak ellenőrzése a SharePoint Online és az Exchange Online szolgáltatáshoz is hozzáférhet.
2. 1\. szabályzat engedélyezése: Győződjön meg arról, hogy a tartományhoz csatlakoztatott eszközökön a kizárási csoportokban nem szereplő felhasználók férnek hozzá az Exchange Online-hoz és a SharePoint Online-hoz. Ellenőrizze, hogy a kizárás csoportban lévő felhasználók hozzáférhetnek-e a SharePoint Online-hoz és az Exchange-hez bármely eszközről.
3. 2\. házirend engedélyezése: Annak ellenőrzése, hogy a kizárási csoportba nem tartozó felhasználók nem tudnak-e hozzáférni a SharePoint Online-hoz és az Exchange Online-hoz a mobileszközökön. Ellenőrizze, hogy a kizárási csoportban lévő felhasználók hozzáférhetnek-e a SharePointhoz és az Exchange-hez bármely eszközről (Windows/iOS/Android).
4. 3\. szabályzat engedélyezése: Annak ellenőrzése, hogy a kizárási csoportba nem tartozó felhasználók nem férhetnek hozzá a SharePointhoz, és hogyan cserélhetik ki a vállalati hálózatot még tartományhoz csatlakoztatott géppel is. Ellenőrizze, hogy a kizárási csoportban lévő felhasználók hozzáférhetnek-e bármely hálózatról a SharePointhoz és az Exchange-hez.
5. 4\. szabályzat engedélyezése: Győződjön meg arról, hogy az összes felhasználó nem tudja lekérni az Exchange Online-t a mobileszközök natív levelezési alkalmazásaiból.
6. Tiltsa le a SharePoint Online és az Exchange Online meglévő MFA-szabályzatát.

Ebben a következő példában például a **B-készenléti hitelesítésszolgáltatói házirendek lehetővé teszik a Salesforce való mobil hozzáférést**, az üzleti alkalmazások hozzáférését visszaállítja a rendszer. Ebben az esetben az ügyfél általában ahhoz szükséges, hogy az értékesítési alkalmazottak hozzáférhessenek a mobileszközök Salesforce (az Azure AD-vel való egyszeri bejelentkezésre konfigurálva), és csak a megfelelő eszközökről legyenek engedélyezve. Ebben az esetben a megszakadási probléma az eszköz megfelelőségének kiértékelése, és a leállás olyan kényes időpontban történik, ahol az értékesítési csapatnak hozzá kell férnie a Salesforce a lezárt ajánlatokhoz. Ezek a készenléti szabályzatok kritikus fontosságú felhasználók számára biztosítanak hozzáférést a mobileszköz Salesforce, így továbbra is lezárhatók az ajánlatok, és nem zavarhatják a vállalatot. Ebben a példában a **SalesforceContingency** tartalmazza az összes olyan értékesítési alkalmazottat, akiknek meg kell őrizniük a hozzáférést, és a **SalesAdmins** a szükséges Salesforce-rendszergazdákat tartalmazza.

**B példa – készenléti HITELESÍTÉSSZOLGÁLTATÓI házirendek:**

* 1\. szabályzat: A SalesContingency-csapatban nem szereplő mindenki letiltása
  * Név: EM001 – ENGEDÉLYEZÉS VÉSZHELYZETBEN: Eszköz megfelelőségének megszakadása [1/2] – Salesforce – letiltja az összes felhasználót a SalesforceContingency kivételével
  * Felhasználók és csoportok: Minden felhasználó belefoglalása. SalesAdmins és SalesforceContingency kizárása
  * Felhőalapú alkalmazások: Salesforce.
  * Feltételek Nincsenek
  * Ellenőrzés engedélyezése: Letiltás
  * Állapot: Letiltva
* 2\. szabályzat: Az értékesítési csapat letiltása a mobiltól eltérő platformról (a támadási terület csökkentése érdekében)
  * Név: EM002 – ENGEDÉLYEZÉS VÉSZHELYZETBEN: Eszköz megfelelőségének megszakadása [2/2] – Salesforce – letiltja az összes platformot az iOS és az Android kivételével
  * Felhasználók és csoportok: SalesforceContingency belefoglalása. SalesAdmins kizárása
  * Felhőalapú alkalmazások: Salesforce
  * Feltételek Az eszköz platformja minden platformot tartalmaz, az iOS és az Android kizárása
  * Ellenőrzés engedélyezése: Letiltás
  * Állapot: Letiltva

Aktiválási sorrend:

1. Zárja ki a SalesAdmins és a SalesforceContingency a Salesforce meglévő megfelelőségi házirendjéből. Ellenőrizze, hogy a SalesforceContingency csoport felhasználója hozzáférhet-e a Salesforce.
2. 1\. szabályzat engedélyezése: A SalesContingency-en kívüli felhasználók ellenőrzése nem fér hozzá a Salesforce. Ellenőrizze, hogy a SalesAdmins és a SalesforceContingency felhasználók hozzáférhetnek-e a Salesforce.
3. 2\. házirend engedélyezése: Annak ellenőrzése, hogy a SalesContingency csoportban lévő felhasználók nem férhetnek hozzá a Salesforce a Windows/Mac rendszerű laptopokról, de továbbra is hozzáférhetnek a mobil eszközökről. Ellenőrizze, hogy a SalesAdmin továbbra is hozzáférhet-e a Salesforce bármely eszközről.
4. Tiltsa le a Salesforce meglévő eszköz megfelelőségi szabályzatát.

### <a name="deploy-password-hash-sync-even-if-you-are-federated-or-use-pass-through-authentication"></a>Jelszó-kivonatolási szinkronizálás telepítése akkor is, ha összevont vagy átmenő hitelesítést használ

A felhasználói zárolás akkor is történhet, ha a következő feltételek teljesülnek:

- A szervezet hibrid identitási megoldást használ átmenő hitelesítéssel vagy összevonással.
- A helyszíni identitás-rendszerek (például Active Directory, AD FS vagy egy függő összetevő) nem érhetők el. 
 
Ahhoz, hogy rugalmasabb legyen, a szervezetnek engedélyeznie kell a [jelszó](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn)-kivonatolási szinkronizálást, mivel lehetővé teszi a [jelszó-kivonatoló szinkronizálás használatát](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-user-signin) , ha a helyszíni identitási rendszerek le vannak állítva.

#### <a name="microsoft-recommendations"></a>Microsoft-javaslatok
 Engedélyezze a jelszó-kivonatok szinkronizálását a Azure AD Connect varázslóval, függetlenül attól, hogy a szervezete összevonási vagy átmenő hitelesítést használ-e.

>[!IMPORTANT]
> A jelszó-kivonatoló szinkronizálás használatához nem szükséges a felhasználókat összevontról felügyelt hitelesítésre konvertálni.

## <a name="during-a-disruption"></a>Megszakítás közben

Ha úgy döntött, hogy egy kockázatcsökkentő terv megvalósítását választotta, automatikusan megmaradhat az egyetlen hozzáférés-vezérlési megszakadási lehetőség. Ha azonban úgy döntött, hogy létrehoz egy készenléti tervet, a hozzáférés-vezérlés megszakadásakor aktiválhatja a készenléti szabályzatokat:

1. Olyan készenléti házirendeket engedélyezhet, amelyek meghatározott hálózatokból biztosítanak célzott felhasználókat, illetve hozzáférést adott alkalmazásokhoz.
2. Tiltsa le a normál vezérlő-alapú házirendeket.

### <a name="microsoft-recommendations"></a>Microsoft-javaslatok

Attól függően, hogy a rendszer milyen enyhítéseket vagy feltételeket használ a megszakadáskor, a szervezet csak jelszóval férhet hozzá. A védelem nem jelent jelentős biztonsági kockázatot, amelyet alaposan le kell mérni. A szervezeteknek a következőket kell tenniük:

1. A változás-ellenőrzési stratégia részeként dokumentálja az összes módosítást és az előző állapotot, hogy vissza tudja állítani az összes olyan előfizetési lehetőséget, amelyet azonnal végrehajtott, amint a hozzáférés-vezérlés teljesen működőképes.
2. Tegyük fel, hogy a rosszindulatú szereplők jelszó-vagy adathalászat-támadásokkal próbálnak begyűjteni jelszavakat az MFA letiltásakor. Emellett előfordulhat, hogy a rossz színészek már rendelkeznek olyan jelszavakkal, amelyek korábban nem adtak hozzáférést minden olyan erőforráshoz, amely ebben az ablakban próbálkozhat. Az olyan kritikus fontosságú felhasználók számára, mint a vezetők, részlegesen csökkenthetik ezt a kockázatot azáltal, hogy visszaállítják a jelszavukat az MFA letiltása előtt.
3. Az összes bejelentkezési tevékenység archiválása annak azonosításához, hogy ki férhet hozzá az MFA idejének letiltásához.
4. Az ablak [összes jelentett kockázati eseményének osztályozása](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) .

## <a name="after-a-disruption"></a>Megszakítás után

Vonja vissza az aktivált készenléti terv részeként elvégzett módosításokat, miután a szolgáltatás helyreállt, ami a megszakadást okozta. 

1. A normál házirendek engedélyezése
2. Tiltsa le a készenléti házirendeket. 
3. Visszaállíthatja a megszakítás során elvégzett és dokumentált egyéb módosításokat.
4. Ha vészhelyzeti hozzáférési fiókot használt, ne felejtse el újragenerált hitelesítő adatokat, és fizikailag biztonságossá tenni az új hitelesítő adatok részleteit a vészhelyzeti hozzáférési fiók eljárásainak részeként.
5. Folytassa a gyanús tevékenységek megszakítása után [jelentett összes kockázati esemény osztályozását](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) .
6. Vonja vissza a [PowerShell használatával](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0) kiadott összes frissítési jogkivonatot a felhasználók egy csoportjának célzásához. Az összes frissítési token visszavonása fontos a megszakadáskor használt privilegizált fiókok esetében, és ezzel kényszeríti őket a visszaállított szabályzatok ismételt hitelesítésére és ellenőrzésére.

## <a name="emergency-options"></a>Vészhelyzeti beállítások

 Vészhelyzet esetén, és a szervezet még nem hajtott végre kockázatcsökkentő vagy készenléti tervet, majd kövesse a [következő](#contingencies-for-user-lockout) témakörben ismertetett javaslatokat, ha már feltételes hozzáférési szabályzatokkal érvényesítik az MFA-t.
Ha a szervezet felhasználónkénti MFA örökölt házirendeket használ, akkor a következő alternatívát veheti figyelembe:

1. Ha rendelkezik a vállalati hálózat kimenő IP-címével, akkor azokat megbízható IP-címekként adhatja hozzá, hogy csak a vállalati hálózathoz engedélyezze a hitelesítést.
   1. Ha nem rendelkezik a kimenő IP-címek leltárával, vagy ha a hozzáférést a vállalati hálózaton belül és kívül is engedélyezni szeretné, a teljes IPv4-címtartományt felveheti megbízható IP-címként a 0.0.0.0/1 és a 128.0.0.0/1 érték megadásával.

>[!IMPORTANT]
 > Ha kibővíti a megbízható IP-címeket a hozzáférés feloldása érdekében, az IP-címekhez társított kockázati események (például a lehetetlen utazás vagy az ismeretlen helyek) nem jönnek létre.

>[!NOTE]
 > Az Azure MFA [megbízható IP](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-mfasettings) -címeinek konfigurálása csak [prémium szintű Azure ad licenccel](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-licensing)lehetséges.

## <a name="learn-more"></a>Tudnivalók a modellalapú alkalmazások létrehozásáról

* [Azure AD-hitelesítési dokumentáció](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-iis)
* [A válságkezelési-hozzáférési rendszergazdai fiókok kezelése az Azure ad-ben](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access)
* [Elnevezett helyszínek konfigurálása Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)
  * [Set-MsolDomainFederationSettings](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0)
* [Hibrid Azure Active Directory csatlakoztatott eszközök konfigurálása](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan)
* [Vállalati Windows Hello – Üzembehelyezési útmutató](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-guide)
  * [Jelszó-útmutató – Microsoft Research](https://research.microsoft.com/pubs/265143/microsoft_password_guidance.pdf)
* [Mik a feltételek a feltételes hozzáférés Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/conditions)
* [Mik azok a hozzáférés-vezérlések Azure Active Directory feltételes hozzáféréshez?](https://docs.microsoft.com/azure/active-directory/conditional-access/controls)
