---
title: Virtuális hálózat megtervezése az Azure HDInsight
description: Ismerje meg, hogyan tervezhet Azure Virtual Network üzemelő példányt a HDInsight más felhőalapú erőforrásokhoz vagy az adatközpontban található erőforrásokhoz való összekapcsolásához.
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: 135855ee33f783e85b398c7f9716c2c897633de9
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779544"
---
# <a name="plan-a-virtual-network-for-azure-hdinsight"></a>Virtuális hálózat megtervezése az Azure HDInsight

Ez a cikk háttér-információkat nyújt az Azure-beli [virtuális hálózatok](../virtual-network/virtual-networks-overview.md) Azure HDInsight való használatáról. Emellett ismerteti a tervezési és megvalósítási döntéseket is, amelyeket el kell végezni ahhoz, hogy a HDInsight-fürthöz virtuális hálózatot lehessen megvalósítani. Ha a tervezési fázis elkészült, akkor folytathatja a [virtuális hálózatok létrehozását az Azure HDInsight](hdinsight-create-virtual-network.md)-fürtökhöz. A hálózati biztonsági csoportok és a felhasználó által definiált útvonalak megfelelő konfigurálásához szükséges HDInsight-felügyeleti IP-címekkel kapcsolatos további információkért lásd: [HDInsight-felügyeleti IP-címek](hdinsight-management-ip-addresses.md).

Az Azure Virtual Network a következő forgatókönyvek használatát teszi lehetővé:

* Csatlakozás a HDInsight közvetlenül egy helyszíni hálózatról.
* HDInsight csatlakoztatása az adattárakhoz egy Azure-beli virtuális hálózaton.
* Közvetlen hozzáférés az interneten keresztül nyilvánosan nem elérhető [Apache Hadoop](https://hadoop.apache.org/) -szolgáltatásokhoz. Például [Apache Kafka](https://kafka.apache.org/) API-kat vagy az [Apache HBase](https://hbase.apache.org/) Java API-t.

> [!IMPORTANT]
> A HDInsight-fürt VNET való létrehozása számos hálózati erőforrást hoz létre, például hálózati adaptereket és terheléselosztókat. Ne törölje ezeket a hálózati erőforrásokat, mivel azok szükségesek ahhoz, hogy a fürt megfelelően működjön a VNET.
>
> A 2019. február 28. után a VNET létrehozott új HDInsight-fürtök hálózati erőforrásai (például hálózati adapterek, LBs-EK stb.) ugyanabba a HDInsight-fürterőforrás-csoportba lesznek kiépítve. Korábban ezeket az erőforrásokat kiosztották a VNET erőforráscsoporthoz. Az aktuálisan futó fürtök és a VNET nélkül létrehozott fürtök nem változnak.

## <a name="planning"></a>Tervezés

A HDInsight virtuális hálózatban való telepítésének tervezésekor a következő kérdéseket kell megválaszolnia:

* Telepítenie kell a HDInsight-t egy meglévő virtuális hálózatra? Vagy létrehoz egy új hálózatot?

    Ha meglévő virtuális hálózatot használ, előfordulhat, hogy a HDInsight telepítése előtt módosítania kell a hálózati konfigurációt. További információ: [HDInsight hozzáadása meglévő virtuális hálózathoz](#existingvnet) szakasz.

* Szeretné összekapcsolni a HDInsight tartalmazó virtuális hálózatot egy másik virtuális hálózattal vagy a helyszíni hálózattal?

    A hálózatokon keresztüli erőforrások egyszerű működéséhez előfordulhat, hogy létre kell hoznia egy egyéni DNS-t, és konfigurálnia kell a DNS-továbbítást. További információkért lásd a [több hálózat csatlakoztatása](#multinet) szakaszt.

* Szeretné korlátozni/átirányítani a bejövő vagy kimenő forgalmat a HDInsight?

    A HDInsight korlátozás nélküli kommunikációt kell biztosítania adott IP-címekkel az Azure-adatközpontban. Több portot is engedélyezni kell a tűzfalakon keresztül az ügyfél-kommunikációhoz. További információ: a [hálózati forgalom szabályozása](#networktraffic) szakasz.

## <a id="existingvnet"></a>HDInsight hozzáadása meglévő virtuális hálózathoz

Az ebben a szakaszban ismertetett lépések segítségével megtudhatja, hogyan adhat hozzá új HDInsight egy meglévő Azure-Virtual Networkhoz.

> [!NOTE]  
> Meglévő HDInsight-fürtöt nem adhat hozzá virtuális hálózathoz.

1. Klasszikus vagy Resource Manager-alapú üzemi modellt használ a virtuális hálózathoz?

    A HDInsight 3,4 és újabb rendszerekhez Resource Manager virtuális hálózat szükséges. A HDInsight korábbi verzióihoz klasszikus virtuális hálózat szükséges.

    Ha a meglévő hálózat egy klasszikus virtuális hálózat, akkor létre kell hoznia egy Resource Manager-alapú virtuális hálózatot, majd össze kell kapcsolni a kettőt. [Klasszikus virtuális hálózatok csatlakoztatása új virtuális hálózatok](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

    A csatlakozást követően a Resource Manager-hálózatban telepített HDInsight a klasszikus hálózaton lévő erőforrásokkal is kezelhetik.

2. Hálózati biztonsági csoportokat, felhasználó által megadott útvonalakat vagy Virtual Network készülékeket használ a virtuális hálózatra vagy onnan érkező forgalom korlátozására?

    Felügyelt szolgáltatásként a HDInsight korlátozás nélküli hozzáférést igényel az Azure-adatközpont több IP-címéhez. Ezen IP-címekkel való kommunikáció engedélyezéséhez frissítse a meglévő hálózati biztonsági csoportokat vagy a felhasználó által megadott útvonalakat.
    
    A HDInsight több szolgáltatást üzemeltet, amelyek számos portot használnak. Ne blokkolja ezen portok forgalmát. A virtuális berendezési tűzfalakon keresztül engedélyezhető portok listáját a biztonsági szakaszban találja.
    
    A meglévő biztonsági beállítások megkereséséhez használja a következő Azure PowerShell vagy Azure CLI-parancsokat:

    * Network security groups (Hálózati biztonsági csoportok)

        Cserélje `RESOURCEGROUP` le a helyére a virtuális hálózatot tartalmazó erőforráscsoport nevét, majd írja be a parancsot:
    
        ```powershell
        Get-AzNetworkSecurityGroup -ResourceGroupName  "RESOURCEGROUP"
        ```
    
        ```azurecli
        az network nsg list --resource-group RESOURCEGROUP
        ```

        További információ: [hálózati biztonsági csoportok hibakeresése](../virtual-network/diagnose-network-traffic-filter-problem.md) dokumentum.

        > [!IMPORTANT]  
        > A hálózati biztonsági csoport szabályait a rendszer a szabály prioritása alapján alkalmazza. A forgalmi mintának megfelelő első szabály lesz alkalmazva, és a rendszer nem alkalmazza másokat erre a forgalomra. A legtöbb engedékenység és a legkevésbé megengedő szabályok sorrendje. További információ: [hálózati forgalom szűrése hálózati biztonsági csoportokkal](../virtual-network/security-overview.md) dokumentum.

    * Felhasználó által megadott útvonalak

        Cserélje `RESOURCEGROUP` le a helyére a virtuális hálózatot tartalmazó erőforráscsoport nevét, majd írja be a parancsot:

        ```powershell
        Get-AzRouteTable -ResourceGroupName "RESOURCEGROUP"
        ```

        ```azurecli
        az network route-table list --resource-group RESOURCEGROUP
        ```

        További információ: [útvonalakkal kapcsolatos hibák megoldása](../virtual-network/diagnose-network-routing-problem.md) .

3. Hozzon létre egy HDInsight-fürtöt, és válassza ki az Azure Virtual Network a konfigurálás során. A fürt létrehozási folyamatának megismeréséhez kövesse az alábbi dokumentumokat:

    * [HDInsight létrehozása az Azure Portalon](hdinsight-hadoop-create-linux-clusters-portal.md)
    * [HDInsight létrehozása az Azure PowerShell-lel](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
    * [HDInsight létrehozása a klasszikus Azure CLI-vel](hdinsight-hadoop-create-linux-clusters-azure-cli.md)
    * [HDInsight létrehozása Azure Resource Manager sablon használatával](hdinsight-hadoop-create-linux-clusters-arm-templates.md)

   > [!IMPORTANT]  
   > A HDInsight virtuális hálózathoz való hozzáadása egy opcionális konfigurációs lépés. Ügyeljen arra, hogy a fürt konfigurálásakor válassza ki a virtuális hálózatot.

## <a id="multinet"></a>Több hálózat összekapcsolása

A több hálózati konfigurációval rendelkező legnagyobb kihívás a hálózatok közötti névfeloldás.

Az Azure a virtuális hálózatban telepített Azure-szolgáltatások névfeloldását teszi lehetővé. Ez a beépített névfeloldás lehetővé teszi a HDInsight számára a következő erőforrásokhoz való kapcsolódást teljes tartománynév (FQDN) használatával:

* Bármely, az interneten elérhető erőforrás. Például: microsoft.com, windowsupdate.com.

* Minden olyan erőforrás, amely ugyanabban az Azure-Virtual Networkban található, az erőforrás __belső DNS-nevének__ használatával. Ha például az alapértelmezett névfeloldást használja, a következő példák a HDInsight-feldolgozó csomópontokhoz rendelt belső DNS-nevekre:

  * wn0-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net
  * wn2-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net

    Mindkét csomópont közvetlenül tud kommunikálni egymással és a HDInsight egyéb csomópontjaival belső DNS-nevek használatával.

Az alapértelmezett névfeloldás __nem__ teszi lehetővé a HDInsight számára a virtuális hálózathoz csatlakoztatott hálózatokban lévő erőforrások nevének feloldását. Például gyakori a helyszíni hálózat csatlakoztatása a virtuális hálózathoz. Csak az alapértelmezett névfeloldással a HDInsight nem fér hozzá a helyszíni hálózat erőforrásaihoz név szerint. Ellenkező esetben is igaz, a helyszíni hálózaton lévő erőforrások név szerint nem férhetnek hozzá a virtuális hálózat erőforrásaihoz.

> [!WARNING]  
> A HDInsight-fürt létrehozása előtt létre kell hoznia az egyéni DNS-kiszolgálót, és konfigurálnia kell a virtuális hálózatot.

Ha engedélyezni szeretné a névfeloldást a virtuális hálózat és az összekapcsolt hálózatokban lévő erőforrások között, a következő műveleteket kell végrehajtania:

1. Hozzon létre egy egyéni DNS-kiszolgálót az Azure Virtual Network, ahol a HDInsight telepítését tervezi.

2. Konfigurálja a virtuális hálózatot az egyéni DNS-kiszolgáló használatára.

3. Keresse meg az Azure-beli hozzárendelt DNS-utótagot a virtuális hálózathoz. Ez az érték hasonló a `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net`következőhöz:. A DNS-utótag megkeresésével kapcsolatos információkért tekintse meg a [következő példát: Egyéni DNS](hdinsight-create-virtual-network.md#example-dns) szakasz.

4. Konfigurálja a DNS-kiszolgálók közötti továbbítást. A konfiguráció a távoli hálózat típusától függ.

   * Ha a távoli hálózat egy helyszíni hálózat, a DNS-t a következőképpen konfigurálja:
        
     * __Egyéni DNS__ (a virtuális hálózatban):

         * Továbbítson kérelmeket a virtuális hálózat DNS-utótagjának az Azure rekurzív feloldó (168.63.129.16) számára. Az Azure kezeli a virtuális hálózatban lévő erőforrásokra vonatkozó kéréseket

         * Továbbítsa az összes többi kérést a helyszíni DNS-kiszolgálónak. A helyszíni DNS minden más névfeloldási kérelmet kezel, még az internetes erőforrásokra, például a Microsoft.com-re vonatkozó kéréseket is.

     * Helyszíni __DNS__: Továbbítsa a virtuális hálózat DNS-utótagjának kéréseit az egyéni DNS-kiszolgálónak. Az egyéni DNS-kiszolgáló ezután továbbítja az Azure rekurzív feloldót.

       Ez a konfiguráció a virtuális hálózat DNS-utótagját tartalmazó teljes tartománynevek kérelmeit az egyéni DNS-kiszolgálóra irányítja. Az összes többi kérést (még a nyilvános internetes címek esetében is) a helyszíni DNS-kiszolgáló kezeli.

   * Ha a távoli hálózat egy másik Azure-Virtual Network, konfigurálja a DNS-t a következőképpen:

     * __Egyéni DNS__ (minden virtuális hálózatban):

         * A rendszer a virtuális hálózatok DNS-utótagjának kérelmeit továbbítja az egyéni DNS-kiszolgálókra. Az egyes virtuális hálózatokban lévő DNS feladata a hálózatban lévő erőforrások feloldása.

         * Továbbítsa az összes többi kérést az Azure rekurzív feloldónak. A rekurzív feloldó feladata a helyi és internetes erőforrások feloldása.

       Az egyes hálózatokhoz tartozó DNS-kiszolgáló továbbítja a kéréseket a másiknak a DNS-utótag alapján. Más kérelmek feloldása az Azure rekurzív feloldó használatával történik.

     Az egyes konfigurációkra példaként tekintse meg [a következő példát: Egyéni DNS](hdinsight-create-virtual-network.md#example-dns) szakasz.

További információ: a [virtuális gépek és a szerepkör példányainak névfeloldása](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) .

## <a name="directly-connect-to-apache-hadoop-services"></a>Közvetlen kapcsolódás Apache Hadoop szolgáltatásokhoz

A fürthöz a következő helyen csatlakozhat `https://CLUSTERNAME.azurehdinsight.net`:. Ez a cím egy nyilvános IP-címet használ, amely nem érhető el, ha a NSG használatával korlátozza a bejövő forgalmat az internetről. Emellett, ha a fürtöt egy VNet helyezi üzembe, a magánhálózati végponton `https://CLUSTERNAME-int.azurehdinsight.net`keresztül is elérheti azt. Ez a végpont egy magánhálózati IP-címhez lett feloldva a VNet a fürt eléréséhez.

Az Apache Ambari és más weboldalakhoz a virtuális hálózaton keresztül történő kapcsolódáshoz kövesse az alábbi lépéseket:

1. A HDInsight-fürtcsomópontok belső teljes tartománynevének (FQDN) felderítéséhez használja az alábbi módszerek egyikét:

    Cserélje `RESOURCEGROUP` le a helyére a virtuális hálózatot tartalmazó erőforráscsoport nevét, majd írja be a parancsot:

    ```powershell
    $clusterNICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP" | where-object {$_.Name -like "*node*"}

    $nodes = @()
    foreach($nic in $clusterNICs) {
        $node = new-object System.Object
        $node | add-member -MemberType NoteProperty -name "Type" -value $nic.Name.Split('-')[1]
        $node | add-member -MemberType NoteProperty -name "InternalIP" -value $nic.IpConfigurations.PrivateIpAddress
        $node | add-member -MemberType NoteProperty -name "InternalFQDN" -value $nic.DnsSettings.InternalFqdn
        $nodes += $node
    }
    $nodes | sort-object Type
    ```

    ```azurecli
    az network nic list --resource-group RESOURCEGROUP --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
    ```

    A visszaadott csomópontok listájában keresse meg a fő csomópontok teljes tartománynevét, és használja a teljes tartománynevet a Ambari és más webszolgáltatásokhoz való kapcsolódáshoz. Például a használatával `http://<headnode-fqdn>:8080` érheti el a Ambari.

    > [!IMPORTANT]  
    > A fő csomópontokon futó egyes szolgáltatások egyszerre csak egy csomóponton aktívak. Ha egy fő csomóponton próbál hozzáférni egy szolgáltatáshoz, és 404-es hibát ad vissza, váltson át a másik fő csomópontra.

2. A szolgáltatás által elérhető csomópontok és portok meghatározásához tekintse meg a [Hadoop Services által a HDInsight dokumentumban használt portokat](./hdinsight-hadoop-port-settings-for-services.md) .

## <a id="networktraffic"></a>Hálózati forgalom szabályozása

### <a name="techniques-for-controlling-inbound-and-outbound-traffic-to-hdinsight-clusters"></a>Technikák a HDInsight-fürtök bejövő és kimenő forgalmának szabályozására

Az Azure-beli virtuális hálózatok hálózati forgalmát a következő módszerekkel lehet vezérelni:

* **Hálózati biztonsági csoportok** (NSG) lehetővé teszi a bejövő és a kimenő forgalom szűrését a hálózatra. További információ: [hálózati forgalom szűrése hálózati biztonsági csoportokkal](../virtual-network/security-overview.md) dokumentum.

* **Hálózati virtuális berendezések** A (NVA) csak kimenő adatforgalom esetén használható. A NVA replikálja az eszközök, például a tűzfalak és az útválasztók működését. További információ: [hálózati berendezések](https://azure.microsoft.com/solutions/network-appliances) dokumentum.

Felügyelt szolgáltatásként a HDInsight a HDInsight állapot-és felügyeleti szolgáltatásokhoz való korlátlan hozzáférést igényel mind a VNET érkező bejövő, mind kimenő forgalmához. A NSG használatakor biztosítania kell, hogy ezek a szolgáltatások továbbra is kommunikálhatnak a HDInsight-fürttel.

![Az Azure egyéni VNET létrehozott HDInsight-entitások ábrája](./media/hdinsight-virtual-network-architecture/vnet-diagram.png)

### <a name="hdinsight-with-network-security-groups"></a>HDInsight hálózati biztonsági csoportokkal

Ha **hálózati biztonsági csoportokat** kíván használni a hálózati forgalom szabályozásához, hajtsa végre a következő műveleteket a HDInsight telepítése előtt:

1. Azonosítsa az HDInsight használni kívánt Azure-régiót.

2. Azonosítsa a HDInsight által igényelt IP-címeket. További információ: [HDInsight-felügyeleti IP-címek](hdinsight-management-ip-addresses.md).

3. Hozza létre vagy módosítsa annak az alhálózatnak a hálózati biztonsági csoportjait, amelyre telepíteni kívánja a HDInsight-et.

    * __Hálózati biztonsági csoportok__: engedélyezze a __bejövő__ forgalmat az __443__ -as porton az IP-címekről. Ezzel biztosíthatja, hogy a HDInsight-kezelési szolgáltatások a virtuális hálózaton kívülről is elérjék a fürtöt.

A hálózati biztonsági csoportokkal kapcsolatos további információkért tekintse meg a [hálózati biztonsági csoportok áttekintése](../virtual-network/security-overview.md)című témakört.

### <a name="controlling-outbound-traffic-from-hdinsight-clusters"></a>HDInsight-fürtök kimenő forgalmának szabályozása

További információ a HDInsight-fürtök kimenő forgalmának szabályozásáról: a [kimenő hálózati forgalom korlátozásának konfigurálása az Azure HDInsight](hdinsight-restrict-outbound-traffic.md)-fürtökhöz.

#### <a name="forced-tunneling-to-on-premise"></a>Kényszerített bújtatás helyszínre

A kényszerített bújtatás egy felhasználó által megadott útválasztási konfiguráció, amelyben az alhálózat összes forgalma egy adott hálózatra vagy helyre, például a helyszíni hálózatra van kényszerítve. A HDInsight __nem__ támogatja a helyszíni hálózatokra irányuló forgalom kényszerített bújtatását. 

## <a id="hdinsight-ip"></a>Szükséges IP-címek

Ha hálózati biztonsági csoportokat vagy felhasználó által megadott útvonalakat használ a forgalom vezérléséhez, tekintse meg a [HDInsight-felügyeleti IP-címek](hdinsight-management-ip-addresses.md)című témakört.
    
## <a id="hdinsight-ports"></a>Szükséges portok

Ha tűzfalat szeretne használni, és bizonyos portokon kívülről fér hozzá a fürthöz, lehetséges, hogy engedélyeznie kell a forgalmat az adott forgatókönyvhöz szükséges portokon. Alapértelmezés szerint a portok speciális engedélyezési beállításai nem szükségesek, ha az előző szakaszban ismertetett Azure felügyeleti forgalom a 443-es porton keresztül érhető el a fürt számára.

Az egyes szolgáltatásokhoz tartozó portok listáját lásd: [Apache Hadoop Services által használt portok a HDInsight](hdinsight-hadoop-port-settings-for-services.md) -dokumentumban.

A virtuális készülékekre vonatkozó tűzfalszabályok részletes ismertetését lásd: [virtuális készülék forgatókönyvének](../virtual-network/virtual-network-scenario-udr-gw-nva.md) dokumentuma.

## <a name="next-steps"></a>További lépések

* Az Azure-beli virtuális hálózatok létrehozásával kapcsolatos Példákért lásd: [virtuális hálózatok létrehozása az Azure HDInsight](hdinsight-create-virtual-network.md)-fürtökhöz.
* A helyszíni hálózathoz való kapcsolódás HDInsight konfigurálásának teljes körű példáját lásd: [a HDInsight összekapcsolása egy](./connect-on-premises-network.md)helyszíni hálózattal.
* Az Apache HBase-fürtök Azure-beli virtuális hálózatokban való konfigurálásával kapcsolatban lásd: [Apache HBase-fürtök létrehozása a HDInsight-ben az azure Virtual Network](hbase/apache-hbase-provision-vnet.md).
* Az Apache HBase geo-replikáció konfigurálásával kapcsolatban lásd: [Apache HBase-fürtök replikálásának beállítása az Azure Virtual Networks](hbase/apache-hbase-replication.md)szolgáltatásban.
* Az Azure Virtual Networks szolgáltatással kapcsolatos további információkért tekintse meg az [azure Virtual Network áttekintését](../virtual-network/virtual-networks-overview.md).
* A hálózati biztonsági csoportokkal kapcsolatos további információkért lásd: [hálózati biztonsági csoportok](../virtual-network/security-overview.md).
* A felhasználó által megadott útvonalakkal kapcsolatos további információkért lásd: [felhasználó által definiált útvonalak és IP-továbbítás](../virtual-network/virtual-networks-udr-overview.md).
