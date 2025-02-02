---
title: Biztonságos kísérletek és következtetések egy virtuális hálózaton
titleSuffix: Azure Machine Learning service
description: megtudhatja, hogyan védheti meg a kísérletezési/betanítási feladatokat, valamint az Azure-Virtual Network belüli Azure Machine Learning következtetési/pontozási feladatait.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 08/05/2019
ms.openlocfilehash: 1b5e3777109b13baa7d774a524664551798ba4ca
ms.sourcegitcommit: a6888fba33fc20cc6a850e436f8f1d300d03771f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69558200"
---
# <a name="secure-azure-ml-experimentation-and-inference-jobs-within-an-azure-virtual-network"></a>Egy Azure-Virtual Networkon belül biztonságossá teheti az Azure ML-kísérletezést és a feladatok következtetéseit

Ebből a cikkből megtudhatja, hogyan védheti meg az Azure-Virtual Network (vnet) Azure Machine Learning kísérletezési/betanítási feladatait és következtetéseit/pontozási feladatait. 

A **virtuális hálózat** biztonsági határként működik, és az Azure-erőforrásokat a nyilvános internetről különíti el. Egy Azure-beli virtuális hálózatot is csatlakoztathat a helyszíni hálózathoz. A hálózatok összekapcsolásával biztonságosan betaníthatja a modelleket, és elérheti az üzembe helyezett modelleket a következtetésekhez.

A Azure Machine Learning szolgáltatás más Azure-szolgáltatásokra támaszkodik számítási erőforrásokhoz. A számítási erőforrások vagy [számítási célok](concept-compute-target.md)a modellek betanítására és üzembe helyezésére szolgálnak. A célok létrehozhatók egy virtuális hálózaton belül. Használhatja például a Microsoft Data Science Virtual Machinet egy modell betanításához, majd a modell üzembe helyezéséhez az Azure Kubernetes Service (ak) szolgáltatásban. A virtuális hálózatokkal kapcsolatos további információkért lásd: az [Azure Virtual Network áttekintése](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

Ez a cikk részletes információkat is tartalmaz a *speciális biztonsági beállításokról*, az alapszintű és a kísérleti felhasználási esetekben nem szükséges információkról. A cikk bizonyos fejezetei számos különböző forgatókönyvhöz biztosítanak konfigurációs információkat. Nem kell megadnia az utasításokat sorrendben vagy teljes egészében.

## <a name="prerequisites"></a>Előfeltételek

+ Egy Azure Machine Learning szolgáltatás [](how-to-manage-workspace.md)munkaterülete. 

+ Az [Azure Virtual Network szolgáltatás](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) és az [IP-hálózatkezelés](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)általános munkaismerete. 

+ Meglévő virtuális hálózat és alhálózat a számítási erőforrásokkal való használathoz. 

## <a name="use-a-storage-account-for-your-workspace"></a>A munkaterülethez tartozó Storage-fiók használata

Ha Azure Storage-fiókot szeretne használni a virtuális hálózatban lévő munkaterülethez, tegye a következőket:

1. Hozzon létre egy kísérletezési számítási példányt (például egy Machine Learning Compute példányt) egy virtuális hálózat mögött, vagy csatoljon egy kísérletezési számítási példányt a munkaterülethez (például egy HDInsight-fürthöz vagy egy virtuális géphez). 

   További információ: "Machine Learning Compute-példány használata" és "virtuális gép vagy HDInsight-fürt használata" című rész ebben a cikkben.

1. A Azure Portal lépjen a munkaterülethez csatolt tárterületre. 

   ![A Azure Machine Learning szolgáltatás munkaterületéhez csatolt tárterület](./media/how-to-enable-virtual-network/workspace-storage.png)

1. Az **Azure Storage** lapon válassza a __tűzfalak és virtuális hálózatok__lehetőséget. 

   ![A Azure Portal Azure Storage lapjának "tűzfalak és virtuális hálózatok" területén](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)

1. A __tűzfalak és virtuális hálózatok__ lapon tegye a következőket:
    - Válassza a __Kiválasztott hálózatok__ lehetőséget.
    - A __virtuális hálózatok__területen válassza a __meglévő virtuális hálózati kapcsolat hozzáadása__ elemet. Ez a művelet hozzáadja azt a virtuális hálózatot, amelyben a kísérletezés számítási példánya található (lásd: 1. lépés).
    - Jelölje be a __megbízható Microsoft-szolgáltatások számára a Storage-fiók elérésének engedélyezése__ jelölőnégyzetet.

   ![A Azure Portal tűzfalak és virtuális hálózatok panelje](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png)

1. A kísérlet futtatásakor a kísérletezési kódban módosítsa a futtatási konfigurációt az Azure Blob Storage használatára:

    ```python
    run_config.source_directory_data_store = "workspaceblobstore"
    ```

> [!IMPORTANT]
> A Azure Machine Learning szolgáltatás _alapértelmezett Storage_ -fiókját a _kísérletezéshez_egy virtuális hálózaton helyezheti el.
>
> A virtuális hálózatokban _nem alapértelmezett tárolási fiókokat_ _csak kísérletezésre_helyezhet el.
>
> A _következtetéshez_ használt alapértelmezett és nem alapértelmezett tárolási fiókok esetében a _Storage-fiókhoz korlátlan hozzáférésre_van szükség.
>
> Ha nem tudja biztosan, hogy módosította-e a beállításokat, tekintse meg az [Azure Storage-tűzfalak és virtuális hálózatok konfigurálása](https://docs.microsoft.com/azure/storage/common/storage-network-security)című témakör "az alapértelmezett hálózati hozzáférési szabály módosítása" című szakaszát. Kövesse az utasításokat az összes hálózatról a következtetések során való hozzáférés engedélyezéséhez vagy a modell pontozásához.

## <a name="use-a-key-vault-instance-with-your-workspace"></a>Key Vault-példány használata a munkaterülettel

A munkaterülethez társított Key Vault-példányt a Azure Machine Learning szolgáltatás használja a következő hitelesítő adatok tárolásához:
* A társított Storage-fiókhoz tartozó kapcsolatok karakterlánca
* Az Azure Container repository példányainak jelszavai
* Adattárakhoz való kapcsolódási karakterláncok

Ha Azure Machine Learning kísérletezési képességeket szeretne használni a virtuális hálózat mögötti Azure Key Vault, tegye a következőket:
1. Nyissa meg a munkaterülethez társított kulcstartót. 

   ![A Azure Machine Learning szolgáltatás munkaterületéhez társított kulcstartó](./media/how-to-enable-virtual-network/workspace-key-vault.png)

1. A **Key Vault** oldalon, a bal oldali panelen válassza a __tűzfalak és virtuális hálózatok__lehetőséget. 

   ![A Key Vault panel "tűzfalak és virtuális hálózatok" szakasza](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks.png)

1. A __tűzfalak és virtuális hálózatok__ lapon tegye a következőket:
    - __A hozzáférés engedélyezése lehetőségnél__válassza a __kiválasztott hálózatok__elemet.
    - A __virtuális hálózatok__területen válassza a __meglévő virtuális hálózatok hozzáadása__ lehetőséget, és adja hozzá azt a virtuális hálózatot, amelyben a kísérletezés számítási példánya található.
    - A __megbízható Microsoft-szolgáltatások a tűzfal megkerülésének engedélyezése__területen válassza az __Igen__lehetőséget.

   ![A Key Vault panel "tűzfalak és virtuális hálózatok" szakasza](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png)

## <a name="use-a-machine-learning-compute-instance"></a>Machine Learning Compute-példány használata

Ha egy virtuális hálózaton Azure Machine Learning számítási példányt szeretne használni, vegye figyelembe a következő hálózati követelményeket:

- A virtuális hálózatnak ugyanabban az előfizetésben és régióban kell lennie, mint a Azure Machine Learning szolgáltatás munkaterületének.

- A számítási fürthöz megadott alhálózatnak elegendő nem hozzárendelt IP-címmel kell rendelkeznie a fürthöz rendelt virtuális gépek számának kielégítéséhez. Ha az alhálózat nem rendelkezik elegendő nem hozzárendelt IP-címmel, a fürt részlegesen le lesz foglalva.

- Ha a virtuális hálózat védelmét úgy tervezi, hogy korlátozza a forgalmat, hagyjon meg néhány portot a számítási szolgáltatás számára. További információ: a [szükséges portok](#mlcports) szakasz.

- Ellenőrizze, hogy a virtuális hálózat előfizetése vagy erőforráscsoport biztonsági szabályzatai vagy zárolásai korlátozzák-e az engedélyeket a virtuális hálózat kezeléséhez.

- Ha egy virtuális hálózatban több számítási fürtöt fog elhelyezni, előfordulhat, hogy egy vagy több erőforrásra vonatkozó kvóta-növelést kell kérnie.

    A Machine Learning Compute-példány automatikusan további hálózati erőforrásokat foglal le a virtuális hálózatot tartalmazó erőforráscsoporthoz. A szolgáltatás minden számítási fürthöz a következő erőforrásokat foglalja le:

    - Egy hálózati biztonsági csoport

    - One public IP address

    - Egy Load Balancer

  Ezekre az erőforrásokra az előfizetésben meghatározott [erőforráskvóták](https://docs.microsoft.com/azure/azure-subscription-service-limits) vonatkoznak.

### <a id="mlcports"></a>Szükséges portok

Machine Learning Compute jelenleg a Azure Batch szolgáltatás használatával helyezi üzembe a virtuális gépeket a megadott virtuális hálózaton. Az alhálózatnak engedélyeznie kell a bejövő kommunikációt a Batch szolgáltatástól. Ezzel a kommunikációval ütemezhet a Machine Learning Compute-csomópontokon futó futtatásokat, és kommunikálhat az Azure Storage szolgáltatással és más erőforrásokkal. A Batch szolgáltatás hálózati biztonsági csoportokat (NSG) helyez üzembe a virtuális gépekhez csatolt hálózati adapterek (NIC-EK) szintjén. Ezek az NSG-k automatikusan konfigurálnak bejövő és kimenő szabályokat a következő forgalom engedélyezéséhez:

- Bejövő TCP-forgalom a 29876-es és a 29877-es portokon a __BatchNodeManagement__ __szolgáltatási címkéjén__ .

    ![A BatchNodeManagement szolgáltatás címkéjét használó bejövő szabály](./media/how-to-enable-virtual-network/batchnodemanagement-service-tag.png)

- Választható Bejövő TCP-forgalom a 22-es porton a távoli hozzáférés engedélyezéséhez. Csak akkor használja ezt a portot, ha az SSH használatával szeretne csatlakozni a nyilvános IP-címen.

- Kimenő forgalom bármilyen porton keresztül a virtuális hálózathoz.

- Kimenő forgalom bármilyen porton keresztül az internetre.

Körültekintően járjon el a bejövő vagy kimenő szabályok módosításakor és hozzáadásakor a Batch által konfigurált NSG-kben. Ha egy NSG blokkolja a számítási csomópontok felé irányuló kommunikációt, a számítási szolgáltatás nem használhatóra állítja a számítási csomópontok állapotát.

Nem kell megadnia a NSG az alhálózat szintjén, mert a Azure Batch szolgáltatás konfigurálja a saját NSG. Ha azonban a megadott alhálózat társított NSG vagy tűzfallal rendelkezik, a korábban említettek szerint konfigurálja a bejövő és kimenő biztonsági szabályokat.

A Azure Portal NSG-szabályának konfigurációja az alábbi képeken látható:

![A Machine Learning Computehoz tartozó bejövő NSG-szabályok](./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png)

![A Machine Learning Compute kimenő NSG szabályai](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a id="limiting-outbound-from-vnet"></a>A virtuális hálózat kimenő kapcsolatának korlátozása

Ha nem szeretné az alapértelmezett kimenő szabályokat használni, és korlátozni szeretné a virtuális hálózat kimenő hozzáférését, tegye a következőket:

- A kimenő internetkapcsolat megtagadása a NSG szabályok használatával.

- A kimenő forgalom korlátozása a következőre:
   - Azure Storage, a __Storage. Region_Name__ (például Storage. EastUS) __szolgáltatási címkéjével__
   - Azure Container Registry a __AzureContainerRegistry. Region_Name__ __szolgáltatási címkéjének__ használatával (például AzureContainerRegistry. EastUS)
   - Azure Machine Learning szolgáltatás a __AzureMachineLearning__ __szolgáltatási címkéjének__ használatával

A Azure Portal NSG-szabályának konfigurációja a következő képen látható:

![A Machine Learning Compute kimenő NSG szabályai](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png)

### <a name="user-defined-routes-for-forced-tunneling"></a>A kényszerített bújtatáshoz tartozó felhasználó által megadott útvonalak

Ha Machine Learning Compute használatával kényszerített bújtatást használ, adja hozzá a [felhasználó által megadott útvonalakat (UDR)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) a számítási erőforrást tartalmazó alhálózathoz.

* Hozzon létre egy UDR minden olyan IP-címhez, amelyet a Azure Batch szolgáltatás használ azon a régióban, ahol az erőforrásai léteznek. Ezek a UDR lehetővé teszik a Batch szolgáltatás számára a feladatütemezés számítási csomópontjaival való kommunikációját. A Batch szolgáltatás IP-címeinek listájának lekéréséhez forduljon az Azure ügyfélszolgálatához.

* A helyszíni hálózati berendezés nem tilthatja le az Azure Storage-ba irányuló kimenő forgalmat. Pontosabban az URL-címek a és `<account>.table.core.windows.net` `<account>.blob.core.windows.net`a `<account>.queue.core.windows.net`formátumúak.

A UDR hozzáadásakor adja meg az útvonalat az egyes kapcsolódó batch IP-címek előtagjaként, és állítsa be a __következő ugrás típusát__ az __Internet__értékre. Az alábbi képen látható példa erre a UDR mutat a Azure Portalban:

![UDR – példa a címek előtagjaként való használatra](./media/how-to-enable-virtual-network/user-defined-route.png)

További információ: [Azure batch készlet létrehozása egy virtuális hálózaton](../../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling).

### <a name="create-a-machine-learning-compute-cluster-in-a-virtual-network"></a>Machine Learning Compute-fürt létrehozása virtuális hálózaton

Machine Learning Compute-fürt létrehozásához tegye a következőket:

1. A [Azure Portal](https://portal.azure.com)válassza ki a Azure Machine learning szolgáltatás munkaterületét.

1. Az __alkalmazás__ szakaszban válassza a __számítás__lehetőséget, majd kattintson a __számítás hozzáadása__lehetőségre.

1. Ha ezt a számítási erőforrást virtuális hálózat használatára szeretné konfigurálni, tegye a következőket:

    a. A __hálózati konfiguráció beállításnál__válassza a __speciális__lehetőséget.

    b. Az __erőforráscsoport__ legördülő listában válassza ki a virtuális hálózatot tartalmazó erőforráscsoportot.

    c. A __virtuális hálózat__ legördülő listában válassza ki azt a virtuális hálózatot, amely az alhálózatot tartalmazza.

    d. Az __alhálózat__ legördülő listában válassza ki a használni kívánt alhálózatot.

   ![Machine Learning Compute virtuális hálózati beállításai](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

Machine Learning Compute-fürtöt a Azure Machine Learning SDK használatával is létrehozhat. A következő kód egy új Machine learning Compute fürtöt `default` hoz létre egy nevű `mynetwork`virtuális hálózat alhálózatában:

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# The Azure virtual network name, subnet, and resource group
vnet_name = 'mynetwork'
subnet_name = 'default'
vnet_resourcegroup_name = 'mygroup'

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print("Found existing cpucluster")
except ComputeTargetException:
    print("Creating new cpucluster")

    # Specify the configuration for the new cluster
    compute_config = AmlCompute.provisioning_configuration(vm_size="STANDARD_D2_V2",
                                                           min_nodes=0,
                                                           max_nodes=4,
                                                           vnet_resourcegroup_name=vnet_resourcegroup_name,
                                                           vnet_name=vnet_name,
                                                           subnet_name=subnet_name)

    # Create the cluster with the specified name and configuration
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

    # Wait for the cluster to be completed, show the output log
    cpu_cluster.wait_for_completion(show_output=True)
```

A létrehozási folyamat befejeződése után a modellt egy kísérletben a fürt használatával kell betanítani. További információkért lásd: [számítási cél kiválasztása és használata képzéshez](how-to-set-up-training-targets.md).

## <a name="use-a-virtual-machine-or-hdinsight-cluster"></a>Virtuális gép vagy HDInsight-fürt használata

> [!IMPORTANT]
> A Azure Machine Learning szolgáltatás csak az Ubuntut futtató virtuális gépeket támogatja.

Ha egy virtuális gépet vagy Azure HDInsight-fürtöt szeretne használni a munkaterülettel rendelkező virtuális hálózatban, tegye a következőket:

1. Hozzon létre egy virtuális GÉPET vagy HDInsight-fürtöt a Azure Portal vagy az Azure CLI használatával, és helyezze üzembe a fürtöt egy Azure-beli virtuális hálózaton. További információkért tekintse át a következő cikkeket:
    * [Azure Virtual Networks létrehozása és kezelése Linux rendszerű virtuális gépekhez](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [HDInsight kiterjesztése Azure Virtual Network használatával](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network)

1. Ha engedélyezni szeretné, hogy a Azure Machine Learning szolgáltatás kommunikáljon a virtuális gép vagy fürt SSH-portjával, konfigurálja a hálózati biztonsági csoporthoz tartozó forrásoldali bejegyzést. Az SSH-port általában a 22-es port. A forrásból érkező forgalom engedélyezéséhez tegye a következőket:

    * A __forrás__ legördülő listában válassza a __szolgáltatás címkéje__elemet.

    * A __forrás szolgáltatás címkéje__ legördülő listában válassza a __AzureMachineLearning__lehetőséget.

    * A __forrás porttartomány-tartományok__ legördülő listában válassza a elemet __*__ .

    * A __cél__ legördülő listában válassza a __bármelyik__lehetőséget.

    * A __cél porttartomány-tartományok__ legördülő listában válassza a __22__lehetőséget.

    * A __protokoll__területen válassza __a bármelyik__lehetőséget.

    * A __művelet__területen válassza az __Engedélyezés__lehetőséget.

   ![Bejövő szabályok a virtuális hálózaton belüli virtuálisgép-vagy HDInsight-fürtök kísérletezéséhez](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

    Tartsa meg a hálózati biztonsági csoport alapértelmezett kimenő szabályait. További információ: [biztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules)alapértelmezett biztonsági szabályai.

    Ha nem szeretné használni az alapértelmezett kimenő szabályokat, és szeretné korlátozni a virtuális hálózat kimenő hozzáférését, tekintse meg a [Kimenő kapcsolat korlátozása a virtuális hálózattal](#limiting-outbound-from-vnet) szakaszt.

1. Csatlakoztassa a virtuális gépet vagy a HDInsight-fürtöt a Azure Machine Learning szolgáltatás munkaterületéhez. További információ: [számítási célok beállítása a modell](how-to-set-up-training-targets.md)betanításához.

## <a name="use-azure-kubernetes-service-aks"></a>Az Azure Kubernetes Service (ak) használata

Ha AK-t szeretne hozzáadni egy virtuális hálózathoz a munkaterülethez, tegye a következőket:

> [!IMPORTANT]
> Az alábbi eljárás megkezdése előtt tekintse át az előfeltételeket, és tervezze meg az IP-címzést a fürt számára. További információ: [speciális hálózatkezelés konfigurálása az Azure Kubernetes szolgáltatásban (ak)](https://docs.microsoft.com/azure/aks/configure-advanced-networking).
>
> Az AK-példánynak és az Azure-beli virtuális hálózatnak ugyanabban a régióban kell lennie.

1. A [Azure Portal](https://portal.azure.com)ellenőrizze, hogy a virtuális hálózatot vezérlő NSG van-e olyan Bejövő szabály, amely engedélyezve van a Azure Machine learning szolgáltatás számára, ha a __AzureMachineLearning__ -thasználja forrásként.

    ![A Azure Machine Learning szolgáltatás számítási paneljének hozzáadása](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-aml.png)

1. Válassza ki a Azure Machine Learning szolgáltatás munkaterületét.

1. Az __alkalmazás__ szakaszban válassza a __számítás__lehetőséget, majd kattintson a __számítás hozzáadása__lehetőségre.

1. Ha ezt a számítási erőforrást virtuális hálózat használatára szeretné konfigurálni, tegye a következőket:

    - A __hálózati konfiguráció beállításnál__válassza a __speciális__lehetőséget.

    - Az __erőforráscsoport__ legördülő listában válassza ki a virtuális hálózatot tartalmazó erőforráscsoportot.

    - A __virtuális hálózat__ legördülő listában válassza ki azt a virtuális hálózatot, amely az alhálózatot tartalmazza.

    - Az __alhálózat__ legördülő listában válassza ki az alhálózatot.

    - A __Kubernetes szolgáltatás címtartomány__ mezőjébe írja be a Kubernetes szolgáltatás címtartományt. Ez a címtartomány egy osztály nélküli tartományok közötti útválasztás (CIDR) jelölésű IP-címtartományt használ a fürt számára elérhető IP-címek definiálásához. Nem lehet átfedésben egyetlen alhálózati IP-tartománnyal sem (például 10.0.0.0/16).

    - A __KUBERNETES DNS-szolgáltatás IP-címe__ mezőbe írja be a Kubernetes DNS-szolgáltatás IP-címét. Ezt az IP-címet a Kubernetes DNS szolgáltatáshoz rendeli a rendszer. A Kubernetes a szolgáltatási címtartomány (például 10.0.0.10) közé kell esnie.

    - A __Docker Bridge-címe__ mezőbe írja be a Docker-híd címe mezőt. Ezt az IP-címet a Docker-híd rendeli hozzá. Nem lehet alhálózati IP-címtartományok vagy a Kubernetes szolgáltatási címtartomány (például 172.17.0.1/16).

   ![Azure Machine Learning szolgáltatás: Virtuális hálózati beállítások Machine Learning Compute](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

1. Győződjön meg arról, hogy a virtuális hálózatot vezérlő NSG-csoport rendelkezik egy bejövő biztonsági szabállyal, amely engedélyezi a pontozási végpontot, hogy a virtuális hálózaton kívülről is meghívható legyen.
   > [!IMPORTANT]
   > Tartsa meg a NSG alapértelmezett kimenő szabályait. További információ: [biztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules)alapértelmezett biztonsági szabályai.
  
   ![Egy bejövő biztonsági szabály](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)

A Azure Machine Learning SDK-val is hozzáadhatja az Azure Kubernetes szolgáltatást egy virtuális hálózatban. Ha már van egy AK-fürtje egy virtuális hálózaton, csatolja azt a munkaterülethez a következő témakörben leírtak szerint: [üzembe helyezés az AK](how-to-deploy-to-aks.md)-ban. A következő kód létrehoz egy új AK- `default` példányt egy nevű `mynetwork`virtuális hálózat alhálózatában:

```python
from azureml.core.compute import ComputeTarget, AksCompute

# Create the compute configuration and set virtual network information
config = AksCompute.provisioning_configuration(location="eastus2")
config.vnet_resourcegroup_name = "mygroup"
config.vnet_name = "mynetwork"
config.subnet_name = "default"
config.service_cidr = "10.0.0.0/16"
config.dns_service_ip = "10.0.0.10"
config.docker_bridge_cidr = "172.17.0.1/16"

# Create the compute target
aks_target = ComputeTarget.create(workspace=ws,
                                  name="myaks",
                                  provisioning_configuration=config)
```

A létrehozási folyamat befejezésekor futtathat következtetéseket vagy modell-pontozást egy virtuális hálózat mögötti AK-fürtön. További információ: [üzembe helyezés az AK](how-to-deploy-to-aks.md)-ban.

## <a name="next-steps"></a>További lépések

* [Képzési környezetek beállítása](how-to-set-up-training-targets.md)
* [Az üzembe helyezés modellek](how-to-deploy-and-where.md)
* [Modellek biztonságos üzembe helyezése SSL használatával](how-to-secure-web-service.md)

