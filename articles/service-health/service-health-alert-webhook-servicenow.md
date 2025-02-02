---
title: Az Azure service health-riasztások küldése a ServiceNow-webhookok használata
description: A Szolgáltatásállapot-események, a ServiceNow-példány kapcsolatos személyre szabott értesítések küldése.
author: stephbaron
ms.author: stbaron
ms.topic: article
ms.service: service-health
ms.date: 06/10/2019
ms.openlocfilehash: e32a32e4961043e0cd967247c8c13420ca8a1969
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67067117"
---
# <a name="send-azure-service-health-alerts-with-servicenow-using-webhooks"></a>Az Azure service health-riasztások küldése a ServiceNow-webhookok használata

Ez a cikk bemutatja, hogyan integrálható az Azure service health-riasztások a servicenow segítségével egy webhook használatával. Miután beállította a ServiceNow-példány a webhook-integrációt, is számíthat riasztások keresztül a meglévő értesítési infrastruktúra Önt érintő problémái az Azure szolgáltatás. Minden alkalommal, amikor egy Azure Service Health-riasztások aktiválásáról, a ServiceNow parancsfájlalapú REST API-val egy webhookot hív meg.

## <a name="creating-a-scripted-rest-api-in-servicenow"></a>A ServiceNow parancsfájlalapú REST API létrehozásával

1.  Győződjön meg arról, hogy regisztrált-, és bejelentkezett, a [ServiceNow](https://www.servicenow.com/) fiókot.

1.  Keresse meg a **System webszolgáltatások** a ServiceNow, és válassza a szakasz **parancsfájlalapú REST API-k**.

    ![A ServiceNow "Parancsfájlalapú webszolgáltatás" című szakaszában](./media/webhook-alerts/servicenow-sws-section.png)

1.  Válassza ki **új** egy új parancsfájlalapú REST-szolgáltatás létrehozásához.
 
    ![Az "Új parancsfájlalapú REST API-t" gombra, a ServiceNow](./media/webhook-alerts/servicenow-new-button.png)

1.  Adjon hozzá egy **neve** a REST API-t és a készlet a **API-azonosító** való `azureservicehealth`.

1.  Válassza ki **elküldése**.

    ![A "REST API-beállítások" a ServiceNow](./media/webhook-alerts/servicenow-restapi-settings.png)

1.  Válassza ki a REST API hozta létre, és a **erőforrások** lapon válassza **új**.

    ![Az "erőforrás lapot" ServiceNow](./media/webhook-alerts/servicenow-resources-tab.png)

1.  **Név** az új erőforrás `event` , és módosítsa a **HTTP-metódus** való `POST`.

1.  Az a **parancsfájl** területen adja hozzá a következő JavaScript-kódot:

    >[!NOTE]
    >Frissítenie kell a `<secret>`,`<group>`, és `<email>` értékét az alábbi parancsprogram.
    >* `<secret>` egy véletlenszerű karakterlánc, például egy GUID Azonosítót kell lennie.
    >* `<group>` a ServiceNow-csoport az incidenst a hozzárendelni kívánt kell lennie.
    >* `<email>` az incidens (nem kötelező) a hozzárendelni kívánt adott személyt kell lennie.
    >

    ```javascript
    (function process( /*RESTAPIRequest*/ request, /*RESTAPIResponse*/ response) {
        var apiKey = request.queryParams['apiKey'];
        var secret = '<secret>';
        if (apiKey == secret) {
            var event = request.body.data;
            var responseBody = {};
            if (event.data.context.activityLog.operationName == 'Microsoft.ServiceHealth/incident/action') {
                var inc = new GlideRecord('incident');
                var incidentExists = false;
                inc.addQuery('number', event.data.context.activityLog.properties.trackingId);
                inc.query();
                if (inc.hasNext()) {
                    incidentExists = true;
                    inc.next();
                } else {
                    inc.initialize();
                }
                var short_description = "Azure Service Health";
                if (event.data.context.activityLog.properties.incidentType == "Incident") {
                    short_description += " - Service Issue - ";
                } else if (event.data.context.activityLog.properties.incidentType == "Maintenance") {
                    short_description += " - Planned Maintenance - ";
                } else if (event.data.context.activityLog.properties.incidentType == "Informational" || event.data.context.activityLog.properties.incidentType == "ActionRequired") {
                    short_description += " - Health Advisory - ";
                }
                short_description += event.data.context.activityLog.properties.title;
                inc.short_description = short_description;
                inc.description = event.data.context.activityLog.properties.communication;
                inc.work_notes = "Impacted subscription: " + event.data.context.activityLog.subscriptionId;
                if (incidentExists) {
                    if (event.data.context.activityLog.properties.stage == 'Active') {
                        inc.state = 2;
                    } else if (event.data.context.activityLog.properties.stage == 'Resolved') {
                        inc.state = 6;
                    } else if (event.data.context.activityLog.properties.stage == 'Closed') {
                        inc.state = 7;
                    }
                    inc.update();
                    responseBody.message = "Incident updated.";
                } else {
                    inc.number = event.data.context.activityLog.properties.trackingId;
                    inc.state = 1;
                    inc.impact = 2;
                    inc.urgency = 2;
                    inc.priority = 2;
                    inc.assigned_to = '<email>';
                    inc.assignment_group.setDisplayValue('<group>');
                    var subscriptionId = event.data.context.activityLog.subscriptionId;
                    var comments = "Azure portal Link: https://app.azure.com/h";
                    comments += "/" + event.data.context.activityLog.properties.trackingId;
                    comments += "/" + subscriptionId.substring(0, 3) + subscriptionId.slice(-3);
                    var impactedServices = JSON.parse(event.data.context.activityLog.properties.impactedServices);
                    var impactedServicesFormatted = "";
                    for (var i = 0; i < impactedServices.length; i++) {
                        impactedServicesFormatted += impactedServices[i].ServiceName + ": ";
                        for (var j = 0; j < impactedServices[i].ImpactedRegions.length; j++) {
                            if (j != 0) {
                                impactedServicesFormatted += ", ";
                            }
                            impactedServicesFormatted += impactedServices[i].ImpactedRegions[j].RegionName;
                        }

                        impactedServicesFormatted += "\n";

                    }
                    comments += "\n\nImpacted Services:\n" + impactedServicesFormatted;
                    inc.comments = comments;
                    inc.insert();
                    responseBody.message = "Incident created.";
                }
            } else {
                responseBody.message = "Hello from the other side!";
            }
            response.setBody(responseBody);
        } else {
            var unauthorized = new sn_ws_err.ServiceError();
            unauthorized.setStatus(401);
            unauthorized.setMessage('Invalid apiKey');
            response.setError(unauthorized);
        }
    })(request, response);
    ```

1.  A biztonság lapon törölje a jelet **hitelesítést igényel** válassza **küldés**. A `<secret>` , set inkább védi az API-t.

    ![A ServiceNow "Szükséges hitelesítés" jelölőnégyzetet](./media/webhook-alerts/servicenow-resource-settings.png)

1.  Vissza a REST API-k parancsfájlalapú területen, keresse meg a **alap API elérési út** az új REST API-hoz:

     ![A "Base API elérési út" a ServiceNow](./media/webhook-alerts/servicenow-base-api-path.png)

1.  A teljes integrációs URL-Címének hasonlóan néz ki:
        
         https://<yourInstanceName>.service-now.com/<baseApiPath>?apiKey=<secret>


## <a name="create-an-alert-using-servicenow-in-the-azure-portal"></a>Hozzon létre egy riasztást, a ServiceNow segítségével az Azure Portalon
### <a name="for-a-new-action-group"></a>Az új műveletcsoport:
1. Végezze el az 1 – 8 [Ez a cikk](../azure-monitor/platform/alerts-activity-log-service-notifications.md) riasztás az új műveletcsoport létrehozásához.

1. Adja meg a listában, **műveletek**:

    a. **Művelet típusa:** *Webhook*

    b. **Részletek:** A ServiceNow **integrációs URL-Címének** , korábban mentett.

    c. **név:** A Webhook nevét, alias vagy azonosítója.

1. Válassza ki **mentése** Ha ezzel elkészült, a riasztás létrehozásához.

### <a name="for-an-existing-action-group"></a>A meglévő műveletcsoport:
1. Az a [az Azure portal](https://portal.azure.com/)válassza **figyelő**.

1. Az a **beállítások** szakaszban jelölje be **Műveletcsoportok**.

1. Keresse meg és válassza ki a szerkeszteni kívánt művelet csoportot.

1. Adja hozzá a listához, **műveletek**:

    a. **Művelet típusa:** *Webhook*

    b. **Részletek:** A ServiceNow **integrációs URL-Címének** , korábban mentett.

    c. **név:** A Webhook nevét, alias vagy azonosítója.

1. Válassza ki **mentése** végeztével a műveletcsoport frissítéséhez.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Egy HTTP POST kérelem használatával a webhook-integráció tesztelése
1. Hozza létre a service health hasznos adatokat szeretne küldeni. Egy példa service health webhook hasznos adatai címen található [Webhookok az Azure-tevékenységi naplóriasztások](../azure-monitor/platform/activity-log-alerts-webhook.md).

1. HTTP POST-kérelmet a következőképpen hozhat létre:

    ```
    POST        https://<yourInstanceName>.service-now.com/<baseApiPath>?apiKey=<secret>

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. Ekkor kap egy `200 OK` válasz a következő üzenettel: "Incidens létrehozása."

1. Lépjen a [ServiceNow](https://www.servicenow.com/) annak ellenőrzéséhez, hogy az integrációs sikeres volt-e beállítva.

## <a name="next-steps"></a>További lépések
- Ismerje meg, hogyan [konfigurálása webhook-értesítésekkel meglévő probléma felügyeleti rendszerek](service-health-alert-webhook-guide.md).
- Tekintse át a [tevékenység log riasztási webhookséma](../azure-monitor/platform/activity-log-alerts-webhook.md). 
- Ismerje meg [szolgáltatás állapotára vonatkozó értesítések](../azure-monitor/platform/service-notifications.md).
- Tudjon meg többet [Műveletcsoportok](../azure-monitor/platform/action-groups.md).