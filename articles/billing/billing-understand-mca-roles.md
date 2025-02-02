---
title: A Microsoft ügyfél-szerződés – az Azure számlázási rendszergazdai szerepkörök ismertetése
description: Ismerje meg a Microsoft ügyfél-megállapodások esetén az Azure-beli fiókok számlázási számlázási szerepkörök.
author: amberbhargava
manager: amberbhargava
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 12e492fee95192d0af49ff7b6a5be3144398fef8
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490445"
---
# <a name="understand-microsoft-customer-agreement-administrative-roles-in-azure"></a>Megismerheti a Microsoft Ügyfélszerződéséhez rendszergazdai szerepkörök az Azure-ban

Számlázási fiók kezelése a Microsoft vevői szerződéssel, használja a szerepkörök, a következő szakaszok ismertetik. Ezek a szerepkörök felelnek a beépített szerepkörök az Azure rendelkezik az erőforrásokhoz való hozzáférés szabályozása mellett. További információkért lásd: [beépített szerepkörök az Azure-erőforrások](../role-based-access-control/built-in-roles.md).

Ez a cikk a Microsoft vevői szerződés számlázási fiók vonatkozik. Ellenőrizze, hogy ha egy Microsoft Ügyfélszerződéséhez hozzáféréssel rendelkezik.

## <a name="billing-role-definitions"></a>Szerepkör-definíciók számlázás

A következő táblázat ismerteti a számlázási szerepkörök számlázási profilok használata kezelheti az Ön számlázási fiókjához, és szakaszok számlázni.

|Role|Leírás|
|---|---|
|Számlázási fiók tulajdonosa|Számlázási fiók az összes funkció kezelését|
|Számlázási közreműködő|A számlázási fiók engedélyeinek kivételével mindent felügyelhetnek|
|Számlázási olvasó fiók|Csak olvasási nézetben minden, a számlázási fiók|
|A számlázás profil tulajdonosa|Minden számlázási profil kezelése|
|A számlázás profil közreműködője|A számlázási profilja engedélyeinek kivételével mindent felügyelhetnek|
|A számlázás profil olvasója|A számlázási profilt minden, a csak olvasható nézet|
|Számla manager|Megtekintheti és számlákat fizetnie a számlázási profilja|
|Számla szakasz tulajdonosa|A számla szakasz összes funkció kezelését|
|Számla szakasz közreműködője|A számla szakasz engedélyeinek kivételével mindent felügyelhetnek|
|Számla szakasz olvasó|A számla szakaszban minden, a csak olvasható nézet|
|Azure-előfizetés létrehozója|Az Azure-előfizetések létrehozása|

## <a name="billing-account-roles-and-tasks"></a>Számlázási fiók szerepkörök és feladatok

Számlázási fiók lehetővé teszi a szervezet használati díjának felszámolása kezelését. Számlázási fiók használatával rendezheti a költségek, a költségek figyelése és a számlákat és a számlázási hozzáférés a szervezet számára. További információkért lásd: [számlázási fiók ismertetése](billing-mca-overview.md#your-billing-account).

Az alábbi táblázatok bemutatják, milyen szerepet feladatot elvégezhet a számlázási fiók kontextusában kell.

### <a name="manage-billing-account-permissions-and-properties"></a>Számlázási fiók engedélyeit, és a tulajdonságok kezelése

|Tevékenység|Számlázási fiók tulajdonosa|Számlázási közreműködő|Számlázási olvasó fiók|
|---|---|---|---|
|Számlázási fiók meglévő engedélyeinek megtekintése|✔|✔|✔|
|Mások megtekintése és kezelése a számlázási fiók jogosultságok megadása|✔|✘|✘|
|Nézet számlázási fióktulajdonságokat, például vállalatnevet, cím és további|✔|✔|✔|

### <a name="manage-billing-profiles-for-billing-account"></a>Számlázási fiók számlázási profilok kezelése

|Tevékenység|Számlázási fiók tulajdonosa|Számlázási közreműködő|Számlázási olvasó fiók|
|---|---|---|---|
|A fiókban lévő összes számlázási profil megtekintése|✔|✔|✔|

### <a name="manage-invoices-for-billing-account"></a>Számlák, Számlázás, fiók kezelése

|Tevékenység|Számlázási fiók tulajdonosa|Számlázási közreműködő|Számlázási olvasó fiók|
|---|---|---|---|
|A fiókban lévő összes számlák megtekintése|✔|✔|✔|
|Töltse le a számlákat, az Azure-használat és költségek fájlok, a árlisták, és a fiókban lévő dokumentumok adó|✔|✔|✔|

### <a name="manage-invoice-sections-for-billing-account"></a>Számla szakaszok számlázási fiók kezelése

|Tevékenység|Számlázási fiók tulajdonosa|Számlázási közreműködő|Számlázási olvasó fiók|
|---|---|---|---|
|A fiókban lévő összes számla szakasz megtekintése|✔|✔|✔|

### <a name="manage-transactions-for-billing-account"></a>Tranzakciók számlázási fiók kezelése

|Tevékenység|Számlázási fiók tulajdonosa|Számlázási közreműködő|Számlázási olvasó fiók|
|---|---|---|---|
|A fiók összes számlázási tranzakciók megtekintése|✔|✔|✔|
|A fiók vásárolt az összes termék megtekintése|✔|✔|✔|

### <a name="manage-subscriptions-for-billing-account"></a>A számlázás, fiók-előfizetések kezelése

|Tevékenység|Számlázási fiók tulajdonosa|Számlázási közreműködő|Számlázási olvasó fiók|
|---|---|---|---|
|Minden Azure-előfizetés megtekintése a számlázási fiók|✔|✔|✔|

## <a name="billing-profile-roles-and-tasks"></a>A számlázás profil szerepkörök és feladatok

A számlázási a profil lehetővé teszi a számlákat és a fizetési módok kezeléséhez. A havi számla jön létre az Azure-előfizetések és az egyéb termékek, a számlázási profillal vásárolt. Fizetési módokat kell fizetnem a számla kell használnia. További információkért lásd: [számlázási a profilok megismerése](billing-mca-overview.md#billing-profiles).

Az alábbi táblázatok bemutatják, milyen szerepet feladatot elvégezhet a számlázási profilja kontextusában kell.

### <a name="manage-billing-profile-permissions-properties-and-policies"></a>Számlázási profilengedélyek, a tulajdonságok és a szabályzatok kezelése

|Tevékenység|A számlázás profil tulajdonosa|A számlázás profil közreműködője|A számlázás profil olvasója|Számla Manager|Számlázási fiók tulajdonosa|Számlázási közreműködő|Számlázási olvasó fiók
|---|---|---|---|---|---|---|---|
|A számlázási profil meglévő engedélyeinek megtekintése|✔|✔|✔|✔|✔|✔|✔|
|Mások megtekintése és kezelése a számlázási profilja jogosultságok megadása|✔|✘|✘|✘|✘|✘|✘|
|Számlázási profil tulajdonságainak megtekintése, például beszerzési rendelés száma, e-mail számla szabályozó és egyéb|✔|✔|✔|✔|✔|✔|✔|
|Számlázási profil tulajdonságainak frissítése |✔|✔|✘|✘|✘|✘|✘|
|A számlázási profil, például a alkalmazni szabályzatok megtekintése Azure foglalás vásárlások engedélyezése, az Azure piactéren történt vásárlást és egyéb engedélyezése|✔|✔|✔|✔|✔|✔|✔|
|A számlázási profil a szabályzatok alkalmazása |✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-invoices-for-billing-profile"></a>Számlák, számlázás profil kezelése

|Tevékenység|A számlázás profil tulajdonosa|A számlázás profil közreműködője|A számlázás profil olvasója|Számla Manager|Számlázási fiók tulajdonosa|Számlázási közreműködő|Számlázási olvasó fiók
|---|---|---|---|---|---|---|---|
|A számlázási profil minden számla megtekintése|✔|✔|✔|✔|✔|✔|✔|
|Töltse le a számlákat, az Azure-használat és költségek fájlok, a árlisták, és a számlázási profil dokumentumok adó|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-invoice-sections-for-billing-profile"></a>Számla szakaszok számlázási profil kezelése

|Tevékenység|A számlázás profil tulajdonosa|A számlázás profil közreműködője|A számlázás profil olvasója|Számla Manager|Számlázási fiók tulajdonosa|Számlázási közreműködő|Számlázási olvasó fiók
|---|---|---|---|---|---|---|---|
|A számlázási profil számla szakaszok megtekintése|✔|✔|✔|✔|✔|✔|✔|
|Új számla szakasz az elszámolási profil létrehozása|✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-transactions-for-billing-profile"></a>Tranzakciók számlázási profil kezelése

|Tevékenység|A számlázás profil tulajdonosa|A számlázás profil közreműködője|A számlázás profil olvasója|Számla Manager|Számlázási fiók tulajdonosa|Számlázási közreműködő|Számlázási olvasó fiók
|---|---|---|---|---|---|---|---|
|A számlázási profil minden elszámolási tranzakciók megtekintése|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-payment-methods-for-billing-profile"></a>Profil Számlázási fizetési módok kezelése

|Tevékenység|A számlázás profil tulajdonosa|A számlázás profil közreműködője|A számlázás profil olvasója|Számla Manager|Számlázási fiók tulajdonosa|Számlázási közreműködő|Számlázási olvasó fiók
|---|---|---|---|---|---|---|---|
|A számlázási profil megtekintése fizetési módok|✔|✔|✔|✔|✔|✔|✔|
|Nyomon követheti az Azure-kreditek elosztás a számlázási profilja|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-subscriptions-for-billing-profile"></a>Számlázási profil-előfizetések kezelése

|Tevékenység|A számlázás profil tulajdonosa|A számlázás profil közreműködője|A számlázás profil olvasója|Számla Manager|Számlázási fiók tulajdonosa|Számlázási közreműködő|Számlázási olvasó fiók
|---|---|---|---|---|---|---|---|
|Minden Azure-előfizetés a számlázási profil megtekintése|✔|✔|✔|✔|✔|✔|✔|

## <a name="invoice-section-roles-and-tasks"></a>Számla szakasz szerepkörök és feladatok

Egy számla szakasz lehetővé teszi a költségek, a számla rendszerezéséhez. Részleg, fejlesztői környezet, a költségek rendszerezéséhez szakasz hozhat létre, vagy a szervezet igényeinek megfelelően. Engedélyezze a többi szakasz Azure-előfizetések létrehozására. Minden használati díjak és az előfizetések, majd a show, a számla a szakasz a vásárlásokat. További információkért lásd: [Understand számla szakasz](billing-mca-overview.md#invoice-sections).

Az alábbi táblázatok bemutatják, milyen szerepet, teljes körű feladatok számla szakaszok kontextusában kell.

### <a name="manage-invoice-section-permissions-and-properties"></a>Számla szakasz engedélyek és tulajdonságok kezelése

|Feladatok|Számla szakasz tulajdonosa|Számla szakasz közreműködője|Számla szakasz olvasó|Azure-előfizetés létrehozója|Számlázási fiók tulajdonosa|Számlázási közreműködő|Számlázási olvasó fiók | |
|---|---|---|---|---|---|---|---|---|
|A számla szakasz összes engedély megtekintése|✔|✔|✔|✔|✔|✔|✔| |
|Mások megtekintése és kezelése a számla szakasz jogosultságok megadása|✔|✘|✘|✘|✘|✘|✘| |
|Számla szakasz tulajdonságainak megtekintése|✔|✔|✔|✔|✔|✔|✔| |
|Számla szakasz tulajdonságainak frissítése|✔|✔|✘|✘|✘|✘|✘|✘|

### <a name="manage-products-for-invoice-section"></a>Kezelheti a termékeket, a számla szakasz

|Feladatok|Számla szakasz tulajdonosa|Számla szakasz közreműködője|Számla szakasz olvasó|Azure-előfizetés létrehozója|Számlázási fiók tulajdonosa|Számlázási közreműködő|Számlázási olvasó fiók
|---|---|---|---|---|---|---|---|
|A számla szakaszban vásárolt az összes termék megtekintése|✔|✔|✔|✘|✔|✔|✔|
|Kezelheti a számlázási termékek számla szakasszal, például a Mégse gombra, kapcsolja ki az automatikus megújítási és egyéb|✔|✔|✘|✘|✘|✘|✘|
|A termékek módosítása számla szakasz|✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-subscriptions-for-invoice-section"></a>A számla szakasz-előfizetések kezelése

|Feladatok|Számla szakasz tulajdonosa|Számla szakasz közreműködője|Számla szakasz olvasó|Azure-előfizetés létrehozója|Számlázási fiók tulajdonosa|Számlázási közreműködő|Számlázási olvasó fiók
|---|---|---|---|---|---|---|---|
|Minden Azure-előfizetés számla szakaszának megtekintése|✔|✔|✔|✘|✔|✔|✔|
|Változás számla szakasz az előfizetésekhez|✔|✔|✘|✘|✘|✘|✘|
|Előfizetések számlázási tulajdonjogának kérése a felhasználóktól az egyéb számlázási fiókok|✔|✔|✘|✘|✘|✘|✘|

## <a name="subscription-billing-roles-and-tasks"></a>Előfizetés számlázási szerepkörök és feladatok

Az alábbi táblázat bemutatja, milyen szerepet feladatot elvégezhet az előfizetés környezetében kell.

|Feladatok|Számla szakasz tulajdonosa|Számla szakasz közreműködője|Számla szakasz olvasó|Azure-előfizetés létrehozója|
|---|---|---|---|---|
|Az Azure-előfizetések létrehozása|✔|✔|✘|✔|
|Költséghely az előfizetés frissítése|✔|✔|✘|✘|
|Az előfizetés módosítása a számla szakasz|✔|✔|✘|✘|

## <a name="manage-billing-roles-in-the-azure-portal"></a>Az Azure Portal számlázási szerepkörök kezelése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Keresse meg **Cost Management és számlázás**.

   ![Az Azure portál keresési bemutató képernyőkép](./media/billing-understand-mca-roles/billing-search-cost-management-billing.png)

3. Válassza ki **hozzáférés-vezérlés (IAM)** például számlázási fiók, számlázási profilja vagy számla ellenében szakaszt, ha hozzáférést szeretne a hatókörben.

4. A hozzáférés-vezérlés (IAM) lap felsorolja a felhasználókat és csoportokat, minden egyes szerepkörrel az adott hatókörnél.

   ![Számlázási fiók a rendszergazdák listáját bemutató képernyőkép](./media/billing-understand-mca-roles/billing-list-admins.png)

5. Hozzáférés biztosítása a felhasználók számára, válassza ki a **Hozzáadás** az oldal tetején. A szerepkör legördülő listában jelölje ki a szerepkört. Adja meg, akiknek hozzáférést szeretne a felhasználó e-mail-címét. Válassza ki **mentése** a szerepkör hozzárendelése.

   ![Képernyőkép a rendszergazda hozzáadása egy számlázási fiók](./media/billing-understand-mca-roles/billing-add-admin.png)

6. Adott felhasználó hozzáférésének eltávolításához jelölje ki a felhasználót az eltávolítani kívánt szerepkör-hozzárendelés. Válassza ki az eltávolítása.

   ![Képernyőkép a rendszergazda eltávolítása egy számlázási fiók](./media/billing-understand-mca-roles/billing-remove-admin.png)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>A Microsoft Ügyfélszerződéséhez való hozzáférés ellenőrzése
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Segítség Forduljon a támogatási szolgálathoz.
Ha segítségre van szüksége, kell [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.

## <a name="next-steps"></a>További lépések

További információ az Ön számlázási fiókjához a következő cikkekben talál:

- [Első conditions stated above az Ön számlázási fiókjához tartozó Microsoft Ügyfélszerződéséhez](billing-mca-overview.md)
- [A számlázási fiók Azure-előfizetés létrehozása a Microsoft ügyfél-szerződés](billing-mca-create-subscription.md)
