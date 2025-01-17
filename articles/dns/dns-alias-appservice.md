---
title: Elosztott terhelésű Azure Web Apps a zóna csúcsán
description: Azure DNS alias rekord használata elosztott terhelésű webalkalmazások üzemeltetéséhez a zóna csúcsán
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 08/10/2019
ms.author: victorh
ms.openlocfilehash: 4f9a42f3d054becfed0b0a6acbf92cdf1e421c16
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946948"
---
# <a name="host-load-balanced-azure-web-apps-at-the-zone-apex"></a>Elosztott terhelésű Azure Web Apps a zóna csúcsán

A DNS protokoll megakadályozza, hogy a zóna csúcsán lévő egy vagy AAAA típusú rekord ne legyen hozzárendelve. Egy példa zóna csúcsa a contoso.com. Ez a korlátozás olyan alkalmazás-tulajdonosokkal kapcsolatos problémát jelent, akik Traffic Manager mögötti elosztott terhelésű alkalmazásokkal rendelkeznek. Nem lehetséges a zóna APEX-rekord Traffic Manager profiljára mutatni. Ennek eredményeképpen az alkalmazás tulajdonosai megkerülő megoldást kell használniuk. Az alkalmazási réteg átirányítását át kell irányítani a zóna csúcsáról egy másik tartományba. Ilyen például a contoso.com és a www\.contoso.com közötti átirányítás. Ez a megoldás az átirányítási függvény egyetlen meghibásodási pontját mutatja be.

Alias-rekordokkal ez a probléma már nem létezik. Az alkalmazások tulajdonosai a zóna csúcspont-rekordját egy olyan Traffic Manager-profilra helyezhetik, amely külső végpontokkal rendelkezik. Az alkalmazások tulajdonosai ugyanarra a Traffic Manager-profilra mutatnak, amelyet a DNS-zónán belüli bármely más tartományhoz is használnak.

Például a contoso.com és a www\.contoso.com ugyanarra a Traffic Manager profilra mutathat. Ebben az esetben, ha a Traffic Manager profilhoz csak külső végpontok vannak konfigurálva.

Ebből a cikkből megtudhatja, hogyan hozhat létre alias-rekordot a tartományhoz, és hogyan konfigurálhatja Traffic Manager profiljának végpontját a webalkalmazásokhoz.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Elérhetőnek kell lennie egy tartománynévnek, amelyet üzemeltethet az Azure DNS-ben a teszteléshez. Teljes körű irányítással kell rendelkeznie a tartomány felett. A teljes körű irányításba beletartozik a tartomány névkiszolgálói (NS-) rekordjainak beállítására való képesség.

A tartomány Azure DNSban történő üzemeltetésével kapcsolatos utasításokért [lásd: oktatóanyag: A tartomány üzemeltetése Azure DNS](dns-delegate-domain-azure-dns.md).

Az ebben az oktatóanyagban használt példatartománynév a contoso.com, de Ön használja a saját tartománynevét.

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot, amely az ebben a cikkben használt összes erőforrást tárolni fogja.

## <a name="create-app-service-plans"></a>App Service csomagok létrehozása

Hozzon létre két webes App Service sémát az erőforráscsoporthoz az alábbi táblázat alapján a konfigurációs információkhoz. App Service terv létrehozásával kapcsolatos további információkért lásd: [app Service-terv kezelése az Azure-ban](../app-service/app-service-plan-manage.md).


|Name (Név)  |Operációs rendszer  |Location  |Tarifacsomag  |
|---------|---------|---------|---------|
|ASP-01     |Windows|East US|Fejlesztési/tesztelési D1 – közös|
|ASP-02     |Windows|USA középső régiója|Fejlesztési/tesztelési D1 – közös|

## <a name="create-app-services"></a>App Services létrehozása

Hozzon létre két webalkalmazást, egyet az egyes App Service-csomagokban.

1. Az Azure Portal lap bal felső sarkában válassza az **erőforrás létrehozása**lehetőséget.
2. Írja be a webalkalmazás kifejezést a keresősávba, majd nyomja le az ENTER billentyűt.
3. Válasszaa webalkalmazás lehetőséget.
4. Kattintson a **Létrehozás** gombra.
5. Fogadja el az alapértelmezett értékeket, és a következő táblázat segítségével konfigurálja a két webalkalmazást:

   |Name (Név)<br>(a. azurewebsites.net belül egyedinek kell lennie)|Erőforráscsoport |Futásidejű verem|Régió|App Service csomag/hely
   |---------|---------|-|-|-------|
   |App-01|Meglévő használata<br>Válassza ki az erőforráscsoportot|.NET Core 2.2|East US|ASP-01 (D1)|
   |App-02|Meglévő használata<br>Válassza ki az erőforráscsoportot|.NET Core 2.2|USA középső régiója|ASP-02 (D1)|

### <a name="gather-some-details"></a>Részletek összegyűjtése

Most fel kell jegyeznie a webalkalmazások IP-címét és állomásnevét.

1. Nyissa meg az erőforráscsoportot, és válassza ki az első webalkalmazását (ebben a példában az**app-01** fájl).
2. A bal oldali oszlopban válassza a **Tulajdonságok**lehetőséget.
3. Jegyezze fel az **URL**-cím alatti címet, és a **kimenő IP-címek** területen jegyezze fel a lista első IP-címét. Ezt az információt később a Traffic Manager végpontok konfigurálásakor fogja használni.
4. Ismételje meg az **app-02-** et.

## <a name="create-a-traffic-manager-profile"></a>Traffic Manager-profil létrehozása

Hozzon létre egy Traffic Manager profilt az erőforráscsoporthoz. Használja az alapértelmezett értékeket, és adjon meg egy egyedi nevet a trafficmanager.net névtérben.

Traffic Manager profil létrehozásával kapcsolatos információkért lásd: gyors [útmutató: Hozzon létre egy Traffic Manager profilt egy magasan elérhető webalkalmazáshoz](../traffic-manager/quickstart-create-traffic-manager-profile.md).

### <a name="create-endpoints"></a>Végpontok létrehozása

Most már létrehozhatja a két webalkalmazáshoz tartozó végpontokat.

1. Nyissa meg az erőforráscsoportot, és válassza ki a Traffic Manager-profilt.
2. A bal oldali oszlopban válassza a **végpontok**lehetőséget.
3. Válassza a **Hozzáadás** lehetőséget.
4. A végpontok konfigurálásához használja a következő táblázatot:

   |Type  |Name (Név)  |Target  |Location  |Egyéni fejléc beállításai|
   |---------|---------|---------|---------|---------|
   |Külső végpont     |End-01|Az App-01-ben rögzített IP-cím|East US|gazdagép:\<az App-01-hez rögzített URL-cím\><br>Példa: **Host: app-01.azurewebsites.net**|
   |Külső végpont     |End-02|Az App-02-hez rögzített IP-cím|USA középső régiója|gazdagép:\<az App-02-hez rögzített URL-cím\><br>Példa: **Host: app-02.azurewebsites.net**

## <a name="create-dns-zone"></a>DNS-zóna létrehozása

Egy meglévő DNS-zónát is használhat teszteléshez, vagy létrehozhat egy új zónát is. Új DNS-zóna létrehozásához és delegálásához az Azure [-ban: oktatóanyag: A tartomány üzemeltetése Azure DNS](dns-delegate-domain-azure-dns.md).

## <a name="add-a-txt-record-for-custom-domain-validation"></a>TXT-rekord hozzáadása az egyéni tartomány érvényesítéséhez

Ha egyéni állomásnevet ad hozzá a webalkalmazásokhoz, az egy adott TXT-rekordot fog keresni a tartomány érvényesítéséhez.

1. Nyissa meg az erőforráscsoportot, és válassza ki a DNS-zónát.
2. Válassza a **Rekordhalmaz** elemet.
3. Adja hozzá a rekordot a következő táblázat használatával. Az értéknél használja a korábban rögzített webalkalmazás URL-címét:

   |Name (Név)  |Típus  |Value|
   |---------|---------|-|
   |@     |TXT|App-01.azurewebsites.net|


## <a name="add-a-custom-domain"></a>Egyéni tartomány hozzáadása

Adjon hozzá egy egyéni tartományt mindkét webalkalmazáshoz.

1. Nyissa meg az erőforráscsoportot, és válassza ki az első webalkalmazását.
2. A bal oldali oszlopban válassza az **Egyéni tartományok**elemet.
3. Az **Egyéni tartományok**területen válassza az **egyéni tartomány hozzáadása**elemet.
4. Az **egyéni tartomány**területen adja meg az egyéni tartomány nevét. Például: contoso.com.
5. Válassza az **Érvényesítés** lehetőséget.

   A tartománynak át kell adnia az érvényesítést, és zöld pipa jeleket kell megadnia az **állomásnév elérhetősége** és a **tartomány tulajdonjoga**mellett.
5. Válassza az **Egyéni tartomány hozzáadása** lehetőséget.
6. Ha meg szeretné tekinteni az új gazdagépet a **helyhez rendelt állomásnevek**alatt, frissítse a böngészőt. A lapon lévő frissítés nem mindig azonnal mutatja a módosításokat.
7. Ismételje meg ezt az eljárást a második webalkalmazás esetében.

## <a name="add-the-alias-record-set"></a>Az alias-rekord hozzáadása

Most adjon hozzá egy alias-rekordot a zóna csúcsához.

1. Nyissa meg az erőforráscsoportot, és válassza ki a DNS-zónát.
2. Válassza a **Rekordhalmaz** elemet.
3. Adja hozzá a rekordot a következő táblázat használatával:

   |Name (Név)  |Type  |Alias-rekord készlete  |Alias típusa  |Azure-erőforrás|
   |---------|---------|---------|---------|-----|
   |@     |J|Igen|Azure-erőforrás|Traffic Manager – a profil|


## <a name="test-your-web-apps"></a>Webes alkalmazások tesztelése

Most már ellenőrizheti, hogy elérhető-e a webalkalmazás, és hogy terheléselosztás alatt áll-e.

1. Nyisson meg egy webböngészőt, és keresse meg a tartományt. Például: contoso.com. Ekkor megjelenik az alapértelmezett webalkalmazás lap.
2. Állítsa le az első webalkalmazást.
3. Zárjunk be egy webböngészőt, és várjon néhány percet.
4. Indítsa el a böngészőt, és keresse meg a tartományt. Az alapértelmezett webalkalmazás lap továbbra is megjelenik.
5. Állítsa le a második webalkalmazást.
6. Zárjunk be egy webböngészőt, és várjon néhány percet.
7. Indítsa el a böngészőt, és keresse meg a tartományt. A 403-es hiba jelenik meg, amely azt jelzi, hogy a webalkalmazás leáll.
8. Indítsa el a második webalkalmazást.
9. Zárjunk be egy webböngészőt, és várjon néhány percet.
10. Indítsa el a böngészőt, és keresse meg a tartományt. Ekkor meg kell jelennie az alapértelmezett webalkalmazás-oldalnak.

## <a name="next-steps"></a>További lépések

Az alias-rekordokkal kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

- [Oktatóanyag: Alias-rekord konfigurálása egy Azure nyilvános IP-címre való hivatkozáshoz](tutorial-alias-pip.md)
- [Oktatóanyag: Alias-rekord konfigurálása a APEX tartománynevek támogatásához Traffic Manager](tutorial-alias-tm.md)
- [DNS – gyakori kérdések](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)

Az aktív DNS-nevek áttelepítésének megismeréséhez tekintse meg [az aktív DNS-név](../app-service/manage-custom-dns-migrate-domain.md)áttelepítését Azure app Servicere című témakört.
