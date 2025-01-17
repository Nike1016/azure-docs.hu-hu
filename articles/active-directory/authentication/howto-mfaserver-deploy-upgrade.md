---
title: Az Azure MFA-kiszolgáló frissítése – Azure Active Directory
description: Lépések és útmutatás az Azure multi-Factor Authentication-kiszolgáló újabb verzióra való frissítéséhez.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 19ca6d82b80a9ed77a842b638ff8e9ff346342e8
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68988542"
---
# <a name="upgrade-to-the-latest-azure-multi-factor-authentication-server"></a>Frissítés a legújabb Azure multi-Factor Authentication-kiszolgálóra

Ez a cikk végigvezeti az Azure multi-Factor Authentication (MFA) Server 6.0-s vagy újabb verziójának frissítésének folyamatán. Ha frissítenie kell a PhoneFactor-ügynök egy korábbi verzióját, tekintse meg a [PhoneFactor-ügynök frissítése az Azure multi-Factor Authentication-kiszolgálóra](howto-mfaserver-deploy-upgrade-pf.md)című témakört.

Ha v6. x-ről vagy régebbiről a v7. x vagy újabb verzióra frissít, az összes összetevő a .NET 2,0-ről a .NET 4,5-re vált. Az összes összetevőhöz a Microsoft C++ Visual 2015 újraterjeszthető frissítése 1 vagy újabb verzió szükséges. Az MFA-kiszolgáló telepítője az összetevők x86-os és x64-es verzióját is telepíti, ha még nincsenek telepítve. Ha a felhasználói portál és a Mobile App Web Service különálló kiszolgálókon fut, akkor az összetevők frissítése előtt telepítenie kell ezeket a csomagokat. A Microsoft letöltőközpontból megkeresheti a legújabb C++ microsoft Visual 2015 újraterjeszthető frissítést. [](https://www.microsoft.com/download/) 

> [!IMPORTANT]
> 2019. július 1-től a Microsoft már nem kínál új, az MFA-kiszolgálót az új üzemelő példányokhoz. A többtényezős hitelesítést a felhasználóktól megkövetelő új ügyfeleknek a felhőalapú Azure multi-Factor Authentication használatát kell használniuk. Azok a meglévő ügyfelek, akik aktiválták az MFA-kiszolgálót a július 1. előtt, le tudják tölteni a legújabb verziót, a jövőbeli frissítéseket, és az aktiválási hitelesítő adatokat a szokásos módon létrehozzák.

Frissítés lépéseinek áttekintése:

* Azure MFA-kiszolgálók frissítése (alárendeltek, majd főkiszolgáló)
* A felhasználói portál példányainak frissítése
* A AD FS-adapter példányainak frissítése

## <a name="upgrade-azure-mfa-server"></a>Az Azure MFA-kiszolgáló frissítése

1. Az Azure [multi-Factor Authentication-kiszolgáló letöltése](howto-mfaserver-deploy.md#download-the-mfa-server) című cikkben található útmutatást követve hozzájuthat az Azure MFA Server telepítőjének legújabb verziójához.
2. Készítsen biztonsági másolatot az MFA-kiszolgáló adatfájljáról a C:\Program Files\Multi-Factor Authentication Server\Data\PhoneFactor.pfdata (feltéve, hogy ez az alapértelmezett telepítési hely) a fő MFA-kiszolgálón.
3. Ha több kiszolgálót is futtat a magas rendelkezésre állás érdekében, módosítsa az MFA-kiszolgálóra hitelesítő ügyfélszoftvereket, hogy ne küldjenek forgalmat a frissített kiszolgálókra. Ha Load balancert használ, távolítsa el az alárendelt MFA-kiszolgálót a terheléselosztó közül, végezze el a frissítést, majd adja hozzá újra a kiszolgálót a farmhoz.
4. Futtassa az új telepítőt az egyes MFA-kiszolgálókon. Először frissítse az alárendelt kiszolgálókat, mert elolvashatják a főkiszolgáló által replikált régi adatfájlt.

   > [!NOTE]
   > A kiszolgálók frissítésekor el kell távolítani a többi MFA-kiszolgálóval való terheléselosztás vagy forgalom megosztását.
   >
   > A telepítő futtatása előtt nem kell eltávolítania az aktuális MFA-kiszolgálót. A telepítő helyben történő frissítést hajt végre. A telepítési útvonal a korábbi telepítésből származó beállításjegyzékből lett kiválasztva, ezért az ugyanarra a helyre települ (például C:\Program Files\Multi-Factor Authentication Server).
  
5. Ha a rendszer kéri, hogy telepítsen egy C++ Microsoft Visual 2015 újraterjeszthető frissítési csomagot, fogadja el a kérést. A csomag x86-os és x64-es verziója is telepítve van.
6. Ha a Web Service SDK-t használja, a rendszer kéri, hogy telepítse az új Web Service SDK-t. Az új Web Service SDK telepítésekor ellenőrizze, hogy a virtuális könyvtár neve megegyezik-e a korábban telepített virtuális könyvtárral (például Phonefactorwebservicesdk).
7. Ismételje meg a lépéseket az összes alárendelt kiszolgálón. Léptesse elő a beosztottak egyikét, hogy az új főkiszolgáló legyen, majd frissítse a régi főkiszolgáló-kiszolgálót.

## <a name="upgrade-the-user-portal"></a>A felhasználói portál frissítése

A szakaszra való áttérés előtt fejezze be az MFA-kiszolgálók frissítését.

1. Készítsen biztonsági másolatot a felhasználói portál telepítési helyének virtuális könyvtárában található web. config fájlról (például C:\inetpub\wwwroot\MultiFactorAuth). Ha bármilyen változás történt az alapértelmezett témánál, készítsen biztonsági másolatot a App_Themes\Default mappáról is. Jobb, ha létrehoz egy másolatot az alapértelmezett mappáról, és új témát hoz létre, mint az alapértelmezett téma módosításához.
2. Ha a felhasználói portál ugyanazon a kiszolgálón fut, mint a többi MFA-kiszolgáló összetevő, az MFA-kiszolgáló telepítése arra kéri, hogy frissítse a felhasználói portált. Fogadja el a parancssort, és telepítse a felhasználói portál frissítését. Győződjön meg arról, hogy a virtuális könyvtár neve megegyezik a korábban telepített virtuális könyvtárral (például MultiFactorAuth).
3. Ha a felhasználói portál saját kiszolgálón található, másolja a MultiFactorAuthenticationUserPortalSetup64. msi fájlt az egyik MFA-kiszolgáló telepítési helyére, és helyezze azt a felhasználói portál webkiszolgálóra. Futtassa a telepítőt.

   Ha hiba történik, "a Microsoft Visual C++ 2015 újraterjeszthető frissítésének 1. vagy újabb verziója szükséges," töltse le és telepítse a legújabb frissítési csomagot a [Microsoft letöltőközpontból](https://www.microsoft.com/download/). Telepítse az x86-os és x64-es verziókat is.

4. A frissített felhasználói portál szoftver telepítése után hasonlítsa össze az 1. lépésben az új web. config fájllal létrehozott web. config biztonsági mentést. Ha nem található új attribútum az új web. config fájlban, másolja a Backup web. config fájlt a virtuális könyvtárba, hogy felülírja az újat. Egy másik lehetőség a appSettings és a Web Service SDK URL-címének másolása/beillesztése a Backup fájlból az új web. config fájlba.

Ha több kiszolgálón van a felhasználói portál, ismételje meg a telepítést.

## <a name="upgrade-the-mobile-app-web-service"></a>A Mobile App Web Service frissítése

> [!NOTE]
> Az 8,0-nál régebbi Azure MFA-kiszolgáló verzióról 8.0 +-ra való frissítés után a Mobile App Web Service a frissítés után is eltávolítható

## <a name="upgrade-the-ad-fs-adapters"></a>A AD FS adapterek frissítése

Az erre a szakaszra való áttérés előtt fejezze be az MFA-kiszolgálók és a felhasználói portál frissítését.

### <a name="if-mfa-runs-on-different-servers-than-ad-fs"></a>Ha az MFA különböző kiszolgálókon fut, mint AD FS

Ezek az utasítások csak akkor érvényesek, ha a multi-Factor Authentication kiszolgálót külön futtatja a AD FS-kiszolgálókról. Ha mindkét szolgáltatás ugyanazon a kiszolgálón fut, ugorja át ezt a szakaszt, és folytassa a telepítési lépésekkel. 

1. Mentse a AD FSban regisztrált MultiFactorAuthenticationAdfsAdapter. config fájl másolatát, vagy exportálja a konfigurációt a következő PowerShell-parancs használatával: `Export-AdfsAuthenticationProviderConfigurationData -Name [adapter name] -FilePath [path to config file]`. Az adapter neve "WindowsAzureMultiFactorAuthentication" vagy "AzureMfaServerAuthentication" lehet a korábban telepített verziótól függően.
2. Másolja a következő fájlokat az MFA-kiszolgáló telepítési helyéről a AD FS kiszolgálókra:

   * MultiFactorAuthenticationAdfsAdapterSetup64.msi
   * Register-MultiFactorAuthenticationAdfsAdapter.ps1
   * Unregister-MultiFactorAuthenticationAdfsAdapter.ps1
   * MultiFactorAuthenticationAdfsAdapter.config

3. Szerkessze a Register-MultiFactorAuthenticationAdfsAdapter. ps1 parancsfájlt `-ConfigurationFilePath [path]` úgy, hogy hozzáadja a `Register-AdfsAuthenticationProvider` parancs végéhez. Cserélje le a *[Path]* parancsot a MultiFactorAuthenticationAdfsAdapter. config fájl teljes elérési útjára, vagy az előző lépésben exportált konfigurációs fájlra.

   Ellenőrizze az új MultiFactorAuthenticationAdfsAdapter. config attribútumait, hogy megegyezzenek-e a régi konfigurációs fájllal. Ha az új verzióban bármely attribútum hozzá lett adva vagy el lett távolítva, másolja az attribútum értékeit a régi konfigurációs fájlból az újat, vagy módosítsa a régi konfigurációs fájlt az egyeztetéshez.

### <a name="install-new-ad-fs-adapters"></a>Új AD FS adapterek telepítése

> [!IMPORTANT]
> A felhasználók nem lesznek szükségesek kétlépéses ellenőrzés végrehajtásához a szakasz 3-8. lépésében. Ha több fürtben van AD FS konfigurálva, a leállás elkerülése érdekében a többi fürttől függetlenül eltávolíthatja, frissítheti és visszaállíthatja a farmban lévő összes fürtöt.

1. Távolítson el néhány AD FS kiszolgálót a farmból. Frissítse ezeket a kiszolgálókat, amíg a többiek még futnak.
2. Telepítse az új AD FS-adaptert az AD FS farmból eltávolított összes kiszolgálóra. Ha az MFA-kiszolgáló minden AD FS-kiszolgálóra telepítve van, az MFA-kiszolgáló rendszergazdai UX-en keresztül frissítheti. Ellenkező esetben frissítse a MultiFactorAuthenticationAdfsAdapterSetup64. msi fájl futtatásával.

   Ha hiba történik, "a Microsoft Visual C++ 2015 újraterjeszthető frissítésének 1. vagy újabb verziója szükséges," töltse le és telepítse a legújabb frissítési csomagot a [Microsoft letöltőközpontból](https://www.microsoft.com/download/). Telepítse az x86-os és x64-es verziókat is.

3. Nyissa meg a **AD FS** > **hitelesítési házirendek** > **globális többtényezős hitelesítési házirend szerkesztése**lehetőséget. Törölje a **WindowsAzureMultiFactorAuthentication** vagy a **AzureMFAServerAuthentication** (a telepített aktuális verziótól függően).

   Ha ez a lépés elkészült, a kétlépéses ellenőrzés az MFA-kiszolgálón keresztül nem érhető el ebben a AD FS-fürtben, amíg el nem végzi a 8. lépést.

4. A Unregister-MultiFactorAuthenticationAdfsAdapter. ps1 PowerShell-parancsfájl futtatásával szüntesse meg a AD FS-adapter korábbi verziójának regisztrációját. Győződjön meg arról, hogy a *-Name* paraméter ("WindowsAzureMultiFactorAuthentication" vagy "AzureMFAServerAuthentication") megegyezik a 3. lépésben megjelenő névvel. Ez az összes olyan kiszolgálóra vonatkozik, amely ugyanabban a AD FS fürtben van, mivel központi konfiguráció van.
5. Regisztrálja az új AD FS adaptert a Register-MultiFactorAuthenticationAdfsAdapter. ps1 PowerShell-parancsfájl futtatásával. Ez az összes olyan kiszolgálóra vonatkozik, amely ugyanabban a AD FS fürtben van, mivel központi konfiguráció van.
6. Indítsa újra a AD FS szolgáltatást minden olyan kiszolgálón, amely el lett távolítva a AD FS farmról.
7. Adja hozzá újra a frissített kiszolgálókat a AD FS farmhoz, és távolítsa el a többi kiszolgálót a farmból.
8. Nyissa meg a **AD FS** > **hitelesítési házirendek** > **globális többtényezős hitelesítési házirend szerkesztése**lehetőséget. **AzureMfaServerAuthentication**-keresés.
9. Ismételje meg a 2. lépést a AD FS farmból eltávolított kiszolgálók frissítéséhez, majd indítsa újra a AD FS szolgáltatást ezeken a kiszolgálókon.
10. Adja hozzá ezeket a kiszolgálókat a AD FS farmhoz.

## <a name="next-steps"></a>További lépések

* Példák az [Azure multi-Factor Authentication és a harmadik féltől származó VPN-EK speciális forgatókönyvekre](howto-mfaserver-nps-vpn.md)

* [MFA-kiszolgáló szinkronizálása a Windows Server Active Directory](howto-mfaserver-dir-ad.md)

* [Windows-hitelesítés konfigurálása](howto-mfaserver-windows.md) az alkalmazásokhoz
