---
title: Azure Database for MySQL a kapcsolódó adatbázis-szolgáltatás áttekintése
description: A Azure Database for MySQL a kapcsolódó adatbázis-szolgáltatás áttekintése.
ms.service: mysql
author: ajlam
ms.author: andrela
ms.custom: mvc
ms.topic: conceptual
ms.date: 08/13/2019
ms.openlocfilehash: 1299f82c28181d1c24f68a0d36ba13c6b40d21ee
ms.sourcegitcommit: fe50db9c686d14eec75819f52a8e8d30d8ea725b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/14/2019
ms.locfileid: "69013406"
---
# <a name="what-is-azure-database-for-mysql"></a>Mi az Azure Database for MySQL?

A Azure Database for MySQL a Microsoft Cloud-on alapuló, a [MySQL Community Edition](https://www.mysql.com/products/community/) (a GPLv2 licenc alatt elérhető) adatbázismotor, a 5,6, a 5,7 és a 8,0 verziók alapján készült, a Microsoft felhőben található, Azure Database for MySQL a következőket biztosítja:

- Beépített magas rendelkezésre állás további költség nélkül.
- Kiszámítható teljesítmény, használatalapú díjszabással.
- Méretezés igény szerint másodpercek alatt.
- A helyi és mozgó bizalmas adatok védelme.
- Automatikus biztonsági mentések és időponthoz kötött visszaállítás akár 35 napig.
- Vállalati szintű biztonság és megfelelőség.

Ezen képességek szinte semmilyen felügyeletet nem igényelnek, és mindegyik további költség nélkül érhető el. Segítségükkel az alkalmazások gyors fejlesztésére és forgalomba hozásának felgyorsítására összpontosíthat ahelyett, hogy értékes időt és erőforrásokat kellene lefoglalnia a virtuális gépek és infrastruktúra kezeléséhez. Ezen kívül folytathatja az alkalmazás fejlesztését a kívánt nyílt forráskódú eszközökkel és platformmal, és ezeket az üzletvitel által igényelt sebességgel és hatékonysággal készítheti el, új készségek elsajátításának szükségessége nélkül.

![Azure Database for MySQL fogalmi diagram](media/overview/1-azure-db-for-mysql-conceptual-diagram.png)

Ez a Azure Database for MySQL cikk a teljesítménygel, a méretezhetőséggel és a kezelhetőséggel kapcsolatos alapfogalmakat és funkciókat mutatja be, amelyek a részletekre mutató hivatkozásokat tartalmaznak. Tekintse meg ezeket a rövid útmutatókat a kezdéshez:

- [Azure-adatbázis létrehozása MySQL-kiszolgálóhoz az Azure Portal használatával](quickstart-create-mysql-server-database-using-azure-portal.md)
- [Azure-adatbázis létrehozása MySQL-kiszolgálóhoz az Azure CLI használatával](quickstart-create-mysql-server-database-using-azure-cli.md)

Több Azure CLI-mintát talál itt:

- [Azure CLI-minták a Azure Database for MySQLhoz](sample-scripts-azure-cli.md)

## <a name="adjust-performance-and-scale-within-seconds"></a>Teljesítmény módosítása és skálázása másodperceken belül
A Azure Database for MySQL szolgáltatás számos szolgáltatási szintet kínál: Alapszintű, általános célú és memória optimalizálva. Az egyes szintek különböző teljesítményt és képességeket kínálnak, így különböző adatbázis-tevékenységprofilokat képesek támogatni, a könnyűtől a nehéz számítási feladatokig. Havi pár dollárért létrehozhatja első, kisméretű adatbázis-alkalmazását, majd később a megoldása szükségletei alapján módosíthatja a méretet. A dinamikus méretezhetőség révén az adatbázis átlátható módon reagál a gyorsan változó erőforrásigényekre. Csak azokért az erőforrásokért kell fizetnie, amelyekre szüksége van, és csak akkor, amikor szüksége van rájuk. A részletekért tekintse meg a [díjszabási szintet](concepts-service-tiers.md) .

## <a name="monitoring-and-alerting"></a>Figyelés és riasztás
Hogyan lehet megállapítani, hogy mikor van szükség fel- és leskálázásra? Használja a virtuális magokon alapuló, teljesítmény-értékeléssel kombinált, beépített teljesítménymonitorozó és riasztási eszközöket. Ezek az eszközök lehetővé teszik a virtuális magok aktuális vagy a becsült teljesítményigényeken alapuló fel- vagy leskálázása hatásainak gyors kiértékelését. A részleteket a [riasztások](howto-alert-on-metric.md) leírása tartalmazza.

## <a name="keep-your-app-and-business-running"></a>Biztosítsa alkalmazása és vállalkozása folyamatos működését
Az Azure szolgáltatói szerződésében (SLA) az ágazatban élenjáró módon 99,99 százalékos rendelkezésre állást biztosítunk – a Microsoft által kezelt adatbázisok globális hálózata teszi lehetővé, hogy alkalmazása a hét mind a 7 napján, napi 24 órában fusson. Minden Azure Database for MySQL-kiszolgáló esetében kihasználhatja a beépített biztonság, hibatűrés és adatvédelem előnyeit, amelyeket egyébként nem kell megvásárolnia vagy megterveznie, kiépítenie és kezelnie. A Azure Database for MySQL használatával az időponthoz tartozó visszaállítással visszaállíthatja a kiszolgálót egy korábbi állapotba, a 35 nap visszamenőleg visszamenőleg.

## <a name="secure-your-data"></a>Az adatok védelme
Az Azure Database Services olyan adatbiztonságot biztosít, amely Azure Database for MySQL megtartja az olyan funkciókat, amelyek korlátozzák a hozzáférést, a REST-alapú és a mozgásban lévő adatok védelméhez, és a tevékenységek figyeléséhez. Az Azure platform biztonságáról az [Azure biztonsági és adatkezelési központban](https://www.microsoft.com/en-us/trustcenter/security) talál információkat.

A Azure Database for MySQL szolgáltatás tárolási titkosítást használ a REST-alapú adatokhoz, és az FIPS 140-2-kompatibilis. Az adatok a biztonsági másolatokkal együtt titkosítva kerülnek a lemezekre (a motor által lekérdezések futtatásakor létrehozott ideiglenes fájlok kivételével). A szolgáltatás AES 256 bites titkosítást használ, amely az Azure tárolási titkosításban szerepel, és a kulcsokat a rendszer felügyeli. A tárolási titkosítás mindig be van kapcsolva és nem tiltható le.

Alapértelmezés szerint a Azure Database for MySQL szolgáltatás úgy van konfigurálva, hogy [SSL-kapcsolati biztonságot](./concepts-ssl-connection-security.md) igényeljen a hálózaton belüli adatmozgáshoz. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti SSL-kapcsolatok kikényszerítése elősegíti a „köztes” támadások elleni védelmet, mert titkosítja a kiszolgáló és az alkalmazás közötti streameket. Választhatja az SSL megkövetelésének letiltását az adatbázis-szolgáltatáshoz való kapcsolódáshoz, ha az ügyfélalkalmazás nem támogatja az SSL-kapcsolatokat.

## <a name="contacts"></a>Névjegyek
Ha bármilyen kérdése vagy javaslata van a Azure Database for MySQL használatáról, küldjön e-mailt a Azure Database for MySQL csapatnak ([ @Ask a MySQL-hez készült Azure-adatbázis](mailto:AskAzureDBforMySQL@service.microsoft.com)). Ne feledje, hogy ez nem műszaki támogatás.

Ezenkívül a következő elérhetőségeken léphet velünk kapcsolatba:

- Ha az Azure-támogatással szeretne kapcsolatba lépni, [nyújtson be igénylést az Azure Portalon](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Ha a fiókjával van probléma, nyújtson be [támogatási kérelmet](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) az Azure Portalon.
- Ha visszajelzést szeretne küldeni vagy új szolgáltatásokat kérne, hozzon létre egy bejegyzést a [UserVoice-on](https://feedback.azure.com/forums/597982-azure-database-for-mysql).

## <a name="next-steps"></a>További lépések
Most, hogy elolvasta a Azure Database for MySQL bevezetését, és megválaszolta a "mi az Azure Database for MySQL?" kérdést, készen áll a következőre:

- Tekintse meg az árképzést ismertető oldalt a költségek összehasonlításáért és árkalkulációjáért. [Díjszabás](https://azure.microsoft.com/pricing/details/mysql/)
- Első lépésként hozza létre első kiszolgálóját. [Azure-adatbázis létrehozása MySQL-kiszolgálóhoz az Azure Portal használatával](quickstart-create-mysql-server-database-using-azure-portal.md)
- Hozza létre első alkalmazását a kívánt nyelv használatával: [Python](./connect-python.md)[Node. js](./connect-nodejs.md)[](./connect-java.md)[](./connect-go.md) [](./connect-php.md)[Java Ruby](./connect-ruby.md)php[.net (C#)](./connect-csharp.md)go |  |  |  |  |  | 
