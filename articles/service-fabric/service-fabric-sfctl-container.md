---
title: Azure Service Fabric CLI – sfctl tároló | Microsoft Docs
description: Ismerteti a Service Fabric CLI sfctl-tároló parancsait.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: 0ce6cf7c627657cf757b0c1ef9aa22428c17a7e7
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036467"
---
# <a name="sfctl-container"></a>sfctl-tároló
Tárolóval kapcsolatos parancsok futtatása fürtcsomóponton.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| invoke-api | A Container API meghívása egy Service Fabric csomóponton telepített tárolón az adott programkódhoz. |
| logs | Beolvassa a tároló naplóit a Service Fabric csomóponton üzembe helyezett tárolóhoz az adott programkódhoz. |

## <a name="sfctl-container-invoke-api"></a>sfctl-tároló meghívása – API
A Container API meghívása egy Service Fabric csomóponton telepített tárolón az adott programkódhoz.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --Application-ID [kötelező] | Az alkalmazás identitása. <br><br> Ez általában az alkalmazás teljes neve a "Fabric\:" URI-séma nélkül. Az 6,0-es verziótól kezdődően a hierarchikus nevek a "\~" karakterrel vannak tagolva. Ha például az alkalmazás neve "Fabric\:/MyApp/App1", az alkalmazás identitása "SajátPr\~App1" lesz a korábbi verziók 6.0 + és "SajátPr/App1" értékében. |
| --Code-Package-instance-ID [kötelező] | Olyan azonosító, amely egyedileg azonosítja a Service Fabric-csomóponton üzembe helyezett Code Package-példányt. <br><br> A "Service Code-Package-List" lekérdezéssel kérhető le. |
| --Code-Package-Name [kötelező] | A szolgáltatás jegyzékfájljában megadott, Service Fabric fürtben egy alkalmazás típusának részeként regisztrált kódlap neve. |
| --Container-API-URI-elérési_út [kötelező] | Tároló REST API URI elérési útja, használja a (z) {ID} nevet a tároló neve/azonosítója helyett. |
| --Node-Name [kötelező] | A csomópont neve. |
| --Service-manifest-Name [kötelező] | Egy Service Fabric-fürtben egy alkalmazás típusának részeként regisztrált szolgáltatási jegyzékfájl neve. |
| --container-api-body | A tároló REST API HTTP-kérelem törzse. |
| --container-api-content-type | A tároló REST API tartalomtípusa alapértelmezett értéke az "Application/JSON". |
| --container-api-http-verb | A tároló REST API HTTP-művelete alapértelmezett értéke a beolvasás. |
| --időtúllépés-t | Kiszolgáló időtúllépése másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek\: : JSON, jsonc, Table, TSV.  Alapértelmezett\: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca További információkat\:és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-container-logs"></a>sfctl-tároló naplói
Beolvassa a tároló naplóit a Service Fabric csomóponton üzembe helyezett tárolóhoz az adott programkódhoz.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --Application-ID [kötelező] | Az alkalmazás identitása. <br><br> Ez általában az alkalmazás teljes neve a "Fabric\:" URI-séma nélkül. Az 6,0-es verziótól kezdődően a hierarchikus nevek a "\~" karakterrel vannak tagolva. Ha például az alkalmazás neve "Fabric\:/MyApp/App1", az alkalmazás identitása "SajátPr\~App1" lesz a korábbi verziók 6.0 + és "SajátPr/App1" értékében. |
| --Code-Package-instance-ID [kötelező] | Kód csomag példányának azonosítója, amelyet a "Service Code-Package-List" paranccsal kérhet le. |
| --Code-Package-Name [kötelező] | A szolgáltatás jegyzékfájljában megadott, Service Fabric fürtben egy alkalmazás típusának részeként regisztrált kódlap neve. |
| --Node-Name [kötelező] | A csomópont neve. |
| --Service-manifest-Name [kötelező] | Egy Service Fabric-fürtben egy alkalmazás típusának részeként regisztrált szolgáltatási jegyzékfájl neve. |
| – farok | A naplók végétől megjelenítendő sorok száma. Az alapértelmezett érték a 100. az "all" (összes) a teljes naplókat jeleníti meg. |
| --időtúllépés-t | Kiszolgáló időtúllépése másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek\: : JSON, jsonc, Table, TSV.  Alapértelmezett\: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca További információkat\:és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |


## <a name="next-steps"></a>További lépések
- [Állítsa be](service-fabric-cli.md) a Service Fabric CLI-t.
- Megtudhatja, hogyan használhatja a Service Fabric CLI-t a [minta-parancsfájlok](/azure/service-fabric/scripts/sfctl-upgrade-application)használatával.