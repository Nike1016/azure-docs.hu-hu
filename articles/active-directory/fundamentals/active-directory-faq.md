---
title: Gyakori kérdések (GYIK) – az Azure Active Directory |} A Microsoft Docs
description: Gyakori kérdések és válaszok az Azure és az Azure Active Directory, jelszókezelés és alkalmazás-hozzáférés.
services: active-directory
author: msaburnley
manager: daveba
ms.assetid: b8207760-9714-4871-93d5-f9893de31c8f
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c1ee5e849d8004f828a2d92d728ad7925fc05c4
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68693953"
---
# <a name="frequently-asked-questions-about-azure-active-directory"></a>Azure Active Directoryval kapcsolatos gyakori kérdések
Az Azure Active Directory (Azure AD) egy átfogó szolgáltatott identitási (IDaaS) megoldás, amely az identitások, a hozzáférés-kezelés és a biztonság minden szempontját lefedi.

További információkért lásd: [Mi az az Azure Active Directory?](active-directory-whatis.md).


## <a name="access-azure-and-azure-active-directory"></a>Az Azure és az Azure Active Directory elérése
**K: Miért kapok "nem találhatók előfizetések" kifejezést, amikor megpróbálom elérni az Azure AD-t a Azure Portal?**

**V:** A Azure Portal eléréséhez minden felhasználónak rendelkeznie kell egy Azure-előfizetéssel. Ha fizetős Office 365- vagy Azure AD előfizetéssel rendelkezik, látogasson el a [https://aka.ms/accessAAD](https://aka.ms/accessAAD) címre egy egyszeri aktiváláshoz. Egyébként aktiválnia kell egy ingyenes [Azure-fiókot](https://azure.microsoft.com/pricing/free-trial/) vagy egy fizetős előfizetést.

További információkért lásd:

* [How Azure subscriptions are associated with Active Directory? (Hogyan kapcsolódnak az Azure-előfizetések az Azure Active Directory-hoz?)](active-directory-how-subscriptions-associated-directory.md)

---
**K: Mi a kapcsolat az Azure AD, az Office 365 és az Azure között?**

**V:** Az Azure AD közös identitás-és hozzáférési képességeket biztosít az összes webszolgáltatáshoz. Akár az Office 365, a Microsoft Azure, az Intune vagy más rendszert használ, már használja az Azure AD-t a bejelentkezés és a hozzáférés-kezelés bekapcsolásához ezen szolgáltatások mindegyikéhez.

A webes szolgáltatások használatához beállított összes felhasználó felhasználói fiókként van meghatározva egy vagy több Azure AD-példányban. Beállíthatja ezeket a fiókokat az ingyenes Azure AD-képességekhez, például a felhőalkalmazások eléréséhez.

A fizetős Azure AD szolgáltatások, például az Enterprise Mobility + Security, átfogó vállalati méretű felügyeleti és biztonsági megoldásokkal egészítenek ki más webes szolgáltatásokat, például az Office 365-öt és a Microsoft Azure-t.

---

**K:  Mi a különbség a tulajdonos és a globális rendszergazda között?**

**V:** Alapértelmezés szerint az Azure-előfizetésre feliratkozott személy a tulajdonos szerepkört rendeli hozzá az Azure-erőforrásokhoz. Egy olyan tulajdonost Microsoft-fiók vagy a címtárból, amelyhez az Azure-előfizetés társítva van egy munkahelyi vagy iskolai fiókot használhat.  Ez a szerepkör jogosult szolgáltatások kezelésére az Azure Portalon.

Ha másoknak is kell bejelentkezniük és elérniük a szolgáltatásokat az ugyanahhoz az előfizetéshez, hozzárendelheti azokat a megfelelő [beépített szerepkör](../../role-based-access-control/built-in-roles.md). További információkért lásd: [rbac-RÓL és az Azure portal-hozzáférés kezelése](../../role-based-access-control/role-assignments-portal.md).

Alapértelmezés szerint az Azure-előfizetésre feliratkozó személy van rendelve a címtár globális rendszergazdai szerepkörrel. A globális rendszergazda az összes Azure AD-címtár funkciók hozzáféréssel rendelkezik. Az Azure AD különböző rendszergazdai szerepköröket a címtárral és az identitással kapcsolatos funkciók kezeléséhez rendelkezik. Ezek a rendszergazdák különböző funkciókhoz is hozzáférést kap az Azure Portalon. A rendszergazda szerepkör meghatározza, hogy mit tehet, például a létrehozása vagy szerkesztése a felhasználók, rendszergazdai szerepköröket hozzárendelni más, felhasználók új jelszavainak létrehozására, felhasználói licencek kezelése vagy tartományok kezelése.  További információ az Azure AD címtárrendszergazdáival és azok szerepköreivel: [felhasználó hozzárendelése az Azure Active Directory rendszergazdai szerepkörök](active-directory-users-assign-role-azure-portal.md) és [rendszergazdai szerepkörök hozzárendelése az Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md).

Ezenkívül a fizetős Azure AD szolgáltatások, például az Enterprise Mobility + Security, átfogó vállalati méretű felügyeleti és biztonsági megoldásokkal egészítenek ki más webes szolgáltatásokat, például az Office 365-öt és a Microsoft Azure-t.

---
**K: Van olyan jelentés, amely azt mutatja be, hogy mikor jár le az Azure AD felhasználói licencei?**

**V:** Nem.  Ez jelenleg nem érhető el.

---

## <a name="get-started-with-hybrid-azure-ad"></a>Első lépések a Hybrid Azure AD-ben


**K: Hogyan hagy egy bérlőt, ha közreműködőként vagyok felvéve?**

**V:** Ha egy másik szervezet bérlője számára ad hozzá közreműködőként, a jobb felső sarokban lévő "bérlői kapcsoló" használatával válthat a bérlők között.  Jelenleg nem hagyhatja el a meghívó szervezetet, és a Microsoft dolgozik ezen a funkción.  A funkció eléréséig megkérheti a meghívó szervezetet, hogy távolítsa el Önt a bérlők közül.

---
**K: Hogyan kapcsolhatom össze a helyszíni címtárat az Azure AD-vel?**

**V:** A helyszíni címtárat Azure AD Connect használatával is összekapcsolhatja az Azure AD-vel.

További információkért lásd: [Helyszíni identitások integrálása az Azure Active Directoryval](../hybrid/whatis-hybrid-identity.md).

---
**K: Hogyan beállítani a helyszíni címtár és a Felhőbeli alkalmazások közötti egyszeri bejelentkezést?**

**V:** Csak egyszeri bejelentkezést (SSO) kell beállítania a helyszíni címtár és az Azure AD között. Amíg az Azure AD-n keresztül éri el a felhőalkalmazásokat, a szolgáltatás automatikusan átirányítja a felhasználókat, hogy megfelelően hitelesíteni tudják magukat a helyszíni hitelesítő adataikkal.

Az egyszeri bejelentkezés helyszínről végzett implementálása könnyen elérhető olyan összevonási megoldásokkal, mint az Active Directory összevonási szolgáltatások (AD FS), illetve a jelszókivonat-szinkronizálás konfigurálásával. Könnyedén telepítheti mindkét lehetőséget az Azure AD Connect konfigurációs varázslójával.

További információkért lásd: [Helyszíni identitások integrálása az Azure Active Directoryval](../hybrid/whatis-hybrid-identity.md).

---
**K: Az Azure AD önkiszolgáló portált biztosít a szervezeten belüli felhasználók számára?**

**V:** Igen, az Azure AD biztosítja az [Azure ad hozzáférési paneljét](https://myapps.microsoft.com) a felhasználói önkiszolgáló és az alkalmazásokhoz való hozzáféréshez. Ha Ön Office 365-ügyfél, az [office 365](https://portal.office.com)-portálon számos különböző funkció közül választhat.

További információkért lásd: [Bevezetés a Hozzáférési panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

---
**K: Segít az Azure AD a helyszíni infrastruktúrám kezelésében?**

**V:** Igen. Az Azure AD Premium Edition tartalmazza az Azure AD Connect Health eszközt. Az Azure AD Connect Health segít megfigyelni a helyszíni identitás-infrastruktúrát és a szinkronizálási szolgáltatásokat, és további betekintést nyújt a működésükbe.  

További információkért lásd: [A helyszíni identitás-infrastruktúra és a szinkronizálási szolgáltatások megfigyelése a felhőben](../hybrid/whatis-hybrid-identity-health.md).  

---
## <a name="password-management"></a>Jelszókezelés
**K: Használhatom az Azure AD-jelszó visszaírását jelszó-szinkronizálás nélkül? (Ebben az esetben használhatom az Azure AD önkiszolgáló jelszó-visszaállítást (SSP) jelszóvisszaírással úgy, hogy nem a felhőben tárolom a jelszavaimat?)**

**V:** A Active Directory jelszavakat nem kell szinkronizálnia az Azure AD-vel a visszaírás engedélyezéséhez. Összevont környezetben az Azure AD egyszeri bejelentkezés (SSO) a helyszíni címtárra támaszkodik a felhasználó hitelesítéséhez. Ehhez a forgatókönyvhöz nem kell nyomon követni a helyszíni jelszót az Azure AD-ben.

---
**K: Mennyi időt vesz igénybe a jelszavak visszaírása Active Directory a helyszínen?**

**V:** A jelszavak visszaírása valós időben működik.

További részletekért lásd: [A jelszókezelés első lépései](../authentication/quickstart-sspr.md).

---
**K: Használhatok jelszó-visszaírást a rendszergazda által kezelt jelszavakkal?**

**V:** Igen, ha engedélyezve van a jelszó-visszaírás, a rendszergazda által végrehajtott jelszó-műveleteket a helyszíni környezetbe kell írni.  

<a name="for-more-answers-to-password-related-questions-see-password-management-frequently-asked-questionsauthenticationactive-directory-passwords-faqmd"></a>A jelszavakkal kapcsolatos kérdésekre adott további válaszokért lásd: [Jelszókezelés – gyakori kérdések](../authentication/active-directory-passwords-faq.md).
---
**K:  Mi a teendő, ha nem tudom megjegyezni a meglévő Office 365/Azure AD-jelszót, miközben megpróbálom megváltoztatni a jelszavát?**

**V:** Ilyen esetben néhány lehetőség közül választhat.  Használjon önkiszolgáló jelszó-visszaállítást (SSPR), ha elérhető.  Az SSPR konfigurációjától függ, hogy működik-e.  További információkért lásd: [A jelszó-visszaállítási portál működése](../authentication/howto-sspr-deployment.md).

Office 365-felhasználók esetében a rendszergazda alaphelyzetbe állíthatja a jelszót a [Felhasználói jelszavak átállítása](https://support.office.com/article/Admins-Reset-user-passwords-7A5D073B-7FAE-4AA5-8F96-9ECD041ABA9C?ui=en-US&rs=en-US&ad=US) szakaszban ismertetett lépések segítségével.

Azure AD-felhasználók esetében a rendszergazdák a következő módszerek valamelyikével állíthatják alaphelyzetbe a jelszót:

- [Fiókok alaphelyzetbe állítása az Azure Portalon](active-directory-users-reset-password-azure-portal.md)
- [A PowerShell használata](/powershell/module/msonline/set-msoluserpassword?view=azureadps-1.0)


---
## <a name="security"></a>Biztonság
**K: Zárolva vannak-e a fiókok egy adott számú sikertelen kísérlet után, vagy van-e kifinomultabb stratégiát használni?**

A fiókok zárolásához ennél kifinomultabb stratégiát alkalmazunk.  A zárolás a kérés IP-címe és a megadott jelszavak alapján történik. A zárolás időtartama annál hosszabb, minél inkább valószínűsíthető, hogy támadásról van szó.  

**K:  Bizonyos (gyakori) jelszavak elutasítása a (z) "Ez a jelszó többször használatos" üzenettel történik, ez a jelenlegi Active Directoryban használt jelszavakra vonatkozik?**

Ez a visszautasítás a világszerte gyakran használt jelszavakra, például a „Jelszó” (Password) és a „123456” karakterláncok különböző változataira vonatkozik.

**K: Le kell tiltani a kétes forrásokból származó bejelentkezési kéréseket (botnetek, Tor-végpontok) a B2C-bérlőben, vagy ehhez alapszintű vagy prémium kiadású bérlő szükséges?**

Az átjárónk szűri a kéréseket és bizonyos fokú védelmet biztosít a botnetek ellen. Ez minden B2C-bérlőre vonatkozik.

## <a name="application-access"></a>Alkalmazás-hozzáférés

**K: Hol találhatók az Azure AD-vel előre integrált alkalmazások és azok képességei?**

**V:** Az Azure AD több mint 2 600 előre integrált alkalmazást biztosít a Microsoft, az Application Service Providers és a partnerek számára. Mindegyik előre integrált alkalmazás támogatja az egyszeri bejelentkezést (SSO). Az egyszeri bejelentkezéssel a szervezeti hitelesítő adatokkal érheti el az alkalmazásokat. Néhány alkalmazás az automatikus üzembe helyezést és megszüntetést is támogatja.

Az előre integrált alkalmazások teljes listájáért lásd: [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/).

---
**K: Mi a teendő, ha a szükséges alkalmazás nem szerepel az Azure AD piactéren?**

**V:** A prémium szintű Azure AD használatával bármilyen alkalmazást hozzáadhat és konfigurálhat. Az alkalmazás képességeitől és a beállításaitól függően konfigurálhat egyszeri bejelentkezést és automatikus üzembe helyezést.  

További információkért lásd:

* [Egyszeri bejelentkezés konfigurálása az Azure Active Directory alkalmazáskatalógusában nem szereplő alkalmazásokhoz](../manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
* [SCIM használata a felhasználók és csoportok automatikus üzembe helyezésének engedélyezéséhez az Azure Active Directoryból az alkalmazásokba](../manage-apps/use-scim-to-provision-users-and-groups.md)

---
**K: Hogyan jelentkezhetnek be a felhasználók az alkalmazásokba az Azure AD használatával?**

**V:** Az Azure AD számos módszert biztosít a felhasználók számára az alkalmazások megtekintésére és elérésére, például:

* Azure AD Hozzáférési panel
* Office 365 alkalmazásindító
* Közvetlen bejelentkezés az összevont alkalmazásokba
* Mélyhivatkozások az összevont, jelszóalapú vagy meglévő alkalmazásokhoz

További információkért lásd: [alkalmazások felhasználói élményének](../manage-apps/end-user-experiences.md).

---
**K: Milyen különböző módokon teszi lehetővé az Azure AD a hitelesítést és az egyszeri bejelentkezést az alkalmazásokba?**

**V:** Az Azure AD számos szabványos protokollt támogat a hitelesítéshez és az engedélyezéshez, mint például az SAML 2,0, az OpenID Connect, a OAuth 2,0 és a WS-Federation. Az Azure AD a jelszótárolást és az automatikus bejelentkezési képességeket is támogatja olyan alkalmazásoknál, amelyek csak az űrlapalapú hitelesítést támogatják.  

További információkért lásd:

* [Hitelesítési forgatókönyvek az Azure AD-hez](../develop/authentication-scenarios.md)
* [Az Active Directory hitelesítési protokolljai](https://msdn.microsoft.com/library/azure/dn151124.aspx)
* [Egyszeri bejelentkezés az Azure AD-alkalmazások](../manage-apps/what-is-single-sign-on.md)

---
**K: A helyszínen futó alkalmazásokat is Hozzáadhatok?**

**V:** Az Azure AD Application Proxy egyszerű és biztonságos hozzáférést biztosít a kiválasztott helyszíni webalkalmazásokhoz. Ezeket az alkalmazásokat ugyanúgy érheti el, ahogyan a szolgáltatott szoftverek (SaaS) alkalmazásokat az Azure AD-ben. Nincs szükség VPN-re vagy a hálózati infrastruktúra módosítására.  

További információért lásd: [Helyszíni alkalmazások biztonságos távoli elérése](../manage-apps/application-proxy.md).

---
**K: Hogyan a többtényezős hitelesítés megkövetelése az adott alkalmazáshoz hozzáférő felhasználók számára?**

**V:** Az Azure AD feltételes hozzáférésével minden alkalmazáshoz hozzárendelhet egyedi hozzáférési szabályzatot. A házirendjében mindig megkövetelheti a többtényezős hitelesítést, vagy csak akkor, amikor a felhasználók nem csatlakoznak a helyi hálózathoz.  

További információkért lásd: [Az Office 365 és az Azure Active Directoryhoz csatlakoztatott egyéb alkalmazások hozzáférésének biztonságossá tétele](../active-directory-conditional-access-azure-portal.md).

---
**K: Mi az az automatizált felhasználói kiépítés SaaS-alkalmazásokhoz?**

**V:** Az Azure AD segítségével automatizálhatja a felhasználói identitások létrehozását, karbantartását és eltávolítását számos népszerű felhőalapú SaaS-alkalmazásban.

További információ: [A felhasználókiépítés és -megszüntetés automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../manage-apps/user-provisioning.md).

---
**K:  Beállíthat biztonságos LDAP-kapcsolatokat az Azure AD-vel?**

**V:**  Nem. Az Azure AD nem támogatja a Lightweight Directory Access Protocol (LDAP) protokollt vagy Secure LDAP közvetlenül. Az Azure AD-bérlőn azonban lehetőség van arra, hogy a megfelelő módon konfigurált hálózati biztonsági csoportokkal engedélyezze az Azure-beli Azure AD Domain Services (Azure AD DS) példányt az LDAP-kapcsolat eléréséhez. További információ: https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.
