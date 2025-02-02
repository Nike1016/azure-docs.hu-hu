---
title: A helyszíni hálózatok csatlakoztatása a Microsoft Cloud globális elérhetőségű – Azure ExpressRoute használatával |} A Microsoft Docs
description: Ez a cikk bemutatja az ExpressRoute globális elérhetőségű.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 28cfbc7183628b00c463f1c3a8a954ba4074409b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65415964"
---
# <a name="expressroute-global-reach"></a>Az ExpressRoute globális elérhetőséggel
Az ExpressRoute egy magán- és rugalmas módja a helyszíni hálózatok csatlakoztatása a Microsoft Cloud. Számos Microsoft felhőszolgáltatások, például Azure, Office 365 és Dynamics 365 elérését a saját adatközpontban vagy a vállalati hálózathoz. Például előfordulhat, hogy rendelkezik egy fiókirodában, a San franciscóban az ExpressRoute-kapcsolatcsoport szilícium-völgy és a egy másik fiókiroda londoni az ExpressRoute-kapcsolatcsoport város. Mindkét fiókirodák lehet nagy sebességű kapcsolat az USA nyugati régiója, Egyesült Királyság déli régiója és az Azure-erőforrások. Azonban a fiókirodák közvetlenül egymással adatok nem váltanak. Más szóval 10.0.1.0/24 küldhet adatokat, 10.0.3.0/24 és 10.0.4.0/24, de nem 10.0.2.0/24.

![nélkül][1]

A **ExpressRoute globális elérhetőségű**, ExpressRoute-Kapcsolatcsoportok teszik a helyszíni hálózat között egy privát hálózati kapcsolat. A fenti példában, ExpressRoute globális elérhetőségű, azonban kiegészül a San Franciscóban office (10.0.1.0/24) is közvetlenül adatcserét (10.0.2.0/24) keresztül a meglévő ExpressRoute-Kapcsolatcsoportok és a Microsoft globális hálózaton keresztül londoni irodájában. 

![a][2]

## <a name="use-case"></a>Használati eset
Az ExpressRoute globális elérhetőségű célja, hogy a szolgáltató WAN végrehajtása kiegészíti, és a fiókirodák összekapcsolása a világ. Például, ha a szolgáltató elsősorban az Egyesült Államokban működik, és rendelkezik társított összes az ágak, az Egyesült Államokban, de a szolgáltató nem működik a japán és Hongkong (KKT), az ExpressRoute globális elérhetőségű együttműködve egy helyi szolgáltató és a A Microsoft fog csatlakozni az ágak van Egyesült államokbeli ExpressRoute-és globális hálózata azokat.

![Használati eset][3]

## <a name="availability"></a>Rendelkezésre állás 
Az ExpressRoute globális elérhetőségű jelenleg támogatott a következő helyeken.

* Ausztrália
* Kanada
* Franciaország
* Németország
* Hongkong (KKT)
* Írország
* Japán
* Korea
* Hollandia
* Szingapúr
* Svájc
* Egyesült Királyság
* Egyesült Államok

Az ExpressRoute-Kapcsolatcsoportok létre kell hozni a [ExpressRoute-társviszony-létesítési helyszínek](expressroute-locations.md) a fenti országban vagy régióban. Ahhoz, hogy az ExpressRoute globális elérhetőségű közötti [geopolitikai régiókon](expressroute-locations.md), a Kapcsolatcsoportok prémium Termékváltozatot kell lennie.

## <a name="next-steps"></a>További lépések
1. [További információ az ExpressRoute globális elérhetőségű](expressroute-faqs.md)
2. [Az ExpressRoute globális elérhetőségű engedélyezése](expressroute-howto-set-global-reach.md)
3. [Azure virtuális hálózati kapcsolat ExpressRoute-kapcsolatcsoport](expressroute-howto-linkvnet-arm.md)


<!--Image References-->
[1]: ./media/expressroute-global-reach/1.png "globális jelenlét nélkül diagramja"
[2]: ./media/expressroute-global-reach/2.png "globális elérhetőségű diagramja"
[3]: ./media/expressroute-global-reach/3.png "használja a kis-és globális elérhetőséggel"
