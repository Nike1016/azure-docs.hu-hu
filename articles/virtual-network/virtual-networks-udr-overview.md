---
title: Azure-beli virtuális hálózati forgalom útválasztása
titlesuffix: Azure Virtual Network
description: Megtudhatja, hogyan irányítja az Azure a virtuális hálózatok forgalmának útválasztását, és hogyan szabható testre az Azure útválasztása.
services: virtual-network
documentationcenter: na
author: malopMSFT
manager: ''
editor: v-miegge
tags: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/26/2017
ms.author: malop
ms.reviewer: kumud
ms.openlocfilehash: b26f876fbe07b1667a579fc040562f1d6ee8a85e
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/15/2019
ms.locfileid: "67871797"
---
# <a name="virtual-network-traffic-routing"></a>Virtuális hálózat forgalmának útválasztása

Megtudhatja, hogyan irányítja az Azure az Azure, a helyszíni és az internetes erőforrások közötti forgalom útválasztását. Az Azure automatikusan létrehoz egy útvonaltáblát az Azure-beli virtuális hálózatokon lévő összes alhálózathoz, és a rendszer alapértelmezett útvonalait hozzáadja az útvonaltáblához. További információ a virtuális hálózatokról és alhálózatokról: [A virtuális hálózat áttekintése](virtual-networks-overview.md). Az Azure rendszerútvonalait [egyéni útvonalakkal](#custom-routes) írhatja felül, és további egyéni útvonalakat adhat az útvonaltáblákhoz. Az Azure az alhálózatokról kimenő forgalom útválasztását az alhálózat útvonaltáblájában szereplő útvonalak alapján irányítja.

## <a name="system-routes"></a>Rendszerútvonalak

Az Azure automatikusan hoz létre rendszerútvonalakat, és a virtuális hálózatban lévő egyes alhálózatokhoz rendeli az útvonalakat. Nem hozhat létre és nem távolíthat el rendszerútvonalakat, de felülbírálhat egyes rendszerútvonalakat [egyéni útvonalakkal](#custom-routes). Az Azure alapértelmezett rendszerútvonalakat hoz létre mindegyik alhálózathoz, és további [választható alapértelmezett útvonalakat](#optional-default-routes) ad adott alhálózatokhoz vagy minden alhálózathoz, amikor bizonyos Azure-képességeket használ.

### <a name="default"></a>Alapértelmezett

Mindegyik útvonal tartalmaz egy címelőtagot és a következő ugrás típusát. Amikor az egyik alhálózatot elhagyó forgalom egy útvonal címelőtagjában lévő IP-címre irányul, az Azure az előtagot tartalmazó útvonalat használja. További információ arról, [hogyan választ útvonalat az Azure](#how-azure-selects-a-route), amikor több útvonal ugyanazokat az előtagokat tartalmazza, illetve átfedésben van. Virtuális hálózat létrehozásakor az Azure automatikusan létrehozza a következő alapértelmezett rendszerútvonalakat a virtuális hálózatban lévő összes alhálózathoz:

|Source |Címelőtagok                                        |A következő ugrás típusa  |
|-------|---------                                               |---------      |
|Alapértelmezett|Egyedi a virtuális hálózaton                           |Virtuális hálózat|
|Alapértelmezett|0.0.0.0/0                                               |Internet       |
|Alapértelmezett|10.0.0.0/8                                              |Nincsenek           |
|Alapértelmezett|192.168.0.0/16                                          |None           |
|Alapértelmezett|100.64.0.0/10                                           |Nincsenek           |

Az előző táblában szereplő következő ugrástípusok azt jelölik, hogyan irányítja az Azure a listában szereplő címelőtagokra irányuló forgalmat. Itt a következő ugrás típusainak magyarázatait láthatja:

* **Virtuális hálózat**: Átirányítja a forgalmat a címtartomány között egy virtuális hálózat [címterület](manage-virtual-network.md#add-or-remove-an-address-range) területén belül. Az Azure létrehoz egy útvonalat a virtuális hálózat címterében meghatározott egyes címtartományoknak megfelelő címelőtaggal. Ha egy virtuális hálózat címteréhez több címtartomány van meghatározva, az Azure egyéni útvonalat hoz létre mindegyik címtartományhoz. Az Azure automatikusan irányítja az alhálózatok közötti forgalmat az egyes címtartományokhoz létrehozott útvonalakkal. Nem kell meghatároznia átjárókat az Azure-nak az alhálózatok közötti forgalom irányításához. Bár a virtuális hálózatok alhálózatokat tartalmaznak, és mindegyik alhálózat meghatározott címtartománnyal rendelkezik, az Azure *nem* hoz létre alapértelmezett útvonalakat az alhálózati címtartományokhoz, mert mindegyik alhálózati címtartomány egy virtuális hálózat címterének címtartományán belül van.<br>
* **Internet**: A címnek az internetre való továbbítása által megadott forgalmat irányítja. A rendszer alapértelmezett útvonala a 0.0.0.0/0 címelőtagot határozza meg. Ha nem bírálja felül az Azure alapértelmezett útvonalait, az Azure az internetre irányítja a virtuális hálózaton címtartomány által nem meghatározott összes cím forgalmát, egy kivétellel. Ha a cél címe egy Azure-szolgáltatáshoz tartozik, az Azure közvetlenül a szolgáltatásra irányítja a forgalmat az Azure gerinchálózatán keresztül, nem pedig az internetre. Az Azure-szolgáltatások közötti forgalom attól függetlenül sem halad át az interneten, hogy melyik Azure-régióban van a virtuális hálózat, vagy hogy mely Azure-régióban van üzembe helyezve az Azure-szolgáltatás példánya. Az Azure 0.0.0.0/0 címelőtaghoz tartozó alapértelmezett rendszerútvonalát felülbírálhatja egy [egyéni útvonallal](#custom-routes).<br>
* **Nincs**: A **nincs** következő ugrási típus felé irányuló forgalmat a rendszer nem az alhálózaton kívülre irányítja. Az Azure automatikusan alapértelmezett útvonalakat hoz létre a következő címelőtagokhoz:<br>

    * **10.0.0.0/8 és 192.168.0.0/16**: Privát használatra fenntartva az RFC 1918-ben.<br>
    * **100.64.0.0/10**: Az RFC 6598-ben foglalt.

    Ha az előző címtartományok valamelyikét egy virtuális hálózat címteréhez rendeli, az Azure **Nincs** értékről automatikusan **Virtuális hálózat** értékre módosítja az útvonal következő ugrási típusát. Ha egy olyan címtartományt rendel egy virtuális hálózat címteréhez, amely tartalmazza a négy fenntartott címelőtag egyikét, de nem egyezik meg velük, az Azure eltávolítja az előtag útvonalát, és hozzáadja az Ön által hozzáadott címelőtag útvonalát a **Virtuális hálózat** következő ugrási típussal.

### <a name="optional-default-routes"></a>Választható alapértelmezett útvonalak

Az Azure további alapértelmezett rendszerútvonalakat ad hozzá a különböző Azure-képességekhez, de csak ha engedélyezi a képességeket. A képességtől függően az Azure választható alapértelmezett útvonalakat ad hozzá a virtuális hálózatban lévő adott alhálózatokhoz vagy egy virtuális hálózat összes alhálózatához. Az Azure a következő további rendszerútvonalakat és következő ugrási típusokat adhatja hozzá a különböző képességek engedélyezésekor:

|Source                 |Címelőtagok                       |A következő ugrás típusa|Virtuális hálózatban lévő alhálózat, amelyhez a rendszer hozzáadja az útvonalat|
|-----                  |----                                   |---------                    |--------|
|Alapértelmezett                |Egyedi a virtuális hálózaton, például: 10.1.0.0/16|Társviszony létesítése virtuális hálózatok között                 |Összes|
|Virtuális hálózati átjáró|A helyszínről BGP-n keresztül meghirdetett vagy a helyi hálózati átjárón konfigurált előtagok     |Virtuális hálózati átjáró      |Összes|
|Alapértelmezett                |Többszörös                               |VirtualNetworkServiceEndpoint|Csak az az alhálózat, amelyhez a szolgáltatásvégpont engedélyezve van.|

* **Virtuális hálózat (VNet)** társítása: Ha két virtuális hálózat között hoz létre virtuális hálózatot, a rendszer minden egyes címtartomány számára létrehoz egy útvonalat, amely az egyes virtuális hálózatok közötti címtartomány-társítást hozza létre. További információk a [virtuális hálózati társviszonyokról](virtual-network-peering-overview.md).<br>
* **Virtuális hálózati átjáró**: Egy vagy több, a következő ugrási típusú *virtuális hálózati átjáróval* rendelkező útvonal hozzáadásakor a rendszer hozzáad egy virtuális hálózati átjárót egy virtuális hálózathoz. A forrás is *virtuális hálózati átjáró*, mert az átjáró hozzáadja az útvonalakat az alhálózathoz. Ha a helyszíni hálózati átjáró Border Gateway Protocol- ([BGP-](#border-gateway-protocol)) útvonalakat cserél egy Azure-beli virtuális hálózati átjáróval, a rendszer hozzáad egy útvonalat a helyszíni hálózati átjáróról propagált összes útvonalhoz. Ajánlott a lehető legnagyobb címtartományokba összegezni a helyszíni útvonalakat, hogy a lehető legkevesebb útvonal legyen Azure-beli virtuális hálózati átjárókba propagálva. Az Azure-beli virtuális hálózati átjárókra propagálható útvonalak száma korlátozott. További részletek: [Az Azure korlátai](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits).<br>
* **VirtualNetworkServiceEndpoint**: Bizonyos szolgáltatások nyilvános IP-címeit az Azure adja hozzá az útválasztási táblázathoz, amikor engedélyezi egy szolgáltatás-végpontot a szolgáltatás számára. A szolgáltatásvégpontok a virtuális hálózatokon lévő egyéni alhálózatokhoz engedélyezve vannak, ezért az útvonal csak olyan alhálózat útvonaltáblázatához lesz hozzáadva, amelyhez a szolgáltatásvégpont engedélyezve van. Az Azure-szolgáltatások nyilvános IP-címei rendszeresen változnak. Az Azure automatikusan kezeli az útvonaltáblában lévő címeket a címek változásakor. További információk a [virtuális hálózati szolgáltatásvégpontokról](virtual-network-service-endpoints-overview.md) és azokról a szolgáltatásokról, amelyekhez szolgáltatásvégpontokat hozhat létre.<br>

    > [!NOTE]
    > A **Virtuális hálózatok közötti társviszony** és a **VirtualNetworkServiceEndpoint** következő ugrási típusokat csak az Azure Resource Manager-alapú üzemi modellel létrehozott virtuális hálózatokban lévő alhálózatok útvonaltábláihoz adja hozzá a rendszer. A következő ugrási típusokat a rendszer nem adja hozzá a klasszikus üzemi modellel létrehozott virtuális hálózati alhálózatokkal társított útvonaltáblákhoz. További információ az Azure [üzemi modellekről](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="custom-routes"></a>Egyéni útvonalak

Egyéni útvonalak létrehozásához hozzon létre [felhasználó által megadott](#user-defined) útvonalakat, vagy cseréljen [Border Gateway Protocol-](#border-gateway-protocol) (BGP-) útvonalakat a helyszíni hálózati átjáró és egy Azure-beli virtuális hálózati átjáró között. 

### <a name="user-defined"></a>Felhasználó által megadott

Egyéni vagy felhasználó által megadott útvonalakat hozhat létre az Azure-ban az Azure alapértelmezett rendszerútvonalainak felülbírálásához, vagy hogy további útvonalakat adjon egy alhálózat útvonaltáblájához. Az Azure-ban először egy útvonaltáblát kell létrehoznia, amelyet nulla vagy több virtuális hálózati alhálózattal társít. Mindegyik alhálózattal nulla vagy egy útvonaltábla társítható. További információ az egy útvonaltáblához adható útvonalak maximális számáról és az Azure-előfizetésenként létrehozható, felhasználó által megadott útvonaltáblák maximális számáról: [Az Azure korlátai](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits). Ha létrehoz és alhálózattal társít egy útvonaltáblát, a benne lévő útvonalak felülbírálják az Azure által alapértelmezés szerint az alhálózathoz adott alapértelmezett útvonalakat, illetve egyesülnek velük.

Felhasználó által megadott útvonal létrehozásakor az alábbi következő ugrási típusokat adhatja meg:

* **Virtuális berendezés**: A virtuális készülékek olyan virtuális gépek, amelyek általában egy hálózati alkalmazást, például egy tűzfalat futtatnak. A virtuális hálózaton üzembe helyezhető különböző előre konfigurált hálózati virtuális berendezésekről az [Azure Marketplace-en](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances) talál további információt. Amikor létrehoz egy **virtuális berendezés** ugrástípusú útvonalat, a következő ugrás IP-címét is meghatározza. Az IP-cím a következő lehet:

    * Egy virtuális géphez csatolt hálózati adapter [magánhálózati IP-címe](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses). Minden olyan, virtuális géphez csatolt hálózati adapterhez, amely nem a saját címére továbbít hálózati forgalmat, engedélyezve kell lennie az Azure *IP-továbbításnak*. A beállítás letiltja a hálózati adapter forrásának és céljának az Azure által végzett ellenőrzését. További információ [a hálózati adapterek IP-továbbításának engedélyezéséről](virtual-network-network-interface.md#enable-or-disable-ip-forwarding). Az *IP-továbbítás engedélyezése* az Azure beállítása, azonban lehet, hogy a virtuális gép operációs rendszerén is engedélyeznie kell az IP-továbbítást, hogy a berendezés továbbítani tudja a forgalmat az Azure hálózati adaptereihez rendelt magánhálózati IP-címek között. Ha a berendezés kénytelen nyilvános IP-címre irányítani a forgalmat, ezt vagy proxykapcsolaton keresztül kell megtennie, vagy a forrás magánhálózati IP-címét a saját magánhálózati IP-címére kell fordítania, amelyet ezt követően az Azure nyilvános IP-címre fordít, mielőtt a forgalmat továbbküldené az internetre. A virtuális gépen belüli kötelező beállítások meghatározásához tekintse meg az operációs rendszer vagy a hálózati alkalmazás dokumentációját. Az Azure kimenő kapcsolatainak ismertetéséhez lásd a [Kimenő kapcsolatok áttekintése](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json) című témakört.<br>

        > [!NOTE]
        > A virtuális berendezéseket ne azon az alhálózaton helyezze üzembe, amelyen a virtuális berendezések keresztül irányított erőforrások üzemelnek. Ha a virtuális berendezést ugyanazon az alhálózaton helyezi üzembe, és útvonaltáblát alkalmaz az alhálózatra, amely a virtuális berendezésen keresztül irányítja a forgalmat, útválasztási hurkok alakulnak ki, és a forgalom soha nem hagyja el az alhálózatot.

    * Azure-beli [belső terheléselosztó](../load-balancer/load-balancer-get-started-ilb-arm-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) magánhálózati IP-címe. A terheléselosztókat gyakran használják [a hálózati virtuális berendezések magas rendelkezésre állási stratégiájának](/azure/architecture/reference-architectures/dmz/nva-ha?toc=%2fazure%2fvirtual-network%2ftoc.json) részeként.

    Meghatározhat egy útvonalat a 0.0.0.0/0 címelőtaggal és a virtuális berendezés következő ugrási típussal, így a berendezés megvizsgálhatja a forgalmat, és meghatározhatja, hogy továbbítja vagy eldobja-e. Ha felhasználó által megadott, a 0.0.0.0/0 címelőtagot tartalmazó útvonalat kíván létrehozni, olvassa el először a [0.0.0.0/0 címelőtag](#default-route) témakört.

* **Virtuális hálózati átjáró**: Itt adhatja meg, hogy a virtuális hálózati átjáróra átirányított megadott címek előtagjaira irányuló forgalom mikor legyen elérhető. A virtuális hálózati átjárót a **VPN** típussal kell létrehozni. Felhasználó által definiált útvonalon nem adhat meg **ExpressRoute** típusú virtuális hálózati átjárót, mert a ExpressRoute esetében a BGP-t egyéni útvonalakhoz kell használnia. Meghatározhat olyan útvonalat, amely a 0.0.0.0/0 címelőtag felé haladó adatforgalmat átirányítja egy [útvonalalapú](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#vpntype) virtuális hálózati átjáróhoz. Előfordulhat, hogy van a helyszínen egy olyan eszköz, amely ellenőrzi az adatforgalmat, és ennek alapján továbbítja vagy elejti azt. Ha felhasználó által megadott útvonalat szeretne létrehozni a 0.0.0.0/0 címelőtaghoz, olvassa el először a [0.0.0.0/0 címelőtag](#default-route) témakört. Ahelyett, hogy a 0.0.0.0/0 címelőtaghoz felhasználó által megadott útvonalat konfigurálna, meghirdethet egy 0.0.0.0/0 címelőtagú útvonalat a BGP-n keresztül, ha [engedélyezte a BGP számára a VPN virtuális hálózati átjárót](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json).<br>
* **Nincs**: Adja meg, hogy mikor kívánja eldobni a forgalmat egy adott címmé, és ne továbbítsa a forgalmat a célhelyre. Ha valamelyik funkciót még nem állította be teljesen, előfordulhat, hogy az Azure egyes opcionális rendszerútvonalakhoz a *Nincs* értéket állítja be. Ha például a **Következő ugrás IP-címe** értéke *Nincs*, és a **Következő ugrás típusa** *Virtuális hálózati átjáró* vagy *Virtuális berendezés*, akkor előfordulhat, hogy az eszköz nem üzemel, vagy nincs megfelelően beállítva. Az Azure rendszer [alapértelmezett útvonalakat](#default) hoz létre a fenntartott címelőtagokhoz, ahol a következő ugrás típusa **Nincs**.<br>
* **Virtuális hálózat**: Itt adhatja meg, hogy mikor szeretné felülbírálni az alapértelmezett útválasztást egy virtuális hálózaton belül. Lásd az [Útválasztási példát](#routing-example), amely azt szemlélteti, miért lehet érdemes **Virtuális hálózat** ugrástípussal létrehozni egy útvonalat.<br>
* **Internet**: Itt adhatja meg, hogy a rendszer explicit módon irányítsa-e a forgalmat az internetre, vagy ha azt szeretné, hogy a nyilvános IP-címekkel rendelkező Azure-szolgáltatások felé irányuló forgalom az Azure-beli gerinc hálózatán belül maradjon.

Felhasználó által megadott útvonalak esetén nem adhat meg **Virtuális hálózatok közötti társviszonyt** vagy **VirtualNetworkServiceEndpoint** típust a következő ugrás típusaként. **Virtuális hálózatok közötti társviszony** vagy **VirtualNetworkServiceEndpoint** következő ugrási típusú útvonalakat az Azure csak akkor hoz létre, ha virtuális hálózati társviszonyt vagy szolgáltatásvégpontot konfigurál.

## <a name="next-hop-types-across-azure-tools"></a>Következő ugrási típusok az Azure-eszközök között

A következő ugrás típusaihoz megjelenített és hivatkozott név eltér az Azure Portal és a parancssori eszközök között, valamint az Azure Resource Manager és a klasszikus üzemi modellek között. A következő táblázat felsorolja a neveket, amelyekkel a különféle eszközök és az [üzembehelyezési modellek](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a következő ugrások típusaira hivatkoznak:

|A következő ugrás típusa                   |Azure CLI és PowerShell (Resource Manager) |Azure klasszikus parancssori felület és PowerShell (klasszikus)|
|-------------                   |---------                                       |-----|
|Virtuális hálózati átjáró         |VirtualNetworkGateway                           |VPNGateway|
|Virtuális hálózat                 |VNetLocal                                       |VNetLocal (nem érhető el a klasszikus parancssori felületen asm mód esetén)|
|Internet                        |Internet                                        |Internet (nem érhető el a klasszikus parancssori felületen asm mód esetén)|
|Virtuális berendezés               |VirtualAppliance                                |VirtualAppliance|
|None                            |Nincsenek                                            |Null (nem érhető el a klasszikus parancssori felületen asm mód esetén)|
|Társviszony létesítése virtuális hálózatok között         |Társviszony létesítése virtuális hálózatok között                                    |Nem alkalmazható|
|Virtuális hálózati szolgáltatásvégpont|VirtualNetworkServiceEndpoint                   |Nem alkalmazható|

### <a name="border-gateway-protocol"></a>Border Gateway Protocol

Egy helyszíni hálózati átjáró útvonalakat cserélhet egy Azure virtuális hálózati átjáróval a Border Gateway Protocol (BGP) használatával. A BGP Azure virtuális hálózati átjáróval történő használata függ az átjáró létrehozásakor kiválasztott típustól. Ha a következő típust választotta ki:

* **ExpressRoute:** A BGP-t kell használnia a helyszíni útvonalak a Microsoft Edge-útválasztóba való reklámozásához. Nem hozhat létre felhasználó által definiált útvonalakat a ExpressRoute virtuális hálózati átjáró felé irányuló forgalom kényszerítéséhez, ha a következő típusú virtuális hálózati átjárót helyezi üzembe: ExpressRoute. Az ExpressRoute forgalmának például egy hálózati virtuális berendezésre való kényszerítéséhez használhat felhasználó által megadott útvonalakat.<br>
* **VPN**: Igény szerint a BGP-t is használhatja. Részletes információért lásd a [BGP helyek közötti VPN-kapcsolatokkal](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) témakört.

Amikor a BGP használatával útvonalakat cserél az Azure-ban, a rendszer minden meghirdetett előtag esetében külön útvonalat ad hozzá a virtuális hálózat összes alhálózatának útvonaltáblájához. Az útvonal forrásaként és következő ugrásának típusaként *Virtuális hálózati átjáró* van feltüntetve. 

Az ABB és a VPN Gateway útvonal-propagálás le lehet tiltani egy alhálózaton egy útválasztási tábla tulajdonságának használatával. Ha a BGP használatával cseréli át az Azure-útvonalakat, az útvonalakat a rendszer nem adja hozzá az összes alhálózat útválasztási táblájához, és a virtuális hálózati átjáró útvonal-propagálás le van tiltva. A VPN-kapcsolatok következő ugrás típusú *virtuális hálózati átjáró* és [egyéni útvonalak](#custom-routes) használatával jönnek létre. Részletekért lásd: [virtuális hálózati átjáró útvonal-propagálásának letiltása](manage-route-table.md#create-a-route-table).

## <a name="how-azure-selects-a-route"></a>Az Azure útvonalválasztásának módja

Ha a kimenő adatforgalmat a rendszer egy alhálózatról küldi ki, az Azure kiválaszt egy útvonalat a cél IP-cím alapján, a leghosszabb előtag-megfeleltetési algoritmus használatával. Egy útválasztási táblázat például két útvonallal rendelkezik: Az egyik útvonal megadja a 10.0.0.0/24-előtagot, míg a másik útvonal megadja a 10.0.0.0/16 előtagot. Az Azure a 10.0.0.5 címre irányuló adatforgalmat az útvonalban meghatározott következő ugrástípushoz irányítja, amely a 10.0.0.0/24 előtaggal rendelkezik, mert a 10.0.0.0/24 hosszabb előtag, mint a 10.0.0.0/16, annak ellenére, hogy a 10.0.0.5 mindkét címelőtagba beletartozik. Az Azure a 10.0.1.5 címre irányuló adatforgalmat az útvonalban meghatározott következő ugrástípushoz irányítja, amely a 10.0.0.0/16 előtaggal rendelkezik, mert a 10.0.1.5 nincs benne a 10.0.0.0/24 címelőtagban, ezért a 10.0.0.0/16 címelőtagot tartalmazó útvonal a leghosszabb előtag, amelyik megfelel a feltételeknek.

Ha több útvonal is tartalmazza ugyanazt a címelőtagot, akkor az Azure a következő prioritási elv alapján választ az útvonaltípusok közül:

1. Felhasználó által megadott útvonal
1. BGP-útvonal
1. Rendszerútvonal

> [!NOTE]
> A virtuális hálózathoz, virtuális társhálózati viszonyhoz vagy virtuális hálózati szolgáltatásvégponthoz kapcsolódó forgalom rendszerútvonalai előnyben részesített útvonalak akkor is, ha a BGP-útvonalak pontosabbak.

Tegyük fel például, hogy egy útvonaltábla a következő útvonalakat tartalmazza:


|Source   |Címelőtagok  |A következő ugrás típusa           |
|---------|---------         |-------                 |
|Alapértelmezett  | 0.0.0.0/0        |Internet                |
|Felhasználó     | 0.0.0.0/0        |Virtuális hálózati átjáró |

Ha az adatforgalom olyan IP-címre irányul, amely nem tartozik bele az útvonaltáblában lévő címelőtagokba, az Azure a **Felhasználó** forrással rendelkező útvonalat választja ki, mert a felhasználó által megadott útvonalak magasabb prioritással bírnak, mint a rendszer alapértelmezés szerinti útvonalai.

Lásd az [útválasztási példát](#routing-example), amely egy részletes útvonaltáblát tartalmaz, a táblázatban előforduló útvonalakhoz fűzött magyarázattal.

## <a name="default-route"></a>0.0.0.0/0 címelőtag

A 0.0.0.0/0 címelőtagú útvonal utasítást ad az Azure-nak arra vonatkozóan, hogyan irányítsa az adatforgalmat, amelynek célja egy olyan IP-cím, amely kívül esik az alhálózat útvonaltáblájában szereplő útvonalainak címelőtagjain. Egy alhálózat létrehozásakor az Azure létrehoz egy [alapértelmezett](#default) útvonalat a 0.0.0.0/0 címelőtagra, ahol a következő ugrás típusa **Internet**. Ha nem bírálja felül ezt az útvonalat, az Azure minden adatforgalmat, amelynek a többi útvonal címelőtagját nem tartalmazó IP-cím a célja, az internetre irányít. Ez alól kivételt képez, amikor az Azure-szolgáltatások nyilvános IP-címeire irányuló adatforgalom az Azure gerinchálózatán belül marad, és nem halad tovább az internetre. Ha felülbírálja ezt az útvonalat egy [egyéni](#custom-routes) útvonallal, akkor az útvonaltáblában lévő többi útvonal címelőtagján kívül eső címekre irányuló adatforgalmat a rendszer egy hálózati virtuális berendezésre vagy virtuális hálózati átjáróra tereli, attól függően, hogy melyiket adja meg egy egyéni útvonalon belül.

Ha felülbírálja a 0.0.0.0/0 címelőtagot, akkor az alhálózatról a virtuális hálózati átjárón vagy virtuális berendezésen keresztül kimenő forgalom mellett a következő változások következhetnek be az Azure alapértelmezett útválasztásában: 

* Az Azure minden forgalmat az útvonalban megadott következő ugrási típus szerint küld ki. Ebbe beletartozik az Azure szolgáltatásainak nyilvános IP-címeire irányuló forgalom is. Amikor a 0.0.0.0/0 címelőtagú útvonal következő ugrásának típusa **Internet**, az alhálózatról érkező, az Azure-szolgáltatások nyilvános IP-címei felé irányuló adatforgalom sosem hagyja el az Azure gerinchálózatát. Ez független az Azure-régiótól, ahol a virtuális hálózat vagy az Azure-szolgáltatási erőforrás megtalálható. Ha azonban felhasználó által megadott vagy BGP-útvonalat hoz létre **Virtuális hálózati átjáró** vagy **Virtuális berendezés** következő ugrási típussal, minden adatforgalom (beleértve az Azure-szolgáltatások nyilvános IP-címeire irányuló adatforgalmat is, amelyhez nem engedélyezett [szolgáltatásvégpontokat](virtual-network-service-endpoints-overview.md)) az útvonalban megadott következő ugrási típusba kerül. Ha szolgáltatásvégpontot engedélyezett egy szolgáltatáshoz, az ahhoz vezető adatforgalom nincs következő ugrástípusra terelve egy 0.0.0.0/0 címelőtagú útvonalban, mert a szolgáltatás címelőtagjai a szolgáltatásvégpontok engedélyezésekor az Azure által létrehozott útvonalban vannak megadva, azok pedig hosszabbak, mint a 0.0.0.0/0.
* Már nem férhet közvetlenül hozzá az alhálózati erőforrásokhoz az internetről. Közvetett módon hozzáférhet az alhálózat erőforrásaihoz az internetről, ha a beérkező adatforgalom áthalad a következő ugrás típusa által megadott eszközön egy olyan útvonal esetében, amely a virtuális hálózatban lévő erőforrás elérését megelőzően a 0.0.0.0/0 címelőtaggal rendelkezik. Ha az útvonal a következő értékeket tartalmazza a következő ugrás típusára vonatkozóan:<br>

    * **Virtuális berendezés**: A készüléknek a következőket kell tennie:<br>

        * Elérhetőnek kell lennie az internetről;<br>
        * Rendelkeznie kell hozzárendelt nyilvános IP-címmel;<br>
        * Nem rendelkezhet olyan társított biztonságicsoport-szabállyal, amely megakadályozza a kommunikációt az eszközzel;<br>
        * Nem utasíthatja el a kommunikációt;<br>
        * Képesnek kell lennie a hálózati címek fordítására és továbbítására, vagy az adatforgalom a célerőforrásra történő átadására proxykapcsolaton keresztül, majd az internetre való visszairányítására.

    * **Virtuális hálózati átjáró**: Ha az átjáró egy ExpressRoute virtuális hálózati átjáró, a helyszíni internetkapcsolattal rendelkező eszköz hálózati címfordítást és továbbítást végez, vagy proxyként továbbítja a forgalmat az alhálózaton lévő cél erőforráshoz az ExpressRoute [privát](../expressroute/expressroute-circuit-peerings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#privatepeering)közvetítésével. 

Ha a virtuális hálózat egy Azure VPN-átjáróhoz csatlakozik, ne társítson útválasztási táblázatot olyan [átjáróalhálózathoz](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub), amely 0.0.0.0/0 célú útvonalat tartalmaz. Ellenkező esetben előfordulhat, hogy az átjárószolgáltatás nem működik megfelelően. Részletes információért lásd a *Miért vannak egyes portok nyitva a VPN-átjárómon?* kérdést a [VPN Gatewayről szóló gyakori kérdések](../vpn-gateway/vpn-gateway-vpn-faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gatewayports) között.

A [DMZ az Azure és a helyszíni adatközpont között](/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid?toc=%2fazure%2fvirtual-network%2ftoc.json) és a [DMZ az Azure és az internet között](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz?toc=%2fazure%2fvirtual-network%2ftoc.json) témakörökkel foglalkozó cikkekben talál további információt a megvalósításról, ha az Azure és az internet közötti kommunikációhoz virtuális hálózati átjárókat és virtuális berendezéseket szeretne használni.

## <a name="routing-example"></a>Útválasztási példa

A jelen cikk fogalmait bemutató alábbi szakaszok a következőket ismertetik:

* Forgatókönyv követelményekkel<br>
* A követelmények teljesítéséhez szükséges egyéni útvonalak<br>
* Az útvonaltábla, amely az egyik alhálózatra vonatkozik, és tartalmazza a követelmények teljesítéséhez szükséges alapértelmezett és egyéni útvonalakat

> [!NOTE]
> Ez a példa nem minősül javasolt vagy ajánlott eljárásnak. A célja mindössze az, hogy a jelen cikkben tárgyalt fogalmakat bemutassa.

### <a name="requirements"></a>Követelmények

1. Hozzon létre két virtuális hálózatot egy Azure-régión belül, és engedélyezze az erőforrások kommunikációját a virtuális hálózatok között.
1. Állítson be egy helyszíni hálózatot, amely biztonságosan kommunikál mindkét virtuális hálózattal az interneten keresztül, egy VPN-alagúton át. *Alternatív megoldásként egy ExpressRoute-kapcsolat is használható, de ebben a példában VPN-kapcsolatot használunk.*
1. Egy virtuális hálózaton belüli egyik alhálózat esetén:

    * Az Azure Storage-ba irányuló és az alhálózaton belüli forgalom kivételével minden kimenő adatforgalmat kényszerítsen egy hálózati virtuális berendezésen történő áthaladásra ellenőrzés és naplózás céljából.<br>
    * Ne ellenőrizze az alhálózaton belüli magánhálózati IP-címek közötti forgalmat; engedje, hogy az erőforrások között közvetlenül haladjon a forgalom.<br>
    * Ejtsen el minden kimenő forgalmat, amely a másik virtuális hálózatra irányul.<br>
    * Engedélyezze az Azure Storage-ba tartó kimenő adatforgalom közvetlen áramlását anélkül, hogy egy hálózati virtuális berendezésen való áthaladásra kényszerítené.

1. Engedélyezzen minden adatforgalmat az összes többi alhálózat és virtuális hálózat között.

### <a name="implementation"></a>Megvalósítás

A következő képen az Azure Resource Manager-alapú üzemi modellel történő megvalósítás látható, amely megfelel a fenti követelményeknek:

![Hálózati diagram](./media/virtual-networks-udr-overview/routing-example.png)

A nyilak az adatforgalom irányát jelzik. 

### <a name="route-tables"></a>Útvonaltáblák

#### <a name="subnet1"></a>Subnet1

A képen látható *Subnet1* alhálózat útvonaltáblája a következő útvonalakat tartalmazza:

|id  |Source |Állapot  |Címelőtagok    |A következő ugrás típusa          |A következő ugrás IP-címe|Felhasználó által megadott útvonal neve| 
|----|-------|-------|------              |-------                |--------           |--------      |
|1   |Alapértelmezett|Érvénytelen|10.0.0.0/16         |Virtuális hálózat        |                   |              |
|2   |Felhasználó   |Aktív |10.0.0.0/16         |Virtuális berendezés      |10.0.100.4         |Ezen belül: VNet1  |
|3   |Felhasználó   |Aktív |10.0.0.0/24         |Virtuális hálózat        |                   |Ezen belül: Subnet1|
|4   |Alapértelmezett|Érvénytelen|10.1.0.0/16         |Társviszony létesítése virtuális hálózatok között           |                   |              |
|5   |Alapértelmezett|Érvénytelen|10.2.0.0/16         |Társviszony létesítése virtuális hálózatok között           |                   |              |
|6   |Felhasználó   |Aktív |10.1.0.0/16         |Nincsenek                   |                   |Ide: VNet2-1-Elejtés|
|7   |Felhasználó   |Aktív |10.2.0.0/16         |Nincsenek                   |                   |Ide: VNet2-2-Elejtés|
|8   |Alapértelmezett|Érvénytelen|10.10.0.0/16        |Virtuális hálózati átjáró|[X.X.X.X]          |              |
|9   |Felhasználó   |Aktív |10.10.0.0/16        |Virtuális berendezés      |10.0.100.4         |A helyszínire    |
|10  |Alapértelmezett|Aktív |[X.X.X.X]           |VirtualNetworkServiceEndpoint    |         |              |
|11  |Alapértelmezett|Érvénytelen|0.0.0.0/0           |Internet               |                   |              |
|12  |Felhasználó   |Aktív |0.0.0.0/0           |Virtuális berendezés      |10.0.100.4         |Alapértelmezett-NVA   |

Az egyes útvonal-azonosítók magyarázata:

1. Az Azure automatikusan hozzáadta ezt az útvonalat a *Virtual-network-1* hálózaton lévő összes alhálózathoz, mert a 10.0.0.0/16 a virtuális hálózat címterében meghatározott egyetlen címtartomány. Ha az ID2 útvonalon nem jött létre a felhasználó által megadott útvonal, a 10.0.0.1 és a 10.0.255.254 közötti bármelyik címre küldött adatforgalom a virtuális hálózaton belül lesz irányítva, mert az előtag hosszabb, mint 0.0.0.0/0, és nincs a többi útvonal címelőtagjain belül. Az Azure automatikusan *Aktívról* *Érvénytelenre* módosította az állapotot az ID2 nevű, felhasználó által megadott útvonal hozzáadásakor, mert ugyanaz az előtagja, mint az alapértelmezett útvonalnak, és a felhasználó által megadott útvonalak felülbírálják az alapértelmezett útvonalakat. Az útvonal állapota továbbra is *Aktív* a *Subnet2* alhálózaton, mert az ID2 nevű, felhasználó által megadott útvonalat tartalmazó útvonaltábla nincs társítva a *Subnet2* alhálózattal.
2. Az Azure akkor adta hozzá ezt az útvonalat, amikor a 10.0.0.0/16 címelőtag felhasználó által megadott útvonala lett társítva a *Subnet1* alhálózattal a *Virtual-network-1* virtuális hálózaton. A felhasználó által megadott útvonal a 10.0.100.4 IP-címet határozza meg a virtuális berendezéshez, mert a cím a virtuális berendezés virtuális gépéhez rendelt magánhálózati IP-cím. Az ezen útvonalat tartalmazó útvonaltábla nincs társítva a *Subnet2* alhálózattal, ezért nem jelenik meg a *Subnet2* útvonaltáblájában. Ez az útvonal felülbírálja a 10.0.0.0/16 előtag (ID1) alapértelmezett útvonalát, amely automatikusan a virtuális hálózaton belüli 10.0.0.1 és 10.0.255.254 címre irányítja a forgalmat a virtuális hálózat következő ugrási típusán keresztül. Ez az útvonal a 3. [követelmény](#requirements) kielégítése érdekében létezik, hogy az összes kimenő forgalmat egy virtuális berendezésen keresztüli haladásra kényszerítsen.
3. Az Azure akkor adta hozzá ezt az útvonalat, amikor a 10.0.0.0/24 címelőtag felhasználó által megadott útvonala lett társítva a *Subnet1* alhálózattal. A 10.0.0.1 és 10.0.0.254 közötti címekre tartó forgalom az alhálózaton belül marad, nincs átirányítva az előző szabályban (ID2) meghatározott virtuális berendezésre, mert hosszabb az előtagja, mint az ID2 útvonalnak. Ez az útvonal nem lett társítva a *Subnet2* alhálózattal, ezért az útvonal nem jelenik meg a *Subnet2* útvonaltáblájában. Ez az útvonal felülbírálja a *Subnet1* alhálózaton lévő forgalom ID2 útvonalát. Ez az útvonal a 3. [követelmény](#requirements) kielégítése érdekében létezik.
4. Az Azure automatikusan hozzáadta az útvonalakat a *Virtual-network-1* hálózaton lévő összes alhálózat 4. és 5. azonosítójához, amikor a virtuális hálózat a *Virtual-network-2* hálózattal került társviszonyba. A *Virtual-Network-2* két címtartományt tartalmaz a címtartomány: 10.1.0.0/16 és 10.2.0.0/16, így az Azure minden tartományhoz hozzáadott egy útvonalat. Ha a 6. és 7. útvonal-azonosítóban lévő, felhasználó által megadott útvonalak nem jöttek volna létre, a 10.1.0.1–10.1.255.254 és 10.2.0.1–10.2.255.254 közötti címek a társviszonyban álló virtuális hálózatra lennének átirányítva, mert az előtag hosszabb, mint 0.0.0.0/0, és nincs a többi útvonal címelőtagjain belül. Az Azure automatikusan *Aktívról* *Érvénytelenre* módosította az állapotot a 6. és 7. azonosítóban lévő útvonalak hozzáadásakor, mert ugyanazok az előtagjaik, mint a 4. és 5. azonosító útvonalainak, és a felhasználó által megadott útvonalak felülbírálják az alapértelmezett útvonalakat. A 4. és az 5. azonosítójú útvonalak állapota továbbra is *aktív* a *subnet2 alhálózattal*esetében, mert a 6. és 7. azonosítóban szereplő felhasználó által megadott útvonalakat tartalmazó útválasztási táblázat nem a *subnet2 alhálózattal*van társítva. A virtuális hálózatok közötti társviszony az 1. [követelmény](#requirements) kielégítése érdekében jött létre.
5. Ugyanaz a magyarázat, mint az ID4 esetében.
6. Az Azure akkor adta hozzá ezt az útvonalat és az ID7 útvonalát, amikor a 10.1.0.0/16 és a 10.2.0.0/16 címelőtagok felhasználó által megadott útvonalai lettek társítva a *Subnet1* alhálózattal. A 10.1.0.1–10.1.255.254 és a 10.2.0.1–10.2.255.254 közötti címekre tartó forgalmat az Azure elejti ahelyett, hogy a virtuális társhálózatra irányítaná, mert a felhasználó által megadott útvonalak felülbírálják az alapértelmezett útvonalakat. Az útvonalak nem lettek társítva a *Subnet2* alhálózattal, ezért az útvonalak nem jelennek meg a *Subnet2* útvonaltáblájában. Az útvonalak felülbírálják a *Subnet1* alhálózatot elhagyó forgalom ID4 és ID5 útvonalait. Az ID6 és ID7 útvonalak a 3. [követelmény](#requirements) kielégítése érdekében léteznek, hogy a másik virtuális hálózatra érkező forgalmat a rendszer elejtse.
7. Ugyanaz a magyarázat, mint az ID6 esetén.
8. Az Azure automatikusan hozzáadta ezt az útvonalat a *Virtual-network-1* hálózaton lévő összes alhálózathoz, amikor VPN típusú virtuális hálózati átjáró létrejött a virtuális hálózaton. Az Azure hozzáadta a virtuális hálózati átjáró nyilvános IP-címét az útvonaltáblához. A 10.10.0.1 és 10.10.255.254 közötti címekre küldött forgalom a virtuális hálózati átjáróra van irányítva. Az előtag hosszabb, mint 0.0.0.0/0, és nincs a többi útvonal címelőtagjain belül. A virtuális hálózati átjáró a 2. [követelmény](#requirements) kielégítése érdekében jött létre.
9. Az Azure akkor adta hozzá ezt az útvonalat, amikor a 10.10.0.0/16 címelőtag felhasználó által megadott útvonala a *Subnet1* alhálózattal társított útvonaltáblába került. Ez az útvonal felülbírálja az ID8 útvonalat. Az útvonal a helyszíni hálózatra tartó összes forgalmat egy hálózati virtuális berendezésre (NVA-ra) küldi vizsgálatra ahelyett, hogy közvetlenül a helyszínre irányítaná a forgalmat. Az útvonal a 3. [követelmény](#requirements) kielégítése érdekében jött létre.
10. Az Azure akkor adta hozzá automatikusan ezt az útvonalat az alhálózathoz, amikor egy Azure-szolgáltatás szolgáltatásvégpontját engedélyezték az alhálózaton. Az Azure az alhálózatról származó forgalmat a szolgáltatás nyilvános IP-címére irányítja az Azure infrastruktúra-hálózaton keresztül. Az előtag hosszabb, mint 0.0.0.0/0, és nincs a többi útvonal címelőtagjain belül. A 3. [követelmény](#requirements) kielégítése érdekében jött létre szolgáltatásvégpont, hogy az Azure Storage-ba irányuló forgalom közvetlenül az Azure Storage tárterületre érkezzen.
11. Az Azure automatikusan hozzáadta ezt az útvonalat a *Virtual-network-1* és a *Virtual-network-2* hálózaton lévő összes alhálózat útvonaltáblájához. A 0.0.0.0/0 címelőtag a legrövidebb előtag. A hosszabb címelőtaggal rendelkező címekre küldött összes forgalom a többi útvonal alapján van átirányítva. Az Azure alapértelmezés szerint átirányítja az internetre az összes olyan forgalmat, amely nem az egyik másik útvonalon megadott címekre tart. Az Azure automatikusan *Aktívról* *Érvénytelenre* módosította a *Subnet1* alhálózat állapotát, amikor a 0.0.0.0/0 címelőtag (ID12) felhasználó által megadott útvonala lett társítva az alhálózattal. Az útvonal állapota továbbra is *Aktív* a mindkét virtuális hálózaton lévő összes többi alhálózat esetén, mert az útvonal nincs társítva a többi virtuális hálózatban lévő más alhálózatokkal.
12. Az Azure akkor adta hozzá ezt az útvonalat, amikor a 0.0.0.0/0 címelőtag felhasználó által megadott útvonala lett társítva a *Subnet1* alhálózattal. A felhasználó által megadott útvonal a 10.0.100.4 IP-címet határozza meg a virtuális berendezéshez. Ez az útvonal nincs társítva a *Subnet2* alhálózattal, ezért az útvonal nem jelenik meg a *Subnet2* útvonaltáblájában. A rendszer a többi útvonal címelőtagjaiban nem szereplő összes címre tartó forgalmat a virtuális berendezésre küldi. Ezen útvonal hozzáadása a 0.0.0.0/0 címelőtag (ID11) alapértelmezett útvonalát *Aktívról* *Érvénytelen* állapotúra módosította a *Subnet1* alhálózaton, mert a felhasználó által megadott útvonalak felülbírálják az alapértelmezett útvonalakat. Ez az útvonal a harmadik [követelmény](#requirements)kielégítése érdekében létezik.

#### <a name="subnet2"></a>Subnet2

A *Subnet2* a képen látható útvonaltáblája a következő útvonalakat tartalmazza:

|Source  |Állapot  |Címelőtagok    |A következő ugrás típusa             |A következő ugrás IP-címe|
|------- |-------|------              |-------                   |--------           
|Alapértelmezett |Aktív |10.0.0.0/16         |Virtuális hálózat           |                   |
|Alapértelmezett |Aktív |10.1.0.0/16         |Társviszony létesítése virtuális hálózatok között              |                   |
|Alapértelmezett |Aktív |10.2.0.0/16         |Társviszony létesítése virtuális hálózatok között              |                   |
|Alapértelmezett |Aktív |10.10.0.0/16        |Virtuális hálózati átjáró   |[X.X.X.X]          |
|Alapértelmezett |Aktív |0.0.0.0/0           |Internet                  |                   |
|Alapértelmezett |Aktív |10.0.0.0/8          |Nincsenek                      |                   |
|Alapértelmezett |Aktív |100.64.0.0/10       |None                      |                   |
|Alapértelmezett |Aktív |192.168.0.0/16      |Nincsenek                      |                   |

A *Subnet2* útvonaltáblája tartalmazza az összes, Azure által létrehozott alapértelmezett útvonalat és a választható virtuális hálózatok közötti társviszony és a virtuális hálózati átjáró választható útvonalait. Az Azure a virtuális hálózaton lévő összes alhálózathoz hozzáadta a választható útvonalakat, amikor az átjáró és a társviszony a virtuális hálózathoz lett adva. Az Azure eltávolította a 10.0.0.0/8, a 192.168.0.0/16 és a 100.64.0.0/10 címek előtagjainak útvonalait  , ha a 0.0.0.0/0 címnek a felhasználó által definiált útvonala hozzá lett adva a *Subnet1*-hez.  

## <a name="next-steps"></a>További lépések

* [Felhasználó által megadott útvonaltábla létrehozása útvonalakkal és hálózati virtuális berendezéssel](tutorial-create-route-table-portal.md)<br>
* [A BGP konfigurálása Azure VPN Gateway-átjáróhoz](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br>
* [A BGP használata az ExpressRoute-tal](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#route-aggregation-and-prefix-limits)<br>
* [Alhálózat összes útvonalának megtekintése](diagnose-network-routing-problem.md). A felhasználó által megadott útvonaltáblák csak a felhasználó által megadott útvonalakat mutatják, az alhálózat alapértelmezett és BGP-útvonalait nem. Az összes útvonal megtekintésekor láthatja a hálózati adaptert tartalmazó alhálózat alapértelmezett, BGP- és felhasználó által megadott útvonalait.<br>
* Virtuális gép és cél IP-cím közötti [következő ugrás típusának meghatározása](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Az Azure Network Watcher Következő ugrás funkciójával meghatározhatja, hogy a forgalom elhagyja-e az alhálózatot, és arra halad-e, amerre szeretné.
