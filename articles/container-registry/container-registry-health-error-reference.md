---
title: Az állapot-ellenőrzési hiba referenciája – Azure Container Registry
description: Hibakódok és lehetséges megoldások olyan problémákra, amelyek a Azure Container Registry
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 07/02/2019
ms.author: danlep
ms.openlocfilehash: 3bb81f33823fff5fb47f5bbbf6bb9b798b26d8af
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840369"
---
# <a name="health-check-error-reference"></a>Állapot-ellenőrzési hiba referenciája

Az alábbi információk az az [ACR állapotfelmérés][az-acr-check-health] parancs által visszaadott hibakódokról találhatók. Minden hiba esetén a lehetséges megoldások szerepelnek a felsorolásban.

## <a name="docker_command_error"></a>DOCKER_COMMAND_ERROR

Ez a hiba azt jelenti, hogy a CLI-hez készült Docker-ügyfél nem található. Ennek eredményeképpen a következő további ellenőrzések nem futnak: a Docker-verzió megkeresése, a Docker-démon állapotának kiértékelése és a Docker pull parancs futtatása.

*Lehetséges megoldások*: A Docker-ügyfél telepítése; adja hozzá a Docker elérési útját a rendszerváltozóhoz.

## <a name="docker_daemon_error"></a>DOCKER_DAEMON_ERROR

Ez a hiba azt jelenti, hogy a Docker-démon állapota nem érhető el, vagy nem érhető el a CLI használatával. Ennek eredményeképpen a Docker-műveletek (például `docker login` és `docker pull`) nem érhetők el a CLI-n keresztül.

*Lehetséges megoldások*: Indítsa újra a Docker-démont, vagy ellenőrizze, hogy megfelelően van-e telepítve.

## <a name="docker_version_error"></a>DOCKER_VERSION_ERROR

Ez a hiba azt jelenti, hogy a CLI nem tudta futtatni `docker --version`a parancsot.

*Lehetséges megoldások*: Próbálja meg manuálisan futtatni a parancsot, győződjön meg arról, hogy rendelkezik a CLI legújabb verziójával, és vizsgálja meg a hibaüzenetet.

## <a name="docker_pull_error"></a>DOCKER_PULL_ERROR

Ez a hiba azt jelenti, hogy a CLI nem tudta lekérni a minta rendszerképét a környezetbe.

*Lehetséges megoldások*: Ellenőrizze, hogy a rendszerképek lekéréséhez szükséges összes összetevő megfelelően fut-e.

## <a name="helm_command_error"></a>HELM_COMMAND_ERROR

Ez a hiba azt jelenti, hogy a parancssori ügyfél nem található a CLI-ben, ami kizárja a többi Helm-műveletet.

*Lehetséges megoldások*: Ellenőrizze, hogy a Helm-ügyfél telepítve van-e, és hogy az elérési útja hozzá van-e adva a rendszerkörnyezeti változókhoz.

## <a name="helm_version_error"></a>HELM_VERSION_ERROR

Ez a hiba azt jelenti, hogy a CLI nem tudta meghatározni a Helm telepített verzióját. Ez akkor fordulhat elő, ha az Azure CLI verziója (vagy a Helm-verzió) elavult.

*Lehetséges megoldások*: Frissítsen az Azure CLI legújabb verziójára vagy az ajánlott Helm-verzióra; futtassa manuálisan a parancsot, és vizsgálja meg a hibaüzenetet.

## <a name="connectivity_dns_error"></a>CONNECTIVITY_DNS_ERROR

Ez a hiba azt jelenti, hogy a megadott beállításjegyzékbeli bejelentkezési kiszolgáló DNS-je pingelésre került, de nem válaszolt, ami azt jelenti, hogy nem érhető el. Ez bizonyos kapcsolódási problémákra utalhat. Előfordulhat, hogy a beállításjegyzék nem létezik, előfordulhat, hogy a felhasználó nem rendelkezik a beállításjegyzék engedélyeivel (a bejelentkezési kiszolgáló megfelelő beolvasásához), vagy a cél beállításjegyzéke az Azure CLI-ben használttól eltérő felhőben található.

*Lehetséges megoldások*: Kapcsolat ellenőrzése; Ellenőrizze a beállításjegyzék helyesírását, és hogy létezik-e a beállításjegyzék. Ellenőrizze, hogy a felhasználó rendelkezik-e a megfelelő engedélyekkel, és hogy a beállításjegyzék felhője megegyezik-e az Azure CLI-vel.

## <a name="connectivity_forbidden_error"></a>CONNECTIVITY_FORBIDDEN_ERROR

Ez a hiba azt jelenti, hogy a megadott beállításjegyzékhez tartozó kérdés-végpont 403 Tiltott HTTP-állapottal válaszolt. Ez a hiba azt jelenti, hogy a felhasználók nem férhetnek hozzá a beállításjegyzékhez, valószínűleg egy virtuális hálózati konfiguráció miatt. A jelenleg konfigurált tűzfalszabályok megtekintéséhez futtassa a parancsot `az acr show --query networkRuleSet --name <registry>`.

*Lehetséges megoldások*: Távolítsa el a virtuális hálózati szabályokat, vagy adja hozzá az aktuális ügyfél IP-címét az engedélyezett listához.

## <a name="connectivity_challenge_error"></a>CONNECTIVITY_CHALLENGE_ERROR

Ez a hiba azt jelenti, hogy a célként megadott beállításjegyzék kérdéses végpontja nem adott meg kihívást.

*Lehetséges megoldások*: Próbálkozzon újra egy kis idő múlva. Ha a hiba továbbra is fennáll, nyisson meg https://aka.ms/acr/issues egy problémát a következő helyen:.

## <a name="connectivity_aad_login_error"></a>CONNECTIVITY_AAD_LOGIN_ERROR

Ez a hiba azt jelenti, hogy a célként megadott beállításjegyzék Challenge végpontja kiadta a problémát, de a beállításjegyzék nem támogatja Azure Active Directory hitelesítést.

*Lehetséges megoldások*: Próbáljon ki egy másik módszert a hitelesítésre, például rendszergazdai hitelesítő adatokkal. Ha a felhasználóknak Azure Active Directory használatával kell hitelesítést végezniük, nyisson meg egy https://aka.ms/acr/issues problémát a következő címen:.

## <a name="connectivity_refresh_token_error"></a>CONNECTIVITY_REFRESH_TOKEN_ERROR

Ez a hiba azt jelenti, hogy a beállításjegyzék bejelentkezési kiszolgálója nem válaszolt a frissítési tokenre, ezért a rendszer megtagadta a hozzáférést a célként megadott beállításjegyzékhez. Ez a hiba akkor fordulhat elő, ha a felhasználó nem rendelkezik a megfelelő engedélyekkel a beállításjegyzékben, vagy ha az Azure CLI felhasználói hitelesítő adatai elavultak.

*Lehetséges megoldások*: Ellenőrizze, hogy a felhasználó rendelkezik-e a megfelelő engedélyekkel a beállításjegyzékben; a `az login` futtatásával frissítheti az engedélyeket, a jogkivonatokat és a hitelesítő adatokat.

## <a name="connectivity_access_token_error"></a>CONNECTIVITY_ACCESS_TOKEN_ERROR

Ez a hiba azt jelenti, hogy a beállításjegyzék bejelentkezési kiszolgálója nem válaszolt egy hozzáférési jogkivonatra, így a rendszer megtagadta a hozzáférést a célként megadott beállításjegyzékhez. Ez a hiba akkor fordulhat elő, ha a felhasználó nem rendelkezik a megfelelő engedélyekkel a beállításjegyzékben, vagy ha az Azure CLI felhasználói hitelesítő adatai elavultak.

*Lehetséges megoldások*: Ellenőrizze, hogy a felhasználó rendelkezik-e a megfelelő engedélyekkel a beállításjegyzékben; a `az login` futtatásával frissítheti az engedélyeket, a jogkivonatokat és a hitelesítő adatokat.

## <a name="connectivity_ssl_error"></a>CONNECTIVITY_SSL_ERROR

Ez a hiba azt jelenti, hogy az ügyfél nem tudott biztonságos kapcsolatot létesíteni a tároló beállításjegyzékével. Ez a hiba általában akkor fordul elő, ha proxykiszolgálót futtat vagy használ.

*Lehetséges megoldások*: A proxy mögötti használatról további információt [itt](https://github.com/Azure/azure-cli/blob/master/doc/use_cli_effectively.md#working-behind-a-proxy)találhat.

## <a name="login_server_error"></a>LOGIN_SERVER_ERROR

Ez a hiba azt jelenti, hogy a CLI nem találta meg az adott beállításjegyzék bejelentkezési kiszolgálóját, és nem található alapértelmezett utótag az aktuális felhőhöz. Ez a hiba akkor fordulhat elő, ha a beállításjegyzék nem létezik, ha a felhasználó nem rendelkezik a megfelelő engedélyekkel a beállításjegyzékben, ha a beállításjegyzék felhője és az aktuális Azure CLI-felhő nem egyezik, vagy ha az Azure CLI verziója elavult.

*Lehetséges megoldások*: Győződjön meg arról, hogy a helyesírás helyes, valamint hogy a beállításjegyzék létezik; Ellenőrizze, hogy a felhasználó rendelkezik-e a megfelelő engedélyekkel a beállításjegyzékben, valamint hogy a beállításjegyzék és a CLI-környezet is egyezik-e a felhővel. frissítse az Azure CLI-t a legújabb verzióra.

## <a name="next-steps"></a>További lépések

A beállításjegyzék állapotának vizsgálatához tekintse meg [Az Azure Container Registry állapotának ellenőrzését](container-registry-check-health.md)ismertető témakört.

Tekintse [](container-registry-faq.md) meg a gyakori kérdések és a Azure Container Registry kapcsolatos egyéb ismert problémák gyakori kérdéseit.





<!-- LINKS - internal -->
[az-acr-check-health]: /cli/azure/acr#az-acr-check-health
