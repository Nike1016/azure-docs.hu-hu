---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines-windows, virtual-machines-linux
author: dlepow
ms.service: multiple
ms.topic: include
ms.date: 10/09/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 4d2235eaea457c89d01a632afa5dd5a862bec344
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67179111"
---
## <a name="deploy-an-image-with-marketplace-terms"></a>Rendszerkép üzembe helyezése a Marketplace használati feltételei

Egyes Virtuálisgép-rendszerképek az Azure Marketplace-en további licenccel rendelkezik, és beszerzési feltételeit, hogy el kell fogadnia programozott módon központi telepítésük előtt.  

Virtuális gép üzembe helyezése egy rendszerképből, kell fogadniuk a feltételeket a lemezkép és a programozott telepítés engedélyezése is. Csak egyszer előfizetésenként ehhez szüksége. Ezután minden alkalommal, amikor telepít egy virtuális Gépet is kell adnia a kép a programozott módon *megvásárlása* paramétereket.

A következő szakaszok show hogyan:

* Ismerje meg, hogy rendelkezik-e Piactéri lemezképet további licencfeltételek 
* Fogadja el a használati programozott módon
* Adja meg a beszerzési terv paramétereket, virtuális gépek programozott módon üzembe

