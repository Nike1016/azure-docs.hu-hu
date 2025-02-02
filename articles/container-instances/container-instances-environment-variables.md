---
title: Környezeti változók beállítása a Azure Container Instancesban
description: Megtudhatja, hogyan állíthatja be a környezeti változókat a Azure Container Instancesban futtatott tárolókban.
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 04/17/2019
ms.author: danlep
ms.openlocfilehash: 9cd62c378270da31079a38f89b040985105a4218
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326039"
---
# <a name="set-environment-variables-in-container-instances"></a>Környezeti változók beállítása a Container instances szolgáltatásban

A környezeti változók beállítása a Container instances szolgáltatásban lehetővé teszi a tároló által futtatott alkalmazás vagy parancsfájl dinamikus konfigurációját. Ez hasonló a `--env` parancssori `docker run`argumentumhoz. 

A tárolók környezeti változóinak megadásához adja meg őket a tároló-példány létrehozásakor. Ez a cikk példákat mutat be környezeti változók beállítására, amikor elindít egy tárolót az [Azure CLI](#azure-cli-example)-vel, [Azure PowerShell](#azure-powershell-example)és a [Azure Portal](#azure-portal-example). 

Ha például a Microsoft [ACI-WordCount][aci-wordcount] tároló rendszerképét futtatja, a következő környezeti változók megadásával módosíthatja a viselkedését:

*NumWords*: Az STDOUT-ba küldendő szavak száma.

*MinLength*: A kifejezésben szereplő karakterek minimális számát kell megszámolni. A nagyobb szám figyelmen kívül hagyja a gyakori szavakat, például az "a" és a "The" kifejezést.

Ha környezeti változókként kell átadnia a titkokat, Azure Container Instances támogatja a Windows-és Linux-tárolók [biztonságos értékeit](#secure-values) .

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-cli-example"></a>Azure CLI-példa

Az [ACI-WordCount][aci-wordcount] container, run it first with this [az container create][az-container-create] parancs alapértelmezett kimenetének megjelenítéséhez (nincsenek megadva környezeti változók):

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure
```

A kimenet módosításához indítson el egy második tárolót a `--environment-variables` hozzáadott argumentummal, adja meg a *NumWords* és a *MinLength* változók értékeit. (Ez a példa feltételezi, hogy a parancssori felületet egy bash-rendszerhéjban vagy Azure Cloud Shellban futtatja. Ha a Windows-parancssort használja, a változókat idézőjelek között `--environment-variables "NumWords"="5" "MinLength"="8"`kell megadni, például:.)

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables 'NumWords'='5' 'MinLength'='8'
```

Ha mindkét tároló állapota *leáll* (az az [Container show][az-container-show] to check state), display their logs with [az container logs][az-container-logs] paranccsal jelenítheti meg a kimenetet).

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
az container logs --resource-group myResourceGroup --name mycontainer2
```

A tárolók kimenete azt mutatja be, hogyan módosította a második tároló parancsfájl-viselkedését környezeti változók beállításával.

```console
azureuser@Azure:~$ az container logs --resource-group myResourceGroup --name mycontainer1
[('the', 990),
 ('and', 702),
 ('of', 628),
 ('to', 610),
 ('I', 544),
 ('you', 495),
 ('a', 453),
 ('my', 441),
 ('in', 399),
 ('HAMLET', 386)]

azureuser@Azure:~$ az container logs --resource-group myResourceGroup --name mycontainer2
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]
```

## <a name="azure-powershell-example"></a>Azure PowerShell példa

A környezeti változók a PowerShellben való beállítása hasonló a CLI-hez, `-EnvironmentVariable` de a parancssori argumentumot használja.

Először indítsa el az [ACI-WordCount][aci-wordcount] container in its default configuration with this [New-AzContainerGroup][new-Azcontainergroup] parancsot:

```azurepowershell-interactive
New-AzContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer1 `
    -Image mcr.microsoft.com/azuredocs/aci-wordcount:latest
```

Most futtassa a következő [New-AzContainerGroup][new-Azcontainergroup] parancsot. Ez határozza meg a *NumWords* és a *MinLength* környezeti változókat a `envVars`tömb változóinak feltöltése után:

```azurepowershell-interactive
$envVars = @{'NumWords'='5';'MinLength'='8'}
New-AzContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer2 `
    -Image mcr.microsoft.com/azuredocs/aci-wordcount:latest `
    -RestartPolicy OnFailure `
    -EnvironmentVariable $envVars
```

Ha mindkét tároló állapota *le van állítva* (a [Get-AzContainerInstanceLog][azure-instance-log] használatával kell ellenőriznie az állapotot), a naplók a [Get-AzContainerInstanceLog][azure-instance-log] paranccsal hívhatók meg.

```azurepowershell-interactive
Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
```

Az egyes tárolók kimenete azt mutatja be, hogyan módosította a tároló által futtatott parancsfájlt környezeti változók beállításával.

```console
PS Azure:\> Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
[('the', 990),
 ('and', 702),
 ('of', 628),
 ('to', 610),
 ('I', 544),
 ('you', 495),
 ('a', 453),
 ('my', 441),
 ('in', 399),
 ('HAMLET', 386)]

Azure:\
PS Azure:\> Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]

Azure:\
```

## <a name="azure-portal-example"></a>Azure Portal példa

Ha környezeti változókat szeretne beállítani a Azure Portal tárolójának indításakor, a tároló létrehozásakor adja meg azokat a **speciális** lapon.

1. A **speciális** lapon állítsa be a *sikertelen* újraindítási **szabályzatot** .
2. A **környezeti változók**területen adja `NumWords` meg az értéket `5` az első változóhoz `8` , és adja meg `MinLength` a értékét a második változó értékeként. 
1. A tároló ellenőrzéséhez és üzembe helyezéséhez válassza a **felülvizsgálat + létrehozás** elemet.

![A portál lap környezeti változó engedélyezése gomb és szövegmezők][portal-env-vars-01]

A tároló naplóinak megtekintéséhez a **Beállítások** területen válassza a **tárolók**, majd a **naplók**lehetőséget. Az előző CLI-és PowerShell-szakaszban bemutatott kimenethez hasonlóan láthatja, hogyan módosították a parancsfájl viselkedését a környezeti változók. Csak öt szó jelenik meg, amelyek mindegyike legalább nyolc karakter hosszú lehet.

![A tároló napló kimenetét bemutató portál][portal-env-vars-02]

## <a name="secure-values"></a>Biztonságos értékek

A biztonságos értékekkel rendelkező objektumok bizalmas adatok, például jelszavak vagy kulcsok tárolására szolgálnak. A környezeti változók biztonságos értékeinek használata biztonságosabb és rugalmasabb, mint például a tároló rendszerképében. Egy másik lehetőség a titkos kötetek használata a [titkos kötet csatlakoztatása Azure Container Instancesban](container-instances-volume-secret.md)című témakörben.

A biztonságos értékekkel rendelkező környezeti változók nem láthatók a tároló tulajdonságaiban – az értékük csak a tárolón belülről érhető el. A Azure Portal vagy az Azure CLI-ben megtekintett tároló tulajdonságai például csak a biztonságos változó nevét jelenítik meg, nem az értékét.

Állítsa be a biztonságos környezeti változót úgy, `secureValue` hogy megadja a tulajdonságot `value` a változó típusának normál helyett. A következő YAML meghatározott két változó mutatja be a két változó típusát.

### <a name="yaml-deployment"></a>YAML üzembe helyezése

Hozzon `secure-env.yaml` létre egy fájlt a következő kódrészlettel.

```yaml
apiVersion: 2018-10-01
location: eastus
name: securetest
properties:
  containers:
  - name: mycontainer
    properties:
      environmentVariables:
        - name: 'NOTSECRET'
          value: 'my-exposed-value'
        - name: 'SECRET'
          secureValue: 'my-secret-value'
      image: nginx
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Futtassa a következő parancsot a tároló csoport YAML való üzembe helyezéséhez (szükség szerint módosítsa az erőforráscsoport nevét):

```azurecli-interactive
az container create --resource-group myResourceGroup --file secure-env.yaml
```

### <a name="verify-environment-variables"></a>Környezeti változók ellenőrzése

Futtassa az az [Container show][az-container-show] parancsot a tároló környezeti változóinak lekérdezéséhez:

```azurecli-interactive
az container show --resource-group myResourceGroup --name securetest --query 'containers[].environmentVariables'
```

A JSON-válasz a nem biztonságos környezeti változó kulcsát és értékét jeleníti meg, de csak a biztonságos környezeti változó nevét:

```json
[
  [
    {
      "name": "NOTSECRET",
      "secureValue": null,
      "value": "my-exposed-value"
    },
    {
      "name": "SECRET",
      "secureValue": null,
      "value": null
    }
  ]
]
```

Az az [Container exec][az-container-exec] paranccsal, amely lehetővé teszi egy parancs futtatását egy futó tárolóban, ellenőrizheti, hogy a biztonságos környezeti változó be van-e állítva. Futtassa a következő parancsot egy interaktív bash-munkamenet elindításához a tárolóban:

```azurecli-interactive
az container exec --resource-group myResourceGroup --name securetest --exec-command "/bin/bash"
```

Miután megnyitotta az interaktív felületet a tárolón belül, elérheti a `SECRET` változó értékét:

```console
root@caas-ef3ee231482549629ac8a40c0d3807fd-3881559887-5374l:/# echo $SECRET
my-secret-value
```

## <a name="next-steps"></a>További lépések

A feladat-alapú forgatókönyvek, például a Batch több tárolóval rendelkező nagyméretű adathalmazok feldolgozásával az egyéni környezeti változók is kihasználhatják futásidőben. A Task-alapú tárolók futtatásával kapcsolatos további információkért lásd: [a tárolózott feladatok futtatása](container-instances-restart-policy.md)újraindítási szabályzatokkal.

<!-- IMAGES -->
[portal-env-vars-01]: ./media/container-instances-environment-variables/portal-env-vars-01.png
[portal-env-vars-02]: ./media/container-instances-environment-variables/portal-env-vars-02.png

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[azure-cli-install]: /cli/azure/
[azure-instance-log]: /powershell/module/az.containerinstance/get-azcontainerinstancelog
[azure-powershell-install]: /powershell/azure/install-Az-ps
[new-Azcontainergroup]: /powershell/module/az.containerinstance/new-azcontainergroup
[portal]: https://portal.azure.com
