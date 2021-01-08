---
title: Azure Arc 啟用的伺服器的 VM 延伸模組管理
description: Azure Arc 啟用的伺服器可以管理虛擬機器擴充功能的部署，以使用非 Azure Vm 提供部署後設定和自動化工作。
ms.date: 01/07/2021
ms.topic: conceptual
ms.openlocfilehash: 5430b1c1318747cccfb95f031700fddaad716284
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98020616"
---
# <a name="virtual-machine-extension-management-with-azure-arc-enabled-servers"></a>已啟用 Azure Arc 的伺服器的虛擬機器擴充功能管理 (機器翻譯)

虛擬機器 (VM) 擴充功能是小型的應用程式，可在 Azure Vm 上提供部署後設定和自動化工作。 例如，如果虛擬機器需要軟體安裝、防毒軟體或在其中執行腳本，則可以使用 VM 擴充功能。

Azure Arc 啟用的伺服器可讓您將 Azure VM 擴充功能部署至非 Azure Windows 和 Linux Vm，並透過其生命週期簡化混合式機器的管理。 您可以在混合式電腦上使用下列方法來管理 VM 擴充功能，或在已啟用 Arc 的伺服器上管理伺服器：

- [Azure 入口網站](manage-vm-extensions-portal.md)
- [Azure CLI](manage-vm-extensions-cli.md)
- [Azure PowerShell](manage-vm-extensions-powershell.md)
- Azure [Resource Manager 範本](manage-vm-extensions-template.md)

## <a name="key-benefits"></a>主要權益

Azure Arc 啟用的伺服器 VM 延伸模組支援提供下列主要優點：

- 使用 [Azure 自動化狀態設定](../../automation/automation-dsc-overview.md) 來集中儲存設定，並維護透過 DSC VM 延伸模組啟用的混合式連線機器所需狀態。

- 使用 Log Analytics 代理程式 VM 擴充功能 [Azure 監視器](../../azure-monitor/platform/data-platform-logs.md) 啟用記錄檔收集記錄資料以進行分析。 這對於跨不同類型來源的資料執行複雜的分析很有用。

- 使用 [適用於 VM 的 Azure 監視器](../../azure-monitor/insights/vminsights-overview.md)時，會分析 Windows 和 Linux vm 的效能，並監視其進程和其他資源和外部進程的相依性。 這可透過啟用 Log Analytics 代理程式和 Dependency agent VM 延伸模組來達成。

- 使用自訂腳本擴充功能，在混合式連線的電腦上下載並執行腳本。 此擴充功能適用於部署後設定、軟體安裝或其他任何設定/管理工作。

- 自動重新整理儲存在 [Azure Key Vault](../../key-vault/general/overview.md)中的憑證。

## <a name="availability"></a>可用性

VM 擴充功能只能在 [支援的區域](overview.md#supported-regions)清單中使用。 確定您已在其中一個區域內架您的電腦。

## <a name="extensions"></a>延伸模組

在此版本中，我們支援 Windows 和 Linux 機器上的下列 VM 擴充功能。

若要瞭解 Azure Connected Machine 代理程式封裝和延伸模組代理程式元件的詳細資料，請參閱 [代理程式總覽](agent-overview.md#agent-component-details)。

### <a name="windows-extensions"></a>Windows 擴充功能

|延伸模組 |Publisher |類型 |其他資訊 |
|----------|----------|-----|-----------------------|
|Azure Defender 整合式弱點掃描器 |Qualys |WindowsAgent.AzureSecurityCenter |[適用于 Azure 和混合式機器的 azure Defender 整合式弱點評定解決方案](../../security-center/deploy-vulnerability-assessment-vm.md)|
|自訂指令碼擴充功能 |Microsoft.Compute | CustomScriptExtension |[Windows 自訂腳本擴充功能](../../virtual-machines/extensions/custom-script-windows.md)|
|PowerShell DSC |Microsoft PowerShell |DSC |[Windows PowerShell DSC 延伸模組](../../virtual-machines/extensions/dsc-windows.md)|
|Log Analytics 代理程式 |Microsoft.EnterpriseCloud.Monitoring |MicrosoftMonitoringAgent |[適用于 Windows 的 Log Analytics VM 擴充功能](../../virtual-machines/extensions/oms-windows.md)|
|適用於 VM 的 Azure 監視器 (深入解析)  |Microsoft.Azure.Monitoring.DependencyAgent |DependencyAgentWindows | [適用于 Windows 的相依性代理程式虛擬機器擴充功能](../../virtual-machines/extensions/agent-dependency-windows.md)|
|Azure Key Vault 憑證同步處理 | Azure. 金鑰保存庫 |KeyVaultForWindows | [適用於 Windows 的金鑰保存庫虛擬機器擴充功能](../../virtual-machines/extensions/key-vault-windows.md) |
|Azure 監視器代理程式 |Microsoft Azure 監視器 |AzureMonitorWindowsAgent |[安裝 Azure 監視器代理程式 (預覽版) ](../../azure-monitor/platform/azure-monitor-agent-install.md) |

### <a name="linux-extensions"></a>Linux 擴充功能

|延伸模組 |Publisher |類型 |其他資訊 |
|----------|----------|-----|-----------------------|
|Azure Defender 整合式弱點掃描器 |Qualys |LinuxAgent.AzureSecurityCenter |[適用于 Azure 和混合式機器的 azure Defender 整合式弱點評定解決方案](../../security-center/deploy-vulnerability-assessment-vm.md)|
|自訂指令碼擴充功能 |Microsoft Azure 延伸模組 |CustomScript |[Linux 自訂腳本擴充功能第2版](../../virtual-machines/extensions/custom-script-linux.md) |
|PowerShell DSC |Microsoft.OSTCExtensions |DSCForLinux |[適用于 Linux 的 PowerShell DSC 擴充功能](../../virtual-machines/extensions/dsc-linux.md) |
|Log Analytics 代理程式 |Microsoft.EnterpriseCloud.Monitoring |OmsAgentForLinux |[適用于 Linux 的 Log Analytics VM 擴充功能](../../virtual-machines/extensions/oms-linux.md) |
|適用於 VM 的 Azure 監視器 (深入解析)  |Microsoft.Azure.Monitoring.DependencyAgent |DependencyAgentLinux |[適用于 Linux 的相依性代理程式虛擬機器擴充功能](../../virtual-machines/extensions/agent-dependency-linux.md) |
|Azure Key Vault 憑證同步處理 | Azure. 金鑰保存庫 |KeyVaultForLinux | [適用於 Linux 的金鑰保存庫虛擬機器擴充功能](../../virtual-machines/extensions/key-vault-linux.md) |
|Azure 監視器代理程式 |Microsoft Azure 監視器 |AzureMonitorLinuxAgent |[安裝 Azure 監視器代理程式 (預覽版) ](../../azure-monitor/platform/azure-monitor-agent-install.md) |

## <a name="prerequisites"></a>必要條件

這項功能取決於您訂用帳戶中的下列 Azure 資源提供者：

- **Microsoft.HybridCompute**
- **Microsoft.GuestConfiguration**

如果尚未註冊，請遵循 [註冊 Azure 資源提供者](agent-overview.md#register-azure-resource-providers)中的步驟。

請務必參閱上表中所參考之每個 VM 擴充功能的檔，以瞭解是否有任何網路或系統需求。 這可協助您避免發生依賴該 VM 擴充功能的 Azure 服務或功能發生任何連線問題。

### <a name="log-analytics-vm-extension"></a>Log Analytics VM 延伸模組

適用于 Linux 的 Log Analytics 代理程式 VM 擴充功能需要在目的電腦上安裝 Python 2.x。 

### <a name="azure-key-vault-vm-extension-preview"></a>Azure Key Vault VM 延伸模組 (預覽) 

Key Vault VM 延伸模組 (preview) 不支援下列 Linux 作業系統：

- CentOS Linux 7 (x64)
- Red Hat Enterprise Linux (RHEL) 7 (x64)
- Amazon Linux 2 (x64)

僅支援使用下列各項來部署 Key Vault VM 延伸模組 (preview) ：

- Azure CLI
- Azure PowerShell
- Azure Resource Manager 範本

部署擴充功能之前，您必須先完成下列步驟：

1. [建立保存庫和憑證](../../key-vault/certificates/quick-create-portal.md) (自我簽署或匯入) 。

2. 授與 Azure Arc 啟用憑證秘密的伺服器存取權。 如果您使用 [RBAC 預覽版](../../key-vault/general/rbac-guide.md)，請搜尋 Azure Arc 資源的名稱，並將 **Key Vault 秘密使用者 (預覽)** 角色指派給它。 如果您使用 [Key Vault 存取原則](../../key-vault/general/assign-access-policy-portal.md)，請將密碼 **取得** 許可權指派給 Azure Arc 資源的系統指派身分識別。

### <a name="connected-machine-agent"></a>Connected Machine 代理程式

確認您的電腦符合 Azure Connected Machine 代理程式支援的 Windows 和 Linux 作業系統 [版本](agent-overview.md#supported-operating-systems) 。

Windows 和 Linux 上這項功能支援的已連線電腦代理程式的最小版本是1.0 版。

若要將您的電腦升級為所需的代理程式版本，請參閱 [升級代理程式](manage-agent.md#upgrading-agent)。

## <a name="next-steps"></a>後續步驟

您可以使用 [Azure CLI](manage-vm-extensions-cli.md)、 [Azure PowerShell](manage-vm-extensions-powershell.md)、 [Azure 入口網站](manage-vm-extensions-portal.md)或 [Azure Resource Manager 範本](manage-vm-extensions-template.md)，來部署、管理和移除 VM 擴充功能。
