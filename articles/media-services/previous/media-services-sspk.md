---
title: Licencelési Microsoft® Smooth Streaming Ügyfélportolási készlet
description: További tudnivalók a Microsoft® Smooth Streaming Client portolása Kit licencelésre.
services: media-services
documentationcenter: ''
author: xpouyat
manager: femila
editor: ''
ms.assetid: e3b488e7-8428-4c10-a072-eb3af46c82ad
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: xpouyat
ms.openlocfilehash: 505def9cde7cddf2ddcc23408fa3159de886167a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "61472693"
---
# <a name="licensing-microsoft-smooth-streaming-client-porting-kit"></a>Licencelési Microsoft® Smooth Streaming Ügyfélportolási készlet 
## <a name="overview"></a>Áttekintés
A Microsoft Smooth Streaming Client portolása Kit (**SSPK** röviden) Smooth Streaming client megvalósítása érdekében a beágyazott eszközgyártók, kábelek és mobilszolgáltatók, tartalom szolgáltatók, kézibeszélő optimalizált gyártó, független szoftverszállítók (ISV-k) és szolgáltatást nyújtó vállalkozásoknak hozhat létre, termékeket és szolgáltatásokat a Smooth Streaming formátumban adaptív tartalmak online lejátszásához. SSPK egy eszköz és a Smooth Streaming-ügyfél, amely a hasznosító minden eszköz és platform szerint is tartalomfájlokat platformfüggetlen megvalósítását. 

Alábbi egy magas szintű architektúra és az IIS Smooth Streaming portolása Kit box Smooth Streaming Client megvalósítása a Microsoft által biztosított és Smooth Streaming-tartalom lejátszását az alapvető logikát tartalmaz. Ez a tartalom egy adott eszköz vagy platform partnerek majd ültették át a megfelelő adapterek alkalmazásával. 

![SSPK](./media/media-services-sspk/sspk-arch.png)

## <a name="description"></a>Leírás
A feltételek, amelyek kiváló üzleti értéket kínál SSPK rendelkezik licenccel. SSPK licencet biztosít az iparág:

* A C++ Smooth Streaming portolása Kit forrás 
  * megvalósítja a Smooth Streaming Client funkció
  * hozzáadja a formátum elemzése, a heurisztika pufferelés logikát, és így tovább.
* Lejátszóalkalmazás API-k 
  * az alkalmazásprogramozási felületek egy médialejátszó alkalmazásba való interakció
* Platform absztrakciós réteg (PAL) kapcsolat 
  * az alkalmazásprogramozási felületek (szálak, sockets) az operációs rendszer-szal
* Hardver absztrakciós réteg (HAL) kapcsolat 
  * alkalmazásprogramozási felület A hardver való együttműködéshez szükséges / V dekóderek (dekódolás, megjelenítése)
* Digitális jogkezelési (technológia DRM) felügyeleti felület 
  * az alkalmazásprogramozási felületek DRM révén a DRM absztrakciós réteg (DAL) kezelése
  * A Microsoft PlayReady portolása Kit mobilplatform külön-külön, de ezen a kapcsolaton keresztül integrálható. A Microsoft PlayReady Device licenc további részletekért kattintson [Itt](https://www.microsoft.com/playready/licensing/device_technology.mspx#pddipdl).
* Megvalósítás minták 
  * a mintavétel PAL végrehajtása linuxhoz
  * hardverabsztrakciós RÉTEG minta megvalósítása GStreamer

## <a name="licensing-options"></a>Licencelési lehetőségek
A Microsoft Smooth Streaming Client portolása Kit tett elérhetővé licenctulajdonosok esetében két különböző szoftverlicenc-szerződések alatt: egy a Smooth Streaming Client ideiglenes termékek és a egy másik a Smooth Streaming Client végső termékek terjesztése a végfelhasználók számára.

* Lapkakészlet gyártók, rendszerintegrátorok vagy független-beszállítót (ISV-k) szükséges egy forrás ideiglenes termékek, egy Microsoft Smooth Streaming Client portolása Kit fejlesztésének portolási kit code **ideiglenes terméklicenc** kell végrehajtani.
* Eszközgyártók vagy Smooth Streaming Client végső termékek terjesztési jogokkal a végfelhasználók számára, a Microsoft Smooth Streaming Client portolása Kit szükséges ISV-k **végső terméklicenc** kell végrehajtani.

### <a name="microsoft-smooth-streaming-client-porting-kit-interim-product-license"></a>A Microsoft Smooth Streaming Ügyfélportolási készlet ideiglenes terméklicenc-hozzárendelésről
A jelen licenc, a Microsoft kínál egy Smooth Streaming Client portolása Kit és a szükséges szellemi tulajdonra vonatkozó jogokat elkészíthetnék és Smooth Streaming Client ideiglenes termékek az egyéb Smooth Streaming Client portolása Kit eszköz licenctulajdonosok esetében, amelyek Smooth Streaming Client végső termékek terjesztése.

#### <a name="fee-structure"></a>Díjszabása
Egyesült Államok $ 50 000 egyszeri licencdíját hozzáférést biztosít a Smooth Streaming Client portolása csomaghoz. 

### <a name="microsoft-smooth-streaming-client-porting-kit-final-product-license"></a>A Microsoft Smooth Streaming Ügyfélportolási készlet utolsó terméklicenc
A jelen licenc Microsoft Smooth Streaming Client ideiglenes termékek fogadjon más Smooth Streaming Client portolása Kit licenctulajdonosok esetében, és a vállalati márkanevű Smooth Streaming Client végső terjeszteni az összes szükséges szellemi tulajdonjogok kínál Termékek, a végfelhasználók számára.

#### <a name="fee-structure"></a>Díjszabása
A Smooth Streaming Client végtermékben alatt, a kiemelt modellben érhető el:

* tartalmazza a szükséges $ 0.10-ás kiszolgálónként megvalósítás az eszközökön
* A kiemelt maximumon $ 50 000 minden évben
* Nincsenek kiemelt az első 10 000 eszköz megvalósításokhoz minden évben 

## <a name="licensing-procedure-and-sspk-access"></a>Engedélyezési eljárás és SSPK hozzáférés
E-mailek [ sspkinfo@microsoft.com ](mailto:sspkinfo@microsoft.com) összes licencelése a lekérdezéseket.

A [SSPK terjesztési portál](https://microsoft.sharepoint.com/teams/SSPKDOWNLOAD/) regisztrált ideiglenes licenctulajdonosok esetében érhető el.

Ideiglenes és végső SSPK licenctulajdonosok esetében technikai kérdéseket küldhet [ smoothpk@microsoft.com ](mailto:smoothpk@microsoft.com).

## <a name="microsoft-smooth-streaming-client-interim-product-agreement-licensees"></a>A Microsoft Smooth Streaming Client ideiglenes termék szerződés licenctulajdonosok esetében
* Adroit üzleti megoldások, Inc
* Fejlett digitális adás SA
* AirTies Kablosuz Iletism Sanayive Dis Ticaret A.S.
* Albis technológiák Kft.
* Alticast Corporation
* Amazon Digital Services, Inc.
* Arion technológia, Inc.
* AVC Multimedia Software Co., Ltd.
* Cavium, Inc.
* EchoStar megvásárlásával Corporation
* Enseo, Inc.
* Fluendo S.A.
* HANDAN BroadInfoCom Co., Ltd.
* Infomir GMBH
* Irdeto USA Inc.
* iWEDIA S.A. 
* Liberty globális szolgáltatások BV
* MediaTek Inc.
* MStar Co, Ltd
* Nintendo Co., Ltd.
* OpenTV, Inc.
* Sáfrány digitális korlátozott
* Sichuan Changhong Electric Co., Ltd
* SoftAtHome
* Sony Corporation
* Tatung technológia Inc.
* TCL technológia (Huizhou) Electronics Co., Ltd.
* Felső győzelem beruházások, Ltd.
* Vestel Elektronik Sanayi ve Ticaret A.S.
* VisualOn, Inc.
* ZTE Corporation

## <a name="microsoft-smooth-streaming-client-final-product-agreement-licensees"></a>A Microsoft Smooth Streaming Client végtermékben szerződés licenctulajdonosok esetében
* Fejlett digitális adás SA
* AirTies Kablosuz Iletism Sanayive Dis Ticaret A.S.
* Albis technológiák Kft.
* Amazon Digital Services, Inc.
* AmTRAN Technology Co., Ltd.
* Arcadyan technológiai vállalat
* Arion technológia, Inc.
* ATMACA ELEKTRONİK SAN. VE TİC. A.Ş
* Brit Sky szórásos korlátozott
* Technológiai CastPal, Inc. Shenzhen
* Compal Electronics, Inc.
* Dongguan digitális AV technológiai vállalat esetében, Ltd.
* EchoStar megvásárlásával Corporation
* Enseo, Inc.
* FilmFlex filmek korlátozott
* Fluendo S.A.
* FUNAI ELECTRIC CO., LTD.
* Gibson Innovációkat korlátozott
* Haier információk Applicantion S.R.L
* HANDAN BroadInfoCom Co., Ltd.
* Hisense International Co., Ltd. 
* Homecast Co., Ltd
* HON Hai pontosság Industry Co., Ltd.
* Infomir GMBH
* Kaonmedia Co., Ltd.
* KDDI Corporation
* Nintendo Co., Ltd.
* Narancssárga SA
* Sáfrány digitális korlátozott
* Sagemcom Broadband SAS
* Shenzhen Coship Electronics CO., LTD
* Shenzhen Jiuzhou Electric Co., Ltd
* Shenzhen Skyworth Digital Technology Co., Ltd
* Szecsuan Changhong Electric Co., Ltd.
* Skardin ipari Corp.
* Sky Deutschland Fernsehen GmbH & Co. KG
* SmarDTV S.A.
* SoftAtHome
* Sony Corporation
* Korlátozott TCL tengerentúli Marketing (tengeri a kereskedelmi Makaó KKT)
* Technicolor szállítási technológiák, SAS
* Tongfang globális Ltd.
* Felső győzelem beruházások, Ltd.
* A Toshiba Lifestyle termékek és szolgáltatások Corporation
* Univerzális Media Corporation /Slovakia/ s.r.o.
* VIZIO, Inc.
* Wistron Corporation
* ZTE Corporation

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

