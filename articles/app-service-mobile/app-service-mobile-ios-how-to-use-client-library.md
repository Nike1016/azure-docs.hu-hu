---
title: Az iOS SDK használata az Azure-ban Mobile Apps
description: Az iOS SDK használata az Azure-ban Mobile Apps
services: app-service\mobile
documentationcenter: ios
author: elamalani
editor: ''
ms.assetid: 4e8e45df-c36a-4a60-9ad4-393ec10b7eb9
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 898bf082874a1e9bf26dd094a6a0fe55417c9d8e
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68851064"
---
# <a name="how-to-use-ios-client-library-for-azure-mobile-apps"></a>Az iOS-hez készült ügyféloldali kódtár használata az Azure-ban Mobile Apps

[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

> [!NOTE]
> A Visual Studio App Center a Mobile App Development új és integrált szolgáltatásaiba fektet. A fejlesztők aszolgáltatások kiépítését, **tesztelését** és terjesztését használhatják a folyamatos integráció és a kézbesítési folyamat beállításához. Az alkalmazás üzembe helyezését követően a fejlesztők az **elemzési** és **diagnosztikai** szolgáltatások segítségével ellenőrizhetik az alkalmazás állapotát és használatát, és a leküldéses szolgáltatást használó felhasználókkal is elvégezhetik a felhasználókat. A fejlesztők a hitelesítést a felhasználók és az adatszolgáltatások hitelesítésére is használhatják a Felhőbeli alkalmazásadatok megőrzése és szinkronizálása érdekében. Tekintse meg [app Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=/app-service-mobile-ios-how-to-use-client-library) még ma.
>

## <a name="overview"></a>Áttekintés
Ez az útmutató bemutatja, hogyan hajthat végre gyakori forgatókönyveket a legújabb [Azure Mobile apps iOS SDK][1]használatával. Ha még nem ismeri az Azure Mobile Appst, először fejezze be az [azure Mobile Apps gyorskonfigurálás] a háttérrendszer létrehozásához, egy tábla létrehozásához és egy előre elkészített iOS Xcode-projekt letöltéséhez. Ebben az útmutatóban az ügyféloldali iOS SDK-ra fogunk összpontosítani. Ha többet szeretne megtudni a háttérbeli kiszolgálóoldali SDK-ról, tekintse meg a Server SDK-val kapcsolatos tudnivalókat.

## <a name="reference-documentation"></a>Segédanyagok

Az iOS-ügyfél SDK dokumentációja itt található: Az [Azure Mobile apps iOS-ügyfél referenciája][2].

## <a name="supported-platforms"></a>Támogatott platformok

Az iOS SDK a Objective-C-projekteket, a Swift 2,2-projekteket és a Swift 2,3-projekteket támogatja az iOS-es vagy újabb 8,0 verziókban.

A "kiszolgálói folyamat" hitelesítés webnézetet használ a bemutatott felhasználói felületen.  Ha az eszköz nem tud Webnézeti felhasználói felületet bemutatni, akkor egy másik hitelesítési módszerre van szükség, amely kívül esik a termék hatókörén.  
Ez az SDK ezért nem alkalmas a Watch-Type vagy a hasonló módon korlátozott eszközökre.

## <a name="Setup"></a>Telepítés és előfeltételek

Ez az útmutató azt feltételezi, hogy létrehozott egy táblázatot tartalmazó hátteret. Ez az útmutató azt feltételezi, hogy a tábla ugyanazzal a sémával rendelkezik, mint az oktatóanyagokban szereplő táblák. Az útmutató emellett azt is feltételezi, hogy a kódban hivatkozik `MicrosoftAzureMobile.framework` és importálja. `MicrosoftAzureMobile/MicrosoftAzureMobile.h`

## <a name="create-client"></a>kézikönyv: Ügyfél létrehozása

Egy Azure Mobile Apps-háttér a projektben való eléréséhez hozzon létre egy `MSClient`. Cserélje `AppUrl` le az alkalmazást az alkalmazás URL-címére. Lehet, hogy `gatewayURLString` elhagyja `applicationKey` és üres. Ha beállít egy átjárót a hitelesítéshez, töltse `gatewayURLString` fel az átjáró URL-címével.

**Objective-C**:

```objc
MSClient *client = [MSClient clientWithApplicationURLString:@"AppUrl"];
```

**Swift**:

```swift
let client = MSClient(applicationURLString: "AppUrl")
```

## <a name="table-reference"></a>kézikönyv: Táblázat létrehozása – hivatkozás

Az adatok elérése vagy frissítése érdekében hozzon létre a háttértáblára mutató hivatkozást. A `TodoItem` helyére írja be a tábla nevét.

**Objective-C**:

```objc
MSTable *table = [client tableWithName:@"TodoItem"];
```

**Swift**:

```swift
let table = client.tableWithName("TodoItem")
```

## <a name="querying"></a>kézikönyv: Adatlekérdezés

Adatbázis-lekérdezés létrehozásához kérdezze le az `MSTable` objektumot. A következő lekérdezés lekéri az összes elemet `TodoItem` , és naplózza az egyes elemek szövegét.

**Objective-C**:

```objc
[table readWithCompletion:^(MSQueryResult *result, NSError *error) {
        if(error) { // error is nil if no error occurred
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) { // items is NSArray of records that match query
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

**Swift**:

```swift
table.readWithCompletion { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```

## <a name="filtering"></a>kézikönyv: Visszaadott adatértékek szűrése

Az eredmények szűréséhez számos lehetőség közül választhat.

Predikátum használatával történő szűréshez használjon `NSPredicate` és. `readWithPredicate` A következő szűrők visszaadott adatokat, hogy csak a hiányos teendőket megtalálják.

**Objective-C**:

```objc
// Create a predicate that finds items where complete is false
NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
// Query the TodoItem table
[table readWithPredicate:predicate completion:^(MSQueryResult *result, NSError *error) {
        if(error) {
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) {
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

**Swift**:

```swift
// Create a predicate that finds items where complete is false
let predicate =  NSPredicate(format: "complete == NO")
// Query the TodoItem table
table.readWithPredicate(predicate) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```

## <a name="query-object"></a>kézikönyv: MSQuery használata

Összetett lekérdezés (beleértve a rendezést és a lapozást is) elvégzéséhez hozzon létre egy `MSQuery` objektumot közvetlenül vagy predikátum használatával:

**Objective-C**:

```objc
MSQuery *query = [table query];
MSQuery *query = [table queryWithPredicate: [NSPredicate predicateWithFormat:@"complete == NO"]];
```

**Swift**:

```swift
let query = table.query()
let query = table.queryWithPredicate(NSPredicate(format: "complete == NO"))
```

`MSQuery`lehetővé teszi több lekérdezési viselkedés szabályozását.

* Az eredmények sorrendjének meghatározása
* A visszaadni kívánt mezők korlátozása
* A visszaadni kívánt rekordok számának korlátozása
* Válaszban szereplő összes szám meghatározása
* Egyéni lekérdezési karakterlánc paramétereinek megadása a kérelemben
* További függvények alkalmazása

Lekérdezés végrehajtása az objektum hívásával `readWithCompletion`. `MSQuery`

## <a name="sorting"></a>kézikönyv: Az Adatrendezés a MSQuery

Az eredmények rendezéséhez nézzük meg a példát. A "text" növekvő érték szerinti rendezéshez, majd a "Befejezés" csökkenő sorrend `MSQuery` szerint:

**Objective-C**:

```objc
[query orderByAscending:@"text"];
[query orderByDescending:@"complete"];
[query readWithCompletion:^(MSQueryResult *result, NSError *error) {
        if(error) {
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) {
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

**Swift**:

```swift
query.orderByAscending("text")
query.orderByDescending("complete")
query.readWithCompletion { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```

## <a name="selecting"></a><a name="parameters"></a>kézikönyv: Mezők korlátozása és a lekérdezési karakterlánc paramétereinek kibontása a MSQuery

A lekérdezésben visszaadott mezők korlátozásához adja meg a mezők nevét a **selectFields** tulajdonságban. Ez a példa csak a szöveget és a befejezett mezőket adja vissza:

**Objective-C**:

```objc
query.selectFields = @[@"text", @"complete"];
```

**Swift**:

```swift
query.selectFields = ["text", "complete"]
```

Ha további lekérdezési karakterlánc-paramétereket szeretne felvenni a kiszolgálói kérelembe (például azért, mert egy egyéni kiszolgálóoldali parancsfájl használja őket), `query.parameters` töltse fel a következőhöz hasonlót:

**Objective-C**:

```objc
query.parameters = @{
    @"myKey1" : @"value1",
    @"myKey2" : @"value2",
};
```

**Swift**:

```swift
query.parameters = ["myKey1": "value1", "myKey2": "value2"]
```

## <a name="paging"></a>kézikönyv: Oldalméret konfigurálása

Az Azure Mobile Apps az oldalméret a háttérbeli táblákban egyszerre lehúzott rekordok számát szabályozza. Ha `pull` az adatok meghívása után az adatok kötegbe kerülnek, akkor a lap mérete alapján, amíg nincs több lekéréses rekord.

A **MSPullSettings** az alább látható módon konfigurálható az oldal mérete. Az alapértelmezett oldalméret 50, az alábbi példa pedig 3 értékre változik.

A teljesítményre vonatkozó okok miatt más oldalméretet is beállíthat. Ha nagy számú kis adatrekordja van, akkor a felső oldalméret csökkenti a kiszolgáló ciklikus útjainak számát.

Ez a beállítás csak az oldal méretét szabályozza az ügyféloldali oldalon. Ha az ügyfél nagyobb méretű oldalméretet kér, mint az Mobile Apps háttérrendszer által támogatott, az oldal mérete meghaladja a maximálisan támogatott hátteret.

Ez a beállítás az adatrekordok *száma* is, nem pedig a *bájtok mérete*.

Ha megnövelte az ügyfél oldalának méretét, növelje az oldalméret méretét is a kiszolgálón. Tekintse [meg a "How to: Módosítsa a tábla lapozófájljának méretét](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) "a lépések végrehajtásához.

**Objective-C**:

```objc
  MSPullSettings *pullSettings = [[MSPullSettings alloc] initWithPageSize:3];
  [table  pullWithQuery:query queryId:@nil settings:pullSettings
                        completion:^(NSError * _Nullable error) {
                               if(error) {
                    NSLog(@"ERROR %@", error);
                }
                           }];
```

**Swift**:

```swift
let pullSettings = MSPullSettings(pageSize: 3)
table.pullWithQuery(query, queryId:nil, settings: pullSettings) { (error) in
    if let err = error {
        print("ERROR ", err)
    }
}
```

## <a name="inserting"></a>kézikönyv: Adatbeszúrás

Új táblázat beszúrásához hozzon létre egy `NSDictionary` és meghívást. `table insert` Ha engedélyezve van a [dinamikus séma] , a Azure app Service Mobile háttérrendszer automatikusan új oszlopokat hoz `NSDictionary`létre a alapján.

Ha `id` a nincs megadva, a háttérrendszer automatikusan létrehoz egy új egyedi azonosítót. Adja meg a `id` saját e-mail-címek, felhasználónevek vagy a saját egyéni értékek azonosítóként való használatát. A saját azonosító megadásával könnyedén összekapcsolhatja az illesztéseket és az üzleti célú adatbázis-logikát.

A `result` tartalmazza a beszúrt új elemeket. A kiszolgálói logikától függően előfordulhat, hogy további vagy módosult adatai vannak a kiszolgálónak továbbított értékekhez képest.

**Objective-C**:

```objc
NSDictionary *newItem = @{@"id": @"custom-id", @"text": @"my new item", @"complete" : @NO};
[table insert:newItem completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**Swift**:

```swift
let newItem = ["id": "custom-id", "text": "my new item", "complete": false]
table.insert(newItem) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let item = result {
        print("Todo Item: ", item["text"])
    }
}
```

## <a name="modifying"></a>kézikönyv: Az adatmódosítás

Meglévő sor frissítéséhez módosítsa az elemeket, és hívja `update`meg a következőt:

**Objective-C**:

```objc
NSMutableDictionary *newItem = [oldItem mutableCopy]; // oldItem is NSDictionary
[newItem setValue:@"Updated text" forKey:@"text"];
[table update:newItem completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**Swift**:

```swift
if let newItem = oldItem.mutableCopy() as? NSMutableDictionary {
    newItem["text"] = "Updated text"
    table2.update(newItem as [NSObject: AnyObject], completion: { (result, error) -> Void in
        if let err = error {
            print("ERROR ", err)
        } else if let item = result {
            print("Todo Item: ", item["text"])
        }
    })
}
```

Azt is megteheti, hogy megadja a sor AZONOSÍTÓját és a frissített mezőt:

**Objective-C**:

```objc
[table update:@{@"id":@"custom-id", @"text":"my EDITED item"} completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**Swift**:

```swift
table.update(["id": "custom-id", "text": "my EDITED item"]) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let item = result {
        print("Todo Item: ", item["text"])
    }
}
```

Legalább az attribútumot `id` be kell állítani a frissítések elkészítésekor.

## <a name="deleting"></a>kézikönyv: Adattörlés

Egy elem törléséhez hívja `delete` meg a következőt:

**Objective-C**:

```objc
[table delete:item completion:^(id itemId, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item ID: %@", itemId);
    }
}];
```

**Swift**:

```swift
table.delete(newItem as [NSObject: AnyObject]) { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

Azt is megteheti, hogy egy sor azonosítót biztosít:

**Objective-C**:

```objc
[table deleteWithId:@"37BBF396-11F0-4B39-85C8-B319C729AF6D" completion:^(id itemId, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item ID: %@", itemId);
    }
}];
```

**Swift**:

```swift
table.deleteWithId("37BBF396-11F0-4B39-85C8-B319C729AF6D") { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

A törléshez legalább `id` az attribútumot be kell állítani.

## <a name="customapi"></a>kézikönyv: Egyéni API hívása

Az egyéni API-k segítségével bármilyen háttérbeli funkció elérhetővé teheti. Nem kell táblázatos műveletre leképezni. Nem csak az üzenetkezelés nagyobb mértékű szabályozására van lehetőség, de a fejléceket is olvashatja/beállíthatja, és módosíthatja a válasz törzsének formátumát.

Egyéni API meghívásához hívja `MSClient.invokeAPI`a következőt:. A kérelem és a válasz tartalma JSON-ként van kezelve. Más adathordozó-típusok használatához [használja a másik túlterhelését `invokeAPI` ][5].  `HTTPMethod` Kérelemhelyett`nil` a paramétert ésa`body` paramétert állítsa be (mivel a Get kérelmek nem rendelkeznek üzenet törzsével). `"GET"` `GET` `POST` Ha az egyéni API más http-műveleteket is támogat, `HTTPMethod` módosítsa a megfelelőt.

**Objective-C**:

```objc
[self.client invokeAPI:@"sendEmail"
                  body:@{ @"contents": @"Hello world!" }
            HTTPMethod:@"POST"
            parameters:@{ @"to": @"bill@contoso.com", @"subject" : @"Hi!" }
               headers:nil
            completion: ^(NSData *result, NSHTTPURLResponse *response, NSError *error) {
                if(error) {
                    NSLog(@"ERROR %@", error);
                } else {
                    // Do something with result
                }
            }];
```

**Swift**:

```swift
client.invokeAPI("sendEmail",
            body: [ "contents": "Hello World" ],
            HTTPMethod: "POST",
            parameters: [ "to": "bill@contoso.com", "subject" : "Hi!" ],
            headers: nil)
            {
                (result, response, error) -> Void in
                if let err = error {
                    print("ERROR ", err)
                } else if let res = result {
                          // Do something with result
                }
        }
```

## <a name="templates"></a>kézikönyv: Leküldéses sablonok regisztrálása platformfüggetlen értesítések küldéséhez

A sablonok regisztrálásához adjon át sablonokat a **Client. push registerDeviceToken** metódussal az ügyfélalkalmazás számára.

**Objective-C**:

```objc
[client.push registerDeviceToken:deviceToken template:iOSTemplate completion:^(NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    }
}];
```

**Swift**:

```swift
client.push?.registerDeviceToken(NSData(), template: iOSTemplate, completion: { (error) in
    if let err = error {
        print("ERROR ", err)
    }
})
```

A sablonok NSDictionary típusúak, és több sablont is tartalmazhatnak a következő formátumban:

**Objective-C**:

```objc
NSDictionary *iOSTemplate = @{ @"templateName": @{ @"body": @{ @"aps": @{ @"alert": @"$(message)" } } } };
```

**Swift**:

```swift
let iOSTemplate = ["templateName": ["body": ["aps": ["alert": "$(message)"]]]]
```

Az összes címke el lett távolítva a biztonsági kérésből.  Ha címkéket szeretne hozzáadni a telepítésekhez vagy sablonokhoz a telepítések között, tekintse meg a következőt: [Az Azure-hoz készült .net backend Server SDK használata Mobile apps][4]  Ha ezekkel a regisztrált sablonokkal szeretne értesítéseket küldeni, használja a [Notification Hubs API-kat][3].

## <a name="errors"></a>kézikönyv: Hibák kezelése

Ha Azure app Service mobil hátteret hív meg, a befejezési blokk egy `NSError` paramétert tartalmaz. Hiba esetén ez a paraméter nem üres. A kódban tekintse meg ezt a paramétert, és szükség szerint kezelje a hibát az előző kódrészletekben bemutatott módon.

A fájl [`<WindowsAzureMobileServices/MSError.h>`][6] meghatározza a `MSErrorResponseKey`konstansokat, `MSErrorRequestKey`és `MSErrorServerItemKey`. További információ a hibával kapcsolatban:

**Objective-C**:

```objc
NSDictionary *serverItem = [error.userInfo objectForKey:MSErrorServerItemKey];
```

**Swift**:

```swift
let serverItem = error.userInfo[MSErrorServerItemKey]
```

Emellett a fájl az egyes hibakódok konstansait is meghatározza:

**Objective-C**:

```objc
if (error.code == MSErrorPreconditionFailed) {
```

**Swift**:

```swift
if (error.code == MSErrorPreconditionFailed) {
```

## <a name="adal"></a>kézikönyv: Felhasználók hitelesítése a Active Directory-hitelesítési tár

A Active Directory-hitelesítési tár (ADAL) használatával a felhasználókat a Azure Active Directory használatával lehet az alkalmazásba írni. Az ügyfél-adatforgalom hitelesítése az Identity Provider SDK használatával ajánlott a `loginWithProvider:completion:` metódus használatára.  Az ügyfél-átfolyásos hitelesítés több natív UX-érzést biztosít, és lehetővé teszi a további testreszabást.

1. A HRE-bejelentkezéshez a [app Service konfigurálása Active Directory bejelentkezési][7] oktatóanyagban című témakörben található. Győződjön meg arról, hogy a natív ügyfélalkalmazás regisztrálásának nem kötelező lépéseit kell végrehajtania. Az iOS esetében javasoljuk, hogy az átirányítási URI legyen az űrlap `<app-scheme>://<bundle-id>`. További információ: [ADAL iOS][8]gyors üzembe helyezése.
2. Telepítse a ADAL-t a Cocoapods használatával. Szerkessze a Cocoapods úgy, hogy az tartalmazza a következő definíciót, és cserélje **le a projektet** a Xcode-projekt nevére:

        source 'https://github.com/CocoaPods/Specs.git'
        link_with ['YOUR-PROJECT']
        xcodeproj 'YOUR-PROJECT'

   és a pod:

        pod 'ADALiOS'

3. A terminál használatával futtassa `pod install` a projektet tartalmazó könyvtárat, majd nyissa meg a létrehozott Xcode-munkaterületet (nem a projektből).
4. Adja hozzá a következő kódot az alkalmazáshoz a használt nyelv alapján. Mindkét esetben végezze el a következő cseréket:

   * Cserélje le a **Insert-Authority-here** nevet annak a bérlőnek a nevére, amelyben az alkalmazást kiépítte. A formátumnak a https://login.microsoftonline.com/contoso.onmicrosoft.com értéknek kell lennie. Ez az érték a [Azure Portal]Azure Active Directory tartomány lapjáról másolható.
   * Cserélje le a **Insert-Resource-id-** t a Mobile apps-háttér ügyfél-azonosítójával. Az ügyfél-azonosítót a portál **Azure Active Directory beállítások** területén található **speciális** lapon szerezheti be.
   * Cserélje le az **Insert-Client-ID-** t a natív ügyfélalkalmazás által másolt ügyfél-azonosítóra.
   * Cserélje le a **Insert-redirect-URI-t – itt** a hely */.auth/login/Done* -végpontján a https-séma használatával. Az értéknek a *https://contoso.azurewebsites.net/.auth/login/done* következőhöz hasonlónak kell lennie:.

**Objective-C**:

```objc
#import <ADALiOS/ADAuthenticationContext.h>
#import <ADALiOS/ADAuthenticationSettings.h>
// ...
- (void) authenticate:(UIViewController*) parent
            completion:(void (^) (MSUser*, NSError*))completionBlock;
{
    NSString *authority = @"INSERT-AUTHORITY-HERE";
    NSString *resourceId = @"INSERT-RESOURCE-ID-HERE";
    NSString *clientId = @"INSERT-CLIENT-ID-HERE";
    NSURL *redirectUri = [[NSURL alloc]initWithString:@"INSERT-REDIRECT-URI-HERE"];
    ADAuthenticationError *error;
    ADAuthenticationContext *authContext = [ADAuthenticationContext authenticationContextWithAuthority:authority error:&error];
    authContext.parentController = parent;
    [ADAuthenticationSettings sharedInstance].enableFullScreen = YES;
    [authContext acquireTokenWithResource:resourceId
                                    clientId:clientId
                                redirectUri:redirectUri
                            completionBlock:^(ADAuthenticationResult *result) {
                                if (result.status != AD_SUCCEEDED)
                                {
                                    completionBlock(nil, result.error);;
                                }
                                else
                                {
                                    NSDictionary *payload = @{
                                                            @"access_token" : result.tokenCacheStoreItem.accessToken
                                                            };
                                    [client loginWithProvider:@"aad" token:payload completion:completionBlock];
                                }
                            }];
}
```

**Swift**:

```swift
// add the following imports to your bridging header:
//        #import <ADALiOS/ADAuthenticationContext.h>
//        #import <ADALiOS/ADAuthenticationSettings.h>

func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
    let authority = "INSERT-AUTHORITY-HERE"
    let resourceId = "INSERT-RESOURCE-ID-HERE"
    let clientId = "INSERT-CLIENT-ID-HERE"
    let redirectUri = NSURL(string: "INSERT-REDIRECT-URI-HERE")
    var error: AutoreleasingUnsafeMutablePointer<ADAuthenticationError?> = nil
    let authContext = ADAuthenticationContext(authority: authority, error: error)
    authContext.parentController = parent
    ADAuthenticationSettings.sharedInstance().enableFullScreen = true
    authContext.acquireTokenWithResource(resourceId, clientId: clientId, redirectUri: redirectUri) { (result) in
            if result.status != AD_SUCCEEDED {
                completion(nil, result.error)
            }
            else {
                let payload: [String: String] = ["access_token": result.tokenCacheStoreItem.accessToken]
                client.loginWithProvider("aad", token: payload, completion: completion)
            }
        }
}
```

## <a name="facebook-sdk"></a>kézikönyv: Felhasználók hitelesítése az iOS-hez készült Facebook SDK-val

Az iOS-hez készült Facebook SDK használatával a felhasználók a Facebook használatával jelentkezhetnek be az alkalmazásba.  Az ügyfél-átfolyásos hitelesítés használata előnyösebb a `loginWithProvider:completion:` metódus használatával.  Az ügyfél flow-hitelesítése több natív UX-élményt nyújt, és lehetővé teszi a további testreszabást.

1. A Facebook-bejelentkezéshez a [app Service konfigurálása a Facebook-bejelentkezési][9] oktatóanyaghoz című témakörben bemutatjuk, hogyan konfigurálhatja a Facebook-bejelentkezési hátteret.
2. Telepítse az iOS-hez készült Facebook SDK-t a [Facebook SDK for iOS – első lépések][10] dokumentációjában. Az alkalmazások létrehozása helyett hozzáadhatja az iOS platformot a meglévő regisztrációhoz.
3. A Facebook dokumentációja tartalmaz néhány Objective-C kódot az alkalmazás delegált részében. Ha **Swift**-t használ, a következő fordításokat használhatja a AppDelegate. Swift-hez:

    ```swift
    // Add the following import to your bridging header:
    //        #import <FBSDKCoreKit/FBSDKCoreKit.h>

    func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject : AnyObject]?) -> Bool {
        FBSDKApplicationDelegate.sharedInstance().application(application, didFinishLaunchingWithOptions: launchOptions)
        // Add any custom logic here.
        return true
    }

    func application(application: UIApplication, openURL url: NSURL, sourceApplication: String?, annotation: AnyObject?) -> Bool {
        let handled = FBSDKApplicationDelegate.sharedInstance().application(application, openURL: url, sourceApplication: sourceApplication, annotation: annotation)
        // Add any custom logic here.
        return handled
    }
    ```
4. A projekthez való `FBSDKCoreKit.framework` Hozzáadás mellett a hivatkozásokat is hozzá kell `FBSDKLoginKit.framework` adnia a hasonló módon.
5. Adja hozzá a következő kódot az alkalmazáshoz a használt nyelv alapján.

    **Objective-C**:

    ```objc
    #import <FBSDKLoginKit/FBSDKLoginKit.h>
    #import <FBSDKCoreKit/FBSDKAccessToken.h>
    // ...
    - (void) authenticate:(UIViewController*) parent
                completion:(void (^) (MSUser*, NSError*)) completionBlock;
    {
        FBSDKLoginManager *loginManager = [[FBSDKLoginManager alloc] init];
        [loginManager
            logInWithReadPermissions: @[@"public_profile"]
            fromViewController:parent
            handler:^(FBSDKLoginManagerLoginResult *result, NSError *error) {
                if (error) {
                    completionBlock(nil, error);
                } else if (result.isCancelled) {
                    completionBlock(nil, error);
                } else {
                    NSDictionary *payload = @{
                                            @"access_token":result.token.tokenString
                                            };
                    [client loginWithProvider:@"facebook" token:payload completion:completionBlock];
                }
            }];
    }
    ```

    **Swift**:

    ```swift
    // Add the following imports to your bridging header:
    //        #import <FBSDKLoginKit/FBSDKLoginKit.h>
    //        #import <FBSDKCoreKit/FBSDKAccessToken.h>

    func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
        let loginManager = FBSDKLoginManager()
        loginManager.logInWithReadPermissions(["public_profile"], fromViewController: parent) { (result, error) in
            if (error != nil) {
                completion(nil, error)
            }
            else if result.isCancelled {
                completion(nil, error)
            }
            else {
                let payload: [String: String] = ["access_token": result.token.tokenString]
                client.loginWithProvider("facebook", token: payload, completion: completion)
            }
        }
    }
    ```

## <a name="twitter-fabric"></a>kézikönyv: Felhasználók hitelesítése az iOS-hez készült Twitter-hálóval

Az iOS-es háló használatával a felhasználók a Twitter használatával jelentkezhetnek be az alkalmazásba. Az ügyfél-átfolyásos hitelesítés előnyben `loginWithProvider:completion:` részesített a metódus használatára, mivel ez egy több natív UX-élményt nyújt, és lehetővé teszi a további testreszabást.

1. A Twitter-bejelentkezéshez a [app Service konfigurálása a Twitter](../app-service/configure-authentication-provider-twitter.md) -bejelentkezéshez oktatóanyagot követve konfigurálhatja a Mobile apps-háttért a Twitter-bejelentkezéshez.
2. Adja hozzá a hálót a projekthez az [Az iOS-hez készült háló – Első lépések] dokumentációját és a TwitterKit beállítását követve.

   > [!NOTE]
   > Alapértelmezés szerint a Fabric létrehoz egy Twitter-alkalmazást. Az alábbi kódrészletek használatával elkerülhető az alkalmazás létrehozása a korábban létrehozott fogyasztói kulcs és fogyasztói titok regisztrálásával.    Azt is megteheti, hogy lecseréli az Ön által megadott fogyasztói kulcsot és a fogyasztói titkos értékeket a [Háló irányítópult]megjelenő értékekre app Service. Ha ezt a beállítást választja, ügyeljen arra, hogy a visszahívási URL-címet egy helyőrző értékre állítsa be, például `https://<yoursitename>.azurewebsites.net/.auth/login/twitter/callback`:.

    Ha úgy dönt, hogy a korábban létrehozott titkokat használja, adja hozzá a következő kódot az alkalmazás delegált számára:

    **Objective-C**:

    ```objc
    #import <Fabric/Fabric.h>
    #import <TwitterKit/TwitterKit.h>
    // ...
    - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
    {
        [[Twitter sharedInstance] startWithConsumerKey:@"your_key" consumerSecret:@"your_secret"];
        [Fabric with:@[[Twitter class]]];
        // Add any custom logic here.
        return YES;
    }
    ```

    **Swift**:

    ```swift
    import Fabric
    import TwitterKit
    // ...
    func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject : AnyObject]?) -> Bool {
        Twitter.sharedInstance().startWithConsumerKey("your_key", consumerSecret: "your_secret")
        Fabric.with([Twitter.self])
        // Add any custom logic here.
        return true
    }
    ```

3. Adja hozzá a következő kódot az alkalmazáshoz a használt nyelv alapján.

    **Objective-C**:

    ```objc
    #import <TwitterKit/TwitterKit.h>
    // ...
    - (void)authenticate:(UIViewController*)parent completion:(void (^) (MSUser*, NSError*))completionBlock
    {
        [[Twitter sharedInstance] logInWithCompletion:^(TWTRSession *session, NSError *error) {
            if (session) {
                NSDictionary *payload = @{
                                            @"access_token":session.authToken,
                                            @"access_token_secret":session.authTokenSecret
                                        };
                [client loginWithProvider:@"twitter" token:payload completion:completionBlock];
            } else {
                completionBlock(nil, error);
            }
        }];
    }
    ```

    **Swift**:

    ```swift
    import TwitterKit
    // ...
    func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
        let client = self.table!.client
        Twitter.sharedInstance().logInWithCompletion { session, error in
            if (session != nil) {
                let payload: [String: String] = ["access_token": session!.authToken, "access_token_secret": session!.authTokenSecret]
                client.loginWithProvider("twitter", token: payload, completion: completion)
            } else {
                completion(nil, error)
            }
        }
    }
    ```

## <a name="google-sdk"></a>kézikönyv: Felhasználók hitelesítése az iOS-hez készült Google bejelentkezési SDK-val

Az iOS-hez készült Google bejelentkezési SDK-val a felhasználók Google-fiókkal való bejelentkezését is elvégezheti az alkalmazásba.  A Google nemrég bejelentette a OAuth biztonsági szabályzatok módosításait.  A szabályzat módosításaihoz a jövőben a Google SDK használatára lesz szükség.

1. Konfigurálja a Google bejelentkezéshez készült Mobile apps-hátteret a [app Service konfigurálása a Google bejelentkezési](../app-service/configure-authentication-provider-google.md) oktatóanyaghoz című témakörben ismertetett módon.
2. Telepítse az iOS-hez készült Google SDK-t az [iOS-hez készült Google-bejelentkezéssel –](https://developers.google.com/identity/sign-in/ios/start-integrating) az integrációs dokumentáció beírásával. Kihagyhatja a "hitelesítés a háttér-kiszolgálóval" szakaszt.
3. Adja hozzá a következőt a delegált `signIn:didSignInForUser:withError:` metódushoz az Ön által használt nyelvnek megfelelően.

    **Objective-C**:
    ```objc
    NSDictionary *payload = @{
                                @"id_token":user.authentication.idToken,
                                @"authorization_code":user.serverAuthCode
                                };

    [client loginWithProvider:@"google" token:payload completion:^(MSUser *user, NSError *error) {
        // ...
    }];
    ```

    **Swift**:

    ```swift
    let payload: [String: String] = ["id_token": user.authentication.idToken, "authorization_code": user.serverAuthCode]
    client.loginWithProvider("google", token: payload) { (user, error) in
        // ...
    }
    ```

4. Győződjön meg arról, hogy a következőt `application:didFinishLaunchingWithOptions:` is hozzáadja az alkalmazás delegált eleméhez, és a "SERVER_CLIENT_ID" helyett ugyanazzal az azonosítóval, amelyet az 1. lépésben a app Service konfigurálásához használt.

    **Objective-C**:

    ```objc
    [GIDSignIn sharedInstance].serverClientID = @"SERVER_CLIENT_ID";
    ```

     **Swift**:

    ```swift
    GIDSignIn.sharedInstance().serverClientID = "SERVER_CLIENT_ID"
    ```

5. Adja hozzá a következő kódot az alkalmazáshoz egy olyan UIViewController, amely megvalósítja a `GIDSignInUIDelegate` protokollt, a használt nyelvnek megfelelően.  Mielőtt újra bejelentkezett, és még nem kell újra megadnia a hitelesítő adatait, megjelenik egy beleegyező párbeszédpanel.  Ezt a metódust csak akkor hívja meg, ha a munkamenet-jogkivonat lejárt.

   **Objective-C**:

    ```objc
    #import <Google/SignIn.h>
    // ...
    - (void)authenticate
    {
            [GIDSignIn sharedInstance].uiDelegate = self;
            [[GIDSignIn sharedInstance] signOut];
            [[GIDSignIn sharedInstance] signIn];
    }
    ```

   **Swift**:

    ```swift
    // ...
    func authenticate() {
        GIDSignIn.sharedInstance().uiDelegate = self
        GIDSignIn.sharedInstance().signOut()
        GIDSignIn.sharedInstance().signIn()
    }
    ```

<!-- Anchors. -->

[What is Mobile Services]: #what-is
[Concepts]: #concepts
[Setup and Prerequisites]: #Setup
[How to: Create the Mobile Services client]: #create-client
[How to: Create a table reference]: #table-reference
[How to: Query data from a mobile service]: #querying
[Filter returned data]: #filtering
[Sort returned data]: #sorting
[Return data in pages]: #paging
[Select specific columns]: #selecting
[How to: Bind data to the user interface]: #binding
[How to: Insert data into a mobile service]: #inserting
[How to: Modify data in a mobile service]: #modifying
[How to: Authenticate users]: #authentication
[Cache authentication tokens]: #caching-tokens
[How to: Upload images and large files]: #blobs
[How to: Handle errors]: #errors
[How to: Design unit tests]: #unit-testing
[How to: Customize the client]: #customizing
[Customize request headers]: #custom-headers
[Customize data type serialization]: #custom-serialization
[Next Steps]: #next-steps
[How to: Use MSQuery]: #query-object

<!-- Images. -->

<!-- URLs. -->
[Azure Mobile Apps gyorskonfigurálás]: app-service-mobile-ios-get-started.md

[Add Mobile Services to Existing App]: /develop/mobile/tutorials/get-started-data
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Validate and modify data in Mobile Services by using server scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-ios
[Mobile Services SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[iOS SDK]: https://developer.apple.com/xcode
[Azure Portal]: https://portal.azure.com/
[Handling Expired Tokens]: https://go.microsoft.com/fwlink/p/?LinkId=301955
[Live Connect SDK]: https://go.microsoft.com/fwlink/p/?LinkId=301960
[Permissions]: https://msdn.microsoft.com/library/windowsazure/jj193161.aspx
[Service-side Authorization]: mobile-services-javascript-backend-service-side-authorization.md
[Use scripts to authorize users]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[Dinamikus séma]: https://go.microsoft.com/fwlink/p/?LinkId=296271
[How to: access custom parameters]: /develop/mobile/how-to-guides/work-with-server-scripts#access-headers
[Create a table]: https://msdn.microsoft.com/library/windowsazure/jj193162.aspx
[NSDictionary object]: https://go.microsoft.com/fwlink/p/?LinkId=301965
[ASCII control codes C0 and C1]: https://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI to manage Mobile Services tables]: /cli/azure/get-started-with-az-cli2
[Conflict-Handler]: mobile-services-ios-handling-conflicts-offline-data.md#add-conflict-handling

[Háló irányítópult]: https://www.fabric.io/home
[Az iOS-hez készült háló – Első lépések]: https://docs.fabric.io/ios/fabric/getting-started.html
[1]: https://github.com/Azure/azure-mobile-apps-ios-client/blob/master/README.md#ios-client-sdk
[2]: https://azure.github.io/azure-mobile-apps-ios-client/
[3]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[4]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags
[5]: https://azure.github.io/azure-mobile-services/iOS/v3/Classes/MSClient.html#//api/name/invokeAPI:data:HTTPMethod:parameters:headers:completion:
[6]: https://github.com/Azure/azure-mobile-services/blob/master/sdk/iOS/src/MSError.h
[7]: ../app-service/configure-authentication-provider-aad.md
[8]:../active-directory/develop/quickstart-v1-ios.md
[9]: ../app-service/configure-authentication-provider-facebook.md
[10]: https://developers.facebook.com/docs/ios/getting-started
