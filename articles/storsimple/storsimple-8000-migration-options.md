---
title: Adatok áttelepítése az a StorSimple 5000-7000-es sorozat lehetőségeinek kiértékeléséhez |} A Microsoft Docs
description: A beállítások át adatokat a StorSimple 5000-7000-es sorozat áttekintést nyújt.
services: storsimple
documentationcenter: NA
author: alkohli
manager: twooley
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 1e637f58b392b2de67a5ead9d57a6a87ab705b93
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60631609"
---
# <a name="options-to-migrate-data-from-storsimple-5000-7000-series"></a>Adatok áttelepítése az a StorSimple 5000-7000-es sorozat beállításai 

> [!IMPORTANT]
> A 2019. július 9. a StorSimple 5000/7000 sorozat megszűnik a támogatás (EOS) állapotát. Azt javasoljuk, hogy a StorSimple 5000/7000 sorozat ügyfelek áttelepítése a a dokumentumban ismertetett alternatív megoldások egyikét.

A StorSimple 5000-7000-es sorozat eléri-e [támogatásuk](https://support.microsoft.com/lifecycle/search?alpha=StorSimple%205000%2F7000%20Series) a július 2019. A futtató ügyfelek StorSimple 5000-7000-es sorozat rendelkezik más Azure-bA frissítését belső hibrid szolgáltatások. Ez a cikk ismerteti a rendelkezésre álló adatok migrálása az Azure hybrid lehetőségeket. 

## <a name="migration-options"></a>Áttelepítési beállítások

A StorSimple használatával az ügyfelek 5000 – 7000 sorozatú Azure-ban vagy külső beállítások rendelkezik.

### <a name="azure-options"></a>Azure-lehetőségek

#### <a name="upgrade-to-storsimple-8000-series"></a>Frissítés a StorSimple 8000 sorozat

A StorSimple 8000 sorozat váltson, és így továbbra is a StorSimple-platformon.  Ezeket a frissítési lépéseket igényel az ügyfelek számára, hogy az 5000 – 7000 sorozatú eszközöket cserélje le a 8000-es sorozat. Az adatok áttelepítése az 5000 – 7000 sorozatú eszközről az áttelepítési eszköz használatával. Az áttelepítés sikeres végrehajtása után a StorSimple 8000 sorozatú eszközök továbbra is az Azure Blob Storage-szintű adatforgalom. 

A StorSimple 8000 sorozat használata az adatok migrálása további információkért látogasson el [át adatokat a StorSimple 5000-7000-es sorozat a 8000-es sorozatú eszköz](storsimple-8000-migrate-from-5000-7000.md).

#### <a name="migrate-to-azure-file-sync"></a>Migrálás az Azure File Syncre

A teljesen új áttelepítési beállítás lehetővé teszi, hogy az ügyfelek számára a szervezeti fájlmegosztások tárolása az Azure Files. Ezek a fájlmegosztások az Azure File Sync (AFS) használatával a helyszíni hozzáférés majd központi üzemelnek. AFS is telepíthető a Windows Server-gazdagépen. Az adatok tényleges áttelepítése akkor hajt végre gazdagépként példányát, vagy az áttelepítési eszköz használata.

Adatok áttelepítése az Azure File Sync további információkért látogasson el [át adatokat a StorSimple 5000-7000-es sorozat az Azure File Sync](storsimple-5000-7000-afs-migration.md).

### <a name="third-party-options"></a>Külső beállításai

#### <a name="migrate-to-panzura-freedom-nas"></a>Panzura szabadon NAS áttelepítése

A StorSimple 5000-7000-es ügyfelek kiválaszthatják a Panzura szabadon NAS tárolni az adatokat az Azure-ban való áttelepítéséhez. Panzura szabadon megoldás egy NAS-megoldás, adatközpontok, iroda, nyilvános és magánfelhőkben is biztosít. A megoldás lehetővé teszi a helyi, hibrid és a felhőbeli adatok munkafolyamatok NFS, SMB és mobilos ügyfeleket. 

Ez az áttelepítés Panzura által támogatott és Kezdésként igénylésével az áttelepítés támogatása a [Panzura webhely](https://panzura.com/storsimple-migration/).

#### <a name="migrate-to-cohesity"></a>A Cohesity áttelepítése

Cohesity lehetővé teszi, hogy a jelenlegi storsimple 5000 – 7000-es adatok áttelepítését a Cohesity Data Platform az Azure-ban. A Cohesity Adatplatform, amely egyesíti a fájlokat, a biztonsági mentések, a objektumok és a virtuális gépek egyetlen natív felhőalapú megoldás alakzatot szoftveresen definiált webes méretű megoldás is. A Data platform az áttelepítés után kezelése, védelme és kiépítése az adatokhoz és alkalmazásokhoz a felhőből egy egyetlen tekinthesse keresztül maghoz. Indítsa el a Cohesity, mindössze három csomóponttal. 

További információ a [a Cohesity Data Platform az áttelepítés](https://info.cohesity.com/migrate-from-storsimple-to-cohesity.html).

#### <a name="migrate-to-nasuni"></a>A Nasuni áttelepítése

A Nasuni megkönnyíti a StorSimple 5000-7000-es ügyfelek migrálása és tárolni az adatokat az Azure-ban.  A Nasuni vezető NAS Azure-alapú tárolási megoldás, így a vevők a teljesítmény és a biztonság feláldozása a helyszíni megoldások, a felhőbeli adatvédelemmel és a méretezési csoport.  Mellett a file storage nagy teljesítményű, és az Azure a Nasuni leíró biztonsági mentés és Vészhelyreállítás, miközben lehetővé teszi, hogy a megosztás és együttműködés az adatokon a központosított fájl storage management a világ minden pontján. 

A Nasuni a működés, győződjön meg arról, a migrálás egyszerű – kezdje el már ma rendelkezik: https://info.nasuni.com/nasuni-storsimple-migration

#### <a name="migrate-to-talon-fast"></a>A Talon FAST áttelepítése

Talon megkönnyíti a StorSimple 5000-7000-es ügyfeleinek, hogy azok értékű sokféle a StorSimple-platform (kis méretű helyszíni erőforrás-igényű korlátlan felhőalapú erőforrások által támogatott) előnyeit kihasználva még nagyobb függvénnyel.  A Talon gyors megoldás révén ügyfeleink telepíthet át, és tartsa adataikat az Azure-ban, most már egy csak szoftveres helyszíni még kisebb üzembe helyezésének előkészítése kapcsolatban, és további előnyöket, például a globális fájl hozzáadása közben zárolása, globális névtér és az együttműködést a többhelyes.  Talon a vezető Azure-ökoszisztéma megoldás, globális ügyfelekkel együttműködve egy konszolidált, az Azure-alapú erőforrás-igényű megalkuvás felhasználói munkafolyamat vagy tapasztalatok nélkül be azok a helyszíni fájlkiszolgálók server számítási feladatok migrálása.  

Ismerje meg, hogyan fejlesztheti tovább egy felhőbeli konszolidált Enterprise, https://www.talonstorage.com/alliances/microsoft-storsimple.


## <a name="migration---frequently-asked-questions"></a>Migrálás – gyakori kérdések

### <a name="q-when-do-the-storsimple-5000-and-7000-series-devices-reach-end-of-service"></a>K. Ha nem a StorSimple 5000 és 7000 sorozatú eszköz éri el a szolgáltatás teljes? 

A. A StorSimple 5000-7000-es sorozat elérni [szolgáltatás végén](https://support.microsoft.com/lifecycle/search?alpha=StorSimple%205000%2F7000%20Series) a július 2019. Szolgáltatás végén, az azt jelenti, hogy a Microsoft már nem tudja támogatást nyújt a hardver és szoftver ezen eszközök július 2019 után. Kifejezetten javasoljuk, hogy először, át az adatokat az eszközök most már terv kidolgozásában.

### <a name="q-what-happens-to-the-data-i-have-stored-in-azure"></a>K. Mi történik, az I az Azure-ban tárolt adatokat?  

A. Továbbra is használhatja az adatokat az Azure-ban, egy újabb szolgáltatás áttelepítése után. 


### <a name="q-what-happens-to-the-data-i-have-stored-locally-on-my-storsimple-device"></a>K. Mi történik az adatok I rendelkezik helyi tárolására az a StorSimple eszköz? 

A. Az adatok a helyi eszközön, az újabb szolgáltatásba az áttelepítési dokumentációban leírt módon lehet másolni.

### <a name="q-what-happens-if-i-want-to-keep-my-storsimple-50007000-series-appliance"></a>K. Mi történik, ha szeretnék, hogy a StorSimple 5000/7000 sorozat készülék? 

A. A szolgáltatások esetleg tovább használhatók, amíg a Microsoft már nem lesz képes biztosítani a hardver- és támogatási. Áttelepítési erősen ajánlott az üzletmenet folytonosságáról.

### <a name="q-what-options-are-available-to-migrate-data-from-storsimple-5000-7000-series-devices"></a>K. Milyen lehetőségek állnak rendelkezésre adatok áttelepítése az a StorSimple 5000-7000-es sorozatú eszközöket? 

A. A forgatókönyvtől függően a StorSimple 5000-7000-es sorozat felhasználók rendelkeznek az alábbi áttelepítési lehetőségek. 

 - **Frissítés a 8000-es sorozat**: Használja ezt a beállítást, ha folytatja a StorSimple-platformon. 
 - **Az Azure File Sync áttelepítése**: Használja ezt a beállítást, ha azt szeretné, váltson át az Azure natív formátumban. Az Azure File Sync fájlmegosztások központi kezelésére használható. 

Áttelepítési beállítások nem szerepel itt vitatni Support forduljon.

### <a name="q-is-migration-to-other-storage-solutions-supported"></a>K. Az áttelepítés egyéb tárolási megoldások támogatott?

A. Igen. Egyéb tárolási megoldások használata az adatok másolatát gazdagépre történő áttelepítése támogatott.

### <a name="q-is-migration-supported-by-microsoft"></a>K. A Microsoft által támogatott áttelepítés? 

A. Az 5000 és 7000 sorozatú-ről egy olyan teljes körűen támogatott művelet. Valójában a Microsoft azt javasolja, lépjen kapcsolatba a támogatási migrálás megkezdése előtt. Migrálás jelenleg egy támogatott művelet. Ha az adatok áttelepíthetők a StorSimple 5000-7000-es sorozatú eszközre, szeretne [hozzon létre egy támogatási jegyet](storsimple-8000-contact-microsoft-support.md).

### <a name="q-what-is-the-pricing-model-for-both-the-migration-options"></a>K. Mi az az áttelepítési lehetőségek díjszabási modellje?

A. Az áttelepítés költség lehetőségtől függően változik. Áttelepítési maga ingyenes, ha úgy dönt, hogy frissítse a StorSimple 8000 sorozat pedig a hardvereszköz költsége lesz. 

Ehhez hasonlóan az Azure File Sync használata esetén a szolgáltatás az előfizetési díjak lehetnek érvényben. Minden esetben ügyfelek is megkapják a folyamatban lévő tárolási költségeket fizetni. Tekintse meg a következő becslésre használható: 
- [A StorSimple-díjszabás](https://azure.microsoft.com/pricing/details/storsimple/)  
- [AFS díjszabása]( https://azure.microsoft.com/pricing/details/storage/files/)

### <a name="q--how-long-does-it-take-to-complete-a-migration"></a>K.  Mennyi ideig tart egy áttelepítés elvégzéséhez?

A. Adatok áttelepítése az idő az adatok és a frissítési lehetőséget kiválasztva függ. 

### <a name="q-what-is-the-end-of-support-date-for-storsimple-8000-series"></a>K. Mi az a StorSimple 8000 sorozat közötti-támogatás dátumát?

A. A StorSimple 8000 sorozat közötti-támogatás dátumát közzé van téve [Itt](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series).


## <a name="next-steps"></a>További lépések
 - [Telepítse át az adatokat a storsimple 5000-7000-es sorozat a 8000-es sorozatú eszköz](storsimple-8000-migrate-from-5000-7000.md).
 - [Az Azure File Sync át adatokat a StorSimple 5000-7000-es sorozat](storsimple-5000-7000-afs-migration.md)
