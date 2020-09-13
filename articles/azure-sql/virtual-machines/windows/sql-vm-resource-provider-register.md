---
title: 向 SQL VM 資源提供者註冊
description: 向 SQL VM 資源提供者註冊您的 Azure SQL Server 虛擬機器，以針對部署于 Azure Marketplace 以外的 SQL Server 虛擬機器，以及合規性和增強的管理功能，啟用功能。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 11e8a2fd709b40c68b90e5ed139f18997e4cb29e
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89396959"
---
# <a name="register-a-sql-server-vm-in-azure-with-the-sql-vm-resource-provider-rp"></a>在 Azure 中使用 SQL VM 資源提供者 (RP 註冊 SQL Server VM) 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

本文說明如何使用 SQL VM 資源提供者 (RP) ，在 Azure 中 (VM) 註冊 SQL Server 虛擬機器。 向資源提供者註冊會在訂用帳戶中建立 **SQL 虛擬機器**_資源_，這是與虛擬機器資源不同的資源。 向資源提供者取消註冊 SQL Server VM 會移除 **SQL 虛擬機器**的_資源_ ，但不會卸載實際的虛擬機器。 

透過 Azure 入口網站部署 SQL Server VM Azure Marketplace 映像，會自動向資源提供者註冊 SQL Server VM。 不過，如果選擇在 Azure 虛擬機器上自行安裝 SQL Server，或從自訂 VHD 佈建 Azure 虛擬機器，則應該向資源提供者註冊 SQL Server VM 以達到下列目的：

- **功能優點**：向資源提供者註冊 SQL Server VM 會解除鎖定[自動修補](automated-patching.md)、[自動備份](automated-backup.md)，以及監視和管理功能。 還會解除鎖定[授權](licensing-model-azure-hybrid-benefit-ahb-change.md)和[版本](change-sql-server-edition.md)彈性。 先前，這些功能僅適用於從 Azure Marketplace 部署的 SQL Server VM 映像。 

- **合規性**：向 SQL VM 資源提供者註冊，您還可以更輕鬆地履行在 Azure Hybrid Benefit 啟用 (依照產品條款中所指定) 時通知Microsoft的要求。 此流程不需要為每項資源管理授權註冊表單。  

- **免費管理**：在這三個管理性模式下註冊 SQL VM 資源提供者完全免費。 與資源提供者或變更管理模式都不會產生任何額外費用。 

- **簡化授權管理**：向 SQL VM 資源提供者註冊可以簡化 SQL Server 授權管理，並可讓您使用 [Azure 入口網站](manage-sql-vm-portal.md)、Azure CLI 或 PowerShell，快速識別已啟用 Azure Hybrid Benefit 的 SQL Server VM： 

   # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```azurecli-interactive
   $vms = az sql vm list | ConvertFrom-Json
   $vms | Where-Object {$_.sqlServerLicenseType -eq "AHUB"}
   ```

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell-interactive
   Get-AzSqlVM | Where-Object {$_.LicenseType -eq 'AHUB'}
   ```

   ---

若要使用 SQL VM 資源提供者，您必須先 [向資源提供者註冊您的訂用](#register-subscription-with-rp)帳戶，讓資源提供者能夠在該特定訂用帳戶內建立資源。

## <a name="prerequisites"></a>必要條件

若要向資源提供者註冊 SQL Server VM，您將需要： 

- [Azure 訂用帳戶](https://azure.microsoft.com/free/)。
- 部署至公用或 Azure Government 雲端的 Azure 資源模型 [SQL Server VM](create-sql-vm-portal.md)。 
- 最新版的 [Azure CLI](/cli/azure/install-azure-cli) 或 [PowerShell](/powershell/azure/new-azureps-module-az)。 

## <a name="management-modes"></a>管理模式

如果尚未安裝 [SQL IaaS 擴充功能](sql-server-iaas-agent-extension-automate-management.md)，向 SQL VM 資源提供者註冊會以 (在註冊過程中指定的) 三種管理模式之一自動安裝 SQL Server IaaS 擴充功能。 若未指定管理模式，將會以完整管理模式安裝 SQL IaaS 擴充功能。  

如果已手動安裝 SQL IaaS 擴充功能，則它已處於完整管理模式。因此，當您以完整模式向資源提供者註冊，SQL Server 服務不會重新啟動。

這三種管理模式如下：

- **輕量**模式不需要重新啟動 SQL Server，而只支援變更 SQL Server 的授權類型和版本。 針對具有多個執行個體的 SQL Server VM，或參與容錯移轉叢集執行個體 (FCI) 的 SQL Server VM，請使用此選項。 使用輕量模式時，不會對記憶體或 CPU 造成任何影響，也不會產生相關聯的成本。 建議您先在輕量模式中註冊 SQL Server VM，然後在排程的維護期間升級至完整模式。  

- **完整**模式提供所有功能，但需要重新啟動 SQL Server 和系統管理員權限。 這是在手動安裝 SQL IaaS 擴充功能時預設安裝的選項。 使用它來管理具有單一執行個體的 SQL Server VM。 完整模式會安裝兩項 Windows 服務，對記憶體和 CPU 的影響最小而且可透過工作管理員來監視。 使用完整的管理模式並不會產生任何相關費用。 

- **NoAgent** 模式專用於安裝在 Windows Server 2008 上的 SQL Server 2008 和 SQL Server 2008 R2。 使用 NoAgent 模式時，不會對記憶體或 CPU 造成任何影響。 使用 NoAgent 管理性模式不會有相關聯的成本。 

您可以使用 PowerShell 來查看 SQL Server IaaS 代理程式的目前模式： 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

## <a name="register-subscription-with-rp"></a>註冊 RP 訂用帳戶

若要向 SQL VM 資源提供者註冊您的 SQL Server VM，您必須先向資源提供者註冊自己的訂用帳戶。 這會讓 SQL VM 資源提供者能夠在訂用帳戶內建立資源。  您可以使用 Azure 入口網站、Azure CLI 或 PowerShell 來完成。

### <a name="azure-portal"></a>Azure 入口網站

1. 開啟 Azure 入口網站並前往 [所有服務]。 
1. 前往 [訂用帳戶] 然後選取感興趣的訂用帳戶。  
1. 在 [訂用帳戶] 頁面上，前往 [資源提供者]。 
1. 在篩選條件中輸入 **sql** 以顯示 SQL 相關的資源提供者。 
1. 取決於您所需的動作，針對 **Microsoft.SqlVirtualMachine** 提供者選取 [註冊]、[重新註冊] 或 [取消註冊]。 

   ![修改提供者](./media/sql-vm-resource-provider-register/select-resource-provider-sql.png)


### <a name="command-line"></a>命令列

使用 Azure CLI 或 PowerShell，向 Azure 訂用帳戶註冊 SQL VM 資源提供者。 

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

```azurecli-interactive
# Register the SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell-interactive
# Register the SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="register-with-rp"></a>向 RP 註冊

### <a name="lightweight-management-mode"></a>輕量管理模式

如果虛擬機器上尚未安裝 [SQL Server IaaS 代理程式擴充](sql-server-iaas-agent-extension-automate-management.md) 功能，建議您在輕量模式中向 SQL VM 資源提供者註冊。 這會以[輕量模式](#management-modes)安裝 SQL IaaS 擴充功能，並防止 SQL Server 服務重新啟動。 然後，您可以隨時升級至完整模式，但是這麼做會重新啟動 SQL Server 服務，因此建議您等到排程的維護時段。 

提供 SQL Server 授權類型做為隨用隨付 (`PAYG`) 以依據使用量付費，Azure Hybrid Benefit (`AHUB`) 使用您自己的授權，或使用嚴重損壞修復 (`DR`) 來啟用 [免費 DR 複本授權](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure)。

容錯移轉叢集實例和多重實例部署只能在輕量模式中向 SQL VM 資源提供者註冊。 

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

使用 Azure CLI 以輕量模式註冊 SQL Server VM： 

  ```azurecli-interactive
  # Register Enterprise or Standard self-installed VM in Lightweight mode
  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type PAYG 
  ```


# <a name="powershell"></a>[PowerShell](#tab/powershell)

使用 PowerShell 以輕量模式註冊 SQL Server VM：  


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  # Register SQL VM with 'Lightweight' SQL IaaS agent
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType LightWeight  
  ```

---

### <a name="full-management-mode"></a>完整管理模式


如果已手動將 SQL IaaS 延伸模組安裝至虛擬機器，您可以在完整模式中註冊 SQL Server VM，而不需重新開機 SQL Server 服務。 **不過，如果尚未安裝 SQL IaaS 擴充功能，以完整模式註冊會以完整模式安裝 SQL IaaS 擴充功能，然後重新啟動 SQL Server 服務。請謹慎執行。**


若要以完整模式 (可能會重新啟動 SQL Server 服務) 直接註冊 SQL Server VM，請使用下列 PowerShell 命令： 

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

### <a name="noagent-management-mode"></a>NoAgent 管理模式 

安裝在 Windows Server 2008 (_不是 R2_) 上的 SQL Server 2008 和 2008 R2，可以在 [NoAgent 模式](#management-modes)中向 SQL VM 資源提供者註冊。 此選項可確保合規性，並允許在功能有限的 Azure 入口網站中監視 SQL Server 的 VM。

將 `AHUB`、`PAYG` 或 `DR` 指定為 **sqlLicenseType**，然後將 `SQL2008-WS2008` 或 `SQL2008R2-WS2008` 指定為 **sqlImageOffer**。 

若要在 Windows Server 2008 執行個體上註冊 SQL Server 2008 或 2008 R2，請使用下列 Azure CLI 或 PowerShell 程式碼片段： 


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

# <a name="powershell"></a>[PowerShell](#tab/powershell)

使用 PowerShell 在 NoAgent 模式中註冊您的 SQL Server 2008 虛擬機器： 


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008-WS2008
  ```
  
  使用 PowerShell 在 NoAgent 模式中註冊您的 SQL Server 2008 R2 虛擬機器： 


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008R2-WS2008
  ```

---

## <a name="upgrade-to-full"></a>升級至 full  

已安裝*輕量* IaaS 擴充功能 SQL Server VM 可以使用 Azure 入口網站、Azure CLI 或 PowerShell，升級為_完整_模式。 在 OS 升級為 Windows 2008 R2 和更新版本後，_NoAgent_ 模式中的 SQL Server VM 就可以升級為_完整_模式。 無法降級 - 若要執行此操作，您需要從 SQL VM 資源提供者[取消註冊](#unregister-from-rp) SQL Server VM。 這麼做會移除 **SQL 虛擬機器**_資源_，但不會刪除實際的虛擬機器。 

您可以使用 PowerShell 來查看 SQL Server IaaS 代理程式的目前模式： 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

若要將代理程式模式升級至完整模式： 


### <a name="azure-portal"></a>Azure 入口網站

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 移至 [SQL 虛擬機器](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource)資源。 
1. 選取您的 SQL Server VM，然後選取 **[總覽**]。 
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

# <a name="powershell"></a>[PowerShell](#tab/powershell)

執行以下 PowerShell 程式碼片段：

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  Update-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

---

## <a name="verify-registration-status"></a>驗證註冊狀態
您可以使用 Azure 入口網站、Azure CLI 或 PowerShell，來確認 SQL Server VM 是否已向 SQL VM 資源提供者註冊。 

### <a name="azure-portal"></a>Azure 入口網站 

1. 登入 [Azure 入口網站](https://portal.azure.com)。 
1. 移至 [SQL Server vm](manage-sql-vm-portal.md)。
1. 從清單中選取 SQL Server VM。 如果此處清單中沒有您的 SQL Server VM，代表該 VM 可能尚未向 SQL VM 資源提供者註冊。 
1. 查看 [狀態]下的值。 如果 [狀態] 為 [成功]，則 SQL Server VM 已成功向 SQL VM 資源提供者註冊。 

   ![驗證 SQL RP 註冊狀態](./media/sql-vm-resource-provider-register/verify-registration-status.png)

### <a name="command-line"></a>命令列

請使用 Azure CLI 或 PowerShell 來確認目前的 SQL Server VM 註冊狀態。 如果註冊成功，則 `ProvisioningState` 會顯示 `Succeeded`。 

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)


  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
 ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

  ```powershell-interactive
  Get-AzSqlVM -Name <vm_name> -ResourceGroupName <resource_group>
  ```

---

錯誤指出 SQL Server VM 尚未向資源提供者註冊。 


## <a name="unregister-from-rp"></a>從 RP 取消登錄

若要向 SQL VM 資源提供者取消註冊 SQL Server VM，請使用 Azure 入口網站或 Azure CLI 來刪除 SQL 虛擬機器 *資源* 。 刪除 SQL 虛擬機器 *資源* 並不會刪除 SQL Server VM。 不過，請謹慎使用並遵循步驟，因為在嘗試移除 *資源*時，可能會不小心刪除虛擬機器。 

若要將管理模式從 full 降級，必須將 SQL 虛擬機器從 SQL VM 資源提供者取消註冊。 

### <a name="azure-portal"></a>Azure 入口網站

若要使用 Azure 入口網站向資源提供者取消註冊 SQL Server VM，請遵循下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 流覽至 SQL VM 資源。 
  
   ![SQL 虛擬機器資源](./media/sql-vm-resource-provider-register/sql-vm-manage.png)

1. 選取 [刪除] 。 

   ![刪除 SQL VM 資源提供者](./media/sql-vm-resource-provider-register/delete-sql-vm-resource-provider.png)

1. 輸入 SQL 虛擬機器的名稱，並 **清除虛擬機器旁的核取方塊**。

   ![刪除 SQL VM 資源提供者](./media/sql-vm-resource-provider-register/confirm-delete-of-resource-uncheck-box.png)

   >[!WARNING]
   > 如果無法清除虛擬機器名稱旁的核取方塊，將會完全*刪除*虛擬機器。 清除核取方塊可從資源提供者取消註冊 SQL Server VM，但*不會刪除實際的虛擬機器*。 

1. 選取 [ **刪除** ] 以確認刪除 SQL 虛擬機器 *資源*，而不是 SQL Server VM。 

### <a name="command-line"></a>命令列

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
若要使用 Azure CLI 向資源提供者取消註冊 SQL Server VM，請使用 [az SQL VM delete](/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-delete) 命令。 這會移除 SQL Server VM *資源* ，但不會刪除虛擬機器。 


```azurecli-interactive
az sql vm delete 
  --name <SQL VM resource name> |
  --resource-group <Resource group name> |
  --yes 
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
若要使用 PowerShell 從資源提供者取消註冊 SQL Server VM，請使用 [AzSqlVM](/powershell/module/az.sqlvirtualmachine/remove-azsqlvm)命令。 這會移除 SQL Server VM *資源* ，但不會刪除虛擬機器。 

```powershell-interactive
Remove-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name>
```

---

## <a name="limitations"></a>限制

SQL VM 資源提供者只支援：
- 透過 Azure Resource Manager 部署的 SQL Server VM。 不支援透過傳統模型部署的 SQL Server VM。 
- 部署至公用或 Azure Government 雲端的 SQL Server VM。 不支援部署到其他私人或政府雲端。 


## <a name="frequently-asked-questions"></a>常見問題集 

**我應該註冊從 Azure Marketplace 中 SQL Server 映像佈建的 SQL Server VM 嗎？**

否。 Microsoft 會自動註冊從 Azure Marketplace 中 SQL Server 映像佈建的 VM。 只有當 VM *不是*從 Azure Marketplace 中的 SQL Server 映像佈建，且已自行安裝 SQL Server 時，才需要向 SQL VM 資源提供者註冊。

**SQL VM 資源提供者是否適用於所有客戶？** 

是。 如果客戶未使用 Azure Marketplace 中的 SQL Server 映像，而改為自行安裝 SQL Server，或如果客戶攜帶自己的自訂 VHD，則應該向 SQL VM 資源提供者註冊其 SQL Server VM。 所有訂用帳戶類型 (直接、Enterprise 合約和雲端解決方案提供者) 所擁有的 VM 都可向 SQL VM 資源提供者註冊。

**如果 SQL Server VM 已安裝 SQL Server IaaS 擴充功能，我應該向 SQL VM 資源提供者註冊嗎？**

如果 SQL Server VM 是自行安裝，而不是從 Azure Marketplace 中的 SQL Server 映像佈建，則應該向 SQL VM 資源提供者註冊，即使已安裝 SQL Server IaaS 延伸模組也一樣。 向 SQL VM 資源提供者註冊，會建立 Microsoft.sqlvirtualmachine 類型的新資源。 安裝 SQL Server IaaS 擴充功能並不會建立該資源。

**向 SQL VM 資源提供者註冊時的預設管理模式為何？**

向 SQL VM 資源提供者註冊時的預設管理模式為*完整*。 如果向 SQL VM 資源提供者註冊時未設定 SQL Server 管理屬性，則會將模式設定為完整管理模式，且 SQL Server 服務將會重新啟動。 建議您先在輕量模式中向 SQL VM 資源提供者註冊，然後在維護期間升級至完整模式。 

**向 SQL VM 資源提供者註冊的先決條件為何？**

在輕量模式或 NoAgent 模式中向 SQL VM 資源提供者註冊沒有任何先決條件。 在完整模式中向 SQL VM 資源提供者註冊的先決條件是在 VM 上安裝 SQL Server IaaS 延伸模組，否則 SQL Server 服務將會重新啟動。 

**如果 VM 上未安裝 SQL Server IaaS 擴充功能，是否可以向 SQL VM 資源提供者註冊？**

是，如果未在 VM 上安裝 SQL Server IaaS 延伸模組，則可在輕量管理模式中向 SQL VM 資源提供者註冊。 在輕量模式中，SQL VM 資源提供者會使用主控台應用程式來確認 SQL Server 執行個體的版本和版次。 

向 SQL VM 資源提供者註冊時的預設 SQL 管理模式是_完整_。 如果向 SQL VM 資源提供者註冊時未設定 SQL 管理屬性，則會將模式設定為完整管理模式。 建議您先在輕量模式中向 SQL VM 資源提供者註冊，然後在維護期間升級至完整模式。 

**向 SQL VM 資源提供者註冊會在 VM 上安裝代理程式嗎？**

是，向 SQL VM 資源提供者註冊會在 VM 上安裝代理程式。

SQL Server IaaS 延伸模組依賴代理程式來查詢 SQL Server 的中繼資料。 只有在 NoAgent 模式中 regsitered SQL VM 資源提供者時，才會安裝代理程式的唯一時間

**將會向我的 VM 上的 SQL VM 資源提供者重新開機 SQL Server 註冊嗎？**

這取決於註冊期間所指定的模式。 如果指定了輕量或 NoAgent 模式，則 SQL Server 服務不會重新啟動。 不過，若將管理模式指定為完整，或將管理模式保留空白，則會以完整管理模式安裝 SQL IaaS 延伸模組，而導致 SQL Server 服務重新啟動。 

**向 SQL VM 資源提供者註冊時，輕量和 NoAgent 管理模式有何差異？** 

NoAgent 管理模式僅適用於 Windows Server 2008 上的 SQL Server 2008 和 SQL Server 2008 R2。 這是唯一可用於這些版本的管理模式。 針對所有其他版本的 SQL Server，則可使用輕量和完整管理模式。 

NoAgent 模式需要客戶設定 SQL Server 版本和版次屬性。 輕量模式會查詢 VM 以尋找 SQL Server 執行個體的版本和版次。

**我可在未指定 SQL Server 授權類型的情況下，向 SQL VM 資源提供者註冊嗎？**

否。 當向 SQL VM 資源提供者註冊時，SQL Server 授權類型不是選擇性的屬性。 在所有管理模式 (NoAgent、輕量和完整) 中向 SQL VM 資源提供者註冊時，則必須將 SQL Server 授權類型設定為隨用隨付或 Azure Hybrid Benefit。

**我可將 SQL Server IaaS 延伸模組從 NoAgent 模式升級為完整模式嗎？**

否。 無法將 NoAgent 模式的管理模式升級為完整或輕量。 這是 Windows Server 2008 的技術限制。 您必須先將 OS 升級至 Windows Server 2008 R2 或更新版本，然後才能升級為完整管理模式。 

**我可將 SQL Server IaaS 延伸模組從輕量模式升級為完整模式嗎？**

是。 支援透過 PowerShell 或 Azure 入口網站，將管理模式從輕量升級為完整。 需要重新啟動 SQL Server 服務。

**我可將 SQL Server IaaS 延伸模組從完整模式降級為 NoAgent 或輕量管理模式嗎？**

否。 不支援降級 SQL Server IaaS 延伸模組管理模式。 管理模式無法從完整模式降級為輕量或 NoAgent 模式，也無法從輕量模式降級為 NoAgent 模式。 

若要從完整管理性變更管理性模式，請卸載 SQL Server*資源*，然後以不同的管理模式再次向 sql vm 資源提供者重新註冊 SQL Server vm，以從 sql vm 資源提供者[取消](#unregister-from-rp)註冊 SQL Server VM。

**我可從 Azure 入口網站向 SQL VM 資源提供者註冊嗎？**

否。 您無法在 Azure 入口網站中向 SQL VM 資源提供者註冊。 僅支援使用 Azure CLI 或 PowerShell 向 SQL VM 資源提供者註冊。 

**我可在安裝 SQL Server 之前向 SQL VM 資源提供者註冊 VM 嗎？**

否。 VM 至少應該要有一個 SQL Server (資料庫引擎) 執行個體，才能成功向 SQL VM 資源提供者註冊。 如果 VM 上沒有 SQL Server 執行個體，則新的 Microsoft.SqlVirtualMachine 資源會處於失敗狀態。

**如果有多個 SQL Server 執行個體，我可向 SQL VM 資源提供者註冊 VM 嗎？**

是。 SQL VM 資源提供者只會註冊一個 SQL Server (資料庫引擎) 執行個體。 如果有多個執行個體，則 SQL VM 資源提供者會註冊預設的 SQL Server 執行個體。 如果沒有預設的執行個體，則僅支援在輕量模式中註冊。 若要從輕量升級為完整管理模式，則預設的 SQL Server 執行個體應該存在，或 VM 應該只有一個具名 SQL Server 執行個體。

**我可向 SQL VM 資源提供者註冊 SQL Server 容錯移轉叢集執行個體嗎？**

是。 在輕量模式中，可以向 SQL VM 資源提供者註冊 Azure VM 上的 SQL Server 容錯移轉叢集執行個體。 不過，您無法將 SQL Server 容錯移轉叢集執行個體升級為完整管理模式。

**如果已設定 Always On 可用性群組，可以向 SQL VM 資源提供者註冊我的 VM 嗎？**

是。 如果您正在參與 Always On 可用性群組設定，則向 SQL VM 資源提供者註冊 Azure VM 上的 SQL Server 執行個體沒有任何限制。

**向 SQL VM 資源提供者註冊或升級為完整管理模式的費用為何？**
無。 向 SQL VM 資源提供者註冊，或使用三種管理模式中的任何一種，都不會產生任何相關聯的費用。 使用資源提供者管理您的 SQL Server VM 完全免費。 

**使用不同的管理模式會對效能造成什麼影響？**
使用 *NoAgent* 和*輕量*管理模式時，不會造成任何影響。 使用*完整*管理模式時，對安裝到 OS 的兩個服務影響最小。 這些服務可透過工作管理員監視，並顯示在內建 Windows 服務主控台中。 

這兩個服務名稱為：
- `SqlIaaSExtensionQuery` (顯示名稱 - `Microsoft SQL Server IaaS Query Service`)
- `SQLIaaSExtension` (顯示名稱 - `Microsoft SQL Server IaaS Agent`)


## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列文章： 

* [Windows VM 上的 SQL Server 概觀](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Windows VM 上的 SQL Server 常見問題集](frequently-asked-questions-faq.md)  
* [Windows VM 上的 SQL Server 定價指引](pricing-guidance.md)
* [Windows VM 上的 SQL Server 版本資訊](../../database/doc-changes-updates-release-notes.md)
