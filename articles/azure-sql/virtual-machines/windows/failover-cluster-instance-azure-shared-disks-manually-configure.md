---
title: '使用 Azure 共用磁片建立 FCI (預覽版) '
description: 使用 Azure 共用磁片，在 Azure 虛擬機器上使用 SQL Server (FCI) 來建立容錯移轉叢集實例。
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
ms.date: 06/26/2020
ms.author: mathoma
ms.openlocfilehash: ffb739affac68898f6ed5ff1d972d3fd4a70df2f
ms.sourcegitcommit: 420c30c760caf5742ba2e71f18cfd7649d1ead8a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2020
ms.locfileid: "89055255"
---
# <a name="create-an-fci-with-azure-shared-disks-sql-server-on-azure-vms"></a>在 Azure Vm 上建立具有 Azure 共用磁片 (SQL Server 的 FCI) 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

本文說明如何使用 Azure 共用磁片搭配 Azure 虛擬機器 (Vm) 上的 SQL Server， (FCI) 建立容錯移轉叢集實例。 

若要深入瞭解，請參閱 [使用 Azure vm 上的 SQL Server](failover-cluster-instance-overview.md) 和叢集 [最佳作法](hadr-cluster-best-practices.md)的 FCI 總覽。 


## <a name="prerequisites"></a>必要條件 

在您完成本文中的指示之前，您應該已經有：

- Azure 訂用帳戶。 [免費](https://azure.microsoft.com/free/)開始使用。 
- 在相同的[可用性設定](../../../virtual-machines/linux/tutorial-availability-sets.md)組和[鄰近放置群組](../../../virtual-machines/windows/co-location.md#proximity-placement-groups)中，有[兩個或多個美國中西部備妥的 Windows Azure 虛擬機器](failover-cluster-instance-prepare-vm.md)，並將容錯網域和更新網域所建立的可用性設定組設定為**1**。 
- 具有在 Azure 虛擬機器和 Active Directory 中建立物件權限的帳戶。
- [PowerShell](/powershell/azure/install-az-ps?view=azps-4.2.0)的最新版本。 


## <a name="add-azure-shared-disk"></a>新增 Azure 共用磁片
部署已啟用共用磁片功能的受控進階 SSD 磁片。 設定 `maxShares` 為 **2** ，可讓磁片可在兩個 FCI 節點之間共用。 

藉由執行下列動作來新增 Azure 共用磁片： 


1. 將下列腳本儲存為 *SharedDiskConfig.js*： 

   ```JSON
   { 
     "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
       "dataDiskName": {
         "type": "string",
         "defaultValue": "mySharedDisk"
       },
       "dataDiskSizeGB": {
         "type": "int",
         "defaultValue": 1024
       },
       "maxShares": {
         "type": "int",
         "defaultValue": 2
       }
     },
     "resources": [
       {
         "type": "Microsoft.Compute/disks",
         "name": "[parameters('dataDiskName')]",
         "location": "[resourceGroup().location]",
         "apiVersion": "2019-07-01",
         "sku": {
           "name": "Premium_LRS"
         },
         "properties": {
           "creationData": {
             "createOption": "Empty"
           },
           "diskSizeGB": "[parameters('dataDiskSizeGB')]",
           "maxShares": "[parameters('maxShares')]"
         }
       }
     ] 
   }
   ```


2. 使用 PowerShell * 在上執行SharedDiskConfig.js* ： 

   ```powershell
   $rgName = < specify your resource group name>
       $location = 'westcentralus'
       New-AzResourceGroupDeployment -ResourceGroupName $rgName `
   -TemplateFile "SharedDiskConfig.json"
   ```

3. 針對每部 VM，請將連結的共用磁片初始化為 GUID 磁碟分割表格 (GPT) ，並將其格式化為新的技術檔案系統 (NTFS) ，方法是執行下列命令： 

   ```powershell
   $resourceGroup = "<your resource group name>"
       $location = "<region of your shared disk>"
       $ppgName = "<your proximity placement groups name>"
       $vm = Get-AzVM -ResourceGroupName "<your resource group name>" `
           -Name "<your VM node name>"
       $dataDisk = Get-AzDisk -ResourceGroupName $resourceGroup `
           -DiskName "<your shared disk name>"
       $vm = Add-AzVMDataDisk -VM $vm -Name "<your shared disk name>" `
           -CreateOption Attach -ManagedDiskId $dataDisk.Id `
           -Lun <available LUN  check disk setting of the VM>
    update-AzVm -VM $vm -ResourceGroupName $resourceGroup
   ```


## <a name="create-failover-cluster"></a>建立容錯移轉叢集

若要建立容錯移轉叢集，您需要：

- 將成為叢集節點的虛擬機器名稱。
- 容錯移轉叢集的名稱。
- 容錯移轉叢集的 IP 位址。 您可使用並未在叢集節點相同 Azure 虛擬網路和子網路中使用的 IP 位址。


# <a name="windows-server-2012-2016"></a>[Windows Server 2012-2016](#tab/windows2012)

下列 PowerShell 腳本會建立容錯移轉叢集。 使用節點名稱 (虛擬機器名稱) 和 Azure 虛擬網路的可用 IP 位址來更新指令碼。

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

# <a name="windows-server-2019"></a>[Windows Server 2019](#tab/windows2019)

下列 PowerShell 腳本會建立容錯移轉叢集。 使用節點名稱 (虛擬機器名稱) 和 Azure 虛擬網路的可用 IP 位址來更新指令碼。

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```

如需詳細資訊，請參閱[容錯移轉叢集：叢集網路物件](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97)。

---


## <a name="configure-quorum"></a>設定仲裁

設定最適合您商務需求的仲裁解決方案。 您可以設定 [磁片見證](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)、 [雲端見證](/windows-server/failover-clustering/deploy-cloud-witness)或檔案 [共用見證](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)。 如需詳細資訊，請參閱 [SQL Server vm 的仲裁](hadr-cluster-best-practices.md#quorum)。 

## <a name="validate-cluster"></a>驗證叢集
在 UI 中或使用 PowerShell 驗證叢集。

若要使用 UI 驗證叢集，請在其中一部虛擬機器上執行下列步驟：

1. 在 [伺服器管理員] 下，選取 [工具]，然後選取 [容錯移轉叢集管理員]。
1. 在 [容錯移轉叢集管理員] 下，選取 [動作]，然後選取 [驗證設定]。
1. 選取 [下一步] 。
1. 在 [選取伺服器或叢集] 下，輸入這兩部虛擬機器的名稱。
1. 在 [測試選項] 下，選取 [僅執行我選取的測試]。 
1. 選取 [下一步] 。
1. 在 [**測試選取專案**] 底下，選取 [**儲存體**]*以外*的所有測試

## <a name="test-cluster-failover"></a>測試叢集容錯移轉

測試叢集的容錯移轉。 在**容錯移轉叢集管理員**中，以滑鼠右鍵按一下您的叢集，然後選取 [**其他動作**]  >  **移動核心叢集資源**  >  **選取節點**，然後選取叢集的其他節點。 將核心叢集資源移到叢集的每個節點，再移回主要節點。 如果您可成功地將叢集移至每個節點，即可開始安裝 SQL Server。  

:::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/test-cluster-failover.png" alt-text="將核心資源移至其他節點以測試叢集容錯移轉":::

## <a name="create-sql-server-fci"></a>建立 SDL Server FCI

在設定容錯移轉叢集和所有叢集元件 (包括儲存體) 後，即可建立 SQL Server FCI。

1. 使用遠端桌面通訊協定 (RDP) 連接到第一部虛擬機器。

1. 在 **容錯移轉叢集管理員**中，請確定所有核心叢集資源都位於第一部虛擬機器上。 如有必要，請將所有資源移至該虛擬機器。

1. 找出安裝媒體。 若虛擬機器是使用其中一個 Azure Marketplace 映像，則媒體會位於 `C:\SQLServer_<version number>_Full`。 

1. 選取 [設定]。

1. 在 [SQL Server 安裝中心] 中，選取 [安裝]。

1. 選取 [安裝新的 SQL Server 容錯移轉叢集]。 請遵循精靈內的指示安裝 SQL Server FCI。

FCI 資料目錄必須位於 Azure 共用磁片上。 

1. 完成精靈中的指示後，安裝程式會在第一個節點上安裝 SQL Server FCI。

1. 安裝程式在第一個節點上安裝 FCI 後，請使用 RDP 連線到第二個節點。

1. 開啟 **SQL Server 安裝中心**，然後選取 [ **安裝**]。

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

若要將流量適當地路由傳送到目前的主要節點，請設定適用于您環境的連線選項。 您可以建立 [Azure 負載平衡器](hadr-vnn-azure-load-balancer-configure.md) ，或如果您使用 SQL Server 2019 和 Windows Server 2016 (或更新版本) 您可以改為預覽 [分散式網路名稱](hadr-distributed-network-name-dnn-configure.md) 功能。 

## <a name="limitations"></a>限制

- 僅支援 Windows Server 2019 上的 SQL Server 2019。 
- 只支援以 [輕量管理模式](sql-vm-resource-provider-register.md#management-modes) 向 SQL VM 資源提供者註冊。

## <a name="next-steps"></a>後續步驟

如果您尚未這麼做，請使用 [虛擬網路名稱和 Azure 負載平衡器](hadr-vnn-azure-load-balancer-configure.md) 或 [分散式網路名稱（ (DNN) ](hadr-distributed-network-name-dnn-configure.md)）設定 FCI 的連線。 

如果 Azure 共用磁片不是適合您的 FCI 儲存體解決方案，請考慮使用 [premium 檔案共用](failover-cluster-instance-premium-file-share-manually-configure.md) 或 [儲存空間直接存取](failover-cluster-instance-storage-spaces-direct-manually-configure.md) 來建立 FCI。 

若要深入瞭解，請參閱 [使用 Azure vm](failover-cluster-instance-overview.md) 和叢集設定 [最佳作法](hadr-cluster-best-practices.md)SQL Server 的 FCI 總覽。

如需詳細資訊，請參閱： 
- [Windows 叢集技術](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server 容錯移轉叢集實例](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
