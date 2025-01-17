---
title: Linuxos virtuális merevlemez letöltése az Azure-ból | Microsoft Docs
description: Töltsön le egy linuxos virtuális merevlemezt az Azure CLI és a Azure Portal használatával.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: 5639571739f3eb6263f62444e7ab02186e2ca945
ms.sourcegitcommit: d060947aae93728169b035fd54beef044dbe9480
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2019
ms.locfileid: "68742586"
---
# <a name="download-a-linux-vhd-from-azure"></a>Linuxos virtuális merevlemez letöltése az Azure-ból

Ebből a cikkből megtudhatja, hogyan tölthet le egy linuxos virtuális merevlemezt (VHD-fájlt) az Azure-ból az Azure CLI és a Azure Portal használatával. 

Ha még nem tette meg, telepítse az [Azure CLI](https://docs.microsoft.com/cli/azure/install-az-cli2)-t.

## <a name="stop-the-vm"></a>A virtuális gép leállítása

Egy virtuális merevlemez nem tölthető le az Azure-ból, ha egy futó virtuális géphez van csatlakoztatva. A virtuális merevlemez letöltéséhez le kell állítania a virtuális gépet. Ha a virtuális merevlemezt [képként](tutorial-custom-images.md) szeretné létrehozni más virtuális gépek új lemezekkel való létrehozásához, meg kell szüntetnie és általánosítani a fájlban található operációs rendszert, és le kell állítania a virtuális gépet. Ha a virtuális merevlemezt lemezként szeretné használni egy meglévő virtuális gép vagy adatlemez új példánya számára, csak le kell állítania és felszabadítani kell a virtuális gépet.

Ha a virtuális merevlemezt képként szeretné használni más virtuális gépek létrehozásához, hajtsa végre a következő lépéseket:

1. Használja az SSH-t, a fiók nevét és a virtuális gép nyilvános IP-címét, hogy csatlakozhasson hozzá, és kiépítse azt. A nyilvános IP-címet az [az Network Public-IP show](https://docs.microsoft.com/cli/azure/network/public-ip#az-network-public-ip-show)paranccsal érheti el. A + User paraméter eltávolítja az utolsó kiosztott felhasználói fiókot is. Ha a fiók hitelesítő adatait a virtuális gépre veszi fel, hagyja ki ezt a + felhasználói paramétert. Az alábbi példa eltávolítja az utolsó kiépített felhasználói fiókot:

    ```bash
    ssh azureuser@<publicIpAddress>
    sudo waagent -deprovision+user -force
    exit 
    ```

2. Jelentkezzen be az Azure-fiókjába az [az login](https://docs.microsoft.com/cli/azure/reference-index)paranccsal.
3. Állítsa le és szabadítsa fel a virtuális gépet.

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

4. A virtuális gép általánosítása. 

    ```azurecli
    az vm generalize --resource-group myResourceGroup --name myVM
    ``` 

Ha a virtuális merevlemezt lemezként szeretné használni egy meglévő virtuális gép vagy adatlemez új példánya számára, hajtsa végre a következő lépéseket:

1.  Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2.  A központi menüben kattintson a **Virtuális gépek** elemre.
3.  Válassza ki a virtuális gépet a listából.
4.  A virtuális gép paneljén kattintson a **Leállítás**gombra.

    ![Virtuális gép leállítása](./media/download-vhd/export-stop.png)

## <a name="generate-sas-url"></a>SAS URL-cím előállítása

A VHD-fájl letöltéséhez egy [közös hozzáférési aláírás (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) URL-címét kell létrehoznia. Az URL-cím létrehozásakor a rendszer lejárati időt rendel az URL-címhez.

1.  A virtuális gép paneljének menüjében kattintson a **lemezek**elemre.
2.  Válassza ki a virtuális gép operációsrendszer-lemezét, majd kattintson a **lemez exportálása**lehetőségre.
3.  Kattintson az **URL-cím**előállítása gombra.

    ![URL-cím generálása](./media/download-vhd/export-generate.png)

## <a name="download-vhd"></a>VHD letöltése

1.  A létrehozott URL-cím alatt kattintson a VHD-fájl letöltése elemre.

    ![VHD letöltése](./media/download-vhd/export-download.png)

2.  Előfordulhat, hogy a letöltés indításához a böngésző **Mentés** gombjára kell kattintania. A VHD-fájl alapértelmezett neve *ABCD*.

    ![Kattintson a Mentés gombra a böngészőben](./media/download-vhd/export-save.png)

## <a name="next-steps"></a>További lépések

- Megtudhatja, hogyan [tölthet fel és hozhat létre Linux rendszerű virtuális gépet egyéni lemezről az Azure CLI-vel](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 
- [Azure-lemezek kezelése az Azure CLI](tutorial-manage-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)-vel.

