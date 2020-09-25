---
title: 使用儲存空間直接存取建立 FCI
description: 使用儲存空間直接存取來建立容錯移轉叢集實例 (FCI) 與 Azure 虛擬機器上的 SQL Server。
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.custom: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/18/2020
ms.author: mathoma
ms.openlocfilehash: 3cc579615a69b659bc1a4736984f0b3dcd6edb6b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91272519"
---
# <a name="create-an-fci-with-storage-spaces-direct-sql-server-on-azure-vms"></a>在 Azure Vm 上建立具有儲存空間直接存取 (SQL Server 的 FCI) 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

本文說明如何在 (Vm) 的 Azure 虛擬機器上，使用 [儲存空間直接存取](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) 搭配 SQL Server， (FCI) 建立容錯移轉叢集實例。 儲存空間直接存取可作為以軟體為基礎的虛擬存放區域網路 (VSAN) ，以同步處理 Windows 叢集中) Azure Vm (節點之間的儲存體 (資料磁片) 。 

若要深入瞭解，請參閱 [使用 Azure vm 上的 SQL Server](failover-cluster-instance-overview.md) 和叢集 [最佳作法](hadr-cluster-best-practices.md)的 FCI 總覽。 


## <a name="overview"></a>概觀 

[儲存空間直接存取 (S2D) ](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) 支援兩種類型的架構：融合式和超融合式。 超融合式基礎結構會將存放裝置放置在裝載叢集應用程式的相同伺服器上，讓儲存體位於每個 SQL Server FCI 節點上。 

下圖顯示完整的解決方案，其使用超融合式儲存空間直接存取搭配 Azure Vm 上的 SQL Server： 

![使用超融合式儲存空間直接存取的完整解決方案圖表](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/00-sql-fci-s2d-complete-solution.png)

上圖顯示相同資源群組中的下列資源：

- Windows Server 容錯移轉叢集中的兩部虛擬機器。 位於容錯移轉叢集中的虛擬機器也稱為「叢集節點」或「節點」。
- 每部虛擬機器有兩個以上的資料磁碟。
- 儲存空間直接存取會同步資料磁碟上的資料，並將同步儲存體作為儲存集區呈現。
- 儲存集區會向容錯移轉叢集提供叢集共用磁碟區 (CSV)。
- SQL Server FCI 叢集角色會針對資料硬碟使用 CSV。
- 保留 SQL Server FCI IP 位址的 Azure Load Balancer。
- 保留所有資源的 Azure 可用性設定組。

   > [!NOTE]
   > 您可在 Azure 中，從範本開始建立整個解決方案。 您可以在 GitHub [Azure 快速入門範本](https://github.com/MSBrett/azure-quickstart-templates/tree/master/sql-server-2016-fci-existing-vnet-and-ad) 頁面上取得範本的範例。 本範例並非為任何特定工作負載設計或測試。 您可執行範本來建立 SQL Server FCI，並將儲存空間直接存取儲存體連線至網域。 您可評估此範本，並依需要進行修改。


## <a name="prerequisites"></a>Prerequisites

在您完成本文中的指示之前，您應該已經有：

- Azure 訂用帳戶。 [免費](https://azure.microsoft.com/free/)開始使用。 
- 一個[可用性設定組](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set)中的[兩個或多個備妥的 Windows Azure 虛擬機器](failover-cluster-instance-prepare-vm.md)。
- 具有在 Azure 虛擬機器和 Active Directory 中建立物件權限的帳戶。
- [PowerShell](/powershell/azure/install-az-ps?view=azps-4.2.0)的最新版本。 


## <a name="add-the-windows-cluster-feature"></a>新增 Windows 叢集功能

1. 使用遠端桌面通訊協定 (RDP) 連接到第一部虛擬機器，其網域帳戶必須是本機系統管理員的成員，而且具有在 Active Directory 中建立物件的許可權。 在之後的設定程序中繼續使用此帳戶。

1. 將容錯移轉叢集新增至每部虛擬機器。

   若要從 UI 安裝容錯移轉叢集，請在兩個虛擬機器上執行下列步驟：

   1. 在 [伺服器管理員] 中，選取 [管理]，然後選取 [新增角色和功能]。
   1. 在 [ **新增角色及功能** ] 嚮導中選取 **[下一步** ]，直到您進入 **選取功能**為止。
   1. 在 [選取功能] 中，選取 [容錯移轉叢集]。 選取所有所需功能與管理工具。 
   1. 選取 [新增功能]。
   1. 選取 [下一步]，然後選取 [完成] 以安裝功能。

   若要使用 PowerShell 安裝容錯移轉叢集，請在其中一部虛擬機器上從系統管理員 PowerShell 會話執行下列腳本：

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

如需後續步驟的詳細資訊，請參閱 [使用 Windows Server 2016 中的儲存空間直接存取超融合式解決方案](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-3-configure-storage-spaces-direct)的「步驟3：設定儲存空間直接存取」一節中的指示。


## <a name="validate-the-cluster"></a>驗證叢集

在 UI 中或使用 PowerShell 驗證叢集。

若要使用 UI 驗證叢集，請在其中一部虛擬機器上執行下列步驟：

1. 在 [伺服器管理員] 下，選取 [工具]，然後選取 [容錯移轉叢集管理員]。
1. 在 [容錯移轉叢集管理員] 下，選取 [動作]，然後選取 [驗證設定]。
1. 選取 [下一步] 。
1. 在 [選取伺服器或叢集] 下，輸入這兩部虛擬機器的名稱。
1. 在 [測試選項] 下，選取 [僅執行我選取的測試]。 
1. 選取 [下一步] 。
1. 在 [測試選取] 下，選取除 [儲存體] 以外的所有測試，如以下所示：

   ![選取叢集驗證測試](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/10-validate-cluster-test.png)

1. 選取 [下一步] 。
1. 在 [確認] 下，選取 [下一步]。

    [ **驗證** 設定] 嚮導會執行驗證測試。

若要使用 PowerShell 驗證叢集，請在其中一部虛擬機器上，從系統管理員 PowerShell 工作階段執行下列指令碼：

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Storage Spaces Direct", "Inventory", "Network", "System Configuration"
   ```

驗證叢集後，請建立容錯移轉叢集。


## <a name="create-failover-cluster"></a>建立容錯移轉叢集

若要建立容錯移轉叢集，您需要：

- 將成為叢集節點的虛擬機器名稱。
- 容錯移轉叢集的名稱。
- 容錯移轉叢集的 IP 位址。 您可使用並未在叢集節點相同 Azure 虛擬網路和子網路中使用的 IP 位址。


# <a name="windows-server-2012---2016"></a>[Windows Server 2012 - 2016](#tab/windows2012)

下列 PowerShell 指令碼會建立 Windows Server 2012 到 Windows Server 2016 的容錯移轉叢集。 使用節點名稱 (虛擬機器名稱) 和 Azure 虛擬網路的可用 IP 位址來更新指令碼。

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

# <a name="windows-server-2019"></a>[Windows Server 2019](#tab/windows2019)

下列 PowerShell 指令碼會建立 Windows Server 2019 的容錯移轉叢集。  使用節點名稱 (虛擬機器名稱) 和 Azure 虛擬網路的可用 IP 位址來更新指令碼。

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```

如需詳細資訊，請參閱[容錯移轉叢集：叢集網路物件](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97)。

---


## <a name="configure-quorum"></a>設定仲裁

設定最適合您商務需求的仲裁解決方案。 您可以設定 [磁片見證](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)、 [雲端見證](/windows-server/failover-clustering/deploy-cloud-witness)或檔案 [共用見證](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)。 如需詳細資訊，請參閱 [SQL Server vm 的仲裁](hadr-cluster-best-practices.md#quorum)。 

## <a name="add-storage"></a>新增儲存體

儲存空間直接存取的磁碟必須是空的。 其不能包含磁碟分割或其他資料。 若要清除磁片，請遵循 [部署儲存空間直接存取](https://docs.microsoft.com/windows-server/storage/storage-spaces/deploy-storage-spaces-direct?redirectedfrom=MSDN#step-31-clean-drives)中的指示。

1. [啟用儲存空間直接存取](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-35-enable-storage-spaces-direct)。

   下列 PowerShell 指令碼會啟用儲存空間直接存取：  

   ```powershell
   Enable-ClusterS2D
   ```

   現在，您可以在**容錯移轉管理員**中看到儲存集區。

1. [建立磁碟區](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-36-create-volumes)。

   儲存空間直接存取會在啟用時自動建立儲存集區。 您現在可開始建立磁碟區。 PowerShell Cmdlet `New-Volume` 會自動化磁碟區的建立流程。 此套裝程式含格式化、將磁片區新增至叢集，以及建立 CSV。 本範例會建立 800 GB 的 CSV：

   ```powershell
   New-Volume -StoragePoolFriendlyName S2D* -FriendlyName VDisk01 -FileSystem CSVFS_REFS -Size 800GB
   ```   

   執行上述命令之後，800 GB 磁片區會掛接為叢集資源。 磁碟區會位於 `C:\ClusterStorage\Volume1\`。

   此螢幕擷取畫面顯示具有儲存空間直接存取的 CSV：

   ![具有儲存空間直接存取之叢集共用磁碟區的螢幕擷取畫面](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/15-cluster-shared-volume.png)



## <a name="test-cluster-failover"></a>測試叢集容錯移轉

測試叢集的容錯移轉。 在**容錯移轉叢集管理員**中，以滑鼠右鍵按一下您的叢集，然後選取 [**其他動作**]  >  **移動核心叢集資源**  >  **選取節點**，然後選取叢集的其他節點。 將核心叢集資源移到叢集的每個節點，再移回主要節點。 如果您可成功地將叢集移至每個節點，即可開始安裝 SQL Server。  

:::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/test-cluster-failover.png" alt-text="將核心資源移至其他節點以測試叢集容錯移轉":::

## <a name="create-sql-server-fci"></a>建立 SDL Server FCI

在設定容錯移轉叢集和所有叢集元件 (包括儲存體) 後，即可建立 SQL Server FCI。

1. 使用 RDP 連線到第一部虛擬機器。

1. 在 **容錯移轉叢集管理員**中，請確定所有核心叢集資源都位於第一部虛擬機器上。 如有必要，請將所有資源移至該虛擬機器。

1. 找出安裝媒體。 若虛擬機器是使用其中一個 Azure Marketplace 映像，則媒體會位於 `C:\SQLServer_<version number>_Full`。 選取 [設定]。

1. 在 [SQL Server 安裝中心] 中，選取 [安裝]。

1. 選取 [安裝新的 SQL Server 容錯移轉叢集]。 請遵循精靈內的指示安裝 SQL Server FCI。

   FCI 資料目錄必須位於叢集儲存體中。 在儲存空間直接存取中，它不是共用磁片，而是每個伺服器上磁片區的掛接點。 儲存空間直接存取會在節點之間同步磁碟區。 磁片區會以 CSV 的形式呈現到叢集。 在資料目錄上使用 CSV 掛接點。

   ![資料目錄](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/20-data-dicrectories.png)

1. 完成嚮導中的指示之後，安裝程式會在第一個節點上安裝 SQL Server FCI。

1. 安裝程式在第一個節點上安裝 FCI 後，請使用 RDP 連線到第二個節點。

1. 開啟 [SQL Server 安裝中心]。 選取 [安裝類型]。

1. 選取 [將節點新增到 SQL Server 容錯移轉叢集]。 遵循精靈中的指示來安裝 SQL Server，並將伺服器新增到 FCI。

   >[!NOTE]
   >若曾使用過包含 SQL Server 的 Azure Marketplace 資源庫映像，則映像會包含 SQL Server 工具。 若沒有用過其中一個映像，請另外安裝 SQL Server 工具。 如需詳細資訊，請參閱 [Download SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)(下載 SQL Server Management Studio (SSMS))。
   >


## <a name="register-with-the-sql-vm-rp"></a>向 SQL VM RP 註冊

若要從入口網站管理您的 SQL Server VM，請使用 [輕量管理模式](sql-vm-resource-provider-register.md#lightweight-management-mode)中的 SQL VM 資源提供者 (RP) 來註冊它，目前只有 Azure vm 上的 FCI 和 SQL Server 支援的模式。 


使用 PowerShell 以輕量模式註冊 SQL Server VM：  

```powershell-interactive
# Get the existing compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
         
# Register SQL VM with 'Lightweight' SQL IaaS agent
New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
   -LicenseType PAYG -SqlManagementType LightWeight  
```

## <a name="configure-connectivity"></a>設定連線能力 

若要將流量適當地路由傳送到目前的主要節點，請設定適用于您環境的連線選項。 您可以建立 [Azure 負載平衡器](hadr-vnn-azure-load-balancer-configure.md) ，或者，如果您使用 SQL Server 2019 和 Windows Server 2016 (或更新版本) ，則可以改為預覽 [分散式網路名稱](hadr-distributed-network-name-dnn-configure.md) 功能。 

## <a name="limitations"></a>限制

- Azure 虛擬機器支援 Windows Server 2019 上的 Microsoft Distributed Transaction Coordinator (MSDTC) ，並在 Csv 和 [標準負載平衡器](../../../load-balancer/load-balancer-standard-overview.md)上使用儲存體。
- 只有在將存放裝置新增至叢集時，未核取或清除磁片資格選項時，才儲存空間直接存取可以搭配使用，以 NTFS 格式的磁片來連接的磁片。 
- 只支援以 [輕量管理模式](sql-vm-resource-provider-register.md#management-modes) 向 SQL VM 資源提供者註冊。

## <a name="next-steps"></a>後續步驟

如果您尚未這麼做，請使用 [虛擬網路名稱和 Azure 負載平衡器](hadr-vnn-azure-load-balancer-configure.md) 或 [分散式網路名稱（ (DNN) ](hadr-distributed-network-name-dnn-configure.md)）設定 FCI 的連線。 

如果儲存空間直接存取不是適合您的 FCI 儲存體解決方案，請考慮改為使用 [Azure 共用磁片](failover-cluster-instance-azure-shared-disks-manually-configure.md) 或 [Premium 檔案共用](failover-cluster-instance-premium-file-share-manually-configure.md) 來建立 FCI。 

若要深入瞭解，請參閱 [使用 Azure vm](failover-cluster-instance-overview.md) 和叢集設定 [最佳作法](hadr-cluster-best-practices.md)SQL Server 的 FCI 總覽。 

如需相關資訊，請參閱： 
- [Windows 叢集技術](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server 容錯移轉叢集實例](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
