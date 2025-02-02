---
title: Az Azure-ban tárolt adatvisszaállítás Windows Server vagy Windows rendszerű számítógépre
description: Megtudhatja, hogyan állíthatja vissza az Azure-ban tárolt információkat Windows Server vagy Windows rendszerű számítógépre.
ms.reviewer: saurse
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 9/7/2018
ms.author: dacurwin
ms.openlocfilehash: c8e0ddb788d5e155bfaa511b1012cc8d8e62aab5
ms.sourcegitcommit: d585cdda2afcf729ed943cfd170b0b361e615fae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68689096"
---
# <a name="restore-files-to-windows-by-using-the-azure-resource-manager-deployment-model"></a>Fájlok visszaállítása a Windowsba az Azure Resource Manager üzemi modell használatával

Ez a cikk azt ismerteti, hogyan lehet visszaállítani az adatok biztonsági mentési tárból. Az adatok visszaállításához használja az Microsoft Azure Recovery Services (MARS) ügynök adatok helyreállítása varázslóját. A következőket teheti:

* Állítsa vissza az adatokat ugyanarra a gépre, amelyről a biztonsági másolatok készültek.
* Adatok visszaállítása egy másik gépre.

Az azonnali visszaállítás funkcióval írható helyreállítási pontok pillanatképét csatlakoztathatja helyreállítási kötetként. Ezután felfedezheti a helyreállítási kötetet, és átmásolhatja a fájlokat egy helyi számítógépre, ezzel szelektív módon helyreállíthatja a fájlokat.

> [!NOTE]
> A [januári 2017 Azure Backup frissítésre](https://support.microsoft.com/en-us/help/3216528?preview) van szükség, ha az adatvisszaállításhoz szeretné használni az azonnali visszaállítást. Emellett a biztonsági mentési adattárakat a támogatási cikkben felsorolt területi beállításokban kell védeni. Az azonnali visszaállítást támogató területi beállítások legújabb listáját a [január 2017 Azure Backup Update webhelyen](https://support.microsoft.com/en-us/help/3216528?preview) találhatja meg.
>

Az Azure Portal Recovery Services tárolókkal való azonnali visszaállítást használhat. Ha biztonsági mentési tárolókban tárolta az adattárolást, a rendszer átalakította Recovery Services-tárolóra. Ha azonnali visszaállítást szeretne használni, töltse le a MARS-frissítést, és kövesse az azonnali visszaállítást megemlítő eljárásokat.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="use-instant-restore-to-recover-data-to-the-same-machine"></a>Az adatok ugyanarra a gépre való helyreállításához használja az azonnali visszaállítást

Ha véletlenül törölt egy fájlt, és vissza szeretné állítani ugyanarra a gépre (amelyről a biztonsági mentés készül), a következő lépések segítenek az adatok helyreállításában.

1. Nyissa meg a **Microsoft Azure Backup** beépülő modult. Ha nem tudja, hol telepítette a beépülő modult, keresse meg **Microsoft Azure Backup**a számítógépet vagy a kiszolgálót.

    Az asztali alkalmazásnak meg kell jelennie a keresési eredmények között.

2. A varázsló elindításához kattintson az **adatok helyreállítása** elemre.

    ![Képernyőkép a Azure Backupről, az adatok kiemelésével](./media/backup-azure-restore-windows-server/recover.png)

3. A **első lépések** lapon a**következőt**válassza a **kiszolgáló (`<server name>`)**  > elemre, ha az adatvisszaállítást ugyanarra a kiszolgálóra vagy számítógépre szeretné visszaállítani.

    ![Képernyőfelvétel az adatok helyreállítása varázsló Első lépések oldaláról](./media/backup-azure-restore-windows-server/samemachine_gettingstarted_instantrestore.png)

4. A **helyreállítási mód kiválasztása** lapon válassza az **egyes fájlok és mappák** > lehetőséget a **Tovább gombra**.

    ![Képernyőkép az adatok helyreállítása varázsló helyreállítási mód kiválasztása lapján](./media/backup-azure-restore-windows-server/samemachine_selectrecoverymode_instantrestore.png)
   > [!IMPORTANT]
   > Az egyes fájlok és mappák visszaállításának lehetősége a .NET-keretrendszer 4.5.2-es vagy újabb verziójára van szükség. Ha nem látja az **egyes fájlok és mappák** beállítást, a .NET-keretrendszert a 4.5.2-es vagy újabb verzióra kell frissíteni, majd próbálkozzon újra.

   > [!TIP]
   > Az **egyes fájlok és mappák** lehetőséggel gyorsan hozzáférhet a helyreállítási ponthoz tartozó adatfájlokhoz. Alkalmas az egyes fájlok helyreállítására, a mérete nem haladhatja meg a 80 GB-ot, és az átviteli vagy a másolási sebesség akár 6 MBps is lehet. A **Volume (kötet** ) beállítással egy adott köteten található összes biztonsági másolati elem helyreállítható. Ez a beállítás gyorsabb átviteli sebességet biztosít (akár 60 MBps), ami ideális a nagyméretű adatok vagy a teljes kötetek helyreállításához.

5. A **kötet és dátum kiválasztása** lapon válassza ki azt a kötetet, amely tartalmazza a visszaállítani kívánt fájlokat és mappákat.

    A naptárban válasszon ki egy helyreállítási pontot. A **félkövérrel szedett** dátumok azt jelzik, hogy legalább egy helyreállítási pont rendelkezésre áll. Ha egy adott napon belül több helyreállítási pont is elérhető, válassza ki az adott helyreállítási pontot az **idő** legördülő menüből.

    ![Képernyőfelvétel az adatok helyreállítása varázsló kötet és dátum kiválasztása lapján](./media/backup-azure-restore-windows-server/samemachine_selectvolumedate_instantrestore.png)

6. Miután kiválasztotta a helyreállítási pontot a visszaállításhoz, válassza a **Csatlakoztatás**lehetőséget.

    Azure Backup csatlakoztatja a helyi helyreállítási pontot, és helyreállítási kötetként használja.

7. A **Fájlok tallózása és helyreállítása** lapon a **Tallózás** gombra kattintva nyissa meg a Windows Intézőt, és keresse meg a kívánt fájlokat és mappákat.

    ![Az adatok helyreállítása varázsló képernyőképe – fájlok tallózása és helyreállítása lap](./media/backup-azure-restore-windows-server/samemachine_browserecover_instantrestore.png)


8. A Windows Intézőben másolja a visszaállítani kívánt fájlokat és mappákat, majd illessze be őket a kiszolgáló vagy a számítógép helyi helyére. A fájlokat közvetlenül a helyreállítási kötetről is megnyithatja vagy továbbíthatja, és ellenőrizheti, hogy a megfelelő verziókat kívánja-e helyreállítani.

    ![Képernyőkép a Windows Intézőben, a másolás kiemelve](./media/backup-azure-restore-windows-server/samemachine_copy_instantrestore.png)


9. Ha elkészült, a **Fájlok tallózása és helyreállítása** lapon válassza a leválasztás lehetőséget. Ezután az **Igen** gombra kattintva erősítse meg, hogy le kívánja választani a kötetet.

    ![Az adatok helyreállítása varázsló képernyőképe – fájlok tallózása és helyreállítása lap](./media/backup-azure-restore-windows-server/samemachine_unmount_instantrestore.png)

    > [!Important]
    > Ha nem választja ki a **Leválasztás**lehetőséget, a helyreállítási kötet a csatlakoztatása után 6 órán keresztül továbbra is csatlakoztatva marad. A csatlakoztatási idő azonban akár legfeljebb 24 óráig is meghosszabbítható egy folyamatban lévő fájl másolásakor. A kötet csatlakoztatása közben nem fut biztonsági mentési művelet. A kötet csatlakoztatásakor ütemezett biztonsági mentési művelet a helyreállítási kötet leválasztása után fog futni.
    >


## <a name="use-instant-restore-to-restore-data-to-an-alternate-machine"></a>Az egy másik gépre való visszaállításhoz használja az azonnali visszaállítást
Ha a teljes kiszolgáló elvész, a Azure Backup adatokat egy másik gépre is helyreállíthatja. Az alábbi lépések bemutatják a munkafolyamatot.


Ezek a lépések a következő terminológiát tartalmazzák:

* *Forrásoldali gép* – az eredeti gép, amelyről a biztonsági mentés készült, és amely jelenleg nem érhető el.
* *Célszámítógép* – az a gép, amelyre az Adathelyreállítás történik.
* *Minta* -tár – az a Recovery Services tároló, amelybe a forrásszámítógép és a célszámítógép regisztrálva van. <br/>

> [!NOTE]
> A biztonsági mentések nem állíthatók vissza olyan célszámítógépre, amely az operációs rendszer korábbi verzióját futtatja. Például egy Windows 7 rendszerű számítógépről készített biztonsági másolat visszaállítható Windows 7 (vagy újabb) rendszerű számítógépen. Windows 8 rendszerű számítógépről készített biztonsági másolat nem állítható vissza Windows 7 rendszerű számítógépre.
>
>

1. Nyissa meg a **Microsoft Azure Backup** beépülő modult a célszámítógépen.

2. Győződjön meg arról, hogy a célszámítógép és a forrásszámítógép ugyanahhoz a Recovery Services-tárolóhoz van regisztrálva.

3. Válassza az **adatok helyreállítása** lehetőséget az **adatok helyreállítása varázsló**megnyitásához.

    ![Képernyőkép a Azure Backupről, az adatok kiemelésével](./media/backup-azure-restore-windows-server/recover.png)

4. A **első lépések** lapon válasszon **egy másik kiszolgálót**.

    ![Képernyőfelvétel az adatok helyreállítása varázsló Első lépések oldaláról](./media/backup-azure-restore-windows-server/alternatemachine_gettingstarted_instantrestore.png)

5. Adja meg a tár hitelesítő adatait tartalmazó fájlt, amely megfelel a mintatartó tárolónak, majd kattintson a **Tovább gombra**.

    Ha a tároló hitelesítőadat-fájlja érvénytelen (vagy lejárt), töltse le a tárolóban lévő új hitelesítőadat-fájlt a Azure Portal. A tár érvényes hitelesítő adatainak megadását követően megjelenik a megfelelő biztonságimásolat-tároló neve.


6. A **biztonsági mentési kiszolgáló kiválasztása** lapon válassza ki a forrásszámítógép listáját a megjelenített gépek listájából, és adja meg a jelszót. Ezután kattintson a **Tovább** gombra.

    ![Képernyőkép az adatok helyreállítása varázsló biztonsági mentési kiszolgáló kiválasztása lapján](./media/backup-azure-restore-windows-server/alternatemachine_selectmachine_instantrestore.png)

7. A **helyreállítási mód kiválasztása** lapon válassza ki az **egyes fájlok és mappák** > lehetőséget a**Tovább gombra**.

    ![Képernyőkép az adatok helyreállítása varázsló helyreállítási mód kiválasztása lapján](./media/backup-azure-restore-windows-server/alternatemachine_selectrecoverymode_instantrestore.png)

8. A **kötet és dátum kiválasztása** lapon válassza ki azt a kötetet, amely tartalmazza a visszaállítani kívánt fájlokat és mappákat.

    A naptárban válasszon ki egy helyreállítási pontot. A **félkövérrel szedett** dátumok azt jelzik, hogy legalább egy helyreállítási pont rendelkezésre áll. Ha egy adott napon belül több helyreállítási pont is elérhető, válassza ki az adott helyreállítási pontot az **idő** legördülő menüből.

    ![Képernyőfelvétel az adatok helyreállítása varázsló kötet és dátum kiválasztása lapján](./media/backup-azure-restore-windows-server/alternatemachine_selectvolumedate_instantrestore.png)

9. Válassza a **Csatlakoztatás** lehetőséget a helyreállítási pont helyi csatlakoztatásához helyreállítási kötetként a célszámítógépen.

10. A **Fájlok tallózása és helyreállítása** lapon a **Tallózás** gombra kattintva nyissa meg a Windows Intézőt, és keresse meg a kívánt fájlokat és mappákat.

    ![Az adatok helyreállítása varázsló képernyőképe – fájlok tallózása és helyreállítása lap](./media/backup-azure-restore-windows-server/alternatemachine_browserecover_instantrestore.png)

11. A Windows Intézőben másolja a fájlokat és mappákat a helyreállítási kötetről, és illessze be őket a célszámítógép helyére. A fájlokat közvetlenül a helyreállítási kötetről is megnyithatja vagy továbbíthatja, és ellenőrizheti, hogy a megfelelő verziók helyreállítva lettek-e.

    ![Képernyőkép a Windows Intézőben, a másolás kiemelve](./media/backup-azure-restore-windows-server/alternatemachine_copy_instantrestore.png)

12. Ha elkészült, a **Fájlok tallózása és helyreállítása** lapon válassza a leválasztás lehetőséget. Ezután az **Igen** gombra kattintva erősítse meg, hogy le kívánja választani a kötetet.

    ![Az adatok helyreállítása varázsló képernyőképe – fájlok tallózása és helyreállítása lap](./media/backup-azure-restore-windows-server/alternatemachine_unmount_instantrestore.png)

    > [!Important]
    > Ha nem választja ki a **Leválasztás**lehetőséget, a helyreállítási kötet a csatlakoztatása után 6 órán keresztül továbbra is csatlakoztatva marad. A csatlakoztatási idő azonban akár legfeljebb 24 óráig is meghosszabbítható egy folyamatban lévő fájl másolásakor. A kötet csatlakoztatása közben nem fut biztonsági mentési művelet. A kötet csatlakoztatásakor ütemezett biztonsági mentési művelet a helyreállítási kötet leválasztása után fog futni.
    >

## <a name="next-steps"></a>További lépések
Most, hogy helyreállítja a fájlokat és mappákat, kezelheti [a biztonsági mentéseket](backup-azure-manage-windows-server.md).
