---
title: 適用于 SAP NetWeaver 的 Azure Vm HA 架構和案例 |Microsoft Docs
description: Azure 虛擬機器上 SAP NetWeaver 的高可用性架構和案例
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 887caaec-02ba-4711-bd4d-204a7d16b32b
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/26/2020
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 08f770ced6cb1ec1102159788e1583d481436b08
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78363618"
---
# <a name="high-availability-architecture-and-scenarios-for-sap-netweaver"></a>SAP NetWeaver 的高可用性架構和案例

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png


[sap-installation-guides]:http://service.sap.com/instguides

[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-ascs-ha-multi-sid-wsfc-file-share]:sap-ascs-ha-multi-sid-wsfc-file-share.md
[sap-ascs-ha-multi-sid-wsfc-shared-disk]:sap-ascs-ha-multi-sid-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md
[sap-suse-ascs-ha-anf]:high-availability-guide-suse-netapp-files.md
[sap-higher-availability]:sap-higher-availability-architecture-scenarios.md

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

[virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]:../../windows/sql/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md
[virtual-machines-windows-portal-sql-alwayson-int-listener]:../../windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md

[sap-ha-bc-virtual-env-hyperv-vmware-white-paper]:https://scn.sap.com/docs/DOC-44415
[sap-ha-partner-information]:https://scn.sap.com/docs/DOC-8541
[azure-sla]:https://azure.microsoft.com/support/legal/sla/
[azure-virtual-machines-manage-availability]:https://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability
[azure-storage-redundancy]:https://azure.microsoft.com/documentation/articles/storage-redundancy/
[azure-storage-managed-disks-overview]:https://docs.microsoft.com/azure/storage/storage-managed-disks-overview

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

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-ha-guide]:sap-high-availability-guide.md
[sap-ha-guide-2]:#42b8f600-7ba3-4606-b8a5-53c4f026da08
[sap-ha-guide-4]:#8ecf3ba0-67c0-4495-9c14-feec1a2255b7
[sap-ha-guide-8]:#78092dbe-165b-454c-92f5-4972bdbef9bf
[sap-ha-guide-8.1]:#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-ha-guide-8.9]:#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.11]:#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-ha-guide-8.12]:#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-ha-guide-8.12.1]:#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-ha-guide-8.12.3]:#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-ha-guide-8.12.3.1]:#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-ha-guide-8.12.3.2]:#dd41d5a2-8083-415b-9878-839652812102
[sap-ha-guide-8.12.3.3]:#d9c1fc8e-8710-4dff-bec2-1f535db7b006
[sap-ha-guide-9]:#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-ha-guide-9.1.1]:#a97ad604-9094-44fe-a364-f89cb39bf097

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (SAP 多重 SID 高可用性設定)


[sap-ha-guide-figure-1000]:./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:./media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png

[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md


## <a name="terminology-definitions"></a>詞彙定義

**高可用性**：指一組技術，該組技術可透過相同資料中心內受備援、容錯或容錯移轉保護的元件，提供 IT 服務的商務持續性，藉此將 IT 中斷的情況降到最低。 在我們的案例中，資料中心位於一個 Azure 區域內。

**災害復原**：也是指最小化 IT 服務中斷及其復原，但是橫跨彼此相距甚遠的各個資料中心。 在我們的案例中，資料中心可能位於相同地理政治區域或由身為客戶的您所建立之位置的各個 Azure 區域。


## <a name="overview-of-high-availability"></a>高可用性概觀
Azure 中的 SAP 高可用性可以分成三種類型：

* **Azure 基礎結構高可用性**： 

    例如，高可用性可以包含計算 (VM)、網路、儲存體，以及它在增加 SAP 應用程式可用性方面的優點。

* **使用 Azure 基礎結構 VM 重新啟動達到 SAP 應用程式的更高可用性**： 

    如果您決定不要在 Linux 上使用 Windows Server 容錯移轉叢集 (WSFC) 或 Pacemaker 等功能，則會利用 Azure VM 重新啟動。 它會針對規劃與未規劃的 Azure 實體伺服器基礎結構停機時間，以及整體基礎 Azure 平台，來保護 SAP 系統。

* **SAP 應用程式高可用性**： 

    若要達到完整 SAP 系統高可用性，您必須保護所有重要的 SAP 系統元件。 例如，
    * 備援 SAP 應用程式伺服器。
    * 唯一的元件。 範例可能是單一失敗點 (SPOF) 元件，例如 SAP ASCS/SCS 執行個體或資料庫管理系統 (DBMS)。

Azure 中的 SAP 高可用性與內部部署實體或虛擬環境中的 SAP 高可用性有所不同。 下列檔[SAP 在 Microsoft windows 上使用 VMware 和 hyper-v 的虛擬環境中 NetWeaver 高可用性和商務持續性][sap-ha-bc-virtual-env-hyperv-vmware-white-paper]說明 Windows 上虛擬化環境中的標準 SAP 高可用性設定。

不同於 Windows，Linux 沒有整合 sapinst 的 SAP 高可用性設定。 如需適用于 Linux 之 SAP 高可用性的詳細資訊，請參閱[高可用性夥伴資訊][sap-ha-partner-information]。

## <a name="azure-infrastructure-high-availability"></a>Azure 基礎結構高可用性

### <a name="sla-for-single-instance-virtual-machines"></a>單一執行個體虛擬機器的 SLA

目前 99.9% 的單一 VM SLA 都具有進階儲存體。 若要瞭解單一 VM 的可用性，您可以建立各種可用[Azure 服務等級協定][azure-sla]的產品。

計算基礎是每個月 30 天 (或 43,200 分鐘)。 例如，0.05% 停機時間會對應至 21.6 分鐘。 像往常一樣，不同服務的可用性會以下列方式計算︰

(可用性服務 #1/100) * (可用性服務 #2/100) * (可用性服務 #3/100) \*...

例如，

(99.95/100) * (99.9/100) * (99.9/100) = 0.9975 或整體可用性 99.75%。

### <a name="multiple-instances-of-virtual-machines-in-the-same-availability-set"></a>同一可用性設定組中虛擬機器的多個執行個體
對於將兩 (含) 個以上之執行個體部署到同一可用性設定組的所有虛擬機器，我們保證虛擬機器可與至少一個執行個體正常連線的時間，至少須達 99.95%。

當兩 (含) 個以上 VM 是同一可用性設定組的一部分時，基礎 Azure 平台會為可用性設定組中的每部虛擬機器指派一個更新網域和一個容錯網域。

* **更新網域**保證多個 VM 不會在 Azure 基礎結構進行計劃性維護期間同時重新開機。 一次只有一個 VM 重新開機。

* **容錯網域**保證會將 VM 部署在硬體元件上，而這些硬體元件不會共用一般電源和網路交換器。 當伺服器、網路交換器或電源發生未規劃停機時，只有一個 VM 會受到影響。

如需詳細資訊，請參閱[在 Azure 中管理 Windows 虛擬機器的可用性][azure-virtual-machines-manage-availability]。

可用性設定組用於達到下列項目的高可用性：

* 備援 SAP 應用程式伺服器。  
* 具有兩個以上節點 (例如 VM) 的叢集，會保護例如 SAP ASCS/SCS 執行個體或 DBMS 的 SPOF。


### <a name="azure-availability-zones"></a>Azure 可用性區域
Azure 正在不同的 [Azure 區域](https://docs.microsoft.com/azure/availability-zones/az-overview)到處推廣 [Azure 可用性區域](https://azure.microsoft.com/global-infrastructure/regions/)的概念。 有提供可用性區域的 Azure 區域會有多個資料中心，而這些資料中心與電力來源、冷卻及網路無關。 之所以在單一 Azure 區域 (region) 中提供不同區域 (zone)，是要讓您可跨兩個或三個所提供的可用性區域來部署應用程式。 假設電力來源和/或網路中的問題只會影響一個可用性區域基礎結構，那麼 Azure 區域內的應用程式部署仍然可完全正常運作。 由於可能會失去單一區域內的一些 VM，因此最後會造成部分產能降低。 但其他兩個區域中的 VM 仍舊會繼續執行。 [Azure 可用性區域](https://docs.microsoft.com/azure/availability-zones/az-overview)會列出提供可用性區域的 Azure 區域。

使用可用性區域時有一些考量事項。 考量清單如下：

- 您無法在可用性區域內部署 Azure 可用性設定組。 您必須選擇可用性區域或可用性設定組作為 VM 的部署框架。
- 您無法使用[基本 Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)，來建立以 Windows 容錯移轉叢集服務或 Linux Pacemaker 為基礎的容錯移轉叢集解決方案。 您必須改為使用 [Azure Standard Load Balancer SKU](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)
- Azure 可用性區域並未保證同一區域內的不同可用性區域之間會相隔一定距離
- 在不同 Azure 區域內的不同 Azure 可用性區域之間，網路延遲可能會隨著所在的 Azure 區域而有所不同。 有時候，因為某個可用性區域到作用中 DBMS VM 的網路延遲對商務程序的影響來說並不大，因此身為客戶的您可以合情合理地執行部署在不同區域的 SAP 應用程式層。 然而，也會有客戶的情況是某個可用性區域中的作用中 DBMS VM 與另一個可用性區域中 VM 內的 SAP 應用程式執行個體之間的延遲太久，而不適合 SAP 商務程序的運轉。 因此，如果延遲太久，部署架構就必須與應用程式的主動/主動架構不同，或與主動/被動架構不同。
- 必須使用 [Azure 受控磁碟](https://azure.microsoft.com/services/managed-disks/)才能部署至 Azure 可用性區域 


### <a name="planned-and-unplanned-maintenance-of-virtual-machines"></a>虛擬機器的規劃和未規劃維護

兩種類型的 Azure 平台事件會影響虛擬機器的可用性：

* **規劃的維護**事件是由 Microsoft 對基礎 Azure 平台執行的定期更新。 更新會改善您的虛擬機器在其上執行之平台基礎結構的整體可靠性、效能和安全性。

* **未規劃的維護**事件會在虛擬機器中的硬體或實體基礎結構產生某些方面的錯誤時發生。 這可能包含本機網路錯誤、本機磁碟錯誤，或其他機架層級的錯誤。 Azure 平台會在偵測到此類錯誤時，自動從裝載虛擬機器且狀況不良的實體伺服器，將虛擬機器移轉至狀況良好的實體伺服器。 這類事件非常稀少，但可能會導致虛擬機器重新啟動。

如需詳細資訊，請參閱[在 Azure 中管理 Windows 虛擬機器的可用性][azure-virtual-machines-manage-availability]。

### <a name="azure-storage-redundancy"></a>Azure 儲存體備援
儲存體帳戶中的資料一律會進行複寫以確保持久性及高可用性，即使在面對暫時性的硬體故障時，仍可滿足 Azure 儲存體 SLA。

因為 Azure 儲存體預設會保留資料的三個映像，所以不需要跨多個 Azure 磁碟使用 RAID 5 或 RAID 1。

如需詳細資訊，請參閱 [Azure 儲存體複寫][azure-storage-redundancy]。

### <a name="azure-managed-disks"></a>Azure 受控磁碟
在 Azure Resource Manager 中，建議使用的資源類型是受控磁碟，可非儲存在 Azure 儲存體帳戶中的虛擬硬碟 (VHD)。 受控磁碟會自動對齊其所連結虛擬機器的 Azure 可用性設定組。 它們會增加虛擬機器和在其上執行之服務的可用性。

如需詳細資訊，請參閱[Azure 受控磁碟總覽][azure-storage-managed-disks-overview]。

由於受控磁碟可以簡化虛擬機器的部署和管理，因此建議您使用受控磁碟。



## <a name="utilizing-azure-infrastructure-high-availability-to-achieve-higher-availability-of-sap-applications"></a>使用 Azure 基礎結構高可用性達到 SAP 應用程式的更高可用性

如果您決定不在 Linux 上使用如 WSFC 或 Pacemaker 等功能 (目前僅支援 SUSE Linux Enterprise Server [SLES] 12 和更新版本)，則會使用 Azure VM 重新啟動。 它會針對規劃與未規劃的 Azure 實體伺服器基礎結構停機時間，以及整體基礎 Azure 平台，來保護 SAP 系統。

如需此方法的詳細資訊，請參閱[利用 Azure 基礎結構 VM 重新開機來達到 SAP 系統的更高可用性][sap-higher-availability]。

## <a name="baed0eb3-c662-4405-b114-24c10a62954e"></a> Azure IaaS 上 SAP 應用程式的高可用性

若要達到完整 SAP 系統高可用性，您必須保護所有重要的 SAP 系統元件。 例如，
  * 備援 SAP 應用程式伺服器。
  * 唯一的元件。 範例可能是單一失敗點 (SPOF) 元件，例如 SAP ASCS/SCS 執行個體或資料庫管理系統 (DBMS)。

以下章節討論如何對所有三個重要 SAP 系統元件達到高可用性。

### <a name="high-availability-architecture-for-sap-application-servers"></a>SAP 應用程式伺服器的高可用性架構

> 本節適用於：
>
> ![Windows][Logo_Windows] Windows 和 ![Linux][Logo_Linux] Linux
>

對於 SAP 應用程式伺服器和對話方塊執行個體，您通常不需要特定的高可用性解決方案。 您可以透過備援來達成高可用性，而且您會在 Azure 虛擬機器之不同的執行個體中，設定多個對話方塊執行個體。 您應該至少要有兩個 SAP 應用程式執行個體安裝在 Azure 虛擬機器的兩個執行個體中。

![圖 1：高可用性 SAP 應用程式伺服器][sap-ha-guide-figure-2000]

_**圖 1：** 高可用性 SAP 應用程式伺服器_

所有裝載 SAP 應用程式伺服器執行個體的虛擬機器都必須放置在同一個 Azure 可用性設定組中。 Azure 可用性設定組可確保：

* 所有虛擬機器都是相同更新網域的一部分。  
    更新網域可確保虛擬機器不會在規劃的維護停機期間同時更新。

    「[更新網域][planning-guide-3.2.2]」一節中已引進基本功能（以 Azure 縮放單位內不同的「更新」和「容錯網域」為基礎）。

* 所有虛擬機器都是相同容錯網域的一部分。  
    容錯網域可確保部署虛擬機器，以便不會有任何單一失敗點影響所有虛擬機器的可用性。

Azure 縮放單位內的 Azure 可用性設定組可使用的更新和容錯網域數目。 如果您持續將 VM 新增至單一可用性設定組，兩個或多個 VM 最終會在相同容錯或更新網域中。

如果您將一些 SAP 應用程式伺服器執行個體部署在其專用 VM 中，並假設我們有五個更新網域，最後會形成下圖。 可用性設定組內之更新和容錯網域的實際數目上限未來可能會有所變更︰

![圖 2：Azure 可用性設定組中 SAP 應用程式伺服器的高可用性][planning-guide-figure-3000]
 _**圖 2：** Azure 可用性設定組中 SAP 應用程式伺服器的高可用性_

如需詳細資訊，請參閱[在 Azure 中管理 Windows 虛擬機器的可用性][azure-virtual-machines-manage-availability]。

如需詳細資訊，請參閱適用于 SAP NetWeaver 的 Azure 虛擬機器規劃與部署檔的[azure 可用性設定組][planning-guide-3.2.3]一節。

**僅限非受控的磁碟：** 由於 Azure 儲存體帳戶是潛在的單一失敗點，因此您務必擁有至少兩個 Azure 儲存體帳戶，且至少要將兩個虛擬機器分散到其中。 在理想的設定中，執行 SAP 對話方塊執行個體的每一個虛擬機器磁碟會部署在不同的儲存體帳戶中。

> [!IMPORTANT]
> 我們強烈建議您針對 SAP 高可用性安裝使用 Azure 受控磁碟。 因為受控磁碟會針對所連接的虛擬機器，自動配合其可用性設定組，提高了虛擬機器和其上所執行服務的可用性。  
>

### <a name="high-availability-architecture-for-an-sap-ascsscs-instance-on-windows"></a>Windows 上 SAP ASCS/SCS 執行個體的高可用性架構

> ![Windows][Logo_Windows] Windows
>

您可以使用 WSFC 解決方案來保護 SAP ASCS/SCS 執行個體。 解決方案有兩種變化：

* **使用叢集共用磁片來叢集化 SAP ASCS/scs 實例**：如需此架構的詳細資訊，請參閱[使用叢集共用磁片在 Windows 容錯移轉叢集上叢集化 SAP ASCS/scs 實例][sap-high-availability-guide-wsfc-shared-disk]。   

* **使用檔案共用來叢集 SAP ASCS/SCS 實例**：如需此架構的詳細資訊，請參閱[使用檔案共用在 Windows 容錯移轉叢集上進行 SAP ASCS/scs 實例][sap-high-availability-guide-wsfc-file-share]叢集。

* **使用及 SMB 共用來叢集 SAP ASCS/scs 實例**：如需此架構的詳細資訊，請參閱[使用及 SMB 檔案共用在 Windows 容錯移轉叢集上叢集叢集 SAP ASCS/scs 實例](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-windows-netapp-files-smb)。

### <a name="high-availability-architecture-for-an-sap-ascsscs-instance-on-linux"></a>Linux 上 SAP ASCS/SCS 執行個體的高可用性架構

> ![Linux][Logo_Linux] Linux
> 
> 如需使用 SLES 叢集架構來叢集化 SAP ASCS/SCS 實例的詳細資訊，請參閱[適用于 sap 應用程式的 SUSE Linux Enterprise Server 上 Azure vm 上的 Sap NetWeaver 高可用性][sap-suse-ascs-ha]。 針對 SLES 上的替代 HA 架構，這不需要高可用性的 NFS，請參閱[SUSE Linux Enterprise Server 上適用于 sap 應用程式之 Azure NetApp Files 的 Sap NetWeaver 高可用性指南][sap-suse-ascs-ha-anf]。

如需如何使用 Red Hat 叢集架構來進行 SAP ASCS/SCS 執行個體叢集處理的詳細資訊，請參閱 [Red Hat Enterprise Linux 上 SAP NetWeaver 的 Azure 虛擬機器高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)


### <a name="sap-netweaver-multi-sid-configuration-for-a-clustered-sap-ascsscs-instance"></a>叢集 SAP ASCS/SCS 執行個體的 SAP NetWeaver 多重 SID 設定

> ![Windows][Logo_Windows] Windows
> 
> WSFC 支援多重 SID，使用檔案共用和共用磁片。
> 
> 如需 Windows 上多 SID 高可用性架構的詳細資訊，請參閱：

* [適用于 Windows Server 容錯移轉叢集和檔案共用的 SAP ASCS/SCS 實例多重 SID 高可用性][sap-ascs-ha-multi-sid-wsfc-file-share]

* [適用于 Windows Server 容錯移轉叢集和共用磁片的 SAP ASCS/SCS 實例多重 SID 高可用性][sap-ascs-ha-multi-sid-wsfc-shared-disk]

> ![Linux][Logo_Linux] Linux
> 
> 適用于 SAP ASCS/ERS 的 Linux Pacemaker 叢集支援多重 SID 叢集，但在相同叢集上限制為**五個**sap sid。
> 如需 Linux 上多 SID 高可用性架構的詳細資訊，請參閱：

* [SLES for SAP 應用程式上的 Azure Vm 上的 HA for SAP NW 多 SID 指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)
* [RHEL for SAP 應用程式上的 Azure Vm 上的 HA for SAP NW 多 SID 指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid)

### <a name="high-availability-dbms-instance"></a>高可用性 DBMS 執行個體

DBMS 也是 SAP 系統的單一連絡點。 您需要使用高可用性的解決方案來保護它。 下圖顯示在 Azure 中使用 Windows Server 容錯移轉叢集和 Azure 內部負載平衡器的 SQL Server Always On 高可用性解決方案的範例。 SQL Server AlwaysOn 會使用自己的 DBMS 複寫來複寫 DBMS 資料和記錄檔。 在此情況下，您不需要叢集共用磁碟，以簡化整個設定。

![圖 3：高可用性 SAP DBMS，使用 SQL Server Always On 的範例][sap-ha-guide-figure-2003]

_**圖 3：** 高可用性 SAP DBMS，使用 SQL Server Always On 的範例_

如需有關使用 Azure Resource Manager 部署模型在 Azure 中將 SQL Server DBMS 組成叢集的詳細資訊，請參閱下列文章：

* [使用 Resource Manager 以手動方式在 Azure 虛擬機器中設定 AlwaysOn 可用性群組][virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]

* [在 Azure 中設定 AlwaysOn 可用性群組的 Azure 內部負載平衡器][virtual-machines-windows-portal-sql-alwayson-int-listener]

如需有關使用 Azure Resource Manager 部署模型在 Azure 中將 SAP Hana DBMS 叢集的詳細資訊，請參閱[azure 虛擬機器（vm）上 SAP Hana 的高可用性][sap-hana-ha]。
