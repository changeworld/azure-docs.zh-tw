---
title: 使用進階檔案共用建立 SQL Server FCI - Azure 虛擬機器
description: 本文說明如何在 Azure 虛擬機器中，使用進階檔案共用建立 SQL Server 容錯移轉叢集執行個體。
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
ms.openlocfilehash: 60526dbeb3e221e6a2e4c6b900ff3a109d4cdf8f
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2020
ms.locfileid: "84032419"
---
# <a name="configure-a-sql-server-failover-cluster-instance-with-premium-file-share-on-azure-virtual-machines"></a>在 Azure 虛擬機器上使用進階檔案共用設定 SQL Server 容錯移轉叢集執行個體
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

本文說明如何在 Azure 虛擬機器中，使用[進階檔案共用](../../../storage/files/storage-how-to-create-premium-fileshare.md)建立 SQL Server 容錯移轉叢集執行個體 (FCI)。

進階檔案共用是支援 SSD 的持續低延遲檔案共用，可在 Windows Server 2012 或更新版本中，完全支援使用 SQL Server 2012 或更新版本的容錯移轉叢集執行個體。 進階檔案共用提供更大的彈性，因此無需停機，即可調整檔案共用的大小和規模。


## <a name="before-you-begin"></a>開始之前

在開始之前，有些事必須先了解並有所準備。

您應了解這些技術的操作方式：

- [Windows 叢集技術](/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server 容錯移轉叢集執行個體](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

其中一個需要注意的是，在 Azure IaaS VM 容錯移轉叢集上，我們建議一部伺服器 (叢集節點) 一個 NIC 以及單一的子網路。 Azure 網路有實體備援，因此 Azure IaaS VM 客體叢集上不需要有額外的 NIC 和子網路。 叢集驗證報告會警告只能連線單一網路上的節點。 您可忽略 Azure IaaS VM 容錯移轉叢集上的此一警告。

您也應對這些技術擁有一般程度的了解：

- [Azure 進階檔案共用](../../../storage/files/storage-how-to-create-premium-fileshare.md)
- [Azure 資源群組](../../../azure-resource-manager/management/manage-resource-groups-portal.md)

> [!IMPORTANT]
> 目前，只能透過 [SQL Server IaaS 代理程式延伸模組](sql-server-iaas-agent-extension-automate-management.md)的[輕量管理模式](sql-vm-resource-provider-register.md#management-modes)來支援 Azure 虛擬機器上 SQL Server 容錯移轉叢集執行個體。 若要從完整的延伸模組模式變更為輕量，請刪除對應 VM 的 **SQL 虛擬機器**資源，然後在輕量模式中使用 SQL VM 資源提供者來註冊這些資源。 使用 Azure 入口網站刪除 **SQL 虛擬機器**資源時，請**取消選取正確虛擬機器旁邊的核取方塊**。 完整的延伸模組支援自動化備份、修補和進階入口網站管理等功能。 以輕量管理模式重新安裝代理程式後，這些功能對 SQL VM 即不再有效。

進階檔案共用可提供符合許多工作負載需求的 IOPS 和輸送量容量。 針對需要大量 IO 的工作負載，請根據受控高階磁碟或 Ultra 磁碟，考慮[使用儲存空間直接存取的 SQL Server 容錯移轉叢集執行個體](failover-cluster-instance-storage-spaces-direct-manually-configure.md)。  

檢查環境的 IOPS 活動，並驗證進階檔案共用會在開始部署或移轉前，提供所需的 IOPS。 使用 Windows 效能監視器磁碟計數器來監視 SQL Server 資料、記錄檔和暫存資料庫檔案所需的總 IOPS (磁碟轉移操作/秒) 和輸送量 (磁碟位元組數/秒)。

許多工作負載都會出現高載 IO，所以最好在流量過高期間執行檢查，並記下最大值 IOPS 和平均 IOPS。 進階檔案共用會根據共用的大小提供 IOPS。 進階檔案共用也會提供附贈的高載用量，以供將 IO 高載到基準量的三倍，最多一個小時。

如需進階檔案共用效能的詳細資訊，請參閱[檔案共用效能層級](https://docs.microsoft.com/azure/storage/files/storage-files-planning#storage-tiers)。

### <a name="licensing-and-pricing"></a>授權和定價

在 Azure 虛擬機器上，可使用隨用隨付 (PAYG) 授權 SQL Server 或自備授權 (BYOL) VM 映像。 您選擇的映像類型會影響收費方式。

使用隨用隨付授權時，Azure 虛擬機器上 SQL Server 容錯移轉叢集執行個體 (FCI) 會為 FCI 包含被動節點在內的所有節點，產生費用。 如需詳細資訊，請參閱 [SQL Server Enterprise 虛擬機器定價](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/)。

若有包含軟體保證的 Enterprise 合約，則每個主動節點就可使用一個免費的被動 FCI 節點。 若要利用 Azure 的這項權益，可使用 BYOL VM 映像，然後在 FCI 的主動和被動節點上使用相同授權。 如需詳細資訊，請參閱 [Enterprise 合約](https://www.microsoft.com/Licensing/licensing-programs/enterprise.aspx)。

若要比較 Azure 虛擬機器上 SQL Server 的隨用隨付和 BYOL 授權，請參閱[開始使用 SQL VM](sql-server-on-azure-vm-iaas-what-is-overview.md#get-started-with-sql-vms)。

如需授權 SQL Server 的完整資訊，請參閱[定價](https://www.microsoft.com/sql-server/sql-server-2017-pricing)。

### <a name="filestream"></a>檔案資料流

使用進階檔案共用的容錯移轉叢集不支援 Filestream。 若要使用 Filestream，請使用[儲存空間直接存取](failover-cluster-instance-storage-spaces-direct-manually-configure.md)來部署叢集。

## <a name="prerequisites"></a>必要條件

建議先準備好下列項目，再完成本文中的步驟：

- Microsoft Azure 訂用帳戶。
- Azure 虛擬機器上的 Windows 網域。
- 有權在 Azure 虛擬機器和 Active Directory 中建立物件的網域使用者帳戶。
- 執行 SQL Server 服務的網域使用者帳戶，且可在掛接檔案共用時用以登入虛擬機器。  
- 具有足夠 IP 位址空間可容納這些元件的 Azure 虛擬網路和子網路：
   - 兩部虛擬機器。
   - 容錯移轉叢集的 IP 位址。
   - 每個 FCI 上一個 IP 位址。
- 設定 Azure 網路上的 DNS，並指向網域控制站。
- 根據資料檔案的資料庫儲存配額，將[進階檔案共用](../../../storage/files/storage-how-to-create-premium-fileshare.md)作為叢集磁碟機使用。
- 如果您使用的是 Windows Server 2012 R2 和更舊版本，則會需要使用另一個檔案共用來作為檔案共用見證，因為 Windows 2016 和更新版本支援雲端見證。 您可使用另一個 Azure 檔案共用，也可在其他的虛擬機器上使用檔案共用。 如果將要使用另一個 Azure 檔案共用，則掛接程序與叢集磁碟機使用進階檔案共用的程序相同。 

備妥這些先決條件後，即可開始建置容錯移轉叢集。 第一步是建立虛擬機器。

## <a name="step-1-create-the-virtual-machines"></a>步驟 1:建立虛擬機器

1. 使用訂用帳戶登入 [Azure 入口網站](https://portal.azure.com)。

1. [建立 Azure 可用性設定組](../../../virtual-machines/linux/tutorial-availability-sets.md)。

   可用性設定組群組虛擬機器遍佈在容錯網域與更新網域中， 其可確保應用程式不受單一失敗點影響，例如網路交換器或伺服器機架的電源裝置。

   若尚未建立虛擬機器的資源群組，請在建立 Azure 可用性設定組時建立。 若正在使用 Azure 入口網站建立可用性設定組，請採取這些步驟：

   1. 在 Azure 入口網站中，選取 [建立資源] 來開啟 Azure Marketplace。 搜尋 [可用性設定組]。
   1. 選取 [可用性設定組]。
   1. 選取 [建立]。
   1. 在 [建立可用性設定組] 下方，提供這些值：
      - **Name**：可用性設定組的名稱。
      - 訂用帳戶：您的 Azure 訂用帳戶。
      - **資源群組**：若想要使用現有的群組，請按一下 [選取現有項目]，然後從清單中選取群組。 否則，請選取 [新建] 並輸入群組的名稱。
      - **位置**：設定您計劃建立虛擬機器的位置。
      - **容錯網域**：使用預設值 (**3**)。
      - **更新網域**：使用預設值 (**5**)。
   1. 選取 [建立] 來建立可用性設定組。

1. 在可用性設定組中建立虛擬機器。

   在 Azure 可用性設定組中佈建兩部 SQL Server 虛擬機器。 如需指示，請參閱[在 Azure 入口網站中佈建 SQL Server 虛擬機器](create-sql-vm-portal.md)。

   將兩部虛擬機器放置於：

   - 與可用性設定組同一 Azure 資源群組。
   - 您網域控制站的相同網路上。
   - 在有足夠 IP 位址空間的子網路上，其可存放兩部虛擬機器及最後用在此叢集的所有 FCI。
   - Azure 可用性設定組中。

      >[!IMPORTANT]
      >建立虛擬機器後，即無法設定或變更可用性設定組。

   從 Azure Marketplace 中選擇映像。 您可使用包含 Windows Server 與 SQL Server 的 Azure Marketplace 映像，或使用只包含 Windows Server 的 Azure Marketplace 映像。 如需詳細資料，請參閱 [Azure 虛擬機器上的 SQL Server 概觀](sql-server-on-azure-vm-iaas-what-is-overview.md)。

   Azure 資源庫中官方 SQL Server 映像包含已安裝的 SQL Server 執行個體、SQL Server 安裝軟體與所需金鑰。

   >[!IMPORTANT]
   > 建立虛擬機器後，請移除預先安裝的獨立 SQL Server 執行個體。 將容錯移轉叢集與進階檔案共用設定為儲存體後，將會使用預先安裝的 SQL Server 媒體來建立 SQL Server FCI。

   或者，您也可以使用只包含作業系統的 Azure Marketplace 映像。 將容錯移轉叢集和進階檔案共用設定為儲存體後，請選擇 **Windows Server 2016 Datacenter** 映像，並安裝 SQL Server FCI。 此映像不包含 SQL Server 安裝媒體。 將 SQL Server 安裝媒體放在您可為每部伺服器執行的位置。

1. 在 Azure 建立虛擬機器後，使用 RDP 連線到每一部虛擬機器。

   當首次使用 RDP 連線到虛擬機器時，會出現提示詢問是否要允許可在網路上搜尋此電腦。 選取 [是]。

1. 若正在使用其中一個 SQL Sever 型虛擬機器映像，請移除 SQL Server 執行個體。

   1. 在 [程式和功能] 中，以滑鼠右鍵按一下 Microsoft SQL Server 201_ (64 位元)，然後選取 [解除安裝/變更]。
   1. 選取 [移除]。
   1. 選取預設執行個體。
   1. 移除所有在 [Database Engine 服務] 下的功能。 請勿移除 [共用功能]。 您會看到類似下列螢幕擷取畫面的內容：

        ![選取功能](./media/failover-cluster-instance-premium-file-share-manually-configure/03-remove-features.png)

   1. 選取 [下一步]，然後選取 [移除]。

1. <span id="ports"> </span> 開啟防火牆連接埠。  

   在每部虛擬機器上，開啟 Windows 防火牆上的下列連接埠：

   | 目的 | TCP 連接埠 | 注意
   | ------ | ------ | ------
   | SQL Server | 1433 | 適用於 SDL Server 預設執行個體的一般連接埠。 若您曾使用來自資源庫的映像，此連接埠會自動開啟。
   | 健全狀況探查 | 59999 | 任何開啟的 TCP 連接埠。 在接下來的步驟中，設定負載平衝器[健全狀況探查](#probe)和要使用此連接埠的叢集。
   | 檔案共用 | 445 | 檔案共用服務所使用的連接埠。

1. [將虛擬機器新增至既存的網域](availability-group-manually-configure-prerequisites-tutorial.md#joinDomain)。

建立並設定虛擬機器後，即可設定進階檔案共用。

## <a name="step-2-mount-the-premium-file-share"></a>步驟 2:掛接進階檔案共用

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後移至儲存體帳戶。
1. 移至 [檔案服務] 下的 [檔案共用]，然後選取想要用於 SQL 儲存體的進階檔案共用。
1. 選取 [連線]，以顯示檔案共用的連接字串。
1. 從下拉式清單中選取想要使用的磁碟機代號，然後將這兩個程式碼區塊都複製到記事本。


   :::image type="content" source="media/manually-configure-failover-cluster-instance-premium-file-share/premium-file-storage-commands.png" alt-text="從檔案共用連線入口網站複製這兩個 PowerShell 命令":::

1. 使用 SQL Server FCI 將用於服務帳戶的帳戶，用 RDP 連線到 SQL Server VM。
1. 開啟系統管理 PowerShell 命令主控台。
1. 執行先前在入口網站中工作時所儲存的命令。
1. 使用檔案總管或 [執行] 對話方塊 (Windows 標誌鍵 + r) 移至共用。 使用網路路徑 `\\storageaccountname.file.core.windows.net\filesharename`。 例如， `\\sqlvmstorageaccount.file.core.windows.net\sqlpremiumfileshare`

1. 在新連線的檔案共用上，至少建立一個資料夾，以放置 SQL 資料檔案。
1. 對要參與叢集的每部 SQL Server VM 重複這些步驟。

  > [!IMPORTANT]
  > - 請考慮為備份檔案使用不同的檔案共用，以儲存資料和記錄檔此一共用的 IOPS 和空間容量。 備份檔案可使用進階或標準檔案共用。
  > - 如果您使用的是 Windows 2012 R2 和更舊版本，請遵循這些相同步驟來掛接將要作為檔案共用見證的檔案共用。 

## <a name="step-3-configure-the-failover-cluster"></a>步驟 3：設定容錯移轉叢集

下一步是設定容錯移轉叢集。 在此步驟中，您將完成下列子步驟：

1. 新增 Windows Server 容錯移轉叢集功能。
1. 驗證叢集。
1. 建立容錯移轉叢集。
1. 建立雲端見證 (適用於 Windows Server 2016 和更新版本) 或檔案共用見證 (適用於 Windows Server 2012 R2 和更舊版本)。


### <a name="add-windows-server-failover-clustering"></a>新增 Windows Server 容錯移轉叢集

1. 使用身為本機系統管理員成員，且有權在 Active Directory 建立物件的網域帳戶，使用 RDP 連線到第一部虛擬機器。 在之後的設定程序中繼續使用此帳戶。

1. [將容錯移轉叢集新增至每部虛擬機器](availability-group-manually-configure-prerequisites-tutorial.md#add-failover-clustering-features-to-both-sql-server-vms)。

   若要從 UI 安裝容錯移轉叢集，請對兩部虛擬機器都採取這些步驟：
   1. 在 [伺服器管理員] 中，選取 [管理]，然後選取 [新增角色及功能]。
   1. 在 [新增角色及功能精靈] 中，一直選取 [下一步] 直到到達 [選取功能]。
   1. 在 [選取功能] 中，選取 [容錯移轉叢集]。 選取所有所需功能與管理工具。 選取 [新增功能]。
   1. 選取 [下一步]，然後選取 [完成] 以安裝功能。

   若要使用 PowerShell 安裝容錯移轉叢集，請在其中一部虛擬機器上從系統管理員 PowerShell 工作階段執行下列指令碼：

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

### <a name="validate-the-cluster"></a>驗證叢集

在 UI 中或使用 PowerShell 驗證叢集。

若要使用 UI 驗證叢集，請在其中一部虛擬機器上執行下列步驟：

1. 在 [伺服器管理員] 下，選取 [工具]，然後選取 [容錯移轉叢集管理員]。
1. 在 [容錯移轉叢集管理員] 下，選取 [動作]，然後選取 [驗證設定]。
1. 選取 [下一步] 。
1. 在 [選取伺服器或叢集] 下，輸入這兩部虛擬機器的名稱。
1. 在 [測試選項] 下，選取 [僅執行我選取的測試]。 選取 [下一步] 。
1. 在 [測試選取] 下，選取除 [儲存體] 和 [儲存空間直接存取] 以外的所有測試，如下所示：

   :::image type="content" source="media/manually-configure-failover-cluster-instance-premium-file-share/cluster-validation.png" alt-text="選取叢集驗證測試":::

1. 選取 [下一步] 。
1. 在 [確認] 下，選取 [下一步]。

[驗證設定精靈] 會執行驗證測試。

若要使用 PowerShell 驗證叢集，請在其中一部虛擬機器上，從系統管理員 PowerShell 工作階段執行下列指令碼：

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
   ```

驗證叢集後，請建立容錯移轉叢集。

### <a name="create-the-failover-cluster"></a>建立容錯移轉叢集

若要建立容錯移轉叢集，您需要：
- 將成為叢集節點的虛擬機器名稱。
- 容錯移轉叢集的名稱
- 容錯移轉叢集的 IP 位址。 您可使用在叢集節點同一 Azure 虛擬網路和子網路中未用過的 IP 位址。

#### <a name="windows-server-2012-through-windows-server-2016"></a>Windows Server 2012 到 Windows Server 2016

下列 PowerShell 指令碼會建立 Windows Server 2012 到 Windows Server 2016 的容錯移轉叢集。 使用節點名稱 (虛擬機器名稱) 和 Azure 虛擬網路的可用 IP 位址來更新指令碼。

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

#### <a name="windows-server-2019"></a>Windows Server 2019

下列 PowerShell 指令碼會建立 Windows Server 2019 的容錯移轉叢集。 如需詳細資訊，請參閱[容錯移轉叢集：叢集網路物件](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97)。 使用節點名稱 (虛擬機器名稱) 和 Azure 虛擬網路的可用 IP 位址來更新指令碼。

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```


### <a name="create-a-cloud-witness-win-2016-"></a>建立雲端見證 (Win 2016 +)

如果您使用的是 Windows Server 2016 和更新版本，將必須建立雲端見證。 雲端見證儲存在 Azure 儲存體 blob 中的新型叢集仲裁見證。 如此即不需要使用獨立的 VM 來裝載見證共用，也不需要使用獨立的檔案共用。

1. [建立容錯移轉叢集的雲端見證](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness)。

1. 建立 Blob 容器。

1. 儲存存取金鑰和容器 URL。

### <a name="configure-quorum"></a>設定仲裁 

若為 Windows Server 2016 和更新版本，請將叢集設定為使用剛才建立的雲端見證。 請遵循[在使用者介面中設定仲裁見證](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness)中的所有步驟。

針對 Windows Server 2012 R2 和更舊版本，請遵循[在使用者介面中設定仲裁見證](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness)的相同步驟，但在 [選取仲裁見證] 頁面中，選取 [設定檔案共用見證] 選項。 指定所配置的檔案共用為檔案共用見證，無論其為您在另一部虛擬機器上設定或從 Azure 掛接的檔案共用。 


## <a name="step-4-test-cluster-failover"></a>步驟 4：測試叢集容錯移轉

測試叢集的容錯移轉。 在 [容錯移轉叢集管理員] 中，以滑鼠右鍵按一下叢集，然後選取 [其他動作] > [Move Core Cluster Resource] \(移動核心叢集資源\) > [選取節點]，然後選取叢集的其他節點。 將核心叢集資源移到叢集的每個節點，再移回主要節點。 如果您可成功地將叢集移至每個節點，即可開始安裝 SQL Server。  

:::image type="content" source="media/manually-configure-failover-cluster-instance-premium-file-share/test-cluster-failover.png" alt-text="將核心資源移至其他節點以測試叢集容錯移轉":::

## <a name="step-5-create-the-sql-server-fci"></a>步驟 5：建立 SQL Server FCI

設定容錯移轉叢集之後，即可建立 SQL Server FCI。

1. 使用 RDP 連線到第一部虛擬機器。

1. 在 [容錯移轉叢集管理員] 中，確認所有核心叢集資源都位於第一部虛擬機器。 如有必要，請將所有資源移至此虛擬機器。

1. 找出安裝媒體。 若虛擬機器是使用其中一個 Azure Marketplace 映像，則媒體會位於 `C:\SQLServer_<version number>_Full`。 選取 [設定]。

1. 在 [SQL Server 安裝中心] 中，選取 [安裝]。

1. 選取 [新的 SQL Server 容錯移轉叢集安裝]。 請遵循精靈內的指示安裝 SQL Server FCI。

   FCI 資料目錄必須位於進階檔案共用中。 輸入共用的完整路徑，格式為：`\\storageaccountname.file.core.windows.net\filesharename\foldername`。 隨即會出現警告告知已指定檔案伺服器作為資料目錄。 這個警告是正常現象。 請確定在保存檔案共用時，使用者帳戶 (以 RDP 連線到 VM) 與 SQL Server 服務用來避免可能發生失敗的帳戶是同一帳戶。

   :::image type="content" source="media/manually-configure-failover-cluster-instance-premium-file-share/use-file-share-as-data-directories.png" alt-text="使用檔案共用作為 SQL 資料目錄":::

1. 完成精靈中的步驟後，安裝程式會在第一個節點上安裝 SQL Server FCI。

1. 安裝程式在第一個節點上安裝 FCI 後，請使用 RDP 連線到第二個節點。

1. 開啟 [SQL Server 安裝中心]。 選取 [安裝]。

1. 選取 [將節點新增到 SQL Server 容錯移轉叢集]。 遵循精靈中的指示來安裝 SQL Server，並將伺服器新增到 FCI。

   >[!NOTE]
   >若您曾透過 SQL Server 使用 Azure Marketplace 資源庫映像，映像會包含 SQL Server 工具。 若沒有用過其中一個映像，請另外安裝 SQL Server 工具。 請參閱[下載 Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)。

## <a name="step-6-create-the-azure-load-balancer"></a>步驟 6：建立 Azure Load Balancer

在 Azure 虛擬機器上，叢集會使用負載平衡器來保留每次均需在一個節點上的 IP 位址。 在此解決方案中，負載平衡器會保留 SQL Server FCI 的 IP 位址。

如需詳細資訊，請參閱[建立及設定 Azure Load Balancer](availability-group-manually-configure-tutorial.md#configure-internal-load-balancer)。

### <a name="create-the-load-balancer-in-the-azure-portal"></a>在 Azure 入口網站中建立負載平衡器

若要建立負載平衡器：

1. 在 Azure 入口網站中，前往包含虛擬機器的資源群組。

1. 選取 [新增]。 在 Azure Marketplace 內搜尋**負載平衡器**。 選取 [負載平衡器]。

1. 選取 [建立]。

1. 使用下列值設定負載平衡器：

   - 訂用帳戶：您的 Azure 訂用帳戶。
   - **資源群組**：包含虛擬機器的資源群組。
   - **Name**：能識別負載平衡器的名稱。
   - **區域**：包含虛擬機器的 Azure 位置。
   - **類型**：公用或私人。 私人負載平衡器可從虛擬網路內存取。 大部分的 Azure 應用程式都能使用私人負載平衡器。 若應用程式需要直接透過網際網路存取 SQL Server，請使用公用負載平衡器。
   - **SKU**：標準。
   - **虛擬網路**：與虛擬機器相同的網路。
   - **IP 位址指派**：靜態。 
   - **私人 IP 位址**：您指派給 SQL Server FCI 叢集網路資源的 IP 位址。

   下列映像會顯示 [建立負載平衡器] UI：

   ![設定負載平衡器](./media/failover-cluster-instance-premium-file-share-manually-configure/30-load-balancer-create.png)
   

### <a name="configure-the-load-balancer-backend-pool"></a>設定負載平衡器後端集區

1. 返回包含虛擬機器的 Azure 資源群組，並找到新的負載平衡器。 您可能需要在資源群組上重新整理檢視。 選取負載平衡器。

1. 選取 [後端集區]，然後選取 [新增]。

1. 將後端集區關聯至包含 VM 的可用性設定組。

1. 在 [目標網路 IP 設定] 下方，選取 [虛擬機器]，並選擇將作為叢集節點加入的虛擬機器。 請務必包含將裝載 FCI 的所有虛擬機器。

1. 選取 [確定] 以建立後端集區。

### <a name="configure-a-load-balancer-health-probe"></a>設定負載平衡器健全狀況探查

1. 在 [負載平衡器] 刀鋒視窗上，選取 [健康狀態探查]。

1. 選取 [新增]。

1. 在 [新增健康狀態探查] 刀鋒視窗中，<span id="probe"></span>設定下列健康狀態探查參數。

   - **Name**：健康情況探查的名稱。
   - **通訊協定**：TCP。
   - **連接埠**：在[此步驟](#ports)中，於防火牆內為健康狀態探查建立的連接埠。 本文中的範例使用 TCP 連接埠 `59999`。
   - **間隔**：5 秒。
   - **狀況不良閾值**：2 次連續失敗。

1. 選取 [確定]。

### <a name="set-load-balancing-rules"></a>設定負載平衡規則

1. 在負載平衡器刀鋒視窗中，選取 [負載平衡規則]。

1. 選取 [新增]。

1. 設定負載平衡規則參數：

   - **Name**：負載平衡規則的名稱。
   - **前端 IP 位址**：SQL Server FCI 叢集網路資源的 IP 位址。
   - **連接埠**：SQL Server FCI TCP 連接埠。 預設執行個體連接埠為 1433。
   - **後端連接埠**：啟用 [浮動 IP (伺服器直接回傳)] 時，請使用與 [連接埠] 值相同的連接埠。
   - **後端集區**：先前設定的後端集區名稱。
   - **健康情況探查**：先前設定的健康狀態探查。
   - **工作階段持續性**：無。
   - **閒置逾時 (分鐘)** ：4.
   - **浮動 IP (伺服器直接回傳)** ：已啟用。

1. 選取 [確定]。

## <a name="step-7-configure-the-cluster-for-the-probe"></a>步驟 7：設定探查的叢集

設定 PowerShell 中的叢集探查連接埠參數。

若要設定叢集探查連接埠參數，請將下列指令碼中的變數更新為環境中的值。 移除指令碼中的角括弧 (`<` 和 `>`)。

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

## <a name="step-8-test-fci-failover"></a>步驟 8：測試 FCI 容錯移轉

測試 FCI 的容錯移轉來驗證叢集功能。 請執行下列步驟：

1. 使用 RDP 連線至其中一個 SQL Server FCI 叢集節點。

1. 開啟 [容錯移轉叢集管理員]。 選取 [角色]。 請注意哪個節點擁有 SQL Server FCI 角色。

1. 以滑鼠右鍵按一下 SQL Server FCI 角色。

1. 選取 [移動]，然後選取 [最佳可行節點]。

[容錯移轉叢集管理員] 會顯示角色，其資源則會變成離線狀態。 接著資源會移動，並在另一個節點上線。

### <a name="test-connectivity"></a>測試連線能力

若要測試連線能力，請登入相同虛擬網路中的另一部虛擬機器。 開啟 [SQL Server Management Studio]，然後連接到 SQL Server FCI 名稱。

>[!NOTE]
>若有需要，您可[下載 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)。

## <a name="limitations"></a>限制

Azure 虛擬機器支援 Windows Server 2019 上的 Microsoft 分散式交易協調器 (MSDTC)，且可使用叢集共用磁碟區 (CSV) 上的儲存體和 [Standard Load Balancer](../../../load-balancer/load-balancer-standard-overview.md)。

在 Azure 虛擬機器上，Windows Server 2016 或更早版本不支援 MSDTC，其原因為：

- 叢集化的 MSDTC 資源無法設為使用共用儲存體。 若在 Windows Server 2016 上建立 MSDTC 資源，即使有儲存體可用，系統也不會顯示任何可用的共用儲存體。 Windows Server 2019 中已修正此問題。
- 基本負載平衡器不處理 RPC 連接埠。

## <a name="see-also"></a>另請參閱

- [Windows 叢集技術](/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server 容錯移轉叢集執行個體](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
