---
title: Azure Active Directory Application Proxy központi telepítésének megtervezése
description: Teljes körű útmutató az alkalmazásproxy üzembe helyezésének megtervezéséhez a szervezeten belül
services: active-directory
documentationcenter: azure
author: barbaraselden
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: baselden
ms.reviewer: ''
ms.openlocfilehash: cd19d1e0cdfa1b160734b23d7f50310948ded80d
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68879918"
---
# <a name="plan-an-azure-ad-application-proxy-deployment"></a>Azure AD Application Proxy üzemelő példány megtervezése

A Azure Active Directory (Azure AD) alkalmazásproxy biztonságos és költséghatékony távelérési megoldás helyszíni alkalmazásokhoz. Azonnali áttérési útvonalat biztosít a "Cloud First" szervezetek számára a régi helyszíni alkalmazásokhoz való hozzáférés kezeléséhez, amelyek még nem képesek modern protokollok használatára. További bevezető információk: [Mi az a alkalmazásproxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy).

Az alkalmazásproxy használata ajánlott a távoli felhasználók számára a belső erőforrásokhoz való hozzáférés biztosítása érdekében. Az alkalmazásproxy lecseréli a szükséges VPN-vagy fordított proxyt ezen távelérési használati esetekben. Nem a vállalati hálózaton lévő felhasználók számára készült. Azok a felhasználók, akik az Application proxyt használják az intranetes hozzáféréshez, nem kívánatos teljesítménnyel kapcsolatos problémákat tapasztalnak.

Ez a cikk az Azure-AD Application Proxy tervezéséhez, működtetéséhez és kezeléséhez szükséges erőforrásokat tartalmazza. 

## <a name="plan-your-implementation"></a>A megvalósítás tervezése

A következő szakasz áttekintést nyújt a legfontosabb tervezési elemekről, amelyek a hatékony üzembe helyezést teszik lehetővé. 

### <a name="prerequisites"></a>Előfeltételek

A megvalósítás megkezdése előtt meg kell felelnie a következő előfeltételeknek. Ebben az oktatóanyagban további információkat talál a környezet beállításával kapcsolatban, beleértve az előfeltételeket [](application-proxy-add-on-premises-application.md)is.

* **Összekötők**: Az összekötők olyan könnyű ügynökök, amelyeket üzembe helyezhet:
   * Helyszíni fizikai hardver
   * Bármely hypervisor-megoldáson belül üzemeltetett virtuális gép
   * Az Azure-ban üzemeltetett virtuális gép, amely lehetővé teszi a kimenő kapcsolódást az alkalmazásproxy szolgáltatáshoz.

* Részletesebb áttekintést a [Azure ad alkalmazás proxy-összekötők ismertetése](application-proxy-connectors.md) című témakörben talál.

     * Az összekötők telepítése előtt engedélyeznie kell az összekötő gépeket [a TLS 1,2-hez](application-proxy-add-on-premises-application.md) .

     * Ha lehetséges, telepítsen összekötőket [ugyanabban](application-proxy-network-topology.md) a hálózatban és szegmensben, mint a háttérbeli webalkalmazás-kiszolgálókat. Az alkalmazások felderítésének befejezése után érdemes az összekötőket üzembe helyezni.
     * Javasoljuk, hogy minden összekötő-csoportnak legalább két összekötője legyen, hogy magas rendelkezésre állást és méretezést biztosítson. Ha a három összekötő optimális, akkor előfordulhat, hogy egy gépet bármikor kell kiszolgálni. Tekintse át az [összekötő kapacitása táblában](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#capacity-planning) , hogy eldöntse, milyen típusú gépet telepítsen az összekötők. Minél nagyobb a gép, annál több puffert és teljesítményű összekötőt használ a rendszer.

* **Hálózati hozzáférési beállítások**: Az Azure AD Application Proxy [-összekötők a https-kapcsolaton keresztül csatlakoznak az Azure-hoz (443-as TCP-port) és a http-t (80](application-proxy-add-on-premises-application.md) 

   * Az összekötő TLS-forgalmának leállítása nem támogatott, és megakadályozza, hogy az összekötők biztonságos csatornát hozzanak létre a megfelelő Azure app proxy-végpontokkal.

   * Kerülje a beágyazott ellenőrzés összes formáját az összekötők és az Azure közötti kimenő TLS-kommunikációra. Az összekötő és a háttérbeli alkalmazások közötti belső ellenőrzés lehetséges, de csökkentheti a felhasználói élményt, és így nem ajánlott.

   * Az összekötők terheléselosztása nem támogatott, vagy akár nem is szükséges.

### <a name="important-considerations-before-configuring-azure-ad-application-proxy"></a>Fontos szempontok az Azure AD Application Proxy konfigurálása előtt

Az Azure AD Application Proxy konfigurálásához és megvalósításához a következő alapvető követelmények teljesülése szükséges.

*  Az **Azure**bevezetése: Az alkalmazásproxy üzembe helyezése előtt a felhasználói identitásokat a helyszíni címtárból kell szinkronizálni, vagy közvetlenül az Azure AD-bérlőn belül kell létrehozni. Identitásszinkronizálás lehetővé teszi az Azure AD számára, hogy előzetesen hitelesítse a felhasználókat, mielőtt hozzáférést adna nekik az App proxy közzétett alkalmazásaihoz, és hogy a szükséges felhasználói azonosító információkkal rendelkezzen az egyszeri bejelentkezés (SSO) végrehajtásához.

* **Feltételes hozzáférési követelmények**: Nem ajánlott az alkalmazásproxy használata az intranetes hozzáféréshez, mert ez a művelet a felhasználókat érintő késést okoz. Azt javasoljuk, hogy az alkalmazásproxy használatát előhitelesítéssel és feltételes hozzáférési szabályzatokkal használja az internetről történő távoli hozzáféréshez.  Az intranetes használatra való feltételes hozzáférés biztosításának megközelítése az alkalmazások modernizálása, hogy diretly a hitelesítést a HRE használatával. További információkért tekintse meg az [alkalmazások HRE](https://docs.microsoft.com/azure/active-directory/manage-apps/migration-resources) való áttelepítésének erőforrásait. 

* **Szolgáltatási korlátok**: Az egyes bérlők erőforrásainak túlfogyasztása elleni védelem érdekében az alkalmazások és a bérlők szabályozási korlátokkal rendelkeznek. A korlátozások megtekintéséhez tekintse meg az [Azure ad szolgáltatás korlátozásait és korlátozásait](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions). Ezek a sávszélesség-szabályozási korlátok a tipikus használati köteten felüli teljesítményteszten alapulnak, és bőséges puffert biztosítanak az üzemelő példányok többsége számára.

* **Nyilvános tanúsítvány**: Ha egyéni tartományneveket használ, be kell szereznie egy nem a Microsofttól származó megbízható hitelesítésszolgáltató által kiadott nyilvános tanúsítványt. A szervezeti követelményektől függően a tanúsítvány beszerzése hosszabb időt vehet igénybe, és a lehető leghamarabb javasoljuk a folyamat megkezdését. Az Azure Application proxy támogatja a standard, a [helyettesítő karaktereket](application-proxy-wildcard.md)vagy a San-alapú tanúsítványokat.

* **Tartományi követelmények**: Az egyszeri bejelentkezés a közzétett alkalmazásokra a Kerberos által korlátozott delegálással (KCD) megköveteli, hogy az összekötőt futtató kiszolgáló és az alkalmazást futtató kiszolgáló tartományhoz legyen csatlakoztatva, és ugyanahhoz a tartományhoz vagy megbízható tartományhoz tartozik.
A témakörrel kapcsolatos részletes információkért lásd: [KCD az egyszeri bejelentkezéshez](application-proxy-configure-single-sign-on-with-kcd.md) az Application proxyval. Az összekötő szolgáltatás a helyi rendszer kontextusában fut, és nem konfigurálható egyéni identitás használatára.

* **DNS-rekordok URL-címekhez**

   * Mielőtt egyéni tartományokat használ az Application proxyban, létre kell hoznia egy CNAME-rekordot a nyilvános DNS-ben, így az ügyfelek feloldják az egyéni, definiált külső URL-címet az előre definiált alkalmazásproxy-címhez. Ha nem sikerül CNAME rekordot létrehozni egy egyéni tartományt használó alkalmazáshoz, azzal megakadályozza, hogy a távoli felhasználók csatlakozzanak az alkalmazáshoz. A CNAME rekordok hozzáadásához szükséges lépések a DNS-szolgáltatótól eltérőek lehetnek, ezért [a Azure Portal használatával megismerheti a DNS-rekordok és-](https://docs.microsoft.com/azure/dns/dns-operations-recordsets-portal)rekordhalmazok kezelését.

   * Hasonlóképpen, az összekötő gazdagépeknek képesnek kell lenniük a közzétett alkalmazások belső URL-címének feloldására.

* **Rendszergazdai jogosultságok és szerepkörök**

   * Az **összekötő telepítéséhez** helyi rendszergazdai jogosultságok szükségesek ahhoz a Windows Server rendszerhez, amelyre telepítve van. Emellett legalább egy *alkalmazás-rendszergazdai* szerepkörrel kell rendelkeznie az összekötő-példány hitelesítéséhez és az Azure ad-bérlőhöz való regisztrálásához. 

   * Az **alkalmazások közzétételéhez és felügyeletéhez** az *alkalmazás-rendszergazdai* szerepkör szükséges. Az alkalmazás-rendszergazdák kezelhetik a címtárban lévő összes alkalmazást, beleértve a regisztrációkat, az SSO-beállításokat, a felhasználók és csoportok hozzárendelését, a licencelést, az alkalmazásproxy beállításait és a jóváhagyást. Nem biztosít lehetőséget a feltételes hozzáférés kezelésére. A *Cloud Application Administrator* szerepkör rendelkezik az alkalmazás-rendszergazda összes képességével, azzal a kivétellel, hogy az alkalmazásproxy-beállítások kezelése nem engedélyezett.

* **Licencelés**: Az alkalmazásproxy a alapszintű Azure AD-előfizetésen keresztül érhető el. A licencelési lehetőségek és szolgáltatások teljes listájáért tekintse meg a [Azure Active Directory díjszabási oldalát](https://azure.microsoft.com/pricing/details/active-directory/) .  

### <a name="application-discovery"></a>Alkalmazás felderítése

A következő információk összegyűjtésével lefordíthatja az Application proxyn keresztül közzétett összes hatókörű alkalmazás leltárát:

| Információ típusa| Gyűjtött információk |
|---|---|
| Service Type| Példa: SharePoint, SAP, CRM, Custom Web Application, API |
| Alkalmazás platform | Példa: Windows IIS, Apache Linuxon, tomcat, NGINX |
| Tartományi tagság| A webkiszolgáló teljesen minősített tartományneve (FQDN) |
| Alkalmazás helye | Hol található a webkiszolgáló vagy a Farm az infrastruktúrában |
| Belső hozzáférés | Az alkalmazás belső eléréséhez használt pontos URL-cím. <br> Ha egy farmon van, milyen típusú terheléselosztás van használatban? <br> Azt határozza meg, hogy az alkalmazás nem magától a forrásból hívja-e a tartalmat.<br> Annak megállapítása, hogy az alkalmazás működik-e WebSockets-en keresztül. |
| Külső hozzáférés | A szállítói megoldás, amelyet az alkalmazás már külsőleg is kihelyezett. <br> A külső hozzáféréshez használni kívánt URL-cím. Ha a SharePoint, az alternatív hozzáférés-hozzárendelések konfigurálása [ebben az útmutatóban](https://docs.microsoft.com/SharePoint/administration/configure-alternate-access-mappings)történik. Ha nem, akkor meg kell határoznia a külső URL-címeket. |
| Nyilvános tanúsítvány | Ha egyéni tartományt használ, egy megfelelő tulajdonos nevű tanúsítványt kell beszereznie. Ha a tanúsítvány létezik, jegyezze fel a sorozatszámot és a helyet, ahonnan beszerezhetők. |
| Hitelesítési típus| Az alkalmazás által támogatott hitelesítés típusa, például az alapszintű, a Windows integrációs hitelesítés, az űrlapalapú, a fejléc-alapú és a jogcímek. <br>Ha az alkalmazás egy adott tartományi fiókban való futtatásra van konfigurálva, jegyezze fel a szolgáltatásfiók teljes tartománynevét (FQDN).<br> Ha SAML-alapú, az azonosító és a válasz URL-címe. <br> Ha a fejléc-alapú, a szállítói megoldás és a hitelesítési típus kezelésére vonatkozó konkrét követelmény. |
| Összekötő csoportjának neve | Az összekötők azon csoportjának logikai neve, amely a csatorna és az SSO e háttérbeli alkalmazás számára történő biztosítására lesz kijelölve. |
| Felhasználók/csoportok hozzáférése | Azok a felhasználók vagy felhasználói csoportok, amelyek külső hozzáférést kapnak az alkalmazáshoz. |
| További követelmények | Jegyezze fel az alkalmazás közzétételéhez szükséges további távelérési vagy biztonsági követelményeket. |

Az alkalmazás leltározási [táblázatát](https://aka.ms/appdiscovery) letöltheti az alkalmazások leltározásához.

### <a name="define-organizational-requirements"></a>Szervezeti követelmények meghatározása

A következő területeken kell megadnia a szervezet üzleti követelményeit. Mindegyik terület a követelményekre vonatkozó példákat tartalmaz

 **Access (Hozzáférés)**

* A tartományhoz csatlakoztatott vagy az Azure AD-hez csatlakoztatott eszközöket használó távoli felhasználók biztonságosan érhetik el a közzétett alkalmazásokat a zökkenőmentes egyszeri bejelentkezéssel (SSO).

* A jóváhagyott személyes eszközökkel rendelkező távoli felhasználók biztonságosan hozzáférhetnek az MFA-ban regisztrált közzétett alkalmazásokhoz, és az Microsoft Authenticator alkalmazást a mobiltelefonján hitelesítési módszerként regisztrálták.

**Cégirányítási** 

* A rendszergazdák megadhatják és megfigyelheti az alkalmazásproxy használatával közzétett alkalmazások felhasználói hozzárendeléseinek életciklusát.

**Biztonság**

* Csak azok a felhasználók férhetnek hozzá az alkalmazásokhoz, akik csoporttagság alapján vannak hozzárendelve az alkalmazásokhoz.

**Teljesítmény**

* A belső hálózatról való hozzáféréshez képest nem lehet az alkalmazás teljesítményének romlása.

**Felhasználói élmény**

* A felhasználók tudomásul veszik, hogyan érhetik el alkalmazásaikat ismerős vállalati URL-címek használatával bármely eszköz platformon.

**Naplózás**
* A rendszergazdák képesek a felhasználó-hozzáférési tevékenység naplózására.


### <a name="best-practices-for-a-pilot"></a>Ajánlott eljárások a pilóták számára

Határozza meg, hogy mennyi idő és erőfeszítés szükséges ahhoz, hogy az egyszeri bejelentkezés (SSO) használatával teljes körűen el lehessen érni a távelérést. Ehhez futtasson egy olyan próbaverziót, amely a kezdeti felderítést, közzétételt és általános tesztelést veszi figyelembe. Ha egy egyszerű, integrált Windows-hitelesítéshez (IWA) már konfigurált IIS-alapú webalkalmazást használ, akkor az alapkonfiguráció létrehozása segítene, mivel ez a beállítás minimális erőfeszítést igényel a távelérés és az egyszeri bejelentkezés sikeres kipróbálásához.

A következő kialakítási elemeknek a próbaüzem megvalósításának sikerességét közvetlenül egy éles bérlőben kell megjavítani.  

**Összekötők kezelése**:  

* Az összekötők kulcsszerepet játszanak a helyszíni adatcsatorna alkalmazásokban való biztosításában. Az **alapértelmezett** összekötő-csoport használata megfelelő a közzétett alkalmazások kezdeti kísérleti teszteléséhez, mielőtt üzembe helyezné őket az éles környezetben. A sikeresen tesztelt alkalmazások ezután áthelyezhetők a termelési összekötő csoportjaiba.

**Alkalmazás-kezelés**:

* A munkaerő nagy valószínűséggel emlékszik egy külső URL-re ismerős és releváns. Kerülje az alkalmazás közzétételét az előre definiált msappproxy.net-vagy onmicrosoft.com-utótagok használatával. Ehelyett adjon meg egy ismerős legfelső szintű ellenőrzött tartományt, amely előre fel van oldva egy olyan logikai állomásnévvel, mint az *intranet. < customers_domain >. com*.

* A kísérleti alkalmazás ikonjának a kísérleti csoportra való láthatóságának korlátozása az Azure MyApps portál indítási ikonjának megjelenítésével. Ha készen áll az éles környezetben való használatra, az alkalmazást megcélozhatja a megfelelő célközönségre, vagy ugyanabban az üzem előtti bérlőben, vagy az alkalmazást is közzéteheti az üzemi bérlőben.

**Egyszeri bejelentkezés beállításai**: Egyes SSO-beállítások olyan függőségekkel rendelkeznek, amelyek lehetővé teszik a beállítását, így elkerülhető, hogy a függőségek kezelése időben történjen. Ez magában foglalja a tartományhoz csatlakozó gazdagépek számára a Kerberos által korlátozott delegálás (KCD) használatával végzett egyszeri bejelentkezést, valamint az egyéb időigényes tevékenységek ellátását. Például egy PING Access-példány beállítása, ha fejléc-alapú egyszeri bejelentkezésre van szükség.

**SSL az összekötő gazdagép és a célalkalmazás között**: A biztonság a legfontosabb, ezért a TLS-t az összekötő gazdagép és a cél alkalmazások között mindig használni kell. Különösen, ha a webalkalmazás az űrlapalapú hitelesítéshez (FBA) van konfigurálva, mivel a felhasználói hitelesítő adatokat a rendszer hatékonyan továbbítja a tiszta szövegbe.

**Fokozatosan hajtsa végre az egyes lépéseket, és tesztelje**azokat. Az alkalmazás közzététele után alapvető funkcionális tesztelést végezhet, így biztosítva, hogy minden felhasználói és üzleti követelmény teljesül az alábbi utasítások követésével:

1. A webalkalmazás általános hozzáférésének tesztelése és ellenőrzése az előhitelesítés letiltásával.
2. Ha sikeresen engedélyezte az előhitelesítést, és hozzárendeli a felhasználókat és a csoportokat. A hozzáférés tesztelése és érvényesítése.
3. Ezután adja hozzá az SSO-metódust az alkalmazáshoz, majd tesztelje újból a hozzáférést a hozzáférés ellenőrzéséhez.
4. Szükség szerint alkalmazza a feltételes hozzáférést és az MFA-szabályzatokat. A hozzáférés tesztelése és érvényesítése.

**Hibaelhárítási eszközök**: A hibaelhárítás során mindig ellenőrizze, hogy a közzétett alkalmazáshoz való hozzáférést az összekötő-gazdagép böngészője ellenőrzi-e, és győződjön meg róla, hogy az alkalmazás a várt módon működik-e. Minél egyszerűbb a beállítás, annál könnyebb meghatározni a kiváltó okot, ezért érdemes lehet olyan minimális konfigurációval reprodukálni a problémákat, mint például egyetlen összekötő használata, és nincs SSO. Bizonyos esetekben a webes hibakeresési eszközök, például a Telerik Hegedűs a proxyn keresztül elért alkalmazások hozzáférési vagy tartalmi problémáinak elhárításához nem feltétlenül szükségesek. A Hegedűs proxyként is működhet a mobil platformok, például az iOS és az Android, illetve gyakorlatilag bármit, ami proxyn keresztüli irányításra konfigurálható. További információért tekintse meg a [hibaelhárítási útmutatót](application-proxy-troubleshoot.md) .

## <a name="implement-your-solution"></a>A megoldás implementálása

### <a name="deploy-application-proxy"></a>Alkalmazásproxy üzembe helyezése

Ebben az oktatóanyagban az alkalmazásproxy központi telepítésének lépéseit tárgyaljuk a táveléréshez helyszíni [alkalmazás hozzáadásához](application-proxy-add-on-premises-application.md). Ha a telepítés nem sikerül, válassza az **alkalmazásproxy hibaelhárítása** lehetőséget a portálon, vagy használja a hibaelhárítási útmutatót az [alkalmazásproxy-ügynök összekötő telepítésével kapcsolatos problémák megoldásához](application-proxy-connector-installation-problem.md).

### <a name="publish-applications-via-application-proxy"></a>Alkalmazások közzététele alkalmazásproxy használatával

A közzétételi alkalmazások feltételezik, hogy az összes előfeltétel teljesült, és az alkalmazásproxy oldalon több összekötő jelenik meg, amelyek regisztrálva és aktívak.

Az alkalmazásokat a [PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)használatával is közzéteheti.

Az alábbi ajánlott eljárások követik az alkalmazások közzétételekor:

* **Összekötő csoportok használata**: Rendeljen hozzá egy olyan összekötő csoportot, amely minden egyes alkalmazás közzétételére lett kijelölve. Javasoljuk, hogy minden összekötő-csoportnak legalább két összekötője legyen, hogy magas rendelkezésre állást és méretezést biztosítson. Ha a három összekötő optimális, akkor előfordulhat, hogy egy gépet bármikor kell kiszolgálni. További információ: [alkalmazások közzététele különálló hálózatokon és helyszíneken összekötő-csoportok használatával](application-proxy-connector-groups.md) , hogy megtudja, hogyan használhatók összekötő csoportok az összekötők hálózat vagy hely szerinti szegmentálásához.

* **Háttérbeli alkalmazás időkorlátjának beállítása**: Ez a beállítás olyan esetekben hasznos, amikor az alkalmazásnak több mint 75 másodpercre lehet szüksége az ügyfél-tranzakció feldolgozásához. Például amikor egy ügyfél egy olyan webalkalmazásnak küld lekérdezést, amely előtérként működik egy adatbázison. Az előtér elküldi a lekérdezést a háttérbeli adatbázis-kiszolgálónak, és megvárja a választ, de a válasz megérkezése után a beszélgetés ügyféloldali oldala időtúllépést okoz. A hosszú Időtúllépés beállítása 180 másodpercet biztosít a hosszabb tranzakciók befejezéséhez.

* **A megfelelő cookie-típusok használata**

   * **Csak HTTP cookie**: További biztonságot nyújt, ha az alkalmazásproxy tartalmazza a HTTPOnly jelzőt a Set-Cookie HTTP-válasz fejlécekben. Ezzel a beállítással csökkenthetők a biztonsági rések, például a helyek közötti parancsfájlok (XSS). Hagyja ezt a nem értékre olyan ügyfelek/felhasználói ügynökök számára, akiknek hozzáférésre van szükségük a munkamenet cookie-hoz. Az RDP/MTSC-ügyfél például az App proxyn keresztül közzétett Távoli asztali átjáróhoz csatlakozik.

   * **Biztonságos cookie**: Ha a cookie-t a biztonságos attribútummal beállították, a felhasználói ügynök (ügyféloldali alkalmazás) csak a cookie-t fogja tartalmazni a HTTP-kérelmekben, ha a kérést TLS-védelemmel ellátott csatornán keresztül továbbítják. Ez segít enyhíteni annak kockázatát, hogy a cookie-t tiszta szöveges csatornákon keresztül feltörték, ezért engedélyezni kell.

   * **Állandó cookie**: Lehetővé teszi, hogy az alkalmazásproxy-munkamenet cookie a böngésző bezárása után is fennmaradjon mindaddig, amíg az érvényesség le nem jár, vagy törölve lesz. Olyan helyzetekben használható, amikor egy gazdag alkalmazás, például az Office hozzáfér egy közzétett webalkalmazásban található dokumentumhoz anélkül, hogy a felhasználót újra kellene kérni a hitelesítésre. Legyen körültekintő, ha azonban az állandó cookie-k végső soron a jogosulatlan hozzáférés veszélye miatt nem használják a szolgáltatást más kompenzáló vezérlőkkel együtt. Ez a beállítás csak olyan régebbi alkalmazásokhoz használható, amelyek nem oszthatnak meg sütiket a folyamatok között. A beállítás használata helyett érdemes frissíteni az alkalmazást, hogy kezelni tudja a különböző folyamatok közötti megosztási cookie-kat.

* **URL-címek lefordítása a fejlécekben**: Ez olyan helyzetekben engedélyezhető, amikor a belső DNS nem konfigurálható úgy, hogy az megfeleljen a szervezet nyilvános névterének (a. k. a megosztott DNS). Ha az alkalmazás az ügyfél kérésére az eredeti állomásfejléc-fejlécet igényli, az értéket állítsa Igen értékre. A másik lehetőség az, hogy az összekötő a belső URL-cím teljes tartománynevét használja a tényleges forgalom útválasztásához, a külső URL-ben pedig a gazdagép fejlécének teljes tartománynevét. A legtöbb esetben ez az alternatíva lehetővé teszi, hogy az alkalmazás a szokásos módon működjön, ha távolról éri el a szolgáltatást, de a felhasználók elveszítik a & kívüli URL-címeken belüli egyezés előnyeit.

* **URL-címek fordítása az alkalmazás törzsében**: Kapcsolja be az alkalmazás törzsének fordítását egy alkalmazáshoz, ha azt szeretné, hogy az alkalmazás hivatkozásait vissza lehessen fordítani az ügyfélnek küldött válaszokban. Ha engedélyezve van, ez a függvény az összes olyan belső hivatkozás lefordításakor, amely az App proxy által megtalált HTML-és CSS-válaszokba kerül, a rendszer a legjobb erőfeszítést nyújt. Akkor hasznos, ha olyan alkalmazásokat tesz közzé, amelyek rögzített abszolút vagy NetBIOS gazdagépbejegyzés-hivatkozásokat tartalmaznak a tartalomban, vagy olyan alkalmazásokkal, amelyek más helyszíni alkalmazásokra hivatkoznak.

Olyan esetekben, amikor egy közzétett alkalmazás más közzétett alkalmazásokra hivatkozik, engedélyezze az egyes alkalmazásokhoz való hivatkozás fordítását, hogy az alkalmazáson belüli szinten szabályozható legyen a felhasználói élmény.

Tegyük fel például, hogy három, az Application proxyn keresztül közzétett alkalmazással rendelkezik, amelyek mindegyike hivatkozik egymásra: Előnyök, költségek és utazás, valamint egy negyedik alkalmazás, visszajelzés, amely nem az alkalmazásproxy használatával van közzétéve.

![1\. kép](media/App-proxy-deployment-plan/link-translation.png)

Ha engedélyezi a kapcsolat fordítását az előnyök alkalmazáshoz, a költségekre és az utazásra mutató hivatkozások átirányítva lesznek az alkalmazások külső URL-címeire, így az alkalmazásokat a vállalati hálózaton kívülről elérő felhasználók is hozzáférhetnek. A költségekre mutató hivatkozások és az előnyök visszautazása nem működik, mert a kapcsolat fordítása nincs engedélyezve a két alkalmazás esetében. A visszajelzésre mutató hivatkozás nem lesz átirányítva, mert nincs külső URL-cím, így az előnyöket használó felhasználók nem tudnak hozzáférni a visszajelzési alkalmazáshoz a vállalati hálózaton kívülről. Tekintse meg a [hivatkozások fordításával és az egyéb átirányítási lehetőségekkel](application-proxy-configure-hard-coded-link-translation.md)kapcsolatos részletes információkat.

### <a name="access-your-application"></a>Hozzáférés az alkalmazáshoz

Számos lehetőség létezik az App proxy által közzétett erőforrásokhoz való hozzáférés kezelésére, ezért az adott forgatókönyvnek és méretezhetőségi igényeknek legmegfelelőbbnek kell kiválasztania. Gyakori megközelítések: a Azure AD Connecton keresztül szinkronizált helyszíni csoportok használata, dinamikus csoportok létrehozása az Azure AD-ben a felhasználói attribútumok alapján, az erőforrás-tulajdonos által felügyelt önkiszolgáló csoportok használatával vagy ezek kombinációja. Tekintse meg a kapcsolódó erőforrásokat az egyes szolgáltatások előnyeinek kihasználásához.

Az alkalmazásokhoz való hozzáférés legközvetlenebb továbbítási módja a közzétett alkalmazás bal oldali ablaktábláján található **felhasználók és csoportok** lehetőség lesz, és közvetlenül a csoportok vagy magánszemélyek kiosztása.

![24. kép](media/App-proxy-deployment-plan/add-user.png)

Azt is lehetővé teheti, hogy a felhasználók önkiszolgáló hozzáférést biztosítanak az alkalmazáshoz egy olyan csoport hozzárendelésével, amely jelenleg nem tagja a szervezetnek, és konfigurálja az önkiszolgáló lehetőségeket.

![25. kép](media/App-proxy-deployment-plan/allow-access.png)

Ha engedélyezve van, a felhasználók ezután bejelentkezhetnek az MyApps-portálra, és megkérhetik a hozzáférést, és vagy automatikusan jóváhagyják, és felveszik a már engedélyezett önkiszolgáló csoportba, vagy jóváhagyásra van szükségük a kijelölt jóváhagyótól.

A vendég felhasználók meghívhatják [az Application proxyn keresztül közzétett belső alkalmazások elérését is az Azure ad B2B használatával](https://docs.microsoft.com/azure/active-directory/b2b/add-users-information-worker).

A általában névtelenül elérhető helyszíni alkalmazásokhoz, amelyek nem igényelnek hitelesítést, érdemes lehet letiltani az alkalmazás **tulajdonságai**között található beállítást.

![26. kép](media/App-proxy-deployment-plan/assignment-required.png)


Ha ezt a beállítást választja, a nem engedélyezi a felhasználók számára a helyszíni alkalmazás elérését az engedélyek nélküli Azure AD alkalmazás proxyn keresztül, ezért körültekintően járjon el.

Az alkalmazás közzététele után elérhetőnek kell lennie a külső URL-cím beírásával egy böngészőben vagy a ikonja [https://myapps.microsoft.com](https://myapps.microsoft.com/)alapján.

### <a name="enable-pre-authentication"></a>Előzetes hitelesítés engedélyezése

Ellenőrizze, hogy az alkalmazás elérhető-e az alkalmazás-proxyn keresztül a külső URL-cím használatával. 

1. Navigáljon a **Azure Active Directory** > **vállalati alkalmazások** > **minden alkalmazás** , és válassza ki a kezelni kívánt alkalmazást.

2. Válassza ki **alkalmazásproxy**.

3. Az **előhitelesítés** mezőben válassza a legördülő listát a **Azure Active Directory**kiválasztásához, majd válassza a **Mentés**lehetőséget.

Az előhitelesítés engedélyezése esetén az Azure AD először a hitelesítést fogja felvenni a felhasználók számára, és ha az egyszeri bejelentkezés configued, akkor a háttérbeli alkalmazás is ellenőrzi a felhasználót, mielőtt hozzáférést kap az alkalmazáshoz. Ha az előhitelesítési mód átadását az Azure AD-be módosítja, a külső URL-cím HTTPS-sel is konfigurálható, ezért a rendszer minden olyan alkalmazást HTTPS-védelemmel fog védeni, amelyet eredetileg a HTTP-hez konfiguráltak.

### <a name="enable-single-sign-on"></a>Egyszeri bejelentkezés engedélyezése

Az SSO a lehető legjobb felhasználói élményt és biztonságot biztosítja, mivel a felhasználóknak csak egyszer kell bejelentkezniük az Azure AD-hez való hozzáféréskor. Miután a felhasználó előzetesen hitelesítette, az SSO-t a felhasználó nevében, a helyszíni alkalmazáshoz hitelesítő alkalmazásproxy-összekötő hajtja végre. A háttérbeli alkalmazás úgy dolgozza fel a bejelentkezést, mintha a felhasználó lenne. 

Az **áteresztő** beállítás választása lehetővé teszi a felhasználók számára a közzétett alkalmazás elérését anélkül, hogy az Azure ad-ben kellene hitelesíteni.

Az egyszeri bejelentkezés csak akkor hajtható végre, ha az Azure AD azonosítani tudja az erőforráshoz hozzáférést kérő felhasználót, ezért az alkalmazásnak úgy kell konfigurálnia, hogy az SSO-hoz való hozzáférés után az Azure AD-vel való bejelentkezéskor előre hitelesítse a felhasználókat, ellenkező esetben az SSO-beállítások le lesznek tiltva.

Olvasási [egyszeri bejelentkezés az Azure ad-alkalmazásokba](what-is-single-sign-on.md) az alkalmazások konfigurálásakor a legmegfelelőbb SSO-módszer kiválasztásához.

###  <a name="working-with-other-types-of-applications"></a>Más típusú alkalmazások használata

Az Azure AD Application Proxy képes támogatni az Azure AD Authentication Library ([ADAL](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)) vagy a Microsoft Authentication Library ([MSAL](https://azure.microsoft.com/blog/start-writing-applications-today-with-the-new-microsoft-authentication-sdks/)) használatára fejlesztett alkalmazásokat is. Támogatja a natív ügyfélalkalmazások használatát azáltal, hogy az ügyfél kérésének fejléc-adataiban kapott Azure AD-jogkivonatokat használja az előhitelesítés végrehajtásához a felhasználók nevében.

Az Application proxy elérhető konfigurációinak megismeréséhez olvassa el a [natív és mobil ügyfélalkalmazások](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-native-client) és a [jogcímbarát alkalmazások](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-claims-aware-apps) közzétételét ismertető témakört.

### <a name="use-conditional-access-to-strengthen-security"></a>A biztonság megerősítése a feltételes hozzáférés használatával

Az alkalmazás biztonsága olyan biztonsági képességek speciális készletét igényli, amelyek védelmet nyújthatnak a helyszíni és a Felhőbeli összetett fenyegetésektől. A támadók leggyakrabban a gyenge, alapértelmezett vagy ellopott felhasználói hitelesítő adatokon keresztül érhetik el a vállalati hálózati hozzáférést.  A Microsoft identitás-vezérelt biztonsága csökkenti az ellopott hitelesítő adatok használatát a privilegizált és nem emelt szintű identitások kezelésével és védelmével.

Az Azure AD Application Proxy támogatásához a következő lehetőségek használhatók:

* Felhasználó-és hely-alapú feltételes hozzáférés: Megtarthatja a bizalmas adatok védelmét azáltal, hogy a földrajzi hely vagy az IP-cím alapján korlátozza a felhasználók hozzáférését a [hely-alapú feltételes hozzáférési házirendekkel](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations).

* Eszköz alapú feltételes hozzáférés: Győződjön meg arról, hogy csak a regisztrált, jóváhagyott és megfelelő eszközök férhetnek hozzá a vállalati adatbázisokhoz az [eszközökön alapuló feltételes hozzáférés](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications)használatával.

* Alkalmazás-alapú feltételes hozzáférés: A munkának nem kell leállítania, ha a felhasználó nem a vállalati hálózaton van. [Biztonságos hozzáférés a vállalati felhőhöz és a](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam) helyszíni alkalmazásokhoz, valamint a vezérlés fenntartása feltételes hozzáféréssel.

* Kockázatalapú feltételes hozzáférés: A rosszindulatú hackerek adatai védelme olyan [kockázatalapú feltételes hozzáférési szabályzattal](https://www.microsoft.com/cloud-platform/conditional-access) , amely minden alkalmazásra és felhasználóra alkalmazható, akár a helyszínen, akár a felhőben.

* Azure AD-hozzáférési panel: Az alkalmazásproxy szolgáltatás üzembe helyezésével és az alkalmazások biztonságos közzétételével a felhasználók egy egyszerű központtal felfedezhetik és érhetik el az összes alkalmazást. Növelje a hatékonyságot önkiszolgáló képességekkel, például az új alkalmazásokhoz és csoportokhoz való hozzáférés kérését, illetve mások nevében az erőforrásokhoz való hozzáférés kezelését a [hozzáférési panelen](https://aka.ms/AccessPanelDPDownload).

## <a name="manage-your-implementation"></a>A megvalósítás kezelése

### <a name="required-roles"></a>Szükséges szerepkörök

A Microsoft javasolja a lehető legkevesebb jogosultság megadását az Azure AD-vel kapcsolatos szükséges feladatok elvégzéséhez. [Tekintse át a különböző elérhető Azure](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) -szerepköröket, és válassza ki a megfelelőt az egyes personák igényeihez. Előfordulhat, hogy bizonyos szerepköröket átmenetileg kell alkalmazni, és el kell távolítani az üzembe helyezés befejeződése után.

| Üzleti szerepkör| Üzleti feladatok| Azure AD-szerepkörök |
|---|---|---|
| Ügyfélszolgálati rendszergazda | Általában korlátozott a végfelhasználók által jelentett problémák és korlátozott feladatok végrehajtása, például a felhasználók jelszavának módosítása, a frissítési tokenek érvénytelenítése és a szolgáltatás állapotának figyelése. | Ügyfélszolgálati adminisztrátor |
| Identity admin| Az alkalmazás-proxyval kapcsolatos problémák hibakereséséhez olvassa el az Azure AD-beli bejelentkezési jelentéseket és a naplókat.| Biztonsági olvasó |
| Alkalmazás tulajdonosa| A vállalati alkalmazások, az alkalmazás-regisztrációk és az alkalmazásproxy-beállítások összes aspektusának létrehozása és kezelése.| Alkalmazás-rendszergazda |
| Infrastruktúra-rendszergazda | Tanúsítvány-átváltási tulajdonos | Alkalmazás-rendszergazda |

A biztonságos információkhoz vagy erőforrásokhoz hozzáférő személyek számának minimalizálása segít csökkenteni annak esélyét, hogy egy rosszindulatú személy jogosulatlan hozzáférést kapjon, vagy ha egy jogosult felhasználó véletlenül kihathat egy bizalmas erőforrásra. 
 
A felhasználóknak azonban továbbra is napi szintű jogosultságokkal rendelkező műveleteket kell elvégezniük, így az igény szerinti (JIT) alapú [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) szabályzatok betartatása az Azure-erőforrásokhoz és az Azure ad-hez szükséges magas szintű hozzáférés biztosításához ajánlott módszer a rendszergazdai hozzáférés és a naplózás hatékony kezelése felé.

### <a name="reporting-and-monitoring"></a>Jelentéskészítés és figyelés

Az Azure AD további elemzéseket biztosít a szervezet alkalmazás-használatáról és az üzemeltetési állapotáról a [naplók és jelentések](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs)segítségével. Az alkalmazásproxy az Azure AD-portál és a Windows-eseménynaplók közötti összekötők figyelését is megkönnyíti.

#### <a name="application-audit-logs"></a>Alkalmazások auditnaplói

Ezek a naplók részletes információkkal szolgálnak az alkalmazásproxy használatával konfigurált alkalmazásokhoz és az alkalmazáshoz hozzáférő felhasználóhoz való bejelentkezésekről. A [naplók](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs) a Azure Portal és az export [audit API](https://docs.microsoft.com/graph/api/resources/directoryaudit?view=graph-rest-beta) -ban találhatók. Emellett a [használati és](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-usage-insights-report) betekintő jelentések is elérhetők az alkalmazáshoz.

#### <a name="application-proxy-connector-monitoring"></a>Alkalmazásproxy-összekötő figyelése

Az összekötők és a szolgáltatás gondoskodik a magas rendelkezésre állású-feladatokat. Az összekötők állapotát az Azure AD-portál alkalmazásproxy oldaláról követheti nyomon. További információ az összekötő maintainence: az [Azure ad Application proxy-összekötők ismertetése](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#maintenance).

![Példa: Azure AD Application Proxy-összekötők](./media/application-proxy-connectors/app-proxy-connectors.png)

#### <a name="windows-event-logs-and-performance-counters"></a>Windows-eseménynaplók és teljesítményszámlálók

Az összekötők rendszergazdai és munkamenet-naplókkal is rendelkeznek. A felügyeleti naplók a fontos eseményeket és azok hibákat tartalmaznak. A munkamenet-naplók tartalmazzák a tranzakciók és a feldolgozási adataikat. A naplók és a számlálók a Windows-eseménynaplókban találhatók további információ: az [Azure ad Application proxy-összekötők ismertetése](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#under-the-hood). Ez az [oktatóanyag az Eseménynapló-adatforrások Azure monitor-ben történő konfigurálását](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-windows-events)ismerteti.

### <a name="troubleshooting-guide-and-steps"></a>Hibaelhárítási útmutató és lépések

További információ a gyakori problémákról és azok megoldásáról az útmutatóban a hibaüzenetek [hibaelhárításához](application-proxy-troubleshoot.md) . 

A következő cikkek olyan gyakori forgatókönyveket mutatnak be, amelyekkel hibaelhárítási útmutatók hozhatók létre a támogatási szervezet számára. 

* [Probléma az alkalmazáslap megjelenítésekor](application-proxy-page-appearance-broken-problem.md)
* [Az alkalmazás betöltési ideje túl hosszú](application-proxy-page-load-speed-problem.md)
* [Az alkalmazáslap hivatkozásai nem működnek](application-proxy-page-links-broken-problem.md)
* [Milyen portokat nyissak meg az alkalmazáshoz](application-proxy-connectivity-ports-how-to.md)
* [Az alkalmazás összekötőcsoportjában nem volt működő összekötő](application-proxy-connectivity-no-working-connector.md)
* [Konfigurálás a felügyeleti portálon](application-proxy-config-how-to.md)
* [Egyszeri bejelentkezés beállítása az alkalmazáshoz](application-proxy-config-sso-how-to.md)
* [Probléma egy alkalmazás felügyeleti portálon való létrehozásakor](application-proxy-config-problem.md)
* [A Kerberos által korlátozott delegálás konfigurálása](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [Konfigurálás a PingAccess segítségével](application-proxy-back-end-ping-access-how-to.md)
* [Nem sikerült hozzáférni a vállalati alkalmazáshiba](application-proxy-sign-in-bad-gateway-timeout-error.md)
* [Probléma az alkalmazásproxy-ügynök összekötőjének telepítésekor](application-proxy-connector-installation-problem.md)
* [Bejelentkezési probléma](application-sign-in-problem-on-premises-application-proxy.md)
