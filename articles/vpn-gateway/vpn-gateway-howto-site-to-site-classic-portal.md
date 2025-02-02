---
title: 'A helyszíni hálózat összekapcsolása egy Azure-beli virtuális hálózattal: Helyek közötti VPN (klasszikus): Portál | Microsoft Docs'
description: Létrehozhat a helyszíni hálózatot a klasszikus Azure-beli virtuális hálózattal a nyilvános interneten keresztül összekötő IPsec-kapcsolatot.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 08/15/2019
ms.author: cherylmc
ms.openlocfilehash: 77cfde8cc9c6556b907f1185f451c70c8c8e888d
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69534025"
---
# <a name="create-a-site-to-site-connection-using-the-azure-portal-classic"></a>Helyek közötti kapcsolat létrehozása az Azure Portal használatával (klasszikus)


Ez a cikk bemutatja, hogyan használhatja az Azure Portalt egy helyek közötti VPN-átjárókapcsolat létrehozására egy helyszíni hálózat és a Vnet között. A cikkben ismertetett lépések a klasszikus üzemi modellre vonatkoznak, és nem vonatkoznak az aktuális üzemi modellre, a Resource Managerre. Ezt a konfigurációt más üzembehelyezési eszközzel vagy üzemi modellel is létrehozhatja, ha egy másik lehetőséget választ az alábbi listáról:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Parancssori felület](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [(Klasszikus) Azure Portal](vpn-gateway-howto-site-to-site-classic-portal.md)
> 
>

A helyek közötti VPN-átjárókapcsolat használatával kapcsolat hozható létre a helyszíni hálózat és egy Azure-beli virtuális hálózat között egy IPsec/IKE (IKEv1 vagy IKEv2) VPN-alagúton keresztül. Az ilyen típusú kapcsolatokhoz egy helyszíni VPN-eszközre van szükség, amelyhez hozzá van rendelve egy kifelé irányuló, nyilvános IP-cím. További információk a VPN-átjárókról: [Információk a VPN Gatewayről](vpn-gateway-about-vpngateways.md).

![Helyek közötti VPN Gateway létesítmények közötti kapcsolathoz – diagram](./media/vpn-gateway-howto-site-to-site-classic-portal/site-to-site-diagram.png)

## <a name="before"></a>Előkészületek

A konfigurálás megkezdése előtt győződjön meg a következő feltételek teljesüléséről:

* Erősítse meg, hogy a klasszikus üzemi modellt kívánja használni. Ha a Resource Manager-alapú üzemi modellt szeretné használni, lásd: [Helyek közötti kapcsolat létrehozása (Resource Manager)](vpn-gateway-howto-site-to-site-resource-manager-portal.md). Amikor lehet, javasoljuk, hogy használja a Resource Manager-alapú üzemi modellt.
* Győződjön meg arról, hogy rendelkezésre áll egy kompatibilis VPN-eszköz és egy azt konfigurálni képes személy. További információk a kompatibilis VPN-eszközökről és az eszközkonfigurációról: [Tudnivalók a VPN-eszközökről](vpn-gateway-about-vpn-devices.md).
* Győződjön meg arról, hogy rendelkezik egy kifelé irányuló, nyilvános IPv4-címmel a VPN-eszköz számára.
* Ha nem ismeri a helyszíni hálózati konfigurációjában található IP-címtereket, egyeztessen valakivel, aki ezeket az adatokat megadhatja Önnek. Amikor létrehozza ezt a konfigurációt, meg kell határoznia az IP-címtartományok előtagjait, amelyeket az Azure majd a helyszínre irányít. A helyszíni hálózat egyik alhálózata sem lehet átfedésben azokkal a virtuális alhálózatokkal, amelyekhez csatlakozni kíván.
* Jelenleg a megosztott kulcs megadásához és a VPN Gateway-kapcsolat létrehozásához a PowerShellre van szükség. Telepítse az Azure Service Management (SM) PowerShell-parancsmagjainak legújabb verzióját. További információt [az Azure PowerShell telepítésével és konfigurálásával](/powershell/azure/overview) foglalkozó témakörben talál. Ahhoz, hogy ezt a konfigurációt elvégezhesse, a PowerShellt rendszergazdaként kell futtatnia.

### <a name="values"></a>Konfigurációs mintaértékek ehhez a gyakorlathoz

A cikkben szereplő példák a következő értékeket használják. Ezekkel az értékekkel létrehozhat egy tesztkörnyezetet, vagy a segítségükkel értelmezheti a cikkben szereplő példákat.

* **VNet neve:** TestVNet1
* **Címtér:** 
  * 10.11.0.0/16
  * 10.12.0.0/16 (nem kötelező ehhez a gyakorlathoz)
* **Alhálózatok:**
  * FrontEnd 10.11.0.0/24
  * BackEnd 10.12.0.0/24 (nem kötelező ehhez a gyakorlathoz)
* **GatewaySubnet** 10.11.255.0/27
* **Erőforráscsoport:** TestRG1
* **Helyen** East US
* **DNS-kiszolgáló:** 10.11.0.3 (nem kötelező ehhez a gyakorlathoz)
* **Helyi hely neve:** Site2
* **Ügyfél címterület:** A helyszíni helyen található címterület.

## <a name="CreatVNet"></a>1. Virtuális hálózat létrehozása

Amikor helyek közötti használatra hoz létre virtuális hálózatot, gondoskodnia kell róla, hogy a megadott címterek ne legyenek átfedésben az azon helyi helyekhez tartozó ügyfélcímterekkel, amelyekhez csatlakozni szeretne. Egymást átfedő alhálózatok esetén a kapcsolat nem fog megfelelően működni.

* Ha már rendelkezik egy virtuális hálózattal, győződjön meg arról, hogy a beállításai kompatibilisek a VPN-átjáró kialakításával. Különösen ügyeljen az esetleges olyan alhálózatokra, amelyek átfedésbe kerülhetnek más hálózatokkal. 

* Ha még nem rendelkezik virtuális hálózattal, akkor hozzon létre egyet. A képernyőképek csak példaként szolgálnak. Ne felejtse el ezeket az értékeket a saját értékeire cserélni.

### <a name="to-create-a-virtual-network"></a>Virtuális hálózat létrehozása

1. Egy böngészőből keresse fel az [Azure portált](https://portal.azure.com), majd jelentkezzen be az Azure-fiókjával, ha szükséges.
2. Kattintson*a * + erőforrás létrehozása*elemre. A **Piactér keresése** mezőbe írja be a „Virtuális hálózat” kifejezést. A visszaadott listában keresse meg a **Virtuális hálózat** elemet, és rákattintva nyissa meg a **Virtuális hálózat** lapot.
3. kattintson **a (klasszikusra váltás)** elemre, majd kattintson a **Létrehozás**gombra.
4. A **Virtuális hálózat létrehozása (klasszikus)** lapon konfigurálja a virtuális hálózat beállításait. Ezen a lapon adhatja hozzá az első címterét és egy önálló alhálózati címtartományt. A virtuális hálózat létrehozása után visszaléphet, és további alhálózatokat és címtereket vehet fel.

   ![Virtuális hálózat létrehozása lap](./media/vpn-gateway-howto-site-to-site-classic-portal/createvnet.png "Virtuális hálózat létrehozása lap")
5. Ellenőrizze, hogy a megfelelő előfizetés jelenik-e meg az **Előfizetés** résznél. Az előfizetéseket a legördülő menüben módosíthatja.
6. Kattintson az **Erőforráscsoport** elemre, és válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy újat egy név beírásával. További információ az erőforráscsoportokkal kapcsolatban: [Azure Resource Manager Overview](../azure-resource-manager/resource-group-overview.md#resource-groups) (Az Azure Resource Manager áttekintése).
7. Ezután válassza ki a virtuális hálózatához tartozó **Hely** beállításokat. Ez a hely határozza meg a VNeten üzembe helyezett erőforrások helyét.
8. A VNet létrehozásához kattintson a **Létrehozás** gombra.
9. A Létrehozás gombra kattintva létrejön egy csempe az irányítópulton, amely a virtuális hálózat állapotát mutatja. A virtuális hálózat létrejöttével a csempe is módosul.

## <a name="additionaladdress"></a>2. További címterek felvétele

Miután létrehozta a virtuális hálózatot, hozzáadhat további címtereket. A további címterek felvétele nem kötelező lépése az S2S-konfigurációnak, de ha több címtérre van szüksége, kövesse az alábbi lépéseket:

1. Keresse meg a virtuális hálózatot a portálon.
2. A virtuális hálózat lapjának **Beállítások** szakasza alatt kattintson a **Címtér** elemre.
3. A Címtér lapon kattintson a **+Hozzáadás** gombra, és adjon meg egy címteret.

## <a name="dns"></a>3. DNS-kiszolgáló megadása

A DNS-beállítások megadása nem kötelező lépése az S2S-konfigurációnak, de a névfeloldáshoz szükség van DNS-re. Az érték megadásával nem jön létre új DNS-kiszolgáló. A megadott DNS-kiszolgáló IP-címének olyan DNS-kiszolgálónak kell lennie, amely fel tudja oldani azoknak az erőforrásoknak a nevét, amelyekkel Ön kapcsolatot fog létesíteni. A példabeállításokban egy magánhálózati IP-címet használtunk. A használt IP-cím valószínűleg nem az Ön DNS-kiszolgálójának IP-címe. Ügyeljen arra, hogy a saját értékeit használja.

Miután létrehozta a virtuális hálózatot, hozzáadhatja a DNS-kiszolgáló IP-címét a névfeloldás kezelésének érdekében. Nyissa meg a virtuális hálózat beállításait, kattintson a DNS-kiszolgálókra, majd adja hozzá a névfeloldáshoz használni kívánt DNS-kiszolgáló IP-címét.

1. Keresse meg a virtuális hálózatot a portálon.
2. A virtuális hálózat lapjának **Beállítások** szakasza alatt kattintson a **DNS-kiszolgálók** elemre.
3. Adjon meg egy DNS-kiszolgálót.
4. A beállítások mentéséhez kattintson a lap tetején található **Mentés** gombra.

## <a name="localsite"></a>4. A helyi hely konfigurálása

A helyi hely általában a használat helyszínét jelenti. Tartalmazza azon VPN-eszköz IP-címét, amelyhez kapcsolatot szeretne létesíteni, valamint azokat az IP-címtartományokat, amelyek a VPN-átjárón keresztül a VPN-eszközre lesznek irányítva.

1. A VNet lapjának **Beállítások**területén kattintson a **diagram**elemre.
1. A **VPN-kapcsolatok** lapon kattintson **a nem rendelkezik meglévő VPN-kapcsolatokkal lehetőségre. Kattintson ide a kezdéshez**.
1. A **kapcsolat típusa**beállításnál hagyja kiválasztva a **helyek** közötti helyet.
4. A **Helyi hely** lap megnyitásához kattintson a **Helyi hely – Kötelező beállítások megadása** elemre. Konfigurálja a beállításokat, majd kattintson az **OK** gombra a beállítások mentéséhez.
   - **név:** Hozzon létre egy nevet a helyi hely számára, hogy könnyen azonosítható legyen.
   - **VPN-átjáró IP-címe:** Ez a helyszíni hálózathoz tartozó VPN-eszköz nyilvános IP-címe. A VPN-eszköznek nyilvános IPv4 IP-címmel kell rendelkeznie. Adjon meg egy érvényes nyilvános IP-címet azon VPN-eszköznek, amelyhez csatlakozni kíván. Az Azure-nak elérhetőnek kell lennie. Ha nem ismeri a VPN-eszköz IP-címét, használhat egy helyőrző értéket (érvényes nyilvános IP-cím formátumban), amelyet később módosíthat.
   - **Ügyfél címterület:** A helyi helyszíni hálózatra ezen az átjárón keresztül átirányítani kívánt IP-címtartományok listázása. Több címtartományt is felvehet. Ügyeljen arra, hogy az itt megadott tartományok ne legyenek átfedésben olyan egyéb hálózatok tartományaival, amelyekhez a virtuális hálózat csatlakozik, illetve magának a virtuális hálózatnak a címtartományaival.

   ![Helyi hely](./media/vpn-gateway-howto-site-to-site-classic-portal/localnetworksite.png "Helyi hely konfigurálása")

A helyi webhely oldal bezárásához kattintson **az OK** gombra. **Az új VPN-kapcsolat lap bezárásához kattintson az OK**gombra.

## <a name="gatewaysubnet"></a>5. Az átjáróalhálózat konfigurálása

Létre kell hoznia egy átjáróalhálózatot a VPN-átjáróhoz. Az átjáróalhálózat tartalmazza a VPN-átjáró szolgáltatásai által használt IP-címeket.


1. Az **Új VPN-kapcsolat** lapon jelölje be az **Átjáró azonnali létrehozása** jelölőnégyzetet. Megjelenik az „Átjáró opcionális konfigurálása” lap. Ha nem jelöli be a jelölőnégyzetet, nem jelenik meg az átjáróalhálózat konfigurálását lehetővé tevő lap.

   ![Átjáró konfigurálása – Alhálózat, méret, útválasztási típus](./media/vpn-gateway-howto-site-to-site-classic-portal/optional.png "Átjáró konfigurálása – Alhálózat, méret, útválasztási típus")
2. Az **Átjáró konfigurálása** lap megnyitásához kattintson az **Átjáró opcionális konfigurálása – Alhálózat, méret és útválasztási típus** elemre.
3. Az **Átjáró konfigurálása** lapon kattintson az **Alhálózat – Kötelező beállítások konfigurálása** elemre az **Alhálózat hozzáadása** lap megnyitásához. Ha végzett a beállítások konfigurálásával, kattintson **az OK**gombra.

   ![Átjáró konfigurációja – átjáróalhálózat](./media/vpn-gateway-howto-site-to-site-classic-portal/subnetrequired.png "Átjáró konfigurációja – átjáró-alhálózat")
4. Az **Alhálózat hozzáadása** lapon adja meg az átjáróalhálózatot. Az Ön által megadott átjáróalhálózat mérete a létrehozni kívánt VPN-átjárókonfigurációtól függ. Bár akár /29-es átjáróalhálózatot is létrehozhat, javasolt /27-eset vagy /28-asat használni. Ez nagyobb, több címet tartalmazó alhálózatot hoz létre. Nagyobb átjáróalhálózat használatával elegendő IP-cím áll rendelkezésre az esetleges jövőbeni konfigurációk megvalósításához.

   ![Átjáróalhálózat hozzáadása](./media/vpn-gateway-howto-site-to-site-classic-portal/addgwsubnet.png "Átjáróalhálózat hozzáadása")

## <a name="sku"></a>6. A termékváltozat és a VPN típusának megadása

1. Válassza ki az átjáró **méretét**. Ez az átjáró a virtuális hálózati átjáró létrehozásához használt termékváltozata. A klasszikus VPN-átjárók a régi (örökölt) átjáró-termékváltozatokat használják. Az átjárók örökölt termékváltozatairól [a virtuális hálózati átjárók termékváltozatainak (régi termékváltozatok) használatát bemutató](vpn-gateway-about-skus-legacy.md) cikkben talál további információt.

   ![A termékváltozat és a VPN típusának kiválasztása](./media/vpn-gateway-howto-site-to-site-classic-portal/sku.png "A termékváltozat és a VPN típusának kiválasztása")
2. Válassza ki az átjáró **útválasztási típusát**. Ez VPN-típus néven is ismert. Fontos, hogy a megfelelő típust válassza, mert az átjárót nem lehet egyik típusról a másikra konvertálni. A VPN-eszköznek kompatibilisnek kell lennie a kiválasztott útválasztási típussal. További információ az útválasztási típusról: [Tudnivalók a VPN Gateway beállításairól](vpn-gateway-about-vpn-gateway-settings.md#vpntype). A „RouteBased” és „PolicyBased” VPN-típusokkal további cikkek is foglalkozhatnak. A „Dynamic” (Dinamikus) a „RouteBased”, a „Static” (Statikus) a „PolicyBased” típusra utal.
3. Kattintson az **OK** gombra a beállítások mentéséhez.
4. Az **új VPN-kapcsolat** lapon kattintson az **OK** gombra a lap alján a virtuális hálózati átjáró üzembe helyezésének megkezdéséhez. A kiválasztott SKU függvényében egy virtuális hálózati átjáró létrehozása akár 45 percet is igénybe vehet.

## <a name="vpndevice"></a>7. VPN-eszköz konfigurálása

A helyszíni hálózaton a helyek közötti kapcsolatok létesítéséhez VPN-eszközre van szükség. Ebben a lépésben a VPN-eszköz konfigurálása következik. A VPN-eszköz konfigurálásakor a következőkre van szüksége:

- Megosztott kulcs. Ez ugyanaz a megosztott kulcs, amelyet a helyek közötti VPN-kapcsolat létrehozásakor ad meg. A példákban alapvető megosztott kulcsot használunk. Javasoljuk egy ennél összetettebb kulcs létrehozását.
- A virtuális hálózati átjáró nyilvános IP-címe. A nyilvános IP-címet az Azure Portalon, valamint a PowerShell vagy a CLI használatával is megtekintheti.

[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

## <a name="CreateConnection"></a>8. A kapcsolat létrehozása
Ebben a lépésben beállítja a megosztott kulcsot, és létrehozza a kapcsolatot. A beállított kulcsnak meg kell egyeznie a VPN-eszköz konfigurációjához használt kulccsal.

> [!NOTE]
> Ez a lépés jelenleg az Azure Portalon nem érhető el. A műveletekhez az Azure PowerShell-parancsmagjainak Service Management (SM) verzióját kell használnia. A parancsmagok telepítésének megkezdése [előtt](#before) olvassa el a következő témakört:.
>

### <a name="step-1-connect-to-your-azure-account"></a>1\.lépés Csatlakozás az Azure-fiókhoz

1. Nyissa meg emelt szintű jogosultságokkal a PowerShell konzolt, és csatlakozzon a fiókjához. A következő példa segít a kapcsolódásban:

   ```powershell
   Add-AzureAccount
   ```
2. Keresse meg a fiókot az előfizetésekben.

   ```powershell
   Get-AzureSubscription
   ```
3. Ha egynél több előfizetéssel rendelkezik, akkor válassza ki azt, amelyiket használni szeretné.

   ```powershell
   Select-AzureSubscription -SubscriptionId "Replace_with_your_subscription_ID"
   ```

### <a name="step-2-set-the-shared-key-and-create-the-connection"></a>2\.lépés A megosztott kulcs beállítása és a kapcsolat létrehozása

A PowerShell és a klasszikus üzemi modell használatakor a portálon található erőforrások neve néha nem egyezik azzal, amelyet az Azure elvár a PowerShell használatakor. Az alábbi lépések segítségével exportálhatja a hálózati konfigurációs fájlt a nevek pontos értékeinek lekéréséhez. Ezeket a parancsokat helyileg kell futtatni a PowerShell Service Management modul használatával. A Service Management szolgáltatásra való váltáshoz használja a következő parancsot:

```powershell
azure config mode asm
```

1. Hozzon létre egy könyvtárat a számítógépén, majd exportálja a hálózati konfigurációs fájlt a könyvtárba. Ebben a példában a hálózati konfigurációs fájlt a C:\AzureNet helyre exportálja.

   ```powershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
2. Nyissa meg a hálózati konfigurációs fájlt egy xml-szerkesztővel, majd ellenőrizze a „LocalNetworkSite name” és a „VirtualNetworkSite name” értékeit. Módosítsa úgy a példát, hogy tükrözze a szükséges értékeket. Amikor szóközt tartalmazó nevet ad meg, zárja az értéket szimpla idézőjelek közé.

3. Állítsa be a megosztott kulcsot, és hozza létre a kapcsolatot. A „-SharedKey” egy Ön által létrehozott és megadott érték. A példában az „abc123” értéket használtuk, de érdemesebb egy ennél összetettebb értéket használni. Vegye figyelembe, hogy az itt megadott értéknek meg kell egyeznie a VPN-eszköz konfigurálásakor meghatározott értékkel.

   ```powershell
   Set-AzureVNetGatewayKey -VNetName 'Group TestRG1 TestVNet1' `
   -LocalNetworkSiteName 'D1BFC9CB_Site2' -SharedKey abc123
   ```
   A csatlakozás létrehozásakor az eredmény a következőket eredményezi: **Állapota Sikeres**művelet.

## <a name="verify"></a>9. A kapcsolat ellenőrzése

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

Ha nem tud csatlakozni, tekintse át a **Hibaelhárítás** című szakaszt a tartalomjegyzékben a bal oldali panelen.

## <a name="reset"></a>VPN-átjáró alaphelyzetbe állítása

Az Azure VPN Gateway alaphelyzetbe állítása akkor hasznos, ha egy vagy több helyek közötti VPN-alagúton elveszíti a létesítmények közötti VPN-kapcsolatot. Ebben az esetben a helyszíni VPN-eszközei megfelelően működnek, de nem tudnak Ipsec-alagutakat létesíteni az Azure VPN Gateway átjárókkal. A lépéseket lásd: [VPN Gateway alaphelyzetbe állítása](vpn-gateway-resetgw-classic.md#resetclassic).

## <a name="changesku"></a>Az átjárók termékváltozatainak módosítása

Az átjárók termékváltozatainak módosításához szükséges lépésekért lásd: [Átjáró-termékváltozatok átméretezése](vpn-gateway-about-SKUS-legacy.md#classicresize).

## <a name="next-steps"></a>További lépések

* Miután a kapcsolat létrejött, hozzáadhat virtuális gépeket a virtuális hálózataihoz. További információkért lásd: [Virtuális gépek](https://docs.microsoft.com/azure/).
* Információk a kényszerített bújtatásról: [Információk a kényszerített bújtatásról](vpn-gateway-about-forced-tunneling.md).
