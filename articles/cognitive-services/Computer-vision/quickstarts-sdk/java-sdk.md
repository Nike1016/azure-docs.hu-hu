---
title: 'Gyors útmutató: A Javához készült ügyféloldali kódtár Computer Vision'
titleSuffix: Azure Cognitive Services
description: Ismerkedjen meg a Javához készült Computer Vision-ügyfél függvénytárával.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: ''
ms.topic: quickstart
ms.date: 07/25/2019
ms.author: pafarley
ms.openlocfilehash: 15baf5ee2418581056d571340ba6e8009c33e4ca
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68828899"
---
# <a name="quickstart-computer-vision-client-library-for-java"></a>Gyors útmutató: A Javához készült ügyféloldali kódtár Computer Vision

Ismerkedjen meg a Javához készült Computer Vision-ügyfél függvénytárával. Az alábbi lépéseket követve telepítheti a csomagot, és kipróbálhatja az alapszintű feladatokhoz tartozó példa kódját. A Computer Vision a képek feldolgozásához és a visszaadott adatokhoz való hozzáférést biztosít a speciális algoritmusokhoz.

A Javához készült Computer Vision ügyféloldali kódtár a következőre használható:

* Elemezheti a címkéket, a szöveges leírást, az arcokat, a felnőtt tartalmakat és egyebeket.

[Dokumentációs](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/computervision?view=azure-java-stable) | anyagok[(Maven)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/) | [mintái](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* A [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html) aktuális verziója
* A [Gradle Build eszköz](https://gradle.org/install/)vagy egy másik függőségi kezelő.

## <a name="setting-up"></a>Beállítás

### <a name="create-a-computer-vision-azure-resource"></a>Computer Vision Azure-erőforrás létrehozása

Az Azure Cognitive Services a-ra előfizetett Azure-erőforrások képviselik. Hozzon létre egy erőforrást Computer Vision a helyi gépen található [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) vagy az [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) használatával. További lehetőségek:

* A [próbaverziós kulcs](https://azure.microsoft.com/try/cognitive-services/#decision) ingyenes hét napig érvényes. A regisztráció után elérhető lesz az [Azure webhelyén](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Tekintse meg az erőforrást a [Azure Portalon](https://portal.azure.com/).

Miután beolvasott egy kulcsot a próbaverziós előfizetésből vagy erőforrásból, [hozzon létre egy környezeti változót](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) a (z) nevű `COMPUTER_VISION_SUBSCRIPTION_KEY`kulcshoz.

### <a name="create-a-new-gradle-project"></a>Új Gradle-projekt létrehozása

Egy konzolablak (például a cmd, a PowerShell vagy a bash) ablakban hozzon létre egy új könyvtárat az alkalmazáshoz, és navigáljon hozzá. 

```console
mkdir myapp && cd myapp
```

Futtassa a `gradle init` parancsot a munkakönyvtárból. Ez a parancs alapvető Build-fájlokat hoz létre a Gradle számára, beleértve a *Build. Gradle. KTS*fájlt, amelyet futásidőben használ az alkalmazás létrehozásához és konfigurálásához.

```console
gradle init --type basic
```

Amikor a rendszer rákérdez a **DSL**kiválasztására, válassza a **Kotlin**lehetőséget.

Keresse meg a *Build. gradle. KTS* , és nyissa meg a kívánt ide-vagy szövegszerkesztővel. Ezután másolja a következő Build-konfigurációba. Ez a konfiguráció definiálja a projektet olyan Java-alkalmazásként, amelynek belépési pontja a **ComputerVisionQuickstarts**osztály. Importálja a Computer Vision könyvtárat.

```kotlin
plugins {
    java
    application
}
application { 
    mainClassName = "ComputerVisionQuickstarts"
}
repositories {
    mavenCentral()
}
```

A munkakönyvtárból futtassa a következő parancsot egy projekt forrás mappájának létrehozásához:

```console
mkdir -p src/main/java
```

Navigáljon az új mappára, és hozzon létre egy *ComputerVisionQuickstarts. Java*nevű fájlt. Nyissa meg a kívánt szerkesztőben vagy ide, és adja `import` hozzá a következő utasításokat:

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_imports)]

Ezután adja hozzá a **ComputerVisionQuickstarts**osztály definícióját.

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

Ez a rövid útmutató a Gradle függőség-kezelőt használja. Megtalálhatja az ügyféloldali függvénytárat és az egyéb függőségi kezelők információit a [Maven központi adattárában](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-textanalytics/).

A projekt *Build. gradle. KTS* fájljában adja meg a Computer Vision ügyféloldali függvénytárat függőségként.

```kotlin
dependencies {
    compile(group = "com.microsoft.azure.cognitiveservices", name = "azure-cognitiveservices-computervision", version = "1.0.2-beta")
}
```

## <a name="object-model"></a>Objektummodell

A következő osztályok és felületek a Computer Vision Java SDK főbb funkcióit kezelik.

|Name (Név)|Leírás|
|---|---|
| [ComputerVisionClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-java-stable) | Ez az osztály minden Computer Vision funkcióhoz szükséges. Ezt az előfizetési adatok alapján hozza létre, és más osztályok példányainak előállítására használja.|
|[ComputerVision](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervision?view=azure-java-stable)| Ez az osztály az ügyfél objektumból származik, és közvetlenül kezeli az összes képműveletet, például a képek elemzését, a szöveg észlelését és a miniatűr létrehozását.
|[VisualFeatureTypes](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-java-stable)| Ez az enumerálás a képelemzés különböző típusait határozza meg, amelyeket szabványos elemzési műveletekben lehet elvégezni. Az igényeinek megfelelően adja meg a VisualFeatureTypes-értékek készletét. |

## <a name="code-examples"></a>Példák a kódokra

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következő feladatokat a Javához készült Computer Vision ügyféloldali kódtár használatával:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Kép elemzése](#analyze-an-image)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

> [!NOTE]
> Ez a rövid útmutató azt feltételezi, hogy [létrehozott egy környezeti változót](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) a ( `COMPUTER_VISION_SUBSCRIPTION_KEY`z) nevű Computer Vision kulcshoz.

A következő kód hozzáad egy `main` metódust az osztályhoz, és változókat hoz létre az erőforrás Azure-végpontja és kulcsa számára. Meg kell adnia a saját végponti karakterláncot, amelyet a Azure Portal **Áttekintés** szakaszának ellenőrzésével talál. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_mainvars)]

Ezután adja hozzá a következő kódot egy [ComputerVisionClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-java-stable) objektum létrehozásához, és továbbítsa azt más metódus (ok) ra, amelyet később meg fog határozni.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_client)]

> [!NOTE]
> Ha a környezeti változót az alkalmazás elindítása után hozta létre, akkor a változó eléréséhez be kell állítania és újra meg kell nyitnia a szerkesztőt, az IDE-t vagy a shellt.

## <a name="analyze-an-image"></a>Rendszerkép elemzése

A következő kód egy metódust határoz `AnalyzeLocalImage`meg, amely az ügyfél-objektumot használja egy helyi rendszerkép elemzéséhez és az eredmények kinyomtatásához. A metódus a szöveges leírást, a kategorizálást, a címkék listáját, az észlelt arcokat, a felnőtt tartalom jelzőit, a fő színeket és a képtípust adja vissza.

### <a name="set-up-test-image"></a>Tesztelési rendszerkép beállítása

Először hozzon létre egy **erőforrást/** mappát a projekt **src/Main/** mappájába, és adjon hozzá egy elemezni kívánt rendszerképet. Ezután adja hozzá a következő metódus-definíciót a **ComputerVisionQuickstarts** osztályhoz. Ha szükséges, módosítsa a értékét a `pathToLocalImage` képfájlnak megfelelő értékre. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_analyzelocal_refs)]

### <a name="specify-visual-features"></a>Vizuális funkciók meghatározása

Ezután adja meg, hogy mely vizuális funkciókat szeretné kibontani az elemzésbe. A teljes listát a [VisualFeatureTypes](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-java-stable) enumerálásban tekintheti meg.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_analyzelocal_features)]

### <a name="analyze"></a>Elemzés
Ez a metódus részletes eredményeket jelenít meg a konzolon a képelemzés egyes hatókörei esetében. Javasoljuk, hogy ezt a metódust a try/catch blokkban hívja meg

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_analyzelocal_analyze)]

### <a name="display-results"></a>Eredmények megjelenítése

A fenti metódus hívása egy ImageAnalysis objektumot ad vissza, amely az összes kinyert információt tartalmazza. Egy adott vizualizáció funkció részleteinek kinyomtatásához a következőhöz hasonló kódrészletet használhat.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_analyzelocal_display)]

A megjelenítési beállítások teljes készletét a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/ComputerVisionQuickstart.java) tekintheti meg.

## <a name="run-the-application"></a>Az alkalmazás futtatása

Az alkalmazást az alábbiakkal hozhatja létre:

```console
gradle build
```

Futtassa az alkalmazást `gradle run` a paranccsal:

```console
gradle run
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha Cognitive Services-előfizetést szeretne törölni, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek.

* [Portál](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan használhatja a Computer Vision Java-függvénytárat a feladatok elvégzésére. Ezután tekintse át a dokumentációt, és ismerkedjen meg a könyvtárral.

> [!div class="nextstepaction"]
>[Computer Vision referenciája (Java)](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/computervision?view=azure-java-stable)

* [Mi az Computer Vision?](../Home.md)
* A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/ComputerVisionQuickstart.java)található.