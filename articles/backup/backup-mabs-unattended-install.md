---
title: Azure Backup Server v2 csendes telepítése
description: Használjon PowerShell-szkriptet a Azure Backup Server v2 csendes telepítéséhez. Ezt a telepítési típust felügyelet nélküli telepítésnek is nevezik.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: dacurwin
ms.openlocfilehash: 4bce7aeebee729ed253d39720ef520880c261a22
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639650"
---
# <a name="run-an-unattended-installation-of-azure-backup-server"></a>Azure Backup Server felügyelet nélküli telepítésének futtatása

Megtudhatja, hogyan futtathatja Azure Backup Server felügyelet nélküli telepítését.

Ezek a lépések nem érvényesek Azure Backup Server v1 telepítésekor.

## <a name="install-backup-server"></a>A biztonsági mentési kiszolgáló telepítése

1. A Azure Backup Server v2 vagy újabb verziót futtató kiszolgálón hozzon létre egy szövegfájlt. (A fájlt a Jegyzettömbben vagy egy másik szövegszerkesztőben is létrehozhatja.) Mentse a fájlt MABSSetup. ini néven.

2. Illessze be a következő kódot a MABSSetup. ini fájlba. Cserélje le a zárójelben (\< \>) lévő szöveget a környezetének megfelelő értékekre. A következő szöveg egy példa:

   ```
   [OPTIONS]
   UserName=administrator
   CompanyName=<Microsoft Corporation>
   SQLMachineName=localhost
   SQLInstanceName=<SQL instance name>
   SQLMachineUserName=administrator
   SQLMachinePassword=<admin password>
   SQLMachineDomainName=<machine domain>
   ReportingMachineName=localhost
   ReportingInstanceName=<reporting instance name>
   SqlAccountPassword=<admin password>
   ReportingMachineUserName=<username>
   ReportingMachinePassword=<reporting admin password>
   ReportingMachineDomainName=<domain>
   VaultCredentialFilePath=<vault credential full path and complete name>
   SecurityPassphrase=<passphrase>
   PassphraseSaveLocation=<passphrase save location>
   UseExistingSQL=<1/0 use or do not use existing SQL>
   ```

3. Mentse a fájlt. Ezután a telepítési kiszolgálón egy rendszergazda jogú parancssorba írja be a következő parancsot:

   ```
   start /wait <cdlayout path>/Setup.exe /i  /f <.ini file path>/setup.ini /L <log path>/setup.log
   ```

Az alábbi jelzőket használhatja a telepítéshez:</br>
**/f**:. ini fájl elérési útja</br>
**/l**: Napló elérési útja</br>
**/i**: Telepítési útvonal</br>
**/x**: Eltávolítási útvonal</br>

## <a name="next-steps"></a>További lépések
A Backup Server telepítése után megtudhatja, hogyan készítheti elő a kiszolgálót, vagy megkezdheti a munkaterhelések védelmét.

- [A biztonsági mentési kiszolgáló munkaterhelésének előkészítése](backup-azure-microsoft-azure-backup.md)
- [Egy VMware-kiszolgáló biztonsági mentése a Backup Server használatával](backup-azure-backup-server-vmware.md)
- [Biztonsági másolat készítése a Backup Server használatával SQL Server](backup-azure-sql-mabs.md)
- [modern biztonsági másolati tárhely hozzáadása a biztonsági mentési kiszolgálóhoz](backup-mabs-add-storage.md)
