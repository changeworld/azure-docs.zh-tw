---
title: 使用 premium 檔案共用 SQL Server FCI-Azure 虛擬機器
description: 本文說明如何在 Azure 虛擬機器上使用 premium 檔案共用，建立 SQL Server 容錯移轉叢集實例。
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
ms.openlocfilehash: b2d49eeadf068cbaacaa5e147f38025c55f33ff4
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2020
ms.locfileid: "77651356"
---
# <a name="configure-a-sql-server-failover-cluster-instance-with-premium-file-share-on-azure-virtual-machines"></a>在 Azure 虛擬機器上使用 premium 檔案共用設定 SQL Server 容錯移轉叢集實例

本文說明如何使用[premium 檔案共用](../../../storage/files/storage-how-to-create-premium-fileshare.md)，在 Azure 虛擬機器上建立 SQL Server 容錯移轉叢集實例（FCI）。

高階檔案共用是以 SSD 為基礎且一致的低延遲檔案共用，可在 Windows Server 2012 或更新版本的 SQL Server 2012 或更新版本中，完全支援搭配容錯移轉叢集實例使用。 Premium 檔案共用為您提供更大的彈性，可讓您在不需停機的情況下調整檔案共用的大小和規模。


## <a name="before-you-begin"></a>開始之前

在開始之前，您必須知道並準備好一些事項。

您應該對這些技術有操作的瞭解：

- [Windows 叢集技術](/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server 容錯移轉叢集實例](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

有一點要注意的是，在 Azure IaaS VM 容錯移轉叢集上，我們建議每個伺服器（叢集節點）和單一子網都有一個 NIC。 Azure 網路有實體冗余，讓 Azure IaaS VM 來賓叢集上不需要額外的 Nic 和子網。 叢集驗證報告會警告您，節點只能在單一網路上連線。 您可以在 Azure IaaS VM 容錯移轉叢集上忽略此警告。

您也應該對這些技術有大致的瞭解：

- [Azure premium 檔案共用](../../../storage/files/storage-how-to-create-premium-fileshare.md)
- [Azure 資源群組](../../../azure-resource-manager/management/manage-resource-groups-portal.md)

> [!IMPORTANT]
> 目前，只有[SQL Server IaaS 代理程式擴充](virtual-machines-windows-sql-server-agent-extension.md)功能的[輕量管理模式](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes)才支援 Azure 虛擬機器上的 SQL Server 容錯移轉叢集實例。 若要從完整延伸模式變更為輕量，請刪除對應 Vm 的**Sql 虛擬機器**資源，然後在輕量模式中向 sql VM 資源提供者註冊。 使用 Azure 入口網站刪除**SQL 虛擬機器**資源時，請**清除正確虛擬機器旁的核取方塊**。 完整延伸模組支援自動備份、修補和先進入口網站管理等功能。 在輕量管理模式中重新安裝代理程式之後，這些功能將無法在 SQL Vm 上使用。

Premium 檔案共用可提供 IOPS 和整個容量，以符合許多工作負載的需求。 針對需要大量 IO 的工作負載，請考慮根據受控 premium 磁片或 ultra 磁片，[使用儲存空間直接存取來 SQL Server 容錯移轉叢集實例](virtual-machines-windows-portal-sql-create-failover-cluster.md)。  

檢查您環境的 IOPS 活動，並確認 premium 檔案共用會在您開始部署或遷移之前，提供您所需的 IOPS。 使用 Windows 效能監視器磁片計數器來監視 SQL Server 資料、記錄檔和暫存資料庫檔案所需的總 IOPS （磁片傳輸/秒）和輸送量（磁片位元組/秒）。

許多工作負載都有高載 IO，因此最好在長時間使用期間進行檢查，並記下最大 IOPS 和平均 IOPS。 Premium 檔案共用會根據共用大小來提供 IOPS。 高階檔案共用也提供免費的高載，可讓您將 IO 高載到最多三個小時的基準量。

如需 premium 檔案共用效能的詳細資訊，請參閱檔案[共用效能層級](https://docs.microsoft.com/azure/storage/files/storage-files-planning#file-share-performance-tiers)。

### <a name="licensing-and-pricing"></a>授權和定價

在 Azure 虛擬機器上，您可以使用隨用隨付（PAYG）或攜帶您自己的授權（BYOL） VM 映射來授權 SQL Server。 您選擇的映射類型會影響向您收費的方式。

使用隨用隨付授權時，Azure 虛擬機器上 SQL Server 的容錯移轉叢集實例（FCI）會產生 FCI 所有節點的費用，包括被動節點。 如需詳細資訊，請參閱 [SQL Server Enterprise 虛擬機器定價](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/)。

如果您具有軟體保證的 Enterprise 合約，就可以針對每個使用中節點使用一個免費的被動 FCI 節點。 若要利用 Azure 中的這項權益，請使用 BYOL VM 映射，並在 FCI 的主動和被動節點上使用相同的授權。 如需詳細資訊，請參閱 [Enterprise 合約](https://www.microsoft.com/Licensing/licensing-programs/enterprise.aspx)。

若要比較 Azure 虛擬機器上 SQL Server 的隨用隨付和 BYOL 授權，請參閱[開始使用 SQL vm](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms)。

如需授權 SQL Server 的完整資訊，請參閱[定價](https://www.microsoft.com/sql-server/sql-server-2017-pricing)。

### <a name="filestream"></a>檔案資料流

具有 premium 檔案共用的容錯移轉叢集不支援 Filestream。 若要使用 filestream，請使用[儲存空間直接存取](virtual-machines-windows-portal-sql-create-failover-cluster.md)來部署您的叢集。

## <a name="prerequisites"></a>Prerequisites

在完成本文中的步驟之前，您應該已經具備：

- Microsoft Azure 訂用帳戶。
- Azure 虛擬機器上的 Windows 網域。
- 具有在 Azure 虛擬機器和 Active Directory 中建立物件之許可權的網域使用者帳戶。
- 執行 SQL Server 服務的網域使用者帳戶，而且您可以在掛接檔案共用時，使用登入虛擬機器。  
- 具有足夠 IP 位址空間可供這些元件使用的 Azure 虛擬網路和子網：
   - 兩部虛擬機器。
   - 容錯移轉叢集的 IP 位址。
   - 每個 FCI 上一個 IP 位址。
- 在 Azure 網路上設定的 DNS，指向網域控制站。
- 用來作為叢集磁片磁碟機的高階檔案[共用](../../../storage/files/storage-how-to-create-premium-fileshare.md)，根據您的資料檔案之資料庫的儲存空間配額。
- 如果您使用 Windows Server 2012 R2 和更舊版本，您將需要另一個檔案共用做為檔案共用見證，因為 Windows 2016 和更新版本支援雲端見證。 您可以使用另一個 Azure 檔案共用，也可以在個別的虛擬機器上使用檔案共用。 如果您要使用另一個 Azure 檔案共用，您可以使用與用於叢集磁片磁碟機的 premium 檔案共用相同的程式來掛接它。 

準備好這些必要條件之後，您就可以開始建立您的容錯移轉叢集。 第一步是建立虛擬機器。

## <a name="step-1-create-the-virtual-machines"></a>步驟1：建立虛擬機器

1. 使用您的訂用帳戶登入[Azure 入口網站](https://portal.azure.com)。

1. [建立 Azure 可用性設定組](../tutorial-availability-sets.md)。

   可用性設定組群組虛擬機器遍佈在容錯網域與更新網域中， 它可確保您的應用程式不會受到單一失敗點的影響，例如網路交換器或伺服器機架的電源裝置。

   如果您尚未建立虛擬機器的資源群組，請在建立 Azure 可用性設定組時執行此動作。 如果您要使用 Azure 入口網站來建立可用性設定組，請執行下列步驟：

   1. 在 Azure 入口網站中，選取 **建立資源** 以開啟 Azure Marketplace。 搜尋 [可用性設定組]。
   1. 選取**可用性設定組**。
   1. 選取 [建立]。
   1. 在 [**建立可用性設定組**] 底下，提供下列值：
      - **名稱**：可用性設定組的名稱。
      - **訂用帳戶**：您的 Azure 訂用帳戶。
      - **資源群組**：如果您想要使用現有的群組，請按一下 [**選取現有**的]，然後從清單中選取群組。 否則，請選取 [**建立新**的] 並輸入群組的名稱。
      - **位置**︰設定您計劃建立虛擬機器的位置。
      - **容錯網域**：使用預設值（**3**）。
      - **更新網域**：使用預設值（**5**）。
   1. 選取 [**建立**] 以建立可用性設定組。

1. 在可用性設定組中建立虛擬機器。

   在 Azure 可用性設定組中佈建兩部 SQL Server 虛擬機器。 如需指示，請參閱[在 Azure 入口網站中佈建 SQL Server 虛擬機器](virtual-machines-windows-portal-sql-server-provision.md)。

   將兩部虛擬機器放置於：

   - 在與您的可用性設定組相同的 Azure 資源群組中。
   - 您網域控制站的相同網路上。
   - 在具有足夠 IP 位址空間的子網上，您最終可能會在叢集上使用的虛擬機器和所有 Fci。
   - Azure 可用性設定組中。

      >[!IMPORTANT]
      >建立虛擬機器之後，您就無法設定或變更可用性設定組。

   從 Azure Marketplace 選擇影像。 您可以使用包含 Windows Server 和 SQL Server 的 Azure Marketplace 映射，或使用其中一個只包含 Windows Server 的映射。 如需詳細資訊，請參閱[Azure 虛擬機器上的 SQL Server 總覽](virtual-machines-windows-sql-server-iaas-overview.md)。

   Azure 資源庫中的官方 SQL Server 映射包含已安裝的 SQL Server 實例、SQL Server 安裝軟體，以及所需的金鑰。

   >[!IMPORTANT]
   > 建立虛擬機器後，請移除預先安裝的獨立 SQL Server 執行個體。 將容錯移轉叢集和 premium 檔案共用設定為存放裝置之後，您將使用預先安裝的 SQL Server 媒體來建立 SQL Server FCI。

   或者，您可以使用只包含作業系統的 Azure Marketplace 映射。 在您將容錯移轉叢集和 premium 檔案共用設定為存放裝置之後，請選擇**Windows Server 2016 Datacenter**映射並安裝 SQL Server FCI。 此映射不包含 SQL Server 安裝媒體。 將 SQL Server 安裝媒體放在您可以為每部伺服器執行它的位置。

1. 在 Azure 建立您的虛擬機器之後，請使用 RDP 連線到每一部。

   當您第一次使用 RDP 連線到虛擬機器時，系統會詢問您是否要允許電腦可在網路上探索。 選取 [是]。

1. 如果您使用其中一個以 SQL Server 為基礎的虛擬機器映射，請移除 SQL Server 實例。

   1. 在 [**程式和功能**] 中，以滑鼠右鍵按一下 **[Microsoft SQL Server 201_ （64位）** ]，然後選取 [**卸載/變更**]。
   1. 選取 [移除]。
   1. 選取預設執行個體。
   1. 移除所有在 [Database Engine 服務] 下的功能。 請勿移除**共用的功能**。 您會看到類似下列螢幕擷取畫面的內容：

        ![選取功能](./media/virtual-machines-windows-portal-sql-create-failover-cluster/03-remove-features.png)

   1. 選取 **[下一步]** ，然後選取 [**移除**]。

1. <span id="ports"></span>開啟防火牆埠。  

   在每部虛擬機器上，開啟 Windows 防火牆上的下列埠：

   | 目的 | TCP 連接埠 | 注意
   | ------ | ------ | ------
   | SQL Server | 1433 | 適用於 SDL Server 預設執行個體的一般連接埠。 若您曾使用來自資源庫的映像，此連接埠會自動開啟。
   | 健全狀況探查 | 59999 | 任何開啟的 TCP 連接埠。 在接下來的步驟中，設定負載平衝器[健全狀況探查](#probe)和要使用此連接埠的叢集。
   | 檔案共用 | 445 | 檔案共用服務所使用的埠。

1. [將虛擬機器新增至既存的網域](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain)。

建立並設定虛擬機器之後，您可以設定 premium 檔案共用。

## <a name="step-2-mount-the-premium-file-share"></a>步驟2：掛接 premium 檔案共用

1. 登入[Azure 入口網站](https://portal.azure.com)並移至您的儲存體帳戶。
1. 移至 [檔案**服務**] 底下的 [檔案**共用**]，然後選取您想要用於 SQL 儲存體的 premium 檔案共用。
1. 選取 **[連線]** ，以顯示您檔案共用的連接字串。
1. 從下拉式清單中選取您想要使用的磁碟機號，然後將這兩個程式碼區塊複製到 [記事本]。


   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/premium-file-storage-commands.png" alt-text="從檔案共用 connect 入口網站複製這兩個 PowerShell 命令":::

1. 使用您的 SQL Server FCI 將用於服務帳戶的帳戶，透過 RDP 連線到 SQL Server VM。
1. 開啟系統管理 PowerShell 命令主控台。
1. 執行您稍早在入口網站中工作時所儲存的命令。
1. 使用 [檔案瀏覽器] 或 [**執行**] 對話方塊（Windows 標誌鍵 + r）移至共用。 使用網路路徑 `\\storageaccountname.file.core.windows.net\filesharename`。 例如， `\\sqlvmstorageaccount.file.core.windows.net\sqlpremiumfileshare`

1. 在新連接的檔案共用上，至少建立一個資料夾，以便將您的 SQL 資料檔案放入其中。
1. 在要參與叢集的每個 SQL Server VM 上重複這些步驟。

  > [!IMPORTANT]
  > - 請考慮針對備份檔案使用個別的檔案共用，以儲存此共用的資料和記錄檔的 IOPS 和空間容量。 您可以針對備份檔案使用 premium 或 standard 檔案共用。
  > - 如果您使用的是 Windows 2012 R2 和更舊版本，請遵循這些相同的步驟來掛接您要做為檔案共用見證的檔案共用。 

## <a name="step-3-configure-the-failover-cluster"></a>步驟3：設定容錯移轉叢集

下一個步驟是設定容錯移轉叢集。 在此步驟中，您將完成下列子步驟：

1. 新增 Windows Server 容錯移轉叢集功能。
1. 驗證叢集。
1. 建立容錯移轉叢集。
1. 建立雲端見證（適用于 Windows Server 2016 和更新版本）或檔案共用見證（適用于 Windows Server 2012 R2 和更舊版本）。


### <a name="add-windows-server-failover-clustering"></a>新增 Windows Server 容錯移轉叢集

1. 使用本機系統管理員成員且有權在 Active Directory 中建立物件的網域帳戶，透過 RDP 連接到第一部虛擬機器。 在之後的設定程序中繼續使用此帳戶。

1. [將容錯移轉叢集新增至每部虛擬機器](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms)。

   若要從 UI 安裝容錯移轉叢集，請在兩部虛擬機器上執行下列步驟：
   1. 在**伺服器管理員**中，選取 [**管理**]，然後選取 [**新增角色及功能**]。
   1. 在 [**新增角色及功能]** 中，選取 **[下一步]** ，直到您**選取 [功能**]。
   1. 在 [**選取功能**] 中，選取 [**容錯移轉**叢集]。 選取所有所需功能與管理工具。 選取 [**新增功能**]。
   1. 選取 **[下一步**]，然後選取 **[完成]** 以安裝功能。

   若要使用 PowerShell 安裝容錯移轉叢集，請在其中一部虛擬機器上的系統管理員 PowerShell 會話中執行下列腳本：

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

### <a name="validate-the-cluster"></a>驗證叢集

在 UI 中或使用 PowerShell 來驗證叢集。

若要使用 UI 來驗證叢集，請在其中一部虛擬機器上執行下列步驟：

1. 在 [**伺服器管理員**] 底下，選取 [**工具**]，然後選取 [**容錯移轉叢集管理員**]。
1. 在 [**容錯移轉叢集管理員**] 底下，選取 [**動作**]，然後選取 [**驗證**設定]。
1. 選取 [下一步]。
1. 在 [**選取伺服器或**叢集] 下，輸入兩部虛擬機器的名稱。
1. 在 [**測試選項**] 底下，選取 [**僅執行我選取的測試**]。 選取 [下一步]。
1. 在 [**測試選取範圍**] 底下，選取 [**儲存體**] 和 [**儲存空間直接存取**] 以外的所有測試，如下所示：

   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/cluster-validation.png" alt-text="選取叢集驗證測試":::

1. 選取 [下一步]。
1. 在 [**確認**] 下選取 **[下一步]** 。

[驗證設定精靈]會執行驗證測試。

若要使用 PowerShell 驗證叢集，請從其中一部虛擬機器上的系統管理員 PowerShell 會話執行下列腳本：

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
   ```

驗證叢集後，請建立容錯移轉叢集。

### <a name="create-the-failover-cluster"></a>建立容錯移轉叢集

若要建立容錯移轉叢集，您需要：
- 將成為叢集節點的虛擬機器名稱。
- 容錯移轉叢集的名稱
- 容錯移轉叢集的 IP 位址。 您可以使用未在與叢集節點相同的 Azure 虛擬網路和子網上使用的 IP 位址。

#### <a name="windows-server-2012-through-windows-server-2016"></a>Windows Server 2012 到 Windows Server 2016

下列 PowerShell 腳本會建立 Windows Server 2012 到 Windows Server 2016 的容錯移轉叢集。 使用節點名稱（虛擬機器名稱）和 Azure 虛擬網路中可用的 IP 位址來更新腳本。

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

#### <a name="windows-server-2019"></a>Windows Server 2019

下列 PowerShell 腳本會建立適用于 Windows Server 2019 的容錯移轉叢集。 如需詳細資訊，請參閱[容錯移轉叢集：叢集網路物件](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97)。 使用節點名稱（虛擬機器名稱）和 Azure 虛擬網路中可用的 IP 位址來更新腳本。

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```


### <a name="create-a-cloud-witness-win-2016-"></a>建立雲端見證（Win 2016 +）

如果您是在 Windows Server 2016 和更新版本上，您必須建立雲端見證。 雲端見證是一種新類型的叢集仲裁見證，儲存在 Azure 儲存體 blob 中。 這不需要裝載見證共用的個別 VM，或使用個別的檔案共用。

1. [建立容錯移轉叢集的雲端見證](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness)。

1. 建立 Blob 容器。

1. 儲存存取金鑰和容器 URL。

### <a name="configure-quorum"></a>設定仲裁 

若為 Windows Server 2016 和更新版本，請將叢集設定為使用您剛才建立的雲端見證。 遵循所有步驟，[在使用者介面中設定仲裁見證](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness)。

針對 Windows Server 2012 R2 和更舊版本，請遵循在[使用者介面中設定仲裁見證中](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness)的相同步驟，但在 [**選取仲裁見證**] 頁面上，選取 [**設定檔案共用見證**] 選項。 指定配置為檔案共用見證的檔案共用，無論您是在另一部虛擬機器上設定，或從 Azure 掛接。 


## <a name="step-4-test-cluster-failover"></a>步驟4：測試叢集容錯移轉

測試叢集的容錯移轉。 在**容錯移轉叢集管理員**中，以滑鼠右鍵按一下您的叢集，然後選取 [**更多動作**] > **移動 [核心叢集資源**] > 選取 [**節點**]，然後選取叢集的其他節點。 將核心叢集資源移到叢集的每個節點，然後將它移回主要節點。 如果您可以成功地將叢集移至每個節點，您就可以開始安裝 SQL Server。  

:::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/test-cluster-failover.png" alt-text="藉由將核心資源移至其他節點來測試叢集容錯移轉":::

## <a name="step-5-create-the-sql-server-fci"></a>步驟5：建立 SQL Server FCI

設定容錯移轉叢集之後，您可以建立 SQL Server FCI。

1. 使用 RDP 連接到第一部虛擬機器。

1. 在**容錯移轉叢集管理員**中，請確定第一部虛擬機器上的所有核心叢集資源都是。 如有需要，請將所有資源移至此虛擬機器。

1. 找出安裝媒體。 若虛擬機器是使用其中一個 Azure Marketplace 映像，則媒體會位於 `C:\SQLServer_<version number>_Full`。 選取 [設定]。

1. 在 [ **SQL Server 安裝中心**] 中，選取 [**安裝**]。

1. 選取 [**新增 SQL Server 容錯移轉叢集安裝**]。 請遵循精靈內的指示安裝 SQL Server FCI。

   FCI 資料目錄必須位於 premium 檔案共用上。 輸入共用的完整路徑，格式為： `\\storageaccountname.file.core.windows.net\filesharename\foldername`。 隨即會出現警告，告訴您您已指定檔案伺服器做為資料目錄。 這是預期的警告。 當您保存檔案共用時，請確定您透過 RDP 連線到 VM 的使用者帳戶，與 SQL Server 服務用來避免可能發生失敗的帳戶相同。

   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/use-file-share-as-data-directories.png" alt-text="使用檔案共用作為 SQL 資料目錄":::

1. 完成嚮導中的步驟之後，安裝程式將會在第一個節點上安裝 SQL Server FCI。

1. 安裝程式在第一個節點上安裝 FCI 之後，請使用 RDP 連接到第二個節點。

1. 開啟 [SQL Server 安裝中心]。 選取 [**安裝**]。

1. 選取 [**將節點加入至 SQL Server 容錯移轉**叢集]。 遵循嚮導中的指示，安裝 SQL Server 並將伺服器新增至 FCI。

   >[!NOTE]
   >若您曾透過 SQL Server 使用 Azure Marketplace 資源庫映像，映像會包含 SQL Server 工具。 如果您未使用這些映射的其中一個，請分別安裝 SQL Server 工具。 請參閱[下載 Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)。

## <a name="step-6-create-the-azure-load-balancer"></a>步驟6：建立 Azure 負載平衡器

在 Azure 虛擬機器上，叢集會使用負載平衡器來保留每次均需在一個節點上的 IP 位址。 在此解決方案中，負載平衡器會保留 SQL Server FCI 的 IP 位址。

如需詳細資訊，請參閱[建立和設定 Azure 負載平衡器](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer)。

### <a name="create-the-load-balancer-in-the-azure-portal"></a>在 Azure 入口網站中建立負載平衡器

若要建立負載平衡器：

1. 在 Azure 入口網站中，移至包含虛擬機器的資源群組。

1. 選取 [新增]。 搜尋**Load Balancer**的 Azure Marketplace。 選取 [ **Load Balancer**]。

1. 選取 [建立]。

1. 使用下列值來設定負載平衡器：

   - **訂用帳戶**：您的 Azure 訂用帳戶。
   - **資源群組**：包含您的虛擬機器的資源群組。
   - **名稱**︰用以識別負載平衡器的名稱。
   - **區域**：包含您的虛擬機器的 Azure 位置。
   - **類型**： [公用] 或 [私用]。 您可以從虛擬網路內部存取私人負載平衡器。 大部分的 Azure 應用程式都能使用私人負載平衡器。 如果您的應用程式需要直接透過網際網路存取 SQL Server，請使用公用負載平衡器。
   - **SKU**：標準。
   - **虛擬網路**：與虛擬機器相同的網路。
   - **IP 位址指派**：靜態。 
   - **私人 IP 位址**：您指派給 SQL Server FCI 叢集網路資源的 ip 位址。

   下圖顯示 [**建立負載平衡器**] UI：

   ![設定負載平衡器](./media/virtual-machines-windows-portal-sql-create-failover-cluster/30-load-balancer-create.png)
   

### <a name="configure-the-load-balancer-backend-pool"></a>設定負載平衡器後端集區

1. 返回包含虛擬機器的 Azure 資源群組，並找出新的負載平衡器。 您可能需要重新整理資源群組上的視圖。 選取負載平衡器。

1. 選取 [**後端**集區]，然後選取 [**新增**]。

1. 將後端集區關聯至包含 VM 的可用性設定組。

1. 在 [**目標網路 IP 設定**] 底下，選取 [**虛擬機器**]，然後選擇將參與做為叢集節點的虛擬機器。 請務必包含將裝載 FCI 的所有虛擬機器。

1. 選取 **[確定]** 以建立後端集區。

### <a name="configure-a-load-balancer-health-probe"></a>設定負載平衡器健全狀況探查

1. 在 [負載平衡器] 分頁上，選取 [**健康情況探查**]。

1. 選取 [新增]。

1. 在 [**新增健康情況探查**] <span id="probe"></span>分頁上，設定下列健全狀況探查參數。

   - **名稱**：健全狀況探查的名稱。
   - **通訊協定**：TCP。
   - **埠**：您在防火牆中為[此步驟](#ports)中的健康狀態探查所建立的埠。 在本文中，範例會使用 TCP 通訊埠 `59999`。
   - **間隔**：5 秒。
   - **狀況不良臨界值**：2 次連續失敗。

1. 選取 [確定]。

### <a name="set-load-balancing-rules"></a>設定負載平衡規則

1. 在 [負載平衡器] 分頁上，選取 [**負載平衡規則**]。

1. 選取 [新增]。

1. 設定負載平衡規則參數：

   - **名稱**︰負載平衡規則的名稱。
   - **前端 ip 位址**： SQL Server FCI 叢集網路資源的 ip 位址。
   - **埠**： SQL SERVER FCI TCP 埠。 預設執行個體連接埠為 1433。
   - **後端埠**：當您啟用**浮動 IP （伺服器直接回傳）** 時，會使用與**埠**值相同的埠。
   - **後端集**區：您稍早設定的後端集區名稱。
   - **健康情況探查**：您先前設定的健康情況探查。
   - **工作階段持續性**：無。
   - **閒置逾時 (分鐘)** ：4。
   - **浮動 IP （伺服器直接回傳）** ：已啟用。

1. 選取 [確定]。

## <a name="step-7-configure-the-cluster-for-the-probe"></a>步驟7：設定探查的叢集

設定 PowerShell 中的叢集探查連接埠參數。

若要設定叢集探查埠參數，請以您環境中的值更新下列腳本中的變數。 從腳本中移除角括弧（`<` 和 `>`）。

   ```powershell
   $ClusterNetworkName = "<Cluster Network Name>"
   $IPResourceName = "<SQL Server FCI IP Address Resource Name>" 
   $ILBIP = "<n.n.n.n>" 
   [int]$ProbePort = <nnnnn>

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

下列清單描述您需要更新的值：

   - `<Cluster Network Name>`：網路的 Windows Server 容錯移轉叢集名稱。 在 [**容錯移轉叢集管理員** > **網路**] 中，以滑鼠右鍵按一下網路，然後選取 [**屬性**]。 正確的值在 [一般] 索引標籤的 [名稱] 底下。

   - `<SQL Server FCI IP Address Resource Name>`： SQL Server FCI IP 位址資源名稱。 在 [**容錯移轉叢集管理員** > **角色**] SQL Server 的 [伺服器名稱] 下，以滑鼠右鍵按一下 [**伺服器名稱**] 底下的 [IP 位址] 資源，然後選取 [**屬性**]。 正確的值在 [一般] 索引標籤的 [名稱] 底下。

   - `<ILBIP>`：ILB IP 位址。 此位址在 Azure 入口網站中會設定為 ILB 前端位址。 這也是 SQL Server FCI IP 位址。 您可以在 [容錯移轉叢集管理員] 中，您找到 `<SQL Server FCI IP Address Resource Name>` 所在位置的相同內容頁面上找到該位址。  

   - `<nnnnn>`：您在負載平衡器健康情況探查中設定的探查埠。 任何未使用的 TCP 連接埠都有效。

>[!IMPORTANT]
>叢集參數的子網路遮罩必須是 TCP IP 廣播位址：`255.255.255.255`。

設定叢集探查之後，您可以在 PowerShell 中看到所有叢集參數。 執行此指令碼︰

   ```powershell
   Get-ClusterResource $IPResourceName | Get-ClusterParameter
  ```

## <a name="step-8-test-fci-failover"></a>步驟8：測試 FCI 容錯移轉

測試 FCI 的容錯移轉來驗證叢集功能。 請執行下列步驟：

1. 使用 RDP 連接到其中一個 SQL Server FCI 叢集節點。

1. 開啟 [容錯移轉叢集管理員]。 選取 [角色]。 請注意哪個節點擁有 SQL Server FCI 角色。

1. 以滑鼠右鍵按一下 SQL Server FCI 角色。

1. 選取 [**移動**]，然後選取 [**最佳可能的節點**]。

**容錯移轉叢集管理員**會顯示角色，且其資源會離線。 然後，資源會在其他節點中移動並恢復上線。

### <a name="test-connectivity"></a>測試連線能力

若要測試連線能力，請登入相同虛擬網路中的另一部虛擬機器。 開啟 [SQL Server Management Studio]，然後連接到 SQL Server FCI 名稱。

>[!NOTE]
>如有需要，您可以[下載 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)。

## <a name="limitations"></a>限制

Azure 虛擬機器支援 Windows Server 2019 上的 Microsoft 分散式交易協調器（MSDTC）與叢集共用磁片區（CSV）和[標準負載平衡器](../../../load-balancer/load-balancer-standard-overview.md)上的儲存體。

在 Azure 虛擬機器上，Windows Server 2016 或更早版本不支援 MSDTC，因為：

- 叢集 MSDTC 資源無法設定為使用共用存放裝置。 在 Windows Server 2016 上，如果您建立 MSDTC 資源，它不會顯示任何可供使用的共用存放裝置，即使有可用的存放裝置也一樣。 Windows Server 2019 中已修正此問題。
- 基本負載平衡器不會處理 RPC 埠。

## <a name="see-also"></a>另請參閱

- [Windows 叢集技術](/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server 容錯移轉叢集實例](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
