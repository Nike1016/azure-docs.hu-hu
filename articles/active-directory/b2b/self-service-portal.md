---
title: B2B-együttműködés – Azure Active Directory-előfizetési portál önkiszolgáló |} A Microsoft Docs
description: Az Azure Active Directory B2B együttműködés a vállalatokon átívelő kapcsolatok támogatása érdekében lehetővé teszi, hogy az üzleti partnerek szelektíven érhessék el a vállalati alkalmazásokat
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: sample
ms.date: 05/08/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 28db43a345fb29a7529136ddfb929f5a48ccb1f8
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65785438"
---
# <a name="self-service-portal-for-azure-ad-b2b-collaboration-sign-up"></a>Önkiszolgáló portál Azure AD B2B-együttműködésre való regisztrációhoz

Az ügyfelek sokféleképpen hasznát vehetik a végfelhasználók számára az [Azure Portalon](https://portal.azure.com) és az [alkalmazás-hozzáférési panelen](https://myapps.microsoft.com) elérhetővé tett, beépített funkcióknak. A B2B-felhasználók bevezetésének munkafolyamatát azonban szükséges lehet a vállalat igényeinek megfelelően testre szabni. Ez [a meghívó API](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation) segítségével tehető meg.

A meghívó vállalat esetleg nem tudja előre, hogy kik azok a külső együttműködők, akiknek hozzá kell férniük az erőforrásaihoz. Olyan módszerre van szükség, amellyel a partnervállalatok a meghívó vállalat által kezelt szabályzatok alapján regisztrálhatják magukat. Ez a forgatókönyv valósítható meg az API-k használatával. Van egy [mintaprojekt a GitHubon](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web), amely éppen ezt teszi.

Ez a GitHub-projekt bemutatja, hogyan biztosíthatnak a vállalatok szabályokon alapuló, önkiszolgáló regisztrálási lehetőséget megbízható partnereiknek API-k használatával, az általuk elérhető alkalmazásokat meghatározó szabályokkal. A partner felhasználók akkor kaphatnak hozzáférést az erőforrásokhoz, amikor azokra szükségük van. Mindezt biztonságos módon, anélkül tehetik meg, hogy a meghívó vállalatnak manuálisan kellene regisztrálnia őket. A projekt egyszerűen üzembe helyezhető az Ön által választott Azure-előfizetésben.

## <a name="as-is-code"></a>Módosítás nélküli kód

A kód mintaként lett elérhetővé téve, hogy bemutassa az Azure Active Directory B2B meghívó API használatát. Saját fejlesztői csapatának vagy egy partnerének kell módosítania, és ellenőriznie egy éles helyzetben való üzembe helyezés előtt.

## <a name="next-steps"></a>További lépések

* [Mi az az Azure AD B2B együttműködés?](what-is-b2b.md)
* [Az Azure AD B2B-együttműködés licencelése](licensing-guidance.md)
* [Azure Active Directory vállalatközi együttműködés – gyakori kérdések](faq.md)
