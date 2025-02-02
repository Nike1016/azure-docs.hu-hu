---
title: Új hitelesítés használata a StorSimple 8000 Eszközkezelő Service-hez az Azure-ban | Microsoft Docs
description: Ismerteti, hogyan használható az HRE-alapú hitelesítés a szolgáltatáshoz, új regisztrációs kulcs előállítása, valamint az eszközök manuális regisztrálásának elvégzése.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: alkohli
ms.openlocfilehash: 88badf6de5e7181763dd72c7a51262d16697bffd
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68963489"
---
# <a name="use-the-new-authentication-for-your-storsimple"></a>Az új hitelesítés használata a StorSimple

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Áttekintés

A StorSimple Eszközkezelő szolgáltatás Microsoft Azure fut, és több StorSimple-eszközhöz csatlakozik. A mai napig a StorSimple Eszközkezelő Service egy Access Control szolgáltatást (ACS) használt a szolgáltatás hitelesítéséhez a StorSimple-eszközön. Az ACS-mechanizmus hamarosan elavult, és egy Azure Active Directory (HRE) hitelesítéssel lesz lecserélve. További információért látogasson el a következő közlemények az ACS-elavulás és a HRE-hitelesítés használata című részhez.

- [Az Azure ACS jövője Azure Active Directory](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/12/the-future-of-azure-acs-is-azure-active-directory/)
- [A Microsoft Access Control Service közelgő módosításai](https://azure.microsoft.com/blog/acs-access-control-service-namespace-creation-restriction/)

Ez a cikk ismerteti a HRE-hitelesítés és a kapcsolódó új szolgáltatás regisztrációs kulcsának részleteit, valamint a tűzfalszabályok StorSimple-eszközökre vonatkozó módosításait. A cikkben szereplő információk csak a StorSimple 8000 Series-eszközökre vonatkoznak.

A HRE hitelesítés az 5. vagy újabb verziót futtató StorSimple 8000 sorozatú eszközön történik. A HRE-hitelesítés bevezetése miatt a következő változások történnek:

- Tűzfalszabályok URL-mintái
- Szolgáltatás regisztrációs kulcsa.

Ezeket a módosításokat a következő szakaszokban részletesen ismertetjük.

## <a name="url-changes-for-aad-authentication"></a>HRE-hitelesítés URL-változásai

Annak biztosítása érdekében, hogy a szolgáltatás HRE-alapú hitelesítést használjon, az összes felhasználónak tartalmaznia kell az új hitelesítési URL-címeket a tűzfalszabályok között.

StorSimple 8000 sorozat használata esetén győződjön meg arról, hogy a tűzfalszabályok a következő URL-címet tartalmazzák:

| URL-minta                         | Felhő | Összetevő/funkció         |
|------------------------------------|-------|----------------------------------|
| `https://login.windows.net`        | Azure Public |HRE hitelesítési szolgáltatás      |
| `https://login.microsoftonline.us` | Az USA kormányzata |HRE hitelesítési szolgáltatás      |

Az StorSimple 8000 Series-eszközök URL-mintáinak teljes listáját a [Tűzfalszabályok URL-mintái](storsimple-8000-system-requirements.md#url-patterns-for-firewall-rules)című részében teheti meg.

Ha a tűzfalszabály nem tartalmazza a hitelesítési URL-címet az elavult dátumon túl, és az eszköz az 5. frissítést futtatja, a felhasználók egy URL-riasztást látnak. A felhasználóknak tartalmazniuk kell az új hitelesítési URL-címet. Ha az eszköz az 5. frissítés előtti verziót futtatja, a felhasználók szívverési riasztást látnak. A StorSimple-eszköz minden esetben nem tud hitelesíteni a szolgáltatással, és a szolgáltatás nem tud kommunikálni az eszközzel.

## <a name="device-version-and-authentication-changes"></a>Az eszköz verziószáma és a hitelesítés módosítása

Ha StorSimple 8000 sorozatú eszközt használ, a következő táblázat segítségével határozza meg, hogy milyen műveletet kell végrehajtania a futtatott eszköz szoftverének verziójától függően.

| Ha az eszköz fut| Végezze el a következő műveletet                                    |
|--------------------------|------------------------|
| 5-ös vagy újabb frissítése, és az eszköz offline állapotban van. <br> Megjelenik egy riasztás, amely szerint az URL-cím nem engedélyezett.|1. A tűzfalszabályok módosításával adja meg a hitelesítési URL-címet. Lásd: [hitelesítési URL-címek](#url-changes-for-aad-authentication).<br>2. [Szerezze be a HRE regisztrációs kulcsát a szolgáltatásból](#aad-based-registration-keys).<br>3. [Kapcsolódjon az StorSimple 8000 Series eszköz Windows PowerShell-felületéhez](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).<br>4. A `Redo-DeviceRegistration` parancsmag használatával regisztrálja az eszközt a Windows PowerShell segítségével. Adja meg az előző lépésben kapott kulcsot.|
| 5-ös vagy újabb, illetve az eszköz online állapotának frissítése.| Nincs szükség felhasználói műveletre.                                       |
| A 4-es vagy korábbi frissítés, és az eszköz offline állapotban van. |1. A tűzfalszabályok módosításával adja meg a hitelesítési URL-címet.<br>2. [Töltse le az 5. frissítést a Catalog Server használatával](storsimple-8000-install-update-5.md#download-updates-for-your-device).<br>3. [Alkalmazza az 5. frissítést a gyorsjavítási módszer használatával](storsimple-8000-install-update-5.md#install-update-5-as-a-hotfix).<br>4. [Szerezze be a HRE regisztrációs kulcsát a szolgáltatásból](#aad-based-registration-keys).<br>5. [Kapcsolódjon az StorSimple 8000 Series eszköz Windows PowerShell-felületéhez](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console). <br>6. A `Redo-DeviceRegistration` parancsmag használatával regisztrálja az eszközt a Windows PowerShell segítségével. Adja meg az előző lépésben kapott kulcsot.|
| A 4-es vagy korábbi frissítés, az eszköz pedig online állapotban van. |A tűzfalszabályok módosításával adja meg a hitelesítési URL-címet.<br> Telepítse az 5. frissítést a Azure Portal.              |
| Állítsa vissza a gyári verziót az 5. frissítés előtt.      |A portálon egy HRE-alapú regisztrációs kulcs látható, miközben az eszköz régebbi szoftvert futtat. Kövesse az előző forgatókönyv lépéseit, amikor az eszköz a 4-es vagy korábbi frissítést futtatja.              |

## <a name="aad-based-registration-keys"></a>HRE-alapú regisztrációs kulcsok

Az 5. frissítés megkezdése a StorSimple 8000 Series-eszközökhöz új HRE-alapú regisztrációs kulcsokat használ. A regisztrációs kulcsok használatával regisztrálja StorSimple Eszközkezelő szolgáltatását az eszközön.

Ha a 4-es vagy korábbi frissítést futtató StorSimple 8000 sorozatú eszközt használ, nem használhatja az új HRE szolgáltatás regisztrációs kulcsait.
Ebben a forgatókönyvben újra kell létrehoznia a szolgáltatás regisztrációs kulcsát. A kulcs újralétrehozása után az új kulcs az összes további eszköz regisztrálására szolgál. A régi kulcs már nem érvényes.

- Az új HRE regisztrációs kulcs 3 nap után lejár.
- A HRE regisztrációs kulcsai csak az 5. vagy újabb frissítést futtató StorSimple 8000 sorozatú eszközökön működnek.
- A HRE regisztrációs kulcsa hosszabb a megfelelő ACS regisztrációs kulcsoknál.

A HRE szolgáltatás regisztrációs kulcsának létrehozásához hajtsa végre az alábbi lépéseket.

#### <a name="to-generate-the-aad-service-registration-key"></a>A HRE szolgáltatás regisztrációs kulcsának előállítása

1. A **StorSimple Eszközkezelő**lépjen a **felügyeleti &gt;**  **kulcsok**elemre. A keresési sáv használatával is megkeresheti a _kulcsokat_.
    
2. Kattintson a **kulcs**előállítása elemre.

    ![Kattintson az újragenerált elemre.](./media/storsimple-8000-aad-registration-key/aad-click-generate-registration-key.png)

3. Másolja az új kulcsot. A régebbi kulcs többé nem fog működni.

    ![Újragenerált megerősítés megerősítése](./media/storsimple-8000-aad-registration-key/aad-registration-key2.png)

    > [!NOTE] 
    > Ha a StorSimple 8000 Series eszközhöz regisztrált szolgáltatáshoz hoz létre StorSimple Cloud Appliance, ne hozzon létre regisztrációs kulcsot, amíg a létrehozás folyamatban van. Várjon, amíg a létrehozás befejeződik, majd hozza létre a regisztrációs kulcsot.

## <a name="next-steps"></a>További lépések

* További információ az [StorSimple 8000 Series-eszközök](storsimple-8000-deployment-walkthrough-u2.md)üzembe helyezéséről.

