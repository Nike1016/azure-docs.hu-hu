---
title: ML-folyamatok létrehozása, futtatása, & nyomon követése
titleSuffix: Azure Machine Learning service
description: Hozzon létre, és futtassa a machine learning-folyamat, az Azure Machine Learning-SDK Pythonhoz készült. A gépi tanulási (ML) fázisokat összevarró munkafolyamatok létrehozásához és kezeléséhez használjon ML-es folyamatokat. Ezek a fázisok közé tartozik az adatelőkészítés, a modell betanítása, a modell üzembe helyezése, valamint a következtetések/pontozás.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.author: sanpil
author: sanpil
ms.date: 08/09/2019
ms.custom: seodec18
ms.openlocfilehash: 1e68f60880e09dfeb46641f40eca12e1fc0560bc
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/12/2019
ms.locfileid: "68950424"
---
# <a name="create-and-run-machine-learning-pipelines-with-azure-machine-learning-sdk"></a>Gépi tanulási folyamatokat hozhat létre és futtathat Azure Machine Learning SDK-val

Ebből a cikkből megtudhatja, hogyan hozhat létre, tehet közzé, futtathat és követhet nyomon egy [gépi tanulási folyamatot](concept-ml-pipelines.md) a [Azure Machine learning SDK](https://aka.ms/aml-sdk)használatával.  A **ml** -folyamatok segítségével hozzon létre egy olyan munkafolyamatot, amely különböző ml fázisokat egyesít, majd tegye közzé ezt a folyamatot a Azure Machine learning munkaterületen, hogy később hozzáférjen, vagy más használatával osszon meg.  A ML-folyamatok ideálisak kötegelt pontozási forgatókönyvekhez, különböző számítások használatával, az újbóli Futtatás helyett a lépéseket, valamint az ML-munkafolyamatok másokkal való megosztását. 

Habár az [Azure](https://docs.microsoft.com/en-us/azure/devops/pipelines/targets/azure-machine-learning?context=azure%2Fmachine-learning%2Fservice%2Fcontext%2Fml-context&view=azure-devops&tabs=yaml) -folyamatokat más típusú folyamatokkal is használhatja, amelyek az ml-feladatok CI/CD-automatizálására szolgálnak, az adott típusú folyamat soha nem tárolódik a munkaterületen. [Hasonlítsa össze ezeket a különböző folyamatokat](concept-ml-pipelines.md#which-azure-pipeline-technology-should-i-use).

Egy ML-folyamat minden fázisa (például az adatelőkészítés és a modell betanítása) egy vagy több lépést is tartalmazhat.

A létrehozott ML-folyamatok a Azure Machine Learning szolgáltatás munkaterületének tagjai számára láthatók [](how-to-manage-workspace.md). 

A ML-folyamatok távoli számítási célokat használnak a számításhoz és a folyamathoz társított közbenső és végső adatok tárolásához. A támogatott [Azure Storage](https://docs.microsoft.com/azure/storage/) -helyekről olvashatnak és írhatnak az adatokról.

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki [Azure Machine learning szolgáltatás ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree).

## <a name="prerequisites"></a>Előfeltételek

* Hozzon létre egy [Azure Machine Learning-munkaterületet](how-to-manage-workspace.md) , amely tárolja az folyamat-erőforrások.

* [Konfigurálja a fejlesztési környezetet](how-to-configure-environment.md) a Azure Machine learning SDK telepítéséhez, vagy használjon egy már telepített SDK-val rendelkező [Jegyzetfüzet virtuális gépet](tutorial-1st-experiment-sdk-setup.md#azure) .

Első lépésként csatolja a munkaterületet:

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```


## <a name="set-up-machine-learning-resources"></a>Állítsa be a machine learning-erőforrások

Hozzon létre egy ML-folyamat futtatásához szükséges erőforrásokat:

* Állítsa be a folyamat lépések során szükség az adatok eléréséhez használt adattárolót.

* Konfiguráljon `DataReference` egy objektumot úgy, hogy olyan adattárolóra mutasson, amely a-ben él vagy elérhető.

* Állítsa be a [számítási céljainak](concept-azure-machine-learning-architecture.md#compute-targets) az a folyamat lépései futtathatók.

### <a name="set-up-a-datastore"></a>Egy adattár beállítása

Egy adattár tárolja az adatokat a folyamat eléréséhez. Az egyes munkaterületeken tartozik egy alapértelmezett adattárhoz. További adattárainak regisztrálhat. 

A munkaterület létrehozásakor a [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) és az [Azure Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) a munkaterülethez van csatolva. Az Azure Blob Storage-hoz való kapcsolódáshoz egy alapértelmezett adattár van regisztrálva. További információ: [a Azure Files, az Azure-blobok és az Azure-lemezek használatának](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks)meghatározása. 

```python
# Default datastore 
def_data_store = ws.get_default_datastore()

# Get the blob storage associated with the workspace
def_blob_store = Datastore(ws, "workspaceblobstore")

# Get file storage associated with the workspace
def_file_store = Datastore(ws, "workspacefilestore")

```

Töltse fel az adatok fájlok vagy könyvtárak az adattárral, hogy a folyamatok a érhető el. Ez a példa a blob Storage-t használja adattárként:

```python
def_blob_store.upload_files(
    ["./data/20news.pkl"],
    target_path="20newsgroups",
    overwrite=True)
```

Egy folyamatot egy vagy több lépésből áll. Egy lépésre egy egység egy számítási célnak futhatnak. Előfordulhat, hogy lépéseket felhasználhat adatforrásokat, és a "köztes" adatok előállításához. Egy lépéssel létrehozhat például egy modellt, a modell és a függő fájlokat egy könyvtárat, és ideiglenes adatokat. Ezek az adatok ezután a folyamat későbbi részében további lépések érhető el.

### <a name="configure-data-reference"></a>Konfigurálja a data-referencia

Létrehozott egy adatforrás, amely lehet hivatkozni a folyamat egy lépéssel bemenetként. Egy adatforrás egy adott folyamat képviseli egy [DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference) objektum. A `DataReference` él vagy egy adattár elérhető adatok objektumra mutat.

```python
blob_input_data = DataReference(
    datastore=def_blob_store,
    data_reference_name="test_data",
    path_on_datastore="20newsgroups/20news.pkl")
```

Köztes adatok (vagy egy lépés kimenetéből) képviseli egy [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) objektum. `output_data1`egy lépés kimenete jön létre, és egy vagy több jövőbeli lépés bemenetéhez használható. `PipelineData`bevezet egy, a lépések közötti függőséget, és egy implicit végrehajtási sorrendet hoz létre a folyamatban.

```python
output_data1 = PipelineData(
    "output_data1",
    datastore=def_blob_store,
    output_name="output_data1")
```

## <a name="set-up-compute-target"></a>Állítsa be a számítási célnak

Azure Machine Learning a __számítási__ (vagy __számítási cél__) kifejezés a gépi tanulási folyamat számítási lépéseit végrehajtó gépekre vagy fürtökre vonatkozik.   A számítási célok teljes listájáért, valamint a munkaterülethez való létrehozásához és csatolásához lásd: [számítási célok a modell](how-to-set-up-training-targets.md) betanításához.  A számítási cél létrehozásához és csatolásához szükséges folyamat ugyanaz, függetlenül attól, hogy a modell betanítása vagy a folyamat lépései futnak-e. A számítási cél létrehozása és csatolása után használja az `ComputeTarget` objektumot a [folyamat lépéseiben](#steps).

> [!IMPORTANT]
> A számítási célokat szolgáló felügyeleti műveletek végrehajtása távoli feladatokon belül nem támogatott. Mivel a gépi tanulási folyamatokat távoli feladatokként küldi el a rendszer, ne használja a számítási célok felügyeleti műveleteit a folyamaton belül.

Az alábbi példák a számítási célok létrehozásához és csatolásához szükségesek a következőkhöz:

* Az Azure Machine Learning Compute
* Azure Databricks 
* Azure Data Lake Analytics

### <a name="azure-machine-learning-compute"></a>Az Azure Machine Learning compute

A lépések futtatásához létrehozhat egy Azure Machine Learning számítást.

```python
from azureml.core.compute import ComputeTarget, AmlCompute

compute_name = "aml-compute"
vm_size = "STANDARD_NC6"
if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('Found compute target: ' + compute_name)
else:
    print('Creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size=vm_size,  # STANDARD_NC6 is GPU-enabled
                                                                min_nodes=0,
                                                                max_nodes=4)
    # create the compute target
    compute_target = ComputeTarget.create(
        ws, compute_name, provisioning_config)

    # Can poll for a minimum number of nodes and for a specific timeout.
    # If no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(
        show_output=True, min_node_count=None, timeout_in_minutes=20)

    # For a more detailed view of current cluster status, use the 'status' property
    print(compute_target.status.serialize())
```

### <a id="databricks"></a>Az Azure Databricks

Az Azure Databricks egy Apache Spark-alapú környezetben az Azure-felhőben. A számítási célként Azure Machine Learning folyamattal is használható.

Használat előtt hozzon létre egy Azure Databricks munkaterületet. Ezek az erőforrás létrehozásához tekintse meg a [Spark-feladatok futtatása Azure Databricksen](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) dokumentumot.

Azure Databricks számítási célként való csatolásához adja meg a következő információkat:

* __Databricks számítási neve__: Az ehhez a számítási erőforráshoz hozzárendelni kívánt név.
* __Databricks-munkaterület neve__: Az Azure Databricks munkaterület neve.
* __Databricks hozzáférési jogkivonat__: A Azure Databricks hitelesítéséhez használt hozzáférési jogkivonat. Hozzáférési jogkivonat létrehozásához, tekintse meg a [hitelesítési](https://docs.azuredatabricks.net/api/latest/authentication.html) dokumentumot.

A következő kód bemutatja, hogyan csatolhatja Azure Databricks számítási célként a Azure Machine Learning SDK-val:

```python
import os
from azureml.core.compute import ComputeTarget, DatabricksCompute
from azureml.exceptions import ComputeTargetException

databricks_compute_name = os.environ.get(
    "AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_workspace_name = os.environ.get(
    "AML_DATABRICKS_WORKSPACE", "<databricks_workspace_name>")
databricks_resource_group = os.environ.get(
    "AML_DATABRICKS_RESOURCE_GROUP", "<databricks_resource_group>")
databricks_access_token = os.environ.get(
    "AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(
        workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_workspace_name {}'.format(databricks_workspace_name))
    print('databricks_access_token {}'.format(databricks_access_token))

    # Create attach config
    attach_config = DatabricksCompute.attach_configuration(resource_group=databricks_resource_group,
                                                           workspace_name=databricks_workspace_name,
                                                           access_token=databricks_access_token)
    databricks_compute = ComputeTarget.attach(
        ws,
        databricks_compute_name,
        attach_config
    )

    databricks_compute.wait_for_completion(True)
```

Részletesebb példaként tekintse meg a GitHubon egy [példát](https://aka.ms/pl-databricks) a notebookra.

### <a id="adla"></a>Az Azure Data Lake Analytics

Az Azure Data Lake Analytics egy big data-elemzési platform az Azure-felhőben. A számítási célként Azure Machine Learning folyamattal is használható.

Használat előtt hozzon létre egy Azure Data Lake Analytics fiókot. Ez az erőforrás létrehozásához tekintse meg a [Ismerkedés az Azure Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal) dokumentumot.

Csatlakoztassa a Data Lake Analytics számítási célként, az Azure Machine Learning SDK, és adja meg a következő információkat:

* __Számítási név__: Az ehhez a számítási erőforráshoz hozzárendelni kívánt név.
* __Erőforráscsoport__: Az Data Lake Analytics fiókot tartalmazó erőforráscsoport.
* __Fiók neve__: A Data Lake Analytics fiók neve.

A következő kód bemutatja, hogyan csatlakoztathat a Data Lake Analytics számítási célként mutat be:

```python
import os
from azureml.core.compute import ComputeTarget, AdlaCompute
from azureml.exceptions import ComputeTargetException


adla_compute_name = os.environ.get(
    "AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_group = os.environ.get(
    "AML_ADLA_RESOURCE_GROUP", "<adla_resource_group>")
adla_account_name = os.environ.get(
    "AML_ADLA_ACCOUNT_NAME", "<adla_account_name>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_group))
    print('adla_account_name {}'.format(adla_account_name))
    # create attach config
    attach_config = AdlaCompute.attach_configuration(resource_group=adla_resource_group,
                                                     account_name=adla_account_name)
    # Attach ADLA
    adla_compute = ComputeTarget.attach(
        ws,
        adla_compute_name,
        attach_config
    )

    adla_compute.wait_for_completion(True)
```

Részletesebb példaként tekintse meg a GitHubon egy [példát](https://aka.ms/pl-adla) a notebookra.

> [!TIP]
> Az Azure Machine Learning-folyamatokat a Data Lake Analytics-fiók az alapértelmezett data store-ban tárolt adatokkal folytatott csak működik. Ha adatokat kell a munkahelyi egy nem alapértelmezett tároló, használhat egy [ `DataTransferStep` ](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) képzési előtt az adatok másolásához.

## <a id="steps"></a>A folyamat lépéseinek megépítése

Miután létrehozta és csatol egy számítási célt a munkaterülethez, készen áll a folyamat lépésének megadására. Nincsenek elérhető az Azure Machine Learning SDK segítségével számos beépített lépéseket. Ezeknek a lépéseknek a legalapvetőbb lépései egy [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py), amely egy Python-szkriptet futtat egy adott számítási célra:

```python
trainStep = PythonScriptStep(
    script_name="train.py",
    arguments=["--input", blob_input_data, "--output", processed_data1],
    inputs=[blob_input_data],
    outputs=[processed_data1],
    compute_target=compute_target,
    source_directory=project_folder
)
```

A korábbi eredmények (`allow_reuse`) használata akkor fontos, ha a folyamatokat együttműködési környezetben használják, mivel a szükségtelen ismételt futtatások nem állnak helyre. Ez az alapértelmezett viselkedés, ha a script_name, a bemenetek és a lépés paramétereinek változatlanok maradnak. A lépés kimenetének újrafelhasználásakor a rendszer nem küldi el a feladatot a számításhoz, hanem az előző Futtatás eredményei azonnal elérhetők lesznek a következő lépés futtatásához. Ha false értékre van állítva, akkor a folyamat végrehajtása során a rendszer mindig új futtatást hoz létre ehhez a lépéshez. 

A lépések meghatározása után a folyamatokat a fenti lépések némelyikével vagy mindegyikével kell felépíteni.

> [!NOTE]
> A lépések megadása vagy a folyamat összeállítása során a rendszer nem tölt fel fájlt vagy adatfájlt a Azure Machine Learning szolgáltatásba.

```python
# list of steps to run
compareModels = [trainStep, extractStep, compareStep]

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=[compareModels])
```

A következő példa a korábban létrehozott Azure Databricks számítási célt használja: 

```python
dbStep = DatabricksStep(
    name="databricksmodule",
    inputs=[step_1_input],
    outputs=[step_1_output],
    num_workers=1,
    notebook_path=notebook_path,
    notebook_params={'myparam': 'testparam'},
    run_name='demo run name',
    compute_target=databricks_compute,
    allow_reuse=False
)
# List of steps to run
steps = [dbStep]

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=steps)
```

További információkért lásd az [Azure-pipeline-Steps csomag](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) és a [folyamat osztályának](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py) referenciáját.

## <a name="submit-the-pipeline"></a>Küldje el a folyamat

A folyamat elküldésekor Azure Machine Learning szolgáltatás ellenőrzi az egyes lépésekhez tartozó függőségeket, és feltölti a megadott forrás könyvtár pillanatképét. Ha nincs forráskönyvtár van megadva, az aktuális helyi könyvtárban van feltöltve. A pillanatkép a munkaterületen a kísérlet részeként is tárolódik.

> [!IMPORTANT]
> Ha meg szeretné akadályozni, hogy a fájlok szerepeljenek a pillanatképben, `.amlignore` hozzon létre egy [. gitignore](https://git-scm.com/docs/gitignore) vagy fájlt a címtárban, és adja hozzá a fájlokat. A `.amlignore` fájl ugyanazt a szintaxist és mintázatot használja, mint a [. gitignore](https://git-scm.com/docs/gitignore) fájl. Ha mindkét fájl létezik, a `.amlignore` fájl elsőbbséget élvez.
>
> További információ: Pillanatképek [](concept-azure-machine-learning-architecture.md#snapshots).

```python
# Submit the pipeline to be run
pipeline_run1 = Experiment(ws, 'Compare_Models_Exp').submit(pipeline1)
pipeline_run1.wait_for_completion()
```

A folyamat első futtatásakor Azure Machine Learning:

* Letölti a projekt pillanatképét a számítási célra a munkaterülethez társított blob Storage-ból.
* Létrehoz egy Docker-rendszerképet a folyamat egyes lépéseinek megfelelően.
* Letölti az egyes lépésekhez tartozó Docker-rendszerképet a számítási célra a tároló-beállításjegyzékből.
* Csatlakoztatja az adattárat, `DataReference` ha egy adott objektum egy lépésben van megadva. Csatlakoztatási nem támogatott, ha, helyette a számítási célnak az adatokat másolja.
* Futtatja a lépést a lépés definíciójában megadott számítási célként. 
* Olyan összetevőket hoz létre, mint például a naplók, az stdout és az stderr, a metrikák és a lépés által megadott kimenet. Ezeket az összetevőket a rendszer feltölti és megőrzi a felhasználó alapértelmezett adattárában.

![Kísérlet folyamatként való futtatásának ábrája](./media/how-to-create-your-first-pipeline/run_an_experiment_as_a_pipeline.png)

További információ: [kísérlet osztály](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py) referenciája.



## <a name="github-tracking-and-integration"></a>GitHub-követés és-integráció

Ha olyan képzést indít el, ahol a forrás könyvtára helyi git-tárház, a rendszer a tárház adatait a futtatási előzményekben tárolja. Az adattár aktuális véglegesítő AZONOSÍTÓját például az előzmények részeként naplózza a rendszer.

## <a name="publish-a-pipeline"></a>Folyamatok közzététele

Egy folyamat később futtatni a különböző adatbevitelek teheti közzé. Egy már közzétett folyamat REST-végpontján a paraméterek elfogadásához el kell parametrizálja a folyamatot a közzététel előtt.

1. Egy folyamat paramétere létrehozásához használja a [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py) objektum alapértelmezett értékkel.

   ```python
   pipeline_param = PipelineParameter(
     name="pipeline_arg",
     default_value=10)
   ```

2. Adja hozzá ezt a `PipelineParameter` paramétert a folyamat lépései valamelyik a következő objektum:

   ```python
   compareStep = PythonScriptStep(
     script_name="compare.py",
     arguments=["--comp_data1", comp_data1, "--comp_data2", comp_data2, "--output_data", out_data3, "--param1", pipeline_param],
     inputs=[ comp_data1, comp_data2],
     outputs=[out_data3],
     target=compute_target,
     source_directory=project_folder)
   ```

3. Ez a folyamat, amely elfogadja a paraméter meghívásakor közzététele.

   ```python
   published_pipeline1 = pipeline_run1.publish_pipeline(
        name="My_Published_Pipeline",
        description="My Published Pipeline Description",
        version="1.0")
   ```

### <a name="run-a-published-pipeline"></a>Egy közzétett folyamat futtatása

Minden közzétett folyamat REST-végponttal rendelkezik. Ez a végpont meghívja a folyamat futtatását a külső rendszerekből, például a nem Python-ügyfelekből. Ez a végpont lehetővé teszi a "felügyelt ismételhetőség" használatát a Batch-pontozási és-átképzési forgatókönyvekben.

Az előző folyamat futtatásának megkezdéséhez szüksége lesz egy Azure Active Directory hitelesítési fejléc-tokenre, amelyet a [AzureCliAuthentication osztály](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py) ismertet, vagy [Azure Machine learning](https://aka.ms/pl-restep-auth) jegyzetfüzetben történő hitelesítéssel kapcsolatos további részletekért.

```python
response = requests.post(published_pipeline1.endpoint,
                         headers=aad_token,
                         json={"ExperimentName": "My_Pipeline",
                               "ParameterAssignments": {"pipeline_arg": 20}})
```

### <a name="view-results-of-a-published-pipeline"></a>Közzétett folyamat eredményeinek megtekintése

Tekintse meg az összes közzétett folyamat listáját és a futtatásuk részleteit:
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).  

1. [A munkaterület megjelenítése](how-to-manage-workspace.md#view) folyamatok listája található.
 ![gépi tanulási folyamatok listája](./media/how-to-create-your-first-pipeline/list_of_pipelines.png)
 
1. Válassza ki az adott folyamatot a futtatási eredmények megtekintéséhez.

### <a name="disable-a-published-pipeline"></a>Közzétett folyamat letiltása

Ha el szeretné rejteni a folyamatokat a közzétett folyamatok listájáról, tiltsa le:

```
# Get the pipeline by using its ID in the Azure portal
p = PublishedPipeline.get(ws, id="068f4885-7088-424b-8ce2-eeb9ba5381a6")
p.disable()
```

A `p.enable()`használatával újra engedélyezheti.


## <a name="caching--reuse"></a>Gyorsítótárazási & újrafelhasználása  

A folyamatok működésének optimalizálásához és testreszabásához hajtson végre néhány dolgot a gyorsítótárazás és az újbóli használat érdekében. Például a következőket teheti:
+ **Állítsa le a kimenet futtatásának alapértelmezett** újrafelhasználását a `allow_reuse=False` [lépés definíciója](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py)szerint. Az újrafelhasználás a kulcs, ha a folyamatokat együttműködésen alapuló környezetben használja, mivel a szükségtelen futtatások kiiktatása rugalmasságot biztosít. Ezt azonban letilthatja.
+ **Kiterjesztheti a kivonatot a szkripten túl**, hogy a forráskönyvtár abszolút elérési utat vagy relatív elérési utakat is tartalmazzon más fájlokhoz és címtárakhoz a következő használatával:`hash_paths=['<file or directory']` 
+ **A kimenet újragenerálásának kényszerítése a futtatási folyamat összes lépése esetében**`pipeline_run = exp.submit(pipeline, regenerate_outputs=False)`

Alapértelmezés `allow-reuse` szerint a lépések engedélyezve vannak, és a rendszer csak a fő parancsfájl kivonatát végzi el. Tehát ha egy adott lépés parancsfájlja ugyanaz (`script_name`, bemenetek és paraméterek) marad, az előző lépés futtatásának kimenete újra felhasználható, a rendszer nem küldi el a feladatot a számításba, és az előző Futtatás eredményei azonnal elérhetővé válnak a következő lépéshez. .  

```python
step = PythonScriptStep(name="Hello World",
                        script_name="hello_world.py",
                        compute_target=aml_compute,
                        source_directory=source_directory,
                        allow_reuse=False,
                        hash_paths=['hello_world.ipynb'])
```
 

## <a name="next-steps"></a>További lépések

- Használat [ezek Jupyter notebookok a Githubon](https://aka.ms/aml-pipeline-readme) való ismerkedés a machine learning-további folyamatokat.
- Olvassa el az SDK-referencia súgó a a [azureml-folyamatok-core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) csomag és a [azureml-folyamatok-lépéseket](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) csomagot.

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
