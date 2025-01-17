---
title: Azure Cloud Services definíciós séma (. cscfg fájl) | Microsoft Docs
services: cloud-services
ms.custom: ''
ms.date: 12/07/2016
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 35
author: georgewallace
ms.author: gwallace
ms.openlocfilehash: 0009f843f8de31b92817dc86ccd718fa5eeeb1ba
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68358928"
---
# <a name="azure-cloud-services-config-schema-cscfg-file"></a>Azure Cloud Services konfigurációs séma (. cscfg fájl)
A szolgáltatás konfigurációs fájlja meghatározza a szolgáltatás egyes szerepköreihez telepítendő szerepkör-példányok számát, a konfigurációs beállítások értékeit, valamint a szerepkörhöz társított tanúsítványok ujjlenyomatai megfelelnek. Ha a szolgáltatás egy Virtual Network része, a hálózat konfigurációs adatait meg kell adni a szolgáltatás konfigurációs fájljában, valamint a virtuális hálózat konfigurációs fájljában. A szolgáltatás konfigurációs fájljának alapértelmezett kiterjesztése a. cscfg.

A szolgáltatási modellt a [Cloud Service (klasszikus) definíciós sémája](schema-csdef-file.md)írja le.

Alapértelmezés szerint a rendszer a Azure Diagnostics konfigurációs sémafájl fájlját telepíti a `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas` könyvtárba. Cserélje `<version>` le az helyére az [Azure SDK](https://azure.microsoft.com/downloads/)telepített verzióját.

A szerepkörök szolgáltatásban való konfigurálásával kapcsolatos további információkért lásd: [Mi a Cloud Service-modell](cloud-services-model-and-package.md).

## <a name="basic-service-configuration-schema"></a>Alapszintű szolgáltatás konfigurációs sémája
A szolgáltatás konfigurációs fájljának alapszintű formátuma a következő.

```xml
<ServiceConfiguration serviceName="<service-name>" osFamily="<osfamily-number>" osVersion="<os-version>" schemaVersion="<schema-version>">

  <Role …>
    …
  </Role>

  <NetworkConfiguration>
    …
  </NetworkConfiguration>

</ServiceConfiguration>
```

## <a name="schema-definitions"></a>Séma-definíciók
A következő témakörök a `ServiceConfiguration` elem sémáját írják le:

- [Szerepkörséma](schema-cscfg-role.md)
- [NetworkConfiguration séma](schema-cscfg-networkconfiguration.md)

## <a name="service-configuration-namespace"></a>Szolgáltatás konfigurációs névtere
A szolgáltatás konfigurációs fájljának XML-névtere a következő `http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration`:.

##  <a name="ServiceConfiguration"></a>ServiceConfiguration elem
Az `ServiceConfiguration` elem a szolgáltatás konfigurációs fájljának legfelső szintű eleme.

Az alábbi táblázat az `ServiceConfiguration` elem attribútumait ismerteti. Minden attribútum értéke karakterlánc típusú.

| Attribútum | Leírás |
| --------- | ----------- |
|serviceName|Kötelező. A felhőalapú szolgáltatás neve. Az itt megadott névnek egyeznie kell a szolgáltatás definíciós fájljában megadott névvel.|
|osFamily|Választható. Meghatározza azt a vendég operációs rendszert, amely a felhőalapú szolgáltatásban a szerepkör példányain fog futni. A támogatott vendég operációs rendszer kiadásaival kapcsolatos információkért lásd: az [Azure vendég operációs rendszer kiadásai és az SDK kompatibilitási mátrixa](cloud-services-guestos-update-matrix.md).<br /><br /> Ha nem tartalmaz `osFamily` értéket, és nem állította be az `osVersion` attribútumot egy adott vendég operációsrendszer-verzióra, a rendszer az alapértelmezett 1 értéket használja.|
|osVersion|Választható. Megadja a vendég operációs rendszernek azt a verzióját, amely a felhőalapú szolgáltatásban a szerepkör példányain fog futni. A vendég operációs rendszer verziójával kapcsolatos további információkért lásd: az [Azure vendég operációs rendszer kiadásai és az SDK kompatibilitási mátrixa](cloud-services-guestos-update-matrix.md).<br /><br /> Megadhatja, hogy a vendég operációs rendszer automatikusan a legújabb verzióra legyen frissítve. Ehhez állítsa az `osVersion` `*`attribútum értékét a következőre:. Ha a értékre `*`van állítva, a rendszer telepíti a szerepkör példányait a vendég operációs rendszer legújabb verziójára a megadott operációsrendszer-családra vonatkozóan, és a rendszer automatikusan frissíti a vendég operációs rendszer új verzióinak kiadásakor.<br /><br /> Egy adott verzió manuális megadásához használja az `Configuration String` [Azure vendég operációs rendszer kiadásainak és az SDK-kompatibilitási mátrixának](cloud-services-guestos-update-matrix.md) **jövőbeli, aktuális és átmeneti vendég operációsrendszer-verziók** szakaszát.<br /><br /> `osVersion` Az`*`attribútum alapértelmezett értéke:.|
|sémaverzióval|Választható. Megadja a szolgáltatás konfigurációs sémájának verzióját. A séma verziója lehetővé teszi a Visual Studio számára, hogy kiválassza a séma-ellenőrzéshez használandó megfelelő SDK-eszközöket, ha az SDK több verziója is telepítve van egymás mellett. További információ a séma és a verziók kompatibilitásáról: az [Azure vendég operációs rendszer kiadásai és az SDK kompatibilitási mátrixa](cloud-services-guestos-update-matrix.md)|

A szolgáltatás konfigurációs fájljának egyetlen `ServiceConfiguration` elemet kell tartalmaznia. Az `ServiceConfiguration` elem tetszőleges `Role` számú elemet és nulla vagy 1 `NetworkConfiguration` elemet tartalmazhat.