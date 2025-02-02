---
title: 'Oktatóanyag: Hangok engedélyezése a robotnak a Speech SDK használatával'
titleSuffix: Azure Cognitive Services
description: Ebben az oktatóanyagban egy echo-robotot fog létrehozni a Microsoft bot-Framework használatával, üzembe helyezi az Azure-ban, és regisztrálja azt a robot-keretrendszer Direct line beszédfelismerési csatornával. Ezután konfigurálhat egy Windows rendszerbeli ügyfélalkalmazás-alkalmazást, amely lehetővé teszi, hogy beszéljen a robothoz, és meghallgassa, hogy válaszoljon vissza Önnek.
services: cognitive-services
author: dargilco
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: dcohen
ms.openlocfilehash: 3c57200591f3b7de9a1f9ab4198e55ed844b4d07
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932058"
---
# <a name="tutorial-voice-enable-your-bot-using-the-speech-sdk"></a>Oktatóanyag: Hang – engedélyezze a robotot a Speech SDK használatával

Most már használhatja a Speech Services hatékonyságát, így egyszerűen elvégezheti a csevegési robot használatát.

Ebben az oktatóanyagban egy echo-robotot fog létrehozni a Microsoft bot-Framework használatával, üzembe helyezi az Azure-ban, és regisztrálja azt a robot-keretrendszer Direct line beszédfelismerési csatornával. Ezután konfigurálhat egy Windows rendszerbeli ügyfélalkalmazás-alkalmazást, amely lehetővé teszi, hogy beszéljen a robothoz, és meghallgassa, hogy válaszoljon vissza Önnek.

Ez az oktatóanyag olyan fejlesztők számára készült, akik most kezdik az Azure-t, a bot-Framework botokat, a közvetlen vonalas beszédet vagy a Speech SDK-t, és szeretnék gyorsan kiépíteni egy munkarendszert korlátozott kódolással. Ezeknek a szolgáltatásoknak nem szükséges tapasztalata vagy ismerete.

A gyakorlat végén egy olyan rendszer lesz beállítva, amely a következőképpen fog működni:

1. A minta ügyfélalkalmazás úgy van konfigurálva, hogy a Direct line Speech Channelhez és az ECHO Robothoz kapcsolódjon.
2. A hang rögzítése az alapértelmezett mikrofonból a gomb megnyomásakor (vagy folyamatosan rögzített, ha az egyéni ébresztési szó aktiválva van)
3. Igény szerint az egyéni ébresztési szó észlelése történik, a kapuzás a felhőbe
4. Az alkalmazás a Speech SDK használatával csatlakozik a Direct line Speech Channel és a Streams hanghoz
5. Igény szerint nagyobb pontosságú ébresztési Word-ellenőrzés történik a szolgáltatásban
6. A rendszer átadja a hangot a beszédfelismerési szolgáltatásnak, és szöveget ad át
7. A rendszer a felismert szöveget az ECHO-bot-ként adja át bot Framework-tevékenységként 
8. A szöveg-beszéd (TTS) szolgáltatás a válaszüzenetet a hanganyagba helyezi, és a lejátszásra az ügyfélalkalmazás felé áramlik.

![diagram – címke](media/tutorial-voice-enable-your-bot-speech-sdk/diagram.png "A beszédfelismerési csatorna folyamata")

> [!NOTE]
> Az oktatóanyag lépései nem igényelnek fizetős szolgáltatást. Új Azure-felhasználóként használhat krediteket az ingyenes Azure Trail-előfizetésből, és a Speech Services ingyenes szintjéről is elvégezheti ezt az oktatóanyagot.

Az oktatóanyag a következőket ismerteti:
> [!div class="checklist"]
> * Új Azure-erőforrások létrehozása
> * Az ECHO-robot minta létrehozása, tesztelése és üzembe helyezése egy Azure App Service
> * A robot regisztrálása közvetlen vonalas beszédfelismerési csatornával
> * A Direct line Speech-ügyfél létrehozása és futtatása az ECHO-robottal való kommunikációhoz
> * Egyéni ébresztési szó aktiválásának hozzáadása
> * Megtudhatja, hogyan módosíthatja a felismert és beszélt beszéd nyelvét

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőket kell elvégeznie:

- Windows 10 rendszerű számítógép, amely egy működő mikrofonnal és beszélővel (vagy fejhallgatóval) rendelkezik
- [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) vagy újabb
- [.NET Core SDK](https://dotnet.microsoft.com/download) 2,1-es vagy újabb verzió
- Egy Azure-fiók. [Regisztráljon ingyen](https://azure.microsoft.com/free/ai/).
- Egy [GitHub](https://github.com/) -fiók
- [Git for Windows](https://git-scm.com/download/win)

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az oktatóanyagban létrehozandó ügyfélalkalmazás néhány Azure-szolgáltatást használ. Ha csökkenteni szeretné a robottól érkező válaszok időkorlátját, győződjön meg arról, hogy ezek a szolgáltatások ugyanabban az Azure-régióban találhatók. Ebben a szakaszban egy erőforráscsoportot hoz létre az **USA nyugati** régiójában. Ezt az erőforráscsoportot fogja használni a rendszer a bot-Framework, a Direct line Speech Channel és a Speech Services egyedi erőforrásainak létrehozásakor.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldali navigációs sávon válassza az **erőforráscsoportok**lehetőséget. Ezután kattintson a **Hozzáadás** gombra egy új erőforráscsoport hozzáadásához.
3. A rendszer a következő információk megadását kéri:
   * **Előfizetés** beállítása az **ingyenes próbaverzióra** (meglévő előfizetést is használhat).
   * Adja meg az **erőforráscsoport**nevét. Javasoljuk, hogy **SpeechEchoBotTutorial-ResourceGroup**.
   * A **régió** legördülő menüben válassza az **USA nyugati**régiója lehetőséget.
4. Kattintson **a felülvizsgálat és létrehozás**gombra. Ekkor meg kell jelennie egy olyan szalagcímnek, amely beolvasta az **érvényesítést**.
5. Kattintson a **Create** (Létrehozás) gombra. Az erőforráscsoport létrehozása eltarthat néhány percig.
6. Az oktatóanyag későbbi részében létrehozott erőforrásokhoz hasonlóan érdemes ezt az erőforráscsoportot az irányítópultra rögzíteni az egyszerű hozzáférés érdekében. Ha rögzíteni szeretné ezt az erőforráscsoportot, kattintson az irányítópult jobb felső sarkában található rögzítés ikonra.

### <a name="choosing-an-azure-region"></a>Azure-régió kiválasztása

Ha más régiót szeretne használni az oktatóanyaghoz, akkor ezek a tényezők korlátozhatják a lehetséges lehetőségeket:

* A közvetlen vonalas beszédfelismerési csatorna egy előzetes verziójú szolgáltatás. Ezért előfordulhat, hogy az adott Azure-régiókra korlátozódik. Az elérhető régiókkal kapcsolatos további információkért lásd: [hang – első virtuális asszisztens](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#voice-first-virtual-assistants).
* A közvetlen vonalas beszéd csatornája a szöveg-beszéd szolgáltatást használja, amely standard és neurális hangokat tartalmaz. A neurális hangok [bizonyos Azure-régiókra korlátozódnak](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices).
* Előfordulhat, hogy az ingyenes próbaverziós kulcsok egy adott régióra korlátozódnak.

A régiókkal kapcsolatos további információkért lásd: [Azure](https://azure.microsoft.com/global-infrastructure/locations/)-beli telephelyek.

## <a name="create-resources"></a>Erőforrások létrehozása

Most, hogy már van egy erőforráscsoport az **USA nyugati** régiójában, a következő lépés az oktatóanyagban használni kívánt egyes szolgáltatások egyedi erőforrásainak létrehozása.

### <a name="create-a-speech-services-resource"></a>Speech Services-erőforrás létrehozása

A beszédfelismerési erőforrások létrehozásához kövesse az alábbi utasításokat:

1. Válassza az **erőforrás létrehozása** lehetőséget a bal oldali navigációs sávon.
2. A keresősáv mezőbe írja be a **Speech**kifejezést.
3. Válassza a **beszéd**lehetőséget, majd kattintson a **Létrehozás**gombra.
4. A rendszer a következő információk megadását kéri:
   * Adjon **nevet**az erőforrásnak. Javasoljuk, hogy **SpeechEchoBotTutorial**
   * **Előfizetés**esetén győződjön meg arról, hogy az **ingyenes próbaverzió** van kiválasztva.
   * A **hely**mezőben válassza az **USA nyugati**régiója lehetőséget.
   * A **díjszabási**szinten válassza a **F0**lehetőséget. Ez az ingyenes szintet.
   * Az **erőforráscsoport**területen válassza a **SpeechEchoBotTutorial-ResourceGroup**elemet.
5. Miután megadta az összes szükséges információt, kattintson a **Létrehozás**gombra. Az erőforrás létrehozása néhány percet is igénybe vehet.
6. Az oktatóanyag későbbi részében szüksége lesz a szolgáltatáshoz tartozó előfizetési kulcsokra. Ezeket a kulcsokat bármikor elérheti az erőforrás- **Áttekintés** (kulcsok kezelése) vagy a **kulcsok**használatával.

Ezen a ponton győződjön meg arról, hogy az erőforráscsoport (**SpeechEchoBotTutorial-ResourceGroup**) rendelkezik beszédfelismerési erőforrással:

| NÉV | TÍPUS  | HELY |
|------|-------|----------|
| SpeechEchoBotTutorial – beszéd | Cognitive Services | USA nyugati régiója |

### <a name="create-an-azure-app-service-plan"></a>Azure App Service-csomag létrehozása

A következő lépés egy App Service terv létrehozása. Az App Service-csomagok határozzák meg a futtatni kívánt webalkalmazások számítási erőforrásait.

1. Válassza az **erőforrás létrehozása** lehetőséget a bal oldali navigációs sávon.
2. A keresősáv mezőbe írja be **app Service tervet**. Ezután keresse meg és válassza ki a **app Service** megtervezése kártyát a keresési eredmények közül.
3. Kattintson a **Create** (Létrehozás) gombra.
4. A rendszer a következő információk megadását kéri:
   * **Előfizetés** beállítása az **ingyenes próbaverzióra** (meglévő előfizetést is használhat).
   * Az **erőforráscsoport**területen válassza a **SpeechEchoBotTutorial-ResourceGroup**elemet.
   * Adjon **nevet**az erőforrásnak. Javasoljuk, hogy **SpeechEchoBotTutorial-AppServicePlan**
   * **Operációs rendszer**esetén válassza a **Windows**lehetőséget.
   * A **régió**területen válassza az **USA nyugati**régiója lehetőséget.
   * Az **árképzési szint**esetében ellenőrizze, hogy a **standard S1** van-e kiválasztva. Ennek az alapértelmezett értéknek kell lennie.
5. Kattintson **a felülvizsgálat és létrehozás**gombra. Ekkor meg kell jelennie egy olyan szalagcímnek, amely beolvasta az **érvényesítést**.
6. Kattintson a **Create** (Létrehozás) gombra. Az erőforráscsoport létrehozása eltarthat néhány percig.

Ezen a ponton győződjön meg arról, hogy az erőforráscsoport (**SpeechEchoBotTutorial-ResourceGroup**) két erőforrással rendelkezik:

| NÉV | TÍPUS  | HELY |
|------|-------|----------|
| SpeechEchoBotTutorial-AppServicePlan | App Service-csomag | USA nyugati régiója |
| SpeechEchoBotTutorial – beszéd | Cognitive Services | USA nyugati régiója |

## <a name="build-an-echo-bot"></a>Visszhangos robot létrehozása

Most, hogy létrehozott néhány erőforrást, hozzon létre egy robotot. Kezdjük az ECHO bot-mintával, amelynek a neve azt jelenti, hogy a válaszként beírt szöveget is megismétli. Ne aggódjon, a mintakód készen áll arra, hogy módosítás nélkül használhassa. Úgy van konfigurálva, hogy működjön együtt a közvetlen vonalas hangcsatornával, amelyet a robot Azure-ba való üzembe helyezése után fogunk csatlakozni.

> [!NOTE]
> Az alábbi utasítások, valamint az ECHO robottal kapcsolatos további információk a [minta readme on githubon](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/directline-speech/csharp_dotnetcore/02.echo-bot/README.md)érhetők el.

### <a name="download-and-run-the-sample"></a>A minta letöltése és futtatása

1. A minták tárházának klónozása:
   ```bash
   git clone https://github.com/Microsoft/botbuilder-samples.git
   ```
2. Indítsa el a Visual studiót.
3. Az eszköztáron válassza a **fájl** > **nyitott** > **projekt/megoldás**lehetőséget, majd nyissa meg a Direct line Speech Channel szolgáltatáshoz konfigurált echo-robot projektfájlt:
   ```
   experimental\directline-speech\csharp_dotnetcore\02.echo-bot\EchoBot.csproj
   ```
4. A projekt betöltését követően kattintson `F5` a projekt futtatásához.

### <a name="test-with-the-bot-framework-emulator"></a>Tesztelés a bot Framework emulátorral

A [bot Framework Emulator](https://github.com/microsoft/botframework-emulator) egy asztali alkalmazás, amely lehetővé teszi a robot-fejlesztők számára, hogy helyileg vagy távolról, egy alagúton keresztül tesztelje és hibakeresést végezzenek a robotok számára. Kövesse az alábbi lépéseket a robot-keretrendszer emulátorának használatához az ECHO-robot teszteléséhez.

1. A [bot Framework Emulator](https://github.com/Microsoft/BotFramework-Emulator/releases/latest) 4.3.0 vagy újabb verziójának telepítése
2. Indítsa el a robot Framework emulátort, és nyissa meg a robotját:
   * **A fájl** -> **megnyitása bot**.
3. Adja meg a robot URL-címét. Példa:
   ```
   http://localhost:3978/api/messages
   ```
4. A felhasználói felület használatával kommunikálhat a robottal gépelt szöveggel. Erősítse meg, hogy kap egy választ.


## <a name="deploy-your-bot-to-an-azure-app-service"></a>A robot üzembe helyezése egy Azure App Service

A következő lépés az ECHO-robot üzembe helyezése az Azure-ban. A robot üzembe helyezésének néhány módja van, de ebben az oktatóanyagban a Visual studióból való közzétételre fogunk összpontosítani.

> [!NOTE]
> Azt is megteheti, hogy az [Azure CLI](https://docs.microsoft.com/azure/bot-service/bot-builder-deploy-az-cli) és a [telepítési sablonok](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/directline-speech/csharp_dotnetcore/02.echo-bot/DeploymentTemplates)használatával is üzembe helyez egy robotot.

1. A Visual studióból nyissa meg a Direct line Speech Channel használatára konfigurált echo-robotot:
   ```
   experimental\directline-speech\csharp_dotnetcore\02.echo-bot\EchoBot.csproj
   ```
2. A **megoldáskezelő**kattintson a jobb gombbal a **EchoBot** -megoldásra, és válassza a **Közzététel...** lehetőséget.
3. Ekkor megnyílik egy új ablak, amelyben **a közzétételi cél kiválasztása** felirat látható.
3. Válassza ki **app Service** a bal oldali navigációs sávon, válassza az **új létrehozása**, majd a **Közzététel**lehetőséget.
5. Amikor megjelenik a **Create app Service (létrehozás** ) ablak:
   * Kattintson a **fiók hozzáadása**lehetőségre, és jelentkezzen be az Azure-fiókja hitelesítő adataival. Ha már bejelentkezett, válassza ki a kívánt fiókot a legördülő listából.
   * Az **alkalmazás neveként**meg kell adnia a robot globálisan egyedi nevét. A rendszer ezt a nevet használja egy egyedi robot URL-cím létrehozásához. Az alapértelmezett érték a dátummal és az idővel együtt lesz kitöltve (például: "EchoBot20190805125647"). Használhatja az oktatóanyag alapértelmezett nevét.
   * Az **előfizetés**beállításnál állítsa az **ingyenes próbaverzióra**
   * **Erőforráscsoport**esetében válassza az **SpeechEchoBotTutorial-ResourceGroup** elemet.
   * Az **üzemeltetési csomag**esetében válassza az **SpeechEchoBotTutorial-AppServicePlan**
6. Kattintson a **Létrehozás** gombra.
7. A Visual Studióban a következőhöz hasonló üzenetnek kell megjelennie:
   ```
   Publish Succeeded.
   Web App was published successfully https://EchoBot20190805125647.azurewebsites.net/
   ```
8. Az alapértelmezett böngészőnek meg kell nyitnia és meg kell jelennie a következőket olvasó oldalnak: "A robot készen áll!"
9. Ezen a ponton ellenőrizze, hogy az erőforráscsoport **SpeechEchoBotTutorial-ResourceGroup szerepel-** e a Azure Portalban, és ellenőrizze, hogy vannak-e három erőforrás:

| NÉV | TÍPUS  | HELY |
|------|-------|----------|
| EchoBot20190805125647 | App Service | USA nyugati régiója |
| SpeechEchoBotTutorial-AppServicePlan | App Service-csomag | USA nyugati régiója |
| SpeechEchoBotTutorial – beszéd | Cognitive Services | USA nyugati régiója |

## <a name="enable-web-sockets"></a>Webes szoftvercsatornák engedélyezése

Egy kis méretű konfigurációs módosítást kell végeznie, hogy a robot a webes szoftvercsatornák használatával kommunikáljon a közvetlen vonalas beszéd csatornával. A websocketek engedélyezéséhez kövesse az alábbi lépéseket:

1. Navigáljon a [Azure Portal](https://portal.azure.com), és keresse meg a app Service. Az erőforrásnak a **EchoBot20190805125647** (egyedi alkalmazásnév) hasonló névvel kell rendelkeznie.
2. A bal oldali navigációs sávon válassza a **Beállítások**, majd a **konfiguráció**elemet.
3. Válassza az **általános beállítások** lapot.
4. Keresse meg a **webes szoftvercsatornák** váltógomb helyét, és állítsabe a következőre:.
5. Kattintson a **Save** (Mentés) gombra.

> [!TIP]
> A szolgáltatás leállításához vagy újraindításához használhatja a Azure App Service lap tetején látható vezérlőket. Ez a hibaelhárítás során hasznos lehet.

## <a name="create-a-channel-registration"></a>Csatorna regisztrációjának létrehozása

Most, hogy létrehozott egy Azure App Service a robot üzemeltetéséhez, a következő lépés egy **robot-csatornák regisztrációjának**létrehozása. A csatorna regisztrálásának előfeltétele, hogy regisztrálja a robotot a bot-Framework csatornákon, beleértve a közvetlen vonalas hangcsatornát.

> [!NOTE]
> Ha többet szeretne megtudni arról, hogy a robotok hogyan használják ki a csatornákat, tekintse meg [a bot](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels?view=azure-bot-service-4.0)összekapcsolása csatornákkal című témakört.

1. Első lépésként létre kell hoznia egy új erőforrást a regisztrációhoz. A [Azure Portal](https://portal.azure.com)kattintson az **erőforrás létrehozása**elemre.
2. A keresési sáv típusának **robotja**az eredmények megjelenése után válassza a **robot-csatornák regisztrálása**lehetőséget.
3. Kattintson a **Create** (Létrehozás) gombra.
4. A rendszer a következő információk megadását kéri:
   * A **bot neve**mezőbe írja be a következőt: **SpeechEchoBotTutorial-BotRegistration**.
   * Az **előfizetés**mezőben válassza az **ingyenes próbaverzió**lehetőséget.
   * Az **erőforráscsoport**területen válassza a **SpeechEchoBotTutorial-ResourceGroup**elemet.
   * A **hely**mezőben válassza az **USA nyugati**régiója lehetőséget.
     * A **díjszabási**szinten válassza a **F0**lehetőséget.
     * Az **üzenetkezelési végpontnál**adja meg a webalkalmazás URL-címét `/api/messages` a végén található elérési úttal. Például: Ha a globálisan egyedi alkalmazás neve **EchoBot20190805125647**volt, az üzenetkezelési végpont a következő `https://EchoBot20190805125647.azurewebsites.net/api/messages/`lesz:.
     * Az **Application**ininsights esetében ezt kikapcsolhatja. További információ: [bot Analytics](https://docs.microsoft.com/azure/bot-service/bot-service-manage-analytics?view=azure-bot-service-4.0).
     * **Az alkalmazás azonosítójának és jelszavának automatikus létrehozása**figyelmen kívül hagyva.
5. Váltson vissza a **bot channels** -regisztrációra, és kattintson a **Létrehozás**gombra.

Ekkor a Azure Portalban keresse meg az **SpeechEchoBotTutorial-ResourceGroup** erőforráscsoportot. Ekkor négy erőforrást kell megjelenítenie:

| NÉV | TÍPUS  | HELY |
|------|-------|----------|
| EchoBot20190805125647 | App Service | USA nyugati régiója |
| SpeechEchoBotTutorial-AppServicePlan | App Service-csomag | USA nyugati régiója |
| SpeechEchoBotTutorial-BotRegistration | Robot-csatornák regisztrálása | Globális |
| SpeechEchoBotTutorial – beszéd | Cognitive Services | USA nyugati régiója |

> [!IMPORTANT]
> A bot channels regisztrációs erőforrása akkor is megjelenik a globális régióban, ha az USA nyugati régióját választotta. Ez a várható eredmény.

## <a name="register-the-direct-line-speech-channel"></a>A közvetlen vonalas beszéd csatorna regisztrálása

Itt az ideje, hogy regisztrálja a robotot a közvetlen vonalas beszéd csatornával. Ez a csatorna az ECHO-robot és a Speech SDK használatával összeállított ügyfélalkalmazás közötti kapcsolat létrehozásához használatos.

1. Keresse meg és nyissa meg a **SpeechEchoBotTutorial-BotRegistration** erőforrást a [Azure Portal](https://portal.azure.com).
2. A bal oldali navigációs sávon válassza a **csatornák**lehetőséget.
   * Keresse meg a **további csatornákat**, keresse meg, majd kattintson a **közvetlen vonalas beszéd**elemre.
   * Tekintse át a **közvetlen vonalas beszéd beállítása (előzetes verzió)** című oldalon található szöveget, majd kattintson a **Mentés**gombra.
   * A létrehozás részeként két **titkos kulcsot** generáltak. Ezek a kulcsok egyediek a roboton. Ha a [SPEECH SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/)használatával ír egy ügyfélalkalmazás alkalmazást, a kulcsok egyikét fogja biztosítani az ügyfélalkalmazás, a közvetlen vonalas csatorna és a bot szolgáltatás közötti kapcsolat létrehozásához. Ebben az oktatóanyagban a Direct line Speech-ügyfelet (WPF, C#) fogja használni.
   * Kattintson a **show (megjelenítés** ) gombra, és másolja ki az egyik kulcsot, hogy könnyedén hozzáférhessen. Ne aggódjon, bármikor elérheti a kulcsokat a Azure Portal.
3. A bal oldali navigációs sávon kattintson a **Beállítások**elemre.
   * Jelölje be a **streaming Endpoint engedélyezése**feliratú jelölőnégyzetet. Erre azért van szükség, hogy egy olyan kommunikációs protokollt engedélyezzen, amely a robot és a közvetlen vonalas beszédfelismerési csatorna közötti webes szoftvercsatornára épül.
   * Kattintson a **Save** (Mentés) gombra.

> [!TIP]
> Ha további információra van szüksége, tekintse meg a bot összekapcsolását ismertető témakört [(előzetes verzió)](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech?view=azure-bot-service-4.0). Ez az oldal további információkat és ismert problémákat tartalmaz.

## <a name="build-the-direct-line-speech-client"></a>A közvetlen vonalas beszédfelismerési ügyfél létrehozása

Ebben a lépésben létrehozza a Direct line Speech-ügyfelet. Az ügyfél egy Windows megjelenítési alaprendszer- C# (WPF-) alkalmazás, amely a [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk) használatával kezeli a robottal való kommunikációt a közvetlen vonalas hangcsatornán keresztül. Használhatja a robot használatát és tesztelését az egyéni ügyfélalkalmazás írása előtt.

A Direct line Speech-ügyfél egy egyszerű felhasználói felülettel rendelkezik, amely lehetővé teszi a robothoz való kapcsolódást, a szöveges beszélgetés megtekintését, a robot-keretrendszer tevékenységeinek JSON formátumban történő megtekintését, valamint az adaptív kártyák megjelenítését. Emellett támogatja az egyéni ébresztési szavak használatát is. Ezt az ügyfelet fogja használni a robottal való kommunikációhoz és a Hangválaszok fogadásához.

Mielőtt továbblép, győződjön meg arról, hogy a mikrofon és a hangszórók engedélyezve vannak és működnek.

1. Navigáljon a [közvetlen vonalas Speech-ügyfél](https://github.com/Azure-Samples/Cognitive-Services-Direct-Line-Speech-Client/blob/master/README.md)GitHub-tárházához.
2. Kövesse a tárház klónozásához, a projekt létrehozásához, az ügyfél konfigurálásához és az ügyfél elindításához megadott utasításokat.
3. Kattintson az újrakapcsolódás lehetőségre, és győződjön meg róla, hogy a mikrofon gomb megnyomásával megnyomja az üzenetet **, vagy írja be, hogy beszéljen**a robotra.
4. Tesztelje. Kattintson a mikrofon gombra, és beszéljen néhány szót angolul. A felismert szöveg a következőképpen jelenik meg:. Ha elkészült, a robot a saját hangján válaszol, amely az "Echo" szót követi, amelyet a felismert szavak követnek.
5. Szöveget is használhat a robottal való kommunikációhoz. Csak írja be a szöveget az alsó sávban. 

### <a name="troubleshooting-errors-in-direct-line-speech-client"></a>Hibák elhárítása a Direct line Speech-ügyfélben

Ha hibaüzenetet kap a fő alkalmazás ablakában, a következő táblázat segítségével azonosíthatja és elháríthatja a hibát:

| Hiba | Mi a teendő? |
|-------|----------------------|
|Alkalmazáshiba (a részletek a naplóban találhatók): Microsoft. CognitiveServices. Speech. csharp: Az érték nem lehet null. Paraméter neve: speechConfig | Ez egy ügyfélalkalmazás-hiba. Győződjön meg arról, hogy a fő alkalmazási ablakban nem üres a *bot Secret* értéke (lásd: [a robot regisztrálása közvetlen vonalas](#register-the-direct-line-speech-channel)hangcsatornával) |
|AuthenticationFailure hiba: A WebSocket frissítése hitelesítési hiba miatt meghiúsult (401). A megfelelő előfizetési kulcs (vagy engedélyezési jogkivonat) és a régió nevének keresése| Az alkalmazás beállítások lapján ellenőrizze, hogy helyesen adta-e meg a beszédfelismerési előfizetési kulcsot és annak régióját.<br>Ellenőrizze, hogy helyesen adta-e meg a robot titkát. |
|ConnectionFailure hiba: A távoli gazdagép lezárta a kapcsolatokat. Hibakód: 1011. Hiba részletei: Az üzenet elküldése előtt nem lehet csatlakozni a robothoz | Győződjön meg arról, hogy bejelölte [az "adatfolyam-végpont engedélyezése"](#register-the-direct-line-speech-channel) és/vagy a [ **webes szoftvercsatornák** ](#enable-web-sockets) bekapcsolva beállítást.<br>Győződjön meg arról, hogy a Azure App Service fut. Ha igen, próbálja meg újraindítani a App Service.|
|ConnectionFailure hiba: A távoli gazdagép lezárta a kapcsolatokat. Hibakód: 1011. Hiba részletei: A válasz állapotkód nem a sikerre utal: 500 (InternalServerError)| A robot a kimeneti tevékenység beszéd mezőjében egy neurális [](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak) hangot adott meg, de az előfizetési kulcshoz társított Azure-régió nem támogatja a neurális hangokat. Lásd: [standard és neurális hangok](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices).|
|ConnectionFailure hiba: A távoli gazdagép lezárta a kapcsolatokat. Hibakód: 1000. Hiba részletei: Túllépte a webes szoftvercsatorna kapcsolatának üresjárati időtartamát (> 300000 MS)| Ez egy várt hiba, ha a csatornához való csatlakozás öt percnél hosszabb ideig nyitott és inaktív. |

Ha a probléma nem szerepel a táblázatban, tekintse [meg a következő témakört: hang-első virtuális asszisztensek előzetes verzió: Gyakran ismételt kérdések](https://docs.microsoft.com/azure/cognitive-services/speech-service/faq-voice-first-virtual-assistants).

### <a name="view-bot-activities"></a>Bot-tevékenységek megtekintése

Minden robot küldi és fogadja a **tevékenységek** üzeneteit. A Direct line Speech Client **tevékenység napló** ablakában az időbélyeggel ellátott naplókat láthatja az ügyfél által a robottól kapott tevékenységekről. Azt is megtekintheti, hogy az ügyfél milyen tevékenységeket küldhet a robotnak [`DialogServiceConnector.SendActivityAsync`](https://docs.microsoft.com/en-us/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.sendactivityasync) a metódus használatával. Amikor kijelöl egy naplóbejegyzést, a rendszer a társított tevékenység részleteit JSON-ként jeleníti meg.

Íme egy példa egy, az ügyfél által fogadott tevékenység JSON-fájlja:
```json
{
    "attachments":[],
    "channelData":{
        "conversationalAiData":{
             "requestInfo":{
                 "interactionId":"8d5cb416-73c3-476b-95fd-9358cbfaebfa",
                 "version":"0.2"
             }
         }
    },
    "channelId":"directlinespeech",
    "conversation":{
        "id":"129ebffe-772b-47f0-9812-7c5bfd4aca79",
        "isGroup":false
    },
    "entities":[],
    "from":{
        "id":"SpeechEchoBotTutorial-BotRegistration"
    },
    "id":"89841b4d-46ce-42de-9960-4fe4070c70cc",
    "inputHint":"acceptingInput",
    "recipient":{
        "id":"129ebffe-772b-47f0-9812-7c5bfd4aca79|0000"
    },
    "replyToId":"67c823b4-4c7a-4828-9d6e-0b84fd052869",
    "serviceUrl":"urn:botframework:websocket:directlinespeech",
    "speak":"<speak version='1.0' xmlns='https://www.w3.org/2001/10/synthesis' xml:lang='en-US'><voice name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>Echo: Hello and welcome.</voice></speak>",
    "text":"Echo: Hello and welcome.",
    "timestamp":"2019-07-19T20:03:51.1939097Z",
    "type":"message"
}
```

Ha többet szeretne megtudni a JSON-kimenetben visszaadott információkról, tekintse meg [a tevékenységek mezőit](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md). Ebben az oktatóanyagban a [szöveg](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#text) és a [beszéd](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak) mezőkre koncentrálhat.

### <a name="view-client-source-code-for-calls-to-the-speech-sdk"></a>Az ügyfél forráskódjának megtekintése a Speech SDK-hívásokhoz

A közvetlen vonalas beszéd ügyfélprogram a [Microsoft. CognitiveServices. Speech](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/)NuGet-csomagot használja, amely a Speech SDK-t tartalmazza. A mintakód áttekintésének kiindulópontja a InitSpeechConnector () metódusa [`DLSpeechClient\MainWindow.xaml.cs`](https://github.com/Azure-Samples/Cognitive-Services-Direct-Line-Speech-Client/blob/master/DLSpeechClient/MainWindow.xaml.cs), amely a következő két Speech SDK-objektumot hozza létre:
- [`DialogServiceConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconfig)– A konfigurációs beállításokhoz (beszéd előfizetési kulcs, fő régió, bot Secret)
- [`DialogServiceConnector`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.-ctor)– A Channel-kapcsolatok és az ügyfél-előfizetési események kezelése a felismert beszéd-és bot-válaszok kezeléséhez.

## <a name="add-custom-wake-word-activation"></a>Egyéni ébresztési szó aktiválásának hozzáadása

A Speech SDK támogatja az egyéni ébresztési szavak aktiválását. A Microsoft Segédhez hasonlóan a "Hey Cortana" is megírhat egy alkalmazást, amely folyamatosan figyeli a választott ébresztési szót. Ne feledje, hogy egy ébresztési szó lehet egy szó vagy egy többszavas kifejezés.

> [!NOTE]
> A Felébresztési *munka* kifejezés gyakran használatos a kulcsszóval, és a Microsoft dokumentációjában is látható.

Az ébresztési szó észlelése az ügyfélalkalmazás esetében történik. Ha ébresztési szót használ, a rendszer csak akkor továbbítja a hanganyagot a közvetlen vonalas csatornára, ha az ébresztési szót észleli. A közvetlen vonalas hangcsatorna tartalmaz egy *Key Word-ellenőrzés (KWV)* nevű összetevőt, amely összetettebb feldolgozást végez a felhőben annak ellenőrzéséhez, hogy a kiválasztott ébresztési szó az audio stream elején van-e. Ha a Key Word ellenőrzése sikeres, akkor a csatorna kommunikálni fog a robottal.

Kövesse az alábbi lépéseket egy ébresztési Word-modell létrehozásához, konfigurálja a Direct line Speech-ügyfelet a modell használatára, és végül tesztelje a robot használatával.

1. Kövesse ezeket az utasításokat [egy egyéni Felébresztési szó létrehozásához a Speech Service használatával](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws).
2. Bontsa ki az előző lépésben letöltött modell fájlt. Az ébresztési szó neve legyen. Egy nevű `kws.table`fájlt keres.
3. A közvetlen vonalas beszédfelismerési ügyfélben keresse meg a **Beállítások** menüt (a jobb felső sarokban található fogaskerék ikont keresse meg). Keresse meg a **modell fájljának elérési útját** , és `kws.table` adja meg a fájl teljes elérési útját a 2. lépésben.
4. Győződjön meg arról, hogy az engedélyezve feliratú jelölőnégyzet be **van**jelölve. Ez az üzenet a jelölőnégyzet mellett jelenik meg: "A következő csatlakozáskor az ébresztési szót fogja figyelni." Ha nem megfelelő fájlt vagy érvénytelen elérési utat adott meg, hibaüzenet jelenik meg.
5. Adja meg a Speech **előfizetés kulcsát**, az előfizetési **kulcs régióját**, majd kattintson az **OK** gombra a **Beállítások** menü bezárásához.
6. Válasszon egy **robot**-titkot,majd kattintson az újrakapcsolás gombra. A következő üzenetnek kell megjelennie: "Új beszélgetés kezdődött – típus, nyomja meg a mikrofon gombot, vagy mondja a Wake szót". Az alkalmazás mostantól folyamatosan figyel.
7. Beszéljen minden olyan kifejezésről, amely az ébresztési szavával kezdődik. Például: " **{Your Wake Word}** , mi az idő?". Az ébresztési szó kiírása után nem kell szüneteltetni. Ha elkészült, két dolog történik:
   * Megtekintheti a küllőt
   * Röviddel azután, hogy meghallja a robot válaszát
8. Folytassa a kísérletet a robot által támogatott három bemeneti típussal:
   * Begépelt szöveg az alsó sávban
   * A mikrofon ikon és a beszéd megnyomása
   * Egy kifejezés, amely az ébresztési szóhoz indul

### <a name="view-the-source-code-that-enables-wake-word"></a>Az ébresztési szót engedélyező forráskód megtekintése

A közvetlen vonalas ügyfél forráskódjában tekintse meg ezeket a fájlokat, és tekintse át az ébresztési szó észlelésének engedélyezéséhez használt kódot:

1. [`DLSpeechClient\Models.cs`](https://github.com/Azure-Samples/Cognitive-Services-Direct-Line-Speech-Client/blob/master/DLSpeechClient/Models.cs)a beszédfelismerési SDK metódusának [`KeywordRecognitionModel.fromFile()`](https://docs.microsoft.com/en-us/javascript/api/microsoft-cognitiveservices-speech-sdk/keywordrecognitionmodel?view=azure-node-latest#fromfile-string-)meghívása, amely a modell helyi fájlból való létrehozásához használatos a lemezen.
1. [`DLSpeechClient\MainWindow.xaml.cs`](https://github.com/Azure-Samples/Cognitive-Services-Direct-Line-Speech-Client/blob/master/DLSpeechClient/MainWindow.xaml.cs)a beszédfelismerési SDK metódusának [`DialogServiceConnector.StartKeywordRecognitionAsync()`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.startkeywordrecognitionasync)meghívása, amely aktiválja a folyamatos ébresztési szó észlelését.

## <a name="optional-change-the-language-and-redeploy-your-bot"></a>Választható A nyelv módosítása és a robot újbóli üzembe helyezése

Az Ön által létrehozott robot az angol nyelven fogja figyelni és válaszolni. Azonban nem csak az angol nyelvet használja. Ebből a szakaszból megtudhatja, hogyan változtathatja meg a robot által megfigyelt és válaszoló nyelvet, majd újból üzembe helyezheti a robotot.

### <a name="change-the-language"></a>A nyelv módosítása

1. Kezdjük a megnyitásával `\experimental\directline-speech\csharp_dotnetcore\02.echo-bot\Bots\echo-bot.cs`.
2. Ezután keresse meg a SSML. Könnyen megtalálható, mert címkével van elfoglalva `<speak></speak>` .
3. A SSML karakterláncban keresse meg a `<voice name>` címkét, cserélje le `<voice name='de-DE-Stefan-Apollo'>`a kifejezésre, és mentse. Ez a formázott sztring közli, hogy a szöveg – beszéd szolgáltatás egy szintetizált beszéd választ ad vissza a hang `de-DE-Stefan-Apollo`használatával, amely a német nyelvre van optimalizálva.

>[!NOTE]
> Nem korlátozódik a németre, és a beszédfelismerési szolgáltatásból kiválaszthatja a rendelkezésre [](language-support.md#text-to-speech)álló hangok listáját is.

### <a name="redeploy-your-bot"></a>A robot újbóli üzembe helyezése

Most, hogy végrehajtotta a szükséges módosításokat a roboton, a következő lépés az újbóli közzététel a Azure App Serviceon, és próbálja ki:

1. Hozzon létre egy megoldást a Visual Studióban, és javítsa ki az esetleges fordítási hibákat.
2. A Megoldáskezelő ablakban kattintson a jobb gombbal a **EchoBot** projektre, és válassza a **Közzététel**lehetőséget.
3. A korábbi telepítési konfiguráció már be van töltve alapértelmezettként. Egyszerűen kattintson a **Közzététel** elemre a **EchoBot20190805125647-web Deploy**lehetőség mellett.
4. A **sikeres közzététel** üzenet megjelenik a Visual Studio kimeneti ablakában, és a "a robot készen áll!" üzenet jelenik meg.
5. Nyissa meg a közvetlen vonalas beszédfelismerési ügyfélprogramot, kattintson a beállítások gombra (jobb felső szintű fogaskerék ikon), `de-de` és írja be a nyelvet a Language (nyelv) mezőbe. Ezzel a beállítással megadhatja a beszélt nyelvet, felülbírálva `en-us`az alapértelmezett értéket.
6. Kövesse a [Direct line Speech-ügyfél felépítése](#build-the-direct-line-speech-client) az újonnan telepített robottal való újrakapcsolódáshoz című témakör utasításait, és beszéljen az új nyelven, és hallgassa meg a robot válaszát az új hanggal.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja a jelen oktatóanyagban üzembe helyezett echo-bot használatát, akkor a **SpeechEchoBotTutorial-ResourceGroup Azure-** erőforráscsoport törlésével eltávolíthatja azt és az összes hozzá tartozó Azure-erőforrást.

1. A [Azure Portal](https://portal.azure.com)kattintson az **erőforráscsoportok** elemre a bal oldali navigációs sávon.
2. Keresse meg a nevű erőforráscsoportot: **SpeechEchoBotTutorial – ResourceGroup**. Kattintson a három pontra (...).
3. Válassza az **Erőforráscsoport törlése** elemet.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Saját ügyfélalkalmazás létrehozása a Speech SDK-val](quickstart-virtual-assistant-csharp-uwp.md)

## <a name="see-also"></a>Lásd még

* Üzembe helyezés egy [közel található Azure-régióban](https://azure.microsoft.com/global-infrastructure/locations/) a bot válaszideje fejlesztéséhez
* [Magas színvonalú NEURÁLIS TTS-hangokat támogató Azure-régió](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices) üzembe helyezése
* A Direct line Speech Channel szolgáltatáshoz kapcsolódó díjszabás:
  * [A bot Service díjszabása](https://azure.microsoft.com/pricing/details/bot-service/)
  * [Speech Services](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)
* Saját hang-kompatibilis bot kiépítése és üzembe helyezése:
    * Hozzon létre egy [robot-keretrendszer robotot](https://dev.botframework.com/). Regisztrálja a [Direct line Speech Channel](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech?view=azure-bot-service-4.0) használatával, és [szabja testre a robotját](https://docs.microsoft.com/azure/bot-service/directline-speech-bot?view=azure-bot-service-4.0)
    * Ismerkedjen meg a meglévő [bot-Framework megoldásokkal](https://github.com/microsoft/botframework-solutions): Hozzon létre egy [egyéni hang-első virtuális asszisztenst](https://docs.microsoft.com/azure/cognitive-services/speech-service/voice-first-virtual-assistants) és hangvételt [– engedélyezze](https://github.com/microsoft/botframework-solutions/blob/master/docs/howto/assistant/csharp/speechenablement.md)
