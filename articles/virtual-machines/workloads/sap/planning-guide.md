---
title: Azure 上的 SAP：規劃和實作指南
description: SAP NetWeaver 的 Azure 虛擬機器規劃和實作指南
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/23/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5cd335d34a67cc5a102bde11366813c53770266e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87036330"
---
# <a name="azure-virtual-machines-planning-and-implementation-for-sap-netweaver"></a>SAP NetWeaver 的 Azure 虛擬機器規劃和實作指南

[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1139904]:https://launchpad.support.sap.com/#/notes/1139904
[1173395]:https://launchpad.support.sap.com/#/notes/1173395
[1245200]:https://launchpad.support.sap.com/#/notes/1245200
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1558958]:https://launchpad.support.sap.com/#/notes/1558958
[1585981]:https://launchpad.support.sap.com/#/notes/1585981
[1588316]:https://launchpad.support.sap.com/#/notes/1588316
[1590719]:https://launchpad.support.sap.com/#/notes/1590719
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1605680]:https://launchpad.support.sap.com/#/notes/1605680
[1619720]:https://launchpad.support.sap.com/#/notes/1619720
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
[1909114]:https://launchpad.support.sap.com/#/notes/1909114
[1922555]:https://launchpad.support.sap.com/#/notes/1922555
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
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
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[azure-cli]:../../../cli-install-nodejs.md
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azure/
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits

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
[resource-group-overview]:../../../azure-resource-manager/management/overview.md
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
[storage-scalability-targets]:../../../storage/common/scalability-targets-standard-accounts.md
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



Microsoft Azure 可讓公司在最短的時間內取得計算和儲存體資源，而不需要冗長的採購週期。 Azure 虛擬機器服務可讓公司將傳統應用程式 (例如以 SAP NetWeaver 為基礎的應用程式) 部署到 Azure，並擴充其可靠性和可用性，而不需要在內部部署提供進一步的資源。 Azure 虛擬機器服務也支援跨單位連線能力，可讓公司主動將 Azure 虛擬機器整合到其內部部署網域、私人雲端和 SAP 系統環境。
本技術白皮書說明 Microsoft Azure 虛擬機器的基本概念，並逐步解說在 Azure 中安裝 SAP NetWeaver 的規劃和實作考量，因此請務必先閱讀本文，再開始於 Azure 上實際部署 SAP NetWeaver。
本白皮書會對「SAP 安裝文件」和「SAP 附註」進行補充說明，指出用來在指定平台上安裝和部署 SAP 軟體的主要資源。

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="summary"></a>摘要
雲端運算這個廣泛使用的名詞已日益受到 IT 產業的重視，不論是小型公司、大型公司還是跨國企業都是如此。

Microsoft Azure 是 Microsoft 所推出的雲端服務平台，可提供各式各樣的新契機。 客戶現在既能將應用程式快速佈建為雲端服務，也能快速取消佈建，因此不會再受到技術或預算所限制。 與其在硬體基礎結構投入時間和預算，公司寧可專注於應用程式、商業流程及其帶給客戶和使用者的優點。

透過 Microsoft Azure 虛擬機器服務，Microsoft 提供完整的基礎結構即服務 (IaaS) 平台。 Azure 虛擬機器 (IaaS) 支援以 SAP NetWeaver 為基礎的應用程式。 本技術白皮書說明如何在作為所選平台的 Microsoft Azure 中，規劃和實作 SAP NetWeaver 應用程式。

本文著重於兩個主要部分︰

* 第一個部分說明在 Azure 上部署 SAP NetWeaver 應用程式所支援的兩種模式， 也會說明考慮到 SAP 部署之一般處理 Azure 的方式。
* 第二個部分詳細說明如何實作第一個部分所述的不同案例。

如需其他資源，請參閱本文的[資源][planning-guide-1.2]一章。

### <a name="definitions-upfront"></a>預先定義
我們在本文件中使用下列詞彙︰

* IaaS：基礎結構即服務
* PaaS：平台即服務
* SaaS：軟體即服務
* SAP 元件︰個別的 SAP 應用程式，例如 ECC、BW、Solution Manager 或 S/4HANA。  SAP 元件可以傳統 ABAP 或 Java 技術為基礎，或以非 NetWeaver 應用程式 (例如商務物件) 為基礎。
* SAP 環境 (SAP Environment)︰一或多個以邏輯方式分組的 SAP 元件，可執行像是開發、QAS、訓練、DR 或生產等商務功能。
* SAP 架構 (SAP Landscape)︰此詞彙是指客戶 IP 環境中的整個 SAP 資產。 SAP 環境包含所有生產和非生產環境。
* SAP 系統 (SAP System)︰DBMS 層與應用程式層的組合，例如 SAP ERP 開發系統、SAP BW 測試系統、SAP CRM 生產系統等。在 Azure 部署中，不支援在內部部署與 Azure 之間分割這兩個層級。 表示 SAP 系統可以在內部部署或在 Azure 部署。 不過，您可以將 SAP 環境的不同系統部署到 Azure 或內部部署。 例如，您可以在 Azure 部署 SAP CRM 開發和測試系統，但在內部部署 SAP CRM 生產系統。
* 跨單位或混合式：描述將 VM 部署到 Azure 訂用帳戶的案例，該訂用帳戶在內部部署資料中心與 Azure 之間具有站對站、多站台或 ExpressRoute 連線能力。 在一般 Azure 文件中，這類部署也會描述為跨單位或混合式案例。 連線的原因是為了將內部部署網域、內部部署 Active Directory/OpenLDAP 和內部部署 DNS 延伸到 Azure。 內部部署的架構會擴充到訂用帳戶的 Azure 資產。 在此擴充下，VM 可以是內部部署網域的一部分。 內部部署網域的網域使用者可以存取伺服器，並可在這些 VM 上執行服務 (例如 DBMS 服務)， 但無法在內部部署的 VM 和 Azure 部署的 VM 之間進行通訊和名稱解析。 這是將 SAP 資產部署到 Azure 時最常見且近乎獨有的情況。 如需詳細資訊，請參閱[這篇文章][vpn-gateway-cross-premises-options]和[這篇文章][vpn-gateway-site-to-site-create]。
* Azure 監視擴充功能、增強型監視和 Azure Extension for SAP：描述的是同一個項目。 其描述的是一項 VM 擴充功能，您必須部署此擴充功能以便向 SAP 主機代理程式提供一些關於 Azure 基礎結構的基本資料。 SAP 附註中的 SAP 可能將此項目稱為監視擴充功能或增強型監視。 在 Azure 中，我們則將其稱為 **Azure Extension for SAP**。

> [!NOTE]
> SAP 生產系統支援跨單位或混合式部署 SAP 系統，其中執行 SAP 系統的 Azure 虛擬機器是內部部署網域的成員。 支援跨單位或混合式組態，以便將部分或完整的 SAP 架構部署到 Azure。 即使在 Azure 中執行完整 SAP 環境，也需要有這些 VM 成為內部部署網域和 ADS/OpenLDAP 的一部分。 
>
>



### <a name="resources"></a><a name="e55d1e22-c2c8-460b-9897-64622a34fdff"></a>資源
在 Azure 文件中[此處](./get-started.md)找到 SAP 工作負載的進入點。 從此進入點開始，您會發現涵蓋下列主題的許多文章：

- Azure 上的 SAP NetWeaver 與 Business One
- Azure 中各種不同 DBMS 系統的 SAP DBMS 指南
- Azure 上 SAP 工作負載的高可用性和災害復原
- 在 Azure 上執行 SAP HANA 的特定指引
- 適用於 SAP HANA DBMS 之 Azure HANA 大型執行個體的特定指引 


> [!IMPORTANT]
> 文件中會儘可能使用參考《SAP 安裝指南》的連結或其他 SAP 文件 (如需參考 InstGuide-01，請參閱 <http://service.sap.com/instguides> \(英文\))。 在涉及必要條件、安裝程序或特定 SAP 功能的詳細資料時，應一律仔細閱讀 SAP 文件和指南，因為 Microsoft 文件僅涵蓋在 Microsoft Azure 虛擬機器中安裝及操作之 SAP 軟體的特定工作。
>
>

下列 SAP 附註與 Azure 上的 SAP 主題相關︰

| 附註編號 | Title |
| --- | --- |
| [1928533] |Azure 上的 SAP 應用程式：支援的產品和大小 |
| [2015553] |Microsoft Azure 上的 SAP：支援必要條件 |
| [1999351] |疑難排解適用於 SAP 且已強化的 Azure 監視功能 |
| [2178632] |Microsoft Azure 上的 SAP 主要監視度量 |
| [1409604] |Windows 上的虛擬化：增強型監視 |
| [2191498] |Linux 上搭配 Azure 的 SAP：增強型監視 |
| [2243692] |Microsoft Azure (IaaS) VM 上的 Linux：SAP 授權問題 |
| [1984787] |SUSE LINUX Enterprise Server 12：安裝注意事項 |
| [2002167] |Red Hat Enterprise Linux 7.x：安裝與升級 |
| [2069760] |Oracle Linux 7.x SAP 安裝和升級 |
| [1597355] |適用於 Linux 的交換空間建議 |

另請參閱 [SCN Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes)，其中包含適用於 Linux 的所有 SAP 附註。

有關 Azure 訂用帳戶的一般預設限制和最大限制，請參閱[這篇文章][azure-resource-manager/management/azure-subscription-service-limits-subscription]。

## <a name="possible-scenarios"></a>可能的案例
SAP 常被視為企業內最常見的關鍵任務應用程式。 這些應用程式的架構和作業大部分都很複雜，而且務必要確保您符合可用性和效能的需求。

因此，企業必須仔細考慮要選擇使用哪一個雲端提供者來執行這類業務關鍵商務程序。 Azure 很適合作為業務關鍵 SAP 應用程式和商務程序的公用雲端平台。 由於 Azure 基礎結構種類繁多，因此目前現有的 SAP NetWeaver 和 S/4HANA 系統幾乎都可以裝載到 Azure 中。 Azure 能提供有好幾 TB 記憶體和 200 個以上 CPU 的 VM。 除此之外，Azure 還提供了「 [HANA 大型實例](./hana-overview-architecture.md)」，可讓相應增加的 hana 部署高達 24 tb，並 SAP Hana 向外延展部署最多 120 tb。 可以這麼說，目前幾乎所有的內部部署 SAP 案例也都可以在 Azure 中執行。 

如需相關案例和一些不受支援案例的粗略描述，請參閱 [Azure 虛擬機器支援案例上的 SAP 工作負載](./sap-planning-supported-configurations.md)文件。

在規劃和開發想要部署至 Azure 的架構期間，請查看這些案例，以及所參考文件中已具名指出不受支援的一些情況。

整體而言，最常見的部署模式是跨單位案例 (如下所示)

![具有站對站連線能力的 VPN (跨單位)][planning-guide-figure-300]

許多客戶之所以會套用跨單位部署模式，是因為對於所有應用程式來說，使用 Azure ExpressRoute 將內部部署環境延伸至 Azure，並將 Azure 視為虛擬資料中心是最為透明的方式。 隨著移入 Azure 的資產越來越多，Azure 所部署的基礎結構和網路基礎結構也會隨之成長，內部部署資產則會隨之減少。 一切過程對於使用者和應用程式來說都透明無比。

若要成功地將 SAP 系統部署到 Azure IaaS 或一般 IaaS，請務必了解傳統外包服務商或主機服務提供者的供應項目與 IaaS 供應項目之間的顯著差異。 傳統的主機服務提供者或外包服務商會將基礎結構 (網路、儲存體和伺服器類型) 調整成客戶想要裝載的工作負載，但在 IaaS 部署中，卻是由客戶或合作夥伴負責描述工作負載的特性，並針對 VM、儲存體和網路選擇正確的 Azure 元件。

為了收集資料以規劃目的地為 Azure 的部署，請務必：

- 評估哪些 SAP 產品支援在 Azure VM 中執行
- 針對這些 SAP 產品評估特定 Azure VM 支援哪些特定的作業系統版本
- 評估哪些 DBMS 版本支援用於您的 SAP 產品與特定的 Azure VM
- 評估某些必要的 OS/DBMS 版本是否需要您執行 SAP 版本、支援套件升級以及核心升級，以便取得支援的設定
- 評估您是否需要移至不同的作業系統才能部署到 Azure 上。

如需有關 Azure 上支援的 SAP 元件、支援的 Azure 基礎結構單位和相關作業系統版本與 DBMS 版本的詳細資訊，請參閱 [Azure 部署支援哪些 SAP 軟體](./sap-supported-product-on-azure.md)一文。 透過評估有效 SAP 版本、作業系統和 DBMS 版本所獲得的結果，會嚴重影響要將 SAP 系統移至 Azure 所需的工作量。 這項評估的結果會定義在需要升級 SAP 版本或變更作業系統的情況下，是否可能要費一番功夫來預做準備。


## <a name="azure-regions"></a><a name="be80d1b9-a463-4845-bd35-f4cebdb5424a"></a>Azure 區域
Microsoft 的 Azure 服務會收集在 Azure 區域內。 Azure 區域是可執行和裝載不同 Azure 服務的硬體和基礎結構所在的一個或一批資料中心。 此基礎結構包含大量節點，既可作為計算節點或儲存體節點，也可執行網路功能。 

如需不同 Azure 區域的清單，請參閱 [Azure 地理位置](https://azure.microsoft.com/global-infrastructure/geographies/)一文。 並非所有 Azure 區域都提供相同的服務。 根據您想要執行的 SAP 產品，以及與其相關的作業系統和 DBMS，您最終可能會落入某些區域未提供您所需 VM 類型的情況。 在執行 SAP HANA 時，您通常需要 M/Mv2 VM 系列的 VM，所以更是容易遇到這種情況。 這些 VM 系列只會部署到一小部分區域。 您可以透過[依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/)網站的協助，來了解各個區域提供的確切 VM、類型、Azure 儲存體類型或其他 Azure 服務。 當您開始規劃並在腦海裡將某些區域視為主要區域，並最終訂出次要區域時，請務必先調查這些區域中是否有必要的服務。 

### <a name="availability-zones"></a>可用性區域
有好幾個 Azure 區域 (region) 會實作一種稱為可用性區域 (zone) 的概念。 可用性區域與 Azure 區域實際上是兩個各自獨立的地點。 每個可用性區域由一或多個資料中心組成，配備了電力、冷卻系統及網路系統。 例如，跨兩個 Azure 可用性區域部署兩個 VM，並為您的 SAP DBMS 系統或 SAP 中央服務實作高可用性架構，可為您提供最佳的 Azure SLA。 若要了解 Azure 中的這個特定虛擬機器 SLA，請查看最新版的[虛擬機器 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)。 由於 Azure 區域在過去幾年快速發展和擴大，因此 Azure 區域的拓撲、實體資料中心的數目、這些資料中心之間的距離，以及 Azure 可用性區域之間的距離會有所不同。 網路延遲也是。

可用性區域的準則並不適用於 [HANA 大型執行個體](./hana-overview-architecture.md)的 HANA 特定服務。 適用於 HANA 大型執行個體的服務等級協定可以在 [Azure 上的 SAP HANA 大型執行個體 SLA](https://azure.microsoft.com/support/legal/sla/sap-hana-large/) 一文中找到 


### <a name="fault-domains"></a><a name="df49dc09-141b-4f34-a4a2-990913b30358"></a>容錯網域
容錯網域代表失敗的實體單位，與資料中心所包含的實體基礎結構密切相關，雖然實體刀鋒視窗或機架可視為容錯網域，但這兩者間並沒有直接的一對一對應。

當您將多個虛擬機器部署為 Microsoft Azure 虛擬機器服務中一個 SAP 系統的一部分時，您可以影響 Azure 網狀架構控制器將您的應用程式部署到不同的容錯網域，以符合更高的可用性 SLA 需求。 不過，將容錯網域分散到 Azure 縮放單位 (數百個計算節點或儲存體節點和網路的集合)，或將 VM 指派給特定容錯網域，並不是可由您直接控制的作業。 若要引導 Azure 網狀架構控制器在不同的容錯網域之間部署一組 VM，您必須在部署時，將 Azure 可用性設定組指派給 VM。 如需有關「Azure 可用性設定組」的詳細資訊，請參閱本文件的 [Azure 可用性設定組][planning-guide-3.2.3]一章。


### <a name="upgrade-domains"></a><a name="fc1ac8b2-e54a-487c-8581-d3cc6625e560"></a>升級網域
升級網域代表邏輯單元，可協助決定 SAP 系統中 VM 的更新方式 (此 SAP 系統是由多個在 VM 中執行的 SAP 執行個體所組成)。 進行升級時，Microsoft Azure 會逐一更新這些升級網域。 藉由在部署時將 VM 散佈到不同的升級網域，即可防止您的 SAP 系統可能有一部分停機。 為了強制 Azure 將 SAP 系統的 VM 分散部署到不同的升級網域，您必須在部署每個 VM 時設定特定屬性。 類似於容錯網域，Azure 縮放單位會分成多個升級網域。 若要引導 Azure 網狀架構控制器在不同的升級網域之間部署一組 VM，您必須在部署時，將 Azure 可用性設定組指派給 VM。 如需有關「Azure 可用性設定組」的詳細資訊，請參閱下一章 [Azure 可用性設定][planning-guide-3.2.3]。


### <a name="azure-availability-sets"></a><a name="18810088-f9be-4c97-958a-27996255c665"></a>Azure 可用性設定組
一個 Azure 可用性設定組內的 Azure 虛擬機器會透過 Azure 網狀架構控制器，分散到不同的容錯和升級網域。 分散到不同容錯和升級網域的目的是，為了防止 SAP 系統的所有 VM，在基礎結構維護或某個容錯網域內發生失敗時全部關機。 根據預設，VM 不是可用性設定組的一部分。 您可以在部署時，或稍後藉由重新設定及重新部署 VM，來定義 VM 參與可用性設定組。

若要了解「Azure 可用性設定組」的概念，以及「可用性設定組」與「容錯網域」和「升級網域」的關聯方式，請參閱[這篇文章][virtual-machines-manage-availability]。 

當您定義可用性設定組，並嘗試在一個可用性設定組內混合使用不同 VM 系列的各種 VM 時，您可能會遇到問題而導致無法將特定 VM 類型納入到這類可用性設定組中。 原因是可用性設定組會繫結至包含特定計算主機類型的縮放單位。 而且特定類型的計算主機只能執行特定類型的 VM 系列。 例如，如果您建立可用性設定組，並將第一個 VM 部署至該可用性設定組，然後選擇 Esv3 系列的 VM 類型，再嘗試將其部署為第二個 VM (M 系列的 VM)，則第二個配置會拒絕您這麼做。 原因是 Esv3 系列的 VM 不會在與 M 系列的虛擬機器相同的主機硬體上執行。 當您嘗試調整 VM 大小，並嘗試將 VM 從 Esv3 系列改移至 M 系列的 VM 類型時，也會發生相同的問題。 如果要將大小調整為無法裝載在相同主機硬體上的 VM 系列，您必須先關閉可用性設定組中的所有 VM，再調整其大小，使其能夠在其他主機機器類型上執行。 若要了解部署於可用性設定組內的 VM 具有的 SLA，請查看[虛擬機器 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) 一文。 

可用性設定組和相關更新與容錯網域的準則並不適用於 [HANA 大型執行個體](./hana-overview-architecture.md)的 HANA 特定服務。 適用於 HANA 大型執行個體的服務等級協定可以在 [Azure 上的 SAP HANA 大型執行個體 SLA](https://azure.microsoft.com/support/legal/sla/sap-hana-large/) 一文中找到。 

> [!IMPORTANT]
> Azure 可用性區域和 Azure 可用性設定組的概念互斥。 也就是說，您可以將一對或多個 VM 部署到特定可用性區域或 Azure 可用性設定組。 但不能同時部署到兩者。

### <a name="azure-paired-regions"></a>Azure 配對的區域
Azure 會提供 Azure 區域配對，其中會在這些固定區域配對之間啟用特定資料的複寫。 區域配對記載于[商務持續性和嚴重損壞修復（BCDR）： Azure 配對的區域](../../../best-practices-availability-paired-regions.md)一文。 如本文所述，資料的複寫會系結 Azure 儲存體類型，而您可以將其設定為複寫到配對的區域。 另請參閱[第二個區域中的儲存體冗余一](../../../storage/common/storage-redundancy.md#redundancy-in-a-secondary-region)文。 允許這類複寫的儲存類型是儲存類型，不適合 DBMS 工作負載。 因此，Azure 儲存體複寫的可用性會限制為 Azure blob 儲存體（例如用於備份）或其他高延遲儲存案例。 當您檢查配對的區域和您想要作為主要或次要區域的服務時，可能會遇到您想要在主要區域中使用的 Azure 服務和/或 VM 類型在配對的區域中無法使用的情況。 或者，您可能會遇到因為資料合規性原因而無法接受 Azure 配對區域的情況。 在這些情況下，您需要使用非配對的區域作為次要/嚴重損壞修復區域。 在這種情況下，您需要特別注意 Azure 會自行複寫的部分資料。 如需如何將您的 Active Directory 和 DNS 複寫到嚴重損壞修復區域的範例，請參閱[設定 Active Directory 和 dns](../../../site-recovery/site-recovery-active-directory.md)的嚴重損壞修復一文
 

## <a name="azure-virtual-machine-services"></a>Azure 虛擬機器服務
Azure 提供眾多種類的虛擬機器供您選擇來部署。 您不必事先購買技術和基礎結構。 Azure VM 服務供應項目會提供隨選計算和儲存體，來裝載、調整及管理 Web 應用程式和連線的應用程式，藉此簡化維護和操作應用程式的作業。 基礎結構管理使用專為高可用性和動態調整規模所設計的平台來自動化，以配合數種不同定價模式選項的使用需求。

![Microsoft Azure 虛擬機器服務的定位][planning-guide-figure-400]

透過 Azure 虛擬機器，Microsoft 可讓您將自訂伺服器映像部署到 Azure 作為 IaaS 執行個體。 或者，您也可以從 Azure 映像庫中選擇種類豐富的可取用作業系統映像。

從作業觀點來看，Azure 虛擬機器服務提供與內部部署的虛擬機器類似的體驗。 您必須負責管理、操作和修補 Azure VM 中執行的特定作業系統，以及該 VM 中的應用程式。 除了用來在其 Azure 基礎結構 (基礎結構即服務 - IaaS) 上裝載該 VM 的服務外，Microsoft 不會再提供任何服務。 對於您身為客戶所部署的 SAP 工作負載，Microsoft 除了 IaaS 供應項目外就不會再提供其他供應項目。 

Microsoft Azure 平台是多租用戶平台。 因此，租用戶之間會共用裝載 Azure VM 的儲存體、網路和計算資源，但有一些例外。 智慧型節流和配額邏輯可用來防止一個租用戶大幅影響另一個租用戶 (鄰點干擾)。 特別是在為 Azure 平台進行 SAP HANA 認證時，如果有多個 VM 會定期在相同主機上執行，Microsoft 就必須對 SAP 證明資源有隔開。 雖然 Azure 的邏輯會嘗試將體驗到的頻寬差異保持在很小，但比起許多客戶在其內部部署所體驗到的變化，高度共用的平台往往會在資源/頻寬可用性方面帶來更大的變化。 您必須考慮 Azure 上的 SAP 系統可能體驗到比內部部署更大差異的可能性。

### <a name="azure-virtual-machines-for-sap-workload"></a>適用於 SAP 工作負載的 Azure 虛擬機器

針對 SAP 工作負載，我們已將不同 VM 系列的選取範圍更具體地縮小至適用於 SAP 工作負載和 SAP HANA 工作負載。 若要了解如何找到正確的 VM 類型及其功能來處理 SAP 工作負載，請參閱 [Azure 部署支援哪些 SAP 軟體](./sap-supported-product-on-azure.md)文件。 

> [!NOTE]
> 針對 SAP 工作負載所認證的 VM 類型不會過度佈建 CPU 和記憶體資源。

除了單純支援的 VM 類型外，您還必須根據[區域可用](https://azure.microsoft.com/global-infrastructure/services/)的網站產品，檢查這些 vm 類型是否可在特定區域中使用。 但更重要的是，您必須評估：

- 不同 VM 類型的 CPU 和記憶體資源 
- 不同 VM 類型的 IOPS 頻寬
- 不同 VM 類型的網路功能
- 可連結的磁碟數目
- 利用特定 Azure 儲存體類型的能力

是否符合您的需求。 特定 VM 類型的大部分資料都可以在[這裡 (Linux)][virtual-machines-sizes-linux] 和[這裡 (Windows)][virtual-machines-sizes-windows] 找到。

作為定價模式，您有數個不同的定價選項，如下所示：

- 隨用隨付
- 保留 1 年
- 保留 3 年
- Spot 定價

[Linux 虛擬機器定價](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)和 [Windows 虛擬機器定價](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)網站上有提供關於作業系統和不同區域中，各個不同供應項目以及不同服務供應項目的定價。 如需一年期和三年期保留執行個體的詳細資訊和彈性，請參閱下列文章：

- [什麼是 Azure 保留項目？](../../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [利用保留 VM 執行個體獲得虛擬機器大小彈性](../../windows/reserved-vm-instance-size-flexibility.md)
- [Azure 保留折扣如何套用至虛擬機器](../../../cost-management-billing/manage/understand-vm-reservation-charges.md) 

如需 Spot 定價的詳細資訊，請參閱 [Azure Spot 虛擬機器](https://azure.microsoft.com/pricing/spot/)一文。 相同 VM 類型的定價在不同的 Azure 區域之間也可能不同。 對於某些客戶來說，部署到成本較低的 Azure 區域是值得的。

此外，Azure 還提供專用主機的概念。 專用主機概念可讓您更能掌控 Azure 所執行的修補週期。 您可以根據自己的排程來安排時間修補。 此供應項目專門鎖定其工作負載可能不會遵循正常工作負載週期的客戶。 若要深入了解 Azure 專用主機供應項目的概念，請閱讀 [Azure 專用主機](../../windows/dedicated-hosts.md)一文。 此供應項目支援用於 SAP 工作負載，許多想要更能掌控 Microsoft 的基礎結構修補甚至是維護計畫的 SAP 客戶都使用了此供應項目。 如需有關 Microsoft 如何維護及修補主控虛擬機器之 Azure 基礎結構的詳細資訊，請參閱 [Azure 中的虛擬機器維護](../../maintenance-and-updates.md)一文。

#### <a name="generation-1-and-generation-2-virtual-machines"></a>第 1 代和第 2 代虛擬機器
Microsoft 的 Hypervisor 能夠處理兩個不同世代的虛擬機器。 這些格式稱為**第 1 代**和**第 2 代**。 **第 2 代**是在 2012 年隨 Windows Server 2012 Hypervisor 引進的。 Azure 則是從使用第 1 代虛擬機器來開始的。 當您部署 Azure 虛擬機器時，預設值仍是使用第 1 代格式。 同時，您也可以部署第 2 代 VM 格式。 [Azure 上第 2 代 VM 的支援](../../windows/generation-2.md)一文列出了可部署為第 2 代 VM 的 Azure VM 系列。 本文也會列出第2代虛擬機器的重要功能差異，因為它們可以在 Hyper-v 私人雲端和 Azure 上執行。 更重要的是，本文也會列出第 1 代虛擬機器和第 2 代 VM 之間的功能差異，因為兩者都會在 Azure 中執行。 

> [!NOTE]
> 在 Azure 中執行的第 1 代和第 2 代 VM 有功能上的差異。 請參閱 [Azure 上第 2 代 VM 的支援](../../windows/generation-2.md)一文，以查看這些差異的清單。  
 
您無法將現有 VM 從某個世代移至另一個世代。 若要變更虛擬機器的世代，您需要部署所需世代的新 VM，並重新安裝您在世代的虛擬機器中執行的軟體。 這項變更只會影響 VM 的基底 VHD 映射，而且對資料磁片或連接的 NFS 或 SMB 共用不會有任何影響。 原本已指派給第1代 VM 上的資料磁片、NFS 或 SMB 共用。 

> [!NOTE]
> 從 2020 年 5 月開始，您已可將 Mv1 VM 系列的 VM 部署為第 2 代 VM。 因此，Mv1 與 Mv2 系列 VM 之間的擴大和縮小看來會變得比較少。
 

### <a name="storage-microsoft-azure-storage-and-data-disks"></a><a name="a72afa26-4bf4-4a25-8cf7-855d6032157f"></a>儲存體：Microsoft Azure 儲存體和資料磁碟
Microsoft Azure 虛擬機器使用不同的儲存體類型。 在 Azure 虛擬機器服務上實作 SAP 時，請務必了解下列兩種主要儲存體類型之間的差異：

* 非永續性、可變更的儲存體。
* 永續性儲存體。

Azure VM 會在部署 VM 之後提供非永續性磁碟。 如果 VM 重新開機，將會抹除這些磁碟機上的所有內容。因此，假設在任何情況下，資料庫的資料檔案和記錄/重做檔案都不得位於這些非持續性磁碟機上。 有些資料庫可能會有一些例外狀況，在這類狀況下這些非持續性磁碟機可能適合用於 tempdb 和暫存資料表空間。 不過，請避免將這些磁碟機使用於 A 系列 VM，因為這些非持續性磁碟機對於該 VM 系列的輸送量受限。 如需進一步的詳細資料，請參閱[了解 Azure 中 Windows VM 上的暫存磁碟機](/archive/blogs/mast/understanding-the-temporary-drive-on-windows-azure-virtual-machines) \(英文\)

---
> ![Windows][Logo_Windows] Windows
> 
> Azure VM 中的磁碟機 D:\ 不是永續性磁碟機，而是由 Azure 計算節點上的一些本機磁碟所組成。 由於它不是永續性的，這表示當 VM 重新開機時，即會遺失對 D:\ 磁碟機的內容所做的任何變更。 「任何變更」就如同已儲存的檔案、已建立的目錄、已安裝的應用程式等。
> 
> ![Linux][Logo_Linux] Linux
> 
> Linux Azure VM 會在 /mnt/resource 上自動掛接磁碟機，這個非永續性磁碟機會利用 Azure 計算節點上的本機磁碟來備份。 由於它不是永續性的，這表示當 VM 重新開機時，會遺失對 /mnt/resource 的內容所做的任何變更。 任何變更就如同已儲存的檔案、已建立的目錄、已安裝的應用程式等。
> 
> 

#### <a name="azure-storage-accounts"></a>Azure 儲存體帳戶

在 Azure 中部署服務或 Vm 時，Vhd 和 VM 映射的部署會以稱為 Azure 儲存體帳戶的單位組織。 [Azure 儲存體帳戶](../../../storage/common/storage-account-overview.md)具有可包含的 IOPS、輸送量或大小方面的限制。 在過去，這些限制記載于： 

- [標準儲存體帳戶的擴充性目標](../../../storage/common/scalability-targets-standard-account.md)
- [Premium 分頁 blob 儲存體帳戶的擴充性目標](../../../storage/blobs/scalability-targets-premium-page-blobs.md)

在規劃 Azure 中的 SAP 部署方面扮演著重要的角色。 您可以管理儲存體帳戶內的保存磁片數目。 您需要管理儲存體帳戶，最後再建立新的儲存體帳戶來建立更多保存的磁片。 

最近幾年來， [Azure 受控磁片](../../windows/managed-disks-overview.md)的引進會向您免除這些工作。 SAP 部署的建議是利用 Azure 受控磁片，而不是自行管理 Azure 儲存體帳戶。 Azure 受控磁片會將磁片分散到不同的儲存體帳戶，因此不會超過個別儲存體帳戶的限制。

在儲存體帳戶中，您有一種名為「容器」的資料夾概念，可用來將特定磁片分組至特定容器。

在 Azure 中，磁片/VHD 名稱會遵循下列命名連線，此連線需要為 Azure 中的 VHD 提供唯一的名稱：

`http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>`

上述字串必須唯一識別儲存在 Azure 儲存體上的磁片/VHD。


#### <a name="azure-persisted-storage-types"></a>Azure 保存的儲存體類型
Azure 提供各種持續性儲存體選項，可用於 SAP 工作負載和特定的 SAP 堆疊元件。 如需詳細資訊，請參閱[適用于 SAP 工作負載的 Azure 儲存體](./planning-guide-storage.md)檔。


### <a name="microsoft-azure-networking"></a><a name="61678387-8868-435d-9f8c-450b2424f5bd"></a>Microsoft Azure 網路

Microsoft Azure 會提供網路基礎結構，可對應至我們想要透過 SAP 軟體實現的所有案例。 其功能如下：

* 透過 Windows 終端機服務或 ssh/VNC 從外部直接存取 VM
* 存取 VM 中的應用程式所使用的服務和特定連接埠
* 在部署為 Azure VM 的一組 VM 之間進行內部通訊和名稱解析
* 客戶內部部署網路與 Azure 網路之間的跨單位連線能力
* Azure 網站之間的跨 Azure 區域或資料中心連線能力

如需詳細資訊，請參閱：<https://azure.microsoft.com/documentation/services/virtual-network/>

在 Azure 中設定名稱和 IP 解析有許多可能的方法。 此外，還可使用 Azure DNS 服務，而不是使用您自己的 DNS 伺服器。 如需詳細資訊，請參閱[這篇文章][virtual-networks-manage-dns-in-vnet]和[這個頁面](https://azure.microsoft.com/services/dns/)。

在跨單位或混合式案例中，我們需要內部部署 AD/OpenLDAP/DNS 已透過 VPN 或私人連線擴充到 Azure。 針對此處所述的特定案例，可能必須在 Azure 中安裝 AD/OpenLDAP 複本。

由於網路和名稱解析是 SAP 系統之資料庫部署不可或缺的一部分，因此在 [DBMS 部署指南][dbms-guide]中會更詳細地討論此概念。

##### <a name="azure-virtual-networks"></a>Azure 虛擬網路

藉由建置 Azure 虛擬網路，您可以定義由 Azure DHCP 功能所配置之私人 IP 位址的位址範圍。 在跨單位案例中，定義的 IP 位址範圍仍會使用 Azure 的 DHCP 進行配置。 不過，網域名稱解析會在內部部署執行 (假設 VM 是內部部署網域的一部分)，因此可解析不同 Azure 雲端服務以外的位址。

Azure 中的每個虛擬機器都必須連線到虛擬網路。

如需更多詳細資料，請參閱[這篇文章][resource-groups-networking]和[這個頁面](https://azure.microsoft.com/documentation/services/virtual-network/)。


> [!NOTE]
> 根據預設，一旦部署 VM，就無法變更虛擬網路組態。 TCP/IP 設定必須留給 Azure DHCP 伺服器。 預設行為是動態 IP 指派。
>
>

虛擬網路卡的 MAC 位址在調整大小後可能會變更，在此情況下，Windows 或 Linux 客體 OS 會挑選新的網路卡，並會自動使用 DHCP 來指派 IP 和 DNS 位址。

##### <a name="static-ip-assignment"></a>靜態 IP 指派
您可以將固定或保留的 IP 位址指派給 Azure 虛擬網路中的 VM。 在 Azure 虛擬網路中執行 VM 可讓您更有機會在需要進行某些案例時利用這項功能。 IP 指派會在整個 VM 存在期間保持有效，而不論 VM 是執行中或已關機。 因此，當您為虛擬網路定義 IP 位址範圍時，必須考慮到 VM (執行中和已停止的 VM) 總數。 在刪除 VM 及其網路介面之前，或在 IP 位址再次取消指派之前，會保持指派此 IP 位址。 如需詳細資訊，請參閱[這篇文章][virtual-networks-static-private-ip-arm-pportal]。

> [!NOTE]
> 您應該透過 Azure 方式將靜態 IP 位址指派給個別 vNIC。 您不應該將客體 OS 內的靜態 IP 位址指派給 vNIC。 某些 Azure 服務 (例如 Azure 備份服務) 依賴至少將主要 vNIC 設為 DHCP 的事實，而不是設為靜態 IP 位址。 另請參閱[針對 Azure 虛擬機器備份進行疑難排解](../../../backup/backup-azure-vms-troubleshoot.md#networking)文件。
>
>

##### <a name="multiple-nics-per-vm"></a>每個 VM 可以有多個 NIC

您可以為一個 Azure 虛擬機器定義多個虛擬網路介面卡 (vNIC)。 由於可擁有多個 vNIC，因此您可以開始設定網路流量分隔，例如透過一個 vNIC 路由傳送用戶端流量，並透過第二個 vNIC 路由傳送後端流量。 視 VM 的類型而定，VM 可以指派的 vNIC 數目有不同的限制。 如需確切詳細資料、功能和限制，請參閱下列文章︰

* [建立具有多個 NIC 的 Windows VM][virtual-networks-multiple-nics-windows]
* [建立連接多個 NIC 的 Linux VM][virtual-networks-multiple-nics-linux]
* [使用範本部署多個 NIC VM][virtual-network-deploy-multinic-arm-template]
* [使用 PowerShell 部署多個 NIC VM][virtual-network-deploy-multinic-arm-ps]
* [使用 Azure CLI 部署多個 NIC VM][virtual-network-deploy-multinic-arm-cli]

#### <a name="site-to-site-connectivity"></a>站對站連線能力

跨單位是指使用透明且永久的 VPN 連線連結 Azure VM 和內部部署 VM。 預期會成為 Azure 中最常見的 SAP 部署模式。 假設 SAP 執行個體在 Azure 中的作業程序和流程應該以透明方式運作。 這表示您應該能夠列印出這些系統，以及使用 SAP Transport Management System (TMS) 將變更從 Azure 中的開發系統傳輸到內部部署的測試系統。 如需有關站對站的詳細記載，請參閱[這篇文章][vpn-gateway-create-site-to-site-rm-powershell]

##### <a name="vpn-tunnel-device"></a>VPN 通道裝置

為了建立站對站連線 (內部部署資料中心對 Azure 資料中心)，您必須取得並設定 VPN 裝置，或使用在 Windows Server 2012 中引進作為軟體元件的路由和遠端存取服務 (RRAS)。

* [使用 PowerShell 建立具有站對站 VPN 連線的虛擬網路][vpn-gateway-create-site-to-site-rm-powershell]
* [關於站對站 VPN 閘道連線的 VPN 裝置][vpn-gateway-about-vpn-devices]
* [VPN 閘道常見問題集][vpn-gateway-vpn-faq]

![內部部署與 Azure 之間的站對站連線][planning-guide-figure-600]

上圖顯示兩個 Azure 訂用帳戶，其 IP 位址子範圍已保留給 Azure 中的「虛擬網路」使用。 從內部部署網路到 Azure 的連線是透過 VPN 建立。

#### <a name="point-to-site-vpn"></a>點對站 VPN

點對站 VPN 需要每個用戶端電腦使用自己的 VPN 連線到 Azure。 針對我們所討論的 SAP 案例，點對站連線能力並不實用。 因此，不會針對點對站 VPN 連線能力提供進一步的參考資料。

如需詳細資訊，請參閱這裡
* [使用 Azure 入口網站設定 VNet 的點對站連線](../../../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)
* [使用 PowerShell 設定 VNet 的點對站連線](../../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

#### <a name="multi-site-vpn"></a>多站台 VPN

Azure 現在也可讓您為一個 Azure 訂用帳戶建立「多站台 VPN」連線能力。 之前，單一訂用帳戶僅限於一個站對站 VPN 連線。 此限制在單一訂用帳戶可以有「多站台 VPN」連線之後已不存在。 您可以透過「跨單位」組態，針對特定訂用帳戶使用多個「Azure 區域」。

如需詳細的記載，請參閱[本文][vpn-gateway-create-site-to-site-rm-powershell]

#### <a name="vnet-to-vnet-connection"></a>VNet 對 VNet 連線

使用多網站 VPN 時，您必須在每個區域中設定不同的 Azure 虛擬網路。 不過，您通常必須讓不同區域中的軟體元件能夠彼此通訊。 在理想情況下，此通訊應該從一個 Azure 區域路由傳送至內部部署，再由此路由傳送至其他 Azure 區域。 簡而言之，Azure 可讓您設定從某個區域中的一個 Azure 虛擬網路到裝載於另一個區域中之另一個 Azure 虛擬網路的連線。 此功能稱為 VNet 對 VNet 連線。 如需此功能的更多詳細資料，請參閱：<https://azure.microsoft.com/documentation/articles/vpn-gateway-vnet-vnet-rm-ps/>。

#### <a name="private-connection-to-azure-expressroute"></a>與 Azure ExpressRoute 的私人連線

Microsoft Azure ExpressRoute 可在 Azure 資料中心與客戶的內部部署基礎結構或共置環境中的基礎結構之間建立私人連線。 ExpressRoute 是由各種 MPLS (封包交換式) VPN 提供者或其他網路服務提供者所提供。 ExpressRoute 連線不會經過公用網際網路。 相較於網際網路一般連線，ExpressRoute 連線提供更高安全性、透過多個平行循環提高可靠性、更快速度以及更低延遲。

如需 Azure ExpressRoute 和供應項目的詳細資訊，請參閱︰

* <https://azure.microsoft.com/documentation/services/expressroute/>
* <https://azure.microsoft.com/pricing/details/expressroute/>
* <https://azure.microsoft.com/documentation/articles/expressroute-faqs/>

如下方所記載，Express Route 可透過一個 ExpressRoute 循環來啟用多個 Azure 訂用帳戶

* <https://azure.microsoft.com/documentation/articles/expressroute-howto-linkvnet-arm/>
* <https://azure.microsoft.com/documentation/articles/expressroute-howto-circuit-arm/>

#### <a name="forced-tunneling-in-case-of-cross-premises"></a>跨單位時的強制通道
針對透過站對站、點對站或 ExpressRoute 加入內部部署網域的 VM，您也必須確定為這些 VM 中的所有使用者部署網際網路 Proxy 設定。 根據預設，在這些 VM 中執行的軟體或使用瀏覽器存取網際網路的使用者不會經過公司 Proxy，而是會透過 Azure 直接連線到網際網路。 但即使是 Proxy 設定也無法 100% 解決將流量引導通過公司 Proxy 的問題，因為檢查 Proxy 的責任在於軟體和服務。 如果在 VM 中執行的軟體未執行該項作業，或是由系統管理員管理設定，則前往網際網路的流量可能會透過 Azure 直接再次繞道至網際網路。

若要避免這類直接網際網路連線，您可以在內部部署與 Azure 之間設定具有站對站連線能力的強制通道。 已於下列網頁發佈強制通道功能的詳細描述：<https://azure.microsoft.com/documentation/articles/vpn-gateway-forced-tunneling-rm/>

ExpressRoute 強制通道會透過 ExpressRoute BGP 對等互連工作階段廣告預設路由的客戶進行啟用。

#### <a name="summary-of-azure-networking"></a>Azure 網路的摘要

本章包含有關 Azure 網路的許多重點。 以下是重點摘要：

* Azure 虛擬網路可讓您將網路結構放入 Azure 部署中。 VNet 可以彼此隔離，也可以藉助網路安全性群組控制 VNet 之間的流量。
* Azure 虛擬網路可用來將 IP 位址範圍指派給 VM，或將固定 IP 位址指派給 VM
* 若要設定站對站或點對站連線，您必須先建立 Azure 虛擬網路
* 一旦部署虛擬機器，就無法再變更指派給 VM 的虛擬網路

### <a name="quotas-in-azure-virtual-machine-services"></a>Azure 虛擬機器服務中的配額
我們必須清楚了解在 Azure 基礎結構中執行各種服務的 VM 之間，會共用儲存體和網路基礎結構。 就像客戶自己的資料中心一樣，對某些基礎結構資源的布建也會在某種程度上進行。 Microsoft Azure 平台使用磁碟、CPU、網路和其他配額來限制資源耗用量，並保持一致且具決定性的效能。  不同的 VM 類型 (A5、A6 等) 會有不同的磁碟數、CPU、RAM 和網路配額。

> [!NOTE]
> 主機節點上會預先配置 SAP 支援之 VM 類型的 CPU 和記憶體資源。 這表示一旦部署 VM，就會如 VM 類型所定義來提供主機上的資源。
>
>

在 Azure 解決方案上規劃 SAP 及調整其大小時，必須考慮每部虛擬機器的大小配額。 如需 VM 配額的說明，請參閱[這裡 (Linux)][virtual-machines-sizes-linux] 和[這裡 (Windows)][virtual-machines-sizes-windows]。

所述配額代表理論上的最大值。  使用小型 i/o （8 KB）可達到每個磁片的 IOPS 限制，但可能無法使用大型 i/o （1 MB）來達成。  IOPS 限制會在單一磁碟的資料粒度上強制執行。

您可以使用下列決策樹，作為在判斷 SAP 系統是否符合 Azure 虛擬機器服務及其功能，或是現有的系統是否必須以不同方式設定才能在 Azure 上部署系統時的大致決策樹︰

![決定能否將 SAP 部署在 Azure 的決策樹][planning-guide-figure-700]

1. 若要開始著手進行，最重要的資訊就是所指定 SAP 系統的 SAPS 需求。 您必須將 SAPS 需求分為 DBMS 部分和 SAP 應用程式部分，即使 SAP 系統已在 2 層組態中內部部署亦然。 若是現有的系統，通常可根據現有的 SAP 基準來判斷或評估與使用中硬體相關的 SAPS。 您可以在[這裡](https://sap.com/about/benchmark.html)找到結果。 若是新部署的 SAP 系統，您應該已經完成調整大小練習，而能判斷系統的 SAPS 需求。 
1. 若為現有的系統，應該測量 DBMS 伺服器上的每秒 I/O 量和 I/O 作業數。 若為新規劃的系統，新系統的調整大小練習也應該讓您大致了解 DBMS 端的 I/O 需求。 如果不確定，您最終還是需要進行概念證明。
1. 將 DBMS 伺服器的 SAPS 需求，與不同的 Azure VM 類型可提供的 SAPS 進行比較。 如需有關不同 Azure VM 類型的 SAPS 資訊，請參閱 SAP 附註 [1928533]。 重點應該先放在 DBMS VM，因為資料庫層是大多數部署中不會向外延展的 SAP NetWeaver 系統層級。 相反地，SAP 應用程式層則可以向外延展。如果 SAP 支援的任何 Azure VM 類型都無法提供所需的 SAPS，則無法在 Azure 上執行規劃之 SAP 系統的工作負載。 您必須在內部部署系統，或必須變更系統的工作負載。
1. 如[這裡 (Linux)][virtual-machines-sizes-linux] 和[這裡 (Windows)][virtual-machines-sizes-windows] 所述，不論您使用的是「標準儲存體」還是「進階儲存體」，Azure 都會針對每個磁碟強制執行 IOPS 配額。 可掛接的資料磁碟數目會因 VM 類型而異。 因此，您可以計算每個不同的 VM 類型可達到的 IOPS 數目上限。 您可以根據資料庫檔案配置，將磁碟等量劃分成客體 OS 中的一個磁碟區。 不過，如果所部署 SAP 系統的目前 IOPS 磁碟區超過最大 Azure VM 類型的計算限制，而且沒有機會可以補償更多記憶體，則會嚴重影響 SAP 系統的工作負載。 在此情況下，您可能不應該在 Azure 上部署系統。
1. 特別是在於 2 層組態中內部部署的 SAP 系統中，有可能必須在一個 3 層組態的 Azure 上設定系統。 在此步驟中，您必須檢查 SAP 應用程式層中是否有元件無法向外延展，而且不符合不同 Azure VM 類型所提供的 CPU 和記憶體資源限制。 如果確實有此元件，則無法將 SAP 系統及其工作負載部署到 Azure。 但是，如果您可以將 SAP 應用程式元件向外延展到多個 Azure VM，則可以將系統部署到 Azure。

如果可以在 Azure VM 中執行 DBMS 和 SAP 應用程式層元件，則必須定義與下列各項相關的組態︰

* Azure VM 數目
* 個別元件的 VM 類型
* DBMS VM 中可提供足夠 IOPS 的 VHD 數目

## <a name="managing-azure-assets"></a>管理 Azure 資產

### <a name="azure-portal"></a>Azure 入口網站

Azure 入口網站是管理 Azure VM 部署的三個介面之一。 基本管理工作 (例如從映像部署 VM) 可透過 Azure 入口網站進行。 此外，儲存體帳戶、虛擬網路和其他 Azure 元件的建立作業也是 Azure 入口網站可妥善處理的工作。 不過，將 VHD 從內部部署環境上傳至 Azure 或在 Azure 中複製 VHD 等功能，則是需要協力廠商工具或是透過 PowerShell 或 CLI 管理的工作。

![Microsoft Azure 入口網站 - 虛擬機器概觀][planning-guide-figure-800]


您可以從 Azure 入口網站進行虛擬機器執行個體的管理和設定工作。

除了重新啟動及關閉虛擬機器之外，您也可以連結、中斷連結及建立虛擬機器執行個體的資料磁碟，以擷取執行個體供映像準備使用，並設定虛擬機器執行個體的大小。

Azure 入口網站提供用以部署及設定 VM 和許多其他 Azure 服務的基本功能。 不過，Azure 入口網站並未涵蓋所有可用的功能。 在 Azure 入口網站中，不可能執行下列工作：

* 將 VHD 上傳至 Azure
* 複製 VM


### <a name="management-via-microsoft-azure-powershell-cmdlets"></a>透過 Microsoft Azure PowerShell Cmdlet 管理

Windows PowerShell 是強大且可擴充的架構，客戶已廣泛採用此架構在 Azure 中部署大量系統。 在桌上型電腦、膝上型電腦或專用管理工作站上安裝 PowerShell Cmdlet 之後，即可從遠端執行 PowerShell Cmdlet。

如需了解讓本機桌上型電腦/膝上型電腦能夠使用 Azure PowerShell Cmdlet 的程序，以及如何設定這些項目以與 Azure 訂用帳戶搭配使用，請參閱[這篇文章][powershell-install-configure]。

如需有關如何安裝、更新及設定 Azure PowerShell Cmdlet 的更詳細步驟，另請參閱[部署指南的這一章][deployment-guide-4.1]。

根據客戶到目前為止的體驗，PowerShell (PS) 確實是可部署 VM 及建立 VM 部署之自訂步驟的更強大工具。 在 Azure 中執行 SAP 執行個體的所有客戶使用 PS Cmdlet 來支援他們在 Azure 入口網站中執行的管理工作，或甚至單獨使用 PS Cmdlet 來管理他們在 Azure 中的部署。 由於 Azure 特定的 Cmdlet 與超過 2000 個 Windows 相關 Cmdlet 共用相同的命名慣例，因此 Windows 系統管理員可輕鬆地利用這些 Cmdlet。

請參閱以下範例：<https://blogs.technet.com/b/keithmayer/archive/2015/07/07/18-steps-for-end-to-end-iaas-provisioning-in-the-cloud-with-azure-resource-manager-arm-powershell-and-desired-state-configuration-dsc.aspx> \(英文\)


您只能透過 PowerShell 或 CLI 來部署 Azure Extension for SAP (請參閱本文件中的 [Azure Extension for SAP][planning-guide-9.1] 一章)。 因此，在 Azure 中部署或管理 SAP NetWeaver 系統時，請務必安裝及設定 PowerShell 或 CLI。  

隨著 Azure 提供更多功能，也會新增 PS Cmdlet，而需要更新 Cmdlet。 因此，您每個月至少要檢查一次 Azure 下載網站 <https://azure.microsoft.com/downloads/>，以確認是否有新的 Cmdlet 版本。 新版本會覆蓋舊版本進行安裝。

如需 Azure 相關 PowerShell 命令的一般清單，請檢查這裡：<https://docs.microsoft.com/powershell/azure/>。

### <a name="management-via-microsoft-azure-cli-commands"></a>透過 Microsoft Azure CLI 命令管理

如果客戶使用 Linux 並想要管理 Azure 資源，則可能不適合使用 PowerShell。 Microsoft 提供 Azure CLI 作為替代方案。
Azure CLI 提供您一組開放原始碼的跨平台命令集合，供您運用在 Azure 平台上。 Azure CLI 提供許多與 Azure 入口網站相同的功能。

如需安裝、組態及如何使用 CLI 命令完成 Azure 工作的資訊，請參閱

* [安裝 Azure 傳統 CLI][xplat-cli]
* [使用 Azure Resource Manager 範本和 Azure CLI 部署和管理虛擬機器][../../linux/create-ssh-secured-vm-from-template.md]
* [搭配使用 Mac、Linux 和 Windows 適用的 Azure 傳統 CLI 與 Azure Resource Manager][xplat-cli-azure-resource-manager]

如需了解如何使用 Azure CLI 來部署 Azure Extension for SAP，另請參閱[部署指南][planning-guide]中的[適用於 Linux VM 的 Azure CLI][deployment-guide-4.5.2] 一章。


## <a name="first-steps-planning-a-deployment"></a>規劃部署的首要步驟
在規劃部署時，首要步驟是「不要」檢查可供用來執行 SAP 的 VM。 首要步驟可能很耗時但卻最為重要，那就是與公司內的合規性小組和安全性小組合作，以了解在將什麼類型的 SAP 工作負載或商務程序部署到公用雲端有何界限條件。 如果貴公司之前曾將其他軟體部署到 Azure 內，則程序會很容易。 如果貴公司才剛開始這趟旅程，則可能需要更大型的討論，以便釐清可讓特定 SAP 資料和 SAP 商務程序裝載於公用雲端的界限條件 (安全性條件)。

如需實用協助，您可以指向 [Microsoft 合規性供應項目](/microsoft-365/compliance/offering-home)，以取得 Microsoft 所能提供的合規性供應項目清單。 

其他考慮事項 (例如待用資料的資料加密或 Azure 服務中的其他加密) 則記載於 [Azure 加密概觀](../../../security/fundamentals/encryption-overview.md)。

在規劃時，千萬不要低估專案的這個階段。 只有在針對本主題達成一致看法並擬定好規則時，才需要移至下一個步驟，也就是規劃您在 Azure 中部署的網路架構。


## <a name="different-ways-to-deploy-vms-for-sap-in-azure"></a>在 Azure 中為 SAP 部署 VM 的不同方式

在本章中，您會學習在 Azure 中部署 VM 的不同方式。 本章涵蓋其他準備程序，以及在 Azure 中處理 VHD 和 VM 的方式。

### <a name="deployment-of-vms-for-sap"></a>為 SAP 部署 VM

Microsoft Azure 提供多種方法來部署 VM 和相關聯的磁碟。 因此，請務必了解這些差異，因為 VM 的準備工作可能會因部署方法而異。 大致上，我們將探討下列案例︰

#### <a name="moving-a-vm-from-on-premises-to-azure-with-a-non-generalized-disk"></a><a name="4d175f1b-7353-4137-9d2f-817683c26e53"></a>使用非一般化磁碟將 VM 從內部部署環境移至 Azure

您想要將特定 SAP 系統從內部部署移至 Azure。 這可藉由將包含 OS、SAP 二進位檔和 DBMS 二進位檔的 VHD，以及包含 DBMS 資料和記錄檔的 VHD 上傳至 Azure 來完成。 相對於[下面的案例 2][planning-guide-5.1.2]，您可以在 Azure VM 中，保留內部部署環境中所設定的主機名稱、SAP SID 及 SAP 使用者帳戶。 因此，不需要將映像一般化。 如需了解內部部署準備步驟，以及如何將非一般化 VM 或 VHD 上傳至 Azure，請參閱本文件的[準備使用非一般化磁碟將 VM 從內部部署環境移至 Azure][planning-guide-5.2.1] 一章。 請參閱[案例 3：使用非一般化 Azure VHD 搭配 SAP 從內部部署環境移動 VM][deployment-guide-3.4] 章節 (位於[部署指南][deployment-guide]中)，以了解在 Azure 中部署這類映像的詳細步驟。

#### <a name="deploying-a-vm-with-a-customer-specific-image"></a><a name="e18f7839-c0e2-4385-b1e6-4538453a285c"></a>使用客戶特定的映像部署 VM

基於您的 OS 或 DBMS 版本的特定修補程式需求，Azure Marketplace 所提供的映像可能不符合您的需求。 因此，您可能必須使用自己私人的 OS/DBMS VM 映像來建立 VM，之後可多次部署此映像。 為了準備這類私人映像以重複使用，必須考慮下列事項︰

---
> ![Windows][Logo_Windows] Windows
>
> 如需詳細資訊，請參閱：<https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed>Windows 設定 (例如 Windows SID 和主機名稱) 必須透過 sysprep 命令在內部部署 VM 抽象化/一般化。
>
>
> ![Linux][Logo_Linux] Linux
>
> 請依照這些適用於 [SUSE][virtual-machines-linux-create-upload-vhd-suse]、[Red Hat][virtual-machines-linux-redhat-create-upload-vhd] 或 [Oracle Linux][virtual-machines-linux-create-upload-vhd-oracle] 的文章中所述的步驟，來準備要上傳至 Azure 的 VHD。
>
>

---
如果您已在內部部署 VM (特別是針對 2 層系統) 中安裝 SAP 內容，您可以在部署 Azure VM 之後，透過 SAP Software Provisioning Manager 支援的執行個體重新命名程序來調整 SAP 系統設定 (SAP 附註 [1619720])。 如需了解內部部署準備步驟，以及如何將一般化 VM 上傳至 Azure，請參閱本文件的[準備使用客戶特定的映像為 SAP 部署 VM][planning-guide-5.2.2] 和[將 VHD 從內部部署環境上傳至 Azure][planning-guide-5.3.2] 章節。 請參閱[案例 2：使用自訂映像為 SAP 部署 VM][deployment-guide-3.3] 章節 (位於[部署指南][deployment-guide]中)，以了解在 Azure 中部署這類映像的詳細步驟。

#### <a name="deploying-a-vm-out-of-the-azure-marketplace"></a>從 Azure Marketplace 部署 VM

您想要從 Azure Marketplace 使用 Microsoft 或協力廠商提供的 VM 映像來部署 VM。 在 Azure 中部署您的 VM 之後，您可以遵循與內部部署環境相同的方針和工具，在 VM 中安裝 SAP 軟體及 (或) DBMS。 如需詳細的部署說明，請參閱[案例 1：從 Azure Marketplace 為 SAP 部署 VM][deployment-guide-3.2] 章節 (位於[部署指南][deployment-guide]中)。

### <a name="preparing-vms-with-sap-for-azure"></a><a name="6ffb9f41-a292-40bf-9e70-8204448559e7"></a>使用適用於 Azure 的 SAP 準備 VM

將 VM 上傳至 Azure 之前，您必須確定 VM 和 VHD 符合特定需求。 這些需求會因所使用的部署方法而有些微的不同。

#### <a name="preparation-for-moving-a-vm-from-on-premises-to-azure-with-a-non-generalized-disk"></a><a name="1b287330-944b-495d-9ea7-94b83aff73ef"></a>準備使用非一般化磁碟將 VM 從內部部署環境移至 Azure

常見的部署方法是將執行 SAP 系統的現有 VM 從內部部署移至 Azure。 該 VM 和 VM 中的 SAP 系統只能使用相同主機名稱並可能使用相同的 SAP SID 在 Azure 中執行。 在此情況下，VM 的客體 OS 不應該針對多個部署進行一般化。 如果內部部署網路已延伸至 Azure，則即使是相同的網域帳戶也能在 VM 內使用，就像其之前在內部部署環境中的使用。

準備您自己的 Azure VM 磁碟時的需求如下︰

* 原先包含作業系統的 VHD，其大小上限為 127 GB。 這項限制已在 2015 年 3 月底排除。 現在包含作業系統的 VHD，其大小最高可達 1 TB，做為任何其他 Azure 儲存體裝載的 VHD。
* 它必須是固定 VHD 格式。 Azure 尚未支援動態 VHD 或 VHDx 格式的 VHD。 當您使用 PowerShell Commandlet 或 CLI 上傳 VHD 時，動態 VHD 會轉換成靜態 VHD
* 掛接到 VM 並應該在 Azure 中再次掛接到 VM 的 VHD 也必須是固定 VHD 格式。 如需資料磁碟的大小限制，請參閱[這篇文章 (Linux)](../../linux/managed-disks-overview.md) 和[這篇文章 (Windows)](../../windows/managed-disks-overview.md)。 當您使用 PowerShell Commandlet 或 CLI 上傳 VHD 時，動態 VHD 會轉換成靜態 VHD
* 使用系統管理員權限新增另一個本機帳戶，此帳戶可供 Microsoft 支援服務使用，或指派為服務和應用程式執行所在的內容，直到部署 VM 並可使用更適當的使用者為止。
* 新增其他本機帳戶，因為特定部署案例可能需要這些帳戶。

---
> ![Windows][Logo_Windows] Windows
>
> 在此案例中，需要一般化 (sysprep) VM 才能上傳並在 Azure 上部署 VM。
> 請確定並未使用磁碟機 D:\，
> 並如本文件中的[為連接的磁碟設定自動掛接][planning-guide-5.5.3]一章所述，為連接的磁碟設定磁碟自動掛接。
>
> ![Linux][Logo_Linux] Linux
>
> 在此案例中，需要一般化 (waagent -deprovision) VM 才能上傳並在 Azure 上部署 VM。
> 確定不會使用 /mnt/resource，並透過 uuid 掛接所有磁碟。 針對 OS 磁碟，請確定確定開機載入器項目也會反映 uuid 型掛接。
>
>

---
#### <a name="preparation-for-deploying-a-vm-with-a-customer-specific-image-for-sap"></a><a name="57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3"></a>準備使用客戶特定的映像為 SAP 部署 VM

含有一般化 OS 的 VHD 檔案會儲存在 Azure 儲存體帳戶的容器中或作為受控磁碟映像。 您可以從這類映像部署新的 VM，方法是在部署範本檔案中將這類 VHD 或受控磁碟應項作為來源來參考，如[案例 2：使用自訂映像為 SAP 部署 VM][deployment-guide-3.3] 章節所述 (位於[部署指南][deployment-guide]中)。

準備您自己的 Azure VM 映像時的需求包括︰

* 原先包含作業系統的 VHD，其大小上限為 127 GB。 這項限制已在 2015 年 3 月底排除。 現在包含作業系統的 VHD，其大小最高可達 1 TB，做為任何其他 Azure 儲存體裝載的 VHD。
* 它必須是固定 VHD 格式。 Azure 尚未支援動態 VHD 或 VHDx 格式的 VHD。 當您使用 PowerShell Commandlet 或 CLI 上傳 VHD 時，動態 VHD 會轉換成靜態 VHD
* 掛接到 VM 並應該在 Azure 中再次掛接到 VM 的 VHD 也必須是固定 VHD 格式。 如需資料磁片的大小限制，請參閱[這篇文章（Linux）](../../windows/managed-disks-overview.md)和[這篇文章（Windows）](../../linux/managed-disks-overview.md) 。 當您使用 PowerShell Commandlet 或 CLI 上傳 VHD 時，動態 VHD 會轉換成靜態 VHD
* 新增其他本機帳戶，因為特定部署案例可能需要這些帳戶。
* 如果映像包含 SAP NetWeaver 的安裝，而且可能在部署 Azure 時重新命名主機名稱的原始名稱，則建議將最新版 SAP Software Provisioning Manager DVD 複製到範本。 這可讓您輕鬆地使用 SAP 提供的重新命名功能，來調整已變更的主機名稱，及 (或) 在啟動新複本之後，變更已部署 VM 映像中 SAP 系統的 SID。

---
> ![Windows][Logo_Windows] Windows
>
> 請確定並未使用磁碟機 D:\，並如本文件中的[為連接的磁碟設定自動掛接][planning-guide-5.5.3]一章所述，為連接的磁碟設定磁碟自動掛接。
>
> ![Linux][Logo_Linux] Linux
>
> 確定不會使用 /mnt/resource，並透過 uuid 掛接所有磁碟。 針對 OS 磁碟，確定開機載入器項目也會反映 uuid 型掛接。
>
>

---
* SAP GUI (用於管理和安裝) 可預先安裝在這類範本中。
* 只要此軟體可使用 VM 的重新命名功能，就能安裝在跨單位案例中成功執行 VM 所需的其他軟體。

如果已準備好 VM 進行一般化，而且最終與目標 Azure 部署案例中未提供的帳戶/使用者無關，則可以進行一般化這類映像的最後一個準備步驟。

##### <a name="generalizing-a-vm"></a>一般化 VM
---
> ![Windows][Logo_Windows] Windows
>
> 最後一個步驟是使用系統管理員帳戶登入 VM。 以「系統管理員身分」開啟 Windows 命令視窗。 移至 %windir%\windows\system32\sysprep and execute sysprep.exe。
> 隨即會出現一個小視窗。 請務必核取 [一般化] 選項 (預設不會核取)，並將 [關機] 選項從預設的 [重新開機] 變更為 [關機]。 此程序假設在 VM 的客體 OS 中以內部部署方式執行 sysprep 處理序。
> 如果您想要使用已在 Azure 中執行的 VM 來執行此程序，請依照[這篇文章](../../windows/capture-image-resource.md)所述的步驟操作。
>
> ![Linux][Logo_Linux] Linux
>
> [如何擷取 Linux 虛擬機器來作為 Resource Manager 範本使用][capture-image-linux-step-2-create-vm-image]
>
>

---
### <a name="transferring-vms-and-vhds-between-on-premises-to-azure"></a>在內部部署與 Azure 之間傳輸 VM 和 VHD
因為無法透過 Azure 入口網站將 VM 映像和磁碟上傳至 Azure，所以您必須使用 Azure PowerShell Cmdlet 或 CLI。 您也可以使用 ’AzCopy’ 工具。 此工具可在內部部署與 Azure 之間 (雙向) 複製 VHD。 它也可以在 Azure 區域之間複製 VHD。 請參閱[這份檔][storage-use-azcopy]，以取得 AzCopy 的下載和使用。

第三個替代方案是使用各種協力廠商 GUI 導向的工具。 不過，請確定這些工具支援 Azure 分頁 Blob。 基於我們的目的，我們需要使用 Azure 分頁 Blob 存放區 (下列網頁會描述其中的差異：<https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>)。 此外，Azure 所提供的工具可有效率地壓縮必須上傳的 VM 和 VHD。 這點很重要，因為此壓縮效率可縮短上傳時間 (也會因上傳的網際網路連結來自內部部署設施和目標 Azure 部署區域而異)。 您可以合理假設將 VM 或 VHD 從歐洲位置上傳至美國 Azure 資料中心，比將相同的 VM/VHD 上傳至歐洲 Azure 資料中心需要更長的時間。

#### <a name="uploading-a-vhd-from-on-premises-to-azure"></a><a name="a43e40e6-1acc-4633-9816-8f095d5a7b6a"></a>將 VHD 從內部部署環境上傳至 Azure
若要從內部部署網路上傳現有的 VM 或 VHD，這類 VM 或 VHD 必須符合本文件的[準備使用非一般化磁碟將 VM 從內部部署環境移至 Azure][planning-guide-5.2.1] 一章中所列的需求。

這類 VM 不需要進行一般化，並且可依內部部署端關機後的狀態和形態進行上傳。 此規則也適用於不含任何作業系統的其他 VHD。

##### <a name="uploading-a-vhd-and-making-it-an-azure-disk"></a>上傳 VHD 並將其設為 Azure 磁碟
在本例中，我們想要上傳 VHD (可含有或不含 OS)，並將其掛接到 VM，以作為資料磁碟或用作 OS 磁碟。 這是一個多步驟程序

**PowerShell**

* 使用 *Connect-AzAccount* 登入您的訂用帳戶
* 使用 *Set-AzContext* 和參數 SubscriptionId 或 SubscriptionName 來設定您內容的訂用帳戶；請參閱 <https://docs.microsoft.com/powershell/module/az.accounts/set-Azcontext> \(英文\)
* 使用 *Add-AzVhd* 將 VHD 上傳到 Azure 儲存體帳戶；請參閱 <https://docs.microsoft.com/powershell/module/az.compute/add-Azvhd> \(英文\)
* (選擇性) 使用 *New-AzDisk* 從 VHD 建立受控磁碟；請參閱 <https://docs.microsoft.com/powershell/module/az.compute/new-Azdisk> \(英文\)
* 使用 *Set-AzVMOSDisk* 將新 VM 設定的 OS 磁碟設定為 VHD 或受控磁碟；請參閱 <https://docs.microsoft.com/powershell/module/az.compute/set-Azvmosdisk> \(英文\)
* 使用 *New-AzVM* 從 VM 設定建立新的 VM；請參閱 <https://docs.microsoft.com/powershell/module/az.compute/new-Azvm> \(英文\)
* 使用 *Add-AzVMDataDisk* 將資料磁碟加入至新的 VM；請參閱 <https://docs.microsoft.com/powershell/module/az.compute/add-Azvmdatadisk> \(英文\)

**Azure CLI**

* 使用 *az login* 登入您的訂用帳戶
* 使用 *az account set --subscription `<subscription name or id`>* 來選取您的訂用帳戶
* 使用 *az storage blob upload* 來上傳 VHD - 請參閱[使用 Azure CLI 搭配 Azure 儲存體][storage-azure-cli]
* (選擇性) 使用 az disk create 從 VHD 建立受控磁碟；請參閱 https://docs.microsoft.com/cli/azure/disk
* 使用 *az vm create* 和參數 *--attach-os-disk* 來建立一個指定以上傳的 VHD 或受控磁碟作為作業系統磁碟的新 VM
* 使用 *az vm disk attach* 和參數 *--new* 來將資料磁碟新增至新的 VM

**範本**

* 使用 PowerShell 或 Azure CLI 上傳 VHD
* (選擇性) 使用 PowerShell、Azure CLI 或 Azure 入口網站從 VHD 建立受控磁碟
* 如[此 JSON 範本範例](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-specialized-vhd-new-or-existing-vnet/azuredeploy.json)所示使用參考 VHD 的 JSON 範本，或如[此 JSON 範本範例](https://github.com/Azure/azure-quickstart-templates/blob/master/sap-2-tier-user-image-md/azuredeploy.json)所示使用受控磁碟，來部署 VM。

#### <a name="deployment-of-a-vm-image"></a>VM 映像的部署
若要從內部部署網路上傳現有的 VM 或 VHD 以作為 Azure VM 映像，這類 VM 或 VHD 必須符合本文件的[準備使用客戶特定映像為 SAP 部署 VM][planning-guide-5.2.2] 一節中所列需求。

* 在 Windows 上使用 *sysprep* 或在 Linux 上使用 *waagent -deprovision* 以將您的 VM 一般化 - 請參閱 [Sysprep 技術參考](/previous-versions/windows/it-pro/windows-vista/cc766049(v=ws.10)) (適用於 Windows) 或[如何擷取 Linux 虛擬機器來作為 Resource Manager 範本使用][capture-image-linux-step-2-create-vm-image] (適用於 Linux)
* 使用 *Connect-AzAccount* 登入您的訂用帳戶
* 使用 *Set-AzContext* 和參數 SubscriptionId 或 SubscriptionName 來設定您內容的訂用帳戶；請參閱 <https://docs.microsoft.com/powershell/module/az.accounts/set-Azcontext> \(英文\)
* 使用 *Add-AzVhd* 將 VHD 上傳到 Azure 儲存體帳戶；請參閱 <https://docs.microsoft.com/powershell/module/az.compute/add-Azvhd> \(英文\)
* (選擇性) 使用 *New-AzImage* 從 VHD 建立受控磁碟映像；請參閱 <https://docs.microsoft.com/powershell/module/az.compute/new-Azimage> \(英文\)
* 將新 VM 設定的 OS 磁碟設定為
  * VHD (使用 *Set-AzVMOSDisk -SourceImageUri -CreateOption fromImage*)；請參閱 <https://docs.microsoft.com/powershell/module/az.compute/set-Azvmosdisk> \(英文\)
  * 受控磁碟映像 *Set-AzVMSourceImage*；請參閱 <https://docs.microsoft.com/powershell/module/az.compute/set-Azvmsourceimage> \(英文\)
* 使用 *New-AzVM* 從 VM 設定建立新的 VM；請參閱 <https://docs.microsoft.com/powershell/module/az.compute/new-Azvm> \(英文\)

**Azure CLI**

* 在 Windows 上使用 *sysprep* 或在 Linux 上使用 *waagent -deprovision* 以將您的 VM 一般化 - 請參閱 [Sysprep 技術參考](/previous-versions/windows/it-pro/windows-vista/cc766049(v=ws.10)) (適用於 Windows) 或[如何擷取 Linux 虛擬機器來作為 Resource Manager 範本使用][capture-image-linux-step-2-create-vm-image] (適用於 Linux)
* 使用 *az login* 登入您的訂用帳戶
* 使用 *az account set --subscription `<subscription name or id`>* 來選取您的訂用帳戶
* 使用 *az storage blob upload* 來上傳 VHD - 請參閱[使用 Azure CLI 搭配 Azure 儲存體][storage-azure-cli]
* (選擇性) 使用 az image create 從 VHD 建立受控磁碟映像；請參閱 https://docs.microsoft.com/cli/azure/image
* 使用 *az vm create* 和參數 *--image* 來建立一個指定以上傳的 VHD 或受控磁碟映像作為作業系統磁碟的新 VM

**範本**

* 在 Windows 上使用 *sysprep* 或在 Linux 上使用 *waagent -deprovision* 以將您的 VM 一般化 - 請參閱 [Sysprep 技術參考](/previous-versions/windows/it-pro/windows-vista/cc766049(v=ws.10)) (適用於 Windows) 或[如何擷取 Linux 虛擬機器來作為 Resource Manager 範本使用][capture-image-linux-step-2-create-vm-image] (適用於 Linux)
* 使用 PowerShell 或 Azure CLI 上傳 VHD
* (選擇性) 使用 PowerShell、Azure CLI 或 Azure 入口網站從 VHD 建立受控磁碟映像
* 如[此 JSON 範本範例](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-specialized-vhd-new-or-existing-vnet/azuredeploy.json)所示使用參考映像 VHD 的 JSON 範本，或如[此 JSON 範本範例](https://github.com/Azure/azure-quickstart-templates/blob/master/sap-2-tier-user-image-md/azuredeploy.json)所示使用受控磁碟映像，來部署 VM。

#### <a name="downloading-vhds-or-managed-disks-to-on-premises"></a>將 VHD 或受控磁碟下載至內部部署
Azure 基礎結構即服務不只可以單向上傳 VHD 和 SAP 系統， 您也可以將 SAP 系統從 Azure 移回內部部署世界。

下載期間無法啟動 VHD 或受控磁碟。 即使是下載掛接到 VM 的磁碟時，也必須關閉 VM 並解除配置。 如果您只想要下載資料庫內容，然後使用此內容在內部部署安裝新系統，而且如果在下載及安裝新系統的期間，Azure 中的系統仍然可以運作，則您可以藉由執行將壓縮的資料庫備份到磁碟，並只下載該磁碟而不是同時下載 OS 基底 VM，來避免停機時間過長。

#### <a name="powershell"></a>PowerShell

* 下載受控磁碟  
  您必須先取得受控磁碟的基礎 Blob 存取權。 然後您可以將基礎 Blob 複製到新的儲存體帳戶，並從這個儲存體帳戶下載 Blob。

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

* 下載 VHD  
  停止 SAP 系統並關閉 VM 之後，您可以在內部部署目標上使用 PowerShell Cmdlet，將 `Save-AzVhd` VHD 磁片下載回內部部署環境。 若要執行這項作業，需要 VHD 的 URL；此 URL 可以在 Azure 入口網站的 [儲存體] 區段中找到 (必須巡覽至儲存體帳戶及 VHD 建立所在的儲存體容器)，且您必須知道複製 VHD 的目的地。

  然後您可以利用此命令，方法是定義參數 SourceUri 作為要下載之 VHD 的 URL，並定義 LocalFilePath 作為 VHD (包括其名稱) 的實體位置。 此命令可能如下所示︰

  ```powerhell
  Save-AzVhd -ResourceGroupName <resource group name of storage account> -SourceUri http://<storage account name>.blob.core.windows.net/<container name>/sapidedata.vhd -LocalFilePath E:\Azure_downloads\sapidesdata.vhd
  ```

  如需 Save-AzVhd Cmdlet 的更多詳細資料，請參閱 <https://docs.microsoft.com/powershell/module/az.compute/save-Azvhd>。

#### <a name="azure-cli"></a>Azure CLI
* 下載受控磁碟  
  您必須先取得受控磁碟的基礎 Blob 存取權。 然後您可以將基礎 Blob 複製到新的儲存體帳戶，並從這個儲存體帳戶下載 Blob。

  ```azurecli
  az disk grant-access --ids "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" --duration-in-seconds 3600
  az storage blob download --sas-token "<sas token>" --account-name <account name> --container-name <container name> --name <blob name> --file <local file>
  az disk revoke-access --ids "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>"
  ```

* 下載 VHD   
  停止 SAP 系統並關閉 VM 之後，您可以使用內部部署目標上的 Azure CLI 命令，將 `_azure storage blob download_` VHD 磁片下載回內部部署環境。 若要執行這項作業，需要 VHD 的名稱和容器。VHD 的名稱和容器可以在 Azure 入口網站的 [儲存體] 區段中找到 (必須巡覽至儲存體帳戶及 VHD 建立所在的儲存體容器)。您也必須知道複製 VHD 的目的地。

  然後您可以利用此命令，方法是定義要下載之 VHD 的參數 blob 和 container，並定義 destination 作為 VHD (包括其名稱) 的實體目標位置。 此命令可能如下所示︰

  ```azurecli
  az storage blob download --name <name of the VHD to download> --container-name <container of the VHD to download> --account-name <storage account name of the VHD to download> --account-key <storage account key> --file <destination of the VHD to download>
  ```

### <a name="transferring-vms-and-disks-within-azure"></a>在 Azure 內傳輸 VM 和磁碟

#### <a name="copying-sap-systems-within-azure"></a>在 Azure 內複製 SAP 系統

SAP 系統或甚至是支援 SAP 應用程式層的專用 DBMS 伺服器很可能是由數個磁碟所組成，其中包含具有二進位檔的 OS 或 SAP 資料庫的資料和記錄檔。 複製磁碟的 Azure 功能或將磁碟儲存至本機磁碟的 Azure 功能都沒有同步處理機制，無法以一致的方式對多個磁碟進行快照。 因此，即使對相同 VM 掛接複製或儲存的磁碟，磁碟的狀態也不會相同。 這表示在不同磁碟含有不同資料和記錄檔的明確情況下，最後的資料庫可能會不一致。

**結論：若要複製或儲存包含在 SAP 系統設定中的磁碟，您必須停止 SAP 系統，也必須關閉已部署的 VM。唯有如此，您才能複製或下載磁碟集合，以在 Azure 中或在內部部署建立 SAP 系統複本。**

資料磁碟可儲存為 Azure 儲存體帳戶中的 VHD 檔案，而且可以直接連接到虛擬機器或用作映像。 在本例中，VHD 會複製到另一個位置，再連接到虛擬機器。 Azure 中之 VHD 檔案的完整名稱必須在 Azure 中是唯一的。 如前所述，此名稱是由三個部分所組成，如下所示︰

`http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>`

資料磁碟也可以是受控磁碟。 在本例中，先使用受控磁碟建立新的受控磁碟，再連接到虛擬機器。 受控磁碟的名稱必須是資源群組內唯一的。

##### <a name="powershell"></a>PowerShell

您可以使用 Azure PowerShell Cmdlet 來複製 VHD，如[這篇文章][storage-powershell-guide-full-copy-vhd]所示。 若要建立新的受控磁碟，請使用 New-AzDiskConfig 和 New-AzDisk，如下列範例所示。

```powershell
$config = New-AzDiskConfig -CreateOption Copy -SourceUri "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" -Location <location>
New-AzDisk -ResourceGroupName <resource group name> -DiskName <disk name> -Disk $config
```

##### <a name="azure-cli"></a>Azure CLI

您可以使用 Azure CLI 來複製 VHD。 若要建立新的受控磁碟，請使用 *az disk create*，如下列範例所示。

```azurecli
az disk create --source "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" --name <disk name> --resource-group <resource group name> --location <location>
```

##### <a name="azure-storage-tools"></a>Azure 儲存體工具

* <https://storageexplorer.com/>

您可以在下面找到 Azure 儲存體總管的專業版︰

* <https://www.cerebrata.com/>
* <https://clumsyleaf.com/products/cloudxplorer>

在儲存體帳戶中複製 VHD 本身是只需要幾秒鐘的程序 (類似於使用延遲複製和寫入時複製來建立快照集的 SAN 硬體)。 擁有 VHD 檔案複本之後，您可以將其連結到虛擬機器，或用作映像以將 VHD 複本連結到虛擬機器。

##### <a name="powershell"></a>PowerShell

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

```azurecli

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

#### <a name="copying-disks-between-azure-storage-accounts"></a><a name="9789b076-2011-4afa-b2fe-b07a8aba58a1"></a>在 Azure 儲存體帳戶之間複製磁碟
無法在 Azure 入口網站上執行這項工作。 您可以使用 Azure PowerShell Cmdlet、Azure CLI 或協力廠商儲存體瀏覽器。 PowerShell Cmdlet 或 CLI 命令可以建立及管理 Blob，其中包括能夠在 Azure 訂用帳戶內以非同步方式跨「儲存體帳戶」及跨區域複製 Blob。

##### <a name="powershell"></a>PowerShell
您也可以訂用帳戶之間複製 VHD。 如需詳細資訊，請參閱[這篇文章][storage-powershell-guide-full-copy-vhd]。

PS Cmdlet 邏輯的基本流程如下所示︰

* 使用 *New-AzStorageContext* 建立**來源**儲存體帳戶的儲存體帳戶內容；請參閱 <https://docs.microsoft.com/powershell/module/az.storage/new-AzStoragecontext>
* 使用 *New-AzStorageContext* 建立**目標**儲存體帳戶的儲存體帳戶內容；請參閱 <https://docs.microsoft.com/powershell/module/az.storage/new-AzStoragecontext>
* 如下開始複製

```powershell
Start-AzStorageBlobCopy -SrcBlob <source blob name> -SrcContainer <source container name> -SrcContext <variable containing context of source storage account> -DestBlob <target blob name> -DestContainer <target container name> -DestContext <variable containing context of target storage account>
```

* 如下檢查迴圈中的複製狀態

```powershell
Get-AzStorageBlobCopyState -Blob <target blob name> -Container <target container name> -Context <variable containing context of target storage account>
```

* 如上所述，將新的 VHD 連接到虛擬機器。

如需範例，請參閱[這篇文章][storage-powershell-guide-full-copy-vhd]。

##### <a name="azure-cli"></a>Azure CLI
* 如下開始複製

```azurecli
az storage blob copy start --source-blob <source blob name> --source-container <source container name> --source-account-name <source storage account name> --source-account-key <source storage account key> --destination-container <target container name> --destination-blob <target blob name> --account-name <target storage account name> --account-key <target storage account name>
```

* 如下檢查仍在迴圈中的複製狀態

```azurecli
az storage blob show --name <target blob name> --container <target container name> --account-name <target storage account name> --account-key <target storage account name>
```

* 如上所述，將新的 VHD 連接到虛擬機器。

### <a name="disk-handling"></a>磁碟處理

#### <a name="vmdisk-structure-for-sap-deployments"></a><a name="4efec401-91e0-40c0-8e64-f2dceadff646"></a>SAP 部署的 VM/磁碟結構

在理想情況下，VM 及相關聯磁碟的結構處理應該很簡單。 在內部部署安裝中，客戶開發了許多方法來結構化伺服器安裝。

* 一個基底磁碟，其中包含 OS 以及 DBMS 和/或 SAP 的所有二進位檔。 自2015年3月起，此磁片的大小最高可達 1 TB，而不是限制為 127 GB 的先前限制。
* 一或多個磁碟，其中包含 SAP 資料庫的 DBMS 記錄檔和 DBMS 暫存區記錄檔 (如果 DBMS 支援)。 如果資料庫記錄 IOPS 需求很高，您必須等量劃分多個磁碟的磁碟區，以達到所需的 IOPS 數量。
* 一些磁碟，其中包含 SAP 資料庫的一或兩個資料庫檔案，也包含 DBMS 暫存資料檔案 (如果 DBMS 支援)。

![適用於 SAP 之 Azure IaaS VM 的參考組態][planning-guide-figure-1300]


---
> ![Windows][Logo_Windows] Windows
>
> 我們發現在許多客戶的組態中，SAP 和 DBMS 二進位檔並未安裝在 OS 安裝所在的 c:\ 磁碟機中。 有各種原因會造成此情況，但回溯至根本原因時，通常是因為 10-15 年前的磁碟機很小，而且 OS 升級需要額外的空間。 最近則不太常發生這兩種情況。 今日，c:\ 磁碟機可對應至大量磁碟或 VM。 為了讓部署的結構保持簡單，建議遵循 Azure 中 SAP NetWeaver 系統的下列部署模式
>
> Windows 作業系統分頁檔應該在 D: 磁碟機 (非永續性磁碟) 上
>
> ![Linux][Logo_Linux] Linux
>
> 將 Linux 分頁檔放在 Linux 上的 /mnt /mnt/resource 下，如[這篇文章][virtual-machines-linux-agent-user-guide]所述。 您可以在 Linux 代理程式 /etc/waagent.conf 的組態檔中設定分頁檔。 新增或變更下列設定：
>
>

```console
ResourceDisk.EnableSwap=y
ResourceDisk.SwapSizeMB=30720
```

若要啟用變更，您必須如下重新啟動 Linux 代理程式

```console
sudo service waagent restart
```

如需建議的交換檔案大小的詳細資訊，請參閱 SAP 附注[1597355]

---
用於 DBMS 資料檔案的磁碟數目，以及裝載這些磁碟的 Azure 儲存體類型，應該取決 IOPS 需求和所需的延遲。 如需確切配額的說明，請參閱[這篇文章 (Linux)][virtual-machines-sizes-linux] 和[這篇文章 (Windows)][virtual-machines-sizes-windows]。

過去兩年的 SAP 部署體驗提供一些教訓，摘要如下：

* 不同資料檔案的 IOPS 流量不一定相同，因為現有的客戶系統可能會有代表其 SAP 資料庫之不同大小的資料檔案。 因此，最好在多個磁碟上使用 RAID 組態，以放置從中切割出的資料檔案 LUN。 有時 (特別是針對 Azure 標準儲存體) IOPS 速率會達到單一磁碟對 DBMS 交易記錄的配額。 在此情況下，建議使用進階儲存體，或是使用軟體帶狀線彙總多個標準儲存體磁碟。

---
> ![Windows][Logo_Windows] Windows
>
> * [Azure 虛擬機器中的 SQL Server 效能最佳做法][virtual-machines-sql-server-performance-best-practices]
>
> ![Linux][Logo_Linux] Linux
>
> * [在 Linux 上設定軟體 RAID][virtual-machines-linux-configure-raid]
> * [設定 Azure 中 Linux VM 的 LVM][virtual-machines-linux-configure-lvm]
>
>

---
* 進階儲存體顯示效能大幅提升，特別是針對重要的交易記錄寫入。 在必須提供產能的 SAP 案例中 (例如效能)，強烈建議使用可利用 Azure 進階儲存體的 VM 系列。

請記住，包含 OS 的磁片，如我們所建議，SAP 和資料庫（基底 VM）的二進位檔並不會再限制為 127 GB。 它現在最多可有 1 TB 的大小。 這對於保留所有必要檔案 (包含 SAP 批次工作記錄) 便已足夠。

如需其他建議和詳細資料 (特別是針對 DBMS VM)，請參閱 [DBMS 部署指南][dbms-guide]

#### <a name="disk-handling"></a>磁碟處理

在大多數情況下，您必須建立額外的磁碟，才能將 SAP 資料庫部署到 VM。 在本文件的 [SAP 部署的 VM/磁碟結構][planning-guide-5.5.1]一章中，我們已談到磁碟數目的考量。 Azure 入口網站允許在部署基底 VM 之後連接及中斷連接磁碟。 您可以在啟動及執行 VM 時，以及在停止 VM 時，連接/中斷連接磁碟。 連結磁碟時，Azure 入口網站可連結空的磁碟，或連結目前未連結到其他 VM 的現有磁碟。

**注意**：磁碟在任何指定的時間都只能連接到一個 VM。

![連接/中斷連接 Azure 標準儲存體的磁碟][planning-guide-figure-1400]

部署新虛擬機器時，您可以決定要使用受控磁碟或將磁碟放在 Azure 儲存體帳戶。 如果您想要使用進階儲存體，建議使用受控磁碟。

接著，您必須決定要建立新的空白磁碟，還是要選取稍早上傳且現在應該連接到 VM 的現有磁碟。

**重要事項**：建議您**不要**搭配「Azure 標準儲存體」使用「主機快取」。 您應該保留 [主機快取] 喜好設定的預設值 [無]。 使用 Azure 進階儲存體時，如果 I/O 特性大部分會讀取為類似對資料庫資料檔案的一般 I/O 流量，則應該啟用 [讀取快取]。 在資料庫交易記錄檔中，不建議使用快取。

---
> ![Windows][Logo_Windows] Windows
>
> [如何在 Azure 入口網站中連接資料磁碟][virtual-machines-linux-attach-disk-portal]
>
> 如果已連結磁碟，您必須登入 VM 來開啟 Windows 磁碟管理員。 如果未依照[為連接的磁碟設定自動掛接][planning-guide-5.5.3]一章所建議來啟用自動掛接，就必須使新連接的磁碟區上線並初始化。
>
> ![Linux][Logo_Linux] Linux
>
> 如果已連結磁碟，您必須登入 VM 並將磁碟初始化，如[本文][virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]中所述
>
>

---
如果新磁碟是空的磁碟，您也必須格式化磁碟。 格式化時，特別是針對 DBMS 資料和記錄檔，也適用對 DBMS 裸機部署的相同建議。

Azure 儲存體帳戶在 I/O 容量、IOPS 及資料量方面提供的資源有其限制。 這點對 DBMS VM 的影響通常最大。 如果您要部署之高 I/O 磁碟區的 VM 很少，最好針對每個 VM 使用個別的儲存體帳戶，以便維持在 Azure 儲存體帳戶磁碟區的限制內。 否則，您必須了解如何在不達到每個儲存體帳戶限制的情況下，於不同的儲存體帳戶之間平衡這些 VM。 如需更多詳細的討論，請參閱 [DBMS 部署指南][dbms-guide]。 針對完全使用 SAP 應用程式伺服器 VM，或最後可能需要更多 VHD 的其他 VM，您也應該記住這些限制。 如果您使用受控磁碟，並不適用這些限制。 如果您打算使用進階儲存體，建議使用受控磁碟。

另一個與儲存體帳戶相關的主題為：是否要對儲存體帳戶中的 VHD 進行異地複寫。 [異地複寫] 會在在儲存體帳戶層級 (而不是 VM 層級) 啟用或停用。 如果啟用 [異地複寫]，則會將儲存體帳戶中的 VHD 複寫至相同區域中的其他 Azure 資料中心。 在做這個決定之前，您應該考慮下列限制︰

Azure 異地複寫可在 VM 中的每個 VHD 上本機運作，而且不會依時間先後順序在 VM 中的多個 VHD 之間複寫 I/O。 因此，代表基底 VM 的 VHD 和連接到 VM 的任何其他 VHD 會彼此獨立複寫。 這表示不會同步處理不同 VHD 的變更。 由於複寫 I/O 的順序與寫入的順序無關，這表示異地複寫的值不是針對其資料庫已分散至多個 VHD 的資料庫伺服器。 除了 DBMS 之外，也可能會有其他應用程式，其中的處理序會在不同的 VHD 中寫入或處理資料，而且必須保持變更順序。 如果這是必要條件，則不應該在 Azure 中啟用 [異地複寫]。 根據您是否需要或想要對一組 VM 進行異地複寫，但不對另一組進行異地複寫，您可能已將 VM 及其相關的 VHD 分類到已啟用或停用 [異地複寫] 的不同儲存體帳戶。

#### <a name="setting-automount-for-attached-disks"></a><a name="17e0d543-7e8c-4160-a7da-dd7117a1ad9d"></a>為連接的磁碟設定自動掛接
---
> ![Windows][Logo_Windows] Windows
>
> 針對從自己的映像或磁碟建立的 VM，您必須檢查並可能設定自動掛接參數。 設定此參數可讓 VM 在 Azure 中重新啟動或重新部署之後，自動重新掛接已連接/掛接的磁碟機。
> 此參數會針對 Microsoft 在 Azure Marketplace 中提供的映像設定。
>
> 若要設定自動掛接，請參閱命令列可執行檔 diskpart.exe 的文件：
>
> * [DiskPart 命令列選項](/previous-versions/windows/it-pro/windows-xp/bb490893(v=technet.10))
> * [Automount (自動掛接)](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/cc753703(v=ws.11))
>
> 您應該以系統管理員身分開啟 Windows 命令列視窗。
>
> 如果已連結磁碟，您必須登入 VM 來開啟 Windows 磁碟管理員。 如果未依照[為連接的磁碟設定自動掛接][planning-guide-5.5.3]一章所建議來啟用自動掛接，就必須使新連接的磁碟區上線並初始化。
>
> ![Linux][Logo_Linux] Linux
>
> 您必須將新連接的空磁碟初始化，如[這篇文章][virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]所述。
> 您也必須將新磁碟加入 /etc/fstab。
>
>

---
### <a name="final-deployment"></a>最終部署

如需最終部署和確切步驟 (特別是與部署 Azure Extension for SAP 相關的步驟)，請參閱[部署指南][deployment-guide]。

## <a name="accessing-sap-systems-running-within-azure-vms"></a>存取在 Azure VM 中執行的 SAP 系統

針對要使用 SAP GUI 跨公用網際網路連線至這些 SAP 系統的情況，您必須套用下列的程序。

本文稍後將討論其他主要案例，這個案例會連線到跨單位部署中的 SAP 系統，這些部署在內部部署系統和 Azure 系統之間具有站對站連線 (VPN 通道) 或 Azure ExpressRoute 連線。

### <a name="remote-access-to-sap-systems"></a>遠端存取 SAP 系統

使用 Azure Resource Manager 時，不再有類似先前傳統模型中的預設端點。 只要符合下列情況，就可以開啟 Azure Resource Manager VM 的所有連接埠：

1. 未針對子網路或網路介面定義網路安全性群組。 可透過所謂的「網路安全性群組」來保護 Azure VM 的網路流量。 如需詳細資訊，請參閱 [什麼是網路安全性群組 (NSG)？][virtual-networks-nsg]
2. 未針對網路介面定義 Azure Load Balancer   

請參閱[這篇文章][virtual-machines-azure-resource-manager-architecture]所述之傳統模型與 ARM 之間的架構差異。

#### <a name="configuration-of-the-sap-system-and-sap-gui-connectivity-over-the-internet"></a>透過網際網路設定 SAP 系統和 SAP GUI 連線能力

請參閱這篇文章，其中描述本主題的詳細資料：<https://blogs.msdn.com/b/saponsqlserver/archive/2014/06/24/sap-gui-connection-closed-when-connecting-to-sap-system-in-azure.aspx>

#### <a name="changing-firewall-settings-within-vm"></a>變更 VM 中的防火牆設定

您可能必須在虛擬機器上設定防火牆，以允許 SAP 系統的輸入流量。

---
> ![Windows][Logo_Windows] Windows
>
> 預設會開啟 Azure 所部署之 VM 中的 Windows 防火牆。 您現在必須允許開啟 SAP 連接埠，否則 SAP GUI 將無法連線。
> 作法：
>
> * 將 [控制台\系統及安全性\Windows 防火牆] 開啟至 [進階設定]。
> * 現在以滑鼠右鍵按一下 [輸入規則]，然後選擇 [新增規則]。
> * 在下列精靈中，選擇建立新的「連接埠」規則。
> * 在精靈的下一個步驟中，保留 [TCP] 設定，然後輸入您要開啟的連接埠號碼。 因為我們的 SAP 執行個體 ID 為 00，所以我們採用 3200。 如果您的執行個體有不同的執行個體號碼，則會開啟稍早依據此執行個體號碼所定義的連接埠。
> * 在精靈的下一個部分，您必須保持核取 [允許連線] 項目。
> * 在精靈的下一個步驟中，您必須定義是否會針對網域、私人和公用網路套用此規則。 視需要調整它。 不過，透過公用網路從外部連線到 SAP GUI 時，您必須將規則套用至公用網路。
> * 在精靈的最後一個步驟中，為規則命名，然後按 [完成] 來儲存。
>
> 此規則會立即生效。
>
> ![連接埠規則定義][planning-guide-figure-1600]
>
> ![Linux][Logo_Linux] Linux
>
> Azure Marketplace 中的 Linux 映像預設不會啟用 iptables 防火牆，而 SAP 系統的連線應該會運作正常。 如果啟用 iptables 或其他防火牆，請參閱 iptables 或所使用防火牆的文件，來允許連接埠 32xx 的輸入 TCP 流量 (其中 xx 是 SAP 系統的系統編號)。
>
>

---
#### <a name="security-recommendations"></a>安全性建議

SAP GUI 不會立即連線到執行中的任何 SAP 執行個體 (連接埠 32xx)，而是先透過已開啟的連接埠連線到 SAP 訊息伺服器處理序 (連接埠 36xx)。 在過去，訊息伺服器會使用相同的連接埠，來對應用程式執行個體進行內部通訊。 為了防止內部部署應用程式伺服器不慎與 Azure 中的訊息伺服器通訊，您可以變更內部通訊連接埠。 強烈建議在已從內部部署系統複製的系統上 (例如專案測試等部署的複製)，將 SAP 訊息伺服器與其應用程式執行個體之間的內部通訊，變更為不同的連接埠號碼。您可以使用預設設定檔參數來完成此動作：

> rdisp/msserv_internal
>
>

如 [SAP 訊息伺服器的安全性設定](https://help.sap.com/saphelp_nwpi71/helpdata/en/47/c56a6938fb2d65e10000000a42189c/content.htm)中所述


### <a name="single-vm-with-sap-netweaver-demotraining-scenario"></a><a name="3e9c3690-da67-421a-bc3f-12c520d99a30"></a>搭配 SAP NetWeaver 示範/訓練案例的單一 VM

![在 Azure 雲端服務中以隔離方式執行具有相同 VM 名稱的單一 VM SAP 示範系統][planning-guide-figure-1700]

在此案例中，我們將實作一個典型的訓練/示範系統案例，其中完整的訓練/示範案例會包含在單一 VM 中。 我們假設部署是透過 VM 映像範本完成。 此外，也假設其中多個示範/訓練 VM 必須使用具有相同名稱的 VM 進行部署。 整個訓練系統無法連線到內部部署資產，且相對於混合式部署。

這裡假設您已如本文件中[使用適用於 Azure 的 SAP 準備 VM][planning-guide-5.2] 一章的某些小節所述建立 VM 映像。

實作案例的事件順序如下所示︰

##### <a name="powershell"></a>PowerShell

* 為每個訓練/示範環境建立新的資源群組

```powershell
$rgName = "SAPERPDemo1"
New-AzResourceGroup -Name $rgName -Location "North Europe"
```

* 如果您不想要使用受控磁碟，請建立新的儲存體帳戶

```powershell
$suffix = Get-Random -Minimum 100000 -Maximum 999999
$account = New-AzStorageAccount -ResourceGroupName $rgName -Name "saperpdemo$suffix" -SkuName Standard_LRS -Kind "Storage" -Location "North Europe"
```

* 為每個訓練/示範環境建立新的虛擬網路，以允許使用相同的主機名稱和 IP 位址。 虛擬網路受到網路安全性群組的保護，只允許連接埠 3389 的流量，以針對 SSH 啟用遠端桌面存取和連接埠 22。

```powershell
# Create a new Virtual Network
$rdpRule = New-AzNetworkSecurityRuleConfig -Name SAPERPDemoNSGRDP -Protocol * -SourcePortRange * -DestinationPortRange 3389 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 100
$sshRule = New-AzNetworkSecurityRuleConfig -Name SAPERPDemoNSGSSH -Protocol * -SourcePortRange * -DestinationPortRange 22 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 101
$nsg = New-AzNetworkSecurityGroup -Name SAPERPDemoNSG -ResourceGroupName $rgName -Location  "North Europe" -SecurityRules $rdpRule,$sshRule

$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name Subnet1 -AddressPrefix  10.0.1.0/24 -NetworkSecurityGroup $nsg
$vnet = New-AzVirtualNetwork -Name SAPERPDemoVNet -ResourceGroupName $rgName -Location "North Europe"  -AddressPrefix 10.0.1.0/24 -Subnet $subnetConfig
```

* 建立可用來從網際網路存取虛擬機器的新公用 IP 位址

```powershell
# Create a public IP address with a DNS name
$pip = New-AzPublicIpAddress -Name SAPERPDemoPIP -ResourceGroupName $rgName -Location "North Europe" -DomainNameLabel $rgName.ToLower() -AllocationMethod Dynamic
```

* 為虛擬機器建立新的網路介面

```powershell
# Create a new Network Interface
$nic = New-AzNetworkInterface -Name SAPERPDemoNIC -ResourceGroupName $rgName -Location "North Europe" -Subnet $vnet.Subnets[0] -PublicIpAddress $pip
```

* 建立虛擬機器。 針對此案例，每個 VM 會有相同的名稱。 這些 VM 中的 SAP NetWeaver 執行個體 SAP SID 也會相同。 在 Azure 資源群組中，VM 的名稱必須是唯一的，但在不同的 Azure 資源群組中，您可以執行具有相同名稱的 VM。 Windows 的預設「系統管理員」帳戶和 Linux 的「根」帳戶無效。 因此，新的系統管理員使用者名稱必須與密碼一起定義。 您也必須定義 VM 的大小。

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

* 選擇性地新增其他磁碟，並還原所需的內容。 請注意，所有 Blob 名稱 (Blob 的 URL) 在 Azure 中必須是唯一的。

```powershell
# Optional: Attach additional VHD data disks
$vm = Get-AzVM -ResourceGroupName $rgName -Name SAPERPDemo
$dataDiskUri = $account.PrimaryEndpoints.Blob.ToString() + "vhds/datadisk.vhd"
Add-AzVMDataDisk -VM $vm -Name datadisk -VhdUri $dataDiskUri -DiskSizeInGB 1023 -CreateOption empty | Update-AzVM

# Optional: Attach additional Managed Disks
$vm = Get-AzVM -ResourceGroupName $rgName -Name SAPERPDemo
Add-AzVMDataDisk -VM $vm -Name datadisk -DiskSizeInGB 1023 -CreateOption empty -Lun 0 | Update-AzVM
```

##### <a name="cli"></a>CLI

下列範例程式碼可用於 Linux。 針對 Windows，請依上述方式使用 PowerShell，或將範例調整成使用 %rgName% 而不是 $rgName，並使用 Windows 命令 *set* 來設定環境變數。

* 為每個訓練/示範環境建立新的資源群組

```azurecli
rgName=SAPERPDemo1
rgNameLower=saperpdemo1
az group create --name $rgName --location "North Europe"
```

* 建立新的儲存體帳戶

```azurecli
az storage account create --resource-group $rgName --location "North Europe" --kind Storage --sku Standard_LRS --name $rgNameLower
```

* 為每個訓練/示範環境建立新的虛擬網路，以允許使用相同的主機名稱和 IP 位址。 虛擬網路受到網路安全性群組的保護，只允許連接埠 3389 的流量，以針對 SSH 啟用遠端桌面存取和連接埠 22。

```azurecli
az network nsg create --resource-group $rgName --location "North Europe" --name SAPERPDemoNSG
az network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGRDP --protocol \* --source-address-prefix \* --source-port-range \* --destination-address-prefix \* --destination-port-range 3389 --access Allow --priority 100 --direction Inbound
az network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGSSH --protocol \* --source-address-prefix \* --source-port-range \* --destination-address-prefix \* --destination-port-range 22 --access Allow --priority 101 --direction Inbound

az network vnet create --resource-group $rgName --name SAPERPDemoVNet --location "North Europe" --address-prefixes 10.0.1.0/24
az network vnet subnet create --resource-group $rgName --vnet-name SAPERPDemoVNet --name Subnet1 --address-prefix 10.0.1.0/24 --network-security-group SAPERPDemoNSG
```

* 建立可用來從網際網路存取虛擬機器的新公用 IP 位址

```azurecli
az network public-ip create --resource-group $rgName --name SAPERPDemoPIP --location "North Europe" --dns-name $rgNameLower --allocation-method Dynamic
```

* 為虛擬機器建立新的網路介面

```azurecli
az network nic create --resource-group $rgName --location "North Europe" --name SAPERPDemoNIC --public-ip-address SAPERPDemoPIP --subnet Subnet1 --vnet-name SAPERPDemoVNet
```

* 建立虛擬機器。 針對此案例，每個 VM 會有相同的名稱。 這些 VM 中的 SAP NetWeaver 執行個體 SAP SID 也會相同。 在 Azure 資源群組中，VM 的名稱必須是唯一的，但在不同的 Azure 資源群組中，您可以執行具有相同名稱的 VM。 Windows 的預設「系統管理員」帳戶和 Linux 的「根」帳戶無效。 因此，新的系統管理員使用者名稱必須與密碼一起定義。 您也必須定義 VM 的大小。

```azurecli
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

```azurecli
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

* 選擇性地新增其他磁碟，並還原所需的內容。 請注意，所有 Blob 名稱 (Blob 的 URL) 在 Azure 中必須是唯一的。

```azurecli
# Optional: Attach additional VHD data disks
az vm unmanaged-disk attach --resource-group $rgName --vm-name SAPERPDemo --size-gb 1023 --vhd-uri https://$rgNameLower.blob.core.windows.net/vhds/data.vhd  --new

# Optional: Attach additional Managed Disks
az vm disk attach --resource-group $rgName --vm-name SAPERPDemo --size-gb 1023 --disk datadisk --new
```

##### <a name="template"></a>[範本]

您可以使用 GitHub 上的 Azure 快速入門-範本存放庫中的範例範本。

* [簡單的 Linux VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
* [簡單的 Windows VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
* [來自映像的 VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

### <a name="implement-a-set-of-vms-that-communicate-within-azure"></a>實作一組會在 Azure 內通訊的 VM

此非混合式案例是用於訓練和示範的典型案例，其中代表訓練/示範案例的軟體會散佈到多個 VM。 安裝在不同 VM 中的不同元件必須能夠彼此通訊。 同樣地，在此案例中，不需要內部部署網路通訊或跨單位案例。

此案例是本文件的[搭配 SAP NetWeaver 示範/訓練案例的單一 VM][planning-guide-7.1] 一章中所述安裝的延伸。 在此情況下，會將更多虛擬機器新增至現有的資源群組。 在下列範例中，定型環境是由 SAP ASCS/SCS VM、執行 DBMS 的 VM 和 SAP 應用程式伺服器執行個體 VM 所組成。

建置此案例之前，您必須考慮先前案例中已練習過的基本設定。

#### <a name="resource-group-and-virtual-machine-naming"></a>資源群組和虛擬機器命名

所有資源群組名稱必須是唯一的。 請開發您自己的資源命名配置，例如 `<rg-name`>-尾碼。

虛擬機器名稱在資源群組內必須是唯一的。

#### <a name="set-up-network-for-communication-between-the-different-vms"></a>設定網路以在不同的 VM 之間進行通訊

![Azure 虛擬網路內的 VM 集合][planning-guide-figure-1900]

若要避免與相同訓練/示範環境複製發生命名衝突，您必須為每個環境建立 Azure 虛擬網路。 Azure 將會提供 DNS 名稱解析，您也可以在 Azure 外設定自己的 DNS 伺服器 (此處不會進一步討論)。 在本案例中，我們不會設定自己的 DNS。 針對一個 Azure 虛擬網路內的所有虛擬機器，會啟用透過主機名稱進行通訊。

依虛擬網路 (而不只是依資源群組) 分隔訓練或示範環境的原因可能如下︰

* 設定的 SAP 環境需要它自己的 AD/OpenLDAP，而且網域伺服器需要成為各環境的一部分。  
* 設定的 SAP 環境具有需要使用固定 IP 位址的元件。

如需有關「Azure 虛擬網路」及如何定義這些網路的更多詳細資料，請參閱[這篇文章][virtual-networks-create-vnet-arm-pportal]。

## <a name="deploying-sap-vms-with-corporate-network-connectivity-cross-premises"></a>使用公司網路連線能力（跨單位）部署 SAP Vm

您執行 SAP 環境，並想要在高階 DBMS 伺服器的裸機，以及應用程式層及更小型 2 層已設定 SAP 系統和 Azure IaaS 的內部部署虛擬環境之間分割部署。 基本假設是一個 SAP 環境內的 SAP 系統必須彼此通訊，並在公司中部署許多其他軟體元件，而不論其部署形式為何。 此外，使用 SAP GUI 或其他介面進行連線之使用者的部署形式應該不會引進任何差異。 只有在已透過站對站/多網站連線能力或私人連線 (例如 Azure ExpressRoute)，將內部部署 Active Directory/OpenLDAP 和 DNS 服務擴充到 Azure 系統時，才符合這些條件。



### <a name="scenario-of-an-sap-landscape"></a>SAP 環境的案例

跨單位或混合式案例大致上可以下圖描述︰

![內部部署與 Azure 資產之間的站對站連線能力][planning-guide-figure-2100]

您必須至少使用 SSL/TLS 等安全的通訊協定，才能存取瀏覽器，或至少必須使用 VPN 連線，才能讓系統存取 Azure 服務。 假設公司處理其公司網路與 Azure 之間 VPN 連線的方式不同。 有些公司可能在不加思索的情況下開啟所有連接埠。 另一些公司可能想要能夠精確地開啟所需的連接埠等。

下表列出一般 SAP 通訊連接埠。 基本上，這便足已開啟 SAP 閘道連接埠。

<!-- sapms is prefix of a SAP service name and not a spelling error -->

| 服務 | 連接埠名稱 | 範例 `<nn`> = 01 | 預設範圍 (最小值-最大值) | 註解 |
| --- | --- | --- | --- | --- |
| 發送器 |sapdp`<nn>` 請參閱 * |3201 |3200 - 3299 |SAP 發送器，供 Windows 和 Java 的 SAP GUI 使用 |
| 訊息伺服器 |sapms`<sid`> 請參閱 ** |3600 |任意 sapms`<anySID`> |sid = SAP 系統 ID |
| 閘道 |sapgw`<nn`> 請參閱 * |3301 |free |SAP 閘道，用於 CPIC 和 RFC 通訊 |
| SAP 路由器 |sapdp99 |3299 |任意 |安裝後，只能將 /etc/services 中的 CI (中央執行個體) 服務名稱重新指派為任意值。 |

*) nn = SAP 執行個體號碼

**) sid = SAP 系統 ID

如需不同 SAP 產品或 SAP 產品所提供之服務所需的連接埠詳細資訊，請參閱 <https://scn.sap.com/docs/DOC-17124> \(英文\)。
透過本文，您應該能夠在 VPN 裝置中，開啟特定 SAP 產品和案例所需的專用連接埠。

在此情況下部署 VM 時，其他安全性措施可能還包括建立[網路安全性群組][virtual-networks-nsg]來定義存取規則。



#### <a name="printing-on-a-local-network-printer-from-sap-instance-in-azure"></a>在 Azure 中從 SAP 執行個體的區域網路印表機進行列印

##### <a name="printing-over-tcpip-in-cross-premises-scenario"></a>在跨單位案例中透過 TCP/IP 進行列印

在 Azure VM 中設定內部部署 TCP/IP 網路印表機就像是在公司網路中設定一樣，都會假設您已確實建立 VPN 站對站通道或 ExpressRoute 連線。

---
> ![Windows][Logo_Windows] Windows
>
> 作法：
>
> * 有些網路印表機隨附組態精靈，可讓您輕鬆地在 Azure VM 中設定印表機。 如果印表機未隨附任何精靈軟體，設定印表機的手動方式是建立新的 TCP/IP 印表機連接埠。
> * 開啟 [控制台] -> [裝置和印表機] -> [新增印表機]
> * 選擇 [使用 TCP/IP 位址或主機名稱新增印表機]
> * 輸入印表機的 IP 位址
> * 印表機連接埠標準 9100
> * 如有必要，請手動安裝適當的印表機驅動程式。
>
> ![Linux][Logo_Linux] Linux
>
> * 類似於 Windows，只要遵循標準程序即可安裝網路印表機
> * 只要遵循 [SUSE](https://www.suse.com/documentation/sles-12/book_sle_deployment/data/sec_y2_hw_print.html) 或 [Red Hat 和 Oracle Linux](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Deployment_Guide/sec-Printer_Configuration.html) 的公用 Linux 指南中有關如何新增印表機的說明操作即可。
>
>

---
![網路列印][planning-guide-figure-2200]

##### <a name="host-based-printer-over-smb-shared-printer-in-cross-premises-scenario"></a>在跨單位案例中透過 SMB 的主機型印表機 (共用印表機)

根據設計，主機型印表機與網路不相容。 但只要主機型印表機連線到開啟電源的電腦，就可以在網路上的電腦之間共用印表機。 請以站對站或 ExpressRoute 方式連線到您的公司網路，並共用您的本機印表機。 SMB 通訊協定使用 NetBIOS 作為名稱服務，而不是 DNS。 NetBIOS 主機名稱可以與 DNS 主機名稱不同。 標準情況是 NetBIOS 主機名稱和 DNS 主機名稱完全相同。 DNS 網域在 NetBIOS 命名空間中毫無意義。 因此，完整的 DNS 主機名稱是由 DNS 主機名稱所組成，而且 NetBIOS 命名空間中不能使用 DNS 網域。

印表機共用將透過網路中的唯一名稱來識別︰

* SMB 主機的主機名稱 (一律需要)。
* 共用的名稱 (一律需要)。
* 如果印表機共用與 SAP 系統不在相同的網域中，則為網域的名稱。
* 此外，還可能需要使用者名稱和密碼，才能存取印表機共用。

如何：

---
> ![Windows][Logo_Windows] Windows
>
> 共用您的本機印表機。
> 在 Azure VM 中，開啟 Windows 檔案總管，並輸入印表機的共用名稱。
> [印表機安裝精靈] 將會引導您完成安裝程序。
>
> ![Linux][Logo_Linux] Linux
>
> 以下是有關在 Linux 中設定網路印表機，或包含在 Linux 中列印之相關章節的一些文件範例。 只要 VM 是 VPN 的一部分，就會以在 Azure Linux VM 中的相同方式來運作︰
>
> * SLES <https://en.opensuse.org/SDB:Printing_via_SMB_(Samba)_Share_or_Windows_Share> \(英文\)
> * RHEL 或 Oracle Linux <https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/System_Administrators_Guide/sec-Printer_Configuration.html#s1-printing-smb-printer> \(英文\)
>
>

---
##### <a name="usb-printer-printer-forwarding"></a>USB 印表機 (印表機轉送)

在 Azure 中，可讓使用者在遠端工作階段中存取本機印表機裝置的遠端桌面服務功能無法使用。

---
> ![Windows][Logo_Windows] Windows
>
> 如需使用 Windows 進行列印的更多詳細資料，請參閱：<https://technet.microsoft.com/library/jj590748.aspx> \(英文\)。
>
>

---
#### <a name="integration-of-sap-azure-systems-into-correction-and-transport-system-tms-in-cross-premises"></a>在跨單位中將 SAP Azure 系統整合到 Correction and Transport System (TMS)

您必須設定 SAP Change and Transport System (TMS)，才能在環境中的不同系統之間匯出及匯入傳輸要求。 假設 SAP 系統 (DEV) 的開發執行個體位於 Azure 中，而品質保證 (QA) 和生產系統 (PRD) 在內部部署。 此外，假設有一個中央傳輸目錄。

##### <a name="configuring-the-transport-domain"></a>設定傳輸網域

在您指定為「傳輸網域控制站」的系統上設定「傳輸網域」，如 [Configuring the Transport Domain Controller (設定傳輸網域控制站)](https://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm)所述。 這會建立系統使用者 TMSADM，並產生必要的 RFC 目的地。 您可以查看使用交易 SM59 的這些 RFC 連線。 您必須在傳輸網域內啟用主機名稱解析。

如何：

* 在本案例中，我們決定內部部署 QAS 系統將成為 CTS 網域控制站。 呼叫交易 STMS。 [TMS] 對話方塊隨即顯示。 [Configure Transport Domain]\(設定傳輸網域) 對話方塊隨即顯示 (只有在您尚未設定傳輸網域時，才會顯示此對話方塊)。
* 確定自動建立的使用者 TMSADM 已獲得授權 ([SM59] -> [ABAP Connection]\(ABAP 連線) -> [TMSADM@E61.DOMAIN_E61] -> [Details]\(詳細資料) -> [Utilities(M)]\(公用程式(M)) -> [Authorization Test]\(授權測試))。 交易 STMS 的初始畫面應該顯示此 SAP 系統現在會作為傳輸網域的控制站，如下所示：

![網域控制站上的交易 STMS 初始畫面][planning-guide-figure-2300]

#### <a name="including-sap-systems-in-the-transport-domain"></a>將 SAP 系統加入傳輸網域

將 SAP 系統加入傳輸網域的順序如下所示︰

* 在 Azure 的 DEV 系統上，移至傳輸系統 (用戶端 000) 並呼叫交易 STMS。 從對話方塊選擇 [Other Configuration]\(其他組態)，並繼續進行 [Include System in Domain]\(將系統加入網域)。 將「網域控制站」指定為目標主機 ([Including SAP Systems in the Transport Domain (將 SAP 系統加入傳輸網域)](https://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0c17acc11d1899e0000e829fbbd/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm))。 系統正在等候加入傳輸網域。
* 基於安全性理由，您必須接著回到網域控制站確認您的要求。 針對等候中系統，選擇 [系統概觀] 和 [核准]。 然後確認提示，設定會隨即發佈。

此 SAP 系統現在包含有關傳輸網域中所有其他 SAP 系統的必要資訊。 同時會將新 SAP 系統的位址資料傳送至所有其他 SAP 系統，並在傳輸控制程式的傳輸設定檔中輸入 SAP 系統。 檢查網域之傳輸目錄的 RFC 和存取是否運作正常。

如 [Change and Transport System (變更和傳輸系統)](https://help.sap.com/saphelp_nw70ehp3/helpdata/en/48/c4300fca5d581ce10000000a42189c/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm)一文所述，像往常一樣繼續設定傳輸系統。

如何：

* 確定您在內部部署的 STMS 已正確設定。
* 確定您在 Azure 上的虛擬機器可解析傳輸網域控制站的主機名稱，反之亦然。
* 呼叫交易 STMS -> [Other Configuration]\(其他組態) -> [Include System in Domain]\(將系統加入網域)。
* 確認內部部署 TMS 系統已連線。
* 像往常一樣，設定傳輸路由、群組和層級。

在站對站連線的跨單位案例中，內部部署與 Azure 之間的延遲時間可能仍然很長。 如果我們遵循傳輸物件通過開發和測試系統到生產環境的順序，並考慮將傳輸或支援封裝套用至不同的系統，請了解根據中央傳輸目錄的位置，部分系統將會遇到讀取或寫入中央傳輸目錄的延遲很高的情況。 此情況類似於 SAP 環境組態，其中不同的系統會散佈到不同的資料中心，而且這些資料中心彼此距離很遠。

為了解決此延遲問題，並讓系統快速地從傳輸目錄讀取或寫入傳輸目錄，您可以設定兩個 STMS 傳輸網域 (一個用於內部部署，一個用於 Azure 中的系統)，並連結傳輸網域。 請參閱此檔，其中說明在 SAP TM 中此概念背後的原則： <https://help.sap.com/saphelp_me60/helpdata/en/c4/6045377b52253de10000009b38f889/content.htm?frameset=/en/57/38dd924eb711d182bf0000e829fbfe/frameset.htm> 。

如何：

* 使用交易 STMS 來設定每個位置 (內部部署和 Azure) 上的傳輸網域 <https://help.sap.com/saphelp_nw70ehp3/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm> \(英文\)
* 使用網域連結來連結網域，並確認兩個網域之間的連結。
  <https://help.sap.com/saphelp_nw73ehp1/helpdata/en/a3/139838280c4f18e10000009b38f8cf/content.htm>
* 將組態分散到連結的系統。

#### <a name="rfc-traffic-between-sap-instances-located-in-azure-and-on-premises-cross-premises"></a>在 Azure 中及內部部署的 SAP 執行個體之間的 RFC 流量 (跨單位)

在內部部署與 Azure 中的系統之間的 RFC 流量必須運作正常。 若要設定連線，請呼叫來源系統中的交易 SM59，您必須在此系統中定義目標系統的 RFC 連線。 此組態類似於 RFC 連線的標準設定。

假設在跨單位案例中，執行 SAP 系統且必須彼此通訊的 VM 位於相同網域中。 因此，SAP 系統之間的 RFC 連線設定，與內部部署案例中的設定步驟和輸入並無不同。

#### <a name="accessing-local-fileshares-from-sap-instances-located-in-azure-or-vice-versa"></a>從 Azure 中的 SAP 執行個體存取本機檔案共用，反之亦然

Azure 中的 SAP 執行個體必須能夠存取公司內部的檔案共用。 此外，內部部署 SAP 執行個體必須能夠存取 Azure 中的檔案共用。 若要啟用檔案共用，您必須設定本機系統上的權限和共用選項。 請務必在 Azure 與您的資料中心之間，開啟 VPN 或 ExpressRoute 連線的連接埠。

## <a name="supportability"></a>支援能力

### <a name="azure-extension-for-sap"></a><a name="6f0a47f3-a289-4090-a053-2521618a28c3"></a>Azure Extension for SAP

為了將任務關鍵性 SAP 系統的某些 Azure 基礎結構資訊部分饋送到已安裝在 VM 中的 SAP 主機代理程式執行個體，您必須為已部署的 VM 安裝 Azure (VM) Extension for SAP。 由於 SAP 的需求是專門針對 SAP 應用程式，因此 Microsoft 決定不將必要功能的實作一般化到 Azure，而由客戶將必要的 VM 擴充功能和設定，部署到他們在 Azure 中執行的虛擬機器。 不過，Azure VM Extension for SAP 的部署和生命週期管理大部分會由 Azure 自動化。

#### <a name="solution-design"></a>解決方案設計

為了讓 SAP 主機代理程式取得所需資訊而開發的解決方案，是以 Azure VM 代理程式和擴充功能架構的架構作為基礎的。 Azure VM 代理程式和擴充功能架構的概念是，允許在 VM 中安裝 Azure VM 擴充功能資源庫中可用的軟體應用程式。 此概念背後的主要概念是，允許 (以 Azure Extension for SAP 為例) 將特殊功能部署到 VM，並在部署時設定這類軟體。

在 Azure 入口網站中建立 VM 時，預設會將可在 VM 中處理特定 Azure VM 擴充功能的「Azure VM 代理程式」，插入 Windows VM。 若是 SUSE、Red Hat 或 Oracle Linux，則 VM 代理程式已經包含在 Azure Marketplace 映像中。 如果使用者會將 Linux VM 從內部部署上傳至 Azure，則必須手動安裝 VM 代理程式。

用來在 Azure 中向 SAP 主機代理程式提供 Azure 基礎結構資訊的解決方案基本建置組塊如下所示：

![Microsoft Azure 擴充功能元件][planning-guide-figure-2400]

如上面的區塊圖所示，解決方案的一部分會裝載於 Azure VM 映像和 Azure 擴充功能資源庫，後者是由 Azure 營運小組管理的全域複寫存放庫。 SAP/MS 聯合團隊會負責處理 SAP 的 Azure 實作，並與 Azure 營運團隊合作發佈新版 Azure Extension for SAP。

當您部署新的 Windows VM 時，會自動將 Azure VM 代理程式新增至 VM。 此代理程式的功能是協調 Azure VM 擴充功能的載入和設定。 針對 Linux VM，Azure VM 代理程式已是 Azure Marketplace OS 映像的一部分。

不過，客戶仍必須執行一個步驟。 那就是啟用及設定效能收集。 設定的相關程序會由 PowerShell 指令碼或 CLI 命令自動化。 您可以從「Microsoft Azure 指令碼中心」下載 PowerShell 指令碼，如[部署指南][deployment-guide]所述。

Azure Extension for SAP 的整體架構如下所示︰

![Azure Extension for SAP ][planning-guide-figure-2500]

**如需確切的操作說明，以及在部署期間使用這些 PowerShell Cmdlet 或 CLI 命令的詳細步驟，請遵循[部署指南][deployment-guide]中所提供的指示。**

### <a name="integration-of-azure-located-sap-instance-into-saprouter"></a>將位於 Azure 的 SAP 執行個體整合到 SAProuter

在 Azure 中執行的 SAP 執行個體也必須能夠從 SAProuter 存取。

![SAP 路由器網路連線][planning-guide-figure-2600]

如果沒有直接的 IP 連線，SAProuter 可啟用參與系統之間的 TCP/IP 通訊。 這樣做的優點是，通訊合作夥伴之間不需要有網路層級的端對端連線。 SAProuter 預設會在連接埠 3299 接聽。
若要透過 SAProuter 連線 SAP 執行個體，您必須提供任何連線嘗試的 SAProuter 字串和主機名稱。

## <a name="sap-netweaver-as-java"></a>SAP NetWeaver AS Java

到目前為止，本文的重點放在 SAP NetWeaver 概觀或 SAP NetWeaver ABAP 堆疊。 在本小節中，會列出 SAP Java 堆疊的特定考量。 完全以 Java 為主的其中一個最重要 SAP NetWeaver 應用程式是 SAP 企業版入口網站。 其他 SAP NetWeaver 應用程式 (例如 SAP PI 和 SAP Solution Manager) 會同時使用 SAP NetWeaver ABAP 和 Java 堆疊。 因此，當然也需要考慮與 SAP NetWeaver Java 堆疊相關的特定層面。

### <a name="sap-enterprise-portal"></a>SAP 企業版入口網站

如果您想要在跨單位案例中進行部署，Azure 虛擬機器中的 SAP 入口網站安裝與內部部署安裝並無不同。 因為 DNS 是在內部部署進行，所以可以像是已設定的內部部署來進行個別執行個體的連接埠設定。 一般而言，本文所述的建議和限制目前適用於 SAP 企業版入口網站或 SAP NetWeaver Java 堆疊等應用程式。

![公開的 SAP 入口網站][planning-guide-figure-2700]

有些客戶的特殊部署案例會直接向網際網路公開 SAP 企業版入口網站，而且虛擬機器主機會透過站對站 VPN 通道或 ExpressRoute 連線到公司網路。 在此案例中，您必須確定特定連接埠已開啟，並且未遭到防火牆或網路安全性群組封鎖。 

初始入口網站 URI 為 http(s):`<Portalserver`>:5XX00/irj，其中連接埠的形成如 SAP 在 <https://help.sap.com/saphelp_nw70ehp1/helpdata/de/a2/f9d7fed2adc340ab462ae159d19509/frameset.htm> 所記載。

![端點組態][planning-guide-figure-2800]

如果您想要自訂 SAP 企業版入口網站的 URL 及/或連接埠，請參閱下列文件：

* [Change Portal URL (變更入口網站 URL)](https://wiki.scn.sap.com/wiki/display/EP/Change+Portal+URL)
* [Change Default port numbers, Portal port numbers (變更預設連接埠號碼、入口網站連接埠號碼)](https://wiki.scn.sap.com/wiki/display/NWTech/Change+Default++port+numbers%2C+Portal+port+numbers)

## <a name="high-availability-ha-and-disaster-recovery-dr-for-sap-netweaver-running-on-azure-virtual-machines"></a>Azure 虛擬機器上執行之 SAP NetWeaver 的高可用性 (HA) 和災害復原 (DR)

### <a name="definition-of-terminologies"></a>術語定義

**高可用性 (HA)** 一詞通常與一組技術相關，該組技術可透過**相同**資料中心內受備援、容錯或容錯移轉保護的元件，提供 IT 服務的商務持續性，藉此將 IT 中斷的情況降到最低。 在本例中，會是在一個 Azure 區域內。

**災害復原 (DR)** 的目標也是將 IT 服務中斷的情況降到最低，但其復原通常會橫跨距離數百公里遠的**不同**資料中心。 在本例中，通常是相同地緣政治區域內的不同 Azure 區域之間，或由身為客戶的您所建立。

### <a name="overview-of-high-availability"></a>高可用性概觀

Azure 中之 SAP 高可用性的相關討論可分為兩個部分：

* **Azure 基礎結構高可用性** (例如計算 (VM)、網路、儲存體等的 HA)，以及它在增加 SAP 應用程式可用性方面的優點。
* **SAP 應用程式高可用性** (例如 SAP 軟體元件的 HA)︰
  * SAP 應用程式伺服器
  * SAP ASCS/SCS 執行個體
  * DB 伺服器

以及如何與 Azure 基礎結構 HA 結合。

Azure 中的 SAP 高可用性與內部部署實體或虛擬環境中的 SAP 高可用性相較下，有一些差異。 下列來自 SAP 的文件說明 Windows 虛擬環境中的標準 SAP 高可用性設定︰<https://scn.sap.com/docs/DOC-44415> \(英文\)。 不同於 Windows，Linux 沒有整合 sapinst 的 SAP-HA 組態。 如需有關 Linux 之 SAP HA 內部部署的詳細資訊，請參閱︰<https://scn.sap.com/docs/DOC-8541> \(英文\)。

### <a name="azure-infrastructure-high-availability"></a>Azure 基礎結構高可用性

目前 99.9% 都是單一 VM SLA。 若要了解單一 VM 可用性可能有的樣貌，您可以建置不同可用 Azure SLA 的乘積：<https://azure.microsoft.com/support/legal/sla/>。

計算基礎是每個月 30 天 (或 43200 分鐘)。 因此，0.05% 停機時間會對應至 21.6 分鐘。 像往常一樣，不同服務的可用性會以下列方式相乘︰

(可用性服務 #1/100) * (可用性服務 #2/100) * (可用性服務 #3/100) 

例如：

(99.95/100) * (99.9/100) * (99.9/100) = 0.9975 或整體可用性 99.75%。

#### <a name="virtual-machine-vm-high-availability"></a>虛擬機器 (VM) 高可用性

有兩種可以影響您虛擬機器可用性的 Azure 平台事件：計劃性維護和非計劃性維護。

* 規劃的維護事件 是由 Microsoft 對基礎 Azure 平台進行的定期更新，為虛擬機器在其中執行的平台基礎結構改善整體可靠性、效能和安全性。
* 未規劃的維護事件會在虛擬機器中的硬體或實體基礎結構產生某些方面的錯誤時發生。 這可能包含本機網路錯誤、本機磁碟錯誤，或其他機架層級的錯誤。 Azure 平台會在偵測到此類錯誤時，自動從裝載虛擬機器且狀況不良的實體伺服器，將虛擬機器移轉至狀況良好的實體伺服器。 這類事件非常稀少，但可能會導致虛擬機器重新啟動。

如需更多詳細資料，請參閱這份文件：<https://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability>

#### <a name="azure-storage-redundancy"></a>Azure 儲存體備援

Microsoft Azure 儲存體帳戶中的資料一律會進行複寫以確保持久性及高可用性，即使在面對暫時性的硬體故障時，仍可滿足 Azure 儲存體 SLA。

由於 Azure 儲存體預設會保留三個資料映像，因此不需要跨多個 Azure 磁碟的 RAID5 或 RAID1。

如需更多詳細資料，請參閱這篇文章：<https://azure.microsoft.com/documentation/articles/storage-redundancy/>

#### <a name="utilizing-azure-infrastructure-vm-restart-to-achieve-higher-availability-of-sap-applications"></a>使用 Azure 基礎結構 VM 重新啟動達到 SAP 應用程式的更高可用性

如果您決定不使用 Windows Server 容錯移轉叢集 (WSFC) 或 Linux 上的 Pacemaker (目前僅支援 SLES 12 及更新版本)，則會使用 Azure VM 重新啟動來保護 SAP 系統，以防止 Azure 實體伺服器基礎結構和整體基礎 Azure 平台發生規劃和未規劃的停機。

> [!NOTE]
> 請注意，Azure VM 重新啟動主要是保護 VM，而不是應用程式。 VM 重新啟動並未提供 SAP 應用程式的高可用性，而是提供特定基礎結構層級的可用性，因而間接達到 SAP 系統的更高可用性。 此外，在規劃或未規劃的主機中斷之後重新啟動 VM 所需的時間，也沒有 SLA。 因此，此高可用性方法不適用於 SAP 系統的重要元件，例如 (A)SCS 或 DBMS。
>
>

高可用性的另一個重要基礎結構項目是儲存體。 例如，Azure 儲存體 SLA 可用性為 99.9%。 如果使用者將所有 VM 及其磁碟部署到單一 Azure 儲存體帳戶，當 Azure 儲存體可能無法使用時，將會導致 Azure 儲存體帳戶中的所有 VM，以及這些 VM 內執行的所有 SAP 元件都無法使用。  

您也可以針對每個 VM 使用專用儲存體帳戶，而不是將所有 VM 放入單一 Azure 儲存體帳戶；如此一來，您就可以藉由使用多個獨立的 Azure 儲存體帳戶，來增加整體 VM 和 SAP 應用程式可用性。

Azure 受控磁片會自動放在其所連接之虛擬機器的容錯網域中。 如果您將兩部虛擬機器放在可用性設定組中並使用受控磁碟，平台也會負責將受控磁碟散發到不同容錯網域。 如果您打算使用進階儲存體，強烈建議也要使用受控磁碟。

使用 Azure 基礎結構 HA 和儲存體帳戶之 SAP NetWeaver 系統的範例架構如下所示︰

![使用 Azure 基礎結構 HA 達到 SAP 應用程式的更高可用性][planning-guide-figure-2900]

使用 Azure 基礎結構 HA 和受控磁碟之 SAP NetWeaver 系統的範例架構如下所示︰

![使用 Azure 基礎結構 HA 達到 SAP 應用程式的更高可用性][planning-guide-figure-2901]

針對重要的 SAP 元件，我們目前為止已達到下列目標：

* SAP 應用程式伺服器 (AS) 的高可用性

  SAP 應用程式伺服器執行個體是備援元件。 每個 SAP AS 執行個體都是部署在自己的 VM 上，而此 VM 是在不同的 Azure「容錯網域」及「升級網域」中執行 (請參閱[容錯網域][planning-guide-3.2.1]和[升級網域][planning-guide-3.2.2]章節)。 這是藉由使用「Azure 可用性設定組」來加以確保 (請參閱 [Azure 可用性設定組][planning-guide-3.2.3]一章)。 當 Azure 容錯或升級網域可能因規劃或未規劃而無法使用時，將會導致有限數目的 VM 及其 SAP AS 執行個體無法使用。

  每個 SAP 執行個體都會在自己的 Azure 儲存體帳戶中 - 當一個 Azure 儲存體可能無法使用時，只會導致一個 VM 及其 SAP AS 執行個體無法使用。 不過請注意，一個 Azure 訂用帳戶中的「Azure 儲存體帳戶」數目有限。 為了確保在 VM 重新啟動後會自動啟動 (A)SCS 執行個體，請務必在[對 SAP 執行個體使用自動啟動][planning-guide-11.5]一章所述的 (A)SCS 執行個體啟動設定檔中，設定 Autostart 參數。
  如需詳細資訊，請參閱[SAP 應用程式伺服器的高可用性][planning-guide-11.4.1]一章。

  即使您使用受控磁碟，這些磁碟也會儲存在 Azure 儲存體帳戶，而且在儲存體發生中斷時會無法使用。

*  SAP (A)SCS 執行個體可用性

  我們在此處使用 Azure VM 重新啟動，來保護已安裝 SAP (A)SCS 執行個體的 VM。 如果 Azure 伺服器發生規劃或未規劃的停機，則會在另一個可用的伺服器上重新啟動 VM。 如先前所述，Azure VM 重新開機主要是保護 Vm，而不是應用程式，在此案例中是（A） SCS 實例。 透過 VM 重新啟動，我們可間接達到 SAP (A)SCS 執行個體的更高可用性。 為了確保在 VM 重新啟動後會自動啟動 (A)SCS 執行個體，請務必在[對 SAP 執行個體使用自動啟動][planning-guide-11.5]一章所述的 (A)SCS 執行個體啟動設定檔中，設定 Autostart 參數。 這表示 (A)SCS 執行個體會當做單一 VM 上執行的單一失敗點 (SPOF)，以決定整個 SAP 環境是否可用。

*  DBMS 伺服器可用性

  類似於 SAP (A) SCS 執行個體使用案例，我們在此使用 Azure VM 重新啟動，來保護已安裝 DBMS 軟體的 VM，並透過 VM 重新啟動達到 DBMS 軟體的高可用性。
  在單一 VM 中執行的 DBMS 也是 SPOF，它會決定整個 SAP 環境是否可用。

### <a name="sap-application-high-availability-on-azure-iaas"></a>Azure IaaS 上的 SAP 應用程式高可用性

為了達到完整 SAP 系統高可用性，我們需要保護所有重要的 SAP 系統元件 (例如備援 SAP 應用程式伺服器)，以及 SAP (A)SCS 執行個體和 DBMS 等獨特的元件 (例如單一失敗點)。

#### <a name="high-availability-for-sap-application-servers"></a><a name="5d9d36f9-9058-435d-8367-5ad05f00de77"></a>SAP 應用程式伺服器的高可用性

針對 SAP 應用程式伺服器/對話方塊執行個體，您不必考慮特定高可用性解決方案。 高可用性可透過備援來達成，因此必須在不同虛擬機器中有足夠的備援。 它們應該會全部放相同的 Azure 可用性設定組中，以避免在規劃的維護停機時間內可能會同時更新 VM。 [升級網域][planning-guide-3.2.2]一章已介紹以「Azure 縮放單位」內不同「升級網域」和「容錯網域」為基礎的基本功能。 本文件的 [Azure 可用性設定組][planning-guide-3.2.3]一章則提供「Azure 可用性設定組」的說明。

Azure 縮放單位內的 Azure 可用性設定組可使用不限數目的容錯和升級網域。 這表示將一些 VM 放在一個可用性設定組中，遲早會有多個 VM 最後位於同一個容錯或升級網域中。

將一些 SAP 應用程式伺服器執行個體部署在其專用 VM 中，並假設我們有五個升級網域，最後會形成下圖。 可用性設定組內之容錯和更新網域的實際數目上限未來可能會有所變更︰

![Azure 中 SAP 應用程式伺服器的 HA][planning-guide-figure-3000]

如需更多詳細資料，請參閱這份文件：<https://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability>

#### <a name="high-availability-for-sap-central-services-on-azure"></a>Azure 上 SAP 中央服務的高可用性

若要了解 Azure 上 SAP 中央服務的高可用性架構，請參閱 [SAP NetWeaver 的高可用性架構和案例](./sap-high-availability-architecture-scenarios.md)一文作為項目資訊。 本文會指向特定作業系統的更詳細描述。

#### <a name="high-availability-for-the-sap-database-instance"></a>SAP 資料庫執行個體的高可用性

一般 SAP DBMS HA 設定會根據兩個 DBMS VM 進行，其中 DBMS 高可用性功能可用來將資料從使用中 DBMS 執行個體複寫到第二個 VM 的被動 DBMS 執行個體。

[DBMS 部署指南][dbms-guide]說明了一般 DBMS 及特定 DBMS 的高可用性和災害復原功能。

#### <a name="end-to-end-high-availability-for-the-complete-sap-system"></a>完整 SAP 系統的端對端高可用性

以下是 Azure 中完整 SAP NetWeaver HA 架構的兩個範例 - 一個用於 Windows，一個用於 Linux。

僅限非受控磁碟：當您部署許多 SAP 系統，而且所部署的 VM 數目將超過每個訂用帳戶的「儲存體帳戶」數目上限時，可能必須對以下所述的概念稍作取捨。 在此情況下，VM 的 VHD 必須結合到一個儲存體帳戶。 您通常會結合不同 SAP 系統之 SAP 應用程式層 VM 的 VHD 來達成目的。  我們也會將不同 SAP 系統之不同 DBMS VM 的不同 VHD 結合到一個 Azure 儲存體帳戶。 因此請記住 Azure 儲存體帳戶的 IOPS 限制 (<https://azure.microsoft.com/documentation/articles/storage-scalability-targets>)


##### <a name="windowslogo_windows-ha-on-windows"></a>![Windows][Logo_Windows] Windows 上的 HA

![Azure IaaS SQL Server 的 SAP NetWeaver 應用程式 HA 架構][planning-guide-figure-3200]

下列 Azure 建構適用於 SAP NetWeaver 系統，可將基礎結構問題和主機修補的影響降到最低︰

* 完整的系統會部署在 Azure 上（必要-DBMS 層、（A） SCS 實例），而且完整的應用層必須在相同的位置中執行）。
* 整個系統在一個 Azure 訂用帳戶內執行 (必要)。
* 整個系統在一個「Azure 虛擬網路」內執行 (必要)。
* 您可以將一個 SAP 系統的多個 VM 分成三個可用性設定組，即使所有 VM 都屬於相同的虛擬網路亦然。
* 每一層 (例如 DBMS、ASCS、應用程式伺服器) 都必須使用專用的可用性設定組。
* 執行一個 SAP 系統之 DBMS 執行個體的所有 VM 都會在一個可用性設定組中。 假設有多個 VM 針對每個系統執行 DBMS 執行個體，因為使用了原生 DBMS 高可用性功能，例如 SQL Server AlwaysOn 或 Oracle Data Guard。
* 所有執行 DBMS 執行個體的 VM 都會使用自己的儲存體帳戶。 DBMS 資料和記錄檔會使用同步處理資料的 DBMS 高可用性功能，從一個儲存體帳戶複寫到另一個儲存體帳戶。 無法使用一個儲存體帳戶會導致無法使用一個 SQL Windows 叢集節點，而不是整個 SQL Server 服務。
* 執行一個 SAP 系統之 (A)SCS 執行個體的所有 VM 都會在一個可用性設定組中。 在這些 VM 中，設定 Windows Sever 容錯移轉叢集 (WSFC) 來保護 (A)SCS 執行個體。
* 所有執行 (A)SCS 執行個體的 VM 都會使用自己的儲存體帳戶。 (A)SCS 執行個體檔案和 SAP 通用資料夾會使用 SIOS DataKeeper 複寫，從一個儲存體帳戶複寫到另一個儲存體帳戶。 無法使用一個儲存體帳戶會導致無法使用一個 (A)SCS Windows 叢集節點，而不是整個 (A)SCS 服務。
* 所有代表 SAP 應用程式伺服器層的 VM 都會在第三個可用性設定組中。
* 所有執行 SAP 應用程式伺服器的 VM 都會使用自己的儲存體帳戶。 無法使用一個儲存體帳戶會導致無法使用一部 SAP 應用程式伺服器，而其他 SAP 應用程式伺服器則可繼續執行。

下圖說明使用受控磁碟的相同環境。

![Azure IaaS SQL Server 的 SAP NetWeaver 應用程式 HA 架構][planning-guide-figure-3201]

##### <a name="linuxlogo_linux-ha-on-linux"></a>![Linux][Logo_Linux] Linux 上的 HA

Azure 上 Linux 的 SAP HA 架構基本上與上述 Windows 相同。 如需支援的高可用性解決方案清單，請參閱 SAP 附註 [1928533]。

### <a name="using-autostart-for-sap-instances"></a><a name="4e165b58-74ca-474f-a7f4-5e695a93204f"></a>對 SAP 執行個體使用自動啟動

SAP 提供可在啟動 VM 內的 OS 之後立即啟動 SAP 執行個體的功能。 確切步驟記載於 SAP 知識庫文章 [1909114]。 不過，不再建議 SAP 使用此設定，因為無法控制執行個體重新啟動的順序，並假設多個 VM 已受到影響，或每個 VM 已執行多個執行個體。 假設這是一個 VM 中有一個 SAP 應用程式伺服器執行個體的典型 Azure 案例，而且這是最終會重新啟動單一 VM 的案例，因此自動啟動不是很重要，而且可以藉由新增此參數來啟用：

`Autostart = 1`

執行 SAP ABAP 及 (或) Java 執行個體的啟動設定檔。

> [!NOTE]
> Autostart 參數可能還有一些缺點。 詳細來說，此參數會在啟動執行個體的相關 Windows/Linux 服務時，觸發 SAP ABAP 或 Java 執行個體的啟動。 當作業系統啟動時，便是這種情況。 不過，SAP 軟體生命週期管理功能 (例如加總或其他更新或升級) 也經常需要重新啟動 SAP 服務。 這些功能完全不會要求必須自動重新啟動執行個體。 因此，執行這類工作之前，應該停用 Autostart 參數。 叢集化的 SAP 執行個體 (例如 ASCS/SCS/CI) 也不應該使用 Autostart 參數。
>
>

如需自動啟動 SAP 執行個體的其他資訊，請參閱︰

* [Start/Stop SAP along with your Unix Server Start/Stop (隨著 Unix 伺服器啟動/停止一起啟動/停止 SAP)](https://scn.sap.com/community/unix/blog/2012/08/07/startstop-sap-along-with-your-unix-server-startstop)
* [Starting and Stopping SAP NetWeaver Management Agents (啟動及停止 SAP NetWeaver 管理代理程式)](https://help.sap.com/saphelp_nwpi711/helpdata/en/49/9a15525b20423ee10000000a421938/content.htm)
* [How to enable auto Start of HANA Database (如何啟用 HANA 資料庫的自動啟動)](http://sapbasisinfo.com/blog/2016/08/15/enabling-autostart-of-sap-hana-database-on-server-boot-situation/)

### <a name="larger-3-tier-sap-systems"></a>更大型的 3 層 SAP 系統
稍早的章節中已談到 3 層 SAP 組態的高可用性觀點。 但如果 DBMS 伺服器需求太大而無法位於 Azure，但可將 SAP 應用程式層部署到 Azure 的系統又該怎麼辦？

#### <a name="location-of-3-tier-sap-configurations"></a>3 層 SAP 組態的位置
不支援在內部部署與 Azure 之間分割應用程式層本身，或分割應用程式和 DBMS 層。 SAP 系統可以完全在內部部署或在 Azure 中部署。 也不支援在內部部署執行一些應用程式伺服器，並在 Azure 中執行其他一些應用程式伺服器。 以此為起點繼續往下討論。 我們也不支援將 SAP 系統的 DBMS 元件和 SAP 應用程式伺服器層部署在兩個不同的 Azure 區域。 例如，DBMS 在美國西部，而 SAP 應用程式層在美國中部。 不支援這類組態是因為 SAP NetWeaver 架構的延遲敏感度。

不過，在去年一整年，資料中心合作夥伴已開發 Azure 區域的共置。 這些共置通常很接近 Azure 區域內的實體 Azure 資料中心。 透過 ExpressRoute 在共同位置將資產的短距離和連線連線到 Azure，可能會導致低於2毫秒的延遲。 在此情況下，您可以將 DBMS 層 (包括 SAN/NAS 存放裝置) 放在這類共置中，並將 SAP 應用程式層放在 Azure 中。 [HANA 大型執行個體](./hana-overview-architecture.md)。 

### <a name="offline-backup-of-sap-systems"></a>SAP 系統的離線備份
根據所選擇的 SAP 組態 (2 層或 3 層)，可能需要進行備份。 您必須備份 VM 本身的內容及資料庫。 DBMS 相關的備份必須使用資料庫方法來進行。 如需不同資料庫的詳細說明，請參閱 [DBMS 指南][dbms-guide]。 另一方面，SAP 資料可如本節所述進行離線備份 (同時包含資料庫內容)，或是如下一節所述進行線上備份。

離線備份基本上需要透過 Azure 入口網站關閉 VM，並將基底 VM 磁碟及所有連接的磁碟複製到 VM。 這會保留 VM 及其相關聯磁碟的某個時間點映像。 建議將備份複製到不同的 Azure 儲存體帳戶。 因此，本文件的[在 Azure 儲存體帳戶之間複製磁碟][planning-guide-5.4.2]一章所述的程序會適用。
除了使用 Azure 入口網站進行關機之外，也可以透過 PowerShell 或 CLI 來執行此動作，如下列網頁所述：<https://azure.microsoft.com/documentation/articles/virtual-machines-deploy-rmtemplates-powershell/>

還原該狀態需要刪除基底 VM 及基底 VM 的原始磁碟和已掛接的磁碟、將儲存的磁碟複製回原始儲存體帳戶，或受控磁碟的資源群組，然後重新部署此系統。
這篇文章示範如何使用 PowerShell 來編寫此程序的指令碼︰<http://www.westerndevs.com/azure-snapshots/> \(英文\)

請務必安裝新的 SAP 授權，因為如上所述還原 VM 備份會建立新的硬體金鑰。

### <a name="online-backup-of-an-sap-system"></a>SAP 系統的線上備份

DBMS 的備份是使用 [DBMS 指南][dbms-guide]中所述的 DBMS 特定方法來執行。

SAP 系統內的其他 VM 可以使用 Azure 虛擬機器備份功能進行備份。 Azure 虛擬機器備份是在 Azure 中備份完整 VM 的標準方法。 Azure 備份將備份儲存在 Azure 中，並允許再次還原 VM。

> [!NOTE]
> 截至 2015 年 12 月為止，使用 VM 備份並不會保留用於 SAP 授權的唯一 VM ID。 這表示從 VM 備份還原需要安裝新的 SAP 授權金鑰，因為還原的 VM 會視為新的 VM，而不會取代之前儲存的舊版。
>
> ![Windows][Logo_Windows] Windows
>
> 理論上，如果 DBMS 系統以例如和 SQL Server 相同的方式支援 Windows VSS (磁碟區陰影複製服務 <https://msdn.microsoft.com/library/windows/desktop/bb968832(v=vs.85).aspx> \(英文\))，則也可以使用一致的方式來備份執行資料庫的 VM。
> 不過請注意，您無法根據 Azure VM 備份還原時間點來還原資料庫。 因此，建議使用 DBMS 功能執行資料庫的備份，而不是依賴 Azure VM 備份。
>
> 若要熟悉 Azure 虛擬機器備份，請從這裡開始：<https://docs.microsoft.com/azure/backup/backup-azure-vms>。
>
> 您也可以混合使用安裝在 Azure VM 中的 Microsoft Data Protection Manager 及 Azure 備份，來備份/還原資料庫。 如需詳細資訊，請參閱：<https://docs.microsoft.com/azure/backup/backup-azure-dpm-introduction>。  
>
> ![Linux][Logo_Linux] Linux
>
> Windows VSS 在 Linux 中沒有對等用法。 因此，您只能進行檔案一致備份，而無法進行應用程式一致備份。 SAP DBMS 備份應該使用 DBMS 功能來進行。 包括 SAP 相關資料的檔案系統，可以透過如下列網頁所述的方式，使用 tar 來進行儲存：<https://help.sap.com/saphelp_nw70ehp2/helpdata/en/d3/c0da3ccbb04d35b186041ba6ac301f/content.htm> \(英文\)
>
>

### <a name="azure-as-dr-site-for-production-sap-landscapes"></a>Azure 作為 SAP 生產環境的 DR 網站

從 2014 年中開始，以 Hyper-V、System Center 和 Azure 為主的各種元件延伸模組，可使用 Azure 作為在內部部署執行並以 Hyper-V 為基礎的 VM DR 站台。

如需詳細說明如何部署此解決方案的部落格，請參閱：<https://blogs.msdn.com/b/saponsqlserver/archive/2014/11/19/protecting-sap-solutions-with-azure-site-recovery.aspx> \(英文\)。

## <a name="summary"></a>摘要

Azure 中 SAP 系統的高可用性重點如下：

* 目前，您無法以與內部部署完全相同的方式來保護 SAP 單一失敗點。 這是因為在 Azure 中還無法在未使用協力廠商軟體的情況下建置共用磁碟叢集。
* 針對 DBMS 層，您需要使用不依賴共用磁片叢集技術的 DBMS 功能。 詳細資料記載於 [DBMS 指南][dbms-guide]。
* 若要將 Azure 基礎結構或主機維護中之容錯網域問題的影響降到最低，您應該使用 Azure 可用性設定組︰
  * 建議針對 SAP 應用程式層使用一個可用性設定組。
  * 建議針對 SAP DBMS 層使用另一個可用性設定組。
  * 不建議將 VM 的相同可用性設定組套用至不同的 SAP 系統。
  * 建議使用進階受控磁碟。
* 如需了解 SAP DBMS 層的備份用途，請參閱 [DBMS 指南][dbms-guide]。
* 備份 SAP 對話方塊執行個體沒有太大幫助，因為重新部署簡單的對話方塊執行個體通常更快。
* 備份含有 SAP 系統通用目錄的 VM 及不同執行個體的所有設定檔則很有幫助，而且應該透過 Windows 備份或 Linux 上的 tar 執行。 由於 Windows Server 2008 (R2) 和 Windows Server 2012 (R2) 之間有差異，因此使用更新版的 Windows Server 可讓您更輕鬆地進行備份，建議執行 Windows Server 2012 (R2) 作為 Windows 客體作業系統。

## <a name="next-steps"></a>後續步驟
閱讀下列文章：

- [適用於 SAP NetWeaver 的 Azure 虛擬機器部署](./deployment-guide.md)
- [適用於 SAP 工作負載的 Azure 虛擬機器 DBMS 部署考量](./dbms_guide_general.md)
- [SAP HANA 在 Azure 上的基礎結構組態和作業](/- azure/virtual-machines/workloads/sap/hana-vm-operations)
