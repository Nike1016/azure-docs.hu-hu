---
title: Oktatóanyag – hitelesítés engedélyezése egyoldalas alkalmazásban – Azure Active Directory B2C
description: Megtudhatja, hogyan használhatja a Azure Active Directory B2Ct egy egyoldalas alkalmazás (JavaScript) felhasználói bejelentkezésének biztosításához.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 07/24/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 8683169e0f8464cdcf52600968f67f3622e61445
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69509583"
---
# <a name="tutorial-enable-authentication-in-a-single-page-application-using-azure-active-directory-b2c"></a>Oktatóanyag: Hitelesítés engedélyezése egyoldalas alkalmazásban Azure Active Directory B2C használatával

Ez az oktatóanyag bemutatja, hogyan használhatja a Azure Active Directory (Azure AD) B2C-t a bejelentkezéshez és a felhasználók regisztrálásához egy egyoldalas alkalmazásban (SPA). Azure AD B2C lehetővé teszi az alkalmazások számára, hogy a nyílt szabványos protokollok használatával hitelesítsék a közösségi fiókokat, a vállalati fiókokat és a Azure Active Directory fiókokat.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az alkalmazás frissítése Azure AD B2C
> * A minta beállítása az alkalmazás használatára
> * Regisztráció a felhasználói folyamat használatával

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag lépéseinek folytatása előtt a következő Azure AD B2C erőforrásokra van szükség:

* [Azure AD B2C bérlő](tutorial-create-tenant.md)
* Az [alkalmazás regisztrálva](tutorial-register-applications.md) van a bérlőben
* A bérlőn [létrehozott felhasználói folyamatok](tutorial-create-user-flows.md)

Emellett a helyi fejlesztési környezetben a következőkre lesz szüksége:

* Kódszerkesztő, például [Visual Studio Code](https://code.visualstudio.com/) vagy [Visual Studio 2019](https://www.visualstudio.com/downloads/)
* [.NET Core SDK 2,2](https://dotnet.microsoft.com/download) vagy újabb
* [Node.js](https://nodejs.org/en/download/)

## <a name="update-the-application"></a>Az alkalmazás frissítése

Az előfeltételek részeként elvégzett második oktatóanyagban egy webalkalmazást regisztrált Azure AD B2Cban. Az oktatóanyagban szereplő példával való kommunikáció engedélyezéséhez hozzá kell adnia egy átirányítási URI-t az alkalmazáshoz Azure AD B2C.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja a felső menüben található **címtár és előfizetés** szűrő kiválasztásával, majd a bérlőt tartalmazó könyvtár kiválasztásával.
1. Válassza az **összes szolgáltatás** elemet a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
1. Válassza az **alkalmazások**lehetőséget, majd válassza ki a *webapp1* alkalmazást.
1. A **Válasz URL-cím**területen adja hozzá `http://localhost:6420`a címet.
1. Kattintson a **Mentés** gombra.
1. A Tulajdonságok lapon jegyezze fel az **alkalmazás azonosítóját**. Az alkalmazás-azonosítót egy későbbi lépésben kell használni, amikor frissíti a kódot az egyoldalas webalkalmazásban.

## <a name="get-the-sample-code"></a>A mintakód letöltése

Ebben az oktatóanyagban egy, a GitHubról letöltött mintakód-mintát konfigurál. A minta azt mutatja be, hogy egy egyoldalas alkalmazás hogyan használhatja Azure AD B2C a felhasználói regisztrációhoz és bejelentkezéshez, valamint egy védett webes API meghívásához.

[Töltse le a zip-fájlt](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip), vagy a klónozza a GitHubon található mintát.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

## <a name="update-the-sample"></a>A minta frissítése

Most, hogy beolvasta a mintát, frissítse a kódot a Azure AD B2C bérlői nevével és egy korábbi lépésben rögzített alkalmazás-AZONOSÍTÓval.

1. Nyissa `index.html` meg a fájlt a minta könyvtár gyökerében.
1. A definícióban módosítsa a clientId értéket a korábbi lépésben rögzített alkalmazás-azonosítóval. `msalConfig` Ezután frissítse a **hitelesítésszolgáltató** URI értékét a Azure ad B2C bérlői nevével. Frissítse az URI-t az előfeltételek egyikében létrehozott regisztrációs/bejelentkezési felhasználói folyamat nevével (például *B2C_1_signupsignin1*).

    ```javascript
    var msalConfig = {
        auth: {
            clientId: "00000000-0000-0000-0000-000000000000", //This is your client ID
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_susi", //This is your tenant info
            validateAuthority: false
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };
    ```

    Az oktatóanyagban használt felhasználói folyamat neve **B2C_1_signupsignin1**. Ha más felhasználói folyamat nevét használja, adja meg a `authority` nevet az érték mezőben.

## <a name="run-the-sample"></a>Minta futtatása

1. Nyisson meg egy konzolablak ablakát, és váltson a mintát tartalmazó könyvtárra. Példa:

    ```console
    cd active-directory-b2c-javascript-msal-singlepageapp
    ```
1. Futtassa az alábbi parancsot:

    ```
    npm install && npm update
    node server.js
    ```

    A konzol ablakban a helyileg futó Node. js-kiszolgáló portszáma látható:

    ```
    Listening on port 6420...
    ```

1. `http://localhost:6420` A böngészőben nyissa meg az alkalmazást.

A minta támogatja a regisztrációt, a bejelentkezést, a profil szerkesztését és a jelszó-visszaállítást. Ez az oktatóanyag kiemeli, hogy egy felhasználó hogyan regisztrál egy e-mail-cím használatával.

### <a name="sign-up-using-an-email-address"></a>Regisztrálás e-mail-címmel

1. A **Bejelentkezés** gombra kattintva kezdeményezzen egy korábbi lépésben megadott *B2C_1_signupsignin1* felhasználói folyamatot.
1. Az Azure AD B2C megjelenít egy bejelentkezési oldalt egy regisztrációs hivatkozással. Mivel még nem rendelkezik fiókkal, válassza a **regisztráció most** hivatkozást.
1. A regisztrációs munkafolyamat megjelenít egy lapot, amely a felhasználó azonosító adatait gyűjti be és ellenőrzi az e-mail-cím alapján. A regisztrációs munkafolyamat a felhasználó jelszavát és a felhasználói folyamatban definiált kért attribútumokat is gyűjti.

    Érvényes e-mail-címet használjon, és ellenőrizze az ellenőrző kód használatával. Állítson be egy jelszót. Adja meg a kért attribútumokhoz tartozó értékeket.

    ![A bejelentkezést és a regisztrációt követő felhasználói folyamat által bemutatott regisztrációs oldal](./media/active-directory-b2c-tutorials-desktop-app/sign-up-workflow.PNG)

1. A **Létrehozás** gombra kattintva helyi fiókot hozhat létre a Azure ad B2C könyvtárban.

Amikor kiválasztja a **Létrehozás**lehetőséget, a regisztrációs oldal bezárul, és megjelenik a bejelentkezési oldal.

Most már használhatja az e-mail-címét és jelszavát az alkalmazásba való bejelentkezéshez.

### <a name="error-insufficient-permissions"></a>Hiba: nem megfelelő engedélyek

A bejelentkezés után az alkalmazás nem megfelelő engedélyekkel kapcsolatos hibaüzenetet jelenít meg – ez **várható**:

```Output
ServerError: AADB2C90205: This application does not have sufficient permissions against this web resource to perform the operation.
Correlation ID: ce15bbcc-0000-0000-0000-494a52e95cd7
Timestamp: 2019-07-20 22:17:27Z
```

Ezt a hibaüzenetet kapja, mert a webalkalmazás megpróbál hozzáférni egy, a bemutató könyvtára által védett webes API-hoz ( *fabrikamb2c*). Mivel a hozzáférési jogkivonat csak az Azure AD-címtár esetében érvényes, az API-hívás ezért nem engedélyezett.

Ennek a hibának a kijavításához folytassa a sorozat következő oktatóanyagával (lásd a [következő lépéseket](#next-steps)) egy védett webes API létrehozásához a címtárban.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan végezheti el a következőket:

> [!div class="checklist"]
> * Az alkalmazás frissítése Azure AD B2C
> * A minta beállítása az alkalmazás használatára
> * Regisztráció a felhasználói folyamat használatával

Most lépjen a sorozat következő oktatóanyagára, hogy hozzáférést biztosítson egy védett webes API-hoz a FÜRDŐből:

> [!div class="nextstepaction"]
> [Oktatóanyag: Hozzáférés biztosítása egy ASP.NET Core webes API-hoz egy SPA használatával Azure AD B2C >](active-directory-b2c-tutorials-spa-webapi.md)
