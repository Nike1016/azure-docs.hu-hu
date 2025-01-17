---
title: Gyors útmutató – Asztali alkalmazásba való bejelentkezés konfigurálása Azure Active Directory B2C-vel | Microsoft Docs
description: Azure Active Directory B2C-alapú bejelentkezést támogató asztali ASP.NET-mintaalkalmazás futtatása.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: quickstart
ms.custom: mvc
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 636e47c1d0c689dd9660f8bf01ada571d3824961
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67835459"
---
# <a name="quickstart-set-up-sign-in-for-a-desktop-app-using-azure-active-directory-b2c"></a>Gyors útmutató: Jelentkezzen be az Azure Active Directory B2C asztali alkalmazások beállítása

Az Azure Active Directory (Azure AD) B2C felhőalapú identitáskezelést nyújt az alkalmazás, az üzlet és az ügyfelek védelme érdekében. Az Azure AD B2C nyílt szabványú protokollokkal teszi lehetővé az alkalmazások hitelesítését közösségi hálózati és vállalati fiókokon. Ebben a rövid útmutatóban egy asztali Windows megjelenítési alaprendszerbeli (WPF-) alkalmazással jelentkezik be egy közösségi identitásszolgáltatót használva, és az Azure AD B2C által védett webes API-t hív meg.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

- [A Visual Studio 2019](https://www.visualstudio.com/downloads/) együtt a **ASP.NET és webfejlesztési** számítási feladatok.
- Egy Facebook, Google, Microsoft vagy Twitter közösségi fiók.
- [Töltse le a zip-fájlt](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/master.zip), vagy a klónozza a mintául szolgáló webalkalmazást a GitHubról.

    ```
    git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
    ```

## <a name="run-the-application-in-visual-studio"></a>Az alkalmazás futtatása a Visual Studióban

1. A mintaalkalmazás projektmappájában nyissa meg az **active-directory-b2c-wpf.sln** megoldást a Visual Studióban.
2. Nyomja le az **F5** billentyűt az alkalmazás hibakereséséhez.

## <a name="sign-in-using-your-account"></a>Bejelentkezés saját fiókkal

1. Kattintson a **Sign in** (Bejelentkezés) gombra a **Sign Up or Sign In** (Regisztráció vagy bejelentkezés) munkafolyamat elindításához.

    ![A mintaalkalmazás WPF képernyőképe](media/active-directory-b2c-quickstarts-desktop-app/wpf-sample-application.png)

    A minta több regisztrációs beállítást támogatja. A lehetőségek közé tartozik egy közösségi identitásszolgáltatót használva, vagy egy helyi fiók az e-mail-cím létrehozása. Ehhez a gyors útmutatóhoz Facebook, Google vagy Twitter közösségi identitásszolgáltatótól származó fiókot használjon.


2. Az Azure AD B2C a minta-webalkalmazáshoz egy Wingtip Toys nevű fiktív márka egyéni bejelentkezési lapját jeleníti meg. Ha közösségi identitásszolgáltatóval szeretne regisztrálni, kattintson a használni kívánt identitásszolgáltató gombjára.

    ![Identitásszolgáltató bejelentkezési vagy regisztrációs oldaláról](media/active-directory-b2c-quickstarts-desktop-app/sign-in-or-sign-up-wpf.png)

    Hitelesítést (bejelentkezés) használatával a közösségi fiók hitelesítő adatait, és engedélyezze az alkalmazás a közösségi fiók származó információk olvasásához. A hozzáférés biztosításával az alkalmazás profiladatokat kérhet le a közösségi fiókból, például a nevét és a települését.

2. Fejezze be az identitásszolgáltató bejelentkezési folyamatát.

    Az új fiókprofil részletei előre ki vannak töltve a közösségi fiókja adataival.

## <a name="edit-your-profile"></a>Saját profil szerkesztése

Az Azure AD B2C-funkcióival a felhasználók frissíthetik a profiljukat. A mintául szolgáló webalkalmazás a munkafolyamat az Azure AD B2C-vel szerkesztési profil felhasználói folyamatot használ.

1. Az alkalmazás menüsávján kattintson az **Edit profile** (Profil szerkesztése) gombra a létrehozott profil szerkesztéséhez.

    ![WPF-mintaalkalmazás gomb profil szerkesztése](media/active-directory-b2c-quickstarts-desktop-app/edit-profile-wpf.png)

2. Válassza ki a létrehozott fiókhoz rendelt identitásszolgáltatót. Ha például a Twittert használta identitásszolgáltatóként a fiók létrehozásakor, válassza a Twittert a hozzárendelt profil adatainak módosításához.

3. Módosítsa a **Display name** (Megjelenítendő név) és a **City** (Város) mezőt, majd kattintson a **Continue** (Folytatás) gombra.

    Ekkor a *Token info* (Jogkivonat adatai) szövegmezőben megjelenik egy új hozzáférési jogkivonat. Ha szeretné ellenőrizni a profilján végzett módosításokat, másolja és illessze be a hozzáférési jogkivonatot a https://jwt.ms jogkivonat-dekóderbe.

## <a name="access-a-protected-api-resource"></a>Védett API-erőforrás elérése

Kattintson a **Call API** (API meghívása) elemre, amellyel kérelmet küldhet a védett erőforrásnak.

    ![Call API](media/active-directory-b2c-quickstarts-desktop-app/call-api-wpf.png)

    The application includes the Azure AD access token in the request to the protected web API resource. The web API sends back the display name contained in the access token.

Az Azure AD B2C felhasználói fiókkal sikeresen intézett hitelesített hívást egy Azure AD B2C által védett webes API-hoz.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az Azure AD B2C-bérlőt ahhoz is használhatja, ha más Azure AD B2C gyors útmutatókat vagy oktatóanyagokat is ki szeretne próbálni. Ha már nincs szüksége rá, akkor [törölheti az Azure AD B2C-bérlőt](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>További lépések

Ez a rövid útmutatóban használt egy mintául szolgáló asztali alkalmazást:

* Jelentkezzen be egy egyéni bejelentkezési oldalon
* Jelentkezzen be egy közösségi identitásszolgáltatót
* Az Azure AD B2C-fiók létrehozása
* Webes API Azure AD B2C által védett meghívása

Most már hozzákezdhet saját Azure AD B2C-bérlőjének létrehozásához.

> [!div class="nextstepaction"]
> [Azure Active Directory B2C-bérlő létrehozása az Azure Portalon](tutorial-create-tenant.md)
