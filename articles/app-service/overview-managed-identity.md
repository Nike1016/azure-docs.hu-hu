---
title: Felügyelt identitások áttekintése – Azure App Service | Microsoft Docs
description: Koncepcionális útmutató és telepítési útmutató a felügyelt identitásokhoz Azure App Service és Azure Functions
services: app-service
author: mattchenderson
manager: cfowler
editor: ''
ms.service: app-service
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 08/15/2019
ms.author: mahender
ms.reviewer: yevbronsh
ms.openlocfilehash: a2b8a4e496094c6275710328e70a09376ce0e5fc
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69563025"
---
# <a name="how-to-use-managed-identities-for-app-service-and-azure-functions"></a>Felügyelt identitások használata App Service és Azure Functions

> [!Important] 
> A App Service és Azure Functions felügyelt identitásai nem a várt módon fognak működni, ha az alkalmazást áttelepítik az előfizetések/bérlők között. Az alkalmazásnak új identitást kell beszereznie, amely a funkció letiltásával és újbóli engedélyezésével végezhető el. Lásd [az](#remove) alábbi identitások eltávolítását ismertető szakaszt. Az alárendelt erőforrásoknak is szükségük lesz a hozzáférési szabályzatok frissítésére az új identitás használatához.

Ebből a témakörből megtudhatja, hogyan hozhat létre felügyelt identitást App Service és Azure Functions alkalmazásokhoz, és hogyan használhatja azt más erőforrásokhoz való hozzáféréshez. A Azure Active Directory felügyelt identitása lehetővé teszi, hogy az alkalmazás könnyedén hozzáférhessen más HRE-védelemmel ellátott erőforrásokhoz, például a Azure Key Vaulthoz. Az identitást az Azure platform felügyeli, és nem igényli semmilyen titok kiépítését és elforgatását. További információ a HRE felügyelt identitásokról: [felügyelt identitások az Azure](../active-directory/managed-identities-azure-resources/overview.md)-erőforrásokhoz.

Az alkalmazás két típusú identitást biztosíthat: 
- A **rendszer által hozzárendelt identitás** az alkalmazáshoz van kötve, és törlődik, ha az alkalmazás törölve lett. Egy alkalmazásnak csak egy rendszerhez rendelt identitása lehet.
- A **felhasználó által hozzárendelt identitás** egy önálló Azure-erőforrás, amelyet hozzá lehet rendelni az alkalmazáshoz. Egy alkalmazáshoz több felhasználó által hozzárendelt identitás is tartozhat.

## <a name="adding-a-system-assigned-identity"></a>Rendszerhez rendelt identitás hozzáadása

A rendszer által hozzárendelt identitással rendelkező alkalmazások létrehozásához további tulajdonságot kell beállítani az alkalmazásban.

### <a name="using-the-azure-portal"></a>Az Azure Portal használata

Ha felügyelt identitást szeretne beállítani a portálon, először hozzon létre egy alkalmazást a megszokott módon, majd engedélyezze a szolgáltatást.

1. A szokásos módon hozzon létre egy alkalmazást a portálon. Navigáljon a portálon.

2. Ha Function alkalmazást használ, navigáljon a **platform szolgáltatásaihoz**. Más típusú alkalmazások esetén görgessen le a **Beállítások** csoportba a bal oldali navigációs sávon.

3. Válassza a **felügyelt identitás**elemet.

4. A **rendszerhez rendelt** lapon váltson az **állapot** bekapcsolva értékre. Kattintson a **Save** (Mentés) gombra.

![Felügyelt identitás a App Serviceban](media/app-service-managed-service-identity/msi-blade-system.png)

### <a name="using-the-azure-cli"></a>Az Azure parancssori felületének használata

Felügyelt identitás Azure CLI használatával történő beállításához a `az webapp identity assign` parancsot egy meglévő alkalmazáshoz kell használnia. Ebben a szakaszban három lehetőség van a példák futtatására:

- Használja [Azure Cloud Shell](../cloud-shell/overview.md) a Azure Portal.
- A beágyazott Azure Cloud Shell az alábbi, az egyes kódrészletek jobb felső sarkában található "kipróbálás" gomb segítségével érheti el.
- [Az Azure CLI legújabb verziójának telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.31 vagy újabb) Ha inkább egy helyi CLI-konzolt szeretne használni. 

A következő lépések végigvezetik a webalkalmazások létrehozásán és az identitás a CLI használatával történő hozzárendelésén.

1. Ha az Azure CLI-t helyi konzolban használja, akkor először az [az login](/cli/azure/reference-index#az-login) paranccsal jelentkezzen be az Azure-ba. Olyan fiókot használjon, amely ahhoz az Azure-előfizetéshez van társítva, amelyben az alkalmazást üzembe szeretné helyezni:

    ```azurecli-interactive
    az login
    ```
2. Hozzon létre egy webalkalmazást a parancssori felület használatával. A CLI és a App Service használatával kapcsolatos további Példákért lásd: [app Service CLI-minták](../app-service/samples-cli.md):

    ```azurecli-interactive
    az group create --name myResourceGroup --location westus
    az appservice plan create --name myPlan --resource-group myResourceGroup --sku S1
    az webapp create --name myApp --resource-group myResourceGroup --plan myPlan
    ```

3. Futtassa a `identity assign` parancsot az alkalmazás identitásának létrehozásához:

    ```azurecli-interactive
    az webapp identity assign --name myApp --resource-group myResourceGroup
    ```

### <a name="using-azure-powershell"></a>Az Azure PowerShell használata

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A következő lépések végigvezetik a webalkalmazások létrehozásán és az identitás hozzárendelésének Azure PowerShell használatával:

1. Szükség esetén telepítse az Azure PowerShellt az [Azure PowerShell útmutatójának](/powershell/azure/overview) utasításait követve, majd a `Login-AzAccount` futtatásával hozza létre a kapcsolatot az Azure-ral.

2. Webalkalmazás létrehozása Azure PowerShell használatával. A Azure PowerShell és a App Service használatával kapcsolatos további példákért tekintse meg az [app Service PowerShell-mintákat](../app-service/samples-powershell.md):

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name myResourceGroup -Location $location
    
    # Create an App Service plan in Free tier.
    New-AzAppServicePlan -Name $webappname -Location $location -ResourceGroupName myResourceGroup -Tier Free
    
    # Create a web app.
    New-AzWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName myResourceGroup
    ```

3. Futtassa a `Set-AzWebApp -AssignIdentity` parancsot az alkalmazás identitásának létrehozásához:

    ```azurepowershell-interactive
    Set-AzWebApp -AssignIdentity $true -Name $webappname -ResourceGroupName myResourceGroup 
    ```

### <a name="using-an-azure-resource-manager-template"></a>Azure Resource Manager sablon használata

Az Azure-erőforrások üzembe helyezésének automatizálásához Azure Resource Manager sablon használható. Ha többet szeretne megtudni a App Service és a függvények üzembe helyezéséről, olvassa el az [erőforrás-telepítés automatizálása app Service](../app-service/deploy-complex-application-predictably.md) és az [erőforrás-telepítés automatizálása a Azure functions-ben](../azure-functions/functions-infrastructure-as-code.md)című témakört.

Bármely típusú `Microsoft.Web/sites` erőforrás létrehozható identitással, az erőforrás-definícióban a következő tulajdonsággal együtt:
```json
"identity": {
    "type": "SystemAssigned"
}    
```

> [!NOTE] 
> Egy alkalmazáshoz a rendszerhez hozzárendelt és felhasználó által hozzárendelt identitások is tartozhatnak egyszerre. Ebben az esetben a tulajdonság `type` a következő:`SystemAssigned,UserAssigned`

A rendszer által hozzárendelt típus hozzáadásával az Azure létrehozza és kezeli az alkalmazás identitását.

Egy webalkalmazás például a következőhöz hasonló lehet:
```json
{
    "apiVersion": "2016-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('appName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "SystemAssigned"
    },
    "properties": {
        "name": "[variables('appName')]",
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "hostingEnvironment": "",
        "clientAffinityEnabled": false,
        "alwaysOn": true
    },
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
    ]
}
```

A hely létrehozásakor a következő tulajdonságokkal rendelkezik:
```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

A `<TENANTID>` és`<PRINCIPALID>` a helyettesítése GUID azonosítókkal. A tenantId tulajdonság azt határozza meg, hogy az identitás melyik HRE-bérlőhöz tartozik. A principalId az alkalmazás új identitásának egyedi azonosítója. A HRE belül az egyszerű szolgáltatásnév neve megegyezik a App Service vagy Azure Functions példányával.


## <a name="adding-a-user-assigned-identity"></a>Felhasználó által hozzárendelt identitás hozzáadása

A felhasználó által hozzárendelt identitással rendelkező alkalmazások létrehozásához létre kell hoznia az identitást, majd hozzá kell adnia annak erőforrás-azonosítóját az alkalmazás-konfigurációhoz.

### <a name="using-the-azure-portal"></a>Az Azure Portal használata

Először létre kell hoznia egy felhasználó által hozzárendelt identitás-erőforrást.

1. Hozzon létre egy felhasználó által hozzárendelt felügyelt identitási erőforrást az [utasításoknak](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)megfelelően.

2. A szokásos módon hozzon létre egy alkalmazást a portálon. Navigáljon a portálon.

3. Ha Function alkalmazást használ, navigáljon a **platform szolgáltatásaihoz**. Más típusú alkalmazások esetén görgessen le a **Beállítások** csoportba a bal oldali navigációs sávon.

4. Válassza a **felügyelt identitás**elemet.

5. A **felhasználó** által hozzárendelt lapon kattintson a **Hozzáadás**gombra.

6. Keresse meg a korábban létrehozott identitást, és válassza ki. Kattintson a **Hozzáadás**lehetőségre.

![Felügyelt identitás a App Serviceban](media/app-service-managed-service-identity/msi-blade-user.png)

### <a name="using-an-azure-resource-manager-template"></a>Azure Resource Manager sablon használata

Az Azure-erőforrások üzembe helyezésének automatizálásához Azure Resource Manager sablon használható. Ha többet szeretne megtudni a App Service és a függvények üzembe helyezéséről, olvassa el az [erőforrás-telepítés automatizálása app Service](../app-service/deploy-complex-application-predictably.md) és az [erőforrás-telepítés automatizálása a Azure functions-ben](../azure-functions/functions-infrastructure-as-code.md)című témakört.

Bármely típusú `Microsoft.Web/sites` erőforrás létrehozható identitással, az erőforrás-definíció következő blokkjának beírásával, a kívánt `<RESOURCEID>` identitás erőforrás-azonosítójával lecserélve:
```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {}
    }
}    
```

> [!NOTE] 
> Egy alkalmazáshoz a rendszerhez hozzárendelt és felhasználó által hozzárendelt identitások is tartozhatnak egyszerre. Ebben az esetben a tulajdonság `type` a következő:`SystemAssigned,UserAssigned`

A felhasználó által hozzárendelt típus hozzáadása és az Azure megjelölése az alkalmazás identitásának létrehozásához és kezeléséhez.

Egy webalkalmazás például a következőhöz hasonló lehet:
```json
{
    "apiVersion": "2016-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('appName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]": {}
        }
    },
    "properties": {
        "name": "[variables('appName')]",
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "hostingEnvironment": "",
        "clientAffinityEnabled": false,
        "alwaysOn": true
    },
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]"
    ]
}
```

A hely létrehozásakor a következő tulajdonságokkal rendelkezik:
```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {
            "principalId": "<PRINCIPALID>",
            "clientId": "<CLIENTID>"
        }
    }
}
```

A `<PRINCIPALID>` és`<CLIENTID>` a helyettesítése GUID azonosítókkal. A principalId a HRE felügyeletéhez használt identitás egyedi azonosítója. A clientId az alkalmazás új identitásának egyedi azonosítója, amely a futásidejű hívásokban használandó identitás megadására szolgál.


## <a name="obtaining-tokens-for-azure-resources"></a>Jogkivonatok beszerzése az Azure-erőforrásokhoz

Az alkalmazás az identitásával lekérheti a jogkivonatokat a HRE által védett más erőforrásokhoz, például a Azure Key Vaulthoz. Ezek a tokenek az erőforráshoz hozzáférő alkalmazást, és nem az alkalmazás adott felhasználóját jelölik. 

> [!IMPORTANT]
> Előfordulhat, hogy a cél erőforrást úgy kell konfigurálnia, hogy engedélyezze az alkalmazáshoz való hozzáférést. Ha például jogkivonatot kér Key Vault, meg kell győződnie arról, hogy az alkalmazás identitását tartalmazó hozzáférési szabályzatot adott hozzá. Ellenkező esetben a rendszer elutasítja a Key Vault meghívásait, még akkor is, ha azok tartalmazzák a jogkivonatot. Ha többet szeretne megtudni arról, hogy mely erőforrások támogatják Azure Active Directory jogkivonatokat, tekintse meg az Azure [ad-hitelesítést támogató Azure-szolgáltatásokat](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

Létezik egy egyszerű REST-protokoll a jogkivonat beszerzéséhez App Service és Azure Functions. A .NET-alkalmazások esetében a Microsoft. Azure. Services. AppAuthentication könyvtár egy absztrakciót biztosít a protokollon keresztül, és támogatja a helyi fejlesztési élményt.

### <a name="asal"></a>A Microsoft. Azure. Services. AppAuthentication kódtár használata a .NET-hez

A .NET-alkalmazások és-függvények esetében a felügyelt identitással való munka legegyszerűbb módja a Microsoft. Azure. Services. AppAuthentication csomag. Ez a kódtár lehetővé teszi a kód helyi tesztelését a fejlesztői gépen, a Visual studióból, az [Azure CLI](/cli/azure)-ből vagy Active Directory integrált hitelesítésből származó felhasználói fiók használatával. A könyvtár helyi fejlesztési lehetőségeiről további információt a [Microsoft.Azure.Services.AppAuthentication reference]talál. Ez a szakasz bemutatja, hogyan kezdheti meg a kódtárat a kódban.

1. Adjon hozzá hivatkozásokat a [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) és minden más szükséges NuGet-csomaghoz az alkalmazásához. Az alábbi példa a [Microsoft. Azure.](https://www.nuget.org/packages/Microsoft.Azure.KeyVault)kulcstartót is használja.

2. Adja hozzá a következő kódot az alkalmazáshoz, amely úgy van módosítva, hogy a megfelelő erőforrást célozza meg. Ez a példa két módszert mutat be a Azure Key Vaultsal való együttműködésre:

```csharp
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.KeyVault;
// ...
var azureServiceTokenProvider = new AzureServiceTokenProvider();
string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://vault.azure.net");
// OR
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
```

Ha többet szeretne megtudni a Microsoft. Azure. Services. AppAuthentication és az általa közzétett műveletekről, tekintse meg a [Microsoft.Azure.Services.AppAuthentication reference] , valamint a app Service és a KULCSTARTÓT [MSI .net-minta](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)használatával.


### <a name="using-the-azure-sdk-for-java"></a>A Javához készült Azure SDK használata

A Java-alkalmazások és-függvények esetében a felügyelt identitással való munka legegyszerűbb módja a [Javához készült Azure SDK](https://github.com/Azure/azure-sdk-for-java). Ez a szakasz bemutatja, hogyan kezdheti meg a kódtárat a kódban.

1. Adjon hozzá egy hivatkozást az [Azure SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure)-könyvtárhoz. A Maven-projektek esetében ezt a kódrészletet a projekt `dependencies` Pom-fájljának szakaszába is hozzáadhatja:

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure</artifactId>
    <version>1.23.0</version>
</dependency>
```

2. Használja az `AppServiceMSICredentials` objektumot a hitelesítéshez. Ez a példa azt mutatja be, hogyan használható ez a mechanizmus a Azure Key Vaulthoz való munkavégzéshez:

```java
import com.microsoft.azure.AzureEnvironment;
import com.microsoft.azure.management.Azure;
import com.microsoft.azure.management.keyvault.Vault
//...
Azure azure = Azure.authenticate(new AppServiceMSICredentials(AzureEnvironment.AZURE))
        .withSubscription(subscriptionId);
Vault myKeyVault = azure.vaults().getByResourceGroup(resourceGroup, keyvaultName);

```

### <a name="using-the-rest-protocol"></a>A REST protokoll használata

A felügyelt identitású alkalmazások esetében két környezeti változó van definiálva:

- MSI_ENDPOINT – a helyi jogkivonat-szolgáltatás URL-címe.
- MSI_SECRET – a kiszolgálóoldali kérelmek hamisításának (SSRF) elleni támadásának enyhítésére szolgáló fejléc. Az értéket a platform forgatja el.

A **MSI_ENDPOINT** egy helyi URL-cím, amelyből az alkalmazás jogkivonatokat igényelhet. Egy erőforráshoz tartozó jogkivonat lekéréséhez hajtson végre egy HTTP GET kérelmet erre a végpontra, beleértve a következő paramétereket:

> |Paraméternév|A|Leírás|
> |-----|-----|-----|
> |resource|Lekérdezés|Annak az erőforrásnak az HRE erőforrás-URI azonosítója, amelynek a jogkivonatát meg kell szerezni. Ez lehet az egyik olyan [Azure-szolgáltatás, amely támogatja az Azure ad-hitelesítést](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) vagy bármilyen más erőforrás-URI-t.|
> |api-version|Lekérdezés|A használni kívánt jogkivonat-API verziója. a "2017-09-01" jelenleg az egyetlen támogatott verzió.|
> |secret|Fejléc|A MSI_SECRET környezeti változó értéke. Ez a fejléc a kiszolgálóoldali kérelmek hamisításának (SSRF) elleni támadásának enyhítésére szolgál.|
> |ClientID|Lekérdezés|Választható A használni kívánt felhasználó által hozzárendelt identitás azonosítója. Ha nincs megadva, a rendszer hozzárendelt identitást használja.|

A sikeres 200 OK válasz egy JSON-törzset tartalmaz, amely a következő tulajdonságokkal rendelkezik:

> |Tulajdonság neve|Leírás|
> |-------------|----------|
> |access_token|A kért hozzáférési jogkivonat. A hívó webszolgáltatás ezt a tokent használhatja a fogadó webszolgáltatáshoz való hitelesítéshez.|
> |expires_on|A hozzáférési jogkivonat lejáratának időpontja. A dátum az 1970-01-01T0:0: 0Z UTC számú másodperc, a lejárati időpontig. Ez az érték a gyorsítótárazott tokenek élettartamának meghatározására szolgál.|
> |resource|A fogadó webszolgáltatás alkalmazás-azonosító URI-ja.|
> |token_type|Megadja a jogkivonat típusának értékét. Az Azure AD által támogatott egyetlen típus a tulajdonos. A tulajdonosi jogkivonatokkal kapcsolatos további információkért tekintse [meg a OAuth 2,0 engedélyezési keretrendszert: Tulajdonosi jogkivonat használata (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt).|

Ez a válasz megegyezik a [HRE szolgáltatás-szolgáltatás hozzáférési jogkivonat-kérelemre adott válaszával](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md#service-to-service-access-token-response).

> [!NOTE]
> A környezet változói a folyamat első indításakor jönnek létre, így az alkalmazás felügyelt identitásának engedélyezése után előfordulhat, hogy újra kell indítania az alkalmazást, vagy újra kell telepítenie a `MSI_ENDPOINT` kódját `MSI_SECRET` , mielőtt a kód elérhetővé válik.

### <a name="rest-protocol-examples"></a>REST protokoll – példák

A kérelem példája a következőhöz hasonló lehet:

```
GET /MSI/token?resource=https://vault.azure.net&api-version=2017-09-01 HTTP/1.1
Host: localhost:4141
Secret: 853b9a84-5bfa-4b22-a3f3-0b9a43d9ad8a
```

A minta válasz a következőhöz hasonlóan néz ki:

```
HTTP/1.1 200 OK
Content-Type: application/json

{
    "access_token": "eyJ0eXAi…",
    "expires_on": "09/14/2017 00:00:00 PM +00:00",
    "resource": "https://vault.azure.net",
    "token_type": "Bearer"
}
```

### <a name="code-examples"></a>Példák a kódokra

<a name="token-csharp"></a>A kérelem elvégzése C#a következőben:

```csharp
public static async Task<HttpResponseMessage> GetToken(string resource, string apiversion)  {
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Add("Secret", Environment.GetEnvironmentVariable("MSI_SECRET"));
    return await client.GetAsync(String.Format("{0}/?resource={1}&api-version={2}", Environment.GetEnvironmentVariable("MSI_ENDPOINT"), resource, apiversion));
}
```

> [!TIP]
> A .NET nyelveken a [Microsoft. Azure. Services. AppAuthentication](#asal) is használhatja a kérelem elvégzése helyett.

<a name="token-js"></a>A Node. JS-ben:

```javascript
const rp = require('request-promise');
const getToken = function(resource, apiver, cb) {
    let options = {
        uri: `${process.env["MSI_ENDPOINT"]}/?resource=${resource}&api-version=${apiver}`,
        headers: {
            'Secret': process.env["MSI_SECRET"]
        }
    };
    rp(options)
        .then(cb);
}
```

<a name="token-python"></a>Pythonban:

```python
import os
import requests

msi_endpoint = os.environ["MSI_ENDPOINT"]
msi_secret = os.environ["MSI_SECRET"]

def get_bearer_token(resource_uri, token_api_version):
    token_auth_uri = f"{msi_endpoint}?resource={resource_uri}&api-version={token_api_version}"
    head_msi = {'Secret':msi_secret}

    resp = requests.get(token_auth_uri, headers=head_msi)
    access_token = resp.json()['access_token']

    return access_token
```

<a name="token-powershell"></a>A PowerShellben:

```powershell
$apiVersion = "2017-09-01"
$resourceURI = "https://<AAD-resource-URI-for-resource-to-obtain-token>"
$tokenAuthURI = $env:MSI_ENDPOINT + "?resource=$resourceURI&api-version=$apiVersion"
$tokenResponse = Invoke-RestMethod -Method Get -Headers @{"Secret"="$env:MSI_SECRET"} -Uri $tokenAuthURI
$accessToken = $tokenResponse.access_token
```

## <a name="remove"></a>Identitás eltávolítása

A rendszer által hozzárendelt identitást eltávolíthatja a szolgáltatás a portál, a PowerShell vagy a parancssori felület használatával történő letiltásával ugyanúgy, ahogyan azt létrehozták. A felhasználó által hozzárendelt identitások egyenként eltávolíthatók. Az összes identitás eltávolításához a REST/ARM sablon protokollban ezt úgy teheti meg, hogy a "None" értékre állítja a típust:

```json
"identity": {
    "type": "None"
}
```

A rendszer által hozzárendelt identitások eltávolítása a HRE-ből is törölve lesz. A rendszer által hozzárendelt identitások is automatikusan törlődnek a HRE-ből az alkalmazás-erőforrás törlésekor.

> [!NOTE]
> Létezik egy olyan Alkalmazásbeállítás is, amely beállítható, WEBSITE_DISABLE_MSI, amely egyszerűen letiltja a helyi jogkivonat-szolgáltatást. Azonban elhagyja az identitást, és az eszközök továbbra is a felügyelt identitást "be" vagy "engedélyezve" állapotba helyezik. Ennek eredményeképpen a beállítás használata nem ajánlott.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Biztonságos hozzáférés SQL Database felügyelt identitás használatával](app-service-web-tutorial-connect-msi.md)

[Microsoft.Azure.Services.AppAuthentication reference]: https://go.microsoft.com/fwlink/p/?linkid=862452
