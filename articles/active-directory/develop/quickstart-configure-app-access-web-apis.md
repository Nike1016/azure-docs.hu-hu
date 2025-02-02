---
title: Alkalmazás konfigurálása a webes API-k eléréséhez – Microsoft Identity platform
description: Megtudhatja, hogyan konfigurálhatja a Microsoft Identity Platformon regisztrált alkalmazást úgy, hogy átirányítási URI-t vagy URI-kat, hitelesítő adatokat vagy webes API-k elérésére vonatkozó engedélyeket is tartalmazzon.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/07/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: lenalepa, aragra, sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: 937fca5698378a8c877b4a981557f87d06170e9a
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68879342"
---
# <a name="quickstart-configure-a-client-application-to-access-web-apis"></a>Gyors útmutató: Ügyfélalkalmazás konfigurálása a webes API-k elérésére

Ahhoz, hogy egy webes/bizalmas ügyfélalkalmazás részt vehessen egy hitelesítést (és hozzáférési jogkivonat beszerzését) előíró engedélyezési folyamatban, biztonságos hitelesítő adatokat kell létesítenie. Az Azure Portal által támogatott alapértelmezett hitelesítési módszer egy ügyfél-azonosítót és egy titkos kulcsot igényel.

Ezenkívül mielőtt az ügyfél hozzáférhet egy erőforrás-alkalmazás webes API-jához (például a Microsoft Graph API-t), a hozzájárulási keretrendszer biztosítja, hogy az ügyfél a kért engedélyek alapján megkapja a szükséges engedélyt. Alapértelmezés szerint minden alkalmazás választhat engedélyeket a Microsoft Graph API-ból. A [Graph API „Bejelentkezés és a felhasználói profil olvasása” engedélye](https://developer.microsoft.com/graph/docs/concepts/permissions_reference#user-permissions) alapértelmezés szerint be van jelölve. [Kétféle engedély](developer-glossary.md#permissions) közül választhat minden egyes kívánt API esetében:

* **Alkalmazásengedély**: Az ügyfélalkalmazásnak a webes API-t közvetlenül a maga nevében kell elérnie (nincs felhasználói környezet). Az ilyen típusú engedély rendszergazdai hozzájárulást igényel, és nyilvános (asztali vagy mobil) ügyfélalkalmazások esetében nem alkalmazható.
* **Delegált engedély**: Az ügyfélalkalmazásnak a webes API-t a bejelentkezett felhasználó nevében, de a kiválasztott engedély által korlátozott hozzáféréssel kell elérnie. Az ilyen típusú engedély megadható a felhasználó által, hacsak nem igényel rendszergazdai hozzájárulást.

  > [!NOTE]
  > A delegált engedély alkalmazáshoz való hozzáadása nem biztosítja automatikusan a hozzájárulást a bérlőn belüli felhasználóknak. A felhasználóknak továbbra is manuálisan kell a futásidőben hozzájárulniuk a delegált engedélyeket, hacsak a rendszergazda nem hagyja azokat jóvá minden felhasználó nevében.

Ebben a rövid útmutatóban bemutatjuk, hogyan konfigurálhatja alkalmazását az alábbi célokra:

* [Átirányítási URI-k hozzáadása az alkalmazáshoz](#add-redirect-uris-to-your-application)
* [Speciális beállítások konfigurálása az alkalmazáshoz](#configure-advanced-settings-for-your-application)
* [Támogatott fióktípus módosítása](#modify-supported-account-types)
* [Hitelesítő adatok hozzáadása a webalkalmazáshoz](#add-credentials-to-your-web-application)
* [Webes API-k elérésére vonatkozó engedélyek hozzáadása](#add-permissions-to-access-web-apis)

## <a name="prerequisites"></a>Előfeltételek

Először is győződjön meg arról, hogy az alábbi előfeltételek teljesülnek:

* Megismerte a támogatott [engedélyekkel és hozzájárulással](v2-permissions-and-consent.md) kapcsolatos tudnivalókat, mivel fontos szem előtt tartania őket olyan alkalmazások készítésekor, amelyeket más felhasználóknak vagy alkalmazásoknak is használniuk kell.
* Rendelkezik olyan bérlővel, amelyhez regisztrált alkalmazások tartoznak.
  * Ha nincsenek regisztrált alkalmazásai, [ismerje meg, hogyan regisztrálhat alkalmazásokat a Microsoft Identity Platformon](quickstart-register-app.md).

## <a name="sign-in-to-the-azure-portal-and-select-the-app"></a>Bejelentkezés az Azure Portalra és az alkalmazás kiválasztása

Mielőtt konfigurálhatná az alkalmazást, végre kell hajtania az alábbi lépéseket:

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. Ha a fiókja több bérlőhöz biztosít hozzáférést, válassza ki a fiókját a jobb felső sarokban, és állítsa be a portál munkamenetét a kívánt Azure AD-bérlőre.
1. A bal oldali navigációs ablaktáblán válassza ki a **Azure Active Directory** szolgáltatást, majd válassza a **Alkalmazásregisztrációk**lehetőséget.
1. Keresse meg és jelölje ki a konfigurálni kívánt alkalmazást. Miután kijelölte az alkalmazást, az alkalmazás **Áttekintés** lapja vagy regisztrációs főoldala jelenik meg.
1. Az alkalmazás webes API-k elérésére való konfigurálásához kövesse az alábbi lépéseket:
    * [Átirányítási URI-k hozzáadása az alkalmazáshoz](#add-redirect-uris-to-your-application)
    * [Speciális beállítások konfigurálása az alkalmazáshoz](#configure-advanced-settings-for-your-application)
    * [Támogatott fióktípus módosítása](#modify-supported-account-types)
    * [Hitelesítő adatok hozzáadása a webalkalmazáshoz](#add-credentials-to-your-web-application)
    * [Webes API-k elérésére vonatkozó engedélyek hozzáadása](#add-permissions-to-access-web-apis)

## <a name="add-redirect-uris-to-your-application"></a>Átirányítási URI(-k) hozzáadása az alkalmazáshoz

Ha átirányítási URI-t szeretne hozzáadni alkalmazásához:

1. Az alkalmazás **Áttekintés** lapján válassza a **Hitelesítés** szakaszt.
1. Webes és nyilvános ügyfélalkalmazások egyéni átirányítási URI-jának hozzáadásához kövesse az alábbi lépéseket:
   1. Keresse meg a **Átirányítási URI** szakaszt.
   1. Válassza ki a létrehozni kívánt alkalmazás típusát: **webes** vagy **nyilvános (mobil és asztali) ügyfél**.
   1. Adja meg alkalmazása átirányítási URI-ját.
      * Webalkalmazások esetében az alkalmazás alap URL-címét adja meg. A `http://localhost:31544` például a helyi gépen futó webalkalmazás URL-címe lehet. A felhasználók ezzel az URL-címmel jelentkeznek be egy webes ügyfélalkalmazásba.
      * Nyilvános alkalmazások esetében azt az URI-t adja meg, amelyet az Azure AD a jogkivonatválaszok visszaadására használ. Adja meg az alkalmazáshoz tartozó értéket, például: `https://MyFirstApp`.

1. Ha a nyilvános (mobil, asztali) ügyfelekhez javasolt átirányítási URI-k közül szeretne választani, kövesse az alábbi lépéseket:
    1. Keresse meg a **Javasolt átirányítási URI-k nyilvános ügyfelekhez (mobil, asztali)** szakaszt.
    1. A jelölőnégyzetekkel válassza ki a megfelelő átirányítási URI-t vagy URI-kat az alkalmazása számára.

> [!NOTE]
> Próbálja ki az új **hitelesítési** beállításokat, ahol konfigurálhatja az alkalmazás beállításait a célként használni kívánt platform vagy eszköz alapján.
>
> A nézet megjelenítéséhez válassza az **új felhasználói élmény kipróbálása** lehetőséget az alapértelmezett **hitelesítési** oldal nézetben.
>
> ![A platform-konfiguráció nézet megjelenítéséhez kattintson az "próbálja ki az új felület" lehetőséget.](./media/quickstart-update-azure-ad-app-preview/authentication-try-new-experience-cropped.png)
>
> Ekkor megjelenik az [új platform- **konfigurációk** oldal](#configure-platform-settings-for-your-application).

### <a name="configure-advanced-settings-for-your-application"></a>Speciális beállítások konfigurálása az alkalmazáshoz

A regisztrálni kívánt alkalmazástól függően szükség lehet néhány további beállításra, például a következőkre:

* **Kijelentkezési URL-cím**
* Egyoldalas alkalmazások esetében engedélyezheti az **implicit hozzáférést** , és kiválaszthatja azokat a jogkivonatokat, amelyeket az engedélyezési végpontnak ki szeretne állítani.
* Az integrált Windows-hitelesítéssel, az eszköz kódjának folyamatával vagy a username/Password értékkel rendelkező asztali alkalmazások esetében az **alapértelmezett ügyfél típusa** szakaszban konfigurálja az **alkalmazás kezelése nyilvános ügyfélként** beállítást **Igen**értékre.
* Az élő SDK-val a Microsoft-fiók szolgáltatással való integrációhoz használt örökölt alkalmazások esetében konfigurálja az **élő SDK-támogatást**. Az új alkalmazásokhoz nincs szükség erre a beállításra.
* **Alapértelmezett ügyfél típusa**

### <a name="modify-supported-account-types"></a>Támogatott fióktípus módosítása

A **támogatott fióktípus** határozza meg, hogy ki használhatja az alkalmazást, vagy hogyan férhet hozzá az API-hoz.

Miután konfigurálta [a támogatott fióktípus beállításait](quickstart-register-app.md) , amikor először regisztrálta az alkalmazást, csak akkor módosíthatja ezt a beállítást az Application manifest Editor használatával, ha:

* A **AzureADMyOrg** vagy **AzureADMultipleOrgs** típusú fiókokat a **AzureADandPersonalMicrosoftAccount**értékre, vagy fordítva.
* A **AzureADMyOrg** a **AzureADMultipleOrgs**-re módosítható, vagy fordítva.

Meglévő alkalmazás-regisztráció támogatott fiók-típusainak módosítása:

* Lásd: [az alkalmazás jegyzékfájljának konfigurálása](reference-app-manifest.md) és `signInAudience` a kulcs frissítése.

## <a name="configure-platform-settings-for-your-application"></a>A platform beállításainak konfigurálása az alkalmazáshoz

[![Az alkalmazás beállításainak konfigurálása a platform vagy az eszköz alapján](./media/quickstart-update-azure-ad-app-preview/authentication-new-platform-configurations-expanded.png)](./media/quickstart-update-azure-ad-app-preview/authentication-new-platform-configurations-small.png#lightbox)

Az Alkalmazásbeállítások a platform vagy az eszköz alapján történő konfigurálásához célozza meg a következőt:

1. A **platform-konfigurációk** lapon válassza a **platform hozzáadása** lehetőséget, és válasszon az elérhető lehetőségek közül.

   ![A platformok konfigurálása lap megjelenítése](./media/quickstart-update-azure-ad-app-preview/authentication-platform-configurations-configure-platforms.png)

1. Adja meg a beállítások információit a kiválasztott platform alapján.

   | Platform                | Választás              | Konfigurációs beállítások            |
   |-------------------------|----------------------|-----------------------------------|
   | **Webalkalmazások**    | **Web**              | Adja meg az alkalmazás **átirányítási URI-ját** . |
   | **Mobil alkalmazások** | **iOS**              | Adja meg az alkalmazás **köteg**-azonosítóját, amely az info. plist fájlban vagy a Build Settings Xcode található. A köteg-azonosító hozzáadása automatikusan létrehoz egy átirányítási URI-t az alkalmazáshoz. |
   |                         | **Android**          | * Adja meg az alkalmazás **csomagjának nevét**, amelyet a AndroidManifest. xml fájlban talál.<br/>* Az **aláírás kivonatának**előállítása és megadása. Az aláírási kivonat hozzáadásakor a rendszer automatikusan létrehoz egy átirányítási URI-t az alkalmazáshoz.  |
   | **Asztali és eszközök**   | **Asztali és eszközök** | Választható. Válassza a javasolt átirányítási **URI** -k egyikét, ha asztali és eszközökhöz készült alkalmazásokat készít.<br/>Választható. Adjon meg egy **Egyéni átirányítási URI**-t, amely azt a helyet használja, ahol az Azure ad átirányítja a felhasználókat a hitelesítési kérésekre adott válaszként. Például olyan .NET Core-alkalmazásokhoz, ahol az interakciót szeretné `https://localhost`használni, használja a következőt:. |

   > [!IMPORTANT]
   > Olyan mobileszközök esetében, amelyek nem a legújabb MSAL függvénytárat használják, vagy nem használ közvetítőt, konfigurálnia kell az alkalmazások átirányítási URI-azonosítóit az asztali és a- **eszközök**esetében.

1. A kiválasztott platformtól függően előfordulhat, hogy további beállításokat is konfigurálhat. A **web** apps esetében a következőket teheti:
    * További átirányítási URI-k hozzáadása
    * Az **implicit engedélyezés** konfigurálásával kiválaszthatja az engedélyezési végpont által kiállított jogkivonatokat:
        * Egyoldalas alkalmazások esetében válassza a **hozzáférési tokenek** és az **azonosító tokenek** lehetőséget.
        * Webalkalmazások esetében válassza az **azonosító tokenek** lehetőséget.

## <a name="add-credentials-to-your-web-application"></a>Hitelesítő adatok hozzáadása a webalkalmazáshoz

Ha hitelesítő adatokat szeretne hozzáadni webalkalmazásához:

1. Az alkalmazás **Áttekintés** lapján válassza a **Tanúsítványok és titkos kódok** szakaszt.

1. Tanúsítvány hozzáadásához kövesse az alábbi lépéseket:

    1. Válassza a **Tanúsítvány feltöltése** lehetőséget.
    1. Válassza ki a feltölteni kívánt fájlt. A fájlnak a következő típusok egyikébe kell tartoznia: .cer, .pem vagy .crt.
    1. Válassza a **Hozzáadás** lehetőséget.

1. Titkos ügyfélkód hozzáadásához kövesse az alábbi lépéseket:

    1. Válassza az **Új titkos ügyfélkód** lehetőséget.
    1. Adja meg titkos ügyfélkódja leírását.
    1. Válassza ki az időtartamot.
    1. Válassza a **Hozzáadás** lehetőséget.

> [!NOTE]
> A konfiguráció módosításainak mentése után a jobb szélső oszlop tartalmazza majd a titkos ügyfélkód értékét. **Mindenképp másolja le az értéket** az ügyfélalkalmazás kódolása során való használathoz, mivel a lap elhagyását követően nem lehet majd megtekinteni.

## <a name="add-permissions-to-access-web-apis"></a>Webes API-k elérésére vonatkozó engedélyek hozzáadása

Ha erőforrás-API-k ügyfélről való elérésére vonatkozó engedélyt vagy engedélyeket kíván hozzáadni:

1. Az alkalmazás **Áttekintés** lapján válassza az **API-engedélyek** lehetőséget.
1. Nyomja meg **Az engedély hozzáadása** gombot.
1. Alapértelmezés szerint ebben a nézetben választhat a **Microsoft API-k** közül. Válassza ki az Önt érdeklő API-k szakaszát:
    * **Microsoft API-k** – itt a Microsoft API-kra, például a Microsoft Graph-ra vonatkozó engedélyeket választhatja ki.
    * **Saját szervezet által használt API-k** – itt a szervezete által közzétett vagy integrált API-kra vonatkozó engedélyeket választhatja ki.
    * **Saját API-k** – itt az Ön által közzétett API-kra vonatkozó engedélyeket választhatja ki.
1. Miután kiválasztotta az API-kat, **Az API-engedélyek kérése** lap jelenik meg. Ha az API delegált és alkalmazásengedélyeket egyaránt közzétesz, válassza ki, milyen típusú engedélyre van szüksége az alkalmazásának.
1. Miután végzett, válassza az **Engedélyek hozzáadása** lehetőséget. Újra megjelenik az **API-engedélyek** lap, ahol az engedélyek a mentést követően bekerülnek a táblázatba.

## <a name="next-steps"></a>További lépések

Tekintse meg az alkalmazásokra vonatkozó alábbi rövid alkalmazásfelügyeleti útmutatókat is:

* [Alkalmazás regisztrálása a Microsoft Identity Platformon](quickstart-register-app.md)
* [Alkalmazás konfigurálása webes API-k közzétételére](quickstart-configure-app-expose-web-apis.md)
* [Alkalmazás által támogatott fiókok módosítása](quickstart-modify-supported-accounts.md)
* [Microsoft Identity Platformon regisztrált alkalmazás eltávolítása](quickstart-remove-app.md)

Ha szeretne többet megtudni a regisztrált alkalmazásokat jelölő két Azure AD-objektumról és azok kapcsolatáról, tekintse meg az [alkalmazás- és szolgáltatásnév-objektumokat](app-objects-and-service-principals.md) ismertető szakaszt.

Ha szeretne többet megtudni az Azure Active Directoryt használó alkalmazások fejlesztése során alkalmazandó védjegyzési irányelvekről, tekintse meg az [alkalmazások védjegyzési irányelveit](howto-add-branding-in-azure-ad-apps.md) ismertető szakaszt.
