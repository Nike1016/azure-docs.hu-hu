---
title: Application Gateway-összetevők
description: Ez a cikk az Application Gateway különböző összetevőivel kapcsolatos információkat tartalmaz
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: d6d7b4cda4bd3b3246b9bc5573246546d8020b38
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68597377"
---
# <a name="application-gateway-components"></a>Application Gateway-összetevők

 Az Application Gateway az ügyfelek számára egyetlen kapcsolódási pontként szolgál. A bejövő alkalmazások forgalmát több háttér-készlet között osztja el, többek között az Azure-beli virtuális gépeket, a virtuálisgép-méretezési csoportokat, a Azure App Servicet és a helyszíni/külső kiszolgálókat. A forgalom terjesztéséhez az Application Gateway számos, a jelen cikkben ismertetett összetevőt használ.

![Az Application gatewayben használt összetevők](./media/application-gateway-components/application-gateway-components.png)

## <a name="frontend-ip-addresses"></a>Előtérbeli IP-címek

Az előtérbeli IP-cím az Application gatewayhöz tartozó IP-cím. Az Application Gateway egy nyilvános IP-címmel, egy magánhálózati IP-címmel vagy mindkettővel is konfigurálható. Az Application Gateway egy nyilvános vagy egy magánhálózati IP-címet támogat. A virtuális hálózatnak és a nyilvános IP-címnek ugyanabban a helyen kell lennie, mint az Application Gateway-nek. A létrehozást követően a rendszer egy előtérbeli IP-címet társít egy figyelőhöz.

### <a name="static-versus-dynamic-public-ip-address"></a>Statikus vagy dinamikus nyilvános IP-cím

Az Azure Application Gateway v2 SKU konfigurálható úgy, hogy mindkét statikus belső IP-címet és statikus nyilvános IP-címet, vagy csak a statikus nyilvános IP-címet támogassa. Nem konfigurálható úgy, hogy csak a statikus belső IP-címet támogassa.

A v1 SKU beállítható úgy, hogy támogassa a statikus belső IP-címet és a dinamikus nyilvános IP-címet, csak a statikus belső IP-címet, vagy csak a dinamikus nyilvános IP-címet, vagy csak a dinamikus magánhálózati IP-címet vagy a dinamikus nyilvános IP-címet, valamint a dinamikus magánhálózati IP A Application Gateway dinamikus IP-címe nem változik egy futó átjárón. Csak az átjáró leállításakor vagy indításakor lehet megváltoztatni. Nem változtatja meg a rendszerhibákat, a frissítéseket, az Azure-gazdagépek frissítéseit stb. 

Az Application gatewayhez társított DNS-név nem változik az átjáró életciklusa során. Ennek eredményeképpen CNAME aliast kell használnia, és az Application Gateway DNS-címeként kell mutatnia.

## <a name="listeners"></a>Figyelők

A figyelő egy logikai entitás, amely ellenőrzi a bejövő kapcsolatok kéréseit. A figyelő fogad egy kérelmet, ha a kérelemhez társított protokoll, port, állomás és IP-cím megegyezik a figyelő konfigurációjával társított elemekkel.

Az Application Gateway használata előtt legalább egy figyelőt hozzá kell adnia. Az Application gatewayhez több figyelő is tartozhat, és ezekhez a protokollhoz is használható.

Miután egy figyelő észleli az ügyfelektől érkező beérkező kéréseket, az Application Gateway ezeket a kéréseket a háttér-készlet tagjaihoz irányítja. Az Application Gateway a beérkező kérést fogadó figyelőhöz megadott útválasztási szabályokat használja.

A figyelők a következő portokat és protokollokat támogatják.

### <a name="ports"></a>Portok

Egy port, ahol a figyelő figyeli az ügyfél kérelmét. Az 1 és 65502 közötti portok a v2 SKU-hoz készült v1 SKU és 1 – 65199 között állíthatók be.

### <a name="protocols"></a>Protokollok

A Application Gateway négy protokollt támogat: HTTP, HTTPS, HTTP/2 és WebSocket:

- A figyelő konfigurációjában adjon meg a HTTP és a HTTPS protokoll közötti értéket.
- A WebSockets [és a http/2 protokollok](https://docs.microsoft.com/azure/application-gateway/overview#websocket-and-http2-traffic) támogatása natív módon történik, és a WebSocket- [támogatás](https://docs.microsoft.com/azure/application-gateway/application-gateway-websocket) alapértelmezés szerint engedélyezve van. Kizárólag WebSocket-támogatásra vonatkozó felhasználói beállítás nem létezik. Websocketek használata HTTP-és HTTPS-figyelővel.
- A HTTP/2 protokoll támogatása csak az Application Gateway-figyelőkhöz csatlakozó ügyfelek számára érhető el. A háttér-kiszolgálói készletekkel való kommunikáció HTTP/1.1-n keresztül történik. Alapértelmezés szerint a HTTP/2 támogatás le van tiltva. Dönthet úgy is, hogy engedélyezi.

HTTPS-figyelő használata az SSL-lezáráshoz. Egy HTTPS-figyelő kiszervezi a titkosítást és a visszafejtést az Application Gateway felé, így a webkiszolgálók terhelése nem terheli a terhelést. Az alkalmazások Ezután szabadon az üzleti logikára koncentrálnak.

### <a name="custom-error-pages"></a>Egyéni hibalapok

Application Gateway lehetővé teszi, hogy egyéni hibaüzeneteket hozzon létre az alapértelmezett hibaüzenetek megjelenítése helyett. Az egyéni hibaoldalakon feltüntetheti saját védjegyeit, és egyéni elrendezést használhat. Application Gateway egy egyéni hibaüzenetet jelenít meg, ha egy kérelem nem tudja elérni a hátteret.

További információ: [az Application Gateway egyéni hibáinak lapja](https://docs.microsoft.com/azure/application-gateway/custom-error).

### <a name="types-of-listeners"></a>Figyelők típusai

Kétféle figyelő létezik:

- Alapszintű. Ez a típusú figyelő egyetlen tartományi helyet figyel, ahol egyetlen DNS-hozzárendelés van az Application Gateway IP-címéhez. Ez a figyelő-konfiguráció akkor szükséges, ha egyetlen helyet üzemeltet az Application Gateway mögött.

- **Több hely**. Ez a figyelő-konfiguráció akkor szükséges, ha egynél több webalkalmazást konfigurál ugyanazon az Application Gateway-példányon. Lehetővé teszi, hogy hatékonyabb topológiát konfiguráljon az üzemelő példányokhoz, ha akár 100 webhelyet ad hozzá egy Application gatewayhez. Mindegyik webhelyet a saját háttérkészletéhez lehet irányítani. Például három altartomány, abc.contoso.com, xyz.contoso.com és pqr.contoso.com, az Application Gateway IP-címére mutat. Hozzon létre három többhelyes figyelőt, és konfigurálja az egyes figyelőket a megfelelő port és protokoll beállításhoz.

    További információ: [több hely üzemeltetése](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview).

A figyelő létrehozása után társítsa azt egy kérelem-útválasztási szabállyal. Ez a szabály határozza meg, hogy a figyelőre érkező kérés hogyan legyen átirányítva a háttérbe.

Application Gateway a figyelőket a megjelenő sorrendben dolgozza fel. Ha az alapszintű figyelő megfelel egy bejövő kérelemnek, akkor a rendszer először dolgozza fel. Ha a forgalmat a megfelelő háttérre irányítja, állítson be egy többhelyes figyelőt egy alapszintű figyelő előtt.

## <a name="request-routing-rules"></a>Kérelmek útválasztási szabályai

A kérelem-útválasztási szabály az Application Gateway egyik fő összetevője, mert meghatározza, hogyan irányíthatja át a forgalmat a figyelőn. A szabály köti a figyelőt, a háttér-kiszolgáló készletet és a háttérbeli HTTP-beállításokat.

Amikor egy figyelő fogad egy kérelmet, a kérések útválasztási szabálya továbbítja a kérést a háttérnek, vagy máshová irányítja át. Ha a kérést továbbítják a háttérbe, a kérelem útválasztási szabálya határozza meg, hogy melyik háttér-kiszolgáló készletet szeretné továbbítani. Emellett a kérelem útválasztási szabálya azt is meghatározza, hogy a kérésben szereplő fejléceket át kell-e írni. Egy figyelő egyetlen szabályhoz is csatolható.

A kérések útválasztási szabályainak két típusa létezik:

- Alapszintű. A társított figyelőn (például blog.contoso.com/*) lévő összes kérelem a társított backend-készletbe van továbbítva a kapcsolódó HTTP-beállítás használatával.

- **Elérésiút-alapú**. Ez az útválasztási szabály lehetővé teszi, hogy a kérelemben szereplő URL-cím alapján a társított figyelőn keresztül átirányítsa a kéréseket egy adott háttér-készletre. Ha egy kérelem URL-címének elérési útja megegyezik egy elérésiút-alapú szabályban szereplő elérésiút-mintával, a szabály a kérést irányítja át. Az elérésiút-mintát csak az URL-címre alkalmazza, nem pedig a lekérdezési paraméterekre. Ha a figyelőre vonatkozó kérelem URL-címének elérési útja nem egyezik az elérésiút-alapú szabályokkal, a kérést az alapértelmezett háttér-készletre és a HTTP-beállításokra irányítja át.

További információ: [URL-alapú útválasztás](https://docs.microsoft.com/azure/application-gateway/url-route-overview).

### <a name="redirection-support"></a>Átirányítási támogatás

A kérések útválasztási szabálya lehetővé teszi az Application Gateway forgalmának átirányítását is. Ez egy általános átirányítási mechanizmus, amellyel bármely, a szabályok segítségével meghatározott portról átirányíthatja a t.

Kiválaszthatja, hogy az átirányítási cél egy másik figyelő legyen (amely lehetővé teszi az automatikus HTTP-t a HTTPS-átirányításhoz) vagy egy külső helyet. Dönthet úgy is, hogy az átirányítás átmenetileg vagy állandó, vagy az átirányított URL-címhez hozzáfűzi az URI elérési utat és a lekérdezési karakterláncot.

További információ: [forgalom átirányítása az Application Gateway](https://docs.microsoft.com/azure/application-gateway/redirect-overview)-ben.

### <a name="rewrite-http-headers"></a>HTTP-fejlécek átírása

A kérelmek útválasztási szabályainak használatával hozzáadhat, eltávolíthat vagy frissíthet HTTP (S) kéréseket és válasz fejléceket, mivel a kérelmek és válaszok csomagjai az Application Gateway segítségével az ügyfél és a háttérbeli készletek között mozognak.

A fejlécek statikus értékekre vagy más fejlécekre és kiszolgálói változókra is állíthatók. Ez segíti a fontos használati eseteket, például az ügyfél IP-címeinek kinyerését, a háttér bizalmas adatainak eltávolítását, a nagyobb biztonság hozzáadását stb.

További információt a HTTP- [fejlécek újraírása az Application Gateway](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)-ben című témakörben talál.

## <a name="http-settings"></a>HTTP-beállítások

Az Application Gateway átirányítja a forgalmat a háttér-kiszolgálókra (a HTTP-beállításokat tartalmazó kérelem-útválasztási szabályban meghatározottak szerint) a portszám, a protokoll és az ebben az összetevőben részletezett egyéb beállítások használatával.

A HTTP-beállításokban használt port és protokoll határozza meg, hogy az Application Gateway és a háttérrendszer-kiszolgálók közötti forgalom titkosítva van-e (végpontok közötti SSL-t biztosítva) vagy titkosítatlan.

Ez az összetevő a következőket is használja:

- Állapítsa meg, hogy a felhasználói munkamenetet ugyanazon a kiszolgálón kell-e tárolni a [cookie-alapú munkamenet-affinitás](https://docs.microsoft.com/azure/application-gateway/overview#session-affinity)használatával.

- A háttérbeli készlet tagjainak biztonságos eltávolítása a [kapcsolatok kiürítésével](https://docs.microsoft.com/azure/application-gateway/overview#connection-draining).

- Rendeljen egyéni mintavételt a háttér állapotának figyeléséhez, állítsa be a kérés időkorlátját, felülbírálja az állomásnév és elérési út értékét a kérelemben, és egy kattintással könnyedén megadhatja a App Service háttér beállításait.

## <a name="backend-pools"></a>Háttérkészletek

A háttér-készlet átirányítja a kérést a háttér-kiszolgálókra. A háttér-készletek a következőket tartalmazhatják:

- Hálózati adapterek (NIC-k)
- Virtuálisgép-méretezési csoportok
- Nyilvános IP-címek
- Belső IP-címek
- TELJES TARTOMÁNYNÉV
- Több-bérlős háttérrendszer (például App Service)

Application Gateway háttérbeli készlet tagjai nem kapcsolódnak rendelkezésre állási csoportokhoz. Az Application Gateway képes kommunikálni a virtuális hálózatán kívüli példányokkal. Ennek eredményeképpen a háttér-készletek tagjai többek között fürtök, adatközpontok vagy az Azure-on kívül is lehetnek, feltéve, hogy IP-kapcsolat van.

Ha belső IP-címeket használ a háttérbeli készlet tagjaiként, [virtuális hálózati](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) társítást vagy [VPN-átjárót](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)kell használnia. A virtuális hálózat társítása támogatott és előnyös a más virtuális hálózatok terheléselosztási forgalmához.

Az Application Gateway képes kommunikálni a helyszíni kiszolgálókkal is, amikor az Azure ExpressRoute vagy VPN-alagutak csatlakoznak, ha a forgalom engedélyezett.

Különböző háttér-készleteket hozhat létre különböző típusú kérelmekhez. Hozzon létre például egy háttér-készletet az általános kérelmekhez, majd egy másik háttér-készletet az alkalmazáshoz tartozó Service-szolgáltatásokhoz intézett kérésekhez.

## <a name="health-probes"></a>Állapotadat-mintavételek

Alapértelmezés szerint az Application Gateway a háttér-készlet összes erőforrásának állapotát figyeli, és automatikusan eltávolítja a nem megfelelő állapotú fájlokat. Ezután figyeli a nem megfelelő állapotú példányokat, és visszaadja azokat az egészséges háttérrendszer-készlethez, amikor elérhetővé válnak, és reagálnak az állapotra.

Amellett, hogy az alapértelmezett állapot mintavételi figyelését is használja, testre is szabhatja az állapot-mintavételt az alkalmazás követelményeinek megfelelően. Az egyéni mintavételek részletesebb szabályozást tesznek lehetővé az állapot figyelése során. Az egyéni mintavételek használatakor beállíthatja a mintavételi intervallumot, a teszt URL-címét és elérési útját, valamint azt, hogy hány sikertelen választ fogadjon el a rendszer, mielőtt a háttér-készlet példánya nem megfelelőként van megjelölve. Javasoljuk, hogy az egyes háttérrendszer-készletek állapotának figyeléséhez egyéni mintavételt állítson be.

További információ: [az Application Gateway állapotának figyelése](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview).

## <a name="next-steps"></a>További lépések

Application Gateway létrehozása:

* [A Azure Portal](quick-create-portal.md)
* [Azure PowerShell használatával](quick-create-powershell.md)
* [Az Azure CLI használatával](quick-create-cli.md)
