---
title: Hogyan hozhat létre, és keresse meg az Azure térbeli horgonyok használatával Objective-C nyelven horgonyok |} A Microsoft Docs
description: Magyarázatra van szüksége, hogy hogyan hozhat létre, és keresse meg a horgonyok Azure térbeli horgonyok Objective-c használatával
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: d9638c96c06c06bd92690e568364551bdc299c80
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672041"
---
# <a name="how-to-create-and-locate-anchors-using-azure-spatial-anchors-in-objective-c"></a>Hogyan hozhat létre, és keresse meg a horgonyok Azure térbeli horgonyok Objective-C használatával

> [!div  class="op_single_selector"]
> * [Unity](create-locate-anchors-unity.md)
> * [Objective-C](create-locate-anchors-objc.md)
> * [Swift](create-locate-anchors-swift.md)
> * [Android Java](create-locate-anchors-java.md)
> * [C++/NDK](create-locate-anchors-cpp-ndk.md)
> * [C++/WinRT](create-locate-anchors-cpp-winrt.md)

Azure térbeli horgonyok horgonyok a világ különböző eszközök közötti megosztását teszi lehetővé. Támogatja a több különböző fejlesztési környezet. Ebben a cikkben fogja nekikezdenénk Azure térbeli horgonyok SDK Objective-C, a használatával:

- Helyes beállítása és kezelése az Azure térbeli horgonyok munkamenet.
- Hozzon létre, és állítsa be a helyi kapcsolatok alapjainak tulajdonságait.
- Töltse fel a felhőbe azokat.
- Keresse meg, és törölje a térbeli felhőbeli horgonyok.

## <a name="prerequisites"></a>Előfeltételek

Ez az útmutató végrehajtásához győződjön meg arról, hogy rendelkezik:

- Olvassa el a [Azure térbeli kapcsolatok alapjainak áttekintése](../overview.md).
- Befejeződött az egyik a [5 perces gyors útmutatók](../index.yml).
- Alapvető ismeretek az Objective-c-hez
- Alapvető ismeretek <a href="https://developer.apple.com/arkit/" target="_blank">ARKit</a>.

[!INCLUDE [Start](../../../includes/spatial-anchors-create-locate-anchors-start.md)]

Tudjon meg többet a [ASACloudSpatialAnchorSession](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsession) osztály.

```objc
    ASACloudSpatialAnchorSession *_cloudSession;
    // In your view handler
    _cloudSession = [[ASACloudSpatialAnchorSession alloc] init];
```

[!INCLUDE [Account Keys](../../../includes/spatial-anchors-create-locate-anchors-account-keys.md)]

Tudjon meg többet a [ASASessionConfiguration](https://docs.microsoft.com/objectivec/api/spatial-anchors/asasessionconfiguration) osztály.

```objc
    _cloudSession.configuration.accountKey = @"MyAccountKey";
```

[!INCLUDE [Access Tokens](../../../includes/spatial-anchors-create-locate-anchors-access-tokens.md)]

```objc
    _cloudSession.configuration.accessToken = @"MyAccessToken";
```

[!INCLUDE [Access Tokens Event](../../../includes/spatial-anchors-create-locate-anchors-access-tokens-event.md)]

Tudjon meg többet a [tokenRequired](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsessiondelegate#tokenrequired) protokollok metódus.

```objc
    - (void)tokenRequired:(ASACloudSpatialAnchorSession *)cloudSession :(ASATokenRequiredEventArgs *)args {
        args.accessToken = @"MyAccessToken";
    }
```

[!INCLUDE [Asynchronous Tokens](../../../includes/spatial-anchors-create-locate-anchors-asynchronous-tokens.md)]

```objc
    - (void)tokenRequired:(ASACloudSpatialAnchorSession *)cloudSession :(ASATokenRequiredEventArgs *)args {
        ASACloudSpatialAnchorSessionDeferral *deferral = [args getDeferral];
        [myGetTokenAsync callback:^(NSString *myToken) {
            if (myToken) args.accessToken = myToken;
            [deferral complete];
        }];
    }
```

[!INCLUDE [Azure AD Tokens](../../../includes/spatial-anchors-create-locate-anchors-aad-tokens.md)]

```objc
    _cloudSession.configuration.authenticationToken = @"MyAuthenticationToken";
```

[!INCLUDE [Azure AD Tokens Event](../../../includes/spatial-anchors-create-locate-anchors-aad-tokens-event.md)]

```objc
    - (void)tokenRequired:(ASACloudSpatialAnchorSession *)cloudSession :(ASATokenRequiredEventArgs *)args {
        args.authenticationToken = @"MyAuthenticationToken";
    }
```

[!INCLUDE [Asynchronous Tokens](../../../includes/spatial-anchors-create-locate-anchors-asynchronous-tokens.md)]

```objc
    - (void)tokenRequired:(ASACloudSpatialAnchorSession *)cloudSession :(ASATokenRequiredEventArgs *)args {
        ASACloudSpatialAnchorSessionDeferral *deferral = [args getDeferral];
        [myGetTokenAsync callback:^(NSString *myToken) {
            if (myToken) args.authenticationToken = myToken;
            [deferral complete];
        }];
    }
```

[!INCLUDE [Setup](../../../includes/spatial-anchors-create-locate-anchors-setup-ios.md)]

Tudjon meg többet a [start](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsession#start) metódust.

```objc0
    _cloudSession.session = self.sceneView.session;
    _cloudSession.delegate = self;
    [_cloudSession start];
```

[!INCLUDE [Frames](../../../includes/spatial-anchors-create-locate-anchors-frames.md)]

Tudjon meg többet a [processFrame](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsession#processframe) metódust.

```objc
    [_cloudSession processFrame:_sceneView.session.currentFrame];
```

[!INCLUDE [Feedback](../../../includes/spatial-anchors-create-locate-anchors-feedback.md)]

Tudjon meg többet a [sessionUpdated](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsessiondelegate#sessionupdated) protokollok metódus.

```objc
    - (void)sessionUpdated:(ASACloudSpatialAnchorSession *)cloudSession :(ASASessionUpdatedEventArgs *)args {
        ASASessionStatus *status = [args status];
        if (status.userFeedback == ASASessionUserFeedbackNone) return;
        _feedback = [NSString
            stringWithFormat:@"Feedback: %@ - Recommend Create=%.0f%%",
            FeedbackToString(status.userFeedback),
            status.recommendedForCreateProgress * 100.f];
    }
```

[!INCLUDE [Creating](../../../includes/spatial-anchors-create-locate-anchors-creating.md)]

Tudjon meg többet a [ASACloudSpatialAnchor](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchor) osztály.

```objc
    // Create a local anchor, perhaps by hit-testing and creating an ARAnchor
    NSArray<ARHitTestResult *> *hits = [_sceneView.session.currentFrame hitTest:CGPointMake(0.5, 0.5) types:ARHitTestResultTypeEstimatedHorizontalPlane];
    if ([hits count] == 0) return;
    // The hitTest method sorts the resulting list by increasing distance from the camera
    // The first hit result will usually be the most relevant when responding to user input
    ARAnchor *localAnchor = [[ARAnchor alloc] initWithTransform:hits[0].worldTransform];
    [_sceneView.session addAnchor:localAnchor];

    // If the user is placing some application content in their environment,
    // you might show content at this anchor for a while, then save when
    // the user confirms placement.
    ASACloudSpatialAnchor *cloudAnchor = [[ASACloudSpatialAnchor alloc] init];
    cloudAnchor.localAnchor = localAnchor;
    [_cloudSession createAnchor:cloudAnchor withCompletionHandler:^(NSError *error) {
        if (error) {
            _feedback = [NSString stringWithFormat:@"Save Failed:%@", error.localizedDescription];
            return;
        }
        _feedback = [NSString stringWithFormat:@"Created a cloud anchor with ID=%@", cloudAnchor.identifier];
    }];
```

[!INCLUDE [Session Status](../../../includes/spatial-anchors-create-locate-anchors-session-status.md)]

Tudjon meg többet a [getSessionStatusWithCompletionHandler](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsession#getsessionstatus) metódust.

```objc
    [_cloudSession getSessionStatusWithCompletionHandler:^(ASASessionStatus *value, NSError *error) {
        if (error) {
            _feedback = [NSString stringWithFormat:@"Session status error:%@", error.localizedDescription];
            return;
        }
        if (value.recommendedForCreateProgress < 1.0f) return;
        // Issue the creation request ...
    }];
```

[!INCLUDE [Setting Properties](../../../includes/spatial-anchors-create-locate-anchors-setting-properties.md)]

Tudjon meg többet a [appProperties](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchor#appproperties) tulajdonság.

```objc
    ASACloudSpatialAnchor *cloudAnchor = [[ASACloudSpatialAnchor alloc] init];
    cloudAnchor.localAnchor = localAnchor;
    cloudAnchor.appProperties = @{ @"model-type" : @"frame", @"label" : @"my latest picture" };
    [_cloudSession createAnchor:cloudAnchor withCompletionHandler:^(NSError *error) {
        // ...
    });
```

[!INCLUDE [Update Anchor Properties](../../../includes/spatial-anchors-create-locate-anchors-updating-properties.md)]

Tudjon meg többet a [updateAnchorProperties](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsession#updateanchorproperties) metódust.

```objc
    ASACloudSpatialAnchor *anchor = /* locate your anchor */;
    [anchor.appProperties setValue:@"just now" forKey:@"last-user-access"];
    [_cloudSession updateAnchorProperties:anchor withCompletionHandler:^(NSError *error) {
        if (error) _feedback = [NSString stringWithFormat:@"Updating Properties Failed:%@", error.localizedDescription];
    }];
```

[!INCLUDE [Getting Properties](../../../includes/spatial-anchors-create-locate-anchors-getting-properties.md)]

Tudjon meg többet a [getAnchorProperties](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsession#getanchorproperties) metódust.

```objc
    [_cloudSession getAnchorProperties:@"anchorId" withCompletionHandler:^(SCCCloudSpatialAnchor *anchor, NSError *error) {
        if (error) {
            _feedback = [NSString stringWithFormat:@"Getting Properties Failed:%@", error.localizedDescription];
            return;
        }
        if (anchor) {
            [anchor.appProperties setValue:@"just now" forKey:@"last-user-access"];
            [_cloudSession updateAnchorProperties:anchor withCompletionHandler:^(NSError *error) {
                // ...
            }];
        }
    }];
```

[!INCLUDE [Expiration](../../../includes/spatial-anchors-create-locate-anchors-expiration.md)]

Tudjon meg többet a [lejárati](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchor#expiration) tulajdonság.

```objc
    int secondsInAWeek = 60 * 60 * 24 * 7;
    NSDate *oneWeekFromNow = [[NSDate alloc] initWithTimeIntervalSinceNow: (NSTimeInterval) secondsInAWeek];
    cloudAnchor.expiration = oneWeekFromNow;
```

[!INCLUDE [Locate](../../../includes/spatial-anchors-create-locate-anchors-locating.md)]

Tudjon meg többet a [createWatcher](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsession#createwatcher) metódust.

```objc
    ASAAnchorLocateCriteria *criteria = [ASAAnchorLocateCriteria new];
    criteria.identifiers = @[ @"id1", @"id2", @"id3" ];
    [_cloudSession createWatcher:criteria];
```

[!INCLUDE [Locate Events](../../../includes/spatial-anchors-create-locate-anchors-locating-events.md)]

Tudjon meg többet a [anchorLocated](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsessiondelegate#anchorlocated) protokollok metódus.

```objc
    - (void)anchorLocated:(ASACloudSpatialAnchorSession *)cloudSession :(ASAAnchorLocatedEventArgs *)args {
        ASALocateAnchorStatus status = [args status];
        switch (status) {
        case ASALocateAnchorStatusLocated: {
            ASACloudSpatialAnchor *foundAnchor = [args anchor];
            // Go add your anchor to the scene...
        }
            break;
        case ASALocateAnchorStatusAlreadyTracked:
            // This anchor has already been reported and is being tracked
            break;
        case ASALocateAnchorStatusNotLocatedAnchorDoesNotExist:
            // The anchor was deleted or never existed in the first place
            // Drop it, or show UI to ask user to anchor the content anew
            break;
        case ASALocateAnchorStatusNotLocated:
            // The anchor hasn't been found given the location data
            // The user might in the wrong location, or maybe more data will help
            // Show UI to tell user to keep looking around
            break;
    }
```

[!INCLUDE [Deleting](../../../includes/spatial-anchors-create-locate-anchors-deleting.md)]

Tudjon meg többet a [deleteAnchor](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsession#deleteanchor) metódust.

```objc
    [_cloudSession deleteAnchor:cloudAnchor withCompletionHandler:^(NSError *error) {
        // Perform any processing you may want when delete finishes
    }];
```

[!INCLUDE [Stopping](../../../includes/spatial-anchors-create-locate-anchors-stopping.md)]

Tudjon meg többet a [leállítása](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsession#stop) metódust.

```objc
    [_cloudSession stop];
```

[!INCLUDE [Resetting](../../../includes/spatial-anchors-create-locate-anchors-resetting.md)]

Tudjon meg többet a [alaphelyzetbe](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsession#reset) metódust.

```objc
    [_cloudSession reset];
```

[!INCLUDE [Cleanup](../../../includes/spatial-anchors-create-locate-anchors-cleanup-others.md)]

```objc
    _cloudSession = NULL;
```

[!INCLUDE [Next Steps](../../../includes/spatial-anchors-create-locate-anchors-next-steps.md)]
