---
title: Oktatóanyag – több webhelyet üzemeltető Application Gateway létrehozása a Azure Portal használatával
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre olyan Application Gateway-t, amely több webhelyet üzemeltet a Azure Portal használatával.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: victorh
ms.openlocfilehash: 73a313a6244971b65ba89fb7b676610d88acabfa
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2019
ms.locfileid: "68498455"
---
# <a name="tutorial-create-and-configure-an-application-gateway-to-host-multiple-web-sites-using-the-azure-portal"></a>Oktatóanyag: Hozzon létre és konfiguráljon egy Application Gatewayt több webhelynek a Azure Portal használatával történő üzemeltetéséhez

Az Azure Portal használatával [több webhely üzemeltetését konfigurálhatja](multiple-site-overview.md) az [Application Gateway](overview.md)létrehozásakor. Ebben az oktatóanyagban a háttérbeli címkészlet a virtuális gépek használatával van meghatározva. Ezután az Ön tulajdonában lévő tartományok alapján konfigurálhat figyelőket és szabályokat a webes forgalom a készletekben lévő megfelelő kiszolgálókra irányításához. Ez az oktatóanyag feltételezi, hogy Ön több tartománnyal rendelkezik. Példaként a *www.contoso.com* és a *www.fabrikam.com* tartományt használja.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Application Gateway létrehozása
> * Virtuális gépek létrehozása háttérbeli kiszolgálókhoz
> * Háttér-készletek létrehozása a háttér-kiszolgálókkal
> * Háttérbeli figyelők létrehozása
> * Útválasztási szabályok létrehozása
> * CNAME rekord létrehozása a tartományban

![Többhelyes útválasztási példa](./media/create-multiple-sites-portal/scenario.png)

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen

## <a name="create-an-application-gateway"></a>Application Gateway létrehozása

1. Válassza az **erőforrás létrehozása** elemet a Azure Portal bal oldali menüjében. Megjelenik az **új** ablak.

2. Válassza a **hálózatkezelés** lehetőséget, majd a **Kiemelt** listában válassza a **Application Gateway** lehetőséget.

### <a name="basics-tab"></a>Alapbeállítások lap

1. Az **alapok** lapon adja meg a következő Application Gateway-beállításokhoz tartozó értékeket:

   - **Erőforráscsoport**: Válassza ki az erőforráscsoport **myResourceGroupAG** . Ha nem létezik, válassza az **új létrehozása** elemet a létrehozásához.
   - **Application Gateway neve**: Adja meg a *myAppGateway* nevet az Application Gateway neveként.

     ![Új Application Gateway létrehozása: Alapvető beállítások](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2.  Ahhoz, hogy az Azure kommunikáljon a létrehozott erőforrások között, szüksége van egy virtuális hálózatra. Hozzon létre egy új virtuális hálózatot, vagy használjon egy meglévőt. Ebben a példában egy új virtuális hálózatot fog létrehozni az Application Gateway létrehozásakor. Application Gateway példányok külön alhálózatokban jönnek létre. Ebben a példában két alhálózatot hoz létre: egyet az Application Gateway számára, és egy másikat a háttér-kiszolgálók számára.

    A **virtuális hálózat konfigurálása**területen válassza az **új létrehozása** lehetőséget az új virtuális hálózat létrehozásához. A megnyíló **virtuális hálózat létrehozása** ablakban adja meg a következő értékeket a virtuális hálózat és két alhálózat létrehozásához:

    - **Név**: Adja meg a virtuális hálózat nevének *myVNet* .

    - **Alhálózat neve** (Application Gateway alhálózat): Az  alhálózatok rácsa az *alapértelmezett*nevű alhálózatot jeleníti meg. Módosítsa az alhálózat nevét a *myAGSubnet*értékre.<br>Az Application Gateway-alhálózat csak Application Gateway átjárókat tartalmazhat. Más erőforrások nem engedélyezettek.

    - **Alhálózat neve** (háttér-kiszolgáló alhálózata): Az alhálózatok rácsának  második sorában írja be a *MyBackendSubnet* **nevet az alhálózat neve** oszlopba.

    - **Címtartomány** (háttér-kiszolgáló alhálózata): Az alhálózatok rácsának  második sorában adjon meg egy címtartományt, amely nem fedi át a *myAGSubnet*címtartományt. Ha például a *myAGSubnet* 10.0.0.0/24, a *10.0.1.0/24* értéket adja meg a *myBackendSubnet*-tartományhoz.

    A **virtuális hálózat létrehozása** ablak bezárásához és a virtuális hálózat beállításainak mentéséhez kattintson **az OK gombra** .

     ![Új Application Gateway létrehozása: virtuális hálózat](./media/application-gateway-create-gateway-portal/application-gateway-create-vnet.png)
    
3. Az **alapvető** beállítások lapon fogadja el a többi beállítás alapértelmezett értékeit, majd válassza **a Next (tovább) gombot: Frontendek**.

### <a name="frontends-tab"></a>Frontendek lap

1. A **frontendek** lapon ellenőrizze, hogy a előtérbeli **IP-cím típusa** **nyilvános**értékre van-e állítva. <br>Az előtérbeli IP-címet úgy állíthatja be, hogy a használati esetnek megfelelően nyilvános vagy privát legyen. Ebben a példában egy nyilvános előtérbeli IP-címet választ.
   > [!NOTE]
   > A Application Gateway v2 SKU esetében csak **nyilvános** ELŐTÉRBELI IP-konfigurációt választhat. A privát előtérbeli IP-konfiguráció jelenleg nincs engedélyezve ehhez a v2 SKU-hoz.

2. Válassza a **nyilvános IP-cím** **új létrehozása** lehetőséget, és adja meg a *myAGPublicIPAddress* a nyilvános IP-cím neveként, majd kattintson **az OK gombra**. 

     ![Új Application Gateway létrehozása: frontendek](./media/application-gateway-create-gateway-portal/application-gateway-create-frontends.png)

3. Válassza **a Next (tovább) lehetőséget: Háttérrendszer**.

### <a name="backends-tab"></a>Háttérrendszer lap

A háttér-készlet arra szolgál, hogy a kérelmeket a kérést kiszolgáló háttér-kiszolgálókra irányítsa. A háttér-készletek lehetnek hálózati adapterek, virtuálisgép-méretezési csoportok, nyilvános IP-címek, belső IP-címek, teljes tartománynevek (FQDN) és több-bérlős háttérrendszer, például Azure App Service. Ebben a példában egy üres háttér-készletet hozunk létre az Application Gateway használatával, majd a háttérbeli célokat hozzá kell adni a háttér-készlethez.

1. A **háttérrendszer** lapon válassza a **+ háttér-készlet hozzáadása**elemet.

2. A megnyíló **háttérbeli készlet hozzáadása** ablakban adja meg a következő értékeket egy üres háttérbeli készlet létrehozásához:

    - **Név**: Adja meg a *contosoPool* nevét a háttérbeli készlet neveként.
    - **Háttérbeli készlet hozzáadása célok nélkül**: Válassza az **Igen** lehetőséget, ha célokat nem tartalmazó háttér-készletet szeretne létrehozni. Az Application Gateway létrehozása után hozzá kell adni a háttérbeli célokat.

3. A háttérbeli **készlet hozzáadása** ablakban válassza a **Hozzáadás** elemet a háttérbeli készlet konfigurációjának mentéséhez, és térjen  vissza a háttérrendszer lapra.
4. Most adjon hozzá egy *fabrikamPool*nevű másik háttér-készletet.

     ![Új Application Gateway létrehozása: háttérrendszer](./media/create-multiple-sites-portal/backend-pools.png)

4. A backends (háttérrendszer) **lapon válassza a Next (tovább) gombot:  Konfiguráció**.

### <a name="configuration-tab"></a>Konfiguráció lap

A **konfiguráció** lapon összekapcsolja az útválasztási szabály használatával létrehozott előtér-és háttér-készleteket.

1. Válassza a **szabály hozzáadása** lehetőséget az **útválasztási szabályok** oszlopban.

2. A megnyíló **útválasztási szabály hozzáadása** ablakban írja be a *ContosoRule* nevet a **szabály neveként**.

3. Egy útválasztási szabályhoz egy figyelő szükséges. Az **útválasztási szabály hozzáadása** ablak **figyelő** lapján adja meg az alábbi értékeket a figyelőhöz:

    - **Figyelő neve**: Adja meg a *contosoListener* a figyelő neveként.
    - Előtéri **IP-cím**: Válassza a **nyilvános** lehetőséget a előtérhez létrehozott nyilvános IP-cím kiválasztásához.

   A **További beállítások**területen:
   - **Figyelő típusa**: Több hely
   - **Állomásnév**: **www.contoso.com**

   Fogadja el az alapértelmezett értékeket a **figyelő** lapon a többi beállításnál, majd válassza a **háttérbeli célok** fület a többi útválasztási szabály konfigurálásához.

   ![Új Application Gateway létrehozása: figyelő](./media/create-multiple-sites-portal/routing-rule.png)

4. A **háttérbeli célok** lapon válassza a **ContosoPool** lehetőséget a **háttérbeli célként**.

5. A **http**-beállításnál válassza az **új létrehozása** lehetőséget egy új http-beállítás létrehozásához. A HTTP-beállítás határozza meg az útválasztási szabály viselkedését. A megnyíló **http-beállítás hozzáadása** ablakban írja be a *ContosoHTTPSetting* nevet a **http-beállítás neveként**. Fogadja el az alapértelmezett értékeket a további beállításokhoz a **http-beállítás hozzáadása** ablakban, majd válassza a **Hozzáadás** lehetőséget az **útválasztási szabály hozzáadása** ablakhoz való visszatéréshez. 

6. Az útválasztási szabály **hozzáadása** ablakban válassza a **Hozzáadás** lehetőséget az útválasztási szabály mentéséhez és a **konfiguráció** laphoz való visszatéréshez.
7. Válassza a **szabály hozzáadása** lehetőséget, és adjon hozzá egy hasonló szabályt, figyelőt, háttér-célt és http-beállítást a fabrikam számára.

     ![Új Application Gateway létrehozása: útválasztási szabály](./media/create-multiple-sites-portal/fabrikamRule.png)

7. Válassza **a Next (tovább) lehetőséget: Címkék** , majd **tovább: Felülvizsgálat + létrehozás**.

### <a name="review--create-tab"></a>Felülvizsgálat + Létrehozás lap

Tekintse át a **felülvizsgálat + létrehozás** lapon található beállításokat, majd válassza a **Létrehozás** lehetőséget a virtuális hálózat, a nyilvános IP-cím és az Application Gateway létrehozásához. Az Azure az Application Gateway létrehozásához több percet is igénybe vehet.

Várjon, amíg a telepítés sikeresen befejeződik, mielőtt továbblép a következő szakaszra.

## <a name="add-backend-targets"></a>Háttérbeli célok hozzáadása

Ebben a példában a virtuális gépeket célként használt háttérként fogja használni. Használhat meglévő virtuális gépeket, vagy újakat is létrehozhat. Hozzon létre két virtuális gépet, amelyeket az Azure háttér-kiszolgálóként használ az Application Gateway számára.

A háttérbeli célok hozzáadásához a következőket kell tennie:

1. Hozzon létre két új virtuális gépet, *contosoVM* és *fabrikamVM*a háttér-kiszolgálóként való használathoz.
2. Telepítse az IIS-t a virtuális gépeken annak ellenőrzéséhez, hogy az Application Gateway sikeresen létrejött-e.
3. Adja hozzá a háttér-kiszolgálókat a háttérbeli készletekhez.

### <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

1. A Azure Portal válassza az **erőforrás létrehozása**lehetőséget. Megjelenik az **új** ablak.
2. Válassza a **számítás** lehetőséget, majd válassza a **Windows Server 2016 Datacenter** elemet a **népszerű** listában. Megjelenik a **virtuális gép létrehozása** lap.<br>Application Gateway átirányíthatja a forgalmat a háttér-készletben használt bármilyen típusú virtuális gépre. Ebben a példában egy Windows Server 2016 Datacenter rendszert használ.
3. Adja meg ezeket az értékeket az **alapok** lapon a következő virtuálisgép-beállításokhoz:

    - **Erőforráscsoport**: Válassza az erőforráscsoport neve **myResourceGroupAG** lehetőséget.
    - **Virtuális gép neve**: Adja meg a *contosoVM* nevet a virtuális gép nevéhez.
    - **Felhasználónév**: Adja  meg az azureuser nevet a rendszergazda felhasználónevénél.
    - **Jelszó**: Adja meg a *Azure123456!* a rendszergazdai jelszóhoz.
4. Fogadja el a többi alapértelmezett értéket, **majd válassza a Next (tovább) gombot: Lemezek**.  
5. Fogadja el a **lemezek** lap alapértelmezett értékeit **, majd kattintson a Next (tovább) gombra: Hálózatkezelés**.
6. A **hálózatkezelés** lapon ellenőrizze, hogy a **virtuális hálózat** **myVNet** van-e kiválasztva, és az **alhálózat** **myBackendSubnet**értékre van-e állítva. Fogadja el a többi alapértelmezett értéket, **majd válassza a Next (tovább) gombot: Felügyelet**.<br>A Application Gateway képes kommunikálni a virtuális hálózaton kívüli példányokkal, de gondoskodnia kell az IP-kapcsolatról.
7. A **felügyelet** lapon állítsa be a rendszerindítási **diagnosztika** beállítást **off**értékre. Fogadja el a többi alapértelmezett értéket, majd válassza a **felülvizsgálat + létrehozás**lehetőséget.
8. A **felülvizsgálat + létrehozás** lapon tekintse át a beállításokat, javítsa ki az érvényesítési hibákat, majd válassza a **Létrehozás**lehetőséget.
9. A folytatás előtt várja meg, amíg a virtuális gép létrehozása befejeződik.

### <a name="install-iis-for-testing"></a>Az IIS telepítése teszteléshez

Ebben a példában az IIS-t csak akkor telepíti a virtuális gépekre, ha ellenőrzi, hogy az Azure sikeresen létrehozta-e az Application Gatewayt.

1. Nyissa meg [Azure PowerShell](https://docs.microsoft.com/azure/cloud-shell/quickstart-powershell). Ehhez válassza ki a **Cloud Shell** elemet a Azure Portal felső navigációs sávján, majd válassza a **PowerShell** elemet a legördülő listából. 

    ![Egyéni bővítmény telepítése](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

2. Futtassa a következő parancsot az IIS a virtuális gépen való telepítéséhez: 

    ```azurepowershell-interactive
    Set-AzVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -ExtensionName IIS `
      -VMName contosoVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
      -Location EastUS
    ```

3. Hozzon létre egy második virtuális gépet, és telepítse az IIS-t a korábban befejezett lépések használatával. Használja a *fabrikamVM* a virtuális gép nevéhez és a **set-AzVMExtension** parancsmag **VMName** beállításához.

### <a name="add-backend-servers-to-backend-pools"></a>Háttér-kiszolgálók hozzáadása a háttérbeli készletekhez

1. Válassza a **minden erőforrás**lehetőséget, majd válassza a **myAppGateway**lehetőséget.

2. Válassza ki a **háttér-készletek** elemet a bal oldali menüben.

3. Válassza a **contosoPool**lehetőséget.

4. A **célok**területen válassza a **virtuális gép** lehetőséget a legördülő listából.

5. A **virtuális gép** és **hálózati adapterek**területen válassza ki a **contosoVM** virtuális gépet, és a társított hálózati adaptert a legördülő listából.

    ![Háttérkiszolgálók hozzáadása](./media/create-multiple-sites-portal/edit-backend-pool.png)

6. Kattintson a **Mentés** gombra.
7. Ismételje meg a *fabrikamVM* és az illesztőfelület hozzáadását a *fabrikamPool*.

Várjon, amíg a telepítés befejeződik, mielőtt továbblép a következő lépésre.

## <a name="create-a-www-a-record-in-your-domains"></a>Www-rekord létrehozása a tartományokban

Miután az Application Gateway nyilvános IP-címmel lett létrehozva, lekérheti az IP-címet, és felhasználhatja egy rekord létrehozásához a tartományokban. 

1. Kattintson **a minden erőforrás**elemre, majd a **myAGPublicIPAddress**elemre.

    ![Application Gateway DNS-címe rögzítése](./media/create-multiple-sites-portal/public-ip.png)

2. Másolja ki az IP-címet, és használja egy új *www* -rekord értékként a tartományokban.

## <a name="test-the-application-gateway"></a>Az alkalmazásátjáró tesztelése

1. Adja meg a tartománya nevét a böngésző címsorában. Például: http://www.contoso.com.

    ![Contoso webhely tesztelése az alkalmazásátjáróban](./media/create-multiple-sites-portal/application-gateway-iistest.png)

2. Változtassa meg a címet a másik tartományára. Ekkor az eredmény a következő példához hasonló:

    ![Fabrikam webhely tesztelése az alkalmazásátjáróban](./media/create-multiple-sites-portal/application-gateway-iistest2.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az Application Gateway használatával létrehozott erőforrásokra, távolítsa el az erőforráscsoportot. Az erőforráscsoport eltávolításakor az Application Gateway és az összes kapcsolódó erőforrás is törlődik.

Az erőforráscsoport eltávolítása:

1. A Azure Portal bal oldali menüjében válassza az **erőforráscsoportok**lehetőséget.
2. Az **erőforráscsoportok** lapon keresse meg a **myResourceGroupAG** a listában, majd válassza ki azt.
3. Az **erőforráscsoport lapon**válassza az **erőforráscsoport törlése**elemet.
4. Írja be a *myResourceGroupAG* **nevet az erőforráscsoport neveként** , majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További információ az Azure Application Gateway](application-gateway-introduction.md)