---
title: Leküldéses értesítések adott iOS-eszközökre az Azure Notification Hubs használatával | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan küldhet leküldéses értesítéseket az Azure Notification Hubs használatával bizonyos iOS-eszközökre.
services: notification-hubs
documentationcenter: ios
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 6ead4169-deff-4947-858c-8c6cf03cc3b2
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 07/28/2019
ms.author: jowargo
ms.openlocfilehash: f83afa62859dee5963749daf2555af08cf6a0e0b
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68663827"
---
# <a name="tutorial-push-notifications-to-specific-ios-devices-using-azure-notification-hubs"></a>Oktatóanyag: Leküldéses értesítések küldése adott iOS-eszközökre az Azure Notification Hubs

[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>Áttekintés

Ebből az oktatóanyagból megtudhatja, hogyan használhatja az Azure Notification Hubst a legfrissebb híreket használó értesítések küldéséhez iOS-alkalmazásokban. Ha elkészült, regisztrálhatja az Önt érdeklő híreket, és csak leküldéses értesítéseket kaphat ezekre a kategóriákra vonatkozóan. Ezt a forgatókönyvet gyakran használják olyan alkalmazásokban, ahol az értesítéseket egy adott téma iránt korábban érdeklődő felhasználók csoportjainak kell elküldeni. Ilyen lehet például egy RSS-olvasó, a zenerajongóknak készült alkalmazások stb.

A közvetítési forgatókönyveket úgy lehet engedélyezni, ha az értesítési központban a regisztráció létrehozásakor hozzáad egy vagy több *címkét*. Ha az értesítéseket egy címkére küldi, a címkére regisztrált eszközök megkapják az értesítést. A címkék egyszerű sztringek, nem szükséges őket előzetesen kiosztani. További információ a címkékről: [Útválasztás és címkekifejezések az értesítési központokban](notification-hubs-tags-segment-push-message.md).

Ebben az oktatóanyagban a következő lépéseket hajtja végre:

> [!div class="checklist"]
> * Kategória kijelölésének hozzáadása az alkalmazáshoz
> * Címkézett értesítések küldése
> * Értesítések küldése az eszközről
> * Az alkalmazás futtatása és értesítések létrehozása

## <a name="prerequisites"></a>Előfeltételek

Ez a témakör az [oktatóanyagban létrehozott alkalmazásra épül. Leküldéses értesítések iOS-alkalmazásokhoz][get-started]az Azure Notification Hubs használatával. Az oktatóanyag elindítása előtt el kell végeznie [az oktatóanyagot: Leküldéses értesítések iOS-alkalmazásokhoz][get-started]az Azure Notification Hubs használatával.

## <a name="add-category-selection-to-the-app"></a>Kategóriaválasztó hozzáadása az alkalmazáshoz

Első lépésként adja hozzá a felhasználói FELÜLETi elemeket a meglévő történethez, amely lehetővé teszi, hogy a felhasználó kiválassza a regisztrálni kívánt kategóriákat. A felhasználó által kiválasztott kategóriákat az eszköz tárolja. Az alkalmazás indításakor egy eszközregisztráció jön létre az értesítési központban, amely címkeként tartalmazza a választott kategóriákat.

1. A **MainStoryboard_iPhone. storyboardban** adja hozzá a következő összetevőket az objektum-tárból:

   * Egy címke "Breaking News" szöveggel,
   * A "világ", "Politics", "Business", "Technology", "Science", "sport" kategóriába tartozó címkéket,
   * Hat kapcsoló, egy kategóriánként, az egyes kapcsolók **állapotának** beállítása alapértelmezés szerint kikapcsolható.
   * Egy gomb a "subscribe" címkével

     A történetnek a következőképpen kell kinéznie:

     ![Xcode Interface Builder][3]

2. A Segéd-szerkesztőben hozzon létre kivezetést az összes kapcsolóhoz, és hívja meg őket "WorldSwitch", "PoliticsSwitch", "BusinessSwitch", "TechnologySwitch", "ScienceSwitch", "SportsSwitch"
3. Hozzon létre egy műveletet az nevű `subscribe`gombhoz `ViewController.h` ; a következő kódot kell tartalmaznia:

    ```objc
    @property (weak, nonatomic) IBOutlet UISwitch *WorldSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *PoliticsSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *BusinessSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *TechnologySwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *ScienceSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *SportsSwitch;

    - (IBAction)subscribe:(id)sender;
    ```

4. Hozzon létre egy új, nevű `Notifications` **kakaó Touch osztályt** . Másolja az alábbi kódot a fájl értesítéseinek felület szakaszában. h:

    ```objc
    @property NSData* deviceToken;

    - (id)initWithConnectionString:(NSString*)listenConnectionString HubName:(NSString*)hubName;

    - (void)storeCategoriesAndSubscribeWithCategories:(NSArray*)categories
                completion:(void (^)(NSError* error))completion;

    - (NSSet*)retrieveCategories;

    - (void)subscribeWithCategories:(NSSet*)categories completion:(void (^)(NSError *))completion;
    ```

5. Adja hozzá az alábbi importálási irányelveket az értesítésekhez. m:

    ```objc
    #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
    ```

6. Másolja a következő kódot a fájl értesítéseinek implementáció szakaszában. m.

    ```objc
    SBNotificationHub* hub;

    - (id)initWithConnectionString:(NSString*)listenConnectionString HubName:(NSString*)hubName{

        hub = [[SBNotificationHub alloc] initWithConnectionString:listenConnectionString
                                    notificationHubPath:hubName];

        return self;
    }

    - (void)storeCategoriesAndSubscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion {
        NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
        [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];

        [self subscribeWithCategories:categories completion:completion];
    }

    - (NSSet*)retrieveCategories {
        NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

        NSArray* categories = [defaults stringArrayForKey:@"BreakingNewsCategories"];

        if (!categories) return [[NSSet alloc] init];
        return [[NSSet alloc] initWithArray:categories];
    }

    - (void)subscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion
    {
        //[hub registerNativeWithDeviceToken:self.deviceToken tags:categories completion: completion];

        NSString* templateBodyAPNS = @"{\"aps\":{\"alert\":\"$(messageParam)\"}}";

        [hub registerTemplateWithDeviceToken:self.deviceToken name:@"simpleAPNSTemplate" 
            jsonBodyTemplate:templateBodyAPNS expiryTemplate:@"0" tags:categories completion:completion];
    }
    ```

    Ez az osztály helyi tárterületet használ az eszköz által fogadott Hírek kategóriáinak tárolásához és lekéréséhez. Emellett egy olyan módszert is tartalmaz, amellyel regisztrálhatja ezeket a kategóriákat a [sablon](notification-hubs-templates-cross-platform-push-messages.md) regisztrációjának használatával.

7. A `AppDelegate.h` fájlban adjon hozzá egy importálási `Notifications.h` utasítást, és adjon hozzá egy tulajdonságot a `Notifications` osztály egy példányához:

    ```objc
    #import "Notifications.h"

    @property (nonatomic) Notifications* notifications;
    ```

8. `didFinishLaunchingWithOptions` A`AppDelegate.m`metódusában adja hozzá a kódot az értesítési példány inicializálásához a metódus elején.  
    `HUBNAME`és `HUBLISTENACCESS` (a ben `hubinfo.h`definiálva) már rendelkezik `<hub name>` a `<connection string with listen access>` és a helyőrzővel az értesítési központ nevével és a beszerzett *DefaultListenSharedAccessSignature* tartozó kapcsolatok karakterláncával. korábbi

    ```objc
    self.notifications = [[Notifications alloc] initWithConnectionString:HUBLISTENACCESS HubName:HUBNAME];
    ```

    > [!NOTE]
    > Mivel az ügyfélalkalmazással terjesztett hitelesítő adatok általában nem biztonságosak, csak a figyelési hozzáférés kulcsát terjessze az ügyfélalkalmazással. A figyelési hozzáférés lehetővé teszi, hogy az alkalmazás regisztráljon értesítésekre, a meglévő regisztrációkat azonban nem lehet módosítani, és értesítéseket sem lehet küldeni. A teljes körű hozzáférési kulcsot egy biztonságos háttérszolgáltatásban használja a rendszer értesítések kiküldésére és a meglévő regisztrációk módosítására.

9. `didRegisterForRemoteNotificationsWithDeviceToken` A `notifications` metódusában cseréljeleametóduskódjátakövetkezőkódra,hogyátadjaazeszközjogkivonatát`AppDelegate.m`az osztálynak. Az `notifications` osztály az értesítések regisztrálását végzi a kategóriákkal. Ha a felhasználó megváltoztatja a kategória kiválasztását `subscribeWithCategories` , akkor az **előfizetés** gombra válaszul hívja meg a metódust a frissítéshez.

    > [!NOTE]
    > Mivel az Apple Push Notification Service (APNS) által hozzárendelt eszköz-token bármikor lehetséges, az értesítési hibák elkerülése érdekében gyakran regisztrálnia kell az értesítéseket. Ebben a példában a rendszer az alkalmazás minden egyes indításakor regisztrál az értesítésekre. A gyakran, naponta egynél többször futtatott alkalmazások esetén a sávszélesség megőrzése érdekében akár ki is hagyhatja a regisztrációt, ha kevesebb mint egy nap telt el az előző regisztráció óta.

    ```objc
    self.notifications.deviceToken = deviceToken;

    // Retrieves the categories from local storage and requests a registration for these categories
    // each time the app starts and performs a registration.

    NSSet* categories = [self.notifications retrieveCategories];
    [self.notifications subscribeWithCategories:categories completion:^(NSError* error) {
        if (error != nil) {
            NSLog(@"Error registering for notifications: %@", error);
        }
    }];
    ```

    Ezen a ponton nem lehet más kód a `didRegisterForRemoteNotificationsWithDeviceToken` metódusban.

10. A következő módszereknek már be kell jelentkezniük `AppDelegate.m` az első [lépések a Notification Hubs][get-started] oktatóanyagban című témakörben. Ha nem, adja hozzá őket.

    ```objc
    - (void)MessageBox:(NSString *)title message:(NSString *)messageText
    {

        UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
            cancelButtonTitle:@"OK" otherButtonTitles: nil];
        [alert show];
    }

    - (void)application:(UIApplication *)application didReceiveRemoteNotification:
       (NSDictionary *)userInfo {
       NSLog(@"%@", userInfo);
       [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
     }
    ```

    Ez a metódus egy egyszerű **UIAlert**megjelenítésével kezeli az alkalmazás futásakor fogadott értesítéseket.

11. A `ViewController.m`alkalmazásban adjon `import` hozzá egy `AppDelegate.h` utasítást, és másolja a következő kódot a Xcode által `subscribe` generált metódusba. Ez a kód frissíti az értesítés regisztrációját, hogy az új kategória címkét használja, amelyet a felhasználó a felhasználói felületen kiválasztott.

    ```objc
    #import "Notifications.h"

    NSMutableArray* categories = [[NSMutableArray alloc] init];

    if (self.WorldSwitch.isOn) [categories addObject:@"World"];
    if (self.PoliticsSwitch.isOn) [categories addObject:@"Politics"];
    if (self.BusinessSwitch.isOn) [categories addObject:@"Business"];
    if (self.TechnologySwitch.isOn) [categories addObject:@"Technology"];
    if (self.ScienceSwitch.isOn) [categories addObject:@"Science"];
    if (self.SportsSwitch.isOn) [categories addObject:@"Sports"];

    Notifications* notifications = [(AppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

    [notifications storeCategoriesAndSubscribeWithCategories:categories completion: ^(NSError* error) {
        if (!error) {
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:"Notification" message:"Subscribed" delegate:self
            cancelButtonTitle:@"OK" otherButtonTitles: nil];
            [alert show];
        } else {
            NSLog(@"Error subscribing: %@", error);
        }
    }];
    ```

    Ez a metódus létrehoz `NSMutableArray` egy kategóriát, és `Notifications` a osztály használatával tárolja a listát a helyi tárolóban, és regisztrálja a megfelelő címkéket az értesítési központban. A kategóriák módosításakor a rendszer újra létrehozza a regisztrációt az új kategóriákkal.

12. A `ViewController.m`alkalmazásban adja hozzá a következő kódot `viewDidLoad` a metódushoz a felhasználói felület a korábban mentett kategóriák alapján történő beállításához.

    ```objc
    // This updates the UI on startup based on the status of previously saved categories.

    Notifications* notifications = [(AppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

    NSSet* categories = [notifications retrieveCategories];

    if ([categories containsObject:@"World"]) self.WorldSwitch.on = true;
    if ([categories containsObject:@"Politics"]) self.PoliticsSwitch.on = true;
    if ([categories containsObject:@"Business"]) self.BusinessSwitch.on = true;
    if ([categories containsObject:@"Technology"]) self.TechnologySwitch.on = true;
    if ([categories containsObject:@"Science"]) self.ScienceSwitch.on = true;
    if ([categories containsObject:@"Sports"]) self.SportsSwitch.on = true;
    ```

Az alkalmazás most már tárolhatja az eszköz helyi tárolójában az értesítési központban való regisztráláshoz használt kategóriákat. A felhasználó módosíthatja a kategóriákat a futtatókörnyezetben, és az eszköz regisztrációjának frissítéséhez kattintson a `subscribe` metódusra. Ezután frissítse az alkalmazást, hogy közvetlenül az alkalmazásban küldje el a legfrissebb híreket.

## <a name="optional-send-tagged-notifications"></a>választható Címkézett értesítések küldése

Ha nem fér hozzá a Visual studióhoz, ugorjon a következő szakaszra, és küldje el az értesítéseket az alkalmazásból. Az értesítési központ hibakeresés lapján is elküldheti a sablon megfelelő értesítését a [Azure Portal] .

[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="optional-send-notifications-from-the-device"></a>választható Értesítések küldése az eszközről

A háttérben érkező értesítéseket általában egy háttér-szolgáltatás küldi el, de a híreket közvetlenül az alkalmazásból is elküldheti. Ehhez frissítse az `SendNotificationRESTAPI` első [lépések Notification Hubs][get-started] oktatóanyagban megadott módszert.

1. A `ViewController.m`alkalmazásban frissítse `SendNotificationRESTAPI` a metódust a következőképpen, hogy elfogadja a kategória címkéhez tartozó paramétert, és elküldi a [sablon](notification-hubs-templates-cross-platform-push-messages.md) megfelelő értesítését.

    ```objc
    - (void)SendNotificationRESTAPI:(NSString*)categoryTag
    {
        NSURLSession* session = [NSURLSession sessionWithConfiguration:[NSURLSessionConfiguration
                                    defaultSessionConfiguration] delegate:nil delegateQueue:nil];

        NSString *json;

        // Construct the messages REST endpoint
        NSURL* url = [NSURL URLWithString:[NSString stringWithFormat:@"%@%@/messages/%@", HubEndpoint,
                                            HUBNAME, API_VERSION]];

        // Generated the token to be used in the authorization header.
        NSString* authorizationToken = [self generateSasToken:[url absoluteString]];

        //Create the request to add the template notification message to the hub
        NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
        [request setHTTPMethod:@"POST"];

        // Add the category as a tag
        [request setValue:categoryTag forHTTPHeaderField:@"ServiceBusNotification-Tags"];

        // Template notification
        json = [NSString stringWithFormat:@"{\"messageParam\":\"Breaking %@ News : %@\"}",
                categoryTag, self.notificationMessage.text];

        // Signify template notification format
        [request setValue:@"template" forHTTPHeaderField:@"ServiceBusNotification-Format"];

        // JSON Content-Type
        [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];

        //Authenticate the notification message POST request with the SaS token
        [request setValue:authorizationToken forHTTPHeaderField:@"Authorization"];

        //Add the notification message body
        [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];

        // Send the REST request
        NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
                    completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
            {
            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (error || httpResponse.statusCode != 200)
                {
                    NSLog(@"\nError status: %d\nError: %@", httpResponse.statusCode, error);
                }
                if (data != NULL)
                {
                    //xmlParser = [[NSXMLParser alloc] initWithData:data];
                    //[xmlParser setDelegate:self];
                    //[xmlParser parse];
                }
            }];

        [dataTask resume];
    }
    ```

2. A `ViewController.m`alkalmazásban frissítse `Send Notification` a műveletet az alábbi kódban látható módon. Annak érdekében, hogy az értesítéseket az egyes címkék használatával külön küldje el, és több platformra küldje.

    ```objc
    - (IBAction)SendNotificationMessage:(id)sender
    {
        self.sendResults.text = @"";

        NSArray* categories = [NSArray arrayWithObjects: @"World", @"Politics", @"Business",
                                @"Technology", @"Science", @"Sports", nil];

        // Lets send the message as breaking news for each category to WNS, FCM, and APNS
        // using a template.
        for(NSString* category in categories)
        {
            [self SendNotificationRESTAPI:category];
        }
    }
    ```

3. Építse újra a projektet, és győződjön meg arról, hogy nincsenek Build-hibák.

## <a name="run-the-app-and-generate-notifications"></a>Az alkalmazás futtatása és értesítések létrehozása

1. Kattintson a Futtatás gombra a projekt felépítéséhez és az alkalmazás elindításához. Válassza ki a feltört híreket az előfizetéshez, majd kattintson az **előfizetés** gombra. Ekkor megjelenik egy párbeszédpanel, amely jelzi, hogy az értesítések előfizetése megtörtént.

    ![Példa iOS-alapú értesítésre][1]

    Ha az **előfizetés**lehetőséget választja, az alkalmazás a kiválasztott kategóriákat címkékre konvertálja, és az értesítési központban új eszköz regisztrációt kér a kiválasztott címkékre.

2. Adja meg a feltörési hírekként küldendő üzenetet, majd kattintson az **Értesítés küldése** gombra. Másik megoldásként futtassa a .NET-konzol alkalmazást az értesítések létrehozásához.

    ![Értesítési beállítások módosítása az iOS-ben][2]

3. A legfrissebb hírekre feliratkozott összes eszköz megkapja az imént elküldött Hírekkel kapcsolatos értesítéseket.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban szórásos értesítéseket küldött az egyes kategóriákhoz regisztrált iOS-eszközökre. A honosított értesítések leküldéséről a következő oktatóanyagban tájékozódhat:

> [!div class="nextstepaction"]
>[Honosított leküldéses értesítések küldése](notification-hubs-ios-xplat-localized-apns-push-notification.md)

<!-- Images. -->
[1]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-subscribed.png
[2]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios1.png
[3]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios2.png

<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: https://msdn.microsoft.com/library/jj927168.aspx
[Use Notification Hubs to broadcast localized breaking news]: notification-hubs-ios-xplat-localized-apns-push-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-ios-notify-users.md
[Notification Hubs Guidance]: https://msdn.microsoft.com/library/dn530749.aspx
[Notification Hubs How-To for iOS]: https://msdn.microsoft.com/library/jj927168.aspx
[get-started]: notification-hubs-ios-apple-push-notification-apns-get-started.md
[Azure Portal]: https://portal.azure.com
