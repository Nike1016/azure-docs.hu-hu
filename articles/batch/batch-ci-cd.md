---
title: HPC-megoldások létrehozása és üzembe helyezése az Azure-folyamatokkal – Azure Batch | Microsoft Docs
description: Megtudhatja, hogyan helyezhet üzembe Build/kiadási folyamatot egy Azure Batchon futó HPC-alkalmazáshoz.
author: christianreddington
ms.author: chredd
ms.date: 03/28/2019
ms.topic: conceptual
ms.custom: fasttrack-new
services: batch
ms.service: batch
ms.openlocfilehash: 47665171ee5ae137e0503b3e5fa1d369aeabb356
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840054"
---
# <a name="use-azure-pipelines-to-build-and-deploy-hpc-solutions"></a>HPC-megoldások létrehozása és üzembe helyezése az Azure-folyamatokkal

Az Azure DevOps Services olyan eszközöket biztosít, amelyeket a fejlesztői csapatok használnak egyéni alkalmazások létrehozásakor. Az Azure DevOps által biztosított eszközök a nagy teljesítményű számítási megoldások automatizált kiépítése és tesztelése révén fordíthatók le. Ez a cikk bemutatja, hogyan állítható be a folyamatos integráció (CI) és a folyamatos üzembe helyezés (CD) az Azure-folyamatokkal a Azure Batch üzembe helyezett nagy teljesítményű számítási megoldásokhoz.

Az Azure-folyamatok számos modern CI/CD-folyamatot biztosítanak a szoftverek kiépítéséhez, üzembe helyezéséhez, teszteléséhez és figyeléséhez. Ezek a folyamatok felgyorsítják a szoftverek kézbesítését, így a támogatási infrastruktúra és műveletek helyett a kódra koncentrálhat.

## <a name="create-an-azure-pipeline"></a>Azure-folyamat létrehozása

Ebben a példában létrehozunk egy létrehozási és kiadási folyamatot egy Azure Batch infrastruktúra üzembe helyezéséhez és alkalmazáscsomag kiadásához. Feltételezve, hogy a kód helyileg lett kifejlesztve, ez az általános telepítési folyamat:

![A folyamaton belüli üzembe helyezés folyamatát bemutató ábra](media/batch-ci-cd/DeploymentFlow.png)

### <a name="setup"></a>Beállítás

A cikkben ismertetett lépések végrehajtásához egy Azure DevOps-szervezetre és egy Team-projektre van szükség.

* [Azure DevOps-szervezet létrehozása](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops)
* [Projekt létrehozása az Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

### <a name="source-control-for-your-environment"></a>A környezet verziókövetés

A verziókövetés lehetővé teszi, hogy a csapatok nyomon kövessék a kód módosításait, és megvizsgálják a kód korábbi verzióit.

A verziókövetés általában a szoftveres kóddal együtt történik. Mi a helyzet a mögöttes infrastruktúrával? Ez kódként szolgál az infrastruktúra számára, ahol Azure Resource Manager sablonokat vagy más nyílt forráskódú alternatívákat fogunk használni a mögöttes infrastruktúra deklaratív definiálásához.

Ez a minta számos Resource Manager-sablonra (JSON-dokumentumra) és meglévő bináris fájlra támaszkodik. Ezeket a példákat átmásolhatja a tárházba, és leküldheti őket az Azure DevOps.

Az ebben a példában használt kód szerkezet a következőhöz hasonló:

* Egy **ARM-templates** mappa, amely több Azure Resource Manager sablont tartalmaz. A sablonokat a cikk ismerteti.
* Egy **ügyfél-alkalmazás** mappa, amely a [Azure Batch .net file Processing és az FFmpeg](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial) minta másolata. Ez nem szükséges ehhez a cikkhez.
* **HPC-Application** mappa, amely az [ffmpeg 3,4](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip)Windows 64 bites verziója.
* A **folyamatok** mappája. Ez egy YAML-fájlt tartalmaz, amely felvázolja a létrehozás folyamatát. Ezt a cikk tárgyalja.

Ez a szakasz feltételezi, hogy már ismeri a verziókövetés és a Resource Manager-sablonok tervezését. Ha nem ismeri ezeket a fogalmakat, további információért tekintse meg a következő lapokat.

* [Mi az a verziókövetés?](https://docs.microsoft.com/azure/devops/user-guide/source-control?view=azure-devops)
* [Az Azure Resource Manager-sablonok struktúrája és szintaxisa](../azure-resource-manager/resource-group-authoring-templates.md)

#### <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sablonok

Ez a példa több Resource Manager-sablont használ a megoldás üzembe helyezéséhez. Ehhez számos képességgel rendelkező sablont használunk (hasonló egységekhez vagy modulokhoz), amelyek egy adott funkciót implementálnak. Egy teljes körű megoldási sablont is használunk, amely felelős az alapul szolgáló képességek összekapcsolásához. Ennek a megközelítésnek több előnye is van:

* Az alapul szolgáló képességi sablonok külön egységesen vizsgálhatók.
* A mögöttes képességgel rendelkező sablonok a szervezeten belül szabványosként definiálhatók, és több megoldásban is használhatók.

Ebben a példában egy teljes körű megoldási sablon (Deployment. JSON) található, amely három sablont telepít. Az alapul szolgáló sablonok a megoldás egy adott aspektusának üzembe helyezéséhez szükséges képesség-sablonok.

![Példa csatolt sablon szerkezetére Azure Resource Manager sablonok használatával](media/batch-ci-cd/ARMTemplateHierarchy.png)

Az első megjelenő sablon egy Azure Storage-fiók. A megoldáshoz egy Storage-fiókra van szükség, amely a Batch-fiókba helyezi üzembe az alkalmazást. Érdemes lehet a Resource Manager-sablonokra vonatkozó [útmutató a Microsoft. Storage erőforrástípusok](https://docs.microsoft.com/azure/templates/microsoft.storage/allversions) számára a Storage-fiókok Resource Manager-sablonjainak létrehozásakor.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Storage Account"
             }
         }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('accountName')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "apiVersion": "2018-02-01",
            "location": "[resourceGroup().location]",
            "properties": {}
        }
    ],
    "outputs": {
        "blobEndpoint": {
          "type": "string",
          "value": "[reference(resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))).primaryEndpoints.blob]"
        },
        "resourceId": {
          "type": "string",
          "value": "[resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))]"
        }
    }
}
```

Ezt követően megtekintjük a Azure Batch fiók sablonját. A Azure Batch-fiók platformként szolgál számos alkalmazás különböző készletekben történő futtatásához (gépek csoportosítása). Érdemes lehet a Resource Manager-sablonokra [vonatkozó útmutató a Microsoft. batch erőforrástípusok](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions) számára a Batch-fiókok Resource Manager-sablonjainak létrehozásakor.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "batchAccountName": {
           "type": "string",
           "metadata": {
                "description": "Name of the Azure Batch Account"
            }
        },
        "storageAccountId": {
           "type": "string",
           "metadata": {
                "description": "ID of the Azure Storage Account"
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "name": "[parameters('batchAccountName')]",
            "type": "Microsoft.Batch/batchAccounts",
            "apiVersion": "2017-09-01",
            "location": "[resourceGroup().location]",
            "properties": {
              "poolAllocationMode": "BatchService",
              "autoStorage": {
                  "storageAccountId": "[parameters('storageAccountId')]"
              }
            }
          }
    ],
    "outputs": {}
}
```

A következő sablon egy példát mutat be Azure Batch készlet létrehozásához (az alkalmazások feldolgozásához a háttérrendszer-gépeken). Érdemes lehet a Resource Manager-sablonokra [vonatkozó útmutató a Microsoft. batch erőforrástípusok](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions) számára a Batch-fiókok Resource Manager-sablonjainak létrehozásakor.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "batchAccountName": {
           "type": "string",
           "metadata": {
                "description": "Name of the Azure Batch Account"
           }
        },
        "batchAccountPoolName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Batch Account Pool"
             }
         }
    },
    "variables": {},
    "resources": [
        {
            "name": "[concat(parameters('batchAccountName'),'/', parameters('batchAccountPoolName'))]",
            "type": "Microsoft.Batch/batchAccounts/pools",
            "apiVersion": "2017-09-01",
            "properties": {
                "deploymentConfiguration": {
                    "virtualMachineConfiguration": {
                        "imageReference": {
                            "publisher": "Canonical",
                            "offer": "UbuntuServer",
                            "sku": "18.04-LTS",
                            "version": "latest"
                        },
                        "nodeAgentSkuId": "batch.node.ubuntu 18.04"
                    }
                },
                "vmSize": "Standard_D1_v2"
            }
          }
    ],
    "outputs": {}
}
```

Végezetül egy olyan sablonnal rendelkezünk, amely egy Orchestrator hasonló módon működik. Ez a sablon felelős a képességek sablonjainak telepítéséhez.

További információt a [csatolt Azure Resource Manager-sablonok létrehozásáról](../azure-resource-manager/resource-manager-tutorial-create-linked-templates.md) a különálló cikkben talál.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "templateContainerUri": {
           "type": "string",
           "metadata": {
                "description": "URI of the Blob Storage Container containing the Azure Resouce Manager templates"
            }
        },
        "templateContainerSasToken": {
           "type": "string",
           "metadata": {
                "description": "The SAS token of the container containing the Azure Resouce Manager templates"
            }
        },
        "applicationStorageAccountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Storage Account"
            }
         },
        "batchAccountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Batch Account"
            }
         },
         "batchAccountPoolName": {
             "type": "string",
             "metadata": {
                  "description": "Name of the Azure Batch Account Pool"
              }
          }
    },
    "variables": {},
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "storageAccountDeployment",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/storageAccount.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "accountName": {"value": "[parameters('applicationStorageAccountName')]"}
                }
            }
        },  
        {
            "apiVersion": "2017-05-10",
            "name": "batchAccountDeployment",
            "type": "Microsoft.Resources/deployments",
            "dependsOn": [
                "storageAccountDeployment"
            ],
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/batchAccount.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "batchAccountName": {"value": "[parameters('batchAccountName')]"},
                    "storageAccountId": {"value": "[reference('storageAccountDeployment').outputs.resourceId.value]"}
                }
            }
        },
        {
            "apiVersion": "2017-05-10",
            "name": "poolDeployment",
            "type": "Microsoft.Resources/deployments",
            "dependsOn": [
                "batchAccountDeployment"
            ],
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/batchAccountPool.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "batchAccountName": {"value": "[parameters('batchAccountName')]"},
                    "batchAccountPoolName": {"value": "[parameters('batchAccountPoolName')]"}
                }
            }
        }
    ],
    "outputs": {}
}
```

#### <a name="the-hpc-solution"></a>A HPC-megoldás

Az infrastruktúra és a szoftver definiálható kódként, és ugyanabban a tárházban helyezhető el.

Ebben a megoldásban az FFmpeg-t használja alkalmazáscsomagként. Az FFmpeg-csomag [innen](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip)tölthető le.

![Példa git-tárház struktúrájára](media/batch-ci-cd/git-repository.jpg)

A tárház négy fő szakaszt tartalmaz:

* Az infrastruktúrát kódként tároló **ARM-templates** mappa
* A **HPC-Application** mappa, amely az FFmpeg bináris fájljait tartalmazza
* A **folyamatokat** tartalmazó mappa, amely tartalmazza a létrehozási folyamat definícióját.
* Nem **kötelező**: Az **ügyfél-alkalmazás** mappa, amely a .NET-alkalmazás kódját tárolja. Ezt a mintát nem használjuk, de a saját projektjeiben előfordulhat, hogy a HPC batch-alkalmazás futtatását egy ügyfélalkalmazás használatával szeretné végrehajtani.

> [!NOTE]
> Ez csak egy példa a rendszerértékre történő rendszerszerkezetre. Ezt a módszert arra használjuk, hogy az alkalmazás, az infrastruktúra és a folyamat kódja ugyanabban a tárházban legyen tárolva.

Most, hogy beállította a forráskódot, megkezdheti az első felépítést.

## <a name="continuous-integration"></a>Folyamatos integráció

Az Azure DevOps Services szolgáltatáson belüli [Azure-folyamatok](https://docs.microsoft.com/azure/devops/pipelines/get-started/?view=azure-devops)segítenek a létrehozási, tesztelési és üzembe helyezési folyamat megvalósításában az alkalmazásaihoz.

A folyamat ezen szakaszában a tesztek általában a kód érvényesítésére és a szoftver megfelelő részeinek összeállítására futnak. A tesztek száma és típusai, valamint az Ön által futtatott további feladatok a szélesebb körű Build-és kiadási stratégiától függenek.

## <a name="preparing-the-hpc-application"></a>A HPC-alkalmazás előkészítése

Ebben a példában a **HPC-Application** mappára fogunk összpontosítani. A **HPC-Application** mappa az az FFmpeg szoftver, amely a Azure batch fiókon belül fog futni.

1. Navigáljon az Azure DevOps-szervezetének Azure-folyamatok buildek szakaszába. Hozzon létre egy **új**folyamatot.

    ![Új build-folyamat létrehozása](media/batch-ci-cd/new-build-pipeline.jpg)

1. A build folyamat létrehozásához két lehetőség közül választhat:

    a. [A vizuális tervező használatával](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=azure-devops&tabs=new-nav). Ha ezt szeretné használni, kattintson a "Visual Designer használata" lehetőségre az **új folyamat** lapon.

    b. [YAML-buildek használata](https://docs.microsoft.com/azure/devops/pipelines/get-started-yaml?view=azure-devops). Az új folyamat lapon az Azure Repos vagy a GitHub lehetőségre kattintva hozhat létre új YAML-folyamatot. Azt is megteheti, hogy az alábbi példát a forrás vezérlőelemben tárolja, és egy meglévő YAML-fájlra hivatkozik, ha a Visual Designerre kattint, majd a YAML sablont használja.

    ```yml
    # To publish an application into Azure Batch, we need to
    # first zip the file, and then publish an artifact, so that
    # we can take the necessary steps in our release pipeline.
    steps:
    # First, we Zip up the files required in the Batch Account
    # For this instance, those are the ffmpeg files
    - task: ArchiveFiles@2
      displayName: 'Archive applications'
      inputs:
        rootFolderOrFile: hpc-application
        includeRootFolder: false
        archiveFile: '$(Build.ArtifactStagingDirectory)/package/$(Build.BuildId).zip'
    # Publish that zip file, so that we can use it as part
    # of our Release Pipeline later
    - task: PublishPipelineArtifact@0
      inputs:
        artifactName: 'hpc-application'
        targetPath: '$(Build.ArtifactStagingDirectory)/package'
    ```

1. Ha a buildet igény szerint konfigurálta, válassza a **mentés & üzenetsor**lehetőséget. Ha engedélyezve van a folyamatos integráció (az **Eseményindítók** szakaszban), a Build automatikusan elindul, amikor új véglegesíti a tárházat, és megfelel a buildben megadott feltételeknek.

    ![Meglévő build-folyamat – példa](media/batch-ci-cd/existing-build-pipeline.jpg)

1. Tekintse meg az élő frissítéseket a Build Azure DevOps való fejlesztésének előrehaladásával az Azure-folyamatok **Build** szakaszának megnyitásával. Válassza ki a megfelelő buildet a Build definíciójában.

    ![Élő kimenetek megtekintése a buildből](media/batch-ci-cd/Build-1.jpg)

> [!NOTE]
> Ha egy ügyfélalkalmazás használatával hajtja végre a HPC batch-alkalmazást, létre kell hoznia egy külön Build-definíciót az alkalmazáshoz. Az [Azure-folyamatok](https://docs.microsoft.com/azure/devops/pipelines/get-started/index?view=azure-devops) dokumentációjában számos útmutatók találhatók.

## <a name="continuous-deployment"></a>Folyamatos telepítés

Az Azure-folyamatok az alkalmazás és a mögöttes infrastruktúra üzembe helyezéséhez is használhatók. [](https://docs.microsoft.com/azure/devops/pipelines/release) A kiadási folyamatok az az összetevő, amely lehetővé teszi a folyamatos üzembe helyezést, és automatizálja a kiadási folyamatokat.

### <a name="deploying-your-application-and-underlying-infrastructure"></a>Az alkalmazás és a mögöttes infrastruktúra üzembe helyezése

Az infrastruktúra üzembe helyezésének számos lépése van. A [csatolt sablonok](../azure-resource-manager/resource-group-linked-templates.md)használata során ezeknek a sablonoknak elérhetőnek kell lenniük egy nyilvános végpontról (http vagy https). Ez lehet egy adattár a GitHubon, vagy egy Azure Blob Storage-fiók vagy egy másik tárolási hely. A feltöltött sablon összetevői biztonságban maradhatnak, mivel azok privát módban is tárolhatók, de a közös hozzáférésű aláírás (SAS) jogkivonatának valamilyen formájával érhetők el. Az alábbi példa bemutatja, hogyan helyezhet üzembe egy infrastruktúrát sablonokkal egy Azure Storage-blob használatával.

1. Hozzon létre egy **új kiadási definíciót**, és válasszon ki egy üres definíciót. Ezután át kell neveznie az újonnan létrehozott környezetet a folyamathoz kapcsolódóan.

    ![Kezdeti kiadási folyamat](media/batch-ci-cd/Release-0.jpg)

1. A HPC-alkalmazás kimenetének lekéréséhez hozzon létre egy függőséget a Build folyamaton.

    > [!NOTE]
    > Ismét jegyezze fel a **forrás aliast**, mivel erre akkor van szükség, amikor a feladatok a kiadás definícióján belül jönnek létre.

    ![Összetevő-hivatkozás létrehozása a HPCApplicationPackage a megfelelő Build-folyamatban](media/batch-ci-cd/Release-1.jpg)

1. Hozzon létre egy hivatkozást egy másik összetevőre, ezúttal egy Azure-tárházra. Ez a tárházban tárolt Resource Manager-sablonok eléréséhez szükséges. Mivel a Resource Manager-sablonok nem igénylik a fordítást, nem szükséges leküldeni őket egy Build folyamaton keresztül.

    > [!NOTE]
    > Ismét jegyezze fel a **forrás aliast**, mivel erre akkor van szükség, amikor a feladatok a kiadás definícióján belül jönnek létre.

    ![Összetevő-hivatkozás létrehozása az Azure Reposhez](media/batch-ci-cd/Release-2.jpg)

1. Navigáljon a **változók** szakaszhoz. Azt javasoljuk, hogy számos változót hozzon létre a folyamat során, így nem kell ugyanazt az információt több feladatba felvennie. Ezek az ebben a példában használt változók, és hogyan befolyásolják az üzemelő példányt.

    * **applicationStorageAccountName**: A HPC-alkalmazás bináris fájljainak tárolására szolgáló Storage-fiók neve
    * **batchAccountApplicationName**: Az alkalmazás neve a Azure Batch fiókban
    * **batchAccountName**: A Azure Batch fiók neve
    * **batchAccountPoolName**: A feldolgozást végző virtuális gépek készletének neve
    * **batchApplicationId**: A Azure Batch alkalmazás egyedi azonosítója
    * **batchApplicationVersion**: A Batch-alkalmazás szemantikai verziója (azaz az FFmpeg bináris fájljai)
    * **hely**: A telepítendő Azure-erőforrások helye
    * **resourceGroupName**: A létrehozandó erőforráscsoport neve, és hogy hol lesznek telepítve az erőforrások
    * **storageAccountName**: A társított Resource Manager-sablonok tárolására szolgáló Storage-fiók neve

    ![Az Azure-folyamatok kiadására beállított változók – példa](media/batch-ci-cd/Release-4.jpg)

1. Navigáljon a fejlesztői környezet feladataihoz. Az alábbi pillanatképben hat feladatot láthat. Ezek a feladatok: letölti a tömörített FFmpeg-fájlokat, telepíti a Storage-fiókot a beágyazott Resource Manager-sablonok tárolására, átmásolja a Resource Manager-sablonokat a Storage-fiókba, üzembe helyezi a Batch-fiókot és a szükséges függőségeket, létrehoz egy alkalmazást a alkalmazásban. a Azure Batch fiókot, és töltse fel az alkalmazáscsomag a Azure Batch-fiókba.

    ![A HPC-alkalmazás Azure Batch való kiadásához használt feladatok – példa](media/batch-ci-cd/Release-3.jpg)

1. Adja hozzá a **letöltési folyamat (előzetes verzió)** feladatot, és állítsa be a következő tulajdonságokat:
    * **Megjelenítendő név:** ApplicationPackage letöltése az ügynöknek
    * A **letölteni kívánt összetevő neve:** HPC-Application
    * **Letöltés elérési útja**: $ (System. DefaultWorkingDirectory)

1. Hozzon létre egy Storage-fiókot az összetevők tárolásához. A megoldásban meglévő Storage-fiókot lehet használni, de az önálló minta és a tartalom elkülönítéséhez dedikált Storage-fiókot adunk az összetevőkhöz (különösen a Resource Manager-sablonokhoz).

    Adja hozzá az **Azure-erőforráscsoport telepítési** feladatát, és állítsa be a következő tulajdonságokat:
    * **Megjelenítendő név:** A Storage-fiók üzembe helyezése Resource Manager-sablonokhoz
    * **Azure-előfizetés:** Válassza ki a megfelelő Azure-előfizetést
    * **Művelet**: Erőforráscsoport létrehozása vagy frissítése
    * **Erőforráscsoport**: $ (resourceGroupName)
    * **Hely**: $ (hely)
    * **Template**: $(System.ArtifactsDirectory)/ **{YourAzureRepoArtifactSourceAlias}** /arm-templates/storageAccount.json
    * **Felülbírálja a sablon paramétereit**:-accountName $ (storageAccountName)

1. Töltse fel az összetevőket a forrás vezérlőelemből a Storage-fiókba. Ez az Azure-folyamat feladata. Ennek a feladatnak a részeként a Storage-fiók tárolójának URL-címét és az SAS-tokent egy Azure-folyamatokban lévő változóban lehet kiépíteni. Ez azt jelenti, hogy az ügynök fázisában újra felhasználható.

    Adja hozzá az **Azure file Copy** feladatot, és állítsa be a következő tulajdonságokat:
    * **Source:** $(System.ArtifactsDirectory)/ **{YourAzureRepoArtifactSourceAlias}** /arm-templates/
    * **Azure-kapcsolattípus**: Azure Resource Manager
    * **Azure-előfizetés:** Válassza ki a megfelelő Azure-előfizetést
    * **Cél típusa**: Azure-blob
    * **RM Storage-fiók**: $ (storageAccountName)
    * **Tároló neve**: sablonok
    * **Storage Container URI**: templateContainerUri
    * **Storage-tároló sas**-tokenje: templateContainerSasToken

1. Telepítse a Orchestrator sablont. A korábbi Orchestrator sablon visszahívása, láthatja, hogy a Storage-fiók tárolójának URL-címe szerepel a SAS-tokenen kívül is. Figyelje meg, hogy a Resource Manager-sablonban szükséges változók megtalálhatók a kiadás definíciójának változók szakaszában, vagy egy másik Azure-folyamattól (például az Azure Blob Copy feladatának részeként) lettek beállítva.

    Adja hozzá az **Azure-erőforráscsoport telepítési** feladatát, és állítsa be a következő tulajdonságokat:
    * **Megjelenítendő név:** Azure Batch üzembe helyezése
    * **Azure-előfizetés:** Válassza ki a megfelelő Azure-előfizetést
    * **Művelet**: Erőforráscsoport létrehozása vagy frissítése
    * **Erőforráscsoport**: $ (resourceGroupName)
    * **Hely**: $ (hely)
    * **Template**: $(System.ArtifactsDirectory)/ **{YourAzureRepoArtifactSourceAlias}** /arm-templates/deployment.json
    * A **sablon paramétereinek felülbírálása**:```-templateContainerUri $(templateContainerUri) -templateContainerSasToken $(templateContainerSasToken) -batchAccountName $(batchAccountName) -batchAccountPoolName $(batchAccountPoolName) -applicationStorageAccountName $(applicationStorageAccountName)```

Gyakori eljárás a Azure Key Vault feladatok használata. Ha az egyszerű szolgáltatásnév (az Azure-előfizetéshez való kapcsolódás) megfelelő hozzáférési szabályzatok vannak beállítva, akkor letöltheti a titkokat egy Azure Key Vault, és változóként használhatja a folyamatában. A titok neve a társított értékkel lesz megadva. A sshPassword titka például a $ (sshPassword) kifejezéssel hivatkozhat a kiadás definíciójában.

1. A következő lépések meghívja az Azure CLI-t. Az első az alkalmazás Azure Batchban való létrehozására szolgál. és töltse fel a társított csomagokat.

    Adja hozzá az **Azure CLI** -feladatot, és állítsa be a következő tulajdonságokat:
    * **Megjelenítendő név:** Alkalmazás létrehozása Azure Batch fiókban
    * **Azure-előfizetés:** Válassza ki a megfelelő Azure-előfizetést
    * **Parancsfájl helye**: Beágyazott parancsfájl
    * **Beágyazott parancsfájl**:```az batch application create --application-id $(batchApplicationId) --name $(batchAccountName) --resource-group $(resourceGroupName)```

1. A második lépés a társított csomagok alkalmazásba való feltöltésére szolgál. Ebben az esetben az FFmpeg-fájlokat.

    Adja hozzá az **Azure CLI** -feladatot, és állítsa be a következő tulajdonságokat:
    * **Megjelenítendő név:** Csomag feltöltése Azure Batch fiókba
    * **Azure-előfizetés:** Válassza ki a megfelelő Azure-előfizetést
    * **Parancsfájl helye**: Beágyazott parancsfájl
    * **Beágyazott parancsfájl**:```az batch application package create --application-id $(batchApplicationId)  --name $(batchAccountName)  --resource-group $(resourceGroupName) --version $(batchApplicationVersion) --package-file=$(System.DefaultWorkingDirectory)/$(Release.Artifacts.{YourBuildArtifactSourceAlias}.BuildId).zip```

    > [!NOTE]
    > Az alkalmazáscsomag verziószáma változóra van állítva. Ez akkor lehet hasznos, ha a csomag korábbi verzióinak felülírásával Ön is működik, és ha manuálisan szeretné vezérelni a csomag verziószámát, amelyet leküldett a Azure Batch.

1. Új kiadás létrehozásához válassza a **kiadás > új kiadás létrehozása**lehetőséget. Az aktiválás után válassza ki az új kiadásra mutató hivatkozást az állapot megtekintéséhez.

1. Az ügynök élő kimenetét a környezet alatti **naplók** gombra kattintva tekintheti meg.

    ![A kiadás állapotának megtekintése](media/batch-ci-cd/Release-5.jpg)

### <a name="testing-the-environment"></a>A környezet tesztelése

A környezet beállítása után ellenőrizze, hogy a következő tesztek sikeresen befejeződtek-e.

Kapcsolódjon az új Azure Batch-fiókhoz az Azure CLI használatával egy PowerShell-parancssorból.

* Jelentkezzen be az Azure-fiókjába, `az login` és kövesse az utasításokat a hitelesítéshez.
* Most hitelesítse a Batch-fiókot:`az batch account login -g <resourceGroup> -n <batchAccount>`

#### <a name="list-the-available-applications"></a>Az elérhető alkalmazások listázása

```azurecli
az batch application list -g <resourcegroup> -n <batchaccountname>
```

#### <a name="check-the-pool-is-valid"></a>A készlet érvényességének ellenőrzéséhez

```azurecli
az batch pool list
```

Jegyezze fel `currentDedicatedNodes` a parancs kimenetének értékét. Ezt az értéket a következő tesztben kell beállítani.

#### <a name="resize-the-pool"></a>A készlet átméretezése

Méretezze át a készletet úgy, hogy elérhetők legyenek a feladatok és a tevékenységek teszteléséhez szükséges számítási csomópontok. a készlet lista parancsával ellenőrizze, hogy az aktuális állapot, amíg az átméretezés be nem fejeződik, és rendelkezésre áll-e csomópontok

```azurecli
az batch pool resize --pool-id <poolname> --target-dedicated-nodes 4
```

## <a name="next-steps"></a>További lépések

A jelen cikk mellett két olyan oktatóanyag is létezik, amely az FFmpeg-t használja a .NET és a Python használatával. A Batch-fiók egyszerű alkalmazáson keresztül történő kezelésével kapcsolatos további információkért tekintse meg ezeket az oktatóanyagokat.

* [Párhuzamos számítási feladatok futtatása Azure Batch a Python API használatával](tutorial-parallel-python.md)
* [Párhuzamos számítási feladat futtatása Azure Batch a .NET API használatával](tutorial-parallel-dotnet.md)
