---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: ccc2b574ea054a1b0ecf32a1e59691050fb66fcf
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/12/2019
ms.locfileid: "67179059"
---
## <a name="tagging-a-virtual-machine-through-templates"></a>Virtuális gép címkézése sablonok használatával
Először tekintsük át a címkézést sablonok használatával. [Ez a sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags) címkéket helyez el a következő erőforrásokon: Számítás (virtuális gép), tárterület (Storage-fiók) és hálózat (nyilvános IP-cím, Virtual Network és hálózati adapter). Ez a sablon egy Windows rendszerű virtuális gép esetében használható, de Linux rendszerű virtuális gépekhez is adaptálható.

Kattintson az **üzembe helyezés az Azure** -ban gombra a [sablon hivatkozásán](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags). Ekkor megnyílik a [Azure Portal](https://portal.azure.com/) , ahol üzembe helyezheti a sablont.

![Egyszerű üzembe helyezés címkékkel](./media/virtual-machines-common-tag/deploy-to-azure-tags.png)

Ez a sablon a következő címkéket tartalmazza: *Részleg*, *alkalmazás*és *létrehozta*. Ezeket a címkéket közvetlenül a sablonban is hozzáadhatja vagy szerkesztheti, ha különböző címkéket szeretne megadni.

![Azure-címkék sablonban](./media/virtual-machines-common-tag/azure-tags-in-a-template.png)

Amint láthatja, a címkék kulcs/érték párokként vannak definiálva, kettősponttal elválasztva (:). A címkéket a következő formátumban kell megadni:

        “tags”: {
            “Key1” : ”Value1”,
            “Key2” : “Value2”
        }

Mentse a sablonfájlt a Szerkesztés befejezése után az Ön által választott címkékkel.

Ezután a **Paraméterek szerkesztése** szakaszban kitöltheti a címkék értékeit.

![Címkék szerkesztése a Azure Portalban](./media/virtual-machines-common-tag/edit-tags-in-azure-portal.png)

Kattintson a **Létrehozás** gombra a sablon a címke értékeivel való telepítéséhez.

## <a name="tagging-through-the-portal"></a>Címkézés a portálon keresztül
Az erőforrások címkékkel való létrehozása után megtekintheti, hozzáadhatja és törölheti a címkéket a portálon.

A címkék ikon kiválasztásával megtekintheti a címkéket:

![Címkék ikon a Azure Portal](./media/virtual-machines-common-tag/azure-portal-tags-icon.png)

Adjon hozzá egy új címkét a portálon a saját kulcs/érték párok definiálásával, és mentse azt.

![Új címke hozzáadása a Azure Portal](./media/virtual-machines-common-tag/azure-portal-add-new-tag.png)

Az új címke ekkor megjelenik az erőforráshoz tartozó címkék listájában.

![Új címke mentve Azure Portal](./media/virtual-machines-common-tag/azure-portal-saved-new-tag.png)

