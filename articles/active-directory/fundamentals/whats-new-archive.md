---
title: Újdonságok archívuma? – Az azure Active Directory |} A Microsoft Docs
description: A Mi az új kibocsátási megjegyzések az áttekintésben Ez a szakasz tartalmazza a tevékenység 6 hónap. 6 hónapra elemeket a fő cikkben eltávolítja és archív cikkben üzembe.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: lizross
ms.reviewer: dhanyahk
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: b338ad90c01c109cc9b2e222f1d7bcaa09f20346
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812949"
---
# <a name="archive-for-whats-new-in-azure-active-directory"></a>Újdonságok archívuma? az Azure Active Directoryban

Az elsődleges [Mi az új kibocsátási megjegyzések](whats-new.md) cikk tartalmazza a legújabb 6 hónapra visszamenőleg adatokat, amíg ez a cikk a régebbi adatokat tartalmaz.

A Mi új kibocsátási megjegyzések-információkat biztosít Önnek kapcsolatban:

- A legújabb kiadásaihoz.
- Ismert problémák
- Hibajavítások
- Elavult funkciók
- Módosítások tervek

---

## <a name="january-2019"></a>Január 2019

### <a name="active-directory-b2b-collaboration-using-one-time-passcode-authentication-public-preview"></a>Active Directory B2B-együttműködés egyszeri PIN-kódos hitelesítéssel (nyilvános előzetes verzió)

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** B2B  
**A termék képességei:** B2B/B2C

Bevezetünk egy egyszeri jelszavas hitelesítést (OTP) a B2B vendég felhasználói számára, akik nem hitelesíthetők más módon (például Azure AD, Microsoft-fiók (MSA) vagy Google Federation). Ez az új hitelesítési módszer azt jelenti, hogy a vendég felhasználóknak nem kell új Microsoft-fiók létrehozniuk. Ehelyett a meghívások beváltásakor vagy egy megosztott erőforrás elérésekor a vendég felhasználó egy ideiglenes kódot kérhet az e-mail-címre történő küldéshez. Ezt az ideiglenes kódot használva a vendég felhasználó továbbra is bejelentkezhet.

További információ: az [e-mailek egyszeri jelszavas hitelesítése (előzetes verzió)](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) és a blog, az [Azure ad lehetővé teszi a megosztást és együttműködést bármilyen fiókkal rendelkező felhasználó számára](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-makes-sharing-and-collaboration-seamless-for-any-user/ba-p/325949).

### <a name="new-azure-ad-application-proxy-cookie-settings"></a>Új Azure AD Application Proxy cookie-beállítások

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** App Proxy  
**A termék képességei:** Hozzáférés-vezérlés

Három új cookie-beállítást vezettünk be, amelyek elérhetők az Application proxyn keresztül közzétett alkalmazások számára:

- **Használjon csak HTTP-alapú cookie-t.** Beállítja a **HTTPOnly** jelzőt az alkalmazásproxy-hozzáférési és munkamenet-cookie-kra. A beállítás bekapcsolása további biztonsági előnyöket biztosít, például segít megakadályozni a cookie-k másolását vagy módosítását az ügyféloldali parancsfájlok használatával. Javasoljuk, hogy kapcsolja be ezt a jelzőt (válassza az **Igen**lehetőséget) a hozzáadott előnyökhöz.

- **Használjon biztonságos cookie-t.** Beállítja az alkalmazásproxy-hozzáférési és munkamenet-cookie-k **biztonságos** jelölőjét. A beállítás bekapcsolása további biztonsági előnyöket biztosít, mivel a cookie-k továbbítása csak TLS-alapú biztonságos csatornákon keresztül történik (például HTTPS). Javasoljuk, hogy kapcsolja be ezt a jelzőt (válassza az **Igen**lehetőséget) a hozzáadott előnyökhöz.

- **Használjon állandó cookie-t.** Megakadályozza, hogy a böngésző bezárása után a hozzáférés-cookie-k lejárnak. Ezek a cookie-k a hozzáférési jogkivonat élettartamának végéig tartanak. A cookie-k azonban alaphelyzetbe állnak, ha eléri a lejárati időt, vagy ha a felhasználó manuálisan törli a cookie-t. Azt javasoljuk, hogy tartsa meg azalapértelmezett beállítást, és csak olyan régebbi alkalmazások beállítását kapcsolja be, amelyek nem osztják meg a cookie-kat a folyamatok között.

További információ az új cookie-król: [cookie-beállítások](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings)a helyszíni alkalmazások eléréséhez Azure Active Directoryban.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2019"></a>Új összevont alkalmazások érhetők el az Azure AD alkalmazáskatalógusában – 2019. január

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**A termék képességei:** Külső integráció
 
Január 2019-én ezeket a 35 új alkalmazásokat az alkalmazás-katalógushoz való összevonási támogatással bővítettük:

[Firstbird](https://docs.microsoft.com/azure/active-directory/saas-apps/firstbird-tutorial), [Folloze](https://docs.microsoft.com/azure/active-directory/saas-apps/folloze-tutorial), [Talent paletta](https://docs.microsoft.com/azure/active-directory/saas-apps/talent-palette-tutorial), [infor CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloud-suite-tutorial), [Cisco Umbrella](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-umbrella-tutorial), [Zscaler internet-hozzáférés rendszergazdája](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-internet-access-administrator-tutorial), lejárati [emlékeztető](https://docs.microsoft.com/azure/active-directory/saas-apps/expiration-reminder-tutorial), [InstaVR Viewer](https://docs.microsoft.com/azure/active-directory/saas-apps/instavr-viewer-tutorial), [CorpTax](https://docs.microsoft.com/azure/active-directory/saas-apps/corptax-tutorial), [ Művelet](https://app.verb.net/login), [OpenLattice](https://openlattice.com/agora), [TheOrgWiki](https://www.theorgwiki.com/signup), [Pavaso digitális Bezárás](https://docs.microsoft.com/azure/active-directory/saas-apps/pavaso-digital-close-tutorial), [GoodPractice Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/goodpractice-toolkit-tutorial), [Cloud Service PICCO](https://docs.microsoft.com/azure/active-directory/saas-apps/cloud-service-picco-tutorial), [AuditBoard](https://docs.microsoft.com/azure/active-directory/saas-apps/auditboard-tutorial), [iProva](https://docs.microsoft.com/azure/active-directory/saas-apps/iprova-tutorial), [működőképes](https://docs.microsoft.com/azure/active-directory/saas-apps/workable-tutorial), [CallPlease ](https://webapp.callplease.com/create-account/create-account.html), [GTNexus SSO-rendszer](https://docs.microsoft.com/azure/active-directory/saas-apps/gtnexus-sso-module-tutorial), [CBRE ServiceInsight](https://docs.microsoft.com/azure/active-directory/saas-apps/cbre-serviceinsight-tutorial), [Deskradar](https://docs.microsoft.com/azure/active-directory/saas-apps/deskradar-tutorial), [Coralogixv](https://docs.microsoft.com/azure/active-directory/saas-apps/coralogix-tutorial), [Signagelive](https://docs.microsoft.com/azure/active-directory/saas-apps/signagelive-tutorial), [Ares for Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/ares-for-enterprise-tutorial), [K2 Office 365](https://www.k2.com/O365), [Xledger](https://www.xledger.net/), [iDiD Manager ](https://docs.microsoft.com/azure/active-directory/saas-apps/idid-manager-tutorial), [HighGear](https://docs.microsoft.com/azure/active-directory/saas-apps/highgear-tutorial), [látogatás](https://docs.microsoft.com/azure/active-directory/saas-apps/visitly-tutorial), [Korn Ferry Alp](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-alp-tutorial), [Acadia](https://docs.microsoft.com/azure/active-directory/saas-apps/acadia-tutorial), [Adoddle cSaas platform](https://docs.microsoft.com/azure/active-directory/saas-apps/adoddle-csaas-platform-tutorial)<!-- , [CaféX Portal (Meetings)](https://docs.microsoft.com/azure/active-directory/saas-apps/cafexportal-meetings-tutorial), [MazeMap Link](https://docs.microsoft.com/azure/active-directory/saas-apps/mazemaplink-tutorial)-->  

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS integrációja az Azure Active Directoryval](https://aka.ms/appstutorial). Az alkalmazás szerepeltetése az Azure AD-alkalmazásgyűjtemény ajánlati kapcsolatos további információkért lásd: [az alkalmazás szerepeltetése az Azure Active Directory alkalmazáskatalógusában](https://aka.ms/azureadapprequest).

---

### <a name="new-azure-ad-identity-protection-enhancements-public-preview"></a>Új Azure AD Identity Protection-funkciók (nyilvános előzetes verzió)

**Típusa** Megváltozott funkció  
**Szolgáltatás kategóriája:** Identitásvédelem  
**A termék képességei:** Identitásbiztonság és -védelem

Örömmel jelentjük be, hogy a következő fejlesztéseket adtuk hozzá a Azure AD Identity Protection nyilvános előzetes ajánlathoz, beleértve a következőket:

- Frissített és integráltabb felhasználói felület

- További API-k

- Továbbfejlesztett kockázatértékelés a gépi tanulás segítségével

- A kockázatos felhasználók és a kockázatos bejelentkezések teljes termékre kiterjedő összehangolása

További információ a fejlesztésekről: [Mi az Azure Active Directory Identity Protection (frissítve)?](https://aka.ms/IdentityProtectionDocs) Ha többet szeretne megtudni, és megoszthatja gondolatait a terméken belüli kérdésekkel.

---

### <a name="new-app-lock-feature-for-the-microsoft-authenticator-app-on-ios-and-android-devices"></a>Új alkalmazászárolási funkció az iOS-es és az androidos Microsoft Authenticator alkalmazásban

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Microsoft Authenticator alkalmazás  
**A termék képességei:** Identitásbiztonság és -védelem

Az egyszer használatos PIN-kódok, az alkalmazásadatok és az Alkalmazásbeállítások biztonságosabbá tételéhez bekapcsolhatja az alkalmazás zárolási funkcióját a Microsoft Authenticator alkalmazásban. Az alkalmazás zárolásának bekapcsolása azt jelenti, hogy minden alkalommal, amikor megnyitja a Microsoft Authenticator alkalmazást, a rendszer kérni fogja a PIN-kód vagy a biometrikus adatok hitelesítését.

További információ: [Microsoft Authenticator alkalmazás – gyakori kérdések](https://docs.microsoft.com/azure/active-directory/user-help/microsoft-authenticator-app-faq).

---

### <a name="enhanced-azure-ad-privileged-identity-management-pim-export-capabilities"></a>Továbbfejlesztett exportálási funkciók az Azure AD Privileged Identity Management (PIM) szolgáltatásban

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Privileged Identity Management  
**A termék képességei:** Privileged Identity Management

A Privileged Identity Management (PIM) rendszergazdák mostantól exportálják az összes aktív és jogosult szerepkör-hozzárendelést egy adott erőforráshoz, beleértve az összes alárendelt erőforráshoz tartozó szerepkör-hozzárendeléseket is. Korábban nehéz volt, hogy a rendszergazdák teljes listát kapjanak az előfizetéshez tartozó szerepkör-hozzárendelésekről, és minden egyes erőforráshoz hozzá kellett exportálni a szerepkör-hozzárendeléseket.

További információ: a [PIM-beli Azure-erőforrás szerepköreinek tevékenység-és naplózási előzményeinek megtekintése](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---

## <a name="novemberdecember-2018"></a>November/december 2018

### <a name="users-removed-from-synchronization-scope-no-longer-switch-to-cloud-only-accounts"></a>A szinkronizálási hatókörből eltávolított felhasználók már nem válthatnak felhőalapú fiókokra

**Típusa** Rögzített  
**Szolgáltatás kategóriája:** Felhasználókezelés  
**A termék képességei:** Címtár

>[!Important]
>A javítás miatt meghallottuk és megértettük a frusztrációt. Ezért ezt a módosítást csak addig a lépésig adtuk vissza, amíg a javítás könnyebben megvalósítható a szervezetében.

Kijavítottunk egy hibát, amelyben a felhasználó DirSyncEnabled jelölője helytelenül lett **hamis** értékre állítva, ha a Active Directory tartományi szolgáltatások (AD DS) objektumot kizárták a szinkronizációs hatókörből, majd áthelyezték a Lomtárba az Azure ad-ben az alábbiak szerint szinkronizálási ciklus. A javítás eredményeképpen, ha a felhasználó ki van zárva a szinkronizálási hatókörből, és ezt követően az Azure AD Lomtárból lett visszaállítva, a felhasználói fiók a várt módon szinkronizálva marad a helyszíni AD-ből, és nem kezelhető a felhőben, mivel a szolgáltatói forrás (SoA) továbbra is a következő marad: helyszíni AD.

A javítás előtt probléma merült fel, ha a DirSyncEnabled jelzőt hamis értékre váltották. Helytelen benyomást tett, hogy ezeket a fiókokat csak felhőalapú objektumokra konvertálták, és a fiókokat a felhőben lehet felügyelni. Azonban a fiókok továbbra is megtartották a SoA-t a helyszínen, és minden szinkronizált tulajdonságot (Shadow Attribute) a helyszíni AD-ből. Ez az állapot több problémát okozott az Azure AD-ben és más Felhőbeli számítási feladatokban (például az Exchange Online-ban), amely a fiókokat az Active Directory-ból szinkronizálva várta, de mostantól csak a csak felhőalapú fiókokkal működött.

Ebben az esetben az egyetlen módja, hogy valóban átalakítson egy szinkronizált-ból-AD-fiókot a csak felhőalapú fiókra, ha letiltja az rSync-t a bérlői szinten, ami egy háttérbeli műveletet indít el a SoA átviteléhez. Ilyen típusú SoA-változásra van szükség (de nem korlátozódik a) a helyszíni kapcsolódó attribútumok (például a LastDirSyncTime és az árnyék attribútumai) tisztítására, és más Felhőbeli számítási feladatokhoz is küld egy jelet, hogy a megfelelő objektuma csak felhőalapú fiókra legyen konvertálva. .

Ez a javítás ezért megakadályozza, hogy a közvetlen frissítések az AD-ből szinkronizált felhasználó ImmutableID attribútumán legyenek, ami a múltban bizonyos esetekben szükséges. A tervezés szerint az Azure AD-beli objektum ImmutableID, ahogy azt a név is jelenti, nem változtatható. Az Azure AD Connect Health és Azure AD Connect szinkronizálási ügyfél által megvalósított új szolgáltatások az ilyen forgatókönyvek kezeléséhez érhetők el:

- **Nagy léptékű ImmutableID-frissítés számos felhasználó számára a szakaszos megközelítésekben**
  
  Tegyük fel például, hogy hosszú AD DS erdők közötti áttelepítést kell végeznie. Megoldás A Azure AD Connect segítségével **konfigurálja a forrás** -horgonyt, és a felhasználó áttelepítésekor másolja át a meglévő ImmutableID-értékeket az Azure ad-ből a helyi AD DS felhasználó ms-DS-konzisztencia-GUID attribútumával az új erdőbe. További információ: [using ms-DS-ConsistencyGuid as sourceAnchor](/azure/active-directory/hybrid/plan-connect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor).

- **Nagy méretű ImmutableID-frissítések egy adott felvétel sok felhasználója számára**

  A Azure AD Connect megvalósítása során például hiba történt, és most meg kell változtatnia a SourceAnchor attribútumot. Megoldás Tiltsa le az rSync-et a bérlői szinten, és törölje az összes érvénytelen ImmutableID-értéket. További információ: [a címtár-szinkronizálás kikapcsolása az Office 365](/office365/enterprise/turn-off-directory-synchronization)-ben.

- **Helyi felhasználó megkeresése egy meglévő felhasználóval az Azure ad-ben** Például egy, a AD DSban újra létrehozott felhasználó létrehoz egy duplikált értéket az Azure AD-fiókban ahelyett, hogy egy meglévő Azure AD-fiókkal (árva objektummal) társítja. Megoldás Használja a Azure Portal Azure AD Connect Health a forrás-és ImmutableID újrafelhasználásához. További információ: [árva objektum forgatókönyve](/azure/active-directory/hybrid/how-to-connect-health-diagnose-sync-errors#orphaned-object-scenario).

### <a name="breaking-change-updates-to-the-audit-and-sign-in-logs-schema-through-azure-monitor"></a>Változás megszakítása: A naplózási és a bejelentkezési naplók sémájának frissítése Azure Monitor

**Típusa** Megváltozott funkció  
**Szolgáltatás kategóriája:** Jelentéskészítés  
**A termék képességei:** Monitorozás és jelentéskészítés

Jelenleg a naplózási és a bejelentkezési naplókat is közzéteszjük Azure Monitoron keresztül, így zökkenőmentesen integrálhatja a naplófájlokat a SIEM-eszközökkel vagy a Log Analyticsokkal. A visszajelzések alapján, és a szolgáltatás általánosan elérhetővé tételének előkészítésében a következő módosításokat végezjük el a sémán. Ezek a séma-változások és a kapcsolódó dokumentációs frissítések a januári első héten történnek.

#### <a name="new-fields-in-the-audit-schema"></a>Új mezők a naplózási sémában
Új **Művelettípus** mezőt adunk hozzá az erőforráson végrehajtott művelet típusának megadásához. Például: **Hozzáadás**, **frissítés**vagy **Törlés**.

#### <a name="changed-fields-in-the-audit-schema"></a>A naplózási séma módosított mezői
A következő mezők változnak a naplózási sémában:

|Mezőnév|Mi változott|Régi értékek|Új értékek|
|----------|------------|----------|----------|
|Category|Ez volt a **szolgáltatás neve** mező. Most már a **naplózási kategóriák** mező. A **szolgáltatás neve** átnevezve lett a **loggedByService** mezőre.|<ul><li>Fiók kiépítése</li><li>Alapvető könyvtár</li><li>Önkiszolgáló jelszó-visszaállítás</li></ul>|<ul><li>Felhasználókezelés</li><li>Csoportkezelés</li><li>Alkalmazáskezelés</li></ul>|
|targetResources|A legfelső szinten tartalmazza a **TargetResourceType** .|&nbsp;|<ul><li>Szabályzat</li><li>Alkalmazás</li><li>Felhasználó</li><li>Csoport</li></ul>|
|loggedByService|Annak a szolgáltatásnak a nevét adja meg, amely a naplót generálta.|Null|<ul><li>Fiók kiépítése</li><li>Alapvető könyvtár</li><li>Új jelszó önkiszolgáló kérése</li></ul>|
|Eredmény|A naplók eredményét adja meg. Korábban ez a számbavétel megtörtént, de most már megmutatjuk a tényleges értéket.|<ul><li>0</li><li>1</li></ul>|<ul><li>Siker</li><li>Hiba</li></ul>|

#### <a name="changed-fields-in-the-sign-in-schema"></a>Módosult mezők a bejelentkezési sémában
A bejelentkezési sémában a következő mezők változnak:

|Mezőnév|Mi változott|Régi értékek|Új értékek|
|----------|------------|----------|----------|
|appliedConditionalAccessPolicies|Ez volt a **conditionalaccessPolicies** mező. Most már a **appliedConditionalAccessPolicies** mező.|Nincs változás|Nincs változás|
|conditionalAccessStatus|A feltételes hozzáférési házirend állapotának eredményét adja meg a bejelentkezéskor. Korábban ez a számbavétel megtörtént, de most már megmutatjuk a tényleges értéket.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Siker</li><li>Hiba</li><li>Nem alkalmazott</li><li>Letiltva</li></ul>|
|appliedConditionalAccessPolicies: eredmény|A bejelentkezéskor az egyes feltételes hozzáférési szabályzatok állapotának eredményét adja meg. Korábban ez a számbavétel megtörtént, de most már megmutatjuk a tényleges értéket.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Siker</li><li>Hiba</li><li>Nem alkalmazott</li><li>Letiltva</li></ul>|

További információ a sémáról: [Az Azure ad-naplók sémájának értelmezése Azure monitor (előzetes verzió)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema)

---

### <a name="identity-protection-improvements-to-the-supervised-machine-learning-model-and-the-risk-score-engine"></a>A felügyelt gépi tanulási modell és a kockázatipontszám-motor új Identity Protectionfunkciókkal bővült

**Típusa** Megváltozott funkció  
**Szolgáltatás kategóriája:** Identitásvédelem  
**A termék képességei:** Kockázati pontszámok

Az Identity Protection szolgáltatással kapcsolatos felhasználói és bejelentkezési kockázatkezelési motor fejlesztései segíthetnek a felhasználók kockázati pontosságának és lefedettségének javításában. A rendszergazdák láthatják, hogy a felhasználói kockázati szint már nem kapcsolódik az adott észlelések kockázati szintjéhez, és a kockázatos bejelentkezési események száma és szintje is növekszik.

A kockázatos észleléseket a felügyelt gépi tanulási modell értékeli ki, amely a felhasználó bejelentkezési funkcióinak és észlelési mintázatának további funkcióival kiszámítja a felhasználói kockázatot. A modell alapján előfordulhat, hogy a rendszergazda magas kockázati pontszámokkal rendelkező felhasználókat talál, még akkor is, ha az adott felhasználóhoz tartozó észlelések alacsony vagy közepes kockázattal rendelkeznek. 

---

### <a name="administrators-can-reset-their-own-password-using-the-microsoft-authenticator-app-public-preview"></a>A rendszergazdák átállíthatják saját jelszavukat a Microsoft Authenticator alkalmazás használatával (nyilvános előzetes verzió)

**Típusa** Megváltozott funkció  
**Szolgáltatás kategóriája:** Önkiszolgáló jelszóátállítás  
**A termék képességei:** Felhasználók hitelesítése

Az Azure AD-rendszergazdák mostantól alaphelyzetbe állíthatják a saját jelszavukat a Microsoft Authenticator alkalmazás értesítésein vagy egy, a mobil hitelesítő alkalmazásból vagy a hardver-tokenből származó kóddal. A saját jelszavának alaphelyzetbe állításához a rendszergazdák mostantól a következő módszerek közül kettőt képesek használni:

- Microsoft Authenticator alkalmazás értesítése

- Egyéb mobil hitelesítő alkalmazás/hardver token kódja

- Email

- Telefonhívás

- Szöveges üzenet

További információ a jelszavak alaphelyzetbe állítására szolgáló Microsoft Authenticator alkalmazás használatáról: [Azure ad önkiszolgáló jelszó-visszaállítás – Mobile App és SSPR (előzetes verzió)](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#mobile-app-and-sspr-preview)

---

### <a name="new-azure-ad-cloud-device-administrator-role-public-preview"></a>Új szerepkör az Azure AD-ben: Felhőeszköz-rendszergazda (nyilvános előzetes verzió)

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Eszközregisztráció és -kezelés  
**A termék képességei:** Hozzáférés-vezérlés

A rendszergazdák a felhőalapú eszköz rendszergazdai feladatainak elvégzéséhez rendelhetnek felhasználókat az új felhőalapú eszköz rendszergazdai szerepkörhöz. A Cloud Device Administrators szerepkörrel rendelkező felhasználók engedélyezheti, letilthatja és törölheti az eszközöket az Azure AD-ben, valamint beolvashatja a Windows 10 BitLocker-kulcsokat (ha vannak) a Azure Portal.

További információ a szerepkörökről és az engedélyekről: [rendszergazdai szerepkörök hozzárendelésének Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

---

### <a name="manage-your-devices-using-the-new-activity-timestamp-in-azure-ad-public-preview"></a>Az eszközök új tevékenység-időbélyeg használatával való kezelése az Azure AD-ben (nyilvános előzetes verzió)

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Eszközregisztráció és -kezelés  
**A termék képességei:** Eszközéletciklus-kezelés

Tisztában vagyunk azzal, hogy idővel frissítenie kell és ki kell vonnia a szervezete eszközeit az Azure AD-ben, hogy elkerülje az elavult eszközöket a környezetben. Ennek a folyamatnak az elősegítése érdekében az Azure AD mostantól új tevékenység-időbélyeggel frissíti az eszközöket, és segít az eszközök életciklusának kezelésében.

További információ az időbélyegző [beszerzéséről és használatáról: How to: Az elavult eszközök kezelése az Azure AD-ben](https://docs.microsoft.com/azure/active-directory/devices/manage-stale-devices)

---

### <a name="administrators-can-require-users-to-accept-a-terms-of-use-on-each-device"></a>A rendszergazdák megkövetelhetik a felhasználóktól a használati feltételek elfogadását minden eszközön

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Használati feltételek  
**A termék képességei:** Cégirányítás
 
A rendszergazdák most bekapcsolhatják a **felhasználók megkövetelése minden eszközre** lehetőséget, hogy a felhasználók elfogadják a használati feltételeket a bérlőn használt összes eszközön.

További információ: a Azure Active Directory használati [feltételeinek használati feltételei](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#per-device-terms-of-use)című szakasza.

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-a-recurring-schedule"></a>A rendszergazdák megadhatják a használati feltételek érvényességét egy ismétlődő ütemterv alapján

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Használati feltételek  
**A termék képességei:** Cégirányítás
 

A rendszergazdák mostantól bekapcsolhatják a lejárati **hozzájárulási** lehetőséget, hogy a használati feltételek lejárnak az összes felhasználó számára a megadott ismétlődő ütemterv alapján. Az ütemterv lehet évente, kétévente, negyedévente vagy havonta. A használati feltételek lejárta után a felhasználóknak újra el kell fogadniuk.

További információ: a használati [feltételek hozzáadása szakasz, a Azure Active Directory használati feltételek funkció](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-each-users-schedule"></a>A rendszergazdák megadhatják a használati feltételek érvényességét az egyes felhasználók ütemterve alapján

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Használati feltételek  
**A termék képességei:** Cégirányítás

A rendszergazdák mostantól megadhatnak egy időtartamot, amelyet a felhasználónak újra el kell fogadnia a használati feltételeket. A rendszergazdák például megadhatják, hogy a felhasználóknak minden 90 naponként újra el kell fogadniuk a használati feltételeket.

További információ: a használati [feltételek hozzáadása szakasz, a Azure Active Directory használati feltételek funkció](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use).
 
---

### <a name="new-azure-ad-privileged-identity-management-pim-emails-for-azure-active-directory-roles"></a>Új, Azure Active Directory-szerepkörökkel kapcsolatos e-mailek az Azure AD Privileged Identity Management (PIM) szolgáltatásban

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Privileged Identity Management  
**A termék képességei:** Privileged Identity Management
 
A Azure AD Privileged Identity Managementt (PIM) használó ügyfelek mostantól heti kivonatoló e-mailt kaphatnak, beleértve az elmúlt hét nap alábbi adatait:

- A legfontosabb jogosult és állandó szerepkör-hozzárendelések áttekintése

- Szerepköröket aktiváló felhasználók száma

- A PIM-szerepkörökhöz hozzárendelt felhasználók száma

- A PIM-n kívüli szerepkörökhöz hozzárendelt felhasználók száma

- A PIM-ben "állandónak" kitett felhasználók száma

A PIM-vel és az elérhető e-mail-értesítésekkel kapcsolatos további információkért lásd: [e-mailes értesítések a PIM-ben](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-email-notifications).

---

### <a name="group-based-licensing-is-now-generally-available"></a>A csoportalapú licencelés már általánosan elérhető

**Típusa** Megváltozott funkció  
**Szolgáltatás kategóriája:** Egyéb  
**A termék képességei:** Címtár

A csoportos licencelés kívül esik a nyilvános előzetes verzióban, és már általánosan elérhető. Ennek az általános kiadásnak a részeként a szolgáltatás skálázható, és lehetővé tette a csoportos licencelési hozzárendelések újrafeldolgozását egyetlen felhasználó számára, valamint az Office 365 E3/a3 licenccel rendelkező csoportos licencelés használatát.

A csoport alapú licenceléssel kapcsolatos további információkért lásd: [Mi az a Azure Active Directory csoportos licencelés?](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2018"></a>Új összevont alkalmazások érhetők el az Azure AD alkalmazáskatalógusában – 2018. november

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**A termék képességei:** Külső integráció
 
November 2018-én a következő 26 új alkalmazást adtuk hozzá az alkalmazás-katalógusban az összevonási támogatással:

[CoreStack](https://cloud.corestack.io/site/login), [HubSpot](https://docs.microsoft.com/azure/active-directory/saas-apps/HubSpot-tutorial), [GetThere](https://docs.microsoft.com/azure/active-directory/saas-apps/getthere-tutorial), [GRA-PE](https://docs.microsoft.com/azure/active-directory/saas-apps/grape-tutorial), [eHour](https://getehour.com/try-now), [Consent2Go](https://docs.microsoft.com/azure/active-directory/saas-apps/Consent2Go-tutorial), [Appinux](https://docs.microsoft.com/azure/active-directory/saas-apps/appinux-tutorial), [DriveDollar](https://azuremarketplace.microsoft.com/marketplace/apps/savitas.drivedollar-azuread?tab=Overview), [Useall](https://docs.microsoft.com/azure/active-directory/saas-apps/useall-tutorial), [végtelen Campus](https://docs.microsoft.com/azure/active-directory/saas-apps/infinitecampus-tutorial), [Alaya](https://alayagood.com/en/demo/), [HeyBuddy](https://docs.microsoft.com/azure/active-directory/saas-apps/heybuddy-tutorial), [ Wrike SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/wrike-tutorial), [drift](https://docs.microsoft.com/azure/active-directory/saas-apps/drift-tutorial), [Zenegy for Business Central 365](https://accounting.zenegy.com/), [Everbridge tag portál](https://docs.microsoft.com/azure/active-directory/saas-apps/everbridge-tutorial), [ideo](https://profile.ideo.com/users/sign_up), [Ivanti Service Manager (ISM)](https://docs.microsoft.com/azure/active-directory/saas-apps/ivanti-service-manager-tutorial), [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-tutorial), [Allbound SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/allbound-sso-tutorial), [plex alkalmazások – klasszikus teszt ](https://test.plexonline.com/signon), [Plex-alkalmazások – klasszikus](https://www.plexonline.com/signon), [plex-alkalmazások – UX-teszt](https://test.cloud.plex.com/sso), [plex-alkalmazások – UX](https://cloud.plex.com/sso), [plex-alkalmazások – iam](https://accounts.plex.com/), [mesterségek – gyermekgondozási nyilvántartások, részvétel, & pénzügyi nyomkövető rendszer](https://getcrafts.ca/craftsregistration) 

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS integrációja az Azure Active Directoryval](https://aka.ms/appstutorial). Az alkalmazás szerepeltetése az Azure AD-alkalmazásgyűjtemény ajánlati kapcsolatos további információkért lásd: [az alkalmazás szerepeltetése az Azure Active Directory alkalmazáskatalógusában](https://aka.ms/azureadapprequest).

---

## <a name="october-2018"></a>2018. október

### <a name="azure-ad-logs-now-work-with-azure-log-analytics-public-preview"></a>Az Azure AD naplókat mostantól működik az Azure Log Analytics (nyilvános előzetes verzió)

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Jelentéskészítés  
**A termék képességei:** Monitorozás és jelentéskészítés

Örömmel jelentjük, hogy most már továbbítás az Azure AD-naplók az Azure Log Analytics! A várt funkció segítségével még jobb hozzáférést biztosíthat a elemzés az üzleti, műveletek, és a biztonsági, valamint az infrastruktúra figyelése a megoldást. További információkért lásd: a [Azure Active Directory-Tevékenységnaplók naplók az Azure Log Analytics mostantól elérhető](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-Activity-logs-in-Azure-Log-Analytics-now/ba-p/274843) blog.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2018"></a>Új összevont alkalmazások érhetők el az Azure AD-alkalmazásgyűjtemény – 2018. október

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**A termék képességei:** Külső integráció

2018 október tettünk elérhetővé az alkalmazáskatalógusban támogatja az összevonási 14 új alkalmazásokról:

[A díjjal járó pontokat](https://docs.microsoft.com/azure/active-directory/saas-apps/myawardpoints-tutorial), [Vibe HCM](https://docs.microsoft.com/azure/active-directory/saas-apps/vibehcm-tutorial), ambyint, [MyWorkDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/myworkdrive-tutorial), [BorrowBox](https://docs.microsoft.com/azure/active-directory/saas-apps/borrowbox-tutorial), Dialpad, [ON24 virtuális környezet](https://docs.microsoft.com/azure/active-directory/saas-apps/on24-tutorial), [RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-tutorial), [Zscaler három](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-three-tutorial), [Phraseanet](https://docs.microsoft.com/azure/active-directory/saas-apps/phraseanet-tutorial), [Appraisd](https://docs.microsoft.com/azure/active-directory/saas-apps/appraisd-tutorial), [Workspot vezérlő](https://docs.microsoft.com/azure/active-directory/saas-apps/workspotcontrol-tutorial), [Shuccho Navi](https://docs.microsoft.com/azure/active-directory/saas-apps/shucchonavi-tutorial), [Glassfrog](https://docs.microsoft.com/azure/active-directory/saas-apps/glassfrog-tutorial)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS integrációja az Azure Active Directoryval](https://aka.ms/appstutorial). Az alkalmazás szerepeltetése az Azure AD-alkalmazásgyűjtemény ajánlati kapcsolatos további információkért lásd: [az alkalmazás szerepeltetése az Azure Active Directory alkalmazáskatalógusában](https://aka.ms/azureadapprequest).

---

### <a name="azure-ad-domain-services-email-notifications"></a>Az Azure AD Domain Services E-mail-értesítések

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Azure AD Domain Services  
**A termék képességei:** Azure AD Domain Services

Az Azure AD Domain Services konfigurációs hibák vagy problémák a felügyelt tartományhoz az Azure Portalon riasztásokat biztosít. Ezek a riasztások tartalmazzák a részletes útmutatókat, így anélkül, hogy forduljon az ügyfélszolgálathoz a probléma javításához próbálkozhat.

Októbertől az lesz az értesítési beállításokat a felügyelt tartományhoz tartozó, amikor új kapcsolatos riasztások történnek, egy e-mailt küld egy kijelölt csoport tagjainak, így nem kell folyamatosan ellenőrzése a portálon a frissítéseket.

További információkért lásd: [értesítési beállítások az Azure AD tartományi szolgáltatásokban](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-notifications).

---

### <a name="azure-ad-portal-supports-using-the-forcedelete-domain-api-to-delete-custom-domains"></a>Az Azure AD portal támogatja a ForceDelete tartomány API használatával törli az egyéni tartományok 

**Típusa** Megváltozott funkció  
**Szolgáltatás kategóriája:** Címtárkezelés  
**A termék képességei:** Címtár

Örömmel jelentjük, hogy mostantól használhatja a ForceDelete tartomány API aszinkron módon átnevezésével, mint például a felhasználók, csoportok és alkalmazások az egyéni tartománynevet (contoso.com) a biztonsági mutató hivatkozásokat a kezdeti alapértelmezett tartomány nevét (az egyéni tartománynevek törlése contoso.onmicrosoft.com).

Ez a változás segítségével gyorsabban törölni az egyéni tartománynevek, ha a szervezet már nem használja a nevét, vagy ha szeretné használni a tartománynév egy másik Azure AD-val.

További információkért lásd: [egy egyéni tartománynév törlése](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-manage#delete-a-custom-domain-name).

---

## <a name="september-2018"></a>2018. szeptember
 
### <a name="updated-administrator-role-permissions-for-dynamic-groups"></a>A dinamikus csoportok esetében frissültek a rendszergazdai szerepkör engedélyei

**Típusa** Rögzített  
**Szolgáltatás kategóriája:** Csoportkezelés  
**A termék képességei:** Együttműködés

Azt már megtörtént egy probléma javítása, adott rendszergazdai szerepkörök mostantól létrehozhat és frissíthet a dinamikus tagsági szabályok, anélkül, hogy a csoport tulajdonosa legyen.

A szerepkörök a következők:

- Globális rendszergazda

- Intune-rendszergazda

- Felhasználói adminisztrátor

További információkért lásd: [állapotának ellenőrzése és a egy dinamikus csoport létrehozása](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule)

---

### <a name="simplified-single-sign-on-sso-configuration-settings-for-some-third-party-apps"></a>Néhány külső gyártótól származó alkalmazás esetében egyszerűsödött az egyszeri bejelentkezés (SSO) konfigurálása

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**A termék képességei:** SSO

Tisztában vagyunk vele, hogy állítja be egyszeri bejelentkezést (SSO) szoftverfrissítési szolgáltatásként (SaaS) alkalmazások kihívást jelenthet az egyes alkalmazások konfiguráció egyedi jellege miatt. Elkésztettünk egy leegyszerűsített konfigurációs élményt biztosít az egyszeri bejelentkezés beállításai a következő külső SaaS-alkalmazások automatikus feltöltéséhez:

- Zendesk

- Az ArcGis Online

- A Jamf Pro

Az egykattintásos felület használatának megkezdéséhez nyissa meg a **az Azure portal** > **egyszeri bejelentkezési konfiguráció** az alkalmazás lapját. További információkért lásd: [SaaS integrációja az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)

---

### <a name="azure-active-directory---where-is-your-data-located-page"></a>Az Azure Active Directory - hol található a adatait? lap

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Egyéb  
**A termék képességei:** GoLocal

Válassza ki a régiót a vállalat a **Azure Active Directory - hol található a adatait** oldal nézet, mely Azure-beli adatközpont-környezetet tároló az Azure AD-szolgáltatásokhoz az Azure AD inaktív adatokat. Specifikus szerint szűrheti az adatokat az Azure AD-szolgáltatások számára, a vállalat.

Ez a funkció eléréséhez, és további információkért lásd: [Azure Active Directory - hol található a adatait](https://aka.ms/AADDataMap).

---

### <a name="new-deployment-plan-available-for-the-my-apps-access-panel"></a>Új üzembe helyezési terv érhető el a Saját alkalmazások hozzáférési panelen

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Saját alkalmazások  
**A termék képességei:** SSO

Tekintse meg az új központi telepítési csomag, amely a saját alkalmazások hozzáférési panelen érhető el (https://aka.ms/deploymentplans).
A saját alkalmazások hozzáférési panel itt rendelkező felhasználók számára egyetlen helyen megtalálhatja és elérheti a saját alkalmazásokban. Ezen a portálon a felhasználók önkiszolgáló lehetőségeket biztosít, például alkalmazások és a csoportok hozzáférést kér vagy erőforrásokhoz való hozzáférés kezelését ezek mások nevében is biztosít.

További információkért lásd: [Mi az a saját alkalmazások portál?](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)

---

### <a name="new-troubleshooting-and-support-tab-on-the-sign-ins-logs-page-of-the-azure-portal"></a>Új lappal (Hibaelhárítás és támogatás) bővült az Azure Portal Bejelentkezések weblapja

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Jelentéskészítés  
**A termék képességei:** Monitorozás és jelentéskészítés

Az új **hibaelhárítás és támogatás** lapján a **bejelentkezések** oldal az Azure portal, ismerteti, hogy a rendszergazdák és a támogatási szakértők az Azure AD bejelentkezési kapcsolatos problémák elhárítása. Ezen a lapon biztosítja a hibakódot, hibaüzenet jelenik meg, és korrigálási javaslatokat tesz (ha van ilyen) a probléma megoldásához. Ha nem sikerül a probléma megoldásához, azt is meg kell adni, hozzon létre egy támogatási jegyet az új módon a **példányt vágólapra** észlel, amely feltölti a **Kérelemazonosító** és **dátuma (UTC)** meg a naplófájlt a támogatási jegy mezőket.  

![Az új lapot megjelenítő bejelentkezési naplók](media/whats-new/troubleshooting-and-support.png)

---

### <a name="enhanced-support-for-custom-extension-properties-used-to-create-dynamic-membership-rules"></a>A dinamikus tagsági szabályok létrehozására szolgáló egyénibővítmény-tulajdonságok továbbfejlesztett támogatása

**Típusa** Megváltozott funkció  
**Szolgáltatás kategóriája:** Csoportkezelés  
**A termék képességei:** Együttműködés

Ez a frissítés most kattintson a **egyéni bővítménytulajdonság lekérése** a dinamikus felhasználói csoport szabály builder a hivatkozásra, adja meg az alkalmazás egyedi azonosítója, és kap egy dinamikus létrehozásakor egyéni bővítménytulajdonságok teljes listája Tagsági szabály a felhasználók számára. Ez a lista is frissíthetők úgy, hogy minden olyan új egyéni bővítmény tulajdonságainak lekérése az adott alkalmazáshoz.

A dinamikus tagsági szabályok egyéni bővítménytulajdonságok használatával kapcsolatos további információkért lásd: [bővítmény és egyéni bővítmény tulajdonságai](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership#extension-properties-and-custom-extension-properties)

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Új jóváhagyott ügyfélalkalmazások az Azure AD alkalmazás-alapú feltételes hozzáféréshez

**Típusa** Tervezett módosítás  
**Szolgáltatás kategóriája:** Feltételes hozzáférés  
**A termék képességei:** Identitás biztonsága és védelme

A következő alkalmazások olyan listájában [jóváhagyott ügyfélalkalmazások](https://docs.microsoft.com/azure/active-directory/conditional-access/technical-reference#approved-client-app-requirement):

- Microsoft To-Do

- Microsoft Stream

További információkért lásd:

- [Azure AD-alkalmazás-alapú feltételes hozzáférés](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="new-support-for-self-service-password-reset-from-the-windows-7881-lock-screen"></a>Már támogatott a Windows 7/8/8.1 zárolási képernyőjéről történő önkiszolgáló jelszómódosítás

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** SSPR  
**A termék képességei:** Felhasználók hitelesítése

Miután beállította az új funkciót, a felhasználók a jelszavuk egy hivatkozás jelenik-e a **zárolási** egy Windows 7, Windows 8 vagy Windows 8.1 rendszerű eszköz képernyőjén. A hivatkozásra kattintva a felhasználó rendszer végigvezeti a azonos jelszóvisszaállítási folyamatot a webböngészőn keresztül.

További információkért lásd: [a Windows 7, 8 és 8.1 jelszóátállítás engedélyezése](https://aka.ms/ssprforwindows78)

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Változási Megjegyzés: Az engedélyezési kódok többé nem lesznek elérhetők újra 

**Típusa** Tervezett módosítás  
**Szolgáltatás kategóriája:** Hitelesítések (Bejelentkezések)  
**A termék képességei:** Felhasználók hitelesítése

2018. November 15., kezdve az Azure AD leáll, az alkalmazások korábban használt hitelesítési kódok elfogadásával. Ez a változás segítséget nyújt ahhoz, hogy az Azure AD az OAuth-specifikációnak megfelelően, és alkalmazza a v1 és v2 végpontokon.

Ha az alkalmazás újból felhasználja a jogkivonatok lekérésére, több erőforrás-engedélyezési kódokat, javasoljuk, hogy a kód használatával egy frissítési jogkivonat lekérése, és a frissítési jogkivonat használatával más erőforrások kiegészítő jogkivonatok beszerzéséhez. Engedélyezési kód csak egyszer használhatók fel, de frissítési biztonsági jogkivonat használható többször több erőforrást. Egy alkalmazás, amely megpróbálja újból felhasználhatja a hitelesítési kódot az OAuth hitelesítésikód-folyamata során invalid_grant hiba lép fel.

Ez és egyéb protokollok-vel kapcsolatos módosításokat, lásd: [teljes listája megtalálható az új hitelesítési](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2018"></a>Új összevont alkalmazások érhetők el az Azure AD alkalmazáskatalógusában – 2018. szeptember

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**A termék képességei:** Külső integráció
 
2018 szeptember tettünk elérhetővé az alkalmazáskatalógusban támogatja az összevonási 16 új alkalmazásokról:

[Uberflip](https://docs.microsoft.com/azure/active-directory/saas-apps/uberflip-tutorial), [Comeet szoftver felvételi](https://docs.microsoft.com/azure/active-directory/saas-apps/comeetrecruitingsoftware-tutorial), [Workteam](https://docs.microsoft.com/azure/active-directory/saas-apps/workteam-tutorial), [ArcGIS vállalati](https://docs.microsoft.com/azure/active-directory/saas-apps/arcgisenterprise-tutorial), [Nuclino](https://docs.microsoft.com/azure/active-directory/saas-apps/nuclino-tutorial), [ JDA felhőalapú](https://docs.microsoft.com/azure/active-directory/saas-apps/jdacloud-tutorial), [Snowflake](https://docs.microsoft.com/azure/active-directory/saas-apps/snowflake-tutorial), NavigoCloud, [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-tutorial), join.me, [ZephyrSSO](https://docs.microsoft.com/azure/active-directory/saas-apps/zephyrsso-tutorial), [Silverback](https://docs.microsoft.com/azure/active-directory/saas-apps/silverback-tutorial), Riverbed Xirrus EasyPass, [Rackspace SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/rackspacesso-tutorial), Enlyft egyszeri bejelentkezés az Azure-hoz, használható surveymonkey szolgáltatást [Convene](https://docs.microsoft.com/azure/active-directory/saas-apps/convene-tutorial), [dmarcian](https://docs.microsoft.com/azure/active-directory/saas-apps/dmarcian-tutorial)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS integrációja az Azure Active Directoryval](https://aka.ms/appstutorial). Az alkalmazás szerepeltetése az Azure AD-alkalmazásgyűjtemény ajánlati kapcsolatos további információkért lásd: [az alkalmazás szerepeltetése az Azure Active Directory alkalmazáskatalógusában](https://aka.ms/azureadapprequest).

---

### <a name="support-for-additional-claims-transformations-methods"></a>Bővült a támogatott jogcím-átalakítási módszerek köre

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**A termék képességei:** SSO

Új jogcím átalakítása módszer ToLower() és ToUpper(), amely is lehet alkalmazni a SAML-jogkivonatok az SAML-alapú vezettünk **egyszeri bejelentkezési konfigurációjának** lapot.

További információkért lásd: [az Azure ad-ben a vállalati alkalmazásokhoz SAML-jogkivonatban kiadott jogcímek testreszabása](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

---

### <a name="updated-saml-based-app-configuration-ui-preview"></a>Frissült az SAML-alapú alkalmazáskonfigurációs kezelőfelület (előzetes verzió)

**Típusa** Megváltozott funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**A termék képességei:** SSO

A frissített SAML-alapú alkalmazás-konfigurációs felhasználói Felületet részeként láthatja:

- Egy frissített útmutató felületet, az SAML-alapú alkalmazások konfigurálásához.

- Mi az a hiányzó vagy helytelen a konfiguráció a kapcsolatos láthatóságot.

- Lehetővé teszi, hogy az e-mail címeket lejárati tanúsítványt az értesítésre.

- Új jogcím-átalakítási módszerek, ToLower() ToUpper() és még.

- Töltse fel a saját jogkivonat-aláíró tanúsítvány a vállalati alkalmazások lehetőséget.

- Is be lehet állítani az alkalmazások SAML NameID-formátum, és a NameID értéket állítja be Címtárbővítmények módja.

A frissített nézet bekapcsolásához kattintson a **próbálja ki az új funkciót** hivatkozás tetején a **egyszeri bejelentkezés** lap. További információ [: oktatóanyag: SAML-alapú egyszeri bejelentkezés konfigurálása Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-portal)-vel rendelkező alkalmazásokhoz.

---

## <a name="august-2018"></a>2018. augusztus

### <a name="changes-to-azure-active-directory-ip-address-ranges"></a>Módosultak az Azure Active Directory-beli IP-címtartományok

**Típusa** Tervezett módosítás  
**Szolgáltatás kategóriája:** Egyéb  
**A termék képességei:** Platform

Nagyobb IP-címtartományok vezetünk be az Azure ad Szolgáltatásba, ami azt jelenti, ha konfigurálta az Azure AD IP-címtartományok esetében a tűzfalak, útválasztók és hálózati biztonsági csoportok kell azokat. Így nem kell a tűzfalat, útválasztó vagy a hálózati biztonsági csoportok IP-címtartomány konfigurációk újra módosítása esetén az Azure AD új végpontjait hozzáadja ezt a frissítést végzünk. 

Hálózati forgalom ezen új tartományok áthelyezése a következő két hónapban. Szolgáltatás megszakításmentes folytatásához, hozzá kell adnia a frissített értékeket az IP-címek 2018. szeptember 10. előtt:

- 20.190.128.0/18 

- 40.126.0.0/18 

Javasoljuk, hogy nem távolítja a régi IP-címtartományokat, mindaddig, amíg az összes, a hálózati forgalom át lett helyezve az új tartományokat. Frissítések információt, és ismerje meg, amikor eltávolíthatja a régi tartományokat, lásd: [Office 365 URL-címei és IP-címtartományok](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Változási Megjegyzés: Az engedélyezési kódok többé nem lesznek elérhetők újra 

**Típusa** Tervezett módosítás  
**Szolgáltatás kategóriája:** Hitelesítések (Bejelentkezések)  
**A termék képességei:** Felhasználók hitelesítése

2018. November 15., kezdve az Azure AD leáll, az alkalmazások korábban használt hitelesítési kódok elfogadásával. Ez a változás segítséget nyújt ahhoz, hogy az Azure AD az OAuth-specifikációnak megfelelően, és alkalmazza a v1 és v2 végpontokon.

Ha az alkalmazás újból felhasználja a jogkivonatok lekérésére, több erőforrás-engedélyezési kódokat, javasoljuk, hogy a kód használatával egy frissítési jogkivonat lekérése, és a frissítési jogkivonat használatával más erőforrások kiegészítő jogkivonatok beszerzéséhez. Engedélyezési kód csak egyszer használhatók fel, de frissítési biztonsági jogkivonat használható többször több erőforrást. Egy alkalmazás, amely megpróbálja újból felhasználhatja a hitelesítési kódot az OAuth hitelesítésikód-folyamata során invalid_grant hiba lép fel.

Ez és egyéb protokollok-vel kapcsolatos módosításokat, lásd: [teljes listája megtalálható az új hitelesítési](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes).
 
---

### <a name="converged-security-info-management-for-self-service-password-sspr-and-multi-factor-authentication-mfa"></a>Önkiszolgáló jelszóregisztráció (SSPR) és többtényezős hitelesítés (MFA) – összevont biztonságiadat-kezelés

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** SSPR  
**A termék képességei:** Felhasználók hitelesítése
 
Ez az új funkció lehetővé teszi a biztonsági adataikat (mint például a telefonszám, mobilalkalmazás és így tovább) kezelheti az SSPR és a egy egyetlen helyen, és felület; MFA képest a korábban, ahol tette két különböző helyen.

SSPR vagy MFA használatával személyek is használható az átszervezett felületet. Ezenkívül ha a szervezet nem kényszeríti a többtényezős hitelesítés vagy az SSPR regisztrációt, személyek továbbra is regisztrálhatja bármilyen MFA vagy az SSPR biztonsági adatai módszer engedélyezett a munkahelyen a saját alkalmazások portálról.

Egy választható nyilvános előzetes kiadásról. A rendszergazdák bekapcsolhatja az új felhasználói felület (ha szükséges) a kijelölt csoport vagy a bérlő összes felhasználója esetében. Konvergens funkciókkal kapcsolatos további információkért lásd: a [Konvergens élmény blog](https://cloudblogs.microsoft.com/enterprisemobility/2018/08/06/mfa-and-sspr-updates-now-in-public-preview/)

---

### <a name="new-http-only-cookies-setting-in-azure-ad-application-proxy-apps"></a>Új (csak a HTTP használatát támogató) cookie-beállítás az Azure AD Application Proxy-alkalmazásokban

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** App Proxy  
**A termék képességei:** Hozzáférés-vezérlés

Van egy új nevű beállítása, **HTTP-Only cookie-k** az alkalmazásproxy-alkalmazásokban. Ez a beállítás biztosítja, beleértve a HTTP-válaszfejléc mindkét alkalmazásproxy hozzáférési és munkamenet-cookie-khoz a HTTPOnly jelző, hozzáférés leállítása a cookie-val ügyféloldali parancsfájl és további megakadályozza a műveleteket, például a Másolás adja meg a további biztonsági vagy a cookie-k módosítását. Bár ez a jelző korábban még nem használt, a cookie-kat mindig lettek titkosítva, és nem megfelelő módosításokat elleni védelem érdekében az SSL-kapcsolat használatával.

Ez a beállítás nem kompatibilis az alkalmazások az ActiveX-vezérlők, például a távoli asztal használatával. Ha ebben a helyzetben, azt javasoljuk, hogy kapcsolja ki ezt a beállítást.

A HTTP-Only cookie-kra vonatkozó beállítással kapcsolatos további információkért lásd: [alkalmazások közzététele az Azure AD-alkalmazásproxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-publish-azure-portal).

---

### <a name="privileged-identity-management-pim-for-azure-resources-supports-management-group-resource-types"></a>A Privileged Identity Management (PIM) az Azure-erőforrások esetében támogatja a felügyeleti csoport típusú erőforrásokat

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Privileged Identity Management  
**A termék képességei:** Privileged Identity Management
 
Just-In-Time aktiválási és hozzárendelési beállítások is érvényesek a felügyeleti csoport erőforrástípusok, ugyanúgy, mint az előfizetések, erőforráscsoportok és erőforrások (például virtuális gépek, alkalmazásszolgáltatások, és további) már nincs. Ezenkívül bárki egy szerepkör, amely rendszergazdai hozzáférést biztosít a felügyeleti csoport felderítése és kezelése a PIM erőforrás.

A PIM és az Azure-erőforrásokkal kapcsolatos további információkért lásd: [felderítése és az Azure-erőforrások kezelése a Privileged Identity Management használatával](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-resource-roles-discover-resources)
 
---

### <a name="application-access-preview-provides-faster-access-to-the-azure-ad-portal"></a>Az alkalmazás-hozzáférés (előzetes verzió) gyorsabb hozzáférést biztosít az Azure AD-portálhoz.

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Privileged Identity Management  
**A termék képességei:** Privileged Identity Management
 
Jelenleg a PIM használata szerepkör aktiválásakor is igénybe vehet az engedélyek érvénybe több mint 10 perc. Ha úgy dönt, hogy az alkalmazás-hozzáférést, amely jelenleg nyilvános előzetes verzióban érhető el, használja a rendszergazdák hozzáférhetnek az Azure AD portálra, amint az aktiválási kérelem befejeződött.

Jelenleg alkalmazás elérése csak támogatja az Azure AD-portál felületének és az Azure-erőforrások. A PIM és az alkalmazások kapcsolatos további információkért lásd: [Mi az Azure AD Privileged Identity Management?](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure)
 
---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2018"></a>Új összevont alkalmazások érhetők el az Azure AD alkalmazáskatalógusában – 2018. augusztus

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**A termék képességei:** Külső integráció
 
2018 augusztus tettünk elérhetővé az alkalmazáskatalógusban támogatja az összevonási 16 új alkalmazásokról:

[Szarvascsőrűmadár](https://docs.microsoft.com/azure/active-directory/saas-apps/hornbill-tutorial), [kötetlen Bridgeline](https://docs.microsoft.com/azure/active-directory/saas-apps/bridgelineunbound-tutorial), [mártás Labs - mobil- és webes tesztelés](https://docs.microsoft.com/azure/active-directory/saas-apps/saucelabs-mobileandwebtesting-tutorial), [Meta-összekötő hálózatok](https://docs.microsoft.com/azure/active-directory/saas-apps/metanetworksconnector-tutorial), [úgy tesszük](https://docs.microsoft.com/azure/active-directory/saas-apps/waywedo-tutorial), [Spotinst](https://docs.microsoft.com/azure/active-directory/saas-apps/spotinst-tutorial), [ProMaster (által Inlogik)](https://docs.microsoft.com/azure/active-directory/saas-apps/promaster-tutorial), SchoolBooking, [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-tutorial), [dokumentáció](https://docs.microsoft.com/azure/active-directory/saas-apps/DOSSIER-tutorial), [N2F - kiadás jelentések](https://docs.microsoft.com/azure/active-directory/saas-apps/n2f-expensereports-tutorial), [Comm100 élő csevegés](https://docs.microsoft.com/azure/active-directory/saas-apps/comm100livechat-tutorial), [SafeConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/safeconnect-tutorial), [ZenQMS](https://docs.microsoft.com/azure/active-directory/saas-apps/zenqms-tutorial), [eLuminate](https://docs.microsoft.com/azure/active-directory/saas-apps/eluminate-tutorial), [ Dovetale](https://docs.microsoft.com/azure/active-directory/saas-apps/dovetale-tutorial).

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS integrációja az Azure Active Directoryval](https://aka.ms/appstutorial). Az alkalmazás szerepeltetése az Azure AD-alkalmazásgyűjtemény ajánlati kapcsolatos további információkért lásd: [az alkalmazás szerepeltetése az Azure Active Directory alkalmazáskatalógusában](https://aka.ms/azureadapprequest).

---

### <a name="native-tableau-support-is-now-available-in-azure-ad-application-proxy"></a>A natív Tableau-támogatás már elérhető az Azure AD Application Proxyban

**Típusa** Megváltozott funkció  
**Szolgáltatás kategóriája:** App Proxy  
**A termék képességei:** Hozzáférés-vezérlés

A frissítés az OpenID Connect, az üzem előtti hitelesítési protokoll az OAuth 2.0-s Kódmegadás protokoll már nem kell az alkalmazásproxy használatával a Tableau használandó további konfigurációt elvégezni. Ez a protokoll változás is segít alkalmazásproxy hatékonyabban támogatják a több modern alkalmazások csak a HTTP átirányítást, gyakran támogatott JavaScript és HTML-címkék használatával.

A natív módon támogatja a Tableau kapcsolatos további információkért lásd: [mostantól natív Tableau támogatásával az Azure AD-alkalmazásproxy](https://blogs.technet.microsoft.com/applicationproxyblog/2018/08/14/azure-ad-application-proxy-now-with-native-tableau-support).

---

### <a name="new-support-to-add-google-as-an-identity-provider-for-b2b-guest-users-in-azure-active-directory-preview"></a>Már támogatott a Google identitásszolgáltatóként való beállítása a vállalatközi vendégfelhasználók számára (előzetes verzió)

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** B2B  
**A termék képességei:** B2B/B2C

A szervezet a Google összevonási beállításával engedélyezheti, meghívott Gmail felhasználók bejelentkezési a megosztott alkalmazások és erőforrások a meglévő Google-fiók létrehozása a személyes Microsoft-Account (msa-k) vagy egy Azure AD-fiók nélkül.

Egy választható nyilvános előzetes kiadásról. Google összevonási kapcsolatos további információkért lásd: [Identitásszolgáltatóként B2B vendégfelhasználó hozzáadása Google](https://docs.microsoft.com/azure/active-directory/b2b/google-federation).

---

## <a name="july-2018"></a>2018. július

### <a name="improvements-to-azure-active-directory-email-notifications"></a>Az Azure Active Directory értesítő e-mailjeinek fejlesztései

**Típusa** Megváltozott funkció  
**Szolgáltatás kategóriája:** Egyéb  
**A termék képességei:** Identitás-életciklus kezelése
 
Az Azure Active Directory (Azure AD) e-mailek most szolgáltatás egy új megjelenés, valamint a módosítások a feladó e-mail címe és a feladó megjelenített neve, amikor a következő szolgáltatások által küldött:
 
- Az Azure AD hozzáférési felülvizsgálatok
- Azure AD Connect Health 
- Azure AD Identity Protection 
- Azure AD Privileged Identity Management
- Vállalati alkalmazás lejáró Tanúsítványértesítéseket
- Vállalati alkalmazás-kiépítés szolgáltatási értesítések
 
Az e-mail-értesítések, a következő e-mail címre küldi, és a megjelenített neve:

- E-mail-cím: azure-noreply@microsoft.com
- Megjelenített név: Microsoft Azure
 
Egy vonatkozó példáért egyes új e-mail mintákra és további információkat lásd: [E-mail-értesítések az Azure AD PIM-ben](https://go.microsoft.com/fwlink/?linkid=2005832).

---

### <a name="azure-ad-activity-logs-are-now-available-through-azure-monitor"></a>Az Azure AD tevékenységnaplói most már elérhetők az Azure Monitorban

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Jelentéskészítés  
**A termék képességei:** Monitorozás és jelentéskészítés

Az Azure AD-Tevékenységnaplók mostantól elérhetők az Azure monitor (az Azure platform kiterjedő figyelési szolgáltatás) nyilvános előzetes verzióban érhető el. Az Azure Monitor kínál hosszú távú adatmegőrzés és zökkenőmentes integráció mellett ezek a fejlesztések:

- Hosszú távú megőrzése a naplófájlok irányítva a saját Azure storage-fiókba.

- Zökkenőmentes SIEM-integráció, nem kell írnia, vagy egyéni parancsfájlok karbantartása.

- Zökkenőmentes integráció a saját egyéni megoldások, elemzési eszközök vagy az incidenskezelés megoldásokat.

Ezekkel az új képességekkel kapcsolatos további információkért lásd: blogunkat [az Azure AD-Tevékenységnaplók a diagnosztika jelenleg nyilvános előzetes verzióban elérhető az Azure Monitor](https://cloudblogs.microsoft.com/enterprisemobility/2018/07/26/azure-ad-activity-logs-in-azure-monitor-diagnostics-now-in-public-preview/) és a dokumentációban, [Azure Active Directory-Tevékenységnaplók az Azure-ban A figyelő (előzetes verzió)](https://docs.microsoft.com/azure/active-directory/reporting-azure-monitor-diagnostics-overview).

---

### <a name="conditional-access-information-added-to-the-azure-ad-sign-ins-report"></a>Az Azure AD bejelentkezési jelentéshez hozzáadott feltételes hozzáférési információk

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Jelentéskészítés  
**A termék képességei:** Identitásbiztonság és -védelem
 
Ez a frissítés lehetővé teszi, hogy láthatja, hogy mely házirendek kiértékelése a felhasználó bejelentkezésekor mellett szabályzat eredménye. A jelentés emellett mostantól tartalmazza a felhasználó által használt, így azonosíthatja a régi protokoll forgalom ügyfélalkalmazás típusa. Jelentés bejegyzések most is kereshető a korrelációs Azonosítót, amely tekintheti meg a felhasználók felé néző hibaüzenet és azonosítása és megoldása a megfelelő bejelentkezési kérelem használható.

---

### <a name="view-legacy-authentications-through-sign-ins-activity-logs"></a>A régi hitelesítések megtekintése a bejelentkezések tevékenységnaplói révén

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Jelentéskészítés  
**A termék képességei:** Monitorozás és jelentéskészítés
 
Bevezetésével a **ügyfélalkalmazás** -naplókat a bejelentkezési tevékenységek mezője ügyfelek is most lásd: felhasználók, amelyek az örökölt hitelesítés használata. Ügyfelek érhetik el ezeket az információkat a bejelentkezések az MS Graph API használatával is, vagy a bejelentkezés keresztül Tevékenységnaplók az Azure AD-portálra, ahol használhatja a **ügyfélalkalmazás** örökölt hitelesítések szűrés vezérlő. Tekintse meg a dokumentáció További részletekért.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2018"></a>2018 júliusától új összevont alkalmazások érhetők el az Azure AD alkalmazáskatalógusában

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**A termék képességei:** Külső integráció
 
2018 július tettünk elérhetővé az alkalmazáskatalógusban támogatja az összevonási 16 új alkalmazásokról:

[Innováció Hub](https://docs.microsoft.com/azure/active-directory/saas-apps/innovationhub-tutorial), [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-tutorial), [bizonyos rendszergazdai egyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/saas-apps/certainadminsso-tutorial), PSUC átmeneti, [iPass SmartConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/ipasssmartconnect-tutorial), [képernyőfelvétel-O felosztásban](https://docs.microsoft.com/azure/active-directory/saas-apps/screencast-tutorial) , PowerSchool osztályterem, egységes [Eli bevezetési](https://docs.microsoft.com/azure/active-directory/saas-apps/elionboarding-tutorial), [Bomgar távoli támogatási](https://docs.microsoft.com/azure/active-directory/saas-apps/bomgarremotesupport-tutorial), [Nimblex](https://docs.microsoft.com/azure/active-directory/saas-apps/nimblex-tutorial), [Imagineer WebVision](https://docs.microsoft.com/azure/active-directory/saas-apps/imagineerwebvision-tutorial) , [Insight4GRC](https://docs.microsoft.com/azure/active-directory/saas-apps/insight4grc-tutorial), [SecureW2 JoinNow összekötő](https://docs.microsoft.com/azure/active-directory/saas-apps/securejoinnow-tutorial), [Kanbanize](https://review.docs.microsoft.com/azure/active-directory/saas-apps/kanbanize-tutorial), [SmartLPA](https://review.docs.microsoft.com/azure/active-directory/saas-apps/smartlpa-tutorial), [képességek alapja](https://docs.microsoft.com/azure/active-directory/saas-apps/skillsbase-tutorial)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS integrációja az Azure Active Directoryval](https://aka.ms/appstutorial). Az alkalmazás szerepeltetése az Azure AD-alkalmazásgyűjtemény ajánlati kapcsolatos további információkért lásd: [az alkalmazás szerepeltetése az Azure Active Directory alkalmazáskatalógusában](https://aka.ms/azureadapprequest).

---
 
### <a name="new-user-provisioning-saas-app-integrations---july-2018"></a>2018. július – új felhasználókiépítési funkció az SaaS-alkalmazás-integrációkban

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Alkalmazás kiépítése  
**A termék képességei:** Külső integráció
 
Azure ad-ben automatizálhatja a létrehozás, a karbantartással és a SaaS-alkalmazások, például a Dropbox, a Salesforce, ServiceNow vagy további felhasználói identitásokat eltávolítását teszi lehetővé. 2018 július hozzáadtuk a felhasználók a következő alkalmazások az Azure AD-alkalmazásgyűjtemény támogatása:

- [Cisco WebEx](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-webex-provisioning-tutorial)

- [Bonusly](https://docs.microsoft.com/azure/active-directory/saas-apps/bonusly-provisioning-tutorial)

Minden alkalmazás, amely támogatja a felhasználók átadásának az Azure AD katalógusban listáját lásd: [SaaS integrációja az Azure Active Directoryval](https://aka.ms/appstutorial).

---

### <a name="connect-health-for-sync---an-easier-way-to-fix-orphaned-and-duplicate-attribute-sync-errors"></a>Connect Health for Sync – Az árván maradt és ismétlődő attribútumok szinkronizálásából fakadó hibák javításának egyszerűbb módja

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** AD Connect  
**A termék képességei:** Monitorozás és jelentéskészítés
 
Az Azure AD Connect Health önkiszolgáló szervizelést kell végeznie, jelölje ki és hárítsa el a szinkronizálási hibák mutatja be. Ez a szolgáltatás hibaelhárítása a duplikált attribútummal szinkronizálási hibák, és kijavítja a rendszer árva objektumokat az Azure ad-ből. A diagnosztika a következő előnyökkel jár:

- Duplikált attribútummal szinkronizálási hibák, adott javításokat nyújtó leszűkíti

- A javítás vonatkozik, kijelölve, az Azure AD-forgatókönyvek esetén egyetlen lépésben hibák megoldása

- Nincs frissítés vagy a konfiguráció be-és a funkció használatához szükséges

További információkért lásd: [diagnosztizálása és a duplikált attribútummal szinkronizálási hibák javítása](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-diagnose-sync-errors)

---

### <a name="visual-updates-to-the-azure-ad-and-msa-sign-in-experiences"></a>Az Azure AD és az MSA bejelentkezési felületének vizuális frissítései

**Típusa** Megváltozott funkció  
**Szolgáltatás kategóriája:** Azure AD  
**A termék képességei:** Felhasználók hitelesítése

Frissítettük a felhasználói felületen, a Microsoft online services bejelentkezési élményt, például az Office 365 és az Azure. Ez a változás lehetővé teszi a képernyők, kevésbé zsúfolt és egyszerűbb. Ez a változás kapcsolatos további információkért lásd: a [hamarosan várható az Azure AD bejelentkezési felület fejlesztései](https://cloudblogs.microsoft.com/enterprisemobility/2018/04/04/upcoming-improvements-to-the-azure-ad-sign-in-experience/) blog.

---

### <a name="new-release-of-azure-ad-connect---july-2018"></a>Az Azure AD Connect új kiadása – 2018. július

**Típusa** Megváltozott funkció  
**Szolgáltatás kategóriája:** Alkalmazás kiépítése  
**A termék képességei:** Identitáséletciklus-menedzsment

Az Azure AD Connect legújabb kiadása tartalmazza: 

- Hibajavítások és a támogatási frissítéseket. 

- A Ping összevonni integráció általános elérhetősége

- A legújabb SQL 2012-ügyfél frissítései 

A frissítéssel kapcsolatos további információkért lásd [: Azure ad Connect: Verziókiadások előzményei](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history)

---

### <a name="updates-to-the-terms-of-use-end-user-ui"></a>A használati feltételek végfelhasználói felhasználói felületének frissítése

**Típusa** Megváltozott funkció  
**Szolgáltatás kategóriája:** Használati feltételek  
**A termék képességei:** Cégirányítás

Frissítjük a jelen használati feltételek végfelhasználó felhasználói felületén az elfogadási karakterlánc.

**Aktuális szöveg.** [TenantName] erőforrásokhoz férhet hozzá, el kell fogadnia a használati feltételeket.<br>**Új szöveget.** [TenantName] erőforrás eléréséhez olvassa el a használati feltételeket.

**Aktuális szöveg:** Az elfogadás kiválasztása azt jelenti, hogy elfogadja a fenti használati feltételeket.<br>**Új szöveg:** Az elfogadás gombra kattintva erősítse meg, hogy elolvasta és megértette a használati feltételeket.

---
 
### <a name="pass-through-authentication-supports-legacy-protocols-and-applications"></a>Az átmenő hitelesítés támogatja az örökölt protokollok és alkalmazások használatát

**Típusa** Megváltozott funkció  
**Szolgáltatás kategóriája:** Hitelesítések (Bejelentkezések)  
**A termék képességei:** Felhasználók hitelesítése
 
Az átmenő hitelesítés most már támogatja az örökölt protokollok és alkalmazások. Most már teljes mértékben támogatja a következő korlátozások vonatkoznak:

- Felhasználói bejelentkezések örökölt Office ügyfélalkalmazások számára, az Office 2010 és Office 2013, anélkül, hogy a modern hitelesítést.

- A hozzáférést az naptár megosztása és a rendelkezésre állási információk az Exchange hibrid környezetek az Office 2010-et csak.

- Felhasználói bejelentkezések Skype az üzleti ügyfélalkalmazások számára a modern hitelesítés nélkül.

- Felhasználói bejelentkezések a PowerShell 1.0-s verzióját.

- Az Apple Device Enrollment Program (Apple DEP), az iOS beállítási asszisztens használatával. 

---
 
### <a name="converged-security-info-management-for-self-service-password-reset-and-multi-factor-authentication"></a>Az önkiszolgáló jelszóátállításhoz és a többtényezős hitelesítéshez használt biztonsági adatok kezelésének összevonása

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** SSPR  
**A termék képességei:** Felhasználók hitelesítése

Ez az új funkció lehetővé teszi, hogy a felhasználók saját biztonsági adatok (például telefonszám, e-mail címét, mobilalkalmazás és így tovább) önkiszolgáló jelszó-visszaállítás (SSPR) és a multi-factor Authentication (MFA) egyetlen felületen kezeljék. Felhasználók többé nem kell az azonos biztonsági információkat kelljen regisztrálniuk az SSPR és a többtényezős hitelesítés a két különböző környezeteket. Az új felületet is vonatkozik, akik rendelkeznek vagy az SSPR, vagy a többtényezős hitelesítés.

Egy szervezet MFA vagy az SSPR regisztrációs nem kényszerítése, ha a felhasználók regisztrálhatják-e a biztonsági adataikat keresztül a **saját alkalmazások** portálon. Itt a felhasználók regisztrálhatnak semmilyen metódus az MFA vagy az SSPR engedélyezve van. 

Egy választható nyilvános előzetes kiadásról. A rendszergazdák bekapcsolhatja az új felhasználói felület (ha szükséges) felhasználók vagy a bérlő minden felhasználók egy kiválasztott csoportja számára.

---
 
### <a name="use-the-microsoft-authenticator-app-to-verify-your-identity-when-you-reset-your-password"></a>A Microsoft Authenticator alkalmazással igazolhatja személyazonosságát a jelszava átállításakor

**Típusa** Megváltozott funkció  
**Szolgáltatás kategóriája:** SSPR  
**A termék képességei:** Felhasználók hitelesítése

Ez a funkció lehetővé teszi, hogy a nem rendszergazda jogosultságú igazolják egy értesítést vagy a Microsoft Authenticator (vagy bármely más hitelesítő alkalmazást) kód jelszó alaphelyzetbe állítása. Rendszergazdák bekapcsolása után a önkiszolgáló jelszó-visszaállítási mód, mobilalkalmazás aka.ms/mfasetup vagy aka.ms/setupsecurityinfo keresztül regisztráló felhasználók használhatja mobilalkalmazásukkal ellenőrzési módszerként a jelszó alaphelyzetbe állítása során.

Mobilalkalmazás-értesítés csak be kell kapcsolni, szabályzata előírja a két módszer a jelszó részeként.

---

## <a name="june-2018"></a>2018. június

### <a name="change-notice-security-fix-to-the-delegated-authorization-flow-for-apps-using-azure-ad-activity-logs-api"></a>Változási Megjegyzés: Biztonsági javítás a delegált engedélyezési folyamathoz az Azure AD Activity logs API-t használó alkalmazásokhoz

**Típusa** Tervezett módosítás  
**Szolgáltatás kategóriája:** Jelentéskészítés  
**A termék képességei:** Monitorozás és jelentéskészítés

Az erősebb biztonsági kényszerítési miatt volt módosítást a delegált engedélyezési folyamatok eléréséhez használó alkalmazásokra vonatkozó engedélyeinek [az Azure AD tevékenység naplók API-k](https://aka.ms/aadreportsapi). Ez a változás történik a **2018. június 26**.

Ha bármely, az alkalmazások az Azure AD tevékenység API-k, kövesse az alábbi lépéseket annak érdekében, hogy az alkalmazás nem lesz történik, a módosítás után új.

**Az Alkalmazásengedélyek frissítése**

1. Jelentkezzen be az Azure Portalon, válassza **Azure Active Directory**, majd válassza ki **Alkalmazásregisztrációk**.
2. Válassza ki az Azure AD tevékenység naplók API-t használó, válassza ki az alkalmazást **beállítások**, jelölje be **szükséges engedélyek**, majd válassza ki a **Windows Azure Active Directory** API.
3. A a **delegált engedélyek** területén a **hozzáférés engedélyezése** panelen jelölje be a **olvasási directory** adatokat, és válassza ki **mentése**.
4. Válassza ki **engedélyeket**, majd válassza ki **Igen**.
    
    >[!Note]
    >A következőkhöz biztosítanak engedélyeket: az alkalmazás globális rendszergazdának kell lennie.

További információkért lásd: a [engedélyeket](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-prerequisites-azure-portal#grant-permissions) eléréséhez az Azure AD jelentéskészítési API-t a cikk az Előfeltételek területéhez.

---

### <a name="configure-tls-settings-to-connect-to-azure-ad-services-for-pci-dss-compliance"></a>Az Azure AD-szolgáltatásokhoz, a PCI DSS TLS-beállítások konfigurálása

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** –  
**A termék képességei:** Platform

Transport Layer Security (TLS) protokoll, amely két kommunikáló alkalmazás közötti adatvédelmi és adatintegritás biztosít, és a jelenleg használt leggyakrabban telepített biztonsági protokoll.

A [PCI biztonsági szabványok Tanácsa](https://www.pcisecuritystandards.org/) megállapította, hogy korai verziói a TLS és a Secure Sockets Layer (SSL) le kell tiltani, új és biztonságosabb alkalmazások protokollok engedélyezése, a megfelelőségi től értéke **június 30., 2018-as**. Ez a módosítás azt jelenti, hogy ha az Azure AD-szolgáltatásokhoz és PCI-DSS szabvány igényelnek, le kell tiltania a TLS 1.0. A TLS több verziói elérhetők, de a TLS 1.2 a legújabb verzió érhető el az Azure Active Directory Services. Erősen ajánlott közvetlenül a TLS 1.2-es ügyfél-kiszolgáló és a böngésző és a kiszolgáló kombinációk áthelyezése.

Elavult böngészők nem támogatja a TLS újabb verziók, például a TLS 1.2. A böngésző által támogatott mely verziói a TLS megtekintéséhez nyissa meg a [Qualys SSL Labs](https://www.ssllabs.com/) webhelyről, és kattintson a **teszteléséhez a böngésző**. Javasoljuk, hogy a böngésző legújabb verziójára frissítse és lehetőleg engedélyezése csak a TLS 1.2-es.

**A TLS 1.2-es, böngésző engedélyezése**

- **A Microsoft Edge és az Internet Explorer (mindkettő vannak beállítva az Internet Explorerben)**

    1. Válassza ki az Internet Explorer megnyitásához **eszközök** > **Internetbeállítások** > **speciális**.
    2. Az a **biztonsági** területen válassza **TLS 1.2 használatára**, majd válassza ki **OK**.
    3. Minden böngészőablakot bezárni, majd indítsa újra az Internet Explorerben. 

- **Google Chrome**

    1. Nyissa meg a Google Chrome, típus *chrome://settings/* a címsorában, majd nyomja le az **Enter**.
    2. Bontsa ki a **speciális** lehetőségeket, nyissa meg a **rendszer** területre, és válassza ki **nyissa meg a proxybeállítások**.
    3. Az a **internetes tulajdonságok** jelölje ki a **speciális** lépjen a **biztonsági** területen válassza **TLS 1.2 használatára**, majd válassza ki a  **OK**.
    4. Minden böngészőablakot bezárni, majd indítsa újra a Google Chrome-ban.

- **Mozilla Firefox**

    1. Nyissa meg a Firefox, típus *kapcsolatos: config* címsorában, és nyomja le az **Enter**.
    2. Keresse meg az érvényességi *TLS*, majd válassza ki a **security.tls.version.max** bejegyzés.
    3. Állítsa az értékét **3** kényszerítése a böngészőben, hogy a TLS 1.2-es verzió használatával, és válassza **OK**.

        >[!NOTE]
        >Firefox 60.0 verzió támogatja a TLS 1.3-as, így a security.tls.version.max érték is megadható **4**.

    4. Minden böngészőablakot bezárni, majd indítsa újra a Mozilla Firefox.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2018"></a>Új összevont alkalmazások érhetők el az Azure AD-alkalmazásgyűjtemény – 2018. június

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**A termék képességei:** Külső integráció
 
2018 június tettünk elérhetővé az alkalmazáskatalógusban támogatja az összevonási 15 új alkalmazásokról:

[Skytap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skytap-tutorial), [zene stabilizálódási](https://docs.microsoft.com/azure/active-directory/active-directory-saas-settlingmusic-tutorial), [SAML 1.1-es jogkivonat engedélyezve LOB-alkalmazás](https://docs.microsoft.com/azure/active-directory/active-directory-saas-saml-tutorial), [Supermood](https://docs.microsoft.com/azure/active-directory/active-directory-saas-supermood-tutorial), [Autotask](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), [ Végponti biztonsági mentési](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), [Skyhigh hálózatok](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skyhighnetworks-tutorial), Smartway2, [TonicDM](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tonicdm-tutorial), [Moconavi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-moconavi-tutorial), [Zoho egy](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zohoone-tutorial), [ Helyszíni SharePoint-](https://docs.microsoft.com/azure/active-directory/active-directory-saas-sharepoint-on-premises-tutorial), [CX Suite fája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-foreseecxsuite-tutorial), [Vidyard](https://docs.microsoft.com/azure/active-directory/active-directory-saas-vidyard-tutorial), [ChronicX](https://docs.microsoft.com/azure/active-directory/active-directory-saas-chronicx-tutorial)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS integrációja az Azure Active Directoryval](https://aka.ms/appstutorial). Az alkalmazás szerepeltetése az Azure AD-alkalmazásgyűjtemény ajánlati kapcsolatos további információkért lásd: [az alkalmazás szerepeltetése az Azure Active Directory alkalmazáskatalógusában](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---

### <a name="azure-ad-password-protection-is-available-in-public-preview"></a>Nyilvános előzetes verzióban érhető el az Azure AD jelszóvédelem

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Identitásvédelem  
**A termék képességei:** Felhasználók hitelesítése

Segítségével az Azure AD jelszóvédelem kiiktathatja könnyen kitalálni a jelszót a környezetből. Mivel ezek a jelszavak segítséget nyújt egy jelszó megfelelő típusú támadás a biztonsági sérülés kockázata.

Pontosabban az Azure AD jelszóvédelem nyújt segítséget:

- A munkahelyi fiókok védelmét is az Azure AD-ben és a Windows Server Active Directory (AD). 
- Leállítja a felhasználók listáját a leggyakrabban használt jelszavak több mint 500, és több mint 1 millió karakter helyettesítő változata ezeket a jelszavakat a jelszavak használatával.
- Felügyelheti az Azure AD-jelszó védelmi egyetlen helyről az Azure AD-portálon is az Azure ad és helyszíni Windows Server AD.

Az Azure AD jelszóvédelem kapcsolatos további információkért lásd: [rossz jelszavak, a szervezet számára](https://aka.ms/aadpasswordprotectiondocs).

---

### <a name="new-all-guests-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Új "minden vendég" – a használati feltételek létrehozása során létrehozott feltételes hozzáférési szabályzat sablonja

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Használati feltételek  
**A termék képességei:** Cégirányítás

A használati feltételek létrehozása során a rendszer létrehoz egy új feltételes hozzáférési szabályzatot a "minden vendég" és az "összes alkalmazás" számára is. Az új csoportházirend-sablon létrehozásának és érvényesítési folyamat egyszerűsítésével vendégek újonnan létrehozott használati feltételek vonatkoznak.

További információkért lásd: [Azure Active Directory használati feltételek funkció](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="new-custom-conditional-access-policy-template-created-during-terms-of-use-creation"></a>A használati feltételek létrehozása során létrehozott új "egyéni" feltételes hozzáférési szabályzat sablonja

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Használati feltételek  
**A termék képességei:** Cégirányítás

A használati feltételek létrehozásakor létrejön egy új "egyéni" feltételes hozzáférési házirend-sablon is. Ez az új házirend-sablon lehetővé teszi a felhasználási feltételek létrehozását, majd a feltételes hozzáférési szabályzat létrehozási paneljén való azonnali megnyitását anélkül, hogy manuálisan kellene navigálnia a portálon.

További információkért lásd: [Azure Active Directory használati feltételek funkció](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="new-and-comprehensive-guidance-about-deploying-azure-multi-factor-authentication"></a>Üzembe helyezése az Azure multi-factor Authentication az új és átfogó útmutatást

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Egyéb  
**A termék képességei:** Identitásbiztonság és -védelem
 
Kiadás új részletes útmutatást nyújt az Azure multi-factor Authentication (MFA) telepítése a szervezetben.

Az MFA üzembe helyezési útmutató megtekintéséhez nyissa meg a [identitás üzembe helyezési útmutatók](https://aka.ms/DeploymentPlans) adattárat a Githubon. Az üzembe helyezési útmutatók visszajelzést megadásához használja a [központi telepítési csomag visszajelzési űrlap](https://aka.ms/deploymentplanfeedback). Ha az üzembe helyezési útmutatók kapcsolatban bármilyen kérdése van, írjon nekünk az [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="azure-ad-delegated-app-management-roles-are-in-public-preview"></a>Az Azure AD delegált felügyeleti szerepkör a nyilvános előzetes verzióban érhető el

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**A termék képességei:** Hozzáférés-vezérlés

A rendszergazdák mostantól delegálhatja kezelési feladatokat a globális rendszergazdai szerepkör hozzárendelése nélkül. Az új szerepkörök és funkciók a következők:

- **Új standard szintű Azure AD felügyeleti szerepkörök:**

    - **Alkalmazás-rendszergazda.** Minden alkalmazást, beleértve a regisztráció, egyszeri bejelentkezési beállításainak, alkalmazás-hozzárendelések és a licencelés, alkalmazást proxybeállításokat és jóváhagyás minden aspektusa kezelhető képesek (kivéve az Azure AD-erőforrások).

    - **Felhőalkalmazás-rendszergazda.** Biztosít minden, az alkalmazás-rendszergazda funkcióit, kivéve az alkalmazásproxy, mert ez nem biztosít helyszíni hozzáférés.

    - **Alkalmazás fejlesztője.** Listázását teszi létrehozása alkalmazásregisztrációkat, akkor is, ha a **engedélyezése a felhasználók számára alkalmazások regisztrálása** beállítás ki van kapcsolva.

- **Tulajdonjog (alkalmazásonkénti regisztrációs és enterprise alkalmazás beállítása, a csoport tulajdonjogának folyamat hasonló:**
 
    - **Alkalmazásregisztráció tulajdonosa.** A saját tulajdonú alkalmazásregisztráció, beleértve az alkalmazás-jegyzékfájl, és további tulajdonosok hozzáadása minden aspektusa kezelhető szerepkörök.

    - **Vállalati alkalmazás tulajdonosa.** Szerepkörök a vállalati tulajdonban lévő alkalmazások, beleértve az egyszeri bejelentkezési beállításainak, alkalmazás-hozzárendelések és jóváhagyás számos szempontból kezelése (kivéve az Azure AD-erőforrások).

További információ a nyilvános előzetes verzióban: a [Azure ad-ben delegált felügyelet szerepkör a nyilvános előzetes verzióban érhető el!](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/13/hallelujah-azure-ad-delegated-application-management-roles-are-in-public-preview/) blog. További információ a szerepkörökről és engedélyekről: [rendszergazdai szerepkörök hozzárendelése az Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal).

---

## <a name="may-2018"></a>2018. május

### <a name="expressroute-support-changes"></a>Az ExpressRoute-támogatás módosításai

**Típusa** Tervezett módosítás  
**Szolgáltatás kategóriája:** Hitelesítések (Bejelentkezések)  
**A termék képességei:** Platform  

A szoftver egy szolgáltatott szoftverként, például az Azure Active Directory (Azure AD) úgy tervezték, hogy leginkább végigkövetésével közvetlenül az internethez, anélkül, hogy az expressroute-on vagy bármely más személyes VPN-alagutat. Emiatt a **2018. augusztus 1**, hogy-környezetei már nem az ExpressRoute használatával az Azure nyilvános társviszony-létesítés Azure AD-szolgáltatások és a Microsoft társviszony-létesítés Azure Közösségek. Ez a változás által érintett szolgáltatások előfordulhat, hogy figyelje meg, hogy az Azure AD-forgalom fokozatosan pótlása ExpressRoute-ból az interneten.

Amíg tudjuk módosítani a támogatási azzal is tisztában van vannak továbbra is olyan helyzetekben, ahol szüksége lehet használ egy dedikált Kapcsolatcsoportok a hitelesítési forgalomhoz. Ez az oka Azure ad-ben továbbra is támogatja a bérlőnkénti IP-címtartomány korlátozások használata az ExpressRoute és a szolgáltatások már a Microsoft társviszony-létesítés az "Egyéb Office 365 Online szolgáltatások"-Közösség tagjaival. Ha a szolgáltatásokat érintő eseményekről, de az ExpressRoute van szüksége, tegye a következőket:

- **Ha Ön az Azure nyilvános társviszony-létesítés.** Ugrás a Microsoft társviszony-létesítés, és regisztráljon a **egyéb Office 365-szolgáltatások (12076:5100)** közösségi. Az Azure nyilvános társviszony-létesítésről Microsoft társviszony-létesítésre áthelyezéséről további információ: a [áthelyezni egy nyilvános társviszony-létesítés Microsoft társviszony-létesítésre](https://docs.microsoft.com/azure/expressroute/how-to-move-peering) cikk.

- **Ha Ön a Microsoft társviszony-létesítés.** Regisztráljon a **egyéb Office 365 Online szolgáltatás (12076:5100)** közösségi. Útválasztási követelményeivel kapcsolatos további információkért tekintse meg a [támogatása BGP-Közösségek szakasz](https://docs.microsoft.com/azure/expressroute/expressroute-routing#bgp) az ExpressRoute útválasztási követelményei című cikkben.

Ha folytatja, dedikált Kapcsolatcsoportok használni kell, kommunikáljon a Microsoft Account team való használatához engedélyt kell a **egyéb Office 365 Online szolgáltatás (12076:5100)** közösségi. A MS Office-felügyelt felülvizsgáló Bizottság ellenőrzi-e ezeket a Kapcsolatcsoportok kell, és győződjön meg arról, hogy megszegéseinek műszaki tartja őket. Az Office 365-höz útvonalszűrők létrehozására tett kísérlet nem engedélyezett előfizetések hibaüzenetet fog kapni. 
 
---

### <a name="microsoft-graph-apis-for-administrative-scenarios-for-tou"></a>Felügyeleti forgatókönyvek a használati feltételek a Microsoft Graph API-k

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Használati feltételek  
**A termék képességei:** Fejlesztői környezet
 
Az Azure AD használati feltételeinek felügyeleti működéséhez Microsoft Graph API-kat adtunk hozzá. Létrehozhatja, frissítheti és törölheti a használati feltételeket.

---

### <a name="add-azure-ad-multi-tenant-endpoint-as-an-identity-provider-in-azure-ad-b2c"></a>Identitás-szolgáltatóként az Azure AD B2C az Azure AD több-bérlős végpont hozzáadása

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** B2C - fogyasztói identitások kezelése  
**A termék képességei:** B2B/B2C
 
Egyéni szabályzatok használatával most már hozzáadhat az Azure AD közös végpont identitásszolgáltatójaként az Azure AD B2C-ben. Ez lehetővé teszi, hogy az összes olyan jelentkezik be az alkalmazások az Azure AD-felhasználó bejegyzés hibaérzékeny pont. További információkért lásd [: Azure Active Directory B2C: Lehetővé teszi, hogy a felhasználók egyéni szabályzatok](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-commonaad-custom)használatával jelentkezzenek be egy több-BÉRLŐs Azure ad-identitásba.

---

### <a name="use-internal-urls-to-access-apps-from-anywhere-with-our-my-apps-sign-in-extension-and-the-azure-ad-application-proxy"></a>Belső URL-címeket használnak az alkalmazások bárhonnan elérheti az Azure AD-alkalmazásproxy és a saját alkalmazások bejelentkezési bővítménye

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Saját alkalmazások  
**A termék képességei:** SSO
 
Felhasználók most már elérheti alkalmazások belső URL-címeket, még ha a vállalati hálózaton kívülről keresztül a saját alkalmazások biztonságos bejelentkezési bővítménye az Azure ad használatával. Ez minden olyan alkalmazással, amely bármely böngészőben, amelyen telepítve van hozzáférési Panel böngészőbővítményének használatánál az Azure AD-alkalmazásproxy használatával közzétett fog működni. Az URL-cím átirányítási funkció automatikusan engedélyezve lesz, miután egy felhasználó bejelentkezik a bővítményt. A bővítmény letölthető [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176), [Chrome](https://go.microsoft.com/fwlink/?linkid=866367), és [Firefox](https://go.microsoft.com/fwlink/?linkid=866366).

---
 
### <a name="azure-active-directory---data-in-europe-for-europe-customers"></a>Az Azure Active Directory - adatok Európában Európa-ügyfelek számára

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Egyéb  
**A termék képességei:** GoLocal

Ügyfeleknek az Európai szükséges az Európai maradjon, és Európai adatközpontok értekezlet védelmében, és az Európai törvényi kívül nem replikálódnak. Ez [cikk](https://go.microsoft.com/fwlink/?linkid=872328) milyen azonosító információkat a vonatkozó részletes Európa tárolja, és adatokat részleteiért tárolt kívül az Európai adatközpontok biztosítanak. 

---
 
### <a name="new-user-provisioning-saas-app-integrations---may-2018"></a>Új felhasználók SaaS-alkalmazás Integrációk – 2018. május

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Alkalmazás kiépítése  
**A termék képességei:** Külső integráció
 
Azure ad-ben automatizálhatja a létrehozás, a karbantartással és a SaaS-alkalmazások, például a Dropbox, a Salesforce, ServiceNow vagy további felhasználói identitásokat eltávolítását teszi lehetővé. 2018 május hozzáadtuk a felhasználók a következő alkalmazások az Azure AD-alkalmazásgyűjtemény támogatása:

- [BlueJeans](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bluejeans-provisioning-tutorial)

- [Alapkövei OnDemand](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cornerstone-ondemand-provisioning-tutorial)

- [Zendesk](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zendesk-provisioning-tutorial)

Minden alkalmazás, amely támogatja a felhasználók átadásának az Azure AD katalógusban listáját lásd: [ https://aka.ms/appstutorial ](https://aka.ms/appstutorial).

---
 
### <a name="azure-ad-access-reviews-of-groups-and-app-access-now-provides-recurring-reviews"></a>A csoportok Azure AD hozzáférési felülvizsgálatok és alkalmazás-hozzáférés mostantól lehetőséget nyújt az ismétlődő értékelések

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Hozzáférési felülvizsgálatok  
**A termék képességei:** Cégirányítás
 
Hozzáférési felülvizsgálat csoportok és alkalmazások már általánosan elérhető az Azure AD Premium P2 részeként.  A rendszergazdák tudják a hozzáférési felülvizsgálatok például havonta vagy negyedévente rendszeres időközönként automatikusan ismétlődni csoporttagságok és alkalmazás-hozzárendelések konfigurálása.

---

### <a name="azure-ad-activity-logs-sign-ins-and-audit-are-now-available-through-ms-graph"></a>Az Azure Active Directory-naplók (bejelentkezési és hitelesítési) mostantól elérhetők az MS Graph használatával

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Jelentéskészítés  
**A termék képességei:** Monitorozás és jelentéskészítés
 
Az Azure AD tevékenységeket tartalmazó naplók, amely tartalmazza a bejelentkezések és auditnaplók, mostantól elérhetők az MS Graph használatával. A Microsoft közzétette a két végpontok keresztül az MS Graph ezek a naplók eléréséhez. Tekintse meg a [dokumentumok](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal) az első lépések az Azure AD Reporting API programozás alapú hozzáférést. 

---
 
### <a name="improvements-to-the-b2b-redemption-experience-and-leave-an-org"></a>Élmény és a egy szervezeti hagyja a B2B-érvényesítési fejlesztései

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** B2B  
**A termék képességei:** B2B/B2C

**Egyszeri visszaváltás:** Miután megoszt egy erőforrást egy vendég felhasználóval a B2B API használatával – nem kell elküldenie egy különleges meghívó e-mailt. A legtöbb esetben a vendégfelhasználó az erőforrás eléréséhez, és megnyílik a beváltási élmény szerinti keresztül. Nincs több hatással miatt kimaradt e-maileket. Nincs több kell megadniuk a Vendég "Volt, érvényesítési hivatkozásra kattintva a rendszer akkor küld?". Ez azt jelenti, miután SPO a meghívó Managert használja – zavaros mellékleteket is rendelkezhet – belső és külső – bármely érvényesítési állapotát az összes felhasználó azonos kanonikus URL-CÍMÉT.

**Modern beváltási élmény:** Nincs több osztott képernyős beváltási Kezdőlap. A modern a felhasználók látják az adatvédelmi nyilatkozat a meghívó szervezetet, felhasználói jóváhagyás, ugyanúgy, mint az ilyen harmadik felek alkalmazásaihoz.

**A vendég felhasználói elhagyhatják a szervezeti szervezetet:** Ha a felhasználó kapcsolata egy szervezettel már véget ért, a szervezete elhagyhatja őket. Nincs több történt a meghívó szervezeti admin "el kell távolítani", több ritikus támogatási jegyeket.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2018"></a>Új összevont alkalmazások érhetők el az Azure AD-alkalmazásgyűjtemény – 2018. május

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**A termék képességei:** Külső integráció
 
2018 május tettünk elérhetővé összevonással 18 új alkalmazásokról, az alkalmazáskatalógusban támogatja:

[AwardSpring](https://docs.microsoft.com/azure/active-directory/active-directory-saas-awardspring-tutorial), Infogix Data3Sixty szabályozzák, [Yodeck](https://docs.microsoft.com/azure/active-directory/active-directory-saas-infogix-tutorial), [a Jamf Pro](https://docs.microsoft.com/azure/active-directory/active-directory-saas-jamfprosamlconnector-tutorial), [KnowledgeOwl](https://docs.microsoft.com/azure/active-directory/active-directory-saas-knowledgeowl-tutorial), [Envi MMIS](https://docs.microsoft.com/azure/active-directory/active-directory-saas-envimmis-tutorial), [LaunchDarkly](https://docs.microsoft.com/azure/active-directory/active-directory-saas-launchdarkly-tutorial), [Adobe Captivate Prime](https://docs.microsoft.com/azure/active-directory/active-directory-saas-adobecaptivateprime-tutorial), [Montázs Online](https://docs.microsoft.com/azure/active-directory/active-directory-saas-montageonline-tutorial),[まなびポケット](https://docs.microsoft.com/azure/active-directory/active-directory-saas-manabipocket-tutorial), OpenReel, [Arc-közzététel – egyszeri bejelentkezés ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-arc-tutorial), [PlanGrid](https://docs.microsoft.com/azure/active-directory/active-directory-saas-plangrid-tutorial), [iWellnessNow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-iwellnessnow-tutorial), [Proxyclick](https://docs.microsoft.com/azure/active-directory/active-directory-saas-proxyclick-tutorial), [Riskware](https://docs.microsoft.com/azure/active-directory/active-directory-saas-riskware-tutorial), [állományt](https://docs.microsoft.com/azure/active-directory/active-directory-saas-flock-tutorial), [Reviewsnap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-reviewsnap-tutorial)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS integrációja az Azure Active Directoryval](https://aka.ms/appstutorial).

Az alkalmazás szerepeltetése az Azure AD-alkalmazásgyűjtemény ajánlati kapcsolatos további információkért lásd: [az alkalmazás szerepeltetése az Azure Active Directory alkalmazáskatalógusában](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

---
 
### <a name="new-step-by-step-deployment-guides-for-azure-active-directory"></a>Az Azure Active Directory új részletes üzembe helyezési útmutatók

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Egyéb  
**A termék képességei:** Címtár
 
Új, lépésenkénti útmutató a Azure Active Directory (Azure AD) üzembe helyezéséhez, beleértve az önkiszolgáló jelszó-visszaállítás (SSPR), az egyszeri bejelentkezés (SSO), a feltételes hozzáférés (CA), az App proxy, a felhasználók kiépítése, a Active Directory összevonási szolgáltatások (AD FS) (ADFS) Átmenő hitelesítés (PTA ESP) és az ADFS jelszavas kivonatának szinkronizálása (PHS).

Az üzembe helyezési útmutatók megtekintéséhez nyissa meg a [identitás üzembe helyezési útmutatók](https://aka.ms/DeploymentPlans) adattárat a Githubon. Az üzembe helyezési útmutatók visszajelzést megadásához használja a [központi telepítési csomag visszajelzési űrlap](https://aka.ms/deploymentplanfeedback). Ha az üzembe helyezési útmutatók kapcsolatban bármilyen kérdése van, írjon nekünk az [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="enterprise-applications-search---load-more-apps"></a>Vállalati alkalmazások keresése, – további alkalmazások betöltése

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**A termék képességei:** SSO
 
Ha nem találja az alkalmazás / egyszerű szolgáltatások? Hozzáadtunk további alkalmazásokat a vállalati alkalmazások betöltéséhez összes alkalmazások listáját. Alapértelmezés szerint 20 alkalmazás mutatjuk be. Rákattinthat, **Továbbiak betöltése** további alkalmazások megtekintéséhez. 

---
 
### <a name="the-may-release-of-aadconnect-contains-a-public-preview-of-the-integration-with-pingfederate-important-security-updates-many-bug-fixes-and-new-great-new-troubleshooting-tools"></a>Az aad Connect kiadása tartalmaz egy nyilvános előzetes verziója a pingfederate-tel, az integráció május fontos biztonsági frissítések, számos hibajavítást és új nagyszerű új hibaelhárítási eszközei. 

**Típusa** Megváltozott funkció  
**Szolgáltatás kategóriája:** AD Connect  
**A termék képességei:** Identitáséletciklus-menedzsment
 
Az aad Connect kiadása tartalmaz egy nyilvános előzetes verziója a pingfederate-tel, az integráció május fontos biztonsági frissítések, számos hibajavítást és új nagyszerű új hibaelhárítási eszközei. A kibocsátási megjegyzésekben talál [Itt](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#118190).

---

### <a name="azure-ad-access-reviews-auto-apply"></a>Az Azure AD hozzáférési felülvizsgálatok: automatikus alkalmazása

**Típusa** Megváltozott funkció  
**Szolgáltatás kategóriája:** Hozzáférési felülvizsgálatok  
**A termék képességei:** Cégirányítás

A hozzáférési felülvizsgálatok a csoportok és alkalmazások most már általánosan elérhetők az Azure AD Premium P2 részeként. Automatikusan a felülvizsgáló módosítások alkalmazásához, csoport vagy alkalmazás, a hozzáférési felülvizsgálatot követően a rendszergazdák konfigurálhatják. A rendszergazda azt is megadhatja, folyamatos hozzáférésre a felhasználó, mi történik, ha a felülvizsgálók nem válaszol, távolítsa el a hozzáférést, férjenek vagy rendszer ajánlásokat. 

---

### <a name="id-tokens-can-no-longer-be-returned-using-the-query-response_mode-for-new-apps"></a>Azonosító-jogkivonatokat már nem adható vissza a lekérdezés response_mode használatával új alkalmazások esetében. 

**Típusa** Megváltozott funkció  
**Szolgáltatás kategóriája:** Hitelesítések (Bejelentkezések)  
**A termék képességei:** Felhasználók hitelesítése
 
Be- és 2018. április 25. után létrehozott alkalmazások már nem igényelhetnek egy **id_token** használatával a **lekérdezés** response_mode.  Ekkor az Azure AD beágyazott OIDC előírásoknak, és segítséget nyújt az alkalmazások támadási felület csökkentése.  2018. április 25. előtt létrehozott alkalmazások nincs letiltva a használatával a **lekérdezés** együtt, egy response_type response_mode **id_token**.  A hiba, az aad-ből, id_token kérésekor **AADSTS70007: "query" érték nem támogatott a "response_mode" jogkivonat kérése során**.

A **töredék** és **form_post** response_modes továbbra is működni – Ha létrehozni új alkalmazás objektumok (például az alkalmazásproxy-használat), győződjön meg, hogy ezek response_modes egyikének használata előtt, hozhatnak létre egy Új alkalmazás.  

---
 
## <a name="april-2018"></a>2018. április 

### <a name="azure-ad-b2c-access-token-are-ga"></a>Az Azure AD B2C hozzáférési tokent is általánosan elérhető

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** B2C - fogyasztói identitások kezelése  
**A termék képességei:** B2B/B2C 

Most már elérheti az Azure AD B2C által védett webes API-k hozzáférési jogkivonatok használatával. A funkció nyilvános előzetes verzióról való általánosan elérhető A felhasználói felület konfigurálása az Azure AD B2C-alkalmazást és a webes API-kat úgy lett továbbfejlesztve, és más kisebb fejlesztések történtek.
 
További információkért lásd [: Azure ad B2C: Hozzáférési jogkivonatok](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-access-tokens)kérése.

---

### <a name="test-single-sign-on-configuration-for-saml-based-applications"></a>Egyszeri bejelentkezés beállításainak a SAML-alapú alkalmazások tesztelése

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**A termék képességei:** SSO

Egyszeri bejelentkezési SAML-alapú alkalmazások konfigurálása, ha már tudjuk tesztelni az integrációt a lapon. Bejelentkezés során hibát észlel, ha a hibát a tesztelési élményt biztosíthat, és az Azure AD nyújt megoldási lépéseket az adott probléma megoldásához.

További információkért lásd:

- [Egyszeri bejelentkezés konfigurálása az Azure Active Directory alkalmazáskatalógusában nem szereplő alkalmazásokhoz](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps)
- [SAML-alapú egyszeri bejelentkezés az Azure Active Directory-alkalmazások hibakeresése](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)

---
 
### <a name="azure-ad-terms-of-use-now-has-per-user-reporting"></a>Az Azure AD használati feltételei mostantól felhasználónkénti jelentéskészítéssel rendelkeznek

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Használati feltételek  
**A termék képességei:** Megfelelőség
 
A rendszergazdák mostantól egy adott használati feltételek kiválasztása, és tekintse meg az összes felhasználót, hogy hozzájárult, hogy, hogy a jelen használati feltételek és milyen dátum/idő, került sor.

További információkért lásd: a [az Azure AD használati feltételek funkció](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---
 
### <a name="azure-ad-connect-health-risky-ip-for-ad-fs-extranet-lockout-protection"></a>Azure AD Connect Health: Kockázatos IP-cím AD FS extranetes zárolási védelemhez 

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Egyéb  
**A termék képességei:** Monitorozás és jelentéskészítés

Connect Health mostantól támogatja a képességére IP-címek, amely meghaladja a küszöbértéket hibás felhasználónév/jelszó bejelentkezések óránkénti vagy napi rendszerességgel. Ez a szolgáltatás által biztosított képességek a következők:

- Átfogó jelentés IP-cím és az óránként/naponta alapján testre szabható küszöbértékkel hozza létre a sikertelen bejelentkezések száma.
- E-mail alapú értesítések megjelenítése, ha egy adott IP-cím túllépte a küszöbértéket, hibás felhasználónév/jelszó bejelentkezések egy óránként/naponta történik.
- A letöltési lehetőséget ehhez az adatok részletes elemzés

További információkért lásd: [kockázatos IP jelentés](https://aka.ms/aadchriskyip).

---
 
### <a name="easy-app-config-with-metadata-file-or-url"></a>Egyszerű alkalmazás konfigurációs metaadatfájl vagy URL-címe

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**A termék képességei:** SSO

A vállalati alkalmazások oldalon rendszergazdák feltölthet egy SAML-metaadatfájl SAML-alapú bejelentkezés az AAD-galéria és a katalógusban nem szereplő alkalmazás konfigurálásához.

Az Azure AD alkalmazás összevonási metaadatainak URL-címe segítségével ezenkívül a célzott alkalmazás egyszeri bejelentkezés konfigurálása.

További információkért lásd: [konfigurálása egyszeri bejelentkezéshez, az alkalmazásokat, amelyek nem szerepelnek az Azure Active Directory alkalmazáskatalógusában](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps).

---

### <a name="azure-ad-terms-of-use-now-generally-available"></a>Az Azure AD használati feltételek már általánosan elérhető

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Használati feltételek  
**A termék képességei:** Megfelelőség
 

Az Azure AD használati feltételei a nyilvános előzetes verzióról általánosan elérhetővé váltanak.

További információkért lásd: a [az Azure AD használati feltételek funkció](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Az adott szervezetek a B2B-felhasználók meghívások engedélyezése vagy letiltása

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** B2B  
**A termék képességei:** B2B/B2C
 

Mostantól megadhatja, mely kívánt megosztás és együttműködés az Azure AD B2B együttműködés a fiókpartner-szervezetek. Ehhez hozzon létre adott választhatja engedélyezik vagy megtagadják a tartományok. Tartomány le van tiltva, ezek a képességek használatával, amikor az alkalmazottak már nem küldhet meghívókat személyek ebben a tartományban.

Ez segít, hogy ki férhet hozzá az erőforrásokat, miközben lehetővé teszi a zökkenőmentes felhasználói élmény engedélyezett.

Ez a VÁLLALATKÖZI együttműködési funkció minden Azure Active Directory ügyfél számára elérhető, és prémium szintű Azure AD-funkciókkal, például a feltételes hozzáféréssel és az identitás-védelemmel együtt használható a külső üzleti felhasználók aláírásának részletesebb szabályozása érdekében. és hozzáférés.

További információkért lásd: [engedélyezési és blokkolási Segítségkérések B2B-felhasználók az adott szervezetek](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-allow-deny-list).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Az Azure AD-alkalmazásgyűjtemény elérhető új összevont alkalmazások

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**A termék képességei:** Külső integráció

2018 április tettünk elérhetővé összevonással 13 új alkalmazásokról, az alkalmazáskatalógusban támogatja:

HCM, feltétel [FiscalNote](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fiscalnote-tutorial), [titkos kiszolgáló (helyszíni)](https://docs.microsoft.com/azure/active-directory/active-directory-saas-secretserver-on-premises-tutorial), [dinamikus jel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-dynamicsignal-tutorial), [mindWireless](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mindwireless-tutorial), [szervezeti diagram Most már](https://docs.microsoft.com/azure/active-directory/active-directory-saas-orgchartnow-tutorial), [Ziflow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ziflow-tutorial), [AppNeta Teljesítményfigyelő](https://docs.microsoft.com/azure/active-directory/active-directory-saas-appneta-tutorial), [Elium](https://docs.microsoft.com/azure/active-directory/active-directory-saas-elium-tutorial) , [Fluxx Labs](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fluxxlabs-tutorial), [ Cisco felhőalapú](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ciscocloud-tutorial), kereskedelmi, [SafetyNet](https://docs.microsoft.com/azure/active-directory/active-directory-saas-safetynet-tutorial)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS integrációja az Azure Active Directoryval](https://aka.ms/appstutorial).

Az alkalmazás szerepeltetése az Azure AD-alkalmazásgyűjtemény ajánlati kapcsolatos további információkért lásd: [az alkalmazás szerepeltetése az Azure Active Directory alkalmazáskatalógusában](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

---
 
### <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications-public-preview"></a>Támogatás B2B-felhasználók Azure AD-ben a hozzáférést a helyszíni alkalmazások (nyilvános előzetes verzió)

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** B2B  
**A termék képességei:** B2B/B2C

Azure Active Directory (Azure AD) B2B együttműködési lehetőségeket az Azure ad a fiókpartner-szervezetek vendégfelhasználók használó szervezetek, most már megadhat a B2B-felhasználók a helyszíni alkalmazásokhoz való hozzáférés. Ezek a helyszíni alkalmazások az SAML-alapú hitelesítés vagy az integrált Windows-hitelesítés (IWA) a Kerberos által korlátozott delegálás (KCD).

További információkért lásd: [Grant B2B-felhasználók Azure AD-ben a hozzáférést a helyszíni alkalmazások](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-hybrid-cloud-to-on-premises).

---
 
### <a name="get-sso-integration-tutorials-from-the-azure-marketplace"></a>Egyszeri bejelentkezés az integrációs oktatóanyagok lekérése az Azure Marketplace-ről

**Típusa** Megváltozott funkció  
**Szolgáltatás kategóriája:** Egyéb  
**A termék képességei:** Külső integráció

Ha egy alkalmazás, amely szerepel az a [Azure Marketplace-en](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1) támogatja az SAML egyszeri bejelentkezéshez, kattintson a alapú **Letöltés most** biztosít az adott alkalmazáshoz kapcsolódó integrációs oktatóanyagát. 

---

### <a name="faster-performance-of-azure-ad-automatic-user-provisioning-to-saas-applications"></a>Nagyobb teljesítmény az Azure ad-ben a felhasználók automatikus átadása a SaaS-alkalmazásokban

**Típusa** Megváltozott funkció  
**Szolgáltatás kategóriája:** Alkalmazás kiépítése  
**A termék képességei:** Külső integráció
 
Korábban, az Azure Active Directory-felhasználó kiépítési összekötőket az olyan SaaS-alkalmazások (például Salesforce, ServiceNow és a Box) használó ügyfelek sikerült teljesítménycsökkenést tapasztal, ha az Azure AD-bérlőt tartalmazott több mint 100 000 összevont felhasználók és csoportok, és meghatározni, hogy mely felhasználókat kell létrehozni felhasználó és csoport-hozzárendelések használta.

2018. április 2. fontos teljesítményjavítás üzembe helyezése az Azure AD létesítési szolgáltatás, amely nagy mértékben csökkentheti az Azure Active Directory és a cél SaaS-alkalmazások közötti kezdeti szinkronizálás végrehajtásához szükséges idő a.

Ennek eredményeképpen a számos ügyfél, amely eddig szinkronizálások az alkalmazásoknak a tartott, hány nap vagy soha nem fejeződött be, most belül percek vagy órák során hiba.

További információkért lásd: [mi történik a kiépítés során?](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning#what-happens-during-provisioning)

---

### <a name="self-service-password-reset-from-windows-10-lock-screen-for-hybrid-azure-ad-joined-machines"></a>Az önkiszolgáló jelszó-visszaállítást a hibrid Azure ad-ben a Windows 10-es zárolási képernyőről csatlakoztatott gépek

**Típusa** Megváltozott funkció  
**Szolgáltatás kategóriája:** Önkiszolgáló jelszóátállítás  
**A termék képességei:** Felhasználók hitelesítése
 
Frissítettük a Windows 10-es SSPR funkció támogatását olyan gépeken, amelyek hibrid Azure AD-hez. Ez a funkció érhető el a Windows 10 RS4 lehetővé teszi, hogy a felhasználók visszaállíthatják a jelszavukat a Windows 10-es gép a zárolási képernyőről. Felhasználók, akik engedélyezve van, és új jelszó önkiszolgáló kérésére regisztrált Ez a szolgáltatás képes használni.

További információkért lásd: [az Azure AD-jelszó visszaállítása a bejelentkezési képernyőről](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-windows).

---

## <a name="march-2018"></a>2018. március
 
### <a name="certificate-expire-notification"></a>Tanúsítvány lejárati értesítés

**Típusa** Rögzített  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**A termék képességei:** SSO
 
Az Azure AD elküld egy értesítést, ha egy gyűjtemény egy tanúsítványt, vagy a katalógusban nem szereplő alkalmazás érvényessége hamarosan lejár. 

Egyes felhasználók nem kapják meg a vállalati alkalmazásokhoz SAML-alapú egyszeri bejelentkezés konfigurálva értesítések. A probléma megoldódott. Az Azure AD elküldi az értesítést a tanúsítványok, 7, 30 és 60 napon belül lejár. Ön ezt az eseményt naplóiban láthatja. 

További információkért lásd:

- [Tanúsítványok kezelése az összevont egyszeri bejelentkezés az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs)
- [Naplózási tevékenységre vonatkozó jelentések az Azure Active Directory portálon](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)
 
---
 
### <a name="twitter-and-github-identity-providers-in-azure-ad-b2c"></a>Azure AD B2C-vel a Twitter és a GitHub Identitásszolgáltatók

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** B2C - fogyasztói identitások kezelése  
**A termék képességei:** B2B/B2C
 
Most már hozzáadhat Twitter- és GitHub identitásszolgáltatójaként az Azure AD B2C-ben. Twitter kerül át a nyilvános előzetes verzióban általánosan elérhető GitHub kiadásra nyilvános előzetes verzióban érhető el.

További információkért lásd: [Mi az Azure AD B2B együttműködés?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
 
---

### <a name="restrict-browser-access-using-intune-managed-browser-with-azure-ad-application-based-conditional-access-for-ios-and-android"></a>Böngésző-hozzáférés korlátozása Intune Managed Browser használatával az Azure AD alkalmazás-alapú feltételes hozzáférés iOS-hez és Androidhoz

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Feltételes hozzáférés  
**A termék képességei:** Identitásbiztonság és -védelem
 
**Nyilvános előzetes verziója!**

**Egyszeri bejelentkezés Intune Managed Browser:** Az alkalmazottak használhatnak egyszeri bejelentkezést a natív ügyfeleken (például a Microsoft Outlookban) és az Azure AD-hez csatlakoztatott összes alkalmazás Intune Managed Browser.

**Intune Managed Browser feltételes hozzáférési támogatás:** Mostantól megkövetelheti, hogy az alkalmazottak az Intune által felügyelt böngészőt használják az alkalmazás alapú feltételes hozzáférési szabályzatok használatával.

További információ erről a [blogbejegyzés](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/15/the-intune-managed-browser-now-supports-azure-ad-sso-and-conditional-access/).

További információkért lásd:

- [Alkalmazás-alapú feltételes hozzáférés beállítása](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

- [Felügyeltböngésző-szabályzatok konfigurálása](https://aka.ms/managedbrowser)  

---
 
### <a name="app-proxy-cmdlets-in-powershell-ga-module"></a>Alkalmazás Webalkalmazásproxy-parancsmagok a Powershell általánosan elérhető a modul

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** App Proxy  
**A termék képességei:** Hozzáférés-vezérlés
 
Alkalmazásproxy parancsmagok támogatása már általánosan elérhető a Powershell-modult az! Ehhez szükség, hogy legyen naprakész a Powershell-modulok – Ha Ön több mint egy év mögött, egyes parancsmagok leállhat. 

További információkért lásd: [AzureAD](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0).
 
---
 
### <a name="office-365-native-clients-are-supported-by-seamless-sso-using-a-non-interactive-protocol"></a>Közvetlen egyszeri bejelentkezés használatával nem interaktív protokoll által támogatott natív Office 365-ügyfelek

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Hitelesítések (Bejelentkezések)  
**A termék képességei:** Felhasználók hitelesítése
 
A felhasználó Office 365-ügyfél natív használatával (verzió 16.0.8730.xxxx és újabb) csendes bejelentkezési felületet közvetlen egyszeri bejelentkezés használatával. A támogatási szolgáltatását az hozzáadását a nem interaktív (WS-Trust) protokoll az Azure AD.

További információkért lásd: [hogyan jelentkezzen be egy natív ügyfél közvetlen egyszeri bejelentkezés munkahelyi?](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-how-it-works#how-does-sign-in-on-a-native-client-with-seamless-sso-work)
 
---

### <a name="users-get-a-silent-sign-on-experience-with-seamless-sso-if-an-application-sends-sign-in-requests-to-azure-ads-tenant-endpoints"></a>A felhasználók kapnak a beavatkozás nélküli bejelentkezést, a közvetlen egyszeri bejelentkezés, ha egy alkalmazás bejelentkezési kéréseket küld az Azure AD-bérlő végpontok

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Hitelesítések (Bejelentkezések)  
**A termék képességei:** Felhasználók hitelesítése
 
A felhasználók kapnak a beavatkozás nélküli bejelentkezést, a közvetlen egyszeri bejelentkezés, ha egy alkalmazás (például `https://contoso.sharepoint.com`) bejelentkezési kéréseket küld, az Azure AD-bérlő végpontok – `https://login.microsoftonline.com/contoso.com/<..>` vagy `https://login.microsoftonline.com/<tenant_ID>/<..>` – helyett az Azure AD közös végpontot (`https://login.microsoftonline.com/common/<...>`).

További információkért lásd: [Azure Active Directory zökkenőmentes egyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). 

---
 
### <a name="need-to-add-only-one-azure-ad-url-instead-of-two-urls-previously-to-users-intranet-zone-settings-to-roll-out-seamless-sso"></a>Közvetlen egyszeri bejelentkezés bevezetése felhasználók Intranet zóna beállítását csak egy Azure ad-ben URL-cím helyett a korábban a két URL-címet hozzá kell adnia

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Hitelesítések (Bejelentkezések)  
**A termék képességei:** Felhasználók hitelesítése
 
Közvetlen egyszeri bejelentkezés vezethet be a felhasználók számára, hozzá, csak egy Azure AD URL-CÍMÉT a felhasználók az intranet zóna beállításait az Active Directory csoportházirend használatával kell: `https://autologon.microsoftazuread-sso.com`. Korábban az ügyfelek kellett adjon hozzá két URL-címet.

További információkért lásd: [Azure Active Directory zökkenőmentes egyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). 
 
---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Új összevont alkalmazások érhetők el az Azure AD-alkalmazásgyűjtemény

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**A termék képességei:** Külső integráció

2018 március tettünk elérhetővé összevonással 15 új alkalmazásokról, az alkalmazáskatalógusban támogatja:

[Boxcryptor](https://docs.microsoft.com/azure/active-directory/active-directory-saas-boxcryptor-tutorial), [CylancePROTECT](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cylanceprotect-tutorial), Wrike, [SignalFx](https://docs.microsoft.com/azure/active-directory/active-directory-saas-signalfx-tutorial), Segéd által FirstAgenda, [YardiOne](https://docs.microsoft.com/azure/active-directory/active-directory-saas-yardione-tutorial), Vtiger CRM, inwink, [amplitúdó](https://docs.microsoft.com/azure/active-directory/active-directory-saas-amplitude-tutorial), [Spacio](https://docs.microsoft.com/azure/active-directory/active-directory-saas-spacio-tutorial), [ContractWorks](https://docs.microsoft.com/azure/active-directory/active-directory-saas-contractworks-tutorial), [Bersin](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bersin-tutorial), [Mercell](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mercell-tutorial), [Trisotech digitális Vállalati kiszolgáló](https://docs.microsoft.com/azure/active-directory/active-directory-saas-trisotechdigitalenterpriseserver-tutorial), [Qumu felhőalapú](https://docs.microsoft.com/azure/active-directory/active-directory-saas-qumucloud-tutorial).
 
Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS integrációja az Azure Active Directoryval](https://aka.ms/appstutorial).

Az alkalmazás szerepeltetése az Azure AD-alkalmazásgyűjtemény ajánlati kapcsolatos további információkért lásd: [az alkalmazás szerepeltetése az Azure Active Directory alkalmazáskatalógusában](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---
 
### <a name="pim-for-azure-resources-is-generally-available"></a>Az Azure-erőforrások PIM szolgáltatás általánosan elérhető

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Privileged Identity Management  
**A termék képességei:** Privileged Identity Management
 
Ha az Azure AD Privileged Identity Management-címtárbeli szerepkörökhöz tartozó használ, mostantól használhatja a PIM időhöz kötött access és a hozzárendelés képességek például az előfizetések, erőforráscsoportok, virtuális gépeket és bármely más, a támogatott erőforrás Azure-erőforrások szerepköreihez tartozó az Azure Resource Manager által. Többtényezős hitelesítés kikényszerítéséhez a Just-In-Time szerepkörök aktiválása során, és jóváhagyott módosítása a windows együttes aktiválások ütemezése. Ez a kiadás emellett bővült a fejlesztések többek között egy frissített felhasználói Felületet, jóváhagyási munkafolyamatokat, valamint a hamarosan lejáró szerepkörök bővítése, és lejárt szerepkörök megújítása a nyilvános előzetes verzióban nem érhető el.

További információkért lásd: [PIM az Azure-erőforrások (előzetes verzió)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac)
 
---
 
### <a name="adding-optional-claims-to-your-apps-tokens-public-preview"></a>Nem kötelező jogcímek, az alkalmazások jogkivonatok (nyilvános előzetes verzió) hozzáadása

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Hitelesítések (Bejelentkezések)  
**A termék képességei:** Felhasználók hitelesítése
 
Az Azure AD-alkalmazás mostantól az egyéni vagy választható jogcímkérések JWTs vagy SAML jogkivonatokat.  Ezek a jogcímeket a felhasználó vagy bérlői kapcsolatos, amelyek nem tartoznak a jogkivonat mérete vagy alkalmazhatósági megkötések miatt alapértelmezés szerint.  Ez jelenleg nyilvános előzetes verzióban elérhető az Azure AD-alkalmazások az 1.0-s és 2.0-s verziójú végpontok.  Információ dokumentációjában milyen jogcímek lehet hozzáadni, a tanúsítványkérelmeket az alkalmazásjegyzék szerkesztése.  

További információkért lásd: [nem kötelező az Azure AD-jogcímek](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims).
 
---
 
### <a name="azure-ad-supports-pkce-for-more-secure-oauth-flows"></a>Az Azure AD támogatja PKCE biztonságosabb OAuth-folyamatok

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Hitelesítések (Bejelentkezések)  
**A termék képességei:** Felhasználók hitelesítése
 
Vegye figyelembe a PKCE, amely lehetővé teszi több biztonságos kommunikáció során az OAuth 2.0 engedélyezési kód engedélyezési folyamatával támogatása az Azure AD-docs frissítve lett-e.  Az 1.0-s és 2.0-s verziójú végpontok S256 mind a titkosítatlan szöveges code_challenges támogatottak. 

További információkért lásd: [hozzáférési kód kérése](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols-oauth-code#request-an-authorization-code). 
 
---
 
### <a name="support-for-provisioning-all-user-attribute-values-available-in-the-workday-get_workers-api"></a>Az összes felhasználói attribútum értékei a Workday Get_Workers API-ban elérhető kiépítés támogatása

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Alkalmazás kiépítése  
**A termék képességei:** Külső integráció
 
A nyilvános előzetes verziója bejövő való üzembe helyezést, a Workday az Active Directory és az most már az Azure AD képes kinyerni és kiépítés a Workday Get_Workers API-ban elérhető összes attribútum értéket támogatja. Ez hozzáadja a több száz további standard szintű csomag támogatja, és tartalmazza a szükséges kezdeti verziójában a Workday túlmutató egyéni attribútumok bejövő összekötő kiépítése.

További információkért lásd: [A munkanap felhasználói attribútumaik listájának testreszabása](https://docs.microsoft.com/azure/active-directory/active-directory-saas-workday-inbound-tutorial#customizing-the-list-of-workday-user-attributes)

---

### <a name="changing-group-membership-from-dynamic-to-static-and-vice-versa"></a>A dinamikus csoporttagság statikusra, és fordítva módosítása

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Csoportkezelés  
**A termék képességei:** Együttműködés
 
Akkor lehet módosítani egy csoport tagságát kezeléséről. Ez akkor hasznos, ha meg szeretné tartani csoport neve és azonosítója a rendszeren, hogy a csoport bármely meglévő hivatkozások még érvényesek; Új csoport létrehozása miatt frissíteni kellene az ezeket a hivatkozásokat.
Frissítettük az Azure AD felügyeleti központban az funkciót. Most már használó ügyfelek átalakíthatják a meglévő csoportokat a dinamikus tagsági hozzárendelt tagságot és fordítva. A meglévő PowerShell-parancsmagok is továbbra is elérhetők.

További információ: a [Azure Active Directory csoportok dinamikus tagsági szabályai](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)

---

### <a name="improved-sign-out-behavior-with-seamless-sso"></a>Közvetlen egyszeri bejelentkezés továbbfejlesztett kijelentkezési viselkedés

**Típusa** Megváltozott funkció  
**Szolgáltatás kategóriája:** Hitelesítések (Bejelentkezések)  
**A termék képességei:** Felhasználók hitelesítése
 
Korábban még akkor is, ha a felhasználók explicit módon az Azure AD által védett alkalmazáshoz kijelentkezik, ezek lenne automatikusan megtörténik vissza a közvetlen egyszeri bejelentkezés használatával, ha azok megpróbált hozzáférni egy Azure AD-alkalmazást újra a vállalati hálózaton belül, tartományhoz csatlakoztatott eszközök. Ezzel a kijelentkezési használata támogatott.  Ez lehetővé teszi, hogy a felhasználók kiválaszthatják az azonos vagy eltérő Azure AD-fiók jelentkezzen be újra, alatt automatikusan megtörténik a közvetlen egyszeri bejelentkezés használata helyett.

További információkért lásd: [Azure Active Directory zökkenőmentes egyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso)
 
---
 
### <a name="application-proxy-connector-version-154020-released"></a>Application Proxy Connector ezen verziója, amely a 1.5.402.0

**Típusa** Megváltozott funkció  
**Szolgáltatás kategóriája:** App Proxy  
**A termék képességei:** Identitásbiztonság és -védelem
 
A connector ezen verziója fokozatosan vezetjük be November keresztül. A connector új verziója az alábbi újításokat tartalmazza:

- Az összekötő már tartományi szintű cookie-k helyett altartomány szintjének beállítása. Ez biztosítja, hogy egy egyenletesebb egyszeri Bejelentkezéses felhasználói élmény, és elkerülhető az redundáns hitelesítési kérések.
- Darabolásos kódolás kérelmek támogatása
- Továbbfejlesztett összekötő állapotfigyelés 
- Számos hibajavítás és stabilitási fejlesztések

További információkért lásd: [megismerheti az Azure AD-alkalmazásproxy összekötőit](https://docs.microsoft.com/azure/active-directory/application-proxy-understand-connectors).
 
---

## <a name="february-2018"></a>2018. február
 
### <a name="improved-navigation-for-managing-users-and-groups"></a>Továbbfejlesztett navigáció a felhasználók és csoportok kezelése

**Típusa** Tervezett módosítás  
**Szolgáltatás kategóriája:** Címtárkezelés  
**A termék képességei:** Címtár

Egyszerűsítettük a navigációt a felhasználók és csoportok kezelése. Akkor most már elhagyhatja a címtár áttekintő közvetlenül, a lista az összes olyan felhasználó, a könnyebb elérhetőség érdekében a törölt felhasználók listájához. Is elérheti a címtár áttekintő a közvetlenül a csoportok, a könnyebb elérhetőség érdekében a csoport felügyeleti beállítások listája. És még a címtár áttekintő lapján kereshet egy felhasználó, csoport, a vállalati alkalmazás vagy alkalmazásregisztráció. 

---

### <a name="availability-of-sign-ins-and-audit-reports-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>Bejelentkezések és naplózási jelentések a 21Vianet által működtetett Microsoft Azure-ban (Azure China 21Vianet)

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Azure Stack  
**A termék képességei:** Monitorozás és jelentéskészítés

Az Azure Active Directory naplózási jelentések mostantól elérhetők a 21Vianet (Azure China 21Vianet) példányok által működtetett Microsoft Azure-ban. A következő naplók kapcsolódnak foglalja magában:

- **Bejelentkezési tevékenységeket tartalmazó naplók** – tartalmazza az összes bejelentkezés a bérlő naplóihoz.

- **Önkiszolgáló jelszó-Auditnaplók** – az SSPR auditnaplókkal tartalmazza.

- **Könyvtár-felügyeleti Audit naplók** -naplók mindegyikét tartalmazza a directory felügyelettel kapcsolatos naplózási például a felhasználói felügyeleti, felügyeleti és mások.

Ezek a naplók is, hogy a környezet működésébe elemzéseket kaphat. A megadott adatokkal a következőket teheti:

- Határozza meg, hogyan az alkalmazások és szolgáltatások vannak az egyes a felhasználók számára.

- Végezzen hibaelhárítást a hibákat, amelyek megakadályozzák a felhasználók hozzáférése a munkájukat.

Ezek a jelentések használatával kapcsolatos további információkért lásd: [Azure Active Directory jelentéskészítési](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal).

---

### <a name="use-report-reader-role-non-admin-role-to-view-azure-ad-activity-reports"></a>"A jelentés olvasó" szerepkör (nem rendszergazdai szerepkör) használata az Azure AD-Tevékenységjelentések megtekintése

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Jelentéskészítés  
**A termék képességei:** Monitorozás és jelentéskészítés

Tartozó ügyfelek visszajelzés engedélyezése nem rendszergazda szerepkörök férnek hozzá az Azure Active Directory-naplókat, engedélyeztük a lehetőségét, hogy a felhasználók számára a "Jelentés olvasó" szerepkör a bejelentkezések hozzáférési és naplózási tevékenység belül az Azure Portalon, valamint a Graph API-k használatával. 

További információ jelentések, olvassa el [Azure Active Directory jelentéskészítési](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). 

---

### <a name="employeeid-claim-available-as-user-attribute-and-user-identifier"></a>Az EmployeeID jogcím felhasználói attribútumként és felhasználói azonosítóként

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**A termék képességei:** SSO
 
Konfigurálható **EmployeeID** a felhasználói azonosító és a felhasználói attribútum tagfelhasználó és B2B-vendégek a SAML-alapú bejelentkezés alkalmazásokat a vállalati alkalmazás felhasználói felületén.

További információkért lásd: [az Azure Active Directory vállalati alkalmazásokhoz SAML-jogkivonatban kiadott jogcímek testreszabása](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).

---

### <a name="simplified-application-management-using-wildcards-in-azure-ad-application-proxy"></a>Helyettesítő karakterek használata az Azure AD-alkalmazásproxy egyszerűsített felügyelet

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** App Proxy  
**A termék képességei:** Felhasználók hitelesítése
 
Alkalmazás központi telepítésének megkönnyíti, valamint csökkentheti a felügyelettel járó többletterhelést, mostantól támogatjuk a helyettesítő karaktereket használó alkalmazások közzétételének lehetősége. Helyettesítő karaktert tartalmazó alkalmazás közzététele, hajtsa végre a szabványos alkalmazás-közzétételi folyamat, de használja egy helyettesítő karaktert a belső és külső URL-címeket.

További információkért lásd: [helyettesítő alkalmazásokat az Azure Active Directory application proxy](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-wildcard)

---

### <a name="new-cmdlets-to-support-configuration-of-application-proxy"></a>Az alkalmazásproxy konfigurálását támogató új parancsmagok

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** App Proxy  
**A termék képességei:** Platform

Az Azure ad PowerShell előzetes modul legújabb kiadása új parancsmagokat, amelyek lehetővé teszik az ügyfelek konfigurálása az Application Proxy alkalmazásai PowerShell-lel tartalmazza.

Az új parancsmagok a következők: 

- Get-AzureADApplicationProxyApplication
- Get-AzureADApplicationProxyApplicationConnectorGroup
- Get-AzureADApplicationProxyConnector
- Get-AzureADApplicationProxyConnectorGroup
- Get-AzureADApplicationProxyConnectorGroupMembers
- Get-AzureADApplicationProxyConnectorMemberOf
- New-AzureADApplicationProxyApplication
- New-AzureADApplicationProxyConnectorGroup
- Remove-AzureADApplicationProxyApplication
- Remove-AzureADApplicationProxyApplicationConnectorGroup
- Remove-AzureADApplicationProxyConnectorGroup
- Set-AzureADApplicationProxyApplication
- Set-AzureADApplicationProxyApplicationConnectorGroup
- Set-AzureADApplicationProxyApplicationCustomDomainCertificate
- Set-AzureADApplicationProxyApplicationSingleSignOn
- Set-AzureADApplicationProxyConnector
- Set-AzureADApplicationProxyConnectorGroup

---
 
### <a name="new-cmdlets-to-support-configuration-of-groups"></a>A csoportok konfigurálását támogató új parancsmagok

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** App Proxy  
**A termék képességei:** Platform

Az Azure ad PowerShell-modul legújabb kiadása a csoportok kezelése az Azure AD-parancsmagokat tartalmaz. Ezek a parancsmagok korábban az AzureADPreview modulban számára elérhető, és most megjelennek az Azure ad-modul

A csoport parancsmagok, amelyek általánosan elérhető kiadás most a következők: 

- Get-AzureADMSGroup
- New-AzureADMSGroup
- Remove-AzureADMSGroup
- Set-AzureADMSGroup
- Get-AzureADMSGroupLifecyclePolicy
- New-AzureADMSGroupLifecyclePolicy
- Remove-AzureADMSGroupLifecyclePolicy
- Add-AzureADMSLifecyclePolicyGroup
- Remove-AzureADMSLifecyclePolicyGroup
- Reset-AzureADMSLifeCycleGroup   
- Get-AzureADMSLifecyclePolicyGroup

---
 
### <a name="a-new-release-of-azure-ad-connect-is-available"></a>Az Azure AD Connect új kiadása érhető el

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** AD-szinkronizáló  
**A termék képességei:** Platform
 
Az Azure AD Connect az az előnyben részesített eszköz szinkronizálhatja az Azure AD között, és a helyszíni adatforráshoz, beleértve a Windows Server Active Directory és az LDAP adatait.

>[!Important]
>A build bemutatja a séma és a szinkronizálási szabály módosításokat. Az Azure AD Connect szinkronizálási szolgáltatás elindít egy teljes importálást és teljes szinkronizálást lépéseket frissítés után. Ezt a viselkedést módosításának módjával kapcsolatos információkért lásd: [késlelteti a frissítés után teljes szinkronizálást hogyan](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version#how-to-defer-full-synchronization-after-upgrade).

Ebben a kiadásban a következő frissítéseket és a változások rendelkezik:

**Rögzített kapcsolatos problémák**

- Javítsa ki a időzítési háttérfeladatok laphoz Partíciószűrés a következő lapra történő váltáskor.

- Kijavítva a hiba, amely engedély nélküli hozzáférési kísérlet során a configdb elemre egyéni művelet okozza.

- Kijavítva a hiba, helyreállítás az sql-kapcsolat időtúllépése.

- Kijavítva a hiba, ahol a SAN helyettesítő karaktereket is tartalmazó tanúsítványok előfeltétel-ellenőrzés sikertelen.

- Kijavítva a hiba, okozó miiserver.exe összeomlási AAD connector exportálás során.

- Kijavítva a hiba, ahol a egy helytelen jelszómegadási kísérlet DC bejelentkezve futtatását okozta az AAD connect varázsló konfigurációjának módosítása

**Új funkciók és fejlesztések**
 
- Alkalmazáshasználati - rendszergazdák válthat Ez az osztály az adatok be-és kikapcsolása.

- Az Azure AD Health-data - rendszergazdák fel kell keresnie azok állapotfigyelő beállításait szabályozhatja a health portálon. Után a szolgáltatás házirend módosítva lett, az ügynökök olvassa el, és azt kényszerítése.

- A jelszóvisszaíró konfigurációs eszközműveletek és se inicializace stránky folyamatjelző hozzáadva.

- A HTML-jelentést és a egy ZIP-szöveg teljes adatgyűjtés általános diagnosztikai továbbfejlesztett / HTML-jelentést.

- Az automatikus megbízhatósága frissítése, és hozzá lehet meghatározni a kiszolgáló állapotának biztosítása érdekében további telemetriai adatokat.

- Kiemelt jogosultságú fiókok AD-összekötő fiókhoz elérhető engedélyek korlátozása. Az új, a varázsló korlátozza az engedélyeket, a kiemelt jogosultságú fiókok tartozik az MSOL-fiók létrehozását követően a MSOL-fiókhoz. A módosítások befolyásolják az Expressz telepítés és a fiók automatikus létrehozása az egyéni telepítés.

- A telepítő, hogy ne követelje meg az aad Connect tiszta telepítését rendszergazdai jogosultság módosítani.

- Egy adott objektumra szinkronizálás hibáinak elhárítása új segédprogramot. Jelenleg a segédprogram ellenőrzi az alábbiakat:

    - Szinkronizált felhasználói objektum és a felhasználói fiók az Azure AD-bérlő a UserPrincipalName nem egyezik.
  
    - Ha az objektum ki lett szűrve a tartomány szűrés miatt
  
    - Ha az objektum ki lett szűrve a szervezeti egység (OU) szűrés miatt

- Új segédprogram az egy adott felhasználói fiók az a helyszíni Active Directoryban tárolt aktuális Jelszókivonat szinkronizálása. A segédprogram nem szükséges a jelszó módosítása. 

---
 
### <a name="applications-supporting-intune-app-protection-policies-added-for-use-with-azure-ad-application-based-conditional-access"></a>Az Azure AD alkalmazás-alapú feltételes hozzáféréssel együtt használható Intune App Protection szabályzatokat támogató alkalmazások

**Típusa** Megváltozott funkció  
**Szolgáltatás kategóriája:** Feltételes hozzáférés  
**A termék képességei:** Identitásbiztonság és -védelem

További alkalmazásokat is felvettünk, amelyek támogatják az alkalmazás-alapú feltételes hozzáférést. Most már hozzáférhet az Office 365-höz és más Azure AD-hez csatlakozó felhőalkalmazások ezeket a jóváhagyott ügyfélalkalmazások használatával.

A következő alkalmazások szerint február végétől lesznek hozzáadva:

- Microsoft Power BI

- A Microsoft indítója

- A Microsoft számlázás

További információkért lásd:

- [Jóváhagyott alkalmazás megkövetelése](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Azure AD-alkalmazás-alapú feltételes hozzáférés](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="terms-of-use-update-to-mobile-experience"></a>A Mobile Experience Használati feltételek frissítése 

**Típusa** Megváltozott funkció  
**Szolgáltatás kategóriája:** Használati feltételek  
**A termék képességei:** Megfelelőség

Amikor megjelennek a használati feltételeket, rákattinthat a **megtekintéssel? Kattintson ide a**. A hivatkozásra kattintva megnyílik a használati feltételeket, natív módon az eszközön. A betűméret a dokumentum vagy eszköz a képernyő méretétől függetlenül nagyításra, és igény szerint, olvassa el a dokumentumot. 

---
 
## <a name="january-2018"></a>2018. január
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Új összevont alkalmazások érhetők el az Azure AD-alkalmazásgyűjtemény 

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**A termék képességei:** Külső integráció

A 2018 január összevonási támogatással a következő új alkalmazások az alkalmazáskatalógusban lettek hozzáadva:

[IBM OpenPages](https://go.microsoft.com/fwlink/?linkid=864698), [OneTrust adatvédelmi felügyeleti szoftver](https://go.microsoft.com/fwlink/?linkid=861660), [Dealpath](https://go.microsoft.com/fwlink/?linkid=863526), [IriusRisk összevont könyvtárat, és [hűség NetBenefits](https://go.microsoft.com/fwlink/?linkid=864701).

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS integrációja az Azure Active Directoryval](https://aka.ms/appstutorial).

Az alkalmazás szerepeltetése az Azure AD-alkalmazásgyűjtemény ajánlati kapcsolatos további információkért lásd: [az alkalmazás szerepeltetése az Azure Active Directory alkalmazáskatalógusában](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---
 
### <a name="sign-in-with-additional-risk-detected"></a>Jelentkezzen be a további észlelt kockázattal érintett

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Identitásvédelem  
**A termék képességei:** Identitásbiztonság és -védelem

Az insight észlelt kockázati eseményekhez kap az Azure AD-előfizetéshez vannak kötve. Az Azure AD Premium P2 kiadás az a legrészletesebb információkat minden mögöttes észlelés beolvasása.

Az Azure AD Premium P1 Edition, amelyek nem tartoznak a licenc tartalomként jelennek meg a bejelentkezési kockázati esemény további észlelt kockázattal érintett.

További információkért tekintse át [Az Azure Active Directory kockázati eseményeivel](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events) foglalkozó cikket.
 
---

### <a name="hide-office-365-applications-from-end-users-access-panels"></a>Office 365-alkalmazásokhoz a felhasználó hozzáférési paneljein elrejtése

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Saját alkalmazások  
**A termék képességei:** SSO

Most már jobban kezelhető hogyan Office 365-alkalmazások jelennek meg az új felhasználói beállítás segítségével a felhasználók hozzáférési paneljein. Ez a beállítás hasznos lehet a felhasználó hozzáférési paneljein alkalmazások számának csökkentése Ha inkább csak Office-alkalmazások jelennek meg az Office portálon. A beállítás a található a **felhasználói beállítások** és címkével ellátott, **csak a tekintse meg az Office 365 portálon az Office 365-alkalmazások**.

További információkért lásd: [alkalmazás elrejtése a felhasználói élmény az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

---
 
### <a name="seamless-sign-into-apps-enabled-for-password-sso-directly-from-apps-url"></a>Zökkenőmentes bejelentkezési engedélyezve a jelszó SSO közvetlenül az alkalmazás URL-alkalmazásokba 

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Saját alkalmazások  
**A termék képességei:** SSO

A saját alkalmazások webböngésző-bővítmény már elérhető kényelmes eszköz használatával, amely áttekintést nyújt a saját alkalmazások egyszeri bejelentkezés lehetőségét a parancsikont a böngészőben. Telepítés után a felhasználó a böngészőben, amely az alkalmazások gyors hozzáférést biztosít számukra egy gofri ikon látható. Felhasználók mostantól igénybe veheti:

- Lehetővé teszi a közvetlenül bejelentkezni jelszó SSO-alapú alkalmazások az alkalmazás bejelentkezési lapján
- Indítsa el minden olyan alkalmazást kész keresési funkciója
- A bővítmény a legutóbb használt alkalmazások parancsikonjai
- A bővítmény a Microsoft Edge, a Chrome és a Firefox számára érhető el.
 
További információkért lásd: [saját alkalmazások biztonságos bejelentkezési bővítménye](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension).

---

### <a name="azure-ad-administration-experience-in-azure-classic-portal-has-been-retired"></a>Az Azure AD felügyeleti élmény a klasszikus Azure portálon kivonása

**Típusa** Elavult   
**Szolgáltatás kategóriája:** Azure AD  
**A termék képességei:** Címtár

8 2018 január, az Azure AD felügyeleti-től a klasszikus Azure portál felület kivonásra került. A klasszikus Azure portál maga funkciókészletét együtt történt. A későbbiekben, használjon a [Azure AD felügyeleti központban](https://aad.portal.azure.com) valamennyi a portal-alapú felügyeleti az Azure ad-ben.
 
---

### <a name="the-phonefactor-web-portal-has-been-retired"></a>A PhoneFactor-webportál kivonása

**Típusa** Elavult  
**Szolgáltatás kategóriája:** Azure AD  
**A termék képességei:** Címtár
 
2018. január 8. a PhoneFactor-webportál visszavontuk. Az MFA-kiszolgáló felügyeletéhez használt ezen a portálon, de ezekhez a függvényekhez rendelkezik át lett helyezve az Azure Portalon a portal.azure.com webhelyen. 

Az MFA-konfiguráció a következő helyen található: **MFA \> -kiszolgáló Azure Active Directory**
 
---
 
### <a name="deprecate-azure-ad-reports"></a>Az Azure AD-jelentések kivezetjük

**Típusa** Elavult  
**Szolgáltatás kategóriája:** Jelentéskészítés  
**A termék képességei:** Identitáséletciklus-menedzsment  


Az általánosan elérhető az új Azure Active Directory felügyeleti konzol és az új API-k már elérhető a tevékenység és a biztonsági jelentések, a jelentéskészítő API-k az a "/ jelentések" végpont kivonásra került, 2017. December 31-ig a végén.

**Mi érhető el?**

Az új felügyeleti konzol az átmenet részeként elérhetővé vált 2 új API-k az Azure AD-Tevékenységnaplók beolvasása. Az új API-készletet biztosít gazdagabb szűrési és rendezési funkció részletesebb naplózás és a bejelentkezési tevékenységek biztosítása mellett. Az adatok korábban már elérhető a biztonsági jelentések mostantól elérhető az Identity Protection kockázati események API-t a Microsoft Graph használatával.

További információkért lásd:

- [Az Azure Active Directory reporting API használatának első lépései](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal)

- [Az Azure Active Directory Identity Protection és a Microsoft Graph használatának első lépései](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-graph-getting-started)

---

## <a name="december-2017"></a>2017. december

### <a name="terms-of-use-in-the-access-panel"></a>Használati feltételek a hozzáférési panelen

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Használati feltételek  
**A termék képességei:** Megfelelőség
 
Most nyissa meg a hozzáférési panelen és megtekintheti a korábban elfogadott használati feltételeket.

Kövesse az alábbi lépéseket:

1. Nyissa meg a [MyApps portálról](https://myapps.microsoft.com), és jelentkezzen be.

2. A jobb felső sarokban, válassza ki a nevét, és válassza **profil** a listából. 

3. Az a **profil**válassza **használati feltételek áttekintése**. 

4. Most már megtekintheti a használati feltételeket a elfogadva. 

További információkért lásd: a [az Azure AD használati feltételek funkció (előzetes verzió)](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).
 
---
 
### <a name="new-azure-ad-sign-in-experience"></a>Új Azure AD bejelentkezési felület

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Azure AD  
**A termék képességei:** Felhasználóhitelesítés
 
Az Azure AD és a Microsoft identitás-fiókrendszer előkészíthetik a korábbinál sokoldalúbban úgy, hogy egy egységes megjelenést és működést. Emellett az Azure AD bejelentkezési oldal gyűjti a felhasználónév először egy második képernyőn a hitelesítő adatok követ.

További információkért lásd: [az új Azure AD bejelentkezési felület jelenleg nyilvános előzetes verzióban elérhető](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/).
 
---
 
### <a name="fewer-sign-in-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-sign-in"></a>Kevesebb bejelentkezési kérés: Az Azure AD bejelentkezési felületének új "bejelentkezve tartása"

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Azure AD  
**A termék képességei:** Felhasználóhitelesítés
 
A **bejelentkezve szeretnék maradni** váltotta fel egy új parancssort, hogy megjelenik-e sikeres hitelesítést követően az Azure AD bejelentkezési lapján jelölje be jelölőnégyzetet. 

Ha válaszol **Igen** , ez a kérdés a szolgáltatása, amely egy állandó frissítési jogkivonatot. Ez a viselkedés megegyezik a kiválasztott mikor a **bejelentkezve szeretnék maradni** jelölőnégyzetet a régi felületet. Összevont bérlők esetén ez a kérdés mutatja be, miután sikeresen hitelesíteni az összevont szolgáltatással.

További információ [: kevesebb bejelentkezési kérés: Az Azure AD-ben az új "bejelentkezve tartom" funkció előzetes](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/)verzióban érhető el. 

---

### <a name="add-configuration-to-require-the-terms-of-use-to-be-expanded-prior-to-accepting"></a>A használati feltételek elfogadása előtt ki kell bővíteni kell a konfiguráció hozzáadása

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Használati feltételek  
**A termék képességei:** Megfelelőség
 
Lehetőség a rendszergazdák számára szükséges a felhasználókat a feltételeket, mielőtt elfogadja a használati feltételek megtekintése.

Ezek közül bármelyikre **a** vagy **ki** a felhasználóktól a használati feltételeket, bontsa ki. A **a** beállítás megköveteli a felhasználóktól a használati feltételeket, mielőtt elfogadhatnák őket megtekintéséhez.

További információkért lásd: a [az Azure AD használati feltételek funkció (előzetes verzió)](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).
 
---

### <a name="scoped-activation-for-eligible-role-assignments"></a>Jogosult szerepkör-hozzárendelések hatókörrel rendelkező aktiválás

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Privileged Identity Management  
**A termék képességei:** Privileged Identity Management
 
Használhatja a hatókörön belüli aktiválási kevesebb, mint az eredeti hozzárendelés alapértelmezett autonómia jogosult Azure-erőforrás szerepkör-hozzárendelések aktiválása. Ilyen például, ha a kapott az előfizetés tulajdonosaként a bérlőben. Hatókörön belüli-aktiválási aktiválhatja a tulajdonosi szerepkör legfeljebb öt erőforrások (például az erőforráscsoportok és virtuális gépek) előfizetésen belül található. Az aktiválás felmerülő előfordulhat, hogy az esélye, kritikus fontosságú Azure-erőforrások nemkívánatos módosítások végrehajtása.

További információkért lásd: [Mi az Azure AD Privileged Identity Management?](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure).
 
---
 
### <a name="new-federated-apps-in-the-azure-ad-app-gallery"></a>Az Azure AD-alkalmazásgyűjtemény új összevont alkalmazások

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**A termék képességei:** Külső integráció

2017 December vezettünk be, ezeket az új összevonási alkalmazásokat az app-galériában támogatja:

[Accredible](https://go.microsoft.com/fwlink/?linkid=863523), Adobe Experience Manager [EFI digitális kirakat](https://go.microsoft.com/fwlink/?linkid=861685), [Communifire](https://go.microsoft.com/fwlink/?linkid=861676) CybSafe, [FactSet](https://go.microsoft.com/fwlink/?linkid=863525), [RENDSZERKÉP működését](https://go.microsoft.com/fwlink/?linkid=863517), [MOBI](https://go.microsoft.com/fwlink/?linkid=863521), [MobileIron az Azure AD-integrációs](https://go.microsoft.com/fwlink/?linkid=858027), [Reflektive](https://go.microsoft.com/fwlink/?linkid=863518), [bambusz feloldási GmbH által a SAML SSO](https://go.microsoft.com/fwlink/?linkid=863520), [SAML SSO a Bitbucket felbontása GmbH](https://go.microsoft.com/fwlink/?linkid=863519), [Vodeclic](https://go.microsoft.com/fwlink/?linkid=863522), WebHR, Zenegy Azure AD-integráció.

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS integrációja az Azure Active Directoryval](https://aka.ms/appstutorial).

Az alkalmazás szerepeltetése az Azure AD-alkalmazásgyűjtemény ajánlati kapcsolatos további információkért lásd: [az alkalmazás szerepeltetése az Azure Active Directory alkalmazáskatalógusában](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 
 
---

### <a name="approval-workflows-for-azure-ad-directory-roles"></a>Az Azure AD-címtárbeli szerepkörökhöz tartozó jóváhagyási munkafolyamatokat

**Típusa** Megváltozott funkció  
**Szolgáltatás kategóriája:** Privileged Identity Management  
**A termék képességei:** Privileged Identity Management
 
Az Azure AD-címtárbeli szerepkörökhöz tartozó jóváhagyási munkafolyamat szolgáltatás általánosan elérhető.

Jóváhagyási munkafolyamat, a rendszerjogosultságú szerepkör rendszergazdák megkövetelhetik, szerepkör-aktiválás kéréséhez jogosult-szerepkör tagjai az emelt szintű szerepkör használatához. Több felhasználó és csoport is lehet delegált jóváhagyó felelőssége. Jogosult szerepkör tagjai értesítéseket kaphat, amikor a jóváhagyás befejeződött, és azok szerepét aktív.

---
 
### <a name="pass-through-authentication-skype-for-business-support"></a>Átmenő hitelesítés: Skype vállalati verzió támogatása

**Típusa** Megváltozott funkció  
**Szolgáltatás kategóriája:** Hitelesítések (Bejelentkezések)  
**A termék képességei:** Felhasználóhitelesítés

Átmenő hitelesítés most támogatja a felhasználói bejelentkezéseket a Skype vállalati verzióhoz való ügyfél, amely támogatja a modern hitelesítést, amely tartalmazza az online és a hibrid topológiák. 

További információkért lásd: [Skype for Business topológiákat támogatja a modern hitelesítést használó](https://technet.microsoft.com/library/mt803262.aspx).
 
---

### <a name="updates-to-azure-ad-privileged-identity-management-for-azure-rbac-preview"></a>Az Azure RBAC (előzetes verzió) az Azure AD Privileged Identity Management frissítések

**Típusa** Megváltozott funkció  
**Szolgáltatás kategóriája:** Privileged Identity Management  
**A termék képességei:** Privileged Identity Management
 
Az Azure AD Privileged Identity Management (PIM) az Azure szerepköralapú hozzáférés-vezérlés (RBAC) a nyilvános előzetes verzióban frissítéssel az alábbi műveleteket hajthatja végre:

* Ezen éppen elég felügyelettel.
* Erőforrás-szerepkörökkel aktiváláshoz jóváhagyásra van szükség.
* Ütemezzen egy jövőbeli aktiválását egy szerepkör, amely jóváhagyásra van szüksége a két Azure AD és az Azure RBAC-szerepkörökhöz.
 
További információkért lásd: [Privileged Identity Management (előzetes verzió) Azure-erőforrások](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---
 
## <a name="november-2017"></a>2017. november
 
### <a name="access-control-service-retirement"></a>Access Control szolgáltatás kivonása

**Típusa** Tervezett módosítás  
**Szolgáltatás kategóriája:** Access Control szolgáltatás  
**A termék képességei:** Access Control szolgáltatás 

Az Azure Active Directory Access Control (más néven a hozzáférés-vezérlés szolgáltatás) kivezetjük késői 2018-ban. Az elkövetkező néhány hét fog adni egy részletes ütemezés és a magas szintű áttelepítési útmutató tartalmaz további információt. Hagyhatja megjegyzéseket ezen az oldalon bármilyen kérdése van, a hozzáférés-vezérlés szolgáltatással kapcsolatos, és a egy csapattag rájuk válaszolni fog.

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>Az Intune Managed Browser böngésző-hozzáférés korlátozása 

**Típusa** Tervezett módosítás  
**Szolgáltatás kategóriája:** Feltételes hozzáférés  
**A termék képességei:** Identitás biztonsága és védelme

Office 365-höz és más Azure AD-hez kapcsolódó felhőalapú alkalmazáshoz való böngészőalapú hozzáférés egy jóváhagyott alkalmazáshoz, az Intune Managed Browser használatával korlátozhatja. 

Mostantól a következő feltételt állíthatja be az alkalmazás alapú feltételes hozzáféréshez:

**Ügyfélalkalmazások:** Browser

**Mi az a változás hatását?**

Használja ezt az állapotot még ma, hozzáférés le lesz tiltva. Az előzetes verzió érhető el, ha minden hozzáférést, a managed browser alkalmazást használatára lesz szükség. 

Keresse meg ezt a funkciót, és a közelgő blogok és kibocsátási megjegyzések további információt. 

További információ: [feltételes hozzáférés az Azure ad-ben](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal).
 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Új jóváhagyott ügyfélalkalmazások az Azure AD alkalmazás-alapú feltételes hozzáféréshez

**Típusa** Tervezett módosítás  
**Szolgáltatás kategóriája:** Feltételes hozzáférés  
**A termék képességei:** Identitás biztonsága és védelme

A következő alkalmazások olyan listájában [jóváhagyott ügyfélalkalmazások](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement):

- [A Microsoft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/)
- Microsoft StaffHub

További információkért lásd:

- [Jóváhagyott alkalmazás megkövetelése](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Azure AD-alkalmazás-alapú feltételes hozzáférés](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="terms-of-use-support-for-multiple-languages"></a>Használati feltételek több nyelv támogatása

**Típusa** Új funkció    
**Szolgáltatás kategóriája:** Használati feltételek  
**A termék képességei:** Megfelelőség

A rendszergazdák most már hozhat létre új használati feltételek, amelyek több PDF-dokumentumok tartalmazzák. Megjelölheti a PDF-dokumentumok egy megfelelő nyelvet. Felhasználók preferenciáik alapján megfelelő nyelven jelennek meg a PDF-fájlt. Ha nem szerepel, az alapértelmezett nyelv jelenik meg.

---
 
### <a name="real-time-password-writeback-client-status"></a>Valós idejű jelszó-visszaírási ügyfél állapotát

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Új jelszó önkiszolgáló kérése  
**A termék képességei:** Felhasználóhitelesítés

Most már megtekintheti a helyi jelszó-visszaírási ügyfél állapotát. Ez a beállítás érhető el a **a helyszíni integrációs** szakaszában a [új jelszó kérésére vonatkozó](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset) lapot. 

Probléma merül fel a helyszíni visszaírási ügyfélhez a kapcsolattal rendelkező, lásd: egy hibaüzenet, amely a következőket tartalmazza:

- Miért nem lehet kapcsolódni a helyszíni visszaírási ügyfélhez információk.
- Dokumentáció, amely segítséget nyújt a problémák elhárításához mutató hivatkozást. 

További információkért lásd: [a helyszíni integrációs](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-how-it-works#on-premises-integration).

---

### <a name="azure-ad-app-based-conditional-access"></a>Azure AD-alkalmazás-alapú feltételes hozzáférés 
 
**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Azure AD  
**A termék képességei:** Identitás biztonsága és védelme

Mostantól az [Azure ad-alapú feltételes hozzáférés](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)használatával korlátozhatja az Office 365 és más Azure ad-hez kapcsolódó felhőalapú alkalmazások elérését az Intune app Protection-szabályzatokat támogató [jóváhagyott ügyfélalkalmazások](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement) számára. Az Intune alkalmazásvédelmi szabályzatai segítségével konfigurálhatja, és ezek ügyfélalkalmazások számára a vállalati adatok védelmét.

Az eszközökön alapuló feltételes [](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications) hozzáférési szabályzatok [alkalmazásának](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) kombinálásával rugalmasan kezelheti a személyes és a vállalati eszközökre vonatkozó adatvédelmet.

Az alábbi feltételek és vezérlők mostantól használhatók az alkalmazás-alapú feltételes hozzáféréssel:

**Támogatott platform feltétel**

- iOS
- Android

**Ügyfél alkalmazások feltétel**

- Mobilalkalmazások és asztali ügyfelek

**Hozzáférés-vezérlés**

- Jóváhagyott ügyfélalkalmazás megkövetelése

További információ: [Azure ad alkalmazás-alapú feltételes hozzáférés](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access).
 
---

### <a name="manage-azure-ad-devices-in-the-azure-portal"></a>Az Azure Portalon az Azure AD-eszközök kezelése

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Eszközök regisztrációja és kezelése  
**A termék képességei:** Identitás biztonsága és védelme

Most már megtalálhatja az Azure ad-hez csatlakoztatott eszközök és a egy helyen az eszközzel kapcsolatos tevékenységeket. Nincs új felügyeleti felület kezeli az eszközidentitások és a beállítások az Azure Portalon. Ebben a kiadásban a következőket teheti:

- Az Azure AD-ben feltételes hozzáféréshez elérhető összes eszköz megtekintése.
- Tekintse meg a tulajdonságokat, többek között a hibrid Azure AD-hez csatlakoztatott eszközök.
- Keresse meg a BitLocker-kulcsok az Azure AD-hez csatlakoztatott eszközök, az eszköz Intune-nal, és további kezelését.
- Az Azure AD-eszközzel kapcsolatos beállítások kezelése.

További információkért lásd: [eszközök kezelése az Azure portal használatával](https://docs.microsoft.com/azure/active-directory/device-management-azure-portal).

---

### <a name="support-for-macos-as-a-device-platform-for-azure-ad-conditional-access"></a>Támogatás macOS platformként az Azure AD feltételes hozzáféréshez 

**Típusa** Új funkció    
**Szolgáltatás kategóriája:** Feltételes hozzáférés  
**A termék képességei:** Identitás biztonsága és védelme 

Mostantól belefoglalhatja (vagy kizárhatja) a macOS-t az Azure AD feltételes hozzáférési szabályzatában található eszköz-platform feltételként. MacOS-gépeken, a támogatott eszközplatformok igény szerinti hozzáadásával a következőket teheti:

- **Regisztrálhat és kezelhet a macOS-eszközök Intune-nal.** Más platformokon, például iOS és Android rendszerhez hasonló, a vállalati portál alkalmazás érhető el a macOS-hez egységes regisztrációk ehhez. Az új céges portál alkalmazás macOS-hez készült segítségével egy eszközöm Intune-nal, és regisztrálja az Azure ad-ben.
- **Győződjön meg arról, macOS-eszközök a vállalat megfelelőségi szabályzatok az Intune-ban definiált formátumhoz.** Az Intune-ban az Azure Portalon mostantól beállíthat is macOS-eszközök megfelelőségi szabályzatainak. 
- **Alkalmazásokhoz való hozzáférés korlátozása csak a megfelelő MacOS rendszerű eszközökhöz az Azure AD-ben.** A feltételes hozzáférési szabályzat létrehozásához a macOS-t külön eszköz platformként kell megválasztani. Most már készíthet macOS-specifikus feltételes hozzáférési szabályzatokat az Azure-beli megcélzott alkalmazás számára.

További információkért lásd:

- [Megfelelőségi szabályzat létrehozása MacOS-eszközökhöz az Intune-nal](https://aka.ms/macoscompliancepolicy)
- [Feltételes hozzáférés az Azure AD-ben](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)
 
---

### <a name="network-policy-server-extension-for-azure-multi-factor-authentication"></a>Hálózati házirend-kiszolgáló kiterjesztése az Azure multi-factor Authentication 

**Típusa** Új funkció    
**Szolgáltatás kategóriája:**  Többtényezős hitelesítés  
**A termék képességei:** Felhasználóhitelesítés

A hálózati házirend-kiszolgáló bővítmény, az Azure multi-factor Authentication a hitelesítési infrastruktúra felhőalapú multi-factor Authentication funkcióinak hozzáadja a meglévő kiszolgálók használatával. A hálózati házirend-kiszolgáló kiterjesztésű telefonhívás, szöveges üzenet vagy telefonos alkalmazás ellenőrzés adhat a meglévő hitelesítési folyamatot. Nem kell telepítése, konfigurálása és új kiszolgálók karbantartása. 

Ez a bővítmény olyan szervezeteknek, amelyek számára védelmet kíván biztosítani virtuális magánhálózati kapcsolatok az Azure multi-factor Authentication-kiszolgáló üzembe helyezése nélkül lett létrehozva. A hálózati házirend-kiszolgáló bővítmény úgy működik, mint az adapter beállításai között, adja meg a hitelesítés második tényezőjét, a RADIUS- és felhőalapú Azure multi-factor Authentication összevont, vagy a szinkronizált felhasználók.

További információkért lásd: [a meglévő hálózati házirend-kiszolgáló infrastruktúra integrálása az Azure multi-factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-nps-extension).
 
---

### <a name="restore-or-permanently-remove-deleted-users"></a>Állítsa vissza vagy végleg eltávolítani a törölt felhasználók

**Típusa** Új funkció    
**Szolgáltatás kategóriája:** Felhasználókezelés  
**A termék képességei:** Címtár 

Az Azure AD felügyeleti központban az alábbi műveleteket hajthatja végre:

- Törölt felhasználói visszaállítása. 
- Felhasználó végleges törlése.

**Kipróbálás:**

1. Az Azure AD felügyeleti központban válassza [minden felhasználó](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All) a a **kezelés** szakaszban. 

2. Az a **megjelenítése** listáról válassza ki **nemrégiben törölt felhasználók**. 

3. Válassza ki egy vagy több nemrégiben törölt felhasználók, és vagy visszaállíthatja a őket, vagy véglegesen törli azokat.
 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Új jóváhagyott ügyfélalkalmazások az Azure AD alkalmazás-alapú feltételes hozzáféréshez
 
**Típusa** Megváltozott funkció  
**Szolgáltatás kategóriája:** Feltételes hozzáférés  
**A termék képességei:** Identitás biztonsága és védelme

A következő alkalmazások listájának hozzáadott [jóváhagyott ügyfélalkalmazások](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement):

- A Microsoft Planner
- Azure Information Protection 

További információkért lásd:

- [Jóváhagyott alkalmazás megkövetelése](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Azure AD-alkalmazás-alapú feltételes hozzáférés](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="use-or-between-controls-in-a-conditional-access-policy"></a>Feltételes hozzáférési szabályzatban található vezérlők közötti "vagy" használata 

**Típusa** Megváltozott funkció    
**Szolgáltatás kategóriája:** Feltételes hozzáférés  
**A termék képességei:** Identitás biztonsága és védelme
 
Most már használhatja a "vagy a" (a kijelölt vezérlők egyikének megkövetelése) használatát a feltételes hozzáférés-vezérléshez. Ez a funkció segítségével szabályzatok létrehozása a "vagy" közötti hozzáférés-vezérlést. Ez a funkció segítségével például olyan szabályzatot, amely egy felhasználó jelentkezhet be a multi-factor Authentication szolgáltatás használata "vagy" a megfelelő eszköz lehet szükséges.

További információ: [vezérlők az Azure ad feltételes hozzáférésben](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-controls).
 
---

### <a name="aggregation-of-real-time-risk-events"></a>Valós idejű kockázati események összesítése

**Típusa** Megváltozott funkció    
**Szolgáltatás kategóriája:** Identitásvédelem  
**A termék képességei:** Identitás biztonsága és védelme

Az Azure AD Identity Protection az összes olyan valós idejű kockázati események, amelyek az ugyanazon IP-cím származik az adott napon most már az egyes kockázati esemény összesítjük. Ez a változás bármilyen változás nélkül jelenik meg a felhasználó biztonsági kockázati események mennyiségét korlátozza.

Az alapul szolgáló valós idejű észlelését működik minden alkalommal, amikor a felhasználó bejelentkezik. Ha rendelkezik egy olyan bejelentkezési kockázat-biztonsági házirendet, állítsa be a multi-factor Authentication szolgáltatás blokkolja a hozzáférést, továbbra is aktivált minden kockázatos bejelentkezés során.
 
---
 
## <a name="october-2017"></a>2017. október

### <a name="deprecate-azure-ad-reports"></a>Az Azure AD-jelentések kivezetjük

**Típusa** Tervezett módosítás  
**Szolgáltatás kategóriája:** Jelentéskészítés  
**A termék képességei:** Identitáséletciklus-menedzsment  

Az Azure Portalon a következőket tartalmazza:

- Egy új Azure AD felügyeleti konzolján.
- Új API-k tevékenység és a biztonsági jelentések.
 
Új funkciók miatt a jelentést, a/Reports végpont API-k visszavontuk, 2017. December 10. 

---

### <a name="automatic-sign-in-field-detection"></a>Az automatikus bejelentkezési mezők

**Típusa** Rögzített   
**Szolgáltatás kategóriája:** Saját alkalmazások  
**A termék képességei:** Egyszeri bejelentkezés  

Az Azure AD automatikus bejelentkezési mezők egy HTML-felhasználói név és jelszó mezőt megjelenítő alkalmazások esetében támogatja. Ezeket a lépéseket vannak dokumentálva [automatikusan az alkalmazás bejelentkezési mezők rögzítése](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-password-single-sign-on-non-gallery-applications-problems#manually-capture-sign-in-fields-for-an-app). Adja hozzá ezt a képességet talál egy *katalógusban nem szereplő* alkalmazás a a **vállalati alkalmazások** lapját a [az Azure portal](https://aad.portal.azure.com). Ezenkívül konfigurálhatja a **egyszeri bejelentkezés** módban az új alkalmazás a **jelszóalapú egyszeri bejelentkezés**, adjon meg egy webes URL-címet, és mentse az oldal.
 
Ez a funkció egy szolgáltatási hiba miatt ideiglenesen letiltotta. A probléma megoldódott, és az automatikus bejelentkezési mezők észlelési érhető el újra.

---

### <a name="new-multi-factor-authentication-features"></a>A multi-factor Authentication szolgáltatás új funkciók

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Többtényezős hitelesítés  
**A termék képességei:** Identitás biztonsága és védelme  

Többtényezős hitelesítés (MFA), a szervezet védelme fontos részét képezi. Ahhoz, hogy több adaptív hitelesítő adatokat, és a felhasználói élményt a zökkenőmentes, a következő funkciók lettek hozzáadva: 

- Multi-factor Authentication ellenőrző eredményei közvetlenül integrálva vannak az Azure AD bejelentkezési jelentést, amely programozott hozzáférést biztosít a többtényezős hitelesítés eredménye.
- Az MFA konfigurálása az Azure AD-konfigurációjának több mélyen integrált élmény az Azure Portalon.

A nyilvános előzetes verziója, az MFA felügyeleti és jelentéskészítési az alapvető Azure AD-konfigurációs felület integrált részét képezik. Mostantól felügyelheti a MFA felügyeleti portálon funkciókat nyújt az Azure AD lehetőséget.

További információkért lásd: [többtényezős hitelesítés az Azure Portalon jelentéskészítési referenciája](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins-mfa). 

---

### <a name="terms-of-use"></a>Használati feltételek

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Használati feltételek  
**A termék képességei:** Megfelelőség  

Az Azure AD használati feltételek szükséges információkkal, mint például a jogi vagy megfelelőségi követelményekre vonatkozó nyilatkozatok felhasználók is használhatja.

Az Azure AD használati feltételek az alábbiakhoz használhatja:

- A szervezet minden tagjára vonatkozó általános használati
- Specifikus használati feltételek a felhasználói attribútumok (például orvosi nővérek és) vagy a belföldi és nemzetközi alkalmazottak, dinamikus csoportok által végzett alapján
- Specifikus használati feltételek a nagy hatású üzleti alkalmazások, mint például a Salesforce elérése

További információkért lásd: [az Azure AD használati feltételek](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="enhancements-to-privileged-identity-management"></a>Privileged Identity Management fejlesztései

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Privileged Identity Management  
**A termék képességei:** Privileged Identity Management  

Az Azure AD Privileged Identity Management kezelheti, irányíthatja, és a szervezet Azure-erőforrások (előzetes verzió) való hozzáférés figyelése:

- Előfizetések
- Erőforráscsoportok
- Virtual machines (Virtuális gépek) 

Összes erőforrást az Azure Portalon az Azure RBAC-funkciókat használó igénybe veheti a biztonsági és az Azure AD Privileged Identity Management által biztosított lehetőségeket alkalmazásélettartam-kezelési képességgel.

További információkért lásd: [Privileged Identity Management az Azure-erőforrások](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---

### <a name="access-reviews"></a>Hozzáférési felülvizsgálatok

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Hozzáférési felülvizsgálatok  
**A termék képességei:** Megfelelőség  

Szervezetek számára a hozzáférési felülvizsgálatok (előzetes verzió) segítségével hatékonyan kezelheti a csoporttagságokat és a vállalati alkalmazásokhoz való hozzáférést: 

- Az alkalmazásokhoz való hozzáférések és a csoporttagságok felülvizsgálatával újrahitelesítheti a vendégfelhasználói hozzáférést. Felülvizsgálók-e, hogy a vendégek folyamatos hozzáférést a hozzáférési felülvizsgálatok által nyújtott alapján eldöntését is.
- A hozzáférési felülvizsgálatok segítségével az alkalmazottak alkalmazás-hozzáférését és csoporttagságait is újból hitelesítheti.

A hozzáférési felülvizsgálati vezérlőket cég- vagy szervezetspecifikus programokban is összegyűjtheti a megfelelőség vagy a kockázatérzékeny alkalmazások felülvizsgálatának nyomon követése céljából.

További információkért lásd: [az Azure AD hozzáférési felülvizsgálatok](https://docs.microsoft.com/azure/active-directory/active-directory-azure-ad-controls-access-reviews-overview).

---

### <a name="hide-third-party-applications-from-my-apps-and-the-office-365-app-launcher"></a>Saját alkalmazások és az Office 365 appindítóján a külső alkalmazások elrejtése

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Saját alkalmazások  
**A termék képességei:** Egyszeri bejelentkezés  

Most már jobban kezelhető alkalmazásokat, amelyeket a felhasználók portálokon keresztül egy új megjelenni **alkalmazás elrejtése** tulajdonság. Azokban az esetekben, ahol a Háttérszolgáltatásokhoz vagy ismétlődő csempék és legyen zsúfolt felhasználók alkalmazás kilövők megjelenni alkalmazáscsempék alkalmazások elrejtéséhez. A váltógomb ki van a **tulajdonságok** szakaszában a harmadik féltől származó alkalmazások és feliratú **felhasználó számára látható?** Egy alkalmazás programozás útján PowerShell-lel is elrejthetők. 

További információkért lásd: [egy külső alkalmazás az Azure ad-ben a felhasználói élmény elrejtése](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app). 


**Mi érhető el?**

 Az új felügyeleti konzol, a két új API-kkal való áttérés részeként az Azure Active Directory érhetők el naplók. Az új API-készletet biztosít gazdagabb szűrési és rendezési funkció részletesebb naplózás és a bejelentkezési tevékenységek biztosítása mellett. Az adatok korábban már elérhető a biztonsági jelentések mostantól elérhetők az Identity Protection kockázati események API-k a Microsoft Graph.


## <a name="september-2017"></a>2017. szeptember

### <a name="hotfix-for-identity-manager"></a>A gyorsjavítás az Identity Managerhez

**Típusa** Megváltozott funkció  
**Szolgáltatás kategóriája:** Identity Manager  
**A termék képességei:** Identitás-életciklus kezelése  

2017. szeptember 25, az Identity Manager 2016 Service Pack 1 (build 4.4.1642.0) gyorsjavítás kumulatív csomag érhető el. Ez a kumulatív csomag:

- Oldja fel a problémákat, és fejlesztéseket.
- Az összesítő frissítés, amely lecseréli az összes Identity Manager 2016 Service Pack 1 frissítés akár build 4.4.1459.0 Identity Manager 2016. 
- Megköveteli, hogy rendelkezik az Identity Manager 2016 4.4.1302.0 hozhat létre. 

További információkért lásd: [gyorsjavítás frissítésgyűjtemény (build 4.4.1642.0) érhető el az Identity Manager 2016 Service Pack 1](https://support.microsoft.com/help/4021562). 

---
