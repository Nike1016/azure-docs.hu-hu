---
title: Az Azure API Management-sablon data model hivatkozás |} A Microsoft Docs
description: Ismerje meg az entitás, és írja be semmilyen felelősséget az adatmodellek az az Azure API Management a fejlesztői portál sablonjainak használt gyakori elemek.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: b0ad7e15-9519-4517-bb73-32e593ed6380
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: apimpm
ms.openlocfilehash: 3c2384b536235554fed7c1cf1a08b7c665f513a8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "61094522"
---
# <a name="azure-api-management-template-data-model-reference"></a>Az Azure API Management sablon adatmodell-referencia
Ez a témakör ismerteti az Azure API Management a fejlesztői portál sablonjainak az adatmodellek a használt gyakori elemek entitás, és írja be semmilyen felelősséget.  
  
 Sablonok használatának ismertetését lásd: [testreszabása sablonok használatával, az API Management fejlesztői portálon](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

A fejlesztői portál nem érhető el a használat rétegében.

## <a name="reference"></a>Leírások

-   [API](#API)  
-   [API összefoglaló](#APISummary)  
-   [Alkalmazás](#Application)  
-   [Melléklet](#Attachment)  
-   [Kódminta](#Sample)  
-   [Megjegyzés](#Comment)  
-   [Szűrés](#Filtering)  
-   [Header](#Header)  
-   [HTTP-kérelem](#HTTPRequest)  
-   [HTTP-válasz](#HTTPResponse)  
-   [A probléma](#Issue)  
-   [Művelet](#Operation)  
-   [A művelet menü](#Menu)  
-   [A művelet menüpont](#MenuItem)  
-   [Stránkování](#Paging)  
-   [A paraméter](#Parameter)  
-   [A termék](#Product)  
-   [Szolgáltató](#Provider)  
-   [Ábrázolás](#Representation)  
-   [Előfizetés](#Subscription)  
-   [Előfizetés összefoglalása](#SubscriptionSummary)  
-   [Felhasználói fiók adatai](#UserAccountInfo)  
-   [Felhasználói bejelentkezés](#UseSignIn)  
-   [A felhasználói regisztráció](#UserSignUp)  
  
##  <a name="API"></a> API  
 A `API` entitás a következő tulajdonságokkal rendelkezik:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`id`|string|Erőforrás-azonosítója. Egyedileg azonosítja az API a jelenlegi API Management-szolgáltatáspéldány belül. Az érték formátuma érvényes relatív URL- `apis/{id}` ahol `{id}` API azonosítója. Ez a tulajdonság csak olvasható.|  
|`name`|string|Az API neve. Nem lehet üres. Hossza legfeljebb 100 karakterből állhat.|  
|`description`|string|Az API leírása. Nem lehet üres. HTML-formázás címkéket tartalmazhat. Hossza legfeljebb 1000 karakter lehet.|  
|`serviceUrl`|string|Abszolút URL-címe az API implementálása a háttérszolgáltatást.|  
|`path`|string|Amely egyedileg azonosítja az API-t és az összes belül az API Management-szolgáltatáspéldány az erőforrás elérési utak relatív URL-címe. Az API végpont alap URL-címe a szolgáltatás-példány létrehozása során megadott kialakításához arra az egy URL-címe az API van hozzáfűzve.|  
|`protocols`|szám tömbje|Ismerteti, mely protokollok a a műveletek az API meghívható. Engedélyezett értékek a következők `1 - http` és `2 - https`, vagy mindkettőt.|  
|`authenticationSettings`|[Engedélyezési kiszolgáló hitelesítési beállítások](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#AuthenticationSettings)|Az API-t szerepel hitelesítési beállítások gyűjteménye.|  
|`subscriptionKeyParameterNames`|objektum|Lekérdezés és/vagy fejléc paraméterek az előfizetési kulcsot tartalmazó egyéni nevének megadására szolgáló nem kötelező tulajdonság. Ha ez a tulajdonság jelen, akkor a két következő tulajdonságok közül legalább tartalmaznia kell.<br /><br /> `{   "subscriptionKeyParameterNames":   {     "query": “customQueryParameterName",     "header": “customHeaderParameterName"   } }`|  
  
##  <a name="APISummary"></a> API összefoglaló  
 A `API summary` entitás a következő tulajdonságokkal rendelkezik:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`id`|string|Erőforrás-azonosítója. Egyedileg azonosítja az API a jelenlegi API Management-szolgáltatáspéldány belül. Az érték formátuma érvényes relatív URL- `apis/{id}` ahol `{id}` API azonosítója. Ez a tulajdonság csak olvasható.|  
|`name`|string|Az API neve. Nem lehet üres. Hossza legfeljebb 100 karakterből állhat.|  
|`description`|string|Az API leírása. Nem lehet üres. HTML-formázás címkéket tartalmazhat. Hossza legfeljebb 1000 karakter lehet.|  
  
##  <a name="Application"></a> Alkalmazás  
 A `application` entitás a következő tulajdonságokkal rendelkezik:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`Id`|string|Az alkalmazás egyedi azonosítója.|  
|`Title`|string|Az alkalmazás címe.|  
|`Description`|string|Az alkalmazás leírását.|  
|`Url`|URI-T|Az alkalmazás URI Azonosítóját.|  
|`Version`|string|Az alkalmazás fájlverzió-információkat.|  
|`Requirements`|string|Az alkalmazás leírását.|  
|`State`|szám|Az alkalmazás aktuális állapotát.<br /><br /> -0 - regisztrálva<br /><br /> -1 - elküldve<br /><br /> -2 - közzététele<br /><br /> És elutasított. 3 –<br /><br /> – 4 – közzé nem tett|  
|`RegistrationDate`|DateTime|A dátum és idő, az alkalmazás regisztrálva lett.|  
|`CategoryId`|szám|A kategória az alkalmazás (pénzügyi, Szórakozással, stb.)|  
|`DeveloperId`|string|A fejlesztői, a kérelmet egyedi azonosítója.|  
|`Attachments`|A gyűjtemény [melléklet](#Attachment) entitásokat.|A mellékletek számára az alkalmazásadatokat, például a képernyőképek vagy ikonjai.|  
|`Icon`|[Melléklet](#Attachment)|Az ikonra az alkalmazásához.|  
  
##  <a name="Attachment"></a> Melléklet  
 A `attachment` entitás a következő tulajdonságokkal rendelkezik:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`UniqueId`|string|A melléklet egyedi azonosítója.|  
|`Url`|string|Az erőforrás URL-címe|  
|`Type`|string|A melléklet típusa.|  
|`ContentType`|string|A melléklet adathordozó-típusát.|  
  
##  <a name="Sample"></a> Kódminta  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`title`|string|A művelet neve.|  
|`snippet`|string|Ez a tulajdonság elavult, és nem használható.|  
|`brush`|string|Melyik kód szintaxisszínek sablon használható, amikor a mintakód jeleníti meg. Engedélyezett értékek a következők `plain`, `php`, `java`, `xml`, `objc`, `python`, `ruby`, és `csharp`.|  
|`template`|string|A kód minta sablon neve.|  
|`body`|string|A kód minta része a kódrészlet egy helyőrző.|  
|`method`|string|A HTTP-metódus a művelet.|  
|`scheme`|string|A művelet kérelem használni kívánt protokollt.|  
|`path`|string|A művelet elérési útja.|  
|`query`|string|Lekérdezési karakterlánc például a megadott paraméterekkel.|  
|`host`|string|Az URL-címe az API Management szolgáltatás átjáró az API-hoz, amely tartalmazza ezt a műveletet.|  
|`headers`|A gyűjtemény [fejléc](#Header) entitásokat.|A fejlécek ehhez a művelethez.|  
|`parameters`|A gyűjtemény [paraméter](#Parameter) entitásokat.|Ez a művelet esetében megadott paramétereket.|  
  
##  <a name="Comment"></a> Megjegyzés  
 A `API` entitás a következő tulajdonságokkal rendelkezik:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`Id`|szám|A Megjegyzés azonosítója.|  
|`CommentText`|string|A Megjegyzés szövege. Lehet olyan HTML.|  
|`DeveloperCompany`|string|A fejlesztői vállalat neve.|  
|`PostedOn`|DateTime|A dátum és idő, a megjegyzést.|  
  
##  <a name="Issue"></a> A probléma  
 A `issue` entitás a következő tulajdonságokkal rendelkezik.  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`Id`|string|A probléma egyedi azonosítója.|  
|`ApiID`|string|Az API-t, amelyhez jelentett probléma azonosítója.|  
|`Title`|string|A probléma címe.|  
|`Description`|string|A probléma leírása.|  
|`SubscriptionDeveloperName`|string|A fejlesztő, amely a hibát jelentett utóneve.|  
|`IssueState`|string|A probléma aktuális állapotát. Lehetséges értékek a következők: javasolt, a megnyitott, a lezárva.|  
|`ReportedOn`|DateTime|A dátum és idő, a probléma lett jelentve.|  
|`Comments`|A gyűjtemény [Megjegyzés](#Comment) entitásokat.|Ennél a hibánál megjegyzéseket.|  
|`Attachments`|A gyűjtemény [melléklet](api-management-template-data-model-reference.md#Attachment) entitásokat.|A probléma a mellékleteket.|  
|`Services`|A gyűjtemény [API](#API) entitásokat.|Az API-k fizethet elő a felhasználót, hogy a probléma mezőjében.|  
  
##  <a name="Filtering"></a> Szűrés  
 A `filtering` entitás a következő tulajdonságokkal rendelkezik:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`Pattern`|string|A jelenlegi keresési kifejezés vagy `null` nincs keresési kifejezés esetén.|  
|`Placeholder`|string|Ha nincs megadott keresési kifejezést a keresőmezőbe írja megjelenítendő szöveg.|  
  
##  <a name="Header"></a> Fejléc  
 Ez a szakasz ismerteti a `parameter` ábrázolását.  
  
|Tulajdonság|Típus|Leírás|  
|--------------|-----------------|----------|  
|`name`|string|A paraméter neve.|  
|`description`|string|Paraméter leírása.|  
|`value`|string|Fejléc értéke.|  
|`typeName`|string|Fejléc érték adattípusa.|  
|`options`|string|Beállítások.|  
|`required`|logikai|A fejléc e megadása kötelező.|  
|`readOnly`|logikai|A fejléc-e írásvédett.|  
  
##  <a name="HTTPRequest"></a> HTTP-kérelem  
 Ez a szakasz ismerteti a `request` ábrázolását.  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`description`|string|A művelet a kérés leírása.|  
|`headers`|a tömb [fejléc](#Header) entitásokat.|Kérelem fejlécei.|  
|`parameters`|a tömb [paraméter](#Parameter)|A művelet kérelem paramétereinek gyűjteménye.|  
|`representations`|a tömb [ábrázolás](#Representation)|Művelet kérelem reprezentációinak gyűjteménye.|  
  
##  <a name="HTTPResponse"></a> HTTP-válasz  
 Ez a szakasz ismerteti a `response` ábrázolását.  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`statusCode`|pozitív egész szám lehet|A művelet válasz állapotkódja.|  
|`description`|string|A művelet válasz leírása.|  
|`representations`|a tömb [ábrázolás](#Representation)|Művelet válasz reprezentációinak gyűjteménye.|  
  
##  <a name="Operation"></a> A művelet  
 A `operation` entitás a következő tulajdonságokkal rendelkezik:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`id`|string|Erőforrás-azonosítója. A művelet a jelenlegi API Management-szolgáltatáspéldány belül egyedileg azonosítja. Az érték formátuma érvényes relatív URL- `apis/{aid}/operations/{id}` ahol `{aid}` egy API-azonosító és `{id}` művelet azonosítója. Ez a tulajdonság csak olvasható.|  
|`name`|string|A művelet neve. Nem lehet üres. Hossza legfeljebb 100 karakterből állhat.|  
|`description`|string|A művelet leírását. Nem lehet üres. HTML-formázás címkéket tartalmazhat. Hossza legfeljebb 1000 karakter lehet.|  
|`scheme`|string|Ismerteti, mely protokollok a a műveletek az API meghívható. Engedélyezett értékek a következők `http`, `https`, vagy mindkettőt `http` és `https`.|  
|`uriTemplate`|string|Relatív URL-cím sablon azonosító a célként megadott erőforrás ehhez a művelethez. Előfordulhat, hogy tartalmazza a paramétereket. Például: `customers/{cid}/orders/{oid}/?date={date}`|  
|`host`|string|Az API Management gateway URL-címe az API-t üzemeltető.|  
|`httpMethod`|string|Művelet HTTP-metódus.|  
|`request`|[HTTP-kérelem](#HTTPRequest)|Kérelem részleteit tartalmazó entitás.|  
|`responses`|a tömb [HTTP-válasz](#HTTPResponse)|A művelet tömbje [HTTP-válasz](#HTTPResponse) entitásokat.|  
  
##  <a name="Menu"></a> A művelet menü  
 A `operation menu` entitás a következő tulajdonságokkal rendelkezik:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`ApiId`|string|A jelenlegi API azonosítója.|  
|`CurrentOperationId`|string|Az aktuális művelet azonosítója.|  
|`Action`|string|A menüben írja be.|  
|`MenuItems`|A gyűjtemény [művelet menüpont](#MenuItem) entitásokat.|A műveletek a jelenlegi API-hoz.|  
  
##  <a name="MenuItem"></a> A művelet menüpont  
 A `operation menu item` entitás a következő tulajdonságokkal rendelkezik:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`Id`|string|A művelet azonosítója.|  
|`Title`|string|A művelet leírását.|  
|`HttpMethod`|string|A Http-metódus a művelet.|  
  
##  <a name="Paging"></a> Stránkování  
 A `paging` entitás a következő tulajdonságokkal rendelkezik:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`Page`|szám|Az aktuális oldalszám.|  
|`PageSize`|szám|Az eredmények maximális száma egy egyetlen lapon jelenik meg.|  
|`TotalItemCount`|szám|A megjelenített elemek száma.|  
|`ShowAll`|logikai|E egyetlen lapon az összes eredmény megjelenítése.|  
|`PageCount`|szám|Az eredmények oldalak számát.|  
  
##  <a name="Parameter"></a> A paraméter  
 Ez a szakasz ismerteti a `parameter` ábrázolását.  
  
|Tulajdonság|Típus|Leírás|  
|--------------|-----------------|----------|  
|`name`|string|A paraméter neve.|  
|`description`|string|Paraméter leírása.|  
|`value`|string|Paraméter értéke.|  
|`options`|sztringek tömbje|Lekérdezés paraméterértékeinek meghatározott értékeket.|  
|`required`|logikai|Itt adhatja meg, hogy paraméter szükség-e vagy sem.|  
|`kind`|szám|Ez a paraméter-e egy elérésiút-paraméter (1), vagy sztringet (2).|  
|`typeName`|string|Paraméter típusa.|  
  
##  <a name="Product"></a> A termék  
 A `product` entitás a következő tulajdonságokkal rendelkezik:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`Id`|string|Erőforrás-azonosítója. A termék a jelenlegi API Management-szolgáltatáspéldány belül egyedileg azonosítja. Az érték formátuma érvényes relatív URL- `products/{pid}` ahol `{pid}` termék azonosítója. Ez a tulajdonság csak olvasható.|  
|`Title`|string|A termék nevét. Nem lehet üres. Hossza legfeljebb 100 karakterből állhat.|  
|`Description`|string|A termék leírását. Nem lehet üres. HTML-formázás címkéket tartalmazhat. Hossza legfeljebb 1000 karakter lehet.|  
|`Terms`|string|A termék használati feltételeit. Iratkozzon fel a termék fejlesztők fog bemutatni, és el kell fogadnia ezeket a feltételeket, az előfizetés folyamat végrehajtása előtt.|  
|`ProductState`|szám|Itt adhatja meg, hogy a termék közzététele, vagy sem. Közzétett és a fejlesztők a fejlesztői portál által felderíthető. A nem közzétett termékek már csak a rendszergazdák jelennek meg.<br /><br /> A termék állapota megengedett értékei a következők:<br /><br /> - `0 - Not Published`<br /><br /> - `1 - Published`<br /><br /> - `2 - Deleted`|  
|`AllowMultipleSubscriptions`|logikai|Megadja, hogy egy felhasználó egyszerre a termékre több előfizetéssel is rendelkezhet.|  
|`MultipleSubscriptionsCount`|szám|Előfizetés a termékre maximális száma a felhasználó szerepelhetnek egyszerre.|  
  
##  <a name="Provider"></a> Szolgáltató  
 A `provider` entitás a következő tulajdonságokkal rendelkezik:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`Properties`|karakterlánc szótár|A hitelesítésszolgáltató tulajdonságait.|  
|`AuthenticationType`|string|A szolgáltató típusát. (Az azure Active Directory, Facebook-bejelentkezés Google-fiókkal, a Microsoft Account, Twitter).|  
|`Caption`|string|A szolgáltató megjelenített nevét.|  
  
##  <a name="Representation"></a> Ábrázolás  
 Ez a szakasz ismerteti a `representation`.  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`contentType`|string|Adja meg például egy regisztrált vagy egyéni tartalom típusát e ábrázolás `application/xml`.|  
|`sample`|string|Példa a megjelenítését.|  
  
##  <a name="Subscription"></a> Előfizetés  
 A `subscription` entitás a következő tulajdonságokkal rendelkezik:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`Id`|string|Erőforrás-azonosítója. Az előfizetés az aktuális API Management-szolgáltatáspéldány belül egyedileg azonosítja. Az érték formátuma érvényes relatív URL- `subscriptions/{sid}` ahol `{sid}` egy előfizetés-azonosító. Ez a tulajdonság csak olvasható.|  
|`ProductId`|string|A termék erőforrás-azonosítója az előfizetett termékkel. Az érték formátuma érvényes relatív URL- `products/{pid}` ahol `{pid}` termék azonosítója.|  
|`ProductTitle`|string|A termék nevét. Nem lehet üres. Hossza legfeljebb 100 karakterből állhat.|  
|`ProductDescription`|string|A termék leírását. Nem lehet üres. HTML-formázás címkéket tartalmazhat. Hossza legfeljebb 1000 karakter lehet.|  
|`ProductDetailsUrl`|string|Relatív URL-címe a termék részletei.|  
|`state`|string|Az előfizetés állapotát. Lehetséges állapotok a következők:<br /><br /> - `0 - suspended` – az előfizetés le van tiltva, és az előfizető nem hívható meg a termék minden API-k.<br /><br /> - `1 - active` – az előfizetés nem aktív.<br /><br /> - `2 - expired` – az előfizetés elérte a lejárati dátum és az inaktiválva lett.<br /><br /> - `3 - submitted` – az előfizetési kérést, fejlesztője által lett végrehajtva, de még nincs jóváhagyták vagy elutasították.<br /><br /> - `4 - rejected` – az előfizetés kérelem el lett utasítva, egy rendszergazda.<br /><br /> - `5 - cancelled` – a fejlesztői vagy a rendszergazda az előfizetés törölve lett.|  
|`DisplayName`|string|Az előfizetés megjelenített neve.|  
|`CreatedDate`|Dátum és idő|Az előfizetés létrehozási dátuma, ISO 8601 formátumú: `2014-06-24T16:25:00Z`.|  
|`CanBeCancelled`|logikai|Hogy az előfizetés az aktuális felhasználó lehet visszavonni.|  
|`IsAwaitingApproval`|logikai|Az előfizetés e jóváhagyására vár.|  
|`StartDate`|Dátum és idő|A kezdő dátum, az előfizetés, az ISO 8601 formátumú: `2014-06-24T16:25:00Z`.|  
|`ExpirationDate`|Dátum és idő|Az előfizetés, az ISO 8601 formátumú a lejárati dátum: `2014-06-24T16:25:00Z`.|  
|`NotificationDate`|Dátum és idő|Az előfizetést, az ISO 8601 formátumú értesítés dátuma: `2014-06-24T16:25:00Z`.|  
|`primaryKey`|string|Az elsődleges előfizetési kulcsot. Maximális hossz 256 karakter.|  
|`secondaryKey`|string|A másodlagos előfizetési kulcs. Maximális hossz 256 karakter.|  
|`CanBeRenewed`|logikai|Hogy az előfizetés meg lehet újítani a jelenlegi felhasználó.|  
|`HasExpired`|logikai|Hogy az előfizetése lejárt.|  
|`IsRejected`|logikai|Hogy az előfizetési kérés megtagadva.|  
|`CancelUrl`|string|A relatív URL-cím megszüntetni az előfizetést.|  
|`RenewUrl`|string|Az előfizetés meghosszabbításához relatív URL-címe|  
  
##  <a name="SubscriptionSummary"></a> Előfizetés összefoglalása  
 A `subscription summary` entitás a következő tulajdonságokkal rendelkezik:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`Id`|string|Erőforrás-azonosítója. Az előfizetés az aktuális API Management-szolgáltatáspéldány belül egyedileg azonosítja. Az érték formátuma érvényes relatív URL- `subscriptions/{sid}` ahol `{sid}` egy előfizetés-azonosító. Ez a tulajdonság csak olvasható.|  
|`DisplayName`|string|Az előfizetés megjelenített neve|  
  
##  <a name="UserAccountInfo"></a> Felhasználói fiók adatai  
 A `user account info` entitás a következő tulajdonságokkal rendelkezik:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`FirstName`|string|Utónév. Nem lehet üres. Hossza legfeljebb 100 karakterből állhat.|  
|`LastName`|string|Utolsó neve. Nem lehet üres. Hossza legfeljebb 100 karakterből állhat.|  
|`Email`|string|E-mail-cím. Nem lehet üres, és a szolgáltatáspéldány egyedinek kell lennie. Hossza legfeljebb 254 karakterből állhat.|  
|`Password`|string|Felhasználói fiók jelszavát.|  
|`NameIdentifier`|string|Fiókazonosító, ugyanaz, mint a felhasználó e-mail-címe.|  
|`ProviderName`|string|Hitelesítési szolgáltató neve.|  
|`IsBasicAccount`|logikai|IGAZ, ha ez a fiók regisztrálva lett, e-mailek és a jelszavát; használatával hamis, ha a fiók regisztrálva lett a szolgáltató használatával.|  
  
##  <a name="UseSignIn"></a> Felhasználói bejelentkezés  
 A `user sign in` entitás a következő tulajdonságokkal rendelkezik:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`Email`|string|E-mail-cím. Nem lehet üres, és a szolgáltatáspéldány egyedinek kell lennie. Hossza legfeljebb 254 karakterből állhat.|  
|`Password`|string|Felhasználói fiók jelszavát.|  
|`ReturnUrl`|string|Jelentkezzen be az URL-címe a lap, ahol a felhasználó kattint.|  
|`RememberMe`|logikai|-E menteni a jelenlegi felhasználó adatait.|  
|`RegistrationEnabled`|logikai|Regisztráció engedélyezve van-e.|  
|`DelegationEnabled`|logikai|Delegált bejelentkezési engedélyezve van-e.|  
|`DelegationUrl`|string|A delegált bejelentkezési URL-címet, ha engedélyezve van.|  
|`SsoSignUpUrl`|string|Az egyszeri bejelentkezési URL-cím a felhasználó számára, ha van ilyen.|  
|`AuxServiceUrl`|string|Ha a jelenlegi felhasználó rendszergazda, ez a hivatkozás a service-példányhoz az Azure Portalon.|  
|`Providers`|A gyűjtemény [szolgáltató](#Provider) entitások|A hitelesítésszolgáltatók, a felhasználó számára.|  
|`UserRegistrationTerms`|string|Az feltételeket, amelyeket a felhasználónak el kell fogadnia a bejelentkezés előtt.|  
|`UserRegistrationTermsEnabled`|logikai|E kifejezések engedélyezettek.|  
  
##  <a name="UserSignUp"></a> Felhasználói regisztráció  
 A `user sign up` entitás a következő tulajdonságokkal rendelkezik:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`PasswordConfirm`|logikai|Érték, amelyet a [előfizetési](api-management-page-controls.md#sign-up)előfizetési vezérlő.|  
|`Password`|string|Felhasználói fiók jelszavát.|  
|`PasswordVerdictLevel`|szám|Érték, amelyet a [előfizetési](api-management-page-controls.md#sign-up)előfizetési vezérlő.|  
|`UserRegistrationTerms`|string|Az feltételeket, amelyeket a felhasználónak el kell fogadnia a bejelentkezés előtt.|  
|`UserRegistrationTermsOptions`|szám|Érték, amelyet a [előfizetési](api-management-page-controls.md#sign-up)előfizetési vezérlő.|  
|`ConsentAccepted`|logikai|Érték, amelyet a [előfizetési](api-management-page-controls.md#sign-up)előfizetési vezérlő.|  
|`Email`|string|E-mail-cím. Nem lehet üres, és a szolgáltatáspéldány egyedinek kell lennie. Hossza legfeljebb 254 karakterből állhat.|  
|`FirstName`|string|Utónév. Nem lehet üres. Hossza legfeljebb 100 karakterből állhat.|  
|`LastName`|string|Utolsó neve. Nem lehet üres. Hossza legfeljebb 100 karakterből állhat.|  
|`UserData`|string|Érték, amelyet a [előfizetési](api-management-page-controls.md#sign-up) vezérlő.|  
|`NameIdentifier`|string|Érték, amelyet a [előfizetési](api-management-page-controls.md#sign-up)előfizetési vezérlő.|  
|`ProviderName`|string|Hitelesítési szolgáltató neve.|

## <a name="next-steps"></a>További lépések
Sablonok használatának ismertetését lásd: [testreszabása sablonok használatával, az API Management fejlesztői portálon](api-management-developer-portal-templates.md).
