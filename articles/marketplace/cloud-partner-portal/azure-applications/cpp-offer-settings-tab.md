---
title: Az Azure ajánlat Alkalmazásbeállítások |} Az Azure Marketplace-en
description: Az Azure-alkalmazás ajánlat az ajánlat beállításainak konfigurálása.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pabutler
ms.openlocfilehash: 789b783629b3cc3528eba1883b21051604cf6e14
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64942936"
---
# <a name="azure-application-offer-settings-tab"></a>Az Azure application ajánlat beállítások lap

Ez a cikk ismerteti az Azure-alkalmazások az ajánlat beállításainak konfigurálása.

A **Azure-alkalmazások > Új ajánlat** oldal megnyílik az aktuális az a **ajánlat beállítások** fülre. Egy csillag (*) hozzáfűzi a mező nevét jelzi, hogy ez szükséges.

![Az ajánlat identitás űrlap](./media/azureapp-offer-settings-tab.png)

## <a name="offer-identity-settings"></a>Az ajánlat nastavení Identity

A **ajánlat identitás**, meg kell adnia a mezők kitöltése a következő táblázat ismerteti.  

|    Mező         |       Leírás                                                            |
|  ---------       |     ---------------                                                          |
| **Ajánlat azonosítója\***       | Az ajánlat egyedi azonosítóját (a közzétevő profil) belül. Ez az azonosító a termék URL-címek és insights jelentésekben látható lesz. Maximális hossza legfeljebb 50 karakter hosszúságú, és a kisbetűs alfanumerikus karaktereket és kötőjeleket (-). (Az azonosító nem végződhet kötőjellel.) **Megjegyzés:** Ez a mező nem módosítható, miután ajánlat élesíti. <br> Például, ha a Contoso tesz közzé egy ajánlatot az ajánlat azonosítója **minta-tároló**, az Azure Marketplace-en URL-cím által megadott `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-container?tab=Overview`. |
| **Közzétevő azonosítója\***     | A szervezet egyedi azonosítója az Azure piactéren. Az ajánlatokat társítható a közzétevő-azonosítót. Ez az érték nem lehet módosítani, az ajánlat mentése után. |
| **Név\***          | Az ajánlat megjelenített neve. Ez a név jelenik meg az Azure piactéren, és a Cloud Partner portálra. Legfeljebb 50 karakterből állhat. A termék egy könnyen felismerhető névre márkanév használatát javasoljuk. A szervezet neve ne tartalmazza, kivéve, ha ezt hogyan a forgalmazás. Ha ez az ajánlat egyéb webhelyeken és kiadványok vannak marketing, győződjön meg arról, hogy a név pontosan azonos között az összes olyan. |
|  |  |

Válassza ki **mentése** az ajánlat beállításainak mentése.

## <a name="next-steps"></a>További lépések

Használja a [termékváltozatok](./cpp-skus-tab.md) lap segítségével konfigurálhatja a termékváltozatok az ajánlatban.