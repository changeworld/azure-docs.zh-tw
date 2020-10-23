---
title: Azure Arc 啟用的伺服器的 VM 延伸模組管理
description: Azure Arc 啟用的伺服器可以管理虛擬機器擴充功能的部署，以使用非 Azure Vm 提供部署後設定和自動化工作。
ms.date: 10/19/2020
ms.topic: conceptual
ms.openlocfilehash: e9865761fd3e5897ee3f01cd3d6ca620d5ea2f4b
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92460881"
---
# <a name="virtual-machine-extension-management-with-azure-arc-enabled-servers"></a>已啟用 Azure Arc 的伺服器的虛擬機器擴充功能管理

虛擬機器 (VM) 擴充功能是小型的應用程式，可在 Azure Vm 上提供部署後設定和自動化工作。 例如，如果虛擬機器需要軟體安裝、防毒保護，或是要在其中執行指令碼，您可以使用 VM 延伸模組。

Azure Arc 啟用的伺服器可讓您將 Azure VM 擴充功能部署至非 Azure Windows 和 Linux Vm，並透過其生命週期簡化混合式機器在內部部署、邊緣和其他雲端環境的管理。 您可以在混合式電腦上使用下列方法來管理 VM 擴充功能，或在已啟用 Arc 的伺服器上管理伺服器：

- [Azure 入口網站](manage-vm-extensions-portal.md)
- [Azure CLI](manage-vm-extensions-cli.md)
- [Azure PowerShell](manage-vm-extensions-powershell.md)
- Azure [Resource Manager 範本](manage-vm-extensions-template.md)

## <a name="key-benefits"></a>主要權益

Azure Arc 啟用的伺服器 VM 延伸模組支援提供下列主要優點：

- 使用 [Azure 自動化狀態設定](../../automation/automation-dsc-overview.md) 來集中儲存設定，並維護透過 DSC VM 延伸模組啟用的混合式連線機器所需狀態。

- 使用 Log Analytics 代理程式 VM 擴充功能 [Azure 監視器](../../azure-monitor/platform/data-platform-logs.md) 啟用記錄檔收集記錄資料以進行分析。 這適用于跨各種來源的資料執行複雜的分析。

- 使用 [適用於 VM 的 Azure 監視器](../../azure-monitor/insights/vminsights-overview.md)時，會分析 Windows 和 Linux vm 的效能，並監視其進程和其他資源和外部進程的相依性。 這可透過啟用 Log Analytics 代理程式和 Dependency agent VM 延伸模組來達成。

- 使用自訂腳本擴充功能，在混合式連線的電腦上下載並執行腳本。 此擴充功能適用於部署後設定、軟體安裝或其他任何設定/管理工作。

## <a name="availability"></a>可用性

VM 擴充功能只能在 [支援的區域](overview.md#supported-regions)清單中使用。 確定您已在其中一個區域內架您的電腦。

## <a name="extensions"></a>延伸模組

在此版本中，我們支援 Windows 和 Linux 機器上的下列 VM 擴充功能。

|延伸模組 |OS |Publisher |其他資訊 |
|----------|---|----------|-----------------------|
|CustomScriptExtension |Windows |Microsoft.Compute |[Windows 自訂腳本擴充功能](../../virtual-machines/extensions/custom-script-windows.md)|
|DSC |Windows |Microsoft PowerShell|[Windows PowerShell DSC 延伸模組](../../virtual-machines/extensions/dsc-windows.md)|
|Log Analytics 代理程式 |Windows |Microsoft.EnterpriseCloud.Monitoring |[適用于 Windows 的 Log Analytics VM 擴充功能](../../virtual-machines/extensions/oms-windows.md)|
|Microsoft Dependency Agent | Windows |Microsoft.Compute | [適用于 Windows 的相依性代理程式虛擬機器擴充功能](../../virtual-machines/extensions/agent-dependency-windows.md)|
|CustomScript|Linux |Microsoft Azure 延伸模組 |[Linux 自訂腳本擴充功能第2版](../../virtual-machines/extensions/custom-script-linux.md) |
|DSC |Linux |Microsoft.OSTCExtensions |[適用于 Linux 的 PowerShell DSC 擴充功能](../../virtual-machines/extensions/dsc-linux.md) |
|Log Analytics 代理程式 |Linux |Microsoft.EnterpriseCloud.Monitoring |[適用于 Linux 的 Log Analytics VM 擴充功能](../../virtual-machines/extensions/oms-linux.md) |
|Microsoft Dependency Agent | Linux |Microsoft.Compute | [適用于 Linux 的相依性代理程式虛擬機器擴充功能](../../virtual-machines/extensions/agent-dependency-linux.md) |

若要瞭解 Azure Connected Machine 代理程式封裝和延伸模組代理程式元件的詳細資料，請參閱 [代理程式總覽](agent-overview.md#agent-component-details)。

## <a name="prerequisites"></a>先決條件

這項功能取決於您訂用帳戶中的下列 Azure 資源提供者：

- **Microsoft.HybridCompute**
- **Microsoft.GuestConfiguration**

如果尚未註冊，請遵循 [註冊 Azure 資源提供者](agent-overview.md#register-azure-resource-providers)中的步驟。

適用于 Linux 的 Log Analytics 代理程式 VM 擴充功能需要在目的電腦上安裝 Python 2.x。

### <a name="connected-machine-agent"></a>Connected Machine 代理程式

確認您的電腦符合 Azure Connected Machine 代理程式支援的 Windows 和 Linux 作業系統 [版本](agent-overview.md#supported-operating-systems) 。

Windows 和 Linux 上這項功能支援的已連線電腦代理程式的最小版本是1.0 版。

若要將您的電腦升級為所需的代理程式版本，請參閱 [升級代理程式](manage-agent.md#upgrading-agent)。

## <a name="next-steps"></a>後續步驟

您可以使用 [Azure CLI](manage-vm-extensions-cli.md)、 [PowerShell](manage-vm-extensions-powershell.md)、 [Azure 入口網站](manage-vm-extensions-portal.md)或 [Azure Resource Manager 範本](manage-vm-extensions-template.md)，來部署、管理和移除 VM 擴充功能。
