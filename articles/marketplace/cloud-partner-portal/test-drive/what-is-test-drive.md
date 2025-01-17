---
title: Mi a Tesztverzió? | Az Azure Marketplace-en
description: Marketplace-en Test Drive funkció ismertetése
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Ricardo.Villalobos
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 5b23a5e93dcbb9c13243240bda760a16d108d731
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64938459"
---
<a name="what-is-test-drive"></a>Mi a Tesztverzió?
===================

Test Drive kiválóan alkalmas lehetőségét, így az Ön ajánlatát a lehetséges ügyfelek bemutatására \'vásárlás előtt próbálja\', ez pedig nagyobb konverziós és magasan képzett érdeklődők generációja.

Miután megadta a kapcsolatfelvételi adatait, ügyfelek elérhetik az előre elkészített Test Drive felhasználói élményt: gyakorlati, önálló irányítású próbaverzió a termék\'s főbb funkcióiról és megvalósítási valós forgatókönyvekben a bemutatott előnyöket.

Test Drive életre telepítheti a termékeket és magasan képzett felkeltésére a folyamat lehetővé teszi.

<a name="how-does-a-test-drive-work"></a>Hogyan működik a Test Drive?
---------------------------

A potenciális ügyfelek felderíti az alkalmazás a Marketplace-en, bejelentkezik, és elfogadja a használati feltételeket. Ezen a ponton az ügyfél megkapja az előre konfigurált környezet rögzített számú óra, próbálja ki a szerepeltetendő magas minősített érdeklődő kap közben.

![Első lépés. Megjelenik a Piactéri ajánlat](./media/what-is-test-drive/step1.png)

![Két lépést. Piactéri ajánlat bejelentkezési képernyő](./media/what-is-test-drive/step1andahalf.png)

![Három lépésben. Piactéri ajánlat kiadói szerződés képernyő](./media/what-is-test-drive/step2.png)

![Négy lépést. Test Drive beállítása képernyőre](./media/what-is-test-drive/step3.png)

És az alábbi Íme egy példa, hogyan néz ki egy ajánlatot a üzembe helyezéséhez szükséges időt, ha:

![Telepített Marketplace-ajánlat](./media/what-is-test-drive/step4.png)

![Marketplace-en Tesztverzió készen képernyő](./media/what-is-test-drive/step5.png)

![Marketplace-en Test Drive befejeződött képernyő](./media/what-is-test-drive/step6.png)

Függetlenül attól, hogy bonyolult az alkalmazás a Microsoft Test Drive segít a termék keltse életre az ügyfél számára. Ma a Tesztverzió alapján a termék, a forgatókönyv és a piactér három különböző típusú biztosítunk.

- **[Az Azure Resource Manager](./azure-resource-manager-test-drive.md)** : Egy Azure Resource Manager Test Drive egy központi telepítési sablont, amely tartalmazza az összes Azure-erőforrást, amely tartalmazza a megoldás a közzétevő létrehozása folyamatban. Ebben a forgatókönyvben igazodó termékek, amelyekről csak Azure-erőforrások használatához.
- **[Logikai alkalmazás](./logic-app-test-drive.md)** : Egy logikai alkalmazás Tesztverziós, amelynek szinkronban kell foglalnia az összes összetett megoldások létrehozására szolgáló architektúrák központi telepítési sablont. Az összes Dynamics-alkalmazások vagy egyéni termékek Test Drive az ilyen típusú kell használnia.
- **Power BI**: A Power bi-ban próbálja ki az egyéni kialakítású irányítópulton egyszerűen egy beágyazott kapcsolat. Minden olyan terméket, amely egyszerűbb egy interaktív Power BI vizuális kell használnia a Test Drive az ilyen típusú megoldást szeretne.
    Fel kell töltenie az itt csak a Power BI embedded URL-CÍMÉT.

<a name="what-goes-on-in-the-background"></a>Mi hová kerül a háttérben?
-------------------------------

A Test Drive szolgáltatás folyamatosan támogatja, és az ügyfelek szolgálnak anélkül, hogy bármely, manuális beavatkozást való használatra készült. Közzétevő kezeléséhez és a Test Drive theCloud Partneráttekintés beállításokat konfigurálni a feladat nem, és ezután ennek a beállításnak közvetlenül lesz elérhető az ügyfelek számára.

Ennek oka az, miután beállította a konfigurációkat a Test Drive esetében, minden egyes Test Drive a felügyelt példány az azt kérő ügyfelek igény szerinti üzembe helyezendő válik. Egy Test Drive-példány van hozzárendelve, miután a Test Drive érhető el a beállított időn számára, és majd törli az egy másik ügyfél felszabadításához.

<a name="next-steps"></a>További lépések
----------

Most, hogy tudja, milyen egy Test Drive lényege, lépjen látogasson el a Test Drive típusát, amely szeretne belépni, mindent megtudhat a kötelező mezőket közzététele szükséges.

- **[Az Azure Resource Manager](./azure-resource-manager-test-drive.md)**
- **[Logic App](./logic-app-test-drive.md)**

Ha további kérdése van, hibaelhárítási tanácsokat keres, vagy kívánja-e a Test Drive sokkal sikeresebb lenne, nyissa meg [– gyakori kérdések, hibaelhárítási és ajánlott eljárások](./marketing-and-best-practices.md).
