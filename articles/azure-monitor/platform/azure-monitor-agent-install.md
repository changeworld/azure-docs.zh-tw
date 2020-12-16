---
title: 安裝 Azure 監視器代理程式
description: 在 Azure 虛擬機器上安裝 Azure 監視器代理程式 (AMA) ，以及 Azure Arc 啟用的伺服器的選項。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/17/2020
ms.openlocfilehash: 893a04f5acd48cf1e6f34033c06a758492e70abf
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516717"
---
# <a name="install-the-azure-monitor-agent-preview"></a>安裝 Azure 監視器代理程式 (預覽版) 
本文提供目前可用來在 Azure 虛擬機器和已啟用 Azure Arc 的伺服器上安裝 [Azure 監視器代理程式](azure-monitor-agent-overview.md) 的不同選項，以及用來建立 [與資料集合規則關聯](data-collection-rule-azure-monitor-agent.md) 的選項，這些規則會定義代理程式應收集哪些資料。

## <a name="prerequisites"></a>必要條件
在安裝 Azure 監視器代理程式之前，需要下列必要條件。

- 必須在 Azure 虛擬機器上啟用[受控系統身分識別](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)。 Azure Arc 啟用的伺服器並不需要這麼做。 如果在 [使用 Azure 入口網站建立和指派資料收集規則](#install-with-azure-portal)的過程中安裝代理程式，系統將會自動啟用系統身分識別。
- 您必須在虛擬機器的虛擬網路上啟用 [AzureResourceManager 服務標記](../../virtual-network/service-tags-overview.md) 。

## <a name="virtual-machine-extension-details"></a>虛擬機器擴充功能詳細資料
Azure 監視器代理程式會實作為 [AZURE VM 擴充](../../virtual-machines/extensions/overview.md) 功能，並包含下表中的詳細資料。 您可以使用任何一種方法來安裝虛擬機器擴充功能，包括本文所述的虛擬機器擴充功能。

| 屬性 | Windows | Linux |
|:---|:---|:---|
| Publisher | Microsoft Azure 監視器  | Microsoft Azure 監視器 |
| 類型      | AzureMonitorWindowsAgent | AzureMonitorLinuxAgent  |
| TypeHandlerVersion  | 1.0 | 1.5 |


## <a name="install-with-azure-portal"></a>使用 Azure 入口網站進行安裝
若要使用 Azure 入口網站安裝 Azure 監視器代理程式，請遵循此程式，在 Azure 入口網站中 [建立資料集合規則](data-collection-rule-azure-monitor-agent.md#create-rule-and-association-in-azure-portal) 。 這可讓您將資料收集規則與一或多部 Azure 虛擬機器或 Azure Arc 啟用的伺服器產生關聯。 代理程式將會安裝在這些尚未具有該代理程式的任何虛擬機器上。


## <a name="install-with-resource-manager-template"></a>使用 Resource Manager 範本安裝
您可以使用 Resource Manager 範本，在 Azure 虛擬機器和 Azure Arc 啟用的伺服器上安裝 Azure 監視器代理程式，以及建立與資料收集規則的關聯。 建立關聯之前，您必須先建立任何資料集合規則。

取得用來安裝代理程式的範例範本，以及從下列程式建立關聯： 

- [用來將 Azure 監視器代理程式安裝 (Azure 和 Azure Arc) 的範本 ](../samples/resource-manager-agent.md#azure-monitor-agent-preview) 
- [用來建立與資料集合規則關聯的範本](../samples/resource-manager-data-collection-rules.md)

[針對 Resource Manager 範本使用任何部署方法](../../azure-resource-manager/templates/deploy-powershell.md)安裝範本，例如下列命令。

# <a name="powershell"></a>[PowerShell](#tab/ARMAgentPowerShell)
```powershell
New-AzResourceGroupDeployment -ResourceGroupName "<resource-group-name>" -TemplateFile "<template-filename.json>" -TemplateParameterFile "<parameter-filename.json>"
```
# <a name="cli"></a>[CLI](#tab/ARMAgentCLI)
```powershell
New-AzResourceGroupDeployment -ResourceGroupName "<resource-group-name>" -TemplateFile "<template-filename.json>" -TemplateParameterFile "<parameter-filename.json>"
```
---

## <a name="install-with-powershell"></a>使用 PowerShell 安裝
您可以使用 PowerShell 命令，將 Azure 監視器代理程式安裝在 Azure 虛擬機器和已啟用 Azure Arc 的伺服器上，以新增虛擬機器擴充功能。 

### <a name="azure-virtual-machines"></a>Azure 虛擬機器
使用下列 PowerShell 命令，在 Azure 虛擬機器上安裝 Azure 監視器代理程式。
# <a name="windows"></a>[Windows](#tab/PowerShellWindows)
```powershell
Set-AzVMExtension -Name AMAWindows -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location>
```
# <a name="linux"></a>[Linux](#tab/PowerShellLinux)
```powershell
Set-AzVMExtension -Name AMALinux -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location>
```
---

### <a name="azure-arc-enabled-servers"></a>已啟用 Azure Arc 的伺服器
使用下列 PowerShell 命令來安裝 Azure 監視器代理程式 onAzure 啟用 Arc 的伺服器。
# <a name="windows"></a>[Windows](#tab/PowerShellWindowsArc)
```powershell
New-AzConnectedMachineExtension -Name AMAWindows -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -MachineName <virtual-machine-name> -Location <location>
```
# <a name="linux"></a>[Linux](#tab/PowerShellLinuxArc)
```powershell
New-AzConnectedMachineExtension -Name AMALinux -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -MachineName <virtual-machine-name> -Location <location>
```
---
## <a name="azure-cli"></a>Azure CLI
您可以使用新增虛擬機器擴充功能的 Azure CLI 命令，在 Azure 虛擬機器和已啟用 Azure Arc 的伺服器上安裝 Azure 監視器代理程式。 

### <a name="azure-virtual-machines"></a>Azure 虛擬機器
使用下列 CLI 命令在 Azure 虛擬機器上安裝 Azure 監視器代理程式。
# <a name="windows"></a>[Windows](#tab/CLIWindows)
```azurecli
az vm extension set --name AzureMonitorWindowsAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id>
```
# <a name="linux"></a>[Linux](#tab/CLILinux)
```azurecli
az vm extension set --name AzureMonitorLinuxAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id>
```
---
### <a name="azure-arc-enabled-servers"></a>已啟用 Azure Arc 的伺服器
使用下列 CLI 命令來安裝 Azure 監視器代理程式 onAzure 啟用 Arc 的伺服器。

# <a name="windows"></a>[Windows](#tab/CLIWindowsArc)
```azurecli
az connectedmachine machine-extension create --name AzureMonitorWindowsAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id>
```
# <a name="linux"></a>[Linux](#tab/CLILinuxArc)
```azurecli
az connectedmachine machine-extension create --name AzureMonitorLinuxAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id>
```
---


## <a name="next-steps"></a>後續步驟

- [建立資料集合規則](data-collection-rule-azure-monitor-agent.md) ，以從代理程式收集資料，並將資料傳送至 Azure 監視器。
