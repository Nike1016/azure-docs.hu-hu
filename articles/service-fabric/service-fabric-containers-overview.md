---
title: A Service Fabric és a tárolók áttekintése | Microsoft Docs
description: A Service Fabric áttekintése és a tárolók használata a Service-alkalmazások üzembe helyezéséhez. Ez a cikk áttekintést nyújt a tárolók használatáról és a Service Fabric rendelkezésre álló lehetőségeiről.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: c98b3fcb-c992-4dd9-b67d-2598a9bf8aab
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/8/2018
ms.author: atsenthi
ms.openlocfilehash: 2ed3a9d4b1ec219d22a9e01e7acec5d7e950289b
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/12/2019
ms.locfileid: "68599760"
---
# <a name="service-fabric-and-containers"></a>Service Fabric és tárolók

## <a name="introduction"></a>Bevezetés

Az Azure Service Fabric egy elosztott rendszerplatform, amely megkönnyíti a skálázható és megbízható mikroszolgáltatások és tárolók csomagolását, üzembe helyezését és kezelését.

Service Fabric a Microsoft [tároló-Orchestrator](service-fabric-cluster-resource-manager-introduction.md) , amellyel a gépeket egy fürtön belül üzembe helyezheti. A Microsoft által nyújtott szolgáltatások nagy léptékű szolgáltatásokkal való ellátása során az évek során szerzett tapasztalatok Service Fabric.

A mikroszolgáltatások számos különféle módon fejleszthetőek a [Service Fabric programozási modellektől](service-fabric-choose-framework.md) és az [ASP.NET Core-tól](service-fabric-reliable-services-communication-aspnetcore.md) [tetszőleges kód üzembe helyezéséig](service-fabric-guest-executables-introduction.md). Ha azonban csak a tárolókat szeretné [üzembe helyezni és felügyelni](service-fabric-containers-overview.md), Service Fabric is remek választás.

Alapértelmezés szerint a Service Fabric folyamatokként telepíti és aktiválja ezeket a szolgáltatásokat. A folyamatok biztosítják a fürt erőforrásainak leggyorsabb aktiválását és legnagyobb sűrűségű használatát. A Service Fabric a szolgáltatásokat tároló lemezképekben is üzembe helyezheti. A szolgáltatásokat a folyamatokban és a tárolókban lévő szolgáltatásokban is összekeverheti ugyanabban az alkalmazásban.

Ha szeretné azonnal beugrani, és kipróbálni a tárolókat a Service Fabricon, próbálkozzon egy gyors útmutatóval, oktatóanyaggal vagy mintával:  

[Rövid útmutató: Linux-tároló alkalmazás üzembe helyezése Service Fabric](service-fabric-quickstart-containers-linux.md)  
[Rövid útmutató: Windows Container-alkalmazás üzembe helyezése Service Fabric](service-fabric-quickstart-containers.md)  
[Meglévő .NET-alkalmazás tárolóba helyezése](service-fabric-host-app-in-a-container.md)  
[Service Fabric-tárolóminták](https://azure.microsoft.com/resources/samples/service-fabric-containers/)  

## <a name="what-are-containers"></a>Mik azok a tárolók

A tárolók megoldják a problémát az alkalmazások különböző számítástechnikai környezetekben való megbízható futtatásában azáltal, hogy megváltoztathatatlan környezetet biztosítanak az alkalmazás futtatásához. A tárolók egy alkalmazást és annak összes függőségét, például a kódtárakat és a konfigurációs fájlokat a saját elkülönített "dobozba" helyezik, amely a szoftvernek a tárolón belüli futtatásához szükséges mindent tartalmazza. Ha a tároló fut, az alkalmazás belsejében mindig minden szükséges, hogy futnia kell, például a függő kódtárak megfelelő verzióit, a konfigurációs fájlokat és bármi mást, amit futtatnia kell.

A tárolók közvetlenül a kernelen futnak, és a fájlrendszer és más erőforrások elkülönített nézetével rendelkeznek. A tárolóban lévő alkalmazások nem ismerik a tárolón kívüli egyéb alkalmazásokat vagy folyamatokat. Minden alkalmazás és annak futtatókörnyezete, függőségei és rendszerkönyvtára egy teljes, privát hozzáféréssel rendelkező tárolóban fut az operációs rendszer saját elkülönített nézetéhez. Amellett, hogy megkönnyíti az alkalmazás összes függőségének a különböző számítástechnikai környezetekben való futtatását, a biztonság és az erőforrások elkülönítése fontos előnyökkel jár a tárolók Service Fabric-vel való használatának, amelyek egyébként a szolgáltatásait futtatják folyamat.

A virtuális gépekhez képest a tárolók a következő előnyöket biztosítják:

* **Kicsi**: A tárolók a hatékonyság növelése érdekében egyetlen tárolóhelyet és a rétegek verzióját és frissítését használják.
* **Gyors**: A tárolóknak nem kell teljes operációs rendszert elindítaniuk, így sokkal gyorsabbak lehetnek – általában másodpercek alatt.
* **Hordozhatóság**: A tárolóban lévő alkalmazás-rendszerképek a felhőben, a helyszínen, a virtuális gépeken, illetve közvetlenül a fizikai gépeken is futtathatók.
* **Erőforrás-szabályozás**: A tárolók korlátozhatják a gazdagépen felhasználható fizikai erőforrásokat.

### <a name="container-types-and-supported-environments"></a>Tárolók típusai és támogatott környezetek

A Service Fabric a Linux és a Windows rendszerű tárolókat is támogatja, és támogatja a Hyper-V elkülönítési üzemmódot Windows rendszeren.

#### <a name="docker-containers-on-linux"></a>Docker-tárolók Linuxon

A Docker API-kat biztosít a tárolók létrehozásához és kezeléséhez a Linux kernel-tárolók felett. A Docker hub egy központi tárházat biztosít a tároló lemezképek tárolásához és lekéréséhez.
Linux-alapú oktatóanyagért lásd: [az első Service Fabric Container-alkalmazás létrehozása Linux rendszeren](service-fabric-get-started-containers-linux.md).

#### <a name="windows-server-containers"></a>Windows Server-tárolók

A Windows Server 2016 két különböző típusú tárolót biztosít, amelyek elkülönítési szinttől eltérőek. A Windows Server-tárolók és a Docker-tárolók hasonlóak, mert mindkettő rendelkezik névtér-és fájlrendszer-elkülönítéssel, miközben a kernelt a-t futtató gazdagépre osztja. Linux rendszeren ezt az elkülönítést hagyományosan a cgroupokban és a névterek biztosították, a Windows Server-tárolók pedig hasonlóan viselkednek.

A Hyper-V-támogatással rendelkező Windows-tárolók nagyobb elkülönítést és biztonságot biztosítanak, mert egyetlen tároló sem osztja meg az operációs rendszer kernelét bármely más tárolóval vagy a gazdagéptel. Ezzel a magasabb szintű biztonsági elkülönítéssel a Hyper-V-t használó tárolók potenciálisan ellenséges, több-bérlős forgatókönyveket céloznak.
Windows-alapú oktatóanyagért lásd: [az első Service Fabric Container-alkalmazás létrehozása Windows](service-fabric-get-started-containers.md)rendszeren.

Az alábbi ábrán az elérhető virtualizációs és elkülönítési szintek különböző típusai láthatók.
![Service Fabric platform][Image1]

## <a name="scenarios-for-using-containers"></a>A tárolók használatára vonatkozó forgatókönyvek

Íme egy tipikus példa, amelyben a tároló jó választás:

* **IIS-emelés és-váltás**: Egy meglévő [ASP.net MVC](https://www.asp.net/mvc) -alkalmazást egy tárolóban helyezhet át a ASP.net Coreba való Migrálás helyett. Ezek a ASP.NET MVC-alkalmazások a Internet Information Servicestól (IIS) függenek. Ezeket az alkalmazásokat tároló lemezképbe csomagolhatja az előre létrehozott IIS-lemezképből, és üzembe helyezheti azokat Service Fabric használatával. A Windows-tárolókkal kapcsolatos információkért lásd: [tároló lemezképek a Windows Serveren](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-server) .

* **Tárolók és Service Fabric-szolgáltatások együttes szolgáltatása**: Egy meglévő tároló-rendszerkép használata az alkalmazás egy részénél. Használhatja például az alkalmazás és az állapot-nyilvántartó szolgáltatások webes kezelőfelületének [NGINX](https://hub.docker.com/_/nginx/) -tárolóját az intenzívebb háttérbeli számításokhoz.

* **A "zajos szomszédok" szolgáltatások hatásának csökkentése**: A tárolók erőforrás-irányítási képességeinek használatával korlátozhatja a szolgáltatás által a gazdagépen használt erőforrásokat. Ha a szolgáltatások sok erőforrást használnak, és hatással vannak mások teljesítményére (például egy hosszan futó, lekérdezés-szerű művelet), fontolja meg, hogy ezek a szolgáltatások olyan tárolókban legyenek, amelyeknek erőforrás-szabályozása van.

## <a name="service-fabric-support-for-containers"></a>Service Fabric tárolók támogatása

A Service Fabric támogatja a Docker-tárolók telepítését Linux rendszeren és Windows Server-tárolón a Windows Server 2016 rendszeren, a Hyper-V elkülönítési üzemmód támogatása mellett. 

A Service Fabric egy olyan [alkalmazás-modellt](service-fabric-application-model.md) biztosít, amelyben a tároló egy olyan alkalmazás-gazdagépet jelöl, amelyben több szolgáltatás replikája van elhelyezve. A Service Fabric támogatja a [vendég végrehajtható fájl](service-fabric-guest-executables-introduction.md) használatát is, amelyben nem a beépített Service Fabric programozási modelleket használja, hanem egy meglévő alkalmazást, amely bármilyen nyelven vagy keretrendszerben, egy tárolón belül lett felhasználva. Ez a forgatókönyv a tárolók általános használati esete.

[A tárolón belül Service Fabric-szolgáltatásokat](service-fabric-services-inside-containers.md)is futtathat. A Service Fabric-szolgáltatások tárolón belüli futtatásának támogatása jelenleg korlátozott.

A Service Fabric számos tároló-képességet biztosít, amelyek segítségével olyan alkalmazásokat hozhat létre, amelyek a tárolós szolgáltatásokból állnak, például:

* Tároló rendszerképének üzembe helyezése és aktiválása.
* Erőforrás-szabályozás, beleértve az erőforrás-értékek beállítását alapértelmezetten az Azure-fürtökön.
* Tárház hitelesítése.
* Tároló portja az állomás portjának leképezéséhez.
* Tároló-tároló felderítés és kommunikáció.
* A környezeti változók konfigurálásának és beállításának lehetősége.
* Biztonsági hitelesítő adatok megadásának lehetősége a tárolón.
* Különböző hálózati módok választhatók a tárolók számára.

Az Azure-beli tárolók támogatásának átfogó áttekintése, például a Kubernetes-fürt létrehozása az Azure Kubernetes szolgáltatással, a privát Docker-beállításjegyzék létrehozása a Azure Container Registry-ben és további információ: [Azure for containers](https://docs.microsoft.com/azure/containers/).

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogy milyen támogatási Service Fabric biztosít a tárolók futtatásához. Ezután az egyes funkciók példáit követve megtudhatja, hogyan használhatja őket.

[Az első Service Fabric Container-alkalmazás létrehozása Linux rendszeren](service-fabric-get-started-containers-linux.md)  
[Az első Service Fabric Container-alkalmazás létrehozása Windows rendszeren](service-fabric-get-started-containers.md)  
[További információ a Windows-tárolók használatáról](https://docs.microsoft.com/virtualization/windowscontainers/about/)

[Image1]: media/service-fabric-containers/Service-Fabric-Types-of-Isolation.png