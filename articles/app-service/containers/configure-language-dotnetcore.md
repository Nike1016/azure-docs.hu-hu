---
title: ASP.NET Core-alkalmazások konfigurálása – Azure App Service | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhatja ASP.NET Core alkalmazások működését Azure App Service
services: app-service
documentationcenter: ''
author: cephalin
manager: gwallace
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/13/2019
ms.author: cephalin
ms.openlocfilehash: b05120148d3b82829c465effbcdc948da950aaf0
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990261"
---
# <a name="configure-a-linux-aspnet-core-app-for-azure-app-service"></a>Linux ASP.NET Core-alkalmazás konfigurálása Azure App Servicehoz

ASP.NET Core alkalmazásokat lefordított bináris fájlként kell telepíteni. A Visual Studio közzétételi eszköze létrehozza a megoldást, majd közvetlenül telepíti a lefordított bináris fájlokat, míg a App Service üzembe helyezési motor először a kódrészletet telepíti, majd lefordítja a bináris fájlokat.

Ez az útmutató a App Service beépített Linux-tárolóját használó ASP.NET Core fejlesztők számára biztosít főbb fogalmakat és útmutatást. Ha még soha nem használta a Azure App Servicet, először kövesse a [ASP.net Core](quickstart-dotnetcore.md) rövid útmutató és a [ASP.net Core SQL Database oktatóanyagot](tutorial-dotnetcore-sqldb-app.md) .

## <a name="show-net-core-version"></a>.NET Core verzió megjelenítése

Az aktuális .NET Core-verzió megjelenítéséhez futtassa a következő parancsot a [Cloud Shellban](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Az összes támogatott .NET Core-verzió megjelenítéséhez futtassa a következő parancsot a [Cloud Shellban](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep DOTNETCORE
```

## <a name="set-net-core-version"></a>A .NET Core verziójának beállítása

Futtassa a következő parancsot a [Cloud Shell](https://shell.azure.com) a .net Core verzió 2,1-re való beállításához:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "DOTNETCORE|2.1"
```

## <a name="access-environment-variables"></a>Hozzáférési környezeti változók

App Service az [Alkalmazásbeállítások](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) az alkalmazás kódján kívül is megadhatók. Ezt követően bármely osztályban elérheti őket a standard ASP.NET Core függőségi befecskendezési minta használatával:

```csharp
include Microsoft.Extensions.Configuration;

namespace SomeNamespace 
{
    public class SomeClass
    {
        private IConfiguration _configuration;
    
        public SomeClass(IConfiguration configuration)
        {
            _configuration = configuration;
        }
    
        public SomeMethod()
        {
            // retrieve App Service app setting
            var myAppSetting = _configuration["MySetting"];
            // retrieve App Service connection string
            var myConnString = _configuration.GetConnectionString("MyDbConnection");
        }
    }
}
```

Ha például a App Service és a *appSettings. JSON*fájlban azonos nevű Alkalmazásbeállítás van konfigurálva, akkor a app Service érték elsőbbséget élvez a *appSettings. JSON* értékkel szemben. A helyi *appSettings. JSON* érték lehetővé teszi az alkalmazás helyi hibakeresését, de a app Service érték lehetővé teszi az alkalmazás futtatását a termékben éles beállításokkal. A kapcsolatok karakterláncai ugyanúgy működnek. Így megtarthatja az alkalmazási titkokat a Code repositoryn kívül, és a kód módosítása nélkül is elérheti a megfelelő értékeket.

## <a name="get-detailed-exceptions-page"></a>Részletes kivételek oldalának beolvasása

Ha a ASP.NET-alkalmazás kivételt hoz létre a Visual Studio debuggerben, a böngésző egy részletes kivétel lapot jelenít meg, de App Service a lapot általános **HTTP 500** -hiba váltja fel, vagy **hiba történt a kérelem feldolgozása során.** üzenetet. A app Service részletes kivétel lapjának megjelenítéséhez adja hozzá az `ASPNETCORE_ENVIRONMENT` alkalmazás beállításait az alkalmazáshoz a következő parancs futtatásával a <a target="_blank" href="https://shell.azure.com" >Cloud Shellban</a>.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASPNETCORE_ENVIRONMENT="Development"
```

## <a name="detect-https-session"></a>HTTPS-munkamenet észlelése

App Service az [SSL-megszakítás](https://wikipedia.org/wiki/TLS_termination_proxy) a hálózati terheléselosztó esetében történik, így minden HTTPS-kérelem titkosítatlan http-kérésként éri el az alkalmazást. Ha az alkalmazás logikájának tudnia kell, hogy a felhasználói kérések titkosítva vannak-e, vagy sem, konfigurálja a továbbított fejlécek middleware-t a *Startup.cs*-ben:

- Konfigurálja a middleware-t a [ForwardedHeadersOptions](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions) , `X-Forwarded-For` hogy `X-Forwarded-Proto` továbbítsa `Startup.ConfigureServices`a és a fejléceket a alkalmazásban.
- Adjon hozzá magánhálózati IP-címtartományt az ismert hálózatokhoz, hogy a köztes kapcsolat megbízható legyen a App Service Load balancerben.
- A [UseForwardedHeaders](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders) metódus `Startup.Configure` meghívása az egyéb köztes közbenső funkciók meghívása előtt.

A három elem együttes elhelyezésével a kód a következő példához hasonlóan néz ki:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders =
            ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:10.0.0.0"), 104));
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:192.168.0.0"), 112));
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:172.16.0.0"), 108));
    });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseForwardedHeaders();

    ...

    app.UseMvc();
}
```

További információkért lásd: [a ASP.net Core konfigurálása a proxykiszolgáló és a](https://docs.microsoft.com/aspnet/core/host-and-deploy/proxy-load-balancer)terheléselosztó működéséhez.

## <a name="deploy-multi-project-solutions"></a>Több projektből álló megoldások üzembe helyezése

Amikor egy ASP.NET-tárházat telepít a központi telepítési motorba egy *. csproj* fájllal a gyökérkönyvtárban, a motor üzembe helyezi a projektet. Ha egy ASP.NET-tárházat telepít egy *. SLN* fájllal a gyökérkönyvtárban, a motor kiválasztja az első webhelyt vagy webalkalmazás-projektet, amelyet app Service alkalmazásként talál. Lehetséges, hogy a motor nem válassza ki a kívánt projektet.

Többprojektes megoldás üzembe helyezéséhez két különböző módon adhatja meg a App Service használandó projektet:

### <a name="using-deployment-file"></a>A. Deployment fájl használata

Adjon hozzá egy *. Deployment* fájlt az adattár gyökeréhez, és adja hozzá a következő kódot:

```
[config]
project = <project-name>/<project-name>.csproj
```

### <a name="using-app-settings"></a>Alkalmazásbeállítások használata

A <a target="_blank" href="https://shell.azure.com">Azure Cloud Shell</a>az alábbi CLI-parancs futtatásával adjon hozzá egy alkalmazást az App Service alkalmazáshoz. Cserélje le  *\<az alkalmazás neve >* ,  *\<az erőforrás-csoport neve >* és  *\<a projekt neve >* a megfelelő értékekre.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="access-diagnostic-logs"></a>Diagnosztikai naplók elérése

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>SSH-munkamenet megnyitása böngészőben

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Alkalmazás ASP.NET Core SQL Database](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [App Service Linux – gyakori kérdések](app-service-linux-faq.md)