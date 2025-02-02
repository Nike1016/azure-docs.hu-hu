---
title: A SAP HANA tárolási architektúrája az Azure-ban (nagyméretű példányok) | Microsoft Docs
description: Tárolási architektúrája SAP HANA üzembe helyezése az Azure-ban (nagyméretű példányok).
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
ms.date: 07/04/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b303a18d481ae1a682d81d87e7c14060ffdfaf14
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2019
ms.locfileid: "67869182"
---
# <a name="sap-hana-large-instances-storage-architecture"></a>SAP HANA (nagyméretű példányok) tárolási architektúrája

Az Azure-beli SAP HANA (nagyméretű példányok) tárolási elrendezését SAP HANA a klasszikus üzemi modellen, az SAP által ajánlott irányelvek szerint konfigurálja. Az irányelvek dokumentációját a [SAP HANA Storage-követelmények](https://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) című tanulmány ismerteti.

Az I. típusú HANA nagy példánya a memória kötetét a tárolási kötetként négyszer adja meg. A HANA Large instances-egységek II. típusú osztálya esetében a tárterület nem sokkal többször van. Az egységek olyan kötettel rendelkeznek, amely a HANA-tranzakciós naplók biztonsági másolatainak tárolására szolgál. További információ: [SAP HANA (nagyméretű példányok) telepítése és konfigurálása az Azure](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)-ban.

Tekintse meg az alábbi táblázatot a tárolók kiosztása szempontjából. A táblázat felsorolja a különböző HANA nagyméretű példány-egységekhez biztosított különböző kötetek durva kapacitását.

| HANA nagyméretű példány SKU | Hana/adatvédelem | Hana/log | Hana/Shared | Hana/logbackups |
| --- | --- | --- | --- | --- |
| S72 | 1 280 GB | 512 GB | 768 GB | 512 GB |
| S72m | 3 328 GB | 768 GB |1 280 GB | 768 GB |
| S96 | 1 280 GB | 512 GB | 768 GB | 512 GB |
| S192 | 4,608 GB | 1 024 GB | 1 536 GB | 1 024 GB |
| S192m | 11 520 GB | 1 536 GB | 1 792 GB | 1 536 GB |
| S192xm |  11 520 GB |  1 536 GB |  1 792 GB |  1 536 GB |
| S384 | 11 520 GB | 1 536 GB | 1 792 GB | 1 536 GB |
| S384m | 12 000 GB | 2 050 GB | 2 050 GB | 2 040 GB |
| S384xm | 16 000 GB | 2 050 GB | 2 050 GB | 2 040 GB |
| S384xxm |  20 000 GB | 3 100 GB | 2 050 GB | 3 100 GB |
| S576m | 20 000 GB | 3 100 GB | 2 050 GB | 3 100 GB |
| S576xm | 31 744 GB | 4,096 GB | 2 048 GB | 4,096 GB |
| S768m | 28 000 GB | 3 100 GB | 2 050 GB | 3 100 GB |
| S768xm | 40 960 GB | 6 144 GB | 4,096 GB | 6 144 GB |
| S960m | 36 000 GB | 4 100 GB | 2 050 GB | 4 100 GB |


A tényleges üzembe helyezett kötetek a telepítés és a kötetek méretének megjelenítéséhez használt eszköz alapján változhatnak.

Ha egy HANA nagyméretű példány SKU-t oszt ki, néhány példa a lehetséges osztási darabokra:

| Memória partíciója GB-ban | Hana/adatvédelem | Hana/log | Hana/Shared | Hana/log/Backup |
| --- | --- | --- | --- | --- |
| 256 | 400 GB | 160 GB | 304 GB | 160 GB |
| 512 | 768 GB | 384 GB | 512 GB | 384 GB |
| 768 | 1 280 GB | 512 GB | 768 GB | 512 GB |
| 1,024 | 1 792 GB | 640 GB | 1 024 GB | 640 GB |
| 1,536 | 3 328 GB | 768 GB | 1 280 GB | 768 GB |


Ezek a méretek olyan durva kötetek, amelyek az üzembe helyezés és a kötetek megkereséséhez használt eszközök alapján némileg változhatnak. Más partíciós méretek is megtalálhatók, például 2,5 TB. A tárolási méretek kiszámítása az előző partíciókhoz használthoz hasonló képlettel történik. A "Partitions" kifejezés nem azt jelenti, hogy az operációs rendszer, a memória vagy a CPU-erőforrás semmilyen módon particionálva van. Azt jelzi, hogy a különböző HANA-példányokhoz tartozó tárolási partíciók egyetlen HANA nagyméretű példány-egységen is üzembe helyezhetők. 

Lehet, hogy több tárterületre van szüksége. Az 1 TB-os egységek további tárterületének megvásárlásával adhat hozzá tárhelyet. Ez a további tárterület további kötetként is felvehető. A meglévő kötetek közül egy vagy több kibővítésére is felhasználható. Nem lehetséges a kötetek méretének csökkentése az eredetileg üzembe helyezett és többnyire az előző táblázatokban dokumentált módon. Emellett nem lehet módosítani a kötetek vagy a csatlakoztatási nevek nevét. A korábban leírt tárolási kötetek a HANA nagyméretű példány-egységekhez vannak csatolva NFS4-kötetként.

A biztonsági mentéshez és helyreállításhoz, valamint a vész-helyreállítási célokra használható tárolási Pillanatképek. További információ: [SAP HANA (nagyméretű példányok) magas rendelkezésre állása és vész-helyreállítás az Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)-ban.

Tekintse át a [HLI által támogatott forgatókönyveket](hana-supported-scenario.md) a tárolási elrendezés részleteihez.

## <a name="run-multiple-sap-hana-instances-on-one-hana-large-instance-unit"></a>Több SAP HANA példány futtatása egy HANA nagyméretű példány-egységen

Több aktív SAP HANA példány is üzemeltethető HANA nagyméretű példány-egységeken. A tárolási Pillanatképek és a vész-helyreállítási funkciók biztosításához a konfigurációhoz egy példány szükséges. A HANA nagyméretű példányai jelenleg a következőképpen oszthatók fel:

- **S72, S72m, S96, S144, S192**: 256 GB-os növekmények esetén 256 GB a legkisebb kezdő egységgel. A különböző növekmények (például 256 GB és 512 GB) kombinálhatók az egység memóriájának maximális értékével.
- **S144m és S192m**: 256 GB-os növekményekben, 512 GB a legkisebb egységgel. A különböző növekmények (például 512 GB és 768 GB) kombinálhatók az egység memóriájának maximális értékével.
- **Type II osztály**: 512 GB-onként, a legkisebb kezdő egységgel 2 TB. A különböző növekmények (például 512 GB, 1 TB és 1,5 TB) kombinálhatók az egység memóriájának maximális értékével.

Néhány példa több SAP HANA-példány futtatására a következőhöz hasonló lehet.

| SKU | Memória mérete | Tárterület mérete | Méretek több adatbázissal |
| --- | --- | --- | --- |
| S72 | 768 GB | 3 TB | 1x768 – GB HANA-példány<br /> vagy 1x512-GB példány + 1x256-GB példány<br /> vagy 3x256-GB példányok | 
| S72m | 1,5 TB | 6 TB | 3x512GB HANA-példányok<br />vagy 1x512-GB példány + 1x1-TB példány<br />vagy 6x256-GB példányok<br />vagy 1x 1.5 TB-példány | 
| S192m | 4 TB | 16 TB | 8x512 – GB-példányok<br />vagy 4x1-TB példányok<br />vagy 4x512-GB instances + 2x1-TB példányok<br />vagy 4x768-GB-példányok + 2x512-GB példányok<br />vagy 1x4-TB-példány |
| S384xm | 8 TB | 22 TB | 4x2 – TB-példányok<br />vagy 2x4-TB példányok<br />vagy 2x3-TB-példányok + 1x2-TB-példányok<br />vagy 2x 2,5 – TB példányok + 1x3-TB példányok<br />vagy 1x8-TB-példány |


Más változatok is vannak. 

## <a name="encryption-of-data-at-rest"></a>A tárolt adatok titkosítása
A HANA nagyméretű példányhoz használt tárterület transzparens titkosítást használ az adatlemezeken, mivel az a 2018-as év vége óta a lemezen tárolódik. A korábbi telepítések esetében a kötetek titkosítását is választhatja. Ha ezt a lehetőséget választja, kérheti a kötetek online titkosítását. A nem titkosítottról titkosított kötetekre való áthelyezés átlátható, és nem igényel állásidőt. 

Az I. típusú SKU-I osztály használata esetén a rendszer titkosítja a rendszerindító LUN-t tároló kötetet. A 3. változat HANA nagyméretű példányú bélyegek, a HANA nagyméretű példányának II. típusának használatával a rendszerindító LUN-t az operációs rendszer módszereivel kell titkosítani. 4\. változat HANA nagy példányszámú bélyegzők, a II. típusú adategységek használatával a rendszerindító LUN tárolja a kötetet, és alapértelmezés szerint titkosítva van a nyugalmi állapotban. 

## <a name="required-settings-for-larger-hana-instances-on-hana-large-instances"></a>Nagyobb HANA-példányok kötelező beállításai a HANA nagyméretű példányain
A HANA nagyméretű példányaiban használt tárterület fájlméret-korlátozást tartalmaz. A [méretre vonatkozó korlátozás 16 TB](https://docs.netapp.com/ontap-9/index.jsp?topic=%2Fcom.netapp.doc.dot-cm-vsmg%2FGUID-AA1419CF-50AB-41FF-A73C-C401741C847C.html) /fájl. Az EXT3 fájlrendszerbeli korlátozásokkal ellentétben a HANA nem ismeri implicit módon a HANA Large instances Storage által kényszerített tárolási korlátozást. Ennek eredményeképpen a HANA nem hoz létre automatikusan új adatfájlt, ha a 16TB elérte a fájlméretet. Mivel a HANA a 16 TB-nál nagyobb mennyiségű fájlt próbál növelni, a HANA hibát jelez, és az index-kiszolgáló összeomlik a végén.

> [!IMPORTANT]
> Annak megakadályozása érdekében, hogy a HANA a Hana nagyméretű példányok tárterületének 16 TB-os fájlméretén kívüli adatfájlokat próbáljon növelni, a következő paramétereket kell megadnia a HANA globális. ini konfigurációs fájljához.
> 
> - datavolume_striping=true
> - datavolume_striping_size_gb = 15000
> - Lásd még: SAP-Megjegyzés [#2400005](https://launchpad.support.sap.com/#/notes/2400005)
> - Vegye figyelembe az SAP-Megjegyzés [#2631285](https://launchpad.support.sap.com/#/notes/2631285)




**Következő lépések**
- Tekintse át [a HANA nagyméretű példányainak támogatott forgatókönyveit](hana-supported-scenario.md)