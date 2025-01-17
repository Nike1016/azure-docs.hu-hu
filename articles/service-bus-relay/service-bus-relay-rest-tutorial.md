---
title: REST-oktatóanyag a Azure Relay használatával | Microsoft Docs
description: Hozzon létre egy egyszerű Azure Service Bus Relay-gazda alkalmazást, amely egy REST-alapú felületet tesz elérhetővé.
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: 1312b2db-94c4-4a48-b815-c5deb5b77a6a
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/06/2018
ms.author: spelluru
ms.openlocfilehash: 4e988724f842ff12cd599eba95c31006fe208fad
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68422895"
---
# <a name="azure-wcf-relay-rest-tutorial"></a>Azure WCF Relay REST-oktatóanyag
Ez az oktatóanyag azt ismerteti, hogyan hozhat létre egy olyan egyszerű Azure Relay gazda alkalmazást, amely egy REST-alapú felületet tesz elérhetővé. A REST lehetővé teszi egy webes ügyfél, például egy webes böngésző számára, hogy hozzáférjen a HTTP-kérelmeken keresztül a Service Bus alkalmazásprogramozási felületekhez.

Az oktatóanyag a Windows Communication Foundation (WCF) REST programozási modellt használja a REST-szolgáltatás Azure Relayn való létrehozásához. További információt a WCF-dokumentáció [WCF REST Programming Model](/dotnet/framework/wcf/feature-details/wcf-web-http-programming-model) (WCF REST programozási modell) és [Designing and Implementing Services](/dotnet/framework/wcf/designing-and-implementing-services) (Szolgáltatások tervezése és megvalósítása) témakörében találhat.

Ebben az oktatóanyagban az alábbi lépéseket kell elvégeznie:

> [!div class="checklist"]
> * Hozzon létre egy Relay-névteret.
> * REST-alapú WCF szolgáltatási szerződés megadása
> * A REST-alapú WCF-szerződés megvalósítása
> * A REST-alapú WCF szolgáltatás üzemeltetése és futtatása
> * A szolgáltatás futtatása és tesztelése

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következő előfeltételekre lesz szüksége:

- Azure-előfizetés. Ha még nincs előfizetése, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/), mielőtt hozzákezd.
- [Visual Studio 2015 vagy újabb](https://www.visualstudio.com). A jelen oktatóanyag példái a Visual Studio 2017-et használják.
- Azure SDK a .NET-hez. Telepítse az [SDK letöltési oldaláról](https://azure.microsoft.com/downloads/).

## <a name="create-a-relay-namespace"></a>Relay-névtér létrehozása

A Relay-funkciók Azure-ban való használatához először létre kell hoznia egy szolgáltatásnévteret. A névtér egy hatókörkezelési tárolót biztosít az Azure erőforrásainak címzéséhez az alkalmazáson belül. Relay-névtér létrehozásához kövesse az [itt leírt utasításokat](relay-create-namespace-portal.md).

## <a name="define-a-rest-based-wcf-service-contract-to-use-with-azure-relay"></a>A Azure Relayhoz használni kívánt REST-alapú WCF szolgáltatási szerződés megadása

WCF REST-stílusú szolgáltatás létrehozásakor meg kell határoznia a szerződést. A szerződés megadja a gazdagép által támogatott műveleteket. A szolgáltatási művelet tekinthető webszolgáltatási módszernek. A szerződések a C++, a C# vagy a Visual Basic felület meghatározásával jönnek létre. A felület minden metódusa egy konkrét szolgáltatási műveletnek felel meg. A [ServiceContractAttriibute](/dotnet/api/system.servicemodel.servicecontractattribute) attribútumot minden felületre, az [OperationContractAttribaute](/dotnet/api/system.servicemodel.operationcontractattribute) attribútumot pedig minden műveletre alkalmazni kell. Ha egy felület egy metódusa rendelkezik a [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) attribútummal, de nem rendelkezik az [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute) attribútummal, nem lesz közzétéve. A feladatokhoz használt kód megtalálható az eljárást követő példában.

A WCF-szerződések és a REST-stílusú szerződések közötti elsődleges különbség egy tulajdonság hozzáadása a [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute): [WebGetAttribute](/dotnet/api/system.servicemodel.web.webgetattribute). Ez a tulajdonság lehetővé teszi a felület egy metódusának leképezését egy, a felület másik oldalán levő metódussá. Ez a példa a [WebGetAttribute](/dotnet/api/system.servicemodel.web.webgetattribute) attribútum használatával kapcsolja össze a metódust a HTTP Get értékkel. Ez lehetővé teszi, hogy Service Bus az interfésznek eljuttatott parancsok pontos lekérését és értelmezését.

### <a name="to-create-a-contract-with-an-interface"></a>Szerződés létrehozása illesztőfelülettel

1. Nyissa meg a Visual Studiót rendszergazdaként: ehhez a **Start** menüben kattintson a jobb gombbal a programra, majd kattintson a **Futtatás rendszergazdaként** parancsra.
2. Hozzon létre új egy új konzolalkalmazás-projektet. Kattintson a **File** (Fájl) menüre, és válassza a **New** (Új), majd a **Project** (Projekt) elemet. A **New Project** (Új projekt) párbeszédpanelen kattintson a **Visual C#** elemre, válassza ki a **Console Application** (Konzolalkalmazás) sablont, és nevezze el **ImageListener** néven. Használja az alapértelmezett **Location** (Hely) értéket. A projekt létrehozásához kattintson az **OK** gombra.
3. C# projekt esetében a Visual Studio létrehoz egy `Program.cs` fájlt. Ez az osztály tartalmaz egy üres `Main()` metódust, amely szükséges a konzolalkalmazás projektek helyes létrejöttéhez.
4. Adjon hozzá Service Bus- és **System.ServiceModel.dll**-hivatkozásokat a projekthez a Service Bus NuGet csomag telepítésével. Ez a csomag automatikusan hivatkozásokat ad a Service Bus-könyvtárakhoz, valamint a WCF **System.ServiceModel** névtérhez. A Solution Explorerben (Megoldáskezelőben) kattintson a jobb gombbal az **ImageListener** projektre, majd kattintson a **Manage NuGet Packages** (NuGet-csomagok kezelése) parancsra. Kattintson a **Browse** (Tallózás) lapra, és keressen a következőre: `Microsoft Azure Service Bus`. Kattintson az **Install** (Telepítés) gombra, és fogadja el a használati feltételeket.
5. Expliciten hozzá kell adnia egy hivatkozást a projekt **System.ServiceModel.Web.dll** fájljához:
   
    a. A Solution Explorerben (Megoldáskezelőben) kattintson a jobb gombbal a **References** (Hivatkozások) mappára a projektmappa területén, majd kattintson az **Add Reference** (Hivatkozás hozzáadása) lehetőségre.
   
    b. Az **Add Reference** (Hivatkozás hozzáadása) párbeszédablakban kattintson a bal oldalon a **Framework** (Keretrendszer) lapra, majd a **Search** (Keresés) mezőben írja be a **System.ServiceModel.Web** kifejezést. Jelölje be a **System.ServiceModel.Web** jelölőnégyzetet, majd kattintson az **OK** lehetőségre.
6. Adja hozzá a következő `using` utasításokat a Program.cs fájl elejéhez:
   
    ```csharp
    using System.ServiceModel;
    using System.ServiceModel.Channels;
    using System.ServiceModel.Web;
    using System.IO;
    ```
   
    A [System.ServiceModel](/dotnet/api/system.servicemodel) az a névtér, amely lehetővé teszi a programozott hozzáférést a WCF alapszintű szolgáltatásaihoz. WCF Relay a WCF számos objektumát és attribútumát használja a szolgáltatási szerződések definiálásához. Ezt a névteret a továbbító alkalmazásai többségében használhatja. Hasonlóképpen, a [System. ServiceModel. Channels](/dotnet/api/system.servicemodel.channels) segít meghatározni a csatornát, amely a Azure Relay és az ügyfél webböngészőjével kommunikáló objektum. Végül a [System.ServiceModel.Web](/dotnet/api/system.servicemodel.web) tartalmazza a webalapú alkalmazások létrehozását engedélyező típusokat.
7. Nevezze át az `ImageListener` névteret **Microsoft.ServiceBus.Samples** névre.
   
    ```csharp
    namespace Microsoft.ServiceBus.Samples
    {
        ...
    ```
8. Közvetlenül a névtér-deklaráció nyitó zárójele után adjon meg egy új, **IImageContract** nevű felületet, és alkalmazza a **ServiceContractAttribute** attribútumot a felületen `https://samples.microsoft.com/ServiceModel/Relay/` értékkel. A névtér értéke különbözik a kód tartományában használt névtértől. A névtér értéke egyedi azonosítóként van használatban ehhez a szerződéshez, és rendelkeznie kell a verzióinformációkkal. További információ: [Service Versioning](https://go.microsoft.com/fwlink/?LinkID=180498) (Szolgáltatás verziószámozása). A névtér explicit meghatározásával megelőzhető az alapértelmezett névtér hozzáadása a szerződésnévhez.
   
    ```csharp
    [ServiceContract(Name = "ImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/RESTTutorial1")]
    public interface IImageContract
    {
    }
    ```
9. Az `IImageContract` felületen belül deklaráljon egy metódust az `IImageContract` szerződés által a felületen közzétett egyetlen művelethez, valamint alkalmazza az `OperationContractAttribute` attribútumot a nyilvános Service Bus szerződés részeként közzétenni kívánt metódusra.
   
    ```csharp
    public interface IImageContract
    {
        [OperationContract]
        Stream GetImage();
    }
    ```
10. Az **OperationContract** attribútumban adja hozzá a **WebGet** értéket.
    
    ```csharp
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }
    ```
    
    Ez lehetővé teszi `GetImage`, hogy a Relay szolgáltatás a HTTP Get kérelmeket átirányítsa a `GetImage` rendszerbe, és lefordítsa a visszatérési értékeit egy http-GETRESPONSE válaszba. Az oktatóanyagban később egy webböngészőt használhat majd a metódus eléréséhez és a kép megjelenítéséhez böngészőben.
11. Közvetlenül az `IImageContract` definíciója után deklaráljon egy csatornát, amely örökli az `IImageContract` és az `IClientChannel` felületek tulajdonságait is.
    
    ```csharp
    public interface IImageChannel : IImageContract, IClientChannel { }
    ```
    
    A csatorna egy olyan WCF-objektum, amelyen keresztül a szolgáltatás és az ügyfél információkat adnak át egymásnak. Később létrehozza a csatornát a gazda alkalmazásban. Azure Relay ezután ezt a csatornát használja a HTTP GET kérések átadására a böngészőből a **GetImage** megvalósításához. A továbbító a csatornát is használja a **GetImage** visszatérési értékének kiszámításához és az ÜGYFÉLOLDALI böngésző http-GETRESPONSE való lefordításához.
12. A **Build** (Létrehozás) menüben kattintson a **Build Solution** (Megoldás létrehozása) elemre az eddigi munkája pontosságának ellenőrzéséhez.

### <a name="example"></a>Példa
A következő kód egy alapszintű felületet mutat be, amely meghatározza a WCF Relay szerződést.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## <a name="implement-the-rest-based-wcf-service-contract"></a>A REST-alapú WCF szolgáltatási szerződés implementálása
A REST-stílusú WCF Relay szolgáltatás létrehozásához először létre kell hoznia a szerződést, amelyet egy felület használatával határozhat meg. A következő lépés a felület megvalósítása. Ebbe beletartozik egy **ImageService** nevű osztály létrehozása, amely megvalósítja a felhasználó által megadott **IImageContract** felületet. A szerződés megvalósítása után egy App.config fájl segítségével konfigurálhatja a felületet. A konfigurációs fájl tartalmazza az alkalmazáshoz szükséges információkat, például a szolgáltatás nevét, a szerződés nevét, valamint a továbbítási szolgáltatással való kommunikációhoz használt protokoll típusát. A feladatokhoz használt kód megtalálható az eljárást követő példában.

Csakúgy, mint az előző lépéseknél, nagyon kicsi a különbség a REST-stílusú szerződések megvalósítása és a WCF Relay szerződések között.

### <a name="to-implement-a-rest-style-service-bus-contract"></a>REST-stílusú Service Bus szerződés megvalósítása
1. Hozzon létre egy új, **ImageService** nevű osztályt közvetlenül az **IImageContract** felület meghatározása után. Az **ImageService** osztály az **IImageContract** felületet valósítja meg.
   
    ```csharp
    class ImageService : IImageContract
    {
    }
    ```
    Hasonlóan az egyéb felületi megvalósításokhoz, a definíciót megvalósíthatja egy másik fájlban. Ebben az oktatóanyagban azonban a megvalósítás ugyanabban a fájlban jelenik meg, mint a felületdefiníció és a `Main()` metódus.
2. Alkalmazza a [ServiceBehaviorAttribute](/dotnet/api/system.servicemodel.servicebehaviorattribute) attribútumot az **IImageService** osztályra, így jelezheti, hogy az osztály egy WCF-szerződés megvalósítása.
   
    ```csharp
    [ServiceBehavior(Name = "ImageService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
    }
    ```
   
    Amint azt korábban említettük, ez a névtér nem egy hagyományos névtér. Ehelyett ez a szerződést azonosító WCF-architektúra része. További információt a WCF dokumentációjának [adategyezmények nevei](https://msdn.microsoft.com/library/ms731045.aspx) című cikkében talál.
3. Adjon hozzá egy .jpg képet a projekthez.  
   
    Ez egy kép, amelyet a szolgáltatás megjelenít a fogadó böngészőben. Kattintson a jobb gombbal a projektre, majd kattintson az **Add** (Hozzáadás) lehetőségre. Ezután kattintson az **Existing Item** (Meglévő elem) elemre. Az **Add Existing Item** (Meglévő elem hozzáadása) párbeszédablak segítségével keressen egy megfelelő .jpg fájlt, és kattintson az **Add** (Hozzáadás) lehetőségre.
   
    Egy fájl hozzáadásakor győződjön meg arról, hogy a legördülő menüben kiválasztotta az **All Files** (Minden fájl) lehetőséget a **File nane:** (Fájlnév:) mező mellett. Az oktatóanyag hátralevő része feltételezi, hogy a kép neve image.jpg. Ha más fájllal rendelkezik, át kell neveznie a képet, vagy módosítania kell a kódot a kompenzálására.
4. Annak biztosításához, hogy a futó szolgáltatás megtalálja a képfájlt, a **Solution Explorerben** (Megoldáskezelőben) kattintson a jobb gombbal képfájlra, majd kattintson a **Properties** (Tulajdonságok) lehetőségre. A **Properties** (Tulajdonságok) panelen állítsa a **Copy to Output Directory** (Másolás a kimeneti könyvtárba) beállítást **Copy if newer** (Másolás, ha újabb) értékre.
5. Adjon egy hivatkozást a projekt **System.Drawing.dll** szerelvényéhez, és adja hozzá az alábbi társított `using` utasításokat is.  
   
    ```csharp
    using System.Drawing;
    using System.Drawing.Imaging;
    using Microsoft.ServiceBus;
    using Microsoft.ServiceBus.Web;
    ```
6. Az **ImageService** osztályban adja hozzá az alábbi konstruktort, amely betölti a bitképet, és előkészíti az ügyfél böngészőjébe való küldéshez.
   
    ```csharp
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";
   
        Image bitmap;
   
        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }
    }
    ```
7. Közvetlenül az előző kód után adja hozzá az alábbi **GetImage** metódust az **ImageService** osztályban egy, a képet tartalmazó HTTP-üzenet visszaadásához.
   
    ```csharp
    public Stream GetImage()
    {
        MemoryStream stream = new MemoryStream();
        this.bitmap.Save(stream, ImageFormat.Jpeg);
   
        stream.Position = 0;
        WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";
   
        return stream;
    }
    ```
   
    Ez a megvalósítás a **MemoryStream** segítségével kéri le a képet, és előkészíti a böngészőbe történő streameléshez. Nullánál indítja el a streamelési pozíciót, jpeg-ként deklarálja a stream tartalmát, valamint streameli az információt.
8. A **Build** (Létrehozás) menüben kattintson a **Build Solution** (Megoldás létrehozása) elemre.

### <a name="to-define-the-configuration-for-running-the-web-service-on-service-bus"></a>A konfiguráció meghatározása a webszolgáltatás Service Buson való futtatásához
1. A **Solution Explorerben** (Megoldáskezelőben) kattintson duplán az **App.config** fájlra a Visual Studio-szerkesztőben való megnyitásához.
   
    Az **app. config** fájl tartalmazza a szolgáltatás nevét, végpontját (azaz azt a helyet, Azure Relay elérhetővé teszi az ügyfelek és a gazdagépek számára az egymással való kommunikációt), valamint a kötést (a kommunikációhoz használt protokoll típusát). A fő különbség az, hogy a konfigurált szolgáltatási végpont egy [WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) kötésre hivatkozik.
2. A `<system.serviceModel>` XML-elem egy WCF-elem, amely egy vagy több szolgáltatást határoz meg. Itt a szolgáltatás nevének és végpontjának meghatározására szolgál. A `<system.serviceModel>` elem aljánál (de még a `<bindings>` elemen belül), adjon hozzá egy, az alábbi tartalommal rendelkező `<system.serviceModel>` elemet. Ez határozza meg az alkalmazásban használt kötéseket. Meghatározhat több kötést, de ez az oktatóanyag csak egyet határoz meg.
   
    ```xml
    <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
            <binding name="default">
                <security relayClientAuthenticationType="None" />
            </binding>
        </webHttpRelayBinding>
    </bindings>
    ```
   
    Az előző kód egy WCF Relay [WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) -kötést határoz meg a **relayClientAuthenticationType** beállítással a **none**értékre. Ez a beállítás jelöli, ha egy, a kötést használó végpont nem igényel ügyfél-hitelesítőt.
3. A `<bindings>` elem után adjon hozzá egy `<services>` elemet. A kötésekhez hasonlóan megadhat több szolgáltatást is egyetlen konfigurációs fájlon belül. Ez az oktatóanyag azonban csak egyet ad meg.
   
    ```xml
    <services>
        <!-- Application Service -->
        <service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
            <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IImageContract"
                    binding="webHttpRelayBinding"
                    bindingConfiguration="default"
                    behaviorConfiguration="sbTokenProvider"
                    address="" />
        </service>
    </services>
    ```
   
    Ez a lépés konfigurál egy szolgáltatást, amely a korábban meghatározott alapértelmezett **webHttpRelayBinding** elemet használja. Használja az alapértelmezett **sbTokenProvider** elemet is, amely a következő lépésben lesz meghatározva.
4. Az `<services>` elem után hozzon létre `<behaviors>` egy elemet a következő tartalommal, és cserélje le a "SAS_KEY" kifejezést a [Azure Portal][Azure portal]által korábban beszerzett *közös hozzáférés* -aláírási (SAS-) kulcsra.
   
    ```xml
    <behaviors>
        <endpointBehaviors>
            <behavior name="sbTokenProvider">
                <transportClientEndpointBehavior>
                    <tokenProvider>
                        <sharedAccessSignature keyName="RootManageSharedAccessKey" key="YOUR_SAS_KEY" />
                    </tokenProvider>
                </transportClientEndpointBehavior>
            </behavior>
            </endpointBehaviors>
            <serviceBehaviors>
                <behavior name="default">
                    <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
                </behavior>
            </serviceBehaviors>
    </behaviors>
    ```
5. Még mindig az App.config fájlban, az `<appSettings>` elemben cserélje le a teljes kapcsolati sztring értékét a korábban a portálról beszerzett kapcsolati sztringre. 
   
    ```xml
    <appSettings>
       <!-- Service Bus specific app settings for messaging connections -->
       <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SAS_KEY"/>
    </appSettings>
    ```
6. A teljes megoldás létrehozásához a **Build** (Létrehozás) menüben kattintson a **Build Solution** (Megoldás létrehozása) elemre.

### <a name="example"></a>Példa
Az alábbi kód bemutatja a szerződés és a szolgáltatás megvalósítását egy REST-alapú szolgáltatáshoz, amely a **WebHttpRelayBinding** kötés használatával fut a Service Buson.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{


    [ServiceContract(Name = "ImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

A következő példa a szolgáltatáshoz társított App.config fájlt mutatja be.

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2"/>
    </startup>
    <system.serviceModel>
        <extensions>
            <!-- In this extension section we are introducing all known service bus extensions. User can remove the ones they don't need. -->
            <behaviorExtensions>
                <add name="connectionStatusBehavior"
                    type="Microsoft.ServiceBus.Configuration.ConnectionStatusElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="transportClientEndpointBehavior"
                    type="Microsoft.ServiceBus.Configuration.TransportClientEndpointBehaviorElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="serviceRegistrySettings"
                    type="Microsoft.ServiceBus.Configuration.ServiceRegistrySettingsElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </behaviorExtensions>
            <bindingElementExtensions>
                <add name="netMessagingTransport"
                    type="Microsoft.ServiceBus.Messaging.Configuration.NetMessagingTransportExtensionElement, Microsoft.ServiceBus,  Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="tcpRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.TcpRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="httpRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.HttpRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="httpsRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.HttpsRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="onewayRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.RelayedOnewayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </bindingElementExtensions>
            <bindingExtensions>
                <add name="basicHttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.BasicHttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="webHttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.WebHttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="ws2007HttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.WS2007HttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netOnewayRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetOnewayRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netEventRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetEventRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netMessagingBinding"
                    type="Microsoft.ServiceBus.Messaging.Configuration.NetMessagingBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </bindingExtensions>
        </extensions>
      <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
          <binding name="default">
            <security relayClientAuthenticationType="None" />
          </binding>
        </webHttpRelayBinding>
      </bindings>
      <services>
        <!-- Application Service -->
        <service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
          <endpoint name="RelayEndpoint"
                  contract="Microsoft.ServiceBus.Samples.IImageContract"
                  binding="webHttpRelayBinding"
                  bindingConfiguration="default"
                  behaviorConfiguration="sbTokenProvider"
                  address="" />
        </service>
      </services>
      <behaviors>
        <endpointBehaviors>
          <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
              <tokenProvider>
                <sharedAccessSignature keyName="RootManageSharedAccessKey" key="YOUR_SAS_KEY" />
              </tokenProvider>
            </transportClientEndpointBehavior>
          </behavior>
        </endpointBehaviors>
        <serviceBehaviors>
          <behavior name="default">
            <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
          </behavior>
        </serviceBehaviors>
      </behaviors>
    </system.serviceModel>
    <appSettings>
        <!-- Service Bus specific app settings for messaging connections -->
        <add key="Microsoft.ServiceBus.ConnectionString"
            value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SAS_KEY>"/>
    </appSettings>
</configuration>
```

## <a name="host-the-rest-based-wcf-service-to-use-azure-relay"></a>A REST-alapú WCF szolgáltatás üzemeltetése Azure Relay
Ez a lépés azt ismerteti, hogyan futtathat webszolgáltatásokat egy WCF Relay használatával rendelkező Console-alkalmazással. Az ebben a lépésben írt kód teljes listája megtalálható az eljárást követő példában.

### <a name="to-create-a-base-address-for-the-service"></a>Alapszintű cím létrehozása a szolgáltatáshoz
1. `Main()` A Function deklarációban hozzon létre egy változót a projekt névterének tárolásához. Ügyeljen arra, hogy `yourNamespace` a helyére a korábban létrehozott Relay-névtér nevét írja.
   
    ```csharp
    string serviceNamespace = "yourNamespace";
    ```
    A Service Bus a névtér nevét használva létrehoz egy egyedi URI-t.
2. Hozzon létre egy `Uri`-példányt a névtéren alapuló szolgáltatás alapszintű címéhez.
   
    ```csharp
    Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");
    ```

### <a name="to-create-and-configure-the-web-service-host"></a>A webszolgáltatásgazda létrehozása és konfigurálása
* Hozza létre a webszolgáltatás gazdáját a szakaszban korábban létrehozott URI-cím használatával.
  
    ```csharp
    WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
    ```
    A szolgáltatásgazda az a WCF-objektum, amely a gazdaalkalmazást példányosítja. Ez a példa továbbítja a létrehozni kívánt gazda típusát (**ImageService**), valamint a címet, ahol közzé kívánja tenni a gazdaalkalmazást.

### <a name="to-run-the-web-service-host"></a>A webszolgáltatásgazda futtatása
1. Nyissa meg a szolgáltatást.
   
    ```csharp
    host.Open();
    ```
    A szolgáltatás jelenleg fut.
2. Megjelenít egy üzenetet, amely jelzi, hogy a szolgáltatás fut, valamint a szolgáltatás leállításának módját.
   
    ```csharp
    Console.WriteLine("Copy the following address into a browser to see the image: ");
    Console.WriteLine(address + "GetImage");
    Console.WriteLine();
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```
3. Ha kész, zárja be a szolgáltatásgazdát.
   
    ```csharp
    host.Close();
    ```

### <a name="example"></a>Példa
Az alábbi példa tartalmazza a szolgáltatási szerződést és a megvalósítását az oktatóanyag előző lépéseiből, és egy konzolalkalmazásban működteti a szolgáltatást. Fordítsa le az alábbi kódot egy ImageListener.exe nevű végrehajtható fájlba.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "ImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            string serviceNamespace = "InsertServiceNamespaceHere";
            Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");

            WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
            host.Open();

            Console.WriteLine("Copy the following address into a browser to see the image: ");
            Console.WriteLine(address + "GetImage");
            Console.WriteLine();
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            host.Close();
        }
    }
}
```

## <a name="run-and-test-the-service"></a>A szolgáltatás futtatása és tesztelése
A megoldás létrehozása után az alábbi lépéseket követve futtathatja az alkalmazást:

1. A szolgáltatás futtatásához nyomja le az **F5** billentyűt, vagy lépjen a végrehajtható fájlhoz (ImageListener\bin\Debug\ImageListener.exe). Nem állítsa le az alkalmazás futását, mert szükség van rá a következő lépés elvégzéséhez.
2. A kép megtekintéséhez másolja és illessze be a címet a parancssorból egy böngészőbe.
3. Ha kész van, a parancssori ablakban az **Enter** billentyűt lenyomva zárja be az alkalmazást.

## <a name="next-steps"></a>További lépések
Most, hogy létrehozott egy alkalmazást, amely a Azure Relay szolgáltatást használja, további információt a következő cikkekben talál:

* [Az Azure Relay áttekintése](relay-what-is-it.md)
* [A WCF Relay szolgáltatás használata a .NET-tel](service-bus-relay-tutorial.md)

[Azure portal]: https://portal.azure.com
