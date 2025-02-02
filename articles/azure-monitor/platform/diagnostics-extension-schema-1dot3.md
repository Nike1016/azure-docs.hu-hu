---
title: Azure Diagnostics bővítmény 1,3-es és újabb konfigurációs sémája
description: A 1,3-es és újabb verziókban az Azure Diagnostics a Microsoft Azure SDK 2,4-es és újabb verzióiban lett leképezve.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: reference
ms.date: 09/20/2018
ms.author: robb
ms.subservice: diagnostic-extension
ms.openlocfilehash: fa03017c35c76d986139eeee00eea8a9b4a00e62
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "60238055"
---
# <a name="azure-diagnostics-13-and-later-configuration-schema"></a>Azure Diagnostics 1,3-es és újabb konfigurációs séma
> [!NOTE]
> A Azure Diagnostics bővítmény a teljesítményszámlálók és más statisztikák gyűjtésére szolgáló összetevő a következő helyekről:
> - Azure Virtual Machines
> - Virtuálisgép-méretezési csoportok
> - Service Fabric
> - Cloud Services
> - Network Security Groups (Hálózati biztonsági csoportok)
>
> Ez a lap csak akkor fontos, ha az egyik szolgáltatást használja.

Ez az oldal a 1,3-es és újabb verziókban érvényes (Azure SDK 2,4 és újabb). Az újabb konfigurációs szakaszban megjegyezhető, hogy milyen verziót adtak hozzá.  

Az itt ismertetett konfigurációs fájl a diagnosztika-figyelő indításakor a diagnosztikai konfigurációs beállítások megadására szolgál.  

A bővítményt más Microsoft diagnosztikai termékekkel (például Azure Monitor) együtt használják, beleértve a Application Insights és a Log Analytics.



Töltse le a nyilvános konfigurációs fájl sémájának definícióját a következő PowerShell-parancs végrehajtásával:  

```powershell  
(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File –Encoding utf8 -FilePath 'C:\temp\WadConfig.xsd'  
```  

További információ a Azure Diagnostics használatáról: [Azure Diagnostics bővítmény](diagnostics-extension-overview.md).  

## <a name="example-of-the-diagnostics-configuration-file"></a>Példa a diagnosztika konfigurációs fájljára  
 Az alábbi példa egy tipikus diagnosztikai konfigurációs fájlt mutat be:  

```xml  
<?xml version="1.0" encoding="utf-8"?>  
<DiagnosticsConfiguration  xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">   
  <PublicConfig>  
    <WadCfg>  
      <DiagnosticMonitorConfiguration overallQuotaInMB="10000">  

        <PerformanceCounters scheduledTransferPeriod="PT1M", sinks="AzureMonitorSink">  
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />  
        </PerformanceCounters>  

        <Directories scheduledTransferPeriod="PT5M">  
          <IISLogs containerName="iislogs" />  
          <FailedRequestLogs containerName="iisfailed" />  

          <DataSources>  
            <DirectoryConfiguration containerName="mynewprocess">  
              <Absolute path="C:\MyNewProcess" expandEnvironment="false" />  
            </DirectoryConfiguration>  
            <DirectoryConfiguration containerName="badapp">  
              <Absolute path="%SYSTEMDRIVE%\BadApp" expandEnvironment="true" />  
            </DirectoryConfiguration>  
            <DirectoryConfiguration containerName="goodapp">  
              <LocalResource name="Skippy" relativePath="..\PeanutButter"/>  
            </DirectoryConfiguration>  
          </DataSources>  

        </Directories>  

        <EtwProviders>  
          <EtwEventSourceProviderConfiguration   
                       provider="MyProviderClass"   
                       scheduledTransferPeriod="PT5M">  
            <Event id="0"/>  
            <Event id="1" eventDestination="errorTable"/>  
            <DefaultEvents />  
          </EtwEventSourceProviderConfiguration>  
          <EtwManifestProviderConfiguration provider="5974b00b-84c2-44bc-9e58-3a2451b4e3ad" scheduledTransferLogLevelFilter="Information" scheduledTransferPeriod="PT2M">  
            <Event id="0"/>  
            <DefaultEvents eventDestination="defaultTable"/>  
          </EtwManifestProviderConfiguration>  
        </EtwProviders>  

        <WindowsEventLog scheduledTransferPeriod="PT5M">  
          <DataSource name="System!*[System[Provider[@Name='Microsoft Antimalware']]]"/>  
          <DataSource name="System!*[System[Provider[@Name='NTFS'] and (EventID=55)]]" />  
          <DataSource name="System!*[System[Provider[@Name='disk'] and (EventID=7 or EventID=52 or EventID=55)]]" />  
        </WindowsEventLog>  

        <Logs  bufferQuotaInMB="1024"   
             scheduledTransferPeriod="PT1M"   
             scheduledTransferLogLevelFilter="Verbose"   
             sinks="ApplicationInsights.AppLogs"/>  <!-- sinks attribute added in 1.5 -->  

        <CrashDumps containerName="wad-crashdumps" directoryQuotaPercentage="30" dumpType="Mini">  
          <CrashDumpConfiguration processName="mynewprocess.exe" />  
          <CrashDumpConfiguration processName="badapp.exe"/>  
        </CrashDumps>  

        <DockerSources> <!-- Added in 1.9 -->
          <Stats enabled="true" sampleRate="PT1M" scheduledTransferPeriod="PT1M" />
        </DockerSources>

      </DiagnosticMonitorConfiguration>  

      <SinksConfig>   <!-- Added in 1.5 -->  
        <Sink name="AzureMonitorSink">
            <AzureMonitor> <!-- Added in 1.11 -->
                <resourceId>{insert resourceId}</ResourceId> <!-- Parameter only needed for classic VMs and Classic Cloud Services, exclude VMSS and Resource Manager VMs-->
                <Region>{insert Azure region of resource}</Region> <!-- Parameter only needed for classic VMs and Classic Cloud Services, exclude VMSS and Resource Manager VMs -->
            </AzureMonitor>
        </Sink>
        <Sink name="ApplicationInsights">   
          <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>   
          <Channels>   
            <Channel logLevel="Error" name="Errors"  />   
            <Channel logLevel="Verbose" name="AppLogs"  />   
          </Channels>   
        </Sink>   
        <Sink name="EventHub"> <!-- Added in 1.7 -->
          <EventHub Url="https://myeventhub-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" usePublisherId="false" />
        </Sink>
        <Sink name="secondaryEventHub"> <!-- Added in 1.7 -->
          <EventHub Url="https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub" SharedAccessKeyName="SendRule" usePublisherId="false" />
        </Sink>
        <Sink name="secondaryStorageAccount"> <!-- Added in 1.7 -->
          <StorageAccount name="secondarydiagstorageaccount" endpoint="https://core.windows.net" />
        </Sink>
   </SinksConfig>

  </WadCfg>  

  <StorageAccount>diagstorageaccount</StorageAccount>
  <StorageType>TableAndBlob</StorageType> <!-- Added in 1.8 -->  
  </PublicConfig>  

  <PrivateConfig>  <!-- Added in 1.3 -->  
    <StorageAccount name="" key="" endpoint="" sasToken="{sas token}"  />  <!-- sasToken in Private config added in 1.8.1 -->  
    <EventHub Url="https://myeventhub-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="{base64 encoded key}" />

    <AzureMonitorAccount>
        <ServicePrincipalMeta> <!-- Added in 1.11; only needed for classic VMs and Classic cloud services -->
            <PrincipalId>{Insert service principal clientId}</PrincipalId>
            <Secret>{Insert service principal client secret}</Secret>
        </ServicePrincipalMeta>
    </AzureMonitorAccount>

    <SecondaryStorageAccounts>
       <StorageAccount name="secondarydiagstorageaccount" key="{base64 encoded key}" endpoint="https://core.windows.net" sasToken="{sas token}" />
    </SecondaryStorageAccounts>

    <SecondaryEventHubs>
       <EventHub Url="https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="{base64 encoded key}" />
    </SecondaryEventHubs>

  </PrivateConfig>  
  <IsEnabled>true</IsEnabled>  
</DiagnosticsConfiguration>  

```  
> [!NOTE]
> A nyilvános konfiguráció Azure Monitor fogadó definíciója két tulajdonsággal, resourceId és régióval rendelkezik. Ezek csak a klasszikus virtuális gépekhez és a klasszikus felhőalapú szolgáltatásokhoz szükségesek. Ezek a tulajdonságok nem használhatók Resource Manager-Virtual Machines vagy virtuálisgép-méretezési csoportokhoz.
> A Azure Monitor fogadó számára további privát konfigurációs elem is rendelkezésre áll, amely a résztvevő azonosítóját és titkos kulcsát adja át. Ez csak a klasszikus virtuális gépek és a klasszikus Cloud Services esetén szükséges. A Resource Manager-alapú virtuális gépekhez és a VMSS a privát konfigurációs elem Azure Monitor definíciója kizárható.
>

Az előző XML-konfigurációs fájl JSON-értéke.

A PublicConfig és a PrivateConfig el vannak különítve, mert a legtöbb JSON-használati esetben eltérő változókként lesznek átadva. Ilyen eset például a Resource Manager-sablonok, a virtuálisgép-méretezési csoport PowerShell és a Visual Studio.

```json
"PublicConfig" {
    "WadCfg": {
        "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": 10000,
            "DiagnosticInfrastructureLogs": {
                "scheduledTransferLogLevelFilter": "Error"
            },
            "PerformanceCounters": {
                "scheduledTransferPeriod": "PT1M",
                "sinks": "AzureMonitorSink",
                "PerformanceCounterConfiguration": [
                    {
                        "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                        "sampleRate": "PT1M",
                        "unit": "percent"
                    }
                ]
            },
            "Directories": {
                "scheduledTransferPeriod": "PT5M",
                "IISLogs": {
                    "containerName": "iislogs"
                },
                "FailedRequestLogs": {
                    "containerName": "iisfailed"
                },
                "DataSources": [
                    {
                        "containerName": "mynewprocess",
                        "Absolute": {
                            "path": "C:\\MyNewProcess",
                            "expandEnvironment": false
                        }
                    },
                    {
                        "containerName": "badapp",
                        "Absolute": {
                            "path": "%SYSTEMDRIVE%\\BadApp",
                            "expandEnvironment": true
                        }
                    },
                    {
                        "containerName": "goodapp",
                        "LocalResource": {
                            "relativePath": "..\\PeanutButter",
                            "name": "Skippy"
                        }
                    }
                ]
            },
            "EtwProviders": {
                "sinks": "",
                "EtwEventSourceProviderConfiguration": [
                    {
                        "scheduledTransferPeriod": "PT5M",
                        "provider": "MyProviderClass",
                        "Event": [
                            {
                                "id": 0
                            },
                            {
                                "id": 1,
                                "eventDestination": "errorTable"
                            }
                        ],
                        "DefaultEvents": {
                        }
                    }
                ],
                "EtwManifestProviderConfiguration": [
                    {
                        "scheduledTransferPeriod": "PT2M",
                        "scheduledTransferLogLevelFilter": "Information",
                        "provider": "5974b00b-84c2-44bc-9e58-3a2451b4e3ad",
                        "Event": [
                            {
                                "id": 0
                            }
                        ],
                        "DefaultEvents": {
                        }
                    }
                ]
            },
            "WindowsEventLog": {
                "scheduledTransferPeriod": "PT5M",
                "DataSource": [
                    {
                        "name": "System!*[System[Provider[@Name='Microsoft Antimalware']]]"
                    },
                    {
                        "name": "System!*[System[Provider[@Name='NTFS'] and (EventID=55)]]"
                    },
                    {
                        "name": "System!*[System[Provider[@Name='disk'] and (EventID=7 or EventID=52 or EventID=55)]]"
                    }
                ]
            },
            "Logs": {
                "scheduledTransferPeriod": "PT1M",
                "scheduledTransferLogLevelFilter": "Verbose",
                "sinks": "ApplicationInsights.AppLogs"
            },
            "CrashDumps": {
                "directoryQuotaPercentage": 30,
                "dumpType": "Mini",
                "containerName": "wad-crashdumps",
                "CrashDumpConfiguration": [
                    {
                        "processName": "mynewprocess.exe"
                    },
                    {
                        "processName": "badapp.exe"
                    }
                ]
            }
        },
        "SinksConfig": {
            "Sink": [
                {
                    "name": "AzureMonitorSink",
                    "AzureMonitor":
                    {
                        "ResourceId": "{insert resourceId if a classic VM or cloud service, else property not needed}",
                        "Region": "{insert Azure region of resource if a classic VM or cloud service, else property not needed}"
                    }
                },
                {
                    "name": "ApplicationInsights",
                    "ApplicationInsights": "{Insert InstrumentationKey}",
                    "Channels": {
                        "Channel": [
                            {
                                "logLevel": "Error",
                                "name": "Errors"
                            },
                            {
                                "logLevel": "Verbose",
                                "name": "AppLogs"
                            }
                        ]
                    }
                },
                {
                    "name": "EventHub",
                    "EventHub": {
                        "Url": "https://myeventhub-ns.servicebus.windows.net/diageventhub",
                        "SharedAccessKeyName": "SendRule",
                        "usePublisherId": false
                    }
                },
                {
                    "name": "secondaryEventHub",
                    "EventHub": {
                        "Url": "https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub",
                        "SharedAccessKeyName": "SendRule",
                        "usePublisherId": false
                    }
                },
                {
                    "name": "secondaryStorageAccount",
                    "StorageAccount": {
                        "name": "secondarydiagstorageaccount",
                        "endpoint": "https://core.windows.net"
                    }
                }
            ]
        }
    },
    "StorageAccount": "diagstorageaccount",
    "StorageType": "TableAndBlob"
}
```

> [!NOTE]
> A nyilvános konfiguráció Azure Monitor fogadó definíciója két tulajdonsággal, resourceId és régióval rendelkezik. Ezek csak a klasszikus virtuális gépekhez és a klasszikus felhőalapú szolgáltatásokhoz szükségesek.
> Ezek a tulajdonságok nem használhatók Resource Manager-Virtual Machines vagy virtuálisgép-méretezési csoportokhoz.
>

```json
"PrivateConfig" {
    "storageAccountName": "diagstorageaccount",
    "storageAccountKey": "{base64 encoded key}",
    "storageAccountEndPoint": "https://core.windows.net",
    "storageAccountSasToken": "{sas token}",
    "EventHub": {
        "Url": "https://myeventhub-ns.servicebus.windows.net/diageventhub",
        "SharedAccessKeyName": "SendRule",
        "SharedAccessKey": "{base64 encoded key}"
    },
    "AzureMonitorAccount": {
        "ServicePrincipalMeta": {
            "PrincipalId": "{Insert service principal client Id}",
            "Secret": "{Insert service principal client secret}"
        }
    },
    "SecondaryStorageAccounts": {
        "StorageAccount": [
            {
                "name": "secondarydiagstorageaccount",
                "key": "{base64 encoded key}",
                "endpoint": "https://core.windows.net",
                "sasToken": "{sas token}"
            }
        ]
    },
    "SecondaryEventHubs": {
        "EventHub": [
            {
                "Url": "https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub",
                "SharedAccessKeyName": "SendRule",
                "SharedAccessKey": "{base64 encoded key}"
            }
        ]
    }
}

```

> [!NOTE]
> A Azure Monitor fogadó számára további privát konfigurációs elem található, amely a résztvevő azonosítóját és titkos kulcsát adja át. Ez csak a klasszikus virtuális gépek és a klasszikus Cloud Services esetén szükséges. A Resource Manager-alapú virtuális gépekhez és a VMSS a privát konfigurációs elem Azure Monitor definíciója kizárható.
>


## <a name="reading-this-page"></a>A lap olvasása  
 Az alább látható címkék nagyjából sorrendben jelennek meg az előző példában.  Ha nem lát teljes leírást a várt értékről, keresse meg az elemet vagy attribútumot a lapon.  

## <a name="common-attribute-types"></a>Általános attribútumok típusai  
 a **scheduledTransferPeriod** attribútum több elemben jelenik meg. Az ütemezett átvitelek közötti intervallum a legközelebbi percre kerekítve. Az érték egy [XML "időtartam adattípusa".](https://www.w3schools.com/xml/schema_dtypes_date.asp)


## <a name="diagnosticsconfiguration-element"></a>DiagnosticsConfiguration elem  
 *Fa Gyökér – DiagnosticsConfiguration*

Az 1,3-es verzióban lett hozzáadva.  

A diagnosztikai konfigurációs fájl legfelső szintű eleme.  

**Attribútum** xmlns – a diagnosztika konfigurációs FÁJLjának XML-névtere a következő:  
http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration  


|Gyermek elemek|Leírás|  
|--------------------|-----------------|  
|**PublicConfig**|Kötelező. Ezen a lapon a Leírás máshol található.|  
|**PrivateConfig**|Választható. Ezen a lapon a Leírás máshol található.|  
|**IsEnabled**|Logikai. Ezen a lapon a Leírás máshol található.|  

## <a name="publicconfig-element"></a>PublicConfig elem  
 *Fa Root-DiagnosticsConfiguration – PublicConfig*

 Ismerteti a nyilvános diagnosztika konfigurációját.  

|Gyermek elemek|Leírás|  
|--------------------|-----------------|  
|**WadCfg**|Kötelező. Ezen a lapon a Leírás máshol található.|  
|**StorageAccount**|Annak az Azure Storage-fióknak a neve, amelybe az adattárakat tárolni szeretné. A set-AzureServiceDiagnosticsExtension parancsmag végrehajtásakor paraméterként is megadható.|  
|**StorageType**|*Tábla*, *blob*vagy *TableAndBlob*lehet. A tábla alapértelmezett. Ha a TableAndBlob van kiválasztva, a diagnosztikai adattípust kétszer, egyszer kell megírni.|  
|**LocalResourceDirectory**|Annak a virtuális gépnek a könyvtára, ahol a figyelési ügynök az eseményekre vonatkozó adatgyűjtést tárolja. Ha nem, akkor a rendszer az alapértelmezett könyvtárat használja:<br /><br /> Feldolgozói/webes szerepkör esetén:`C:\Resources\<guid>\directory\<guid>.<RoleName.DiagnosticStore\`<br /><br /> Virtuális gépek esetén:`C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<WADVersion>\WAD<WADVersion>`<br /><br /> A kötelező attribútumok a következők:<br /><br /> - **elérési út** – a rendszer Azure Diagnostics által használandó könyvtára.<br /><br /> - **expandEnvironment** – meghatározza, hogy a környezeti változók ki vannak-e bontva az elérési útban.|  

## <a name="wadcfg-element"></a>WadCFG elem  
 *Fa Gyökér-DiagnosticsConfiguration-PublicConfig-WadCFG*

 Azonosítja és konfigurálja az összegyűjtött telemetria-adatokat.  


## <a name="diagnosticmonitorconfiguration-element"></a>DiagnosticMonitorConfiguration elem
 *Fa Root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration*

 Kötelező

|Attribútumok|Leírás|  
|----------------|-----------------|  
| **overallQuotaInMB** | A Azure Diagnostics által gyűjtött különböző diagnosztikai adatok által felhasználható helyi lemezterület maximális mérete. Az alapértelmezett beállítás 4096 MB.<br />
|**useProxyServer** | Konfigurálja Azure Diagnostics az IE-beállításokban beállított proxykiszolgáló-beállítások használatára.|
|**fogadóként** | 1,5-ben hozzáadva. Választható. A fogadó helyre mutat, hogy az összes olyan gyermekobjektum számára is küldjön diagnosztikai adatokat, amelyek támogatják a mosogatókat. A fogadó példa Application Insights vagy Event Hubs.|  


<br /> <br />

|Gyermek elemek|Leírás|  
|--------------------|-----------------|  
|**CrashDumps**|Ezen a lapon a Leírás máshol található.|  
|**DiagnosticInfrastructureLogs**|A Azure Diagnostics által létrehozott naplók gyűjtésének engedélyezése. A diagnosztikai infrastruktúra naplói a diagnosztikai rendszer hibaelhárításához hasznosak. A választható attribútumok a következők:<br /><br /> - **scheduledTransferLogLevelFilter** – a begyűjtött naplók minimális súlyossági szintjét konfigurálja.<br /><br /> - **scheduledTransferPeriod** – az ütemezett átvitelek közötti időköz a legközelebbi percre kerekítve. Az érték egy [XML "időtartam adattípusa".](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  
|**Címtárak**|Ezen a lapon a Leírás máshol található.|  
|**EtwProviders**|Ezen a lapon a Leírás máshol található.|  
|**Metrikák**|Ezen a lapon a Leírás máshol található.|  
|**PerformanceCounters**|Ezen a lapon a Leírás máshol található.|  
|**WindowsEventLog**|Ezen a lapon a Leírás máshol található.|
|**DockerSources**|Ezen a lapon a Leírás máshol található. |



## <a name="crashdumps-element"></a>CrashDumps Element  
 *Fa Root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-CrashDumps*

 Az összeomlási memóriaképek gyűjtésének engedélyezése.  

|Attribútumok|Leírás|  
|----------------|-----------------|  
|**containerName**|Választható. Az összeomlási memóriaképek tárolására szolgáló Azure Storage-fiókban található blob-tároló neve.|  
|**crashDumpType**|Választható.  A Azure Diagnostics konfigurálja a mini-vagy teljes összeomlási memóriaképek gyűjtésére.|  
|**directoryQuotaPercentage**|Választható.  Az összeomlási memóriaképek számára fenntartott **overallQuotaInMB** százalékos arányát konfigurálja a virtuális gépen.|  

|Gyermek elemek|Leírás|  
|--------------------|-----------------|  
|**CrashDumpConfiguration**|Kötelező. Meghatározza az egyes folyamatok konfigurációs értékeit.<br /><br /> A következő attribútumra is szükség van:<br /><br /> **processName** – annak a folyamatnak a neve, amelyet az összeomlási memóriakép gyűjtéséhez Azure Diagnostics szeretne.|  

## <a name="directories-element"></a>Címtárak elem
 *Fa Root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-könyvtárak*

 Engedélyezi egy könyvtár tartalmának gyűjtését, az IIS nem tudta elérni a hozzáférési kérelmek naplóit és/vagy az IIS-naplókat.  

 Nem kötelező **scheduledTransferPeriod** attribútum. Lásd a korábbi magyarázatot.  

|Gyermek elemek|Leírás|  
|--------------------|-----------------|  
|**IISLogs**|A konfigurációban található elemet is beleértve az IIS-naplók gyűjteményének engedélyezése:<br /><br /> **containerName** – az IIS-naplók tárolására szolgáló Azure Storage-fiókban található blob-tároló neve.|   
|**FailedRequestLogs**|A konfigurációban található elem belefoglalása lehetővé teszi, hogy az IIS-helyekre vagy-alkalmazásokra vonatkozó sikertelen kérelmeket tartalmazó naplók gyűjtése megtörténjen. A nyomkövetési beállításokat is engedélyeznie kell a **rendszeren. Webkiszolgáló** a **web. config fájlban**.|  
|**Adatforrások**|A figyelni kívánt címtárak listája.|




## <a name="datasources-element"></a>Adatforrások eleme  
 *Fa Root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-könyvtárak-adatforrások*

 A figyelni kívánt címtárak listája.  

|Gyermek elemek|Leírás|  
|--------------------|-----------------|  
|**DirectoryConfiguration**|Kötelező. Kötelező attribútum:<br /><br /> **containerName** – a naplófájlok tárolására szolgáló Azure Storage-fiókban található blob-tároló neve.|  





## <a name="directoryconfiguration-element"></a>DirectoryConfiguration elem  
 *Fa Root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-könyvtárak-adatforrások-DirectoryConfiguration*

 Az **abszolút** vagy **LocalResource** elemet is tartalmazhat, de mindkettőt nem.  

|Gyermek elemek|Leírás|  
|--------------------|-----------------|  
|**Abszolút**|A figyelni kívánt könyvtár abszolút elérési útja. A következő attribútumok szükségesek:<br /><br /> - **Elérési út** – a figyelni kívánt könyvtár abszolút elérési útja.<br /><br /> - **expandEnvironment** – azt határozza meg, hogy az elérési úton lévő környezeti változók kibontása megtörténjen-e.|  
|**LocalResource**|A figyelni kívánt helyi erőforráshoz viszonyított elérési út. A kötelező attribútumok a következők:<br /><br /> - **Név** – a figyelni kívánt könyvtárat tartalmazó helyi erőforrás<br /><br /> - **relativePath** – a figyelni kívánt könyvtárat tartalmazó névhez viszonyított elérési út|  



## <a name="etwproviders-element"></a>EtwProviders elem  
 *Fa Root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-EtwProviders*

 A EventSource és/vagy a ETW manifest-alapú szolgáltatók ETW-eseményeinek gyűjteményét konfigurálja.  

|Gyermek elemek|Leírás|  
|--------------------|-----------------|  
|**EtwEventSourceProviderConfiguration**|A [EventSource osztályból](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx)generált események gyűjteményét konfigurálja. Kötelező attribútum:<br /><br /> **szolgáltató** – a EventSource esemény osztályának neve.<br /><br /> A választható attribútumok a következők:<br /><br /> - **scheduledTransferLogLevelFilter** – a minimális súlyossági szint, amelyet át kell vinni a Storage-fiókjába.<br /><br /> - **scheduledTransferPeriod** – az ütemezett átvitelek közötti időköz a legközelebbi percre kerekítve. Az érték egy [XML "időtartam adattípusa".](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  
|**EtwManifestProviderConfiguration**|Kötelező attribútum:<br /><br /> **Provider** – az esemény SZOLGÁLTATÓJÁNAK GUID azonosítója<br /><br /> A választható attribútumok a következők:<br /><br /> - **scheduledTransferLogLevelFilter** – a minimális súlyossági szint, amelyet át kell vinni a Storage-fiókjába.<br /><br /> - **scheduledTransferPeriod** – az ütemezett átvitelek közötti időköz a legközelebbi percre kerekítve. Az érték egy [XML "időtartam adattípusa".](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  



## <a name="etweventsourceproviderconfiguration-element"></a>EtwEventSourceProviderConfiguration Element  
 *Fa Root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-EtwProviders-EtwEventSourceProviderConfiguration*

 A [EventSource osztályból](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx)generált események gyűjteményét konfigurálja.  

|Gyermek elemek|Leírás|  
|--------------------|-----------------|  
|**DefaultEvents**|Nem kötelező attribútum:<br/><br/> **eventDestination** – az eseményeket tároló tábla neve|  
|**Esemény**|Kötelező attribútum:<br /><br /> **azonosító** – az esemény azonosítója.<br /><br /> Nem kötelező attribútum:<br /><br /> **eventDestination** – az eseményeket tároló tábla neve|  



## <a name="etwmanifestproviderconfiguration-element"></a>EtwManifestProviderConfiguration Element  
 *Fa Root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-EtwProviders-EtwManifestProviderConfiguration*

|Gyermek elemek|Leírás|  
|--------------------|-----------------|  
|**DefaultEvents**|Nem kötelező attribútum:<br /><br /> **eventDestination** – az eseményeket tároló tábla neve|  
|**Esemény**|Kötelező attribútum:<br /><br /> **azonosító** – az esemény azonosítója.<br /><br /> Nem kötelező attribútum:<br /><br /> **eventDestination** – az eseményeket tároló tábla neve|  



## <a name="metrics-element"></a>Metrikák elem  
 *Fa Gyökér-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-metrikák*

 Lehetővé teszi a gyors lekérdezésekhez optimalizált teljesítményszámláló-tábla létrehozását. A **PerformanceCounters** elemben definiált minden teljesítményszámláló a teljesítményszámláló táblán kívül a metrikák táblázatban található.  

 A **resourceId** attribútum megadása kötelező.  Annak a virtuális gépnek vagy virtuálisgép-méretezési csoportnak az erőforrás-azonosítója, amelyre a Azure Diagnostics telepíti. Szerezze be a **resourceID** a [Azure Portal](https://portal.azure.com). Válassza a **Tallózás** -> **erőforráscsoportok** ->  **< anév\>** elemet. Kattintson a **Tulajdonságok** csempére, és másolja az értéket az **azonosító** mezőből.  

|Gyermek elemek|Leírás|  
|--------------------|-----------------|  
|**MetricAggregation**|Kötelező attribútum:<br /><br /> **scheduledTransferPeriod** – az ütemezett átvitelek közötti időköz a legközelebbi percre kerekítve. Az érték egy [XML "időtartam adattípusa".](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  



## <a name="performancecounters-element"></a>PerformanceCounters elem  
 *Fa Root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-PerformanceCounters*

 A teljesítményszámlálók gyűjteményének engedélyezése.  

 Nem kötelező attribútum:  

 Nem kötelező **scheduledTransferPeriod** attribútum. Lásd a korábbi magyarázatot.

|Gyermek elem|Leírás|  
|-------------------|-----------------|  
|**PerformanceCounterConfiguration**|A következő attribútumok szükségesek:<br /><br /> - **counterSpecifier** – a teljesítményszámláló neve. Például: `\Processor(_Total)\% Processor Time`. A gazdagépen található teljesítményszámlálók listájának lekéréséhez futtassa a parancsot `typeperf`.<br /><br /> - **mintavételi** – milyen gyakran kell mintát venni a számlálóból.<br /><br /> Nem kötelező attribútum:<br /><br /> **Unit (egység** ) – a számláló mértékegysége.|
|**fogadóként** | 1,5-ben hozzáadva. Választható. A fogadó helyre mutat, és a diagnosztikai információk küldésére is. Például Azure Monitor vagy Event Hubs.|    




## <a name="windowseventlog-element"></a>WindowsEventLog elem
 *Fa Root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-WindowsEventLog*

 Engedélyezi a Windows-eseménynaplók gyűjteményét.  

 Nem kötelező **scheduledTransferPeriod** attribútum. Lásd a korábbi magyarázatot.  

|Gyermek elem|Leírás|  
|-------------------|-----------------|  
|**DataSource**|A gyűjteni kívánt Windows-eseménynaplók. Kötelező attribútum:<br /><br /> **név** – a gyűjteni kívánt Windows-eseményeket leíró XPath-lekérdezés. Példa:<br /><br /> `Application!*[System[(Level <=3)]], System!*[System[(Level <=3)]], System!*[System[Provider[@Name='Microsoft Antimalware']]], Security!*[System[(Level <= 3)]`<br /><br /> Az összes esemény összegyűjtéséhez válassza a "*" lehetőséget|  




## <a name="logs-element"></a>Elemek naplózása  
 *Fa Root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-logs*

 Megtalálható a 1,0-es és a 1,1-es verzióban. Hiányzik a 1,2-es verzióban. Ismét hozzáadva a 1,3-es verzióhoz.  

 Meghatározza az alapszintű Azure-naplók pufferének konfigurációját.  

|Attribútum|Type|Leírás|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|**unsignedInt**|Választható. Meghatározza a fájlrendszer tárterületének maximális számát, amely elérhető a megadott adatmennyiség esetében.<br /><br /> Az alapértelmezett érték a 0.|  
|**scheduledTransferLogLevelFilter**|**string**|Választható. Megadja az átvitt naplóbejegyzések minimális súlyossági szintjét. Az alapértelmezett érték nincs **definiálva**, amely az összes naplót átviszi. A többi lehetséges érték (a legkevesebb információhoz képest) **részletes**, **információ**, **Figyelmeztetés**, **hiba**és **kritikus**.|  
|**scheduledTransferPeriod**|**Időtartam**|Választható. Meghatározza az ütemezett adatátvitelek közötti időközt, a legközelebbi percre kerekítve.<br /><br /> Az alapértelmezett érték a PT0S.|  
|**fogadóként** |**string**| 1,5-ben hozzáadva. Választható. A fogadó helyre mutat, és a diagnosztikai információk küldésére is. Például Application Insights vagy Event Hubs.|  

## <a name="dockersources"></a>DockerSources
 *Fa Root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-DockerSources*

 1,9-ben hozzáadva.

|Elem neve|Leírás|  
|------------------|-----------------|  
|**Statisztikák**|Azt jelzi, hogy a rendszer a Docker-tárolók statisztikáit gyűjti|  

## <a name="sinksconfig-element"></a>SinksConfig elem  
 *Fa Root-DiagnosticsConfiguration-PublicConfig-WadCFG-SinksConfig*

 Azon helyszínek listája, amelyek diagnosztikai adatait küldik, valamint az ezekhez a helyszínekhez társított konfigurációt.  

|Elem neve|Leírás|  
|------------------|-----------------|  
|**Sink**|Ezen a lapon a Leírás máshol található.|  

## <a name="sink-element"></a>Fogadó elem
 *Fa Root-DiagnosticsConfiguration-PublicConfig-WadCFG-SinksConfig-mosogató*

 Az 1,5-es verzióban lett hozzáadva.  

 Azokat a helyszíneket határozza meg, amelyekben diagnosztikai adatként küldenek. Például a Application Insights szolgáltatás.  

|Attribútum|Type|Leírás|  
|---------------|----------|-----------------|  
|**name**|sztring|A sinkname azonosító sztring.|  

|Elem|Type|Leírás|  
|-------------|----------|-----------------|  
|**Application Insights**|Karakterlánc|Csak akkor használható, ha az adatokat Application Insightsba küldi. Egy aktív Application Insights fiók kialakítási kulcsát tartalmazza, amelyhez hozzáféréssel rendelkezik.|  
|**Csatornák**|sztring|Egy minden további szűréshez, amelyet a stream|  

## <a name="channels-element"></a>Csatornák elem  
 *Fa Root-DiagnosticsConfiguration-PublicConfig-WadCFG-SinksConfig-mosogató-csatornák*

 Az 1,5-es verzióban lett hozzáadva.  

 Meghatározza a fogadón keresztül áthaladó naplózási adatstreamek szűrőit.  

|Elem|Type|Leírás|  
|-------------|----------|-----------------|  
|**Csatorna**|sztring|Ezen a lapon a Leírás máshol található.|  

## <a name="channel-element"></a>Csatorna elem
 *Fa Root-DiagnosticsConfiguration-PublicConfig-WadCFG-SinksConfig-mosogató-csatornák-csatorna*

 Az 1,5-es verzióban lett hozzáadva.  

 Azokat a helyszíneket határozza meg, amelyekben diagnosztikai adatként küldenek. Például a Application Insights szolgáltatás.  

|Attribútumok|Type|Leírás|  
|----------------|----------|-----------------|  
|**logLevel**|**string**|Megadja az átvitt naplóbejegyzések minimális súlyossági szintjét. Az alapértelmezett érték nincs **definiálva**, amely az összes naplót átviszi. A többi lehetséges érték (a legkevesebb információhoz képest) **részletes**, **információ**, **Figyelmeztetés**, **hiba**és **kritikus**.|  
|**name**|**string**|A csatorna egyedi neve, amelyre hivatkozni kell|  


## <a name="privateconfig-element"></a>PrivateConfig elem
 *Fa Root-DiagnosticsConfiguration – PrivateConfig*

 Az 1,3-es verzióban lett hozzáadva.  

 Választható  

 A Storage-fiók személyes adatait (név, kulcs és végpont) tárolja. Ezeket az adatokat a rendszer elküldi a virtuális géphez, de nem kérhető le belőle.  

|Gyermek elemek|Leírás|  
|--------------------|-----------------|  
|**StorageAccount**|A használandó Storage-fiók. A következő attribútumok szükségesek<br /><br /> - **név** – a Storage-fiók neve.<br /><br /> - **kulcs** – a Storage-fiók kulcsa.<br /><br /> - **Endpoint (végpont** ) – a Storage-fiók eléréséhez használt végpont. <br /><br /> -**sasToken** (hozzáadott 1.8.1) – a privát konfigurációban a Storage-fiók kulcsa helyett egy SAS-tokent is megadhat. Ha meg van adni, a rendszer figyelmen kívül hagyja a Storage-fiók kulcsát. <br />Az SAS-tokenre vonatkozó követelmények: <br />-Csak a fiók SAS-tokenjét támogatja <br />- *b*, a *t* szolgáltatás típusának megadása kötelező. <br /> - *a*, *c*, *u*, *w* engedélyekre van szükség. <br /> - *c*, *o* típusú erőforrásokra van szükség. <br /> – Csak a HTTPS protokollt támogatja <br /> – A kezdő és a lejárati időre érvényesnek kell lennie.|  


## <a name="isenabled-element"></a>IsEnabled elem  
 *Fa Root-DiagnosticsConfiguration – IsEnabled*

 Logikai. A `true` használatával engedélyezheti a diagnosztikát `false` , vagy letilthatja a diagnosztikát.

