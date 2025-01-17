---
title: Mi az a NetApp Azure-fájlok hierarchiája |} A Microsoft Docs
description: A cikk a tárhely-hierarchiát ismerteti, beleértve az Azure NetApp Files-fiókokat, -kapacitáskészleteket és -köteteket.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 04/16/2019
ms.author: b-juche
ms.openlocfilehash: c2984e012ae83a8bc17d72ed4eac0c5c469c2694
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2019
ms.locfileid: "65522870"
---
# <a name="what-is-the-storage-hierarchy-of-azure-netapp-files"></a>Mi az a NetApp Azure-fájlok hierarchiája

Mielőtt létrehozna egy kötetet az Azure NetApp Filesban, meg kell vásárolnia és be kell állítania egy készletet a kiosztott kapacitáshoz.  Kapacitáskészlet beállításához rendelkeznie kell NetApp-fiókkal. Az Azure NetApp Files tárhely-hierarchiájának megismerése segítségére lehet az Azure NetApp Files-erőforrások beállítása és kezelése során.

## <a name="azure_netapp_files_account"></a>NetApp-fiókok

- A NetApp-fiókok a kapacitáskészlet-tagok felügyeleti csoportosítására szolgálnak.  
- A NetApp-fiók nem egyezik meg az általános Azure Storage-fiókkal. 
- A NetApp-fiók regionális hatókörrel rendelkezik.   
- Több NetApp-fiókkal is rendelkezhet egy régióban, de minden NetApp-fiók egyetlen régióhoz van társítva.

## <a name="capacity_pools"></a>Kapacitáskészletek

- A kapacitáskészletek mérése a kiosztott kapacitás alapján történik.  
- A kapacitás a rögzített termékváltozatok (például egy 4-TiB kapacitás) vásárolt lett kiépítve.
- A kapacitáskészletek csak egy szolgáltatásszinttel rendelkezhetnek.  
- Minden kapacitás címkészlet NetApp csak egy fiók is tartozik. Azonban akkor is több kapacitás készletek NetApp-fiókon belül.  
- A kapacitáskészletek nem mozgathatók a NetApp-fiókok között.   
  Az alábbi, [tárhely-hierarchiát ábrázoló fogalmi diagramon](#conceptual_diagram_of_storage_hierarchy) például az 1. kapacitáskészlet nem helyezhető át az USA keleti régiójában lévő NetApp-fiókból az USA 2. nyugati régiójának NetApp-fiókjába.  
- Egy kapacitás készlet nem törölhető, amíg a kapacitás a készletben lévő összes kötetet, hogy törölték.

## <a name="volumes"></a>Kötetek

- A kötet logikai kapacitásfogyasztásának mérjük és skálázható. 
- A kötet kapacitásfogyasztása beleszámít a készlet kiosztott kapacitásába.
- Minden kötet egyetlen készlethez tartozik, de egy készlet több kötetet is tartalmazhat. 
- A kötet kapacitása készletek között nem lehet áthelyezni. <!--Within the same NetApp account, you can move a volume across pools.  -->   
  Például a [fogalmi diagramját, hierarchiája](#conceptual_diagram_of_storage_hierarchy) lent, nem válthat a kötetek kapacitása készlet 1 kapacitás Pool 2.
- A kötet nem lehet törölni, mindaddig, amíg az összes pillanatképet törölve lett.

## <a name="conceptual_diagram_of_storage_hierarchy"></a>Tárhely-hierarchia fogalmi diagramja 
Az alábbi példák az Azure-előfizetés, a NetApp-fiókok, a kapacitáskészletek és a kötetek közötti kapcsolatokat mutatják be.   

![Tárhely-hierarchia fogalmi diagramja](../media/azure-netapp-files/azure-netapp-files-storage-hierarchy.png)

## <a name="next-steps"></a>További lépések

- [Az Azure NetApp Files erőforráskorlátai](azure-netapp-files-resource-limits.md)
- [Regisztráljon a Azure NetApp-fájlok](azure-netapp-files-register.md)
