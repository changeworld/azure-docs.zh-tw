---
title: SQL Server FCI - Azure 虛擬機器 | Microsoft Docs
description: 本文介紹如何在 Azure 虛擬機器上創建 SQL Server 容錯移轉叢集實例。
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
ms.openlocfilehash: 20c231e4f3052797eac79a3c97a3d8148690b8c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249798"
---
# <a name="configure-a-sql-server-failover-cluster-instance-on-azure-virtual-machines"></a>在 Azure 虛擬機器上配置 SQL Server 容錯移轉叢集實例

本文介紹如何在 Azure 資源管理器模型中的 Azure 虛擬機器上創建 SQL Server 容錯移轉叢集實例 （FCI）。 此解決方案使用[Windows Server 2016 資料中心版存儲空間直接](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview)作為基於軟體的虛擬 SAN，用於同步 Windows 群集中的節點 （Azure VM） 之間的存儲（資料磁片）。 存儲空間直接是 Windows 伺服器 2016 中的新功能。

下圖顯示 Azure 虛擬機器中的完整解決方案：

![完整解決方案](./media/virtual-machines-windows-portal-sql-create-failover-cluster/00-sql-fci-s2d-complete-solution.png)

此圖顯示：

- Windows 伺服器容錯移轉叢集中的兩個 Azure 虛擬機器。 當虛擬機器處於容錯移轉叢集中時，它也稱為*叢集節點*或*節點*。
- 每部虛擬機器有兩個以上的資料磁碟。
- 存儲空間直接同步資料磁片上的資料，並將同步存儲作為存儲池呈現。
- 存儲池向容錯移轉叢集顯示群集共用卷 （CSV）。
- SQL Server FCI 叢集角色會針對資料硬碟使用 CSV。
- 保留 SQL Server FCI IP 位址的 Azure Load Balancer。
- 保留所有資源的 Azure 可用性設定組。

>[!NOTE]
>關係圖中的所有 Azure 資源都位於同一資源組中。

有關存儲空間直接的詳細資訊，請參閱[Windows 伺服器 2016 資料中心版存儲空間直接](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview)。

存儲空間直接支援兩種類型的體系結構：融合和超融合。 本文件中的架構為超交集。 超交集基礎結構會將儲存體放置於與裝載叢集應用程式相同的伺服器上。 在此架構中，儲存體會放置在每個 SQL Server FCI 節點上。

## <a name="licensing-and-pricing"></a>授權和定價

在 Azure 虛擬機器上，可以使用即用即付 （PAYG） 或自帶許可證 （BYOL） VM 映射對 SQL Server 進行許可證。 您選擇的圖像類型會影響您的充電方式。

通過即付即用許可，Azure 虛擬機器上的 SQL Server 容錯移轉叢集實例 （FCI） 會為 FCI 的所有節點（包括被動節點）產生費用。 如需詳細資訊，請參閱 [SQL Server Enterprise 虛擬機器定價](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/)。

如果您有與軟體保證的企業協定，則可以為每個活動節點使用一個免費被動 FCI 節點。 要利用 Azure 中的此優勢，請使用 BYOL VM 映射，並在 FCI 的主動節點和被動節點上使用相同的許可證。 如需詳細資訊，請參閱 [Enterprise 合約](https://www.microsoft.com/Licensing/licensing-programs/enterprise.aspx)。

要比較 Azure 虛擬機器上 SQL Server 的即付即付和 BYOL 許可，請參閱[使用 SQL VM 入門](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms)。

如需授權 SQL Server 的完整資訊，請參閱[定價](https://www.microsoft.com/sql-server/sql-server-2017-pricing)。

### <a name="example-azure-template"></a>Azure 範本範例

您可以在 Azure 中從範本創建整個解決方案。 GitHub [Azure 快速入門範本](https://github.com/MSBrett/azure-quickstart-templates/tree/master/sql-server-2016-fci-existing-vnet-and-ad) 內有提供範本範例。 此示例未針對任何特定工作負載進行設計或測試。 您可以運行該範本以創建 SQL Server FCI，其中存儲空間直接存儲連接到您的域。 您可以評估範本並出於您的目的對其進行修改。

## <a name="before-you-begin"></a>開始之前

在開始之前，您需要瞭解並具備一些功能。

### <a name="what-to-know"></a>注意事項
您應該對這些技術有操作性瞭解：

- [Windows 叢集技術](https://docs.microsoft.com/windows-server/failover-clustering/failover-clustering-overview)
- [SQL 伺服器容錯移轉叢集實例](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

需要注意的是，在 Azure IaaS VM 來賓容錯移轉叢集上，我們建議每個伺服器（叢集節點）和單個子網使用單個 NIC。 Azure 網路具有物理冗余，這使得 Azure IaaS VM 來賓群集上不需要額外的 NIC 和子網。 群集驗證報告將警告您，這些節點只能在單個網路上訪問。 您可以在 Azure IaaS VM 來賓容錯移轉叢集上忽略此警告。

您還應對這些技術有一般瞭解：

- [在 Windows 伺服器 2016 中使用存儲空間直接的超融合解決方案](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview)
- [Azure 資源群組](../../../azure-resource-manager/management/manage-resource-groups-portal.md)

> [!IMPORTANT]
> 此時，Azure 虛擬機器上的 SQL Server 容錯移轉叢集實例僅支援[SQL Server IaaS 代理擴展](virtual-machines-windows-sql-server-agent-extension.md)的[羽量級管理模式](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes)。 要從完全擴展模式更改為羽量級，請刪除相應 VM 的**SQL 虛擬機器**資源，然後在羽量級模式下將其註冊到 SQL VM 資來源提供者。 使用 Azure 門戶刪除**SQL 虛擬機器**資源時，**請清除正確的虛擬機器 旁邊的核取方塊**。 完整擴展支援自動備份、修補和高級門戶管理等功能。 在輕量管理模式下重新安裝代理後，這些功能不適用於 SQL VM。

### <a name="what-to-have"></a>應備項目

在完成本文中的步驟之前，您應該已經具備：

- Microsoft Azure 訂用帳戶。
- Azure 虛擬機器上的 Windows 網域。
- 具有在 Azure 虛擬機器和活動目錄中創建物件的許可權的帳戶。
- 具有足夠 IP 位址空間的 Azure 虛擬網路和子網，這些元件：
   - 兩部虛擬機器。
   - 容錯移轉叢集的 IP 位址。
   - 每個 FCI 上一個 IP 位址。
- 在 Azure 網路上配置的 DNS，指向網域控制站。

使用這些先決條件後，您可以開始構建容錯移轉叢集。 第一步是建立虛擬機器。

## <a name="step-1-create-the-virtual-machines"></a>第 1 步：創建虛擬機器

1. 使用訂閱登錄到[Azure 門戶](https://portal.azure.com)。

1. [建立 Azure 可用性設定組](../tutorial-availability-sets.md)。

   可用性設定組群組虛擬機器遍佈在容錯網域與更新網域中， 它可確保應用程式不受單點故障的影響，例如網路交換器或伺服器機架的電源單元。

   如果尚未為虛擬機器創建資源組，請在創建 Azure 可用性集時執行此操作。 如果使用 Azure 門戶創建可用性集，請執行以下步驟：

   1. 在 Azure 門戶中，選擇 **"創建資源**以打開 Azure 應用商店"。 搜尋 [可用性設定組]****。
   1. 選擇**可用性集**。
   1. 選取 [建立]****。
   1. 在 **"創建可用性集"** 下，提供以下值：
      - **名稱**：可用性設定組的名稱。
      - **訂閱**：Azure 訂閱。
      - **資源組**：如果要使用現有組，請按一下"**選擇現有"，** 然後從清單中選擇該組。 否則，選擇 **"創建新"** 並輸入組的名稱。
      - **位置**︰設定您計劃建立虛擬機器的位置。
      - **容錯域**： 使用預設值 （**3**）。
      - **更新域**： 使用預設值 （**5**）。
   1. 選擇 **"創建**"以創建可用性集。

1. 在可用性設定組中建立虛擬機器。

   在 Azure 可用性設定組中佈建兩部 SQL Server 虛擬機器。 如需指示，請參閱[在 Azure 入口網站中佈建 SQL Server 虛擬機器](virtual-machines-windows-portal-sql-server-provision.md)。

   將兩部虛擬機器放置於：

   - 與可用性集位於同一 Azure 資源組中。
   - 您網域控制站的相同網路上。
   - 在子網上，該子網為虛擬機器和最終可能在群集上使用的所有 FCI 具有足夠的 IP 位址空間。
   - Azure 可用性設定組中。

      >[!IMPORTANT]
      >創建虛擬機器後，無法設置或更改可用性集。

   從 Azure 應用商店中選擇映射。 可以使用包含 Windows 伺服器和 SQL 伺服器的 Azure 應用商店映射，也可以使用僅包含 Windows 伺服器的 Azure 應用商店映射。 有關詳細資訊，請參閱[Azure 虛擬機器上的 SQL Server 概述](virtual-machines-windows-sql-server-iaas-overview.md)。

   Azure 庫中的官方 SQL Server 映射包括已安裝的 SQL Server 實例、SQL Server 安裝軟體和所需的金鑰。

   根據您要為 SQL Server 許可證付費的方式選擇正確的映射：

   - **按使用量付費許可**。 費用會以秒計算，且會包含下列 SQL Server 授權：
      - **SQL 伺服器 2016 企業視窗伺服器 2016 資料中心**
      - **SQL 伺服器 2016 標準在 Windows 伺服器 2016 資料中心**
      - **SQL 伺服器 2016 Windows 伺服器上的開發人員 2016 資料中心**

   - **自帶許可證 （BYOL）**

      - **（BYOL）SQL 伺服器 2016 企業視窗伺服器 2016 資料中心**
      - **（BYOL）SQL 伺服器 2016 標準在 Windows 伺服器 2016 資料中心**

   >[!IMPORTANT]
   >建立虛擬機器後，請移除預先安裝的獨立 SQL Server 執行個體。 設置容錯移轉叢集和存儲空間直接後，將使用預先安裝的 SQL Server 媒體創建 SQL Server FCI。

   或者，您可以使用僅包含作業系統的 Azure 應用商店映射。 選擇**Windows 伺服器 2016 資料中心**映射，並在設置容錯移轉叢集和存儲空間直接後安裝 SQL Server FCI。 此映射不包含 SQL Server 安裝介質。 將 SQL Server 安裝介質放置在可以為每個伺服器運行它的位置。

1. Azure 創建虛擬機器後，請使用 RDP 連接到每個虛擬機器。

   首次使用 RDP 連接到虛擬機器時，會提示您是否希望允許 PC 在網路上被發現。 選取 [是]****。

1. 如果使用基於 SQL Server 的虛擬機器映射之一，請刪除 SQL Server 實例。

   1. 在**程式和功能**中，按右鍵**Microsoft SQL Server 2016（64 位），** 然後選擇 **"卸載/更改**"。
   1. 選取 [移除]****。
   1. 選取預設執行個體。
   1. 移除所有在 [Database Engine 服務]**** 下的功能。 不要刪除**共用功能**。 您將看到以下螢幕截圖：

      ![選取功能](./media/virtual-machines-windows-portal-sql-create-failover-cluster/03-remove-features.png)

   1. 選擇 **"下一步**"，然後選擇 **"刪除**"。

1. <a name="ports"></a>開啟防火牆連接埠。

   在每個虛擬機器上，打開 Windows 防火牆上的這些埠：

   | 目的 | TCP 連接埠 | 注意
   | ------ | ------ | ------
   | SQL Server | 1433 | 適用於 SDL Server 預設執行個體的一般連接埠。 若您曾使用來自資源庫的映像，此連接埠會自動開啟。
   | 健全狀況探查 | 59999 | 任何開啟的 TCP 連接埠。 在接下來的步驟中，設定負載平衝器[健全狀況探查](#probe)和要使用此連接埠的叢集。  

1. 將儲存體新增至虛擬機器中。 如需詳細資訊，請參閱[新增儲存區](../disks-types.md)。

   每部虛擬機器需要至少兩個資料磁碟。

   附加原始磁片，而不是 NTFS 格式化的磁片。
      >[!NOTE]
      >如果附加了 NTFS 格式化的磁片，則只能在不檢查磁片資格的情況下啟用存儲空間直接。  

   每部 VM 至少可連接兩個進階 SSD。 我們建議至少使用 P30 （1-TB） 磁片。

   將主機快取設為**唯讀**。

   在生產環境中使用的儲存體容量是依您的工作負載而定。 本文所描述的值僅供示範與測試之用。

1. [將虛擬機器新增至既存的網域](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain)。

創建和配置虛擬機器後，可以設置容錯移轉叢集。

## <a name="step-2-configure-the-windows-server-failover-cluster-with-storage-spaces-direct"></a>第 2 步：使用存儲空間直接配置 Windows 伺服器容錯移轉叢集

下一步是使用存儲空間直接配置容錯移轉叢集。 在此步驟中，您將完成以下子步驟：

1. 添加 Windows 伺服器容錯移轉叢集功能。
1. 驗證群集。
1. 建立容錯移轉叢集。
1. 創建雲見證。
1. 添加存儲。

### <a name="add-windows-server-failover-clustering"></a>添加 Windows 伺服器容錯移轉叢集

1. 通過使用本地管理員的成員並有權在 Active Directory 中創建物件的域帳戶連接到具有 RDP 的第一個虛擬機器。 在之後的設定程序中繼續使用此帳戶。

1. [將容錯移轉叢集添加到每個虛擬機器](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms)。

   要從 UI 安裝容錯移轉叢集，請在兩個虛擬機器上執行以下步驟：
   1. 在**伺服器管理員**中，選擇 **"管理**"，然後選擇 **"添加角色和功能**"。
   1. 在 **"添加角色和要素"嚮導**中，選擇 **"下一步**"，直到開始**選擇要素**。
   1. 在 **"選擇要素"** 中，選擇**容錯移轉叢集**。 選取所有所需功能與管理工具。 選擇 **"添加要素**"。
   1. 選擇 **"下一步**"，然後選擇 **"完成"** 以安裝功能。

   要使用 PowerShell 安裝容錯移轉叢集，請在其中一個虛擬機器上的管理員 PowerShell 會話中運行以下腳本：

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

有關後續步驟的進一步參考，請參閱[在 Windows Server 2016 中使用存儲空間直接的超融合解決方案](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-3-configure-storage-spaces-direct)步驟 3 下的說明。

### <a name="validate-the-cluster"></a>驗證叢集

在 UI 中或使用 PowerShell 驗證群集。

要使用 UI 驗證群集，請對其中一個虛擬機器執行以下步驟：

1. 在 **"伺服器管理員**"下，選擇 **"工具**"，然後選擇**容錯移轉叢集管理器**。
1. 在**容錯移轉叢集管理器**下，選擇**操作**，然後選擇 **"驗證配置**"。
1. 選取 [下一步]****。
1. 在 **"選擇伺服器"或"群集**"下，輸入兩個虛擬機器的名稱。
1. 在 **"測試"選項**下，選擇 **"僅運行"我選擇的測試**。 選取 [下一步]****。
1. 在 **"測試選擇**"下，選擇**除存儲**之外的所有測試，如下所示：

   ![選擇群集驗證測試](./media/virtual-machines-windows-portal-sql-create-failover-cluster/10-validate-cluster-test.png)

1. 選取 [下一步]****。
1. 在 **"確認"** 下，選擇 **"下一步**"。

[驗證設定精靈] 會執行驗證測試。

要使用 PowerShell 驗證群集，請從其中一個虛擬機器上的管理員 PowerShell 會話運行以下腳本：

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Storage Spaces Direct", "Inventory", "Network", "System Configuration"
   ```

驗證叢集後，請建立容錯移轉叢集。

### <a name="create-the-failover-cluster"></a>建立容錯移轉叢集

若要建立容錯移轉叢集，您需要：
- 將成為叢集節點的虛擬機器的名稱。
- 容錯移轉叢集的名稱
- 容錯移轉叢集的 IP 位址。 可以使用與叢集節點相同的 Azure 虛擬網路和子網上未使用的 IP 位址。

#### <a name="windows-server-2008-through-windows-server-2016"></a>Windows 伺服器 2008 通過 Windows 伺服器 2016

以下 PowerShell 腳本通過 Windows Server 2016 為 Windows 伺服器 2008 創建容錯移轉叢集。 使用節點的名稱（虛擬機器名稱）和 Azure 虛擬網路中的可用 IP 位址更新腳本。

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

#### <a name="windows-server-2019"></a>Windows Server 2019

以下 PowerShell 腳本為 Windows Server 2019 創建容錯移轉叢集。 有關詳細資訊，請參閱[容錯移轉叢集：群集網路物件](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97)。 使用節點的名稱（虛擬機器名稱）和 Azure 虛擬網路中的可用 IP 位址更新腳本。

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```


### <a name="create-a-cloud-witness"></a>建立雲端見證

雲見證是存儲在 Azure 存儲 Blob 中的一種新型群集仲裁見證。 這樣就不需要託管見證共用的單獨 VM。

1. [建立容錯移轉叢集的雲端見證](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness)。

1. 建立 Blob 容器。

1. 儲存存取金鑰和容器 URL。

1. 設定容錯移轉叢集的叢集仲裁見證。 請參閱[在使用者介面中配置仲裁見證](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness)。

### <a name="add-storage"></a>新增儲存體

存儲空間直接磁片需要為空。 它們不能包含分區或其他資料。 要清理磁片，請按照[本指南中的步驟](https://docs.microsoft.com/windows-server/storage/storage-spaces/deploy-storage-spaces-direct?redirectedfrom=MSDN#step-31-clean-drives)操作。

1. [啟用存儲空間直接](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-35-enable-storage-spaces-direct)。

   以下 PowerShell 腳本支援存儲空間直接：  

   ```powershell
   Enable-ClusterS2D
   ```

   現在，您可以在**容錯移轉管理員**中看到儲存集區。

1. [創建卷](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-36-create-volumes)。

   存儲空間直接在啟用存儲池時自動創建存儲池。 現在，您可以創建卷。 PowerShell Cmdlet`New-Volume`可自動執行卷創建過程。 此過程包括格式化、將卷添加到群集以及創建群集共用卷 （CSV）。 此示例創建一個 800 GB （GB） CSV：

   ```powershell
   New-Volume -StoragePoolFriendlyName S2D* -FriendlyName VDisk01 -FileSystem CSVFS_REFS -Size 800GB
   ```   

   完成此命令後，將裝載 800 GB 卷作為群集資源。 磁碟區會位於 `C:\ClusterStorage\Volume1\`。

   此螢幕截圖顯示具有存儲空間直接的群集共用卷：

   ![叢集共用磁碟區](./media/virtual-machines-windows-portal-sql-create-failover-cluster/15-cluster-shared-volume.png)

## <a name="step-3-test-failover-cluster-failover"></a>步驟 3︰測試容錯移轉叢集的容錯移轉

在**容錯移轉叢集管理器中**，驗證是否可以將存儲資源移動到其他叢集節點。 如果可以使用**容錯移轉叢集管理器**連接到容錯移轉叢集，並將存儲從一個節點移動到另一個節點，則可以配置 FCI。

## <a name="step-4-create-the-sql-server-fci"></a>第 4 步：創建 SQL 伺服器 FCI

配置容錯移轉叢集和所有群集元件（包括存儲）後，可以創建 SQL Server FCI。

1. 使用 RDP 連接到第一個虛擬機器。

1. 在**容錯移轉叢集管理器**中，確保所有核心群集資源都位於第一個虛擬機器上。 如有必要，將所有資源移動到該虛擬機器。

1. 找出安裝媒體。 若虛擬機器是使用其中一個 Azure Marketplace 映像，則媒體會位於 `C:\SQLServer_<version number>_Full`。 選取 [設定]****。

1. 在**SQL 伺服器安裝中心**中，選擇 **"安裝**"。

1. 選擇**新的 SQL Server 容錯移轉叢集安裝**。 請遵循精靈內的指示安裝 SQL Server FCI。

   FCI 資料目錄必須位於叢集儲存體中。 使用存儲空間直接，它不是共用磁片，而是每個伺服器上的卷裝載點。 存儲空間直接同步兩個節點之間的卷。 卷以群集共用卷的形式呈現給群集。 在資料目錄上使用 CSV 掛接點。

   ![資料目錄](./media/virtual-machines-windows-portal-sql-create-failover-cluster/20-data-dicrectories.png)

1. 完成嚮導中的說明後，安裝程式將在第一個節點上安裝 SQL Server FCI。

1. 安裝程式在第一個節點上安裝 FCI 後，使用 RDP 連接到第二個節點。

1. 開啟 [SQL Server 安裝中心]****。 選擇**安裝**。

1. 選擇**將節點添加到 SQL Server 容錯移轉叢集**。 按照嚮導中的說明安裝 SQL Server 並將伺服器添加到 FCI。

   >[!NOTE]
   >如果使用包含 SQL Server 的 Azure 應用商店庫映射，則映射中包含 SQL Server 工具。 如果不使用這些映射之一，請單獨安裝 SQL Server 工具。 請參閱[下載 Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)。

## <a name="step-5-create-the-azure-load-balancer"></a>第 5 步：創建 Azure 負載等化器

在 Azure 虛擬機器上，叢集會使用負載平衡器來保留每次均需在一個節點上的 IP 位址。 在此解決方案中，負載平衡器會保留 SQL Server FCI 的 IP 位址。

有關詳細資訊，請參閱[創建和配置 Azure 負載等化器](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer)。

### <a name="create-the-load-balancer-in-the-azure-portal"></a>在 Azure 入口網站中建立負載平衡器

若要建立負載平衡器：

1. 在 Azure 門戶中，轉到包含虛擬機器的資源組。

1. 選取 [加入]****。 在 Azure 應用商店中搜索**負載等化器**。 選擇**負載平衡器**。

1. 選取 [建立]****。

1. 使用下列項目設定負載平衡器：

   - **訂閱**：Azure 訂閱。
   - **資源組**：包含虛擬機器的資源組。
   - **名稱**︰用以識別負載平衡器的名稱。
   - **區域**：包含虛擬機器的 Azure 位置。
   - **類型**：公共或私有。 可以從虛擬網路內訪問專用負載等化器。 大部分的 Azure 應用程式都能使用私人負載平衡器。 如果應用程式需要直接通過互聯網訪問 SQL Server，請使用公共負載等化器。
   - **SKU**： 標準.
   - **虛擬網路**：與虛擬機器相同的網路。
   - **IP 位址分配**：靜態。 
   - **私人 IP 位址**：分配給 SQL Server FCI 群集網路資源的 IP 位址。

 以下螢幕截圖顯示了**創建負載等化器**UI：

   ![設置負載等化器](./media/virtual-machines-windows-portal-sql-create-failover-cluster/30-load-balancer-create.png)

### <a name="configure-the-load-balancer-backend-pool"></a>設定負載平衡器後端集區

1. 返回到包含虛擬機器的 Azure 資源組並查找新的負載等化器。 您可能需要刷新資源組的視圖。 選取負載平衡器。

1. 選擇 **"後端池**"，然後選擇 **"添加**"。

1. 將後端集區關聯至包含 VM 的可用性設定組。

1. 在**目標網路 IP 配置**下，選擇 **"虛擬電腦"** 並選擇將作為叢集節點參與的虛擬機器。 請務必包含將裝載 FCI 的所有虛擬機器。

1. 選擇 **"確定"** 以創建後端池。

### <a name="configure-a-load-balancer-health-probe"></a>設定負載平衡器健全狀況探查

1. 在負載等化器刀片上，選擇**運行狀況探頭**。

1. 選取 [加入]****。

1. 在 **"添加運行狀況探測"** 邊<a name="probe"></a>欄選項卡上，設置運行狀況探測參數。

   - **名稱**：健全狀況探查的名稱。
   - **協定**：TCP。
   - **埠**：設置為[您在防火牆](#ports)中為運行狀況探測在此步驟中創建的埠。 在本文中，該示例使用 TCP 埠`59999`。
   - **間隔**：5 秒。
   - **狀況不良臨界值**：2 次連續失敗。

1. 選取 [確定]****。

### <a name="set-load-balancing-rules"></a>設定負載平衡規則

1. 在負載平衡器邊欄選項卡上，選擇**負載平衡規則**。

1. 選取 [加入]****。

1. 設置負載平衡規則參數：

   - **名稱**︰負載平衡規則的名稱。
   - **前端 IP 位址**：SQL Server FCI 群集網路資源的 IP 位址。
   - **埠**：SQL 伺服器 FCI TCP 埠。 預設執行個體連接埠為 1433。
   - **後端埠**：啟用**浮動 IP（直接伺服器返回）** 時，使用與埠值相同的**埠**。
   - **後端池**：您之前配置的後端池名稱。
   - **運行狀況探測**：您之前配置的運行狀況探測。
   - **工作階段持續性**：無。
   - **閒置逾時 (分鐘)**：4。
   - **浮動 IP（直接伺服器返回）：** 已啟用。

1. 選取 [確定]****。

## <a name="step-6-configure-the-cluster-for-the-probe"></a>步驟 6：為探測器配置群集

設定 PowerShell 中的叢集探查連接埠參數。

要設置群集探測埠參數，請使用環境中的值更新以下腳本中的變數。 從腳本中刪除角度括弧`<`（`>`和 ） 。

   ```powershell
   $ClusterNetworkName = "<Cluster Network Name>"
   $IPResourceName = "<SQL Server FCI IP Address Resource Name>" 
   $ILBIP = "<n.n.n.n>" 
   [int]$ProbePort = <nnnnn>

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

以下清單描述了需要更新的值：

   - `<Cluster Network Name>`：網路的 Windows 伺服器容錯移轉叢集名稱。 在**容錯移轉叢集管理器** > **網路中**，按右鍵網路並選擇**屬性**。 正確的值在 [一般]**** 索引標籤的 [名稱]**** 底下。

   - `<SQL Server FCI IP Address Resource Name>`：SQL 伺服器 FCI IP 位址資源名稱。 在**容錯移轉叢集管理器** > **角色**中，在 SQL Server FCI 角色下，在**伺服器名稱**下，按右鍵 IP 位址資源並選擇**屬性**。 正確的值在 [一般]**** 索引標籤的 [名稱]**** 底下。 

   - `<ILBIP>`：ILB IP 位址。 此位址在 Azure 入口網站中會設定為 ILB 前端位址。 這也是 SQL Server FCI IP 位址。 您可以在 [容錯移轉叢集管理員]**** 中，您找到 `<SQL Server FCI IP Address Resource Name>` 所在位置的相同內容頁面上找到該位址。  

   - `<nnnnn>`：在負載等化器運行狀況探測器中配置的探頭埠。 任何未使用的 TCP 連接埠都有效。

>[!IMPORTANT]
>叢集參數的子網路遮罩必須是 TCP IP 廣播位址：`255.255.255.255`。

設置群集探測後，可以看到 PowerShell 中的所有群集參數。 執行此指令碼︰

   ```powershell
   Get-ClusterResource $IPResourceName | Get-ClusterParameter 
  ```

## <a name="step-7-test-fci-failover"></a>步驟 7：測試 FCI 容錯移轉

測試 FCI 的容錯移轉來驗證叢集功能。 請執行下列步驟：

1. 使用 RDP 連接到其中一個 SQL Server FCI 叢集節點。

1. 打開**容錯移轉叢集管理器**。 選取 [角色]****。 請注意哪個節點擁有 SQL Server FCI 角色。

1. 以滑鼠右鍵按一下 SQL Server FCI 角色。

1. 選擇 **"移動"，** 然後選擇 **"最佳可能節點**"。

**容錯移轉叢集管理器**顯示角色，其資源離線。 接著資源會移動，並在另一個節點上線。

### <a name="test-connectivity"></a>測試連線能力

要測試連接，請登錄到同一虛擬網路中的另一個虛擬機器。 開啟 [SQL Server Management Studio]****，然後連接到 SQL Server FCI 名稱。

>[!NOTE]
>如果需要，可以下載 SQL[伺服器管理工作室](https://msdn.microsoft.com/library/mt238290.aspx)。

## <a name="limitations"></a>限制

Azure 虛擬機器支援 Windows Server 2019 上的 Microsoft 分散式交易協調器 （MSDTC），在群集共用卷 （CSV） 上存儲和[標準負載等化器](../../../load-balancer/load-balancer-standard-overview.md)。

在 Azure 虛擬機器上，Windows Server 2016 或更早版本不支援 MSDTC，因為：

- 無法將群集的 MSDTC 資源配置為使用共用存儲。 在 Windows Server 2016 上，如果創建 MSDTC 資源，即使存儲可用，也不會顯示任何可供使用的共用存儲。 Windows Server 2019 中已修正此問題。
- 基本負載等化器不處理 RPC 埠。

## <a name="see-also"></a>另請參閱

[使用遠端桌面（Azure）直接設置存儲空間](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-storage-spaces-direct-deployment)

[帶存儲空間直接的超融合解決方案](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct)

[儲存空間直接存取概觀](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview)

[SQL 伺服器對存儲空間直接的支援](https://blogs.technet.microsoft.com/dataplatforminsider/2016/09/27/sql-server-2016-now-supports-windows-server-2016-storage-spaces-direct/)
