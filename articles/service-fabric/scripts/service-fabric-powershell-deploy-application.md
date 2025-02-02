---
title: Azure PowerShell parancsfájl-minta – alkalmazás üzembe helyezése egy fürtön | Microsoft Docs
description: Azure PowerShell parancsfájl-minta – alkalmazás üzembe helyezése egy Service Fabric-fürtön.
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 01/18/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: d03eec5101f932caff43bf876a41219e3f1fa75d
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035565"
---
# <a name="deploy-an-application-to-a-service-fabric-cluster"></a>Alkalmazás üzembe helyezése egy Service Fabric-fürtön

Ez a parancsfájl egy telepítőcsomagot másol egy fürt rendszerkép-tárolójába, regisztrálja az alkalmazás típusát a fürtben, eltávolítja a szükségtelen alkalmazáscsomag-csomagot, és létrehoz egy alkalmazás-példányt az alkalmazás típusától.  Ha az alapértelmezett szolgáltatások a célalkalmazás-típus alkalmazási jegyzékfájljában lettek definiálva, akkor ezek a szolgáltatások most jönnek létre. Szabja testre a paramétereket szükség szerint. 

Ha szükséges, telepítse a Service Fabric PowerShell-modult a [Service Fabric SDK](../service-fabric-get-started.md)-val. 

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/service-fabric/deploy-application/deploy-application.ps1 "Deploy an application to a cluster")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

A parancsfájl-minta futtatása után az [alkalmazás eltávolítása](service-fabric-powershell-remove-application.md) lehetőséggel eltávolíthatja az alkalmazás példányát, visszavonhatja az alkalmazás típusát, és törölheti az alkalmazáscsomag a rendszerkép-tárolóból.

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
|[Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps)| Létrehoz egy Service Fabric-fürthöz való kapcsolódást. |
|[Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) | Egy alkalmazáscsomag másolása a fürt rendszerkép-tárolójába.  |
|[Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps)| Egy alkalmazás típusának és verziójának regisztrálása a fürtön. |
|[New-ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps)| Létrehoz egy alkalmazást egy regisztrált alkalmazás típusától. |
| [Remove-ServiceFabricApplicationPackage](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps) | Eltávolít egy Service Fabric alkalmazáscsomag a rendszerkép-tárolóból.|

## <a name="next-steps"></a>További lépések

További információ a Service Fabric PowerShell-modullal kapcsolatban: [Azure PowerShell dokumentáció](/powershell/azure/service-fabric/?view=azureservicefabricps).

További Powershell-példákat az Azure Service Fabrichez az [Azure PowerShell-példák](../service-fabric-powershell-samples.md) között találhat.
