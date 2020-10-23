---
title: 使用 Azure CLI 啟用 VM 擴充功能
description: 本文說明如何使用 Azure CLI，將虛擬機器擴充功能部署到在混合式雲端環境中執行的 Azure Arc 啟用的伺服器。
ms.date: 10/19/2020
ms.topic: conceptual
ms.openlocfilehash: 8f09914f246635f07b3c51c682bd67591c706732
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92462905"
---
# <a name="enable-azure-vm-extensions-using-the-azure-cli"></a>使用 Azure CLI 啟用 Azure VM 擴充功能

本文說明如何使用 Azure CLI 將 Azure Arc 啟用的伺服器所支援的 Azure VM 擴充功能部署和卸載至 Linux 或 Windows 混合式電腦。

[!INCLUDE [Azure CLI Prepare your environment](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="prerequisites"></a>先決條件

[安裝 Azure CLI](/cli/azure/install-azure-cli)。

在使用 Azure CLI 管理已啟用 Arc 之伺服器所管理的混合式伺服器上的 VM 擴充功能之前，您必須安裝 `ConnectedMachine` CLI 擴充功能。 在啟用 Arc 的伺服器上執行下列命令：

```azurecli
az extension add connectedmachine
```

當安裝完成時，會傳回下列訊息：

`The installed extension `connectedmachine` is experimental and not covered by customer support. Please use with discretion.`

## <a name="enable-extension"></a>啟用延伸模組

若要在已啟用 Arc 的伺服器上啟用 VM 擴充功能，請使用 [az connectedmachine 電腦延伸模組建立](/cli/azure/ext/connectedmachine/connectedmachine/machine-extension#ext_connectedmachine_az_connectedmachine_machine_extension_create) 搭配 `--machine-name` 、 `--extension-name` 、 `--location` 、 `--type` 、 `settings` 和 `--publisher` 參數。

下列範例會在啟用 Arc 的 Linux 伺服器上啟用 Log Analytics VM 延伸模組：

```azurecli
az connectedmachine machine-extension create --machine-name "myMachineName" --name "OmsAgentforLinux" --location "eastus" --type "CustomScriptExtension" --publisher "Microsoft.EnterpriseCloud.Monitoring" --settings "{\"workspaceId\":\"workspaceId"}" --protected-settings "{\workspaceKey\":"\workspaceKey"} --type-handler-version "1.10" --resource-group "myResourceGroup"
```

下列範例會在啟用 Arc 的伺服器上啟用自訂腳本延伸模組：

```azurecli
az connectedmachine machine-extension create --machine-name "myMachineName" --name "CustomScriptExtension" --location "eastus" --type "CustomScriptExtension" --publisher "Microsoft.Compute" --settings "{\"commandToExecute\":\"powershell.exe -c \\\"Get-Process | Where-Object { $_.CPU -gt 10000 }\\\"\"}" --type-handler-version "1.10" --resource-group "myResourceGroup"
```

## <a name="list-extensions-installed"></a>已安裝清單延伸模組

若要取得已啟用 Arc 之伺服器上的 VM 延伸模組清單，請使用 [az connectedmachine 電腦延伸模組清單](/cli/azure/ext/connectedmachine/connectedmachine/machine-extension#ext_connectedmachine_az_connectedmachine_machine_extension_list) 搭配 `--machine-name` 和 `--resource-group` 參數。

範例：

```azurecli
az connectedmachine machine-extension list --machine-name "myMachineName" --resource-group "myResourceGroup"
```

Azure CLI 命令的輸出預設是採用 JSON (JavaScript 物件標記法) 格式。 舉例來說，若要將預設輸出變更為清單或資料表，請使用 [az configure --output](/cli/azure/reference-index)。 您也可以將 `--output` 新增到任何命令，以輸出格式進行一次變更。

下列範例顯示來自命令的部分 JSON 輸出 `az connectedmachine machine-extension -list` ：

```json
[
  {
    "autoUpgradingMinorVersion": "false",
    "forceUpdateTag": null,
    "id": "/subscriptions/subscriptionId/resourceGroups/resourceGroupName/providers/Microsoft.HybridCompute/machines/SVR01/extensions/DependencyAgentWindows",
    "location": "eastus",
    "name": "DependencyAgentWindows",
    "namePropertiesInstanceViewName": "DependencyAgentWindows",
```

## <a name="remove-an-installed-extension"></a>移除已安裝的擴充功能

若要移除已安裝 Arc 的伺服器上已安裝的 VM 擴充功能，請使用 [az connectedmachine 電腦延伸模組刪除](/cli/azure/ext/connectedmachine/connectedmachine/machine-extension#ext_connectedmachine_az_connectedmachine_machine_extension_delete) 搭配 `--extension-name` 、 `--machine-name` 和 `--resource-group` 參數。

例如，若要移除適用于 Linux 的 Log Analytics VM 擴充功能，請執行下列命令：

```azurecli
az connectedmachine machine-extension delete --machine-name "myMachineName" --name "OmsAgentforLinux" --resource-group "myResourceGroup"
```

## <a name="next-steps"></a>後續步驟

- 您可以從[Azure 入口網站](manage-vm-extensions-portal.md)或[Azure Resource Manager 範本](manage-vm-extensions-template.md)，使用[PowerShell](manage-vm-extensions-powershell.md)來部署、管理和移除 VM 擴充功能。

- 疑難排解資訊可在 [VM 延伸模組指南](troubleshoot-vm-extensions.md)中找到。
