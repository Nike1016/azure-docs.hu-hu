---
title: Azure Virtual Machines az SAP NetWeaver tervezése és megvalósítása | Microsoft Docs
description: Azure Virtual Machines az SAP NetWeaver tervezése és megvalósítása
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: gwallace
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/07/2019
ms.author: sedusch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 11a54dee653bcfa6c94a861e483183ac39f465bf
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "67710182"
---
# <a name="azure-virtual-machines-planning-and-implementation-for-sap-netweaver"></a>Azure Virtual Machines az SAP NetWeaver tervezése és megvalósítása

[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1139904]:https://launchpad.support.sap.com/#/notes/1139904
[1173395]:https://launchpad.support.sap.com/#/notes/1173395
[1245200]:https://launchpad.support.sap.com/#/notes/1245200
[1409604]: https://launchpad.support.sap.com/#/notes/1409604
[1558958]:https://launchpad.support.sap.com/#/notes/1558958
[1585981]:https://launchpad.support.sap.com/#/notes/1585981
[1588316]:https://launchpad.support.sap.com/#/notes/1588316
[1590719]:https://launchpad.support.sap.com/#/notes/1590719
[1597355]: https://launchpad.support.sap.com/#/notes/1597355
[1605680]:https://launchpad.support.sap.com/#/notes/1605680
[1619720]: https://launchpad.support.sap.com/#/notes/1619720
[1619726]:https://launchpad.support.sap.com/#/notes/1619726
[1619967]:https://launchpad.support.sap.com/#/notes/1619967
[1750510]:https://launchpad.support.sap.com/#/notes/1750510
[1752266]:https://launchpad.support.sap.com/#/notes/1752266
[1757924]:https://launchpad.support.sap.com/#/notes/1757924
[1757928]:https://launchpad.support.sap.com/#/notes/1757928
[1758182]:https://launchpad.support.sap.com/#/notes/1758182
[1758496]:https://launchpad.support.sap.com/#/notes/1758496
[1772688]:https://launchpad.support.sap.com/#/notes/1772688
[1814258]:https://launchpad.support.sap.com/#/notes/1814258
[1882376]:https://launchpad.support.sap.com/#/notes/1882376
[1909114]: https://launchpad.support.sap.com/#/notes/1909114
[1922555]:https://launchpad.support.sap.com/#/notes/1922555
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[1941500]:https://launchpad.support.sap.com/#/notes/1941500
[1956005]:https://launchpad.support.sap.com/#/notes/1956005
[1973241]:https://launchpad.support.sap.com/#/notes/1973241
[1984787]: https://launchpad.support.sap.com/#/notes/1984787
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[2002167]: https://launchpad.support.sap.com/#/notes/2002167
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2069760]: https://launchpad.support.sap.com/#/notes/2069760
[2121797]:https://launchpad.support.sap.com/#/notes/2121797
[2134316]:https://launchpad.support.sap.com/#/notes/2134316
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]: https://launchpad.support.sap.com/#/notes/2243692

[azure-cli]:../../../cli-install-nodejs.md
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azureps-cmdlets-docs
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md#subscription-limits

[dbms-guide]:dbms-guide.md
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b
[dbms-guide-5.6]:dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737
[dbms-guide-8.4.1]:dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573
[dbms-guide-8.4.2]:dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d
[dbms-guide-8.4.3]:dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c
[dbms-guide-8.4.4]:dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818
[dbms-guide-900-sap-cache-server-on-premises]:dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8

[dbms-guide-figure-100]:media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]:deployment-guide.md
[deployment-guide-2.2]:deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94
[deployment-guide-3.1.2]:deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab
[deployment-guide-3.2]:deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281
[deployment-guide-3.3]:deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2
[deployment-guide-3.4]:deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e
[deployment-guide-4.1]:deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7
[deployment-guide-4.2]:deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e
[deployment-guide-4.3]:deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc
[deployment-guide-4.4.2]:deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542
[deployment-guide-4.4]:deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d
[deployment-guide-4.5.1]:deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4
[deployment-guide-4.5.2]:deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f
[deployment-guide-4.5]:deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca
[deployment-guide-5.1]:deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2
[deployment-guide-5.2]:deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1
[deployment-guide-5.3]:deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8

[deployment-guide-configure-monitoring-scenario-1]:deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b
[deployment-guide-configure-proxy]:deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d
[deployment-guide-figure-100]:media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]:media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]:deployment-guide.md#figure-11
[deployment-guide-figure-1100]:media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]:media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]:media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]:deployment-guide.md#figure-14
[deployment-guide-figure-1400]:media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]:media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]:media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]:deployment-guide.md#figure-5
[deployment-guide-figure-50]:media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]:media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]:deployment-guide.md#figure-6
[deployment-guide-figure-600]:media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]:deployment-guide.md#figure-7
[deployment-guide-figure-700]:media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]:media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]:media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]:deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]:deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]:deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]:deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b

[deploy-template-cli]:../../../resource-group-template-deploy-cli.md
[deploy-template-portal]:../../../resource-group-template-deploy-portal.md
[deploy-template-powershell]:../../../resource-group-template-deploy.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md
[getting-started-dbms]:get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]:../../virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]:../../virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]:../../virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]:../../virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]:../../virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]:../../virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]:https://go.microsoft.com/fwlink/?LinkId=613056

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-Azvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]:planning-guide.md  
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10
[planning-guide-3.1]:planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a
[planning-guide-3.2.1]:planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358
[planning-guide-3.2.2]:planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560
[planning-guide-3.2.3]:planning-guide.md#18810088-f9be-4c97-958a-27996255c665
[planning-guide-3.2]:planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77
[planning-guide-3.3.2]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92
[planning-guide-5.1.1]:planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53
[planning-guide-5.1.2]:planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c
[planning-guide-5.2.1]:planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef
[planning-guide-5.2.2]:planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3
[planning-guide-5.2]:planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7
[planning-guide-5.3.1]:planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79
[planning-guide-5.3.2]:planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a
[planning-guide-5.4.2]:planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1
[planning-guide-5.5.1]:planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646
[planning-guide-5.5.3]:planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d
[planning-guide-7.1]:planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30
[planning-guide-7]:planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3
[planning-guide-azure-premium-storage]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92

[planning-guide-figure-100]:media/virtual-machines-shared-sap-planning-guide/100-single-vm-in-azure.png
[planning-guide-figure-1300]:media/virtual-machines-shared-sap-planning-guide/1300-ref-config-iaas-for-sap.png
[planning-guide-figure-1400]:media/virtual-machines-shared-sap-planning-guide/1400-attach-detach-disks.png
[planning-guide-figure-1600]:media/virtual-machines-shared-sap-planning-guide/1600-firewall-port-rule.png
[planning-guide-figure-1700]:media/virtual-machines-shared-sap-planning-guide/1700-single-vm-demo.png
[planning-guide-figure-1900]:media/virtual-machines-shared-sap-planning-guide/1900-vm-set-vnet.png
[planning-guide-figure-200]:media/virtual-machines-shared-sap-planning-guide/200-multiple-vms-in-azure.png
[planning-guide-figure-2100]:media/virtual-machines-shared-sap-planning-guide/2100-s2s.png
[planning-guide-figure-2200]:media/virtual-machines-shared-sap-planning-guide/2200-network-printing.png
[planning-guide-figure-2300]:media/virtual-machines-shared-sap-planning-guide/2300-sapgui-stms.png
[planning-guide-figure-2400]:media/virtual-machines-shared-sap-planning-guide/2400-vm-extension-overview.png
[planning-guide-figure-2500]:media/virtual-machines-shared-sap-planning-guide/planning-monitoring-overview-2502.png
[planning-guide-figure-2600]:media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-2901]:media/virtual-machines-shared-sap-planning-guide/2901-azure-ha-sap-ha-md.png
[planning-guide-figure-300]:media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-3201]:media/virtual-machines-shared-sap-planning-guide/3201-sap-ha-with-sql-md.png
[planning-guide-figure-400]:media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[powershell-install-configure]:https://docs.microsoft.com/powershell/azure/install-az-ps
[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/resource-group-overview.md
[resource-groups-networking]:../../../networking/networking-overview.md
[sap-pam]:https://support.sap.com/pam
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[storage-azure-cli]:../../../storage/common/storage-azure-cli.md
[storage-azure-cli-copy-blobs]:../../../storage/common/storage-azure-cli.md#copy-blobs
[storage-introduction]:../../../storage/common/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md
[storage-premium-storage-preview-portal]:../../windows/disks-types.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/storage-scalability-targets.md
[storage-use-azcopy]:../../../storage/common/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:../../linux/attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../resource-manager-deployment-model.md
[virtual-machines-Az-versus-azuresm]:virtual-machines-linux-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md
[virtual-machines-linux-agent-user-guide]:../../extensions/agent-linux.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../extensions/agent-windows.md#command-line-options
[virtual-machines-linux-capture-image]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager-capture]:../../linux/capture-image.md#step-2-capture-the-vm
[virtual-machines-windows-capture-image-resource-manager]:../../windows/capture-image.md
[virtual-machines-windows-capture-image]:../../virtual-machines-windows-generalize-vhd.md
[virtual-machines-windows-capture-image-prepare-the-vm-for-image-capture]:../../virtual-machines-windows-generalize-vhd.md
[virtual-machines-linux-configure-raid]:../../linux/configure-raid.md
[virtual-machines-linux-configure-lvm]:../../linux/configure-lvm.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:../../virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:../../linux/suse-create-upload-vhd.md
[virtual-machines-linux-create-upload-vhd-oracle]:../../linux/oracle-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:../../linux/redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:../../linux/add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:../../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:../../linux/quick-create-cli.md
[virtual-machines-linux-update-agent]:../../linux/update-agent.md
[virtual-machines-manage-availability]:../../linux/manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:virtual-machines-windows-create-powershell.md
[virtual-machines-sizes-linux]:../../linux/sizes.md
[virtual-machines-sizes-windows]:../../windows/sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:./../../windows/sql/virtual-machines-windows-sql-high-availability-dr.md
[virtual-machines-sql-server-infrastructure-services]:./../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md
[virtual-machines-sql-server-performance-best-practices]:./../../windows/sql/virtual-machines-windows-sql-performance.md
[virtual-machines-upload-image-windows-resource-manager]:../../virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:../../virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/documentation/templates/sql-server-2014-alwayson-dsc/
[virtual-network-deploy-multinic-arm-cli]:../../linux/multiple-nics.md
[virtual-network-deploy-multinic-arm-ps]:../../windows/multiple-nics.md
[virtual-network-deploy-multinic-arm-template]:../../../virtual-network/template-samples.md
[virtual-networks-configure-vnet-to-vnet-connection]:../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../../../virtual-network/manage-virtual-network.md#create-a-virtual-network
[virtual-networks-manage-dns-in-vnet]:../../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics-windows]:../../windows/multiple-nics.md
[virtual-networks-multiple-nics-linux]:../../linux/multiple-nics.md
[virtual-networks-nsg]:../../../virtual-network/security-overview.md
[virtual-networks-reserved-private-ip]:../../../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]:../../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]:../../../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]:../../../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]:../../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]:../../../vpn-gateway/vpn-gateway-about-vpngateways.md
[vpn-gateway-site-to-site-create]:../../../vpn-gateway/vpn-gateway-site-to-site-create.md
[vpn-gateway-vpn-faq]:../../../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../../../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../../../xplat-cli-azure-resource-manager.md
[capture-image-linux-step-2-create-vm-image]:../../linux/capture-image.md#step-2-create-vm-image

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Microsoft Azure lehetővé teszi a vállalatok számára a számítási és tárolási erőforrások beszerzését a hosszadalmas beszerzési ciklusok nélküli minimális idő alatt. Az Azure Virtual Machine szolgáltatás lehetővé teszi a vállalatok számára, hogy klasszikus alkalmazásokat telepítsenek, például az SAP NetWeaver-alapú alkalmazásokat az Azure-ba, és a megbízhatóságot és a rendelkezésre állást anélkül bővítik, hogy a helyszínen további erőforrások Az Azure Virtual Machine Services a létesítmények közötti kapcsolatot is támogatja, amely lehetővé teszi a vállalatok számára, hogy aktívan integrálják az Azure Virtual Machines a helyszíni tartományba, a saját felhőbe és az SAP-rendszerük környezetében.
Ez a tanulmány ismerteti Microsoft Azure virtuális gép alapjait, és végigvezeti az Azure-beli SAP NetWeaver-telepítések tervezési és megvalósítási szempontjain, és így a tényleges Kezdés előtt olvassa el a dokumentumot. az SAP NetWeaver üzembe helyezése az Azure-ban.
A tanulmány kiegészíti az SAP-telepítési dokumentációt és az SAP-megjegyzéseket, amelyek az SAP-szoftverek telepítésének és üzembe helyezésének elsődleges erőforrásait jelölik az adott platformokon.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="summary"></a>Összegzés
A felhő-számítástechnika egy széles körben használt kifejezés, amely egyre nagyobb jelentőséggel bír az informatikai iparban, a kisvállalkozásoktól egészen a nagy és a multinacionális vállalatokig.

A Microsoft Azure a Microsoft Cloud Services platformja, amely az új lehetőségek széles spektrumát kínálja. Mostantól az ügyfelek gyorsan üzembe helyezhetik és kioszthatják az alkalmazásokat a felhőben, így azok nem korlátozódnak technikai vagy költségvetési korlátozásokra. Az idő és a költségvetés hardveres infrastruktúrába való befektetése helyett a vállalatok az alkalmazásra, az üzleti folyamatokra, valamint az ügyfelek és a felhasználók számára nyújtott előnyökre is koncentrálnak.

A Microsoft Azure Virtual Machine Services szolgáltatással a Microsoft átfogó szolgáltatott infrastruktúra (IaaS) platformot kínál. Az Azure virtuális gépek támogatják az SAP NetWeaver-alapú alkalmazásokat (IaaS). Ez a tanulmány azt ismerteti, hogyan tervezhet és implementálhat SAP NetWeaver-alapú alkalmazásokat Microsoft Azureon belül a választott platformon.

A könyv két fő szempontra összpontosít:

* Az első rész két támogatott üzembe helyezési mintát ismertet az Azure-beli SAP NetWeaver-alapú alkalmazásokhoz. Ismerteti az Azure általános kezelését is az SAP-környezetekben.
* A második rész részletei az első részben ismertetett két különböző forgatókönyv megvalósításában.

További forrásokért tekintse meg a jelen dokumentumban található [erőforrások][planning-guide-1.2] című fejezetet.

### <a name="definitions-upfront"></a>Előzetes definíciók
A dokumentum teljes egészében a következő kifejezéseket használjuk:

* IaaS Szolgáltatásként szolgáló infrastruktúra
* PaaS Szolgáltatásként nyújtott platform
* Saas Szolgáltatásként szolgáló szoftver
* SAP-összetevő: egy egyéni SAP-alkalmazás, például ECC, BW, Solution Manager vagy S/4HANA.  Az SAP-összetevők a hagyományos ABAP vagy Java-technológiákon, vagy egy nem NetWeaver-alapú alkalmazáson, például üzleti objektumokon alapulnak.
* SAP-környezet: egy vagy több SAP-összetevő logikailag csoportosítva olyan üzleti funkciók végrehajtásához, mint például a fejlesztés, a QAS, a képzés, a DR vagy a termelés.
* SAP-környezet: Ez a kifejezés a teljes SAP-eszközre vonatkozik az ügyfél informatikai környezetében. Az SAP-környezet az összes éles és nem éles környezetet magában foglalja.
* SAP-System: Az adatbázis-kezelő réteg és az alkalmazás rétegének kombinációja, például egy SAP ERP fejlesztői rendszer, SAP BW test System, SAP CRM üzemi rendszer stb. Az Azure-beli üzemelő példányok nem támogatják ezt a két réteget a helyszíni és az Azure közötti felosztásban. Azt jelenti, hogy az SAP-rendszer központi telepítése helyileg történik, vagy az Azure-ban van üzembe helyezve. Az SAP-környezetek különböző rendszereit azonban az Azure-ban vagy a helyszínen is üzembe helyezheti. Például üzembe helyezheti az SAP CRM fejlesztési és tesztelési rendszereit az Azure-ban, de az SAP CRM üzemi rendszerét a helyszínen.
* Létesítmények közötti vagy hibrid: Leírja azt a forgatókönyvet, amelyben a virtuális gépeket egy olyan Azure-előfizetéshez telepítik, amely helyek közötti, többhelyes vagy ExpressRoute kapcsolatot létesít a helyszíni adatközpont (ok) és az Azure között. A közös Azure-dokumentációban az ilyen típusú központi telepítések a létesítmények közötti vagy hibrid forgatókönyvekben is szerepelnek. A kapcsolódás oka a helyszíni tartományok, a helyszíni Active Directory/OpenLDAP és a helyszíni DNS kiterjesztése az Azure-ba. A helyszíni környezet az előfizetés Azure-eszközeire van kiterjesztve. Ennek a bővítménynek a birtokában a virtuális gépek a helyszíni tartomány részei lehetnek. A helyszíni tartomány tartományi felhasználói hozzáférhetnek a kiszolgálókhoz, és szolgáltatásokat futtathatnak ezeken a virtuális gépeken (például az adatbázis-kezelő szolgáltatásokban). A helyszíni és az Azure-beli üzembe helyezett virtuális gépek közötti kommunikáció és névfeloldás lehetséges. Ez a leggyakoribb és szinte kizárólagos eset, ha SAP-eszközöket helyez üzembe az Azure-ban. További információt [ebben][vpn-gateway-cross-premises-options] a cikkben talál. [][vpn-gateway-site-to-site-create]

> [!NOTE]
> Az SAP-rendszerek olyan létesítmények közötti vagy hibrid telepítései, amelyeken az Azure Virtual Machines az SAP Systems-t futtatja, az éles környezetben működő SAP-rendszerek tagjai. A létesítmények közötti vagy hibrid konfigurációk támogatják a részek üzembe helyezését vagy az SAP-tájak Azure-ba való betöltését. A teljes SAP-környezetnek az Azure-ban való futtatásához a virtuális gépeknek a helyszíni tartományhoz és a HIRDETÉSekhez/OpenLDAP kell tartozniuk. 
>
>



### <a name="e55d1e22-c2c8-460b-9897-64622a34fdff"></a>Erőforrások
[Itt](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started)található az Azure dokumentációjában az SAP-számítási feladatok belépési pontja. Ettől a belépési ponttól kezdve számos cikket talál, amelyek a következő témaköröket fedik le:

- SAP NetWeaver és Business One az Azure-ban
- SAP adatbázis-kezelői útmutatók az Azure-ban különböző adatbázis-kezelő rendszerekhez
- Magas rendelkezésre állás és vész-helyreállítás az Azure-beli SAP-munkaterheléshez
- Konkrét útmutatás a SAP HANA Azure-beli futtatásához
- Az Azure HANA nagyméretű példányaihoz kapcsolódó útmutatás az SAP HANA adatbázis-kezelő rendszerhez 


> [!IMPORTANT]
> Ahol lehetséges, a hivatkozó SAP-telepítési útmutatókra vagy más SAP-dokumentációra mutató hivatkozás van használatban (lásd: <http://service.sap.com/instguides>InstGuide-01. hivatkozás). Ha az előfeltételek, a telepítési folyamat vagy az adott SAP-funkciók részleteiről van szó, az SAP-dokumentációt és az útmutatókat mindig alaposan el kell olvasni, mivel a Microsoft-dokumentumok csak a telepített és a-ben üzemeltetett SAP-szoftverek konkrét feladataira vonatkoznak. Microsoft Azure virtuális gép.
>
>

A következő SAP-megjegyzések az Azure-beli SAP-vel kapcsolatos témakörhöz kapcsolódnak:

| Megjegyzés száma | Beosztás |
| --- | --- |
| [1928533] |SAP-alkalmazások az Azure-ban: Támogatott termékek és méretezés |
| [2015553] |SAP on Microsoft Azure: Támogatási előfeltételek |
| [1999351] |Az SAP-hez készült bővített Azure monitoring hibaelhárítása |
| [2178632] |Az SAP-hez Microsoft Azure legfontosabb monitorozási metrikák |
| [1409604] |Virtualizáció Windows rendszeren: Továbbfejlesztett figyelés |
| [2191498] |SAP Linuxon az Azure-ban: Továbbfejlesztett figyelés |
| [2243692] |Linux on Microsoft Azure (IaaS) virtuális gép: SAP-licencekkel kapcsolatos problémák |
| [1984787] |SUSE LINUX Enterprise Server 12: Telepítési megjegyzések |
| [2002167] |Red Hat Enterprise Linux 7. x: Telepítés és frissítés |
| [2069760] |Oracle Linux 7. x SAP telepítése és frissítése |
| [1597355] |A Linux rendszerhez készült swap-space-javaslat |

Olvassa el a Linux rendszerhez készült összes SAP-megjegyzést tartalmazó [állapotváltozás wikit](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) is.

[Ebben a cikkben][azure-subscription-service-limits-subscription]a általános alapértelmezett korlátozások és az Azure-előfizetések maximális korlátai találhatók.

## <a name="possible-scenarios"></a>Lehetséges forgatókönyvek
Az SAP-t gyakran a vállalatok egyik legfontosabb alapvető fontosságú alkalmazásának tekinti. Az alkalmazások architektúrája és műveletei többnyire összetettek, és fontos, hogy megfeleljenek a rendelkezésre állásra és a teljesítményre vonatkozó követelményeknek.

Ezért a vállalatoknak alaposan meg kell fontolniuk, hogy melyik felhőalapú szolgáltató dönti el, hogy az üzleti szempontból kritikus fontosságú üzleti folyamatokat kívánják-e futtatni. Az Azure ideális nyilvános felhőalapú platform az üzleti szempontból kritikus fontosságú SAP-alkalmazások és az üzleti folyamatok számára. Az Azure-infrastruktúra széles választéka miatt napjainkban szinte minden meglévő SAP NetWeaver-és S/4HANA-rendszer üzemeltethető az Azure-ban. Az Azure számos terabájt memóriát és több mint 200 processzort biztosít a virtuális gépek számára. Az Azure-on túl [nagy méretű Hana](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)-példányok is használhatók, amelyek lehetővé teszik a legfeljebb 120TB 24TB és kibővíthető Ana üzembe helyezését. 


Az SAP-rendszerek sikeres üzembe helyezéséhez az Azure IaaS vagy a IaaS-ben fontos megérteni a hagyományos outsourcerek vagy a szolgáltatók és a IaaS-ajánlatok ajánlatai közötti jelentős különbségeket. Míg a hagyományos szolgáltató vagy a kiszervezés az infrastruktúrát (hálózat, tárolás és kiszolgáló típusa) az ügyfél által üzemeltetett munkaterheléshez igazítja, az ügyfél vagy a partner feladata a számítási feladat jellemzése, és kiválasztja a megfelelő Azure-t a virtuális gépek, a tárolás és a hálózat összetevői a IaaS üzembe helyezéséhez.

Első lépésként az ügyfeleknek ellenőriznie kell a következő elemeket:

* Az SAP által támogatott VM-típusok az Azure-ban
* Az SAP által támogatott termékek/kiadások az Azure-ban
* Az Azure-beli SAP-kiadások támogatott operációsrendszer-és adatbázis-kiadásai
* SAP átviteli sebesség különböző Azure SKU-kal

Az ezekre a kérdésekre adott válaszok a [1928533]-es SAP-megjegyzésben olvashatók.

Második lépésként az Azure-erőforrások és a sávszélesség korlátozásait össze kell vetni a helyszíni rendszerek tényleges erőforrás-fogyasztásával. Ezért az ügyfeleknek ismerniük kell az SAP-ban támogatott Azure-típusok különböző képességeit a következő területeken:

* Különböző virtuálisgép-típusok CPU-és memória-erőforrásai
* A különböző virtuálisgép-típusok és a IOPS sávszélesség
* A különböző virtuálisgép-típusok hálózati képességei.

Ezen adatmennyiségek többsége [itt (Linux)][virtual-machines-sizes-linux] és [itt (Windows)][virtual-machines-sizes-windows]érhető el.

Ne feledje, hogy a fenti hivatkozásban felsorolt korlátok felső határértékek. Nem jelenti azt, hogy az egyes erőforrások korlátai, például a IOPS minden esetben megadhatók. A kivételek azonban a kiválasztott VM-típus CPU-és memória-erőforrásai. Az SAP által támogatott virtuálisgép-típusok esetében a CPU-és a memória-erőforrások le vannak foglalva, és a virtuális gépen történő használathoz bármely időpontban elérhetővé válnak.

A Microsoft Azure platform több-bérlős platform. Ennek eredményeképpen a tárolók, a hálózatok és az egyéb erőforrások megoszthatók a bérlők között. Az intelligens szabályozás és a kvóta logikájának használatával megakadályozható, hogy egy bérlő drasztikus módon befolyásolja egy másik bérlő (zajos szomszéd) teljesítményét. Különösen a SAP HANA Azure-platformjának tanúsítása érdekében a Microsoftnak bizonyítania kell az erőforrás-elkülönítést olyan esetekben, amikor több virtuális gép is futhat ugyanazon a gazdagépen, rendszeres időközönként az SAP-hez. Bár az Azure-ban a logika megtartja a kis-és nagyvállalati adatforgalomban tapasztalható eltéréseket, a nagy mértékben megosztott platformok általában nagyobb eltéréseket vezetnek be az erőforrás/sávszélesség rendelkezésre állásában, mint az ügyfelek a helyszíni környezetben. Annak valószínűsége, hogy az Azure-beli SAP-rendszer nagyobb eltéréseket tapasztalhat, mint a helyszíni rendszerekben, figyelembe kell venni.

Az utolsó lépés a rendelkezésre állási követelmények kiértékelése. Előfordulhat, hogy az alapul szolgáló Azure-infrastruktúrát frissíteni kell, és szükség van a virtuális gépeket futtató gazdagépek újraindítására. A Microsoft dokumentálja az [Azure-beli virtuális gépek karbantartásának](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates)különböző eseteit. Azon ritka esetekben, amikor a virtuális gépek újraindítására kényszerülnek, de még ennél is fontosabbak, ha a vendég operációs rendszer vagy az adatbázis-kezelői összetevők javításához szükséges, az éles SAP-rendszerek számára érvényes magas rendelkezésre állási fogalmakat kell kidolgoznia. Ez a követelmény nem különbözik a helyszíni igényektől. A Microsoft folyamatosan fejleszti az Azure platformot a platformok változásai által okozott állásidő csökkentése érdekében. 

Az SAP-rendszer Azure-ra való sikeres üzembe helyezéséhez a helyszíni SAP-rendszer (ek) operációs rendszerének, adatbázisának és SAP-alkalmazásainak szerepelniük kell az SAP Azure támogatási mátrixában, az Azure-infrastruktúra által biztosított erőforrásoknak megfelelően, és amelyek működhetnek a rendelkezésre állási SLA-Microsoft Azure ajánlatokkal. A rendszerek azonosítása során a következő két telepítési forgatókönyv egyikét kell eldöntenie.





### <a name="f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10"></a>Létesítmények közötti – egy vagy több SAP virtuális gép üzembe helyezése az Azure-ban, a helyszíni hálózatba való teljes integráció követelménye
![VPN a helyek közötti kapcsolattal (telephelyek közötti)][planning-guide-figure-300]

Ez a forgatókönyv egy létesítmények közötti forgatókönyv, amely számos lehetséges üzembe helyezési mintát tartalmaz. Azt is megteheti, hogy a helyszíni SAP-környezet egyes részeit és az Azure-beli SAP-környezet más részeit futtatja. Az, hogy az SAP-összetevők egy részét az Azure-ban futtatja, transzparensnek kell lennie a végfelhasználók számára. Így az SAP Transport Hibajavító rendszer (STM), az RFC-kommunikáció, a nyomtatás, a biztonság (SSO) stb. zökkenőmentesen működik az Azure-on futó SAP-rendszerek esetében. A létesítmények közötti forgatókönyv azonban azt is ismerteti, hogy a teljes SAP-környezet hogyan fut az Azure-ban az ügyfél tartományával és a DNS az Azure-ba való kiterjesztésével.

> [!NOTE]
> Ez az üzembe helyezési forgatókönyv, amely a produktív SAP-rendszerek futtatására használható.
>
>

A helyszíni hálózat Microsoft Azurehoz való összekapcsolásával kapcsolatos további információkért olvassa el [ezt a cikket.][vpn-gateway-create-site-to-site-rm-powershell]

> [!IMPORTANT]
> Ha az Azure-beli és a helyszíni ügyfelek központi telepítései közötti helyzetekről beszélünk, a teljes SAP-rendszerek részletességét tekintjük át. Olyan forgatókönyvek, amelyek *nem támogatottak* a létesítmények közötti helyzetekben:
>
> * Az SAP-alkalmazások különböző rétegeit különböző üzembe helyezési módokon futtathatja. Például a helyszíni adatbázis-kezelő réteg futtatása, de az SAP-alkalmazás rétege Azure-beli virtuális gépekként üzembe helyezett virtuális gépeken, vagy fordítva.
> * Egy SAP-réteg egyes összetevői az Azure-ban és néhány helyszínen. Például az SAP-alkalmazás réteg példányainak felosztása a helyszíni és az Azure-beli virtuális gépek között.
> * Az egyik rendszer SAP-példányait futtató virtuális gépek több Azure-régióban való elosztása nem támogatott.
>
> Ennek a korlátozásnak az az oka, hogy egy SAP-rendszeren belül alacsony késésű, nagy teljesítményű hálózatra van szükség, különösen az alkalmazások példányai és az SAP-rendszer adatbázis-kezelő rétege között.
>
> A rendszerek és régiók speciális megtervezése akkor fordul elő, ha több, nagymértékben integrált SAP-rendszert használ. Győződjön meg arról, hogy ezeket a rendszereket a lehető legközelebb helyezi üzembe a hálózat késésének csökkentése érdekében. A kifejezetten integrált SAP-rendszerek példái a következők:
> * SAP BW SAP OLTP rendszerek (például ERP vagy CRM vagy SRM) adatainak beolvasása; vagy
> * Az SAP SLT több SPA rendszer vagy az SAP és a nem SAP rendszerek közötti adatreplikálásra szolgál. vagy
> * SAP S/4 egy SAP ERP rendszerhez csatlakoztatva; stb.


### <a name="supported-os-and-database-releases"></a>Támogatott operációsrendszer-és adatbázis-kiadások
* Az Azure-beli virtuálisgép-szolgáltatásokhoz támogatott Microsoft Server szoftvereket ebben a <https://support.microsoft.com/kb/2721672>cikkben találja:.
* A támogatott operációsrendszer-kiadásokat, az Azure-beli virtuálisgép-szolgáltatásokban támogatott adatbázisrendszer-kiadásokat az SAP- [1928533]SAP-megjegyzéssel együtt dokumentáljuk.
* Az Azure Virtual Machine Services által támogatott SAP-alkalmazásokat és-kiadásokat a [1928533]-es SAP-Megjegyzés dokumentálja.
* Az SAP-forgatókönyvek esetében csak a 64 bites lemezképek támogatottak a vendég virtuális gépek Azure-ban való futtatásához. Ennek eredményeképpen csak a 64 bites SAP-alkalmazások és-adatbázisok támogatottak.

## <a name="microsoft-azure-virtual-machine-services"></a>Virtuális gépi szolgáltatások Microsoft Azure
A Microsoft Azure platform egy Microsoft-adatközpontokban üzemeltetett és üzemeltetett, internetre kiterjedő felhőalapú szolgáltatási platform. A platform része a Microsoft Azure virtuálisgép-szolgáltatások (infrastruktúra-szolgáltatás, IaaS) és a szolgáltatásként nyújtott platform ("a") funkcióinak összessége.

Az Azure platform csökkenti a kezdeti technológia és az infrastruktúra megvásárlásának szükségességét. Leegyszerűsíti az alkalmazások karbantartását és üzemeltetését azáltal, hogy igény szerinti számítási és tárolási kapacitást biztosít a webalkalmazások és a csatlakoztatott alkalmazások üzemeltetéséhez, méretezéséhez és kezeléséhez. Az infrastruktúra-kezelés olyan platformmal van automatizálva, amely magas rendelkezésre állást és dinamikus skálázást biztosít a használati igényeknek megfelelően az utólagos elszámolású díjszabási modell használatával.

![Microsoft Azure virtuálisgép-szolgáltatások elhelyezése][planning-guide-figure-400]

Az Azure Virtual Machine Services segítségével a Microsoft lehetővé teszi, hogy egyéni kiszolgálói lemezképeket helyezzen üzembe az Azure-ban IaaS-példányként (lásd a 4. ábrát). Az Azure-beli Virtual Machines a Hyper-V virtuális merevlemezeken (VHD) alapul, és különböző operációs rendszereket futtathatnak vendég operációs rendszerként.

Működési szempontból az Azure Virtual Machine szolgáltatás hasonló élményeket kínál, mint a helyszínen üzembe helyezett virtuális gépek. Azonban ez a jelentős előnye, hogy nem szükséges az infrastruktúra beszerzéséhez, felügyeletéhez és kezeléséhez. A fejlesztők és a rendszergazdák teljes mértékben szabályozzák az operációs rendszer rendszerképét ezeken a virtuális gépeken belül. A rendszergazdák távolról is bejelentkezhetnek a virtuális gépekre a karbantartási és hibaelhárítási feladatok, valamint a szoftvertelepítési feladatok elvégzéséhez. Az üzembe helyezés tekintetében az egyetlen korlátozás az Azure-beli virtuális gépek méretei és képességei. Előfordulhat, hogy ezek a méretek nem annyira részletesek a konfigurációban, ahogy azt a helyszínen lehet elvégezni. A következők kombinációját képviselő virtuálisgép-típusok közül választhat:

* VCPU száma
* Memory (Memória)
* A csatlakoztatható virtuális merevlemezek száma
* Hálózati és tárolási sávszélesség

A különböző ajánlott virtuálisgép-méretek mérete és korlátai a [jelen cikk (Linux)][virtual-machines-sizes-linux] és [a jelen cikk (Windows)][virtual-machines-sizes-windows]táblázatában láthatók.

Az Azure-régiók mindegyikében (az Azure-régiók esetében lásd a következő fejezetet) nem minden különböző virtuálisgép-sorozat kínálható fel. Ügyeljen arra is, hogy nem minden virtuális gép vagy virtuálisgép-sorozat hitelesítve van az SAP-hez.

> [!IMPORTANT]
> Az SAP NetWeaver-alapú alkalmazások használatához csak a [1928533] -es SAP-megjegyzésben felsorolt virtuálisgép-típusok és-konfigurációk részhalmaza támogatott.
>
>

### <a name="be80d1b9-a463-4845-bd35-f4cebdb5424a"></a>Azure-régiók
A Virtual Machines üzembe helyezése az *Azure*-régiókba történik. Egy Azure-régió lehet egy vagy több olyan adatközpont is, amelyek közelében található. A világon a legtöbb geopolitikai régió esetében a Microsoft legalább két Azure-régióval rendelkezik. Európában például egy *észak-európai* Azure-régió és egy *Nyugat-Európa*található. A geopolitikai régiókban a két Azure-régiót elég nagy távolság választja el, így a természeti vagy technikai katasztrófák nem érintik az azonos geopolitikai régióba tartozó Azure-régiókat. Mivel a Microsoft folyamatosan felépíti az új Azure-régiókat a különböző geopolitikai régiókban, a régiók száma folyamatosan növekszik, és a DEC 2015 a már meglévő további régiókkal rendelkező 20 Azure-régiót is elérte. Ön, hogy az ügyfél minden régióban üzembe helyezhet SAP-rendszereket, beleértve a két, Kínában található Azure-régiót. Az Azure-régiók aktuális naprakész információit a következő webhely tartalmazza:<https://azure.microsoft.com/regions/>

### <a name="8d8ad4b8-6093-4b91-ac36-ea56d80dbf77"></a>A Microsoft Azure virtuális gép koncepciója
A Microsoft Azure egy infrastruktúra-szolgáltatási (IaaS) megoldást kínál, amely a helyszíni virtualizációs megoldásként hasonló funkciókkal rendelkező Virtual Machines üzemeltetésére szolgál. Létrehozhat Virtual Machineseket a Azure Portal, a PowerShell vagy a parancssori felületről, amely üzembe helyezési és felügyeleti funkciókat is kínál.

Az Azure Resource Manager lehetővé teszi, hogy alkalmazásait egy deklaratív sablon használatával helyezze üzembe. Egyetlen sablonnal több szolgáltatást is üzembe helyezhet azok függőségeivel együtt. Ugyanazt a sablont használja az alkalmazás ismételt üzembe helyezéséhez az alkalmazás életciklusának minden fázisában.

A Resource Manager-sablonok használatával kapcsolatos további információkért tekintse meg a következőt:

* [Virtuális gépek üzembe helyezése és kezelése Azure Resource Manager-sablonok és az Azure CLI használatával](../../linux/create-ssh-secured-vm-from-template.md)
* [Virtuális gépek kezelése a Azure Resource Manager és a PowerShell használatával][virtual-machines-deploy-rmtemplates-powershell]
* <https://azure.microsoft.com/documentation/templates/>

Egy másik érdekes funkció a rendszerképek létrehozása a Virtual Machinesból, amely lehetővé teszi bizonyos Tárházak előkészítését, amelyekkel gyorsan üzembe helyezhetők a követelményeinek megfelelő virtuálisgép-példányok.

A lemezképek Virtual Machinesról való létrehozásával kapcsolatos további információkért tekintse meg a [jelen cikk (Linux)][virtual-machines-linux-capture-image-resource-manager] és [a jelen cikk (Windows)][virtual-machines-windows-capture-image-resource-manager]című témakört.

#### <a name="df49dc09-141b-4f34-a4a2-990913b30358"></a>Tartalék tartományok
A tartalék tartományok a meghibásodás fizikai egységének felelnek meg, amely szorosan összefügg az adatközpontokban található fizikai infrastruktúrával, míg a fizikai panel vagy az állvány tartalék tartománynak tekinthető, a kettő között nincs közvetlen egy-az-egyhez leképezés.

Ha több Virtual Machines telepít egy SAP-rendszer részeként Microsoft Azure virtuálisgép-szolgáltatásokban, befolyásolhatja az Azure Fabric-vezérlőt, hogy az alkalmazást különböző tartalék tartományokra telepítse, így megfelel a következő követelményeknek: Microsoft Azure SLA. A tartalék tartományok azonban egy Azure-méretezési egységen (több száz számítási csomópontok vagy tárolási csomópontok és Hálózatkezelés) vagy a virtuális gépek egy adott tartalék tartományhoz való hozzárendelése egy olyan dolog, amelynek nincs közvetlen vezérlése. Ahhoz, hogy az Azure Fabric-vezérlőt a virtuális gépek különböző tartalék tartományokon keresztüli üzembe helyezésére irányítsa, egy Azure-beli rendelkezésre állási készletet kell hozzárendelni a virtuális gépekhez a telepítéskor. Az Azure rendelkezésre állási csoportjaival kapcsolatos további információkért lásd a jelen dokumentum Azure-beli [rendelkezésre állási csoportjai][planning-guide-3.2.3] című szakaszát.

#### <a name="fc1ac8b2-e54a-487c-8581-d3cc6625e560"></a>Frissítési tartományok
A frissítési tartományok olyan logikai egységnek felelnek meg, amely segít meghatározni, hogy egy SAP-rendszeren belül egy, több virtuális gépen futó SAP-példányból álló virtuális gép frissítve legyen. Ha frissítés történik, Microsoft Azure végigvezeti a frissítési tartományok egyenkénti frissítésének folyamatán. Ha a virtuális gépeket a különböző frissítési tartományokon keresztül terjesztik a központi telepítési időszakban, az SAP-rendszer részben akár az esetleges állásidőtől is védve lehet. Ahhoz, hogy az Azure-t a különböző frissítési tartományokba tartozó SAP-rendszerek virtuális gépei üzembe helyezésére kényszerítse, egy adott attribútumot kell beállítania az egyes virtuális gépek központi telepítésének időpontjában. A tartalék tartományokhoz hasonlóan az Azure skálázási egység több frissítési tartományra van osztva. Ahhoz, hogy az Azure Fabric-vezérlőt a virtuális gépek különböző frissítési tartományokon keresztüli üzembe helyezéséhez szeretné irányítani, egy Azure-beli rendelkezésre állási készletet kell hozzárendelni a virtuális gépekhez a telepítéskor. Az Azure rendelkezésre állási csoportjaival kapcsolatos további információkért lásd az alábbi Azure-beli [rendelkezésre állási][planning-guide-3.2.3] csoportok című szakaszt.

#### <a name="18810088-f9be-4c97-958a-27996255c665"></a>Azure rendelkezésre állási készletek
Az Azure Virtual Machines egy Azure-beli rendelkezésre állási csoporton belül az Azure Fabric Controller különböző hibatűrési és frissítési tartományokon keresztül terjeszthető. A különböző hibatűrési és frissítési tartományok közötti eloszlás célja, hogy az SAP-rendszer összes virtuális gépe le legyen állítva az infrastruktúra karbantartása vagy egy tartalék tartományon belüli meghibásodás esetén. Alapértelmezés szerint a virtuális gépek nem részei a rendelkezésre állási csoportnak. Egy virtuális gép a rendelkezésre állási csoportokban való részvételét a központi telepítés idején vagy később a virtuális gép újrakonfigurálásával és újratelepítésével határozzák meg.

Az Azure rendelkezésre állási csoportok fogalmának megismeréséhez és a rendelkezésre állási csoportok hibákhoz és frissítési tartományokhoz való kapcsolódásának módjáról [ebben a cikkben olvashat.][virtual-machines-manage-availability]

Ha JSON-sablon használatával szeretné meghatározni Azure Resource Manager rendelkezésre állási csoportjait, tekintse meg [a REST API-specifikációkat](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-compute/2015-06-15/swagger/compute.json) , és keressen rá a "rendelkezésre állás" kifejezésre.

### <a name="a72afa26-4bf4-4a25-8cf7-855d6032157f"></a>Storage Microsoft Azure Storage és adatlemezek
Microsoft Azure Virtual Machines eltérő tárolási típusokat használ. Az SAP Azure-beli virtuálisgép-szolgáltatásokon való megvalósításakor fontos megérteni a két fő tárolási típus közötti különbségeket:

* Nem állandó, felejtő tárterület.
* Állandó tárterület.

Az Azure-beli virtuális gépek nem állandó lemezeket kínálnak a virtuális gép üzembe helyezése után. A virtuális gépek újraindításakor a rendszer a meghajtókon lévő összes tartalmat törli. Ezért az is előfordulhat, hogy az adatfájlok és az adatbázisok naplófájljainak naplózása vagy visszaállítása semmilyen körülmények között nem található meg a nem megőrzött meghajtókon. Előfordulhat, hogy egyes adatbázisok esetében kivételek vannak, ahol ezek a nem megőrzött meghajtók alkalmasak lehetnek a tempdb és a temp tablespaces-hez. Ne használja azonban ezeket a meghajtókat A-sorozatú virtuális gépekhez, mivel ezek a nem megőrzött meghajtók korlátozottak az adott virtuálisgép-családdal való átviteli sebességben. További részletekért olvassa el a [Windows rendszerű virtuális gépek ideiglenes meghajtójának ismertetése az Azure-ban](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/) című cikket.

---
> ![Windows][Logo_Windows] Windows
> 
> Meghajtó D:\ egy Azure-beli virtuális gépen egy nem megőrzött meghajtó, amelyet az Azure számítási csomópontján található egyes helyi lemezek támogatnak. Mivel ez nem tartható fenn, ez azt jelenti, hogy a D:\ található tartalomon végrehajtott módosítások a meghajtó elvész a virtuális gép újraindításakor. A "minden változás", például a tárolt fájlok, a létrehozott könyvtárak, a telepített alkalmazások stb. alapján.
> 
> ![Linux][Logo_Linux] Linux
> 
> A Linux Azure virtuális gépek automatikusan csatlakoztatnak egy meghajtót a/mnt/Resource-on, amely az Azure számítási csomópontján helyi lemezek által támogatott, nem megőrzött meghajtó. Mivel ez nem tartható fenn, ez azt jelenti, hogy a/mnt/Resource lévő tartalmakon végrehajtott módosítások elvesznek a virtuális gép újraindításakor. Bármilyen módosítással, például tárolt fájlokkal, létrehozott könyvtárakkal, telepített alkalmazásokkal stb.
> 
> 

---

Microsoft Azure Storage biztosítja a megőrzött tárterületet, valamint a SAN-tárolóban látható védelem és redundancia jellemző szintjét. Az Azure Storage-alapú lemezek az Azure Storage-szolgáltatásokban található virtuális merevlemezek (VHD-k). A helyi operációsrendszer-lemez (Windows C:\, A Linux-/dev/sda1 az Azure Storage tárolja, és a virtuális géphez csatlakoztatott további kötetek/lemezek is ott tárolódnak.

A meglévő virtuális merevlemezek feltölthetők a helyszíni környezetből, vagy üresek is létrehozhatók az Azure-ból, és a telepített virtuális gépekhez csatolhatók.

Miután létrehozta vagy feltölt egy virtuális merevlemezt az Azure Storage-ba, csatlakoztathatja és csatlakoztathatja azokat egy meglévő virtuális géphez, és átmásolhatja a meglévő (leválasztott) VHD-t.

Mivel ezek a VHD-k megmaradnak, a virtuálisgép-példányok újraindításakor és újbóli létrehozásakor a rendszer az adatkezelési és-módosítási műveletekkel biztonságos. A virtuális merevlemezek még akkor is biztonságosak maradnak, ha egy példány törölve lett, vagy ha a nem operációsrendszer-lemezeket más virtuális gépekhez is csatlakoztatni lehet.

Az Azure Storage szolgáltatással kapcsolatos további információkért tekintse meg a következőt:

* <https://azure.microsoft.com/documentation/services/storage/>
* <https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>
* <https://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/azure-disk-encryption-for-linux-and-windows-virtual-machines-public-preview.aspx>

#### <a name="azure-standard-storage"></a>Azure standard Storage
Az Azure standard Storage az Azure IaaS kiadásakor elérhető tárolási típus. IOPS kvóták lettek kikényszerítve egy lemezen. A tapasztalt késés nem ugyanabban az osztályban található, mint a helyi környezetben üzemeltetett, nagy teljesítményű SAP-rendszerek esetében jellemzően telepített SAN/NAS-eszközök. Az Azure standard Storage azonban elég sok száz SAP-rendszernek bizonyult az Azure-ban üzembe helyezett időpontokban.

Az Azure standard Storage-fiókokon tárolt lemezek számlázása a tárolt tényleges adatok, a tárolási tranzakciók mennyisége, a kimenő adatforgalom és a redundancia beállítás alapján történik. Több lemez is létrehozható az 1 TB-os maximális méretnél, de ha ezek üresen maradnak, díjmentesek. Ha ezt követően kitölt egy virtuális merevlemezt a 100 GB-mel, akkor a 100 GB tárolásért kell fizetnie, nem pedig a által létrehozott VHD-fájl névleges méretéhez.

#### <a name="ff5ad0f9-f7f4-4022-9102-af07aef3bc92"></a>Azure Premium Storage
Az Azure Premium Storage a következő céllal lett bevezetve:

* Jobb I/O-késleltetés.
* Jobb átviteli sebesség.
* Kevésbé változékonyság az I/O-késésben.

Erre a célra számos változás mutatkozott be, amelyek közül a két legjelentősebb a következő:

* SSD-lemezek használata az Azure Storage-csomópontokban
* Új olvasási gyorsítótár, amelyet egy Azure számítási csomópont helyi SSD-je támogat.

A standard szintű tárolóval szemben, ahol a képességek nem változnak a lemez méretétől (vagy VHD) függően, Premium Storage jelenleg három különböző lemez-kategóriával rendelkezik, amelyek a jelen cikkben láthatók:<https://azure.microsoft.com/pricing/details/storage/unmanaged-disks/>

Láthatja, hogy a IOPS/lemez és a lemez átviteli sebessége/lemez a lemezek méretétől függ.

Premium Storage esetén a költséghatékonyság nem az ezekben a lemezekben tárolt tényleges adatmennyiség, hanem a lemez méret kategóriája, a lemezen tárolt adatok mennyiségétől függetlenül.

Emellett olyan Premium Storage lemezeket is létrehozhat, amelyek nem közvetlenül vannak leképezve a méret kategóriába. Ez lehet a helyzet, különösen, ha lemezeket másol a standard szintű tárolóból a Premium Storageba. Ilyen esetekben a rendszer a következő legnagyobb Premium Storage lemezre való leképezést hajtja végre.

Az SAP-tanúsítvánnyal rendelkező Azure VM-családok többsége képes együttműködni Premium Storage és az Azure standard és a Premium Storage közötti keverékkel.

Ha a [jelen cikkben (Linux)][virtual-machines-sizes-linux] és a [jelen cikkben (Windows)][virtual-machines-sizes-windows]lévő DS sorozatú virtuális gépek részét szeretné kipróbálni, a rendszer a virtuális gép szintjének Premium Storage lemezekre vonatkozó adatmennyiség-korlátozásokat is figyelembe veszi. A különböző DS-sorozatú vagy GS sorozatú virtuális gépek eltérő korlátozásokkal rendelkeznek a csatlakoztatható adatlemezek számával kapcsolatban. Ezeket a korlátokat a fent említett cikkben is dokumentáljuk. Lényegében azonban azt is jelenti, hogy ha például a 32 x P30 lemezeket egyetlen DS14 virtuális géphez csatlakoztatja, nem tudja lekérni a 32 x-et egy P30-lemez maximális átviteli sebességével. Ehelyett a virtuálisgép-szinten a maximális átviteli sebesség a cikkben ismertetett módon korlátozza az adatok átviteli sebességét.

A Premium Storageről további információt itt találhat:<https://azure.microsoft.com/blog/2015/04/16/azure-premium-storage-now-generally-available-2>

#### <a name="azure-storage-accounts"></a>Azure-tárfiókok

Szolgáltatások vagy virtuális gépek Azure-beli üzembe helyezése esetén a VHD-k és a virtuálisgép-lemezképek üzembe helyezése az Azure Storage-fiókok nevű egységekben is megszervezhető. Az Azure-beli üzembe helyezés tervezése során alaposan meg kell fontolnia az Azure korlátozásait. Az Azure-előfizetések esetében az egyik oldalon korlátozott számú Storage-fiók található. Bár az egyes Azure Storage-fiókok nagy mennyiségű VHD-fájlt tárolhatnak, a teljes IOPS rögzített korlát van. Több száz olyan SAP virtuális gép üzembe helyezése során, amelyekben jelentős IO-hívásokat hoznak létre az adatbázis-kezelő rendszerekkel, ajánlott nagy IOPS adatbázis-kezelő virtuális gépeket terjeszteni több Azure Storage-fiók között. Ügyelni kell arra, hogy ne lépje túl az Azure Storage-fiókok aktuális korlátját előfizetésben. Mivel a tárterület az adatbázis központi telepítésének létfontosságú része egy SAP-rendszer számára, ezt a koncepciót a már hivatkozott adatbázis- [kezelő üzembe helyezési útmutatójában][dbms-guide]részletesebben tárgyaljuk.

Az Azure Storage-fiókokról további információt ebben a [cikkben][storage-scalability-targets]találhat. A cikk elolvasása során kiderül, hogy az Azure standard Storage-fiókok és a Premium Storage-fiókok korlátai között különbségek vannak. A fő különbségek az ilyen Storage-fiókban tárolható adatmennyiség. A standard szintű tárolóban a kötet mérete nagyobb, mint Premium Storage. A másik oldalon a standard Storage-fiók szigorúan korlátozott a IOPS (lásd az oszlop **teljes kérelmek arányát**), míg az Azure Premium Storage fióknak nincs ilyen korlátozása. Ezen eltérések részleteit és eredményeit az SAP-rendszerek, különösen az adatbázis-kezelő kiszolgálók központi telepítései kapcsán tárgyaljuk.

A Storage-fiókon belül lehetősége van különböző tárolók létrehozására a különböző virtuális merevlemezek rendszerezése és kategorizálása céljából. Ezek a tárolók például a különböző virtuális gépek különálló virtuális merevlemezeit használják. Egyetlen Azure Storage-fiók alatt egyetlen tároló vagy több tároló használata nem jelent teljesítménybeli hatást.

Az Azure-ban a virtuális merevlemez neve a következő elnevezési kapcsolatok alapján kell megadnia az Azure-ban található VHD egyedi nevét:

    http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>

A fenti sztringnek egyedi módon kell azonosítania az Azure Storage-ban tárolt virtuális merevlemezt.

#### <a name="c55b2c6e-3ca1-4476-be16-16c81927550f"></a>Managed Disks

A Managed Disks Azure Resource Manager új erőforrástípus, amely az Azure Storage-fiókokban tárolt virtuális merevlemezek helyett használható. Managed Disks automatikusan összehangolja a virtuális gép rendelkezésre állási csoportját, amelyhez csatolva van, így növelheti a virtuális gép és a virtuális gépen futó szolgáltatások rendelkezésre állását. További információért olvassa el az áttekintő [cikket](https://docs.microsoft.com/azure/storage/storage-managed-disks-overview).

Javasoljuk, hogy felügyelt lemezt használjon, mivel leegyszerűsítik a virtuális gépek üzembe helyezését és felügyeletét.
Az SAP jelenleg csak a prémium szintű Managed Disks támogatja. További információ: SAP Note [1928533].

#### <a name="microsoft-azure-storage-resiliency"></a>Microsoft Azure Storage rugalmasság

Microsoft Azure Storage tárolja az alap VHD-t (operációs rendszer) és a csatlakoztatott lemezeket vagy blobokat legalább három különálló tárolási csomóponton. Ezt a tényt helyi redundáns tárolónak (LRS) nevezzük. A LRS alapértelmezés szerint az Azure-ban található összes tárolási típushoz használható. 

Több redundancia-módszer is rendelkezésre áll, amelyek mindegyike az [Azure Storage-replikáció](https://docs.microsoft.com/azure/storage/common/storage-redundancy?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)című cikkben található.

> [!NOTE]
>Az Azure Premium Storage-től kezdve, amely az adatbázis-kezelő virtuális gépekhez és az adatbázis-és a naplófájl-visszaállítási fájlok tárolására szolgáló lemezek ajánlott tárolási típusa, az egyetlen elérhető módszer a LRS. Ennek eredményeképpen konfigurálnia kell az adatbázis-metódusokat, például SQL Server always on, Oracle-vagy HANA-rendszerreplikációt, hogy az adatbázis-adatreplikáció egy másik Azure-régióban vagy egy másik Azure-beli rendelkezésre állási zónában legyen.


> [!NOTE]
> Az adatbázis-kezelők üzembe helyezéséhez az Azure standard Storage szolgáltatással elérhető geo redundáns tároló használata nem ajánlott, mivel súlyos teljesítménnyel kapcsolatos hatással van, és nem veszi figyelembe a virtuális géphez csatlakoztatott különböző virtuális merevlemezek írási sorrendjét. Abban a tényben, hogy a különböző virtuális merevlemezek írási sorrendjét nem kell megbecsülni, magas a valószínűsége annak, hogy a replikálási cél oldalán lévő inkonzisztens adatbázisok véget lépjenek, ha az adatbázis és a napló/ismétlés fájlok több VHD-ben (például a legtöbb esetben) oszlanak meg a forrás virtuális gép oldalán.


### <a name="61678387-8868-435d-9f8c-450b2424f5bd"></a>Microsoft Azure hálózatkezelés

A Microsoft Azure hálózati infrastruktúrát biztosít, amely lehetővé teszi az SAP szoftverrel megvalósítani kívánt forgatókönyvek leképezését. A képességek a következők:

* Hozzáférés kívülről, közvetlenül a virtuális gépekhez Windows Terminal Services vagy SSH/VNC használatával
* Hozzáférés a szolgáltatásokhoz és a virtuális gépeken belüli alkalmazások által használt konkrét portokhoz
* Belső kommunikáció és névfeloldás az Azure virtuális gépekként üzembe helyezett virtuális gépek csoportja között
* Létesítmények közötti kapcsolat az ügyfél helyszíni hálózata és az Azure-hálózat között
* Több Azure-régió vagy adatközpont-kapcsolat az Azure-helyek között

További információt itt találhat: <https://azure.microsoft.com/documentation/services/virtual-network/>

Az Azure-ban számos különböző lehetőség van a név és az IP-megoldás konfigurálására. Létezik egy Azure DNS szolgáltatás is, amely a saját DNS-kiszolgáló beállítása helyett használható. További információt ebben a [cikkben][virtual-networks-manage-dns-in-vnet] és [ezen a lapon](https://azure.microsoft.com/services/dns/)találhat.

A létesítmények közötti vagy hibrid forgatókönyvek esetében arra a tényre támaszkodunk, hogy a helyszíni AD/OpenLDAP/DNS-t VPN-en vagy privát kapcsolaton keresztül bővítettük az Azure-ban. Az itt dokumentált bizonyos helyzetekben szükség lehet az Azure-ban telepített AD/OpenLDAP-replikára.

Mivel a Hálózatkezelés és a névfeloldás létfontosságú részét képezi az adatbázis-telepítésnek egy SAP-rendszer számára, ezt a koncepciót részletesebben ismertetjük az adatbázis- [kezelő üzembe helyezési útmutatójában][dbms-guide].

##### <a name="azure-virtual-networks"></a>Azure virtuális hálózatok

Egy Azure-Virtual Network létrehozásával megadhatja az Azure DHCP funkciói által lefoglalt magánhálózati IP-címek címtartományt. A létesítmények közötti forgatókönyvekben a definiált IP-címtartomány továbbra is a DHCP által az Azure használatával van lefoglalva. A tartománynév-feloldás azonban a helyszínen történik (feltételezve, hogy a virtuális gépek egy helyszíni tartomány részei), ezért a különböző Azure-Cloud Serviceseken túli címek feloldására is képes.

Az Azure-ban minden virtuális gépnek csatlakoznia kell egy Virtual Networkhoz.

További részleteket ebben a [cikkben][resource-groups-networking] és [ezen a lapon](https://azure.microsoft.com/documentation/services/virtual-network/)találhat.


> [!NOTE]
> Alapértelmezés szerint a virtuális gép üzembe helyezésekor a Virtual Network konfiguráció nem módosítható. A TCP/IP-beállításokat az Azure DHCP-kiszolgálón kell hagyni. Az alapértelmezett viselkedés dinamikus IP-hozzárendelés.
>
>

A virtuális hálózati kártya MAC-címe változhat, például az átméretezés után, a Windows vagy Linux vendég operációs rendszer pedig az új hálózati kártyát választja, és automatikusan DHCP használatával rendeli hozzá az IP-és DNS-címeket ebben az esetben.

##### <a name="static-ip-assignment"></a>Statikus IP-hozzárendelés
Az Azure-Virtual Networkon belüli virtuális gépekhez rögzített vagy fenntartott IP-címeket lehet hozzárendelni. Ha a virtuális gépeket egy Azure-Virtual Network futtatja, akkor nagy lehetőséget nyújt a funkció kihasználására, ha szükséges vagy szükséges bizonyos forgatókönyvekhez. Az IP-hozzárendelés a virtuális gép létezése alatt marad, függetlenül attól, hogy a virtuális gép fut-e vagy le van-e állítva. Ennek eredményeképpen a virtuális gépek (futó és leállított virtuális gépek) teljes számát figyelembe kell venni a Virtual Network IP-címeinek meghatározásakor. Az IP-cím csak akkor lesz hozzárendelve, ha a virtuális gép és annak hálózati adaptere törölve lett, vagy amíg az IP-címet újra hozzá nem rendeli. További információért olvassa el [ezt a cikket][virtual-networks-static-private-ip-arm-pportal].

> [!NOTE]
> Statikus IP-címeket kell hozzárendelni az Azure-ban az egyes Vnic. Ne rendeljen statikus IP-címeket a vendég operációs rendszeren belül egy vNIC. Bizonyos Azure-szolgáltatások, például a Azure Backup-szolgáltatás arra támaszkodnak, hogy legalább az elsődleges vNIC DHCP-re van beállítva, és nem statikus IP-címekre. Tekintse meg az [Azure-beli virtuális gépek biztonsági mentését](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking)ismertető dokumentumot is.
>
>

##### <a name="multiple-nics-per-vm"></a>Több hálózati adapter/virtuális gép

Az Azure-beli virtuális gépekhez több virtuális hálózati adaptert (vNIC) is meghatározhat. Ha több Vnic is rendelkezik, megkezdheti a hálózati forgalom elkülönítésének beállítását, ahol például az ügyfél forgalmának továbbítása egy vNIC történik, a háttérbeli forgalom pedig egy második vNIC. A virtuális gép típusától függően különböző korlátozások vonatkoznak a Vnic számára. A részletes adatok, funkciók és korlátozások a következő cikkekben találhatók:

* [Több hálózati adapterrel rendelkező Windows rendszerű virtuális gép létrehozása][virtual-networks-multiple-nics-windows]
* [Linux rendszerű virtuális gép létrehozása több hálózati adapterrel][virtual-networks-multiple-nics-linux]
* [Több hálózati adapteres virtuális gép üzembe helyezése sablon használatával][virtual-network-deploy-multinic-arm-template]
* [Több hálózati adapteres virtuális gép üzembe helyezése a PowerShell használatával][virtual-network-deploy-multinic-arm-ps]
* [Több hálózati adapteres virtuális gép üzembe helyezése az Azure CLI használatával][virtual-network-deploy-multinic-arm-cli]

#### <a name="site-to-site-connectivity"></a>Helyek közötti kapcsolat

A telephelyek között az Azure-beli virtuális gépek, valamint a helyszíni, átlátható és állandó VPN-kapcsolattal összekapcsolt környezetek. Az Azure-ban várhatóan az SAP üzembe helyezésének leggyakoribb mintája lesz. Feltételezhető, hogy az Azure-beli SAP-példányokkal kapcsolatos működési eljárások és folyamatok transzparens módon működnek. Ez azt jelenti, hogy ki kell tudnia nyomtatni ezeket a rendszereket, és az SAP Transport Management System (TMS) használatával az Azure-ban egy olyan tesztelési rendszerbe helyezheti a változásokat, amely a helyszínen üzemel. [Ebben a cikkben][vpn-gateway-create-site-to-site-rm-powershell] további dokumentáció található a helyek közötti helyről

##### <a name="vpn-tunnel-device"></a>VPN-alagút eszköz

Helyek közötti kapcsolat létrehozásához (helyszíni adatközpontból az Azure-adatközpontba) be kell szereznie és konfigurálnia kell egy VPN-eszközt, vagy az Útválasztás és távelérés szolgáltatást (RRAS) kell használnia, amely a Windows Server 2012-es szoftver-összetevőként lett bevezetve.

* [Virtuális hálózat létrehozása helyek közötti VPN-kapcsolattal a PowerShell használatával][vpn-gateway-create-site-to-site-rm-powershell]
* [Információk a helyek közötti VPN Gateway-kapcsolatok VPN-eszközeiről][vpn-gateway-about-vpn-devices]
* [VPN Gateway – gyakori kérdések][vpn-gateway-vpn-faq]

![Helyek közötti kapcsolat a helyszíni és az Azure között][planning-guide-figure-600]

A fenti ábra két Azure-előfizetést mutat be az Azure-beli virtuális hálózatokban való használatra fenntartott IP-címek altartományával. A helyszíni hálózatról az Azure-ra való kapcsolódás VPN-kapcsolaton keresztül történik.

#### <a name="point-to-site-vpn"></a>Pont – hely VPN

A pont – hely típusú VPN-hez minden ügyfélszámítógépnek csatlakoznia kell a saját VPN-hez az Azure-ban. Az SAP-forgatókönyvek esetében a pont – hely kapcsolat nem praktikus. Ezért a pont – hely VPN-kapcsolathoz nem kapnak további referenciákat.

További információt itt találhat
* [Pont–hely kapcsolat konfigurálása virtuális hálózat számára az Azure Portalon](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)
* [Pont–hely kapcsolat konfigurálása virtuális hálózathoz a PowerShell segítségével](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)

#### <a name="multi-site-vpn"></a>Többhelyes VPN

Az Azure napjainkban is lehetővé teszi, hogy több helyből álló VPN-kapcsolatot hozzon létre egy Azure-előfizetéshez. Korábban egyetlen előfizetés egy helyek közötti VPN-kapcsolatra korlátozódott. Ez a korlátozás a többhelyes VPN-kapcsolatokkal együtt egyetlen előfizetéshez lett elmentve. Ez lehetővé teszi, hogy több Azure-régiót is kihasználjon egy adott előfizetéshez a létesítmények közötti konfigurációkon keresztül.

További dokumentációt [ebben a cikkben talál.][vpn-gateway-create-site-to-site-rm-powershell]

#### <a name="vnet-to-vnet-connection"></a>VNet – VNet-kapcsolatok

A többhelyes VPN használata esetén külön Azure-Virtual Network kell konfigurálnia az egyes régiókban. Gyakran azonban követelmény, hogy a különböző régiókban lévő szoftver-összetevők kommunikáljanak egymással. Ideális esetben ez a kommunikáció nem irányítható át az egyik Azure-régióból a helyi környezetbe, és onnan a másik Azure-régióba. A parancsikonhoz az Azure lehetővé teszi, hogy az egyik régióban lévő Azure-Virtual Network egy másik régióban üzemeltetett Azure-Virtual Network is konfiguráljon kapcsolatokat. Ennek a funkciónak a neve VNet – VNet-kapcsolatok. A funkcióról további részleteket itt találhat: <https://azure.microsoft.com/documentation/articles/vpn-gateway-vnet-vnet-rm-ps/>.

#### <a name="private-connection-to-azure-expressroute"></a>Magánhálózati kapcsolódás az Azure ExpressRoute

Microsoft Azure ExpressRoute lehetővé teszi az Azure-adatközpontok és az ügyfél helyszíni infrastruktúrája, illetve a közös elhelyezésű környezet közötti magánhálózati kapcsolatok létrehozását. A ExpressRoute különböző MPLS (csomagkapcsolt) VPN-szolgáltatók vagy más hálózati szolgáltatók kínálják. Az ExpressRoute-kapcsolatok nem a nyilvános interneten haladnak át. A ExpressRoute-kapcsolatok nagyobb biztonságot, nagyobb megbízhatóságot és több párhuzamos áramkört, gyorsabb sebességet és kisebb késést biztosítanak, mint az interneten keresztüli szokásos kapcsolatok.

Az Azure-ExpressRoute és-ajánlatokról itt talál további információt:

* <https://azure.microsoft.com/documentation/services/expressroute/>
* <https://azure.microsoft.com/pricing/details/expressroute/>
* <https://azure.microsoft.com/documentation/articles/expressroute-faqs/>

Az Express Route több Azure-előfizetést is lehetővé tesz egy ExpressRoute áramkörön az itt dokumentált módon.

* <https://azure.microsoft.com/documentation/articles/expressroute-howto-linkvnet-arm/>
* <https://azure.microsoft.com/documentation/articles/expressroute-howto-circuit-arm/>

#### <a name="forced-tunneling-in-case-of-cross-premises"></a>Kényszerített bújtatás a létesítmények közötti
A helyszíni tartományokhoz helyek közötti, pont – hely vagy ExpressRoute keresztül csatlakozó virtuális gépek esetében meg kell győződnie arról, hogy az internetes proxy beállításai üzembe helyezése az adott virtuális gépek összes felhasználója számára is megtörténik. Alapértelmezés szerint az olyan virtuális gépeken vagy felhasználókon futó szoftverek, amelyek böngészővel férnek hozzá az internethez, nem haladnak át a vállalati proxyn, hanem egyenesen az Azure-on keresztül csatlakoznak az internethez. A proxy beállítás azonban még nem 100%-os megoldás a vállalati proxyn keresztüli forgalom irányítására, mivel a proxyt a szoftverek és szolgáltatások feladata alapján kell megkeresni. Ha a virtuális gépen futó szoftverek nem ezt a műveletet végzik, vagy a rendszergazda kezeli a beállításokat, az internetre irányuló forgalom közvetlenül az Azure-on keresztül érhető el az interneten keresztül.

A közvetlen internetkapcsolat elkerülése érdekében a helyszíni és az Azure közötti helyek közötti kapcsolattal kényszerített bújtatást is beállíthat. Itt jelennek meg a kényszerített bújtatási szolgáltatás részletes leírása<https://azure.microsoft.com/documentation/articles/vpn-gateway-forced-tunneling-rm/>

A kényszerített bújtatást a ExpressRoute-mel engedélyezte az ügyfelek, akik az alapértelmezett útvonalat hirdetik meg a ExpressRoute BGP-társi munkameneteken keresztül.

#### <a name="summary-of-azure-networking"></a>Az Azure hálózatkezelésének összefoglalása

Ez a fejezet az Azure hálózatkezelésének számos fontos pontját foglalta magába. Itt látható a főbb pontok összegzése:

* Az Azure Virtual Networks lehetővé teszi egy hálózati struktúra üzembe helyezését az Azure-beli üzembe helyezésben. A virtuális hálózatok el lehet különíteni egymással, vagy a hálózati biztonsági csoportok virtuális hálózatok közötti adatforgalmat szabályozhatja.
* Az Azure Virtual Networks szolgáltatással IP-címtartományok rendelhetők virtuális gépekhez, vagy rögzített IP-címeket rendelhetek hozzá virtuális gépekhez
* Helyek közötti vagy pont – hely kapcsolat beállításához először létre kell hoznia egy Azure-Virtual Network
* A virtuális gép üzembe helyezése után már nem lehet módosítani a virtuális géphez hozzárendelt Virtual Network

### <a name="quotas-in-azure-virtual-machine-services"></a>Kvóták az Azure Virtual Machine Servicesben
Tisztázni kell azt a tényt, hogy a tárolási és hálózati infrastruktúrát az Azure-infrastruktúra különböző szolgáltatásait futtató virtuális gépek között osztják meg. Csakúgy, mint az ügyfél saját adatközpontjai esetében, az infrastruktúra egyes erőforrásainak túlzott kiépítését a rendszer egy bizonyos fokig kiépítve végzi. A Microsoft Azure platform lemez-, CPU-, hálózati és egyéb kvótákat használ az erőforrások felhasználásának korlátozására és a konzisztens és determinisztikus teljesítmény megőrzésére.  A különböző virtuálisgép-típusok (a5, A6 stb.) eltérő kvótával rendelkeznek a lemezek, a processzor, a RAM és a hálózat számára.

> [!NOTE]
> Az SAP által támogatott virtuálisgép-típusok processzor-és memória-erőforrásai előre le vannak foglalva a gazdagép csomópontjain. Ez azt jelenti, hogy a virtuális gép üzembe helyezése után a gazdagépen lévő erőforrások a virtuális gép típusa által meghatározott módon érhetők el.
>
>

Az Azure-megoldások SAP-tervezése és méretezése során figyelembe kell venni az egyes virtuálisgép-méretek kvótáit. A virtuális gépek kvótái itt vannak leírva [(Linux)][virtual-machines-sizes-linux] és [itt (Windows)][virtual-machines-sizes-windows].

Az ismertetett kvóták az elméleti maximális értékeket jelölik.  A IOPS korlátja csak kis IOs-sel (8 kb) érhető el, de lehetséges, hogy a nagyméretű IOs (1Mb) nem érhető el.  Az IOPS korlátja az egy lemez részletességére van kényszerítve.

Durva döntési fában döntse el, hogy az SAP-rendszer megfelel-e az Azure Virtual Machine Services szolgáltatásnak és képességeinek, vagy hogy a rendszer az Azure-ban való üzembe helyezéséhez másképp kell-e konfigurálni egy meglévő rendszer használatát:

![Döntési fa az SAP Azure-beli üzembe helyezési képességének eldöntéséhez][planning-guide-figure-700]

**1. lépés**: A legfontosabb tudnivalók az SAP-rendszerhez szükséges SAP-követelmények. Az SAP-követelményeket el kell különíteni az adatbázis-kezelő és az SAP-alkalmazás részbe, még akkor is, ha az SAP-rendszer már telepítve van a helyszínen egy kétrétegű konfigurációban. A meglévő rendszerek esetében a használatban lévő hardverhez kapcsolódó SAP-t gyakran a meglévő SAP-referenciaértékek alapján lehet meghatározni vagy megbecsülni. Az eredmények itt találhatók: <https://sap.com/about/benchmark.html>.
Az újonnan telepített SAP-rendszerek esetében egy méretezési gyakorlattal kell rendelkezni, amelynek meg kell határoznia a rendszer SAP-követelményeit.
Lásd még ezt a blogot és a csatolt dokumentumot az Azure-beli SAP-méretezéshez:<https://blogs.msdn.com/b/saponsqlserver/archive/2015/12/01/new-white-paper-on-sizing-sap-solutions-on-azure-public-cloud.aspx>

**2. lépés**: A meglévő rendszerek esetében az adatbázis-kezelő kiszolgáló másodpercenkénti I/O-kötetét és I/O-műveleteit kell mérni. Az újonnan tervezett rendszerek esetében az új rendszer méretezési gyakorlatának az adatbázis-kezelői oldalon található I/O-követelményekkel kapcsolatos durva ötleteket is meg kell adni. Ha nem biztos abban, hogy végül a koncepció igazolását kell elvégeznie.

**3. lépés**: Hasonlítsa össze az adatbázis-kezelő kiszolgáló SAP-követelményét az SAP-vel az Azure különböző virtuálisgép-típusai biztosíthatják. A különböző Azure-beli virtuálisgép-típusok SAP-vel kapcsolatos információit a [1928533]-es SAP-Megjegyzés dokumentálja. A fókusznak az adatbázis-kezelő virtuális gépen kell lennie, mivel az adatbázis réteg egy olyan SAP NetWeaver-rendszer rétege, amely nem az üzemelő példányok többségében kibővíthető. Ezzel szemben az SAP-alkalmazás rétegét ki lehet bővíteni. Ha a SAP által támogatott Azure-beli virtuálisgép-típusok egyike sem tudja biztosítani a szükséges SAP-t, a tervezett SAP-rendszer munkaterhelése nem futtatható az Azure-ban. Telepítenie kell a rendszer helyi telepítését, vagy módosítania kell a rendszer munkaterhelési kötetét.

**4. lépés**: A jelen dokumentációban [(Linux)][virtual-machines-sizes-linux] és [itt (Windows)][virtual-machines-sizes-windows]az Azure IOPS-kvótát alkalmaz, függetlenül attól, hogy szabványos tárolót vagy Premium Storage használ-e. A virtuálisgép-típustól függően a csatlakoztatható adatlemezek száma változó lehet. Ennek eredményeképpen kiszámíthatja a maximális IOPS-számot, amely a különböző virtuálisgép-típusokkal is megvalósítható. Az adatbázis-fájl elrendezéstől függően a lemezeket a vendég operációs rendszer egyik kötetére lehet leválasztani. Ha azonban egy telepített SAP-rendszer aktuális IOPS-mennyisége meghaladja az Azure-beli legnagyobb virtuálisgép-típus számított korlátait, és ha nincs esély a további memória ellensúlyozására, akkor az SAP-rendszer terhelése jelentősen befolyásolható. Ilyen esetekben elérheti azt a pontot, ahol a rendszer nem helyezhető üzembe az Azure-ban.

**5. lépés**: Különösen az olyan SAP-rendszerek esetében, amelyeket a helyszínen telepítenek a kétrétegű konfigurációkban, az esélye, hogy a rendszernek az Azure-ban kell konfigurálnia egy háromrétegű konfigurációban. Ebben a lépésben ellenőriznie kell, hogy van-e olyan összetevő az SAP-alkalmazás rétegében, amely nem méretezhető ki, és amely nem fér bele a processzor és a memória erőforrásaiba a különböző Azure-beli virtuálisgép-típusok ajánlatával. Ha valóban van ilyen összetevő, az SAP-rendszer és annak munkaterhelése nem helyezhető üzembe az Azure-ban. Ha azonban több Azure-beli virtuális gépre is kibővítheti az SAP alkalmazás összetevőit, a rendszer üzembe helyezhető az Azure-ban.

**6. lépés**: Ha az adatbázis-kezelő és az SAP-alkalmazás rétegbeli összetevői futhatnak az Azure-beli virtuális gépeken, a konfigurációt a következő vonatkozásban kell meghatározni:

* Azure-beli virtuális gépek száma
* Az egyes összetevők virtuálisgép-típusai
* Az adatbázis-kezelő virtuális merevlemezek száma, amely elegendő IOPS biztosít

## <a name="managing-azure-assets"></a>Azure-eszközök kezelése

### <a name="azure-portal"></a>Azure Portal

A Azure Portal az Azure-beli virtuális gépek központi telepítésének kezelésére szolgáló három felület egyike. Az alapszintű felügyeleti feladatok, például a virtuális gépek lemezképből való üzembe helyezése a Azure Portal keresztül végezhető el. Emellett a Storage-fiókok, a virtuális hálózatok és az egyéb Azure-összetevők létrehozása is felveszi a feladatokat, a Azure Portal képes jól kezelni. Azonban a virtuális merevlemezek a helyszínről az Azure-ba való feltöltése vagy az Azure-on belüli virtuális merevlemezek másolása is felhasználható, amelyek a PowerShell vagy a parancssori felület használatával vagy harmadik féltől származó eszközöket vagy felügyeletet igényelnek.

![Microsoft Azure Portal – a virtuális gépek áttekintése][planning-guide-figure-800]


A virtuálisgép-példány felügyeleti és konfigurációs feladatai a Azure Portal belül lehetségesek.

A virtuális gépek újraindítása és leállítása mellett a virtuálisgép-példányhoz is csatlakoztathatja, leválaszthatja és létrehozhatja az adatlemezeket, rögzítheti a példányt a lemezkép előkészítéséhez, és beállíthatja a virtuálisgép-példány méretét.

A Azure Portal alapvető funkciókat biztosít a virtuális gépek és számos más Azure-szolgáltatás üzembe helyezéséhez és konfigurálásához. A Azure Portal azonban nem minden rendelkezésre álló funkciót érint. A Azure Portal nem lehetséges olyan feladatokat végrehajtani, mint például:

* Virtuális merevlemezek feltöltése az Azure-ba
* Virtuális gépek másolása


### <a name="management-via-microsoft-azure-powershell-cmdlets"></a>Felügyelet Microsoft Azure PowerShell-parancsmagok használatával

A Windows PowerShell egy hatékony és bővíthető keretrendszer, amelyet az ügyfelek széles körben elfogadnak az Azure-ban több rendszer üzembe helyezésével. Miután telepítette a PowerShell-parancsmagokat egy asztali gépen, laptopon vagy dedikált felügyeleti állomáson, a PowerShell-parancsmagok távolról is futtathatók.

[Ez a cikk][powershell-install-configure]a helyi asztali számítógép/laptop Azure PowerShell-parancsmagok használatára való használatának, valamint az Azure-előfizetésekkel való használathoz való konfigurálásának folyamatát ismerteti.

Az Azure PowerShell-parancsmagok telepítésével, frissítésével és konfigurálásával kapcsolatos részletesebb lépések a [telepítési útmutató ebben][deployment-guide-4.1]a fejezetében is találhatók.

Az eddigi felhasználói élmény az volt, hogy a PowerShell (PS) minden bizonnyal a virtuális gépek üzembe helyezéséhez és a virtuális gépek üzembe helyezéséhez szükséges egyéni lépések létrehozásához használható. Az Azure-beli SAP-példányokat futtató összes ügyfél PS-parancsmagokkal egészíti ki a Azure Portal felügyeleti feladatait, vagy akár csak PS parancsmagokat használ az Azure-ban való üzembe helyezések kezeléséhez. Mivel az Azure-specifikus parancsmagok ugyanazt az elnevezési konvenciót használják, mint a több mint 2000 Windows-hez kapcsolódó parancsmag, ez egy egyszerű feladat, amellyel a Windows-rendszergazdák kihasználhatják ezeket a parancsmagokat.

Lásd itt a példát:<https://blogs.technet.com/b/keithmayer/archive/2015/07/07/18-steps-for-end-to-end-iaas-provisioning-in-the-cloud-with-azure-resource-manager-arm-powershell-and-desired-state-configuration-dsc.aspx>


Az SAP-hez készült Azure monitorozási bővítmény üzembe helyezése (lásd a jelen dokumentum [Azure monitoring-megoldás SAP][planning-guide-9.1] -ban című szakaszát) csak a PowerShell vagy a CLI használatával lehetséges. Ezért a PowerShell vagy a parancssori felület beállítása és konfigurálása szükséges az Azure-beli SAP NetWeaver-rendszerek telepítésekor vagy felügyeletekor.  

Mivel az Azure további funkciókat biztosít, új PS-parancsmagokat kell hozzáadnia, amelyek a parancsmagok frissítését igénylik. Ezért érdemes az Azure letöltési webhelyét legalább egyszer <https://azure.microsoft.com/downloads/> megkeresni a parancsmagok új verziójához. Az új verzió a régebbi verzióra van telepítve.

Az Azure-hoz kapcsolódó PowerShell-parancsok általános listáját itt tekintheti <https://docs.microsoft.com/powershell/azure/overview>meg:.

### <a name="management-via-microsoft-azure-cli-commands"></a>Felügyelet Microsoft Azure CLI-parancsok használatával

Előfordulhat, hogy a Linux rendszerű és az Azure-erőforrások kezelésére szolgáló PowerShell-t használó ügyfeleinknek nem a lehetőségük. A Microsoft Alternatív megoldásként kínálja az Azure CLI-t.
Az Azure CLI nyílt forráskódú, platformfüggetlen parancsokat biztosít az Azure platformmal való munkavégzéshez. Az Azure CLI számos, a Azure Portal található funkcióval rendelkezik.

További információ a telepítéséről, konfigurálásáról és a CLI-parancsok Azure-feladatok végrehajtásához való használatáról:

* [A klasszikus Azure CLI telepítése][xplat-cli]
* [Telepítését és kezelését a virtuális gépek Azure Resource Manager-sablonok és az Azure parancssori felület használatával][../../linux/create-ssh-secured-vm-from-template.md]
* [A klasszikus Azure CLI használata Mac, Linux és Windows rendszerhez Azure Resource Manager][xplat-cli-azure-resource-manager]

Olvassa el [][planning-guide] a [Linux rendszerű virtuális gépekhez készült Azure CLI][deployment-guide-4.5.2] című témakört is, amely bemutatja, hogyan használhatja az Azure CLI-t az SAP-hez készült Azure monitoring bővítmény üzembe helyezéséhez.

## <a name="different-ways-to-deploy-vms-for-sap-in-azure"></a>Az Azure-beli virtuális gépek üzembe helyezésének különböző módjai

Ebben a fejezetben megismerheti a virtuális gépek Azure-ban való üzembe helyezésének különböző módjait. Ez a fejezet további előkészítési eljárásokat, valamint a virtuális merevlemezek és virtuális gépek Azure-beli kezelését ismerteti.

### <a name="deployment-of-vms-for-sap"></a>Virtuális gépek üzembe helyezése az SAP-ban

Microsoft Azure több módszert kínál a virtuális gépek és a kapcsolódó lemezek üzembe helyezésére. Ezért fontos megérteni a különbségeket, mivel a virtuális gépek előkészítése a telepítés módjától függően eltérő lehet. Általánosságban elmondjuk a következő forgatókönyveket:

#### <a name="4d175f1b-7353-4137-9d2f-817683c26e53"></a>Virtuális gép áthelyezése a helyszínről az Azure-ba egy nem általánosított lemezzel

Egy adott SAP-rendszer áthelyezését tervezi a helyszínről az Azure-ba. Ezt úgy teheti meg, hogy feltölti a virtuális merevlemezt, amely tartalmazza az operációs rendszer, az SAP bináris fájljait és az adatbázis-kezelő bináris fájljait, valamint a virtuális merevlemezeket, amelyek az adatbázis-kezelő és az Azure-ba Az [alább #2 forgatókönyvhöz][planning-guide-5.1.2]képest a helyi környezetben konfigurált ÁLLOMÁSNÉV, SAP SID és SAP felhasználói fiókok is megmaradnak az Azure-beli virtuális gépen. Ezért a rendszerkép általánosítása nem szükséges. A helyszíni előkészítési lépések és a nem általánosított virtuális gépek vagy virtuális merevlemezek Azure-ba való feltöltésének előkészítési fejezeteiben lásd: [felkészülés a virtuális gép helyszíni rendszerből az Azure-][planning-guide-5.2.1] ba történő áthelyezésére. A 3 [. forgatókönyv olvasása: Egy virtuális gép helyszíni áthelyezése egy nem általánosított Azure VHD-vel, SAP][deployment-guide-3.4] használatával a [telepítési útmutatóban][deployment-guide] az ilyen lemezképek Azure-beli üzembe helyezésének részletes lépéseihez.

#### <a name="e18f7839-c0e2-4385-b1e6-4538453a285c"></a>Virtuális gép üzembe helyezése egy ügyfél-specifikus képpel

Az operációs rendszer vagy az adatbázis-kezelő verziója adott javítási követelményei miatt előfordulhat, hogy az Azure piactéren megadott lemezképek nem felelnek meg az igényeinek. Ezért előfordulhat, hogy létre kell hoznia egy virtuális gépet a saját privát operációs rendszer/adatbázis-kezelő virtuális gép rendszerképével, amely később többször is üzembe helyezhető. A privát rendszerképek ismétlődésre való előkészítéséhez a következő elemeket kell figyelembe venni:

---
> ![Windows][Logo_Windows] Windows
>
> További részletek itt találhatók: <https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed>A Windows-beállításokat (például a Windows SID-t és az állomásnévt) a Sysprep parancs használatával kell absztrakt/általánosítani a helyszíni virtuális gépen.
>
>
> ![Linux][Logo_Linux] Linux
>
> A virtuális merevlemezek Azure-ba való feltöltésének előkészítéséhez kövesse a következő cikkben ismertetett lépéseket: [SUSE][virtual-machines-linux-create-upload-vhd-suse], [Red Hat][virtual-machines-linux-redhat-create-upload-vhd]vagy [Oracle Linux][virtual-machines-linux-create-upload-vhd-oracle].
>
>

---
Ha már telepítette az SAP-tartalmakat a helyszíni virtuális gépen (különösen a kétrétegű rendszerek esetében), az SAP-rendszerbeállítások az Azure-beli virtuális gép üzembe helyezése után az SAP szoftver kiépítési kezelője által támogatott példány-átnevezési eljárással módosíthatók (SAP [1619720]. megjegyzés). Tekintse meg a következő fejezeteket: [felkészülés a virtuális gép üzembe helyezésére egy ügyfél-specifikus rendszerképpel az SAP][planning-guide-5.2.2] -hez, és a helyszíni és az Azure [-][planning-guide-5.3.2] ba történő feltöltését is feltölthetik a jelen dokumentumból az Azure-ba. A 2 [. forgatókönyv olvasása: Egy virtuális gép üzembe helyezése az SAP][deployment-guide-3.3] -hez készült egyéni rendszerképekkel a [telepítési útmutatóban][deployment-guide] az ilyen lemezképek Azure-beli üzembe helyezésének részletes lépéseihez.

#### <a name="deploying-a-vm-out-of-the-azure-marketplace"></a>Virtuális gép üzembe helyezése az Azure Marketplace-en

A virtuális gép üzembe helyezéséhez Microsoft vagy harmadik féltől származó virtuálisgép-lemezképet szeretne használni az Azure piactéren. Miután üzembe helyezte a virtuális gépet az Azure-ban, ugyanezeket az irányelveket és eszközöket követve telepítse az SAP-szoftvert és/vagy az adatbázis-kezelőt a virtuális gépen, ahogy azt egy helyszíni környezetben tenné. Az üzembe helyezés részletes leírását az 1 [. forgatókönyvben tekintheti meg: Virtuális gép üzembe helyezése az Azure piactéren az SAP][deployment-guide-3.2] -hez a [telepítési útmutatóban][deployment-guide].

### <a name="6ffb9f41-a292-40bf-9e70-8204448559e7"></a>Virtuális gépek előkészítése az Azure-beli SAP-vel

Mielőtt virtuális gépeket tölt fel az Azure-ba, meg kell győződnie arról, hogy a virtuális gépek és a virtuális merevlemezek megfelelnek bizonyos követelményeknek. A használt üzembe helyezési módszertől függően kis különbségek vannak.

#### <a name="1b287330-944b-495d-9ea7-94b83aff73ef"></a>A virtuális gép a helyszínről az Azure-ba való áthelyezésének előkészítése nem általánosított lemezzel

Az általános telepítési módszer egy meglévő virtuális gép áthelyezése, amely a helyszíni rendszerből az Azure-ba irányuló SAP-szolgáltatást futtatja. A virtuális gépen és a virtuális gépen lévő SAP rendszernek csak az Azure-ban kell futnia ugyanazzal az állomásnévvel, és valószínűleg ugyanazt az SAP SID-t kell használnia. Ebben az esetben a virtuális gép vendég operációs rendszere nem lehet általánosítva több üzemelő példánynál. Ha a helyszíni hálózat ki lett bővítve az Azure-ba (lásd: az [önálló vagy több SAP virtuális gép üzembe helyezése az Azure][planning-guide-2.2] -ban, a jelen dokumentumban található helyszíni hálózatba való teljes integráció követelménye), akkor is, ha az adott tartományban van. a fiókokat a virtuális gépen lehet használni, mivel azokat a helyszínen használták.

A saját Azure-beli virtuális gép lemezének előkészítéséhez szükséges követelmények a következők:

* Az operációs rendszert tartalmazó virtuális merevlemeznek eredetileg csak a 127GB maximális mérete lehet. Ez a korlátozás a 2015. márciusi időszak végén megszűnt. Az operációs rendszert tartalmazó VHD-k mostantól akár 1 TB méretűek is lehetnek, más Azure Storage-beli virtuális merevlemezek esetében is.
* A fájlnak rögzített VHD formátumban kell lennie. Az Azure-ban a dinamikus VHD-k vagy VHDx-formátumú virtuális merevlemezek még nem támogatottak. A dinamikus VHD-k statikus VHD-re lesznek konvertálva a virtuális merevlemez PowerShell-parancsmagok vagy parancssori felülettel való feltöltésekor
* A virtuális géphez csatlakoztatott virtuális merevlemezeket, amelyeket újra csatlakoztatni kell az Azure-ban a virtuális GÉPHEZ, rögzített VHD-formátumúnak kell lennie. Olvassa el [ezt a cikket (Linux)](../../linux/managed-disks-overview.md) és [Ez a cikk (Windows)](../../windows/managed-disks-overview.md)) az adatlemezek méretének korlátozásához. A dinamikus VHD-k statikus VHD-re lesznek konvertálva a virtuális merevlemez PowerShell-parancsmagok vagy parancssori felülettel való feltöltésekor
* Adjon hozzá egy másik helyi fiókot rendszergazdai jogosultságokkal, amelyeket a Microsoft támogatási szolgálata használhat, vagy amely a szolgáltatásokhoz és alkalmazásokhoz környezetként rendelhető a virtuális gép üzembe helyezése és a megfelelő felhasználók használata érdekében.
* Vegyen fel más helyi fiókokat, amelyek szükségesek lehetnek az adott telepítési forgatókönyvhöz.

---
> ![Windows][Logo_Windows] Windows
>
> Ebben az esetben a virtuális gép az Azure-ban való feltöltéséhez és üzembe helyezéséhez nincs szükség a virtuális gép általánosítására (Sysprep).
> Győződjön meg arról, hogy a meghajtó D:\ nincs használatban.
> Állítsa be a lemez automatikus csatlakoztatását a csatolt lemezekhez a jelen dokumentum [csatolt lemezek automatikus csatlakoztatásának beállítása][planning-guide-5.5.3] című részében leírtak szerint.
>
> ![Linux][Logo_Linux] Linux
>
> Ebben a forgatókönyvben a virtuális gép az Azure-ban való feltöltéséhez és üzembe helyezéséhez nem szükséges az általánosítás (waagent).
> Győződjön meg arról, hogy a/mnt/Resource nincs használatban, és hogy az összes lemez az UUID használatával van csatlakoztatva. Az operációsrendszer-lemez esetében ügyeljen arra, hogy a rendszerbetöltő bejegyzés az UUID-alapú csatlakoztatást is tükrözze.
>
>

---
#### <a name="57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3"></a>A virtuális gép üzembe helyezésének előkészítése az SAP-hez készült ügyfél-specifikus rendszerképpel

Az általánosított operációs rendszert tartalmazó VHD-fájlokat tárolók tárolják az Azure Storage-fiókokban vagy felügyelt lemezképként. Egy új virtuális gépet központilag telepíthet az adott lemezképből úgy, hogy a virtuális merevlemez vagy a felügyelt lemez képére hivatkozik a telepítési sablon fájljaiban [lévő forrásként, a 2. forgatókönyvben leírtak szerint. Virtuális gép üzembe helyezése egyéni rendszerképpel a][deployment-guide-3.3] [telepítési útmutató][deployment-guide]SAP-hez.

A saját Azure-beli virtuálisgép-rendszerkép előkészítésének követelményei a következők:

* Az operációs rendszert tartalmazó virtuális merevlemeznek eredetileg csak a 127GB maximális mérete lehet. Ez a korlátozás a 2015. márciusi időszak végén megszűnt. Az operációs rendszert tartalmazó VHD-k mostantól akár 1 TB méretűek is lehetnek, más Azure Storage-beli virtuális merevlemezek esetében is.
* A fájlnak rögzített VHD formátumban kell lennie. Az Azure-ban a dinamikus VHD-k vagy VHDx-formátumú virtuális merevlemezek még nem támogatottak. A dinamikus VHD-k statikus VHD-re lesznek konvertálva a virtuális merevlemez PowerShell-parancsmagok vagy parancssori felülettel való feltöltésekor
* A virtuális géphez csatlakoztatott virtuális merevlemezeket, amelyeket újra csatlakoztatni kell az Azure-ban a virtuális GÉPHEZ, rögzített VHD-formátumúnak kell lennie. Olvassa el [ezt a cikket (Linux)](../../windows/managed-disks-overview.md) és [ezt a cikket (Windows)](../../linux/managed-disks-overview.md) az adatlemezek méretének korlátozásához. A dinamikus VHD-k statikus VHD-re lesznek konvertálva a virtuális merevlemez PowerShell-parancsmagok vagy parancssori felülettel való feltöltésekor
* Vegyen fel más helyi fiókokat, amelyek szükségesek lehetnek az adott telepítési forgatókönyvhöz.
* Ha a rendszerkép tartalmazza az SAP NetWeaver telepítését, és az állomásnév átnevezése az eredeti név alapján az Azure-beli központi telepítés pontján, ajánlott az SAP szoftveres kiépítési kezelő DVD legújabb verziójának másolása a sablonba. Ezzel a beállítással egyszerűen használhatja az SAP által megadott átnevezési funkciót a módosított állomásnév és/vagy az SAP-rendszer biztonsági azonosítójának az üzembe helyezett VM-lemezképen belüli módosításához, amint az új másolat elindult.

---
> ![Windows][Logo_Windows] Windows
>
> Győződjön meg arról, hogy a meghajtó D:\ a nem használja a csatlakoztatott lemezek automatikus csatlakoztatásának beállítása a jelen dokumentum [csatolt lemezek automatikus csatlakoztatásának beállítása][planning-guide-5.5.3] című részében leírtak szerint.
>
> ![Linux][Logo_Linux] Linux
>
> Győződjön meg arról, hogy a/mnt/Resource nincs használatban, és hogy az összes lemez az UUID használatával van csatlakoztatva. Az operációsrendszer-lemez esetében ügyeljen arra, hogy a rendszerbetöltő bejegyzés az UUID-alapú csatlakoztatást is tükrözze.
>
>

---
* Az SAP GUI (felügyeleti és beállítási célokra) előre telepíthető egy ilyen sablonban.
* A virtuális gépek a létesítmények közötti forgatókönyvek sikeres futtatásához szükséges egyéb szoftverek telepíthetők, amennyiben ez a szoftver képes működni a virtuális gép átnevezésével.

Ha a virtuális gép az Azure-beli üzembe helyezési forgatókönyvben nem elérhető fiókoktól/felhasználóktól függetlenül is általános, és végül független a fiókoknak és a felhasználóknak, az adott rendszerkép általánosításának utolsó előkészítési lépése.

##### <a name="generalizing-a-vm"></a>Virtuális gép általánosítása
---
> ![Windows][Logo_Windows] Windows
>
> Utolsó lépésként jelentkezzen be egy virtuális gépre egy rendszergazdai fiókkal. Nyisson meg egy Windows-parancssorablakot *rendszergazdaként*. Nyissa meg a%windir%\Windows\System32\Sysprep, és futtassa a Sysprep. exe fájlt.
> Egy kis ablak jelenik meg. Fontos, hogy ellenőrizzék az **általánosítási** beállítást (az alapértelmezett beállítás nincs bejelölve), és az alapértelmezett "újraindítás" értékről a "Leállítás" értékre módosítsa a leállítási lehetőséget. Ez az eljárás azt feltételezi, hogy a Sysprep folyamat a helyszínen fut a virtuális gép vendég operációs rendszerében.
> Ha az eljárást az Azure-ban már futó virtuális géppel szeretné elvégezni, kövesse a [jelen cikkben](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)ismertetett lépéseket.
>
> ![Linux][Logo_Linux] Linux
>
> [Linuxos virtuális gép rögzítése Resource Manager-sablonként][capture-image-linux-step-2-create-vm-image]
>
>

---
### <a name="transferring-vms-and-vhds-between-on-premises-to-azure"></a>Virtuális gépek és virtuális merevlemezek átvitele a helyszínről az Azure-ba
Mivel a virtuálisgép-lemezképek és-lemezek az Azure-ba való feltöltése nem lehetséges a Azure Portalon keresztül, Azure PowerShell parancsmagokat vagy parancssori felületet kell használnia. Egy másik lehetőség a "AzCopy" eszköz használata. Az eszköz átmásolhatja a virtuális merevlemezeket a helyszíni és az Azure között (mindkét irányban). Emellett az Azure-régiók között is másolhatja a virtuális merevlemezeket. A AzCopy letöltéséhez és használatához tekintse meg [ezt a dokumentációt][storage-use-azcopy] .

A harmadik alternatíva a különböző külső gyártótól származó GUI-alapú eszközök használata. Azonban győződjön meg arról, hogy ezek az eszközök támogatják az Azure Page blobokat. Ehhez az Azure Page blob Store-t kell használnia (a különbségek itt vannak leírva: <https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>). Az Azure által biztosított eszközök is hatékonyan tömörítik a virtuális gépeket és a virtuális merevlemezeket, amelyeket fel kell tölteni. Ez azért fontos, mert a tömörítésnek ez a hatékonysága csökkenti a feltöltési időt (ami egyébként az internetre a helyszíni létesítményből és az Azure-beli üzembe helyezési régióból származó feltöltési hivatkozástól függően változhat). Ez egy valós feltételezés, hogy egy virtuális gépet vagy virtuális merevlemezt az Európai helyről az Egyesült államokbeli Azure-adatközpontba tölt fel, hosszabb ideig tart, mint az Európai Azure-adatközpontokban.

#### <a name="a43e40e6-1acc-4633-9816-8f095d5a7b6a"></a>Virtuális merevlemez feltöltése a helyszínről az Azure-ba
Ha egy meglévő virtuális gépet vagy virtuális MEREVLEMEZt szeretne feltölteni a helyszíni hálózatból, például egy virtuális gépről vagy virtuális merevlemezről, meg kell felelnie a (z) című fejezet a [virtuális gép helyszíniről Azure-][planning-guide-5.2.1] ba való áthelyezésének előkészítésével kapcsolatos követelményeknek, a jelen dokumentum nem általánosított lemezével.

Az ilyen virtuális gépeket nem kell általánosítani, és a helyszíni oldalon való leállítás után fel lehet tölteni az állapotba és az alakzatba. Ugyanez érvényes a további VHD-k esetében is, amelyek nem tartalmaznak operációs rendszert.

##### <a name="uploading-a-vhd-and-making-it-an-azure-disk"></a>VHD feltöltése és Azure-lemez készítése
Ebben az esetben egy virtuális merevlemezt kell feltöltenie az operációs rendszersel vagy anélkül, és csatlakoztatni kell egy virtuális géphez adatlemezként, vagy az operációs rendszer lemezként kell használni. Ez egy többlépéses folyamat

**PowerShell**

* Jelentkezzen be az előfizetésbe a *AzAccount*
* Állítsa be a környezet előfizetését a *set-AzContext* és a paraméter SubscriptionId vagy SubscriptionName – lásd:<https://docs.microsoft.com/powershell/module/az.accounts/set-Azcontext>
* Töltse fel a VHD-t az *Add-AzVhd* használatával egy Azure Storage-fiókba – lásd:<https://docs.microsoft.com/powershell/module/az.compute/add-Azvhd>
* Választható Hozzon létre egy felügyelt lemezt a VHD-ről a *New-AzDisk* -lásd:<https://docs.microsoft.com/powershell/module/az.compute/new-Azdisk>
* Állítsa be egy új virtuálisgép-konfiguráció operációsrendszer-lemezét a VHD-re vagy a felügyelt lemezre a *set-AzVMOSDisk* -lásd:<https://docs.microsoft.com/powershell/module/az.compute/set-Azvmosdisk>
* Hozzon létre egy új virtuális gépet a VM-konfigurációból a *New-AzVM* -lásd:<https://docs.microsoft.com/powershell/module/az.compute/new-Azvm>
* Adatlemez hozzáadása egy új virtuális géphez az *Add-AzVMDataDisk* -lásd:<https://docs.microsoft.com/powershell/module/az.compute/add-Azvmdatadisk>

**Azure CLI**

* Jelentkezzen be az előfizetésbe az *az login* használatával
* Válassza ki az előfizetését az *az Account set `<subscription name or id` - > -előfizetés* paranccsal
* Töltse fel a VHD-t az *az Storage blob upload* -lásd: [Az Azure CLI használata az Azure Storage használatával][storage-azure-cli]
* Választható Felügyelt lemez létrehozása a VHD-ből az *az Disk Create* -lásd https://docs.microsoft.com/cli/azure/disk
* Új virtuális gép létrehozása a feltöltött VHD vagy a felügyelt lemez operációsrendszer-lemezként való megadásával az *az VM Create* és a paraméter *--Attach-os-Disk* paranccsal
* Adatlemez hozzáadása egy új virtuális géphez az *az VM Disk Attach* és a paraméter *– New*

**Sablon**

* A virtuális merevlemez feltöltése a PowerShell vagy az Azure CLI használatával
* Választható Felügyelt lemez létrehozása a VHD-ből a PowerShell, az Azure CLI vagy a Azure Portal használatával
* Telepítse a virtuális gépet egy olyan JSON-sablonnal, amely a virtuális merevlemezre hivatkozik, ahogy az [ebben a PÉLDÁBAN JSON](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-specialized-vhd-new-or-existing-vnet/azuredeploy.json) -sablonban is látható, vagy használja a Managed Disks az [ebben a példában szereplő JSON-sablonnal](https://github.com/Azure/azure-quickstart-templates/blob/master/sap-2-tier-user-image-md/azuredeploy.json)

#### <a name="deployment-of-a-vm-image"></a>Virtuálisgép-rendszerkép üzembe helyezése
Ha egy meglévő virtuális gépet vagy virtuális merevlemezt szeretne feltölteni a helyszíni hálózatból, akkor azt Azure-beli virtuálisgép-rendszerképként kell használni, mint egy virtuális gépet vagy virtuális merevlemezt, meg kell felelnie a jelen dokumentum [SAP-re vonatkozó ügyfél-specifikus rendszerképpel rendelkező virtuális gép üzembe helyezésének előkészítése][planning-guide-5.2.2] című fejezetben felsorolt követelményeknek.

* A *Sysprep* használata Windows rendszeren vagy *waagent –* Linux rendszeren való kiépítés a virtuális gép általánosítása érdekében – lásd: [Sysprep – technikai útmutató](https://technet.microsoft.com/library/cc766049.aspx) a Windowshoz vagy Linux rendszerű [virtuális gép rögzítése Linux Resource Manager-sablonként való használatra][capture-image-linux-step-2-create-vm-image]
* Jelentkezzen be az előfizetésbe a *AzAccount*
* Állítsa be a környezet előfizetését a *set-AzContext* és a paraméter SubscriptionId vagy SubscriptionName – lásd:<https://docs.microsoft.com/powershell/module/az.accounts/set-Azcontext>
* Töltse fel a VHD-t az *Add-AzVhd* használatával egy Azure Storage-fiókba – lásd:<https://docs.microsoft.com/powershell/module/az.compute/add-Azvhd>
* Választható Hozzon létre egy felügyelt lemezképet a VHD-ről a *New-AzImage* -lásd:<https://docs.microsoft.com/powershell/module/az.compute/new-Azimage>
* Egy új virtuálisgép-konfiguráció operációsrendszer-lemezének beállítása a következőre
  * VHD a *set-AzVMOSDisk-SourceImageUri-CreateOption fromImage* -lásd:<https://docs.microsoft.com/powershell/module/az.compute/set-Azvmosdisk>
  * Felügyelt lemezkép *set-AzVMSourceImage* -lásd:<https://docs.microsoft.com/powershell/module/az.compute/set-Azvmsourceimage>
* Hozzon létre egy új virtuális gépet a VM-konfigurációból a *New-AzVM* -lásd:<https://docs.microsoft.com/powershell/module/az.compute/new-Azvm>

**Azure CLI**

* A *Sysprep* használata Windows rendszeren vagy *waagent –* Linux rendszeren való kiépítés a virtuális gép általánosítása érdekében – lásd: [Sysprep – technikai útmutató](https://technet.microsoft.com/library/cc766049.aspx) a Windowshoz vagy Linux rendszerű [virtuális gép rögzítése Linux Resource Manager-sablonként való használatra][capture-image-linux-step-2-create-vm-image]
* Jelentkezzen be az előfizetésbe az *az login* használatával
* Válassza ki az előfizetését az *az Account set `<subscription name or id` - > -előfizetés* paranccsal
* Töltse fel a VHD-t az *az Storage blob upload* -lásd: [Az Azure CLI használata az Azure Storage használatával][storage-azure-cli]
* Választható Felügyelt lemezkép létrehozása a VHD-ből az *az rendszerkép Create* -lásd https://docs.microsoft.com/cli/azure/image
* Hozzon létre egy új virtuális gépet, amely a feltöltött virtuális merevlemezt vagy a felügyelt lemezképet operációsrendszer-lemezként határozza meg az *az VM Create* és a paraméter *--rendszerkép*

**Sablon**

* A *Sysprep* használata Windows rendszeren vagy *waagent –* Linux rendszeren való kiépítés a virtuális gép általánosítása érdekében – lásd: [Sysprep – technikai útmutató](https://technet.microsoft.com/library/cc766049.aspx) a Windowshoz vagy Linux rendszerű [virtuális gép rögzítése Linux Resource Manager-sablonként való használatra][capture-image-linux-step-2-create-vm-image]
* A virtuális merevlemez feltöltése a PowerShell vagy az Azure CLI használatával
* Választható Felügyelt lemezkép létrehozása a VHD-ből a PowerShell, az Azure CLI vagy a Azure Portal használatával
* Telepítse a virtuális gépet egy olyan JSON-sablonnal, amely hivatkozik a virtuális merevlemezre, ahogy az [ebben a PÉLDÁBAN JSON](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-specialized-vhd-new-or-existing-vnet/azuredeploy.json) -sablonban is látható, vagy a felügyelt lemez lemezképét használja az [ebben a példában szereplő JSON-sablonnal](https://github.com/Azure/azure-quickstart-templates/blob/master/sap-2-tier-user-image-md/azuredeploy.json).

#### <a name="downloading-vhds-or-managed-disks-to-on-premises"></a>VHD-k vagy Managed Disksek letöltése a helyszíni környezetbe
Az Azure-infrastruktúra mint szolgáltatás nem a VHD-k és az SAP-rendszerek feltöltésére szolgáló egyirányú utca. Az Azure-beli SAP-rendszereket a helyszíni világban is áthelyezheti.

A letöltés ideje alatt a VHD-k vagy a Managed Disks nem lehetnek aktívak. Még a virtuális gépekhez csatlakoztatott lemezek letöltésekor is le kell állítania és fel kell állítani a virtuális gépet. Ha csak azt szeretné letölteni, hogy melyik adatbázis-tartalmat kívánja használni, akkor egy új rendszer létrehozására van szükség a helyszínen, és ha a letöltés időpontja és az új rendszer beállítása az Azure-ban továbbra is működőképes lesz. a hosszú állásidő elkerülhető, ha a tömörített adatbázis biztonsági másolatát egy lemezre helyezi, és csak a lemezt tölti le, ahelyett, hogy az operációs rendszer alapját is letölti.

#### <a name="powershell"></a>Powershell

* Felügyelt lemez letöltése  
  Először el kell érnie a felügyelt lemez mögöttes blobját. Ezután átmásolhatja az alapul szolgáló blobot egy új Storage-fiókba, és letöltheti a blobot ebből a Storage-fiókból.

  ```powershell
  $access = Grant-AzDiskAccess -ResourceGroupName <resource group> -DiskName <disk name> -Access Read -DurationInSecond 3600
  $key = (Get-AzStorageAccountKey -ResourceGroupName <resource group> -Name <storage account name>)[0].Value
  $destContext = (New-AzStorageContext -StorageAccountName <storage account name -StorageAccountKey $key)
  Start-AzStorageBlobCopy -AbsoluteUri $access.AccessSAS -DestContainer <container name> -DestBlob <blob name> -DestContext $destContext
  # Wait for blob copy to finish
  Get-AzStorageBlobCopyState -Container <container name> -Blob <blob name> -Context $destContext
  Save-AzVhd -SourceUri <blob in new storage account> -LocalFilePath <local file path> -StorageKey $key
  # Wait for download to finish
  Revoke-AzDiskAccess -ResourceGroupName <resource group> -DiskName <disk name>
  ```

* Virtuális merevlemez letöltése  
  Ha az SAP-rendszer leállt, és a virtuális gép le van állítva, a AzVhd PowerShell-parancsmag használatával letöltheti a VHD-lemezeket a helyi világba. Ehhez szüksége lesz a virtuális merevlemez URL-címére, amelyet a Azure Portal tárolási szakasza tartalmaz (a Storage-fiókra és a VHD-t létrehozó tárolóra kell navigálnia), és tudnia kell, hogy hová kell másolni a virtuális merevlemezt.

  Ezután kihasználhatja a parancsot úgy, hogy meghatározza a SourceUri paramétert a letölteni kívánt VHD URL-címével, a LocalFilePath pedig a virtuális merevlemez fizikai helyén (beleértve a nevét is). A parancs a következőképpen néz ki:

  ```powerhell
  Save-AzVhd -ResourceGroupName <resource group name of storage account> -SourceUri http://<storage account name>.blob.core.windows.net/<container name>/sapidedata.vhd -LocalFilePath E:\Azure_downloads\sapidesdata.vhd
  ```

  A Save-AzVhd parancsmaggal kapcsolatos további részletekért tekintse <https://docs.microsoft.com/powershell/module/az.compute/save-Azvhd>meg a következőt:.

#### <a name="azure-cli"></a>Azure CLI
* Felügyelt lemez letöltése  
  Először el kell érnie a felügyelt lemez mögöttes blobját. Ezután átmásolhatja az alapul szolgáló blobot egy új Storage-fiókba, és letöltheti a blobot ebből a Storage-fiókból.
  ```
  az disk grant-access --ids "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" --duration-in-seconds 3600
  az storage blob download --sas-token "<sas token>" --account-name <account name> --container-name <container name> --name <blob name> --file <local file>
  az disk revoke-access --ids "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>"
  ```

* Virtuális merevlemez letöltése   
  Ha az SAP-rendszer leáll, és a virtuális gép le van állítva, az Azure CLI-parancs _Azure Storage-blob letöltésével_ a helyszíni célhelyen töltheti le a VHD-lemezeket a helyszíni világba. Ennek elvégzéséhez szüksége lesz a virtuális merevlemez nevére és tárolóra, amelyet a Azure Portal tárolási szakasza tartalmaz (meg kell keresnie a Storage-fiókot és a virtuális merevlemezt létrehozó tárolót), és tudnia kell, hol kell a virtuális merevlemez COP-nak lennie IED –.

  Ezután kihasználhatja a parancsot úgy, hogy meghatározza a parancsfájlhoz tartozó blobot és tárolót a letöltéshez, a célhelyet pedig a VHD fizikai célhelyének (beleértve a nevét is). A parancs a következőképpen néz ki:

  ```
  az storage blob download --name <name of the VHD to download> --container-name <container of the VHD to download> --account-name <storage account name of the VHD to download> --account-key <storage account key> --file <destination of the VHD to download>
  ```

### <a name="transferring-vms-and-disks-within-azure"></a>Virtuális gépek és lemezek átvitele az Azure-on belül

#### <a name="copying-sap-systems-within-azure"></a>SAP-rendszerek másolása az Azure-on belül

Az SAP-alkalmazási réteget támogató SAP-rendszer vagy egy dedikált adatbázis-kezelő kiszolgáló valószínűleg több lemezből áll, amelyek az operációs rendszert vagy az SAP-adatbázis bináris fájljait, illetve az azokhoz tartozó fájlokat vagy naplófájlokat tartalmazzák. Sem a lemezek másolásának, sem a lemezek helyi lemezre való mentésének Azure-funkciója sem tartalmaz szinkronizálási mechanizmust, amely egységes módon pillanatfelvételt készít több lemezről. Ezért a másolt vagy a mentett lemezek állapota akkor is eltérő lehet, ha az azonos virtuális géphez csatlakoztatott gépek nem azonosak. Ez azt jelenti, hogy abban az esetben, ha a különböző lemezeken található különböző adatfájlok és naplófájlok eltérőek, a végén a adatbázis inkonzisztens lenne.

**Megkötéséről Az SAP rendszerkonfiguráció részét képező lemezek másolásához vagy mentéséhez le kell állítania az SAP-rendszer beállítását, és le kell állítania a telepített virtuális gépet is. Csak ezután másolhatja vagy letöltheti a lemezek készletét, hogy az Azure-ban vagy a helyszínen hozzon létre egy másolatot az SAP-rendszerről.**

Az adatlemezek tárolható VHD-fájlként egy Azure Storage-fiókban, és közvetlenül csatlakoztathatók egy virtuális géphez, vagy képként is használhatók. Ebben az esetben a VHD-t egy másik helyre másolja a rendszer, mielőtt csatlakoztatja a virtuális géphez. Az Azure-ban lévő VHD-fájl teljes nevének egyedinek kell lennie az Azure-on belül. Ahogy korábban már említettük, a név egy háromrészes név, amely a következőképpen néz ki:

    http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>

Az adatlemezek Managed Disks is lehetnek. Ebben az esetben a felügyelt lemez használatával új felügyelt lemez hozható létre a virtuális géphez való csatolás előtt. A felügyelt lemez nevének egyedinek kell lennie az erőforráscsoport területén.

##### <a name="powershell"></a>Powershell

Azure PowerShell parancsmagok segítségével a [cikkben][storage-powershell-guide-full-copy-vhd]látható módon másolhat egy virtuális merevlemezt. Új felügyelt lemez létrehozásához használja a New-AzDiskConfig és a New-AzDisk az alábbi példában látható módon.

```powershell
$config = New-AzDiskConfig -CreateOption Copy -SourceUri "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" -Location <location>
New-AzDisk -ResourceGroupName <resource group name> -DiskName <disk name> -Disk $config
```

##### <a name="azure-cli"></a>Azure CLI

Az Azure CLI használatával a [cikkben][storage-azure-cli-copy-blobs]látható módon másolhat egy virtuális merevlemezt. Új felügyelt lemez létrehozásához használja az *az Disk Create* lehetőséget az alábbi példában látható módon.

```
az disk create --source "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" --name <disk name> --resource-group <resource group name> --location <location>
```

##### <a name="azure-storage-tools"></a>Azure Storage-eszközök

* <https://storageexplorer.com/>

Az Azure Storage Explorer Professional kiadásai itt találhatók:

* <https://www.cerebrata.com/>
* <https://clumsyleaf.com/products/cloudxplorer>

A virtuális merevlemez másolata a Storage-fiókon belül egy olyan folyamat, amely mindössze néhány másodpercet vesz igénybe (a SAN-hardverhez hasonlóan, ha a pillanatképeket a rendszer lusta másolattal hozza létre, és a másolást írja). Miután elvégezte a VHD-fájl másolatát, csatolhatja azt egy virtuális géphez, vagy felhasználhatja képként, hogy csatolja a VHD-t a virtuális gépekhez.

##### <a name="powershell"></a>Powershell

```powershell
# attach a vhd to a vm
$vm = Get-AzVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzVMDataDisk -VM $vm -Name newdatadisk -VhdUri <path to vhd> -Caching <caching option> -DiskSizeInGB $null -Lun <lun, for example 0> -CreateOption attach
$vm | Update-AzVM

# attach a managed disk to a vm
$vm = Get-AzVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzVMDataDisk -VM $vm -Name newdatadisk -ManagedDiskId <managed disk id> -Caching <caching option> -DiskSizeInGB $null -Lun <lun, for example 0> -CreateOption attach
$vm | Update-AzVM

# attach a copy of the vhd to a vm
$vm = Get-AzVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzVMDataDisk -VM $vm -Name <disk name> -VhdUri <new path of vhd> -SourceImageUri <path to image vhd> -Caching <caching option> -DiskSizeInGB $null -Lun <lun, for example 0> -CreateOption fromImage
$vm | Update-AzVM

# attach a copy of the managed disk to a vm
$vm = Get-AzVM -ResourceGroupName <resource group name> -Name <vm name>
$diskConfig = New-AzDiskConfig -Location $vm.Location -CreateOption Copy -SourceUri <source managed disk id>
$disk = New-AzDisk -DiskName <disk name> -Disk $diskConfig -ResourceGroupName <resource group name>
$vm = Add-AzVMDataDisk -VM $vm -Caching <caching option> -Lun <lun, for example 0> -CreateOption attach -ManagedDiskId $disk.Id
$vm | Update-AzVM
```
##### <a name="azure-cli"></a>Azure CLI

```

# attach a vhd to a vm
az vm unmanaged-disk attach --resource-group <resource group name> --vm-name <vm name> --vhd-uri <path to vhd>

# attach a managed disk to a vm
az vm disk attach --resource-group <resource group name> --vm-name <vm name> --disk <managed disk id>

# attach a copy of the vhd to a vm
# this scenario is currently not possible with Azure CLI. A workaround is to manually copy the vhd to the destination.

# attach a copy of a managed disk to a vm
az disk create --name <new disk name> --resource-group <resource group name> --location <location of target virtual machine> --source <source managed disk id>
az vm disk attach --disk <new disk name or managed disk id> --resource-group <resource group name> --vm-name <vm name> --caching <caching option> --lun <lun, for example 0>
```

#### <a name="9789b076-2011-4afa-b2fe-b07a8aba58a1"></a>Lemezek másolása az Azure Storage-fiókok között
Ez a feladat nem hajtható végre a Azure Portalon. Használhat Azure PowerShell parancsmagokat, az Azure CLI-t vagy egy külső gyártótól származó tároló böngészőt. A PowerShell-parancsmagok és a CLI-parancsok létrehozhatják és kezelhetik a blobokat, amelyekben a Blobok aszinkron módon másolhatók a Storage-fiókok és az Azure-előfizetésben található régiók között.

##### <a name="powershell"></a>Powershell
A virtuális merevlemezeket az előfizetések között is másolhatja. További információért olvassa el [ezt a cikket][storage-powershell-guide-full-copy-vhd].

A PS-parancsmag logikájának alapszintű folyamata így néz ki:

* Hozzon létre egy Storage-fiók környezetét a forrásként szolgáló Storage-fiókhoz a *New-AzStorageContext* -lásd:<https://docs.microsoft.com/powershell/module/az.storage/new-AzStoragecontext>
* Hozzon létre egy Storage-fiók környezetét a **cél** Storage-fiókhoz a *New-AzStorageContext* -lásd:<https://docs.microsoft.com/powershell/module/az.storage/new-AzStoragecontext>
* A másolás elindítása a

```powershell
Start-AzStorageBlobCopy -SrcBlob <source blob name> -SrcContainer <source container name> -SrcContext <variable containing context of source storage account> -DestBlob <target blob name> -DestContainer <target container name> -DestContext <variable containing context of target storage account>
```

* A másolás állapotának ellenőrzését egy hurokban a

```powershell
Get-AzStorageBlobCopyState -Blob <target blob name> -Container <target container name> -Context <variable containing context of target storage account>
```

* Csatlakoztassa az új VHD-t egy virtuális géphez a fent leírtak szerint.

Példa erre a [cikkre][storage-powershell-guide-full-copy-vhd].

##### <a name="azure-cli"></a>Azure CLI
* A másolás elindítása a

```
az storage blob copy start --source-blob <source blob name> --source-container <source container name> --source-account-name <source storage account name> --source-account-key <source storage account key> --destination-container <target container name> --destination-blob <target blob name> --account-name <target storage account name> --account-key <target storage account name>
```

* Ellenőrizze az állapotot, ha a másolat még mindig hurokban van

```
az storage blob show --name <target blob name> --container <target container name> --account-name <target storage account name> --account-key <target storage account name>
```

* Csatlakoztassa az új VHD-t egy virtuális géphez a fent leírtak szerint.

Példa erre a [cikkre][storage-azure-cli-copy-blobs].

### <a name="disk-handling"></a>Lemezek feldolgozása

#### <a name="4efec401-91e0-40c0-8e64-f2dceadff646"></a>VM/Disk-struktúra SAP-környezetekhez

Ideális esetben a virtuális gép szerkezetének és a kapcsolódó lemezeknek egyszerűnek kell lenniük. A helyszíni telepítések során az ügyfelek számos módszert fejlesztettek ki a kiszolgálók telepítésének szervezéséhez.

* Egy alaplemez, amely az operációs rendszer és az adatbázis-kezelő és/vagy az SAP összes bináris fájlját tartalmazza. Március 2015 óta ez a lemez akár 1 TB méretű is lehet, a korábbi korlátozások helyett, amelyek korlátozzák a 127GB.
* Egy vagy több lemez, amely a SAP-adatbázis adatbázis-kezelői naplófájlját és az adatbázis-KEZELŐrendszer ideiglenes tárolóhelyének naplófájlját tartalmazza (ha az adatbázis-kezelő rendszer támogatja ezt a fájlt). Ha az adatbázis-naplózási IOPS követelményei magasak, több lemezt kell megadnia ahhoz, hogy elérje a szükséges IOPS-kötetet.
* Több lemez, amely az SAP-adatbázis egy vagy két adatbázisát, valamint az adatbázis-kezelő rendszer ideiglenes adatfájljait is tartalmazza (ha az adatbázis-kezelő rendszer támogatja ezt).

![Az Azure IaaS VM SAP-hoz való konfigurálása][planning-guide-figure-1300]


---
> ![Windows][Logo_Windows] Windows
>
> Számos ügyfelünk láttuk a konfigurációt, ahol például az SAP és az adatbázis-kezelő bináris fájljai nem voltak telepítve a c:\ az operációs rendszer telepítési helyéül szolgáló meghajtó. Ennek több oka is volt, de amikor a gyökérbe mentünk, általában az volt, hogy a meghajtók kicsik voltak, és az operációs rendszer frissítésére a 10-15 évvel ezelőtt további lemezterület szükséges. Ezek a napok túl gyakran nem vonatkoznak mindkét feltételre. Ma a c:\ a meghajtó nagy mennyiségű lemezen vagy virtuális gépen képezhető le. Az üzembe helyezések struktúrában való egyszerű megtartásához ajánlott az Azure-beli SAP NetWeaver-rendszerek következő üzembe helyezési mintájának követése:
>
> A Windows operációs rendszer lapozófájljának a D: meghajtón kell lennie (nem állandó lemez)
>
> ![Linux][Logo_Linux] Linux
>
> Az [ebben a cikkben][virtual-machines-linux-agent-user-guide]leírtak szerint helyezze a Linux-swapfile a Linux/mnt/mnt/Resource alá. A swap-fájl konfigurálható a Linux-ügynök/etc/waagent.conf. konfigurációs fájljában. Adja hozzá vagy módosítsa a következő beállításokat:
>
>

```
ResourceDisk.EnableSwap=y
ResourceDisk.SwapSizeMB=30720
```

A módosítások aktiválásához újra kell indítania a Linux-ügynököt a következővel

```
sudo service waagent restart
```

Az ajánlott swap-fájl méretével kapcsolatos további részletekért olvassa el a [1597355] -es SAP-megjegyzést.

---
Az adatbázis-kezelői adatfájlokhoz használt lemezek számát és az Azure Storage-nak ezeket a lemezeket a IOPS követelmények és a szükséges késés határozza meg. A pontos kvótákat a [cikk (Linux)][virtual-machines-sizes-linux] és [a jelen cikk (Windows)][virtual-machines-sizes-windows]ismerteti.

Az SAP üzemelő példányok tapasztalatai az elmúlt két évben néhány leckét tanítottak, amelyek a következőképpen foglalhatók össze:

* A különböző adatfájlokra irányuló adatforgalom nem mindig azonos, mivel a meglévő IOPS eltérő méretű adatfájlok lehetnek, amelyek az SAP-adatbázis (ok) t jelképezik. Ennek eredményeképpen a több lemezről származó RAID-konfiguráció jobb használatát eredményezte, hogy az adatfájlok a logikai egységek kifaragva legyenek. Voltak olyan helyzetek, különösen az Azure standard Storage-ban, ahol a IOPS ráta egyetlen lemez kvótáját éri el az adatbázis-kezelői tranzakciónaplóban. Ilyen esetekben a Premium Storage használata javasolt, vagy ha több standard tárolóeszközt is összevon a szoftveres szalagok használatával.

---
> ![Windows][Logo_Windows] Windows
>
> * [Ajánlott eljárások az SQL Server teljesítményének Azure Virtual Machines szolgáltatásbeli növeléséhez][virtual-machines-sql-server-performance-best-practices]
>
> ![Linux][Logo_Linux] Linux
>
> * [Szoftveres RAID konfigurálása Linuxon][virtual-machines-linux-configure-raid]
> * [Az LVM konfigurálása Linux rendszerű virtuális gépen az Azure-ban][virtual-machines-linux-configure-lvm]
> * [Azure Storage-titkok és Linux I/O-optimalizálások](https://blogs.msdn.com/b/igorpag/archive/2014/10/23/azure-storage-secrets-and-linux-i-o-optimizations.aspx)
>
>

---
* A Premium Storage jelentős jobb teljesítményt mutat, különösen a kritikus tranzakciós naplók írásakor. Az olyan SAP-forgatókönyvek esetében, amelyek várhatóan az éles környezethez hasonló teljesítményt biztosítanak, erősen ajánlott olyan VM-sorozatot használni, amely képes az Azure Premium Storage használatára.

Ne feledje, hogy az operációs rendszert tartalmazó lemez, és ahogy azt javasoljuk, az SAP és az adatbázis (alapszintű virtuális gép) bináris fájljai már nem korlátozódnak a 127GB. Mostantól akár 1 TB méretű is lehet. Ennek elegendő helynek kell lennie az összes szükséges fájl (például az SAP batch-feladatok naplói) megtartásához.

További javaslatok és további részletek, különösen az adatbázis-kezelő virtuális gépek esetében tekintse meg az [adatbázis-kezelő üzembe helyezési útmutatóját][dbms-guide]

#### <a name="disk-handling"></a>Lemezek feldolgozása

A legtöbb esetben további lemezeket kell létrehoznia ahhoz, hogy az SAP-adatbázist üzembe lehessen helyezni a virtuális gépen. Beszéltünk a jelen dokumentum SAP-környezetek számára készült virtuális gépek [/lemezek szerkezete][planning-guide-5.5.1] című fejezetében található lemezek számával kapcsolatos megfontolásokról. A Azure Portal lemezek csatlakoztatását és leválasztását teszi lehetővé az alapszintű virtuális gép üzembe helyezésekor. A lemezek csatlakoztathatók/leválaszthatók, ha a virtuális gép működik, és leáll. Lemez csatlakoztatásakor a Azure Portal egy üres lemez vagy egy meglévő lemez csatolására szolgál, amely ebben az időpontban nem csatlakozik egy másik virtuális géphez.

**Megjegyzés**: A lemezeket csak egy virtuális géphez lehet csatlakoztatni egy adott időpontban.

![Lemezek csatlakoztatása/leválasztása az Azure standard Storage szolgáltatással][planning-guide-figure-1400]

Egy új virtuális gép telepítése során eldöntheti, hogy szeretné-e használni a Managed Disks, vagy helyezze el a lemezeket az Azure Storage-fiókokban. Ha Premium Storage használatát szeretné használni, javasoljuk, hogy használja a Managed Diskst.

Ezután el kell döntenie, hogy létre kell-e hoznia egy új és egy üres lemezt, vagy ha olyan meglévő lemezt szeretne kijelölni, amely korábban fel lett töltve, és most csatlakoztatni kell a virtuális géphez.

**FONTOS**: A gazdagépek gyorsítótárazását **nem** kívánja az Azure standard Storage szolgáltatással használni. A gazdagép-gyorsítótár beállításait a NONE (alapértelmezett) értéknél hagyja meg. Az Azure Premium Storage használata esetén engedélyeznie kell az olvasási gyorsítótárazást, ha az I/O-jellemző többnyire olvasási, például az adatbázis-adatfájlokkal kapcsolatos jellemző I/O-forgalom. Adatbázis-tranzakciós naplófájl esetén nem ajánlott a gyorsítótárazás.

---
> ![Windows][Logo_Windows] Windows
>
> [Adatlemez csatolása a Azure Portal][virtual-machines-linux-attach-disk-portal]
>
> Ha lemez van csatlakoztatva, be kell jelentkeznie a virtuális gépre, hogy megnyissa a Windows Disk Managert. Ha az automatikus csatlakoztatás nincs engedélyezve a [csatolt lemezek automatikus csatlakoztatásának beállítása][planning-guide-5.5.3]című fejezetben leírtak szerint, az újonnan csatolt kötetet online állapotba kell hozni, és inicializálni kell.
>
> ![Linux][Logo_Linux] Linux
>
> Ha lemez van csatlakoztatva, be kell jelentkeznie a virtuális gépre, és inicializálnia kell a lemezeket a [jelen cikkben][virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux] leírtak szerint.
>
>

---
Ha az új lemez üres lemez, a lemezt is formázni kell. A formázáshoz, különösen az adatbázis-kezelői és a naplófájlok esetében, ugyanazok a javaslatok érvényesek, mint az adatbázis-kezelőrendszer operációs rendszer nélküli üzembe helyezésére.

Ahogy már említettük [a Microsoft Azure virtuálisgép][planning-guide-3.2]-koncepcióban, az Azure Storage-fiók nem biztosít végtelen erőforrásokat az I/O-kötetek, a IOPS és az adatmennyiségek tekintetében. Ez általában az adatbázis-kezelő virtuális gépeket érinti. Érdemes lehet külön Storage-fiókot használni az egyes virtuális gépekhez, ha kevés magas I/O-kötettel rendelkező virtuális gép van üzembe helyezni ahhoz, hogy az Azure Storage-fiók kötetén belül maradjon. Ellenkező esetben meg kell ismernie, hogyan egyenlítheti ki ezeket a virtuális gépeket a különböző Storage-fiókok között anélkül, hogy megütötte az egyes Storage-fiókok korlátját. További részleteket az [adatbázis-kezelő üzembe helyezési útmutatójában][dbms-guide]talál. Ezeket a korlátozásokat figyelembe kell vennie a tiszta SAP Application Server-alapú virtuális gépek vagy más virtuális gépek esetében is, amelyek esetleg további VHD-ket igényelhetnek. A felügyelt lemez használata esetén ezek a korlátozások nem érvényesek. Ha Premium Storage használatát tervezi, javasoljuk, hogy felügyelt lemezt használjon.

Egy másik témakör, amely a Storage-fiókok esetében fontos, hogy a Storage-fiókban lévő virtuális merevlemezek földrajzilag replikálódnak-e. A Geo-replikáció engedélyezve van vagy le van tiltva a tárolási fiók szintjén, nem pedig a virtuális gép szintjén. Ha a Geo-replikáció engedélyezve van, a Storage-fiókban található virtuális merevlemezek egy másik Azure-adatközpontba lesznek replikálva ugyanazon a régión belül. Mielőtt döntene, gondolja át a következő korlátozást:

Az Azure geo-Replication helyileg működik a virtuális gépek mindegyik virtuális merevlemezén, és az IOs-t időrendi sorrendben replikálja a virtuális gépek több VHD-je között. Ezért az alap virtuális gépet és a virtuális géphez csatolt további virtuális merevlemezeket jelképező VHD-k egymástól függetlenül replikálódnak egymástól. Ez azt jelenti, hogy nincs szinkronizálás a különböző virtuális merevlemezek változásai között. Az a tény, hogy az IOs-et az írásuk sorrendjétől függetlenül replikálják, azt jelenti, hogy a Geo-replikáció nem érték olyan adatbázis-kiszolgálók esetében, amelyek adatbázis-kiszolgálói több virtuális merevlemezen vannak elosztva. Az adatbázis-kezelőn kívül más alkalmazások is lehetnek, amelyekben az adatok írásával vagy módosításával különböző VHD-k, és ahol fontos a módosítások sorrendjének megőrzése. Ha ez követelmény, a Geo-replikáció az Azure-ban nem engedélyezhető. Attól függően, hogy szükség van-e a Geo-replikációra a virtuális gépek egy készlete számára, de nem egy másik készlet esetében, már kategorizálhatja a virtuális gépeket és a kapcsolódó virtuális merevlemezeket különböző, a földrajzi replikálást engedélyező vagy letiltott tárolási fiókokba.

#### <a name="17e0d543-7e8c-4160-a7da-dd7117a1ad9d"></a>A csatlakoztatott lemezek automatikus csatlakoztatásának beállítása
---
> ![Windows][Logo_Windows] Windows
>
> A saját lemezképből vagy lemezből létrehozott virtuális gépek esetében szükség van az automatikus csatlakoztatási paraméter ellenőrzésére és beállítására. A paraméter beállítása lehetővé teszi, hogy a virtuális gép újraindítás vagy az Azure-beli újratelepítése után automatikusan csatlakoztassa a csatlakoztatott/csatlakoztatott meghajtókat.
> A paraméter a Microsoft által az Azure piactéren biztosított rendszerképekhez van beállítva.
>
> Az automatikus csatlakoztatás beállításához tekintse meg a parancssori végrehajtható DiskPart. exe dokumentációját itt:
>
> * [A DiskPart parancssori kapcsolói](https://technet.microsoft.com/library/bb490893.aspx)
> * [Automount](https://technet.microsoft.com/library/cc753703.aspx)
>
> A Windows parancssori ablakát rendszergazdaként kell megnyitni.
>
> Ha lemez van csatlakoztatva, be kell jelentkeznie a virtuális gépre, hogy megnyissa a Windows Disk Managert. Ha az automatikus csatlakoztatás nincs engedélyezve a [csatolt lemezek automatikus csatlakoztatásának beállítása][planning-guide-5.5.3]című fejezetben leírtak szerint, az újonnan csatolt kötetet > kell online állapotba hozni és inicializálni.
>
> ![Linux][Logo_Linux] Linux
>
> Inicializálnia kell egy újonnan csatolt üres lemezt a [jelen cikkben][virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]leírtak szerint.
> Új lemezeket is fel kell vennie a/etc/fstab.
>
>

---
### <a name="final-deployment"></a>Végső üzembe helyezés

A végső telepítés és a pontos lépések esetében, különösen az SAP kiterjesztett figyelésének üzembe helyezésével kapcsolatban, tekintse meg a [telepítési útmutatót][deployment-guide].

## <a name="accessing-sap-systems-running-within-azure-vms"></a>Azure-beli virtuális gépeken futó SAP-rendszerek elérése

Azokban az esetekben, amikor a nyilvános interneten keresztül szeretne csatlakozni az SAP-rendszerekhez az SAP GUI használatával, a következő eljárásokat kell alkalmazni.

A dokumentum későbbi részében megbeszéljük a másik fő forgatókönyvet, amely a helyek közötti kapcsolat (VPN-alagút) és a helyszíni rendszerek és az Azure-rendszerek közötti Azure ExpressRoute-kapcsolaton keresztül csatlakozik az SAP-rendszerekhez.

### <a name="remote-access-to-sap-systems"></a>Távoli hozzáférés az SAP-rendszerekhez

A Azure Resource Managerban nincsenek olyan alapértelmezett végpontok, mint a korábbi klasszikus modellben. Egy Azure Resource Manager virtuális gép összes portja nyitva van, amíg:

1. Nincs definiálva hálózati biztonsági csoport az alhálózathoz vagy a hálózati adapterhez. Az Azure-beli virtuális gépek felé irányuló hálózati forgalom az úgynevezett "hálózati biztonsági csoportok" segítségével biztosítható. További információ: [What is a Network Security Group (NSG)?][virtual-networks-nsg] (Mi az a hálózati biztonsági csoport?).
2. Nincs megadva Azure Load Balancer a hálózati adapterhez   

Tekintse meg a klasszikus modell és a kar architektúrájának különbségét a [jelen cikkben][virtual-machines-azure-resource-manager-architecture]leírtak szerint.

#### <a name="configuration-of-the-sap-system-and-sap-gui-connectivity-over-the-internet"></a>Az SAP-rendszerek és az SAP GUI-kapcsolat konfigurációja az interneten keresztül

Tekintse meg ezt a cikket, amely leírja a témakör részleteit:<https://blogs.msdn.com/b/saponsqlserver/archive/2014/06/24/sap-gui-connection-closed-when-connecting-to-sap-system-in-azure.aspx>

#### <a name="changing-firewall-settings-within-vm"></a>A tűzfal beállításainak módosítása a virtuális gépen

Előfordulhat, hogy a tűzfalat úgy kell konfigurálnia a virtuális gépeken, hogy engedélyezze a bejövő forgalmat az SAP-rendszeren.

---
> ![Windows][Logo_Windows] Windows
>
> Alapértelmezés szerint az Azure-beli üzembe helyezett virtuális gépen belüli Windows tűzfal be van kapcsolva. Most engedélyeznie kell az SAP-port megnyitását, ellenkező esetben az SAP GUI nem fog tudni csatlakozni.
> Ehhez tegye a következőket:
>
> * Nyissa meg a vezérlés Vezérlőpult és a biztonság tűzfalat a **Speciális beállítások**között.
> * Most kattintson a jobb gombbal a bejövő szabályok elemre, és válassza az **új szabály**lehetőséget.
> * A következő varázslóban új **portszabály** létrehozását választotta.
> * A varázsló következő lépésében hagyja a beállítást a TCP-ben, és írja be a megnyitni kívánt portszámot. Mivel az SAP-példány azonosítója 00, a 3200-es évekig tartottunk. Ha a példánya eltérő példányszámmal rendelkezik, meg kell nyitni a korábban a példány száma alapján megadott portot.
> * A varázsló következő részén hagyja bejelölve a **kapcsolat engedélyezése** elemet.
> * A varázsló következő lépésében meg kell határoznia, hogy a szabály a tartományra, a magán-és a nyilvános hálózatra vonatkozik-e. Szükség esetén módosítsa az igényeket. Ha azonban az SAP grafikus felhasználói felületről a nyilvános hálózaton kívülről csatlakozik, akkor a nyilvános hálózaton alkalmaznia kell a szabályt.
> * A varázsló utolsó lépésében nevezze el a szabályt, és mentse a Finish ( **Befejezés**) billentyűt.
>
> A szabály azonnal érvénybe lép.
>
> ![Portszabály definíciója][planning-guide-figure-1600]
>
> ![Linux][Logo_Linux] Linux
>
> Az Azure Marketplace-en található Linux-lemezképek alapértelmezés szerint nem engedélyezik az iptables tűzfalat, és az SAP-rendszerhez való kapcsolódásnak működnie kell. Ha engedélyezte az iptables-t vagy egy másik tűzfalat, tekintse meg az iptables vagy a használt tűzfal dokumentációját, hogy engedélyezze a bejövő TCP-forgalmat a port 32xx (ahol XX az SAP-rendszer rendszerszáma).
>
>

---
#### <a name="security-recommendations"></a>Biztonsági javaslatok

Az SAP GUI nem csatlakozik azonnal a-t futtató SAP-példányokhoz (32xx), de először az SAP-üzenetküldési kiszolgáló folyamatához megnyitott porton keresztül csatlakozik (36xx port). A múltban ugyanezt a portot használta az alkalmazáskiszolgáló a belső kommunikációhoz az alkalmazás példányaihoz. Annak megakadályozása érdekében, hogy a helyszíni alkalmazások akaratlanul kommunikáljanak az Azure-beli üzenetkezelő kiszolgálóval, a belső kommunikációs portok megváltoztathatók. Erősen ajánlott megváltoztatni az SAP-üzenet kiszolgálója és az alkalmazás példányai közötti belső kommunikációt a helyszíni rendszerekről klónozott rendszereken, például a projekt tesztelési célú fejlesztésének klónozásán keresztül. Ezt az alapértelmezett profil paraméterrel végezheti el:

> rdisp/msserv_internal
>
>

[az SAP-üzenet kiszolgálójának biztonsági beállításaiban](https://help.sap.com/saphelp_nwpi71/helpdata/en/47/c56a6938fb2d65e10000000a42189c/content.htm) leírt módon


### <a name="3e9c3690-da67-421a-bc3f-12c520d99a30"></a>Egyetlen virtuális gép SAP NetWeaver bemutatóval/betanítási forgatókönyvvel

![Egyetlen VM-es SAP-bemutató rendszer futtatása azonos virtuálisgép-nevekkel, amelyek az Azure-ban elkülönítettek Cloud Services][planning-guide-figure-1700]

Ebben a forgatókönyvben egy tipikus betanítási/bemutató rendszeresetet implementálunk, ahol a teljes képzési/bemutató forgatókönyv egyetlen virtuális gépen található. Feltételezzük, hogy az üzembe helyezés a virtuálisgép-rendszerkép sablonjain keresztül történik. Azt is feltételezzük, hogy a bemutató/betanítási virtuális gépek többszörösét az azonos nevű virtuális gépekkel kell telepíteni. A teljes képzési rendszerek nem kapcsolódnak a helyszíni eszközökhöz, és a hibrid üzembe helyezésük ellentétes.

Ennek a dokumentumnak a feltételezése, hogy létrehozott egy virtuálisgép-rendszerképet, amely a virtuális [gépek SAP for Azure][planning-guide-5.2] -hoz való előkészítése című fejezet néhány szakasza ismerteti.

A forgatókönyv megvalósításához szükséges események sorozatot a következőképpen kell kinéznie:

##### <a name="powershell"></a>Powershell

* Új erőforráscsoport létrehozása minden betanítási vagy demó környezethez

```powershell
$rgName = "SAPERPDemo1"
New-AzResourceGroup -Name $rgName -Location "North Europe"
```
* Hozzon létre egy új Storage-fiókot, ha nem kívánja használni a Managed Disks

```powershell
$suffix = Get-Random -Minimum 100000 -Maximum 999999
$account = New-AzStorageAccount -ResourceGroupName $rgName -Name "saperpdemo$suffix" -SkuName Standard_LRS -Kind "Storage" -Location "North Europe"
```

* Hozzon létre egy új virtuális hálózatot minden betanítás/bemutató környezet számára, hogy lehetővé váljon ugyanaz az állomásnév és IP-címek használata. A virtuális hálózatot egy olyan hálózati biztonsági csoport védi, amely csak az 3389-es portra irányuló forgalmat engedélyezi az SSH-hoz Távoli asztal hozzáférés és a 22-es port számára.

```powershell
# Create a new Virtual Network
$rdpRule = New-AzNetworkSecurityRuleConfig -Name SAPERPDemoNSGRDP -Protocol * -SourcePortRange * -DestinationPortRange 3389 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 100
$sshRule = New-AzNetworkSecurityRuleConfig -Name SAPERPDemoNSGSSH -Protocol * -SourcePortRange * -DestinationPortRange 22 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 101
$nsg = New-AzNetworkSecurityGroup -Name SAPERPDemoNSG -ResourceGroupName $rgName -Location  "North Europe" -SecurityRules $rdpRule,$sshRule

$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name Subnet1 -AddressPrefix  10.0.1.0/24 -NetworkSecurityGroup $nsg
$vnet = New-AzVirtualNetwork -Name SAPERPDemoVNet -ResourceGroupName $rgName -Location "North Europe"  -AddressPrefix 10.0.1.0/24 -Subnet $subnetConfig
```

* Hozzon létre egy új nyilvános IP-címet, amelyet a virtuális gép internetről való eléréséhez használhat

```powershell
# Create a public IP address with a DNS name
$pip = New-AzPublicIpAddress -Name SAPERPDemoPIP -ResourceGroupName $rgName -Location "North Europe" -DomainNameLabel $rgName.ToLower() -AllocationMethod Dynamic
```

* Új hálózati adapter létrehozása a virtuális géphez

```powershell
# Create a new Network Interface
$nic = New-AzNetworkInterface -Name SAPERPDemoNIC -ResourceGroupName $rgName -Location "North Europe" -Subnet $vnet.Subnets[0] -PublicIpAddress $pip
```

* Virtuális gépet hoz létre. Ebben a forgatókönyvben minden virtuális gépnek ugyanazzal a névvel kell rendelkeznie. A virtuális gépeken található SAP NetWeaver-példányok SAP-SID-je ugyanezt is megteszi. Az Azure-erőforráscsoport területén a virtuális gép nevének egyedinek kell lennie, de különböző Azure-erőforráscsoportok esetében azonos nevű virtuális gépeket futtathat. A Windows vagy a "root" rendszergazdai fiókja nem érvényes a Linux rendszerhez. Ezért új rendszergazdai felhasználónevet kell definiálni a jelszóval együtt. A virtuális gép méretét is meg kell határozni.

```powershell
#####
# Create a new virtual machine with an official image from the Azure Marketplace
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzVMConfig -VMName SAPERPDemo -VMSize Standard_D11

# select image
$vmconfig = Set-AzVMSourceImage -VM $vmconfig -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer" -Skus "2012-R2-Datacenter" -Version "latest"
$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
# $vmconfig = Set-AzVMSourceImage -VM $vmconfig -PublisherName "SUSE" -Offer "SLES-SAP" -Skus "12-SP1" -Version "latest"
# $vmconfig = Set-AzVMSourceImage -VM $vmconfig -PublisherName "RedHat" -Offer "RHEL" -Skus "7.2" -Version "latest"
# $vmconfig = Set-AzVMSourceImage -VM $vmconfig -PublisherName "Oracle" -Offer "Oracle-Linux" -Skus "7.2" -Version "latest"
# $vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Add-AzVMNetworkInterface -VM $vmconfig -Id $nic.Id

$vmconfig = Set-AzVMBootDiagnostics -Disable -VM $vmconfig
$vm = New-AzVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

```powershell
#####
# Create a new virtual machine with a VHD that contains the private image that you want to use
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzVMConfig -VMName SAPERPDemo -VMSize Standard_D11

$vmconfig = Add-AzVMNetworkInterface -VM $vmconfig -Id $nic.Id

$diskName="osfromimage"
$osDiskUri=$account.PrimaryEndpoints.Blob.ToString() + "vhds/" + $diskName  + ".vhd"

$vmconfig = Set-AzVMOSDisk -VM $vmconfig -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri <path to VHD that contains the OS image> -Windows
$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred
#$vmconfig = Set-AzVMOSDisk -VM $vmconfig -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri <path to VHD that contains the OS image> -Linux
#$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Set-AzVMBootDiagnostics -Disable -VM $vmconfig
$vm = New-AzVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

```powershell
#####
# Create a new virtual machine with a Managed Disk Image
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzVMConfig -VMName SAPERPDemo -VMSize Standard_D11

$vmconfig = Add-AzVMNetworkInterface -VM $vmconfig -Id $nic.Id

$vmconfig = Set-AzVMSourceImage -VM $vmconfig -Id <Id of Managed Disk Image>
$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred
#$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Set-AzVMBootDiagnostics -Disable -VM $vmconfig
$vm = New-AzVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

* Opcionálisan hozzáadhat további lemezeket, és helyreállíthatja a szükséges tartalmakat. Minden blob nevének (a Blobok URL-címei) egyedinek kell lennie az Azure-on belül.

```powershell
# Optional: Attach additional VHD data disks
$vm = Get-AzVM -ResourceGroupName $rgName -Name SAPERPDemo
$dataDiskUri = $account.PrimaryEndpoints.Blob.ToString() + "vhds/datadisk.vhd"
Add-AzVMDataDisk -VM $vm -Name datadisk -VhdUri $dataDiskUri -DiskSizeInGB 1023 -CreateOption empty | Update-AzVM

# Optional: Attach additional Managed Disks
$vm = Get-AzVM -ResourceGroupName $rgName -Name SAPERPDemo
Add-AzVMDataDisk -VM $vm -Name datadisk -DiskSizeInGB 1023 -CreateOption empty -Lun 0 | Update-AzVM
```

##### <a name="cli"></a>parancssori felület

A következő példában a kód Linux rendszeren is használható. Windows esetén a PowerShellt a fent leírtak szerint használja, vagy a példát a (z)% rgName% használatára a $rgName helyett, a környezetiváltozót pedig a Windows-paranccsal állíthatja be.

* Új erőforráscsoport létrehozása minden betanítási vagy demó környezethez

```
rgName=SAPERPDemo1
rgNameLower=saperpdemo1
az group create --name $rgName --location "North Europe"
```

* Új tárfiók létrehozása

```
az storage account create --resource-group $rgName --location "North Europe" --kind Storage --sku Standard_LRS --name $rgNameLower
```

* Hozzon létre egy új virtuális hálózatot minden betanítás/bemutató környezet számára, hogy lehetővé váljon ugyanaz az állomásnév és IP-címek használata. A virtuális hálózatot egy olyan hálózati biztonsági csoport védi, amely csak az 3389-es portra irányuló forgalmat engedélyezi az SSH-hoz Távoli asztal hozzáférés és a 22-es port számára.

```
az network nsg create --resource-group $rgName --location "North Europe" --name SAPERPDemoNSG
az network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGRDP --protocol \* --source-address-prefix \* --source-port-range \* --destination-address-prefix \* --destination-port-range 3389 --access Allow --priority 100 --direction Inbound
az network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGSSH --protocol \* --source-address-prefix \* --source-port-range \* --destination-address-prefix \* --destination-port-range 22 --access Allow --priority 101 --direction Inbound

az network vnet create --resource-group $rgName --name SAPERPDemoVNet --location "North Europe" --address-prefixes 10.0.1.0/24
az network vnet subnet create --resource-group $rgName --vnet-name SAPERPDemoVNet --name Subnet1 --address-prefix 10.0.1.0/24 --network-security-group SAPERPDemoNSG
```

* Hozzon létre egy új nyilvános IP-címet, amelyet a virtuális gép internetről való eléréséhez használhat

```
az network public-ip create --resource-group $rgName --name SAPERPDemoPIP --location "North Europe" --dns-name $rgNameLower --allocation-method Dynamic
```

* Új hálózati adapter létrehozása a virtuális géphez

```
az network nic create --resource-group $rgName --location "North Europe" --name SAPERPDemoNIC --public-ip-address SAPERPDemoPIP --subnet Subnet1 --vnet-name SAPERPDemoVNet
```

* Virtuális gépet hoz létre. Ebben a forgatókönyvben minden virtuális gépnek ugyanazzal a névvel kell rendelkeznie. A virtuális gépeken található SAP NetWeaver-példányok SAP-SID-je ugyanezt is megteszi. Az Azure-erőforráscsoport területén a virtuális gép nevének egyedinek kell lennie, de különböző Azure-erőforráscsoportok esetében azonos nevű virtuális gépeket futtathat. A Windows vagy a "root" rendszergazdai fiókja nem érvényes a Linux rendszerhez. Ezért új rendszergazdai felhasználónevet kell definiálni a jelszóval együtt. A virtuális gép méretét is meg kell határozni.

```
#####
# Create virtual machines using storage accounts
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image SUSE:SLES-SAP:12-SP1:latest --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image RedHat:RHEL:7.2:latest --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image "Oracle:Oracle-Linux:7.2:latest" --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --authentication-type password

#####
# Create virtual machines using Managed Disks
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image SUSE:SLES-SAP:12-SP1:latest --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image RedHat:RHEL:7.2:latest --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image "Oracle:Oracle-Linux:7.2:latest" --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --authentication-type password
```

```
#####
# Create a new virtual machine with a VHD that contains the private image that you want to use
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --os-type Windows --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --image <path to image vhd>
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --os-type Linux --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --image <path to image vhd> --authentication-type password

#####
# Create a new virtual machine with a Managed Disk Image
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --image <managed disk image id>
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --image <managed disk image id> --authentication-type password
```

* Opcionálisan hozzáadhat további lemezeket, és helyreállíthatja a szükséges tartalmakat. Minden blob nevének (a Blobok URL-címei) egyedinek kell lennie az Azure-on belül.

```
# Optional: Attach additional VHD data disks
az vm unmanaged-disk attach --resource-group $rgName --vm-name SAPERPDemo --size-gb 1023 --vhd-uri https://$rgNameLower.blob.core.windows.net/vhds/data.vhd  --new

# Optional: Attach additional Managed Disks
az vm disk attach --resource-group $rgName --vm-name SAPERPDemo --size-gb 1023 --disk datadisk --new
```

##### <a name="template"></a>Sablon

A sablonok a GitHubon használhatók az Azure-Gyorsindítás-sablonok tárházban.

* [Egyszerű linuxos virtuális gép](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
* [Egyszerű Windowsos virtuális gép](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
* [Virtuális gép rendszerképből](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

### <a name="implement-a-set-of-vms-that-communicate-within-azure"></a>Az Azure-ban kommunikáló virtuális gépek készletének implementálása

Ez a nem hibrid forgatókönyv egy tipikus forgatókönyv a betanítási és a bemutató célokra, ahol a bemutató/betanítási forgatókönyvet jelképező szoftver több virtuális gépre oszlik. A különböző virtuális gépeken telepített különböző összetevőknek kommunikálnia kell egymással. Ebben az esetben ez a forgatókönyv nem igényel helyszíni hálózati kommunikációt vagy létesítmények közötti helyzetet.

Ez a forgatókönyv az önálló virtuális gép fejezetében ismertetett telepítés kiterjesztése a jelen dokumentum [SAP NetWeaver demo/][planning-guide-7.1] betanítási forgatókönyvével. Ebben az esetben a rendszer több virtuális gépet ad hozzá egy meglévő erőforráscsoporthoz. A következő példában a betanítási környezet egy SAP ASCS/SCS virtuális gépről, egy adatbázis-kezelő rendszert futtató virtuális gépről és egy SAP Application Server-példány virtuális gépről áll.

A forgatókönyv létrehozása előtt gondolja át az alapszintű beállításokat, ahogy azt korábban már gyakorolta.

#### <a name="resource-group-and-virtual-machine-naming"></a>Erőforráscsoport és virtuális gépek elnevezése

Minden erőforráscsoport-névnek egyedinek kell lennie. Fejlessze saját elnevezési sémáját az erőforrásaihoz, például `<rg-name`> utótagot.

A virtuális gép nevének egyedinek kell lennie az erőforráscsoport területén.

#### <a name="set-up-network-for-communication-between-the-different-vms"></a>Hálózat beállítása a különböző virtuális gépek közötti kommunikációhoz

![Azure-Virtual Network belüli virtuális gépek készlete][planning-guide-figure-1900]

Ha meg szeretné akadályozni, hogy az ütközések azonos képzés/bemutató tájak klónozásával legyenek, létre kell hoznia egy Azure-Virtual Network minden környezethez. A DNS-névfeloldást az Azure biztosítja, vagy a saját DNS-kiszolgálóját az Azure-on kívül is konfigurálhatja (ezt nem tárgyalja itt). Ebben a forgatókönyvben nem konfigurálja a saját DNS-t. Egy Azure-Virtual Networkon belüli összes virtuális gép esetében engedélyezve lesz a kommunikáció a gazdagépeken keresztül.

A képzések és a bemutató tájak virtuális hálózatok általi elkülönítésének okai, és nem csupán az erőforráscsoportok:

* A beállított SAP-környezetnek saját AD-/OpenLDAP kell lennie, és a tartományi kiszolgálónak az egyes tájakhoz kell tartoznia.  
* A beállított SAP-környezet olyan összetevőket tartalmaz, amelyeknek rögzített IP-címekkel kell dolgozniuk.

Az Azure-beli virtuális hálózatokkal és azok definiálásával kapcsolatos további információkért tekintse meg [ezt a cikket][virtual-networks-create-vnet-arm-pportal].

## <a name="deploying-sap-vms-with-corporate-network-connectivity-cross-premises"></a>SAP virtuális gépek üzembe helyezése vállalati hálózati kapcsolattal (telephelyek közötti)

Egy SAP-tájat futtat, és szeretné felosztani az operációs rendszer nélküli számítógépeket a nagy teljesítményű adatbázis-kezelő kiszolgálók, a helyszíni virtualizált környezetek és az alkalmazások rétegei között, valamint kisebb, kétrétegű konfigurált SAP-rendszereket és Azure-IaaS. Az alap feltételezés az, hogy az egyik SAP-környezeten belüli SAP-rendszereknek kommunikálnia kell egymással, és a vállalaton belül számos más, a központi telepítési űrlaptól függetlenül telepített szoftver-összetevővel. Az SAP grafikus felhasználói felülettel vagy más felülettel csatlakozó végfelhasználók számára nem kell különbséget tennie a központi telepítési űrlapon. Ezek a feltételek csak akkor teljesíthetők, ha a helyszíni Active Directory/OpenLDAP és a DNS-szolgáltatások kiterjeszthetők Az Azure-rendszerekre helyek közötti/többhelyes kapcsolaton vagy magánhálózati kapcsolaton keresztül, például az Azure ExpressRoute.



### <a name="scenario-of-an-sap-landscape"></a>SAP-környezet forgatókönyve

A létesítmények közötti vagy hibrid forgatókönyvek nagyjából az alábbi ábrán látható módon vannak leírva:

![Helyek közötti kapcsolat a helyszíni és az Azure-eszközök között][planning-guide-figure-2100]

A fent látható forgatókönyv olyan helyzetet ismertet, amelyben a helyszíni

A minimális követelmény a biztonságos kommunikációs protokollok, például az SSL/TLS használata a böngésző-hozzáféréshez vagy a VPN-alapú kapcsolatok számára az Azure-szolgáltatásokhoz való hozzáféréshez. Feltételezi, hogy a vállalatok a VPN-kapcsolatot a vállalati hálózat és az Azure között eltérően kezelik. Előfordulhat, hogy egyes vállalatok üresen nyitják meg az összes portot. Előfordulhat, hogy egyes vállalatok pontosan szeretnék, hogy mely portokat kell megnyitni, stb.

Az alábbi táblázatban látható az SAP kommunikációs portok listája. Alapvetően elegendő az SAP Gateway portjának megnyitása.

<!-- sapms is prefix of a SAP service name and not a spelling error -->

| Szolgáltatás | Port neve | Példa `<nn`> = 01 | Alapértelmezett tartomány (min. max.) | Megjegyzés |
| --- | --- | --- | --- | --- |
| Kézbesítő |sapdp`<nn>` : * |3201 |3200 - 3299 |SAP-diszpécser, amelyet a Windows és a Java SAP grafikus felülete használ |
| Üzenet-kiszolgáló |sapms`<sid`> lásd: * * |3600 |ingyenes sapms`<anySID`> |SID = SAP-System-ID |
| Átjáró |sapgw`<nn`> lásd: * |3301 |ingyenes |CPIC és RFC-kommunikációhoz használt SAP Gateway |
| SAP-útválasztó |sapdp99 |3299 |ingyenes |A/etc/Services-ben csak a CI (központi példány) szolgáltatások nevei állíthatók be tetszőleges értékre a telepítés után. |

*) NN = SAP-példány száma

\* *) SID = SAP-System-ID

Az SAP-termékek által a különböző SAP-termékekhez és-szolgáltatásokhoz szükséges portokkal kapcsolatos <https://scn.sap.com/docs/DOC-17124>részletesebb információkat itt találja.
Ebben a dokumentumban az adott SAP-termékek és-forgatókönyvek számára szükséges dedikált portok megnyitása a VPN-eszközön.

Más biztonsági intézkedések a virtuális gépek ilyen esetekben történő telepítésekor egy [hálózati biztonsági csoport][virtual-networks-nsg] hozható létre a hozzáférési szabályok definiálásához.

### <a name="dealing-with-different-virtual-machine-series"></a>A különböző virtuálisgép-sorozatok kezelése

A Microsoft számos olyan virtuálisgép-típust adott hozzá, amelyek a vCPU, a memóriában vagy ennél nagyobb mértékben különböznek a rendszeren futó hardverek számától. Nem minden virtuális gép támogatja az SAP-t (lásd a támogatott VM-típusok az SAP Note [1928533]-ben című témakört). A virtuális gépek némelyike különböző gazdagép-hardver generáción fut. Ezek a gazdagépek hardveres generációi üzembe helyezése egy Azure-os méretezési egység részletességével történik. Előfordulhatnak olyan esetek, amikor a választott virtuálisgép-típusok nem futhatnak ugyanazon a méretezési egységen. A rendelkezésre állási csoport a különböző hardvereken alapuló méretezési egységekre korlátozódik.  Ha például az SAP adatbázis-kezelői réteget egy olyan E64s_v3 virtuális gépen futtatja, amely egy rendelkezésre állási csoporton belül a másodlagos adatbázis-kezelő példányt futtató virtuális géppel együtt egy HA-konfigurációban fut, nem egyszerűen leállíthatja és újraindíthatja az M-sorozatú virtuális gépet, mivel előfordulhat, hogy upg Rade a virtuális gépet. Ennek az az oka, hogy az M sorozatú virtuális gépek és a Ev3 sorozatú virtuális gépek különböző hardvereken futnak, és a különböző méretezési egységekkel rendelkeznek. Létre kell hoznia egy új rendelkezésre állási készletet, törölnie kell a másodlagos Ev3 sorozatú virtuális gépet a tárterület törlése nélkül, majd újra üzembe kell helyeznie az M sorozatú virtuális gépet az új rendelkezésre állási csoportba.

#### <a name="printing-on-a-local-network-printer-from-sap-instance-in-azure"></a>Nyomtatás helyi hálózati nyomtatón az Azure-beli SAP-példányból

##### <a name="printing-over-tcpip-in-cross-premises-scenario"></a>A TCP/IP protokollon keresztüli nyomtatás a létesítmények közötti helyzetben

A helyszíni TCP/IP-alapú hálózati nyomtatók Azure-beli virtuális gépen való beállítása megegyezik a vállalati hálózattal, feltételezve, hogy rendelkezik egy VPN-helyek közötti alagút-vagy ExpressRoute-kapcsolattal.

---
> ![Windows][Logo_Windows] Windows
>
> Ehhez tegye a következőket:
>
> * Egyes hálózati nyomtatók egy konfigurációs varázslóval jönnek létre, így egyszerűen beállítható a nyomtató egy Azure-beli virtuális gépen. Ha a nyomtatóval nem terjesztettek elő varázslót, a nyomtató beállításának manuális módja egy új TCP/IP nyomtatóportot tartalmazó port létrehozása.
> * Vezérlőpult megnyitása – > eszközök és nyomtatók – > nyomtató hozzáadása
> * Válassza a nyomtató hozzáadása TCP/IP-cím vagy állomásnév használatával lehetőséget.
> * Adja meg a nyomtató IP-címét
> * Printer port standard 9100
> * Ha szükséges, telepítse manuálisan a megfelelő nyomtatóillesztőt.
>
> ![Linux][Logo_Linux] Linux
>
> * a Windowshoz hasonlóan a standard eljárást követve telepítheti a hálózati nyomtatót
> * csak kövesse a [SUSE](https://www.suse.com/documentation/sles-12/book_sle_deployment/data/sec_y2_hw_print.html) vagy [a Red Hat nyilvános Linux-útmutatóját, és Oracle Linux](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Deployment_Guide/sec-Printer_Configuration.html) a nyomtató hozzáadásával.
>
>

---
![Hálózati nyomtatás][planning-guide-figure-2200]

##### <a name="host-based-printer-over-smb-shared-printer-in-cross-premises-scenario"></a>Gazdagép-alapú nyomtató SMB-en keresztül (megosztott nyomtató) a létesítmények közötti környezetben

A gazdagép-alapú nyomtatók nem kompatibilisek a kialakítással. A gazdagép-alapú nyomtatók azonban a hálózaton lévő számítógépek között megoszthatók, feltéve, hogy a nyomtató egy bekapcsolt számítógéphez csatlakozik. Kapcsolja össze a vállalati hálózatot helyek közötti vagy ExpressRoute, és ossza meg a helyi nyomtatót. Az SMB protokoll NetBIOS protokollt használ a DNS helyett a name szolgáltatás helyett. A NetBIOS-állomásnév eltérő lehet a DNS-állomásnévtől. A standard eset az, hogy a NetBIOS-állomásnév és a DNS-állomásnév azonos. A DNS-tartomány nem értelme a NetBIOS-név területének. Ennek megfelelően a DNS-állomásnévből és a DNS-tartományból álló teljesen minősített DNS-állomásnév nem használható a NetBIOS-névtérben.

A nyomtató megosztását a hálózat egy egyedi neve azonosítja:

* Az SMB-állomás állomásneve (mindig szükséges).
* A megosztás neve (mindig szükséges).
* A tartomány neve, ha a nyomtató megosztása nem ugyanabban a tartományban van, mint az SAP rendszer.
* Emellett szükség lehet egy felhasználónévre és egy jelszóra is a nyomtató megosztásának eléréséhez.

Útmutató:

---
> ![Windows][Logo_Windows] Windows
>
> Ossza meg a helyi nyomtatót.
> Az Azure-beli virtuális gépen nyissa meg a Windows Intézőt, és írja be a nyomtató megosztási nevét.
> A nyomtató telepítővarázslója végigvezeti a telepítési folyamaton.
>
> ![Linux][Logo_Linux] Linux
>
> Íme néhány példa arra a dokumentációra, amely a hálózati nyomtatók Linux rendszerben történő konfigurálását, illetve a Linuxon történő nyomtatással kapcsolatos fejezetet tartalmaz. Ugyanúgy működik, mint egy Azure Linux rendszerű virtuális gépen, ha a virtuális gép egy VPN része:
>
> * SLES<https://en.opensuse.org/SDB:Printing_via_SMB_(Samba)_Share_or_Windows_Share>
> * RHEL vagy Oracle Linux<https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/System_Administrators_Guide/sec-Printer_Configuration.html#s1-printing-smb-printer>
>
>

---
##### <a name="usb-printer-printer-forwarding"></a>USB-nyomtató (nyomtató továbbítása)

Az Azure-ban a Távoli asztali szolgáltatások azon képessége, hogy a felhasználók számára a távoli munkamenetben lévő helyi nyomtatóeszköz elérését ne lehessen elérni.

---
> ![Windows][Logo_Windows] Windows
>
> A Windows rendszerrel való nyomtatásról további részleteket itt talál: <https://technet.microsoft.com/library/jj590748.aspx>.
>
>

---
#### <a name="integration-of-sap-azure-systems-into-correction-and-transport-system-tms-in-cross-premises"></a>SAP Azure-rendszerek integrálása korrekciós és átviteli rendszerbe (TMS) a létesítmények közötti

Az SAP változási és átviteli rendszerét (TMS) úgy kell konfigurálni, hogy a környezet minden rendszerében exportálja és importálja a szállítási kérelmeket. Feltételezzük, hogy az SAP-rendszer (DEV) fejlesztői példányai az Azure-ban találhatók, míg a minőségbiztosítási (QA) és a produktív rendszerek (PRD-EK) a helyszínen vannak. Továbbá feltételezzük, hogy van egy központi átviteli könyvtár.

##### <a name="configuring-the-transport-domain"></a>A átviteli tartomány konfigurálása

Konfigurálja a átviteli tartományt a Transport tartományvezérlőként kijelölt rendszeren a következő témakörben leírtak szerint: [a Transport tartományvezérlő konfigurálása](https://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm). Létrejön egy rendszerfelhasználó TMSADM, és a rendszer létrehozza a szükséges RFC-célt. Ezeket az RFC-kapcsolatokat a Transaction SM59 lehet megtekinteni. Az állomásnév feloldását engedélyezni kell a szállítási tartományon belül.

Útmutató:

* A forgatókönyvben úgy döntöttünk, hogy a helyszíni QAS rendszer lesz a CTS-tartományvezérlő. Hívja meg a tranzakció STM. Megjelenik a TMS párbeszédpanel. Megjelenik a átviteli tartomány konfigurálása párbeszédpanel. (Ez a párbeszédpanel csak akkor jelenik meg, ha még nem konfigurált átviteli tartományt.)
* Győződjön meg arról, hogy az automatikusan létrehozott felhasználói TMSADM engedélyezve van (SM59-> ABAP kapcsolat TMSADM@E61.DOMAIN_E61 ->-> részletek – > segédprogramok (M) – > engedélyezési teszt). A tranzakciós STM kezdeti képernyőjén meg kell jeleníteni, hogy ez az SAP-rendszer most már működik a szállítási tartomány vezérlője, ahogy az itt látható:

![A tartományvezérlő tranzakciós STM kezdeti képernyője][planning-guide-figure-2300]

#### <a name="including-sap-systems-in-the-transport-domain"></a>SAP-rendszerek belefoglalása a szállítási tartományba

Egy átviteli tartományban az SAP-rendszer többek között a következőképpen néz ki:

* Az Azure-beli fejlesztői rendszeren lépjen az átviteli rendszer (ügyfél 000) elemre, és hívja meg a tranzakciós STM. Válasszon másik konfigurációt a párbeszédpanelről, és folytassa a rendszer belefoglalása a tartományban. Határozza meg a tartományvezérlőt célként megadott gazdagépként ([beleértve a szállítási tartomány SAP-rendszereit](https://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0c17acc11d1899e0000e829fbbd/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm)). A rendszer most már a szállítási tartományba való felvételre vár.
* Biztonsági okokból ezután vissza kell térnie a tartományvezérlőre, hogy erősítse a kérést. Válassza a rendszer áttekintése lehetőséget, és hagyja jóvá a várakozási rendszer jóváhagyását. Ezután erősítse meg a parancssort, és a konfiguráció terjesztése megtörténik.

Ez az SAP-rendszer most már tartalmazza a szállítási tartomány összes többi SAP-rendszeréhez szükséges információkat. Ugyanakkor az új SAP-rendszerhez tartozó címadatok az összes többi SAP-rendszerbe kerülnek, az SAP-rendszer pedig a Transport Control program szállítási profiljában szerepel. Győződjön meg arról, hogy az RFC-k és a hozzáférés a tartományhoz tartozó szállítási címtárhoz.

A szokásos módon folytassa a szállítási rendszerek konfigurációját a dokumentáció [módosítása és a szállítási rendszerek](https://help.sap.com/saphelp_nw70ehp3/helpdata/en/48/c4300fca5d581ce10000000a42189c/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm)című cikkben leírtak szerint.

Útmutató:

* Győződjön meg arról, hogy a helyszíni STM megfelelően van konfigurálva.
* Győződjön meg arról, hogy a virtuális gép az Azure-ban és az alvisaben is feloldható a szállítási tartományvezérlő állomásneve.
* Hívja meg a Transaction STM – > egyéb konfigurációs > a tartományba tartozó rendszert is.
* Erősítse meg a kapcsolatokat a helyszíni TMS rendszerben.
* A szokásos módon konfigurálja a szállítási útvonalakat, csoportokat és rétegeket.

A helyek közötti kapcsolattal rendelkező létesítmények közötti helyzetekben a helyszíni és az Azure közötti késés továbbra is jelentős lehet. Ha egy fejlesztési és tesztelési rendszeren keresztül hajtja végre az objektumok átvitelének folyamatát, vagy úgy gondolja, hogy a különböző rendszerekre történő átvitelt vagy támogatási csomagokat alkalmazza, akkor a központi átviteli könyvtár helyétől függ. a rendszerek némelyike nagy késleltetésű olvasást és adatírást fog tapasztalni a központi átviteli címtárban. A helyzet hasonló az SAP tájképi konfigurációhoz, ahol a különböző rendszerek különböző adatközpontokon keresztül oszlanak el, és jelentős távolságban vannak az adatközpontok között.

Ahhoz, hogy megtörténjen az ilyen késések megoldása, és hogy a rendszerek gyorsak legyenek a továbbítási címtárba vagy a szolgáltatásba való írás során, két STM-átviteli tartományt is beállíthat (egyet a helyszíni rendszerhez, egyet pedig az Azure-ban lévő rendszerekhez és az átviteli tartományokhoz. Tekintse meg ezt a dokumentációt, amely ismerteti a jelen koncepció mögötti alapelveket az SAP TMS: <https://help.sap.com/saphelp_me60/helpdata/en/c4/6045377b52253de10000009b38f889/content.htm?frameset=/en/57/38dd924eb711d182bf0000e829fbfe/frameset.htm>.

Útmutató:

* Szállítási tartomány beállítása minden helyen (a helyszínen és az Azure-ban) a Transaction STM használatával<https://help.sap.com/saphelp_nw70ehp3/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm>
* Kapcsolja össze a tartományokat egy tartományi kapcsolattal, és erősítse meg a két tartomány közötti kapcsolatot.
  <https://help.sap.com/saphelp_nw73ehp1/helpdata/en/a3/139838280c4f18e10000009b38f8cf/content.htm>
* Terjessze a konfigurációt a csatolt rendszeren.

#### <a name="rfc-traffic-between-sap-instances-located-in-azure-and-on-premises-cross-premises"></a>RFC-forgalom az Azure-ban és a helyszínen (több telephelyen) található SAP-példányok között

A helyszíni és az Azure-ban üzemelő rendszerek közötti, az RFC-adatforgalomnak működnie kell. Ha a SM59 egy olyan forrásoldali rendszeren szeretné beállítani a kapcsolódási hívási tranzakciót, amelyben meg kell határoznia egy RFC-kapcsolódást a célként megadott rendszer felé. A konfiguráció hasonló az RFC-kapcsolatok szabványos beállításához.

Feltételezzük, hogy a létesítmények közötti forgatókönyvben a virtuális gépek, amelyek olyan SAP-rendszereket futtatnak, amelyeknek kommunikálni kell egymással, ugyanabban a tartományban vannak. Ezért az SAP-rendszerek közötti RFC-kapcsolat beállítása nem különbözik a telepítési lépésektől és a helyi környezetekben található bemenettől.

#### <a name="accessing-local-fileshares-from-sap-instances-located-in-azure-or-vice-versa"></a>Helyi állnak elérése az Azure-ban található SAP-példányok vagy fordítva

Az Azure-ban található SAP-példányoknak a vállalati helyszínen lévő fájlmegosztást kell elérniük. Emellett a helyszíni SAP-példányoknak hozzá kell férniük az Azure-ban található fájlmegosztási fájlokhoz. A fájlmegosztás engedélyezéséhez konfigurálnia kell az engedélyeket és a megosztási beállításokat a helyi rendszeren. Győződjön meg arról, hogy az Azure és az adatközpont közötti VPN-vagy ExpressRoute-kapcsolat portjait nyitja meg.

## <a name="supportability"></a>Támogatási lehetőségek

### <a name="6f0a47f3-a289-4090-a053-2521618a28c3"></a>Azure monitoring megoldás az SAP-hoz

Az Azure-beli kritikus fontosságú SAP-rendszerek monitorozásának engedélyezéséhez az SAP monitoring Tools SAPOSCOL vagy az SAP-állomás ügynöke az Azure-beli virtuálisgép-szolgáltatásból származó adatmennyiséget az SAP-hez készült Azure monitoring bővítménnyel érheti el. Mivel az SAP igényei az SAP-alkalmazásokhoz voltak jellemzőek, a Microsoft úgy döntött, hogy nem általános módon implementálja a szükséges funkciókat az Azure-ban, de hagyja, hogy az ügyfelek a szükséges monitorozási összetevőket és konfigurációkat a virtuális helyükre telepítsék. Az Azure-ban futó gépek. A figyelési összetevők üzembe helyezését és életciklus-felügyeletét azonban többnyire az Azure fogja automatizálni.

#### <a name="solution-design"></a>Megoldás kialakítása

Az SAP-figyelés engedélyezéséhez kifejlesztett megoldás az Azure virtuálisgép-ügynök és a kiterjesztési keretrendszer architektúráján alapul. Az Azure-beli virtuálisgép-ügynök és-bővítmény keretrendszerének ötlete az Azure virtuálisgép-bővítmények katalógusában elérhető szoftveres alkalmazás (ok) telepítésének engedélyezése a virtuális gépen belül. A koncepció mögött az elv az, hogy lehetővé tegye a speciális funkciók üzembe helyezését egy virtuális gépre, valamint az ilyen szoftverek konfigurálását az üzembe helyezéskor.

Az Azure virtuálisgép-ügynök, amely lehetővé teszi, hogy a virtuális GÉPEN lévő adott Azure-beli virtuálisgép-bővítmények kezelését a virtuális gépeken a Azure Portal alapértelmezés szerint beinjektálja a Windows rendszerű virtuális gépekre. SUSE, Red hat vagy Oracle Linux esetén a virtuálisgép-ügynök már része az Azure Marketplace-rendszerképnek. Ha egy Linux rendszerű virtuális gépet a helyszínen az Azure-ba tölt fel, akkor manuálisan kell telepíteni a virtuálisgép-ügynököt.

Az Azure-beli monitorozási megoldás alapszintű építőelemei a következőképpen néz ki:

![Microsoft Azure bővítmény összetevői][planning-guide-figure-2400]

Ahogy az a fenti blokk ábrán is látható, az SAP-figyelési megoldás egyik része az Azure VM-rendszerkép és az Azure-bővítmények katalógusában található, amely az Azure-műveletek által felügyelt, globálisan replikált tárház. Az SAP Azure-beli implementációjában dolgozó közös SAP/MS-csapat feladata az Azure-ban, hogy az SAP-hez készült Azure monitoring Extension új verzióit közzétegyék.

Új Windows rendszerű virtuális gép telepítésekor a rendszer automatikusan hozzáadja az Azure virtuálisgép-ügynököt a virtuális géphez. Az ügynök funkciója az Azure-bővítmények betöltésének és konfigurációjának koordinálása az SAP NetWeaver-rendszerek figyeléséhez. Linux rendszerű virtuális gépek esetében az Azure-beli virtuálisgép-ügynök már része az Azure Marketplace operációsrendszer-rendszerképének.

Van azonban egy olyan lépés, amelyet továbbra is az ügyfélnek kell végrehajtania. Ez a teljesítmény-gyűjtemény engedélyezése és konfigurációja. A konfigurációhoz kapcsolódó folyamatot egy PowerShell-parancsfájl vagy CLI-parancs automatizálja. A PowerShell-szkript a Microsoft Azure script Centerben tölthető le a [telepítési útmutatóban][deployment-guide]leírtak szerint.

Az SAP-hez készült Azure monitoring megoldás általános architektúrája a következőképpen néz ki:

![Azure monitoring megoldás az SAP NetWeaver-hoz][planning-guide-figure-2500]

**A PowerShell-parancsmagok vagy a CLI-parancsok üzembe helyezés során történő használatának pontos útmutatója és részletes lépéseinek végrehajtásához kövesse a [telepítési útmutatóban][deployment-guide]megadott utasításokat.**

### <a name="integration-of-azure-located-sap-instance-into-saprouter"></a>Az Azure-beli SAP-példány integrációja a SAProuter-be

Az Azure-ban futó SAP-példányoknak elérhetőnek kell lenniük a SAProuter-től is.

![SAP-útválasztó hálózati kapcsolatok][planning-guide-figure-2600]

A SAProuter lehetővé teszi a TCP/IP-kommunikációt a résztvevő rendszerek között, ha nincs közvetlen IP-kapcsolat. Ez biztosítja azt az előnyt, hogy a kommunikációs partnerek közötti végpontok közötti kapcsolatra nincs szükség hálózati szinten. A SAProuter alapértelmezés szerint a 3299-es portot figyeli.
Az SAP-példányok SAProuter való összekapcsolásához meg kell adnia a SAProuter karakterláncot és az állomásnevet a kapcsolódási kísérletekhez.

## <a name="sap-netweaver-as-java"></a>SAP NetWeaver AS Java

Eddig a dokumentum középpontjában az SAP NetWeaver általános vagy az SAP NetWeaver ABAP-verem található. Ebben a kis szakaszban az SAP Java Stackre vonatkozó konkrét szempontok szerepelnek. Az egyik legfontosabb SAP NetWeaver Java-alapú alkalmazás az SAP Enterprise Portal. Más SAP NetWeaver-alapú alkalmazások, például az SAP PI és az SAP Solution Manager egyaránt az SAP NetWeaver ABAP és a Java stackeket használják. Ezért mindenképpen meg kell fontolni az SAP NetWeaver Java-veremmel kapcsolatos konkrét szempontokat is.

### <a name="sap-enterprise-portal"></a>SAP-Enterprise Portal

Az SAP-portál Azure-beli virtuális gépeken való telepítése nem különbözik a helyszíni telepítéstől, ha létesítmények közötti környezetben végzi az üzembe helyezést. Mivel a DNS-t a helyszíni környezetben végzik el, az egyes példányok portbeállítások a helyszínen konfiguráltként hajthatók végre. Az ebben a dokumentumban ismertetett javaslatok és korlátozások eddig olyan alkalmazásokra vonatkoznak, mint az SAP Enterprise Portal vagy az SAP NetWeaver Java stack általában.

![Feltett SAP-portál][planning-guide-figure-2700]

Egyes ügyfeleknél a speciális üzembe helyezési forgatókönyvek az SAP-Enterprise Portal közvetlen kitettségét jelentik az interneten, miközben a virtuális gép gazdagépe helyek közötti VPN-alagúton vagy ExpressRoute keresztül csatlakozik a vállalati hálózathoz. Ilyen esetben meg kell győződnie arról, hogy az adott portok nyitva vannak, és a tűzfal vagy a hálózati biztonsági csoport nem blokkolja őket. 

A kezdeti portál URI-`<Portalserver` <https://help.sap.com/saphelp_nw70ehp1/helpdata/de/a2/f9d7fed2adc340ab462ae159d19509/frameset.htm>ja http (s): >: 5XX00/irj, ahol a port a SAP által dokumentált módon jön létre.

![Végpont konfigurációja][planning-guide-figure-2800]

Ha testre szeretné szabni az SAP-Enterprise Portal URL-címét és/vagy portjait, tekintse meg ezt a dokumentációt:

* [Portál URL-címének módosítása](https://wiki.scn.sap.com/wiki/display/EP/Change+Portal+URL)
* [Alapértelmezett portszámok, portál portszámok módosítása](https://wiki.scn.sap.com/wiki/display/NWTech/Change+Default++port+numbers%2C+Portal+port+numbers)

## <a name="high-availability-ha-and-disaster-recovery-dr-for-sap-netweaver-running-on-azure-virtual-machines"></a>Magas rendelkezésre állású (HA) és vész-helyreállítási (DR) az Azure-Virtual Machines futó SAP NetWeaver-hoz

### <a name="definition-of-terminologies"></a>A terminológia definíciója

A **magas rendelkezésre állású (ha)** kifejezés általában olyan technológiákhoz kapcsolódik, amelyek lekicsinyítik az IT-szolgáltatások üzletmenet-folytonosságát a redundáns, hibatűrő vagy feladatátvételi védelemmel ellátott összetevőkön belül. adatközpont. Ebben az esetben egy Azure-régión belül.

A vész- **helyreállítási (Dr)** az IT-szolgáltatások megszakadásának minimalizálása és a helyreállításuk, de **különböző** adatközpontok esetében is megcélozza, amelyek általában több száz kilométerre találhatók. A mi esetünkben általában a különböző Azure-régiók között, amelyek ugyanabban a geopolitikai régióban találhatóak, vagy Ön az Ön által létrehozott ügyfél.

### <a name="overview-of-high-availability"></a>A magas rendelkezésre állás áttekintése

Az Azure-beli SAP magas rendelkezésre állással kapcsolatos vitát két részre lehet bontani:

* Az **Azure-infrastruktúrák magas rendelkezésre állása**, például ha számítási (VM), hálózat, tárterület stb., valamint az SAP-alkalmazások rendelkezésre állásának növeléséhez szükséges előnyök.
* **SAP-alkalmazások magas rendelkezésre állása**, például ha az SAP szoftver összetevői:
  * SAP-alkalmazások kiszolgálói
  * SAP ASCS/SCS-példány
  * ADATBÁZIS-kiszolgáló

és hogyan lehet kombinálni az Azure-infrastruktúrával.

Az Azure-ban az SAP magas rendelkezésre állása némileg különbözik az SAP magas rendelkezésre állásával egy helyszíni fizikai vagy virtuális környezetben. Az SAP-ben a következő tanulmány ismerteti a szabványos SAP magas rendelkezésre állású konfigurációkat a <https://scn.sap.com/docs/DOC-44415>virtualizált környezetekben a Windows rendszerben:. Nincs olyan sapinst-integrált SAP-HA konfiguráció a Linux rendszerhez, mint a Windows számára. Az SAP HA a helyszínen a Linux esetében további információkat talál itt: <https://scn.sap.com/docs/DOC-8541>.

### <a name="azure-infrastructure-high-availability"></a>Azure-infrastruktúra – magas rendelkezésre állás

Jelenleg a 99,9%-os egy virtuális gépre vonatkozó SLA. Ha meg szeretné tudni, hogyan nézhet ki egy adott virtuális gép rendelkezésre állása, a különböző elérhető Azure SLA-kat is létrehozhatja: <https://azure.microsoft.com/support/legal/sla/>.

A számítás alapja havi 30 nap, vagy 43200 perc. Ezért a 0,05%-os állásidő megfelel a 21,6 percnek. A szokásos módon a különböző szolgáltatások rendelkezésre állása a következőképpen fog szaporodni:

(Rendelkezésre állási szolgáltatás #1/100) * (rendelkezésre állási szolgáltatás #2/100) * (rendelkezésre állási szolgáltatás #3/100) 

Például

(99,95/100) * (99,9/100) * (99,9/100) = 0,9975 vagy a 99,75% teljes rendelkezésre állása.

#### <a name="virtual-machine-vm-high-availability"></a>Virtuális gép (VM) magas rendelkezésre állása

A virtuális gépek rendelkezésre állását a tervezett karbantartás és a nem tervezett karbantartás két típusa befolyásolja:

* A tervezett karbantartási események a Microsoft által a mögöttes Azure platformon végzett időszakos frissítések, amelyek a virtuális gépek által futtatott platform-infrastruktúra általános megbízhatóságának, teljesítményének és biztonságának javításához szükségesek.
* Nem tervezett karbantartási események történnek, ha a virtuális gép alapjául szolgáló hardver vagy fizikai infrastruktúra valamilyen módon hibát jelzett. Ez lehet helyi hálózati hiba, a helyi lemezek meghibásodása, vagy egyéb állványszintű meghibásodások. Ha a rendszer ilyen hibát észlel, az Azure platform automatikusan áttelepíti a virtuális gépet a virtuális gépet egy kifogástalan fizikai kiszolgálóra üzemeltető sérült fizikai kiszolgálóról. Ilyen esetek ritkán lépnek fel, de ezek is okozhatják a virtuális gép újraindítását.

További részleteket ebben a dokumentációban találhat:<https://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability>

#### <a name="azure-storage-redundancy"></a>Azure Storage-redundancia

Az Microsoft Azure Storage-fiókban lévő adatait a rendszer mindig replikálja a tartósság és a magas rendelkezésre állás biztosítása érdekében, még az átmeneti hardver meghibásodása esetén is az Azure Storage SLA-nak megfelelően.

Mivel az Azure Storage szolgáltatás alapértelmezés szerint három rendszerképet tárol az adatmennyiségről, a RAID5 vagy a RAID1 több Azure-lemezen nem szükséges.

További részleteket ebben a cikkben találhat:<https://azure.microsoft.com/documentation/articles/storage-redundancy/>

#### <a name="utilizing-azure-infrastructure-vm-restart-to-achieve-higher-availability-of-sap-applications"></a>Az Azure-infrastruktúra VM újraindítása az SAP-alkalmazások magasabb rendelkezésre állásának elérése érdekében

Ha úgy dönt, hogy nem használ olyan funkciókat, mint a Windows Server feladatátvételi fürtszolgáltatás (WSFC) vagy a pacemaker a Linux rendszeren (jelenleg csak a 12. és a magasabb SLES támogatott), az Azure-beli virtuális gépek újraindítását a rendszer az Azure tervezett és nem tervezett leállása elleni SAP-rendszerek biztosítására használja. fizikai kiszolgálói infrastruktúra és az általános mögöttes Azure platform.

> [!NOTE]
> Fontos megjegyezni, hogy az Azure-beli virtuális gép újraindítása elsősorban a virtuális gépeket és nem alkalmazásokat védi. A virtuális gép újraindítása nem biztosít magas rendelkezésre állást az SAP-alkalmazásokhoz, de az infrastruktúra bizonyos szintjének rendelkezésre állását nyújtja, ezért az SAP-rendszerek közvetve magasabb rendelkezésre állása. A virtuális gépek tervezett vagy nem tervezett leállása utáni újraindításához szükséges idő nem biztosít SLA-t. Ezért ez a magas rendelkezésre állású módszer nem alkalmas az olyan SAP-rendszerek kritikus összetevőire, mint például az (A) SCS vagy az adatbázis-kezelő rendszer.
>
>

Egy másik fontos infrastruktúra-elem a magas rendelkezésre álláshoz a Storage. Például az Azure Storage SLA 99,9%-os rendelkezésre állást biztosít. Ha egy üzembe helyezi az összes virtuális gépet egyetlen Azure Storage-fiókba, az Azure Storage-ban rejlő lehetőségek nem lesznek elérhetők az Azure Storage-fiókba helyezett összes virtuális gép, valamint a virtuális gépeken belül futó összes SAP-összetevő számára.  

Ahelyett, hogy az összes virtuális gépet egyetlen Azure Storage-fiókba helyezné, az egyes virtuális gépekhez dedikált Storage-fiókokat is használhat, így a teljes virtuális gép és az SAP-alkalmazás rendelkezésre állását több független Azure Storage-fiók használatával növelheti.

Az Azure Managed Disks automatikusan a virtuális gép tartalék tartományába kerülnek, amelyhez csatolva vannak. Ha két virtuális gépet helyez egy rendelkezésre állási csoportba, és Managed Disks használ, akkor a platform gondoskodik a Managed Disks különböző tartalék tartományokra való terjesztéséről is. Ha Premium Storage használatát tervezi, javasoljuk, hogy a lemezek kezelése lehetőséget is használja.

Az Azure-infrastruktúrát és a Storage-fiókokat használó SAP NetWeaver rendszer minta architektúrája a következőképpen néz ki:

![Az Azure Infrastructure HA használatával magasabb rendelkezésre állást érhet el az SAP-alkalmazások számára][planning-guide-figure-2900]

Az Azure-infrastruktúrát és Managed Diskst használó SAP NetWeaver rendszer minta architektúrája a következőképpen nézhet ki:

![Az Azure Infrastructure HA használatával magasabb rendelkezésre állást érhet el az SAP-alkalmazások számára][planning-guide-figure-2901]

A kritikus fontosságú SAP-összetevők esetében eddig a következőt értünk el:

* Az SAP-alkalmazások kiszolgálóinak magas rendelkezésre állása (AS)

  Az SAP Application Server-példányok redundáns összetevők. Minden SAP mint példány a saját virtuális gépén van telepítve, amely egy másik Azure-beli hiba-és frissítési tartományban fut (lásd: [][planning-guide-3.2.1] a tartalék tartományok és a [frissítési tartományok][planning-guide-3.2.2]fejezetei). Ezt az Azure rendelkezésre állási csoportok használatával biztosítjuk (lásd az Azure-beli [rendelkezésre állási készletek][planning-guide-3.2.3]című fejezetet). Egy Azure-beli hiba vagy frissítési tartomány lehetséges tervezett vagy nem tervezett leállása miatt a korlátozott számú virtuális gép nem lesz elérhető az SAP-példányokkal.

  A rendszer minden egyes SAP-példányt saját Azure Storage-fiókba helyez el – az egyik Azure Storage-fiók lehetséges nem érhető el, mert az SAP-példánnyal csak egy virtuális gép lesz elérhető. Vegye azonban figyelembe, hogy az Azure Storage-fiókok egyetlen Azure-előfizetésen belül vannak korlátozva. Annak érdekében, hogy az (A) SCS-példány automatikus indítása a virtuális gép újraindítása után történjen, ügyeljen arra, hogy az (A) SCS-példány indítási profiljában a ( [][planning-guide-11.5]
  További részletekért olvassa el az [SAP-alkalmazások kiszolgálóinak magas rendelkezésre állásáról][planning-guide-11.4.1] szóló fejezetet is.

  Még ha Managed Disks is használ, ezeket a lemezeket egy Azure Storage-fiókban is tárolja a rendszer, és a tárolási leállás esetén nem érhető el.

* *Magasabb szintű* Az SAP (A) SCS-példány rendelkezésre állása

  Itt az Azure-beli virtuális gép újraindítását használjuk a virtuális gép a telepített SAP (A) SCS-példánnyal való védelemmel való ellátásához. Az Azure-beli kiszolgálók tervezett vagy nem tervezett leállása esetén a virtuális gépek egy másik elérhető kiszolgálón lesznek újraindítva. Ahogy korábban említettük, az Azure-beli virtuális gép újraindítása elsődlegesen védi a virtuális gépeket és nem alkalmazásokat, ebben az esetben az (A) SCS-példányt. A virtuális gép újraindításakor a SAP (A) SCS-példány közvetettan magasabb rendelkezésre állása érhető el. Ha az (A) SCS-példány automatikus indítását szeretné biztosítani a virtuális gép újraindítása után, ügyeljen arra, hogy a (A) SCS-példány indítási profiljában a ( [][planning-guide-11.5] Ez azt jelenti, hogy az (A) SCS-példány egyetlen virtuális gépen futtatott egyetlen meghibásodási pont (SPOF) lesz a teljes SAP-környezet rendelkezésre állásának lezáró tényezője.

* *Magasabb szintű* Az adatbázis-kezelő kiszolgáló rendelkezésre állása

  Itt az SAP (A) SCS-példány használati esetéhez hasonlóan az Azure VM újraindítását használjuk a virtuális gép telepített adatbázis-kezelő szoftverrel való védelemmel való ellátásához, és a virtuális gépek újraindításával magasabb rendelkezésre állást biztosítunk az adatbázis-kezelő szoftverek számára
  Az egyetlen virtuális gépen futó adatbázis-kezelők is SPOF, és ez a teljes SAP-környezet rendelkezésre állását meghatározó felmondási tényező.

### <a name="sap-application-high-availability-on-azure-iaas"></a>SAP-alkalmazások magas rendelkezésre állása az Azure IaaS

A teljes SAP-rendszer magas rendelkezésre állásának biztosítása érdekében minden kritikus SAP-rendszerösszetevőt, például a redundáns SAP-alkalmazások kiszolgálóit és az egyedi összetevőket (például egy meghibásodási pont), például az SAP (A) SCS-példányt és az adatbázis-kezelő rendszert kell védetté tenni.

#### <a name="5d9d36f9-9058-435d-8367-5ad05f00de77"></a>Magas rendelkezésre állás az SAP-alkalmazások kiszolgálói számára

Az SAP-alkalmazások kiszolgálói/párbeszédpanel-példányai esetében nem szükséges egy adott magas rendelkezésre állási megoldásra gondolni. A magas rendelkezésre állás a redundancia révén érhető el, így a különböző virtuális gépeken is elég sok más. Mindegyiket ugyanabba az Azure rendelkezésre állási készletbe kell helyezni, hogy a virtuális gépek a tervezett karbantartási állásidő során egyidejűleg frissüljenek. Az Azure-méretezési egységen belül a különböző frissítési és tartalék tartományokra épülő alapszintű funkciók már bekerültek a [frissítési tartományok][planning-guide-3.2.2]fejezetbe. Az Azure rendelkezésre állási csoportjait a jelen dokumentum Azure-beli [rendelkezésre állási csoportjaiban][planning-guide-3.2.3] mutatjuk be.

Nem áll rendelkezésre végtelen számú hiba-és frissítési tartomány, amelyet egy Azure-beli méretezési egységen belül használhat egy Azure-beli rendelkezésre állási csoport. Ez azt jelenti, hogy több virtuális gép egy rendelkezésre állási csoportba helyezése, amely hamarabb vagy később egynél több virtuális gép véget ér ugyanabban a hiba-vagy frissítési tartományban.

Néhány SAP Application Server-példány üzembe helyezése a dedikált virtuális gépeken, és feltételezve, hogy öt frissítési tartományt kaptunk, az alábbi kép a végén jelennek meg. A rendelkezésre állási csoporton belüli hibák és frissítési tartományok tényleges maximális száma a jövőben változhat:

![Az Azure-beli SAP-alkalmazások kiszolgálói][planning-guide-figure-3000]

További részleteket ebben a dokumentációban találhat:<https://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability>

#### <a name="high-availability-for-sap-central-services-on-azure"></a>Magas rendelkezésre állás az SAP központi szolgáltatásaihoz az Azure-ban

Az Azure-beli SAP központi szolgáltatások magas rendelkezésre állású architektúrája esetében tekintse meg a [magas rendelkezésre állású architektúrát és az SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios) – beléptetési adatokat ismertető cikket. A cikk az adott operációs rendszerek részletesebb leírására mutat.

#### <a name="high-availability-for-the-sap-database-instance"></a>Magas rendelkezésre állás az SAP-adatbázis példánya számára

A tipikus SAP adatbázis-kezelő HA a telepítő két adatbázis-kezelői virtuális gépen alapul, ahol az adatbázis-kezelő magas rendelkezésre állási funkciója az adatok replikálására szolgál az aktív adatbázis-kezelő példányról a második virtuális gépre egy passzív adatbázis-kezelő példányba.

A magas rendelkezésre állás és a vész-helyreállítási funkciók általában az adatbázis-kezelő rendszerekben, valamint az egyes adatbázis-kezelők [telepítési útmutatójában][dbms-guide]olvashatók.

#### <a name="end-to-end-high-availability-for-the-complete-sap-system"></a>A teljes SAP-rendszer végpontok közötti magas rendelkezésre állása

Íme két példa egy teljes SAP NetWeaver HA-architektúrára az Azure-ban – egy a Windowshoz, egy pedig a Linux rendszerhez.

Csak a nem felügyelt lemezek: Előfordulhat, hogy az alább ismertetett fogalmakat egy kis mennyiségű SAP-rendszer központi telepítésekor kell megsérteni, és a telepített virtuális gépek száma meghaladja az előfizetések maximálisan engedélyezett tárolási fiókját. Ilyen esetekben a virtuális gépeket egy Storage-fiókon belül kell egyesíteni. Ezt általában úgy teheti meg, hogy kombinálja a különböző SAP-rendszerekben futó SAP-alkalmazási rétegbeli virtuális merevlemezeket.  Egy Azure Storage-fiókban különböző SAP-rendszerekkel rendelkező különböző adatbázis-kezelői virtuális gépek eltérő virtuális merevlemezeit is kombináljuk. Az Azure Storage-fiókok IOPS korlátainak megőrzése (<https://azure.microsoft.com/documentation/articles/storage-scalability-targets>)


##### <a name="windowslogo_windows-ha-on-windows"></a>![Windows][Logo_Windows] HA Windows rendszeren

![SAP NetWeaver Application HA architektúra SQL Server az Azure IaaS][planning-guide-figure-3200]

Az SAP NetWeaver rendszerhez az alábbi Azure-összeállítások használhatók az infrastruktúra-problémák és a gazdagép-javítások hatásának csökkentése érdekében:

* A teljes rendszer üzembe helyezése az Azure-ban (kötelező-adatbázis-kezelő réteg, (A) SCS-példány, és az alkalmazási réteg teljes futtatásának ugyanazon a helyen kell futnia).
* A teljes rendszeren egy Azure-előfizetésen belül fut (kötelező).
* A teljes rendszeren egy Azure-Virtual Network (kötelező) belül fut.
* Az egyik SAP-rendszer virtuális gépei három rendelkezésre állási csoportba való elkülönítése az ugyanahhoz a Virtual Networkhoz tartozó összes virtuális géphez is lehetséges.
* Minden réteghez (például adatbázis-kezelő, ASCS, alkalmazáskiszolgáló) dedikált rendelkezésre állási készletet kell használnia.
* Az egyik SAP-rendszer adatbázis-kezelő példányait futtató virtuális gépek egyetlen rendelkezésre állási csoportba tartoznak. Feltételezzük, hogy a rendszer több adatbázis-kezelő példányt futtató virtuális gépet használ, mivel a natív adatbázis-kezelők magas rendelkezésre állási funkciói használhatók, mint például a SQL Server AlwaysOn vagy az Oracle-adatvédelem.
* Az adatbázis-kezelő példányokat futtató összes virtuális gép a saját Storage-fiókját használja. Az adatbázis-kezelői adatok és naplófájlok egy Storage-fiókból egy másikba replikálódnak az adatbázis-kezelő magas rendelkezésre állási funkciói segítségével, amelyek szinkronizálják az adatokat. Ha egy Storage-fiók nem áll rendelkezésre, az egy SQL Windows-fürtcsomópont nem lesz elérhető, a teljes SQL Server szolgáltatás nem.
* Minden olyan virtuális gép, amely (A) az egyik SAP-rendszer SCS-példánya egy rendelkezésre állási csoportba tartoznak. A virtuális gépeken belül egy Windows Server feladatátvevő fürt (WSFC) van konfigurálva, hogy megvédje az (A) SCS-példányt.
* Minden (A) SCS-példányt futtató virtuális gép a saját Storage-fiókját használja. Egy Az SCS-példány fájljai és az SAP globális mappája egy Storage-fiókból egy másik Storage-fiókba replikálódik a SIOS DataKeeper-replikáció használatával. Ha egy Storage-fiók nem áll rendelkezésre, az egy (A) SCS Windows-fürtcsomópont nem lesz elérhető, de nem az egész (A) SCS szolgáltatás.
* Az SAP Application Server réteget jelölő összes virtuális gép egy harmadik rendelkezésre állási csoportba kerül.
* Az összes SAP-alkalmazáskiszolgáló futtató virtuális gép a saját Storage-fiókját használja. Ha az egyik Storage-fiók nem áll rendelkezésre, az egy SAP-alkalmazáskiszolgáló nem lesz elérhető, ahol más SAP-alkalmazáskiszolgáló továbbra is fut.

A következő ábra ugyanazt a tájat mutatja Managed Disks használatával.

![SAP NetWeaver Application HA architektúra SQL Server az Azure IaaS][planning-guide-figure-3201]

##### <a name="linuxlogo_linux-ha-on-linux"></a>![Linux][Logo_Linux] HA Linuxon

A Linuxon az Azure-on futó SAP HA architektúrája alapvetően ugyanaz, mint a fentiekben leírtak szerint. Tekintse meg a támogatott magas rendelkezésre állású megoldások listáját a [1928533] -es SAP-megjegyzésben.

### <a name="4e165b58-74ca-474f-a7f4-5e695a93204f"></a>Az Automatikus indítás használata az SAP-példányok esetében

Az SAP az operációs rendszernek a virtuális gépen való elindítása után azonnal elindíthatja az SAP-példányokat. A pontos lépéseket dokumentálták az SAP Tudásbázis [1909114]-es cikkében. Az SAP azonban nem javasolja, hogy a beállítást többé ne használja, mert a példányok újraindításának sorrendje nincs szabályozva, feltételezve, hogy több virtuális gépnek van érintett vagy több példánya van egy virtuális gépen. Feltételezve, hogy egy virtuális gépen egy SAP Application Server-példány tipikus Azure-forgatókönyve van, és egyetlen virtuális gép esetében az újraindítást követően az Automatikus indítás nem kritikus, és a következő paraméter hozzáadásával engedélyezhető:

    Autostart = 1

Az SAP ABAP és/vagy a Java példány indítási profiljába.

> [!NOTE]
> Az Automatikus indítás paraméternek van néhány bukása is. Részletesebben a paraméter egy SAP ABAP vagy Java-példány kezdetét indítja el, ha a példány kapcsolódó Windows/Linux szolgáltatása elindult. Ez természetesen akkor is igaz, ha az operációs rendszer elindul. Az SAP-szolgáltatások újraindítása azonban az SAP-szoftverek életciklus-kezelési funkcióinak, például az ÖSSZEGnek vagy más frissítéseknek és frissítéseknek is gyakori funkciója. Ezek a funkciók nem várnak a példányok automatikus újraindítására. Ezért az Automatikus indítás paramétert le kell tiltani az ilyen feladatok futtatása előtt. Az Automatikus indítás paraméter nem használható a fürtözött, például ASCS/SCS/CI típusú SAP-példányokhoz.
>
>

Az SAP-példányok automatikus indításával kapcsolatban itt talál további információt:

* [Az SAP elindítása/leállítása a UNIX-kiszolgáló indításával/leállításával együtt](https://scn.sap.com/community/unix/blog/2012/08/07/startstop-sap-along-with-your-unix-server-startstop)
* [Az SAP NetWeaver felügyeleti ügynökök elindítása és leállítása](https://help.sap.com/saphelp_nwpi711/helpdata/en/49/9a15525b20423ee10000000a421938/content.htm)
* [A HANA-adatbázis automatikus indításának engedélyezése](http://www.freehanatutorials.com/2012/10/how-to-enable-auto-start-of-hana.html)

### <a name="larger-3-tier-sap-systems"></a>Nagyobb 3 szintű SAP-rendszerek
A 3 rétegbeli SAP-konfigurációk magas rendelkezésre állási szempontjai már a korábbi szakaszokban lettek megtárgyalva. De mi a helyzet azokon a rendszereken, amelyekben az adatbázis-kezelő kiszolgáló követelményei túl nagyok ahhoz, hogy az Azure-ban legyenek, de az SAP-alkalmazás rétege üzembe helyezhető az Azure-ban

#### <a name="location-of-3-tier-sap-configurations"></a>A háromrétegű SAP-konfigurációk helye
Nem támogatott az alkalmazási réteg, illetve az alkalmazás-és az adatbázis-kezelői réteg felosztása a helyszíni és az Azure között. Az SAP-rendszer telepítése vagy üzembe helyezése teljesen megtörtént a helyszínen vagy az Azure-ban. Az is előfordulhat, hogy néhány alkalmazás-kiszolgáló nem fut a helyszínen, és mások az Azure-ban. Ez a vitafórum kiindulási pontja. Nem támogatjuk az SAP-rendszer és a két különböző Azure-régióban üzembe helyezett SAP Application Server-réteg adatbázis-kezelői összetevőjének támogatását is. Például az USA nyugati régiójában és az SAP-alkalmazás rétegében, az USA középső régiójában. Az ilyen konfigurációk nem támogatásának oka az SAP NetWeaver architektúra késleltetési érzékenysége.

A tavalyi év folyamán azonban az adatközpont-partnerek közös helyszíneket fejlesztettek ki az Azure-régiók között. Ezek a közös helyszínek gyakran az Azure-régióban található fizikai Azure-adatközpontok közelében vannak. A ExpressRoute-ből az Azure-ba történő közös elhelyezésű adategységek rövid távolsága és összekapcsolása olyan késést eredményezhet, amely kisebb, mint a 2ms. Ilyen esetekben az adatbázis-kezelő réteget (beleértve a Storage SAN/NAS-t) a közös helyen és az Azure-beli SAP-alkalmazás rétegében lehet megkeresni. [HANA nagyméretű példányai](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture). 

### <a name="offline-backup-of-sap-systems"></a>Az SAP Systems offline biztonsági mentése
A kiválasztott SAP-konfigurációtól (kétrétegű vagy 3 szintű) függ, hogy szükség van-e biztonsági mentésre. A virtuális gép tartalma, valamint az adatbázis biztonsági mentése. Az adatbázis-KEZELŐi szolgáltatással kapcsolatos biztonsági másolatok várhatóan adatbázis-metódusokkal lesznek végrehajtva. A különböző adatbázisok részletes leírását az [adatbázis][dbms-guide]-kezelői útmutatóban találja. Másfelől az SAP-adatokat kapcsolat nélküli módban is lehet készíteni (beleértve az adatbázis tartalmát is), ahogyan azt a következő szakaszban leírtak szerint a jelen szakaszban vagy az interneten is ismertetjük.

Az offline biztonsági mentés alapvetően a virtuális gép leállítását igényli a Azure Portalon keresztül, valamint az alap VM-lemez és az összes csatlakoztatott lemez másolatát a virtuális géphez. Ez megőrizheti a virtuális gép és a hozzá tartozó lemez időpontját. Azt javasoljuk, hogy a biztonsági mentéseket egy másik Azure Storage-fiókba másolja. Ezért alkalmazni kell a jelen dokumentum [Azure Storage-fiókok közötti másolása][planning-guide-5.4.2] című fejezetben ismertetett eljárást.
A Leállítás mellett a Azure Portal használatával is megteheti a PowerShell vagy a parancssori felület segítségével, az itt leírtak szerint:<https://azure.microsoft.com/documentation/articles/virtual-machines-deploy-rmtemplates-powershell/>

Az állapot visszaállítása magában foglalhatja az alapszintű virtuális gép, valamint az alapszintű virtuális gép és a csatlakoztatott lemezek eredeti lemezének törlését, a mentett lemezek másolását az eredeti Storage-fiókba vagy az erőforráscsoport számára a felügyelt lemezek esetében, majd a rendszer újbóli üzembe helyezését.
Ez a cikk egy példát mutat be a folyamat PowerShellben való megírására:<http://www.westerndevs.com/azure-snapshots/>

Ügyeljen arra, hogy új SAP-licencet telepítsen a virtuális gép biztonsági mentésének visszaállítása óta, a fentiekben leírtak szerint új hardver-kulcsot hoz létre.

### <a name="online-backup-of-an-sap-system"></a>SAP-rendszer online biztonsági mentése

Az adatbázis-kezelő rendszer biztonsági mentését az adatbázis-kezelői útmutatóban [][dbms-guide]leírtak szerint, az adatbázis-kezelői szolgáltatással kapcsolatos módszerek

Az SAP-rendszeren belüli más virtuális gépek biztonsági mentését az Azure virtuális gépek biztonsági mentési funkciója segítségével végezheti el. Az Azure-beli virtuális gépek biztonsági mentése standard módszer egy teljes virtuális gép biztonsági mentésére az Azure-ban. Azure Backup tárolja a biztonsági másolatokat az Azure-ban, és lehetővé teszi a virtuális gép visszaállítását.

> [!NOTE]
> A virtuális gépek biztonsági mentésének használatakor a DEC 2015 nem őrzi meg az SAP licenceléshez használt egyedi VM-azonosítót. Ez azt jelenti, hogy egy virtuális gép biztonsági mentésének visszaállításához új SAP-licenckulcs telepítését kell megadnia, mivel a visszaállított virtuális gép új virtuális gép, és nem helyettesíti a korábban mentett példányt.
>
> ![Windows][Logo_Windows] Windows
>
> Elméletileg az adatbázisokat futtató virtuális gépeket konzisztens módon lehet biztonsági másolatot készíteni, és ha az adatbázis-kezelő rendszer támogatja a Windows VSS (Kötet árnyékmásolata szolgáltatás <https://msdn.microsoft.com/library/windows/desktop/bb968832(v=vs.85).aspx>) rendszert, például SQL Server.
> Vegye azonban figyelembe, hogy az Azure-beli virtuális gépek biztonsági mentései időponthoz tartozó adatbázis-visszaállítások nem lehetségesek. Ezért az ajánlott, hogy az adatbázisok biztonsági másolatait az Azure virtuális gépek biztonsági mentésének használata helyett az adatbázis-KEZELŐi funkcióval végezze.
>
> Az Azure-beli virtuális gépek biztonsági mentésének megismeréséhez <https://docs.microsoft.com/azure/backup/backup-azure-vms>olvassa el a következőt:.
>
> A többi lehetőség az Azure-beli virtuális gépeken telepített Microsoft Data Protection Manager kombinációjának használata, valamint az adatbázisok biztonsági mentésének és visszaállításának Azure Backup. További információt itt találhat: <https://docs.microsoft.com/azure/backup/backup-azure-dpm-introduction>.  
>
> ![Linux][Logo_Linux] Linux
>
> A Linux rendszerben a Windows VSS nem megfelelő. Ezért csak a fájl-konzisztens biztonsági mentések lehetségesek, de nem az alkalmazás-konzisztens biztonsági másolatok. Az SAP adatbázis-kezelői biztonsági mentését az adatbázis-kezelői funkciók használatával kell elvégezni. Az SAP-vel kapcsolatos adatait tartalmazó fájlrendszer menthető például a Tar használatával az itt leírtak szerint:<https://help.sap.com/saphelp_nw70ehp2/helpdata/en/d3/c0da3ccbb04d35b186041ba6ac301f/content.htm>
>
>

### <a name="azure-as-dr-site-for-production-sap-landscapes"></a>Azure as DR-hely az éles környezetben futó SAP-tájakhoz

2014 közepe óta a Hyper-V, a System Center és az Azure különböző összetevőinek bővítményei lehetővé teszik az Azure-t DR-helyként a helyszínen futó virtuális gépek számára a Hyper-V alapján.

A megoldás üzembe helyezésének részletes leírását itt találja: <https://blogs.msdn.com/b/saponsqlserver/archive/2014/11/19/protecting-sap-solutions-with-azure-site-recovery.aspx>.

## <a name="summary"></a>Összegzés

Az Azure-beli SAP-rendszerek magas rendelkezésre állásának legfontosabb pontjai a következők:

* Ebben az időpontban az SAP egypontos meghibásodása nem biztosítható pontosan ugyanúgy, mint a helyszíni környezetekben. Ennek az az oka, hogy a megosztott lemezes fürtök még nem hozhatók létre az Azure-ban harmadik féltől származó szoftverek használata nélkül.
* Az adatbázis-kezelő réteghez olyan adatbázis-kezelői funkciót kell használnia, amely nem a megosztott lemezes fürt technológiára támaszkodik. A részletek leírását az [adatbázis][dbms-guide]-kezelői útmutatóban találja.
* Az Azure-infrastruktúra vagy a gazdagép karbantartása során felmerülő problémák hatásának csökkentése érdekében az Azure rendelkezésre állási készleteit kell használnia:
  * Azt javasoljuk, hogy egy rendelkezésre állási csoport legyen az SAP-alkalmazás rétegében.
  * Azt javasoljuk, hogy az SAP adatbázis-kezelő rétegéhez külön rendelkezésre állási készlet legyen.
  * NEM ajánlott ugyanazt a rendelkezésre állási készletet alkalmazni a különböző SAP-rendszerű virtuális gépekhez.
  * A prémium szintű Managed Disks használata javasolt.
* Az SAP adatbázis-kezelő rétegének biztonsági mentéséhez tekintse meg az [adatbázis-kezelői útmutató című útmutatót][dbms-guide].
* Az SAP-párbeszédpanel példányainak biztonsági mentése kevés értelmet, mivel általában gyorsabb az egyszerű párbeszédpanel-példányok újratelepítése.
* A virtuális gép biztonsági mentése, amely tartalmazza az SAP-rendszer globális könyvtárát, valamint a különböző példányok összes profilját, a Windows biztonsági mentésével, vagy például a Linux-alapú tar-vel kell elvégezni. Mivel a Windows Server 2008 (R2) és a Windows Server 2012 (R2) között különbségek vannak, így könnyebben készíthető biztonsági mentés a legújabb Windows Server-kiadásokkal, javasoljuk, hogy Windows Server 2012 (R2) operációs rendszert futtasson Windows vendég operációs rendszerként.

## <a name="next-steps"></a>További lépések
Olvassa el a cikkeket:

- [Azure Virtual Machines üzembe helyezés az SAP NetWeaver-ben](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [Az Azure Virtual Machines adatbázis-kezelő üzembe helyezésének szempontjai az SAP-munkaterheléshez](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [SAP HANA infrastruktúra-konfigurációk és-műveletek az Azure-on] (https://docs.microsoft.com/
- azure/virtual-machines/workloads/sap/hana-vm-operations)
