---
title: 'Oktatóanyag: IPass-SmartConnect konfigurálása a felhasználók automatikus üzembe helyezéséhez a következővel: Azure Active Directory | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat a iPass SmartConnect.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 43d6de4a-b3a2-439b-95f2-8883fffded52
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 09ef80742c931c9b7e8f8c2e2cf0b9e373ad6184
ms.sourcegitcommit: fe50db9c686d14eec75819f52a8e8d30d8ea725b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/14/2019
ms.locfileid: "69015773"
---
# <a name="tutorial-configure-ipass-smartconnect-for-automatic-user-provisioning"></a>Oktatóanyag: IPass-SmartConnect konfigurálása a felhasználók automatikus kiépítési felállításához

Ennek az oktatóanyagnak a célja, hogy bemutassa a iPass SmartConnect és Azure Active Directory (Azure AD) végrehajtandó lépéseit az Azure AD konfigurálásához, hogy a felhasználók és/vagy csoportok automatikus kiosztása és kiépítése a iPass SmartConnect.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésének, működésének és gyakori kérdéseinek részletes ismertetését lásd: a felhasználók üzembe helyezésének [automatizálása és az SaaS-alkalmazások kiépítése Azure Active Directory használatával](../manage-apps/user-provisioning.md).
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók általános Microsoft Azure használati feltételeivel kapcsolatos további információkért tekintse meg a Microsoft Azure-előnézetek [kiegészítő használati](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)feltételeit.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Egy Azure AD-bérlő.
* [Egy IPass SmartConnect-bérlő](https://www.ipass.com/buy-ipass/).
* A iPass SmartConnect felhasználói fiókja rendszergazdai jogosultságokkal rendelkezik.

## <a name="assigning-users-to-ipass-smartconnect"></a>Felhasználók kiosztása iPass SmartConnect

Azure Active Directory a hozzárendelések nevű koncepció használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában csak az Azure AD-alkalmazáshoz hozzárendelt felhasználók és/vagy csoportok lesznek szinkronizálva.

Az automatikus felhasználó-kiépítés konfigurálása és engedélyezése előtt el kell döntenie, hogy mely felhasználókra és/vagy csoportokra van szükség az Azure AD-ben a iPass-SmartConnect eléréséhez. Miután eldöntötte, hogy ezek a felhasználók és/vagy csoportok iPass SmartConnect, az alábbi utasításokat követve rendelje hozzá ezeket a felhasználókat:
* [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-ipass-smartconnect"></a>Fontos Tippek a felhasználók iPass-SmartConnect való hozzárendeléséhez

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve a iPass-SmartConnect az automatikus felhasználó-létesítési konfiguráció teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

* Amikor felhasználót rendel a iPass SmartConnect, ki kell választania bármely érvényes alkalmazásspecifikus szerepkört (ha elérhető) a hozzárendelés párbeszédpanelen. Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="setup-ipass-smartconnect-for-provisioning"></a>IPass SmartConnect beállítása a kiépítés számára

Mielőtt iPass a SmartConnect az Azure AD-vel való automatikus felhasználói üzembe helyezéshez, a konfigurációs adatokat le kell kérnie a iPass SmartConnect felügyeleti konzolról:

1. A iPass SmartConnect SCIM-végponton való hitelesítéshez szükséges tulajdonosi jogkivonat lekéréséhez tekintse meg az első alkalommal, amikor beállítja a iPass-SmartConnect, mivel ez az érték csak akkor van megadva. 
2. Ha nem rendelkezik a tulajdonosi jogkivonattal, a [ipass SmartConnect támogatási csapatának] (mailto:help@ipass.com) elérésével kérje le az újat.

## <a name="add-ipass-smartconnect-from-the-gallery"></a>IPass-SmartConnect hozzáadása a gyűjteményből

Az Azure AD-vel való automatikus iPass SmartConnect konfigurálásához hozzá kell adnia a iPass-SmartConnect az Azure AD Application Gallery-ből a felügyelt SaaS-alkalmazások listájához.

**Az Azure AD iPass SmartConnect hozzáadásához hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen válassza a **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások**elemre, majd válassza a **minden alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a panel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **IPass SmartConnect**, válassza az **ipass SmartConnect** elemet az eredmények panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![iPass SmartConnect az eredmények listájában](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-ipass-smartconnect"></a>Automatikus felhasználó-kiépítés beállítása a iPass SmartConnect 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy iPass alapuló felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltsa le a SmartConnect.

> [!TIP]
>  Azt is megteheti, hogy engedélyezi az SAML-alapú egyszeri bejelentkezést a BitaBIZ, a [IPass SmartConnect egyszeri bejelentkezés oktatóanyagában](ipasssmartconnect-tutorial.md)megadott utasításokat követve. Az egyszeri bejelentkezés az automatikus felhasználó-kiépítés függetlenül is konfigurálható, bár ez a két funkció egymáshoz tartozik.

### <a name="to-configure-automatic-user-provisioning-for-ipass-smartconnect-in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása a iPass-SmartConnect az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **vállalati alkalmazások**lehetőséget, majd válassza **a minden alkalmazás**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **IPass SmartConnect**elemet.

    ![Az iPass SmartConnect hivatkozása az alkalmazások listájában](common/all-applications.png)

3. Válassza ki a kiépítés lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **kiépítési módot** **automatikus**értékre.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban `https://openmobile.ipass.com/moservices/scim/v1` adja meg a **bérlői URL-címet**. Adja meg a **titkos**jogkivonatban korábban lekért tulajdonosi jogkivonatot. Kattintson a **kapcsolat tesztelése** elemre annak ellenőrzéséhez, hogy az Azure ad tud-e csatlakozni a IPass-SmartConnect. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a iPass SmartConnect-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Bérlői URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be a jelölőnégyzetet – **e-mail-értesítés küldése hiba**esetén.

    ![Értesítő e-mail](common/provisioning-notification-email.png)

7. Kattintson a **Save** (Mentés) gombra.

8. A **leképezések** szakaszban válassza a **szinkronizálás Azure Active Directory a felhasználók ipass SmartConnect**lehetőséget.

    ![iPass SmartConnect felhasználói leképezései](media/ipass-smartconnect-provisioning-tutorial/usermapping.png)

9. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az attribútum- **hozzárendelési** szakasz ipass SmartConnect. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a ipass SmartConnect felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![iPass SmartConnect felhasználói leképezései](media/ipass-smartconnect-provisioning-tutorial/userattribute.png)


10. A hatóköri szűrők konfigurálásához tekintse meg az alábbi utasításokat a [hatókör szűrője oktatóanyagban](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Ha engedélyezni szeretné az Azure AD-kiépítési szolgáltatást a iPass SmartConnect, módosítsa a kiépítési **állapotot** a következőre a Settings ( **Beállítások** ) szakaszban.

    ![Kiépítés állapota bekapcsolva](common/provisioning-toggle-on.png)

12. Adja meg azokat a felhasználókat és/vagy csoportokat, amelyeket a iPass SmartConnect szeretne kiépíteni, ehhez válassza ki a kívánt értékeket a hatókörben a **Beállítások** szakaszban.

    ![Kiépítési hatókör](common/provisioning-scope.png)

13. Ha készen áll a létesítésre, kattintson a **Mentés**gombra.

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz hatókörében meghatározott összes felhasználó és/vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenységre mutató hivatkozásokat, amelyek az Azure ad-kiépítési szolgáltatás által a ipass SmartConnect végzett összes műveletet ismertetik.

Az Azure AD létesítési naplók olvasása további információkért lásd: [-jelentések automatikus felhasználói fiók kiépítése](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Összekötő korlátozásai

* a iPass SmartConnect csak azokat a felhasználóneveket fogadja el, amelyeknek a tartománya regisztrálva van a iPass SmartConnect felügyeleti konzolján.  

## <a name="additional-resources"></a>További források

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Megtudhatja, hogyan tekintheti át a naplókat, és hogyan kérhet jelentéseket a kiépítési tevékenységekről](../manage-apps/check-status-user-account-provisioning.md)
