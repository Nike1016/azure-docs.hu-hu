---
title: A Pásti-adatbázisok védelme az Azure-ban | Microsoft Docs
description: 'Ismerje meg, Azure SQL Database és SQL Data Warehouse biztonsági eljárásokat a Pásti web-és Mobile-alkalmazások biztonságossá tételéhez. '
services: security
documentationcenter: na
author: techlake
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2018
ms.author: terrylan
ms.openlocfilehash: f7d993799fed637fbec55afc8f06d90c8fc6910f
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68726778"
---
# <a name="best-practices-for-securing-paas-databases-in-azure"></a>Ajánlott eljárások a Pásti-adatbázisok biztonságossá tételéhez az Azure-ban

Ebből a cikkből megbeszéljük [Azure SQL Database](../../sql-database/sql-database-technical-overview.md) és [SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) biztonsági eljárások gyűjteményét, amelyekkel biztosítható a platform-szolgáltatásként szolgáló (Pásti) webes és mobil alkalmazások védelme. Ezek az ajánlott eljárások az Azure tapasztalataiból és az ügyfelek, például saját tapasztalataiból származnak.

A Azure SQL Database és a SQL Data Warehouse az internetalapú alkalmazások számára biztosítanak egy kapcsolódó adatbázis-szolgáltatást. Tekintse meg azokat a szolgáltatásokat, amelyek segítenek az alkalmazások és az adatvédelemben, amikor a Azure SQL Database és a SQL Data Warehouse egy Pásti üzemelő példányban használja:

- Azure Active Directory hitelesítés (SQL Server hitelesítés helyett)
- Azure SQL tűzfal
- Transzparens adattitkosítás (TDE)

## <a name="use-a-centralized-identity-repository"></a>Központosított Identity repository használata
Az Azure SQL Database-adatbázisok két hitelesítési típus egyikének használatára konfigurálhatók:

- Az **SQL-hitelesítés** felhasználónevet és jelszót használ. Az adatbázis logikai kiszolgálójának létrehozásakor megadta a „kiszolgálói rendszergazda” bejelentkezés felhasználónevét és jelszavát. Ezekkel a hitelesítő adatokkal hitelesítheti az adott kiszolgálón található adatbázis-tulajdonost.

- **Azure Active Directory hitelesítés** a Azure Active Directory által felügyelt identitásokat használ, és a felügyelt és integrált tartományok esetében támogatott. Azure Active Directory hitelesítés használatához létre kell hoznia egy másik, "Azure AD admin" nevű kiszolgálói rendszergazdát, amely jogosult az Azure AD-felhasználók és-csoportok felügyeletére. Ez a rendszergazda a normál kiszolgálói rendszergazdák által elvégezhető összes műveletet is végrehajthatja.

[Azure Active Directory a hitelesítés](../../active-directory/develop/authentication-scenarios.md) olyan mechanizmus, amellyel Azure SQL Database és SQL Data Warehousehoz kapcsolódhat Azure Active Directory (ad) identitások használatával. Az Azure AD alternatívát biztosít a SQL Server hitelesítéshez, így leállíthatja a felhasználói identitások elterjedését az adatbázis-kiszolgálók között. Az Azure AD-hitelesítés segítségével központilag kezelheti az adatbázis-felhasználók és más Microsoft-szolgáltatások identitásait egy központi helyen. A központi AZONOSÍTÓk kezelése egyetlen helyet biztosít az adatbázis-felhasználók felügyeletéhez, és egyszerűsíti az engedélyek kezelését.  

### <a name="benefits-of-using-azure-ad-instead-of-sql-authentication"></a>Az Azure AD használatának előnyei az SQL-hitelesítés helyett
- Lehetővé teszi a jelszó elforgatását egyetlen helyen.
- Az adatbázis-engedélyeket külső Azure AD-csoportok használatával kezeli.
- Az integrált Windows-hitelesítés és az Azure AD által támogatott hitelesítés más formáinak engedélyezésével kiküszöböli a jelszavak tárolását.
- A a tárolt adatbázis-felhasználók használatával hitelesíti az identitásokat az adatbázis szintjén.
- Támogatja a jogkivonat-alapú hitelesítést a SQL Databasehoz csatlakozó alkalmazások esetében.
- Támogatja a tartományi összevonást a Active Directory összevonási szolgáltatások (AD FS) (ADFS) vagy a natív felhasználói/jelszó-hitelesítéssel egy helyi Azure AD-hoz tartományi szinkronizálás nélkül.
- A a többtényezős [hitelesítést (MFA)](/azure/active-directory/authentication/multi-factor-authentication)tartalmazó Active Directory univerzális hitelesítést használó SQL Server Management Studio kapcsolatainak támogatása. A számos egyszerű ellenőrzési lehetőségnek (telefonhívás, SMS, intelligens kártya PIN-kóddal vagy mobilalkalmazásos értesítés) köszönhetően az MFA erős hitelesítést kínál. További információ: [az univerzális hitelesítés SQL Database és SQL Data Warehouse](../../sql-database/sql-database-ssms-mfa-authentication.md).

Az Azure AD-hitelesítéssel kapcsolatos további információkért lásd:

- [Azure Active Directory hitelesítés használata SQL Database, felügyelt példány vagy SQL Data Warehouse használatával történő hitelesítéshez](../../sql-database/sql-database-aad-authentication.md)
- [Hitelesítés az Azure SQL Warehouse-szal](../../sql-data-warehouse/sql-data-warehouse-authentication.md)
- [Jogkivonat-alapú hitelesítés támogatása az Azure SQL DB-hez az Azure AD-hitelesítés használatával](../../sql-database/sql-database-aad-authentication.md)

> [!NOTE]
> Annak biztosítása érdekében, hogy a Azure Active Directory a környezetnek megfelelő legyen, tekintse meg az [Azure ad funkcióit és korlátozásait](../../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations).
>
>

## <a name="restrict-access-based-on-ip-address"></a>Hozzáférés korlátozása IP-cím alapján
Létrehozhat olyan tűzfalszabályok, amelyek elfogadható IP-címek tartományait határozzák meg. Ezek a szabályok a kiszolgáló és az adatbázis szintjén is megadhatók. Ha lehetséges, az adatbázis-szintű tűzfalszabályok használatát javasoljuk a biztonság növelése és az adatbázis hordozhatóvé tételéhez. A kiszolgálói szintű tűzfalszabályok a rendszergazdák számára ajánlottak, és ha sok olyan adatbázissal rendelkezik, amelyek azonos hozzáférési követelményekkel rendelkeznek, de nem szeretne időt beállítani az egyes adatbázisok konfigurálására.

SQL Database alapértelmezett forrás IP-cím korlátozásai lehetővé teszik a hozzáférést bármely Azure-címről, beleértve az egyéb előfizetéseket és bérlőket is. Ezt úgy korlátozhatja, hogy csak az IP-címek számára engedélyezze a példány elérését. Még az SQL tűzfal és az IP-címek korlátozásai esetében is erős hitelesítésre van szükség. Tekintse meg a cikk korábbi részében ismertetett javaslatokat.

Az Azure SQL Firewall és az IP-korlátozásokról további információt a következő témakörben talál:

- [Azure SQL Database és SQL Data Warehouse hozzáférés-vezérlés](../../sql-database/sql-database-control-access.md)
- [Tűzfalszabályok Azure SQL Database és SQL Data Warehouse](../../sql-database/sql-database-firewall-configure.md)


## <a name="encrypt-data-at-rest"></a>Inaktív adatok titkosítása
A [transzparens adattitkosítás (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) alapértelmezés szerint engedélyezve van. A TDE transzparens módon titkosítja SQL Server, Azure SQL Database és Azure SQL Data Warehouse az adatfájlokat és a naplófájlokat. A TDE védelmet biztosít a fájlok vagy a biztonsági másolatok közvetlen hozzáférésének veszélye ellen. Ez lehetővé teszi, hogy a meglévő alkalmazások módosítása nélkül titkosítsa az adatvédelmet. A TDE mindig engedélyezve kell maradnia; Ez azonban nem állítja le a támadót a normál hozzáférési útvonal használatával. A TDE lehetővé teszi a különböző iparágakban bevezetett törvények, rendeletek és irányelvek betartását.

Az Azure SQL a TDE kapcsolatos legfontosabb problémákat kezeli. A TDE hasonlóan a helyszíni speciális felügyeletet is meg kell tenni a helyreállítás és az adatbázisok áthelyezése érdekében. Kifinomultabb helyzetekben a kulcsok explicit módon kezelhetők Azure Key Vault a bővíthető kulcskezelő szolgáltatással. Lásd: [a TDE engedélyezése SQL Server a EKM használatával](/sql/relational-databases/security/encryption/enable-tde-on-sql-server-using-ekm). Ez lehetővé teszi, hogy a Bring Your Own Key (BYOK) az Azure Key Vaults BYOK képességgel is rendelkezik.

Az Azure SQL Always Encryptedon keresztül biztosítja [](/sql/relational-databases/security/encryption/always-encrypted-database-engine)az oszlopok titkosítását. Ez lehetővé teszi, hogy csak az engedélyezett alkalmazások férhessenek hozzá a bizalmas oszlopokhoz. Az ilyen típusú titkosítás a titkosított oszlopokhoz tartozó SQL-lekérdezéseket az esélyegyenlőségi alapú értékekre korlátozza.

Szelektív adathoz is használható az alkalmazás szintjének titkosítása. Az adatszuverenitással kapcsolatos problémákat esetenként enyhítheti az adatoknak a megfelelő országban/régióban tárolt kulccsal való titkosításával. Ez megakadályozza a véletlen adatátvitelt a probléma okozása miatt, mivel a kulcs nélkül nem lehet visszafejteni az adatok visszafejtését, feltételezve, hogy erős algoritmus van használatban (például AES 256).

Az adatbázis biztonságossá tételéhez további óvintézkedéseket is használhat, például a biztonságos rendszer megtervezését, a bizalmas eszközök titkosítását és a tűzfal létrehozását az adatbázis-kiszolgálók köré.

## <a name="next-steps"></a>További lépések
Ez a cikk a SQL Database és SQL Data Warehouse biztonsági bevált eljárások gyűjteményét mutatta be, amelyekkel biztonságossá teheti a Pásti web-és mobil alkalmazásait. További információ a Pásti-telepítések biztonságossá tételéről:

- [PaaS-környezetek védelme](paas-deployments.md)
- [A Pásti web-és Mobile-alkalmazások biztonságossá tétele az Azure App Services](paas-applications-using-app-services.md)
