---
title: SQL Server FCI - Azure 虛擬機器 | Microsoft Docs
description: 本文說明如何在 Azure 虛擬機器上建立 SQL Server 容錯移轉叢集實例。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79249798"
---
# <a name="configure-a-sql-server-failover-cluster-instance-on-azure-virtual-machines"></a>在 Azure 虛擬機器上設定 SQL Server 容錯移轉叢集實例

本文說明如何在 Azure Resource Manager 模型中的 Azure 虛擬機器上建立 SQL Server 容錯移轉叢集實例（FCI）。 此解決方案使用[Windows Server 2016 Datacenter edition 儲存空間直接存取](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview)作為以軟體為基礎的虛擬 SAN，以同步處理 Windows 叢集中節點（Azure vm）之間的儲存體（資料磁片）。 儲存空間直接存取是 Windows Server 2016 中的新功能。

下圖顯示 Azure 虛擬機器中的完整解決方案：

![完整解決方案](./media/virtual-machines-windows-portal-sql-create-failover-cluster/00-sql-fci-s2d-complete-solution.png)

下圖顯示：

- Windows Server 容錯移轉叢集中的兩部 Azure 虛擬機器。 當虛擬機器在容錯移轉叢集中時，也稱為叢集*節點*或*節點*。
- 每部虛擬機器有兩個以上的資料磁碟。
- 儲存空間直接存取會同步處理資料磁片上的資料，並以存放集區形式呈現已同步處理的儲存體。
- 存放集區會向容錯移轉叢集呈現叢集共用磁碟區（CSV）。
- SQL Server FCI 叢集角色會針對資料硬碟使用 CSV。
- 保留 SQL Server FCI IP 位址的 Azure Load Balancer。
- 保留所有資源的 Azure 可用性設定組。

>[!NOTE]
>圖表中的所有 Azure 資源都位於相同的資源群組中。

如需儲存空間直接存取的詳細資訊，請參閱[Windows Server 2016 Datacenter edition 儲存空間直接存取](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview)。

儲存空間直接存取支援兩種類型的架構：聚合式和超交集。 本文件中的架構為超交集。 超交集基礎結構會將儲存體放置於與裝載叢集應用程式相同的伺服器上。 在此架構中，儲存體會放置在每個 SQL Server FCI 節點上。

## <a name="licensing-and-pricing"></a>授權和定價

在 Azure 虛擬機器上，您可以使用隨用隨付（PAYG）或攜帶您自己的授權（BYOL） VM 映射來授權 SQL Server。 您選擇的映射類型會影響向您收費的方式。

使用隨用隨付授權時，Azure 虛擬機器上 SQL Server 的容錯移轉叢集實例（FCI）會產生 FCI 所有節點的費用，包括被動節點。 如需詳細資訊，請參閱 [SQL Server Enterprise 虛擬機器定價](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/)。

如果您具有軟體保證的 Enterprise 合約，就可以針對每個使用中節點使用一個免費的被動 FCI 節點。 若要利用 Azure 中的這項權益，請使用 BYOL VM 映射，並在 FCI 的主動和被動節點上使用相同的授權。 如需詳細資訊，請參閱 [Enterprise 合約](https://www.microsoft.com/Licensing/licensing-programs/enterprise.aspx)。

若要比較 Azure 虛擬機器上 SQL Server 的隨用隨付和 BYOL 授權，請參閱[開始使用 SQL vm](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms)。

如需授權 SQL Server 的完整資訊，請參閱[定價](https://www.microsoft.com/sql-server/sql-server-2017-pricing)。

### <a name="example-azure-template"></a>Azure 範本範例

您可以從範本在 Azure 中建立此整個解決方案。 GitHub [Azure 快速入門範本](https://github.com/MSBrett/azure-quickstart-templates/tree/master/sql-server-2016-fci-existing-vnet-and-ad) 內有提供範本範例。 這個範例不是針對任何特定的工作負載而設計或測試。 您可以執行範本來建立 SQL Server FCI，並將儲存空間直接存取儲存體連線到您的網域。 您可以評估範本，並針對您的用途加以修改。

## <a name="before-you-begin"></a>開始之前

在開始之前，您必須知道並準備好一些事項。

### <a name="what-to-know"></a>注意事項
您應該對這些技術有操作的瞭解：

- [Windows 叢集技術](https://docs.microsoft.com/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server 容錯移轉叢集實例](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

有一點要注意的是，在 Azure IaaS VM 來賓容錯移轉叢集上，我們建議每個伺服器（叢集節點）和單一子網都有一個 NIC。 Azure 網路有實體冗余，因此在 Azure IaaS VM 來賓叢集上不需要額外的 Nic 和子網。 叢集驗證報告會警告您，節點只能在單一網路上連線。 您可以在 Azure IaaS VM 來賓容錯移轉叢集上忽略此警告。

您也應該對這些技術有大致的瞭解：

- [在 Windows Server 2016 中使用儲存空間直接存取的超融合式解決方案](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview)
- [Azure 資源群組](../../../azure-resource-manager/management/manage-resource-groups-portal.md)

> [!IMPORTANT]
> 目前，只有[SQL Server IaaS 代理程式擴充](virtual-machines-windows-sql-server-agent-extension.md)功能的[輕量管理模式](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes)才支援 Azure 虛擬機器上的 SQL Server 容錯移轉叢集實例。 若要從完整延伸模式變更為輕量，請刪除對應 Vm 的**Sql 虛擬機器**資源，然後在輕量模式中向 sql VM 資源提供者註冊。 使用 Azure 入口網站刪除**SQL 虛擬機器**資源時，請**清除正確虛擬機器旁的核取方塊**。 完整延伸模組支援自動備份、修補和先進入口網站管理等功能。 在輕量管理模式中重新安裝代理程式之後，這些功能將無法在 SQL Vm 上使用。

### <a name="what-to-have"></a>應備項目

在完成本文中的步驟之前，您應該已經具備：

- Microsoft Azure 訂用帳戶。
- Azure 虛擬機器上的 Windows 網域。
- 具有在 Azure 虛擬機器和 Active Directory 中建立物件之許可權的帳戶。
- 具有足夠 IP 位址空間可供這些元件使用的 Azure 虛擬網路和子網：
   - 兩部虛擬機器。
   - 容錯移轉叢集的 IP 位址。
   - 每個 FCI 上一個 IP 位址。
- 在 Azure 網路上設定的 DNS，指向網域控制站。

準備好這些必要條件之後，您就可以開始建立您的容錯移轉叢集。 第一步是建立虛擬機器。

## <a name="step-1-create-the-virtual-machines"></a>步驟1：建立虛擬機器

1. 使用您的訂用帳戶登入[Azure 入口網站](https://portal.azure.com)。

1. [建立 Azure 可用性設定組](../tutorial-availability-sets.md)。

   可用性設定組群組虛擬機器遍佈在容錯網域與更新網域中， 它可確保您的應用程式不會受到單一失敗點影響，例如網路交換器或伺服器機架的電源裝置。

   如果您尚未建立虛擬機器的資源群組，請在建立 Azure 可用性設定組時執行此動作。 如果您要使用 Azure 入口網站來建立可用性設定組，請執行下列步驟：

   1. 在 [Azure 入口網站中，選取 [**建立資源**] 以開啟 Azure Marketplace。 搜尋 [可用性設定組]****。
   1. 選取**可用性設定組**。
   1. 選取 [建立]  。
   1. 在 [**建立可用性設定組**] 底下，提供下列值：
      - **名稱**：可用性設定組的名稱。
      - **訂**用帳戶：您的 Azure 訂用帳戶。
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

   根據您要支付 SQL Server 授權的方式，選擇正確的影像：

   - **按使用量付費授權**。 費用會以秒計算，且會包含下列 SQL Server 授權：
      - **Windows Server 2016 Datacenter 上的 SQL Server 2016 Enterprise**
      - **Windows Server 2016 Datacenter 上的 SQL Server 2016 Standard**
      - **Windows Server 2016 Datacenter 上的 SQL Server 2016 開發人員**

   - **自備授權（BYOL）**

      - **BYOLWindows Server 2016 Datacenter 上的 SQL Server 2016 Enterprise**
      - **BYOLWindows Server 2016 Datacenter 上的 SQL Server 2016 Standard**

   >[!IMPORTANT]
   >建立虛擬機器後，請移除預先安裝的獨立 SQL Server 執行個體。 在設定容錯移轉叢集和儲存空間直接存取之後，您將使用預先安裝的 SQL Server 媒體來建立 SQL Server FCI。

   或者，您可以使用只包含作業系統的 Azure Marketplace 映射。 在您設定容錯移轉叢集和儲存空間直接存取之後，請選擇**Windows Server 2016 Datacenter**映射並安裝 SQL Server FCI。 此映射不包含 SQL Server 安裝媒體。 將 SQL Server 安裝媒體放在您可以為每部伺服器執行它的位置。

1. 在 Azure 建立您的虛擬機器之後，請使用 RDP 連線到每一部。

   當您第一次使用 RDP 連線到虛擬機器時，系統會詢問您是否要允許電腦可在網路上探索。 選取 [是]  。

1. 如果您使用其中一個以 SQL Server 為基礎的虛擬機器映射，請移除 SQL Server 實例。

   1. 在 [**程式和功能**] 中，以滑鼠右鍵按一下 **[Microsoft SQL Server 2016 （64位）** ]，然後選取 [**卸載/變更**]。
   1. 選取 [移除]****。
   1. 選取預設執行個體。
   1. 移除所有在 [Database Engine 服務]**** 下的功能。 請勿移除**共用的功能**。 您會看到類似下列螢幕擷取畫面的內容：

      ![選取功能](./media/virtual-machines-windows-portal-sql-create-failover-cluster/03-remove-features.png)

   1. 選取 **[下一步]**，然後選取 [**移除**]。

1. <a name="ports"></a>開啟防火牆連接埠。

   在每部虛擬機器上，開啟 Windows 防火牆上的下列埠：

   | 目的 | TCP 連接埠 | 備忘錄
   | ------ | ------ | ------
   | SQL Server | 1433 | 適用於 SDL Server 預設執行個體的一般連接埠。 若您曾使用來自資源庫的映像，此連接埠會自動開啟。
   | 健全狀況探查 | 59999 | 任何開啟的 TCP 連接埠。 在接下來的步驟中，設定負載平衝器[健全狀況探查](#probe)和要使用此連接埠的叢集。  

1. 將儲存體新增至虛擬機器中。 如需詳細資訊，請參閱[新增儲存區](../disks-types.md)。

   每部虛擬機器需要至少兩個資料磁碟。

   連接原始磁片，而不是 NTFS 格式的磁片。
      >[!NOTE]
      >如果您附加 NTFS 格式的磁片，您只能在沒有磁片資格檢查的情況下啟用儲存空間直接存取。  

   每部 VM 至少可連接兩個進階 SSD。 我們建議至少 P30 （1 TB）的磁片。

   將主機快取設為**唯讀**。

   在生產環境中使用的儲存體容量是依您的工作負載而定。 本文所描述的值僅供示範與測試之用。

1. [將虛擬機器新增至既存的網域](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain)。

建立和設定虛擬機器之後，您可以設定容錯移轉叢集。

## <a name="step-2-configure-the-windows-server-failover-cluster-with-storage-spaces-direct"></a>步驟2：使用儲存空間直接存取設定 Windows Server 容錯移轉叢集

下一個步驟是使用儲存空間直接存取設定容錯移轉叢集。 在此步驟中，您將完成下列子步驟：

1. 新增 Windows Server 容錯移轉叢集功能。
1. 驗證叢集。
1. 建立容錯移轉叢集。
1. 建立雲端見證。
1. 新增儲存體。

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

如需後續步驟的進一步參考，請參閱[使用 Windows Server 2016 中的儲存空間直接存取超融合解決方案](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-3-configure-storage-spaces-direct)的步驟3底下的指示。

### <a name="validate-the-cluster"></a>驗證叢集

在 UI 中或使用 PowerShell 來驗證叢集。

若要使用 UI 來驗證叢集，請在其中一部虛擬機器上執行下列步驟：

1. 在 [**伺服器管理員**] 底下，選取 [**工具**]，然後選取 [**容錯移轉叢集管理員**]。
1. 在 [**容錯移轉叢集管理員**] 底下，選取 [**動作**]，然後選取 [**驗證**設定]。
1. 選取 [下一步]  。
1. 在 [**選取伺服器或**叢集] 下，輸入兩部虛擬機器的名稱。
1. 在 [**測試選項**] 底下，選取 [**僅執行我選取的測試**]。 選取 [下一步]  。
1. 在 [**測試選取範圍**] 底下，選取 [**儲存體**] 以外的所有測試，如下所示：

   ![選取叢集驗證測試](./media/virtual-machines-windows-portal-sql-create-failover-cluster/10-validate-cluster-test.png)

1. 選取 [下一步]  。
1. 在 [**確認**] 下選取 **[下一步]**。

[驗證設定精靈] 會執行驗證測試。

若要使用 PowerShell 驗證叢集，請從其中一部虛擬機器上的系統管理員 PowerShell 會話執行下列腳本：

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Storage Spaces Direct", "Inventory", "Network", "System Configuration"
   ```

驗證叢集後，請建立容錯移轉叢集。

### <a name="create-the-failover-cluster"></a>建立容錯移轉叢集

若要建立容錯移轉叢集，您需要：
- 將成為叢集節點的虛擬機器名稱。
- 容錯移轉叢集的名稱
- 容錯移轉叢集的 IP 位址。 您可以使用未在與叢集節點相同的 Azure 虛擬網路和子網上使用的 IP 位址。

#### <a name="windows-server-2008-through-windows-server-2016"></a>Windows Server 2008 到 Windows Server 2016

下列 PowerShell 腳本會建立 Windows Server 2008 到 Windows Server 2016 的容錯移轉叢集。 使用節點名稱（虛擬機器名稱）和 Azure 虛擬網路中可用的 IP 位址來更新腳本。

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

#### <a name="windows-server-2019"></a>Windows Server 2019

下列 PowerShell 腳本會建立適用于 Windows Server 2019 的容錯移轉叢集。 如需詳細資訊，請參閱[容錯移轉叢集：叢集網路物件](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97)。 使用節點名稱（虛擬機器名稱）和 Azure 虛擬網路中可用的 IP 位址來更新腳本。

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```


### <a name="create-a-cloud-witness"></a>建立雲端見證

雲端見證是一種新類型的叢集仲裁見證，儲存在 Azure 儲存體 blob 中。 這將不再需要裝載見證共用的個別 VM。

1. [建立容錯移轉叢集的雲端見證](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness)。

1. 建立 Blob 容器。

1. 儲存存取金鑰和容器 URL。

1. 設定容錯移轉叢集的叢集仲裁見證。 請參閱在[使用者介面中設定仲裁見證](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness)。

### <a name="add-storage"></a>新增儲存體

儲存空間直接存取的磁片必須是空的。 它們不能包含分割區或其他資料。 若要清除磁片，請依照[本指南中的步驟](https://docs.microsoft.com/windows-server/storage/storage-spaces/deploy-storage-spaces-direct?redirectedfrom=MSDN#step-31-clean-drives)進行。

1. [啟用儲存空間直接](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-35-enable-storage-spaces-direct)存取。

   下列 PowerShell 腳本會啟用儲存空間直接存取：  

   ```powershell
   Enable-ClusterS2D
   ```

   現在，您可以在**容錯移轉管理員**中看到儲存集區。

1. [建立磁片](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-36-create-volumes)區。

   當您啟用存放集區時，儲存空間直接存取會自動加以建立。 您現在已經準備好建立磁片區。 PowerShell Cmdlet `New-Volume`會自動執行磁片區建立程式。 此套裝程式括格式化、將磁片區新增至叢集，以及建立叢集共用磁碟區（CSV）。 這個範例會建立 800 gb 的 CSV：

   ```powershell
   New-Volume -StoragePoolFriendlyName S2D* -FriendlyName VDisk01 -FileSystem CSVFS_REFS -Size 800GB
   ```   

   此命令完成之後，800-GB 磁片區會掛接為叢集資源。 磁碟區會位於 `C:\ClusterStorage\Volume1\`。

   這個螢幕擷取畫面顯示具有儲存空間直接存取的叢集共用磁碟區：

   ![叢集共用磁碟區](./media/virtual-machines-windows-portal-sql-create-failover-cluster/15-cluster-shared-volume.png)

## <a name="step-3-test-failover-cluster-failover"></a>步驟 3︰測試容錯移轉叢集的容錯移轉

在**容錯移轉叢集管理員**中，確認您可以將儲存體資源移到另一個叢集節點。 如果您可以使用**容錯移轉叢集管理員**連線到容錯移轉叢集，並將存放裝置從一個節點移到另一個，您就可以開始設定 FCI。

## <a name="step-4-create-the-sql-server-fci"></a>步驟4：建立 SQL Server FCI

設定容錯移轉叢集和所有叢集元件（包括儲存體）之後，您可以建立 SQL Server FCI。

1. 使用 RDP 連接到第一部虛擬機器。

1. 在**容錯移轉叢集管理員**中，請確定所有核心叢集資源都在第一部虛擬機器上。 如有必要，請將所有資源移到該虛擬機器。

1. 找出安裝媒體。 若虛擬機器是使用其中一個 Azure Marketplace 映像，則媒體會位於 `C:\SQLServer_<version number>_Full`。 選取 [設定]****。

1. 在**SQL Server 安裝中心**] 中，選取 [**安裝**]。

1. 選取 [**新增 SQL Server 容錯移轉叢集安裝**]。 請遵循精靈內的指示安裝 SQL Server FCI。

   FCI 資料目錄必須位於叢集儲存體中。 使用儲存空間直接存取時，它不是共用磁片，而是每部伺服器上的磁片區掛接點。 儲存空間直接存取在兩個節點之間同步處理磁片區。 磁片區會以叢集共用磁碟區形式呈現給叢集。 在資料目錄上使用 CSV 掛接點。

   ![資料目錄](./media/virtual-machines-windows-portal-sql-create-failover-cluster/20-data-dicrectories.png)

1. 當您完成嚮導中的指示之後，安裝程式將會在第一個節點上安裝 SQL Server FCI。

1. 安裝程式在第一個節點上安裝 FCI 之後，請使用 RDP 連接到第二個節點。

1. 開啟 [SQL Server 安裝中心]****。 選取 [**安裝**]。

1. 選取 [**將節點加入至 SQL Server 容錯移轉**叢集]。 遵循嚮導中的指示，安裝 SQL Server 並將伺服器新增至 FCI。

   >[!NOTE]
   >如果您使用包含 SQL Server 的 Azure Marketplace 圖庫映射，則影像中會包含 SQL Server 工具。 如果您未使用這些映射的其中一個，請分別安裝 SQL Server 工具。 請參閱[下載 Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)。

## <a name="step-5-create-the-azure-load-balancer"></a>步驟5：建立 Azure 負載平衡器

在 Azure 虛擬機器上，叢集會使用負載平衡器來保留每次均需在一個節點上的 IP 位址。 在此解決方案中，負載平衡器會保留 SQL Server FCI 的 IP 位址。

如需詳細資訊，請參閱[建立和設定 Azure 負載平衡器](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer)。

### <a name="create-the-load-balancer-in-the-azure-portal"></a>在 Azure 入口網站中建立負載平衡器

若要建立負載平衡器：

1. 在 [Azure 入口網站中，移至包含虛擬機器的資源群組。

1. 選取 [新增]  。 搜尋**Load Balancer**的 Azure Marketplace。 選取 [ **Load Balancer**]。

1. 選取 [建立]  。

1. 使用下列項目設定負載平衡器：

   - **訂**用帳戶：您的 Azure 訂用帳戶。
   - **資源群組**：包含您的虛擬機器的資源群組。
   - **名稱**︰用以識別負載平衡器的名稱。
   - **區域**：包含您的虛擬機器的 Azure 位置。
   - **類型**： [公用] 或 [私用]。 您可以從虛擬網路內部存取私人負載平衡器。 大部分的 Azure 應用程式都能使用私人負載平衡器。 如果您的應用程式需要直接透過網際網路存取 SQL Server，請使用公用負載平衡器。
   - **SKU**：標準。
   - **虛擬網路**：與虛擬機器相同的網路。
   - **IP 位址指派**：靜態。 
   - **私人 IP 位址**：您指派給 SQL Server FCI 叢集網路資源的 ip 位址。

 下列螢幕擷取畫面顯示 [**建立負載平衡器**] UI：

   ![設定負載平衡器](./media/virtual-machines-windows-portal-sql-create-failover-cluster/30-load-balancer-create.png)

### <a name="configure-the-load-balancer-backend-pool"></a>設定負載平衡器後端集區

1. 返回包含虛擬機器的 Azure 資源群組，並找出新的負載平衡器。 您可能需要重新整理資源群組上的視圖。 選取負載平衡器。

1. 選取 [**後端**集區]，然後選取 [**新增**]。

1. 將後端集區關聯至包含 VM 的可用性設定組。

1. 在 [**目標網路 IP 設定**] 底下，選取 [**虛擬機器**]，然後選擇將參與做為叢集節點的虛擬機器。 請務必包含將裝載 FCI 的所有虛擬機器。

1. 選取 **[確定]** 以建立後端集區。

### <a name="configure-a-load-balancer-health-probe"></a>設定負載平衡器健全狀況探查

1. 在 [負載平衡器] 分頁上，選取 [**健康情況探查**]。

1. 選取 [新增]  。

1. 在 [**新增健康情況探查**] <a name="probe"></a>分頁上，設定健康情況探查參數。

   - **名稱**：健全狀況探查的名稱。
   - **通訊協定**： TCP。
   - **埠**：將設定為您在[此步驟](#ports)中為健康情況探查在防火牆中建立的埠。 在本文中，範例會使用 TCP 通訊`59999`埠。
   - **間隔**：5 秒。
   - **狀況不良臨界值**：2 次連續失敗。

1. 選取 [確定]  。

### <a name="set-load-balancing-rules"></a>設定負載平衡規則

1. 在 [負載平衡器] 分頁上，選取 [**負載平衡規則**]。

1. 選取 [新增]  。

1. 設定負載平衡規則參數：

   - **名稱**︰負載平衡規則的名稱。
   - **前端 ip 位址**： SQL Server FCI 叢集網路資源的 ip 位址。
   - **埠**： SQL SERVER FCI TCP 埠。 預設執行個體連接埠為 1433。
   - **後端埠**：當您啟用**浮動 IP （伺服器直接回傳）** 時，會使用與**埠**值相同的埠。
   - **後端集**區：您稍早設定的後端集區名稱。
   - **健康情況探查**：您先前設定的健康情況探查。
   - **工作階段持續性**：無。
   - **閒置逾時 (分鐘)**：4。
   - **浮動 IP （伺服器直接回傳）**：已啟用。

1. 選取 [確定]  。

## <a name="step-6-configure-the-cluster-for-the-probe"></a>步驟6：設定探查的叢集

設定 PowerShell 中的叢集探查連接埠參數。

若要設定叢集探查埠參數，請以您環境中的值更新下列腳本中的變數。 從腳本中移除角`<`括弧`>`（和）。

   ```powershell
   $ClusterNetworkName = "<Cluster Network Name>"
   $IPResourceName = "<SQL Server FCI IP Address Resource Name>" 
   $ILBIP = "<n.n.n.n>" 
   [int]$ProbePort = <nnnnn>

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

下列清單描述您需要更新的值：

   - `<Cluster Network Name>`：網路的 Windows Server 容錯移轉叢集名稱。 在 [**容錯移轉叢集管理員** > **網路**] 中，以滑鼠右鍵按一下網路，然後選取 [**屬性**]。 正確的值在 [一般]**** 索引標籤的 [名稱]**** 底下。

   - `<SQL Server FCI IP Address Resource Name>`： SQL Server FCI IP 位址資源名稱。 在**容錯移轉叢集管理員** > **角色**的 [SQL Server FCI] 角色底下的 [**伺服器名稱**] 底下，以滑鼠右鍵按一下 [IP 位址] 資源，然後選取 [**屬性**]。 正確的值在 [一般]**** 索引標籤的 [名稱]**** 底下。 

   - `<ILBIP>`：ILB IP 位址。 此位址在 Azure 入口網站中會設定為 ILB 前端位址。 這也是 SQL Server FCI IP 位址。 您可以在 [容錯移轉叢集管理員]**** 中，您找到 `<SQL Server FCI IP Address Resource Name>` 所在位置的相同內容頁面上找到該位址。  

   - `<nnnnn>`：您在負載平衡器健康情況探查中設定的探查埠。 任何未使用的 TCP 連接埠都有效。

>[!IMPORTANT]
>叢集參數的子網路遮罩必須是 TCP IP 廣播位址：`255.255.255.255`。

設定叢集探查之後，您可以在 PowerShell 中看到所有叢集參數。 執行此指令碼︰

   ```powershell
   Get-ClusterResource $IPResourceName | Get-ClusterParameter 
  ```

## <a name="step-7-test-fci-failover"></a>步驟 7：測試 FCI 容錯移轉

測試 FCI 的容錯移轉來驗證叢集功能。 請執行下列步驟：

1. 使用 RDP 連接到其中一個 SQL Server FCI 叢集節點。

1. 開啟**容錯移轉叢集管理員**。 選取 [角色]****。 請注意哪個節點擁有 SQL Server FCI 角色。

1. 以滑鼠右鍵按一下 SQL Server FCI 角色。

1. 選取 [**移動**]，然後選取 [**最佳可能的節點**]。

**容錯移轉叢集管理員**會顯示角色，且其資源會離線。 接著資源會移動，並在另一個節點上線。

### <a name="test-connectivity"></a>測試連線能力

若要測試連線能力，請登入相同虛擬網路中的另一部虛擬機器。 開啟 [SQL Server Management Studio]****，然後連接到 SQL Server FCI 名稱。

>[!NOTE]
>如有需要，您可以[下載 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)。

## <a name="limitations"></a>限制

Azure 虛擬機器支援 Windows Server 2019 上的 Microsoft 分散式交易協調器（MSDTC）與叢集共用磁片區（CSV）和[標準負載平衡器](../../../load-balancer/load-balancer-standard-overview.md)上的儲存體。

在 Azure 虛擬機器上，Windows Server 2016 或更早版本不支援 MSDTC，因為：

- 叢集 MSDTC 資源無法設定為使用共用存放裝置。 在 Windows Server 2016 上，如果您建立 MSDTC 資源，它不會顯示任何可供使用的共用存放裝置，即使有可用的存放裝置也一樣。 Windows Server 2019 中已修正此問題。
- 基本負載平衡器不會處理 RPC 埠。

## <a name="see-also"></a>請參閱

[使用遠端桌面（Azure）設定儲存空間直接存取](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-storage-spaces-direct-deployment)

[具有儲存空間直接存取的超融合式解決方案](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct)

[儲存空間直接存取概觀](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview)

[儲存空間直接存取的 SQL Server 支援](https://blogs.technet.microsoft.com/dataplatforminsider/2016/09/27/sql-server-2016-now-supports-windows-server-2016-storage-spaces-direct/)
