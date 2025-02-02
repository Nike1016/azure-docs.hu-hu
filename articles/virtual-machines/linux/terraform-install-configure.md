---
title: Terraform telepítése és konfigurálása az Azure-hoz való használatra | Microsoft Docs
description: Ismerje meg, hogyan telepítheti és konfigurálhatja az Azure-erőforrások létrehozásához szükséges Terraform
services: virtual-machines-linux
documentationcenter: virtual-machines
author: echuvyrov
manager: gwallace
editor: na
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/19/2018
ms.author: gwallace
ms.openlocfilehash: 14bbbb6581d3e6d00db532e343f8362fc44d0044
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876342"
---
# <a name="install-and-configure-terraform-to-provision-vms-and-other-infrastructure-into-azure"></a>Terraform telepítése és konfigurálása a virtuális gépek és más infrastruktúra üzembe helyezéséhez az Azure-ban
 
A Terraform segítségével egyszerűen meghatározhatja, megtekintheti és üzembe helyezheti a felhőalapú infrastruktúrát egy [egyszerű sablonos nyelv](https://www.terraform.io/docs/configuration/syntax.html)használatával. Ez a cikk a Terraform Azure-beli erőforrások kiépítéséhez szükséges lépéseit ismerteti.

Ha többet szeretne megtudni arról, hogyan használható az Terraform az Azure-ban, látogasson el az [Terraform hub](/azure/terraform)webhelyére.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

A Terraform alapértelmezés szerint telepítve van a [Cloud Shell](/azure/terraform/terraform-cloud-shell). Ha úgy dönt, hogy helyileg telepíti a Terraform-t, hajtsa végre a következő lépést, máskülönben folytassa a [Terraform-hozzáférés beállítását az Azure-hoz](#set-up-terraform-access-to-azure).

## <a name="install-terraform"></a>A Terraform telepítése

A Terraform telepítéséhez [töltse le](https://www.terraform.io/downloads.html) az operációs rendszerének megfelelő csomagot egy különálló telepítési könyvtárba. A letöltés egyetlen végrehajtható fájlt tartalmaz, amelynek globális elérési utat is meg kell határoznia. A Linux-és Mac-eszközök elérési útjának beállításával kapcsolatos útmutatásért keresse fel [ezt](https://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux)a weblapot. Az elérési út Windows rendszeren való beállításával kapcsolatos utasításokért keresse fel [ezt](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows)a weblapot.

Ellenőrizze az elérési út konfigurációját a `terraform` paranccsal. Megjelenik az elérhető Terraform lehetőségek listája, ahogy az alábbi példában is látható:

```bash
azureuser@Azure:~$ terraform
Usage: terraform [--version] [--help] <command> [args]
```

## <a name="set-up-terraform-access-to-azure"></a>Terraform-hozzáférés beállítása az Azure-hoz

Ahhoz, hogy a Terraform erőforrásokat lehessen kiépíteni az Azure-ba, hozzon létre egy [Azure ad egyszerű szolgáltatásnevet](/cli/azure/create-an-azure-service-principal-azure-cli). Az egyszerű szolgáltatásnév az Azure-előfizetésében lévő erőforrások kiépítéséhez biztosít Terraform-parancsfájlokat.

Ha több Azure-előfizetéssel rendelkezik, először kérdezze le a fiókját az [az Account show](/cli/azure/account#az-account-show) paranccsal az előfizetés-azonosító és a bérlői azonosító értékek listájának lekéréséhez:

```azurecli-interactive
az account show --query "{subscriptionId:id, tenantId:tenantId}"
```

Ha a kiválasztott előfizetést szeretné használni, állítsa be ennek a munkamenetnek az előfizetését az [az Account set](/cli/azure/account#az-account-set)paranccsal. Állítsa be `SUBSCRIPTION_ID` úgy a környezeti változót, hogy a visszaadott mező értékét a használni kívánt előfizetésből kapja: `id`

```azurecli-interactive
az account set --subscription="${SUBSCRIPTION_ID}"
```

Most létrehozhat egy egyszerű szolgáltatásnevet a Terraform-hez való használatra. Használja az [az ad SP Create-for-RBAC](/cli/azure/ad/sp#az-ad-sp-create-for-rbac), és állítsa  be a hatókört az előfizetésre a következőképpen:

```azurecli-interactive
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${SUBSCRIPTION_ID}"
```

A `appId` ,`password`a, a és`tenant` a értéket adja vissza. `sp_name` Jegyezze fel a és `appId` `password`a.

## <a name="configure-terraform-environment-variables"></a>Terraform környezeti változók konfigurálása

Ha a Terraform-t az Azure AD-szolgáltatásnév használatára szeretné konfigurálni, állítsa be az alábbi környezeti változókat, amelyeket az [Azure Terraform-modulok](https://registry.terraform.io/modules/Azure)használnak. A környezetet akkor is beállíthatja, ha az Azure-felhőn kívül más Azure-felhővel dolgozik.

- `ARM_SUBSCRIPTION_ID`
- `ARM_CLIENT_ID`
- `ARM_CLIENT_SECRET`
- `ARM_TENANT_ID`
- `ARM_ENVIRONMENT`

A következő minta rendszerhéj-parancsfájl használatával állíthatja be a változókat:

```bash
#!/bin/sh
echo "Setting environment variables for Terraform"
export ARM_SUBSCRIPTION_ID=your_subscription_id
export ARM_CLIENT_ID=your_appId
export ARM_CLIENT_SECRET=your_password
export ARM_TENANT_ID=your_tenant_id

# Not needed for public, required for usgovernment, german, china
export ARM_ENVIRONMENT=public
```

## <a name="run-a-sample-script"></a>Minta parancsfájl futtatása

Hozzon létre `test.tf` egy fájlt egy üres könyvtárban, és illessze be az alábbi szkriptet.

```tf
provider "azurerm" {
}
resource "azurerm_resource_group" "rg" {
        name = "testResourceGroup"
        location = "westus"
}
```

Mentse a fájlt, majd inicializálja a Terraform-telepítést. Ez a lépés letölti az Azure-erőforráscsoport létrehozásához szükséges Azure-modulokat.

```bash
terraform init
```

A kimenet a következő példához hasonló:

```bash
* provider.azurerm: version = "~> 0.3"

Terraform has been successfully initialized!
```

Megtekintheti a Terraform-szkript `terraform plan`által végrehajtandó műveleteket. Ha készen áll az erőforráscsoport létrehozására, alkalmazza a Terraform tervet a következőképpen:

```bash
terraform apply
```

A kimenet a következő példához hasonló:

```bash
An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  + azurerm_resource_group.rg
      id:       <computed>
      location: "westus"
      name:     "testResourceGroup"
      tags.%:   <computed>

azurerm_resource_group.rg: Creating...
  location: "" => "westus"
  name:     "" => "testResourceGroup"
  tags.%:   "" => "<computed>"
azurerm_resource_group.rg: Creation complete after 1s
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben telepítette a Terraform-t, vagy használta a Cloud Shell az Azure-beli hitelesítő adatok konfigurálásához és az erőforrások létrehozásának megkezdéséhez az Azure-előfizetésében. A Terraform üzembe helyezéséhez az Azure-ban a következő cikkben talál további információt:

> [!div class="nextstepaction"]
> [Azure-beli virtuális gép létrehozása a Terraform](terraform-create-complete-vm.md)
