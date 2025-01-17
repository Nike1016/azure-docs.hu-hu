---
title: A Microsoft Identity platform fejlesztői szószedete | Azure
description: A gyakran használt Microsoft Identity platform-fejlesztői fogalmak és szolgáltatások használati feltételeinek listája.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 551512df-46fb-4219-a14b-9c9fc23998ba
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/21/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jmprieur, saeeda, jesakowi, nacanuma
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6b7c732a0af7fb3519cf255fa26478cd9ae82d2
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68835119"
---
# <a name="microsoft-identity-platform-developer-glossary"></a>A Microsoft Identity platform fejlesztői szószedete

Ez a cikk a fejlesztői alapfogalmakkal és terminológiával kapcsolatos néhány definíciót tartalmaz, amelyek hasznosak lehetnek a Microsoft Identity platform használatával történő alkalmazásfejlesztés megismerése során.

## <a name="access-token"></a>hozzáférési jogkivonat

Egy [engedélyezési kiszolgáló](#authorization-server)által kiállított [biztonsági jogkivonat](#security-token) típusa, amelyet egy [ügyfélalkalmazás](#client-application) használ egy [védett erőforrás-kiszolgáló](#resource-server)eléréséhez. Általában egy [JSON web token (JWT)][JWT]formájában a token az [erőforrás tulajdonosa](#resource-owner)által az ügyfélnek megadott engedélyeket testesíti meg a kért hozzáférési szint alapján. A jogkivonat tartalmazza a tulajdonossal kapcsolatos összes vonatkozó jogcímet, lehetővé téve az ügyfélalkalmazás számára, hogy hitelesítő adatként használja az adott erőforráshoz való hozzáféréskor. [](#claim) Emellett nincs szükség arra, hogy az erőforrás tulajdonosa hitelesítő adatokat tegyen elérhetővé az ügyfélnek.

A hozzáférési jogkivonatokat más néven "felhasználó + alkalmazás" vagy "csak alkalmazás" néven kell megjeleníteni, a képviselt hitelesítő adatoktól függően. Ha például egy ügyfélalkalmazás a következőt használja:

* Az ["engedélyezési kód" engedélyezési jogosultsága](#authorization-grant), a végfelhasználó először az erőforrás tulajdonosaként hitelesíti az ügyfelet az erőforrás eléréséhez. Az ügyfél a hozzáférési jogkivonat beszerzése után hitelesíti magát. A tokent időnként a "felhasználó + alkalmazás" tokennek is nevezik, mivel a felhasználó az ügyfélalkalmazás engedélyezését és az alkalmazást is képviseli.
* ["Ügyfél-hitelesítő adatok" engedélyezési engedély](#authorization-grant), az ügyfél biztosítja az egyetlen hitelesítést, amely az erőforrás-tulajdonos hitelesítése/engedélyezése nélkül működik, így a jogkivonat esetenként "csak alkalmazás" tokennek is nevezhető.

További részletekért tekintse meg a [Microsoft Identity platform jogkivonat][AAD-Tokens-Claims] -referenciáját.

## <a name="application-id-client-id"></a>alkalmazás azonosítója (ügyfél-azonosító)

Az egyedi azonosító az Azure AD-vel egy adott alkalmazást és a hozzá tartozó konfigurációkat azonosító alkalmazás-regisztrálási hibát okoz. Ez az alkalmazás-azonosító ([ügyfél-azonosító](https://tools.ietf.org/html/rfc6749#page-15)) a hitelesítési kérések végrehajtásakor használatos, és a hitelesítési kódtárak számára a fejlesztési időszakban van megadva. Az alkalmazás azonosítója (ügyfél-azonosító) nem titok.

## <a name="application-manifest"></a>alkalmazás jegyzékfájlja

A [Azure Portal][AZURE-portal]által biztosított szolgáltatás, amely az alkalmazás identitás-konfigurációjának JSON-ábrázolását állítja elő, amely a hozzá tartozó [alkalmazás][AAD-Graph-App-Entity] -és [ServicePrincipal][AAD-Graph-Sp-Entity] -entitások frissítésére szolgáló mechanizmusként használható. További részletekért tekintse meg [a Azure Active Directory Application manifest ismertetése][AAD-App-Manifest] című témakört.

## <a name="application-object"></a>Application objektum

Ha a Azure Portalban regisztrál/frissít egy alkalmazást [][AZURE-portal], a portál egy Application objektumot és egy hozzá tartozó [egyszerű szolgáltatásnevet](#service-principal-object) is létrehoz/frissít a bérlő számára. Az Application objektum globálisan definiálja az alkalmazás identitásának konfigurációját (az összes bérlőn, ahol hozzáféréssel rendelkezik), így biztosítva azt a sablont, amelyből a kapcsolódó szolgáltatásnév-objektum (ok) a helyi használatra van *származtatva* futási idő (egy adott bérlőn).

További információ: [alkalmazás-és egyszerű szolgáltatások objektumai][AAD-App-SP-Objects].

## <a name="application-registration"></a>alkalmazásregisztráció

Ahhoz, hogy az alkalmazások integrálva legyenek az identitás-és hozzáférés-kezelési funkciókkal az Azure AD-be, regisztrálni kell egy Azure AD [](#tenant)-bérlőben. Ha az Azure AD-vel regisztrálja az alkalmazást, az alkalmazáshoz meg kell adnia egy identitás-konfigurációt, amely lehetővé teszi, hogy integrálható legyen az Azure AD-vel és olyan funkciókkal, mint a következők:

* Az egyszeri bejelentkezés robusztus felügyelete az Azure AD Identity Management és az [OpenID Connect][OpenIDConnect] protokoll implementációjának használatával
* A [védett erőforrásokhoz](#resource-server) való felügyelt hozzáférés az [ügyfélalkalmazások](#client-application)által, a OAuth 2,0 [engedélyezési kiszolgálón](#authorization-server) keresztül
* Engedélyezési [keretrendszer](#consent) a védett erőforrásokhoz való ügyfél-hozzáférés kezeléséhez az erőforrás-tulajdonosi hitelesítés alapján.

További részletekért lásd: [alkalmazások integrálása a Azure Active Directorysal][AAD-Integrating-Apps] .

## <a name="authentication"></a>hitelesítés

A feleknek a jogos hitelesítő adatokkal szembeni kihívása, amely az identitás-és hozzáférés-vezérléshez használt rendszerbiztonsági tag létrehozásának alapja. A [OAuth2 engedélyezési megadásakor](#authorization-grant) például a hitelesítésre szolgáló fél az [erőforrás-tulajdonos](#resource-owner) vagy [ügyfélalkalmazás](#client-application)szerepkörét tölti ki a megadott támogatástól függően.

## <a name="authorization"></a>authorization

Egy hitelesített rendszerbiztonsági tag engedélyének megadására irányuló művelet. Az Azure AD programozási modellben két alapvető használati eset létezik:

* A [OAuth2 engedélyezési](#authorization-grant) megadási folyamata során: Ha az [erőforrás tulajdonosa](#resource-owner) engedélyt ad az [ügyfélalkalmazás](#client-application)számára, lehetővé teszi az ügyfél számára az erőforrás-tulajdonos erőforrásainak elérését.
* Az ügyfél erőforrás-hozzáférése során: az erőforrás- [kiszolgáló](#resource-server)által megvalósított módon, a [hozzáférési](#access-token) jogkivonatban található [jogcím](#claim) -értékekkel, hogy a hozzáférés-vezérlési döntéseket az alapján hozza létre.

## <a name="authorization-code"></a>engedélyezési kód

Egy rövid élettartamú "token", amelyet az [engedélyezési végpont](#authorization-endpoint)biztosít egy [ügyfélalkalmazás](#client-application) számára az "engedélyezési kód" folyamatának részeként, amely a négy OAuth2- [engedélyezési támogatás](#authorization-grant)egyike. A rendszer visszaküldi a kódot az ügyfélalkalmazás számára az [erőforrás tulajdonosának](#resource-owner)hitelesítésére válaszul, jelezve, hogy az erőforrás tulajdonosa delegált engedéllyel rendelkezik a kért erőforrások eléréséhez. A folyamat részeként a rendszer később beváltja a kódot egy [hozzáférési](#access-token)jogkivonatra.

## <a name="authorization-endpoint"></a>engedélyezési végpont

Az [engedélyezési kiszolgáló](#authorization-server)által megvalósított egyik végpont, amely az [erőforrás](#resource-owner) -tulajdonossal való interakcióra szolgál, hogy a OAuth2-engedélyezési folyamat során [engedélyezési](#authorization-grant) engedélyt lehessen biztosítani. A használt engedélyezési folyamattól függően a ténylegesen megadott támogatás változhat, beleértve az [engedélyezési kódot](#authorization-code) vagy a [biztonsági](#security-token)jogkivonatot is.

További részletekért tekintse meg a OAuth2 specifikációjának [engedélyezési][OAuth2-AuthZ-Grant-Types] és [engedélyezési végponti][OAuth2-AuthZ-Endpoint] részeit, valamint a [OpenIDConnect][OpenIDConnect-AuthZ-Endpoint] -specifikációt.

## <a name="authorization-grant"></a>engedélyezés engedélyezése

Az [ügyfél-alkalmazás](#client-application)számára biztosított védett erőforrások eléréséhez szükséges [erőforrás](#resource-owner) [](#authorization) -tulajdonosi jogosultságot képviselő hitelesítő adat. Az ügyfélalkalmazás a [OAuth2 engedélyezési keretrendszere által meghatározott négy engedélyezési típus][OAuth2-AuthZ-Grant-Types] egyikét használhatja a támogatás megszerzéséhez az ügyfél típusától/követelményeitől függően: "engedélyezési kód engedélyezése", "ügyfél hitelesítő adatok megadása", "implicit támogatás" és "erőforrás tulajdonos jelszavas hitelesítő adatok megadása ". Az ügyfélnek visszaadott hitelesítő adat vagy egy hozzáférési [jogkivonat](#access-token), vagy egy, a hozzáférési tokenhez később kicserélt hitelesítési [kód](#authorization-code) , a használt engedélyezési típustól függően.

## <a name="authorization-server"></a>engedélyezési kiszolgáló

A [OAuth2-engedélyezési keretrendszer][OAuth2-Role-Def]által meghatározottak szerint a hozzáférési jogkivonatok az ügyfélnek való [](#client-application) kiküldéséhez felelős kiszolgáló az [erőforrás-tulajdonos](#resource-owner) sikeres hitelesítése és az engedély beszerzése után. Az [ügyfélalkalmazás](#client-application) az engedélyezési és [jogkivonat](#token-endpoint) -végpontokon keresztül kommunikál az engedélyezési [](#authorization-endpoint) kiszolgálóval, a OAuth2 által meghatározott [engedélyezési támogatásoknak](#authorization-grant)megfelelően.

A Microsoft Identity platform Application Integration esetében a Microsoft Identity platform implementálja az Azure AD-alkalmazások és a Microsoft-szolgáltatások API-k engedélyezési kiszolgálói szerepkörét, például [Microsoft Graph API][Microsoft-Graph]-kat.

## <a name="claim"></a>igénylés

A [biztonsági jogkivonat](#security-token) olyan jogcímeket tartalmaz, amelyek az egyik entitásra (például az [ügyfélalkalmazás](#client-application) vagy az [erőforrás tulajdonosára](#resource-owner)) vonatkozó kijelentéseket biztosítanak egy másik entitáshoz (például az [erőforrás-kiszolgálóhoz](#resource-server)). A jogcímek olyan név/érték párok, amelyek a jogkivonat tárgyával kapcsolatos adatokat továbbítanak (például az [engedélyezési kiszolgáló](#authorization-server)által hitelesített rendszerbiztonsági tag). Az adott jogkivonatban található jogcímek több változótól függenek, beleértve a jogkivonat típusát, a tulajdonos hitelesítéséhez használt hitelesítő adatokat, az alkalmazás konfigurációját stb.

További részletekért tekintse meg a [Microsoft Identity platform jogkivonat][AAD-Tokens-Claims] -referenciáját.

## <a name="client-application"></a>ügyfélalkalmazás

A [OAuth2 engedélyezési keretrendszere][OAuth2-Role-Def]által meghatározottak szerint egy olyan alkalmazás, amely az [erőforrás tulajdonosának](#resource-owner)nevében teszi elérhetővé a védett erőforrás-kérelmeket. Az "ügyfél" kifejezés nem jelent semmilyen speciális hardver-megvalósítási jellemzőt (például azt, hogy az alkalmazás egy kiszolgálón, asztali gépen vagy más eszközön fut-e).

Az ügyfélalkalmazás [engedélyt](#authorization) kér egy erőforrás-tulajdonostól, hogy részt vegyen egy [OAuth2-engedélyezési](#authorization-grant) adatforgalomban, és hozzáférhet az API-khoz/adatokhoz az erőforrás tulajdonosának nevében. A OAuth2 engedélyezési keretrendszere [két típusú ügyfelet határoz][OAuth2-Client-Types]meg, "bizalmas" és "nyilvános", az ügyfél azon képessége alapján, hogy megőrizze a hitelesítő adatai titkosságát. Az alkalmazások olyan [webes ügyfélprogramot (bizalmas)](#web-client) implementálnak, amely webkiszolgálón, [natív ügyfélen (nyilvános)](#native-client) , vagy egy eszköz böngészőjében futó, [felhasználói ügynökön alapuló ügyfélen (nyilvános)](#user-agent-based-client) fut.

## <a name="consent"></a>hozzájárulása

Az [erőforrás tulajdonosának](#resource-owner) az a folyamata, amely engedélyezi egy [ügyfélalkalmazás](#client-application)számára a védett erőforrások elérését az erőforrás tulajdonosának nevében, adott [engedélyekkel](#permissions). Az ügyfél által kért engedélyektől függően a rendszergazda vagy a felhasználó beleegyezik, hogy engedélyezze a hozzáférést a szervezethez vagy az egyes adatbázisokhoz. Vegye figyelembe, hogy a [több-bérlős](#multi-tenant-application) forgatókönyvekben az alkalmazás [egyszerű szolgáltatását](#service-principal-object) is rögzíti a rendszer a beleegyezést küldő felhasználó bérlője számára.

További információért lásd: a beleegyezikés [keretrendszere](consent-framework.md) .

## <a name="id-token"></a>AZONOSÍTÓ token

Egy, az [engedélyezési kiszolgáló](#authorization-server) [engedélyezési végpontja](#authorization-endpoint)által biztosított [OpenID Connect][OpenIDConnect-ID-Token] [biztonsági jogkivonat](#security-token) , amely [](#claim) a végfelhasználói [erőforrás tulajdonosának](#resource-owner)hitelesítésére vonatkozó jogcímeket tartalmaz. A hozzáférési tokenekhez hasonlóan az azonosító tokenek is digitális aláírású JSON Web Tokenként [(JWT)][JWT]jelennek meg. A hozzáférési jogkivonattal ellentétben azonban az azonosító jogkivonat jogcímeit nem használja az erőforrás-hozzáféréshez és a speciális hozzáférés-vezérléshez kapcsolódó célokra.

További részletekért tekintse meg a [Microsoft Identity platform jogkivonat][AAD-Tokens-Claims] -referenciáját.

## <a name="microsoft-identity-platform"></a>Microsoft Identity Platform

A Microsoft identitásplatformja az Azure Active Directory (Azure AD) identitásszolgáltatás és fejlesztői platform fejlődésének eredménye. Lehetővé teszi a fejlesztők számára, hogy olyan alkalmazásokat építsenek, amelyek az összes Microsoft-identitás használatával biztonságosan jelentkeznek be, és jogkivonattal hívják meg a Microsoft Graphot, más Microsoft API-kat vagy olyan API-kat, amelyeket fejlesztők készítettek. Ez egy teljes körű funkcionalitást biztosító platform, amely egy hitelesítési szolgáltatásból, a könyvtárakból, az alkalmazás-regisztrációból és-konfigurációból, a teljes fejlesztői dokumentációból, a kódokból és más fejlesztői tartalmakból áll. A Microsoft identitásplatformja támogatja a nyílt szabványokat, többek között az OAuth 2.0-t és az OpenID Connectet. További részletekért tekintse meg a [Microsoft Identity platformot ismertető](about-microsoft-identity-platform.md) témakört.

## <a name="multi-tenant-application"></a>több-bérlős alkalmazás

Olyan alkalmazási osztály, amely lehetővé teszi az [](#consent) Azure ad- [bérlőben](#tenant)kiépített felhasználók bejelentkezését és belefoglalását, beleértve az ügyfél regisztrálásának helyétől eltérő bérlőket is. A [natív ügyfélalkalmazások](#native-client) a több-bérlős alapértelmezés szerint, míg a [webes ügyfél](#web-client) -és weberőforrás [/API-](#resource-server) Alkalmazások kiválaszthatnak egy vagy több-bérlőt. Ezzel szemben egy egybérlős szolgáltatásként regisztrált webalkalmazás csak olyan felhasználói fiókokból engedélyezheti a bejelentkezéseket, amelyek ugyanabban a bérlőben lettek kiépítve, mint ahol az alkalmazás regisztrálva van.

További részletekért lásd: [bármely Azure ad-felhasználó bejelentkezni a több-bérlős alkalmazás mintájának használatával][AAD-Multi-Tenant-Overview] .

## <a name="native-client"></a>natív ügyfél

Az eszközön natív módon telepített [ügyfélalkalmazás](#client-application) . Mivel az összes kód végrehajtása egy eszközön történik, "nyilvános" ügyfélnek számít, mivel a hitelesítő adatok magánjellegű és bizalmas tárolása nem lehetséges. További részletekért tekintse meg a [OAuth2-ügyfelek típusai és profiljai][OAuth2-Client-Types] című témakört.

## <a name="permissions"></a>engedélyek

Az [ügyfélalkalmazás](#client-application) az engedélyek megadásával éri el az [erőforrás-kiszolgálót](#resource-server) . Két típus érhető el:

* A "delegált" engedélyek, amelyek a bejelentkezett [erőforrás tulajdonosától](#resource-owner)származó, [hatókörön alapuló](#scopes) hozzáférést határoznak meg, a rendszer az erőforrást futásidőben " [SCP" jogcímként](#claim) mutatja be az ügyfél hozzáférési jogkivonatában. [](#access-token)
* Az ügyfélalkalmazás hitelesítő adataival/identitásával [szerepköralapú](#roles) hozzáférést megadó "alkalmazás" engedélyek az erőforráshoz futásidőben ["szerepkörök" jogcímként](#claim) jelennek meg az ügyfél hozzáférési jogkivonatában.

Emellett felszínre kerülnek az [engedélyezési](#consent) folyamat során, így a rendszergazda vagy az erőforrás tulajdonosa a bérlő erőforrásaihoz való hozzáférés engedélyezése/tiltása.

Az engedélyezési kérelmeket az alkalmazás **API-engedélyei** lapon kell konfigurálni a [Azure Portalban][AZURE-portal], a kívánt "delegált engedélyek" és az "alkalmazás engedélyei" lehetőség kiválasztásával (az utóbbi megköveteli a globális rendszergazdai szerepkör tagságát). Mivel a [nyilvános ügyfél](#client-application) nem tudja biztonságosan karbantartani a hitelesítő adatokat, csak delegált engedélyeket igényelhet, míg a [bizalmas ügyfél](#client-application) jogosult a delegált és az alkalmazásra vonatkozó engedélyek igénylésére is. Az ügyfél [Application objektuma](#application-object) a deklarált engedélyeket a [requiredResourceAccess tulajdonságában][AAD-Graph-App-Entity]tárolja.

## <a name="resource-owner"></a>erőforrás tulajdonosa

A [OAuth2 engedélyezési keretrendszere][OAuth2-Role-Def]által meghatározottak szerint egy olyan entitás, amely képes a védett erőforrásokhoz való hozzáférés megadására. Ha az erőforrás tulajdonosa személy, akkor azt végfelhasználónak nevezzük. Ha például egy [ügyfélalkalmazás](#client-application) a [Microsoft Graph API][Microsoft-Graph]-n keresztül szeretne hozzáférni egy felhasználó postaládájához, akkor a postaláda erőforrás-tulajdonosának engedélyre van szüksége.

## <a name="resource-server"></a>erőforrás-kiszolgáló

A [OAuth2 engedélyezési keretrendszere][OAuth2-Role-Def]által meghatározottak szerint a védett erőforrásokat üzemeltető kiszolgáló, amely képes a [hozzáférési](#access-token)jogkivonatot futtató [ügyfélalkalmazások](#client-application) által a védett erőforrásokra vonatkozó kérések fogadására és megválaszolására. Más néven védett erőforrás-kiszolgáló vagy erőforrás-alkalmazás.

Az erőforrás-kiszolgáló elérhetővé teszi az API-kat, és kikényszeríti a védett erőforrások elérését [hatókörökön](#scopes) és [szerepkörökön](#roles)keresztül a OAuth 2,0 engedélyezési keretrendszer használatával. Ilyenek például az Azure AD Graph API, amely hozzáférést biztosít az Azure AD-bérlői információhoz, valamint az Office 365 API-kat, amelyek hozzáférést biztosítanak a levelezéshez és a naptárhoz. Mindkettő a [Microsoft Graph API][Microsoft-Graph]-n keresztül is elérhető.

Akárcsak egy ügyfélalkalmazás esetében, az erőforrás-alkalmazás identitásának konfigurációja az Azure [](#application-registration) ad-bérlőben való regisztrációval történik, amely az alkalmazás és a szolgáltatás egyszerű objektumát is megadja. Egyes Microsoft által biztosított API-k (például az Azure AD Graph API) előzetesen regisztrált egyszerű szolgáltatásokkal rendelkeznek, amelyek az üzembe helyezés során minden bérlőn elérhetők.

## <a name="roles"></a>roles

A [hatókörökhöz](#scopes)hasonlóan a szerepkörök lehetővé teszik az [erőforrás-kiszolgáló](#resource-server) számára a védett erőforrásokhoz való hozzáférés szabályozását. Kétféle típus létezik: a "felhasználói" szerepkör implementálja a szerepköralapú hozzáférés-vezérlést olyan felhasználók/csoportok számára, amelyek hozzáférést igényelnek az erőforráshoz, míg az "alkalmazás" szerepkör ugyanazokat valósít meg a hozzáférést igénylő [ügyfélalkalmazások](#client-application) számára.

A szerepkörök erőforrás-definiált karakterláncok (például "költségek jóváhagyója", "csak olvasható", "könyvtár. ReadWrite. all"), a [Azure Portal][AZURE-portal] az erőforrás alkalmazási jegyzékfájlján [](#application-manifest)keresztül kezelhetők, és az erőforrás [appRoles tulajdonságában][AAD-Graph-Sp-Entity] vannak tárolva. . A Azure Portal a felhasználók "felhasználói" szerepkörökhöz való hozzárendelésére, valamint az "alkalmazás" szerepkör elérésére vonatkozó ügyfélalkalmazás- [engedélyek](#permissions) konfigurálására is használható.

Az Azure AD Graph API által elérhetővé tett alkalmazási szerepkörök részletes ismertetését lásd: [Graph API jogosultsági hatókörök][AAD-Graph-Perm-Scopes]. Lépésenkénti megvalósítási példát a következő témakörben talál: [hozzáférés kezelése a RBAC és a Azure Portal használatával][AAD-RBAC].

## <a name="scopes"></a>hatókörök

A [szerepkörökhöz](#roles)hasonlóan a hatókörök lehetővé teszik az [erőforrás-kiszolgáló](#resource-server) számára a védett erőforrásokhoz való hozzáférés szabályozását. A hatókörök a hatókörön [alapuló hozzáférés-][OAuth2-Access-Token-Scopes] vezérlés megvalósítására szolgálnak olyan [ügyfélalkalmazás](#client-application) esetében, amely a tulajdonosával delegált hozzáférést kapott az erőforráshoz.

A hatókörök erőforrás-definiált karakterláncok (például "mail. Read", "Directory. ReadWrite. all"), a [Azure Portal][AZURE-portal] felügyelve az erőforrás [alkalmazási jegyzékfájlján](#application-manifest)keresztül, és az erőforrás [oauth2Permissions tulajdonságában][AAD-Graph-Sp-Entity]vannak tárolva. A Azure Portal az ügyfélalkalmazások [delegált engedélyeinek](#permissions) konfigurálására is használható a hatókör eléréséhez.

Az ajánlott eljárás elnevezési konvenció az "erőforrás. Operation. megkötés" formátum használata. Az Azure AD Graph API által közzétett hatókörök részletes ismertetését lásd: [Graph API jogosultsági hatókörök][AAD-Graph-Perm-Scopes]. Az Office 365-szolgáltatások által közzétett hatókörökkel kapcsolatban lásd: [office 365 API-engedélyek referenciája][O365-Perm-Ref].

## <a name="security-token"></a>biztonsági jogkivonat

Jogcímeket tartalmazó aláírt dokumentum, például egy OAuth2-jogkivonat vagy SAML 2,0-állítás. A OAuth2 [engedélyezéséhez](#authorization-grant)egy [hozzáférési jogkivonat](#access-token) (OAuth2) és egy [azonosító jogkivonat](https://openid.net/specs/openid-connect-core-1_0.html#IDToken) a biztonsági jogkivonatok típusai, amelyek közül mindkettő JSON web tokenként [(JWT)][JWT]van implementálva.

## <a name="service-principal-object"></a>egyszerű szolgáltatásnév objektum

Ha a Azure Portalban regisztrál/frissít egy alkalmazást [][AZURE-portal], a portál egy [Application objektumot](#application-object) és egy hozzá tartozó egyszerű szolgáltatásnevet is létrehoz/frissít a bérlő számára. Az Application objektum globálisan definiálja az alkalmazás identitásának konfigurációját (az összes olyan bérlőn, ahol a társított alkalmazás hozzáférése van), és az a sablon, amelyből a megfelelő egyszerű szolgáltatásnév-objektum (ok)helyi használatra származtatva (egy adott bérlő esetében).

További információ: [alkalmazás-és egyszerű szolgáltatások objektumai][AAD-App-SP-Objects].

## <a name="sign-in"></a>sign-in

Egy [ügyfélalkalmazás](#client-application) folyamata, amely a végfelhasználói hitelesítést és a kapcsolódó állapot rögzítését kezdeményezi a [biztonsági jogkivonat](#security-token) beszerzésének, valamint az alkalmazás munkamenetének az adott állapotra való hatókörének megkezdése érdekében. Az állapot tartalmazhat olyan összetevőket, mint például a felhasználói profil adatai, valamint a jogkivonat jogcímeiből származtatott információk.

Az alkalmazások bejelentkezési funkciója általában egyszeri bejelentkezés (SSO) megvalósítására szolgál. Azt is megteheti, hogy egy "regisztrációs" függvény előzi meg, mivel a végfelhasználók belépési pontként férhetnek hozzá az alkalmazásokhoz (az első bejelentkezéskor). A regisztrációs függvény a felhasználóra vonatkozó további állapot összegyűjtésére és megtartására szolgál, és [felhasználói hozzájárulásukat](#consent)is igényelhet.

## <a name="sign-out"></a>kijelentkezés

A végfelhasználók hitelesítésének visszaigazolása, a [Bejelentkezés](#sign-in) során az [ügyfélalkalmazás](#client-application) -munkamenethez társított felhasználói állapot leválasztása

## <a name="tenant"></a>tenant

Az Azure AD-címtár egy példányát Azure AD-bérlőnek nevezzük. Számos funkciót kínál, többek között:

* beállításjegyzék-szolgáltatás integrált alkalmazásokhoz
* felhasználói fiókok és regisztrált alkalmazások hitelesítése
* A különböző protokollok támogatásához szükséges REST-végpontok, például a OAuth2 és az SAML, beleértve az [engedélyezési végpontot](#authorization-endpoint), a [jogkivonat](#token-endpoint) -végpontot és a [több-bérlős alkalmazások](#multi-tenant-application)által használt "Common" végpontot.

Az Azure AD-bérlők az Azure-beli és az Office 365-előfizetésekkel hozhatók létre/vannak társítva a regisztráció során, Identitás-és hozzáféréskezelés szolgáltatásokat biztosítanak az előfizetéshez. Az Azure-előfizetés rendszergazdái további Azure AD-bérlőket is létrehozhatnak a Azure Portal használatával. Megtudhatja, [hogyan szerezhet be egy Azure Active Directory bérlőt][AAD-How-To-Tenant] a bérlőhöz való hozzáférés különböző módjaival kapcsolatos részletekért. Az előfizetések és az Azure AD-bérlő közötti kapcsolat részleteiért tekintse meg [, hogy az Azure-előfizetések hogyan vannak társítva Azure Active Directory][AAD-How-Subscriptions-Assoc] .

## <a name="token-endpoint"></a>jogkivonat-végpont

Az [engedélyezési kiszolgáló](#authorization-server) által megvalósított egyik végpont, amely támogatja a OAuth2- [engedélyezési](#authorization-grant)támogatást. A támogatástól függően felhasználható egy [hozzáférési jogkivonat](#access-token) (és a kapcsolódó "frissítés" token) beszerzése egy ügyfélhez [](#client-application), vagy [azonosító token](#id-token) az [OpenID Connect][OpenIDConnect] protokollal való használatkor.

## <a name="user-agent-based-client"></a>Felhasználó-ügynök alapú ügyfél

Olyan [ügyfélalkalmazás](#client-application) , amely egy webkiszolgálóról tölt le kódot, és egy felhasználói ügynökön belül (például egy webböngészőben) hajtja végre, például egy egyoldalas alkalmazás (Spa) számára. Mivel az összes kód végrehajtása egy eszközön történik, "nyilvános" ügyfélnek számít, mivel a hitelesítő adatok magánjellegű és bizalmas tárolása nem lehetséges. További információ: [OAuth2-ügyfelek típusai és profiljai][OAuth2-Client-Types].

## <a name="user-principal"></a>egyszerű Felhasználónév

Hasonlóan ahhoz, ahogyan egy egyszerű szolgáltatásnév-objektumot használ egy alkalmazás példányának ábrázolására, a felhasználói tag objektum egy másik rendszerbiztonsági tag, amely egy felhasználót jelöl. Az Azure AD Graph [felhasználói entitás][AAD-Graph-User-Entity] definiálja egy felhasználói objektum sémáját, beleértve a felhasználóval kapcsolatos tulajdonságokat, például az utónév és a vezetéknév, az egyszerű felhasználónév, a címtárbeli szerepkör tagsága stb. Ez biztosítja az Azure AD felhasználói identitásának konfigurációját, hogy futásidőben hozzon létre egy felhasználói rendszerbiztonsági tag. Az egyszerű felhasználónév az egyszeri bejelentkezéshez, a belefoglalási felhatalmazáshoz, a hozzáférés [](#consent) -vezérlési döntések meghozatalához stb.

## <a name="web-client"></a>webes ügyfél

Egy olyan [ügyfélalkalmazás](#client-application) , amely a webkiszolgálón lévő összes kódot végrehajtja, és a hitelesítő adatai biztonságos tárolásával a kiszolgálón a "bizalmas" ügyfélként működhet. További információ: [OAuth2-ügyfelek típusai és profiljai][OAuth2-Client-Types].

## <a name="next-steps"></a>További lépések

A Microsoft Identity [platform fejlesztői útmutatója][AAD-Dev-Guide] az összes Microsoft Identity platform-fejlesztéssel kapcsolatos témakör kezdőlapja, beleértve az [alkalmazások integrálásának][AAD-How-To-Integrate] áttekintését és a Microsoft Identity alapjait [ a platform hitelesítése és a támogatott hitelesítési forgatókönyvek][AAD-Auth-Scenarios]. A [githubon](https://github.com/azure-samples?utf8=%E2%9C%93&q=active%20directory&type=&language=)megtalálhatja a kód mintáit & oktatóanyagokat is.

A következő Megjegyzések szakaszban visszajelzést és segítséget nyújthat a tartalom pontosításához és formálásához, beleértve az új definíciók kéréseit vagy a meglévők frissítését is.

<!--Image references-->

<!--Reference style links -->
[AAD-App-Manifest]:reference-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AAD-Graph-User-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity
[AAD-How-Subscriptions-Assoc]:../fundamentals/active-directory-how-subscriptions-associated-directory.md
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-How-To-Tenant]:quickstart-create-new-tenant.md
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AAD-Multi-Tenant-Overview]:howto-convert-app-to-be-multi-tenant.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]:access-tokens.md
[AZURE-portal]: https://portal.azure.com
[AAD-RBAC]: ../../role-based-access-control/role-assignments-portal.md
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[Microsoft-Graph]: https://developer.microsoft.com/graph
[O365-Perm-Ref]: https://msdn.microsoft.com/office/office365/howto/application-manifest
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Endpoint]: https://tools.ietf.org/html/rfc6749#section-3.1
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: https://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-AuthZ-Endpoint]: https://openid.net/specs/openid-connect-core-1_0.html#AuthorizationEndpoint
[OpenIDConnect-ID-Token]: https://openid.net/specs/openid-connect-core-1_0.html#IDToken
