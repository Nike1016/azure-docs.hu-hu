---
title: Az Azure PowerShell-Példaszkript – egy másik előfizetéshez visszaállítási alkalmazás biztonsági másolatának |} A Microsoft Docs
description: Az Azure PowerShell-Példaszkript – webalkalmazás egy másik előfizetésben biztonsági visszaállítása
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jpconnoc
editor: ''
tags: azure-service-management
ms.assetid: a2a27d94-d378-4c17-a6a9-ae1e69dc4a72
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 11/21/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: a33df69a10dc803c60652c64a11a137f085e5c61
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "66136552"
---
# <a name="restore-a-web-app-from-a-backup-in-another-subscription-using-powershell"></a>Webalkalmazás visszaállítása biztonsági másolatból egy másik előfizetésben, PowerShell-lel

Ez a példaszkript egy korábban elvégzett biztonsági mentés beolvassa egy meglévő web Apps, és annak visszaállítására egy másik előfizetésben található webalkalmazás. 

Szükség esetén telepítse az Azure PowerShellt az [Azure PowerShell útmutatójának](/powershell/azure/overview) utasításait követve, majd a `Connect-AzAccount` futtatásával hozza létre a kapcsolatot az Azure-ral. 

## <a name="sample-script"></a>Példaszkript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/backup-restore-diff-sub/backup-restore-diff-sub.ps1?highlight=1-6 "Restore a web app from a backup in another subscription")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

Ha többé már nincs szüksége a webalkalmazást, a következő paranccsal törölheti az erőforráscsoportot, a webalkalmazás, és az összes kapcsolódó erőforrás.

```powershell
Remove-AzResourceGroup -Name $resourceGroupName -Force
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [Add-AzAccount](/powershell/module/az.accounts/connect-azaccount) | Az Azure Resource Manager parancsmag-kérelmek egy hitelesített fiók hozzáadása.  |
| [Get-AzWebAppBackupList](/powershell/module/az.websites/get-azwebappbackuplist) | Lekéri egy webalkalmazás biztonsági másolatainak listáját. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Egy webalkalmazást hoz létre |
| [Restore-AzWebAppBackup](/powershell/module/az.websites/restore-azwebappbackup) | Webalkalmazás visszaállítása egy korábban elvégzett biztonsági másolatból. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

További Azure Powershell-példákat az Azure App Service Web Appshez az [Azure PowerShell-példák](../samples-powershell.md) között találhat.
