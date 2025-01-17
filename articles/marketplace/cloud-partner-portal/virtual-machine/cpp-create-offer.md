---
title: Virtuálisgép-ajánlat létrehozása az Azure Marketplace-en
description: Listák hozzon létre egy új virtuális gépet (VM) szükséges lépéseket kínálnak az Azure Marketplace-en.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 10/19/2018
ms.author: pabutler
ms.openlocfilehash: 4cd635c6f664a5260b79e62ea72bbb86fc4e1e4f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64938357"
---
# <a name="create-virtual-machine-offer"></a>Virtuálisgép-ajánlat létrehozása

Ebben a szakaszban egy új virtuális gép (VM) az ajánlat kérelmet az Azure Marketplace-en létrehozásához szükséges lépéseket tartalmazza.  Minden ajánlat jelenik meg a saját entitás az Azure Marketplace-en, és a egy vagy több termékváltozatok társított.  Egy Virtuálisgép-ajánlat a következő eszközök és a támogató szolgáltatások csoportosítását tevődik össze: 

![Egy Virtuálisgép-ajánlat eszközök](./media/publishvm_002.png)

Ahol:

|  **Eszközcsoport**   |  **Leírás**  |
|  ---------------   |  ---------------  |
|    SKU-k            |  A legkisebb megvásárolható egysége egy ajánlatot. (A termék osztály) egyetlen ajánlatba rendelhető hozzá több termékváltozatok különbséget tenni a támogatott szolgáltatások, a Virtuálisgép-lemezképek típusai és a számlázási modellek között lehet. |
|  Piactér       | Marketing, jogi és elégtelen felügyeleti eszközök és előírásokat tartalmazza.  <ul><li> Marketing eszközök tartalmazzák az ajánlat nevét, leírását és emblémák</li> <li> Jogi eszközök közé tartozik egy adatvédelmi szabályzatát, használati feltételeit és egyéb jogi dokumentáció</li>  <li> Elégtelen felügyeleti házirend lehetővé teszi, hogy adja meg, hogyan kezelje az Azure Marketplace-en a végfelhasználói portálon vezet.</li> </ul> |
| Támogatás            | Támogatási kapcsolattartó és házirend információkat tartalmaz |
| Tesztverzió         | Határozza meg, amelyek lehetővé teszik a végfelhasználók számára, hogy tesztelje ajánlatát, mielőtt azok a vásárláskor eszközök |
|  |  |


## <a name="new-offer-form"></a>Új ajánlat űrlap

Egyszer a jelentkezzen be a [Cloud Partner Portalon](https://cloudpartner.azure.com/), kattintson a **+ új ajánlat** elemet a bal oldali menüsáv. Az eredményül kapott menüben kattintson a **virtuális gépek** megjelenítéséhez a **új ajánlat** képezik, és egy új Virtuálisgép-ajánlat eszközök meghatározása folyamatának elindításához. 
<!-- not all publishers see corevm or azure apps test, you need to be whitelisted to see them. we should hide those in these images. -->

![Új virtuális gép ajánlat felhasználói felület kiválasztása](./media/publishvm_003.png)

> [!WARNING]
> Ha a **virtuális gépek** beállítás nem jelenik meg, vagy nincs engedélyezve, akkor a fiók nincs engedélye ezen ajánlat típus létrehozásához.  Ellenőrizze, hogy teljesül-e az összes a [Előfeltételek](./cpp-prerequisites.md) ajánlat típus, beleértve a fejlesztői fiókot regisztrálni.


## <a name="next-steps"></a>További lépések

Ebben a szakaszban a következő témakörök lapjait tükrözik a **új ajánlat** (a virtuális gép ajánlattípusra) lapot.  Minden egyes cikk leírja, hogyan használatához a társított lapon adható meg az eszközintelligencia-csoportok és az új Virtuálisgép-ajánlat segítő szolgáltatás.

- [Ajánlatbeállítási lap](./cpp-offer-settings-tab.md)
- [Termékváltozatok lap](./cpp-skus-tab.md)
- [Tesztverzió lap](./cpp-test-drive-tab.md)
- [Marketplace lap](./cpp-marketplace-tab.md)
- [Támogatási lap](./cpp-support-tab.md)
