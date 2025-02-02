---
title: Hagyja jóvá vagy utasítsa a kéréseket az Azure AD-szerepköröket a PIM - Azure Active Directory |} A Microsoft Docs
description: Megtudhatja, hogyan jóváhagyja vagy elutasítja a kérelmeket az Azure AD-szerepkörök az Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: f83cb38567feb51ba7959ada7730d66ded677bf9
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476541"
---
# <a name="approve-or-deny-requests-for-azure-ad-roles-in-pim"></a>Hagyja jóvá vagy utasítsa a PIM az Azure AD-szerepkörökhöz tartozó kérelmek

Az Azure Active Directory (Azure AD) Privileged Identity Management (PIM), konfigurálhatja szerepkörök jóváhagyást kér az aktiválás, és válasszon egy vagy több felhasználót, vagy delegált jóváhagyók csoportot. Delegált jóváhagyók rendelkezik jóváhagyásra váró kérelmek 24 óra. Ha 24 órán belül nem jóváhagyják a kérését, majd a jogosult felhasználó újra egy új kérelmet kell benyújtania. A 24 órás jóváhagyási időtartomány érték nem módosítható.

Kövesse az ebben a cikkben jóváhagyja vagy elutasítja a kérelmeket az Azure AD-szerepkörökhöz tartozó lépéseket.

## <a name="view-pending-requests"></a>Függőben lévő kérelmek megtekintése

Delegált jóváhagyójaként e-mailben értesítést fog kapni, ha egy Azure AD-szerepkör kérelem jóváhagyásra váró. Megtekintheti a függőben lévő kérések az PIM-ben.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Nyissa meg **az Azure AD Privileged Identity Management**.

1. Kattintson a **Azure AD-szerepkörök**.

1. Kattintson a **kérések jóváhagyása**.

    ![Az Azure AD-szerepkörök – kérések jóváhagyása](./media/azure-ad-pim-approval-workflow/pim-directory-roles-approve-requests.png)

    Jóváhagyásra váró láthatja a kérések listáját.

## <a name="approve-requests"></a>Kérések jóváhagyása

1. Válassza ki a kérelmeket, és kattintson a kívánt **jóváhagyás** megnyitásához a jóváhagyás kijelölt kérések ablaktáblán.

    ![Jóváhagyási kérelmek lista a jóváhagyás lehetőség kiemelésével](./media/azure-ad-pim-approval-workflow/pim-approve-requests-list.png)

1. Az a **jóváhagyási OK** mezőbe írja be az okot.

    ![A jóváhagyás okát ablaktáblán kijelölt kérések jóváhagyása](./media/azure-ad-pim-approval-workflow/pim-approve-selected-requests.png)

1. Kattintson a **jóváhagyása**.

    A jóváhagyással frissülni fog az állapotjelzőben.

    ![Kijelölt kérések ablaktábla jóváhagyása után a Jóváhagyás gombra kattint](./media/azure-ad-pim-approval-workflow/pim-approve-status.png)

## <a name="deny-requests"></a>Kérelmek elutasítása

1. Válassza ki a kérések elutasítása, és kattintson a kívánt **Megtagadás** , nyissa meg a Megtagadás kijelölt kérések ablaktáblán.

    ![Jóváhagyási kérelmek lista Megtagadás lehetőséggel kiemelve](./media/azure-ad-pim-approval-workflow/pim-deny-requests-list.png)

1. Az a **elutasítás oka** mezőbe írja be az okot.

    ![A Megtagadás okát ablaktáblán kijelölt kérések elutasítása](./media/azure-ad-pim-approval-workflow/pim-deny-selected-requests.png)

1. Kattintson a **megtagadása**.

    A elutasítási frissül az állapotjelzőben.

## <a name="next-steps"></a>További lépések

- [A PIM e-mail-értesítések](pim-email-notifications.md)
- [Hagyja jóvá vagy utasítsa a PIM az Azure-erőforrások szerepköreihez tartozó kérelmek](pim-resource-roles-approval-workflow.md)
