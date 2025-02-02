---
title: Tartalom-kulcs házirendjei, a Media Services – Azure |} A Microsoft Docs
description: Ez a cikk annak magyarázatát, Mik azok a Tartalomszabályzat kulcsot, és hogyan használják az Azure Media Services biztosítja.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/26/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 8809bf25c3bcfb26fb0ad251a2b09dfdca2a3e04
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68679190"
---
# <a name="content-key-policies"></a>Tartalomkulcsszabályok

A Media Services használatával dinamikusan titkosíthatja az élő és igény szerinti tartalmat Advanced Encryption Standard (AES-128) vagy a három jelentős digitális jogkezelési (DRM) rendszerből: Microsoft PlayReady, Google Widevine és Apple FairPlay. Media Services is biztosít a modult az AES-kulcsok és a DRM (PlayReady, Widevine és FairPlay) licenceket az arra jogosult ügyfelek. 

Az adatfolyam titkosítási beállításainak megadásához létre kell hoznia egy [adatfolyam](streaming-policy-concept.md) -szabályzatot, és hozzá kell rendelnie a [folyamatos átviteli lokátorhoz](streaming-locators-concept.md). Hozza létre a [tartalmi kulcs házirendjét](https://docs.microsoft.com/rest/api/media/contentkeypolicies) annak konfigurálásához, hogy a tartalom kulcsa (amely biztonságos hozzáférést [](assets-concept.md)biztosít az eszközeihez) a végfelhasználók számára legyen továbbítva. Meg kell adnia a tartalmi kulcs házirendjének követelményeit (korlátozásait), amelyeknek teljesülniük kell ahhoz, hogy a megadott konfigurációval rendelkező kulcsok eljussanak az ügyfeleknek. A tartalmi kulcsra vonatkozó házirend nem szükséges a folyamatos átvitelhez vagy a letöltéshez. 

A tartalmi kulcs házirendjét általában a [folyamatos átviteli lokátorhoz](streaming-locators-concept.md)társítja. Azt is megteheti, hogy a tartalmi kulcsra vonatkozó házirendet egy [folyamatos átviteli házirenden](streaming-policy-concept.md) belül is megadhatja (ha speciális forgatókönyvekhez egyéni adatfolyam-szabályzatot hoz létre) 

> [!NOTE]
> A `Datetime` típusú tartalmi kulcsokra vonatkozó házirendek tulajdonságai mindig UTC formátumban jelennek meg.

## <a name="best-practices-and-considerations"></a>Ajánlott eljárások és szempontok

> [!IMPORTANT]
> Kérjük, tekintse át a következő javaslatokat.

* A Media Service-fiókhoz korlátozott számú szabályzatot kell terveznie, és újra fel kell használni őket a streaming-lokátorok számára, amikor ugyanazok a beállítások szükségesek. További információ: kvóták [és korlátozások](limits-quotas-constraints.md).
* A tartalmi kulcs házirendjei frissíthető. Akár 15 percet is igénybe vehet, amíg a kulcsok kézbesítési gyorsítótárai frissülnek, és felveszik a frissített szabályzatot. 

   A szabályzat frissítésével felülírja a meglévő CDN-gyorsítótárat, ami lejátszási problémát okozhat a gyorsítótárazott tartalmat használó ügyfelek számára.  
* Azt javasoljuk, hogy az egyes eszközökhöz ne hozzon létre új tartalmi kulcsokra vonatkozó házirendet. A legfontosabb előnye, hogy ugyanazokat a tartalmi kulcsokra vonatkozó szabályzatokat osztják meg egymás között, amelyek ugyanazokat a házirend-beállításokat igényelik:
   
   * Könnyebben kezelhető kis mennyiségű szabályzat.
   * Ha frissítenie kell a tartalmi kulcsra vonatkozó házirendet, a módosítások szinte azonnal érvénybe lépnek az összes új licencelési kérelemben.
* Ha új szabályzatot kell létrehoznia, létre kell hoznia egy új adatfolyam-keresőt az eszközhöz.
* Javasoljuk, hogy Media Services automatikusan létrehozza a tartalmi kulcsot. 

   Általában hosszú élettartamú kulcsot kell használnia, és ellenőriznie kell, hogy létezik-e a tartalmi kulcs házirendje a [Get](https://docs.microsoft.com/rest/api/media/contentkeypolicies/get)paranccsal. A kulcs lekéréséhez külön műveleti módszert kell meghívnia a titkok és a hitelesítő adatok beszerzéséhez. lásd az alábbi példát.

## <a name="example"></a>Példa

A kulcs eléréséhez használja `GetPolicyPropertiesWithSecretsAsync`a alkalmazást, ahogy az az [aláíró kulcs beolvasása a meglévő házirendből](get-content-key-policy-dotnet-howto.md#get-contentkeypolicy-with-secrets) című példa mutatja.

## <a name="filtering-ordering-paging"></a>Szűrési, rendezési, stránkování

Lásd: [Media Services entitások szűrése, rendezése](entities-overview.md)és lapozása.

## <a name="next-steps"></a>További lépések

* [AES-128, a dinamikus titkosítás és a kulcstovábbítást használata](protect-with-aes128.md)
* [DRM a dinamikus titkosítás és a licenc kézbesítési szolgáltatás használata](protect-with-drm.md)
* [EncodeHTTPAndPublishAESEncrypted](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/EncodeHTTPAndPublishAESEncrypted)
