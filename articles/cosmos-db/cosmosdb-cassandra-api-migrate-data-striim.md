---
title: Az adatáttelepítés Azure Cosmos DB Cassandra API-fiókba a Striim használatával
description: Ismerje meg, hogyan telepítheti át az Striim egy Oracle-adatbázisból egy Azure Cosmos DB Cassandra API-fiókba.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: c739a6e79cab14ecc8e5a4be23c551d2774cbbd8
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68933976"
---
# <a name="migrate-data-to-azure-cosmos-db-cassandra-api-account-using-striim"></a>Az adatáttelepítés Azure Cosmos DB Cassandra API-fiókba a Striim használatával

Az Azure piactéren elérhető Striim-rendszerkép folyamatos, valós idejű adatáthelyezést biztosít az adattárházak és az adatbázisok között az Azure-ba. Az adatok áthelyezésekor elvégezhető a beágyazott denormalizálás, az adatok átalakítása, a valós idejű elemzések és az adatjelentéskészítési forgatókönyvek. Könnyen megkezdheti a Striim, hogy folyamatosan áthelyezze a vállalati adatAzure Cosmos DB Cassandra API. Az Azure egy Piactéri ajánlatot biztosít, amely megkönnyíti a Striim üzembe helyezését és az Azure Cosmos DBba való áttelepítését. 

Ez a cikk bemutatja, hogyan lehet a Striim használatával áttelepíteni az adatok egy **Oracle** -adatbázisból egy **Azure Cosmos db Cassandra API**-fiókba.

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik [Azure](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) .

* Egy helyszíni Oracle-adatbázis, amely néhány adattal rendelkezik.

## <a name="deploy-the-striim-marketplace-solution"></a>A Striim Marketplace-megoldás üzembe helyezése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Válassza az **erőforrás létrehozása** lehetőséget, és keresse meg a **Striim** az Azure Marketplace-en. Válassza ki az első lehetőséget, és **hozzon létre**.

   ![Striim Marketplace-tétel keresése](./media/cosmosdb-sql-api-migrate-data-striim/striim-azure-marketplace.png)

1. Ezután adja meg a Striim-példány konfigurációs tulajdonságait. A Striim-környezet üzembe helyezése egy virtuális gépen történik. Az **alapvető beállítások** panelen adja meg avirtuális gép felhasználónevét, a **virtuális gép jelszavát** (ezt a jelszót használja a rendszer az SSH-ba a virtuális géphez). Válassza kiaz előfizetését, az **erőforráscsoportot**és a **hely részleteit** , ahol a Striim telepíteni szeretné. Ha elkészült, kattintson **az OK gombra**.

   ![A Striim alapszintű beállításainak konfigurálása](./media/cosmosdb-sql-api-migrate-data-striim/striim-configure-basic-settings.png)


1. A **Striim-fürt beállításai** ablaktáblán válassza ki a Striim-telepítés típusát és a virtuális gép méretét.

   |Beállítás | Érték | Leírás |
   | ---| ---| ---|
   |Striim központi telepítési típus |Különálló | A Striim **önálló** vagy fürtbeli központi telepítési típusokban is futtathatók. Az önálló mód a Striim-kiszolgálót egyetlen virtuális gépre telepíti, és az adatmennyiségtől függően kiválaszthatja a virtuális gépek méretét. A fürt mód két vagy több virtuális gépre telepíti a Striim-kiszolgálót a kiválasztott mérettel. A több mint 2 csomóponttal rendelkező fürt-környezetek automatikus magas rendelkezésre állást és feladatátvételt biztosítanak.</br></br> Ebben az oktatóanyagban a különálló lehetőséget választhatja. Használja az alapértelmezett "Standard_F4s" méretű virtuális gépet. | 
   | A Striim-fürt neve|    < Striim_cluster_Name >|  A Striim-fürt neve.|
   | Striim-fürt jelszava|   < Striim_cluster_password >|  A fürt jelszava.|

   Az űrlap kitöltése után a folytatáshoz kattintson **az OK gombra** .

1. A **Striim-hozzáférési beállítások** panelen konfigurálja a **nyilvános IP-címet** (válassza ki az alapértelmezett értékeket), a **Striim**, a Striim felhasználói felületére való bejelentkezéshez használni kívánt **rendszergazdai jelszót** . Konfigurálja a VNET és az alhálózatot (válassza az alapértelmezett értékeket). A részletek kitöltése után kattintson **az OK gombra** a folytatáshoz.

   ![Striim hozzáférési beállításai](./media/cosmosdb-sql-api-migrate-data-striim/striim-access-settings.png)

1. Az Azure ellenőrzi a telepítést, és gondoskodik róla, hogy minden jól látható legyen; az ellenőrzés elvégzése néhány percet vesz igénybe. Az ellenőrzés befejezése után kattintson **az OK gombra**.
  
1. Végül tekintse át a használati feltételeket, és válassza a **Létrehozás** lehetőséget a Striim-példány létrehozásához. 

## <a name="configure-the-source-database"></a>A forrásadatbázis konfigurálása

Ebben a szakaszban az Oracle-adatbázist konfigurálja az adatáthelyezés forrásaként.  Az Oracle-hez való kapcsolódáshoz szüksége lesz az [Oracle JDBC](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html) -illesztőprogramra. Ha olvasni szeretné a forrás Oracle-adatbázis változásait, használhatja a [LogMiner](https://www.oracle.com/technetwork/database/features/availability/logmineroverview-088844.html) vagy a [xStream API-kat](https://docs.oracle.com/cd/E11882_01/server.112/e16545/xstrm_intro.htm#XSTRM72647). Az Oracle JDBC illesztőprogramjának jelen kell lennie a Striim Java-osztályútvonal az Oracle-adatbázisból származó adatok olvasásához, írásához és megőrzéséhez.

Töltse le a [ojdbc8. jar](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html) illesztőprogramot a helyi gépre. Később telepíteni fogja a Striim-fürtön.

## <a name="configure-target-database"></a>Céladatbázis konfigurálása

Ebben a szakaszban a Azure Cosmos DB Cassandra API fiókot fogja konfigurálni az adatáthelyezés céljára.

1. Hozzon létre egy [Azure Cosmos DB Cassandra API fiókot](create-cassandra-dotnet.md#create-a-database-account) a Azure Portal használatával.

1. Navigáljon a **adatkezelő** panelre az Azure Cosmos-fiókjában. Új tároló létrehozásához válassza az **új tábla** lehetőséget. Tegyük fel, hogy áttelepíti a *termékeket* , és az Oracle Database-ből Azure Cosmos DBra *rendeli* az adatokról. Hozzon létre egy új, **StriimDemo** nevű webtárhelyet egy Orders tárolóval. A tároló kiépítése a **1000 RUS**használatával (ez a példa a 1000 RUS-t használja, de a számítási feladathoz becsült átviteli sebességet kell használnia), és a **/ORDER_ID** elsődleges kulcsként kell megjelennie. Ezek az értékek a forrásadatoktől függően eltérőek lesznek. 

   ![Cassandra API fiók létrehozása](./media/cosmosdb-cassandra-api-migrate-data-striim/create-cassandra-api-account.png)

## <a name="configure-oracle-to-azure-cosmos-db-data-flow"></a>Az Oracle konfigurálása Azure Cosmos DB adatfolyamatra

1. Most térjünk vissza a Striim. A Striim való interakció előtt telepítse a korábban letöltött Oracle JDBC-illesztőprogramot.

1. Navigáljon a Azure Portal üzembe helyezett Striim-példányhoz. A felső menüsorban kattintson a **kapcsolat** gombra, és az **SSH** lapon másolja ki az URL-címet a **Bejelentkezés a virtuális gép helyi fiókja mező használatával** .

   ![Az SSH URL-cím beszerzése](./media/cosmosdb-sql-api-migrate-data-striim/get-ssh-url.png)

1. Nyisson meg egy új Terminálablak-ablakot, és futtassa a Azure Portalból másolt SSH-parancsot. Ez a cikk a Terminalt használja a MacOS-ben, a hasonló utasításokat a PuTTY vagy egy másik SSH-ügyfél használatával követheti Windows-gépen. Ha a rendszer kéri, írja be az **Igen értéket** a folytatáshoz, és adja meg az előző lépésben a virtuális géphez beállított **jelszót** .

   ![Kapcsolódás Striim virtuális géphez](./media/cosmosdb-sql-api-migrate-data-striim/striim-vm-connect.png)

1. Most nyisson meg egy új terminál fület a korábban letöltött **ojdbc8. jar** fájl másolásához. A következő SZOLGÁLTATÁSKAPCSOLÓDÁSI parancs használatával másolja a jar-fájlt a helyi gépről az Azure-ban futó Striim-példány tmp mappájába:

   ```bash
   cd <Directory_path_where_the_Jar_file_exists> 
   scp ojdbc8.jar striimdemo@striimdemo.westus.cloudapp.azure.com:/tmp
   ```

   ![Másolja a jar-fájlt a hely gépről a Striim-be](./media/cosmosdb-sql-api-migrate-data-striim/copy-jar-file.png)

1. Ezután lépjen vissza az ablakba, ahol az SSH-t a Striim-példányhoz, és jelentkezzen be sudo-ként. Helyezze át a **ojdbc8. jar** fájlt a **/tmp** könyvtárból a Striim-példány **lib** könyvtárába a következő parancsokkal:

   ```bash
   sudo su
   cd /tmp
   mv ojdbc8.jar /opt/striim/lib
   chmod +x ojdbc8.jar
   ```

   ![A jar-fájl áthelyezése a lib mappába](./media/cosmosdb-sql-api-migrate-data-striim/move-jar-file.png)


1. Ugyanabból a terminálból indítsa újra a Striim-kiszolgálót a következő parancsok végrehajtásával:

   ```bash
   Systemctl stop striim-node
   Systemctl stop striim-dbms
   Systemctl start striim-dbms
   Systemctl start striim-node
   ```
 
1. A Striim egy percet vesz igénybe. Ha meg szeretné tekinteni az állapotot, futtassa a következő parancsot: 

   ```bash
   tail -f /opt/striim/logs/striim-node.log
   ```

1. Most lépjen vissza az Azure-ba, és másolja a Striim virtuális gép nyilvános IP-címét. 

   ![Striim virtuális gép IP-címének másolása](./media/cosmosdb-sql-api-migrate-data-striim/copy-public-ip-address.png)

1. A Striim webes felhasználói felületének megkereséséhez nyisson meg egy új fület egy böngészőben, és másolja a nyilvános IP-címet, majd a következőt: 9080. Jelentkezzen be a **rendszergazdai** felhasználónévvel, valamint a Azure Portal megadott rendszergazdai jelszóval.

   ![Bejelentkezés a Striim](./media/cosmosdb-sql-api-migrate-data-striim/striim-login-ui.png)

1. Most megérkezik a Striim kezdőlapján. Három különböző ablaktábla van – **irányítópultok**, **alkalmazások**és **SourcePreview**. Az irányítópultok panelen valós időben helyezheti át az adatátvitelt, és megjelenítheti azt. Az alkalmazások ablaktábla tartalmazza a folyamatos átviteli adatfolyamatokat vagy az adatfolyamatokat. Az oldal jobb oldalán a SourcePreview, ahol megtekintheti az adatait a mozgatás előtt.

1. Válassza az **alkalmazások** panelt, és most erre a panelre fogunk összpontosítani. A Striim megtanulásához számos különböző minta alkalmazás használható, ebben a cikkben azonban a sajátját fogja létrehozni. Kattintson az **alkalmazás hozzáadása** gombra a jobb felső sarokban.

   ![A Striim alkalmazás hozzáadása](./media/cosmosdb-sql-api-migrate-data-striim/add-striim-app.png)

1. A Striim-alkalmazások létrehozása néhány különböző módon történik. Ebben a forgatókönyvben válassza a **teljesen új kezdés** lehetőséget.

   ![Az alkalmazás elindítása a semmiből](./media/cosmosdb-cassandra-api-migrate-data-striim/start-app-from-scratch.png)

1. Adjon egy rövid nevet az alkalmazásnak, például **oraToCosmosDB** , majd válassza a **Mentés**lehetőséget.

   ![Új alkalmazás létrehozása](./media/cosmosdb-cassandra-api-migrate-data-striim/create-new-application.png)

1. A flow Designerben fog megérkezni, ahol a Box-összekötők segítségével elhúzhatja a folyamatos átviteli alkalmazásokat. Írja be az **Oracle** kifejezést a keresősávba, húzza az **Oracle CDC** -forrást az alkalmazás-vászonra.  

   ![Oracle CDC-forrás](./media/cosmosdb-cassandra-api-migrate-data-striim/oracle-cdc-source.png)

1. Adja meg az Oracle-példány forrás-konfigurációs tulajdonságait. A forrás neve csak a Striim alkalmazás elnevezési konvenciója, például a **src_onPremOracle**nevet használhatja. További adatokat is megadhat, például az adapter típusát, a kapcsolat URL-címét, a felhasználónevet, a jelszót és a táblanév nevét. A folytatáshoz válassza a **Mentés** lehetőséget.

   ![Forrás paramétereinek konfigurálása](./media/cosmosdb-cassandra-api-migrate-data-striim/configure-source-parameters.png)

1. Most kattintson a stream Wave ikonjára a cél Azure Cosmos DB példányának összekapcsolásához. 

   ![Kapcsolódás a célhoz](./media/cosmosdb-cassandra-api-migrate-data-striim/connect-to-target.png)

1. Adja meg a cél Azure Cosmos DB példány konfigurációs tulajdonságait, és a folytatáshoz kattintson a **Mentés** gombra.

   ![Cél tulajdonságainak konfigurálása](./media/cosmosdb-cassandra-api-migrate-data-striim/configure-target-parameters1.png)

   ![Cél tulajdonságainak konfigurálása](./media/cosmosdb-cassandra-api-migrate-data-striim/configure-target-parameters2.png)

1. Most pedig folytassa a Striim alkalmazás futtatásával. A felső menüsorban válassza a **Létrehozva**lehetőséget, majd **telepítse az alkalmazást**. A központi telepítés ablakban megadhatja, hogy az alkalmazás bizonyos részeit az üzembe helyezési topológia adott részein kívánja-e futtatni. Mivel az Azure-on keresztül egyszerű üzembe helyezési topológián fut, az alapértelmezett beállítást fogjuk használni.

   ![Az alkalmazás üzembe helyezése](./media/cosmosdb-cassandra-api-migrate-data-striim/deploy-the-app.png)


1. Most pedig előre megtekintjük a streamet, hogy lássuk a Striim keresztül áramló adatforgalmat. Kattintson a Wave (hullám) ikonra, és kattintson a mellette lévő szem ikonra. A telepítés után megtekintheti a streamet, hogy láthassa az átáramló adatfolyamokat. Válassza ki a **Wave** ikont és a **szemgolyót** a mellette. Válassza az **üzembe helyezett** gombot a felső menüsorban, majd válassza az **alkalmazás indítása**lehetőséget.

   ![Az alkalmazás elindítása](./media/cosmosdb-cassandra-api-migrate-data-striim/start-the-app.png)

1. A **CDC (adatváltozások rögzítése)** olvasó használatával a Striim csak az adatbázis új módosításait fogja kiválasztani. Ha a forrástábla használatával áramlik át a folyamatokat, akkor azt fogja látni. Mivel azonban ez egy minta tábla, a forrás, amely nem kapcsolódik egyetlen alkalmazáshoz sem. Ha minta típusú adatgenerátort használ, beillesztheti az események láncát az Oracle-adatbázisba.

1. Ekkor a Striim platformon keresztül áramlik az adatforgalom. A Striim a táblához tartozó összes metaadatot is felveszi, ami hasznos lehet az adatok figyelésére, és gondoskodni róla, hogy az adatok a jobb oldalon legyenek kiképezve.

   ![A CDC-folyamat beállítása](./media/cosmosdb-cassandra-api-migrate-data-striim/setup-cdc-pipeline.png)

1. Végül jelentkezzen be az Azure-ba, és navigáljon az Azure Cosmos-fiókjához. Frissítse a Adatkezelőt, és láthatja, hogy az adatai megérkeztek. 

Az Azure-beli Striim megoldás használatával folyamatosan áttelepítheti az adatok Azure Cosmos DB különböző forrásokból, például Oracle, Cassandra, MongoDB és másoktól, hogy Azure Cosmos DB. Az áttelepítési útvonal Striim-vel való beállításakor felmerülő problémák esetén a [Striim webhelyén](https://go2.striim.com/request-support-striim)adja meg a támogatási kérelmet.


## <a name="next-steps"></a>További lépések

* Ha Azure Cosmos DB SQL API-ra végez áttelepítést, olvassa el az [adatáttelepítés Cassandra API-fiókba az Striim használatával](cosmosdb-sql-api-migrate-data-striim.md) című témakört.

* [Az adatok monitorozása és hibakeresése Azure Cosmos DB metrikákkal](use-metrics.md)