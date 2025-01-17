---
title: Azure Marketplace SaaS-alkalmazások – technikai közzétételi útmutató
description: Részletes útmutató és közzétételi ellenőrzőlista az SaaS-alkalmazások Azure Marketplace-en való közzétételéhez
services: Marketplace, Compute, Storage, Networking, Blockchain, Security, SaaS
author: keithcharlie
ms.service: marketplace
ms.topic: article
ms.date: 07/09/2018
ms.author: kevidal
ms.openlocfilehash: f9ff6e19a0f0091cb5b831279eee90727bbb89fd
ms.sourcegitcommit: d060947aae93728169b035fd54beef044dbe9480
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2019
ms.locfileid: "68742255"
---
# <a name="saas-applications-offer-publishing-guide"></a>SaaS-alkalmazások ajánlat-közzétételi útmutató

Az SaaS-alkalmazások közzétételét a piactéren három különböző művelettel hívhatja: "Kapcsolatfelvétel", "kipróbálás" és "Letöltés most". Ez az útmutató a három lehetőséget ismerteti, beleértve az egyes követelmények követelményeit is. 

## <a name="offer-overview"></a>Ajánlat áttekintése  

Az SaaS-alkalmazások az Azure kirakatokban is elérhetők. az alábbi táblázat az aktuálisan elérhető lehetőségeket ismerteti:

| Kirakat lehetőség | Listázása | Próbaverzió/Transact |  
| --- | --- | --- |  
| AppSource | Igen (Kapcsolatfelvétel) | Igen (PowerBI/Dynamics) |
| Azure Marketplace | Nem | Igen (SaaS-alkalmazások) |   

**Listáját**  A tőzsdei közzétételi lehetőség egy kapcsolatfelvételi ajánlat típusát tartalmazza, és akkor használható, ha a próbaverziós vagy tranzakciós szintű részvétel nem valósítható meg. Ennek a megközelítésnek az az előnye, hogy lehetővé teszi a kiadók számára, hogy a piacon elérhető megoldással azonnal elkezdhetik a vállalkozása növekedéséhez szükséges érdeklődők fogadását.  
**Próbaverzió/tranzakció:**  Az ügyfél lehetősége van arra, hogy közvetlenül vásároljon vagy kérjen próbaverziót a megoldásához. A próbaverziós szolgáltatás megadásával növelheti az ügyfelek számára felkínált engagement szintet, és a vásárlás előtt lehetővé teszi az ügyfeleknek a megoldás megismerését. A próbaverziós szolgáltatással jobb eséllyel népszerűsítheti a kirakatokat, és több és gazdagabb érdeklődőket kell várnia az ügyfelek bevonásával. A próbaverzióknak legalább a próbaidőszak időtartama alatt ingyenes támogatást kell tartalmazniuk.  

| SaaS-alkalmazások ajánlata | Üzleti feltételek | Technikai követelmények |  
| --- | --- | --- |  
| **Kapcsolatfelvétel** | Igen | Nem |  
| **PowerBI / Dynamics** | Igen | Igen (Azure AD-integráció) |  
| **SaaS-alkalmazások**| Igen | Igen (Azure AD-integráció) |     

## <a name="saas-list"></a>SaaS-lista

Ha egy SaaS-listához próbaverziót szeretne felvenni, és nincs számlázási funkciója, akkor a "Kapcsolatfelvétel" lehetőséggel. 

Az SaaS-alkalmazások listázásához nem kell Azure Active Directory konfigurálnia. 

|Követelmények  |Részletek  |
|---------|---------|
|Az alkalmazás egy SaaS-ajánlat  |   A megoldás egy SaaS-ajánlat, és több-bérlős SaaS-terméket is kínál.      |


## <a name="saas-trial"></a>SaaS-próbaverzió

A megoldás vagy az alkalmazás egy ingyenes, szoftveres (SaaS-) alapú próbaverzió használatával biztosítható. Az ingyenes próbaverziós ajánlatokat korlátozott használati vagy korlátozott időtartamú próbaverziós fiókként lehet megjeleníteni. 


|Követelmények  |Részletek  |
|---------|---------|
|Az alkalmazás egy SaaS-ajánlat  |   A megoldás egy SaaS-ajánlat, és több-bérlős SaaS-terméket is kínál.      |
|Az alkalmazás HRE engedélyezve van     |   A rendszer átirányítja az ügyfelet a tartományhoz, és közvetlenül az ügyféllel fog tranzakciót kötni       |


## <a name="saas-trial-technical-requirements"></a>SaaS-próbaverzió műszaki követelményei

Az SaaS-alkalmazások technikai követelményei egyszerűek. A közzétevőket csak a közzétételhez szükséges Azure Active Directory (Azure AD) integrációra kell integrálni. Az Azure AD-integráció az alkalmazásokkal jól dokumentált, és a Microsoft több SDK-t és erőforrást biztosít ennek megvalósításához.  

A kezdéshez javasoljuk, hogy az Azure Marketplace Publishing szolgáltatáshoz dedikált előfizetéssel elkülönítse a munkát más kezdeményezésekkel. Ha ez megtörtént, megkezdheti az SaaS-alkalmazás üzembe helyezését ebben az előfizetésben a fejlesztési munka elindításához.  

A legjobb Azure Active Directory dokumentáció, minták és útmutatás a következő helyeken található: 

* [Azure Active Directory fejlesztői útmutató](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)

* [Integráció a Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-to-integrate)

* [Alkalmazások integrálása a Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)

* [Azure-ütemterv – biztonság és identitás](https://azure.microsoft.com/roadmap/?category=security-identity)

Videós oktatóanyagok esetében tekintse át a következőket:

* [Hitelesítés Azure Active Directory Vittorio Bertocci](https://channel9.msdn.com/Shows/XamarinShow/Episode-27-Azure-Active-Directory-Authentication-with-Vittorio-Bertocci?term=azure%20active%20directory%20integration)

* [Azure Active Directory Identity műszaki tájékoztató – 1. rész, 2](https://channel9.msdn.com/Blogs/MVP-Enterprise-Mobility/Azure-Active-Directory-Identity-Technical-Briefing-Part-1-of-2?term=azure%20active%20directory%20integration)

* [Azure Active Directory Identity műszaki tájékoztató – 2. rész](https://channel9.msdn.com/Blogs/MVP-Azure/Azure-Active-Directory-Identity-Technical-Briefing-Part-2-of-2?term=azure%20active%20directory%20integration)

* [Alkalmazások kiépítése Microsoft Azure Active Directory](https://channel9.msdn.com/Blogs/Windows-Development-for-the-Enterprise/Building-Apps-with-Microsoft-Azure-Active-Directory?term=azure%20active%20directory%20integration)

* [Active Directoryre összpontosító Microsoft Azure videók](https://azure.microsoft.com/resources/videos/index/?services=active-directory)

Az ingyenes Azure Active Directory képzés a következő címen érhető el:  
* [Microsoft Azure informatikai szakemberek számára: Azure Active Directory](https://mva.microsoft.com/training-courses/microsoft-azure-for-it-pros-content-series-azure-active-directory-16754?l=N0e23wtxC_2106218965)

Emellett Azure Active Directory biztosít a szolgáltatás frissítéseinek keresésére szolgáló helyet.   
* [Azure AD-szolgáltatások frissítései](https://azure.microsoft.com/updates/?product=active-directory)

## <a name="using-azure-active-directory-to-enable-trials"></a>A Azure Active Directory használata a próbaverziók engedélyezéséhez  

A Microsoft hitelesíti az összes piactér-felhasználót az Azure AD-vel, így ha egy hitelesített felhasználó a piactéren lévő próbaverzióra kattint, és átirányítja a próbaverziós környezetbe, a felhasználó közvetlenül a Próbaverzióba is kiépíthető, anélkül, hogy További bejelentkezési lépés. A hitelesítés során az alkalmazás által az Azure AD-től kapott token értékes felhasználói adatokat tartalmaz, amelyekkel felhasználói fiókot hozhat létre az alkalmazásban, lehetővé téve a kiépítési élmény automatizálását és az átalakítás valószínűségének növelését. A jogkivonattal kapcsolatos további információkért lásd: [minta tokenek](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims) .

Az Azure AD használatával engedélyezheti az alkalmazásra vagy próbaverzióra vonatkozó 1 kattintásos hitelesítést a következő műveleteket:  
* A piactéren a próbaverzióra egyszerűsítheti a felhasználói élményt.  
* A "terméken belüli felhasználói élmény" megtartása akkor is, ha a felhasználó a piactérről a tartományba vagy a próbaverziós környezetbe van átirányítva.  
* Csökkenti annak a valószínűségét, hogy az átirányítás lemondható, mert nincs további bejelentkezési lépés.  
* Csökkenti az Azure AD-felhasználók nagy sokaságának üzembe helyezési korlátait.  

## <a name="certifying-your-azure-ad-integration-for-marketplace"></a>Az Azure AD-integráció tanúsítása a piactéren  

Az Azure AD-integrációt néhány különböző módon tanúsíthatja, attól függően, hogy az alkalmazása egybérlős vagy több bérlős-e, és hogy új-e az Azure AD összevont egyszeri bejelentkezés (SSO), vagy már támogatja azt.  

**Több-bérlős alkalmazások esetén:**  

Ha már támogatja az Azure AD-t, tegye a következőket:
1.  Az alkalmazás regisztrálása a Azure Portal
2.  Engedélyezze az Azure AD több-bérlős támogatási szolgáltatását, és kérjen le egy kattintással elérhető próbaverziót. Részletesebb információkat [itt](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)találhat.  

Ha még nem ismeri az Azure AD összevont egyszeri bejelentkezést, tegye a következőket: 
1.  Az alkalmazás regisztrálása a Azure Portal
2.  Az Azure AD-val történő egyszeri bejelentkezést az [OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code) vagy a [OAuth 2,0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code)használatával fejlesztheti.
3.  A HRE több-bérlős támogatási funkciója lehetővé teszi, hogy a "One-Click" próbaverzióval kapcsolatos további információkat [itt](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified)találja.  

**Egybérlős alkalmazások esetén használja az alábbi lehetőségek egyikét:**  
* Felhasználók hozzáadása a címtárhoz vendégként az [Azure B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b) használatával
* Próbaverziók manuális kiépítése az ügyfelek számára a "Kapcsolatfelvétel" használatával
* Felhasználónkénti "tesztelési meghajtó" fejlesztése
* Több-bérlős minta bemutató alkalmazás létrehozása SSO-val

## <a name="saas-subscriptions"></a>SaaS-előfizetések

Az SaaS-alkalmazások ajánlatának típusa lehetővé teszi, hogy az ügyfél a SaaS-alapú, technikai megoldását előfizetésként vásárolja meg. A következő követelményeknek kell teljesülniük az SaaS-alkalmazáshoz:
- Díj és számlázás a szolgáltatással (havonta vagy évente), vagy felhasználónkénti díjszabással.
- Adjon meg egy módszert a szolgáltatás frissítésére vagy megszakítására.
A Microsoft üzemelteti a kereskedelmi tranzakciót. A Microsoft az Ön nevében számlázza az ügyfelet. Az SaaS-alkalmazások előfizetésként való ellátásához integrálni kell a SaaS-megvalósítási API-kkal.  A szolgáltatásnak támogatnia kell a kiépítés, a verziófrissítés és a megszakítást.

| Követelmény | Részletek |  
|:--- |:--- |  
|Számlázás és mérés | Az ajánlat díjszabása a közzététel előtt kiválasztott árképzési modell alapján történik (átalány vagy felhasználónként).  Az átalány-modell használata esetén igény szerint további dimenziókat is megadhat, amelyeket a rendszer az átalányban nem szereplő használati díjak megadására használ fel. |  
|Lemondás | Ajánlatát bármikor megszakíthatja az ügyfél. |  
|Tranzakció kezdőlapja | Egy Azure-beli közös védjeggyel ellátott tranzakció-kezdőlapot üzemeltet, ahol a felhasználók létrehozhatják és kezelhetik az SaaS-szolgáltatásfiókot. |   
| Előfizetés API | Olyan szolgáltatást tesz elérhetővé, amely képes kommunikálni az SaaS-előfizetéssel egy felhasználói fiók és szolgáltatáscsomag létrehozásához, frissítéséhez és törléséhez. A kritikus API-módosításokat 24 órán belül támogatni kell. A nem kritikus API-változások rendszeresen jelennek majd meg. |  

>[!Note]
>A Cloud Solution Providers (CSP) Partner Channel opt-in mostantól elérhető.  Az ajánlat Microsoft CSP-partneri csatornákon keresztüli forgalmazásával kapcsolatos további információkért tekintse meg a [Cloud Solution Providers](./cloud-solution-providers.md) című témakört.

## <a name="next-steps"></a>További lépések
Ha még nem tette meg,

- [Regisztráljon](https://azuremarketplace.microsoft.com/sell) a piactéren.

Ha regisztrálva van, és új ajánlatot hoz létre, vagy dolgozik egy meglévőn,

- [](https://cloudpartner.azure.com) Az ajánlat létrehozásához vagy befejezéséhez jelentkezzen be a Cloud Partner Portalba.
- További információért lásd az [Azure SaaS-alkalmazás ajánlatát](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer) .
