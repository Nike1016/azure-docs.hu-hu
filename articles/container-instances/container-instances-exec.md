---
title: Parancsok végrehajtása a futó tárolókban Azure Container Instances
description: Megtudhatja, hogyan hajthat végre egy parancsot egy olyan tárolóban, amely jelenleg fut Azure Container Instances
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 03/30/2018
ms.author: danlep
ms.openlocfilehash: a8583cf605891631a2bce6914b24525aebd59ea0
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68325982"
---
# <a name="execute-a-command-in-a-running-azure-container-instance"></a>Parancs végrehajtása egy futó Azure Container-példányban

Azure Container Instances támogatja a parancs futtatását egy futó tárolóban. A már elindított tárolók egy parancsának futtatása különösen hasznos az alkalmazások fejlesztése és hibaelhárítása során. Ennek a funkciónak a leggyakoribb funkciója egy interaktív rendszerhéj elindítása, amely a futó tárolók hibáinak hibakeresését teszi elérhetővé.

## <a name="run-a-command-with-azure-cli"></a>Parancs futtatása az Azure CLI-vel

Parancs végrehajtása egy futó tárolóban az az [Container exec][az-container-exec] használatával az [Azure CLI][azure-cli]-ben:

```azurecli
az container exec --resource-group <group-name> --name <container-group-name> --exec-command "<command>"
```

Például egy bash-rendszerhéj elindításához egy Nginx-tárolóban:

```azurecli
az container exec --resource-group myResourceGroup --name mynginx --exec-command "/bin/bash"
```

Az alábbi példában a bash rendszerhéj egy futó Linux-tárolóban indul el, amely egy olyan terminált biztosít, `ls` amelyben a végrehajtás fut:

```console
$ az container exec --resource-group myResourceGroup --name mynginx --exec-command "/bin/bash"
root@caas-83e6c883014b427f9b277a2bba3b7b5f-708716530-2qv47:/# ls
bin   dev  home  lib64  mnt  proc  run   srv  tmp  var
boot  etc  lib   media  opt  root  sbin  sys  usr
root@caas-83e6c883014b427f9b277a2bba3b7b5f-708716530-2qv47:/# exit
exit
Bye.
```

Ebben a példában a parancssor egy futó Nanoserver-tárolóban indul el:

```console
$ az container exec --resource-group myResourceGroup --name myiis --exec-command "cmd.exe"
Microsoft Windows [Version 10.0.14393]
(c) 2016 Microsoft Corporation. All rights reserved.

C:\>dir
 Volume in drive C has no label.
 Volume Serial Number is 76E0-C852

 Directory of C:\

03/23/2018  09:13 PM    <DIR>          inetpub
11/20/2016  11:32 AM             1,894 License.txt
03/23/2018  09:13 PM    <DIR>          Program Files
07/16/2016  12:09 PM    <DIR>          Program Files (x86)
03/13/2018  08:50 PM           171,616 ServiceMonitor.exe
03/23/2018  09:13 PM    <DIR>          Users
03/23/2018  09:12 PM    <DIR>          var
03/23/2018  09:22 PM    <DIR>          Windows
               2 File(s)        173,510 bytes
               6 Dir(s)  21,171,609,600 bytes free

C:\>exit
Bye.
```

## <a name="multi-container-groups"></a>Többtárolós csoportok

Ha a [tároló-csoport](container-instances-container-groups.md) több tárolót tartalmaz, például egy alkalmazás-tárolót és egy az oldalkocsit, adja meg annak a tárolónak a nevét, `--container-name`amelyben a parancsot futtatni kívánja.

Például a Container Group *mynginx* két tároló, *Nginx-app* és *Logger*. Rendszerhéj elindítása az *Nginx-app* tárolóban:

```azurecli
az container exec --resource-group myResourceGroup --name mynginx --container-name nginx-app --exec-command "/bin/bash"
```

## <a name="restrictions"></a>Korlátozások

Azure Container Instances jelenleg egyetlen folyamat indítását támogatja az [az Container exec][az-container-exec]paranccsal, és a parancs argumentumai nem adhatók át. Például nem lehet a (z) vagy a `sh -c "echo FOO && echo BAR"`Execute `echo FOO`parancshoz hasonló parancsokat kikötni.

## <a name="next-steps"></a>További lépések

További információ a hibaelhárítási eszközökről és az általános telepítési problémákról: a [tárolók és az üzembe helyezési problémák elhárítása Azure Container Instancesban](container-instances-troubleshooting.md).

<!-- LINKS - internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[azure-cli]: /cli/azure