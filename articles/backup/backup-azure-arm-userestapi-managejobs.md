---
title: 'Azure Backup: Biztonsági mentési feladatok kezelése REST API használatával'
description: Azure Backup biztonsági mentési és visszaállítási feladatainak kezelése REST API használatával
ms.reviewer: pullabhk
author: dcurwin
manager: carmonm
keywords: REST API; Azure-beli virtuális gép biztonsági mentése; Azure-beli virtuális gép visszaállítása;
ms.service: backup
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: dacurwin
ms.assetid: b234533e-ac51-4482-9452-d97444f98b38
ms.openlocfilehash: b10283c2946d01101b941d53b6bf03be3a12e99e
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/12/2019
ms.locfileid: "68954918"
---
# <a name="track-backup-and-restore-jobs-using-rest-api"></a>Biztonsági mentési és visszaállítási feladatok nyomon követése REST API használatával

Azure Backup szolgáltatás olyan feladatokat indít el, amelyek a háttérben futnak különböző forgatókönyvekben, például a biztonsági mentés elindítása, a visszaállítási műveletek, a biztonsági mentés letiltása. Ezek a feladatok az azonosítóik használatával követhetők nyomon.

## <a name="fetch-job-information-from-operations"></a>Feladatok adatainak beolvasása a műveletből

Egy művelet, például a biztonsági mentés elindítása mindig jobID ad vissza. Példa: A [trigger biztonsági mentési REST API műveletének](backup-azure-arm-userestapi-backupazurevms.md#example-responses-3) utolsó válasza a következő:

```http
{
  "id": "cd153561-20d3-467a-b911-cc1de47d4763",
  "name": "cd153561-20d3-467a-b911-cc1de47d4763",
  "status": "Succeeded",
  "startTime": "2018-09-12T02:16:56.7399752Z",
  "endTime": "2018-09-12T02:16:56.7399752Z",
  "properties": {
    "objectType": "OperationStatusJobExtendedInfo",
    "jobId": "41f3e94b-ae6b-4a20-b422-65abfcaf03e5"
  }
}
```

Az Azure virtuális gép biztonsági mentési feladatainak azonosítása a "jobId" mező alapján történik, és az [itt](https://docs.microsoft.com/rest/api/backup/jobdetails/) leírtak szerint nyomon követhető egy egyszerű *Get* kérelem használatával.

## <a name="tracking-the-job"></a>A feladatok nyomon követése

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupJobs/{jobName}?api-version=2017-07-01
```

A `{jobName}` a fent említett "jobId". A válasz mindig 200 OK az "állapot" mezővel, amely a feladatok aktuális állapotát jelzi. Ha "befejezett" vagy "CompletedWithWarnings", a "extendedInfo" szakasz a feladattal kapcsolatos további részleteket mutatja be.

### <a name="response"></a>Válasz

|Name (Név)  |Típus  |Leírás  |
|---------|---------|---------|
|200 OK     | [JobResource](https://docs.microsoft.com/rest/api/backup/jobdetails/get#jobresource)        | OK        |

#### <a name="example-response"></a>Példaválasz

Miután elküldte a *Get* URI-t, a rendszer egy 200 (ok) választ ad vissza.

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: e9702101-9da2-4681-bdf3-a54e17329a56
x-ms-client-request-id: ba4dff71-1655-4c1d-a71f-c9869371b18b; ba4dff71-1655-4c1d-a71f-c9869371b18b
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14989
x-ms-correlation-request-id: e9702101-9da2-4681-bdf3-a54e17329a56
x-ms-routing-request-id: SOUTHINDIA:20180521T102317Z:e9702101-9da2-4681-bdf3-a54e17329a56
Cache-Control: no-cache
Date: Mon, 21 May 2018 10:23:17 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-RecoveryServices-ResourceGroup-centralindia/providers/microsoft.recoveryservices/vaults/abdemovault/backupJobs/7ddead57-bcb9-4269-ac31-6a1b57588700",
  "name": "7ddead57-bcb9-4269-ac31-6a1b57588700",
  "type": "Microsoft.RecoveryServices/vaults/backupJobs",
  "properties": {
    "jobType": "AzureIaaSVMJob",
    "duration": "00:20:23.0896697",
    "actionsInfo": [
      1
    ],
    "virtualMachineVersion": "Compute",
    "extendedInfo": {
      "tasksList": [
        {
          "taskId": "Take Snapshot",
          "duration": "00:00:00",
          "status": "Completed"
        },
        {
          "taskId": "Transfer data to vault",
          "duration": "00:00:00",
          "status": "Completed"
        }
      ],
      "propertyBag": {
        "VM Name": "uttestvmub1",
        "Backup Size": "2332 MB"
      }
    },
    "entityFriendlyName": "uttestvmub1",
    "backupManagementType": "AzureIaasVM",
    "operation": "Backup",
    "status": "Completed",
    "startTime": "2018-05-21T08:35:40.9488967Z",
    "endTime": "2018-05-21T08:56:04.0385664Z",
    "activityId": "7df8e874-1d66-4f81-8e91-da2fe054811d"
  }
}
}

```