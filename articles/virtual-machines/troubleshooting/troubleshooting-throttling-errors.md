---
title: Hibák elhárítása az Azure-ban | Microsoft Docs
description: Szabályozási hibák, újrapróbálkozások és leállítási az Azure-beli számítási feladatokban.
services: virtual-machines
documentationcenter: ''
author: changov
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.service: virtual-machines
ms.devlang: na
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: changov
ms.reviewer: vashan, rajraj
ms.openlocfilehash: 6ae14edb7fa6b44f7c3bb961ffbcceb26eb9dee3
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875462"
---
# <a name="troubleshooting-api-throttling-errors"></a>API-szabályozási hibák elhárítása 

Az Azure-beli számítási kérelmeket előfizetésben és régiónként is szabályozhatja, hogy segítse a szolgáltatás általános teljesítményét. Biztosítjuk az Azure számítási erőforrás-szolgáltató (CRP) összes hívását, amely a Microsofthoz tartozó erőforrásokat kezeli. a számítási névtér nem lépi túl a maximálisan engedélyezett API-kérelmek sebességét. Ez a dokumentum leírja az API-szabályozást, részletesen ismerteti a szabályozással kapcsolatos problémákat, valamint a szabályozás elkerülésének ajánlott eljárásait.  

## <a name="throttling-by-azure-resource-manager-vs-resource-providers"></a>Szabályozás Azure Resource Manager vs erőforrás-szolgáltatók által  

Az Azure-ba való bejárati ajtó Azure Resource Manager az összes bejövő API-kérelem hitelesítését és megrendelésének ellenőrzését és szabályozását végzi. Az Azure Resource Manager hívási sebességre vonatkozó korlátozások és a kapcsolódó diagnosztikai válaszok HTTP-fejlécek [itt](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-request-limits)olvashatók.
 
Ha egy Azure API-ügyfél szabályozási hibát észlel, akkor a HTTP-állapot 429 túl sok kérés. Ha meg szeretné tudni, hogy a kérelmek szabályozása Azure Resource Manager vagy egy mögöttes erőforrás-szolgáltató, például a `x-ms-ratelimit-remaining-subscription-reads` CRP, vizsgálja meg `x-ms-ratelimit-remaining-subscription-writes` a Get kérések és a válasz fejléceit a nem Get kérelmek esetében. Ha a fennmaradó hívások száma eléri a 0 értéket, a rendszer elérte az előfizetés Azure Resource Manager által meghatározott általános hívási korlátját. Az összes előfizetési ügyfél által végzett tevékenységek együtt számítanak. Ellenkező esetben a szabályozás a cél erőforrás-szolgáltatótól (a kérelem URL-címének `/providers/<RP>` szegmense által tárgyalt) származik. 

## <a name="call-rate-informational-response-headers"></a>Hívási sebesség – tájékoztató válasz fejlécei 

| Fejléc                            | Érték formátuma                           | Példa                               | Leírás                                                                                                                                                                                               |
|-----------------------------------|----------------------------------------|---------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| x-ms-ratelimit-remaining-resource |```<source RP>/<policy or bucket>;<count>```| Microsoft.Compute/HighCostGet3Min;159 | A fennmaradó API-hívások száma az erőforrás-gyűjtőre vagy a műveleti csoportra kiterjedő szabályozási házirendhez, beleértve a kérelem célját                                                                   |
| x-MS-Request-Charge               | ```<count>```                             | 1                                     | A "felszámított hívások száma" a HTTP-kérelemhez a vonatkozó szabályzat korlátja felé. Ez általában 1. A Batch-kérések, például a virtuálisgép-méretezési csoport skálázása több számlálást is igénybe vehet. |


Vegye figyelembe, hogy az API-kérelmek több szabályozási házirendnek is kivonhatók. Az egyes szabályzatok külön `x-ms-ratelimit-remaining-resource` fejlécet kapnak. 

Íme egy példa a virtuálisgép-méretezési csoport törlésére vonatkozó kérelemre.

```
x-ms-ratelimit-remaining-resource: Microsoft.Compute/DeleteVMScaleSet3Min;107 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/DeleteVMScaleSet30Min;587 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/VMScaleSetBatchedVMRequests5Min;3704 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/VmssQueuedVMOperations;4720 
```

## <a name="throttling-error-details"></a>Szabályozási hiba részletei

A 429 HTTP-állapotot általában a rendszer a kérelmek elutasítására használja, mert elérte a hívási sebességre vonatkozó korlátot. A számítási erőforrás-szolgáltató jellemző sávszélesség-szabályozásra adott válasza az alábbi példához hasonlóan fog kinézni (csak a megfelelő fejlécek jelennek meg):

```
HTTP/1.1 429 Too Many Requests
x-ms-ratelimit-remaining-resource: Microsoft.Compute/HighCostGet3Min;46
x-ms-ratelimit-remaining-resource: Microsoft.Compute/HighCostGet30Min;0
Retry-After: 1200
Content-Type: application/json; charset=utf-8
{
  "code": "OperationNotAllowed",
  "message": "The server rejected the request because too many requests have been received for this subscription.",
  "details": [
    {
      "code": "TooManyRequests",
      "target": "HighCostGet30Min",
      "message": "{\"operationGroup\":\"HighCostGet30Min\",\"startTime\":\"2018-06-29T19:54:21.0914017+00:00\",\"endTime\":\"2018-06-29T20:14:21.0914017+00:00\",\"allowedRequestCount\":800,\"measuredRequestCount\":1238}"
    }
  ]
}

```

Az a házirend, amelynek a maradék hívási száma 0, az az egyik oka, hogy a rendszer visszaadja a sávszélesség-szabályozási hibát. Ebben az esetben `HighCostGet30Min`ez a következő:. A válasz törzsének teljes formátuma az általános Azure Resource Manager API-hiba formátuma (OData). A fő hibakód `OperationNotAllowed`a (z) egy számítási erőforrás-szolgáltató, amely a szabályozási hibák jelentését (más típusú ügyfél-hibák között) használja. A belső hiba (ok) tulajdonságaszerializáltJSON-struktúráttartalmazaszabályozásmegsértésénekrészleteivel.`message`

Ahogy az a fenti ábrán látható, minden szabályozási `Retry-After` hiba magában foglalja a fejlécet, amely megadja, hogy az ügyfélnek hány másodpercig kell várnia a kérés újrapróbálkozása előtt. 

## <a name="api-call-rate-and-throttling-error-analyzer"></a>API-hívások sebessége és a szabályozási hibák analizátora
A hibaelhárítási funkció előzetes verziója érhető el a számítási erőforrás-szolgáltató API-hoz. Ezek a PowerShell-parancsmagok az API-kérelmek sebességének és a műveleti csoportonként történő szabályozásának (szabályzata) alapján adják meg a statisztikát.
-   [Export-AzLogAnalyticRequestRateByInterval](https://docs.microsoft.com/powershell/module/az.compute/export-azloganalyticrequestratebyinterval)
-   [Export-AzLogAnalyticThrottledRequest](https://docs.microsoft.com/powershell/module/az.compute/export-azloganalyticthrottledrequest)

Az API-hívási statisztika nagyszerű betekintést nyújt az előfizetés ügyfeleinek viselkedésére, és lehetővé teszi a szabályozást okozó hívási minták egyszerű azonosítását.

Az elemzőnek az az időkorlátja, hogy nem számítja fel a lemezes és a pillanatkép-erőforrástípusok kéréseit (a felügyelt lemezek támogatásával). Mivel adatokat gyűjt a CRP telemetria, nem tud segítséget nyújtani az ARM-hibák szabályozásához. Ezek azonban könnyen azonosíthatók a megkülönböztető ARM-válasz fejlécei alapján, amint azt korábban már említettük.

A PowerShell-parancsmagok REST Service API-t használnak, amelyet közvetlenül az ügyfelek hívhatnak meg (bár még formális támogatás nélkül). A HTTP-kérelmek formátumának megtekintéséhez futtassa a-Debug kapcsolóval vagy a Snoop-parancsmaggal való végrehajtást a Hegedűs használatával.


## <a name="best-practices"></a>Ajánlott eljárások 

- Ne próbálja megismételni az Azure Service API-hibákat feltétel nélkül és/vagy azonnal. Gyakori esemény, hogy az ügyfél kódja gyors újrapróbálkozási hurokba kerül, amikor olyan hibát tapasztal, amely nem próbálkozik újra. Az újrapróbálkozások végül kimerítik a megcélzott művelet csoportjának engedélyezett hívási korlátját, és az előfizetés más ügyfeleire is hatással vannak. 
- A nagy mennyiségű API-automatizálási esetekben érdemes lehet a proaktív ügyféloldali önszabályozást megvalósítani, ha a cél műveleti csoport számára elérhető hívások száma az alacsonyabb küszöbérték alá esik. 
- Az aszinkron műveletek nyomon követése esetén vegye figyelembe az újrapróbálkozások utáni fejléc-emlékeztetőket. 
- Ha az ügyfél kódjának egy adott virtuális gépre vonatkozó információra van szüksége, a virtuális gép közvetlen lekérdezése ahelyett, hogy a megjelenő erőforráscsoporthoz tartozó összes virtuális gépet, vagy a teljes előfizetést fel kell vennie, majd ki kell vennie a szükséges virtuális gépet az ügyféloldali 
- Ha az ügyfél kódjában virtuális gépek, lemezek és Pillanatképek szükségesek egy adott Azure-helyről, használja a lekérdezés hely alapján történő lekérdezését az összes előfizetési virtuális gép lekérdezésekor, majd az ügyféloldali `GET /subscriptions/<subId>/providers/Microsoft.Compute/locations/<location>/virtualMachines?api-version=2017-03-30` szűrés hely szerint: lekérdezés a számítási erőforrás-szolgáltató régiójában végpontok. 
-   A virtuális gépek és a virtuálisgép-méretezési csoportok API-erőforrásainak létrehozásakor vagy frissítésekor sokkal hatékonyabb a visszaadott aszinkron művelet befejezésének követése, mint az erőforrás URL-címén végzett lekérdezés (a `provisioningState`alapján).

## <a name="next-steps"></a>További lépések

További információ az Azure egyéb szolgáltatásaira vonatkozó újrapróbálkozási útmutatóról: [adott szolgáltatások](https://docs.microsoft.com/azure/architecture/best-practices/retry-service-specific) újrapróbálkozási útmutatója
