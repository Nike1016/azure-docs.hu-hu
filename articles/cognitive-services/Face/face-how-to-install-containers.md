---
title: Tárolók telepítése és futtatása – FACE API
titleSuffix: Azure Cognitive Services
description: A bemutatóhoz tartozó tárolók letöltése, telepítése és futtatása az útmutatóban.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: dapine
ms.openlocfilehash: 93a6d0a42d6d7f07dd8947ce9f8ae99a39d44475
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564144"
---
# <a name="install-and-run-face-containers"></a>Face containers telepítése és futtatása

Az Azure Cognitive Services Face szabványos Linux-tárolót biztosít a Docker számára, amely észleli az emberi arcokat a képeken. Emellett azonosítja azokat az attribútumokat is, amelyek olyan arc-tereptárgyak, mint például az orr és a szemek, a nemek, az életkor és más, géppel előre jelzett arc-funkciók. Az észlelésen kívül a Face utasítással megtekintheti, hogy két arc van-e ugyanazon a képen, vagy a különböző képek azonosak-e a megbízhatósági pontszám használatával. Az arc emellett összehasonlíthatja az arcokat egy adatbázissal, hogy megtudja, van-e már hasonló vagy azonos arc. A hasonló arcok csoportokba rendezése közös vizualizációs tulajdonságok használatával is elvégezhető.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Face API tárolók használata előtt meg kell felelnie a következő előfeltételeknek.

|Kötelező|Cél|
|--|--|
|Docker-motor| A Docker-motornak telepítve kell lennie a [gazdagépen](#the-host-computer). A Docker csomagokat biztosít a Docker-környezet konfigurálásához [MacOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/)és [Linux](https://docs.docker.com/engine/installation/#supported-platforms)rendszereken. A Docker és a tárolók alapfogalmainak ismertetését lásd: a [a Docker áttekintése](https://docs.docker.com/engine/docker-overview/).<br><br> Docker kell konfigurálni, hogy a tárolók számlázási adatok küldése az Azure-ba történő csatlakozáshoz. <br><br> Windows rendszeren a Docker-t is konfigurálni kell a Linux-tárolók támogatásához.<br><br>|
|A Docker ismerete | Alapvető ismeretekre van szüksége a Docker-fogalmakról, például a kibocsátásiegység-forgalmi jegyzékekről, a adattárakról, a tárolók és a tárolók lemezképéről. Emellett az alapszintű `docker` parancsok ismeretére is szükség van.| 
|Face erőforrás |A tároló használatához a következőket kell tennie:<br><br>Egy Azure **Face** -erőforrást és a hozzá tartozó API-kulcsot és a végpont URI-ját. Mindkét érték elérhető az erőforrás **Áttekintés** és **kulcsok** oldalain. Szükségük van a tároló elindítására.<br><br>**{API_KEY}** : A **kulcsok** oldalon található két elérhető erőforrás-kulcs egyike<br><br>**{ENDPOINT_URI}** : Az **Áttekintés** oldalon megadott végpont

## <a name="request-access-to-the-private-container-registry"></a>A privát tárolóregisztrációs hozzáférés kérése

[!INCLUDE [Request access to private container registry](../../../includes/cognitive-services-containers-request-access.md)]

### <a name="the-host-computer"></a>A gazdaszámítógép

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Tároló-követelményeket és javaslatokat

A következő táblázat az egyes Face API tárolók számára lefoglalható minimális és ajánlott CPU-magokat és memóriát ismerteti.

| Tároló | Minimális | Ajánlott | Másodpercenkénti tranzakciók<br>(Minimum, maximum)|
|-----------|---------|-------------|--|
|Face | 1 mag, 2 GB memória | 1 mag, 4 GB memória |10, 20|

* Minden mag legalább 2,6 GHz-es vagy gyorsabb lehet.
* Másodpercenkénti tranzakciók (TPS).

Az alap és a memória a `--cpus` `docker run` parancs `--memory` részeként használt és beállításoknak felel meg.

## <a name="get-the-container-image-with-docker-pull"></a>A tároló rendszerképének beolvasása a Docker-lekéréssel

A Face API tároló lemezképei érhetők el. 

| Tároló | Tárház |
|-----------|------------|
| Face | `containerpreview.azurecr.io/microsoft/cognitive-services-face:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-face-container"></a>Docker-lekérés a Face tárolóhoz

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-face:latest
```

## <a name="use-the-container"></a>A tároló használata

Miután a tároló a gazdagépen [](#the-host-computer)található, a következő folyamat használatával dolgozhat a tárolóval.

1. [Futtassa a tárolót](#run-the-container-with-docker-run) a szükséges számlázási beállításokkal. További [példák](./face-resource-container-config.md#example-docker-run-commands) a `docker run` parancsra. 
1. [A tároló előrejelzési végpontjának lekérdezése](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>A tároló futtatása a Docker futtatásával

A három tároló bármelyikének futtatásához használja a [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) parancsot. A parancs a következő paramétereket használja.

| Helyőrző | Value |
|-------------|-------|
|{API_KEY} | Ez a kulcs a tároló elindítására szolgál, és elérhető az Azure `Cognitive Services` **Keys** lapon. |
|{ENDPOINT_URI} | A számlázási végpont URI-értéke az Azure `Cognitive Services` **Áttekintés** oldalán érhető el. Például: `https://westus.api.cognitive.microsoft.com/face/v1.0`.|

Adja hozzá `face/v1.0` az útválasztást a végpont URI-hoz, ahogy az az előző ENDPOINT_URI példában is látható. 

Cserélje le ezeket a paramétereket a saját értékeire a `docker run` következő parancs példájában:

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-face \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Ez a parancs:

* Egy Face tárolót futtat a tároló rendszerképből.
* Egy CPU-mag és 4 GB memóriát foglal le.
* Elérhetővé teszi a 5000-es TCP-portot, és kioszt egy pszeudo TTY-t a tárolóhoz.
* A automatikusan eltávolítja a tárolót a kilépés után. A tároló rendszerképe továbbra is elérhető a gazdaszámítógépen. 

További [példák](./face-resource-container-config.md#example-docker-run-commands) a `docker run` parancsra. 

> [!IMPORTANT]
> A `Eula`, `Billing`a és`ApiKey` a beállításokat meg kell adni a tároló futtatásához, vagy a tároló nem indul el. További információkért lásd: [számlázási](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>A tároló előrejelzési végpontjának lekérdezése

A tároló REST-alapú lekérdezés-előrejelzési végpont API-kat biztosít. 

A tároló API `https://localhost:5000`-k esetében használja a gazdagépet.


<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>A tároló leállítása

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Hibaelhárítás

Ha a tárolót kimeneti [csatlakoztatással](./face-resource-container-config.md#mount-settings) futtatja, és a naplózás engedélyezve van, a tároló olyan naplófájlokat hoz létre, amelyek hasznosak lehetnek a tároló indításakor vagy futtatásakor előforduló problémák elhárításához. 


## <a name="billing"></a>Számlázás

Az Face API tárolók számlázási adatokat küldenek az Azure-nak az Azure-fiókjában lévő Face API-erőforrás használatával. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Ezek a beállítások kapcsolatos további információkért lásd: [tárolók konfigurálása](./face-resource-container-config.md).

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Összegzés

Ebben a cikkben megtanulta a fogalmakat és a munkafolyamatot Face API tárolók letöltéséhez, telepítéséhez és futtatásához. Összegezve:

* A Face API három Linux-tárolót biztosít a Docker számára, amelyek kulcsfontosságú kifejezést biztosítanak a kinyeréshez, a nyelvfelismerés és a hangulat elemzéséhez.
* A rendszer letölti a tároló lemezképeit a Azure Container Registryról.
* Tárolórendszerképek futtatása a Docker.
* Az REST API vagy az SDK használatával meghívhatja a műveleteket Face API tárolókban a tároló gazdagép URI azonosítójának megadásával.
* A tárolók példányainak létrehozásakor számlázási adatokat kell megadnia.

> [!IMPORTANT]
> Cognitive Services tárolók nem futtathatók az Azure-hoz való csatlakozás nélkül. Az ügyfeleknek engedélyezniük kell, hogy a tárolók a számlázási adatokat mindig a mérési szolgáltatással kommunikáljanak. Cognitive Services tárolók nem küldenek ügyféladatokat, például az elemzett képet vagy szöveget a Microsoftnak.

## <a name="next-steps"></a>További lépések

* A konfigurációs beállításokkal kapcsolatban lásd: [tárolók konfigurálása](face-resource-container-config.md).
* Az arcok észlelésével és azonosításával kapcsolatos további tudnivalókért tekintse meg a [Face Overview](Overview.md)című témakört.
* További információ a tároló által támogatott módszerekről: [Face API](//westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).
* További Cognitive Services tárolók használatához lásd: [Cognitive Services tárolók](../cognitive-services-container-support.md).
