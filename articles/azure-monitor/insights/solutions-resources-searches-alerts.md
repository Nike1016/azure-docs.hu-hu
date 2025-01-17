---
title: Mentett keresések a felügyeleti megoldásokban | Microsoft Docs
description: A felügyeleti megoldások általában a Log Analytics mentett kereséseket tartalmaznak a megoldás által gyűjtött adatok elemzéséhez. Emellett riasztásokat is meghatározhatnak a felhasználó értesítése céljából, vagy a kritikus problémákra reagálva automatikusan műveleteket hajtanak végre. Ez a cikk bemutatja, hogyan határozhatja meg Log Analytics mentett kereséseket egy Resource Manager-sablonban, hogy azok a felügyeleti megoldásokban is szerepeljenek.
services: monitoring
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/29/2019
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e2e32fb57a5ee34da8c342649cc1740d111723ec
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68662905"
---
# <a name="adding-log-analytics-saved-searches-and-alerts-to-management-solution-preview"></a>Log Analytics mentett keresések és riasztások hozzáadása a felügyeleti megoldáshoz (előzetes verzió)

> [!IMPORTANT]
> Amint azt [korábban bejelentettük](https://azure.microsoft.com/updates/switch-api-preference-log-alerts/), az *2019. június 1.* után létrehozott log Analytics-munkaterület (ek) a riasztási szabályokat **csak** az Azure scheduledQueryRules [REST API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/), [Azure Resource Manager sablon](../../azure-monitor/platform/alerts-log.md#managing-log-alerts-using-azure-resource-template) és a PowerShell használatával kezelhetik. [ parancsmag](../../azure-monitor/platform/alerts-log.md#managing-log-alerts-using-powershell). Az ügyfelek könnyedén [válthatnak a riasztási szabályok kezelésének előnyben részesített eszközein](../../azure-monitor/platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api) a régebbi munkaterületek számára, hogy Azure monitor scheduledQueryRules használják alapértelmezettként, és számos [új előnyt](../../azure-monitor/platform/alerts-log-api-switch.md#benefits-of-switching-to-new-azure-api) szerezzenek, mint például a natív PowerShell-parancsmagok használatának lehetősége lookback időszak a szabályokban, szabályok létrehozása külön erőforráscsoporthoz vagy előfizetéshez, és még sok minden más.

> [!NOTE]
> Ez a jelenleg előzetes verzióban elérhető felügyeleti megoldások létrehozásának előzetes dokumentációja. Az alább ismertetett sémák változhatnak.

A [felügyeleti megoldások](solutions.md) általában a log Analytics [mentett kereséseket](../../azure-monitor/log-query/log-query-overview.md) is tartalmaznak a megoldás által gyűjtött adatok elemzéséhez. Emellett riasztásokat is [](../../azure-monitor/platform/alerts-overview.md) meghatározhatnak a felhasználó értesítése céljából, vagy a kritikus problémákra reagálva automatikusan műveleteket hajtanak végre. Ez a cikk azt ismerteti, hogyan határozható meg Log Analytics mentett keresések és riasztások egy [erőforrás-kezelési sablonban](../../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md) , hogy azok a [felügyeleti megoldásokban](solutions-creating.md)is szerepeljenek.

> [!NOTE]
> A cikkben szereplő minták olyan paramétereket és változókat használnak, amelyek szükségesek vagy közösek a felügyeleti megoldásokhoz, és az Azure-beli [felügyeleti megoldás kialakítása és](solutions-creating.md) létrehozása című témakörben olvashatók.

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk azt feltételezi, hogy már ismeri a [felügyeleti megoldás létrehozását](solutions-creating.md) , valamint egy [Resource Manager-sablon](../../azure-resource-manager/resource-group-authoring-templates.md) és a megoldás fájljának szerkezetét.


## <a name="log-analytics-workspace"></a>Log Analytics-munkaterület
Log Analytics összes erőforrása egy munkaterületen [](../../azure-monitor/platform/manage-access.md)található. A [log Analytics munkaterület és az Automation-fiók](solutions.md#log-analytics-workspace-and-automation-account)című témakörben leírtak szerint a munkaterület nem szerepel a felügyeleti megoldásban, de a megoldás telepítése előtt léteznie kell. Ha nem érhető el, a megoldás telepítése sikertelen lesz.

A munkaterület neve minden Log Analytics erőforrás neve. Ezt a megoldásban a **munkaterület** paraméterrel végezheti el, mint a SavedSearch-erőforrás következő példájában.

    "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearchId'))]"

## <a name="log-analytics-api-version"></a>Log Analytics API-verzió
A Resource Manager-sablonban definiált összes Log Analytics-erőforráshoz tartozik egy **apiVersion** , amely meghatározza az erőforrás által használandó API verzióját.

A következő táblázat az ebben a példában használt erőforrás API-verzióját sorolja fel.

| Erőforrás típusa | API-verzió | Lekérdezés |
|:---|:---|:---|
| savedSearches | 2017-03-15 – előzetes verzió | Esemény &#124; , ahol EventLevelName = = "hiba"  |


## <a name="saved-searches"></a>Mentett keresések
Belefoglalja a [mentett kereséseket](../../azure-monitor/log-query/log-query-overview.md) egy megoldásba, hogy lehetővé tegye a felhasználók számára a megoldás által gyűjtött adatok lekérdezését. A mentett keresések a Azure Portal **mentett keresések** területén jelennek meg. Minden egyes riasztáshoz szükség van egy mentett keresésre is.

[Log Analytics mentett keresési](../../azure-monitor/log-query/log-query-overview.md) erőforrások típusa, `Microsoft.OperationalInsights/workspaces/savedSearches` és a következő szerkezettel rendelkezik. Ilyenek például a gyakori változók és paraméterek, hogy a kódrészletet másolja és illessze be a megoldás fájljába, és módosítsa a paraméterek nevét.

    {
        "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name)]",
        "type": "Microsoft.OperationalInsights/workspaces/savedSearches",
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "dependsOn": [
        ],
        "tags": { },
        "properties": {
            "etag": "*",
            "query": "[variables('SavedSearch').Query]",
            "displayName": "[variables('SavedSearch').DisplayName]",
            "category": "[variables('SavedSearch').Category]"
        }
    }

A mentett keresés minden tulajdonságát az alábbi táblázat ismerteti.

| Tulajdonság | description |
|:--- |:--- |
| category | A mentett keresés kategóriája.  Az azonos megoldásban mentett keresések gyakran egyetlen kategóriába kerülnek, így a konzolon együtt lesznek csoportosítva. |
| displayName | A portálon a mentett kereséshez megjelenítendő név. |
| query | A futtatandó lekérdezés. |

> [!NOTE]
> Előfordulhat, hogy Escape-karaktereket kell használnia a lekérdezésben, ha olyan karaktereket tartalmaz, amelyek JSON-ként értelmezhetők. Ha például a lekérdezés **AzureActivity volt | OperationName: "Microsoft. számítás/virtualMachines/írás"** , a megoldás fájljában kell megírni a **AzureActivity | OperationName:/\"Microsoft. számítás/virtualMachines/írás\"** .

## <a name="alerts"></a>Riasztások
Az Azure- [naplók riasztásait](../../azure-monitor/platform/alerts-unified-log.md) az Azure riasztási szabályai hozzák létre, amelyek rendszeres időközönként futtatják a megadott naplózási lekérdezéseket. Ha a lekérdezés eredményei megfelelnek a megadott feltételeknek, egy riasztási rekord jön létre, és egy vagy több művelet is fut a [műveleti csoportok](../../azure-monitor/platform/action-groups.md)használatával.

A felhasználók számára, hogy a riasztások kiterjesztése az Azure-bA műveletek most már az Azure action groups általi szabályozza. Ha egy munkaterület és a hozzá tartozó riasztások ki vannak bővítve az Azure-ra, a [műveleti csoport – Azure Resource Manager sablon](../../azure-monitor/platform/action-groups-create-resource-manager-template.md)segítségével lekérheti vagy hozzáadhatja a műveleteket.
A régi felügyeleti megoldás riasztási szabályai a következő három különböző erőforrásból állnak.

- **Mentett keresés.** Meghatározza a futtatott naplóbeli keresést. Több riasztási szabály is megoszthat egyetlen mentett keresést.
- **Menetrend.** Meghatározza, hogy a rendszer milyen gyakran futtassa a naplóbeli keresést. Minden riasztási szabályhoz egy és csak egy ütemterv tartozik.
- **Riasztási művelet.** Minden riasztási szabályhoz egy műveleti csoport erőforrás vagy műveleti erőforrás (örökölt) tartozik, **amely meghatározza** a riasztás részleteit, például a riasztási rekord létrehozási feltételeit és a riasztás súlyosságát. A [műveleti csoport](../../azure-monitor/platform/action-groups.md) erőforrás a riasztások elindítására szolgáló konfigurált műveletek listáját is tartalmazhatja, például hanghívást, SMS-t, e-mailt, webhookot, ITSM eszközt, Automation-runbook, logikai alkalmazást stb.

A fentiekben a mentett keresési erőforrások szerepelnek. A többi erőforrást az alábbiakban ismertetjük.

### <a name="schedule-resource"></a>Erőforrás-ütemterv

Egy mentett kereséshez egy vagy több ütemterv is tartozhat, amely egy külön riasztási szabályt jelképez. Az ütemterv meghatározza, hogy a keresés milyen gyakran fusson, és milyen időintervallumot kell lekérni az adat lekéréséhez. Az erőforrások ütemezett erőforrásai rendelkeznek `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/` a következő szerkezettel: Ilyenek például a gyakori változók és paraméterek, hogy a kódrészletet másolja és illessze be a megoldás fájljába, és módosítsa a paraméterek nevét.

    {
        "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name, '/', variables('Schedule').Name)]",
        "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/",
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('SavedSearch').Name)]"
        ],
        "properties": {
            "etag": "*",
            "interval": "[variables('Schedule').Interval]",
            "queryTimeSpan": "[variables('Schedule').TimeSpan]",
            "enabled": "[variables('Schedule').Enabled]"
        }
    }
Az ütemezett erőforrások tulajdonságait az alábbi táblázat ismerteti.

| Elem neve | Szükséges | description |
|:--|:--|:--|
| enabled       | Igen | Megadja, hogy a riasztás engedélyezve van-e a létrehozásakor. |
| tartam      | Igen | Milyen gyakran fut a lekérdezés percek alatt. |
| queryTimeSpan | Igen | Az az időtartam, ameddig az eredmények kiértékelése megtörténik. |

Az ütemezett erőforrásnak a mentett kereséstől kell függ lennie, hogy az az ütemterv előtt legyen létrehozva.
> [!NOTE]
> Az ütemterv nevének egyedinek kell lennie egy adott munkaterületen; két ütemterv nem rendelkezhet ugyanazzal az AZONOSÍTÓval, még akkor sem, ha különböző mentett keresésekhez vannak társítva. Emellett a Log Analytics API-val létrehozott összes mentett kereséshez, ütemtervhez és művelethez is neve legyen kisbetűs.

### <a name="actions"></a>Műveletek
Egy több művelet is lehet. Művelet egy vagy több folyamat végrehajtásához, például egy levelet küld, vagy a runbook indítása adhat meg, vagy azt adhat meg egy küszöbértéket, amely azt határozza meg, ha a keresési eredmények bizonyos feltételeknek megfelelő-e. Bizonyos műveleteket meghatározzuk is, hogy a folyamatok hajtja végre, hogy a küszöbértéket.
A műveletek a [műveleti csoport] erőforrás vagy a műveleti erőforrás használatával definiálhatók.

A **Type** tulajdonságnak két típusú művelet-erőforrása van megadva. Az ütemtervnek egy **riasztási** műveletre van szüksége, amely meghatározza a riasztási szabály részleteit, valamint azt, hogy milyen műveleteket kell végrehajtani a riasztások létrehozásakor. A művelet erőforrásainak típusa a `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions`következő:.

A riasztási műveletek az alábbi struktúrával rendelkeznek. Ilyenek például a gyakori változók és paraméterek, hogy a kódrészletet másolja és illessze be a megoldás fájljába, és módosítsa a paraméterek nevét.

```json
{
    "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name, '/', variables('Schedule').Name, '/', variables('Alert').Name)]",
    "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions",
    "apiVersion": "[variables('LogAnalyticsApiVersion')]",
    "dependsOn": [
        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('SavedSearch').Name, '/schedules/', variables('Schedule').Name)]"
    ],
    "properties": {
        "etag": "*",
        "type": "Alert",
        "name": "[variables('Alert').Name]",
        "description": "[variables('Alert').Description]",
        "severity": "[variables('Alert').Severity]",
        "threshold": {
            "operator": "[variables('Alert').Threshold.Operator]",
            "value": "[variables('Alert').Threshold.Value]",
            "metricsTrigger": {
                "triggerCondition": "[variables('Alert').Threshold.Trigger.Condition]",
                "operator": "[variables('Alert').Trigger.Operator]",
                "value": "[variables('Alert').Trigger.Value]"
            },
        },
        "AzNsNotification": {
            "GroupIds": "[variables('MyAlert').AzNsNotification.GroupIds]",
            "CustomEmailSubject": "[variables('MyAlert').AzNsNotification.CustomEmailSubject]",
            "CustomWebhookPayload": "[variables('MyAlert').AzNsNotification.CustomWebhookPayload]"
        }
    }
}
```

A riasztási művelet erőforrásaihoz tartozó tulajdonságokat az alábbi táblázatokban ismertetjük.

| Elem neve | Szükséges | description |
|:--|:--|:--|
| type | Igen | A művelet típusa.  Riasztás a riasztási műveletekhez. |
| name | Igen | A riasztás megjelenítendő neve.  Ez a név jelenik meg a konzolon a riasztási szabályhoz. |
| description | Nem | A riasztás opcionális leírása. |
| severity | Igen | A riasztási rekord súlyossága a következő értékekkel:<br><br> **critical**<br>**Figyelmeztetés**<br>**tájékoztató**


#### <a name="threshold"></a>Küszöbérték
Ezt a szakaszt kötelező megadni. Meghatározza a riasztási küszöbérték tulajdonságait.

| Elem neve | Szükséges | description |
|:--|:--|:--|
| Operator | Igen | A következő értékek összehasonlítására szolgáló operátor:<br><br>**gt = nagyobb,<br>mint lt = kisebb, mint** |
| Value | Igen | Az eredmények összehasonlítására szolgáló érték. |

##### <a name="metricstrigger"></a>MetricsTrigger
Ez a szakasz nem kötelező. Adja meg egy metrika-mérési riasztáshoz.

| Elem neve | Szükséges | description |
|:--|:--|:--|
| TriggerCondition | Igen | Azt határozza meg, hogy a küszöbérték a következő értékek összes megszegése vagy egymást követő megszegése esetén:<br><br>**Összesen<br>egymást követő** |
| Operator | Igen | A következő értékek összehasonlítására szolgáló operátor:<br><br>**gt = nagyobb,<br>mint lt = kisebb, mint** |
| Value | Igen | Azon időpontok száma, amelyeknek a feltételeknek teljesülnie kell a riasztás indításához. |


#### <a name="throttling"></a>Szabályozás
Ez a szakasz nem kötelező. Akkor adja meg ezt a szakaszt, ha a riasztások létrehozása után bizonyos időtartamon belül szeretné letiltani a riasztásokat egy adott szabályból.

| Elem neve | Szükséges | description |
|:--|:--|:--|
| DurationInMinutes | Igen, ha a szabályozás eleme tartalmaz | Azon percek száma, ameddig a riasztások elhagyása után a rendszer létrehoz egy azonos riasztási szabályból. |

#### <a name="azure-action-group"></a>Azure-műveleti csoport
Az Azure-ban, az összeset műveletcsoport használja az alapértelmezett mechanizmusként műveletek kezelésére. A műveletcsoport adja meg a műveletet egyszer, és társíthatja a műveletcsoport több riasztás – az Azure-ban. Nem szükséges, ismételten deklarálja és újra ugyanazokat a műveleteket. Műveletcsoportok támogatja a több műveletek – például az e-mailben, SMS, hanghívás, az ITSM-kapcsolatot, Automation-Runbook, Webhook URI.

A felhasználó számára ki van bővítve a riasztások az Azure-bA – ütemezés most rendelkezik küszöbértéket, riasztást létrehozni a együtt átadott műveletcsoport részletei. Az e-mailek adatait, a webhook URL-jeit, a Runbook Automation részleteit és az egyéb műveleteket a riasztás létrehozása előtt először a műveleti csoportba kell definiálni. Létrehozhat [műveleti csoportot Azure monitor](../../azure-monitor/platform/action-groups.md) a portálon, vagy használhat [műveleti csoport – erőforrás sablont](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

| Elem neve | Szükséges | description |
|:--|:--|:--|
| AzNsNotification | Igen | Azon Azure-műveleti csoport erőforrás-azonosítója, amelyet a riasztáshoz kell társítani a szükséges műveletek elvégzéséhez, ha a riasztási feltételek teljesülnek. |
| CustomEmailSubject | Nem | Az e-mailek egyéni tárgya a társított műveleti csoportban megadott összes címre elküldve. |
| CustomWebhookPayload | Nem | Testreszabott hasznos adatok küldése a társított műveleti csoportban definiált összes webhook-végpontnak. A formátum attól függ, hogy mit vár a webhook, és érvényes szerializált JSON-ként kell lennie. |

## <a name="sample"></a>Minta

Az alábbi példa egy olyan megoldást mutat be, amely a következő erőforrásokat tartalmazza:

- Mentett keresés
- Ütemezés
- Műveletcsoport

A minta a [standard megoldás paramétereinek]( solutions-solution-file.md#parameters) változóit használja, amelyeket általában egy megoldásban használtak, szemben az rögzítjük lévő értékekkel.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0",
    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "Description": "Name of Log Analytics workspace"
            }
        },
        "workspaceregionId": {
            "type": "string",
            "metadata": {
                "Description": "Region of Log Analytics workspace"
            }
        },
        "actiongroup": {
            "type": "string",
            "metadata": {
                "Description": "List of action groups for alert actions separated by semicolon"
            }
        }
    },
    "variables": {
        "SolutionName": "MySolution",
        "SolutionVersion": "1.0",
        "SolutionPublisher": "Contoso",
        "ProductName": "SampleSolution",
        "LogAnalyticsApiVersion-Search": "2017-03-15-preview",
        "LogAnalyticsApiVersion-Solution": "2015-11-01-preview",
        "MySearch": {
            "displayName": "Error records by hour",
            "query": "MyRecord_CL | summarize AggregatedValue = avg(Rating_d) by Instance_s, bin(TimeGenerated, 60m)",
            "category": "Samples",
            "name": "Samples-Count of data"
        },
        "MyAlert": {
            "Name": "[toLower(concat('myalert-',uniqueString(resourceGroup().id, deployment().name)))]",
            "DisplayName": "My alert rule",
            "Description": "Sample alert. Fires when 3 error records found over hour interval.",
            "Severity": "critical",
            "ThresholdOperator": "gt",
            "ThresholdValue": 3,
            "Schedule": {
                "Name": "[toLower(concat('myschedule-',uniqueString(resourceGroup().id, deployment().name)))]",
                "Interval": 15,
                "TimeSpan": 60
            },
            "MetricsTrigger": {
                "TriggerCondition": "Consecutive",
                "Operator": "gt",
                "Value": 3
            },
            "ThrottleMinutes": 60,
            "AzNsNotification": {
                "GroupIds": [
                    "[parameters('actiongroup')]"
                ],
                "CustomEmailSubject": "Sample alert"
            }
        }
    },
    "resources": [
        {
            "name": "[concat(variables('SolutionName'), '[' ,parameters('workspacename'), ']')]",
            "location": "[parameters('workspaceRegionId')]",
            "tags": { },
            "type": "Microsoft.OperationsManagement/solutions",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Solution')]",
            "dependsOn": [
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches', parameters('workspacename'), variables('MySearch').Name)]",
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name)]",
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Name)]"
            ],
            "properties": {
                "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspacename'))]",
                "referencedResources": [
                ],
                "containedResources": [
                    "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches', parameters('workspacename'), variables('MySearch').Name)]",
                    "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name)]",
                    "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Name)]"
                ]
            },
            "plan": {
                "name": "[concat(variables('SolutionName'), '[' ,parameters('workspaceName'), ']')]",
                "Version": "[variables('SolutionVersion')]",
                "product": "[variables('ProductName')]",
                "publisher": "[variables('SolutionPublisher')]",
                "promotionCode": ""
            }
        },
        {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Search')]",
            "dependsOn": [ ],
            "tags": { },
            "properties": {
                "etag": "*",
                "query": "[variables('MySearch').query]",
                "displayName": "[variables('MySearch').displayName]",
                "category": "[variables('MySearch').category]"
            }
        },
        {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name, '/', variables('MyAlert').Schedule.Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Search')]",
            "dependsOn": [
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('MySearch').Name)]"
            ],
            "properties": {
                "etag": "*",
                "interval": "[variables('MyAlert').Schedule.Interval]",
                "queryTimeSpan": "[variables('MyAlert').Schedule.TimeSpan]",
                "enabled": true
            }
        },
        {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name, '/', variables('MyAlert').Schedule.Name, '/', variables('MyAlert').Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Search')]",
            "dependsOn": [
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('MySearch').Name, '/schedules/', variables('MyAlert').Schedule.Name)]"
            ],
            "properties": {
                "etag": "*",
                "Type": "Alert",
                "Name": "[variables('MyAlert').DisplayName]",
                "Description": "[variables('MyAlert').Description]",
                "Severity": "[variables('MyAlert').Severity]",
                "Threshold": {
                    "Operator": "[variables('MyAlert').ThresholdOperator]",
                    "Value": "[variables('MyAlert').ThresholdValue]",
                    "MetricsTrigger": {
                        "TriggerCondition": "[variables('MyAlert').MetricsTrigger.TriggerCondition]",
                        "Operator": "[variables('MyAlert').MetricsTrigger.Operator]",
                        "Value": "[variables('MyAlert').MetricsTrigger.Value]"
                    }
                },
                "Throttling": {
                    "DurationInMinutes": "[variables('MyAlert').ThrottleMinutes]"
                },
                "AzNsNotification": {
                    "GroupIds": "[variables('MyAlert').AzNsNotification.GroupIds]",
                    "CustomEmailSubject": "[variables('MyAlert').AzNsNotification.CustomEmailSubject]"
                }
            }
        }
    ]
}
```

A következő paraméter a megoldáshoz tartozó minták értékeit tartalmazza.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspacename": {
            "value": "myWorkspace"
        },
        "accountName": {
            "value": "myAccount"
        },
        "workspaceregionId": {
            "value": "East US"
        },
        "regionId": {
            "value": "East US 2"
        },
        "pricingTier": {
            "value": "Free"
        },
        "actiongroup": {
            "value": "/subscriptions/3b540246-808d-4331-99aa-917b808a9166/resourcegroups/myTestGroup/providers/microsoft.insights/actiongroups/sample"
        }
    }
}
```

## <a name="next-steps"></a>További lépések
* [Nézetek hozzáadása](solutions-resources-views.md) a felügyeleti megoldáshoz.
* Az [Automation-runbookok és egyéb erőforrásokat is hozzáadhat](solutions-resources-automation.md) a felügyeleti megoldáshoz.
