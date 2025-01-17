---
title: Exchange-kiszolgáló biztonsági mentése az Azure Backupba a System Center 2012 R2 DPM-mel
description: Ismerje meg, hogyan készíthet biztonsági mentést egy Exchange-kiszolgálóról a System Center 2012 R2 DPM használatával Azure Backup
ms.reviewer: kasinh
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: dacurwin
ms.openlocfilehash: 0c8975aed79e78b4bb66ce1516b85ceeb78628e8
ms.sourcegitcommit: d585cdda2afcf729ed943cfd170b0b361e615fae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68689426"
---
# <a name="back-up-an-exchange-server-to-azure-backup-with-system-center-2012-r2-dpm"></a>Exchange-kiszolgáló biztonsági mentése az Azure Backupba a System Center 2012 R2 DPM-mel
Ez a cikk azt ismerteti, hogyan konfigurálható a System Center 2012 R2 Data Protection Manager (DPM) kiszolgáló a Microsoft Exchange Server biztonsági mentésére Azure Backup.  

## <a name="updates"></a>Frissítések
A DPM-kiszolgáló Azure Backuphoz való sikeres regisztrálásához telepítenie kell a System Center 2012 R2 DPM legújabb kumulatív frissítését és a Azure Backup-ügynök legújabb verzióját. Szerezze be a legújabb kumulatív frissítést a [Microsoft Catalog](https://catalog.update.microsoft.com/v7/site/Search.aspx?q=System%20Center%202012%20R2%20Data%20protection%20manager)webhelyről.

> [!NOTE]
> A jelen cikkben szereplő példák esetében a Azure Backup-ügynök 2.0.8719.0 verziója van telepítve, a 6. kumulatív frissítés pedig a System Center 2012 R2 DPM van telepítve.
>
>

## <a name="prerequisites"></a>Előfeltételek
A folytatás előtt győződjön meg arról, hogy teljesülnek a Microsoft Azure Backup használatának előfeltételei a munkaterhelések megóvása érdekében. [](backup-azure-dpm-introduction.md#prerequisites-and-limitations) Az előfeltételek közé tartoznak a következők:

* Létrehozott egy backup-tárolót az Azure-webhelyen.
* Az ügynök és a tároló hitelesítő adatai le lettek töltve a DPM-kiszolgálóra.
* Az ügynök telepítve van a DPM-kiszolgálón.
* A rendszer a tároló hitelesítő adatait használta a DPM-kiszolgáló regisztrálásához.
* Ha az Exchange 2016-et védi, frissítsen a DPM 2012 R2 UR9 vagy újabb verzióra

## <a name="dpm-protection-agent"></a>DPM védelmi ügynök
Ha a DPM védelmi ügynököt az Exchange-kiszolgálóra szeretné telepíteni, kövesse az alábbi lépéseket:

1. Győződjön meg arról, hogy a tűzfalak megfelelően vannak konfigurálva. Lásd: [tűzfal-kivételek konfigurálása az ügynökhöz](https://technet.microsoft.com/library/Hh758204.aspx).
2. Telepítse az ügynököt az Exchange-kiszolgálóra, ehhez kattintson a **felügyeleti > ügynökök** elemre, > telepítse a DPM felügyeleti konzol. A részletes lépésekért lásd [a DPM védelmi ügynök telepítése](https://technet.microsoft.com/library/hh758186.aspx?f=255&MSPPError=-2147217396) című témakört.

## <a name="create-a-protection-group-for-the-exchange-server"></a>Védelmi csoport létrehozása az Exchange-kiszolgálóhoz
1. A DPM Felügyeleti konzol kattintson a **védelem**elemre, majd kattintson az eszköz menüszalagjának **új** elemére az **új védelmi csoport létrehozása** varázsló megnyitásához.
2. A varázsló **üdvözlő** képernyőjén kattintson a **tovább**gombra.
3. A **védelmi csoport típusának kiválasztása** képernyőn válassza a **kiszolgálók** elemet, majd kattintson a **tovább**gombra.
4. Válassza ki a védelemmel ellátni kívánt Exchange Server-adatbázist, és kattintson a **tovább**gombra.

   > [!NOTE]
   > Ha az Exchange 2013-et védi, ellenőrizze az [exchange 2013](https://technet.microsoft.com/library/dn751029.aspx)előfeltételeit.
   >
   >

    A következő példában az Exchange 2010-adatbázis van kiválasztva.

    ![Csoporttagok kiválasztása](./media/backup-azure-backup-exchange-server/select-group-members.png)
5. Válassza ki az adatvédelmi módszert.

    Nevezze el a védelmi csoportot, majd válassza az alábbi lehetőségek egyikét:

   * Rövid távú védelmet szeretnék a lemezzel.
   * Online védelmet szeretnék.
6. Kattintson a **Tovább** gombra.
7. Jelölje be az **eseutil futtatása az adatok sértetlenségének ellenőrzése** lehetőséget, ha szeretné megtekinteni az Exchange Server-adatbázisok integritását.

    Ha ezt a beállítást választja, a biztonsági mentés konzisztencia-ellenőrzése a DPM-kiszolgálón fog futni, hogy elkerülje az **eseutil** parancsnak az Exchange-kiszolgálón való futtatásával generált I/O-forgalmat.

   > [!NOTE]
   > A beállítás használatához át kell másolnia az ESE. dll és az Eseutil. exe fájlokat a C:\Program Files\Microsoft System Center 2012 R2\DPM\DPM\bin könyvtárba a DPM-kiszolgálón. Ellenkező esetben a rendszer a következő hibát váltja ki:  
   > ![Eseutil-hiba](./media/backup-azure-backup-exchange-server/eseutil-error.png)
   >
   >
8. Kattintson a **Tovább** gombra.
9. Válassza ki a **másolási biztonsági mentés**adatbázisát, majd kattintson a **tovább**gombra.

   > [!NOTE]
   > Ha nem a "teljes biztonsági mentés" lehetőséget választja egy adatbázis legalább egy DAG-példányához, a rendszer nem csonkolja a naplókat.
   >
   >
10. Konfigurálja a **rövid távú biztonsági mentés**céljait, és kattintson a **tovább**gombra.
11. Tekintse át a rendelkezésre álló lemezterületet, majd kattintson a **tovább**gombra.
12. Válassza ki azt az időpontot, amikor a DPM-kiszolgáló létrehozza a kezdeti replikálást, majd kattintson a **tovább**gombra.
13. Válassza ki a konzisztencia-ellenőrzési beállításokat, majd kattintson a **tovább**gombra.
14. Válassza ki azt az adatbázist, amelyről biztonsági másolatot szeretne készíteni az Azure-ba, majd kattintson a **tovább**gombra. Példa:

    ![Online védelmi adatértékek meghatározása](./media/backup-azure-backup-exchange-server/specify-online-protection-data.png)
15. Adja meg a **Azure Backup**ütemtervét, majd kattintson a **tovább**gombra. Példa:

    ![Online biztonsági mentési ütemterv megadása](./media/backup-azure-backup-exchange-server/specify-online-backup-schedule.png)

    > [!NOTE]
    > Megjegyzés: az online helyreállítási pontok expressz teljes helyreállítási pontokon alapulnak. Ezért az online helyreállítási pontot az expressz teljes helyreállítási ponthoz megadott idő után kell ütemeznie.
    >
    >
16. Konfigurálja **Azure Backup**adatmegőrzési szabályát, majd kattintson a **tovább**gombra.
17. Válasszon online replikálási lehetőséget, majd kattintson a **tovább**gombra.

    Ha nagyméretű adatbázissal rendelkezik, hosszú időt is igénybe vehet, hogy a rendszer létrehozza a kezdeti biztonsági mentést a hálózaton keresztül. A probléma elkerüléséhez létrehozhat egy offline biztonsági mentést.  

    ![Online adatmegőrzési szabály meghatározása](./media/backup-azure-backup-exchange-server/specify-online-retention-policy.png)
18. Erősítse meg a beállításokat, majd kattintson a **csoport létrehozása**elemre.
19. Kattintson a **Bezárás** gombra.

## <a name="recover-the-exchange-database"></a>Az Exchange-adatbázis helyreállítása
1. Exchange-adatbázis helyreállításához kattintson a DPM Felügyeleti konzol **helyreállítás** elemére.
2. Keresse meg a helyreállítani kívánt Exchange-adatbázist.
3. Válasszon ki egy online helyreállítási pontot a *helyreállítási idő* legördülő listából.
4. Kattintson **a** helyreállítás gombra a **helyreállítási varázsló**elindításához.

Az online helyreállítási pontok esetében öt helyreállítási típus létezik:

* **Helyreállítás az eredeti Exchange-kiszolgáló helyére:** A rendszer az eredeti Exchange-kiszolgálóra állítja vissza az adatgyűjtést.
* **Helyreállítás egy másik adatbázisba egy Exchange-kiszolgálón:** Az adatgyűjtést egy másik Exchange-kiszolgálón lévő másik adatbázisba fogja helyreállítani.
* **Helyreállítás helyreállítási adatbázisba:** Az adatgyűjtés egy Exchange helyreállítási adatbázisba (RDB) lesz helyreállítva.
* **Másolás hálózati mappába:** Az Adathelyreállítás egy hálózati mappába történik.
* **Másolás szalagra:** Ha szalagos kódtár vagy önálló szalagos meghajtó van csatlakoztatva és konfigurálva a DPM-kiszolgálón, a helyreállítási pont egy szabad szalagra lesz másolva.

    ![Online replikáció kiválasztása](./media/backup-azure-backup-exchange-server/choose-online-replication.png)

## <a name="next-steps"></a>További lépések
* [Azure Backup GYIK](backup-azure-backup-faq.md)
