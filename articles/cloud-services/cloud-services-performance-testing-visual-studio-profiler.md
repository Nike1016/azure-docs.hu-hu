---
title: Felhőalapú szolgáltatás profilkészítése helyileg a számítási emulátorban | Microsoft Docs
services: cloud-services
description: A Cloud Services teljesítménnyel kapcsolatos problémáinak vizsgálata a Visual Studio Profilerrel
documentationcenter: ''
author: mikejo
manager: jillfra
editor: ''
tags: ''
ms.assetid: 25e40bf3-eea0-4b0b-9f4a-91ffe797f6c3
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/18/2016
ms.author: mikejo
ms.openlocfilehash: 4844e07b83f7e529d7e3de2c5bac1dadb5414391
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69515944"
---
# <a name="testing-the-performance-of-a-cloud-service-locally-in-the-azure-compute-emulator-using-the-visual-studio-profiler"></a>Felhőalapú szolgáltatás teljesítményének tesztelése helyileg az Azure számítási emulátorban a Visual Studio Profiler használatával
A Cloud Services teljesítményének teszteléséhez különféle eszközök és technikák állnak rendelkezésre.
Ha felhőalapú szolgáltatást tesz közzé az Azure-ban, akkor a Visual Studio begyűjtheti a profilkészítési adatokat, majd helyileg elemezheti azt az [Azure-alkalmazás profilkészítése][1]című részben leírtak szerint.
A diagnosztika használatával számos teljesítményszámláló nyomon követhető a [teljesítményszámlálók az Azure][2]-ban című témakörben leírtak szerint.
A felhőbe való üzembe helyezés előtt érdemes lehet az alkalmazás helyi profilját is felvenni a Compute Emulatorba.

Ez a cikk a profilkészítés CPU-mintavételezési módszerét ismerteti, amely helyben végezhető el az emulátorral. A CPU-mintavételezés olyan profilkészítési módszer, amely nem zavaró. Egy kijelölt mintavételi időszakban a Profiler pillanatképet készít a hívási veremről. Az adatok gyűjtése egy adott időszakon belül történik, és egy jelentésben jelenik meg. Ez a profilkészítési módszer általában azt jelzi, hogy hol történik a CPU-munka nagy részét képező számítási igényű alkalmazás.  Ez lehetőséget nyújt arra, hogy a "gyors elérésű útvonalra" koncentráljon, ahol az alkalmazás a legtöbb időt tölti.

## <a name="1-configure-visual-studio-for-profiling"></a>1: A Visual Studio konfigurálása profilkészítéshez
Először is van néhány Visual Studio konfigurációs lehetőség, amely a profilkészítés során hasznos lehet. A profilkészítési jelentések értelmezéséhez szüksége lesz az alkalmazáshoz tartozó szimbólumokra (. pdb fájlokra), valamint a rendszerkönyvtárakhoz tartozó szimbólumokra is. Érdemes meggyőződnie arról, hogy az elérhető szimbólum-kiszolgálókra hivatkozik. Ehhez a Visual Studióban a **Tools (eszközök** ) menüben válassza a **Beállítások**, majd a **hibakeresés**, majd a **szimbólumok**elemet. Győződjön meg arról, hogy a Microsoft Symbol kiszolgálók szerepelnek a **Symbol file (. PDB) helyein**.  Hivatkozhat https://referencesource.microsoft.com/symbols arra is, hogy további szimbólumokat tartalmazó fájlok is szerepelhetnek.

![Szimbólum beállításai][4]

Ha kívánja, egyszerűbbé teheti a Profiler által generált jelentéseket a Saját kód beállításával. Ha a Saját kód engedélyezve van, a függvény hívási veremei egyszerűsítettek, így a teljes mértékben belső hívások a tárakban, a .NET-keretrendszer pedig rejtve marad a jelentésekben. Az **eszközök** menüben válassza a **Beállítások lehetőséget**. Ezután bontsa ki a **Teljesítményfigyelő eszközök** csomópontot, és válassza az **általános**lehetőséget. Jelölje be a **saját kód engedélyezése a Profiler**-jelentésekhez jelölőnégyzetet.

![Saját kód beállítások][17]

Ezeket az utasításokat meglévő projekttel vagy új projekttel is használhatja.  Ha új projektet hoz létre az alább ismertetett módszerek kipróbálásához, válasszon ki C# egy **Azure Cloud Service** -projektet, és válasszon ki egy **webes szerepkört** és egy feldolgozói **szerepkört**.

![Azure Cloud Service-projekt szerepkörei][5]

Például vegyen fel egy kódot a projektbe, amely sok időt vesz igénybe, és nyilvánvaló teljesítménnyel kapcsolatos problémát mutat. Adja hozzá például a következő kódot egy feldolgozói szerepkör projekthez:

```csharp
public class Concatenator
{
    public static string Concatenate(int number)
    {
        int count;
        string s = "";
        for (count = 0; count < number; count++)
        {
            s += "\n" + count.ToString();
        }
        return s;
    }
}
```

Ezt a kódot a feldolgozói szerepkör RoleEntryPoint származtatott osztályának RunAsync metódusával hívja meg. (Hagyja figyelmen kívül a szinkron módon futó metódus figyelmeztetését.)

```csharp
private async Task RunAsync(CancellationToken cancellationToken)
{
    // TODO: Replace the following with your own logic.
    while (!cancellationToken.IsCancellationRequested)
    {
        Trace.TraceInformation("Working");
        Concatenator.Concatenate(10000);
    }
}
```

A Cloud Service-t hibakeresés nélkül (CTRL + F5) is kiépítheti és futtathatja a megoldás-konfiguráció kiadásával. Ez biztosítja, hogy minden fájl és mappa az alkalmazás helyi futtatásához legyen létrehozva, és gondoskodik arról, hogy az összes emulátor el legyen indítva. Indítsa el a számítási emulátor felhasználói felületét a tálcáról annak ellenőrzéséhez, hogy a feldolgozói szerepkör fut-e.

## <a name="2-attach-to-a-process"></a>2: Csatolás egy folyamathoz
Az alkalmazásnak a Visual Studio 2010 IDE-ből való elindításával való profilkészítése helyett a Profilert egy futó folyamathoz kell csatlakoztatnia. 

Ha a Profilert egy folyamathoz szeretné csatolni, az **elemzés** menüben válassza a **Profiler** és a **csatolás/leválasztás**lehetőséget.

![Profil csatolása lehetőség][6]

Feldolgozói szerepkör esetén keresse meg a WaWorkerHost. exe folyamatot.

![WaWorkerHost folyamat][7]

Ha a projekt mappája egy hálózati meghajtón található, a Profiler megkéri, hogy adjon meg egy másik helyet a profilkészítési jelentések mentéséhez.

 A WaIISHost. exe fájlhoz csatolva is csatolhat webes szerepkörhöz.
Ha az alkalmazásban több feldolgozói szerepkör is található, akkor a folyamatazonosító kell használnia, hogy megkülönböztesse őket. A folyamatazonosító programozott módon kérdezheti le a Process objektum elérésével. Ha például hozzáadja ezt a kódot a RoleEntryPoint származtatott osztályának futtatási metódusához egy szerepkörben, a számítási emulátor felhasználói felületén megtekintheti a naplót, amelyből megtudhatja, hogy milyen folyamathoz csatlakozhat.

```csharp
var process = System.Diagnostics.Process.GetCurrentProcess();
var message = String.Format("Process ID: {0}", process.Id);
Trace.WriteLine(message, "Information");
```

A napló megtekintéséhez indítsa el a számítási emulátor felhasználói felületét.

![A számítási emulátor felhasználói felületének elindítása][8]

A konzol ablakának címsorára kattintva nyissa meg a feldolgozói szerepkör naplózása konzol ablakát a számítási emulátor felhasználói felületén. A naplóban láthatja a folyamat AZONOSÍTÓját.

![Folyamat AZONOSÍTÓjának megtekintése][9]

Az egyiket, amelyet csatolt, hajtsa végre az alkalmazás felhasználói felületének (ha szükséges) lépéseit a forgatókönyv újbóli létrehozásához.

Ha le szeretné állítani a profilkészítést, válassza a **profilkészítés leállítása** hivatkozást.

![Profilkészítés leállítása beállítás][10]

## <a name="3-view-performance-reports"></a>3: Teljesítményadatokat tartalmazó jelentések megtekintése
Megjelenik az alkalmazáshoz tartozó teljesítmény jelentés.

Ezen a ponton a Profiler leállítja a végrehajtást, egy. VSP fájlba menti az adatait, és megjelenít egy jelentést, amely megjeleníti az adatelemzést.

![Profiler-jelentés][11]

Ha a "string. wstrcpy" szöveget látja a gyors elérési úton, kattintson a Saját kód elemre a nézet módosításához a csak felhasználói kód megjelenítéséhez.  Ha a string. concat látja, próbálkozzon a minden kód megjelenítése gomb megnyomásával.

Ekkor meg kell jelennie az ÖSSZEFŰZ metódusnak és a string. összefűzésnek, amely a végrehajtási idő nagy részét veszi figyelembe.

![Jelentés elemzése][12]

Ha ebben a cikkben a karakterlánc-összefűzési kódot adta hozzá, akkor a Feladatlista figyelmeztetés jelenik meg. Azt is láthatja, hogy a rendszer túl nagy mennyiségű szemetet gyűjt, ami a létrehozott és ártalmatlanított karakterláncok száma miatt.

![Teljesítménnyel kapcsolatos figyelmeztetések][14]

## <a name="4-make-changes-and-compare-performance"></a>4: Módosítások végrehajtása és a teljesítmény összehasonlítása
A teljesítményt a kód módosítása előtt és után is összehasonlíthatja.  Állítsa le a futó folyamatot, és szerkessze a kódot, és cserélje le a karakterlánc-összefűzési műveletet a StringBuilder használatával:

```csharp
public static string Concatenate(int number)
{
    int count;
    System.Text.StringBuilder builder = new System.Text.StringBuilder("");
    for (count = 0; count < number; count++)
    {
        builder.Append("\n" + count.ToString());
    }
    return builder.ToString();
}
```

Végezzen el egy másik teljesítményt, majd hasonlítsa össze a teljesítményt. Ha a teljesítmény-kezelőben a futtatások ugyanabban a munkamenetben találhatók, egyszerűen kiválaszthatja mindkét jelentést, megnyithatja a helyi menüt, és választhatja a **teljesítmény-jelentések összehasonlítása**lehetőséget is. Ha egy másik teljesítmény-munkamenetben lévő futtatással szeretne összehasonlítani, nyissa meg az **elemzés** menüt, és válassza a **teljesítmény-jelentések összehasonlítása**lehetőséget. A megjelenő párbeszédpanelen mindkét fájlt meg kell adni.

![Teljesítmény-jelentések összehasonlítása lehetőség][15]

A jelentések kiemelik a két futtatás közötti különbségeket.

![Összehasonlító jelentés][16]

Gratulálunk! A Profiler elindult.

## <a name="troubleshooting"></a>Hibaelhárítás
* Győződjön meg arról, hogy a kiadás létrehozásához profilkészítést készít, és hibakeresés nélkül indítja el.
* Ha a Profiler menüben nincs engedélyezve a csatolás/leválasztás lehetőség, futtassa a teljesítmény varázslót.
* Az alkalmazás állapotának megtekintéséhez használja a számítási emulátor felhasználói felületét. 
* Ha problémák merülnek fel az emulátorban, vagy csatolja a Profilert, állítsa le a Compute Emulator, majd indítsa újra. Ha ez nem oldja meg a problémát, próbálja meg újraindítani. Ez a probléma akkor fordulhat elő, ha a számítási emulátor használatával felfüggeszti és eltávolítja a futó központi telepítéseket.
* Ha a parancssorból bármelyik profilkészítési parancsot használta, különösen a globális beállításokat, győződjön meg arról, hogy a VSPerfClrEnv/globaloff meghívása megtörtént, és hogy a VsPerfMon. exe le lett állítva.
* Ha mintavételezéskor a következő üzenet jelenik meg: "PRF0025: Nem történt adatgyűjtés, "győződjön meg róla, hogy a csatlakoztatott folyamat CPU-tevékenységgel rendelkezik. Előfordulhat, hogy a számítási munkát nem végző alkalmazások nem hoznak létre mintavételi adatmennyiséget.  Az is lehetséges, hogy a folyamat kilépett a mintavételezés befejezése előtt. Ellenőrizze, hogy egy profilkészítési szerepkör futtatási metódusa nem áll-e le.

## <a name="next-steps"></a>További lépések
A Visual Studio Profiler nem támogatja az Azure bináris fájljainak az emulátorban való kiosztását, de ha a memóriát le szeretné tesztelni, kiválaszthatja ezt a lehetőséget a profilkészítés során. A párhuzamossági profilkészítést is választhatja, amely segít megállapítani, hogy a szálak a zárolások vagy a rétegbeli interakciók profilkészítését használják-e, ami segít nyomon követni a teljesítménnyel kapcsolatos problémákat, amikor interakciót végez az alkalmazások szintjei között. gyakran az adatcsomag és a feldolgozói szerepkör között.  Megtekintheti az alkalmazás által generált adatbázis-lekérdezéseket, és a profilkészítési adatait használva javíthatja az adatbázis használatát. További információ a többplatformos interakciós profilkészítéssel kapcsolatban: [a blogbejegyzés áttekintése: A platform interakciós Profiler használata a Visual Studio Team System][3]2010-ben.

[1]: https://docs.microsoft.com/azure/application-insights/app-insights-profiler
[2]: https://msdn.microsoft.com/library/azure/hh411542.aspx
[3]: https://blogs.msdn.com/b/habibh/archive/2009/06/30/walkthrough-using-the-tier-interaction-profiler-in-visual-studio-team-system-2010.aspx
[4]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally09.png
[5]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally10.png
[6]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally02.png
[7]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally05.png
[8]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally010.png
[9]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally07.png
[10]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally06.png
[11]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally03.png
[12]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally011.png
[14]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally04.png 
[15]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally013.png
[16]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally012.png
[17]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally08.png
