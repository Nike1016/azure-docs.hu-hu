---
title: Minta - naplózási Key vault-Kulcstartók nincs virtuális hálózat végpontok
description: A szabályzatdefiníció-minta naplózza a Key Vault-tárolók nem virtuális hálózati szolgáltatásvégpontokkal rendelkező példányok észleléséhez.
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 01/26/2019
ms.author: dacoulte
ms.openlocfilehash: bc5ce4a6a2e52ed8d21de8db8da1f815293b61f7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60506994"
---
# <a name="sample---key-vault-vaults-with-no-virtual-network-endpoints"></a>Minta – nincs virtuális hálózat végpontokkal rendelkező Key Vault-tárolók

Ez a szabályzat naplózza a Key Vault tároló, amely nem virtuális hálózati végpontokat. Használatával érvényesíti a biztonsági követelményeknek. További információkért lásd: [virtuális hálózati Szolgáltatásvégpontok a Key vaultban](../../../key-vault/key-vault-overview-vnet-service-endpoints.md)

Ennek a minta szabályzatnak az üzembe helyezéséhez a következőre lesz szükség:

- Az [Azure Portal](#azure-portal)
- [Azure PowerShell](#azure-powershell)
- [Azure CLI](#azure-cli)
- [REST API](#rest-api)

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-policy"></a>Minta szabályzat

### <a name="policy-definition"></a>Szabályzatdefiníció

A REST API által, az „Üzembe helyezés az Azure-ban” gombok által, valamint a portálon manuálisan használt teljes összeállított JSON szabályzatdefiníció.

[!code-json[full](../../../../policy-templates/samples/KeyVault/audit-keyvault-vnet-rules/azurepolicy.json "KeyVault vnet rules")]

> [!NOTE]
> Ha manuálisan hoz létre a portálon szabályzatot, használja a fenti **properties.parameters** és **properties.policyRule** részeket. A két szakaszt kapcsos zárójelekkel `{}` összekapcsolva érvényes JSON-t hozhat létre.

### <a name="policy-rules"></a>Szabályzat szabályai

A szabályzat szabályait Azure CLI és Azure PowerShell segítségével meghatározó JSON.

[!code-json[rule](../../../../policy-templates/samples/KeyVault/audit-keyvault-vnet-rules/azurepolicy.rules.json "Policy rules (JSON)")]

### <a name="policy-parameters"></a>Szabályzatparaméterek

A szabályzatdefiníció-minta nem tartozik paraméter definiálva.

## <a name="azure-portal"></a>Azure Portal

[![A házirend-minta üzembe helyezése Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FKeyVault%2Faudit-keyvault-vnet-rules%2Fazurepolicy.json)
[![a házirend-minta üzembe helyezése az Azure-beli államigazgatás –](https://docs.microsoft.com/azure/governance/policy/media/deploy/deployGovbutton.png)](https://portal.azure.us/#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FKeyVault%2Faudit-keyvault-vnet-rules%2Fazurepolicy.json)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

### <a name="deploy-with-azure-powershell"></a>Üzembe helyezés az Azure PowerShell-lel

```azurepowershell-interactive
# Create the Policy Definition (Subscription scope)
$definition = New-AzPolicyDefinition -Name "audit-keyvault-vnet-rules" -DisplayName "Audit if Key Vault has no virtual network rules" -description "Audits Key Vault vaults if they do not have virtual network service endpoints set up. More information on virtual network service endpoints in Key Vault is available here: https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints" -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/KeyVault/audit-keyvault-vnet-rules/azurepolicy.rules.json' -Mode Indexed

# Set the scope to a resource group; may also be a subscription or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Create the Policy Assignment
$assignment = New-AzPolicyAssignment -Name 'audit-keyvault-vnet-rules-assignment' -DisplayName 'Audit Key Vault Assignment' -Scope $scope.ResourceId -PolicyDefinition $definition
```

### <a name="remove-with-azure-powershell"></a>Eltávolítás az Azure PowerShell-lel

Az előző hozzárendelés és definíció eltávolításához futtassa az alábbi parancsokat:

```azurepowershell-interactive
# Remove the Policy Assignment
Remove-AzPolicyAssignment -Id $assignment.ResourceId

# Remove the Policy Definition
Remove-AzPolicyDefinition -Id $definition.ResourceId
```

### <a name="azure-powershell-explanation"></a>Azure PowerShell magyarázat

Az üzembe helyezési és eltávolítási szkriptek a következő parancsokat használják. Az alábbi táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik:

| Parancs | Megjegyzések |
|---|---|
| [New-AzPolicyDefinition](/powershell/module/az.resources/New-Azpolicydefinition) | Létrehoz egy új Azure Policy-definíciót. |
| [Get-AzResourceGroup](/powershell/module/az.resources/Get-Azresourcegroup) | Egyetlen erőforráscsoportot kér le. |
| [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) | Létrehoz egy új Azure Policy-hozzárendelést. Ebben a példában adunk hozzá egy definíciót, de használhat egy kezdeményezést is. |
| [Remove-AzPolicyAssignment](/powershell/module/az.resources/Remove-Azpolicyassignment) | Eltávolít egy létező Azure Policy-hozzárendelést. |
| [Remove-AzPolicyDefinition](/powershell/module/az.resources/Remove-Azpolicydefinition) | Eltávolít egy létező Azure Policy-definíciót. |

## <a name="azure-cli"></a>Azure CLI

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

### <a name="deploy-with-azure-cli"></a>Üzembe helyezés az Azure CLI-vel

```azurecli-interactive
# Create the Policy Definition (Subscription scope)
definition=$(az policy definition create --name 'audit-keyvault-vnet-rules' --display-name 'Audit if Key Vault has no virtual network rules' --description 'Audits Key Vault vaults if they do not have virtual network service endpoints set up. More information on virtual network service endpoints in Key Vault is available here: https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/KeyVault/audit-keyvault-vnet-rules/azurepolicy.rules.json' --mode Indexed)

# Set the scope to a resource group; may also be a subscription or management group
scope=$(az group show --name 'YourResourceGroup')

# Create the Policy Assignment
assignment=$(az policy assignment create --name 'audit-keyvault-vnet-rules-assignment' --display-name 'Audit Key Vault Assignment' --scope `echo $scope | jq '.id' -r` --policy `echo $definition | jq '.name' -r`)
```

### <a name="remove-with-azure-cli"></a>Eltávolítás az Azure CLI segítségével

Az előző hozzárendelés és definíció eltávolításához futtassa az alábbi parancsokat:

```azurecli-interactive
# Remove the Policy Assignment
az policy assignment delete --name `echo $assignment | jq '.name' -r`

# Remove the Policy Definition
az policy definition delete --name `echo $definition | jq '.name' -r`
```

### <a name="azure-cli-explanation"></a>Azure CLI magyarázat

| Parancs | Megjegyzések |
|---|---|
| [az policy definition create](/cli/azure/policy/definition?view=azure-cli-latest#az-policy-definition-create) | Létrehoz egy új Azure Policy-definíciót. |
| [az group show](/cli/azure/group?view=azure-cli-latest#az-group-show) | Egyetlen erőforráscsoportot kér le. |
| [az policy assignment create](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create) | Létrehoz egy új Azure Policy-hozzárendelést. Ebben a példában adunk hozzá egy definíciót, de használhat egy kezdeményezést is. |
| [az policy assignment delete](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-delete) | Eltávolít egy létező Azure Policy-hozzárendelést. |
| [az policy definition delete](/cli/azure/policy/definition?view=azure-cli-latest#az-policy-definition-delete) | Eltávolít egy létező Azure Policy-definíciót. |

## <a name="rest-api"></a>REST API

Számos eszköz alkalmas a Resource Manager REST API-val való kommunikációra, például az [ARMClient](https://github.com/projectkudu/ARMClient) vagy a PowerShell.

### <a name="deploy-with-rest-api"></a>Üzembe helyezés a REST API-val

- Hozza létre a szabályzat definícióját (Előfizetési hatókör). A kérelem törzséhez használja a [szabályzatdefiníció](#policy-definition) JSON-t.

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/audit-keyvault-vnet-rules?api-version=2018-05-01
  ```

- Hozza létre a szabályzat hozzárendelését (Erőforráscsoporti hatókör)

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/YourResourceGroup/providers/Microsoft.Authorization/policyAssignments/audit-keyvault-vnet-rules-assignment?api-version=2018-05-01
  ```

  A kérelem törzséhez használja az alábbi JSON-mintát:

  ```json
  {
      "properties": {
          "displayName": "Audit Key Vault Assignment",
          "policyDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/audit-keyvault-vnet-rules"
      }
  }
  ```

### <a name="remove-with-rest-api"></a>Eltávolítás a REST API-val

- A szabályzat-hozzárendelés eltávolítása

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/audit-keyvault-vnet-rules-assignment?api-version=2018-05-01
  ```

- A szabályzatdefiníció eltávolítása

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/audit-keyvault-vnet-rules?api-version=2018-05-01
  ```

### <a name="rest-api-explanation"></a>REST API magyarázat

| Szolgáltatás | Csoport | Művelet | Megjegyzések |
|---|---|---|---|
| Erőforrás-kezelés | Szabályzatdefiníciók | [Létrehozás](/rest/api/resources/policydefinitions/createorupdate) | Létrehoz egy új Azure Policy definíciót egy előfizetésnél. Alternatív: [Hozzon létre, amikor a felügyeleti csoport](/rest/api/resources/policydefinitions/createorupdateatmanagementgroup) |
| Erőforrás-kezelés | Szabályzat-hozzárendelések | [Létrehozás](/rest/api/resources/policyassignments/create) | Létrehoz egy új Azure Policy-hozzárendelést. Ebben a példában adunk hozzá egy definíciót, de használhat egy kezdeményezést is. |
| Erőforrás-kezelés | Szabályzat-hozzárendelések | [Törlés](/rest/api/resources/policyassignments/delete) | Eltávolít egy létező Azure Policy-hozzárendelést. |
| Erőforrás-kezelés | Szabályzatdefiníciók | [Törlés](/rest/api/resources/policydefinitions/delete) | Eltávolít egy létező Azure Policy-definíciót. Alternatív: [A felügyeleti csoport törlése](/rest/api/resources/policydefinitions/deleteatmanagementgroup) |

## <a name="next-steps"></a>További lépések

- További [Azure Policy-minták](index.md) áttekintése
- Az [Azure szabályzatdefiníciók struktúrája](../concepts/definition-structure.md) szakasz áttekintése