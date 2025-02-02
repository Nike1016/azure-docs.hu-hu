---
title: Folyamatos integráció és az Azure Stream Analytics CI/CD npm-csomag fejlesztése
description: Ez a cikk ismerteti, hogyan állítsa be a folyamatos integráció és üzembe helyezési folyamat Azure Stream Analytics CI/CD npm-csomag használatával.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: fa5a57afa379c6bbe027be80f400fc176800d289
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66158501"
---
# <a name="continuously-integrate-and-develop-with-stream-analytics-cicd-npm-package"></a>Folyamatos integráció és a Stream Analytics CI/CD npm-csomag fejlesztése
Ez a cikk ismerteti, hogyan állítsa be a folyamatos integráció és üzembe helyezési folyamat az Azure Stream Analytics CI/CD npm-csomag használatával.

## <a name="build-the-vs-code-project"></a>A VS Code-projekt létrehozása

Folyamatos integráció és üzembe helyezés az Azure Stream Analytics-feladatok használatával engedélyezheti a **asa-streamanalytics-CI/CD** npm-csomag. Az npm-csomag biztosítja az eszközöket, az Azure Resource Manager-sablonok készítése [Stream Analytics Visual Studio Code-projektek](quick-create-vs-code.md). Használat Windows, macOS és Linux rendszereken a Visual Studio Code telepítése nélkül.

Miután [letölti a csomagot](https://www.npmjs.com/package/azure-streamanalytics-cicd), használja az alábbi parancsot a kimenetben az Azure Resource Manager-sablonok. A **scriptPath** argumentum az abszolút elérési útját a **asaql** fájlt a projektben. Győződjön meg arról, hogy a asaproj.json és JobConfig.json fájlok ugyanabban a mappában, a parancsfájl-fájllal. Ha a **outputPath** nincs megadva, a sablonok kerülnek a **telepítés** mappát a projekt **bin** mappát.

```powershell
azure-streamanalytics-cicd build -scriptPath <scriptFullPath> -outputPath <outputPath>
```
Példa (a macOS)
```powershell
azure-streamanalytics-cicd build -scriptPath "/Users/roger/projects/samplejob/script.asaql" 
```

Ha egy Stream Analytics Visual Studio Code-projektet sikeresen létrejött, a következő két Azure Resource Manager sablon fájlok mellett állít elő a **bin / [hibakeresési/kiskereskedelmi] és üzembe helyezése** mappa: 

*  Resource Manager-sablonfájl

       [ProjectName].JobTemplate.json 

*  Resource Manager-paraméterfájl

       [ProjectName].JobTemplate.parameters.json   

Az alapértelmezett paramétereket a parameters.json fájlban vannak a Visual Studio Code-projektben a beállításokat. Ha azt szeretné, egy másik környezetben való üzembe helyezéséhez, annak megfelelően cserélje le a paramétereket.

> [!NOTE]
> Az összes a hitelesítő adatokat, az alapértelmezett értékek vannak beállítva a null. Ön **szükséges** értékeinek beállítását a felhő üzembe helyezése előtt.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```
Ismerje meg, hogyan [üzembe helyezése Resource Manager-sablonfájlban és Azure PowerShell-lel](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). Ismerje meg, hogyan [objektum használata paraméterként a Resource Manager-sablonnal](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters).

Felügyelt identitás használatára az Azure Data Lake Store Gen1 kimeneti fogadóként, meg kell hozzáférést biztosítania a szolgáltatásnévnek előtt üzembe helyezni az Azure PowerShell használatával. Ismerje meg, hogyan [ADLS Gen1 felügyelt identitással üzembe helyezése a Resource Manager-sablonnal](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment).
## <a name="next-steps"></a>További lépések

* [Rövid útmutató: Az Azure Stream Analytics felhőalapú feladat létrehozása a Visual Studio Code (előzetes verzió)](quick-create-vs-code.md)
* [Tesztelje a Stream Analytics-lekérdezések helyileg a Visual Studio Code (előzetes verzió)](vscode-local-run.md)
* [Ismerkedés az Azure Stream Analytics Visual Studio Code-dal (előzetes verzió)](vscode-explore-jobs.md)
