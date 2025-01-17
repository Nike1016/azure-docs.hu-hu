---
title: A Azure App Service biztonsági javaslatai
description: Biztonsági javaslatok a Azure App Service. A javaslatok megvalósítása a közös felelősségi modellben leírtak szerint biztosítja a biztonsági kötelezettségek teljesítését, és javítja a webalkalmazás-megoldások általános biztonságát.
services: app-service
author: barclayn
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: barclayn
ms.custom: security-recommendations
ms.openlocfilehash: 71d564dac43328371e9d34684f2d13a26616a99d
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68609775"
---
# <a name="security-recommendations-for-app-service"></a>A App Service biztonsági javaslatai

Ez a cikk a Azure App Service biztonsági javaslatait tartalmazza. A javaslatok megvalósítása a közös felelősségi modellben leírtak szerint biztosítja a biztonsági kötelezettségek teljesítését, és javítja a webalkalmazás-megoldások általános biztonságát. További információk arról, hogy a Microsoft miként teljesíti a szolgáltatói feladatokat, olvassa el az [Azure-infrastruktúra biztonsága](../security/fundamentals/infrastructure.md)című témakört.

## <a name="general"></a>Általános

| Ajánlás | Megjegyzések |
|-|-|----|
| Naprakész marad | A támogatott platformok, programozási nyelvek, protokollok és keretrendszerek legújabb verzióit használhatja. |

## <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

| Ajánlás | Megjegyzések |
|-|----|
| Névtelen hozzáférés letiltása | Ha nem kell támogatnia a névtelen kérelmeket, tiltsa le a névtelen hozzáférést. A Azure App Service hitelesítési lehetőségeivel kapcsolatos további információkért lásd: [hitelesítés és engedélyezés a Azure app Serviceban](overview-authentication-authorization.md).|
| Hitelesítés megkövetelése | Amikor csak lehetséges, használja a App Service hitelesítési modult, és ne írjon kódot a hitelesítés és az engedélyezés kezelésére. Lásd: [hitelesítés és engedélyezés Azure app Serviceban](overview-authentication-authorization.md). |
| A háttérbeli erőforrások és a hitelesített hozzáférések elleni védelem | Használhatja a felhasználó identitását, vagy használhat egy alkalmazás-identitást a háttérbeli erőforráshoz való hitelesítéshez. Ha úgy dönt, hogy egy alkalmazás-identitást [](overview-managed-identity.md)használ, felügyelt identitást használ.
| Ügyféltanúsítvány-alapú hitelesítés megkövetelése | Az ügyféltanúsítvány-alapú hitelesítés javítja a biztonságot azáltal, hogy csak az Ön által megadott tanúsítványokkal hitelesíthető ügyfelektől érkező kapcsolatokat engedélyez. |

## <a name="data-protection"></a>Adatvédelem

| Ajánlás | Megjegyzések |
|-|-|
| HTTP átirányítása HTTPs-re | Alapértelmezés szerint az ügyfelek HTTP-vagy HTTPS-kapcsolaton keresztül csatlakozhatnak a webalkalmazásokhoz. Javasoljuk, hogy a HTTP-t a HTTPs-re irányítsa, mert a HTTPS SSL/TLS protokollt használ a biztonságos kapcsolat biztosításához, amely titkosítva és hitelesítve van. |
| Az Azure-erőforrásokkal folytatott kommunikáció titkosítása | Ha az alkalmazás az Azure-erőforrásokhoz, például [](https://azure.microsoft.com/services/sql-database/) a SQL Databasehoz vagy az [Azure Storage](/azure/storage/)-hoz csatlakozik, a kapcsolat az Azure-ban marad. Mivel a kapcsolat az Azure megosztott hálózatán halad végig, mindig titkosítsa az összes kommunikációt. |
| A legújabb TLS-verzió megkövetelése | 2018 óta az új Azure App Service alkalmazások a TLS 1,2-et használják. A TLS újabb verziói biztonsági funkciókat is tartalmaznak a régebbi protokollos verziókhoz képest. |
| FTPS használata | A App Service az FTP-t és a FTPS is támogatja a fájlok telepítéséhez. Ha lehetséges, FTP helyett használjon FTPS. Ha az egyik vagy mindkét protokoll nincs használatban, [Tiltsa le őket](deploy-ftp.md#enforce-ftps). |
| Az alkalmazásadatok védelme | Ne tárolja az alkalmazás-titkokat, például az adatbázis hitelesítő adatait, az API-jogkivonatokat vagy a titkos kulcsokat a kódban vagy a konfigurációs fájlokban. Az általánosan elfogadott módszer az, hogy az Ön által választott nyelven a standard minta alapján [környezeti változókként](https://wikipedia.org/wiki/Environment_variable) férjen hozzájuk. A Azure App Serviceban környezeti változókat adhat meg az [Alkalmazásbeállítások](web-sites-configure.md) és a [kapcsolatok karakterláncai](web-sites-configure.md)segítségével. Az Alkalmazásbeállítások és a kapcsolatok karakterláncai titkosítva tárolódnak az Azure-ban. Az Alkalmazásbeállítások csak akkor lesznek visszafejtve, ha az alkalmazás elindul az alkalmazás folyamatának memóriájában. A titkosítási kulcsok elforgatása rendszeresen történik. Azt is megteheti, hogy a Azure App Service alkalmazást a speciális titkok kezeléséhez [Azure Key Vault](/azure/key-vault/) segítségével integrálja. Ha [a Key Vault felügyelt identitással fér hozzá](../key-vault/tutorial-web-application-keyvault.md), a app Service alkalmazás biztonságosan hozzáférhet a szükséges titkokhoz. |

## <a name="networking"></a>Hálózat

| Ajánlás | Megjegyzések |
|-|-|
| Statikus IP-korlátozások használata | A Windows Azure App Service lehetővé teszi az alkalmazás elérésére jogosult IP-címek listájának megadását. Az engedélyezett lista tartalmazhat egyedi IP-címeket vagy egy alhálózati maszk által meghatározott IP-címtartományt. További információ: [Azure app Service statikus IP-korlátozások](app-service-ip-restrictions.md).  |
| Az elkülönített díjszabási csomag használata | Az elkülönített díjszabási szinten kívül minden csomag a Azure App Service megosztott hálózati infrastruktúráján futtatja az alkalmazásokat. Az elkülönített csomag lehetővé teszi a hálózati elkülönítést az alkalmazások dedikált [app Service környezetben](environment/intro.md)való futtatásával. App Service környezet az [Azure Virtual Network](/azure/virtual-network/)saját példányában fut.|
| Biztonságos kapcsolatok használata a helyszíni erőforrások elérésekor | A helyszíni erőforrásokhoz való csatlakozáshoz [hibrid kapcsolatokat](app-service-hybrid-connections.md), [Virtual Network integrációt](web-sites-integrate-with-vnet.md)vagy [app Service környezetet](environment/intro.md) használhat. |
| A bejövő hálózati forgalomra való kitettség korlátozása | A hálózati biztonsági csoportok lehetővé teszik a hálózati hozzáférés korlátozását és a kitett végpontok számának szabályozását. További információ: [a bejövő forgalom vezérlése egy app Service Environment](environment/app-service-app-service-environment-control-inbound-traffic.md). |

## <a name="monitoring"></a>Figyelés

| Ajánlás | Megjegyzések |
|-|-|
|Standard szintű Azure Security Center használata | A [Azure Security Center](../security-center/security-center-app-services.md) natív módon van integrálva a Azure app Serviceval. Értékeléseket futtathat, és biztonsági javaslatokat is megadhat. |

## <a name="next-steps"></a>További lépések

Érdeklődjön az alkalmazás szolgáltatójánál, hogy vannak-e további biztonsági követelmények. A biztonságos alkalmazások fejlesztésével kapcsolatos további információkért lásd a [biztonságos fejlesztési dokumentációt](../security/fundamentals/abstract-develop-secure-apps.md).
