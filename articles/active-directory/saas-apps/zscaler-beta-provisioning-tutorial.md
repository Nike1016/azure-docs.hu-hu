---
title: 'Oktatóanyag: A Zscaler Beta konfigurálása automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat a Zscaler Bétaverzióba.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 83db6b8d-503b-48f3-b918-f9fba1369d53
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: d5ca1efb607160447bdbca59522c8ac72bc7eeb3
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68515518"
---
# <a name="tutorial-configure-zscaler-beta-for-automatic-user-provisioning"></a>Oktatóanyag: A Zscaler Beta konfigurálása automatikus felhasználó-kiépítési szolgáltatáshoz

Ennek az oktatóanyagnak a célja, hogy bemutassa a Zscaler Beta és Azure Active Directory (Azure AD) szolgáltatásban elvégzendő lépéseket, hogy az Azure AD konfigurálja a felhasználókat és/vagy csoportokat a bétaverzió Zscaler való automatikus kiépítésére és kiépítésére.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésének, működésének és gyakori kérdéseinek részletes ismertetését lásd: a felhasználók üzembe helyezésének [automatizálása és az SaaS-alkalmazások kiépítése Azure Active Directory használatával](../active-directory-saas-app-provisioning.md).
>


## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következőkkel:

* Azure AD-bérlő
* Zscaler Beta-bérlő
* Felhasználói fiók a Zscaler Beta-ban rendszergazdai engedélyekkel

> [!NOTE]
> Az Azure AD-kiépítés integrációja a Zscaler Beta SCIM API-ra támaszkodik, amely a vállalati csomaggal rendelkező fiókok Zscaler béta-fejlesztői számára érhető el.

## <a name="adding-zscaler-beta-from-the-gallery"></a>Zscaler Beta hozzáadása a katalógusból

Mielőtt a Zscaler Beta-ot konfigurálja az Azure AD-vel való automatikus felhasználói üzembe helyezéshez, hozzá kell adnia a Zscaler Beta-t az Azure AD-alkalmazás-katalógusból a felügyelt SaaS-alkalmazások listájára.

**A Zscaler Beta az Azure AD Application Galleryből való hozzáadásához hajtsa végre a következő lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)** , kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Zscaler Beta**kifejezést, válassza a **Zscaler Beta** elemet az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Zscaler Beta az eredmények listájában](common/search-new-app.png)

## <a name="assigning-users-to-zscaler-beta"></a>Felhasználók kiosztása a Zscaler Beta-hoz

Azure Active Directory a "hozzárendelések" nevű fogalom használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában a rendszer csak azokat a felhasználókat és/vagy csoportokat szinkronizálja, akik az Azure AD-alkalmazáshoz lettek rendelve.

Az automatikus felhasználó-kiépítés konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználóinak és/vagy csoportjai számára szükséges a Zscaler Beta eléréséhez. A döntés után a felhasználókat és/vagy csoportokat hozzárendelheti a Zscaler Betahoz az alábbi utasításokat követve:

* [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-zscaler-beta"></a>Fontos Tippek a felhasználók Zscaler bétaverzióhoz való hozzárendeléséhez

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve a Zscaler Beta rendszerhez az automatikus felhasználó-kiépítési konfiguráció teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

* Amikor felhasználót rendel a Zscaler bétaverzióhoz, ki kell választania bármely érvényes alkalmazásspecifikus szerepkört (ha elérhető) a hozzárendelés párbeszédpanelen. Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="configuring-automatic-user-provisioning-to-zscaler-beta"></a>Automatikus felhasználó-kiépítés beállítása a Zscaler Beta-ra

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy csoportos hozzárendelések alapján hozza létre, frissítse és tiltsa le a Zscaler Beta felhasználóit és/vagy csoportjait.

> [!TIP]
> Azt is megteheti, hogy engedélyezi az SAML-alapú egyszeri bejelentkezést a Zscaler Beta esetében, a [Zscaler Beta egyszeri bejelentkezés oktatóanyagában](zscaler-beta-tutorial.md)szereplő utasításokat követve. Az egyszeri bejelentkezés az automatikus felhasználó-kiépítés függetlenül is konfigurálható, bár ez a két funkció egymáshoz tartozik.

### <a name="to-configure-automatic-user-provisioning-for-zscaler-beta-in-azure-ad"></a>A Zscaler Beta automatikus felhasználói üzembe helyezésének konfigurálása az Azure AD-ben:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza a **Zscaler Beta**elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Zscaler Beta**elemet.

    ![A Zscaler Beta hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza ki a kiépítés lapot.

    ![Zscaler Beta-kiépítés](./media/zscaler-beta-provisioning-tutorial/provisioning-tab.png)

4. Állítsa a **kiépítési módot** **automatikus**értékre.

    ![Zscaler Beta-kiépítés](./media/zscaler-beta-provisioning-tutorial/provisioning-credentials.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a Zscaler Beta-fiókjának **bérlői URL-címét** és titkos jogkivonatát a 6. lépésben leírtak szerint.

6. A bérlői **URL-cím** és a **titkos jogkivonat**beszerzéséhez navigáljon az **Adminisztráció > hitelesítési beállítások** elemre a Zscaler Beta Portal felhasználói felületén, és kattintson az **SAML** elemre a **Hitelesítés típusa**alatt.

    ![Zscaler Beta-kiépítés](./media/zscaler-beta-provisioning-tutorial/secret-token-1.png)

    Az SAML **konfigurálása** elemre kattintva nyissa meg a **konfigurációs SAML** -beállításokat.

    ![Zscaler Beta-kiépítés](./media/zscaler-beta-provisioning-tutorial/secret-token-2.png)

    Válassza az **scim kiépítés engedélyezése** lehetőséget az **alap URL-cím** és a tulajdonosi **jogkivonat**lekéréséhez, majd mentse a beállításokat. Másolja az **alap URL-címet** a **bérlői URL**-címre, és a tulajdonosi jogkivonatot a Azure Portal **titkos** jogkivonatára.

7. Az 5. lépésben megjelenő mezők kitöltése után kattintson a **kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure ad csatlakozhasson a Zscaler Beta szolgáltatáshoz. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a Zscaler Beta-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Zscaler Beta-kiépítés](./media/zscaler-beta-provisioning-tutorial/test-connection.png)

8. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be az **e-mail-értesítés küldése hiba**esetén jelölőnégyzetet.

    ![Zscaler Beta-kiépítés](./media/zscaler-beta-provisioning-tutorial/notification.png)

9. Kattintson a **Save** (Mentés) gombra.

10. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a Zscaler Beta-** ra lehetőséget.

    ![Zscaler Beta-kiépítés](./media/zscaler-beta-provisioning-tutorial/user-mappings.png)

11. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az attribútum- **hozzárendelési** szakaszban található Zscaler Beta elemre. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Zscaler Beta felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![Zscaler Beta-kiépítés](./media/zscaler-beta-provisioning-tutorial/user-attribute-mappings.png)

12. A **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása a Zscaler bétaverzióhoz**lehetőséget.

    ![Zscaler Beta-kiépítés](./media/zscaler-beta-provisioning-tutorial/group-mappings.png)

13. Tekintse át az Azure AD-ből szinkronizált Zscaler az attribútumok leképezése szakaszban . Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Zscaler Beta-ban lévő csoportok egyeztetésére szolgálnak a frissítési műveletekhez. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![Zscaler Beta-kiépítés](./media/zscaler-beta-provisioning-tutorial/group-attribute-mappings.png)

14. A hatóköri szűrők konfigurálásához tekintse meg az alábbi utasításokat a [hatókör szűrője oktatóanyagban](./../active-directory-saas-scoping-filters.md).

15. Ha engedélyezni szeretné az Azure AD-kiépítési szolgáltatást a Zscaler Beta számára, módosítsa a **kiépítési állapotot** **a következőre** a Settings ( **Beállítások** ) szakaszban.

    ![Zscaler Beta-kiépítés](./media/zscaler-beta-provisioning-tutorial/provisioning-status.png)

16. Adja meg azokat a felhasználókat és/vagy csoportokat, akik számára a Zscaler Beta-t szeretné kiépíteni. Ehhez válassza ki a kívánt értékeket a hatókörben a **Beállítások** szakaszban.

    ![Zscaler Beta-kiépítés](./media/zscaler-beta-provisioning-tutorial/scoping.png)

17. Ha készen áll a létesítésre, kattintson a **Mentés**gombra.

    ![Zscaler Beta-kiépítés](./media/zscaler-beta-provisioning-tutorial/save-provisioning.png)

Ez a művelet elindítja a **Beállítások** szakasz hatókörében meghatározott összes felhasználó és/vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenységre mutató hivatkozásokat, amelyek az Azure ad-kiépítési szolgáltatás által a Zscaler Beta-on végrehajtott összes műveletet ismertetik.

Az Azure AD létesítési naplók olvasása további információkért lásd: [-jelentések automatikus felhasználói fiók kiépítése](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>További források

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Megtudhatja, hogyan tekintheti át a naplókat, és hogyan kérhet jelentéseket a kiépítési tevékenységekről](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-03.png
