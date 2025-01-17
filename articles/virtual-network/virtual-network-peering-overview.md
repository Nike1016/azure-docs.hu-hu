---
title: Az Azure virtuális hálózatok közötti társviszony
titlesuffix: Azure Virtual Network
description: Tudnivalók az Azure-beli virtuális hálózatok közötti társviszony-létesítésről
services: virtual-network
documentationcenter: na
author: anavinahar
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/01/2019
ms.author: anavin
ms.openlocfilehash: 100bbb6e0ed8e2ea5b35e30e7759a3b11c169b60
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67077622"
---
# <a name="virtual-network-peering"></a>Társviszony létesítése virtuális hálózatok között

Virtuális hálózatok közötti társviszony lehetővé teszi, hogy zökkenőmentesen csatlakoztathatja az Azure [virtuális hálózatok](virtual-networks-overview.md). A társviszony kialakítását követően a virtuális hálózatok a csatlakozás szempontjából egyetlen hálózatnak látszanak. A virtuális társhálózatokba tartozó virtuális gépek közötti forgalmat lényegében ugyanúgy továbbítja a rendszer a Microsoft gerincinfrastruktúráján keresztül, ahogyan az azonos virtuális hálózatba tartozó virtuális gépek közötti forgalmat, *magánhálózati* IP-címeken keresztül. Az Azure a következőket támogatja:
* Virtuális társhálózatok létesítése – virtuális hálózatok csatlakoztatása egyazon Azure-régióban
* Globális virtuális társhálózatok létesítése – virtuális hálózatok csatlakoztatása Azure-régiók között

A virtuális társhálózatok akár helyi, akár globális létesítésének előnyei:

* A társított virtuális hálózatok közti hálózati adatforgalom nem nyilvános. A virtuális hálózatok közötti forgalom a Microsoft gerinchálózatán belül marad. A virtuális hálózatok közti forgalomhoz nincs szükség nyilvános internetre, átjárókra vagy titkosításra.
* Kis késésű, nagy sávszélességű kapcsolat jön létre eltérő virtuális hálózatokba tartozó erőforrások között.
* Az egyes virtuális hálózatokban lévő erőforrások a hálózatok társítását követően képesek más virtuális hálózatokban lévő erőforrásokkal kommunikálni.
* Adatok továbbíthatók Azure-előfizetések, üzemi modellek és Azure-régiók között.
* Létrehozható társviszony az Azure Resource Managerrel létrehozott virtuális hálózatok között, vagy egy, a Resource Managerrel létrehozott virtuális hálózat és egy, a klasszikus üzemi modellel létrehozott virtuális hálózat között. Az Azure üzembehelyezési modellekkel kapcsolatos további információkért lásd: [Az Azure üzemi modelljeinek megismerése](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
* Ez nem okoz leállást egyik virtuális hálózat erőforrásaiban sem a társításkor és azt követően sem.

## <a name="connectivity"></a>Kapcsolat

Ha társviszony jött létre a virtuális hálózatok között, akkor a virtuális hálózatokba tartozó erőforrások közvetlenül tudnak kommunikálni a virtuális társhálózat erőforrásaival.

Az azonos régióban lévő virtuális társhálózaton belüli virtuális gépek közötti hálózati késés megegyezik az egyetlen virtuális hálózaton belüli hálózati késéssel. A hálózat átbocsátóképessége attól függ, hogy milyen, a virtuális gépek méretével arányos sávszélesség van engedélyezve. A társviszonyon belül más korlátozás nem vonatkozik a sávszélességre.

A virtuális társhálózatokon belüli virtuális gépek közötti forgalom közvetlenül a Microsoft gerincinfrastruktúráján halad át, nem pedig átjárón vagy a nyilvános interneten.

Szükség esetén bármelyik virtuális hálózatban alkalmazhatók hálózati biztonsági csoportok a más virtuális hálózatok vagy alhálózatok elérésének blokkolására.
Virtuális hálózatok társviszonyának konfigurálásakor megnyithatja vagy lezárhatja a hálózati biztonsági csoportszabályokat a virtuális hálózatok között. Ha úgy dönt, hogy teljes körű kapcsolatra nyit lehetőséget a virtuális társhálózatok között (ez az alapértelmezett beállítás), akkor a hálózati biztonsági csoportok segítségével konkrét alhálózatokra vagy virtuális gépekre vonatkozóan blokkolhatja vagy megtagadhatja a megadott típusú hozzáférést. A hálózati biztonsági csoportokkal kapcsolatos további információk: [Hálózati biztonsági csoportok áttekintése](security-overview.md).

## <a name="service-chaining"></a>Szolgáltatásláncolás

Konfigurálhatók olyan felhasználó által megadott útvonalak, amelyekben a társviszonyban álló virtuális hálózatba tartozó virtuális gépek (a *következő ugrás* IP-címeként) vagy virtuális hálózati átjárók vannak megadva, így lehetővé válik a szolgáltatásláncolás. A szolgáltatásláncolással a forgalom a felhasználó által meghatározott útvonalon átirányítható egy virtuális hálózatból a virtuális társhálózatban működő virtuális berendezésre vagy virtuális hálózati átjáróra.

Üzembe helyezhet küllős topológiájú hálózatokat is, ahol a központi virtuális hálózatban infrastrukturális összetevők (például virtuális hálózati berendezések vagy VPN-átjárók) működhetnek. Az ágakon lévő virtuális hálózatok társhálózatai lehetnek a középponti hálózatnak. A középponti virtuális hálózatban az adatforgalom virtuális hálózati berendezéseken vagy VPN-átjárókon is áthaladhat. 

A virtuális hálózatok közötti társviszony-létesítéssel a felhasználó által definiált útvonalon egy virtuális társhálózat egyik virtuális gépének vagy egy VPN-átjárónak az IP-címe beállítható a következő ugrás IP-címeként. Ugyanakkor nem hozhat létre virtuális hálózatok közötti útvonalat olyan felhasználói útvonallal, amely egy ExpressRoute-átjárót határoz meg a következő ugrás típusaként. A felhasználó által megadott útvonalakkal kapcsolatos további információkért lásd a [felhasználó által megadott útvonalak áttekintését](virtual-networks-udr-overview.md#user-defined). A küllős hálózati topológiák létrehozásával kapcsolatos további információkért lásd a [küllős hálózati topológiákat](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json) ismertető cikket

## <a name="gateways-and-on-premises-connectivity"></a>Átjárók és kapcsolat helyszíni rendszerekkel

Minden virtuális hálózatnak – függetlenül attól, hogy társviszonyban áll-e más virtuális hálózattal – lehet saját átjárója, és használhatja egy helyi hálózathoz való csatlakozásra. A [virtuális hálózatok közötti kapcsolat](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) is konfigurálható átjárók segítségével, még abban az esetben is, ha a virtuális hálózatok társviszonyban állnak.

Ha a virtuális hálózatok mindkét összekapcsolási módszere konfigurálva van, a virtuális hálózatok közötti forgalom a társhálózati konfiguráción keresztül (vagyis az Azure gerinchálózatán keresztül) zajlik.

Ha két virtuális hálózat között társhálózati viszony van, a virtuális társhálózatban működő átjáró is konfigurálható átviteli pontként a helyi hálózat felé. Ebben az esetben a távoli átjárót használó virtuális hálózatnak nem lehet saját átjáróra. Egy virtuális hálózatnak csak egy átjárója lehet. Az átjáró lehet helyi vagy távoli (virtuális társhálózatban működő), amint a következő képen látható:

![virtuális társhálózatok közötti átvitel](./media/virtual-networks-peering-overview/figure04.png)

Átjárói átvitel virtuális hálózatok közötti Társviszony és a globális virtuális társhálózatok létesítésének is támogatott. Átjárói átvitel (Resource Manager és Klasszikus modell) különböző üzembehelyezési modellel létrehozott virtuális hálózatok között csak akkor, ha az átjáró a virtuális hálózatban (Resource Manager) nem támogatott. További információ az átjárók adatátvitelre való használatáról: [VPN-átjáró konfigurálása adatátvitelhez virtuális hálózatok közötti társviszony-létesítésben](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Ha két, ugyanazon az Azure ExpressRoute-kapcsolaton osztozó virtuális hálózatot állítanak társviszonyba, akkor a társhálózaton (vagyis az Azure gerinchálózatán) keresztül áramlik közöttük a forgalom. Az egyes virtuális hálózatok helyi átjárói ennek ellenére használhatók arra, hogy kapcsolatot létesítsenek a helyszíni kapcsolatcsoporttal. Közös átjárót is használhat, és átvitel konfigurálásával létesíthet kapcsolatot a helyszíni rendszerrel.

## <a name="troubleshoot"></a>Hibaelhárítás

A meg kíván győződni a virtuális hálózatok közötti társviszony-létesítés sikeres végrehajtásáról, a virtuális hálózat bármely alhálózatában elvégezheti a hálózati adapter [érvényes útvonalainak ellenőrzését](diagnose-network-routing-problem.md). Ha létezik a virtuális hálózatok közötti társviszony, a virtuális hálózat összes alhálózata *Virtuális hálózatok közötti társviszony* következő ugrási típusú útvonalakkal rendelkezik minden virtuális társhálózat minden címterében.

A virtuális társhálózatokban lévő virtuális gépek kapcsolatának hibaelhárításához használhatja a Network Watcher [kapcsolat-ellenőrzési funkcióját](../network-watcher/network-watcher-connectivity-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). A kapcsolat ellenőrzése során megtekintheti az adatforgalmat a forrás és a cél virtuális gépek hálózati adapterei között.

Megpróbálhatja a [virtuális hálózati társviszony-létesítési hibák elhárítója](https://support.microsoft.com/help/4486956/troubleshooter-for-virtual-network-peering-issues).

## <a name="requirements-and-constraints"></a>Követelmények és korlátozások

A következő korlátozások vonatkoznak, csak ha virtuális hálózatok globálisan társviszonyban állnak:
- Egy virtuális hálózatban lévő erőforrásokra globálisan két társviszonyban álló virtuális hálózatban lévő alapvető belső terheléselosztó előtérbeli IP-címe nem lehet kommunikálni. Alapszintű Load Balancer támogatása csak az azonos régión belüli létezik. A Standard Load Balancer támogatását is, a virtuális hálózatok közötti Társviszony és a globális virtuális társhálózatok létesítésének létezik. Egy alapszintű load balancer, amely nem fog működni a globális virtuális hálózatok közötti társviszony-Létesítésen keresztül használó szolgáltatások vannak dokumentálva [itt.](virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)

További információ a követelményekről és a korlátozásokról: [A virtuális hálózatok közötti társviszony követelményei és korlátozásai](virtual-network-manage-peering.md#requirements-and-constraints). A virtuális hálózatok közötti társviszonyok számának korlátozásairól az [Azure hálózati korlátait](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) ismertető szakaszban talál további információt. 

## <a name="permissions"></a>Engedélyek

További információ a virtuális hálózatok közötti társviszonyok létrehozásához szükséges engedélyekről: [Virtuális hálózatok közötti társviszonyra vonatkozó engedélyek](virtual-network-manage-peering.md#permissions).

## <a name="pricing"></a>Díjszabás

Egy névleges díj vonatkozik a társviszony-kapcsolatot használó bejövő és kimenő forgalomra. A virtuális társhálózatok és a globális virtuális társhálózatok díjszabásával kapcsolatos további tájékoztatás a [díjszabási lapon](https://azure.microsoft.com/pricing/details/virtual-network) olvasható.

Átjárói átvitel a társviszony-létesítési tulajdona, amely lehetővé teszi a virtuális hálózat társviszonyban lévő virtuális hálózat közötti helyszíni vagy virtuális hálózatok közötti kapcsolat egy VPN/ExpressRoute-átjáró használatához. Ebben a forgatókönyvben egy távoli átjáró áthaladó forgalom státuszban van [VPN gateway díjak](https://azure.microsoft.com/pricing/details/vpn-gateway/) vagy ExpressRoute-átjáró díjat, és nem jár [virtuális hálózatok közötti társviszony-létesítési díjak.](https://azure.microsoft.com/pricing/details/virtual-network) Például ha hálózattal rendelkezik helyszíni kapcsolatok VPN-átjáró és a b társviszonyban van a hálózattal konfigurált megfelelő tulajdonságok b forgalmát a helyszíni, akkor csak mennyiségre kimenő forgalom VPN gateway díjszabás vagy ExpressRoute – díjszabás. Virtuális társhálózati díjak nem lesznek felszámolva. Ismerje meg, hogyan [konfigurálhat VPN-átjáróval történő adatátvitelt virtuális hálózatok közötti társviszony létesítéséhez](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="next-steps"></a>További lépések

* A virtuális hálózatok közötti társviszony az azonos vagy eltérő előfizetésekben lévő, azonos vagy eltérő üzembehelyezési modelleken keresztül létrehozott virtuális hálózatok között jön létre. Végezzen el egy oktatóanyagot a következő forgatókönyvek egyikéhez:

    |Azure üzembehelyezési modell             | Előfizetés  |
    |---------                          |---------|
    |Mindkét Resource Manager              |[Ugyanaz](tutorial-connect-virtual-networks-portal.md)|
    |                                   |[Különböző](create-peering-different-subscriptions.md)|
    |Egy Resource Manager, egy klasszikus  |[Ugyanaz](create-peering-different-deployment-models.md)|
    |                                   |[Különböző](create-peering-different-deployment-models-subscriptions.md)|

* További információ a [küllős hálózati topológiák létrehozásáról](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json).
* További tudnivalók a [virtuális társhálózatok beállításairól és azok megváltoztatásáról](virtual-network-manage-peering.md).
* A virtuális társhálózatokkal és a globális virtuális társhálózatokkal kapcsolatos kérdéseire a [virtuális hálózatok társviszonyaival kapcsolatos gyakori kérdésekben](virtual-networks-faq.md#vnet-peering) kaphat választ.
