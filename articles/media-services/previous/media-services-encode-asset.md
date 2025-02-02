---
title: Az Azure igény szerinti adathordozó-kódolóinak áttekintése | Microsoft Docs
description: Ez a témakör áttekintést nyújt az Azure igény szerinti adathordozó-kódolóról.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: e6bfc068-fa46-4d68-b1ce-9092c8f3a3c9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/25/2019
ms.author: juliako
ms.openlocfilehash: 7e8c49815ed5e9294739a840dd0314d1c8c6c174
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "69015816"
---
# <a name="overview-of-azure-on-demand-media-encoders"></a>Az Azure igény szerinti adathordozó-kódolóinak áttekintése 

## <a name="encoding-overview"></a>Kódolás áttekintése

> [!NOTE]
> A Media Services v2 nem fog bővülni újabb funkciókkal és szolgáltatásokkal. <br/>Próbálja ki a legújabb verziót, ami a [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Lásd még: [az áttelepítési útmutató v2-től v3-ig](../latest/migrate-from-v2-to-v3.md)

Azure Media Services több lehetőséget kínál a felhőben lévő adathordozók kódolására.

A Media Services indításakor fontos megérteni a kodekek és a fájlformátumok közötti különbséget.
A kodekek a tömörítési/kibontási algoritmust megvalósító szoftverek, míg a fájlformátumok olyan tárolók, amelyek a tömörített videót tárolják.

A Media Services dinamikus csomagolást biztosít, amely lehetővé teszi az adaptív sávszélességű MP4 vagy Smooth Streaming kódolású tartalom továbbítását a Media Services által támogatott folyamatos átviteli formátumokban (MPEG DASH, HLS, Smooth Streaming) anélkül, hogy újra kellene csomagolnia ezeket folyamatos átviteli formátumok.

Az AMS-fiók létrehozásakor a rendszer hozzáad egy **alapértelmezett** streamvégpontot a fiókhoz **Leállítva** állapotban. A tartalom streamelésének megkezdéséhez, valamint a dinamikus csomagolás és a dinamikus titkosítás kihasználásához a tartalomstreameléshez használt streamvégpontnak **Fut** állapotban kell lennie.

> [!Note]
> A folyamatos átviteli végpontok számlázása akkor történik meg, amikor a végpont **futó** állapotban van.

Media Services a következő, igény szerinti kódolókat támogatja, amelyek a jelen cikkben olvashatók:

* [Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)
* [Media Encoder Premium-munkafolyamat](media-services-encode-asset.md#media-encoder-premium-workflow)

Ez a cikk rövid áttekintést nyújt az igény szerinti adathordozó-kódolókkal kapcsolatban, és hivatkozásokat tartalmaz a részletesebb információkat tartalmazó cikkekhez. A témakör emellett a kódolók összehasonlítását is tartalmazza.

Alapértelmezés szerint minden Media Services fiók egyszerre csak egy aktív kódolási feladattal rendelkezhet. Olyan kódolási egységeket foglalhat le, amelyek lehetővé teszik, hogy egyszerre több kódolási feladatot futtasson, egyet a megvásárolt kódoláshoz fenntartott egységekhez. További információ: [kódolási egységek skálázása](media-services-scale-media-processing-overview.md).

## <a name="media-encoder-standard"></a>Media Encoder Standard
### <a name="how-to-use"></a>A használat módja
[Kódolás a Media Encoder Standard használatával](media-services-dotnet-encode-with-media-encoder-standard.md)

### <a name="formats"></a>Formátumok
[Formátumok és kodekek](media-services-media-encoder-standard-formats.md)

### <a name="presets"></a>Előzetes beállítások
Media Encoder Standard az [itt](https://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409)ismertetett kódoló-beállításkészletek egyikével van konfigurálva.

### <a name="input-and-output-metadata"></a>Bemeneti és kimeneti metaadatok
A kódolók bemeneti metaadatait [itt](media-services-input-metadata-schema.md)mutatjuk be.

A kódoló kimeneti metaadatait [itt](media-services-output-metadata-schema.md)mutatjuk be.

### <a name="generate-thumbnails"></a>Miniatűrök előállítása
További információ: [miniatűrök készítése a Media Encoder standard használatával](media-services-advanced-encoding-with-mes.md#thumbnails).

### <a name="trim-videos-clipping"></a>Videók körülvágása (vágás)
További információ: [videók körülvágása Media Encoder standard használatával](media-services-advanced-encoding-with-mes.md#trim_video).

### <a name="create-overlays"></a>Átfedések létrehozása
További információ: [átfedések létrehozása Media Encoder standard használatával](media-services-advanced-encoding-with-mes.md#overlay).

### <a name="see-also"></a>Lásd még
[A Media Services blog](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)

## <a name="media-encoder-premium-workflow"></a>Media Encoder Premium-munkafolyamat
### <a name="overview"></a>Áttekintés
[Prémium szintű kódolás bemutatása Azure Media Services](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/)

### <a name="how-to-use"></a>A használat módja
A Media Encoder Premium Workflow összetett munkafolyamatok használatával van konfigurálva. A munkafolyamat-fájlok a [munkafolyamat-tervező](media-services-workflow-designer.md) eszköz használatával hozhatók létre és frissíthetők.

[Prémium szintű kódolás használata a Azure Media Servicesban](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services/)

### <a name="known-issues"></a>Ismert problémák
Ha a bemeneti videó nem tartalmaz kódolt feliratozást, a kimeneti eszköz továbbra is tartalmaz egy üres TTML-fájlt.

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Kapcsolódó cikkek
* [Speciális kódolási feladatok végrehajtása a Media Encoder Standard-készletek testreszabásával](media-services-custom-mes-presets-with-dotnet.md)
* [Kvóták és korlátozások](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/
