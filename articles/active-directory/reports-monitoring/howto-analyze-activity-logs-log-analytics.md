---
title: Azure Active Directory tevékenység naplófájljainak elemzése Azure Monitor naplók használatával | Microsoft Docs
description: Megtudhatja, hogyan elemezheti Azure Active Directory tevékenység naplóit Azure Monitor naplók használatával
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.assetid: 4535ae65-8591-41ba-9a7d-b7f00c574426
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: chadam
ms.reviewer: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 77eb03089d956d0fb32ef0463b3d1cdb49ff0dbb
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68989817"
---
# <a name="analyze-azure-ad-activity-logs-with-azure-monitor-logs"></a>Azure AD-beli tevékenység-naplók elemzése Azure Monitor naplókkal

Miután integrálta az [Azure ad](howto-integrate-activity-logs-with-log-analytics.md)-beli tevékenység-naplókat Azure monitor naplókkal, a Azure monitor naplók erejével használhatja a környezetbe való betekintést. A [log Analytics-nézeteket az Azure ad-tevékenységek naplóihoz](howto-install-use-log-analytics-views.md) is telepítheti, hogy hozzáférést kapjon a környezetében található naplózási és bejelentkezési eseményekhez szükséges előre elkészített jelentésekhez.

Ebből a cikkből megtudhatja, hogyan elemezheti az Azure AD-tevékenységek naplóit a Log Analytics munkaterületen. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Előfeltételek 

A lépések végrehajtásához a következőkre lesz szüksége:

* Egy Log Analytics munkaterület az Azure-előfizetésében. Megtudhatja, hogyan [hozhat létre log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace)munkaterületet.
* Először végezze el az [Azure ad-tevékenység naplófájljainak a log Analytics](howto-integrate-activity-logs-with-log-analytics.md)munkaterületre történő átirányításához szükséges lépéseket.

## <a name="navigate-to-the-log-analytics-workspace"></a>Navigáljon a Log Analytics munkaterületre

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 

2. Válassza a **Azure Active Directory**lehetőséget, majd a **figyelés** szakaszban válassza a **naplók** lehetőséget a log Analytics munkaterület megnyitásához. A munkaterület alapértelmezett lekérdezéssel fog megnyílni.

    ![Alapértelmezett lekérdezés](./media/howto-analyze-activity-logs-log-analytics/defaultquery.png)


## <a name="view-the-schema-for-azure-ad-activity-logs"></a>Az Azure AD-tevékenységek naplóihoz tartozó séma megtekintése

A naplók a munkaterület **AuditLogs** és **SigninLogs** tábláiba vannak leküldve. A következő táblák sémájának megtekintése:

1. Az előző szakasz alapértelmezett lekérdezés nézetében válassza ki a **séma** elemet, és bontsa ki a munkaterületet. 

2. Bontsa ki a **naplók kezelése** szakaszt, majd a **AuditLogs** vagy a **SignInLogs** kibontásával tekintse meg a naplózási sémát.
    ![](./media/howto-analyze-activity-logs-log-analytics/auditlogschema.png) Naplók![bejelentkezési naplói](./media/howto-analyze-activity-logs-log-analytics/signinlogschema.png)

## <a name="query-the-azure-ad-activity-logs"></a>Az Azure AD-tevékenységek naplóinak lekérdezése

Most, hogy már rendelkezik a naplókkal a munkaterületen, mostantól futtathat lekérdezéseket. Például az utolsó héten használt leggyakoribb alkalmazások lekéréséhez cserélje le az alapértelmezett lekérdezést a következőre, és válassza a **Futtatás** lehetőséget.

```
SigninLogs 
| where CreatedDateTime >= ago(7d)
| summarize signInCount = count() by AppDisplayName 
| sort by signInCount desc 
```

A legfelső szintű naplózási események a múlt héten való beszerzéséhez használja a következő lekérdezést:

```
AuditLogs 
| where TimeGenerated >= ago(7d)
| summarize auditCount = count() by OperationName 
| sort by auditCount desc 
```
## <a name="alert-on-azure-ad-activity-log-data"></a>Riasztás az Azure AD-tevékenység naplójának adatszolgáltatásáról

A lekérdezésen is beállíthat riasztásokat. Ha például riasztást szeretne beállítani, ha több mint 10 alkalmazást használtak az elmúlt héten:

1. A munkaterületen válassza a **riasztás beállítása** elemet a **szabály létrehozása** lap megnyitásához.

    ![Riasztás beállítása](./media/howto-analyze-activity-logs-log-analytics/setalert.png)

2. Válassza ki a riasztásban létrehozott alapértelmezett **riasztási feltételeket** , és frissítse a küszöbértéket az alapértelmezett metrika 10 értékre.

    ![Riasztási feltételek](./media/howto-analyze-activity-logs-log-analytics/alertcriteria.png)

3. Adja meg a riasztás nevét és leírását, és válassza ki a súlyossági szintet. A példánkban megadhatjuk a tájékoztatót.

4. Válassza ki azt a **műveleti csoportot** , amelyre a rendszer riasztást küld, amikor a jel bekövetkezik. Dönthet úgy is, hogy e-mailben vagy szöveges üzenetben értesíti a csapatot, vagy automatizálhatja a műveletet webhookok, Azure functions vagy Logic Apps használatával. További információ [a riasztási csoportok létrehozásáról és kezeléséről a Azure Portal](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups).

5. Miután konfigurálta a riasztást, válassza a **riasztás létrehozása** lehetőséget az engedélyezéséhez. 

## <a name="install-and-use-pre-built-views-for-azure-ad-activity-logs"></a>Előre elkészített nézetek telepítése és használata az Azure AD-tevékenység naplóihoz

Az Azure AD-tevékenységek naplóihoz tartozó előre elkészített log Analytics-nézeteket is letöltheti. A nézetek több jelentést biztosítanak a naplózási és bejelentkezési eseményeket érintő gyakori forgatókönyvekkel kapcsolatban. Az előző szakaszban ismertetett lépéseket követve riasztást is használhat a jelentésekben megadott összes adattal kapcsolatban.

* **Azure ad-fiók kiépítési eseményei**: Ez a nézet a naplózási kiépítési tevékenységgel kapcsolatos jelentéseket jeleníti meg, például az új felhasználók kiépített és kiépítési hibáit, a frissített felhasználók számát és a frissítési hibákat, valamint a kiosztott és a hozzájuk tartozó hibák számát.    
* **Bejelentkezési események**: Ez a nézet a figyelési tevékenységekkel kapcsolatos legfontosabb jelentéseket mutatja be, például az alkalmazás, a felhasználó, az eszköz, valamint egy összegző nézet, amely az idő múlásával követi nyomon a bejelentkezések számát.
* Az **engedélyt végző felhasználók**: Ebben a nézetben láthatók a felhasználói hozzájárulással kapcsolatos jelentések, például a felhasználó hozzájárulása, a hozzájárulást küldő felhasználók, valamint az összes hozzájárulási alapú alkalmazáshoz tartozó bejelentkezések. 

Ismerje meg, hogyan [telepítheti és használhatja a log Analytics-nézeteket az Azure ad-tevékenységek naplóihoz](howto-install-use-log-analytics-views.md). 


## <a name="next-steps"></a>További lépések

* [Ismerkedés a Azure Monitor-naplókban található lekérdezésekkel](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries)
* [Riasztási csoportok létrehozása és kezelése a Azure Portal](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups)
* [A log Analytics-nézetek telepítése és használata Azure Active Directory](howto-install-use-log-analytics-views.md)
