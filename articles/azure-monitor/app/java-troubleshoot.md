---
title: Java-webprojektek Application Insightsának hibáinak megoldása
description: Hibaelhárítási útmutató – élő Java-alkalmazások figyelése Application Insightsokkal.
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: ef602767-18f2-44d2-b7ef-42b404edd0e9
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: mbullwin
ms.openlocfilehash: a26302b0c0b4361fe3e7aae6aba798f433c72ade
ms.sourcegitcommit: d060947aae93728169b035fd54beef044dbe9480
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2019
ms.locfileid: "68742187"
---
# <a name="troubleshooting-and-q-and-a-for-application-insights-for-java"></a>Hibaelhárítás, kérdések és válaszok: Application Insights Java-hoz
[Az Azure Application Insights-][java]mel kapcsolatos kérdések és problémák Java-ban? Íme néhány tipp.

## <a name="build-errors"></a>Felépítési hibák
**Az Eclipse-ben vagy a IntelliJ-ben, ha a Application Insights SDK-t Maven-vagy Gradle-n keresztül adja hozzá, a Build vagy az ellenőrzőösszeg érvényesítési hibáit.**

* Ha a függőségi `<version>` elem helyettesítő karakterekkel rendelkező mintát használ (például Maven vagy (Gradle) `version:'2.0.+'`), próbáljon meg inkább egy adott verziót megadni `2.0.1`, például:. `<version>[2.0,)</version>` Tekintse meg a legújabb verzió [kibocsátási megjegyzéseit](https://github.com/Microsoft/ApplicationInsights-Java/releases) .

## <a name="no-data"></a>Nincs adat
**Felvettem Application Insights sikeresen, és futtattam az alkalmazást, de még soha nem láttam az adataim a portálon.**

* Várjon egy percet, és kattintson a frissítés gombra. A diagramok rendszeresen frissülnek, de manuálisan is frissítheti őket. A frissítési időköz a diagram időtartományának függvénye.
* Győződjön meg arról, hogy a ApplicationInsights. xml fájlban (a projekt erőforrások mappájában) vagy környezeti változóként konfigurált kialakítási kulccsal rendelkezik.
* Ellenőrizze, hogy `<DisableTelemetry>true</DisableTelemetry>` nincs-e csomópont az XML-fájlban.
* Előfordulhat, hogy a tűzfalon meg kell nyitnia a 80-es és a 443-es TCP-portot a dc.services.visualstudio.com kimenő forgalmához. A [tűzfal-kivételek teljes listáját](../../azure-monitor/app/ip-addresses.md) lásd:
* A Microsoft Azure kezdőképernyőn tekintse meg a szolgáltatás állapotát jelző lapot. Ha vannak riasztási jelzések, várjon, amíg vissza nem tért az OK gombra, majd zárjunk be és nyissa meg újra a Application Insights alkalmazás paneljét.
* A [naplózás](#debug-data-from-the-sdk) bekapcsolásához vegyen fel egy `<SDKLogger />` elemet a ApplicationInsights. xml fájl legfelső szintű csomópontjában (a projekt erőforrások mappájába), és keresse meg a AI használatával előre megadott bejegyzéseket: INFORMÁCIÓ/figyelmeztetés/hiba bármely gyanús naplónál. 
* Győződjön meg arról, hogy a Java SDK sikeresen betöltötte a megfelelő ApplicationInsights. xml fájlt, és megtekinti a konzol kimeneti üzeneteit a "konfigurációs fájl sikeresen megtalálva" utasításban.
* Ha a konfigurációs fájl nem található, tekintse meg a kimeneti üzeneteket, ahol megtekintheti a konfigurációs fájl keresésének helyét, és győződjön meg arról, hogy a ApplicationInsights. xml az egyik keresési helyen található. Az általános szabály, hogy a konfigurációs fájlt a Application Insights SDK-tégelyek közelében helyezheti el. Például: a Tomcat-ben ez a WEB-INF/classs mappát jelenti. A fejlesztés során elhelyezheti a ApplicationInsights. xml fájlt a webes projekt erőforrások mappájába.
* Tekintse meg a [GitHub-problémák oldalát](https://github.com/Microsoft/ApplicationInsights-Java/issues) is, amely az SDK ismert problémáit tartalmazza.
* A verziók ütközésével kapcsolatos problémák elkerülése érdekében győződjön meg arról, hogy az Application Insights Core, web, Agent és naplózási hozzáfűzése azonos verzióját használja.

#### <a name="i-used-to-see-data-but-it-has-stopped"></a>Felhasználtam az adatmegjelenítést, de leállt
* Keresse meg az [állapot blogját](https://blogs.msdn.com/b/applicationinsights-status/).
* Megtalálta az adatpontok havi kvótáját? A beállítások/kvóta és díjszabás megnyitásával tájékozódhat. Ha igen, lehetősége van a csomag frissítésére, vagy a további kapacitás megfizetésére. Tekintse [](https://azure.microsoft.com/pricing/details/application-insights/)meg a díjszabási sémát.
* Nemrég frissítette az SDK-t? Győződjön meg arról, hogy csak egyedi SDK-tégelyek jelennek meg a projekt címtárában. Az SDK-nak nem lehet két különböző verziója.
* A megfelelő AI-erőforrást keresi? Adja meg az alkalmazás Rendszerállapotkulcsot az erőforráshoz, ahol a telemetria vár. Azonosnak kell lenniük.

#### <a name="i-dont-see-all-the-data-im-expecting"></a>Nem látom az összes várt adatértéket
* Nyissa meg a használati és becsült költségek lapot, és győződjön meg arról, hogy a [mintavételezés](../../azure-monitor/app/sampling.md) működik-e. (a 100%-os átviteli érték azt jelenti, hogy a mintavételezés nem működik.) A Application Insights szolgáltatás beállítható úgy, hogy csak az alkalmazásból érkező telemetria egy részét fogadja el. Ez segít megőrizni a telemetria havi kvótáját.
* Be van kapcsolva az SDK-mintavétel? Ha igen, az adat mintavétele az összes vonatkozó típushoz megadott sebességgel történik.
* A Java SDK egy régebbi verzióját futtatja? Az 2.0.1-es verziótól kezdődően a hibatűrési mechanizmust bevezetjük az időszakos hálózati és háttérbeli hibák kezelésére, valamint az adatmegőrzésre a helyi meghajtókon.
* A túlságosan nagy telemetria miatt szabályozásra kerül? Ha bekapcsolja az információ-naplózást, megjelenik egy "alkalmazás szabályozása" üzenet. Aktuális korlátunk a 32k telemetria-elemek/másodperc.

### <a name="java-agent-cannot-capture-dependency-data"></a>A Java-ügynök nem tudja rögzíteni a függőségi adatmennyiséget
* Konfigurálta a Java-ügynököt a [Java-ügynök konfigurálása](java-agent.md) után?
* Győződjön meg arról, hogy a Java Agent jar és a AI-Agent. xml fájl is ugyanabba a mappába kerül.
* Győződjön meg arról, hogy az automatikus gyűjtéshez szükséges függőséget az automatikus gyűjtemény támogatja. Jelenleg csak a MySQL, az MsSQL, a Oracle DB és az Azure cache támogatott a Redis függőségi gyűjteményhez.
* A JDK 1,7 vagy a 1,8-es verzióját használja? Jelenleg nem támogatott a függőségi gyűjtemény a JDK 9-ben.

## <a name="no-usage-data"></a>Nincsenek használati adatok
**Megjelennek a kérelmekkel és a válaszadási időpontokkal kapcsolatos információk, de nem találhatók meg az oldal nézet, a böngésző vagy a felhasználói információ.**

Sikeresen beállította az alkalmazást, hogy telemetria küldjön a kiszolgálóról. A következő lépés a weblapok [beállítása a telemetria a webböngészőből való][usage]elküldéséhez.

Azt is megteheti, hogy ha az ügyfél egy [telefonon vagy más eszközön][platforms]lévő alkalmazás, telemetria is küldhet.

Használja ugyanazt a kialakítási kulcsot az ügyfél és a kiszolgáló telemetria beállításához. Az adatok ugyanabban a Application Insights erőforrásban jelennek meg, és az ügyfél és a kiszolgáló eseményei is összekapcsolhatók.


## <a name="disabling-telemetry"></a>Telemetria letiltása
**Hogyan lehet letiltani a telemetria-gyűjteményt?**

A kódban:

```Java

    TelemetryConfiguration config = TelemetryConfiguration.getActive();
    config.setTrackingIsDisabled(true);
```

**vagy**

Frissítse a ApplicationInsights. xml fájlt (a projekt erőforrások mappájából). Adja hozzá a következőt a gyökérszintű csomóponthoz:

```XML

    <DisableTelemetry>true</DisableTelemetry>
```

Az XML-metódus használatával újra kell indítania az alkalmazást az érték módosításakor.

## <a name="changing-the-target"></a>A cél módosítása
**Hogyan változtathatom meg, hogy a projekt melyik Azure-erőforráshoz küld adatokat?**

* [Szerezze be az új erőforrás rendszerállapot-kulcsát.][java]
* Ha a Azure Toolkit for Eclipse használatával adta hozzá Application Insights a projekthez, kattintson a jobb gombbal a webes projektre, válassza az **Azure**-t, **konfigurálja a Application Insights**, és módosítsa a kulcsot.
* Ha a kialakítási kulcsot környezeti változóként konfigurálta, frissítse a környezeti változó értékét új Rendszerállapotkulcsot.
* Ellenkező esetben frissítse a kulcsot a ApplicationInsights. xml fájlban a projekt erőforrások mappájából.

## <a name="debug-data-from-the-sdk"></a>Adatok hibakeresése az SDK-ból

**Honnan tudhatom meg, hogy mit csinál az SDK?**

Ha többet szeretne megtudni arról, hogy mi történik az API-ban `<SDKLogger/>` , adja hozzá a ApplicationInsights. xml konfigurációs fájljának gyökérszintű csomópontját.

### <a name="applicationinsightsxml"></a>ApplicationInsights.xml

Azt is utasíthatja, hogy a naplózó fájlba írja a kimenetet:

```XML
  <SDKLogger type="FILE"><!-- or "CONSOLE" to print to stderr -->
    <Level>TRACE</Level>
    <UniquePrefix>AI</UniquePrefix>
    <BaseFolderPath>C:/agent/AISDK</BaseFolderPath>
</SDKLogger>
```

### <a name="spring-boot-starter"></a>Spring boot Starter

Ha az SDK-naplózást a Spring boot-alkalmazásokkal szeretné engedélyezni a Application Insights Spring boot Starter `application.properties` használatával, adja hozzá a következőt a fájlhoz:

```yaml
azure.application-insights.logger.type=file
azure.application-insights.logger.base-folder-path=C:/agent/AISDK
azure.application-insights.logger.level=trace
```

vagy a standard hibába való nyomtatáshoz:

```yaml
azure.application-insights.logger.type=console
azure.application-insights.logger.level=trace
```

### <a name="java-agent"></a>Java-ügynök

A JVM-ügynök naplózásának engedélyezéséhez frissítse a [AI-Agent. xml fájlt](java-agent.md):

```xml
<AgentLogger type="FILE"><!-- or "CONSOLE" to print to stderr -->
    <Level>TRACE</Level>
    <UniquePrefix>AI</UniquePrefix>
    <BaseFolderPath>C:/agent/AIAGENT</BaseFolderPath>
</AgentLogger>
```

### <a name="java-command-line-properties"></a>Java parancssori tulajdonságok
_A verzió 2.4.0 óta_

A naplózás engedélyezése parancssori kapcsolók használatával a konfigurációs fájlok módosítása nélkül:

```
java -Dapplicationinsights.logger.file.level=trace -Dapplicationinsights.logger.file.uniquePrefix=AI -Dapplicationinsights.logger.baseFolderPath="C:/my/log/dir" -jar MyApp.jar
```

vagy a standard hibába való nyomtatáshoz:

```
java -Dapplicationinsights.logger.console.level=trace -jar MyApp.jar
```

## <a name="the-azure-start-screen"></a>Az Azure kezdőképernyő
**Megtekintem [a Azure Portal](https://portal.azure.com). A Térkép az alkalmazásról szól?**

Nem, az Azure-kiszolgálók állapotát mutatja az egész világon.

*Az Azure-beli kezdőképernyőn (kezdőképernyő) Hogyan kereshetek meg az alkalmazással kapcsolatos adatok?*

Feltételezve, hogy az [alkalmazást a Application Insightshoz állítja be][java], kattintson a Tallózás gombra, válassza a Application Insights lehetőséget, majd válassza ki az alkalmazáshoz létrehozott alkalmazás-erőforrást. A jövőben a közeljövőben rögzítheti az alkalmazást az üzenőfalon.

## <a name="intranet-servers"></a>Intranetes kiszolgálók
**Figyelhető egy kiszolgáló az intraneten?**

Igen, ha a kiszolgáló telemetria tud küldeni a Application Insights portálra a nyilvános interneten keresztül.

Előfordulhat, hogy a tűzfalon a 80-es és a 443-es TCP-portot kell megnyitnia a kimenő forgalom dc.services.visualstudio.com és f5.services.visualstudio.com.

## <a name="data-retention"></a>Adatmegőrzés
**Mennyi ideig tartanak a portálon tárolt adat? Biztonságos?**

Tekintse meg [az adatmegőrzést és az][data]adatvédelmet.

## <a name="debug-logging"></a>Hibakeresési naplózás
Application Insights használja `org.apache.http`. Ez a névtér `com.microsoft.applicationinsights.core.dependencies.http`alatt Application Insights Core tégelyben található. Ez lehetővé teszi, hogy a Application Insights olyan forgatókönyveket kezeljen, `org.apache.http` amelyekben ugyanazok a különböző verziói találhatók meg egy kódon belül.

>[!NOTE]
>Ha engedélyezi a hibakeresési szintű naplózást az alkalmazás összes névterében, a rendszer az összes végrehajtó modult tiszteletben tartja, `org.apache.http` beleértve a nevet `com.microsoft.applicationinsights.core.dependencies.http`is. A Application Insights nem fogja tudni alkalmazni ezeket a hívásokat, mert az Apache Library a napló hívását végzi. A HIBAKERESÉSi szint naplózása jelentős mennyiségű naplófájlt eredményez, és nem ajánlott éles üzemi példányok esetén.


## <a name="next-steps"></a>További lépések
**Beállítottam Application Insights a Java Server-alkalmazáshoz. Mit tehetek?**

* [Weblapok rendelkezésre állásának monitorozása][availability]
* [Weblap használatának figyelése][usage]
* [A használat követése és a problémák diagnosztizálása az eszköz alkalmazásaiban][platforms]
* [Kód írása az alkalmazás használatának nyomon követéséhez][track]
* [Diagnosztikai naplók rögzítése][javalogs]

## <a name="get-help"></a>Segítségkérés
* [Stack Overflow](https://stackoverflow.com/questions/tagged/ms-application-insights)
* [Probléma fájl a GitHubon](https://github.com/Microsoft/ApplicationInsights-Java/issues)

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[data]: ../../azure-monitor/app/data-retention-privacy.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[platforms]: ../../azure-monitor/app/platforms.md
[track]: ../../azure-monitor/app/api-custom-events-metrics.md
[usage]: javascript.md

