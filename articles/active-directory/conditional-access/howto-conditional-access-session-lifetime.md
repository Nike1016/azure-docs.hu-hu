---
title: A hitelesítési munkamenetek kezelésének konfigurálása Azure Active Directory feltételes hozzáféréssel
description: Az Azure AD-alapú hitelesítési munkamenet konfigurációjának testreszabása, beleértve a felhasználói bejelentkezés gyakoriságát és a böngésző munkamenetének megőrzését.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 05c93c9fe2b34ae3b87c44608cc5c5c8947ecc73
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2019
ms.locfileid: "68499851"
---
# <a name="configure-authentication-session-management-with-conditional-access"></a>A hitelesítési munkamenet felügyeletének konfigurálása feltételes hozzáféréssel

Összetett központi telepítések esetén a szervezeteknek szükségük lehet a hitelesítési munkamenetek korlátozására. Egyes forgatókönyvek például a következők lehetnek:

* Erőforrás-hozzáférés nem felügyelt vagy megosztott eszközről
* Bizalmas adatokhoz való hozzáférés külső hálózatról
* Nagy hatású felhasználók
* Kritikus fontosságú üzleti alkalmazások

A feltételes hozzáférés vezérlőkkel olyan házirendeket hozhat létre, amelyek konkrét használati eseteket céloznak meg a szervezeten belül anélkül, hogy ez befolyásolná az összes felhasználót.

Mielőtt megkezdené a szabályzat konfigurálásának részleteit, vizsgáljuk meg az alapértelmezett konfigurációt.

## <a name="user-sign-in-frequency"></a>Felhasználói bejelentkezés gyakorisága

A bejelentkezési gyakoriság határozza meg azt az időtartamot, ameddig a felhasználónak újra be kell jelentkeznie, amikor egy erőforráshoz próbál hozzáférni.

A felhasználói bejelentkezési gyakorisághoz tartozó Azure Active Directory (Azure AD) alapértelmezett konfigurációja a 90 napos gördülő ablak. A felhasználók a hitelesítő adatokkal való megkérdezése gyakran úgy tűnik, mint egy értelmes dolog, de nem sül el: a hitelesítő adatok megadására betanított felhasználók szándékosan nem tudják megadni őket rosszindulatú hitelesítő adatok megadásához.

Előfordulhat, hogy a felhasználók nem kérik a felhasználót, hogy 90 napig újra bejelentkezzenek, a valóságban az IT-szabályzatok megszegése visszavonja a munkamenetet. Néhány példa a jelszó módosítására, a nem megfelelő eszközre vagy a fiók letiltására vonatkozik. Explicit módon [visszavonhatja a felhasználói munkameneteket a PowerShell használatával](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0). Az Azure AD alapértelmezett konfigurációja a "ne kérdezze meg a felhasználókat, hogy adja meg a hitelesítő adataikat, ha a munkamenetek biztonsági helyzete nem változott".

A bejelentkezési gyakoriság beállítása olyan alkalmazásokkal működik, amelyek a szabványok alapján OAUTH2 vagy OIDC protokollokat implementáltak. A Microsoft natív alkalmazásai (Windows, Mac és Mobile) megfelelnek a beállításnak.

## <a name="persistence-of-browsing-sessions"></a>Böngészési munkamenetek megőrzése

Az állandó böngésző-munkamenet lehetővé teszi a felhasználók számára, hogy a böngészőablak bezárása és újbóli megnyitása után is bejelentkezve maradjanak.

Az Azure AD alapértelmezett böngésző-munkamenetének megőrzése lehetővé teszi, hogy a személyes eszközökön lévő felhasználók kiválaszthatják, hogy a "Stay bejelentkezett?" kifejezéssel megmaradnak-e a munkamenetek. sikeres hitelesítés után Rákérdezés. Ha a böngésző adatmegőrzési funkciója be van állítva AD FS az egyszeri [bejelentkezési beállítások](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-single-sign-on-settings#enable-psso-for-office-365-users-to-access-sharepoint-online
)AD FS cikk útmutatása alapján, akkor a szabályzatnak meg kell felelnie, és az Azure ad-munkamenetet is meg kell őriznie. Azt is beállíthatja, hogy a bérlő felhasználói lássák-e a "Stay bejelentkezve?" című részt. az [Azure ad bejelentkezési oldal testreszabása](../fundamentals/customize-branding.md)című cikk útmutatását követve megváltoztathatja a megfelelő beállítást a vállalati védjegyezés panelen Azure Portal.

## <a name="configuring-authentication-session-controls"></a>A hitelesítési munkamenet vezérlőinek konfigurálása

A feltételes hozzáférés prémium szintű Azure AD képesség, és prémium szintű licencre van szükség. Ha többet szeretne megtudni a feltételes hozzáférésről, tekintse meg a [Mi a feltételes hozzáférés Azure Active Directory?](overview.md#license-requirements) című témakört.

> [!WARNING]
> Ha a [konfigurálható jogkivonat élettartama](../develop/active-directory-configurable-token-lifetimes.md) funkciót jelenleg nyilvános előzetes verzióban használja, vegye figyelembe, hogy nem támogatott két különböző szabályzat létrehozása ugyanahhoz a felhasználóhoz vagy alkalmazás-kombinációhoz: egyet ezzel a szolgáltatással, és egy másikat konfigurálható jogkivonat használatával. élettartam funkció. A Microsoft azt tervezi, hogy november 1-jén kivonja a konfigurálható jogkivonat élettartama funkciót, és lecseréli a feltételes hozzáférés hitelesítési munkamenet-kezelési funkciójával.  

### <a name="policy-1-sign-in-frequency-control"></a>1\. szabályzat: Bejelentkezés gyakoriságának vezérlése

1. Új szabályzat létrehozása
1. Válassza ki az ügyfél környezetének összes kötelező feltételét, beleértve a felhőalapú alkalmazásokat is.

   > [!NOTE]
   > A legjobb felhasználói élmény érdekében javasoljuk, hogy a kulcs Microsoft Office alkalmazások, például az Exchange Online és a SharePoint Online esetében az egyenlő hitelesítési kérések gyakoriságát adja meg.

1. Nyissa meg a **hozzáférés** > -vezérlési**munkamenetet** , és kattintson a **bejelentkezési gyakoriság** lehetőségre.
1. Adja meg az első szövegmezőben a napok és órák szükséges értékét
1. Válassza ki az **órák** vagy **napok** értékét a legördülő listából
1. Szabályzat mentése

![Bejelentkezési gyakoriságra konfigurált feltételes hozzáférési szabályzat](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-sign-in-frequency.png)

Az Azure AD-ben regisztrált Windows-eszközökön jelentkezzen be az eszközre. Ha például az Office-alkalmazások esetében 24 órán keresztül konfigurálta a bejelentkezés gyakoriságát, akkor az Azure AD-ben regisztrált Windows-eszközökön lévő felhasználók a bejelentkezési gyakoriságra vonatkozó szabályzatot az eszközre való bejelentkezéssel fogják kielégíteni, és az Office-alkalmazások megnyitásakor nem fognak megjelenni.

Ha az azonos böngésző-munkamenetben futó különböző webalkalmazásokhoz eltérő bejelentkezési gyakoriságot állított be, a rendszer mindkét alkalmazásra alkalmazza a legszigorúbb szabályzatot, mivel az azonos böngésző-munkamenetben futó összes alkalmazás egyetlen munkamenet-tokent használ.

### <a name="policy-2-persistent-browser-session"></a>2\. szabályzat: Állandó böngésző-munkamenet

1. Új szabályzat létrehozása
1. Válassza ki az összes szükséges feltételt.

   > [!NOTE]
   > Vegye figyelembe, hogy ehhez a vezérlőhöz a "minden felhőalapú alkalmazás" lehetőséget kell választania feltételként. A böngésző-munkamenet megőrzését a hitelesítési munkamenet tokenje vezérli. A böngésző-munkamenet összes lapja egyetlen munkamenet-jogkivonattal rendelkezik, ezért mindegyiknek meg kell egyeznie az adatmegőrzési állapottal.

1. Nyissa meg a **hozzáférés** > -vezérlési**munkamenetet** , és kattintson az **állandó böngésző munkamenet**
1. Válasszon ki egy értéket a legördülő listából
1. Szabályzat mentése

![Állandó böngészőre konfigurált feltételes hozzáférési szabályzat](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-persistent-browser.png)

> [!NOTE]
> Az Azure AD feltételes hozzáférés állandó böngésző-munkamenet-konfigurációja felülírja a "Stay bejelentkezve?" Ha mindkét szabályzatot konfigurálta, akkor a Azure Portal vállalati védjegyezése ablaktáblán állítsa be ugyanezt a felhasználót.

## <a name="validation"></a>Érvényesítés

A What-if eszköz használatával szimulálhatja a felhasználót a célalkalmazás és más feltételek alapján, hogy a szabályzatot hogyan konfigurálta. A hitelesítési munkamenetek felügyeleti vezérlői az eszköz eredményében jelennek meg.

![Feltételes hozzáférés What If eszköz eredményei](media/howto-conditional-access-session-lifetime/conditional-access-what-if-tool-result.png)

## <a name="policy-deployment"></a>Házirend központi telepítése

Annak ellenőrzéséhez, hogy a házirend a várt módon működik-e, az ajánlott eljárás az, hogy tesztelje azt az éles üzemben való működés előtt. Ideális esetben egy tesztelési bérlő használatával ellenőrizheti, hogy az új szabályzat a kívánt módon működik-e. További információkért tekintse meg a [Azure Active Directory a feltételes hozzáférésre vonatkozó ajánlott eljárásokat](best-practices.md)ismertető cikket.

## <a name="next-steps"></a>További lépések

* Ha tudni szeretné, hogyan kell konfigurálni a feltételes hozzáférési szabályzatot, tekintse meg a többtényezős hitelesítés megkövetelése [adott alkalmazásokhoz Azure Active Directory feltételes hozzáféréssel](app-based-mfa.md)című cikket.
* Ha készen áll a környezet feltételes hozzáférési házirendjeinek konfigurálására, tekintse meg az [ajánlott eljárásokat a feltételes hozzáféréshez Azure Active Directory](best-practices.md).
