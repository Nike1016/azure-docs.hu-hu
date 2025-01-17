---
title: 'Gyors útmutató: Beszéd, Objective-C-Speech szolgáltatás felismerése'
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan ismerheti fel a beszédfelismerést a macOS Objective-C-ben a Speech SDK használatával
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: chlandsi
ms.openlocfilehash: 45cd1210ee6af3c456171a427729f6e16caf2d58
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559365"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-macos-using-the-speech-sdk"></a>Gyors útmutató: Beszéd felismerése a macOS Objective-C-ben a Speech SDK használatával

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Ebből a cikkből megtudhatja, hogyan hozhat létre egy macOS-alkalmazást a Objective-C-ben a Cognitive Services Speech SDK használatával, amely a mikrofonból a szövegbe rögzített beszédet mutatja be.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, a következő előfeltételek listáját:

* A [előfizetési kulcs](get-started.md) a beszédfelismerési szolgáltatás
* MacOS rendszerű gép [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) vagy újabb verzióval, MacOS 10,13 vagy újabb verzióval

## <a name="get-the-speech-sdk-for-macos"></a>A macOS-hez készült Speech SDK beszerzése

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

A Cognitive Services Speech SDK jelenlegi verziója az `1.6.0`.

A Mac rendszerhez készült Cognitive Services Speech SDK-t keretrendszer-csomagként terjesztik.
Xcode-projektekben használható [CocoaPod](https://cocoapods.org/), illetve manuálisan is letölthető https://aka.ms/csspeech/macosbinary és csatolható. Ez az útmutató egy CocoaPod használ.

## <a name="create-an-xcode-project"></a>Xcode-projekt létrehozása

Indítsa el az Xcode-ot, majd a **File** > **New** > **Project** lehetőséget választva kezdjen új projektet.
A sablon kiválasztása párbeszédpanelen válassza a "kakaó-alkalmazás" sablont.

A további párbeszédpaneleken válassza az alábbi lehetőségeket:

1. Project Options párbeszédpanel
    1. Adja meg a gyorsindítási alkalmazás nevét, például: `helloworld`.
    1. Ha már rendelkezik Apple Developer-fiókkal, adja meg a megfelelő szervezet nevét és a szervezet azonosítóját. Kipróbálás céljából választhat bármilyen nevet, például: `testorg`. Az alkalmazás aláírásához megfelelő kiépítési profilra van szükség. További részletekért tekintse meg az [Apple Developer](https://developer.apple.com/) webhelyét.
    1. Ügyeljen arra, hogy a projekt nyelveként az Objective-C-t válassza ki.
    1. Tiltsa le a jelölőnégyzeteket a forgatókönyvek használatához és egy dokumentum alapú alkalmazás létrehozásához. A minta alkalmazás egyszerű felhasználói felülete programozott módon lesz létrehozva.
    1. Távolítsa el a jelölést a tesztekre és az alapvető adatokra vonatkozó jelölőnégyzetekből.
    ![Projektbeállítások](media/sdk/qs-objectivec-macos-project-settings.png)
1. A projektkönyvtár kiválasztása
    1. Válassza ki azt a könyvtárat, ahová a projektet be szeretné állítani. Ez létrehoz egy `helloworld` könyvtárat a saját könyvtárában, amely a Xcode projekt összes fájlját tartalmazza.
    1. Ennél a próbaprojektnél tiltsa le a Git-adattár létrehozását.
1. Állítsa be a jogosultságokat a hálózati és a mikrofonhoz való hozzáféréshez. Kattintson az alkalmazás nevére a bal oldali áttekintés első sorában, hogy beolvassa az alkalmazás konfigurációját, majd válassza a "képességek" fület.
    1. Engedélyezze az alkalmazáshoz tartozó "homokozó" beállítást.
    1. Engedélyezze a "kimenő kapcsolatok" és a "mikrofon" hozzáférés jelölőnégyzeteit.
    ![A homokozó beállításai](media/sdk/qs-objectivec-macos-sandbox.png)
1. Az alkalmazásnak meg kell adnia a mikrofon használatát a `Info.plist` fájlban. Kattintson a fájlra az áttekintésben, és adja hozzá a "privacy-mikrofon használati Leírás" kulcsot, amelynek értéke például "mikrofon szükséges a beszédfelismeréshez".
    ![Beállítások az info. plist fájlban](media/sdk/qs-objectivec-macos-info-plist.png)
1. A Xcode projekt lezárása. A CocoaPods beállítása után később egy másik példányt fog használni.

## <a name="install-the-sdk-as-a-cocoapod"></a>Az SDK telepítése CocoaPod

1. Telepítse a CocoaPod-függőség kezelőjét a [telepítési utasításokban](https://guides.cocoapods.org/using/getting-started.html)leírtak szerint.
1. Navigáljon a minta alkalmazás (`helloworld`) könyvtárába. Helyezzen el egy szövegfájlt a névvel `Podfile` és a következő tartalommal a könyvtárban:  
   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-macos/helloworld/Podfile)]
1. Navigáljon a `helloworld` címtárhoz egy terminálon, és futtassa `pod install`a parancsot. Ez egy `helloworld.xcworkspace` Xcode-munkaterületet hoz majd, amely a minta alkalmazást és a Speech SDK-t is függőségként fogja tartalmazni. Ezt a munkaterületet a következőben fogjuk használni.

## <a name="add-the-sample-code"></a>A mintakód hozzáadása

1. Nyissa `helloworld.xcworkspace` meg a munkaterületet a Xcode.
1. Cserélje le az automatikusan létrehozott `AppDelegate.m` fájl tartalmát az alábbi kódra:  
   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-macos/helloworld/helloworld/AppDelegate.m#code)]
1. Cserélje le a `YourSubscriptionKey` sztringet az előfizetői azonosítóra.
1. Cserélje le a `YourServiceRegion` sztringet az előfizetéséhez társított [régióra](regions.md) (ez a `westus` régió, ha az ingyenes próbaverzióra regisztrált).

## <a name="build-and-run-the-sample"></a>A minta létrehozása és futtatása

1. Tegye láthatóvá a hibakeresési kimenetet (**View** > **Debug Area** > **Activate Console**).
1. Hozza létre és futtassa a példát a **termék** > **futtatása** elem kiválasztásával a menüből, vagy kattintson a **Lejátszás** gombra.
1. Miután rákattintott a gombra, és néhány szót mondott, a képernyő alsó részén megjelenő szöveg jelenik meg. Amikor első alkalommal futtatja az alkalmazást, a rendszer kérni fogja, hogy az alkalmazás hozzáférjen a számítógép mikrofonja számára.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A Objective-C minták megismerése a GitHubon](https://aka.ms/csspeech/samples)
