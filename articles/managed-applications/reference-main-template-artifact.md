---
title: Az Azure felügyelt alkalmazás telepítési sablonjának referenciája
description: Ez a cikk az üzembe helyezési sablon összetevőre mutató hivatkozás.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 07/11/2019
ms.openlocfilehash: fe2517a8cc2ddee6623f88b2ae5e46c9e5319567
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68336490"
---
# <a name="reference-deployment-template-artifact"></a>Leírások: Telepítési sablon összetevő

Ez a cikk a Azure Managed Applications *mainTemplate. JSON* összetevőjét ismerteti. További információ a központi telepítési sablon létrehozásáról: [Azure Resource Manager sablonok](../azure-resource-manager/resource-group-authoring-templates.md).

## <a name="deployment-template"></a>Üzembehelyezési sablon

A következő JSON a Azure Managed Applications *mainTemplate. JSON* fájljának példáját mutatja be:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "eastus",
      "allowedValues": [
        "australiaeast",
        "eastus",
        "westeurope"
      ],
      "metadata": {
        "description": "Location for the resources."
      }
    },
    "funcname": {
      "type": "string",
      "metadata": {
        "description": "Name of the Azure Function that hosts the code. Must be globally unique"
      },
      "defaultValue": ""
    },
    "storageName": {
      "type": "string",
      "metadata": {
        "description": "Name of the storage account that hosts the function. Must be globally unique. The field can contain only lowercase letters and numbers. Name must be between 3 and 24 characters"
      },
      "defaultValue": ""
    },
    "zipFileBlobUri": {
      "type": "string",
      "defaultValue": "https://github.com/Azure/azure-quickstart-templates/tree/master/101-custom-rp-with-function/artifacts/functionzip/functionpackage.zip",
      "metadata": {
        "description": "The Uri to the uploaded function zip file"
      }
    }
  },
  "variables": {
    "customrpApiversion": "2018-09-01-preview",
    "customProviderName": "public",
    "serverFarmName": "functionPlan"
  },
  "resources": [
    {
      "type": "Microsoft.Web/serverfarms",
      "apiVersion": "2016-09-01",
      "name": "[variables('serverFarmName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Y1",
        "tier": "Dynamic",
        "size": "Y1",
        "family": "Y",
        "capacity": 0
      },
      "kind": "functionapp",
      "properties": {
        "name": "[variables('serverFarmName')]",
        "perSiteScaling": false,
        "reserved": false,
        "targetWorkerCount": 0,
        "targetWorkerSizeId": 0
      }
    },
    {
      "type": "Microsoft.Web/sites",
      "kind": "functionapp",
      "name": "[parameters('funcname')]",
      "apiVersion": "2018-02-01",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageName'))]",
        "[resourceId('Microsoft.Web/serverfarms', variables('serverFarmName'))]"
      ],
      "identity": {
        "type": "SystemAssigned"
      },
      "properties": {
        "name": "[parameters('funcname')]",
        "siteConfig": {
          "appSettings": [
            {
              "name": "AzureWebJobsDashboard",
              "value": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageName'),';AccountKey=',listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageName')), '2015-05-01-preview').key1)]"
            },
            {
              "name": "AzureWebJobsStorage",
              "value": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageName'),';AccountKey=',listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageName')), '2015-05-01-preview').key1)]"
            },
            {
              "name": "FUNCTIONS_EXTENSION_VERSION",
              "value": "~2"
            },
            {
              "name": "AzureWebJobsSecretStorageType",
              "value": "Files"
            },
            {
              "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
              "value": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageName'),';AccountKey=',listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageName')), '2015-05-01-preview').key1)]"
            },
            {
              "name": "WEBSITE_CONTENTSHARE",
              "value": "[concat(toLower(parameters('funcname')), 'b86e')]"
            },
            {
              "name": "WEBSITE_NODE_DEFAULT_VERSION",
              "value": "6.5.0"
            },
            {
              "name": "WEBSITE_RUN_FROM_PACKAGE",
              "value": "[parameters('zipFileBlobUri')]"
            }
          ]
        },
        "clientAffinityEnabled": false,
        "reserved": false,
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('serverFarmName'))]"
      }
    },
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[parameters('storageName')]",
      "apiVersion": "2018-02-01",
      "kind": "StorageV2",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard_LRS"
      }
    },
    {
      "apiVersion": "[variables('customrpApiversion')]",
      "type": "Microsoft.CustomProviders/resourceProviders",
      "name": "[variables('customProviderName')]",
      "location": "[parameters('location')]",
      "properties": {
        "actions": [
          {
            "name": "ping",
            "routingType": "Proxy",
            "endpoint": "[listSecrets(resourceId('Microsoft.Web/sites/functions', parameters('funcname'), 'HttpTrigger1'), '2018-02-01').trigger_url]"
          },
          {
            "name": "users/contextAction",
            "routingType": "Proxy",
            "endpoint": "[listSecrets(resourceId('Microsoft.Web/sites/functions', parameters('funcname'), 'HttpTrigger1'), '2018-02-01').trigger_url]"
          }
        ],
        "resourceTypes": [
          {
            "name": "users",
            "routingType": "Proxy,Cache",
            "endpoint": "[listSecrets(resourceId('Microsoft.Web/sites/functions', parameters('funcname'), 'HttpTrigger1'), '2018-02-01').trigger_url]"
          }
        ]
      },
      "dependsOn": [
        "[concat('Microsoft.Web/sites/',parameters('funcname'))]"
      ]
    }
  ],
  "outputs": {}
}
```

## <a name="next-steps"></a>További lépések

- [Oktatóanyag: Felügyelt alkalmazás létrehozása egyéni műveletekkel és erőforrásokkal](tutorial-create-managed-app-with-custom-provider.md)
- [Referencia Felhasználói felület elemeinek összetevője](reference-createuidefinition-artifact.md)
- [Referencia Definíciós összetevő megtekintése](reference-view-definition-artifact.md)