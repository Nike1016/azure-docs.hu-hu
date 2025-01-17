---
title: Az Azure-ban Linux rendszerű virtuális gépek rendelkezésre állásának kezelése |} A Microsoft Docs
description: Több virtuális gép használata a Linux-alkalmazás az Azure-ban magas rendelkezésre állásának biztosításához
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 891c852a-84c0-4940-a61e-ada6e185bf37
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 914efbed2d65468cbbbccb3f62f9c21b52c38e42
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67667692"
---
# <a name="manage-the-availability-of-linux-virtual-machines"></a>Linux rendszerű virtuális gépek rendelkezésre állásának kezelése

Ismerje meg, hogyan és felügyelhet több virtuális gépet a Linuxos alkalmazás az Azure-ban magas rendelkezésre állásának biztosításához. Emellett [Windows virtuális gépek rendelkezésre állásának kezelése](../windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Egy rendelkezésre állási csoportot a parancssori felület használatával a Resource Manager-alapú üzemi modellben létrehozásával kapcsolatos útmutatóért lásd: [azure availset: a rendelkezésre állási készletek felügyelete parancsokat](../azure-cli-arm-commands.md#azure-availset-commands-to-manage-your-availability-sets).

[!INCLUDE [virtual-machines-common-manage-availability](../../../includes/virtual-machines-common-manage-availability.md)]

## <a name="next-steps"></a>További lépések
A virtuális gépek terheléselosztásáról további információkért lásd: [virtuális gépek terheléselosztását](../virtual-machines-linux-load-balance.md).

