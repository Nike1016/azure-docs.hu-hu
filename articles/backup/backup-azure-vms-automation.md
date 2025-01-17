---
title: Azure-beli virtuális gépek biztonsági mentése és helyreállítása a PowerShell-lel Azure Backup használatával
description: Az Azure-beli virtuális gépek biztonsági mentését és helyreállítását ismerteti a PowerShell-lel Azure Backup használatával
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: dacurwin
ms.openlocfilehash: 23492133035f27aa3e1217269022565e0ff217a9
ms.sourcegitcommit: b12a25fc93559820cd9c925f9d0766d6a8963703
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/14/2019
ms.locfileid: "69018757"
---
# <a name="back-up-and-restore-azure-vms-with-powershell"></a>Azure-beli virtuális gépek biztonsági mentése és visszaállítása a PowerShell-lel

Ez a cikk az Azure-beli virtuális gépek biztonsági mentését és visszaállítását ismerteti egy [Azure Backup](backup-overview.md) Recovery Services-tárolóban PowerShell-parancsmagok használatával.

Ebben a cikkben az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzon létre egy Recovery Services-tárolót, és állítsa be a tár környezetét.
> * Biztonsági mentési szabályzat meghatározása
> * A biztonsági mentési szabályzat alkalmazása több virtuális gép védelme érdekében
> * Egy igény szerinti biztonsági mentési feladatot indít el a védett virtuális gépek számára, mielőtt biztonsági másolatot készít (vagy véd) egy virtuális gépet, el kell végeznie az előfeltételeket, hogy előkészítse a környezetet a virtuális gépek védelmére. [](backup-azure-arm-vms-prepare.md)

## <a name="before-you-start"></a>Előkészületek

- [További](backup-azure-recovery-services-vault-overview.md) információ a Recovery Services-tárolókkal kapcsolatban.
- [Tekintse át](backup-architecture.md#architecture-direct-backup-of-azure-vms) az Azure-beli virtuális gépek biztonsági mentésének architektúráját, [Ismerkedjen meg](backup-azure-vms-introduction.md) a biztonsági mentési folyamattal, és [tekintse át](backup-support-matrix-iaas.md) a támogatás, a korlátozások és az Előfeltételek
- Tekintse át Recovery Services PowerShell-objektumának hierarchiáját.

## <a name="recovery-services-object-hierarchy"></a>Recovery Services objektum-hierarchia

Az objektum-hierarchiát a következő ábra összegzi.

![Recovery Services objektum-hierarchia](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Tekintse át az az **. recoveryservices szolgáltatónál** [parancsmag](https://docs.microsoft.com/powershell/module/Az.RecoveryServices/?view=azps-1.4.0) -referenciát az Azure Library-ben.

## <a name="set-up-and-register"></a>Beállítás és regisztrálás

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Kezdés:

1. [A PowerShell legújabb verziójának letöltése](https://docs.microsoft.com/powershell/azure/install-az-ps)

2. Keresse meg a rendelkezésre álló Azure Backup PowerShell-parancsmagokat a következő parancs beírásával:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

    Megjelenik a Azure Backup, Azure Site Recovery és a Recovery Services tároló aliasai és parancsmagjai. Az alábbi ábrán egy példa látható, amit látni fog. Nem a parancsmagok teljes listája.

    ![Recovery Services listája](./media/backup-azure-vms-automation/list-of-recoveryservices-ps.png)

3. Jelentkezzen be az Azure-fiókjába a **AzAccount**használatával. Ez a parancsmag egy weblapot hoz létre a fiók hitelesítő adatainak megadásához:

    * Másik lehetőségként a fiók hitelesítő adatait a **kapcsolat-AzAccount** parancsmag paraméterként is hozzáadhatja a **-hitelesítőadat** paraméter használatával.
    * Ha a CSP-partner bérlő nevében dolgozik, adja meg az ügyfelet bérlőként a tenantID vagy a bérlő elsődleges tartománynevének használatával. Példa: **Kapcsolat – AzAccount – bérlő "fabrikam.com"**

4. Társítsa a fiókhoz használni kívánt előfizetést, mivel egy fiók több előfizetéssel is rendelkezhet:

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

5. Ha első alkalommal használja a Azure Backup-t, a **[Register-AzResourceProvider](https://docs.microsoft.com/powershell/module/az.resources/register-azresourceprovider)** parancsmaggal regisztrálnia kell az Azure Recovery Service providert az előfizetésében.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. A következő parancsokkal ellenőrizheti, hogy a szolgáltatók sikeresen regisztráltak-e:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

    A parancs kimenetében a **RegistrationState** a **regisztrált**értékre kell váltania. Ha nem, egyszerűen futtassa újra a **[Register-AzResourceProvider](https://docs.microsoft.com/powershell/module/az.resources/register-azresourceprovider)** parancsmagot.


## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

A következő lépések végigvezetik a Recovery Services-tároló létrehozásának lépésein. Egy Recovery Services-tár nem azonos a Backup-tárolóval.

1. A Recovery Services-tároló Resource Manager-erőforrás, ezért egy erőforráscsoporthoz kell helyeznie. Használhat meglévő erőforráscsoportot, vagy létrehozhat egy erőforráscsoportot a **[New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup)** parancsmaggal. Erőforráscsoport létrehozásakor adja meg az erőforráscsoport nevét és helyét.  

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```
2. A [New-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/new-azrecoveryservicesvault?view=azps-1.4.0) parancsmag használatával hozza létre a Recovery Services-tárolót. Ügyeljen arra, hogy ugyanazt a helyet határozza meg a tárolóhoz, mint amelyet az erőforráscsoport használ.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```
3. Adja meg a használandó tárolási redundancia típusát; használhatja a [helyileg redundáns tárolást (LRS)](../storage/common/storage-redundancy-lrs.md) vagy a [geo redundáns tárolást (GRS)](../storage/common/storage-redundancy-grs.md). Az alábbi példa a-BackupStorageRedundancy beállítást mutatja be a testvault beállításnál a GeoRedundant értékre.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperty  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

   > [!TIP]
   > Számos Azure Backup-parancsmaghoz szükséges bemenetként a helyreállítási tár objektum. Ebből az okból célszerű egy változóban tárolni a helyreállítási tár objektumot.
   >
   >

## <a name="view-the-vaults-in-a-subscription"></a>Az előfizetésben található tárolók megtekintése

Az előfizetés összes tárolójának megtekintéséhez használja a [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0):

```powershell
Get-AzRecoveryServicesVault
```

A kimenet a következő példához hasonló, figyelje meg, hogy a társított ResourceGroupName és hely van megadva.

```
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```


## <a name="back-up-azure-vms"></a>Azure-beli virtuális gépek biztonsági mentése

Használjon Recovery Services tárolót a virtuális gépek biztonságának biztosítása érdekében. A védelem alkalmazása előtt állítsa be a tár környezetét (a tárolóban védett adattípust), és ellenőrizze a védelmi házirendet. A védelmi házirend a biztonsági mentési feladatok futtatása és az egyes biztonsági mentési Pillanatképek megtartásának időpontja.

### <a name="set-vault-context"></a>Tár környezetének beállítása

A virtuális gépek védelmének engedélyezése előtt a [set-AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0) paranccsal állítsa be a tár környezetét. A tárolási környezet beállítását követően az minden további parancsmagra érvényes lesz. Az alábbi példa a tároló környezetét állítja be a *testvault*.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="modifying-storage-replication-settings"></a>Tárolási replikálási beállítások módosítása

A [set-AzRecoveryServicesBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/Set-AzRecoveryServicesBackupProperty) parancs használata a tároló tárolási replikációs konfigurációjának beállításához a LRS/GRS

```powershell
$vault= Get-AzRecoveryServicesVault -name "testvault"
Set-AzRecoveryServicesBackupProperty -Vault $vault -BackupStorageRedundancy GeoRedundant/LocallyRedundant
```

> [!NOTE]
> A tárterület-redundancia csak akkor módosítható, ha nincsenek védett biztonsági mentési elemek a tárolóban.

### <a name="create-a-protection-policy"></a>Védelmi szabályzat létrehozása

Helyreállítási tár létrehozásakor a tár alapértelmezett védelmi és megőrzési szabályzatokkal rendelkezik. Az alapértelmezett védelmi szabályzat naponta egyszer, adott időben aktivál egy biztonsági mentési feladatot. Az alapértelmezett megőrzési szabályzat 30 napig őrzi meg a napi helyreállítási pontokat. Az alapértelmezett szabályzat használatával gyorsan védetté teheti a virtuális gépet, és szerkesztheti a szabályzatot később, különböző részletekkel.

A **[Get-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy)** használatával megtekintheti a tárolóban elérhető védelmi szabályzatokat. Ezzel a parancsmaggal kérhet le egy adott szabályzatot, vagy megtekintheti a munkaterhelés-típushoz társított házirendeket. Az alábbi példa lekéri a számítási feladatok típusát, a AzureVM.

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM"
```

A kimenet a következő példához hasonló:

```
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
DefaultPolicy        AzureVM            AzureVM              4/14/2016 5:00:00 PM
```

> [!NOTE]
> A PowerShell BackupTime mezőjének időzónája UTC. Ha azonban a biztonsági mentés ideje megjelenik a Azure Portalban, az idő a helyi időzónára van igazítva.
>
>

A biztonsági mentési védelmi szabályzat legalább egy adatmegőrzési szabályzattal van társítva. A megőrzési házirend határozza meg, hogy a rendszer mennyi ideig tart a helyreállítási pontok törlése előtt.

- A [Get-AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject) használatával megtekintheti az alapértelmezett adatmegőrzési szabályt.
- Hasonlóképpen használhatja a [Get-AzRecoveryServicesBackupSchedulePolicyObject-](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject) t az alapértelmezett ütemezett házirend beszerzéséhez.
- A [New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy) parancsmag egy PowerShell-objektumot hoz létre, amely a biztonsági mentési szabályzat adatait tárolja.
- Az ütemezett és adatmegőrzési házirend objektumai bemenetként használatosak a New-AzRecoveryServicesBackupProtectionPolicy parancsmaghoz.

Alapértelmezés szerint a rendszer a kezdési időt határozza meg az ütemezett házirend objektumban. A következő példa használatával módosíthatja a kezdési időt a kívánt kezdési időpontra. A kívánt kezdési időpontnak UTC-ként is kell lennie. Az alábbi példa feltételezi, hogy a várt kezdési idő a napi biztonsági másolatok 01:00-as UTC-értéke.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
$UtcTime = Get-Date -Date "2019-03-20 01:00:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> A kezdési időt csak 30 percenként kell megadnia. A fenti példában csak "01:00:00" vagy "02:30:00" lehet. A kezdési időpont nem lehet "01:15:00"

A következő példa az ütemezett házirendet és a változókban tárolt adatmegőrzési szabályzatot tárolja. A példa ezeket a változókat használja a *NewPolicy*védelmi szabályzat létrehozásakor használt paraméterek definiálásához.

```powershell
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -WorkloadType "AzureVM" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

A kimenet a következő példához hasonló:

```
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewPolicy           AzureVM            AzureVM              4/24/2016 1:30:00 AM
```

### <a name="enable-protection"></a>Védelem engedélyezése

A védelmi házirend meghatározása után továbbra is engedélyeznie kell egy elem házirendjét. A védelem engedélyezéséhez használja az [enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) . A védelem engedélyezéséhez két objektum szükséges – az elem és a házirend. Miután a házirend társítva lett a tárolóhoz, a biztonsági mentési munkafolyamat a házirend-ütemtervben meghatározott időpontban aktiválódik.

> [!IMPORTANT]
> A PS használatával egyszerre több virtuális gép biztonsági mentését is lehetővé teheti, hogy egyetlen házirendhez ne legyen több, mint 100 virtuális gép társítva. Ez az [ajánlott eljárás](https://docs.microsoft.com/azure/backup/backup-azure-vm-backup-faq#is-there-a-limit-on-number-of-vms-that-can-beassociated-with-a-same-backup-policy). Jelenleg a PS-ügyfél nem blokkolja explicit módon, ha több mint 100 virtuális gép van, de az ellenőrzési terv a jövőben is felvehető.

Az alábbi példák lehetővé teszik a V2VM, az NewPolicy-t használó elem védelmét. A példák attól függően különböznek, hogy a virtuális gép titkosítva van-e, és milyen típusú titkosítást tartalmaz.

A védelem engedélyezése a **nem titkosított Resource Manager-alapú virtuális gépeken**:

```powershell
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

Ha engedélyezni szeretné a védelem használatát a titkosított virtuális gépeken (a BEK és a KEK használatával titkosítva), meg kell adnia a Azure Backup szolgáltatás számára a kulcsok és titkos kulcsok olvasását a kulcstartóból.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToKeys backup,get,list -PermissionsToSecrets get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

Ha engedélyezni szeretné a védelmet a **titkosított virtuális gépeken (csak a csak BEK használatával titkosítva)** , meg kell adnia a Azure Backup szolgáltatás számára a titkos kulcsok olvasására vonatkozó engedélyt.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToSecrets backup,get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

> [!NOTE]
> Ha a Azure Government-felhőt használja, használja a [set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) parancsmag ServicePrincipalName paraméterének ff281ffe-705c-4f53-9f37-a40e6f2c68f3 értékét.
>

## <a name="monitoring-a-backup-job"></a>Biztonsági mentési feladatok figyelése

A hosszú ideig futó műveleteket, például a biztonsági mentési feladatokat a Azure Portal használata nélkül is figyelheti. A folyamatban lévő feladatok állapotának lekéréséhez használja a [Get-AzRecoveryservicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) parancsmagot. Ez a parancsmag egy adott tár biztonsági mentési feladatait olvassa be, és azt a tároló környezetében adja meg. A következő példa lekérdezi a folyamatban lévő feladatok állapotát tömbként, és a $joblist változóban tárolja az állapotot.

```powershell
$joblist = Get-AzRecoveryservicesBackupJob –Status "InProgress"
$joblist[0]
```

A kimenet a következő példához hasonló:

```
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM             Backup               InProgress            4/23/2016                5:00:30 PM                cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

A feladatok befejezésének lekérdezése helyett – ami szükségtelen további kódokat használ – a [WAIT-AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) parancsmagot használja. Ez a parancsmag addig szünetelteti a végrehajtást, amíg a feladat be nem fejeződik, vagy elérte a megadott időtúllépési értéket.

```powershell
Wait-AzRecoveryServicesBackupJob -Job $joblist[0] -Timeout 43200
```

## <a name="manage-azure-vm-backups"></a>Azure-beli virtuális gépek biztonsági mentésének kezelése

### <a name="modify-a-protection-policy"></a>Védelmi szabályzat módosítása

A védelmi szabályzat módosításához a [set-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy) használatával módosítsa a SchedulePolicy vagy a RetentionPolicy objektumokat.

#### <a name="modifying-scheduled-time"></a>Ütemezett idő módosítása

Védelmi szabályzat létrehozásakor alapértelmezés szerint a rendszer a kezdő időpontot rendeli hozzá. Az alábbi példák bemutatják, hogyan módosíthatja a védelmi szabályzatok kezdési idejét.

````powershell
$SchPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
$UtcTime = Get-Date -Date "2019-03-20 01:00:00Z" (This is the time that the customer wants to start the backup)
$UtcTime = $UtcTime.ToUniversalTime()
$SchPol.ScheduleRunTimes[0] = $UtcTime
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $pol  -SchedulePolicy $SchPol
````

#### <a name="modifying-retention"></a>Megőrzés módosítása

A következő példa a helyreállítási pont megőrzését 365 napra módosítja.

```powershell
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
$retPol.DailySchedule.DurationCountInDays = 365
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $pol  -RetentionPolicy $RetPol
```

#### <a name="configuring-instant-restore-snapshot-retention"></a>Az azonnali visszaállítás pillanatkép-megőrzésének konfigurálása

> [!NOTE]
> Az az PS Version 1.6.0-től kezdve az egyik frissítéssel frissítheti az azonnali visszaállítás pillanatképének megőrzési időszakát a szabályzatban a PowerShell használatával

````powershell
PS C:\> $bkpPol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM"
$bkpPol.SnapshotRetentionInDays=7
PS C:\> Set-AzureRmRecoveryServicesBackupProtectionPolicy -policy $bkpPol
````

Az alapértelmezett érték 2, a felhasználó beállíthatja az értéket 1 és legfeljebb 5 közötti értékre. A heti biztonsági mentési házirendek esetében az időszak értéke 5, és nem módosítható.

### <a name="trigger-a-backup"></a>Biztonsági mentés indítása

Biztonsági mentési feladatok elindításához használja a [Backup-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) . Ha ez a kezdeti biztonsági mentés, a teljes biztonsági mentés. A későbbi biztonsági másolatok növekményes másolatot készítenek. A következő példa a virtuális gép biztonsági mentését 60 napig őrzi meg.

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
$item = Get-AzRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM"
$endDate = (Get-Date).AddDays(60).ToUniversalTime()
$job = Backup-AzRecoveryServicesBackupItem -Item $item -VaultId $targetVault.ID -ExpiryDateTimeUTC $endDate
```

A kimenet a következő példához hasonló:

```
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM              Backup              InProgress          4/23/2016                  5:00:30 PM                cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

> [!NOTE]
> A PowerShell kezdési és befejezési mezőinek időzónája UTC. Ha azonban az idő megjelenik a Azure Portalban, az idő a helyi időzónára van igazítva.
>
>

### <a name="change-policy-for-backup-items"></a>Biztonsági mentési elemek szabályzatának módosítása

A felhasználó módosíthatja a meglévő szabályzatot, vagy megváltoztathatja a biztonsági másolatban szereplő elem házirendjét a Házirend1 és a Policy2 között. Ha egy biztonsági másolati elemhez szeretne házirendeket váltani, olvassa be a vonatkozó házirendet, és készítsen biztonsági másolatot az elemről, és használja az [enable-AzRecoveryServices](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) parancsot a biztonsági mentési elemmel paraméterként.

````powershell
$TargetPol1 = Get-AzRecoveryServicesBackupProtectionPolicy -Name <PolicyName>
$anotherBkpItem = Get-AzRecoveryServicesBackupItem -WorkloadType AzureVM -BackupManagementType AzureVM -Name "<BackupItemName>"
Enable-AzRecoveryServicesBackupProtection -Item $anotherBkpItem -Policy $TargetPol1
````

A parancs megvárja, amíg a biztonsági mentés be nem fejeződik, és a következő kimenetet adja vissza.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
TestVM           ConfigureBackup      Completed            3/18/2019 8:00:21 PM      3/18/2019 8:02:16 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="stop-protection"></a>Védelem leállítása

#### <a name="retain-data"></a>Adatok megőrzése

Ha a felhasználó le szeretné állítani a védelmet, használhatja a [disable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) PS parancsmagot. Ezzel leállítja az ütemezett biztonsági mentéseket, de a biztonsági mentés egészen addig, amíg a rendszer örökre megőrzi az adatokat.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -Name "<backup item name>" -VaultId $targetVault.ID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID
````

#### <a name="delete-backup-data"></a>Biztonsági mentési adatok törlése

Ahhoz, hogy teljesen el lehessen távolítani a tárolt biztonsági mentési fájlokat a tárolóban, egyszerűen adja hozzá a "-RemoveRecoveryPoints" jelzőt, vagy váltson a ["letiltás" védelmi parancsra](#retain-data).

````powershell
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID -RemoveRecoveryPoints
````

## <a name="restore-an-azure-vm"></a>Azure-beli virtuális gép visszaállítása

A virtuális gépek a PowerShell használatával történő visszaállításával és a virtuális gép helyreállításával kapcsolatban a Azure Portal és a helyreállítás közötti különbség fontos. A PowerShell-lel a visszaállítási művelet akkor fejeződik be, ha a helyreállítási pontról a lemezek és a konfigurációs adatok jönnek létre. A visszaállítási művelet nem hozza létre a virtuális gépet. A virtuális gép lemezről történő létrehozásával kapcsolatban tekintse meg a virtuális gép [létrehozása visszaállított lemezekről](backup-azure-vms-automation.md#create-a-vm-from-restored-disks)című szakaszt. Ha nem szeretné visszaállítani a teljes virtuális gépet, de szeretné visszaállítani vagy helyreállítani néhány fájlt egy Azure-beli virtuális gép biztonsági másolatából, tekintse meg a [Fájl-helyreállítási szakaszt](backup-azure-vms-automation.md#restore-files-from-an-azure-vm-backup).

> [!Tip]
> A visszaállítási művelet nem hozza létre a virtuális gépet.
>
>

Az alábbi ábrán a RecoveryServicesVault található objektum-hierarchia látható a BackupRecoveryPoint.

![BackupContainer mutató Recovery Services objektum-hierarchia](./media/backup-azure-vms-arm-automation/backuprecoverypoint-only.png)

A biztonsági másolatok állapotának visszaállításához azonosítsa a biztonsági másolatban szereplő elemeket, valamint az időponthoz tartozó adatelemet tartalmazó helyreállítási pontot. A [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) használatával állítsa vissza az adatait a tárból a fiókjába.

Az Azure-beli virtuális gépek visszaállításának alapvető lépései a következők:

* Válassza ki a virtuális gépet.
* Válasszon egy helyreállítási pontot.
* Állítsa vissza a lemezeket.
* Hozza létre a virtuális gépet a tárolt lemezekről.

### <a name="select-the-vm"></a>Válassza ki a virtuális gépet

A megfelelő biztonsági mentési elemet azonosító PowerShell-objektum beszerzéséhez indítsa el a tárolóban található tárolóból, és az objektum-hierarchiában dolgozzon végig. A virtuális gépet jelölő tároló kiválasztásához használja a [Get-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer) parancsmagot és a pipe-ot a [Get-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) parancsmaghoz.

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM"
```

### <a name="choose-a-recovery-point"></a>Válasszon helyreállítási pontot

A [Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) parancsmag használatával listázhatja a biztonsági mentési elemek összes helyreállítási pontját. Ezután válassza ki a visszaállítani kívánt helyreállítási pontot. Ha nem tudja biztosan, hogy melyik helyreállítási pontot szeretné használni, érdemes kiválasztani a legutóbbi RecoveryPointType = AppConsistent pontot a listában.

A következő szkriptben a **$RP**változó a kiválasztott biztonsági mentési elemhez tartozó helyreállítási pontok tömbje az elmúlt hét napban. A tömb a 0. indexben szereplő legutóbbi helyreállítási ponttal fordított sorrendben van rendezve. A helyreállítási pont kiválasztásához használja a PowerShell-tömb szabványos indexelését. A példában a $rp [0] kiválasztja a legutóbbi helyreállítási pontot.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
$rp[0]
```

A kimenet a következő példához hasonló:

```powershell
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM/recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```

### <a name="restore-the-disks"></a>A lemezek visszaállítása

A [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) parancsmag használatával állítsa vissza a biztonsági másolati elemek beállításait és konfigurációját egy helyreállítási pontra. Miután azonosította a helyreállítási pontot, használja a **-RecoveryPoint** paraméter értékeként. A fenti mintában **$RP [0]** volt a használni kívánt helyreállítási pont. A következő mintakód esetében **$RP [0]** a lemez visszaállításához használandó helyreállítási pont.

A lemezek és a konfigurációs adatok visszaállítása:

```powershell
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG"
$restorejob
```

#### <a name="restore-managed-disks"></a>Felügyelt lemezek visszaállítása

> [!NOTE]
> Ha a háttérbe állított virtuális gép felügyelt lemezekkel rendelkezik, és felügyelt lemezként szeretné visszaállítani őket, akkor a Azure PowerShell RM modul v 6.7.0 képességét vezették be. től
>
>

Adjon meg egy további paramétert, amely meghatározza, hogy a rendszer mely **TargetResourceGroupName** adja vissza a felügyelt lemezeket.

> [!NOTE]
> Erősen ajánlott a **TargetResourceGroupName** paraméter használata a felügyelt lemezek visszaállítására, mivel jelentős teljesítménybeli javítást eredményez. Emellett az Azure PowerShell az a modul 1,0-től kezdődően ez a paraméter kötelező a felügyelt lemezekkel való visszaállítás esetén.
>
>


```powershell
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks"
```

A rendszer visszaállítja a **VMConfig. JSON** fájlt a Storage-fiókba, és a felügyelt lemezeket a rendszer visszaállítja a megadott cél RG-ra.

A kimenet a következő példához hasonló:

```powershell
WorkloadName     Operation          Status               StartTime                 EndTime            JobID
------------     ---------          ------               ---------                 -------          ----------
V2VM              Restore           InProgress           4/23/2016 5:00:30 PM                        cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

A [WAIT-AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) parancsmag használatával várjon, amíg a visszaállítási feladatok befejeződik.

```powershell
Wait-AzRecoveryServicesBackupJob -Job $restorejob -Timeout 43200
```

A visszaállítási feladatok befejezését követően a [Get-AzRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) parancsmaggal kérheti le a visszaállítási művelet részleteit. A JobDetails tulajdonság a virtuális gép újraépítéséhez szükséges adatokat tartalmaz.

```powershell
$restorejob = Get-AzRecoveryServicesBackupJob -Job $restorejob
$details = Get-AzRecoveryServicesBackupJobDetails -Job $restorejob
```

A lemezek visszaállítása után lépjen a következő szakaszra a virtuális gép létrehozásához.

## <a name="replace-disks-in-azure-vm"></a>Lemezek cseréje az Azure-beli virtuális gépen

A lemezek és a konfigurációs adatok cseréjéhez hajtsa végre az alábbi lépéseket:

- 1\. lépés: [A lemezek visszaállítása](backup-azure-vms-automation.md#restore-the-disks)
- 2\. lépés: [Adatlemez leválasztása a PowerShell használatával](https://docs.microsoft.com/azure/virtual-machines/windows/detach-disk#detach-a-data-disk-using-powershell)
- 3\. lépés: [Adatlemez csatolása Windows rendszerű virtuális géphez a PowerShell-lel](https://docs.microsoft.com/azure/virtual-machines/windows/attach-disk-ps)


## <a name="create-a-vm-from-restored-disks"></a>Virtuális gép létrehozása visszaállított lemezekről

A lemezek visszaállítása után a következő lépésekkel hozza létre és konfigurálja a virtuális gépet a lemezről.

> [!NOTE]
> Ahhoz, hogy titkosított virtuális gépeket hozzon létre a visszaállított lemezekről, az Azure-szerepkörnek engedéllyel kell rendelkeznie a művelet elvégzéséhez, Microsoft. kulcstartó/tárolók/ **üzembe helyezés/művelet**. Ha a szerepkör nem rendelkezik ezzel az engedéllyel, hozzon létre egy egyéni szerepkört ehhez a művelethez. További információ: [Egyéni szerepkörök az Azure RBAC-ben](../role-based-access-control/custom-roles.md).
>
>

> [!NOTE]
> A lemezek visszaállítása után már beszerezheti a központi telepítési sablont, amelyet közvetlenül használhat új virtuális gép létrehozásához. Nincs több különböző PS-parancsmag olyan felügyelt/nem felügyelt virtuális gépek létrehozásához, amelyek titkosított/nem titkosítottak.

Az eredő feladatok részletei megadják a sablon URI-JÁT, amely lekérdezhető és telepíthető.

```powershell
   $properties = $details.properties
   $templateBlobURI = $properties["Template Blob Uri"]
```

Csak a sablon üzembe helyezésével hozzon létre egy új virtuális gépet az [itt](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)leírtak szerint.

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment ResourceGroupName ExampleResourceGroup -TemplateUri $templateBlobURI -storageAccountType Standard_GRS
```

A következő szakasz azokat a lépéseket sorolja fel, amelyek szükségesek a virtuális gépek "VMConfig" fájl használatával történő létrehozásához.

> [!NOTE]
> Javasoljuk, hogy a fent ismertetett központi telepítési sablon használatával hozzon létre egy virtuális gépet. Ez a szakasz (1-6-es pont) hamarosan elavulttá válik.

1. A visszaállított lemez tulajdonságainak lekérdezése a feladatok részleteihez.

   ```powershell
   $properties = $details.properties
   $storageAccountName = $properties["Target Storage Account Name"]
   $containerName = $properties["Config Blob Container Name"]
   $configBlobName = $properties["Config Blob Name"]
   ```

2. Állítsa be az Azure Storage-környezetet, és állítsa vissza a JSON konfigurációs fájlt.

   ```powershell
   Set-AzCurrentStorageAccount -Name $storageaccountname -ResourceGroupName "testvault"
   $destination_path = "C:\vmconfig.json"
   Get-AzStorageBlobContent -Container $containerName -Blob $configBlobName -Destination $destination_path
   $obj = ((Get-Content -Path $destination_path -Raw -Encoding Unicode)).TrimEnd([char]0x00) | ConvertFrom-Json
   ```

3. A virtuális gép konfigurációjának létrehozásához használja a JSON konfigurációs fájlt.

   ```powershell
   $vm = New-AzVMConfig -VMSize $obj.'properties.hardwareProfile'.vmSize -VMName "testrestore"
   ```

4. Csatlakoztassa az operációsrendszer-lemezt és az adatlemezeket. Ez a lépés példákat tartalmaz a különböző felügyelt és titkosított virtuálisgép-konfigurációkra. Használja a virtuális gép konfigurációjának megfelelő példát.

   * **Nem felügyelt és nem titkosított virtuális gépek** – a következő mintát használja a nem felügyelt, nem titkosított virtuális gépekhez.

       ```powershell
       Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
       $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
       foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
       {
        $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
       }
       ```

   * **Nem felügyelt és titkosított virtuális gépek az Azure ad-vel (csak BEK esetén)** – nem felügyelt, titkosított virtuális gépekhez az Azure ad-vel (csak a BEK használatával), a lemezek csatlakoztatása előtt vissza kell állítania a titkos kulcsot a kulcstartóhoz. További tudnivalókért tekintse meg a [titkosított virtuális gép visszaállítása Azure Backup helyreállítási pontról](backup-azure-restore-key-secret.md)című témakört. Az alábbi minta azt mutatja be, hogyan lehet az operációs rendszer és az adatlemezek csatlakoztatását a titkosított virtuális gépekhez. Az operációsrendszer-lemez beállításakor ügyeljen rá, hogy megemlíti a megfelelő operációsrendszer-típust.

      ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $dekUrl = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows/Linux
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
      foreach($dd in $obj.'properties.storageProfile'.dataDisks)
      {
       $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
      }
      ```

   * **Nem felügyelt és titkosított virtuális gépek az Azure ad-vel (BEK és KEK)** – nem felügyelt, titkosított virtuális gépekhez az Azure ad-vel (a BEK és KEK használatával titkosított), a lemezek csatlakoztatása előtt állítsa vissza a kulcsot és a titkos kulcsot a kulcstartóhoz. További információ: [titkosított virtuális gép visszaállítása Azure Backup helyreállítási pontról](backup-azure-restore-key-secret.md). Az alábbi minta azt mutatja be, hogyan lehet az operációs rendszer és az adatlemezek csatlakoztatását a titkosított virtuális gépekhez.

      ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $kekUrl = "https://ContosoKeyVault.vault.azure.net:443/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
      foreach($dd in $obj.'properties.storageProfile'.dataDisks)
     {
     $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
     }
      ```

   * **Nem felügyelt és titkosított virtuális gépek az Azure ad nélkül (csak BEK esetén)** – nem felügyelt, Azure ad nélküli titkosított virtuális gépek esetében (csak a BEK használatával), ha a forrás kulcstartója **/titkos kulcsa nem érhető el** a Key Vault számára a következő eljárás használatával: [ Nem titkosított virtuális gép visszaállítása Azure Backup helyreállítási pontról](backup-azure-restore-key-secret.md). Ezután hajtsa végre a következő parancsfájlokat a visszaállított operációsrendszer-blob titkosítási adatainak beállításához (ez a lépés nem szükséges az adatblobhoz). A $dekurl a visszaállított kulcstartóból hívható le.<br>

   Az alábbi szkriptet csak akkor kell végrehajtani, ha a forrás kulcstartó/titkos kulcs nem érhető el.

      ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      $encSetting = "{""encryptionEnabled"":true,""encryptionSettings"":[{""diskEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""secretUrl"":""$dekUrl""}}]}"
      $osBlobName = $obj.'properties.StorageProfile'.osDisk.name + ".vhd"
      $osBlob = Get-AzStorageBlob -Container $containerName -Blob $osBlobName
      $osBlob.ICloudBlob.Metadata["DiskEncryptionSettings"] = $encSetting
      $osBlob.ICloudBlob.SetMetadata()
      ```

    Ha a **titkok elérhetők** , és a titkosítási adatok is be vannak állítva az operációs rendszer blobján, csatolja a lemezeket az alábbi parancsfájl használatával.<br>

    Ha a forrás-és a titkos kulcs már elérhető, akkor a fenti szkriptet nem kell végrehajtani.

      ```powershell
      Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
      foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
      {
      $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
      }
      ```

   * **Nem felügyelt és titkosított virtuális gépek Azure ad nélkül (BEK és KEK)** – nem felügyelt, titkosított virtuális gépekhez az Azure ad nélkül (a BEK & KEK használatával titkosított) – Ha a forrás kulcstartó **/kulcs/titkos kód nem érhető el** , állítsa vissza a Key Vault kulcsát és titkait a következővel: a [nem titkosított virtuális gépek Azure Backup helyreállítási pontról](backup-azure-restore-key-secret.md)történő visszaállításának eljárása. Ezután hajtsa végre a következő parancsfájlokat a visszaállított operációsrendszer-blob titkosítási adatainak beállításához (ez a lépés nem szükséges az adatblobhoz). A $dekurl és $kekurl a visszaállított kulcstartóból hívható le.

   Az alábbi szkriptet csak akkor kell végrehajtani, ha a forrás kulcstartó/kulcs/titok nem érhető el.

    ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $kekUrl = "https://ContosoKeyVault.vault.azure.net/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      $encSetting = "{""encryptionEnabled"":true,""encryptionSettings"":[{""diskEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""secretUrl"":""$dekUrl""},""keyEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""keyUrl"":""$kekUrl""}}]}"
      $osBlobName = $obj.'properties.StorageProfile'.osDisk.name + ".vhd"
      $osBlob = Get-AzStorageBlob -Container $containerName -Blob $osBlobName
      $osBlob.ICloudBlob.Metadata["DiskEncryptionSettings"] = $encSetting
      $osBlob.ICloudBlob.SetMetadata()
      ```
   Ha a **kulcs/titkos** kulcsok elérhetők, és a titkosítási adatok az operációs rendszer blobján vannak beállítva, csatolja a lemezeket az alább megadott parancsfájllal.

    Ha a forrás kulcstartó/kulcs/titkos kódok elérhetők, akkor a fenti szkriptet nem kell végrehajtani.

    ```powershell
      Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
      foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
      {
      $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
      }
      ```

   * **Felügyelt és nem titkosított virtuális gépek** – felügyelt, nem titkosított virtuális gépekhez csatolja a visszaállított felügyelt lemezeket. Részletes információk: adatlemez csatolása [Windows rendszerű virtuális géphez a PowerShell használatával](../virtual-machines/windows/attach-disk-ps.md).

   * **Felügyelt és titkosított virtuális gépek az Azure ad-vel (csak BEK)** – felügyelt titkosított virtuális gépekhez az Azure ad-vel (csak a BEK használatával), csatolja a visszaállított felügyelt lemezeket. Részletes információk: adatlemez csatolása [Windows rendszerű virtuális géphez a PowerShell használatával](../virtual-machines/windows/attach-disk-ps.md).

   * **Felügyelt és titkosított virtuális gépek az Azure ad-vel (BEK és KEK)** – felügyelt titkosított virtuális gépekhez az Azure ad-vel (a BEK és a KEK használatával titkosítva) csatlakoztassa a visszaállított felügyelt lemezeket. Részletes információk: adatlemez csatolása [Windows rendszerű virtuális géphez a PowerShell használatával](../virtual-machines/windows/attach-disk-ps.md).

   * **Felügyelt és titkosított virtuális gépek az Azure ad nélkül (csak BEK)** – felügyelt, titkosított virtuális gépek Azure ad nélkül (csak BEK-vel titkosítva), ha a forrás kulcstartója **/titkos kulcsa nem érhető el** a Key vaulthoz való visszaállítási eljárás használatával. [ nem titkosított virtuális gép Azure Backup helyreállítási pontból](backup-azure-restore-key-secret.md). Ezután hajtsa végre a következő parancsfájlokat a visszaállított operációsrendszer-lemez titkosítási adatainak beállításához (ez a lépés nem szükséges az adatlemezhez). A $dekurl a visszaállított kulcstartóból hívható le.

     Az alábbi szkriptet csak akkor kell végrehajtani, ha a forrás kulcstartó/titkos kulcs nem érhető el.  

     ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      $diskupdateconfig = New-AzDiskUpdateConfig -EncryptionSettingsEnabled $true
      $diskupdateconfig = Set-AzDiskUpdateDiskEncryptionKey -DiskUpdate $diskupdateconfig -SecretUrl $dekUrl -SourceVaultId $keyVaultId  
      Update-AzDisk -ResourceGroupName "testvault" -DiskName $obj.'properties.StorageProfile'.osDisk.name -DiskUpdate $diskupdateconfig
      ```

     Ha a titkok elérhetők, és a titkosítási adatok az operációsrendszer-lemezen vannak beállítva, a visszaállított felügyelt lemezek csatlakoztatásáról lásd: [adatlemez csatolása Windows rendszerű virtuális géphez a PowerShell használatával](../virtual-machines/windows/attach-disk-ps.md).

   * **Felügyelt és titkosított virtuális gépek az Azure ad nélkül (BEK és KEK)** – felügyelt, titkosított virtuális gépekhez az Azure ad nélkül (a BEK & KEK használatával titkosított), ha a forrás kulcstartó **/kulcs/titkos kód nem érhető el** a Key vaulthoz tartozó kulcs és titkok visszaállítása a következő eljárás használatával: [Nem titkosított virtuális gép visszaállítása Azure Backup helyreállítási pontról](backup-azure-restore-key-secret.md). Ezután hajtsa végre a következő parancsfájlokat a visszaállított operációsrendszer-lemez titkosítási adatainak beállításához (ez a lépés nem szükséges az adatlemezhez). A $dekurl és $kekurl a visszaállított kulcstartóból hívható le.

   Az alábbi szkriptet csak akkor kell végrehajtani, ha a forrás kulcstartó/kulcs/titok nem érhető el.

   ```powershell
     $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
     $kekUrl = "https://ContosoKeyVault.vault.azure.net/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
     $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
     $diskupdateconfig = New-AzDiskUpdateConfig -EncryptionSettingsEnabled $true
     $diskupdateconfig = Set-AzDiskUpdateDiskEncryptionKey -DiskUpdate $diskupdateconfig -SecretUrl $dekUrl -SourceVaultId $keyVaultId  
     $diskupdateconfig = Set-AzDiskUpdateKeyEncryptionKey -DiskUpdate $diskupdateconfig -KeyUrl $kekUrl -SourceVaultId $keyVaultId  
     Update-AzDisk -ResourceGroupName "testvault" -DiskName $obj.'properties.StorageProfile'.osDisk.name -DiskUpdate $diskupdateconfig
    ```

    Ha a kulcs/titkos kulcsok elérhetők, és a titkosítási adatok az operációsrendszer-lemezen vannak beállítva, a visszaállított felügyelt lemezek csatlakoztatásáról lásd: [adatlemez csatolása Windows rendszerű virtuális géphez a PowerShell használatával](../virtual-machines/windows/attach-disk-ps.md).

5. Adja meg a hálózati beállításokat.

    ```powershell
    $nicName="p1234"
    $pip = New-AzPublicIpAddress -Name $nicName -ResourceGroupName "test" -Location "WestUS" -AllocationMethod Dynamic
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName "test" -Location "WestUS" -Name "testvNET" -AddressPrefix 10.0.0.0/16
    $virtualNetwork | Set-AzVirtualNetwork
    $vnet = Get-AzVirtualNetwork -Name "testvNET" -ResourceGroupName "test"
    $subnetindex=0
    $nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName "test" -Location "WestUS" -SubnetId $vnet.Subnets[$subnetindex].Id -PublicIpAddressId $pip.Id
    $vm=Add-AzVMNetworkInterface -VM $vm -Id $nic.Id
    ```

6. Hozza létre a virtuális gépet.

    ```powershell  
    New-AzVM -ResourceGroupName "test" -Location "WestUS" -VM $vm
    ```

7. Leküldéses ADE-bővítmény.

   * **Virtuális gép az Azure ad-vel** – a következő parancs használatával manuálisan engedélyezheti az adatlemezek titkosítását  

     **Csak BEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

     **BEK és KEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId  -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

   * Az **Azure ad** -t nem használó virtuális gépek esetén a következő paranccsal manuálisan engedélyezheti az adatlemezek titkosítását.

     Ha a parancs végrehajtása során AADClientID kér, akkor frissítenie kell a Azure PowerShell.

     **Csak BEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -SkipVmBackup -VolumeType "All"
      ```

      **BEK és KEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -SkipVmBackup -VolumeType "All"
      ```


## <a name="restore-files-from-an-azure-vm-backup"></a>Fájlok visszaállítása Azure-beli virtuális gépek biztonsági másolatából

A lemezek visszaállítása mellett az egyes fájlokat is visszaállíthatja egy Azure-beli virtuális gép biztonsági másolatából. A fájlok visszaállítása funkció lehetővé teszi a helyreállítási pont összes fájljának elérését. A fájlokat a Fájlkezelőben kezelheti a normál fájlok esetében.

A fájlok Azure-beli virtuális gépekről történő biztonsági mentésének alapszintű lépései a következők:

* Válassza ki a virtuális gépet
* Válasszon helyreállítási pontot
* A helyreállítási pont lemezének csatlakoztatása
* A szükséges fájlok másolása
* Lemez leválasztása

### <a name="select-the-vm"></a>Válassza ki a virtuális gépet

A megfelelő biztonsági mentési elemet azonosító PowerShell-objektum beszerzéséhez indítsa el a tárolóban található tárolóból, és az objektum-hierarchiában dolgozzon végig. A virtuális gépet jelölő tároló kiválasztásához használja a [Get-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer) parancsmagot és a pipe-ot a [Get-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) parancsmaghoz.

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM"
```

### <a name="choose-a-recovery-point"></a>Válasszon helyreállítási pontot

A [Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) parancsmag használatával listázhatja a biztonsági mentési elemek összes helyreállítási pontját. Ezután válassza ki a visszaállítani kívánt helyreállítási pontot. Ha nem tudja biztosan, hogy melyik helyreállítási pontot szeretné használni, érdemes kiválasztani a legutóbbi RecoveryPointType = AppConsistent pontot a listában.

A következő szkriptben a **$RP**változó a kiválasztott biztonsági mentési elemhez tartozó helyreállítási pontok tömbje az elmúlt hét napban. A tömb a 0. indexben szereplő legutóbbi helyreállítási ponttal fordított sorrendben van rendezve. A helyreállítási pont kiválasztásához használja a PowerShell-tömb szabványos indexelését. A példában a $rp [0] kiválasztja a legutóbbi helyreállítási pontot.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
$rp[0]
```

A kimenet a következő példához hasonló:

```powershell
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM/recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```

### <a name="mount-the-disks-of-recovery-point"></a>A helyreállítási pont lemezének csatlakoztatása

A [Get-AzRecoveryServicesBackupRPMountScript](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprpmountscript) parancsmaggal kérheti le a helyreállítási pont összes lemezének csatlakoztatására szolgáló parancsfájlt.

> [!NOTE]
> A lemezek iSCSI-csatlakoztatott lemezként vannak csatlakoztatva ahhoz a géphez, amelyen a parancsfájl fut. A csatlakoztatás azonnal történik, és nem számítunk fel díjat.
>
>

```powershell
Get-AzRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0]
```

A kimenet a következő példához hasonló:

```powershell
OsType  Password        Filename
------  --------        --------
Windows e3632984e51f496 V2VM_wus2_8287309959960546283_451516692429_cbd6061f7fc543c489f1974d33659fed07a6e0c2e08740.exe
```

Futtassa a parancsfájlt azon a gépen, amelyen helyre kívánja állítani a fájlokat. A szkript végrehajtásához meg kell adnia a megadott jelszót. A lemezek csatolása után a Windows fájlkezelővel keresse meg az új köteteket és fájlokat. További információ: Backup (biztonsági mentés) című cikk, [fájlok helyreállítása az Azure-beli virtuális gépek biztonsági másolatából](backup-azure-restore-files-from-vm.md).

### <a name="unmount-the-disks"></a>Lemezek leválasztása

A szükséges fájlok másolását követően a [disable-AzRecoveryServicesBackupRPMountScript](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackuprpmountscript) paranccsal válassza le a lemezeket. Győződjön meg arról, hogy leválasztja a lemezeket, így a helyreállítási pont fájljaihoz való hozzáférés el lesz távolítva.

```powershell
Disable-AzRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0]
```

## <a name="next-steps"></a>További lépések

Ha inkább a PowerShell-lel szeretné használni az Azure-erőforrásokat, tekintse meg a PowerShell-cikket, a [Windows Server biztonsági mentésének központi telepítését és kezelését](backup-client-automation.md)ismertető témakört. Ha DPM biztonsági mentéseket kezel, tekintse [meg a DPM biztonsági mentésének üzembe helyezése és kezelése](backup-dpm-automation.md)című cikket.
