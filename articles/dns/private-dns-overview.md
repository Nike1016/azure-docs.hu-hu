---
title: Mi az Azure Private DNS?
description: A privát DNS-üzemeltetési szolgáltatás áttekintése Microsoft Azureon.
services: dns
author: vhorne
ms.service: dns
ms.topic: overview
ms.date: 6/12/2019
ms.author: victorh
ms.openlocfilehash: 0921a1ac7aa1192fae78f168c2eb51ee3e74e24a
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2019
ms.locfileid: "68774623"
---
# <a name="what-is-azure-private-dns"></a>Mi az Azure Private DNS?

> [!IMPORTANT]
> Az Azure saját DNS jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A tartománynévrendszer vagy a DNS felelős a szolgáltatás nevének az IP-címére való fordításához (vagy feloldásához).  A Azure DNS a DNS-tartományok üzemeltetési szolgáltatása, amely névfeloldást biztosít a Microsoft Azure-infrastruktúra használatával. Az internetre irányuló DNS-tartományok támogatása mellett Azure DNS is támogatja a magánhálózati DNS-zónákat.

Az Azure saját DNS megbízható, biztonságos DNS-szolgáltatást biztosít a tartománynevek kezeléséhez és megoldásához egy virtuális hálózaton anélkül, hogy egyéni DNS-megoldást kellene hozzáadnia. Privát DNS-zónák használatával a jelenleg elérhető Azure-nevek helyett saját egyéni tartományneveket is használhat. Az egyéni tartománynevek segítségével testre szabhatja a virtuális hálózati architektúrát, hogy az megfeleljen a szervezet igényeinek. A virtuális gépek (VM-EK) névfeloldását biztosítja a virtuális hálózatokon és a virtuális hálózatokon belül. Emellett a zónák nevét is konfigurálhatja egy osztott horizontú nézettel, amely lehetővé teszi, hogy a privát és a nyilvános DNS-zónák megosszák a nevet.

Ha egy magánhálózati DNS-zóna rekordjait szeretné feloldani a virtuális hálózatról, a virtuális hálózatot a zónával kell összekapcsolni. A csatolt virtuális hálózatok teljes hozzáféréssel rendelkeznek, és a magánhálózati zónában közzétett összes DNS-rekordot feloldják. Emellett a virtuális hálózati kapcsolaton is engedélyezheti az automatikus regisztrációt. Ha engedélyezi az automatikus regisztrációt egy virtuális hálózati kapcsolaton, a virtuális hálózaton lévő virtuális gépek DNS-rekordjait a rendszer regisztrálja a privát zónában. Ha engedélyezve van az automatikus regisztráció, a Azure DNS a zóna rekordjait is frissíti, amikor létrejön egy virtuális gép, megváltoztatja az IP-címét, vagy törli azt.

![A DNS áttekintése](./media/private-dns-overview/scenario.png)

> [!NOTE]
> Ajánlott eljárásként ne használjon *. local* tartományt a saját DNS-zónájához. Nem minden operációs rendszer támogatja ezt.

## <a name="benefits"></a>Előnyök

Az Azure saját DNS a következő előnyöket biztosítja:

* **Eltávolítja az egyéni DNS-megoldások szükségességét**. Korábban számos ügyfél hozott létre egyéni DNS-megoldásokat a virtuális hálózat DNS-zónáinak kezeléséhez. Mostantól a natív Azure-infrastruktúrával kezelheti a DNS-zónákat, ami eltávolítja az egyéni DNS-megoldások létrehozásának és kezelésének terheit.

* **Használja az összes általános DNS-rekord típust**. Azure DNS supports A, AAAA, CNAME, MX, PTR, SOA, SRV, and TXT records.

* **Automatikus állomásnév-nyilvántartás kezelése**. Az egyéni DNS-rekordok üzemeltetése mellett az Azure automatikusan a megadott virtuális hálózatokban lévő virtuális gépek gazdagép-rekordjait is fenntartja. Ebben az esetben optimalizálhatja a használt tartományneveket anélkül, hogy egyéni DNS-megoldásokat kellene létrehoznia, vagy alkalmazásokat kellene módosítania.

* **Állomásnév feloldása a virtuális hálózatok között**. Az Azure által biztosított állomásnevektól eltérően a magánhálózati DNS-zónák megoszthatók a virtuális hálózatok között. Ez a funkció egyszerűsíti a hálózatok közötti és a szolgáltatás-felderítési forgatókönyveket, például a virtuális hálózati társítást.

* **Ismerős eszközök és felhasználói élmény**. A tanulási görbe csökkentése érdekében a szolgáltatás jól bevált Azure DNS-eszközöket (Azure Portal, Azure PowerShell, Azure CLI, Azure Resource Manager sablonokat és a REST API) használ.

* **Felosztási horizontú DNS-támogatás**. A Azure DNS használatával olyan zónákat hozhat létre, amelyek ugyanazzal a névvel rendelkeznek, mint a virtuális hálózaton belüli és a nyilvános internetről érkező különböző válaszokra. A megosztott horizontú DNS tipikus forgatókönyve, hogy a szolgáltatás dedikált verzióját adja meg a virtuális hálózaton belüli használatra.

* **Minden Azure-régióban elérhető**. Az Azure DNS Private Zones szolgáltatás az Azure nyilvános felhőben található összes Azure-régióban elérhető.

## <a name="capabilities"></a>Funkciók

Azure DNS a következő képességeket biztosítja:

* **Virtuális gépek automatikus regisztrálása egy olyan virtuális hálózatból, amely egy privát zónához van csatolva, és engedélyezve van az automatikus regisztráció**. A virtuális gépek regisztrálva vannak (hozzáadva) a privát zónához a magánhálózati IP-címekre mutató rekordokként. Ha az automatikus regisztrációval rendelkező virtuális hálózati kapcsolaton belül egy virtuális gép törölve lett, Azure DNS automatikusan eltávolítja a megfelelő DNS-rekordot a társított privát zónából.

* **A továbbítási DNS-feloldás a privát zónához csatolt virtuális hálózatok között támogatott**. A virtuális hálózatok közötti DNS-feloldáshoz nincs olyan explicit függőség, amely a virtuális hálózatokat egymáshoz társítja. Előfordulhat azonban, hogy más forgatókönyvek (például HTTP-forgalom) esetében is szeretne egyenrangú virtuális hálózatokat használni.

* **A fordított DNS-keresés a virtuális hálózat hatókörén belül támogatott**. A privát zónákhoz rendelt virtuális hálózaton belüli magánhálózati IP-címek névkeresési DNS-címkeresés a gazdagép/rekord nevét és a zóna nevét tartalmazó teljes tartománynevet adja vissza utótagként.

## <a name="known-issues"></a>Ismert problémák
A következő elemek ismert hibákat és problémákat észleltek az előzetes kiadásban:
* Ha egy magánhálózati DNS-zónához csatolt virtuális hálózatot töröl, nem törli a magánhálózati DNS-zónára mutató hivatkozásokat. A hivatkozás meghiúsul, ha a virtuális hálózatot ugyanazzal a névvel és erőforráscsoporthoz hozza létre, és megpróbálja újra csatolni a saját DNS-zónához. A probléma megkerüléséhez hozzon létre egy másik erőforráscsoporthoz tartozó virtuális hálózatot, vagy adjon meg egy másik nevet ugyanabban az erőforráscsoporthoz.
* Ha egy virtuális hálózatot másik erőforráscsoporthoz vagy előfizetésbe helyez át, a nem frissíti a magánhálózati DNS-zónára mutató hivatkozásokat. Az áthelyezett virtuális hálózat névfeloldása továbbra is működik, azonban a virtuális hálózat régi ARM-azonosítóit látja, amikor megtekinti a magánhálózati DNS-zóna virtuális hálózati kapcsolatait.
* Jelenleg az Egyesült Arab Emírségek északi régiójában, az Egyesült Királyság nyugati régiójában, Dél-Afrikában, Dél-Afrikában, Észak-Kanadában, Kelet-Kanada, Dél-Afrika, és előfordulhat, hogy időszakos DNS-feloldási problémák merülhetnek fel. 


## <a name="other-considerations"></a>Egyéb szempontok

A Azure DNS a következő korlátozásokkal rendelkezik:

* Egy adott virtuális hálózat csak egy privát zónához kapcsolható, ha engedélyezve van a virtuális gépek DNS-rekordjainak automatikus regisztrálása. Azonban több virtuális hálózatot is csatolhat egyetlen DNS-zónához.
* A fordított DNS csak a társított virtuális hálózat magánhálózati IP-címére működik
* A csatolt virtuális hálózat magánhálózati IP-címéhez fordított DNS a "internal.cloudapp.net" értéket adja vissza a virtuális gép alapértelmezett utótagjának. Az automatikus regisztrációt engedélyező privát zónához kapcsolt virtuális hálózatok esetében a magánhálózati IP-címek fordított DNS-je 2 teljes tartománynevet ad vissza, egyet az alapértelmezett utótag *Internal.cloudapp.net* , a másik pedig a privát zóna utótagját.
* A feltételes továbbítás jelenleg nem támogatott natív módon. Az Azure és a helyszíni hálózatok közötti megoldás engedélyezéséhez lásd [a virtuális gépek és a szerepkör](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)-példányok névfeloldását ismertető témakört.

 
## <a name="pricing"></a>Díjszabás

A díjszabással kapcsolatos információkért tekintse meg a [Azure DNS díjszabását](https://azure.microsoft.com/pricing/details/dns/).

## <a name="next-steps"></a>További lépések

* Megtudhatja, hogyan hozhat létre saját zónát Azure DNSban [Azure PowerShell](./private-dns-getstarted-powershell.md) vagy az [Azure CLI](./private-dns-getstarted-cli.md)használatával.

* További információ a privát [](./private-dns-scenarios.md) zónák Azure DNS-beli privát zónákkal való használatával kapcsolatban.

* A Azure DNS privát zónákkal kapcsolatos gyakori kérdések és válaszok, beleértve a bizonyos típusú műveletek várható viselkedését, [saját DNS a gyakori](./dns-faq-private.md)kérdések című témakört.

* A DNS-zónák és-rekordok megismerése a [DNS-zónák és-rekordok áttekintésével](dns-zones-records.md).

* Ebben a dokumentumban az Azure egyéb lényeges [hálózat képességeivel](../networking/networking-overview.md) ismerkedhet meg.
