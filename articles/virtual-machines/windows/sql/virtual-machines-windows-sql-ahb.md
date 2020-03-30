---
title: 更改 Azure 中 SQL Server VM 的許可證模型
description: 瞭解如何使用 Azure 混合權益將 Azure 中的 SQL Server 虛擬機器的許可從即用即付切換到自帶許可證。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 502d1fe599accb29ccc99c9e527f8d1c8e1d52b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201812"
---
# <a name="change-the-license-model-for-a-sql-server-virtual-machine-in-azure"></a>更改 Azure 中 SQL Server 虛擬機器的許可證模型
本文介紹如何使用新的 SQL VM 資來源提供者**Microsoft.SqlVirtualItit**，更改 Azure 中的 SQL Server 虛擬機器 （VM） 的許可證模型。

託管 SQL Server 的 VM 有三個許可證模型：即即付即用、Azure 混合權益和災害復原 （DR）。 可以使用 Azure 門戶、Azure CLI 或 PowerShell 修改 SQL Server VM 的許可證模型。 

- 即**用即付**模型意味著運行 Azure VM 的每秒成本包括 SQL Server 許可證的成本。
- [Azure 混合權益](https://azure.microsoft.com/pricing/hybrid-benefit/)允許您將自己的 SQL Server 許可證與正在運行的 SQL Server 的 VM 一起使用。 
- **災害復原**許可證類型用於 Azure 中的[免費 DR 副本](virtual-machines-windows-sql-high-availability-dr.md#free-dr-replica-in-azure)。 

Azure 混合權益允許在 Azure 虛擬機器上使用具有軟體保證（"合格許可證"）的 SQL Server 許可證。 使用 Azure 混合權益，客戶在 VM 上使用 SQL Server 許可證不收取費用。 但是，他們仍必須支付基礎雲計算（即基本速率）、存儲和備份的成本。 他們還必須支付與使用服務相關的 I/O（如適用）。

根據 Microsoft 產品術語："客戶必須指示他們在 Azure AZURE 下使用 Azure SQL 資料庫（託管實例、彈性池和單個資料庫）、Azure 資料工廠、SQL 伺服器整合服務或 SQL Server 虛擬機器在 Azure 上配置工作負荷時，SQL Server 的混合優勢。

要指示在 Azure VM 上對 SQL Server 使用 Azure 混合權益並符合要求，可以使用三個選項：

- 使用 Azure 應用商店中自帶許可證 SQL Server 映射預配虛擬機器。 此選項僅適用于具有企業協定的客戶。
- 使用 Azure 應用商店中的即付 SQL Server 映射預配虛擬機器，並啟動 Azure 混合權益。
- 在 Azure VM 上自行安裝 SQL 伺服器，手動[向 SQL VM 資來源提供者註冊](virtual-machines-windows-sql-register-with-resource-provider.md)，並啟動 Azure 混合權益。

在預配 VM 時設置 SQL Server 的許可證類型。 之後您可以隨時更改它。 在許可證模型之間切換不會導致停機，不會重新開機 VM 或 SQL Server 服務，不會增加任何額外費用，並立即生效。 事實上，啟動 Azure 混合優勢*會降低成本*。

## <a name="prerequisites"></a>Prerequisites

更改 SQL Server VM 的許可模型具有以下要求： 

- [Azure 訂閱](https://azure.microsoft.com/free/)。
- 在[SQL VM 資來源提供者](virtual-machines-windows-sql-register-with-resource-provider.md)中註冊的[SQL Server VM。](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision)
- [軟體保證](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default)是利用[Azure 混合優勢的要求](https://azure.microsoft.com/pricing/hybrid-benefit/)。 


## <a name="vms-already-registered-with-the-resource-provider"></a>已註冊到資來源提供者的 VM 

# <a name="portal"></a>[入口網站](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

可以直接從門戶修改許可證模型： 

1. 打開[Azure 門戶](https://portal.azure.com)並打開 SQL Server VM 的[SQL 虛擬機器資源](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource)。 
1. 選擇 **"在設置**下**配置**"。 
1. 選擇**Azure 混合權益**選項，然後選中"核取方塊以確認您擁有具有軟體保證的 SQL Server 許可證。 
1. 在 **"配置"** 頁的底部選擇 **"應用**"。 

![門戶中的 Azure 混合優勢](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

您可以使用 Azure CLI 更改許可證模型。  


**Azure 混合優勢**

```azurecli-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

**付你而去**： 

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type PAYG

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type PAYG
```

**災害復原 （DR）**

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type DR

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type DR
```

# <a name="powershell"></a>[電源外殼](#tab/azure-powershell)

您可以使用 PowerShell 更改許可證型號。

**Azure Hybrid Benefit**

```powershell-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType AHUB
```

**一樣付錢**

```powershell-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType PAYG
```

**災害復原** 

```powershell-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType DR
```

---

## <a name="vms-not-registered-with-the-resource-provider"></a>未在資來源提供者註冊的 VM

如果從即用即付 Azure 應用商店映射預配 SQL Server VM，則 SQL Server 許可證類型將為即用即付。 如果使用 Azure 應用商店中的自帶許可證映射預配 SQL Server VM，則許可證類型將為 AHUB。 從預設（即用即付）或自帶許可證 Azure 應用商店映射預配的所有 SQL Server VM 將自動註冊到 SQL VM 資來源提供者，以便他們可以更改[許可證類型](#vms-already-registered-with-the-resource-provider)。

您僅有資格通過 Azure 混合權益在 Azure VM 上自行安裝 SQL 伺服器。 應通過將 SQL Server 許可證設置為 Azure 混合權益來將這些[VM 註冊到 SQL VM 資來源提供者](virtual-machines-windows-sql-register-with-resource-provider.md)，以根據 Microsoft 產品條款指示 Azure 混合權益使用方式。

僅當 SQL Server VM 註冊到 SQL VM 資來源提供者時，才能將 SQL Server VM 的許可證類型更改為即用即付或 Azure 混合權益。

## <a name="remarks"></a>備註

- Azure 雲解決方案供應商 （CSP） 客戶可以使用 Azure 混合權益，首先部署即用即付 VM，然後將其轉換為自帶許可證（如果他們具有有效的軟體保證）。
- 如果刪除 SQL Server VM 資源，將返回映射的硬編碼許可證設置。 
- 更改許可證模型的功能是 SQL VM 資來源提供者的一項功能。 通過 Azure 門戶部署 Azure 應用商店映射會自動向資來源提供者註冊 SQL Server VM。 但是，自行安裝 SQL Server 的客戶將需要手動[註冊其 SQL Server VM。](virtual-machines-windows-sql-register-with-resource-provider.md) 
- 將 SQL Server VM 添加到可用性集需要重新創建 VM。 因此，添加到可用性集的任何 VM 都將回到預設的即用即付許可證類型。 需要再次啟用 Azure 混合權益。 


## <a name="limitations"></a>限制

更改許可證模型是：
   - 僅適用于具有[軟體保證的客戶](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-overview)。
   - 僅適用于 SQL Server 的標準和企業版。 不支援 Express、Web 和開發人員的許可證更改。 
   - 僅支援通過 Azure 資源管理器模型部署的虛擬機器。 不支援通過經典模型部署的虛擬機器。 
   - 僅適用于公共或 Azure 政府雲。 
   - 僅在具有單個網路介面 （NIC） 的虛擬機器上受支援。 


## <a name="known-errors"></a>已知錯誤

### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found"></a>找不到資源組"資源組>"下的資源"Microsoft.SqlVirtual\<\<電腦/SqlVirtual電腦/SqlVirtualMachines/資源組>"。

當您嘗試更改尚未向 SQL VM 資來源提供者註冊的 SQL Server VM 上的許可證模型時，將發生此錯誤：

`The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>' under resource group '\<resource-group>' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set.`

您需要向資來源提供者註冊訂閱，然後[向資來源提供者註冊 SQL Server VM。](virtual-machines-windows-sql-register-with-resource-provider.md) 


### <a name="the-virtual-machine-vmname-has-more-than-one-nic-associated"></a>虛擬機器\<"vmname"\>有多個 NIC 關聯

此錯誤發生在具有多個 NIC 的虛擬機器上。 在更改許可模型之前，請刪除其中一個 NIC。 儘管在更改許可證模型後可以將 NIC 添加回 VM，但 Azure 門戶中的操作（如自動備份和修補）將不再受支援。 


## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列文章： 

* [Windows VM 上的 SQL 伺服器概述](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM 上的 SQL 伺服器常見問題解答](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM 上的 SQL 伺服器定價指南](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM 上的 SQL 伺服器的版本資訊](virtual-machines-windows-sql-server-iaas-release-notes.md)


