---
title: Kimenő IP-cím módosítása – az Azure App Service előkészítése
description: Ha módosítani kell a kimenő IP-cím, megtudhatja, mit kell tennie, hogy az alkalmazása továbbra is működik, a módosítás után.
services: app-service\web
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.topic: article
ms.date: 06/28/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: ac62217af096653d61a79ff29ae352c8e950f8af
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "61269769"
---
# <a name="how-to-prepare-for-an-outbound-ip-address-change"></a>Hogyan készíti elő a kimenő IP-cím módosítása

Ha módosítja a kimenő IP-címek az Azure App Service-alkalmazás értesítést kapott, kövesse a cikkben.

## <a name="determine-if-you-have-to-do-anything"></a>Ha meg semmit sem kell tennie meghatározása

* Option 1: Ha az App Service-alkalmazás nem használja az IP-szűrés, explicit listában vagy a kimenő forgalom-útválasztást vagy a tűzfal például különleges kezelést, semmit nem kell.

* Option 2: Ha az alkalmazása különleges kezelést a kimenő IP-címeit (lásd az alábbi példákat), az új kimenő IP-címek hozzáadása, bárhol is a meglévőket jelennek meg. Ne cserélje le a meglévő IP-címek. Az új kimenő IP-címek az alábbi utasításokat a következő szakaszban találja.

  Például előfordulhat, hogy kimenő IP-cím explicit módon szerepel az alkalmazáson kívüli tűzfal, vagy egy külső fizetési szolgáltatás előfordulhat, hogy rendelkezik, amely tartalmazza a kimenő IP-címet az alkalmazás engedélyezett listával. Ha a kimenő címét egy listában bárhol az alkalmazáson kívül van konfigurálva, hogy kell módosítani.

## <a name="find-the-outbound-ip-addresses-in-the-azure-portal"></a>Az Azure portálon található – a kimenő IP-címek

Az új kimenő IP-címek láthatók a portálon életbe. Amikor Azure elindul, az újakat használatával, a régieket már nem használható. Egyszerre csak egy set szolgál, így listáihoz bejegyzést kell rendelkeznie a régi és új IP-címeket, hogy a kimaradás, ha a kapcsoló történik. 

1.  Nyissa meg az [Azure Portalt](https://portal.azure.com).

2.  A bal oldali navigációs menüben válassza ki a **App Services**.

3.  Válassza ki az App Service-alkalmazást a listából.

1.  Ha az alkalmazás egy függvényalkalmazást, lásd: [alkalmazás kimenő IP-címek függvény](../azure-functions/ip-addresses.md#find-outbound-ip-addresses).

4.  Alatt a **beállítások** fejléc, kattintson a **tulajdonságok** a bal oldali navigációs és a keresés feliratú szakasz **kimenő IP-címeket**.

5. Másolja ki az IP-címek, és hozzáadhatja őket a különleges kezelést, a kimenő forgalom például egy szűrő vagy engedélyezettek listájához. Ne törölje a meglévő IP-címeket a listában.

## <a name="next-steps"></a>További lépések

Ez a cikk részletesen egy IP-cím módosítása az Azure által kezdeményezett előkészítése. IP-címek az Azure App Service kapcsolatos további információkért lásd: [bejövő és kimenő IP-címek az Azure App Service](overview-inbound-outbound-ips.md).
