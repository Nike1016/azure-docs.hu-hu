---
title: 'Gyors útmutató: Hozzon létre egy nyilvános alapszintű Load Balancer a Azure Portal használatával'
titlesuffix: Azure Load Balancer
description: Ez az útmutató bemutatja, hogyan hozhat létre alapszintű nyilvános terheléselosztót az Azure Portal használatával.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to create a Basic Load balancer so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2019
ms.author: allensu
ms.custom: seodec18
ms.openlocfilehash: 9819111c8264493648233f40252db4fb4410aaf1
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68274086"
---
# <a name="quickstart-create-a-basic-load-balancer-by-using-the-azure-portal"></a>Gyors útmutató: Alapszintű Load Balancer létrehozása a Azure Portal használatával

Terheléselosztási a bejövő kérelmeket virtuális gépeken (VM) között osztja szét a magasabb rendelkezésre állást és méretezést biztosít. Az Azure portal használatával létrehozhat egy terheléselosztót, és a virtuális gépek közötti forgalom elosztására. Ez a rövid útmutató bemutatja, hogyan hozhat létre és konfigurálja a terheléselosztó háttér-kiszolgálók és hálózati erőforrások, az alapszintű díjcsomagot.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt. 

Ebben a rövid útmutatóban a feladatokat végezheti el, jelentkezzen be a [az Azure portal](https://portal.azure.com).

## <a name="create-a-basic-load-balancer"></a>Alapszintű Load Balancer létrehozása

Először hozzon létre egy nyilvános alapszintű Load Balancer a portál használatával. A név és a nyilvános IP-címet hoz létre automatikusan megtörténik, a terheléselosztó előtérbeli.

1. A képernyő bal felső részén kattintson az **Erőforrás létrehozása** > **Hálózatkezelés** > **Terheléselosztó** elemre.
2. A **Load Balancer létrehozása** lap **alapok** lapján adja meg vagy válassza ki a következő adatokat, fogadja el az alapértelmezett értékeket a többi beállításnál, majd válassza a **felülvizsgálat + létrehozás**:

    | Beállítás                 | Value                                              |
    | ---                     | ---                                                |
    | Subscription               | Válassza ki előfizetését.    |    
    | Resource group         | Válassza az **új létrehozása** lehetőséget, és írja be a *myresourcegrouplb erőforráscsoportban* szöveget a szövegmezőbe.|
    | Name (Név)                   | *myLoadBalancer*                                   |
    | Régió         | Válassza a **Nyugat-Európa** régiót.                                        |
    | Type          | Válassza a **nyilvános**lehetőséget.                                        |
    | SKU           | Válassza az **Alapszintű** lehetőséget.                          |
    | Nyilvános IP-cím | Válassza az **Új létrehozása** lehetőséget. |
    | Nyilvános IP-cím neve              | *MyPublicIP*   |
    | Hozzárendelés| Statikus|

3. A **felülvizsgálat + létrehozás** lapon kattintson a **Létrehozás**gombra.   


## <a name="create-back-end-servers"></a>Háttérkiszolgálók létrehozása

Ezután hozzon létre egy virtuális hálózatot és két virtuális gépet az alapszintű load Balancer háttérkészlethez számára. 

### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

1. A portál bal felső oldalán válassza **erőforrás létrehozása** > **hálózatkezelés** > **virtuális hálózati**.
   
1. Az a **virtuális hálózat létrehozása** panelen adja meg ezeket az értékeket:
   
   - **Név**: Írja be a következőt: *MyVnet*.
   - **ResourceGroup**: A legördülő **listából válassza a meglévő lehetőséget** , és válassza a **myresourcegrouplb erőforráscsoportban**lehetőséget. 
   - Alhálózat > **neve**: Írja be a következőt: *MyBackendSubnet*.
   
1. Kattintson a **Létrehozás** gombra.

   ![Virtuális hálózat létrehozása](./media/load-balancer-get-started-internet-portal/2-load-balancer-virtual-network.png)

### <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

1. A portál bal felső oldalán válassza **erőforrás létrehozása** > **számítási** > **Windows Server 2016 Datacenter**. 
   
1. A **hozzon létre egy virtuális gépet**adja meg a következő értékeket a **alapjai** lapon:
   - **Előfizetési** > **erőforráscsoport**: Legördülő menüből válassza a **myresourcegrouplb erőforráscsoportban**lehetőséget.
   - **Példány részletei** > –**virtuális gép neve**: Írja be a következőt: *MyVM1*.
   - **Példány részletei** > **rendelkezésre állási beállítások**: 
     1. Legördülő listára, és válassza ki **rendelkezésre állási csoport**. 
     2. Válassza ki **új létrehozása**, típus *MyAvailabilitySet*, és válassza ki **OK**.
  
1. Válassza a **hálózatkezelés** lapot, vagy válassza **a Next (tovább) lehetőséget: Lemezek**, majd **tovább: Hálózatkezelés**. 
   
   Győződjön meg arról, hogy a következő kijelölt:
   - **Virtuális hálózat**: **MyVnet**
   - **Alhálózat**: **MyBackendSubnet**
   - **Nyilvános IP-cím**: **MyVM1-ip**
   
   Alatt létrehozni egy új hálózati biztonsági csoport (NSG), egy tűzfal, típusú **hálózati biztonsági csoport**válassza **speciális**. 
   1. Az a **konfigurálása a hálózati biztonsági csoport** mezőben válassza **új létrehozása**. 
   1. Típus *MyNetworkSecurityGroup*, és válassza ki **OK**. 
   
1. Válassza ki a **felügyeleti** lapon, vagy válasszon **tovább** > **felügyeleti**. A **figyelés**állítsa be **rendszerindítási diagnosztika** való **ki**.
   
1. Válassza az **Áttekintés + létrehozás** lehetőséget.
   
1. Tekintse át a beállításokat, és válassza ki **létrehozás**. 

1. A lépésekkel hozzon létre egy második virtuális Gépet nevű *MyVM2*, az egy **nyilvános IP-cím** címe *MyVM2-ip*, és a többi beállítás ugyanaz, mint a MyVM1. 

### <a name="create-nsg-rules-for-the-vms"></a>Az NSG-szabályok létrehozása a virtuális gépekhez

Ebben a szakaszban létrehozott hálózati biztonsági csoport (NSG) szabályai engedélyezik a bejövő internetes (HTTP) és a távoli asztali (RDP) kapcsolatokat a virtuális gépek.

1. Válassza ki a bal oldali menü **Összes erőforrás** elemét. Az erőforrások listájából válassza ki a **MyNetworkSecurityGroup** a a **MyResourceGroupLB** erőforráscsoportot.
   
1. A **Beállítások** területen válassza a **Bejövő biztonsági szabályok** elemet, majd a **Hozzáadás** lehetőséget.
   
1. Az a **bejövő biztonsági szabály felvétele** párbeszédpanelen írja be vagy válassza ki a következő HTTP-szabály:
   
   - **Forrás**: Válassza ki a **szolgáltatás címkéjét**.  
   - **Forrásoldali szolgáltatás címkéje**: Válassza az **Internet**lehetőséget. 
   - **Célport tartományai**: *80*típusú.
   - **Protokoll**: Válassza a **TCP**lehetőséget. 
   - **Művelet**: Válassza az **Engedélyezés**lehetőséget.  
   - **Prioritás**: *100*típusú. 
   - **Név**: Írja be a következőt: *: myhttprule*. 
   - **Leírás**: Írja be a következőt: *http engedélyezése*. 
   
1. Válassza a **Hozzáadás** lehetőséget. 
   
   ![NSG-szabály létrehozása](./media/load-balancer-get-started-internet-portal/8-load-balancer-nsg-rules.png)
   
1. A bejövő RDP-szabályban, az alábbi eltérő értékekkel ismételje meg a lépéseket:
   - **Célport tartományai**: *3389*típusú.
   - **Prioritás**: *200*típusú. 
   - **Név**: Írja be a következőt: *MyRDPRule*. 
   - **Leírás**: Írja be az *RDP engedélyezése lehetőséget*. 

## <a name="create-resources-for-the-load-balancer"></a>A load Balancer-erőforrások létrehozása

Ebben a szakaszban a terheléselosztó-beállításai a háttér címkészletet, az állapotfigyelő mintavételező és a egy terheléselosztó-szabályt konfigurálja.

### <a name="create-a-backend-address-pool"></a>Hátércímkészlet létrehozása

A terheléselosztó osztja el a forgalmat a virtuális gépekhez, használja a háttér címkészletet. A háttér-címkészletet a terheléselosztóhoz csatlakozó virtuális hálózati adaptereket (NIC) IP-címét tartalmazza. 

**A háttér-címkészlet létrehozása, amely tartalmazza a VM1, VM2 és:**

1. Válassza ki **összes erőforrás** a bal oldali menüben, és válassza ki a **MyLoadBalancer** az erőforrások listájából.
   
1. A **Beállítások** alatt válassza a **Háttérkészletek**, majd a **Hozzáadás** lehetőséget.
   
1. Az a **háttérkészlet hozzáadása** lapon adja meg a következő értékeket:
   
   - **Név**: Írja be a következőt: *MyBackEndPool*.
   - **Társított**: Legördülő listára, és válassza ki **rendelkezésre állási csoport**.
   - **Rendelkezésre állási csoport**: Válassza a **MyAvailabilitySet**lehetőséget.
   
1. Válassza ki **cél IP-konfiguráció hozzáadása**. 
   1. Adja hozzá az egyes virtuális gépek (**MyVM1** és **MyVM2**), amely az Ön hozott létre a háttércímkészletet.
   2. Miután hozzáadta az egyes, a legördülő listára, és válassza ki a **hálózati IP-konfiguráció**. 
   
1. Kattintson az **OK** gombra.
   
   ![Adja hozzá a háttércímkészlethez](./media/load-balancer-get-started-internet-portal/3-load-balancer-backend-02.png)
   
1. Az a **háttérkészletek** lapon, bontsa ki a **MyBackendPool** , és győződjön meg arról, hogy mindkét **VM1** és **VM2** szerepelnek.

### <a name="create-a-health-probe"></a>Állapotminta létrehozása

Ahhoz, hogy a load balancer monitorozhassa a virtuális gép állapotát, állapotmintát használni. Az állapotminta dinamikusan hozzáadja a virtuális gépeket a terheléselosztó rotációjához vagy eltávolítja belőle azokat, az állapotellenőrzésekre adott válaszuk alapján. 

**A virtuális gépek állapotának figyelése egy állapotminta létrehozásához:**

1. Válassza ki **összes erőforrás** a bal oldali menüben, és válassza ki a **MyLoadBalancer** az erőforrások listájából.
   
1. A **Beállítások** területn válassza az **Állapotminták**, majd a **Hozzáadás** lehetőséget.
   
1. Az a **állapotadat-mintavétel hozzáadása** lapon adja meg a következő értékeket:
   
   - **Név**: Írja be a következőt: *MyHealthProbe*.
   - **Protokoll**: Legördülő menüből válassza a **http**lehetőséget. 
   - **Port**: *80*típusú. 
   - **Elérési út**: Fogadja el */* az alapértelmezett URI-t. Ez az érték lecserélheti bármely más URI-t. 
   - **Időköz**: Adja meg a *15*értéket. Az időköz a mintavételi kísérletek között eltelt másodpercek száma.
   - Nem megfelelő **állapot küszöbértéke**: *2*. típus. Ez az érték az egymást követő mintavételi hiba, amely a virtuális gép nem megfelelő állapotúnak számít még azelőtt számát jelenti.
   
1. Kattintson az **OK** gombra.
   
   ![Mintavétel hozzáadása](./media/load-balancer-get-started-internet-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Terheléselosztási szabály létrehozása

A terheléselosztási szabály megadja, hogy a rendszer hogyan ossza el a forgalmat a virtuális gépek között. A szabályok meghatározzák az előtérbeli IP-konfigurációt a bejövő forgalmat, a háttérbeli IP-címkészletet, és a forgalom és a szükséges forrás és cél portok. 

A nevű terheléselosztási szabályt **MyLoadBalancerRule** az előtér-alkalmazást a 80-as porton figyel **LoadBalancerFrontEnd**. A szabály küld hálózati forgalmat a háttér-címkészletet **MyBackEndPool**, szintén a 80-as porton. 

**A terheléselosztási szabály létrehozása:**


1. Válassza ki **összes erőforrás** a bal oldali menüben, és válassza ki a **MyLoadBalancer** az erőforrások listájából.
   
1. A **Beállítások** területen válassza a **Terheléselosztási szabályok** elemet, majd a **Hozzáadás** lehetőséget.
   
1. Az a **terheléselosztási szabály hozzáadása** lapon adja meg a következő értékeket:
   
   - **Név**: Írja be a következőt: *MyLoadBalancerRule*.
   - **Előtérbeli IP-cím:** Írja be a következőt: *LoadBalancerFrontend*.
   - **Protokoll**: Válassza a **TCP**lehetőséget.
   - **Port**: *80*típusú.
   - **Háttér-port**: *80*típusú.
   - **Háttér-készlet**: Válassza a **MyBackendPool**lehetőséget.
   - **Állapot**mintavétele: Válassza a **MyHealthProbe**lehetőséget. 
   
1. Kattintson az **OK** gombra.
   
   ![Adjon hozzá egy terheléselosztó-szabályt](./media/load-balancer-get-started-internet-portal/5-load-balancing-rules.png)

## <a name="test-the-load-balancer"></a>A terheléselosztó tesztelése

A nyilvános IP-címet fogja használni a virtuális gépeken a terheléselosztó teszteléséhez. 

A portálon a a **áttekintése** lapján **MyLoadBalancer**, nyilvános IP-cím alatt található **nyilvános IP-cím**. Vigye a kurzort a címet, majd válassza a **másolási** másolja azt a ikonra. 

### <a name="install-iis-on-the-vms"></a>Az IIS telepítése a virtuális gépeken

Telepítse az Internet Information Services (IIS) a virtuális gépeken a terheléselosztó tesztelése érdekében.

**A távoli asztali (RDP) a virtuális géppel:**

1. Válassza a portál **összes erőforrás** a bal oldali menüben. Az erőforrások listájából válassza ki a **MyVM1** a a **MyResourceGroupLB** erőforráscsoportot.
   
1. Az a **áttekintése** lapon jelölje be **Connect**, majd válassza ki **töltse le az RDP-fájl**. 
   
1. Nyissa meg az RDP letöltött, majd válasszon **Connect**.
   
1. A Windows biztonsági képernyőn válassza ki a **további lehetőségek** , majd **másik fiók használata**. 
   
   Adja meg a felhasználónevét és jelszavát, és válassza ki **OK**.
   
1. Válasz **Igen** a minden tanúsítvány-kérdésre. 
   
   A virtuális gép asztalához egy új ablakban nyílik meg. 
   
**Az IIS telepítése**

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás**lehetőséget, majd az erőforrások listából válassza ki a **myVM1** , amely a *myresourcegroupslb erőforráscsoportban* erőforráscsoporthoz található.
2. Az **Áttekintés** oldalon a **Csatlakozás** gombra kattintva hozzon létre RDP-kapcsolatot a virtuális géppel.
5. Jelentkezzen be a virtuális gépre az adott virtuális gép létrehozásakor megadott hitelesítő adatokkal. Ez elindít egy távoli asztali munkamenetet a *myVM1* virtuális géppel.
6. A kiszolgáló asztalán lépjen a **Windows felügyeleti eszközök**>**Windows PowerShell** elemre.
7. A PowerShell-ablakban futtassa az alábbi parancsokat az IIS-kiszolgáló telepítéséhez, távolítsa el az alapértelmezett iisstart.htm fájlt, majd adjon hozzá egy új iisstart.htm fájt, amely megjeleníti a virtuális gép nevét:

   ```azurepowershell
    
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # remove default htm file
    remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
   ```
6. Szüntesse meg a távoli asztali munkamenetet a *myVM1*-gyel.
7. Az 1–6. lépés megismétlésével telepítse az IIS-t és a frissített iisstart.htm fájlt a *myVM2* gépen.
   
1. Ismételje meg a virtuális gép **MyVM2**, kivéve a célkiszolgáló beállítása **MyVM2**.

### <a name="test-the-load-balancer"></a>A terheléselosztó tesztelése

Nyisson meg egy böngészőt, és illessze be a terheléselosztó nyilvános IP-címet a böngésző címsorában. Az IIS kiszolgáló alapértelmezett weblap kell megjelennie a böngészőben.

![IIS-webkiszolgáló](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Annak megtekintéséhez, hogyan osztja el a Load Balancer az alkalmazást futtató két virtuális gép között a forgalmat, kényszerítheti a webböngésző frissítését.
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Törli a terheléselosztó és az összes kapcsolódó erőforrást, ha már nincs szüksége, nyissa meg a **MyResourceGroupLB** erőforrás-csoport, és válassza **erőforráscsoport törlése**.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy Basic szintű terheléselosztót. Létrehozott és konfigurált egy erőforráscsoportot, hálózati erőforrások, háttérkiszolgálók, az állapotfigyelő mintavételező és szabályok a terheléselosztó használatát. Az IIS telepítése a virtuális gépeken, és használja azt a terheléselosztó teszteléséhez. 

Az Azure Load Balancer kapcsolatos további információkért folytassa az oktatóanyagokat.

> [!div class="nextstepaction"]
> [Azure Load Balancer-oktatóanyagok](tutorial-load-balancer-basic-internal-portal.md)
