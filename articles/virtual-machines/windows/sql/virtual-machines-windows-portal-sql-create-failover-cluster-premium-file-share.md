---
title: 具有高級檔共用的 SQL Server FCI - Azure 虛擬機器
description: 本文介紹如何使用 Azure 虛擬機器上的高級檔共用創建 SQL Server 容錯移轉叢集實例。
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.assetid: 9fc761b1-21ad-4d79-bebc-a2f094ec214d
ms.service: virtual-machines-sql
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/09/2019
ms.author: mathoma
ms.openlocfilehash: 9595ee87801fa4ce187a50197fc58d6c448eac24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303217"
---
# <a name="configure-a-sql-server-failover-cluster-instance-with-premium-file-share-on-azure-virtual-machines"></a>配置在 Azure 虛擬機器上具有高級檔共用的 SQL Server 容錯移轉叢集實例

本文介紹如何使用[高級檔共用](../../../storage/files/storage-how-to-create-premium-fileshare.md)在 Azure 虛擬機器上創建 SQL Server 容錯移轉叢集實例 （FCI）。

高級檔共用是 SSD 支援的、始終保持的低延遲檔共用，完全支援這些共用與 SQL Server 2012 或更高版本的 Windows Server 2012 或更高版本的容錯移轉叢集實例一起使用。 高級檔共用為您提供更大的靈活性，使您能夠調整檔共用的大小並擴展檔共用，而不會有任何停機時間。


## <a name="before-you-begin"></a>開始之前

在開始之前，您需要瞭解並具備一些功能。

您應該對這些技術有操作性瞭解：

- [Windows 叢集技術](/windows-server/failover-clustering/failover-clustering-overview)
- [SQL 伺服器容錯移轉叢集實例](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

需要注意的是，在 Azure IaaS VM 容錯移轉叢集上，我們建議每個伺服器（叢集節點）和單個子網使用單個 NIC。 Azure 網路具有物理冗余，因此 Azure IaaS VM 來賓群集上不需要其他 NIC 和子網。 群集驗證報告將警告您，這些節點只能在單個網路上訪問。 您可以在 Azure IaaS VM 容錯移轉叢集上忽略此警告。

您還應對這些技術有一般瞭解：

- [Azure 高級檔共用](../../../storage/files/storage-how-to-create-premium-fileshare.md)
- [Azure 資源群組](../../../azure-resource-manager/management/manage-resource-groups-portal.md)

> [!IMPORTANT]
> 此時，Azure 虛擬機器上的 SQL Server 容錯移轉叢集實例僅支援[SQL Server IaaS 代理擴展](virtual-machines-windows-sql-server-agent-extension.md)的[羽量級管理模式](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes)。 要從完全擴展模式更改為羽量級，請刪除相應 VM 的**SQL 虛擬機器**資源，然後在羽量級模式下將其註冊到 SQL VM 資來源提供者。 使用 Azure 門戶刪除**SQL 虛擬機器**資源時，**請清除正確的虛擬機器 旁邊的核取方塊**。 完整擴展支援自動備份、修補和高級門戶管理等功能。 在輕量管理模式下重新安裝代理後，這些功能不適用於 SQL VM。

高級檔共用提供 IOPS 和整個容量，以滿足許多工作負載的需求。 對於 IO 密集型工作負載，請考慮[SQL Server 容錯移轉叢集實例，該群集實例具有存儲空間直接](virtual-machines-windows-portal-sql-create-failover-cluster.md)，基於託管高級磁片或超級磁片。  

檢查環境的 IOPS 活動，並驗證高級檔共用在開始部署或遷移之前是否提供所需的 IOPS。 使用 Windows 效能監視器磁片計數器監視 SQL 伺服器資料、日誌和臨時資料庫檔所需的總 IOPS（磁片傳輸/秒）和輸送量（磁片位元組/秒）。

許多工作負載都具有 IO 的突發性，因此最好在大量使用期間進行檢查，並注意最大 IOPS 和平均 IOPS。 高級檔共用根據共用的大小提供 IOPS。 高級檔共用還提供免費突發功能，允許您將 IO 爆裂到基準金額的三倍，長達一小時。

有關高級檔共用性能的詳細資訊，請參閱[檔共用性能層](https://docs.microsoft.com/azure/storage/files/storage-files-planning#storage-tiers)。

### <a name="licensing-and-pricing"></a>授權和定價

在 Azure 虛擬機器上，可以使用即用即付 （PAYG） 或自帶許可證 （BYOL） VM 映射對 SQL Server 進行許可證。 您選擇的圖像類型會影響您的充電方式。

通過即付即用許可，Azure 虛擬機器上的 SQL Server 容錯移轉叢集實例 （FCI） 會為 FCI 的所有節點（包括被動節點）產生費用。 如需詳細資訊，請參閱 [SQL Server Enterprise 虛擬機器定價](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/)。

如果您有與軟體保證的企業協定，則可以為每個活動節點使用一個免費被動 FCI 節點。 要利用 Azure 中的此優勢，請使用 BYOL VM 映射，並在 FCI 的主動節點和被動節點上使用相同的許可證。 如需詳細資訊，請參閱 [Enterprise 合約](https://www.microsoft.com/Licensing/licensing-programs/enterprise.aspx)。

要比較 Azure 虛擬機器上 SQL Server 的即付即付和 BYOL 許可，請參閱[使用 SQL VM 入門](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms)。

如需授權 SQL Server 的完整資訊，請參閱[定價](https://www.microsoft.com/sql-server/sql-server-2017-pricing)。

### <a name="filestream"></a>檔案資料流

對於具有高級檔共用的容錯移轉叢集，不支援檔流。 要使用檔流，請使用[存儲空間直接](virtual-machines-windows-portal-sql-create-failover-cluster.md)部署群集。

## <a name="prerequisites"></a>Prerequisites

在完成本文中的步驟之前，您應該已經具備：

- Microsoft Azure 訂用帳戶。
- Azure 虛擬機器上的 Windows 網域。
- 具有在 Azure 虛擬機器和活動目錄中創建物件的許可權的域使用者帳戶。
- 運行 SQL Server 服務的域使用者帳戶，您可以在安裝檔共用時登錄到虛擬機器。  
- 具有足夠 IP 位址空間的 Azure 虛擬網路和子網，這些元件：
   - 兩個虛擬機器。
   - 容錯移轉叢集的 IP 位址。
   - 每個 FCI 上一個 IP 位址。
- 在 Azure 網路上配置的 DNS，指向網域控制站。
- 基於資料庫對資料檔案的存儲配額，用作群集磁碟機的高級[檔共用](../../../storage/files/storage-how-to-create-premium-fileshare.md)。
- 如果您在 Windows Server 2012 R2 及更高版本上，則需要另一個檔共用用作檔案共用見證人，因為 Windows 2016 和更高版本支援雲見證。 您可以使用其他 Azure 檔共用，也可以在單獨的虛擬機器上使用檔共用。 如果要使用另一個 Azure 檔共用，則可以使用與群集磁碟機中使用的高級檔共用相同的過程裝載它。 

使用這些先決條件後，您可以開始構建容錯移轉叢集。 第一步是建立虛擬機器。

## <a name="step-1-create-the-virtual-machines"></a>第 1 步：創建虛擬機器

1. 使用訂閱登錄到[Azure 門戶](https://portal.azure.com)。

1. [建立 Azure 可用性設定組](../tutorial-availability-sets.md)。

   可用性設定組群組虛擬機器遍佈在容錯網域與更新網域中， 它可確保應用程式不受單點故障的影響，例如網路交換器或伺服器機架的電源單元。

   如果尚未為虛擬機器創建資源組，請在創建 Azure 可用性集時執行此操作。 如果使用 Azure 門戶創建可用性集，請執行以下步驟：

   1. 在 Azure 門戶中，選擇 **"創建要**打開 Azure 應用商店的資源"。 搜尋 [可用性設定組]****。
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

   >[!IMPORTANT]
   > 建立虛擬機器後，請移除預先安裝的獨立 SQL Server 執行個體。 將容錯移轉叢集和高級檔共用設置為存儲後，將使用預先安裝的 SQL Server 媒體創建 SQL Server FCI。

   或者，您可以使用僅包含作業系統的 Azure 應用商店映射。 選擇**Windows Server 2016 資料中心**映射，並在將容錯移轉叢集和高級檔共用設置為存儲後安裝 SQL Server FCI。 此映射不包含 SQL Server 安裝介質。 將 SQL Server 安裝介質放置在可以為每個伺服器運行它的位置。

1. Azure 創建虛擬機器後，請使用 RDP 連接到每個虛擬機器。

   首次使用 RDP 連接到虛擬機器時，會提示您是否希望允許 PC 在網路上被發現。 選取 [是]****。

1. 如果使用基於 SQL Server 的虛擬機器映射之一，請刪除 SQL Server 實例。

   1. 在**程式和功能**中，按右鍵**Microsoft SQL Server 201* （64 位），** 然後選擇 **"卸載/更改**"。
   1. 選取 [移除]****。
   1. 選取預設執行個體。
   1. 移除所有在 [Database Engine 服務]**** 下的功能。 不要刪除**共用功能**。 您將看到以下螢幕截圖：

        ![選取功能](./media/virtual-machines-windows-portal-sql-create-failover-cluster/03-remove-features.png)

   1. 選擇 **"下一步**"，然後選擇 **"刪除**"。

1. <span id="ports"> </span>打開防火牆埠。  

   在每個虛擬機器上，打開 Windows 防火牆上的這些埠：

   | 目的 | TCP 連接埠 | 注意
   | ------ | ------ | ------
   | SQL Server | 1433 | 適用於 SDL Server 預設執行個體的一般連接埠。 若您曾使用來自資源庫的映像，此連接埠會自動開啟。
   | 健全狀況探查 | 59999 | 任何開啟的 TCP 連接埠。 在接下來的步驟中，設定負載平衝器[健全狀況探查](#probe)和要使用此連接埠的叢集。
   | 檔案共用 | 445 | 檔共用服務使用的埠。

1. [將虛擬機器新增至既存的網域](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain)。

創建和配置虛擬機器後，可以配置高級檔共用。

## <a name="step-2-mount-the-premium-file-share"></a>第 2 步：裝載高級檔共用

1. 登錄到 Azure[門戶](https://portal.azure.com)並轉到存儲帳戶。
1. 轉到**檔服務**下的 **"檔共用**"，然後選擇要用於 SQL 存儲的高級檔共用。
1. 選擇 **"連接"** 可打開檔共用的連接字串。
1. 從下拉清單中選擇要使用的磁碟機號，然後將這兩個代碼塊複製到記事本。


   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/premium-file-storage-commands.png" alt-text="從檔共用連接門戶複製兩個 PowerShell 命令":::

1. 使用 RDP 使用 SQL Server FCI 將用於服務帳戶的帳戶連接到 SQL Server VM。
1. 打開管理 PowerShell 命令主控台。
1. 運行在門戶中工作時保存的命令。
1. 使用檔資源管理器或 **"運行"** 對話方塊（Windows 徽標鍵 = r）轉到共用。 使用網路路徑`\\storageaccountname.file.core.windows.net\filesharename`。 例如， `\\sqlvmstorageaccount.file.core.windows.net\sqlpremiumfileshare`

1. 在新連接的檔共用上至少創建一個資料夾，以將 SQL 資料檔案放入其中。
1. 在將參與群集的每個 SQL Server VM 上重複這些步驟。

  > [!IMPORTANT]
  > - 請考慮對備份檔案使用單獨的檔共用來保存資料和日誌檔的 IOPS 和空間容量。 您可以將高級檔或標準檔共用用於備份檔案。
  > - 如果您在 Windows 2012 R2 和舊版上，請按照以下步驟裝載要用作檔案共用見證的檔共用。 

## <a name="step-3-configure-the-failover-cluster"></a>步驟 3：配置容錯移轉叢集

下一步是配置容錯移轉叢集。 在此步驟中，您將完成以下子步驟：

1. 添加 Windows 伺服器容錯移轉叢集功能。
1. 驗證群集。
1. 建立容錯移轉叢集。
1. 創建雲見證（適用于 Windows Server 2016 和更高版本）或檔案共用見證（對於 Windows Server 2012 R2 及更高版本）。


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

### <a name="validate-the-cluster"></a>驗證叢集

在 UI 中或使用 PowerShell 驗證群集。

要使用 UI 驗證群集，請對其中一個虛擬機器執行以下步驟：

1. 在 **"伺服器管理員**"下，選擇 **"工具**"，然後選擇**容錯移轉叢集管理器**。
1. 在**容錯移轉叢集管理器**下，選擇**操作**，然後選擇 **"驗證配置**"。
1. 選取 [下一步]****。
1. 在 **"選擇伺服器"或"群集**"下，輸入兩個虛擬機器的名稱。
1. 在 **"測試"選項**下，選擇 **"僅運行"我選擇的測試**。 選取 [下一步]****。
1. 在 **"測試選擇**"下，選擇**除存儲空間**和**存儲空間直接**之外的所有測試，如下所示：

   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/cluster-validation.png" alt-text="選擇群集驗證測試":::

1. 選取 [下一步]****。
1. 在 **"確認"** 下，選擇 **"下一步**"。

[驗證設定精靈] **** 會執行驗證測試。

要使用 PowerShell 驗證群集，請從其中一個虛擬機器上的管理員 PowerShell 會話運行以下腳本：

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
   ```

驗證叢集後，請建立容錯移轉叢集。

### <a name="create-the-failover-cluster"></a>建立容錯移轉叢集

若要建立容錯移轉叢集，您需要：
- 將成為叢集節點的虛擬機器的名稱。
- 容錯移轉叢集的名稱
- 容錯移轉叢集的 IP 位址。 可以使用與叢集節點相同的 Azure 虛擬網路和子網上未使用的 IP 位址。

#### <a name="windows-server-2012-through-windows-server-2016"></a>Windows 伺服器 2012 通過 Windows 伺服器 2016

以下 PowerShell 腳本通過 Windows Server 2016 為 Windows 伺服器 2012 創建容錯移轉叢集。 使用節點的名稱（虛擬機器名稱）和 Azure 虛擬網路中的可用 IP 位址更新腳本。

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

#### <a name="windows-server-2019"></a>Windows Server 2019

以下 PowerShell 腳本為 Windows Server 2019 創建容錯移轉叢集。 有關詳細資訊，請參閱[容錯移轉叢集：群集網路物件](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97)。 使用節點的名稱（虛擬機器名稱）和 Azure 虛擬網路中的可用 IP 位址更新腳本。

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```


### <a name="create-a-cloud-witness-win-2016-"></a>創建雲見證（贏 2016 +）

如果您在 Windows Server 2016 及更高版本上，則需要創建雲見證。 雲見證是存儲在 Azure 存儲 Blob 中的一種新型群集仲裁見證。 這樣，就不需要託管見證共用的單獨 VM 或使用單獨的檔共用。

1. [建立容錯移轉叢集的雲端見證](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness)。

1. 建立 Blob 容器。

1. 儲存存取金鑰和容器 URL。

### <a name="configure-quorum"></a>設定仲裁 

對於 Windows 伺服器 2016 及更高版本，請將群集配置為使用您剛剛創建的雲見證。 按照所有步驟[配置使用者介面中的仲裁見證](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness)。

對於 Windows Server 2012 R2 及舊版版，請按照[使用者介面中的仲裁見證人配置](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness)相同的步驟，但在 **"選擇仲裁見證"** 頁上，選擇"**配置檔案共用見證"** 選項。 指定分配給檔案共用見證的檔共用，無論是您在單獨的虛擬機器上配置的檔共用，還是從 Azure 裝載的檔共用。 


## <a name="step-4-test-cluster-failover"></a>第 4 步：測試群集容錯移轉

測試群集的容錯移轉。 在**容錯移轉叢集管理器**中，按右鍵群集並選擇 **"移動** > **核心群集資源** > **選擇節點**更多操作"，然後選擇群集的其他節點。 將核心群集資源移動到群集的每個節點，然後將其移回主節點。 如果可以成功將群集移動到每個節點，則可以安裝 SQL Server。  

:::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/test-cluster-failover.png" alt-text="通過將核心資源移動到其他節點來測試群集容錯移轉":::

## <a name="step-5-create-the-sql-server-fci"></a>第 5 步：創建 SQL 伺服器 FCI

配置容錯移轉叢集後，可以創建 SQL Server FCI。

1. 使用 RDP 連接到第一個虛擬機器。

1. 在**容錯移轉叢集管理器**中，確保所有核心群集資源都位於第一個虛擬機器上。 如果需要，將所有資源移動到此虛擬機器。

1. 找出安裝媒體。 若虛擬機器是使用其中一個 Azure Marketplace 映像，則媒體會位於 `C:\SQLServer_<version number>_Full`。 選取 [設定]****。

1. 在**SQL 伺服器安裝中心**中，選擇 **"安裝**"。

1. 選擇**新的 SQL Server 容錯移轉叢集安裝**。 請遵循精靈內的指示安裝 SQL Server FCI。

   FCI 資料目錄需要位於高級檔共用上。 輸入共用的完整路徑，以此形式輸入： `\\storageaccountname.file.core.windows.net\filesharename\foldername`。 將顯示一條警告，告訴您已指定檔案伺服器為數據目錄。 這是預期的警告。 確保 RDP 在保留檔共用時使用的使用者帳戶與 SQL Server 服務用於避免可能失敗的帳戶相同。

   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/use-file-share-as-data-directories.png" alt-text="將檔共用用作 SQL 資料目錄":::

1. 完成嚮導中的步驟後，安裝程式將在第一個節點上安裝 SQL Server FCI。

1. 安裝程式在第一個節點上安裝 FCI 後，使用 RDP 連接到第二個節點。

1. 開啟 [SQL Server 安裝中心]****。 選擇**安裝**。

1. 選擇**將節點添加到 SQL Server 容錯移轉叢集**。 按照嚮導中的說明安裝 SQL Server 並將伺服器添加到 FCI。

   >[!NOTE]
   >若您曾透過 SQL Server 使用 Azure Marketplace 資源庫映像，映像會包含 SQL Server 工具。 如果不使用這些映射之一，請單獨安裝 SQL Server 工具。 請參閱[下載 Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)。

## <a name="step-6-create-the-azure-load-balancer"></a>步驟 6：創建 Azure 負載等化器

在 Azure 虛擬機器上，叢集會使用負載平衡器來保留每次均需在一個節點上的 IP 位址。 在此解決方案中，負載平衡器會保留 SQL Server FCI 的 IP 位址。

有關詳細資訊，請參閱[創建和配置 Azure 負載等化器](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer)。

### <a name="create-the-load-balancer-in-the-azure-portal"></a>在 Azure 入口網站中建立負載平衡器

若要建立負載平衡器：

1. 在 Azure 門戶中，轉到包含虛擬機器的資源組。

1. 選取 [加入]****。 在 Azure 應用商店中搜索**負載等化器**。 選擇**負載平衡器**。

1. 選取 [建立]****。

1. 使用以下值設置負載等化器：

   - **訂閱**：Azure 訂閱。
   - **資源組**：包含虛擬機器的資源組。
   - **名稱**︰用以識別負載平衡器的名稱。
   - **區域**：包含虛擬機器的 Azure 位置。
   - **類型**：公共或私有。 可以從虛擬網路內訪問專用負載等化器。 大部分的 Azure 應用程式都能使用私人負載平衡器。 如果應用程式需要直接通過互聯網訪問 SQL Server，請使用公共負載等化器。
   - **SKU**： 標準.
   - **虛擬網路**：與虛擬機器相同的網路。
   - **IP 位址分配**：靜態。 
   - **私人 IP 位址**：分配給 SQL Server FCI 群集網路資源的 IP 位址。

   下圖顯示了**創建負載等化器**UI：

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

1. 在 **"添加運行狀況探測"** 邊<span id="probe"> </span>欄選項卡上，設置以下運行狀況探測參數。

   - **名稱**：健全狀況探查的名稱。
   - **協定**：TCP。
   - **埠**：在[此步驟](#ports)中為運行狀況探測在防火牆中創建的埠。 在本文中，該示例使用 TCP 埠`59999`。
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

## <a name="step-7-configure-the-cluster-for-the-probe"></a>步驟 7：為探測器配置群集

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

## <a name="step-8-test-fci-failover"></a>第 8 步：測試 FCI 容錯移轉

測試 FCI 的容錯移轉來驗證叢集功能。 請執行下列步驟：

1. 使用 RDP 連接到其中一個 SQL Server FCI 叢集節點。

1. 打開**容錯移轉叢集管理器**。 選取 [角色]****。 請注意哪個節點擁有 SQL Server FCI 角色。

1. 以滑鼠右鍵按一下 SQL Server FCI 角色。

1. 選擇 **"移動"，** 然後選擇 **"最佳可能節點**"。

**容錯移轉叢集管理器**顯示角色，其資源離線。 然後，資源移動並在其他節點中重新連線。

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

- [Windows 叢集技術](/windows-server/failover-clustering/failover-clustering-overview)
- [SQL 伺服器容錯移轉叢集實例](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
