---
title: Azure-előfizetések fizetése számla alapján
description: Útmutatás az Azure-előfizetések számlán való kifizetéséhez.
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 90c7014e3c22ac7186854d5c01b911aa630d20dd
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2019
ms.locfileid: "68774661"
---
# <a name="pay-for-your-azure-subscription-by-invoice"></a>Az Azure-előfizetés számlán való kifizetése

Ha számlára vált, az azt jelenti, hogy a számlázást a számla dátumától számított 30 napon belül fizeti be. Ahhoz, hogy az Azure-előfizetését a számla alapján fizesse, küldjön egy Azure-támogatási kérést. Ha jóváhagyta a kérelmét, átválthat a számla fizetése (ellenőrzések/átvitel) lehetőségre a [Azure Portal](https://portal.azure.com).

> [!IMPORTANT]
> * A számla fizetése (ellenőrzések/átvitel) csak üzleti fiókok esetében érhető el.
> * A számlán való fizetésre való áttérés előtt minden esedékes díjat ki kell fizetnie.

## <a name="request-to-pay-by-invoice"></a>Fizetési kérelem számla alapján

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/). Válassza a **Súgó + támogatás** > **új támogatási kérés**lehetőséget.

    ![Súgó és támogatás hivatkozás](./media/billing-how-to-pay-by-invoice/help-and-support.png)

2. A **probléma típusaként**válassza a **számlázás** lehetőséget. A *probléma típusa* a támogatási kérelem kategóriája. Válassza ki azt az előfizetést, amelyhez számlát szeretne fizetni, válasszon ki egy támogatási csomagot, majd válassza a **tovább**lehetőséget.

3. A **probléma típusaként**válassza a **fizetés** lehetőséget. A *probléma típusa* a támogatási kérelem alkategóriája.

4. Válassza a **Váltás a számla alapján fizetéshez** a **probléma** altípusa szerint lehetőséget.

5. Adja meg a következő adatokat a **részletek** mezőben, majd kattintson a **tovább**gombra.

         New or existing customer:
         If existing, current payment method:
         Order ID (requesting for invoice option):
         Account Admins Live ID (or Org ID) (should be company domain):
         Commerce Account ID:
         Company Name (as registered under VAT or Government Website):
         Company Address (as registered under VAT or Government Website):
         Company Website:
         Country:
         TAX ID/ VAT ID:
         Company Established on (Year):
         Any prior business with Microsoft:
         Contact Name:
         Contact Phone:
         Contact Email:
         Justification on why you prefer Invoice option over credit card:

        For cores increase, provide the following additional information:

         (Old quota) Existing Cores:
         (New quota) Requested cores:
         Specific region & series of Subscription:

    - A **vállalat nevének** és a **vállalat címnek** meg kell egyeznie az Azure-fiókhoz megadott adatokkal. Az információk megtekintéséhez vagy frissítéséhez tekintse [meg az Azure-fiók profiljának adatainak módosítása](billing-how-to-change-azure-account-profile.md)című témakört.
    - A hitelkeret jóváhagyása előtt fel kell vennie a számlázási kapcsolattartási adatait a Azure Portalba. A kapcsolattartási adatoknak a vállalat számlájára vagy pénzügyi részlegére kell kapcsolódniuk. A számlázási kapcsolattartási adatok frissítéséhez lépjen a [Azure Fiókközpontra](https://account.azure.com/Profile).

6. Ellenőrizze a kapcsolattartási adatokat és a kívánt kapcsolatfelvételi módot, majd kattintson a **Létrehozás** lehetőségre.

Ha hitelkeret-ellenőrzését kell futtatnia a szükséges kreditek mennyisége miatt, kredit-ellenőrzési alkalmazást fogunk küldeni.

## <a name="switch-to-invoice-pay-checkwire-transfer"></a>Váltás számlás fizetésre (ellenőrzések/átvitel)

Miután jóváhagyta a számlázást, átválthat a számla fizetése (ellenőrzés/átvitel) lehetőségre a Azure Portal.

Ha rendelkezik Microsoft Online Services program-fiókkal, átválthatja az Azure-előfizetését az ellenőrzések/átvitel lehetőségre. Ha rendelkezik Microsoft-ügyféli szerződéssel, átválthatja a számlázási profilt az ellenőrzések és a huzalok átvitele érdekében. [Útmutató a fiók típusának](#check-access-to-a-microsoft-customer-agreement)megadásához.

### <a name="switch-azure-subscription-to-checkwire-transfer"></a>Az Azure-előfizetés átváltása az ellenőrzések és a huzalok átvitelére

Kövesse az alábbi lépéseket az Azure-előfizetés számlázására való váltáshoz (ellenőrzési/átviteli forgalom). **Ha átvált a számla kifizetésére (ellenőrzések/átvitel), nem válthat vissza a hitelkártyára**.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) a fiók rendszergazdájaként.
1. Keressen rá **Cost Management + számlázásra**.

    ![A keresést bemutató képernyőkép](./media/billing-how-to-pay-by-invoice/search.png)

1. Válassza ki azt az előfizetést, amelynek a számlázását át szeretné váltani.
1. Válassza a **Fizetési módok** elemet.
1. A parancssorban kattintson a **fizetés számla alapján** gombra.

    ![A pay by számla gombon látható képernyőfelvétel](./media/billing-how-to-pay-by-invoice/pay-by-invoice.png)

### <a name="switch-billing-profile-to-checkwire-transfer"></a>Számlázási profil váltása az ellenőrzések/huzalok átadásához

Az alábbi lépéseket követve válthat egy számlázási profilt az ellenőrzések és a huzalok átvitele érdekében. Vegye figyelembe, hogy csak az Azure-ra regisztrált személy módosíthatja a számlázási profil alapértelmezett fizetési módját.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A Keresés **Cost Management és számlázás**.
1. A bal oldali menüben kattintson a **Számlázási profilok**elemre.

    ![a számlázási profilt bemutató képernyőkép a menüben](./media/billing-how-to-pay-by-invoice/billing-profile.png)

1. Válasszon számlázási profilt.
1. A bal oldali menüben válassza a **fizetési módok**lehetőséget.

   ![A fizetési módokat bemutató képernyőkép a menüben](./media/billing-how-to-pay-by-invoice/billing-profile-payment-methods.png)

1. Kattintson a kék szalagcímre, amely szerint az ellenőrzés/átvitel lehetőséggel fizethet.

    ![Képernyőkép, amely a kék szalagcímet jeleníti meg az ellenőrzések és a huzalok közötti váltáshoz](./media/billing-how-to-pay-by-invoice/customer-led-switch-to-invoice.png)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft ügyfél-szerződéshez való hozzáférés engedélyezése
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van, vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

- Ha szükséges, frissítse a számlázási kapcsolattartási adatait a [Azure Fiókközpont](https://account.azure.com/Profile).
