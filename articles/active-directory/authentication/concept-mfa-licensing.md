---
title: Az Azure MFA-verziók és a használatalapú csomagok – Azure Active Directory
description: A multi-factor Authentication-ügyfél és a különböző módszerek és elérhető verziók vonatkozó adatokat.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/03/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a1ee55dd3aebca869da47bbc994f546aa4fe528
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66496770"
---
# <a name="how-to-get-azure-multi-factor-authentication"></a>Az Azure multi-factor Authentication beszerzése

Esetén, a fiókok védelmét, a kétlépéses ellenőrzés kell standard a szervezetben. Ez a szolgáltatás különösen fontos az fiókra, amelyet a privilegizált erőforrásokhoz való hozzáférést. Ebből kifolyólag a Microsoft alapvető kétlépéses ellenőrzési funkciókat kínál az Office 365 és az Azure Active Directory (Azure AD) a rendszergazdák nem kapcsolódik további költség. Ha azt szeretné, a rendszergazdák számára a szolgáltatások frissítésével, vagy a többi felhasználó kétlépéses ellenőrzés kiterjesztése, az Azure multi-factor Authentication többféle módon is vásárolhat.

> [!IMPORTANT]
> Ez a cikk szinkronban kell lennie egy útmutató segítségével megismerheti az Azure multi-factor Authentication szolgáltatás vásárlása különböző módjait. Díjszabás és számlázás kapcsolatos részleteket, mindig tekintse át a [a multi-factor Authentication szolgáltatás díjszabási oldalán](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).
>

## <a name="available-versions-of-azure-multi-factor-authentication"></a>Az Azure multi-factor Authentication elérhető verzióinak

A következő táblázat ismerteti a multi-factor authentication szolgáltatás három verziója közötti különbségek:

| Version | Leírás |
| --- | --- |
| Multi-Factor Authentication az Office 365-höz <br> Microsoft 365 Business | Ez a verzió az Office 365 vagy a Microsoft 365 portáljáról felügyelhető. A rendszergazdák is [védheti az Office 365-erőforrásokat az kétlépéses ellenőrzés](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6). Ez a verzió az Office 365 vagy a Microsoft 365 Business-előfizetés részét képezi. |
| Többtényezős hitelesítés az Azure AD-rendszergazdák | Az Azure AD-bérlőt az Azure AD globális rendszergazdai szerepkörrel rendelkező engedélyezheti a kétlépéses ellenőrzés további költségek nélkül. |
| Azure Multi-Factor Authentication | A "teljes" verziója más néven, az Azure multi-factor Authentication a képességek richest készletét kínálja. Elérhető további konfigurációs lehetőségeket biztosít a [az Azure portal](https://portal.azure.com), speciális jelentéskészítés és a helyszíni széles körű támogatása és a felhőbeli alkalmazásokhoz. Az Azure multi-factor Authentication csak a [Azure Active Directory Premium](https://www.microsoft.com/cloud-platform/azure-active-directory-features). |

> [!NOTE]
> Új ügyfeleket már nem vásárolhat Azure multi-factor Authentication kínál hatékony 1-től, 2018 szeptember önálló. A multi-factor authentication továbbra is elérhető az Azure AD Premium-licencet.

## <a name="feature-comparison-of-versions"></a>Verzió szolgáltatásainak összehasonlítása

Az alábbi táblázat az Azure multi-factor Authentication szolgáltatás különféle verzióihoz elérhető funkciók listáját tartalmazza.

> [!NOTE]
> Az összehasonlító táblázatot a többtényezős hitelesítés minden verziója részét képező funkciókat ismerteti. Ha a teljes Azure multi-factor Authentication szolgáltatás, bizonyos szolgáltatások nem lesznek elérhetők, ha használja [MFA a felhőben vagy MFA helyszíni](concept-mfa-whichversion.md).
>

| Funkció | Multi-Factor Authentication az Office 365-höz | Többtényezős hitelesítés az Azure AD-rendszergazdák | Azure Multi-Factor Authentication |
| --- |:---:|:---:|:---:|
| MFA-val az Azure AD rendszergazdai fiókok védelme |● |● (csak az Azure AD globális rendszergazdai fiókok esetében) |● |
| Mobilalkalmazás második tényezőként |● |● |● |
| Telefonhívás második tényezőként |● |● |● |
| SMS második tényezőként |● |● |● |
| Alkalmazásjelszavak az MFA-t nem támogató ügyfelek |● |● |● |
| Hitelesítési módszerek rendszergazdai szabályozása |● |● |● |
| MFA-val nem rendszergazdai fiókok védelme |● | |● |
| PIN-mód | | |● |
| Csalási riasztás | | |● |
| MFA-jelentések | | |● |
| Egyszeri mellőzés | | |● |
| Egyéni üdvözlések a telefonhívásokhoz | | |● |
| Egyéni Hívóazonosító a telefonhívásokhoz | | |● |
| Megbízható IP-címek | | |● |
| MFA megjegyzése megbízható eszközökön |● |● |● |
| MFA helyszíni alkalmazások | | |● |

> [!IMPORTANT]
> A telefonhívási beállítások verzióját 2019. március kezdve nem lesz elérhető ingyenes vagy próbaverziója az Azure AD-bérlők MFA és az SSPR felhasználók számára. SMS-ezni, ez a változás nem érinti. Telefonhívás továbbra is elérhető a felhasználók számára a fizetős Azure AD-bérlőt. Ez a változás csak az Azure AD ingyenes vagy próbaverziója bérlők hatással van.

## <a name="how-to-turn-on-azure-multi-factor-authentication-for-azure-ad-administrators"></a>Az Azure AD-rendszergazdák az Azure multi-factor Authentication bekapcsolása

Az Azure AD-bérlő globális rendszergazdai szerepkörrel rendelkező engedélyezheti a kétlépéses ellenőrzést, az Azure AD globális rendszergazdai fiókok további költségek nélkül. Ha Microsoft-Account használ, a multi-factor authentication a Microsoft fiók támogatási cikkben található útmutató segítségével regisztrálhatja [tudnivalók a kétlépéses ellenőrzés](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification). Ha nem használ egy Microsoft-Account, kapcsolja be a többtényezős hitelesítést a globális rendszergazdák, a cikkben található útmutató segítségével [igénylése a kétlépéses ellenőrzés egy felhasználó vagy csoport](howto-mfa-userstates.md).

## <a name="how-to-purchase-azure-multi-factor-authentication"></a>Hogyan vásárolhatja meg az Azure multi-factor Authentication

Vásároljon licencet, amely tartalmazza az Azure multi-factor Authentication, mint például az Azure Active Directory Premium, vagy egy licenc-csomagot, amely tartalmazza az Azure AD prémium vagy a feltételes hozzáférés, és hozzárendelheti azokat a felhasználók számára az Azure Active Directoryban.

### <a name="consumption-based-licensing"></a>Consumption-alapú licencelés

Licencelési fogyasztásalapú már nem érhető el az új ügyfelek számára hatékony 2018. szeptember 1.

Hatékony 2018. szeptember 1. új auth-szolgáltatók többé nem hozható létre. Meglévő hitelesítésszolgáltatók előfordulhat, hogy továbbra is használja, és frissítve. A multi-factor authentication továbbra is egy elérhető funkciónak az Azure AD Premium-licencet.

Azure multi-factor Authentication-szolgáltatót használ, ha van elérhető az Azure-előfizetés díjszabása vonatkozik, amely két használati modellekről:

1. **Engedélyezett felhasználónként** – vállalatok számára szeretné engedélyezni a kétlépéses ellenőrzés egy rögzített számú alkalmazottak, akik rendszeresen hitelesítésre van szükség. Felhasználóalapú számlázás az Azure AD-bérlőhöz, és az Azure MFA-kiszolgáló az MFA engedélyezett felhasználók számát alapul. Felhasználók engedélyezve vannak-e a multi-factor Authentication két Azure AD-ben és az Azure MFA-kiszolgáló és a tartomány-szinkronizáló (Azure AD Connect) engedélyezve van, akkor azt száma, hogy a felhasználók nagyobb készlete. Ha tartományi szinkronizálás nincs engedélyezve, akkor azt a count összege az összes olyan felhasználó, az Azure AD MFA engedélyezett, és Azure MFA-kiszolgáló. A számlázás napokra és a kereskedelmi rendszer jelentett naponta.

   > [!NOTE]
   > A számlázás 1. példa: 5000 felhasználó MFA-kiszolgáló jelenleg engedélyezve van. Az MFA-rendszer elosztja ezt a számot 31, és a jelentések 161.29 felhasználók az adott napra. Holnap engedélyezése további 15 felhasználót, így az MFA jelenti 161.77 felhasználók az adott napra. Az elszámolási időszakban a végén az Azure-előfizetés terhére felhasználók teljes száma ad legfeljebb 5000 körül.
   >
   > A számlázás 2. példa: Licenccel rendelkező felhasználók és a felhasználók anélkül, így van egy felhasználónkénti Azure MFA-szolgáltató a különbség a létre rendelkezik. Nincsenek 4500 Enterprise Mobility + Security-licencek a bérlő, de 5000 felhasználó engedélyezve van az MFA-hoz. Az Azure-előfizetés 500 felhasználónak kell fizetnie, napokra és más 16.13 felhasználói naponta jelentett.
   >

1. **Hitelesítésenként** – a nagyvállalatoknak, amely engedélyezi a felhasználók, akik ritkábban hitelesítésre van szükség nagyszámú kétlépéses ellenőrzést. A kétlépéses ellenőrzési kérelmeket, függetlenül attól, hogy ezen ellenőrzések sikeres, vagy a rendszer megtagadta a száma alapján számoljuk. A számlázás az Azure-használati utasítás a csomagokban 10 hitelesítésből álló megjelenik, és naponta jelentett.

   > [!NOTE]
   > A számlázás 3. példa: Jelenleg az Azure MFA szolgáltatás kapott 3,105 kétlépéses ellenőrzési kérelmeket. Az Azure-előfizetés elszámolási 310.5 hitelesítési csomagokat.
   >

Fontos megjegyezni, hogy Ön is rendelkezik licencekkel, de továbbra is díjat kell fizetnie a használat alapú konfigurálást. Ha beállította a hitelesítés az Azure MFA-szolgáltató, számlázzuk ki minden kétlépéses ellenőrzési kérelmet, még akkor is ezeket a műveleteket a licenccel rendelkező felhasználók. Ha egy tartományhoz, amely az Azure AD-bérlő nem kapcsolódik a beállítva a felhasználónkénti Azure MFA-szolgáltató, használatáért engedélyezett felhasználónként akkor is, ha a felhasználók rendelkeznek licenccel az Azure ad-ben.

## <a name="next-steps"></a>További lépések

- További díjszabási információkért tekintse meg a [Azure MFA szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

- Döntse el, hogy üzembe helyezése az Azure MFA [a felhőben vagy helyszíni](concept-mfa-whichversion.md)
