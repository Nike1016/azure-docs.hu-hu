---
title: 'Azure AD Connect: Zökkenőmentes egyszeri bejelentkezés – gyors üzembe helyezés | Microsoft Docs'
description: Ez a cikk azt ismerteti, hogyan kezdheti el a Azure Active Directory zökkenőmentes egyszeri bejelentkezést
services: active-directory
keywords: Mi az Azure AD Connect, az Azure AD, az egyszeri bejelentkezéshez szükséges összetevők telepítése Active Directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/16/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87764ed30182dc548fc3a260582174f121e27e24
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68965218"
---
# <a name="azure-active-directory-seamless-single-sign-on-quick-start"></a>Azure Active Directory zökkenőmentes egyszeri bejelentkezés: Gyors üzembe helyezés

## <a name="deploy-seamless-single-sign-on"></a>Zökkenőmentes egyszeri bejelentkezés üzembe helyezése

Azure Active Directory (Azure AD) zökkenőmentes egyszeri bejelentkezés (zökkenőmentes SSO) automatikusan bejelentkezik a felhasználók számára, amikor azok a vállalati hálózathoz csatlakozó vállalati asztali számítógépeken vannak. A zökkenőmentes SSO lehetővé teszi, hogy a felhasználók könnyen hozzáférjenek a felhőalapú alkalmazásokhoz anélkül, hogy további helyszíni összetevőket kellene megadniuk.

A zökkenőmentes egyszeri bejelentkezés telepítéséhez kövesse az alábbi lépéseket.

## <a name="step-1-check-the-prerequisites"></a>1\. lépés: Az előfeltételek ellenőrzése

Győződjön meg arról, hogy a következő előfeltételek vannak érvényben:

* **A Azure ad Connect-kiszolgáló beállítása**: Ha a bejelentkezési módszerként [áteresztő hitelesítést](how-to-connect-pta.md) használ, nincs szükség további előfeltételek ellenőrzésére. Ha a bejelentkezési módszerként [jelszó](how-to-connect-password-hash-synchronization.md) -kivonatolási szinkronizálást használ, és ha tűzfal van Azure ad Connect és az Azure ad között, ügyeljen a következőkre:
   - A Azure AD Connect 1.1.644.0 vagy újabb verzióját használja. 
   - Ha a tűzfal vagy a proxy engedélyezi a DNS-engedélyezést, akkor az a  **\*. msappproxy.net** URL-címekkel létesített kapcsolatokat a 443-es porton keresztül. Ha nem, engedélyezze a hozzáférést az [Azure Datacenter IP-tartományokhoz](https://www.microsoft.com/download/details.aspx?id=41653), amelyek hetente frissülnek. Ez az előfeltétel csak akkor alkalmazható, ha engedélyezi a szolgáltatást. A tényleges felhasználói bejelentkezésekhez nem szükséges.

    >[!NOTE]
    >A 1.1.557.0, a 1.1.558.0, a 1.1.561.0 és a 1.1.614.0 verziók a jelszó-kivonat szinkronizálásával kapcsolatos problémával rendelkeznek. Azure AD Connect Ha _nem_ kívánja használni a jelszó-kivonatolási szinkronizálást az átmenő hitelesítéssel együtt, olvassa el a [Azure ad Connect kibocsátási megjegyzéseit](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#116470) további információért.

* **Támogatott Azure ad Connect topológia használata**: Győződjön meg arról, hogy az [itt](plan-connect-topologies.md)ismertetett Azure ad Connect által támogatott topológiák egyikét használja.

    >[!NOTE]
    >A zökkenőmentes egyszeri bejelentkezés több AD-erdőt is támogat, függetlenül attól, hogy vannak-e közöttük AD-megbízhatósági kapcsolatok.

* **Tartományi rendszergazdai hitelesítő adatok beállítása**: Minden Active Directory erdőhöz tartományi rendszergazdai hitelesítő adatokkal kell rendelkeznie, amely:
    * Azure AD Connect keresztül szinkronizál az Azure AD-vel.
    * Azokat a felhasználókat tartalmazza, akik számára engedélyezni szeretné a zökkenőmentes egyszeri bejelentkezést.
    
* **Modern hitelesítés engedélyezése**: A szolgáltatás működéséhez engedélyeznie kell a [modern hitelesítést](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016) a bérlőn.

* **Használja az Office 365-ügyfelek legújabb verzióit**: Az Office 365-ügyfelekkel (Outlook, Word, Excel és mások) való csendes bejelentkezési élmény érdekében a felhasználóknak a 16.0.8730. xxxx vagy újabb verziókat kell használniuk.

## <a name="step-2-enable-the-feature"></a>2\. lépés: A szolgáltatás engedélyezése

Zökkenőmentes egyszeri bejelentkezés engedélyezése [Azure ad Connecton](whatis-hybrid-identity.md)keresztül.

>[!NOTE]
> Ha Azure AD Connect nem felel meg a követelményeknek, a [PowerShell használatával is engedélyezheti a zökkenőmentes egyszeri bejelentkezést](tshoot-connect-sso.md#manual-reset-of-the-feature) . Akkor használja ezt a beállítást, ha Active Directory erdőben több tartománya van, és szeretné jobban megcélozni azt a tartományt, amelyen engedélyezni szeretné a zökkenőmentes SSO-t.

Ha Azure AD Connect friss telepítését végzi, válassza az [egyéni telepítési útvonalat](how-to-connect-install-custom.md). A **felhasználó bejelentkezési** oldalán jelölje be az **egyszeri bejelentkezés engedélyezése** lehetőséget.

>[!NOTE]
> A beállítás csak akkor érhető el, ha a bejelentkezési módszer a **jelszó kivonatának szinkronizálása** vagy a **továbbított hitelesítés**.

![Azure AD Connect: Felhasználói bejelentkezés](./media/how-to-connect-sso-quick-start/sso8.png)

Ha már rendelkezik Azure AD Connect-telepítéssel, válassza a **felhasználó bejelentkezési** oldalának módosítása Azure ad Connect, majd a **tovább**lehetőséget. Ha Azure AD Connect 1.1.880.0 vagy újabb verziót használ, alapértelmezés szerint az **egyszeri bejelentkezés engedélyezése** lehetőség lesz kiválasztva. Ha a Azure AD Connect régebbi verzióit használja, jelölje be az **egyszeri bejelentkezés engedélyezése** lehetőséget.

![Azure AD Connect: Felhasználói bejelentkezés módosítása](./media/how-to-connect-sso-quick-start/changeusersignin.png)

Folytassa a varázslót egészen addig, amíg az **egyszeri bejelentkezés engedélyezése** lapra nem kerül. Adja meg a tartományi rendszergazdai hitelesítő adatokat minden olyan Active Directory erdőben, amely:

* Azure AD Connect keresztül szinkronizál az Azure AD-vel.
* Azokat a felhasználókat tartalmazza, akik számára engedélyezni szeretné a zökkenőmentes egyszeri bejelentkezést.

A varázsló befejezése után a zökkenőmentes egyszeri bejelentkezés engedélyezve van a bérlőn.

>[!NOTE]
> A tartományi rendszergazdai hitelesítő adatokat a rendszer nem tárolja Azure AD Connect vagy az Azure AD-ben. Ezeket csak a funkció engedélyezésére használják.

Kövesse ezeket az utasításokat annak ellenőrzéséhez, hogy megfelelően engedélyezte-e a zökkenőmentes egyszeri bejelentkezést:

1. Jelentkezzen be a [Azure Active Directory felügyeleti](https://aad.portal.azure.com) központba a bérlő globális rendszergazdai hitelesítő adataival.
2. A bal oldali ablaktáblán válassza a **Azure Active Directory** lehetőséget.
3. Válassza a **Azure ad Connect**lehetőséget.
4. Győződjön meg arról, hogy a **zökkenőmentes egyszeri bejelentkezés** funkció **engedélyezve van**.

![Azure Portal: Azure AD Connect ablaktábla](./media/how-to-connect-sso-quick-start/sso10.png)

>[!IMPORTANT]
> A zökkenőmentes egyszeri bejelentkezés minden egyes ad `AZUREADSSOACC` -erdőben létrehoz egy nevű számítógépfiókot a helyszíni Active Directoryban (ad). Biztonsági `AZUREADSSOACC` okokból szigorúan védeni kell a számítógépfiókot. Csak a Tartománygazdák kezelhetik a számítógépfiókot. Győződjön meg `AZUREADSSOACC` arról, hogy a számítógépfiók Kerberos-delegálása le van tiltva, és a Active Directory egyetlen más fiókja sincs delegálási engedélyekkel a számítógépfiók számára. Tárolja a számítógépfiókot a szervezeti egységben (OU), ahol biztonságos a véletlen törléstől, és csak a Tartománygazdák férhetnek hozzá.

>[!NOTE]
> Ha a pass-The-hash és a hitelesítőadat-lopás mérséklési architektúrát használja a helyszíni környezetben, végezze el a megfelelő módosításokat annak biztosításához `AZUREADSSOACC` , hogy a számítógépfiók ne kerüljön be a karanténba helyezett tárolóba. 

## <a name="step-3-roll-out-the-feature"></a>3\. lépés: A szolgáltatás bevezetése

A zökkenőmentes SSO-t fokozatosan kiderítheti a felhasználók számára az alábbi utasítások alapján. Első lépésként adja hozzá a következő Azure AD URL-címet az összes vagy a kiválasztott felhasználó intranetes zónájának beállításaihoz a Active Directory Csoportházirend használatával:

- `https://autologon.microsoftazuread-sso.com`

Emellett engedélyeznie kell egy intranetes zóna házirend-beállítását, amelynek neve a **frissítés engedélyezése az állapotsoron parancsfájlon** keresztül csoportházirend. 

>[!NOTE]
> Az alábbi utasítások csak az Internet Explorer és a Google Chrome Windows rendszeren működnek (ha a megbízható webhelyek URL-címeinek az Internet Explorerben való megosztását). A következő szakaszban megtudhatja, hogyan állíthatja be a Mozilla Firefoxot és a Google Chrome-ot macOS rendszeren.

### <a name="why-do-you-need-to-modify-users-intranet-zone-settings"></a>Miért kell módosítania a felhasználók intranetes zónájának beállításait?

Alapértelmezés szerint a böngésző automatikusan kiszámítja a megfelelő zónát (Internet vagy intranet) egy adott URL-címről. Például `http://contoso/` az intranet zónához, míg `http://intranet.contoso.com/` az Internet zónához való leképezés (mivel az URL-cím egy pontot tartalmaz). A böngészők nem küldenek Kerberos-jegyeket a Felhőbeli végpontoknak (például az Azure AD URL-címére), kivéve, ha explicit módon hozzáadja az URL-címet a böngésző intranetes zónájához.

A felhasználók intranetes zónájának beállításai kétféleképpen módosíthatók:

| Beállítás | Rendszergazdai megfontolás | Felhasználói élmény |
| --- | --- | --- |
| Csoportházirend | A rendszergazda lezárja az intranetes zóna beállításainak szerkesztését | A felhasználók nem módosíthatják a saját beállításait |
| Csoportházirend-beállítások |  A rendszergazda engedélyezi az intranetes zóna beállításainak szerkesztését | A felhasználók módosíthatják a saját beállításait |

### <a name="group-policy-option---detailed-steps"></a>"Csoportházirend" lehetőség – részletes lépések

1. Nyissa meg az Csoportházirend-felügyeleti szerkesztő eszközt.
2. Szerkessze az egyes felhasználókra alkalmazott csoportházirendet. Ez a példa az **alapértelmezett tartományi házirendet**használja.
3. Tallózás a **felhasználói konfigurációs** > **házirend** > **Felügyeleti sablonok** **Windows-összetevők** **Internet**ExplorerInternet >  >  >  **A Vezérlőpult** **Biztonság lapja.**  >  Ezután válassza **a hely – zóna hozzárendelési lista**lehetőséget.
    ![Egyszeri bejelentkezés](./media/how-to-connect-sso-quick-start/sso6.png)
4. Engedélyezze a szabályzatot, majd adja meg a következő értékeket a párbeszédpanelen:
   - **Érték neve**: A Kerberos-jegyek továbbítására szolgáló Azure AD URL-cím.
   - **Érték** (Adatfeldolgozás): **1** az intranet zónát jelzi.

     Az eredmény így néz ki:

     Érték neve:`https://autologon.microsoftazuread-sso.com`
  
     Érték (adathalmaz): 1

   >[!NOTE]
   > Ha szeretné letiltani egyes felhasználók számára a zökkenőmentes egyszeri bejelentkezést (például ha ezek a felhasználók megosztott kioszkokra jelentkeznek be), állítsa a fenti értékeket **4**értékre. Ez a művelet hozzáadja az Azure AD URL-címét a korlátozott zónához, és minden alkalommal sikertelen lesz a zökkenőmentes egyszeri bejelentkezés.
   >

5. Kattintson **az OK gombra**, majd kattintson ismét **az OK gombra** .

    ![Egyszeri bejelentkezés](./media/how-to-connect-sso-quick-start/sso7.png)

6. Tallózás a **felhasználói konfigurációs** > **házirend** > **Felügyeleti sablonok** **Windows-összetevők** **Internet**ExplorerInternet >  >  >  **A Vezérlőpult** **biztonsági oldalának** > intranetes zónája. >  Ezután válassza **a frissítések engedélyezése állapotsoron parancsfájl használatával**lehetőséget.

    ![Egyszeri bejelentkezés](./media/how-to-connect-sso-quick-start/sso11.png)

7. Engedélyezze a házirend-beállítást, majd kattintson **az OK gombra**.

    ![Egyszeri bejelentkezés](./media/how-to-connect-sso-quick-start/sso12.png)

### <a name="group-policy-preference-option---detailed-steps"></a>"Csoportházirend-beállítások" beállítás – részletes lépések

1. Nyissa meg az Csoportházirend-felügyeleti szerkesztő eszközt.
2. Szerkessze az egyes felhasználókra alkalmazott csoportházirendet. Ez a példa az **alapértelmezett tartományi házirendet**használja.
3. Tallózással keresse meg a **felhasználói konfiguráció** > **beállításait** > a**Windows beállításai** > **beállításjegyzék** > **új** > **beállításjegyzék-eleme**.

    ![Egyszeri bejelentkezés](./media/how-to-connect-sso-quick-start/sso15.png)

4. Adja meg a következő értékeket a megfelelő mezőkben, majd kattintson **az OK**gombra.
   - **Kulcs elérési útja**: ***Software\Microsoft\Windows\CurrentVersion\Internet Settings\ZoneMap\Domains\microsoftazuread-sso.com\autologon***
   - **Érték neve**: ***https***.
   - **Érték típusa**: ***REG_DWORD***.
   - **Érték**: ***00000001***.
 
     ![Egyszeri bejelentkezés](./media/how-to-connect-sso-quick-start/sso16.png)
 
     ![Egyszeri bejelentkezés](./media/how-to-connect-sso-quick-start/sso17.png)

### <a name="browser-considerations"></a>A böngésző szempontjai

#### <a name="mozilla-firefox-all-platforms"></a>Mozilla Firefox (minden platform)

A Mozilla Firefox nem használ automatikusan Kerberos-hitelesítést. Az alábbi lépések segítségével minden felhasználónak manuálisan hozzá kell adnia az Azure AD URL-címét a Firefox-beállításaihoz:
1. Futtassa a Firefoxot, `about:config` és írja be a címsorba. Zárja be a megjelenő értesítéseket.
2. Keressen rá a **Network. Negotiate – auth. megbízható – URI-** k beállításra. Ez a beállítás felsorolja a Firefox megbízható helyeit a Kerberos-hitelesítéshez.
3. Kattintson a jobb gombbal, és válassza a **módosítás**lehetőséget.
4. Adja `https://autologon.microsoftazuread-sso.com` meg a mezőt a mezőben.
5. Kattintson **az OK gombra** , majd nyissa meg újra a böngészőt.

#### <a name="safari-macos"></a>Safari (macOS)

Győződjön meg arról, hogy a macOS-t futtató számítógép csatlakozik az AD-hez. A macOS-eszközhöz való csatlakozásra vonatkozó utasítások a jelen cikk hatókörén kívül esnek.

#### <a name="google-chrome-all-platforms"></a>Google Chrome (minden platform)

Ha felülbírálta a [AuthNegotiateDelegateWhitelist](https://www.chromium.org/administrators/policy-list-3#AuthNegotiateDelegateWhitelist) vagy a [AuthServerWhitelist](https://www.chromium.org/administrators/policy-list-3#AuthServerWhitelist) házirend-beállításait a környezetben, ügyeljen arra, hogy az Azure ad URL-címét (`https://autologon.microsoftazuread-sso.com`) is hozzáadja hozzájuk.

#### <a name="google-chrome-macos-and-other-non-windows-platforms"></a>Google Chrome (macOS és egyéb nem Windows platform)

A Google Chrome Mac OS és más, nem Windows platformokon történő használata esetén tekintse meg [a Chromium-projekt házirendjének listáját](https://dev.chromium.org/administrators/policy-list-3#AuthServerWhitelist) , amelyből megtudhatja, hogyan engedélyezheti az Azure ad URL-cím integrált hitelesítéshez való engedélyezési módját.

A harmadik féltől származó Active Directory Csoportházirend bővítmények használata az Azure AD URL-címének a Firefox-ra és a Google Chrome-ra való bevezetésére Mac-felhasználókon kívül esik a jelen cikk hatókörén.

#### <a name="known-browser-limitations"></a>Böngészőkkel kapcsolatos ismert korlátozások

A zökkenőmentes egyszeri bejelentkezés nem működik a Firefox és a Microsoft Edge böngészők privát böngészési módjában. Emellett az Internet Explorerben nem működik, ha a böngésző fokozottan védett módban fut.

## <a name="step-4-test-the-feature"></a>4\. lépés: A szolgáltatás tesztelése

Egy adott felhasználó szolgáltatásának teszteléséhez győződjön meg arról, hogy a következő feltételek teljesülnek:
  - A felhasználó bejelentkezik egy vállalati eszközre.
  - Az eszköz csatlakoztatva van a Active Directory tartományhoz. Az eszköznek _nem_ kell csatlakoznia az [Azure ad-hez](../active-directory-azureadjoin-overview.md).
  - Az eszköz közvetlen kapcsolattal rendelkezik a tartományvezérlővel (DC) a vállalati vezetékes vagy vezeték nélküli hálózaton, vagy egy távelérési kapcsolaton keresztül, például egy VPN-kapcsolaton keresztül.
  - A funkciót a Csoportházirendon keresztül [építették ki a](##step-3-roll-out-the-feature) felhasználó számára.

A forgatókönyv teszteléséhez, ahol a felhasználó csak a felhasználónevet adja meg, nem pedig a jelszót:
   - Jelentkezzen be egy új privát böngésző -munkamenetbe.`https://myapps.microsoft.com/`

A következő lépések egyikével tesztelheti azt a forgatókönyvet, amelyben a felhasználónak nem kell megadnia a felhasználónevet vagy a jelszót: 
   - Jelentkezzen be egy új privát böngésző -munkamenetbe.`https://myapps.microsoft.com/contoso.onmicrosoft.com` A *contoso* helyére írja be a bérlő nevét.
   - Jelentkezzen be egy új privát böngésző -munkamenetbe.`https://myapps.microsoft.com/contoso.com` Cserélje le a *contoso.com* egy ellenőrzött tartományra (nem összevont tartományra) a bérlőn.

## <a name="step-5-roll-over-keys"></a>5\. lépés: Kulcsok átadása

A 2. lépésben Azure AD Connect az összes olyan Active Directory erdőben hoz létre számítógépfiókokat (amely az Azure AD-t jelöli), amelyeken engedélyezve van a zökkenőmentes egyszeri bejelentkezés. További információ: [Azure Active Directory zökkenőmentes egyszeri bejelentkezés: Technikai részletesen](how-to-connect-sso-how-it-works.md)elmerülhet.

>[!IMPORTANT]
>Egy számítógépfiók Kerberos-visszafejtési kulcsa, ha kiszivárgott, felhasználható Kerberos-jegyek létrehozásához bármely, az AD-erdőben lévő felhasználó számára. A rosszindulatú szereplők ezután megszemélyesítheti az Azure AD-bejelentkezéseket a feltört felhasználók számára. Javasoljuk, hogy rendszeres időközönként áttekintse ezeket a Kerberos-visszafejtési kulcsokat – legalább 30 naponta egyszer.

A kulcsok átadására vonatkozó utasításokért lásd [: Azure Active Directory zökkenőmentes egyszeri bejelentkezés: Gyakran ismételt kérdések](how-to-connect-sso-faq.md#how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacc-computer-account). Dolgozunk a kulcsok automatizált átadásának bevezetésében.

>[!IMPORTANT]
>Ezt a lépést nem kell _azonnal_ végrehajtania, miután engedélyezte a szolgáltatást. A Kerberos-visszafejtési kulcsok legalább 30 naponkénti átadása.

## <a name="next-steps"></a>További lépések

- [Technikai részletes merülés](how-to-connect-sso-how-it-works.md): Ismerje meg, hogyan működik a zökkenőmentes egyszeri bejelentkezés funkció.
- [Gyakori kérdések](how-to-connect-sso-faq.md): Választ kaphat a zökkenőmentes egyszeri bejelentkezéssel kapcsolatos gyakori kérdésekre.
- [Hibák megoldása](tshoot-connect-sso.md): Ismerje meg, Hogyan oldhatók meg a zökkenőmentes egyszeri bejelentkezés funkció gyakori problémái.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Az új szolgáltatásokra vonatkozó kérelmeket a Azure Active Directory fórum használatával teheti meg.
