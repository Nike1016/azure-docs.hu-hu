---
title: Automatikus biztonsági mentés a SQL Server 2014 Azure Virtual Machineshoz | Microsoft Docs
description: Ismerteti az Azure-ban futó SQL Server 2014 virtuális gépek automatizált biztonsági mentési funkcióját. Ez a cikk a Resource Managert használó virtuális gépekre jellemző.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: bdc63fd1-db49-4e76-87d5-b5c6a890e53c
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/03/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 5c9d2acf3e58d233bd789e335c585f61511b975d
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68846217"
---
# <a name="automated-backup-for-sql-server-2014-virtual-machines-resource-manager"></a>SQL Server 2014 Virtual Machines automatikus biztonsági mentése (Resource Manager)

> [!div class="op_single_selector"]
> * [SQL Server 2014](virtual-machines-windows-sql-automated-backup.md)
> * [SQL Server 2016/2017](virtual-machines-windows-sql-automated-backup-v2.md)

Az automatikus biztonsági mentés automatikusan [úgy konfigurálja a felügyelt biztonsági mentést, hogy](https://msdn.microsoft.com/library/dn449496.aspx) az Azure-beli virtuális gépen lévő összes meglévő és új adatbázisra Microsoft Azure a SQL Server 2014 standard vagy Enterprise rendszert futtató Azure Ez lehetővé teszi, hogy az Azure Blob Storage-t használó normál adatbázis-biztonsági mentéseket konfigurálja. Az automatikus biztonsági mentés a [SQL Server IaaS-ügynök bővítménytől](virtual-machines-windows-sql-server-agent-extension.md)függ.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Előfeltételek
Az automatikus biztonsági mentés használatához vegye figyelembe a következő előfeltételeket:

**Operációs rendszer**:

- Windows Server 2012
- Windows Server 2012 R2
- Windows Server 2016

**SQL Server verzió/kiadás**:

- SQL Server 2014 Standard
- SQL Server 2014 Enterprise

> [!IMPORTANT]
> Az automatikus biztonsági mentés a SQL Server 2014-es verzióval működik. Ha SQL Server 2016/2017-es verziót használ, az adatbázisok biztonsági mentéséhez használhatja az automatikus Backup v2-t. További információ: [SQL Server 2016 Azure Virtual Machines automatikus biztonsági mentés v2](virtual-machines-windows-sql-automated-backup-v2.md)-es verziója.

**Adatbázis-konfiguráció**:

- A célként megadott adatbázisoknak a teljes helyreállítási modellt kell használniuk. A teljes helyreállítási modell biztonsági mentésekre gyakorolt hatásával kapcsolatos további információkért lásd: [biztonsági mentés a teljes helyreállítási modell alatt](https://technet.microsoft.com/library/ms190217.aspx).
- A célként megadott adatbázisoknak az alapértelmezett SQL Server példányon kell lenniük. A SQL Server IaaS bővítmény nem támogatja a nevesített példányokat.

> [!NOTE]
> Az automatikus biztonsági mentés a SQL Server IaaS-ügynök bővítményére támaszkodik. Az aktuális SQL-virtuálisgép-katalógus lemezképei alapértelmezés szerint a bővítményt adja hozzá. További információ: [SQL Server IaaS-ügynök bővítmény](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="settings"></a>Beállítások

Az alábbi táblázat az automatikus biztonsági mentéshez konfigurálható beállításokat ismerteti. A tényleges konfigurációs lépések attól függően változnak, hogy az Azure Portal vagy az Azure Windows PowerShell-parancsokat használja-e.

| Beállítás | Tartomány (alapértelmezett) | Leírás |
| --- | --- | --- |
| **Automatikus biztonsági mentés** | Engedélyezés/letiltás (letiltva) | Engedélyezheti vagy letilthatja a SQL Server 2014 standard vagy Enterprise rendszert futtató Azure-beli virtuális gépek automatizált biztonsági mentését. |
| **Megőrzési időtartam** | 1-30 nap (30 nap) | A biztonsági másolat megőrzésének napjainak száma. |
| **Tárfiók** | Azure Storage-fiók | Egy Azure Storage-fiók, amelyet a blob Storage-ban lévő automatizált biztonságimásolat-fájlok tárolására használ. Ezen a helyen létrejön egy tároló az összes biztonságimásolat-fájl tárolásához. A biztonságimásolat-fájl elnevezési konvenciója tartalmazza a dátumot, az időt és a gép nevét. |
| **Titkosítás** | Engedélyezés/letiltás (letiltva) | Engedélyezheti vagy letilthatja a titkosítást. Ha engedélyezve van a titkosítás, a biztonsági mentés visszaállításához használt tanúsítványok az azonos elnevezési konvencióval azonos `automaticbackup` tárolóban található megadott Storage-fiókban találhatók. Ha a jelszó megváltozik, új tanúsítvány jön létre ezzel a jelszóval, de a régi tanúsítvány továbbra is a korábbi biztonsági mentéseket állítja vissza. |
| **Jelszó** | Jelszó szövege | A titkosítási kulcsok jelszava. Erre csak akkor van szükség, ha engedélyezve van a titkosítás. A titkosított biztonsági mentés visszaállításához a biztonsági másolat készítésének időpontjában használt jelszóval és kapcsolódó tanúsítvánnyal kell rendelkeznie. |

## <a name="configure-in-the-portal"></a>Konfigurálás a portálon

A Azure Portal használatával automatikus biztonsági mentést konfigurálhat a kiépítés vagy a meglévő SQL Server 2014 virtuális gépek esetében.

## <a name="configure-new-vms"></a>Új virtuális gépek konfigurálása

Ha új SQL Server 2014 virtuális gépet hoz létre a Resource Manager-alapú üzemi modellben, a Azure Portal segítségével konfigurálja az automatikus biztonsági mentést.

A **SQL Server beállítások** lapon görgessen le az **automatikus biztonsági mentés** elemre, és válassza az **Engedélyezés**lehetőséget. Megadhatja a megőrzési időszakot és a Storage-fiókot, valamint engedélyezheti a titkosítást, a rendszeradatbázisok biztonsági mentését és a biztonsági mentési ütemterv konfigurálását is.  Az alábbi Azure Portal képernyőképen az **SQL-alapú automatikus biztonsági mentési** beállítások láthatók.

![SQL-alapú automatikus biztonsági mentési konfiguráció a Azure Portal](./media/virtual-machines-windows-sql-automated-backup/azure-sql-arm-autobackup.png)

## <a name="configure-existing-vms"></a>Meglévő virtuális gépek konfigurálása

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Meglévő SQL Server virtuális gépek esetében navigáljon az [SQL Virtual Machines erőforráshoz](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) , majd válassza a **biztonsági másolatok**lehetőséget. 

![SQL automatikus biztonsági mentés meglévő virtuális gépekhez](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-existing-vms.png)

Ha elkészült, kattintson a **biztonsági másolatok** oldal alján található **alkalmaz** gombra a módosítások mentéséhez.

Ha első alkalommal engedélyezi az automatikus biztonsági mentést, az Azure a háttérben konfigurálja a SQL Server IaaS-ügynököt. Ebben az időszakban előfordulhat, hogy a Azure Portal nem jelenik meg, hogy az automatikus biztonsági mentés konfigurálva van. Várjon néhány percet, amíg az ügynök telepítve van, konfigurálva van. Ezt követően a Azure Portal az új beállításokat fogja tükrözni.

> [!NOTE]
> Az automatikus biztonsági mentést sablon használatával is konfigurálhatja. További információ: Azure gyors üzembe helyezési [sablon az automatikus biztonsági mentéshez](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autobackup-update).

## <a name="configure-with-powershell"></a>Konfigurálás a PowerShell-lel

Az automatikus biztonsági mentést a PowerShell használatával is konfigurálhatja. A Kezdés előtt a következőket kell tennie:

- [Töltse le és telepítse a legújabb Azure PowerShell](https://aka.ms/webpi-azps).
- Nyissa meg a Windows PowerShellt, és társítsa azt a fiókhoz a **AzAccount** paranccsal.

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

### <a name="install-the-sql-iaas-extension"></a>Az SQL IaaS bővítmény telepítése
Ha kiépített egy SQL Server virtuális gépet a Azure Portalból, a SQL Server IaaS bővítménynek már telepítve kell lennie. A **Get-AzVM** parancs meghívásával és a bővítmények tulajdonság vizsgálatával megállapíthatja, hogy a virtuális géphez van-e telepítve.

```powershell
$vmname = "vmname"
$resourcegroupname = "resourcegroupname"

(Get-AzVM -Name $vmname -ResourceGroupName $resourcegroupname).Extensions
```

Ha a SQL Server IaaS-ügynök bővítmény telepítve van, akkor az "SqlIaaSAgent" vagy "SQLIaaSExtension" néven jelenik meg. A bővítmény **ProvisioningState** a "sikeres" kifejezést is meg kell jeleníteni.

Ha nincs telepítve vagy nem sikerült kiépíteni, akkor a következő paranccsal telepítheti. A virtuális gép neve és az erőforráscsoport mellett azt a régiót ( **$region**) is meg kell adnia, amelyben a virtuális gép található.

```powershell
$region = "EASTUS2"
Set-AzVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "1.2" -Location $region
```

> [!IMPORTANT]
> Ha a bővítmény még nincs telepítve, a bővítmény telepítése újraindítja a SQL Server szolgáltatást.

### <a id="verifysettings"></a>Aktuális beállítások ellenőrzése

Ha engedélyezte az automatikus biztonsági mentést a kiépítés során, a PowerShell segítségével ellenőrizze az aktuális konfigurációt. Futtassa a **Get-AzVMSqlServerExtension** parancsot, és vizsgálja meg a **AutoBackupSettings** tulajdonságot:

```powershell
(Get-AzVMSqlServerExtension -VMName $vmname -ResourceGroupName $resourcegroupname).AutoBackupSettings
```

A következőhöz hasonló kimenetnek kell megjelennie:

```
Enable                      : False
EnableEncryption            : False
RetentionPeriod             : -1
StorageUrl                  : NOTSET
StorageAccessKey            : 
Password                    : 
BackupSystemDbs             : False
BackupScheduleType          : 
FullBackupFrequency         : 
FullBackupStartTime         : 
FullBackupWindowHours       : 
LogBackupFrequency          : 
```

Ha a kimenet azt mutatja, hogy az **Engedélyezés** értéke **false (hamis**), akkor engedélyeznie kell az automatikus biztonsági mentést. A jó hír az, hogy az automatikus biztonsági mentést ugyanúgy engedélyezheti és konfigurálhatja. Tekintse meg ezt az információt a következő szakaszban.

> [!NOTE] 
> Ha azonnal bejelöli a beállításokat a módosítás után, akkor előfordulhat, hogy vissza fogja kérni a régi konfigurációs értékeket. Várjon néhány percet, és ellenőrizze újra a beállításokat, hogy megbizonyosodjon róla, hogy a módosítások érvénybe lépnek.

### <a name="configure-automated-backup"></a>Automatikus biztonsági mentés konfigurálása
A PowerShell használatával engedélyezheti az automatikus biztonsági mentést, valamint bármikor módosíthatja a konfigurációját és működését.

Először válasszon ki vagy hozzon létre egy Storage-fiókot a biztonságimásolat-fájlokhoz. A következő parancsfájl kiválasztja a Storage-fiókot, vagy létrehozza, ha nem létezik.

```powershell
$storage_accountname = “yourstorageaccount”
$storage_resourcegroupname = $resourcegroupname

$storage = Get-AzStorageAccount -ResourceGroupName $resourcegroupname `
    -Name $storage_accountname -ErrorAction SilentlyContinue
If (-Not $storage)
    { $storage = New-AzStorageAccount -ResourceGroupName $storage_resourcegroupname `
    -Name $storage_accountname -SkuName Standard_GRS -Location $region }
```

> [!NOTE]
> Az automatikus biztonsági mentés nem támogatja a biztonsági másolatok tárolását a Premium Storage-ban, de a Premium Storaget használó VM-lemezekről is készíthet biztonsági másolatokat.

Ezután a **New-AzVMSqlServerAutoBackupConfig** paranccsal engedélyezheti és konfigurálhatja az automatikus biztonsági mentési beállításokat a biztonsági másolatok Azure Storage-fiókban való tárolásához. Ebben a példában a biztonsági mentések 10 napig őrződnek meg. A második parancs, a **set-AzVMSqlServerExtension**, frissíti a megadott Azure-beli virtuális gépet ezekkel a beállításokkal.

```powershell
$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

A SQL Server IaaS-ügynök telepítése és konfigurálása több percet is igénybe vehet.

> [!NOTE]
> A **New-AzVMSqlServerAutoBackupConfig** egyéb beállításai is érvényesek, amelyek csak a SQL Server 2016 és az automatikus Backup v2-re vonatkoznak. A SQL Server 2014 nem támogatja a következő beállításokat: **BackupSystemDbs**, **BackupScheduleType**, **FullBackupFrequency**, **FullBackupStartHour**, **FullBackupWindowInHours**és **LogBackupFrequencyInMinutes**. Ha ezeket a beállításokat egy SQL Server 2014 virtuális gépen kísérli meg konfigurálni, nincs hiba, de a beállítások nem lesznek alkalmazva. Ha ezeket a beállításokat egy SQL Server 2016 virtuális gépen szeretné használni, tekintse meg [az automatikus Backup v2 SQL Server 2016 Azure Virtual Machineshoz](virtual-machines-windows-sql-automated-backup-v2.md)című témakört.

A titkosítás engedélyezéséhez módosítsa az előző szkriptet, hogy átadja a **EnableEncryption** paramétert a **CertificatePassword** paraméterhez tartozó jelszóval (Secure string) együtt. A következő parancsfájl lehetővé teszi az előző példában szereplő automatizált biztonsági mentési beállításokat, és titkosítja a titkosítást.

```powershell
$password = "P@ssw0rd"
$encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force

$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -EnableEncryption -CertificatePassword $encryptionpassword `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

A beállítások alkalmazásának megerősítéséhez [ellenőrizze az automatikus biztonsági mentés konfigurációját](#verifysettings).

### <a name="disable-automated-backup"></a>Automatikus biztonsági mentés letiltása

Az automatikus biztonsági mentés letiltásához futtassa ugyanazt a parancsfájlt anélkül, hogy az **-enable** paramétert a **New-AzVMSqlServerAutoBackupConfig** parancsra. A **-enable** paraméter hiánya jelzi, hogy a parancs letiltja a funkciót. A telepítéshez hasonlóan több percet is igénybe vehet, hogy letiltsa az automatizált biztonsági mentést.

```powershell
$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -ResourceGroupName $storage_resourcegroupname

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

### <a name="example-script"></a>Példaszkript

A következő parancsfájl egy olyan változót biztosít, amely testreszabható a virtuális gép automatizált biztonsági mentésének engedélyezéséhez és konfigurálásához. Előfordulhat, hogy a saját igényeinek megfelelően testre kell szabnia a parancsfájlt. Előfordulhat például, hogy módosításokat kell végeznie, ha le szeretné tiltani a rendszeradatbázisok biztonsági mentését, vagy engedélyeznie kell a titkosítást.

```powershell
$vmname = "yourvmname"
$resourcegroupname = "vmresourcegroupname"
$region = “Azure region name such as EASTUS2”
$storage_accountname = “storageaccountname”
$storage_resourcegroupname = $resourcegroupname
$retentionperiod = 10

# ResourceGroupName is the resource group which is hosting the VM where you are deploying the SQL IaaS Extension

Set-AzVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "1.2" -Location $region

# Creates/use a storage account to store the backups

$storage = Get-AzStorageAccount -ResourceGroupName $resourcegroupname `
    -Name $storage_accountname -ErrorAction SilentlyContinue
If (-Not $storage)
    { $storage = New-AzStorageAccount -ResourceGroupName $storage_resourcegroupname `
    -Name $storage_accountname -SkuName Standard_GRS -Location $region }

# Configure Automated Backup settings

$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays $retentionperiod -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

# Apply the Automated Backup settings to the VM

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

## <a name="monitoring"></a>Figyelés

A SQL Server 2014-es automatikus biztonsági mentés figyeléséhez két fő lehetőség közül választhat. Mivel az automatikus biztonsági mentés a SQL Server felügyelt biztonsági mentési szolgáltatást használja, ugyanaz a figyelési módszer is érvényes mindkét eszközre.

Először is lekérdezheti az állapotot a [msdb. smart_admin. sp_get_backup_diagnostics](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-get-backup-diagnostics-transact-sql)hívásával. Vagy a [msdb. smart_admin. fn_get_health_status](https://docs.microsoft.com/sql/relational-databases/system-functions/managed-backup-fn-get-health-status-transact-sql) tábla értékű függvény lekérdezése.

> [!NOTE]
> A SQL Server 2014 felügyelt biztonsági mentésének sémája a **msdb. smart_admin**. SQL Server 2016 ez a **msdb. managed_backup**értékre változott, a hivatkozási témakörök pedig ezt az újabb sémát használják. SQL Server 2014 esetében azonban továbbra is a **smart_admin** sémát kell használnia az összes felügyelt biztonsági mentési objektumhoz.

Egy másik lehetőség, hogy kihasználja az értesítések beépített Database Mail funkciójának előnyeit.

1. A [msdb. smart_admin. sp_set_parameter](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-set-parameter-transact-sql) tárolt eljárás meghívásával rendeljen hozzá egy e-mail-címet a **SSMBackup2WANotificationEmailIds** paraméterhez. 
1. Engedélyezze a [SendGrid](../../../sendgrid-dotnet-how-to-send-email.md) az e-mailek küldését az Azure-beli virtuális gépről.
1. A Database Mail konfigurálásához használja az SMTP-kiszolgálót és a felhasználónevet. SQL Server Management Studio vagy Transact-SQL-parancsokkal is konfigurálhatja a Database Mail. További információ: [Database mail](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail).
1. [Konfigurálja SQL Server Agent Database mail használatára](https://docs.microsoft.com/sql/relational-databases/database-mail/configure-sql-server-agent-mail-to-use-database-mail).
1. Győződjön meg arról, hogy az SMTP-port engedélyezve van a helyi virtuálisgép-tűzfalon és a virtuális gép hálózati biztonsági csoportján keresztül.

## <a name="next-steps"></a>További lépések

Az automatikus biztonsági mentés az Azure-beli virtuális gépeken felügyelt biztonsági mentést konfigurál. Ezért fontos, hogy áttekintse [a felügyelt biztonsági mentés dokumentációját SQL Server 2014](https://msdn.microsoft.com/library/dn449497(v=sql.120).aspx)-es számítógépen.

A következő cikkben további biztonsági mentési és visszaállítási útmutatást talál az Azure-beli virtuális gépek SQL Serverához: [SQL Server biztonsági mentése és visszaállítása az Azure Virtual Machinesban](virtual-machines-windows-sql-backup-recovery.md).

További információ az egyéb rendelkezésre álló automatizálási feladatokról: [SQL Server IaaS-ügynök bővítmény](virtual-machines-windows-sql-server-agent-extension.md).

A SQL Server Azure-beli virtuális gépeken való futtatásával kapcsolatos további információkért lásd: [SQL Server az azure Virtual Machines áttekintése](virtual-machines-windows-sql-server-iaas-overview.md).
