---
title: 'Gyors útmutató: Beszédfelismerési szolgáltatás felismerése, egység – beszéd'
titleSuffix: Azure Cognitive Services
description: Ezzel az útmutatóval létrehozhat egy beszéd – szöveg alkalmazást az Unity és az Unity (Beta) rendszerhez készült Speech SDK használatával. Ha elkészült, a számítógép mikrofonjával valós időben konvertálhat át beszédet szöveggé.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/24/2019
ms.author: jhakulin
ms.openlocfilehash: 1b6e60edd86cff2d657b562f05351e20571c0909
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68815406"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-unity-beta"></a>Gyors útmutató: Beszéd felismerése az Unity (béta) Speech SDK-val

A rövid útmutatók [szövegről beszédre](quickstart-text-to-speech-csharp-unity.md)is elérhetők.

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Ezzel az útmutatóval hozhat létre egy beszéd-szöveges alkalmazást az [Unity](https://unity3d.com/) és az Unity (Beta) rendszerhez készült Speech SDK használatával.
Ha elkészült, megbeszélheti az eszközét, hogy valós időben átmásolja a beszédet a szövegbe.
Ha még csak most ismerkedik az egységességgel, javasoljuk, hogy az alkalmazás fejlesztése előtt tanulmányozza az [egység felhasználói kézikönyvét](https://docs.unity3d.com/Manual/UnityManual.html) .

> [!NOTE]
> Az egységhez készült Speech SDK jelenleg a Beta verzióban érhető el.
> Támogatja a Windows asztali (x86 és x64) vagy Univerzális Windows-platform (x86, x64, ARM/ARM64) és az Android (x86, ARM32/64) használatát.

## <a name="prerequisites"></a>Előfeltételek

A projekt teljesítéséhez a következők szükségesek:

- [Unity 2018,3 vagy újabb](https://store.unity.com/) , az [Unity 2019,1 támogatásával UWP ARM64](https://blogs.unity3d.com/2019/04/16/introducing-unity-2019-1/#universal).
- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/). A Visual Studio 2017 15,9-es vagy újabb verziója is elfogadható.
  - A ARM64 támogatásához telepítse a [ARM64 választható Build-eszközeit, valamint a ARM64 Windows 10 SDK](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/)-t.
- A beszédfelismerési szolgáltatáshoz tartozó előfizetési kulcs. [Szerezze be az egyiket ingyenesen](get-started.md).
- A számítógép mikrofonjának elérése.

## <a name="create-a-unity-project"></a>Unity-projekt létrehozása

1. Nyissa meg az egységet. Ha első alkalommal használja az egységet, megjelenik az **Unity hub** *<version number>* ablak. (Az Unity hub-t közvetlenül is megnyithatja az ablak megnyitásához.)

   [![Unity hub ablak](media/sdk/qs-csharp-unity-hub.png)](media/sdk/qs-csharp-unity-hub.png#lightbox)
1. Válassza az **Új** lehetőséget. Megjelenik az **új projekt létrehozása egységgel** *<version number>* ablak.

   [![Új projekt létrehozása az Unity hub-ban](media/sdk/qs-csharp-unity-create-a-new-project.png)](media/sdk/qs-csharp-unity-create-a-new-project.png#lightbox)
1. A **Project Name (projekt neve**) mezőben adja meg a **csharp-Unity**értéket.
1. A **sablonokban**, ha a **3D** még nincs kiválasztva, jelölje ki.
1. A **hely**mezőben válasszon ki vagy hozzon létre egy mappát, amelybe menteni szeretné a projektet.
1. Kattintson a **Létrehozás** gombra.

Egy kis idő elteltével megjelenik az Unity Editor ablak.

## <a name="install-the-speech-sdk"></a>A Speech SDK telepítése

Az egységhez készült Speech SDK telepítéséhez kövesse az alábbi lépéseket:

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

1. Töltse le és nyissa meg az [Unity (Beta)](https://aka.ms/csspeech/unitypackage)rendszerhez készült Speech SDK csomagot (. unitypackage). Amikor megnyílik az eszközoldali csomag, megjelenik az **egységességi csomag importálása** párbeszédpanel.

   [![Unity-csomag importálása párbeszédpanel az Unity Editorban](media/sdk/qs-csharp-unity-01-import.png)](media/sdk/qs-csharp-unity-01-import.png#lightbox)
1. Győződjön meg arról, hogy az összes fájl ki van választva, majd válassza az **Importálás**lehetőséget. Néhány pillanat elteltével a rendszer importálja az Unity Asset csomagot a projektbe.

Az adategység-csomagok Unity-ba történő importálásával kapcsolatos további információkért tekintse meg az [Unity dokumentációját](https://docs.unity3d.com/Manual/AssetPackages.html).

## <a name="add-ui"></a>Felhasználói felület hozzáadása

Most vegyünk fel egy minimális felhasználói felületet a színtérbe. Ez a felhasználói felület egy gombból áll, amely elindítja a beszédfelismerést és egy szöveges mezőt az eredmény megjelenítéséhez. A [ **hierarchia** ablakban](https://docs.unity3d.com/Manual/Hierarchy.html)megjelenik egy minta jelenet, amely az új projekttel létrehozott egységet mutatja.

1. A **hierarchia** ablak tetején válassza a**felhasználói felület** >  **létrehozása** > **gombot**.

   Ez a művelet három játék objektumot hoz létre, amelyek a **hierarchia** ablakban láthatók: egy **gomb** objektum, egy **vászon** objektum, amely tartalmazza a gombot, valamint egy **EventSystem** objektumot.

   [![Unity-szerkesztő környezet](media/sdk/qs-csharp-unity-editor-window.png)](media/sdk/qs-csharp-unity-editor-window.png#lightbox)

1. [Navigáljon a **jelenet** ](https://docs.unity3d.com/Manual/SceneViewNavigation.html) nézetbe, hogy jól látható legyen a vászon és a gomb a [ **jelenet** nézetben](https://docs.unity3d.com/Manual/UsingTheSceneView.html).

1. A [ **felügyelő** ablakban](https://docs.unity3d.com/Manual/UsingTheInspector.html) (alapértelmezés szerint a jobb oldalon) állítsa a **POS X** és a **POS Y** tulajdonságokat **0-ra**, így a gomb középre van állítva a vászon közepén.

1. A **hierarchia** ablakban válassza a**felhasználói felület** >  **létrehozása** > **szöveg** lehetőséget egy **szöveges** objektum létrehozásához.

1. A **felügyelő** ablakban állítsa a **POS X** és a **pos Y** tulajdonságokat **0** és **120**értékre, majd állítsa a **szélesség** és a **magasság** tulajdonságokat **240** és **120**értékre. Ezek az értékek biztosítják, hogy a szöveg mező és a gomb ne legyen átfedésben.

Ha elkészült, a **jelenet** nézetnek a következő képernyőképhez hasonlóan kell kinéznie:

[![Jelenet nézet az Unity Editorban](media/sdk/qs-csharp-unity-02-ui-inline.png)](media/sdk/qs-csharp-unity-02-ui-inline.png#lightbox)

## <a name="add-the-sample-code"></a>A mintakód hozzáadása

A következő lépésekkel adhatja hozzá a minta parancsfájl kódját az Unity projekthez:

1. A [projekt ablakban](https://docs.unity3d.com/Manual/ProjectView.html)válassza a **C# parancsfájl** **létrehozása**  >  lehetőséget egy új C# parancsfájl hozzáadásához.

   [![Projekt ablak az Unity Editorban](media/sdk/qs-csharp-unity-project-window.png)](media/sdk/qs-csharp-unity-project-window.png#lightbox)
1. Adja meg a `HelloWorld`parancsfájl nevét.

1. Kattintson ide `HelloWorld` duplán az újonnan létrehozott parancsfájl szerkesztéséhez.

   > [!NOTE]
   > Ha úgy szeretné beállítani a Kódszerkesztő használatát, hogy az Unity a szerkesztéshez használja, válassza a**Beállítások** **szerkesztése** > lehetőséget, majd lépjen a **külső eszközök** beállításaihoz. További információ: [Unity felhasználói kézikönyv](https://docs.unity3d.com/Manual/Preferences.html).

1. Cserélje le a meglévő parancsfájlt a következő kódra:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-unity/Assets/Scripts/HelloWorld.cs#code)]

1. Keresse meg és cserélje le `YourSubscriptionKey` a karakterláncot a Speech Services előfizetési kulcsával.

1. Keresse meg és cserélje le `YourServiceRegion` a karakterláncot az előfizetéséhez társított [régióval](regions.md) . Ha például az ingyenes próbaverziót használja, akkor a régió a `westus`.

1. Mentse a parancsfájl módosításait.

Most térjen vissza az Unity Editorhoz, és adja hozzá a parancsfájlt összetevőként az egyik játék-objektumhoz:

1. A **hierarchia** ablakban válassza ki a **vászon** objektumot.

1. Az **ellenőr** ablakban kattintson az **összetevő hozzáadása** gombra.

   [![Ellenőr ablak az Unity Editorban](media/sdk/qs-csharp-unity-inspector-window.png)](media/sdk/qs-csharp-unity-inspector-window.png#lightbox)

1. A legördülő listában keresse meg a `HelloWorld` fent létrehozott parancsfájlt, és adja hozzá. A **"Helló világ!" alkalmazás (szkript)** szakasz a **felügyelő** ablakban jelenik meg, amely két nem inicializált tulajdonságot, a **kimeneti szöveget** és a kiindulási **gombot**tartalmazza. Ezek az egység-összetevők tulajdonságai megegyeznek az `HelloWorld` osztály nyilvános tulajdonságaival.

1. Jelölje be a kiválasztó **gomb** tulajdonság objektumának kiválasztása (a tulajdonság jobb oldalán lévő kis kör ikon), és válassza ki a korábban létrehozott **gomb** objektumot.

1. Válassza ki a **kimeneti szöveg** tulajdonság objektum-választóját, és válassza ki a korábban létrehozott **szöveges** objektumot.

   > [!NOTE]
   > A gomb beágyazott szöveges objektummal is rendelkezik. Ügyeljen arra, hogy a szöveg kimenete ne legyen véletlenül kiválasztva (vagy nevezze át az egyik szöveges objektumot a **felügyelő** ablak **név** mezőjéből a félreértések elkerülése érdekében).

## <a name="run-the-application-in-the-unity-editor"></a>Az alkalmazás futtatása az Unity Editorban

Most már készen áll az alkalmazás futtatására az Unity Editoron belül.

1. Az Unity Editor eszköztárán (a menüsáv alatt) válassza a **Lejátszás** gombot (egy jobb oldali háromszög).

1. Nyissa meg a [ **játék** nézetet](https://docs.unity3d.com/Manual/GameView.html), és várja meg, amíg a **text** objektum a **beszédfelismerés felismeréséhez kattintson a gombra**. ( **Új szöveget** jelenít meg, ha az alkalmazás nem indult el, vagy nem áll készen a válaszadásra.)

1. Kattintson a gombra, és beszéljen egy angol kifejezéssel vagy mondattal a számítógép mikrofonjában. A beszéd a beszédfelismerési szolgáltatásokhoz lett továbbítva, és szövegbe kerül, amely a **játék** nézetben jelenik meg.

   [![Játék nézet az Unity Editorban](media/sdk/qs-csharp-unity-03-output-inline.png)](media/sdk/qs-csharp-unity-03-output-inline.png#lightbox)

1. A hibakeresési üzenetekért tekintse meg a [ **konzol** ablakát](https://docs.unity3d.com/Manual/Console.html) . Ha a **konzolablak** nem jelenik meg, nyissa meg a menüsávot, és válassza ki az **ablak** > **általános** > **konzolját** a megjelenítéshez.

1. Ha elvégezte a beszédfelismerést, válassza a **Lejátszás** gombot az Unity Editor eszköztárán az alkalmazás leállításához.

## <a name="additional-options-to-run-this-application"></a>További lehetőségek az alkalmazás futtatásához

Ez az alkalmazás Android-alkalmazásként, Windows önálló alkalmazásként vagy UWP alkalmazásként is üzembe helyezhető.
További információkért tekintse meg a [minta tárházat](https://aka.ms/csspeech/samples). A `quickstart/csharp-unity` mappa leírja a további célok konfigurációját.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Minták C# feltárása a githubon](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Lásd még

- [Custom Speech modell betanítása](how-to-custom-speech-train-model.md)
