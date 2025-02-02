---
title: Azure-beli virtuális gépek biztonsági mentése
description: Ismerje meg az Azure virtuális gépek biztonsági mentését, és jegyezze fel az ajánlott eljárásokat.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: dacurwin
ms.openlocfilehash: 72ab33cd280892ac6de827986e21e04672e58960
ms.sourcegitcommit: acffa72239413c62662febd4e39ebcb6c6c0dd00
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/12/2019
ms.locfileid: "68951849"
---
# <a name="an-overview-of-azure-vm-backup"></a>Az Azure virtuális gépek biztonsági mentésének áttekintése

Ez a cikk azt ismerteti, hogy az [Azure Backup szolgáltatás](backup-introduction-to-azure-backup.md) hogyan készít biztonsági másolatot az Azure Virtual Machines-ről (VM).

## <a name="backup-process"></a>Biztonsági mentési folyamat

A Azure Backup az Azure-beli virtuális gépek biztonsági mentését hajtja végre:

1. A biztonsági mentésre kijelölt Azure-beli virtuális gépek esetében a Azure Backup a megadott biztonsági mentési ütemtervnek megfelelően elindítja a biztonsági mentési feladatot.
1. Az első biztonsági mentés során a rendszer egy biztonsági mentési bővítményt telepít a virtuális gépre, ha a virtuális gép fut.
    - Windows rendszerű virtuális gépek esetén a _VMSnapshot_ bővítmény telepítve van.
    - Linux rendszerű virtuális gépek esetén a _VMSnapshotLinux_ bővítmény telepítve van.
1. A rendszert futtató Windows rendszerű virtuális gépek esetén a biztonsági mentési koordinátákat a Windows Kötet árnyékmásolata szolgáltatás (VSS) segítségével végezze el a virtuális gép alkalmazással konzisztens pillanatképének elkészítéséhez.
    - Alapértelmezés szerint a Backup teljes VSS biztonsági mentést készít.
    - Ha a biztonsági mentés nem tud alkalmazás-konzisztens pillanatképet készíteni, akkor a rendszer az alapul szolgáló tárterület fájl-konzisztens pillanatképét veszi igénybe (mivel a virtuális gép leállításakor nem történik alkalmazás-írás).
1. Linux rendszerű virtuális gépek esetén a Backup fájl-konzisztens biztonsági mentést készít. Az alkalmazással konzisztens Pillanatképek esetében manuálisan kell testreszabnia a parancsfájlok előtti és utáni parancsfájlokat.
1. A biztonsági mentést követően a pillanatkép átviszi az adattárolóba.
    - A biztonsági mentést úgy optimalizálták, hogy az egyes virtuálisgép-lemezek párhuzamos biztonsági mentését végzi.
    - A biztonsági mentés alatt álló minden lemez esetében Azure Backup beolvassa a lemezen lévő blokkokat, és csak azokat az adatblokkokat azonosítja és továbbítja, amelyek az előző biztonsági mentés óta módosultak (a különbözetet).
    - Előfordulhat, hogy a pillanatkép-adatok nem másolódnak azonnal a tárba. A csúcsidőben több órát is igénybe vehet. A napi biztonsági mentési szabályzatok esetében a virtuális gép teljes biztonsági mentési ideje kevesebb, mint 24 óra lesz.
 1. A Windows rendszerű virtuális gépeken a Azure Backup engedélyezése után végrehajtott módosítások a következők:
    -   Microsoft Visual C++ 2013 Újraterjeszthető csomag (x64) – a 12.0.40660 telepítve van a virtuális gépen.
    -   A Kötet árnyékmásolata szolgáltatás (VSS) indítási típusa a kézi értékről automatikusra módosult
    -   IaaSVmProvider Windows-szolgáltatás hozzáadva

1. Amikor az adatátvitel befejeződött, a rendszer eltávolítja a pillanatképet, és létrehoz egy helyreállítási pontot.

![Azure-beli virtuális gépek biztonsági mentési architektúrája](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

## <a name="encryption-of-azure-vm-backups"></a>Azure-beli virtuális gépek biztonsági másolatainak titkosítása

Az Azure-beli virtuális gépek Azure Backup-vel történő biztonsági mentésekor a virtuális gépek Storage Service Encryption (SSE) inaktív állapotban vannak titkosítva. A Azure Backup a Azure Disk Encryption használatával titkosított Azure-beli virtuális gépek biztonsági mentését is elvégezheti.

**Titkosítás** | **Részletek** | **Támogatás**
--- | --- | ---
**Azure Disk Encryption** | Azure Disk Encryption titkosítja az operációs rendszer és az adatlemezeket az Azure-beli virtuális gépekhez.<br/><br/> A Azure Disk Encryption a BitLocker titkosítási kulcsaival (BEKs) integrálható, amelyek titkos kulccsal rendelkeznek a Key vaultban. A Azure Disk Encryption Azure Key Vault kulcs-titkosítási kulcsokkal (KEK) is integrálva van. | Azure Backup támogatja a kizárólag BEKs-mel titkosított felügyelt és nem felügyelt Azure-beli virtuális gépek biztonsági mentését, illetve a BEKs és a KEK együttes használatát.<br/><br/> Mind a BEKs, mind a KEK biztonsági mentése és titkosítása is megtörténik.<br/><br/> Mivel a KEK és a BEKs biztonsági mentése történik, a szükséges engedélyekkel rendelkező felhasználók visszaállíthatják a kulcsokat és a titkokat a kulcstartóba, ha szükséges. Ezek a felhasználók a titkosított virtuális gépet is helyreállítják.<br/><br/> A titkosított kulcsokat és titkos kulcsokat nem lehet beolvasni jogosulatlan felhasználók vagy az Azure.
**SSE** | Az SSE használatával az Azure Storage az adatok tárolása előtt automatikusan titkosítja a titkosítást. Az Azure Storage a beolvasás előtt visszafejti az adatokkal. | A Azure Backup az SSE-t használja az Azure-beli virtuális gépek Rest titkosításához.

Felügyelt és nem felügyelt Azure-beli virtuális gépek esetén a Backup a BEKs-mel titkosított virtuális gépeket, illetve a BEKs-mel és a KEK-szel együtt titkosított virtuális gépeket

A biztonsági másolatban szereplő BEKs (Secrets) és KEK (kulcsok) titkosítva vannak. Csak akkor olvashatók és használhatók, ha a jogosult felhasználók a Key vaultba visszaállnak. Sem a jogosulatlan felhasználók, sem az Azure nem tudja olvasni vagy használni a biztonsági másolatok által készített kulcsokat vagy titkos kódokat.

A BEKs is biztonsági másolat készül. Tehát ha a BEKs elvesznek, a jogosult felhasználók visszaállíthatják a BEKs a kulcstartóba, és helyreállítják a titkosított virtuális gépeket. Csak a megfelelő szintű engedélyekkel rendelkező felhasználók hozhatnak létre és állíthatnak vissza titkosított virtuális gépeket, kulcsokat és titkos kódokat.

## <a name="snapshot-creation"></a>Pillanatkép létrehozása

A Azure Backup a biztonsági mentés ütemtervének megfelelően hozza a pillanatképeket.

- **Windows rendszerű virtuális gépek:** Windows rendszerű virtuális gépek esetén a Backup szolgáltatás a VSS-vel koordinálja a virtuálisgép-lemezek alkalmazás-konzisztens pillanatképét.

  - Alapértelmezés szerint a Azure Backup a teljes VSS-biztonsági mentést végzi. [További információk](https://blogs.technet.com/b/filecab/archive/2008/05/21/what-is-the-difference-between-vss-full-backup-and-vss-copy-backup-in-windows-server-2008.aspx).
  - Ha úgy szeretné módosítani a beállítást, hogy Azure Backup a VSS-t másolja, állítsa be a következő beállításkulcsot a parancssorból:

    **REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgent" /v USEVSSCOPYBACKUP /t REG_SZ /d TRUE /f**

- **Linux rendszerű virtuális gépek:** A Linux rendszerű virtuális gépek alkalmazás-konzisztens pillanatképének készítéséhez használja a Linux előtti és parancsfájl utáni keretrendszert a saját egyéni parancsfájljainak megírásához a konzisztencia biztosítása érdekében.

  - Azure Backup csak az Ön által írt Pre/post parancsfájlokat hívja meg.
  - Ha az előkészítő parancsfájlok és a parancsfájlok utáni végrehajtás sikeresen befejeződött, Azure Backup a helyreállítási pontot alkalmazás-konzisztensként jelöli meg. Ha azonban egyéni parancsfájlokat használ, az alkalmazás konzisztenciája végső soron a felelős.
  - [További](backup-azure-linux-app-consistent.md) információ a parancsfájlok konfigurálásáról.

### <a name="snapshot-consistency"></a>Pillanatkép konzisztenciája

A következő táblázat a pillanatkép-konzisztencia különböző típusait ismerteti:

**Snapshot** | **Részletek** | **Helyreállítási** | **Figyelembe**
--- | --- | --- | ---
**Application-consistent** | Az alkalmazással konzisztens biztonsági másolatok rögzítik a memória tartalmát és a függőben lévő I/O-műveleteket. Az alkalmazás-konzisztens Pillanatképek VSS-író (vagy Linux előtti/post szkriptek) használatával biztosítják az alkalmazásadatok egységességét a biztonsági mentés előtt. | Ha egy virtuális gépet egy alkalmazással konzisztens pillanatképtel állít le, a virtuális gép elindul. Nincs adatsérülés vagy adatvesztés. Az alkalmazások konzisztens állapotban kezdődnek. | Windows: Az összes VSS-író sikeresen befejeződött<br/><br/> Linux: Az előzetes/post parancsfájlokat a rendszer konfigurálta és sikeresen elvégezte
**Fájlrendszer-konzisztens** | A fájlrendszerrel konzisztens biztonsági másolatok következetességet biztosítanak azáltal, hogy az összes fájlról pillanatképet készítenek.<br/><br/> | Ha olyan virtuális gépet állít vissza, amely fájlrendszerrel konzisztens pillanatképet használ, a virtuális gép elindul. Nincs adatsérülés vagy adatvesztés. Az alkalmazásoknak saját "felerősítő" mechanizmust kell alkalmazniuk, hogy a visszaállított adategységek konzisztensek legyenek. | Windows: Egyes VSS-írók sikertelenek voltak <br/><br/> Linux: Alapértelmezett (ha a Pre/post parancsfájl nincs konfigurálva vagy sikertelen)
**Crash-consistent** | Az összeomlás-konzisztens Pillanatképek általában akkor fordulnak elő, ha egy Azure-beli virtuális gép le van állítva a biztonsági mentés időpontjában. A rendszer csak azokat az adatmennyiségeket rögzíti, amelyek már léteznek a lemezen a biztonsági mentés során.<br/><br/> Az összeomlás-konzisztens helyreállítási pont nem garantálja az operációs rendszer vagy az alkalmazás adatkonzisztenciáját. | Bár nincsenek garanciák, a virtuális gép általában elindul, majd egy lemez-ellenőrzési folyamattal javítja a sérülési hibákat. Olyan memóriában tárolt adatok vagy írási műveletek, amelyeket nem a lemezre vittek át az összeomlás elvesztése előtt. Az alkalmazások saját adatellenőrzést hajtanak végre. Egy adatbázis-alkalmazás például az ellenőrzéshez használhatja a tranzakciónaplóját. Ha a tranzakciónapló olyan bejegyzéseket tartalmaz, amelyek nem szerepelnek az adatbázisban, akkor az adatbázis szoftvere az adatok konzisztenciája előtt Visszagörgeti a tranzakciókat. | A virtuális gép leállítási állapotban van

## <a name="backup-and-restore-considerations"></a>Biztonsági mentési és visszaállítási megfontolások

**Figyelembe** | **Részletek**
--- | ---
**Lemez** | A VM-lemezek biztonsági mentése párhuzamos. Ha például egy virtuális gépnek négy lemeze van, a Backup szolgáltatás mind a négy lemezről párhuzamosan kísérli meg a biztonsági mentést. A biztonsági mentés növekményes (csak módosult adatértékek).
**Ütemezési** |  A biztonsági mentési forgalom csökkentése érdekében készítsen biztonsági másolatot a különböző virtuális gépekről a nap különböző pontjain, és győződjön meg arról, hogy az idő nem fedi át egymást. A virtuális gépek biztonsági mentése egy időben a forgalmi torlódásokat okoz.
**Biztonsági mentések előkészítése** | Tartsa szem előtt a biztonsági mentés előkészítéséhez szükséges időt. Az előkészítési idő magában foglalja a biztonsági mentési bővítmény telepítését vagy frissítését, valamint a pillanatkép aktiválását a biztonsági mentési ütemtervnek megfelelően.
**Adatátvitel** | Vegye figyelembe a Azure Backup számára szükséges időt az előző biztonsági mentés növekményes változásainak azonosításához.<br/><br/> A növekményes biztonsági mentésben a Azure Backup meghatározza a módosításokat a blokk ellenőrzőösszegének kiszámításával. Ha egy blokkot módosítanak, a rendszer a tárolóba való átvitelre van megjelölve. A szolgáltatás elemzi az azonosított blokkokat, így az átvinni kívánt adatok mennyiségét tovább csökkentheti. Az összes módosult blokk kiértékelése után Azure Backup továbbítja a módosításokat a tárolóba.<br/><br/> A pillanatkép elkészítése és a tárolóba való másolása között késés lehet.<br/><br/> Csúcsidőben a biztonsági mentések feldolgozása akár nyolc órát is igénybe vehet. A virtuális gép biztonsági mentésének ideje a napi biztonsági mentésnél kevesebb, mint 24 óra lesz.
**Kezdeti biztonsági mentés** | Bár a növekményes biztonsági mentések teljes biztonsági mentésének ideje kevesebb, mint 24 óra, előfordulhat, hogy az első biztonsági mentés nem feltétlenül igaz. A kezdeti biztonsági mentéshez szükséges idő az adatok méretétől és a biztonsági mentés feldolgozásának időpontjától függ.
**Várólista visszaállítása** | Azure Backup folyamatok egyszerre több Storage-fiókból is visszaállítják a feladatokat, és a visszaállítási kérelmeket egy várólistába helyezi.
**Másolat visszaállítása** | A visszaállítási folyamat során az adatok a tárolóból a Storage-fiókba lesznek másolva.<br/><br/> A teljes visszaállítási idő a másodpercenkénti I/O-műveletektől (IOPS) és a Storage-fiók átviteli sebességtől függ.<br/><br/> A másolási idő csökkentése érdekében válasszon ki egy olyan Storage-fiókot, amely nincs betöltve más alkalmazások írásával és olvasásával.

### <a name="backup-performance"></a>Biztonsági mentési teljesítmény

Ezek a gyakori forgatókönyvek a teljes biztonsági mentés idejére hatással lehetnek:

- **Új lemez hozzáadása egy védett Azure-beli virtuális géphez:** Ha egy virtuális gép növekményes biztonsági mentést készít, és új lemezt ad hozzá, a biztonsági mentés ideje növekedni fog. A teljes biztonsági mentési idő az új lemez kezdeti replikálása, valamint a meglévő lemezek különbözeti replikációja miatt akár 24 óránál is tarthat.
- **Töredezett lemezek:** A biztonsági mentési műveletek gyorsabbak, ha a lemez változása összefüggő. Ha a módosítások szét vannak osztva és töredezettek egy lemezen, a biztonsági mentés lassabb lesz.
- **Lemez elváltozása:** Ha a növekményes biztonsági mentés alatt álló védett lemezek napi adatváltozása meghaladja a 200 GB-ot, a biztonsági mentés hosszú időt is igénybe vehet (több mint nyolc óra) a befejezéshez.
- **Biztonsági mentési verziók:** A biztonsági mentés legújabb verziója (vagyis az azonnali visszaállítás verziója) egy optimalizált folyamatot használ, mint a változások azonosításához szükséges ellenőrzőösszeg-összehasonlítás. Ha azonban azonnali visszaállítást használ, és törölt egy biztonsági mentési pillanatképet, a biztonsági mentés ellenőrzőösszeg-összehasonlításra vált. Ebben az esetben a biztonsági mentési művelet 24 óránál hosszabb időt vesz igénybe (vagy sikertelen).

## <a name="best-practices"></a>Ajánlott eljárások

Ha virtuális gépek biztonsági mentését konfigurálja, javasoljuk, hogy kövesse a következő eljárásokat:

- Módosíthatja a házirendben beállított alapértelmezett ütemezett időpontokat. Ha például a házirendben az alapértelmezett idő 12:00, az időzítést több percen belül növelni kell, hogy az erőforrások optimálisan használhatók legyenek.
- Ha egyetlen tárolóból állítja vissza a virtuális gépeket, javasoljuk, hogy használjon különböző [általános célú v2 Storage](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade) -fiókokat annak biztosítására, hogy a cél Storage-fiók ne kapjon szabályozást. Az egyes virtuális gépeknek például eltérő Storage-fiókkal kell rendelkezniük. Ha például 10 virtuális gép van visszaállítva, használjon 10 különböző Storage-fiókot.
- A Premium Storage szolgáltatást használó virtuális gépek biztonsági mentését azonnali visszaállítással ajánlott kiosztani a teljes lefoglalt tárterület *50%-os* szabad területét, amely **csak** az első biztonsági mentéshez szükséges. Az első biztonsági mentés befejezése után az 50%-os szabad terület nem követelmény a biztonsági mentéshez.
- Az általános célú v1 tárolási réteg (snapshot) visszaállítása percek alatt elvégezhető, mivel a pillanatkép ugyanabban a Storage-fiókban található. Az általános célú v2 tárolási rétegből (tárolóból) való visszaállítás akár órákig is elvégezhető. Azokban az esetekben, amikor az adat elérhető az általános célú v1-tárolóban, javasoljuk, hogy az [azonnali visszaállítás](backup-instant-restore-capability.md) funkciót használja a gyorsabb visszaállításhoz. (Ha az adatok visszaállítását egy tárolóból kell visszaállítani, a rendszer több időt vesz igénybe.)
- A lemezek tárolási fiókra vonatkozó korlátozása attól függ, hogy milyen mértékben fér hozzá a lemezek a szolgáltatásként szolgáló infrastruktúra-(IaaS-) virtuális gépen futó alkalmazások számára. Általános gyakorlatként, ha 5 – 10 lemez vagy több van jelen egyetlen Storage-fiókban, akkor a terhelést úgy egyenlítheti ki, hogy egyes lemezeket külön Storage-fiókokra helyez át.

## <a name="backup-costs"></a>Biztonsági mentési költségek

A Azure Backupekkel biztonsági mentést folytató Azure-beli virtuális gépek [Azure Backup díjszabásra](https://azure.microsoft.com/pricing/details/backup/)vonatkoznak.

A számlázás nem indul el, amíg az első sikeres biztonsági mentés be nem fejeződik. Ekkor megkezdődik a tárterület és a védett virtuális gépek számlázása. A számlázás addig tart, amíg a virtuális gép biztonsági mentési adatai egy tárolóban tárolódnak. Ha leállítja a virtuális gép védelmét, de a virtuális gép biztonsági mentési adatai egy tárolóban találhatóak, a számlázás továbbra is fennáll.

Egy adott virtuális gép számlázása csak akkor leáll, ha a védelem le van állítva, és az összes biztonsági mentési érték törlődik. Ha a védelem leáll, és nincsenek aktív biztonsági mentési feladatok, a legutóbbi sikeres virtuális gép biztonsági mentésének mérete a havi számlán használt védett példány mérete lesz.

A védett példány méretének kiszámítása a virtuális gép *tényleges* méretétől függ. A virtuális gép mérete a virtuális gép összes adatmennyiségének összege, kivéve az ideiglenes tárolót. A díjszabás az adatlemezeken tárolt tényleges adatokon alapul, nem a virtuális géphez csatlakoztatott adatlemezek maximális támogatott méretével.

Hasonlóképpen, a biztonsági mentési tár a Azure Backup tárolt adatok mennyisége alapján történik, amely az egyes helyreállítási pontokban lévő tényleges adatok összege.

Tegyük fel például, hogy egy olyan a2-es szabványú virtuális gép, amely két további adatlemezt tartalmaz, amelyek maximális mérete 4 TB. A következő táblázat az egyes lemezeken tárolt tényleges adatokat mutatja be:

**Lemez** | **Maximális méret** | **Tényleges adatok jelennek meg**
--- | --- | ---
Operációsrendszer-lemez | 4095 GB | 17 GB
Helyi/ideiglenes lemez | 135 GB | 5 GB (nem tartalmazza a biztonsági mentést)
1\. adatlemez | 4095 GB | 30 GB
2\. adatlemez | 4095 GB | 0 GB

Ebben az esetben a virtuális gép tényleges mérete 17 GB + 30 GB + 0 GB = 47 GB. Ez a védett példány mérete (47 GB) lesz a havi számla alapja. Ahogy a virtuális gépen lévő adatmennyiség növekszik, a számlázáshoz használt védett példány mérete megegyezik.

<a name="limited-public-preview-backup-of-vm-with-disk-sizes-up-to-30tb"></a>
## <a name="limited-public-preview-backup-of-vm-with-disk-sizes-up-to-30-tb"></a>Korlátozott nyilvános előzetes verzió: A virtuális gép biztonsági mentése 30 TB-ig terjedő méretű lemezekkel

Azure Backup mostantól támogatja a nagyobb és nagyobb teljesítményű Azure- [Managed Disks](https://azure.microsoft.com/blog/larger-more-powerful-managed-disks-for-azure-virtual-machines/) korlátozott nyilvános előzetes verzióját, amely akár 30 TB méretű is lehet. Ez az előzetes verzió a felügyelt virtuális gépek termelési szintű támogatását biztosítja.

A folyamatban lévő biztonsági mentésekre gyakorolt hatás nélkül is zökkenőmentesen regisztrálhat az előzetes verzióra. Miután az előfizetés regisztrálva van az előzetes verzióban, az összes olyan virtuális gépet, amelynek a mérete legfeljebb 30 TB, sikeresen biztonsági mentést kell készíteni. Regisztrálás az előzetes verzióban:
 
Hajtsa végre a következő parancsmagokat egy emelt szintű PowerShell-terminálról:

1. Jelentkezzen be Azure-fiókjába.

    ```powershell
    PS C:> Login-AzureRmAccount
    ```

2. Válassza ki azt az előfizetést, amelyet regisztrálni szeretne a frissítéshez:

    ```powershell
    PS C:>  Get-AzureRmSubscription –SubscriptionName "Subscription Name" | Select-AzureRmSubscription
    ```
3. Az előfizetés regisztrálása az előzetes programban: 

    ```powershell
    PS C:> Register-AzureRmProviderFeature -FeatureName "LargeDiskVMBackupPreview" –ProviderNamespace Microsoft.RecoveryServices
    ```

    Várjon 30 percet, amíg az előfizetés regisztrálva lesz az előzetes verzióban. 

 4. Az állapot ellenõrzéséhez futtassa a következő parancsmagokat:

    ```powershell
    PS C:> Get-AzureRmProviderFeature -FeatureName "LargeDiskVMBackupPreview" –ProviderNamespace Microsoft.RecoveryServices 
    ```
5. Ha az előfizetés regisztráltként jelenik meg, futtassa a következő parancsot:
    
    ```powershell
    PS C:> Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices
    ```

> [!NOTE]
> Ez az előzetes verzió nem támogatja a 4 TB-nál nagyobb méretű lemezekkel rendelkező titkosított virtuális gépeket.



## <a name="next-steps"></a>További lépések

Most [készítse elő az Azure](backup-azure-arm-vms-prepare.md)-beli virtuális gépek biztonsági mentését.
