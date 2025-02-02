---
title: Engedélyezze az SR-IOV - Azure-beli virtuális gépek InifinBand |} A Microsoft Docs
description: Ismerje meg, hogyan engedélyezze az SR-IOV InfiniBand.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/15/2019
ms.author: amverma
ms.openlocfilehash: 2e28627359f339a3bf818a15d6a5c8e456fb554a
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797525"
---
# <a name="enable-infiniband-with-sr-iov"></a>Engedélyezze az SR-IOV InfiniBand

A legegyszerűbb és az ajánlott módszer az egyéni Virtuálisgép-rendszerképet konfigurálhatja az InfiniBand (IB) a InfiniBandDriverLinux vagy InfiniBandDriverWindows VM-bővítmény hozzáadása az üzemelő példány.
Ezek a Virtuálisgép-bővítmények használata [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc#rdma-capable-instances) és [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances)

Manuálisan konfigurálnia a InfiniBand az SR-IOV engedélyezve van a virtuális gépek (jelenleg HB és a hibrid kapcsolat sorozat), kövesse az alábbi lépéseket. Ezeket a lépéseket csak az RHEL/CentOS vannak. Az Ubuntu (16.04 és 18.04), és a SLES (12 SP4 és 15) a beépített illesztőprogramokat megfelelően működjön.

## <a name="manually-install-ofed"></a>Telepítse manuálisan a OFED

Telepítse a legújabb MLNX_OFED illesztőprogramokat ConnectX-5. a [Mellanox](https://www.mellanox.com/page/products_dyn?product_family=26).

Az RHEL/CentOS (az alábbi példában a 7.6):

```bash
sudo yum install -y kernel-devel python-devel
sudo yum install -y redhat-rpm-config rpm-build gcc-gfortran gcc-c++
sudo yum install -y gtk2 atk cairo tcl tk createrepo
wget --retry-connrefused --tries=3 --waitretry=5 http://content.mellanox.com/ofed/MLNX_OFED-4.5-1.0.1.0/MLNX_OFED_LINUX-4.5-1.0.1.0-rhel7.6-x86_64.tgz
tar zxvf MLNX_OFED_LINUX-4.5-1.0.1.0-rhel7.6-x86_64.tgz
sudo ./MLNX_OFED_LINUX-4.5-1.0.1.0-rhel7.6-x86_64/mlnxofedinstall --add-kernel-support
```

A Windows, töltse le és telepítse a WinOF-2 illesztőprogramokat ConnectX-5. a [Mellanox](https://www.mellanox.com/page/products_dyn?product_family=32&menu_section=34)

## <a name="enable-ipoib"></a>IPoIB engedélyezése

```bash
sudo sed -i 's/LOAD_EIPOIB=no/LOAD_EIPOIB=yes/g' /etc/infiniband/openib.conf
sudo /etc/init.d/openibd restart
if [ $? -eq 1 ]
then
  sudo modprobe -rv  ib_isert rpcrdma ib_srpt
  sudo /etc/init.d/openibd restart
fi
```

## <a name="assign-an-ip-address"></a>IP-cím hozzárendelése

IP-cím hozzárendelése a ib0 kapcsolat használatával:

- Manuálisan IP-cím hozzárendelése a ib0 felületen (rendszergazdaként).

    ```bash
    ifconfig ib0 $(sed '/rdmaIPv4Address=/!d;s/.*rdmaIPv4Address="\([0-9.]*\)".*/\1/' /var/lib/waagent/SharedConfig.xml)/16
    ```

VAGY

- WALinuxAgent használatával rendelje hozzá az IP-címet, és adja meg maradnak.

    ```bash
    yum install -y epel-release
    yum install -y python-pip
    python -m pip install --upgrade pip setuptools wheel
    wget "https://github.com/Azure/WALinuxAgent/archive/release-2.2.36.zip"
    unzip release-2.2.36.zip
    cd WALinuxAgent*
    python setup.py install --register-service --force
    sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
    sed -i -e 's/# AutoUpdate.Enabled=y/AutoUpdate.Enabled=y/g' /etc/waagent.conf
    systemctl restart waagent
    ```

## <a name="next-steps"></a>További lépések

Tudjon meg többet [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) az Azure-ban.
