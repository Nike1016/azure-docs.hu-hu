---
title: Válassza ki a tarifacsomagot az Azure Maps jobb |} A Microsoft Docs
description: További tudnivalók az Azure Maps által kínált tarifacsomagok
author: walsehgal
ms.author: v-musehg
ms.date: 01/02/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 1bf60014b20f4cec29d661db4deb029fea4ab1d5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60796322"
---
# <a name="choose-the-right-pricing-tier-in-azure-maps"></a>A jobb oldalon, az Azure Maps-tarifacsomag kiválasztása

Az Azure Maps két tarifacsomaggal rendelkezik. Ez a cikk célja segítségével válassza ki a tarifacsomagot az igényeinek jobb. Válassza ki a tarifacsomagot jobb érdekében tegye fel magának a következő két kérdéseket.

## <a name="what-geospatial-capabilities-do-i-plan-to-use"></a>Milyen térinformatikai funkciókat tervezhetem használni?
Az S0 tarifacsomag akkor megfelelő, ha a core API-k térinformatikai a szolgáltatás követelményeinek. Ha azt szeretné, speciális funkciókat az alkalmazáshoz, érdemes lehet térségen az S1 tarifacsomagot. Példa képességek állnak közötti és hibrid képeken útválasztási tartomány és a batch geokódolás beolvasása. A **szolgáltatásszintek lehetőségei díjszabás** , az alábbi táblázat segít az alkalmazás igényeinek jobb képet. Emellett segít válasszon egy tarifacsomagot az alkalmazásához leginkább megfelelő.

## <a name="how-many-concurrent-users-do-i-plan-to-support"></a>Egyidejű felhasználók tervezhetem támogatásához? 
Az S0 és S1 szintű tarifacsomagban is kapható különböző mennyiségű adat kezelésére. Mielőtt egy tarifacsomagot az Azure Maps választja, tegye fel magának a feltett kérdésekre. Ilyen például, "hogyan nagyszámú egyidejű felhasználót tegye szeretnék támogatásához?" Akár kezeli az S0 tarifacsomag **50 lekérdezések másodpercenkénti**. S1 árképzési szint fogópontjainak **másodpercenként több mint 50 lekérdezések**.

### <a name="pricing-tier-capabilities"></a>Díjszabási szint képességek

| Képesség                              |        S0           |  S1      |
|-----------------------------------------|:-------------------:|:--------:|
| Keresés (fwd/Ford geokódolás, hasznos helyek)  |        ✓           |     ✓    |
| A Batch geokódolás (előzetes verzió)              |                   |     ✓    |
| Keresés a sokszög          |                   |     ✓    |
| Útválasztás                                 |        ✓           |     ✓    |
| Útválasztási tartomány                    |                   |     ✓    |
| Batch-útválasztási (előzetes verzió)                |                   |     ✓    |
| Mátrix útválasztás (előzetes verzió)               |                   |     ✓    |
| Megjelenítés                                  |        ✓           |     ✓    |
| Képeken és hibrid képeken    |            |     ✓    |
| Adatforgalom                                 |        ✓           |     ✓    |
| Időzónák                              |        ✓           |     ✓    |
| Földrajzi hely (előzetes verzió)                |        ✓           |     ✓    |
| Adatok (előzetes verzió)               |                   |     ✓    |
| Térbe (előzetes verzió)               |                   |     ✓    |
| A Geokerítések (előzetes verzió)               |                   |     ✓    |



Ezek további adatpontokat mellett szóló érvek a következők:
* Milyen típusú vállalati rendelkezik?
* Hogyan kritikus fontosságú az alkalmazás létrehozása folyamatban van?

Tekintse meg a **tarifacsomag ügyfelek célzott** tábla jobb megtapasztalhatja, az S0 és S1 szintű tarifacsomagban is kapható. További információkért lásd: [díjszabás az Azure Maps](https://azure.microsoft.com/pricing/details/azure-maps/). 

### <a name="pricing-tier-targeted-customers"></a>A tarifacsomag megcélzott felhasználók

| Tarifacsomag  |     Megcélzott felhasználók                                                                |
|---------------|:-----------------------------------------------------------------------------------------|
| S0            |    <p>Az S0 tarifacsomag ügyfelek esetében, akik kis és közepes méretű vállalkozások van. A tarifacsomag az Ön számára, ha várhatóan nem jelentős mennyiségű egyidejű felhasználók jobb legyen. A akkor is, jobb, ha a core térinformatikai API-k az előző táblázatban látható a szolgáltatás követelményeinek. Ez a szint szolgáltatás általánosan elérhető. Azon alkalmazások esetében működik, az éles összes fázisa: proof-of-concept fejlesztési és tesztelési alkalmazás éles és a központi telepítési korai szakaszában.<p>|
| S1            |    <p>Az S1 tarifacsomagot az ellenőrzést a nagy méretű nagyvállalati, alapvető fontosságú alkalmazások és az egyidejű felhasználók nagy mennyiségű igénylő ügyfelek számára. Azon ügyfelek számára, akik igényelnek speciális térinformatikai szolgáltatások is van.</p>|

## <a name="next-steps"></a>További lépések

További információ megtekintése és módosítása a tarifacsomagok:

> [!div class="nextstepaction"] 
> [Tarifacsomag kezelése](how-to-manage-pricing-tier.md)
