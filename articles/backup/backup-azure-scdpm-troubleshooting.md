---
title: A System Center Data Protection Manager és a Azure Backup hibáinak megoldása
description: A System Center Data Protection Manager hibáinak elhárítása.
ms.reviewer: kasinh
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: dacurwin
ms.openlocfilehash: 501e61d06b7724147d7224ae51bde5de736279b3
ms.sourcegitcommit: d585cdda2afcf729ed943cfd170b0b361e615fae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68689033"
---
# <a name="troubleshoot-system-center-data-protection-manager"></a>A System Center Data Protection Manager hibaelhárítása

Ez a cikk a Data Protection Manager használata során felmerülő problémák megoldásait ismerteti.

A System Center Data Protection Manager legújabb kibocsátási megjegyzései a [System Center dokumentációjában](https://docs.microsoft.com/system-center/dpm/dpm-release-notes?view=sc-dpm-2016)olvashatók. További információ a Data Protection Manager támogatásáról [ebben](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-2016)a mátrixban.


## <a name="error-replica-is-inconsistent"></a>Hiba: Inkonzisztens replika

A replika a következő okok miatt inkonzisztens lehet:
- A replika-létrehozási feladatok meghiúsulnak.
- Problémák léptek fel a módosítási naplóban.
- A kötet szintű szűrő bitképe hibákat tartalmaz.
- A forrásoldali gép váratlanul leáll.
- A szinkronizálási napló túlcsordul.
- A replika valóban inkonzisztens.

A probléma megoldásához hajtsa végre a következő műveleteket:
- A inkonzisztens állapot eltávolításához futtassa manuálisan a konzisztencia-ellenőrzést, vagy ütemezzen egy napi konzisztencia-ellenőrzést.
- Győződjön meg arról, hogy a Microsoft Azure Backup Server és Data Protection Manager legújabb verzióját használja.
- Győződjön meg arról, hogy az **automatikus konzisztencia** -beállítás engedélyezve van.
- Próbálja meg újraindítani a szolgáltatásokat a parancssorból. Használja a `net stop dpmra` parancsot, `net start dpmra`majd a parancsot.
- Győződjön meg arról, hogy a hálózati kapcsolatra és a sávszélességre vonatkozó követelmények teljesítése folyamatban van.
- Ellenőrizze, hogy a forrásszámítógép leállítása váratlanul megszakadt-e.
- Győződjön meg arról, hogy a lemez kifogástalan állapotban van, és hogy van-e elegendő hely a replikához.
- Győződjön meg arról, hogy nincsenek párhuzamosan futó biztonsági mentési feladatok.

## <a name="error-online-recovery-point-creation-failed"></a>Hiba: Az online helyreállítási pont létrehozása sikertelen volt.

A probléma megoldásához hajtsa végre a következő műveleteket:
- Győződjön meg arról, hogy a Azure Backup ügynök legújabb verzióját használja.
- Próbáljon meg manuálisan létrehozni egy helyreállítási pontot a védelmi feladat területén.
- Győződjön meg arról, hogy az adatforráson konzisztencia-ellenőrzés fut.
- Győződjön meg arról, hogy a hálózati kapcsolatra és a sávszélességre vonatkozó követelmények teljesítése folyamatban van.
- Ha a replika-adatmennyiség inkonzisztens állapotban van, hozzon létre egy lemez-helyreállítási pontot ebből az adatforrásból.
- Győződjön meg arról, hogy a replika létezik, és nem hiányzik.
- Győződjön meg arról, hogy a replika elegendő lemezterülettel rendelkezik a frissítési sorszám (USN) napló létrehozásához.

## <a name="error-unable-to-configure-protection"></a>Hiba: Nem sikerült beállítani a védelmet.

Ez a hiba akkor fordul elő, ha a Data Protection Manager-kiszolgáló nem tud kapcsolatba lépni a védett kiszolgálóval. 

A probléma megoldásához hajtsa végre a következő műveleteket:
- Győződjön meg arról, hogy a Azure Backup ügynök legújabb verzióját használja.
- Ellenőrizze, hogy van-e kapcsolat (hálózati/tűzfal/proxy) a Data Protection Manager-kiszolgáló és a védett kiszolgáló között.
- Ha az SQL Servert védi, győződjön meg arról, hogy a **bejelentkezési tulajdonságok** > **NT AUTHORITY\SYSTEM** tulajdonság a **sysadmin (rendszergazda** ) beállítást engedélyezi.

## <a name="error-server-not-registered-as-specified-in-vault-credential-file"></a>Hiba: A kiszolgáló nincs regisztrálva a tár hitelesítőadat-fájljában megadott módon

Ez a hiba a Data Protection Manager/Azure Backup Server-adatbázis helyreállítási folyamata során fordul elő. A helyreállítási folyamatban használt tár hitelesítőadat-fájl nem tartozik a Data Protection Manager/Azure Backup kiszolgáló Recovery Services-tárolójához.

A probléma megoldásához hajtsa végre az alábbi lépéseket:
1. Töltse le a tároló hitelesítőadat-fájlját a Recovery Services tárolóból, amelyhez a Data Protection Manager/Azure Backup-kiszolgáló regisztrálva van.
2. Próbálja meg regisztrálni a kiszolgálót a tárolóhoz a legutóbb letöltött tár hitelesítőadat-fájl használatával.

## <a name="error-no-recoverable-data-or-selected-server-not-a-data-protection-manager-server"></a>Hiba: A helyreállítható vagy a kijelölt kiszolgáló nem Data Protection Manager kiszolgáló

Ez a hiba a következő okok miatt fordul elő:
- Nincs más Data Protection Manager/Azure Backup-kiszolgáló regisztrálva a Recovery Services-tárolóban.
- A kiszolgálók még nem töltötték fel a metaadatokat.
- A kiválasztott kiszolgáló nem Data Protection Manager/Azure Backup-kiszolgáló.

Ha más Data Protection Manager/Azure Backup-kiszolgáló regisztrálva van a Recovery Services-tárolóban, hajtsa végre a következő lépéseket a probléma megoldásához:
1. Győződjön meg arról, hogy a legújabb Azure Backup ügynök telepítve van.
2. Miután megtörtént a legújabb ügynök telepítésének ellenőrzése, várjon egy nappal a helyreállítási folyamat elindítása előtt. Az éjszakai biztonsági mentési feladatok feltöltik az összes védett biztonsági mentés metaadatait a felhőbe. Ezután a biztonsági mentési állapotok elérhetők a helyreállításhoz.

## <a name="error-provided-encryption-passphrase-doesnt-match-passphrase-for-server"></a>Hiba: A megadott titkosítási jelszó nem egyezik meg a kiszolgáló jelszavával

Ez a hiba akkor fordul elő, ha a titkosítási folyamat során Data Protection Manager/Azure Backup Server-adatbázis helyreállítását hajtja végre. A helyreállítási folyamatban használt titkosítási jelszó nem egyezik a kiszolgáló titkosítási jelszavával. Ennek eredményeképpen az ügynök nem tudja visszafejteni az adattitkosítást, és a helyreállítás meghiúsul.

> [!IMPORTANT]
> Ha elfelejti vagy elveszíti a titkosítási jelszót, az adat helyreállítására nincs más módszer. Az egyetlen lehetőség a jelszó újbóli előállítása. Az új jelszó használatával titkosíthatja a jövőbeli biztonsági másolati adataikat.
>
> Amikor helyreállítja az adatgyűjtést, mindig ugyanazt a titkosítási jelszót adja meg, amely a Data Protection Manager/Azure Backup kiszolgálóhoz van társítva. 
>
