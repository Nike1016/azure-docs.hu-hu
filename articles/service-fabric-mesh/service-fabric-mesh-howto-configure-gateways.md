---
title: Átjáró konfigurálása a kérelmek továbbításához | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhatja az átjárót, amely a Service Fabric rácsvonalon futó alkalmazás (ok) bejövő forgalmát kezeli.
services: service-fabric-mesh
documentationcenter: .net
author: dkkapur
manager: chakdan
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/28/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: b4fc6f91ee2429205974b9cb7ceb05b7cff53f15
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69034213"
---
# <a name="configure-a-gateway-resource-to-route-requests"></a>Átjáró erőforrásának konfigurálása a kérelmek továbbításához

Az átjáró erőforrás a bejövő forgalom útválasztására szolgál az alkalmazást tároló hálózatra. Konfigurálja úgy, hogy meghatározza azokat a szabályokat, amelyeken keresztül a kérelmeket a kérés szerkezete alapján adott szolgáltatásokhoz vagy végpontokhoz irányítja a rendszer. A Mesh hálózatokkal és átjárókkal kapcsolatos további információkért lásd: a [Service Fabric Mesh hálózatkezelésének bemutatása](service-fabric-mesh-networks-and-gateways.md) . 

Az átjáró erőforrásait a központi telepítési sablon (JSON vagy YAML) részeként kell deklarálni, és a hálózati erőforrástól függ. Ez a dokumentum az átjáróhoz beállítható különböző tulajdonságokat, valamint a minta-átjáró konfigurációját ismerteti.

## <a name="options-for-configuring-your-gateway-resource"></a>Az átjáró erőforrásának konfigurálási lehetőségei

Mivel az átjáró-erőforrás hidat szolgál az alkalmazás hálózata és a mögöttes infrastruktúra hálózata (a `open` hálózat) között. Csak az egyiket kell konfigurálnia (a rácsvonalak előnézetében az alkalmazás legfeljebb egy átjárót tartalmazhat). Az erőforrás deklarációja két fő részből áll: az erőforrás-metaadatokból és a tulajdonságokból. 

### <a name="gateway-resource-metadata"></a>Átjáró erőforrás-metaadatai

Az átjáró a következő metaadatokkal van deklarálva:
* `apiVersion`-a következőre kell állítani: "2018-09-01-Preview" (vagy újabb, a jövőben)
* `name`– az átjáró sztring-neve
* `type`-"Microsoft. ServiceFabricMesh/Gateways"
* `location`-be kell állítani az alkalmazás/hálózat helyét; általában az üzemelő példányban található Location paraméterre mutató hivatkozás lesz.
* `dependsOn`– az a hálózat, amelyhez ez az átjáró a bejövő forgalom pontként fog szolgálni

A következőképpen néz ki egy Azure Resource Manager (JSON) telepítési sablon: 

```json
{
  "apiVersion": "2018-09-01-preview",
  "name": "myGateway",
  "type": "Microsoft.ServiceFabricMesh/gateways",
  "location": "[parameters('location')]",
  "dependsOn": [
    "Microsoft.ServiceFabricMesh/networks/myNetwork"
  ],
  "properties": {
    [...]
  }
}
```

### <a name="gateway-properties"></a>Átjáró tulajdonságai

A Properties (Tulajdonságok) szakasz azokat a hálózatokat határozza meg, amelyek között az átjáró található, valamint az útválasztási kérelmek szabályait. 

#### <a name="source-and-destination-network"></a>Forrás és cél hálózat 

Minden átjáróhoz a `sourceNetwork` és `destinationNetwork`a szükséges. A forrásoldali hálózat azt a hálózatot határozza meg, amelyről az alkalmazás fogadja a bejövő kérelmeket. A name tulajdonságot mindig "Open" értékre kell beállítani. A célként megadott hálózat a kérelmek célcsoportját képező hálózat. Ennek a név értékét az alkalmazás helyi hálózatának erőforrás-nevére kell beállítani (tartalmaznia kell az erőforrás teljes hivatkozását). Tekintse meg az alábbi, a "myNetwork" nevű hálózaton üzemelő példányhoz hasonló minta konfigurációját.

```json 
"properties": {
  "description": "Service Fabric Mesh Gateway",
  "sourceNetwork": {
    "name": "Open"
  },
  "destinationNetwork": {
    "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'myNetwork')]"
  },
  [...]
}
```

#### <a name="rules"></a>Szabályok 

Az átjárók több útválasztási szabállyal is rendelkezhetnek, amelyek megadják a bejövő forgalom kezelésének módját. Az útválasztási szabályok határozzák meg a figyelési port és a célként megadott végpont közötti kapcsolatot egy adott alkalmazás esetében. A TCP-útválasztási szabályok esetében 1:1 a port: végpont közötti leképezés. A HTTP-útválasztási szabályok esetében olyan összetettebb útválasztási szabályokat állíthat be, amelyek megvizsgálják a kérelem elérési útját, és opcionálisan fejléceket is, hogy eldöntsék, hogyan irányítja a rendszer a kérést. 

Az útválasztási szabályok a portszám alapján vannak megadva. Az egyes bejövő portok az átjáró konfigurációjának tulajdonságok szakaszában találhatók a szabályok saját tömbje. 

#### <a name="tcp-routing-rules"></a>TCP-útválasztási szabályok 

A TCP-útválasztási szabályok a következő tulajdonságokből állnak: 
* `name`-hivatkozás arra a szabályra, amely bármilyen tetszőleges sztring lehet 
* `port`-a bejövő kérelmek figyelésének portja 
* `destination`-a végpont specifikációja `applicationName`, `serviceName`amely tartalmazza `endpointName`a, a és a rendszerét, ahol a kérelmeket át kell irányítani a következőre:

Íme egy példa a TCP-útválasztási szabályra:

```json
"properties": {
  [...]
  "tcp": [
    {
      "name": "web",
      "port": 80,
      "destination": {
        "applicationName": "myApp",
        "serviceName": "myService",
        "endpointName": "myListener"
      }
    }
  ]
}
```


#### <a name="http-routing-rules"></a>HTTP-útválasztási szabályok 

A HTTP-útválasztási szabályok a következő tulajdonságokből állnak: 
* `name`-hivatkozás arra a szabályra, amely bármilyen tetszőleges sztring lehet 
* `port`-a bejövő kérelmek figyelésének portja 
* `hosts`– a fent megadott porton a különböző gazdagépekre érkező kérésekre vonatkozó szabályzatok tömbje. A gazdagépek azon alkalmazások és szolgáltatások összessége, amelyek futhatnak a hálózaton, és képesek a bejövő kérések, például egy webalkalmazás kiszolgálására. A gazdagép-házirendeket a rendszer sorrendben értelmezi, ezért a következőt kell létrehoznia a specificitás csökkenő szintjein.
    * `name`– annak a gazdagépnek a DNS-neve, amelyre a következő útválasztási szabályok vannak megadva. A "*" használata itt az összes gazdagép útválasztási szabályait hozza létre.
    * `routes`– az adott gazdagéphez tartozó szabályzatok tömbje
        * `match`-az ehhez a szabályhoz tartozó bejövő kérelmek struktúrájának megadása, a következők alapján:`path`
            * `path`-tartalmaz egy `value` (bejövő URI-t `rewrite` ), (hogyan szeretné továbbítani a kérést) és a `type` (jelenleg csak "előtag" lehet)
            * `header`– a fejlécek azon értékeinek opcionális tömbje, amelyek egyeznek a kérelem fejlécében szereplő értékekkel, ha a kérelem megfelel az elérésiút-specifikációnak (fenti).
              * mindegyik bejegyzés tartalmazza `name` (a megegyező fejléc karakterlánc-nevét), `value` a (a kérelem fejlécének karakterlánc-értékét), a `type` (jelenleg csak "pontos" lehet)
        * `destination`-Ha a kérelem megfelel, a rendszer átirányítja erre a célhelyre, amely az a `applicationName`, `serviceName`a és a használatával van megadva.`endpointName`

Íme egy példa a HTTP-útválasztási szabályra, amely az 80-es portra érkező kérésekre vonatkozik az ezen a hálózaton lévő alkalmazások által szolgáltatott összes gazdagépre. Ha a kérelem URL-címe olyan szerkezettel rendelkezik, amely megfelel az elérésiút- `<IPAddress>:80/pickme/<requestContent>`specifikációnak, azaz a, a `myListener` végpontra lesz irányítva.  

```json
"properties": {
  [...]
  "http": [
    {
      "name": "web",
      "port": 80,
      "hosts": [
        {
          "name": "*",
          "routes": [
            {
              "match": {
                "path": {
                  "value": "/pickme",
                  "rewrite": "/",
                  "type": "Prefix"
                }
              },
              "destination": {
                "applicationName": "meshApp",
                "serviceName": "myService",
                "endpointName": "myListener"
              }
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="sample-config-for-a-gateway-resource"></a>Egy átjáró erőforrásának mintája 

Az átjárók teljes erőforrás-konfigurációját a következőképpen tekintheti meg: (ez a [háló minták](https://github.com/Azure-Samples/service-fabric-mesh/blob/2018-09-01-preview/templates/ingress/meshingress.linux.json)tárházában elérhető bejövő minta alapján módosítható):

```json
{
  "apiVersion": "2018-09-01-preview",
  "name": "ingressGatewayLinux",
  "type": "Microsoft.ServiceFabricMesh/gateways",
  "location": "[parameters('location')]",
  "dependsOn": [
    "Microsoft.ServiceFabricMesh/networks/meshNetworkLinux"
  ],
  "properties": {
    "description": "Service Fabric Mesh Gateway for Linux mesh samples.",
    "sourceNetwork": {
      "name": "Open"
    },
    "destinationNetwork": {
      "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'meshNetworkLinux')]"
    },
    "http": [
      {
        "name": "web",
        "port": 80,
        "hosts": [
          {
            "name": "*",
            "routes": [
              {
                "match": {
                  "path": {
                    "value": "/hello",
                    "rewrite": "/",
                    "type": "Prefix"
                  }
                },
                "destination": {
                  "applicationName": "meshAppLinux",
                  "serviceName": "helloWorldService",
                  "endpointName": "helloWorldListener"
                }
              },
              {
                "match": {
                  "path": {
                    "value": "/counter",
                    "rewrite": "/",
                    "type": "Prefix"
                  }
                },
                "destination": {
                  "applicationName": "meshAppLinux",
                  "serviceName": "counterService",
                  "endpointName": "counterServiceListener"
                }
              }
            ]
          }
        ]
      }
    ]
  }
}
```

Ez az átjáró egy "meshAppLinux" nevű Linux-alkalmazáshoz van konfigurálva, amely legalább két szolgáltatásból áll: "helloWorldService" és "counterService", amely a 80-es portot figyeli. A bejövő kérelem URL-struktúrájától függően a rendszer továbbítja a kérést ezen szolgáltatások egyikére. 
* Az\<"IP_cím >: 80/helloWorld\</\>Request" utasítás egy kérést eredményez a helloWorldService "helloWorldListener". 
* "\<Az IP_cím >: 80/Counter\</\>Request" utasítás egy kérést eredményez a counterService "counterListener". 

## <a name="next-steps"></a>További lépések
* A [bejövő minták](https://github.com/Azure-Samples/service-fabric-mesh/tree/2018-09-01-preview/templates/ingress) üzembe helyezése az átjárók működés közbeni megtekintéséhez
