---
title: Databricks-jegyzetfüzet futtatása a Databricks-jegyzetfüzet tevékenységeivel az Azure Data Factoryban
description: Ismerje meg a Databricks-jegyzetfüzetek tevékenységeinek használatát egy Azure adat-előállítóban a Databricks-jegyzetfüzetek Databricks-feladatfürtön való futtatásához.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/12/2018
ms.author: abnarain
ms.reviewer: douglasl
ms.openlocfilehash: fad8045ac8bddb236f0f80ad223ebafc7aa7e93a
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/22/2019
ms.locfileid: "66002861"
---
# <a name="run-a-databricks-notebook-with-the-databricks-notebook-activity-in-azure-data-factory"></a>Databricks-jegyzetfüzet futtatása a Databricks-jegyzetfüzet tevékenységeivel az Azure Data Factoryban

Ebben az oktatóanyagban egy olyan Azure Data Factory-folyamatot hoz létre az Azure Portal használatával, amely végrehajt egy Databricks-jegyzetfüzetet a Databricks-feladatfürtön. Továbbítja továbbá az Azure Data Factory paramétereit a Databricks-jegyzetfüzetnek a végrehajtás során.

Az oktatóanyagban az alábbi lépéseket fogja végrehajtani:

  - Adat-előállító létrehozása

  - Hozzon létre egy Databricks-jegyzetfüzetek tevékenységeit használó folyamatot.

  - Folyamat futtatásának aktiválása

  - A folyamat futásának monitorozása.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

Az alábbi videóban a funkció bemutatását és ismertetését tekintheti meg tizenegy percben:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/ingest-prepare-and-transform-using-azure-databricks-and-data-factory/player]

## <a name="prerequisites"></a>Előfeltételek

  - **Azure Databricks-munkaterület**. [Hozzon létre egy Databricks-munkaterületet](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal), vagy használjon egy már meglévőt. Hozzon létre egy Python-jegyzetfüzetet az Azure Databricks-munkaterületen. Ezután hajtsa végre a jegyzetfüzetet, és továbbítson neki paramétereket az Azure Data Factory segítségével.

## <a name="create-a-data-factory"></a>Data factory létrehozása

1.  Indítsa el a **Microsoft Edge** vagy a **Google Chrome** böngészőt. A Data Factory felhasználói felületének használata jelenleg csak a Microsoft Edge-ben és a Google Chrome-ban támogatott.

1.  Kattintson az **Erőforrás létrehozása** elemre a bal oldali menüben, majd az **Analitika**, végül a **Data Factory** elemre.

    ![Új adat-előállító létrehozása](media/transform-data-using-databricks-notebook/new-azure-data-factory-menu.png)

1.  Az **Új adat-előállító** lap **Név** mezőjében adja meg az **ADFTutorialDataFactory** értéket.

    Az Azure data factory nevének *globálisan egyedinek* kell lennie. Ha a következő hibát látja, módosítsa az adat-előállító nevét. (Például: **\<sajátneve\>ADFTutorialDataFactory**). A Data Factory-összetevők elnevezési szabályait a [Data Factory elnevezési szabályait](https://docs.microsoft.com/azure/data-factory/naming-rules) ismertető cikkben találja.

    ![Az új adat-előállító elnevezése](media/transform-data-using-databricks-notebook/new-azure-data-factory.png)

1.  **Előfizetés:** válassza ki azt az Azure-előfizetést, amelyben az adat-előállítót létre szeretné hozni.

1.  **Erőforráscsoport:** hajtsa végre a következő lépések egyikét:
    
    - Kattintson a **Meglévő használata** elemre, majd a legördülő listából válasszon egy meglévő erőforráscsoportot.
    
    - Kattintson az **Új létrehozása** elemre, és adja meg az erőforráscsoport nevét.

    A rövid útmutató egyes lépései azt feltételezik, hogy az **ADFTutorialResourceGroup** nevet adta az erőforráscsoportnak. Az erőforráscsoportokkal kapcsolatos információkért tekintse meg a [Using resource groups to manage your Azure resources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez) című cikket.

1.  A **Verzió** résznél válassza a **V2** értéket.

1.  **Hely:** válassza ki az adat-előállító helyét.

    Azure-régióban, amelyben a Data Factory jelenleg listája, válassza ki a régiók, amelyek a következő oldalon érdeklődésére számot tartó, és bontsa ki **Analytics** található **adat-előállító**: [Régiónként elérhető termékek](https://azure.microsoft.com/global-infrastructure/services/). A Data Factory által használt adattárak (Azure Storage, Azure SQL Database stb.) és számítási erőforrások (HDInsight stb.) más régiókban is lehetnek.
1.  Kattintson a **Létrehozás** gombra.


1.  A létrehozás befejezése után megjelenik az **Adat-előállító** lap. A Data Factory felhasználói felületi (UI) alkalmazás külön lapon történő elindításához kattintson a **Létrehozás és monitorozás** csempére.

    ![Az adat-előállító felhasználói felület alkalmazás indítása](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image4.png)

## <a name="create-linked-services"></a>Társított szolgáltatások létrehozása

Ebben a szakaszban létrehoz egy Databricks társított szolgáltatást. Ez a társított szolgáltatás tartalmazza a Databricks-fürt kapcsolatadatait:

### <a name="create-an-azure-databricks-linked-service"></a>Azure Databricks-beli társított szolgáltatás létrehozása

1.  Az **Első lépések** lapon váltson a **Szerkesztés** lapra a bal oldali ablaktáblán.

    ![Az új társított szolgáltatás szerkesztése](media/transform-data-using-databricks-notebook/get-started-page.png)

1.  Kattintson az ablak alján látható **Kapcsolatok**, majd az **+ Új** elemre.
    
    ![Új kapcsolat létrehozása](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image6.png)

1.  Az **Új társított szolgáltatás** ablakban válassza a **Compute** \> **Azure Databricks** lehetőséget, majd kattintson a **Folytatás** elemre.
    
    ![Databricks társított szolgáltatás megadása](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image7.png)

1.  Az **Új társított szolgáltatás** ablakban végezze el az alábbi lépéseket:
    
    1.  A **Név** mezőben adja meg a következőt: ***AzureDatabricks\_LinkedService***
    
    1.  Válassza ki a megfelelő **Databricks-munkaterületet**, amelyen a jegyzetfüzetet futtatni fogja

    1.  A **Fürt kiválasztása** mezőben válassza az **Új feladatfürt** lehetőséget
    
    1.  A **Tartomány/régió** mezőben az adatoknak automatikusan ki kell töltődnie

    1.  A **Hozzáférési jogkivonatot** hozza létre az Azure Databricks-munkaterületről. A lépéseket [itt](https://docs.databricks.com/api/latest/authentication.html#generate-token) találhatja meg.

    1.  A **fürtverzió**válassza **4.2** (az Apache Spark 2.3.1, Scala 2.11-et)

    1.  A **Fürtcsomópont típusa** mezőben válassza a **Standard\_D3\_v2** lehetőséget az **Általános célú (HDD)** területen ehhez az oktatóanyaghoz. 
    
    1.  A **Feldolgozók** mezőben adja meg a **2** mennyiséget.
    
    1.  Válassza a **Befejezés** lehetőséget.

        ![A társított szolgáltatás létrehozásának befejezése](media/transform-data-using-databricks-notebook/new-databricks-linkedservice.png)

## <a name="create-a-pipeline"></a>Folyamat létrehozása

1.  Kattintson a **+** (plusz) gombra, majd a menüben válassza a **Folyamat** elemet.

    ![Új folyamat létrehozására szolgáló gombok](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image9.png)

1.  Hozzon létre egy, a **folyamatban** használni kívánt **paramétert**. Később ezt a paramétert átadhatja a Databricks-jegyzetfüzet tevékenységeinek. Az üres folyamatban kattintson a **Paraméterek** lapra, majd az **Új** lehetőségre, és adja neki a „**name**” nevet.

    ![Új paraméter létrehozása](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image10.png)

    ![A név paraméter létrehozása](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image11.png)

1.  A **Tevékenységek** eszközkészletben bontsa ki a **Databricks** elemet. Húzza a **Jegyzetfüzet** tevékenységet a **Tevékenységek** eszközkészletből a folyamat tervezőfelületére.

    ![A jegyzetfüzet áthúzása a tervezőfelületre](media/transform-data-using-databricks-notebook/new-adf-pipeline.png)

1.  A **Databricks**-**jegyzetfüzet** tevékenységeinek tulajdonságok ablakában végezze el az alábbi lépéseket:

    a. Váltson az **Azure Databricks** lapra.

    b. Válassza ki az előző lépésben létrehozott **AzureDatabricks\_LinkedService** elemet.

    c. Váltson a **Beállítások** lapra

    c. Tallózással válasszon ki egy Databricks-**jegyzetfüzet útvonalat**. Hozzunk létre egy jegyzetfüzetet, és adjuk meg itt az útvonalat. A következő néhány lépést követve megkaphatja a jegyzetfüzet útvonalat.

       1. Az Azure Databricks-munkaterület indítása

       1. Hozzon létre egy **Új mappát** a munkaterületen, és adja neki az **adftutorial** nevet.

          ![Új mappa létrehozása](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image13.png)

       1. [Hozzon létre egy új jegyzetfüzetet](https://docs.databricks.com/user-guide/notebooks/index.html#creating-a-notebook) (Python), neki **mynotebook** alatt **adftutorial** mappát, kattintson a **létrehozása.**

          ![Új jegyzetfüzet létrehozása](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image14.png)

          ![Az új jegyzetfüzet tulajdonságainak beállítása](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image15.png)

       1. Az újonnan létrehozott „mynotebook” jegyzetfüzetben adja hozzá a következő kódot:

           ```
           # Creating widgets for leveraging parameters, and printing the parameters

           dbutils.widgets.text("input", "","")
           dbutils.widgets.get("input")
           y = getArgument("input")
           print ("Param -\'input':")
           print (y)
           ```

           ![Widgetek létrehozása paraméterekhez](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image16.png)

       1. Ebben az esetben a **jegyzetfüzet útvonala** **/adftutorial/mynotebook**.

1.  Váltson vissza a **Data Factory felhasználói felületet létrehozó eszközre**. Navigáljon a **Beállítások** lapra az **1. jegyzetfüzet tevékenységei** területen.

    a.  **Adjon paramétert** a jegyzetfüzet tevékenységeihez. Ugyanazt a paramétert használja, mint amit korábban hozzáadott a **folyamathoz**.

       ![Paraméter hozzáadása](media/transform-data-using-databricks-notebook/new-adf-parameters.png)

    b.  A paraméter neve legyen **bemeneti** , és az értéket kifejezésként  **\@folyamat ().parameters.name**.

1.  A folyamat érvényesítéséhez kattintson az **Érvényesítés** gombra az eszköztáron. Az érvényesítési ablak bezárásához kattintson a **\>\>** (jobbra mutató nyíl) gombra.

    ![A folyamat érvényesítése](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image18.png)

1.  Kattintson **Az összes közzététele** gombra. A Data Factory felhasználói felülete entitásokat (társított szolgáltatásokat és folyamatot) tesz közzé az Azure Data Factory szolgáltatásban.

    ![Az új adat-előállító entitások közzététele](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image19.png)

## <a name="trigger-a-pipeline-run"></a>Folyamat futtatásának aktiválása

Kattintson az **Aktiválás** gombra az eszköztáron, majd válassza az **Aktiválás most** lehetőséget.

![Az Aktiválás most parancs kiválasztása](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image20.png)

A **Folyamatfuttatás** párbeszédpanel kéri a **név** paramétert. Itt paraméterként használja a következőt: **/path/filename**. Kattintson a **Befejezés** gombra.

![Érték megadása a név paraméterekhez](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image21.png)

## <a name="monitor-the-pipeline-run"></a>A folyamat futásának monitorozása

1.  Váltson a **Monitorozás** lapra. Ellenőrizze, hogy megjelenik-e a futó folyamat. Egy, a jegyzetfüzetet végrehajtó Databricks feladatfürt létrehozása körülbelül 5–8 percet vesz igénybe.

    ![A folyamat figyelése](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image22.png)

1.  Rendszeres időközönként kattintson a **Frissítés** gombra a folyamat futási állapotának ellenőrzéséhez.

1.  A folyamat futásához kapcsolódó tevékenységfuttatások megtekintéséhez kattintson a **Tevékenységfuttatások megtekintése** elemre a **Műveletek** oszlopban.

    ![A tevékenységfuttatások megtekintése](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image23.png)

A fenti **Folyamatok** elemre kattintva visszaválthat a folyamatfuttatások nézetre.

## <a name="verify-the-output"></a>Kimenet ellenőrzése

Jelentkezzen be az **Azure Databricks-munkaterületre**, lépjen a **Fürtökhöz**, és láthatja, hogy a **Feladat** állapota *végrehajtás függőben, fut vagy megszakítva*.

![A feladatfürt és a feladat megtekintése](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image24.png)

A **Feladat nevére** kattintva megtekintheti a további részleteket. Ha a futás sikeres, érvényesítheti az átadott paramétereket és a Python-jegyzetfüzet kimenetét.

![A futtatás részleteinek és kimenetének megtekintése](media/transform-data-using-databricks-notebook/databricks-output.png)

## <a name="next-steps"></a>További lépések

A mintában szereplő folyamat elindít egy Databricks-jegyzetfüzet tevékenységet, és átad neki egy paramétert. Megismerte, hogyan végezheti el az alábbi műveleteket:

  - Adat-előállító létrehozása

  - Hozzon létre egy Databricks-jegyzetfüzetek tevékenységeit használó folyamatot.

  - Folyamat futtatásának aktiválása

  - A folyamat futásának monitorozása.
