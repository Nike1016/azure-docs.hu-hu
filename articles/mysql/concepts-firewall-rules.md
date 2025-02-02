---
title: Azure Database for MySQL kiszolgálói tűzfalszabályok
description: Leírja a Azure Database for MySQL-kiszolgáló tűzfalszabályok szabályait.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 0802185b7fb0d1a6d7d41cd1fa5a30f5ce10424b
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443911"
---
# <a name="azure-database-for-mysql-server-firewall-rules"></a>Azure Database for MySQL kiszolgálói tűzfalszabályok
A tűzfalak mindaddig megakadályozzák az adatbázis-kiszolgáló elérését, amíg meg nem adja, hogy mely számítógépek rendelkeznek engedéllyel. A tűzfal az egyes kérések származó IP-címe alapján engedélyezi a hozzáférést a kiszolgálóhoz.

A tűzfal konfigurálásához hozzon létre olyan tűzfalszabályok, amelyek elfogadható IP-címek tartományait határozzák meg. A tűzfalszabályok a kiszolgáló szintjén hozhatók létre.

**Tűzfalszabályok:** Ezek a szabályok lehetővé teszik az ügyfelek számára, hogy elérjék a teljes Azure Database for MySQL-kiszolgálót, azaz az azonos logikai kiszolgálón belüli összes adatbázist. A kiszolgálói szintű tűzfalszabályok a Azure Portal vagy az Azure CLI-parancsok használatával konfigurálhatók. A kiszolgálói szintű tűzfalszabályok létrehozásához az előfizetés tulajdonosának vagy az előfizetés közreműködőinek kell lennie.

## <a name="firewall-overview"></a>Tűzfal áttekintése
A tűzfal alapértelmezés szerint blokkolja a Azure Database for MySQL-kiszolgálóhoz tartozó összes adatbázis-hozzáférést. A kiszolgáló másik számítógépről való használatának megkezdéséhez meg kell adnia egy vagy több kiszolgálóoldali tűzfalszabályok használatát a kiszolgálóhoz való hozzáférés engedélyezéséhez. A tűzfalszabályok használatával adhatja meg, hogy mely IP-címtartományok legyenek engedélyezve az internetről. A tűzfalszabályok nem érintik a Azure Portal webhelyhez való hozzáférést.

Az internetről és az Azure-ról érkező csatlakozási kísérleteknek előbb át kell haladnia a tűzfalon, mielőtt azok elérnék a Azure Database for MySQL-adatbázist, ahogy az a következő ábrán is látható:

![Példa a tűzfal működésének folyamatára](./media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>Csatlakozás az internetről
A kiszolgálói szintű tűzfalszabályok a Azure Database for MySQL-kiszolgálón lévő összes adatbázisra érvényesek.

Ha a kérés IP-címe a kiszolgálói szintű tűzfalszabályok megadott tartományán belül van, akkor a rendszer megadja a kapcsolatokat.

Ha a kérelem IP-címe kívül esik az adatbázis-vagy a kiszolgálói szintű tűzfalszabályok valamelyikében megadott tartományon, akkor a kapcsolati kérelem sikertelen lesz.

## <a name="connecting-from-azure"></a>Csatlakozás az Azure-ból
Ha engedélyezni szeretné, hogy az Azure-alkalmazások csatlakozni tudjanak a Azure Database for MySQL-kiszolgálóhoz, engedélyezni kell az Azure-kapcsolatokat. Például egy Azure Web Apps-alkalmazás vagy egy Azure-beli virtuális gépen futó alkalmazás üzemeltetéséhez, vagy egy Azure Data Factory adatkezelési átjáróból való kapcsolódáshoz. Az erőforrásoknak nem kell ugyanabban a Virtual Networkban (VNet) vagy erőforráscsoporthoz kell lenniük ahhoz, hogy engedélyezze ezeket a kapcsolatokat. Amikor egy Azure-alkalmazás megkísérel csatlakozni az adatbázis-kiszolgálóhoz, a tűzfal ellenőrzi, hogy az Azure-kapcsolatok engedélyezve vannak-e. Az ilyen típusú kapcsolatok engedélyezéséhez több módszer is rendelkezésre áll. A 0.0.0.0 kezdő- és zárócímet tartalmazó tűzfalbeállítás jelzi, hogy ezek a kapcsolatok engedélyezettek. Azt is megteheti **, hogy az** **Azure-szolgáltatásokhoz való hozzáférés engedélyezése** lehetőségre kattint a portálon a **kapcsolat biztonsági** paneljén, és megnyomja a **Mentés gombot**. Ha a kapcsolódási kísérlet nem engedélyezett, a kérelem nem éri el a Azure Database for MySQL-kiszolgálót.

> [!IMPORTANT]
> Ez a beállítás konfigurálja a tűzfalat arra, hogy engedélyezzen minden, az Azure felől érkező kapcsolatot, beleértve a más ügyfelek előfizetéseiből érkező kapcsolatokat is. Ezen beállítás kiválasztásakor győződjön meg arról, hogy a bejelentkezési és felhasználói engedélyei a hozzáféréseket az arra jogosult felhasználókra korlátozzák.
> 

![Az Azure-szolgáltatásokhoz való hozzáférés engedélyezése a portálon](./media/concepts-firewall-rules/allow-azure-services.png)

## <a name="programmatically-managing-firewall-rules"></a>Tűzfalszabályok szoftveres kezelése
A Azure Portalon kívül a tűzfalszabályok programozott módon is kezelhetők az Azure CLI használatával. Lásd még: [Azure Database for MySQL tűzfalszabályok létrehozása és kezelése az Azure CLI-](./howto-manage-firewall-using-cli.md) vel

## <a name="troubleshooting-the-database-firewall"></a>Az adatbázistűzfal hibaelhárítása
Vegye figyelembe a következő szempontokat, amikor a MySQL-kiszolgáló szolgáltatáshoz való Microsoft Azure adatbázishoz való hozzáférés nem a várt módon viselkedik:

* **Az engedélyezési lista módosításai még nem léptek érvénybe:** A Azure Database for MySQL-kiszolgáló tűzfal-konfigurációjának változásaira vonatkozóan akár öt perc is eltelhet.

* **A bejelentkezési azonosító nincs engedélyezve, vagy helytelen jelszót használt:** Ha a bejelentkezési azonosító nem rendelkezik engedéllyel a Azure Database for MySQL-kiszolgálón, vagy helytelen a használt jelszó, a rendszer megtagadja a kapcsolódást a Azure Database for MySQL-kiszolgálóhoz. Egy tűzfalbeállítás létrehozása lehetővé teszi az ügyfelek számára a kiszolgálóhoz való csatlakozás megkísérlését, azonban minden egyes ügyfélnek meg kell adnia a szükséges biztonsági hitelesítő adatokat.

* **Dinamikus IP-cím:** Ha a dinamikus IP-címzéssel rendelkező internetkapcsolattal rendelkezik, és a tűzfalon keresztül nem sikerül bejutnia, akkor a következő megoldások valamelyikét kipróbálhatja:

* Kérje meg az internetszolgáltatót (ISP) a Azure Database for MySQL-kiszolgálót elérő ügyfélszámítógépekhez hozzárendelt IP-címtartomány számára, majd adja hozzá az IP-címtartományt tűzfalszabályként.

* Állítson be statikus IP-címeket az ügyfélszámítógépei számára, majd adja meg az IP-címeket tűzfalszabályokként.

## <a name="next-steps"></a>További lépések

* [Azure Database for MySQL tűzfalszabályok létrehozása és kezelése a Azure Portal használatával](./howto-manage-firewall-using-portal.md)
* [Azure Database for MySQL tűzfalszabályok létrehozása és kezelése az Azure CLI-vel](./howto-manage-firewall-using-cli.md)
