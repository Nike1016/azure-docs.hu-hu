---
title: Közvetlen összevonás beállítása a B2B-Azure Active Directory identitás-szolgáltatóval | Microsoft Docs
description: Közvetlenül összevonása SAML vagy WS-fed identitás-szolgáltatóval, hogy a vendégek be tudják jelentkezni az Azure AD-alkalmazásokba
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3aea108ed87debac56b18b5959d492f2bcb291d
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68853606"
---
# <a name="direct-federation-with-ad-fs-and-third-party-providers-for-guest-users-preview"></a>Közvetlen összevonás AD FS és külső szolgáltatókkal a vendég felhasználói számára (előzetes verzió)
|     |
| --- |
| A közvetlen összevonás a Azure Active Directory nyilvános előzetes verziója. További információ az előzetes verziókról: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

Ez a cikk azt ismerteti, hogyan állítható be a közvetlen összevonás egy másik szervezettel a B2B-együttműködéshez. Beállíthat közvetlen összevonást bármely olyan szervezettel, amelynek az identitás-szolgáltatója (identitásszolgáltató) támogatja az SAML 2,0 vagy a WS-fed protokollt.
Ha közvetlen kapcsolatot hoz létre egy partner identitásszolgáltató, az adott tartományhoz tartozó új vendég felhasználók saját identitásszolgáltató által felügyelt szervezeti fiókkal jelentkezhetnek be az Azure AD-bérlőbe, és megkezdhetik a velük való együttműködést. Nincs szükség arra, hogy a vendég felhasználó külön Azure AD-fiókot hozzon létre.
> [!NOTE]
> A közvetlen összevonási vendég felhasználóknak be kell jelentkezniük egy olyan hivatkozás használatával, amely tartalmazza a bérlői `https://myapps.microsoft.com/?tenantid=<tenant id>` környezetet `https://portal.azure.com/<tenant id>`(például vagy egy ellenőrzött tartomány `https://myapps.microsoft.com/\<verified domain>.onmicrosoft.com`esetén). Az alkalmazásokra és az erőforrásokra mutató közvetlen hivatkozásokat is használhatja, amennyiben azok tartalmazzák a bérlői környezetet. A közvetlen összevonási felhasználók jelenleg nem tudnak bejelentkezni olyan közös végpontok használatával, amelyek nem rendelkeznek bérlői környezettel. Például a, `https://portal.azure.com`a `https://myapps.microsoft.com`, a vagy `https://teams.microsoft.com` a használatakor hibaüzenetet fog eredményezni.
 
## <a name="when-is-a-guest-user-authenticated-with-direct-federation"></a>Mikor van hitelesítve a vendég felhasználó közvetlen összevonással?
Miután beállította a közvetlen összevonást egy szervezettel, a meghívott új vendég-felhasználók a közvetlen összevonás használatával lesznek hitelesítve. Fontos megjegyezni, hogy a közvetlen összevonás beállítása nem módosítja a hitelesítési módszert azon vendég felhasználók számára, akik már beváltottak egy meghívót. Néhány példa:
 - Ha a vendég felhasználói már beváltották a meghívókat, és ezt követően a szervezethez tartozó közvetlen összevonást is beállította, akkor a vendég felhasználói továbbra is ugyanazt a hitelesítési módszert használják, mint a közvetlen összevonás beállítása előtt.
 - Ha közvetlen összevonást állít be egy partner szervezettel, és meghívja a vendég felhasználókat, majd a partnerszervezet később az Azure AD-ba kerül, a meghívókat már beváltó vendég felhasználók továbbra is közvetlen kapcsolatot fognak használni, ha a közvetlen létezik összevonási házirend a bérlőben.
 - Ha közvetlen kapcsolatot töröl egy partner szervezettel, a közvetlen kapcsolatot használó vendég felhasználók nem fognak tudni bejelentkezni.

Ezen forgatókönyvek bármelyikében frissítheti a vendég felhasználói hitelesítési módszerét, ha törli a vendég felhasználói fiókot a címtárból, és meghívja őket.

A közvetlen összevonás tartományi névterekhez van kötve, például contoso.com és fabrikam.com. AD FS vagy harmadik féltől származó identitásszolgáltató rendelkező közvetlen összevonási konfiguráció létrehozásakor a szervezetek egy vagy több tartományi névteret társítanak ezekhez a IDP. 

## <a name="end-user-experience"></a>Végfelhasználói élmény 
A közvetlen összevonással a vendég felhasználók saját szervezeti fiókjával jelentkezhetnek be az Azure AD-bérlőbe. Ha megosztott erőforrásokhoz férnek hozzá, és a rendszer kéri a bejelentkezést, a rendszer átirányítja a közvetlen összevonási felhasználókat a identitásszolgáltató. A sikeres bejelentkezés után a rendszer visszaküldi őket az Azure AD-nek az erőforrások eléréséhez. A közvetlen összevonási felhasználók frissítési jogkivonatai 12 órára érvényesek, az [áteresztő frissítési token alapértelmezett hossza](../develop/active-directory-configurable-token-lifetimes.md#exceptions) az Azure ad-ben. Ha az összevont identitásszolgáltató egyszeri bejelentkezés engedélyezve van, akkor a felhasználó egyszeri bejelentkezést fog tapasztalni, és a kezdeti hitelesítés után nem jelenik meg a bejelentkezési üzenet.

## <a name="limitations"></a>Korlátozások

### <a name="dns-verified-domains-in-azure-ad"></a>DNS által ellenőrzött tartományok az Azure AD-ben
A összevonása kívánt tartomány ***nem*** lehet DNS-ellenőrzés alatt állni az Azure ad-ben. Engedélyezheti a közvetlen összevonás felügyelet nélküli (e-mailben ellenőrzött vagy "vírusos") Azure AD-bérlők általi beállítását, mivel azok nem DNS-ellenőrzés alatt állnak.
### <a name="authentication-url"></a>Hitelesítési URL-cím
A közvetlen összevonás csak olyan házirendek esetében engedélyezett, amelyekben a hitelesítési URL tartománya megegyezik a céltartományban, vagy ha a hitelesítési URL-cím az egyik engedélyezett Identity Provider (ez a lista változhat):
-   accounts.google.com
-   pingidentity.com
-   login.pingone.com
-   okta.com
-   my.salesforce.com
-   federation.exostar.com
-   federation.exostartest.com

Ha például a **fabrikam.com**közvetlen összevonását állítja be, a hitelesítési URL `https://fabrikam.com/adfs` -cím továbbítja az ellenőrzést. Az azonos tartományba tartozó gazdagépek is átadhatják például `https://sts.fabrikam.com/adfs`a-t. Azonban a hitelesítési URL- `https://fabrikamconglomerate.com/adfs` cím `https://fabrikam.com.uk/adfs` vagy ugyanahhoz a tartományhoz nem lesz továbbítva.

### <a name="signing-certificate-renewal"></a>Tanúsítvány megújításának aláírása
Ha a metaadatok URL-címét a személyazonosság-szolgáltató beállításaiban adta meg, az Azure AD automatikusan megújítja az aláíró tanúsítványt, amikor lejár. Ha azonban a rendszer a lejárati idő előtt bármilyen okból elforgatja a tanúsítványt, vagy ha nem ad meg metaadat-URL-címet, az Azure AD nem tudja megújítani. Ebben az esetben manuálisan kell frissítenie az aláíró tanúsítványt.

### <a name="limit-on-federation-relationships"></a>Összevonási kapcsolatok korlátozása
Jelenleg legfeljebb 1 000 összevonási kapcsolat támogatott. Ez a korlát magában foglalja a [belső szövetségeket](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) és a közvetlen szövetségeket is.
## <a name="frequently-asked-questions"></a>Gyakori kérdések
### <a name="can-i-set-up-direct-federation-with-a-domain-for-which-an-unmanaged-email-verified-tenant-exists"></a>Beállíthat közvetlen összevonást olyan tartománnyal, amelyhez nem felügyelt (e-mailben ellenőrzött) bérlő létezik? 
Igen. Ha a tartomány még nem lett ellenőrizve, és a bérlő nem ment át a [rendszergazdai](../users-groups-roles/domains-admin-takeover.md)átvételre, beállíthatja, hogy a közvetlen összevonás legyen a tartománnyal. Nem felügyelt, vagy e-mailben ellenőrzött bérlők akkor jönnek létre, amikor egy felhasználó bevált egy B2B-meghívást, vagy önkiszolgáló regisztrációt hajt végre az Azure AD-ben olyan tartomány használatával, amely jelenleg nem létezik. Ezeket a tartományokat közvetlen összevonással is megadhatja. Ha a közvetlen összevonás DNS által ellenőrzött tartománnyal való beállítását kísérli meg a Azure Portal vagy a PowerShellen keresztül, akkor hibaüzenet jelenik meg.
### <a name="if-direct-federation-and-email-one-time-passcode-authentication-are-both-enabled-which-method-takes-precedence"></a>Ha a közvetlen összevonás és az egyszeri bejelentkezéses e-mail-hitelesítés egyaránt engedélyezve van, akkor melyik módszer elsőbbséget élvez?
A közvetlen összevonás partner szervezettel való létrehozásakor elsőbbséget élvez az e-mailek egyszeri jelszavas hitelesítése az adott szervezet új vendég felhasználói számára. Ha a vendég felhasználó egyszeri jelszó-hitelesítéssel váltott ki egy meghívót a közvetlen összevonás beállítása előtt, akkor továbbra is egyszer használatos hitelesítő kódot használ. 
### <a name="does-direct-federation-address-sign-in-issues-due-to-a-partially-synced-tenancy"></a>Egy részlegesen szinkronizált bérlet miatt a közvetlen összevonási címek bejelentkezési problémái vannak?
Nem, ebben a forgatókönyvben az [egyszeri jelszavas e-mail-](one-time-passcode.md) szolgáltatást kell használni. A "részlegesen szinkronizált bérlet" olyan partner Azure AD-bérlőre vonatkozik, ahol a helyszíni felhasználói identitások nem teljesen szinkronizálva vannak a felhővel. Egy vendég, amelynek identitása még nem létezik a felhőben, de a B2B-meghívás beváltására irányuló kísérlet nem fog tudni bejelentkezni. Az egyszeri jelszó funkció lehetővé teszi a vendég számára a bejelentkezést. A közvetlen összevonási funkciók olyan forgatókönyveket foglalnak magukban, ahol a vendég saját identitásszolgáltató által felügyelt szervezeti fiókkal rendelkezik, de a szervezet egyáltalán nem rendelkezik Azure AD-beli jelenléttel.

## <a name="step-1-configure-the-partner-organizations-identity-provider"></a>1\. lépés: A partner szervezet identitás-szolgáltatójának konfigurálása
Először is a partnervállalat a szükséges jogcímekhez és a függő entitások megbízhatóságához kell konfigurálnia az identitás-szolgáltatót. 

> [!NOTE]
> Ha szeretné bemutatni, hogyan konfigurálhat egy identitás-szolgáltatót a közvetlen összevonáshoz, használja a Active Directory összevonási szolgáltatások (AD FS) (AD FS) példaként. Tekintse meg a [közvetlen összevonás konfigurálása ad FSsal](direct-federation-adfs.md)című cikket, amely példákat mutat be arra, hogyan konfigurálhatja az AD FS SAML 2,0 vagy ws-fed identitás-szolgáltatóként a közvetlen összevonás előkészítéséhez.

### <a name="saml-20-configuration"></a>SAML 2,0 konfiguráció

Az Azure AD B2B konfigurálható úgy, hogy az SAML protokollt használó összevonása az alább felsorolt konkrét követelményekkel. Az SAML-identitás szolgáltatója és az Azure AD közötti megbízhatóság beállításával kapcsolatos további információkért lásd: [SAML 2,0 Identity Provider (identitásszolgáltató) használata egyszeri bejelentkezéshez](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-saml-idp).  

> [!NOTE]
> Vegye figyelembe, hogy a közvetlen összevonás céljának tartománya nem lehet DNS-ellenőrzés alatt állni az Azure AD-ben. A hitelesítési URL-tartománynak meg kell egyeznie a céltartományban, vagy egy engedélyezett identitás-szolgáltató tartományának kell lennie. A részletekért tekintse meg a [korlátozásokat](#limitations) ismertető szakaszt. 

#### <a name="required-saml-20-attributes-and-claims"></a>Szükséges SAML 2,0 attribútumok és jogcímek
Az alábbi táblázatokban a harmadik féltől származó identitás-szolgáltatónál konfigurálni kívánt attribútumok és jogcímek követelményei láthatók. Közvetlen összevonás beállításához a következő attribútumoknak kell szerepelniük az SAML 2,0-válaszban az identitás-szolgáltatótól. Ezek az attribútumok konfigurálhatók az online biztonsági jogkivonat szolgáltatás XML-fájljához való csatolással, vagy manuálisan is.

Az SAML 2,0 válaszához szükséges attribútumok a identitásszolgáltató:

|Attribútum  |Érték  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|Célközönség     |`urn:federation:MicrosoftOnline`         |
|Kiállító     |A partner identitásszolgáltató kiállítói URI-ja, például:`http://www.example.com/exk10l6w90DHM0yi...`         |


A identitásszolgáltató által kiadott SAML 2,0-tokenhez szükséges jogcímek:

|Attribútum  |Value  |
|---------|---------|
|NameID formátuma     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|EmailAddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

### <a name="ws-fed-configuration"></a>WS-fed konfiguráció 
Az Azure AD B2B konfigurálható úgy, hogy a WS-fed protokollt használó összevonása az alább felsorolt követelményekkel. Jelenleg az Azure AD-vel való kompatibilitás érdekében tesztelték a két WS-fed szolgáltatót, AD FS és Shibboleth is. A függő entitás megbízhatóságának az Azure AD-vel való létrehozásával kapcsolatos további információkért tekintse meg az [Azure ad Identity Provider kompatibilitási dokumentációjában](https://www.microsoft.com/download/details.aspx?id=56843)elérhető "STS-integrációs dokumentum a WS Protocols használatával" című részt.

> [!NOTE]
> A közvetlen összevonás céljának tartománya nem lehet DNS-ellenőrzés alatt állni az Azure AD-ben. A hitelesítési URL-tartománynak meg kell egyeznie a céltartományban vagy egy engedélyezett identitás-szolgáltató tartományával. A részletekért tekintse meg a [korlátozásokat](#limitations) ismertető szakaszt. 

#### <a name="required-ws-fed-attributes-and-claims"></a>Szükséges WS-fed attribútumok és jogcímek

Az alábbi táblázatokban az adott attribútumokra és jogcímekre vonatkozó követelményeket tekintheti meg, amelyeket a külső gyártótól származó "WS-fed" identitás-szolgáltatónál kell konfigurálni. Közvetlen összevonás beállításához a következő attribútumokat kell megkapnia a WS-fed üzenetben az identitás-szolgáltatótól. Ezek az attribútumok konfigurálhatók az online biztonsági jogkivonat szolgáltatás XML-fájljához való csatolással, vagy manuálisan is.

Szükséges attribútumok a WS-fed üzenetben a identitásszolgáltató:
 
|Attribútum  |Value  |
|---------|---------|
|PassiveRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|Célközönség     |`urn:federation:MicrosoftOnline`         |
|Kiállító     |A partner identitásszolgáltató kiállítói URI-ja, például:`http://www.example.com/exk10l6w90DHM0yi...`         |

A identitásszolgáltató által kiadott WS-fed jogkivonat számára szükséges jogcímek:

|Attribútum  |Value  |
|---------|---------|
|ImmutableID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|EmailAddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

## <a name="step-2-configure-direct-federation-in-azure-ad"></a>2\. lépés: Közvetlen összevonás konfigurálása az Azure AD-ben 
Ezután konfigurálnia kell az összevonást az Azure AD 1. lépésében konfigurált identitás-szolgáltatóval. Használhatja az Azure AD-portált vagy a PowerShellt is. A közvetlen összevonási házirend érvénybe léptetése 5-10 percet is igénybe vehet. Ez idő alatt nem próbál beváltani egy meghívót a közvetlen összevonási tartományhoz. A következő attribútumok szükségesek:
- Partneri identitásszolgáltató kibocsátói URI-azonosítója
- A partneri identitásszolgáltató passzív hitelesítési végpontja (csak HTTPS támogatott)
- Tanúsítvány

### <a name="to-configure-direct-federation-in-the-azure-ad-portal"></a>Közvetlen összevonás konfigurálása az Azure AD-portálon

1. Nyissa meg az [Azure Portal](https://portal.azure.com/). A bal oldali panelen válassza az **Azure Active Directory** lehetőséget. 
2. Válassza a **szervezeti kapcsolatok**lehetőséget.
3. Válassza az **identitás-szolgáltatók**lehetőséget, majd válassza az **Új SAML/ws-fed identitásszolgáltató**elemet.

    ![Új SAML-vagy WS-fed-identitásszolgáltató hozzáadását bemutató gomb](media/direct-federation/new-saml-wsfed-idp.png)

4. Az **Új SAML/ws-fed identitásszolgáltató** oldalon az **Identity Provider protokoll**területen válassza az **SAML** vagy a **ws-fed**lehetőséget.

    ![Az SAML vagy a WS-fed identitásszolgáltató oldalon található elemzés gombot ábrázoló képernyőfelvétel](media/direct-federation/new-saml-wsfed-idp-parse.png)

5. Adja meg a partner szervezete tartománynevét, amely a közvetlen összevonás célként megadott tartományneve lesz.
6. Feltölthet egy metaadat-fájlt a metaadatok részleteinek feltöltéséhez. Ha a metaadatok manuális bevitelét választja, adja meg a következő adatokat:
   - Partneri identitásszolgáltató tartományneve
   - Partneri identitásszolgáltató entitásazonosítója
   - Partneri identitásszolgáltató passzív kérelmező végpontja
   - Tanúsítvány
   > [!NOTE]
   > A metaadatok URL-címe nem kötelező, de erősen ajánlott. Ha megadja a metaadatok URL-címét, az Azure AD automatikusan megújíthatja az aláíró tanúsítványt, amikor lejár. Ha a tanúsítvány a lejárati idő előtt bármilyen okból forog, vagy ha nem ad meg metaadat-URL-címet, az Azure AD nem fogja tudni megújítani. Ebben az esetben manuálisan kell frissítenie az aláíró tanúsítványt.

7. Kattintson a **Mentés** gombra. 

### <a name="to-configure-direct-federation-in-azure-ad-using-powershell"></a>Közvetlen összevonás konfigurálása az Azure AD-ben a PowerShell használatával

1. Telepítse az Azure AD PowerShell for Graph modul ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)) legújabb verzióját. (Ha részletes lépések szükségesek, a vendég felhasználó hozzáadására szolgáló rövid útmutató tartalmazza a [legújabb AzureADPreview-modul telepítését](b2b-quickstart-invite-powershell.md#install-the-latest-azureadpreview-module)ismertető szakaszt.) 
2. Futtassa a következő parancsot: 
   ```powershell
   Connect-AzureAD
   ```
1. A bejelentkezési kérésben jelentkezzen be a felügyelt globális rendszergazdai fiókkal. 
2. Futtassa az alábbi parancsokat, és cserélje le az összevonási metaadatok fájljának értékeit. AD FS-kiszolgáló és a okta esetében az összevonási fájl az federationmetadata. XML, például: `https://sts.totheclouddemo.com/federationmetadata/2007-06/federationmetadata.xml`. 

   ```powershell
   $federationSettings = New-Object Microsoft.Open.AzureAD.Model.DomainFederationSettings
   $federationSettings.PassiveLogOnUri ="https://sts.totheclouddemo.com/adfs/ls/"
   $federationSettings.LogOffUri = $federationSettings.PassiveLogOnUri
   $federationSettings.IssuerUri = "http://sts.totheclouddemo.com/adfs/services/trust"
   $federationSettings.MetadataExchangeUri="https://sts.totheclouddemo.com/adfs/services/trust/mex"
   $federationSettings.SigningCertificate= <Replace with X509 signing cert’s public key>
   $federationSettings.PreferredAuthenticationProtocol="WsFed" OR "Samlp"
   $domainName = <Replace with domain name>
   New-AzureADExternalDomainFederation -ExternalDomainName $domainName  -FederationSettings $federationSettings
   ```

## <a name="step-3-test-direct-federation-in-azure-ad"></a>3\. lépés: Közvetlen összevonás tesztelése az Azure AD-ben
Most tesztelje a közvetlen összevonási telepítőt egy új B2B vendég felhasználó meghívásával. Részletekért lásd: [Azure ad B2B együttműködési felhasználók hozzáadása a Azure Portal](add-users-administrator.md).
 
## <a name="how-do-i-edit-a-direct-federation-relationship"></a>Hogyan a közvetlen összevonási kapcsolat szerkesztését?

1. Nyissa meg az [Azure Portal](https://portal.azure.com/). A bal oldali panelen válassza az **Azure Active Directory** lehetőséget. 
2. Válassza a **szervezeti kapcsolatok**lehetőséget.
3. **Identitás-szolgáltatók** kiválasztása
4. Az **SAML/ws-fed identitás-szolgáltatók**területen válassza ki a szolgáltatót.
5. Az identitás-szolgáltató részletei ablaktáblán frissítse az értékeket.
6. Kattintson a **Mentés** gombra.


## <a name="how-do-i-remove-direct-federation"></a>Hogyan a közvetlen összevonás eltávolítása?
Törölheti a közvetlen összevonási telepítést. Ha így tesz, a meghívónak már beváltott felhasználók nem fognak tudni bejelentkezni. Ha azonban ismét hozzáférést ad az erőforrásokhoz, törölje őket a címtárból, és hívja fel őket. Az Azure AD-portálon lévő közvetlen összevonás eltávolítása az identitás-szolgáltatóval:

1. Nyissa meg az [Azure Portal](https://portal.azure.com/). A bal oldali panelen válassza az **Azure Active Directory** lehetőséget. 
2. Válassza a **szervezeti kapcsolatok**lehetőséget.
3. Válassza az **identitás-szolgáltatók**elemet.
4. Válassza ki az identitás-szolgáltatót, majd válassza a **Törlés**lehetőséget. 
5. A törlés megerősítéséhez válassza az **Igen** lehetőséget. 

Az identitás-szolgáltatóval való közvetlen összevonás eltávolítása a PowerShell használatával:
1. Telepítse az Azure AD PowerShell for Graph modul ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)) legújabb verzióját.
2. Futtassa a következő parancsot: 
   ```powershell
   Connect-AzureAD
   ```
3. A bejelentkezési kérésben jelentkezzen be a felügyelt globális rendszergazdai fiókkal. 
4. Írja be a következő parancsot:
   ```powershell
   Remove-AzureADExternalDomainFederation -ExternalDomainName  $domainName
   ```
