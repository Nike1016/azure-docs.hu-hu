---
title: Az Azure monitorban egyéni JSON-adatok gyűjtése |} A Microsoft Docs
description: Egyéni JSON-adatforrások gyűjthetők be az Azure Monitor használatával a Log Analytics-ügynök Linux rendszeren.  Ezek az egyéni adatforrások JSON visszaadó curl vagy valamelyik, a FluentD mint 300 beépülő modulok például egyszerű parancsfájlokat is lehet. Ez a cikk ismerteti a adatgyűjteményben szükséges konfigurációnak.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: f1d5bde4-6b86-4b8e-b5c1-3ecbaba76198
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2018
ms.author: magoedte
ms.openlocfilehash: 101719668fee155e84b7a767647a662ca845f0f2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60804648"
---
# <a name="collecting-custom-json-data-sources-with-the-log-analytics-agent-for-linux-in-azure-monitor"></a>Egyéni JSON adatforrások az Azure monitorban Linuxhoz készült Log Analytics-ügynökkel rendelkező gyűjtése
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

Egyéni JSON-adatforrások gyűjthetők be [Azure Monitor](data-platform.md) a Log Analytics ügynökét használja a Linux rendszerre.  Ezek az egyéni adatforrások lehet például visszaadó JSON egyszerű parancsfájlokat [curl](https://curl.haxx.se/) vagy az egyik [FluentD a mint 300 beépülő modulok](https://www.fluentd.org/plugins/all). Ez a cikk ismerteti a adatgyűjteményben szükséges konfigurációnak.


> [!NOTE]
> Log Analytics-ügynököket Linux v1.1.0-217 + kötelező megadni egyéni JSON-adatok

## <a name="configuration"></a>Konfiguráció

### <a name="configure-input-plugin"></a>A bemeneti beépülő modul konfigurálása

Az Azure monitorban JSON-adatokat gyűjteni, adjon hozzá `oms.api.` egy FluentD címkét egy bemeneti beépülő modul elejére.

Ha például az alábbiakban a különálló konfigurációs fájlt `exec-json.conf` a `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/`.  Ez a FluentD beépülő modul használja `exec` 30 másodpercenként a curl-parancs futtatásához.  Ez a parancs kimenete a JSON-kimenet beépülő modul gyűjti.

```
<source>
  type exec
  command 'curl localhost/json.output'
  format json
  tag oms.api.httpresponse
  run_interval 30s
</source>

<match oms.api.httpresponse>
  type out_oms_api
  log_level info

  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms_api_httpresponse*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
```
A konfigurációs fájl alapján hozzáadott `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/` a tulajdonosa, a következő paranccsal módosítani kell.

`sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/exec-json.conf`

### <a name="configure-output-plugin"></a>Kimeneti beépülő modul konfigurálása 
Adja hozzá a következő kimeneti beépülő modul konfiguráció a fő konfigurációhoz a `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` vagy különálló konfigurációs fájlt helyezni `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/`

```
<match oms.api.**>
  type out_oms_api
  log_level info

  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms_api*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
```

### <a name="restart-log-analytics-agent-for-linux"></a>Indítsa újra a Linuxhoz készült Log Analytics-ügynök
Indítsa újra a Log Analytics-ügynök Linux-szolgáltatás a következő paranccsal.

    sudo /opt/microsoft/omsagent/bin/service_control restart 

## <a name="output"></a>Kimenet
Az adatgyűjtést az Azure monitorban típusú rekord `<FLUENTD_TAG>_CL`.

Ha például az egyéni címke `tag oms.api.tomcat` az Azure monitorban típusú rekord `tomcat_CL`.  A típus a következő naplófájl-lekérdezést az összes rekord beolvasása.

    Type=tomcat_CL

Beágyazott JSON-adatok források támogatottak, de az indexelt minden szülő mező alapján. Például a következő JSON-adatokat a log-lekérdezéshez, mivel visszaadott `tag_s : "[{ "a":"1", "b":"2" }]`.

```
{
    "tag": [{
        "a":"1",
        "b":"2"
    }]
}
```


## <a name="next-steps"></a>További lépések
* Ismerje meg [lekérdezések naplózását](../log-query/log-query-overview.md) az adatforrások és megoldások gyűjtött adatok elemzéséhez. 