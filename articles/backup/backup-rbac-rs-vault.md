---
title: Biztonsági másolatok kezelése az Azure szerepköralapú Access Controlával
description: Szerepköralapú Access Control segítségével kezelheti a biztonsági mentési felügyeleti műveletekhez való hozzáférést Recovery Services-tárolóban.
author: utraghuv
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: utraghuv
ms.openlocfilehash: 928c08862fdb8a447b6b7afdd7fc12317a201224
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68464962"
---
# <a name="use-role-based-access-control-to-manage-azure-backup-recovery-points"></a>Szerepköralapú Access Control használata Azure Backup helyreállítási pontok kezeléséhez
Az Azure Szerepköralapú hozzáférés-vezérlés (RBAC) részletes hozzáférés-vezérlést biztosít az Azure-hoz. Az RBAC használata lehetővé teszi, hogy elkülönítse a kötelességeket a csapaton belül, valamint csak olyan mértékű hozzáférést biztosítson, amelyre a felhasználóknak a feladataik elvégzéséhez szüksége van.

> [!IMPORTANT]
> Az Azure Backup által biztosított szerepkörök csak olyan műveletekre korlátozódnak, amelyek Azure Portal vagy REST API vagy Recovery Services Vault PowerShell vagy CLI parancsmagok segítségével hajthatók végre. Az Azure Backup-ügynök felhasználói felületén vagy a System Center Data Protection Manager felhasználói felületén vagy a Azure Backup Server felhasználói felületen végrehajtott műveletek nem tartoznak a szerepkörök felügyeletéhez.

A Azure Backup három beépített szerepkört biztosít a biztonságimásolat-kezelési műveletek vezérléséhez. További információ az [Azure beépített RBAC-szerepköreivel](../role-based-access-control/built-in-roles.md) kapcsolatban.

* [Biztonsági másolat közreműködői](../role-based-access-control/built-in-roles.md#backup-contributor) – ez a szerepkör rendelkezik a biztonsági másolatok létrehozásához és kezeléséhez szükséges engedélyekkel, kivéve a Recovery Services-tároló törlését és másokhoz való hozzáférést biztosít. Képzelje el ezt a szerepkört a biztonsági mentési felügyelet rendszergazdájának, aki minden biztonsági mentési felügyeleti műveletet végrehajthat.
* [Biztonságimásolat-felelős](../role-based-access-control/built-in-roles.md#backup-operator) – ez a szerepkör a biztonsági mentést és a biztonsági mentési házirendek felügyeletét kivéve minden közreműködőhöz rendelkezik engedéllyel. Ez a szerepkör egyenértékű a közreműködővel, kivéve, ha nem tud olyan romboló műveleteket végezni, mint például a biztonsági mentés leállítása vagy a helyszíni erőforrások regisztrációjának törlése.
* [Backup Reader](../role-based-access-control/built-in-roles.md#backup-reader) – ez a szerepkör jogosult az összes biztonsági mentési felügyeleti művelet megtekintésére. Képzelje el, hogy ezt a szerepkört figyelő személynek kell lennie.

Ha a saját szerepköröket is meg szeretné határozni még több szabályozáshoz, tekintse meg az [Egyéni szerepkörök létrehozása](../role-based-access-control/custom-roles.md) az Azure RBAC című témakört.



## <a name="mapping-backup-built-in-roles-to-backup-management-actions"></a>Beépített szerepkörök biztonsági mentésének leképezése biztonsági mentési felügyeleti műveletekre
Az alábbi táblázat a művelet végrehajtásához szükséges biztonságimásolat-kezelési műveleteket és a hozzá tartozó minimális RBAC szerepkört rögzíti.

| Kezelési művelet | Minimálisan szükséges RBAC-szerepkör | Hatókör szükséges |
| --- | --- | --- |
| Helyreállítási tár létrehozása | Biztonsági mentési közreműködő | A tárolót tartalmazó erőforráscsoport |
| Azure-beli virtuális gépek biztonsági mentésének engedélyezése | Biztonsági mentési operátor | A tárolót tartalmazó erőforráscsoport |
| | Virtuális gépek közreműködője | VM-erőforrás |
| Virtuális gép igény szerinti biztonsági mentése | Biztonsági mentési operátor | Helyreállítási tár erőforrása |
| Virtuális gép visszaállítása | Biztonsági mentési operátor | Helyreállítási tár |
| | Közreműködő | Az erőforráscsoport, amelyben a virtuális gép üzembe lesz helyezve |
| | Virtuális gépek közreműködője | A forrásként szolgáló virtuális gép, amelyről biztonsági mentés készül |
| Nem felügyelt lemezek visszaállítása virtuális gép biztonsági mentése | Biztonsági mentési operátor | Helyreállítási tár erőforrása |
| | Virtuális gépek közreműködője | A forrásként szolgáló virtuális gép, amelyről biztonsági mentés készül |
| | Tárfiók-közreműködő | A Storage-fiók erőforrása, ahol a lemezek vissza lesznek állítva |
| Felügyelt lemezek visszaállítása a virtuális gép biztonsági másolatából | Biztonsági mentési operátor | Helyreállítási tár erőforrása |
| | Virtuális gépek közreműködője | A forrásként szolgáló virtuális gép, amelyről biztonsági mentés készül |
| | Tárfiók-közreműködő | A visszaállítás részeként kijelölt ideiglenes Storage-fiók, amely a tárolóból származó adatok tárolására szolgál, mielőtt átalakítja őket a felügyelt lemezekre. |
| | Közreműködő | Az erőforráscsoport, amelybe a felügyelt lemez (ek) vissza lesz állítva |
| Egyedi fájlok visszaállítása a virtuális gép biztonsági másolatából | Biztonsági mentési operátor | Helyreállítási tár erőforrása |
| | Virtuális gépek közreműködője | A forrásként szolgáló virtuális gép, amelyről biztonsági mentés készül |
| Biztonsági mentési szabályzat létrehozása az Azure-beli virtuális gépek biztonsági mentéséhez | Biztonsági mentési közreműködő | Helyreállítási tár erőforrása |
| Azure-beli virtuális gép biztonsági mentési szabályzatának módosítása | Biztonsági mentési közreműködő | Helyreállítási tár erőforrása |
| Azure-beli virtuális gép biztonsági mentési szabályzatának törlése | Biztonsági mentési közreműködő | Helyreállítási tár erőforrása |
| Biztonsági mentés leállítása (az adat megőrzése vagy az adat törlése) a virtuális gép biztonsági mentésében | Biztonsági mentési közreműködő | Helyreállítási tár erőforrása |
| Helyszíni Windows Server/Client/SCDPM vagy Azure Backup Server regisztrálása | Biztonsági mentési operátor | Helyreállítási tár erőforrása |
| Regisztrált helyszíni Windows Server/Client/SCDPM vagy Azure Backup Server törlése | Biztonsági mentési közreműködő | Helyreállítási tár erőforrása |

> [!IMPORTANT]
> Ha a virtuálisgép-erőforrás hatókörében megad egy virtuálisgép-közreműködőt, és a virtuálisgép-beállítások részeként a biztonsági mentés elemre kattint, akkor a biztonsági mentés engedélyezése képernyő is megnyílik, bár a virtuális gép már biztonsági másolatként működik, mert a biztonsági mentési állapot ellenőrzésének hívása csak az előfizetés szintjén működik. Ennek elkerüléséhez nyissa meg a tárolót, és nyissa meg a virtuális gép biztonsági mentési elemének nézetét, vagy a virtuális gép közreműködői szerepkört egy előfizetési szinten válassza.

## <a name="minimum-role-requirements-for-the-azure-file-share-backup"></a>Az Azure-fájlmegosztás biztonsági mentésének minimális szerepköri követelményei
Az alábbi táblázat rögzíti az Azure fájlmegosztás művelet végrehajtásához szükséges biztonságimásolat-kezelési műveleteket és a hozzá tartozó szerepkört.

| Kezelési művelet | Szerepkör szükséges | További források |
| --- | --- | --- |
| Azure-fájlmegosztás biztonsági mentésének engedélyezése | Biztonsági mentési közreműködő | Helyreállítási tár |
| | Tárfiók | Közreműködői Storage-fiók erőforrása |
| Virtuális gép igény szerinti biztonsági mentése | Biztonsági mentési operátor | Helyreállítási tár |
| Fájlmegosztás visszaállítása | Biztonsági mentési operátor | Helyreállítási tár |
| | Tárfiók-közreműködő | A Storage-fiók erőforrásai, ahol a visszaállítási forrás és a célfájl-megosztások találhatók |
| Egyedi fájlok visszaállítása | Biztonsági mentési operátor | Helyreállítási tár |
| | Tárfiók-közreműködő |   A Storage-fiók erőforrásai, ahol a visszaállítási forrás és a célfájl-megosztások találhatók |
| Védelem leállítása | Biztonsági mentési közreműködő | Helyreállítási tár |      
| Storage-fiók regisztrációjának törlése a tárból |   Biztonsági mentési közreműködő | Helyreállítási tár |
| | Tárfiók-közreműködő | Storage-fiók erőforrása|


## <a name="next-steps"></a>További lépések
* [Szerepkör-alapú Access Control](../role-based-access-control/role-assignments-portal.md): Ismerkedés a RBAC szolgáltatással a Azure Portalban.
* Ismerje meg, hogyan kezelheti a hozzáférést a következővel:
  * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [Azure CLI](../role-based-access-control/role-assignments-cli.md)
  * [REST API](../role-based-access-control/role-assignments-rest.md)
* [Szerepköralapú Access Control hibaelhárítás](../role-based-access-control/troubleshooting.md): Javaslatok a gyakori problémák kijavítására.
