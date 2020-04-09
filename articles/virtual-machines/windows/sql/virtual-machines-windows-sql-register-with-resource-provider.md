---
title: 註冊到 SQL VM 資源提供者
description: 將 Azure SQL Server 虛擬機器註冊到 SQL VM 資源提供者,以啟用 Azure 應用商店之外部署的 SQL Server VM 的功能,以及合規性以及增強的可管理性。
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
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985383"
---
# <a name="register-a-sql-server-virtual-machine-in-azure-with-the-sql-vm-resource-provider"></a>在 Azure 中向 SQL VM 資源提供程式註冊 SQL Server 虛擬機器

本文介紹如何在 Azure 中向 SQL VM 資源提供程式註冊 SQL Server 虛擬機器 (VM)。 向資源提供程式註冊會在訂閱中創建**SQL 虛擬機器**_資源_,這是與虛擬機器資源分離的資源。 從資源提供程式取消註冊 SQL Server VM 將刪除**SQL 虛擬機器**_資源_,但不會刪除實際虛擬機器。 

通過 Azure 門戶部署 SQL Server VM Azure 應用商店映像會自動將 SQL Server VM 註冊到資源提供程式。 但是,如果選擇在 Azure 虛擬機器上自行安裝 SQL Server,或者從自訂 VHD 預配 Azure 虛擬機器,則應向資源提供程式註冊 SQL Server VM,以便:

- **功能優勢**:向資源提供者註冊 SQL Server VM 可解鎖[自動修補](virtual-machines-windows-sql-automated-patching.md)、[自動備份](virtual-machines-windows-sql-automated-backup-v2.md)以及監視和可管理功能。 它還可解鎖[許可](virtual-machines-windows-sql-ahb.md)和[版本](virtual-machines-windows-sql-change-edition.md)靈活性。 以前,這些功能僅適用於從 Azure 應用商店部署的 SQL Server VM 映射。 

- **合規性**:向 SQL VM 資源提供程式註冊提供了一種簡化的方法,用於滿足通知 Microsoft Azure 混合權益已啟用的要求,如產品術語中所述。 此過程無需管理每個資源的許可註冊表單。  

- **免費管理**:在所有三種可管理模式下向 SQL VM 資源提供程式註冊是完全免費的。 與資源提供程式或更改管理模式沒有關聯的額外費用。 

- **簡化授權管理**:與 SQL VM 資源提供程式註冊可簡化 SQL Server 授權管理,並允許您使用[Azure 門戶](virtual-machines-windows-sql-manage-portal.md)、Az CLI 或 PowerShell 快速識別啟用 Azure 混合權益的 SQL Server VM: 

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

要利用 SQL VM 資源提供程式,必須首先[向資源提供程式註冊訂閱](#register-subscription-with-rp),使資源提供程式能夠創建該特定訂閱中的資源。

有關使用 SQL VM 資源提供程式的好處的詳細資訊,請觀看此[通道 9](https://channel9.msdn.com/Shows/Data-Exposed/Benefit-from-SQL-VM-Resource-Provider-when-self-installing-SQL-Server-on-Azure?WT.mc_id=dataexposed-c9-niner)影片: 

<iframe src="https://channel9.msdn.com/Shows/Data-Exposed/Benefit-from-SQL-VM-Resource-Provider-when-self-installing-SQL-Server-on-Azure/player" width="960" height="540" allowFullScreen frameBorder="0" title="在 Azure 上自行安裝 SQL 伺服器時,從 SQL VM 資源提供程式中獲益 - Microsoft 頻道 9 影片"></iframe>


## <a name="prerequisites"></a>Prerequisites

要向資源提供程式註冊 SQL Server VM,您需要: 

- [Azure 訂閱](https://azure.microsoft.com/free/)。
- 部署到公共或 Azure 政府雲的 Azure 資源模型[SQL Server VM。](virtual-machines-windows-portal-sql-server-provision.md) 
- 最新版本的[Azure CLI](/cli/azure/install-azure-cli)或[PowerShell](/powershell/azure/new-azureps-module-az)。 

## <a name="management-modes"></a>管理模式

如果[SQL IaaS 擴展](virtual-machines-windows-sql-server-agent-extension.md)尚未安裝,則向 SQL VM 資源提供程式註冊,在註冊過程中指定的三種管理模式下自動安裝 SQL Server IaaS 擴展。 未指定管理模式將在完全管理模式下安裝 SQL IaaS 擴展。  

如果 SQL IaaS 擴展已手動安裝,則該擴展已處於完全管理模式,並且以全模式向資源提供程式註冊將不會重新啟動 SQL Server 服務。

三種管理模式是:

- **輕巧模式**不需要重新啟動 SQL Server,但僅支援更改 SQL Server 的許可證類型和版本。 對具有多個實例的 SQL Server VM 或參與故障轉移叢集實例 (FCI) 的 SQL Server VM 使用此選項。 使用輕量模式時對記憶體或 CPU 沒有影響,並且沒有關聯的成本。 建議先以輕量級模式註冊 SQL Server VM,然後在計劃維護時段內升級到"完整"模式。  

- **完全**模式提供所有功能,但需要重新啟動 SQL Server 和系統管理員許可權。 這是默認情況下手動安裝 SQL IaaS 擴展時安裝的選項。 使用它使用單個實例管理 SQL Server VM。 完全模式安裝兩個視窗服務,對記憶體和 CPU 的影響最小 - 可透過任務管理器監視這些服務。 使用完全可管理性模式無需相關成本。 

- **NoAgent**模式專用於 Windows Server 2008 上安裝的 SQL Server 2008 和 SQL Server 2008 R2。 使用 NoAgent 模式時,對記憶體或 CPU 沒有影響。 使用 NoAgent 可管理性模式無需相關成本。 

您可以使用 PowerShell 檢視 SQL Server IaaS 代理的目前模式: 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

## <a name="register-subscription-with-rp"></a>使用 RP 註冊訂閱

要向 SQL VM 資源提供程式註冊 SQL Server VM,必須首先向資源提供程式註冊訂閱。 這使 SQL VM 資源提供程式能夠在訂閱中創建資源。  可以使用 Azure 門戶、Azure CLI 或 PowerShell 執行此操作。

### <a name="azure-portal"></a>Azure 入口網站

1. 開啟 Azure 門戶並轉到**所有服務**。 
1. 轉到 **「訂閱」** 並選擇感興趣的訂閱。  
1. 在 **「訂閱」** 頁上,轉到**資源提供者**。 
1. 在篩選器中輸入**sql**以啟動與 SQL 相關的資源提供程式。 
1. 選擇**Microsoft.SqlVirtualMachine**提供程式的**註冊**、**重新註冊**或**取消註冊**,具體取決於所需的操作。 

![修改提供者](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)


### <a name="command-line"></a>命令列

使用 Az CLI 或 PowerShell 將 SQL VM 資源提供程式註冊到 Azure 訂閱。 

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

## <a name="register-sql-vm-with-rp"></a>將 SQL VM 註冊為 RP 

### <a name="lightweight-management-mode"></a>輕巧層管理模式

如果[SQL Server IaaS 代理擴展](virtual-machines-windows-sql-server-agent-extension.md)尚未安裝在 VM 上,則建議以輕量模式向 SQL VM 資源提供程式註冊。 這將在[輕量級模式下](#management-modes)安裝 SQL IaaS 擴展,並阻止 SQL Server 服務重新啟動。 然後,您可以隨時升級到完整模式,但這樣做將重新啟動 SQL Server 服務,因此建議等到計劃維護視窗。 

將`PAYG`SQL Server 許可證類型作為即用即付 () 支付每個用時`AHUB`費用,Azure 混合權益 () 使用您自己的授權,或`DR`災難復原 () 來啟動[免費的 DR 副本授權](virtual-machines-windows-sql-high-availability-dr.md#free-dr-replica-in-azure)。

故障轉移群集實例和多實例部署只能在輕量級模式下註冊到 SQL VM 資源提供程式。 

# <a name="az-cli"></a>[AZ CLI](#tab/bash)

使用 Az CLI 以輕巧模式註冊 SQL Server VM: 

  ```azurecli-interactive
  # Register Enterprise or Standard self-installed VM in Lightweight mode
  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type PAYG 
  ```


# <a name="powershell"></a>[PowerShell](#tab/powershell)

使用 PowerShell 以輕巧模式註冊 SQL Server VM:  


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  # Register SQL VM with 'Lightweight' SQL IaaS agent
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType LightWeight  
  ```

---

### <a name="full-management-mode"></a>完整管理模式


如果 SQL IaaS 擴展已手動安裝到 VM 中,則可以以完全模式註冊 SQL Server VM,而無需重新啟動 SQL Server 服務。 **但是,如果 SQL IaaS 擴展尚未安裝,則完全模式下註冊將以完整模式安裝 SQL IaaS 擴展並重新啟動 SQL Server 服務。請謹慎行事。**


要直接以完整模式註冊 SQL Server VM(並可能重新啟動 SQL Server 服務),請使用以下 PowerShell 命令: 

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

### <a name="noagent-management-mode"></a>沒有代理管理模式 

SQL Server 2008 和 2008 R2 安裝在 Windows Server 2008 (_而不是 R2)_ 上,可以在[NoAgent 模式下](#management-modes)註冊到 SQL VM 資源提供程式。 此選項可確保合規性,並允許在 Azure 門戶中監視 SQL Server VM,功能有限。

指定`AHUB`,`PAYG``DR`或指定**sqlLicenseType**,或`SQL2008-WS2008``SQL2008R2-WS2008`指定為**sqlImageOffer**。 

要在 Windows Server 2008 實體註冊 SQL Server 2008 或 2008 R2,請使用以下 Az CLI 或 PowerShell 程式碼段: 


# <a name="az-cli"></a>[AZ CLI](#tab/bash)

在無代理模式下將 SQL Server 2008 VM 與 Az CLI 註冊: 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008-WS2008
 ```
 
 
將 SQL Server 2008 R2 VM 以 NoAgent 模式與 Az CLI 註冊: 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008R2-WS2008
 ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

將 SQL Server 2008 VM 註冊為無代理模式,使用 PowerShell: 


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008-WS2008
  ```
  
  將 SQL Server 2008 R2 VM 註冊為無代理模式,使用 PowerShell: 


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008R2-WS2008
  ```

---

## <a name="upgrade-to-full-management-mode"></a>升級到完全管理模式 

安裝了*輕巧的*IaaS 延伸的 SQL Server VM 可以使用 Azure 門戶、Az CLI 或 PowerShell 將模式升級到_完全_。 在作業系統升級到 Windows 2008 R2 及以上後,_並置於 NoAgent_模式下的 SQL Server VM 可以升級到_完整_。 無法降級 - 為此,您需要從 SQL VM 資源提供程式[中取消註冊](#unregister-vm-from-rp)SQL Server VM。 這樣做將刪除**SQL 虛擬機器**_資源_,但不會刪除實際虛擬機器。 

您可以使用 PowerShell 檢視 SQL Server IaaS 代理的目前模式: 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

要將代理模式升級到完整: 


### <a name="azure-portal"></a>Azure 入口網站

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 轉到[SQL 虛擬機器](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource)資源。 
1. 選擇 SQL Server 虛擬機器,然後選擇 **「概述**」。 
1. 對於具有 NoAgent 或輕巧級 IaaS 模式的 SQL Server VM,選擇**SQL IaaS 擴充訊息提供的「僅授權類型和版本更新**」。

   ![從門戶變更模式的選擇](media/virtual-machines-windows-sql-server-agent-extension/change-sql-iaas-mode-portal.png)

1. 勾選此**選項, 我同意重新啟動虛擬機器上的 SQL Server 服務**「複選框,然後選擇」**確認「 以**將 IaaS 模式升級到完整。 

    ![用來同意在虛擬機器上重新啟動 SQL Server 服務的複選框](media/virtual-machines-windows-sql-server-agent-extension/enable-full-mode-iaas.png)

### <a name="command-line"></a>命令列

# <a name="az-cli"></a>[AZ CLI](#tab/bash)

執行以下 Az CLI 程式碼:

  ```azurecli-interactive
  # Update to full mode
  az sql vm update --name <vm_name> --resource-group <resource_group_name> --sql-mgmt-type full  
  ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

執行以下 PowerShell 程式碼段:

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  Update-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

---

## <a name="verify-registration-status"></a>驗證註冊狀態
可以使用 Azure 門戶、Azure CLI 或 PowerShell 驗證 SQL Server VM 是否已註冊到 SQL VM 資源提供程式。 

### <a name="azure-portal"></a>Azure 入口網站 

1. 登入 [Azure 入口網站](https://portal.azure.com)。 
1. 跳到[SQL Server 虛擬機器](virtual-machines-windows-sql-manage-portal.md)。
1. 從清單中選擇 SQL Server VM。 如果此處未列出 SQL Server VM,則可能尚未向 SQL VM 資源提供程式註冊。 
1. 查看**狀態**下的值。 如果**狀態****成功**,則 SQL Server VM 已成功註冊到 SQL VM 資源提供程式。 

![使用 SQL RP 註冊驗證狀態](media/virtual-machines-windows-sql-register-with-rp/verify-registration-status.png)

### <a name="command-line"></a>命令列

使用 Az CLI 或 PowerShell 驗證當前 SQL Server VM 註冊狀態。 `ProvisioningState`將顯示`Succeeded`註冊是否成功。 

# <a name="az-cli"></a>[AZ CLI](#tab/bash)


  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
 ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

  ```powershell-interactive
  Get-AzSqlVM -Name <vm_name> -ResourceGroupName <resource_group>
  ```

---

錯誤表示 SQL Server VM 尚未向資源提供程式註冊。 


## <a name="unregister-vm-from-rp"></a>從 RP 取消註冊 VM

要將 SQL Server VM 與 SQL VM 資源提供程式取消註冊,請使用 Azure 門戶或 Azure CLI 刪除 SQL 虛擬機器*資源*。 刪除 SQL 虛擬機器*資源*不會刪除 SQL Server VM。 但是,請謹慎操作並仔細執行這些步驟,因為在嘗試刪除*資源*時可能會無意中刪除虛擬機。 

要將管理模式從完全降級,必須取消向 SQL VM 資源提供程式註冊 SQL VM。 

### <a name="azure-portal"></a>Azure 入口網站

要使用 Azure 門戶向資源提供程式取消註冊 SQL Server VM,請按照以下步驟操作:

1. 登入[Azure 門戶](https://portal.azure.com)。
1. 導航到 SQL Server VM 資源。 
  
   ![SQL 虛擬機器資源](media/virtual-machines-windows-sql-manage-portal/sql-vm-manage.png)

1. 選擇 **「刪除**」。 

   ![移除 SQL VM 資源提供者](media/virtual-machines-windows-sql-register-with-rp/delete-sql-vm-resource-provider.png)

1. 鍵入 SQL 虛擬機器的名稱,並**清除虛擬機器旁邊的複選框**。

   ![移除 SQL VM 資源提供者](media/virtual-machines-windows-sql-register-with-rp/confirm-delete-of-resource-uncheck-box.png)

   >[!WARNING]
   > 無法清除虛擬機名稱旁邊的複選框將完全*刪除*虛擬機器。 清除此選項以從資源提供程式取消註冊 SQL Server VM,*但不刪除實際虛擬機器*。 

1. 選擇 **「刪除**」以確認刪除 SQL 虛擬機器*資源*,而不是 SQL Server 虛擬機器。 

### <a name="command-line"></a>命令列

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
要從 Azure CLI 的資源提供程式取消註冊 SQL Server 虛擬機器,請使用[az sql vm 刪除](/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-delete)命令。 這將刪除 SQL Server 虛擬機器*資源*,但不會刪除虛擬機器。 


```azurecli-interactive
az sql vm delete 
  --name <SQL VM resource name> |
  --resource-group <Resource group name> |
  --yes 
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
要從 Azure CLI 的資源提供程式取消註冊 SQL Server 虛擬機器,請使用[New-AzSqlVM](/powershell/module/az.sqlvirtualmachine/new-azsqlvm)命令。 這將刪除 SQL Server 虛擬機器*資源*,但不會刪除虛擬機器。 

```powershell-interactive
Remove-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name>
```

---

## <a name="limitations"></a>限制

SQL VM 資源提供者僅支援:
- 通過 Azure 資源管理員部署的 SQL Server VM。 不支援透過經典模型部署的 SQL Server VM。 
- 部署到公共或 Azure 政府雲的 SQL Server VM。 不支援部署到其他私有或政府雲。 


## <a name="frequently-asked-questions"></a>常見問題集 

**我應該註冊從 Azure 應用商店中的 SQL Server 映像預配的 SQL Server VM 嗎?**

否。 Microsoft 自動註冊從 Azure 應用商店中的 SQL Server 映像預配的 VM。 僅當 VM*未*從 Azure 應用商店中的 SQL Server 映像預配且 SQL Server 已自行安裝時,才需要向 SQL VM 資源提供程式註冊。

**SQL VM 資源提供者是否適用於所有客戶？** 

是。 如果客戶沒有使用 Azure 應用商店中的 SQL Server 映射,而是使用自安裝的 SQL Server,或者帶來了自定義 VHD,則應將其 SQL Server VM VM 註冊給 SQL VM 資源提供程式。 所有類型的訂閱(直接、企業協定和雲端解決方案供應商)擁有的 VM 都可以向 SQL VM 資源供應商註冊。

**如果我的 SQL Server VM 已經安裝了 SQL Server IaaS 擴展,我應該向 SQL VM 資源提供程式註冊嗎?**

如果 SQL Server VM 是自行安裝的,並且未從 Azure 應用商店中的 SQL Server 映像預配,則應向 SQL VM 資源提供程式註冊,即使您安裝了 SQL Server IaaS 擴展。 向 SQL VM 資源提供程式註冊將創建 Microsoft.Sql 虛擬電腦類型的新資源。 安裝 SQL Server IaaS 擴展不會建立該資源。

**註冊 SQL VM 資源提供程式時,預設管理模式是什麼?**

在 SQL VM 資源提供程式註冊時,預設管理模式*已滿*。 如果在向 SQL VM 資源提供程式註冊時未設置 SQL Server 管理屬性,則該模式將設置為完全可管理性,SQL Server 服務將重新啟動。 建議先以輕量級模式向 SQL VM 資源提供程式註冊,然後在維護時段內升級到已滿。 

**向 SQL VM 資源提供程式註冊的先決條件是什麼?**

在輕量級模式或無代理模式下向 SQL VM 資源提供程式註冊沒有先決條件。 在全模式下向 SQL VM 資源提供程式註冊的先決條件是在 VM 上安裝 SQL Server IaaS 擴展,否則 SQL Server 服務將重新啟動。 

**如果我沒有在 VM 上安裝 SQL Server IaaS 擴展,是否可以向 SQL VM 資源提供程式註冊?**

可以,如果您沒有在 VM 上安裝 SQL Server IaaS 擴展,則可以以輕量級管理模式向 SQL VM 資源提供程式註冊。 在輕量級模式下,SQL VM 資源提供程式將使用主控台應用來驗證 SQL Server 實例的版本和版本。 

註冊 SQL VM 資源提供程式時的預設 SQL 管理模式為 _「已滿_」。。 如果在註冊 SQL VM 資源提供程式時未設置 SQL 管理屬性,則該模式將設置為「完全可管理性」。 建議先以輕量級模式向 SQL VM 資源提供程式註冊,然後在維護時段內升級到已滿。 

**向 SQL VM 資源提供程式註冊是否會在我的 VM 上安裝代理?**

否。 向 SQL VM 資源提供程式註冊只會創建新的元數據資源。 它不會在 VM 上安裝代理。

SQL Server IaaS 擴展僅需要實現完全可管理性。 將可管理性模式從輕巧級升級到完全將安裝 SQL Server IaaS 擴展並將重新啟動 SQL Server。

**向 SQL Server VM 資源提供程式註冊是否會在我的 VM 上重新啟動 SQL Server?**

這取決於註冊期間指定的模式。 如果指定了輕量級或 NoAgent 模式,則 SQL Server 服務將不會重新啟動。 但是,將管理模式指定為完整,或將管理模式留空將在完全管理模式下安裝 SQL IaaS 擴展,這將導致 SQL Server 服務重新啟動。 

**在 SQL VM 資源提供程式註冊時,輕量級和非代理管理模式之間的區別是什麼?** 

無代理管理模式僅適用於 Windows Server 2008 上的 SQL Server 2008 和 SQL Server 2008 R2。 這是這些版本的唯一可用管理模式。 對於所有其他版本的 SQL Server,兩種可用的可管理模式是輕量級和完整的。 

無代理模式需要由客戶設定 SQL Server 版本和版本屬性。 輕巧模式查詢 VM 以查找 SQL Server 實體版本和版本。

**是否可以在不指定 SQL Server 許可證類型的情況下向 SQL VM 資源提供程式註冊?**

否。 當您向 SQL VM 資源提供程式註冊時,SQL Server 許可證類型不是可選屬性。 在其所有可管理模式(無代理、輕量級和完整)中註冊 SQL VM 資源提供程式時,必須將 SQL Server 許可證類型設定為即用即付或 Azure 混合權益。

**是否可以將 SQL Server IaaS 擴展從無代理模式升級到完整模式?**

否。 將可管理性模式升級到完全或輕量級不適用於無代理模式。 這是 Windows 伺服器 2008 的技術限制。 您需要首先將作業系統升級到 Windows Server 2008 R2 或更高版本,然後您將能夠升級到完全管理模式。 

**是否可以將 SQL Server IaaS 擴展從輕量級模式升級到完整模式?**

是。 通過 PowerShell 或 Azure 門戶支援將可管理性模式從輕量級升級到完全。 它需要重新啟動 SQL Server 服務。

**是否可以將 SQL Server IaaS 擴展從完整模式降級為無代理或輕量級管理模式?**

否。 不支援降級 SQL Server IaaS 擴展可管理性模式。 可管理性模式不能從完整模式降級為羽量級模式或無代理模式,也不能從輕量模式降級為無代理模式。 

要從完全可管理性更改可管理性模式,請從 SQL Server 資源提供程式[取消註冊](#unregister-vm-from-rp)SQL Server 虛擬機器,刪除 SQL Server*資源*,並在不同的管理模式下再次將 SQL Server VM 重新註冊到 SQL VM 資源提供程式。

**是否可以從 Azure 門戶向 SQL VM 資源提供程式註冊?**

否。 在 Azure 門戶中,向 SQL VM 資源提供程式註冊不可用。 僅 Azure CLI 或 PowerShell 支援向 SQL VM 資源提供程式註冊。 

**在安裝 SQL Server 之前,是否可以向 SQL VM 資源提供程式註冊 VM?**

否。 VM 應至少有一個 SQL Server(資料庫引擎)實例才能成功註冊到 SQL VM 資源提供程式。 如果 VM 上沒有 SQL Server 實例,則新的 Microsoft.SqlVirtualMachine 資源將處於失敗狀態。

**如果有多個 SQL Server 實例,是否可以向 SQL VM 資源提供程式註冊 VM?**

是。 SQL VM 資源提供程式將僅註冊一個 SQL Server(資料庫引擎)實例。 SQL VM 資源提供程式將在多個實例的情況下註冊預設 SQL Server 實例。 如果沒有預設實例,則僅支持在羽量級模式下註冊。 要從輕量級模式升級到完全可管理性模式,預設 SQL Server 實例應存在,或者 VM 應只有一個命名 SQL Server 實例。

**是否可以向 SQL VM 資源提供程式註冊 SQL Server 故障轉移群集實例?**

是。 Azure VM 上的 SQL Server 故障轉移群集實例可以在輕量級模式下註冊到 SQL VM 資源提供程式。 但是,SQL Server 故障轉移群集實例無法升級到完全可管理模式。

**如果配置了「始終打開」可用性組,是否可以向 SQL VM 資源提供程式註冊 VM?**

是。 如果您正在參與「始終打開」可用性組設定,則在 Azure VM 上向 SQL VM 資源提供程式註冊 SQL Server 實例沒有限制。

**註冊 SQL VM 資源提供程式或升級到完全可管理性模式的成本是多少?**
無。 與註冊 SQL VM 資源提供程式或使用三種可管理模式中的任何一種不收取任何費用。 使用資源提供程式管理 SQL Server VM 是完全免費的。 

**使用不同的可管理性模式對性能有何影響?**
使用*NoAgent*和*輕量級*可管理性模式時,沒有任何影響。 使用從安裝到作業系統的兩個服務*的完整*可管理性模式時,影響最小。 這些可以通過任務管理器進行監視,並在內置的 Windows 服務控制台中看到。 

兩個服務名稱是:
- `SqlIaaSExtensionQuery`( 顯示`Microsoft SQL Server IaaS Query Service`名稱 - )
- `SQLIaaSExtension`( 顯示`Microsoft SQL Server IaaS Agent`名稱 - )


## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列文章： 

* [Windows VM 上的 SQL 伺服器概述](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM 上的 SQL 伺服器錯誤](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM 上的 SQL 伺服器定價指南](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM 上的 SQL 伺服器的發行說明](virtual-machines-windows-sql-server-iaas-release-notes.md)
