---
title: A vállalati biztonság áttekintése az Azure HDInsight
description: Ismerje meg a vállalati biztonság Azure HDInsight való biztosításának különböző módszereit.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: overview
ms.date: 07/22/2019
ms.openlocfilehash: 4619545ab1fed5f55504e80eede0d1cf240eea87
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2019
ms.locfileid: "68488692"
---
# <a name="overview-of-enterprise-security-in-azure-hdinsight"></a>A vállalati biztonság áttekintése az Azure HDInsight

Az Azure HDInsight számos módszert kínál a nagyvállalati biztonsági igények kielégítésére. A megoldások többsége alapértelmezés szerint nincs aktiválva. Ez a rugalmasság lehetővé teszi, hogy kiválassza a legfontosabb biztonsági funkciókat, és segít elkerülni a nem kívánt szolgáltatások kifizetését. Ez azt is jelenti, hogy az Ön felelőssége annak biztosítása, hogy a megfelelő megoldások engedélyezve legyenek a telepítéshez és a környezethez.

Ez a cikk a biztonsági megoldásokat tekinti át a biztonsági megoldások négy hagyományos biztonsági pillér soraiban való felosztásával: biztonsági, hitelesítési, engedélyezési és titkosítási feltételeit.

A cikk emellett bemutatja az **Azure HDInsight Enterprise Security Package (ESP)** , amely Active Directory-alapú hitelesítést, többfelhasználós támogatást és szerepköralapú hozzáférés-vezérlést biztosít a HDInsight-fürtökhöz.

## <a name="enterprise-security-pillars"></a>Vállalati biztonsági oszlopok

A nagyvállalati biztonság egyik módja a biztonsági megoldások felosztása négy fő csoportra a vezérlés típusa alapján. Ezeket a csoportokat biztonsági pilléreknek is nevezik, és a következők: peremhálózat-biztonság, hitelesítés, engedélyezés és titkosítás.

### <a name="perimeter-security"></a>Szegélyhálózati biztonság

A HDInsight [virtuális hálózatokon](../hdinsight-plan-virtual-network-deployment.md)keresztül érhető el. A vállalati rendszergazda létrehozhat egy fürtöt egy virtuális hálózaton (VNET) belül, és hálózati biztonsági csoportok (NSG-EK) használatával korlátozhatja a virtuális hálózathoz való hozzáférést. Csak az engedélyezett IP-címek lesznek képesek kommunikálni a HDInsight-fürttel a bejövő NSG-szabályokban. Ez a konfiguráció peremhálózati biztonságot nyújt.

A VNET telepített összes fürt egy privát végponttal is rendelkezik, amely a VNET belüli magánhálózati IP-címekre oldja fel a fürt átjáróinak magánhálózati HTTP-hozzáférését.

### <a name="authentication"></a>Authentication

A HDInsight [Enterprise Security Package](apache-domain-joined-architecture.md) Active Directory-alapú hitelesítést, többfelhasználós támogatást és szerepköralapú hozzáférés-vezérlést biztosít. A Active Directory integrációja [Azure Active Directory Domain Services](../../active-directory-domain-services/overview.md)használatával érhető el. Ezekkel a képességekkel a felügyelt Active Directory tartományhoz csatlakoztatott HDInsight-fürtöt hozhat létre. Ezután konfigurálhatja azon vállalat alkalmazottainak listáját, akik hitelesíthetők és be tudnak jelentkezni a fürtbe.

Ezzel a beállítással a vállalati alkalmazottak tartományi hitelesítő adataikkal jelentkezhetnek be a fürt csomópontjaira. A tartományi hitelesítő adataikat is használhatják a más jóváhagyott végpontok, például az Apache Ambari views, az ODBC, a JDBC, a PowerShell és a REST API-k hitelesítésére a fürttel való kommunikációhoz. 

### <a name="authorization"></a>Authorization

A legtöbb vállalat által követett ajánlott eljárás biztosítja, hogy nem minden alkalmazott férhet hozzá az összes vállalati erőforráshoz. Hasonlóképpen a rendszergazda szerepköralapú hozzáférés-vezérlési házirendeket is meghatározhat a fürterőforrások számára. Ez csak az ESP-fürtökben érhető el.

A Hadoop-rendszergazda szerepköralapú hozzáférés-vezérlést (RBAC) konfigurálhat az Apache [Ranger, a](apache-domain-joined-run-hive.md) [HBase](apache-domain-joined-run-hbase.md) és a [Kafka](apache-domain-joined-run-kafka.md) védelméhez. A RBAC-házirendek konfigurálása lehetővé teszi, hogy a szervezet egyik szerepkörével társítsa az engedélyeket. Ez az absztrakciós réteg megkönnyíti annak biztosítását, hogy a felhasználók csak a munkahelyi feladataik elvégzéséhez szükséges engedélyekkel rendelkezzenek. A Ranger lehetővé teszi az alkalmazottak és a hozzáférés-vezérlési házirendek által végzett módosítások naplózását is.

Például, a rendszergazda konfigurálhatja az [Apache Ranger](https://ranger.apache.org/) keretrendszert, hogy beállítsa a Hive hozzáférés-vezérlés házirendjét. Ez a funkció biztosítja a sor szintű és az oszlop szintű szűrést (az adatmaszkolást), és szűri a bizalmas adatokat a jogosulatlan felhasználóktól.

### <a name="auditing"></a>Naplózás

Az erőforrások jogosulatlan vagy véletlen hozzáférésének nyomon követéséhez szükség van a fürterőforrások összes hozzáférésének naplózására, valamint az adatmennyiségre. Ez olyan fontos, mint a HDInsight-fürt erőforrásainak jogosulatlan felhasználóktól való védelme és az adatok védelme.

A rendszergazda megtekintheti és jelentheti az összes hozzáférést a HDInsight-fürt erőforrásaihoz és az adatforrásokhoz. A rendszergazda az Apache Ranger által támogatott végpontokban létrehozott hozzáférés-vezérlési szabályzatok összes módosítását is megtekintheti és bejelentheti. 

Az Apache Ranger-és Ambari-naplók, valamint az SSH-hozzáférési naplók eléréséhez [engedélyezze Azure monitor](../hdinsight-hadoop-oms-log-analytics-tutorial.md) és tekintse meg a kapcsolódó naplózási rekordokat.

### <a name="encryption"></a>Encryption

Az adatok védelme fontos a szervezeti biztonsági és megfelelőségi követelmények teljesítéséhez. A jogosulatlan alkalmazottaktól származó adatokhoz való hozzáférés korlátozásával együtt érdemes titkosítani.

A HDInsight-fürtök, az Azure Blob Storage és a Azure Data Lake Storage Gen1/Gen2 mindkét adattárolója támogatja az inaktív [adatok](../../storage/common/storage-service-encryption.md) transzparens kiszolgálóoldali titkosítását. A biztonságos HDInsight-fürtök zökkenőmentesen együttműködnek ezzel a képességgel az inaktív adatok kiszolgálóoldali titkosításával.

## <a name="shared-responsibility-model"></a>Megosztott felelősségi modell

Az alábbi ábra összefoglalja a főbb rendszerbiztonsági területeket és az egyes eszközökön elérhető biztonsági megoldásokat. Emellett kiemeli, hogy mely biztonsági területek felelnek meg az ügyfélnek, és hogy mely területek felelősek a szolgáltató HDInsight.

![HDInsight – megosztott felelősségi diagram](./media/hdinsight-security-overview/hdinsight-shared-responsibility.png)

Az alábbi táblázat a biztonsági megoldások egyes típusaihoz kapcsolódó erőforrásokra mutató hivatkozásokat tartalmaz.

| Biztonsági térség | Elérhető megoldások | Felelős fél |
|---|---|---|
| Adathozzáférés biztonsága | [Hozzáférés-vezérlési listák](../../storage/blobs/data-lake-storage-access-control.md) konfigurálása a Azure Data Lake Storage Gen1 és a Gen2 hozzáférés-vezérlési listáihoz  | Ügyfél |
|  | Engedélyezze a ["biztonságos átvitel szükséges"](../../storage/common/storage-require-secure-transfer.md) tulajdonságot a Storage-fiókokon. | Ügyfél |
|  | [Azure Storage-tűzfalak](../../storage/common/storage-network-security.md) és virtuális hálózatok konfigurálása | Ügyfél |
|  | Győződjön meg arról, hogy a [TLS-titkosítás](../../storage/common/storage-security-tls.md) engedélyezve van az átvitt adatforgalomban. | Ügyfél |
|  | [Ügyfél által felügyelt kulcsok](../../storage/common/storage-encryption-keys-portal.md) konfigurálása az Azure Storage encryption szolgáltatáshoz | Ügyfél |
| Alkalmazás-és middleware-biztonság | Integráció a HRE-DS szolgáltatással és a [hitelesítés konfigurálása](apache-domain-joined-configure-using-azure-adds.md) | Ügyfél |
|  | [Apache Ranger-engedélyezési](apache-domain-joined-run-hive.md) házirendek konfigurálása | Ügyfél |
|  | [Azure monitor naplók](../hdinsight-hadoop-oms-log-analytics-tutorial.md) használata | Ügyfél |
| Operációs rendszer biztonsága | Fürtök létrehozása a legújabb biztonságos alapképpel | Ügyfél |
|  | Az [operációs rendszer javításának](../hdinsight-os-patching.md) rendszeres időközönkénti ellenőrzése | Ügyfél |
| Hálózati biztonság | [Virtuális hálózat](../hdinsight-plan-virtual-network-deployment.md) konfigurálása |
|  | A [bejövő hálózati biztonsági csoport (NSG) szabályainak](../hdinsight-plan-virtual-network-deployment.md#networktraffic) konfigurálása | Ügyfél |
|  | A [kimenő forgalom korlátozásának](../hdinsight-restrict-outbound-traffic.md) konfigurálása a tűzfallal (előzetes verzió) | Ügyfél |
| Virtualizált infrastruktúra | – | HDInsight (felhőalapú szolgáltató) |
| Fizikai infrastruktúra biztonsága | – | HDInsight (felhőalapú szolgáltató) |

## <a name="next-steps"></a>További lépések

* [Az ESP-vel rendelkező HDInsight-fürtök tervezése](apache-domain-joined-architecture.md)
* [HDInsight-fürtök beállítása az ESP-vel](apache-domain-joined-configure.md)
* [HDInsight-fürtök az ESP-vel való kezelése](apache-domain-joined-manage.md)
