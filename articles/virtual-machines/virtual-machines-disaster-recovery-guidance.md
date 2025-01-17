---
title: Vész-helyreállítási forgatókönyvek az Azure-beli virtuális gépekhez | Microsoft Docs
description: Ismerje meg, mi a teendő abban az esetben, ha egy Azure-szolgáltatás megszakad az Azure-beli virtuális gépeken.
services: virtual-machines
documentationcenter: ''
author: kmouss
manager: gwallace
editor: ''
ms.assetid: 65272148-ff06-4bce-91f1-851d706d4d40
ms.service: virtual-machines
ms.workload: virtual-machines
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2017
ms.author: gwallace
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bc9ca5f5a638f0b36a28d58172fe8052b3d1522f
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875449"
---
# <a name="what-to-do-in-the-event-that-an-azure-service-disruption-impacts-azure-vms"></a>Mi a teendő abban az esetben, ha egy Azure-szolgáltatás megszakítja az Azure-beli virtuális gépeket
A Microsoftnál keményen dolgozunk, hogy a szolgáltatások mindig elérhetők legyenek, amikor szüksége van rájuk. A szabályozáson kívüli erők időnként a nem tervezett szolgáltatások megszakadását okozó módokat érintik.

A Microsoft szolgáltatói szerződés (SLA) szolgáltatást biztosít a szolgáltatásai számára az üzemidő és a kapcsolat iránti elkötelezettségként. Az egyes Azure-szolgáltatásokra vonatkozó SLA-t az [Azure](https://azure.microsoft.com/support/legal/sla/)-szolgáltatói szerződésekben találhatja meg.

Az Azure már számos beépített platformot kínál, amelyek támogatják a magas rendelkezésre állású alkalmazásokat. A szolgáltatásokkal kapcsolatos további információkért olvassa el a vész [-helyreállítást és a magas rendelkezésre állást az Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)-alkalmazásokhoz.

Ez a cikk egy valós vész-helyreállítási forgatókönyvet mutat be, amikor egy egész régió jelentős természeti katasztrófák vagy széleskörű szolgáltatás-megszakítás miatt leáll. Ezek ritkán előfordulnak, de fel kell készülnie arra, hogy a teljes régió leálljon. Ha egy teljes régió a szolgáltatás megszakadását tapasztalja, az adatai helyileg redundáns másolatai átmenetileg elérhetetlenné válnak. Ha engedélyezte a földrajzi replikálást, az Azure Storage-blobok és-táblák három további példánya egy másik régióban tárolódik. Ha egy teljes regionális leállás vagy egy olyan katasztrófa következik be, amelyben az elsődleges régió nem helyreállítható, az Azure az összes DNS-bejegyzést átképezi a földrajzilag replikált régióba.

A ritka előfordulások kezeléséhez a következő útmutatást nyújtjuk az Azure-beli virtuális gépekhez abban az esetben, ha az Azure-beli virtuálisgép-alkalmazást üzembe helyező teljes régiót megzavarják.

## <a name="option-1-initiate-a-failover-by-using-azure-site-recovery"></a>1\. lehetőség: Feladatátvétel kezdeményezése Azure Site Recovery használatával
A virtuális gépekhez Azure Site Recovery konfigurálhatja, hogy az alkalmazás a percek alatt egyetlen kattintással helyreállítható legyen. A replikációt a választott Azure-régióba replikálhatja, és nem korlátozhatja a párosított régiókat. [A virtuális gépek replikálásával](https://aka.ms/a2a-getting-started)kezdheti meg a lépéseket. [Létrehozhat egy helyreállítási tervet](../site-recovery/site-recovery-create-recovery-plans.md) , amely segítségével automatizálhatja az alkalmazás teljes feladatátvételi folyamatát. [A feladatátvételt](../site-recovery/site-recovery-test-failover-to-azure.md) előre tesztelheti a termelési alkalmazás vagy a folyamatban lévő replikáció nélkül. Az elsődleges régiók megszakadásakor a feladatátvételt kezdeményezni kell [](../site-recovery/site-recovery-failover.md) , és az alkalmazást a célként megadott régióban kell használni.


## <a name="option-2-wait-for-recovery"></a>2\. lehetőség: Várakozás a helyreállításra
Ebben az esetben nincs szükség beavatkozásra a részen. Biztos lehet benne, hogy szorgalmasan dolgozunk a szolgáltatás rendelkezésre állásának helyreállításán. A szolgáltatás aktuális állapotát a [Azure Service Health irányítópulton](https://azure.microsoft.com/status/)tekintheti meg.

Ez a legjobb megoldás, ha nem állított be Azure Site Recovery, a megszakadás előtt olvassa el a Geo-redundáns tárterületet vagy a Geo-redundáns tárterületet. Ha a virtuális gép virtuális merevlemezeit (VHD-ket) tartalmazó Storage-fiókhoz beállította a Geo-redundáns tárolást vagy az olvasási hozzáférésű geo-redundáns tárolót, akkor megkeresheti az alapszintű lemezkép VHD-jét, és kiépítheti az új virtuális gépet. Ez nem előnyben részesített lehetőség, mert nincs garancia az adatszinkronizálásra. Ennek következtében ez a lehetőség nem garantált a működéshez.


> [!NOTE]
> Ügyeljen arra, hogy a folyamat felett ne legyen vezérlés, és csak az egész régióra kiterjedő szolgáltatásokkal kapcsolatos fennakadások esetén fog történni. Emiatt a legmagasabb szintű rendelkezésre állás elérése érdekében más alkalmazásspecifikus biztonsági mentési stratégiákra is támaszkodnia kell. További információ: a vész- [helyreállítási](https://docs.microsoft.com/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan)adatstratégiákkal foglalkozó szakasz.
>
>

## <a name="next-steps"></a>További lépések

- [Az Azure-beli virtuális gépeken futó alkalmazások védelmének](https://aka.ms/a2a-getting-started) megkezdése Azure site Recovery használatával

- Ha többet szeretne megtudni a vész-helyreállítási és a magas rendelkezésre állási stratégia megvalósításáról, tekintse meg a vész [-helyreállítási és magas rendelkezésre állású Azure-alkalmazások](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)című témakört.

- A felhőalapú platform képességeinek részletes technikai megismeréséhez lásd: az [Azure rugalmasságával kapcsolatos technikai útmutató](../resiliency/resiliency-technical-guidance.md).


- Ha az utasítások nem egyértelműek, vagy ha szeretné, hogy a Microsoft végezze el a műveleteket az Ön nevében [](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade), forduljon az ügyfélszolgálathoz.
