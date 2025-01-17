---
title: 'Gyors útmutató: A Pythonhoz készült Face ügyféloldali kódtár | Microsoft Docs'
description: Ismerkedés a Pythonhoz készült Face ügyféloldali kódtáraval...
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 07/26/2019
ms.author: pafarley
ms.openlocfilehash: 011345cca1ed1c763a628c94401320862182c9cc
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707368"
---
# <a name="quickstart-face-client-library-for-python"></a>Gyors útmutató: A Pythonhoz készült Face ügyféloldali kódtár

Ismerkedés a Pythonhoz készült Face ügyféloldali kódtáraval. Az alábbi lépéseket követve telepítheti a csomagot, és kipróbálhatja az alapszintű feladatokhoz tartozó példa kódját. A Face API szolgáltatás lehetővé teszi a speciális algoritmusok elérését a képeken található emberi arcok észleléséhez és felismeréséhez.

A Pythonhoz készült Face ügyféloldali kódtár a következőre használható:

* Arcok felismerése a képeken
* Hasonló arcok keresése
* Személy csoport létrehozása és betanítása
* Arc azonosítása
* Pillanatkép készítése az adatok áttelepítéséhez

[](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/?view=azure-python) | A dokumentációs[könyvtár forráskód](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-face) | [-csomagjához (PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-face/) | tartozó[minták](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=Face&sort=0)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>Beállítás

### <a name="create-a-face-azure-resource"></a>Face Azure-erőforrás létrehozása

Az Azure Cognitive Services a-ra előfizetett Azure-erőforrások képviselik. Hozzon létre egy erőforrást a helyi gépen a [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) vagy az [Azure parancssori](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) felület használatával. További lehetőségek:

* A [próbaverziós kulcs](https://azure.microsoft.com/try/cognitive-services/#decision) ingyenes hét napig érvényes. A regisztráció után elérhető lesz az [Azure webhelyén](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Az erőforrás megtekintése a [Azure Portal](https://portal.azure.com/)

Miután beolvasott egy kulcsot a próbaverziós előfizetésből vagy erőforrásból, [hozzon létre egy környezeti változót](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) a (z) nevű `FACE_SUBSCRIPTION_KEY`kulcshoz.
 
### <a name="create-a-new-python-application"></a>Új Python-alkalmazás létrehozása

Hozzon létre egy új&mdash;Python-parancsfájl*Quickstart-file.py*, például:. Ezután nyissa meg a kívánt szerkesztőben vagy IDE, és importálja a következő könyvtárakat.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_imports)]

Ezután hozzon létre változókat az erőforrás Azure-végpontjának és-kulcsának létrehozásához. Előfordulhat, hogy módosítania kell a végpont (`westus`) első részét, hogy az megfeleljen az előfizetésnek.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_subvars)]

> [!NOTE]
> Ha a környezeti változót az alkalmazás elindítása után hozta létre, akkor a változó eléréséhez be kell állítania és újra meg kell nyitnia a szerkesztőt, az IDE-t vagy a shellt.

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

Az ügyféloldali kódtár a használatával telepíthető:

```console
pip install --upgrade azure-cognitiveservices-Face
```

## <a name="object-model"></a>Objektummodell

A következő osztályok és felületek a Face Python SDK főbb funkcióit kezelik.

|Name (Név)|Leírás|
|---|---|
|[FaceClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.faceclient?view=azure-python) | Ez az osztály a Face szolgáltatás használatára vonatkozó engedélyt jelöli, és minden arc funkcióhoz szüksége van rá. Ezt az előfizetési adatok alapján hozza létre, és más osztályok példányainak előállítására használja. |
|[FaceOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python)|Ez az osztály az emberi arcokkal elvégezhető alapvető észlelési és felismerési feladatokat kezeli. |
|[DetectedFace](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.models.detectedface?view=azure-python)|Ez az osztály a rendszerkép egyetlen arca által észlelt összes adatmennyiséget jelképezi. Ezzel az oldallal kapcsolatos részletes információkat kérhet le.|
|[FaceListOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.facelistoperations?view=azure-python)|Ez az osztály kezeli a felhőben tárolt **FaceList** -szerkezeteket, amelyek az arcok válogatott készleteit tárolják. |
|[PersonGroupPersonOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.persongrouppersonoperations?view=azure-python)| Ez az osztály kezeli a felhőalapú tárolt **személyek** szerkezetét, amelyek egyetlen személyhez tartozó arcok készletét tárolják.|
|[PersonGroupOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.persongroupoperations?view=azure-python)| Ez az osztály kezeli a felhőben tárolt **PersonGroup** -szerkezeteket, amelyek a különböző személyre kiterjedő objektumok készletét tárolják. |
|[ShapshotOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.snapshotoperations?view=azure-python)|Ez az osztály kezeli a pillanatkép funkciót; a használatával ideiglenesen mentheti az összes felhőalapú Arcfelismerés, és áttelepítheti az adott adatait egy új Azure-előfizetésbe. |

## <a name="code-examples"></a>Példák a kódokra

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következő feladatokat a Pythonhoz készült Face ügyféloldali kódtár használatával:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Képeken lévő arcok észlelése](#detect-faces-in-an-image)
* [Hasonló arcok keresése](#find-similar-faces)
* [Személy csoport létrehozása és betanítása](#create-and-train-a-person-group)
* [Arc azonosítása](#identify-a-face)
* [Pillanatkép készítése az adatok áttelepítéséhez](#take-a-snapshot-for-data-migration)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

> [!NOTE]
> Ez a rövid útmutató azt feltételezi, hogy [létrehozott egy környezeti változót](../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) a ( `FACE_SUBSCRIPTION_KEY`z) nevű Face kulcshoz.

Ügyfelet hoz létre a végponttal és a kulccsal. Hozzon létre egy [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) objektumot a kulccsal, és használja a végpontján egy [FaceClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.faceclient?view=azure-python) objektum létrehozásához.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_auth)]

## <a name="detect-faces-in-an-image"></a>Arcok felismerése a képeken

A következő kód egy távoli rendszerképben észlel egy arcot. Kinyomtatja az észlelt Face AZONOSÍTÓját a konzolon, és a program memóriájában tárolja azt is. Ezt követően észleli a több emberrel rendelkező képekben lévő arcokat, és kiírja az azonosítókat a konzolra. A [detect_with_url](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python#detect-with-url-url--return-face-id-true--return-face-landmarks-false--return-face-attributes-none--recognition-model--recognition-01---return-recognition-model-false--detection-model--detection-01---custom-headers-none--raw-false----operation-config-) metódus paramétereinek módosításával különböző adatokat adhat vissza az egyes [DetectedFace](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.models.detectedface?view=azure-python) objektumokhoz.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_detect)]

További észlelési helyzetekben a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/Face/FaceQuickstart.py) tekintse meg a mintakód című témakört.

## <a name="find-similar-faces"></a>Hasonló arcok keresése

A következő kód egyetlen észlelt arcot használ, és más arcokban keres meg találatokat. Ha egyezést talál, az összehasonlított arc téglalap koordinátáit kiírja a-konzolra. 

### <a name="find-matches"></a>Egyezések keresése

Először is futtassa a kódot a fenti szakaszban ([egy képen lévő arcok észlelése](#detect-faces-in-an-image)), hogy egyetlen arc hivatkozását mentse. Ezután futtassa a következő kódot, hogy egy csoport rendszerképében több arc hivatkozása legyen.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_detectgroup)]

Ezután adja hozzá a következő kódrészletet a csoport első arc példányainak megkereséséhez. A viselkedés módosításához tekintse meg a [find_similar](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python#find-similar-face-id--face-list-id-none--large-face-list-id-none--face-ids-none--max-num-of-candidates-returned-20--mode--matchperson---custom-headers-none--raw-false----operation-config-) metódust.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_findsimilar)]

### <a name="print-matches"></a>Egyezések nyomtatása

A következő kód használatával nyomtassa ki a megfeleltetés részleteit a-konzolra.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_findsimilar_print)]

## <a name="create-and-train-a-person-group"></a>Személy csoport létrehozása és betanítása

A következő kód egy **PersonGroup** hoz létre három különböző **személy** objektummal. Az egyes **személyeket** például a lemezképek halmazával társítja, majd az összes személyt felismerheti. 

### <a name="create-persongroup"></a>PersonGroup létrehozása

Ennek a forgatókönyvnek a lépéseihez a következő rendszerképeket kell mentenie a projekt gyökérkönyvtárba: https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images.

A képek ezen csoportja három különböző személynek megfelelő arc-rendszerképeket tartalmaz. A kód három **személy** objektumot határoz meg, és társítja azokat a `woman`, `man`, és `child`rendszerű képfájlokkal.

Miután beállította a lemezképeket, Definiáljon egy címkét a szkript tetején a létrehozandó **PersonGroup** objektumhoz.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroupvars)]

Ezután adja hozzá a következő kódot a szkript aljához. Ez a kód egy **PersongGroup** és három **személy** objektumot hoz létre.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_create)]

### <a name="assign-faces-to-persons"></a>Arcok társítása személyekhez

A következő kód a képeket előtagjaként rendezi, észleli az arcokat, és hozzárendeli az arcokat az egyes **személyek** objektumokhoz.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_assign)]

### <a name="train-persongroup"></a>PersonGroup betanítása

Az arcok hozzárendelése után be kell tanítania a **PersonGroup** , hogy azonosítani tudja az egyes **személy** objektumaihoz kapcsolódó vizuális funkciókat. A következő kód meghívja az aszinkron betanítási módszert, és lekérdezi az eredményt, kinyomtatja az állapotot a konzolra.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_train)]

## <a name="identify-a-face"></a>Arc azonosítása

A következő kód több képpel rendelkező képet helyez el, és megkeresi az egyes személyek identitását a képen. Összehasonlítja az észlelt **PersonGroup**, a különböző **személy** objektumokat tartalmazó adatbázisokat, amelyek mindegyikéhez több arca van társítva. 

> [!IMPORTANT]
> A példa futtatásához először futtatnia kell a kódot [egy személy csoport létrehozása és betanítása csoportban](#create-and-train-a-person-group).

### <a name="get-a-test-image"></a>Tesztelési rendszerkép beolvasása

A következő kód a projekt gyökerében található, amely egy _test-Image-person-Group. jpg_ képet keres, és észleli a képen látható arcokat. Ezt a képet a **PersonGroup** -felügyelethez használt rendszerképekkel találja https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images:.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_identify_testimage)]

### <a name="identify-faces"></a>Arcok azonosítása

Az **azonosítási** módszer az észlelt arcok tömbjét veszi fel, és összehasonlítja őket egy **PersonGroup**. Ha egy **személynek**észlelt arcot tud megfeleltetni, a rendszer menti az eredményt. Ez a kód részletes találatokat nyomtat a-konzolra.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_identify)]

## <a name="take-a-snapshot-for-data-migration"></a>Pillanatkép készítése az adatok áttelepítéséhez

A pillanatképek funkció lehetővé teszi a mentett Arcfelismerés, például a betanított **PersonGroup**áthelyezését egy másik Azure Cognitive Services Face-előfizetésbe. Érdemes lehet ezt a funkciót használni, ha például egy ingyenes próbaverziós előfizetéssel létrehozott egy **PersonGroup** objektumot, és most szeretné áttelepíteni egy fizetős előfizetésre. A pillanatképek szolgáltatás széles körű áttekintéséhez tekintse [meg az Arcfelismerés](../Face-API-How-to-Topics/how-to-migrate-face-data.md) áttelepítését ismertető cikket.

Ebben a példában a [személy csoport létrehozása és](#create-and-train-a-person-group)betanítása során létrehozott **PersonGroup** kell áttelepítenie. Először hajtsa végre az adott szakaszt, vagy használjon saját Face adatszerkezet (eke) t.

### <a name="set-up-target-subscription"></a>Cél-előfizetés beállítása

Először is rendelkeznie kell egy másik Azure-előfizetéssel, egy Face erőforrással. ezt a [beállítás](#setting-up) szakasz lépéseit követve teheti meg. 

Ezután hozza létre a következő változókat a parancsfájl teteje közelében. Emellett új környezeti változókat is létre kell hoznia az Azure-fiók előfizetés-AZONOSÍTÓJÁRA, valamint az új (cél) fiók kulcs-és előfizetés-AZONOSÍTÓját. 

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshotvars)]

### <a name="authenticate-target-client"></a>A célként megadott ügyfél hitelesítése

A parancsfájlban később mentse a jelenlegi ügyfél-objektumot a forrás-ügyfélként, majd hitelesítse a cél előfizetéséhez tartozó új ügyfél-objektumot. 

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_auth)]

### <a name="use-a-snapshot"></a>Pillanatkép használata

A pillanatképek többi művelete egy aszinkron függvényen belül történik. 

1. Első lépésként készítse **el a** pillanatképet, amely az eredeti előfizetése adatait egy ideiglenes Felhőbeli helyre menti. Ez a metódus egy azonosítót ad vissza, amelyet a művelet állapotának lekérdezéséhez használ.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_take)]

1. Ezután kérdezze le az azonosítót, amíg a művelet be nem fejeződik.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_wait)]

    Ez a kód a `wait_for_operation` függvény használatát végzi el, amelyet külön kell meghatároznia:

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_waitforop)]

1. Térjen vissza az aszinkron függvényhez. Az **Apply** művelet használatával írja be az arc adatait a cél előfizetésbe. Ez a metódus egy azonosítót is visszaad.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_apply)]

1. Ismét a `wait_for_operation` függvény használatával kérdezheti le az azonosítót, amíg a művelet be nem fejeződik.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_wait2)]

Miután elvégezte ezeket a lépéseket, elérheti a Face adatok szerkezeteit az új (cél) előfizetésből.

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást `python` a gyors üzembe helyezési fájlban található paranccsal.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha Cognitive Services-előfizetést szeretne törölni, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek.

* [Portál](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#clean-up-resources)
* [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli#clean-up-resources)

Ha ebben a rövid útmutatóban létrehozott egy **PersonGroup** , és törölni szeretné, futtassa a következő kódot a parancsfájlban:

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_deletegroup)]

Ha ebben a rövid útmutatóban az adatok áttelepítve lettek a pillanatkép-szolgáltatással, törölnie kell a megcélzott előfizetésbe mentett **PersonGroup** is.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_deletetargetgroup)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan használhatja a Javához készült Face Library-t a feladatok elvégzésére. Ezután tekintse át a dokumentációt, és ismerkedjen meg a könyvtárral.

> [!div class="nextstepaction"]
> [Face API-hivatkozás (Python)](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/?view=azure-python)

* [Mi a Face API?](../overview.md)
* A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/Face/FaceQuickstart.py)található.