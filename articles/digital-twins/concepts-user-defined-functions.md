---
title: Adatfeldolgozási és felhasználó által definiált függvények az Azure Digital Twins szolgáltatással | Microsoft Docs
description: Az Azure Digital Twins adatfeldolgozási,-egyeztető és felhasználó által definiált függvények áttekintése.
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: alinast
ms.openlocfilehash: f4aa7e6660e3febdca6e0e5b1ad9f11bebaa48ea
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638460"
---
# <a name="data-processing-and-user-defined-functions"></a>Adatfeldolgozás és felhasználó által definiált függvények

Az Azure Digital Twins speciális számítási funkciókat kínál. A fejlesztők egyéni függvényeket adhatnak meg és futtathatnak a bejövő telemetria üzenetekben az események előre meghatározott végpontokra való küldéséhez.

## <a name="data-processing-flow"></a>Adatfeldolgozási folyamat

Miután az eszközök telemetria-adatok küldését az Azure digitális Twinsba, a fejlesztők négy fázisban dolgozhatnak fel adatokkal: *Érvényesítés*, *egyezés*, *számítás*és *Küldés*.

![Azure digitális ikrek adatfeldolgozási folyamata][1]

1. Az érvényesítési fázis átalakítja a bejövő telemetria üzenetet egy gyakran értelmezhető [adatátviteli objektum](https://docs.microsoft.com/aspnet/web-api/overview/data/using-web-api-with-entity-framework/part-5) formátumára. Ez a fázis az eszköz-és érzékelő érvényesítését is végrehajtja.
1. Az egyeztetési fázis megkeresi a megfelelő, felhasználó által definiált függvényeket a futtatáshoz. Az előre definiált egyeztetések a felhasználó által definiált függvényeket a bejövő telemetria üzenetből származó eszköz, az érzékelő és a lemezterület adatai alapján megtalálják.
1. A számítási fázis a felhasználó által definiált függvényeket futtatja az előző fázisban. Ezek a függvények elolvashatják és frissíthetik a kiszámított értékeket a térbeli gráf csomópontjain, és egyéni értesítéseket hozhatnak létre.
1. A küldő fázis a számítási fázisból származó összes egyéni értesítést átirányítja a gráfban definiált végpontokra.

## <a name="data-processing-objects"></a>Adatfeldolgozási objektumok

Az Azure Digital Twins adatfeldolgozása három objektumot határoz meg:a megfeleltetéseket, a *felhasználó által definiált függvényeket*és a *szerepkör*-hozzárendeléseket.

![Azure digitális Twins adatfeldolgozási objektumok][2]

<div id="matcher"></div>

### <a name="matchers"></a>Egyezők

Az egyeztetések olyan feltételeket határoznak meg, amelyek kiértékelik, hogy milyen műveleteket kell végrehajtani a bejövő érzékelők telemetria alapján. A egyezés meghatározására szolgáló feltételek tartalmazhatják az érzékelő, az érzékelő szülő eszköze és az érzékelő fölérendelt területének tulajdonságait is. A feltételek a [JSON-útvonal](https://jsonpath.com/) összehasonlításával vannak megadva, az alábbi példában látható módon:

- Az összes olyan adattípusi **hőmérséklet** -érzékelő, amelyet az Escape-karakterlánc értéke képvisel`\"Temperature\"`
- A `01` portján belül
- A kibővített tulajdonságot **gyártó** eszközökhöz tartozó, az Escape-karakterlánc értékére beállított`\"GoodCorp\"`
- Az Escape-karakterlánc által megadott típusú szóközökhöz tartozik`\"Venue\"`
- A szülő **SpaceId** leszármazottai`DE8F06CA-1138-4AD7-89F4-F782CC6F69FD`

```JSON
{
  "SpaceId": "DE8F06CA-1138-4AD7-89F4-F782CC6F69FD",
  "Name": "My custom matcher",
  "Description": "All sensors of datatype Temperature with 01 in their port that belong to devices with the extended property key Manufacturer set to the value GoodCorp and that belong to spaces of type Venue that are somewhere below space Id DE8F06CA-1138-4AD7-89F4-F782CC6F69FD",
  "Conditions": [
    {
      "target": "Sensor",
      "path": "$.dataType",
      "value": "\"Temperature\"",
      "comparison": "Equals"
    },
    {
      "target": "Sensor",
      "path": "$.port",
      "value": "01",
      "comparison": "Contains"
    },
    {
      "target": "SensorDevice",
      "path": "$.properties[?(@.name == 'Manufacturer')].value",
      "value": "\"GoodCorp\"",
      "comparison": "Equals"
    },
    {
      "target": "SensorSpace",
      "path": "$.type",
      "value": "\"Venue\"",
      "comparison": "Equals"
    }
  ]
}
```

> [!IMPORTANT]
> - A JSON-elérési utak kis-és nagybetűk.
> - A JSON-adattartalom megegyezik a által visszaadott adattartalommal:
>   - `/sensors/{id}?includes=properties,types`az érzékelőhöz.
>   - `/devices/{id}?includes=properties,types,sensors,sensorsproperties,sensorstypes`az érzékelő fölérendelt eszköze számára.
>   - `/spaces/{id}?includes=properties,types,location,timezone`az érzékelő fölérendelt területéhez.
> - Az összehasonlítás a kis-és nagybetűk megkülönböztetése.

### <a name="user-defined-functions"></a>Felhasználó által meghatározott függvények

A felhasználó által definiált függvény egy elszigetelt Azure digitális Twins-környezetben végrehajtott egyéni függvény. A felhasználó által definiált függvények hozzáférhetnek a nyers érzékelő telemetria-üzeneteihez, amint az beérkezett. A felhasználó által definiált függvények hozzáférhetnek a térbeli gráfhoz és a diszpécser szolgáltatáshoz is. A felhasználó által definiált függvény egy gráfon belüli regisztrálása után létre kell hozni egy Matcher ( [fent](#matcher)részletezett), hogy meg lehessen adni a függvény végrehajtásának időpontját. Ha például az Azure Digital Twins új telemetria kap egy adott érzékelőből, az egyeztetett felhasználó által definiált függvény az utolsó néhány érzékelő beolvasásának mozgóátlagát is kiszámíthatja.

A felhasználó által definiált függvények a JavaScriptben is megírhatók. A segítő módszerek a felhasználó által definiált végrehajtási környezetben működnek a gráfmal. A fejlesztők egyéni kódrészleteket futtathatnak az érzékelő telemetria üzeneteiben. Példák erre vonatkozóan:

- Állítsa az érzékelőt közvetlenül az érzékelő objektumra a gráfon belül.
- Hajtson végre egy műveletet a diagramon belül a különböző szenzorok beolvasása alapján.
- Értesítés létrehozása, ha bizonyos feltételek teljesülnek egy bejövő érzékelő olvasásakor.
- Csatolja a Graph-metaadatokat az érzékelő olvasásához, mielőtt elküld egy értesítést.

További információ: [a felhasználó által definiált függvények használata](./how-to-user-defined-functions.md).


#### <a name="examples"></a>Példák

A [digitális Twins C# minta GitHub](https://github.com/Azure-Samples/digital-twins-samples-csharp/) -tárháza néhány példát tartalmaz a felhasználó által definiált függvényekre:
- [Ez a függvény](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availabilityForTutorial.js) a szén-dioxid, a mozgás és a hőmérséklet értékeit keresi annak megállapítására, hogy a helyiség elérhető-e a tartományon belül ezekkel az értékekkel. A [digitális ikrek számára készült oktatóanyagok](tutorial-facilities-udf.md) részletesebben ismertetik ezt a funkciót. 
- [Ez a függvény](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/multiplemotionsensors.js) több mozgásérzékelőből származó adatra keres, és meghatározza, hogy a szabad terület elérhető-e, ha egyik sem észleli a mozgást. Egyszerűen lecserélheti a gyors útmutatóban vagy az [oktatóanyagokban](tutorial-facilities-setup.md)használt [](quickstart-view-occupancy-dotnet.md)felhasználó által definiált függvényt, ha a fájl megjegyzések szakaszában megemlített módosításokat végez. 



### <a name="role-assignment"></a>Szerepkör-hozzárendelés

A felhasználó által definiált függvény műveletei az Azure Digital Twins [szerepköralapú hozzáférés-vezérlése](./security-role-based-access-control.md) alá esnek, hogy a szolgáltatáson belül is biztonságossá tegye az adataikat. A szerepkör-hozzárendelések meghatározzák, hogy mely felhasználó által definiált függvények rendelkeznek a megfelelő engedélyekkel a térbeli gráf és az entitások használatához. Előfordulhat például, hogy egy felhasználó által definiált függvénynek lehetősége van arra, hogy egy adott lemezterület alapján *hozzon létre*, *Olvasson*, *frissítsen*vagy *töröljön* egy gráf-adattípust. A felhasználó által definiált függvény hozzáférési szintje akkor van bejelölve, ha a felhasználó által definiált függvény megkéri a diagramot az adatra, vagy megkísérli a műveletet. További információ: [szerepköralapú hozzáférés-vezérlés](./security-create-manage-role-assignments.md).

Lehetséges, hogy egy Matcher olyan felhasználó által definiált függvényt aktivál, amely nem rendelkezik szerepkör-hozzárendeléssel. Ebben az esetben a felhasználó által definiált függvény nem tudja beolvasni a gráfból származó összes adatát.

## <a name="next-steps"></a>További lépések

- Ha többet szeretne megtudni arról, hogyan irányíthatja az eseményeket és telemetria üzeneteket más Azure-szolgáltatásoknak, olvassa el az [útválasztási eseményeket és üzeneteket](./concepts-events-routing.md).

- Ha szeretne többet megtudni a megfeleltetések, a felhasználó által definiált függvények és a szerepkör-hozzárendelések létrehozásáról, olvassa el a következő témakört [: útmutató a felhasználó által definiált függvények használatához](./how-to-user-defined-functions.md).

- Tekintse át a [felhasználó által definiált ügyféloldali függvénytár](./reference-user-defined-functions-client-library.md)-referenciát ismertető dokumentációt.

<!-- Images -->
[1]: media/concepts/digital-twins-data-processing-flow.png
[2]: media/concepts/digital-twins-user-defined-functions.png
