---
title: Az Azure-beli SAP HANA áttekintése (nagyméretű példányok) | Microsoft Docs
description: A SAP HANA üzembe helyezésének áttekintése az Azure-ban (nagyméretű példányok).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b2076778751161d5763d7bd0643cfe8f71a5f522
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2019
ms.locfileid: "67869195"
---
#  <a name="what-is-sap-hana-on-azure-large-instances"></a>Mi az SAP HANA az Azure-ban (nagy méretű példányok)?

A SAP HANA az Azure-ban (nagyméretű példányok) egyedülálló megoldás az Azure-ra. Amellett, hogy virtuális gépeket biztosít a SAP HANA üzembe helyezéséhez és futtatásához, az Azure lehetőséget biztosít az Ön számára dedikált, operációs rendszer nélküli kiszolgálókon történő SAP HANA futtatására és telepítésére. Az Azure-beli (nagyméretű példányok) megoldás SAP HANA az Önhöz rendelt, nem megosztott gazdagépre vagy kiszolgálóra épülő hardverre épít. A kiszolgáló hardvere nagyobb bélyegbe van ágyazva, amely számítási/kiszolgálói, hálózatkezelési és tárolási infrastruktúrát tartalmaz. Kombinálva az adatközponthoz igazított HANA-integráció (TDI) minősítéssel rendelkezik. A SAP HANA az Azure-ban (nagyméretű példányok) különböző kiszolgálói SKU-ket vagy méreteket biztosít. Az egységek 36 Intel CPU-maggal és 768 GB memóriával rendelkezhetnek, és akár 480 Intel CPU-maggal rendelkező, akár 24 TB memóriát tartalmazó egységekre is felmehetnek.

Az infrastruktúra-bélyegzőn belüli ügyfél-elkülönítést a bérlők végzik, amelyek a következőképpen néznek ki:

- **Hálózatkezelés**: Az infrastruktúra-veremben lévő ügyfelek elkülönítése az ügyfél által hozzárendelt bérlői virtuális hálózatokon keresztül. A bérlő egyetlen ügyfélhez van rendelve. Egy ügyfél több Bérlővel is rendelkezhet. A bérlők hálózati elkülönítése tiltja a bérlők közötti hálózati kommunikációt az infrastruktúra-Stamp szintjén, még akkor is, ha a bérlők ugyanahhoz az ügyfélhez tartoznak.
- **Tárolási összetevők**: Elkülönítés olyan tárolási virtuális gépeken keresztül, amelyekhez tárolási kötetek vannak rendelve. A tárolási kötetek csak egy tároló virtuális géphez rendelhetők hozzá. A tároló virtuális gépek kizárólag egyetlen bérlőhöz vannak hozzárendelve a SAP HANA TDI Certified infrastruktúra-veremben. Ennek eredményeképpen a tároló virtuális géphez rendelt tárolási kötetek csak egy adott és kapcsolódó bérlőhöz érhetők el. Nem láthatók a különböző telepített bérlők között.
- **Kiszolgáló vagy gazdagép**: Egy kiszolgáló vagy egy gazda egység nincs megosztva az ügyfelek vagy A bérlők között. Egy ügyfélre telepített kiszolgáló vagy gazdagép egy olyan atomi operációs rendszer nélküli számítási egység, amely egyetlen bérlőhöz van rendelve. *Nincs* szükség hardver particionálásra vagy lágy particionálásra, ami a gazdagép vagy a kiszolgáló másik ügyféllel való megosztását eredményezheti. Az adott bérlő tároló virtuális géphez hozzárendelt tárolási kötetek egy ilyen kiszolgálóhoz vannak csatlakoztatva. A bérlők egy vagy több, kizárólag a különböző SKU-ban hozzárendelt kiszolgálói egységhez tartozhatnak.
- Az Azure-beli (nagyméretű példányok) infrastruktúra-bélyegzőn belüli SAP HANA számos különböző bérlőt helyezünk üzembe és különítenek el egymástól a bérlői fogalmakkal a hálózatkezelés, a tárolás és a számítási szint alapján. 


Ezek az operációs rendszer nélküli kiszolgálói egységek csak SAP HANA futtatására használhatók. A virtuális gépeken futó SAP-alkalmazás rétege vagy munkaterhelése. Az Azure-ban (nagyméretű példányok) lévő SAP HANAt futtató infrastruktúra-bélyegek az Azure hálózati szolgáltatások gerincéhez csatlakoznak. Így az Azure-beli (nagyméretű példányok) egységek és a virtuális gépek közötti SAP HANA kis késleltetésű kapcsolat van megadva.

Július 2019-én megkülönböztetjük a HANA nagy példányszámú bélyegek két különböző változatát és az üzemelő példányok helyét:

- "3. változat" (Rev 3): Az ügyfél számára elérhetővé tett bélyegek a 2019 júliusa előtt lettek telepítve.
- "4. változat" (Rev 4): Új Stamp-kialakítás, amely az Azure-beli virtuális gépek gazdagépei közelében van üzembe helyezve, és amely eddig a következő Azure-régiókban jelent meg:
    -  USA 2. nyugati régiója 
    -  East US 
    -  Nyugat-Európa
    -  Észak-Európa


Ez a dokumentum az Azure-ban (nagyméretű példányok) SAP HANAt lefedő dokumentumok egyike. Ez a dokumentum bemutatja a megoldás által nyújtott alapszintű architektúrát, felelősségeket és szolgáltatásokat. A megoldás magas szintű képességeit is tárgyaljuk. A legtöbb más terület, például a Hálózatkezelés és a kapcsolatok esetében négy további dokumentum részletezi és részletezi az információkat. Az Azure-beli SAP HANA dokumentációja (nagyméretű példányok) nem fedi le a virtuális gépeken futó SAP NetWeaver telepítésének vagy központi telepítésének aspektusait. Az Azure-beli SAP NetWeaver az azonos Azure-beli dokumentációs tárolóban található külön dokumentumokban szerepel. 


A HANA nagyméretű példányok különböző dokumentumai a következő területeket fedik le:

- [SAP HANA (nagyméretű példányok) áttekintése és architektúrája az Azure-ban](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [SAP HANA (nagyméretű példányok) infrastruktúrája és kapcsolódás az Azure-ban](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [SAP HANA (nagyméretű példányok) telepítése és konfigurálása az Azure-ban](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [SAP HANA (nagyméretű példányok) magas rendelkezésre állása és vész-helyreállítás az Azure-ban](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [SAP HANA (nagyméretű példányok) hibaelhárítás és monitorozás az Azure-ban](troubleshooting-monitoring.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Magas rendelkezésre állás beállítása a SUSE-ben a STONITH használatával](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/ha-setup-with-stonith)
- [Az operációs rendszer biztonsági mentése és visszaállítása a 3. típusú bélyegzők II. típusához](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/os-backup-type-ii-skus)

**Következő lépések**
- Tekintse át [a feltételeket](hana-know-terms.md)