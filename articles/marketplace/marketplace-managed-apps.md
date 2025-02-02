---
title: Az Azure-alkalmazások kezelt alkalmazásra vonatkozó ajánlat közzétételi útmutató
description: Ez a cikk ismerteti a felügyelt alkalmazás közzététele a piactéren
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: qianw211
manager: evansma
ms.service: marketplace
ms.topic: article
ms.date: 06/14/2018
ms.author: v-qiwe
ms.openlocfilehash: 29546b0969751a43959a55860fc22e9f3c3e225b
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2019
ms.locfileid: "67154944"
---
# <a name="azure-applications-managed-application-offer-publishing-guide"></a>Az Azure-alkalmazások: Felügyelt alkalmazás az ajánlat közzétételi útmutató

Felügyelt alkalmazás a fő módon lehet a megoldás a piactéren közzétett egyike. Ez az útmutató segítségével áttekinteni a követelményeket, ezt az ajánlatot. 

Ezek a tranzakció ajánlatok, amelyek üzembe helyezve, és a Marketplace-en keresztül számlázott. Művelet, amely a felhasználó kap, a hívást a "Letöltés most."

Használja az Azure-alkalmazás: az ajánlat alkalmazástípus kezelése, ha az alábbi feltételek szükségesek:
- Vagy egy előfizetés-alapú megoldás üzembe helyezése egy virtuális gép vagy a teljes IaaS-alapú megoldás segítségével az ügyfelek számára.
- Ön vagy az ügyfél szükséges, hogy a megoldás egy partner kezeli-e.

>[!NOTE]
>Ha például egy partner egy SI vagy felügyelt szolgáltató (MSP) lehet.  

## <a name="managed-application-offer"></a>Felügyelt alkalmazásra vonatkozó ajánlat

|Követelmények |Részletek  |
|---------|---------|
|Az ügyfél az Azure-előfizetés keretében üzembe | Felügyelt alkalmazások telepíteni kell az ügyfél-előfizetés és a egy harmadik fél által felügyelhető. | 
|Számlázással és méréssel    |  Az erőforrások lesznek üzembe helyezve, az ügyfél Azure-előfizetésében. Használatalapú fizetés (ÁTTÉRÉSHEZ) virtuális gépek az ügyfélnél, Microsoft, a számlázás az ügyfél az Azure-előfizetés (ÁTTÉRÉSHEZ) keresztül lesz kell feldolgozva. <br> Esetén bring-your-saját licenc míg infrastrukturális költségeit az ügyfél-előfizetés a Microsoft ki, fog transact a szoftverlicenc-díjak, az ügyfél közvetlenül.        |
|Azure-kompatibilis virtuális merevlemez (VHD)    |   Windows vagy Linux rendszerű virtuális gépek kell épül.<ul> <ul> <li>Linux rendszerű virtuális Merevlemezek létrehozásával kapcsolatos további információkért lásd: [az Azure által támogatott Linux-disztribúciók](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).</li> <li>Windows virtuális Merevlemezek létrehozásával kapcsolatos további információkért lásd: [hozzon létre egy Azure-kompatibilis virtuális Merevlemezt](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md).</li> </ul> |

>[!NOTE]
> Felügyelt alkalmazások a Marketplace-en keresztül telepíthető kell lennie. Ha az ügyfél-kommunikáció fontos, majd meg az elérhető érdeklődőnek megosztó érdeklődő engedélyezése után.  

>[!Note]
>Cloud Solution Providers (CSP) partner csatorna vehetnek részt már elérhető.  Lásd: [Cloud Solution Providers](./cloud-solution-providers.md) további tájékoztatást a marketing, az ajánlat keretében a Microsoft CSP partner-csatornákon.

## <a name="next-steps"></a>További lépések
Ha ezt még nem tette meg, 

- [Regisztráljon](https://azuremarketplace.microsoft.com/sell) a Marketplace-en.

Ha regisztrálva van és a egy új ajánlat létrehozása vagy egy meglévő, dolgozik

- [Jelentkezzen be a Cloud Partner portálra](https://cloudpartner.azure.com) létrehozni, vagy végezze el az ajánlatot.
