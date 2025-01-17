---
title: Felügyelt identitások használata az Azure API Management |} A Microsoft Docs
description: Az API Management szolgáltatásban felügyelt identitás használata
services: api-management
documentationcenter: ''
author: miaojiang
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 10/18/2017
ms.author: apimpm
ms.openlocfilehash: 49576b805e6c6d01340e663bfb5d8e9013917625
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67461609"
---
# <a name="use-managed-identities-in-azure-api-management"></a>Felügyelt identitások használata az Azure API Management

Ez a cikk bemutatja, hogyan hozhat létre egy felügyelt identitás létre API Management szolgáltatáspéldányt, és hogyan egyéb erőforrásainak elérésére. Azure Active Directory (Azure AD) által létrehozott egy felügyelt identitás lehetővé teszi, hogy az API Management-példány egyszerűen és biztonságosan más Azure AD által védett erőforrások, például az Azure Key Vault eléréséhez. Ezt az identitást az Azure által felügyelt és nem igényli, üzembe helyezése és titkos kulcsok elforgatása. Felügyelt identitások kapcsolatos további információkért lásd: [Mi az Azure-erőforrások felügyelt identitások](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="create-a-managed-identity-for-an-api-management-instance"></a>Hozzon létre egy felügyelt identitás, az API Management-példány

### <a name="using-the-azure-portal"></a>Az Azure Portal használata

A portálon egy felügyelt identitás beállítása esetén lesz először a szokásos módon API Management szolgáltatáspéldány létrehozása, és ezután engedélyezze a szolgáltatást.

1. API Management-példány létrehozása a portálon, ahogy azt szokásosan tenné. Keresse meg azt a portálon.
2. Válassza ki **felügyelt szolgáltatásidentitások**.
3. Regisztráljon az Azure Active Directoryval váltson be. Kattintson a Mentés gombra.

![Az MSI engedélyezéséhez](./media/api-management-msi/enable-msi.png)

### <a name="using-the-azure-resource-manager-template"></a>Az Azure Resource Manager-sablon használatával

API Management-példány létrehozhat egy identitással többek között a következő tulajdonság az erőforrás-definícióban:

```json
"identity" : {
    "type" : "SystemAssigned"
}
```

Ez jelzi az Azure hozhat létre és kezelhet az identitás, az API Management-példány számára.

Ha például egy teljes körű Azure Resource Manager-sablon előfordulhat, hogy a következőhöz hasonló:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "0.9.0.0",
    "resources": [{
        "apiVersion": "2017-03-01",
        "name": "contoso",
        "type": "Microsoft.ApiManagement/service",
        "location": "[resourceGroup().location]",
        "tags": {},
        "sku": {
            "name": "Developer",
            "capacity": "1"
        },
        "properties": {
            "publisherEmail": "admin@contoso.com",
            "publisherName": "Contoso"
        },
        "identity": {
            "type": "systemAssigned"
        }
    }]
}
```
## <a name="use-the-managed-service-identity-to-access-other-resources"></a>Egyéb erőforrásainak elérésére a felügyeltszolgáltatás-identitás használata

> [!NOTE]
> Jelenleg a felügyelt identitásokból használható tanúsítványok beszerzése az Azure Key vault az egyéni tartománynevek az API Management. További forgatókönyvek hamarosan támogatott lesz.
>
>


### <a name="obtain-a-certificate-from-azure-key-vault"></a>Tanúsítvány beszerzése az Azure Key vault

#### <a name="prerequisites"></a>Előfeltételek
1. A Key Vault, a pfx-tanúsítványt tartalmazó Azure-előfizetéshez és az API Management szolgáltatás ugyanabban az erőforráscsoportban kell lennie. Ez a követelmény az Azure Resource Manager-sablon.
2. A titkos kulcs tartalom típusúnak kell lennie *application/x-pkcs12*. A következő parancsfájl használatával töltse fel a tanúsítványt:

```powershell
$pfxFilePath = "PFX_CERTIFICATE_FILE_PATH" # Change this path 
$pwd = "PFX_CERTIFICATE_PASSWORD" # Change this password 
$flag = [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::Exportable 
$collection = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2Collection 
$collection.Import($pfxFilePath, $pwd, $flag) 
$pkcs12ContentType = [System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12 
$clearBytes = $collection.Export($pkcs12ContentType) 
$fileContentEncoded = [System.Convert]::ToBase64String($clearBytes) 
$secret = ConvertTo-SecureString -String $fileContentEncoded -AsPlainText –Force 
$secretContentType = 'application/x-pkcs12' 
Set-AzureKeyVaultSecret -VaultName KEY_VAULT_NAME -Name KEY_VAULT_SECRET_NAME -SecretValue $Secret -ContentType $secretContentType
```

> [!Important]
> Ha az objektum verziója, a tanúsítvány nincs megadva, az API Management automatikusan beszerzése az újabb verzióra, a tanúsítvány a Key Vault való feltöltésük után.

Az alábbi példa bemutatja egy Azure Resource Manager-sablon, amely tartalmazza az alábbi lépéseket:

1. Hozzon létre API Management-példány egy felügyelt identitás.
2. A hozzáférési szabályzatok az Azure Key Vault-példány frissítése, és lehetővé teszi az API Management szolgáltatáspéldány titkos kódok lekérését.
3. Az API Management-példány egy tanúsítvánnyal történő egyéni tartománynév beállítása a Key Vault-példány a frissítés.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publisherEmail": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "The email address of the owner of the service"
            }
        },
        "publisherName": {
            "type": "string",
            "defaultValue": "Contoso",
            "minLength": 1,
            "metadata": {
                "description": "The name of the owner of the service"
            }
        },
        "sku": {
            "type": "string",
            "allowedValues": ["Developer",
            "Standard",
            "Premium"],
            "defaultValue": "Developer",
            "metadata": {
                "description": "The pricing tier of this API Management service"
            }
        },
        "skuCount": {
            "type": "int",
            "defaultValue": 1,
            "metadata": {
                "description": "The instance size of this API Management service."
            }
        },
        "keyVaultName": {
            "type": "string",
            "metadata": {
                "description": "Name of the vault"
            }
        },
        "proxyCustomHostname1": {
            "type": "string",
            "metadata": {
                "description": "Proxy Custom hostname."
            }
        },
        "keyVaultIdToCertificate": {
            "type": "string",
            "metadata": {
                "description": "Reference to the KeyVault certificate. https://contoso.vault.azure.net/secrets/contosogatewaycertificate."
            }
        }
    },
    "variables": {
        "apiManagementServiceName": "[concat('apiservice', uniqueString(resourceGroup().id))]",
        "apimServiceIdentityResourceId": "[concat(resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName')),'/providers/Microsoft.ManagedIdentity/Identities/default')]"
    },
    "resources": [{
        "apiVersion": "2017-03-01",
        "name": "[variables('apiManagementServiceName')]",
        "type": "Microsoft.ApiManagement/service",
        "location": "[resourceGroup().location]",
        "tags": {
        },
        "sku": {
            "name": "[parameters('sku')]",
            "capacity": "[parameters('skuCount')]"
        },
        "properties": {
            "publisherEmail": "[parameters('publisherEmail')]",
            "publisherName": "[parameters('publisherName')]"
        },
        "identity": {
            "type": "systemAssigned"
        }
    },
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2015-06-01",
        "dependsOn": [
            "[resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName'))]"
        ],
        "properties": {
            "accessPolicies": [{
                "tenantId": "[reference(variables('apimServiceIdentityResourceId'), '2015-08-31-PREVIEW').tenantId]",
                "objectId": "[reference(variables('apimServiceIdentityResourceId'), '2015-08-31-PREVIEW').principalId]",
                "permissions": {
                    "secrets": ["get"]
                }
            }]
        }
    },
    {
        "apiVersion": "2017-05-10",
        "name": "apimWithKeyVault",
        "type": "Microsoft.Resources/deployments",
        "dependsOn": [
        "[resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName'))]"
        ],
        "properties": {
            "mode": "incremental",
            "templateLink": {
                "uri": "https://raw.githubusercontent.com/solankisamir/arm-templates/master/basicapim.keyvault.json",
                "contentVersion": "1.0.0.0"
            },
            "parameters": {
                "publisherEmail": { "value": "[parameters('publisherEmail')]"},
                "publisherName": { "value": "[parameters('publisherName')]"},
                "sku": { "value": "[parameters('sku')]"},
                "skuCount": { "value": "[parameters('skuCount')]"},
                "proxyCustomHostname1": {"value" : "[parameters('proxyCustomHostname1')]"},
                "keyVaultIdToCertificate": {"value" : "[parameters('keyVaultIdToCertificate')]"}
            }
        }
    }]
}
```

## <a name="next-steps"></a>További lépések

További információ az Azure-erőforrások felügyelt identitások:

* [Mi az Azure-erőforrások felügyelt identitások](../active-directory/managed-identities-azure-resources/overview.md)
* [Az Azure Resource Manager-sablonok](https://github.com/Azure/azure-quickstart-templates)
* [A házirend a felügyelt identitás a hitelesítéshez](./api-management-authentication-policies.md#ManagedIdentity)
