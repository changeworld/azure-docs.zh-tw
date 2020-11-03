---
title: 安裝 SQL Server IaaS 代理程式擴充功能
description: 向 SQL VM 資源提供者註冊您的 Azure SQL Server 虛擬機器，以針對部署于 Azure Marketplace 以外的 SQL Server 虛擬機器，以及合規性和增強的管理功能，啟用功能。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/30/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 8099bf87410e8359894d8b40d7637451891ad664
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2020
ms.locfileid: "93286148"
---
# <a name="install-sql-server-iaas-agent-extension"></a>安裝 SQL Server IaaS 代理程式擴充功能
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

使用 SQL VM 資源提供者註冊 SQL Server VM 會安裝 [SQL Server IaaS 代理程式擴充](sql-server-iaas-agent-extension-automate-management.md)功能。 

本文將指導您向 SQL VM 資源提供者註冊單一 SQL Server VM。 或者，您也可以 [自動](sql-vm-resource-provider-automatic-registration.md) 註冊所有 SQL Server vm 或 [大量編寫腳本](sql-vm-resource-provider-bulk-register.md)。


## <a name="overview"></a>總覽

向資源提供者註冊會安裝 [SQL Server IaaS 代理程式延伸](sql-server-iaas-agent-extension-automate-management.md)模組，也會在您的訂用帳戶內建立 **SQL 虛擬機器**_資源_ ，這是與虛擬機器資源 _不同_ 的資源。 向資源提供者取消註冊 SQL Server VM 會移除 **SQL 虛擬機器** 的 _資源_ ，但不會卸載實際的虛擬機器。

透過 Azure 入口網站部署 SQL Server VM Azure Marketplace 映像，會自動向資源提供者註冊 SQL Server VM。 不過，如果您選擇在 Azure 虛擬機器上自行安裝 SQL Server，或從自訂 VHD 布建 Azure 虛擬機器，則必須向 SQL VM 資源提供者註冊您的 SQL Server VM，以安裝 SQL IaaS 代理程式擴充功能。 

若要使用 SQL VM 資源提供者，您必須先 [向資源提供者註冊您的訂用](#register-subscription-with-rp)帳戶，讓資源提供者能夠在該特定訂用帳戶內建立資源。

> [!IMPORTANT]
> SQL IaaS 代理程式擴充功能會收集資料，以在 Azure 虛擬機器中使用 SQL Server 時，為客戶提供選擇性的權益。 Microsoft 不會在未經客戶同意的情況下，使用此資料進行授權審核。 如需詳細資訊，請參閱 [SQL Server 隱私權補充](/sql/sql-server/sql-server-privacy#non-personal-data) 資訊。

## <a name="prerequisites"></a>必要條件

若要向資源提供者註冊 SQL Server VM，您將需要： 

- [Azure 訂用帳戶](https://azure.microsoft.com/free/)。
- Azure 資源模型 [Windows Server 2008 (或更高) 虛擬機器](../../../virtual-machines/windows/quick-create-portal.md) ，其 [SQL Server 2008 (或更高) ](https://www.microsoft.com/sql-server/sql-server-downloads) 部署至公用或 Azure Government 雲端。 
- 最新版本的 [Azure CLI](/cli/azure/install-azure-cli) 或 [Azure PowerShell (5.0 的最小) ](/powershell/azure/install-az-ps)。 


## <a name="register-subscription-with-rp"></a>註冊 RP 訂用帳戶

若要向 SQL VM 資源提供者註冊您的 SQL Server VM，您必須先向資源提供者註冊自己的訂用帳戶。 這會讓 SQL VM 資源提供者能夠在訂用帳戶內建立資源。  您可以使用 Azure 入口網站、Azure CLI 或 Azure PowerShell 來這麼做。

### <a name="azure-portal"></a>Azure 入口網站

1. 開啟 Azure 入口網站並前往 [所有服務]。 
1. 前往 [訂用帳戶] 然後選取感興趣的訂用帳戶。  
1. 在 [訂用帳戶] 頁面上，前往 [資源提供者]。 
1. 在篩選條件中輸入 **sql** 以顯示 SQL 相關的資源提供者。 
1. 取決於您所需的動作，針對 **Microsoft.SqlVirtualMachine** 提供者選取 [註冊]、[重新註冊] 或 [取消註冊]。 

   ![修改提供者](./media/sql-vm-resource-provider-register/select-resource-provider-sql.png)


### <a name="command-line"></a>命令列

使用 Azure CLI 或 Azure PowerShell，向您的 Azure 訂用帳戶註冊您的 SQL VM 資源提供者。 

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

```azurecli-interactive
# Register the SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

```powershell-interactive
# Register the SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="register-with-rp"></a>向 RP 註冊

[SQL Server IaaS 代理程式擴充](sql-server-iaas-agent-extension-automate-management.md#management-modes)功能有三種管理模式。 

以完整管理模式安裝擴充功能會重新開機 SQL Server 服務，因此建議您先在輕量模式中安裝擴充功能，然後在維護期間 [升級為 full](#upgrade-to-full) 。 

### <a name="lightweight-management-mode"></a>輕量管理模式

使用 Azure CLI 或 Azure PowerShell 向資源提供者註冊您的 SQL Server VM，並以輕量模式安裝 SQL IaaS 擴充功能。 這樣就不會重新開機 SQL Server 服務。 然後，您可以隨時升級至完整模式，但是這麼做會重新啟動 SQL Server 服務，因此建議您等到排程的維護時段。 

提供 SQL Server 授權類型做為隨用隨付 (`PAYG`) 以依據使用量付費，Azure Hybrid Benefit (`AHUB`) 使用您自己的授權，或使用嚴重損壞修復 (`DR`) 來啟用 [免費 DR 複本授權](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure)。

容錯移轉叢集實例和多重實例部署只能在輕量模式中向 SQL VM 資源提供者註冊。 

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

使用 Azure CLI 以輕量模式註冊 SQL Server VM： 

  ```azurecli-interactive
  # Register Enterprise or Standard self-installed VM in Lightweight mode
  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type PAYG 
  ```


# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

以 Azure PowerShell 的輕量模式註冊 SQL Server VM：  


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  # Register SQL VM with 'Lightweight' SQL IaaS agent
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType LightWeight  
  ```

---

### <a name="full-management-mode"></a>完整管理模式

以完整模式註冊 SQL Server VM 將會重新開機 SQL Server 服務。 請小心進行。 

若要直接以完整模式註冊 SQL Server VM (並可能重新開機 SQL Server 服務) ，請使用下列 Azure PowerShell 命令： 

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

### <a name="noagent-management-mode"></a>NoAgent 管理模式 

安裝在 Windows Server 2008 ( _不是 R2_ ) 上的 SQL Server 2008 和 2008 R2，可以在 [NoAgent 模式](sql-server-iaas-agent-extension-automate-management.md#management-modes)中向 SQL VM 資源提供者註冊。 此選項可確保合規性，並允許在功能有限的 Azure 入口網站中監視 SQL Server 的 VM。

將 `AHUB`、`PAYG` 或 `DR` 指定為 **sqlLicenseType** ，然後將 `SQL2008-WS2008` 或 `SQL2008R2-WS2008` 指定為 **sqlImageOffer** 。 

若要在 Windows Server 2008 實例上註冊 SQL Server 2008 或 2008 R2，請使用下列 Azure CLI 或 Azure PowerShell 程式碼片段： 


# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

使用 Azure CLI 在 NoAgent 模式下註冊您的 SQL Server 2008 虛擬機器： 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008-WS2008
 ```
 
 
使用 Azure CLI 在 NoAgent 模式下註冊您的 SQL Server 2008 R2 虛擬機器： 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008R2-WS2008
 ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

使用 Azure PowerShell 在 NoAgent 模式下註冊您的 SQL Server 2008 虛擬機器： 

  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008-WS2008
  ```
  

使用 Azure PowerShell 在 NoAgent 模式下註冊您的 SQL Server 2008 R2 虛擬機器： 

  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008R2-WS2008
  ```

---

## <a name="verify-mode"></a>驗證模式

您可以使用 Azure PowerShell 來查看 SQL Server IaaS 代理程式的目前模式： 

```powershell-interactive
# Get the SqlVirtualMachine
$sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
$sqlvm.SqlManagementType
```

## <a name="upgrade-to-full"></a>升級至 full  

SQL Server 已安裝 *輕量* IaaS 擴充功能的 vm，可以使用 Azure 入口網站、Azure CLI 或 Azure PowerShell 將模式升級為 _full_ 。 在 OS 升級為 Windows 2008 R2 和更新版本後， _NoAgent_ 模式中的 SQL Server VM 就可以升級為 _完整_ 模式。 無法降級 - 若要執行此操作，您需要從 SQL VM 資源提供者[取消註冊](#unregister-from-rp) SQL Server VM。 這麼做會移除 **SQL 虛擬機器**_資源_ ，但不會刪除實際的虛擬機器。 


### <a name="azure-portal"></a>Azure 入口網站

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 移至 [SQL 虛擬機器](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource)資源。 
1. 選取您的 SQL Server VM，然後選取 **[總覽** ]。 
1. 針對具有 NoAgent 或輕量 IaaS 模式的 SQL Server VM，請選取 [僅授權類型和版本更新適用於 SQL IaaS 擴充功能] 訊息。

   ![從入口網站變更模式的選項](./media/sql-vm-resource-provider-register/change-sql-iaas-mode-portal.png)

1. 選取 [我同意在虛擬機器上重新啟動 SQL Server 服務] 核取方塊，然後選取 [確認]，將 IaaS 升級為完整模式。 

    ![同意重新啟動虛擬機器上 SQL Server 服務的核取方塊](./media/sql-vm-resource-provider-register/enable-full-mode-iaas.png)

### <a name="command-line"></a>命令列

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

執行下列 Azure CLI 程式碼片段：

  ```azurecli-interactive
  # Update to full mode
  az sql vm update --name <vm_name> --resource-group <resource_group_name> --sql-mgmt-type full  
  ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

執行下列 Azure PowerShell 程式碼片段：

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  Update-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

---

## <a name="verify-registration-status"></a>驗證註冊狀態
您可以使用 Azure 入口網站、Azure CLI 或 Azure PowerShell，確認您的 SQL Server VM 是否已向 SQL VM 資源提供者註冊。 

### <a name="azure-portal"></a>Azure 入口網站 

1. 登入 [Azure 入口網站](https://portal.azure.com)。 
1. 移至 [SQL Server vm](manage-sql-vm-portal.md)。
1. 從清單中選取 SQL Server VM。 如果此處清單中沒有您的 SQL Server VM，代表該 VM 可能尚未向 SQL VM 資源提供者註冊。 
1. 查看 [狀態]下的值。 如果 [狀態] 為 [成功]，則 SQL Server VM 已成功向 SQL VM 資源提供者註冊。 

   ![驗證 SQL RP 註冊狀態](./media/sql-vm-resource-provider-register/verify-registration-status.png)

### <a name="command-line"></a>命令列

使用 Azure CLI 或 Azure PowerShell 來確認目前 SQL Server VM 註冊狀態。 如果註冊成功，則 `ProvisioningState` 會顯示 `Succeeded`。 

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)


  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
 ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

  ```powershell-interactive
  Get-AzSqlVM -Name <vm_name> -ResourceGroupName <resource_group>
  ```

---

錯誤指出 SQL Server VM 尚未向資源提供者註冊。 


## <a name="unregister-from-rp"></a>從 RP 取消登錄

若要向 SQL VM 資源提供者取消註冊 SQL Server VM，請使用 Azure 入口網站或 Azure CLI 來刪除 SQL 虛擬機器 *資源* 。 刪除 SQL 虛擬機器 *資源* 並不會刪除 SQL Server VM。 不過，請謹慎使用並遵循步驟，因為在嘗試移除 *資源* 時，可能會不小心刪除虛擬機器。 

若要將管理模式從 full 降級，必須將 SQL 虛擬機器從 SQL VM 資源提供者取消註冊。 

### <a name="azure-portal"></a>Azure 入口網站

若要使用 Azure 入口網站向資源提供者取消註冊 SQL Server VM，請遵循下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 流覽至 SQL VM 資源。 
  
   ![SQL 虛擬機器資源](./media/sql-vm-resource-provider-register/sql-vm-manage.png)

1. 選取 [刪除] 。 

   ![選取頂端導覽中的 [刪除]](./media/sql-vm-resource-provider-register/delete-sql-vm-resource-provider.png)

1. 輸入 SQL 虛擬機器的名稱，並 **清除虛擬機器旁的核取方塊** 。

   ![取消核取 VM 以防止刪除實際的虛擬機器，然後選取 [刪除] 以繼續刪除 SQL VM 資源](./media/sql-vm-resource-provider-register/confirm-delete-of-resource-uncheck-box.png)

   >[!WARNING]
   > 如果無法清除虛擬機器名稱旁的核取方塊，將會完全 *刪除* 虛擬機器。 清除核取方塊可從資源提供者取消註冊 SQL Server VM，但 *不會刪除實際的虛擬機器* 。 

1. 選取 [ **刪除** ] 以確認刪除 SQL 虛擬機器 *資源* ，而不是 SQL Server VM。 

### <a name="command-line"></a>命令列

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
若要使用 Azure CLI 向資源提供者取消註冊 SQL Server VM，請使用 [az SQL VM delete](/cli/azure/sql/vm?view=azure-cli-latest&preserve-view=true#az-sql-vm-delete) 命令。 這會移除 SQL Server VM *資源* ，但不會刪除虛擬機器。 


```azurecli-interactive
az sql vm delete 
  --name <SQL VM resource name> |
  --resource-group <Resource group name> |
  --yes 
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
若要使用 Azure PowerShell 從資源提供者取消註冊 SQL Server VM，請使用 [AzSqlVM](/powershell/module/az.sqlvirtualmachine/remove-azsqlvm)命令。 這會移除 SQL Server VM *資源* ，但不會刪除虛擬機器。 

```powershell-interactive
Remove-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name>
```

---



## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列文章： 

* [Windows VM 上的 SQL Server 概觀](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Windows VM 上的 SQL Server 常見問題集](frequently-asked-questions-faq.md)  
* [Windows VM 上的 SQL Server 定價指引](pricing-guidance.md)
* [Windows VM 上的 SQL Server 版本資訊](../../database/doc-changes-updates-release-notes.md)
