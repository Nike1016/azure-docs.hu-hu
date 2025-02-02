---
title: A Azure File Sync hibáinak megoldása | Microsoft Docs
description: Azure File Syncával kapcsolatos gyakori problémák elhárítása.
author: jeffpatt24
ms.service: storage
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 3395159e1427fa3d174b62c74c777d2f2ddd4900
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68721685"
---
# <a name="troubleshoot-azure-file-sync"></a>Azure File Sync – hibaelhárítás
A Azure File Sync segítségével központilag kezelheti a szervezete fájlmegosztást Azure Filesban, miközben megőrizheti a helyszíni fájlkiszolgáló rugalmasságát, teljesítményét és kompatibilitását. Azure File Sync átalakítja a Windows Servert az Azure-fájlmegosztás gyors gyorsítótárba. A Windows Serveren elérhető bármely protokoll használatával helyileg férhet hozzá az adataihoz, beleértve az SMB-t, az NFS-t és a FTPS is. Tetszőleges számú gyorsítótárral rendelkezhet a világ minden tájáról.

Ez a cikk a Azure File Sync-telepítéssel kapcsolatban felmerülő problémák hibaelhárításához és megoldásához nyújt segítséget. Azt is leírjuk, hogyan gyűjthetjük be a fontos naplókat a rendszerből, ha a probléma mélyebb vizsgálata szükséges. Ha nem látja a választ a kérdésére, felveheti velünk a kapcsolatot a következő csatornákon keresztül (növekvő sorrendben):

1. [Azure Storage-fórum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).
2. [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files).
3. A Microsoft ügyfélszolgálata. Új támogatási kérelem létrehozásához a Azure Portal **Súgó** lapján kattintson a **Súgó + támogatás** gombra, majd válassza az **új támogatási kérelem**lehetőséget.

## <a name="im-having-an-issue-with-azure-file-sync-on-my-server-sync-cloud-tiering-etc-should-i-remove-and-recreate-my-server-endpoint"></a>Probléma merült fel a Azure File Sync a saját kiszolgálón (szinkronizálás, felhőalapú rétegek stb.). El kell távolítani, majd újra létre kell hozni a kiszolgálói végpontot?
[!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]

## <a name="agent-installation-and-server-registration"></a>Ügynök telepítése és kiszolgáló regisztrálása
<a id="agent-installation-failures"></a>**Az ügynök telepítési hibáinak elhárítása**  
Ha a Azure File Sync-ügynök telepítése sikertelen, egy rendszergazda jogú parancssorban futtassa a következő parancsot a naplózás bekapcsolásához az ügynök telepítése során:

```
StorageSyncAgent.msi /l*v AFSInstaller.log
```

A telepítési hiba okának megállapításához tekintse át a Installer. log naplófájlt.

<a id="agent-installation-on-DC"></a>**Az ügynök telepítése nem sikerül Active Directory-tartomány vezérlőn**  
Ha olyan Active Directory tartományvezérlőre próbálja telepíteni a Szinkronizáló ügynököt, amelyen a PDC-szerepkör tulajdonosa egy Windows Server 2008 R2 vagy újabb operációs rendszer verziója, akkor előfordulhat, hogy a szinkronizálási ügynök telepítése sikertelen lesz.

A probléma megoldásához vigye át az elsődleges tartományvezérlői szerepkört egy másik, Windows Server 2012 R2 vagy újabb rendszert futtató tartományvezérlőre, majd telepítse a szinkronizálást.

<a id="server-registration-prerequisites"></a>**A kiszolgáló regisztrálása a következő üzenetet jeleníti meg: "Az előfeltételek hiányoznak"**

Ez az üzenet akkor jelenik meg, ha az az vagy az AzureRM PowerShell-modul nincs telepítve a PowerShell 5,1-ben. 

> [!Note]  
> A ServerRegistration. exe nem támogatja a PowerShell 6. x verzióját. A-kiszolgáló regisztrálásához a PowerShell 6. x verziójában használhatja a Register-AzStorageSyncServer parancsmagot.

Az az vagy a AzureRM modul a PowerShell 5,1-ben történő telepítéséhez hajtsa végre a következő lépéseket:

1. Írja be a **PowerShell** parancsot egy rendszergazda jogú parancssorba, és nyomja le az ENTER billentyűt.
2. Telepítse a legújabb az vagy AzureRM modult a dokumentációt követve:
    - [Az Module (.NET 4.7.2 szükséges)](https://go.microsoft.com/fwlink/?linkid=2062890)
    - [AzureRM modul]( https://go.microsoft.com/fwlink/?linkid=856959)
3. Futtassa a ServerRegistration. exe fájlt, és fejezze be a varázslót, hogy regisztrálja a kiszolgálót egy Storage Sync szolgáltatással.

<a id="server-already-registered"></a>**A kiszolgáló regisztrálása a következő üzenetet jeleníti meg: "Ez a kiszolgáló már regisztrálva van"** 

![A "kiszolgáló már regisztrálva" hibaüzenet jelenik meg a kiszolgáló regisztrálása párbeszédpanelén](media/storage-sync-files-troubleshoot/server-registration-1.png)

Ez az üzenet akkor jelenik meg, ha a kiszolgáló korábban a Storage Sync szolgáltatásban volt regisztrálva. A kiszolgáló aktuális Storage Sync szolgáltatásból való regisztrációjának megszüntetéséhez, majd az új Storage Sync Service-ben való regisztrálásához hajtsa végre a következő témakörben ismertetett lépéseket: [kiszolgáló regisztrációjának](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service)megszüntetése a Azure file Sync használatával.

Ha a kiszolgáló nem szerepel a Storage Sync szolgáltatásban a **regisztrált kiszolgálók** területen, akkor a regisztrálni kívánt kiszolgálón futtassa a következő PowerShell-parancsokat:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Reset-StorageSyncServer
```

> [!Note]  
> Ha a kiszolgáló egy fürt része, akkor a nem kötelező *reset-StorageSyncServer-CleanClusterRegistration* paraméter használatával is eltávolíthatja a fürt regisztrációját.

<a id="web-site-not-trusted"></a>**Amikor Regisztrálok egy kiszolgálót, számos "webhely nem megbízható" választ látok. Hogy miért?**  
Ez a probléma akkor fordul elő, ha a **bővített Internet Explorer biztonsági** házirend engedélyezve van a kiszolgáló regisztrálása során. További információ arról, hogyan lehet helyesen letiltani az **Internet Explorer biztonsági** házirendjét: [felkészülés a Windows Server használatára a Azure File Sync](storage-sync-files-deployment-guide.md#prepare-windows-server-to-use-with-azure-file-sync) és az [Azure file Sync üzembe helyezése](storage-sync-files-deployment-guide.md)során.

<a id="server-registration-missing"></a>**A kiszolgáló nem szerepel a Azure Portal regisztrált kiszolgálók listáján**  
Ha egy kiszolgáló nem szerepel a Storage Sync szolgáltatáshoz tartozó **regisztrált kiszolgálók** területen:
1. Jelentkezzen be a regisztrálni kívánt kiszolgálóra.
2. Nyissa meg a fájlkezelőt, és lépjen a Storage Sync Agent telepítési könyvtárához (az alapértelmezett hely a C:\Program Files\Azure\StorageSyncAgent). 
3. Futtassa a ServerRegistration. exe fájlt, és fejezze be a varázslót, hogy regisztrálja a kiszolgálót egy Storage Sync szolgáltatással.

## <a name="sync-group-management"></a>Szinkronizálási csoport kezelése
<a id="cloud-endpoint-using-share"></a>**A Felhőbeli végpont létrehozása sikertelen, a következő hibával: „A megadott Azure-fájlmegosztást már egy másik felhőbeli végpont használja”**  
Ez a hiba akkor fordul elő, ha az Azure-fájlmegosztás már használatban van egy másik Felhőbeli végponton. 

Ha ezt az üzenetet látja, és az Azure-fájlmegosztást jelenleg nem egy felhőalapú végpont használja, hajtsa végre a következő lépéseket az Azure-fájlmegosztás Azure File Sync metaadatainak törléséhez:

> [!Warning]  
> Ha egy felhőalapú végpont által jelenleg használt Azure-fájlmegosztás metaadatait törli, Azure File Sync művelet sikertelen lesz. 

1. A Azure Portal nyissa meg az Azure-fájlmegosztást.  
2. Kattintson a jobb gombbal az Azure-fájlmegosztás elemre, majd válassza a **metaadatok szerkesztése**lehetőséget.
3. Kattintson a jobb gombbal a **SyncService**elemre, majd válassza a **Törlés**lehetőséget.

<a id="cloud-endpoint-authfailed"></a>**A Felhőbeli végpont létrehozása sikertelen, a következő hibával: „AuthorizationFailed”**  
Ez a hiba akkor fordul elő, ha a felhasználói fiók nem rendelkezik megfelelő jogosultsággal a Felhőbeli végpont létrehozásához. 

A Felhőbeli végpont létrehozásához a felhasználói fióknak a következő Microsoft-engedélyezési engedélyekkel kell rendelkeznie:  
* Olvasni Szerepkör-definíció beolvasása
* Írni Egyéni szerepkör-definíció létrehozása vagy módosítása
* Olvasni Szerepkör-hozzárendelés beolvasása
* Írni Szerepkör-hozzárendelés létrehozása

A következő beépített szerepkörök rendelkeznek a szükséges Microsoft-engedélyezési engedélyekkel:  
* Tulajdonos
* Felhasználói hozzáférés adminisztrátora

Annak megállapításához, hogy a felhasználói fiók szerepköre rendelkezik-e a szükséges engedélyekkel:  
1. A Azure Portal válassza az **erőforráscsoportok**lehetőséget.
2. Válassza ki azt az erőforráscsoportot, amelyben a Storage-fiók található, majd válassza a **hozzáférés-vezérlés (iam)** lehetőséget.
3. Válassza ki a **szerepkör** -hozzárendelések lapot.
4. Válassza ki a felhasználói fiókhoz tartozó szerepkört (például tulajdonos vagy közreműködő).
5. Az **erőforrás-szolgáltató** listában válassza a **Microsoft-hitelesítés**lehetőséget. 
    * A **szerepkör** -hozzárendelésnek **olvasási** és **írási** engedélyekkel kell rendelkeznie.
    * A **szerepkör** -definíciónak **olvasási** és **írási** engedélyekkel kell rendelkeznie.

<a id="-2134375898"></a>**A kiszolgálói végpont létrehozása sikertelen, a következő hibával: "MgmtServerJobFailed" (hibakód:-2134375898 vagy 0x80c80226)**  
Ez a hiba akkor fordul elő, ha a kiszolgálói végpont elérési útja a rendszerköteten, a felhő-rétegek pedig engedélyezve van. A felhő-rétegek nem támogatottak a rendszerköteten. Ha kiszolgálói végpontot szeretne létrehozni a rendszerköteten, tiltsa le a Felhőbeli rétegek létrehozását a kiszolgálói végpont létrehozásakor.

<a id="-2147024894"></a>**A kiszolgálói végpont létrehozása sikertelen, a következő hibával: "MgmtServerJobFailed" (hibakód:-2147024894 vagy 0x80070002)**  
Ez a hiba akkor fordul elő, ha a megadott kiszolgálói végpont elérési útja érvénytelen. Ellenőrizze, hogy a megadott kiszolgálói végpont elérési útja egy helyileg csatlakoztatott NTFS-kötet. Megjegyzés: Azure File Sync nem támogatja a csatlakoztatott meghajtók kiszolgálói végponti útvonalként való használatát.

<a id="-2134347507"></a>**A kiszolgálói végpont létrehozása sikertelen, a következő hibával: "MgmtServerJobFailed" (hibakód:-2134347507 vagy 0x80c8710d)**  
Ez a hiba azért fordul elő, mert Azure File Sync nem támogatja a kiszolgálói végpontokat olyan köteteken, amelyeken tömörítve van a rendszerkötet információs mappája. A probléma megoldásához bontsa ki a rendszerkötet információs mappáját. Ha a rendszerkötet információ mappája az egyetlen, a köteten tömörített mappa, hajtsa végre a következő lépéseket:

1. Töltse le a [PsExec](https://docs.microsoft.com/sysinternals/downloads/psexec) eszközt.
2. Futtassa a következő parancsot egy rendszergazda jogú parancssorból a rendszerfiókban futó parancssor indításához: **PsExec. exe-i-s-d cmd**
3. A rendszerfiók alatt futó parancssorból írja be a következő parancsokat, és nyomja le az ENTER billentyűt:   
    **CD/d "meghajtóbetűjel: \ rendszerkötet adatai"**  
    **Compact/u/s**

<a id="-2134376345"></a>**A kiszolgálói végpont létrehozása sikertelen, a következő hibával: "MgmtServerJobFailed" (hibakód:-2134376345 vagy 0x80C80067)**  
Ez a hiba akkor fordul elő, ha a kiszolgálói végpontok száma elérte a kiszolgálói korlátot. Azure File Sync jelenleg legfeljebb 30 kiszolgálói végpontot támogat kiszolgálónként. További információ: [Azure file Sync méretezési célok](https://docs.microsoft.com/azure/storage/files/storage-files-scale-targets#azure-file-sync-scale-targets).

<a id="-2134376427"></a>**A kiszolgálói végpont létrehozása sikertelen, a következő hibával: "MgmtServerJobFailed" (hibakód:-2134376427 vagy 0x80c80015)**  
Ez a hiba akkor fordul elő, ha egy másik kiszolgálói végpont már szinkronizálta a megadott kiszolgálói végpont elérési útját. A Azure File Sync nem támogatja több kiszolgálói végpont szinkronizálását ugyanazon a címtáron vagy köteten.

<a id="-2134347757"></a>**A kiszolgálói végpont törlése sikertelen, a következő hibával: "MgmtServerJobExpired" (hibakód:-2134347757 vagy 0x80c87013)**  
Ez a hiba akkor fordul elő, ha a kiszolgáló kapcsolat nélküli üzemmódban van, vagy nem rendelkezik hálózati kapcsolattal. Ha a kiszolgáló már nem érhető el, törölje a kiszolgáló regisztrációját a portálon, amely törli a kiszolgálói végpontokat. A kiszolgálói végpontok törléséhez kövesse a [kiszolgáló regisztrációjának megszüntetése a Azure file Sync](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service)használatával című témakörben ismertetett lépéseket.

<a id="server-endpoint-provisioningfailed"></a>**Nem lehet megnyitni a kiszolgálói végpont tulajdonságlapját, vagy frissíteni kell a felhőre vonatkozó rétegű szabályzatot**  
Ez a probléma akkor fordulhat elő, ha a kiszolgálói végponton nem sikerül felügyeleti művelet. Ha a kiszolgálói végpont tulajdonságai lap nem nyílik meg a Azure Portalban, a kiszolgáló-végpont frissítése a kiszolgáló PowerShell-parancsaival kijavíthatja ezt a problémát. 

```powershell
# Get the server endpoint id based on the server endpoint DisplayName property
Get-AzStorageSyncServerEndpoint `
    -ResourceGroupName myrgname `
    -StorageSyncServiceName storagesvcname `
    -SyncGroupName mysyncgroup | `
Tee-Object -Variable serverEndpoint

# Update the free space percent policy for the server endpoint
Set-AzStorageSyncServerEndpoint `
    -InputObject $serverEndpoint
    -CloudTiering `
    -VolumeFreeSpacePercent 60
```
<a id="server-endpoint-noactivity"></a>**A kiszolgálói végpont "nincs tevékenység" vagy "függő" állapotú, míg a regisztrált kiszolgálókon a kiszolgáló állapota "offline" jelenik meg.**  

Ez a probléma akkor fordulhat elő, ha a tárolási szinkronizálási figyelő folyamata nem fut, vagy ha a kiszolgáló proxy vagy tűzfal miatt nem tud kommunikálni a Azure File Sync szolgáltatással.

A probléma megoldásához végezze el az alábbi lépéseket:

1. Nyissa meg a Feladatkezelőt a kiszolgálón, és ellenőrizze, hogy fut-e a Storage Sync Monitor-folyamat (AzureStorageSyncMonitor.exe). Ha a folyamat nem fut, először próbálja meg újraindítani a kiszolgálót. Ha a kiszolgáló újraindítása nem oldja meg a problémát, frissítsen a legújabb Azure File Sync [ügynök verziójára](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes).
2. Ellenőrizze, hogy a tűzfal és a proxybeállítások helyesen vannak-e konfigurálva:
    - Ha a kiszolgáló tűzfal mögött van, ellenőrizze, hogy engedélyezve van-e a 443-as port kimenő forgalma. Ha a tűzfal bizonyos tartományokra korlátozza a forgalmat, ellenőrizze, hogy elérhetők-e a [](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#firewall) tűzfal dokumentációjában felsorolt tartományok.
    - Ha a kiszolgáló proxy mögött van, a proxy [dokumentációjában](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#proxy)ismertetett lépéseket követve konfigurálja a számítógép-szintű vagy alkalmazásspecifikus proxybeállításokat.

<a id="endpoint-noactivity-sync"></a>**A kiszolgálói végpont állapota "nincs tevékenység", a kiszolgáló állapota pedig a regisztrált kiszolgálók panelen "online"**  

A "nincs tevékenység" kiszolgálói végpont állapota azt jelenti, hogy a kiszolgálói végpont nem naplózta a szinkronizálási tevékenységet az elmúlt két órában.

Előfordulhat, hogy a kiszolgálói végpont nem naplózza a szinkronizálási tevékenységet a következő okok miatt:

- A 4.3.0.0 vagy régebbi verziójú ügynök telepítve van, és a kiszolgáló aktív VSS-szinkronizálási munkamenettel (SnapshotSync) rendelkezik. Ha egy VSS-szinkronizálási munkamenet aktív egy kiszolgálói végpont esetében, az ugyanazon a köteten lévő más kiszolgálói végpontok nem indíthatnak indítási szinkronizálási munkamenetet, amíg a VSS-szinkronizálási munkamenet be nem fejeződik. A probléma megoldásához telepítse az Agent 5.0.2.0 vagy újabb verzióját, amely támogatja a köteteken szinkronizált több kiszolgálói végpontot, amikor a VSS-szinkronizálási munkamenet aktív.

    A kiszolgáló aktuális szinkronizálási tevékenységének ellenőrzéséhez tekintse meg [az aktuális szinkronizálási munkamenet állapotának figyelését hogyan](#how-do-i-monitor-the-progress-of-a-current-sync-session).

- A kiszolgáló elérte az egyidejű szinkronizálási munkamenetek maximális számát. 
    - Ügynök 4-es verziója. x és újabb: A korlát a rendelkezésre álló rendszererőforrások alapján változhat.
    - Ügynök 3. x verziója: 2 másodpercenként aktív szinkronizálási munkamenet vagy legfeljebb 8 aktív szinkronizálási munkamenet kiszolgálónként.

> [!Note]  
> Ha a kiszolgáló állapota a regisztrált kiszolgálók panelen "offline" állapotú, akkor hajtsa végre a kiszolgálói végponton dokumentált lépéseket ["nincs tevékenység" vagy "függő" állapotú, a regisztrált kiszolgálók panelen pedig a kiszolgáló állapota "offline"](#server-endpoint-noactivity) szakasz .

## <a name="sync"></a>Szinkronizálás
<a id="afs-change-detection"></a>**Ha egy fájlt közvetlenül az Azure-fájlmegosztás SMB-n vagy a portálon keresztül hoztam létre, mennyi ideig tart a fájl szinkronizálása a szinkronizálási csoportban lévő kiszolgálókhoz?**  
[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="serverendpoint-pending"></a>**A kiszolgálói végpont állapota több órán keresztül függő állapotban van**  
Ez a probléma akkor várható, ha Felhőbeli végpontot hoz létre, és olyan Azure-fájlmegosztást használ, amely tartalmaz egy adatkészletet. Az Azure-fájlmegosztás változásainak vizsgálatára szolgáló változás-számbavételi feladatot be kell fejezni ahhoz, hogy a fájlok szinkronizálva legyenek a Felhőbeli és a kiszolgálói végpontok között. A feladatok elvégzéséhez szükséges idő az Azure-fájlmegosztás névterének méretétől függ. A kiszolgálói végpont állapotának frissítenie kell a változás-számbavételi feladatok befejeződése után.

### <a id="broken-sync"></a>Hogyan figyeli a szinkronizálás állapotát?
# <a name="portaltabportal1"></a>[Portál](#tab/portal1)
Az egyes szinkronizálási csoportokon belül megtekintheti az egyes kiszolgálói végpontokat az utolsó befejezett szinkronizálási munkamenetek állapotának megtekintéséhez. A zöld állapot oszlop és a 0 értéknél nem szinkronizált fájlok azt jelzik, hogy a szinkronizálás a várt módon működik. Ha ez nem igaz, tekintse meg a gyakori szinkronizálási hibák listáját és a nem szinkronizált fájlok kezelését ismertető szakaszt. 

![A Azure Portal képernyőképe](media/storage-sync-files-troubleshoot/portal-sync-health.png)

# <a name="servertabserver"></a>[Server](#tab/server)
Nyissa meg a kiszolgáló telemetria-naplóit, amelyek a Eseménynapló címen `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry`találhatók. Az 9102-es esemény megfelel egy befejezett szinkronizálási munkamenetnek; a szinkronizálás legutóbbi állapotában keresse meg a legutóbbi, 9102-es AZONOSÍTÓJÚ eseményt. A SyncDirection azt jelzi, hogy ez a munkamenet feltöltés vagy Letöltés volt. Ha a HResult 0, akkor a szinkronizálási munkamenet sikeres volt. A nem nulla HResult azt jelzi, hogy hiba történt a szinkronizálás során; a gyakori hibák listáját alább találja. Ha a PerItemErrorCount nagyobb, mint 0, ez azt jelenti, hogy egyes fájlok vagy mappák nem voltak megfelelően szinkronizálva. Lehetséges, hogy HResult 0, de a PerItemErrorCount értéke 0-nál nagyobb.

Az alábbi példa egy sikeres feltöltést mutat be. A rövidség kedvéért csak néhány 9102-eseményben szereplő érték szerepel az alábbiakban. 

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: 0, 
SyncFileCount: 2, SyncDirectoryCount: 0,
AppliedFileCount: 2, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0,
TransferredFiles: 2, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

Ezzel szemben egy sikertelen feltöltés a következőképpen néz ki:

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: -2134364065,
SyncFileCount: 0, SyncDirectoryCount: 0, 
AppliedFileCount: 0, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0, 
TransferredFiles: 0, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

Időnként a szinkronizálási munkamenetek meghiúsulnak, vagy nem nulla PerItemErrorCount, de továbbra is előrehaladást végeznek, és néhány fájl szinkronizálása sikeresen megtörtént. Ez megtekinthető az alkalmazott * mezőkben (AppliedFileCount, AppliedDirCount, AppliedTombstoneCount és AppliedSizeBytes), amelyekből megtudhatja, hogy a munkamenet mekkora mértékben sikeres. Ha olyan sorban több szinkronizálási munkamenet jelenik meg, amely meghibásodik, de egyre több alkalmazott * szám van, akkor a támogatási jegy megnyitása előtt meg kell adnia a szinkronizálási időt.

---

### <a name="how-do-i-monitor-the-progress-of-a-current-sync-session"></a>Hogyan figyeli az aktuális szinkronizálási munkamenet állapotát?
# <a name="portaltabportal1"></a>[Portál](#tab/portal1)
A szinkronizálási csoporton belül nyissa meg a kérdéses kiszolgálói végpontot, és tekintse meg a szinkronizálási tevékenység szakaszt az aktuális szinkronizálási munkamenetben feltöltött vagy letöltött fájlok számának megtekintéséhez. Vegye figyelembe, hogy ez az állapot körülbelül 5 percet vesz igénybe, és ha a szinkronizálási munkamenete elég kicsi az adott időszakon belül, előfordulhat, hogy a portálon nem lehet jelenteni. 

# <a name="servertabserver"></a>[Server](#tab/server)
Tekintse meg a legutóbbi 9302 eseményt a telemetria-naplóban a kiszolgálón (a Eseménynapló lépjen az alkalmazások és szolgáltatások Logs\Microsoft\FileSync\Agent\Telemetry). Ez az esemény az aktuális szinkronizálási munkamenet állapotát jelzi. A TotalItemCount azt jelzi, hogy hány fájlt kell szinkronizálni, AppliedItemCount az eddig szinkronizált fájlok számát, és PerItemErrorCount a szinkronizálni nem kívánt fájlok számát (lásd alább, hogyan kell kezelni ezt).

```
Replica Sync Progress. 
ServerEndpointName: <CI>sename</CI>, SyncGroupName: <CI>sgname</CI>, ReplicaName: <CI>rname</CI>, 
SyncDirection: Upload, CorrelationId: {AB4BA07D-5B5C-461D-AAE6-4ED724762B65}. 
AppliedItemCount: 172473, TotalItemCount: 624196. AppliedBytes: 51473711577, 
TotalBytes: 293363829906. 
AreTotalCountsFinal: true. 
PerItemErrorCount: 1006.
```
---

### <a name="how-do-i-know-if-my-servers-are-in-sync-with-each-other"></a>Hogyan tudni, hogy a kiszolgálók szinkronizálva vannak-e egymással?
# <a name="portaltabportal1"></a>[Portál](#tab/portal1)
Az adott szinkronizálási csoport minden egyes kiszolgálójára vonatkozóan ügyeljen a következőre:
- A legutóbbi szinkronizálásra és letöltésre irányuló legutóbbi szinkronizálás időbélyege nemrég történt.
- Az állapot a feltöltéshez és a letöltéshez egyaránt zöld.
- A szinkronizálási tevékenység mezője nagyon kevés vagy nem szinkronizálható fájlt jelenít meg.
- A nem szinkronizált fájlok esetében a feltöltés és a letöltés is 0.

# <a name="servertabserver"></a>[Server](#tab/server)
Tekintse meg a befejezett szinkronizálási munkameneteket, amelyeket 9102 esemény jelöl a telemetria eseménynaplójában az egyes kiszolgálók esetében (a Eseménynapló `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry`a következő helyen:). 

1. Az adott kiszolgálón ellenőrizze, hogy a legújabb feltöltési és letöltési munkamenetek sikeresen befejeződtek-e. Ehhez ellenőrizze, hogy a HResult és a PerItemErrorCount egyaránt 0-e a feltöltéshez és a letöltéshez (a SyncDirection mező azt jelzi, hogy egy adott munkamenet egy feltöltési vagy letöltési munkamenet). Vegye figyelembe, hogy ha nem látja a legutóbb befejezett szinkronizálási munkamenetet, valószínűleg egy szinkronizálási munkamenet van folyamatban, ami várható, ha csak nagy mennyiségű adattal bővült vagy módosított.
2. Ha egy kiszolgáló teljesen naprakész állapotú a felhőben, és a szinkronizálás nem változik egyik irányban sem, akkor üres szinkronizálási munkamenetek jelennek meg. Ezeket a feltöltési és letöltési események jelzik, amelyekben az összes szinkronizálási * mező (SyncFileCount, SyncDirCount, SyncTombstoneCount és SyncSizeBytes) nulla, ami azt jelenti, hogy nem történt szinkronizálás. Vegye figyelembe, hogy ezek az üres szinkronizálási munkamenetek nem fordulnak elő nagy forgalmú kiszolgálókon, mivel mindig valami újat kell szinkronizálni. Ha nincs szinkronizálási tevékenység, azok 30 percenként történnek. 
3. Ha az összes kiszolgáló naprakész a felhővel, a legutóbbi feltöltési és letöltési munkamenetek üres szinkronizálási munkameneteket jelentenek, de ésszerű bizonyossággal mondhatjuk, hogy a rendszer egésze szinkronban van. 
    
Vegye figyelembe, hogy ha közvetlenül az Azure-fájlmegosztás módosításait végezte, Azure File Sync nem fogja észlelni ezt a változást, amíg a változások számbavétele nem fut, ami 24 óránként történik. Az is lehetséges, hogy egy kiszolgáló a felhővel naprakész állapotba kerül, ha valójában nem az Azure-fájlmegosztás legutóbbi módosításait adta meg. 

---

### <a name="how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing"></a>Hogyan ellenőrizze, hogy vannak-e olyan fájlok vagy mappák, amelyek nem szinkronizálhatók?
Ha a kiszolgálón lévő PerItemErrorCount vagy a portálon nem szinkronizált fájlok száma meghaladja a 0 értéket az adott szinkronizálási munkamenet esetében, az azt jelenti, hogy egyes elemek szinkronizálása sikertelen. A fájlok és mappák tartalmazhatnak olyan jellemzőket, amelyek megakadályozzák a szinkronizálást. Ezek a jellemzők tartósak lehetnek, és a szinkronizálás folytatásához explicit műveletre van szükség, például a nem támogatott karakterek eltávolítása a fájl vagy a mappa nevéből. Átmenetiek is lehetnek, ami azt jelenti, hogy a fájl vagy mappa automatikusan folytatja a szinkronizálást. a megnyitott leíróval rendelkező fájlok például automatikusan folytatják a szinkronizálást a fájl bezárásakor. Ha a Azure File Sync motor észleli ezt a problémát, a rendszer egy olyan naplófájlt állít elő, amely elemezhető a jelenleg nem szinkronizált elemek listázásához.

Ezeknek a hibáknak a megtekintéséhez futtassa a **FileSyncErrorsReport. ps1** PowerShell-szkriptet (a Azure file Sync-ügynök ügynök-telepítési könyvtárában), hogy azonosítsa azokat a fájlokat, amelyeket nem sikerült szinkronizálni a megnyitott kezelők, a nem támogatott karakterek vagy más problémák miatt. A Elemelérési út mező megadja a fájl helyét a gyökérszintű szinkronizálási könyvtárhoz viszonyítva. Az alábbi gyakori szinkronizálási hibák listáját itt találja: szervizelési lépések.

#### <a name="troubleshooting-per-filedirectory-sync-errors"></a>Hibaelhárítás/fájl/címtár-szinkronizálási hibák
**ItemResults-naplózási hibák**  

| HRESULT | HRESULT (decimális) | Hibasztring | Probléma | Szervizelés |
|---------|-------------------|--------------|-------|-------------|
| 0x80070043 | – 2147942467 | ERROR_BAD_NET_NAME | A (z) kiszolgálón található, rétegű fájl nem érhető el. Ez a probléma akkor fordul elő, ha a lépcsőzetes fájl nem lett meghívva a kiszolgálói végpont törlése előtt. | A probléma megoldásához tekintse [meg a kiszolgálói végpont törlése után a kiszolgálón nem érhető el a lépcsőzetes fájlok](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint). |
| 0x80c80207 | -2134375929 | ECS_E_SYNC_CONSTRAINT_CONFLICT | A fájl vagy a könyvtár módosítása még nem szinkronizálható, mert egy függő mappa még nincs szinkronizálva. Ez az érték szinkronizálva lesz a függő módosítások szinkronizálása után. | Nincs szükség felhasználói műveletre. |
| 0x8007007b | -2147024773 | ERROR_INVALID_NAME | A fájl vagy a könyvtár neve érvénytelen. | Nevezze át a szóban forgó fájlt vagy könyvtárat. További információt a nem [támogatott karakterek kezelésével](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#handling-unsupported-characters) foglalkozó témakörben talál. |
| 0x80c80255 | – 2134375851 | ECS_E_XSMB_REST_INCOMPATIBILITY | A fájl vagy a könyvtár neve érvénytelen. | Nevezze át a szóban forgó fájlt vagy könyvtárat. További információt a nem [támogatott karakterek kezelésével](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#handling-unsupported-characters) foglalkozó témakörben talál. |
| 0x80c80018 | -2134376424 | ECS_E_SYNC_FILE_IN_USE | A fájl nem szinkronizálható, mert használatban van. A fájl szinkronizálva lesz, ha már nincs használatban. | Nincs szükség felhasználói műveletre. Azure File Sync létrehoz egy ideiglenes VSS-pillanatképet naponta egyszer a kiszolgálón a megnyitott leíróval rendelkező fájlok szinkronizálásához. |
| 0x80c8031d | -2134375651 | ECS_E_CONCURRENCY_CHECK_FAILED | A fájl módosult, de a szinkronizálás még nem észlelte a változást. A rendszer a változás észlelése után helyreállítja a szinkronizálást. | Nincs szükség felhasználói műveletre. |
| 0x80070002 | – 2147024894 | ERROR_FILE_NOT_FOUND | A fájl törölve lett, és a szinkronizálás nem ismeri a változást. | Nincs szükség felhasználói műveletre. A szinkronizálás leállítja a hiba naplózását, ha a Change észlelés észleli a fájlt. |
| 0x80c80205 | – 2134375931 | ECS_E_SYNC_ITEM_SKIP | A rendszer kihagyta a fájlt, de szinkronizálva lesz a következő szinkronizálási munkamenet során. | Nincs szükség felhasználói műveletre. |
| 0x80c8603e | -2134351810 | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED | A fájl nem szinkronizálható, mert elérte az Azure-fájlmegosztás korlátját. | A probléma megoldásához tekintse meg a hibaelhárítási útmutató [Azure fájlmegosztás tárolási korlátja](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#-2134351810) című szakaszát. |
| 0x80c8027C | – 2134375812 | ECS_E_ACCESS_DENIED_EFS | A fájlt egy nem támogatott megoldás (például az NTFS EFS) titkosítja. | A fájl visszafejtése és egy támogatott titkosítási megoldás használata. A támogatási megoldások listáját a tervezési útmutató [titkosítási megoldások](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#encryption-solutions) című szakaszában találja. |
| 0x80c80283 | – 2160591491 | ECS_E_ACCESS_DENIED_DFSRRO | A fájl egy DFS-R Írásvédett replikációs mappában található. | A fájl egy DFS-R Írásvédett replikációs mappában található. Azure Files a szinkronizálás nem támogatja a kiszolgálói végpontokat az elosztott fájlrendszer-R Írásvédett replikációs mappáiban. További információt a [tervezési útmutatóban](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#distributed-file-system-dfs) talál. |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | A fájl törlés függő állapotú. | Nincs szükség felhasználói műveletre. A rendszer törli a fájlt, ha az összes megnyitott fájl leírója le van zárva. |
| 0x80c86044 | – 2134351804 | ECS_E_AZURE_AUTHORIZATION_FAILED | A fájl nem szinkronizálható, mert a Storage-fiók tűzfal-és virtuális hálózati beállításai engedélyezve vannak, és a kiszolgáló nem fér hozzá a Storage-fiókhoz. | Adja hozzá a kiszolgáló IP-címét vagy virtuális hálózatát a telepítési útmutató [tűzfal-és virtuális hálózati beállításainak konfigurálása](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings) című szakaszában ismertetett lépéseket követve. |
| 0x80c80243 | – 2134375869 | ECS_E_SECURITY_DESCRIPTOR_SIZE_TOO_LARGE | A fájl nem szinkronizálható, mert a biztonsági leíró mérete meghaladja az 64 KiB-os korlátot. | A probléma megoldásához távolítsa el a hozzáférés-vezérlési bejegyzéseket (ACE) a fájlon a biztonsági leíró méretének csökkentése érdekében. |
| 0x8000ffff | – 2147418113 | E_UNEXPECTED | Váratlan hiba miatt nem lehet szinkronizálni a fájlt. | Ha a hiba több napig is fennáll, nyisson meg egy támogatási esetet. |
| 0x80070020 | -2147024864 | ERROR_SHARING_VIOLATION | A fájl nem szinkronizálható, mert használatban van. A fájl szinkronizálva lesz, ha már nincs használatban. | Nincs szükség felhasználói műveletre. |
| 0x80c80017 | -2134376425 | ECS_E_SYNC_OPLOCK_BROKEN | A fájl módosult a szinkronizálás során, ezért újra kell szinkronizálni. | Nincs szükség felhasználói műveletre. |

#### <a name="handling-unsupported-characters"></a>Nem támogatott karakterek feldolgozása
Ha a **FileSyncErrorsReport. ps1** PowerShell-parancsfájl a nem támogatott karakterek (hibakód: 0x8007007b vagy 0x80c80255) miatt hibát jelez, távolítsa el vagy nevezze át a hibákat a megfelelő fájlnevek alapján. A PowerShell valószínűleg kérdőjelként vagy üres téglalapként fogja kinyomtatni ezeket a karaktereket, mivel a legtöbb ilyen karakternek nincs szabványos vizualizációs kódolása. A [kiértékelési eszköz](storage-sync-files-planning.md#evaluation-cmdlet) használható a nem támogatott karakterek azonosítására.

Az alábbi táblázat tartalmazza az összes Unicode-karaktert, Azure File Sync még nem támogatott.

| Karakterkészlet | Karakterek száma |
|---------------|-----------------|
| <ul><li>0x0000009D (osc operációs rendszer parancs)</li><li>0x00000090 (DCS-eszköz vezérlő sztringje)</li><li>0x0000008F (ss3 Single SHIFT Three)</li><li>0x00000081 (nagy oktettes beállításkészlet)</li><li>0x0000007F (del delete)</li><li>0x0000008D (ri fordított soros csatorna)</li></ul> | 6 |
| 0x0000FDD0-0x0000FDEF (arab bemutató űrlapok – a) | 32 |
| 0x0000FFF0 – 0x0000FFFF (speciális) | 16 |
| <ul><li>0x0001FFFE-0x0001FFFF = 2 (nem karakter)</li><li>0x0002FFFE-0x0002FFFF = 2 (nem karakter)</li><li>0x0003FFFE-0x0003FFFF = 2 (nem karakter)</li><li>0x0004FFFE-0x0004FFFF = 2 (nem karakter)</li><li>0x0005FFFE-0x0005FFFF = 2 (nem karakter)</li><li>0x0006FFFE-0x0006FFFF = 2 (nem karakter)</li><li>0x0007FFFE-0x0007FFFF = 2 (nem karakter)</li><li>0x0008FFFE-0x0008FFFF = 2 (nem karakter)</li><li>0x0009FFFE-0x0009FFFF = 2 (nem karakter)</li><li>0x000AFFFE-0x000AFFFF = 2 (nem karakter)</li><li>0x000BFFFE-0x000BFFFF = 2 (nem karakter)</li><li>0x000CFFFE-0x000CFFFF = 2 (nem karakter)</li><li>0x000DFFFE-0x000DFFFF = 2 (nem karakter)</li><li>0x000EFFFE-0x000EFFFF = 2 (nem definiált)</li><li>0x000FFFFE-0x000FFFFF = 2 (kiegészítő magánjellegű felhasználási területek)</li></ul> | 30 |
| 0x0010FFFE, 0x0010FFFF | 2 |

### <a name="common-sync-errors"></a>Gyakori szinkronizálási hibák
<a id="-2147023673"></a>**A szinkronizálási munkamenet meg lett szakítva.**  

| | |
|-|-|
| **HRESULT** | 0x800704c7 |
| **HRESULT (decimális)** | -2147023673 | 
| **Hiba karakterlánca** | ERROR_CANCELLED |
| **Szervizelés szükséges** | Nem |

A szinkronizálási munkamenetek különböző okok miatt sikertelenek lehetnek, például a kiszolgáló újraindítása vagy frissítése, VSS-Pillanatképek stb. Bár ez a hiba úgy tűnik, hogy a követést igényli, nyugodtan figyelmen kívül hagyhatja ezt a hibát, ha több órán keresztül nem szűnik meg.

<a id="-2147012889"></a>**Nem hozhatók összefüggésbe a szolgáltatással létesített kapcsolatok.**    

| | |
|-|-|
| **HRESULT** | 0x80072ee7 |
| **HRESULT (decimális)** | -2147012889 | 
| **Hiba karakterlánca** | WININET_E_NAME_NOT_RESOLVED |
| **Szervizelés szükséges** | Igen |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134376372"></a>**A szolgáltatás szabályozza a felhasználói kérelmet.**  

| | |
|-|-|
| **HRESULT** | 0x80c8004c |
| **HRESULT (decimális)** | -2134376372 |
| **Hiba karakterlánca** | ECS_E_USER_REQUEST_THROTTLED |
| **Szervizelés szükséges** | Nem |

Nincs szükség beavatkozásra; a kiszolgáló megkísérli újra. Ha a hiba több órán keresztül is fennáll, hozzon létre egy támogatási kérést.

<a id="-2134364043"></a>**A szinkronizálás le van tiltva, amíg a változások észlelése befejeződött a visszaállítás után.**  

| | |
|-|-|
| **HRESULT** | 0x80c83075 |
| **HRESULT (decimális)** | -2134364043 |
| **Hiba karakterlánca** | ECS_E_SYNC_BLOCKED_ON_CHANGE_DETECTION_POST_RESTORE |
| **Szervizelés szükséges** | Nem |

Nincs szükség felhasználói műveletre. Ha egy fájl-vagy fájlmegosztás (Felhőbeli végpont) Azure Backup használatával lett visszaállítva, a rendszer letiltja a szinkronizálást, amíg a változások észlelése be nem fejeződik az Azure-fájlmegosztás esetében. Az észlelés azonnali futtatása a visszaállítás befejeződése után azonnal megtörténik, és az időtartam a fájlmegosztás fájljainak számától függ.

<a id="-2147216747"></a>**A szinkronizálás nem sikerült, mert a szinkronizálási adatbázis el lett távolítva.**  

| | |
|-|-|
| **HRESULT** | 0x80041295 |
| **HRESULT (decimális)** | – 2147216747 |
| **Hiba karakterlánca** | SYNC_E_METADATA_INVALID_OPERATION |
| **Szervizelés szükséges** | Nem |

Ez a hiba általában akkor fordul elő, amikor egy biztonságimásolat-készítő alkalmazás létrehoz egy VSS-pillanatképet, és a szinkronizálási adatbázis el van távolítva. Ha a hiba több órán keresztül is fennáll, hozzon létre egy támogatási kérést.

<a id="-2134364065"></a>**A szinkronizálás nem fér hozzá a Felhőbeli végponton megadott Azure-fájlmegosztás eléréséhez.**  

| | |
|-|-|
| **HRESULT** | 0x80c8305f |
| **HRESULT (decimális)** | -2134364065 |
| **Hiba karakterlánca** | ECS_E_EXTERNAL_STORAGE_ACCOUNT_AUTHORIZATION_FAILED |
| **Szervizelés szükséges** | Igen |

Ez a hiba azért fordul elő, mert az Azure File Sync ügynök nem fér hozzá az Azure-fájlmegosztás eléréséhez, mert az Azure-fájlmegosztás vagy az azt futtató Storage-fiók már nem létezik. Ezt a hibát a következő lépésekkel végezheti el:

1. [Ellenőrizze, hogy létezik-e a Storage-fiók.](#troubleshoot-storage-account)
2. [Győződjön meg arról, hogy az Azure-fájlmegosztás létezik.](#troubleshoot-azure-file-share)
3. [Győződjön meg arról, Azure File Sync hozzáfér a Storage-fiókhoz.](#troubleshoot-rbac)
4. [Ellenőrizze, hogy a Storage-fiók tűzfal-és virtuális hálózati beállításai megfelelően vannak-e konfigurálva (ha engedélyezve vannak)](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134364064"></a><a id="cannot-resolve-storage"></a>**A használt Storage-fiók neve nem oldható fel.**  

| | |
|-|-|
| **HRESULT** | 0x80C83060 |
| **HRESULT (decimális)** | -2134364064 |
| **Hiba karakterlánca** | ECS_E_STORAGE_ACCOUNT_NAME_UNRESOLVED |
| **Szervizelés szükséges** | Igen |

1. Győződjön meg arról, hogy fel tudja oldani a tárolási DNS-nevet a kiszolgálóról.

    ```powershell
    Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 443
    ```
2. [Ellenőrizze, hogy létezik-e a Storage-fiók.](#troubleshoot-storage-account)
3. [Ellenőrizze, hogy a Storage-fiók tűzfal-és virtuális hálózati beállításai megfelelően vannak-e konfigurálva (ha engedélyezve vannak)](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-1906441138"></a>**A szinkronizálás nem sikerült, mert a szinkronizálási adatbázissal kapcsolatos probléma merült fel.**  

| | |
|-|-|
| **HRESULT** | 0x8e5e044e |
| **HRESULT (decimális)** | -1906441138 |
| **Hiba karakterlánca** | JET_errWriteConflict |
| **Szervizelés szükséges** | Igen |

Ez a hiba akkor fordul elő, ha a Azure File Sync által használt belső adatbázissal kapcsolatos probléma merül fel. A probléma előfordulásakor hozzon létre egy támogatási kérést, és felvesszük Önnel a kapcsolatot, hogy segítsen a probléma megoldásában.

<a id="-2134364053"></a>**A kiszolgálón telepített Azure File Sync ügynök verziója nem támogatott.**  

| | |
|-|-|
| **HRESULT** | 0x80C8306B |
| **HRESULT (decimális)** | -2134364053 |
| **Hiba karakterlánca** | ECS_E_AGENT_VERSION_BLOCKED |
| **Szervizelés szükséges** | Igen |

Ez a hiba akkor fordul elő, ha a kiszolgálón telepített Azure File Sync ügynök verziója nem támogatott. A probléma megoldásához [frissítsen]( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#upgrade-paths) egy [támogatott ügynök]( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#supported-versions)-verzióra.

<a id="-2134351810"></a>**Elérte az Azure fájlmegosztás tárolási korlátját.**  

| | |
|-|-|
| **HRESULT** | 0x80c8603e |
| **HRESULT (decimális)** | -2134351810 |
| **Hiba karakterlánca** | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED |
| **Szervizelés szükséges** | Igen |

Ez a hiba akkor fordul elő, ha elérte az Azure fájlmegosztás tárolási korlátját, ami akkor fordulhat elő, ha egy Azure-fájlmegosztás kvótáját alkalmazza, vagy ha a használat meghaladja az Azure-fájlmegosztás korlátait. További információkért tekintse meg az [Azure-fájlmegosztás aktuális korlátait](storage-files-scale-targets.md).

1. Navigáljon a szinkronizálási csoportra a Storage Sync szolgáltatáson belül.
2. Válassza ki a Felhőbeli végpontot a szinkronizálási csoporton belül.
3. Jegyezze fel az Azure-fájlmegosztás nevét a megnyitott ablaktáblán.
4. Válassza ki a társított Storage-fiókot. Ha a hivatkozás nem sikerül, a hivatkozott Storage-fiók el lett távolítva.

    ![Képernyőkép a Felhőbeli végpont részletei panelről, amely a Storage-fiókra mutató hivatkozást mutat be.](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

5. A fájlmegosztás listájának megtekintéséhez válassza a **fájlok** lehetőséget.
6. Kattintson a sor végén található három pontra a Felhőbeli végpont által hivatkozott Azure-fájlmegosztás esetében.
7. Ellenőrizze, hogy a **használat** a **kvóta**alá esik-e. Megjegyzés: Ha meg van adva egy alternatív kvóta, akkor a kvóta az [Azure-fájlmegosztás maximális méretével egyezik meg](storage-files-scale-targets.md).

    ![Képernyőkép az Azure-fájlmegosztás tulajdonságairól.](media/storage-sync-files-troubleshoot/file-share-limit-reached-1.png)

Ha a megosztás megtelt, és nincs megadva kvóta, a probléma megoldásának egyik lehetséges módja az, hogy az aktuális kiszolgálói végpont összes almappáját saját, különálló szinkronizálási csoportokba tartozó saját kiszolgálói végpontba hozza. Így minden egyes almappa szinkronizálva lesz az egyes Azure-fájlmegosztás használatával.

<a id="-2134351824"></a>**Az Azure-fájlmegosztás nem található.**  

| | |
|-|-|
| **HRESULT** | 0x80c86030 |
| **HRESULT (decimális)** | -2134351824 |
| **Hiba karakterlánca** | ECS_E_AZURE_FILE_SHARE_NOT_FOUND |
| **Szervizelés szükséges** | Igen |

Ez a hiba akkor fordul elő, ha az Azure-fájlmegosztás nem érhető el. A hibák megoldása:

1. [Ellenőrizze, hogy létezik-e a Storage-fiók.](#troubleshoot-storage-account)
2. [Győződjön meg arról, hogy az Azure-fájlmegosztás létezik.](#troubleshoot-azure-file-share)

Ha az Azure-fájlmegosztás törölve lett, létre kell hoznia egy új fájlmegosztást, majd újra létre kell hoznia a szinkronizálási csoportot. 

<a id="-2134364042"></a>**A szinkronizálás szünetel, miközben az Azure-előfizetés fel van függesztve.**  

| | |
|-|-|
| **HRESULT** | 0x80C83076 |
| **HRESULT (decimális)** | -2134364042 |
| **Hiba karakterlánca** | ECS_E_SYNC_BLOCKED_ON_SUSPENDED_SUBSCRIPTION |
| **Szervizelés szükséges** | Igen |

Ez a hiba akkor fordul elő, ha az Azure-előfizetés fel van függesztve. Az Azure-előfizetés visszaállításakor a rendszer újraengedélyezi a szinkronizálást. Lásd: [Miért van letiltva az Azure-előfizetés, és hogyan lehet újraaktiválni?](../../billing/billing-subscription-become-disable.md) további információ.

<a id="-2134364052"></a>**A Storage-fiókhoz tűzfal vagy virtuális hálózat van konfigurálva.**  

| | |
|-|-|
| **HRESULT** | 0x80c8306c |
| **HRESULT (decimális)** | -2134364052 |
| **Hiba karakterlánca** | ECS_E_MGMT_STORAGEACLSNOTSUPPORTED |
| **Szervizelés szükséges** | Igen |

Ez a hiba akkor fordul elő, ha az Azure-fájlmegosztás tárolási fiók tűzfala miatt nem érhető el, vagy mert a Storage-fiók egy virtuális hálózathoz tartozik. Ellenőrizze, hogy a Storage-fiók tűzfal-és virtuális hálózati beállításai megfelelően vannak-e konfigurálva. További információ: a [tűzfal és a virtuális hálózat beállításainak konfigurálása](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings). 

<a id="-2134375911"></a>**A szinkronizálás nem sikerült, mert a szinkronizálási adatbázissal kapcsolatos probléma merült fel.**  

| | |
|-|-|
| **HRESULT** | 0x80c80219 |
| **HRESULT (decimális)** | -2134375911 |
| **Hiba karakterlánca** | ECS_E_SYNC_METADATA_WRITE_LOCK_TIMEOUT |
| **Szervizelés szükséges** | Nem |

Ez a hiba általában feloldja magát, és a következő esetekben fordulhat elő:

* Nagy számú fájl változik a szinkronizálási csoport kiszolgálóin.
* Számos hiba az egyes fájlokon és címtárakon.

Ha a hiba hosszabb ideig továbbra is fennáll, hozzon létre egy támogatási kérést, és felvesszük Önnel a kapcsolatot, hogy segítsen a probléma megoldásában.

<a id="-2146762487"></a>**A kiszolgáló nem tudott biztonságos kapcsolatot létesíteni. A felhőalapú szolgáltatás nem várt tanúsítványt kapott.**  

| | |
|-|-|
| **HRESULT** | 0x800b0109 |
| **HRESULT (decimális)** | -2146762487 |
| **Hiba karakterlánca** | CERT_E_UNTRUSTEDROOT |
| **Szervizelés szükséges** | Igen |

Ez a hiba akkor fordulhat elő, ha a szervezete SSL-leállítási proxyt használ, vagy ha egy rosszindulatú entitás elfogja a kiszolgáló és a Azure File Sync szolgáltatás közötti adatforgalmat. Ha biztos abban, hogy ez várható (mivel a szervezet SSL-leállítási proxyt használ), a tanúsítvány-ellenőrzés kihagyható a beállításjegyzék felülbírálásával.

1. Hozza létre a SkipVerifyingPinnedRootCertificate beállításazonosító értékét.

    ```powershell
    New-ItemProperty -Path HKLM:\SOFTWARE\Microsoft\Azure\StorageSync -Name SkipVerifyingPinnedRootCertificate -PropertyType DWORD -Value 1
    ```

2. Indítsa újra a szinkronizálási szolgáltatást a regisztrált kiszolgálón.

    ```powershell
    Restart-Service -Name FileSyncSvc -Force
    ```

Ha beállítja ezt a beállításazonosítót, az Azure File Sync-ügynök minden helyileg megbízhatónak minősülő SSL-tanúsítványt elfogad a kiszolgáló és a felhőszolgáltatás közötti adatátvitel során.

<a id="-2147012894"></a>**Nem hozhatók összefüggésbe a szolgáltatással létesített kapcsolatok.**  

| | |
|-|-|
| **HRESULT** | 0x80072ee2 |
| **HRESULT (decimális)** | -2147012894 |
| **Hiba karakterlánca** | WININET_E_TIMEOUT |
| **Szervizelés szükséges** | Igen |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134375680"></a>**A szinkronizálás egy hitelesítéssel kapcsolatos probléma miatt nem sikerült.**  

| | |
|-|-|
| **HRESULT** | 0x80c80300 |
| **HRESULT (decimális)** | -2134375680 |
| **Hiba karakterlánca** | ECS_E_SERVER_CREDENTIAL_NEEDED |
| **Szervizelés szükséges** | Igen |

Ez a hiba általában azért fordul elő, mert a kiszolgáló ideje helytelen. Ha a kiszolgáló virtuális gépen fut, ellenőrizze, hogy a gazdagépen megadott idő helyes-e.

<a id="-2134364040"></a>**A szinkronizálás a tanúsítvány lejárata miatt nem sikerült.**  

| | |
|-|-|
| **HRESULT** | 0x80c83078 |
| **HRESULT (decimális)** | – 2134364040 |
| **Hiba karakterlánca** | ECS_E_AUTH_SRV_CERT_EXPIRED |
| **Szervizelés szükséges** | Igen |

Ez a hiba azért fordul elő, mert a hitelesítéshez használt tanúsítvány lejárt.

A tanúsítvány érvényességének ellenőrzéséhez hajtsa végre a következő lépéseket:  
1. Nyissa meg a tanúsítványok MMC beépülő modult, válassza a számítógépfiók lehetőséget, és navigáljon a tanúsítványok (helyi számítógép) \Personal\Certificates.
2. Ellenőrizze, hogy az ügyfél-hitelesítési tanúsítvány lejárt-e.

Ha az ügyfél-hitelesítési tanúsítvány lejárt, hajtsa végre a következő lépéseket a probléma megoldásához:

1. Ellenőrizze, hogy telepítve van-e Azure File Sync ügynök 4.0.1.0 vagy újabb verziója.
2. Futtassa a következő PowerShell-parancsot a kiszolgálón:

    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```

<a id="-2134375896"></a>**A szinkronizálás nem sikerült, mert a hitelesítési tanúsítvány nem található.**  

| | |
|-|-|
| **HRESULT** | 0x80c80228 |
| **HRESULT (decimális)** | – 2134375896 |
| **Hiba karakterlánca** | ECS_E_AUTH_SRV_CERT_NOT_FOUND |
| **Szervizelés szükséges** | Igen |

Ez a hiba azért fordul elő, mert a hitelesítéshez használt tanúsítvány nem található.

A probléma megoldásához végezze el az alábbi lépéseket:

1. Ellenőrizze, hogy telepítve van-e Azure File Sync ügynök 4.0.1.0 vagy újabb verziója.
2. Futtassa a következő PowerShell-parancsot a kiszolgálón:

    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```

<a id="-2134364039"></a>**A szinkronizálás nem sikerült, mert a hitelesítési identitás nem található.**  

| | |
|-|-|
| **HRESULT** | 0x80c83079 |
| **HRESULT (decimális)** | – 2134364039 |
| **Hiba karakterlánca** | ECS_E_AUTH_IDENTITY_NOT_FOUND |
| **Szervizelés szükséges** | Igen |

Ez a hiba azért fordul elő, mert a kiszolgálói végpont törlése sikertelen volt, és a végpont már részben törölt állapotban van. A probléma megoldásához próbálkozzon újra a kiszolgálói végpont törlésével.

<a id="-1906441711"></a><a id="-2134375654"></a><a id="doesnt-have-enough-free-space"></a>**Az a kötet, amelyben a kiszolgálói végpont található, kevés a szabad lemezterület.**  

| | |
|-|-|
| **HRESULT** | 0x8e5e0211 |
| **HRESULT (decimális)** | -1906441711 |
| **Hiba karakterlánca** | JET_errLogDiskFull |
| **Szervizelés szükséges** | Igen |
| | |
| **HRESULT** | 0x80c8031a |
| **HRESULT (decimális)** | -2134375654 |
| **Hiba karakterlánca** | ECS_E_NOT_ENOUGH_LOCAL_STORAGE |
| **Szervizelés szükséges** | Igen |

Ez a hiba azért fordul elő, mert a kötet megtelt. Ez a hiba általában azért fordul elő, mert a kiszolgálói végponton kívüli fájlok lemezterületet használnak a köteten. Szabadítson fel lemezterületet a köteten további kiszolgálói végpontok hozzáadásával, a fájlok másik kötetre való áthelyezésével vagy a kiszolgálói végpontot tartalmazó kötet méretének növelésével.

<a id="-2134364145"></a><a id="replica-not-ready"></a>**A szolgáltatás még nem áll készen az ezzel a kiszolgálói végponttal való szinkronizálásra.**  

| | |
|-|-|
| **HRESULT** | 0x80c8300f |
| **HRESULT (decimális)** | -2134364145 |
| **Hiba karakterlánca** | ECS_E_REPLICA_NOT_READY |
| **Szervizelés szükséges** | Nem |

Ez a hiba azért fordul elő, mert az Azure-fájlmegosztás közvetlenül módosul, és az észlelés változása folyamatban van. A változások észlelésének befejeződése után a szinkronizálás megkezdődik.

[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="-2134375877"></a><a id="-2134375908"></a><a id="-2134375853"></a>**A szinkronizálás nem sikerült, mert számos egyéni fájllal kapcsolatos probléma merült fel.**  

| | |
|-|-|
| **HRESULT** | 0x80c8023b |
| **HRESULT (decimális)** | -2134364145 |
| **Hiba karakterlánca** | ECS_E_SYNC_METADATA_KNOWLEDGE_SOFT_LIMIT_REACHED |
| **Szervizelés szükséges** | Igen |
| | |
| **HRESULT** | 0x80c8021c |
| **HRESULT (decimális)** | -2134375908 |
| **Hiba karakterlánca** | ECS_E_SYNC_METADATA_KNOWLEDGE_LIMIT_REACHED |
| **Szervizelés szükséges** | Igen |
| | |
| **HRESULT** | 0x80c80253 |
| **HRESULT (decimális)** | -2134375853 |
| **Hiba karakterlánca** | ECS_E_TOO_MANY_PER_ITEM_ERRORS |
| **Szervizelés szükséges** | Igen |

A szinkronizálási munkamenetek sikertelenek lehetnek, ha több fájl szinkronizálási hibája van. <!-- To troubleshoot this state, see [Troubleshooting per file/directory sync errors]().-->

> [!NOTE]
> Azure File Sync létrehoz egy ideiglenes VSS-pillanatképet naponta egyszer a kiszolgálón a megnyitott leíróval rendelkező fájlok szinkronizálásához.

<a id="-2134376423"></a>**A szinkronizálás nem sikerült, mert hiba történt a kiszolgálói végpont elérési útján.**  

| | |
|-|-|
| **HRESULT** | 0x80c80019 |
| **HRESULT (decimális)** | -2134376423 |
| **Hiba karakterlánca** | ECS_E_SYNC_INVALID_PATH |
| **Szervizelés szükséges** | Igen |

Győződjön meg arról, hogy az elérési út létezik, helyi NTFS-köteten található, és nem újraelemzési pont vagy meglévő kiszolgálói végpont.

<a id="-2134375817"></a>**A szinkronizálás nem sikerült, mert a szűrő illesztőprogramjának verziója nem kompatibilis az ügynök verziójával**  

| | |
|-|-|
| **HRESULT** | 0x80C80277 |
| **HRESULT (decimális)** | -2134375817 |
| **Hiba karakterlánca** | ECS_E_INCOMPATIBLE_FILTER_VERSION |
| **Szervizelés szükséges** | Igen |

Ez a hiba azért fordul elő, mert a felhőalapú StorageSync. sys fájl betöltött verziója nem kompatibilis a Storage Sync Agent (FileSyncSvc) szolgáltatással. Ha a Azure File Sync ügynök frissítése megtörtént, indítsa újra a kiszolgálót a telepítés befejezéséhez. Ha a hiba továbbra is fennáll, távolítsa el az ügynököt, indítsa újra a kiszolgálót, és telepítse újra a Azure File Sync ügynököt.

<a id="-2134376373"></a>**A szolgáltatás jelenleg nem érhető el.**  

| | |
|-|-|
| **HRESULT** | 0x80c8004b |
| **HRESULT (decimális)** | -2134376373 |
| **Hiba karakterlánca** | ECS_E_SERVICE_UNAVAILABLE |
| **Szervizelés szükséges** | Nem |

Ez a hiba azért fordul elő, mert a Azure File Sync szolgáltatás nem érhető el. Ez a hiba automatikusan feloldásra kerül, amikor a Azure File Sync szolgáltatás újra elérhetővé válik.

<a id="-2146233088"></a>**Kivétel miatt nem sikerült a szinkronizálás.**  

| | |
|-|-|
| **HRESULT** | 0x80131500 |
| **HRESULT (decimális)** | -2146233088 |
| **Hiba karakterlánca** | COR_E_EXCEPTION |
| **Szervizelés szükséges** | Nem |

Ez a hiba azért fordul elő, mert kivétel miatt nem sikerült a szinkronizálás. Ha a hiba több órán keresztül is fennáll, hozzon létre egy támogatási kérést.

<a id="-2134364045"></a>**A szinkronizálás nem sikerült, mert a Storage-fiók feladatátvétele egy másik régióba történt.**  

| | |
|-|-|
| **HRESULT** | 0x80c83073 |
| **HRESULT (decimális)** | – 2134364045 |
| **Hiba karakterlánca** | ECS_E_STORAGE_ACCOUNT_FAILED_OVER |
| **Szervizelés szükséges** | Igen |

Ez a hiba azért fordul elő, mert a Storage-fiók feladatátvétele egy másik régióba történt. A Azure File Sync nem támogatja a Storage-fiók feladatátvételi funkcióját. A Azure File Syncban Felhőbeli végpontként használt Azure-fájlmegosztást tartalmazó Storage-fiókok feladatátvétele nem lehetséges. Ennek hatására a szinkronizálás leáll, és az újonnan rétegű fájlok esetében váratlan adatvesztést okozhat. A probléma megoldásához helyezze át a Storage-fiókot az elsődleges régióba.

<a id="-2134375922"></a>**A szinkronizálás nem sikerült, mert átmeneti probléma történt a szinkronizálási adatbázissal.**  

| | |
|-|-|
| **HRESULT** | 0x80c8020e |
| **HRESULT (decimális)** | -2134375922 |
| **Hiba karakterlánca** | ECS_E_SYNC_METADATA_WRITE_LEASE_LOST |
| **Szervizelés szükséges** | Nem |

Ez a hiba a szinkronizálási adatbázis belső problémája miatt fordul elő. Ez a hiba automatikusan feloldásra kerül, amikor a Azure File Sync a szinkronizálás újrapróbálkozásakor. Ha a hiba hosszabb időt vesz igénybe, hozzon létre egy támogatási kérést, és felvesszük Önnel a kapcsolatot, hogy segítsen a probléma megoldásában.

<a id="-2134364024"></a>**A szinkronizálás a Azure Active Directory bérlő változása miatt nem sikerült**  

| | |
|-|-|
| **HRESULT** | 0x80c83088 |
| **HRESULT (decimális)** | – 2134364024 | 
| **Hiba karakterlánca** | ECS_E_INVALID_AAD_TENANT |
| **Szervizelés szükséges** | Igen |

Ez a hiba azért fordul elő, mert Azure File Sync jelenleg nem támogatja az előfizetés másik Azure Active Directory bérlőre való áthelyezését.
 
A probléma megoldásához hajtsa végre az alábbi lehetőségek egyikét:

- **1. lehetőség (ajánlott)** : Az előfizetés visszahelyezése az eredeti Azure Active Directory bérlőre
- **2. lehetőség**: Törölje, majd hozza létre újra az aktuális szinkronizálási csoportot. Ha a felhő-rétegek engedélyezve lettek a kiszolgálói végponton, törölje a szinkronizálási csoportot, majd hajtsa végre a [Felhőbeli rétegek című szakaszban]( https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) ismertetett lépéseket, és távolítsa el az árva rétegekből származó fájlokat a szinkronizálási csoportok újbóli létrehozása előtt. 

<a id="-2134364010"></a>**A szinkronizálás nem sikerült, mert a tűzfal és a virtuális hálózati kivétel nincs konfigurálva**  

| | |
|-|-|
| **HRESULT** | 0x80c83096 |
| **HRESULT (decimális)** | – 2134364010 | 
| **Hiba karakterlánca** | ECS_E_MGMT_STORAGEACLSBYPASSNOTSET |
| **Szervizelés szükséges** | Igen |

Ez a hiba akkor fordul elő, ha a tűzfal és a virtuális hálózat beállításai engedélyezve vannak a Storage-fiókban, és a "megbízható Microsoft-szolgáltatások hozzáférésének engedélyezése ehhez a Storage-fiókhoz" kivétel nincs bejelölve. A probléma megoldásához kövesse a telepítési útmutató [tűzfal-és virtuális hálózati beállításainak konfigurálása](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings) című szakaszának lépéseit.

<a id="-2147024891"></a>**A szinkronizálás nem sikerült, mert a rendszerkötet-információs mappa engedélyei helytelenek.**  

| | |
|-|-|
| **HRESULT** | 0x80070005 |
| **HRESULT (decimális)** | -2147024891 |
| **Hiba karakterlánca** | ERROR_ACCESS_DENIED |
| **Szervizelés szükséges** | Igen |

Ez a hiba akkor fordulhat elő, ha az NT AUTHORITY\SYSTEM-fióknak nincs engedélye a rendszerkötet információs mappájához azon a köteten, ahol a kiszolgálói végpont található. Vegye figyelembe, hogy ha az egyes fájlok nem fognak szinkronizálni a ERROR_ACCESS_DENIED-mel, hajtsa végre a [fájl/címtár-szinkronizálási hibák](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#troubleshooting-per-filedirectory-sync-errors) című szakaszban ismertetett lépéseket.

A probléma megoldásához végezze el az alábbi lépéseket:

1. Töltse le a [PsExec](https://docs.microsoft.com/sysinternals/downloads/psexec) eszközt.
2. Futtassa a következő parancsot egy rendszergazda jogú parancssorból egy parancssor elindításához a rendszerfiók használatával: **PsExec. exe-i-s-d cmd** 
3. A rendszerfiókban futtatott parancssorból futtassa a következő parancsot annak megerősítéséhez, hogy az NT AUTHORITY\SYSTEM-fiók nem rendelkezik hozzáféréssel a rendszerkötet információs mappájához: **cacls "meghajtóbetűjel: \ rendszerkötet információi"/T/c**
4. Ha az NT AUTHORITY\SYSTEM-fiók nem rendelkezik hozzáféréssel a rendszerkötet információs mappájához, futtassa a következő parancsot: **cacls "meghajtóbetűjel: \ rendszerkötet információi"/T/E/g Administrators "NT AUTHORITY\SYSTEM: F"**
    - Ha a #4 a hozzáférés megtagadásával meghiúsul, futtassa a következő parancsot a rendszerkötet információs mappájának tulajdonjogának átvételéhez, majd ismételje meg a lépést #4: **takeown/A/R/f "meghajtóbetűjel: \ rendszerkötet információi"**

<a id="-2134375810"></a>**A szinkronizálás nem sikerült, mert az Azure-fájlmegosztás törölve lett, és újra lett létrehozva.**  

| | |
|-|-|
| **HRESULT** | 0x80c8027e |
| **HRESULT (decimális)** | – 2134375810 |
| **Hiba karakterlánca** | ECS_E_SYNC_REPLICA_ROOT_CHANGED |
| **Szervizelés szükséges** | Igen |

Ez a hiba azért fordul elő, mert Azure File Sync nem támogatja az Azure-fájlmegosztás törlését és újbóli létrehozását ugyanabban a szinkronizálási csoportban. 

A probléma megoldásához törölje és hozza létre újra a szinkronizálási csoportot a következő lépések végrehajtásával:

1. Törölje az összes kiszolgálói végpontot a szinkronizálási csoportban.
2. Törölje a Felhőbeli végpontot. 
3. Törölje a szinkronizálási csoportot.
4. Ha a felhő-rétegek engedélyezve lettek egy kiszolgálói végponton, törölje az árva rétegű fájlokat a kiszolgálón úgy, hogy a többszakaszos fájlokban ismertetett lépések végrehajtásával a kiszolgálói [végpont törlése után nem érhető](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) el a kiszolgálón.
5. Hozza létre újra a szinkronizálási csoportot.

### <a name="common-troubleshooting-steps"></a>Gyakori hibaelhárítási lépések
<a id="troubleshoot-storage-account"></a>**Ellenőrizze, hogy létezik-e a Storage-fiók.**  
# <a name="portaltabazure-portal"></a>[Portál](#tab/azure-portal)
1. Navigáljon a szinkronizálási csoportra a Storage Sync szolgáltatáson belül.
2. Válassza ki a Felhőbeli végpontot a szinkronizálási csoporton belül.
3. Jegyezze fel az Azure-fájlmegosztás nevét a megnyitott ablaktáblán.
4. Válassza ki a társított Storage-fiókot. Ha a hivatkozás nem sikerül, a hivatkozott Storage-fiók el lett távolítva.
    ![Képernyőkép a Felhőbeli végpont részletei panelről, amely a Storage-fiókra mutató hivatkozást mutat be.](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
# Variables for you to populate based on your configuration
$region = "<Az_Region>"
$resourceGroup = "<RG_Name>"
$syncService = "<storage-sync-service>"
$syncGroup = "<sync-group>"

# Log into the Azure account
Connect-AzAccount

# Check to ensure Azure File Sync is available in the selected Azure
# region.
$regions = [System.String[]]@()
Get-AzLocation | ForEach-Object { 
    if ($_.Providers -contains "Microsoft.StorageSync") { 
        $regions += $_.Location 
    } 
}

if ($regions -notcontains $region) {
    throw [System.Exception]::new("Azure File Sync is either not available in the " + `
        " selected Azure Region or the region is mistyped.")
}

# Check to ensure resource group exists
$resourceGroups = [System.String[]]@()
Get-AzResourceGroup | ForEach-Object { 
    $resourceGroups += $_.ResourceGroupName 
}

if ($resourceGroups -notcontains $resourceGroup) {
    throw [System.Exception]::new("The provided resource group $resourceGroup does not exist.")
}

# Check to make sure the provided Storage Sync Service
# exists.
$syncServices = [System.String[]]@()

Get-AzStorageSyncService -ResourceGroupName $resourceGroup | ForEach-Object {
    $syncServices += $_.StorageSyncServiceName
}

if ($syncServices -notcontains $syncService) {
    throw [System.Exception]::new("The provided Storage Sync Service $syncService does not exist.")
}

# Check to make sure the provided Sync Group exists
$syncGroups = [System.String[]]@()

Get-AzStorageSyncGroup -ResourceGroupName $resourceGroup -StorageSyncServiceName $syncService | ForEach-Object {
    $syncGroups += $_.SyncGroupName
}

if ($syncGroups -notcontains $syncGroup) {
    throw [System.Exception]::new("The provided sync group $syncGroup does not exist.")
}

# Get reference to cloud endpoint
$cloudEndpoint = Get-AzStorageSyncCloudEndpoint `
    -ResourceGroupName $resourceGroup `
    -StorageSyncServiceName $syncService `
    -SyncGroupName $syncGroup

# Get reference to storage account
$storageAccount = Get-AzStorageAccount | Where-Object { 
    $_.Id -eq $cloudEndpoint.StorageAccountResourceId
}

if ($storageAccount -eq $null) {
    throw [System.Exception]::new("The storage account referenced in the cloud endpoint does not exist.")
}
```
---

<a id="troubleshoot-azure-file-share"></a>**Győződjön meg arról, hogy az Azure-fájlmegosztás létezik.**  
# <a name="portaltabazure-portal"></a>[Portál](#tab/azure-portal)
1. Kattintson a bal oldali tartalomjegyzék **Áttekintés** elemére, és térjen vissza a fő Storage-fiók lapra.
2. A fájlmegosztás listájának megtekintéséhez válassza a **fájlok** lehetőséget.
3. Ellenőrizze, hogy a felhőalapú végpont által hivatkozott fájlmegosztás megjelenik-e a fájlmegosztás listájában (ezt a fenti 1. lépésben kell megemlíteni).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
$fileShare = Get-AzStorageShare -Context $storageAccount.Context | Where-Object {
    $_.Name -eq $cloudEndpoint.AzureFileShareName -and
    $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    throw [System.Exception]::new("The Azure file share referenced by the cloud endpoint does not exist")
}
```
---

<a id="troubleshoot-rbac"></a>**Győződjön meg arról, Azure File Sync hozzáfér a Storage-fiókhoz.**  
# <a name="portaltabazure-portal"></a>[Portál](#tab/azure-portal)
1. Kattintson a **hozzáférés-vezérlés (iam)** elemre a bal oldali tartalomjegyzékben.
1. Kattintson a **szerepkör** -hozzárendelések lapra a Storage-fiókhoz hozzáférő felhasználók és alkalmazások (*egyszerű szolgáltatásnév*) listázásához.
1. Ellenőrizze, hogy a **hibrid file Sync szolgáltatás** megjelenik-e a listában az **olvasó és** az adatelérési szerepkörrel. 

    ![Képernyőkép a hibrid File Sync szolgáltatásnév szolgáltatásról a Storage-fiók hozzáférés-vezérlés lapján](media/storage-sync-files-troubleshoot/file-share-inaccessible-3.png)

    Ha a **hibrid file Sync szolgáltatás** nem jelenik meg a listában, hajtsa végre a következő lépéseket:

    - Kattintson a **Hozzáadás**lehetőségre.
    - A **szerepkör** mezőben válassza ki az **olvasó és az adathozzáférés**lehetőséget.
    - A **kiválasztás** mezőbe írja be a **Hybrid file Sync Service**elemet, válassza ki a szerepkört, majd kattintson a **Mentés**gombra.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell    
$role = Get-AzRoleAssignment -Scope $storageAccount.Id | Where-Object { $_.DisplayName -eq "Hybrid File Sync Service" }

if ($role -eq $null) {
    throw [System.Exception]::new("The storage account does not have the Azure File Sync " + `
                "service principal authorized to access the data within the " + ` 
                "referenced Azure file share.")
}
```
---

### <a name="how-do-i-prevent-users-from-creating-files-containing-unsupported-characters-on-the-server"></a>Hogyan meggátolja, hogy a felhasználók a nem támogatott karaktereket tartalmazó fájlokat hozzanak létre a kiszolgálón?
A Fájlkiszolgálói [erőforrás-kezelő (FSRM)](https://docs.microsoft.com/windows-server/storage/fsrm/file-screening-management) fájlszűrők használatával letilthatja a nevükben nem támogatott karaktereket tartalmazó fájlokat a kiszolgálón. Ehhez a PowerShellt kell használnia, mivel a nem támogatott karakterek többsége nem nyomtatható, ezért először a hexadecimális ábrázolást kell elvégeznie.

Először hozzon létre egy FSRM-fájlt a [New-FsrmFileGroup parancsmag](https://docs.microsoft.com/powershell/module/fileserverresourcemanager/new-fsrmfilegroup)használatával. Ez a példa azt a csoportot határozza meg, amely a nem támogatott karakterek közül csak kettőt tartalmaz, de a fájlokban a szükségesnél több karaktert is megadhat.

```powershell
New-FsrmFileGroup -Name "Unsupported characters" -IncludePattern @(("*"+[char]0x00000090+"*"),("*"+[char]0x0000008F+"*"))
```

Miután meghatározta a FSRM, létrehozhat egy FSRM-fájlt a New-FsrmFileScreen parancsmag használatával.

```powershell
New-FsrmFileScreen -Path "E:\AFSdataset" -Description "Filter unsupported characters" -IncludeGroup "Unsupported characters"
```

> [!Important]  
> Vegye figyelembe, hogy a fájlszűrők csak a Azure File Sync által nem támogatott karakterek létrehozásának blokkolására használhatók. Ha a fájlszűrők más helyzetekben is használatban vannak, a szinkronizálás folyamatosan megpróbálja letölteni a fájlokat az Azure-fájlmegosztás és a-kiszolgáló között, és a fájl képernyő miatt le lesz tiltva, ami magas adatforgalomhoz vezet. 

## <a name="cloud-tiering"></a>Felhőbeli rétegzés 
A Felhőbeli rétegek meghibásodásának két útvonala van:

- A fájlok sikertelenek lehetnek, ami azt jelenti, hogy a Azure File Sync sikertelen kísérletet tesz a fájl Azure Filesre való kiterjesztésére.
- A fájlok sikertelenek lehetnek, ami azt jelenti, hogy a Azure File Sync fájlrendszer-szűrő (StorageSync. sys) nem tudja letölteni az adatfájlokat, amikor egy felhasználó megpróbál hozzáférni egy többszintű fájlhoz.

A meghibásodások elérési útjának két fő osztálya fordulhat elő:

- Felhőbeli tárolási hibák
    - *Átmeneti tárolási szolgáltatás rendelkezésre állási problémái*. További információkért tekintse meg az [Azure Storage szolgáltatói szerződés (SLA)](https://azure.microsoft.com/support/legal/sla/storage/v1_2/)című témakört.
    - Az *Azure-fájlmegosztás nem érhető*el. Ez a hiba általában akkor fordul elő, ha törli az Azure-fájlmegosztást, ha a szinkronizálási csoportban továbbra is Felhőbeli végpont van.
    - Nem *elérhető Storage-fiók*. Ez a hiba általában akkor fordul elő, ha törli a Storage-fiókot, miközben az Azure-fájlmegosztás továbbra is egy szinkronizálási csoportban lévő felhőalapú végpont. 
- Kiszolgálói hibák 
  - *Azure file Sync fájlrendszer-szűrő (StorageSync. sys)* nincs betöltve. A többszintű/visszahívási kérelmekre való válaszadáshoz be kell tölteni a Azure File Sync fájlrendszer szűrőjét. A szűrő betöltése több okból is előfordulhat, de a leggyakoribb ok az, hogy a rendszergazda manuálisan távolítja el. A Azure File Sync megfelelő működéséhez mindig be kell tölteni a Azure File Sync fájlrendszer szűrőjét.
  - *Hiányzó, sérült vagy egyéb módon*megszakított újraelemzési pont. Az újraelemzési pont egy speciális adatstruktúra egy olyan fájlon, amely két részből áll:
    1. Egy újraelemzési címke, amely azt jelzi, hogy a Azure File Sync fájlrendszer-szűrőnek (StorageSync. sys) az operációs rendszernek kell valamilyen műveletet végrehajtania az IO-on a fájlra. 
    2. Újraelemzési információ, amely azt jelzi, hogy a fájlrendszer szűri a fájl URI-JÁT a társított Felhőbeli végponton (az Azure-fájlmegosztás). 
        
       Az újraelemzési pontok megsérülnek a leggyakoribb módon, ha a rendszergazda megpróbálja módosítani a címkét vagy az adatforrást. 
  - *Hálózati kapcsolódási problémák*. Egy fájl felkészítése vagy felidézése érdekében a kiszolgálónak internetkapcsolattal kell rendelkeznie.

A következő fejezetek azt mutatják be, hogyan lehet elhárítani a Felhőbeli rétegbeli problémákat, és megállapítani, hogy a probléma a Felhőbeli tárolási probléma vagy a kiszolgáló problémája.

### <a name="how-to-monitor-tiering-activity-on-a-server"></a>A többplatformos tevékenységek figyelése egy kiszolgálón  
A-kiszolgálón a rétegek közötti tevékenységek figyeléséhez használja a 9003, 9016 és 9029 AZONOSÍTÓJÚ eseményt a telemetria eseménynaplóban (a Eseménynapló alkalmazások és Services\Microsoft\FileSync\Agent területén található).

- A 9003-as AZONOSÍTÓJÚ esemény egy kiszolgálói végpont hibáinak eloszlását biztosítja. Például az összes hiba száma, ErrorCode stb. Vegye figyelembe, hogy a rendszer egy eseményt naplóz egy hibakód alapján.
- A 9016-es azonosítójú esemény a kötetek szellemképes eredményeit biztosítja. Például a szabad terület százalékos értéke, a munkamenetek által feltett fájlok száma, a fájlok száma nem sikerült, és így tovább.
- A 9029-as AZONOSÍTÓJÚ esemény a kiszolgálói végpontok szellemkép-információit biztosítja. Például a munkamenetben megkísérelt fájlok száma, a munkamenetben megjelenő fájlok száma, a már rétegű fájlok száma stb.

### <a name="how-to-monitor-recall-activity-on-a-server"></a>A visszahívás tevékenység figyelése egy kiszolgálón
A kiszolgálón található visszahívás-tevékenységek figyeléséhez használja a 9005, 9006, 9009 és 9059 azonosítójú eseményt a telemetria eseménynaplóban (az Eseménynapló alkalmazások és Services\Microsoft\FileSync\Agent területen).

- Az 9005-as AZONOSÍTÓJÚ esemény egy kiszolgálói végpontra vonatkozó visszahívás megbízhatóságot biztosít. Például az elért egyedi fájlok teljes száma, a sikertelen hozzáféréssel rendelkező egyedi fájlok száma stb.
- Az 9006-as AZONOSÍTÓJÚ esemény a kiszolgálói végpontok visszahívási hibáinak eloszlását biztosítja. Például: összes sikertelen kérelem, ErrorCode stb. Vegye figyelembe, hogy a rendszer egy eseményt naplóz egy hibakód alapján.
- Az 9009-as AZONOSÍTÓJÚ esemény egy kiszolgálói végpontra vonatkozó visszahívás-munkamenet-információkat biztosít. Például: DurationSeconds, CountFilesRecallSucceeded, CountFilesRecallFailed stb.
- Az 9059-as AZONOSÍTÓJÚ esemény egy kiszolgálói végpont alkalmazás-visszahívási eloszlását biztosítja. Például: ShareId, alkalmazásnév és TotalEgressNetworkBytes.

### <a name="how-to-troubleshoot-files-that-fail-to-tier"></a>A nem a szintet nem tartalmazó fájlok hibáinak megoldása
Ha a fájlok nem tudják felvenni a Azure Files:

1. A Eseménynaplóban tekintse át az alkalmazások és Services\Microsoft\FileSync\Agent. területen található telemetria, operatív és diagnosztikai eseménynaplókat. 
   1. Ellenőrizze, hogy a fájlok léteznek-e az Azure-fájlmegosztás alatt.

      > [!NOTE]
      > Egy fájlnak a lépcsőzetes megkezdése előtt szinkronizálva kell lennie egy Azure-fájlmegosztás használatával.

   2. Ellenőrizze, hogy a kiszolgáló rendelkezik-e internetkapcsolattal. 
   3. Ellenőrizze, hogy fut-e a Azure File Sync szűrő-illesztőprogramok (StorageSync. sys és StorageSyncGuard. sys):
       - Futtassa `fltmc`a parancsot egy rendszergazda jogú parancssorban. Győződjön meg arról, hogy a StorageSync. sys és a StorageSyncGuard. sys fájlrendszer-szűrő illesztőprogramjai vannak felsorolva.

> [!NOTE]
> Az 9003-as AZONOSÍTÓJÚ esemény óránként egyszer van naplózva a telemetria-eseménynaplóban, ha egy fájl nem rétegbeli (egy esemény naplózása hibakód). Az operatív és a diagnosztikai eseménynaplókat akkor kell használni, ha további információkra van szükség a probléma diagnosztizálásához.

### <a name="how-to-troubleshoot-files-that-fail-to-be-recalled"></a>A nem felhívható fájlok hibáinak megoldása  
Ha a fájlokat nem lehet visszahívni:
1. A Eseménynaplóban tekintse át az alkalmazások és Services\Microsoft\FileSync\Agent. területen található telemetria, operatív és diagnosztikai eseménynaplókat.
    1. Ellenőrizze, hogy a fájlok léteznek-e az Azure-fájlmegosztás alatt.
    2. Ellenőrizze, hogy a kiszolgáló rendelkezik-e internetkapcsolattal. 
    3. Nyissa meg a szolgáltatások MMC beépülő modult, és ellenőrizze, hogy fut-e a Storage Sync Agent (FileSyncSvc) szolgáltatás.
    4. Ellenőrizze, hogy fut-e a Azure File Sync szűrő-illesztőprogramok (StorageSync. sys és StorageSyncGuard. sys):
        - Futtassa `fltmc`a parancsot egy rendszergazda jogú parancssorban. Győződjön meg arról, hogy a StorageSync. sys és a StorageSyncGuard. sys fájlrendszer-szűrő illesztőprogramjai vannak felsorolva.

> [!NOTE]
> A rendszer óránként egyszer naplózza az 9006-es AZONOSÍTÓJÚ eseményt a telemetria-eseménynaplóban, ha egy fájl nem hívható vissza (a rendszer hibakódként egy eseményt naplóz). Az operatív és a diagnosztikai eseménynaplókat akkor kell használni, ha további információkra van szükség a probléma diagnosztizálásához.

### <a name="tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint"></a>A többplatformos fájlok nem érhetők el a kiszolgálón a kiszolgálói végpont törlése után
A kiszolgálón található többkötetes fájlok elérhetetlenné válnak, ha a fájlok nem lettek meghívva a kiszolgálói végpont törlése előtt.

Naplózott hibák, ha a lépcsőzetes fájlok nem érhetők el
- Fájl szinkronizálásakor a rendszer a-2147942467 (0x80070043-ERROR_BAD_NET_NAME) hibakódot naplózza a ItemResults-eseménynaplóban.
- Fájl visszahívásakor a rendszer a-2134376393 (0x80c80037-ECS_E_SYNC_SHARE_NOT_FOUND) hibakódot naplózza a RecallResults-eseménynaplóban.

Ha a következő feltételek teljesülnek, a rétegű fájlokhoz való hozzáférés visszaállítása lehetséges:
- Kiszolgálói végpont törölve az elmúlt 30 napban
- A Felhőbeli végpont nem lett törölve 
- A fájlmegosztás nem lett törölve
- A szinkronizálási csoport nem lett törölve

Ha a fenti feltételek teljesülnek, visszaállíthatja a kiszolgálón lévő fájlokhoz való hozzáférést úgy, hogy az ugyanazon a szinkronizálási csoportban lévő kiszolgálón található kiszolgáló-végpontot 30 napon belül újra létrehozza. 

Ha a fenti feltételek nem teljesülnek, a hozzáférés visszaállítása nem lehetséges, mert a kiszolgálón található, többkötetes fájlok már elárvultak. Az árva rétegű fájlok eltávolításához kövesse az alábbi utasításokat.

**Megjegyzések**
- Ha a többhelyes fájlok nem érhetők el a kiszolgálón, a teljes fájlnak továbbra is elérhetőnek kell lennie, ha közvetlenül az Azure-fájlmegosztást éri el.
- Az árva rétegű fájlok jövőbeli megakadályozása érdekében kövesse a [kiszolgálói végpont eltávolítása](https://docs.microsoft.com/azure/storage/files/storage-sync-files-server-endpoint#remove-a-server-endpoint) a kiszolgálói végpontok törlésekor című cikkben ismertetett lépéseket.

<a id="get-orphaned"></a>**Az árva rétegű fájlok listájának beolvasása** 

1. Ellenőrizze, hogy telepítve van-e a Azure File Sync Agent v 5.1-es vagy újabb verziója.
2. A következő PowerShell-parancsok futtatásával sorolja fel az árva rétegekből származó fájlokat:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
3. Mentse a OrphanTieredFiles. txt kimeneti fájlt abban az esetben, ha a fájlokat a törlés után vissza kell állítani a biztonsági másolatból.

<a id="remove-orphaned"></a>**Árva rétegű fájlok eltávolítása** 

*1. lehetőség: Az árva rétegű fájlok törlése*

Ez a beállítás törli az árva rétegekből származó fájlokat a Windows Serveren, de a kiszolgáló-végpont eltávolítására van szükség, ha az a 30 nap elteltével vagy egy másik szinkronizálási csoporthoz van csatlakoztatva. A fájlok ütközése akkor fordul elő, ha a Windows Server vagy az Azure-fájlmegosztás fájljai frissülnek a kiszolgálói végpont újbóli létrehozása előtt.

1. Ellenőrizze, hogy telepítve van-e a Azure File Sync Agent v 5.1-es vagy újabb verziója.
2. Az Azure fájlmegosztás és a kiszolgálói végpont helyének biztonsági mentése.
3. Távolítsa el a kiszolgálói végpontot a szinkronizálási csoportban (ha létezik) a [kiszolgálói végpont eltávolítása](https://docs.microsoft.com/azure/storage/files/storage-sync-files-server-endpoint#remove-a-server-endpoint)című témakörben leírt lépéseket követve.

> [!Warning]  
> Ha a kiszolgálói végpontot a Remove-StorageSyncOrphanedTieredFiles parancsmag használata előtt nem távolítja el, a kiszolgálón lévő árva rétegbeli fájl törlésével törölheti az Azure-fájlmegosztás teljes fájlját. 

4. A következő PowerShell-parancsok futtatásával sorolja fel az árva rétegekből származó fájlokat:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
5. Mentse a OrphanTieredFiles. txt kimeneti fájlt abban az esetben, ha a fájlokat a törlés után vissza kell állítani a biztonsági másolatból.
6. A következő PowerShell-parancsok futtatásával törölheti az árva rétegű fájlokat:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFilesRemoved = Remove-StorageSyncOrphanedTieredFiles -Path <folder path containing orphaned tiered files> -Verbose
$orphanFilesRemoved.OrphanedTieredFiles > DeletedOrphanFiles.txt
```
**Megjegyzések** 
- A kiszolgálón módosult, az Azure-fájlmegosztás számára nem szinkronizált, rétegű fájlok törlődnek.
- Az elérhető (nem árva) fájlokat nem törli a rendszer.
- A nem rétegű fájlok a kiszolgálón maradnak.

7. Nem kötelező: Ha törli a 3. lépésben, hozza létre újra a kiszolgálói végpontot.

*2. lehetőség: Csatlakoztassa az Azure-fájlmegosztást, és másolja a helyileg használt fájlokat a kiszolgálón*

Ehhez a beállításhoz nem szükséges a kiszolgálói végpont eltávolítása, de elegendő lemezterület szükséges a teljes fájlok helyi másolásához.

1. [Csatlakoztassa](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) az Azure-fájlmegosztást a Windows Serveren, amely árva rétegű fájlokat tartalmaz.
2. A következő PowerShell-parancsok futtatásával sorolja fel az árva rétegekből származó fájlokat:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
3. A OrphanTieredFiles. txt kimeneti fájl segítségével azonosíthatja a kiszolgálón lévő árva rétegű fájlokat.
4. Írja felül az árva rétegekből származó fájlokat úgy, hogy az Azure-fájlmegosztás teljes fájlját a Windows Serverre másolja.

### <a name="how-to-troubleshoot-files-unexpectedly-recalled-on-a-server"></a>A fájlok váratlanul felhívhatók a kiszolgálókon  
A víruskereső, a biztonsági mentés és a nagy mennyiségű fájlt olvasó alkalmazások nem szándékolt visszahívásokat okoznak, kivéve, ha nem tartják tiszteletben az offline attribútum kihagyását, és kihagyja a fájlok tartalmának olvasását. Az offline fájlok kihagyását támogató termékek esetében a kihagyás segíthet elkerülni a nem kívánt visszahívásokat a víruskeresések, a biztonsági mentési feladatok és a hasonló műveletek során.

Érdeklődjön a szoftverszállítónál, hogy megtudja, hogyan konfigurálhatja a megoldást az offline fájlok olvasásának kihagyására.

A nem szándékolt visszahívások más helyzetekben is előfordulhatnak, például ha a Fájlkezelőben böngészik a fájlokat. Ha a kiszolgálón található Fájlkezelőben felhőalapú rétegzett fájlokat tartalmazó mappákat nyit meg, az nem kívánt visszahívásokat eredményezhet. Ez még gyakrabban előfordul, ha engedélyezve van egy víruskereső megoldás a kiszolgálón.

> [!NOTE]
>A telemetria eseménynaplóban használja az 9059-es AZONOSÍTÓJÚ eseményt annak meghatározására, hogy mely alkalmazás (ok) idézi elő a visszahívást. Ez az esemény biztosítja az alkalmazások visszahívási eloszlását egy kiszolgálói végpont számára, és óránként egyszer naplózza.

## <a name="general-troubleshooting"></a>Általános hibaelhárítás
Ha a kiszolgáló Azure File Syncával kapcsolatos problémákat tapasztal, kezdje a következő lépések végrehajtásával:
1. A Eseménynaplóban tekintse át a telemetria, az operatív és a diagnosztikai eseménynaplókat.
    - A szinkronizálás, a rétegek és a visszahívási problémák naplózása a telemetria, az alkalmazások és a Services\Microsoft\FileSync\Agent. alatti diagnosztikai és üzemeltetési eseménynaplókban történik.
    - A kiszolgálók kezelésével kapcsolatos problémákat (például a konfigurációs beállításokat) az alkalmazás-és Services\Microsoft\FileSync\Management. az operatív és a diagnosztikai eseménynaplóban naplózzák.
2. Ellenőrizze, hogy fut-e a Azure File Sync szolgáltatás a kiszolgálón:
    - Nyissa meg a szolgáltatások MMC beépülő modult, és ellenőrizze, hogy fut-e a Storage Sync Agent szolgáltatás (FileSyncSvc).
3. Ellenőrizze, hogy fut-e a Azure File Sync szűrő-illesztőprogramok (StorageSync. sys és StorageSyncGuard. sys):
    - Futtassa `fltmc`a parancsot egy rendszergazda jogú parancssorban. Győződjön meg arról, hogy a StorageSync. sys és a StorageSyncGuard. sys fájlrendszer-szűrő illesztőprogramjai vannak felsorolva.

Ha a probléma nem oldódik meg, futtassa a AFSDiag eszközt:
1. Hozzon létre egy könyvtárat, amelybe a rendszer menti a AFSDiag kimenetét (például C:\Output).
    > [!NOTE]
    >A AFSDiag a naplófájlok gyűjtése előtt törli a kimeneti könyvtár összes tartalmát. Adja meg a kimeneti helyet, amely nem tartalmaz adatokat.
2. Nyisson meg egy rendszergazda jogú PowerShell-ablakot, majd futtassa a következő parancsokat (nyomja le az ENTER billentyűt minden parancs után):

    ```powershell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-Afs c:\output # Note: Use the path created in step 1.
    ```

3. A Azure File Sync kernel módú nyomkövetési szinten adja meg az **1** értéket (kivéve, ha másként van megadva), majd nyomja le az ENTER billentyűt.
4. A Azure File Sync felhasználói mód nyomkövetési szintjén adja meg az **1** értéket (kivéve, ha másként van megadva), majd nyomja le az ENTER billentyűt.
5. Reprodukálja a problémát. Ha elkészült, adja meg a **D**értéket.
6. A rendszer a naplókat és nyomkövetési fájlokat tartalmazó. zip-fájlt menti a megadott kimeneti könyvtárba.

## <a name="see-also"></a>Lásd még
- [Az Azure File Sync monitorozása](storage-sync-files-monitoring.md)
- [Azure Files gyakori kérdések](storage-files-faq.md)
- [Azure Files-problémák hibaelhárítása Windowson](storage-troubleshoot-windows-file-connection-problems.md)
- [Azure Files-problémák hibaelhárítása Linuxon](storage-troubleshoot-linux-file-connection-problems.md)
