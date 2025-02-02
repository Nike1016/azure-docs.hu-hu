---
title: Alsóbb rétegbeli eszközök – Azure IoT Edge-átjárókat |} A Microsoft Docs
description: Az Azure IoT Edge segítségével átlátható, átlátszatlan vagy proxy átjáróeszköz létrehozása, amely adatokat küld a több alárendelt eszközről a felhőbe vagy helyi feldolgozza azt.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/25/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: c3a49c4333838652f7063d6a89cfd8cceace1cf8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67054198"
---
# <a name="how-an-iot-edge-device-can-be-used-as-a-gateway"></a>IoT Edge-eszköz segítségével hogyan biztosítható átjáróként

Az IoT Edge-megoldások átjárók biztosítják az eszközkapcsolat és az IoT-eszközök, amelyek egyébként nem lenne ezekre a képességekre, peremhálózati elemzést. Az Azure IoT Edge segítségével IoT-átjáró, függetlenül attól, hogy azok kapcsolódó kapcsolat, identity vagy peremhálózati elemzés futtatása a szükségletét. Ez a cikk az átjáró minták csak tekintse meg jellemzői alsóbb rétegbeli eszközök csatlakoztatása és az eszközidentitással, nem hogyan eszköz adatok feldolgozása az átjárón.

## <a name="patterns"></a>Minták

Nincsenek az IoT Edge-eszköz használata átjáróként a három minták: transzparens, protokoll, fordítás, és identitás fordítási:
* **Transzparens** – sikerült elméletileg csatlakoztatása az IoT hubhoz eszközök csatlakozhatnak átjáróeszköz helyette. Az alsóbb rétegbeli eszközök a saját IoT Hub-identitásokat, és az MQTT, AMQP vagy HTTP-protokollokat használja. Az átjáró egyszerűen továbbítja a kommunikációt az eszközök és az IoT Hub között. Az eszközök is észleli, hogy a felhő egy átjárón keresztül kommunikálnak, és az eszközöket az IoT Hub használata a felhasználók nem észleli a köztes átjáróeszköz. Így az átjáró az átlátható. Tekintse meg [transzparens átjáró létrehozása](how-to-create-transparent-gateway.md) bírálattal transzparens átjáróként IoT Edge-eszköz használatával.
* **Fordítási protokoll** – más néven egy nem átlátszó átjáró-minta, eszközöket, amelyek nem támogatják az MQTT, AMQP vagy HTTP segítségével átjáróeszköz az IoT Hub a felhasználók nevében adatokat küldeni. Az átjáró tisztában van azzal az alsóbb rétegbeli eszközök által használt protokollt, és az egyetlen eszköz, amely rendelkezik egy identitást az IoT hubon. Minden információt tűnik, hogy egy eszközt, az átjáró érkezik. Alsóbb rétegbeli eszközök kell beágyazása az üzenetek további információkra, ha felhőalapú alkalmazások elemezheti az adatokat az eszközönkénti alapon. Emellett az IoT Hub primitívek, például a párok, és módszereket csak az átjáróeszköz nem alsóbb rétegbeli eszközök számára érhető el.
* **Identitás fordítási** -eszközöket, amelyek nem kapcsolódnak az IoT Hub csatlakozhatnak egy átjáróeszköz, helyette. Az átjáró biztosítja az IoT Hub identitás- és protokoll fordítási az alsóbb rétegbeli eszközök nevében. Az átjáró nem elég intelligensek ahhoz az alsóbb rétegbeli eszközök által használt protokollt, identitást biztosíthat, és az IoT Hub primitívek fordítása. Alsóbb rétegbeli eszközök első osztályú eszközökön twins és módszerek az IoT Hub jelennek meg. Egy felhasználó kezelheti az eszközöket az IoT Hubban, és nem észleli a köztes átjáróeszköz.

![Diagram – transzparens, protokoll és identitás átjáró minták](./media/iot-edge-as-gateway/edge-as-gateway.png)

## <a name="use-cases"></a>Használati esetek
Az összes átjáró-minták a következő előnyöket nyújtják:
* **A peremhálózaton Analytics** – használata AI-szolgáltatások helyileg a felhőbe teljes hűségű telemetria küldése nélkül alsóbb rétegbeli eszközökről érkező adatok feldolgozása. Keresse meg és helyileg insights reagálhat rájuk, és csak IoT hubnak küldi az adatok egy részét. 
* **Alsóbb rétegbeli eszközök elkülönítési** – az átjáró eszköz valamennyi alsóbb rétegbeli eszközök az interneten való kitettség segédalkalmazási. Azt is Dőljön között egy Zásoktól hálózati kapcsolat nem rendelkező és a egy IT-hálózat, amely a webes hozzáférést biztosít. 
* **Kapcsolat multiplexálási** – minden eszköz csatlakoztatása az IoT hub az IoT Edge-átjáró használatával ugyanazt az alapul szolgáló kapcsolatot.
* **Forgalom simítás** – az IoT Edge-eszköz automatikus végrehajtása exponenciális visszatartással, ha az IoT Hub szabályozza a forgalom, az üzenetek a helyi megőrzése közben. Ezzel az értékelemmel teszi adatforgalmának rugalmas megoldását.
* **Offline támogatást** – az átjáró eszköz tárolja az üzeneteket, és ikereszköz-frissítések, amelyekhez nem lehet kézbesíteni az IoT hubnak.

Olyan átjáró, amely fordítási protokollt is elvégezheti, peremhálózati elemzést, eszköz elkülönítés, forgalom simítás és offline támogatja a meglévő eszközökkel és új eszközöket, amelyek korlátozott erőforráshoz. Több meglévő eszközt állít elő, amelyek az üzleti elemzések; működtethet adatok azonban nem tervezték őket felhőalapú összefüggő szem előtt. Átlátszatlan átjárók lehetővé teszik, hogy ezeket az adatokat a fel és IoT-megoldások használják.

Egy átjáró, amely identitás fordítási protokollfordítás előnyét, és emellett lehetővé teszi, hogy a felhőből alsóbb rétegbeli eszközök teljes kezelhetőségét. Minden eszközét az IoT-megoldás jelennek meg az IoT Hub függetlenül a protokollt használják.

## <a name="cheat-sheet"></a>Hasznos tanácsok
Íme egy gyors Adatlap, amely összehasonlítja az IoT Hub primitívek használatakor transzparens, átlátszatlan (protokoll), és proxy-átjárók.

| &nbsp; | Transzparens átjáró | Protokollfordítás | Identitás-fordítás |
|--------|-------------|--------|--------|
| Az IoT Hub-identitásjegyzék tárolja identitások | Az összes csatlakoztatott eszközön identitások | Csak az átjáró eszköz identitása | Az összes csatlakoztatott eszközön identitások |
| Ikereszközök | Minden csatlakoztatott eszközhöz tartozik a saját ikereszköz | Az átjáró csak egy eszköz- és modul twins rendelkezik | Minden csatlakoztatott eszközhöz tartozik a saját ikereszköz |
| Közvetlen metódusok és a felhőből az eszközre irányuló üzenetek | A felhő külön-külön kezelheti minden csatlakoztatott eszköz | A felhő csak kezelheti az átjáró eszköz | A felhő külön-külön kezelheti minden csatlakoztatott eszköz |
| [Az IoT Hub szabályozások és kvóták](../iot-hub/iot-hub-devguide-quotas-throttling.md) | Egyes eszközök a alkalmazni | Az átjáró eszköz a alkalmazni | Egyes eszközök a alkalmazni |

Egy nem átlátszó átjáró (protokollfordítás) minta használatakor, hogy az átjárón keresztül csatlakozó összes eszközök megosztása azonos felhőből az eszközre beolvasása, amely legfeljebb 50 üzenetet is tartalmazhat. Ebből az következik, hogy az átjáró nem átlátszó minta kell használni, csak akkor, amikor minden mező-átjárón keresztül kapcsolódik néhány olyan eszközt, és a felhőből az eszközre irányuló forgalom értéke alacsony.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan állítható be transzparens átjáró: 

* [A transzparens átjáróként működő IoT Edge-eszköz konfigurálása](how-to-create-transparent-gateway.md)
* [Az Azure IoT hubra egy alsóbb rétegbeli eszköz hitelesítéséhez](how-to-authenticate-downstream-device.md)
* [Egy alárendelt eszköz csatlakoztatása az Azure IoT Edge-átjáró](how-to-connect-downstream-device.md)