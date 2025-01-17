---
title: Diagnosztika engedélyezése az Azure-ban Cloud Services a PowerShell használatával | Microsoft Docs
description: Ismerje meg, hogyan engedélyezheti a diagnosztika szolgáltatást a Cloud Serviceshez a PowerShell használatával
services: cloud-services
documentationcenter: .net
author: georgewallace
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.date: 09/06/2016
ms.author: gwallace
ms.openlocfilehash: 4beed4dd874c23c36e125b5855e2e8380859ef83
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359164"
---
# <a name="enable-diagnostics-in-azure-cloud-services-using-powershell"></a>Diagnosztika engedélyezése az Azure Cloud Services a PowerShell használatával
A Azure Diagnostics-bővítmény használatával diagnosztikai adatokat gyűjthet, például az alkalmazás naplóit, a teljesítményszámlálókat stb. a felhőalapú szolgáltatásokból. Ez a cikk azt ismerteti, hogyan engedélyezhető a Azure Diagnostics bővítmény a Cloud Service-hez a PowerShell használatával.  Tekintse meg, [hogyan telepítheti és konfigurálhatja a Azure PowerShell](/powershell/azure/overview) a cikkhez szükséges előfeltételekhez.

## <a name="enable-diagnostics-extension-as-part-of-deploying-a-cloud-service"></a>A diagnosztikai bővítmény engedélyezése egy felhőszolgáltatás telepítésének részeként
Ez a megközelítés a forgatókönyvek folyamatos integrációs típusára vonatkozik, ahol a diagnosztikai bővítmény engedélyezhető a felhőalapú szolgáltatás üzembe helyezésének részeként. Új felhőalapú szolgáltatás központi telepítésének létrehozásakor engedélyezheti a diagnosztikai bővítményt, ha a *ExtensionConfiguration* paramétert a [New-AzureDeployment](/powershell/module/servicemanagement/azure/new-azuredeployment?view=azuresmps-3.7.0) parancsmagra továbbítja. A *ExtensionConfiguration* paraméter a [New-AzureServiceDiagnosticsExtensionConfig](/powershell/module/servicemanagement/azure/new-azureservicediagnosticsextensionconfig?view=azuresmps-3.7.0) parancsmaggal létrehozható diagnosztikai konfigurációk tömbjét veszi fel.

Az alábbi példa bemutatja, hogyan engedélyezheti a diagnosztika szolgáltatást egy olyan WorkerRole, amely egy másik diagnosztika-konfigurációval rendelkezik.

```powershell
$service_name = "MyService"
$service_package = "CloudService.cspkg"
$service_config = "ServiceConfiguration.Cloud.cscfg"
$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml"
$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath

New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig)
```

Ha a diagnosztikai konfigurációs fájl egy olyan `StorageAccount` elemet ad meg, amely egy Storage-fiók `New-AzureServiceDiagnosticsExtensionConfig` nevét tartalmazza, akkor a parancsmag automatikusan ezt a Storage-fiókot fogja használni. Ahhoz, hogy működjön, a Storage-fióknak ugyanabban az előfizetésben kell lennie, mint az üzembe helyezett felhőalapú szolgáltatásnak.

Az Azure SDK 2,6-tól kezdve az MSBuild közzétételi cél kimenetében létrehozott bővítmény konfigurációs fájljai a Storage-fiók nevét is tartalmazzák a szolgáltatás konfigurációs fájljában (. cscfg) megadott diagnosztikai konfigurációs karakterlánc alapján. Az alábbi szkript bemutatja, hogyan elemezheti a bővítmény konfigurációs fájljait a közzétételi cél kimenetből, és hogyan konfigurálhatja a diagnosztikai bővítményt az egyes szerepkörökhöz a Cloud Service telepítésekor.

```powershell
$service_name = "MyService"
$service_package = "C:\build\output\CloudService.cspkg"
$service_config = "C:\build\output\ServiceConfiguration.Cloud.cscfg"

#Find the Extensions path based on service configuration file
$extensionsSearchPath = Join-Path -Path (Split-Path -Parent $service_config) -ChildPath "Extensions"

$diagnosticsExtensions = Get-ChildItem -Path $extensionsSearchPath -Filter "PaaSDiagnostics.*.PubConfig.xml"
$diagnosticsConfigurations = @()
foreach ($extPath in $diagnosticsExtensions)
{
    #Find the RoleName based on file naming convention PaaSDiagnostics.<RoleName>.PubConfig.xml
    $roleName = ""
    $roles = $extPath -split ".",0,"simplematch"
    if ($roles -is [system.array] -and $roles.Length -gt 1)
    {
        $roleName = $roles[1]
        $x = 2
        while ($x -le $roles.Length)
            {
               if ($roles[$x] -ne "PubConfig")
                {
                    $roleName = $roleName + "." + $roles[$x]
                }
                else
                {
                    break
                }
                $x++
            }
        $fullExtPath = Join-Path -path $extensionsSearchPath -ChildPath $extPath
        $diagnosticsconfig = New-AzureServiceDiagnosticsExtensionConfig -Role $roleName -DiagnosticsConfigurationPath $fullExtPath
        $diagnosticsConfigurations += $diagnosticsconfig
    }
}
New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration $diagnosticsConfigurations
```

A Visual Studio online hasonló módszert használ a Cloud Services automatikus üzembe helyezéséhez a diagnosztikai bővítménnyel. Tekintse meg a [publish-AzureCloudDeployment. ps1](https://github.com/Microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureCloudPowerShellDeploymentV1/Publish-AzureCloudDeployment.ps1) teljes példáját.

Ha nem `StorageAccount` lett megadva a diagnosztika konfigurációjában, akkor át kell adni a *StorageAccountName* paramétert a parancsmagnak. Ha a *StorageAccountName* paraméter meg van adva, a parancsmag mindig a paraméterben megadott Storage-fiókot fogja használni, nem pedig a diagnosztikai konfigurációs fájlban megadott értéket.

Ha a diagnosztikai Storage-fiók egy másik előfizetésben található a Cloud Service-ben, akkor explicit módon át kell adni a *StorageAccountName* és a *StorageAccountKey* paramétereket a parancsmagnak. A *StorageAccountKey* paraméter nem szükséges, ha a diagnosztika Storage-fiók ugyanabban az előfizetésben van, mivel a parancsmag automatikusan kérdezheti le és állíthatja be a kulcs értékét a diagnosztikai bővítmény engedélyezésekor. Ha azonban a diagnosztikai tároló fiók egy másik előfizetésben található, előfordulhat, hogy a parancsmag nem tudja automatikusan beolvasni a kulcsot, és explicit módon meg kell adnia a kulcsot a *StorageAccountKey* paraméterrel.

```powershell
$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
```

## <a name="enable-diagnostics-extension-on-an-existing-cloud-service"></a>A diagnosztikai bővítmény engedélyezése egy meglévő felhőszolgáltatáson
A [set-AzureServiceDiagnosticsExtension](/powershell/module/servicemanagement/azure/set-azureservicediagnosticsextension?view=azuresmps-3.7.0) parancsmag használatával engedélyezheti vagy frissítheti a diagnosztikai konfigurációt egy már futó felhőalapú szolgáltatásban.

[!INCLUDE [cloud-services-wad-warning](../../includes/cloud-services-wad-warning.md)]

```powershell
$service_name = "MyService"
$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml"
$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath

Set-AzureServiceDiagnosticsExtension -DiagnosticsConfiguration @($webrole_diagconfig,$workerrole_diagconfig) -ServiceName $service_name
```

## <a name="get-current-diagnostics-extension-configuration"></a>Az aktuális diagnosztikai bővítmény konfigurációjának lekérése
Használja a [Get-AzureServiceDiagnosticsExtension](/powershell/module/servicemanagement/azure/get-azureservicediagnosticsextension?view=azuresmps-3.7.0) parancsmagot a felhőalapú szolgáltatás aktuális diagnosztikai konfigurációjának lekéréséhez.

```powershell
Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

## <a name="remove-diagnostics-extension"></a>A diagnosztikai bővítmény eltávolítása
A diagnosztika a Cloud Service-ben való kikapcsolásához használhatja a [Remove-AzureServiceDiagnosticsExtension](/powershell/module/servicemanagement/azure/remove-azureservicediagnosticsextension?view=azuresmps-3.7.0) parancsmagot.

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

Ha engedélyezte a diagnosztikai bővítményt a *set-AzureServiceDiagnosticsExtension* vagy a *New-AzureServiceDiagnosticsExtensionConfig* *szerepkör* -paraméter nélkül, akkor eltávolíthatja a bővítményt a következők használatával *: A Remove-AzureServiceDiagnosticsExtension* *szerepkör* -paraméter nélkül. Ha a *szerepkör* -paramétert a bővítmény engedélyezésekor használták, akkor azt is használni kell a bővítmény eltávolításához.

A diagnosztika bővítmény egyes szerepkörökből való eltávolítása:

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"
```

## <a name="next-steps"></a>További lépések
* Az Azure Diagnostics és a problémák elhárítására szolgáló egyéb technikák használatával kapcsolatos további útmutatásért lásd: [a diagnosztika engedélyezése az Azure-ban Cloud Services és Virtual Machines](cloud-services-dotnet-diagnostics.md).
* A [diagnosztika konfigurációs sémája](/azure/azure-monitor/platform/diagnostics-extension-schema-1dot2) a diagnosztikai bővítmény különböző XML-konfigurációk beállításait ismerteti.
* Ha meg szeretné tudni, hogyan engedélyezhető a Virtual Machines diagnosztikai bővítménye, tekintse meg a [Windows rendszerű virtuális gép létrehozása figyelési és diagnosztikai Azure Resource Manager sablon használatával](../virtual-machines/windows/extensions-diagnostics-template.md) című témakört.
