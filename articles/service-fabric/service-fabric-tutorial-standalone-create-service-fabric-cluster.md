---
title: 'Oktatóanyag: Önálló Service Fabric-ügyfél telepítése – Azure Service Fabric | Microsoft Docs'
description: Ez az oktatóanyag ismerteti, hogyan telepítheti az önálló Service Fabric-ügyfelet az előző oktatóanyagcikkben létrehozott fürtön.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/22/2019
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: 82358f4d5548464f1304d77a846d71863ed9efa1
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385005"
---
# <a name="tutorial-install-and-create-service-fabric-cluster"></a>Oktatóanyag: Service Fabric-fürt telepítése és létrehozása

Az önálló Service Fabric-fürtök lehetővé teszik, hogy kiválassza a saját környezetét, és hogy a Service Fabric „bármely operációs rendszer, bármilyen felhő” módszerével hozzon létre egy fürtöt. Ebben az oktatóanyag-sorozatban létre fog hozni egy AWS-ben vagy az Azure-ban üzemeltetett önálló fürtöt, és telepítenie kell egy alkalmazást.

Ez az oktatóanyag egy sorozat második része. Ez az oktatóanyag végigvezeti az önálló Service Fabric-fürt létrehozásának lépésein.

A sorozat második részében az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az önálló Service Fabric-csomag letöltése és telepítése
> * A Service Fabric-fürt létrehozása
> * Csatlakozás a Service Fabric-fürthöz

## <a name="download-the-service-fabric-for-windows-server-package"></a>A Windows Serverhez készült Service Fabric-csomag letöltése

A Service Fabric egy telepítőcsomagot biztosít az önálló Service Fabric-fürtök telepítéséhez.  [Töltse le a telepítőcsomagot](https://go.microsoft.com/fwlink/?LinkId=730690) a helyi számítógépre.  Miután sikeresen letöltötte a példányt a virtuális gépre irányuló RDP-kapcsolaton keresztül, és beilleszti az asztalra.

Jelölje ki a ZIP-fájlt, nyissa meg a helyi menüt, ás válassza az **Összes kibontása** > **Kibontás** lehetőséget.  A fájlok kibontásakor létrejön egy mappa az asztalon, amelynek neve megegyezik a ZIP-fájl nevével.

További információ a [telepítőcsomag tartalmáról](service-fabric-cluster-standalone-package-contents.md).

## <a name="set-up-your-configuration-file"></a>A konfigurációs fájl beállítása

Egy három csomópontos Windows-fürtöt hoz létre, ezért módosítania kell a `ClusterConfig.Unsecure.MultiMachine.json` fájlt.

Ezután frissítse a három a fájlban a 8., 15. és 22. sorokban lévő ipAddress sorokat az egyes példányok IP-címére.

A csomópontok a frissítésük után a következőképpen jelennek meg:

```json
        {
            "nodeName": "vm0",
            "ipAddress": "172.31.27.1",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD0"
        }
```

Ezután frissítenie kell néhány tulajdonságot.  A 34. sorban módosítania kell a diagnosztikai tároló kapcsolati sztringjét. A módosítás után a következőképpen kell kinéznie: `"connectionstring": "C:\\ProgramData\\SF\\DiagnosticsStore"`

Végül a konfiguráció `nodeTypes` szakaszában adjon hozzá egy új szakaszt a Windows által használt rövid élettartamú portok leképezéséhez.  A konfigurációs fájlnak a következőképpen kell kinéznie:

```json
"applicationPorts": {
    "startPort": "20001",
    "endPort": "20031"
},
"ephemeralPorts": {
    "startPort": "20606",
    "endPort": "20861"
},
"isPrimary": true
```

## <a name="validate-the-environment"></a>A környezet érvényesítése

Az önálló csomagban lévő *TestConfiguration.ps1* szkript az ajánlott eljárásokat elemző eszközként használható annak ellenőrzésére, hogy egy fürt az adott környezetben üzembe helyezhető-e. Az [Üzembe helyezés előkészítése](service-fabric-cluster-standalone-deployment-preparation.md) az előfeltételeket és a környezeti követelményeket sorolja fel. A szkript futtatásával ellenőrizze, hogy létre tudja-e hozni a fejlesztési fürtöt:

```powershell
cd .\Desktop\Microsoft.Azure.ServiceFabric.WindowsServer.6.2.274.9494\
.\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
```

Az alábbihoz hasonló kimenet jelenik meg. Az alsó „Sikerült” mezőt `True` értékkel adja vissza a rendszer, az épségtesztek sikerültek, és a fürt üzembe helyezhetőnek tűnik a bemeneti konfiguráció alapján.

```powershell
Trace folder already exists. Traces will be written to existing trace folder: C:\Users\Administrator\Desktop\Microsoft.Azure.ServiceFabric.WindowsServer.6.2.274.9494\DeploymentTraces
Running Best Practices Analyzer...
Best Practices Analyzer completed successfully.


LocalAdminPrivilege        : True
IsJsonValid                : True
IsCabValid                 :
RequiredPortsOpen          : True
RemoteRegistryAvailable    : True
FirewallAvailable          : True
RpcCheckPassed             : True
NoConflictingInstallations : True
FabricInstallable          : True
DataDrivesAvailable        : True
NoDomainController         : True
Passed                     : True
```

## <a name="create-the-cluster"></a>A fürt létrehozása

Miután sikeresen ellenőrizte a fürtkonfigurációt, a *CreateServiceFabricCluster.ps1* szkript futtatásával helyezze üzembe a Service Fabric-fürtöt a konfigurációs fájlban szereplő virtuális gépeken.

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json -AcceptEULA
```

Ha minden működik, az alábbihoz hasonló kimenetet kap:

```powershell
Your cluster is successfully created! You can connect and manage your cluster using Microsoft Azure Service Fabric Explorer or PowerShell. To connect through PowerShell, run 'Connect-ServiceFabricCluster [ClusterConnectionEndpoint]'.
```

> [!NOTE]
> Az üzembe helyezés nyomait arra a gépre/virtuális gépre írja a rendszer, amelyen a CreateServiceFabricCluster.ps1 PowerShell-szkriptet futtatta. Ezek a DeploymentTraces nevű almappában találhatók, azon könyvtár alapján, amelyből a szkriptet futtatta. Ha ellenőrizni kívánja, hogy a Service Fabric megfelelően lett-e üzembe helyezve a gépen, keresse meg a telepített fájlokat a FabricDataRoot könyvtárban, ahogy az a fürtkonfigurációs fájl FabricSettings szakaszában szerepel (alapértelmezés szerint ez a c:\ProgramData\SF). A FabricHost.exe és a Fabric.exe folyamat futása is látható a Feladatkezelőben.
>
>

### <a name="bring-up-service-fabric-explorer"></a>A Service Fabric Explorer megnyitása

Most már csatlakozhat a fürthöz Service Fabric Explorer vagy\/közvetlenül az egyik gépen, http:/localhost:19080/Explorer/index.html vagy távolról, http:\//<IPAddressofaMachine > használatával : 19080/Explorer/index.html.

## <a name="add-and-remove-nodes"></a>Csomópontok hozzáadása és eltávolítása

Az üzleti igényei változásával hozzáadhat vagy eltávolíthat csomópontokat az önálló Service Fabric-fürtről. A lépések részletes leírása: [Csomópontok hozzáadása vagy eltávolítása egy önálló Service Fabric-fürtről](service-fabric-cluster-windows-server-add-remove-nodes.md).

## <a name="next-steps"></a>További lépések

A sorozat második részében megismerkedett a nagy mennyiségű véletlenszerű adat tárfiókba történő párhuzamos feltöltésével, többek között a következőkkel:

> [!div class="checklist"]
> * Kapcsolati sztring konfigurálása
> * Az alkalmazás létrehozása
> * Az alkalmazás futtatása
> * A kapcsolatok számának ellenőrzése

A sorozat harmadik részében egy alkalmazást fog telepíteni a létrehozott fürtre.

> [!div class="nextstepaction"]
> [Az alkalmazás telepítése a Service Fabric-fürtre](service-fabric-tutorial-standalone-install-an-application.md)

<!--Image references-->
[Trusted Zone]: ./media/service-fabric-cluster-creation-for-windows-server/TrustedZone.png
