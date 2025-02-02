---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 08/08/2019
ms.author: cynthn;kareni
ms.custom: include file
ms.openlocfilehash: b13b809b04f6cf878d68311b756ed2ca826f9697
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68935306"
---
**Utolsó dokumentum frissítése**: 9 augusztus 2019 10:00 AM PST.

Az [új CPU-sebezhetőségi biztonsági rések](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002) , mint a spekulatív végrehajtási oldalsó csatornák elleni támadások, az ügyfelektől származó kérdésekkel kapcsolatos kérdéseket eredményeztek.  

A Microsoft az összes felhőalapú szolgáltatásban üzembe helyezte a megoldásait. Az Azure-t futtató infrastruktúra és az ügyfelek munkaterhelésének elkülönítése védett. Ez azt jelenti, hogy az azonos infrastruktúrát használó lehetséges támadók nem tudják megtámadni az alkalmazást ezen biztonsági rések használatával.

Ha lehetséges, az Azure-ban [memóriát](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates#maintenance-that-doesnt-require-a-reboot) használ a karbantartáshoz, hogy minimálisra csökkentse az ügyfelek hatásait, és szükségtelenné váljon az újraindítások szükségessége. Az Azure továbbra is felhasználja ezeket a metódusokat, amikor rendszerszintű-frissítéseket készít a gazdagépre, és megóvja ügyfeleinket.

További információ arról, hogy az Azure minden aspektusa hogyan integrálható a biztonsággal az [Azure Security dokumentációs](https://docs.microsoft.com/azure/security/) webhelyén. 

> [!NOTE] 
> A dokumentum első közzétételét követően a sebezhetőségi osztály több változata is közzé lett téve. A Microsoft továbbra is nagy mértékben befektetve biztosítja az ügyfelek védelmét és útmutatást nyújt. A rendszer frissíti a lapot, mivel továbbra is további javításokat szabadít fel. 
> 
> 2019. május 14-én az [Intel feltárta](https://www.intel.com/content/www/us/en/security-center/advisory/intel-sa-00233.html) a spekulációs végrehajtási oldalsó csatorna új biztonsági réseit (az MDS a Microsoft biztonsági útmutató [ADV190013](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV190013)), amely több CVEs van hozzárendelve: 
> - CVE-2019-11091 – a architektúrában tárolt adatmintavételezés nem gyorsítótárazható memória (MDSUM)
> - CVE-2018-12126-a architektúrán tárolt pufferek adatmintavételezése (MSBDS) 
> - CVE-2018-12127 – architektúrán belüli betöltési port adatmintavételezése (MLPDS)
> - CVE-2018-12130 – architektúra-kitöltési puffer adatmintavételezése (MFBDS)
>
> Ez a biztonsági rés az Intel® Core® processzorait és az Intel® Xeon® processzorokat érinti.  Microsoft Azure kiadta az operációs rendszer frissítéseit, és új, az Intel által elérhetővé tett, az új biztonsági rések elleni védelem érdekében a flottán keresztül elérhetővé teszi az ügyfeleket.   Az Azure szorosan együttműködik az Inteltel az új programkód teszteléséhez és érvényesítéséhez a platform hivatalos kiadása előtt. 
>
> **Azok a felhasználók, akik nem megbízható kódot futtatnak a virtuális gépen** , lépéseket kell tenniük a biztonsági rések elleni védelemhez, ha további útmutatásra van szüksége az összes spekulációs végrehajtási oldal-csatornás biztonsági réssel kapcsolatban (Microsoft ADVISORs ADV [ 180002](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002), [180018](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/adv180018)és [190013](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV190013)).
>
> A többi ügyfélnek alaposan meg kell vizsgálnia ezeket a biztonsági réseket a védelemből, és figyelembe kell vennie a kiválasztott konfiguráció biztonsági és teljesítménybeli következményeit is.



## <a name="keeping-your-operating-systems-up-to-date"></a>Az operációs rendszerek naprakészen tartása

Habár az operációs rendszer frissítése nem szükséges az Azure-ban más Azure-ügyfelektől futtatott alkalmazások elkülönítéséhez, mindig ajánlott a szoftver naprakészen tartásához. A Windows rendszerhez készült legújabb biztonsági összesítések számos spekulatív végrehajtási oldalsó csatorna biztonsági rések enyhítését tartalmazzák. Hasonlóképpen, a Linux-disztribúciók több frissítést is kiadtak a biztonsági rések kezeléséhez. Az operációs rendszer frissítésének javasolt lépései:

| Ajánlat | Ajánlott művelet  |
|----------|---------------------|
| Azure Cloud Services  | Engedélyezze az [automatikus frissítést](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-configure-portal) , vagy győződjön meg arról, hogy a legújabb vendég operációs rendszert futtatja. |
| Azure Linux Virtual Machines | Telepítse a frissítéseket az operációs rendszer szolgáltatójából. További információkért lásd a jelen dokumentum [Linux](#linux) című részében. |
| Azure-Windows Virtual Machines  | Telepítse a legújabb biztonsági összesítést.
| Egyéb Azure Pásti-szolgáltatások | Ezen szolgáltatásokat használó ügyfelek esetében nincs szükség beavatkozásra. Az Azure automatikusan naprakészen tartja az operációs rendszer verzióját. |

## <a name="additional-guidance-if-you-are-running-untrusted-code"></a>További útmutatás, ha nem megbízható kódot futtat 

Azok az ügyfelek, akik lehetővé teszik, hogy a nem megbízható felhasználók tetszőleges programkódot hajtsanak végre, további biztonsági funkciókat kívánnak megvalósítani az Azure-Virtual Machines vagy Cloud Services. Ezek a funkciók a folyamaton belüli közzétételi vektorokkal szembeni védelmet biztosítanak, amelyek számos spekulációs végrehajtási sebezhetőséget ismertetnek.

Példák olyan esetekre, amikor további biztonsági funkciók használata javasolt:

- Engedélyezheti a virtuális gépen belül nem megbízhatónak ítélt kódokat.  
    - *Tegyük fel például, hogy az egyik ügyfél feltölt egy bináris vagy parancsfájlt, amelyet aztán végrehajt az alkalmazáson belül*. 
- Lehetővé teszi a felhasználók számára, hogy alacsony jogosultsági szintű fiókok használatával jelentkezzen be a virtuális gépre.   
    - *Tegyük fel például, hogy egy alacsony jogosultsági szintű felhasználó bejelentkezik az egyik virtuális gépre a távoli asztal vagy az SSH használatával*.  
- Lehetővé teszi a nem megbízható felhasználók számára a beágyazott virtualizálás használatával megvalósított virtuális gépek elérését.  
    - *Például beállíthatja a Hyper-V-gazdagépet, de a virtuális gépeket nem megbízható felhasználók számára is*kioszthatja. 

Azok a felhasználók, akik nem nem megbízható programkódot alkalmaznak, nem szükséges engedélyezni ezeket a további biztonsági funkciókat. 

## <a name="enabling-additional-security"></a>További biztonság engedélyezése 

Ha nem megbízható kódot futtat, a virtuális gépen vagy a Cloud Service-ben is engedélyezheti a további biztonsági funkciókat. Párhuzamosan gondoskodjon arról, hogy az operációs rendszer naprakész legyen, hogy lehetővé tegye a biztonsági funkciók használatát a virtuális gépen vagy a felhőalapú szolgáltatáson belül

### <a name="windows"></a>Windows 

A cél operációs rendszernek naprakésznek kell lennie a további biztonsági funkciók engedélyezéséhez. Habár számos spekulációs végrehajtási oldali csatorna-megoldás alapértelmezés szerint engedélyezve van, az itt ismertetett további funkciókat manuálisan kell engedélyezni, és a teljesítményre gyakorolt hatást okozhatnak. 


**1. lépés: A Hyper-Threading szolgáltatás letiltása a** virtuális gépen – a nem megbízható programkódot futtató ügyfeleken le kell tiltania a Hyper-Threading szolgáltatást, vagy a nem Hyper-threadd VM-méretre kell lépnie. [Ez a dokumentum](https://docs.microsoft.com/azure/virtual-machines/windows/acu) a Hyper-threaded VM-méretek listájára hivatkozik (ahol a vCPU és a mag aránya 2:1). Annak vizsgálatához, hogy a virtuális gép rendelkezik-e Hyper-Threading szolgáltatással, tekintse meg az alábbi szkriptet a virtuális gépen található Windows-parancssor használatával.

Írja `wmic` be az interaktív interfészt. Ezután írja be az alábbit a virtuális gépen található fizikai és logikai processzorok mennyiségének megtekintéséhez.

```console
CPU Get NumberOfCores,NumberOfLogicalProcessors /Format:List
```

Ha a logikai processzorok száma nagyobb, mint a fizikai processzor (mag), akkor a Hyper-Threading engedélyezve van.  Ha Hyper-threaded virtuális gépet futtat, [forduljon az Azure támogatási szolgálatához](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) a Hyper-Threading letiltásához.  Ha a Hyper-Threading le van tiltva, **a támogatáshoz teljes virtuális gép újraindítására lesz szükség**. Tekintse meg az [alapvető darabszámot](#core-count) , és Ismerje meg, hogy miért csökkent a VM-mag darabszáma.


**2. lépés**: Az 1. lépéssel párhuzamosan a [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) utasításait követve ellenőrizze, hogy a védelem engedélyezve van-e a [SpeculationControl](https://aka.ms/SpeculationControlPS) PowerShell-modul használatával.

> [!NOTE]
> Ha korábban már letöltötte ezt a modult, akkor telepítenie kell a legújabb verziót.
>


A PowerShell-parancsfájl kimenetének az alábbi értékekkel kell rendelkeznie az engedélyezett védelem ellenőrzéséhez a biztonsági rések ellen:

```
Windows OS support for branch target injection mitigation is enabled: True
Windows OS support for kernel VA shadow is enabled: True
Windows OS support for speculative store bypass disable is enabled system-wide: False
Windows OS support for L1 terminal fault mitigation is enabled: True
Windows OS support for MDS mitigation is enabled: True
```

Ha a kimenet látható `MDS mitigation is enabled: False`, [forduljon az Azure támogatási szolgálatához](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) a rendelkezésre álló kockázatcsökkentő lehetőségekért.



**3. lépés**: A kernel virtuális IP-KVAS és az `Session Manager` ág-cél injekciós (KTF) operációs rendszer támogatásának engedélyezéséhez kövesse a [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) utasításait a beállításkulcsok használatával történő védelem engedélyezéséhez. Újraindítás szükséges.


**4. lépés**: [Beágyazott virtualizálási](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization) szolgáltatást használó központi telepítések esetén (csak D3 és E3 esetén): Ezek az utasítások a Hyper-V-gazdagépként használt virtuális gépen belül érvényesek.

1.  Kövesse a [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) utasításait a védelem engedélyezéséhez a `MinVmVersionForCpuBasedMitigations` beállításkulcsok használatával.
2.  Adja meg a hypervisor Scheduler típusát `Core` a következő útmutatás szerint [](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-scheduler-types):.


### <a name="linux"></a>Linux

<a name="linux"></a>A további biztonsági funkciók készletének engedélyezéséhez a cél operációs rendszernek teljesen naprakésznek kell lennie. Bizonyos enyhítések alapértelmezés szerint engedélyezve lesznek. A következő szakasz azokat a funkciókat ismerteti, amelyek alapértelmezés szerint ki vannak kapcsolva, és/vagy a hardveres támogatásra támaszkodnak. A funkciók engedélyezése hatással lehet a teljesítményre. További útmutatást az operációs rendszer szolgáltatójának dokumentációja tartalmaz.


**1. lépés: A Hyper-Threading szolgáltatás letiltása a** virtuális gépen – a nem megbízható programkódot futtató, Hyper-threaded virtuális gépen futó ügyfeleknek le kell tiltaniuk a Hyper-Threading szolgáltatást, vagy át kell térniük egy nem Hyper-szálas virtuális gépre.  [Ez a dokumentum](https://docs.microsoft.com/azure/virtual-machines/linux/acu) a Hyper-threaded VM-méretek listájára hivatkozik (ahol a vCPU és a mag aránya 2:1). A `lscpu` parancs futtatásával ellenőrizze, hogy fut-e Hyper-threaded virtuális gép. 

Ha `Thread(s) per core = 2`a, a Hyper-Threading engedélyezve lett. 

Ha `Thread(s) per core = 1`a, a Hyper-Threading le lett tiltva. 

 
Minta kimenete egy virtuális gép számára, amelyen engedélyezve van a Hyper-Threading: 

```console
CPU Architecture:      x86_64
CPU op-mode(s):        32-bit, 64-bit
Byte Order:            Little Endian
CPU(s):                8
On-line CPU(s) list:   0-7
Thread(s) per core:    2
Core(s) per socket:    4
Socket(s):             1
NUMA node(s):          1

```

Ha Hyper-threaded virtuális gépet futtat, [forduljon az Azure támogatási szolgálatához](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) a Hyper-Threading letiltásához.  Ha a Hyper-Threading le van tiltva, **a támogatáshoz teljes virtuális gép újraindítására lesz szükség**. Tekintse meg az [alapvető darabszámot](#core-count) , és Ismerje meg, hogy miért csökkent a VM-mag darabszáma.



**2. lépés**: Az operációs rendszer szolgáltatójának dokumentációjában az alábbi spekulációs, spekulatív végrehajtással kapcsolatos biztonsági rések elleni védelem érdekében tájékozódhat:   
 
- [RedHat és CentOS](https://access.redhat.com/security/vulnerabilities) 
- [SUSE](https://www.suse.com/support/kb/?doctype%5B%5D=DT_SUSESDB_PSDB_1_1&startIndex=1&maxIndex=0) 
- [Ubuntu](https://wiki.ubuntu.com/SecurityTeam/KnowledgeBase/) 


### <a name="core-count"></a>Magok száma

Egy Hyper-threaded virtuális gép létrehozásakor az Azure 2 szálat foglal le a Core-ban, ezek neve vCPU. Ha a Hyper-Threading szolgáltatás le van tiltva, az Azure eltávolítja a szálat, és felfedi az egyszálas magok (fizikai magok) felszínét. A vCPU és a CPU közötti arány 2:1, így a Hyper-Threading letiltását követően a virtuális gépen lévő CPU-szám a felére csökken. A D8_v3 virtuális gép például egy 8 vCPU futó Hyper-threadd VM (2 szál/mag x 4 mag).  Ha a Hyper-Threading szolgáltatás le van tiltva, a processzorok 4 fizikai magot vesznek, és az összes mag 1 szálat. 

## <a name="next-steps"></a>További lépések

Ez a cikk útmutatást nyújt az alábbi, spekulatív végrehajtást biztosító, számos modern processzort érintő támadásokról:

A [fantom összeomlása](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV180002):
- CVE-2017-5715 – ág céljának befecskendezése (KTF)  
- CVE-2017-5754 – kernel-lapok elkülönítése (KPTI)
- CVE-2018-3639 – spekulatív tároló megkerülése (KPTI) 
- [CVE-2019-1125](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-1125) – Windows kernel Information – a kísértet 1. változatának változata
 
[L1 terminál-hiba (L1TF)](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV180018):
- CVE-2018-3615-Intel Software Guard-bővítmények (Intel SGX ENKLÁVÉHOZ)
- CVE-2018-3620 – operációs rendszerek (OS) és rendszerfelügyeleti mód (SMM)
- CVE-2018-3646 – hatások Virtual Machine Manager (VMM)

A [architektúrában tárolt adatmintavétel](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190013): 
- CVE-2019-11091 – a architektúrában tárolt adatmintavételezés nem gyorsítótárazható memória (MDSUM)
- CVE-2018-12126-a architektúrán tárolt pufferek adatmintavételezése (MSBDS)
- CVE-2018-12127 – architektúrán belüli betöltési port adatmintavételezése (MLPDS)
- CVE-2018-12130 – architektúra-kitöltési puffer adatmintavételezése (MFBDS)








