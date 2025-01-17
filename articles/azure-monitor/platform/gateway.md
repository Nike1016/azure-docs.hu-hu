---
title: Számítógépek összekötése az Log Analytics átjáró használatával | Microsoft Docs
description: Az eszközök és a Operations Manager által figyelt számítógépek összekapcsolásához használja az Log Analytics Gatewayt az Azure Automation és Log Analytics szolgáltatásba való adatküldés esetén, amikor nincs internet-hozzáférése.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ae9a1623-d2ba-41d3-bd97-36e65d3ca119
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: magoedte
ms.openlocfilehash: 1d735a3740b473806835f2e80f40cea02b48387e
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/12/2019
ms.locfileid: "68955097"
---
# <a name="connect-computers-without-internet-access-by-using-the-log-analytics-gateway-in-azure-monitor"></a>Internet-hozzáférés nélküli számítógépek összekötése a Log Analytics átjáró használatával Azure Monitor

>[!NOTE]
>Microsoft Operations Management Suite (OMS) váltás Microsoft Azure figyelőre, a terminológia módosul. Ez a cikk az Azure Log Analytics-átjáróként működő OMS-átjáróra vonatkozik. 
>

Ez a cikk azt ismerteti, hogyan konfigurálható a Azure Automation és a Azure Monitor kommunikációja a Log Analytics átjáróval, ha a közvetlenül csatlakoztatott vagy a Operations Manager által figyelt számítógépek nem rendelkeznek internet-hozzáféréssel. 

A Log Analytics átjáró egy http-továbbítási proxy, amely támogatja a http-bújtatást a HTTP-kapcsolat parancs használatával. Ez az átjáró adatokat küld Azure Automation és egy Log Analytics munkaterületre az Azure Monitor azon számítógépek nevében, amelyek nem tudnak közvetlenül csatlakozni az internethez. Nem gyorsítótárazza az ügynökök adatait, az ügynök ebben a helyzetben kezeli a gyorsítótárazási adatait, amíg a rendszer visszaállítja a kommunikációt.

A Log Analytics-átjáró támogatja:

* Jelentéskészítés akár ugyanahhoz a négy Log Analytics munkaterület-ügynökhöz, amelyek mögött Azure Automation hibrid Runbook-feldolgozókkal vannak konfigurálva.  
* Windows rendszerű számítógépek, amelyeken a Microsoft monitoring Agent közvetlenül kapcsolódik egy Log Analytics munkaterülethez Azure Monitorban.
* A Linux rendszerű számítógépek, amelyeken a Linux Log Analytics ügynöke közvetlenül kapcsolódik a Azure Monitor Log Analytics munkaterületéhez.  
* System Center Operations Manager 2012 SP1 HASZNÁLNA 7, Operations Manager 2012 R2 és UR3, illetve Operations Manager 2016-ban vagy később, Log Analytics integrált felügyeleti csoporttal.  

Bizonyos informatikai biztonsági házirendek nem engedélyezik a hálózati számítógépek internetkapcsolatát. Ezek a nem csatlakoztatott számítógépek lehetnek például a POS-eszközök vagy az informatikai szolgáltatásokat támogató kiszolgálók. Ahhoz, hogy az eszközöket Azure Automation vagy egy Log Analytics munkaterülethez lehessen kapcsolni, így kezelheti és figyelheti őket, és úgy is beállíthatja őket, hogy közvetlenül kommunikáljanak az Log Analytics-átjáróval. A Log Analytics átjáró konfigurációs adatokat fogadhat és továbbíthatja az adatokat a nevükben. Ha a számítógépek a Log Analytics ügynökkel vannak konfigurálva Log Analytics munkaterülethez való közvetlen kapcsolódáshoz, a számítógépek ehelyett a Log Analytics átjáróval kommunikálnak.  

A Log Analytics átjáró közvetlenül az ügynökökről továbbítja az adatok átvitelét a szolgáltatásba. Nem elemzi a tranzitban lévő összes adatforgalmat.

Ha egy Operations Manager felügyeleti csoport integrálva van Log Analyticsrel, a felügyeleti kiszolgálók konfigurálhatók úgy, hogy az Log Analytics átjáróhoz csatlakozzanak a konfigurációs adatok fogadásához és az összegyűjtött adatok elküldéséhez az engedélyezett megoldástól függően .  Operations Manager ügynökök küldenek egyes adatfájlokat a felügyeleti kiszolgálónak. Az ügynökök például küldhetnek Operations Manager riasztásokat, a konfigurációs felmérési és a példányok adatterületét, valamint a kapacitási adatkészleteket. A rendszer az egyéb nagy mennyiségű, például Internet Information Services (IIS) naplókat, teljesítményadatokat és biztonsági eseményeket közvetlenül a Log Analytics átjárónak küldi el. 

Ha egy vagy több Operations Manager átjárókiszolgáló telepítve van a nem megbízható rendszerek figyelésére egy peremhálózati hálózaton vagy egy elszigetelt hálózaton, akkor ezek a kiszolgálók nem tudnak kommunikálni Log Analytics átjáróval.  Operations Manager átjárókiszolgáló csak felügyeleti kiszolgálónak tud jelentést készíteni.  Ha egy Operations Manager felügyeleti csoport úgy van konfigurálva, hogy az Log Analytics átjáróval kommunikáljon, a rendszer automatikusan elosztja a proxy konfigurációs adatait minden ügynök által felügyelt számítógépre, amely a Azure Monitor naplózási adatainak gyűjtésére van konfigurálva. még akkor is, ha a beállítás üres.

Ha magas rendelkezésre állást szeretne biztosítani a közvetlenül csatlakoztatott vagy az Operations felügyeleti csoportok számára, amelyek az átjárón keresztül kommunikálnak egy Log Analytics munkaterülettel, a hálózati terheléselosztás (NLB) segítségével átirányíthatja és terjesztheti a forgalmat több átjárókiszolgáló között. Így ha az egyik átjárókiszolgáló leáll, a rendszer átirányítja a forgalmat egy másik elérhető csomópontra.  

A Log Analytics átjárót futtató számítógépen a Log Analytics Windows-ügynöknek azonosítania kell azokat a szolgáltatási végpontokat, amelyeknek az átjárónak kommunikálnia kell. Az ügynöknek arra is szüksége van, hogy az átjárót ugyanarra a munkaterületre irányítsa, hogy az átjáró mögött lévő ügynökök vagy Operations Manager felügyeleti csoport konfigurálva legyen. Ez a konfiguráció lehetővé teszi, hogy az átjáró és az ügynök kommunikáljon a hozzárendelt munkaterülettel.

Egy átjáró akár négy munkaterülethez is többoldalas lehet. A Windows-ügynökök által támogatott munkaterületek teljes száma.  

Minden ügynöknek hálózati kapcsolattal kell rendelkeznie az átjáróhoz, hogy az ügynökök automatikusan átvihetik az adatátvitelt az átjáróba. Ne telepítse az átjárót egy tartományvezérlőre.

Az alábbi ábrán a közvetlen ügynököktől, az átjárón keresztül Azure Automation és Log Analyticsig áramló adatok láthatók. Az ügynök proxyjának konfigurációjának egyeznie kell azzal a porttal, amelyhez az Log Analytics átjáró konfigurálva van.  

![A közvetlen ügynökkel való kommunikáció diagramja a szolgáltatásokkal](./media/gateway/oms-omsgateway-agentdirectconnect.png)

Az alábbi ábrán az adatfolyam egy Operations Manager felügyeleti csoportból a Log Analytics szolgáltatásba.   

![Operations Manager kommunikáció ábrája Log Analytics](./media/gateway/log-analytics-agent-opsmgrconnect.png)

## <a name="set-up-your-system"></a>A System beállítása

Az Log Analytics átjáró futtatására kijelölt számítógépeknek a következő konfigurációval kell rendelkezniük:

* Windows 10, Windows 8,1 vagy Windows 7
* Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 vagy Windows Server 2008
* Microsoft .NET Framework 4,5
* Legalább 4 magos processzor és 8 GB memória 
* A Windows rendszerhez készült [log Analytics ügynök](agent-windows.md) , amely az átjárón keresztül kommunikáló ügynökökkel azonos munkaterületre való jelentésre van konfigurálva

### <a name="language-availability"></a>Nyelvi rendelkezésre állása

A Log Analytics átjáró a következő nyelveken érhető el:

- kínai (egyszerűsített)
- kínai (hagyományos)
- cseh
- holland
- Angol
- francia
- német
- magyar
- olasz
- japán
- koreai
- lengyel
- portugál (brazíliai)
- portugál (általános)
- orosz
- Spanyol (nemzetközi)

### <a name="supported-encryption-protocols"></a>Támogatott titkosítási protokollokkal

Az Log Analytics-átjáró csak Transport Layer Security (TLS) 1,0, 1,1 és 1,2 protokollt támogat.  Nem támogatja SSL (SSL) használatát.  A Log Analytics felé irányuló adatforgalom biztonsága érdekében konfigurálja az átjárót legalább TLS 1,2 használatára. A TLS vagy az SSL régebbi verziói sebezhetőek. Bár jelenleg lehetővé teszik a visszamenőleges kompatibilitást, ne használja őket.  

További információkért tekintse át a [biztonságosan a TLS 1.2 használatával az adatok küldésének](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12). 

### <a name="supported-number-of-agent-connections"></a>Támogatott ügynök-kapcsolatok száma

Az alábbi táblázat azt mutatja be, hogy hány ügynök tud kommunikálni egy átjáró-kiszolgálóval. A támogatás olyan ügynökökön alapul, amelyek 6 másodpercenként 200 KB adatmennyiséget töltenek fel. Minden tesztelt ügynök esetében az adatmennyiség körülbelül 2,7 GB.

|Átjáró |Támogatott ügynökök (hozzávetőleges)|  
|--------|----------------------------------|  
|CPU Intel Xeon processzor E5 – 2660 v3 \@ 2,6 GHz 2 mag<br> Memória: 4 GB<br> Hálózati sávszélesség: 1 Gbps| 600|  
|CPU Intel Xeon processzor E5 – 2660 v3 \@ 2,6 GHz 4 mag<br> Memória: 8 GB<br> Hálózati sávszélesség: 1 Gbps| 1000|  

## <a name="download-the-log-analytics-gateway"></a>A Log Analytics-átjáró letöltése

Szerezze be a Log Analytics átjáró telepítési fájljának legújabb verzióját a [Microsoft letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=54443) vagy a Azure Portal.

Az Log Analytics-átjárónak a Azure Portalból való lekéréséhez kövesse az alábbi lépéseket:

1. Keresse meg a szolgáltatások listájában, és válassza ki **Log Analytics**. 
1. Jelöljön ki egy munkaterületet.
1. A munkaterület panel **általános**területén válassza a **gyorskonfigurálás**lehetőséget. 
1. Az adatforrás választása a munkaterülethez **való kapcsolódáshoz**területen válassza a **számítógépek**lehetőséget.
1. A **közvetlen ügynök** panelen válassza a **Letöltés log Analytics átjáró**elemet.
 
   ![Képernyőkép az Log Analytics-átjáró letöltésének lépéseiről](./media/gateway/download-gateway.png)

vagy 

1. A munkaterület panel **Beállítások**területén válassza a **Speciális beállítások**lehetőséget.
1. Lépjen a **csatlakoztatott források** > **Windows-kiszolgálók** elemre, és válassza a **log Analytics-átjáró letöltése**lehetőséget.

## <a name="install-log-analytics-gateway-using-setup-wizard"></a>Log Analytics átjáró telepítése a telepítővarázsló használatával

Ha az átjárót a telepítővarázsló segítségével szeretné telepíteni, kövesse az alábbi lépéseket. 

1. Kattintson duplán a célmappa **Log Analytics gateway.msi**.
1. Az **Üdvözöljük** lapon kattintson a **Tovább** gombra.

   ![A Kezdőlap képernyőképe az átjáró telepítővarázslója](./media/gateway/gateway-wizard01.png)

1. A **licencszerződés** lapon jelölje be az **Elfogadom a licencszerződés** feltételeit, hogy fogadja el a Microsoft szoftverlicenc-szerződést, majd kattintson a **tovább**gombra.
1. Az a **Port és a proxykiszolgáló címét** oldalon:

   a. Adja meg az átjáróhoz használandó TCP-portszámot. A telepítő ezt a portszámot használja egy bejövő szabály konfigurálásához a Windows tűzfalon.  Az alapértelmezett érték: 8080-as.
      A portszám érvényes tartománya 1 – 65535. Ha a bemeneti nem esik a tartományba, hibaüzenet jelenik meg.

   b. Ha az átjárót telepítő kiszolgálónak proxyn keresztül kell kommunikálnia, adja meg azt a proxy-címeket, ahová az átjárónak csatlakoznia kell. Adja meg például a következőt: `http://myorgname.corp.contoso.com:80`.  Ha ezt a mezőt üresen hagyja, akkor az átjáró közvetlenül csatlakozik az internethez.  Ha a proxykiszolgáló hitelesítést igényel, adjon meg egy felhasználónevet és jelszót.

   c. Kattintson a **Tovább** gombra.

   ![Az átjáró-proxy konfigurációjának képernyőképe](./media/gateway/gateway-wizard02.png)

1. Ha nincs Microsoft Update engedélyezve, a Microsoft Update lap jelenik meg, amely lehetővé teszi, hogy engedélyezze. Válasszon ki egy kijelölést, majd kattintson a **Tovább gombra**. Ellenkező esetben folytassa a következő lépéssel.
1. A **célmappa** oldalon hagyja meg az alapértelmezett C:\Program Files\OMS Gateway mappát, vagy adja meg azt a helyet, ahová az átjárót telepíteni kívánja. Ezután kattintson a **Tovább** gombra.
1. A **telepítésre kész** lapon válassza a **telepítés**lehetőséget. Ha a felhasználói fiókok felügyelete engedélyt kér a telepítésre, válassza az **Igen**lehetőséget.
1. A telepítés befejezése után válassza a **Befejezés**lehetőséget. Annak ellenőrzéséhez, hogy a szolgáltatás fut-e, nyissa meg a Services. msc beépülő modult, és ellenőrizze, hogy a **OMS átjáró** megjelenik-e a szolgáltatások listájában, és hogy az állapota **fut**-e.

   ![A helyi szolgáltatások képernyőképe, amely azt mutatja, hogy a OMS-átjáró fut](./media/gateway/gateway-service.png)

## <a name="install-the-log-analytics-gateway-using-the-command-line"></a>Az Log Analytics-átjáró telepítése a parancssor használatával
Az átjáró letöltött fájlja egy Windows Installer csomag, amely támogatja a csendes telepítést a parancssorból vagy más automatizált metódusból. Ha nem ismeri a Windows Installer szabványos parancssori kapcsolóit, tekintse meg a [parancssori kapcsolókat](https://docs.microsoft.com/windows/desktop/Msi/command-line-options).
 
Az alábbi táblázat a telepítő által támogatott paramétereket mutatja be.

|Paraméterek| Megjegyzések|
|----------|------| 
|PORTSZÁM | A figyelni kívánt átjáró TCP-portjának száma |
|PROXY | A proxykiszolgáló IP-címe |
|INSTALLDIR | Teljes elérési út az átjáró-szoftverek fájljainak telepítési könyvtárának megadásához |
|FELHASZNÁLÓNÉV | A proxykiszolgálóhoz való hitelesítést végző felhasználói azonosító |
|JELSZÓ | A proxyval hitelesíteni kívánt felhasználói azonosító jelszava |
|LicenseAccepted | **1** érték megadása a licencszerződés elfogadásának ellenőrzéséhez |
|HASAUTH | Adja meg az **1** értéket, ha a Felhasználónév/jelszó paraméter meg van adva |
|HASPROXY | **1** érték megadása a **proxy** paraméter IP-címének megadásakor |

Ha az átjárót csendesen szeretné telepíteni, és egy adott proxy-címnek, portszámot kell konfigurálnia, írja be a következőt:

```dos
Msiexec.exe /I “oms gateway.msi” /qn PORTNUMBER=8080 PROXY=”10.80.2.200” HASPROXY=1 LicenseAccepted=1 
```

A/Qn parancssori kapcsoló elrejti a telepítőt, a/QB a telepítést a csendes telepítés során mutatja.  

Ha hitelesítő adatokat kell megadnia a proxyval történő hitelesítéshez, írja be a következőt:

```dos
Msiexec.exe /I “oms gateway.msi” /qn PORTNUMBER=8080 PROXY=”10.80.2.200” HASPROXY=1 HASAUTH=1 USERNAME=”<username>” PASSWORD=”<password>” LicenseAccepted=1 
```

A telepítés után ellenőrizheti a beállításokat (exlcuding a felhasználónevet és a jelszót) a következő PowerShell-parancsmagok használatával:

- **Get-OMSGatewayConfig** – az átjáró által a figyelésre konfigurált TCP-portot adja vissza.
- **Get-OMSGatewayRelayProxy** – Visszaadja annak a proxykiszolgálónek az IP-címét, amelyet a szolgáltatással való kommunikációra konfigurált.

## <a name="configure-network-load-balancing"></a>Hálózati terheléselosztás beállítása 
A hálózati terheléselosztás (NLB) használatával a magas rendelkezésre állású átjárót a Microsoft hálózati terheléselosztás [(NLB)](https://docs.microsoft.com/windows-server/networking/technologies/network-load-balancing), a [Azure Load Balancer](../../load-balancer/load-balancer-overview.md)vagy a hardveres terheléselosztó segítségével állíthatja be. A terheléselosztó forgalmat kezeli a csomópontok között a Log Analytics-ügynökök kért kapcsolatot, vagy az Operations Manager felügyeleti kiszolgálók átirányításával. Egy átjáró kiszolgáló leáll, ha más csomópontokhoz átirányítja a forgalmat.

### <a name="microsoft-network-load-balancing"></a>Microsoft hálózati terheléselosztás
Ismerje meg, hogyan tervezhet és a egy Windows Server 2016 hálózati terheléselosztási fürt üzembe helyezése, lásd: [a hálózati terheléselosztás](https://docs.microsoft.com/windows-server/networking/technologies/network-load-balancing). A következő lépések a Microsoft hálózati terheléselosztási fürtök konfigurálását ismertetik.  

1. Jelentkezzen be a Windows server, amely tagja a rendszergazdák rendszergazdai fiókkal az NLB-fürt.  
2. Hálózati terheléselosztás kezelőjének megnyitása a Kiszolgálókezelőben, kattintson a **eszközök**, és kattintson a **a hálózati terheléselosztás kezelője**.
3. A Microsoft Monitoring Agent telepítve van egy Log Analytics-átjáró kiszolgáló kapcsolódni, kattintson a jobb gombbal a fürt IP-címét, és kattintson **gazdagép hozzáadása fürthöz**. 

    ![Hálózati terheléselosztás kezelője – gazdagép hozzáadása fürthöz](./media/gateway/nlb02.png)
 
4. Adja meg az IP-cím, az átjárókiszolgáló, amely kapcsolódni szeretne. 

    ![Hálózati terheléselosztás kezelője – gazdagép hozzáadása a fürthöz: Kapcsolódás](./media/gateway/nlb03.png) 

### <a name="azure-load-balancer"></a>Azure Load Balancer
A Azure Load Balancer megtervezéséhez és üzembe helyezéséhez tekintse meg a [Mi az a Azure Load Balancer?](../../load-balancer/load-balancer-overview.md)című témakört. Alapszintű terheléselosztó üzembe helyezéséhez kövesse az ebben a rövid útmutatóban ismertetett lépéseket, a **háttér-kiszolgálók létrehozása**című szakaszban ismertetett lépéseket kizárva. [](../../load-balancer/quickstart-create-basic-load-balancer-portal.md)   

> [!NOTE]
> A Azure Load Balancer alapszintű **SKU**-val történő konfigurálásához az szükséges, hogy az Azure-beli virtuális gépek rendelkezésre állási csoportba tartozzanak. A rendelkezésre állási csoportokkal kapcsolatos további tudnivalókért lásd: [Windows rendszerű virtuális gépek rendelkezésre állásának kezelése az Azure-ban](../../virtual-machines/windows/manage-availability.md). Meglévő virtuális gépek rendelkezésre állási csoportba való felvételéhez tekintse meg [Azure Resource Manager virtuális gép rendelkezésre állási csoportjának beállítása](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4)című témakört.
> 

A terheléselosztó létrehozása után létre kell hozni egy háttér-készletet, amely egy vagy több átjárókiszolgáló forgalmát osztja el. Kövesse a következő témakörben ismertetett lépéseket: a [terheléselosztó erőforrásainak létrehozása](../../load-balancer/quickstart-create-basic-load-balancer-portal.md#create-resources-for-the-load-balancer)szakasz.  

>[!NOTE]
>Az állapot-mintavétel konfigurálásakor úgy kell konfigurálni, hogy az átjárókiszolgáló TCP-portját használja. Az állapotfigyelő szolgáltatás dinamikusan adja hozzá vagy távolítja el az átjáró-kiszolgálókat a terheléselosztó elforgatásával az állapot-ellenőrzésekre adott válasz alapján. 
>

## <a name="configure-the-log-analytics-agent-and-operations-manager-management-group"></a>A Log Analytics-ügynök és a Operations Manager felügyeleti csoport konfigurálása
Ebből a szakaszból megtudhatja, hogyan konfigurálhat közvetlenül csatlakoztatott Log Analytics ügynököket, egy Operations Manager felügyeleti csoportot vagy Azure Automation hibrid Runbook-feldolgozókat az Log Analytics átjáróval a Azure Automation vagy a Log Analytics használatával folytatott kommunikációhoz.  

### <a name="configure-a-standalone-log-analytics-agent"></a>Önálló Log Analytics-ügynök konfigurálása
A Log Analytics ügynök konfigurálásakor cserélje le a proxykiszolgáló értékét a Log Analytics átjárókiszolgáló IP-címére és portszámára. Ha több átjárókiszolgáló van telepítve egy terheléselosztó mögött, a Log Analytics ügynök proxy konfigurációja a terheléselosztó virtuális IP-címe.  

>[!NOTE]
>Ha a Log Analytics-ügynököt az átjáróra és a közvetlenül a Log Analyticshoz csatlakozó Windows-számítógépekre szeretné telepíteni, tekintse meg a [Windows rendszerű számítógépek összekapcsolása az Azure](agent-windows.md)-beli log Analytics szolgáltatással A Linux rendszerű számítógépek összekapcsolásáról lásd: [log Analytics-ügynök konfigurálása Linux rendszerű számítógépekhez hibrid környezetben](../../azure-monitor/learn/quick-collect-linux-computer.md). 
>

Miután telepítette az ügynököt az átjárókiszolgálón, állítsa be úgy, hogy az átjáróval kommunikáló munkaterülethez vagy munkaterülethez tartozó ügynököknek jelentsen. Ha a Log Analytics Windows-ügynök nincs telepítve az átjárón, a 300-es esemény a OMS átjáró eseménynaplóba íródik, ami azt jelzi, hogy az ügynököt telepíteni kell. Ha az ügynök telepítve van, de nincs úgy konfigurálva, hogy ugyanarra a munkaterületre jelentsen, mint az azt használó ügynökök, a 105-es eseményt ugyanarra a naplóba írja a rendszer, amely azt jelzi, hogy az átjárón lévő ügynököt úgy kell konfigurálni, hogy ugyanarra a munkaterületre jelentsen, mint a Co-t használó ügynökök mmunicate az átjáróval.

A konfiguráció befejezése után indítsa újra a OMS-átjáró szolgáltatást a módosítások alkalmazásához. Ellenkező esetben az átjáró elutasítja a Log Analytics kommunikálni próbáló ügynököket, és a 105-es eseményt a OMS átjáró eseménynaplójában fogja jelenteni. Ez akkor is megtörténik, amikor hozzáad vagy eltávolít egy munkaterületet az átjárókiszolgáló ügynökének konfigurációjával.   

Az Automation hibrid Runbook-feldolgozóval kapcsolatos információkért lásd: az adatközpontban [vagy a felhőben lévő erőforrások automatizálása hibrid Runbook](../../automation/automation-hybrid-runbook-worker.md)-feldolgozók használatával.

### <a name="configure-operations-manager-where-all-agents-use-the-same-proxy-server"></a>Operations Manager konfigurálása, ahol az összes ügynök ugyanazt a proxykiszolgálót használja

A Operations Manager proxy konfigurációját a rendszer automatikusan alkalmazza minden olyan ügynökre, amely Operations Manager jelentést készít, még akkor is, ha a beállítás üres.  

Ha a OMS-átjárót szeretné használni a Operations Manager támogatásához, rendelkeznie kell a következővel:

* A Microsoft monitoring Agent (8.0.10900.0 vagy újabb verzió) telepítve van a OMS-átjárókiszolgálón, és ugyanazokkal a Log Analytics munkaterületekkel van konfigurálva, amelyeket a felügyeleti csoportnak be kell jelentenie.
* Internetkapcsolat. Azt is megteheti, hogy a OMS-átjárónak csatlakoztatva kell lennie az internethez csatlakozó proxykiszolgálóhoz.

> [!NOTE]
> Ha nem adja meg az átjáró értékét, az üres értékeket a rendszer az összes ügynök számára leküldi.
>

Ha a Operations Manager felügyeleti csoport első alkalommal regisztrálja Log Analytics munkaterületet, akkor az operatív konzolon nem jelenik meg a felügyeleti csoport proxy-konfigurációjának megadására szolgáló beállítás. Ez a beállítás csak akkor érhető el, ha a felügyeleti csoport regisztrálva van a szolgáltatásban.  

Az integráció konfigurálásához frissítse a rendszerproxy konfigurációját a netsh használatával azon a rendszeren, amelyen az operatív konzolt és a felügyeleti csoportban található összes felügyeleti kiszolgálón futtatja a rendszert. Kövesse az alábbi lépéseket:

1. Nyisson meg egy rendszergazda jogú parancssort:

   a. Kattintson a **Start** gombra, és írja be a **cmd parancsot**.  

   b. Kattintson a jobb gombbal a **parancssor** elemre, és válassza **a Futtatás rendszergazdaként**lehetőséget.  

1. Írja be a következő parancsot:

   `netsh winhttp set proxy <proxy>:<port>`

Az Log Analytics-integráció befejezése után távolítsa el a változást a `netsh winhttp reset proxy`futtatásával. Ezután az operatív konzolon a **proxykiszolgáló konfigurálása** lehetőséggel adja meg az log Analytics átjárókiszolgáló megadását. 

1. A Operations Manager-konzol Operations **Management Suite**területén válassza a **Csatlakozás**lehetőséget, majd válassza a **proxykiszolgáló konfigurálása**lehetőséget.

   ![Képernyőkép a Operations Managerről, amely a proxykiszolgáló konfigurálását mutatja be](./media/gateway/scom01.png)

1. Válassza a **proxykiszolgáló használata az Operations Management Suite eléréséhez** lehetőséget, majd adja meg a terheléselosztó log Analytics átjáró-kiszolgálójának vagy virtuális IP-címének IP-címét. Ügyeljen arra, hogy az előtaggal `http://`kezdődjön.

   ![Képernyőkép a Operations Managerről, amely a proxykiszolgáló címeit jeleníti meg](./media/gateway/scom02.png)

1. Válassza a **Finish** (Befejezés) elemet. Az Operations Manager felügyeleti csoportban most már a Log Analytics szolgáltatás az átjárókiszolgálón keresztül kommunikációra van konfigurálva.

### <a name="configure-operations-manager-where-specific-agents-use-a-proxy-server"></a>Operations Manager konfigurálása, ahol az egyes ügynökök proxykiszolgálót használnak

Nagyméretű vagy összetett környezetek esetén előfordulhat, hogy csak bizonyos kiszolgálókat (vagy csoportokat) szeretne használni a Log Analytics átjárókiszolgáló használatához.  Ezen kiszolgálók esetében nem lehet közvetlenül frissíteni a Operations Manager ügynököt, mert ezt az értéket a felügyeleti csoport globális értéke felülírja.  Ehelyett felülbírálja az értékek leküldéséhez használt szabályt.  

> [!NOTE] 
> Ezt a konfigurációs technikát akkor használja, ha több Log Analytics átjáró-kiszolgálót szeretne engedélyezni a környezetben.  Megkövetelheti például, hogy egy adott Log Analytics átjáró-kiszolgálót regionális szinten lehessen megadni.
>

Adott kiszolgálók vagy csoportok konfigurálása a Log Analytics átjárókiszolgáló használatára: 

1. Nyissa meg az Operations Manager konzolt, és válassza ki a **szerzői műveletek** munkaterületen.  
1. A szerzői műveletek munkaterületen válassza a **szabályok**lehetőséget. 
1. A Operations Manager eszköztáron kattintson a **hatókör** gombra. Ha a gomb nem érhető el, győződjön meg róla, hogy a **figyelés** ablaktáblán egy objektumot, nem pedig mappát jelölt ki. A **felügyeleti csomag objektumai** párbeszédpanel közös célzott osztályok, csoportok és objektumok listáját jeleníti meg. 
1. A **Keresés** mezőben adja meg a **állapotfigyelő szolgáltatás** , majd válassza ki a listából. Kattintson az **OK** gombra.  
1. Keresse meg az **Advisor proxy beállítási szabályát**. 
1. A Operations Manager eszköztáron válassza a **felülbírálások elemet, majd a Rule\For felülbírálása a következő osztály egy adott objektumához: Állapotfigyelő szolgáltatás** , majd válasszon ki egy objektumot a listából.  Vagy hozzon létre egy egyéni csoportot, amely tartalmazza azon kiszolgálók állapotfigyelő szolgáltatási objektumát, amelyekre alkalmazni kívánja ezt a felülbírálást. Ezután alkalmazza a felülbírálást az egyéni csoportra.
1. A **felülbírálás tulajdonságai** párbeszédpanelen vegyen fel egy pipát a **felülbírálás** oszlopban a **WebProxyAddress** paraméter mellett.  A **felülbírálás értéke** mezőben adja meg az log Analytics átjárókiszolgáló URL-címét. Ügyeljen arra, hogy az előtaggal `http://`kezdődjön.  

    >[!NOTE]
    > Nincs szükség a szabály engedélyezésére. A Microsoft System Center Advisor biztonságos referenciájának felülbírálási felügyeleti csomagja a Microsoft System Center Advisor figyelési kiszolgáló csoportra irányuló felülbírálással már automatikusan felügyelhető.
    > 

1. Válasszon ki egy felügyeleti csomagot a **cél felügyeleti csomag kiválasztása** listából, vagy hozzon létre egy új, lezáratlan felügyeleti csomagot az **új**lehetőség kiválasztásával. 
1. Ha elkészült, kattintson az **OK** gombra. 

### <a name="configure-for-automation-hybrid-runbook-workers"></a>Konfigurálás az Automation hibrid Runbook-feldolgozói számára

Ha a környezetében Automation hibrid Runbook-feldolgozókkal rendelkezik, kövesse az alábbi lépéseket a manuális, ideiglenes megkerülő megoldásokhoz, amelyekkel konfigurálhatja a OMS-átjárót a feldolgozók támogatására.

Az ebben a szakaszban leírt lépések követéséhez ismernie kell az Azure-régiót, ahol az Automation-fiók található. A hely megkeresése:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Válassza ki az Azure Automation szolgáltatást.
1. Válassza ki a megfelelő Azure Automation-fiókot.
1. Megtekintheti a régió alatt **hely**.

   ![Képernyőkép az Automation-fiók helyéről a Azure Portal](./media/gateway/location.png)

Az egyes helyekhez tartozó URL-címek azonosításához használja az alábbi táblázatokat.

**A Job Runtime adatszolgáltatásának URL-címei**

| **Location** | **URL-cím** |
| --- | --- |
| USA északi középső régiója |ncus-jobruntimedata-prod-su1.azure-automation.net |
| Nyugat-Európa |we-jobruntimedata-prod-su1.azure-automation.net |
| USA déli középső régiója |scus-jobruntimedata-prod-su1.azure-automation.net |
| USA 2. keleti régiója |eus2-jobruntimedata-prod-su1.azure-automation.net |
| Közép-Kanada |cc-jobruntimedata-prod-su1.azure-automation.net |
| Észak-Európa |ne-jobruntimedata-prod-su1.azure-automation.net |
| Délkelet-Ázsia |sea-jobruntimedata-prod-su1.azure-automation.net |
| Közép-India |cid-jobruntimedata-prod-su1.azure-automation.net |
| Japán |jpe-jobruntimedata-prod-su1.azure-automation.net |
| Ausztrália |ase-jobruntimedata-prod-su1.azure-automation.net |

**Az ügynök szolgáltatás URL-címek**

| **Location** | **URL-cím** |
| --- | --- |
| USA északi középső régiója |ncus-agentservice-prod-1.azure-automation.net |
| Nyugat-Európa |we-agentservice-prod-1.azure-automation.net |
| USA déli középső régiója |scus-agentservice-prod-1.azure-automation.net |
| USA 2. keleti régiója |eus2-agentservice-prod-1.azure-automation.net |
| Közép-Kanada |cc-agentservice-prod-1.azure-automation.net |
| Észak-Európa |ne-agentservice-prod-1.azure-automation.net |
| Délkelet-Ázsia |sea-agentservice-prod-1.azure-automation.net |
| Közép-India |cid-agentservice-prod-1.azure-automation.net |
| Japán |jpe-agentservice-prod-1.azure-automation.net |
| Ausztrália |ase-agentservice-prod-1.azure-automation.net |

Ha a számítógép automatikusan hibrid Runbook-feldolgozóként van regisztrálva, akkor a Update Management megoldással kezelheti a javítást. Kövesse az alábbi lépéseket:

1. Adja hozzá a Feladatadatok futásidejű szolgáltatás URL-címeinek a gazdagépen engedélyezett a Log Analytics-átjáró-listája. Például:`Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. Indítsa újra a Log Analytics-átjáró szolgáltatás a következő PowerShell-parancsmag segítségével: `Restart-Service OMSGatewayService`

Ha a számítógép a hibrid Runbook Worker Registration parancsmag használatával csatlakozik Azure Automationhoz, kövesse az alábbi lépéseket:

1. A Log Analytics-átjáró engedélyezett gazdagép listájában adja hozzá az ügynök regisztrációs URL-címe. Például:`Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
1. Adja hozzá a Feladatadatok futásidejű szolgáltatás URL-címeinek a gazdagépen engedélyezett a Log Analytics-átjáró-listája. Például:`Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. Indítsa újra a Log Analytics-átjáró szolgáltatást.
    `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>Hasznos PowerShell-parancsmagok

A Log Analytics átjáró konfigurációs beállításainak frissítéséhez parancsmagokat használhat a feladatok végrehajtásához. A parancsmagok használata előtt ügyeljen a következőre:

1. Telepítse a Log Analytics átjárót (Microsoft Windows Installer).
1. Nyisson meg egy PowerShell-konzolablakot.
1. Importálja a modult a következő parancs beírásával:`Import-Module OMSGateway`
1. Ha nem történt hiba az előző lépésben, a modul importálása sikeresen megtörtént, és a parancsmag is használható. Be`Get-Module OMSGateway`
1. Miután a parancsmagok használatával módosításokat hajt végre, indítsa újra a OMS-átjáró szolgáltatást.

A 3. lépésben szereplő hiba azt jelenti, hogy a modult nem importálták. A hiba akkor fordulhat elő, ha a PowerShell nem találja a modult. A modult a OMS-átjáró telepítési útvonalán találja: *C:\Program FILES\MICROSOFT OMS Gateway\PowerShell\OmsGateway*.

| **A parancsmag** | **Paraméterek** | **Leírás** | **Példa** |
| --- | --- | --- | --- |  
| `Get-OMSGatewayConfig` |Kulcs |A szolgáltatás konfigurációjának beolvasása |`Get-OMSGatewayConfig` |  
| `Set-OMSGatewayConfig` |Kulcs (kötelező) <br> Érték |A szolgáltatás konfigurációjának módosítása |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |  
| `Get-OMSGatewayRelayProxy` | |Relay-(upstream) proxy címét |`Get-OMSGatewayRelayProxy` |  
| `Set-OMSGatewayRelayProxy` |Cím<br> Felhasználónév<br> Jelszó |Beállítja a relay (upstream) proxy címét (vagy hitelesítő adatok) |1. Állítsa be a relay-proxy és a hitelesítő adatokat:<br> `Set-OMSGatewayRelayProxy`<br>`-Address http://www.myproxy.com:8080`<br>`-Username user1 -Password 123` <br><br> 2. A relay proxy hitelesítést nem igénylő beállításait: `Set-OMSGatewayRelayProxy`<br> `-Address http://www.myproxy.com:8080` <br><br> 3. A relay-proxybeállítást törléséhez:<br> `Set-OMSGatewayRelayProxy` <br> `-Address ""` |  
| `Get-OMSGatewayAllowedHost` | |A jelenleg engedélyezett gazdagép beolvasása (csak a helyileg konfigurált engedélyezett gazdagép, az engedélyezett gazdagépek nem tölthetők le automatikusan) |`Get-OMSGatewayAllowedHost` | 
| `Add-OMSGatewayAllowedHost` |A gazdagép (kötelező) |Hozzáadja a gazdagépet, az engedélyezett listára |`Add-OMSGatewayAllowedHost -Host www.test.com` |  
| `Remove-OMSGatewayAllowedHost` |A gazdagép (kötelező) |A gazdagép eltávolítása az engedélyezett listára |`Remove-OMSGatewayAllowedHost`<br> `-Host www.test.com` |  
| `Add-OMSGatewayAllowedClientCertificate` |Tulajdonos (kötelező) |Az engedélyezett listára vonatkoznak az ügyféltanúsítvány hozzáadása |`Add-OMSGatewayAllowed`<br>`ClientCertificate` <br> `-Subject mycert` |  
| `Remove-OMSGatewayAllowedClientCertificate` |Tulajdonos (kötelező) |Az ügyfél-tanúsítvány tulajdonosának távolít el az engedélyezett listára |`Remove-OMSGatewayAllowed` <br> `ClientCertificate` <br> `-Subject mycert` |  
| `Get-OMSGatewayAllowedClientCertificate` | |A jelenleg engedélyezett ügyféltanúsítvány-alanyok beolvasása (csak a helyileg konfigurált engedélyezett alanyok, az engedélyezett témák nem tölthetők le automatikusan) |`Get-`<br>`OMSGatewayAllowed`<br>`ClientCertificate` |  

## <a name="troubleshooting"></a>Hibaelhárítás

Az átjáró által naplózott események gyűjtéséhez telepítenie kell a Log Analytics ügynököt.

![A Log Analytics Gateway-napló Eseménynapló listájának képernyőképe](./media/gateway/event-viewer.png)

### <a name="log-analytics-gateway-event-ids-and-descriptions"></a>Log Analytics átjáró-események azonosítói és leírása

A következő táblázat a Log Analytics átjáró eseményeinek esemény-azonosítóit és leírásait mutatja be.

| **Azonosító** | **Leírás** |
| --- | --- |
| 400 |Bármilyen alkalmazáshiba, amely nem rendelkezik egyedi AZONOSÍTÓval. |
| 401 |Helytelen konfiguráció. Például: listenPort = "text", egész szám helyett. |
| 402 |Kivétel történt a TLS-kézfogási üzenetek elemzésekor. |
| 403 |Hálózati hiba történt. Nem lehet például csatlakozni a célkiszolgálóra. |
| 100 |Általános információk. |
| 101 |A szolgáltatás elindult. |
| 102 |A szolgáltatás leállt. |
| 103 |HTTP-csatlakozási parancs érkezett az ügyféltől. |
| 104 |Nem HTTP-csatlakozási parancs. |
| 105 |A célkiszolgáló nincs engedélyezve a listán, vagy a célport nem biztonságos (443). <br> <br> Győződjön meg arról, hogy az OMS-átjárókiszolgáló és az OMS-átjáróval kommunikáló ügynökök az MMA-ügynök ugyanahhoz a Log Analytics munkaterülethez csatlakoznak. |
| 105 |HIBA TcpConnection – érvénytelen ügyféltanúsítvány: CN=Gateway. <br><br> Győződjön meg arról, hogy a OMS Gateway 1.0.395.0 vagy újabb verzióját használja. Győződjön meg arról is, hogy az OMS-átjárókiszolgáló és az OMS-átjáróval kommunikáló ügynökök az MMA-ügynök ugyanahhoz a Log Analytics-munkaterülethez csatlakoznak. |
| 106 |A TLS/SSL protokoll verziója nem támogatott.<br><br> A Log Analytics átjáró csak a TLS 1,0, TLS 1,1 és 1,2 protokollt támogatja. Nem támogatja az SSL.|
| 107 |A TLS-munkamenet ellenőrzése megtörtént. |

### <a name="performance-counters-to-collect"></a>A gyűjteni kívánt teljesítményszámlálók

Az alábbi táblázat a Log Analytics-átjáró esetében rendelkezésre álló teljesítményszámlálók. A teljesítményszámlálók hozzáadásához használja a Teljesítményfigyelőt.

| **Name** | **Leírás** |
| --- | --- |
| Log Analytics-aktív átjáró ügyfél-kapcsolat |Aktív ügyfél (TCP) hálózati kapcsolatok száma |
| Log Analytics-átjáró/hibák száma |Hibák száma |
| Log Analytics-átjáró/csatlakozott ügyfél |Csatlakoztatott ügyfelek száma |
| Log Analytics Gateway/elutasító száma |Bármely TLS-érvényesítési hiba miatt elutasítások száma |

![Képernyőkép a Log Analytics átjáró felületéről, a teljesítményszámlálók megjelenítéséhez](./media/gateway/counters.png)

## <a name="assistance"></a>Segítség

Ha bejelentkezett a Azure Portalba, segítséget kérhet az Log Analytics átjáróval vagy bármely más Azure-szolgáltatással vagy szolgáltatással kapcsolatban.
Ha segítséget szeretne kérni, válassza a portál jobb felső sarkában látható kérdőjel ikont, és válassza az **új támogatási kérés**lehetőséget. Ezután fejezze be az új támogatási kérelem űrlapját.

![Képernyőkép egy új támogatási kérelemről](./media/gateway/support.png)

## <a name="next-steps"></a>További lépések

[](../../azure-monitor/platform/agent-data-sources.md) Adatforrások hozzáadásával adatokat gyűjthet a csatlakoztatott forrásokból, és tárolhatja az adatokat a log Analytics munkaterületen.