---
title: Metrikák a Azure Monitor-ban – Azure Event Hubs | Microsoft Docs
description: Ez a cikk információt nyújt az Azure Monitoring segítségével figyelheti az Azure Event Hubs
services: event-hubs
documentationcenter: .NET
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 99b3b4b8d48ff04fc2ced686c01b2d4de12c6555
ms.sourcegitcommit: d060947aae93728169b035fd54beef044dbe9480
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2019
ms.locfileid: "68742135"
---
# <a name="azure-event-hubs-metrics-in-azure-monitor"></a>Az Azure Event Hubs metrikái a Azure Monitor

Event Hubs-mérőszámok az Azure-előfizetésében az Event Hubs-erőforrások állapotának biztosítja. Metrikai adatok széles skáláját felmérheti az event hubs szolgáltatás nem csak a névterek szintjén, hanem az entitások szintjén általános állapotát. A statisztikai lehet fontos, mert ezek segítségével figyelheti az event hubs szolgáltatás állapotát. Metrikák is segít kiváltó problémák elhárítása anélkül, hogy forduljon az Azure ügyfélszolgálatához.

Az Azure Monitor egységes felhasználói felületet biztosít a különböző Azure-szolgáltatások figyelésére. További információkért lásd: [a Microsoft Azure figyelés](../monitoring-and-diagnostics/monitoring-overview.md) és a [lekérése az Azure Monitor-metrikák .NET-tel](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) mintát a Githubon.

## <a name="access-metrics"></a>Access-metrikák

Az Azure Monitor hozzáférés metrikák több módot is biztosít. A metrikák a Azure Portalon keresztül érhetők [](https://portal.azure.com)el, vagy a Azure monitor API-k (REST és .net) és az Analysis Solutions (például Log Analytics és Event Hubs) használatával. További információkért lásd: [figyelési adatokat gyűjtött az Azure Monitor](../azure-monitor/platform/data-platform.md).

Alapértelmezés szerint engedélyezve vannak a metrikákat, és elérheti az utolsó 30 nap adatait. Ha szeretne egy hosszabb ideig megőrizni az adatokat, úgy archiválhatók metrikák adatai egy Azure Storage-fiókhoz. Ennek a konfigurációja a [diagnosztikai beállítások](../azure-monitor/platform/diagnostic-logs-overview.md#diagnostic-settings) az Azure monitorban.

## <a name="access-metrics-in-the-portal"></a>Hozzáférés mérőszámok portálon

Az idő függvényében segítségével figyelheti a mérőszámokat a [az Azure portal](https://portal.azure.com). Az alábbi példa bemutatja, hogyan sikeres kérések és a fiók szintjén a bejövő kérelmek megtekintése:

![A sikeres metrikáinak megtekintése][1]

Metrikák a névtér keresztül közvetlenül is elérheti. Ehhez válassza ki a névteret, majd kattintson a **metrikák**elemre. Az Event hub hatókörére szűrt metrikák megjelenítéséhez válassza ki az Event hub-t, majdkattintson a metrikák elemre.

Dimenziók támogató metrikákhoz a kívánt dimenzió értékű a következő példában látható módon kell szűrése:

![A dimenzió értéket szűrése][2]

## <a name="billing"></a>Számlázás

A Azure Monitor metrikáinak használata jelenleg ingyenes. Azonban használ további megoldásokat, amelyek fogadni a mérőszámadatokat, akkor előfordulhat, hogy után kell díjat ezek a megoldások által. Ha például számítjuk fel az Azure Storage által archiválja mérőszámok az Azure Storage-fiók. Az Azure-t akkor is számlázza, ha a metrikák adatait a speciális elemzések Azure Monitor naplókra továbbítja.

Az alábbi mérőszámok segítségével a szolgáltatás állapotának áttekintése. 

> [!NOTE]
> Más néven áthelyezett azt rendszer kivezetése több metrikát. Ehhez szükség lehet, hogy a hivatkozások frissítését. Metrikák, a "elavult" kulcsszó megjelölve a jövőben nem támogatja.

Az összes értékeihez kapnak az Azure Monitor percenként. Az idő részletessége határozza meg az időintervallum, amelynek metrikák jelennek meg. Minden Event Hubs-metrika támogatott idő időköz 1 perc.

## <a name="request-metrics"></a>Kérelem-metrikák

Számít az adatok és a felügyeleti műveletek kérések száma.

| Metrika neve | Leírás |
| ------------------- | ----------------- |
| Bejövő kérések  | Az Azure Event Hubs szolgáltatás egy megadott időszakban végrehajtott kérelmek száma. <br/><br/> Egység Count <br/> Összesítés típusa: Összes <br/> Dimenzió EntityName |
| Sikeres kérések    | Az Azure Event Hubs szolgáltatás egy megadott időszakban végrehajtott sikeres kérelmek száma. <br/><br/> Egység Count <br/> Összesítés típusa: Összes <br/> Dimenzió EntityName |
| Kiszolgálóhibák  | Az Azure Event Hubs szolgáltatás hibája miatt a meghatározott időtartamon belül nem feldolgozott kérelmek száma. <br/><br/>Egység Count <br/> Összesítés típusa: Összes <br/> Dimenzió EntityName |
|Felhasználói hibák |Egy meghatározott időtartamon belül a felhasználói hibák miatt nem feldolgozott kérelmek száma.<br/><br/> Egység Count <br/> Összesítés típusa: Összes <br/> Dimenzió EntityName|
|A kvóta túllépte a hibákat |A kérelmek száma túllépte a rendelkezésre álló kvótát. Lásd: [Ez a cikk](event-hubs-quotas.md) Event Hubs-kvótákról további információt.<br/><br/> Egység Count <br/> Összesítés típusa: Összes <br/> Dimenzió EntityName|

## <a name="throughput-metrics"></a>Átviteli sebességre vonatkozó mérőszámok

| Metrika neve | Leírás |
| ------------------- | ----------------- |
|Szabályozott kérelmek |Voltak szabályozva, mert túllépte az átviteli egységek használati kérelmek száma.<br/><br/> Egység Count <br/> Összesítés típusa: Összes <br/> Dimenzió EntityName|

## <a name="message-metrics"></a>Üzenet-metrikák

| Metrika neve | Leírás |
| ------------------- | ----------------- |
|Bejövő üzenetek |Események vagy adott időszakon belül az Event hubs szolgáltatásba küldött üzenetek számát.<br/><br/> Egység Count <br/> Összesítés típusa: Összes <br/> Dimenzió EntityName|
|Kimenő üzenetek |Események vagy üzenetek száma egy meghatározott időtartamon belül beolvasni az Event hubs Eseményközpontokból.<br/><br/> Egység Count <br/> Összesítés típusa: Összes <br/> Dimenzió EntityName|
|Bejövő bájtok |Az Azure Event Hubs szolgáltatás egy meghatározott időtartamon belül küldött bájtok száma.<br/><br/> Egység Bájt <br/> Összesítés típusa: Összes <br/> Dimenzió EntityName|
|Kimenő bájtok |Az Azure Event Hubs szolgáltatás egy meghatározott időtartamon belül veszi bájtok száma.<br/><br/> Egység Bájt <br/> Összesítés típusa: Összes <br/> Dimenzió EntityName|

## <a name="connection-metrics"></a>Kapcsolati metrika

| Metrika neve | Leírás |
| ------------------- | ----------------- |
|ActiveConnections |A névtér, valamint egy entitására aktív kapcsolatok száma.<br/><br/> Egység Count <br/> Összesítés típusa: Összes <br/> Dimenzió EntityName|
|Megnyitott kapcsolatok |Megnyitott kapcsolatok száma.<br/><br/> Egység Count <br/> Összesítés típusa: Összes <br/> Dimenzió EntityName|
|Lezárt kapcsolatok |Lezárt kapcsolatok száma.<br/><br/> Egység Count <br/> Összesítés típusa: Összes <br/> Dimenzió EntityName|

## <a name="event-hubs-capture-metrics"></a>Event Hubs Capture metrikák

Az Event Hubs Capture metrikák figyelheti az event hubs rögzítési funkciója engedélyezésekor. A következő metrikák ismertetik, mit is figyelheti a rögzítés engedélyezve van.

| Metrika neve | Leírás |
| ------------------- | ----------------- |
|Várakozó fájlok rögzítése |Amely során a kiválasztott cél még el bájtok száma.<br/><br/> Egység Bájt <br/> Összesítés típusa: Összes <br/> Dimenzió EntityName|
|Rögzített üzenetek |Az üzenetek vagy a kiválasztott cél egy megadott időszakban rögzített események száma.<br/><br/> Egység Count <br/> Összesítés típusa: Összes <br/> Dimenzió EntityName|
|Rögzített bájtok száma |A kiválasztott cél egy megadott időszakban rögzített bájtok száma.<br/><br/> Egység Bájt <br/> Összesítés típusa: Összes <br/> Dimenzió EntityName|

## <a name="metrics-dimensions"></a>Metrikák dimenziók

Azure Event hubs szolgáltatás a következő dimenziókat támogatja a mérőszámok az Azure Monitor. A metrikák dimenziók hozzáadása nem kötelező. Ha nem adja hozzá a dimenziók, a metrikák a névterek szintjén vannak megadva. 

| Metrika neve | Leírás |
| ------------------- | ----------------- |
|EntityName| Az Event Hubs névtér event hub entitása támogatja.|

## <a name="next-steps"></a>További lépések

* Tekintse meg a [Azure Figyelés áttekintése](../monitoring-and-diagnostics/monitoring-overview.md).
* [.NET-tel az Azure Monitor-metrikák beolvasása](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) mintát a Githubon. 

Ha további információkat szeretne az Event Hubsról, tekintse meg az alábbi hivatkozásokat:

* Bevezetés az [Event Hubs használatába oktatóanyag](event-hubs-dotnet-standard-getstarted-send.md)
* [Event Hubs – gyakori kérdések](event-hubs-faq.md)
* [Az Event Hubsot használó mintaalkalmazások](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor1.png
[2]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor2.png



