---
title: Az Azure Backup-fájlok és -mappák lassú biztonsági mentésének hibaelhárítása
description: Hibaelhárítási útmutatót biztosít a Azure Backup teljesítményproblémák okának diagnosztizálásához
ms.reviewer: saurse
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.author: dacurwin
ms.openlocfilehash: 16a8eb4eea4e5e1e3bb49049c49d73adb99eef55
ms.sourcegitcommit: d585cdda2afcf729ed943cfd170b0b361e615fae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68688616"
---
# <a name="troubleshoot-slow-backup-of-files-and-folders-in-azure-backup"></a>Az Azure Backup-fájlok és -mappák lassú biztonsági mentésének hibaelhárítása
Ez a cikk hibaelhárítási útmutatást nyújt a fájlok és mappák lassú biztonsági mentési teljesítményének okának diagnosztizálásához Azure Backup használatakor. Ha a Azure Backup ügynök használatával készít biztonsági másolatot a fájlokról, a biztonsági mentési folyamat a vártnál több időt vehet igénybe. Ezt a késleltetést a következők egyike okozhatja:

* [A biztonsági mentés alatt álló számítógépen szűk a teljesítmény.](#cause1)
* [Egy másik folyamat vagy víruskereső szoftver zavarja a Azure Backup folyamatot.](#cause2)
* [A biztonságimásolat-készítő ügynök egy Azure-beli virtuális gépen (VM) fut.](#cause3)  
* [Nagy mennyiségű (millió) fájl biztonsági mentését végzi.](#cause4)

A hibaelhárítási problémák megkezdése előtt javasoljuk, hogy töltse le és telepítse a [legújabb Azure Backup](https://aka.ms/azurebackup_agent)-ügynököt. Gyakori frissítéseket végezünk a Backup ügynöknek a különböző problémák megoldásához, funkciók hozzáadásához és a teljesítmény javításához.

Javasoljuk továbbá, hogy tekintse át a [Azure Backup szolgáltatással](backup-azure-backup-faq.md) kapcsolatos gyakori kérdéseket, és győződjön meg arról, hogy nem tapasztalt a gyakori konfigurációs problémák egyikét sem.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

<a id="cause1"></a>

## <a name="cause-performance-bottlenecks-on-the-computer"></a>Ok: A számítógép teljesítményének szűk keresztmetszetei
A biztonsági mentés alatt álló számítógép szűk keresztmetszetei késéseket okozhatnak. Előfordulhat például, hogy a számítógép képes a lemezre írni vagy olvasni, vagy a rendelkezésre álló sávszélességet a hálózaton keresztüli adatküldés esetén szűk keresztmetszetet okozhat.

A Windows olyan beépített eszközt biztosít, amely a [Teljesítményfigyelő](https://technet.microsoft.com/magazine/2008.08.pulse.aspx) (Perfmon) használatával ismeri fel ezeket a szűk keresztmetszeteket.

Íme néhány teljesítményszámlálók és tartományok, amelyek hasznosak lehetnek a szűk keresztmetszetek optimális biztonsági mentéshez való diagnosztizálásában.

| Számláló | Állapot |
| --- | --- |
| Logikai lemez (fizikai lemez) –% tétlen |• 100% üresjárati állapot: 50% tétlen = kifogástalan</br>• 49% tétlen – 20% tétlen = figyelmeztetés vagy figyelő</br>• 19% üresjáratban 0% tétlen = kritikus vagy a spec |
| Logikai lemez (fizikai lemez) –% AVG. Lemez (mp) olvasása vagy írása |• 0,001 MS – 0,015 MS = kifogástalan</br>• 0,015 MS – 0,025 MS = figyelmeztetés vagy figyelő</br>• 0,026 MS vagy több = kritikus vagy nem a spec |
| Logikai lemez (fizikai lemez) – a lemez aktuális várólistájának hossza (az összes példány esetében) |80 kérelem 6 percnél hosszabb ideig |
| Memória – nem lapozható készlet (bájt) |• A felhasznált készlet kevesebb mint 60%-a felhasználva = kifogástalan<br>• 61% – 80%-a készlet felhasználva = figyelmeztetés vagy figyelő</br>• Nagyobb, mint 80%-os készlet felhasználva = kritikus vagy a spec |
| Memória – a készlet lapozható bájtjai |• A felhasznált készlet kevesebb mint 60%-a felhasználva = kifogástalan</br>• 61% – 80%-a készlet felhasználva = figyelmeztetés vagy figyelő</br>• Nagyobb, mint 80%-os készlet felhasználva = kritikus vagy a spec |
| Memória – rendelkezésre álló megabájt |• a rendelkezésre álló szabad memória 50%-a vagy több = kifogástalan</br>• a rendelkezésre álló szabad memória 25%-a = figyelő</br>• a rendelkezésre álló szabad memória 10%-a = figyelmeztetés</br>• A rendelkezésre álló szabad memória kevesebb, mint 100 MB vagy 5%-a érhető el = kritikus vagy a spec |
| Processzor –\%processzoridő (minden példány) |• Kevesebb, mint 60% felhasznált = kifogástalan</br>• 61% – 90% felhasználva = figyelő vagy figyelmeztetés</br>• 91% – 100% felhasználva = kritikus |

> [!NOTE]
> Ha azt állapítja meg, hogy az infrastruktúra a bűnös, javasoljuk, hogy a lemezeket a jobb teljesítmény érdekében rendszeresen töredezettségmentesíteni.
>
>

<a id="cause2"></a>

## <a name="cause-another-process-or-antivirus-software-interfering-with-azure-backup"></a>Ok: Egy másik folyamat vagy víruskereső szoftver zavarja a Azure Backup
Láttuk, hogy a Windows rendszer más folyamatai negatív hatással voltak a Azure Backup ügynök folyamatának teljesítményére. Ha például a Azure Backup-ügynököt és egy másik programot használ az adatbiztonsági mentéshez, vagy ha víruskereső szoftver fut, és zárolva van a fájlok biztonsági mentésére szolgáló zárolás, a fájlok több zárolása is okozhatja a tartalmat. Ebben az esetben előfordulhat, hogy a biztonsági mentés meghiúsul, vagy a feladat a vártnál több időt is igénybe vehet.

Ebben a forgatókönyvben a legjobb javaslat, hogy kikapcsolja a másik biztonsági mentési programot, hogy megtekintse a Azure Backup-ügynök biztonsági mentésének időpontját. Általában ügyeljen arra, hogy a több biztonsági mentési feladat ne fusson egyszerre, hogy ne legyenek hatással egymásra.

A víruskereső programok esetében javasoljuk, hogy zárja ki a következő fájlokat és helyet:

* C:\Program Files\Microsoft Azure Recovery Services Agent\bin\cbengine.exe folyamatként
* C:\Program Files\Microsoft Azure Recovery Services Agent \ mappák
* Üres hely (ha nem a standard helyet használja)

<a id="cause3"></a>

## <a name="cause-backup-agent-running-on-an-azure-virtual-machine"></a>Ok: Azure-beli virtuális gépen futó biztonsági mentési ügynök
Ha a biztonsági mentési ügynököt egy virtuális gépen futtatja, a teljesítmény lassabb lesz, mint amikor egy fizikai gépen futtatja. Ez a IOPS korlátai miatt várható.  Azonban optimalizálhatja a teljesítményt úgy, hogy átváltja az Azure-Premium Storage biztonsági mentés alatt álló adatmeghajtókat. Dolgozunk a probléma javításán, és a javítás egy későbbi kiadásban is elérhető lesz.

<a id="cause4"></a>

## <a name="cause-backing-up-a-large-number-millions-of-files"></a>Ok: Nagy számú (millió) fájl biztonsági mentése
A nagyméretű adatmennyiség hosszabb időt vesz igénybe, mint a kisebb adatmennyiségek áthelyezése. Bizonyos esetekben a biztonsági mentés ideje nem csupán az adat méretére, hanem a fájlok vagy mappák számára is vonatkozik. Ez különösen akkor igaz, ha több millió kis fájlról (néhány bájtról néhány kilobájtra) készül biztonsági mentés.

Ez a viselkedés azért fordul elő, mert az adatbiztonsági mentés és az Azure-ba való áthelyezés során az Azure egyszerre katalogizálja a fájlokat. Bizonyos ritkán előforduló esetekben a katalógusbeli művelet a vártnál hosszabb időt vesz igénybe.

A következő mutatók segítségével megismerheti a szűk keresztmetszeteket, és ennek megfelelően működik a következő lépésekben:

* **A felhasználói felületen az adatátvitel előrehaladása látható**. Az adatforgalom továbbra is átvitel alatt áll. Előfordulhat, hogy a hálózati sávszélesség vagy az adatméret késést okoz.
* **A felhasználói felület nem mutatja az adatátvitel folyamatát**. Nyissa meg a C:\Program Files\Microsoft Azure Recovery Services Agent\Temp található naplókat, és keresse meg a FileProvider:: EndData bejegyzést a naplókban. Ez a bejegyzés azt jelzi, hogy az adatátvitel befejeződött, és a katalógus-művelet zajlik. Ne szakítsa meg a biztonsági mentési feladatokat. Ehelyett várjon egy kicsit, amíg a katalógus-művelet befejeződik. Ha a probléma továbbra is fennáll, forduljon az [Azure ügyfélszolgálatához](https://portal.azure.com/#create/Microsoft.Support).
