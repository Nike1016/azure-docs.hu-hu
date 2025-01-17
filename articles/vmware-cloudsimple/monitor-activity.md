---
title: Azure VMware-megoldás CloudSimple szerint – saját Felhőbeli tevékenység figyelése
description: Az Azure VMware megoldás CloudSimple-környezettel, például riasztásokkal, eseményekkel, feladatokkal és naplózással kapcsolatos tevékenységeit ismerteti.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/13/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ddb3741c987e839fafb8bc222231547988d72f01
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69543751"
---
# <a name="monitor-vmware-solution-by-cloudsimple-activity"></a>VMware-megoldás figyelése CloudSimple-tevékenység alapján

A CloudSimple-tevékenység naplói betekintést nyújtanak a CloudSimple-portálon végzett műveletekre.  A lista riasztásokat, eseményeket, feladatokat és naplózást tartalmaz.  A tevékenységi naplók segítségével meghatározhatja, hogy ki, mikor és milyen műveleteket hajtottak végre.  A Tevékenységnaplók nem tartalmazzák a felhasználó által végzett olvasási műveleteket.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="access-the-cloudsimple-portal"></a>Hozzáférés a CloudSimple portáljához

Nyissa meg a [CloudSimple portált](access-cloudsimple-portal.md).

## <a name="activity-information"></a>Tevékenység adatai

A tevékenység oldalaihoz való hozzáféréshez válassza az oldalsó menü **tevékenység** elemét.

![A tevékenységek oldalának áttekintése](media/activity-page-overview.png)

A tevékenység lapon található tevékenységek részleteinek megtekintéséhez válassza ki a tevékenységet. A jobb oldalon megnyílik a részletek panel. A panel műveletei a tevékenység típusától függenek. A panel bezárásához kattintson az **X** gombra.

Kattintson egy oszlop fejlécére a Megjelenítés rendezéséhez.  A megtekinthető adott értékek oszlopai szűrhetők.  Töltse le a tevékenység jelentését a **Letöltés CSV-ként** elemre kattintva.

## <a name="alerts"></a>Riasztások

A riasztások a CloudSimple-környezetben felmerülő jelentős tevékenységekről szóló értesítések.  A riasztások olyan eseményeket érintenek, amelyek befolyásolják a számlázási vagy a felhasználói hozzáférést.

A riasztások elfogadásához és a listából való eltávolításához válasszon ki egyet vagy többet a listából, majd kattintson az **nyugtázás**gombra.

A riasztásokhoz a következő oszlopokban találhatók információk. Kattintson az **Oszlopok szerkesztése** elemre, és válassza ki a megtekinteni kívánt oszlopokat.

| Oszlop | Leírás |
------------ | ------------- |
| Riasztás típusa | A riasztás kategóriája.|
| Time | A riasztás ideje. |
| severity | A riasztás jelentősége.|
| Erőforrás neve | Az erőforráshoz hozzárendelt név, például a saját felhő neve. |
| Erőforrástípus | Erőforrás kategóriája: Saját felhő, felhőalapú állvány. |
| Erőforrás-azonosító | Az erőforrás azonosítója. |
| Leírás | A riasztást kiváltó esemény leírása. |
| Arra vonatkozik | Annak jelzése, hogy a riasztás igazolva van-e. |

## <a name="events"></a>Events

Az események a CloudSimple-portál felhasználói és rendszertevékenységeit mutatják be. Az események lap felsorolja az adott erőforráshoz társított tevékenységet és a hatás súlyosságát.

A riasztásokhoz a következő oszlopokban találhatók információk. Kattintson az **Oszlopok szerkesztése** elemre, és válassza ki a megtekinteni kívánt oszlopokat.

| Oszlop | Leírás |
------------ | ------------- |
| Time | Az esemény dátuma és időpontja. |
| Eseménytípus | Az eseményt azonosító numerikus kód. |
| severity | Esemény súlyossága.|
| Erőforrás neve | Az erőforráshoz hozzárendelt név, például a saját felhő neve. |
| Erőforrástípus | Erőforrás kategóriája: Saját felhő, felhőalapú állvány. |
| Leírás | A riasztást kiváltó esemény leírása. |

## <a name="tasks"></a>Feladatok

A feladatok olyan magánjellegű felhőalapú tevékenységek, amelyek várhatóan 30 másodpercet vagy annál nagyobb időt vesznek igénybe. (A várhatóan 30 másodpercnél rövidebb tevékenységek jelentése csak eseményként történik.) Nyissa meg a feladatok lapokat a privát felhőhöz tartozó feladatok állapotának nyomon követéséhez.

A riasztásokhoz a következő oszlopokban találhatók információk. Kattintson az **Oszlopok szerkesztése** elemre, és válassza ki a megtekinteni kívánt oszlopokat.

| Oszlop | Leírás |
------------ | ------------- |
| Feladatazonosító | A feladat egyedi azonosítója. |
| Művelet | A feladat által végrehajtott művelet. |
| Felhasználó | A feladat végrehajtásához hozzárendelt felhasználó. |
| Erőforrás neve | Az erőforráshoz rendelt név. |
| Erőforrástípus | Erőforrás kategóriája: Saját felhő, felhőalapú állvány. |
| Erőforrás-azonosító | Az erőforrás azonosítója. |
| Start | A feladat kezdési időpontja. |
| vége | A feladat befejezési időpontja. |
| Állapot | Aktuális feladat állapota. |
| Eltelt idő | Az az idő, ameddig a feladat befejeződik (ha elkészült) vagy éppen folyamatban van (ha folyamatban van). |
| Leírás | Feladat leírása. |

## <a name="audit"></a>Naplózás

A naplók nyomon követhetik a felhasználói tevékenységeket. A naplók segítségével figyelheti a felhasználói tevékenységeket az összes felhasználó számára.

A riasztásokhoz a következő oszlopokban találhatók információk. Kattintson az **Oszlopok szerkesztése** elemre, és válassza ki a megtekinteni kívánt oszlopokat.

| Oszlop | Leírás |
------------ | ------------- |
| Time | A naplóbejegyzés időpontja. |
| Művelet | A feladat által végrehajtott művelet. |
| Felhasználó | A feladathoz hozzárendelt felhasználó. |
| Erőforrás neve | Az erőforráshoz rendelt név. |
| Erőforrástípus | Erőforrás kategóriája: Saját felhő, felhőalapú állvány. |
| Erőforrás-azonosító | Az erőforrás azonosítója. |
| Eredmény | A tevékenység eredménye, például **sikeres**. |
| Szükséges idő | A feladat elvégzéséhez szükséges idő. |
| Leírás | A művelet leírása. |

## <a name="next-steps"></a>További lépések

* [VMware virtuális gépek használata az Azure-ban](quickstart-create-vmware-virtual-machine.md)
* További információ a [privát felhőkről](cloudsimple-private-cloud.md)
