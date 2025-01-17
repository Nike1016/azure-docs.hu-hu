---
title: A Microsoft Azure virtuális gép előfeltételei |} Az Azure Marketplace-en
description: Egy Virtuálisgép-ajánlat közzététele az Azure piactéren szükséges előfeltételek listája.
services: Azure, Marketplace, Cloud Partner Portal
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 03/13/2019
ms.author: pabutler
ms.openlocfilehash: 258d21eae5af50b5dc0bed6887618e2999cae45a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66257382"
---
# <a name="virtual-machine-prerequisites"></a>A virtuális gép előfeltételei

Ez a cikk felsorolja mindkét a műszaki és üzleti követelményeknek, amelyek előtt meg kell felelnie egy Virtuálisgép-ajánlat, közzéteheti a [Azure Marketplace-en](https://azuremarketplace.microsoft.com/marketplace/).  Ha még nem tette meg, tekintse át a [virtuális gép ajánlat közzétételi útmutató](../../marketplace-virtual-machines.md).


## <a name="technical-requirements"></a>Technikai követelmények

A virtuális gép (VM) a megoldás közzététele műszaki előfeltételeinek magától értetődnek:

- Aktív Azure-fiókkal kell rendelkeznie. Ha nem rendelkezik egy, iratkozzon fel a [a Microsoft Azure site](https://azure.microsoft.com).  
- Rendelkeznie kell konfigurálni, hogy támogassa a Windows vagy Linux rendszerű virtuális gépek fejlesztési környezet.  További információkért tekintse meg a társított virtuális gép dokumentációs webhelyen:
    - [Linux rendszerű virtuális gépek dokumentációja](https://docs.microsoft.com/azure/virtual-machines/linux/)
    - [Windows virtuális gépek dokumentációja](https://docs.microsoft.com/azure/virtual-machines/windows/)


## <a name="business-requirements"></a>Üzleti feltételek

Az üzleti követelmények eljárási szerződéses és jogi kötelezettségek tartalmazza: 

<!-- TD: Aren't most of these business requirements common to all AMP offerings?  If yes, then move to higher level, perhaps to the AMP section "Become a Cloud Marketplace Publisher" -->
<!-- TD: Need references for remaining docs/business reqs!-->

- Regisztrált Felhőbeli piactér kiadói kell lennie.  Ha nincs még regisztrálva, kövesse a cikkben található lépéseket [válnak a Felhőbeli piactér kiadói](https://docs.microsoft.com/azure/marketplace/become-publisher).

    > [!NOTE]
    > Jelentkezzen be a Microsoft Developer Center regisztrációs ugyanazt a fiókot kell használnia a [Cloud Partner Portalon](https://cloudpartner.azure.com).
    > Az Azure piactér-i ajánlatainak közzétételéhez egyetlen Microsoft-fiókkal kell rendelkeznie. Nem lehet adott egyes szolgáltatásokat vagy ajánlatokat.
    
- A vállalatnak (vagy az leányvállalatának) kell működnie egy értékesítési-a-országot/régiót az Azure piactér által támogatott.  Ezekben az országokban/régiókban aktuális listáját lásd: [a Microsoft Azure Marketplace részvételi szabályzata](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).
- A termék úgy, hogy az Azure piactér által támogatott számlázási modellek kompatibilis licenccel kell rendelkezniük.  További információkért lásd: [számlázási lehetőségek az Azure Marketplace-en](https://docs.microsoft.com/azure/marketplace/billing-options-azure-marketplace). 
- Ön felelős az ügyfelek számára elérhetővé tétele az műszaki támogatást, olyan üzletileg ésszerű módon. Ez a támogatás ingyenes, fizetős, vagy közösségi megközelítések keresztül lehet.
- Ön felelős a szoftver- és külső függőségek licencelése.
- Meg kell adnia a tartalmat, amely megfelel az Azure Marketplace-en és az Azure Portalon való megjelentetéséhez ajánlat feltételeit. <!-- TD: Meaning/links? -->
- El kell fogadnia a használati a [a Microsoft Azure Marketplace részvételi szabályzata](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) és Kiadókra vonatkozó szerződése.
- Be kell tartania a [a Microsoft Azure webhelyhasználati feltételek](https://azure.microsoft.com/support/legal/website-terms-of-use/), [Microsoft adatvédelmi nyilatkozatát](https://privacy.microsoft.com/privacystatement), és [a Microsoft Azure Certified Program szerződését](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/).


## <a name="next-steps"></a>További lépések

Miután az előfeltételek teljesülnek, [a Virtuálisgép-ajánlat létrehozása](./cpp-create-offer.md).
