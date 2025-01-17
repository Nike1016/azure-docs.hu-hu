---
title: Hálózati biztonsági csoport adatfolyam-naplófájljainak kezelése az Azure Network Watcher-PowerShell használatával | Microsoft Docs
description: Ez az oldal ismerteti, hogyan kezelhetők a hálózati biztonsági csoportok folyamatábrái az Azure Network Watcher a PowerShell-lel
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 2dfc3112-8294-4357-b2f8-f81840da67d3
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: c34ed9c5f1b5e422ba9e4e0b12fbaf833c8a4a7c
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69563464"
---
# <a name="configuring-network-security-group-flow-logs-with-powershell"></a>Hálózati biztonsági csoport Folyamatábráinak konfigurálása a PowerShell-lel

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)

A hálózati biztonsági csoport folyamatábrái a Network Watcher szolgáltatása, amely lehetővé teszi, hogy a hálózati biztonsági csoporton keresztül megtekintse a bejövő és kimenő IP-forgalomra vonatkozó információkat. Ezeket a folyamatokat a rendszer JSON formátumban írja be, és a kimenő és bejövő folyamatokat egy szabály alapján jeleníti meg, a flow a folyamatra vonatkozik, 5 rekordos információ a folyamatról (forrás/cél IP-cím, forrás/cél port, protokoll), és ha a forgalom engedélyezett vagy megtagadott volt.

## <a name="register-insights-provider"></a>Insights-szolgáltató regisztrálása

Ahhoz, hogy a folyamatok naplózása sikeresen működjön, regisztrálni kell a **Microsoft. bepillantást** nyújtó szolgáltatót. Ha nem biztos abban, hogy a **Microsoft.** betekintő szolgáltató regisztrálva van, futtassa az alábbi szkriptet.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Insights
```

## <a name="enable-network-security-group-flow-logs-and-traffic-analytics"></a>Hálózati biztonsági csoport Folyamatábráinak és Traffic Analyticsának engedélyezése

A flow-naplók engedélyezésére szolgáló parancs az alábbi példában látható:

```powershell
$NW = Get-AzNetworkWatcher -ResourceGroupName NetworkWatcherRg -Name NetworkWatcher_westcentralus
$nsg = Get-AzNetworkSecurityGroup -ResourceGroupName nsgRG -Name nsgName
$storageAccount = Get-AzStorageAccount -ResourceGroupName StorageRG -Name contosostorage123
Get-AzNetworkWatcherFlowLogStatus -NetworkWatcher $NW -TargetResourceId $nsg.Id

#Traffic Analytics Parameters
$workspaceResourceId = "/subscriptions/bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb/resourcegroups/trafficanalyticsrg/providers/microsoft.operationalinsights/workspaces/taworkspace"
$workspaceGUID = "cccccccc-cccc-cccc-cccc-cccccccccccc"
$workspaceLocation = "westeurope"

#Configure Version 1 Flow Logs
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 1

#Configure Version 2 Flow Logs, and configure Traffic Analytics
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 2

#Configure Version 2 FLow Logs with Traffic Analytics Configured
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 2 -EnableTrafficAnalytics -WorkspaceResourceId $workspaceResourceId -WorkspaceGUID $workspaceid -WorkspaceLocation $workspaceRegion

#Query Flow Log Status
Get-AzNetworkWatcherFlowLogStatus -NetworkWatcher $NW -TargetResourceId $nsg.Id
```

A megadott Storage-fiók nem rendelkezhet olyan hálózati szabályokkal, amelyek csak a Microsoft-szolgáltatásokhoz vagy adott virtuális hálózatokhoz való hálózati hozzáférést korlátozzák. A Storage-fiók azonos vagy egy másik Azure-előfizetéssel is rendelkezhet, mint a NSG. Ha különböző előfizetéseket használ, mindkettőhöz ugyanahhoz a Azure Active Directory bérlőhöz kell tartoznia. Az egyes előfizetésekhez használt fióknak rendelkeznie kell a [szükséges engedélyekkel](required-rbac-permissions.md).

## <a name="disable-traffic-analytics-and-network-security-group-flow-logs"></a>Traffic Analytics és hálózati biztonsági csoport Folyamatábráinak letiltása

A következő példa a Traffic Analytics és a flow-naplók letiltására használható:

```powershell
#Disable Traffic Analaytics by removing -EnableTrafficAnalytics property
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 2 -WorkspaceResourceId $workspaceResourceId -WorkspaceGUID $workspaceGUID -WorkspaceLocation $workspaceLocation

#Disable Flow Logging
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $false
```

## <a name="download-a-flow-log"></a>Flow-napló letöltése

A folyamat naplójának tárolási helye a létrehozáskor van meghatározva. A rendszer a Storage-fiókba mentett adatforgalmi naplók elérésére alkalmas eszközt Microsoft Azure Storage Explorer, amely letölthető innen: https://storageexplorer.com/

Ha meg van adva Storage-fiók, a rendszer a flow-naplófájlokat a következő helyen menti a Storage-fiókba:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```
> [!IMPORTANT]
> Jelenleg hiba történt a [hálózati biztonsági csoport (NSG)](network-watcher-nsg-flow-logging-overview.md) Network Watcher adatforgalmának naplózása során, ezért a rendszer nem törli automatikusan a blob Storage-ból a megőrzési házirend beállításai alapján. Ha meglévő, nem nulla adatmegőrzési szabályzattal rendelkezik, javasoljuk, hogy rendszeresen törölje a megőrzési időtartamon felüli tárolási blobokat a felmerülő költségek elkerülése érdekében. További információ a NSG flow log-tárolási blog törléséről: [NSG flow log Storage-Blobok törlése](network-watcher-delete-nsg-flow-log-blobs.md).

A naplófájl felépítésével kapcsolatos információkért lásd: a [hálózati biztonsági csoport folyamatábrájának áttekintése](network-watcher-nsg-flow-logging-overview.md)

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan jelenítheti meg [a NSG flow-naplókat a PowerBI](network-watcher-visualize-nsg-flow-logs-power-bi.md) használatával

Ismerje meg, hogyan [jelenítheti meg a NSG-flow-naplókat nyílt forráskódú eszközökkel](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
