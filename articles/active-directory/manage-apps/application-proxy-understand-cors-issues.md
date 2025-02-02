---
title: Az Azure AD Application Proxy CORS kapcsolatos problémák megismerése és megoldása
description: Az Azure AD Application Proxy CORS megismerése, valamint a CORS kapcsolatos problémák azonosítása és megoldása.
services: active-directory
author: jeevanbisht
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: celested
ms.reviewer: japere
ms.openlocfilehash: 265458066a528246cbfa7876bf61b02a0382581b
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2019
ms.locfileid: "68499607"
---
# <a name="understand-and-solve-azure-active-directory-application-proxy-cors-issues"></a>Azure Active Directory Application Proxy CORS kapcsolatos problémák megismerése és megoldása

[A több eredetű erőforrás-megosztás (CORS)](https://www.w3.org/TR/cors/) időnként kihívást jelenthet a Azure Active Directory Application Proxyon közzétett alkalmazások és API-k számára. Ez a cikk az Azure AD Application Proxy CORS kapcsolatos problémákat és megoldásokat ismerteti.

A böngésző biztonsága általában megakadályozza, hogy a weblapok AJAX-kérelmeket hozzanak egy másik tartományba. Ezt a korlátozást *azonos eredetű házirendnek*nevezzük, és megakadályozza, hogy egy rosszindulatú hely bizalmas adatokat olvasson be egy másik helyről. Előfordulhat azonban, hogy más helyekre is meg szeretné hívni a webes API-t. A CORS egy W3C-szabvány, amely lehetővé teszi, hogy a kiszolgálók megpihenjenek az azonos eredetű házirendben, és egyes eltérő eredetű kérelmeket is engedélyezzenek, míg mások elutasítását.

## <a name="understand-and-identify-cors-issues"></a>CORS kapcsolatos problémák ismertetése és azonosítása

Két URL-cím azonos eredetű, ha azonos sémákkal, gazdagépekkel és portokkal rendelkeznek ([RFC 6454](https://tools.ietf.org/html/rfc6454)), például:

-   http:\//contoso.com/foo.html
-   http:\//contoso.com/Bar.html

A következő URL-címek eltérő eredetűek, mint az előző kettő:

-   http:\//contoso.net – különböző tartomány
-   http:\//contoso.com:9000/foo.html – különböző port
-   https:\//contoso.com/foo.html – eltérő séma
-   http:\//www.contoso.com/foo.html – eltérő altartomány

Az azonos eredetű házirend megakadályozza, hogy az alkalmazások más eredetű forrásokból férhessenek hozzá az erőforrásokhoz, kivéve, ha a megfelelő hozzáférés-vezérlési fejléceket használják. Ha a CORS fejlécei hiányoznak vagy helytelenek, az adatforrások közötti kérelmek meghiúsulnak. 

A CORS problémák a böngésző hibakeresési eszközeivel azonosíthatók:

1. Indítsa el a böngészőt, és keresse meg a webalkalmazást.
1. Nyomja meg az **F12** billentyűt a hibakeresési konzol létrehozásához.
1. Próbálja megismételni a tranzakciót, és tekintse át a konzol üzenetét. A CORS megsértése konzolos hibát eredményez a forrásról.

A következő képernyőképen a **kipróbálás** gomb kiválasztásával egy CORS hibaüzenetet okozott, amely szerint\/a https:/corswebclient-contoso.msappproxy.net nem található a hozzáférés-vezérlés – engedélyezés-eredet fejlécben.

![CORS probléma](./media/application-proxy-understand-cors-issues/image3.png)

## <a name="cors-challenges-with-application-proxy"></a>CORS kapcsolatos kihívások az alkalmazásproxy esetében

Az alábbi példa egy tipikus Azure AD Application Proxy CORS forgatókönyvet mutat be. A belső kiszolgáló egy **CORSWebService** web API-vezérlőt és egy **CORSWebService**meghívására szolgáló **CORSWebClient** üzemeltet. Van egy AJAX-kérés a **CORSWebClient** és a **CORSWebService**között.

![Helyszíni azonos eredetű kérelem](./media/application-proxy-understand-cors-issues/image1.png)

A CORSWebClient alkalmazás akkor működik, ha a helyszínen üzemelteti, de az Azure AD Application Proxy-n keresztül történő közzétételkor nem sikerül betölteni vagy hibákba ütközik. Ha a CORSWebClient és a CORSWebService alkalmazásokat külön, különböző alkalmazásokként közzétette az alkalmazásproxy használatával, a két alkalmazás különböző tartományokban üzemel. A CORSWebClient-ből a CORSWebService-re irányuló AJAX-kérelem egy kereszthivatkozási kérelem, amely nem sikerül.

![Alkalmazásproxy-CORS kérelem](./media/application-proxy-understand-cors-issues/image2.png)

## <a name="solutions-for-application-proxy-cors-issues"></a>Az alkalmazásproxy CORS kapcsolatos problémák megoldása

A fenti CORS probléma többféleképpen is feloldható.

### <a name="option-1-set-up-a-custom-domain"></a>1\. lehetőség: Egyéni tartomány beállítása

Egy Azure-AD Application Proxy [egyéni tartomány](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-custom-domains) használatával tehet közzé ugyanabból a forrásból, anélkül, hogy módosítania kellene az alkalmazás eredetét, kódját vagy fejlécét. 

### <a name="option-2-publish-the-parent-directory"></a>2\. lehetőség: A szülő könyvtár közzététele

Mindkét alkalmazás szülő könyvtárának közzététele. Ez a megoldás különösen jól működik, ha csak két alkalmazással rendelkezik a webkiszolgálón. Az egyes alkalmazások külön közzététele helyett közzéteheti a közös szülő könyvtárat, amely ugyanezt a forrást eredményezi.

Az alábbi példák a portál Azure AD Application Proxy lapját mutatják be a CORSWebClient alkalmazáshoz.  Ha a **belső URL-cím** a *contoso.com/CORSWebClient*értékre van állítva, az alkalmazás nem tud sikeres kéréseket készíteni a *contoso.com/CORSWebService* könyvtárára, mert azok több eredetűek. 

![Alkalmazás közzététele egyenként](./media/application-proxy-understand-cors-issues/image4.png)

Ehelyett állítsa be a **belső URL-címet** a szülő könyvtár közzétételéhez, amely magában foglalja a *CORSWebClient* és a *CORSWebService* könyvtárakat is:

![Szülő könyvtár közzététele](./media/application-proxy-understand-cors-issues/image5.png)

Az eredményül kapott alkalmazás URL-címei hatékonyan oldják meg a CORS problémát:

- https:\//corswebclient-contoso.msappproxy.net/CORSWebService
- https:\//corswebclient-contoso.msappproxy.net/CORSWebClient

### <a name="option-3-update-http-headers"></a>3\. lehetőség: HTTP-fejlécek frissítése

Adjon hozzá egy egyéni HTTP-válasz fejlécet a webszolgáltatáshoz, hogy az megfeleljen a forrás kérelmének. Internet Information Services (IIS) szolgáltatásban futó webhelyek esetén az IIS-kezelővel módosítsa a fejlécet:

![Egyéni válasz fejlécének hozzáadása az IIS-kezelőben](./media/application-proxy-understand-cors-issues/image6.png)

Ehhez a módosításhoz nincs szükség kód módosítására. A Hegedűs nyomkövetései között a következőket ellenőrizheti:

**A fejléc közzétételének hozzáadása**\
HTTP/1.1 200 OK \
Cache-Control: no-cache \
Sorpragmákat: no-cache \
Content-Type: text/plain; charset = UTF-8 \
Lejárat:-1 \
Változhat Elfogadás – Encoding \
Kiszolgáló: Microsoft-IIS/8.5 Microsoft-HTTPAPI/2.0 \
**Hozzáférés-vezérlés engedélyezése – forrás: https\://corswebclient-contoso.msappproxy.net**\
X-AspNet-Version: 4.0.30319 elemet
X-Powered-By: ASP.NET\
Content-Length: 17

### <a name="option-4-modify-the-app"></a>4\. lehetőség: Az alkalmazás módosítása

A CORS támogatásához módosíthatja az alkalmazást úgy, hogy a megfelelő értékekkel hozzáadja a hozzáférés-vezérlés – Allow-Origin fejlécet. A fejléc hozzáadásának módja az alkalmazás kódjának nyelvétől függ. A kód módosítása a legkevésbé ajánlott lehetőség, mert a legnagyobb erőfeszítést igényli.

### <a name="option-5-extend-the-lifetime-of-the-access-token"></a>5\. lehetőség: A hozzáférési jogkivonat élettartamának meghosszabbítása

Bizonyos CORS problémák nem oldhatók fel, például amikor az alkalmazás átirányítja a *login.microsoftonline.com* a hitelesítésre, és a hozzáférési jogkivonat lejár. A CORS hívása sikertelen lesz. A forgatókönyv áthidaló megoldása a hozzáférési jogkivonat élettartamának meghosszabbítása annak megakadályozása érdekében, hogy a felhasználó munkamenete közben lejárjon. Ennek módjáról további információt a [konfigurálható jogkivonat-élettartamok az Azure ad-ben](../develop/active-directory-configurable-token-lifetimes.md)című témakörben talál.

## <a name="see-also"></a>Lásd még
- [Oktatóanyag: Helyszíni alkalmazás hozzáadása a távoli eléréshez az alkalmazásproxy használatával Azure Active Directory](application-proxy-add-on-premises-application.md) 
- [Azure AD Application Proxy üzemelő példány megtervezése](application-proxy-deployment-plan.md) 
- [Helyi alkalmazásokhoz való távoli hozzáférés Azure Active Directory Application Proxy](application-proxy.md) 
