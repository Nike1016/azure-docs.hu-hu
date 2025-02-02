---
title: Kísérletek megjelenítése a TensorBoard használatával
titleSuffix: Azure Machine Learning service
description: Indítsa el a TensorBoard a kísérleti futtatási előzmények megjelenítéséhez, és azonosítsa a hiperparaméter hangolásának és átképzésének lehetséges területeit.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: maxluk
ms.author: maxluk
ms.date: 06/28/2019
ms.openlocfilehash: 2e4527ba167076935505b668a7879e5f1446b25e
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68856077"
---
# <a name="visualize-experiment-runs-and-metrics-with-tensorboard-and-azure-machine-learning"></a>A kísérlet futtatásának és metrikáinak megjelenítése TensorBoard és Azure Machine Learning

Ebből a cikkből megtudhatja, hogyan tekintheti meg a kísérlet futtatását és a [ `tensorboard` ](https://docs.microsoft.com/python/api/azureml-tensorboard/?view=azure-ml-py) metrikákat a TensorBoard a fő Azure Machine learning Service SDK csomagjának használatával. Miután megtekintette a kísérlet futtatását, jobban beállíthatja és áttaníthatja a gépi tanulási modelleket.

A [TensorBoard](https://www.tensorflow.org/tensorboard/r1/overview) egy webalkalmazás-csomag, amely a kísérlet szerkezetének és teljesítményének vizsgálatára és megismerésére szolgál.

A TensorBoard Azure Machine Learning kísérletekkel való elindításának módja a kísérlet típusától függ:
+ Ha a kísérlet natív módon kiírja a TensorBoard által felhasználható naplófájlokat, például a PyTorch, a Láncolter és a TensorFlow kísérleteket, akkor a [TensorBoard közvetlenül](#direct) a kísérlet futtatási előzményeiből indítható el. 

+ Olyan kísérletek esetén, amelyek nem rendelkeznek natív módon kimeneti TensorBoard (például a Scikit-Learn vagy a Azure Machine learning kísérletekkel [ `export_to_tensorboard()` ](#export) ), a futtatási előzményeket TensorBoard-naplókként és a TensorBoard indításával exportálhatja. 

## <a name="prerequisites"></a>Előfeltételek

* A TensorBoard elindításához és a kísérlet futtatási előzményeinek megtekintéséhez a kísérleteknek előzőleg engedélyeznie kell a naplózást, hogy nyomon kövessék a metrikákat és a teljesítményt.  

* A útmutatóban szereplő kód a következő környezetek bármelyikében futtatható: 

    * Azure Machine Learning notebook VM – nincs szükség letöltésre vagy telepítésre

        * Fejezze be [az oktatóanyagot: Az SDK-val](tutorial-1st-experiment-sdk-setup.md) és a minta adattárral előre betöltött dedikált jegyzetfüzet-kiszolgáló létrehozásához beállíthatja a környezetet és a munkaterületet.

        * A notebook-kiszolgáló Samples (minták) mappájában keresse meg a következő két befejezett és kibontott jegyzetfüzetet: Navigálás ehhez a könyvtárhoz: **útmutató – használat-azureml > képzés – mélyreható tanulás**.
        * Exportálás-előzmények és futtatási előzmények. ipynb
        * tensorboard. ipynb

    * Saját Juptyer notebook-kiszolgáló
          * [Telepítse a Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) -t `tensorboard` a további
          * [Hozzon létre egy Azure Machine learning szolgáltatás](how-to-manage-workspace.md)munkaterületet.  
          * [Hozzon létre egy munkaterület-konfigurációs fájlt](how-to-configure-environment.md#workspace).
  
<a name="direct"></a>
## <a name="option-1-directly-view-run-history-in-tensorboard"></a>1\. lehetőség: A futtatási előzmények közvetlen megtekintése a TensorBoard-ben

Ez a beállítás olyan kísérletekhez használható, amelyek natív módon kimeneti naplófájlokat TensorBoard, például PyTorch, láncolási és TensorFlow kísérletek. Ha ez nem igaz a kísérletre, használja helyette [a `export_to_tensorboard()` metódust](#export) .

Az alábbi mintakód a [MNIST demo kísérletet](https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py) használja a TensorFlow adattárában egy távoli számítási célpontban, Azure Machine learning a számítást. Ezután betanítjuk a modellt az SDK egyéni TensorFlow- [kalkulátorával](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py), majd elkezdjük a TensorBoard a TensorFlow-kísérlettel, vagyis egy olyan kísérlettel, amely natív módon kimenetet ad a TensorBoard.

### <a name="set-experiment-name-and-create-project-folder"></a>A kísérlet nevének beállítása és a Project mappa létrehozása

Itt nevezjük el a kísérletet, és létrehozjuk a mappát. 
 
```python
from os import path, makedirs
experiment_name = 'tensorboard-demo'

# experiment folder
exp_dir = './sample_projects/' + experiment_name

if not path.exists(exp_dir):
    makedirs(exp_dir)

```

### <a name="download-tensorflow-demo-experiment-code"></a>TensorFlow demo-kísérlet kódjának letöltése

A TensorFlow adattárának MNIST-bemutatója kiterjedt TensorBoard-kialakítással rendelkezik. Nem kell módosítania a bemutató kódját, hogy működjön a Azure Machine Learning szolgáltatással. A következő kódban letöltjük a MNIST-kódot, és elmentjük az újonnan létrehozott kísérlet mappájába.

```python
import requests
import os

tf_code = requests.get("https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py")
with open(os.path.join(exp_dir, "mnist_with_summaries.py"), "w") as file:
    file.write(tf_code.text)
```
A mnist_with_summaries. MNIST fájl teljes egészében figyelje meg, hogy vannak-e olyan sorok `tf.summary.scalar()`, `tf.summary.histogram()`amelyek `tf.summary.FileWriter()` meghívása stb. Ezek a metódusok a kísérletek csoport, napló és címke kulcs mérőszámait futtatják a futtatási előzményekben. `tf.summary.FileWriter()` Ez különösen fontos, mivel a naplózott kísérleti mérőszámokból származó adatokat szerializálja, ami lehetővé teszi a TensorBoard számára a vizualizációk létrehozását.

 ### <a name="configure-experiment"></a>Kísérlet konfigurálása

A következőben konfiguráljuk a kísérletet, és beállítja a címtárakat a naplókhoz és az adatkezeléshez. A rendszer feltölti ezeket a naplókat az összetevő-szolgáltatásba, amelyet később TensorBoard is elérhet.

>[!Note]
> Ehhez a TensorFlow-példához telepítenie kell a TensorFlow-t a helyi gépre. A TensorBoard modulnak (azaz a TensorFlow-nek is) elérhetőnek kell lennie a notebook kerneléhez, mivel a helyi gép a TensorBoard-t futtatja.

```Python
import azureml.core
from azureml.core import Workspace
from azureml.core import Experiment

ws = Workspace.from_config()

# create directories for experiment logs and dataset
logs_dir = os.path.join(os.curdir, "logs")
data_dir = os.path.abspath(os.path.join(os.curdir, "mnist_data"))

if not path.exists(data_dir):
    makedirs(data_dir)

os.environ["TEST_TMPDIR"] = data_dir

# Writing logs to ./logs results in their being uploaded to Artifact Service,
# and thus, made accessible to our TensorBoard instance.
arguments_list = ["--log_dir", logs_dir]

# Create an experiment
exp = Experiment(ws, experiment_name)
```

### <a name="create-a-cluster-for-your-experiment"></a>Fürt létrehozása a kísérlethez
Létrehozunk egy AmlCompute-fürtöt a kísérlethez, de a kísérletek bármilyen környezetben létrehozhatók, és továbbra is elindítható a TensorBoard a kísérlet futtatási előzményeivel. 

```Python
from azureml.core.compute import ComputeTarget, AmlCompute

cluster_name = "cpucluster"

cts = ws.compute_targets
found = False
if cluster_name in cts and cts[cluster_name].type == 'AmlCompute':
   found = True
   print('Found existing compute target.')
   compute_target = cts[cluster_name]
if not found:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2', 
                                                           max_nodes=4)

    # create the cluster
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

compute_target.wait_for_completion(show_output=True, min_node_count=None)

# use get_status() to get a detailed status for the current cluster. 
# print(compute_target.get_status().serialize())
```

### <a name="submit-run-with-tensorflow-estimator"></a>Futtatás a TensorFlow kalkulátorral

A TensorFlow kalkulátor egyszerű módszert kínál a TensorFlow-betanítási feladatok számítási célra való elindítására. Ez az általános [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) osztályon keresztül valósul meg, amely bármely keretrendszer támogatásához használható. További információ a képzési modellekről az általános kalkulátor használatával: [modellek betanítása Azure Machine learning a kalkulátor használatával](how-to-train-ml-models.md)

```Python
from azureml.train.dnn import TensorFlow
script_params = {"--log_dir": "./logs"}

tf_estimator = TensorFlow(source_directory=exp_dir,
                          compute_target=compute_target,
                          entry_script='mnist_with_summaries.py',
                          script_params=script_params)

run = exp.submit(tf_estimator)
```

### <a name="launch-tensorboard"></a>TensorBoard elindítása

A TensorBoard a Futtatás vagy a befejezés után indítható el. A következő példában létrehozunk egy TensorBoard, `tb`amely végrehajtja a kísérlet futtatási előzményeit a `run`alkalmazásban, `start()` majd elindítja a TensorBoard a metódussal. 
  
A [TensorBoard konstruktora](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py) futtatja a futtatások tömbjét, ezért ne feledje, hogy egyetlen elemből álló tömbként adja át azt.

```python
from azureml.tensorboard import Tensorboard

tb = Tensorboard([run])

# If successful, start() returns a string with the URI of the instance.
tb.start()

# After your job completes, be sure to stop() the streaming otherwise it will continue to run. 
tb.stop()
```

<a name="export"></a>

## <a name="option-2-export-history-as-log-to-view-in-tensorboard"></a>2\. lehetőség: Előzmények exportálása naplóként a TensorBoard megtekintéséhez

A következő kód egy minta-kísérletet állít be, megkezdi a naplózási folyamatot a Azure Machine Learning futtatási előzmények API-k használatával, és a kísérleti futtatási előzményeket a vizualizációk számára a TensorBoard által használt naplókba exportálja. 

### <a name="set-up-experiment"></a>Kísérlet beállítása

A következő kód egy új kísérletet állít be, és a futtatási könyvtárat `root_run`nevezi. 

```python
from azureml.core import Workspace, Experiment
import azuremml.core

# set experiment name and run name
ws = Workspace.from_config()
experiment_name = 'export-to-tensorboard'
exp = Experiment(ws, experiment_name)
root_run = exp.start_logging()
```

Itt betöltjük a diabétesz-adathalmazt – egy beépített, kis adatkészletet, amely a scikit-Learn szolgáltatáshoz tartozik, és a teszt-és betanítási csoportokra osztja azokat.

```Python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
X, y = load_diabetes(return_X_y=True)
columns = ['age', 'gender', 'bmi', 'bp', 's1', 's2', 's3', 's4', 's5', 's6']
x_train, x_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=0)
data = {
    "train":{"x":x_train, "y":y_train},        
    "test":{"x":x_test, "y":y_test}
}
```

### <a name="run-experiment-and-log-metrics"></a>Kísérlet és naplók metrikáinak futtatása

Ebben a kódban egy lineáris regressziós modellt és a log Key mérőszámokat, az Alpha együtthatót `alpha` és az azt jelenti `mse`, hogy a futtatási előzményekben.

```Python
from tqdm import tqdm
alphas = [.1, .2, .3, .4, .5, .6 , .7]
# try a bunch of alpha values in a Linear Regression (aka Ridge regression) mode
for alpha in tqdm(alphas):
  # create child runs and fit lines for the resulting models
  with root_run.child_run("alpha" + str(alpha)) as run
 
   reg = Ridge(alpha=alpha)
   reg.fit(data["train"]["x"], data["train"]["y"])    
 
   preds = reg.predict(data["test"]["x"])
   mse = mean_squared_error(preds, data["test"]["y"])
   # End train and eval

# log alpha, mean_squared_error and feature names in run history
   root_run.log("alpha", alpha)
   root_run.log("mse", mse)
```

### <a name="export-runs-to-tensorboard"></a>A futtatások exportálása a TensorBoard

Az SDK [export_to_tensorboard ()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.export?view=azure-ml-py) metódusával exportálhatja az Azure Machine learning-kísérlet futtatási előzményeit a tensorboard-naplókba, így megtekintheti őket a tensorboard-on keresztül.  

A következő kódban létrehozjuk a mappát `logdir` az aktuális munkakönyvtárban. Ebben a mappában fogjuk exportálni a kísérlet futtatási előzményeit és `root_run` naplóit, majd megjelölik a futtatást befejezettként. 

```Python
from azureml.tensorboard.export import export_to_tensorboard
import os

logdir = 'exportedTBlogs'
log_path = os.path.join(os.getcwd(), logdir)
try:
    os.stat(log_path)
except os.error:
    os.mkdir(log_path)
print(logdir)

# export run history for the project
export_to_tensorboard(root_run, logdir)

root_run.complete()
```

>[!Note]
 Egy adott futtatást TensorBoard is exportálhat a Futtatás nevének megadásával.`export_to_tensorboard(run_name, logdir)`

TensorBoard elindítása és leállítása a kísérlet exportálási előzményeinek exportálása után a TensorBoard a [Start ()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#start-start-browser-false-) metódussal indítható el. 

```Python
from azureml.tensorboard import Tensorboard

# The TensorBoard constructor takes an array of runs, so be sure and pass it in as a single-element array here
tb = Tensorboard([], local_root=logdir, port=6006)

# If successful, start() returns a string with the URI of the instance.
tb.start()
```

Ha elkészült, győződjön meg arról, hogy a TensorBoard objektum [Leállítás ()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#stop--) metódusát hívja meg. Ellenkező esetben a TensorBoard továbbra is futni fog, amíg le nem állítja a notebook kernelét. 

```python
tb.stop()
```

## <a name="next-steps"></a>További lépések

Ebben az útmutatóban két kísérletet hozott létre, és megtanulta, hogyan indíthat el TensorBoard a futtatási előzményekkel a lehetséges hangolási és újraképzési területek azonosítása érdekében. 

* Ha elégedett a modellel, látogasson el a [modell üzembe helyezésével](how-to-deploy-and-where.md) foglalkozó cikkre. 
* További információ a [hiperparaméter hangolásáról](how-to-tune-hyperparameters.md). 
