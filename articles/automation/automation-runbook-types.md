---
title: Azure Automation Runbook-típusok
description: 'Ismerteti azokat a runbookok, amelyeket Azure Automation használhat, és figyelembe kell vennie, hogy milyen típusú használatot kell alkalmaznia. '
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 03/05/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e655e286c3aebe28bcb09c8723516c2ff52ad20e
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68850360"
---
# <a name="azure-automation-runbook-types"></a>Azure Automation runbook-típusok

A Azure Automation számos runbookok támogat, amelyek rövid leírása a következő táblázatban található.  Az alábbi szakaszokban további információkat talál az egyes típusokról, beleértve az egyes típusok használatának szempontjait is.

| Type | Leírás |
|:--- |:--- |
| [Grafikus](#graphical-runbooks)|A Windows PowerShell alapján, és a Azure Portal grafikus szerkesztőjében teljesen elkészült és szerkesztve. |
| [Grafikus PowerShell-munkafolyamat](#graphical-runbooks)|A Windows PowerShell-munkafolyamaton alapuló, és a Azure Portal grafikus szerkesztőjében teljesen elkészült és szerkesztve. |
| [PowerShell](#powershell-runbooks) |Szöveges forgatókönyv Windows PowerShell-szkript alapján. |
| [PowerShell-munkafolyamat](#powershell-workflow-runbooks)|Windows PowerShell-munkafolyamaton alapuló szöveges forgatókönyv. |
| [Python](#python-runbooks) |Szöveg runbook Python alapján. |

## <a name="graphical-runbooks"></a>Grafikus runbookok

A [grafikus](automation-runbook-types.md#graphical-runbooks) és a grafikus PowerShell-munkafolyamat runbookok a Azure Portal grafikus szerkesztőjével hozhatók létre és szerkeszthetők.  Exportálhatja őket egy fájlba, majd importálhatja őket egy másik Automation-fiókba. Azonban nem hozhat létre vagy szerkeszthet egy másik eszközzel. A grafikus runbookok PowerShell-kódot hoz, de nem tudja közvetlenül megtekinteni vagy módosítani a kódot. A grafikus runbookok nem alakíthatók át az egyik [szöveges formátumba](automation-runbook-types.md), és a szöveg runbook nem alakítható át grafikus formátumra. A grafikus runbookok az importálás során grafikus PowerShell-munkafolyamat-runbookok alakíthatók át, a másik pedig fordítva.

### <a name="advantages"></a>Előnyök

* Vizuális Beszúrás – hivatkozás – szerzői modell konfigurálása
* Koncentráljon arra, hogy az adatáramlás hogyan halad át a folyamaton
* Felügyeleti folyamatok vizuális ábrázolása
* Más runbookok belefoglalása alárendelt runbookok a magas szintű munkafolyamatok létrehozásához
* A moduláris programozás ösztönzése

### <a name="limitations"></a>Korlátozások

* A Azure Portalon kívüli runbook nem szerkeszthető.
* Az összetett logika végrehajtásához a PowerShell-kódot tartalmazó programkód-tevékenységre lehet szükség.
* Nem lehet megtekinteni vagy közvetlenül szerkeszteni a grafikus munkafolyamat által létrehozott PowerShell-kódot. Megtekintheti a létrehozott kódot bármely kód tevékenységben.
* Nem futtatható linuxos hibrid Runbook-feldolgozón

## <a name="powershell-runbooks"></a>PowerShell-forgatókönyvek

PowerShell-forgatókönyvek Windows Powershellen alapulnak.  Közvetlenül szerkesztheti a runbook kódját a Azure Portal szövegszerkesztő használatával.  Használhatja az offline szövegszerkesztőt is, és [importálhatja a runbook](manage-runbooks.md) Azure Automationba.

### <a name="advantages"></a>Előnyök

* PowerShell-kóddal további vesződni PowerShell-munkafolyamat minden összetett logikát alkalmazzák.
* Runbook, mint a PowerShell-munkafolyamati runbookok gyorsabban indul, mivel nincs szüksége futtatása előtt kell összeállítani.
* Az Azure-ban vagy Linux-és Windows Hybrid Runbook-feldolgozón is futtatható

### <a name="limitations"></a>Korlátozások

* Ismernie kell a PowerShell-parancsfájlok futtatását.
* A [párhuzamos feldolgozás](automation-powershell-workflow.md#parallel-processing) nem használható párhuzamosan több művelet végrehajtásához.
* Az [ellenőrzőpontok](automation-powershell-workflow.md#checkpoints) nem használhatók a runbook folytatására, ha hiba történt.
* A PowerShell-munkafolyamatok runbookok és grafikus runbookok csak alárendelt runbookok szerepelhetnek a Start-AzureAutomationRunbook parancsmaggal, amely létrehoz egy új feladatot.

### <a name="known-issues"></a>Ismert problémák

A következő ismert problémák a PowerShell-runbookok.

* A PowerShell-runbookok nem tud beolvasni egy titkosítatlan [változó](automation-variables.md) objektumot NULL értékkel.
* A PowerShell *~* -runbookok nem tud beolvasni egy [változót](automation-variables.md) a névben.
* A folyamat lekérése egy PowerShell-runbook egy hurokban az 80-es iteráció után összeomlhat.
* Előfordulhat, hogy egy PowerShell-runbook meghiúsul, ha nagy mennyiségű adatokat próbál meg a kimeneti adatfolyamba írni egyszerre.   Ezt a problémát általában úgy is megkerülheti, ha a nagyméretű objektumok használatakor csak a szükséges információkat helyezi üzembe.  Például ahelyett, hogy a lekéréses folyamathoz hasonló *műveletet*kellene végrehajtania, csak a szükséges mezőket kell kiadnia a *Get-Process szolgáltatással | Válassza a ProcessName, majd a CPU elemet*.

## <a name="powershell-workflow-runbooks"></a>PowerShell-munkafolyamat runbookok

A PowerShell-munkafolyamat runbookok a [Windows PowerShell](automation-powershell-workflow.md)-munkafolyamaton alapuló szöveges runbookok.  Közvetlenül szerkesztheti a runbook kódját a Azure Portal szövegszerkesztő használatával.  Használhatja az offline szövegszerkesztőt is, és [importálhatja a runbook](manage-runbooks.md) Azure Automationba.

### <a name="advantages"></a>Előnyök

* A PowerShell-munkafolyamati kód minden összetett logikát alkalmazzák.
* Az [ellenőrzőpontok](automation-powershell-workflow.md#checkpoints) használatával folytathatja a runbook, ha hiba történt.
* Használat [párhuzamos feldolgozási](automation-powershell-workflow.md#parallel-processing) párhuzamosan több művelet végrehajtásához.
* Más grafikus runbookok és PowerShell-munkafolyamatok runbookok is tartalmazhat, mint a magas szintű munkafolyamatok létrehozására szolgáló runbookok.

### <a name="limitations"></a>Korlátozások

* A szerzőnek ismernie kell a PowerShell-munkafolyamatot.
* A Runbook-nek a PowerShell-munkafolyamatok további összetettségét kell megfelelnie, például deszerializált [objektumokat](automation-powershell-workflow.md#code-changes).
* A Runbook elindításához, mint a PowerShell-forgatókönyvek, mivel fordítható futtatása előtt kell hosszabb időt vesz igénybe.
* A PowerShell-runbookok csak alárendelt runbookok szerepelhetnek a Start-AzureAutomationRunbook parancsmag használatával, amely létrehoz egy új feladatot.
* Nem futtatható linuxos hibrid Runbook-feldolgozón

## <a name="python-runbooks"></a>Python-runbookok

Python-runbookok fordítása a Python 2 alatt.  Közvetlenül szerkesztheti a runbook kódját a Azure Portal szövegszerkesztővel, vagy egy offline szövegszerkesztővel, és [importálhatja a runbook a](manage-runbooks.md) Azure Automationba.

### <a name="advantages"></a>Előnyök

* Használja a robusztus Python-kódtárakat.
* Az Azure-ban vagy a Linux hibrid Runbook-feldolgozókon is futtatható. A Windows Hybrid Runbook-feldolgozók a [Python 2.7](https://www.python.org/downloads/release/latest/python2) -es verzióval támogatottak.

### <a name="limitations"></a>Korlátozások

* Ismernie kell a Python-parancsfájlokat.
* Jelenleg csak a Python 2 támogatott, ami azt jelenti, hogy a Python 3 bizonyos funkciói sikertelenek lesznek.
* A külső gyártótól származó kódtárak használatához importálnia kell [a csomagot](python-packages.md) az Automation-fiókba ahhoz, hogy használni lehessen.

## <a name="considerations"></a>Megfontolandó szempontok

Vegye figyelembe a következő további szempontokat, amikor meghatározza, hogy melyik típust kell használni egy adott runbook.

* A runbookok nem konvertálható grafikusról szöveges típusra, vagy fordítva.
* A runbookok különböző típusú runbook használnak. További információ: [gyermek runbookok Azure Automation](automation-child-runbooks.md).

## <a name="next-steps"></a>További lépések

* További információ a grafikus runbook létrehozásáról: [grafikus szerzői műveletek Azure Automation](automation-graphical-authoring-intro.md)
* A runbookok PowerShell-és PowerShell-munkafolyamatai közötti különbségek megismeréséhez tekintse meg a [Windows PowerShell](automation-powershell-workflow.md) -munkafolyamatok megismerése című témakört.
* A Runbook létrehozásával és importálásával kapcsolatos további információkért lásd: [Runbook létrehozása vagy importálása](manage-runbooks.md)
* A PowerShell-lel kapcsolatos további információkért, beleértve a nyelvi referenciákat és a tanulási modulokat, tekintse át a [PowerShell-dokumentumokat](https://docs.microsoft.com/en-us/powershell/scripting/overview).
