---
title: Az Azure-beli virtuális gépen futó SQL Server Azure Backup
description: Az Azure-beli virtuális gépen futó Azure Backup SQL Server regisztrálása
services: backup
author: swatisachdeva
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: vijayts
ms.openlocfilehash: 25f23078af67b2f80f39faab975cbec54721c560
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/15/2019
ms.locfileid: "67871900"
---
# <a name="azure-backup-for-sql-server-running-in-azure-vm"></a>Az Azure-beli virtuális gépen futó SQL Server Azure Backup

Azure Backup többek között az Azure-beli virtuális gépeken futó munkaterhelések biztonsági mentését SQL Server is támogatja. Mivel az SQL-alkalmazás egy Azure-beli virtuális gépen fut, a Backup szolgáltatásnak engedéllyel kell rendelkeznie az alkalmazás eléréséhez és a szükséges adatok beolvasásához.
Ehhez Azure Backup telepíti a **AzureBackupWindowsWorkload** bővítményt a virtuális gépre, amelyben a SQL Server fut, a felhasználó által aktivált regisztrációs folyamat során.

## <a name="prerequisites"></a>Előfeltételek

A támogatott forgatókönyvek listáját a Azure Backup által támogatott [támogatási mátrixban](https://docs.microsoft.com/azure/backup/backup-azure-sql-database#scenario-support) találja.

## <a name="network-connectivity"></a>Hálózati kapcsolat

Azure Backup támogatja a NSG-címkéket, a proxykiszolgáló vagy a felsorolt IP-címtartományok telepítését; az egyes módszerekkel kapcsolatos részletekért tekintse meg ezt a [cikket](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#establish-network-connectivity).

## <a name="extension-schema"></a>Bővítményséma

A bővítmény sémája és a tulajdonság értéke a szolgáltatás által a CRP API-nak áthaladó konfigurációs értékek (futásidejű beállítások). Ezek a konfigurációs értékek a regisztráció és a frissítés során használatosak. A **AzureBackupWindowsWorkload** -bővítmény ezt a sémát is használja. A séma előre be van állítva; a objectStr mezőben új paraméter adható hozzá

  ```json
      "runtimeSettings": [{
      "handlerSettings": {
      "protectedSettingsCertThumbprint": "",
      "protectedSettings": {
      "objectStr": "",
      "logsBlobUri": "",
      "statusBlobUri": ""
      }
      },
      "publicSettings": {
      "locale": "en-us",
      "taskId": "1c0ae461-9d3b-418c-a505-bb31dfe2095d",
      "objectStr": "",
      "commandStartTimeUTCTicks": "636295005824665976",
      "vmType": "vmType"
      }
      }]
      }
  ```

A következő JSON a WorkloadBackup-bővítmény sémáját jeleníti meg.  

  ```json
  {
    "type": "extensions",
    "name": "WorkloadBackup",
    "location":"<myLocation>",
    "properties": {
      "publisher": "Microsoft.RecoveryServices",
      "type": "AzureBackupWindowsWorkload",
      "typeHandlerVersion": "1.1",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "locale":"<location>",
        "taskId":"<TaskId used by Azure Backup service to communicate with extension>",

        "objectStr": "<The configuration passed by Azure Backup service to extension>",

        "commandStartTimeUTCTicks": "<Scheduled start time of registration or upgrade task>",
        "vmType": "<Type of VM where registration got triggered Eg. Compute or ClassicCompute>"
      },
      "protectedSettings": {
        "objectStr": "<The sensitive configuration passed by Azure Backup service to extension>",
        "logsBlobUri": "<blob uri where logs of command execution by extension are written to>",
        "statusBlobUri": "<blob uri where status of the command executed by extension is written>"
      }
    }
  }
  ```

### <a name="property-values"></a>Tulajdonságok értékei

Name (Név) | Érték/példa | Adattípus
 --- | --- | ---
területi beállítás | en-us  |  sztring
taskId | "1c0ae461-9d3b-418c-a505-bb31dfe2095d"  | sztring
objectStr <br/> (publicSettings)  | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzVjMjQxYTItOGRjNy00ZGE5LWI4NTMtMjdjYTJhNDZlM2ZkIiwiSWRNZ210Q29udGFpbmVySWQiOjM0NTY3ODg5LCJSZXNvdXJjZUlkIjoiMDU5NWIwOGEtYzI4Zi00ZmFlLWE5ODItOTkwOWMyMGVjNjVhIiwiU3Vic2NyaXB0aW9uSWQiOiJkNGEzOTliNy1iYjAyLTQ2MWMtODdmYS1jNTM5O DI3ZTgzNTQiLCJVbmlxdWVDb250YWluZXJOYW1lIjoiODM4MDZjODUtNTQ4OS00NmNhLWEyZTctNWMzNzNhYjg3OTcyIn0sInN0YW1wTGlzdCI6W3siU2VydmljZU5hbWUiOjUsIlNlcnZpY2VTdGFtcFVybCI6Imh0dHA6XC9cL015V0xGYWJTdmMuY29tIn1dfQ = = " | Karakterlánc
commandStartTimeUTCTicks | "636967192566036845"  | Karakterlánc
vmType  | "Microsoft. számítás/virtualmachines"  | sztring
objectStr <br/> Protectedsettingsfromkeyvault | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzVjMjQxYTItOGRjNy00ZGE5LWI4NTMtMjdjYTJhNDZlM2ZkIiwiSWRNZ210Q29udGFpbmVySWQiOjM0NTY3ODg5LCJSZXNvdXJjZUlkIjoiMDU5NWIwOGEtYzI4Zi00ZmFlLWE5ODItOTkwOWMyMGVjNjVhIiwiU3Vic2NyaXB0aW9uSWQiOiJkNGEzOTliNy1iYjAyLTQ2MWMtODdmYS1jNTM5O DI3ZTgzNTQiLCJVbmlxdWVDb250YWluZXJOYW1lIjoiODM4MDZjODUtNTQ4OS00NmNhLWEyZTctNWMzNzNhYjg3OTcyIn0sInN0YW1wTGlzdCI6W3siU2VydmljZU5hbWUiOjUsIlNlcnZpY2VTdGFtcFVybCI6Imh0dHA6XC9cL015V0xGYWJTdmMuY29tIn1dfQ = = " | Karakterlánc
logsBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | Karakterlánc
statusBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | sztring


## <a name="template-deployment"></a>Sablonalapú telepítés

Azt javasoljuk, hogy a AzureBackupWindowsWorkload bővítményt a virtuális géphez adja hozzá, ha engedélyezi SQL Server biztonsági mentést a virtuális gépen. Ez a SQL Server VMon történő biztonsági mentés automatizálására szolgáló [Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vm-workload-backup) -sablonon keresztül érhető el.


## <a name="powershell-deployment"></a>PowerShell-telepítés

Regisztrálnia kell az Azure-beli virtuális gépet, amely tartalmazza az SQL-alkalmazást a Recovery Services-tárolóval. A regisztráció során a rendszer telepíti a AzureBackupWindowsWorkload bővítményt a virtuális gépre. A virtuális gép regisztrálásához használja a [Register-AzRecoveryServicesBackupContainerPS](https://docs.microsoft.com/powershell/module/az.recoveryservices/Register-AzRecoveryServicesBackupContainer?view=azps-1.5.0) parancsmagot.
 
```powershell
$myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
```
 
A parancs visszaküldi az erőforrás **biztonsági mentési tárolóját** , és a rendszer **regisztrálja**az állapotot.


## <a name="next-steps"></a>További lépések

- [További](https://docs.microsoft.com/azure/backup/backup-sql-server-azure-troubleshoot) információ az Azure SQL Server VM Backup hibaelhárítási irányelveiről
- [Gyakori kérdések](https://docs.microsoft.com/azure/backup/faq-backup-sql-server) az Azure Virtual Machines szolgáltatásban (VM) futó SQL Server adatbázisok biztonsági mentéséről és azokról, amelyek az Azure Backup szolgáltatást használják.
