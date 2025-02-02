---
title: Azure Active Directory-alkalmazást – végfelhasználói élményének |} A Microsoft Docs
description: Az Azure Active Directory (Azure AD) többféleképpen testre szabható is telepíthet központilag alkalmazásokat a végfelhasználók számára a szervezetben.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/03/2019
ms.author: mimart
ms.reviewer: arvindh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74c6787068cf8ba1e86cbf43955d0ac995aa8de1
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67702114"
---
# <a name="end-user-experiences-for-applications-in-azure-active-directory"></a>Azure Active Directory-alkalmazások a végfelhasználói élmény

Az Azure Active Directory (Azure AD) többféleképpen testre szabható is telepíthet központilag alkalmazásokat a szervezet a végfelhasználók számára:

* Az Azure AD hozzáférési panel
* Az Office 365 alkalmazásindítójában
* Közvetlen bejelentkezés az összevont alkalmazásokba
* Mélyhivatkozások az összevont, jelszóalapú vagy meglévő alkalmazásokhoz

Melyik mód(ok) a szervezetében telepíteni kíván a saját belátása szerint értékeli.

## <a name="azure-ad-access-panel"></a>Az Azure AD hozzáférési panel

A hozzáférési Panel Itt https://myapps.microsoft.com egy olyan webes portál, amely lehetővé teszi, hogy a végfelhasználó szervezeti fiókkal az Azure Active Directoryban megtekintése és indítása felhőalapú alkalmazásokat, amelyhez már rendelkezik hozzáféréssel az Azure AD-rendszergazda által. Ha a végfelhasználó [Azure Active Directory Premium](https://azure.microsoft.com/pricing/details/active-directory/), úgy is használhatja az önkiszolgáló csoportkezelési képességeinek a hozzáférési panelen keresztül.

![Képernyőkép az Azure AD hozzáférési Panel portal jeleníti meg.](media/what-is-single-sign-on/azure-ad-access-panel.png)

A hozzáférési panelen elkülönül az Azure Portalon, és nem igényel a felhasználók számára egy Azure-előfizetés vagy az Office 365-előfizetéssel rendelkezik.

Az Azure AD hozzáférési panel további információkért lásd: a [Bevezetés a hozzáférési panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="office-365-application-launcher"></a>Az Office 365 alkalmazásindítójában

A szervezet számára, amely az Office 365 üzembe helyezett alkalmazások hozzárendelve a felhasználókhoz az Azure AD-n keresztül is megjelennek, az Office 365 portálon [ https://portal.office.com/myapps ](https://portal.office.com/myapps). Segítségével könnyen és kényelmesen használható a felhasználók számára egy szervezet alkalmazások indítása a második a portál használata nélkül, és az ajánlott alkalmazás indításakor megoldás használja az Office 365 szervezetek számára.

![Képernyőkép az Office 365 portálon jeleníti meg.](./media/end-user-experiences/microsoft-365-portal-office-com.png)

Az Office 365 alkalmazásindítójában kapcsolatos további információkért lásd: [jelennek meg az Office 365 appindítóban alkalmazása](https://msdn.microsoft.com/office/office365/howto/connect-your-app-to-o365-app-launcher).

## <a name="direct-sign-on-to-federated-apps"></a>Közvetlen bejelentkezés az összevont alkalmazásokba

Legtöbb összevont alkalmazásokhoz, amelyek támogatják a SAML 2.0, WS-Federation vagy OpenID connect is támogatás lehetővé teszi a felhasználók számára az alkalmazások indítása, és ezután első bejelentkezett az Azure AD-n keresztül automatikus átirányítása vagy egy hivatkozásra kattintva jelentkezzen be. Ez az úgynevezett szolgáltató-kezdeményezett bejelentkezési, és leginkább összevont alkalmazások az Azure AD alkalmazáskatalógusában támogatja a (lásd a dokumentációt a részletek az Azure Portalon az alkalmazás egyszeri bejelentkezési konfigurációjának varázsló társított).

![A mobilalkalmazás bejelentkezési oldal példája](./media/end-user-experiences/workdaymobile.png)

## <a name="direct-sign-on-links"></a>Közvetlen bejelentkezés hivatkozás

Az Azure AD közvetlen egyszeri bejelentkezés mutató hivatkozások jelszóalapú egyszeri bejelentkezés, a társított egyszeri bejelentkezést és a összevont egyszeri bejelentkezés bármilyen támogató egyéni alkalmazások is támogatja.

Ezek a hivatkozások akkor kifejezetten kialakított URL-címeket, anélkül, hogy azokat az Azure AD hozzáférési panel vagy az Office 365 felhasználói bevezetésével a felhasználó leállította az Azure AD bejelentkezési folyamat egy adott alkalmazáshoz küldése. Ezek **felhasználói hozzáférési URL-címek** elérhető vállalati alkalmazásokhoz tulajdonságai területen találhatók. Az Azure Portalon válassza ki a **Azure Active Directory** > **vállalati alkalmazások**. Válassza ki az alkalmazást, és válassza **tulajdonságok**.

![A felhasználói hozzáférési URL-címe, a Twitter-tulajdonságok – példa](media/end-user-experiences/direct-sign-on-link.png)

Ezek a hivatkozások másolható, és a beillesztett bárhol lehetővé szeretné tenni a bejelentkezési hivatkozásra a kijelölt alkalmazáshoz. Ez lehet egy e-mailben, vagy a webes bármely egyéni portálon, amely a felhasználó alkalmazás-hozzáférési beállítása. Íme egy példa egy Azure AD közvetlen egyszeri bejelentkezési URL-cím a Twitter használatához:

`https://myapps.microsoft.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Hasonlóan a szervezet konkrét URL-címek esetében a hozzáférési panelen, további testre szabhatja az URL-címet a myapps.microsoft.com tartomány ellenőrzése után a aktív vagy egy ellenőrzött tartomány a címtár egyik hozzáadásával. Ez biztosítja a vállalati védjegyek betöltött azonnal a bejelentkezési oldalon a felhasználónak meg kellene először a felhasználói azonosító megadása nélkül:

`https://myapps.microsoft.com/contosobuild.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Amikor egy jogosult felhasználó az alábbi alkalmazás-specifikus hivatkozások kattint, akkor először tekintse meg a szervezeti bejelentkezési oldal (feltéve, hogy azok még nem már tette), és bejelentkezést követően a rendszer átirányítja az alkalmazás a hozzáférési panelen, először leállítása nélkül. Ha a felhasználó hiányoznak az Előfeltételek hozzáférni az alkalmazáshoz, például a jelszavas egyszeri bejelentkezés böngészőbővítmény majd a hivatkozás fogja kérni a felhasználót a hiányzó bővítmény telepítéséhez. A hivatkozás URL-CÍMÉT is állandó marad, ha az alkalmazás egyszeri bejelentkezési konfiguráció módosításait.

Ezeket a hivatkozásokat használja ugyanazt a hozzáférési vezérlési mechanizmust a hozzáférési panelen és az Office 365-höz, és csak adott felhasználók vagy csoportok, akiknek vannak hozzárendelve az alkalmazás az Azure Portalon lesz képes sikeresen hitelesíteni. Azonban bármely felhasználó, aki nem jogosult megjelenik egy üzenet tájékoztatja, hogy azok még nem kapott hozzáférést, és betöltése, amelyhez hozzáférésük van az elérhető alkalmazások megtekintéséhez a hozzáférési panel egy hivatkozást kapnak.

## <a name="next-steps"></a>További lépések

Központi telepítési csomagokat, lásd: [Azure Active Directory központi telepítési csomagok](../fundamentals/active-directory-deployment-plans.md)
