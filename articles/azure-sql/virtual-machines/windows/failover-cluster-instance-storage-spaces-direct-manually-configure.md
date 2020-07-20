---
title: SQL Server FCI - Azure 虛擬機器 | Microsoft Docs
description: 本文說明如何在 Azure 虛擬機器上建立 SQL Server 容錯移轉叢集執行個體。
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 9fc761b1-21ad-4d79-bebc-a2f094ec214d
ms.service: virtual-machines-sql
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/11/2018
ms.author: mikeray
ms.openlocfilehash: 75c25454451b733870f8a674b292cd131454f4d2
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2020
ms.locfileid: "84032309"
---
# <a name="configure-a-sql-server-failover-cluster-instance-on-azure-virtual-machines"></a>在 Azure 虛擬機器上設定 SQL Server 容錯移轉叢集執行個體
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

本文說明如何在 Azure Resource Manager 模型內的 Azure 虛擬機器中，建立 SQL Server 容錯移轉叢集執行個體 (FCI)。 此解決方案會使用 [Windows Server 2016 Datacenter 版本儲存空間直接存取](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview)作為同步 Windows 叢集中節點 (Azure VM) 間儲存體 (資料磁碟) 的軟體式虛擬 SAN。 Windows Server 2016 中的儲存空間直接存取。

下圖顯示 Azure 虛擬機器中的完整解決方案：

![完整解決方案](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/00-sql-fci-s2d-complete-solution.png)

此圖顯示：

- Windows Server 容錯移轉叢集中的兩部 Azure 虛擬機器。 位於容錯移轉叢集中的虛擬機器也稱為「叢集節點」或「節點」。
- 每部虛擬機器有兩個以上的資料磁碟。
- 儲存空間直接存取會同步資料磁碟上的資料，並將同步儲存體作為儲存集區呈現。
- 儲存集區會向容錯移轉叢集提供叢集共用磁碟區 (CSV)。
- SQL Server FCI 叢集角色會針對資料硬碟使用 CSV。
- 保留 SQL Server FCI IP 位址的 Azure Load Balancer。
- 保留所有資源的 Azure 可用性設定組。

>[!NOTE]
>圖表內的所有 Azure 資源都位於相同的資源群組中。

如需儲存空間直接存取的詳細資料，請參閱 [Windows Server 2016 Datacenter 版本儲存空間直接存取](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview)。

儲存空間直接存取支援兩種架構類型：交集和超交集。 本文件中的架構為超交集。 超交集基礎結構會將儲存體放置於與裝載叢集應用程式相同的伺服器上。 在此架構中，儲存體會放置在每個 SQL Server FCI 節點上。

## <a name="licensing-and-pricing"></a>授權和定價

在 Azure 虛擬機器上，您可使用隨用隨付 (PAYG) 或自備授權 (BYOL) VM 映像來授權 SQL Server。 您選擇的映像類型會影響收費方式。

使用隨用隨付授權時，Azure 虛擬機器上的 SQL Server 容錯移轉叢集執行個體 (FCI) 會為 FCI 的所有節點產生費用，包含被動節點。 如需詳細資訊，請參閱 [SQL Server Enterprise 虛擬機器定價](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/)。

若有具備軟體保證的 Enterprise 合約，則可針對每個主動節點使用一個免費的被動 FCI 節點。 若要利用 Azure 的這項優點，可使用 BYOL VM 映像，然後在 FCI 的主動和被動節點上使用相同的授權。 如需詳細資訊，請參閱 [Enterprise 合約](https://www.microsoft.com/Licensing/licensing-programs/enterprise.aspx)。

若要比較 Azure 虛擬機器上 SQL Server 的隨用隨付和 BYOL 授權，請參閱[開始使用 SQL VM](sql-server-on-azure-vm-iaas-what-is-overview.md#get-started-with-sql-vms)。

如需授權 SQL Server 的完整資訊，請參閱[定價](https://www.microsoft.com/sql-server/sql-server-2017-pricing)。

### <a name="example-azure-template"></a>Azure 範本範例

您可在 Azure 中，從範本開始建立整個解決方案。 GitHub [Azure 快速入門範本](https://github.com/MSBrett/azure-quickstart-templates/tree/master/sql-server-2016-fci-existing-vnet-and-ad) 內有提供範本範例。 本範例並非為任何特定工作負載設計或測試。 您可執行範本來建立 SQL Server FCI，並將儲存空間直接存取儲存體連線至網域。 您可評估此範本，並依需要進行修改。

## <a name="before-you-begin"></a>開始之前

在開始之前，您必須了解並準備好一些項目。

### <a name="what-to-know"></a>注意事項
您應了解這些技術的操作方式：

- [Windows 叢集技術](https://docs.microsoft.com/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server 容錯移轉叢集執行個體](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

其中一個需要注意的是，在 Azure IaaS VM 客體容錯移轉叢集上，我們建議每部伺服器 (叢集節點) 使用單一 NIC，以及單一的子網路。 Azure 網路有實體備援，因此 Azure IaaS VM 客體叢集上不需要額外的 NIC 和子網路。 叢集驗證報告會警告您節點只能在單一網路上觸達。 您可在 Azure IaaS VM 客體容錯移轉叢集上忽略此警告。

您也應對這些技術擁有一般程度的了解：

- [在 Windows Server 2016 中使用儲存空間直接存取的超交集解決方案](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview)
- [Azure 資源群組](../../../azure-resource-manager/management/manage-resource-groups-portal.md)

> [!IMPORTANT]
> 目前，只能透過 [SQL Server IaaS 代理程式延伸模組](sql-server-iaas-agent-extension-automate-management.md)的[輕量管理模式](sql-vm-resource-provider-register.md#management-modes)來支援 Azure 虛擬機器上的 SQL Server 容錯移轉叢集執行個體。 若要從完整的延伸模組模式變更為輕量，請刪除對應 VM 的 **SQL 虛擬機器**資源，然後在輕量模式中使用 SQL VM 資源註冊這些 VM。 使用 Azure 入口網站刪除 **SQL 虛擬機器**資源時，請**取消選取正確虛擬機器旁邊的核取方塊**。 完整的延伸模組支援自動化備份、修補和進階入口網站管理等功能。 這些功能在代理程式以輕量管理模式重新安裝後即無法針對 SQL VM 運作。

### <a name="what-to-have"></a>應備項目

完成本文中的步驟之前，您應準備好下列項目：

- Microsoft Azure 訂用帳戶。
- Azure 虛擬機器上的 Windows 網域。
- 具有在 Azure 虛擬機器和 Active Directory 中建立物件權限的帳戶。
- Azure 虛擬網路和子網路且具有足夠 IP 位址空間以容納這些元件：
   - 兩部虛擬機器。
   - 容錯移轉叢集的 IP 位址。
   - 每個 FCI 上一個 IP 位址。
- 在 Azure 網路上設定的 DNS，指向網域控制站。

備妥這些先決條件後，即可繼續建置容錯移轉叢集。 第一步是建立虛擬機器。

## <a name="step-1-create-the-virtual-machines"></a>步驟 1:建立虛擬機器

1. 使用訂用帳戶登入 [Azure 入口網站](https://portal.azure.com)。

1. [建立 Azure 可用性設定組](../../../virtual-machines/linux/tutorial-availability-sets.md)。

   可用性設定組群組虛擬機器遍佈在容錯網域與更新網域中， 其可確保應用程式不受單一失敗點影響 (例如網路交換器或伺服器機架的電源裝置)。

   若尚未建立虛擬機器的資源群組，請在建立 Azure 可用性設定組時建立。 若正在使用 Azure 入口網站建立可用性設定組，請採取這些步驟：

   1. 在 Azure 入口網站中，選取 [建立資源] 來開啟 Azure Marketplace。 搜尋 [可用性設定組]。
   1. 選取 [可用性設定組]。
   1. 選取 [建立]。
   1. 在 [建立可用性設定組] 下方，提供這些值：
      - **Name**：可用性設定組的名稱。
      - 訂用帳戶：您的 Azure 訂用帳戶。
      - **資源群組**：若想要使用現有的群組，請按一下 [選取現有項目]，然後從清單中選取群組。 否則，請選取 [新建] 並輸入群組的名稱。
      - **位置**：設定您計劃建立虛擬機器的位置。
      - **容錯網域**：使用預設 (**3**)。
      - **更新網域**：使用預設 (**5**)。
   1. 選取 [建立] 來建立可用性設定組。

1. 在可用性設定組中建立虛擬機器。

   在 Azure 可用性設定組中佈建兩部 SQL Server 虛擬機器。 如需指示，請參閱[在 Azure 入口網站中佈建 SQL Server 虛擬機器](create-sql-vm-portal.md)。

   將兩部虛擬機器放置於：

   - 與可用性設定組相同的 Azure 資源群組中。
   - 您網域控制站的相同網路上。
   - 有足夠 IP 位址空間存放兩部虛擬機器，與最終會用於此叢集所有 FCI 的子網路上。
   - Azure 可用性設定組中。

      >[!IMPORTANT]
      >建立虛擬機器後，您即無法設定或變更可用性設定組。

   從 Azure Marketplace 中選擇映像。 您可使用包含 Windows Server 與 SQL Server，或只包含 Windows Server 的 Azure Marketplace 映像。 如需詳細資料，請參閱 [Azure 虛擬機器上的 SQL Server 概觀](sql-server-on-azure-vm-iaas-what-is-overview.md)。

   Azure 資源庫中的官方 SQL Server 映像包含已安裝的 SQL Server 執行個體、SQL Server 安裝軟體與所需金鑰。

   根據喜好的付款方式購買 SQL Server 授權，並選擇合適的映像：

   - **依使用量付費的授權**。 費用會以秒計算，且會包含下列 SQL Server 授權：
      - **Windows Server 2016 Datacenter 上的 SQL Server 2016 Enterprise**
      - **Windows Server Datacenter 2016 上的 SQL Server 2016 Standard**
      - **Windows Server Datacenter 2016 上的 SQL Server 2016 Developer**

   - **自備授權 (BYOL)**

      - **(BYOL) Windows Server 2016 Datacenter 上的 SQL Server 2016 Enterprise**
      - **BYOL) Windows Server 2016 Datacenter 上的 SQL Server 2016 Standard**

   >[!IMPORTANT]
   >建立虛擬機器後，請移除預先安裝的獨立 SQL Server 執行個體。 設定容錯移轉叢集與儲存空間直接存取後，您將使用預先安裝的 SQL Server 媒體來建立 SQL Server FCI。

   或者，您可使用只包含作業系統的 Azure Marketplace 映像。 選擇 **Windows Server 2016 Datacenter** 映像，並在設定容錯移轉叢集和儲存空間直接存取後安裝 SQL Server FCI。 此映像不包含 SQL Server 安裝媒體。 將 SQL Server 安裝媒體放在可為每部伺服器執行的位置。

1. 在 Azure 建立虛擬機器後，使用 RDP 連線到每一部虛擬機器。

   當首次透過 RDP 連線到虛擬機器時會出現提示來詢問是否要允許此電腦在網路上可供搜尋。 選取 [是]。

1. 若正在使用其中一個 SQL Sever 型虛擬機器映像，請移除 SQL Server 執行個體。

   1. 在 [程式和功能] 中，以滑鼠右鍵按一下 [Microsoft SQL Server 2016 (64-bit)]，然後選取 [解除安裝/變更]。
   1. 選取 [移除]。
   1. 選取預設執行個體。
   1. 移除所有在 [Database Engine 服務] 下的功能。 請勿移除 [共用功能]。 您會看到類似下列螢幕擷取畫面的內容：

      ![選取功能](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/03-remove-features.png)

   1. 選取 [下一步]，然後選取 [移除]。

1. <a name="ports"></a>開啟防火牆連接埠。

   在每部虛擬機器上，開啟 Windows 防火牆上的下列連接埠：

   | 目的 | TCP 連接埠 | 注意
   | ------ | ------ | ------
   | SQL Server | 1433 | 適用於 SDL Server 預設執行個體的一般連接埠。 若您曾使用來自資源庫的映像，此連接埠會自動開啟。
   | 健全狀況探查 | 59999 | 任何開啟的 TCP 連接埠。 在接下來的步驟中，設定負載平衝器[健全狀況探查](#probe)和要使用此連接埠的叢集。  

1. 將儲存體新增至虛擬機器中。 如需詳細資訊，請參閱[新增儲存區](../../../virtual-machines/linux/disks-types.md)。

   每部虛擬機器需要至少兩個資料磁碟。

   連接原始磁碟，非 NTFS 格式的磁碟。
      >[!NOTE]
      >若附加 NTFS 格式的磁碟，則只能啟用儲存空間直接存取而無法進行磁碟適用性檢查。  

   每部 VM 至少可連接兩個進階 SSD。 建議使用 P30 (1-TB) 或以上的磁碟。

   將主機快取設為**唯讀**。

   在生產環境中使用的儲存體容量是依您的工作負載而定。 本文所描述的值僅供示範與測試之用。

1. [將虛擬機器新增至既存的網域](availability-group-manually-configure-prerequisites-tutorial.md#joinDomain)。

建立並設定虛擬機器後，即可設定容錯移轉叢集。

## <a name="step-2-configure-the-windows-server-failover-cluster-with-storage-spaces-direct"></a>步驟 2:使用儲存空間直接存取設定 Windows Server 容錯移轉叢集

下一步是使用儲存空間直接存取設定容錯移轉叢集。 在此步驟中，您將會完成這些子步驟：

1. 新增 Windows Server 容錯移轉叢集功能。
1. 驗證叢集。
1. 建立容錯移轉叢集。
1. 建立雲端見證。
1. 新增儲存體。

### <a name="add-windows-server-failover-clustering"></a>新增 Windows Server 容錯移轉叢集

1. 使用身為本機系統管理員成員且具備在 Active Directory 中建立物件權限的網域帳戶，以透過 RDP 連線到第一個虛擬機器。 在之後的設定程序中繼續使用此帳戶。

1. [將容錯移轉叢集新增至每部虛擬機器](availability-group-manually-configure-prerequisites-tutorial.md#add-failover-clustering-features-to-both-sql-server-vms)。

   若要從 UI 安裝容錯移轉叢集，請在兩部虛擬機器上採取這些步驟：
   1. 在 [伺服器管理員] 中，選取 [管理]，然後選取 [新增角色和功能]。
   1. 在 [新增角色及功能精靈] 中，依序選取 [下一步] 直到到達 [選取功能]。
   1. 在 [選取功能] 中，選取 [容錯移轉叢集]。 選取所有所需功能與管理工具。 選取 [新增功能]。
   1. 選取 [下一步]，然後選取 [完成] 以安裝功能。

   若要使用 PowerShell 安裝容錯移轉叢集，請在其中一部虛擬機器上從系統管理員 PowerShell 工作階段執行下列指令碼：

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

如需後續步驟的進一步參考，請參閱[在 Windows Server 2016 中使用儲存空間直接存取的超交集解決方案](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-3-configure-storage-spaces-direct)步驟 3 的指示。

### <a name="validate-the-cluster"></a>驗證叢集

在 UI 中或使用 PowerShell 驗證叢集。

若要使用 UI 驗證叢集，請在其中一部虛擬機器上執行下列步驟：

1. 在 [伺服器管理員] 下，選取 [工具]，然後選取 [容錯移轉叢集管理員]。
1. 在 [容錯移轉叢集管理員] 下，選取 [動作]，然後選取 [驗證設定]。
1. 選取 [下一步] 。
1. 在 [選取伺服器或叢集] 下，輸入這兩部虛擬機器的名稱。
1. 在 [測試選項] 下，選取 [僅執行我選取的測試]。 選取 [下一步] 。
1. 在 [測試選取] 下，選取除 [儲存體] 以外的所有測試，如以下所示：

   ![選取叢集驗證測試](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/10-validate-cluster-test.png)

1. 選取 [下一步] 。
1. 在 [確認] 下，選取 [下一步]。

[驗證設定精靈] 會執行驗證測試。

若要使用 PowerShell 驗證叢集，請在其中一部虛擬機器上，從系統管理員 PowerShell 工作階段執行下列指令碼：

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Storage Spaces Direct", "Inventory", "Network", "System Configuration"
   ```

驗證叢集後，請建立容錯移轉叢集。

### <a name="create-the-failover-cluster"></a>建立容錯移轉叢集

若要建立容錯移轉叢集，您需要：
- 將成為叢集節點的虛擬機器名稱。
- 容錯移轉叢集的名稱
- 容錯移轉叢集的 IP 位址。 您可使用並未在叢集節點相同 Azure 虛擬網路和子網路中使用的 IP 位址。

#### <a name="windows-server-2008-through-windows-server-2016"></a>Windows Server 2008 到 Windows Server 2016

下列 PowerShell 指令碼會建立 Windows Server 2008 到 Windows Server 2016 的容錯移轉叢集。 使用節點名稱 (虛擬機器名稱) 和 Azure 虛擬網路的可用 IP 位址來更新指令碼。

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

#### <a name="windows-server-2019"></a>Windows Server 2019

下列 PowerShell 指令碼會建立 Windows Server 2019 的容錯移轉叢集。 如需詳細資訊，請參閱[容錯移轉叢集：叢集網路物件](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97)。 使用節點名稱 (虛擬機器名稱) 和 Azure 虛擬網路的可用 IP 位址來更新指令碼。

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```


### <a name="create-a-cloud-witness"></a>建立雲端見證

雲端見證儲存在 Azure 儲存體 blob 中，是一種新型的叢集仲裁見證。 若有雲端見證，即不再需要使用獨立的 VM 來裝載見證共用。

1. [建立容錯移轉叢集的雲端見證](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness)。

1. 建立 Blob 容器。

1. 儲存存取金鑰和容器 URL。

1. 設定容錯移轉叢集的叢集仲裁見證。 請參閱[在使用者介面中設定仲裁見證](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness)。

### <a name="add-storage"></a>新增儲存體

儲存空間直接存取的磁碟必須是空的。 其不能包含磁碟分割或其他資料。 若要清理磁碟，請遵循[本指南中的步驟](https://docs.microsoft.com/windows-server/storage/storage-spaces/deploy-storage-spaces-direct?redirectedfrom=MSDN#step-31-clean-drives)。

1. [啟用儲存空間直接存取](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-35-enable-storage-spaces-direct)。

   下列 PowerShell 指令碼會啟用儲存空間直接存取：  

   ```powershell
   Enable-ClusterS2D
   ```

   現在，您可以在**容錯移轉管理員**中看到儲存集區。

1. [建立磁碟區](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-36-create-volumes)。

   儲存空間直接存取會在啟用時自動建立儲存集區。 您現在可開始建立磁碟區。 PowerShell Cmdlet `New-Volume` 會自動化磁碟區的建立流程。 此程序包括格式化、將磁碟區新增到叢集，以及建立叢集共用磁碟區 (CSV)。 本範例會建立 800 GB 的 CSV：

   ```powershell
   New-Volume -StoragePoolFriendlyName S2D* -FriendlyName VDisk01 -FileSystem CSVFS_REFS -Size 800GB
   ```   

   完成此命令後，系統會將 800 GB 的磁碟區掛接為叢集資源。 磁碟區會位於 `C:\ClusterStorage\Volume1\`。

   這個螢幕擷取畫面顯示具有儲存空間直接存取的叢集共用磁碟區：

   ![叢集共用磁碟區](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/15-cluster-shared-volume.png)

## <a name="step-3-test-failover-cluster-failover"></a>步驟 3：測試容錯移轉叢集的容錯移轉

在 [容錯移轉叢集管理員] 中，請確認可將儲存體資源移至其他叢集節點。 如果可透過 [容錯移轉叢集管理員] 連線到容錯移轉叢集，且能將儲存體從一個節點移到另一個，則可設定 FCI。

## <a name="step-4-create-the-sql-server-fci"></a>步驟 4：建立 SQL Server FCI

在設定容錯移轉叢集和所有叢集元件 (包括儲存體) 後，即可建立 SQL Server FCI。

1. 使用 RDP 連線到第一部虛擬機器。

1. 在 [容錯移轉叢集管理員] 中，確認所有核心叢集資源都位於第一部虛擬機器。 如有必要，請將所有資源移至該虛擬機器。

1. 找出安裝媒體。 若虛擬機器是使用其中一個 Azure Marketplace 映像，則媒體會位於 `C:\SQLServer_<version number>_Full`。 選取 [設定]。

1. 在 [SQL Server 安裝中心] 中，選取 [安裝]。

1. 選取 [安裝新的 SQL Server 容錯移轉叢集]。 請遵循精靈內的指示安裝 SQL Server FCI。

   FCI 資料目錄必須位於叢集儲存體中。 與儲存空間直接存取搭配使用時，FCI 會作為每部伺服器上的磁碟區掛接點，而非共用磁碟。 儲存空間直接存取會在節點之間同步磁碟區。 磁碟區會作為叢集共用磁碟區向叢集呈現。 在資料目錄上使用 CSV 掛接點。

   ![資料目錄](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/20-data-dicrectories.png)

1. 完成精靈中的指示後，安裝程式會在第一個節點上安裝 SQL Server FCI。

1. 安裝程式在第一個節點上安裝 FCI 後，請使用 RDP 連線到第二個節點。

1. 開啟 [SQL Server 安裝中心]。 選取 [安裝類型]。

1. 選取 [將節點新增到 SQL Server 容錯移轉叢集]。 遵循精靈中的指示來安裝 SQL Server，並將伺服器新增到 FCI。

   >[!NOTE]
   >若曾使用過包含 SQL Server 的 Azure Marketplace 資源庫映像，則映像會包含 SQL Server 工具。 若沒有用過其中一個映像，請另外安裝 SQL Server 工具。 請參閱[下載 Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)。

## <a name="step-5-create-the-azure-load-balancer"></a>步驟 5：建立 Azure Load Balancer

在 Azure 虛擬機器上，叢集會使用負載平衡器來保留每次均需在一個節點上的 IP 位址。 在此解決方案中，負載平衡器會保留 SQL Server FCI 的 IP 位址。

如需詳細資訊，請參閱[建立及設定 Azure Load Balancer](availability-group-manually-configure-tutorial.md#configure-internal-load-balancer)。

### <a name="create-the-load-balancer-in-the-azure-portal"></a>在 Azure 入口網站中建立負載平衡器

若要建立負載平衡器：

1. 在 Azure 入口網站中，前往包含虛擬機器的資源群組。

1. 選取 [新增]。 在 Azure Marketplace 內搜尋 **oad Balancer**。 選取 [Load Balancer]。

1. 選取 [建立]。

1. 使用下列項目設定負載平衡器：

   - 訂用帳戶：您的 Azure 訂用帳戶。
   - **資源群組**：包含虛擬機器的資源群組。
   - **Name**：能識別負載平衡器的名稱。
   - **區域**：包含虛擬機器的 Azure 位置。
   - **類型**：公用或私人。 私人負載平衡器可從相同的虛擬網路內存取。 大部分的 Azure 應用程式都能使用私人負載平衡器。 若應用程式需要直接透過網際網路存取 SQL Server，請使用公用負載平衡器。
   - **SKU**：標準。
   - **虛擬網路**：與虛擬機器相同的網路。
   - **IP 位址指派**：靜態。 
   - **私人 IP 位址**：您指派給 SQL Server FCI 叢集網路資源的 IP 位址。

 下列螢幕擷取畫面顯示 [建立負載平衡器] UI：

   ![設定負載平衡器](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/30-load-balancer-create.png)

### <a name="configure-the-load-balancer-backend-pool"></a>設定負載平衡器後端集區

1. 返回包含虛擬機器的 Azure 資源群組，並找到新的負載平衡器。 您可能需要在資源群組上重新整理檢視。 選取負載平衡器。

1. 選取 [後端集區]，然後選取 [新增]。

1. 將後端集區關聯至包含 VM 的可用性設定組。

1. 在 [目標網路 IP 設定] 下方，選取 [虛擬機器]，並選擇將作為叢集節點加入的虛擬機器。 請務必包含將裝載 FCI 的所有虛擬機器。

1. 選取 [確定] 以建立後端集區。

### <a name="configure-a-load-balancer-health-probe"></a>設定負載平衡器健全狀況探查

1. 在 [負載平衡器] 刀鋒視窗上，選取 [健康狀態探查]。

1. 選取 [新增]。

1. 在 [新增健康狀態探查] 刀鋒視窗上，<a name="probe"></a>設定健康狀態探查參數。

   - **Name**：健康情況探查的名稱。
   - **通訊協定**：TCP。
   - **連接埠**：設為在[此步驟](#ports)中為健康狀態探查於防火牆內建立的連接埠。 在本文中，範例使用 TCP 連接埠 `59999`。
   - **間隔**：5 秒。
   - **狀況不良閾值**：2 次連續失敗。

1. 選取 [確定]。

### <a name="set-load-balancing-rules"></a>設定負載平衡規則

1. 在 [負載平衡器] 刀鋒視窗中，選取 [負載平衡規則]。

1. 選取 [新增]。

1. 設定負載平衡規則參數：

   - **Name**：負載平衡規則的名稱。
   - **前端 IP 位址**：SQL Server FCI 叢集網路資源的 IP 位址。
   - **連接埠**：SQL Server FCI TCP 連接埠。 預設執行個體連接埠為 1433。
   - **後端連接埠**：使用與啟用 [浮動 IP (伺服器直接回傳)] 時與 [連接埠] 相同的值。
   - **後端集區**：先前設定的後端集區名稱。
   - **健康情況探查**：先前設定的健康狀態探查。
   - **工作階段持續性**：無。
   - **閒置逾時 (分鐘)** ：4.
   - **浮動 IP (伺服器直接回傳)** ：已啟用。

1. 選取 [確定]。

## <a name="step-6-configure-the-cluster-for-the-probe"></a>步驟 6：設定探查的叢集

設定 PowerShell 中的叢集探查連接埠參數。

若要設定叢集探查連接埠參數，請將下列指令碼中變數更新為環境中的值。 從指令碼移除角括弧 (`<` 和 `>`)。

   ```powershell
   $ClusterNetworkName = "<Cluster Network Name>"
   $IPResourceName = "<SQL Server FCI IP Address Resource Name>" 
   $ILBIP = "<n.n.n.n>" 
   [int]$ProbePort = <nnnnn>

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

下列清單描述需要更新的值：

   - `<Cluster Network Name>`:網路的 Windows Server 容錯移轉叢集名稱。 在 [容錯移轉叢集管理員] > [網路] 中，以滑鼠右鍵按一下網路，然後選取 [屬性]。 正確的值在 [一般] 索引標籤的 [名稱] 底下。

   - `<SQL Server FCI IP Address Resource Name>`:SQL Server FCI IP 位址資源名稱。 在 [容錯移轉叢集管理員] > [角色] 中，於 SQL Server FCI 角色下方的 [伺服器名稱] 下，以滑鼠右鍵按一下 IP 位址資源，然後選取 [屬性]。 正確的值在 [一般] 索引標籤的 [名稱] 底下。 

   - `<ILBIP>`:ILB IP 位址。 此位址在 Azure 入口網站中會設定為 ILB 前端位址。 這也是 SQL Server FCI IP 位址。 您可以在 [容錯移轉叢集管理員] 中，您找到 `<SQL Server FCI IP Address Resource Name>` 所在位置的相同內容頁面上找到該位址。  

   - `<nnnnn>`:您在負載平衡器健康狀態探查中設定的探查連接埠。 任何未使用的 TCP 連接埠都有效。

>[!IMPORTANT]
>叢集參數的子網路遮罩必須是 TCP IP 廣播位址：`255.255.255.255`。

設定叢集探查之後，即可在 PowerShell 中查看所有叢集參數。 執行此指令碼︰

   ```powershell
   Get-ClusterResource $IPResourceName | Get-ClusterParameter 
  ```

## <a name="step-7-test-fci-failover"></a>步驟 7：測試 FCI 容錯移轉

測試 FCI 的容錯移轉來驗證叢集功能。 請執行下列步驟：

1. 使用 RDP 連線至其中一個 SQL Server FCI 叢集節點。

1. 開啟 [容錯移轉叢集管理員]。 選取 [角色]。 請注意哪個節點擁有 SQL Server FCI 角色。

1. 以滑鼠右鍵按一下 SQL Server FCI 角色。

1. 選取 [移動]，然後選取 [最佳可能節點]。

[容錯移轉叢集管理員] 會顯示角色，其資源會進入離線狀態。 接著資源會移動，並在另一個節點上線。

### <a name="test-connectivity"></a>測試連線能力

若要測試連線能力，請登入相同虛擬網路中的另一部虛擬機器。 開啟 [SQL Server Management Studio]，然後連接到 SQL Server FCI 名稱。

>[!NOTE]
>若有需要，您可[下載 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)。

## <a name="limitations"></a>限制

Azure 虛擬機器支援 Windows Server 2019 上的 Microsoft 分散式交易協調器 (MSDTC)，且可使用叢集共用磁碟區 (CSV) 上的儲存體和 [Standard Load Balancer](../../../load-balancer/load-balancer-standard-overview.md)。

在 Azure 虛擬機器上，Windows Server 2016 或更早版本無法支援 MSDTC，因為：

- 叢集 MSDTC 資源無法設為使用共用儲存體。 若在 Windows Server 2016 上建立 MSDTC 資源，則即使有儲存體存在，系統也不會顯示任何可用的共用儲存體。 Windows Server 2019 中已修正此問題。
- 基本負載平衡器不會處理 RPC 連接埠。

## <a name="see-also"></a>另請參閱

[使用遠端桌面設定儲存空間直接存取 (Azure)](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-storage-spaces-direct-deployment)

[使用儲存空間直接存取的超交集解決方案](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct)

[儲存空間直接存取概觀](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview)

[儲存空間直接存取的 SQL Server 支援](https://blogs.technet.microsoft.com/dataplatforminsider/2016/09/27/sql-server-2016-now-supports-windows-server-2016-storage-spaces-direct/)
