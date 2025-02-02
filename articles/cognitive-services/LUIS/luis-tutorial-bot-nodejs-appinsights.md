---
title: Application Insights, Node. js-LUIS
titleSuffix: Azure Cognitive Services
description: Ez az oktatóanyag a robotokat és a Language Understanding információt adja hozzá Application Insights telemetria adattároláshoz.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 06/16/2019
ms.author: diberry
ms.openlocfilehash: 72ce681b6b0e4109151b987a5f8cc4bc050aafa0
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563290"
---
# <a name="add-luis-results-to-application-insights-from-a-bot-in-nodejs"></a>LUIS-eredmények hozzáadása az Application Insightshoz a Node. js-ből származó robotból
Ez az oktatóanyag a robotokat és a Language Understanding információt adja hozzá [Application Insights](https://azure.microsoft.com/services/application-insights/) telemetria adattároláshoz. Az ilyen adatokat lekérdezheti a Kusto nyelvével vagy Power BI a leképezések elemzéséhez, összesítéséhez és jelentésekhez, valamint a teljes körű, valós idejű entitások megjelenítéséhez. Az elemzés segít annak meghatározásában, ha kell hozzáadása vagy szerkesztése a szándékok és entitások, a LUIS-alkalmazás.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A robot és a Language Understanding-adatértelmezések rögzítése Application Insights
> * Language Understanding-adatApplication Insights lekérdezése

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure bot Service-robot, amely Application Insights engedélyezve lett létrehozva.
* Az előző robot-oktatóanyagból letöltött **[](luis-nodejs-tutorial-bf-v4.md)** robot-kód. 
* [Bot Emulator](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)

Az oktatóanyagban szereplő összes kód elérhető az [Azure-samples Language Understanding GitHub-tárházban](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-web-app-bot-application-insights/v4/luis-nodejs-bot-johnsmith-src-telemetry). 

## <a name="add-application-insights-to-web-app-bot-project"></a>Application Insights hozzáadása a webalkalmazás-robot projekthez
Jelenleg az Application Insights szolgáltatás a web app bot használt gyűjt a robot telemetriája általános állapotát. Nem gyűjti a LUIS-információkat. 

A LUIS-információk rögzítéséhez a webalkalmazás-robotnak telepítve és konfigurálva kell lennie a **[Application Insights](https://www.npmjs.com/package/applicationinsights)** NPM-csomagnak.  

1. A VSCode integrált terminálján, a robot projekt gyökerénél adja hozzá a következő NPM-csomagokat az alábbi parancs használatával: 

    ```console
    npm install applicationinsights && npm install underscore
    ```
    
    Az **aláhúzásjel** -csomag segítségével a Luis JSON-struktúra lelapulható, így könnyebben megtekinthető és használható a Application Insights.
    


## <a name="capture-and-send-luis-query-results-to-application-insights"></a>Rögzíti és továbbítja a LUIS lekérdezés eredményeit az Application Insights

1. A VSCode-ben hozzon létre egy új **appInsightsLog. js** fájlt, és adja hozzá a következő kódot:

    ```javascript
    const appInsights = require('applicationinsights');
    const _ = require("underscore");
    
    // Log LUIS results to Application Insights
    // must flatten as name/value pairs
    var appInsightsLog = (botContext,luisResponse) => {

        appInsights.setup(process.env.MicrosoftApplicationInsightsInstrumentationKey).start();
        const appInsightsClient = appInsights.defaultClient;

        // put bot context and LUIS results into single object
        var data = Object.assign({}, {'botContext': botContext._activity}, {'luisResponse': luisResponse});
    
        // Flatten data into name/value pairs
        flatten = (x, result, prefix) => {
            if(_.isObject(x)) {
                _.each(x, (v, k) => {
                    flatten(v, result, prefix ? prefix + '_' + k : k)
                })
            } else {
                result["LUIS_" + prefix] = x
            }
            return result;
        }
    
        // call fn to flatten data
        var flattenedData = flatten(data, {});
    
        // ApplicationInsights Trace 
        console.log(JSON.stringify(flattenedData));
    
        // send data to Application Insights
        appInsightsClient.trackTrace({message: "LUIS", severity: appInsights.Contracts.SeverityLevel.Information, properties: flattenedData});
    }
    
    module.exports.appInsightsLog = appInsightsLog;
    ```

    Ez a fájl a robot környezetét és a Luis-választ veszi fel, lelapul mindkét objektumot,  és beszúrja őket egy nyomkövetési eseménybe az Application ininsights szolgáltatásban. Az esemény neve: **Luis**. 

1. Nyissa  meg a párbeszédablakok mappát, majd a **luisHelper. js** fájlt. Adja hozzá az új **appInsightsLog. js** fájlt a szükséges fájlhoz, és rögzítse a bot kontextusát és a Luis-választ. A fájl teljes kódja: 

    ```javascript
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    const { LuisRecognizer } = require('botbuilder-ai');
    const { appInsightsLog } = require('../appInsightsLog');
    
    class LuisHelper {
        /**
         * Returns an object with preformatted LUIS results for the bot's dialogs to consume.
         * @param {*} logger
         * @param {TurnContext} context
         */
        static async executeLuisQuery(logger, context) {
            const bookingDetails = {};
    
            try {
                const recognizer = new LuisRecognizer({
                    applicationId: process.env.LuisAppId,
                    endpointKey: process.env.LuisAPIKey,
                    endpoint: `https://${ process.env.LuisAPIHostName }`
                }, {}, true);
    
                const recognizerResult = await recognizer.recognize(context);
    
                // APPINSIGHT: Log results to Application Insights
                appInsightsLog(context,recognizerResult);
    
    
                const intent = LuisRecognizer.topIntent(recognizerResult);
    
                bookingDetails.intent = intent;
    
                if (intent === 'Book_flight') {
                    // We need to get the result from the LUIS JSON which at every level returns an array
    
                    bookingDetails.destination = LuisHelper.parseCompositeEntity(recognizerResult, 'To', 'Airport');
                    bookingDetails.origin = LuisHelper.parseCompositeEntity(recognizerResult, 'From', 'Airport');
    
                    // This value will be a TIMEX. And we are only interested in a Date so grab the first result and drop the Time part.
                    // TIMEX is a format that represents DateTime expressions that include some ambiguity. e.g. missing a Year.
                    bookingDetails.travelDate = LuisHelper.parseDatetimeEntity(recognizerResult);
                }
            } catch (err) {
                logger.warn(`LUIS Exception: ${ err } Check your LUIS configuration`);
            }
            return bookingDetails;
        }
    
        static parseCompositeEntity(result, compositeName, entityName) {
            const compositeEntity = result.entities[compositeName];
            if (!compositeEntity || !compositeEntity[0]) return undefined;
    
            const entity = compositeEntity[0][entityName];
            if (!entity || !entity[0]) return undefined;
    
            const entityValue = entity[0][0];
            return entityValue;
        }
    
        static parseDatetimeEntity(result) {
            const datetimeEntity = result.entities['datetime'];
            if (!datetimeEntity || !datetimeEntity[0]) return undefined;
    
            const timex = datetimeEntity[0]['timex'];
            if (!timex || !timex[0]) return undefined;
    
            const datetime = timex[0].split('T')[0];
            return datetime;
        }
    }
    
    module.exports.LuisHelper = LuisHelper;
    ```

## <a name="add-application-insights-instrumentation-key"></a>Application Insights kialakítási kulcs hozzáadása 

Az Application-elemzésekhez való adathozzáadáshoz szükség van a kialakítási kulcsra.

1. A böngészőben a [Azure Portal](https://portal.azure.com)keresse meg a robot **Application Insights** erőforrását. A neve lesz a robot neve, majd a név végén található véletlenszerű karakterek, például `luis-nodejs-bot-johnsmithxqowom`:. 
1. A Application Insights erőforrás **Áttekintés** lapján másolja a kialakítási **kulcsot**.
1. A VSCode-ben nyissa meg a **. env** fájlt a robot-projekt gyökerében. Ez a fájl tartalmazza az összes környezeti változót.  
1. Adjon hozzá egy új változót `MicrosoftApplicationInsightsInstrumentationKey` a kialakítási kulcs értékével. Idézőjelek között ne helyezze el az értéket. 

## <a name="start-the-bot"></a>A robot indítása

1. A VSCode integrált terminálon indítsa el a robotot:
    
    ```console
    npm start
    ```

1. Indítsa el a robot-emulátort, és nyissa meg a robotot. Ez a [lépés](luis-nodejs-tutorial-bf-v4.md#use-the-bot-emulator-to-test-the-bot) az előző oktatóanyagban van megadva.

1. Kérdezze meg a robot kérdését. Ez a [lépés](luis-nodejs-tutorial-bf-v4.md#ask-bot-a-question-for-the-book-flight-intent) az előző oktatóanyagban van megadva.

## <a name="view-luis-entries-in-application-insights"></a>Nézet LUIS bejegyzések az Application insights szolgáltatásban

Nyissa meg az Application Insights a LUIS-bejegyzések megtekintéséhez. Néhány percet is igénybe vehet, hogy az adat megjelenjen a Application Insightsban.

1. A [Azure Portal](https://portal.azure.com)nyissa meg a robot Application Insights erőforrását. 
1. Amikor megnyílik az erőforrás, válassza a **Keresés** lehetőséget, és az utolsó **30 percben** keresse meg az összes adat kifejezést a **nyomkövetési**esemény típusával. Válassza ki a **Luis**nevű nyomkövetést. 
1. A bot és a LUIS információ az **Egyéni tulajdonságok**területen érhető el. 

    ![Az Application Insightsban tárolt LUIS egyéni tulajdonságok áttekintése](./media/luis-tutorial-appinsights/application-insights-luis-trace-custom-properties-nodejs.png)

## <a name="query-application-insights-for-intent-score-and-utterance"></a>Lekérdezés az Application Insights szándékot, pontszám és utterance (kifejezés)
Application Insights lehetővé teszi az adatlekérdezést a [Kusto](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview#what-language-do-log-queries-use) nyelvével, valamint a Power BIba való exportálással. [](https://powerbi.microsoft.com) 

1. Válassza a **napló (elemzés)** lehetőséget. Egy lekérdezési ablak tetején, és a egy tábla ablak, amely alatt megjelenik egy új ablak. Ha a használt adatbázisokat korábban, ezzel az elrendezéssel fokozott tisztában van-e. A lekérdezés az előző szűrt adatait jelöli. A **CustomDimensions** oszlop a bot és a Luis információval rendelkezik.
1. A felső szándék, a pontszám és a Kimondás lekéréséhez adja hozzá a következőt a lekérdezési ablak utolsó `|top...` sora (a sor) fölé:

    ```kusto
    | extend topIntent = tostring(customDimensions.LUIS_luisResponse_luisResult_topScoringIntent_intent)
    | extend score = todouble(customDimensions.LUIS_luisResponse_luisResult_topScoringIntent_score)
    | extend utterance = tostring(customDimensions.LUIS_luisResponse_text)
    ```

1. Futtassa a lekérdezést. Az új oszlopok topIntent, pontszám és utterance (kifejezés) érhetők el. Válassza ki a rendezni kívánt topIntent oszlopot.

Tudjon meg többet a [Kusto lekérdezési nyelvéről](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries) , vagy [exportálja az adatait Power BIba](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi). 

## <a name="next-steps"></a>További lépések

Egyéb információkat, érdemes hozzáadni az application insights-adatok tartalmazza Alkalmazásazonosító, a verzió azonosítója, a legutóbbi modell dátuma, a legutóbbi train dátuma, a legutóbbi közzététel dátuma. Ezek az értékek lekérhető a végpont URL-címéről (az alkalmazás azonosítója és a verziószáma), vagy egy szerzői API-hívásból, majd a webalkalmazás bot-beállításaiban, majd onnan kihúzva.  

Végpont ugyanahhoz az előfizetéshez egynél több LUIS alkalmazás használ, akkor is tartalmaznia kell az előfizetés-azonosító és a egy tulajdonság arról, hogy egy megosztott kulcsot. 

> [!div class="nextstepaction"]
> [További tudnivalók a példa kimondott szöveg](luis-how-to-add-example-utterances.md)
