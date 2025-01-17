---
title: 'Útmutató SQL Database több-bérlős alkalmazáshoz – példa: Wingtip SaaS | Microsoft Docs'
description: A Azure SQL Database, a Wingtip jegyek SaaS-példáját használó minta több-bérlős alkalmazás telepítésének és futtatásának lépéseit és útmutatását tartalmazza.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: sstein
ms.date: 12/18/2018
ms.openlocfilehash: 6c14fd69521be85dbda5ec4ceda991dfdff54ae0
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68570067"
---
# <a name="general-guidance-for-working-with-wingtip-tickets-sample-saas-apps"></a>Általános útmutató a Wingtip jegyek használatához – példa SaaS-alkalmazásokra

Ez a cikk általános útmutatást tartalmaz a Wingtip tickets Azure SQL Database-t használó SaaS-alkalmazások futtatásához. 

## <a name="download-and-unblock-the-wingtip-tickets-saas-scripts"></a>A Wingtip tickets SaaS-parancsfájlok letöltése és feloldása

A Windows letilthatja a végrehajtható tartalmat (parancsfájlokat, DLL-eket), ha a ZIP-fájlokat külső forrásból tölti le, és kinyeri. A parancsfájlok zip-fájlból való kibontásakor **kövesse az alábbi lépéseket a. zip fájl zárolásának feloldásához a kicsomagolás előtt**. Ez biztosítja, hogy a parancsfájlok futtatása engedélyezett legyen.

1. Keresse meg a Wingtip tickets SaaS GitHub-tárházat a vizsgálni kívánt adatbázis-bérleti minta alapján: 
    - [WingtipTicketsSaaS-StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp)
    - [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant)
    - [WingtipTicketsSaaS-MultiTenantDb](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb)
2. Kattintson a **klónozás vagy a letöltés**elemre.
3. Kattintson a **zip letöltése** elemre, és mentse a fájlt.
4. Kattintson a jobb gombbal a zip-fájlra, és válassza a **Tulajdonságok**lehetőséget. A zip-fájl neve meg fog egyezni a tárház nevével. például. _WingtipTicketsSaaS-DbPerTenant-master.zip_)
5. Az **általános** lapon válassza a **Tiltás feloldása**lehetőséget.
6. Kattintson az **OK** gombra.
7. Bontsa ki a fájlokat.

A parancsfájlok a *.. Tanulási\\modulok* mappája.


## <a name="working-with-the-wingtip-tickets-powershell-scripts"></a>A Wingtip tickets PowerShell-parancsfájlok használata

Ahhoz, hogy a lehető legtöbbet hozza ki a mintából, el kell sajátítani a megadott szkripteket. Használjon töréspontokat, és hajtsa végre a parancsfájlok végrehajtását, és vizsgálja meg a különböző SaaS-minták megvalósításának módját. Ha a lehető legpontosabban szeretné megismerni a megadott parancsfájlokat és modulokat, javasoljuk, hogy használja a [POWERSHELL ISE](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise)-t.

### <a name="update-the-configuration-file-for-your-deployment"></a>A központi telepítés konfigurációs fájljának frissítése

Szerkessze a **userconfig. psm1** fájlt az üzembe helyezés során beállított erőforráscsoporthoz és felhasználói értékkel:

1. Nyissa meg a *POWERSHELL ISE* -t és a betöltést... Learning-\\modulok*userconfig. psm1* \\ 
2. Frissítse a *ResourceGroupName* és a *nevet* az üzemelő példány megadott értékeivel (csak a 10. és a 11. sorban).
3. Mentse a módosításokat!

Ezeknek az értékeknek a beállításával egyszerűen megtekintheti, hogy az adott központi telepítési értékeket minden parancsfájlban frissíteni kell-e.

### <a name="execute-the-scripts-by-pressing-f5"></a>Futtassa a szkripteket az F5 billentyű lenyomásával

Több parancsfájl *$PSScriptRoott* használ a mappák eléréséhez, és az *$PSScriptRoot* csak akkor lesz kiértékelve, ha az **F5**billentyű lenyomásával végzi a parancsfájlok végrehajtását.  Ha kijelöli és futtatja a kijelölést (**F8**), hibákat eredményezhet, ezért a parancsfájlok futtatásakor nyomja le az **F5** billentyűt.

### <a name="step-through-the-scripts-to-examine-the-implementation"></a>A megvalósítás vizsgálatához lépjen végig a szkripteken

A szkriptek megismerésének legjobb módja az, hogy megtekintheti, hogy mit csinálnak. Tekintse meg a mellékelt bemutatót, amely egy könnyen követhető, magas szintű munkafolyamatot jelent. A **bemutató-** szkriptek az egyes feladatok végrehajtásához szükséges lépéseket mutatják be, ezért a töréspontok és a részletes részletezés használatával megtekintheti a különböző SaaS-minták megvalósítási részleteit.

Tippek a PowerShell-parancsfájlok vizsgálatához és megerősítéséhez:

- Nyissa meg a **bemutató-** szkripteket a PowerShell ISE-ben.
- Végrehajtás vagy Folytatás az **F5** billentyűvel (az **F8** használata nem ajánlott, mert a rendszer nem értékeli ki *$PSScriptRoot* egy parancsfájl kiválasztásakor.)
- Töréspontok elhelyezéséhez kattintson egy sorra, vagy válasszon ki egyet, és nyomja le az **F9** billentyűt.
- Függvény- vagy szkripthívás átlépéséhez használja az **F10** billentyűt.
- Függvény- vagy szkripthívásba az **F11** billentyűvel léphet.
- A jelenlegi függvény- vagy szkripthívásból a **Shift + F11** billentyűkkel léphet ki.


## <a name="explore-database-schema-and-execute-sql-queries-using-ssms"></a>Adatbázisséma vizsgálatát és SQL-lekérdezések végrehajtása SSMS használatával

A [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) használatával csatlakozhat, és böngészhet az alkalmazás-kiszolgálók és-adatbázisok között.

Az üzembe helyezés kezdetben a bérlők és a katalógus SQL Database-kiszolgálókkal csatlakozik. A kiszolgálók elnevezése az adatbázis-kiosztási mintától függ (lásd alább az egyes sajátosságokat). 

   - **Önálló alkalmazás:** minden bérlő kiszolgálója (pl. *contosoconcerthall-&lt;User&gt;*  Server) és *Catalog-SA-&lt;User&gt;*
   - **Adatbázis/bérlő:** *tenants1-DPT-&lt;User&gt;*  és *Catalog-DPT-&lt;User&gt;*  kiszolgálók
   - **Több-bérlős adatbázis:** *tenants1-MT&lt;-User&gt;*  és *Catalog-MT-&lt;User&gt;*  kiszolgálók

A sikeres bemutató-kapcsolatok biztosítása érdekében minden kiszolgálónak van egy [Tűzfalszabály](sql-database-firewall-configure.md) , amely engedélyezi az összes IP-címet.


1. Nyissa meg a *SSMS* , és kapcsolódjon a bérlőhöz. A kiszolgáló neve a kiválasztott adatbázis-bérlettől függ (lásd alább az egyes sajátosságokat):
    - **Önálló alkalmazás:** az egyes bérlők kiszolgálói (pl. *contosoconcerthall-&lt;User&gt;. database.Windows.net*) 
    - **Adatbázis/bérlő:** *tenants1-DPT-&lt;User&gt;. database.Windows.net*
    - **Több-bérlős adatbázis:** *tenants1-MT-&lt;User&gt;. database.Windows.net* 
2. Kattintson a **Kapcsolódás** > **Adatbázismotor...** :

   ![katalóguskiszolgáló elemre](media/saas-tenancy-wingtip-app-guidance-tips/connect.png)

3. A bemutató hitelesítő adatai a következők: Login = *fejlesztői*, jelszó = *P\@ssword1*

    Az alábbi képen látható, hogy az adatbázisnak a bérlői minta alapján kell bejelentkeznie. 
    ![kapcsolat](media/saas-tenancy-wingtip-app-guidance-tips/tenants1-connect.png)
    
   

4. Ismételje meg a 2-3 lépést, és kapcsolódjon a katalógus-kiszolgálóhoz (lásd az alábbi, az adatbázis kiválasztási mintája alapján megadott kiszolgálók neveit).
    - **Önálló alkalmazás:** *katalógus – SA-&lt;User&gt;. database.Windows.net*
    - **Adatbázis/bérlő:** *Catalog-DPT-&lt;User&gt;. database.Windows.net*
    - **Több-bérlős adatbázis:** *katalógus – MT-&lt;User&gt;. database.Windows.net*


A sikeres csatlakozás után az összes kiszolgálót látnia kell. Az adatbázisok listája a kiépített bérlőtől függően eltérő lehet.

Az alábbi képen bemutatjuk, hogyan történik a bejelentkezés az adatbázishoz a bérlői minta alapján.

![Object Explorer](media/saas-tenancy-wingtip-app-guidance-tips/object-explorer.png)



## <a name="next-steps"></a>További lépések
- [A Wingtip tickets SaaS önálló alkalmazás üzembe helyezése](saas-standaloneapp-get-started-deploy.md)
- [A Wingtip tickets SaaS-adatbázis üzembe helyezése bérlői alkalmazásokban](saas-dbpertenant-get-started-deploy.md)
- [A Wingtip tickets SaaS több-bérlős adatbázis-alkalmazás üzembe helyezése](saas-multitenantdb-get-started-deploy.md)

