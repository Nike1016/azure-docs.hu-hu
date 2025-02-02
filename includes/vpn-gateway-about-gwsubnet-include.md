---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 0334f9fd2d749b88580ff3857d705de2ae961902
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178973"
---
A virtuális hálózati átjáró az átjáróalhálózat elnevezésű alhálózatot használja. Az átjáróalhálózat a virtuális hálózat azon IP-címtartományának része, amelyet a virtuális hálózat konfigurálásakor ad meg. Tartalmazza a virtuális hálózati átjáró-erőforrások és -szolgáltatások által használt IP-címeket. Az alhálózatnak a GatewaySubnet névvel kell rendelkeznie ahhoz, hogy az Azure üzembe helyezze az átjáró-erőforrásokat. Nem helyezheti üzembe az átjáró-erőforrásokat másik alhálózatban. Ha nem rendelkezik „GatewaySubnet” nevű alhálózattal, a létrehozott VPN-átjáró nem fog működni.

Az átjáróalhálózat létrehozásakor meg kell adnia, hogy hány IP-címet tartalmaz az alhálózat. A szükséges IP-címek száma a létrehozni kívánt VPN-átjárókonfigurációtól függ. Egyes konfigurációknak a többinél nagyobb számú IP-címre van szükségük. /27-es vagy /28-as átjáróalhálózat létrehozását javasoljuk.

Ha azt a hibaüzenetet kapja, hogy az alhálózat átfedésben van a címterekkel, vagy az alhálózat nincs a virtuális hálózat címterén belül, ellenőrizze a VNet címtartományát. Előfordulhat, hogy nincs elég IP-cím a virtuális hálózathoz létrehozott címtartományban. Ha például az alapértelmezett alhálózat magában foglalja a teljes címtartományt, nem marad elég IP-cím további alhálózatok létrehozására. Módosíthatja a meglévő címtérben található alhálózatokat, hogy IP-címeket szabadítson fel, vagy megadhat egy további címtartományt, és létrehozhatja ott az átjáróalhálózatot.