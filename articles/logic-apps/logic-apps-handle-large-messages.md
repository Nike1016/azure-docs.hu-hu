---
title: Nagyméretű üzenetek kezelése – Azure Logic Apps | Microsoft Docs
description: Megtudhatja, hogyan kezelheti a nagyméretű üzenetek méretét a darabolással Azure Logic Apps
services: logic-apps
documentationcenter: ''
author: shae-hurst
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: logic-apps
ms.workload: logic-apps
ms.devlang: ''
ms.tgt_pltfrm: ''
ms.topic: article
ms.date: 4/27/2018
ms.author: shhurst
ms.openlocfilehash: 4a37345cf33cbb02a6bd9a70b0253a55ee4c9478
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035598"
---
# <a name="handle-large-messages-with-chunking-in-azure-logic-apps"></a>Nagy méretű üzenetek kezelése Azure Logic Apps

Az üzenetek kezelésekor Logic Apps korlátozza az üzenet tartalmát a maximális méretre. Ez a korlát segít csökkenteni a nagy méretű üzenetek tárolásával és feldolgozásával létrehozott terhelést. Ha ennél a korlátnál nagyobb üzeneteket szeretne kezelni, a Logic apps nagy méretű üzenetet készíthet kisebb üzenetekre. Így továbbra is átviheti a nagyméretű fájlokat a Logic Apps az adott körülmények között. Ha összekötőn vagy HTTP-n keresztül kommunikál más szolgáltatásokkal, Logic Apps nagy méretű üzeneteket használhat, de *csak* darabokban. Ez az állapot azt jelenti, hogy az összekötőknek a darabolást is támogatnia kell, vagy a Logic Apps és ezen szolgáltatások közötti HTTP-üzenetváltásnak a darabolást kell használnia.

Ebből a cikkből megtudhatja, hogyan állíthatja be a korlátnál nagyobb üzeneteket kezelő műveletek darabolását. A Logic apps-eseményindítók nem támogatják a darabolást, mert a több üzenet cseréje nagyobb terhelést jelent. 

## <a name="what-makes-messages-large"></a>Mi teszi a "nagy" üzeneteket?

Az üzenetek nagy méretűek az üzeneteket kezelő szolgáltatás alapján. A nagyméretű üzenetek esetében a pontos méretkorlát különbözik a Logic Apps és az összekötők között. A Logic Apps és az összekötők nem tudnak közvetlenül nagy mennyiségű üzenetet felvenni, amelyeket fel kell darabolni. Logic Apps üzenet méretének korlátja: [Logic apps korlátok és konfiguráció](../logic-apps/logic-apps-limits-and-config.md).
Az összekötők összes üzenetének méretére vonatkozó korlátozásért tekintse [meg az összekötő konkrét technikai részleteit](../connectors/apis-list.md).

### <a name="chunked-message-handling-for-logic-apps"></a>Darabolásos üzenetkezelés Logic Apps

Logic Apps nem használhatja közvetlenül az üzenet méretéhez képest nagyobb méretű üzenetekből származó kimeneteket. Ezekben a kimenetekben csak a darabolást támogató műveletek férhetnek hozzá az üzenet tartalmához. Így a nagyméretű üzeneteket kezelő műveletnek meg kell felelnie a következő feltételeknek:

* Natív módon támogatja a darabolást, ha az adott művelet egy összekötőhöz tartozik. 
* A művelet futásidejű konfigurációjában engedélyezve van a darabolási támogatás. 

Ellenkező esetben futásidejű hibaüzenetet kap, amikor megpróbál hozzáférni a nagyméretű tartalom kimenetéhez. A darabolás engedélyezéséhez tekintse [meg a darabolási támogatás beállítása](#set-up-chunking)című témakört.

### <a name="chunked-message-handling-for-connectors"></a>Az összekötők darabolásos üzenetkezelése

A Logic Apps kommunikáló szolgáltatások rendelkezhetnek saját üzenetek méretével. Ezek a korlátok gyakran kisebbek, mint a Logic Apps korlát. Tegyük fel például, hogy egy összekötő támogatja az adatdarabolást, az összekötők akár egy 30 MB méretű üzenetet is megtekinthetnek, a Logic Apps azonban nem. Az összekötő korlátjának betartása érdekében Logic Apps a 30 MB-nál nagyobb méretű üzeneteket kisebb adattömbökre osztja fel.

A darabolást támogató összekötők esetében az alapul szolgáló adatdarabolási protokoll láthatatlan a végfelhasználók számára. Azonban nem minden összekötő támogatja a darabolást, így ezek az összekötők futásidejű hibákat eredményeznek, ha a bejövő üzenetek túllépik az összekötők méretének korlátait.

<a name="set-up-chunking"></a>

## <a name="set-up-chunking-over-http"></a>Adatdarabolás beállítása HTTP-n keresztül

Általános HTTP-forgatókönyvek esetén a nagyméretű tartalmak letöltését és feltöltését HTTP-n keresztül, így a logikai alkalmazás és a végpont nagy méretű üzeneteket cserélhet le. Az üzeneteket azonban a Logic Apps várt módon kell megadnia. 

Ha egy végpont engedélyezte a letöltések vagy a feltöltések darabolását, a logikai alkalmazás HTTP-műveletei automatikusan nagy mennyiségű üzenetet tartalmaznak. Ellenkező esetben be kell állítania az adatdarabolás támogatását a végponton. Ha nem rendelkezik a végpont vagy az összekötő vezérlésével, előfordulhat, hogy nem rendelkezik a darabolás beállításával.

Ha egy HTTP-művelet még nem teszi lehetővé a darabolást, akkor a művelet `runTimeConfiguration` tulajdonságában is be kell állítania a darabolást. Ezt a tulajdonságot a műveleten belül állíthatja be közvetlenül a Code View Editorban a későbbiekben leírtak szerint, vagy a Logic Apps Designerben az itt leírtak szerint:

1. A http-művelet jobb felső sarkában kattintson a három pontot ábrázoló gombra ( **...** ), majd válassza a **Beállítások**lehetőséget.

   ![A műveletnél nyissa meg a Beállítások menüt.](./media/logic-apps-handle-large-messages/http-settings.png)

2. A **tartalom átvitele**területen állítsa be a **darabolás engedélyezése** **a**következőre:.

   ![Adatdarabolás bekapcsolása](./media/logic-apps-handle-large-messages/set-up-chunking.png)

3. Ha továbbra is szeretné beállítani a letöltésekhez vagy feltöltésekhez szükséges darabolást, folytassa a következő részekkel.

<a name="download-chunks"></a>

## <a name="download-content-in-chunks"></a>Tartalom letöltése a darabokban

Számos végpont automatikusan küld nagy mennyiségű üzenetet a HTTP GET kéréssel letöltött adattömbökben. A ledarabolt üzenetek HTTP-n keresztüli letöltéséhez a végpontnak támogatnia kell a részlegestartalmi kéréseket vagy a darabolásos letöltéseket. Ha a logikai alkalmazás HTTP GET kérést küld egy végpontnak a tartalom letöltéséhez, és a végpont "206" állapotkódot válaszol, a válasz feldarabolt tartalmat tartalmaz. Logic Apps nem tudja szabályozni, hogy a végpont támogatja-e a részleges kérelmeket. Ha azonban a logikai alkalmazás megkapja az első "206" választ, a logikai alkalmazás automatikusan több kérést küld az összes tartalom letöltéséhez.

Ha szeretné megtekinteni, hogy egy végpont támogatja-e a részleges tartalmat, küldjön egy HEAD-kérelmet. Ez a kérelem segít megállapítani, hogy a válasz `Accept-Ranges` tartalmazza-e a fejlécet. Így ha a végpont támogatja a darabolásos letöltéseket, de nem küldi el a darabolásos tartalmat, ezt a beállítást a `Range` HTTP Get-kérelem fejlécének beállításával javasolhatja. 

Ezek a lépések részletesen ismertetik azokat a folyamatokat, Logic Apps a feldarabolt tartalomnak egy végpontról a logikai alkalmazásba való letöltéséhez használja:

1. A logikai alkalmazás HTTP GET kérelmet küld a végpontnak.

   A kérelem fejléce opcionálisan tartalmazhat `Range` egy olyan mezőt is, amely leírja a tartalom adattömböket kérő bájtok tartományát.

2. A végpont az "206" állapotkódot és egy HTTP-üzenet törzsét válaszolja meg.

    Az ebben a részletekben található tartalom részletei a válasz `Content-Range` fejlécében jelennek meg, beleértve azokat az információkat is, amelyek segítenek Logic apps meghatározni a darab kezdetét és végét, valamint a teljes tartalom teljes méretét a darabolás előtt.

3. A logikai alkalmazás automatikusan elküldi a következő HTTP GET-kéréseket.

    A logikai alkalmazás követő GET kérelmeket küld, amíg a teljes tartalmat le nem kéri.

Ez a műveleti definíció például egy HTTP Get kérést mutat be, amely `Range` beállítja a fejlécet. A fejléc *azt sugallja* , hogy a végpontnak a darabolásos tartalommal kell válaszolnia:

```json
"getAction": {
    "inputs": {
        "headers": {
            "Range": "bytes=0-1023"
        },
       "method": "GET",
       "uri": "http://myAPIendpoint/api/downloadContent"
    },
    "runAfter": {},
    "type": "Http"
}
```

A GET kérelem beállítja a "tartomány" fejlécet a "Bytes = 0-1023" értékre, amely a bájtok tartománya. Ha a végpont támogatja a részleges tartalomra vonatkozó kéréseket, a végpont a kért tartományból származó tartalom-adatrészlettel válaszol. A végpont alapján a "Range" fejléc pontos formátuma eltérő lehet.

<a name="upload-chunks"></a>

## <a name="upload-content-in-chunks"></a>Tartalom feltöltése a darabokban

A feldarabolt tartalom http-műveletből való feltöltéséhez a művelet `runtimeConfiguration` tulajdonságában engedélyezni kell a darabolás támogatását. Ez a beállítás lehetővé teszi, hogy a művelet elindítsa a darabolási protokollt. A logikai alkalmazás ezután elküldheti a kezdeti BEJEGYZÉST, vagy ELHELYEZheti az üzenetet a célként megadott végponton. Azt követően, hogy a végpont egy javasolt mérettel válaszol, a logikai alkalmazás a tartalom adattömböket tartalmazó HTTP-javítási kérelmek küldésével követi nyomon.

Ezek a lépések részletesen ismertetik azokat a folyamatokat, Logic Apps a logikai alkalmazásból egy végpontra feltölti a darabolásos tartalmat:

1. A logikai alkalmazás egy kezdeti HTTP POST-vagy PUT-kérést küld egy üres üzenet törzsének. A kérelem fejléce tartalmazza ezt az információt arról a tartalomról, amelyet a logikai alkalmazás fel szeretne tölteni a darabokban:

   | Logic Apps kérelem fejlécének mezője | Value | Type | Leírás |
   |---------------------------------|-------|------|-------------|
   | **x-ms-transfer-mode** | darabolásos | Sztring | Azt jelzi, hogy a tartalom fel van töltve a darabokban |
   | **x-ms-content-length** | <*Content-Length*> | Integer | A teljes tartalom mérete bájtban a darabolás előtt |
   ||||

2. A végpont az "200" sikerességi állapotkód és a nem kötelező információk esetében válaszol:

   | Végpont válaszának fejléce mező | Type | Kötelező | Leírás |
   |--------------------------------|------|----------|-------------|
   | **x-ms-chunk-size** | Integer | Nem | A javasolt adathalmaz mérete bájtban |
   | **Location** | Sztring | Nem | A HTTP-javítási üzenetek küldésének helye |
   ||||

3. A logikai alkalmazás a következő adatokat tartalmazó HTTP-javítási üzeneteket hozza létre és küldi el:

   * Az **x-MS-darab-méret** vagy egy belső kiszámított méret alapján, amíg az összes **x-MS-Content-Length** teljes tartalom fel van töltve.

   * Ezek a fejlécek az egyes javítási üzenetekben küldött tartalmi adattömbökkel kapcsolatos adatokat tartalmazzák:

     | Logic Apps kérelem fejlécének mezője | Value | Type | Leírás |
     |---------------------------------|-------|------|-------------|
     | **Content-Range** | <*tartomány*> | Sztring | Az aktuális tartalom adatrészletének bájtjai, beleértve a kezdő értéket, a záró értéket és a tartalom teljes méretét, például: "Bytes = 0-1023/10100" |
     | **Content-Type** | <*Content-Type*> | Sztring | A darabolásos tartalom típusa |
     | **Content-Length** | <*Content-Length*> | Sztring | Az aktuális adathalmaz mérete bájtban megadva |
     |||||

4. Az egyes javítási kérelmek után a végpont az "200" állapotkód és a következő válasz fejlécek megválaszolásával megerősíti az egyes adathalmazok fogadását:

   | Végpont válaszának fejléce mező | Type | Kötelező | Leírás |
   |--------------------------------|------|----------|-------------|
   | **Tartomány** | Sztring | Igen | A végpont által fogadott tartalomhoz tartozó bájtok köre, például: "Bytes = 0-1023" |   
   | **x-ms-chunk-size** | Integer | Nem | A javasolt adathalmaz mérete bájtban |
   ||||

Ez a műveleti definíció például egy HTTP POST-kérést mutat be a darabolásos tartalom egy végpontra való feltöltéséhez. A művelet `runTimeConfiguration` tulajdonságában a tulajdonság a `contentTransfer` következőre van `chunked`kijelölve `transferMode` :

```json
"postAction": {
    "runtimeConfiguration": {
        "contentTransfer": {
            "transferMode": "chunked"
        }
    },
    "inputs": {
        "method": "POST",
        "uri": "http://myAPIendpoint/api/action",
        "body": "@body('getAction')"
    },
    "runAfter": {
    "getAction": ["Succeeded"]
    },
    "type": "Http"
}
```
