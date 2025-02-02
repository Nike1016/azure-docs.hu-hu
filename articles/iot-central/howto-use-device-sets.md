---
title: Beállítja a eszköz használata az Azure IoT Central alkalmazáshoz |} A Microsoft Docs
description: Kezelőként használata az eszköz beállítása az Azure IoT Central alkalmazáshoz.
author: ellenfosborne
ms.author: elfarber
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: e1e7b91e0808b9e23e653acd43b95f24a46c7d27
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503210"
---
# <a name="use-device-sets-in-your-azure-iot-central-application"></a>Eszköz használata az Azure IoT központi alkalmazás beállítása

Ez a cikk bemutatja, hogyan kezelőként eszközével állít be az Azure IoT Central alkalmazáshoz.

Egy eszköz csoportot az eszközöket, amelyek vannak csoportosítva, mivel azok megfelelnek bizonyos megadott feltételeknek megfelelő listája. Eszközök kezelése, megjelenítését és elemzését a nagy mennyiségű eszközt eszközök kisebb, logikai csoportokba csoportosításával súgó állítja be. Például egy eszköz listáját Seattle megtalálhatja az eszközöket, amelyhez még felelős technikus engedélyezéséhez az légkondicionálóját eszközök beállítása is létrehozhat. Ez a cikk bemutatja, hogyan hozhat létre, és az eszköz csoportjainak konfigurálása.

## <a name="create-a-device-set"></a>Hozzon létre egy eszköz csoportot

Eszköz létrehozásához állítsa be:

1. Válasszon **eszköz csoportok** a bal oldali navigációs menüben.

1. Válassza ki **+ új**.

    ![Új eszköz beállítása](media/howto-use-device-sets/image1.png)

1. Nevezze el az eszköz beállítása, amely egyedi a teljes alkalmazáson. Hozzáadhat egy leírást is. Egy eszköz csoportot csak egyetlen eszköz sablonból eszközök tartalmazhat. Válassza ki az eszközt a készlet használni kívánt sablont.

1. Az eszköz kiválasztásával, egy tulajdonságot egy összehasonlító operátor és egy értéket állítsa be az eszközök azonosítására a lekérdezés létrehozásához. Hozzáadhat több lekérdezések és eszközei megfeleljenek **minden** az eszköz beállítása a feltételek vannak elhelyezve. Az eszköz készletet hoz létre, bárki, aki hozzáfér az alkalmazáshoz, így bárki megtekintése, módosítása vagy törlése az eszköz beállítása érhető el.

    ![Eszköz beállítása lekérdezés](media/howto-use-device-sets/image2.png)

    > [!NOTE]
    > Az eszköz be kapcsolva egy dinamikus lekérdezést. Minden alkalommal, amikor az eszközök listájának megtekintéséhez lehet a különböző eszközök a listában. A lista attól függ, mely eszközök jelenleg felel meg a lekérdezés feltételeinek.

1. Válassza a **Mentés** elemet.

## <a name="configure-the-dashboard-for-your-device-set"></a>Az eszköz beállítása az irányítópulton konfigurálása

Miután létrehozta az eszköz beállítása, konfigurálhatja a **irányítópult**. A **irányítópult** , a kezdőlap elérését, ahol elhelyezi képek és hivatkozások. Azt is megteheti, hogy az eszköz beállítása az eszközök rácsok.

1. Válasszon **eszköz csoportok** a bal oldali navigációs menüben.

1. Válassza ki az eszköz beállítása.

1. Válassza ki az **Irányítópult** lapot.

1. Válassza a **Szerkesztés** elemet.

    ![A Tervező módban](media/howto-use-device-sets/image3.png)

1. Kép adásával kapcsolatos információkért lásd: [előkészítése és a feltöltés rendszerképek az Azure IoT Central alkalmazásnak](howto-prepare-images.md).

1. Hivatkozás csempe hozzáadása:
    1. Válasszon **hivatkozás** a jobb oldali ablaktáblán.
    1. Adja meg a hivatkozás egy **cím**.
    1. Válasszon egy URL-címet kell megnyitni a hivatkozás kiválasztásakor.
    1. Adjon a hivatkozás egy leírást, amely az alább látható a **cím**.
    1. Válassza a **Mentés** elemet.

        ![Hivatkozás mentése](media/howto-use-device-sets/image7.png)

    1. Helyezze át, és a hivatkozás csempe átméretezése a a **irányítópult**.

1. Adjon hozzá egy rácsot. Rács az eszköz beállítása az oszlopokat választja a eszközök tábláját.
    1. Válasszon **rács** a jobb oldali ablaktáblán.
    1. A rács adjon egy **cím**.
    1. Válassza ki az oszlopok kiválasztásával megjelenítendő **hozzáadása/eltávolítása**. A felugró panelen válassza ki a jelenjenek, és válassza a jobbra mutató nyílra, válassza ki azt az oszlopban.
    1. Válassza az **OK** gombot.
    1. Válassza a **Mentés** elemet.

        ![Mentse a rács](media/howto-use-device-sets/image9.png)

    1. Helyezze el a rácsban áthúzása a **irányítópult**.

        > [!NOTE]
        > Több lemezképek, a hivatkozások és a rácsok is hozzáadhat.
  
    1. Válassza a **Done** (Kész) lehetőséget.

Csempék az Azure IoT Central használatával kapcsolatos további tudnivalókért lásd: [irányítópult-csempék használata](howto-use-tiles.md).

### <a name="configure-a-location-map-in-your-device-sets-dashboard"></a>Az eszköz beállítása irányítópulton a helyek térképe konfigurálása

Hozzáadhat egy térkép jeleníthetik meg az eszköz beállítása az eszközök helyét.

Szeretne hozzáadni, az eszköz olyan térképet beállítja az irányítópulton, konfigurálnia kell egy hely mérési vagy a location tulajdonsághoz a eszköz sablonban. További tudnivalókért lásd: [hozzon létre egy helyen mérési](howto-set-up-template.md) vagy [hozzon létre egy hely tulajdonságot](howto-set-up-template.md).

1. Az eszközön állítsa **irányítópult**válassza **térkép** a könyvtárból.
2. Cím hozzáadásához, és válassza ki a helyet mérési vagy korábban konfigurált tulajdonság.
3. Válassza ki **mentése** , és a szolgáltatástérkép csempe megjeleníti az utolsó ismert helyek, az eszköz az eszköz beállítása.
4. Ha az operátor megtekinti a csoportok irányítópultját, az üzemeltető konfigurálta, ideértve a hely térkép minden csempe fog látni.

Átméretezheti a szolgáltatástérkép csempe az irányítópulton. Az eszköz adatait, nevét és helyét a térképen a PIN-kód kiválasztása jeleníti meg. Válassza ki az előugró ablak az eszköz tulajdonság lap megnyitásához.

## <a name="configure-the-list-for-your-device-set"></a>A lista az eszköz készlet konfigurálása

Miután létrehozta az eszköz beállítása, konfigurálhatja a **lista**. A **lista** megjeleníti az összes az eszközök az eszközön állítsa be a választott oszlopokat tartalmazó tábla.

1. Válasszon **eszköz csoportok** a bal oldali navigációs menüben.

1. Válassza ki a **lista** fülre.

1. Válasszon **Oszloptörlés beállításai**.

    ![Oszloptörlés beállításai](media/howto-use-device-sets/image11.png)

1. Jelölje ki a megjeleníteni kívánt oszlop kiválasztva, majd a jobbra mutató nyílra megjelenítendő oszlopok kiválasztása.

    ![Oszlop kiválasztása](media/howto-use-device-sets/image12.png)

1. Válassza az **OK** gombot.

## <a name="analytics"></a>Elemzés

Az eszköz beállítása az analytics megegyezik a fő analytics lapra a bal oldali navigációs menüben. További kapcsolatos elemzések a cikkben a [analytics létrehozása](howto-use-device-sets.md).

## <a name="next-steps"></a>További lépések

Most, hogy az eszköz csoportok használata az Azure IoT Central alkalmazásban megtanulhatta, Íme a javasolt következő lépésre:

> [!div class="nextstepaction"]
> [Telemetria szabályok létrehozása](howto-create-telemetry-rules.md)
