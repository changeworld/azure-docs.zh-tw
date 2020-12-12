---
title: 變更 Azure 中 SQL VM 的授權模型
description: 了解如何使用 Azure Hybrid Benefit，在 Azure 中將 SQL Server VM 的授權從隨用隨付切換至自備授權。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.subservice: management
ms.workload: iaas-sql-server
ms.date: 11/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: da136c1f3ce36bf85592c3c73c9e8f92c41c7442
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/12/2020
ms.locfileid: "97357903"
---
# <a name="change-the-license-model-for-a-sql-virtual-machine-in-azure"></a>變更 Azure 中 SQL 虛擬機器的授權模型
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


本文說明如何使用 [SQL IaaS 代理程式擴充](./sql-server-iaas-agent-extension-automate-management.md)功能，在 Azure 中變更 SQL Server 虛擬機器 (VM) 的授權模型。

## <a name="overview"></a>概觀

有三種適用于裝載 SQL Server 的 Azure VM 授權模型：隨用隨付、Azure Hybrid Benefit (AHB) ，以及高可用性/嚴重損壞修復 (HA/DR) 。 您可使用 Azure 入口網站、Azure CLI 或 PowerShell 來修改 SQL Server VM 的授權模型。 

- **隨用隨付** 模型意謂著執行 Azure VM 的每秒鐘費用包含 SQL Server 授權的費用。
- [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) 可供將自有 SQL Server 授權搭配執行 SQL Server 的 VM 使用。 
- **Ha/dr** 授權類型可用於 Azure 中的 [免費 HA/dr 複本](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure)。 

Azure Hybrid Benefit 允許在 Azure 虛擬機器上使用具有軟體保證 (即「合格授權」) 的 SQL Server 授權。 透過使用 Azure Hybrid Benefit，客戶不需支付在 VM 上使用 SQL Server 授權的費用。 但客戶仍需支付基礎雲端計算 (即基本費率)、儲存體及備份的費用。 也必須支付與服務使用建立關聯的 I/O 費用 (如適用)。

根據 Microsoft 產品條款：「客戶在 Azure 上設定工作負載時，必須表明自己目前在使用 Azure SQL Database (受控執行個體、彈性集區和單一資料庫)、Azure Data Factory、SQL Server Integration Services 或適用於 SQL Server 的 Azure Hybrid Benefit 下的 SQL Server 虛擬機器。」

若要表明在 Azure VM 上使用適用於 SQL Server 的 Azure Hybrid Benefit 且符合規範，您有三個可用選項：

- 使用來自 Azure Marketplace 的自備授權 SQL Server 映像來佈建虛擬機器。 此選項僅適用於具有 Enterprise 合約的客戶。
- 使用 Azure Marketplace 的隨用隨付 SQL Server 映像來佈建虛擬機器，並啟用 Azure Hybrid Benefit。
- 在 Azure VM 上自行安裝 SQL Server， [以手動方式向 SQL IaaS 代理程式延伸模組註冊](sql-agent-extension-manually-register-single-vm.md)，然後啟用 Azure Hybrid Benefit。

可在佈建 VM 時設定 SQL Server 的授權類型，佈建之後也可隨時設定。 在授權模型之間切換時，不會產生停機、不會重新啟動 VM 或 SQL Server 服務、無須支付額外費用，而且立即生效。 事實上，啟用 Azure Hybrid Benefit 會「降低」成本。

## <a name="prerequisites"></a>必要條件

變更 SQL Server VM 授權模型有下列需求： 

- [Azure 訂用帳戶](https://azure.microsoft.com/free/)。
- 以[SQL IaaS 代理程式擴充](./sql-server-iaas-agent-extension-automate-management.md)功能註冊[SQL Server VM](./create-sql-vm-portal.md) 。
- 使用 [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) 所必要的[軟體保證](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default)。 


## <a name="change-license-model"></a>變更授權模型

# <a name="azure-portal"></a>[Azure 入口網站](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

您可直接從入口網站修改授權模型： 

1. 開啟 [Azure 入口網站](https://portal.azure.com) 並開啟 SQL Server VM 的[SQL 虛擬機器資源](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource)。 
1. 選取 [設定] 下方的 [設定]。 
1. 選取 [Azure Hybrid Benefit] 選項，然後勾選核取方塊以確認擁有軟體保證的 SQL Server 授權。 
1. 選取 [設定] 頁面底部的 [套用]。 

![入口網站中的 Azure Hybrid Benefit](./media/licensing-model-azure-hybrid-benefit-ahb-change/ahb-in-portal.png)


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

您可使用 Azure CLI 來變更授權模型。  

為 **授權類型** 指定下列值：
- `AHUB` 針對 Azure Hybrid Benefit
- `PAYG` 適用于隨用隨付
- `DR` 啟用免費 HA/DR 複本


```azurecli-interactive
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type <license-type>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

您可使用 PowerShell 變更授權模型。

為 **授權類型** 指定下列值：
- `AHUB` 針對 Azure Hybrid Benefit
- `PAYG` 適用于隨用隨付
- `DR` 啟用免費 HA/DR 複本


```powershell-interactive
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType <license-type>
```

---

## <a name="remarks"></a>備註

- Azure 雲端方案提供者 (CSP) 客戶可使用 Azure Hybrid Benefit，方法是先部署隨用隨付的 VM，然後將其轉換成自備授權 (前提是 VM 具備有效的軟體保證)。
- 如果您卸載 SQL 虛擬機器資源，您將會回到影像的硬式編碼授權設定。 
- 變更授權模型的功能是 SQL IaaS 代理程式擴充功能的功能。 透過 Azure 入口網站部署 Azure Marketplace 映射時，會自動以延伸模組註冊 SQL Server VM。 但自行安裝 SQL Server 的客戶將必須手動[註冊自己的 SQL Server VM](sql-agent-extension-manually-register-single-vm.md)。 
- 將 SQL Server VM 新增至可用性設定組，則需要重新建立 VM。 因此，任何新增至可用性設定組的 VM 都會回到預設隨用隨付授權類型。 您將需要再次啟用 Azure Hybrid Benefit。 


## <a name="limitations"></a>限制

變更授權模型的限制如下：
   - 僅適用於具有[軟體保證](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-overview)的客戶。
   - 僅支援 SQL Server 的 Standard 和 Enterprise 版本。 不支援變更 Express、Web 和 Developer 的授權。 
   - 僅支援透過 Azure Resource Manager 模型部署的虛擬機器。 不支援透過傳統模型部署的虛擬機器。 
   - 僅適用於公用或 Azure Government 雲端。 
   - 僅支援具備單一網路介面 (NIC) 的虛擬機器。 

> [!Note]
> 只有 SQL Server 以核心為基礎的軟體保證或訂用帳戶授權授權適用于 Azure Hybrid Benefit。 如果您使用 Server + CAL 授權進行 SQL Server，而且您有軟體保證，您可以使用 Azure SQL Server 虛擬機器映射的「自備授權」來利用這些伺服器的授權流動性，但是無法利用 Azure Hybrid Benefit 的其他功能。 

## <a name="known-errors"></a>已知錯誤

檢查常見的錯誤及其解決方法。 

**在資源群組 ' ' 下找不到資源 ' Microsoft.sqlvirtualmachine/SqlVirtualMachines/ \<resource-group> ' \<resource-group> 。**

當您嘗試在尚未向 SQL Server IaaS 代理程式擴充功能註冊的 SQL Server VM 上變更授權模型時，就會發生此錯誤：

`The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>' under resource group '\<resource-group>' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set.`

您必須向資源提供者註冊您的訂用帳戶，然後 [使用 SQL IaaS 代理程式擴充功能來註冊您的 SQL SERVER VM](sql-agent-extension-manually-register-single-vm.md)。 


**虛擬機器 ' \<vmname\> ' 有多個相關聯的 NIC**

此錯誤會在具備超過一個 NIC 的虛擬機器上發生。 在變更授權模型之前，請先移除其中一個 NIC。 雖然可在變更授權模型之後，將 NIC 新增回 VM，但系統不會再支援 Azure 入口網站中的作業，例如自動備份和修補。 


## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列文章： 

* [Windows VM 上的 SQL Server 概觀](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Windows VM 上的 SQL Server 常見問題集](frequently-asked-questions-faq.md)
* [Windows VM 上的 SQL Server 定價指引](pricing-guidance.md)
* [Windows VM 上的 SQL Server 版本資訊](../../database/doc-changes-updates-release-notes.md)
* [SQL IaaS 代理程式擴充功能總覽](./sql-server-iaas-agent-extension-automate-management.md)
