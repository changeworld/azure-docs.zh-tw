---
title: 什麼是 SQL Server IaaS 代理程式擴充功能？
description: 本文說明 SQL Server IaaS 代理程式擴充功能如何協助自動化管理 Azure Vm 上 SQL Server 的特定系統管理工作。 其中包括自動備份、自動修補、Azure Key Vault 整合、授權管理、儲存體設定，以及所有 SQL Server VM 實例的集中管理等功能。
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/30/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 070058eae36bf4f8546cfcf4beb85ac5023e9c79
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2020
ms.locfileid: "93286167"
---
# <a name="what-is-the-sql-server-iaas-agent-extension"></a>什麼是 SQL Server IaaS 代理程式擴充功能？
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


SQL Server IaaS 代理程式延伸模組 (SqlIaasExtension) 會在 Azure 虛擬機器上的 SQL Server 上執行， (Vm) 以自動化管理和管理工作。 

本文提供延伸模組的總覽。 若要在 Azure Vm 上安裝 SQL Server IaaS 擴充功能以 SQL Server，請參閱大量的 [自動安裝](sql-vm-resource-provider-automatic-registration.md)、 [單一 vm](sql-vm-resource-provider-register.md)或 [vm](sql-vm-resource-provider-bulk-register.md)的相關文章。 

## <a name="overview"></a>總覽

SQL Server IaaS 代理程式擴充功能可為 Azure Vm 上的 SQL Server 提供許多優點： 

- **功能優點** ：此延伸模組會解除許多自動化功能的優點，例如入口網站管理、授權彈性、自動備份、自動修補等等。 如需詳細資訊，請參閱本文稍後的 [功能優勢](#feature-benefits) 。 

- **合規性** ：此延伸模組提供了一種簡化的方法，讓您能夠以產品條款中指定的方式啟用 Azure Hybrid Benefit 來通知 Microsoft。 此流程不需要為每項資源管理授權註冊表單。  

- **Free** ：所有三種管理性模式中的擴充功能完全免費。 與資源提供者或變更管理模式都不會產生任何額外費用。 

- **簡化的授權管理** ：此延伸模組可簡化 SQL Server 授權管理，並可讓您快速識別 Azure Hybrid Benefit 使用 [Azure 入口網站](manage-sql-vm-portal.md)、Azure CLI 或 PowerShell 啟用的 SQL Server vm： 

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


> [!IMPORTANT]
> SQL IaaS 代理程式擴充功能會收集資料，以在 Azure 虛擬機器中使用 SQL Server 時，為客戶提供選擇性的權益。 Microsoft 不會在未經客戶同意的情況下，使用此資料進行授權審核。 如需詳細資訊，請參閱 [SQL Server 隱私權補充](/sql/sql-server/sql-server-privacy#non-personal-data) 資訊。


## <a name="feature-benefits"></a>功能優點 

SQL Server IaaS 代理程式擴充功能會解除管理 SQL Server VM 的一些功能優點。 

下表詳細說明這些優點： 


| 功能 | 描述 |
| --- | --- |
| **連接埠管理** | 在 [入口網站](manage-sql-vm-portal.md)中解除鎖定管理，讓您可以在同一處查看所有 SQL Server vm，以便您可以直接從入口網站啟用和停用 SQL 特定功能。 
| **自動備份** |為預設執行個體或在 VM 上[正確安裝](frequently-asked-questions-faq.md#administration)的 SQL Server 具名執行個體，自動化所有資料庫的備份排程。 如需詳細資訊，請參閱 [Azure 虛擬機器中的 SQL Server 自動備份 (Resource Manager)](automated-backup-sql-2014.md)。 |
| **自動修補** |設定維護期間，在這段期間，您的 VM 有重要的 Windows 和 SQL Server 安全性更新可以進行，因此您可以在工作負載的尖峰時間內避免更新。 如需詳細資訊，請參閱 [Azure 虛擬機器中的 SQL Server 自動修補 (Resource Manager)](automated-patching.md)。 |
| **Azure Key Vault 整合** |讓您在 SQL Server VM 上自動安裝和設定 Azure 金鑰保存庫。 如需詳細資訊，請參閱[在 Azure 虛擬機器上設定 SQL Server 的 Azure Key Vault 整合 (Resource Manager)](azure-key-vault-integration-configure.md)。 |
| **彈性授權** | 從自備授權 (（也稱為「隨用隨付」授權模型的 Azure Hybrid Benefit) ） [順暢地轉換](licensing-model-azure-hybrid-benefit-ahb-change.md) ，以節省成本。 | 
| **彈性版本/版本** | 如果您決定要變更[SQL Server 的](change-sql-server-edition.md)[版本](change-sql-server-version.md)，您可以更新 Azure 入口網站內的中繼資料，而不需要重新部署整個 SQL Server VM。  | 


## <a name="management-modes"></a>管理模式

SQL IaaS 擴充功能有三個管理模式：

- **輕量** 模式不需要重新啟動 SQL Server，而只支援變更 SQL Server 的授權類型和版本。 針對具有多個執行個體的 SQL Server VM，或參與容錯移轉叢集執行個體 (FCI) 的 SQL Server VM，請使用此選項。 此管理模式會將 SQL IaaS 代理程式擴充二進位檔保留在電腦上，但不會安裝代理程式。 當使用 [自動註冊](sql-vm-resource-provider-automatic-registration.md) 功能，或在手動註冊期間未指定管理類型時，輕量模式是預設的管理模式。 使用輕量模式時，不會對記憶體或 CPU 造成任何影響，也不會產生相關聯的成本。 建議您先在輕量模式中註冊 SQL Server VM，然後在排程的維護期間升級至完整模式。

- **完整** 模式提供所有功能，但需要重新啟動 SQL Server 和系統管理員權限。 使用它來管理具有單一執行個體的 SQL Server VM。 完整模式會安裝兩項 Windows 服務，對記憶體和 CPU 的影響最小而且可透過工作管理員來監視。 使用完整的管理模式並不會產生任何相關費用。 

- **NoAgent** 模式專用於安裝在 Windows Server 2008 上的 SQL Server 2008 和 SQL Server 2008 R2。 使用 NoAgent 模式時，不會對記憶體或 CPU 造成任何影響。 使用 NoAgent 管理性模式不會有相關聯的成本。 

您可以使用 Azure PowerShell 來查看 SQL Server IaaS 代理程式的目前模式： 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

## <a name="installation"></a>安裝

當您向 SQL VM 資源提供者註冊 SQL Server Vm 時，會安裝 SQL Server IaaS 代理程式擴充功能。 向資源提供者註冊會在您的訂用帳戶內建立 **SQL 虛擬機器**_資源_ ，這是與虛擬機器資源 _不同_ 的資源。 向資源提供者取消註冊 SQL Server VM 會移除 **SQL 虛擬機器** 的 _資源_ ，但不會卸載實際的虛擬機器。

透過 Azure 入口網站部署 SQL Server VM Azure Marketplace 映像，會自動向資源提供者註冊 SQL Server VM。 不過，如果您選擇在 Azure 虛擬機器上自行安裝 SQL Server，或從自訂 VHD 布建 Azure 虛擬機器，則必須向 SQL VM 資源提供者註冊您的 SQL Server VM，以安裝 SQL IaaS 代理程式擴充功能。 

若要安裝擴充功能，請向資源提供者註冊 SQL Server VM：
- [針對訂用帳戶中所有目前和未來的 Vm 自動進行](sql-vm-resource-provider-automatic-registration.md)
- [針對單一 VM](sql-vm-resource-provider-register.md)
- [多個 Vm 大量](sql-vm-resource-provider-bulk-register.md)

### <a name="named-instance-support"></a>命名實例支援

如果是虛擬機器上唯一可用的 SQL Server 實例，SQL Server IaaS 擴充功能將會與已命名的實例搭配使用 SQL Server。 擴充功能將無法安裝在具有多個 SQL Server 實例的 Vm 上。 

若要使用 SQL Server 的已命名實例，請部署 Azure 虛擬機器、在其中安裝一個名為 SQL Server 的實例，然後向 [SQL VM 資源提供者](sql-vm-resource-provider-register.md) 註冊以安裝延伸模組。

或者，若要使用具有 Azure Marketplace SQL Server 映射的已命名實例，請遵循下列步驟： 

   1. 從 Azure Marketplace 部署 SQL Server VM。 
   1. 從 SQL VM 資源提供者[取消註冊](sql-vm-resource-provider-register.md#unregister-from-rp)SQL Server VM。 
   1. 完全解除安裝 SQL Server VM 內的 SQL Server。
   1. 使用 SQL Server VM 中的具名執行個體來安裝 SQL Server。 
   1. [向 SQL vm 資源提供者註冊 SQL SERVER VM，以](sql-vm-resource-provider-register.md#register-with-rp)安裝 Sql IaaS 代理程式擴充功能。 

## <a name="verify-status-of-extension"></a>確認延伸模組的狀態

使用 Azure 入口網站或 Azure PowerShell 檢查延伸模組的狀態。 


### <a name="azure-portal"></a>Azure 入口網站

確認延伸模組已安裝在 Azure 入口網站中。 

選取 [虛擬機器] 窗格中的 [ **所有設定** ]，然後選取 [ **擴充** 功能]。 您應該會看到列出 **SqlIaasExtension** 擴充功能。

![Azure 入口網站中 SQL Server IaaS 代理程式擴充功能的狀態](./media/sql-server-iaas-agent-extension-automate-management/azure-rm-sql-server-iaas-agent-portal.png)


### <a name="azure-powershell"></a>Azure PowerShell

您也可以使用 **Get-AzVMSqlServerExtension** Azure PowerShell Cmdlet：

   ```powershell-interactive
   Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
   ```

前一個命令確認已安裝代理程式，並提供一般的狀態資訊。 使用下列命令可取得自動備份和修補的特定狀態資訊：

   ```powershell-interactive
    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings
   ```


## <a name="limitations"></a>限制

SQL IaaS 代理程式擴充功能只支援： 

- 透過 Azure Resource Manager 部署的 SQL Server VM。 不支援透過傳統模型部署的 SQL Server VM。 
- 部署至公用或 Azure Government 雲端的 SQL Server VM。 不支援部署到其他私人或政府雲端。 


## <a name="frequently-asked-questions"></a>常見問題集 

**我應該註冊從 Azure Marketplace 中 SQL Server 映像佈建的 SQL Server VM 嗎？**

否。 Microsoft 會自動註冊從 Azure Marketplace 中 SQL Server 映像佈建的 VM。 只有當 VM *不是* 從 Azure Marketplace 中的 SQL Server 映像佈建，且已自行安裝 SQL Server 時，才需要向 SQL VM 資源提供者註冊。

**SQL VM 資源提供者是否適用於所有客戶？** 

是。 如果客戶未使用 Azure Marketplace 中的 SQL Server 映像，而改為自行安裝 SQL Server，或如果客戶攜帶自己的自訂 VHD，則應該向 SQL VM 資源提供者註冊其 SQL Server VM。 所有訂用帳戶類型 (直接、Enterprise 合約和雲端解決方案提供者) 所擁有的 VM 都可向 SQL VM 資源提供者註冊。

**向 SQL VM 資源提供者註冊時的預設管理模式為何？**

當您向 SQL VM 資源提供者註冊時，預設的管理模式很 *輕量* 。 如果您向 SQL VM 資源提供者註冊時未設定 SQL Server 管理屬性，則會將模式設定為輕量，而您的 SQL Server 服務將不會重新開機。 建議您先在輕量模式中向 SQL VM 資源提供者註冊，然後在維護期間升級至完整模式。 同樣地，使用 [自動註冊功能](sql-vm-resource-provider-automatic-registration.md)時，預設管理也很輕量。

**向 SQL VM 資源提供者註冊的先決條件為何？**

除了在 VM 上安裝 SQL Server 之外，不需要向 SQL VM 資源提供者註冊任何必要條件。 請注意，如果 SQL IaaS 代理程式延伸模組是以完整模式安裝，SQL Server 服務將會重新開機，因此建議在維護期間執行此動作。

**向 SQL VM 資源提供者註冊會在 VM 上安裝代理程式嗎？**

是，以完整管理模式向 SQL VM 資源提供者註冊，會將代理程式安裝到 VM。 以輕量或 NoAgent 模式註冊並不會。 

以輕量模式向 SQL VM 資源提供者註冊時，只會將 SQL IaaS 代理程式擴充 *二進位* 檔複製到 VM，而不會安裝代理程式。 這些二進位檔接著會在管理模式升級為 full 時，用來安裝代理程式。


**將會向我的 VM 上的 SQL VM 資源提供者重新開機 SQL Server 註冊嗎？**

這取決於註冊期間所指定的模式。 如果指定了輕巧或 NoAgent 模式，SQL Server 服務將不會重新開機。 但是，將管理模式指定為 full 會導致 SQL Server 服務重新開機。 自動註冊功能會以輕量模式註冊 SQL Server Vm，除非 Windows Server 版本是2008，在此情況下，會在 NoAgent 模式中註冊 SQL Server VM。 

**向 SQL VM 資源提供者註冊時，輕量和 NoAgent 管理模式有何差異？** 

NoAgent 管理模式是 Windows Server 2008 上 SQL Server 2008 和 SQL Server 2008 R2 唯一可用的管理模式。 針對所有更新版的 Windows Server，這兩個可用的管理性模式是輕量且完整。 

NoAgent 模式需要客戶設定 SQL Server 版本和版本屬性。 輕量模式會查詢 VM 以尋找 SQL Server 執行個體的版本和版次。

**我可在未指定 SQL Server 授權類型的情況下，向 SQL VM 資源提供者註冊嗎？**

否。 當向 SQL VM 資源提供者註冊時，SQL Server 授權類型不是選擇性的屬性。 您必須以隨用隨付或 Azure Hybrid Benefit 在所有管理性模式下註冊 SQL VM 資源提供者時，將 SQL Server 授權類型設定為隨用隨付或 (NoAgent、羽量級和 full) 。 如果您已安裝任何免費版本的 SQL Server，例如 Developer 或評估版，您必須向隨用隨付授權註冊。 Azure Hybrid Benefit 僅適用于 SQL Server 的付費版本，例如 Enterprise 和 Standard edition。

**我可以將 SQL Server IaaS 延伸模組從 NoAgent 模式升級為完整模式嗎？**

否。 NoAgent 模式無法使用 [完整] 或 [輕量] 將管理性模式升級 這是 Windows Server 2008 的技術限制。 您必須先將 OS 升級至 Windows Server 2008 R2 或更新版本，然後才能升級為完整管理模式。 

**我可將 SQL Server IaaS 延伸模組從輕量模式升級為完整模式嗎？**

是。 透過 Azure PowerShell 或 Azure 入口網站，可支援從輕型升級為「完整」的管理模式。 這會觸發 SQL Server 服務的重新開機。

**我可以將 SQL Server IaaS 延伸模組從完整模式降級至 NoAgent 或輕量管理模式嗎？**

否。 不支援降級 SQL Server IaaS 延伸模組管理模式。 管理性模式無法從完整模式降級為輕量或 NoAgent 模式，而且無法從輕量模式降級為 NoAgent 模式。 

若要從完整管理性變更管理性模式，請卸載 SQL 虛擬機器 _資源_ ，然後以不同的管理模式再次向 sql vm 資源提供者重新註冊 SQL Server vm，以從 sql vm 資源提供者 [取消](sql-vm-resource-provider-register.md#unregister-from-rp)註冊 SQL Server VM。

**我可從 Azure 入口網站向 SQL VM 資源提供者註冊嗎？**

否。 您無法在 Azure 入口網站中向 SQL VM 資源提供者註冊。 只有 Azure CLI 或 Azure PowerShell 才支援向 SQL VM 資源提供者註冊。 

**我可在安裝 SQL Server 之前向 SQL VM 資源提供者註冊 VM 嗎？**

否。 VM 至少必須有一個 SQL Server (資料庫引擎) 實例，才能成功向 SQL VM 資源提供者註冊。 如果 VM 上沒有 SQL Server 執行個體，則新的 Microsoft.SqlVirtualMachine 資源會處於失敗狀態。

**如果有多個 SQL Server 執行個體，我可向 SQL VM 資源提供者註冊 VM 嗎？**

是。 SQL VM 資源提供者只會註冊一個 SQL Server (資料庫引擎) 執行個體。 如果有多個執行個體，則 SQL VM 資源提供者會註冊預設的 SQL Server 執行個體。 如果沒有預設的執行個體，則僅支援在輕量模式中註冊。 若要從輕量升級為完整管理模式，則預設的 SQL Server 執行個體應該存在，或 VM 應該只有一個具名 SQL Server 執行個體。

**我可向 SQL VM 資源提供者註冊 SQL Server 容錯移轉叢集執行個體嗎？**

是。 在輕量模式中，可以向 SQL VM 資源提供者註冊 Azure VM 上的 SQL Server 容錯移轉叢集執行個體。 不過，您無法將 SQL Server 容錯移轉叢集執行個體升級為完整管理模式。

**如果已設定 Always On 可用性群組，可以向 SQL VM 資源提供者註冊我的 VM 嗎？**

是。 如果您正在參與 Always On 可用性群組設定，則向 SQL VM 資源提供者註冊 Azure VM 上的 SQL Server 執行個體沒有任何限制。

**向 SQL VM 資源提供者註冊或升級為完整管理模式的費用為何？**

無。 向 SQL VM 資源提供者註冊，或使用三種管理模式中的任何一種，都不會產生任何相關聯的費用。 使用資源提供者管理您的 SQL Server VM 完全免費。 

**使用不同的管理模式會對效能造成什麼影響？**

使用 *NoAgent* 和 *輕量* 管理模式時，不會造成任何影響。 使用 *完整* 管理模式時，對安裝到 OS 的兩個服務影響最小。 這些服務可透過工作管理員監視，並顯示在內建 Windows 服務主控台中。 

這兩個服務名稱為：
- `SqlIaaSExtensionQuery` (顯示名稱 - `Microsoft SQL Server IaaS Query Service`)
- `SQLIaaSExtension` (顯示名稱 - `Microsoft SQL Server IaaS Agent`)

**如何? 移除擴充功能？**

從 SQL VM 資源提供者取消 [註冊](sql-vm-resource-provider-register.md#unregister-from-rp) SQL Server VM，以移除擴充功能。 

## <a name="next-steps"></a>後續步驟

若要在 Azure Vm 上安裝 SQL Server IaaS 擴充功能以 SQL Server，請參閱大量的 [自動安裝](sql-vm-resource-provider-automatic-registration.md)、 [單一 vm](sql-vm-resource-provider-register.md)或 [vm](sql-vm-resource-provider-bulk-register.md)的相關文章。

如需在 Azure 虛擬機器上執行 SQL Server 的詳細資訊，請參閱 [Azure 虛擬機器上的 SQL Server 是什麼](sql-server-on-azure-vm-iaas-what-is-overview.md)。
