---
title: Az Azure-beli SQL Server biztonsági szempontjai | Microsoft Docs
description: Ez a témakör általános útmutatást nyújt az Azure-beli virtuális gépeken futó SQL Server biztonságossá tételéhez.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: d710c296-e490-43e7-8ca9-8932586b71da
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/23/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 5def34b4e3e7227daf96d952457869658bbce61e
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68855271"
---
# <a name="security-considerations-for-sql-server-in-azure-virtual-machines"></a>Az SQL Server Azure-beli virtuális gépeken történő futtatásának biztonsági szempontjai

Ez a témakör olyan általános biztonsági irányelveket tartalmaz, amelyek segítenek az Azure-beli virtuális gépek (VM) SQL Server példányainak biztonságos elérésében.

Az Azure számos iparági szabályozást és szabványt is teljesít, amelyek lehetővé teszik egy megfelelő megoldás kiépítését a virtuális gépeken futó SQL Serverokkal. További információ az Azure-ban való megfelelőség szabályozásáról: [Azure biztonsági és adatkezelési központ](https://azure.microsoft.com/support/trust-center/).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="control-access-to-the-sql-vm"></a>Az SQL virtuális gép elérésének szabályozása

A SQL Server virtuális gép létrehozásakor gondolja át, hogyan szabályozhatja, hogy ki és milyen mértékben fér hozzá a géphez, és SQL Server. Általánosságban a következőket kell tennie:

- A SQL Serverhoz való hozzáférés korlátozása csak azokra az alkalmazásokra és ügyfelekre, amelyeknek szüksége van rá.
- Kövesse az ajánlott eljárásokat a felhasználói fiókok és jelszavak kezeléséhez.

A következő szakaszokban javaslatokat talál a fenti pontokon való gondolkodásra.

## <a name="secure-connections"></a>Biztonságos kapcsolatok

Ha katalógus-rendszerképpel rendelkező SQL Server virtuális gépet hoz létre, a **SQL Server kapcsolódási** lehetőséggel **helyi (virtuális gépen belüli)** , **magánjellegű (Virtual Networkon belüli**) vagy **nyilvános (Internet)** kapcsolaton keresztül választhat.

![SQL Server kapcsolat](./media/virtual-machines-windows-sql-security/sql-vm-connectivity-option.png)

A legjobb biztonság érdekében válassza a legszigorúbb beállítást a forgatókönyvhöz. Ha például olyan alkalmazást futtat, amely ugyanazon a virtuális gépen SQL Server fér hozzá, akkor a **helyi** a legbiztonságosabb választás. Ha olyan Azure-alkalmazást futtat, amelynek hozzáférésre van szüksége a SQL Serverhoz, akkor a **titkos** kommunikáció csak a megadott Azure- [Virtual Networkon](../../../virtual-network/virtual-networks-overview.md)belül SQL Server. Ha **nyilvános** (Internet) hozzáférést igényel a SQL Server VMhoz, akkor az ebben a témakörben ismertetett egyéb ajánlott eljárások követésével csökkentse a támadási felületet.

A portálon kiválasztott beállítások bejövő biztonsági szabályokat használnak a virtuális gép [hálózati biztonsági csoportján](../../../virtual-network/security-overview.md) (NSG), hogy engedélyezzék vagy megtagadják a virtuális géphez való hálózati forgalmat. Módosíthatja vagy létrehozhat új bejövő NSG szabályokat, amelyek engedélyezik a forgalmat a SQL Server portra (alapértelmezés szerint 1433). Megadhat konkrét IP-címeket is, amelyek számára engedélyezett a porton keresztüli kommunikáció.

![Hálózatbiztonságicsoport-szabályok](./media/virtual-machines-windows-sql-security/sql-vm-network-security-group-rules.png)

A hálózati forgalom korlátozására szolgáló NSG kívül a Windows tűzfalat is használhatja a virtuális gépen.

Ha a klasszikus üzemi modellel rendelkező végpontokat használ, távolítsa el az összes végpontot a virtuális gépen, ha nem használja őket. Az ACL-ek végpontokkal való használatával kapcsolatos utasításokért lásd: [az ACL kezelése egy végponton](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint). Ez a Resource Managert használó virtuális gépek esetében nem szükséges.

Végezetül vegye fontolóra a titkosított kapcsolatok engedélyezését az Azure-beli virtuális gépen a SQL Server adatbázismotor példányához. Konfigurálja az SQL Server-példányt egy aláírt tanúsítvánnyal. További információ: [titkosított kapcsolatok engedélyezése az adatbázismotor és a](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine) [kapcsolati karakterlánc szintaxisával](https://msdn.microsoft.com/library/ms254500.aspx).

## <a name="use-a-non-default-port"></a>Nem alapértelmezett port használata

Alapértelmezés szerint a SQL Server a 1433-as, jól ismert porton figyel. A fokozott biztonság érdekében konfigurálja a SQL Servert egy nem alapértelmezett port figyelésére, például 1401. Ha kiépít egy SQL Server katalógus rendszerképet a Azure Portalban, ezt a portot a **SQL Server beállítások** panelen adhatja meg.

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Ha ezt a kiépítés után szeretné konfigurálni, két lehetőség közül választhat:

- A Resource Manager-alapú [virtuális gépek esetében az SQL Virtual Machines erőforrásból](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource)választhatja ki a **Security (biztonság** ) lehetőséget. Ez lehetőséget biztosít a port módosítására.

  ![TCP-port változása a portálon](./media/virtual-machines-windows-sql-security/sql-vm-change-tcp-port.png)

- A klasszikus virtuális gépek vagy a portálon nem kiépített SQL Server virtuális gépek esetében manuálisan is konfigurálhatja a portot úgy, hogy távolról csatlakozik a virtuális géphez. A konfigurációs lépésekért lásd: [kiszolgáló konfigurálása egy adott TCP-port figyelésére](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-server-to-listen-on-a-specific-tcp-port). Ha ezt a manuális módszert használja, egy Windows tűzfal-szabályt is hozzá kell adnia, hogy engedélyezze a bejövő forgalmat az adott TCP-porton.

> [!IMPORTANT]
> A nem alapértelmezett port meghatározása jó ötlet, ha a SQL Server-port nyilvános internetes kapcsolatokhoz van nyitva.

Ha SQL Server egy nem alapértelmezett portot figyel, a csatlakozáskor meg kell adnia a portot. Vegyünk például egy olyan forgatókönyvet, ahol a kiszolgáló IP-címe 13.55.255.255, és SQL Server figyeli a 1401-es portot. A SQL Serverhoz való kapcsolódáshoz a kapcsolati sztringben kell megadnia `13.55.255.255,1401` .

## <a name="manage-accounts"></a>Fiókok kezelése

Nem szeretné, hogy a támadók egyszerűen kitalálják a fiókok nevét vagy jelszavát. A következő tippek segíthetnek a használatához:

- Hozzon létre egy olyan egyedi helyi rendszergazdai fiókot, amelynek neve nem **rendszergazda**.

- Komplex erős jelszavakat használhat az összes fiókhoz. Az erős jelszavak létrehozásával kapcsolatos további információkért tekintse meg az [erős jelszó létrehozásával](https://support.microsoft.com/instantanswers/9bd5223b-efbe-aa95-b15a-2fb37bef637d/create-a-strong-password) foglalkozó cikket.

- Alapértelmezés szerint az Azure a SQL Server virtuális gép telepítése során kiválasztja a Windows-hitelesítést. Ezért az **sa** -bejelentkezés le van tiltva, és a telepítő jelszót rendel hozzá. Javasoljuk, hogy ne használjon vagy engedélyezze az **sa** bejelentkezést. Ha SQL-bejelentkezésre van szüksége, használja az alábbi stratégiák egyikét:

  - Hozzon létre egy olyan SQL-fiókot, amely a **sysadmin** tagsággal rendelkező egyedi névvel rendelkezik. Ezt a portálon végezheti el az **SQL-hitelesítés** engedélyezésével a kiépítés során.

    > [!TIP] 
    > Ha nem engedélyezi az SQL-hitelesítést a kiépítés során, manuálisan kell módosítania a hitelesítési módot **SQL Server és Windows hitelesítési módra**. További információ: a [kiszolgáló hitelesítési módjának módosítása](https://docs.microsoft.com/sql/database-engine/configure-windows/change-server-authentication-mode).

  - Ha az **sa** bejelentkezést kell használnia, engedélyezze a bejelentkezést a kiépítés után, és rendeljen hozzá egy új erős jelszót.

## <a name="follow-on-premises-best-practices"></a>Helyszíni ajánlott eljárások követése

A jelen témakörben ismertetett eljárások mellett javasoljuk, hogy tekintse át és implementálja a hagyományos helyszíni biztonsági eljárásokat, ahol lehetséges. További információ: [SQL Server telepítésének biztonsági szempontjai](https://docs.microsoft.com/sql/sql-server/install/security-considerations-for-a-sql-server-installation)

## <a name="next-steps"></a>További lépések

Ha a teljesítményre vonatkozó ajánlott eljárások is érdeklik, tekintse meg az [Azure Virtual Machines SQL Server teljesítményével kapcsolatos ajánlott eljárásokat](virtual-machines-windows-sql-performance.md)ismertető témakört.

A SQL Server Azure-beli virtuális gépeken való futtatásával kapcsolatos további témakörökért lásd: [SQL Server az azure Virtual Machines áttekintése](virtual-machines-windows-sql-server-iaas-overview.md). Ha kérdése van az SQL Servert futtató virtuális gépek használatával kapcsolatban, tekintse meg a [gyakori kérdéseket](virtual-machines-windows-sql-server-iaas-faq.md).

