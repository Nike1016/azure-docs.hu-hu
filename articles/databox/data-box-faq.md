---
title: Microsoft Azure Data Box – gyakori kérdések | Microsoft Docs
description: Gyakran ismételt kérdéseket tartalmaz, valamint választ ad az Azure Data Box egy felhőalapú megoldás, amely lehetővé teszi nagy mennyiségű adat átvitele az Azure-bA.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: overview
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 8f090a3f07eba529fd39976545eb654083e637eb
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561400"
---
# <a name="azure-data-box-frequently-asked-questions"></a>Azure Data Box: Gyakori kérdések

A Microsoft Azure Data Box hibrid megoldással gyorsan, költséghatékonyan és megbízhatóan küldhet több terabájtnyi adatot az Azure-ba egy átviteli eszköz segítségével. A gyakori kérdések a Data Box Azure Portalon történő használatával kapcsolatban esetlegesen felmerülő kérdésekre adnak választ. 

A kérdéseket és a válaszokat az alábbi kategóriák szerint csoportosítottuk:

- Tudnivalók a szolgáltatásról
- Eszköz rendelése
- Konfigurálás és csatlakoztatás 
- Állapot nyomon követése
- Adatok másolása 
- Eszköz kiszállítása
- Adatok ellenőrzése és feltöltése 
- Felügyeleti lánc támogatása

## <a name="about-the-service"></a>Tudnivalók a szolgáltatásról

### <a name="q-what-is-azure-data-box-service"></a>K. Mi az az Azure Data Box szolgáltatás? 
A.  Az Azure Data Box szolgáltatást offline adatbetöltéshez hoztuk létre. A szolgáltatás különböző tárolókapacitású termékek egész tárházát kezeli, amelyek mindegyike adatátvitelhez lett igazítva. 

### <a name="q-what-is-azure-data-box"></a>K. Mi az az Azure Data Box?
A. Az Azure Data Box lehetővé teszi, hogy egy gyors, költségkímélő, és biztonságos átvitelét terabájtnyi adatot az Azure-bA. A Data Box eszköz az Azure Portalon keresztül rendelhető meg. A Microsoft, 80 TB felhasználható kapacitás keresztül egy regionális szolgáltatója tárolóeszköz tartalmaz. 

Miután az eszköz megérkezett, gyorsan beállítható a helyi webes felhasználói felülettel. Másolja át a kiszolgálóin található adatokat az eszközre, és küldje vissza az eszközt az Azure-nak. Az Azure-adatközpontban az adatok automatikusan feltöltődnek az eszközről az Azure-ba. A teljes folyamatot végigkövetheti a Data Box szolgáltatásban, az Azure Portalon.

### <a name="q-when-should-i-use-data-box"></a>K. Mikor érdemes a Data Boxot használni?
A. Ha 40 és 500 TB közötti mennyiségű adata van, amelyet szeretne átvinni az Azure-ba, akkor hasznos lehet a Data Box használata. Adatok méretek < 40 TB-ot, a Data Box-lemezek használata, és az adatok > 500 TB méretű regisztrálhat a [Data Box nehéz](data-box-heavy-overview.md).

### <a name="q-what-is-the-price-of-data-box"></a>K. Mennyibe kerül a Data Box?
A. A Data Box 10 napig egy névleges díj fejében érhető el. Amikor az Azure Portalon egy rendelés létrehozásakor kiválaszt egy termékmodellt, megjelenik az eszköz ára. A szállítás is ingyenes, az Azure-tárhely azonban költségekkel jár. További információt [az Azure Data Box díjszabási oldalán](https://azure.microsoft.com/pricing/details/storage/databox/) talál. 

### <a name="q-what-is-the-maximum-amount-of-data-i-can-transfer-with-data-box-in-one-instance"></a>K. Egyszerre legfeljebb mennyi adatot vihetek át a Data Box segítségével?
A. A Data Box összes kapacitása 100 TB, használható kapacitása pedig 80 TB. Az adatok Data Box akár 80 TB lehet átvinni. Több adat átviteléhez több eszköz megrendelése szükséges.

### <a name="q-how-can-i-check-if-data-box-is-available-in-my-region"></a>K. Honnan tudhatom meg, hogy a Data Box elérhető-e a régiómban? 
A.  Információk a mely országokban és régiókban érhető el a Data Box, [régiók rendelkezésre állása](data-box-overview.md#region-availability).  

### <a name="q-which-regions-can-i-store-data-in-with-data-box"></a>K. Mely régiókban tárolhatok adatokat a Data Box segítségével?
A. Data Box RÉGIÓJA, Nyugat-Európa, Észak-Európa, Franciaország, Egyesült Királyság, japán, Ausztrália és Kanadában belül minden régió esetében támogatott. További információt a [Regionális elérhetőség](data-box-overview.md#region-availability) témakörben talál.

### <a name="q-whom-should-i-contact-if-i-encounter-any-issues-with-data-box"></a>K. Kihez fordulhatok, ha probléma merülne fel a Data Box kapcsán?
A. Ha bármilyen probléma merülne fel a Data Box kapcsán, kérjük, [forduljon a Microsoft támogatási szolgálatához](data-box-disk-contact-microsoft-support.md).

### <a name="q-i-have-lost-my-data-box-is-there-a-lost-device-charge"></a>K. A Data Box I megszakadt. Van valamilyen díja elveszett eszköz?
A. Igen. Elveszett vagy sérült Data boxért díj van. Ez a díj tárgyalja a [díjszabási oldalunkon](https://azure.microsoft.com/pricing/details/storage/databox/) is, a a [a termék használati feltételeit](https://www.microsoft.com/licensing/product-licensing/products).


## <a name="order-device"></a>Eszköz rendelése

### <a name="q-how-do-i-get-data-box"></a>K. Hogyan szerezhetem be a Data Boxot? 
A.  Az Azure Data Box beszerzéséhez jelentkezzen be az Azure Portalon, majd hozzon létre egy Data Box-megrendelést. Adja meg a kapcsolattartási és értesítési adatait. A megrendelés elküldése után a rendelkezésre állás függvényében 10 napon belül szállítjuk Önnek a Data Boxot. További információ [a Data Box megrendeléséről](data-box-deploy-ordered.md).

### <a name="q-i-was-not-able-to-create-a-data-box-order-in-the-azure-portal-why-would-this-be"></a>K. Nem sikerült létrehoznom egy Data Box-megrendelést az Azure Portalon. Mi lehet ennek az oka?
A. Ha nem sikerült létrehoznia egy Data Box-megrendelést, akkor a probléma az előfizetési típusában vagy a hozzáférésében keresendő. 

Ellenőrizze az előfizetését. A Data Box csak Nagyvállalati Szerződés (EA) és Felhőszolgáltató (CSP) típusú előfizetés esetén érhető el. Ha az előfizetése nem az említett típusok egyikéhez tartozik, forduljon a Microsoft támogatási szolgálatához az előfizetése bővítése érdekében.

Ha egy támogatott típusú előfizetéssel rendelkezik, ellenőrizze a hozzáférési szintjét. Megrendelés létrehozására csak az előfizetések közreműködői vagy tulajdonosai jogosultak.

### <a name="q-i-ordered-a-couple-of-data-box-devices-i-am-not-able-to-create-any-additional-orders-why-would-this-be"></a>K. Rendeltem néhány Data Box-eszközt. Nem tudok további rendeléseket létrehozni. Mi lehet ennek az oka?
A. Legfeljebb öt aktív megrendelés engedélyezett előfizetésenként és a kereskedelmi korlátokon belül (ez az ország és a kiválasztott régió kombinációjából áll össze). Ha további eszközökre van szüksége, forduljon a Microsoft támogatási szolgálatához az előfizetésére vonatkozó korlátozás bővítéséhez.

### <a name="q-when-i-try-to-create-an-order-i-receive-a-notification-that-the-data-box-service-is-not-available-what-does-this-mean"></a>K. Amikor megpróbálok létrehozni egy rendelést, arról kapok értesítést, hogy a Data Box szolgáltatás nem érhető el. Ez mit jelent?
A. Ez azt jelenti, hogy a Data Box szolgáltatás nem érhető el az ország és régió kiválasztott kombinációja esetében. A kombináció módosításával valószínűleg elérhetővé válik a Data Box szolgáltatás. Azon régiók listáját, ahol a szolgáltatás elérhető, a [Data Box regionális elérhetőségét](data-box-overview.md#region-availability) ismertető témakörben találja.

### <a name="q-i-placed-my-data-box-order-few-days-back-when-will-i-receive-my-data-box"></a>K. Néhány napja adtam fel a Data Box-megrendelésem. Mikor fogom megkapni a Data Boxot?
A. A megrendelések beérkezésekor ellenőrizzük, hogy van-e a rendelésnek megfelelő elérhető eszköz. Ha van, akkor 10 napon belül kiszállítjuk. Elképzelhető, hogy bizonyos időszakokban a kereslet megnövekszik. Ebben az esetben rendelését sorba állítjuk, a rendelés állapotát pedig az Azure Portalon követheti nyomon. Ha a rendelés 90 napon belül sem teljesül, akkor a rendszer automatikusan visszavonja azt.

### <a name="q-i-have-filled-up-my-data-box-with-data-and-need-to-order-another-one-is-there-a-way-to-quickly-place-the-order"></a>K. Feltöltöttem a Data Boxom adatokkal, és rendelnem kell egy másikat. Fel lehet adni gyorsan a megrendelést?
A. Lehetősége van klónozni az előző megrendelését. A klónozással létrehozhat egy, az előzővel azonos megrendelést, amelynek szerkesztheti a részleteit, anélkül, hogy újra meg kellene adnia a címet vagy az értesítési adatokat.

## <a name="configure-and-connect"></a>Konfigurálás és csatlakoztatás

### <a name="q-how-do-i-unlock-the-data-box"></a>K. Hogyan oldhatom fel a Data Box zárolását? 
A.  Nyissa meg az Azure Portalon a Data Box-megrendelését, majd az **Eszköz részleteit**. Másolja ki a zárolásfeloldási jelszót. Jelentkezzen be ezzel a jelszóval a Data Box helyi webes felhasználói felületére. További információért ugorjon [oktatóanyag: Kicsomagolása, kábelezése, csatlakozzon az Azure Data Box](data-box-deploy-set-up.md).

### <a name="q-can-i-use-a-linux-host-computer-to-connect-and-copy-the-data-on-to-the-data-box"></a>K. Használhatok linuxos gazdaszámítógépet a Data Box csatlakoztatásához és az adatok másolásához?
A.  Igen. A Data Box-eszközökkel SMB- és NFS-ügyfelekhez is csatlakozhat. További információkért tekintse meg a gazdaszámítógépre vonatkozó [Támogatott operációs rendszerek](data-box-system-requirements.md) listáját.

### <a name="q-my-data-box-is-dispatched-but-now-i-want-to-cancel-this-order-why-is-the-cancel-button-not-available"></a>K. Már feladták a Data Boxom, de szeretném lemondani a megrendelést. Miért nincs Mégse gomb?
A.  A rendelést csak annak feladását követően, de még a feldolgozását megelőzően vonhatja vissza. Ha a Data Box-megrendelés feldolgozása már megtörtént, nem mondhatja le a rendelést. 

### <a name="q-can-i-connect-a-data-box-at-the-same-to-multiple-host-computers-to-transfer-data"></a>K. Csatlakoztathatok egy Data Boxot egyszerre több gazdaszámítógéphez adatátvitel céljából?
A. Igen. Egy Data Boxhoz egyszerre több gazdaszámítógép is csatlakoztatható, és párhuzamosan több másolási feladat is futtatható. További információért ugorjon [oktatóanyag: Adatok másolása az Azure Data Box](data-box-deploy-copy-data.md).

### <a name="q-can-i-connect-to-both-the-10-gbe-interfaces-on-the-data-box-to-transfer-data"></a>K. Csatlakozhatok mind a 10-GbE adaptert adatátvitelhez a Data Box?
A. Igen. 10-GbE interfészek, mind a Data Box másolhat adatokat egy időben a csatlakoztathatók. További információk az adatok másolása, [oktatóanyag: Adatok másolása az Azure Data Box](data-box-deploy-copy-data.md).

<!--### Q. The network interface on my Data Box is not working. What should I do? 
A. 

### Q. I could not set up Data Box using a Dynamic (DHCP) IP address. Why would this be?
A.

### Q. I could not set up Data Box using a Static IP address. Why would this be?
A.

### Q. I could not set up Data Box on a private network. Why would this be?
A.-->

### <a name="q-the-system-fault-indicator-led-on-the-front-operating-panel-is-on-what-should-i-do"></a>K. Az elülső kezelőpanelen világít a rendszerhibajelző LED. Mit tegyek?
A. Ha a rendszerhibajelző LED világít, az azt jelzi, hogy a rendszer állapota nem kifogástalan. A további lépésekért [forduljon a Microsoft ügyfélszolgálatához](data-box-disk-contact-microsoft-support.md).

### <a name="q-i-cant-access-the-data-box-unlock-password-in-the-azure-portal-why-would-this-be"></a>K. Nem férek hozzá a Data Box zárolásfeloldási jelszavához az Azure Portalon. Mi lehet ennek az oka?
A. Ha nem fér hozzá a Data Box zárolásfeloldási jelszavához az Azure Portalon, ellenőrizze az előfizetése és a tárfiókja jogosultságait. Győződjön meg arról, hogy közreműködői vagy tulajdonosi jogosultságokkal rendelkezik az erőforráscsoport szintjén. Ha nem, majd van szüksége, hogy legalább Data Box-operátor szerepkör engedély a hozzáférési hitelesítő adatok megtekintéséhez.

### <a name="q-is-port-channel-configuration-supported-on-data-box-how-about-mpio"></a>K. Port csatorna konfigurációja támogatott a Data Box? Mi a helyzet MPIO-t?
A. A Data Box csatorna Portkonfiguráció, többutas I/O (MPIO) konfigurációs vagy vLAN-konfiguráció nem támogatott.

## <a name="track-status"></a>Állapot nyomon követése

### <a name="q-how-do-i-track-the-data-box-from-when-i-placed-the-order-to-shipping-the-device-back"></a>K. Hogyan tudom nyomon követni a Data Boxot a megrendelésem feladásától kezdve egészen az eszköz visszaküldéséig? 
A.  A Data Box-megrendelésének állapotát az Azure Portalon követheti nyomon. A megrendelés létrehozásakor a rendszer egy értesítési e-mail-cím megadását is kéri. Ha adott meg ilyen e-mail-címet, akkor a megrendelés állapotváltozásairól e-mailben is értesíteni fogjuk. További információ az [Értesítési e-mailek beállítása](data-box-portal-ui-admin.md#edit-notification-details) témában.

### <a name="q-how-do-i-return-the-device"></a>K. Hogyan küldhetem vissza az eszközt? 
A.  A Microsoft megjelenít egy fuvarlevélcímkét az E-ink-megjelenítőn. Ha a fuvarlevélcímke nem jelenik meg az E-ink-képernyőn, lépjen az **Áttekintés > Levélcímke letöltése** menüpontra. Töltse le és nyomtassa ki a címkét, majd helyezze az átlátszó műanyag tasakba az eszközön, és adja le azt a szállítmányozójánál. 

### <a name="q-i-received-an-email-notification-that-my-device-has-reached-the-azure-datacenter-how-do-i-find-out-if-the-data-upload-is-in-progress"></a>K. Kaptam egy e-mail-értesítést, hogy az eszközöm elért az Azure-adatközpontba. Hogyan deríthetem ki, hogy az adatok felöltése folyamatban van-e?
A. Nyissa meg az Azure Portalon a Data Box-megrendelését, majd az **Áttekintést**. Ha az adatok feltöltése már megkezdődött, a jobb oldali panelen látható a másolás folyamatának előrehaladása. 

## <a name="migrate-data"></a>Adatok áttelepítése

### <a name="q-what-is-the-maximum-data-size-that-can-be-used-with-data-box"></a>K. Legfeljebb mekkora mennyiségű adat esetén használható a Data Box?  
A.  A Data Box hasznos tárolókapacitása 80 TB. Egyetlen Data Box-eszközön 40 TB és 80 TB közti mennyiségű adatot tárolhat. A nagyobb adathalmazokat akár 500 TB méretű rendezheti több Data Box-eszközre. Az 500 TB-ot meghaladó adatmennyiségek esetén regisztráljon a Data Box Heavy szolgáltatásba.  

### <a name="q-what-are-the-maximum-block-blob-and-page-blob-sizes-supported-by-data-box"></a>K. Mekkora a Data Box-eszközökön támogatott legnagyobb blokkblob és lapblob mérete? 
A.  A legnagyobb méreteket az Azure Storage tárhelykorlátai határozzák meg. A legnagyobb blokkblob mérete körülbelül 4,768 TiB, a legnagyobb lapblob mérete pedig 8 TiB. További információkért lásd: [Azure Storage Scalability and Performance Targets](../storage/common/storage-scalability-targets.md) (Az Azure Storage méretezhetőségi és teljesítménycéljai). 

### <a name="q-how-do-i-know-that-my-data-is-secure-during-transit"></a>K. Honnan tudhatom, hogy az átvitel során az adataim biztonságban vannak? 
A. Több biztonsági funkció is gondoskodik a Data Box biztonságáról az átvitel során. Ezek közé tartozik az illetéktelen hozzáférést megakadályozó burkolat, az illetéktelen hardver- vagy szoftverhozzáférést észlelő megoldások, vagy az eszköz zárolásának feloldására szolgáló jelszó. További információt [az Azure Data Box biztonsági és adatvédelmi szolgáltatásait](data-box-security.md) ismertető cikkben talál.

### <a name="q-how-do-i-copy-the-data-to-the-data-box"></a>K. Hogyan másolhatom az adataimat a Data Boxra? 
A.  Ha SMB-ügyfelet használ, az adatokat egy SMB-másolóeszköz, például a Robocopy vagy a Diskboss használatával másolhatja az eszközre, de használhatja akár a Windows Fájlkezelő húzás funkcióját is. 

Ha NFS-ügyfelet használ, akkor az [rsync](https://rsync.samba.org/), [FreeFileSync](https://www.freefilesync.org/), [Unison](https://www.cis.upenn.edu/~bcpierce/unison/) vagy [Ultracopier](https://ultracopier.first-world.info/) alkalmazást használhatja. 

További információért ugorjon [oktatóanyag: Adatok másolása az Azure Data Box](data-box-deploy-copy-data.md).

### <a name="q-are-there-any-tips-to-speed-up-the-data-copy"></a>K. Vannak tippek az adatok másolásának felgyorsítására?
A.  A másolási folyamat felgyorsításához:

- Használjon több másolási adatfolyamot. Használja például a Robocopy többszálas lehetőségét. A pontos parancsát további információért látogasson el [oktatóanyag: Adatok másolása az Azure Data Box, és ellenőrizze](data-box-deploy-copy-data.md).
- Használjon több munkamenetet.
- Például a hálózati megosztásból történő másolás helyett (ahol a hálózat sebessége korlátokat szabhat) gondoskodjon róla, hogy az adatok azon a helyi számítógépen legyenek, amelyhez a Data Boxot csatlakoztatja.
- Mérje meg az adatok másolására használt számítógép teljesítményét. Töltse le és használja a [Bluestop FIO eszközt](https://bluestop.org/fio/), amellyel megmérheti a kiszolgáló hardverének teljesítményét.

<!--### Q. How to speed up the data copy if the source data has small files (KBs or few MBs)?
A.  To speed up the copy process:

- Create a local VHDx on fast storage or create an empty VHD on the HDD/SSD (slower).
- Mount it to a VM.
- Copy files to the VM’s disk.-->


### <a name="q-can-i-use-multiple-storage-accounts-with-data-box"></a>K. Használhatok több tárfiókot is a Data Boxhoz?
A.  Igen. A Data Boxhoz legfeljebb 10 tárfiók használata támogatott. Ezek lehetnek általános célú, klasszikus vagy Blob Storage-fiókok. A gyakori és a ritka elérésű blob is támogatott. 


## <a name="ship-device"></a>Eszköz kiszállítása

<!--### Q. How do I schedule a pickup for my Data Box?--> 

### <a name="q-my-device-was-delivered-but-the-device-seems-to-be-damaged-what-should-i-do"></a>K. Megérkezett az eszközöm, de sérültnek tűnik. Mit tegyek?
A. Ha az eszköz sérülten érkezett, vagy a jelek illetéktelen hozzáférésre utalnak, ne használja az eszközt. Vegye fel a kapcsolatot a [Microsoft támogatási szolgálatával](data-box-disk-contact-microsoft-support.md), és a lehető leghamarabb küldje vissza az eszközt. Létrehozhat egy új Data Box-megrendelést is egy csereeszköz igényléséhez. Ebben az esetben nem számítjuk fel a csereeszköz árát.

### <a name="q-can-i-use-my-own-shipping-carrier-to-ship-data-box"></a>K. Használhatom a saját futáromat a Data Box szállításához?
A. A Data Box szolgáltatás keretében a Microsoft intézi az Azure-adatközpontba és onnan történő szállítást. Ha saját futárt szeretne igénybe venni, használja az Azure Import/Export szolgáltatást. További információ [az Azure Import/Export szolgáltatásról](../storage/common/storage-import-export-service.md).

### <a name="q-will-my-data-box-devices-cross-country-borders-during-shipping"></a>K. Data Box eszközök közötti ország szegélyek fog szállítási során?
A. Minden Data Box-eszköz belül a rendeltetési országot postáztuk, és nem lesz adatbázisközi bármely nemzetközi határokon. Az egyetlen kivétel, az Európai Unió (EU), ahol eszközök szállíthat, és bármely EU országból rendeléseket. Ez vonatkozik a Data Box és a Data Box nehéz eszközökön is.

### <a name="q-e-ink-display-is-not-showing-the-return-shipment-label-what-should-i-do"></a>K. Az E-ink-képernyőn nem jelenik meg a csomagvisszaküldési címke. Mit tegyek?
A. Ha az E Ink-képernyőn nem jelenik meg a csomagvisszaküldési címke, hajtsa végre a következő lépéseket:
- Távolítsa el a régi szállítási címkét és minden egyéb, az előző szállításból származó címkét.
- Nyissa meg az Azure Portalon a megrendelését. Lépjen az **Áttekintés** oldalra, és **töltse le a fuvarlevélcímkét**. További információ a [fuvarlevélcímke letöltéséről](data-box-portal-admin.md#download-shipping-label).
- Nyomtassa ki a címkét, és helyezze az eszközre rögzített átlátszó műanyag tasakba. 
- Ügyeljen arra, hogy a fuvarlevélcímke jól látható legyen. 

### <a name="q-how-is-my-data-protected-during-transit"></a>K. Milyen védelem alatt állnak az adataim a szállítás közben? 
A.  Az átvitel során a Data Box következő funkciói védik az adatokat.
 - A Data Box-lemezek 256 bites AES-titkosítással vannak titkosítva. 
 - Az eszköz zárolva van, és egy zárolásfeloldási jelszót igényel a belépéshez és az adatok eléréséhez.
További információ [a Data Box biztonsági funkcióiról](data-box-security.md).  

### <a name="q-i-have-finished-prepare-to-ship-and-shut-down-the-device-can-i-still-add-more-data-to-data-box"></a>K. Előkészítettem a szállításhoz, és leállítottam az eszközt. Hozzáadhatok még adatokat?
A. Igen. Bekapcsolhatja az eszközt, és további adatokat adhat hozzá. A másolás végeztével újra futtatnia kell a **szállításra való előkészítés** műveletet.

### <a name="q-i-received-my-device-and-it-is-not-booting-up-how-do-i-ship-the-device-back"></a>K. Az eszköz kapott, és a van nem másolatából? Hogyan szállíthat a vissza az eszközt?
A. Ha az eszköz nem indul el, nyissa meg az ahhoz az Azure Portalon. Egy levélcímke letöltése, és elhelyezi, hogy az eszközön. További információ a [fuvarlevélcímke letöltéséről](data-box-portal-admin.md#download-shipping-label).

## <a name="verify-and-upload"></a>Ellenőrzés és feltöltés

### <a name="q-how-soon-can-i-access-my-data-in-azure-once-ive-shipped-the-data-box-back"></a>K. A Data Box visszaküldését követően mennyi időn belül férhetek hozzá az adataimhoz az Azure-ban? 
A.  Amint az **Adatmásolás** megrendelési állapota **befejeződöttként** jelenik meg, azonnal hozzáférhet az adataihoz.

### <a name="q-where-is-my-data-located-in-azure-after-the-upload"></a>K. A feltöltést követően hol lesznek az adataim az Azure-ban?
A.  Attól függően, hogy az adatok blokkblob vagy lapblob vagy az Azure files a Data Box, másolja az adatokat, amikor az adatok töltenek fel az Azure Storage-fiókban a következő útvonalak egyikét.
- `https://<storage_account_name>.blob.core.windows.net/<containername>` 
- `https://<storage_account_name>.file.core.windows.net/<sharename>`
 
  Alternatív megoldásként navigálhat az Azure Storage-fiókjából is az Azure Portalon.

### <a name="q-i-just-noticed-that-i-did-not-follow-the-azure-naming-requirements-for-my-containers-will-my-data-fail-to-upload-to-azure"></a>K. Most vettem észre, hogy nem követtem az Azure elnevezési követelményeit a tárolóim esetében. Így is fel lehet tölteni az adataimat az Azure-ba?
A.  Ha a tároló nevének nagybetű, majd ezeket a neveket automatikusan alakulnak kisbetűs. Ha a nevek valami más szempontból nem megfelelőek (például speciális karaktereket tartalmaznak, más nyelvűek stb.) akkor a feltöltés meghiúsul. További információk a megosztások, tárolók és fájlok elnevezésének ajánlott eljárásairól:
- [Megosztások elnevezése és hivatkozása](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Blokkblobok és lapblobok konvenciói](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).

### <a name="q-how-do-i-verify-the-data-i-copied-onto-data-box"></a>K. Hogyan tudom ellenőrizni a Data Boxra feltöltött adataimat?
A.  Az adatmásolást követően a **szállításra való előkészítés** művelet futtatásakor a rendszer ellenőrzi az adatokat. Az ellenőrzési folyamat során létrehoz egy listát az adatfájlokról és azok ellenőrzőösszegeiről. Töltse le a fájlokat, és ellenőrizze a fájlokat a forrásadatok a listában. További információ [a szállításra való előkészítésről](data-box-deploy-picked-up.md#prepare-to-ship).

### <a name="q-what-happens-to-my-data-after-i-have-returned-the-data-box"></a>K. Mi történik az adataimmal, miután visszaküldtem a Data Boxot?
A.  Ha befejeződött az adatok Azure-ba történő másolása, a rendszer a Data Box-lemezeken található adatokat a NIST SP 800-88 Revision 1 útmutatásainak megfelelően biztonságosan törli. További információ [az adatok a Data Boxról történő törléséről](data-box-deploy-picked-up.md#erasure-of-data-from-data-box).

## <a name="audit-report"></a>Naplózási jelentés

### <a name="how-does-azure-data-box-service-help-support-customers-chain-of-custody-procedure"></a>Hogyan támogatja az Azure Data Box szolgáltatás az ügyfelek felügyeletilánc-eljárásait?
A.  Az Azure Data Box szolgáltatás natív módon biztosít jelentéseket, amelyek felhasználhatók a felügyeleti láncok dokumentációjához. A naplózási és másolási naplók az Azure-beli tárfiókban érhetők el, a [rendelési előzmények letöltése](data-box-portal-admin.md#download-order-history) pedig az Azure Portalon lehetséges a teljesítést követően.


### <a name="what-type-of-reporting-is-available-to-support-chain-of-custody"></a>Milyen típusú jelentések érhetők el a felügyeleti láncokhoz?
A.  A következő típusú jelentések érhetők el a felügyeleti láncokhoz:

- A UPS logisztikai átviteli.
- Bekapcsolási naplók és a felhasználói megosztások hozzáférési naplói.
- Fájljegyzék a Data Boxon sikeresen feldolgozott minden fájlról, 64 bites ciklikus redundancia-ellenőrzéssel (CRC-64) vagy ellenőrzőösszegekkel.
- Jelentések azon fájlokról, amelyeknek sikertelen volt a feltöltése az Azure-tárfiókba.
- A Data Box (az NIST 800 88R1 szabvány szerinti) megtisztítása az adatok Azure-tárfiókba való átmásolását követően.

### <a name="are-the-carrier-tracking-logs-from-ups-available"></a>Azok a szállítmányozó nyomkövetési naplóinak (UPS) érhető el? 
A.  A futárcégek nyomkövetési naplóit a Data Box rendelési előzményei tartalmazzák. Ez a jelentés azt követően érhető el, miután az eszköz visszatért az Azure-adatközpontba, és a lemezeken lévő adatok törölve lettek. Ha azonnali információra van szüksége, közvetlenül a futárcég webhelyén, a megrendelés nyomkövetési számát használva lekérheti a nyomkövetési információkat.

### <a name="can-i-transport-the-data-box-to-azure-datacenter"></a>Elszállíthatom magam a Data Boxot az Azure-adatközpontba? 
A.  Nem. Azure-adatközpont jelenleg nem fogadja el a Data Box kézbesítési ügyfelek vagy a szállító Elvégzéséért eltérő.


## <a name="next-steps"></a>További lépések

- A [Data Box rendszerkövetelményeinek](data-box-system-requirements.md) áttekintése.
- A [Data Box korlátjainak](data-box-limits.md) értelmezése.
- Az [Azure Data Box](data-box-quickstart-portal.md) gyors üzembe helyezése az Azure Portalon.
