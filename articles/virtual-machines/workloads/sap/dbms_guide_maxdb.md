---
title: Azure virtuális gépeken MaxDB liveCache és webtartalom-kiszolgáló telepítési SAP |} A Microsoft Docs
description: SAP MaxDB, liveCache, and Content Server deployment on Azure
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 83319118c778d89749b1eb5d5fd792a5200c19c5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60836034"
---
# <a name="sap-maxdb-livecache-and-content-server-deployment-on-azure-vms"></a>SAP MaxDB, liveCache és a webtartalom-kiszolgáló üzembe helyezés az Azure virtuális gépekhez

[767598]: https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]: https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1114181]:https://launchpad.support.sap.com/#/notes/1114181
[1139904]: https://launchpad.support.sap.com/#/notes/1139904
[1173395]: https://launchpad.support.sap.com/#/notes/1173395
[1245200]:https://launchpad.support.sap.com/#/notes/1245200
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1558958]:https://launchpad.support.sap.com/#/notes/1558958
[1585981]:https://launchpad.support.sap.com/#/notes/1585981
[1588316]:https://launchpad.support.sap.com/#/notes/1588316
[1590719]:https://launchpad.support.sap.com/#/notes/1590719
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1605680]:https://launchpad.support.sap.com/#/notes/1605680
[1619720]:https://launchpad.support.sap.com/#/notes/1619720
[1619726]: https://launchpad.support.sap.com/#/notes/1619726
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
[1909114]:https://launchpad.support.sap.com/#/notes/1909114
[1922555]:https://launchpad.support.sap.com/#/notes/1922555
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[1941500]:https://launchpad.support.sap.com/#/notes/1941500
[1956005]:https://launchpad.support.sap.com/#/notes/1956005
[1973241]:https://launchpad.support.sap.com/#/notes/1973241
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2069760]:https://launchpad.support.sap.com/#/notes/2069760
[2121797]:https://launchpad.support.sap.com/#/notes/2121797
[2134316]:https://launchpad.support.sap.com/#/notes/2134316
[2171857]:https://launchpad.support.sap.com/#/notes/2171857
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

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
[dbms-guide-managed-disks]:dbms-guide.md#f42c6cb5-d563-484d-9667-b07ae51bce29

[dbms-guide-figure-100]:media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:media/dbms_maxdb_deployment_guide/900-sap-cache-server-on-premises.png

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

[msdn-set-azurermvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]:planning-guide.md 
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff 
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f 
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 
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
[planning-guide-figure-2500]:media/virtual-machines-shared-sap-planning-guide/2500-vm-extension-details.png
[planning-guide-figure-2600]:media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-300]:media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-400]:media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd 
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f 

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
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]:../../windows/disks-types.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/storage-scalability-targets.md
[storage-use-azcopy]:../../../storage/common/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:../../linux/attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../resource-manager-deployment-model.md
[virtual-machines-azurerm-versus-azuresm]:../../../resource-manager-deployment-model.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md 
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md 
[virtual-machines-linux-agent-user-guide]:../../linux/agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../linux/agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager-capture]:../../linux/capture-image.md#step-2-capture-the-vm
[virtual-machines-linux-configure-raid]:../../linux/configure-raid.md
[virtual-machines-linux-configure-lvm]:../../linux/configure-lvm.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:../../virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:../../linux/suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:../../linux/redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:../../linux/add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:../../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:../../linux/quick-create-cli.md
[virtual-machines-linux-update-agent]:../../linux/update-agent.md
[virtual-machines-manage-availability-linux]:../../linux/manage-availability.md
[virtual-machines-manage-availability-windows]:../../windows/manage-availability.md
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
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/resources/templates/sql-server-2014-alwayson-existing-vnet-and-ad/
[virtual-network-deploy-multinic-arm-cli]:../linux/multiple-nics.md
[virtual-network-deploy-multinic-arm-ps]:../windows/multiple-nics.md
[virtual-network-deploy-multinic-arm-template]:../../../virtual-network/template-samples.md
[virtual-networks-configure-vnet-to-vnet-connection]:../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../../../virtual-network/manage-virtual-network.md#create-a-virtual-network
[virtual-networks-manage-dns-in-vnet]:../../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics]:../../../virtual-network/virtual-network-deploy-multinic-classic-ps.md
[virtual-networks-nsg]:../../../virtual-network/security-overview.md
[virtual-networks-reserved-private-ip]:../../../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]:../../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]:../../../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]:../../../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]:../../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]:../../../vpn-gateway/vpn-gateway-plan-design.md
[vpn-gateway-site-to-site-create]:../../../vpn-gateway/vpn-gateway-site-to-site-create.md
[vpn-gateway-vpn-faq]:../../../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../../../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../../../xplat-cli-azure-resource-manager.md




Ez a dokumentum MaxDB liveCache és az Azure IaaS webtartalom-kiszolgáló üzembe helyezésekor érdemes figyelembe venni számos különböző területekre terjed ki. Ebben a dokumentumban előfeltételeként rendelkezik olvassa el a dokumentumot [SAP számítási feladatok Azure virtuális gépek DBMS üzembe szempontjai](dbms_guide_general.md) lévő többi útmutató és a [SAP számítási feladatok az Azure-dokumentáció](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started). 

## <a name="specifics-for-the-sap-maxdb-deployments-on-windows"></a>Az SAP MaxDB központi telepítések a Windows-adatait
### <a name="sap-maxdb-version-support-on-azure"></a>SAP MaxDB verzió támogatása az Azure-ban
SAP az SAP MaxDB 7.9 vagy újabb verziója jelenleg támogatja SAP NetWeaver-alapú termékeknél az Azure-ban való használatra. Kizárólag az SAP Service Marketplace-en keresztül, minden frissítés SAP MaxDB kiszolgáló, vagy az SAP NetWeaver-alapú termékeknél használandó JDBC és az ODBC illesztőprogramjai biztosított <https://support.sap.com/swdc>.
Általános információk a SAP NetWeaver futó SAP MaxDB található <https://www.sap.com/community/topic/maxdb.html>.

### <a name="supported-microsoft-windows-versions-and-azure-vm-types-for-sap-maxdb-dbms"></a>A Microsoft Windows-verziók és az Azure-beli Virtuálisgép-típusok támogatott SAP MaxDB adatbázis-kezelő
A Microsoft Windows támogatott verzióját az Azure-beli SAP MaxDB DBMS című témakörben talál:

* [SAP-termék rendelkezésre állási mátrix (PAM)][sap-pam]
* SAP Note [1928533]

Erősen ajánlott az operációs rendszer a Microsoft Windows, amely Microsoft Windows 2016 legújabb verzióját használja.

### <a name="available-sap-maxdb-documentation-for-maxdb"></a>Rendelkezésre álló SAP MaxDB MaxDB dokumentációja
A frissített listáját SAP MaxDB dokumentációjában találhatja az alábbi SAP-Jegyzetnek [767598]

### <a name="sap-maxdb-configuration-guidelines-for-sap-installations-in-azure-vms"></a>SAP MaxDB beállítási útmutatója az Azure-beli virtuális gépeken SAP-telepítések
#### <a name="b48cfe3b-48e9-4f5b-a783-1d29155bd573"></a>Tárolási konfiguráció
Az Azure storage ajánlott eljárások az SAP MaxDB hajtsa végre a fejezet az említett általános javaslatok [RDBMS-környezetekben virtuális gépek tárolószerkezet](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#65fa79d6-a85f-47ee-890b-22e794f51a64).

> [!IMPORTANT]
> Más adatbázisok, például SAP MaxDB adat és naplófájl is van. Azonban az SAP MaxDB terminológiája megfelelő kifejezés "kötete" (nem "file"). Például nincsenek SAP MaxDB adatköteteket és naplózási köteteket. Ne keverje össze ezeket az operációsrendszer-lemez kötetek. 
> 
> 

Röviden kell:

* Ha Azure Storage-fiókokat használ, állítsa be az Azure storage-fiókot, amely az SAP MaxDB adat- és naplózási köteteket (adathoz és naplófájlhoz) tárol **helyi redundáns tárolást (LRS)** meghatározott [Azure-beli virtuális szempontjai DBMS üzembe helyezési SAP számítási feladathoz tartozó gépek](dbms_guide_general.md).
* Az IO-útvonalához SAP MaxDB adatkötetnél (adatfájlok) külön naplózási kötetek (naplófájlok) tartozó i/o-útvonalról. Az azt jelenti, hogy SAP MaxDB adatkötetek (adatfájlok) kell telepíteni kell egy logikai meghajtó és SAP MaxDB naplózási kötetek (naplófájlok) rendelkezik egy másik logikai meghajtó kell telepíteni.
* Állítsa be az egyes lemezek, attól függően, hogy használat SAP MaxDB adat- vagy naplófájl kötetek (adathoz és naplófájlhoz), és protokollt használja az Azure standard szintű vagy az Azure Premium Storage, a megfelelő gyorsítótárazási típus leírtak szerint [Azure virtuális gépek DBMS szempontjai az SAP számítási feladatok üzembe helyezése](dbms_guide_general.md).
* Mindaddig, amíg a jelenlegi IOPS-kvóta lemezenként megfelel a követelményeknek, lehetőség az adatkötetek tárolni egyetlen csatlakoztatott lemez és is tárolhatja az összes adatbázis naplózási kötetek másik egyetlen csatlakoztatott lemezen.
* Ha további iops-érték és/vagy lemezterület szükség, ajánlott keresztül több csatlakoztatott lemez egy nagy méretű logikai eszköz létrehozásához használja a Microsoft ablak Tárolókészletek (csak elérhető a Microsoft Windows Server 2012 és újabb). További részletek: [SAP számítási feladatok Azure virtuális gépek DBMS üzembe szempontjai](dbms_guide_general.md). Ez a megközelítés leegyszerűsíti az adminisztratív terhelés kezeléséhez a lemezterület, és elkerülhető az a munka, amelyet manuálisan elosztása több csatlakoztatott lemezek között fájlokat.
* erősen ajánlott az Azure Premium Storage olyan MaxDB telepítésekhez. 

![Azure IaaS virtuális Gépeken SAP MaxDB DBMS referencia konfigurációja](./media/dbms_maxdb_deployment_guide/Simple_disk_structure_maxdb.PNG)


#### <a name="23c78d3b-ca5a-4e72-8a24-645d141a3f5d"></a>Biztonsági mentés és visszaállítás
Amikor SAP MaxDB üzembe az Azure-ba, nézze át a biztonsági mentési módszer. Még ha a rendszer nem egy hatékony rendszer, az SAP MaxDB futó SAP-adatbázist kell készíteni rendszeres időközönként. Mivel az Azure Storage három rendszerkép tartja, a biztonsági mentés már kevésbé fontos a rendszer tárolási hiba és a fontosabb operatív vagy felügyeleti meghibásodása elleni védelme. Elsődleges fenntartása a megfelelő biztonsági mentési és visszaállítási terv oka, hogy lehet kompenzálni logikai vagy kézi-e hibák időponthoz helyreállítási képességek biztosításával. Így az a célja, hogy az adatbázis visszaállítása egy bizonyos ponton, vagy használhatja a biztonsági másolatokat az Azure használatával ültet be egy másik rendszer a meglévő adatbázis másolásával vagy használja a biztonsági mentéseket. 

Biztonsági mentése és visszaállítása egy adatbázist az Azure-ban működik ugyanúgy, mint a helyszíni rendszerek, így használhatja a szokásos SAP MaxDB biztonsági mentési és visszaállítási eszközöket, amelyek a SAP MaxDB dokumentációja a dokumentumokat, az SAP-Jegyzetnek felsorolt ismertetett [767598]. 

#### <a name="77cd2fbb-307e-4cbf-a65f-745553f72d2c"></a>Teljesítménnyel kapcsolatos biztonsági mentés és visszaállítás
Operációs rendszer nélküli telepítések, mint a biztonsági mentési és helyreállítási teljesítmény sem függ, hány köteteket tudja olvasni a párhuzamos és az átviteli sebességet, a köteteket. Ezért egy feltételezheti, hogy:

* A kevesebb, a lemezek számát az adatbázis-eszközökre, az alacsonyabb tárolja az összesített olvasás átviteli sebességét
* A kevesebb céllal (lemezek Stripe könyvtárak) írni a biztonsági mentés, az alacsonyabb az átviteli sebesség

Növelje az írási tárolók, két lehetőség van használható, valószínűleg kombináció, az Ön szükségleteinek:

* Dedikálni különböző kötetet a biztonsági mentéshez
* A biztonsági mentési célkötet szétosztott keresztül több csatlakoztatott lemez annak érdekében, hogy a köteten lévő csíkozott lemez iops-érték javítani
* Különálló dedikált logikai lemez eszközök rendelkeznek:
  * SAP MaxDB biztonsági másolatokat tároló köteteken (azaz fájlok)
  * SAP MaxDB adatkötetek (azaz fájlok)
  * SAP MaxDB naplózási kötetek (azaz fájlok)

Kötet szétosztott keresztül több csatlakoztatott lemez van már tárgyalt korábban a [SAP számítási feladatok Azure virtuális gépek DBMS üzembe szempontjai](dbms_guide_general.md). 

#### <a name="f77c1436-9ad8-44fb-a331-8671342de818"></a>Egyéb szempontok
Egyéb általános területre, például az Azure rendelkezésre állási csoportok vagy az SAP figyelése is érvényesek a leírtak szerint [SAP számítási feladatok Azure virtuális gépek DBMS üzembe szempontjai](dbms_guide_general.md).  virtuális gépek az SAP MaxDB adatbázis központi telepítéseknél.
Egyéb SAP MaxDB-specifikus beállításokat átlátható az Azure virtuális gépeire, és az SAP-Jegyzetnek felsorolt különböző dokumentumokon ismertetett [767598] és az alábbi SAP-megjegyzések:

* [826037] 
* [1139904]
* [1173395]

## <a name="specifics-for-sap-livecache-deployments-on-windows"></a>SAP liveCache központi Windows-adatait
### <a name="sap-livecache-version-support"></a>SAP liveCache Version Support
Minimális verziója támogatja az Azure Virtual machines gépeken SAP liveCache **SAP LC/LCAPPS 10.0 SP 25** beleértve **liveCache 7.9.08.31** és **LCA-Build 25**, az engedélyezett **EhP 2 az SAP SCM 7.0** vagy későbbi kiadásaihoz.

### <a name="supported-microsoft-windows-versions-and-azure-vm-types-for-sap-livecache-dbms"></a>A Microsoft Windows-verziók és az Azure-beli Virtuálisgép-típusok támogatott SAP liveCache adatbázis-kezelő
A Microsoft Windows támogatott verzióját az Azure-beli SAP liveCache című témakörben talál:

* [SAP-termék rendelkezésre állási mátrix (PAM)][sap-pam]
* SAP Note [1928533]

Erősen ajánlott az operációs rendszer a Microsoft Windows Server legújabb verzióját használja. 

### <a name="sap-livecache-configuration-guidelines-for-sap-installations-in-azure-vms"></a>SAP liveCache beállítási útmutatója az Azure virtuális gépeken futó SAP-telepítések
#### <a name="recommended-azure-vm-types-for-livecache"></a>Az Azure-beli Virtuálisgép-típusok ajánlott liveCache
Az SAP liveCache az olyan alkalmazás, amely hatalmas számításokat végez, a mennyiségét és sebességét, a RAM-MAL és CPU SAP liveCache teljesítményének jelentős hatással van. 

Az SAP által támogatott Azure-beli Virtuálisgép-típusok (SAP-Jegyzetnek [1928533]), minden virtuális Processzor-erőforrások a virtuális gép számára lefoglalt dedikált fizikai Processzor-erőforrások a hipervizor élvezik. Nincs túlzott (és a Processzor-erőforrások ezért nincsenek verseny) történik.

Minden Azure virtuális gép példány esetében az SAP által támogatott, ehhez hasonlóan a Virtuálisgép-memória hozzárendelve a fizikai memória - túlzott kiosztása például (túlzott kötelezettségvállalás), 100 %-os nem használatos.

Ezen szempontból ajánlott a legutóbbi Dv2, Dv3, Ev3 és M sorozatú virtuális gépek használatához. A kiválasztott virtuális gépek különböző típusairól liveCache és a szükséges CPU-erőforrások számára szükséges memória függ. Mint minden más DBMS üzembe tanácsos kihasználhatja az Azure Premium Storage a teljesítmény kritikus köteteket.

#### <a name="storage-configuration-for-livecache-in-azure"></a>Az Azure-ban liveCache Tárkonfigurációjának
SAP liveCache SAP MaxDB technológia alapul, ahogy a összes az Azure storage ajánlásokat és tanácsokat a jelen dokumentumban ismertetett SAP MaxDB említett is jsou platné Pro SAP liveCache. 

#### <a name="dedicated-azure-vm-for-livecache-scenario"></a>LiveCache forgatókönyvhöz dedikált Azure virtuális Gépen
SAP liveCache intenzíven használ a számítási teljesítménnyel, hatékony használatot javasoljuk egy dedikált Azure virtuális gép üzembe helyezése. 

![Azure virtuális gépek hatékony használati esetekhez liveCache számára fenntartva](./media/dbms_maxdb_deployment_guide/700-livecach-prod.PNG)


#### <a name="backup-and-restore-for-livecache-in-azure"></a>Biztonsági mentés és visszaállítás liveCache az Azure-ban
biztonsági mentés és visszaállítás, beleértve a teljesítménnyel kapcsolatos megfontolások már ismertetett vonatkozó SAP MaxDB fejezeteiben ebben a dokumentumban. 

#### <a name="other-considerations"></a>Egyéb szempontok
Egyéb általános területek már a megfelelő SAP MaxDB fejezet ismerteti. 

## <a name="specifics-for-the-sap-content-server-deployment-on-windows-in-azure"></a>SAP-kiszolgáló üzembe helyezését, az Azure-ban Windows-adatait
Az SAP-tartalom kiszolgáló összetevő külön, server-alapú különböző formátumokban tartalmat, például elektronikus dokumentumok tárolására. A SAP-kiszolgáló technológia biztosítja, és minden olyan SAP-alkalmazások használt cross-alkalmazás lesz. Ez egy külön rendszeren telepítve van. Tipikus tartalma képzési anyagokat és az ismeretek adatraktár vagy a mySAP PLM dokumentumkezelő rendszer származó műszaki rajzok dokumentáció. 

### <a name="sap-content-server-version-support-for-azure-vms"></a>SAP webtartalom-kiszolgáló verzió támogatása az Azure-beli virtuális gépek
SAP jelenleg támogatja:

* **SAP-kiszolgáló** verziójával **6.50 (vagy újabb)**
* **SAP MaxDB 7.9 verzió**
* **Microsoft IIS (Internet Information Server) 8.0-s (és újabb)**

Erősen javasoljuk, hogy az SAP-kiszolgáló legújabb verziója, valamint a legújabb verziója **Microsoft IIS**. 

A legújabb támogatott verzióit SAP-kiszolgálóból, valamint a Microsoft IIS ellenőrizze a [SAP termék rendelkezésre állási mátrix (PAM)][sap-pam].

### <a name="supported-microsoft-windows-and-azure-vm-types-for-sap-content-server"></a>Támogatott a Microsoft Windows- és Azure virtuális Gépen az SAP-kiszolgálóhoz
Ismerje meg a támogatott Windows-verziót, SAP-kiszolgálóhoz az Azure-ban, lásd:

* [SAP-termék rendelkezésre állási mátrix (PAM)][sap-pam]
* SAP Note [1928533]

Erősen ajánlott a Microsoft Windows Server legújabb verzióját használja.

### <a name="sap-content-server-configuration-guidelines-for-sap-installations-in-azure-vms"></a>SAP webtartalom-kiszolgáló beállítási útmutatója az Azure-beli virtuális gépeken SAP-telepítések
#### <a name="storage-configuration-for-content-server-in-azure"></a>Tárolási konfiguráció-kiszolgálóhoz az Azure-ban
Ha konfigurálja az SAP az SAP MaxDB adatbázis fájlok tárolására kiszolgáló, az összes Azure storage ajánlott eljárások javaslat az SAP MaxDB ebben a dokumentumban említett is érvényesek a webtartalom-kiszolgáló az SAP-forgatókönyvhöz. 

Ha konfigurálja az SAP webtartalom-kiszolgáló tárolja a fájlokat a fájlrendszer, dedikált logikai meghajtó használata ajánlott. A Windows tárolóhelyek használata lehetővé teszi, hogy is növelheti a logikai lemez méretét és IOPS-teljesítmény, leírtak szerint [SAP számítási feladatok Azure virtuális gépek DBMS üzembe szempontjai](dbms_guide_general.md). 

#### <a name="sap-content-server-location"></a>SAP Content Server Location
SAP-kiszolgáló rendelkezik az ugyanazon Azure-régióban és az Azure virtuális hálózat, ahol telepíti az SAP-rendszerhez üzembe helyezni. Szabadon eldöntheti, hogy szeretné-e egy dedikált Azure virtuális gépen, vagy ugyanazon a virtuális Gépen a SAP-rendszer fut, ahol az SAP-kiszolgáló-összetevők üzembe helyezése. 

![Az Azure virtuális gép dedikált SAP-kiszolgálóhoz](./media/dbms_maxdb_deployment_guide/800-azure-vm-sap-content-server.png)


#### <a name="sap-cache-server-location"></a>SAP gyorsítótár-kiszolgáló helye
Az SAP gyorsítótár-kiszolgáló helyi (gyorsítótárazott) dokumentumokhoz való hozzáférés biztosításához új server-alapú összetevőt. Az SAP gyorsítótár-kiszolgáló gyorsítótárazza a dokumentumok tartalom SAP-kiszolgáló. Ez a hálózati forgalom optimalizálása, ha dokumentumok rendelkezik a különböző helyeken lévő egynél többször lekérni. Az általános szabály, hogy az SAP gyorsítótár-kiszolgálót kell fizikailag közel az ügyfél, amely hozzáfér az SAP gyorsítótár-kiszolgáló rendelkezik-e. 

Itt két lehetősége van:

1. **Ügyfél egy olyan háttérrendszer SAP-rendszer** egy háttérrendszer SAP az SAP webtartalom-kiszolgáló eléréséhez van konfigurálva, a SAP-rendszer-e egy ügyfél. SAP-rendszerhez és a webtartalom-kiszolgáló SAP vannak üzembe helyezve az azonos Azure-régióban, az azonos Azure-adatközpontban, azok fizikailag közel egymással. Így hiba esetén nem kell egy dedikált SAP gyorsítótár-kiszolgáló. SAP felhasználói felület ügyfelek (SAP grafikus felhasználói felületének vagy a webes böngészőben) a közvetlen elérés az SAP-rendszerhez, és az SAP-rendszer beolvassa a dokumentumok a SAP-kiszolgálóhoz.
2. **Ügyfél egy helyszíni webböngésző** az SAP-tartalom kiszolgáló beállítható úgy, hogy közvetlenül a böngésző számára legyen elérhető. Ebben az esetben egy webböngészőben a helyszínen futó egy ügyfél, a SAP-kiszolgálóhoz. Helyszíni adatközpont és az Azure-adatközpont (ideális esetben közeli egymással) különböző fizikai helyen vannak elhelyezve. A helyszíni adatközpont Azure-t az Azure Site-to-Site VPN vagy az ExpressRoute segítségével csatlakozik. Bár mindkét lehetőséget kínálnak biztonságos VPN-kapcsolattal az Azure-ba, helyek közötti hálózati kapcsolat nem vonatkozik a hálózati sávszélesség és késés SLA-t a helyszíni adatközpont és az Azure-adatközpont között. Gyorsabb dokumentumokhoz való hozzáférést, a következők egyikét teheti:
   1. Telepítse az SAP-gyorsítótár-kiszolgálót a helyszínen, közel a helyi böngésző (az alábbi ábra beállítása)
   2. Azure ExpressRoute lehetőséget kínál a nagy sebességű és kis késleltetésű dedikált hálózati kapcsolat a helyszíni adatközpont és az Azure-adatközpont között, amelyek konfigurálásához.

![Helyszíni SAP-gyorsítótár-kiszolgáló telepítését.](./media/dbms_maxdb_deployment_guide/900-sap-cache-server-on-premises.png)
<a name="642f746c-e4d4-489d-bf63-73e80177a0a8"></a>

#### <a name="backup--restore"></a>Biztonsági mentés / helyreállítás
Ha a SAP-kiszolgáló fájlok tárolására az SAP MaxDB adatbázisban konfigurálja, a biztonsági mentési és visszaállítási eljárást és teljesítménnyel kapcsolatos már SAP MaxDB fejezeteknek: Ez a dokumentum ismerteti. 

Ha konfigurálja a SAP-kiszolgáló tárolja a fájlokat a fájlrendszer, egy lehetőség, hogy hajtsa végre a manuális biztonsági mentés/visszaállítás az egész fájlra struktúra hol találhatók a dokumentumokat. SAP MaxDB biztonsági mentési és visszaállítási hasonlóan, azt javasoljuk, hogy a dedikált lemezkötet biztonsági mentési célból. 

#### <a name="other"></a>Egyéb
Egyéb SAP tartalom-kiszolgálóra jellemző beállításokat az Azure virtuális gépek transzparens, és különböző dokumentumok és az SAP-megjegyzések ismerteti:

* <https://service.sap.com/contentserver> 
* SAP-Jegyzetnek [1619726]  
