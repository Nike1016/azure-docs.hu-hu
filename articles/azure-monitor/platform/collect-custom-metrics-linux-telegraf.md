---
title: Egyéni metrikák gyűjtése Linux rendszerű virtuális gépekhez a InfluxData-ben-Graf ügynökkel
description: Egyéni metrikák gyűjtése Linux rendszerű virtuális gépekhez a InfluxData-ben-Graf ügynökkel
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: e8164a111b9ad5ebcc67c248586e2576046334b0
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883241"
---
# <a name="collect-custom-metrics-for-a-linux-vm-with-the-influxdata-telegraf-agent"></a>Egyéni metrikák gyűjtése Linux rendszerű virtuális gépekhez a InfluxData-ben-Graf ügynökkel

A Azure Monitor használatával egyéni metrikákat gyűjthet az alkalmazás telemetria, az Azure-erőforrásokon futó ügynökkel, vagy akár a figyelési rendszereken kívül is. Ezt követően közvetlenül is beküldheti őket a Azure Monitorba. Ez a cikk útmutatást nyújt arról, hogyan helyezheti üzembe a [InfluxData](https://www.influxdata.com/) -gyártói ügynököt egy Linux rendszerű virtuális gépen az Azure-ban, és hogyan konfigurálhatja az ügynököt a metrikák közzétételére Azure monitor 

## <a name="influxdata-telegraf-agent"></a>InfluxData-s Grafi ügynök 

[](https://docs.influxdata.com/telegraf/v1.7/) A-ben egy olyan beépülő modulra épülő ügynök, amely lehetővé teszi a metrikák több mint 150 különböző forrásból való gyűjtését. Attól függően, hogy milyen számítási feladatok futnak a virtuális gépen, beállíthatja, hogy az ügynök speciális bemeneti beépülő modulokat használjanak a metrikák összegyűjtéséhez. Ilyenek például a MySQL, az NGINX és az Apache. A kimeneti beépülő modulok használatával az ügynök írhat a kiválasztott célhelyekre. A Grafi ügynök közvetlenül integrált Azure Monitor egyéni metrikák REST API. Támogatja Azure Monitor kimeneti beépülő modult. Ennek a beépülő modulnak a használatával az ügynök a Linux rendszerű virtuális gépen gyűjthet munkaterhelés-specifikus mérőszámokat, és beküldheti azokat egyéni metrikáként Azure Monitorba. 

 ![A távíró-ügynök áttekintése](./media/collect-custom-metrics-linux-telegraf/telegraf-agent-overview.png)

## <a name="send-custom-metrics"></a>Egyéni metrikák küldése 

Ebben az oktatóanyagban egy Linux rendszerű virtuális gépet telepítünk, amely az Ubuntu 16,04 LTS operációs rendszert futtatja. A-ben a legtöbb Linux operációs rendszer támogatja a a Grafi ügynököt. A Debian és az RPM csomagok is elérhetők a [InfluxData letöltési portálján](https://portal.influxdata.com/downloads)a kicsomagolt linuxos bináris fájlokkal együtt. További telepítési utasításokért és beállításokért tekintse meg ezt a [Grafi telepítési útmutatót](https://docs.influxdata.com/telegraf/v1.8/introduction/installation/) . 

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

Új linuxos virtuális gép létrehozása: 

1. Válassza az **erőforrás létrehozása** lehetőséget a bal oldali navigációs ablaktáblán. 
1. Keresse meg a **virtuális gépet**.  
1. Válassza az **Ubuntu 16,04 LTS** elemet, és válassza a **Létrehozás**lehetőséget. 
1. Adja meg a virtuális gép nevét, például **MyTelegrafVM**.  
1. Hagyja meg a lemez típusát **SSD**-ként. Ezután adjon megegy felhasználónevet, például: **azureuser**. 
1. A **Hitelesítés típusa**mezőben válassza a **jelszó**lehetőséget. Ezután adjon meg egy jelszót, amelyet később az SSH-ba fog használni a virtuális gépen. 
1. **Új erőforráscsoport létrehozásához**válassza az elemet. Ezután adjon meg egy nevet, például **myResourceGroup**. Válassza ki a **helyet**. Ezután kattintson az **OK** gombra. 

    ![Ubuntu rendszerű virtuális gép létrehozása](./media/collect-custom-metrics-linux-telegraf/create-vm.png)

1. Válasszon méretet a virtuális gép számára. Szűrhet például **számítási típus** vagy **lemeztípus** alapján. 

    ![Virtuálisgép-méret távíró-ügynök áttekintése](./media/collect-custom-metrics-linux-telegraf/vm-size.png)

1. A **hálózati** > hálózati biztonsági csoport beállítások lapján**válassza a nyilvános bejövő portok lehetőséget**, majd válassza a http és az **SSH (22)** lehetőséget. >  Tartsa meg az alapértelmezett értékeket a többi beállításnál, majd kattintson az **OK** gombra. 

1. Az Összefoglalás lapon válassza a **Létrehozás** lehetőséget a virtuális gép üzembe helyezésének megkezdéséhez. 

1. A virtuális gép rögzítve lesz az Azure Portal irányítópultján. Az üzembe helyezés befejeződése után a virtuális gép összegzése automatikusan megnyílik. 

1. A virtuális gép ablaktáblán navigáljon az **Identity (identitás** ) lapra. Győződjön **meg**arról, hogy a virtuális gépnek van-e beállítva a rendszerhez rendelt identitás. 
 
    ![A Graf VM-identitás előzetes verziója](./media/collect-custom-metrics-linux-telegraf/connect-to-VM.png)
 
## <a name="connect-to-the-vm"></a>Kapcsolódás a virtuális géphez 

Hozzon léte egy SSH-kapcsolatot a virtuális géppel. A virtuális gép áttekintő oldalán kattintson a **Csatlakozás** gombra. 

![A Grafi virtuális gépek áttekintése oldal](./media/collect-custom-metrics-linux-telegraf/connect-VM-button2.png)

A **Csatlakozás virtuális géphez** oldalon tartsa meg az alapértelmezett beállításokat a DNS-név alapján a 22-es porton keresztül való csatlakozáshoz. A **Bejelentkezés a virtuális gép helyi fiókjával**beállításnál megjelenik egy kapcsolási parancs. A parancs másolásához kattintson a gombra. Az SSH-kapcsolat parancsa az alábbi példához hasonlóan néz ki: 

```cmd
ssh azureuser@XXXX.XX.XXX 
```

Illessze be az SSH-kapcsolódási parancsot egy rendszerhéjba, például Azure Cloud Shell vagy bash on Ubuntu on Windows rendszeren, vagy használjon egy tetszőleges SSH-ügyfelet a kapcsolódás létrehozásához. 

## <a name="install-and-configure-telegraf"></a>A-Graf telepítése és konfigurálása 

A következő parancsok futtatásával telepítheti az SSH-munkamenetből a (z)-t a virtuális gépre: 

```cmd
# download the package to the VM 
wget https://dl.influxdata.com/telegraf/releases/telegraf_1.8.0~rc1-1_amd64.deb 
# install the package 
sudo dpkg -i telegraf_1.8.0~rc1-1_amd64.deb
```
A Grafi konfigurációs fájlja a következőképpen határozza meg a gyári műveleteket:. Alapértelmezés szerint egy példaként szolgáló konfigurációs fájl van telepítve a Path **/etc/Telegraf/Telegraf.conf**. A példa konfigurációs fájl felsorolja az összes lehetséges bemeneti és kimeneti beépülő modult. A következő parancsok futtatásával azonban létrehozunk egy egyéni konfigurációs fájlt, amelyet az ügynök használ: 

```cmd
# generate the new Telegraf config file in the current directory 
telegraf --input-filter cpu:mem --output-filter azure_monitor config > azm-telegraf.conf 

# replace the example config with the new generated config 
sudo cp azm-telegraf.conf /etc/telegraf/telegraf.conf 
```

> [!NOTE]  
> Az előző kód csak két bemeneti beépülő modult engedélyez: **CPU** és **mem**. A gépen futó munkaterheléstől függően további bemeneti beépülő modulokat adhat hozzá. Ilyenek például a Docker, a MySQL és az NGINX. A bemeneti beépülő modulok teljes listáját a **további konfigurációs** szakaszban találja. 

Végezetül, hogy az ügynök megkezdje az új konfiguráció használatát, a következő parancsok futtatásával kényszerítjük az ügynököt, hogy állítsa le és kezdjen el: 

```cmd
# stop the telegraf agent on the VM 
sudo systemctl stop telegraf 
# start the telegraf agent on the VM to ensure it picks up the latest configuration 
sudo systemctl start telegraf 
```
Az ügynök mostantól összegyűjti a megadott bemeneti beépülő modulok metrikáit, és kibocsátja őket a Azure Monitorba. 

## <a name="plot-your-telegraf-metrics-in-the-azure-portal"></a>A saját Graf metrikáinak ábrázolása a Azure Portalban 

1. Nyissa meg az [Azure Portalt](https://portal.azure.com). 

1. Navigáljon az új **figyelő** lapra. Ezután válasszaa metrikák lehetőséget.  

     ![Figyelő-metrikák (előzetes verzió)](./media/collect-custom-metrics-linux-telegraf/metrics.png)

1. Válassza ki a virtuális gépet az erőforrás-választóban.

     ![Metrika diagram](./media/collect-custom-metrics-linux-telegraf/metric-chart.png)

1. Válassza ki a **Graf/CPU** névteret, és válassza ki a **usage_system** metrikáját. Dönthet úgy is, hogy a metrika dimenziói alapján szűri vagy felosztja őket.  

     ![Névtér és metrika kiválasztása](./media/collect-custom-metrics-linux-telegraf/VM-resource-selector.png)

## <a name="additional-configuration"></a>További konfiguráció 

Az előző útmutató arról nyújt tájékoztatást, hogyan konfigurálható a (z)-ben a (z) A Grafi ügynök több mint 150 bemeneti beépülő modult támogat, néhány további konfigurációs lehetőséggel. A InfluxData közzétette a [támogatott beépülő modulok listáját](https://docs.influxdata.com/telegraf/v1.7/plugins/inputs/) , és [](https://docs.influxdata.com/telegraf/v1.7/administration/configuration/)útmutatást ad a konfigurálásához.  

Emellett ebben az útmutatóban a (z)-ben a (z)-ben a (z)-ben a (z)-ügynök üzembe helyezéséhez használt virtuális géppel kapcsolatos metrikák A Grafi ügynök más erőforrásokhoz tartozó mérőszámok gyűjtőként és továbbítóként is használható. Ha szeretné megtudni, hogyan konfigurálhatja az ügynököt más Azure-erőforrások metrikáinak kibocsátására, tekintse meg a [Azure monitor egyéni metrika kimenete a következőben](https://github.com/influxdata/telegraf/blob/fb704500386214655e2adb53b6eb6b15f7a6c694/plugins/outputs/azure_monitor/README.md):.  

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 

Ha már nincs rájuk szükség, törölheti az erőforráscsoportot, a virtuális gépet és az összes kapcsolódó erőforrást. Ehhez válassza ki a virtuális géphez tartozó erőforráscsoportot, és válassza a **Törlés**lehetőséget. Ezután erősítse meg a törölni kívánt erőforráscsoport nevét. 

## <a name="next-steps"></a>További lépések
- További információ az [Egyéni metrikákkal](metrics-custom-overview.md)kapcsolatban.



