---
title: Hitelesítés és engedélyezés az Azure térbeli horgonyokhoz | Microsoft Docs
description: Ismerje meg, hogy az alkalmazás vagy szolgáltatás milyen módon tud hitelesíteni az Azure térbeli Horgonyokban, valamint az Azure térbeli horgonyokhoz való hozzáférést biztosító vezérlési szinteket.
author: julianparismorgan
manager: vriveras
services: azure-spatial-anchors
ms.author: pmorgan
ms.date: 05/28/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 850748462f0273f2dfb1522d900ce9f1b2156d2a
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68517072"
---
# <a name="authentication-and-authorization-to-azure-spatial-anchors"></a>Hitelesítés és engedélyezés az Azure térbeli horgonyokhoz

Ebben a szakaszban az Azure térbeli horgonyok alkalmazásból vagy webszolgáltatásból való hitelesítésének különféle módjait ismertetjük, valamint az Azure-címtár (Azure AD) szerepköralapú Access Control használatának módja a térbeli horgonyok fiókjaihoz való hozzáférés szabályozásához.  

## <a name="overview"></a>Áttekintés

![Az Azure térbeli horgonyok hitelesítésének áttekintése](./media/spatial-anchors-authentication-overview.png)

Egy adott Azure térbeli horgonyhoz tartozó fiók eléréséhez az ügyfeleknek először hozzáférési tokent kell beszerezniük az Azure Mixed Reality biztonságijogkivonat-szolgáltatásból (STS). Az STS Live-ból 24 órán keresztül beszerzett tokenek, valamint a térbeli horgonyok szolgáltatásaira vonatkozó információk, amelyek lehetővé teszik a fiók engedélyezési döntését, és biztosítják, hogy csak a jogosult résztvevői férhessenek hozzá a fiókhoz. 

Hozzáférési jogkivonatok bármelyik fiók kulcsaiból vagy az Azure AD-ből kiállított jogkivonatokból szerezhetők be. 

A fiókok kulcsai lehetővé teszik az Azure térbeli horgonyok szolgáltatás használatának gyors megkezdését; az alkalmazás éles környezetben való üzembe helyezése előtt azonban javasoljuk, hogy frissítse az alkalmazást az Azure AD-alapú hitelesítés használatára. 

Az Azure AD-hitelesítési tokenek kétféleképpen érhetők el:

- Ha vállalati alkalmazást hoz létre, és a vállalata az Azure ad-t használja identitásrendszer, használhat felhasználói Azure ad-hitelesítést az alkalmazásban, és hozzáférést biztosíthat a térbeli horgonyokhoz a meglévő Azure ad-beli biztonsági csoportok használatával, vagy közvetlenül a szervezet felhasználói számára. 
- Ellenkező esetben javasoljuk, hogy az Azure AD-jogkivonatokat egy, az alkalmazást támogató webszolgáltatásból szerezze be. A támogató webszolgáltatás használata a javasolt hitelesítési módszer az üzemi alkalmazások számára, mivel elkerüli a hitelesítő adatok beágyazását az ügyfélalkalmazás Azure térbeli horgonyokhoz való hozzáféréséhez. 

## <a name="account-keys"></a>Fiókkulcsok

Az első lépésekhez az Azure térbeli horgonyok fiókjához való hozzáféréshez használható fiókok kulcsai. A fiók kulcsait a Azure Portal fogja megtalálni. Navigáljon a fiókjához, és válassza a "kulcsok" fület.

![Az Azure térbeli horgonyok hitelesítésének áttekintése](../../../includes/media/spatial-anchors-get-started-create-resource/view-account-key.png)


A rendszer két kulcsot tesz elérhetővé, amelyek egyszerre érvényesek a térbeli horgonyok fiókhoz való hozzáféréshez. Javasoljuk, hogy rendszeresen frissítse a fiók eléréséhez használt kulcsot. a két különálló érvényes kulcs lehetővé teszi az ilyen frissítések állásidő nélküli engedélyezését; csak az elsődleges kulcsot és a másodlagos kulcsot kell frissítenie. 

Az SDK beépített támogatást nyújt a fiókok kulcsaival történő hitelesítéshez; egyszerűen be kell állítania a AccountKey tulajdonságot a cloudSession objektumon. 

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccountKey = @"MyAccountKey";
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
_cloudSession.configuration.accountKey = @"MyAccountKey";
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accountKey = "MyAccountKey"
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccountKey("MyAccountKey");
```

# <a name="c-ndktabcpp"></a>[C++NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccountKey(R"(MyAccountKey)");
```

# <a name="c-winrttabcppwinrt"></a>[C++WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccountKey(LR"(MyAccountKey)");
```

---

Ha elkészült, az SDK kezeli a hozzáférési tokenhez tartozó fiók kulcsának cseréjét, valamint az alkalmazáshoz tartozó jogkivonatok szükséges gyorsítótárazását. 

> [!WARNING] 
> A fiók kulcsainak használata ajánlott a gyors beléptetéshez, de csak a fejlesztés és a prototípusok során. Erősen ajánlott, hogy az alkalmazást az élesben lévő beágyazott fiók kulcsa alapján szállítsa az alkalmazásba, és Ehelyett használja a következőként felsorolt felhasználó-vagy szolgáltatás-alapú Azure AD-hitelesítési módszereket.

## <a name="azure-ad-user-authentication"></a>Azure AD-beli felhasználói hitelesítés

Azure Active Directory felhasználókat célzó alkalmazások esetén az ajánlott módszer egy Azure ad-jogkivonat használata a felhasználó számára, amely a ADAL-könyvtár használatával szerezhető be a következő dokumentációban leírtak szerint: [https://docs.microsoft.com/azure/active-directory/develop/v1-overview](../../active-directory/develop/v1-overview.md); kövesse a felsorolt lépéseket. a "Gyors indítás" alatt, amely a következőket tartalmazza:

1. Konfiguráció a Azure Portalban
    1.  Az alkalmazás regisztrálása az Azure AD-ben **natív alkalmazásként**. A regisztrálás részeként meg kell határoznia, hogy az alkalmazás több-bérlős vagy sem, és megadja az alkalmazáshoz engedélyezett átirányítási URL-címeket.  
    2.  Adja meg az alkalmazás vagy a felhasználók számára az erőforráshoz való hozzáférést: 
        1.  Navigáljon a térbeli horgonyok erőforrásához Azure Portal
        2.  Váltson a **hozzáférés-vezérlés (iam)** lapra
        3.  A **szerepkör-hozzárendelés hozzáadása** megnyomva
            1.  [Szerepkör kiválasztása](#role-based-access-control)
            2.  A **Select (kiválasztás** ) mezőben adja meg azon felhasználók, csoportok és/vagy alkalmazások nevét, amelyekhez hozzáférést szeretne rendelni. 
            3.  Kattintson a **Mentés gombra**.
2. A kódban:
    1.  Ügyeljen arra, hogy a saját Azure AD-alkalmazásának **alkalmazás** -azonosítóját és átirányítási **URI-ját** használja az **ügyfél-azonosító** és a **RedirectUri** paraméterként a ADAL-ben.
    2.  Adja meg a bérlő adatait:
        1.  Ha az alkalmazás **csak a saját szervezetet**támogatja, cserélje le ezt az értéket a **bérlői azonosító** vagy a **bérlő nevére** (például contoso.microsoft.com).
        2.  Ha az alkalmazás **bármely szervezeti címtárban**támogatja a fiókokat, cserélje le ezt az értéket szervezetekkel
        3.  Ha az alkalmazás támogatja az **összes Microsoft-fiók felhasználót**, cserélje le ezt az értéket közösre
    3.  A jogkivonat-kérelemben állítsa be az erőforrást https://sts.mixedreality.azure.com a következőre: "". Ez az "erőforrás" arra utal az Azure AD-ra, hogy az alkalmazás jogkivonatot kér az Azure térbeli horgonyok szolgáltatáshoz.  

Ezzel az alkalmazásnak képesnek kell lennie az Azure AD-token ADAL való beszerzésére; beállíthatja, hogy az Azure AD-jogkivonat **authenticationToken** legyen a felhőalapú munkamenet-konfigurációs objektumon. 

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AuthenticationToken = @"MyAuthenticationToken";
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
_cloudSession.configuration.authenticationToken = @"MyAuthenticationToken";
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.authenticationToken = "MyAuthenticationToken"
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAuthenticationToken("MyAuthenticationToken");
```

# <a name="c-ndktabcpp"></a>[C++NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AuthenticationToken(R"(MyAuthenticationToken)");
```

# <a name="c-winrttabcppwinrt"></a>[C++WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AuthenticationToken(LR"(MyAuthenticationToken)");
```

---

## <a name="azure-ad-service-authentication"></a>Azure AD-szolgáltatás hitelesítése

Az Azure térbeli horgonyokat használó alkalmazások éles környezetben való üzembe helyezéséhez ajánlott lehetőség, hogy kihasználja a hitelesítési kérelmeket közvetítő háttér-szolgáltatást. Az általános sémának az alábbi ábrán leírt módon kell szerepelnie:

![Az Azure térbeli horgonyok hitelesítésének áttekintése](./media/spatial-anchors-aad-authentication.png)

Ez azt feltételezi, hogy az alkalmazás saját mechanizmust használ (például: Microsoft-fiók, PlayFab, Facebook, Google ID, egyéni Felhasználónév/jelszó stb.) hitelesítés a háttér-szolgáltatásban. Miután a felhasználók hitelesítése megtörtént a háttér-szolgáltatásban, a szolgáltatás lekérhet egy Azure AD-tokent, kicserélheti az Azure térbeli horgonyok hozzáférési jogkivonatára, majd visszaküldheti azt az ügyfélalkalmazás számára.

Az Azure ad hozzáférési jogkivonatot a ADAL-könyvtár használatával kérdezi le a következő dokumentációban [https://docs.microsoft.com/azure/active-directory/develop/v1-overview](../../active-directory/develop/v1-overview.md)leírtak szerint:. kövesse a "Gyors indítás" szakaszban felsorolt lépéseket, amelyek a következőket tartalmazzák:

1.  Konfiguráció a Azure Portalban:
    1.  Az alkalmazás regisztrálása az Azure AD-ben:
        1.  Azure Portalban navigáljon a **Azure Active Directory**, és válassza az **alkalmazás** -regisztrációk lehetőséget.
        2.  **Új alkalmazás regisztrációjának** kiválasztása
        3.  Adja meg az alkalmazás nevét, válassza a **webalkalmazás/API** lehetőséget az alkalmazás típusaként, és adja meg a szolgáltatáshoz tartozó hitelesítési URL-címet. Ezután nyomja meg a **create (létrehozás**) gombra.
        4.  Az alkalmazásban nyomja meg a **Beállítások**elemet, majd kattintson a **kulcsok** fülre. Adja meg a kulcs nevét, válasszon ki egy időtartamot, és kattintson a **Mentés gombra**. Ügyeljen arra, hogy az adott időpontban megjelenő kulcs értékét mentse, mert a webszolgáltatás kódjában szerepelnie kell a szolgáltatásnak.
    2.  Adja meg az alkalmazás és/vagy a felhasználók hozzáférését az erőforráshoz:
        1.  Navigáljon a térbeli horgonyok erőforrásához Azure Portal
        2.  Váltson a **hozzáférés-vezérlés (iam)** lapra
        3.  A **szerepkör-hozzárendelés hozzáadása** megnyomva
        1.  [Szerepkör kiválasztása](#role-based-access-control)
        2.  A **Select (kiválasztás** ) mezőben adja meg a létrehozott alkalmazás (ok) nevét, és amelyhez hozzáférést szeretne hozzárendelni. Ha azt szeretné, hogy az alkalmazás felhasználói különböző szerepkörökkel rendelkezzenek a térbeli horgonyok fiókjában, több alkalmazást kell regisztrálnia az Azure AD-ben, és hozzá kell rendelnie egy külön szerepkörhöz. Ezután implementálja az engedélyezési logikát, hogy a megfelelő szerepkört használja a felhasználók számára.  
    3.  Kattintson a **Mentés gombra**.
2.  A kódban (Megjegyzés: a GitHubon található szolgáltatási mintát használhatja):
    1.  Ügyeljen arra, hogy a saját Azure AD-alkalmazásának alkalmazás-AZONOSÍTÓját, az alkalmazás titkos kulcsát és átirányítási URI-ját használja az ügyfél-azonosító, a titkos kulcs és a RedirectUri paraméterként a ADAL
    2.  Adja meg a bérlő AZONOSÍTÓját a saját AAAzure, és adja hozzá a bérlő AZONOSÍTÓját a ADAL-ben található Authority paraméterben.
    3.  A jogkivonat-kérelemben állítsa be az erőforrást https://sts.mixedreality.azure.com a következőre: "" 

Ezzel a háttér-szolgáltatás lekérheti az Azure AD-tokent. Ezután egy MR-tokenre cserélheti azt, amelyet vissza fog térni az ügyfélnek. A MR-tokenek lekéréséhez egy Azure AD-tokent kell használni REST-hívással. Íme egy példa a hívásra:

```
GET https://mrc-auth-prod.trafficmanager.net/Accounts/35d830cb-f062-4062-9792-d6316039df56/token HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni<truncated>FL8Hq5aaOqZQnJr1koaQ
Host: mrc-auth-prod.trafficmanager.net
Connection: Keep-Alive

HTTP/1.1 200 OK
Date: Sun, 24 Feb 2019 08:00:00 GMT
Content-Type: application/json; charset=utf-8
Content-Length: 1153
Accept: application/json
MS-CV: 05JLqWeKFkWpbdY944yl7A.0
{"AccessToken":"eyJhbGciOiJSUzI1NiIsImtpZCI6IjI2MzYyMTk5ZTI2NjQxOGU4ZjE3MThlM2IyMThjZTIxIiwidHlwIjoiSldUIn0.eyJqdGkiOiJmMGFiNWIyMy0wMmUxLTQ1MTQtOWEzNC0xNzkzMTA1NTc4NzAiLCJjYWkiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJ0aWQiOiIwMDAwMDAwMC0wMDAwLTAwMDAtMDAwMC0wMDAwMDAwMDAwMDAiLCJhaWQiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJhYW8iOi0xLCJhcHIiOiJlYXN0dXMyIiwicmlkIjoiL3N1YnNjcmlwdGlvbnMvNzIzOTdlN2EtNzA4NC00ODJhLTg3MzktNjM5Y2RmNTMxNTI0L3Jlc291cmNlR3JvdXBzL3NhbXBsZV9yZXNvdXJjZV9ncm91cC9wcm92aWRlcnMvTWljcm9zb2Z0Lk1peGVkUmVhbGl0eS9TcGF0aWFsQW5jaG9yc0FjY291bnRzL2RlbW9fYWNjb3VudCIsIm5iZiI6MTU0NDU0NzkwMywiZXhwIjoxNTQ0NjM0MzAzLCJpYXQiOjE1NDQ1NDc5MDMsImlzcyI6Imh0dHBzOi8vbXJjLWF1dGgtcHJvZC50cmFmZmljbWFuYWdlci5uZXQvIiwiYXVkIjoiaHR0cHM6Ly9tcmMtYW5jaG9yLXByb2QudHJhZmZpY21hbmFnZXIubmV0LyJ9.BFdyCX9UJj0i4W3OudmNUiuaGgVrlPasNM-5VqXdNAExD8acFJnHdvSf6uLiVvPiQwY1atYyPbOnLYhEbIcxNX-YAfZ-xyxCKYb3g_dbxU2w8nX3zDz_X3XqLL8Uha-rkapKbnNgxq4GjM-EBMCill2Svluf9crDmO-SmJbxqIaWzLmlUufQMWg_r8JG7RLseK6ntUDRyDgkF4ex515l2RWqQx7cw874raKgUO4qlx0cpBAB8cRtGHC-3fA7rZPM7UQQpm-BC3suXqRgROTzrKqfn_g-qTW4jAKBIXYG7iDefV2rGMRgem06YH_bDnpkgUa1UgJRRTckkBuLkO2FvA"}
```

Ahol az engedélyezési fejléc a következőképpen van formázva:`Bearer <accoundId>:<accountKey>`

A válasz pedig az MR tokent tartalmazza egyszerű szövegben.
 
Ekkor a rendszer visszaküldi az MR tokent az ügyfélnek. Az ügyfélalkalmazás ezután a Felhőbeli munkamenet-konfigurációban állíthatja be hozzáférési jogkivonatként.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccessToken = @"MyAccessToken";
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
_cloudSession.configuration.accessToken = @"MyAccessToken";
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accessToken = "MyAccessToken"
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccessToken("MyAccessToken");
```

# <a name="c-ndktabcpp"></a>[C++NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccessToken(R"(MyAccessToken)");
```

# <a name="c-winrttabcppwinrt"></a>[C++WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccessToken(LR"(MyAccessToken)");
```

---

## <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés

Az alkalmazásokhoz, szolgáltatásokhoz vagy a szolgáltatás Azure AD-felhasználóinak biztosított hozzáférés szintjének szabályozásához a következő szerepköröket hozta létre, amelyeket szükség szerint rendelhet hozzá az Azure térbeli szerkesztőpontok fiókjaihoz:

- **Térbeli horgonyok fiókjának tulajdonosa**: az e szerepkörrel rendelkező alkalmazások vagy felhasználók létrehozhatnak térbeli horgonyokat, lekérdezéseket végezhetnek, és törölhetik azokat. Ha a fiók kulcsainak használatával hitelesíti a fiókot, a rendszer hozzárendeli a **térbeli horgonyok fiók tulajdonosi** szerepkörét a hitelesített rendszerbiztonsági tag számára. 
- **Térbeli horgonyok fiókjának közreműködői**: a szerepkörrel rendelkező alkalmazások vagy felhasználók létrehozhatnak térbeli horgonyokat, lekérdezéseket végezhetnek, de nem törölhetik azokat. 
- **Térbeli horgonyok fiókjának olvasója**: azok az alkalmazások vagy felhasználók, akik ezt a szerepkört használják, csak a térbeli horgonyok lekérdezésére képesek, de újakat nem hozhatnak létre, törölhetik a meglévőket, vagy frissíthetik a metaadatokat a térbeli horgonyokon. Ez általában olyan alkalmazásokhoz használatos, ahol egyes felhasználók a környezetet használják, míg mások csak az adott környezetbe helyezett horgonyokat tudják visszahívni.

## <a name="next-steps"></a>További lépések

Hozza létre első alkalmazását az Azure térbeli Horgonyokkal.

> [!div class="nextstepaction"]
> [Unity](../unity-overview.yml)

> [!div class="nextstepaction"]
> [iOS](../quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](../quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [HoloLens](../quickstarts/get-started-hololens.md)
