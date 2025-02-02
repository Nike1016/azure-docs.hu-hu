---
title: 'Oktatóanyag: Összevont könyvtár konfigurálása az automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat az összevont könyvtárba.
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
ms.openlocfilehash: bc19a64fc72fef6407416f65cdc3b60a4c4f9464
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2019
ms.locfileid: "68296075"
---
# <a name="tutorial-configure-federated-directory-for-automatic-user-provisioning"></a>Oktatóanyag: Összevont könyvtár konfigurálása a felhasználók automatikus üzembe helyezéséhez

Ennek az oktatóanyagnak a célja, hogy bemutassa az összevont címtárban végrehajtandó lépéseket, valamint Azure Active Directory (Azure AD) az Azure AD konfigurálásához a felhasználók és/vagy csoportok összevont címtárba való automatikus kiépítéséhez és kiépítéséhez.

> [!NOTE]
>  Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésének, működésének és gyakori kérdéseinek részletes ismertetését lásd: a felhasználók üzembe helyezésének [automatizálása és az SaaS-alkalmazások kiépítése Azure Active Directory használatával](../manage-apps/user-provisioning.md).
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók általános Microsoft Azure használati feltételeivel kapcsolatos további információkért tekintse meg a Microsoft Azure-előnézetek [kiegészítő használati](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)feltételeit.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Egy Azure AD-bérlő.
* [Egy összevont könyvtár](https://www.federated.directory/pricing).
* Egy felhasználói fiók az összevont címtárban rendszergazdai engedélyekkel.

## <a name="assign-users-to-federated-directory"></a>Felhasználók társítása összevont címtárhoz
Azure Active Directory a hozzárendelések nevű koncepció használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában csak az Azure AD-alkalmazáshoz hozzárendelt felhasználók és/vagy csoportok lesznek szinkronizálva.

Az automatikus felhasználó-kiépítés konfigurálása és engedélyezése előtt döntse el, hogy az Azure AD mely felhasználóinak és/vagy csoportjai számára szükséges az összevont címtárhoz való hozzáférés. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat a következő utasítások alapján rendelheti hozzá az összevont címtárhoz:

 * [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md) 
 
 ## <a name="important-tips-for-assigning-users-to-federated-directory"></a>Fontos Tippek a felhasználók összevont címtárhoz való hozzárendeléséhez
 * Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve az összevont címtárhoz az automatikus felhasználó-kiépítési konfiguráció teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

* Ha egy felhasználót összevont címtárhoz rendel hozzá, ki kell választania egy érvényes alkalmazásspecifikus szerepkört (ha elérhető) a hozzárendelési párbeszédpanelen. Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.
    
 ## <a name="set-up-federated-directory-for-provisioning"></a>Összevont könyvtár beállítása a kiépítés számára

Az összevont címtár az Azure AD-vel való automatikus felhasználói üzembe helyezése előtt engedélyeznie kell a SCIM-létesítést az összevont címtárban.

1. Jelentkezzen be az [összevont címtár-felügyeleti konzolba](https://federated.directory/of)

    ![Összevont címtár – oktatóanyag](media/federated-directory-provisioning-tutorial/companyname.png)

2. Navigáljon a **címtárak > felhasználói könyvtárak** elemre, és válassza ki a bérlőt. 

    ![összevont könyvtár](media/federated-directory-provisioning-tutorial/ad-user-directories.png)

3.  Állandó tulajdonosi jogkivonat létrehozásához navigáljon a **címtár kulcsai > hozzon létre új kulcsot.** 

    ![összevont könyvtár](media/federated-directory-provisioning-tutorial/federated01.png)

4. Hozzon létre egy könyvtárat. 

    ![összevont könyvtár](media/federated-directory-provisioning-tutorial/federated02.png)
    

5. Másolja a **hozzáférési jogkivonat** értékét. Ezt az értéket az összevont Directory-alkalmazás kiépítés lapjának **titkos jogkivonat** mezőjébe írja be a Azure Portal. 

    ![összevont könyvtár](media/federated-directory-provisioning-tutorial/federated03.png)
    
## <a name="add-federated-directory-from-the-gallery"></a>Összevont könyvtár hozzáadása a gyűjteményből

Az összevont címtár az Azure AD-vel való automatikus felhasználók számára történő konfigurálásához hozzá kell adnia az összevont könyvtárat az Azure AD Application Gallery-ből a felügyelt SaaS-alkalmazások listájához.

**Összevont címtár hozzáadásához az Azure AD alkalmazás-katalógusból hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen válassza a **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások**elemre, majd válassza a **minden alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a panel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be az **összevont címtár**kifejezést, majd válassza az **összevont címtár** lehetőséget az eredmények panelen.

    ![Összevont címtár az eredmények listájában](common/search-new-app.png)

5. Nyissa meg az alábbi **URL-címet** egy külön böngészőben. 

    ![összevont könyvtár](media/federated-directory-provisioning-tutorial/loginpage1.png)

6. Kattintson **a bejelentkezés**elemre.

    ![összevont könyvtár](media/federated-directory-provisioning-tutorial/federated04.png)

7.  Mivel az összevont címtár egy OpenIDConnect-alkalmazás, az összevont címtárba való bejelentkezést a Microsoft munkahelyi fiókjával lehet bejelentkezni.
    
    ![összevont könyvtár](media/federated-directory-provisioning-tutorial/loginpage3.png)
 
8. Sikeres hitelesítés után fogadja el a jóváhagyást kérő lapot. Az alkalmazás ezután automatikusan hozzáadódik a bérlőhöz, és a rendszer átirányítja az összevont címtár-fiókhoz.

    ![összevont címtár SCIM hozzáadása](media/federated-directory-provisioning-tutorial/premission.png)



## <a name="configuring-automatic-user-provisioning-to-federated-directory"></a>Az automatikus felhasználó-kiépítés konfigurálása az összevont címtárba 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, amelyekkel felhasználókat és/vagy csoportokat hozhat létre, frissíthet és tilt le az összevont címtárban felhasználói és/vagy csoportos hozzárendelések alapján az Azure AD-ben.

### <a name="to-configure-automatic-user-provisioning-for-federated-directory-in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása az összevont címtárhoz az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **vállalati alkalmazások**lehetőséget, majd válassza **a minden alkalmazás**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza az **összevont címtár**elemet.

    ![Az összevont könyvtár hivatkozása az alkalmazások listájában](common/all-applications.png)

3. Válassza ki  a kiépítés lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **kiépítési módot** **automatikus**értékre.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban `https://api.federated.directory/v2/` adja meg a bérlői URL-címet. Adja meg a beolvasott és a korábban mentett értéket az összevont könyvtárból **titkos**jogkivonatban. Kattintson a **kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure ad csatlakozni tud az összevont címtárhoz. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy az összevont címtár rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Bérlői URL + token](common/provisioning-testconnection-tenanturltoken.png)

8. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be a jelölőnégyzetet – **e-mail-értesítés küldése hiba**esetén.

    ![Értesítő E-mail](common/provisioning-notification-email.png)

9. Kattintson a **Save** (Mentés) gombra.

10. A **leképezések** szakaszban válassza a **szinkronizálás Azure Active Directory a felhasználók összevont címtárba**lehetőséget.

    ![Összevont címtár – oktatóanyag](media/federated-directory-provisioning-tutorial/user-mappings.png)
    
    
11. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az attribútum- **hozzárendelési** szakaszban az összevont címtárba. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok az összevont címtárban lévő felhasználói fiókoknak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![Összevont címtár – oktatóanyag](media/federated-directory-provisioning-tutorial/user-attributes.png)
    

12. A hatóköri szűrők konfigurálásához tekintse meg az alábbi utasításokat a [hatókör szűrője oktatóanyagban](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Az Azure AD kiépítési szolgáltatás összevont címtárhoz való engedélyezéséhez módosítsa a **kiépítési állapotot** a  **Beállítások** szakaszban.

    ![Kiépítés állapota bekapcsolva](common/provisioning-toggle-on.png)

14. Adja meg azokat a felhasználókat és/vagy csoportokat, amelyeket az összevont címtárban szeretne kiépíteni, ehhez válassza ki a kívánt értékeket a **hatókör** területen a **Beállítások** szakaszban.

    ![Kiépítési hatókör](common/provisioning-scope.png)

15. Ha készen áll a létesítésre, kattintson a **Mentés**gombra.

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz hatókörében meghatározott összes felhasználó és/vagy  csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenységek jelentésére mutató hivatkozásokat, amelyek az Azure ad-kiépítési szolgáltatás által az összevont címtárban végrehajtott összes műveletet ismertetik.

Az Azure AD-kiépítési naplók beolvasásával kapcsolatos további információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](../manage-apps/check-status-user-account-provisioning.md) helyezésével kapcsolatban.
## <a name="additional-resources"></a>További források

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Megtudhatja, hogyan tekintheti át a naplókat, és hogyan kérhet jelentéseket a kiépítési tevékenységekről](../manage-apps/check-status-user-account-provisioning.md)
