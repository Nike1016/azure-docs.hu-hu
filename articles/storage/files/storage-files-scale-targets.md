---
title: Az Azure Files méretezhetőségi és teljesítménycéljai |} A Microsoft Docs
description: További információk a méretezhetőségi és teljesítménycéljai az Azure Files között, beleértve a kapacitás, a kérelmek arányának és a bejövő és kimenő sávszélesség korlátja.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 5/5/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: ed4aa832c4ec7ccda760d535aa920be8d5c4e2e3
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699628"
---
# <a name="azure-files-scalability-and-performance-targets"></a>Az Azure Files méretezhetőségi és teljesítménycéljai

[Az Azure Files](storage-files-introduction.md) teljes körűen felügyelt fájlmegosztást kínáló, amely az iparági szabvány SMB protokollon keresztül érhető el a felhőben. Ez a cikk ismerteti a méretezhetőségi és teljesítménycéljai Azure Files és az Azure File Sync.

Az itt felsorolt méretezhetőségi és teljesítménycéljai csúcskategóriás célozza, de hatással lehet más változókat a központi telepítésben. Például egy fájl az átviteli sebességet is korlátozhatja a rendelkezésre álló hálózati sávszélességet, nem csak a az Azure Files szolgáltatást futtató kiszolgálók. Erősen ajánlott a tesztelés a használati mintáját, és határozza meg, a méretezhetőség és teljesítmény az Azure Files megfelel-e a követelményeknek. Idővel ezek a korlátok növelését is igyekszünk áthelyezését. Visszajelzését, vagy a Megjegyzések alatt vagy a véleménye a [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files), mely korlátaival hozzánk növelni szeretné.

## <a name="azure-storage-account-scale-targets"></a>Az Azure storage-fiók méretezési célok

A szülő erőforrás Azure-fájlmegosztások az Azure storage-fiók. Storage-fiók az Azure-beli adatok tárolására használható több tárolószolgáltatások, beleértve az Azure Files között, amelyet egy tárolókészletet jelöli. Egyéb szolgáltatások, amelyek az adatok tárolása a storage-fiókok az Azure Blob storage, Azure Queue storage és Azure Table storage. A következő célokat a alkalmazni adatok tárolása a storage-fiókban az összes tárolási szolgáltatások:

[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

> [!Important]  
> Az általános célú Storage-fiókok más tárolási szolgáltatásokból való kihasználtsága hatással van az Azure-fájlmegosztás használatára a Storage-fiókban. Például ha eléri a maximális tárfiókok kapacitásával az Azure Blob storage, nem tudja hozni az Azure-fájlmegosztás, az új fájlok akkor is, ha az Azure-fájlmegosztás maximális megosztási méreténél.

## <a name="azure-files-scale-targets"></a>Az Azure Files méretezési célok

Háromféle korlátozást kell figyelembe venni a Azure Files: Storage-fiókok,-megosztások és-fájlok esetében.

Példa: A prémium fájlmegosztás esetében egyetlen megosztás 100 000 IOPS érhet el, és egyetlen fájl akár 5 000 IOPS is méretezhető. Ha tehát három fájl található egy megosztásban, az adott megosztásból lekérhető maximális IOPS 15 000.

### <a name="standard-storage-account-limits"></a>Standard Storage-fiók korlátai

Ezeket a korlátokat az [Azure Storage-fiók méretezési céljai](#azure-storage-account-scale-targets) című szakaszban találja.

### <a name="premium-filestorage-account-limits"></a>Prémium szintű FileStorage-fiók korlátai

[!INCLUDE [azure-storage-limits-filestorage](../../../includes/azure-storage-limits-filestorage.md)]

> [!IMPORTANT]
> A Storage-fiókra vonatkozó korlátozások minden megosztásra érvényesek. A FileStorage-fiókok maximálisra skálázása csak akkor érhető el, ha FileStorage-fiókban csak egy megosztás van.

### <a name="file-share-and-file-scale-targets"></a>Fájlmegosztás és méretezési célok

> [!NOTE]
> Az 5 TiB-nál nagyobb standard fájlmegosztás előzetes verzióban érhető el, és bizonyos korlátozásokkal rendelkezik.
> A korlátozások listájának megtekintéséhez és a nagyobb fájlmegosztás-méretek előzetes verziójának előkészítéséhez tekintse meg a tervezési útmutató [szabványos fájlmegosztás](storage-files-planning.md#standard-file-shares) szakaszát.

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

[!INCLUDE [storage-files-premium-scale-targets](../../../includes/storage-files-premium-scale-targets.md)]

## <a name="azure-file-sync-scale-targets"></a>Az Azure File Sync méretezési célok

Azure File Sync a korlátlan használat céljával lett tervezve, de a korlátlan használat nem mindig lehetséges. Az alábbi táblázat a Microsoft tesztelésének határait mutatja be, és azt is jelzi, hogy mely célok rögzített határértékek:

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

### <a name="azure-file-sync-performance-metrics"></a>Az Azure File Sync teljesítmény-mérőszámok

Mivel a Azure File Sync ügynök egy olyan Windows Server rendszerű gépen fut, amely az Azure-fájlmegosztást csatlakoztatja, a tényleges szinkronizálási teljesítmény számos tényezőtől függ az infrastruktúrában: A Windows Server és a mögöttes lemez konfigurációja, a kiszolgáló és az Azure Storage közötti hálózati sávszélesség, a fájlméret, a teljes adatkészlet mérete és az adatkészlet tevékenysége. Mivel az Azure File Sync a fájlok szintjén működik, a teljesítményjellemzők az Azure File Sync-alapú megoldás jobban mérik a másodpercenként feldolgozott (fájlok és könyvtárak) objektumok száma.

Az Azure File Sync, a teljesítmény fontos két szakaszban végzik:

1. **Egyszeri kiépítés kezdeti időpontja**: A kezdeti kiépítés teljesítményének optimalizálása érdekében tekintse meg a bevezetést a [Azure file Sync](storage-sync-files-deployment-guide.md#onboarding-with-azure-file-sync) az optimális üzembe helyezési részletekért.
2. **Folyamatban lévő szinkronizálás**: Az Azure-fájlmegosztás első beültetése után Azure File Sync több végpontot is megtart a szinkronizálásban.

Segítséget az egyes szakaszok üzembe helyezésének megtervezése, az alábbiakban az eredmények figyelhetők egy konfigurációs rendszereken belső tesztelése során

| Rendszer-konfiguráció |  |
|-|-|
| CPU | 64 virtuális maggal és 64 MiB L3-gyorsítótár |
| Memory (Memória) | 128 GiB |
| Lemez | Akkumulátor a RAID 10-zel SAS-lemezek biztonsági gyorsítótár |
| Network (Hálózat) | 1 GB/s hálózat |
| Számítási feladat | Általános célú fájlkiszolgáló|

| Kezdeti egyszeri kiépítése  |  |
|-|-|
| Objektumok száma | 25 000 000 objektum |
| Adatkészlet mérete| ~ 4,7 TiB |
| Átlagos mérete | ~ 200 KiB (legnagyobb fájl: 100 GiB) |
| Töltse fel az átviteli sebesség | másodpercenként 20 objektumok |
| Namespace letöltési átviteli * | 400 objektum/másodperc |

Ha a kiszolgáló új végpont létrehozása az Azure File Sync ügynök nem töltse le a fájl tartalma. Először szinkronizálja a teljes névterét és a majd eseményindítók háttér-e letölteni a fájlokat, akár teljes egészében visszaírási, vagy ha, felhőbeli rétegezés engedélyezve van, a kiszolgálói végpont beállított felhőalapú rétegzési szabályzat.

| Folyamatban lévő szinkronizálás  |   |
|-|--|
| Szinkronizált objektumok száma| 125,000 objektumok (~ 1 %-os forgalom) |
| Adatkészlet mérete| 50 GB |
| Átlagos mérete | Körülbelül 500 KiB |
| Töltse fel az átviteli sebesség | másodpercenként 30 objektumok |
| Teljes átviteli sebesség * | 60 objektum/másodperc |

\* Ha felhőbeli rétegezés engedélyezve van, akkor valószínű, hogy tekintse át az adatokat a rendszer letölti a fájlt csak néhány jobb teljesítményt. Az Azure File Sync csak letölti az adatokat a gyorsítótárban tárolt fájlok, a végpontok bármelyikét, módosításakor. Rétegzett vagy újonnan létrehozott fájlokhoz az ügynök nem tölti le a fájl adatait, és inkább csak szinkronizálja a névtér összes kiszolgálói végpontot. Az ügynök is támogatja a részleges letöltés rétegzett fájlok, a felhasználó által elért. 

> [!Note]  
> A fenti számok nem állnak arra utalhat, hogy a teljesítmény, amely fog tapasztalni. A tényleges teljesítmény több tényezőtől függ, ez a szakasz elején leírt módon.

Az üzemelő példány általános útmutatásként kell tartania néhány dolgot figyelembe:

- Az objektum átviteli körülbelül méretezi a kiszolgálón a szinkronizálási csoportok száma arányos. Jobb átviteli sebességet, amely is korlátozza a kiszolgáló és a hálózat egy kiszolgálón több szinkronizálási csoportokba felosztása a adatokat eredményez.
- Az objektum átviteli sebesség a MiB s sebességet fordítottan arányos. A kisebb fájlok esetében tapasztalható nagyobb átviteli sebességet egy második, de alacsonyabb MiB s sebességet feldolgozott objektumok számának tekintetében. Ezzel szemben nagyobb méretű fájlok, kap egy második, de magasabb MiB s sebességet feldolgozott kevesebb objektumot. Az Azure Files méretezési célokat a MiB s sebességet korlátozza.

## <a name="see-also"></a>Lásd még

- [Az Azure Files üzembe helyezésének megtervezése](storage-files-planning.md)
- [Az Azure File Sync üzembe helyezésének megtervezése](storage-sync-files-planning.md)
- [Más tárolási szolgáltatásokra méretezhetőségi és teljesítménycéljai](../common/storage-scalability-targets.md)
