---
title: Az Azure Event Grid eseménykezelők
description: Támogatott eseménykezelők ismerteti az Azure Event Gridhez
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2019
ms.author: spelluru
ms.openlocfilehash: 6093e1017af2fb8c54eaf1c3192f937172567982
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67080559"
---
# <a name="event-handlers-in-azure-event-grid"></a>Az Azure Event Grid eseménykezelők

Eseménykezelőt az a hely, ahol a rendszer. A kezelő néhány további művelet feldolgozni az eseményt vesz igénybe. Több Azure-szolgáltatások automatikusan megtörténik az események kezeléséhez. Bármilyen WebHook események kezeléséhez is használható. A WebHook nem kell-e üzemeltetni, arra zpracovávat události. Event Grid csak a HTTPS WebHook-végpontokat támogatja.

Ez a cikk tartalmakra mutató hivatkozásokat biztosít minden eseménykezelő.

## <a name="azure-automation"></a>Azure Automation

Azure Automation segítségével a runbookok automatizált eseményeinek feldolgozása.

|Beosztás  |Leírás  |
|---------|---------|
|[Oktatóanyag: Az Event Grid és a Microsoft Teams az Azure Automation](ensure-tags-exists-on-new-virtual-machines.md) |Hozzon létre egy virtuális gépet, amely eseményt küld. Az esemény aktiválása a címkéket a virtuális gép, és a egy Microsoft Teams-csatornához küldött üzenet aktivál egy Automation-runbook. |

## <a name="azure-functions"></a>Azure Functions

Az Azure Functions használata kiszolgáló nélküli által kiváltott kódfuttatáshoz.

Ha az Azure Functionst használja kezelőként, használja az Event Grid-triggereket az általános HTTP-triggerek helyett. Az Event Grid automatikusan érvényesíti az Event Grid függvény eseményindítóit. Általános HTTP-eseményindítók esetén meg kell valósítani az [érvényesítési választ](security-authentication.md#webhook-event-delivery).

|Beosztás  |Leírás  |
|---------|---------|
| [Event Grid-trigger az Azure Functions szolgáltatáshoz](../azure-functions/functions-bindings-event-grid.md) | Az Event Grid eseményindító használata a funkciók áttekintése. |
| [Oktatóanyag: az Event Grid használatával feltöltött képek átméretezésének automatizálása](resize-images-on-storage-blob-upload-event.md) | Felhasználók webalkalmazás keresztül képek feltöltéséhez a storage-fiókot. Amikor létrejön egy storage-blobból, az Event Grid eseményt küld az a függvényalkalmazást, amely átméretezi a feltöltött kép. |
| [Oktatóanyag: big Data típusú adatok streamelése adattárházba](event-grid-event-hubs-integration.md) | Az Event Hubs rögzítési fájlt hoz létre, amikor az Event Grid eseményt küld egy függvényalkalmazáshoz. Az alkalmazás lekéri a rögzítési fájlt, és egy data warehouse-bA migrálja az adatokat. |
| [Oktatóanyag: Azure Service Bus – Azure Event Grid integráció példák](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid üzeneteket küld a Service Bus-témakörbe, alkalmazás és a logikai alkalmazás működéséhez. |

## <a name="event-hubs"></a>Event Hubs

Az Event Hubs akkor használja, ha a megoldás gyorsabb ütemben fel tud dolgozni az események beolvassa az eseményeket. Az alkalmazás dolgozza fel az Event hubsból, saját ütemezés. A bejövő események kezeléséhez a Eseményfeldolgozási skálázhatja.

Az Event Hubs egy eseményforrás vagy egy eseménykezelő működhet. A következő cikk az Event Hubs használata a kezelő jeleníti meg.

|Beosztás  |Leírás  |
|---------|---------|
| [Gyors útmutató: egyéni események átirányítása az Azure Event Hubs az Azure CLI-vel és az Event Griddel](custom-event-to-eventhub.md) | Egyéni eseményt küld egy event hubot, az alkalmazások feldolgozását. |
| [Resource Manager-sablon: egyéni témakör és az Event Hubs-végpont](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Resource Manager-sablonnal, amely egy egyéni témakör-előfizetést hoz létre. Az Azure Event Hubs küld eseményeket. |

Példák az Event hubs forrásként: [az Event Hubs-forrás](event-sources.md#event-hubs).

## <a name="hybrid-connections"></a>Hibrid kapcsolatok

Azure Relay hibrid kapcsolatok használatával az események küldése az alkalmazásokat, amelyek egy vállalati hálózaton belül, és nem rendelkezik egy nyilvánosan elérhető végpontot.

|Beosztás  |Leírás  |
|---------|---------|
| [Oktatóanyag: a hibrid kapcsolat események küldése](custom-event-to-hybrid-connection.md) | Egyéni eseményt küld egy meglévő, a hibrid kapcsolat feldolgozása egy figyelő alkalmazás. |

## <a name="logic-apps"></a>Logic Apps

A Logic Apps segítségével automatizálhatja üzleti folyamatait eseményekre reagálni.

|Beosztás  |Leírás  |
|---------|---------|
| [Oktatóanyag: az Azure Event Grid és a Logic Apps a virtuális gépek módosításainak monitorozása](monitor-virtual-machine-changes-event-grid-logic-app.md) | Egy logikai alkalmazást egy virtuális gép módosításainak figyeli, és ezeket a módosításokat kapcsolatos e-mailt küld. |
| [Oktatóanyag: küldése e-mailes értesítést az Azure IoT Hub-események Logic Apps használatával](publish-iot-hub-events-to-logic-apps.md) | Egy logikai alkalmazás küld értesítő e-mailt, minden alkalommal, amikor egy eszköz az IoT hubhoz való hozzáadása. |
| [Oktatóanyag: Azure Service Bus – Azure Event Grid integráció példák](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid üzeneteket küld a Service Bus-témakörbe, alkalmazás és a logikai alkalmazás működéséhez. |

## <a name="service-bus-queue-preview"></a>Service Bus-üzenetsor (előzetes verzió)

Eseménykezelő, Service Bus használatával az események irányítása az Event Grid közvetlenül a Service Bus-üzenetsorok pufferelés vagy parancs és vezérlés helyzetekben a vállalati alkalmazásokban való használatra. Az előzetes verzió a Service Bus-témakörök és a munkamenetek nem működik, de a Service Bus-üzenetsorok, minden szinten működik.

Kérjük, vegye figyelembe, miközben Service Bus, a kezelő nyilvános előzetes verzióban érhető el az telepítenie kell a parancssori felület vagy PowerShell-bővítmény használata azokat, esemény-előfizetések létrehozása.

### <a name="install-extension-for-azure-cli"></a>Azure CLI-hez a bővítmény telepítése

Azure CLI-vel, szüksége lesz a [Event Grid-bővítmény](/cli/azure/azure-cli-extensions-list).

A [cloud Shell](/azure/cloud-shell/quickstart):

* Ha korábban telepítette a bővítményt, frissítse az `az extension update -n eventgrid`.
* Ha korábban még nem telepítette a bővítményt, telepítse a `az extension add -n eventgrid`.

Helyi telepítéséhez:

1. [Az Azure CLI telepítése](/cli/azure/install-azure-cli). Győződjön meg arról, hogy rendelkezik-e a legújabb verziót az ellenőrzésével `az --version`.
1. Távolítsa el a bővítményt a korábbi verzióinak `az extension remove -n eventgrid`.
1. Telepítse a `eventgrid` bővítmény `az extension add -n eventgrid`.

### <a name="install-module-for-powershell"></a>PowerShell-modul telepítése

PowerShell esetén van szükség a [AzureRM.EventGrid modul](https://www.powershellgallery.com/packages/AzureRM.EventGrid/0.4.1-preview).

A [cloud Shell](/azure/cloud-shell/quickstart-powershell):

* Telepítse a modult a következővel `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`.

Helyi telepítéséhez:

1. Nyissa meg a PowerShell-konzolt rendszergazdaként.
1. Telepítse a modult a következővel `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`.

Ha a `-AllowPrerelease` paraméter nem érhető el, kövesse az alábbi lépéseket:

1. Futtassa az `Install-Module PowerShellGet -Force` parancsot.
1. Futtassa az `Update-Module PowerShellGet` parancsot.
1. Zárja be a PowerShell-konzolon.
1. Indítsa újra a PowerShell rendszergazdaként.
1. A modul telepítése `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`.

### <a name="using-cli-to-add-a-service-bus-handler"></a>Parancssori felület használatával a Service Bus-kezelő hozzáadása

Azure CLI-hez az alábbi példa feliratkozik, és a egy Event Grid-témakör csatlakozik a Service Bus-üzenetsorba:

```azurecli-interactive
# If you haven't already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebusqueue \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/queues/queue1
```

## <a name="queue-storage"></a>Queue Storage

A Queue storage használatával kapja, le kell kérnie. A Queue storage használatával, ha egy hosszú ideig futó folyamatot, amely a túl sok ideig nem válaszolt. Eseményeket küld a Queue storage, az alkalmazás kérheti le és dolgozza fel a saját ütemezés szerint.

|Beosztás  |Leírás  |
|---------|---------|
| [Gyors útmutató: egyéni események átirányítása az Azure CLI és az Event Grid Azure Queue storage](custom-event-to-queue-storage.md) | Ismerteti, hogyan lehet egyéni eseményeket küldeni egy Queue Storage. |

## <a name="webhooks"></a>Webhookok

Webhookok használata a eseményekre reagáló, testre szabható végpontok.

|Beosztás  |Leírás  |
|---------|---------|
| Gyors útmutató: hozzon létre, és az egyéni események – útvonal [Azure CLI-vel](custom-event-quickstart.md), [PowerShell](custom-event-quickstart-powershell.md), és [portál](custom-event-quickstart-portal.md). | Bemutatja, hogyan küldhet egyéni eseményeket egy Webhookot. |
| Gyors útmutató: Blob storage-események átirányítása egyéni webes végpontra az - [Azure CLI-vel](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json), [PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json), és [portál](blob-event-quickstart-portal.md). | Bemutatja, hogyan blob storage-események küldéséhez egy Webhookot. |
| [Gyors útmutató: a tároló beállításjegyzék események küldése](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Bemutatja a tároló-beállításjegyzék események küldése az Azure CLI használatával. |
| [Áttekintés: a HTTP-végponton események fogadása](receive-events.md) | Események fogadása az esemény-előfizetés, és fogadni, és deszerializálni az eseményeket egy HTTP-végpontot ellenőrzését ismerteti. |

## <a name="next-steps"></a>További lépések

* Az Event Grid megismeréséhez tekintse meg [az Event Grid bevezetőjét](overview.md).
* Tekintse meg a gyors kezdéshez Event Grid használatával [az Azure Event Griddel egyéni események létrehozása és útvonal](custom-event-quickstart.md).
