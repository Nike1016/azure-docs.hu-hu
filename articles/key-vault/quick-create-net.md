---
title: 'Gyors útmutató: Azure Key Vault titkos kulcsának beállítása és beolvasása a .NET-webalkalmazások használatával – Azure Key Vault | Microsoft Docs'
description: Ebben a rövid útmutatóban egy .NET-webalkalmazás használatával állít be és kér le titkos kulcsot a Azure Key Vaultból
services: key-vault
author: msmbaldwin
manager: sumedhb
ms.service: key-vault
ms.topic: quickstart
ms.date: 01/02/2019
ms.author: barclayn
ms.custom: mvc
ms.openlocfilehash: 9ddb1db9b39ac942a3476f50aad39c98198b2a18
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68958610"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-by-using-a-net-web-app"></a>Gyors útmutató: Azure Key Vault titkos kulcsának beállítása és beolvasása egy .NET-webalkalmazás használatával

Ebben a rövid útmutatóban követheti az Azure-webalkalmazások beolvasásának lépéseit, amelyekkel az Azure-erőforrások felügyelt identitásai használatával olvashat az Azure Key Vaultról. A Key Vault használata segít megőrizni az adatokat. Az alábbiak végrehajtásának módját ismerheti meg:

* Kulcstartó létrehozása.
* Titkos kulcs tárolása a kulcstartóban.
* Titkos kulcs lekérése a kulcstartóból.
* Azure-webalkalmazás létrehozása.
* [Felügyeltszolgáltatás-identitás](../active-directory/managed-identities-azure-resources/overview.md) engedélyezése a webalkalmazáshoz.
* A szükséges engedélyek megadása a webalkalmazás számára az adatoknak a kulcstartóból való olvasásához.

Mielőtt továbblépne, olvassa el a [Key Vault alapvető fogalmait](key-vault-whatis.md#basic-concepts).

>[!NOTE]
>A Key Vault egy központi adattár a titkos kulcsok programozott módon való tárolásához. A használatához azonban az alkalmazásoknak és a felhasználóknak először hitelesíteniük kell magukat a Key Vaultban, azaz be kell mutatniuk egy titkos kulcsot. Az ajánlott biztonsági eljárásoknak megfelelően ezt az első titkot rendszeresen el kell forgatni. 
>
>Az Azure- [erőforrások felügyelt szolgáltatásbeli identitásával](../active-directory/managed-identities-azure-resources/overview.md)az Azure-ban futó alkalmazások az Azure által automatikusan felügyelt identitást kapnak. Ez segít megoldani a *titkos kulcsok bemutatásának problémáját*, így a felhasználók és az alkalmazások az ajánlott eljárásokat követhetik, és nem kell aggódniuk az első titkos kulcs leváltása miatt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Előfeltételek

* Windows rendszeren:
  * [Visual Studio 2019](https://www.microsoft.com/net/download/windows) a következő számítási feladatokkal:
    * ASP.NET és webfejlesztés
    * .NET Core platformfüggetlen fejlesztés
  * [.NET Core 2.1 SDK vagy újabb](https://www.microsoft.com/net/download/windows)

* Mac gépen:
  * Lásd a [Visual Studio for Mac újdonságait](https://visualstudio.microsoft.com/vs/mac/) bemutató cikket.

* Összes platform:
  * Git ([letöltés](https://git-scm.com/downloads)).
  * Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.
  * Az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 2.0.4-es vagy újabb verziója. Ez elérhető Windows, Mac és Linux rendszerekhez.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Ha az Azure-ba az Azure CLI használatával szeretne bejelentkezni, írja be a következőt:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az-group-create) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Válasszon egy erőforráscsoport-nevet, és töltse ki a helyőrzőt.
A következő példa létrehoz egy erőforráscsoportot az USA keleti régiójában:

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "East US"
```

A cikkben végig az imént létrehozott erőforráscsoportot használjuk majd.

## <a name="create-a-key-vault"></a>Kulcstartó létrehozása

A következő lépésben létre fogunk hozni egy kulcstartót az előző lépésben létrehozott erőforráscsoportban. Adja meg az alábbi információkat:

* Key Vault neve: A névnek 3-24 karakterből álló sztringnek kell lennie, és csak 0-9, a-z, A-Z és egy kötőjelet (-) tartalmazhat.
* Az erőforráscsoport neve.
* Hely: **USA keleti**régiója.

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "East US"
```

Jelenleg az Ön Azure-fiókja az egyetlen fiók, amelyik jogosult műveleteket végrehajtani ezen az új tárolón.

## <a name="add-a-secret-to-the-key-vault"></a>Titkos kulcs hozzáadása a kulcstartóhoz

Egy titkos kulcs hozzáadásával mutatjuk be ennek működését. Tárolhat SQL-kapcsolati sztringeket vagy bármely olyan adatot, amelyet biztonságosan kell tárolni, azonban az alkalmazás számára elérhetővé kell tenni.

Írja be az alábbi parancsokat, amelyek egy titkos kulcsot hoznak létre az **AppSecret** nevű kulcstartóban. A titkos kulcs értéke **MySecret** lesz.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

A titkos kódban tárolt érték megtekintése egyszerű szövegként:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

Ez a parancs megjeleníti a titkos információkat, beleértve az URI-t is. A fenti lépések végrehajtása után rendelkeznie kell egy, a kulcstartóban tárolt titkos kulcshoz tartozó URI-val. Jegyezze fel ezt az információt. Egy későbbi lépésben szüksége lesz rá.

## <a name="clone-the-repo"></a>Az adattár klónozása

A forrás szerkesztéséhez szükséges helyi másolat létrehozásához klónozza az adattárat. Futtassa a következő parancsot:

```
git clone https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart.git
```

## <a name="open-and-edit-the-solution"></a>Nyissa meg és szerkessze a megoldást

Módosítsa a program.cs fájlt, hogy a minta az adott kulcstartó nevével fusson:

1. Lépjen a key-vault-dotnet-core-quickstart mappára.
2. Nyissa meg a Key-Vault-DotNet-Core-Quickstart. SLN fájlt a Visual Studio 2019-ben.
3. Nyissa meg a Program.cs fájlt, és frissítse a helyőrző *YourKeyVaultName* a korábban létrehozott kulcstartó nevével.

Ez a megoldás [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) és [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) NuGet-kódtárakat használ.

## <a name="run-the-app"></a>Az alkalmazás futtatása

A Visual Studio 2019 főmenüjében válassza a **hibakeresés** > **indítása hibakeresés nélkül**lehetőséget. Amikor megjelenik a böngésző, lépjen az **About** (Névjegy) oldalra. Megjelenik az **AppSecret** értéke.

## <a name="publish-the-web-application-to-azure"></a>A webalkalmazás közzététele az Azure-ban

Tegye közzé ezt az alkalmazást az Azure-ban, hogy élő webalkalmazásként megtekinthető legyen, illetve hogy látható legyen, hogy a titkos érték lekérhető-e:

1. A Visual Studióban válassza a **key-vault-dotnet-core-quickstart** projektet.
2. Válassza a **Publish** > **Start** (Közzététel > Indítás) lehetőséget.
3. Hozzon létre egy új **App Service-t**, majd kattintson a **Publish** (Közzététel) parancsra.
4. Módosítsa az alkalmazás nevét a következőre: **keyvaultdotnetcorequickstart**.
5. Kattintson a **Létrehozás** gombra.

>[!VIDEO https://sec.ch9.ms/ch9/e93d/a6ac417f-2e63-4125-a37a-8f34bf0fe93d/KeyVault_high.mp4]

## <a name="enable-a-managed-identity-for-the-web-app"></a>Felügyelt identitás engedélyezése a webalkalmazáshoz

Az Azure Key Vault módot kínál a hitelesítő adatok, valamint egyéb kulcsok és titkos kódok biztonságos tárolására, azonban a kódnak hitelesítenie kell magát a Key Vaultban az adatok lekéréséhez. [Az Azure-erőforrások felügyelt identitásainak áttekintése](../active-directory/managed-identities-azure-resources/overview.md) leegyszerűsíti ezt a problémát, mivel az Azure-szolgáltatások számára egy automatikusan felügyelt identitást biztosít az Azure Active Directoryban (Azure AD-ben). Ezzel az identitással bármely, az Azure AD-hitelesítést támogató szolgáltatásban, többek között a Key Vaultban is elvégezheti a hitelesítést anélkül, hogy a hitelesítő adatokat a kódban kellene tárolnia.

Az Azure CLI-ben futtassa a assign-Identity parancsot az alkalmazás identitásának létrehozásához:

   ```azurecli
   az webapp identity assign --name "keyvaultdotnetcorequickstart" --resource-group "<YourResourceGroupName>"
   ```

>[!NOTE]
>Ez a parancs ebben az eljárásban egyenértékű azzal, mintha megnyitná a portált, és a webalkalmazás tulajdonságai között átállítaná az **Identitás/Rendszer által hozzárendelt** beállítást **Be** értékűre.

## <a name="assign-permissions-to-your-application-to-read-secrets-from-key-vault"></a>Engedélyek kiosztása az alkalmazásnak a Key Vault titkos kulcsainak olvasásához

Jegyezze fel a kimenetből származó adatokat, amikor az Azure-ban közzéteszi az alkalmazást. Az adatok a következő formátumban jelennek meg:
        
        {
          "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "SystemAssigned"
        }
        
Ezután futtassa ezt a parancsot a kulcstartó neve és a **PrincipalId** értéke használatával:

```azurecli

az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get list

```

Az alkalmazás futtatásakor meg kell jelennie a titkos kulcs lekért értékének. Az előző parancsban megadja az App Service engedélyeit a Key Vault lekérési és listázási műveleteihez.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs szüksége rájuk, törölje az erőforráscsoportot, a virtuális gépet és az összes kapcsolódó erőforrást. Ehhez válassza ki a kulcstartó erőforráscsoportot, és válassza a **Törlés**lehetőséget.

Törölje a Key vaultot az az kulcstartó [delete](https://docs.microsoft.com/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-delete) paranccsal:

```azurecli
az keyvault delete --name
                   [--resource-group]
                   [--subscription]
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További információ a Key Vault](key-vault-whatis.md)
