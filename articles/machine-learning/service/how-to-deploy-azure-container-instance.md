---
title: Modellek üzembe helyezése Azure Container Instances
titleSuffix: Azure Machine Learning service
description: Megtudhatja, hogyan helyezheti üzembe a Azure Machine Learning szolgáltatási modelljeit webszolgáltatásként Azure Container Instances használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 07/08/2019
ms.openlocfilehash: 29bff3383985905ac3146dfc7fc097fbd49a5a6d
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69543558"
---
# <a name="deploy-a-model-to-azure-container-instances"></a>Modell üzembe helyezése Azure Container Instances

Megtudhatja, hogyan helyezheti üzembe a modelleket Azure Container Instances (ACI) webszolgáltatásként a Azure Machine Learning szolgáltatás használatával. Azure Container Instances használata, ha a következő feltételek egyike igaz:

- Gyors üzembe helyezése és a modell érvényesítése kell. Előre nem kell létrehoznia ACI-tárolókat. Ezek a telepítési folyamat részeként jönnek létre.
- A tesztelt egy olyan modell, fejlesztés alatt áll. 

Az ACI-ra vonatkozó kvóta-és területi elérhetőséggel kapcsolatos információkért lásd: a [kvóták és a régió rendelkezésre állása Azure Container instances](https://docs.microsoft.com/azure/container-instances/container-instances-quotas) cikkben.

## <a name="prerequisites"></a>Előfeltételek

- Az Azure Machine Learning szolgáltatás munkaterületén. További információ: [Azure Machine learning szolgáltatás munkaterületének létrehozása](how-to-manage-workspace.md).

- A munkaterületen regisztrált gépi tanulási modell. Ha nem rendelkezik regisztrált modellel, tekintse meg a [modellek üzembe helyezésének módját és helyét](how-to-deploy-and-where.md).

- Az [Azure CLI-bővítmény Machine learning szolgáltatáshoz](reference-azure-machine-learning-cli.md), [Azure Machine learning Python SDK](https://aka.ms/aml-sdk)-hoz vagy a [Azure Machine learning Visual Studio Code bővítményhez](how-to-vscode-tools.md).

- A cikkben szereplő __Python__ -kódrészletek azt feltételezik, hogy a következő változók vannak beállítva:

    * `ws`– Állítsa be a munkaterületre.
    * `model`– Állítsa be a regisztrált modellt.
    * `inference_config`– Állítsa be a modellre vonatkozó következtetési konfigurációt.

    A változók beállításával kapcsolatos további információkért lásd: [how és How to Deploy models (modellek üzembe helyezése](how-to-deploy-and-where.md)).

- A cikkben szereplő __CLI__ -kódrészletek azt feltételezik, hogy létrehozott `inferenceconfig.json` egy dokumentumot. A dokumentum létrehozásával kapcsolatos további információkért lásd: [how és How to Deploy models (modellek üzembe helyezése](how-to-deploy-and-where.md)).

## <a name="deploy-to-aci"></a>Üzembe helyezés az ACI-ban

Azure Container Instances modell üzembe helyezéséhez hozzon létre egy __központi telepítési konfigurációt__ , amely leírja a szükséges számítási erőforrásokat. Például a magok és a memória száma. Szüksége lesz egy __következtetésre__is, amely leírja a modell és a webszolgáltatás üzemeltetéséhez szükséges környezetet. A következtetések konfigurációjának létrehozásáról további információt a [modellek üzembe helyezésének módja és helye](how-to-deploy-and-where.md)című témakörben talál.

### <a name="using-the-sdk"></a>Az SDK használata

```python
from azureml.core.webservice import AciWebservice, Webservice
from azureml.core.model import Model

deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "aciservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Az ebben a példában használt osztályokkal, metódusokkal és paraméterekkel kapcsolatos további információkért tekintse meg a következő dokumentációt:

* [AciWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-)
* [Modell. Deploy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config--deployment-config-none--deployment-target-none-)
* [Webszolgáltatás. wait_for_deployment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#wait-for-deployment-show-output-false-)

### <a name="using-the-cli"></a>A parancssori felület használata

A CLI használatával történő üzembe helyezéshez használja a következő parancsot. Cserélje `mymodel:1` le a nevet a regisztrált modell nevére és verziójára. Cserélje `myservice` le a nevet a következő szolgáltatáshoz:

```azurecli-interactive
az ml model deploy -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../../includes/machine-learning-service-aci-deploy-config.md)]

További információ: az [ml Model Deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) Reference. 

## <a name="using-vs-code"></a>A VS Code használata

Lásd: [modellek üzembe helyezése a vs Code](how-to-vscode-tools.md#deploy-and-manage-models)-ban.

> [!IMPORTANT]
> A teszteléshez nem kell ACI-tárolót létrehoznia. Az ACI-tárolók szükség szerint jönnek létre.

## <a name="update-the-web-service"></a>A web service frissítése

[!INCLUDE [aml-update-web-service](../../../includes/machine-learning-update-web-service.md)]

## <a name="next-steps"></a>További lépések

* [Modell üzembe helyezése egyéni Docker-rendszerkép használatával](how-to-deploy-custom-docker-image.md)
* [Üzembe helyezés hibaelhárítása](how-to-troubleshoot-deployment.md)
* [Biztonságos SSL-lel az Azure Machine Learning-webszolgáltatások](how-to-secure-web-service.md)
* [Webszolgáltatásként üzembe helyezett gépi Tanulási modell felhasználása](how-to-consume-web-service.md)
* [A Azure Machine Learning modellek monitorozása a Application Insights](how-to-enable-app-insights.md)
* [Adatok gyűjtése a termelési modellekhez](how-to-enable-data-collection.md)
