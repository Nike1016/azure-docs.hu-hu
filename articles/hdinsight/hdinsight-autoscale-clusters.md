---
title: Azure HDInsight-fürtök automatikus méretezése (előzetes verzió)
description: A fürtök automatikus méretezése a HDInsight automatikus méretezési funkciójával
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: hrasheed
ms.openlocfilehash: 333eecb11f0bd20c747bc44419fea26765f886c5
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69509110"
---
# <a name="automatically-scale-azure-hdinsight-clusters-preview"></a>Azure HDInsight-fürtök automatikus méretezése (előzetes verzió)

> [!Important]
> Az autoscale funkció csak a május 8. és 2019. után létrehozott Spark-, kaptár-és MapReduce-fürtökön működik. 

Az Azure HDInsight fürt automatikus méretezési funkciója automatikusan felfelé és lefelé méretezi a munkavégző csomópontok számát a fürtben. A fürtben lévő más típusú csomópontok jelenleg nem méretezhetők.  Új HDInsight-fürt létrehozása során beállítható a munkavégző csomópontok minimális és maximális száma. Az automatikus skálázás ezután figyeli az elemzési terhelés erőforrás-követelményeit, és a feldolgozói csomópontok számát felfelé vagy lefelé méretezi. Ehhez a szolgáltatáshoz nem számítunk fel további díjat.

## <a name="cluster-compatibility"></a>Fürt kompatibilitása

Az alábbi táblázat az autoscale szolgáltatással kompatibilis fürtök típusát és verzióját ismerteti.

| Version | Spark | Hive | LLAP | HBase | Kafka | Storm | ML |
|---|---|---|---|---|---|---|---|
| HDInsight 3,6 ESP nélkül | Igen | Igen | Nem | Nem | Nem | Nem | Nem |
| HDInsight 4,0 ESP nélkül | Igen | Igen | Nem | Nem | Nem | Nem | Nem |
| HDInsight 3,6, ESP-vel | Igen | Igen | Nem | Nem | Nem | Nem | Nem |
| HDInsight 3,6, ESP-vel | Igen | Igen | Nem | Nem | Nem | Nem | Nem |

## <a name="how-it-works"></a>Működés

Kiválaszthatja a HDInsight-fürt terheléselosztási vagy ütemezett skálázási lehetőségeit. A terhelésen alapuló skálázás megváltoztatja a fürtben lévő csomópontok számát a beállított tartományon belül, így biztosítva az optimális CPU-kihasználtságot, és csökkentheti a futtatási költségeket.

Az ütemezett skálázás a fürt csomópontjainak számát a megadott időpontokban érvényes feltételek alapján módosítja. Ezek a feltételek a fürtöt a kívánt számú csomópontra méretezhetik.

### <a name="metrics-monitoring"></a>Metrikák figyelése

Az autoscale folyamatosan figyeli a fürtöt, és a következő metrikákat gyűjti össze:

* **Összes függőben lévő CPU**: Az összes függőben lévő tároló végrehajtásának megkezdéséhez szükséges magok teljes száma.
* **Függőben lévő memória összesen**: Az összes függőben lévő tároló végrehajtásának megkezdéséhez szükséges teljes memória (MB).
* **Teljes szabad processzor**: Az aktív munkavégző csomópontokon fel nem használt magok összege.
* **Szabad memória összesen**: A nem használt memória (MB) összege az aktív munkavégző csomópontokon.
* **Felhasznált memória/csomópont**: A munkavégző csomópont terhelése. Egy munkavégző csomópontot, amelyen 10 GB memóriát használ, nagyobb terhelésnek számít, mint egy 2 GB-nyi memóriát használó feldolgozó.
* **Alkalmazás-főkiszolgálók száma csomópont szerint**: A munkavégző csomóponton futó Application Master (AM) tárolók száma. A két tárolót üzemeltető munkavégző csomópont sokkal fontosabbnak számít, mint a nulla AM tárolókat üzemeltető munkavégző csomópont.

A fenti mérőszámok 60 másodpercenként vannak ellenőrizve. Az autoskálázás a mérőszámok alapján vertikális felskálázást és leskálázást tesz lehetővé.

### <a name="load-based-cluster-scale-up"></a>Load-alapú fürt felskálázása

Ha a következő feltételek észlelhetők, az autoskálázás egy Felskálázási kérelmet fog kiadni:

* A függőben lévő CPU összesen több mint 3 percnél nagyobb az összes szabad PROCESSZORnál.
* A függőben lévő memória összesen több mint 3 percnél nagyobb a teljes szabad memóriánál.

A HDInsight szolgáltatás kiszámítja, hogy hány új feldolgozó csomópontra van szükség ahhoz, hogy megfeleljen a jelenlegi CPU-és memória-követelményeknek, majd kiadja a szükséges számú csomópont hozzáadására szolgáló Felskálázási kérést.

### <a name="load-based-cluster-scale-down"></a>Terheléselosztási fürt leskálázása

Ha a következő feltételek észlelhetők, az autoscale leskálázási kérést ad ki:

* A függőben lévő PROCESSZORok száma több mint 10 percnél kevesebb, mint az összes szabad CPU.
* A függőben lévő memória mennyisége kevesebb, mint 10 percnél nagyobb a teljes szabad memória.

A csomópontok száma és a jelenlegi CPU-és memória-követelmények alapján az autoskálázás egy adott számú csomópont eltávolítására irányuló kérést bocsát ki. A szolgáltatás azt is észleli, hogy mely csomópontok vannak kiválasztva az eltávolításra a jelenlegi feladatok végrehajtása alapján. A leskálázási művelet először leszereli a csomópontokat, majd eltávolítja őket a fürtből.

## <a name="get-started"></a>Bevezetés

### <a name="create-a-cluster-with-load-based-autoscaling"></a>Fürt létrehozása terheléselosztási alapú automatikus skálázással

Ha az automatikus skálázási funkciót terheléselosztásos skálázással szeretné engedélyezni, hajtsa végre a következő lépéseket a fürt normál létrehozási folyamatának részeként:

1. **Gyors létrehozás**helyett válassza **az egyéni (méret, beállítások, alkalmazások)** lehetőséget.
1. Az 5. **Egyéni** lépés (**fürt mérete**) területen jelölje be a munkavégző csomópontok autoskálázása jelölőnégyzetet.
1. Válassza az **automatikus** **skálázás típusa**beállításnál a betöltés lehetőséget.
1. Adja meg a kívánt értékeket a következő tulajdonságokhoz:  

    * **A munkavégző csomópontok kezdeti száma**.  
    * A munkavégző csomópontok **minimális** száma.  
    * A munkavégző csomópontok **maximális** száma.  

    ![Munkavégző csomópont terheléses automatikus méretezési beállításának engedélyezése](./media/hdinsight-autoscale-clusters/usingAutoscale.png)

A munkavégző csomópontok kezdeti számának a minimális és a maximális érték közé kell esnie. Ez az érték határozza meg a fürt kezdeti méretét a létrehozáskor. A munkavégző csomópontok minimális számának nullánál nagyobbnak kell lennie.

### <a name="create-a-cluster-with-schedule-based-autoscaling"></a>Fürt létrehozása Schedule-alapú automatikus skálázással

Ha az automatikus skálázási funkciót ütemezett méretezéssel szeretné engedélyezni, hajtsa végre a következő lépéseket a fürt normál létrehozási folyamatának részeként:

1. **Gyors létrehozás**helyett válassza **az egyéni (méret, beállítások, alkalmazások)** lehetőséget.
1. Az 5. **Egyéni** lépés (**fürt mérete**) területen jelölje be a munkavégző csomópontok autoskálázása jelölőnégyzetet.
1. Adja meg a **munkavégző csomópontok számát**, amely a fürt skálázásának korlátját vezérli.
1. Válassza az **ütemezett** beállítás lehetőséget az **autoskálázás típusa**területen.
1. Kattintson a **Konfigurálás** elemre az **automatikus skálázási konfiguráció** ablak megnyitásához.
1. Válassza ki az időzónát, majd kattintson a **+ feltétel hozzáadása** elemre.
1. Válassza ki a hét azon napjait, amelyeket az új feltételnek alkalmaznia kell.
1. Szerkessze a feltétel érvénybe léptetésének időpontját, valamint azt, hogy a fürt hány csomópontra legyen méretezhető.
1. Szükség esetén további feltételek is felvehetők.

    ![A munkavégző csomópont Schedule-alapú autoskálázási beállításának engedélyezése](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-schedule-creation.png)

A csomópontok számának 1 és a feltételek hozzáadása előtt megadott munkavégző csomópontok számának kell lennie.

### <a name="final-creation-steps"></a>Végső létrehozás lépései

A terheléselosztási és az ütemezett skálázás esetében válassza ki a virtuális gép típusát a munkavégző csomópontok számára a feldolgozó **csomópontjának mérete** és a **fő csomópont mérete**lehetőségre kattintva. Miután kiválasztotta a virtuálisgép-típust az egyes csomópont-típusokhoz, a teljes fürt becsült hatókörét láthatja. Állítsa be úgy a virtuális gépek típusait, hogy illeszkedjenek a költségkerethez.

![A munkavégző csomópont Schedule-alapú autoskálázási beállításának engedélyezése](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-node-size-selection.png)

Az előfizetéshez tartozik egy kapacitási kvóta az egyes régiókban. A fő csomópontok teljes száma és a munkavégző csomópontok maximális száma együttesen nem lépheti túl a kapacitás kvótáját. Ez a kvóta azonban egy enyhe korlát; bármikor létrehozhat egy támogatási jegyet, hogy könnyebben megnövelhető legyen.

> [!Note]  
> Ha túllépi a teljes felső korlátot, a következő hibaüzenet jelenik meg: "a maximális csomópont túllépte az elérhető magok számát ebben a régióban, válasszon egy másik régiót, vagy forduljon a támogatási szolgálathoz a kvóta növeléséhez."

További információ a HDInsight-fürtök létrehozásáról a Azure Portal használatával: [Linux-alapú fürtök létrehozása a HDInsight-ben a Azure Portal használatával](hdinsight-hadoop-create-linux-clusters-portal.md).  

### <a name="create-a-cluster-with-a-resource-manager-template"></a>Fürt létrehozása Resource Manager-sablonnal

#### <a name="load-based-autoscaling"></a>Load-alapú automatikus skálázás

HDInsight `autoscale` -fürtöt egy Azure Resource Manager-sablon terheléses automatikus skálázásával hozhat létre, ha hozzáad egy csomópontot a `computeProfile` `minInstanceCount`  >  `workernode` szakaszhoz a tulajdonságok és `maxInstanceCount` a következővel: az alábbi JSON-kódrészletben látható.

```json
{
  "name": "workernode",
  "targetInstanceCount": 4,
  "autoscale": {
      "capacity": {
          "minInstanceCount": 2,
          "maxInstanceCount": 10
      }
  },
  "hardwareProfile": {
      "vmSize": "Standard_D13_V2"
  },
  "osProfile": {
      "linuxOperatingSystemProfile": {
          "username": "[parameters('sshUserName')]",
          "password": "[parameters('sshPassword')]"
      }
  },
  "virtualNetworkProfile": null,
  "scriptActions": []
}
```

A fürtök Resource Manager-sablonokkal való létrehozásával kapcsolatos további információkért lásd: [Apache Hadoop-fürtök létrehozása a HDInsight-ben Resource Manager-sablonok használatával](hdinsight-hadoop-create-linux-clusters-arm-templates.md).  

#### <a name="schedule-based-autoscaling"></a>Ütemterv-alapú automatikus skálázás

HDInsight-fürtöt úgy hozhat létre `autoscale` , hogy a szakaszhoz hozzáad egy csomópontot a `computeProfile`  >  `workernode` Azure Resource Manager-sablonhoz. A `autoscale` csomópont `timezone` tartalmazegy-`schedule` t, amely leírja, hogy mikor kerül sor a módosításra. `recurrence`

```json
{
  "autoscale": {
    "recurrence": {
      "timeZone": "Pacific Standard Time",
      "schedule": [
        {
          "days": [
            "Monday",
            "Tuesday",
            "Wednesday",
            "Thursday",
            "Friday"
          ],
          "timeAndCapacity": {
            "time": "11:00",
            "minInstanceCount": 10,
            "maxInstanceCount": 10
          }
        },
      ]
    }
  },
  "name": "workernode",
  "targetInstanceCount": 4,
}
```

### <a name="enable-and-disable-autoscale-for-a-running-cluster"></a>Az autoskálázás engedélyezése és letiltása egy futó fürtön

#### <a name="using-the-azure-portal"></a>Az Azure Portal használata
Ha egy futó fürtön engedélyezni szeretné az autoskálázást, a **Beállítások**területen válassza a **fürt méretét** . Ezután kattintson az **autoskálázás engedélyezése**lehetőségre. Válassza ki a kívánt automatikus méretezési típust, és adja meg a terhelés vagy az ütemterv szerinti skálázás beállításait. Végül kattintson a **Mentés**gombra.

![A munkavégző csomópont Schedule-alapú autoskálázási beállításának engedélyezése](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-enable-running-cluster.png)

#### <a name="using-the-rest-api"></a>A REST API használata
Ha egy futó fürtön a REST API használatával szeretné engedélyezni vagy letiltani az autoskálázást, tegye az alábbi kódrészletben látható POST-kérést az autoscale végpontra:

```
https://management.azure.com/subscriptions/{subscription Id}/resourceGroups/{resourceGroup Name}/providers/Microsoft.HDInsight/clusters/{CLUSTERNAME}/roles/workernode/autoscale?api-version=2018-06-01-preview
```

Használja a kérelem hasznos adatainak megfelelő paramétereket. Az alábbi JSON-adattartalom használatával engedélyezhető az autoskálázás. Az autoscale `{autoscale: null}` letiltásához használja a hasznos adatokat.

```json
{ autoscale: { capacity: { minInstanceCount: 1, maxInstanceCount: 2 } } }
```

Tekintse meg az előző szakaszt a betöltési [alapú automatikus skálázás engedélyezéséhez](#load-based-autoscaling) az összes hasznos adat paraméterének teljes leírásához.

## <a name="best-practices"></a>Ajánlott eljárások

### <a name="choosing-load-based-or-schedule-based-scaling"></a>A Load-based vagy az Schedule-alapú skálázás kiválasztása

A következő tényezőket kell figyelembe vennie, mielőtt döntést hozna a választott üzemmódról:

* Betöltési eltérés: a fürt terhelése adott időpontokban, meghatározott napokon követi a konzisztens mintát. Ha nem, akkor a betöltés alapú ütemezés jobb megoldás.
* SLA-követelmények: Az automatikus skálázás a prediktív végrehajtás helyett reaktív. Elegendő késés lesz a terhelés növekedésének megkezdése és a fürtnek a célként megadott méretnél való megadása között? Ha szigorú SLA-követelmények vannak meghatározva, és a terhelés egy rögzített ismert minta, az "ütemezés-alapú" jobb megoldás.

### <a name="consider-the-latency-of-scale-up-or-scale-down-operations"></a>Vegye figyelembe a vertikális Felskálázási vagy leskálázási műveletek késését

A skálázási művelet befejezéséhez 10 – 20 percet is igénybe vehet. Testreszabott ütemterv beállításakor tervezze meg ezt a késleltetést. Ha például a fürt méretének 20-at kell megadnia a 9:00-es verziónál, állítsa az ütemezett triggert egy korábbi időpontra, például a 8:30-re, hogy a skálázási művelet a 9:00-as értékkel fejeződött be.

### <a name="preparation-for-scaling-down"></a>Felkészülés a méretezésre

A fürt skálázási folyamata során az automatikus skálázás leszereli a csomópontokat a célként megadott méret kielégítése érdekében. Ha ezeken a csomópontokon futó feladatok futnak, az automatikusan megvárja, amíg a feladatok befejeződik. Mivel az egyes munkavégző csomópontok is a HDFS szerepkört is kiszolgálják, a rendszer a többi csomópontra helyezi át a temp-adatait. Ezért győződjön meg arról, hogy a többi csomóponton elegendő lemezterület áll rendelkezésre az összes Temp-érték üzemeltetéséhez. 

A futó feladatok továbbra is futnak és befejeződik. A függőben lévő feladatok a szokásosnál kevesebb munkavégző csomóponttal lesznek ütemezve.

## <a name="monitoring"></a>Figyelés

### <a name="cluster-status"></a>Fürt állapota

A Azure Portalban felsorolt fürt állapota segíthet az autoskálázási tevékenységek figyelésében.

![Munkavégző csomópont terheléses automatikus méretezési beállításának engedélyezése](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-cluster-status.png)

Az alábbi listában az összes olyan fürt állapotüzenetek látható, amelyet látni fog.

| Fürt állapota | Magyarázat |
|---|---|
| Fut | A fürt rendesen működik. Az összes korábbi autoskálázási tevékenység sikeresen befejeződött. |
| Frissítés  | A fürt automatikus skálázási konfigurációjának frissítése folyamatban van.  |
| HDInsight-konfiguráció  | Egy fürt vertikális fel-vagy leskálázási művelete folyamatban van.  |
| Frissítési hiba  | A HDInsight problémákba ütközött az automatikus skálázási konfiguráció frissítése során. Az ügyfelek dönthetnek úgy, hogy megpróbálják megismételni a frissítést vagy letiltani az autoskálázást.  |
| Hiba  | Probléma van a fürttel, és nem használható. Törölje a fürtöt, és hozzon létre egy újat.  |

A fürtben lévő csomópontok aktuális számának megtekintéséhez nyissa meg a fürt áttekintő lapján a fürt **mérete** diagramot, vagy kattintson a **fürt mérete** lehetőségre a **Beállítások**területen.

### <a name="operation-history"></a>Műveleti előzmények

A fürt metrikáinak részeként megtekintheti a fürt vertikális felskálázásának és leskálázásának előzményeit is. Az összes skálázási műveletet az elmúlt nap, a hét vagy más idő alatt is listázhatja.

A **figyelés**területen válassza a **metrikák** lehetőséget. Ezután kattintson a **metrika hozzáadása** és az **aktív feldolgozók száma** lehetőségre a **metrika** legördülő listából. Kattintson a jobb felső sarokban lévő gombra az időtartomány módosításához.

![A munkavégző csomópont Schedule-alapú autoskálázási beállításának engedélyezése](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-chart-metric.png)


## <a name="next-steps"></a>További lépések

* Olvassa el a fürtök manuális méretezésével kapcsolatos ajánlott eljárásokat az [ajánlott eljárások méretezése](hdinsight-scaling-best-practices.md) című cikkből.
