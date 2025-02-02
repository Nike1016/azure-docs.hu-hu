---
title: Microsoft Azure Data Box Disk GYIK | Microsoft Docs in data
description: Gyakori kérdéseket és válaszokat tartalmaz a Azure Data Box Disk, egy felhőalapú megoldásra, amely lehetővé teszi nagy mennyiségű adatok átvitelét az Azure-ba
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: overview
ms.date: 08/15/2019
ms.author: alkohli
ms.openlocfilehash: 1361224fb59a32ca227c5ea1e6cc92a4b0fdff6e
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69541813"
---
# <a name="azure-data-box-disk-frequently-asked-questions"></a>Azure Data Box Disk: Gyakori kérdések

A Microsoft Azure Data Box Disk felhőalapú megoldással gyorsan, költséghatékonyan és megbízhatóan küldhet több terabájtnyi adatot az Azure-ba. A GYIK a Data Box Disk-lemezek Azure Portalon történő használatával kapcsolatban esetlegesen felmerülő kérdésekre ad választ. 

A kérdéseket és a válaszokat az alábbi kategóriák szerint csoportosítottuk:

- Tudnivalók a szolgáltatásról
- Konfigurálás és csatlakoztatás 
- Állapotkövetés
- Adatok migrálása 
- Adatok ellenőrzése és feltöltése 


## <a name="about-the-service"></a>Tudnivalók a szolgáltatásról

### <a name="q-what-is-azure-data-box-service"></a>K. Mi az az Azure Data Box szolgáltatás? 
A.  Az Azure Data Box szolgáltatást offline adatbetöltéshez hoztuk létre. A szolgáltatás termékek egész tárházát kezeli, amelyek mindegyike különböző kapacitású tárolókra történő adatátvitelhez lett igazítva. 

### <a name="q-what-are-azure-data-box-disks"></a>K. Mik azok az Azure Data Box-lemezek?
A. Az Azure Data Box-lemezek több terabájtnyi adat gyors, költséghatékony és biztonságos adatátvitelét teszik lehetővé az Azure-ba, illetve az Azure-ból. A Microsoft elküld Önnek 1–5 lemezt, legfeljebb 35 TB-os tárkapacitással. Az Azure Portalon a Data Box szolgáltatás segítségével könnyedén konfigurálhatja, csatlakoztathatja és feloldhatja ezeket a lemezeket.  

A lemezek a Microsoft BitLocker meghajtótitkosítással vannak titkosítva, amelynek titkosítási kulcsait az Azure Portalon kezelheti. Ezt követően átmásolhatja az adatokat az ügyfél kiszolgálóiról. Az adatközpontban a Microsoft egy gyors és privát hálózati feltöltési kapcsolaton keresztül a lemezről a felhőbe telepíti az adatait, majd feltölti őket az Azure-ba.

### <a name="q-when-should-i-use-data-box-disks"></a>K. Mikor érdemes Data Box Disk-lemezeket használnom?
A. Ha 40 TB-nyi (vagy kevesebb) adata van, amelyet szeretne átvinni az Azure-ba, akkor hasznos lehet a Data Box Disk-lemezek használata.

### <a name="q-what-is-the-price-of-data-box-disks"></a>K. Mennyibe kerülnek a Data Box Disk-lemezek?
A. Data Box lemezek árával kapcsolatos információkért lépjen a [díjszabás oldalra](https://azure.microsoft.com/pricing/details/databox/disk/).

### <a name="q-how-do-i-get-data-box-disks"></a>K. Hogyan kaphatok Data Box Disk-lemezeket? 
A.  Azure Data Box lemezek beszerzéséhez jelentkezzen be Azure Portal és hozzon létre egy Data Box-sorrendet a lemezekhez. Adja meg a kapcsolattartási és értesítési adatait. A megrendelés elküldése után a rendelkezésre állás függvényében 10 napon belül szállítjuk Önnek a lemezeket.

### <a name="q-what-is-the-maximum-amount-of-data-i-can-transfer-with-data-box-disks-in-one-instance"></a>K. Egyszerre legfeljebb mennyi adatot vihetek át a Data Box Disks-lemezekkel?
A. 5 darab egyenként 8 TB-os lemez (7 TB-os felhasználható kapacitással) esetén a felhasználható kapacitás legfeljebb 35 TB. Így egyszerre legfeljebb 35 TB-nyi adat vihető át. Több adat átviteléhez több lemez megrendelése szükséges.

### <a name="q-how-can-i-check-if-data-box-disks-are-available-in-my-region"></a>K. Honnan tudhatom meg, hogy a Data Box Disk-lemezek elérhetők-e a régiómban? 
A.  Ha szeretné megtekinteni, hogy jelenleg milyen Data Box lemezek érhetők el, lépjen a [régió rendelkezésre állása területre](data-box-disk-overview.md#region-availability).  

### <a name="q-which-regions-can-i-store-data-in-with-data-box-disks"></a>K. Mely régiókban tárolhatok adatokat a Data Box Disk-lemezekkel?
A. A Data Box Disk az Egyesült Államok, Kanada, Ausztrália, Nyugat-Európa és Észak-Európa, Korea és Japán régiókban is támogatott. Csak a nyilvános Azure-felhőrégiók támogatottak. Az Azure Government és más független felhők nem támogatottak.

### <a name="q-will-my-data-box-disk-cross-country-borders-during-shipping"></a>K. A Data Box Disk országbeli határok lesznek a szállítás során?
A. Data Box Disk szállítása a rendeltetési országon belül történik, és nem léphet fel nemzetközi határokra. Az egyetlen kivétel az Európai Unió (EU) megrendeléseire vonatkozik, ahol a lemezek bármelyik EU-országba oda tudnak szállítani.

### <a name="q-whom-should-i-contact-if-i-encounter-any-issues--with-data-box-disks"></a>K. Kihez fordulhatok, ha probléma merülne fel a Data Box Disk-lemezekkel kapcsolatban?
A. Ha Data Box lemezzel kapcsolatos problémákat tapasztal, vegye fel a [kapcsolatot](https://docs.microsoft.com/azure/databox/data-box-disk-contact-microsoft-support)a Microsoft ügyfélszolgálataval.

## <a name="configure-and-connect"></a>Konfigurálás és csatlakoztatás
 
### <a name="q-can-i-specify-the-number-of-data-box-disks-in-the-order"></a>K. Megadhatom a megrendelésben a Data Box Disk-lemezek számát?
A.  Nem. 8 TB-os lemezeket (legfeljebb 5-öt) fog kapni az adatai mérete és a lemezek rendelkezésre állásának függvényében.  

### <a name="q-how-do-i-unlock-the-data-box-disks"></a>K. Hogyan oldhatom fel a Data Box Disk-lemezeket? 
A.  Nyissa meg az Azure Portalon a Data Box Disk-megrendelését, majd az **Eszköz részletei** lehetőséget. Másol le a jelszót. Töltse le az Azure Portalról az operációs rendszerének megfelelő Data Box Disk lemezzárolás-feloldó eszközt. Futtassa azon a számítógépen, amelyről az adatokat a lemezekre szeretné másolni. Adja meg a jelszót a lemezek feloldásához. Ugyanaz a jelszó oldja fel az összes lemezt. 

A részletes útmutatót a [Lemez feloldása Windows-ügyfélen](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client) vagy a [Lemez feloldása Linux-ügyfélen](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client) című szakaszban talál.

### <a name="q-can-i-use-a-linux-host-computer-to-connect-and-copy-the-data-on-to-the-data-box-disks"></a>K. Használhatok linuxos gazdaszámítógépet a Data Box Disk-lemezek csatlakoztatásához és az adatok másolásához?
A.  Igen. Linux- és a Windows-ügyfelek is használhatók a Data Box Disk-lemezek csatlakoztatásához és az adatok másolásához. További információkért tekintse meg a gazdaszámítógépre vonatkozó [Támogatott operációs rendszerek](data-box-disk-system-requirements.md) listáját.

### <a name="q-my-disks-are-dispatched-but-now-i-want-to-cancel-this-order-why-is-the-cancel-button-not-available"></a>K. Már feladták a lemezeket, de szeretném lemondani a megrendelést. Miért nincs Mégse gomb?
A.  A megrendelést csak a lemezek megrendelése után, de még a szállítás megkezdése előtt mondhatja le. Ha feladtuk a lemezeket, már nem mondhatja le a megrendelést. A lemezeket azonban ingyenesen visszaállíthatja. 

### <a name="q-can-i-connect-multiple-data-box-disks-at-the-same-to-the-host-computer-to-transfer-data"></a>K. Csatlakoztathatok adatátvitel céljából több Data Box Disk-lemezt is ugyanahhoz a gazdaszámítógéphez?
A. Igen. Ugyanahhoz a gazdaszámítógéphez egyszerre több Data Box Disk-lemez is csatlakoztatható, és párhuzamosan több másolási feladat is futtatható.

## <a name="track-status"></a>Állapot nyomon követése

### <a name="q-how-do-i-track-the-disks-from-when-i-placed-the-order-to-shipping-the-disks-back"></a>K. Hogyan tudom nyomon követni a lemezeket a megrendelésem feladásától kezdve egészen azok visszaküldéséig? 
A.  A Data Box Disk-lemez megrendelésének állapotát az Azure Portalon követheti nyomon. A megrendelés létrehozásakor a rendszer egy értesítési e-mail-cím megadását is kéri. Ha adott meg ilyen e-mail-címet, akkor a megrendelés állapotváltozásairól e-mailben is értesíteni fogjuk. További információ az [Értesítési e-mailek beállítása](data-box-portal-ui-admin.md#edit-notification-details) témában.

### <a name="q-how-do-i-return-the-disks"></a>K. Hogyan küldhetem vissza a lemezeket? 
A.  A Microsoft a Data Box Disk-lemezek csomagjában egy levélcímkét is elküld Önnek. Ragassza ezt a címkét egy szállítódobozra, majd adja le a lezárt dobozt a szállítmányozójánál. Ha a címke megsérült vagy elveszett, nyissa meg az **Áttekintés > Levélcímke letöltése** menüpontot, és töltsön le egy új levélcímkét.

## <a name="migrate-data"></a>Adatok migrálása

### <a name="q-what-is-the-maximum-data-size-that-can-be-used-with-data-box-disks"></a>K. Legfeljebb mekkora méretű adat esetén használhatók a Data Box Disk-lemezek?  
A.  A Data Box Disk megoldással legfeljebb 5, összesen 35 TB-os felhasználható kapacitással rendelkező lemez használható. Maguk a lemezek 8 TB-osak (amelyből 7 használható fel).

### <a name="q-what-are-the-maximum-block-blob-and-page-blob-sizes-supported-by-data-box-disks"></a>K. Mekkora a Data Box Disk-lemezeken támogatott legnagyobb blokkblob és lapblob mérete? 
A.  A legnagyobb méreteket az Azure Storage tárhelykorlátai határozzák meg. A legnagyobb blokkblob mérete körülbelül 4,768 TiB, a legnagyobb lapblob mérete pedig 8 TiB. További információkért lásd: [Azure Storage Scalability and Performance Targets](../storage/common/storage-scalability-targets.md) (Az Azure Storage méretezhetőségi és teljesítménycéljai).

### <a name="q-what-is-the-data-transfer-speed-for-data-box-disks"></a>K. Milyen adatátviteli sebességgel rendelkeznek a Data Box Disk-lemezek?
A. A tesztjeink során USB 3.0-ás kapcsolat esetén a lemezeknél 430 MB/s-os adatátviteli sebességet is mértünk. A tényleges szám a használt fájl méretének függvényében eltérő lehet. Kisebb fájlok esetén gyengébb teljesítmény figyelhető meg.

### <a name="q-how-do-i-know-that-my-data-is-secure-during-transit"></a>K. Honnan tudhatom, hogy az átvitel során az adataim biztonságban vannak? 
A.  A Data Box Disk-lemezek 128 bites BitLocker AES-titkosítással vannak titkosítva, és a titkosítás jelszava csak az Azure Portalon érhető el. A jelszó lekéréséhez jelentkezzen be az Azure Portalra a fiókja hitelesítő adataival. Ezt a jelszót a Data Box lemezzárolás-feloldó eszköz használata során adhatja meg.

### <a name="q-how-do-i-copy-the-data-to-the-data-box-disks"></a>K. Hogyan tudom az adataimat a Data Box Disk-lemezekre másolni? 
A.  Az adatok egy SMB-másoló eszköz, például a Robocopy vagy a Diskboss használatával másolhatja a lemezekre, de használhatja akár a Windows Fájlkezelő húzás funkcióját is.

### <a name="q-are-there-any-tips-to-speed-up-the-data-copy"></a>K. Vannak tippek az adatok másolásának felgyorsítására?
A.  A másolási folyamat felgyorsításához:

- Használjon több másolási adatfolyamot. Használja például a Robocopy többszálas lehetőségét. A pontos parancs használatáról [további információt a következő oktatóanyagban olvashat: Másolja az adatAzure Data Box Disk és ellenőrizze](data-box-disk-deploy-copy-data.md#copy-data-to-disks)a következőt:.
- Használjon több munkamenetet.
- Például a hálózati megosztásból történő másolás helyett (ahol a hálózat sebessége korlátokat szabhat) gondoskodjon róla, hogy az adatok azon a helyi számítógépen legyenek, amelyre a lemezeket csatlakoztatja.
- Gondoskodjon róla, hogy a másolási folyamathoz USB 3.0-ás vagy újabb kapcsolatot használjon. Töltse le és használja az [USBView eszközt](https://docs.microsoft.com/windows-hardware/drivers/debugger/usbview), amellyel azonosíthatja a számítógép USB-vezérlőit és a számítógéphez kapcsolódó USB-s eszközöket.
- Mérje meg az adatok másolására használt számítógép teljesítményét. Töltse le és használja a [Bluestop FIO eszközt](https://bluestop.org/fio/), amellyel megmérheti a kiszolgáló hardverének teljesítményét.

### <a name="q-how-to-speed-up-the-data-if-the-source-data-has-small-files-kbs-or-few-mbs"></a>K. Hogyan lehet felgyorsítani az adatok másolását, ha a forrásadatok között sok kisméretű fájl található (KB-os vagy néhány MB-os méretűek)?
A.  A másolási folyamat felgyorsításához:

- Hozzon létre egy helyi VHDx-et egy gyors tárolón, vagy hozzon létre egy üres VHD-t a HDD-n vagy az SSD-n (lassabb).
- Csatlakoztassa egy virtuális géphez.
- Másolja a fájlokat a virtuális gép lemezére.

### <a name="q-can-i-use-multiple-storage-accounts-with-data-box-disks"></a>K. Használhatok több tárfiókot is a Data Box Disk-lemezekkel?
A.  Nem. A Data Box Disk-lemezekkel jelenleg csak egyetlen általános célú vagy hagyományos tárfiók használata támogatott. A gyakori és a ritka elérésű blob is támogatott. Jelenleg csak az USA-beli, Nyugat-európai és észak-európai Azure-beli Storage-fiókok támogatottak.

### <a name="q-what-is-the-toolset-available-for-my-data-with-data-box-disks"></a>K. Milyen eszközkészlet érhető el az adataim Data Box lemezzel?
A. A Data Box Disk elérhető eszközkészlet három eszközt tartalmaz:
 - **Data Box Disk-feloldási eszköz**: Ezzel az eszközzel oldhatja fel a Microsoft által szállított titkosított lemezeket. Ha a lemezeket az eszközzel oldja fel, meg kell adnia a Azure Portal Data Box Disk sorrendjében elérhető hozzáférési kulcsot. 
 - **Data Box Disk ellenőrző eszköz**: Ezzel az eszközzel ellenőrizheti a méretet, a formátumot és a blob nevét az Azure elnevezési konvenciók szerint. Emellett ellenőrzőösszegeket is generál a másolt adatfájlokhoz, amelyeket azután az Azure-ba feltöltött adatellenőrzéshez használ a rendszer.
 - **Data Box Disk a felosztott másolási eszköz**: Akkor használja ezt az eszközt, ha több lemezt használ, és olyan nagy adatkészlettel rendelkezik, amelyet az összes lemezre el kell osztani és át kell másolni. Ez az eszköz jelenleg Windows rendszeren érhető el. Ez az eszköz felügyelt lemezek esetén nem támogatott. Ez az eszköz azt is ellenőrzi, hogy az Adatmásolás során az adatmásolási lépés kihagyható-e az eszköz használatakor.

Az eszközkészlet a Windows és a Linux rendszerhez egyaránt elérhető. Az eszközkészletet innen töltheti le:
- [Data Box Disk eszközkészlet letöltése Windowsra](https://aka.ms/databoxdisktoolswin) 
- [Data Box Disk eszközkészlet letöltése Linuxra](https://aka.ms/databoxdisktoolslinux)
 
### <a name="q-can-i-use-data-box-disk-to-transfer-data-to-azure-files-and-then-use-the-data-with-azure-file-sync"></a>K. Használhatom a Data Box Disk az adatok Azure Filesba való átvitelére, majd az adatok Azure File Sync használatával történő felhasználására? 
A. A Azure Files Data Box Disk, de nem fognak jól működni Azure File Sync. A metaadatokat a rendszer nem őrzi meg, ha a fájlra vonatkozó adatokat Azure File Sync használja.


## <a name="verify-and-upload"></a>Ellenőrzés és feltöltés

### <a name="q-how-soon-can-i-access-my-data-in-azure-once-ive-shipped-the-disks-back"></a>K. A lemezek visszaküldését követően mennyi időn belül férhetek hozzá az adataimhoz az Azure-ban? 
A.  Amint az Adatmásolás megrendelési állapota befejeződöttként jelenik meg, azonnal hozzáférhet az adataihoz.

### <a name="q-where-is-my-data-located-in-azure-after-the-upload"></a>K. A feltöltést követően hol lesznek az adataim az Azure-ban?
A.  Ha a lemezen a *BlockBlob* és a *PageBlob* mappákba másolta az adatait, akkor a *BlockBlob* és a *PageBlob* mappákban található almappáknak létrejön az Azure-tárfiókban egy-egy tároló. Ha a fájlokat közvetlenül a *BlockBlob* és a *PageBlob* mappákba másolta, akkor ezek az alapértelmezett *$root* tárolóban lesznek megtalálhatók az Azure-tárfiókban. Amikor az *AzureFile* mappában található mappába másol egy mappát, létrejön egy fájlmegosztás.

### <a name="q-i-just-noticed-that-i-did-not-follow-the-azure-naming-requirements-for-my-containers-will-my-data-fail-to-upload-to-azure"></a>K. Most vettem észre, hogy nem követtem az Azure elnevezési követelményeit a tárolóim esetében. Így is fel lehet tölteni az adataimat az Azure-ba?
A. Ha a tárolók nevei nagybetűket tartalmaznak, akkor azokat automatikusan kisbetűsekké alakítja át a rendszer. Ha a nevek valami más szempontból nem megfelelőek (például speciális karaktereket tartalmaznak, más nyelvűek stb.) akkor a feltöltés meghiúsul. További információt az [Azure elnevezési konvencióit](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions) ismertető szakaszban talál.

### <a name="q-how-do-i-verify-the-data-i-copied-onto-multiple-data-box-disks"></a>K. Hogyan tudom ellenőrizni a több Data Box Disk-lemezre feltöltött adataimat?
A.  Az adatok másolásának befejezése után futtassa az `DataBoxDiskValidation.cmd` fájlt a *DataBoxDiskImport* mappából, hogy létrehozzon az érvényesítéshez egy ellenőrzőösszeget. Ha több lemeze is van, akkor minden egyes lemezen meg kell nyitnia a parancssori ablakot, és le kell futtatnia ezt a parancsot. Vegye figyelembe, hogy ez a művelet az adatok méretétől függően akár hosszú ideig (órákig) is eltarthat.

### <a name="q-what-happens-to-my-data-after-i-have-returned-the-disks"></a>K. Mi történik az adataimmal, miután visszaküldtem a lemezeket?
A.  Ha befejeződött az adatok Azure-ba történő másolása, a rendszer a lemezen található adatokat a NIST SP 800-88 Revision 1 útmutatásainak megfelelően biztonságosan törli.  

### <a name="q-how-is-my-data-protected-during-transit"></a>K. Milyen védelem alatt állnak az adataim a szállítás közben? 
A.  A Data Box Disk-lemezek 128 bites Microsoft BitLocker AES-titkosítással vannak titkosítva. A feloldáshoz és az adatokhoz való hozzáféréshez egyetlen jelszó szükséges.

### <a name="q-do-i-need-to-rerun-checksum-validation-if-i-add-more-data-to-the-data-box-disks"></a>K. Le kell futtatnom ismét az ellenőrzőösszeg-érvényesítést, ha további adatokat másolok a Data Box Disk-lemezekre?
A. Igen. Ha úgy dönt, hogy érvényesíti az adatait (ezt mi is javasoljuk), akkor ha további adatokat másol a lemezre, újra le kell futtatnia az érvényesítést.

### <a name="q-i-used-all-my-disks-to-transfer-data-and-need-to-order-more-disks-is-there-a-way-to-quickly-place-the-order"></a>K. Az összes lemezt felhasználtam az adataim átviteléhez, és szükségem van további lemezek megrendelésére. Fel lehet adni gyorsan a megrendelést?
A. Lehetősége van klónozni az előző megrendelését. A klónozással létrehozhat egy, az előzővel azonos megrendelést, amelynek szerkesztheti a részleteit, anélkül, hogy újra meg kellene adnia a címet vagy az értesítési adatokat.

### <a name="q-i-copied-data-to-manageddisk-folder-i-dont-see-any-managed-disks-with-the-resource-group-specified-for-managed-disks-was-my-data-uploaded-to-azure-and-how-can-i-locate-it"></a>K. Átmásoltam az ManagedDisk mappába. Nem látok felügyelt lemezeket a felügyelt lemezekhez megadott erőforráscsoporthoz. Feltöltöttem az adataim az Azure-ba, és hogyan találom meg?
A. Igen. Az adatai az Azure-ba lettek feltöltve, de ha nem lát felügyelt lemezt a megadott erőforráscsoporthoz, akkor valószínű, hogy az adatai érvénytelenek. Ha a Blobok, a Blobok, a Azure Files és a felügyelt lemezek blokkolása nem volt érvényes, akkor a következő mappákhoz juthat:
 - Az oldal Blobok egy *databoxdisk-érvénytelen-PB-* vel kezdődő blokk blob-tárolóra mutatnak.
 - A Azure Files egy blokk blob-tárolóba kerül, amely a *databoxdisk-érvénytelen-AF-* -vel kezdődik.
 - A felügyelt lemezek egy blokk blob-tárolóba kerülnek, amely a következővel kezdődik: *databoxdisk-érvénytelen-MD-* .

## <a name="next-steps"></a>További lépések

- Tekintse át a [Data Box Disk](data-box-disk-system-requirements.md)rendszerkövetelményeit.
- A [Data Box Disk korlátainak](data-box-disk-limits.md) megismerése.
- Az [Azure Data Box Disk](data-box-disk-quickstart-portal.md) gyors üzembe helyezése az Azure Portalon.
