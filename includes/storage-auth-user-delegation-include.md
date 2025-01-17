---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/25/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 2ea936ba489024648583cf22c5ab73ae06bb1537
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "69011972"
---
## <a name="about-the-user-delegation-sas-preview"></a>Tudnivalók a felhasználói delegálási SAS-ről (előzetes verzió)

Egy tárolóhoz vagy blobhoz való hozzáféréshez szükséges SAS-tokent az Azure AD hitelesítő adataival vagy egy fiók kulcsával lehet védeni. Az Azure AD-beli hitelesítő adatokkal védett SAS-t egy felhasználói delegálási SAS-nek nevezzük, mert az SAS aláírásához használt OAuth 2,0 tokent a felhasználó nevében kell kérelmezni.

A Microsoft azt javasolja, hogy az Azure AD hitelesítő adatait akkor használja, ha a fiók kulcsa helyett biztonsági szempontból ajánlott, ami könnyebben sérülhet. Ha az alkalmazás kialakításához közös hozzáférésű aláírásokra van szükség, az Azure AD hitelesítő adataival hozzon létre egy felhasználói delegálási SAS-t a kiváló biztonság érdekében. A felhasználói delegálási SAS-vel kapcsolatos további információkért lásd: [felhasználói delegálási sas létrehozása](/rest/api/storageservices/create-a-user-delegation-sas).

> [!NOTE]
> A felhasználói delegálás SAS előzetes verziója csak nem éles használatra készült.

> [!CAUTION]
> Minden olyan ügyfél, amely érvényes SAS-val rendelkezik, a Storage-fiókban lévő, az adott SAS által engedélyezett módon férhet hozzá az adataihoz. Fontos, hogy az SAS-t rosszindulatú vagy nem rendeltetésszerű használatból védjék. Saját belátása szerint oszthatja meg a SAS-t, és megtervezheti a feltört SAS visszavonását.

A közös hozzáférésű aláírásokkal kapcsolatos további információkért lásd: [korlátozott hozzáférés engedélyezése az Azure Storage-erőforrásokhoz közös hozzáférésű aláírások (SAS) használatával](../articles/storage/common/storage-sas-overview.md).
