---
title: Gyakori keresési indexelő hibáinak elhárítása – Azure Search
description: Javítsa ki a hibákat, és az indexelők szolgáltatással kapcsolatos gyakori problémák az Azure Search, beleértve az adatforrás-kapcsolat, a tűzfal és a hiányzó dokumentumok.
author: mgottein
manager: cgronlun
services: search
ms.service: search
ms.devlang: na
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: magottei
ms.custom: seodec2018
ms.openlocfilehash: 1cb3260fa11354de963318a023fec912d082eae4
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2019
ms.locfileid: "67653403"
---
# <a name="troubleshooting-common-indexer-issues-in-azure-search"></a>Az Azure Search gyakori indexelő hibáinak elhárítása

Az indexelők számos olyan problémák futtathatja, ha az adatok indexelése az Azure Search szolgáltatásba. Hiba a fő kategóriák a következők:

* [Egy adatforráshoz való kapcsolódáshoz](#data-source-connection-errors)
* [A dokumentum-feldolgozás](#document-processing-errors)
* [A dokumentum Adatbetöltési egy indexbe](#index-errors)

## <a name="data-source-connection-errors"></a>Adatok forrás csatlakozási hibák

### <a name="blob-storage"></a>Blob Storage

#### <a name="storage-account-firewall"></a>Storage-fiók tűzfal

Az Azure Storage konfigurálható tűzfal biztosít. Alapértelmezés szerint a tűzfal le van tiltva, az Azure Search csatlakozhat a tárfiókhoz.

Nem érkezik a nem meghatározott hibaüzenet jelenik meg, ha a tűzfal engedélyezve van. Általában tűzfal hibák kinéznie `The remote server returned an error: (403) Forbidden`.

Ellenőrizheti, hogy a tűzfal engedélyezve van a [portál](https://docs.microsoft.com/azure/storage/common/storage-network-security#azure-portal). Az egyetlen támogatott megkerülő megoldás az, hogy a tűzfal engedélyezi a hozzáférést a letiltása ["Minden hálózatból elérhető"](https://docs.microsoft.com/azure/storage/common/storage-network-security#azure-portal).

Ha az indexelő nem rendelkezik egy csatlakoztatott indexmezők, _előfordulhat, hogy_ próbál meg [kivételként hozzáadni](https://docs.microsoft.com/azure/storage/common/storage-network-security#managing-ip-network-rules) a search szolgáltatás az IP-címeit. Azonban ez a forgatókönyv nem támogatott, és működése nem garantált.

A teljes Tartományneve pingelésével talál meg a keresési szolgáltatás IP-címét (`<your-search-service-name>.search.windows.net`).

### <a name="cosmos-db"></a>Cosmos DB

#### <a name="indexing-isnt-enabled"></a>Indexelő nincs engedélyezve

Az Azure Search rendelkezik a Cosmos DB-indexelő az implicit függ. Ha kikapcsolja az Automatikus indexelés Cosmos DB-ben, az Azure Search adja vissza egy sikeres állapot, de nem index tároló tartalmának el. Ellenőrizze a beállításokat, és kapcsolja be az indexelő vonatkozó utasításokért lásd: [kezelése az Azure Cosmos DB indexelése](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#use-the-azure-portal).

## <a name="document-processing-errors"></a>A dokumentum feldolgozási hibákkal

### <a name="unprocessable-or-unsupported-documents"></a>Ez vagy nem támogatott dokumentumok

A blob indexelőjével [dokumentumokat, amelyeket a dokumentum-formátumok explicit módon támogatottak.](search-howto-indexing-azure-blob-storage.md#supported-document-formats). Egyes esetekben a blob storage-tároló nem támogatott dokumentumok tartalmazza. Máskor lehet problémás dokumentumokat. Elkerülheti ezeket a dokumentumokat, amelyet az indexelő leállítása [konfigurációs beállítások módosítása](search-howto-indexing-azure-blob-storage.md#dealing-with-errors):

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false, "failOnUnprocessableDocument" : false } }
}
```

### <a name="missing-document-content"></a>A dokumentum tartalma hiányzik

A blob indexelőjével [megkeresi és szöveges állomány kinyerésére tárolóban lévő blobokat](search-howto-indexing-azure-blob-storage.md#how-azure-search-indexes-blobs). Szöveg kibontása kapcsolatos problémákat a következők:

* A dokumentum csak beolvasott lemezképet tartalmaz. PDF-blobok, amelyek nem szöveges tartalmak, például a beolvasott képeket (alakíthatóak), a szokásos blob indexelési folyamat nem eredményt. Ha szöveges elemek-kép tartalmához, használhat [cognitive search](cognitive-search-concept-image-scenarios.md) keresse meg és bontsa ki a szöveget.
* A blob indexelőjével csak index metaadatainak van konfigurálva. A tartalom kibontása a blob indexelőjével kell állítani [bontsa ki a tartalom és metaadatok is](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed):

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "dataToExtract" : "contentAndMetadata" } }
}
```

## <a name="index-errors"></a>Index hibák

### <a name="missing-documents"></a>Hiányzó dokumentumok

Az indexelők a dokumentumok keresése egy [adatforrás](https://docs.microsoft.com/rest/api/searchservice/create-data-source). Néha az adatforrásból kell indexelt dokumentum jelenik meg, hogy hiányzik az index. Van néhány gyakori okok miatt fordulhat elő, ezek a hibák:

* A dokumentum még nem lett indexelve. Ellenőrizze a portálon egy indexelő sikeres Futtatás esetében.
* A dokumentum frissítve lett az indexelő futtatása után. Ha az indexelő egy [ütemezés](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-schedule), csupán végül futtassa újra a, és vegye fel a dokumentumot.
* A [lekérdezés](https://docs.microsoft.com/rest/api/searchservice/create-data-source#request-body-syntax) adatban megadott adatforrás nem tartalmazza a dokumentumot. Az indexelők dokumentumok, amelyek nem részei az adatforrás nem tud indexelni.
* [Mezőleképezéseivel](https://docs.microsoft.com/rest/api/searchservice/create-indexer#fieldmappings) vagy [cognitive search](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro) megváltoztak a dokumentumot, és különböző a vártnál.
* Használja a [keresési dokumentum API](https://docs.microsoft.com/rest/api/searchservice/lookup-document) a dokumentumban található.
