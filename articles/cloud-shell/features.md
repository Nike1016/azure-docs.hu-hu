---
title: Azure Cloud Shell funkciók | Microsoft Docs
description: A Azure Cloud Shell szolgáltatásainak áttekintése
services: Azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/26/2019
ms.author: damaerte
ms.openlocfilehash: 1354f7befd8c38537a555e17733f431dd488cf60
ms.sourcegitcommit: d060947aae93728169b035fd54beef044dbe9480
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2019
ms.locfileid: "68742056"
---
# <a name="features--tools-for-azure-cloud-shell"></a>Szolgáltatások & eszközök Azure Cloud Shell

[!INCLUDE [features-introblock](../../includes/cloud-shell-features-introblock.md)]

Azure Cloud Shell fut `Ubuntu 16.04 LTS`.

## <a name="features"></a>Szolgáltatások

### <a name="secure-automatic-authentication"></a>Biztonságos automatikus hitelesítés

Cloud Shell biztonságosan és automatikusan hitelesíti a fiókhoz való hozzáférést az Azure CLI-hez és a Azure PowerShellhoz.

### <a name="home-persistence-across-sessions"></a>$HOME adatmegőrzés a munkamenetek között

A fájlok munkamenetek közötti megőrzéséhez Cloud Shell végigvezeti egy Azure-fájlmegosztás első indításkor való csatlakoztatásán.
Ha elkészült, Cloud Shell automatikusan csatolja a tárolót (csatolva `$HOME\clouddrive`) az összes jövőbeli munkamenethez.
Emellett a `$HOME` címtára. img néven is megmarad az Azure-fájlmegosztás számára.
A és a `$HOME` gép állapotán kívüli fájlok nem maradnak meg a munkamenetek között. Ajánlott eljárások használata titkos kódok, például SSH-kulcsok tárolásához. Olyan szolgáltatások [, mint a Azure Key Vault a telepítőhöz kapcsolódó oktatóanyagok](https://docs.microsoft.com/azure/key-vault/key-vault-manage-with-cli2#prerequisites).

[További információ a fájlok megőrzéséről a Cloud Shellban.](persisting-shell-storage.md)

### <a name="azure-drive-azure"></a>Azure-meghajtó (Azure:)

A Cloud Shell PowerShell az Azure Drive (`Azure:`) szolgáltatásban indul el.
Az Azure-meghajtó az Azure-erőforrások (például számítás, hálózat, tárolás stb.) egyszerű felderítését és navigálását teszi lehetővé a fájlrendszerhez hasonló módon.
Továbbra is használhatja az ismerős [Azure PowerShell parancsmagokat](https://docs.microsoft.com/powershell/azure) az erőforrások kezeléséhez, függetlenül attól, hogy melyik meghajtóról van.
Az Azure-erőforrásokon végrehajtott, közvetlenül Azure Portal vagy Azure PowerShell parancsmagokkal végzett módosítások az Azure-meghajtón is megjelennek.  A futtatásával `dir -Force` frissítheti az erőforrásokat.

![](media/features-powershell/azure-drive.png)

### <a name="manage-exchange-online"></a>Exchange Online kezelése

A Cloud Shell PowerShell az Exchange Online modul privát összeállítását tartalmazza.  Az `Connect-EXOPSSession` Exchange-parancsmagok beszerzéséhez futtassa a parancsot.

![](media/features-powershell/exchangeonline.png)

 Futtassa a `Get-Command -Module tmp_*` parancsot.
> [!NOTE]
> A modul nevének a (z `tmp_`) értékkel kell kezdődnie, ha azonos előtaggal rendelkező modulokat telepített, a parancsmagok is felszínre kerülnek. 

![](media/features-powershell/exchangeonlinecmdlets.png)

### <a name="deep-integration-with-open-source-tooling"></a>Mélyreható integráció nyílt forráskódú eszközökkel

A Cloud Shell a nyílt forráskódú eszközök, például a Terraform, a Ansible és a Chef inspec előre konfigurált hitelesítését tartalmazza. Próbálja ki a példákat a forgatókönyvek közül.

## <a name="tools"></a>Eszközök

|Category   |Name (Név)   |
|---|---|
|Linux-eszközök            |bash<br> zsh<br> sh<br> tmux<br> dig<br>               |
|Azure-eszközök            |[Azure CLI](https://github.com/Azure/azure-cli) és [klasszikus Azure CLI](https://github.com/Azure/azure-xplat-cli)<br> [AzCopy](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy#writing-your-first-azcopy-command)<br> [Azure Functions parancssori felület](https://github.com/Azure/azure-functions-core-tools)<br> [Service Fabric parancssori felület](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli)<br> [Batch Shipyard](https://github.com/Azure/batch-shipyard)<br> [blobxfer](https://github.com/Azure/blobxfer)|
|Szövegszerkesztők           |Code (Cloud Shell Editor)<br> Vim<br> Nano<br> Emacs    |
|Verziókövetés         |git                    |
|Eszközök kiépítése            |Győződjön<br> Maven<br> npm<br> pip         |
|Containers             |[Docker Machine](https://github.com/docker/machine)<br> [Kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/)<br> [Helm](https://github.com/kubernetes/helm)<br> [DC/OS PARANCSSORI FELÜLET](https://github.com/dcos/dcos-cli)         |
|Adatbázisok              |MySQL-ügyfél<br> PostgreSql-ügyfél<br> [Sqlcmd segédprogram](https://docs.microsoft.com/sql/tools/sqlcmd-utility)<br> [mssql-scripter](https://github.com/Microsoft/sql-xplat-cli) |
|Egyéb                  |iPython-ügyfél<br> [Cloud Foundry parancssori felület](https://github.com/cloudfoundry/cli)<br> [Terraform](https://www.terraform.io/docs/providers/azurerm/)<br> [Ansible](https://www.ansible.com/microsoft-azure)<br> [A Chef InSpec](https://www.chef.io/inspec/)<br> [Báb-bolt](https://puppet.com/docs/bolt/latest/bolt.html)<br> [HashiCorp-csomagoló](https://www.packer.io/)|

## <a name="language-support"></a>Nyelvi támogatás

|Nyelv   |Version   |
|---|---|
|.NET Core  |2.0.0       |
|Ugrás         |1.9        |
|Java       |1.8        |
|Node.js    |8.9.4      |
|PowerShell |[6.2.0](https://github.com/PowerShell/powershell/releases)       |
|Python     |2,7 és 3,5 (alapértelmezett)|

## <a name="next-steps"></a>További lépések
[Bash Cloud Shell rövid útmutatóban](quickstart.md) <br>
[PowerShell Cloud Shell rövid útmutató](quickstart-powershell.md) <br>
[További tudnivalók az Azure CLI-ről](https://docs.microsoft.com/cli/azure/) <br>
[Tudnivalók a Azure PowerShell](https://docs.microsoft.com/powershell/azure/) <br>
