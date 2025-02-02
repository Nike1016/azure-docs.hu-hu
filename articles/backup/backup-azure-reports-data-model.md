---
title: Azure Backup adatmodellje
description: Ez a cikk a Azure Backup-jelentésekhez Power BI adatmodell részleteiről beszél.
author: adigan
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 06/26/2017
ms.author: adigan
ms.openlocfilehash: 66245a25af000d999ded87c58e3ba225b373b101
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465592"
---
# <a name="data-model-for-azure-backup-reports"></a>Adatmodell az Azure Backup-jelentésekhez
Ez a cikk a Azure Backup-jelentések létrehozásához használt Power BI adatmodellt ismerteti. Az adatmodell használatával a meglévő jelentéseket a megfelelő mezők alapján szűrheti, ami még fontosabb, a modellben található táblák és mezők használatával saját jelentéseket hozhat létre. 

## <a name="creating-new-reports-in-power-bi"></a>Új jelentések létrehozása a Power BIban
A Power BI testreszabási funkciókat biztosít, amelyekkel [jelentéseket hozhat létre az adatmodell használatával](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/).

## <a name="using-azure-backup-data-model"></a>Azure Backup adatmodell használata
A következő mezőket használhatja az adatmodell részeként jelentések létrehozásához és a meglévő jelentések testreszabásához.

### <a name="alert"></a>Riasztás
Ez a tábla alapvető mezőket és összesítéseket biztosít különböző riasztásokkal kapcsolatos mezőkhöz.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| #AlertsCreatedInPeriod |Egész szám |A kiválasztott időszakban létrehozott riasztások száma |
| %ActiveAlertsCreatedInPeriod |Százalék |Aktív riasztások százalékos aránya a kiválasztott időszakban |
| %CriticalAlertsCreatedInPeriod |Százalék |Kritikus riasztások százalékos aránya a kiválasztott időszakban |
| AlertOccurrenceDate |Date |A riasztás létrehozásának dátuma |
| AlertSeverity |Text |A riasztás súlyossága például kritikus |
| AlertStatus |Text |A riasztás állapota, például aktív |
| AlertType |Text |A generált riasztás típusa például: biztonsági mentés |
| AlertUniqueId |Text |A generált riasztás egyedi azonosítója |
| AsOnDateTime |Date/Time |A kijelölt sor legutóbbi frissítésének ideje |
| AvgResolutionTimeInMinsForAlertsCreatedInPeriod |Tizedes tört szám |A kijelölt időszakra vonatkozó riasztás feloldásának átlagos időtartama (percben) |
| EntityState |Text |A riasztási objektum aktuális állapota például: aktív, törölve |

### <a name="backup-item"></a>Biztonsági másolati tétel
Ez a tábla alapszintű mezőket és összesítéseket biztosít a különböző biztonsági mentési elemek kapcsolódó mezőihez.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| #BackupItems |Egész szám |Biztonsági másolati elemek száma |
| #UnprotectedBackupItems |Egész szám |A védelemhez leállított biztonságimásolat-elemek száma, de biztonsági mentések vannak konfigurálva, de a biztonsági másolatok nem indult el|
| AsOnDateTime |Date/Time |A kijelölt sor legutóbbi frissítésének ideje |
| BackupItemFriendlyName |Text |A biztonságimásolat-tétel rövid neve |
| BackupItemId |Text |A biztonsági másolati tétel azonosítója |
| BackupItemName |Text |A biztonságimásolat-tétel neve |
| BackupItemType |Text |A biztonsági mentési elem típusa (például virtuális gép, fájlmappa) |
| EntityState |Text |A biztonsági mentési elem objektumának aktuális állapota például: aktív, törölve |
| LastBackupDateTime |Date/Time |A kijelölt biztonsági másolati elemek legutóbbi biztonsági mentésének időpontja |
| LastBackupState |Text |A kiválasztott biztonsági másolati elem legutóbbi biztonsági mentésének állapota, például sikeres, sikertelen |
| LastSuccessfulBackupDateTime |Date/Time |A kijelölt biztonsági másolati tétel utolsó sikeres biztonsági mentésének időpontja |
| ProtectionState |Text |A biztonsági mentési elem jelenlegi védelmi állapota például védett, ProtectionStopped |

### <a name="calendar"></a>Naptár
Ez a táblázat a naptárral kapcsolatos mezők részleteit tartalmazza.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| Date |Date |Az adatok szűréséhez kiválasztott dátum |
| DateKey |Text |Minden dátumérték egyedi kulcsa |
| DayDiff |Tizedes tört szám |Az adatszűrési nap különbsége például a 0 az aktuális napi adat, a-1 az előző egy nap adatai, 0 és-1 az aktuális és az előző nap adataira utal.  |
| hónap |Text |Az év hónapja, amely az adat szűrésére lett kiválasztva, a hónap első napján kezdődik, és a 31. napon ér véget |
| MonthDate | Date |A hónap végének dátuma, amely az adatok szűrésére van kiválasztva |
| MonthDiff |Tizedes tört szám |Az adatszűrési hónap eltérése például a 0 a jelenlegi hónap adataira utal, a-1 az előző hónap adataira utal, 0 és-1 az aktuális és az előző hónapra vonatkozó adatértéket jelzi. |
| Hét |Text |Az adatszűrésre kijelölt hét, a hét vasárnap kezdődik, és szombaton ér véget |
| WeekDate |Date |Az adatok szűrésére kijelölt hét napja, a hét végén |
| WeekDiff |Tizedes tört szám |Az adatszűrési hét eltérése például az aktuális heti adatértéket jelzi, a-1 az előző heti adatértékeket jelzi, 0 és-1 az aktuális és az előző hét adataira utal. |
| Év |Text |Az adatszűréshez kiválasztott naptári év |
| YearDate |Date |Az év végének dátuma, amely az adatok szűrésére van kiválasztva |

### <a name="job"></a>Feladat
Ez a tábla alapszintű mezőket és összesítéseket biztosít a különböző feladatokhoz kapcsolódó mezőkben.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| #JobsCreatedInPeriod |Egész szám |A kiválasztott időszakban létrehozott feladatok száma |
| %FailuresForJobsCreatedInPeriod |Százalék |A feladatok általános feladatainak százalékos aránya a kiválasztott időszakban |
| 80thPercentileDataTransferredInMBForBackupJobsCreatedInPeriod |Tizedes tört szám |a kiválasztott időszakban létrehozott **biztonsági mentési** feladatok MB-ban továbbított adatok 80th százalékos értéke |
| AsOnDateTime |Date/Time |A kijelölt sor legutóbbi frissítésének ideje |
| AvgBackupDurationInMinsForJobsCreatedInPeriod |Tizedes tört szám |A kiválasztott időszakban létrehozott **befejezett biztonsági mentési** feladatok átlagos ideje (percben) |
| AvgRestoreDurationInMinsForJobsCreatedInPeriod |Tizedes tört szám |A kiválasztott időszakban létrehozott **befejezett visszaállítási** feladatok átlagos ideje (percben) |
| BackupStorageDestination |Text |A biztonsági mentési tár célja, például felhő, lemez  |
| EntityState |Text |A feladatütemezés aktuális állapota például: aktív, törölve |
| JobFailureCode |Text |Hiba történt a hibakód karakterlánca miatt, mert a művelet sikertelen volt |
| JobOperation |Text |A művelet, amelynek a feladata fut, például biztonsági mentés, visszaállítás, biztonsági mentés konfigurálása |
| JobStartDate |Date |Az a dátum, amikor a feladatot elindították |
| JobStartTime |Time |A feladatok futásának ideje |
| Feladat állapota |Text |A Befejezett feladatok állapota, például befejezett, sikertelen |
| JobUniqueId |Text |A feladatot azonosító egyedi azonosító |

### <a name="policy"></a>Szabályzat
Ez a táblázat alapvető mezőket és összesítéseket biztosít különböző házirendekkel kapcsolatos mezőkhöz.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| #Policies |Egész szám |A rendszeren létező biztonsági mentési házirendek száma |
| #PoliciesInUse |Egész szám |A biztonsági másolatok konfigurálásához jelenleg használt házirendek száma |
| AsOnDateTime |Date/Time |A kijelölt sor legutóbbi frissítésének ideje |
| BackupDaysOfTheWeek |Text |A hét azon napjai, amikor a biztonsági mentések ütemezése megtörtént |
| BackupFrequency |Text |A biztonsági másolatok futtatásának gyakorisága például naponta, hetente |
| BackupTimes |Text |A biztonsági másolatok ütemezésének dátuma és időpontja |
| DailyRetentionDuration |Egész szám |A beállított biztonsági másolatok teljes megőrzési időtartama (nap) |
| DailyRetentionTimes |Text |A napi megőrzés konfigurálásának dátuma és időpontja |
| EntityState |Text |A házirend-objektum aktuális állapota például: aktív, törölt |
| MonthlyRetentionDaysOfTheMonth |Text |A havi megőrzésre kijelölt hónap dátuma |
| MonthlyRetentionDaysOfTheWeek |Text |A havi megőrzésre kiválasztott hét napjai |
| MonthlyRetentionDuration |Tizedes tört szám |A beállított biztonsági másolatok teljes megőrzési időtartama (hónap) |
| MonthlyRetentionFormat |Text |A havi adatmegőrzéshez szükséges konfiguráció típusa, például naponta, hetente, hetente |
| MonthlyRetentionTimes |Text |A havi megőrzés konfigurálásának dátuma és időpontja |
| MonthlyRetentionWeeksOfTheMonth |Text |A hónap hete, amikor a havi megőrzés konfigurálva van például az első, az utolsó stb. |
| PolicyName |Text |A megadott házirend neve |
| PolicyUniqueId |Text |A szabályzat azonosítására szolgáló egyedi azonosító |
| RetentionType |Text |Adatmegőrzési szabály típusa például napi, heti, havi, éves |
| WeeklyRetentionDaysOfTheWeek |Text |A heti megőrzéshez kiválasztott hét napjai |
| WeeklyRetentionDuration |Tizedes tört szám |Heti megőrzési időtartam összesen hetekben a konfigurált biztonsági másolatok esetében |
| WeeklyRetentionTimes |Text |A heti megőrzés konfigurálásának dátuma és időpontja |
| YearlyRetentionDaysOfTheMonth |Text |Az éves megőrzéshez kiválasztott hónap dátuma |
| YearlyRetentionDaysOfTheWeek |Text |Az éves megőrzéshez kiválasztott hét napjai |
| YearlyRetentionDuration |Tizedes tört szám |Teljes megőrzési időtartam években a konfigurált biztonsági másolatok esetében |
| YearlyRetentionFormat |Text |Az évenkénti megőrzéshez szükséges konfiguráció típusa (például naponta, napi rendszerességgel, hetente a hét alapján) |
| YearlyRetentionMonthsOfTheYear |Text |Az év hónapos megőrzésre kiválasztott hónapja |
| YearlyRetentionTimes |Text |Az éves adatmegőrzés konfigurálásának dátuma és időpontja |
| YearlyRetentionWeeksOfTheMonth |Text |A hónap hete, amikor az éves megőrzés konfigurálva van például az első, az utolsó stb. |

### <a name="protected-server"></a>Védett kiszolgáló
Ez a táblázat alapvető mezőket és összesítéseket biztosít a különböző védett kiszolgálókkal kapcsolatos mezőkhöz képest.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| #ProtectedServers |Egész szám |Védett kiszolgálók száma |
| AsOnDateTime |Date/Time |A kijelölt sor legutóbbi frissítésének ideje |
| AzureBackupAgentOSType |Text |Azure Backup ügynök operációs rendszerének típusa |
| AzureBackupAgentOSVersion |Text |Azure Backup ügynök operációs rendszerének verziója |
| AzureBackupAgentUpdateDate |Text |Az ügynök biztonsági mentési ügynökének frissítésének dátuma |
| AzureBackupAgentVersion |Text |Az ügynök biztonsági mentési verziójának verziószáma |
| BackupManagementType |Text |Szolgáltatói típus a biztonsági mentéshez, például IaaSVM, fájlmappa |
| EntityState |Text |A védett kiszolgáló objektum aktuális állapota például: aktív, törölve |
| ProtectedServerFriendlyName |Text |A védett kiszolgáló rövid neve |
| ProtectedServerName |Text |A védett kiszolgáló neve |
| ProtectedServerType |Text |A védett kiszolgáló típusa (például IaaSVMContainer) |
| ProtectedServerName |Text |Annak a védett kiszolgálónak a neve, amelyhez a biztonsági másolati elemek tartoznak |
| RegisteredContainerId |Text |A biztonsági mentéshez regisztrált tároló azonosítója |

### <a name="storage"></a>Storage
Ez a táblázat alapvető mezőket és összesítéseket biztosít a különböző tárterületekkel kapcsolatos mezőkhöz képest.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| #ProtectedInstances |Tizedes tört szám |A előtér-tároló kiszámításához használt védett példányok száma a számlázásban, a kijelölt idő legutóbbi értékének alapján számítva |
| AsOnDateTime |Date/Time |A kijelölt sor legutóbbi frissítésének ideje |
| CloudStorageInMB |Tizedes tört szám |A biztonsági mentések által használt Felhőbeli biztonsági mentési tár, amely a kijelölt idő legutóbbi értéke alapján lett kiszámítva |
| EntityState |Text |Az objektum aktuális állapota például: aktív, törölve |
| LastUpdatedDate |Date |A kijelölt sor legutóbbi frissítésének dátuma |

### <a name="time"></a>Time
Ez a táblázat az időponthoz kapcsolódó mezők részleteit tartalmazza.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| Óra |Time |A nap órája, például 1:00:00 PM |
| HourNumber |Tizedes tört szám |Az óra száma a napon, például 13,00 |
| Perc |Tizedes tört szám |Az óra perce |
| PeriodOfTheDay |Text |A nap adott időszakának tárolóhelye, például 12-3 |
| Time |Time |A nap időpontja például: 12:00:01 |
| TimeKey |Text |Az időt jelölő kulcs értéke |

### <a name="vault"></a>Tár
Ez a tábla alapszintű mezőket és összesítéseket biztosít különböző tárolókkal kapcsolatos mezőkben.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| #Vaults |Egész szám |Tárolók száma |
| AsOnDateTime |Date/Time |A kijelölt sor legutóbbi frissítésének ideje |
| AzureDataCenter |Text |Az adatközpont, ahol a tár található |
| EntityState |Text |A tár objektum aktuális állapota például: aktív, törölve |
| StorageReplicationType |Text |A tár tárolási replikálásának típusa, például GeoRedundant |
| SubscriptionId |Text |A jelentések létrehozásához kiválasztott ügyfél előfizetés-azonosítója |
| VaultName |Text |A tár neve |
| VaultTags |Text |A tárolóhoz társított Címkék |

## <a name="next-steps"></a>További lépések
Miután áttekintette a Azure Backup-jelentések létrehozásához szükséges adatmodellt, további részleteket a következő cikkekben talál a jelentések létrehozásával és megtekintésével kapcsolatban Power BIban.

* [Jelentések létrehozása a Power BIban](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/)
* [Jelentések szűrése Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-about-filters-and-highlighting-in-reports/)
