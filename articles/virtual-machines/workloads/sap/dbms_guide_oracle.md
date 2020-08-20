---
title: 適用於 SAP 工作負載的 Oracle Azure 虛擬機器 DBMS 部署 | Microsoft Docs
description: 適用於 SAP 工作負載的 Oracle Azure 虛擬機器 DBMS 部署
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/14/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 75f8a1f42c5d5702fdb4a588b0829636c30f80a6
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2020
ms.locfileid: "88654611"
---
# <a name="azure-virtual-machines-dbms-deployment-for-sap-workload"></a>適用於 SAP 工作負載的 Azure 虛擬機器 DBMS 部署

[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1114181]:https://launchpad.support.sap.com/#/notes/1114181
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
[2171857]:https://launchpad.support.sap.com/#/notes/2171857
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
[dbms-guide-managed-disks]:dbms-guide.md#f42c6cb5-d563-484d-9667-b07ae51bce29

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
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]:../../disks-types.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/scalability-targets-standard-accounts.md
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
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:../../../azure-sql/virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md
[virtual-machines-sql-server-infrastructure-services]:../../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md
[virtual-machines-sql-server-performance-best-practices]:../../../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md
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


本文件介紹在 Azure IaaS 中部署適用於 SAP 工作負載的 Oracle Database 時，幾個需要考量的地方。 在您閱讀本文件之前，建議您先參閱[適用於 SAP 工作負載的 Azure 虛擬機器 DBMS 部署考量](dbms_guide_general.md)。 我們也建議您參閱 [Azure 上的 SAP 工作負載文件](./get-started.md)中的其他指南。 

您可以在 SAP 附註 [2039619] 中找到支援在 Azure 的 Oracle 上執行 SAP 的 Oracle 版本和對應的 OS 版本。

有關在 Oracle 上執行 SAP 商務套件的一般資訊，可以在 [Oracle 上的 SAP](https://www.sap.com/community/topic/oracle.html) \(英文\) 中找到。
Oracle 支援 Oracle 軟體在 Microsoft Azure 上執行。 如需 Windows Hyper-V 和 Azure 之一般支援的詳細資訊，請查看 [Oracle 和 Microsoft Azure 常見問題集](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html) \(英文\)。 

## <a name="sap-notes-relevant-for-oracle-sap-and-azure"></a>與 Oracle、SAP 和 Azure 相關的 SAP 附註 

以下是與 Azure 上的 SAP 有關的 SAP 附註。

| 附註編號 | Title |
| --- | --- |
| [1928533] |Azure 上的 SAP 應用程式：支援的產品和 Azure VM 類型 |
| [2015553] |Microsoft Azure 上的 SAP：支援的必要條件 |
| [1999351] |對適用於 SAP 的增強型 Azure 監視功能進行疑難排解 |
| [2178632] |Microsoft Azure 上適用於 SAP 的主要監視度量 |
| [2191498] |Linux 上搭配 Azure 的 SAP：增強型監視 |
| [2039619] |Microsoft Azure 上使用 Oracle Database 的 SAP 應用程式︰支援的產品和版本 |
| [2243692] |Microsoft Azure (IaaS) VM 上的 Linux：SAP 授權問題 |
| [2069760] |Oracle Linux 7.x SAP 安裝和升級 |
| [1597355] |適用於 Linux 的交換空間建議 |
| [2171857] |Oracle Database 12c - Linux 上的檔案系統支援 |
| [1114181] |Oracle Database 11g - Linux 上的檔案系統支援 |

Oracle 及 Azure 上的 SAP 所支援的確切設定和功能，已記載於 SAP 附註 [#2039619](https://launchpad.support.sap.com/#/notes/2039619) \(英文\) 之中。

Windows 和 Oracle Linux 是 Oracle 和 Azure 上的 SAP 唯一支援的作業系統。 廣泛使用的 SLES 和 RHEL Linux 散發套件並無法在 Azure 中部署 Oracle 元件。 Oracle 元件包含 Oracle Database 用戶端，SAP 應用程式會使用此用戶端來針對 Oracle DBMS 進行連線。 

根據 SAP 附註 [#2039619](https://launchpad.support.sap.com/#/notes/2039619) \(英文\)，例外狀況為不使用 Oracle Database 用戶端的 SAP 元件。 這些 SAP 元件是 SAP 的獨立加入佇列、訊息伺服器、加入佇列複寫服務、WebDispatcher，以及 SAP 閘道。  

就算您是在 Oracle Linux 上執行 Oracle DBMS 和 SAP 應用程式執行個體，您仍可以在 SLES 或 RHEL 上執行 SAP 中央服務，並使用以 Pacemaker 為基礎的叢集來保護它。 Oracle Linux 上並不支援作為高可用性架構的 Pacemaker。

## <a name="specifics-for-oracle-database-on-windows"></a>Windows 上 Oracle Database 專屬的詳細資料

### <a name="oracle-configuration-guidelines-for-sap-installations-in-azure-vms-on-windows"></a>在 Windows 上的 Azure VM 中安裝 SAP 的 Oracle 設定指導方針

根據 SAP 安裝手冊，與 Oracle 相關的檔案都不應該安裝或置於 VM 之 OS 磁碟 (C: 磁碟) 的系統驅動程式中。 不同大小的虛擬機器所能支援的附加磁碟數目皆不相同。 較小的虛擬機器類型只能支援較少數目的附加磁碟。 

如果您擁有較小的 VM，我們建議將 Oracle home、stage、"saptrace"、"saparch"、"sapbackup"、"sapcheck"，或 "sapreorg" 安裝/置於 OS 磁碟中。 這些部分的 Oracle DBMS 元件並不會密集地使用 I/O 和 I/O 輸送量。 這代表 OS 磁碟可以處理它們的 I/O 需求。 OS 磁碟的預設大小為 127 GB。 

如果可用空間不足，則可以將該磁碟的大小[調整](../../windows/expand-os-disk.md)為 2048 GB。 Oracle Database 和重做記錄檔則必須儲存在個別的資料磁碟上。 例外狀況為 Oracle 暫存資料表空間。 Tempfiles 可以在 D:/ (非永續性磁碟) 上建立。 非持續性 D:\ 磁碟機也會提供更好的 I/O 延遲和輸送量 (但 A 系列 VM 除外)。 

若要判斷 Tempfiles 空間的正確容量，您可以檢查現有系統上的 Tempfiles 大小。

### <a name="storage-configuration"></a>儲存體組態
只支援使用 NTFS 格式化磁碟的單一執行個體 Oracle。 所有的資料庫檔案都必須儲存在受控磁碟 (建議) 或 VHD 上的 NTFS 檔案系統上。 這些磁碟會掛接到 Azure VM，並且以 [Azure 分頁 Blob 儲存體](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) \(英文\) 或 [Azure 受控磁碟](../../managed-disks-overview.md)為基礎。 

我們強烈建議使用 [Azure 受控磁碟](../../managed-disks-overview.md)。 我們也強烈建議針對 Oracle Database 部署使用[進階 SSD](../../disks-types.md)。

Oracle Database 檔案不支援網路磁碟機或遠端共用 (例如 Azure 檔案服務)。 如需詳細資訊，請參閱

- [Microsoft Azure 檔案服務簡介](/archive/blogs/windowsazurestorage/introducing-microsoft-azure-file-service)

- [保留與 Microsoft Azure 檔案的連線](/archive/blogs/windowsazurestorage/persisting-connections-to-microsoft-azure-files)


如果您是使用以 Azure 分頁 Blob 儲存體或受控磁碟為基礎的磁碟，在[適用於 SAP 工作負載的 Azure 虛擬機器 DBMS 部署考量](dbms_guide_general.md)中所述的內容同樣適用於搭配 Oracle Database 所做的部署。

Azure 磁碟具有 IOPS 輸送量上的配額。 此概念已詳述於[適用於 SAP 工作負載的 Azure 虛擬機器 DBMS 部署考量](dbms_guide_general.md)。 確切的配額會根據您使用的 VM 類型而定。 您可在 [Azure 中 Windows 虛擬機器的大小][virtual-machines-sizes-windows]中找到 VM 類型清單及其個別的配額。

若要識別支援的 Azure VM 類型，請參閱 SAP 附註 [1928533]。

最小設定如下： 

| 元件 | 磁碟 | Caching | 儲存體集區 |
| --- | ---| --- | --- |
| \oracle\<SID>\origlogaA & mirrlogB | Premium | None | 不需要 |
| \oracle\<SID>\origlogaB & mirrlogA | Premium | None | 不需要 |
| \oracle\<SID>\sapdata1...n | Premium | 唯讀 | 可以使用 |
| \oracle\<SID>\oraarch | 標準 | None | 不需要 |
| Oracle Home、saptrace... | 作業系統磁碟 | | 不需要 |


裝載線上重做記錄之磁碟的選取，應由 IOPs 需求來決定。 可以將所有 sapdata1...n (資料表空間) 儲存在單一已掛接磁碟上，前提是其大小、IOPS 及輸送量必須能滿足需求。 

效能設定如下：

| 元件 | 磁碟 | Caching | 儲存體集區 |
| --- | ---| --- | --- |
| \oracle\<SID>\origlogaA | Premium | None | 可以使用  |
| \oracle\<SID>\origlogaB | Premium | None | 可以使用 |
| \oracle\<SID>\mirrlogAB | Premium | None | 可以使用 |
| \oracle\<SID>\mirrlogBA | Premium | None | 可以使用 |
| \oracle\<SID>\sapdata1...n | Premium | 唯讀 | 建議  |
| \oracle\SID\sapdata(n+1)* | Premium | None | 可以使用 |
| \oracle\<SID>\oraarch* | Premium | None | 不需要 |
| Oracle Home、saptrace... | 作業系統磁碟 | 不需要 |

*(n+1)：裝載 SYSTEM、TEMP 及 UNDO 資料表空間。 System 和 Undo 資料表空間的 I/O 模式，與其他裝載應用程式資料的資料表空間不同。 對於 System 和 Undo 資料表空間的效能來說，無快取是最佳選項。

*oraarch：從效能的觀點來看，儲存體集區並非必要。 可以用它來換取更多空間。

如果需要更多 IOPS，建議使用 Windows 存放集區 (僅適用於 Windows Server 2012 和更新版本)，以在多個已掛接的磁碟上建立一個大型邏輯裝置。 這種方法可以簡化管理磁碟空間的系統管理負荷，並使您無需手動將檔案分散到多個掛接的磁碟。


#### <a name="write-accelerator"></a>寫入加速器
針對 Azure M 系列的 VM，和 Azure 進階儲存體效能相比，寫入線上重做記錄的延遲將會大幅降低。 請針對以 Azure 進階儲存體為基礎並用於線上重做記錄檔的磁碟 (VHD) 啟用 Azure 寫入加速器。 如需詳細資訊，請參閱[寫入加速器](../../how-to-enable-write-accelerator.md)。


### <a name="backuprestore"></a>備份/還原
針對備份/還原功能，適用於 Oracle 的 SAP BR*Tools 的支援方式，與其在標準 Windows Server 作業系統上的支援方式相同。 Oracle Recovery Manager (RMAN) 也支援備份至磁碟，以及從磁碟還原。

您也可以使用 Azure 備份來執行應用程式一致的 VM 備份。 [在 Azure 中規劃 VM 備份基礎結構](../../../backup/backup-azure-vms-introduction.md)一文說明 Azure 備份如何使用 Windows VSS 功能來執行應用程式一致的備份。 由 SAP 在 Azure 上所支援的 Oracle DBMS 版本，能夠利用 VSS 功能進行備份。 如需詳細資訊，請參閱 Oracle 文件[搭配 VSS 進行資料庫備份與復原的基本概念](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/ntqrf/basic-concepts-of-database-backup-and-recovery-with-vss.html#GUID-C085101B-237F-4773-A2BF-1C8FD040C701) \(英文\)。


### <a name="high-availability"></a>高可用性
基於高可用性和災害復原目的支援 Oracle Data Guard。 若要在 Data Guard 中達成自動容錯移轉，便必須使用 Fast-Start Failover (FSFA)。 觀察者 (FSFA) 會觸發容錯移轉。 如果您不使用 FSFA，則只能使用手動容錯移轉設定。

如需 Azure 中適用於 Oracle Database 之災害復原的相關資訊，請參閱[Azure 環境中 Oracle Database 12c 資料庫的災害復原](../oracle/oracle-disaster-recovery.md)。

### <a name="accelerated-networking"></a>加速網路
針對 Windows 上的 Oracle 部署，我們強烈建議使用 [Azure 加速網路](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) \(英文\) 中所述的加速網路功能。 另請考慮在[適用於 SAP 工作負載的 Azure 虛擬機器 DBMS 部署考量](dbms_guide_general.md)中所提出的建議。 
### <a name="other"></a>其他
[適用於 SAP 工作負載的 Azure 虛擬機器 DBMS 部署考量](dbms_guide_general.md)也說明其他與搭配 Oracle Database 的 VM 部署相關的重要概念，包括 Azure 可用性設定組和 SAP 監視。

## <a name="specifics-for-oracle-database-on-oracle-linux"></a>Oracle Linux 上 Oracle Database 專屬的詳細資料
Oracle 支援 Oracle 軟體在以 Oracle Linux 為客體 OS 的 Microsoft Azure 上執行。 如需 Windows Hyper-V 和 Azure 之一般支援的詳細資訊，請參閱 [Azure 和 Oracle 常見問題集](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html) \(英文\)。 

同時也支援運用 Oracle Database 之 SAP 應用程式的特定案例。 詳細資料將在本文件的下一個部分中討論。

### <a name="oracle-version-support"></a>Oracle 版本支援
如需支援在 Azure 虛擬機器的 Oracle 上執行 SAP 的 Oracle 版本和對應 OS 版本的相關資訊，請參閱 SAP 附註 [2039619]。

有關在 Oracle 上執行 SAP 商務套件的一般資訊，可以在 [Oracle 上的 SAP 社群頁面](https://www.sap.com/community/topic/oracle.html) \(英文\) 中找到。

### <a name="oracle-configuration-guidelines-for-sap-installations-in-azure-vms-on-linux"></a>在 Linux 上的 Azure VM 中安裝 SAP 的 Oracle 設定指導方針

根據 SAP 安裝手冊，與 Oracle 相關的檔案都不應該安裝或置於 VM 之開機磁碟的系統驅動程式中。 不同大小的虛擬機器所能支援的附加磁碟數目皆不相同。 較小的虛擬機器類型只能支援較少數目的附加磁碟。 

在此情況下，我們建議將 Oracle home、stage、saptrace、saparch、sapbackup、sapcheck，或 sapreorg 安裝/置於開機磁碟中。 這些部分的 Oracle DBMS 元件並不會密集地使用 I/O 和 I/O 輸送量。 這代表 OS 磁碟可以處理它們的 I/O 需求。 OS 磁碟的預設大小為 30 GB。 您可以使用 Azure 入口網站、PowerShell 或 CLI 來擴展開機磁碟。 擴展開機磁碟之後，您便可以針對 Oracle 二進位檔新增額外的磁碟分割。


### <a name="storage-configuration"></a>儲存體組態

針對 Azure 上的 Oracle Database 檔案，支援 ext4、xfs 或 Oracle ASM 檔案系統。 所有的資料庫檔案都必須儲存於以 VHD 或受控磁碟為基礎的這些檔案系統上。 這些磁碟會掛接到 Azure VM，並且以 [Azure 分頁 Blob 儲存體](<https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>) \(英文\) 或 [Azure 受控磁碟](../../managed-disks-overview.md)為基礎。

針對 Oracle Linux UEK 核心，UEK 必須至少為第 4 版，才能支援 [Azure 進階 SSD](../../windows/premium-storage-performance.md#disk-caching)。

我們非常建議使用 [Azure 受控磁碟](../../managed-disks-overview.md)。 我們也非常建議針對 Oracle Database 部署使用 [Azure 進階 SSD](../../disks-types.md)。

Oracle Database 檔案不支援網路磁碟機或遠端共用 (例如 Azure 檔案服務)。 如需詳細資訊，請參閱下列： 

- [Microsoft Azure 檔案服務簡介](/archive/blogs/windowsazurestorage/introducing-microsoft-azure-file-service)

- [保留與 Microsoft Azure 檔案的連線](/archive/blogs/windowsazurestorage/persisting-connections-to-microsoft-azure-files)

如果您是使用以 Azure 分頁 Blob 儲存體或受控磁碟為基礎的磁碟，在[適用於 SAP 工作負載的 Azure 虛擬機器 DBMS 部署考量](dbms_guide_general.md)中所述的內容同樣適用於搭配 Oracle Database 所做的部署。

 Azure 磁碟具有 IOPS 輸送量上的配額。 此概念已詳述於[適用於 SAP 工作負載的 Azure 虛擬機器 DBMS 部署考量](dbms_guide_general.md)。確切的配額會根據使用的 VM 類型而定。 如需 VM 類型及其配額的清單，請參閱 [Azure 中 Linux 虛擬機器的大小][virtual-machines-sizes-linux]。

若要識別支援的 Azure VM 類型，請參閱 SAP 附註 [1928533]。

最小設定：

| 元件 | 磁碟 | Caching | 移除* |
| --- | ---| --- | --- |
| /oracle/\<SID>/origlogaA & mirrlogB | Premium | None | 不需要 |
| /oracle/\<SID>/origlogaB & mirrlogA | Premium | None | 不需要 |
| /oracle/\<SID>/sapdata1...n | Premium | 唯讀 | 可以使用 |
| /oracle/\<SID>/oraarch | 標準 | None | 不需要 |
| Oracle Home、saptrace... | 作業系統磁碟 | | 不需要 |

*移除：使用 RAID0 的 LVM stripe 或 MDADM

裝載 Oracle 線上重做記錄之磁碟的選取，應由 IOPS 需求來決定。 可以將所有 sapdata1...n (資料表空間) 儲存在單一已掛接磁碟上，前提是其容量、IOPS 及輸送量必須能滿足需求。 

效能設定：

| 元件 | 磁碟 | Caching | 移除* |
| --- | ---| --- | --- |
| /oracle/\<SID>/origlogaA | Premium | None | 可以使用  |
| /oracle/\<SID>/origlogaB | Premium | None | 可以使用 |
| /oracle/\<SID>/mirrlogAB | Premium | None | 可以使用 |
| /oracle/\<SID>/mirrlogBA | Premium | None | 可以使用 |
| /oracle/\<SID>/sapdata1...n | Premium | 唯讀 | 建議  |
| /oracle/\<SID>/sapdata(n+1)* | Premium | None | 可以使用 |
| /oracle/\<SID>/oraarch* | Premium | None | 不需要 |
| Oracle Home、saptrace... | 作業系統磁碟 | 不需要 |

*移除：使用 RAID0 的 LVM stripe 或 MDADM

*(n+1)：裝載 SYSTEM、TEMP 及 UNDO 資料表空間：System 和 Undo 資料表空間的 I/O 模式，與其他裝載應用程式資料的資料表空間不同。 對於 System 和 Undo 資料表空間的效能來說，無快取是最佳選項。

*oraarch：從效能的觀點來看，儲存體集區並非必要。


如果需要更多 IOPS，建議使用 LVM (邏輯磁碟區管理員) 或 MDADM，以在多個已掛接的磁碟上建立一個大型邏輯裝置。 如需詳細資訊，請參閱[適用於 SAP 工作負載的 Azure 虛擬機器 DBMS 部署考量](dbms_guide_general.md)文件，以取得利用 LVM 或 MDADM 的指導方針和指示。 這種方法可以簡化管理磁碟空間的系統管理負荷，並使您無需手動將檔案分散到多個掛接的磁碟。


#### <a name="write-accelerator"></a>寫入加速器
針對 Azure M 系列的 VM，當您使用 Azure 寫入加速器時，和 Azure 進階儲存體效能相比，寫入線上重做記錄的延遲將會大幅降低。 請針對以 Azure 進階儲存體為基礎並用於線上重做記錄檔的磁碟 (VHD) 啟用 Azure 寫入加速器。 如需詳細資訊，請參閱[寫入加速器](../../how-to-enable-write-accelerator.md)。


### <a name="backuprestore"></a>備份/還原
針對備份/還原功能，適用於 Oracle 的 SAP BR*Tools 的支援方式，與其在裸機和 Hyper-V 上的支援方式相同。 Oracle Recovery Manager (RMAN) 也支援備份至磁碟，以及從磁碟還原。

如需如何使用 Azure 備份及復原服務來備份和復原 Oracle Database 的詳細資料，請參閱[在 Azure Linux 虛擬機器上備份及復原 Oracle Database 12c 資料庫](../oracle/oracle-backup-recovery.md)。

### <a name="high-availability"></a>高可用性
基於高可用性和災害復原目的支援 Oracle Data Guard。 若要在 Data Guard 中達成自動容錯移轉，便必須使用 Fast-Start Failover (FSFA)。 觀察者功能 (FSFA) 會觸發容錯移轉。 如果您不使用 FSFA，則只能使用手動容錯移轉設定。 如需詳細資訊，請參閱[在 Azure Linux 虛擬機器上實作 Oracle Data Guard](../oracle/configure-oracle-dataguard.md)。


Azure 中適用於 Oracle 資料庫的災害復原層面，已詳述於[Azure 環境中 Oracle Database 12c 資料庫的災害復原](../oracle/oracle-disaster-recovery.md)中。

### <a name="accelerated-networking"></a>加速網路
Oracle Linux 中針對 Azure 加速網路的支援，已於 Oracle Linux 7 Update 5 (Oracle Linux 7.5) 中推出。 如果您無法升級至最新的 Oracle Linux 7.5 版，可能的因應措施為使用 RedHat Compatible Kernel (RHCK)，而非使用 Oracle UEK 核心。 

根據 SAP 附註 [#1565179](https://launchpad.support.sap.com/#/notes/1565179) \(英文\)，支援在 Oracle Linux 內使用 RHEL 核心。 針對 Azure 加速網路，最小的 RHCKL 核心版本必須是 3.10.0-862.13.1.el7。 如果您是將 Oracle Linux 中的 UEK 核心搭配 [Azure 加速網路](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) \(英文\) 使用，您必須使用 Oracle UEK 核心第 5 版。

如果您是從不是以 Azure Marketplace 為基礎的映像部署 VM，則必須執行下列程式碼以將額外的設定檔複製到 VM： 
<pre><code># Copy settings from GitHub to the correct place in the VM
sudo curl -so /etc/udev/rules.d/68-azure-sriov-nm-unmanaged.rules https://raw.githubusercontent.com/LIS/lis-next/master/hv-rhel7.x/hv/tools/68-azure-sriov-nm-unmanaged.rules 
</code></pre>


### <a name="other"></a>其他
[適用於 SAP 工作負載的 Azure 虛擬機器 DBMS 部署考量](dbms_guide_general.md)也說明其他與搭配 Oracle Database 的 VM 部署相關的重要概念，包括 Azure 可用性設定組和 SAP 監視。