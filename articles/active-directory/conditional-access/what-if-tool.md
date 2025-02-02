---
title: Mi a mi Ha eszközt az Azure Active Directory feltételes hozzáférés?
description: Ismerje meg, hogyan képes megérteni a feltételes hozzáférési szabályzatokat a hatását a környezetben.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/20/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97d2ec4099045e17b8482fcde313d31720083583
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67506743"
---
# <a name="what-is-the-what-if-tool-in-azure-active-directory-conditional-access"></a>Mi a mi Ha eszközt az Azure Active Directory feltételes hozzáférés?

[Feltételes hozzáférés](../active-directory-conditional-access-azure-portal.md) egy funkció az Azure Active Directory (Azure AD), amelyek segítségével szabályozhatja, hogy hogyan jogosult felhasználók hozzáférésének a felhőalkalmazásokhoz. Hogyan, hogy mi a feltételes hozzáférési szabályzatokat az adott környezetben várható? Ez a kérdés megválaszolásához használhatja a **feltételes hozzáférés What If eszközt**.

Ez a cikk bemutatja, hogyan használhatja ezt az eszközt a feltételes hozzáférési szabályzatokat teszteléséhez.

## <a name="what-it-is"></a>Mi ez?

A **feltételes hozzáférés What If házirend eszközzel** lehetővé teszi, hogy a feltételes hozzáférési szabályzatokat az adott környezet hatásának megismerése. Nem szükséges manuálisan végrehajtott bejelentkezésekkel tesztelni a szabályzatokat, az eszköz használatával egy szimulált felhasználói bejelentkezést értékelhet ki. A szimuláció megbecsüli a bejelentkezésnek a szabályzatokra gyakorolt hatását, és létrehoz egy szimulációs jelentést. A jelentés nem tartalmazza csak alkalmazott feltételes hozzáférési szabályzatokat, hanem [klasszikus szabályzatok](policy-migration.md#classic-policies) ha vannak ilyenek.    

A **mi történik, ha** eszköz lehetővé teszi a gyorsan meghatározhatja a házirendekben, amelyek egy adott felhasználó a alkalmazni. Használhatja a információkat, például ha a probléma elhárításához van szüksége.    

## <a name="how-it-works"></a>Működés

Az a **feltételes hozzáférés What If eszközt**, először konfigurálja a beállításokat szeretné szimulálni bejelentkezési forgatókönyv. Ezek a beállítások a következők:

- A vizsgálni kívánt felhasználó 
- A felhasználó megpróbálja elérni a felhőalkalmazások
- Melyik való hozzáférés feltételeit, a konfigurálja a felhőalapú alkalmazások történik.
     
A következő lépésben a szimuláció futtatása, amely kiértékeli a beállítások is kezdeményezhető. Csak olyan szabályzatokat, a rendszer-kiértékelés futtatása részét képezik.

Az értékelés befejeződött, amikor az eszköz szabályzat érintett jelentést hoz létre.

## <a name="running-the-tool"></a>Az eszköz futtatása

Annak a **mi történik, ha** eszköz a **[feltételes hozzáférés – szabályzatok](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies)** oldal az Azure Portalon.

Kattintson az eszköztár fölött a szabályzatok listáján, az eszköz indításához **mi történik, ha**.

![mi van, ha](./media/what-if-tool/01.png)

Egy értékelés futtatása előtt konfigurálnia kell a beállításokat.

## <a name="settings"></a>Beállítások

Ez a szakasz a szimuláció futtatása az beállításokra vonatkozó információkat biztosít.

![mi van, ha](./media/what-if-tool/02.png)

### <a name="user"></a>Felhasználó

Csak kijelölhet egy felhasználót. Ez az egyetlen mező kitöltése kötelező.

### <a name="cloud-apps"></a>Felhőalkalmazások

Ez a beállítás alapértelmezett értéke **az összes felhőalapú alkalmazások**. Az alapértelmezett beállítás az összes rendelkezésre álló házirend-értékelés környezetében hajtja végre. A hatókör, a házirendek adott felhőalkalmazásokat érintő szűkíthető.

### <a name="ip-address"></a>IP-cím

Az IP-cím egy IPv4-címmel referenciaszámítógépnek a [helyfeltétel](location-condition.md). A címet jelöli az internetre irányuló jelentkezzen be a felhasználó által használt eszköz címe. Ellenőrizheti az IP-cím egy eszköz, például ellenőrizheti, hogy [Mi az saját IP-cím](https://whatismyipaddress.com).    

### <a name="device-platforms"></a>Eszközplatformok

Ez a beállítás utánozza a [eszköz platformok feltétel](conditions.md#device-platforms) és egyenértékű jelöli **minden platformon (beleértve a nem támogatottakat)** . 

### <a name="client-apps"></a>Ügyfélalkalmazások

Ez a beállítás utánozza a [ügyfél alkalmazások feltétel](conditions.md#client-apps).
Alapértelmezés szerint a beállítás hatására az összes házirend kellene értékelését **böngésző** vagy **mobilalkalmazások és asztali ügyfelek** vagy külön-külön vagy mindkettő kiválasztva. Azt is észleli házirendeket kikényszerítő **Exchange ActiveSync (EAS)** . Ez a beállítás kiválasztásával szűkítheti:

- **Böngésző** értékelheti ki az összes szabályzat, legalább **böngésző** kiválasztott. 
- **Mobilalkalmazások és asztali ügyfelek** értékelheti ki az összes szabályzat, legalább **mobilalkalmazások és asztali ügyfelek** kiválasztott. 

### <a name="sign-in-risk"></a>Bejelentkezési kockázat

Ez a beállítás utánozza a [bejelentkezési kockázati feltétellel](conditions.md#sign-in-risk).   

## <a name="evaluation"></a>Értékelés 

Az értékelés indításához kattintson **mi történik, ha**. Az értékelés eredménye nyújt egy jelentést, amely a következőkből áll: 

![mi van, ha](./media/what-if-tool/03.png)

- Indikátor e klasszikus szabályzatok vannak jelen a környezetében
- A felhasználói házirendeket
- A felhasználó nem vonatkoznak a szabályzatok

Ha [klasszikus szabályzatok](policy-migration.md#classic-policies) találhatók a kiválasztott felhőalapú alkalmazások, a mutatója megjelenik. A kijelző kattintva, a rendszer átirányítja a klasszikus szabályzatok lapon. A klasszikus szabályzatok oldalon a klasszikus szabályzat áttelepítése, vagy csak letiltja azt. Zárja be ezt oldal visszatérhessen a kiértékelésének eredménye.

A házirendekben, amelyek érvényesek a kiválasztott felhasználó listából is megtalálhatja listáját [vezérlők biztosítson](controls.md#grant-controls) és [munkamenet](controls.md#session-controls) szabályozza a felhasználónak meg kell felelniük.

A házirendekben, amelyek nem érvényesek a felhasználói listán is, és is megkeresheti, ha az okokat, hogy miért ezek a szabályzatok nem vonatkoznak. Minden egyes listázott házirendjének okát jelöli az első feltétel nem teljesült. Egy lehetséges oka az olyan szabályzatot, amely a rendszer nem alkalmazza a letiltott házirend azért, továbbá nem értékeli ki.   

## <a name="next-steps"></a>További lépések

- Ha azt szeretné tudni, hogyan lehet feltételes hozzáférési szabályzat konfigurálása, lásd: [megkövetelése MFA konkrét alkalmazások esetén az Azure Active Directory feltételes hozzáférés](app-based-mfa.md).
- Ha készen áll a környezetre vonatkozó feltételes hozzáférési szabályzatok konfigurálására, tekintse meg a [gyakorlati tanácsok az Azure Active Directory feltételes hozzáférés](best-practices.md). 
- Ha azt szeretné, hogy klasszikus szabályzatok migrálása, lásd: [az Azure Portalon klasszikus szabályzatok Migrálása](policy-migration.md)  
