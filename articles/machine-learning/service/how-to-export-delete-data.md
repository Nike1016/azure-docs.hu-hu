---
title: Exportálja, vagy a munkaterület adatainak törlése
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan exportálhatja, vagy az Azure portal, a CLI, az SDK-t és a hitelesített REST API-k és-munkaterület törlése.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: ph-com
ms.author: pahusban
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: 3b55282adf56bedb0724eca578b8eaea00c26074
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65023977"
---
# <a name="export-or-delete-your-machine-learning-service-workspace-data"></a>Exportálja, vagy a Machine Learning szolgáltatás munkaterület adatainak törlése 

Az Azure Machine Learning exportál, vagy törölje a munkaterület adatainak hitelesített REST API-val. Ez a cikk bemutatja, hogyan.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="control-your-workspace-data"></a>Munkaterület adatfelügyelet
Terméken belüli, az Azure Machine Learning-szolgáltatások által tárolt adatok exportálási és törlése az Azure Portalon, CLI, SDK, keresztül érhető el, és a hitelesített REST API-k. Az adatvédelem az Azure Portalon keresztül elérhető telemetriai adatokat. 

Az Azure Machine Learning-szolgáltatások, a személyes adatok felhasználói adatokat a futtatási előzmények dokumentumok és a szolgáltatás egyes felhasználói interakció telemetriai rekordjait áll.

## <a name="delete-workspace-data-with-the-rest-api"></a>A REST API-val a munkaterület adatainak törlése 

Annak érdekében, hogy az adatok törléséhez a következő API-hívások a HTTP DELETE művelet is végezhető. Ezeket felhatalmazott kellene egy `Authorization: Bearer <arm-token>` a kérelemben szereplő tartományfejléc ahol `<arm-token>` van az AAD-hozzáférési tokent a `https://management.core.windows.net/` végpont.  

Ismerje meg, hogyan ez token beszerzése és az Azure-végpontok hívása, lásd: [Azure REST API-dokumentáció](https://docs.microsoft.com/rest/api/azure/).  

A következő példákban cserélje le a szöveget {} a példány nevekkel, amelyek meghatározzák a társított erőforrás.

### <a name="delete-an-entire-workspace"></a>Egy teljes munkaterület törlése

Ez a hívás használatával egy teljes munkaterület törlése.  
> [!WARNING]
> Összes adat törlődik, és a munkaterület többé nem lesz használható.

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2018-03-01-preview

### <a name="delete-models"></a>Modell törlése

Ez a hívás segítségével modelleket és a hozzájuk tartozó azonosítóik listáját:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2018-03-01-preview

Egyéni modellek törölhetők:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2018-03-01-preview

### <a name="delete-assets"></a>Eszközök törlése

Ez a hívás használja az eszközök és a hozzájuk tartozó azonosítóik listáját:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2018-03-01-preview

Egyes eszközök törölhetők:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2018-03-01-preview

### <a name="delete-images"></a>A képek törlése

Ez a hívás segítségével képeket és a hozzájuk tartozó azonosítóik listáját:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2018-03-01-preview

Az egyéni lemezképek törlése:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2018-03-01-preview

### <a name="delete-services"></a>Szolgáltatások törlése

Ez a hívás használatával services és a hozzájuk tartozó azonosítóik listáját:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2018-03-01-preview

Az egyes szolgáltatások is törölve:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2018-03-01-preview

## <a name="export-service-data-with-the-rest-api"></a>A REST API-val szolgáltatás az adatok exportálása

Annak érdekében, hogy az adatok exportálása a következő API-hívások a HTTP GET művelet végrehajtásával lehessen elvégezni. Ezek jogosult létesíteni egy `Authorization: Bearer <arm-token>` a kérelemben szereplő tartományfejléc ahol `<arm-token>` van az AAD-jogkivonat a végpont `https://management.core.windows.net/`  

Ismerje meg, hogyan ez token beszerzése és az Azure-végpontok hívása, lásd: [Azure REST API-dokumentáció](https://docs.microsoft.com/rest/api/azure/).   

A következő példákban cserélje le a szöveget {} a példány nevekkel, amelyek meghatározzák a társított erőforrás.

### <a name="export-workspace-information"></a>Munkaterületek adatainak exportálása

Ez a hívás használatával minden munkaterületek listájának lekérése:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces?api-version=2018-03-01-preview

Által egy adott munkaterület információ szerezhető be:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2018-03-01-preview

### <a name="export-compute-information"></a>Számítási adatainak exportálása

Az összes számítási munkaterülethez csatlakoztatott tárolók lekérheti:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes?api-version=2018-03-01-preview

Egyetlen számítási célt információ kérhetők le:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes/{computeName}?api-version=2018-03-01-preview

### <a name="export-run-history-data"></a>Exportálás futtatási előzményadatokat
Ez a hívás használatával minden kísérletek és az adataik listájának lekérése:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments 

Egy adott kísérletet az összes futó szerezhető be:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/runs 

Futtatási előzmények, elemek lekérheti:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/runs/{runId} 

A kísérlet összes futtatási metrikák szerezhető be:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/metrics 

Egyszeri futtatás metrika szerezhető be:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/metrics/{metricId}
    
### <a name="export-artifacts"></a>Összetevők exportálása

Ez a hívás használatával összetevők és az elérési utak listájának lekérése:

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/artifacts/origins/ExperimentRun/containers/{runId}
    
### <a name="export-notifications"></a>Értesítések exportálása

Ez a hívás használatával tárolt feladatok listájának lekérése:     

    https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/tasks

Egyetlen feladat értesítéseire való regisztrációt szerezhető be:

    https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}tasks/{taskId}

### <a name="export-data-stores"></a>Adattárak exportálása

Ez a hívás használatával adattárak listáját:

    https://{location}.experiments.azureml.net/datastore/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/datastores

Egyes adattárak szerezhető be:

    https://{location}.experiments.azureml.net/datastore/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/datastores/{name}

### <a name="export-models"></a>Modell exportálása

Ez a hívás segítségével modelleket és a hozzájuk tartozó azonosítóik listáját:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2018-03-01-preview

Egyéni modellek szerezhető be:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2018-03-01-preview

### <a name="export-assets"></a>Eszközök exportálása

Ez a hívás használja az eszközök és a hozzájuk tartozó azonosítóik listáját:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2018-03-01-preview

Egyes eszközök által szerezhető be:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2018-03-01-preview

### <a name="export-images"></a>Képek exportálása

Ez a hívás segítségével képeket és a hozzájuk tartozó azonosítóik listáját:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2018-03-01-preview

Egyéni rendszerképek szerezhető be:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2018-03-01-preview

### <a name="export-services"></a>Exportálás szolgáltatása

Ez a hívás használatával services és a hozzájuk tartozó azonosítóik listáját:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2018-03-01-preview

Egyes szolgáltatások szerezhető be:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2018-03-01-preview

### <a name="export-pipeline-experiments"></a>Folyamat kísérletek exportálása

Az egyes kísérletek szerezhető be:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Experiments/{experimentId}

### <a name="export-pipeline-graphs"></a>Folyamat Gráfok exportálása

Az egyes diagramok szerezhető be:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Graphs/{graphId}

### <a name="export-pipeline-modules"></a>Folyamat modulok exportálása

Modulok által szerezhető be:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Modules/{id}

### <a name="export-pipeline-templates"></a>Folyamat-sablonok exportálása

Sablonok szerezhető be:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Templates/{templateId}

### <a name="export-pipeline-data-sources"></a>Folyamat adatforrások exportálása

Adatforrások szerezhető be:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/DataSources/{id}

## <a name="delete-visual-interface-assets"></a>Vizuális felhasználói felületet eszközök törlése

A vizuális felületén, ahol létrehozta a kísérlethez törölje az egyedi eszközök:

1. A bal oldalon válassza ki a törölni kívánt objektum típusát.

    ![Eszközök törlése](media/how-to-export-delete-data.md/delete-experiment.png)

1. A listában válassza ki az egyes eszközök törléséhez.

1. Az alsó részén válassza **törlése**.

## <a name="export-visual-interface-data"></a>Vizuális felhasználói felületet adatok exportálása

A vizuális felületén, ahol létrehozta a kísérlethez felvett adatok exportálása:

1. A bal oldalon válassza ki a **adatok**.

1. A képernyő felső részén válassza **saját adatkészletek** vagy **minták** keresse meg az exportálni kívánt adatokat.

    ![Adatok letöltése](media/how-to-export-delete-data.md/download-data.png)

1. A listában válassza ki az egyes adatkészletek exportálása.

1. Az alsó részén válassza **letöltése**.