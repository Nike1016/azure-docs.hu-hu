---
title: 'Azure Backup: a Linux rendszerű virtuális gépek alkalmazással konzisztens biztonsági mentései'
description: A Linux rendszerű virtuális gépek alkalmazás-konzisztens biztonsági másolatait az Azure-ba hozhatja létre. Ez a cikk a parancsfájl-keretrendszer konfigurálását ismerteti az Azure-ban üzembe helyezett Linux virtuális gépek biztonsági mentéséhez. Ez a cikk hibaelhárítási információkat is tartalmaz.
ms.reviewer: anuragm
author: dcurwin
manager: carmonm
keywords: alkalmazás-konzisztens biztonsági mentés; alkalmazás-konzisztens Azure-beli virtuális gépek biztonsági mentése; Linuxos virtuális gép biztonsági mentése; Azure Backup
ms.service: backup
ms.topic: conceptual
ms.date: 1/12/2018
ms.author: dacurwin
ms.openlocfilehash: bb764cfa14b4bc8e53f25629961a1baecd6a6c1f
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/12/2019
ms.locfileid: "68954694"
---
# <a name="application-consistent-backup-of-azure-linux-vms"></a>Az Azure Linux rendszerű virtuális gépek alkalmazásának konzisztens biztonsági mentése

A virtuális gépek biztonsági mentési pillanatképének készítésekor az alkalmazás konzisztenciája azt jelenti, hogy az alkalmazások akkor kezdődnek, amikor a virtuális gépek a visszaállítás után indulnak. Ahogy az is elképzelhető, hogy az alkalmazások konzisztenciája rendkívül fontos. Annak érdekében, hogy a Linux rendszerű virtuális gépek konzisztensek legyenek, az alkalmazással konzisztens biztonsági mentéseket használhat a Linux előtti és utáni parancsfájl-keretrendszer használatával. A pre-script és a parancsfájl utáni keretrendszer támogatja a Azure Resource Manager telepített linuxos virtuális gépeket. Az alkalmazás-konzisztencia parancsfájljai nem támogatják Service Manager telepített virtuális gépeket vagy Windowsos virtuális gépeket.

## <a name="how-the-framework-works"></a>A keretrendszer működése

A keretrendszer lehetővé teszi, hogy a VM-pillanatképek készítése közben egyéni parancsfájlok futtatását és a parancsfájlok utáni parancsfájlokat is futtasson. A virtuális gép pillanatképének megkezdése előtt a parancsfájlok futtatása előtt futtassa a parancsfájlokat, és a szkriptek azonnal futnak a virtuális gép pillanatképének elkészítése után. A parancsfájlok előtti és utáni parancsfájlok lehetővé teszik az alkalmazás és a környezet szabályozását, miközben a virtuális gépek pillanatképeit futtatja.

A parancsfájl-előkészítők natív Application API-kat indítanak, amelyek fokozatos leválasztása az IOs-et, és a memóriában lévő tartalmakat a lemezre ürítik. Ezek a műveletek biztosítják, hogy a pillanatkép alkalmazás konzisztens legyen. A szkriptek a natív alkalmazás API-kat használják az IOs kiolvasztására, ami lehetővé teszi, hogy az alkalmazás a virtuális gép pillanatképe után folytassa a normál műveleteket.

## <a name="steps-to-configure-pre-script-and-post-script"></a>A parancsfájl előtti és utáni parancsfájl konfigurálásának lépései

1. Jelentkezzen be a legfelső szintű felhasználóként arra a linuxos virtuális gépre, amelyről biztonsági másolatot szeretne készíteni.

2. A [githubról](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig)töltse le a **VMSnapshotScriptPluginConfig. JSON** fájlt, és másolja a **/etc/Azure** mappába minden olyan virtuális gép számára, amelyről biztonsági másolatot szeretne készíteni. Ha a **/etc/Azure** mappa nem létezik, hozza létre.

3. Másolja az alkalmazáshoz tartozó előzetes parancsfájlt és parancsfájlt a biztonsági mentésre tervezett összes virtuális gépre. A szkripteket a virtuális gép bármely helyére másolhatja. Ügyeljen arra, hogy a **VMSnapshotScriptPluginConfig. JSON** fájlban frissítse a parancsfájlok teljes elérési útját.

4. Ellenőrizze a következő engedélyeket a fájlokhoz:

   - **VMSnapshotScriptPluginConfig.json**: Engedély "600." Például csak a "root" felhasználó "READ" és "Write" engedélyekkel kell rendelkeznie ehhez a fájlhoz, és egyetlen felhasználónak sem kell "EXECUTE" engedélyekkel rendelkeznie.

   - **Parancsfájl előtti fájl**: Engedély "700."  Például csak a "root" felhasználó "READ", "Write" és "EXECUTE" engedélyekkel kell rendelkeznie ehhez a fájlhoz.

   - **Parancsfájl utáni** Engedély "700." Például csak a "root" felhasználó "READ", "Write" és "EXECUTE" engedélyekkel kell rendelkeznie ehhez a fájlhoz.

   > [!Important]
   > A keretrendszer nagy teljesítményt nyújt a felhasználóknak. Gondoskodjon a keretrendszer védelméről, és győződjön meg arról, hogy csak a "root" felhasználó fér hozzá a kritikus JSON-és parancsfájl-fájlokhoz.
   > Ha a követelmények nem teljesülnek, a parancsfájl nem fog futni, ami egy fájlrendszer összeomlását és inkonzisztens biztonsági mentését eredményezi.
   >

5. Konfigurálja a **VMSnapshotScriptPluginConfig. JSON** fájlt az itt leírtak szerint:
    - **pluginName**: Hagyja meg a mezőt a következő módon, vagy előfordulhat, hogy a parancsfájlok nem a várt módon működnek.

    - **preScriptLocation**: Adja meg az előzetes parancsfájl teljes elérési útját azon a virtuális gépen, amelyen biztonsági mentés készül.

    - **postScriptLocation**: Adja meg annak a virtuális gépnek a teljes elérési útját, amelyről biztonsági mentés készül.

    - **preScriptParams**: Adja meg az előzetes parancsfájlnak átadandó opcionális paramétereket. Az összes paraméternek idézőjelek közé kell esnie. Ha több paramétert használ, a paramétereket vesszővel válassza el egymástól.

    - **postScriptParams**: Adja meg azokat a választható paramétereket, amelyeket át kell adni a parancsfájl utáni művelethez. Az összes paraméternek idézőjelek közé kell esnie. Ha több paramétert használ, a paramétereket vesszővel válassza el egymástól.

    - **preScriptNoOfRetries**: Állítsa be, hogy a rendszer hányszor próbálja meg újból végrehajtani a parancsfájlt, ha hiba történt a megszakítás előtt. Ha hiba történt, akkor a nulla érték csak egyetlen próbálkozást jelent.

    - **postScriptNoOfRetries**:  Állítsa be, hogy a rendszer hányszor próbálja meg újból végrehajtani a parancsfájlt, ha hiba történt a megszakítás előtt. Ha hiba történt, akkor a nulla érték csak egyetlen próbálkozást jelent.

    - **timeoutInSeconds**: Adja meg a pre-script és a parancsfájl utáni egyéni időtúllépéseket (a maximális érték 1800).

    - **continueBackupOnFailure**: Állítsa **igaz** értékre, ha azt szeretné, hogy a Azure Backup a fájlrendszer konzisztens/összeomlás-konzisztens biztonsági mentéséhez térjen vissza, ha az előzetes parancsfájl vagy a parancsfájl utáni hiba meghiúsul. Ha ezt a beállítást **hamis** értékre állítja, akkor a parancsfájl meghibásodása esetén sikertelen lesz a biztonsági mentés (kivéve, ha olyan egylemezes virtuális géppel rendelkezik, amely a beállítástól függetlenül visszaesik az összeomlás-konzisztens biztonsági mentésre).

    - **fsFreezeEnabled**: Annak megadása, hogy a rendszer a Linux fsfreeze hívja-e meg, miközben a virtuális gép pillanatképének megadásával gondoskodik a fájlrendszer konzisztenciájáról. Azt javasoljuk, hogy ezt a beállítást állítsa **igaz** értékre, kivéve, ha az alkalmazás nem függ a fsfreeze letiltásával.

6. A parancsfájl-keretrendszer konfigurálva van. Ha a virtuális gép biztonsági mentése már be van állítva, a következő biztonsági mentés elindítja a parancsfájlokat, és elindítja az alkalmazás-konzisztens biztonsági mentést. Ha a virtuális gép biztonsági mentése nincs konfigurálva, állítsa be úgy, hogy az [Azure-beli virtuális gépek biztonsági](https://docs.microsoft.com/azure/backup/backup-azure-vms-first-look-arm) mentését Recovery Services tárolók használatára konfigurálja.

## <a name="troubleshooting"></a>Hibaelhárítás

Győződjön meg arról, hogy a megfelelő naplózást adja hozzá a parancsfájl előtti és utáni parancsfájl írásakor, majd tekintse át a parancsfájl-naplókat a parancsfájlokkal kapcsolatos problémák megoldásához. Ha továbbra is problémákat tapasztal a parancsfájlok futtatásakor, további információért tekintse meg a következő táblázatot.

| Hiba | Hibaüzenet | Javasolt művelet |
| ------------------------ | -------------- | ------------------ |
| Pre-ScriptExecutionFailed |Az előzetes parancsfájl hibát adott vissza, ezért előfordulhat, hogy a biztonsági mentés nem alkalmazás-konzisztens.   | A probléma megoldásához tekintse meg a parancsfájlhoz tartozó hibák naplóit.|  
|   Post-ScriptExecutionFailed |    A post-script olyan hibát adott vissza, amely hatással lehet az alkalmazás állapotára. |    A probléma megoldásához és az alkalmazás állapotának megtekintéséhez tekintse meg a parancsfájlhoz tartozó hibák naplóit. |
| Pre-ScriptNotFound |  Az előzetes parancsfájl nem található a **VMSnapshotScriptPluginConfig. JSON** konfigurációs fájlban megadott helyen. |   Győződjön meg arról, hogy a konfigurációs fájlban megadott elérési úton lévő előzetes parancsfájl szerepel az alkalmazás-konzisztens biztonsági mentés biztosításához.|
| Post-ScriptNotFound | A parancsfájl nem található a **VMSnapshotScriptPluginConfig. JSON** konfigurációs fájlban megadott helyen. |   Győződjön meg arról, hogy a parancsfájl a konfigurációs fájlban megadott elérési úton van, hogy biztosítsa az alkalmazás-konzisztens biztonsági mentést.|
| IncorrectPluginhostFile | A VmSnapshotLinux-bővítményhez tartozó **pluginhost fájlja** -fájl sérült, ezért a parancsfájl előtti és utáni parancsfájl nem futtatható, és a biztonsági mentés nem lesz alkalmazás-konzisztens. | Távolítsa el a **VmSnapshotLinux** bővítményt, és a rendszer automatikusan újratelepíti a következő biztonsági mentéssel a probléma megoldásához. |
| IncorrectJSONConfigFile | A **VMSnapshotScriptPluginConfig. JSON** fájl helytelen, ezért a parancsfájl előtti és utáni parancsfájl nem futtatható, és a biztonsági mentés nem lesz az alkalmazás-konzisztens. | Töltse le a másolatot [](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig) a githubról, és konfigurálja újra. |
| InsufficientPermissionforPre – parancsfájl | A parancsfájlok futtatásához a "root" felhasználónak a fájl tulajdonosának kell lennie, és a fájlnak "700" engedélyekkel kell rendelkeznie (azaz csak a "tulajdonos" legyen "READ", "Write" és "EXECUTE" engedélyekkel). | Győződjön meg arról, hogy a "root" felhasználó a parancsfájl tulajdonosa, és hogy csak a "tulajdonos" rendelkezik "READ", "Write" és "EXECUTE" engedélyekkel. |
| InsufficientPermissionforPost – parancsfájl | A parancsfájlok futtatásához a legfelső szintű felhasználónak a fájl tulajdonosának kell lennie, és a fájlnak "700" engedélyekkel kell rendelkeznie (azaz csak a "tulajdonos" legyen "READ", "Write" és "EXECUTE" engedélyekkel). | Győződjön meg arról, hogy a "root" felhasználó a parancsfájl tulajdonosa, és hogy csak a "tulajdonos" rendelkezik "READ", "Write" és "EXECUTE" engedélyekkel. |
| Pre-ScriptTimeout | Az alkalmazás-konzisztens biztonsági mentés előzetes parancsfájljának végrehajtása időtúllépés miatt megszakítva. | Keresse meg a parancsfájlt, és növelje az időtúllépést a **VMSnapshotScriptPluginConfig. JSON** fájlban, amely a következő helyen található: **/etc/Azure**. |
| Post-ScriptTimeout | Az alkalmazás-konzisztens biztonsági mentési parancsfájl végrehajtása időtúllépés miatt megszakítva. | Keresse meg a parancsfájlt, és növelje az időtúllépést a **VMSnapshotScriptPluginConfig. JSON** fájlban, amely a következő helyen található: **/etc/Azure**. |

## <a name="next-steps"></a>További lépések
[Virtuális gép biztonsági mentésének konfigurálása Recovery Services-tárolóra](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms)
