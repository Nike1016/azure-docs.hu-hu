---
title: Threat Modeling Tool kiadások – Microsoft Threat Modeling Tool – Azure | Microsoft Docs
description: A veszélyforrások modellezése eszköz kibocsátási megjegyzéseit dokumentálja
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2019
ms.author: jegeib
ms.openlocfilehash: 6992ac58ebf8976714e5a478dd7b886efab2a4b7
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934791"
---
# <a name="threat-modeling-tool-update-release-71604081---492019"></a>Threat Modeling Tool Update kiadás 7.1.60408.1-4/9/2019

A Microsoft Threat Modeling Tool (TMT) 7.1.60408.1 verziója április 9 2019-én megjelent, és a következő módosításokat tartalmazza:

- Új rajzsablonok a Azure Key Vault és az Azure Traffic Manager
- Az TMT verziószáma mostantól megjelenik a kezdőképernyőn
- A támogatási hivatkozások frissítve lettek
- Hibajavítások

## <a name="feature-changes"></a>Szolgáltatások módosításai

### <a name="new-stencils-for-azure-key-vault-and-azure-traffic-manager"></a>Új rajzsablonok a Azure Key Vault és az Azure Traffic Manager

![Azure Key Vault stencil](./media/threat-modeling-tool-releases-71604081/tmt_keyvault_trafficmanager.PNG)

Az Azure-beli Azure Key Vault és az Azure-Traffic Manager új rajzsablonok és fenyegetések is megjelennek. Amikor az Azure-beli rajzsablonon alapuló modelleket nyit meg, a rendszer kérni fogja a felhasználókat a modellhez társított sablon frissítésére. A modellek Azure-beli rajzsablonon alapuló frissítése manuálisan is elindítható a "fájl" menü "sablon alkalmazása" parancsának használatával, és újraalkalmazhatja a legújabb Azure Cloud Services. tb7 fájlt.

### <a name="tmt-version-number-is-now-shown-on-the-home-screen"></a>Az TMT verziószáma mostantól megjelenik a kezdőképernyőn

A könnyű elérés érdekében a Threat Modeling Tool ügyfél-verziója mostantól megjelenik a kezdőképernyő alkalmazásának képernyőjén.

![Azure Key Vault stencil](./media/threat-modeling-tool-releases-71604081/tmt_version.PNG)

### <a name="support-links-have-been-updated"></a>A támogatási hivatkozások frissítve lettek

Az eszközön belüli összes támogatási hivatkozás frissítve lett, és [tmtextsupport@microsoft.com](mailto:tmtextsupport@microsoft.com) nem MSDN-fórumként irányítja a felhasználókat.

## <a name="system-requirements"></a>Rendszerkövetelmények

- Támogatott operációs rendszerek
  - [Microsoft Windows 10 évfordulós frissítés](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) vagy újabb
- .NET-verzió szükséges
  - [.Net-4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) vagy újabb
- További követelmények
  - Az eszköz és a sablonok frissítéseinek fogadásához internetkapcsolat szükséges.

## <a name="documentation-and-feedback"></a>Dokumentáció és visszajelzés

- A Threat Modeling Tool dokumentációja a [docs.microsoft.com](threat-modeling-tool.md)található, és [az eszköz használatával](threat-modeling-tool-getting-started.md)kapcsolatos információkat tartalmaz.

## <a name="next-steps"></a>További lépések

Töltse le a [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool)legújabb verzióját.
