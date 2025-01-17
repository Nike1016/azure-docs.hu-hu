---
title: Több protokollos hozzáférés a Azure Data Lake Storageon | Microsoft Docs
description: Használjon olyan blob API-kat és alkalmazásokat, amelyek blob API-kat használnak Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: cc0191a9484a09ed12e0ca0cde4d51681e44ec5f
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68855542"
---
# <a name="multi-protocol-access-on-azure-data-lake-storage"></a>Több protokollos hozzáférés Azure Data Lake Storage

A blob API-k mostantól hierarchikus névtérrel rendelkező fiókokkal működnek. Ezzel feloldja az eszközök, alkalmazások és szolgáltatások teljes ökoszisztémáját, valamint a blob Storage összes funkcióját a hierarchikus névtérrel rendelkező fiókokhoz.

A legutóbbi időpontig előfordulhat, hogy külön tárolási megoldásokat kellett volna fenntartania az objektumok tárolásához és az elemzések tárolásához. Ennek az az oka, hogy Azure Data Lake Storage Gen2 korlátozott az ökoszisztéma-támogatással. Emellett korlátozott hozzáféréssel rendelkezik Blob service-funkciókhoz, például a diagnosztikai naplózáshoz. A töredezett tárolási megoldás nehezen kezelhető, mert a különböző forgatókönyvek elvégzése érdekében át kell helyeznie az adatátvitelt a fiókok között. Már nem kell ezt megtennie.

> [!NOTE]
> A többprotokollos hozzáférés Data Lake Storage nyilvános előzetes verzióban érhető el, és csak az **USA 2. nyugati** régiójában és az **USA nyugati középső** régiójában érhető el. A korlátozások áttekintéséhez tekintse meg az [ismert problémákkal foglalkozó](data-lake-storage-known-issues.md) cikket. Az előzetes verzióra való regisztráláshoz tekintse meg [ezt a lapot](https://aka.ms/blobinteropsignup).

## <a name="use-the-entire-ecosystem-of-applications-tools-and-services"></a>Az alkalmazások, eszközök és szolgáltatások teljes ökoszisztémájának használata

Ha a többprotokollos hozzáférés előnézetét regisztrálja Data Lake Storageon, az összes adatait használhatja az eszközök, alkalmazások és szolgáltatások teljes ökoszisztémájának használatával. Ide tartoznak az olyan Azure-szolgáltatások, mint a [Azure stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-introduction), az [IOT Hub](https://docs.microsoft.com/azure/iot-hub/), a [Power bi](https://docs.microsoft.com/power-bi/desktop-data-sources)és sok más. 

Ez magában foglalja a harmadik féltől származó eszközöket és alkalmazásokat is. Azokat olyan fiókokra irányíthatja, amelyek hierarchikus névtérrel rendelkeznek anélkül, hogy módosítani kellene őket. Ezek az alkalmazások *ugyanúgy működnek, mint a* blob API-k meghívásával, mivel a blob API-k mostantól hierarchikus névtérrel rendelkező fiókokban is működhetnek.

> [!NOTE]
> A korlátozások áttekintéséhez tekintse meg az [ismert problémákkal foglalkozó](data-lake-storage-known-issues.md) cikket.

## <a name="use-all-blob-storage-features"></a>A blob Storage összes funkciójának használata

A blob Storage olyan funkciói, mint a [diagnosztikai naplózás](../common/storage-analytics-logging.md), a [hozzáférési szintek](storage-blob-storage-tiers.md)és a [blob Storage életciklus-kezelési szabályzatai](storage-lifecycle-management-concepts.md) mostantól hierarchikus névtérrel rendelkező fiókokkal működnek. Ezért a blob Storage-fiókok hierarchikus névtereit a fontos funkciók elérésének elvesztése nélkül is engedélyezheti. 

> [!NOTE]
> A korlátozások áttekintéséhez tekintse meg az [ismert problémákkal foglalkozó](data-lake-storage-known-issues.md) cikket.

## <a name="how-multi-protocol-access-on-data-lake-storage-works"></a>A multi-Protocol-hozzáférés a (z) adatközpont-tárolóban

A blob API-k és a Data Lake Storage Gen2 API-k ugyanazon az adattárban működhetnek, mint a hierarchikus névtérrel rendelkező Storage-fiókok. Data Lake Storage Gen2 a blob API-kat a hierarchikus névtéren keresztül irányítja, így az első osztályú címtár-műveletek és a POSIX-kompatibilis hozzáférés-vezérlési listák (ACL-ek) előnyeit érheti el. 

![Több protokollos hozzáférés Data Lake Storage fogalmi](./media/data-lake-storage-interop/interop-concept.png) 

A blob API-t használó meglévő eszközök és alkalmazások automatikusan hozzáférhetnek ezekhez az előnyökhöz. A fejlesztőknek nem kell módosítaniuk azokat. Data Lake Storage Gen2 konzisztensen alkalmazza a címtár-és a fájl szintű ACL-eket, függetlenül attól, hogy az eszközök és az alkalmazások milyen protokollt használnak az adateléréshez.   

## <a name="next-steps"></a>További lépések

[Ismert problémák](data-lake-storage-known-issues.md)




