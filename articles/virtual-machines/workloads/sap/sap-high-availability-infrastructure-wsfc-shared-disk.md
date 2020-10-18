---
title: 適用于 SAP ASCS/SCS 和 WSFC&共用磁片的 Azure 基礎結構 |Microsoft Docs
description: 了解如何使用 SAP ASCS/SCS 執行個體的 Windows 容錯移轉叢集和共用磁碟，為 SAP HA 準備 Azure 基礎結構。
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ec976257-396b-42a0-8ea1-01c97f820fa6
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/16/2020
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1af2e741b2ab8a6a0aa6257272798961f5962c43
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/18/2020
ms.locfileid: "92167333"
---
# <a name="prepare-the-azure-infrastructure-for-sap-ha-by-using-a-windows-failover-cluster-and-shared-disk-for-sap-ascsscs"></a>使用 SAP ASCS/SCS 的 Windows 容錯移轉叢集和共用磁碟，為 SAP HA 準備 Azure 基礎結構

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides
[tuning-failover-cluster-network-thresholds]:https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834

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
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-ha-guide-9.1.1]:high-availability-guide.md#a97ad604-9094-44fe-a364-f89cb39bf097
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f


[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-shared-disk-vpn]:sap-high-availability-infrastructure-wsfc-shared-disk.md#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-high-availability-infrastructure-wsfc-shared-disk-change-def-ilb]:sap-high-availability-infrastructure-wsfc-shared-disk.md#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-high-availability-infrastructure-wsfc-shared-disk-setup-wsfc]:sap-high-availability-infrastructure-wsfc-shared-disk.md#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-high-availability-infrastructure-wsfc-shared-disk-collect-cluster-config]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-high-availability-infrastructure-wsfc-shared-disk-add-dot-net]:sap-high-availability-infrastructure-wsfc-shared-disk.md#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios-both-nodes]:sap-high-availability-infrastructure-wsfc-shared-disk.md#dd41d5a2-8083-415b-9878-839652812102
[sap-high-availability-infrastructure-wsfc-shared-disk-setup-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (SAP 多重 SID 高可用性設定)

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png


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


> ![Windows 作業系統][Logo_Windows] Windows


本文說明在 Windows 容錯移轉叢集上使用叢集 *共用磁片* 作為叢集 SAP ASCS 實例的選項，準備 Azure 基礎結構以在 Windows 容錯移轉叢集上安裝和設定高可用性 SAP ASCS/SCS 實例時，所採取的步驟。
叢集 *共用磁片* 的兩個替代方案會顯示在檔中：

- [Azure 共用磁碟](../../windows/disks-shared.md)
- 使用 [SIOS DataKeeper Cluster Edition](https://us.sios.com/products/datakeeper-cluster/) 建立鏡像儲存體，以模擬叢集共用磁片 

呈現的設定會依賴 [Azure 鄰近放置群組 (PPG) ](./sap-proximity-placement-scenarios.md) ，以達到 SAP 工作負載的最佳網路延遲。 檔未涵蓋資料庫層。  

> [!NOTE]
> Azure 鄰近放置群組是使用 Azure 共用磁片的先決條件。
 

## <a name="prerequisites"></a>必要條件

在開始安裝之前，請檢閱這篇文章：

* [架構手冊：使用叢集共用磁碟於 Windows 容錯移轉叢集上進行 SAP ASCS/SCS 執行個體叢集處理][sap-high-availability-guide-wsfc-shared-disk]

## <a name="create-the-ascs-vms"></a>建立 ASCS Vm

針對 SAP ASCS/SCS 叢集，請在 Azure 可用性設定組中部署兩個 Vm。 將 Vm 部署在相同的鄰近位置群組中。 部署 Vm 之後：  
- 建立適用于 SAP ASCS/SCS 實例的 Azure Internal Load Balancer 
- 將 Windows Vm 新增至 AD 網域

顯示案例的主機名稱和 IP 位址如下：

| 主機名稱角色 | 主機名稱 | 靜態 IP 位址 | 可用性設定組 | 鄰近位置群組 |
| --- | --- | --- |---| ---|
| 第1個叢集節點 ASCS/SCS 叢集 |pr1-ascs-10 |10.0.0.4 |pr1-ascs-avset |PR1PPG |
| 第2個叢集節點 ASCS/SCS 叢集 |pr1-ascs-11 |10.0.0.5 |pr1-ascs-avset |PR1PPG |
| 叢集網路名稱 | pr1clust |10.0.0.42 (**僅** 適用于 Win 2016 叢集)  | n/a | n/a |
| ASCS 叢集網路名稱 | pr1-ascscl |10.0.0.43 | n/a | n/a |
| **僅**針對 ERS2) ERS 叢集網路名稱 ( | pr1-erscl |10.0.0.44 | n/a | n/a |


## <a name="create-azure-internal-load-balancer"></a><a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a> 建立 Azure 內部負載平衡器

SAP ASCS、SAP SCS 和新的 SAP ERS2 會使用虛擬主機名稱和虛擬 IP 位址。 在 Azure 上，需要 [負載平衡器](../../../load-balancer/load-balancer-overview.md) 才能使用虛擬 IP 位址。 我們強烈建議使用 [標準負載平衡器](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md)。 

> [!IMPORTANT]
> 負載平衡案例中的 NIC 次要 IP 設定不支援浮動 IP。 如需詳細資訊，請參閱 [Azure 負載平衡器的限制](https://docs.microsoft.com/azure/load-balancer/load-balancer-multivip-overview#limitations)。 如果您需要 VM 的其他 IP 位址，請部署第二個 NIC。    


下列清單顯示) SCS/ERS 負載平衡器 (的設定。 在相同的 Azure 負載平衡器中執行的 SAP ASCS 和 ERS2 設定。  

**(A)SCS**
- 前端組態
    - 靜態 ASCS/SCS IP 位址 **10.0.0.43**
- 後端組態  
    新增應屬於 () SCS/ERS 叢集一部分的所有虛擬機器。 在此範例中，Vm **pr1-ascs-10** 和 **pr1-ascs-11**。
- 探查連接埠
    - 埠 620**nr** 保留 PROTOCOL (TCP) 的預設選項，間隔 (5) ，狀況不良閾值 (2) 
- 負載平衡規則
    - 若使用 Standard Load Balancer，請選取 [HA 連接埠]
    - 若使用基本負載平衡器，請為下列連接埠建立負載平衡規則
        - 32**nr** TCP
        - 36**nr** TCP
        - 39**nr** TCP
        - 81**nr** TCP
        - 5**nr**13 TCP
        - 5**nr**14 TCP
        - 5**nr**16 TCP

    - 請確定 [閒置超時] (分鐘) 設定為 [最大值 30]，而且已啟用 [直接伺服器傳回) ] 浮動 IP (。

**ERS2**

由於排入佇列複寫伺服器 2 (ERS2) 也會叢集化，因此除了 SAP ASCS/SCS IP 之外，也必須在 Azure ILB 上設定 ERS2 虛擬 IP 位址。 本節僅適用于使用排入佇列複寫伺服器2架構的情況下。  
- 第2個前端設定
    - 靜態 SAP ERS2 IP 位址 **10.0.0.44**

- 後端組態  
  Vm 已新增至 ILB 後端集區。  

- 第2個探查埠
    - 連接埠 621**nr**  
    保留通訊協定 (TCP) 的預設選項、間隔 (5) 、狀況不良的閾值 (2) 

- 第2個負載平衡規則
    - 若使用 Standard Load Balancer，請選取 [HA 連接埠]
    - 若使用基本負載平衡器，請為下列連接埠建立負載平衡規則
        - 32**nr** TCP
        - 33**nr** TCP
        - 5**nr**13 TCP
        - 5**nr**14 TCP
        - 5**nr**16 TCP

    - 請確定 [閒置超時] (分鐘) 設定為 [最大值 30]，而且已啟用 [直接伺服器傳回) ] 浮動 IP (。


> [!TIP]
> 使用適用于 [WSFC 的 Azure Resource Manager 範本搭配 Azure 共用磁片進行 SAP ASCS/SCS 實例](https://github.com/robotechredmond/301-shared-disk-sap)時，您可以將 Azure 共用磁片用於一個具有 ERS1 的 sap SID，以將基礎結構準備工作自動化。  
> Azure ARM 範本將會建立兩個 Windows 2019 或 2016 Vm、建立 Azure 共用磁片並連結至 Vm。 也會建立和設定 Azure 內部 Load Balancer。 如需詳細資訊，請參閱 ARM 範本。 

## <a name="add-registry-entries-on-both-cluster-nodes-of-the-ascsscs-instance"></a><a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a> 在 ASCS/SCS 實例的兩個叢集節點上新增登錄專案

如果連線閒置一段時間，並超過閒置時間，Azure Load Balancer 可能會關閉連接。 當需要傳送第一個排入佇列/清除佇列要求時，SAP 工作會處理與 SAP 排入佇列進程的開啟連線。 若要避免中斷這些連線，請變更兩個叢集節點上的 TCP/IP KeepAliveTime 和 KeepAliveInterval 值。 如果使用 ERS1，則也必須新增 SAP 設定檔參數，如本文稍後所述。
下列登錄專案必須在這兩個叢集節點上變更：

- KeepAliveTime
- KeepAliveInterval

| 路徑| 變數名稱 | 變數類型  | 值 | 文件 |
| --- | --- | --- |---| ---|
| HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |KeepAliveTime |REG_DWORD (十進位) |120000 |[KeepAliveTime](/previous-versions/windows/it-pro/windows-2000-server/cc957549(v=technet.10)) |
| HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |KeepAliveInterval |REG_DWORD (十進位) |120000 |[KeepAliveInterval](/previous-versions/windows/it-pro/windows-2000-server/cc957548(v=technet.10)) |


若要套用變更，請重新啟動這兩個叢集節點。
  
## <a name="add-the-windows-vms-to-the-domain"></a><a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a> 將 Windows Vm 新增至網域
將靜態 IP 位址指派給虛擬機器之後，請將虛擬機器新增至網域。 

## <a name="install-and-configure--windows-failover-cluster"></a><a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a> 安裝和設定 Windows 容錯移轉叢集 

### <a name="install-the-windows-failover-cluster-feature"></a>安裝 Windows 容錯移轉叢集功能

在其中一個叢集節點上執行此命令：

   ```powershell
    # Hostnames of the Win cluster for SAP ASCS/SCS
    $SAPSID = "PR1"
    $ClusterNodes = ("pr1-ascs-10","pr1-ascs-11")
    $ClusterName = $SAPSID.ToLower() + "clust"
    
    # Install Windows features.
    # After the feature installs, manually reboot both nodes
    Invoke-Command $ClusterNodes {Install-WindowsFeature Failover-Clustering, FS-FileServer -IncludeAllSubFeature -IncludeManagementTools }
   ```

功能安裝完成後，請重新開機這兩個叢集節點。  

### <a name="test-and-configure-windows-failover-cluster"></a>測試和設定 Windows 容錯移轉叢集 

在 Windows 2019 上，叢集會自動辨識它是在 Azure 中執行，而且作為叢集管理 IP 的預設選項，它會使用分散式網路名稱。 因此，它會使用任何叢集節點的本機 IP 位址。 因此，叢集不需要專用 (虛擬) 網路名稱，也不需要在 Azure 內部 Load Balancer 上設定此 IP 位址。

如需詳細資訊，請參閱 [Windows Server 2019 容錯移轉叢集的新功能](https://techcommunity.microsoft.com/t5/failover-clustering/windows-server-2019-failover-clustering-new-features/ba-p/544029) 在其中一個叢集節點上執行此命令：

   ```powershell
    # Hostnames of the Win cluster for SAP ASCS/SCS
    $SAPSID = "PR1"
    $ClusterNodes = ("pr1-ascs-10","pr1-ascs-11")
    $ClusterName = $SAPSID.ToLower() + "clust"
    
    # IP adress for cluster network name is needed ONLY on Windows Server 2016 cluster
    $ClusterStaticIPAddress = "10.0.0.42"
        
    # Test cluster
    Test-Cluster –Node $ClusterNodes -Verbose
    
    $ComputerInfo = Get-ComputerInfo
    
    $WindowsVersion = $ComputerInfo.WindowsProductName
    
    if($WindowsVersion -eq "Windows Server 2019 Datacenter"){
        write-host "Configuring Windows Failover Cluster on Windows Server 2019 Datacenter..."
        New-Cluster –Name $ClusterName –Node  $ClusterNodes -Verbose
    }elseif($WindowsVersion -eq "Windows Server 2016 Datacenter"){
        write-host "Configuring Windows Failover Cluster on Windows Server 2016 Datacenter..."
        New-Cluster –Name $ClusterName –Node  $ClusterNodes –StaticAddress $ClusterStaticIPAddress -Verbose 
    }else{
        Write-Error "Not supported Windows version!"
    }
   ```

### <a name="configure-cluster-cloud-quorum"></a>設定叢集雲端仲裁
當您使用 Windows Server 2016 或2019時，建議您將 [Azure 雲端見證](/windows-server/failover-clustering/deploy-cloud-witness)設定為叢集仲裁。

在其中一個叢集節點上執行此命令：

   ```powershell
    $AzureStorageAccountName = "cloudquorumwitness"
    Set-ClusterQuorum –CloudWitness –AccountName $AzureStorageAccountName -AccessKey <YourAzureStorageAccessKey> -Verbose
   ```

### <a name="tuning-the-windows-failover-cluster-thresholds"></a>調整 Windows 容錯移轉叢集閾值
 
成功安裝 Windows 容錯移轉叢集之後，您需要調整一些閾值，以適用于在 Azure 中部署的叢集。 需要變更的參數記載於[調整容錯移轉叢集網路閥值](https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834)中。 假設為 ASCS/SCS 建置 Windows 叢集組態的兩部 VM 位於相同子網路中，請將下列參數變更為這些值︰
- SameSubNetDelay = 2000
- SameSubNetThreshold = 15
- RoutingHistoryLength = 30

這些設定已通過客戶的測試，並提供良好的危害。 它們具有足夠的彈性，但它們也提供快速容錯移轉，以滿足 SAP 工作負載或 VM 失敗中的真實錯誤情況。  

## <a name="configure-azure-shared-disk"></a>設定 Azure 共用磁片
只有在您使用 Azure 共用磁片時，本節才適用。 

### <a name="create-and-attach-azure-shared-disk-with-powershell"></a>使用 PowerShell 建立和附加 Azure 共用磁片
在其中一個叢集節點上執行此命令。 您將需要調整資源群組、Azure 區域、SAPSID 等等的值。  

   ```powershell
    #############################
    # Create Azure Shared Disk
    #############################
    
    $ResourceGroupName = "MyResourceGroup"
    $location = "MyAzureRegion"
    $SAPSID = "PR1"
    
    $DiskSizeInGB = 512
    $DiskName = "$($SAPSID)ASCSSharedDisk"
    
    # With parameter '-MaxSharesCount', we define the maximum number of cluster nodes to attach the shared disk
    $NumberOfWindowsClusterNodes = 2
            
    $diskConfig = New-AzDiskConfig -Location $location -SkuName Premium_LRS  -CreateOption Empty  -DiskSizeGB $DiskSizeInGB -MaxSharesCount $NumberOfWindowsClusterNodes
    $dataDisk = New-AzDisk -ResourceGroupName $ResourceGroupName -DiskName $DiskName -Disk $diskConfig
    
    ##################################
    ## Attach the disk to cluster VMs
    ##################################
    # ASCS Cluster VM1
    $ASCSClusterVM1 = "$SAPSID-ascs-10"
    
    # ASCS Cluster VM2
    $ASCSClusterVM2 = "$SAPSID-ascs-11"
    
    # Add the Azure Shared Disk to Cluster Node 1
    $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $ASCSClusterVM1 
    $vm = Add-AzVMDataDisk -VM $vm -Name $DiskName -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 0
    Update-AzVm -VM $vm -ResourceGroupName $ResourceGroupName -Verbose
    
    # Add the Azure Shared Disk to Cluster Node 2
    $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $ASCSClusterVM2
    $vm = Add-AzVMDataDisk -VM $vm -Name $DiskName -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 0
    Update-AzVm -VM $vm -ResourceGroupName $ResourceGroupName -Verbose
   ```

### <a name="format-the-shared-disk-with-powershell"></a>使用 PowerShell 將共用磁片格式化
1. 取得磁片編號。 在其中一個叢集節點上執行下列 PowerShell 命令：

   ```powershell
    Get-Disk | Where-Object PartitionStyle -Eq "RAW"  | Format-Table -AutoSize 
    # Example output
    # Number Friendly Name     Serial Number HealthStatus OperationalStatus Total Size Partition Style
    # ------ -------------     ------------- ------------ ----------------- ---------- ---------------
    # 2      Msft Virtual Disk               Healthy      Online                512 GB RAW            

   ```
2. 將磁片格式化。 在此範例中，它是磁片編號2。 

   ```powershell
    # Format SAP ASCS Disk number '2', with drive letter 'S'
    $SAPSID = "PR1"
    $DiskNumber = 2
    $DriveLetter = "S"
    $DiskLabel = "$SAPSID" + "SAP"
    
    Get-Disk -Number $DiskNumber | Where-Object PartitionStyle -Eq "RAW" | Initialize-Disk -PartitionStyle GPT -PassThru |  New-Partition -DriveLetter $DriveLetter -UseMaximumSize | Format-Volume  -FileSystem ReFS -NewFileSystemLabel $DiskLabel -Force -Verbose
    # Example outout
    # DriveLetter FileSystemLabel FileSystem DriveType HealthStatus OperationalStatus SizeRemaining      Size
    # ----------- --------------- ---------- --------- ------------ ----------------- -------------      ----
    # S           PR1SAP          ReFS       Fixed     Healthy      OK                    504.98 GB 511.81 GB
   ```

3. 確認磁片現在已顯示為叢集磁片。  
   ```powershell
    # List all disks
    Get-ClusterAvailableDisk -All
    # Example output
    # Cluster    : pr1clust
    # Id         : 88ff1d94-0cf1-4c70-89ae-cbbb2826a484
    # Name       : Cluster Disk 1
    # Number     : 2
    # Size       : 549755813888
    # Partitions : {\\?\GLOBALROOT\Device\Harddisk2\Partition2\}
   ```
4. 在叢集中註冊磁片。  
   ```powershell
    # Add the disk to cluster 
    Get-ClusterAvailableDisk -All | Add-ClusterDisk
    # Example output     
    # Name           State  OwnerGroup        ResourceType 
    # ----           -----  ----------        ------------ 
    # Cluster Disk 1 Online Available Storage Physical Disk
   ```

## <a name="sios-datakeeper-cluster-edition-for-the-sap-ascsscs-cluster-share-disk"></a><a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a> SAP ASCS/SCS 叢集共用磁片的 SIOS DataKeeper Cluster Edition
只有在您使用協力廠商軟體 SIOS DataKeeper Cluster Edition 來建立鏡像儲存體，以模擬叢集共用磁片時，本節才適用。  

現在，您已在 Azure 中使用 Windows Server 容錯移轉叢集設定。 若要安裝 SAP ASCS/SCS 執行個體，您需要有一個共用磁碟資源。 其中一個選項是使用 SIOS DataKeeper Cluster Edition 是可供您用來建立共用磁片資源的協力廠商解決方案。  

為 SAP ASCS/SCS 叢集共用磁碟安裝 SIOS DataKeeper Cluster Edition 包含下列工作︰
- 如有需要，請新增 Microsoft .NET Framework。 請參閱 [SIOS 檔] ( # B1 https://us.sios.com/products/datakeeper-cluster/) 以取得最新的 .net framework 需求 
-  安裝 SIOS DataKeeper
- 設定 SIOS DataKeeper

### <a name="install-sios-datakeeper"></a> 安裝 SIOS DataKeeper
在叢集中的每個節點上安裝 SIOS DataKeeper Cluster Edition。 若要使用 SIOS DataKeeper 建立虛擬共用儲存體，請建立同步處理的鏡像，然後模擬叢集共用儲存體。

安裝 SIOS 軟體之前，請先建立 DataKeeperSvc 網域使用者。

> [!NOTE]
> 將 DataKeeperSvc 往欲使用者同時新增到兩個叢集節點上的 [本機管理員] 群組中。
>

1. 在兩個叢集節點上都安裝 SIOS 軟體。

   ![SIOS 安裝程式][sap-ha-guide-figure-3030]

   ![圖 31：第一個 SIOS DataKeeper 安裝頁面][sap-ha-guide-figure-3031]

   _SIOS DataKeeper 安裝的第一頁_

2. 在對話方塊中，選取 [是]****。

   ![圖 32：DataKeeper 通知將停用某個服務][sap-ha-guide-figure-3032]

   _DataKeeper 通知您將停用服務_

3. 在對話方塊中，建議您選取 [網域或伺服器帳戶]****。

   ![圖 33：SIOS DataKeeper 的使用者選取項目][sap-ha-guide-figure-3033]

   _SIOS DataKeeper 的使用者選取項目_

4. 輸入您為 SIOS DataKeeper 建立的網域帳戶使用者名稱和密碼。

   ![圖 34：為 SIOS DataKeeper 安裝輸入網域使用者名稱和密碼][sap-ha-guide-figure-3034]

   _為 SIOS DataKeeper 安裝輸入網域使用者名稱和密碼_

5. 為您的 SIOS DataKeeper 執行個體安裝授權金鑰，如圖 35 所示。

   ![圖 35：輸入您的 SIOS DataKeeper 授權金鑰][sap-ha-guide-figure-3035]

   _輸入您的 SIOS DataKeeper 授權金鑰_

6. 出現提示時，重新啟動虛擬機器。

### <a name="configure-sios-datakeeper"></a>設定 SIOS DataKeeper
在兩個節點上都安裝 SIOS DataKeeper 之後，開始設定組態。 組態的目標是要在連接至每個虛擬機器的額外磁碟之間進行同步資料複寫。

1. 啟動 DataKeeper 管理與組態工具，然後選取 [連線伺服器]。

   ![圖 36：SIOS DataKeeper 管理和組態工具][sap-ha-guide-figure-3036]

   _SIOS DataKeeper 管理和組態工具_

2. 輸入管理和組態工具應連接之第一個節點的名稱或 TCP/IP 位址，然後在第二個步驟中，插入第二個節點。

   ![圖 37：插入管理和組態工具應連接之第一個節點的名稱或 TCP/IP 位址，然後在第二個步驟中，插入第二個節點][sap-ha-guide-figure-3037]

   _插入管理和設定工具應連接之第一個節點的名稱或 TCP/IP 位址，然後在第二個步驟中，插入第二個節點_

3. 在兩個節點之間建立複寫作業。

   ![圖 38：建立複寫作業][sap-ha-guide-figure-3038]

   _建立複寫作業_

   精靈會引導您完成建立複寫作業的程序。

4. 定義複寫作業的名稱。

   ![圖 39：定義複寫作業的名稱][sap-ha-guide-figure-3039]

   _定義複寫作業的名稱_

   ![圖 40：定義節點 (應該是目前的來源節點) 的基底資料][sap-ha-guide-figure-3040]

   _定義節點（應該是目前的來源節點）的基底資料_

5. 定義名稱、TCP/IP 位址和目標節點的磁碟區。

   ![圖 41：定義名稱、TCP/IP 位址和目前目標節點的磁碟區][sap-ha-guide-figure-3041]

   _定義名稱、TCP/IP 位址和目前目標節點的磁碟區_

6. 定義壓縮演算法。 在我們的範例中，建議壓縮複寫資料流。 尤其是在重新同步處理的情況下，壓縮複寫資料流可大幅縮短重新同步處理時間。 壓縮會使用虛擬機器的 CPU 和 RAM 資源。 隨著壓縮率增加，使用的 CPU 資源數量也跟著增加。 您可以稍後調整此設定。

7. 另一個您需要檢查的設定是複寫是以非同步方式還是同步方式進行。 當您保護 SAP ASCS/SCS 組態時，必須使用同步複寫。  

   ![圖 42︰定義複寫詳細資料][sap-ha-guide-figure-3042]

   _定義複寫詳細資料_

8. 定義是否應該向 Windows Server 容錯移轉叢集的叢集組態指出複寫作業所複寫的磁碟區是共用磁碟。 就 SAP ASCS/SCS 組態而言，選取 [是] ，如此 Windows 叢集才會將複寫的磁碟區視為可用來作為叢集磁碟區的共用磁碟。

   ![圖 43：選取 [是] 以將複寫的磁碟區設定為叢集磁碟區][sap-ha-guide-figure-3043]

   _選取 [ **是]** 以將複寫的磁片區設定為叢集磁片區_

   建立磁碟區之後，DataKeeper 管理和組態工具會顯示複寫作業是使用中。

   ![圖 44：SAP ASCS/SCS 共用磁碟的 DataKeeper 同步鏡像處於作用中狀態][sap-ha-guide-figure-3044]

   _SAP ASCS/SCS 共用磁碟的 DataKeeper 同步鏡像處於作用中狀態_

   「容錯移轉叢集管理員」現在會顯示磁碟為 DataKeeper 磁碟，如圖 45 所示：

   ![圖 45：「容錯移轉叢集管理員」顯示 DataKeeper 複寫的磁碟][sap-ha-guide-figure-3045]

   _「容錯移轉叢集管理員」顯示 DataKeeper 複寫的磁碟_


## <a name="next-steps"></a>後續步驟

* [使用 Windows 容錯移轉叢集和共用磁碟為 SAP ASCS/SCS 執行個體安裝 SAP NetWeaver HA][sap-high-availability-installation-wsfc-shared-disk]