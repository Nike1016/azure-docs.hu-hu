---
title: Az adatáttelepítés Azure File Syncba Azure Data Box és más módszerek használatával
description: A tömeges adatátvitelt olyan módon telepítse át, amely kompatibilis a Azure File Syncával.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: b0c9d55846a0240dde92de16ea17e9403a112c3e
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699222"
---
# <a name="migrate-bulk-data-to-azure-file-sync"></a>Tömeges adatáttelepítés Azure File Syncba
A tömeges adatmennyiségeket kétféleképpen is áttelepítheti Azure File Syncba:

* **Töltse fel a fájlokat Azure File Sync használatával.** Ez a legegyszerűbb módszer. A fájlokat helyileg helyezheti át a Windows Server 2012 R2 vagy újabb verzióra, és telepítheti a Azure File Sync ügynököt. A szinkronizálás beállítása után a fájlok a kiszolgálóról lesznek feltöltve. (Ügyfeleink jelenleg 1 TiB átlagos feltöltési sebességét tapasztalják kéthetente.) Annak biztosítása érdekében, hogy a kiszolgáló ne használjon túl sok sávszélességet az adatközpontban, érdemes lehet [sávszélesség-szabályozási ütemtervet](storage-sync-files-server-registration.md#ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter)beállítani.
* **Fájlok átvitele offline állapotba.** Ha nem rendelkezik elegendő sávszélességgel, előfordulhat, hogy nem tudja ésszerű időn belül feltölteni a fájlokat az Azure-ba. A kihívás a fájlok teljes készletének kezdeti szinkronizálása. A probléma megoldásához használja az offline tömeges áttelepítési eszközöket, például a [Azure Data Box családot](https://azure.microsoft.com/services/storage/databox). 

Ez a cikk azt ismerteti, hogyan lehet a fájlokat offline módon áttelepíteni úgy, hogy az kompatibilis legyen Azure File Syncával. A fájlok ütközésének elkerüléséhez és a fájl-és mappa-hozzáférés-vezérlési listák (ACL-ek) és időbélyegek megőrzéséhez kövesse az alábbi utasításokat.

## <a name="online-migration-tools"></a>Online áttelepítési eszközök
A cikkben ismertetett folyamat nem csak Data Box, hanem más offline áttelepítési eszközök esetében is működik. Emellett olyan online eszközökre is használható, mint a AzCopy, a Robocopy vagy a partneri eszközök és szolgáltatások. Azonban a kezdeti feltöltési kihívás leküzdése érdekében kövesse a cikkben ismertetett lépéseket, hogy azok a Azure File Syncsal kompatibilis módon használhassák ezeket az eszközöket.


## <a name="benefits-of-using-a-tool-to-transfer-data-offline"></a>Az adatok offline átvitelére szolgáló eszköz használatának előnyei
Az alábbi fő előnyökkel jár az olyan átviteli eszközök használata, mint a Data Box az offline áttelepítéshez:

- Nem kell feltöltenie az összes fájlt a hálózaton keresztül. Nagyméretű névterek esetében ez az eszköz jelentős hálózati sávszélességet és időt takaríthat meg.
- Ha Azure File Sync használ, függetlenül attól, hogy melyik továbbítási eszközt használja (Data Box, az Azure import/export szolgáltatás stb.), az élő kiszolgáló csak azokat a fájlokat tölti fel, amelyek az adatok Azure-ba való áthelyezését követően változnak.
- Azure File Sync szinkronizálja a fájl-és mappa ACL-jeit még akkor is, ha az offline tömeges áttelepítési eszköz nem rendelkezik az ACL-ekkel.
- Data Box és Azure File Sync nem igényel állásidőt. Ha a Data Box használatával helyezi át az adatátvitelt az Azure-ba, hatékonyan használja a hálózati sávszélességet, és megőrizheti a fájl megbízhatóságát. A névteret naprakészen tarthatja úgy is, hogy csak az adatok Azure-ba való áthelyezését követően módosult fájlokat tölt fel.

## <a name="prerequisites-for-the-offline-data-transfer"></a>Az offline adatátvitel előfeltételei
Az offline adatátvitel befejezése előtt ne engedélyezze a szinkronizálást az áttelepíteni kívánt kiszolgálón. A Kezdés előtt megfontolandó szempontok a következők:

- Ha a tömeges Migrálás Data Box használatát tervezi: Tekintse át a [Data Box üzembe helyezésének](../../databox/data-box-deploy-ordered.md#prerequisites)előfeltételeit.
- A végső Azure File Sync topológia megtervezése: [Azure File Sync központi telepítésének megtervezése](storage-sync-files-planning.md)
- Válassza ki azokat az Azure Storage-fiókokat, amelyek a-val szinkronizálni kívánt fájlmegosztást fogja tárolni. Győződjön meg arról, hogy a tömeges áttelepítés ugyanazon a Storage-fiók (ok) ban lévő ideiglenes előkészítési megosztások esetében történik. A tömeges Migrálás csak olyan végleges és átmeneti megosztás használatával engedélyezhető, amely ugyanabban a Storage-fiókban található.
- A tömeges áttelepítést csak akkor lehet használni, ha új szinkronizálási kapcsolatot hoz létre egy kiszolgáló helyével. Meglévő szinkronizálási kapcsolattal nem engedélyezhető a tömeges áttelepítés.


## <a name="process-for-offline-data-transfer"></a>Offline adatátviteli folyamat
A következőképpen állíthatja be a Azure File Sync úgy, hogy az kompatibilis legyen a tömeges áttelepítési eszközökkel, például a Azure Data Boxekkel:

![A Azure File Sync beállítását bemutató ábra](media/storage-sync-files-offline-data-transfer/data-box-integration-1-600.png)

| Lépés | Részletek |
|---|---------------------------------------------------------------------------------------|
| ![1\. lépés](media/storage-sync-files-offline-data-transfer/bullet_1.png) | [Rendeljen Data Box](../../databox/data-box-deploy-ordered.md). A Data Box család [számos olyan terméket](https://azure.microsoft.com/services/storage/databox/data) kínál, amelyek megfelelnek az igényeinek. Ha megkapja a Data Boxt, kövesse a [dokumentációját, és másolja](../../databox/data-box-deploy-copy-data.md#copy-data-to-data-box) át az adatait erre az UNC elérési útra a Data Box:  *\\< DeviceIPAddres StorageAccountName_AzFile\> \>\<\< Megosztásnév\>* . Itt a *megosztásnév* az átmeneti megosztás neve. Küldje vissza a Data Box az Azure-ba. |
| ![2\. lépés](media/storage-sync-files-offline-data-transfer/bullet_2.png) | Várjon, amíg a fájlok megjelennek az ideiglenes előkészítési megosztásként választott Azure-fájlmegosztás alatt. *Ne engedélyezze ezen megosztások szinkronizálását.* |
| ![3\. lépés](media/storage-sync-files-offline-data-transfer/bullet_3.png) | Hozzon létre egy új, üres megosztást minden olyan fájlmegosztás esetében, amelyet Data Box hozott létre. Az új megosztásnak ugyanabban a Storage-fiókban kell lennie, mint a Data Box-megosztásnak. [Új Azure-fájlmegosztás létrehozása](storage-how-to-create-file-share.md). |
| ![4\. lépés](media/storage-sync-files-offline-data-transfer/bullet_4.png) | [Hozzon létre egy szinkronizálási csoportot](storage-sync-files-deployment-guide.md#create-a-sync-group-and-a-cloud-endpoint) a Storage Sync szolgáltatásban. Hivatkozzon az üres megosztásra Felhőbeli végpontként. Ismételje meg ezt a lépést minden Data Box fájlmegosztás esetén. [Azure file Sync beállítása](storage-sync-files-deployment-guide.md). |
| ![5\. lépés](media/storage-sync-files-offline-data-transfer/bullet_5.png) | [Adja hozzá az élő kiszolgáló címtárát kiszolgálói végpontként](storage-sync-files-deployment-guide.md#create-a-server-endpoint). A folyamat során állítsa be, hogy áthelyezte a fájlokat az Azure-ba, és hivatkozzon az átmeneti megosztásokra. Igény szerint engedélyezheti vagy letilthatja a Felhőbeli rétegek elvégzését. Egy kiszolgálói végpontnak az élő kiszolgálón való létrehozásakor hivatkozzon az átmeneti megosztásra. A **kiszolgáló-végpont hozzáadása** panelen, az **Offline adatátvitel**területen válassza az **engedélyezve**lehetőséget, majd válassza ki azt az átmeneti megosztást, amelynek a Felhőbeli végpontjának ugyanabban a Storage-fiókban kell lennie. Itt az elérhető megosztások listáját a Storage-fiók és a már nem szinkronizált megosztások alapján szűri a rendszer. |

![Képernyőfelvétel a Azure Portal felhasználói felületéről, amely bemutatja, hogyan engedélyezhető az offline adatátvitel az új kiszolgálói végpont létrehozásakor](media/storage-sync-files-offline-data-transfer/data-box-integration-2-600.png)

## <a name="syncing-the-share"></a>A megosztás szinkronizálása
Miután létrehozta a kiszolgálói végpontot, a rendszer elindítja a szinkronizálást. A szinkronizálási folyamat meghatározza, hogy a kiszolgálón lévő összes fájl megtalálható-e az átmeneti megosztásban, ahol a Data Box a fájlokat letétbe helyezte. Ha a fájl létezik, a szinkronizálási folyamat átmásolja a fájlt az átmeneti megosztásból ahelyett, hogy feltölti azt a-kiszolgálóról. Ha a fájl nem létezik az átmeneti megosztásban, vagy ha újabb verzió érhető el a helyi kiszolgálón, a szinkronizálási folyamat feltölti a fájlt a helyi kiszolgálóról.

> [!IMPORTANT]
> Csak akkor engedélyezheti a tömeges áttelepítési módot, ha kiszolgálói végpontot hoz létre. Miután létrehozta a kiszolgálói végpontot, nem tudja integrálni a nagyméretű áttelepített adatok egy már szinkronizált kiszolgálóról a névtérbe.

## <a name="acls-and-timestamps-on-files-and-folders"></a>Fájlok és mappák ACL-jei és időbélyegei
Azure File Sync biztosítja, hogy a fájl-és mappa ACL-ek szinkronizálva legyenek az élő kiszolgálóról, még akkor is, ha a használt tömeges áttelepítési eszköz nem használta először a ACL-eket. Emiatt az átmeneti megosztásnak nem kell tartalmaznia a fájlokhoz és mappákhoz tartozó ACL-eket. Amikor engedélyezi az offline adatáttelepítés funkciót új kiszolgálói végpont létrehozásakor, az összes fájl ACL-je szinkronizálva lesz a kiszolgálón. Az újonnan létrehozott és módosított időbélyegek is szinkronizálva vannak.

## <a name="shape-of-the-namespace"></a>A névtér alakja
Ha engedélyezi a szinkronizálást, a kiszolgáló tartalma határozza meg a névtér alakját. Ha a Data Box pillanatkép és áttelepítés befejezése után töröl fájlokat a helyi kiszolgálóról, akkor ezek a fájlok nem kerülnek át az élő, szinkronizált névtérbe. Az átmeneti megosztásban maradnak, de nem másolhatók. Erre azért van szükség, mert a szinkronizálás az élő kiszolgáló alapján megtartja a névteret. A Data Box *Pillanatkép* csak egy átmeneti terület a hatékony fájlmásolás érdekében. Az élő névtér alakja nem a mérvadó.

## <a name="cleaning-up-after-bulk-migration"></a>Takarítás a tömeges Migrálás után 
Mivel a kiszolgáló befejezte a névtér kezdeti szinkronizálását, a Data Box tömeges áttelepített fájlok az átmeneti fájlmegosztást használják. A Azure Portal **kiszolgáló végpont tulajdonságai** paneljén, az **Offline adatátvitel** szakaszban az állapot **folyamatban** értékről **Befejezettre**változik. 

![Képernyőfelvétel a kiszolgálói végpont tulajdonságai panelről, ahol az offline adatátviteli állapot és letiltás vezérlők találhatók](media/storage-sync-files-offline-data-transfer/data-box-integration-3-444.png)

Most már törölheti az átmeneti megosztást a költségek megtakarítása érdekében:

1. A **kiszolgálói végpont tulajdonságai** panelen az állapot **befejezése után**válassza az **Offline adatátvitel letiltása**lehetőséget.
2. Vegye fontolóra az átmeneti megosztás törlését a költségek megtakarítása érdekében. Az átmeneti megosztás valószínűleg nem tartalmaz fájl-és mappa-ACL-eket, ezért nem nagyon hasznos. A biztonsági mentési időponthoz hozzon létre egy valós [pillanatképet az Azure-fájlmegosztás szinkronizálásáról](storage-snapshots-files.md). [Azure Backup beállíthatja, hogy]( ../../backup/backup-azure-files.md) a pillanatképek ütemezettek legyenek.

Tiltsa le az offline adatátviteli módot csak akkor, ha az állapot be van **töltve** , vagy ha egy hibás konfiguráció miatt meg kívánja szakítani. Ha a telepítés során letiltja a módot, a fájlok akkor is fel lesznek feltöltve a kiszolgálóról, ha az átmeneti megosztás továbbra is elérhető.

> [!IMPORTANT]
> Miután letiltotta az offline adatátviteli módot, nem engedélyezheti újra, még akkor is, ha a tömeges áttelepítésből származó átmeneti megosztás továbbra is elérhető.

## <a name="next-steps"></a>További lépések
- [Azure File Sync központi telepítésének megtervezése](storage-sync-files-planning.md)
- [Az Azure File Sync üzembe helyezése](storage-sync-files-deployment-guide.md)
