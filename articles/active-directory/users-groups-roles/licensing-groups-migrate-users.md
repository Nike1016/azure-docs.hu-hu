---
title: Csoportalapú licencelés – külön-külön licenccel rendelkező felhasználók hozzáadása az Azure Active Directory |} A Microsoft Docs
description: Az egyes felhasználói licencek migrálása Csoportalapú licencelést, az Azure Active Directoryval
services: active-directory
keywords: Az Azure AD licencelése
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: seohack1;it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 333f0ae0153073b57740446ecf47e36a1f9ce590
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65192456"
---
# <a name="how-to-migrate-users-with-individual-licenses-to-groups-for-licensing"></a>Az egyes licenccel rendelkező felhasználók migrálása a csoportok licenckezeléshez

Előfordulhat, hogy telepíti a felhasználók számára a "közvetlen hozzárendelés"; keresztül a szervezetek meglévő licencek PowerShell-szkriptekkel vagy más eszközökkel, használatával az egyes felhasználói licencek hozzárendelése. Mielőtt elkezdené, Csoportalapú licencelést használ a szervezet licencek kezeléséhez, ez a migrálási terv segítségével zökkenőmentesen cserélje le a meglévő megoldásokkal a Csoportalapú licenceléssel.

A legfontosabb, amit figyelembe kell venni az, hogy egy olyan helyzetet, ahol migrálása Csoportalapú licencelésre fog eredményezni a jelenleg hozzárendelt licencek ideiglenesen elvesztése felhasználók kerülendő. Minden olyan folyamat, amelynek hatására a licencek eltávolítása el kell kerülni, távolítsa el a felhasználók, szolgáltatások és az adataik való hozzáférés elvesztése kockázatát.

## <a name="recommended-migration-process"></a>Javasolt áttelepítési folyamata

1. A licenc-hozzárendelés és eltávolítási felhasználók kezelése automation (például a PowerShell) rendelkezik. Hagyja futni, a rendszer.

2. Hozzon létre egy új licencelési csoportot (vagy döntse el, melyik meglévő csoportokat kívánja használni), és győződjön meg arról, hogy minden szükséges felhasználók vannak hozzáadva tagként.

3. A szükséges licencek hozzárendelése a csoportokhoz; a cél, hogy a meglévő automation (például a PowerShell) alkalmaz a felhasználók licencelési állapotra kell lennie.

4. Győződjön meg arról, hogy megtörtént-e a licencek ezeket a csoportokat az összes felhasználóra. Ez az alkalmazás az egyes csoportok a feldolgozási állapot ellenőrzésével és a naplók ellenőrzésével végezheti el.

   - Szúrópróbaszerű ellenőrzésével megerősítheti egyéni felhasználók számára is saját licenc részletek megtekintésével. Látni fogja, hogy rendelkeznek azonos licenccel "közvetlenül" és "örökölt" csoportokból.

   - Egy PowerShell-parancsprogram futtatása [győződjön meg arról, hogy a felhasználók hogyan vannak licencek rendelve](licensing-group-advanced.md#use-powershell-to-see-who-has-inherited-and-direct-licenses).

   - Az azonos terméklicenc hozzárendelése a felhasználóhoz mindkét közvetlenül és a egy csoporton keresztül esetén csak egy-egy licencet a felhasználó használja fel. Ezért semmilyen további licenceket áttelepítés végrehajtásához szükséges.

5. Győződjön meg arról, hogy nincs licenc-hozzárendelés nem sikerült minden csoport a hibás állapotú felhasználók ellenőrzésével. További információkért lásd: [azonosítása és megoldása a csoportok licencproblémáinak](licensing-groups-resolve-problems.md).

6. Fontolja meg az eredeti közvetlen hozzárendelés; eltávolítása fokozatos, ehhez "regisztrálások", a felhasználók egy alhalmazára eredményeinek figyelése először érdemes.

   Az eredeti közvetlen hozzárendelés sikerült hagyja, a felhasználók, de ha a felhasználók elhagyják a licenccel rendelkező csoportok azok továbbra is megőrzik az eredeti licenc, amely valószínűleg nem ajánlott.

## <a name="an-example"></a>Példa

Egy szervezet 1000 felhasználó rendelkezik. Az összes felhasználóknak szükségük van az Enterprise Mobility + Security (EMS) licencet. 200 felhasználó a pénzügyi részleg és az Office 365 nagyvállalati E3 csomag licencek szükségesek. A szervezet jelenleg rendelkezik a helyszíni, hozzáadása és licencek eltávolításával felhasználóktól származnak, és nyissa meg egy PowerShell-parancsfájlt. Azonban a szervezet szeretné Csoportalapú licencelést, így licencek automatikusan az Azure AD által kezelhető cserélje le a parancsfájlt.

Íme az áttelepítési folyamat sikerült kinézni:

1. Az Azure portal használatával, az EMS-licenc hozzárendelése a **minden felhasználó** csoportot az Azure ad-ben. A E3 csomag licenc hozzárendelése a **pénzügyi részleg** csoportot, amely tartalmazza a szükséges felhasználókat.

2. Mindegyik csoportnál ellenőrizze, hogy a licenc-hozzárendelés az összes felhasználó számára befejeződött. Nyissa meg az egyes csoportokhoz, válassza a panel **licencek**, és a feldolgozás állapotának felső részén a **licencek** panelen.

   - Keressen a "Legújabb licenc változtatások alkalmazása minden felhasználó" megerősítéséhez feldolgozása befejeződött.

   - Keresse meg felül minden olyan felhasználók, akiknek előfordulhat, hogy nem lett sikeresen rendelt licenceket kapcsolatos értesítést. Futtattuk bizonyos felhasználók licenceinek kívül? Egyes felhasználók rendelkeznek ütköző licenc, amely megakadályozza, hogy azok örökli a csoport licencek termékváltozatok?

3. Helyszíni néhány felhasználó, ellenőrizze, hogy rendelkeznek-e mind a direct- és licenceket alkalmazott ellenőrizze. Lépjen a felhasználó, válassza a panel **licencek**, és vizsgálja meg a licencek állapotát.

   - Ez az a várt felhasználói állapot áttelepítése során:

      ![a várt felhasználói állapot áttelepítése során](./media/licensing-groups-migrate-users/expected-user-state.png)

   Ezzel megerősíti, hogy a felhasználó rendelkezik-e közvetlen és az örökölt licenceket. Láthatjuk, hogy mindkét **EMS** és **E3** vannak hozzárendelve.

   - Válassza ki az egyes licencekhez az engedélyezett szolgáltatások részleteinek megjelenítéséhez. Ez használható ellenőrizheti, ha a direct- és licenceket engedélyezze a pontosan az azonos service-csomagok a felhasználó számára.

      ![Ellenőrizze a felhasználó service-csomagok](./media/licensing-groups-migrate-users/check-service-plans.png)

4. Miután meggyőződött arról, hogy közvetlen és a csoport licencek egyenértékűek, elindíthatja a felhasználók közvetlen licenceinek eltávolításával. Tesztelje az egyéni felhasználók számára a portál eltávolításával, és majd a tömeges eltávolítja őket az automatizálási szkriptek futtatásához. Íme egy példa ugyanaz a felhasználó eltávolítja a portálon keresztül közvetlen licencekkel. Figyelje meg, hogy a licenc állapotát változatlan marad, de már nem látható a közvetlen hozzárendelések.

   ![Győződjön meg arról, hogy a rendszer eltávolítja a közvetlen licenc](./media/licensing-groups-migrate-users/direct-licenses-removed.png)

## <a name="next-steps"></a>További lépések

Licenc felügyeleti csoportok használatával kapcsolatos egyéb forgatókönyvek kapcsolatos további információkért olvassa el

* [Mit jelent a Csoportalapú licencelés az Azure Active Directoryban?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
* [Licencek hozzárendelése egy csoporthoz az Azure Active Directoryban](licensing-groups-assign.md)
* [A csoportok licencproblémáinak azonosítása és megoldása az Azure Active Directoryban](licensing-groups-resolve-problems.md)
* [Felhasználók az Azure Active Directoryban Csoportalapú licencelést használ terméklicencek közötti migrálása](licensing-groups-change-licenses.md)
* [Az Azure Active Directory csoportalapú licencelésének további forgatókönyvei](licensing-group-advanced.md)
* [PowerShell forgatókönyvek Csoportalapú licenceléshez az Azure Active Directoryban](licensing-ps-examples.md)
