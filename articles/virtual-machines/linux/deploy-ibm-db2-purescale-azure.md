---
title: Az IBM DB2-pureScale üzembe helyezése az Azure-ban
description: Megtudhatja, hogyan helyezhet üzembe egy közelmúltbeli példát a vállalatnak az IBM DB2-környezetből, amely a z/OS-ben fut az Azure-ban az IBM DB2-pureScale.
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/09/2018
ms.author: edprice
ms.openlocfilehash: 68fde09b1ee5f18aa784793cc19e9f547b19ed43
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/15/2019
ms.locfileid: "67871882"
---
# <a name="deploy-ibm-db2-purescale-on-azure"></a>Az IBM DB2-pureScale üzembe helyezése az Azure-ban

Ez a cikk azt ismerteti, hogyan lehet üzembe helyezni egy olyan [példa architektúrát](ibm-db2-purescale-azure.md) , amelyet a vállalati ügyfél nemrégiben használt az IBM DB2-környezetből a z/os-ben, az Azure-on futó IBM DB2-pureScale.

Az áttelepítéshez használt lépések követéséhez tekintse meg a [DB2onAzure](https://aka.ms/db2onazure) adattár telepítési parancsfájljait a githubon. Ezek a parancsfájlok egy tipikus, közepes méretű online tranzakció-feldolgozási (OLTP) munkaterhelés architektúráján alapulnak.

## <a name="get-started"></a>Bevezetés

Az architektúra üzembe helyezéséhez töltse le és futtassa a [DB2onAzure](https://aka.ms/db2onazure) -tárházban található Deploy.sh-parancsfájlt a githubon.

A tárház parancsfájlokat is tartalmaz a Grafana-irányítópult beállításához. Az irányítópult segítségével lekérdezheti a Prometheus-t, a nyílt forráskódú figyelési és riasztási rendszer részét képező DB2-t.

> [!NOTE]
> Az ügyfélen található deploy.sh-parancsfájl titkos SSH-kulcsokat hoz létre, és a központi telepítési sablonhoz HTTPS-kapcsolaton keresztül továbbítja azokat. A nagyobb biztonság érdekében javasoljuk, hogy az [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) használatával tárolja a titkokat, a kulcsokat és a jelszavakat.

## <a name="how-the-deployment-script-works"></a>Az üzembe helyezési parancsfájl működése

A deploy.sh szkript létrehozza és konfigurálja az architektúrához tartozó Azure-erőforrásokat. A parancsfájl kéri a célként megadott környezetben használt Azure-előfizetést és virtuális gépeket, majd a következő műveleteket hajtja végre:

-   Az Azure-beli erőforráscsoport, virtuális hálózat és alhálózatok beállítása a telepítéshez

-   A hálózati biztonsági csoportok és az SSH beállítása a környezethez

-   Hálózati adapterek beállítása a GlusterFS és a DB2 pureScale virtuális gépeken

-   Létrehozza a GlusterFS Storage-beli virtuális gépeket

-   Létrehozza a Jumpbox virtuális gépet.

-   Létrehozza a DB2 pureScale virtuális gépeket

-   Létrehoz egy olyan tanúsító virtuális gépet, amely DB2 pureScale kopog

-   Létrehoz egy Windows rendszerű virtuális gépet, amelyet tesztelésre használ, de nem telepít semmit

Ezt követően az üzembe helyezési parancsfájlok egy iSCSI virtuális tárolóhely hálózatot (vSAN) állítanak be a megosztott tárolóhoz az Azure-ban. Ebben a példában az iSCSI csatlakozik a GlusterFS. Ez a megoldás lehetővé teszi az iSCSI-tárolók egyetlen Windows-csomópontként történő telepítését is. az iSCSI egy megosztott blokk tárolási felületet biztosít a TCP/IP protokollon keresztül, amely lehetővé teszi, hogy a DB2 pureScale telepítési eljárása egy eszköz felületét használja a megosztott tárolóhoz való kapcsolódáshoz. A GlusterFS alapjaival kapcsolatban tekintse meg az [architektúrát: A kötetek](https://docs.gluster.org/en/latest/Quick-Start-Guide/Architecture/) típusai témakör a Gluster docs-ban.

Az üzembe helyezési parancsfájlok az alábbi általános lépéseket futtatják:

1.  A GlusterFS használatával hozzon létre egy megosztott tárolási fürtöt az Azure-ban. Ez a lépés legalább két Linux-csomópontot foglal magában. A telepítés részleteiért lásd: [Red Hat Gluster Storage beállítása Microsoft Azureban](https://access.redhat.com/documentation/en-us/red_hat_gluster_storage/3.1/html/deployment_guide_for_public_cloud/chap-documentation-deployment_guide_for_public_cloud-azure-setting_up_rhgs_azure) a Red Hat Gluster dokumentációjában.

2.  Hozzon létre egy iSCSI Direct-felületet a cél linuxos kiszolgálókon a GlusterFS. A telepítés részleteiért lásd: [iSCSI-GlusterFS](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/) a GlusterFS felügyeleti útmutatóban.

3.  Állítsa be az iSCSI-kezdeményezőt a Linux rendszerű virtuális gépeken. A kezdeményező iSCSI-tároló használatával fogja elérni a GlusterFS-fürtöt. A telepítés részleteiért lásd: [iSCSI-tároló és kezdeményező konfigurálása Linux rendszeren](https://www.rootusers.com/how-to-configure-an-iscsi-target-and-initiator-in-linux/) a RootUsers dokumentációjában.

4.  Telepítse a GlusterFS-t az iSCSI-felület tárolási rétegéhez.

Miután a parancsfájlok létrehozták az iSCSI-eszközt, az utolsó lépés a DB2-pureScale telepítése. A DB2 pureScale-telepítés részeként az [IBM Spectrum](https://www.ibm.com/support/knowledgecenter/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0057167.html) (korábbi nevén GPFS) a GlusterFS-fürtön van lefordítva és telepítve. Ez a fürtözött fájlrendszer lehetővé teszi a DB2-pureScale számára az adatmegosztást a DB2 pureScale motort futtató virtuális gépek között. További információ: [IBM Spectrum Scale](https://www.ibm.com/support/knowledgecenter/en/STXKQY_4.2.0/ibmspectrumscale42_welcome.html) dokumentáció az IBM webhelyén.

## <a name="db2-purescale-response-file"></a>DB2 pureScale-válaszfájl

A GitHub-tárház tartalmazza a DB2server. RSP, a Response (. RSP) fájlt, amely lehetővé teszi, hogy automatizált parancsfájlt állítson elő a DB2 pureScale telepítéséhez. A következő táblázat a válaszfájl által a telepítőhöz használt DB2 pureScale-beállításokat sorolja fel. A válaszfájlt igény szerint testreszabhatja a környezetében.

> [!NOTE]
> A DB2server. RSP egy minta válaszfájlt tartalmaz a GitHubon található [DB2onAzure](https://aka.ms/db2onazure) -tárházban. Ha ezt a fájlt használja, szerkesztenie kell, mielőtt működni fog a környezetben.

| Képernyő neve               | Mező                                        | Érték                                                                                                 |
|---------------------------|----------------------------------------------|-------------------------------------------------------------------------------------------------------|
| Üdvözlőképernyő                   |                                              | Új telepítés                                                                                           |
| Termék kiválasztása          |                                              | A DB2 verziója 11.1.3.3. Kiszolgálói kiadások DB2 pureScale                                              |
| Konfiguráció             | Címtár                                    | /data1/opt/ibm/db2/V11.1                                                                              |
|                           | A telepítés típusának kiválasztása                 | Tipikus                                                                                               |
|                           | Elfogadom az IBM használati feltételeit                     | Bejelölve                                                                                               |
| Példány tulajdonosa            | Meglévő felhasználó, példány, Felhasználónév        | DB2sdin1                                                                                              |
| Bekerített felhasználó               | Meglévő felhasználó, felhasználó neve                     | DB2sdfe1                                                                                              |
| Fürt fájlrendszere       | Megosztott lemezpartíció eszközének elérési útja            | /dev/dm-2                                                                                             |
|                           | Csatlakoztatási pont                                  | /DB2sd\_1804a                                                                                         |
|                           | Megosztott lemez az adattároláshoz                         | /dev/dm-1                                                                                             |
|                           | Csatlakoztatási pont (adatcsatorna)                           | /DB2fs/datafs1                                                                                        |
|                           | Naplóhoz tartozó megosztott lemez                          | /dev/dm-0                                                                                             |
|                           | Csatlakoztatási pont (napló)                            | /DB2fs/logfs1                                                                                         |
|                           | DB2-fürtszolgáltatások tiebreaker. Eszköz elérési útja | /dev/dm-3                                                                                             |
| Gazdagépek listája                 | D1 [eth1], D2 [eth1], cf1 [eth1], CF2 [eth1] |                                                                                                       |
|                           | Elsődleges CF                         | cf1                                                                                                   |
|                           | Előnyben részesített másodlagos CF                       | cf2                                                                                                   |
| Válaszfájl és összefoglalás | első lehetőség                                 | Telepítse a DB2 Server Editiont az IBM DB2 pureScale szolgáltatással, és mentse a beállításokat egy válaszfájlban |
|                           | Válaszfájl neve                           | /root/DB2server.rsp                                                                                   |

### <a name="notes-about-this-deployment"></a>Megjegyzések az üzemelő példányról

- A/dev-dm0, a/dev-DM1, a/dev-dm2 és a/dev-dm3 értékeit a rendszer újraindítása után módosíthatja a virtuális gépen, ahol a telepítés történik (D0 az automatizált parancsfájlban). A megfelelő értékek megkereséséhez adja ki a következő parancsot a válaszfájl azon a kiszolgálón való befejezése előtt, amelyen a telepítő futni fog:

   ```
   [root\@d0 rhel]\# ls -als /dev/mapper
   total 0
   0 drwxr-xr-x 2 root root 140 May 30 11:07 .
   0 drwxr-xr-x 19 root root 4060 May 30 11:31 ..
   0 crw------- 1 root root 10, 236 May 30 11:04 control
   0 lrwxrwxrwx 1 root root 7 May 30 11:07 db2data1 -\> ../dm-1
   0 lrwxrwxrwx 1 root root 7 May 30 11:07 db2log1 -\> ../dm-0
   0 lrwxrwxrwx 1 root root 7 May 30 11:26 db2shared -\> ../dm-2
   0 lrwxrwxrwx 1 root root 7 May 30 11:08 db2tieb -\> ../dm-3
   ```

- A telepítési parancsfájlok aliasokat használnak az iSCSI-lemezekhez, hogy a tényleges nevek könnyen megtalálhatók legyenek.

- Ha a d0 futtatja a telepítési parancsfájlt, **a\* /dev/DM-** -értékek a D1, a cf0 és a cf1 értéktől eltérőek lehetnek. Az értékek különbsége nem befolyásolja a DB2 pureScale telepítését.

## <a name="troubleshooting-and-known-issues"></a>Hibaelhárítás és ismert problémák

A GitHub-tárház tartalmaz egy tudásbázist, amelyet a szerzők tartanak fenn. Felsorolja az esetlegesen felmerülő esetleges problémákat és a kipróbálható megoldásokat. Az ismert problémák például a következő esetekben fordulnak elő:

-   Az átjáró IP-címét próbálja elérni.

-   Általános nyilvános licenc (GPL) fordítását végzi.

-   A gazdagépek közötti biztonsági kézfogás meghiúsul.

-   A DB2-telepítő egy meglévő fájlrendszert észlel.

-   Manuálisan telepíti az IBM Spectrum-méretezést.

-   A DB2-pureScale telepítésekor már létrejött az IBM Spectrum skálázása.

-   A DB2-pureScale és az IBM Spectrum-méretezést távolítja el.

További információ ezekről és az egyéb ismert problémákról: kb.md-fájl a [DB2onAzure](https://aka.ms/DB2onAzure) -tárházban.

## <a name="next-steps"></a>További lépések

-   [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)

-   [Szükséges felhasználók létrehozása a DB2 pureScale szolgáltatás telepítéséhez](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)

-   [DB2icrt – példány létrehozása parancs](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)

-   [DB2 pureScale-fürtök adatmegoldása](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)

-   [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

-   [Platform modernizációs Szövetsége: IBM DB2 az Azure-ban](https://www.platformmodernization.org/pages/ibmdb2azure.aspx)

-   [Az Azure Virtual adatközpontjának átemelési és átváltási útmutatója](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
