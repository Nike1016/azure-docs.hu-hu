---
title: Védett webes API – alkalmazás kódjának konfigurálása | Azure Active Directory
description: Ismerje meg, hogyan hozhat létre védett webes API-t, és hogyan konfigurálhatja az alkalmazás kódját.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b249b99faa62e73b9aa3247f71f88767fca96f01
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2019
ms.locfileid: "68488838"
---
# <a name="protected-web-api-adding-authorization-to-your-api"></a>Védett webes API: Engedélyezés hozzáadása az API-hoz

Ez a cikk azt ismerteti, hogyan adhat hozzá engedélyt a webes API-hoz. Ez a védelem biztosítja, hogy az API-t csak a következő hívja meg:

- Alkalmazások a megfelelő hatókörökkel rendelkező felhasználók nevében.
- A megfelelő alkalmazás-szerepkörökkel rendelkező Daemon-alkalmazások.

> [!NOTE]
> A cikkben szereplő kódrészleteket a rendszer kinyeri a következő mintákból, amelyek teljes mértékben működőképesek
>
> - [ASP.net Core web API növekményes oktatóanyag](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/1.%20Desktop%20app%20calls%20Web%20API/TodoListService/Controllers/TodoListController.cs#L37) a githubon
> - [ASP.NET web API-minta](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof/blob/dfd0115533d5a230baff6a3259c76cf117568bd9/TodoListService/Controllers/TodoListController.cs#L48)

A ASP.net/ASP.net Core webes API-k védeleméhez hozzá kell adnia az `[Authorize]` attribútumot a következők közül:

- A vezérlőt, ha azt szeretné, hogy a vezérlő összes művelete védve legyen
- Az API-hoz tartozó egyéni vezérlő művelet

```CSharp
    [Authorize]
    public class TodoListController : Controller
    {
     ...
    }
```

Ez a védelem azonban nem elég. Csak azt garantálja, hogy a ASP.NET/ASP.NET mag érvényesíti a jogkivonatot. Az API-nak ellenőriznie kell, hogy a webes API meghívásához használt tokent kérték-e a várt jogcímek, különösen:

- A *hatókörök*, ha az API-t egy felhasználó nevében hívják meg.
- Az *alkalmazás szerepkörei*, ha az API-t egy Daemon-alkalmazásból lehet meghívni.

## <a name="verifying-scopes-in-apis-called-on-behalf-of-users"></a>A hatókörök ellenőrzése a felhasználók nevében meghívott API-k között

Ha az API-t egy felhasználó nevében egy ügyfélalkalmazás hívja meg, akkor olyan tulajdonosi jogkivonatot kell igényelnie, amely meghatározott hatókörökkel rendelkezik az API-hoz. (Lásd a [kód konfigurációját | Tulajdonosi jogkivonat](scenario-protected-web-api-app-configuration.md#bearer-token).)

```CSharp
[Authorize]
public class TodoListController : Controller
{
    /// <summary>
    /// The web API will accept only tokens 1) for users, 2) that have the `access_as_user` scope for
    /// this API.
    /// </summary>
    const string scopeRequiredByAPI = "access_as_user";

    // GET: api/values
    [HttpGet]
    public IEnumerable<TodoItem> Get()
    {
        VerifyUserHasAnyAcceptedScope(scopeRequiredByAPI);
        // Do the work and return the result.
        ...
    }
...
}
```

A `VerifyUserHasAnyAcceptedScope` metódus a következőhöz hasonló módon fog kinézni:

- Ellenőrizze, hogy létezik-e a `http://schemas.microsoft.com/identity/claims/scope` vagy `scp`a nevű jogcím.
- Győződjön meg arról, hogy a jogcím olyan értékkel rendelkezik, amely az API által várt hatókört tartalmazza.

```CSharp
    /// <summary>
    /// When applied to a <see cref="HttpContext"/>, verifies that the user authenticated in the 
    /// web API has any of the accepted scopes.
    /// If the authenticated user doesn't have any of these <paramref name="acceptedScopes"/>, the
    /// method throws an HTTP Unauthorized error with a message noting which scopes are expected in the token.
    /// </summary>
    /// <param name="acceptedScopes">Scopes accepted by this API</param>
    /// <exception cref="HttpRequestException"/> with a <see cref="HttpResponse.StatusCode"/> set to 
    /// <see cref="HttpStatusCode.Unauthorized"/>
    public static void VerifyUserHasAnyAcceptedScope(this HttpContext context,
                                                     params string[] acceptedScopes)
    {
        if (acceptedScopes == null)
        {
            throw new ArgumentNullException(nameof(acceptedScopes));
        }
        Claim scopeClaim = HttpContext?.User
                                      ?.FindFirst("http://schemas.microsoft.com/identity/claims/scope");
        if (scopeClaim == null || !scopeClaim.Value.Split(' ').Intersect(acceptedScopes).Any())
        {
            context.Response.StatusCode = (int)HttpStatusCode.Unauthorized;
            string message = $"The 'scope' claim does not contain scopes '{string.Join(",", acceptedScopes)}' or was not found";
            throw new HttpRequestException(message);
        }
    }
```

Ez a [mintakód](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/Microsoft.Identity.Web/Resource/ScopesRequiredByWebAPIExtension.cs#L47) a ASP.net Core. A ASP.net esetében csak cserélje `HttpContext.User` le `ClaimsPrincipal.Current`a-t, és cserélje `"http://schemas.microsoft.com/identity/claims/scope"` le `"scp"`a jogcím típusát a következőre:. (Lásd még a cikk későbbi részében található kódrészletet.)

## <a name="verifying-app-roles-in-apis-called-by-daemon-apps"></a>Az alkalmazás szerepköreinek ellenőrzése Daemon-alkalmazások által hívott API-kon

Ha a webes API-t egy [Daemon-alkalmazás](scenario-daemon-overview.md)hívja meg, akkor az alkalmazásnak a webes API-hoz szükséges alkalmazási engedéllyel kell rendelkeznie. Láttuk az [alkalmazás engedélyeinek (az alkalmazás szerepkörei)](https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-app-registration#exposing-application-permissions-app-roles) az API-k által elérhetővé tett (például az `access_as_application` alkalmazási szerepkörnek) megadását.
Most az API-knak ellenőriznie kell, hogy a kapott jogkivonat tartalmazza-e `roles` a jogcímet, és hogy a jogcím a várt értékkel rendelkezik-e. Az ellenőrzést végző kód hasonló a delegált engedélyeket ellenőrző kódhoz, kivéve, hogy a tesztelés `scopes`helyett a vezérlő művelete a következő `roles`műveleteket végzi el:

```CSharp
[Authorize]
public class TodoListController : ApiController
{
    public IEnumerable<TodoItem> Get()
    {
        ValidateAppRole("access_as_application");
        ...
    }
```

A `ValidateAppRole()` metódus a következőhöz hasonló lehet:

```CSharp
private void ValidateAppRole(string appRole)
{
    //
    // The `role` claim tells you what permissions the client application has in the service.
    // In this case, we look for a `role` value of `access_as_application`.
    //
    Claim roleClaim = ClaimsPrincipal.Current.FindFirst("roles");
    if (roleClaim == null || !roleClaim.Value.Split(' ').Contains(appRole))
    {
        throw new HttpResponseException(new HttpResponseMessage 
        { StatusCode = HttpStatusCode.Unauthorized, 
            ReasonPhrase = $"The 'roles' claim does not contain '{appRole}' or was not found" 
        });
    }
}
}
```

Ezúttal a kódrészlet a ASP.NET. ASP.net Core esetében egyszerűen cserélje le `ClaimsPrincipal.Current` a `HttpContext.User`-t, és `"roles"` cserélje le a `"http://schemas.microsoft.com/identity/claims/roles"`jogcím nevét a következőre:. (Lásd a cikk korábbi részében található kódrészletet is.)

### <a name="accepting-app-only-tokens-if-the-web-api-should-be-called-only-by-daemon-apps"></a>Csak alkalmazásbeli tokenek fogadása, ha a webes API-t csak Daemon-alkalmazások hívhatják meg

A `roles` jogcímet a felhasználók felhasználói hozzárendelési mintákban is használják. (Lásd [a következő témakört: Vegyen fel alkalmazás-szerepköröket az alkalmazásban, és](howto-add-app-roles-in-azure-ad-apps.md)fogadja őket a tokenbe.) Így a szerepkörök ellenőrzése lehetővé teszi az alkalmazások számára, hogy felhasználóként és a másik módon jelentkezzenek be, ha a szerepkörök mindkettőhöz hozzárendelhetők. Javasoljuk, hogy a félreértések elkerülése érdekében különböző szerepköröket deklaráljon a felhasználók és az alkalmazások számára.

Ha csak a démoni alkalmazásokat szeretné engedélyezni a webes API meghívásához, vegyen fel egy feltételt, amikor érvényesíti az alkalmazás szerepkörét, hogy a jogkivonat csak alkalmazási jogkivonat:

```CSharp
string oid = ClaimsPrincipal.Current.FindFirst("oid")?.Value;
string sub = ClaimsPrincipal.Current.FindFirst("sub")?.Value;
bool isAppOnlyToken = oid == sub;
```

Az inverz feltétel ellenőrzése csak azokat az alkalmazásokat engedélyezi, amelyek bejelentkeznek egy felhasználónak az API meghívásához.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Áthelyezés éles környezetbe](scenario-protected-web-api-production.md)
