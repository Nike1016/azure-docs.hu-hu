---
title: Tudnivalók a Azure Availability Zones-beli virtuális hálózati átjárók használatáról | Microsoft Docs
description: Ismerkedjen meg VPN Gateway-és ExpressRoute-átjárókkal Availability Zonesban.
services: vpn-gateway
author: cherylmc
Customer intent: As someone with a basic network background, I want to understand zone-redundant gateways.
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
ms.openlocfilehash: d076e2b0057f0ba666fa47ffd0b3d7d1fcc14631
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68725585"
---
# <a name="about-zone-redundant-virtual-network-gateways-in-azure-availability-zones"></a>Tudnivalók a Azure Availability Zones-beli virtuális hálózati átjárók használatáról

A VPN-és ExpressRoute-átjárók üzembe [](../availability-zones/az-overview.md)helyezhetők a Azure Availability Zonesban. Ez rugalmasságot, méretezhetőséget és magasabb rendelkezésre állást biztosít a virtuális hálózati átjárók számára. Az átjárók üzembe helyezése Azure Availability Zones fizikailag és logikailag elkülöníti az átjárókat a régión belül, miközben a helyszíni hálózati kapcsolatot az Azure-hoz a zóna szintű hibákkal védi.

### <a name="zrgw"></a>Zóna – redundáns átjárók

A virtuális hálózati átjárók rendelkezésre állási zónákon keresztüli automatikus üzembe helyezéséhez használhatja a zóna-redundáns virtuális hálózati átjárókat. A Zone-redundáns átjárókkal kihasználhatja az Azure-beli üzleti szempontból alapvető fontosságú, méretezhető szolgáltatások elérését.

<br>
<br>

![zóna – redundáns átjárók ábrája](./media/create-zone-redundant-vnet-gateway/zonered.png)

### <a name="zgw"></a>Zóna-átjárók

Az átjárók egy adott zónában való üzembe helyezéséhez a zóna-átjárókat használhatja. Ha egy zóna-átjárót helyez üzembe, az átjáró összes példánya ugyanabban a rendelkezésre állási zónában lesz telepítve.

<br>
<br>

![zóna-átjárók ábrája](./media/create-zone-redundant-vnet-gateway/zonal.png)

## <a name="gwskus"></a>Átjáró-termékváltozatok

A Zone-redundáns és a zóna-átjárók új Gateway SKU-ként érhetők el. Új Virtual Network Gateway SKU-ket adott hozzá az Azure-ban az Regions. Ezek a SKU-azonosítók a ExpressRoute és a VPN Gateway vonatkozó meglévő SKU-hoz hasonlóak, azzal a különbséggel, hogy azok a zóna-redundáns és a zónákhoz tartozó átjárók.

Az új átjárók a következők:

### <a name="vpn-gateway"></a>VPN Gateway

* VpnGw1AZ
* VpnGw2AZ
* VpnGw3AZ

### <a name="expressroute"></a>ExpressRoute

* ErGw1AZ
* ErGw2AZ
* ErGw3AZ

## <a name="pipskus"></a>Nyilvános IP-SKU

A Zone-redundáns átjárók és a zóna-átjárók egyaránt az Azure nyilvános IP-erőforrás *standard* SKU-ra támaszkodnak. Az Azure nyilvános IP-erőforrásának konfigurációja határozza meg, hogy az Ön által telepített átjáró a zóna redundáns vagy a zónákhoz van-e telepítve. Ha alapszintű SKU-val hoz létre egy nyilvános IP-erőforrást, akkor az átjáró nem rendelkezik zónával, és az átjáró erőforrásai regionálisak lesznek.

### <a name="pipzrg"></a>Zóna – redundáns átjárók

Amikor nyilvános IP-címet hoz létre a **szabványos** nyilvános IP-SKU használatával a zóna megadása nélkül, a viselkedés eltér attól függően, hogy az átjáró VPN-átjáró vagy ExpressRoute-átjáró. 

* A VPN-átjárók esetében a két átjáró közül bármelyik kettőt üzembe helyezi a rendszer a következő három zónából a zóna-redundancia biztosítása érdekében. 
* Egy ExpressRoute-átjáró esetében, mivel kettőnél több példány is lehet, az átjáró a három zónára terjedhet.

### <a name="pipzg"></a>Zóna-átjárók

Amikor nyilvános IP-címet hoz létre a **standard** nyilvános IP-SKU használatával, és megadja a zónát (1, 2 vagy 3), az összes átjáró-példány ugyanabban a zónában lesz telepítve.

### <a name="piprg"></a>Regionális átjárók

Amikor nyilvános IP-címet hoz létre az alapszintű nyilvános IP-SKU használatával, az átjáró regionális átjáróként van üzembe helyezve, és nem rendelkezik az átjáróba épített összes zónával.

## <a name="faq"></a>GYIK

### <a name="what-will-change-when-i-deploy-these-new-skus"></a>Mi változik, ha az új SKU-t üzembe helyezem?

Az átjárók az Ön szemszögéből is üzembe helyezhetők a zóna-redundanciával. Ez azt jelenti, hogy az átjárók összes példánya Azure Availability Zones lesz telepítve, és az egyes rendelkezésre állási zónák egy másik hiba-és frissítési tartomány. Így az átjárók megbízhatóbbak, elérhetők és rugalmasak lesznek a zónák meghibásodása miatt.

### <a name="can-i-use-the-azure-portal"></a>Használhatom a Azure Portal?

Igen, a Azure Portal használatával telepítheti az új SKU-t. Ezeket az új SKU-ket azonban csak azokban az Azure-régiókban fogja látni, amelyek Azure Availability Zones rendelkeznek.

### <a name="what-regions-are-available-for-me-to-use-the-new-skus"></a>Milyen régiókban érhetők el az új SKU-i használata?

Az új SKU-ket olyan Azure-régiókban lehet elérni, amelyek Azure Availability Zones – Közép-USA, Közép-Franciaország, Észak-Európa, Nyugat-Európa és az USA 2. nyugati régiója, USA keleti régiója, USA 2. keleti régiója, Délkelet-Ázsia, Kelet Egyesült Királyság déli régiója-Japán A jövőben az Azure-beli nyilvános régiókban elérhetővé tesszük a zóna-redundáns átjárókat.

### <a name="can-i-changemigrateupgrade-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>Módosíthatom/telepíthetem vagy frissíthetem a meglévő virtuális hálózati átjárókat a zóna-redundáns vagy a zónák közötti átjáróra?

Jelenleg nem támogatott a meglévő virtuális hálózati átjárók áttelepítése a zóna-redundáns vagy a zónák közötti átjáróra. Azonban törölheti a meglévő átjárót, és újból létrehozhatja a zóna-redundáns vagy a zónákhoz tartozó átjárót.

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>Telepíthetek VPN-és Express Route Gateway-átjárókat is ugyanazon a virtuális hálózaton?

Az azonos virtuális hálózatban található VPN-és Express Route-átjárók együttes létezése is támogatott. Az átjáró-alhálózat esetében azonban foglaljon le egy/27 IP-címtartományt.

## <a name="next-steps"></a>További lépések

[Zónaredundáns virtuális hálózati átjáró létrehozása](create-zone-redundant-vnet-gateway.md)
