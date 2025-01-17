---
title: 'Oktatóanyag: A Figma automatikus felhasználó-kiépítés konfigurálása a Azure Active Directoryrel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat a Figma.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: 38ebba8803e584e9b5d1179281fcff3a3f98d5a4
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2019
ms.locfileid: "67848222"
---
# <a name="tutorial-configure-figma-for-automatic-user-provisioning"></a>Oktatóanyag: Figma konfigurálása automatikus felhasználó-kiépítési szolgáltatáshoz

Az oktatóanyag célja annak bemutatása, hogy milyen lépéseket kell végrehajtani a Figma és a Azure Active Directory (Azure AD) szolgáltatásban az Azure AD konfigurálásához, hogy a felhasználók és/vagy csoportok automatikusan kiépítsék és kiépítsék a Figma.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésének, működésének és gyakori kérdéseinek részletes ismertetését lásd: a felhasználók üzembe helyezésének [automatizálása és az SaaS-alkalmazások kiépítése Azure Active Directory használatával](../manage-apps/user-provisioning.md).
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók általános Microsoft Azure használati feltételeivel kapcsolatos további információkért tekintse meg a Microsoft Azure-előnézetek [kiegészítő használati](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)feltételeit.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Egy Azure AD-bérlő.
* [Egy Figma-bérlő](https://www.figma.com/pricing/).
* Rendszergazdai jogosultságokkal rendelkező felhasználói fiók a Figma-ben.

## <a name="assign-users-to-figma"></a>Felhasználók kiosztása a Figma.
Azure Active Directory a hozzárendelések nevű koncepció használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában csak az Azure AD-alkalmazáshoz hozzárendelt felhasználók és/vagy csoportok lesznek szinkronizálva.

A felhasználók automatikus üzembe helyezésének konfigurálása és engedélyezése előtt döntse el, hogy az Azure AD mely felhasználóinak és/vagy csoportjai számára szükséges a Figma való hozzáférés. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat hozzárendelheti a Figma az alábbi utasításokat követve:
 
* [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)
## <a name="important-tips-for-assigning-users-to-figma"></a>Fontos Tippek a felhasználók Figma való hozzárendeléséhez

 * Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve a Figma-hoz az automatikus felhasználó-kiépítési konfiguráció teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

* Amikor Figma rendel hozzá egy felhasználóhoz, a hozzárendelés párbeszédpanelen ki kell választania bármely érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="set-up-figma-for-provisioning"></a>Figma beállítása a kiépítés számára

Az Azure AD-vel való automatikus Figma konfigurálása előtt le kell kérnie néhány kiépítési információt a Figma-ből.

1. Jelentkezzen be a [Figma felügyeleti konzolra](https://www.Figma.com/). Kattintson a bérlő melletti fogaskerék ikonra.

    ![FigmaFigma-employee-provision](media/Figma-provisioning-tutorial/image0.png)

2. Lépjen az **általános > frissítési napló beállításai elemre**.

    ![FigmaFigma-employee-provision](media/Figma-provisioning-tutorial/figma03.png)

3. Másolja a **bérlő azonosítóját**. Ezt az értéket fogja használni a SCIM-végpont URL-címének megadásához  a Figma alkalmazás létesítés lapján a Azure Portal.

    ![Figma-létrehozási jogkivonat](media/Figma-provisioning-tutorial/figma-tenantid.png)

4. Görgessen le, és kattintson az **API-jogkivonat**előállítása elemre.

    ![Figma-létrehozási jogkivonat](media/Figma-provisioning-tutorial/token.png)

5. Másolja az **API-jogkivonat** értékét. Ez az érték a Figma alkalmazás üzembe helyezés lapjának **titkos jogkivonat** mezőjében jelenik meg a Azure Portal. 

    ![Figma-létrehozási jogkivonat](media/Figma-provisioning-tutorial/figma04.png)

## <a name="add-figma-from-the-gallery"></a>Figma hozzáadása a gyűjteményből

Az Azure AD-vel való automatikus Figma konfigurálásához hozzá kell adnia a Figma az Azure AD Application Gallery-ből a felügyelt SaaS-alkalmazások listájához.

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen válassza a **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások**elemre, majd válassza a **minden alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a panel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Figma**kifejezést, válassza az **Figma** elemet az eredmények panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Figma az eredmények listájában](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-figma"></a>Automatikus felhasználó-kiépítés beállítása a Figma 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy Figma alapuló felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltsa le.

> [!TIP]
> Azt is megteheti, hogy engedélyezi az SAML-alapú egyszeri bejelentkezést a Figma számára, az [Figma egyszeri bejelentkezés oktatóanyagában](figma-tutorial.md)megadott utasításokat követve. Az egyszeri bejelentkezés az automatikus felhasználó-kiépítés függetlenül is konfigurálható, bár ez a két funkció egymáshoz tartozik.

### <a name="to-configure-automatic-user-provisioning-for-figma--in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása a Figma az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **vállalati alkalmazások**lehetőséget, majd válassza **a minden alkalmazás**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Figma**lehetőséget.

    ![Az Figma hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza ki  a kiépítés lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **kiépítési módot** **automatikus**értékre.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja `https://www.figma.com/scim/v2/<TenantID>` meg a **bérlői URL-címet** , ahol a **TenantID** az az érték, amelyet korábban a Figma adott vissza. Adja meg az **API-jogkivonat** értékét a titkos jogkivonatban. Kattintson a **kapcsolat tesztelése** lehetőségre, hogy az Azure ad képes legyen csatlakozni a Figma. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a Figma-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Bérlői URL + token](common/provisioning-testconnection-tenanturltoken.png)

8. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be a jelölőnégyzetet – **e-mail-értesítés küldése hiba**esetén.

    ![Értesítő E-mail](common/provisioning-notification-email.png)

9. Kattintson a **Save** (Mentés) gombra.

10. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a Figma**lehetőséget.

    ![Figma felhasználói leképezések](media/Figma-provisioning-tutorial/figma05.png)

11. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum** -hozzárendelési szakaszban található Figma. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Figma felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![Figma felhasználói attribútumai](media/Figma-provisioning-tutorial/figma06.png)

12. A hatóköri szűrők konfigurálásához tekintse meg az alábbi utasításokat a [hatókör szűrője oktatóanyagban](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Az Azure AD-kiépítési szolgáltatás Figma való engedélyezéséhez módosítsa a kiépítési **állapotot** a **Beállítások** szakaszban.

    ![Kiépítés állapota bekapcsolva](common/provisioning-toggle-on.png)

14. Adja meg a Figma kiépíteni kívánt felhasználókat és/vagy csoportokat a Settings ( **Beállítások** ) szakasz **hatókörében** a kívánt értékek kiválasztásával.

    ![Kiépítési hatókör](common/provisioning-scope.png)

15. Ha készen áll a létesítésre, kattintson a **Mentés**gombra.

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz hatókörében meghatározott összes felhasználó és/vagy  csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenységre mutató hivatkozásokat, amelyek az Azure ad-kiépítési szolgáltatás által a Figma-on végrehajtott összes műveletet ismertetik.

Az Azure AD létesítési naplók olvasása további információkért lásd: [-jelentések automatikus felhasználói fiók kiépítése](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>További források

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Megtudhatja, hogyan tekintheti át a naplókat, és hogyan kérhet jelentéseket a kiépítési tevékenységekről](../manage-apps/check-status-user-account-provisioning.md)