---
title: Mély tanulási neurális hálózat betanítása a kerasz
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan lehet betanítani és regisztrálni a TensorFlow-on futó kerasz Deep neurális hálózati besorolási modellt Azure Machine Learning szolgáltatás használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: peterlu
ms.date: 08/01/2019
ms.custom: seodec18
ms.openlocfilehash: e7646330d9d89d5257a991b5095b7b6814aa3ba9
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68966818"
---
# <a name="train-and-register-a-keras-classification-model-with-azure-machine-learning-service"></a>Kerasz besorolási modell betanítása és regisztrálása Azure Machine Learning szolgáltatással

Ez a cikk bemutatja, hogyan lehet betanítani és regisztrálni a TensorFlow-re épülő kerasz-besorolási modellt Azure Machine Learning szolgáltatás használatával. A szolgáltatás a népszerű [MNIST](http://yann.lecun.com/exdb/mnist/) -adatkészletet használja a kézzel írt számjegyek besorolására egy olyan Deep neurális hálózat (DNN) használatával, amely a [TensorFlow](https://www.tensorflow.org/overview)-on futó [kerasz Python-kódtár](https://keras.io) használatával készült.

A kerasz egy magas szintű neurális hálózati API, amely a fejlesztés egyszerűsítése érdekében más népszerű DNN-keretrendszerek felett is futtatható. A Azure Machine Learning szolgáltatással rugalmas felhőalapú számítási erőforrásokkal gyorsan bővítheti a betanítási feladatokat. Nyomon követheti a képzések futtatását, a verziók modelljeit, a modellek üzembe helyezését és még sok mást is.

Függetlenül attól, hogy a kerasz-modellt fejleszti az alapoktól, vagy egy meglévő modellt hoz létre a felhőben, Azure Machine Learning a szolgáltatás segítséget nyújt a termelésre kész modellek létrehozásában.

A gépi tanulás és a mély tanulás közötti különbségekkel kapcsolatos információkat a [fogalmi cikkben](concept-deep-learning-vs-machine-learning.md) találja.

## <a name="prerequisites"></a>Előfeltételek

Futtassa ezt a kódot ezen környezetek bármelyikén:

 - Azure Machine Learning notebook VM – nincs szükség letöltésre vagy telepítésre

     - Fejezze be [az oktatóanyagot: Az SDK-val](tutorial-1st-experiment-sdk-setup.md) és a minta adattárral előre betöltött dedikált jegyzetfüzet-kiszolgáló létrehozásához beállíthatja a környezetet és a munkaterületet.
    - A notebook-kiszolgáló Samples (minták) mappájában keresse meg a befejezett és kibontott jegyzetfüzetet, ehhez a következő könyvtárra navigálva: **útmutató – használat – azureml > képzés – Deep-learning > Train-hiperparaméter-Tune-Deploy-with-kerasz** Folder.

 - Saját Jupyter Notebook-kiszolgáló

    - [Telepítse a Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)-t.
    - [Hozzon létre egy munkaterület-konfigurációs fájlt](how-to-configure-environment.md#workspace).
    - [A minta parancsfájl fájljainak letöltése](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras) `mnist-keras.py` és`utils.py`

    Az útmutató egy befejezett [Jupyter notebook verzióját](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb) is megtalálhatja a GitHub-minták lapon. A jegyzetfüzet tartalmaz kibővített szakaszt az intelligens hiperparaméter hangolás, a modell üzembe helyezése és a notebook widgetek számára.

## <a name="set-up-the-experiment"></a>A kísérlet beállítása

Ez a szakasz a betanítási kísérletet a szükséges Python-csomagok betöltésével, egy munkaterület inicializálásával, egy kísérlet létrehozásával, valamint a betanítási adat és a betanítási parancsfájlok feltöltésével állítja be.

### <a name="import-packages"></a>Csomagok importálása

Először importálja a szükséges Python-kódtárakat.

```Python
import os
import urllib
import shutil
import azureml

from azureml.core import Experiment
from azureml.core import Workspace, Run

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>Munkaterület inicializálása

A [Azure Machine learning szolgáltatás munkaterület](concept-workspace.md) a szolgáltatás legfelső szintű erőforrása. Központi helyet biztosít az összes létrehozott összetevővel való együttműködéshez. A Python SDK-ban egy [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) objektum létrehozásával érheti el a munkaterület összetevőit.

Hozzon létre egy munkaterület- `config.json` objektumot az [Előfeltételek szakaszban](#prerequisites)létrehozott fájlból.

```Python
ws = Workspace.from_config()
```

### <a name="create-an-experiment"></a>Kísérlet létrehozása

Hozzon létre egy kísérletet és egy mappát a betanítási szkriptek tárolásához. Ebben a példában hozzon létre egy "kerasz-mnist" nevű kísérletet.

```Python
script_folder = './keras-mnist'
os.makedirs(script_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='keras-mnist')
```

### <a name="upload-dataset-and-scripts"></a>Adatkészlet és parancsfájlok feltöltése

Az [adattár](how-to-access-data.md) olyan hely, ahol az adatok tárolása és elérése az adatoknak a számítási célra való csatlakoztatásával vagy másolásával történik. Mindegyik munkaterület alapértelmezett adattárt biztosít. Töltse fel az adatok és a betanítási szkripteket az adattárba, hogy azok könnyen elérhetők legyenek a betanítás során.

1. Töltse le helyileg a MNIST adatkészletet.

    ```Python
    os.makedirs('./data/mnist', exist_ok=True)

    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz', filename = './data/mnist/train-images.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz', filename = './data/mnist/train-labels.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename = './data/mnist/test-images.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz', filename = './data/mnist/test-labels.gz')
    ```

1. Töltse fel a MNIST-adatkészletet az alapértelmezett adattárba.

    ```Python
    ds = ws.get_default_datastore()
    ds.upload(src_dir='./data/mnist', target_path='mnist', overwrite=True, show_progress=True)
    ```

1. Töltse fel a kerasz-betanítási szkriptet `keras_mnist.py`, és a segítő `utils.py`fájlt.

    ```Python
    shutil.copy('./keras_mnist.py', script_folder)
    shutil.copy('./utils.py', script_folder)
    ```

## <a name="create-a-compute-target"></a>Hozzon létre egy számítási célnak

Hozzon létre egy számítási célt a TensorFlow-feladatokhoz a futtatásához. Ebben a példában hozzon létre egy GPU-kompatibilis Azure Machine Learning számítási fürtöt.

```Python
cluster_name = "gpucluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6',
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

A számítási célokkal kapcsolatos további információkért tekintse meg a [Mi az a számítási cél](concept-compute-target.md) című cikket.

## <a name="create-a-tensorflow-estimator-and-import-keras"></a>TensorFlow-kalkulátor létrehozása és kerasz importálása

A [TensorFlow kalkulátor](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) egyszerű módszert kínál a TensorFlow-betanítási feladatok számítási célra való elindítására. Mivel a kerasz a TensorFlow-on fut, használhatja a TensorFlow-kalkulátort, és importálhatja a kerasz `pip_packages` könyvtárat az argumentum használatával.

A TensorFlow kalkulátor az általános [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) osztályon keresztül valósul meg, amely bármely keretrendszer támogatásához használható. Emellett hozzon létre egy `script_params` szótárt, amely tartalmazza a DNN hiperparaméter beállításait. További információ a képzési modellekről az általános kalkulátor használatával: [modellek betanítása Azure Machine learning a kalkulátor használatával](how-to-train-ml-models.md)

```Python
script_params = {
    '--data-folder': ds.path('mnist').as_mount(),
    '--batch-size': 50,
    '--first-layer-neurons': 300,
    '--second-layer-neurons': 100,
    '--learning-rate': 0.001
}

est = TensorFlow(source_directory=script_folder,
                 entry_script='keras_mnist.py',
                 script_params=script_params,
                 compute_target=compute_target,
                 pip_packages=['keras', 'matplotlib'],
                 use_gpu=True)
```

## <a name="submit-a-run"></a>Futtatás küldése

A [Run objektum](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) biztosítja a felületet a futtatási előzményekhez, miközben a feladatot futtatja, és a művelet befejeződött.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

A Futtatás végrehajtásakor a következő szakaszokon halad végig:

- **Felkészülés**: A Docker-rendszerkép létrehozása a TensorFlow-kalkulátor alapján történik. A rendszer feltölti a rendszerképet a munkaterület tároló-Hivatalához, és a gyorsítótárba helyezi a későbbi futtatásokhoz. A naplók a futtatási előzményekre is továbbítva lesznek, és a folyamat figyelésére is megtekinthetők.

- **Méretezés**: A fürt akkor kísérli meg a skálázást, ha a Batch AI-fürthöz több csomópont szükséges a jelenleg elérhető futtatáshoz.

- **Futtatás**: A rendszer a parancsfájl mappájában lévő összes parancsfájlt feltölti a számítási célra, az adattárakat csatlakoztatja vagy másolja, és a entry_script hajtja végre. Az stdout és a./Logs mappa kimeneteit a rendszer a futtatási előzményekre továbbítja, és a Futtatás figyelésére használható.

- **Feldolgozás utáni**: A Futtatás./outputs mappáját a rendszer átmásolja a futtatási előzményekbe.

## <a name="register-the-model"></a>Regisztrálja a modellt

Miután betöltötte a DNN modellt, regisztrálhatja azt a munkaterületen. A modell regisztrálása lehetővé teszi a modellek tárolását és verzióját a munkaterületen a [modell kezelésének és üzembe helyezésének](concept-model-management-and-deployment.md)egyszerűsítése érdekében.

```Python
model = run.register_model(model_name='keras-dnn-mnist', model_path='outputs/model')
```

Letöltheti a modell helyi példányát is. Ez akkor lehet hasznos, ha a modell további ellenőrzése helyileg működik. A betanítási parancsfájlban `mnist-keras.py`egy TensorFlow-megtakarítási objektum a modellt egy helyi mappába (a számítási cél számára) őrzi meg. A Futtatás objektum használatával letöltheti az adattárból származó másolatot.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben a Azure Machine Learning Service-ben betanított és regisztrált egy kerasz modellt. A modellek üzembe helyezésének megismeréséhez folytassa a modell üzembe helyezésével kapcsolatos cikket.

> [!div class="nextstepaction"]
> [Modellek üzembe helyezésének módja és helye](how-to-deploy-and-where.md)
* [Metrikák futtatása a betanítás során nyomon követése](how-to-track-experiments.md)
* [Hiperparaméterek hangolása](how-to-tune-hyperparameters.md)
* [A betanított modell üzembe helyezése](how-to-deploy-and-where.md)
* [Az Azure-ban elosztott Deep learning-képzés hivatkozási architektúrája](/azure/architecture/reference-architectures/ai/training-deep-learning)
