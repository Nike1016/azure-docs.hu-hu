---
title: Az Azure Application Insights tölcsérek
description: Ismerje meg, hogyan használhatja tölcsérek felderíteni az ügyfelek hogyan használja az alkalmazását.
services: application-insights
documentationcenter: ''
author: NumberByColors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/17/2017
ms.pm_owner: daviste;NumberByColors
ms.reviewer: mbullwin
ms.author: daviste
ms.openlocfilehash: 2cb7e15b701b53e74618c21bf219a355d495f985
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60372917"
---
# <a name="discover-how-customers-are-using-your-application-with-application-insights-funnels"></a>Fedezze fel, ügyfeleink miként használják alkalmazását az Application Insights tölcsérek

A felhasználói élmény ismertetése nem az üzleti kiemelt fontossággal bír. Az alkalmazás magában foglalja a több szakaszt, ha tudja, hogy a legtöbb ügyfél számára a teljes folyamat keresztül halad előre, hogy, vagy ha befejezése esetén a folyamat olyan pont van szükség. Egy webalkalmazás lépések sorozatát keresztül metódushívásainak néven egy *tölcsér*. Az Azure Application Insights tölcsérek segítségével betekintést nyerhet a felhasználók számára, és részletes konverziós figyelése. 

## <a name="create-your-funnel"></a>A tölcsér létrehozása
A tölcsér hoz létre, mielőtt a kérdést, hogy megválaszoljuk a dönt. Például érdemes tudni, hogy hány felhasználó tekinti meg a kezdőlapján a felhasználói profil megtekintése, és a egy a jegy létrehozása. Ebben a példában a Fabrikam-Fiber vállalati tulajdonosai szeretnék tudni, hogy az ügyfelek, akik sikeresen a felhasználói jegy létrehozása aránya.

Az alábbiakban a lépéseket, a tölcsér.

1. Válassza ki az Application Insights tölcsérek eszközben **új**.
1. Az a **időtartomány** legördülő menüjében válassza **utolsó 90 napban**. Ezek közül bármelyikre **saját tölcsérek** vagy **közös tölcsérek**.
1. Az a **1. lépés** legördülő listában válassza **Index**. 
1. Az a **2. lépés** listáról válassza ki **ügyfél**.
1. Az a **3. lépés** listáról válassza ki **létrehozás**.
1. A tölcsér név hozzáadása, és válassza ki **mentése**.

A következő képernyőképen látható egy példa, hogy milyen típusú adatokat a tölcsérek eszközt hoz létre. A Fabrikam tulajdonosok láthatja, hogy az utolsó 90 napban, ügyfeleiknek a kezdőlap, létrehozott egy felhasználói jegy látogató 54.3 százaléka. Akkor is megtekintheti, hogy ügyfelei 2,700 származik az indexbe a kezdőlapon. Ez jelentheti a hibát egy frissítési probléma.


![Képernyőkép a tölcsérek eszköz adatokkal](media/usage-funnels/funnel1.png)

### <a name="funnels-features"></a>Tölcsérek funkciók
Az előző képernyőképen látható öt kiemelt területet foglal magában. Ezek a tölcsérek funkcióját. Az alábbi lista további ismerteti a képernyőképen látható minden egyes megfelelő terület:
1. Ha az alkalmazás feladata, látni fogja a mintavételi fejléc. Kontextuspanel, azzal az információval mintavételi kikapcsolása szalagcím kiválasztásával megnyílik. 
2. Exportálhatja a tölcsér [Power BI](../../azure-monitor/app/export-power-bi.md ).
3. Válasszon egy lépést, amely további részleteket a jobb oldalon. 
4. A konvertálási előzményadatok grafikonja az átváltási árfolyamok az elmúlt 90 napban. 
5. Ismerje meg a felhasználók jobb a felhasználók eszköz elérésével. Minden egyes lépésének szűrőket is használhatja. 

## <a name="next-steps"></a>További lépések
  * [Használat – áttekintés](usage-overview.md)
  * [Felhasználók, munkamenetek és események](usage-segmentation.md)
  * [Megőrzés](usage-retention.md)
  * [Munkafüzetek](../../azure-monitor/app/usage-workbooks.md)
  * [Adja hozzá a felhasználói környezet](usage-send-user-context.md)
  * [Power BI-exportálás](../../azure-monitor/app/export-power-bi.md )

