---
title: Konfigurációk konvertálása összetett erőforrásokra az állapot konfigurálásához – Azure Automation
description: Megtudhatja, hogyan alakíthatja át a konfigurációkat összetett erőforrásokra az Azure Automation állapotának konfigurálásához.
keywords: DSC, PowerShell, konfigurálás, beállítás
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d9e76532d41e23cba376755ca524ca6911385204
ms.sourcegitcommit: a6888fba33fc20cc6a850e436f8f1d300d03771f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69559377"
---
# <a name="convert-configurations-to-composite-resources"></a>Konfigurációk átalakítása összetett erőforrásokra

> A következőkre vonatkozik: Windows PowerShell 5,1

A konfigurációk létrehozási lépéseinek megkezdése után gyorsan létrehozhat olyan "forgatókönyveket", amelyek a beállítások csoportjait kezelik.
Ilyenek például a következők:

- webkiszolgáló létrehozása
- DNS-kiszolgáló létrehozása
- SharePoint-kiszolgáló létrehozása
- SQL-fürt konfigurálása
- tűzfalbeállítások kezelése
- jelszavas beállítások kezelése

Ha szeretné megosztani másokkal a munkát, a legjobb megoldás a konfiguráció [összetett erőforrásként](/powershell/dsc/resources/authoringresourcecomposite)való csomagolása.
Az összetett erőforrások első alkalommal történő létrehozása rendkívül nagy lehet.

> [!NOTE]
> Ez a cikk egy olyan megoldásra utal, amelyet a nyílt forráskódú közösség tart fenn.
> A támogatás csak GitHub-együttműködés formájában érhető el, nem a Microsofttól.

## <a name="community-project-compositeresource"></a>Közösségi projekt: CompositeResource

A probléma megoldásához létrehozott egy [CompositeResource](https://github.com/microsoft/compositeresource) nevű közösségi karbantartó megoldást.

A CompositeResource automatizálja a konfigurációban egy új modul létrehozásának folyamatát.
Első lépésként [](https://blogs.technet.microsoft.com/heyscriptingguy/2010/08/10/how-to-reuse-windows-powershell-functions-in-scripts/) a (z) (vagy Build kiszolgáló) konfigurációs parancsfájl beszerzése a munkaállomáson, hogy be legyen töltve a memóriában.
Ezután ahelyett, hogy a konfigurációt futtatja egy MOF-fájl létrehozásához, használja a CompositeResource modul által biztosított funkciót az átalakítás automatizálásához.
A parancsmag betölti a konfiguráció tartalmát, lekéri a paraméterek listáját, és létrehoz egy új modult, amire szüksége van.

Miután létrehozta a modult, megnövelheti a verziót, és hozzáadhat kibocsátási megjegyzéseket minden alkalommal, amikor módosításokat végez, és közzéteszi azt a saját [PowerShellGet](https://kevinmarquette.github.io/2018-03-03-Powershell-Using-a-NuGet-server-for-a-PSRepository/?utm_source=blog&utm_medium=blog&utm_content=psscriptrepo)-adattárában.

Miután létrehozta a konfigurációját (vagy több konfigurációját) tartalmazó összetett erőforrás-modult, használhatja azokat az Azure-ban a készíthető [authoring Experience](/azure/automation/compose-configurationwithcompositeresources) -ben, vagy hozzáadhatja őket a [DSC konfigurációs PARANCSFÁJLJAIhoz](/powershell/dsc/resources/configurations) a MOF-fájlok létrehozásához és [töltse fel a MOF-fájlokat](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation)a Azure Automationba.
Ezután regisztrálja a kiszolgálókat akár [a helyszínen](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azureaws) , akár [Az Azure-ban](/azure/automation/automation-dsc-onboarding#azure-virtual-machines) a konfigurációk lekéréséhez.
A projekt legújabb frissítése [runbookok](https://www.powershellgallery.com/packages?q=DscGallerySamples) is közzétett a Azure Automation számára, hogy automatizálja a konfigurációk importálásának folyamatát a PowerShell-galériaból.

A DSC-hez készült összetett erőforrások létrehozásának automatizálásához látogasson el a [PowerShell-galériare](https://www.powershellgallery.com/packages/compositeresource/) , és töltse le a megoldást, vagy kattintson a "Project site" (projekt helye) elemre a [dokumentáció](https://github.com/microsoft/compositeresource)megtekintéséhez.

## <a name="next-steps"></a>További lépések

- [A Windows PowerShell kívánt állapotának konfigurálása – áttekintés](/powershell/dsc/overview/overview)
- [DSC-erőforrások](/powershell/dsc/resources/resources)
- [A helyi Configuration Manager konfigurálása](/powershell/dsc/managing-nodes/metaconfig)
