---
title: Microsoft AppSource és az Azure Marketplace listázásának engedélyezése Azure Active Directory használatával | Azure
description: Az Azure Marketplace-en és a AppSource az alkalmazások és szolgáltatások közzétevői számára való Azure Active Directory használatával engedélyezheti a listaelemek típusát.
services: Azure, AppSource, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: qianw211
manager: pabutler
ms.service: marketplace
ms.topic: article
ms.date: 09/12/2018
ms.author: pabutler
ms.openlocfilehash: 0b68687a2bbaa817f2776757ccab9571638c0fd5
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876007"
---
# <a name="enable-an-appsource-and-marketplace-listing-by-using-azure-active-directory"></a>AppSource- és Marketplace-termékoldal engedélyezése az Azure Active Directory használatával

 Azure Active Directory (Azure AD) egy felhőalapú identitás-szolgáltatás, amely lehetővé teszi a hitelesítést egy Microsoft-fiók. Az Azure AD az iparági szabványoknak megfelelő keretrendszereket használ. [További információ a Azure Active Directoryról](https://azure.microsoft.com/services/active-directory).

## <a name="azure-ad-benefits"></a>Az Azure AD előnyei

A Microsoft AppSource és az Azure Marketplace ügyfelei a terméken belüli tapasztalatokat használják a listázási katalógusok kereséséhez. Ezeknek a műveleteknek a használatához a felhasználóknak be kell jelentkezniük a termékbe. Az Azure AD-integráció a következő előnyöket biztosítja:

- Gyorsabb engagement és optimalizált felhasználói élmény
- Egyszeri bejelentkezés (SSO) több millió vállalati felhasználó számára
- Konzisztens, bejelentkezési élmény különböző partnerek által közzétett alkalmazások között
- Méretezhető, többplatformos hitelesítés mobil-és felhőalapú alkalmazásokhoz

## <a name="offers-that-require-azure-ad"></a>Az Azure AD-t igénylő ajánlatok

A AppSource és az Azure Marketplace különböző [listázási lehetőségei és típusai](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type) eltérő követelményekkel rendelkeznek az Azure ad megvalósításához. A részletekért tekintse meg az alábbi táblázatot:

| **Ajánlat típusa**    | **Azure AD SSO szükséges?**  |  |   |  |
| :------------------- | :-------------------|:-------------------|:-------------------|:-------------------|
|  | Kapcsolatfelvétel | Próbaverzió | Tesztverzió | Tranzakció |
| Virtuális gép | – | Nem | Nem | Nem |
| Azure-alkalmazások (megoldás-sablon)  | – | N/A | N/A | – |
| Felügyelt alkalmazások  | – | N/A | – | Nem |
| SaaS  | Nem | Igen | Igen | Igen |
| Containers  | – | N/A | – | Nem |
| Tanácsadási szolgáltatások  | Nem | – | N/A | – |

További információ a SaaS-technikai követelményekről: [SaaS-alkalmazások ajánlat közzétételi útmutatója](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide).

## <a name="azure-ad-integration"></a>Azure AD-integráció

- További információ az egyszeri bejelentkezés engedélyezéséről az Azure AD-nek a listához való integrálásával: [Azure Active Directory for Developers]( https://aka.ms/aaddev).
- Az Azure AD egyszeri bejelentkezéssel kapcsolatos részletekért tekintse meg [a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)című témakört.

## <a name="enable-a-trial-listing"></a>Próbaverziók listájának engedélyezése

Az automatizált ügyfelek telepítése növelheti a konverzió valószínűségét. Ha az ügyfél kiválasztja a próbaidőszakot, és a rendszer átirányítja a próbaverziós környezetbe, az ügyfelet közvetlenül a további bejelentkezési lépések megkövetelése nélkül is beállíthatja.

A hitelesítés során az Azure AD jogkivonatot küld az alkalmazásnak vagy az ajánlatnak. A jogkivonat által megadott felhasználói adatok lehetővé teszik felhasználói fiók létrehozását az alkalmazásban vagy az ajánlatban. További információ: [minta tokenek](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).

Ha az Azure AD-t használja az egykattintásos hitelesítés engedélyezéséhez az alkalmazásban vagy a próbaverzióban, a következőt kell tennie:

- Egyszerűsítse a piactér felhasználói élményét a próbaverziós listához.
- Ha a felhasználót a piactérről a tartományba vagy a próbaverziós környezetbe irányítja át, akkor is megtarthatja a terméken belüli élményt.
- Csökkentse annak a valószínűségét, hogy a rendszer elutasítja a felhasználókat, mert nincsenek további bejelentkezési lépések.
- Csökkentse az Azure AD-felhasználók nagy sokaságának üzembe helyezési korlátait.

## <a name="verify-azure-ad-integration"></a>Az Azure AD-integráció ellenőrzése

### <a name="multitenant-solutions"></a>Több-bérlős megoldások

Az Azure AD használata a következő műveletek támogatásához:

- Az alkalmazás regisztrálása az egyik piactér-kirakatban. További információért tekintse meg az [alkalmazás regisztrációjának](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) vagy [AppSource minősítését](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified) .
- Az Azure AD bérlős-támogatás funkciójának engedélyezésével egyetlen kattintással elsajátíthatja a próbaverziót.

Ha most ismerkedik az Azure AD összevont egyszeri bejelentkezéssel, hajtsa végre a következő lépéseket:

1. Az alkalmazás regisztrálása a piactéren.
1. Az SSO-t az Azure AD-vel [OAuth 2,0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code) vagy [OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code)használatával fejlesztheti.
1. Az Azure AD bérlős-támogatás funkciójának engedélyezése egy kattintással elérhető próbaverziót biztosít.

### <a name="single-tenant-solutions"></a>Egybérlős megoldások

Az Azure AD használata a következő műveletek egyikének támogatásához használható:

- Adja hozzá a vendég felhasználókat a címtárhoz az [Azure ad B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)használatával.
- Manuálisan állítson be próbaverziókat az ügyfelek számára a **kapcsolat** küldése közzétételi lehetőség használatával.
- Felhasználónkénti tesztelési meghajtó fejlesztése.
- Hozzon létre egy több-bérlős minta bemutató alkalmazást, amely egyszeri bejelentkezést használ.

## <a name="next-steps"></a>További lépések

- Győződjön meg róla, hogy regisztrálva van [Az Azure Marketplace-](https://azuremarketplace.microsoft.com/sell)en.
- Az ajánlat létrehozásával vagy végrehajtásával kapcsolatos további információkért tekintse meg [a partner Center-fiók létrehozása](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) című témakört.
