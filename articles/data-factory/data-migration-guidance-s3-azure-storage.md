---
title: Az Amazon S3-ból az Azure Storage-ba való Migrálás Azure Data Factory használata | Microsoft Docs
description: Az Amazon S3-ból az Azure Storage-ba való Migrálás Azure Data Factory használata.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 8/04/2019
ms.openlocfilehash: c56e6e004fe7f63725b5f6f4b9c71f60cc7b91ed
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68829111"
---
# <a name="use-azure-data-factory-to-migrate-data-from-amazon-s3-to-azure-storage"></a>Az Amazon S3-ból az Azure Storage-ba való Migrálás Azure Data Factory használata 

A Azure Data Factory az Amazon S3-ból az Azure-ba való áttelepítéshez nagy teljesítményű, robusztus és költséghatékony mechanizmust biztosít az Azure Blob Storage vagy a Azure Data Lake Storage Gen2 számára.  Ez a cikk a következő információkat tartalmazza az adatmérnökök és a fejlesztők számára: 

> [!div class="checklist"]
> * Teljesítmény 
> * Rugalmasság másolása
> * Hálózati biztonság
> * Magas szintű megoldás-architektúra 
> * Gyakorlati tanácsok a megvalósításhoz  

## <a name="performance"></a>Teljesítmény

Az ADF olyan kiszolgáló nélküli architektúrát kínál, amely különböző szinteken teszi lehetővé a párhuzamosságot, ami lehetővé teszi a fejlesztők számára, hogy teljes mértékben kihasználják a hálózati sávszélességet, valamint a tárolási IOPS és sávszélességet, hogy maximalizálják az adatátviteli sebességet a környezet számára. 

Az ügyfelek sikeresen áttelepítették az Amazon S3-ból az Azure-ba Blob Storage több száz millió fájlból álló petabájt, és a folyamatos átviteli sebesség 2 GB/s. 

![teljesítmény](media/data-migration-guidance-s3-to-azure-storage/performance.png)

A fenti képen azt mutatjuk be, hogyan érheti el a nagy adatátviteli sebességet a különböző párhuzamossági szinteken:
 
- Egy másolási tevékenység kihasználhatja a skálázható számítási erőforrások előnyeit: Azure Integration Runtime használata esetén [akár 256 DIUs-t](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#data-integration-units) is megadhat az egyes másolási tevékenységekhez kiszolgáló nélküli módon. saját üzemeltetésű Integration Runtime használatakor manuálisan is méretezheti a gépet, vagy akár több gépre is kibővíthető ([legfeljebb 4 csomópont](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)), és egyetlen másolási tevékenység fogja particionálni az összes csomóponton beállított fájlt. 
- Egy másolási tevékenység több szál használatával olvas be és ír az adattárba. 
- Az ADF-vezérlési folyamat egyszerre több másolási tevékenységet is elindíthat, például az [egyes hurkok esetében](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity). 

## <a name="resilience"></a>Rugalmasság

Egy másolási tevékenység futtatásakor az ADF beépített újrapróbálkozási mechanizmussal rendelkezik, így képes az adattárakban vagy a mögöttes hálózaton lévő átmeneti hibák bizonyos szintjének kezelésére. 

Ha az S3-ról Blobra, illetve S3-ról ADLS Gen2re történő bináris másolást végez, az ADF automatikusan végrehajtja az ellenőrzőpontokat.  Ha egy másolási tevékenység futása sikertelen vagy időtúllépés miatt megszakadt, egy későbbi újrapróbálkozáskor (ne feledje, hogy az újrapróbálkozások száma > 1), a másolás az elejétől kezdődően folytatódik az utolsó meghibásodási ponttól. 

## <a name="network-security"></a>Hálózati biztonság 

Alapértelmezés szerint az ADF az Amazon S3-ből az Azure-ba Blob Storage vagy a HTTPS protokollon keresztüli titkosított kapcsolaton keresztül Azure Data Lake Storage Gen2.  A HTTPS adattitkosítást biztosít az átvitel során, és megakadályozza a lehallgatást és a belső támadásokat. 

Ha nem szeretné, hogy az adatátvitel nyilvános interneten keresztül történjen, magasabb szintű biztonságot érhet el, ha az AWS Direct-kapcsolat és az Azure Express Route közötti privát kapcsolaton keresztül továbbítja az adatátvitelt.  Tekintse át az alábbi megoldás-architektúrát, hogy ez hogyan érhető el. 

## <a name="solution-architecture"></a>Megoldás architektúrája

Az adatáttelepítés nyilvános interneten keresztül:

![megoldás – architektúra – nyilvános hálózat](media/data-migration-guidance-s3-to-azure-storage/solution-architecture-public-network.png)

- Ebben az architektúrában a rendszer biztonságosan továbbítja az adatátvitelt a HTTPS-en keresztül a nyilvános interneten keresztül. 
- Mind a forrás Amazon S3, mind a cél Azure Blob Storage vagy Azure Data Lake Storage Gen2 úgy vannak konfigurálva, hogy engedélyezzék az összes hálózati IP-címről érkező forgalmat.  Tekintse meg az alábbi második architektúrát, amely azt ismerteti, hogyan korlátozható a hálózati hozzáférés adott IP-tartományhoz. 
- A hálózat és a tároló sávszélességének teljes kihasználásához könnyedén méretezheti a lóerős mennyiségét, hogy a lehető legjobb átviteli sebességet kapja a környezet számára. 
- Ez az architektúra a kezdeti pillanatkép-áttelepítés és a különbözeti adatok áttelepítését is megvalósíthatja. 

Az adatáttelepítés privát kapcsolaton keresztül: 

![megoldás-architektúra-private-Network](media/data-migration-guidance-s3-to-azure-storage/solution-architecture-private-network.png)

- Ebben az architektúrában az adatáttelepítés az AWS Direct csatlakozás és az Azure Express Route közötti privát kapcsolaton keresztül történik, így az adatforgalom soha nem halad át a nyilvános interneten.  Az AWS VPC és az Azure Virtual Network használatát igényli. 
- Az architektúra eléréséhez telepítenie kell az ADF saját üzemeltetésű integrációs modulját egy Windows rendszerű virtuális GÉPRE az Azure Virtual Networkben.  A saját üzemeltetésű IR virtuális gépeket manuálisan, vagy akár több virtuális gépre is kibővítheti (legfeljebb 4 csomópont), így teljes mértékben kihasználhatja a hálózat és a tárhely IOPS/sávszélességét. 
- Ha a HTTPS-kapcsolaton keresztüli adatátvitelre elfogadható, de a forrás S3 hálózati hozzáférését egy adott IP-tartományhoz kívánja zárolni, akkor az AWS VPC eltávolításával és a privát hivatkozás HTTPS-vel való cseréjével elfogadhatja az architektúra egy változatát.  Szeretné megőrizni az Azure virtuális és saját üzemeltetésű integrációs modulját az Azure-beli virtuális GÉPEN, így statikus, nyilvánosan irányítható IP-címmel is rendelkezhet az engedélyezési célra. 
- Ez az architektúra a kezdeti pillanatkép-adatáttelepítés és a Delta-adatáttelepítés során is elérhető. 

## <a name="implementation-best-practices"></a>Gyakorlati tanácsok a megvalósításhoz 

### <a name="authentication-and-credential-management"></a>Hitelesítés és hitelesítőadat-kezelés 

- Ha az Amazon S3-fiókkal szeretne hitelesítést végezni, [hozzáférési kulcsot](https://docs.microsoft.com/azure/data-factory/connector-amazon-simple-storage-service#linked-service-properties)kell használnia a iam-fiókhoz. 
- Több hitelesítési típus is támogatott az Azure Blob Storagehoz való kapcsolódáshoz.  A [felügyelt identitások Azure-erőforrásokhoz való](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity) használata erősen ajánlott: az Azure ad-ben automatikusan felügyelt ADF azonosítására épülő megoldás lehetővé teszi a folyamatok konfigurálását anélkül, hogy hitelesítő adatokat kellene megadnia a társított szolgáltatás definíciójában.  Azt is megteheti, hogy az Azure-Blob Storage az [egyszerű szolgáltatásnév](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication), a [közös hozzáférési aláírás](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)vagy a [Storage-fiók kulcsa](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication)segítségével hitelesíti. 
- Több hitelesítési típus is támogatott a Azure Data Lake Storage Gen2hoz való kapcsolódáshoz.  Az [Azure-erőforrásokhoz felügyelt identitások](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity) használata kifejezetten ajánlott, bár az [egyszerű szolgáltatásnév](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication) vagy a [Storage-fiók kulcsa](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication) is használható. 
- Ha nem használ felügyelt identitásokat az Azure-erőforrásokhoz, [a hitelesítő adatok tárolása Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) erősen ajánlott, hogy könnyebb legyen a kulcsok központi kezelése és elforgatása az ADF-hez társított szolgáltatások módosítása nélkül.  Ez a [CI/CD-vel kapcsolatos ajánlott eljárások](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd)egyike is. 

### <a name="initial-snapshot-data-migration"></a>Kezdeti pillanatkép-adatok áttelepítése 

Az adatpartíció különösen akkor ajánlott, ha 10 TB-nál több adat áttelepítését végzi.  Az adatok particionálásához használja az "előtag" beállítást a mappák és fájlok szűréséhez az Amazon S3-ban név szerint, majd az egyes ADF-másolási feladatok egyszerre csak egy partíciót másolhatnak.  Több ADF-másolási feladatot is futtathat párhuzamosan a jobb teljesítmény érdekében. 

Ha a másolási feladatok egyike a hálózat vagy az adattár átmeneti hibája miatt meghiúsul, újra lefuttathatja a sikertelen másolási feladatot, hogy az adott partíciót újra újra lehessen tölteni az AWS S3-ból.  Az egyéb partíciókat betöltő többi másolási feladatot nem érinti a rendszer. 

### <a name="delta-data-migration"></a>Különbözeti adatáttelepítés 

Az AWS S3-ból származó új vagy módosított fájlok azonosításának legtöbbje az idő-particionált elnevezési konvenció használatával történik. Ha az AWS S3-ban lévő adatok időszelet-információval vannak particionálva a fájlban vagy a mappában (például/yyyy/MM/DD/file.csv), akkor a folyamat könnyen azonosítható, hogy mely fájlok/mappák legyenek növekményes másolásra. 

Azt is megteheti, hogy ha az AWS S3-ban lévő adatai particionálva vannak, az ADF képes azonosítani az új vagy módosított fájlokat a LastModifiedDate.   Úgy működik, hogy az ADF megvizsgálja az AWS S3 összes fájlját, és csak azt az új és frissített fájlt másolja, amelynek utolsó módosításának időbélyeg-értéke nagyobb, mint egy bizonyos érték.  Vegye figyelembe, hogy ha az S3-ban nagy számú fájl található, akkor a kezdeti fájl vizsgálata hosszú időt is igénybe vehet, függetlenül attól, hogy hány fájl felel meg a szűrési feltételnek.  Ebben az esetben azt javasoljuk, hogy először particionálja az adatokhoz ugyanazt az "előtag" beállítást használja a pillanatkép-áttelepítés kezdeti áttelepítéséhez, hogy a fájl vizsgálata párhuzamosan történjen.  

### <a name="for-scenarios-that-require-self-hosted-integration-runtime-on-azure-vm"></a>A saját üzemeltetésű integrációs modult igénylő forgatókönyvek az Azure-beli virtuális gépen 

Akár privát kapcsolaton keresztül végzi az adatáttelepítést, akár adott IP-címtartományt szeretne engedélyezni az Amazon S3 tűzfalon, telepítenie kell a saját üzemeltetésű integrációs modult az Azure Windows rendszerű virtuális gépen. 

- Az egyes Azure-beli virtuális gépekhez való kezdéshez javasolt konfiguráció a 32 vCPU és a 128-GB memóriával Standard_D32s_v3.  Az adatáttelepítés során továbbra is megtarthatja a processzor és a memória kihasználtságának figyelését, és megtekintheti, hogy a virtuális gép nagyobb teljesítményhez vagy a virtuális gép méretezéséhez szükséges-e a költség megtakarítás érdekében. 
- Akár 4 virtuálisgép-csomópont társításával is felskálázást hajthat végre, egyetlen saját üzemeltetésű IR-vel.  A saját üzemeltetésű integrációs modulon futó egyetlen másolási feladatot a rendszer automatikusan particionálja, és minden virtuálisgép-csomóponton kihasználja a fájlok párhuzamos másolását.  A magas rendelkezésre állás érdekében ajánlott 2 virtuálisgép-csomóponttal kezdeni, hogy elkerülje az adatáttelepítés során előforduló meghibásodási pontokat. 

### <a name="rate-limiting"></a>Díjszabás korlátozása 

Ajánlott eljárásként a teljesítmény POC-t egy reprezentatív minta adatkészlettel kell elvégeznie, hogy meg tudja határozni a partíciók megfelelő méretét. 

Kezdje egyetlen partícióval és egyetlen másolási tevékenységgel az alapértelmezett DIU beállítással.  Fokozatosan növelje a DIU beállítást, amíg el nem éri az adattárolók hálózati vagy IOPS vagy sávszélességi korlátját, vagy elérte a maximális 256 DIU egyetlen másolási tevékenységnél. 

Ezután fokozatosan növelje az egyidejű másolási tevékenységek számát, amíg el nem éri a környezet korlátait. 

Ha az ADF másolási tevékenység által jelentett szabályozási hibákba ütközik, csökkentse a párhuzamosságot vagy az DIU-beállítást az ADF-ben, vagy növelje a hálózat és az adattárak sávszélesség-/IOPS-korlátait.  

### <a name="estimating-price"></a>Az ár becslése 

> [!NOTE]
> Ez egy feltételezett árképzési példa.  A tényleges díjszabás a környezet tényleges átviteli sebességével függ.

Vegye figyelembe a következő, az S3-ból az Blob Storage Azure-ba való Migrálás során felépített folyamatot: 

![díjszabás – folyamat](media/data-migration-guidance-s3-to-azure-storage/pricing-pipeline.png)

Tegyük fel a következőket: 

- Az adatmennyiség összesen 2 PB 
- Adatáttelepítés HTTPS protokollon keresztül az első megoldási architektúrával 
- 2 a PB 1 K partícióra van osztva, és mindegyik másolat egy partíciót helyez át 
- Minden másolat DIU = 256 értékkel van konfigurálva, és 1 GB/s átviteli sebességet érhet el. 
- A ForEach Egyidejűség értéke 2, az összesített átviteli sebesség pedig 2 GB/s 
- Összesen 292 órát vesz igénybe az áttelepítés befejezése 

A fenti feltételezések alapján a becsült ár a következő: 

![díjszabás – tábla](media/data-migration-guidance-s3-to-azure-storage/pricing-table.png)


### <a name="additional-references"></a>További referenciák 
- [Amazon Simple Storage szolgáltatás-összekötő](https://docs.microsoft.com/azure/data-factory/connector-amazon-simple-storage-service)
- [Az Azure Blob Storage-összekötő](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Azure Data Lake Storage Gen2-összekötő](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [Másolási tevékenység teljesítményének hangolási útmutatója](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [Saját üzemeltetésű Integration Runtime létrehozása és konfigurálása](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [Saját üzemeltetésű integrációs modul, HA és méretezhetőség](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [Az adatáthelyezés biztonsági szempontjai](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [Hitelesítő adatok tárolása Azure Key Vaultban](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [A fájl növekményes másolása az idő particionált fájljának neve alapján](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-partitioned-file-name-copy-data-tool)
- [Új és módosított fájlok másolása a LastModifiedDate alapján](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-lastmodified-copy-data-tool)
- [Az ADF díjszabási lapja](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>További lépések

- [Több tárolóból származó fájlok másolása Azure Data Factory](solution-template-copy-files-multiple-containers.md)