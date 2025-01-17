---
title: Minta-ISO 27001 terv – lépések üzembe helyezése
description: Az ISO 27001 tervrajzi minta lépéseinek üzembe helyezése.
author: DCtheGeek
ms.author: dacoulte
ms.date: 07/22/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 00e60d41b34531462a4e85623a19f1dd22c6e4e6
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816810"
---
# <a name="deploy-the-iso-27001-blueprint-sample"></a>Az ISO 27001 tervrajzi minta üzembe helyezése

Az Azure-tervezetek ISO 27001 tervrajzi minta üzembe helyezéséhez a következő lépéseket kell elvégeznie:

> [!div class="checklist"]
> - Új terv létrehozása a mintából
> - A minta másolatának megjelölése **közzétettként**
> - A terv másolatának kiosztása meglévő előfizetéshez

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-blueprint-from-sample"></a>Terv létrehozása mintából

Először is implementálja a terv mintáját úgy, hogy létrehoz egy új tervet a környezetben a minta kezdőként való használatával.

1. Válassza a **minden szolgáltatás** lehetőséget, és keresse meg és válassza ki a **házirend** elemet a bal oldali ablaktáblán. A **szabályzat** lapon válassza a **tervrajzok**lehetőséget.

1. A bal oldali **első lépések** lapon kattintson a **Létrehozás** gombra a _terv létrehozása_területen.

1. Keresse meg az **ISO 27001** tervrajz mintát _más minták_ alatt, és válassza a **minta használata**lehetőséget.

1. Adja meg a tervezet mintájának alapjait:

   - **Terv neve**: Adja meg az ISO 27001 Blueprint minta példányának nevét.
   - **Definíció helye**: Használja a három pontot, és válassza ki a felügyeleti csoportot, és mentse a minta másolatát a következőre:.

1. Válassza ki az összetevők fület a lap tetején, vagy **a következőt:**  Az oldal alján található összetevők.

1. Tekintse át a terv mintáját alkotó összetevők listáját. Számos összetevőhöz vannak olyan paraméterek, amelyeket később definiálunk. Válassza a **Piszkozat mentése** lehetőséget, amikor befejezte a tervezet mintájának áttekintését.

## <a name="publish-the-sample-copy"></a>A minta másolatának közzététele

A terv mintájának másolata már létre lett hozva a környezetében. A rendszer **Piszkozat** módban jön létre, és **közzé** kell tenni ahhoz, hogy hozzá lehessen rendelni és telepíteni lehessen. A terv mintájának másolata testreszabható a környezet és a szükséges igények alapján, de ez a módosítás az ISO 27001 szabványból is áthelyezhető.

1. Válassza a **minden szolgáltatás** lehetőséget, és keresse meg és válassza ki a **házirend** elemet a bal oldali ablaktáblán. A **szabályzat** lapon válassza a **tervrajzok**lehetőséget.

1. Válassza a bal oldali **terv** -definíciók lapot. A szűrők használatával megkeresheti a tervezet mintájának másolatát, majd kiválaszthatja.

1. Válassza a **terv közzététele** lehetőséget az oldal tetején. A jobb oldalon található új lapon adjon meg egy **verziót** a tervezet mintájának másolatához. Ez a tulajdonság akkor hasznos, ha később módosítja a módosítást. Adjon meg olyan **módosítási megjegyzéseket** , mint az "első verzió, amely az ISO 27001 Blueprint Sample-ből lett közzétéve." Ezután válassza a **Közzététel** elemet az oldal alján.

## <a name="assign-the-sample-copy"></a>A minta másolatának kiosztása

Miután a tervezet mintájának **közzététele**sikeresen megtörtént, hozzárendelhető egy előfizetéshez, amely a felügyeleti csoporton belül lett mentve. Ezzel a lépéssel megadhatja, hogy az egyes központi telepítések egyediek legyenek-e.

1. Válassza a **minden szolgáltatás** lehetőséget, és keresse meg és válassza ki a **házirend** elemet a bal oldali ablaktáblán. A **szabályzat** lapon válassza a **tervrajzok**lehetőséget.

1. Válassza a bal oldali **terv** -definíciók lapot. A szűrők használatával megkeresheti a tervezet mintájának másolatát, majd kiválaszthatja.

1. Válassza a terv kiosztása elemet a terv definíciója oldal tetején.

1. Adja meg a tervrajz-hozzárendelés paramétereinek értékét:

   - Alapvető beállítások

     - **Előfizetések**: Válasszon ki egy vagy több olyan előfizetést, amely a felügyeleti csoportban található, a terv mintájának másolatát a következőre mentette:. Ha egynél több előfizetést választ ki, a rendszer minden megadott paraméterrel létrehoz egy hozzárendelést.
     - **Hozzárendelés neve**: A név előre ki van töltve a terv neve alapján.
       Szükség szerint módosítsa a változást, vagy hagyja a következőt:.
     - **Hely**: Válassza ki azt a régiót, amelyben létre kívánja hozni a felügyelt identitást. Az Azure Blueprint a hozzárendelt tervben lévő összes összetevő üzembe helyezéséhez ezt a felügyelt identitást használja. További tudnivalók: [Azure-erőforrások felügyelt identitásai](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Terv definíciójának verziója**: Válasszon egy **közzétett** verziót a tervezet mintájának másolatáról.

   - Hozzárendelés zárolása

     Válassza ki a környezethez tartozó terv zárolási beállítását. További információkat talál a [terv-erőforrások zárolásáról](../../concepts/resource-locking.md) szóló cikkben.

   - Felügyelt identitás

     Hagyja meg az alapértelmezett _rendszerhez rendelt_ felügyelt identitás beállítást.

   - Terv paraméterei

     Az ebben a szakaszban meghatározott paramétereket a terv definíciójában található számos összetevő használja a konzisztencia biztosításához.

     - **Erőforrások és erőforráscsoportok engedélyezett helye**: Az erőforráscsoportok és erőforrások engedélyezett helyét jelző érték.

   - Összetevő paraméterei

     Az ebben a szakaszban meghatározott paraméterek a definiált összetevőre vonatkoznak. Ezek a paraméterek [dinamikus paraméterek](../../concepts/parameters.md#dynamic-parameters) , mert a terv hozzárendelése során vannak meghatározva. A teljes listát vagy az összetevő paramétereit és azok leírását lásd: összetevő- [Paraméterek táblázata](#artifact-parameters-table).

1. Az összes paraméter megadása után válassza a lap alján található **hozzárendelés** elemet. A terv-hozzárendelés létrejött, és az összetevő üzembe helyezése megkezdődik. Az üzembe helyezés nagyjából egy órát vesz igénybe. Az üzembe helyezés állapotának megtekintéséhez nyissa meg a terv-hozzárendelést.

> [!WARNING]
> Az Azure BluePrints szolgáltatás és a beépített tervrajzi minták díjmentesek. Az Azure-erőforrások [díjszabása termékenként](https://azure.microsoft.com/pricing/)történik. A [díjszabási számológép](https://azure.microsoft.com/pricing/calculator/) használatával megbecsülheti a tervrajzi minta által üzembe helyezett erőforrások futtatásának költségeit.

## <a name="artifact-parameters-table"></a>Összetevő-paraméterek táblázata

A következő táblázat a tervrajz-összetevő paramétereinek listáját tartalmazza:

|Elemnév|Összetevő típusa|Paraméternév|Leírás|
|-|-|-|-|
|\[Előzetes\]verzió: A Linux VM Scale Sets (VMSS) Log Analytics-ügynökének üzembe helyezése|Szabályzat-hozzárendelés|Log Analytics a Linux VM Scale Sets (VMSS) munkaterülete|Ha ez a munkaterület kívül esik a hozzárendelés hatókörén, manuálisan kell megadnia a "Log Analytics közreműködői" engedélyeket (vagy hasonlókat) a szabályzat-hozzárendelés elsődleges AZONOSÍTÓjának.|
|\[Előzetes\]verzió: A Linux VM Scale Sets (VMSS) Log Analytics-ügynökének üzembe helyezése|Szabályzat-hozzárendelés|Nem kötelező: A hatókörbe felvenni kívánt Linux operációs rendszert futtató virtuálisgép-rendszerképek listája|Egy üres tömb is felhasználható a nem kötelező paraméterek jelölésére:\[\]|
|\[Előzetes\]verzió: A Linux rendszerű virtuális gépek Log Analytics ügynökének üzembe helyezése|Szabályzat-hozzárendelés|A Linux rendszerű virtuális gépek Log Analytics munkaterülete|Ha ez a munkaterület kívül esik a hozzárendelés hatókörén, manuálisan kell megadnia a "Log Analytics közreműködői" engedélyeket (vagy hasonlókat) a szabályzat-hozzárendelés elsődleges AZONOSÍTÓjának.|
|\[Előzetes\]verzió: A Linux rendszerű virtuális gépek Log Analytics ügynökének üzembe helyezése|Szabályzat-hozzárendelés|Nem kötelező: A hatókörbe felvenni kívánt Linux operációs rendszert futtató virtuálisgép-rendszerképek listája|Egy üres tömb is felhasználható a nem kötelező paraméterek jelölésére:\[\]|
|\[Előzetes\]verzió: A Windows VM Scale Sets (VMSS) Log Analytics ügynökének üzembe helyezése|Szabályzat-hozzárendelés|Log Analytics munkaterület a Windows VM Scale Setshoz (VMSS)|Ha ez a munkaterület kívül esik a hozzárendelés hatókörén, manuálisan kell megadnia a "Log Analytics közreműködői" engedélyeket (vagy hasonlókat) a szabályzat-hozzárendelés elsődleges AZONOSÍTÓjának.|
|\[Előzetes\]verzió: A Windows VM Scale Sets (VMSS) Log Analytics ügynökének üzembe helyezése|Szabályzat-hozzárendelés|Nem kötelező: A hatókörbe felvenni kívánt Windows operációs rendszert futtató virtuálisgép-rendszerképek listája|Egy üres tömb is felhasználható a nem kötelező paraméterek jelölésére:\[\]|
|\[Előzetes\]verzió: Windows rendszerű virtuális gépek Log Analytics ügynökének üzembe helyezése|Szabályzat-hozzárendelés|Log Analytics munkaterület a Windows rendszerű virtuális gépekhez|Ha ez a munkaterület kívül esik a hozzárendelés hatókörén, manuálisan kell megadnia a "Log Analytics közreműködői" engedélyeket (vagy hasonlókat) a szabályzat-hozzárendelés elsődleges AZONOSÍTÓjának.|
|\[Előzetes\]verzió: Windows rendszerű virtuális gépek Log Analytics ügynökének üzembe helyezése|Szabályzat-hozzárendelés|Nem kötelező: A hatókörbe felvenni kívánt Windows operációs rendszert futtató virtuálisgép-rendszerképek listája|Egy üres tömb is felhasználható a nem kötelező paraméterek jelölésére:\[\]|
|Engedélyezett tárfiók-termékváltozatok|Szabályzat-hozzárendelés|Engedélyezett tárolási SKU-ket tartalmazó lista|A tárfiókok esetében megadható termékváltozatok listája.|
|Engedélyezett VM-termékváltozatok|Szabályzat-hozzárendelés|Engedélyezett virtuálisgép-SKU-ket tartalmazó lista|A virtuális gépek esetében megadható termékváltozatok listája.|
|ISO 27001-alapú tervkezdeményezés|Szabályzat-hozzárendelés|Azon erőforrástípusok listája, amelyek esetében engedélyezni kell a diagnosztikai naplókat|A naplózni kívánt erőforrástípusok listája, ha a diagnosztikai napló beállítása nincs engedélyezve. Elfogadható értékek találhatók [Azure monitor diagnosztikai naplók sémái](../../../../azure-monitor/platform/diagnostic-logs-schema.md#supported-log-categories-per-resource-type)között.|

## <a name="next-steps"></a>További lépések

Most, hogy áttekintette az ISO 27001 tervrajzi minta üzembe helyezésének lépéseit, tekintse meg az alábbi cikkeket az architektúra és a vezérlés leképezésének megismeréséhez:

> [!div class="nextstepaction"]
> [ISO 27001 terv – áttekintés](./index.md)
> [ISO 27001 terv – vezérlés leképezése](./control-mapping.md)

További cikkek a tervekről és azok használatáról:

- Tudnivalók a [tervek életciklusáról](../../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../../how-to/update-existing-assignments.md) elsajátítása.
