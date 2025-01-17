---
title: 'Azure Portal: SQL Database felügyelt példány létrehozása | Microsoft Docs'
description: Hozzon létre egy SQL Database felügyelt példányt, hálózati környezetet és ügyfél virtuális gépet a hozzáféréshez.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab
ms.date: 05/07/2019
ms.openlocfilehash: 646a602d7e6c47454f039a5f4f1981a60b56fa56
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567519"
---
# <a name="quickstart-create-an-azure-sql-database-managed-instance"></a>Gyors útmutató: Azure SQL Database felügyelt példány létrehozása

Ez a rövid útmutató bemutatja, hogyan hozhat létre Azure SQL Database [felügyelt példányt](sql-database-managed-instance.md) a Azure Portalban.

> [!IMPORTANT]
> Korlátozásokkal kapcsolatban lásd: [támogatott régiók](sql-database-managed-instance-resource-limits.md#supported-regions) és [támogatott előfizetési típusok](sql-database-managed-instance-resource-limits.md#supported-subscription-types).

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/).

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="create-a-managed-instance"></a>Felügyelt példány létrehozása

A következő lépések bemutatják, hogyan hozhat létre felügyelt példányt.

1. Válassza az **erőforrás létrehozása** lehetőséget a Azure Portal bal felső sarkában.
2. Keresse meg a **felügyelt példányt**, majd válassza az **Azure SQL felügyelt példány**lehetőséget.
3. Kattintson a **Létrehozás** gombra.

   ![Felügyelt példány létrehozása](./media/sql-database-managed-instance-get-started/managed-instance-create.png)

4. Töltse ki az **SQL felügyelt példány** űrlapját a kért információkkal a következő táblázatban található információk alapján.

   | Beállítás| Ajánlott érték | Leírás |
   | ------ | --------------- | ----------- |
   | **Előfizetés** | Az előfizetése. | Olyan előfizetés, amely engedélyt ad új erőforrások létrehozására. |
   |**Felügyelt példány neve**|Bármely érvényes név.|Az érvényes nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) ismertető cikket.|
   |**Felügyelt példány rendszergazdai bejelentkezési neve**|Bármilyen érvényes Felhasználónév.|Az érvényes nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) ismertető cikket. Ne használja a "ServerAdmin" kulcsszót, mert ez egy fenntartott kiszolgálói szintű szerepkör.|
   |**Jelszó**|Bármilyen érvényes jelszó.|A jelszónak legalább 16 karakter hosszúságúnak kell lennie, és teljesítenie kell [a meghatározott összetettségi követelményeket](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|
   |**Időzóna**|A felügyelt példány által megfigyelt időzóna.|További információ: időzóna [](sql-database-managed-instance-timezone.md).|
   |**Egybevetés**|A felügyelt példányhoz használni kívánt rendezés.|Ha SQL Serverból telepít át adatbázisokat, ellenőrizze a forrás rendezését a `SELECT SERVERPROPERTY(N'Collation')` használatával, és használja ezt az értéket. További információ a rendezésekről: [a kiszolgáló rendezésének beállítása vagy módosítása](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-server-collation).|
   |**Location**|A hely, amelyben létre kívánja hozni a felügyelt példányt.|További információ a régiókkal kapcsolatban: [Azure-régiók](https://azure.microsoft.com/regions/).|
   |**Virtuális hálózat**|Válassza az **új virtuális hálózat létrehozása** vagy egy érvényes virtuális hálózat és alhálózat lehetőséget.| Ha egy hálózat vagy alhálózat nem érhető el, akkor azt módosítani kell, [hogy megfeleljen a hálózati követelményeknek](sql-database-managed-instance-configure-vnet-subnet.md) , mielőtt kiválasztja az új felügyelt példány célhelyének. A felügyelt példányok hálózati környezetének konfigurálásával kapcsolatos tudnivalókat lásd: [virtuális hálózat konfigurálása felügyelt példányhoz](sql-database-managed-instance-connectivity-architecture.md). |
   |**Nyilvános végpont engedélyezése**   |Jelölje be ezt a beállítást a nyilvános végpont engedélyezéséhez   |Ahhoz, hogy a felügyelt példány elérhető legyen a nyilvános adatvégponton keresztül, engedélyeznie kell a **nyilvános végpontot** .| 
   |**Hozzáférés engedélyezése innen:**   |Válasszon egyet a lehetőségek közül: <ul> <li>**Azure-szolgáltatások**</li> <li>**Internet**</li> <li>**Nincs hozzáférés**</li></ul>   |A portál felülete lehetővé teszi a biztonsági csoport nyilvános végponttal történő konfigurálását. </br> </br> A forgatókönyv alapján válasszon a következő lehetőségek közül: </br> <ul> <li>Azure-szolgáltatások – ajánlott Power BI vagy más több-bérlős szolgáltatáshoz való csatlakozáskor. </li> <li> Internet – tesztelési célokra használható, ha gyorsan el szeretné végezni a felügyelt példányok üzembe helyezését. Éles környezetekben való használatra nem ajánlott. </li> <li> Nincs hozzáférés – ez a beállítás egy megtagadási biztonsági szabályt hoz létre. Módosítania kell ezt a szabályt ahhoz, hogy a felügyelt példány elérhető legyen nyilvános végponton keresztül. </li> </ul> </br> A nyilvános végpontok biztonságával kapcsolatos további információkért lásd: [Azure SQL Database felügyelt példány biztonságos használata nyilvános végponttal](sql-database-managed-instance-public-endpoint-securely.md).|
   |**Kapcsolattípus**|Válasszon egy proxy és egy átirányítási kapcsolat típusa közül.|További információ a kapcsolatok típusairól: [Azure SQL Database a kapcsolatkérelem-házirend](sql-database-connectivity-architecture.md#connection-policy).|
   |**Erőforráscsoport**|Új vagy meglévő erőforráscsoport.|Az érvényes erőforráscsoport-nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) ismertető cikket.|

   ![Felügyelt példány űrlapja](./media/sql-database-managed-instance-get-started/managed-instance-create-form.png)

5. Ha a felügyelt példányt másodlagos feladatátvételi csoportként szeretné használni, válassza ki a kivételt, és adja meg a DnsAzurePartner felügyelt példányát. Ez a funkció előzetes verzióban érhető el, és nem jelenik meg a következő képernyőképen.
6. Válassza ki az **árképzési szintet** a számítási és tárolási erőforrások méretének kiválasztásához, valamint az árképzési szintek beállításainak áttekintéséhez. Az alapértelmezett érték az általános célú tarifacsomag, amely 32 GB memóriával és 16 virtuális maggal rendelkezik.
7. A csúszkák vagy a szövegmezők segítségével adja meg a tárterület méretét és a virtuális magok számát.
8. Ha elkészült, kattintson az **alkalmaz** gombra a kijelölés mentéséhez. 
9. Válassza a **Létrehozás** lehetőséget a felügyelt példány telepítéséhez.
10. A központi telepítés állapotának megtekintéséhez kattintson az **értesítések** ikonra.

    ![Felügyelt példányok telepítési folyamata](./media/sql-database-managed-instance-get-started/deployment-progress.png)

11. Válassza a **telepítés folyamatban** lehetőséget a felügyelt példány ablak megnyitásához a központi telepítési folyamat további figyeléséhez. 

> [!IMPORTANT]
> Egy alhálózat első példányában a telepítési idő általában sokkal hosszabb, mint a későbbi példányokban. Ne szakítsa meg a telepítési műveletet, mert az a vártnál tovább tart.

## <a name="review-resources-and-retrieve-your-fully-qualified-server-name"></a>Erőforrások áttekintése és a teljes kiszolgálónév beolvasása

Az üzembe helyezés sikerességét követően tekintse át a létrehozott erőforrásokat, és kérje le a teljes kiszolgálónevet a későbbi gyors útmutatókban való használatra.

1. Nyissa meg a felügyelt példányhoz tartozó erőforráscsoportot. Tekintse meg a [felügyelt példány létrehozása](#create-a-managed-instance) rövid útmutatójában létrehozott erőforrásait.

   ![Felügyelt példány erőforrásai](./media/sql-database-managed-instance-get-started/resources.png)

2. Válassza ki az útválasztási táblázatot az Ön számára létrehozott felhasználó által megadott route (UDR) tábla áttekintéséhez.

   ![Útválasztási táblázat](./media/sql-database-managed-instance-get-started/route-table.png)

3. Az útválasztási táblázatban tekintse át azokat a bejegyzéseket, amelyek a felügyelt példányok virtuális hálózatán belüli és onnan érkező forgalmat irányítják. Ha manuálisan hozza létre vagy konfigurálja az útválasztási táblázatot, akkor ezeket a bejegyzéseket az útválasztási táblában kell létrehoznia.

   ![Felügyelt példány alhálózatának helyire való bejegyzése](./media/sql-database-managed-instance-get-started/udr.png)

4. Térjen vissza az erőforráscsoporthoz, és válassza ki a hálózati biztonsági csoportot a biztonsági szabályok áttekintéséhez.

   ![Hálózati biztonsági csoport](./media/sql-database-managed-instance-get-started/network-security-group.png)

5. Tekintse át a bejövő és kimenő biztonsági szabályokat. Ha a felügyelt példányhoz nyilvános végpontokat konfigurált, további információért tekintse meg a [nyilvános végpont konfigurálása](sql-database-managed-instance-public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group) című cikket.

   ![Biztonsági szabályok](./media/sql-database-managed-instance-get-started/security-rules.png)

6. Térjen vissza az erőforráscsoporthoz, és válassza ki a felügyelt példányt.

   ![Felügyelt példány](./media/sql-database-managed-instance-get-started/managed-instance.png)

7. Az **Áttekintés** lapon keresse meg a **gazdagép** tulajdonságot. Másolja a felügyelt példány teljes állomásnevét a következő rövid útmutatóban való használatra.

   ![Gazdagép neve](./media/sql-database-managed-instance-get-started/host-name.png)

   A név hasonlít a **your_machine_name. a1b2c3d4e5f6. database. Windows. net**névre.

## <a name="next-steps"></a>További lépések

- A felügyelt példányokhoz való kapcsolódással kapcsolatos tudnivalók:
  - Az alkalmazások csatlakozási lehetőségeinek áttekintését lásd: [alkalmazások csatlakoztatása felügyelt példányhoz](sql-database-managed-instance-connect-app.md).
  - A felügyelt példányok Azure-beli virtuális gépekről történő csatlakoztatását bemutató rövid útmutató: Azure-beli virtuálisgép- [kapcsolat konfigurálása](sql-database-managed-instance-configure-vm.md).
  - A pont – hely kapcsolat [konfigurálásával](sql-database-managed-instance-configure-p2s.md)megtudhatja, hogyan csatlakozhat egy felügyelt példányhoz egy helyszíni ügyfélszámítógépről pont – hely kapcsolat használatával.
- Meglévő SQL Server-adatbázis visszaállítása a helyszínről a felügyelt példányra: 
    - Az áttelepítéshez használja a [Azure Database Migration Service (DMS)](../dms/tutorial-sql-server-to-managed-instance.md) az adatbázis biztonságimásolat-fájljából való visszaállításhoz. 
    - Az adatbázis-biztonságimásolat-fájlból történő visszaállításhoz használja a [T-SQL Restore parancsot](sql-database-managed-instance-get-started-restore.md) .
- A felügyelt példányok adatbázisának teljesítményének speciális figyelése a beépített hibaelhárítási intelligenciával: [Azure SQL Database figyelése Azure SQL Analytics használatával](../azure-monitor/insights/azure-sql.md).
