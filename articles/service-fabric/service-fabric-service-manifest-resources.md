---
title: Service Fabric szolgáltatási végpontok meghatározása | Microsoft Docs
description: A végponti erőforrások leírása a szolgáltatás jegyzékfájljában, beleértve a HTTPS-végpontok beállítását is
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: da36cbdb-6531-4dae-88e8-a311ab71520d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 82b6e701a5f76aa4c2cea78417ca9bcbeeb10308
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68927695"
---
# <a name="specify-resources-in-a-service-manifest"></a>Erőforrások meghatározása szolgáltatásjegyzékben
## <a name="overview"></a>Áttekintés
A szolgáltatás jegyzékfájlja lehetővé teszi, hogy a szolgáltatás a lefordított kód módosítása nélkül deklarálja/módosítsa a szolgáltatás által használt erőforrásokat. Az Azure Service Fabric támogatja a végponti erőforrások konfigurációját a szolgáltatáshoz. A szolgáltatás jegyzékfájljában megadott erőforrásokhoz való hozzáférést az alkalmazás jegyzékfájljának SecurityGroup keresztül lehet szabályozni. Az erőforrások deklarációja lehetővé teszi, hogy ezeket az erőforrásokat a központi telepítési időpontra módosítsák, ami azt jelenti, hogy a szolgáltatásnak nincs szüksége új konfigurációs mechanizmus bevezetésére. A ServiceManifest. xml fájl sémájának definíciója a *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*Service Fabric SDK-val és-eszközökkel együtt települ.

## <a name="endpoints"></a>Végpontok
Ha egy végponti erőforrás van definiálva a szolgáltatás jegyzékfájljában, akkor Service Fabric a fenntartott alkalmazás-porttartomány portjait rendeli hozzá, ha nincs explicit módon megadva port. Tekintse meg például az ezen bekezdés után megadott manifest-kódrészletben megadott végpont *ServiceEndpoint1* . Emellett a szolgáltatások egy adott portot is igényelhetnek egy adott erőforrásban. A különböző fürtcsomópontokon futó szolgáltatási replikák különböző portszámokhoz rendelhetők, míg az ugyanazon a csomóponton futó szolgáltatás replikái megoszthatják a portot. A szolgáltatási replikák ezt követően igény szerint használhatják ezeket a portokat a replikáláshoz és az ügyfelek kéréseinek figyeléséhez.

> [!WARNING] 
> A statikus portok megtervezése nem fedi át a ClusterManifest megadott alkalmazási porttartomány tartományát. Ha statikus portot ad meg, az alkalmazás-porttartomány kívülre rendeli, ellenkező esetben a port ütközéseket eredményez. A Release 6.5 CU2 esetében az ütközés észlelése után **Figyelmeztetés** jelenik meg, de az üzemelő példány szinkronizálása a szállított 6,5-viselkedéssel folytatódik. Előfordulhat azonban, hogy az alkalmazás központi telepítését a következő jelentős kiadásokból meggátoljuk.
>

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
    <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
    <Endpoint Name="ServiceEndpoint3" Protocol="https"/>
  </Endpoints>
</Resources>
```

Ha egyetlen szervizcsomagban több kód is található, akkor azt is a **végpontok** szakaszban kell hivatkozni.  Ha például a **ServiceEndpoint2a** és a **ServiceEndpoint2b** azonos szervizcsomagból származó végpontok eltérő kódú csomagokra hivatkoznak, akkor az egyes végpontoknak megfelelő programkódot a következőképpen pontosítjuk:

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint2a" Protocol="http" Port="802" CodePackageRef="Code1"/>
    <Endpoint Name="ServiceEndpoint2b" Protocol="http" Port="801" CodePackageRef="Code2"/>
  </Endpoints>
</Resources>
```

Tekintse át az [állapot-nyilvántartó Reliable Services konfigurálását](service-fabric-reliable-services-configuration.md) , ha többet szeretne megtudni a végpontokról a konfigurációs csomag beállítási fájljáról (Settings. xml).

## <a name="example-specifying-an-http-endpoint-for-your-service"></a>Példa: HTTP-végpont megadása a szolgáltatáshoz
A következő szolgáltatási jegyzékfájl egy TCP-végponti erőforrást és két http-végponti&gt; erőforrást határoz meg az &lt;erőforrások elemben.

A HTTP-végpontok Service Fabric automatikusan ACL-t kapnak.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Stateful1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         This name must match the string used in the RegisterServiceType call in Program.cs. -->
    <StatefulServiceType ServiceTypeName="Stateful1Type" HasPersistedState="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>Stateful1.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an
       independently updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port number on which to
           listen. Note that if your service is partitioned, this port is shared with
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
      <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
      <Endpoint Name="ServiceEndpoint3" Protocol="https"/>

      <!-- This endpoint is used by the replicator for replicating the state of your service.
           This endpoint is configured through the ReplicatorSettings config section in the Settings.xml
           file under the ConfigPackage. -->
      <Endpoint Name="ReplicatorEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="example-specifying-an-https-endpoint-for-your-service"></a>Példa: HTTPS-végpont megadása a szolgáltatáshoz
A HTTPS protokoll kiszolgáló-hitelesítést biztosít, és az ügyfél-kiszolgáló kommunikáció titkosítására is használható. Ha engedélyezni szeretné a HTTPS-t a Service Fabric szolgáltatáson, adja meg a protokollt a szolgáltatás jegyzékfájljának *erőforrások-> végpontok-> végpont* szakaszában, ahogy azt a végpont *ServiceEndpoint3*korábban is mutatja.

> [!NOTE]
> A szolgáltatás protokollja nem módosítható az alkalmazások frissítése közben. Ha a frissítés során módosul, akkor ez a változás megszakítása.
> 

> [!WARNING] 
> HTTPS használata esetén ne használja ugyanazt a portot és tanúsítványt az azonos csomópontra telepített különböző szolgáltatási példányokhoz (az alkalmazástól függetlenül). Ha két különböző szolgáltatást frissít a különböző alkalmazás-példányok ugyanazon portjával, a frissítés sikertelen lesz. További információ: [több alkalmazás frissítése HTTPS ](service-fabric-application-upgrade.md#upgrading-multiple-applications-with-https-endpoints)-végpontokkal.
>

Íme egy példa a HTTPS-re vonatkozó ApplicationManifest. Meg kell adni a tanúsítvány ujjlenyomatát. A EndpointRef a ServiceManifest EndpointResource mutató hivatkozás, amelyhez a HTTPS protokollt be kell állítani. Egyszerre több EndpointCertificate is hozzáadhat.  

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="Application1Type"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
    <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion
       should match the Name and Version attributes of the ServiceManifest element defined in the
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <EndpointBindingPolicy CertificateRef="TestCert1" EndpointRef="ServiceEndpoint3"/>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types when an instance of this
         application type is created. You can also create one or more instances of service type by using the
         Service Fabric PowerShell module.

         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Stateful1">
      <StatefulService ServiceTypeName="Stateful1Type" TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]" MinReplicaSetSize="[Stateful1_ ]">
        <UniformInt64Partition PartitionCount="[Stateful1_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
      </StatefulService>
    </Service>
  </DefaultServices>
  <Certificates>
    <EndpointCertificate Name="TestCert1" X509FindValue="FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF F0" X509StoreName="MY" />  
  </Certificates>
</ApplicationManifest>
```

Linux-fürtök esetén a **saját** áruház alapértelmezett értéke a **/var/lib/sfcerts**mappa.


## <a name="overriding-endpoints-in-servicemanifestxml"></a>Végpontok felülbírálása a ServiceManifest. xml fájlban

A ApplicationManifest adjon hozzá egy ResourceOverrides szakaszt, amely a testvér – ConfigOverrides szakasz lesz. Ebben a szakaszban megadhatja a végpontok szakaszának felülbírálását a szolgáltatás jegyzékfájljában megadott erőforrások szakaszban. A felülbírálási végpontok a Runtime 5.7.217/SDK 2.7.217 és újabb verziókban támogatottak.

A végpontnak a ServiceManifest-ben való felülbírálásához a ApplicationParameters használatával módosítsa a ApplicationManifest a következőképpen:

A ServiceManifestImport szakaszban adjon hozzá egy új "ResourceOverrides" szakaszt.

```xml
<ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <ResourceOverrides>
      <Endpoints>
        <Endpoint Name="ServiceEndpoint" Port="[Port]" Protocol="[Protocol]" Type="[Type]" />
        <Endpoint Name="ServiceEndpoint1" Port="[Port1]" Protocol="[Protocol1] "/>
      </Endpoints>
    </ResourceOverrides>
        <Policies>
           <EndpointBindingPolicy CertificateRef="TestCert1" EndpointRef="ServiceEndpoint"/>
        </Policies>
  </ServiceManifestImport>
```

Adja meg az alábbi paramétereket:

```xml
  <Parameters>
    <Parameter Name="Port" DefaultValue="" />
    <Parameter Name="Protocol" DefaultValue="" />
    <Parameter Name="Type" DefaultValue="" />
    <Parameter Name="Port1" DefaultValue="" />
    <Parameter Name="Protocol1" DefaultValue="" />
  </Parameters>
```

Az alkalmazás telepítése során ezeket az értékeket ApplicationParameters-ként adhatja át.  Példa:

```powershell
PS C:\> New-ServiceFabricApplication -ApplicationName fabric:/myapp -ApplicationTypeName "AppType" -ApplicationTypeVersion "1.0.0" -ApplicationParameter @{Port='1001'; Protocol='https'; Type='Input'; Port1='2001'; Protocol='http'}
```

Megjegyzés: Ha a ApplicationParameters megadott értékek üresek, térjen vissza a megfelelő Végpontneve ServiceManifest megadott alapértelmezett értékre.

Példa:

Ha a megadott ServiceManifest

```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint1" Protocol="tcp"/>
    </Endpoints>
  </Resources>
```

Az alkalmazás paramétereinek Port1 és Protocol1 értéke null vagy üres. A portot a ServiceFabric továbbra is eldönti. A protokoll pedig a TCP protokollt fogja kimutatni.

Tegyük fel, hogy helytelen értéket ad meg. A porthoz hasonlóan a "foo" sztring értéket adta meg egy int helyett.  A New-ServiceFabricApplication parancs hibával meghiúsul: A (z) "ResourceOverrides" szakasz "Port1" nevű "ServiceEndpoint1" attribútumát tartalmazó felülbírálási paraméter érvénytelen. A megadott érték a "foo" és a Required értéke "int".
