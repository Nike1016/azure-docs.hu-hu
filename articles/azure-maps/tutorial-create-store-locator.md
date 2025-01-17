---
title: Tároló-lokátor létrehozása a Azure Maps használatával | Microsoft Docs
description: Hozzon létre egy áruházbeli lokátort Azure Maps használatával.
author: walsehgal
ms.author: v-musehg
ms.date: 11/15/2018
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 4cc21a4dbab7d5114eed8414c6530eab5f42bb00
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68478862"
---
# <a name="create-a-store-locator-by-using-azure-maps"></a>Tároló-lokátor létrehozása Azure Maps használatával

Ez az oktatóanyag végigvezeti egy egyszerű tároló-lokátor létrehozásának folyamatán Azure Maps használatával. Az áruházbeli lokátorok gyakoriak. Az ilyen típusú alkalmazásokban használt fogalmak számos más típusú alkalmazásra alkalmazhatók. Az ügyfeleknek nyújtott áruházi lokátort a legtöbb, közvetlenül a fogyasztóknak értékesítő vállalat számára ajánlott biztosítani. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
    
> [!div class="checklist"]
> * Hozzon létre egy új weblapot az Azure térképkezelés API használatával.
> * Egyéni adatok betöltése egy fájlból, és megjelenítése egy térképen.
> * A Azure Maps keresési szolgáltatással megkeresheti a címeket, vagy megadhat egy lekérdezést.
> * Szerezze be a felhasználó helyét a böngészőben, és jelenítse meg a térképen.
> * Több réteg egyesítésével egyéni szimbólumokat hozhat létre a térképen.  
> * Fürt adatpontjai.  
> * Nagyítási vezérlők hozzáadása a térképhez

<a id="Intro"></a>

Ugorjon az [élő áruház kereső példájának](https://azuremapscodesamples.azurewebsites.net/?sample=Simple%20Store%20Locator) vagy [forráskódjának](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator)a fölé. 

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag lépéseinek elvégzéséhez először létre kell hoznia [Azure Maps](./tutorial-search-location.md#createaccount) -fiókját, és [le kell kérnie a fiókjához tartozó előfizetési kulcsot](./tutorial-search-location.md#getkey).

## <a name="design"></a>Tervezés

A kód beugrása előtt érdemes megkezdeni a kialakítást. Az áruház lokátora lehet olyan egyszerű vagy összetett, amennyire csak szeretné. Ebben az oktatóanyagban egy egyszerű tároló-lokátort hozunk létre. Néhány tippet is tartalmaz, amely segítséget nyújt bizonyos funkciók kibővítéséhez, ha úgy dönt, hogy a lehetőséget választja. A contoso Coffee nevű kitalált vállalathoz hozzunk létre egy áruházbeli lokátort. Az alábbi ábrán az oktatóanyagban felépített áruházi lokátor általános elrendezését láthatja:

<br/>
<center>

![A contoso Coffee Coffee Shop helyeihez tartozó tároló-lokátor drótváza](./media/tutorial-create-store-locator/SimpleStoreLocatorWireframe.png)</center>

Az áruház kereső hasznosságának maximalizálása érdekében olyan rugalmas elrendezést is tartalmazunk, amely akkor módosítható, ha a felhasználó képernyő szélessége kisebb, mint 700 képpont. A rugalmas elrendezés megkönnyíti az áruházbeli lokátor használatát egy kis képernyőn, például egy mobileszközön. Íme egy kis képernyős elrendezés drótváza:  

<br/>
<center>

![A contoso Coffee Store-lokátor drótváza egy mobileszközön](./media/tutorial-create-store-locator/SimpleStoreLocatorMobileWireframe.png)</center>

A drótváz egy meglehetősen egyszerű alkalmazást mutat be. Az alkalmazás tartalmaz egy keresőmezőt, a közeli áruházak listáját, egy olyan térképet, amely tartalmaz néhány jelölőt (szimbólumot), valamint egy előugró ablakot, amely további információkat jelenít meg, amikor a felhasználó kiválaszt egy jelölőt. Részletesebben itt láthatók a következő oktatóanyagban az áruház-lokátorban felépített funkciók:

* Az importált tabulátorral tagolt adatfájlból származó összes hely betöltődik a térképen.
* A felhasználó megadhatja és nagyíthatja a térképet, keresést végezhet, és kiválaszthatja a saját hely GPS-gombját.
* A lapelrendezés az eszköz képernyőjének szélessége alapján állítható be.  
* A fejlécben az áruház emblémája látható.  
* A felhasználó keresési és keresési gomb használatával kereshet meg egy helyet, például egy cím, egy irányítószám vagy egy város. 
* Ha a felhasználó megnyomja az ENTER billentyűt, a keresőmezőbe felvett eseményelindítjaakeresést.`keypress` Ez a funkció gyakran megtekinthető, de jobb felhasználói élményt nyújt.
* A Térkép mozgatásakor a rendszer kiszámítja az egyes helyek távolságát a Térkép közepétől. A rendszer frissíti az eredmények listáját a Térkép tetején található legközelebbi hely megjelenítéséhez.  
* Ha kijelöl egy eredményt az eredmények listájában, a Térkép középpontba kerül a kijelölt helyen, és a helyhez tartozó információk egy előugró ablakban jelennek meg.  
* Egy adott hely kiválasztása a térképen is elindítja az előugró ablakot.
* Ha a felhasználó nagyítja, a helyszínek fürtökbe vannak csoportosítva. A fürtöket egy kör jelöli egy számmal a körben belül. A fürtök űrlapja és elkülönítése, amikor a felhasználó megváltoztatja a nagyítási szintet.
* Ha kijelöl egy fürtöt, a két szint és a középpontban, a fürt helyén nagyítja a térképet.

<a id="create a data-set"></a>

## <a name="create-the-store-location-dataset"></a>Az áruházbeli hely adatkészletének létrehozása

Az áruházbeli lokátor alkalmazás fejlesztése előtt létre kell hoznia egy adatkészletet a térképen megjelenítendő áruházakból. Ebben az oktatóanyagban egy contoso Coffee nevű fiktív Coffee Shop adatkészletét használjuk. Az egyszerű tároló-lokátor adatkészlete egy Excel-munkafüzetben van kezelve. Az adatkészlet 10 213 contoso Coffee Coffee Shop-helyet tartalmaz kilenc ország/régió között: a Egyesült Államok, Kanadában, az Egyesült Királyságban, Franciaországban, Németországban, Olaszországban, Hollandiában, Dániában és Spanyolországban. Itt látható egy képernyőkép arról, hogy az adatnézet milyen módon néz ki:

<br/>
<center>

![Képernyőfelvétel az áruházbeli lokátorról egy Excel-munkafüzetben](./media/tutorial-create-store-locator/StoreLocatorDataSpreadsheet.png)</center>

Letöltheti [az Excel](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/data)-munkafüzetet. 

Az alábbi észrevételeket követve megtekintheti az adatképernyőképet:
    
* A helyadatok tárolása a **AddressLine**, a **város**, az **önkormányzat** (megye), a **AdminDivision** (állam/tartomány), az **Irányítószám** (postai kód) és az **ország** oszlopai alapján történik.  
* A **szélesség** és **hosszúság** oszlop az egyes contoso Coffee Coffee Shop-helyek koordinátáit tartalmazza. Ha nem rendelkezik koordináta-információkkal, a Azure Maps keresési szolgáltatásaival meghatározhatja a hely koordinátáit.
* Néhány további oszlop a kávézókkal kapcsolatos metaadatokat tartalmaz: egy telefonszámot, logikai oszlopokat a Wi-Fi elérési ponthoz és a kerekesszékes hozzáférhetőséghez, valamint 24 órás formátumban tárolja a nyitó és záró időpontokat. Létrehozhat saját oszlopokat is, amelyek a tartózkodási hely adataihoz kapcsolódó metaadatokat tartalmaznak.

> [!Note]
> Azure Maps megjeleníti az adatokat a gömb Mercator-kivetítés "EPSG: 3857" elemben, de a WGS84 datumt használó "EPSG: 4325" adatokat olvas be. 

Számos módon teheti elérhetővé az adatkészletet az alkalmazás számára. Az egyik módszer az, ha az adatokat egy adatbázisba tölti be, és olyan webszolgáltatást tesz elérhetővé, amely lekérdezi az adatokat, és elküldi az eredményeket a felhasználó böngészőjének. Ez a lehetőség nagy adathalmazok vagy gyakran frissített adatkészletek esetén ideális. Ez a lehetőség azonban jelentősen nagyobb fejlesztési munkát igényel, és magasabb a díjszabása. 

Egy másik módszer az adatkészlet átalakítása egy egyszerű szövegfájlba, amelyet a böngésző könnyen elemez. Maga a fájl a többi alkalmazással is üzemeltethető. Ez a lehetőség egyszerűen megtartja a dolgokat, de ez a lehetőség csak kisebb adatkészletek esetében hasznos, mert a felhasználó letölti az összes adatát. Ehhez az adatkészlethez az egyszerű szövegfájlt használjuk, mert az adatfájl mérete kisebb, mint 1 MB.  

Ha a munkafüzetet egy sima szövegfájlba szeretné átalakítani, mentse a munkafüzetet tabulátorral tagolt fájlként. Az egyes oszlopokat tabulátor karakterekkel tagoljuk, így az oszlopok könnyen elemezhetők a kódban. Vesszővel tagolt (CSV) formátumot használhat, de ez a beállítás több elemzési logikát igényel. A körülötte található vesszőt tartalmazó mezők idézőjelekkel lesznek becsomagolva. Ha ezeket az adatfájlokat tabulátorral tagolt fájlként szeretné exportálni az Excelben, válassza a **Mentés másként**lehetőséget. A fájltípus **legördülő listából** válassza a **szöveg (tabulátorral tagolt) (*. txt)** lehetőséget. Nevezze el a *ContosoCoffee. txt*fájlt. 

<br/>
<center>

![Képernyőfelvétel a fájltípus párbeszédpanelről](./media/tutorial-create-store-locator/SaveStoreDataAsTab.png)</center>

Ha a Jegyzettömbben megnyitja a szövegfájlt, az a következő ábrához hasonlóan néz ki:

<br/>
<center>

![Képernyőfelvétel egy tabulátorral tagolt adatkészletet megjelenítő Jegyzettömb-fájlról](./media/tutorial-create-store-locator/StoreDataTabFile.png)</center>


## <a name="set-up-the-project"></a>A projekt beállítása

A projekt létrehozásához használhatja a [Visual studiót](https://visualstudio.microsoft.com) vagy az Ön által választott programkód-szerkesztőt. A Project mappában hozzon létre három fájlt: *index. html*, *index. css*és *index. js*. Ezek a fájlok határozzák meg az alkalmazás elrendezését, stílusát és logikáját. Hozzon létre egy adatmappa nevű mappát *, és adja* hozzá a *ContosoCoffee. txt fájlt* a mappához. Hozzon létre egy másik mappát a *képek*nevű mappában. Ebben az alkalmazásban tíz lemezképet használunk a térképen látható ikonokhoz, gombokhoz és jelölőhöz. [Ezeket a lemezképeket letöltheti](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/data). A projekt mappájának ekkor az alábbi ábrához hasonlóan kell kinéznie:

<br/>
<center>

![Az egyszerű tároló-lokátor projekt mappájának képernyőképe](./media/tutorial-create-store-locator/StoreLocatorVSProject.png)</center>

## <a name="create-the-user-interface"></a>Felhasználói felület létrehozása

A felhasználói felület létrehozásához vegyen fel egy kódot az *index. html fájlba*:

1. Adja hozzá a `meta` következő címkéket az `head` *index. html*fájlhoz. A címkék határozzák meg a karakterkészletet (UTF-8), az Internet Explorer és a Microsoft Edge segítségével a legújabb böngésző-verziókat használják, és olyan nézőpontot adjon meg, amely jól működik a rugalmas elrendezésekhez.

    ```HTML
    <meta charset="utf-8">
    <meta http-equiv="x-ua-compatible" content="IE=Edge">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    ```

1. Hivatkozások hozzáadása a Azure Maps webes vezérlő JavaScript-és CSS-fájlokhoz:

    ```HTML
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
    ```

1. Adjon hozzá egy hivatkozást a Azure Maps Services modulhoz. A modul egy JavaScript-kódtár, amely a Azure Maps REST-szolgáltatásokat csomagolja, és könnyen használható a JavaScriptben. A modul hasznos a keresési funkciók bekapcsolásához.

    ```HTML
    <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>
    ```

1. Adja hozzá a hivatkozásokat az *index. js* és az *index. css*fájlhoz:

    ```HTML
    <link rel="stylesheet" href="index.css" type="text/css">
    <script src="index.js"></script>
    ```

1. A dokumentum törzsében adjon hozzá egy `header` címkét. `header` A címkén belül adja hozzá az emblémát és a vállalat nevét.

    ```HTML
    <header>
        <img src="images/Logo.png" />
        <span>Contoso Coffee</span>
    </header>
    ```

1. Adjon hozzá `main` egy címkét, és hozzon létre egy szövegmezőt és egy keresési gombot tartalmazó keresési panelt. Továbbá adjon hozzá `div` hivatkozásokat a térképhez, a lista panelhez és a saját hely GPS gombhoz.

    ```HTML
    <main>
        <div class="searchPanel">
            <div>
                <input id="searchTbx" type="search" placeholder="Find a store" />
                <button id="searchBtn" title="Search"></button>
            </div>
        </div>
        <div id="listPanel"></div>
        <div id="myMap"></div>
        <button id="myLocationBtn" title="My Location"></button>
    </main>
    ```

Ha elkészült, az *index. html* fájlnak a következőhöz hasonlóan kell kinéznie: [index. html](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/index.html).

A következő lépés a CSS-stílusok definiálása. A CSS-stílusok határozzák meg az alkalmazás-összetevők elrendezésének és az alkalmazás megjelenésének módját. Nyissa meg az *index. css* programot, és adja hozzá a következő kódot. A `@media` stílus alternatív stílusú beállításokat határoz meg, amelyeket akkor kell használni, ha a képernyő szélessége 700 képpontnál kisebb.  

   ```CSS
    html, body {
        padding: 0;
        margin: 0;
        font-family: Gotham, Helvetica, sans-serif;
        overflow-x: hidden;
    }

    header {
        width: calc(100vw - 10px);
        height: 30px;
        padding: 15px 0 20px 20px;
        font-size: 25px;
        font-style: italic;
        font-family: "Comic Sans MS", cursive, sans-serif;
        line-height: 30px;
        font-weight: bold;
        color: white;
        background-color: #007faa;
    }

    header span {
        vertical-align: middle;
    }

    header img {
        height: 30px;
        vertical-align: middle;
    }

    .searchPanel {
        position: relative;
        width: 350px;
    }

    .searchPanel div {
        padding: 20px;
    }

    .searchPanel input {
        width: calc(100% - 50px);
        font-size: 16px;
        border: 0;
        border-bottom: 1px solid #ccc;
    }

    #listPanel {
        position: absolute;
        top: 135px;
        left: 0px;
        width: 350px;
        height: calc(100vh - 135px);
        overflow-y: auto;
    }

    #myMap { 
        position: absolute;
        top: 65px;
        left: 350px;
        width: calc(100vw - 350px);
        height: calc(100vh - 65px);
    }

    .statusMessage {
        margin: 10px;
    }

    #myLocationBtn, #searchBtn {
        margin: 0;
        padding: 0;
        border: none;
        border-collapse: collapse;
        width: 32px;
        height: 32px; 
        text-align: center;
        cursor: pointer;
        line-height: 32px;
        background-repeat: no-repeat;
        background-size: 20px;
        background-position: center center;
        z-index: 200;
    }

    #myLocationBtn {
        position: absolute;
        top: 150px;
        right: 10px;
        box-shadow: 0px 0px 4px rgba(0,0,0,0.16);
        background-color: white;
        background-image: url("images/GpsIcon.png");
    }

    #myLocationBtn:hover {
        background-image: url("images/GpsIcon-hover.png");
    }

    #searchBtn {
        background-color: transparent;
        background-image: url("images/SearchIcon.png");
    }

    #searchBtn:hover {
        background-image: url("images/SearchIcon-hover.png");
    }

    .listItem {
        height: 50px;
        padding: 20px;
        font-size: 14px;
    }

    .listItem:hover {
        cursor: pointer;
        background-color: #f1f1f1;
    }

    .listItem-title {
        color: #007faa;
        font-weight: bold;
    }

    .storePopup {
        min-width: 150px;
    }

    .storePopup .popupTitle {
        border-top-left-radius: 4px;
        border-top-right-radius: 4px;
        padding: 8px;
        height: 30px;
        background-color: #007faa;
        color: white;
        font-weight: bold;
    }

    .storePopup .popupSubTitle {
        font-size: 10px;
        line-height: 12px;
    }

    .storePopup .popupContent {
        font-size: 11px;
        line-height: 18px;
        padding: 8px;
    }

    .storePopup img {
        vertical-align:middle;
        height: 12px;
        margin-right: 5px;
    }

    /* Adjust the layout of the page when the screen width is less than 700 pixels. */
    @media screen and (max-width: 700px) {
        .searchPanel {
            width: 100vw;
        }

        #listPanel {
            top: 385px;
            width: 100%;
            height: calc(100vh - 385px);
        }

        #myMap {
            width: 100vw;
            height: 250px;
            top: 135px;
            left: 0px;
        }

        #myLocationBtn {
            top: 220px;
        }
    }

    .mapCenterIcon {
        display: block;
        width: 10px;
        height: 10px;
        border-radius: 50%;
        background: orange;
        border: 2px solid white;
        cursor: pointer;
        box-shadow: 0 0 0 rgba(0, 204, 255, 0.4);
        animation: pulse 3s infinite;
    }

    @keyframes pulse {
        0% {
            box-shadow: 0 0 0 0 rgba(0, 204, 255, 0.4);
        }

        70% {
            box-shadow: 0 0 0 50px rgba(0, 204, 255, 0);
        }

        100% {
            box-shadow: 0 0 0 0 rgba(0, 204, 255, 0);
        }
    }
   ```

Ha most futtatja az alkalmazást, a fejléc, a keresőmező és a Keresés gomb jelenik meg, de a Térkép nem látható, mert még nem töltődött be. Ha keresést próbál végrehajtani, semmi nem történik. Be kell állítania a következő szakaszban ismertetett JavaScript-logikát az áruház kereső összes funkciójának eléréséhez.

## <a name="wire-the-application-with-javascript"></a>Az alkalmazás drótba való átvitele JavaScript-sel

Ekkor minden be van állítva a felhasználói felületen. Most hozzá kell adnia a JavaScriptet az adatgyűjtés betöltéséhez és elemzéséhez, majd a térképen az adatmegjelenítést. Első lépésként nyissa meg az *index. js fájlt* , és adja hozzá a kódot az alábbi lépések szerint.

1. Globális beállítások hozzáadásával könnyebben frissítheti a beállításokat. Emellett definiálhat változókat a térképhez, egy előugró ablakhoz, egy adatforráshoz, egy ikon réteghez, egy keresési terület középpontját megjelenítő HTML-jelölőhöz, valamint a Azure Maps keresési szolgáltatás ügyfelének egy példányához.

    ```JavaScript
    //The maximum zoom level to cluster data point data on the map.
    var maxClusterZoomLevel = 11;

    //The URL to the store location data.
    var storeLocationDataUrl = 'data/ContosoCoffee.txt';

    //The URL to the icon image.
    var iconImageUrl = 'images/CoffeeIcon.png';
    var map, popup, datasource, iconLayer, centerMarker, searchURL;
    ```

1. Adja hozzá a kódot az *index. js*fájlhoz. A következő kód inicializálja a térképet, egy olyan [esemény](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) -figyelőt hoz létre, amely megvárja, amíg az oldal be nem fejeződik, és bekapcsolja az eseményeket a Térkép betöltésének figyeléséhez, és a keresés és a saját hely gombra kattint.

   Ha a felhasználó a Keresés gombra kattint, vagy amikor a felhasználó megnyomja az ENTER billentyűt a keresőmezőbe való belépés után, a rendszer megkezdi a felhasználó lekérdezésével kapcsolatos fuzzy keresést. Adja át az ország ISO 2 értékének egy tömbjét `countrySet` a keresési eredmények ezen országokban/régiókban való korlátozásához. Az országok és régiók keresésének korlátozása segít a visszaadott eredmények pontosságának növelésében. 
  
   Ha a keresés elkészült, végezze el az első eredményt, és állítsa be a Térkép kamerát ezen a területen. Amikor a felhasználó kiválasztja a saját hely gombot, használja a böngészőben beépített HTML5 Térinformatikai API-t, hogy lekérje a felhasználó helyét, és középpontba helyezi a térképet a helyükön.  

   > [!Tip]
   > Amikor felugró ablakokat használ, érdemes egyetlen `Popup` példányt létrehoznia, és újra felhasználni a példányt a tartalom és a pozíció frissítésével. A kódhoz `Popup`hozzáadott minden példányhoz több Dom-elemet adnak hozzá a laphoz. Minél több DOM-elem van egy oldalon, annál több dolog szükséges a böngésző nyomon követésében. Ha túl sok elem van, előfordulhat, hogy a böngésző lassú lesz.

    ```JavaScript
    function initialize() {
        //Initialize a map instance.
        map = new atlas.Map('myMap', {
            center: [-90, 40],
            zoom: 2,

            //Add your Azure Maps subscription key to the map SDK.
            authOptions: {
                authType: 'subscriptionKey',
                subscriptionKey: '<Your Azure Maps Key>'
            }
        });

        //Create a pop-up window, but leave it closed so we can update it and display it later.
        popup = new atlas.Popup();

        //Use SubscriptionKeyCredential with a subscription key
        const subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

        //Use subscriptionKeyCredential to create a pipeline
        const pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential, {
            retryOptions: { maxTries: 4 } // Retry options
        });

        //Create an instance of the SearchURL client.
        searchURL = new atlas.service.SearchURL(pipeline);

        //If the user selects the search button, geocode the value the user passed in.
        document.getElementById('searchBtn').onclick = performSearch;

        //If the user presses Enter in the search box, perform a search.
        document.getElementById('searchTbx').onkeyup = function(e) {
            if (e.keyCode === 13) {
                performSearch();
            }
        };

        //If the user selects the My Location button, use the Geolocation API to get the user's location. Center and zoom the map on that location.
        document.getElementById('myLocationBtn').onclick = setMapToUserLocation;

        //Wait until the map resources are ready.
        map.events.add('ready', function() {

            //Add your post-map load functionality.

        });
    }

    //Create an array of country ISO 2 values to limit searches to. 
    var countrySet = ['US', 'CA', 'GB', 'FR','DE','IT','ES','NL','DK'];

    function performSearch() {
        var query = document.getElementById('searchTbx').value;

        //Perform a fuzzy search on the users query.
        searchURL.searchFuzzy(atlas.service.Aborter.timeout(3000), query, {
            //Pass in the array of country ISO2 for which we want to limit the search to.
            countrySet: countrySet
        }).then(results => {
            //Parse the response into GeoJSON so that the map can understand.
            var data = results.geojson.getFeatures();

            if (data.features.length > 0) {
                //Set the camera to the bounds of the results.
                map.setCamera({
                    bounds: data.features[0].bbox,
                    padding: 40
                });
            } else {
                document.getElementById('listPanel').innerHTML = '<div class="statusMessage">Unable to find the location you searched for.</div>';
            }
        });
    }

    function setMapToUserLocation() {
        //Request the user's location.
        navigator.geolocation.getCurrentPosition(function(position) {
            //Convert the Geolocation API position to a longitude and latitude position value that the map can interpret and center the map over it.
            map.setCamera({
                center: [position.coords.longitude, position.coords.latitude],
                zoom: maxClusterZoomLevel + 1
            });
        }, function(error) {
            //If an error occurs when the API tries to access the user's position information, display an error message.
            switch (error.code) {
                case error.PERMISSION_DENIED:
                    alert('User denied the request for geolocation.');
                    break;
                case error.POSITION_UNAVAILABLE:
                    alert('Position information is unavailable.');
                    break;
                case error.TIMEOUT:
                    alert('The request to get user position timed out.');
                    break;
                case error.UNKNOWN_ERROR:
                    alert('An unknown error occurred.');
                    break;
            }
        });
    }

    //Initialize the application when the page is loaded.
    window.onload = initialize;
    ```

1. A Térkép `ready` esemény-figyelőben adjon hozzá egy nagyítás vezérlőelemet és egy HTML-jelölőt a keresési terület középpontjának megjelenítéséhez.

    ```JavaScript
    //Add a zoom control to the map.
    map.controls.add(new atlas.control.ZoomControl(), {
        position: 'top-right'
    });

    //Add an HTML marker to the map to indicate the center to use for searching.
    centerMarker = new atlas.HtmlMarker({
        htmlContent: '<div class="mapCenterIcon"></div>',
        position: map.getCamera().center
    });

    map.markers.add(centerMarker);
    ```

1. A Térkép `ready` esemény-figyelőben adjon hozzá egy adatforrást. Ezután hívja meg az adatkészlet betöltését és elemzését. Fürtözés engedélyezése az adatforráson. Az adatforrás-csoportok fürtözése a fürtben egymással átfedésben lévő pontokra mutat. A fürtök különálló pontokra vannak osztva, ahogy a felhasználó nagyítja. Ez a felhasználói élmény növelését és a teljesítmény javítását teszi lehetővé.

    ```JavaScript
    //Create a data source, add it to the map, and then enable clustering.
    datasource = new atlas.source.DataSource(null, {
        cluster: true,
        clusterMaxZoom: maxClusterZoomLevel - 1
    });

    map.sources.add(datasource);

    //Load all the store data now that the data source is defined.  
    loadStoreData();
    ```

1. Miután betöltötte az adatkészletet a `ready` Térkép esemény-figyelőben, Definiáljon egy réteget az adat megjelenítéséhez. A fürtözött adatpontok megjelenítéséhez buborék réteget kell használni. A szimbólum réteg az egyes fürtökben lévő pontok számát jeleníti meg a buborék réteg felett. A második szimbólum réteg egyéni ikont jelenít meg a térképen lévő egyes helyszínekhez.

   A `mouseover` buborék `mouseout` -és ikon-rétegek hozzáadásával és eseményeivel megváltoztathatja az egérmutatót, amikor a felhasználó egy fürt vagy ikon fölé viszi a térképet. Adjon hozzá `click` egy eseményt a fürt buborék rétegéhez. Ez `click` az esemény két szinten nagyítja a térképet, és a térképet a fürtön keresztül középpontba veszi, amikor a felhasználó kiválaszt egy fürtöt. Adjon hozzá `click` egy eseményt az ikon réteghez. Ez `click` az esemény egy olyan előugró ablakot jelenít meg, amely a kávézó részleteit mutatja be, amikor a felhasználó egy adott hely ikonját választja ki. Adjon hozzá egy eseményt a térképhez, és figyelje, hogy mikor fejeződik be a Térkép. Ha ez az esemény következik be, frissítse a lista panel elemeit.  

    ```JavaScript
    //Create a bubble layer to render clustered data points.
    var clusterBubbleLayer = new atlas.layer.BubbleLayer(datasource, null, {
        radius: 12,
        color: '#007faa',
        strokeColor: 'white',
        strokeWidth: 2,
        filter: ['has', 'point_count'] //Only render data points that have a point_count property; clusters have this property.
    });

    //Create a symbol layer to render the count of locations in a cluster.
    var clusterLabelLayer = new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            image: 'none' //Hide the icon image.
        },

        textOptions: {
            textField: ['get', 'point_count_abbreviated'],
            size: 12,
            font: ['StandardFont-Bold'],
            offset: [0, 0.4],
            color: 'white'
        }
    });

    map.layers.add([clusterBubbleLayer, clusterLabelLayer]);

    //Load a custom image icon into the map resources.
    map.imageSprite.add('myCustomIcon', iconImageUrl).then(function() {

    //Create a layer to render a coffee cup symbol above each bubble for an individual location.
    iconLayer = new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            //Pass in the ID of the custom icon that was loaded into the map resources.
            image: 'myCustomIcon',

            //Optionally, scale the size of the icon.
            font: ['SegoeUi-Bold'],

            //Anchor the center of the icon image to the coordinate.
            anchor: 'center',

            //Allow the icons to overlap.
            allowOverlap: true
        },

        filter: ['!', ['has', 'point_count']] //Filter out clustered points from this layer.
    });

    map.layers.add(iconLayer);

    //When the mouse is over the cluster and icon layers, change the cursor to a pointer.
    map.events.add('mouseover', [clusterBubbleLayer, iconLayer], function() {
        map.getCanvasContainer().style.cursor = 'pointer';
    });

    //When the mouse leaves the item on the cluster and icon layers, change the cursor back to the default (grab).
    map.events.add('mouseout', [clusterBubbleLayer, iconLayer], function() {
        map.getCanvasContainer().style.cursor = 'grab';
    });

    //Add a click event to the cluster layer. When the user selects a cluster, zoom into it by two levels.  
    map.events.add('click', clusterBubbleLayer, function(e) {
        map.setCamera({
            center: e.position,
            zoom: map.getCamera().zoom + 2
        });
    });

    //Add a click event to the icon layer and show the shape that was selected.
    map.events.add('click', iconLayer, function(e) {
        showPopup(e.shapes[0]);
    });

    //Add an event to monitor when the map is finished rendering the map after it has moved.
    map.events.add('render', function() {
        //Update the data in the list.
        updateListItems();
    });
    ```

1. A Coffee Shop-adatkészlet betöltését követően először le kell tölteni. Ezt követően a szövegfájlt sorba kell bontani. Az első sor tartalmazza a fejléc információit. Annak érdekében, hogy a kód könnyebben követhető legyen, a fejlécet egy objektumba elemezzük, amelyet aztán az egyes tulajdonságok cellás indexének megkereséséhez használhatunk. Az első sor után hurkot a többi sorban, és hozzon létre egy pont funkciót. Adja hozzá a pont szolgáltatást az adatforráshoz. Végül frissítse a lista panelt.

    ```JavaScript
    function loadStoreData() {

    //Download the store location data.
    fetch(storeLocationDataUrl)
        .then(response => response.text())
        .then(function(text) {

            //Parse the tab-delimited file data into GeoJSON features.
            var features = [];

            //Split the lines of the file.
            var lines = text.split('\n');

            //Grab the header row.
            var row = lines[0].split('\t');

            //Parse the header row and index each column to make the code for parsing each row easier to follow.
            var header = {};
            var numColumns = row.length;
            for (var i = 0; i < row.length; i++) {
                header[row[i]] = i;
            }

            //Skip the header row and then parse each row into a GeoJSON feature.
            for (var i = 1; i < lines.length; i++) {
                row = lines[i].split('\t');

                //Ensure that the row has the correct number of columns.
                if (row.length >= numColumns) {

                    features.push(new atlas.data.Feature(new atlas.data.Point([parseFloat(row[header['Longitude']]), parseFloat(row[header['Latitude']])]), {
                        AddressLine: row[header['AddressLine']],
                        City: row[header['City']],
                        Municipality: row[header['Municipality']],
                        AdminDivision: row[header['AdminDivision']],
                        Country: row[header['Country']],
                        PostCode: row[header['PostCode']],
                        Phone: row[header['Phone']],
                        StoreType: row[header['StoreType']],
                        IsWiFiHotSpot: (row[header['IsWiFiHotSpot']].toLowerCase() === 'true') ? true : false,
                        IsWheelchairAccessible: (row[header['IsWheelchairAccessible']].toLowerCase() === 'true') ? true : false,
                        Opens: parseInt(row[header['Opens']]),
                        Closes: parseInt(row[header['Closes']])
                    }));
                }
            }

            //Add the features to the data source.
            datasource.add(new atlas.data.FeatureCollection(features));

            //Initially, update the list items.
            updateListItems();
        });
    }
    ```

1. Ha frissíti a lista panelt, a rendszer kiszámítja a Térkép középpontjának és az aktuális Térkép nézet összes funkciójának a távolságát. A funkciók ezután távolság szerint rendezve jelennek meg. A rendszer létrehoz egy HTML-kódot, amely megjeleníti az egyes helyeket a lista paneljén.

    ```JavaScript
    var listItemTemplate = '<div class="listItem" onclick="itemSelected(\'{id}\')"><div class="listItem-title">{title}</div>{city}<br />Open until {closes}<br />{distance} miles away</div>';

    function updateListItems() {
        //Hide the center marker.
        centerMarker.setOptions({
            visible: false
        });

        //Get the current camera and view information for the map.
        var camera = map.getCamera();
        var listPanel = document.getElementById('listPanel');

        //Check to see whether the user is zoomed out a substantial distance. If they are, tell the user to zoom in and to perform a search or select the My Location button.
        if (camera.zoom < maxClusterZoomLevel) {
            //Close the pop-up window; clusters might be displayed on the map.  
            popup.close(); 
            listPanel.innerHTML = '<div class="statusMessage">Search for a location, zoom the map, or select the My Location button to see individual locations.</div>';
        } else {
            //Update the location of the centerMarker property.
            centerMarker.setOptions({
                position: camera.center,
                visible: true
            });

            //List the ten closest locations in the side panel.
            var html = [], properties;

            /*
            Generating HTML for each item that looks like this:
            <div class="listItem" onclick="itemSelected('id')">
                <div class="listItem-title">1 Microsoft Way</div>
                Redmond, WA 98052<br />
                Open until 9:00 PM<br />
                0.7 miles away
            </div>
            */

            //Get all the shapes that have been rendered in the bubble layer. 
            var data = map.layers.getRenderedShapes(map.getCamera().bounds, [iconLayer]);

            //Create an index of the distances of each shape.
            var distances = {};

            data.forEach(function (shape) {
                if (shape instanceof atlas.Shape) {

                    //Calculate the distance from the center of the map to each shape and store in the index. Round to 2 decimals.
                    distances[shape.getId()] = Math.round(atlas.math.getDistanceTo(camera.center, shape.getCoordinates(), 'miles') * 100) / 100;
                }
            });

            //Sort the data by distance.
            data.sort(function (x, y) {
                return distances[x.getId()] - distances[y.getId()];
            });

            data.forEach(function(shape) {
                properties = shape.getProperties();
                html.push('<div class="listItem" onclick="itemSelected(\'', shape.getId(), '\')"><div class="listItem-title">',
                properties['AddressLine'],
                '</div>',
                //Get a formatted addressLine2 value that consists of City, Municipality, AdminDivision, and PostCode.
                getAddressLine2(properties),
                '<br />',

                //Convert the closing time to a format that is easier to read.
                getOpenTillTime(properties),
                '<br />',

                //Get the distance of the shape.
                distances[shape.getId()],
                ' miles away</div>');
            });

            listPanel.innerHTML = html.join('');

            //Scroll to the top of the list panel in case the user has scrolled down.
            listPanel.scrollTop = 0;
        }
    }

    //This converts a time that's in a 24-hour format to an AM/PM time or noon/midnight string.
    function getOpenTillTime(properties) {
        var time = properties['Closes'];
        var t = time / 100;
        var sTime;

        if (time === 1200) {
            sTime = 'noon';
        } else if (time === 0 || time === 2400) {
            sTime = 'midnight';
        } else {
            sTime = Math.round(t) + ':';

            //Get the minutes.
            t = (t - Math.round(t)) * 100;

            if (t === 0) {
                sTime += '00';
            } else if (t < 10) {
                sTime += '0' + t;
            } else {
                sTime += Math.round(t);
            }

            if (time < 1200) {
                sTime += ' AM';
            } else {
                sTime += ' PM';
            }
        }

        return 'Open until ' + sTime;
    }

    //Create an addressLine2 string that contains City, Municipality, AdminDivision, and PostCode.
    function getAddressLine2(properties) {
        var html = [properties['City']];

        if (properties['Municipality']) {
            html.push(', ', properties['Municipality']);
        }

        if (properties['AdminDivision']) {
            html.push(', ', properties['AdminDivision']);
        }

        if (properties['PostCode']) {
            html.push(' ', properties['PostCode']);
        }

        return html.join('');
    }
    ```

1. Amikor a felhasználó kiválaszt egy elemet a lista paneljén, az az alakzat, amelyhez az elem kapcsolódik, beolvassa az adatforrásból. Létrejön egy előugró ablak, amely az alakzatban tárolt tulajdonságok adatai alapján jön létre. A Térkép középpontban van az alakzaton. Ha a Térkép kevesebb, mint 700 képpont széles, a Térkép nézet eltolással jelenik meg, hogy az előugró ablak látható legyen.

    ```JavaScript
    //When a user selects a result in the side panel, look up the shape by its ID value and display the pop-up window.
    function itemSelected(id) {
        //Get the shape from the data source by using its ID.  
        var shape = datasource.getShapeById(id);
        showPopup(shape);

        //Center the map over the shape on the map.
        var center = shape.getCoordinates();
        var offset;

        //If the map is less than 700 pixels wide, then the layout is set for small screens.
        if (map.getCanvas().width < 700) {
            //When the map is small, offset the center of the map relative to the shape so that there is room for the popup to appear.
            offset = [0, -80];
        }

        map.setCamera({
            center: center,
            centerOffset: offset
        });
    }

    function showPopup(shape) {
        var properties = shape.getProperties();

        /* Generating HTML for the pop-up window that looks like this:

            <div class="storePopup">
                <div class="popupTitle">
                    3159 Tongass Avenue
                    <div class="popupSubTitle">Ketchikan, AK 99901</div>
                </div>
                <div class="popupContent">
                    Open until 22:00 PM<br/>
                    <img title="Phone Icon" src="images/PhoneIcon.png">
                    <a href="tel:1-800-XXX-XXXX">1-800-XXX-XXXX</a>
                    <br>Amenities:
                    <img title="Wi-Fi Hotspot" src="images/WiFiIcon.png">
                    <img title="Wheelchair Accessible" src="images/WheelChair-small.png">
                </div>
            </div>
        */

         //Calculate the distance from the center of the map to the shape in miles, round to 2 decimals.
        var distance = Math.round(atlas.math.getDistanceTo(map.getCamera().center, shape.getCoordinates(), 'miles') * 100)/100;

        var html = ['<div class="storePopup">'];
        html.push('<div class="popupTitle">',
            properties['AddressLine'],
            '<div class="popupSubTitle">',
            getAddressLine2(properties),
            '</div></div><div class="popupContent">',

            //Convert the closing time to a format that's easier to read.
            getOpenTillTime(properties),

            //Add the distance information.  
            '<br/>', distance,
            ' miles away',
            '<br /><img src="images/PhoneIcon.png" title="Phone Icon"/><a href="tel:',
            properties['Phone'],
            '">',  
            properties['Phone'],
            '</a>'
        );

        if (properties['IsWiFiHotSpot'] || properties['IsWheelchairAccessible']) {
            html.push('<br/>Amenities: ');

            if (properties['IsWiFiHotSpot']) {
                html.push('<img src="images/WiFiIcon.png" title="Wi-Fi Hotspot"/>');
            }

            if (properties['IsWheelchairAccessible']) {
                html.push('<img src="images/WheelChair-small.png" title="Wheelchair Accessible"/>');
            }
        }

        html.push('</div></div>');

        //Update the content and position of the pop-up window for the specified shape information.
        popup.setOptions({

            //Create a table from the properties in the feature.
            content:  html.join(''),
            position: shape.getCoordinates()
        });

        //Open the pop-up window.
        popup.open(map);
    }
    ```

Most már van egy teljesen működőképes tároló-lokátora. A böngészőben nyissa meg az *index. html* fájlt az áruház keresője számára. Ha a fürtök megjelennek a térképen, a keresőmező használatával kereshet egy helyet a saját hely gomb kiválasztásával, egy fürt kiválasztásával, vagy a Térkép nagyításával az egyes helyek megjelenítéséhez.

Amikor a felhasználó először kiválasztja a saját hely gombot, a böngésző egy biztonsági figyelmeztetést jelenít meg, amely engedélyt kér a felhasználó tartózkodási helyének elérésére. Ha a felhasználó beleegyezik, hogy megosztja a helyüket, a Térkép nagyítja a felhasználó helyét, és megjelenik a közeli kávézók. 

<br/>
<center>

![Képernyőfelvétel a böngésző azon kérelméről, amely hozzáfér a felhasználó helyéhez](./media/tutorial-create-store-locator/GeolocationApiWarning.png)</center>

Ha egy Coffee Shop-hellyel rendelkező területen közelíti meg a nagyítást, a fürtök különálló helyekre vannak osztva. Válassza ki az egyik ikont a térképen, vagy válasszon ki egy elemet az oldalsó panelen egy előugró ablak megjelenítéséhez, amely az adott hely információit jeleníti meg.

<br/>
<center>

![A befejezett tároló-lokátor képernyőképe](./media/tutorial-create-store-locator/FinishedSimpleStoreLocator.png)</center>

Ha a böngészőablakot kevesebb, mint 700 képpont szélesre módosítja, vagy egy mobileszközön nyitja meg az alkalmazást, az elrendezés úgy változik, hogy jobban megfeleljen a kisebb képernyőknek. 

<br/>
<center>

![Képernyőkép az áruházi lokátor kis képernyős verziójáról](./media/tutorial-create-store-locator/FinishedSimpleStoreLocatorSmallScreen.png)</center>

## <a name="next-steps"></a>További lépések

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre alapszintű tároló-lokátort Azure Maps használatával. Előfordulhat, hogy az oktatóanyagban létrehozott áruház-lokátor rendelkezik az összes szükséges funkcióval. Lehetőség van az áruházbeli lokátor szolgáltatásainak hozzáadására, vagy további speciális funkciók használatára az egyéni felhasználói élmény érdekében: 

> [!div class="checklist"]
> * A keresőmezőbe való beíráskor engedélyezze a [javaslatokat](https://azuremapscodesamples.azurewebsites.net/?sample=Search%20Autosuggest%20and%20JQuery%20UI) .  
> * [Több nyelv támogatásának](https://azuremapscodesamples.azurewebsites.net/?sample=Map%20Localization)megadása. 
> * Lehetővé teszi a felhasználók számára, hogy [egy útvonal mentén szűrhetik](https://azuremapscodesamples.azurewebsites.net/?sample=Filter%20Data%20Along%20Route)a tárolóhelyeket. 
> * Adja [meg a szűrők beállításának](https://azuremapscodesamples.azurewebsites.net/?sample=Filter%20Symbols%20by%20Property)lehetőségét. 
> * Adja meg a támogatást egy kezdeti keresési érték megadásához egy lekérdezési karakterlánc használatával. Ha ezt a lehetőséget használja az áruház lokátorában, a felhasználók könyvjelzőket és megosztási kereséseket is tartalmazhatnak. Emellett egyszerű módszert is biztosít, amellyel egy másik oldalról átadhatja a keresést ezen a lapon.  
> * Az áruházbeli lokátor üzembe helyezése [Azure app Service](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-html)webalkalmazásként. 
> * Tárolja adatait egy adatbázisban, és keressen a közeli helyekre. További információ: [SQL Server térbeli adattípusok áttekintése](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-types-overview?view=sql-server-2017) és [térbeli adatainak lekérdezése a legközelebbi szomszédban](https://docs.microsoft.com/sql/relational-databases/spatial/query-spatial-data-for-nearest-neighbor?view=sql-server-2017).

> [!div class="nextstepaction"]
> [Teljes forráskód megtekintése](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator)

> [!div class="nextstepaction"]
> [Élő minta megtekintése](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Simple%20Store%20Locator)

További információ az Azure Maps lefedettségéről és képességeiről:

> [!div class="nextstepaction"]
> [Nagyítási szintek és csemperács](zoom-levels-and-tile-grid.md)

További példakódok és az interaktív kódolási felület bemutatása:

> [!div class="nextstepaction"]
> [A térkép vezérlőelem használata](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [Adatvezérelt stílusú kifejezések használata](data-driven-style-expressions-web-sdk.md)