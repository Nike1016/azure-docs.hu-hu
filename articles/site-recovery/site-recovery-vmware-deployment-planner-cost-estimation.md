---
title: Tekintse át a Azure Site Recovery Deployment Planner becsült költségbecslés jelentését | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan tekintheti át a Azure Site Recovery Deployment Planner a VMware – Azure vész-helyreállítási szolgáltatáshoz tartozó költségbecslés jelentését.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 7/29/2019
ms.author: mayg
ms.openlocfilehash: 1f825b67baf36c9a1a9187d555522f5a5955d1c7
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68620074"
---
# <a name="review-the-cost-estimation-report-in-the-site-recovery-deployment-planner-for-vmware-disaster-recovery-to-azure"></a>Tekintse át a Site Recovery Deployment Planner az Azure-ba történő VMware vész-helyreállításra vonatkozó költségbecslés jelentését

A Deployment Planner jelentése a [Javaslatok](site-recovery-vmware-deployment-planner-analyze-report.md#recommendations) táblázatokban költségbecslési összefoglalást, a Költségbecslés táblázatban pedig részletes költségelemzést biztosít. Részletes költségelemzést tartalmaz virtuális gépenként. 

>[!Note]
>A Deployment Planner Tool 1.0-s verziójának aktuális verziója a Managed Disks-ba replikált virtuális gépek költséghatékonyságát tartalmazza.

### <a name="cost-estimation-summary"></a>Költségbecslés összefoglalása 
Az ábra a választott célrégió és a jelentéskészítéshez megadott pénznem vészhelyreállításának (DR) becsült, teljes költségeinek összesített nézetét mutatja be az Azure-hoz.
Költségbecslés összefoglalása

![Költségbecslés összefoglalása](media/site-recovery-vmware-deployment-planner-analyze-report/cost-estimation-summary-v2a.png)

Az összefoglalás segít megismerni a tárolás, számítás, hálózat és licenc költségeit, amelyet akkor kell fizetnie, ha az Azure-ban lévő összes kompatibilis virtuális gépet az Azure Site Recovery használatával védi. A költségek kiszámítása a kompatibilis virtuális gépek, nem pedig a profilkészítésben részt vevő virtuális gépek alapján történik.  
 
A költségeket havi vagy éves bontásban tekintheti meg. További információkat olvashat a [támogatott célrégiókról](./site-recovery-vmware-deployment-planner-cost-estimation.md#supported-target-regions) és a [támogatott pénznemekről](./site-recovery-vmware-deployment-planner-cost-estimation.md#supported-currencies).

**Cost by Components** A DR teljes költsége négy összetevőre oszlik: Számítási, tárolási, hálózati és Azure Site Recovery licencek díja. A költségeket a használat alapján számoljuk ki, amely költségek a replikáció során és a számítás, tárterület (prémium és standard), a helyszíni hely és az Azure között konfigurált ExpressRoute/VPN, valamint az Azure Site Recovery licence DR működési ideje során lépnek fel.

**Állapot szerinti költségek**: A vészhelyreállítás (DR) teljes költségeinek kategóriái két különböző állapoton alapulnak – Replikáció és DR működése. 

**Replikálási díj**:  A replikáció során felmerülő költségek. Ez fedezi a tárolás, a hálózat és az Azure Site Recovery-licenc költségeit. 

**Dr**működésének díja: A feladatátvételi teszt során felmerülő költségek. Az Azure Site Recovery virtuális gépeket indít el a feladatátvételi tesztek során. A DR működési költségei a futó virtuális gépek számítási és tárolási költségeit fedezik. 

**Az Azure Storage tárterület éves/havi költségei** A tárterület teljes költségeit mutatja, amelyek a replikációhoz és a DR működésekor használt prémium és standard tárterület esetén merülnek fel.

## <a name="detailed-cost-analysis"></a>Részletes költségelemzés
Az Azure számítási, tárolási, hálózati és egyéb díjszabása Azure-régiónként eltérő. Létrehozhat egy költségbecslési jelentést a legújabb árakkal az előfizetése alapján, az előfizetéséhez tartozó ajánlat figyelembe vételével, és az adott cél Azure-régióhoz a megadott pénznemben. Alapértelmezés szerint az eszköz Azure-régióként az USA 2. nyugati régióját, pénznemként pedig az amerikai dollárt (USD) használja. Ha bármely más régiót vagy pénznemet használt, a következő alkalommal, amikor előfizetés-azonosító, ajánlatazonosító, célrégió és pénznem nélkül hoz létre jelentést, a rendszer a legutóbb használt célrégió díjszabását és a legutóbb használt pénznemet fogja felhasználni a költségbecsléshez.
Ebben a szakaszban a jelentés létrehozásához használt előfizetés-azonosítót és ajánlatazonosítót tekintheti meg.  Ha nincs használatban, a szakasz üres.

A jelentésben a szürkével jelölt cellák írásvédettek. A fehér színű cellák igény szerint módosíthatók.

![Cost estimation details1](media/site-recovery-hyper-v-deployment-planner-cost-estimation/cost-estimation1-h2a.png)

### <a name="overall-dr-cost-by-components"></a>Teljes vészhelyreállítási költség összetevőnként
Az első szakaszban a teljes vészhelyreállítási költség látható összetevőnként, valamint a vészhelyreállítási költség állapotonként. 

**Számítás**: Az Azure-ban DR-re futó virtuális gépek IaaS díja. Az Azure Site Recovery által a DR működés (feladatátvételi tesztek) során létrehozott virtuális gépeket és az Azure-on futó virtuális gépeket tartalmazza (például SQL Server Always On rendelkezésre állási csoportokkal és tartományvezérlőkkel / tartományi névkiszolgálók).

**Tárterület**: A DR igényeinek megfelelő Azure Storage-felhasználás díja. A replikáció és a DR működés tárhelyhasználatát tartalmazza.
Hálózati A ExpressRoute és a helyek közötti VPN-költségeket DR-igényeknek megfelelően. 

**ASR-licenc**: Azure Site Recovery az összes kompatibilis virtuális gép licencének költségeit. Ha manuálisan adta meg a virtuális gépet a részletes költségelemzési táblában, a virtuális gép Azure Site Recovery-licencköltségeit is tartalmazza.

### <a name="overall-dr-cost-by-states"></a>Teljes vészhelyreállítási költség állapotonként
A vészhelyreállítás (DR) teljes költségének kategóriái két különböző állapoton alapulnak – Replikáció és DR működése

**Replikálási díj**: A költségek a replikálás időpontjában keletkeznek. Ez fedezi a tárolás, a hálózat és az Azure Site Recovery-licenc költségeit. 

**Dr**működésének díja: A DR működésének időpontjában megjelenő költségek. Az Azure Site Recovery virtuális gépeket indít el a DR működése során. A DR működési költségei a futó virtuális gépek számítási és tárolási költségeit fedezik.
A DR teljes működési időtartama egy évben = DR működéseinek száma x DR működéseinek időtartama (napokban) DR működésének átlagos költsége (havonta) = DR működésének teljes költsége / 12

### <a name="storage-cost-table"></a>A tárolási költség táblázata:
Ez a tábla a replikáció és a DR működése során használt prémium és standard tárterület költségeit mutatja kedvezményekkel és kedvezmények nélkül.

### <a name="site-to-azure-network"></a>Hely–Azure hálózat
Válassza ki az igényeinek megfelelő beállítást. 

**ExpressRoute:** Alapértelmezés szerint az eszköz kiválasztja a legközelebbi ExpressRoute-csomagot, amely megfelel a különbözeti replikációhoz szükséges hálózati sávszélességnek. Igény szerint módosíthatja a tervet.

**VPN Gateway**: Válassza ki a VPN Gateway, ha rendelkezik a környezetében. Alapértelmezés szerint NA értékű.

**Cél régió**: A DR-hez megadott Azure-régió. A jelentésben a számításhoz, tároláshoz, hálózathoz és licenchez használt árak a régió Azure-díjszabásán alapulnak. 

### <a name="vm-running-on-azure"></a>Az Azure-ban futó virtuális gép
Ha az Azure-ban bármilyen tartományvezérlő, DNS virtuális gép vagy Always On rendelkezésre állási csoportokkal rendelkező, SQL Server rendszerű virtuális gép fut vészhelyreállítási céllal, megadhatja a virtuális gépek számát és méretét, hogy azok számítási költségei is figyelembe legyenek véve a vészhelyreállítás teljes költségében. 

### <a name="apply-overall-discount-if-applicable"></a>Általános kedvezmény alkalmazása, ha van
Ha Ön Azure-partner vagy -ügyfél, és a teljes Azure-díjszabásból bármilyen kedvezményre jogosult, használja ezt a mezőt. Az eszköz minden összetevőre alkalmazza a kedvezményt (%-ban).

### <a name="number-of-virtual-machines-type-and-compute-cost-per-year"></a>A virtuális gépek típusainak száma és a számítási költségek (évente)
Ez a tábla a Windows és nem Windows rendszerű virtuális gépek számát és a hozzájuk tartozó DR működés számítási költségeit tartalmazza.

### <a name="settings"></a>Beállítások 

**Pénznem**: Az a pénznem, amelyben a jelentés létrejött. A Cost időtartama:  A havi vagy az egész évre vonatkozó összes költséget megtekintheti. 

## <a name="detailed-cost-analysis-table"></a>Részletes költségelemzési táblázat
![Részletes költségelemzés](media/site-recovery-hyper-v-deployment-planner-cost-estimation/detailed-cost-analysis-h2a.png): A táblában a kompatibilis virtuális gépek lebontott költségeinek listája látható. Ezt a táblát a profil nélküli virtuális gépek becsült Azure-beli vészhelyreállítási költségeinek megismeréséhez is használhatja a virtuális gépek manuális hozzáadásával. Ez olyan esetekben hasznos, amikor egy új vészhelyreállítás üzembe helyezésének Azure-költségeit kell megbecsülni részletes profilkészítés nélkül.
Virtuális gépek manuális hozzáadása: 
1.  Kattintson a „Sor beszúrása” gombra egy új sor beszúrásához a kezdő és a zárósor közé.

2.  Töltse ki az alábbi oszlopokat a virtuális gép hozzávetőleges mérete és a konfigurációnak megfelelő virtuális gépek száma alapján: 

* Virtuális gépek száma, IaaS-méret (felhasználó választása)
* Tárolás típusa (Standard/Prémium)
* A virtuális gép teljes tárolási mérete (GB) a forrásszámítógép számára
* DR működéseinek száma évente 
* Az egyes DR működések időtartama (nap) 
* Operációs rendszer típusa
* Adatredundancia 
* Azure Hybrid Benefit értékelem

1. Alkalmazhatja ugyanazokat az értékeket minden virtuális gépre a táblázatban az „Alkalmazás az összesre” gombra kattintva a következőknél: DR működéseinek száma évente, Az egyes DR működések időtartama (nap), Adatredundancia, és az Azure Hybrid Use Benefit.

1. A költségek frissítéséhez kattintson a „Költségek újraszámítása” lehetőségre.

**Virtuális gép neve**: A virtuális gép neve.

**Virtuális gépek száma**: A konfigurációnak megfelelő virtuális gépek száma. Frissítheti a meglévő virtuális gépek számát, ha a hasonló konfigurációjú virtuális gépekről nem készül profil, de védve lesznek.

**IaaS-méret (javaslat)** : Ez az eszköz által javasolt kompatibilis virtuális gép virtuálisgép-szerepkörének mérete. 

**IaaS mérete (az Ön választása)** : Alapértelmezés szerint ez ugyanaz, mint az ajánlott virtuálisgép-szerepkör mérete. Igény szerint módosíthatja a szerepkört. A számítási költség a kiválasztott virtuális gépi szerepkör méretén alapul.

**Tárolási típus**: A virtuális gép által használt tároló típusa. Lehet standard vagy prémium szintű tároló.

**Virtuális gép tárterületének teljes mérete (GB)** : A forrásként szolgáló virtuális gép teljes tárterülete.

**Dr-részletezések száma évente**: A DR-részletezések évente történő elvégzésének száma. Alapértelmezés szerint az értéke évente 4. Módosíthatja adott virtuális gépek időszakait, vagy alkalmazhatja az új értéket az összes virtuális gépre úgy, hogy a felső sorba beírja az új értéket és az „Alkalmazás az összesre” gombra kattint. A DR működéseinek száma évente és a DR egyes működéseinek időtartama alapján a rendszer kiszámítja a DR működésének teljes költségét.  

A DR működésének **időtartama (nap)** : Az egyes DR-részletezések időtartama. Alapértelmezés szerint ez 90 naponként 7 nap a [Vészhelyreállítási frissítési garanciának](https://azure.microsoft.com/pricing/details/site-recovery) megfelelően. Módosíthatja adott virtuális gépek időszakait, vagy alkalmazhatja az új értéket az összes virtuális gépre úgy, hogy a felső sorba beírja az új értéket és az „Alkalmazás az összesre” gombra kattint. A DR működésének teljes költsége kiszámítása a DR évi működéseinek számán és a DR egyes működéseinek időtartamán alapul.
  
**Operációs rendszer típusa**: A virtuális gép operációs rendszerének típusa. Lehet Windows vagy Linux. Ha az operációs rendszer típusa Windows, az Azure Hybrid Use Benefit alkalmazható a virtuális gépre. 

Adatredundancia: A következők egyike lehet a következő – helyileg redundáns tárolás (LRS), a Geo-redundáns tárolás (GRS) vagy a Read-Access geo-redundáns tárolás (RA-GRS). Az alapértelmezett érték LRS. Módosíthatja a típust adott virtuális gépek tárfiókja alapján, vagy alkalmazhatja az új típust minden virtuális gépre úgy, hogy módosítja a felső sor típusát, és az „Alkalmazás az összesre” gombra kattint.  A replikáció tárolási költsége a kiválasztott adatredundancia árának alapján lesz kiszámítva. 

**Azure Hybrid Benefit**: Ha alkalmazható, a Windows rendszerű virtuális gépekre is alkalmazhat Azure Hybrid Benefit.  Az alapértelmezett érték az Igen. Módosíthatja adott virtuális gépek beállításait, vagy frissítheti az összes virtuális gépet az „Alkalmazás az összesre” gombra kattintva.

**Összes Azure-felhasználás**: Ez magában foglalja a DR. számítási, tárolási és Azure Site Recovery licencek költségeit. A választott beállítás alapján a költségeket havi vagy éves bontásban jeleníti meg.

**Állandó állapotú replikálási díj**: Magában foglalja a replikálás tárolási költségeit.

**Dr működésének teljes költsége (átlag)** : A DR-részletezés számítási és tárolási költségeit is tartalmazza.

**ASR-licenc díja**: Azure Site Recovery a licenc költségeit.

## <a name="supported-target-regions"></a>Támogatott célrégiók
Az Azure Site Recovery Deployment Planner a következő Azure-régiókhoz biztosít költségbecslést. Ha a régiója nem található meg a lenti listában, a következő régiók közül használhatja azt, amelynek díjszabása a legközelebb áll az Ön régiójához.

eastus, eastus2, westus, centralus, northcentralus, southcentralus, northeurope, westeurope, eastasia, southeastasia, japaneast, japanwest, australiaeast, australiasoutheast, brazilsouth, southindia, centralindia, westindia, canadacentral, canadaeast, westus2, westcentralus, uksouth, ukwest, koreacentral, koreasouth 

## <a name="supported-currencies"></a>Támogatott pénznemek
Az Azure Site Recovery Deployment Planner az alábbi pénznemek bármelyikének használatával létre tudja hozni a költségjelentést.

|Currency|Name (Név)||Currency|Name (Név)||Currency|Name (Név)|
|---|---|---|---|---|---|---|---|
|ARS|Argentin peso ($)||AUD|Ausztrál dollár ($)||BRL|Brazil real (R$)|
|CAD|Kanadai dollár ($)||CHF|Svájci frank (chf)||DKK|Dán korona (kr)|
|EUR|Euró (€)||GBP|Angol font (£)||HKD|Hongkongi dollár (HK$)|
|IDR|Indonéz rúpia (Rp)||INR|Indiai rúpia (₹)||JPY|Japán jen (¥)|
|KRW|Koreai von (₩)||MXN|Mexikói peso (MX$)||MYR|Maláj ringgit (RM$)|
|NOK|Norvég korona (kr)||NZD|Új-zélandi dollár ($)||RUB|Orosz rubel (руб)|
|SAR|Szaúdi riál (SR)||SEK|Svéd korona (kr)||TWD|Tajvani új dollár (NT$)|
|TRY|Török líra (TL)||USD| Amerikai dollár ($)||ZAR|Dél-afrikai rand (R)|

## <a name="next-steps"></a>További lépések
Többet tudhat meg az [Azure-ba migrált VMware virtuális gépek védelméről az Azure Site Recoveryvel](https://docs.microsoft.com/azure/site-recovery/tutorial-vmware-to-azure).
