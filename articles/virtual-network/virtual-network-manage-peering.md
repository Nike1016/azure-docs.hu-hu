---
title: Létrehozása, módosítása vagy törlése az Azure virtuális hálózatok közötti társviszony |} A Microsoft Docs
description: Ismerje meg, hogyan létrehozása, módosítása vagy egy virtuális hálózati társviszony törlése.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/01/2019
ms.author: anavin
ms.openlocfilehash: c77608aa7a4d1410277dfbe259fb1f55ea8324ae
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449380"
---
# <a name="create-change-or-delete-a-virtual-network-peering"></a>Létrehozása, módosítása vagy egy virtuális hálózati társviszony törlése

Ismerje meg, hogyan létrehozása, módosítása vagy egy virtuális hálózati társviszony törlése. Virtuális hálózatok közötti társviszony lehetővé teszi, hogy a virtuális hálózatok ugyanabban a régióban, és (más néven globális virtuális társhálózatok létesítésének)-régiók között az Azure gerinchálózatán keresztül. Társviszonyba a virtuális hálózatok továbbra is külön erőforrásként kezelhetők. Ha most ismerkedik a virtuális hálózatok közötti társviszony, többet is megtudhat a kapcsolatos a [társviszony-létesítés virtuális hálózatok áttekintése](virtual-network-peering-overview.md) vagy; Ehhez hajtsa végre egy [oktatóanyag](tutorial-connect-virtual-networks-portal.md).

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ez a cikk bármely szakaszban ismertetett lépések elvégzése előtt hajtsa végre a következőket:

- Ha még nincs Azure-fiókkal, regisztráljon egy [ingyenes próbafiókot](https://azure.microsoft.com/free).
- A portál használatával, nyissa meg a https://portal.azure.com, és jelentkezzen be egy olyan fiókkal, amely rendelkezik a [szükséges engedélyek](#permissions) társviszonyok dolgozhat.
- Ha a PowerShell-parancsokkal cikkben leírt feladatok elvégzéséhez, vagy futtassa a parancsokat a [Azure Cloud Shell](https://shell.azure.com/powershell), vagy a számítógépről futtatja a Powershellt. Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Ehhez az oktatóanyaghoz az Azure PowerShell-modul verzióját 1.0.0 vagy újabb. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha futtatja a Powershellt helyileg, is futtatni szeretné `Connect-AzAccount` egy olyan fiókkal, amely rendelkezik a [szükséges engedélyek](#permissions) társviszony-létesítéshez, dolgozhat kapcsolat létrehozása az Azure-ral.
- Ha használja az Azure parancssori felület (CLI) parancsai cikkben leírt feladatok elvégzéséhez, vagy futtassa a parancsokat a [Azure Cloud Shell](https://shell.azure.com/bash), vagy a parancssori felület futtatásával a számítógépről. Ehhez az oktatóanyaghoz az Azure CLI 2.0.31-es verzió vagy újabb. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli). Ha helyileg futtatja az Azure CLI, is futtatni szeretné `az login` egy olyan fiókkal, amely rendelkezik a [szükséges engedélyek](#permissions) társviszony-létesítéshez, dolgozhat kapcsolat létrehozása az Azure-ral.

Jelentkezzen be, vagy csatlakozhat az Azure-ban, a fiókot hozzá kell rendelni a [hálózati közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkör vagy egy [egyéni szerepkör](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) szerepel a megfelelő műveleteket rendelt [engedélyek ](#permissions).

## <a name="create-a-peering"></a>Társviszony-létesítés

Mielőtt létrehozná a társviszonyt, ismerkedjen meg a követelményeket és korlátozásokat és [szükséges engedélyek](#permissions).

1. Az Azure portal tetején a Keresés mezőbe írja be *virtuális hálózatok* kifejezést a keresőmezőbe. Amikor **virtuális hálózatok** jelennek meg a keresési eredmények közül válassza ki azt. Ne válassza **virtuális hálózatok (klasszikus)** Ha, megjelenik a listán, mert nem hozható létre a társviszony a klasszikus üzemi modellel üzembe helyezett virtuális hálózatról.
2. A társviszony-létesítés kívánt listában jelölje ki a virtuális hálózat.
3. A **beállítások**válassza **Társviszonyok**.
4. Válassza a **+ Hozzáadás** lehetőséget. 
5. <a name="add-peering"></a>Adja meg vagy válassza ki a következő beállítások értékeit:
    - **név:** A társviszony-létesítés neve a virtuális hálózaton belül egyedinek kell lennie.
    - **Virtuális hálózat üzemelőpéldány-modellje:** Válassza ki, melyik üzembe helyezési modellben a virtuális hálózati társviszonyt szeretne segítségével telepítve lett.
    - **Tudom, hogy az erőforrás-Azonosítómat:** Ha a virtuális hálózati társviszonyt szeretne olvasási hozzáféréssel rendelkezik, ne jelölje be ezt a jelölőnégyzetet. Ha nem rendelkezik olvasási hozzáférés a virtuális hálózathoz vagy előfizetéshez szeretne társviszonyt, ezt a jelölőnégyzetet. Adja meg a virtuális hálózat a társviszonyt szeretne teljes erőforrás-Azonosítóját a **erőforrás-azonosító** dobozra, amelyik jelent meg, ha a mezőben be van jelölve. Az erőforrás-Azonosítóját meg kell lennie egy virtuális hálózat, amely azonos, vagy [támogatott különböző](#requirements-and-constraints) Azure [régió](https://azure.microsoft.com/regions) a virtuális hálózattal. A teljes erőforrás-azonosító hasonlít `/subscriptions/<Id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>`. Az erőforrás-azonosítója egy virtuális hálózat egy virtuális hálózat tulajdonságainak megtekintésével beolvasása. Virtuális hálózat tulajdonságainak megtekintése kapcsolatban lásd: [virtuális hálózatok kezelése](manage-virtual-network.md#view-virtual-networks-and-settings). Ha az előfizetés tartozik, mint a virtuális hálózattal a társviszonyt próbál létrehozni az előfizetést egy másik Azure Active Directory-bérlő, először adja hozzá a felhasználó minden bérlő, egy [vendégfelhasználó](../active-directory/b2b/add-users-administrator.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-guest-users-to-the-directory) ellentétes a bérlőben.
    - **Előfizetés:** Válassza ki a [előfizetés](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) a virtuális hálózat társviszonyt szeretne. Egy vagy több előfizetés jelennek meg, attól függően, hány előfizetés az olvasási hozzáféréssel rendelkezik. Ha bejelölte a **erőforrás-azonosító** jelölőnégyzetét, ez a beállítás nem érhető el.
    - **Virtuális hálózat:** Válassza ki a virtuális hálózati társviszonyt szeretne. Vagy az Azure üzemi modellel létrehozott virtuális hálózatban választhat. Ha szeretne egy másik régióban lévő virtuális hálózat kiválasztása, jelöljön ki egy virtuális hálózat egy [támogatott régió](#cross-region). Ahhoz, hogy a listában látható a virtuális hálózat olvasási hozzáféréssel kell rendelkeznie. Ha egy virtuális hálózat szerepel, de szürkén jelenik meg, lehet, mert a virtuális hálózatok átfedésben a virtuális hálózat címterét. Ha a virtuális hálózati cím tárolóhelyek között átfedés van, akkor nem létesíthető társviszony. Ha bejelölte a **erőforrás-azonosító** jelölőnégyzetét, ez a beállítás nem érhető el.
    - **Virtuális hálózati hozzáférés engedélyezése:** Válassza ki **engedélyezve** (alapértelmezett), ha azt szeretné, a két virtuális hálózat közötti kommunikáció engedélyezése. Virtuális hálózatok közötti kommunikáció lehetővé teszi, hogy bármelyik virtuális hálózatban kommunikálnak egymással sávszélességgel és a késés, mintha ugyanahhoz a virtuális hálózathoz kapcsolódnak kapcsolódó erőforrásokhoz. A két virtuális hálózat-erőforrások közötti minden kommunikáció van az Azure privát hálózaton keresztül. A **VirtualNetwork** szolgáltatáscímke hálózati biztonsági csoportok magában foglalja a virtuális hálózat és a virtuális társhálózatban működő. Hálózati biztonsági csoport szolgáltatáscímkék kapcsolatos további információkért lásd: [hálózati biztonsági csoportok áttekintése](security-overview.md#service-tags). Válassza ki **letiltott** Ha nem szeretné, hogy a virtuális társhálózatba tartozó virtuális hálózat bejövő forgalmat. Választhat **letiltott** Ha egy virtuális hálózatot és egy másik virtuális hálózat már társviszonyban álló, de alkalmanként le kívánja tiltani a virtuális hálózatok közötti adatforgalmat. Azt tapasztalhatja, engedélyezése vagy letiltása sokkal kényelmesebb, mint törlésével és ismételt létrehozásával a társviszony-létesítéseket. Ha ez a beállítás le van tiltva, adatforgalom ugyanis nem a virtuális társhálózatok között.
    - **Továbbított forgalom engedélyezése:** Ezt a jelölőnégyzetet, hogy a forgalom *továbbított* egy hálózati virtuális készüléket egy virtuális hálózaton (amely a virtuális hálózat nem származik) a flow, a virtuális hálózaton, egy társviszony-létesítésen keresztül. Vegyük példaként a három virtuális hálózatok Spoke1 Spoke2 és Eseményközpont neve. Minden küllő virtuális hálózat és a központi virtuális hálózaton között társviszonyt létezik, de a társviszonyok nem léteznek a küllő virtuális hálózatok között. Egy hálózati virtuális készüléket a központi virtuális hálózaton van üzembe helyezve, és a felhasználó által megadott útvonalak érvényesek minden küllő virtuális hálózat, amely a hálózati virtuális készüléken keresztül az alhálózatok közötti forgalom irányítására. Ha a jelölőnégyzet nincs bejelölve, minden küllő virtuális hálózat és az eseményközpont virtuális hálózat között társviszony-létesítéshez, adatforgalom ugyanis nem a küllő virtuális hálózatok között, mert a hub nem továbbít a virtuális hálózatok közötti forgalom. Ez a funkció lehetővé teszi, hogy a továbbított forgalom a társviszony-létesítésen keresztül, míg azt nem hoz létre minden olyan felhasználó által megadott útvonalakat vagy a hálózati virtuális berendezések. Felhasználó által definiált útvonalak és hálózati virtuális berendezések külön kell létrehozni. Ismerje meg [felhasználó által megadott útvonalak](virtual-networks-udr-overview.md#user-defined). Nem kell ellenőrizze ezt a beállítást, ha a forgalmat továbbítja az Azure VPN-átjárón keresztül a virtuális hálózatok között.
    - **Átjárótranzit engedélyezése:** Jelölje be ezt a jelölőnégyzetet, ha rendelkezik a virtuális hálózathoz csatlakozó virtuális hálózati átjáró, és szeretné engedélyezni az átjárón keresztül a virtuális társhálózatba tartozó virtuális hálózatból a forgalmat. Például a virtuális hálózathoz csatlakoztatható egy virtuális hálózati átjárón keresztül a helyszíni hálózathoz. Az átjáró egy expressroute-on vagy VPN-átjáró lehet. A jelölőnégyzet bejelölésével lehetővé teszi, hogy a virtuális társhálózatban működő az átjáró csatlakozik a helyi hálózatot a virtuális hálózaton keresztül érkező forgalmat. Ha bejelöli ezt a jelölőnégyzetet, a virtuális társhálózatban működő nem lehet beállítva átjáró. A virtuális Társhálózat kell rendelkeznie a **távoli átjárók használata** jelölőnégyzet be van jelölve, a társviszonyt a másik virtuális hálózatra a virtuális hálózat beállítása során. Ha üresen hagyja ezt a jelölőnégyzetet bejelölve (alapértelmezett), a virtuális társhálózatban működő szolgáltatásból továbbra is a forgalmat a virtuális hálózat, de nem flow, a virtuális hálózathoz csatlakoztatott virtuális hálózati átjárón keresztül. Ha a társviszony-létesítés egy virtuális hálózat (Resource Manager) és a egy virtuális hálózat (klasszikus), az átjáró a virtuális hálózatban (Resource Manager) kell lennie.

       Felül, továbbítja a forgalmat a helyszíni hálózathoz, a VPN-átjáró a virtuális hálózatot az átjáró nem található, anélkül, hogy hozható egymással társviszonyba kellene a virtuális hálózatok társviszonyba virtuális hálózatok közötti hálózati forgalom is továbbíthatja. Továbbítsa a forgalmat a VPN-átjáró használata akkor hasznos, ha egy VPN-átjárót használ a hubon szeretne (a küllős ismertetett példa **továbbított forgalom engedélyezése**), amelyek nem küllő virtuális hálózatok közötti forgalom irányítása a virtuális hálózat társviszonyban állnak egymással. Lehetővé teszi az átjáró használatának átvitel kapcsolatos további információkért lásd: [átvitel VPN-átjáró konfigurálása a virtuális hálózatok közötti társviszony](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Ehhez a forgatókönyvhöz szükséges implementálása a felhasználó által definiált útvonalak, adja meg a virtuális hálózati átjáró a következő ugrás típusa. Ismerje meg [felhasználó által megadott útvonalak](virtual-networks-udr-overview.md#user-defined). Csak egy VPN-átjáró megadhatja egy felhasználó által definiált útvonal következő ugrási típussal, a következő ugrástípusra terelve egy felhasználó által megadott útvonal nem adható meg egy ExpressRoute-átjárót.

    - **Távoli átjárók használata:** Ezt a jelölőnégyzetet a virtuális hálózati társviszony-létesítés még a virtuális hálózathoz csatlakozó virtuális hálózati átjáró funkción keresztül érkező adatforgalom engedélyezéséhez. Például a virtuális hálózati társviszony-létesítés már rendelkezik csatlakoztatott VPN-átjáró, amely lehetővé teszi a kommunikációt a helyszíni hálózathoz.  A jelölőnégyzet bejelölésével engedélyezi a forgalmat ezen keresztül a VPN-átjáró csatlakozik a virtuális társhálózatban működő virtuális hálózat. Ha bejelöli ezt a jelölőnégyzetet, a virtuális társhálózatban működő rendelkeznie kell egy virtuális hálózati átjáró csatlakozik, és rendelkeznie kell a **átjárótranzit engedélyezése** jelölőnégyzet be van jelölve. Ha üresen hagyja a nem ellenőrzött (alapértelmezett), a virtuális társhálózatba tartozó virtuális hálózatból is továbbra is adatforgalom a virtuális hálózaton, de nem a folyamat egy virtuális hálózati átjárón keresztül csatlakozik a virtuális hálózathoz.
    A virtuális hálózatnak csak egy társviszonynál lehet ez a beállítás engedélyezve van.

        Ha már van beállítva a virtuális hálózati átjáró nem használható távoli átjárókat. Az átvitel során az átjáró használatával kapcsolatos további tudnivalókért lásd: [átvitel VPN-átjáró konfigurálása a virtuális hálózatok közötti társviszony](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

6. Válassza ki **OK** felvenni a kiválasztott virtuális hálózati társviszonyt.

Társviszony-létesítés virtuális hálózatok különböző előfizetésekben és üzemi modellek között megvalósításának lépésenkénti útmutatójáért lásd: [lépések](#next-steps).

### <a name="commands"></a>Parancsok

- **Az Azure CLI**: [az hálózat virtuális hálózatok közötti társviszony létrehozása](/cli/azure/network/vnet/peering)
- **PowerShell**: [Add-AzVirtualNetworkPeering](/powershell/module/az.network/add-azvirtualnetworkpeering)

## <a name="view-or-change-peering-settings"></a>Társviszony-létesítési beállításainak megtekintése vagy módosítása

Mielőtt módosítja a társviszonyt, ismerkedjen meg a követelményeket és korlátozásokat és [szükséges engedélyek](#permissions).

1. Írja be a keresőmezőbe, a portál tetején, *virtuális hálózatok* kifejezést a keresőmezőbe. Amikor **virtuális hálózatok** jelennek meg a keresési eredmények közül válassza ki azt. Ne válassza **virtuális hálózatok (klasszikus)** Ha, megjelenik a listán, mert nem hozható létre a társviszony a klasszikus üzemi modellel üzembe helyezett virtuális hálózatról.
2. Válassza ki a virtuális hálózati társviszony-létesítési beállításait módosítani szeretné a listában.
3. A **beállítások**válassza **Társviszonyok**.
4. Válassza ki a társviszony-létesítés szeretné megtekinteni vagy módosítani a beállításokat.
5. Módosítsa a megfelelő beállítást. Olvassa el a beállításokat az egyes beállítások a [5. lépés](#add-peering) Create társviszonyt.
6. Kattintson a **Mentés** gombra.

**Parancsok**

- **Az Azure CLI**: [az hálózat virtuális hálózatok közötti társviszony-létesítési lista](/cli/azure/network/vnet/peering) lista társviszonyok egy virtuális hálózathoz, hogy [az network vnet peering show](/cli/azure/network/vnet/peering) egy adott társviszony-létesítéshez beállítások megjelenítéséhez és [az hálózati virtuális hálózatok közötti társviszony-létesítési frissítés](/cli/azure/network/vnet/peering) társviszony-létesítési beállítások módosításához. |}
- **PowerShell**: [Get-AzVirtualNetworkPeering](/powershell/module/az.network/get-azvirtualnetworkpeering) lekérdezni a társviszony-létesítési beállítások megtekintése és [Set-AzVirtualNetworkPeering](/powershell/module/az.network/set-azvirtualnetworkpeering) beállítások módosításához.

## <a name="delete-a-peering"></a>A társviszony-létesítés törlése

Mielőtt törölné a társviszonyt, győződjön meg, hogy a fiók rendelkezik-e a [szükséges engedélyek](#permissions).

A társviszony-létesítés törlése esetén a virtuális hálózat közötti forgalom már nem elkezdenek beérkezni a virtuális társhálózatban működő. Resource Managerrel üzembe helyezett virtuális hálózatok társviszonyban állnak, ha minden virtuális hálózathoz tartozik, a más virtuális hálózati társviszonyt. Egy virtuális hálózati társviszony törlése letiltja a virtuális hálózatok közötti kommunikációt, de nem törli a többi virtuális hálózati társviszonyt. A társviszony-létesítés állapota a társviszony-létesítéshez, hogy megtalálható-e a virtuális hálózat **leválasztott**. Ön nem tudja újra létrehozni a társviszony-létesítés mindaddig, amíg újra létrehozza a társviszonyt az első virtuális hálózat és a társviszony-létesítés állapota mindkét virtuális hálózat módosításainak *csatlakoztatva*.

Ha azt szeretné, hogy a virtuális hálózatok néha kommunikációhoz, de nem mindig, ahelyett, hogy a társviszony törlése, beállíthatja a **virtuális hálózati hozzáférés engedélyezése** beállítást **letiltott** helyette. Megtudhatja, hogyan, olvassa el a létrehozás 6. lépés egy társviszony-létesítési című szakaszát. Előfordulhat, hogy letiltása és engedélyezése a hálózati hozzáférés könnyebb, mint a társviszonyok újbóli létrehozása és törlése.

1. Írja be a keresőmezőbe, a portál tetején, *virtuális hálózatok* kifejezést a keresőmezőbe. Amikor **virtuális hálózatok** jelennek meg a keresési eredmények közül válassza ki azt. Ne válassza **virtuális hálózatok (klasszikus)** Ha, megjelenik a listán, mert nem hozható létre a társviszony a klasszikus üzemi modellel üzembe helyezett virtuális hálózatról.
2. Válassza ki a virtuális hálózat a listában, hogy törli a társviszony-létesítést.
3. A **beállítások**válassza **Társviszonyok**.
4. Válassza ki a jobb oldalon, a társviszony-létesítés törli, **...** válassza **törlése**, majd **Igen** törölni a társviszonyt az első virtuális hálózatról.
5. Az előző lépéseket a társviszony-létesítést a más virtuális hálózati társviszony törlése.

**Parancsok**

- **Az Azure CLI**: [az hálózat virtuális hálózatok közötti társviszony-létesítés törlése](/cli/azure/network/vnet/peering)
- **PowerShell**: [Remove-AzVirtualNetworkPeering](/powershell/module/az.network/remove-azvirtualnetworkpeering)

## <a name="requirements-and-constraints"></a>Követelmények és korlátozások

- <a name="cross-region"></a>Az ugyanabban a régióban, vagy eltérő régiókban lévő virtuális hálózatok társviszonyt. Különböző régiókban található virtuális hálózatok társviszony-létesítés is nevezik *globális virtuális társhálózatok létesítésének*. 
- Amikor hoz létre, globális társviszonyt, a társviszonyban álló virtuális hálózatba bármely Azure nyilvános felhő régióban vagy a China cloud régiók vagy a kormányzati felhő-régiók is létezik. Nem lehet a felhők között társviszonyt létesíteni. Azure nyilvános felhő virtuális hálózat például nem létesíthető társviszony az Azure China cloud egy virtuális hálózathoz.
- Egy virtuális hálózatban lévő erőforrásokra globálisan két társviszonyban álló virtuális hálózatban lévő alapvető belső terheléselosztó előtérbeli IP-címe nem lehet kommunikálni. Alapszintű Load Balancer támogatása csak az azonos régión belüli létezik. A Standard Load Balancer támogatását is, a virtuális hálózatok közötti Társviszony és a globális virtuális társhálózatok létesítésének létezik. Egy alapszintű load balancer, amely nem fog működni a globális virtuális hálózatok közötti társviszony-Létesítésen keresztül használó szolgáltatások vannak dokumentálva [itt.](virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)
- Távoli átjárók használata, vagy a globális virtuális társhálózatok és helyileg társviszonyban álló virtuális hálózatba átjárótranzit engedélyezése.
- A virtuális hálózatok lehetnek azonos vagy eltérő előfizetésekben. Különböző előfizetésekben található virtuális hálózatok társviszonyba állítása akkor, ha mindkét előfizetés társíthatók az azonos vagy eltérő Azure Active Directory-bérlő. Ha még nem rendelkezik az AD-bérlő, [hozzon létre egyet](../active-directory/develop/quickstart-create-new-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json-a-new-azure-ad-tenant). A különböző Azure Active Directory-bérlőkhöz társított előfizetésekben virtuális hálózatok közötti társviszony-létesítés támogatása a portál nem érhető el. Parancssori felület, a PowerShell vagy a sablonok is használhatja.
- A virtuális hálózat társviszonyt, egymást nem átfedő IP-címtereket kell rendelkeznie.
- Adja hozzá a címtartományok nem, vagy törlése címtartományokat egy virtuális hálózat címtere egy virtuális hálózatot és egy másik virtuális hálózat társviszonyba. Hozzáadása vagy törlése címtartományokat, a társviszony-létesítés törlése, hozzáadása vagy eltávolítása a címtartományt, majd újra létrehozza a társviszonyt. Adja hozzá a-címtartományokat, vagy távolítsa el a címtartomány a virtuális hálózatok, lásd: [virtuális hálózatok kezelése](manage-virtual-network.md).
- Erőforrás-kezelő vagy a Resource Managerrel üzembe helyezett egy virtuális hálózathoz a klasszikus üzemi modellel üzembe helyezett virtuális hálózaton üzembe helyezett két virtuális hálózat társviszonyt. A klasszikus üzemi modellel létrehozott két virtuális hálózat nem társviszonyt. Ha még nem ismeri az Azure üzembe helyezési modellel, olvassa el a [Azure üzemi modelljeinek megismerése](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) cikk. A [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V) használatával összeköthető két, a klasszikus üzembehelyezési modellel létrehozott virtuális hálózat.
- Két, Resource Managerrel létrehozott virtuális hálózat közötti társviszony létrehozása esetén mindkét érintett virtuális hálózathoz konfigurálni kell egy társítást. A társviszony-létesítés állapota a következők egyikét látja: 
  - *A kezdeményezett:* Az első virtuális hálózatról a második virtuális hálózati társviszony létrehozásakor van-e a társviszony-létesítés állapota *kezdeményezve*. 
  - *Csatlakoztatva:* A társviszonyt a második virtuális hálózatról az első virtuális hálózat létrehozásakor a társviszony-létesítés állapota *csatlakoztatva*. Ha megtekinti az első virtuális hálózat társítási állapota, láthatja a állapota megváltozott *kezdeményezve* való *csatlakoztatva*. A társviszony-létesítés nem sikerült jön létre, amíg a társviszony-létesítés állapota mindkét virtuális hálózati társviszony-létesítéshez *csatlakoztatva*.
- Ha a társviszony a klasszikus üzemi modellel létrehozott virtuális hálózat a Resource Managerrel létrehozott virtuális hálózatban, csak konfigurálja a Resource Managerrel üzembe helyezett virtuális hálózat társviszonyt. Egy virtuális hálózat (klasszikus) vagy a klasszikus üzemi modellel üzembe helyezett virtuális hálózatok közötti társviszony nem konfigurálható. Amikor létrehozza a társítást a virtuális hálózatról (Resource Manager) a virtuális hálózat (klasszikus), a társviszony-létesítés állapota van *frissítés*, majd hamarosan vált *csatlakoztatva*.
- A társviszony jön létre két virtuális hálózat között. Társviszonyok nem tranzitív el. Ha közötti társviszonyok hoz létre:
  - VirtualNetwork1 & VirtualNetwork2
  - VirtualNetwork2 & VirtualNetwork3

  Nincs nem, nincs társviszony-létesítés VirtualNetwork1 és keresztül VirtualNetwork2 VirtualNetwork3 között. Ha szeretne létrehozni egy virtuális hálózati társviszony-létesítés VirtualNetwork1 és VirtualNetwork3 között, akkor egy VirtualNetwork1 és VirtualNetwork3 közötti társviszony-létesítés.
- Nevek használatával Azure névfeloldási szolgáltatása alapértelmezett virtuális társhálózatok nem oldható fel. Más virtuális hálózatok neveinek feloldásához, használjon [Azure DNS használata saját tartományok esetében](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy egy egyéni DNS-kiszolgálót. Megtudhatja, hogyan állítható be a saját DNS-kiszolgálót, tekintse meg a [névfeloldás saját DNS-kiszolgáló használatával](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
- Az ugyanazon régióban lévő virtuális társhálózaton belüli erőforrások kommunikálhatnak egymással azonos sávszélesség és késés, mintha az azonos virtuális hálózatba. Minden virtuális gép méretét azonban rendelkezik a saját maximális hálózati sávszélességet. A maximális hálózati sávszélességével kapcsolatban különböző virtuálisgép-méretekkel kapcsolatos további információkért lásd: [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuálisgép-méretek.
- Virtuális hálózat társviszonyba állíthatók, egy másik virtuális hálózathoz, és is egy másik virtuális hálózathoz csatlakozni egy Azure virtuális hálózati átjáróval. Virtuális hálózatok társviszony-létesítés és a egy átjárón keresztül csatlakoznak, amikor a virtuális hálózatok közötti forgalom a társviszony-létesítési konfigurációjának ahelyett, hogy az átjárón keresztül.
- Pont – hely VPN-ügyfelek kell újra le kell tölteni a virtuális hálózati társviszony sikeres beállítása után annak érdekében, hogy az új útvonalakat a rendszer letölti az ügyfél.
- Egy névleges díj vonatkozik a társhálózati viszonyt használó bejövő és kimenő forgalomra. További tájékoztatás a [díjszabási lapon](https://azure.microsoft.com/pricing/details/virtual-network) olvasható.

## <a name="permissions"></a>Engedélyek

A fiókok, akkor használható a virtuális hálózatok közötti társviszony hozzá kell rendelni a következő szerepkörök:

- [Hálózati közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor): A Resource Managerrel üzembe helyezett virtuális hálózathoz.
- [Klasszikus hálózati közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor): A klasszikus üzemi modellel üzembe helyezett virtuális hálózathoz.

Ha a fiókjához nincs hozzárendelve egy előző szerepköröket, akkor hozzá kell rendelni egy [egyéni szerepkör](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) rendelt a szükséges lépéseket a következő táblázatból:

| Műveletek                                                          | Name (Név) |
|---                                                              |---   |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write  | Szükséges egy társviszony-létesítés létrehozása A virtuális hálózatról virtuális hálózatra b virtuális hálózat egy virtuális hálózatot (Resource Manager) kell lennie          |
| Microsoft.Network/virtualNetworks/peer/action                   | A virtuális hálózathoz a B (Resource Manager) virtuális hálózati társviszony-létesítés szükséges                                                       |
| Microsoft.ClassicNetwork/virtualNetworks/peer/action                   | A virtuális hálózathoz a B (klasszikus) virtuális hálózati társviszony-létesítés szükséges                                                                |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/read   | Olvassa el a virtuális hálózatok közötti társviszony   |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/delete | Egy virtuális hálózati társviszony törlése |

## <a name="next-steps"></a>További lépések

- A virtuális hálózatok közötti társviszony az azonos vagy eltérő előfizetésekben lévő, azonos vagy eltérő üzembehelyezési modelleken keresztül létrehozott virtuális hálózatok között jön létre. Végezzen el egy oktatóanyagot a következő forgatókönyvek egyikéhez:

  |Azure üzembehelyezési modell             | Előfizetés  |
  |---------                          |---------|
  |Mindkét Resource Manager              |[Ugyanaz](tutorial-connect-virtual-networks-portal.md)|
  |                                   |[Különböző](create-peering-different-subscriptions.md)|
  |Egy Resource Manager, egy klasszikus  |[Ugyanaz](create-peering-different-deployment-models.md)|
  |                                   |[Különböző](create-peering-different-deployment-models-subscriptions.md)|

- További információ a [küllős hálózati topológiák](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json) létrehozásáról
- Hozzon létre egy virtuális hálózati társviszony segítségével [PowerShell](powershell-samples.md) vagy [Azure CLI-vel](cli-samples.md) parancsfájlokat, vagy az Azure-minta [Resource Manager-sablonok](template-samples.md)
- Hozzon létre, és a alkalmazni [az Azure policy](policy-samples.md) virtuális hálózatok
