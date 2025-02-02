---
title: Az Azure Red Hat OpenShift hibaelhárítása |} A Microsoft Docs
description: Gyakori hibák elhárítása Azure Red Hat OpenShift és hibáinak elhárítása
services: container-service
author: jimzim
ms.author: jzim
manager: jeconnoc
ms.service: container-service
ms.topic: troubleshooting
ms.date: 05/08/2019
ms.openlocfilehash: 7f2bdf643f12671bec3d0c087d8775844099fe9a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66306249"
---
# <a name="troubleshooting-for-azure-red-hat-openshift"></a>Az Azure Red Hat OpenShift hibaelhárítása

Ez a cikk részletesen létrehozása vagy a Microsoft Azure Red Hat OpenShift fürtök felügyelete során tapasztalt gyakori problémákat.

## <a name="retrying-the-creation-of-a-failed-cluster"></a>Újrapróbálkozás sikertelen fürt létrehozása

Ha az Azure Red Hat OpenShift létrehozása a fürt használatával a `az` CLI-parancs meghiúsul, a létrehozás újrapróbálkozás továbbra is sikertelen lesz.
Használat `az openshift delete` törölje a sikertelen fürtöt, majd hozzon létre egy teljesen új fürtöt.

## <a name="hidden-azure-red-hat-openshift-cluster-resource-group"></a>Rejtett Azure Red Hat OpenShift fürterőforrás-csoportban

Jelenleg a `Microsoft.ContainerService/openShiftManagedClusters` az Azure CLI által automatikusan létrehozott erőforrás (`az openshift create` parancs) az Azure Portalon rejtve marad. Az a **erőforráscsoport** nézet, ellenőrzés **rejtett típusok megjelenítése** az erőforráscsoport megtekintéséhez.

![Képernyőkép a portálon a rejtett típus jelölőnégyzet](./media/aro-portal-hidden-type.png)

## <a name="creating-a-cluster-results-in-error-that-no-registered-resource-provider-found"></a>A hiba, amely a nem regisztrált erőforrás található szolgáltató eredmények fürt létrehozása

Ha létrehoz egy fürt eredmények hibát, amely `No registered resource provider found for location '<location>' and API version '2019-04-30' for type 'openShiftManagedClusters'. The supported api-versions are '2018-09-30-preview`, ezután az előzetes verzió része volt, és most be kell [beszerzési Azure virtuális gép fenntartott példányok](https://aka.ms/openshift/buy) általánosan elérhető a termék használatához. Foglalás csökkenti a teljes körűen felügyelt Azure-szolgáltatások előzetes és felhőköltéseiket. Tekintse meg [ *Mik az Azure-foglalások* ](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations) tudhat meg többet a foglalást, és hogyan, pénzt takaríthat meg.

## <a name="next-steps"></a>További lépések

- Próbálja ki a [Red Hat OpenShift súgóközpont](https://help.openshift.com/) további az OpenShift hibaelhárítási.

- Választ találhat [– gyakran ismételt kérdések az Azure Red Hat OpenShift](openshift-faq.md).
