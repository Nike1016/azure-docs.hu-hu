---
title: Biztonságos SQL Database-hozzáférés felügyelt identitással – Azure App Service | Microsoft Docs
description: Ismerje meg, hogyan teheti biztonságossá az adatbázis-kapcsolatot egy felügyelt identitás használatával, és hogyan alkalmazhatja azt más Azure-szolgáltatásokra is.
services: app-service\web
documentationcenter: dotnet
author: cephalin
manager: syntaxc4
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 08/06/2019
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 2cf5e0f6da52670d383a1d1508dc7bcc7847831f
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68824548"
---
# <a name="tutorial-secure-azure-sql-database-connection-from-app-service-using-a-managed-identity"></a>Oktatóanyag: Biztonságos Azure SQL Database-kapcsolatok a App Service felügyelt identitás használatával

Az [App Service](overview.md) egy hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatás az Azure-ban. [Felügyelt identitást](overview-managed-identity.md) biztosít az alkalmazásához, vagyis egy kulcsrakész megoldást, amely biztosítja az [Azure SQL Database-hez](/azure/sql-database/) és egyéb Azure-szolgáltatásokhoz való hozzáférés védelmét. Az App Service-ben található felügyelt identitások biztonságosabbá teszik alkalmazását a titkos kódok, pl. a kapcsolati sztringekben lévő hitelesítő adatok szükségességének megszüntetésével. Ebben az oktatóanyagban a felügyelt identitást a következő oktatóanyagok egyikében létrehozott minta webalkalmazáshoz fogja hozzáadni: 

- [Oktatóanyag: ASP.NET-alkalmazás létrehozása az Azure-ban SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)
- [Oktatóanyag: ASP.NET Core és SQL Database alkalmazás létrehozása a Azure App Service](app-service-web-tutorial-dotnetcore-sqldb.md)

Ha ezzel végzett, a mintaalkalmazása biztonságosan csatlakozhat az SQL Database-hez, felhasználónév és jelszó használata nélkül.

> [!NOTE]
> Az oktatóanyagban szereplő lépések a következő verziókat támogatják:
> 
> - .NET-keretrendszer 4.7.2 és újabb verziók.
> - .NET Core 2,2 és újabb verziók.
>

A következőket fogja megtanulni:

> [!div class="checklist"]
> * Felügyelt identitások engedélyezése
> * SQL Database-hozzáférés engedélyezése a felügyelt identitáshoz
> * Entity Framework konfigurálása az Azure AD-hitelesítés használatára SQL Database
> * Kapcsolódás SQL Database a Visual studióból az Azure AD-hitelesítés használatával

> [!NOTE]
>Az Azure AD- hitelesítés különbözik a helyszíni Active Directory (AD DS) [integrált Windows](/previous-versions/windows/it-pro/windows-server-2003/cc758557(v=ws.10)) -hitelesítéstől. AD DS és az Azure AD teljesen különböző hitelesítési protokollokat használ. További információkért lásd: [az Azure AD Domain Services – dokumentáció](https://docs.microsoft.com/azure/active-directory-domain-services/).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk az oktatóanyagban való [abbahagyás helyétől folytatódik. ASP.NET-alkalmazás létrehozása az Azure-ban](app-service-web-tutorial-dotnet-sqldatabase.md) SQL Database [vagy oktatóanyaggal: Hozzon létre egy ASP.NET Core és SQL Database alkalmazást](app-service-web-tutorial-dotnetcore-sqldb.md)a Azure app Service. Ha még nem tette meg, kövesse az első két oktatóanyag egyikét. Azt is megteheti, hogy a saját .NET-alkalmazásának lépéseit a SQL Database használatával is módosíthatja.

Ha a háttérrendszer használatával szeretne hibakeresést végezni az alkalmazásban SQL Database, győződjön meg arról, hogy engedélyezte az ügyfélkapcsolatot a számítógépről. Ha nem, adja hozzá az ügyfél IP-címét a [kiszolgálói szintű IP-tűzfalszabályok kezelése a Azure Portal használatával](../sql-database/sql-database-firewall-configure.md#manage-server-level-ip-firewall-rules-using-the-azure-portal)című témakör lépéseit követve.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="grant-azure-ad-user-access-to-database"></a>Az Azure AD felhasználói hozzáférésének biztosítása az adatbázishoz

Először engedélyezze az Azure AD-hitelesítés SQL Database egy Azure AD-felhasználó kiosztásával a SQL Database-kiszolgáló Active Directory rendszergazdájaként. Ez a felhasználó különbözik az Azure-előfizetéshez való regisztrációhoz használt Microsoft-fióktól. Az Azure AD-ben létrehozott, importált, szinkronizált vagy meghívott felhasználónak kell lennie. További információ az engedélyezett Azure AD-felhasználókról: [Azure ad-szolgáltatások és-korlátozások a SQL Database](../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations). 

Keresse meg az Azure ad-felhasználó objektum-azonosítóját [`az ad user list`](/cli/azure/ad/user?view=azure-cli-latest#az-ad-user-list) a (z) és a Replace  *\<User-principal-name >* használatával. Az eredmény egy változóba lesz mentve.

```azurecli-interactive
azureaduser=$(az ad user list --filter "userPrincipalName eq '<user-principal-name>'" --query [].objectId --output tsv)
```
> [!TIP]
> Az Azure AD összes egyszerű felhasználóneve listájának megtekintéséhez futtassa a parancsot `az ad user list --query [].userPrincipalName`.
>

Adja hozzá ezt az Azure ad-felhasználót Active Directory rendszergazdaként a Cloud Shell parancs használatával [`az sql server ad-admin create`](/cli/azure/sql/server/ad-admin?view=azure-cli-latest#az-sql-server-ad-admin-create) . A következő parancsban cserélje le  *\<a Server-Name >* .

```azurecli-interactive
az sql server ad-admin create --resource-group myResourceGroup --server-name <server-name> --display-name ADMIN --object-id $azureaduser
```

Active Directory-rendszergazda hozzáadásával kapcsolatos további információkért lásd: [Azure Active Directory-rendszergazda létesítése a Azure SQL Database-kiszolgálóhoz](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)

## <a name="set-up-visual-studio"></a>A Visual Studio beállítása

A fejlesztés és a hibakeresés a Visual Studióban való engedélyezéséhez adja hozzá az Azure ad-felhasználót > a Visual Studióban a**Fiókbeállítások** kiválasztásával a menüben, majd kattintson a **fiók hozzáadása**lehetőségre.

Az Azure ad-felhasználó Azure-szolgáltatásbeli hitelesítéshez való beállításához válassza az **eszközök** > **lehetőséget** a menüben, majd válassza az **Azure szolgáltatás-hitelesítési** > **fiók kiválasztása**lehetőséget. Válassza ki a hozzáadott Azure AD-felhasználót, és kattintson **az OK**gombra.

Most már készen áll az alkalmazás fejlesztésére és hibakeresésére SQL Database a háttérben, az Azure AD-hitelesítés használatával.

## <a name="modify-your-project"></a>A projekt módosítása

A projekthez követett lépések attól függnek, hogy ASP.NET-vagy ASP.NET Core-projektről van-e szó.

- [ASP.NET módosítása](#modify-aspnet)
- [ASP.NET Core módosítása](#modify-aspnet-core)

### <a name="modify-aspnet"></a>ASP.NET módosítása

A Visual Studióban nyissa meg a Package Manager konzolt, és adja hozzá a [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)NuGet-csomagot:

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.3.0
```

A *web. config*fájlban a fájl elejére, és végezze el a következő módosításokat:

- A `<configSections>`alkalmazásban adja hozzá a következő szakasz deklarációját:

    ```xml
    <section name="SqlAuthenticationProviders" type="System.Data.SqlClient.SqlAuthenticationProviderConfigurationSection, System.Data, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" />
    ```

- a záró `</configSections>` címke alatt adja hozzá a következő XML- `<SqlAuthenticationProviders>`kódot.

    ```xml
    <SqlAuthenticationProviders>
      <providers>
        <add name="Active Directory Interactive" type="Microsoft.Azure.Services.AppAuthentication.SqlAppAuthenticationProvider, Microsoft.Azure.Services.AppAuthentication" />
      </providers>
    </SqlAuthenticationProviders>
    ```    

- Keresse meg a nevű `MyDbConnection` kapcsolati karakterláncot, és `"server=tcp:<server-name>.database.windows.net;database=<db-name>;UID=AnyString;Authentication=Active Directory Interactive"` `connectionString` cserélje le az értékét a értékre. Cserélje le  _\<a Server-Name >_ és  _\<az db-Name >_ a kiszolgáló nevére és az adatbázis nevére.

Ez minden dolog, amire szüksége van a SQL Databasehoz való kapcsolódáshoz. A Visual Studióban végzett hibakeresés során a kód a [Visual Studióban](#set-up-visual-studio)beállított Azure ad-felhasználót használja. A SQL Database-kiszolgálót később kell beállítania, hogy engedélyezze a kapcsolódást a App Service alkalmazás felügyelt identitásával.

Írja `Ctrl+F5` be újra az alkalmazás futtatását. A böngészőben megjelenő szifilisz-alkalmazás mostantól közvetlenül az Azure AD-hitelesítés használatával csatlakozik a Azure SQL Databasehoz. Ez a beállítás lehetővé teszi, hogy adatbázis-áttelepítést futtasson a Visual studióból.

### <a name="modify-aspnet-core"></a>ASP.NET Core módosítása

A Visual Studióban nyissa meg a Package Manager konzolt, és adja hozzá a [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)NuGet-csomagot:

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.3.0
```

A [ASP.net Core és SQL Database oktatóanyagban](app-service-web-tutorial-dotnetcore-sqldb.md)nincs használatban a `MyDbConnection` kapcsolatok karakterlánca, mert a helyi fejlesztési környezet egy SQLite-adatbázisfájlt használ, és az Azure éles környezet a app Service származó kapcsolatok karakterláncot használ. Active Directory hitelesítéssel mindkét környezet ugyanazt a kapcsolódási karakterláncot szeretné használni. A *appSettings. JSON*fájlban cserélje le a `MyDbConnection` kapcsolatok karakterláncának értékét a alábbiakra:

```json
"Server=tcp:<server-name>.database.windows.net,1433;Database=<database-name>;"
```

A *Startup.cs*távolítsa el a korábban hozzáadott kód szakaszt:

```csharp
// Use SQL Database if in Azure, otherwise, use SQLite
if (Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT") == "Production")
    services.AddDbContext<MyDatabaseContext>(options =>
            options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));
else
    services.AddDbContext<MyDatabaseContext>(options =>
            options.UseSqlite("Data Source=localdatabase.db"));

// Automatically perform database migration
services.BuildServiceProvider().GetService<MyDatabaseContext>().Database.Migrate();
```

És cserélje le a következő kódra:

```csharp
services.AddDbContext<MyDatabaseContext>(options => {
    options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection"));
});
```

Ezután adja meg a Entity Framework adatbázis-környezetet a SQL Database hozzáférési jogkivonatával. A *Data\MyDatabaseContext.cs*-ben adja hozzá a következő kódot az üres `MyDatabaseContext (DbContextOptions<MyDatabaseContext> options)` konstruktor kapcsos zárójelében:

```csharp
var conn = (System.Data.SqlClient.SqlConnection)Database.GetDbConnection();
conn.AccessToken = (new Microsoft.Azure.Services.AppAuthentication.AzureServiceTokenProvider()).GetAccessTokenAsync("https://database.windows.net/").Result;
```

Ez minden dolog, amire szüksége van a SQL Databasehoz való kapcsolódáshoz. A Visual Studióban végzett hibakeresés során a kód a [Visual Studióban](#set-up-visual-studio)beállított Azure ad-felhasználót használja. A SQL Database-kiszolgálót később kell beállítania, hogy engedélyezze a kapcsolódást a App Service alkalmazás felügyelt identitásával.

Írja `Ctrl+F5` be újra az alkalmazás futtatását. A böngészőben megjelenő szifilisz-alkalmazás mostantól közvetlenül az Azure AD-hitelesítés használatával csatlakozik a Azure SQL Databasehoz. Ez a beállítás lehetővé teszi, hogy adatbázis-áttelepítést futtasson a Visual studióból.

## <a name="use-managed-identity-connectivity"></a>Felügyelt identitású kapcsolat használata

Ezután konfigurálja a App Service alkalmazást úgy, hogy az SQL Databasehoz kapcsolódjon egy rendszerhez rendelt felügyelt identitással.

### <a name="enable-managed-identity-on-app"></a>Felügyelt identitás engedélyezése az alkalmazásban

Ha engedélyezni szeretné a felügyelt identitást az Azure-alkalmazásához, használja az [az webapp identity assign](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) parancsot a Cloud Shellben. A következő parancsban cserélje le  *\<az App-Name >* .

```azurecli-interactive
az webapp identity assign --resource-group myResourceGroup --name <app-name>
```

Íme egy példa a kimenetre:

```json
{
  "additionalProperties": {},
  "principalId": "21dfa71c-9e6f-4d17-9e90-1d28801c9735",
  "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47",
  "type": "SystemAssigned"
}
```

### <a name="add-managed-identity-to-an-azure-ad-group"></a>Felügyelt identitás hozzáadása egy Azure AD-csoporthoz

Ahhoz, hogy ez az identitás hozzáférhessen a SQL Databasehoz, hozzá kell adnia azt egy [Azure ad](../active-directory/fundamentals/active-directory-manage-groups.md)-csoporthoz. A Cloud Shell vegye fel azt egy új, _myAzureSQLDBAccessGroup_nevű csoportba, amely a következő parancsfájlban látható:

```azurecli-interactive
groupid=$(az ad group create --display-name myAzureSQLDBAccessGroup --mail-nickname myAzureSQLDBAccessGroup --query objectId --output tsv)
msiobjectid=$(az webapp identity show --resource-group myResourceGroup --name <app-name> --query principalId --output tsv)
az ad group member add --group $groupid --member-id $msiobjectid
az ad group member list -g $groupid
```

Ha minden egyes parancsnál meg szeretné tekinteni a JSON-kimenetet, hagyja el a `--query objectId --output tsv` paramétereket.

### <a name="grant-permissions-to-azure-ad-group"></a>Engedélyek megadása az Azure AD-csoportnak

A Cloud Shellben az SQLCMD parancsot használva jelentkezzen be az SQL Database-be. Cserélje le  _\<a Server-Name >t_ a SQL Database-kiszolgálónévre,  _\<az adatbázis nevére >_ az alkalmazás által használt adatbázis nevére, valamint  _\<a HRE >_ és a _\<HRE-Password >_ az Azure ad-felhasználó hitelesítő adataival.

```azurecli-interactive
sqlcmd -S <server-name>.database.windows.net -d <db-name> -U <aad-user-name> -P "<aad-password>" -G -l 30
```

A kívánt adatbázishoz tartozó SQL-parancssorban futtassa az alábbi parancsokat az Azure AD-csoport hozzáadásához, és adja meg az alkalmazás igényeinek megfelelő engedélyeket. Például: 

```sql
CREATE USER [myAzureSQLDBAccessGroup] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [myAzureSQLDBAccessGroup];
ALTER ROLE db_datawriter ADD MEMBER [myAzureSQLDBAccessGroup];
ALTER ROLE db_ddladmin ADD MEMBER [myAzureSQLDBAccessGroup];
GO
```

Az `EXIT` parancs begépelésével térjen vissza a Cloud Shell-parancssorba.

### <a name="modify-connection-string"></a>Kapcsolati sztring módosítása

Ne feledje, hogy a *web. config* vagy a *appSettings. JSON* fájlban elvégzett módosítások a felügyelt identitással működnek, így az egyetlen teendő, ha el szeretné távolítani a meglévő kapcsolati karakterláncot a app Serviceban, amelyet a Visual Studio létrehozta az alkalmazás üzembe helyezését az első idő. Használja az alábbi parancsot, de cserélje le  *\<az App-Name >* az alkalmazás nevére.

```azurecli-interactive
az webapp config connection-string delete --resource-group myResourceGroup --name <app-name> --setting-names MyDbConnection
```

## <a name="publish-your-changes"></a>A módosítások közzététele

Már csak közzé kell tennie a módosításait az Azure-ban.

**Ha az [oktatóanyagból származik: Hozzon létre egy ASP.NET-alkalmazást az](app-service-web-tutorial-dotnet-sqldatabase.md)Azure-ban SQL Database**, tegye közzé a módosításokat a Visual Studióban. A **Solution Explorer** (Megoldáskezelő) lapon kattintson a jobb gombbal a **DotNetAppSqlDb** projektre, és válassza a **Publish** (Közzététel) elemet.

![Közzététel a Megoldáskezelőből](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

A közzétételi oldalon kattintson a **Publish** (Közzététel) elemre. 

**Ha az [oktatóanyagból származik: Hozzon létre egy ASP.net Core és SQL Database alkalmazást](app-service-web-tutorial-dotnetcore-sqldb.md)a Azure app Serviceban**, tegye közzé a módosításokat a git használatával, a következő parancsokkal:

```bash
git commit -am "configure managed identity"
git push azure master
```

Amikor az új weblapon megjelenik a feladatlista, az alkalmazása kapcsolódik az adatbázishoz a felügyelt identitás segítségével.

![Azure-alkalmazás a kód első áttelepítése után](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

Most már ugyanúgy szerkesztheti a feladatlistát, mint korábban.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>További lépések

Az alábbiak elvégzését ismerte meg:

> [!div class="checklist"]
> * Felügyelt identitások engedélyezése
> * SQL Database-hozzáférés engedélyezése a felügyelt identitáshoz
> * Entity Framework konfigurálása az Azure AD-hitelesítés használatára SQL Database
> * Kapcsolódás SQL Database a Visual studióból az Azure AD-hitelesítés használatával

Lépjen a következő oktatóanyaghoz, amelyből megtudhatja, hogyan képezhet le egyedi DNS-nevet a webalkalmazáshoz.

> [!div class="nextstepaction"]
> [Meglévő egyéni DNS-név leképezése Azure App Service](app-service-web-tutorial-custom-domain.md)
