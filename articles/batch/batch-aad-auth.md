---
title: Azure Active Directory használata Azure Batch szolgáltatási megoldások hitelesítéséhez | Microsoft Docs
description: A Batch támogatja az Azure AD-t a Batch szolgáltatásban történő hitelesítéshez.
services: batch
documentationcenter: .net
author: laurenhughes
manager: gwallace
editor: ''
tags: ''
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/18/2018
ms.author: lahugh
ms.openlocfilehash: 64921a2ab69306df0b7c3d968055e698dd6995e7
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68323945"
---
# <a name="authenticate-batch-service-solutions-with-active-directory"></a>Batch szolgáltatási megoldások hitelesítése Active Directory

Azure Batch támogatja a [Azure Active Directory][aad_about] (Azure ad) hitelesítését. Az Azure AD a Microsoft több-bérlős felhőalapú címtár-és Identitáskezelés-kezelő szolgáltatása. Az Azure maga az Azure AD-t használja az ügyfelek, a szolgáltatás-rendszergazdák és a szervezeti felhasználók hitelesítéséhez.

Az Azure AD-hitelesítés Azure Batch használatával történő használatakor kétféleképpen végezheti el a hitelesítést:

- **Integrált hitelesítéssel** hitelesítheti az alkalmazással kommunikáló felhasználókat. Az integrált hitelesítést használó alkalmazások összegyűjtik a felhasználó hitelesítő adatait, és ezeket a hitelesítő adatokat használják a Batch-erőforrásokhoz való hozzáférés hitelesítéséhez.
- Egy **egyszerű szolgáltatásnév** használatával hitelesítheti a felügyelet nélküli alkalmazást. Egy egyszerű szolgáltatásnév határozza meg az alkalmazáshoz tartozó házirendet és engedélyeket, hogy az alkalmazás az erőforrásokhoz való hozzáféréskor a futtatókörnyezetben legyen elérhető.

Az Azure AD-vel kapcsolatos további tudnivalókért tekintse meg a [Azure Active Directory dokumentációját](https://docs.microsoft.com/azure/active-directory/).

## <a name="endpoints-for-authentication"></a>Hitelesítéshez használt végpontok

A Batch-alkalmazások Azure AD-vel történő hitelesítéséhez néhány jól ismert végpontot kell tartalmaznia a kódban.

### <a name="azure-ad-endpoint"></a>Azure AD-végpont

Az alapszintű Azure AD-szolgáltató végpontja:

`https://login.microsoftonline.com/`

Az Azure AD-vel való hitelesítéshez ezt a végpontot kell használni a bérlői AZONOSÍTÓval (a címtár-AZONOSÍTÓval) együtt. A bérlő Azonosítóját az Azure AD-bérlő, a hitelesítéshez használandó azonosítja. A bérlő AZONOSÍTÓjának lekéréséhez kövesse a [Azure Active Directory bérlői azonosítójának](#get-the-tenant-id-for-your-active-directory)lekérése című szakasz lépéseit:

`https://login.microsoftonline.com/<tenant-id>`

> [!NOTE] 
> A bérlő-specifikus végpontra akkor van szükség, ha egy egyszerű szolgáltatásnév használatával végzi a hitelesítést. 
> 
> A bérlő-specifikus végpont nem kötelező, ha az integrált hitelesítés használatával végzi a hitelesítést, de ajánlott. Azonban használhatja az Azure AD Common Endpoint (általános) végpontját is. A Common Endpoint (általános hitelesítő adatok összegyűjtése) felületet biztosít, ha nincs megadva egy adott bérlő. Az általános végpont `https://login.microsoftonline.com/common`.
>
>

További információ az Azure AD-végpontokról: [hitelesítési forgatókönyvek az Azure ad-hez][aad_auth_scenarios].

### <a name="batch-resource-endpoint"></a>Batch erőforrás-végpont

A **Azure batch erőforrás-végpont** használatával szerezzen be egy jogkivonatot a kérelmeknek a Batch szolgáltatásba történő hitelesítéséhez:

`https://batch.core.windows.net/`

## <a name="register-your-application-with-a-tenant"></a>Alkalmazás regisztrálása Bérlővel

Az Azure AD hitelesítéshez való használatának első lépése az alkalmazás regisztrálása egy Azure AD-bérlőben. Az alkalmazás regisztrálása lehetővé teszi, hogy, hogy az Azure meghívható [Active Directory Authentication Library][aad_adal] (ADAL) a kódból. Az ADAL API-alkalmazását az Azure ad-ben való hitelesítéshez használt biztosít. Az alkalmazás regisztrálása kötelező, függetlenül attól, hogy integrált hitelesítést vagy egyszerű szolgáltatásnevet kíván használni.

Ha regisztrálja az alkalmazást, adja meg információkat az alkalmazásról, az Azure ad-hez. Az Azure AD ezt követően egy alkalmazás-azonosítót (más néven *ügyfél-azonosítót*) biztosít az alkalmazás Azure ad-hez való hozzárendeléséhez futásidőben. Az alkalmazás-AZONOSÍTÓval kapcsolatos további tudnivalókért tekintse meg az [alkalmazás-és szolgáltatásnév objektumait Azure Active Directoryban](../active-directory/develop/app-objects-and-service-principals.md).

A Batch-alkalmazás regisztrálásához kövesse az alkalmazások [hozzáadása](../active-directory/develop/quickstart-register-app.md) az [Azure Active Directory][aad_integrate]használatával történő integrálását ismertető szakasz lépéseit. Ha natív alkalmazásként regisztrálja az alkalmazást, megadhat bármely érvényes URI-t az **átirányítási URI**-hoz. Nem kell valódi végpontnak lennie.

Az alkalmazás regisztrálását követően megjelenik az alkalmazás azonosítója:

![Batch-alkalmazás regisztrálása az Azure AD-ben](./media/batch-aad-auth/app-registration-data-plane.png)

Az alkalmazások Azure AD-vel való regisztrálásával kapcsolatos további információkért lásd: [hitelesítési forgatókönyvek az Azure ad-hez](../active-directory/develop/authentication-scenarios.md).

## <a name="get-the-tenant-id-for-your-active-directory"></a>A Active Directory bérlői AZONOSÍTÓjának beolvasása

A bérlő azonosítója azonosítja azt az Azure AD-bérlőt, amely hitelesítési szolgáltatásokat biztosít az alkalmazás számára. A Bérlőazonosító lekéréséhez kövesse az alábbi lépéseket:

1. Az Azure Portalon válassza ki az Active Directoryban.
2. Kattintson a **Tulajdonságok** elemre.
3. Másolja a megadott GUID értéket a **címtár-azonosító**. Ennek az értéknek is nevezik a bérlő azonosítója.

![A könyvtár AZONOSÍTÓjának másolása](./media/batch-aad-auth/aad-directory-id.png)


## <a name="use-integrated-authentication"></a>Integrált hitelesítés használata

Az integrált hitelesítéssel történő hitelesítéshez meg kell adnia az alkalmazás engedélyeit a Batch szolgáltatás API-hoz való kapcsolódáshoz. Ez a lépés lehetővé teszi, hogy az alkalmazás a Batch szolgáltatás API-ját az Azure AD-vel hitelesítse.

Miután regisztrálta az alkalmazást, kövesse az alábbi lépéseket a Azure Portal, hogy hozzáférést biztosítson a Batch szolgáltatáshoz:

1. A Azure Portal bal oldali navigációs paneljén válassza a **minden szolgáltatás**lehetőséget. Kattintson az **alkalmazás**-regisztrációk elemre.
2. Keresse meg az alkalmazás nevét az alkalmazások regisztrációinak listájában:

    ![Az alkalmazás nevének megkeresése](./media/batch-aad-auth/search-app-registration.png)

3. Kattintson az alkalmazásra, majd a **Beállítások**elemre. Az a **API-hozzáférés** szakaszban jelölje be **szükséges engedélyek**.
4. Az a **szükséges engedélyek** panelen kattintson a **Hozzáadás** gombra.
5. Az **API kiválasztása lapon**keressen rá a Batch API kifejezésre. Keressen rá az egyes sztringekre, addig amíg meg nem találja az API-t:
    1. **MicrosoftAzureBatch**.
    2. **Microsoft Azure Batch**. Újabb Azure AD-bérlők ezt a nevet használhatják.
    3. A **ddbf3205-c6bd-46ae-8127-60eb93363864** a Batch API azonosítója. 
6. Miután megtalálta a Batch API-t, jelölje ki, majd kattintson a **kiválasztás**gombra.
7. Az **engedélyek kiválasztása**területen jelölje be a **hozzáférés Azure batch szolgáltatás** melletti jelölőnégyzetet, majd kattintson a **kiválasztás**gombra.
8. Kattintson a **Done** (Kész) gombra.

A **szükséges engedélyek** Windows mostantól azt mutatja, hogy az Azure ad-alkalmazás hozzáfér mindkét ADAL és a Batch szolgáltatás API-hoz. Az Azure AD-vel való első regisztráláskor az engedélyek automatikusan ADAL.

![API-engedélyek megadása](./media/batch-aad-auth/required-permissions-data-plane.png)

## <a name="use-a-service-principal"></a>Egyszerű szolgáltatásnév használata 

Felügyelet nélküli alkalmazást futtató alkalmazás hitelesítéséhez használjon egyszerű szolgáltatásnevet. Az alkalmazás regisztrálása után kövesse az alábbi lépéseket a Azure Portal egy egyszerű szolgáltatásnév konfigurálásához:

1. Kérjen titkos kulcsot az alkalmazáshoz.
2. Rendeljen hozzá egy RBAC-szerepkört az alkalmazáshoz.

### <a name="request-a-secret-key-for-your-application"></a>Az alkalmazás titkos kulcsának kérése

Ha az alkalmazás egy egyszerű szolgáltatással végzi a hitelesítést, az az alkalmazás AZONOSÍTÓját és egy titkos kulcsot is küld az Azure AD-nek. Létre kell hoznia és át kell másolnia a kód alapján használni kívánt titkos kulcsot.

Kövesse az alábbi lépéseket a Azure Portalban:

1. A Azure Portal bal oldali navigációs paneljén válassza a **minden szolgáltatás**lehetőséget. Kattintson az **alkalmazás**-regisztrációk elemre.
2. Keresse meg az alkalmazás nevét az alkalmazások regisztrációinak listájában.
3. Kattintson az alkalmazásra, majd a **Beállítások**elemre. Az **API-hozzáférés** szakaszban válassza a **kulcsok**elemet.
4. Kulcs létrehozásához adja meg a kulcs leírását. Ezután válasszon egy időtartamot egy vagy két év kulcsához. 
5. Kattintson a **Save (Mentés** ) gombra a kulcs létrehozásához és megjelenítéséhez. Másolja a kulcs értékét biztonságos helyre, mivel a panel elhagyása után nem fogja tudni elérni azt. 

    ![Titkos kulcs létrehozása](./media/batch-aad-auth/secret-key.png)

### <a name="assign-an-rbac-role-to-your-application"></a>RBAC-szerepkör társítása az alkalmazáshoz

Egy egyszerű szolgáltatással történő hitelesítéshez hozzá kell rendelnie egy RBAC-szerepkört az alkalmazáshoz. Kövesse az alábbi lépéseket:

1. A Azure Portal navigáljon az alkalmazás által használt batch-fiókhoz.
2. A Batch-fiók **Beállítások** paneljén válassza a **Access Control (iam)** lehetőséget.
3. Kattintson a **szerepkörök** -hozzárendelések fülre.
4. Kattintson a **szerepkör-hozzárendelés hozzáadása** gombra. 
5. A **szerepkör** legördülő listából válassza ki az alkalmazás _közreműködő_ vagy _olvasó_ szerepkörét. További információ ezekről a szerepkörökről: [a Azure Portal szerepköralapú Access Control első lépései](../role-based-access-control/overview.md).  
6. A **Select (kiválasztás** ) mezőben adja meg az alkalmazás nevét. Válassza ki az alkalmazást a listából, és kattintson a **Mentés**gombra.

Az alkalmazásnak ekkor meg kell jelennie a hozzáférés-vezérlési beállításaiban egy hozzárendelt RBAC-szerepkörrel. 

![RBAC-szerepkör társítása az alkalmazáshoz](./media/batch-aad-auth/app-rbac-role.png)

### <a name="get-the-tenant-id-for-your-azure-active-directory"></a>A Bérlőazonosító beszerzése az Azure Active Directory

A bérlő azonosítója azonosítja azt az Azure AD-bérlőt, amely hitelesítési szolgáltatásokat biztosít az alkalmazás számára. A Bérlőazonosító lekéréséhez kövesse az alábbi lépéseket:

1. Az Azure Portalon válassza ki az Active Directoryban.
2. Kattintson a **Tulajdonságok** elemre.
3. Másolja a megadott GUID értéket a **címtár-azonosító**. Ennek az értéknek is nevezik a bérlő azonosítója.

![A könyvtár AZONOSÍTÓjának másolása](./media/batch-aad-auth/aad-directory-id.png)


## <a name="code-examples"></a>Példák a kódokra

Az ebben a szakaszban szereplő példák azt mutatják be, hogyan lehet hitelesíteni az Azure AD-t az integrált hitelesítéssel és egy egyszerű szolgáltatásnév használatával. A példák többsége a .NET-et használja, de a fogalmak hasonlóak más nyelvekhez.

> [!NOTE]
> Egy Azure AD-hitelesítési jogkivonat egy óra elteltével lejár. Ha hosszú élettartamú **BatchClient** objektumot használ, javasoljuk, hogy minden kérelem esetében kérjen le egy tokent a ADAL-ből, hogy mindig érvényes jogkivonat legyen. 
>
>
> Ennek a .NET-ben való eléréséhez írjon egy metódust, amely lekéri a jogkivonatot az Azure AD-ből, és átadja ezt a metódust egy **BatchTokenCredentials** objektumnak meghatalmazottként. A delegált metódust minden, a Batch szolgáltatásnak küldött kérelemre meghívjuk, hogy érvényes jogkivonat legyen megadva. Alapértelmezés szerint a ADAL gyorsítótárazza a tokeneket, így a rendszer csak akkor kér le új jogkivonatot az Azure AD-ből, ha szükséges. Az Azure AD-jogkivonatokkal kapcsolatos további információkért lásd: [hitelesítési forgatókönyvek az Azure ad-hez][aad_auth_scenarios].
>
>

### <a name="code-example-using-azure-ad-integrated-authentication-with-batch-net"></a>Kód példa: Az Azure AD integrált hitelesítésének használata a Batch .NET-tel

A Batch .NET-ből származó integrált hitelesítéssel történő hitelesítéshez hivatkozzon a [Azure Batch .net](https://www.nuget.org/packages/Microsoft.Azure.Batch/) -csomagra és a [ADAL](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) -csomagra.

Adja meg a `using` következő utasításokat a kódban:

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Hivatkozzon a kódban található Azure AD-végpontra, beleértve a bérlő AZONOSÍTÓját. A bérlő AZONOSÍTÓjának lekéréséhez kövesse a [Azure Active Directory bérlői azonosítójának](#get-the-tenant-id-for-your-active-directory)lekérése című szakasz lépéseit:

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

A Batch szolgáltatás erőforrás-végpontjának hivatkozása:

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

A Batch-fiókra való hivatkozás:

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

Adja meg az alkalmazás AZONOSÍTÓját (ügyfél-AZONOSÍTÓját). Az alkalmazás-azonosító elérhető az alkalmazás regisztrációjában a Azure Portalban:

```csharp
private const string ClientId = "<application-id>";
```

Másolja a megadott átirányítási URI-t is, ha az alkalmazást natív alkalmazásként regisztrálta. A kódban megadott átirányítási URI-nek meg kell egyeznie az alkalmazás regisztrálásakor megadott átirányítási URI-azonosítóval:

```csharp
private const string RedirectUri = "http://mybatchdatasample";
```

A hitelesítési jogkivonat Azure AD-ből való beszerzéséhez írjon be egy visszahívási módszert. Az itt látható **GetAuthenticationTokenAsync** callback metódus meghívja a ADAL-t az alkalmazással kommunikáló felhasználó hitelesítéséhez. A ADAL által biztosított **AcquireTokenAsync** metódus felszólítja a felhasználót a hitelesítő adataik megadására, és az alkalmazás a felhasználó által biztosítottak szerint folytatódik (kivéve, ha már gyorsítótárazott hitelesítő adatokkal rendelkezik):

```csharp
public static async Task<string> GetAuthenticationTokenAsync()
{
    var authContext = new AuthenticationContext(AuthorityUri);

    // Acquire the authentication token from Azure AD.
    var authResult = await authContext.AcquireTokenAsync(BatchResourceUri, 
                                                        ClientId, 
                                                        new Uri(RedirectUri), 
                                                        new PlatformParameters(PromptBehavior.Auto));

    return authResult.AccessToken;
}
```

Állítson össze egy **BatchTokenCredentials** objektumot, amely a delegált paraméterként veszi át. A hitelesítő adatok használatával nyisson meg egy **BatchClient** objektumot. Ezt a **BatchClient** -objektumot használhatja a következő műveletekhez a Batch szolgáltatásban:

```csharp
public static async Task PerformBatchOperations()
{
    Func<Task<string>> tokenProvider = () => GetAuthenticationTokenAsync();

    using (var client = await BatchClient.OpenAsync(new BatchTokenCredentials(BatchAccountUrl, tokenProvider)))
    {
        await client.JobOperations.ListJobs().ToListAsync();
    }
}
```

### <a name="code-example-using-an-azure-ad-service-principal-with-batch-net"></a>Kód példa: Azure AD egyszerű szolgáltatás használata a Batch .NET-tel

Ha egy egyszerű szolgáltatásnév használatával szeretne hitelesítést végezni a Batch .NET-ben, hivatkozzon a [Azure Batch .net](https://www.nuget.org/packages/Azure.Batch/) -csomagra és a [ADAL](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) -csomagra.

Adja meg a `using` következő utasításokat a kódban:

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Hivatkozzon a kódban található Azure AD-végpontra, beleértve a bérlő AZONOSÍTÓját. Egyszerű szolgáltatásnév használatakor meg kell adnia egy bérlői specifikus végpontot. A bérlő AZONOSÍTÓjának lekéréséhez kövesse a [Azure Active Directory bérlői azonosítójának](#get-the-tenant-id-for-your-active-directory)lekérése című szakasz lépéseit:

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

A Batch szolgáltatás erőforrás-végpontjának hivatkozása:  

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

A Batch-fiókra való hivatkozás:

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

Adja meg az alkalmazás AZONOSÍTÓját (ügyfél-AZONOSÍTÓját). Az alkalmazás-azonosító elérhető az alkalmazás regisztrációjában a Azure Portalban:

```csharp
private const string ClientId = "<application-id>";
```

A Azure Portalból másolt titkos kulcs meghatározása:

```csharp
private const string ClientKey = "<secret-key>";
```

A hitelesítési jogkivonat Azure AD-ből való beszerzéséhez írjon be egy visszahívási módszert. Az itt látható **GetAuthenticationTokenAsync** visszahívási módszer a felügyelet nélküli hitelesítéshez ADAL hív meg:

```csharp
public static async Task<string> GetAuthenticationTokenAsync()
{
    AuthenticationContext authContext = new AuthenticationContext(AuthorityUri);
    AuthenticationResult authResult = await authContext.AcquireTokenAsync(BatchResourceUri, new ClientCredential(ClientId, ClientKey));

    return authResult.AccessToken;
}
```

Állítson össze egy **BatchTokenCredentials** objektumot, amely a delegált paraméterként veszi át. A hitelesítő adatok használatával nyisson meg egy **BatchClient** objektumot. Ezután használja a **BatchClient** objektumot a következő műveletekhez a Batch szolgáltatáshoz:

```csharp
public static async Task PerformBatchOperations()
{
    Func<Task<string>> tokenProvider = () => GetAuthenticationTokenAsync();

    using (var client = await BatchClient.OpenAsync(new BatchTokenCredentials(BatchAccountUrl, tokenProvider)))
    {
        await client.JobOperations.ListJobs().ToListAsync();
    }
}
```
### <a name="code-example-using-an-azure-ad-service-principal-with-batch-python"></a>Kód példa: Azure AD egyszerű szolgáltatás használata a Batch Python használatával

Ha egy egyszerű szolgáltatásnevet szeretne hitelesíteni a Batch Pythonból, telepítse és hivatkozzon az [Azure-batch](https://pypi.org/project/azure-batch/) és az [Azure-Common](https://pypi.org/project/azure-common/) modulokra.


```python
from azure.batch import BatchServiceClient
from azure.common.credentials import ServicePrincipalCredentials
```

Egyszerű szolgáltatásnév használatakor meg kell adnia a bérlő AZONOSÍTÓját. A bérlő AZONOSÍTÓjának lekéréséhez kövesse a [Azure Active Directory bérlői azonosítójának](#get-the-tenant-id-for-your-active-directory)lekérése című szakasz lépéseit:

```python
TENANT_ID = "<tenant-id>"
```

A Batch szolgáltatás erőforrás-végpontjának hivatkozása:  

```python
RESOURCE = "https://batch.core.windows.net/"
```

A Batch-fiókra való hivatkozás:

```python
BATCH_ACCOUNT_URL = "https://myaccount.mylocation.batch.azure.com"
```

Adja meg az alkalmazás AZONOSÍTÓját (ügyfél-AZONOSÍTÓját). Az alkalmazás-azonosító elérhető az alkalmazás regisztrációjában a Azure Portalban:

```python
CLIENT_ID = "<application-id>"
```

A Azure Portalból másolt titkos kulcs meghatározása:

```python
SECRET = "<secret-key>"
```

Hozzon létre egy **ServicePrincipalCredentials** objektumot:

```python
credentials = ServicePrincipalCredentials(
    client_id=CLIENT_ID,
    secret=SECRET,
    tenant=TENANT_ID,
    resource=RESOURCE
)
```

A **BatchServiceClient** objektum megnyitásához használja az egyszerű szolgáltatás hitelesítő adatait. Ezt követően használja ezt a **BatchServiceClient** objektumot a Batch szolgáltatással kapcsolatos későbbi műveletekhez.

```python
    batch_client = BatchServiceClient(
    credentials,
    base_url=BATCH_ACCOUNT_URL
)
```

## <a name="next-steps"></a>További lépések

* Az Azure AD-vel kapcsolatos további tudnivalókért tekintse meg a [Azure Active Directory dokumentációját](https://docs.microsoft.com/azure/active-directory/). A ADAL használatát bemutató részletes példák az [Azure Code Samples](https://azure.microsoft.com/resources/samples/?service=active-directory) Library-ben érhetők el.

* Az egyszerű szolgáltatásokkal kapcsolatos további tudnivalókért tekintse meg [az alkalmazás-és szolgáltatásnév objektumait Azure Active Directoryban](../active-directory/develop/app-objects-and-service-principals.md). Ha a Azure Portal használatával szeretne szolgáltatásnevet létrehozni, tekintse meg az [erőforrásokhoz hozzáférő Active Directory alkalmazás és egyszerű szolgáltatás létrehozása a portál használatával](../active-directory/develop/howto-create-service-principal-portal.md)című témakört. A PowerShell vagy az Azure CLI használatával is létrehozhat egy egyszerű szolgáltatást.

* A Batch-felügyeleti alkalmazások Azure AD-vel történő hitelesítéséhez tekintse meg [a Batch-felügyeleti megoldások Active Directory](batch-aad-auth-management.md)használatával történő hitelesítését ismertető témakört.

* Az Azure AD-jogkivonattal hitelesített batch-ügyfelek létrehozásával kapcsolatban lásd: [Azure batch egyéni rendszerkép üzembe helyezése Python](https://github.com/azurebigcompute/recipes/blob/master/Azure%20Batch/CustomImages/CustomImagePython.md) -parancsfájlos minta használatával.

[aad_about]:../active-directory/fundamentals/active-directory-whatis.md "Mi az Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Hitelesítési forgatókönyvek az Azure AD-hez"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Alkalmazások integrálása a Azure Active Directory"
[azure_portal]: https://portal.azure.com
