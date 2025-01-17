---
title: 'Oktatóanyag: A Python és a TensorFlow használata Azure Functions a Machine learning-következtetések készítéséhez | Microsoft Docs'
description: Ez az oktatóanyag bemutatja, hogyan alkalmazhat TensorFlow Machine learning-modelleket Azure Functions
services: functions
author: anthonychu
manager: gwallace
ms.service: azure-functions
ms.devlang: python
ms.topic: tutorial
ms.date: 07/29/2019
ms.author: antchu
ms.custom: mvc
ms.openlocfilehash: abc7302ee59103a9cbab156b95a41b77eb95d474
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68729175"
---
# <a name="tutorial-apply-machine-learning-models-in-azure-functions-with-python-and-tensorflow"></a>Oktatóanyag: Gépi tanulási modellek alkalmazása Azure Functions Python és TensorFlow

Ez a cikk azt mutatja be, hogyan Azure Functions lehetővé teszi a Python és a TensorFlow gépi tanulási modellel való használatát a rendszerképeknek a tartalom alapján történő besorolásához.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni: 

> [!div class="checklist"]
> * Helyi környezet inicializálása a Python-Azure Functions fejlesztéséhez
> * Egyéni TensorFlow Machine learning-modell importálása egy Function alkalmazásba
> * Kiszolgáló nélküli HTTP API létrehozása, amely azt jelzi, hogy egy fénykép tartalmaz-e kutyát vagy macskát
> * Az API felhasználása egy webalkalmazásból

![A befejezett projekt képernyőképe](media/functions-machine-learning-tensorflow/functions-machine-learning-tensorflow-screenshot.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek 

Azure Functions a Pythonban való létrehozásához néhány eszközt telepítenie kell.

- [Python 3,6](https://www.python.org/downloads/release/python-360/)
- [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools)
- Kódszerkesztő, például [Visual Studio Code](https://code.visualstudio.com/)

## <a name="clone-the-tutorial-repository"></a>Az oktatóanyag-adattár klónozása

A kezdéshez nyisson meg egy terminált, és a git használatával a következő adattár klónozásával:

```console
git clone https://github.com/Azure-Samples/functions-python-tensorflow-tutorial.git
cd functions-python-tensorflow-tutorial
```

A tárház néhány mappát tartalmaz.

- *kezdés*:  Ez a munkahelyi mappa az oktatóanyaghoz
- *Befejezés*: Ez a hivatkozás végső eredménye és teljes megvalósítása
- *erőforrások*: A Machine learning-modellt és a segítő kódtárakat tartalmazza
- előtér: Egy webhely, amely meghívja a Function alkalmazást

## <a name="create-and-activate-a-python-virtual-environment"></a>Python virtuális környezet létrehozása és aktiválása

A Azure Functions a Python 3.6. x verzióját igényli. Létre fog hozni egy virtuális környezetet, amely biztosítja, hogy a szükséges Python-verziót használja.

Módosítsa az aktuális munkakönyvtárat a *Start* mappába. Ezután hozzon létre és aktiváljan egy *. venv*nevű virtuális környezetet. A Python-telepítéstől függően a Python 3,6 virtuális környezet létrehozásához szükséges parancsok eltérhetnek az alábbi utasításoktól.

#### <a name="linux-and-macos"></a>Linux és macOS:

```bash
cd start
python3.6 -m venv .venv
source .venv/bin/activate
```

#### <a name="windows"></a>Windows:

```powershell
cd start
py -3.6 -m venv .venv
.venv\scripts\activate
```

A terminál-parancssor már előre van rögzítve, `(.venv)` amely azt jelzi, hogy sikeresen aktiválta a virtuális környezetet. Győződjön meg `python` arról, hogy a virtuális környezetben valóban a Python 3.6. x.

```console
python --version
```

> [!NOTE]
> Az oktatóanyag hátralévő részében parancsokat futtathat a virtuális környezetben. Ha újra kell aktiválnia a virtuális környezetet egy terminálon, hajtsa végre a megfelelő aktiválási parancsot az operációs rendszeréhez.

## <a name="create-an-azure-functions-project"></a>Azure Functions-projekt létrehozása

A *Start* mappában használja a Azure functions Core Tools egy Python-függvény alkalmazásának inicializálásához.

```console
func init --worker-runtime python
```

A Function alkalmazás egy vagy több Azure Functions tartalmaz. Nyissa meg a *Start* mappát egy szerkesztőben, és vizsgálja meg a tartalmat.

- [*Local. Settings. JSON*](functions-run-local.md#local-settings-file): Helyi fejlesztéshez használt Alkalmazásbeállítások
- [*Host. JSON*](functions-host-json.md): A Azure Functions gazdagép és a bővítmények beállításait tartalmazza
- [*követelmények. txt*](functions-reference-python.md#python-version-and-package-management): Az alkalmazás által igényelt Python-csomagokat tartalmazza

## <a name="create-an-http-function"></a>HTTP-függvény létrehozása

Az alkalmazáshoz egyetlen HTTP API-végpont szükséges, amely egy képurl-címet használ bemenetként, és visszaadja annak előrejelzését, hogy a rendszerkép tartalmaz-e kutyát vagy macskát.

A terminálban használja a Azure Functions Core Tools a *besorolás*nevű új http-függvény előkészítéséhez.

```console
func new --language python --template HttpTrigger --name classify
```

Létrejön egy *besorolás* nevű új mappa, amely két fájlt tartalmaz.

- *init.a\_: \_ \_\_* A fő függvény fájlja
- *function. JSON*:  A függvény triggerét és a hozzá tartozó bemeneti és kimeneti kötéseket leíró fájl

### <a name="run-the-function"></a>A függvény futtatása

Az aktivált Python virtuális környezettel rendelkező terminálon indítsa el a Function alkalmazást.

```console
func start
```

Nyisson meg egy böngészőt, és navigáljon a következő URL-címre. A függvénynek végre kell hajtania és vissza kell adni a *Hello Azure* -t!

```
http://localhost:7071/api/classify?name=Azure
```

A `Ctrl-C` Function alkalmazás leállításához használja a következőt:.

## <a name="import-the-tensorflow-model"></a>A TensorFlow-modell importálása

Egy előre elkészített TensorFlow modellt fog használni, amely az Azure Custom Vision Service-ból lett betanítva és exportálva.

> [!NOTE]
> Ha a Custom Vision Service ingyenes szintjével szeretné létrehozni a sajátját, kövesse a [minta projekt adattárának utasításait](https://github.com/Azure-Samples/functions-python-tensorflow-tutorial/blob/master/train-custom-vision-model.md).

A modell két fájlból áll a *< REPOSITORY_ROOT >/Resources/Model* mappában: *Model. db* és *labels. txt*. Másolja őket a *besorolási* függvény mappájába.

#### <a name="linux-and-macos"></a>Linux és macOS:

```bash
cp ../resources/model/* classify
```

#### <a name="windows"></a>Windows:

```powershell
copy ..\resources\model\* classify
```

Ügyeljen arra, hogy tartalmazza \* a fenti parancs utasításait. Győződjön meg arról, hogy a *besorolás* most a *Model. PB* és a *labels. txt*nevű fájlokat tartalmazza.

## <a name="add-the-helper-functions-and-dependencies"></a>Segítő függvények és függőségek hozzáadása

Egyes segítő függvények a bemeneti rendszerkép előkészítéséhez és a TensorFlow használatával történő előrejelzéshez a *Predict.py* nevű fájlban találhatók a Resources mappában. Másolja ezt a fájlt a *besorolási* függvény mappájába.

#### <a name="linux-and-macos"></a>Linux és macOS:

```bash
cp ../resources/predict.py classify
```

#### <a name="windows"></a>Windows:

```powershell
copy ..\resources\predict.py classify
```

Győződjön meg arról, hogy a *besorolás* most tartalmaz egy *Predict.py*nevű fájlt.

### <a name="install-dependencies"></a>Függőségek telepítése

A Segéd-függvénytárban vannak olyan függőségek, amelyeket telepíteni kell. Nyissa meg a *Start/követelmények. txt* fájlt a szerkesztőben, és adja hozzá a következő függőségeket a fájlhoz.

```txt
tensorflow
Pillow
requests
```

Mentse a fájlt.

Az aktivált virtuális környezettel rendelkező terminálon futtassa a következő parancsot a *Start* mappában a függőségek telepítéséhez. Néhány telepítési lépés eltarthat néhány percig.

```console
pip install --no-cache-dir -r requirements.txt
```

### <a name="caching-the-model-in-global-variables"></a>A modell gyorsítótárazása globális változókban

A szerkesztőben nyissa meg a *Predict.py* , és `_initialize` tekintse meg a fájl tetején található függvényt. Figyelje meg, hogy a TensorFlow modell betöltődik a lemezről a függvény első futtatásakor és globális változókba mentésekor. A rendszer kihagyja a lemezről történő betöltést a `_initialize` függvény későbbi végrehajtásakor. A memóriabeli modell gyorsítótárazása ezzel a technikával felgyorsítja a későbbi előrejelzéseket.

A globális változókra vonatkozó további információkért tekintse meg a [Azure functions Python fejlesztői útmutatóját](functions-reference-python.md#global-variables).

## <a name="update-function-to-run-predictions"></a>Frissítési függvény az előrejelzések futtatásához

Nyissa meg a *\_besorolás/\_init\_\_.* a szerkesztőt. Importálja a korábban ugyanahhoz a mappához hozzáadott *előrejelző* könyvtárat. Adja hozzá a `import` következő utasításokat a már a fájlban lévő többi importálás alatt.

```python
import json
from .predict import predict_image_from_url
```

Cserélje le a Function sablon kódját a következőre.

```python
def main(req: func.HttpRequest) -> func.HttpResponse:
    image_url = req.params.get('img')
    results = predict_image_from_url(image_url)

    headers = {
        "Content-type": "application/json",
        "Access-Control-Allow-Origin": "*"
    }
    return func.HttpResponse(json.dumps(results), headers = headers)
```

Győződjön meg arról, hogy a módosítások mentéséhez.

Ez a függvény egy nevű `img`lekérdezési karakterlánc-paraméterben fogadja a RENDSZERKÉP URL-címét. Meghívja `predict_image_from_url` a segítő könyvtárat, amely letölti a rendszerképet, és visszaadja az előrejelzést a TensorFlow-modell használatával. A függvény ezután egy HTTP-választ ad vissza az eredményekkel.

Mivel a http-végpontot egy másik tartományban található weblap hívja meg, a http-válasz egy `Access-Control-Allow-Origin` fejlécet tartalmaz, amely megfelel a böngésző eltérő eredetű erőforrás-megosztási (CORS) követelményeinek.

> [!NOTE]
> Éles alkalmazásban váltson `*` a weblap speciális forrására a további biztonság érdekében.

### <a name="run-the-function-app"></a>A Function alkalmazás futtatása

Győződjön meg arról, hogy a Python virtuális környezet továbbra is aktiválva van, és indítsa el a Function alkalmazást a következő parancs használatával.

```console
func start
```

Egy böngészőben nyissa meg ezt az URL-címet, amely meghívja a függvényt egy Cat-fénykép URL-címével. Győződjön meg arról, hogy a rendszer érvényes előrejelzési eredményt ad vissza.

```
http://localhost:7071/api/classify?img=https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat1.png
```

A Function alkalmazás futásának megtartása.

### <a name="run-the-web-app"></a>A webalkalmazás futtatása

Létezik egy egyszerű webalkalmazás a *frontend* mappában, amely a http API-t használja a Function alkalmazásban.

Nyisson meg egy *különálló* terminált, és váltson át a *frontend* mappára. Indítsa el a HTTP-kiszolgálót a Python 3,6-mel.

#### <a name="linux-and-macos"></a>Linux és macOS:

```bash
cd <FRONT_END_FOLDER>
python3.6 -m http.server
```

#### <a name="windows"></a>Windows:

```powershell
cd <FRONT_END_FOLDER>
py -3.6  -m http.server
```

Egy böngészőben nyissa meg a HTTP-kiszolgáló URL-címét, amely a terminálon jelenik meg. Meg kell jelennie egy webalkalmazásnak. Adja meg a következő fénykép URL-címek egyikét a szövegmezőbe. A nyilvánosan elérhető Cat-vagy Dog-fényképek URL-címét is használhatja.

- `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat1.png`
- `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat2.png`
- `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/dog1.png`
- `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/dog2.png`

Amikor a Submit (küldés) gombra kattint, a rendszer meghívja a Function alkalmazást, és egy eredmény jelenik meg az oldalon.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Az oktatóanyag teljes egészében helyileg fut a gépen, így nincs szükség Azure-erőforrásokra vagy-szolgáltatásokra.

## <a name="next-steps"></a>További lépések

Ebből az oktatóanyagból megtudhatta, hogyan hozhat létre és szabhat testre egy HTTP API-t Azure Functions segítségével, hogy előrejelzéseket TensorFlow modell használatával. Azt is megtanulta, hogyan hívhatja meg az API-t egy webalkalmazásból.

Az oktatóanyagban található technikák használatával bármilyen bonyolultságú API-t építhet ki, miközben a Azure Functions által biztosított kiszolgáló nélküli számítási modellen fut.

A Function alkalmazás Azure-ban való üzembe helyezéséhez használja a [Azure functions Core Tools](./functions-run-local.md#publish) vagy a [Visual Studio Code](https://code.visualstudio.com/docs/python/tutorial-azure-functions)-ot.

> [!div class="nextstepaction"]
> [Azure Functions Python fejlesztői útmutató](./functions-reference-python.md)
