---
title: Java-webalkalmazások elemzése az Azure Application Insights (2.5.0-BETA) használatával | Microsoft Docs
description: 'Alkalmazások teljesítményének figyelése Java-webalkalmazásokhoz a Application Insights (2.5.0-BETA) szolgáltatással. '
services: application-insights
documentationcenter: java
author: lgayhardt
manager: carmonm
ms.assetid: 051d4285-f38a-45d8-ad8a-45c3be828d91
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/24/2019
ms.author: lagayhar
ms.openlocfilehash: 17ad99d372bbca1d82c5c3701751da1b009764ee
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967845"
---
# <a name="get-started-with-application-insights-in-a-java-web-project-250-beta"></a>Ismerkedés a Application Insights a Java webes projektben (2.5.0-BETA)

Az [Application Insights](https://azure.microsoft.com/services/application-insights/) egy bővíthető elemzési szolgáltatás a webfejlesztők számára, amely segít megérteni az élő alkalmazása teljesítményét és használatát. Ezzel a megoldással automatikusan kérheti a kérelmeket, [nyomon követheti a függőségeket, és](auto-collect-dependencies.md#java)összegyűjtheti a teljesítményszámlálókat, diagnosztizálhatja a teljesítménnyel kapcsolatos hibákat és kivételeket, és [kódot írhat][api] a felhasználók által az alkalmazással való használatra. 

![A mintaadatok áttekintését bemutató képernyőkép](./media/java-get-started/overview-graphs.png)

Az Application Insights a Linux, Unix vagy Windows rendszeren futó Java alkalmazásokat támogatja.

A következők szükségesek:

* Java 7 vagy újabb verzió
* Egy [Microsoft Azure](https://azure.microsoft.com/)-előfizetés.

## <a name="1-get-an-application-insights-instrumentation-key"></a>1. Application Insights-kialakítási kulcs beszerzése
1. Jelentkezzen be a [Microsoft Azure Portalra](https://portal.azure.com).
2. Hozzon létre egy Application Insights-erőforrást. Állítsa be a Java webalkalmazás alkalmazástípust.

3. Keresse meg az új erőforrás kialakítási kulcsát. Ezt a kulcsot nemsokára a kódprojektbe kell illesztenie.

    ![Az új erőforrás áttekintésében kattintson a Tulajdonságok gombra, és másolja le a kialakítási kulcsot](./media/java-get-started/instrumentation-key-001.png)

## <a name="2-add-the-application-insights-sdk-for-java-to-your-project"></a>2. A Javához készült Application Insights SDK hozzáadása a projekthez
*Válassza ki a projektnek megfelelő módszert.*

#### <a name="if-youre-using-maven-a-namemaven-setup-"></a>Ha Mavent használ... <a name="maven-setup" />
Ha a projekt már úgy van beállítva, hogy Mavent használ buildként, egyesítse a következő kódot a pom.xml fájllal.

Ezután frissítse a projektfüggőségeket, hogy letöltse a bináris fájlokat.

```XML
    <dependencies>
      <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-web-auto</artifactId>
        <!-- or applicationinsights-web for manual web filter registration -->
        <!-- or applicationinsights-core for bare API -->
        <version>2.5.0-BETA</version>
      </dependency>
    </dependencies>
```

#### <a name="if-youre-using-gradle-a-namegradle-setup-"></a>Ha Gradle-t használ... <a name="gradle-setup" />
Ha a projekt már úgy van beállítva, hogy Gradle-t használ buildként, egyesítse a következő kódot a build.gradle fájllal.

Ezután frissítse a projektfüggőségeket, hogy letöltse a bináris fájlokat.

```gradle
    dependencies {
      compile group: 'com.microsoft.azure', name: 'applicationinsights-web-auto', version: '2.5.0-BETA'
      // or applicationinsights-web for manual web filter registration
      // or applicationinsights-core for bare API
    }
```

#### <a name="otherwise-if-you-are-manually-managing-dependencies-"></a>Egyéb esetben, ha manuálisan kezeli a függőségeket...
Töltse le a [legújabb verziót](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest), és a korábbi verziókat felülírva másolja a szükséges fájlokat a projektbe.

### <a name="questions"></a>Kérdések...
* *Mi a kapcsolat a `-web-auto` `-web` és `-core` az összetevők között?*
  * `applicationinsights-web-auto`olyan mérőszámokat biztosít, amelyek nyomon követik a HTTP servlet-kérelmek számát és a válaszadási időpontokat, ha automatikusan regisztrálja a Application Insights servlet szűrőt futásidőben.
  * `applicationinsights-web`olyan metrikákat is biztosít, amelyek nyomon követik a HTTP servlet-kérelmek számát és a válaszadási időpontokat, de az alkalmazásban manuálisan kell regisztrálni az Application Insights servlet-szűrőt.
  * `applicationinsights-core`csak az operációs rendszer nélküli API-t biztosítja, például ha az alkalmazás nem servlet-alapú.
  
* *Hogyan frissíthetek az SDK legújabb verziójára?*
  * Ha Gradle-t vagy Mavent használ...
    * Frissítse a Build-fájlt, és adja meg a legújabb verziót.
  * Ha manuálisan kezeli a függőségeket...
    * Töltse le a legújabb [Javához készült Application Insights SDK-t](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest), és cserélje le a régieket. A változások leírását az [SDK kiadási megjegyzéseiben](https://github.com/Microsoft/ApplicationInsights-Java#release-notes) találja.

## <a name="3-add-an-applicationinsightsxml-file"></a>3. ApplicationInsights.xml fájl hozzáadása
Adja az ApplicationInsights.xml fájlt a projekt erőforrások mappájához, vagy győződjön meg arról, hogy a projekt üzembe helyezési osztályának elérési útjához van adva. Másolja bele a következő XML-t.

Helyettesítse be az Azure Portalról kapott kialakítási kulcsot.

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">

   <!-- The key from the portal: -->
   <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>

   <!-- HTTP request component (not required for bare API) -->
   <TelemetryModules>
      <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
   </TelemetryModules>

   <!-- Events correlation (not required for bare API) -->
   <!-- These initializers add context data to each event -->
   <TelemetryInitializers>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>
   </TelemetryInitializers>

</ApplicationInsights>
```

A konfigurációs fájlt bármely, az alkalmazás számára elérhető helyen tárolhatja.  A `-Dapplicationinsights.configurationDirectory` rendszertulajdonság határozza meg, hogy melyik tár tartalmazza az ApplicationInsights.xml fájlt. Az `E:\myconfigs\appinsights\ApplicationInsights.xml` mappában tárolt konfigurációs fájl konfigurálásához például a `-Dapplicationinsights.configurationDirectory="E:\myconfigs\appinsights"` tulajdonság használható.

* A kialakítási kulcsot a telemetria minden elemével megkapja, és ez közli az Application Insights eszközzel, hogy megjelenítse azt az erőforrásban.
* A HTTP-kérelemösszetevő nem kötelező. Automatikusan telemetriát küld a kérelmekkel és válaszidőkkel kapcsolatban a portálra.
* Az eseménykorreláció a HTTP-kérelemösszetevő további eleme. Azonosítót rendel a kiszolgáló által fogadott összes kérelemhez, és az azonosítót „Operation.Id” tulajdonságként hozzáadja a telemetria minden eleméhez. Lehetővé teszi, hogy összekapcsolja az egyes kérelmekhez társított telemetria egy szűrő beállításával a [diagnosztikai keresésben][diagnostic].

### <a name="alternative-ways-to-set-the-instrumentation-key"></a>A kialakítási kulcs beállításának egyéb módjai
Az Application Insights SDK ebben a sorrendben keresi a kulcsot:

1. Rendszertulajdonság:-DAPPINSIGHTS_INSTRUMENTATIONKEY = your_ikey
2. Környezeti változó: ÁLLÍTANI AZ APPINSIGHTS_INSTRUMENTATIONKEY
3. Konfigurációs fájl: ApplicationInsights.xml

[Beállíthatja a programkódban](../../azure-monitor/app/api-custom-events-metrics.md#ikey) is:

```java
    String instrumentationKey = "00000000-0000-0000-0000-000000000000";

    if (instrumentationKey != null)
    {
        TelemetryConfiguration.getActive().setInstrumentationKey(instrumentationKey);
    }
```

## <a name="4-add-agent"></a>4. Ügynök hozzáadása

[Telepítse a Java](java-agent-25-beta.md) -ügynököt a kimenő HTTP-hívások, a JDBC-lekérdezések, az alkalmazások naplózása és a jobb működés elnevezésének rögzítéséhez.

## <a name="5-run-your-application"></a>5. Az alkalmazás futtatása
Futtassa hibakeresés módban a fejlesztési számítógépén, vagy tegye közzé a kiszolgálóján.

## <a name="6-view-your-telemetry-in-application-insights"></a>6. A telemetria megtekintése az Application Insights szolgáltatásban
Térjen vissza az Application Insights-erőforráshoz a [Microsoft Azure Portalon](https://portal.azure.com).

A HTTP-kérelemadatok az áttekintési panelen jelennek meg. (Ha nincsenek ott, várjon néhány másodpercig, majd kattintson a Frissítés gombra.)

![A mintaadatok áttekintését bemutató képernyőkép](./media/java-get-started/overview-graphs.png)

[További információk a metrikákról.][metrics]

Részletesebb összesített mérőszámokért kattintson bármelyik diagramra.

![Application Insights hibák panel diagramokkal](./media/java-get-started/006-barcharts.png)

<!--
[TODO update image with 2.5.0-BETA operation naming provided by agent]
-->

### <a name="instance-data"></a>Példányadatok
Kattintson az adott kérelemtípusokra az egyes példányok megtekintéséhez.

![Részletezés egy adott minta nézetbe](./media/java-get-started/007-instance.png)

### <a name="analytics-powerful-query-language"></a>Analytics Hatékony lekérdezési nyelv
Ahogy egyre több adatot gyűjt össze, lekérdezéseket futtathat az adatok összegzéséhez és egyéni példányok megkereséséhez is.  Az [elemzés](../../azure-monitor/app/analytics.md) erőteljes eszköz a teljesítmény és a használat megértéséhez és diagnosztikai célokra is.

![Példa elemzésre](./media/java-get-started/0025.png)

## <a name="7-install-your-app-on-the-server"></a>7. Az alkalmazás telepítése a kiszolgálón
Most tegye közzé az alkalmazást a kiszolgálón, hagyja, hogy mások használják, és nézze, ahogyan a telemetria megjelenik a portálon.

* Győződjön meg arról, hogy a tűzfal lehetővé teszi, hogy az alkalmazás telemetriát küldjön ezekre a portokra:

  * dc.services.visualstudio.com:443
  * f5.services.visualstudio.com:443

* Ha a kimenő forgalmat át kell irányítani egy tűzfalon, adja meg a `http.proxyHost` és a `http.proxyPort` rendszertulajdonságot.

* Windows-kiszolgálókon telepítse a következőt:

  * [Microsoft Visual C++ újraterjeszthető csomag](https://www.microsoft.com/download/details.aspx?id=40784)

    (Ez az összetevő lehetővé teszi a teljesítményszámlálókat.)

## <a name="azure-app-service-config-spring-boot"></a>Azure App Service config (Spring boot)

A Windows rendszeren futó Spring boot-alkalmazások további konfigurálást igényelnek az Azure App Services-on való futtatáshoz. Módosítsa a **web. config** fájlt, és adja hozzá a következőket:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <system.webServer>
        <handlers>
            <add name="httpPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified"/>
        </handlers>
        <httpPlatform processPath="%JAVA_HOME%\bin\java.exe" arguments="-Djava.net.preferIPv4Stack=true -Dserver.port=%HTTP_PLATFORM_PORT% -jar &quot;%HOME%\site\wwwroot\AzureWebAppExample-0.0.1-SNAPSHOT.jar&quot;">
        </httpPlatform>
    </system.webServer>
</configuration>
```

## <a name="exceptions-and-request-failures"></a>Kivételek és kérelemhibák
A nem kezelt kivételeket és a kérelmekkel kapcsolatos hibákat a Application Insights webes szűrő automatikusan gyűjti.

Ha más kivételekkel szeretne adatokat gyűjteni, a [kódban trackException () hívásokat is beszúrhat][apiexceptions].

## <a name="monitor-method-calls-and-external-dependencies"></a>Metódushívások és külső függőségek megfigyelése
[Telepítse a Java ügynököt](java-agent.md) a megadott belső módszerek és a JDBC-n keresztül végzett hívások naplózásához, időzítési adatokkal.

És automatikus művelet-elnevezéssel.

## <a name="w3c-distributed-tracing"></a>W3C elosztott nyomkövetés

A Application Insights Java SDK mostantól támogatja a [W3C elosztott nyomkövetést](https://w3c.github.io/trace-context/).

A bejövő SDK konfigurációját a [korrelációról](correlation.md#w3c-distributed-tracing)szóló cikkben ismertetjük.

A kimenő SDK-konfiguráció a [AI-Agent. XML](java-agent.md) fájlban van definiálva.

## <a name="performance-counters"></a>Teljesítményszámlálók
Nyissa meg a **vizsgálat**, **mérőszámok**lehetőséget, és tekintse meg a teljesítményszámlálók tartományát.

![Képernyőfelvétel a metrikák panelről a folyamat saját bájtjainak kiválasztásával](./media/java-get-started/011-perf-counters.png)

### <a name="customize-performance-counter-collection"></a>Teljesítményszámláló-gyűjtemény testreszabása
A teljesítményszámlálók standard készlete gyűjtésének letiltásához adja a következő kódot az ApplicationInsights.xml fájl gyökércsomópontja alatt:

```XML
    <PerformanceCounters>
       <UseBuiltIn>False</UseBuiltIn>
    </PerformanceCounters>
```

### <a name="collect-additional-performance-counters"></a>További teljesítményszámlálók gyűjtése
További gyűjtendő teljesítményszámlálókat határozhat meg.

#### <a name="jmx-counters-exposed-by-the-java-virtual-machine"></a>JMX-számlálók (a Java virtuális gép által feltárt)

```XML
    <PerformanceCounters>
      <Jmx>
        <Add objectName="java.lang:type=ClassLoading" attribute="TotalLoadedClassCount" displayName="Loaded Class Count"/>
        <Add objectName="java.lang:type=Memory" attribute="HeapMemoryUsage.used" displayName="Heap Memory Usage-used" type="composite"/>
      </Jmx>
    </PerformanceCounters>
```

* `displayName`– Az Application Insights portálon megjelenő név.
* `objectName`– A JMX objektum neve.
* `attribute`– A JMX objektum nevének lehívni kívánt attribútuma
* `type`(választható) – A JMX objektum attribútumának típusa:
  * Alapértelmezett: egyszerű típus, például int vagy long.
  * `composite`: a teljesítményszámláló-adatok az „Attribútum.Adat” formátumban szerepelnek
  * `tabular`: a teljesítményszámláló-adatok táblázatsor formájában szerepelnek

#### <a name="windows-performance-counters"></a>Windows-teljesítményszámlálók
Mindegyik [Windows-teljesítményszámláló](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx) egy kategória tagja (ugyanúgy, ahogyan a mezők osztályok tagjai). A kategóriák globálisak lehetnek, vagy számozott vagy elnevezett példányokkal rendelkezhetnek.

```XML
    <PerformanceCounters>
      <Windows>
        <Add displayName="Process User Time" categoryName="Process" counterName="%User Time" instanceName="__SELF__" />
        <Add displayName="Bytes Printed per Second" categoryName="Print Queue" counterName="Bytes Printed/sec" instanceName="Fax" />
      </Windows>
    </PerformanceCounters>
```

* displayName – Az Application Insights portálon megjelenő név.
* categoryName – A teljesítményszámláló kategóriája (teljesítményobjektum), amelyhez ez a teljesítményszámláló társítva van.
* counterName – A teljesítményszámláló neve.
* instanceName – A teljesítményszámláló-kategória példányneve, vagy üres sztring („”), ha a kategória egyetlen példányt tartalmaz. Ha a categoryName Folyamat, és a gyűjteni kívánt teljesítményszámláló az aktuális JVM folyamatról származik, amelyen az alkalmazása fut, adja meg a következőt: `"__SELF__"`.

### <a name="unix-performance-counters"></a>Unix-teljesítményszámlálók
* [Telepítse a gyűjteményt az Application Insights beépülő modullal](java-collectd.md) számos rendszer- és hálózati adat lekéréséhez.

## <a name="get-user-and-session-data"></a>Felhasználói és munkamenetadatok lekérése
Telemetriát küld a webkiszolgálóról. Az alkalmazás teljes körű megfigyelése érdekében további megfigyelést adhat hozzá:

* Az telemetria és a felhasználói mérőszámok figyeléséhez [vegyen fel][usage] weblapokat.
* [Állítson be][availability] webteszteket annak biztosítására, hogy az alkalmazás élő és rugalmas maradjon.

## <a name="send-your-own-telemetry"></a>Saját telemetria küldése
Most, hogy telepítette az SDK-t, az API-val saját telemetriát küldhet.

* [Nyomon követheti az egyéni eseményeket és mérőszámokat][api] , hogy megtudja, mit csinálnak a felhasználók az alkalmazással.
* [Keressen eseményeket és naplókat][diagnostic] a problémák diagnosztizálásához.

## <a name="availability-web-tests"></a>Rendelkezésre állási webes tesztek
Az Application Insights rendszeres időközönként teszteli a webhelyét, hogy működik és jól válaszol-e.

[További információ a rendelkezésre állási webes tesztek beállításáról.][availability]

## <a name="questions-problems"></a>Kérdései vannak? Problémákat tapasztal?
[A Java hibaelhárítása](java-troubleshoot.md)

## <a name="next-steps"></a>További lépések
* [Függőségi hívások figyelése](java-agent.md)
* [Unix-teljesítményszámlálók figyelése](java-collectd.md)
* [A weboldalak figyelésével](javascript.md) megfigyelheti az oldalbetöltési időket, az AJAX-hívásokat és a böngészőkivételeket.
* [Egyéni telemetriát](../../azure-monitor/app/api-custom-events-metrics.md) írhat a böngészőben vagy a kiszolgálón való használat nyomon követése érdekében.
* Az [Elemzések](../../azure-monitor/app/analytics.md) használatával hatékony telemetriai lekérdezéseket hajthat végre az alkalmazásból
* További információ: [Azure Java-fejlesztőknek](/java/azure).

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiexceptions]: ../../azure-monitor/app/api-custom-events-metrics.md#trackexception
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[javalogs]: java-trace-logs-25-beta.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
[usage]: javascript.md
