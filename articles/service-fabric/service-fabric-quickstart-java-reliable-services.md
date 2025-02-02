---
title: 'Gyors útmutató: Java-alkalmazás létrehozása az Azure Service Fabric'
description: Ez a rövid útmutató azt ismerteti, hogyan lehet Java-alkalmazást létrehozni az Azure-ban a Service Fabric Reliable Services mintaalkalmazásának használatával.
services: service-fabric
documentationcenter: java
author: suhuruli
manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/29/2019
ms.author: suhuruli
ms.custom: mvc, devcenter, seo-java-august2019
ms.openlocfilehash: a69590adc329361ac1c2191e7a984757af4f69af
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68977110"
---
# <a name="quickstart-deploy-a-java-reliable-services-application-to-service-fabric"></a>Gyors útmutató: Java megbízható Services-alkalmazás üzembe helyezése Service Fabric

Az Azure Service Fabric egy elosztott rendszerplatform, amely mikroszolgáltatások és tárolók üzembe helyezésére és kezelésére szolgál.

Ez a gyors útmutató azt ismerteti, hogy hogyan helyezheti üzembe első Java-alkalmazását a Service Fabricben az Eclipse IDE használatával, Linux fejlesztői gépen. Az útmutató elvégzése után rendelkezni fog egy Java webes kezelőfelületes szavazóalkalmazással, amely egy a fürtben található állapotalapú háttérszolgáltatásba menti a szavazati adatokat.

![Képernyőkép az alkalmazásról](./media/service-fabric-quickstart-java/votingapp.png)

Ezen rövid útmutató segítségével megtanulhatja a következőket:

* Az Eclipse használata a Service Fabricben üzemeltetett Java-alkalmazásokhoz
* Az alkalmazás központi telepítése a helyi fürtre
* Az alkalmazás horizontális felskálázása több csomópontra

## <a name="prerequisites"></a>Előfeltételek

A gyorsútmutató elvégzéséhez:

1. [A Service Fabric SDK és a Service Fabric parancssori felület (CLI) telepítése](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#installation-methods)
2. [A Git telepítése](https://git-scm.com/)
3. [Az Eclipse telepítése](https://www.eclipse.org/downloads/)
4. [A Java környezet beállítása](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-java-development) az Eclipse beépülő modul telepítéséhez szükséges választható lépések elvégzésével

## <a name="download-the-sample"></a>A minta letöltése

Egy parancssori ablakban futtassa a következő parancsot a mintaalkalmazás-adattár helyi számítógépre történő klónozásához.

```git
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
```

## <a name="run-the-application-locally"></a>Az alkalmazás helyi futtatása

1. A helyi fürt elindításához futtassa a következő parancsot:

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```
    A helyi fürt elindítása eltarthat egy ideig. A fürt üzembe állásának ellenőrzéséhez nyissa meg a Service Fabric Explorert a **http://localhost:19080** címen. Ha öt kifogástalan csomópontot lát, a fürt megfelelően üzemel.

    ![A helyi fürt kifogástalan](./media/service-fabric-quickstart-java/localclusterup.png)

2. Nyissa meg az Eclipse környezetet.
3. Válassza **a fájl** > **importálása** > Gradlemeglévő > Gradle-**projekt** lehetőséget, és kövesse a varázslót.
4. Válassza a **címtár** lehetőséget, `Voting` majd válassza ki `service-fabric-java-quickstart` a könyvtárat a githubról klónozott mappából. Válassza a **Finish** (Befejezés) elemet.

    ![Az Eclipse importálási párbeszédablaka](./media/service-fabric-quickstart-java/eclipseimport.png)

5. Ezzel létrehozta a `Voting` projektet az Eclipse-hez készült Package Explorerben.
6. Kattintson a jobb gombbal a projektre, és válassza az **alkalmazás közzététele** a **Service Fabric** legördülő menüben. Válassza ki a **célprofilnak publishprofiles/local. JSON** fájlt célként megadott profilként, és válassza a **Közzététel**lehetőséget.

    ![Publish (Közzététel) helyi párbeszédpanel](./media/service-fabric-quickstart-java/localjson.png)

7. Nyissa meg a kedvenc webböngészőjét, és nyissa meg az alkalmazást az alkalmazáshoz `http://localhost:8080`való hozzáféréssel.

    ![Helyi alkalmazás kezelőfelülete](./media/service-fabric-quickstart-java/runninglocally.png)

Most hozzáadhat szavazási lehetőségeket az alkalmazáshoz, és megkezdheti a szavazatok gyűjtését. Az alkalmazás különálló adatbázis nélkül futtatja és tárolja az összes adatot a Service Fabric-fürtben.

## <a name="scale-applications-and-services-in-a-cluster"></a>Alkalmazások és szolgáltatások méretezése a fürtökben

A szolgáltatások skálázhatók egy adott fürtben, hogy kövessék a szolgáltatások terhelésének változásait. A szolgáltatások méretezése a fürtben futó példányok számának módosításával történik. A szolgáltatásokat többféleképpen is skálázhatja, használhat például szkripteket, vagy a Service Fabric parancssori felület (sfctl) parancsait. A következő lépések során a Service Fabric Explorert használjuk.

A Service Fabric Explorer az összes Service Fabric-fürtben fut. Az eléréséhez egy böngészőben navigáljon az adott fürt HTTP-kezelési portjára (19080), például: `http://localhost:19080`.

A webes előtér-szolgáltatás skálázásához tegye a következőket:

1. Nyissa meg a Service Fabric Explorert a fürtben – például: `https://localhost:19080`.
2. Válassza a **háló:/szavazó/VotingWeb** csomópont melletti három pontot ( **..** .) a TreeView vezérlőben, és válassza a **szolgáltatás méretezése**lehetőséget.

    ![A Service Fabric Explorer méretezési szolgáltatása](./media/service-fabric-quickstart-java/scaleservicejavaquickstart.png)

    Most már méretezheti a webes előtér-szolgáltatás példányainak számát.

3. Módosítsa a számot **2** értékre, és válassza a **szolgáltatás méretezése**lehetőséget.
4. Válassza ki a **háló:/szavazás/VotingWeb** csomópontot a fanézetben, és bontsa ki a partíciós csomópontot (amelyet egy GUID képvisel).

    ![A Service Fabric Explorer skálázási szolgáltatása kész](./media/service-fabric-quickstart-java/servicescaled.png)

    Ekkor láthatja, hogy a szolgáltatás két példányban jelenik meg, a fanézetben pedig megtekintheti, hogy a példányok melyik csomópontokon futnak.

Ezzel az egyszerű felügyeleti eljárással megduplázta az előtér-szolgáltatás számára a felhasználói terhelések feldolgozásához rendelkezésre álló erőforrások mennyiségét. Fontos tudni, hogy nincs szükség több példányra ahhoz, hogy a szolgáltatás megbízhatóan fusson. Ha egy szolgáltatás meghibásodik, a Service Fabric gondoskodik róla, hogy egy új szolgáltatáspéldány elinduljon a fürtben.

## <a name="next-steps"></a>További lépések

Ennek a rövid útmutatónak a segítségével megtanulta a következőket:

* Az Eclipse használata a Service Fabricben üzemeltetett Java-alkalmazásokhoz
* Java-alkalmazások központi telepítése a helyi fürtre
* Az alkalmazás horizontális felskálázása több csomópontra

Ha bővebb információra van szüksége a Java-alkalmazások Service Fabricben való használatával kapcsolatban, lépjen tovább a Java-alkalmazásokról szóló oktatóanyagra.

> [!div class="nextstepaction"]
> [Java-alkalmazás üzembe helyezése](./service-fabric-tutorial-create-java-app.md)
