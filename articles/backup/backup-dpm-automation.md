---
title: Azure Backup – a PowerShell használatával biztonsági mentést készíthet a DPM-munkaterhelésekről
description: Megtudhatja, hogyan helyezheti üzembe és kezelheti Data Protection Manager (DPM) Azure Backup a PowerShell használatával
ms.reviewer: adigan
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 1/23/2017
ms.author: dacurwin
ms.openlocfilehash: 8e17747e2f1f29243215eac85e4e5fa761e11692
ms.sourcegitcommit: d585cdda2afcf729ed943cfd170b0b361e615fae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68688937"
---
# <a name="deploy-and-manage-backup-to-azure-for-data-protection-manager-dpm-servers-using-powershell"></a>Az Azure-ba történő biztonsági mentés üzembe helyezése és kezelése DPM-kiszolgálókon a PowerShell-lel

Ez a cikk bemutatja, hogyan használható a PowerShell a Azure Backup DPM-kiszolgálón történő beállításához, valamint a biztonsági mentés és a helyreállítás kezeléséhez.

## <a name="setting-up-the-powershell-environment"></a>A PowerShell-környezet beállítása

Mielőtt a PowerShell használatával felügyeli a Data Protection Managerról az Azure-ba történő biztonsági mentést, rendelkeznie kell a megfelelő környezettel a PowerShellben. A PowerShell-munkamenet elején győződjön meg arról, hogy a következő parancsot futtatja a megfelelő modulok importálásához, és lehetővé teszi az DPM-parancsmagok helyes hivatkozását:

```powershell
& "C:\Program Files\Microsoft System Center 2012 R2\DPM\DPM\bin\DpmCliInitScript.ps1"
```

```Output
Welcome to the DPM Management Shell!

Full list of cmdlets: Get-Command
Only DPM cmdlets: Get-DPMCommand
Get general help: help
Get help for a cmdlet: help <cmdlet-name> or <cmdlet-name> -?
Get definition of a cmdlet: Get-Command <cmdlet-name> -Syntax
Sample DPM scripts: Get-DPMSampleScript
```

## <a name="setup-and-registration"></a>Telepítés és regisztrálás

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A kezdéshez [töltse le a legújabb Azure PowerShell](/powershell/azure/install-az-ps).

A PowerShell használatával a következő telepítési és regisztrációs feladatok automatizálható:

* Recovery Services-tároló létrehozása
* Az Azure Backup-ügynök telepítése
* Regisztrálás a Azure Backup szolgáltatással
* Hálózati beállítások
* Titkosítási beállítások

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

A következő lépések végigvezetik a Recovery Services-tároló létrehozásának lépésein. Egy Recovery Services-tár nem azonos a Backup-tárolóval.

1. Ha első alkalommal használja a Azure Backup-t, a **Register-AzResourceProvider** parancsmaggal regisztrálnia kell az Azure Recovery Service providert az előfizetésében.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

2. A Recovery Services tároló egy ARM-erőforrás, ezért egy erőforráscsoporthoz kell helyeznie. Használhat meglévő erőforráscsoportot, vagy létrehozhat egy újat. Új erőforráscsoport létrehozásakor adja meg az erőforráscsoport nevét és helyét.

    ```powershell
    New-AzResourceGroup –Name "test-rg" –Location "West US"
    ```

3. Új tároló létrehozásához használja a **New-AzRecoveryServicesVault** parancsmagot. Ügyeljen arra, hogy ugyanazt a helyet határozza meg a tárolóhoz, mint amelyet az erőforráscsoport használ.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "West US"
    ```

4. Adja meg a használandó tárolási redundancia típusát; használhatja a [helyileg redundáns tárolást (LRS)](../storage/common/storage-redundancy-lrs.md) vagy a [geo redundáns tárolást (GRS)](../storage/common/storage-redundancy-grs.md). Az alábbi példa a-BackupStorageRedundancy beállítást mutatja be a testVault beállításnál a GeoRedundant értékre.

   > [!TIP]
   > Számos Azure Backup-parancsmaghoz szükséges bemenetként a helyreállítási tár objektum. Ebből az okból célszerű egy változóban tárolni a helyreállítási tár objektumot.
   >
   >

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault –Name "testVault"
    Set-AzRecoveryServicesBackupProperties  -vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>Az előfizetésben található tárolók megtekintése

A **Get-AzRecoveryServicesVault** használatával megtekintheti az aktuális előfizetésben található összes tároló listáját. Ezzel a paranccsal ellenőrizhető, hogy az új tároló létrejött-e, vagy hogy milyen tárolók érhetők el az előfizetésben.

Futtassa a parancsot, a Get-AzRecoveryServicesVault és az előfizetés összes tárolóját.

```powershell
Get-AzRecoveryServicesVault
```

```Output
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```


## <a name="installing-the-azure-backup-agent-on-a-dpm-server"></a>A Azure Backup-ügynök telepítése DPM-kiszolgálóra

A Azure Backup ügynök telepítése előtt le kell töltenie a telepítőt, és be kell jelentkeznie a Windows Serveren. A telepítő legújabb verzióját a [Microsoft letöltőközpontból](https://aka.ms/azurebackup_agent) vagy a Recovery Services-tároló irányítópult-lapjáról szerezheti be. Mentse a telepítőt egy könnyen elérhető helyre, például *\*letöltések.

Az ügynök telepítéséhez futtassa a következő parancsot egy emelt szintű PowerShell-konzolon a **DPM-kiszolgálón**:

```powershell
MARSAgentInstaller.exe /q
```

Ezzel telepíti az ügynököt az összes alapértelmezett beállítással. A telepítés eltarthat néhány percig a háttérben. Ha nem ad meg */Nu* beállítást, a telepítés végén megnyílik a **Windows Update** ablak, hogy ellenőrizze a frissítéseket.

Az ügynök megjelenik a telepített programok listájában.  > A telepített programok listájának megtekintéséhez válassza a Vezérlőpult**programok** > **programok és szolgáltatások**elemét.

![Ügynök telepítve](./media/backup-dpm-automation/installed-agent-listing.png)

### <a name="installation-options"></a>Telepítési beállítások

A parancssoron keresztül elérhető összes lehetőség megtekintéséhez használja a következő parancsot:

```powershell
MARSAgentInstaller.exe /?
```

Az elérhető lehetőségek a következők:

| Beállítás | Részletek | Alapértelmezett |
| --- | --- | --- |
| /q |Csendes telepítés |- |
| /p: "location" |A Azure Backup ügynök telepítési mappájának elérési útja. |C:\Program Files\Microsoft Azure Recovery Services ügynök |
| /s: "location" |A Azure Backup ügynökhöz tartozó gyorsítótár mappájának elérési útja. |C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch |
| /m |Microsoft Update |- |
| /nu |Frissítések keresése a telepítés befejezése után |- |
| /d |Microsoft Azure Recovery Services ügynök eltávolítása |- |
| /ph |Proxy gazdagép címe |- |
| /po |Proxy gazdagép portszáma |- |
| /pu |Proxy gazdagép felhasználóneve |- |
| /PW |Proxy jelszava |- |

## <a name="registering-dpm-to-a-recovery-services-vault"></a>DPM regisztrálása egy Recovery Services-tárolóban

Miután létrehozta a Recovery Services-tárolót, töltse le a legújabb ügynököt és a tároló hitelesítő adatait, és tárolja a megfelelő helyen (például C:\Downloads.).

```powershell
$credspath = "C:\downloads"
$credsfilename = Get-AzRecoveryServicesVaultSettingsFile -Backup -Vault $vault1 -Path  $credspath
$credsfilename
```

```Output
C:\downloads\testvault\_Sun Apr 10 2016.VaultCredentials
```

A DPM-kiszolgálón futtassa a [Start-OBRegistration](https://technet.microsoft.com/library/hh770398%28v=wps.630%29.aspx) parancsmagot a gép a tárolóban való regisztrálásához.

```powershell
$cred = $credspath + $credsfilename
Start-OBRegistration-VaultCredentials $cred -Confirm:$false
```

```Output
CertThumbprint      :7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName: testvault
Region              :West US
Machine registration succeeded.
```

### <a name="initial-configuration-settings"></a>Kezdeti konfigurációs beállítások

Ha a DPM-kiszolgáló regisztrálva van a Recovery Services-tárolóban, az alapértelmezett előfizetési beállításokkal kezdődik. Ezek az előfizetési beállítások közé tartoznak a hálózatkezelés, a titkosítás és az átmeneti környezet. Az előfizetési beállítások módosításához először a [Get-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612793) parancsmag használatával kell beolvasnia a meglévő (alapértelmezett) beállításokat a következővel:

```powershell
$setting = Get-DPMCloudSubscriptionSetting -DPMServerName "TestingServer"
```

A rendszer minden módosítást végrehajt a helyi PowerShell ```$setting``` -objektumon, majd a teljes objektum elkötelezett a DPM és Azure Backup a [set-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612791) parancsmag használatával történő mentéshez. A módosítások megőrzése érdekében a ```–Commit``` jelzőt kell használnia. A rendszer nem alkalmazza a beállításokat, és nem használja a Azure Backup, hacsak nincs véglegesítve.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## <a name="networking"></a>Hálózat

Ha a DPM-gép a Azure Backup szolgáltatáshoz való kapcsolódása egy proxykiszolgálón keresztül történik, akkor a sikeres biztonsági mentéshez meg kell adni a proxykiszolgáló beállításait. ```-ProxyServer```Ezt a ```-ProxyPort```és a, ```-ProxyUsername``` valamint a ```ProxyPassword``` [set-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612791) parancsmaggal végzett paraméterek használatával végezheti el. Ebben a példában nincs proxykiszolgáló, ezért explicit módon töröljük a proxyval kapcsolatos információkat.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoProxy
```

A sávszélesség ```-WorkHourBandwidth``` - ```-NonWorkHourBandwidth``` használat a hét egy adott halmazának beállításaival is vezérelhető. Ebben a példában nem állítunk be szabályozást.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoThrottle
```

## <a name="configuring-the-staging-area"></a>Az átmeneti környezet konfigurálása

A DPM-kiszolgálón futó Azure Backup ügynöknek ideiglenes tárterületre van szüksége a felhőből visszaállított adatokról (helyi átmeneti terület). Konfigurálja az előkészítési körzetet a [set-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612791) parancsmag és a ```-StagingAreaPath``` paraméter használatával.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -StagingAreaPath "C:\StagingArea"
```

A fenti példában az átmeneti területen a PowerShell-objektum ```$setting``` *C:\StagingArea* lesz beállítva. Győződjön meg arról, hogy a megadott mappa már létezik, különben az előfizetés beállításainak végleges véglegesítése sikertelen lesz.

### <a name="encryption-settings"></a>Titkosítási beállítások

A Azure Backup elküldett biztonsági mentési adatok titkosítva vannak az adatok titkosságának védelme érdekében. A titkosítási jelszó a "password" (jelszó), amely a visszaállításkor visszafejti az adatmennyiséget. Fontos, hogy a beállítás után biztonságos és biztonságos legyen az információ.

Az alábbi példában az első parancs egy biztonságos karakterlánccá alakítja ```passphrase123456789``` át a karakterláncot, és hozzárendeli a biztonságos karakterláncot a nevű ```$Passphrase```változóhoz. a második parancs a biztonságos karakterláncot ```$Passphrase``` állítja be jelszóként a biztonsági mentések titkosításához.

```powershell
$Passphrase = ConvertTo-SecureString -string "passphrase123456789" -AsPlainText -Force

Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -EncryptionPassphrase $Passphrase
```

> [!IMPORTANT]
> Ha beállította, a hozzáférési kód adatai biztonságban és biztonságban maradnak. Ezen jelszó nélkül nem állíthatók vissza az Azure-ból származó adatok.
>
>

Ekkor el kell végeznie az ```$setting``` objektum összes szükséges módosítását. Ne felejtse el véglegesíteni a módosításokat.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## <a name="protect-data-to-azure-backup"></a>Azure Backup adatainak védelme

Ebben a szakaszban egy üzemi kiszolgálót fog hozzáadni a DPM, majd az adatvédelmet a helyi DPM-tárolóba, majd Azure Backup. A példákban bemutatjuk, hogyan lehet biztonsági másolatot készíteni a fájlokról és mappákról. A logika egyszerűen bővíthető a DPM által támogatott adatforrások biztonsági mentéséhez. Az összes DPM biztonsági mentést egy olyan védelmi csoport (PG) szabályozza, amely négy részből áll:

1. A **csoporttagok** az összes olyan védhető objektum (más néven adatforrások a DPM ) listája, amelyeket a védelemmel ellátott csoportban kíván védelemmel ellátni. Előfordulhat például, hogy egy védelmi csoportba tartozó üzemi virtuális gépeket szeretne védelemmel ellátni, és egy másik védelmi csoportban lévő adatbázisokat SQL Server, mivel azok eltérő biztonsági mentési követelményekkel rendelkezhetnek. Mielőtt biztonsági mentést szeretne készíteni egy üzemi kiszolgáló adatforrásáról, győződjön meg arról, hogy a DPM-ügynök telepítve van a kiszolgálón, és felügyeli a DPM. Kövesse a DPM- [ügynök telepítésének](https://technet.microsoft.com/library/bb870935.aspx) és a megfelelő DPM-kiszolgálóhoz való csatolásának lépéseit.
2. Az **adatvédelmi módszer** megadja a cél biztonsági mentési helyeit – szalag, lemez és felhő. A példánkban a helyi lemezre és a felhőbe is biztosítjuk az adatvédelmet.
3. Egy **biztonsági mentési ütemezés** , amely meghatározza, hogy mikor kell a biztonsági mentést készíteni, és milyen gyakran kell szinkronizálni az adatokat a DPM-kiszolgáló és az üzemi kiszolgáló között.
4. Egy **adatmegőrzési ütemterv** , amely meghatározza, hogy mennyi ideig kell megőrizni a helyreállítási pontokat az Azure-ban.

### <a name="creating-a-protection-group"></a>Védelmi csoport létrehozása

Először hozzon létre egy új védelmi csoportot a [New-DPMProtectionGroup](https://technet.microsoft.com/library/hh881722) parancsmag használatával.

```powershell
$PG = New-DPMProtectionGroup -DPMServerName " TestingServer " -Name "ProtectGroup01"
```

A fenti parancsmag létrehoz egy *ProtectGroup01*nevű védelmi csoportot. Egy meglévő védelmi csoport később is módosítható a biztonsági mentés Azure-felhőbe való hozzáadásához. Ahhoz azonban, hogy a védelmi csoport módosításait – új vagy meglévő – a [Get-DPMModifiableProtectionGroup](https://technet.microsoft.com/library/hh881713) parancsmag használatával egy módosítható objektumhoz kell beszereznie a leírót.

```powershell
$MPG = Get-ModifiableProtectionGroup $PG
```

### <a name="adding-group-members-to-the-protection-group"></a>Csoporttagok hozzáadása a védelmi csoporthoz

Minden DPM-ügynök ismeri azon a kiszolgálón lévő adatforrások listáját, amelyeken telepítve van. Ha adatforrást szeretne hozzáadni a védelmi csoporthoz, a DPM-ügynöknek először el kell küldenie az adatforrások listáját a DPM-kiszolgálónak. Ezután egy vagy több adatforrás van kiválasztva, és hozzá lesz adva a védelmi csoporthoz. A megvalósításához szükséges PowerShell-lépések a következők:

1. A DPM által felügyelt összes kiszolgáló listájának beolvasása a DPM-ügynökön keresztül.
2. Válasszon egy adott kiszolgálót.
3. A kiszolgálón található összes adatforrás listájának beolvasása.
4. Válasszon ki egy vagy több adatforrást, és vegye fel őket a védelmi csoportba.

Azon kiszolgálók listáját, amelyeken a DPM-ügynök telepítve van, és a DPM-kiszolgáló felügyeli, a [Get-DPMProductionServer](https://technet.microsoft.com/library/hh881600) parancsmaggal szerezheti be. Ebben a példában a (z) *productionserver01* nevű PS-t fogjuk szűrni és konfigurálni a biztonsági mentéshez.

```powershell
$server = Get-ProductionServer -DPMServerName "TestingServer" | Where-Object {($_.servername) –contains “productionserver01”}
```

Most olvassa be ```$server``` az adatforrások listáját a [Get-DPMDatasource](https://technet.microsoft.com/library/hh881605) parancsmag használatával. Ebben a példában a *D\\*  kötet szűrését szeretnénk beállítani a biztonsági mentéshez. Ezt az adatforrást Ezután hozzáadja a védelmi csoporthoz az [Add-DPMChildDatasource](https://technet.microsoft.com/library/hh881732) parancsmag használatával. Ne felejtse el használni a módosítható védelmi ```$MPG``` csoport objektumot a hozzáadások végrehajtásához.

```powershell
$DS = Get-Datasource -ProductionServer $server -Inquire | Where-Object { $_.Name -contains “D:\” }

Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS
```

Ismételje meg ezt a lépést a szükségesnél többször is, amíg hozzá nem adta az összes kiválasztott adatforrást a védelmi csoporthoz. Akár csak egy adatforrást is elindíthat, és befejezheti a védelmi csoport létrehozásához szükséges munkafolyamatot, és egy későbbi időpontban további adatforrásokat adhat hozzá a védelmi csoporthoz.

### <a name="selecting-the-data-protection-method"></a>Adatvédelmi módszer kiválasztása

Miután hozzáadta az adatforrásokat a védelmi csoporthoz, a következő lépés a védelmi módszer megadása a [set-DPMProtectionType](https://technet.microsoft.com/library/hh881725) parancsmag használatával. Ebben a példában a védelmi csoport a helyi lemez és a felhő biztonsági mentésének beállítása. Az [Add-DPMChildDatasource](https://technet.microsoft.com/library/hh881732.aspx) parancsmag és az-online jelző használatával is meg kell adnia a felhőben védetté tenni kívánt adatforrást.

```powershell
Set-DPMProtectionType -ProtectionGroup $MPG -ShortTerm Disk –LongTerm Online
Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS –Online
```

### <a name="setting-the-retention-range"></a>A megőrzési tartomány beállítása

Állítsa be a biztonsági mentési pontok megőrzését a [set-DPMPolicyObjective](https://technet.microsoft.com/library/hh881762) parancsmag használatával. Habár a biztonsági mentés ütemtervének meghatározása előtt furcsának tűnhet, a ```Set-DPMPolicyObjective``` parancsmaggal automatikusan beállítja az alapértelmezett biztonsági mentési ütemtervet, amelyet később módosíthat. A biztonsági mentési ütemtervet először is be kell állítani, és a megőrzési házirendet követően.

Az alábbi példában a parancsmag beállítja a lemezes biztonsági mentések megőrzési paramétereit. Ez 10 napig megőrzi a biztonsági mentéseket, és 6 óránként szinkronizálja az adatokat az üzemi kiszolgáló és a DPM-kiszolgáló között. A ```SynchronizationFrequencyMinutes``` nem határozza meg, hogy a rendszer milyen gyakran hozza létre a biztonsági mentési pontokat, de milyen gyakran másolja a rendszer az adatfájlokat a DPM-kiszolgálóra.  Ez a beállítás megakadályozza, hogy a biztonsági másolatok túlságosan nagy méretűek legyenek.

```powershell
Set-DPMPolicyObjective –ProtectionGroup $MPG -RetentionRangeInDays 10 -SynchronizationFrequencyMinutes 360
```

Az Azure-ba irányuló biztonsági mentések esetén (a DPM az őket online biztonsági mentésként hivatkozik). a megőrzési tartományok a [nagyapa-apa-Son séma (GFS) használatával konfigurálhatók a hosszú távú megőrzéshez](backup-azure-backup-cloud-as-tape.md). Ez azt is megteheti, hogy a napi, heti, havi és éves adatmegőrzési házirendeket tartalmazó kombinált adatmegőrzési szabályzatot határoz meg. Ebben a példában egy tömböt hozunk létre, amely a kívánt összetett adatmegőrzési sémát jelképezi, majd a [set-DPMPolicyObjective](https://technet.microsoft.com/library/hh881762) parancsmag használatával konfigurálja a megőrzési tartományt.

```powershell
$RRlist = @()
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 180, Days)
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 104, Weeks)
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 60, Month)
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 10, Years)
Set-DPMPolicyObjective –ProtectionGroup $MPG -OnlineRetentionRangeList $RRlist
```

### <a name="set-the-backup-schedule"></a>A biztonsági mentési ütemterv beállítása

A DPM automatikusan beállítja az alapértelmezett biztonsági mentési ütemtervet, ha a ```Set-DPMPolicyObjective``` parancsmag használatával adja meg a védelmi célt. Az alapértelmezett ütemtervek módosításához használja a [Get-DPMPolicySchedule](https://technet.microsoft.com/library/hh881749) parancsmagot, majd a [set-DPMPolicySchedule](https://technet.microsoft.com/library/hh881723) parancsmagot.

```powershell
$onlineSch = Get-DPMPolicySchedule -ProtectionGroup $mpg -LongTerm Online
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[0] -TimesOfDay 02:00
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[1] -TimesOfDay 02:00 -DaysOfWeek Sa,Su –Interval 1
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[2] -TimesOfDay 02:00 -RelativeIntervals First,Third –DaysOfWeek Sa
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[3] -TimesOfDay 02:00 -DaysOfMonth 2,5,8,9 -Months Jan,Jul
Set-DPMProtectionGroup -ProtectionGroup $MPG
```

A fenti példában egy olyan ```$onlineSch``` tömb, amely négy elemet tartalmaz, amely a GFS séma védelmi csoportjának meglévő online védelmi ütemtervét tartalmazza:

1. ```$onlineSch[0]```a napi ütemtervet tartalmazza
2. ```$onlineSch[1]```a heti ütemtervet tartalmazza
3. ```$onlineSch[2]```a havi ütemtervet tartalmazza
4. ```$onlineSch[3]```az éves ütemezést tartalmazza

Tehát ha módosítania kell a heti ütemtervet, a ```$onlineSch[1]```következőre kell hivatkoznia:.

### <a name="initial-backup"></a>Kezdeti biztonsági mentés

Amikor első alkalommal készít biztonsági másolatot egy adatforrásról, a DPM-nek létre kell tennie a kezdeti replikát, amely létrehozza az adatforrásnak a DPM-replika kötetén védeni kívánt teljes másolatát. Ez a tevékenység egy adott időpontra ütemezhető, vagy manuálisan is aktiválható a [set-DPMReplicaCreationMethod](https://technet.microsoft.com/library/hh881715) parancsmag és a paraméter ```-NOW```használatával.

```powershell
Set-DPMReplicaCreationMethod -ProtectionGroup $MPG -NOW
```

### <a name="changing-the-size-of-dpm-replica--recovery-point-volume"></a>DPM-replika méretének módosítása & helyreállítási pont kötete

A DPM-replika kötetét és az árnyékmásolat-kötet méretét a [set-DPMDatasourceDiskAllocation](https://technet.microsoft.com/library/hh881618.aspx) parancsmaggal is módosíthatja, ahogy az alábbi példában látható: Get-DatasourceDiskAllocation-DataSource $DS set-DatasourceDiskAllocation-DataSource $DS-ProtectionGroup $MPG-Manual-ReplicaArea (2GB) – ShadowCopyArea (2GB)

### <a name="committing-the-changes-to-the-protection-group"></a>A védelmi csoport módosításainak véglegesítése

Végezetül a módosításokat véglegesíteni kell, mielőtt a DPM a biztonsági mentést az új védelmi csoport konfigurációjában. Ezt a [set-DPMProtectionGroup](https://technet.microsoft.com/library/hh881758) parancsmag használatával lehet megvalósítani.

```powershell
Set-DPMProtectionGroup -ProtectionGroup $MPG
```

## <a name="view-the-backup-points"></a>A biztonsági mentési pontok megtekintése

A [Get-DPMRecoveryPoint](https://technet.microsoft.com/library/hh881746) parancsmag használatával lekérheti az adatforrások összes helyreállítási pontjának listáját. Ebben a példában a következőket tesszük:

* az összes PGs beolvasása a DPM-kiszolgálón és egy tömbben tárolva```$PG```
* a következőhöz tartozó adatforrások beolvasása```$PG[0]```
* az adatforrás összes helyreállítási pontjának beolvasása.

```powershell
$PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
$DS = Get-DPMDatasource -ProtectionGroup $PG[0]
$RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online
```

## <a name="restore-data-protected-on-azure"></a>Az Azure-ban védett adatvédelem visszaállítása

Az adatvisszaállítás egy ```RecoverableItem``` objektum és egy ```RecoveryOption``` objektum kombinációja. Az előző szakaszban egy adatforráshoz tartozó biztonsági mentési pontok listáját kaptunk.

Az alábbi példában bemutatjuk, hogyan állíthatja vissza a Hyper-V rendszerű virtuális gépeket Azure Backupről a biztonsági mentési pontok és a helyreállítás céljának kombinálásával. Ez a példa a következőket tartalmazza:

* Helyreállítási lehetőség létrehozása a [New-DPMRecoveryOption](https://technet.microsoft.com/library/hh881592) parancsmag használatával.
* A biztonsági mentési pontok tömbjét a ```Get-DPMRecoveryPoint``` parancsmag használatával olvashatja be.
* Válasszon ki egy biztonsági mentési pontot a visszaállításhoz.

```powershell
$RecoveryOption = New-DPMRecoveryOption -HyperVDatasource -TargetServer "HVDCenter02" -RecoveryLocation AlternateHyperVServer -RecoveryType Recover -TargetLocation “C:\VMRecovery”

$PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
$DS = Get-DPMDatasource -ProtectionGroup $PG[0]
$RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online

Restore-DPMRecoverableItem -RecoverableItem $RecoveryPoints[0] -RecoveryOption $RecoveryOption
```

A parancsok egyszerűen bővíthetők bármilyen adatforrás-típusra.

## <a name="next-steps"></a>További lépések

* További információ a DPM-ről Azure Backupről: [Bevezetés a DPM Backup](backup-azure-dpm-introduction.md) használatába
