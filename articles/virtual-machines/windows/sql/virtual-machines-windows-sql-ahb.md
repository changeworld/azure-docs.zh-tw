---
title: 變更 Azure 中 SQL Server VM 的授權模型
description: 瞭解如何在 Azure 中切換 SQL Server 虛擬機器的授權，從隨用隨付，到使用 Azure Hybrid Benefit 攜帶您自己的授權。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77201812"
---
# <a name="change-the-license-model-for-a-sql-server-virtual-machine-in-azure"></a>變更 Azure 中 SQL Server 虛擬機器的授權模型
本文說明如何使用新的 SQL VM 資源提供者**microsoft.sqlvirtualmachine**，在 Azure 中變更 SQL Server 虛擬機器（VM）的授權模型。

裝載 SQL Server 的 VM 有三種授權模式：隨用隨付、Azure Hybrid Benefit 和嚴重損壞修復（DR）。 您可以使用 Azure 入口網站、Azure CLI 或 PowerShell 來修改 SQL Server VM 的授權模型。 

- **隨用隨付**模型表示執行 Azure VM 的每秒成本，包括 SQL Server 授權的成本。
- [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)可讓您將自己的 SQL Server 授權與執行 SQL SERVER 的 VM 搭配使用。 
- 「嚴重損壞**修復**授權類型」用於 Azure 中的[免費 DR 複本](virtual-machines-windows-sql-high-availability-dr.md#free-dr-replica-in-azure)。 

Azure Hybrid Benefit 允許在 Azure 虛擬機器上使用具有軟體保證（「合格授權」）的 SQL Server 授權。 使用 Azure Hybrid Benefit，客戶不需支付在 VM 上使用 SQL Server 授權的費用。 但它們仍然必須支付基礎雲端計算成本（也就是基本費率）、儲存體和備份的費用。 他們也必須支付與服務使用相關聯的 i/o （如適用）。

根據 Microsoft 產品條款：「客戶必須指出他們在 Azure 上設定工作負載時，使用 Azure SQL Database （受控執行個體、彈性集區和單一資料庫）、Azure Data Factory、SQL Server Integration Services 或 SQL Server 虛擬機器的 Azure Hybrid Benefit。」

若要指出在 Azure VM 上使用 Azure Hybrid Benefit 進行 SQL Server 並符合規範，您有三個選項：

- 使用來自 Azure Marketplace 的自備授權 SQL Server 映射來布建虛擬機器。 此選項僅適用于具有 Enterprise 合約的客戶。
- 使用 Azure Marketplace 的隨用隨付 SQL Server 映射來布建虛擬機器，並啟動 Azure Hybrid Benefit。
- 在 Azure VM 上自行安裝 SQL Server、手動[向 SQL VM 資源提供者註冊](virtual-machines-windows-sql-register-with-resource-provider.md)，然後啟動 Azure Hybrid Benefit。

布建 VM 時，會設定 SQL Server 的授權類型。 之後可以隨時變更。 在授權模型之間切換並不會造成停機、不會重新開機 VM 或 SQL Server 服務，也不會增加任何額外的成本，而且會立即生效。 事實上，啟用 Azure Hybrid Benefit 可*降低*成本。

## <a name="prerequisites"></a>先決條件

變更 SQL Server VM 的授權模型具有下列需求： 

- [Azure 訂](https://azure.microsoft.com/free/)用帳戶。
- 向[SQL vm 資源提供者](virtual-machines-windows-sql-register-with-resource-provider.md)註冊的[SQL Server VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) 。
- [軟體保證](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default)是使用[Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)的需求。 


## <a name="vms-already-registered-with-the-resource-provider"></a>已向資源提供者註冊的 Vm 

# <a name="portal"></a>[入口網站](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

您可以直接從入口網站修改授權模型： 

1. 開啟[Azure 入口網站](https://portal.azure.com)，並開啟 SQL Server VM 的[SQL 虛擬機器資源](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource)。 
1. 選取 [**設定**] 底下的 **[設定]** 。 
1. 選取 [ **Azure Hybrid Benefit** ] 選項，然後選取核取方塊以確認您擁有具有軟體保證的 SQL Server 授權。 
1. 選取 **[** **設定**] 頁面底部的 [套用]。 

![在入口網站中 Azure Hybrid Benefit](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

您可以使用 Azure CLI 來變更您的授權模型。  


**Azure 混合式權益**

```azurecli-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

**隨用隨付**： 

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type PAYG

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type PAYG
```

**嚴重損壞修復（DR）**

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type DR

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type DR
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

您可以使用 PowerShell 來變更您的授權模型。

**Azure Hybrid Benefit**

```powershell-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType AHUB
```

**隨用隨付**

```powershell-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType PAYG
```

**嚴重損壞修復** 

```powershell-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType DR
```

---

## <a name="vms-not-registered-with-the-resource-provider"></a>未向資源提供者註冊的 Vm

如果您從隨用隨付 Azure Marketplace 映射布建 SQL Server VM，則 SQL Server 授權類型將會是隨用隨付。 如果您使用來自 Azure Marketplace 的自備授權映射來布建 SQL Server VM，授權類型將會是 AHUB。 從預設布建的所有 SQL Server Vm （隨用隨付）或自備授權 Azure Marketplace 映射都會自動向 SQL VM 資源提供者註冊，因此他們可以變更[授權類型](#vms-already-registered-with-the-resource-provider)。

您僅有資格透過 Azure Hybrid Benefit 在 Azure VM 上自行安裝 SQL Server。 您應將 SQL Server 授權設定為 Azure Hybrid Benefit，以向[SQL VM 資源提供者註冊這些 vm](virtual-machines-windows-sql-register-with-resource-provider.md) ，以根據 Microsoft 產品條款指出 Azure Hybrid Benefit 的使用方式。

只有在 SQL Server VM 已向 SQL VM 資源提供者註冊時，您才可以將 SQL Server VM 的授權類型變更為隨用隨付或 Azure Hybrid Benefit。

## <a name="remarks"></a>備註

- Azure 雲端解決方案提供者（CSP）客戶可以使用 Azure Hybrid Benefit，方法是先部署隨用隨付 VM，然後將它轉換成自備授權（如果它們具有有效的軟體保證）。
- 如果您卸載 SQL Server 的 VM 資源，將會回到映射的硬式編碼授權設定。 
- 變更授權模型的能力是 SQL VM 資源提供者的一項功能。 透過 Azure 入口網站部署 Azure Marketplace 映射，會自動向資源提供者註冊 SQL Server VM。 但正在自行安裝 SQL Server 的客戶必須手動[註冊其 SQL SERVER VM](virtual-machines-windows-sql-register-with-resource-provider.md)。 
- 將 SQL Server VM 新增至可用性設定組需要重新建立 VM。 因此，任何新增至可用性設定組的 Vm 都會回到預設的隨用隨付授權類型。 必須再次啟用 Azure Hybrid Benefit。 


## <a name="limitations"></a>限制

變更授權模型為：
   - 僅適用于具有[軟體保證](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-overview)的客戶。
   - 僅支援 Standard 和 Enterprise 版本的 SQL Server。 不支援 Express、Web 和 Developer 的授權變更。 
   - 僅支援透過 Azure Resource Manager 模型部署的虛擬機器。 不支援透過傳統模型部署的虛擬機器。 
   - 僅適用于公用或 Azure Government 雲端。 
   - 只有在具有單一網路介面（NIC）的虛擬機器上才支援。 


## <a name="known-errors"></a>已知錯誤

### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found"></a>找不到資源群組「\<\<資源群組>」下的資源 ' microsoft.sqlvirtualmachine/SqlVirtualMachines/資源群組> '。

當您嘗試在尚未向 SQL VM 資源提供者註冊的 SQL Server VM 上變更授權模型時，就會發生此錯誤：

`The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>' under resource group '\<resource-group>' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set.`

您必須向資源提供者註冊您的訂用帳戶，然後向[資源提供者註冊您的 SQL SERVER VM](virtual-machines-windows-sql-register-with-resource-provider.md)。 


### <a name="the-virtual-machine-vmname-has-more-than-one-nic-associated"></a>虛擬機器 '\<vmname\>' 有一或多個相關聯的 NIC

此錯誤發生在具有一個以上 NIC 的虛擬機器上。 在您變更授權模型之前，請先移除其中一個 Nic。 雖然您可以在變更授權模型之後，將 NIC 新增回 VM，但將不再支援 Azure 入口網站中的作業，例如自動備份和修補。 


## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列文章： 

* [Windows VM 上的 SQL Server 總覽](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM 上的 SQL Server 常見問題](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM 上 SQL Server 的定價指導方針](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM 上 SQL Server 的版本資訊](virtual-machines-windows-sql-server-iaas-release-notes.md)


