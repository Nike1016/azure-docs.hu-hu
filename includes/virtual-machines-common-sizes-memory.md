---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 08/08/2019
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 3d45defa9ff8e7b2e03d550b76c0e18192c58c4a
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881595"
---
A memóriára optimalizált virtuálisgép-méretek magas memória-CPU arányt kínálnak, amely kiválóan használható a kapcsolódó adatbázis-kiszolgálók, a közepes és a nagy gyorsítótárak, valamint a memóriabeli elemzések számára. Ez a cikk a vCPU, az adatlemezek és a hálózati adapterek számával, valamint a tárolási teljesítményével és a hálózati sávszélességgel kapcsolatos információkat tartalmaz ebben a csoportosításban.

* A Ev3 sorozat az E5-2673 v4 2,3 GHz-es (Broadwell) processzorát egy Hyper-threaded konfigurációban tartalmazza, ami jobb értéket biztosít a legtöbb általános célú számítási feladathoz, és a Ev3 a legtöbb más felhőben lévő általános célú virtuális gépekre hangolja.  A memória ki lett bővítve (7 GiB/vCPU – 8 GiB/vCPU), míg a lemez-és hálózati korlátokat a rendszer alapszintű módon igazította ki, hogy illeszkedjen a feleznie-re való áttéréshez.  A Ev3 a D/Dv2 családok nagy memóriabeli virtuálisgép-méretének követése.

* A Eav3 sorozat és a Easv3 sorozat az AMD 2.35 GHz EPYC<sup>TM</sup> 7452V processzorát egy többszálas konfigurációban, akár 256GB L3 gyorsítótárral, a legtöbb memóriára optimalizált számítási feladatok futtatásához szükséges lehetőségek növelésével használja fel.  A Eav3-sorozat és a Easv3-sorozat ugyanazokkal a memória-és lemez-konfigurációval rendelkezik, mint a Ev3 & Esv3 sorozat.

* A Mv2 sorozat a legmagasabb vCPU (legfeljebb 208 vCPU) és a legnagyobb (legfeljebb 5,7 TiB) memóriával rendelkezik a felhőben. Ideális a rendkívül nagy méretű adatbázisokhoz vagy olyan egyéb alkalmazásokhoz, amelyek ki tudják használni a nagy vCPU-számot és a nagy memóriamennyiséget.

* Az M sorozat magas vCPU (akár 128 vCPU) és nagy mennyiségű memóriát (legfeljebb 3,8 TiB) kínál. Emellett ideális a rendkívül nagy méretű adatbázisok vagy más alkalmazások számára, amelyek nagy vCPU és nagy mennyiségű memóriát foglalnak magukban.

* A Dv2 sorozat, a G-sorozat és a DSv2/GS-partnerek ideálisak olyan alkalmazások számára, amelyek gyorsabb vCPU, jobb ideiglenes tárolási teljesítményt vagy nagyobb mennyiségű memóriát igényelnek. Nagyon hatékony kombinációt kínálnak számos nagyvállalati szintű alkalmazáshoz.

* A Dv2-sorozat az eredeti D-sorozat újabb verziója, amely nagyobb teljesítményű processzorokat kínál. A Dv2-sorozathoz használt processzor mintegy 35%-kal gyorsabb, mint a D-sorozathoz használt processzorok. A rendszer a legújabb generációs 2,4 GHz-es Intel Xeon® E5-2673 v3 2,4 GHz-es (Haswell) vagy E5-2673 v4 2,3 GHz-es (Broadwell-) processzorok alapján, valamint az Intel Turbo Boost Technology 2,0-es verziójának használatával képes felmenni a 3,1 GHz-es verzióra. A Dv2-sorozat ugyanolyan memória- és lemezkonfigurációkban érhető el, mint a D-sorozat.

* Az Azure Compute olyan virtuálisgép-méreteket kínál, amelyek egy meghatározott hardvertípusban vannak elkülönítve, és egyetlen ügyfél számára vannak fenntartva.  Ezek a virtuálisgép-méretek olyan számítási feladatokhoz megfelelőek, amelyeket magas szintű izolációval szükséges elkülöníteni más ügyfelek számítási feladataitól, beleértve olyan elemeket is mint a megfelelőség vagy a jogszabályi előírások betartása.  Az ügyfelek emellett dönthetnek úgy is, hogy a [beágyazott virtuális gépek Azure](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)-támogatásának használatával tovább alcsoportba helyezik az elkülönített virtuális gépek erőforrásait.  Az elkülönített virtuálisgép-lehetőségekért tekintse meg az alábbi virtuálisgép-családok táblázatait.

## <a name="esv3-series"></a>Esv3 sorozat

ACU: 160-190 <sup>1</sup>

Prémium szintű Storage:  Támogatott

Premium Storage gyorsítótárazás:  Támogatott

Az ESv3-sorozat példányai a 2,3 GHz-es Intel XEON ® E5-2673 v4 (Broadwell) processzoron alapulnak, amelynek az órajele akár 3,5 GHz-re is növelhető az Intel Turbo Boost Technology 2.0 alkalmazásával, valamint prémium szintű tárolás használatára is képes. Az Ev3-sorozat példányai ideálisak a memóriaigényes vállalati alkalmazásokhoz.


| Size             | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Gyorsítótárazott és ideiglenes tárolás max. átviteli sebessége IOPS/MBps (gyorsítótár mérete GiB-ban) | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS / MBps | Hálózati adapterek max. száma / várt hálózati sávszélesség (Mbps) |
|------------------|--------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------------------------|
| Standard_E2s_v3 | 2      | 16          | 32             | 4              | 4000 / 32 (50)                                                       | 3200 / 48                                | 2 / 1000                                   |
| Standard_E4s_v3&nbsp;<sup>2</sup> | 4      | 32          | 64             | 8              | 8000 / 64 (100)                                                      | 6400/96                                | 2 / 2000                                   |
| Standard_E8s_v3&nbsp;<sup>2</sup> | 8      | 64          | 128            | 16             | 16000 / 128 (200)                                                    | 12800 / 192                              | 4 / 4000                                       |
| Standard_E16s_v3&nbsp;<sup>2</sup> | 16     | 128         | 256            | 32             | 32000 / 256 (400)                                                    | 25600 / 384                              | 8 / 8000                                       |
| Standard_E20s_v3                   | 20     | 160         | 320            | 32             | 40000/320 (400)                                                    | 32000/480                              | 8 / 10000                                       |
| &nbsp;<sup>2</sup> . Standard_E32s_v3 | 32     | 256         | 512            | 32             | 64000 / 512 (800)                                                    | 51200 / 768                              | 8 / 16000                             |
| &nbsp;<sup>2</sup> . Standard_E48s_v3 | 48     | 384         | 768            | 32             | 96000/768 (1200)                                                   | 76800 / 1152                             | 8 / 24000                             |
| Standard_E64s_v3&nbsp;<sup>2</sup> | 64     | 432         | 864            | 32             | 128000 / 1024 (1600)                                                   | 80000/1200                             | 8 / 30000                             |
| &nbsp;<sup>3</sup> . Standard_E64is_v3 | 64     | 432         | 864            | 32             | 128000 / 1024 (1600)                                                   | 80000/1200                             | 8 / 30000                             |


<sup>1</sup> a Esv3 SOROZATú virtuális gépek funkciójának Intel® Hyper-Threading technológiája.

<sup>2</sup> korlátozott méretű alapméret érhető el.

<sup>3</sup> a példány egyetlen ügyfél számára dedikált hardveren van elkülönítve.

## <a name="easv3-series"></a>Easv3 sorozat

Prémium szintű Storage: Támogatott

Premium Storage gyorsítótárazás: Támogatott

A Easv3-sorozat méretei a 2.35 GHz-es AMD EPYC<sup>TM</sup> 7452V processzoron alapulnak, amely növelheti a Fmax GHz-es 3.35 és a Premium Storage használatát. A Easv3-sorozat méretei ideálisak a nagy mennyiségű, nagyvállalati alkalmazások számára.

| Size | vCPU | Memória: GiB | Ideiglenes tároló (SSD): GiB |
|---|---|---|---|
| Standard_E2as_v3  | 2  | 16  | 32  |
| Standard_E4as_v3  | 4  | 32  | 64  |
| Standard_E8as_v3  | 8  | 64  | 128 |
| Standard_E16as_v3 | 16 | 128 | 256 |
| Standard_E32as_v3 | 32 | 256 | 512 |
| Standard_E48as_v3 | 48 | 384 | 768 |
| Standard_E64as_v3 | 64 | 432 | 864 |

## <a name="ev3-series"></a>Ev3-sorozat 

ACU: 160 - 190 <sup>1</sup>

Prémium szintű Storage:  Nem támogatott

Premium Storage gyorsítótárazás:  Nem támogatott

Az Ev3-sorozat példányai a 2,3 GHz-es Intel XEON ® E5-2673 v4 (Broadwell) processzoron alapulnak, amelynek az órajele akár 3,5 GHz-re is növelhető az Intel Turbo Boost Technology 2.0 alkalmazásával. Az Ev3-sorozat példányai ideálisak a memóriaigényes vállalati alkalmazásokhoz.

Az adatlemezes tárolást a virtuális gépektől függetlenül számlázzuk. Prémium szintű tárolólemezek használatához ESv3-méreteket vegyen igénybe. Az Esv3-méretek díjszabása és számlázási mérőszámai megegyeznek az Ev3-sorozatéval. 


| Size            | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Maximális Temp Storage-átviteli sebesség: IOPS/olvasási MBps/írási MBps | Hálózati adapterek max. száma/hálózati sávszélesség |
|-----------------|-----------|-------------|----------------|----------------|----------------------------------------------------------|------------------------------|
| Standard_E2_v3  | 2         | 16          | 50             | 4              | 3000/46/23                                               | 2 / 1000                 |
| Standard_E4_v3  | 4         | 32          | 100            | 8              | 6000/93/46                                               | 2 / 2000                 |
| Standard_E8_v3  | 8         | 64          | 200            | 16             | 12000/187/93                                             | 4 / 4000                     |
| Standard_E16_v3 | 16        | 128         | 400            | 32             | 24000/375/187                                            | 8 / 8000                     |
| Standard_E20_v3 | 20        | 160         | 500            | 32             | 30000/469/234                                            | 8 / 10000                     |
| Standard_E32_v3 | 32        | 256         | 800            | 32             | 48000/750/375                                            | 8 / 16000                 |
| Standard_E48_v3 | 48        | 384         | 1200            | 32             | 96000/1000/500                                            | 8 / 24000                 |
| Standard_E64_v3 | 64        | 432         | 1600           | 32             | 96000/1000/500                                           | 8 / 30000           |
| Standard_E64i_v3&nbsp;<sup>2,&nbsp;3</sup> | 64        | 432         | 1600           | 32             | 96000/1000/500                                           | 8 / 30000           |

<sup>1</sup> a Ev3 SOROZATú virtuális gépek funkciójának Intel® Hyper-Threading technológiája.

<sup>2</sup> korlátozott méretű alapméret érhető el.

<sup>3</sup> a példány egyetlen ügyfél számára dedikált hardveren van elkülönítve.

## <a name="eav3-series"></a>Eav3 sorozat

Prémium szintű Storage: Nem támogatott

Premium Storage gyorsítótárazás: Nem támogatott

A Eav3-sorozat méretei a 2.35 GHz-es AMD EPYC<sup>TM</sup> 7452V processzoron alapulnak, amely növelheti a Fmax GHz-es 3.35 és a Premium Storage használatát. A Eav3-sorozat méretei ideálisak a nagy mennyiségű, nagyvállalati alkalmazások számára. Az adatlemezes tárolást a virtuális gépektől függetlenül számlázzuk. A Premium Storage-lemezek használatához használja a Easv3-sorozat méretét. A Easv3-méretek díjszabása és számlázási mérőszámai megegyeznek a Eav3 sorozattal.

| Size | vCPU | Memória: GiB | Ideiglenes tároló (SSD): GiB |
|---|---|---|---|---|---|
| Standard_E2a_v3  | 2  | 16  | 50   |
| Standard_E4a_v3  | 4  | 32  | 100  |
| Standard_E8a_v3  | 8  | 64  | 200  |
| Standard_E16a_v3 | 16 | 128 | 400  |
| Standard_E32a_v3 | 32 | 256 | 800  |
| Standard_E48a_v3 | 48 | 384 | 1200 |
| Standard_E64a_v3 | 64 | 432 | 1600 |

## <a name="mv2-series"></a>Mv2 sorozat

Prémium szintű Storage: Támogatott

Premium Storage gyorsítótárazás: Támogatott

Írásgyorsító: [Támogatott](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

A Mv2 sorozat nagy átviteli sebességű, kis késleltetésű, közvetlenül leképezett helyi NVMe-tárolót használ egy Hyper-threaded Intel® Xeon® Platinum 8180M 2,5 GHz-es (Skylake) processzoron, amely a 2,5 GHz-es és a max. 3,8 GHz-es maximális Turbo gyakorisággal rendelkezik. A Mv2 sorozatú virtuálisgép-méretek standard és prémium szintű állandó lemezeket is használhatnak. A Mv2 sorozat példányai a memóriára optimalizált virtuálisgép-méretek, amelyek páratlan számítási teljesítményt biztosítanak a nagy memóriában lévő adatbázisok és munkaterhelések támogatásához, magas memória-CPU aránnyal, amely ideális a kapcsolódó adatbázis-kiszolgálók, a nagyméretű gyorsítótárak és a memóriában tárolt adatok számára. Analytics. 

|Size | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Gyorsítótárazott és ideiglenes tárolás max. átviteli sebessége IOPS/MBps (gyorsítótár mérete GiB-ban) | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS / MBps | Hálózati adapterek max. száma / várt hálózati sávszélesség (Mbps) |
|-----------------|------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------|
| Standard_M208ms_v2<sup>1, 2</sup> | 208 | 5700 | 4096 | 64 | 80000/800 (7040) | 40000/1000 | 8 / 16000 |
| Standard_M208s_v2<sup>1, 2</sup> | 208 | 2850 | 4096 | 64 | 80000/800 (7040) | 40000/1000 | 8 / 16000 |

A Mv2 sorozatú virtuális gépek funkciójának Intel® Hyper-Threading technológiája  

<sup>1</sup> ezek a nagyméretű virtuális gépek a következő támogatott vendég operációs rendszer egyikét igénylik: Windows Server 2016, Windows Server 2019, SLES 12 SP4, SLES 15.

<sup>2</sup> a Mv2 sorozatú virtuális gépek csak 2. generációsak. Ha Linuxot használ, tekintse meg a következő szakaszt a SUSE Linux-rendszerképek megkereséséhez és kiválasztásához.

#### <a name="find-a-suse-image"></a>SUSE-rendszerkép keresése

Egy megfelelő SUSE Linux-rendszerkép kiválasztásához a Azure Portalban: 

1. A Azure Portal válassza az **erőforrás létrehozása** lehetőséget. 
1. A "SUSE SAP" kifejezés keresése 
1. Az SAP 2. generációs SLES az utólagos elszámolású, vagy saját előfizetést (BYOS) is elérhetővé teheti. A keresési eredmények között bontsa ki a kívánt rendszerkép kategóriáját:

    * SUSE Linux Enterprise Server (SLES) az SAP-hoz
    * SUSE Linux Enterprise Server (SLES) for SAP (BYOS)
    
1. A Mv2-sorozattal kompatibilis SUSE-lemezképek előtaggal vannak ellátva a névvel `GEN2:`. A Mv2 sorozatú virtuális gépekhez a következő SUSE-lemezképek érhetők el:

    * GEN2 SUSE Linux Enterprise Server (SLES) 12 SP4 SAP-alkalmazásokhoz
    * GEN2 SUSE Linux Enterprise Server (SLES) 15 SAP-alkalmazásokhoz
    * GEN2 SUSE Linux Enterprise Server (SLES) 12 SP4 SAP-alkalmazásokhoz (BYOS)
    * GEN2 SUSE Linux Enterprise Server (SLES) 15 SAP-alkalmazásokhoz (BYOS)

#### <a name="select-a-suse-image-via-azure-cli"></a>SUSE-rendszerkép kiválasztása az Azure CLI-n keresztül

A Mv2 sorozatú virtuális gépekhez jelenleg elérhető SLES listáját az alábbi [`az vm image list`](https://docs.microsoft.com/cli/azure/vm/image?view=azure-cli-latest#az-vm-image-list) paranccsal tekintheti meg:

```azurecli
az vm image list --output table --publisher SUSE --sku gen2 --all
```

A parancs kimenete a SUSE Mv2 sorozatú virtuális gépekhez elérhető, jelenleg elérhető 2. generációs virtuális gépeket adja eredményül. 

Példa a kimenetre:

```
Offer          Publisher  Sku          Urn                                        Version
-------------  ---------  -----------  -----------------------------------------  ----------
SLES-SAP       SUSE       gen2-12-sp4  SUSE:SLES-SAP:gen2-12-sp4:2019.05.13       2019.05.13
SLES-SAP       SUSE       gen2-15      SUSE:SLES-SAP:gen2-15:2019.05.13           2019.05.13
SLES-SAP-BYOS  SUSE       gen2-12-sp4  SUSE:SLES-SAP-BYOS:gen2-12-sp4:2019.05.13  2019.05.13
SLES-SAP-BYOS  SUSE       gen2-15      SUSE:SLES-SAP-BYOS:gen2-15:2019.05.13      2019.05.13
```

## <a name="m-series"></a>M sorozat 

ACU: 160-180 <sup>1</sup>

Prémium szintű Storage:  Támogatott

Premium Storage gyorsítótárazás:  Támogatott

Írásgyorsító:  [Támogatott](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

| Size            | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Gyorsítótárazott és ideiglenes tárolás max. átviteli sebessége IOPS/MBps (gyorsítótár mérete GiB-ban) | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS / MBps | Hálózati adapterek max. száma / várt hálózati sávszélesség (Mbps) |
|-----------------|------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------|
| &nbsp;<sup>3</sup> . Standard m8ms    | 8  | 218,75 | 256  | 8  | 10000/100 (793)  | 5000  / 125 | 4 / 2000 |
| Standard_M16ms&nbsp;<sup>3</sup>   | 16 | 437,5  | 512  | 16 | 20000 / 200 (1587) | 10000/250 | 8 / 4000 |
| Standard m32ts | 32 | 192    | 1024 | 32 | 40000/400 (3174) | 20000/500 | 8 / 8000 |
| Standard m32ls | 32 | 256    | 1024 | 32 | 40000/400 (3174) | 20000/500 | 8 / 8000 |
| &nbsp;<sup>3</sup> . Standard m32ms   | 32 | 875    | 1024 | 32 | 40000/400 (3174) | 20000/500 | 8 / 8000 |
| Standard m64s  | 64 | 1024   | 2048 | 64 | 80000 / 800 (6348)| 40000/1000 | 8 / 16000          |
| Standard m64ls  | 64 | 512    | 2048 | 64 | 80000 / 800 (6348) | 40000/1000 | 8 / 16000 |
| &nbsp;<sup>3</sup> . Standard m64ms  | 64   | 1792 | 2048 | 64 | 80000 / 800 (6348)| 40000/1000 | 8 / 16000          |
| &nbsp;<sup>2</sup> . Standard m128s | 128  | 2048        | 4096  | 64 | 160000 / 1600 (12696) | 80000 / 2000                            | 8 / 30000          |
| Standard m128ms&nbsp;<sup>2,&nbsp;3,&nbsp;4</sup> | 128  | 3892  | 4096 | 64 | 160000 / 1600 (12696) | 80000 / 2000                            | 8 / 30000          |
| Standard_M64   | 64  | 1024 | 7168  | 64 | 80000/800 (1228) | 40000/1000 | 8 / 16000 |
| Standard_M64m  | 64  | 1792 | 7168  | 64 | 80000/800 (1228) | 40000/1000 | 8 / 16000 |
| Standard_M128&nbsp;<sup>2  | 128 | 2048 | 14336 | 64 | 250000 / 1600 (2456) | 80000 / 2000 | 8 / 32000 |
| Standard_M128m&nbsp;<sup>2 | 128 | 3892 | 14336 | 64 | 250000 / 1600 (2456) | 80000 / 2000 | 8 / 32000 |



<sup>1</sup> M sorozatú virtuális gép funkciójának Intel® Hyper-Threading technológiája

<sup>2</sup> több mint 64 vCPU esetén a következő támogatott vendég operációs rendszer egyike szükséges: Windows Server 2016, Ubuntu 16,04 LTS, SLES 12 SP2 és Red Hat Enterprise Linux, CentOS 7,3 vagy Oracle Linux 7,3 és LIS 4.2.1.

<sup>3</sup> korlátozott méretű alapméret érhető el.

<sup>4</sup> a példány egyetlen ügyfél számára dedikált hardveren van elkülönítve.
<br>


## <a name="dsv2-series-11-15"></a>DSv2 – 11-15-es sorozat

ACU: 210 – 250 <sup>1</sup>

Prémium szintű Storage:  Támogatott

Premium Storage gyorsítótárazás:  Támogatott

| Size | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Gyorsítótárazott és ideiglenes tárolás max. átviteli sebessége IOPS/MBps (gyorsítótár mérete GiB-ban) | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS / MBps | Hálózati adapterek max. száma / várt hálózati sávszélesség (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11_v2&nbsp;<sup>3</sup> |2 |14 |28 |8 |8000 / 64 (72) |6400/96 |2 / 1500 |
| Standard_DS12_v2&nbsp;<sup>3</sup> |4 |28 |56 |16 |16000 / 128 (144) |12800 / 192 |4 / 3000 |
| Standard_DS13_v2&nbsp;<sup>3</sup> |8 |56 |112 |32 |32000 / 256 (288) |25600 / 384 |8 / 6000 |
| Standard_DS14_v2&nbsp;<sup>3</sup>|16 |112 |224 |64 |64000 / 512 (576) |51200 / 768 |8 / 12000 |
| Standard_DS15_v2&nbsp;<sup>2</sup> |20 |140 |280 |64 |80000 / 640 (720) |64000 / 960 |8 / 25000&nbsp;<sup>4</sup>

<sup>1</sup> a DSv2 sorozatú virtuális géppel lehetséges maximális átviteli sebesség (IOPS vagy Mbps) a csatlakoztatott lemez (ek) számával, méretétől és csíkozásával korlátozható.  Részletekért lásd: [a nagy teljesítmény kialakítása](../articles/virtual-machines/windows/premium-storage-performance.md).  
<sup>2</sup> a példány egyetlen ügyfél számára dedikált hardveren van elkülönítve.  
<sup>3</sup> korlátozott méretű alapméret érhető el.  
<sup>4</sup> 25000 Mbps gyorsított hálózatkezeléssel. 

<br>

## <a name="dv2-series-11-15"></a>Dv2 – 11-15-es sorozat

ACU: 210 – 250

Prémium szintű Storage:  Nem támogatott

Premium Storage gyorsítótárazás:  Nem támogatott

| Méret              | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Maximális Temp Storage-átviteli sebesség: IOPS/olvasási MBps/írási MBps | Adatlemezek maximális száma/átviteli sebesség: IOPS | Hálózati adapterek max. száma / várt hálózati sávszélesség (Mbps) |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D11_v2   | 2         | 14          | 100            | 6000 / 93 / 46                                           | 8 / 8x500                         | 2 / 1500                     |
| Standard_D12_v2   | 4         | 28          | 200            | 12000 / 187 / 93                                         | 16 / 16x500                         | 4 / 3000                     |
| Standard_D13_v2   | 8         | 56          | 400            | 24000 / 375 / 187                                        | 32 / 32x500                       | 8 / 6000                     |
| Standard_D14_v2   | 16        | 112         | 800            | 48000 / 750 / 375                                        | 64 / 64x500                       | 8 / 12000          |
| Standard_D15_v2&nbsp;<sup>1</sup> | 20        | 140         | 1000          | 60000 / 937 / 468                                        | 64 / 64x500                       | 8 / 25000&nbsp;<sup>2</sup> |

<sup>1</sup> példány az egyetlen ügyfél számára dedikált hardveren van elkülönítve.  
<sup>2</sup> 25000 Mbps gyorsított hálózatkezeléssel. 
