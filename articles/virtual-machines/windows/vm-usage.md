---
title: Az Azure-beli virtuális gépek használatának ismertetése | Microsoft Docs
description: A virtuális gépek használati adatainak megismerése
services: virtual-machines
documentationcenter: ''
author: mmccrory
manager: gwallace
editor: ''
tags: azure-virtual-machine
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 12/04/2017
ms.author: memccror
ms.openlocfilehash: 303bf41d943267c42c1311d185a4ff65baf5178f
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68834604"
---
# <a name="understanding-azure-virtual-machine-usage"></a>Az Azure-beli virtuális gépek használatának ismertetése
Az Azure-beli használati adatok elemzésével hatékony fogyasztási adatokat nyerhetnek ki – olyan elemzéseket készíthet, amelyek lehetővé teszik a szervezeten belüli jobb költséghatékonyságot és kiosztást. Ez a dokumentum részletesen ismerteti az Azure-beli számítási felhasználás részleteit. Az Azure általános használatának részletes ismertetését a [számla megismerése](../../billing/billing-understand-your-bill.md)című rész ismerteti.

## <a name="download-your-usage-details"></a>A használati adatok letöltése
A kezdéshez [töltse le a használati adatokat](../../billing/billing-download-azure-invoice-daily-usage-date.md). Az alábbi táblázat a Azure Resource Manager használatával üzembe helyezett Virtual Machines használatának definícióját és példáit tartalmazza. Ez a dokumentum nem tartalmaz részletes információkat a klasszikus modellen keresztül üzembe helyezett virtuális gépekről.


| Mezők             | Jelentés                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | Példa értékek                                                                                                                                                                                                                                                                                                                                                   |
|--------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Usage Date (Használat dátuma)         | Az erőforrás használatának dátuma.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |  “11/23/2017”                                                                                                                                                                                                                                                                                                                                                     |
| Fogyasztásmérő azonosítója           | Meghatározza azt a legfelső szintű szolgáltatást, amelynek a használata a következőhöz tartozik:.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | "Virtual Machines"                                                                                                                                                                                                                                                                                                                                               |
| Fogyasztásmérő alkategóriája | A kiszámlázott mérő azonosítója. <ul><li>A számítási órák használata esetén a rendszer minden virtuálisgép-mérethez (Windows, nem Windows) és régióhoz tartozó mérőszámot használ.</li><li>A prémium szintű szoftverek használata esetén minden egyes szoftver esetében van egy mérőszám. A prémium szintű szoftverek többsége minden alapmérethez különböző mérőszámokat tartalmaz. További információért látogasson el a [számítási díjszabást ismertető oldalra.](https://azure.microsoft.com/pricing/details/virtual-machines/)</li></ul>                                                                                                                                                                                                                                                                                                                                         | “2005544f-659d-49c9-9094-8e0aea1be3a5”                                                                                                                                                                                                                                                                                                                           |
| Fogyasztásmérő neve         | Ez az egyes Azure-szolgáltatásokra jellemző. A számítási feladatokhoz mindig "Üzemóra".                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | "Üzemóra"                                                                                                                                                                                                                                                                                                                                                  |
| Fogyasztásmérő régiója       | Az igénybe vett vagy üzemeltető adatközpont elhelyezkedése, ha a szolgáltatás díjszabása az adatközpontok elhelyezkedésétől is függ.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |  "JA Kelet"                                                                                                                                                                                                                                                                                                                                                       |
| Unit (Egység)               | Meghatározza azt az egységet, amelyben a szolgáltatás díjköteles. A számítási erőforrások számlázása óránként történik.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | Óra                                                                                                                                                                                                                                                                                                                                                          |
| Felhasznált           | Az adott napra felhasznált erőforrás mennyisége. A számítási feladatokhoz minden percben a virtuális gép egy adott órában futott (legfeljebb 6 tizedesjegy pontossággal).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |    “1”, “0.5”                                                                                                                                                                                                                                                                                                                                                    |
| Erőforrás helye  | Az az adatközpont, amelyen az erőforrás fut.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      | "JA Kelet"                                                                                                                                                                                                                                                                                                                                                        |
| Igénybe vett szolgáltatás   | Az Azure platform szolgáltatás, amelyet használt.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     | "Microsoft.Compute"                                                                                                                                                                                                                                                                                                                                              |
| Erőforráscsoport     | Az az erőforráscsoport, amelyben az üzembe helyezett erőforrás fut. További információ: [Azure Resource Manager Overview (áttekintés).](../../azure-resource-manager/resource-group-overview.md)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |    MyRG                                                                                                                                                                                                                                                                                                                                                        |
| Példányazonosító        | Az erőforrás azonosítója. Az azonosító tartalmazza az erőforrás számára a létrehozáskor megadott nevet. A virtuális gépek esetében a példány azonosítója a SubscriptionId, a ResourceGroupName és a VMName (vagy a méretezési csoportnak a méretezési csoport használatának nevét) fogja tartalmazni.                                                                                                                                                                                                                                                                                                                                                                                                                    | "/Subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/MyRG/Providers/Microsoft. számítás/virtualMachines/MyVM1"<br><br>vagy<br><br>"/Subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/MyRG/Providers/Microsoft. számítás/virtualMachineScaleSets/MyVMSS1"                                                                                           |
| Tags               | Az erőforráshoz hozzárendelt címke. Használjon címkéket a számlázási rekordok csoportosításához. Megtudhatja, hogyan [címkézheti Virtual machines.](tag.md) Ez csak Resource Manager-alapú virtuális gépek esetén érhető el.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | "{" myDepartment ":" RD "," myUser ":" myName "}"                                                                                                                                                                                                                                                                                                                        |
| További információ    | Szolgáltatás-specifikus metaadatok. A virtuális gépek esetében a további információ mezőben a következő adatok szerepelnek: <ul><li>Képtípusra jellemző rendszerkép, amelyet futtatott. A támogatott sztringek teljes listáját megtalálja a képtípusok alatt.</li><li>Szolgáltatás típusa: a telepített méret.</li><li>VMName: a virtuális gép neve. Ez csak a méretezési csoport virtuális gépei esetében van kitöltve. Ha szüksége van a virtuális gép nevére a méretezési csoport virtuális gépei számára, akkor azt a fenti AZONOSÍTÓJÚ példányban találja.</li><li>Usagetype értékre Ez határozza meg az ezt jelképező használati típust.<ul><li>A ComputeHR az alapul szolgáló virtuális gép (például a Standard_D1_v2) számítási óradíjának használata.</li><li>A ComputeHR_SW a prémium szintű szoftverek díja, ha a virtuális gép prémium szintű szoftvert használ, például Microsoft R Server.</li></ul></li></ul>    | Virtual Machines {"ImageType":"Canonical","ServiceType":"Standard_DS1_v2","VMName":"", "UsageType":"ComputeHR"}<br><br>Virtual Machine Scale Sets {"ImageType":"Canonical","ServiceType":"Standard_DS1_v2","VMName":"myVM1", "UsageType":"ComputeHR"}<br><br>Premium Software {"ImageType":"","ServiceType":"Standard_DS1_v2","VMName":"", "UsageType":"ComputeHR_SW"} |

## <a name="image-type"></a>Lemezkép típusa
Az Azure-katalógusban található egyes rendszerképeknél a kép típusa a további információ mezőben lesz kitöltve. Ez lehetővé teszi a felhasználók számára, hogy megértsék és nyomon kövessék, mire vannak telepítve a virtuális gépen. Az ebben a mezőben az üzembe helyezett lemezkép alapján kitöltött értékek a következők:
  - BitRock 
  - Canonical 
  - FreeBSD 
  - Logika megnyitása 
  - Oracle 
  - SLES az SAP-hoz 
  - SQL Server 14 előzetes verzió a Windows Server 2012 R2 előzetes verziójában 
  - SUSE
  - SUSE Premium
  - StorSimple Cloud Appliance 
  - Red Hat
  - Red Hat az SAP Business Applications     
  - Red Hat SAP HANA 
  - Windows-ügyfél BYOL 
  - Windows Server BYOL 
  - Windows Server előzetes verzió 

## <a name="service-type"></a>Service Type
A további információ mezőben a szolgáltatás típusa mező a telepített virtuális gép méretének felel meg. A Premium Storage virtuális gépek (SSD-alapú) és a nem prémium szintű Storage-beli virtuális gépek (HDD-alapú) díjszabása azonos. Ha SSD-\_alapú méretet (például standard DS2\_v2) helyez üzembe, az SSD-méret ("standard\_D2\_v2 virtuális gép") jelenik meg a mérőszám alkategóriája oszlopban és az SSD-Size ("standard\_DS2\_ v2 ") a további információ mezőben.

## <a name="region-names"></a>Régiók nevei
A használati adatok erőforrás helye mezőjében kitöltött régió neve eltér a Azure Resource Manager használt régió nevétől. Itt látható a régiók értékei közötti leképezés:

|    **Resource Manager-régió neve**       |    **Erőforrás helye a használat részleteiben**    |
|--------------------------|------------------------------------------|
|    australiaeast         |    Ausztrália keleti régiója                               |
|    australiasoutheast    |    Délkelet-Ausztrália                          |
|    brazilsouth           |    Dél-Brazília                              |
|    CanadaCentral         |    Közép-Kanada                            |
|    CanadaEast            |    Kelet-Kanada                               |
|    CentralIndia          |    Közép-India                            |
|    centralus             |    USA középső régiója                            |
|    chinaeast             |    Kelet-Kína                            |
|    chinanorth            |    Észak-Kína                           |
|    eastasia              |    Kelet-Ázsia                             |
|    eastus                |    East US                               |
|    eastus2               |    USA 2. keleti régiója                             |
|    GermanyCentral        |    Közép-Németország                            |
|    GermanyNortheast      |    Északkelet-Németország                          |
|    japaneast             |    Kelet-Japán                               |
|    japanwest             |    Nyugat-Japán                               |
|    KoreaCentral          |    Korea középső régiója                            |
|    KoreaSouth            |    Korea déli régiója                              |
|    northcentralus        |    USA északi középső régiója                      |
|    northeurope           |    Észak-Európa                          |
|    southcentralus        |    USA déli középső régiója                      |
|    southeastasia         |    Délkelet-Ázsia                        |
|    SouthIndia            |    Dél-India                              |
|    UKNorth               |    USA északi régiója                              |
|    uksouth               |    Az Egyesült Királyság déli régiója                              |
|    UKSouth2              |    Egyesült Királyság 2. déli régiója                            |
|    ukwest                |    Az Egyesült Királyság nyugati régiója                               |
|    USDoDCentral          |    US DoD – Középső régió                        |
|    USDoDEast             |    US DoD – Kelet                           |
|    USGovArizona          |    USA-beli államigazgatás – Arizona                         |
|    usgoviowa             |    USGov Iowa                            |
|    USGovTexas            |    USA-beli államigazgatás – Texas                           |
|    usgovvirginia         |    USGov Virginia                        |
|    westcentralus         |    USA nyugati középső régiója                       |
|    westeurope            |    Nyugat-Európa                           |
|    WestIndia             |    Nyugat-India                               |
|    westus                |    USA nyugati régiója                               |
|    westus2               |    USA 2. nyugati régiója                             |


## <a name="virtual-machine-usage-faq"></a>Virtuális gépek használata – gyakori kérdések
### <a name="what-resources-are-charged-when-deploying-a-vm"></a>Milyen erőforrásokra számítunk fel a virtuális gépek telepítésekor?    
A virtuális gépek költségei megvásárlása maga a virtuális gép, a virtuális gépen futó prémium szoftverek, a virtuális géphez társított account\managed lemez, valamint a hálózati sávszélességnek a virtuális gépről való átvitele után.
### <a name="how-can-i-tell-if-a-vm-is-using-azure-hybrid-benefit-in-the-usage-csv"></a>Honnan tudhatom meg, hogy egy virtuális gép használ-e Azure Hybrid Benefitt a CSV-használatban?
Ha a [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)használatával végzi a telepítést, akkor a nem Windows RENDSZERű virtuális gépek díját számítjuk fel, mivel a saját licencét a felhőbe hozza. A számlán megszabhatja, hogy mely Resource Manager-alapú virtuális gépek futnak Azure Hybrid Benefit, mert a\_ImageType oszlopban a "Windows Server\_BYOL" vagy a "Windows Client BYOL" szerepel.
### <a name="how-are-basic-vs-standard-vm-types-differentiated-in-the-usage-csv"></a>Az alapszintű és a Szabványos virtuálisgép-típusok differenciálva a használatban lévő CSV-fájlban?
Az alapszintű és A standard A-sorozatú virtuális gépek egyaránt elérhetők. Ha alapszintű virtuális gépet helyez üzembe, a mérőszám alkategóriájában az "alapszintű" karakterlánc szerepel. Ha standard A sorozatú virtuális gépet telepít, akkor a virtuális gép mérete "a1 virtuális gép" lesz, mivel a standard érték az alapértelmezett. Ha többet szeretne megtudni az alapszintű és a standard közötti különbségekről, tekintse meg a [díjszabási oldalt](https://azure.microsoft.com/pricing/details/virtual-machines/).
### <a name="what-are-extrasmall-small-medium-large-and-extralarge-sizes"></a>Mik azok a ExtraSmall, kis-, közepes, nagy és ExtraLarge méretek?
A ExtraSmall-ExtraLarge a standard\_a0 – standard\_a4 örökölt nevei. A klasszikus virtuálisgép-használati rekordokban láthatja ezt az egyezményt, ha telepítette ezeket a méreteket.
### <a name="what-is-the-difference-between-meter-region-and-resource-location"></a>Mi a különbség a mérési régió és az erőforrás helye között?
A mérési régió a mérőhöz van társítva. Néhány olyan Azure-szolgáltatás esetében, amely minden régió esetében egy árat használ, üres lehet a mérési régió mezője. Mivel azonban a virtuális gépek a Virtual Machines régiónként külön díjszabással rendelkeznek, ez a mező fel van töltve. Hasonlóképpen, a Virtual Machines erőforrásának helye a virtuális gép üzembe helyezési helye. Az Azure-régió mindkét mezőben azonos, bár a régió nevéhez eltérő karakterlánc-konvenció tartozhat.
### <a name="why-is-the-imagetype-value-blank-in-the-additional-info-field"></a>Miért üres a ImageType érték a további információ mezőben?
A ImageType mező csak a lemezképek egy részhalmazára van kitöltve. Ha nem telepítette a fenti rendszerképek egyikét, a ImageType üres.
### <a name="why-is-the-vmname-blank-in-the-additional-info"></a>Miért üres a VMName a további információban?
A VMName csak a méretezési csoportba tartozó virtuális gépek további információ mezőjében van kitöltve. A InstanceID mező tartalmazza a virtuális gép nevét a nem méretezési csoport virtuális gépei számára.
### <a name="what-does-computehr-mean-in-the-usagetype-field-in-the-additional-info"></a>Mit jelent a ComputeHR a további információ Usagetype értékre mezőjében?
A ComputeHR a számítási órákra vonatkozik, amely a mögöttes infrastruktúra-díj használati eseményét jelöli. Ha a usagetype értékre ComputeHR\_SW, a használati esemény a virtuális gép prémium szintű szoftveres díját jelenti.
### <a name="how-do-i-know-if-i-am-charged-for-premium-software"></a>Hogyan tudni, hogy a prémium szintű szoftverekért kell fizetni?
Az [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute)-en tekintse meg, hogy melyik virtuálisgép-rendszerkép megfelel az igényeinek. A rendszerképnek a szoftver csomagjának aránya van. Ha "ingyenes" érték jelenik meg a díjszabásban, a szoftverre nem vonatkozik további díj. 
### <a name="what-is-the-difference-between-microsoftclassiccompute-and-microsoftcompute-in-the-consumed-service"></a>Mi a különbség a Microsoft. ClassicCompute és a Microsoft. számítás között a felhasznált szolgáltatásban?
A Microsoft. ClassicCompute az Azure Service Manager használatával üzembe helyezett klasszikus erőforrásokat képviseli. Ha a Resource Managerrel végzi a telepítést, akkor a Microsoft. számítás a felhasználható szolgáltatásban van feltöltve. További információ az [Azure üzembe helyezési modelljeiről](../../azure-resource-manager/resource-manager-deployment-model.md).
### <a name="why-is-the-instanceid-field-blank-for-my-virtual-machine-usage"></a>Miért üres a InstanceID mező a virtuális gépek használatakor?
Ha a klasszikus üzemi modellen keresztül végzi a telepítést, a InstanceID karakterlánc nem érhető el.
### <a name="why-are-the-tags-for-my-vms-not-flowing-to-the-usage-details"></a>Miért nem áramlanak a használati adatokba a virtuális gépekhez tartozó címkék?
A címkék csak a Resource Manager-alapú virtuális gépek használati CSV-fájlját használják. A klasszikus erőforrás címkéi nem érhetők el a használat részleteiben.
### <a name="how-can-the-consumed-quantity-be-more-than-24-hours-one-day"></a>Hogyan lehet a felhasznált mennyiség több mint 24 órát egy nap múlva?
A klasszikus modellben az erőforrások számlázása a Cloud Service szintjén összesítve történik. Ha egy felhőalapú szolgáltatásban több virtuális gép is ugyanazt a számlázási mérőszámot használja, a használatot együtt összesítjük. A Resource Managerrel üzembe helyezett virtuális gépek számlázása virtuálisgép-szinten történik, így ez az Összesítés nem érvényes.
### <a name="why-is-pricing-not-available-for-dsfsgsls-sizes-on-the-pricing-page"></a>Miért nem érhető el az árképzés a DS/FS/GS/LS méretekhez a díjszabási oldalon?
A Premium Storage-kompatibilis virtuális gépek számlázása ugyanolyan sebességgel történik, mint a nem prémium szintű Storage-kompatibilis virtuális gépek. Csak a tárolási költségek különböznek. További információért látogasson el a [Storage díjszabási oldalára](https://azure.microsoft.com/pricing/details/storage/unmanaged-disks/) .

## <a name="next-steps"></a>További lépések
A használati adatokkal kapcsolatos további információkért lásd: [Microsoft Azure számlájának ismertetése.](../../billing/billing-understand-your-bill.md)

