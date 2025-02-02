---
title: Az Azure Resource Graph áttekintése
description: Ismerje meg, hogyan az Azure-erőforrás Graph szolgáltatás lehetővé teszi, hogy nagy mennyiségű erőforrást összetett lekérdezés.
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/06/2019
ms.topic: overview
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: d78c640f4269c799d3d371e6dd9db477faf96694
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807414"
---
# <a name="overview-of-the-azure-resource-graph-service"></a>Az Azure-erőforrás Graph szolgáltatás áttekintése

Az Azure Erőforrás-grafikon egy olyan szolgáltatás, az Azure-ban, amely való kiterjesztésére szolgál Azure Resource Management azáltal, hogy hatékony és nagy teljesítményű erőforrások feltárási és ipari méretekben lekérdezés lehetővé teszi egy adott halmazát az előfizetések között, hogy hatékonyan szabályozhatja a környezet. Ezek a lekérdezések a következő funkciókat biztosítják:

- Erőforrások lekérdezése az erőforrás-tulajdonságok alapján végzett összetett szűréssel, csoportosítással és rendezéssel.
- Azon képessége, iteratív a források a cégirányítási követelmények alapján.
- Szabályzatok alkalmazásából adódó következmények felmérése kiterjedt felhőkörnyezetben.
- Lehetővé teszi [erőforrás-tulajdonságok módosításait részletezik](./how-to/get-resource-changes.md) (előzetes verzió).

Ez a dokumentáció mindegyik funkciót részletesen tárgyalja.

> [!NOTE]
> Az Azure Erőforrás-grafikon működteti az Azure portál keresősávjában, az új tallózási "Minden erőforrás" élményt és az Azure Policy [módosítási előzmények](../policy/how-to/determine-non-compliance.md#change-history-preview)
> _diff vizuális_. Úgy van kialakítva, hogy az ügyfelek kezelése nagyméretű környezetekben.

[!INCLUDE [service-provider-management-toolkit](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="how-does-resource-graph-complement-azure-resource-manager"></a>Hogyan egészíti ki a Resource Graph az Azure Resource Managert

Az Azure Resource Manager jelenleg támogatja lekérdezések keresztül alapvető erőforrás mezők, kifejezetten - erőforrás nevét, Azonosítóját, típus, erőforráscsoport, előfizetés és hely. Erőforrás-kezelő szolgáltatásokat egyéni erőforrás-szolgáltató hívása egy erőforrás részletes tulajdonságok egyszerre is tartalmazza.

Az Azure Resource Graph segítségével az erőforrás-szolgáltatók egyenkénti hívása nélkül is hozzáférhet az általuk visszaadott tulajdonságokhoz. Támogatott erőforrástípusait listáját, keressen egy **Igen** a a [erőforrások teljes üzemmód telepítéseit](../../azure-resource-manager/complete-mode-deletion.md) tábla.

Az Azure Erőforrás-grafikon a következőket teheti:

- Hozzáférés a anélkül, hogy az egyes hívásokat mindegyik erőforrás-szolgáltató erőforrás-szolgáltató által visszaadott tulajdonságait.
- Az elmúlt 14 napban tulajdonságok változott az erőforrás végzett módosítási előzmények megtekintése és mikor. (előzetes verzió)

## <a name="how-resource-graph-is-kept-current"></a>Hogyan tárolódik aktuális erőforrás-grafikon

Egy Azure-erőforrás frissítésekor az Erőforrás-grafikon Resource Manager által a változás értesítést kap.
Erőforrás-grafikon majd frissíti az adatbázist. Erőforrás-grafikon is elvégzi a szokványos _teljes vizsgálat_. Ez a vizsgálat biztosítja, hogy erőforrás gráfadatok aktuális kihagyott értesítések esetén, vagy egy erőforrás-en kívül a Resource Manager frissítésekor.

## <a name="the-query-language"></a>A lekérdezőnyelv

Most, hogy már jobban érti az Azure Resource Graph lényegét, ismerkedjünk meg közelebbről a lekérdezések összeállításával.

Fontos megérteni, hogy az Azure Resource Graph lekérdezési nyelv alapul a [Kusto-lekérdezés nyelvi](../../data-explorer/data-explorer-overview.md) Azure Data Explorer által használt.

Első lépésként olvassa el az Azure Resource Graphfal használható műveleteket és funkciókat ismertető, [a Resource Graph lekérdezőnyelve](./concepts/query-language.md) című cikket.
Az erőforrások tallózását az [erőforrások kezeléséről](./concepts/explore-resources.md) szóló cikk írja le.

## <a name="permissions-in-azure-resource-graph"></a>Engedélyek az Azure Resource Graphban

A Resource Graph használatához megfelelő jogosultságokkal kell rendelkeznie a [szerepköralapú hozzáférés-vezérlésben](../../role-based-access-control/overview.md) (RBAC), és legalább olvasási jogosultsággal kell rendelkeznie a lekérdezni kívánt erőforrásokon. Ha nem rendelkezik legalább `read` engedélyekkel az Azure-objektumhoz vagy -objektumcsoporthoz, a rendszer nem ad vissza eredményeket.

> [!NOTE]
> Erőforrás-grafikon egy egyszerű elérhető előfizetések használja a bejelentkezés során. Aktív munkamenet során hozzáadott új előfizetés erőforrások megtekintéséhez az egyszerű frissítenie kell a környezetben. Ez a művelet automatikusan megtörténik, amikor kijelentkezik, majd újból.

## <a name="throttling"></a>Throttling

Ingyenes szolgáltatás erőforrás-grafikon a lekérdezések szabályozott a legjobb felhasználói élményt és válasz ideje biztosít minden ügyfél számára. Ha a szervezet által támogatni kívánt nagy méretű és gyakori lekérdezések a erőforrás Graph API használatával, használja a portál "Visszajelzés" a [erőforrás Graph portáloldalán](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/ResourceGraph).
Adja meg az üzleti esetekhez, és jelölje be a "Microsoft e-mail üzeneteket küldhet Önnek a Visszajelzésével kapcsolatban" jelölőnégyzetet ahhoz, hogy a csapat Önnel a kapcsolatot.

Erőforrás-grafikon felhasználói szinten lekérdezések szabályozza. A szolgáltatás válasza a következő HTTP-fejléceket tartalmazza:

- `x-ms-user-quota-remaining` (int): A felhasználó többi erőforrás kvótáját. Ez az érték lekérdezés száma képezi le.
- `x-ms-user-quota-resets-after` (ÓÓ:) Az időtartamot, amíg a felhasználó kvóta fogyasztás alaphelyzetbe állítása

További információkért lásd: [szabályozott kérelmeinek útmutatást](./concepts/guidance-for-throttled-requests.md).

## <a name="running-your-first-query"></a>Az első lekérdezés futtatása

Erőforrás-grafikon támogatja a .NET-hez készült Azure CLI-vel, az Azure PowerShell és az Azure SDK-t. A lekérdezés szerkezete ugyanaz az egyes nyelvekhez. Útmutató a Resource Graph engedélyezéséhez az [Azure CLI-ben](first-query-azurecli.md#add-the-resource-graph-extension) és az [Azure PowerShellben](first-query-powershell.md#add-the-resource-graph-module).

## <a name="next-steps"></a>További lépések

- Az első lekérdezés futtatása [Azure CLI-vel](first-query-azurecli.md).
- Az első lekérdezés futtatása [Azure PowerShell-lel](first-query-powershell.md).
- Kezdje [alapszintű lekérdezéseket](./samples/starter.md).
- A elmélyítse [speciális lekérdezések](./samples/advanced.md).