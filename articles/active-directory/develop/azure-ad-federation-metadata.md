---
title: Az Azure AD összevonási metaadatai | Microsoft Docs
description: Ez a cikk az összevonási metaadatokat tartalmazó dokumentumot ismerteti, amely Azure Active Directory Azure Active Directory jogkivonatokat fogadó szolgáltatások számára tesz közzé.
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: c2d5f80b-aa74-452c-955b-d8eb3ed62652
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/07/2017
ms.author: ryanwi
ms.reviewer: hirsin, dastrock
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: fefaf618ff29cc2186dc555eb6f452223f4cd097
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68835127"
---
# <a name="federation-metadata"></a>Összevonási metaadatok
Azure Active Directory (Azure AD) egy összevonási metaadat-dokumentumot tesz közzé olyan szolgáltatások számára, amelyek az Azure AD által felmerülő biztonsági jogkivonatok elfogadására vannak konfigurálva. Az összevonási metaadatok dokumentumának formátuma a [Web Services összevonási nyelv (WS-Federation) 1,2](https://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html)-es verziójában található, amely kiterjeszti [a metaadatokat az Oasis Security Assertion Markup Language (SAML) v 2.0](https://docs.oasis-open.org/security/saml/v2.0/saml-metadata-2.0-os.pdf)-s verziójára.

## <a name="tenant-specific-and-tenant-independent-metadata-endpoints"></a>Bérlő-specifikus és bérlői független metaadat-végpontok
Az Azure AD közzéteszi a bérlő-specifikus és a bérlői független végpontokat.

A bérlői specifikus végpontok egy adott bérlő számára lettek kialakítva. A bérlői specifikus összevonási metaadatok a bérlőre vonatkozó információkat, köztük a bérlői és a végponti információkat is tartalmazzák. Az egyetlen bérlőhöz való hozzáférést korlátozó alkalmazások a bérlő-specifikus végpontokat használják.

A bérlői független végpontok az összes Azure AD-Bérlővel közös információt biztosítanak. Ez az információ a *login.microsoftonline.com* -on üzemeltetett bérlők esetében érvényes, és a bérlők között van megosztva. A bérlői független végpontok használata több-bérlős alkalmazások esetén ajánlott, mivel ezek nincsenek egy adott bérlőhöz társítva.

## <a name="federation-metadata-endpoints"></a>Összevonási metaadatok végpontjai
Az Azure AD az összevonási metaadatokat `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`a következő címen teszi közzé:.

A **bérlői specifikus végpontok**esetében az `TenantDomainName` a következő típusok egyike lehet:

* Egy Azure AD-bérlő regisztrált tartományneve, például: `contoso.onmicrosoft.com`.
* A tartomány nem módosítható bérlői azonosítója, például `72f988bf-86f1-41af-91ab-2d7cd011db45`:.

A **bérlői független végpontok**esetében a `TenantDomainName` a `common`következő:. Ez a dokumentum csak azokat az összevonási metaadatokat sorolja fel, amelyek a login.microsoftonline.com-on üzemeltetett összes Azure AD-bérlő esetében közösek.

Például a bérlő-specifikus végpont lehet `https://login.microsoftonline.com/contoso.onmicrosoft.com/FederationMetadata/2007-06/FederationMetadata.xml`. A bérlőtől független végpont [https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml](https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml). Az összevonási metaadatokat tartalmazó dokumentumot úgy tekintheti meg, ha beírja ezt az URL-címet egy böngészőben.

## <a name="contents-of-federation-metadata"></a>Az összevonási metaadatok tartalma
A következő szakasz az Azure AD által kiállított jogkivonatokat használó szolgáltatások számára szükséges információkat tartalmazza.

### <a name="entity-id"></a>Entitásazonosító
Az `EntityDescriptor` elem egy `EntityID` attribútumot tartalmaz. Az `EntityID` attribútum értéke a kiállítót jelöli, vagyis a tokent kiállító biztonságijogkivonat-szolgáltatást (STS). Fontos, hogy a kibocsátót a jogkivonat fogadásakor érvényesítse.

A következő metaadatok egy példaként szolgáló, bérlőre jellemző `EntityDescriptor` elemet mutatnak be egy `EntityID` elemmel.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="_b827a749-cfcb-46b3-ab8b-9f6d14a1294b"
entityID="https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db45/">
```
A bérlői azonosító a bérlői független végponton, a bérlői azonosítóval helyettesíthető a bérlő-specifikus `EntityID` érték létrehozásához. Az eredményül kapott érték megegyezik a jogkivonat-kiállítóval. A stratégia lehetővé teszi, hogy egy több-bérlős alkalmazás érvényesítse egy adott bérlő kiállítóját.

A következő metaadatok egy példa bérlői független `EntityID` elemet mutatnak be. Vegye figyelembe, hogy az `{tenant}` a konstans, nem helyőrző.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="="_0e5bd9d0-49ef-4258-bc15-21ce143b61bd"
entityID="https://sts.windows.net/{tenant}/">
```

### <a name="token-signing-certificates"></a>Jogkivonat-aláíró tanúsítványok
Ha egy szolgáltatás egy Azure AD-bérlő által kiadott jogkivonatot kap, a jogkivonat aláírását érvényesíteni kell egy, az összevonási metaadatokat tartalmazó dokumentumban közzétett aláíró kulccsal. Az összevonási metaadatok tartalmazzák a tanúsítványok azon nyilvános részét, amelyet a bérlők a jogkivonat-aláíráshoz használnak. A tanúsítvány nyers bájtjai a `KeyDescriptor` elemben jelennek meg. A jogkivonat-aláíró tanúsítvány csak akkor érvényes az aláírásra, ha az `use` `signing`attribútum értéke.

Az Azure AD által közzétett összevonási metaadatokat tartalmazó dokumentum több aláíró kulccsal is rendelkezhet, például amikor az Azure AD előkészíti az aláíró tanúsítvány frissítését. Ha egy összevonási metaadat-dokumentum több tanúsítványt is tartalmaz, a jogkivonatokat érvényesítő szolgáltatásnak támogatnia kell a dokumentum összes tanúsítványát.

A következő metaadatok egy minta `KeyDescriptor` elemet mutatnak az aláíró kulccsal.

```
<KeyDescriptor use="signing">
<KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
<X509Data>
<X509Certificate>
MIIDPjCCAiqgAwIBAgIQVWmXY/+9RqFA/OG9kFulHDAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTIwNjA3MDcwMDAwWhcNMTQwNjA3MDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEArCz8Sn3GGXmikH2MdTeGY1D711EORX/lVXpr+ecGgqfUWF8MPB07XkYuJ54DAuYT318+2XrzMjOtqkT94VkXmxv6dFGhG8YZ8vNMPd4tdj9c0lpvWQdqXtL1TlFRpD/P6UMEigfN0c9oWDg9U7Ilymgei0UXtf1gtcQbc5sSQU0S4vr9YJp2gLFIGK11Iqg4XSGdcI0QWLLkkC6cBukhVnd6BCYbLjTYy3fNs4DzNdemJlxGl8sLexFytBF6YApvSdus3nFXaMCtBGx16HzkK9ne3lobAwL2o79bP4imEGqg+ibvyNmbrwFGnQrBc1jTF9LyQX9q+louxVfHs6ZiVwIDAQABo2IwYDBeBgNVHQEEVzBVgBCxDDsLd8xkfOLKm4Q/SzjtoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQVWmXY/+9RqFA/OG9kFulHDAJBgUrDgMCHQUAA4IBAQAkJtxxm/ErgySlNk69+1odTMP8Oy6L0H17z7XGG3w4TqvTUSWaxD4hSFJ0e7mHLQLQD7oV/erACXwSZn2pMoZ89MBDjOMQA+e6QzGB7jmSzPTNmQgMLA8fWCfqPrz6zgH+1F1gNp8hJY57kfeVPBiyjuBmlTEBsBlzolY9dd/55qqfQk6cgSeCbHCy/RU/iep0+UsRMlSgPNNmqhj5gmN2AFVCN96zF694LwuPae5CeR2ZcVknexOWHYjFM0MgUSw0ubnGl0h9AJgGyhvNGcjQqu9vd1xkupFgaN+f7P3p3EVN5csBg5H94jEcQZT7EKeTiZ6bTrpDAnrr8tDCy8ng
</X509Certificate>
</X509Data>
</KeyInfo>
</KeyDescriptor>
  ```

Az `KeyDescriptor` elem az összevonási metaadatok dokumentumának két helyén jelenik meg, a WS-Federation-specifikus szakaszban és az SAML-specifikus szakaszban. A két szakaszban közzétett tanúsítványok azonosak lesznek.

A WS-Federation-specifikus szakaszban a WS-Federation metaadat-olvasó a `RoleDescriptor` `SecurityTokenServiceType` típussal rendelkező elemből olvassa be a tanúsítványokat.

A következő metaadatok egy minta `RoleDescriptor` elemet mutatnak be.

```
<RoleDescriptor xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:fed="https://docs.oasis-open.org/wsfed/federation/200706" xsi:type="fed:SecurityTokenServiceType"protocolSupportEnumeration="https://docs.oasis-open.org/wsfed/federation/200706">
```

Az SAML-specifikus szakaszban a WS-Federation metaadat-olvasó egy `IDPSSODescriptor` elemből olvassa be a tanúsítványokat.

A következő metaadatok egy minta `IDPSSODescriptor` elemet mutatnak be.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
```
A bérlő-specifikus és a bérlői független tanúsítványok formátuma nem különbözik egymástól.

### <a name="ws-federation-endpoint-url"></a>WS-Federation végpont URL-címe
Az összevonási metaadatok közé tartozik az Azure AD az egyszeri bejelentkezéshez és az egyszeri bejelentkezéshez használt URL-cím a WS-Federation protokollban. Ez a végpont a `PassiveRequestorEndpoint` elemben jelenik meg.

A következő metaadatok egy adott bérlői végponthoz tartozó minta `PassiveRequestorEndpoint` elemet mutatnak be.

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="https://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/72f988bf-86f1-41af-91ab-2d7cd011db45/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```
A bérlői független végpont esetében a WS-Federation URL-cím megjelenik a WS-Federation végponton, ahogy az a következő példában látható.

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="https://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/common/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```

### <a name="saml-protocol-endpoint-url"></a>SAML-protokoll végpontjának URL-címe
Az összevonási metaadatok tartalmazzák azt az URL-címet, amelyet az Azure AD az egyszeri bejelentkezéshez és az egyszeri bejelentkezéshez használ az SAML 2,0 protokollban. Ezek a végpontok a `IDPSSODescriptor` elemben jelennek meg.

A bejelentkezési és a kijelentkezési URL-cím a és `SingleSignOnService` `SingleLogoutService` az elemekben jelenik meg.

A következő metaadatok egy adott bérlői végponthoz tartozó mintát `PassiveResistorEndpoint` mutatnak be.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com /saml2" />
  </IDPSSODescriptor>
```

Hasonlóképpen a Common SAML 2,0 protokoll-végpontok végpontja is közzé van téve a bérlői független összevonási metaadatokban, ahogy az az alábbi példában is látható.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
  </IDPSSODescriptor>
```
