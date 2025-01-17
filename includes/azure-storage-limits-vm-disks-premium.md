---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: rogarana
ms.openlocfilehash: e7e57c6a821731874dcb1d99a3133b6ede1da26e
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67179108"
---
**Prémium szintű nem felügyelt virtuálisgép-lemezek: Fiók korlátok**

| Resource | Alapértelmezett korlát |
| --- | --- |
| Fiókonkénti teljes lemezkapacitás |35 TB |
| Fiókonkénti teljes pillanatkép-kapacitás |10 TB |
| Maximális sávszélesség (bejövő + kimenő forgalom) fiókonként<sup>1</sup> |<=50 Gbps |

<sup>1</sup>*bejövő* tárfiókhoz küldött kérések az összes adat hivatkozik. *Kimenő forgalom* minden adat hivatkozik, amely egy storage-fiók érkező válaszok.

**Prémium szintű nem felügyelt virtuálisgép-lemezek: Lemezenkénti korlátok**

| Prémium szintű tárolólemezek típusai | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- |
| Lemezméret |128 GiB |512 GiB |(1 TB-os) 1 024 GB |2048 giB (2 TB)|4095 giB (4 TB-ig)|
| Lemezenkénti maximális iops-érték |500 |2,300 |5000 |7500 |7,500 |
| Lemezenkénti maximális átviteli sebesség |100 MB/mp | 150 MB/mp |200 MB/mp |250 MB/mp |250 MB/mp |
| Lemezek tárfiókonkénti maximális száma |280 |70 |35 | 17 | 8 |

**Prémium szintű nem felügyelt virtuálisgép-lemezek: És a virtuális gépenkénti korlátok**

| Resource | Alapértelmezett korlát |
| --- | --- |
| Virtuális gépenkénti maximális IOPS |80 000 IOPS GS5 virtuális géppel |
| Virtuális gépenkénti maximális átviteli sebesség |2000 MB/s GS5 virtuális géppel |

