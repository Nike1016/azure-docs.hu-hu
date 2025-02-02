---
title: 'Oktatóanyag: Adatok áthelyezése az Azure PowerShell-lel folyamatok létrehozására |} A Microsoft Docs'
description: Az oktatóanyag során létrehoz egy Azure Data Factory-folyamatot másolási tevékenységgel az Azure PowerShell használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: 71087349-9365-4e95-9847-170658216ed8
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: ce9566618870419e22dc9c420e77ef2ed6cd3c81
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836550"
---
# <a name="tutorial-create-a-data-factory-pipeline-that-moves-data-by-using-azure-powershell"></a>Oktatóanyag: Hozzon létre egy Data Factory-folyamatot adatok áthelyezéséhez az Azure PowerShell-lel
> [!div class="op_single_selector"]
> * [Áttekintés és előfeltételek](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Másolás varázsló](data-factory-copy-data-wizard-tutorial.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Azure Resource Manager-sablon](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse meg a [másolási tevékenység oktatóanyagát](../quickstart-create-data-factory-powershell.md). 

A cikk útmutatást nyújt adat-előállítók PowerShell használatával való létrehozására olyan folyamatokkal, amelyek az Azure Blob Storage-ból másolnak adatokat egy Azure SQL-adatbázisba. Ha még csak ismerkedik az Azure Data Factory szolgáltatással, olvassa el a [Bevezetés az Azure Data Factory használatába](data-factory-introduction.md) című cikket az oktatóanyag elvégzése előtt.   

Ebben az oktatóanyagban egy folyamatot egy tevékenységgel rendelkező létrehozása: Másolási tevékenység. A másolási tevékenység adatokat másol a forrásadattárból egy támogatott fogadó adattárba. A forrásként és fogadóként támogatott adattárak listájáért lásd: [támogatott adattárak](data-factory-data-movement-activities.md#supported-data-stores-and-formats). A tevékenységet egy globálisan elérhető szolgáltatás működteti, amely biztonságos, megbízható és skálázható módon másolja az adatokat a különböző adattárak között. További információ a másolási tevékenységről: [adatáthelyezési tevékenységek](data-factory-data-movement-activities.md).

Egy folyamathoz több tevékenység is tartozhat. Ezenkívül össze is fűzhet két tevékenységet (egymás után futtathatja őket), ha az egyik tevékenység kimeneti adatkészletét a másik tevékenység bemeneti adatkészleteként állítja be. További információért lásd: [egy folyamaton belüli több tevékenység](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline).

> [!NOTE]
> Ez a cikk nem tárgyalja az összes Data Factory-parancsmagot. A parancsmagokkal kapcsolatos átfogó dokumentációért tekintse meg a [Data Factory-parancsmagok referenciáját](/powershell/module/az.datafactory).
> 
> Az oktatóanyagban található adatfeldolgozási folyamat adatokat másol egy forrásadattárból egy céladattárba. Adatok átalakítása az Azure Data Factory használatával kapcsolatos oktatóanyagért lásd: [oktatóanyag: Az adatok Hadoop-fürttel történő átalakítására szolgáló folyamat létrehozása](data-factory-build-your-first-pipeline.md).

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

- Hajtsa végre [Az oktatóanyag előfeltételei](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) című cikkben előfeltételként felsorolt lépéseket.
- Telepítse az **Azure PowerShellt**. Kövesse [az Azure PowerShell telepítését és konfigurálását](/powershell/azure/install-Az-ps) ismertető cikkben szereplő utasításokat.

## <a name="steps"></a>Lépések
Az oktatóanyag során a következő lépéseket fogja elvégezni:

1. Azure **adat-előállító** létrehozása. Ebben a lépésben egy adat-előállítót hoz létre ADFTutorialDataFactoryPSH néven. 
1. Hozzon létre **társított szolgáltatásokat** az adat-előállítóban. Ebben a lépésben két típusú társított szolgáltatást hoz létre: Az Azure Storage és az Azure SQL-adatbázis. 
    
    Az AzureStorageLinkedService az Azure Storage-fiókot társítja az adat-előállítóval. Létrehozott egy tárolót, és adatokat töltött fel ebbe a tárfiókba az [előfeltételek](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) részeként.   

    Az AzureSqlLinkedService az Azure SQL-adatbázist társítja az adat-előállítóval. A blobtárolóból másolt adatokat a rendszer ebben az adatbázisban tárolja. Az [előfeltételek](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) részeként létrehozta az SQL-táblát az adatbázisban.   
1. Hozza létre a bemeneti és kimeneti **adatkészleteket** az adat-előállítóban.  
    
    Az Azure Storage társított szolgáltatása határozza meg azt a kapcsolati sztringet, amelyet futtatáskor a Data Factory szolgáltatás az Azure Storage-fiók csatlakoztatásához használ. A bemeneti blob adatkészlete pedig a tárolót és a bemeneti adatokat tartalmazó mappát határozza meg.  

    Ehhez hasonlóan az Azure SQL Database társított szolgáltatása határozza meg azt a kapcsolati sztringet, amelyet futtatáskor a Data Factory szolgáltatás az Azure SQL-adatbázishoz való csatlakoztatáshoz használ. Az SQL-tábla kimeneti adatkészlete határozza meg azt az adatbázistáblát, amelybe a rendszer a blobtárolóból származó adatokat másolja.
1. Hozzon létre egy **folyamatot** az adat-előállítóban. Ebben a lépésben létre fog hozni egy másolási tevékenységgel rendelkező folyamatot.   
    
    A másolási tevékenység adatokat másol az Azure Blob Storage-ból az Azure SQL-adatbázis egyik táblájába. A folyamat másolási tevékenységével adatokat másolhat bármely támogatott forrásból bármely támogatott célhelyre. A támogatott adattárak listájáért lásd: [adatáthelyezési tevékenységek](data-factory-data-movement-activities.md#supported-data-stores-and-formats). 
1. A folyamat figyelése. Ebben a lépésben a bemeneti és a kimeneti adatkészletek szeleteit **figyeli** PowerShell használatával.

## <a name="create-a-data-factory"></a>Data factory létrehozása
> [!IMPORTANT]
> Hajtsa végre az [oktatóanyag előfeltételeinek lépéseit](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md), ha még nem tette volna meg.   

A data factory egy vagy több folyamattal rendelkezhet. A folyamaton belül egy vagy több tevékenység lehet. Például egy olyan másolási tevékenység, amely adatokat másol egy forrásadattárból egy céladattárba, és egy HDInsight Hive-tevékenység, amely egy Hive-szkript futtatásával alakítja át a bemeneti adatokat kimeneti adatokká. Ebben a lépésben létrehozzuk a data factoryt.

1. Indítsa el a **PowerShellt**. Az Azure PowerShellt hagyja megnyitva az oktatóanyag végéig. Ha bezárja és újra megnyitja a programot, akkor újra le kell futtatnia a parancsokat.

    Futtassa a következő parancsot, és adja meg az Azure Portalra való bejelentkezéshez használt felhasználónevet és jelszót.

    ```powershell
    Connect-AzAccount
    ```   
   
    Futtassa a következő parancsot a fiókhoz tartozó előfizetések megtekintéséhez.

    ```powershell
    Get-AzSubscription
    ```

    Futtassa a következő parancsot a használni kívánt előfizetés kiválasztásához. Cserélje a **&lt;NameOfAzureSubscription**&gt; kifejezést az Azure-előfizetése nevére.

    ```powershell
    Get-AzSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzContext
    ```
1. Hozzon létre egy Azure-erőforráscsoportot **ADFTutorialResourceGroup** néven a következő parancs futtatásával:

    ```powershell
    New-AzResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
    ```
    
    Az oktatóanyag különböző lépései során feltételezzük, hogy az **ADFTutorialResourceGroup** elnevezésű erőforráscsoportot használja. Ha másik erőforráscsoportot használ, akkor az oktatóanyagban azt használja az ADFTutorialResourceGroup helyett.
1. Futtassa a **New-AzDataFactory** parancsmaggal hozzon létre egy data factoryt **ADFTutorialDataFactoryPSH**:  

    ```powershell
    $df=New-AzDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH –Location "West US"
    ```
    Előfordulhat, hogy ez a név már foglalt. Ezért egyedivé az adat-előállító nevét egy elő- vagy utótag hozzáadásával (például: ADFTutorialDataFactoryPSH05152017), és futtassa újra a parancsot.  

Vegye figyelembe a következő szempontokat:

* Az Azure data factory nevének globálisan egyedinek kell lennie. Ha a következő hibaüzenetet kapja, módosítsa a nevet (például sajátnévADFTutorialDataFactoryPSH-ra). Használja ezt az ADFTutorialFactoryPSH helyett az oktatóanyag lépéseinek végrehajtása során. A Data Factory-összetevők részleteit a [Data Factory elnevezési szabályait](data-factory-naming-rules.md) ismertető témakörben találja.

    ```
    Data factory name “ADFTutorialDataFactoryPSH” is not available
    ```
* Data Factory-példányok létrehozásához az Azure-előfizetés közreműködőjének vagy rendszergazdájának kell lennie.
* Az adat-előállító neve később DNS-névként regisztrálható, így nyilvánosan láthatóvá tehető.
* A következő hiba jelenhet meg: "**Ez az előfizetés nincs regisztrálva a Microsoft.DataFactory névtér használatára.** " Tegye a következők egyikét, és próbálkozzon újra a közzététellel:

  * Az Azure PowerShellben futtassa az alábbi parancsot a Data Factory-szolgáltató regisztrálásához:

    ```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```

    Az alábbi parancs futtatásával ellenőrizheti, hogy a Data Factory-szolgáltató regisztrálva van-e.

    ```powershell
    Get-AzResourceProvider
    ```
  * Az Azure-előfizetés használatával jelentkezzen be az [Azure Portalra](https://portal.azure.com). Navigáljon egy Data Factory-panelre, vagy hozzon létre egy data factoryt az Azure Portalon. Ezzel a művelettel automatikusan regisztrálja a szolgáltatót.

## <a name="create-linked-services"></a>Társított szolgáltatások létrehozása
Társított szolgáltatásokat hoz létre egy adat-előállítóban az adattárak és a számítási szolgáltatások adat-előállítóval történő társításához. Ebben az oktatóanyagban nem használunk számítási szolgáltatásokat (például Azure HDInsight vagy Azure Data Lake Analytics). Csak kétféle típusú adattárat használunk: Azure Storage (forrás) és Azure SQL Database (cél). 

Ezért AzureStorageLinkedService és azuresqllinkedservice néven típusú két társított szolgáltatást hoz létre: AzureStorage és AzureSqlDatabase.  

Az AzureStorageLinkedService az Azure Storage-fiókot társítja az adat-előállítóval. Ebben a tárfiókban hozta létre a tárolót, és ebbe töltötte fel az adatokat az [előfeltételek](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) részeként.   

Az AzureSqlLinkedService az Azure SQL-adatbázist társítja az adat-előállítóval. A blobtárolóból másolt adatokat a rendszer ebben az adatbázisban tárolja. Az [előfeltételek](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) részeként létrehozta az emp táblát az adatbázisban. 

### <a name="create-a-linked-service-for-an-azure-storage-account"></a>Társított szolgáltatás létrehozása Azure Storage-fiókhoz
Ebben a lépésben társítja az Azure Storage-fiókot az adat-előállítóval.

1. Hozzon létre egy JSON-fájlt **AzureStorageLinkedService.json** a **C:\ADFGetStartedPSH** mappában az alábbi tartalommal: (Ha még nem létezik, hozza létre az ADFGetStartedPSH mappát.)

    > [!IMPORTANT]
    > A fájl mentése előtt az &lt;accountname&gt; és az &lt;accountkey&gt; kifejezés helyére írja be Azure Storage-tárfiókja nevét, illetve kulcsát. 

    ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        }
     }
    ``` 
1. Az **Azure PowerShellben** váltson az **ADFGetStartedPSH** mappára.
1. Futtassa a **New-AzDataFactoryLinkedService** parancsmagot a társított szolgáltatás létrehozásához: **AzureStorageLinkedService**. Ehhez, valamint az oktatóanyagban használt többi Data Factory-parancsmaghoz is meg kell adnia értékeket a **ResourceGroupName** és a **DataFactoryName** paraméterek számára. Másik lehetőségként a ResourceGroupName és a datafactoryname értékeket a parancsmag futtatásakor beírása nélkül a New-AzDataFactory parancsmag által visszaadott DataFactory-objektum adhat át. 

    ```powershell
    New-AzDataFactoryLinkedService $df -File .\AzureStorageLinkedService.json
    ```
    Itt látható a minta kimenete:

    ```
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    Properties        : Microsoft.Azure.Management.DataFactories.Models.LinkedServiceProperties
    ProvisioningState : Succeeded
    ``` 

    A társított szolgáltatás létrehozásának egy másik módja egy erőforráscsoport és egy adat-előállító nevének megadása a DataFactory-objektum helyett.  

    ```powershell
    New-AzDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName <Name of your data factory> -File .\AzureStorageLinkedService.json
    ```

### <a name="create-a-linked-service-for-an-azure-sql-database"></a>Társított szolgáltatás létrehozása Azure SQL-adatbázishoz
Ebben a lépésben társítani fogja az Azure SQL-adatbázist az adat-előállítóhoz.

1. Hozzon létre egy AzureSqlLinkedService.json nevű JSON-fájlt a C:\ADFGetStartedPSH mappában az alábbi tartalommal:

    > [!IMPORTANT]
    > A &lt;servername&gt;, &lt;databasename&gt;, &lt;username@servername&gt; és &lt;password&gt; paraméterek értékét cserélje le az Azure SQL-kiszolgáló, az adatbázis és a felhasználói fiók nevére, valamint a jelszóra.
    
    ```json
    {
        "name": "AzureSqlLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": "Server=tcp:<server>.database.windows.net,1433;Database=<databasename>;User ID=<user>@<server>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        }
     }
    ```
1. Futtassa az alábbi parancsot egy társított szolgáltatás létrehozásához.

    ```powershell
    New-AzDataFactoryLinkedService $df -File .\AzureSqlLinkedService.json
    ```
    
    Itt látható a minta kimenete:

    ```
    LinkedServiceName : AzureSqlLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    Properties        : Microsoft.Azure.Management.DataFactories.Models.LinkedServiceProperties
    ProvisioningState : Succeeded
    ```

   Győződjön meg arról, hogy az **Allow access to Azure services** (Azure-szolgáltatásokhoz való hozzáférés engedélyezése) beállítás BE van kapcsolva az SQL-adatbáziskiszolgálón. Az ellenőrzéséhez és bekapcsolásához hajtsa végre a következő lépéseket:

    1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com)
    1. Kattintson a bal oldalon a **További szolgáltatások>** menüpontra, majd az **SQL kiszolgálók**ra az **ADATBÁZISOK** kategóriában.
    1. Válassza ki a kiszolgálóját az SQL-kiszolgálók listájából.
    1. Az SQL-kiszolgáló panelen kattintson a **Tűzfal-beállítások mutatása** hivatkozásra.
    1. A **Tűzfalbeállítások** panelen kattintson a **BE** kapcsolóra az **Azure-szolgáltatások hozzáférésének engedélyezése** beállítás mellett.
    1. Kattintson az eszköztár **Mentés** elemére. 

## <a name="create-datasets"></a>Adatkészletek létrehozása
Az előző lépésben létrehozta az Azure Storage-fiók és az Azure SQL-adatbázis összekapcsolását végző társított szolgáltatásokat. Ebben a lépésben két adatkészletet határoz meg – InputDataset és OutputDataset néven –, amelyek az AzureStorageLinkedService és az AzureSqlLinkedService szolgáltatás által hivatkozott bemeneti és kimeneti adatokat jelölik.

Az Azure Storage társított szolgáltatása határozza meg azt a kapcsolati sztringet, amelyet futtatáskor a Data Factory szolgáltatás az Azure Storage-fiók csatlakoztatásához használ. A bemeneti blob adatkészlete (InputDataset) pedig a tárolót és a bemeneti adatokat tartalmazó mappát határozza meg.  

Ehhez hasonlóan az Azure SQL Database társított szolgáltatása határozza meg azt a kapcsolati sztringet, amelyet futtatáskor a Data Factory szolgáltatás az Azure SQL-adatbázishoz való csatlakoztatáshoz használ. Az SQL-tábla kimeneti adatkészlete (OututDataset) határozza meg azt az adatbázistáblát, amelybe a rendszer a blobtárolóból származó adatokat másolja. 

### <a name="create-an-input-dataset"></a>Bemeneti adatkészlet létrehozása
Ebben a lépésben hozza létre az InputDataset nevű adatkészletet, amely az AzureStorageLinkedService társított szolgáltatás által hivatkozott Azure Storage blobtárolójának (adftutorial) gyökérmappájában található blobfájlra mutat (emp.txt). Ha nem ad meg értéket a fájlnévnek (vagy kihagyja azt), a rendszer a bemeneti mappában található összes blob adatát a célhelyre másolja. Ebben az oktatóanyagban a fileName értékét adja meg.  

1. Hozzon létre egy JSON-fájlt **InputDataset.json** néven a **C:\ADFGetStartedPSH** mappában az alábbi tartalommal:

    ```json
    {
        "name": "InputDataset",
        "properties": {
            "structure": [
                {
                    "name": "FirstName",
                    "type": "String"
                },
                {
                    "name": "LastName",
                    "type": "String"
                }
            ],
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "fileName": "emp.txt",
                "folderPath": "adftutorial/",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "external": true,
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
     }
    ```

    Az alábbi táblázat ismerteti a kódrészletben használt JSON-tulajdonságokat:

    | Tulajdonság | Leírás |
    |:--- |:--- |
    | type | A tulajdonság beállításának értéke **AzureBlob**, mert az adatok egy Azure Blob Storage-tárban találhatók. |
    | linkedServiceName | A korábban létrehozott **AzureStorageLinkedService** szolgáltatásra hivatkozik. |
    | folderPath | A **blobtárolót** és a bemeneti blobokat tartalmazó **mappát** határozza meg. Ebben az oktatóanyagban az adftutorial a blobtároló és a folder a gyökérmappa. | 
    | fileName | Ez a tulajdonság nem kötelező. Ha kihagyja, a rendszer a folderPath elérési úton található összes fájlt kiválasztja. Ebben az oktatóanyagban az **emp.txt** a fileName értéke, így a rendszer csak ezt a fájlt használja a feldolgozáshoz. |
    | format -> type |A bemeneti fájl szöveges formátumú, ezért a **TextFormat** értéket használjuk. |
    | columnDelimiter | A bemeneti fájlban **vesszővel (`,`)** vannak elválasztva az oszlopok. |
    | frequency/interval | A frequency (gyakoriság) beállítása **Hour** (Óra), az interval (időköz) beállítása pedig **1**, ami azt jelenti, hogy a bemeneti szeletek **óránként** érhetők el. Vagyis a Data Factory szolgáltatás óránként keres bemeneti adatokat a megadott blobtároló (**adftutorial**) gyökérmappájában. A szolgáltatás a folyamat kezdő és befejező időpontja közti időszakban – és nem azon kívül – keres adatokat.  |
    | external | Ez a tulajdonság a **true** (igaz) értékre van állítva, ha az adatokat nem ez a folyamat hozta létre. Az oktatóanyagban használt bemeneti adatok az emp.txt fájlban találhatók, amelyet nem ez a folyamat hoz létre, ezért ezt a tulajdonságot true (igaz) értékre állítottuk. |

    Ezekről a JSON-tulajdonságokról további tudnivalók az [Azure Blob-összekötőről](data-factory-azure-blob-connector.md#dataset-properties) szóló cikkben olvashatók.
1. A Data Factory-adatkészlet létrehozásához futtassa az alábbi parancsot.

    ```powershell  
    New-AzDataFactoryDataset $df -File .\InputDataset.json
    ```
    Itt látható a minta kimenete:

    ```
    DatasetName       : InputDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    Availability      : Microsoft.Azure.Management.DataFactories.Common.Models.Availability
    Location          : Microsoft.Azure.Management.DataFactories.Models.AzureBlobDataset
    Policy            : Microsoft.Azure.Management.DataFactories.Common.Models.Policy
    Structure         : {FirstName, LastName}
    Properties        : Microsoft.Azure.Management.DataFactories.Models.DatasetProperties
    ProvisioningState : Succeeded
    ```

### <a name="create-an-output-dataset"></a>Kimeneti adatkészlet létrehozása
A lépés ezen részében egy kimeneti adatkészletet hoz létre **OutputDataset** néven. Ez az adathalmaz egy SQL-táblára mutat abban az Azure SQL-adatbázisban, amelyet az **AzureSqlLinkedService** jelöl. 

1. Hozzon létre egy JSON-fájlt **OutputDataset.json** néven a **C:\ADFGetStartedPSH** mappában az alábbi tartalommal:

    ```json
    {
        "name": "OutputDataset",
        "properties": {
            "structure": [
                {
                    "name": "FirstName",
                    "type": "String"
                },
                {
                    "name": "LastName",
                    "type": "String"
                }
            ],
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": {
                "tableName": "emp"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```

    Az alábbi táblázat ismerteti a kódrészletben használt JSON-tulajdonságokat:

    | Tulajdonság | Leírás |
    |:--- |:--- |
    | type | A type tulajdonság beállítása **AzureSqlTable**, mert az adatok másolása az Azure SQL-adatbázis egyik táblájába történik. |
    | linkedServiceName | A korábban létrehozott **AzureSqlLinkedService** szolgáltatásra hivatkozik. |
    | tableName | Azt a **táblát** határozza meg, amelybe a rendszer az adatokat másolja. | 
    | frequency/interval | A frequency (gyakoriság) értéke **Hour** (Óra), az interval (időköz) értéke pedig **1**, azaz a rendszer a kimeneti szeleteket **óránként** állítja elő a folyamat kezdő és befejező időpontja közti időszakban (és nem azon kívül).  |

    Az adatbázis emp táblájában három oszlop van – **ID**, **FirstName** és **LastName**. Az ID azonosítóoszlop, ezért itt csak a **FirstName** és **LastName** tulajdonságokat kell megadnia.

    További információ ezekről a JSON-tulajdonságokról: [Azure SQL-összekötő](data-factory-azure-sql-connector.md#dataset-properties).
1. A data factory-adatkészlet létrehozásához futtassa az alábbi parancsot.

    ```powershell   
    New-AzDataFactoryDataset $df -File .\OutputDataset.json
    ```

    Itt látható a minta kimenete:

    ```
    DatasetName       : OutputDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    Availability      : Microsoft.Azure.Management.DataFactories.Common.Models.Availability
    Location          : Microsoft.Azure.Management.DataFactories.Models.AzureSqlTableDataset
    Policy            :
    Structure         : {FirstName, LastName}
    Properties        : Microsoft.Azure.Management.DataFactories.Models.DatasetProperties
    ProvisioningState : Succeeded
    ```

## <a name="create-a-pipeline"></a>Folyamat létrehozása
Ebben a lépésben létrehoz egy **másolási tevékenységgel** rendelkező folyamatot, amely bemenetként az **InputDataset**, kimenetként pedig az **OutputDataset** adatkészletet használja.

Jelenleg a kimeneti adatkészlet határozza meg az ütemezést. Az oktatóanyagban a kimeneti adatkészletet úgy konfiguráljuk, hogy a szeletek létrehozása óránként történjen meg. A folyamat kezdő és befejező időpontja között egy nap, azaz 24 óra telik el. Ezért a folyamat a kimeneti adatkészletből 24 szeletet hoz létre. 


1. Hozzon létre egy JSON-fájlt **ADFTutorialPipeline.json** néven a **C:\ADFGetStartedPSH** mappában az alábbi tartalommal:

    ```json   
    {
      "name": "ADFTutorialPipeline",
      "properties": {
        "description": "Copy data from a blob to Azure SQL table",
        "activities": [
          {
            "name": "CopyFromBlobToSQL",
            "type": "Copy",
            "inputs": [
              {
                "name": "InputDataset"
              }
            ],
            "outputs": [
              {
                "name": "OutputDataset"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "SqlSink",
                "writeBatchSize": 10000,
                "writeBatchTimeout": "60:00:00"
              }
            },
            "Policy": {
              "concurrency": 1,
              "executionPriorityOrder": "NewestFirst",
              "retry": 0,
              "timeout": "01:00:00"
            }
          }
        ],
        "start": "2017-05-11T00:00:00Z",
        "end": "2017-05-12T00:00:00Z"
      }
    } 
    ```
    Vegye figyelembe a következő szempontokat:
   
   - A tevékenységek szakaszban csak egyetlen tevékenység van, amelynek a **típusa** **Copy** értékre van beállítva. További információ a másolási tevékenységről: [adatáthelyezési tevékenységek](data-factory-data-movement-activities.md). A Data Factory megoldásaiban használhatja az [adatátalakítási tevékenységeket](data-factory-data-transformation-activities.md) is.
   - A tevékenység bemenetének beállítása **InputDataset**, a kimeneté pedig **OutputDataset**. 
   - A **typeProperties** szakaszban forrástípusként a **BlobSource**, fogadótípusként pedig az **SqlSink** érték van megadva. A másolási tevékenység által forrásként és fogadóként támogatott adattárak teljes listájáért lásd: [támogatott adattárak](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Egy forrásként/fogadóként támogatott konkrét adattár használatával kapcsolatos útmutatóért kattintson a tábla adott hivatkozására.  
     
     A **start** (kezdés) tulajdonság értékét cserélje az aktuális, az **end** (befejezés) tulajdonság értékét pedig a következő napra. Azt is megteheti, hogy a dátum-időpont paraméternek csak a dátum részét adja meg, az időpont részét pedig kihagyja. Megadhatja például a „2016-02-03” értéket, amely a következőnek felel meg: „2016-02-03T00:00:00Z”
     
     Mind a kezdő, mind a befejező dátum-időpont értéket [ISO formátumban](https://en.wikipedia.org/wiki/ISO_8601) kell megadni. Példa: 2016-10-14T16:32:41Z. Az **end** (befejező) időpont megadása opcionális, a jelen oktatóanyagban azonban azt is használjuk. 
     
     Ha nem adja meg az **end** (befejezés) tulajdonság értékét, akkor a rendszer a „**kezdő időpont + 48 óra**” számítással határozza meg azt. A folyamat határozatlan ideig történő futtatásához adja meg a **9999-09-09** értéket az **end** (befejezés) tulajdonsághoz.
     
     Az előző példában 24 adatszelet van, mert a rendszer óránként létrehoz egy adatszeletet.

     A folyamathoz tartozó definíció JSON-tulajdonságainak leírásáért lásd: [folyamatok létrehozása](data-factory-create-pipelines.md). A másolási tevékenységhez tartozó definíció JSON-tulajdonságainak leírásáért lásd: [adatáthelyezési tevékenységek](data-factory-data-movement-activities.md). A BlobSource által támogatott JSON-tulajdonságok leírásáért lásd: [Azure Blob-összekötő](data-factory-azure-blob-connector.md). Az SqlSink által támogatott JSON-tulajdonságok leírása az [Azure SQL Database-összekötő](data-factory-azure-sql-connector.md) című cikkben található.
1. A data factory-tábla létrehozásához futtassa az alábbi parancsot.

    ```powershell   
    New-AzDataFactoryPipeline $df -File .\ADFTutorialPipeline.json
    ```

    Itt látható a minta kimenete: 

    ```
    PipelineName      : ADFTutorialPipeline
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    Properties        : Microsoft.Azure.Management.DataFactories.Models.PipelinePropertie
    ProvisioningState : Succeeded
    ```

**Gratulálunk!** Sikeresen létrehozott egy Azure-beli adat-előállítót egy olyan folyamattal, amely az Azure Blob Storage-ból egy Azure SQL-adatbázisba másol adatokat. 

## <a name="monitor-the-pipeline"></a>A folyamat figyelése
Ebben a lépésben az Azure PowerShell használatával figyeli az Azure data factory eseményeit.

1. Cserélje le &lt;DataFactoryName&gt; nevét a data factory és a Futtatás **Get-AzDataFactory**, és rendelje a kimenetet egy $df változóhoz.

    ```powershell  
    $df=Get-AzDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name <DataFactoryName>
    ```

    Példa:
    ```powershell
    $df=Get-AzDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH0516
    ```
    
    Ez után futtassa le a $df tartalmát a következő kimenet előállításához: 
    
    ```
    PS C:\ADFGetStartedPSH> $df
    
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    DataFactoryId     : 6f194b34-03b3-49ab-8f03-9f8a7b9d3e30
    ResourceGroupName : ADFTutorialResourceGroup
    Location          : West US
    Tags              : {}
    Properties        : Microsoft.Azure.Management.DataFactories.Models.DataFactoryProperties
    ProvisioningState : Succeeded
    ```
1. Futtassa **Get-AzDataFactorySlice** összes szelet részleteinek az **OutputDataset**, azaz a kimeneti adatkészlet a folyamat.  

    ```powershell   
    Get-AzDataFactorySlice $df -DatasetName OutputDataset -StartDateTime 2017-05-11T00:00:00Z
    ```

   Ennek a beállításnak egyeznie kell a **Start** (Kezdés) értékkel a folyamat JSON-fájljában. 24 szeletet kell látnia, éjféltől másnap éjfélig.

   Itt látható három példaszelet a kimenetből: 

    ``` 
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    DatasetName       : OutputDataset
    Start             : 5/11/2017 11:00:00 PM
    End               : 5/12/2017 12:00:00 AM
    RetryCount        : 0
    State             : Ready
    SubState          :
    LatencyStatus     :
    LongRetryCount    : 0

    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    DatasetName       : OutputDataset
    Start             : 5/11/2017 9:00:00 PM
    End               : 5/11/2017 10:00:00 PM
    RetryCount        : 0
    State             : InProgress
    SubState          :
    LatencyStatus     :
    LongRetryCount    : 0   

    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    DatasetName       : OutputDataset
    Start             : 5/11/2017 8:00:00 PM
    End               : 5/11/2017 9:00:00 PM
    RetryCount        : 0
    State             : Waiting
    SubState          : ConcurrencyLimit
    LatencyStatus     :
    LongRetryCount    : 0
    ```
1. Futtatás **Get-AzDataFactoryRun** részleteit a tevékenység futtatása egy **adott** szeletet. Az előbbi parancs kimenetéből kimásolt dátum-idő értékkel adjon értéket a StartDateTime paraméternek. 

    ```powershell  
    Get-AzDataFactoryRun $df -DatasetName OutputDataset -StartDateTime "5/11/2017 09:00:00 PM"
    ```

   Itt látható a kimenet mintája: 

    ```
    Id                  : c0ddbd75-d0c7-4816-a775-704bbd7c7eab_636301332000000000_636301368000000000_OutputDataset
    ResourceGroupName   : ADFTutorialResourceGroup
    DataFactoryName     : ADFTutorialDataFactoryPSH0516
    DatasetName         : OutputDataset
    ProcessingStartTime : 5/16/2017 8:00:33 PM
    ProcessingEndTime   : 5/16/2017 8:01:36 PM
    PercentComplete     : 100
    DataSliceStart      : 5/11/2017 9:00:00 PM
    DataSliceEnd        : 5/11/2017 10:00:00 PM
    Status              : Succeeded
    Timestamp           : 5/16/2017 8:00:33 PM
    RetryAttempt        : 0
    Properties          : {}
    ErrorMessage        :
    ActivityName        : CopyFromBlobToSQL
    PipelineName        : ADFTutorialPipeline
    Type                : Copy  
    ```

A Data Factory-parancsmagokkal kapcsolatos átfogó dokumentációt a [Data Factory Cmdlet Reference](/powershell/module/az.datafactory) (Data Factory-parancsmagok referenciája) című cikk tartalmaz.

## <a name="summary"></a>Összegzés
Az oktatóanyag során létrehozott egy Azure data factoryt, hogy adatokat másoljon egy Azure-blobból egy Azure SQL-adatbázisba. A PowerShellt használta a data factory, a társított szolgáltatások, az adatkészletek és a folyamat létrehozásához. Az oktatóanyag során a következő főbb lépéseket végezte el:  

1. Létrehozott egy Azure **data factoryt**.
1. **Társított szolgáltatásokat** hozott létre:

   a. Egy **Azure Storage** társított szolgáltatást a bemeneti adatokat tároló Azure Storage-fiók társításához.     
   b. Egy **Azure SQL** társított szolgáltatást a kimeneti adatokat tároló SQL Database társításához.
1. **Adatkészleteket** hozott létre, amelyek a folyamat bemeneti és kimeneti adatait írják le.
1. Létrehozott egy **folyamatot** egy **Másolási tevékenységgel**, ahol a **BlobSource** a forrás, az **SqlSink** pedig a fogadó.

## <a name="next-steps"></a>További lépések
Ez az oktatóanyag egy olyan másolási műveletet mutatott be, amelynek a forrásadattára egy Azure Blob Storage-tár, a céladattára pedig egy Azure SQL-adatbázis volt. Az alábbi táblázatban a másolási tevékenység által támogatott forrásadattárak és céladattárak listája látható: 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

A táblázatban lévő adattárak hivatkozására kattintva megismerheti az adattárakba és az adattárakból történő adatmásolás módszereit. 

