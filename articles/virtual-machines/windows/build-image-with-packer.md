---
title: Windows Virtuálisgép-rendszerképek létrehozása a Packer az Azure-ban |} A Microsoft Docs
description: Ismerje meg, hogyan Windows virtuálisgép-rendszerképek létrehozása az Azure-ban a Packer használatával
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/22/2019
ms.author: cynthn
ms.openlocfilehash: 905f330af7052b7d39058b5d84fb51a70311248d
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67719321"
---
# <a name="how-to-use-packer-to-create-windows-virtual-machine-images-in-azure"></a>Windows virtuálisgép-rendszerképek létrehozása az Azure-ban a Packer használatával
Az Azure-ban minden virtuális gép (VM) jön létre egy rendszerképből, amely meghatározza a Windows-telepítési és az operációs rendszer verzióját. Rendszerképek előre telepített alkalmazások és konfigurációk tartalmazhatnak. Az Azure piactér sok első és a külső képek biztosít az operációs rendszer leggyakrabban használt, és az környezetek, vagy létrehozhat saját igényeire szabott lemezképek. Ez a cikk részletesen bemutatja a nyílt forráskódú eszköz [Packer](https://www.packer.io/) definiálására és egyéni lemezképeket az Azure-ban.

Ez a cikk utolsó tesztelésének a 2019/2/21 használatával a [Az PowerShell-modul](https://docs.microsoft.com/powershell/azure/install-az-ps) verzió 1.3.0 és [Packer](https://www.packer.io/docs/install/index.html) 1.3.4 verzió.

> [!NOTE]
> Az Azure-szolgáltatás, az Azure az Image Builder (előzetes verzió), most már rendelkezik meghatározására és saját egyéni rendszerképek létrehozásához. Az Azure az Image Builder Packer, épül, így még akkor is használhatja a meglévő Packer rendszerhéj webhelykiépítőt parancsfájlok vele. Ismerkedés az Azure az Image Builder, lásd: [Windows virtuális gép létrehozása az Azure az Image Builder](image-builder.md).

## <a name="create-azure-resource-group"></a>Azure-erőforráscsoport létrehozása
Az összeállítási folyamat során Packer hozza létre, ideiglenes Azure-erőforrások, a forrás virtuális Gépet épít. Rögzíti a forrásoldali virtuális gép lemezképként való használatra, meg kell határoznia egy erőforráscsoportot. Ez az erőforráscsoport a Packer buildelési folyamat kimenete tárolja.

Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *EastUS* helyen:

```azurepowershell
$rgName = "myResourceGroup"
$location = "East US"
New-AzResourceGroup -Name $rgName -Location $location
```

## <a name="create-azure-credentials"></a>Azure-beli hitelesítő adatok létrehozása
Csomagolói hitelesíti az Azure-ral egyszerű szolgáltatás használatával. Azure-beli szolgáltatásnév egy biztonsági identitás, az alkalmazások, szolgáltatások és automatizálási eszközökkel, mint például a Packer használható. Szabályozhatja és az egyszerű szolgáltatás az Azure-ban hajthat végre műveleteket helyrendszerszerepkörökre engedélyeinek megadásához.

Az egyszerű szolgáltatás létrehozása [New-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal) és az egyszerű szolgáltatás létrehozása és-erőforrások kezelése a engedélyeket [New-AzRoleAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment). Az érték `-DisplayName` egyedi; kell lennie igény szerint cserélje le a saját értékét.  

```azurepowershell
$sp = New-AzADServicePrincipal -DisplayName "PackerServicePrincipal"
$BSTR = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($sp.Secret)
$plainPassword = [System.Runtime.InteropServices.Marshal]::PtrToStringAuto($BSTR)
New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $sp.ApplicationId
```

Ezután kimeneti a jelszót és az alkalmazás azonosítóját.

```powershell
$plainPassword
$sp.ApplicationId
```


Azure-beli hitelesítésre, is kell szerezze be az Azure bérlői és az előfizetés azonosítókat [Get-AzSubscription](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription):

```powershell
Get-AzSubscription
```


## <a name="define-packer-template"></a>Csomagoló sablon meghatározása
A lemezképeket, hozzon létre egy sablon JSON-fájlként. A sablonban és adjon meg sikerei provisioners, amely a tényleges buildelési folyamat végrehajtására. Csomagolói rendelkezik egy [az Azure jelentéskészítő](https://www.packer.io/docs/builders/azure.html) , amely lehetővé teszi, megadhatja az Azure-erőforrások, például az egyszerű szolgáltatás hitelesítő adatai az előző létrehozott lépést.

Hozzon létre egy fájlt *windows.json* , és illessze be az alábbi tartalommal. Adja meg a saját értékeit a következőkhöz:

| Paraméter                           | Beszerzési helyét |
|-------------------------------------|----------------------------------------------------|
| *client_id*                         | Nézet szolgáltatásnév-Azonosítót a `$sp.applicationId` |
| *client_secret*                     | Az automatikusan generált jelszót megtekintése `$plainPassword` |
| *tenant_id*                         | A kimeneti `$sub.TenantId` parancs |
| *subscription_id*                   | A kimeneti `$sub.SubscriptionId` parancs |
| *managed_image_resource_group_name* | Az első lépésben létrehozott erőforráscsoport nevét |
| *managed_image_name*                | A létrehozott felügyelt lemez lemezkép neve |

```json
{
  "builders": [{
    "type": "azure-arm",

    "client_id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "client_secret": "ppppppp-pppp-pppp-pppp-ppppppppppp",
    "tenant_id": "zzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz",
    "subscription_id": "yyyyyyy-yyyy-yyyy-yyyy-yyyyyyyyyyy",

    "managed_image_resource_group_name": "myResourceGroup",
    "managed_image_name": "myPackerImage",

    "os_type": "Windows",
    "image_publisher": "MicrosoftWindowsServer",
    "image_offer": "WindowsServer",
    "image_sku": "2016-Datacenter",

    "communicator": "winrm",
    "winrm_use_ssl": true,
    "winrm_insecure": true,
    "winrm_timeout": "5m",
    "winrm_username": "packer",

    "azure_tags": {
        "dept": "Engineering",
        "task": "Image deployment"
    },

    "location": "East US",
    "vm_size": "Standard_DS2_v2"
  }],
  "provisioners": [{
    "type": "powershell",
    "inline": [
      "Add-WindowsFeature Web-Server",
      "& $env:SystemRoot\\System32\\Sysprep\\Sysprep.exe /oobe /generalize /quiet /quit",
      "while($true) { $imageState = Get-ItemProperty HKLM:\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Setup\\State | Select ImageState; if($imageState.ImageState -ne 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { Write-Output $imageState.ImageState; Start-Sleep -s 10  } else { break } }"
    ]
  }]
}
```

Ezzel a sablonnal hoz létre egy Windows Server 2016 virtuális gép, telepíti az IIS szolgáltatást, és általánosítja a virtuális Gépet a Sysprep használatával. Az IIS telepítését mutatja, hogyan használhatja a PowerShell webhelykiépítőt további parancsok futtatásához. A végső Packer lemezkép ezután tartalmazza a szükséges szoftverek telepítése és konfigurálása.


## <a name="build-packer-image"></a>Csomagolói rendszerkép összeállítása
Ha még nincs telepítve a helyi gépen Packer [csomagolói telepítési utasításokat](https://www.packer.io/docs/install/index.html).

A rendszerkép létrehozásához nyissa meg egy parancssort, és a Packer megadásával sablonfájl módon:

```
./packer build windows.json
```

Egy példa a kimenetre az az előző parancsokban a következőképpen történik:

```bash
azure-arm output will be in this color.

==> azure-arm: Running builder ...
    azure-arm: Creating Azure Resource Manager (ARM) client ...
==> azure-arm: Creating resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> Location          : ‘East US’
==> azure-arm:  -> Tags              :
==> azure-arm:  ->> task : Image deployment
==> azure-arm:  ->> dept : Engineering
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Getting the certificate’s URL ...
==> azure-arm:  -> Key Vault Name        : ‘pkrkvpq0mthtbtt’
==> azure-arm:  -> Key Vault Secret Name : ‘packerKeyVaultSecret’
==> azure-arm:  -> Certificate URL       : ‘https://pkrkvpq0mthtbtt.vault.azure.net/secrets/packerKeyVaultSecret/8c7bd823e4fa44e1abb747636128adbb'
==> azure-arm: Setting the certificate’s URL ...
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Getting the VM’s IP address ...
==> azure-arm:  -> ResourceGroupName   : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> PublicIPAddressName : ‘packerPublicIP’
==> azure-arm:  -> NicName             : ‘packerNic’
==> azure-arm:  -> Network Connection  : ‘PublicEndpoint’
==> azure-arm:  -> IP Address          : ‘40.76.55.35’
==> azure-arm: Waiting for WinRM to become available...
==> azure-arm: Connected to WinRM!
==> azure-arm: Provisioning with Powershell...
==> azure-arm: Provisioning with shell script: /var/folders/h1/ymh5bdx15wgdn5hvgj1wc0zh0000gn/T/packer-powershell-provisioner902510110
    azure-arm: #< CLIXML
    azure-arm:
    azure-arm: Success Restart Needed Exit Code      Feature Result
    azure-arm: ------- -------------- ---------      --------------
    azure-arm: True    No             Success        {Common HTTP Features, Default Document, D...
    azure-arm: <Objs Version=“1.1.0.1” xmlns=“http://schemas.microsoft.com/powershell/2004/04"><Obj S=“progress” RefId=“0"><TN RefId=“0”><T>System.Management.Automation.PSCustomObject</T><T>System.Object</T></TN><MS><I64 N=“SourceId”>1</I64><PR N=“Record”><AV>Preparing modules for first use.</AV><AI>0</AI><Nil /><PI>-1</PI><PC>-1</PC><T>Completed</T><SR>-1</SR><SD> </SD></PR></MS></Obj></Objs>
==> azure-arm: Querying the machine’s properties ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> ComputeName       : ‘pkrvmpq0mthtbtt’
==> azure-arm:  -> Managed OS Disk   : ‘/subscriptions/guid/resourceGroups/packer-Resource-Group-pq0mthtbtt/providers/Microsoft.Compute/disks/osdisk’
==> azure-arm: Powering off machine ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> ComputeName       : ‘pkrvmpq0mthtbtt’
==> azure-arm: Capturing image ...
==> azure-arm:  -> Compute ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> Compute Name              : ‘pkrvmpq0mthtbtt’
==> azure-arm:  -> Compute Location          : ‘East US’
==> azure-arm:  -> Image ResourceGroupName   : ‘myResourceGroup’
==> azure-arm:  -> Image Name                : ‘myPackerImage’
==> azure-arm:  -> Image Location            : ‘eastus’
==> azure-arm: Deleting resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm: Deleting the temporary OS disk ...
==> azure-arm:  -> OS Disk : skipping, managed disk was used...
Build ‘azure-arm’ finished.

==> Builds finished. The artifacts of successful builds are:
--> azure-arm: Azure.ResourceManagement.VMImage:

ManagedImageResourceGroupName: myResourceGroup
ManagedImageName: myPackerImage
ManagedImageLocation: eastus
```

A virtuális gép létrehozása, futtassa a provisioners és a központi telepítés tisztítása Packer néhány percig tart.


## <a name="create-a-vm-from-the-packer-image"></a>Virtuális gép létrehozása a Packer rendszerképből
Most már létrehozhat egy virtuális Gépet a rendszerképből [New-azvm parancsmag](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). A támogató hálózati erőforrások jönnek létre, ha azok nem léteznek. Amikor a rendszer kéri, adja meg egy rendszergazdai felhasználónevet és jelszót a virtuális gép hozható létre. A következő példában létrehozunk egy nevű virtuális Gépet *myVM* a *myPackerImage*:

```powershell
New-AzVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80 `
    -Image "myPackerImage"
```

Ha szeretne egy másik erőforráscsoportban vagy régióban, mint a Packer lemezkép hozzon létre virtuális gépet, adja meg a rendszerkép neve helyett a lemezkép-azonosító. Szerezheti be a lemezkép-azonosító [Get-AzImage](https://docs.microsoft.com/powershell/module/az.compute/Get-AzImage).

A virtuális gép létrehozása a Packer lemezkép alapján néhány percet vesz igénybe.


## <a name="test-vm-and-webserver"></a>Virtuális gép és a webkiszolgáló tesztelése
Szerezze be a virtuális gép nyilvános IP-címét [Get-AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress). A következő példa a korábban létrehozott *myPublicIP* IP-címét kéri le:

```powershell
Get-AzPublicIPAddress `
    -ResourceGroupName $rgName `
    -Name "myPublicIPAddress" | select "IpAddress"
```

A virtuális Gépen, amely tartalmazza az IIS telepítését a Packer webhelykiépítőt a működés közben, hogy adja meg a nyilvános IP-címet egy webböngészőben.

![Alapértelmezett IIS-webhely](./media/build-image-with-packer/iis.png) 


## <a name="next-steps"></a>További lépések
Használhatja a meglévő Packer webhelykiépítőt parancsfájlok [Azure Image Builder](image-builder.md).
