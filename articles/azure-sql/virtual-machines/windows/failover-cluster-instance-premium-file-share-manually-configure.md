---
title: 建立具有 premium 檔案共用的 FCI
description: 使用 premium 檔案共用（PFS），在 Azure 虛擬機器上建立具有 SQL Server 的容錯移轉叢集實例（FCI）。
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/18/2020
ms.author: mathoma
ms.openlocfilehash: befab4dfb8d414743b70c535d041112bd9ccb700
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2020
ms.locfileid: "85964126"
---
# <a name="create-an-fci-with-a-premium-file-share-sql-server-on-azure-vms"></a>使用 premium 檔案共用建立 FCI （在 Azure Vm 上 SQL Server）
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

本文說明如何使用[premium 檔案共用](../../../storage/files/storage-how-to-create-premium-fileshare.md)，在 Azure 虛擬機器（vm）上建立具有 SQL Server 的容錯移轉叢集實例（FCI）。

Premium 檔案共用是儲存空間直接存取（SSD）備份、一致的低延遲檔案共用，可在 Windows Server 2012 或更新版本上與 SQL Server 2012 或更新版本的容錯移轉叢集實例搭配使用。 進階檔案共用提供更大的彈性，因此無需停機，即可調整檔案共用的大小和規模。

若要深入瞭解，請參閱[FCI 與 Azure vm 上的 SQL Server](failover-cluster-instance-overview.md)和叢集[最佳做法](hadr-cluster-best-practices.md)。 

## <a name="prerequisites"></a>必要條件

在您完成本文中的指示之前，您應該已經具備：

- Azure 訂用帳戶。
- 具有在 Azure 虛擬機器和 Active Directory 中建立物件權限的帳戶。
- [可用性設定組](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set)或不同[可用性區域](../../../virtual-machines/windows/create-portal-availability-zone.md#confirm-zone-for-managed-disk-and-ip-address)中的[兩個或多個已備妥的 Windows Azure 虛擬機器](failover-cluster-instance-prepare-vm.md)。
- 根據資料檔案的資料庫儲存配額，將[進階檔案共用](../../../storage/files/storage-how-to-create-premium-fileshare.md)作為叢集磁碟機使用。
- 最新版的[PowerShell](/powershell/azure/install-az-ps?view=azps-4.2.0)。 

## <a name="mount-premium-file-share"></a>裝載 premium 檔案共用

1. 登入 [Azure 入口網站](https://portal.azure.com)。 並移至您的儲存體帳戶。
1. 移至 [檔案**服務**] 底下的 [檔案**共用**]，然後選取您想要用於 SQL 儲存體的 premium 檔案共用。
1. 選取 [連線]，以顯示檔案共用的連接字串。
1. 在下拉式清單中，選取您想要使用的磁碟機號，然後將這兩個程式碼區塊複製到 [記事本]。

   :::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/premium-file-storage-commands.png" alt-text="從檔案共用連線入口網站複製這兩個 PowerShell 命令":::

1. 使用遠端桌面通訊協定（RDP），透過 SQL Server FCI 將用於服務帳戶的帳戶來連線至 SQL Server VM。
1. 開啟系統管理 PowerShell 命令主控台。
1. 執行先前在入口網站中工作時所儲存的命令。
1. 使用 [檔案瀏覽器] 或 [**執行**] 對話方塊（選取 [Windows + R]）移至共用。 使用網路路徑 `\\storageaccountname.file.core.windows.net\filesharename`。 例如， `\\sqlvmstorageaccount.file.core.windows.net\sqlpremiumfileshare`

1. 在新連接的檔案共用上，至少建立一個資料夾，以便將您的 SQL 資料檔案放入其中。
1. 對要參與叢集的每部 SQL Server VM 重複這些步驟。

  > [!IMPORTANT]
  > - 請考慮針對備份檔案使用個別的檔案共用，以儲存此共用的每秒輸入/輸出作業（IOPS）和空間容量，以用於資料和記錄檔。 您可以針對備份檔案使用 Premium 或 Standard 檔案共用。
  > - 如果您使用的是 Windows 2012 R2 或更早版本，請遵循這些相同的步驟來掛接要做為檔案共用見證的檔案共用。 
  > 


## <a name="add-windows-cluster-feature"></a>新增 Windows 叢集功能

1. 使用身為本機系統管理員成員且具備在 Active Directory 中建立物件權限的網域帳戶，以透過 RDP 連線到第一個虛擬機器。 在之後的設定程序中繼續使用此帳戶。

1. [將容錯移轉叢集新增至每部虛擬機器](availability-group-manually-configure-prerequisites-tutorial.md#add-failover-clustering-features-to-both-sql-server-vms)。

   若要從 UI 安裝容錯移轉叢集，請在兩部虛擬機器上執行下列動作：
   1. 在 [伺服器管理員] 中，選取 [管理]，然後選取 [新增角色和功能]。
   1. 在 [**新增角色及功能**] 中，選取 **[下一步]** ，直到您**選取 [功能**]。
   1. 在 [選取功能] 中，選取 [容錯移轉叢集]。 選取所有所需功能與管理工具。 
   1. 選取 [新增功能]。
   1. 選取 [下一步]，然後選取 [完成] 以安裝功能。

   若要使用 PowerShell 安裝容錯移轉叢集，請在其中一部虛擬機器上的系統管理員 PowerShell 會話中執行下列腳本：

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

## <a name="validate-cluster"></a>驗證叢集

在 UI 中或使用 PowerShell 驗證叢集。

若要使用 UI 來驗證叢集，請在其中一部虛擬機器上執行下列動作：

1. 在 [伺服器管理員] 下，選取 [工具]，然後選取 [容錯移轉叢集管理員]。
1. 在 [容錯移轉叢集管理員] 下，選取 [動作]，然後選取 [驗證設定]。
1. 選取 [下一步]。
1. 在 [選取伺服器或叢集] 下，輸入這兩部虛擬機器的名稱。
1. 在 [測試選項] 下，選取 [僅執行我選取的測試]。 
1. 選取 [下一步]。
1. 在 [測試選取] 下，選取除 [儲存體] 和 [儲存空間直接存取] 以外的所有測試，如下所示：

   :::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/cluster-validation.png" alt-text="選取叢集驗證測試":::

1. 選取 [下一步] 。
1. 在 [確認] 下，選取 [下一步]。

[**驗證**設定] 嚮導會執行驗證測試。

若要使用 PowerShell 驗證叢集，請在其中一部虛擬機器上，從系統管理員 PowerShell 工作階段執行下列指令碼：

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
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

設定最適合您業務需求的仲裁解決方案。 您可以設定[磁片見證](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)、[雲端見證](/windows-server/failover-clustering/deploy-cloud-witness)或檔案[共用見證](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)。 如需詳細資訊，請參閱[使用 SQL Server vm 的仲裁](hadr-cluster-best-practices.md#quorum)。 


## <a name="test-cluster-failover"></a>測試叢集容錯移轉

測試叢集的容錯移轉。 在**容錯移轉叢集管理員**中，以滑鼠右鍵按一下您的叢集，選取 [**更多動作**] [  >  **移動核心叢集資源**]  >  [**選取節點**]，然後選取叢集的其他節點。 將核心叢集資源移到叢集的每個節點，再移回主要節點。 如果您可成功地將叢集移至每個節點，即可開始安裝 SQL Server。  

:::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/test-cluster-failover.png" alt-text="將核心資源移至其他節點以測試叢集容錯移轉":::


## <a name="create-sql-server-fci"></a>建立 SDL Server FCI

設定容錯移轉叢集之後，即可建立 SQL Server FCI。

1. 使用 RDP 連線到第一部虛擬機器。

1. 在**容錯移轉叢集管理員**中，請確定所有核心叢集資源都在第一部虛擬機器上。 如有必要，請將所有資源移至此虛擬機器。

1. 找出安裝媒體。 若虛擬機器是使用其中一個 Azure Marketplace 映像，則媒體會位於 `C:\SQLServer_<version number>_Full`。 

1. 選取 [設定]。

1. 在 [SQL Server 安裝中心] 中，選取 [安裝]。

1. 選取 [**新增 SQL Server 容錯移轉叢集安裝**]，然後依照嚮導中的指示安裝 SQL Server FCI。

   FCI 資料目錄必須位於進階檔案共用中。 輸入共用的完整路徑，格式為： `\\storageaccountname.file.core.windows.net\filesharename\foldername` 。 隨即會出現警告告知已指定檔案伺服器作為資料目錄。 這個警告是正常現象。 當您保存檔案共用時，請確定您用來透過 RDP 存取 VM 的使用者帳戶，與 SQL Server 服務用來避免可能發生失敗的帳戶相同。

   :::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/use-file-share-as-data-directories.png" alt-text="使用檔案共用作為 SQL 資料目錄":::

1. 完成精靈中的步驟後，安裝程式會在第一個節點上安裝 SQL Server FCI。

1. 安裝程式在第一個節點上安裝 FCI 後，請使用 RDP 連線到第二個節點。

1. 開啟 [ **SQL Server 安裝中心**]，然後選取 [**安裝**]。

1. 選取 [將節點新增到 SQL Server 容錯移轉叢集]。 遵循精靈中的指示來安裝 SQL Server，並將伺服器新增到 FCI。

   >[!NOTE]
   >若您曾透過 SQL Server 使用 Azure Marketplace 資源庫映像，映像會包含 SQL Server 工具。 若沒有用過其中一個映像，請另外安裝 SQL Server 工具。 如需詳細資訊，請參閱 [Download SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms)(下載 SQL Server Management Studio (SSMS))。

1. 在您想要新增至 SQL Server 容錯移轉叢集實例的任何其他節點上重複這些步驟。 

## <a name="register-with-the-sql-vm-rp"></a>向 SQL VM RP 註冊

若要從入口網站管理您的 SQL Server VM，請以[輕量管理模式](sql-vm-resource-provider-register.md#lightweight-management-mode)向 SQL vm 資源提供者（RP）註冊，此為目前僅在 Azure VM 上 FCI 和 SQL Server 支援的模式。 

使用 PowerShell 在輕量模式中註冊 SQL Server VM （-LicenseType 可以是 `PAYG` 或 `AHUB` ）：

```powershell-interactive
# Get the existing compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
         
# Register SQL VM with 'Lightweight' SQL IaaS agent
New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
   -LicenseType ???? -SqlManagementType LightWeight  
```

## <a name="configure-connectivity"></a>設定連線能力 

若要將流量適當地路由至目前的主要節點，請設定適用于您環境的連線選項。 您可以建立[Azure 負載平衡器](hadr-vnn-azure-load-balancer-configure.md)，或者，如果您使用 SQL Server 2019 和 Windows Server 2019，則可以改為預覽[分散式網路名稱](hadr-distributed-network-name-dnn-configure.md)功能。 

## <a name="limitations"></a>限制

- Windows Server 2016 和更早版本不支援 Microsoft 分散式交易協調器（MSDTC）。 
- 使用進階檔案共用的容錯移轉叢集不支援 Filestream。 若要使用 filestream，請改為使用[儲存空間直接存取](failover-cluster-instance-storage-spaces-direct-manually-configure.md)或[Azure 共用磁片](failover-cluster-instance-azure-shared-disks-manually-configure.md)來部署您的叢集。
- 僅支援以[輕量管理模式](sql-vm-resource-provider-register.md#management-modes)向 SQL VM 資源提供者註冊。 

## <a name="next-steps"></a>後續步驟

如果您尚未這麼做，請使用[虛擬網路名稱和 Azure 負載平衡器](hadr-vnn-azure-load-balancer-configure.md)或[分散式網路名稱（DNN）](hadr-distributed-network-name-dnn-configure.md)，設定與您的 FCI 的連線能力。 

如果 premium 檔案共用不是適合您的 FCI 儲存體解決方案，請考慮改為使用[Azure 共用磁片](failover-cluster-instance-azure-shared-disks-manually-configure.md)或[儲存空間直接存取](failover-cluster-instance-storage-spaces-direct-manually-configure.md)來建立 FCI。 

若要深入瞭解，請參閱[FCI 與 Azure vm 上的 SQL Server](failover-cluster-instance-overview.md)和叢集設定的[最佳做法](hadr-cluster-best-practices.md)。 

如需詳細資訊，請參閱： 
- [Windows 叢集技術](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server 容錯移轉叢集實例](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
