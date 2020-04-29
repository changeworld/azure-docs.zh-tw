---
title: 向 SQL VM 資源提供者註冊
description: 向 SQL VM 資源提供者註冊您的 Azure SQL Server 虛擬機器，以啟用在 Azure Marketplace 以外部署 SQL Server Vm 的功能、合規性，以及改良的管理能力。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: d9c1cff53d5d0f0385d3d61938c7fb6309efb7b1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80985383"
---
# <a name="register-a-sql-server-virtual-machine-in-azure-with-the-sql-vm-resource-provider"></a>在 Azure 中使用 SQL VM 資源提供者註冊 SQL Server 虛擬機器

本文說明如何在 Azure 中使用 SQL VM 資源提供者註冊您的 SQL Server 虛擬機器（VM）。 向資源提供者註冊會在您的訂用帳戶中建立**SQL 虛擬機器**_資源_，這是與虛擬機器資源不同的資源。 從資源提供者取消註冊您的 SQL Server VM 將會移除**SQL 虛擬機器**_資源_，但不會捨棄實際的虛擬機器。 

透過 Azure 入口網站部署 SQL Server VM Azure Marketplace 映射，會自動向資源提供者註冊 SQL Server VM。 不過，如果您選擇在 Azure 虛擬機器上自行安裝 SQL Server，或從自訂 VHD 布建 Azure 虛擬機器，您應該向資源提供者註冊您的 SQL Server VM 以進行下列動作：

- **功能優點**：向資源提供者註冊您的 SQL Server VM，可解除鎖定[自動修補](virtual-machines-windows-sql-automated-patching.md)、[自動備份](virtual-machines-windows-sql-automated-backup-v2.md)，以及監視和管理功能。 它也會將[授權](virtual-machines-windows-sql-ahb.md)和[版本](virtual-machines-windows-sql-change-edition.md)彈性解除鎖定。 先前，這些功能僅適用于從 Azure Marketplace 部署的 SQL Server VM 映射。 

- **合規性**：向 SQL VM 資源提供者註冊提供簡化的方法，以通知 Microsoft，已依照產品條款中所指定的方式來啟用 Azure Hybrid Benefit。 此流程不需要管理每個資源的授權註冊表單。  

- **免費管理**：在這三種管理模式中使用 SQL VM 資源提供者註冊，完全免費。 與資源提供者沒有相關聯的額外成本，或變更管理模式。 

- **簡化的授權管理**：向 SQL VM 資源提供者註冊會簡化 SQL Server 授權管理，並可讓您使用[AZURE 入口網站](virtual-machines-windows-sql-manage-portal.md)、Az CLI 或 PowerShell，快速識別已啟用 Azure Hybrid Benefit 的 SQL Server vm： 

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

若要利用 SQL VM 資源提供者，您必須先[向資源提供者註冊您的訂用](#register-subscription-with-rp)帳戶，讓資源提供者能夠在該特定訂用帳戶內建立資源。

如需有關使用 SQL VM 資源提供者之優點的詳細資訊，請觀看此[channel9](https://channel9.msdn.com/Shows/Data-Exposed/Benefit-from-SQL-VM-Resource-Provider-when-self-installing-SQL-Server-on-Azure?WT.mc_id=dataexposed-c9-niner)影片： 

<iframe src="https://channel9.msdn.com/Shows/Data-Exposed/Benefit-from-SQL-VM-Resource-Provider-when-self-installing-SQL-Server-on-Azure/player" width="960" height="540" allowFullScreen frameBorder="0" title="在 Azure 上自行安裝 SQL Server 時，從 SQL VM 資源提供者獲益-Microsoft Channel 9 影片"></iframe>


## <a name="prerequisites"></a>先決條件

若要向資源提供者註冊您的 SQL Server VM，您將需要： 

- [Azure 訂](https://azure.microsoft.com/free/)用帳戶。
- Azure 資源模型 SQL Server 部署至公用或 Azure Government 雲端的[VM](virtual-machines-windows-portal-sql-server-provision.md) 。 
- [Azure CLI](/cli/azure/install-azure-cli)或[PowerShell](/powershell/azure/new-azureps-module-az)的最新版本。 

## <a name="management-modes"></a>管理模式

如果尚未安裝[Sql IaaS 擴充](virtual-machines-windows-sql-server-agent-extension.md)功能，向 sql VM 資源提供者註冊會自動以下列三種管理模式之一安裝 SQL Server IaaS 延伸模組，在註冊過程中指定。 若未指定管理模式，將會以完整管理模式安裝 SQL IaaS 延伸模組。  

如果已手動安裝 SQL IaaS 擴充功能，則它已處於完整管理模式，而且以完整模式向資源提供者註冊，將不會重新開機 SQL Server 服務。

這三種管理模式如下：

- **輕量**模式不需要重新開機 SQL Server，而只支援變更 SQL Server 的授權類型和版本。 針對具有多個實例的 SQL Server Vm，或參與容錯移轉叢集實例（FCI），請使用此選項。 使用輕量模式時，不會對記憶體或 CPU 造成任何影響，也不會產生相關聯的成本。 建議您先在輕量模式中註冊您的 SQL Server VM，然後在排程的維護期間升級至完整模式。  

- **完整**模式會提供所有功能，但需要重新開機 SQL Server 和系統管理員許可權。 這是手動安裝 SQL IaaS 擴充功能時預設安裝的選項。 使用它來管理具有單一實例的 SQL Server VM。 完整模式會安裝兩個對記憶體和 CPU 的影響最小的 windows 服務，這可以透過 [工作管理員] 來監視。 使用完整的管理模式並不會產生任何費用。 

- **NoAgent**模式專門用於在 Windows Server 2008 上安裝 SQL Server 2008 和 SQL Server 2008 R2。 使用 NoAgent 模式時，不會影響記憶體或 CPU。 使用 NoAgent 管理性模式沒有相關聯的成本。 

您可以使用 PowerShell 來查看 SQL Server IaaS 代理程式的目前模式： 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

## <a name="register-subscription-with-rp"></a>向 RP 註冊訂用帳戶

若要向 SQL VM 資源提供者註冊您的 SQL Server VM，您必須先向資源提供者註冊您的訂用帳戶。 這會讓 SQL VM 資源提供者能夠在您的訂用帳戶內建立資源。  您可以使用 Azure 入口網站、Azure CLI 或 PowerShell 來執行此動作。

### <a name="azure-portal"></a>Azure 入口網站

1. 開啟 [Azure 入口網站並移至 [**所有服務**]。 
1. 移至 [訂用帳戶]，然後選取您感**的訂閱。**  
1. 在 [**訂閱**] 頁面上，移至 [**資源提供者**]。 
1. 在篩選準則中輸入**sql** ，以顯示 sql 相關的資源提供者。 
1. 針對**microsoft.sqlvirtualmachine**提供者，選取 [**註冊**]、[**重新註冊**] 或 [**取消**登錄]，視您想要的動作而定。 

![修改提供者](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)


### <a name="command-line"></a>命令列

使用 Az CLI 或 PowerShell，將您的 SQL VM 資源提供者註冊到您的 Azure 訂用帳戶。 

# <a name="az-cli"></a>[AZ CLI](#tab/bash)

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

## <a name="register-sql-vm-with-rp"></a>向 RP 註冊 SQL VM 

### <a name="lightweight-management-mode"></a>輕量管理模式

如果 VM 上未安裝[SQL Server IaaS 代理程式擴充](virtual-machines-windows-sql-server-agent-extension.md)功能，則建議使用輕量模式向 SQL VM 資源提供者註冊。 這會以[輕量模式](#management-modes)安裝 SQL IaaS 擴充功能，並防止 SQL Server 服務重新開機。 然後，您可以隨時升級至完整模式，但是這麼做會重新開機 SQL Server 服務，因此建議您等到排程的維護時段。 

提供 SQL Server 授權類型做為隨用`PAYG`隨付（）以依據使用量付費，Azure Hybrid Benefit （`AHUB`）以使用您自己的授權，或使用嚴重損壞修復（`DR`）來啟動免費的[DR 複本授權](virtual-machines-windows-sql-high-availability-dr.md#free-dr-replica-in-azure)。

容錯移轉叢集實例和多重實例部署只能以輕量模式向 SQL VM 資源提供者註冊。 

# <a name="az-cli"></a>[AZ CLI](#tab/bash)

使用 Az CLI 在輕量模式中註冊 SQL Server VM： 

  ```azurecli-interactive
  # Register Enterprise or Standard self-installed VM in Lightweight mode
  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type PAYG 
  ```


# <a name="powershell"></a>[PowerShell](#tab/powershell)

使用 PowerShell 在輕量模式中註冊 SQL Server VM：  


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  # Register SQL VM with 'Lightweight' SQL IaaS agent
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType LightWeight  
  ```

---

### <a name="full-management-mode"></a>完整管理模式


如果已手動將 SQL IaaS 擴充功能安裝至 VM，則您可以在完整模式中註冊 SQL Server VM，而不需要重新開機 SQL Server 服務。 **不過，如果尚未安裝 SQL IaaS 擴充功能，以完整模式註冊會以完整模式安裝 SQL IaaS 延伸模組，然後重新開機 SQL Server 服務。請小心進行。**


若要以完整模式（可能會重新開機您的 SQL Server 服務）直接註冊您的 SQL Server VM，請使用下列 PowerShell 命令： 

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

### <a name="noagent-management-mode"></a>NoAgent 管理模式 

安裝在 Windows Server 2008 （_不是 R2_）上的 SQL Server 2008 和 2008 R2 可以在[NoAgent 模式](#management-modes)中向 SQL VM 資源提供者註冊。 此選項可確保合規性，並允許在功能有限的 Azure 入口網站中監視 SQL Server 的 VM。

`AHUB`將`PAYG`、 `DR`或指定為**sqlLicenseType**，並`SQL2008-WS2008`將或`SQL2008R2-WS2008`指定為**sqlImageOffer**。 

若要在 Windows Server 2008 實例上註冊您的 SQL Server 2008 或 2008 R2，請使用下列 Az CLI 或 PowerShell 程式碼片段： 


# <a name="az-cli"></a>[AZ CLI](#tab/bash)

使用 Az CLI 在 NoAgent 模式中註冊您的 SQL Server 2008 VM： 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008-WS2008
 ```
 
 
使用 Az CLI 在 NoAgent 模式中註冊您的 SQL Server 2008 R2 VM： 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008R2-WS2008
 ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

使用 PowerShell 在 NoAgent 模式中註冊 SQL Server 2008 VM： 


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008-WS2008
  ```
  
  使用 PowerShell 在 NoAgent 模式中註冊 SQL Server 2008 R2 VM： 


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008R2-WS2008
  ```

---

## <a name="upgrade-to-full-management-mode"></a>升級為完整管理模式 

已安裝*羽量級*IaaS 延伸模組 SQL Server vm 可以使用 Azure 入口網站、Az CLI 或 PowerShell，將模式升級為_full_ 。 在作業系統升級為 Windows 2008 R2 和更新版本之後， _NoAgent_模式中的 SQL Server vm 可以升級至_完整_版本。 不可能降級-若要這麼做，您必須從 SQL VM 資源提供者[取消註冊](#unregister-vm-from-rp)SQL Server VM。 這麼做會移除**SQL 虛擬機器**_資源_，但不會刪除實際的虛擬機器。 

您可以使用 PowerShell 來查看 SQL Server IaaS 代理程式的目前模式： 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

若要將代理程式模式升級為完整： 


### <a name="azure-portal"></a>Azure 入口網站

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 移至您的[SQL 虛擬機器](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource)資源。 
1. 選取您的 SQL Server 虛擬機器，然後選取 **[總覽**]。 
1. 針對具有 NoAgent 或輕量 IaaS 模式的 SQL Server Vm，請選取 [**唯一授權類型] 和 [版本更新]，並提供 SQL IaaS 擴充**功能訊息。

   ![從入口網站變更模式的選項](media/virtual-machines-windows-sql-server-agent-extension/change-sql-iaas-mode-portal.png)

1. 選取 [**我同意在虛擬機器上重新開機 SQL Server 服務**] 核取方塊，然後選取 [**確認**] 將 IaaS 模式升級為 [完整]。 

    ![核取方塊，同意在虛擬機器上重新開機 SQL Server 服務](media/virtual-machines-windows-sql-server-agent-extension/enable-full-mode-iaas.png)

### <a name="command-line"></a>命令列

# <a name="az-cli"></a>[AZ CLI](#tab/bash)

執行下列 Az CLI 程式碼片段：

  ```azurecli-interactive
  # Update to full mode
  az sql vm update --name <vm_name> --resource-group <resource_group_name> --sql-mgmt-type full  
  ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

執行下列 PowerShell 程式碼片段：

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  Update-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

---

## <a name="verify-registration-status"></a>確認註冊狀態
您可以使用 Azure 入口網站、Azure CLI 或 PowerShell，來確認您的 SQL Server VM 是否已向 SQL VM 資源提供者註冊。 

### <a name="azure-portal"></a>Azure 入口網站 

1. 登入 [Azure 入口網站](https://portal.azure.com)。 
1. 移至您的[SQL Server 虛擬機器](virtual-machines-windows-sql-manage-portal.md)。
1. 從清單中選取您的 SQL Server VM。 如果您的 SQL Server VM 未列于此處，可能尚未向 SQL VM 資源提供者註冊。 
1. 查看 [**狀態**] 底下的值。 如果**狀態**為 [**成功**]，則 SQL Server 的 vm 已成功向 SQL vm 資源提供者註冊。 

![使用 SQL RP 註冊來驗證狀態](media/virtual-machines-windows-sql-register-with-rp/verify-registration-status.png)

### <a name="command-line"></a>命令列

使用 Az CLI 或 PowerShell 來確認目前 SQL Server 的 VM 註冊狀態。 `ProvisioningState`會顯示`Succeeded`註冊是否成功。 

# <a name="az-cli"></a>[AZ CLI](#tab/bash)


  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
 ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

  ```powershell-interactive
  Get-AzSqlVM -Name <vm_name> -ResourceGroupName <resource_group>
  ```

---

錯誤表示 SQL Server 的 VM 尚未向資源提供者註冊。 


## <a name="unregister-vm-from-rp"></a>從 RP 取消註冊 VM

若要使用 SQL VM 資源提供者取消註冊您的 SQL Server VM，請使用 Azure 入口網站或 Azure CLI 刪除 SQL 虛擬機器*資源*。 刪除 SQL 虛擬機器*資源*並不會刪除 SQL SERVER 的 VM。 不過，請謹慎使用並遵循步驟，因為在嘗試移除*資源*時，可能會不小心刪除虛擬機器。 

若要將管理模式從完整降級，必須使用 SQL VM 資源提供者取消註冊 SQL VM。 

### <a name="azure-portal"></a>Azure 入口網站

若要使用 Azure 入口網站向資源提供者取消註冊您的 SQL Server VM，請遵循下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 流覽至 SQL Server 的 VM 資源。 
  
   ![SQL 虛擬機器資源](media/virtual-machines-windows-sql-manage-portal/sql-vm-manage.png)

1. 選取 [刪除]  。 

   ![刪除 SQL VM 資源提供者](media/virtual-machines-windows-sql-register-with-rp/delete-sql-vm-resource-provider.png)

1. 輸入 SQL 虛擬機器的名稱，並**清除虛擬機器旁的核取方塊**。

   ![刪除 SQL VM 資源提供者](media/virtual-machines-windows-sql-register-with-rp/confirm-delete-of-resource-uncheck-box.png)

   >[!WARNING]
   > 如果無法清除虛擬機器名稱旁的核取方塊，將會完全*刪除*虛擬機器。 清除核取方塊以從資源提供者取消註冊 SQL Server VM，但*不要刪除實際的虛擬機器*。 

1. 選取 [**刪除**] 以確認刪除 SQL 虛擬機器*資源*，而不是 SQL Server 的虛擬機器。 

### <a name="command-line"></a>命令列

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
若要使用 Azure CLI 從資源提供者取消註冊您的 SQL Server 虛擬機器，請使用[az SQL vm delete](/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-delete)命令。 這會移除 SQL Server 虛擬機器*資源*，但不會刪除虛擬機器。 


```azurecli-interactive
az sql vm delete 
  --name <SQL VM resource name> |
  --resource-group <Resource group name> |
  --yes 
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
若要使用 Azure CLI 從資源提供者取消註冊您的 SQL Server 虛擬機器，請使用[AzSqlVM](/powershell/module/az.sqlvirtualmachine/new-azsqlvm)命令。 這會移除 SQL Server 虛擬機器*資源*，但不會刪除虛擬機器。 

```powershell-interactive
Remove-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name>
```

---

## <a name="limitations"></a>限制

SQL VM 資源提供者僅支援：
- SQL Server 透過 Azure Resource Manager 部署的 Vm。 不支援透過傳統模型部署的 SQL Server Vm。 
- SQL Server 部署至公用或 Azure Government 雲端的 Vm。 不支援對其他私人或政府雲端進行部署。 


## <a name="frequently-asked-questions"></a>常見問題集 

**我應該在 Azure Marketplace 中註冊從 SQL Server 映射布建的 SQL Server VM 嗎？**

否。 Microsoft 會從 Azure Marketplace 中的 SQL Server 映射自動註冊布建的 Vm。 只有在*未*從 Azure Marketplace 中的 SQL Server 映射布建 VM，而且 SQL Server 已自行安裝時，才需要向 SQL VM 資源提供者註冊。

**SQL VM 資源提供者是否適用於所有客戶？** 

是。 客戶應該向 SQL VM 資源提供者註冊其 SQL Server 的 Vm （如果未使用 Azure Marketplace 的 SQL Server 映射），而改為自行安裝的 SQL Server，或其是否有自訂的 VHD。 所有訂閱類型（直接、Enterprise 合約和雲端解決方案提供者）所擁有的 Vm 可以向 SQL VM 資源提供者註冊。

**如果我的 SQL Server VM 已安裝 SQL Server IaaS 延伸模組，我應該向 SQL VM 資源提供者註冊嗎？**

如果您的 SQL Server VM 已自行安裝，而不是從 Azure Marketplace 中的 SQL Server 映射布建，則您應該向 SQL VM 資源提供者註冊，即使您已安裝 SQL Server IaaS 延伸模組也一樣。 向 SQL VM 資源提供者註冊，會建立 SqlVirtualMachines 類型的新資源。 安裝 SQL Server IaaS 延伸模組並不會建立該資源。

**向 SQL VM 資源提供者註冊時，預設的管理模式是什麼？**

當您向 SQL VM 資源提供者註冊時，預設的管理模式已*滿*。 當您向 SQL VM 資源提供者註冊時，如果未設定 SQL Server 管理屬性，則會將模式設定為完整管理性，而您的 SQL Server 服務將會重新開機。 建議您先以輕量模式向 SQL VM 資源提供者註冊，然後在維護期間升級為完整。 

**向 SQL VM 資源提供者註冊的必要條件為何？**

在輕量模式或無代理程式模式中，不需要向 SQL VM 資源提供者註冊。 以完整模式向 SQL VM 資源提供者註冊的必要條件是已在 VM 上安裝 SQL Server IaaS 延伸模組，否則 SQL Server 服務將會重新開機。 

**如果 VM 上未安裝 SQL Server IaaS 擴充功能，是否可以向 SQL VM 資源提供者註冊？**

是，如果您未在 VM 上安裝 SQL Server IaaS 擴充功能，您可以在輕量管理模式中向 SQL VM 資源提供者註冊。 在輕量模式中，SQL VM 資源提供者會使用主控台應用程式來確認 SQL Server 實例的版本。 

向 SQL VM 資源提供者註冊時的預設 SQL 管理模式已_滿_。 如果向 SQL VM 資源提供者註冊時未設定 SQL 管理屬性，則會將模式設定為完整管理能力。 建議您先以輕量模式向 SQL VM 資源提供者註冊，然後在維護期間升級為完整。 

**向 SQL VM 資源提供者註冊會在我的 VM 上安裝代理程式嗎？**

否。 向 SQL VM 資源提供者註冊，只會建立新的中繼資料資源。 它不會在 VM 上安裝代理程式。

只有在啟用完整的管理能力時，才需要 SQL Server IaaS 延伸模組。 將管理性模式從輕量升級為 full 會安裝 SQL Server IaaS 延伸模組，並將 SQL Server 重新開機。

**是否會向 VM 上的 SQL Server VM 資源提供者註冊 SQL Server 重新開機？**

這取決於註冊期間所指定的模式。 如果指定了輕量或 NoAgent 模式，則 SQL Server 服務將不會重新開機。 不過，將管理模式指定為 [完整]，或將 [管理模式] 保留空白，將會以完整管理模式安裝 SQL IaaS 延伸模組，這會導致 SQL Server 服務重新開機。 

**向 SQL VM 資源提供者註冊時，輕量和無代理程式管理模式有何差異？** 

無代理程式管理模式僅適用于 Windows Server 2008 上的 SQL Server 2008 和 SQL Server 2008 R2。 這是唯一可用於這些版本的管理模式。 對於所有其他版本的 SQL Server，這兩個可用的管理模式是輕量且完整的。 

無代理程式模式需要客戶設定 SQL Server 版本和版本屬性。 輕量模式會查詢 VM，以尋找 SQL Server 實例的版本。

**我可以向 SQL VM 資源提供者註冊，而不需要指定 SQL Server 授權類型嗎？**

否。 當您向 SQL VM 資源提供者註冊時，SQL Server 授權類型不是選擇性的屬性。 在所有管理性模式（無代理程式、輕量和完整）中向 SQL VM 資源提供者註冊時，您必須將 SQL Server 授權類型設定為隨用隨付或 Azure Hybrid Benefit。

**我可以將 SQL Server IaaS 擴充功能從無代理程式模式升級為完整模式嗎？**

否。 無代理程式模式無法使用 [完整] 或 [輕量] 升級管理模式。 這是 Windows Server 2008 的技術限制。 您必須先將 OS 升級至 Windows Server 2008 R2 或更新版本，然後才能升級至完整管理模式。 

**是否可以將 SQL Server IaaS 延伸模組從輕量模式升級至完整模式？**

是。 透過 PowerShell 或 Azure 入口網站，可支援從輕型升級至完整的管理性模式。 需要重新開機 SQL Server 服務。

**我可以將 SQL Server IaaS 擴充功能從完整模式降級為無代理程式或輕量管理模式嗎？**

否。 不支援降級 SQL Server IaaS 擴充功能管理模式。 管理模式無法從完整模式降級為輕量或無代理程式模式，而且無法從輕量模式降級為無代理程式模式。 

若要從完整的管理性中變更管理性模式，請卸載 SQL Server*資源*，然後在不同的管理模式中再次向 SQL vm 資源提供者重新註冊 SQL Server VM，以從 SQL Server 資源提供者[取消](#unregister-vm-from-rp)登錄 SQL Server 的虛擬機器。

**我可以從 Azure 入口網站向 SQL VM 資源提供者註冊嗎？**

否。 Azure 入口網站中無法使用 SQL VM 資源提供者註冊。 只有 Azure CLI 或 PowerShell 才支援向 SQL VM 資源提供者註冊。 

**在安裝 SQL Server 之前，我可以向 SQL VM 資源提供者註冊 VM 嗎？**

否。 VM 至少應該要有一個 SQL Server （資料庫引擎）實例，才能成功向 SQL VM 資源提供者註冊。 如果 VM 上沒有 SQL Server 實例，新的 Microsoft.sqlvirtualmachine 資源會處於失敗狀態。

**如果有多個 SQL Server 實例，可以向 SQL VM 資源提供者註冊 VM 嗎？**

是。 SQL VM 資源提供者只會註冊一個 SQL Server （資料庫引擎）實例。 SQL VM 資源提供者會在多個實例的情況下，註冊預設的 SQL Server 實例。 如果沒有預設實例，則只支援在輕量模式下註冊。 若要從輕量升級到完整的管理性模式，預設 SQL Server 實例應該存在，或 VM 應該只有一個名為 SQL Server 實例。

**我可以向 SQL VM 資源提供者註冊 SQL Server 容錯移轉叢集實例嗎？**

是。 在輕量模式中，可以向 SQL VM 資源提供者註冊 Azure VM 上的 SQL Server 容錯移轉叢集實例。 不過，SQL Server 容錯移轉叢集實例無法升級至完整的管理性模式。

**如果已設定 Always On 可用性群組，可以向 SQL VM 資源提供者註冊我的 VM 嗎？**

是。 如果您要參與 Always On 可用性群組設定，則在 Azure VM 上使用 SQL VM 資源提供者註冊 SQL Server 實例沒有任何限制。

**向 SQL VM 資源提供者註冊的成本為何，或升級為完整的管理性模式？**
無。 向 SQL VM 資源提供者註冊，或使用三種管理模式中的任何一種，都不會產生任何費用。 使用資源提供者管理您的 SQL Server VM 完全免費。 

**使用不同的管理模式會對效能有何影響？**
使用*NoAgent*和*輕量*管理模式時，不會有任何影響。 使用安裝在作業系統上的兩個服務的*完整*管理模式時，會產生最小的影響。 這些可以透過 [工作管理員] 監視，並在內建的 Windows 服務主控台中看到。 

這兩個服務名稱包括：
- `SqlIaaSExtensionQuery`（顯示名稱- `Microsoft SQL Server IaaS Query Service`）
- `SQLIaaSExtension`（顯示名稱- `Microsoft SQL Server IaaS Agent`）


## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列文章： 

* [Windows VM 上的 SQL Server 總覽](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM 上的 SQL Server 常見問題](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM 上 SQL Server 的定價指導方針](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM 上 SQL Server 的版本資訊](virtual-machines-windows-sql-server-iaas-release-notes.md)
