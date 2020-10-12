---
title: 在 Azure 中使用共用磁片的 WSFC 上的 SAP ASCS/SCS 實例叢集 |Microsoft Docs
description: 了解如何使用叢集共用磁碟於 Windows 容錯移轉叢集上進行 SAP ASCS/SCS 執行個體叢集處理。
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: f6fb85f8-c77a-4af1-bde8-1de7e4425d2e
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/12/2020
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b286812ba0a418d74738837fd5cfb7a7b617a9fa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88854425"
---
# <a name="cluster-an-sap-ascsscs-instance-on-a-windows-failover-cluster-by-using-a-cluster-shared-disk-in-azure"></a>在 Azure 中使用叢集共用磁碟於 Windows 容錯移轉叢集上進行 SAP ASCS/SCS 執行個體叢集處理

> ![Windows 作業系統][Logo_Windows] Windows
>

Windows Server 容錯移轉叢集是 Windows 中高可用性 SAP ASCS/SCS 安裝和 DBMS 的基礎。

容錯移轉叢集是由 1+n 個獨立伺服器 (節點) 所組成的群組，這些伺服器會共同運作以提升應用程式和服務的可用性。 如果發生節點失敗，Windows Server 容錯移轉叢集會計算發生的失敗次數，以及仍然維持狀況良好的叢集，以提供應用程式和服務。 您可以從不同的仲裁模式選擇以達成容錯移轉叢集。

## <a name="prerequisites"></a>Prerequisites
開始本文中的工作之前，請檢閱下列文章：

* [SAP NetWeaver 的 Azure 虛擬機器高可用性架構和案例][sap-high-availability-architecture-scenarios]


## <a name="windows-server-failover-clustering-in-azure"></a>Azure 中的 Windows Server 容錯移轉叢集

使用 Azure 虛擬機器的 Windows Server 容錯移轉叢集需要額外的設定步驟。 當您建置叢集時，需要為 SAP ASCS/SCS 執行個體設定數個 IP 位址和虛擬主機名稱。

### <a name="name-resolution-in-azure-and-the-cluster-virtual-host-name"></a>Azure 和叢集虛擬主機名稱中的名稱解析

Azure 雲端平台不提供設定虛擬 IP 位址的選項，例如浮動 IP 位址。 您需要一個替代解決方案來設定虛擬 IP，以便連線到雲端的叢集資源。 

Azure Load Balancer 服務可為 Azure 提供「內部負載平衡器」**。 使用內部負載平衡器，用戶端可透過叢集虛擬 IP 位址連線叢集。 

在包含叢集節點的資源群組中部署內部負載平衡器。 接著，使用內部負載平衡器的探查連接埠來設定所有必要的連接埠轉送規則。 用戶端可以透過虛擬主機名稱進行連線。 DNS 伺服器會解析叢集 IP 位址，而內部負載平衡器則會處理對作用中叢集節點的轉送。

![圖 1：Azure 中不含共用磁碟的 Windows 容錯移轉叢集設定][sap-ha-guide-figure-1001]

_Azure 中不含共用磁片的 Windows Server 容錯移轉叢集設定_

### <a name="sap-ascsscs-ha-with-cluster-shared-disks"></a>含叢集共用磁碟的 SAP ASCS/SCS HA
在 Windows 中，SAP ASCS/SCS 執行個體包含 SAP 中央服務、SAP 訊息伺服器、加入佇列伺服器處理序和 SAP 全域主機檔案。 SAP 全域主機檔案會儲存整個 SAP 系統的中央檔案。

SAP ASCS/SCS 執行個體包含下列元件：

* SAP 中央服務：
    * 兩個處理常式、訊息和排入佇列伺服器，以及 \<ASCS/SCS virtual host name> 用來存取這兩個處理常式的。
    * 檔案結構： S:\usr\sap \\ &lt; SID &gt; \ ASCS/SCS\<instance number\>


* SAP 全域主機檔案：
  * 檔案結構：S:\usr\sap\\&lt;SID&gt;\SYS\..
  * sapmnt 檔案共用，可利用下列 UNC 路徑，供存取這些全域 S:\usr\sap\\&lt;SID&gt;\SYS\... 檔案：

    \\\\<ASCS/SCS 虛擬主機名稱 \> \Sapmnt \\ &lt; SID &gt; \SYS \. .。。


![圖 2：SAP ASCS/SCS 執行個體的處理序、檔案結構和全域主機 sapmnt 檔案共用][sap-ha-guide-figure-8001]

_SAP ASCS/SCS 實例的進程、檔案結構和全域主機 sapmnt 檔案共用_

在高可用性設定中，您要將 SAP ASCS/SCS 執行個體進行叢集處理。 我們會使用*叢集共用磁碟* (在我們的範例為 S 磁碟機) 來放置 SAP ASCS/SCS 和 SAP 全域主機檔案。

![圖 3：含共用磁碟的 SAP ASCS/SCS HA 架構][sap-ha-guide-figure-8002]

_具有共用磁片的 SAP ASCS/SCS HA 架構_


使用排入佇列伺服器複寫1架構：
* 相同的 \<ASCS/SCS virtual host name> 用途是透過 sapmnt 檔案共用來存取 sap 訊息和排入佇列伺服器進程，以及 sap 全域主機檔案。
* 會在兩者間共用相同的叢集共用磁碟 S 磁碟機。  

使用排入佇列 server replication 2 架構： 
* 這 \<ASCS/SCS virtual host name> 是用來存取 sap 訊息伺服器進程，以及透過 sapmnt 檔案共用的 sap 全域主機檔案。
* 會在兩者間共用相同的叢集共用磁碟 S 磁碟機。
* \<ERS virtual host name>存取排入佇列伺服器進程的情況不同  

![圖 4：含共用磁碟的 SAP ASCS/SCS HA 架構][sap-ha-guide-figure-8003]

_具有共用磁片的 SAP ASCS/SCS HA 架構_

#### <a name="shared-disk-and-enqueue-replication-server"></a>共用磁片和排入佇列複寫伺服器 

1. 佇列伺服器複寫1架構支援共用磁片，其中排入佇列複寫伺服器 (ERS) 實例：   

   - 未叢集化
   - 使用 `localhost` 名稱
   - 會部署在每個叢集節點上的本機磁片上

2. 佇列伺服器複寫2架構也支援共用磁片，其中佇列複寫伺服器 2 (ERS2) 實例：  

   - 已叢集化
   - 使用專用的虛擬/網路主機名稱
   - 除了 () SCS IP 位址之外，還需要在 Azure Internal Load Balancer 上設定 ERS 虛擬主機名稱的 IP 位址
   - 會部署在每個叢集節點上的 **本機磁片** 上，因此不需要共用磁片。

   > [!TIP]
   > 您可以在這裡找到有關將 Replication Server 1 和 2 (ERS1 和) ERS2 排入佇列的詳細資訊：  
   > [在 Microsoft 容錯移轉叢集中將複寫伺服器排入佇列](https://help.sap.com/viewer/3741bfe0345f4892ae190ee7cfc53d4c/CURRENT_VERSION_SWPM20/en-US/8abd4b52902d4b17a105c2fabdf5c0cf.html)  
   > [容錯移轉叢集環境中的新佇列複寫器](https://blogs.sap.com/2019/03/19/new-enqueue-replicator-in-failover-cluster-environments/)  

#### <a name="options-for-shared-disk-in-azure-for-sap-workloads"></a>適用于 SAP 工作負載的 Azure 中的共用磁片選項

在 Azure 中的 windows 容錯移轉叢集中，有兩個共用磁片選項：

- [Azure 共用磁片](https://docs.microsoft.com/azure/virtual-machines/windows/disks-shared) -功能，可讓您同時將 Azure 受控磁片連結到多個 vm。 
- 使用協力廠商軟體 [SIOS DataKeeper Cluster Edition](https://us.sios.com/products/datakeeper-cluster) 來建立鏡像儲存體，以模擬叢集共用儲存體。 

為共用磁片選取技術時，請記住下列考慮：

**適用于 SAP 工作負載的 Azure 共用磁片**
- 可讓您同時將 Azure 受控磁片連結到多個 Vm，而不需要額外的軟體來維護和操作 
- 您將在一個儲存體叢集上使用單一 Azure 共用磁片。 這會影響您的 SAP 解決方案的可靠性。
- 目前唯一支援的部署是在可用性設定組中使用 Azure shared Premium 磁片。 區域部署不支援 Azure 共用磁片。     
- 請務必使用 [進階 SSD 範圍](https://docs.microsoft.com/azure/virtual-machines/windows/disks-shared#disk-sizes) 中所指定的最小磁片大小來布建 Azure Premium 磁片，以同時連接到所需的 vm 數目 (通常是2個用於 SAP ASCS Windows 容錯移轉叢集 ) 。 
- SAP 工作負載不支援 Azure 共用 Ultra 磁片，因為它不支援可用性設定組或區域部署中的部署。  
 
**SIOS**
- SIOS 解決方案可提供兩個磁片之間的即時同步資料複寫
- 利用 SIOS 解決方案，您可以使用兩個受控磁片，而且如果使用可用性設定組或可用性區域，受控磁片將會落在不同的儲存體叢集上。 
- 支援在可用性區域中部署
- 需要安裝和操作協力廠商軟體，您必須另外購買

### <a name="shared-disk-using-azure-shared-disk"></a>使用 Azure 共用磁片的共用磁片

Microsoft 提供 [Azure 共用磁片](https://docs.microsoft.com/azure/virtual-machines/windows/disks-shared)，可用來利用共用磁片選項來執行 SAP ASCS/SCS 高可用性。

#### <a name="prerequisites-and-limitations"></a>必要條件和限制

目前，您可以使用 Azure 進階 SSD 磁片作為 SAP ASCS/SCS 實例的 Azure 共用磁片。 目前已有下列限制：

-  [Azure Ultra 磁片](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk) 不支援作為 SAP 工作負載的 Azure 共用磁片。 目前無法在可用性設定組中使用 Azure Ultra 磁片來放置 Azure Vm
-  只有可用性設定組中的 Vm 支援具有進階 SSD 磁片的[Azure 共用磁片](https://docs.microsoft.com/azure/virtual-machines/windows/disks-shared)。 可用性區域部署中並不支援此功能。 
-  Azure 共用磁片值 [maxShares](https://docs.microsoft.com/azure/virtual-machines/windows/disks-shared-enable?tabs=azure-cli#disk-sizes) 決定可使用共用磁片的叢集節點數目。 通常針對 SAP ASCS/SCS 實例，您將在 Windows 容錯移轉叢集中設定兩個節點，因此的值 `maxShares` 必須設定為 [2]。
-  所有 SAP ASCS/SCS 叢集 Vm 都必須部署在相同的 [Azure 鄰近放置群組](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups)中。   
   雖然您可以在沒有 PPG 的情況下，使用 Azure 共用磁片在可用性設定組中部署 Windows 叢集 Vm，PPG 將可確保接近 Azure 共用磁片和叢集 Vm 的實體鄰近程度，因此可在 Vm 和儲存層之間達到較低的延遲。    

如需 Azure 共用磁片限制的進一步詳細資料，請仔細閱讀 Azure 共用磁片檔的 [限制](https://docs.microsoft.com/azure/virtual-machines/linux/disks-shared#limitations) 一節。

> [!IMPORTANT]
> 使用 Azure 共用磁片部署 SAP ASCS/SCS Windows 容錯移轉叢集時，請注意，您的部署將會使用一個儲存體叢集中的單一共用磁片來運作。 您的 SAP ASCS/SCS 實例會受到影響，以防儲存體叢集發生問題，也就是部署 Azure 共用磁片的位置。    

> [!TIP]
> 請參閱 sap [Netweaver On Azure 規劃指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide) 和 [sap 工作負載的 Azure 儲存體指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide-storage) ，以瞭解規劃 sap 部署時的重要考慮。

### <a name="supported-os-versions"></a>支援的 OS 版本

支援 Windows Server 2016 和 2019 (使用最新的資料中心映射) 。

我們強烈建議使用 **Windows Server 2019 Datacenter**，如下所示：
- Windows 2019 容錯移轉叢集服務為 Azure 感知
- 藉由監視 Azure 排程事件，可新增 Azure 主機維護的整合和認知，並改善體驗。
- 您可以使用分散式網路名稱 (它是) 的預設選項。 因此，叢集網路名稱不需要有私人 IP 位址。 此外，您也不需要在 Azure Internal Load Balancer 上設定此 IP 位址。 

### <a name="shared-disks-in-azure-with-sios-datakeeper"></a>使用 SIOS DataKeeper 在 Azure 中共用磁碟

共用磁片的另一個選項是使用協力廠商軟體 SIOS DataKeeper Cluster Edition 來建立鏡像儲存體，以模擬叢集共用儲存體。 SIOS 解決方案提供即時同步的資料複寫。

若要為叢集建立共用磁碟資源：

1. 將額外的磁碟連接到 Windows 叢集組態中的每個虛擬機器。
2. 在兩個虛擬機器節點上執行 SIOS DataKeeper Cluster Edition。
3. 設定 SIOS DataKeeper Cluster Edition，使它將來源虛擬機器的額外磁碟連接磁碟區內容鏡像至目標虛擬機器的額外磁碟連接磁碟區。 SIOS DataKeeper 會提取來源和目標本機磁碟區，並將它們當作一個共用磁碟來呈現給 Windows Server 容錯移轉叢集。

取得 [SIOS DataKeeper](https://us.sios.com/products/datakeeper-cluster/)的詳細資訊。

![圖 5：Azure 中含 SIOS DataKeeper 的 Windows Server 容錯移轉叢集組態][sap-ha-guide-figure-1002]

_使用 SIOS DataKeeper 在 Azure 中設定 Windows 容錯移轉叢集_

> [!NOTE]
> 您不需要與某些 DBMS 產品 (例如 SQL Server) 共用提供高可用性的磁碟。 SQL Server AlwaysOn 會將 DBMS 資料及記錄檔，從一個叢集節點的本機磁碟複寫到另一個叢集節點的本機磁碟。 在此情況下，Windows 叢集組態不需要共用磁碟。
>

## <a name="next-steps"></a>接下來的步驟

* [使用 SAP ASCS/SCS 執行個體的 Windows 容錯移轉叢集和共用磁碟，為 SAP HA 準備 Azure 基礎結構][sap-high-availability-infrastructure-wsfc-shared-disk]

* [在 SAP ASCS/SCS 執行個體的 Windows 容錯移轉叢集和共用磁碟上安裝 SAP NetWeaver HA][sap-high-availability-installation-wsfc-shared-disk]


[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides

[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md
[ha-guide]:sap-high-availability-guide.md
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

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


[sap-ha-guide-figure-8001]:./media/virtual-machines-shared-sap-high-availability-guide/8001.png
[sap-ha-guide-figure-8002]:./media/virtual-machines-shared-sap-high-availability-guide/8002.png
[sap-ha-guide-figure-8003]:./media/virtual-machines-shared-sap-high-availability-guide/8003.png
[sap-ha-guide-figure-8004]:./media/virtual-machines-shared-sap-high-availability-guide/8004.png
[sap-ha-guide-figure-8005]:./media/virtual-machines-shared-sap-high-availability-guide/8005.png
[sap-ha-guide-figure-8006]:./media/virtual-machines-shared-sap-high-availability-guide/8006.png
[sap-ha-guide-figure-8007]:./media/virtual-machines-shared-sap-high-availability-guide/8007.png
[sap-ha-guide-figure-8008]:./media/virtual-machines-shared-sap-high-availability-guide/8008.png
[sap-ha-guide-figure-8009]:./media/virtual-machines-shared-sap-high-availability-guide/8009.png
[sap-ha-guide-figure-8010]:./media/virtual-machines-shared-sap-high-availability-guide/8010.png
[sap-ha-guide-figure-8011]:./media/virtual-machines-shared-sap-high-availability-guide/8011.png
[sap-ha-guide-figure-8012]:./media/virtual-machines-shared-sap-high-availability-guide/8012.png
[sap-ha-guide-figure-8013]:./media/virtual-machines-shared-sap-high-availability-guide/8013.png
[sap-ha-guide-figure-8014]:./media/virtual-machines-shared-sap-high-availability-guide/8014.png
[sap-ha-guide-figure-8015]:./media/virtual-machines-shared-sap-high-availability-guide/8015.png
[sap-ha-guide-figure-8016]:./media/virtual-machines-shared-sap-high-availability-guide/8016.png
[sap-ha-guide-figure-8017]:./media/virtual-machines-shared-sap-high-availability-guide/8017.png
[sap-ha-guide-figure-8018]:./media/virtual-machines-shared-sap-high-availability-guide/8018.png
[sap-ha-guide-figure-8019]:./media/virtual-machines-shared-sap-high-availability-guide/8019.png
[sap-ha-guide-figure-8020]:./media/virtual-machines-shared-sap-high-availability-guide/8020.png
[sap-ha-guide-figure-8021]:./media/virtual-machines-shared-sap-high-availability-guide/8021.png
[sap-ha-guide-figure-8022]:./media/virtual-machines-shared-sap-high-availability-guide/8022.png
[sap-ha-guide-figure-8023]:./media/virtual-machines-shared-sap-high-availability-guide/8023.png
[sap-ha-guide-figure-8024]:./media/virtual-machines-shared-sap-high-availability-guide/8024.png
[sap-ha-guide-figure-8025]:./media/virtual-machines-shared-sap-high-availability-guide/8025.png


[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md
