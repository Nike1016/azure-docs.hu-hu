---
title: Támogatott operációs rendszerek, tároló motorok – Azure IoT Edge |} A Microsoft Docs
description: Ismerje meg, melyik operációs rendszerek futtathatók az Azure IoT Edge-démon és a futtatókörnyezeti és az éles eszközök támogatott tároló-motorok
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/13/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 81d19552b56de540f235960c498c64e7b276320c
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69030966"
---
# <a name="azure-iot-edge-supported-systems"></a>Az Azure IoT Edge által támogatott rendszerek

Ez a cikk részletesen ismerteti, hogy mely rendszerek és összetevők támogatottak a IoT Edge, akár hivatalosan, akár előzetes verzióban. 

Ha a Azure IoT Edge szolgáltatás használata során problémákba ütközik, többféleképpen is kérhet támogatást. Próbálja ki a következő csatornák egyikét a támogatáshoz:

**Hibák Reporting** – a legtöbb fejlesztési, amely az Azure IoT Edge-termék hiányzóra történik, az IoT Edge nyílt forráskódú projekt. A hibák jelenteni lehet a [problémák lap](https://github.com/azure/iotedge/issues) a projekt. Javítások gyorsan eljutnak a projektből a termékfrissítéseket.

**A Microsoft ügyfél-támogatási csapatának** – felhasználók, akik rendelkeznek egy [támogatási csomag](https://azure.microsoft.com/support/plans/) hozzon létre egy támogatási jegyet közvetlenül a Microsoft ügyfél-támogatási csapata is léphet az [az Azure portal](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac).

**Kérelmek funkció** – az Azure IoT Edge-termék nyomon követi a szolgáltatással kapcsolatos kéréseit a termék keresztül [User Voice lap](https://feedback.azure.com/forums/907045-azure-iot-edge).

## <a name="container-engines"></a>Tároló-motorok

Azure IoT Edge-modulok tárolóként vannak megvalósítva, ezért IoT Edge a tároló motorjának kell elindítania azokat. A Microsoft biztosít egy tároló motor moby-motor, ez a követelmény teljesítéséhez. Ez a tároló motor a Moby nyílt forráskódú projekten alapul. A docker CE és a Docker EE más népszerű motorokkal. Emellett a nyílt forráskódú Moby-projekten alapulnak, és kompatibilisek Azure IoT Edgeokkal. A Microsoft a tároló-motorokat használó rendszerek esetében nyújt legjobb támogatást. a Microsoft azonban nem tudja kiszolgálni a javításokat a bennük felmerülő problémák miatt. Ebből kifolyólag a Microsoft azt javasolja, az éles rendszereket moby-motor használatával.

<br>
<center>

![Moby as Container Runtime](./media/support/only-moby-for-production.png)
</center>

## <a name="operating-systems"></a>Operációs rendszerek
Azure IoT Edge a tárolók futtatására képes legtöbb operációs rendszeren fut; Ezek a rendszerek azonban nem egyformán támogatottak. Operációs rendszerek számíthatnak támogatás szintjét képviselő szinten vannak csoportosítva.
* Az 1. szintű rendszerek támogatottak. Az 1. szintű rendszerek esetében a Microsoft:
    * az operációs rendszer automatikus tesztek
    * biztosít a számukra a telepítési csomagok
* A 2. szintű rendszerek kompatibilisek Azure IoT Edgeekkel, és viszonylag könnyen használhatók. 2\. szintű rendszerek esetén:
    * A Microsoft ad hoc tesztelést végez a platformokon, vagy tudja, hogy egy partner sikeresen futtatott Azure IoT Edge a platformon
    * Más platformok telepítőcsomagok ezeken a platformokon is működhet.
    
A gazda operációs rendszer családjának mindig egyeznie kell a modul tárolójában használt vendég operációs rendszer családjának. Ez azt jelenti, hogy a Linux-tárolókat csak Linux-és Windows-tárolókban használhatja Windows rendszeren. A Windows használata esetén csak az elkülönített tárolók feldolgozása támogatott, a Hyper-V elkülönített tárolók nem.  

<br>
<center>

![A gazdagép operációs rendszere megfelel a vendég operációs rendszernek](./media/support/edge-on-device.png)
</center>

### <a name="tier-1"></a>1\. szint

A Microsoft a következő táblázatban felsorolt rendszereket támogatja: általánosan elérhető vagy nyilvános előzetes verzióban, és minden új kiadással tesztelve van. 

| Operációs rendszer | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| A stretch Raspbian |  | ![Raspbian stretch + ARM32v7](./media/tutorial-c-module/green-check.png) |  |
| Ubuntu Server 16.04 | ![Ubuntu Server 16,04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Nyilvános előzetes  |
| Ubuntu Server 18.04 | ![Ubuntu Server 18,04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Nyilvános előzetes |
| Windows 10 IoT Enterprise, Build 17763 | ![Windows 10 IoT Enterprise + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| Windows Server 2019, 17763-es Build | ![Windows Server 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| Windows Server IoT 2019, Build 17763 | ![Windows Server IoT 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| Windows 10 IoT Core, Build 17763 | ![Windows IoT Core + AMD64](./media/tutorial-c-module/green-check.png) |  |  |


A fent felsorolt Windows operációs rendszerek a Windows-tárolókat futtató eszközökre vonatkozó követelmények a Windows rendszeren, amely az egyetlen támogatott konfiguráció az éles környezetben. A Windows Azure IoT Edge telepítési csomagjai lehetővé teszik a Linux-tárolók használatát Windows rendszeren; Ez a konfiguráció azonban csak fejlesztési és tesztelési célokra használható. További információ: [IoT Edge használata Windows rendszeren Linux-tárolók futtatásához](how-to-install-iot-edge-windows-with-linux.md).

### <a name="tier-2"></a>2\. szint

Az alábbi táblázatban felsorolt rendszerek kompatibilisek Azure IoT Edgeekkel, de aktívan nem tesztelik és nem tartják karban őket. 

| Operációs rendszer | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| CentOS 7.5 | ![CentOS + AMD64](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM64](./media/tutorial-c-module/green-check.png) |
| Debian 8 | ![Debian 8 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM64](./media/tutorial-c-module/green-check.png) |
| Debian 9 | ![Debian 9 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM64](./media/tutorial-c-module/green-check.png) |
| Debian 10<sup>1</sup> | ![Debian 10 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM64](./media/tutorial-c-module/green-check.png) |
| RHEL 7.5 | ![RHEL 7,5 + AMD64](./media/tutorial-c-module/green-check.png) | ![RHEL 7,5 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![RHEL 7,5 + ARM64](./media/tutorial-c-module/green-check.png) |
| Ubuntu 16.04 | ![Ubuntu 16,04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16,04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16,04 + ARM64](./media/tutorial-c-module/green-check.png) |
| Ubuntu 18.04 | ![Ubuntu 18,04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18,04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18,04 + ARM64](./media/tutorial-c-module/green-check.png) |
| A szél folyó 8 | ![A Wind River 8 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| Yocto | ![Yocto + AMD64](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM64](./media/tutorial-c-module/green-check.png) |
| Raspbian Buster<sup>1</sup> |  | ![Raspbian Buster + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Raspbian Buster + ARM64](./media/tutorial-c-module/green-check.png) |

<sup>1</sup> a Debian 10 rendszer, köztük a Raspian Buster, az OpenSSL olyan verzióját használja, IoT Edge nem támogatja. A következő parancs használatával telepítsen egy korábbi verziót a IoT Edge telepítése előtt: 

```bash
sudo apt-get install libssl1.0.2
```

## <a name="virtual-machines"></a>Virtuális gépek
A Azure IoT Edge virtuális gépeken is futtathatók. A virtuális gépek IoT Edge eszközként való használata gyakori, ha az ügyfelek a meglévő infrastruktúrát szeretnék kibővíteni a peremhálózati intelligenciával. A gazda VM operációs rendszer családjának meg kell egyeznie a modul tárolójában használt vendég operációs rendszer családjának. Ez a követelmény ugyanaz, mint amikor a Azure IoT Edge közvetlenül egy eszközön fut. Azure IoT Edge a mögöttes virtualizációs technológia, és olyan platformokon működik, mint például a Hyper-V és a vSphere.

<br>
<center>

![Azure IoT Edge egy virtuális gépen](./media/support/edge-on-vm.png)
</center>

## <a name="minimum-system-requirements"></a>Minimális rendszerkövetelmények
A Azure IoT Edge nagyszerűen fut az eszközökön, mint a Pi3 a kiszolgálói minőségű hardverek számára. Ha kiválasztja a megfelelő hardvert a forgatókönyvhöz, a futtatni kívánt munkaterheléstől függ. Az eszköz végső döntése bonyolult lehet; a hagyományos laptopokon és asztali számítógépeken azonban könnyedén elindíthatja a prototípust.

A prototípus-készítés során a rendszer segít a végső eszköz kiválasztásában. Vegye figyelembe a következőket: 

* Hány modul van a munkaterhelésben?
* Hány réteget osztanak meg a modulok tárolói?
* Milyen nyelven íródott modulok? 
* Mennyi adattal fog dolgozni a modulok feldolgozása?
* Szükség van-e a modulok speciális hardverre a számítási feladatok felgyorsításához?
* Mik a megoldás kívánt teljesítménybeli jellemzői?
* Mi a hardveres költségkeret?
