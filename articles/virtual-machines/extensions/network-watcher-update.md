---
title: 將網路監看員延伸模組更新為最新版本
description: 瞭解如何將 Azure 網路監看員延伸模組更新為最新版本。
services: virtual-machines-windows
documentationcenter: ''
author: damendo
manager: balar
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.workload: infrastructure-services
ms.date: 09/23/2020
ms.author: damendo
ms.openlocfilehash: 23520a0249e22b3f81c7f7c598ef10d8c3acb550
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92900182"
---
# <a name="update-the-network-watcher-extension-to-the-latest-version"></a>將網路監看員延伸模組更新為最新版本

## <a name="overview"></a>概觀

[Azure 網路](../../network-watcher/network-watcher-monitoring-overview.md) 監看員是一種網路效能監視、診斷和分析服務，可監視 Azure 網路。  (VM) 擴充功能的網路監看員代理程式虛擬機器，是視需要捕獲網路流量，以及在 Azure Vm 上使用其他 advanced 功能的必要條件。 網路監看員延伸模組是由連線監視器、連線監視器 (預覽) 、連線疑難排解和封包捕獲等功能使用。

## <a name="prerequisites"></a>Prerequisites

本文假設您已在 VM 中安裝網路監看員延伸模組。

## <a name="latest-version"></a>最新版本

目前的網路監看員延伸模組最新版本 `1.4.1654.1` 。

## <a name="update-your-extension"></a>更新您的延伸模組

若要更新您的延伸模組，您需要知道您的延伸模組版本。

### <a name="check-your-extension-version"></a>檢查您的延伸模組版本

您可以使用 Azure 入口網站、Azure CLI 或 PowerShell 來檢查您的延伸模組版本。

#### <a name="usetheazureportal"></a>使用 Azure 入口網站

1. 在 Azure 入口網站中，移至 VM 的 [ **擴充** 功能] 窗格。
1. 選取 **AzureNetworkWatcher** 延伸模組，以查看詳細資料窗格。
1. 在 [ **版本** ] 欄位中找出版本號碼。  

#### <a name="use-the-azure-cli"></a>使用 Azure CLI

從 Azure CLI 提示字元執行下列命令：

```azurecli
az vm get-instance-view --resource-group  "SampleRG" --name "Sample-VM"
```
在輸出中找出 **"AzureNetworkWatcherExtension"** ，並從輸出中的 *"TypeHandlerVersion"* 欄位識別版本號碼。  請注意：延伸模組的相關資訊會出現在 JSON 輸出中多次。 請查看「延伸模組」區塊，您應該會看到延伸模組的完整版本號碼。 

您應該會看到如下所示的內容： ![ Azure CLI 的螢幕擷取畫面](./media/network-watcher/azure-cli-screenshot.png)

#### <a name="usepowershell"></a>使用 PowerShell

從 PowerShell 提示字元執行下列命令：

```powershell
Get-AzVM -ResourceGroupName "SampleRG" -Name "Sample-VM" -Status
```
在輸出中找出 Azure 網路監看員延伸模組，並從輸出中的 *"TypeHandlerVersion"* 欄位識別版本號碼。   

您應該會看到如下所示的內容： ![ PowerShell 螢幕擷取畫面](./media/network-watcher/powershell-screenshot.png)

### <a name="update-your-extension"></a>更新您的延伸模組

如果您的版本早于 `1.4.1654.1` （目前最新版本），請使用下列任何選項來更新您的延伸模組。

#### <a name="option-1-use-powershell"></a>選項1：使用 PowerShell

執行下列命令：

```powershell
#Linux command
Set-AzVMExtension -ResourceGroupName "myResourceGroup1" -Location "WestUS" -VMName "myVM1" -Name "AzureNetworkWatcherExtension" -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentLinux"

#Windows command
Set-AzVMExtension -ResourceGroupName "myResourceGroup1" -Location "WestUS" -VMName "myVM1" -Name "AzureNetworkWatcherExtension" -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentWindows"
```

如果無法運作。 使用下列步驟，再次移除並安裝擴充功能。 這會自動新增最新版本。

移除擴充功能 

```powershell
#Same command for Linux and Windows
Remove-AzVMExtension -ResourceGroupName "SampleRG" -VMName "Sample-VM" -Name "AzureNetworkWatcherExtension"
``` 

再次安裝擴充功能

```powershell
#Linux command
Set-AzVMExtension -ResourceGroupName "SampleRG" -Location "centralus" -VMName "Sample-VM" -Name "AzureNetworkWatcherExtension" -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentLinux" -typeHandlerVersion "1.4"

#Windows command
Set-AzVMExtension -ResourceGroupName "SampleRG" -Location "centralus" -VMName "Sample-VM" -Name "AzureNetworkWatcherExtension" -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentWindows" -typeHandlerVersion "1.4"
```

#### <a name="option-2-use-the-azure-cli"></a>選項2：使用 Azure CLI

強制升級。

```azurecli
#Linux command
az vm extension set --resource-group "myResourceGroup1" --vm-name "myVM1" --name "NetworkWatcherAgentLinux" --publisher "Microsoft.Azure.NetworkWatcher" --force-update

#Windows command
az vm extension set --resource-group "myResourceGroup1" --vm-name "myVM1" --name "NetworkWatcherAgentWindows" --publisher "Microsoft.Azure.NetworkWatcher" --force-update
```

如果無法運作，請再次移除並安裝擴充功能，並遵循下列步驟來自動新增最新版本。

移除擴充功能。

```azurecli
#Same for Linux and Windows
az vm extension delete --resource-group "myResourceGroup1" --vm-name "myVM1" -n "AzureNetworkWatcherExtension"

```

請重新安裝延伸模組。

```azurecli
#Linux command
az vm extension set --resource-group "DALANDEMO" --vm-name "Linux-01" --name "NetworkWatcherAgentLinux" --publisher "Microsoft.Azure.NetworkWatcher"

#Windows command
az vm extension set --resource-group "DALANDEMO" --vm-name "Linux-01" --name "NetworkWatcherAgentWindows" --publisher "Microsoft.Azure.NetworkWatcher"

```

#### <a name="option-3-reboot-your-vms"></a>選項3：重新開機 Vm

如果您已將網路監看員延伸模組的自動升級設定為 true，請將您的 VM 安裝重新開機至最新的擴充功能。

## <a name="support"></a>支援

如果您在本文的任何時間點需要更多協助，請參閱 [Linux](./network-watcher-linux.md) 或 [Windows](./network-watcher-windows.md)的網路監看員延伸模組檔。 您也可以洽詢 [MSDN azure 和 Stack Overflow 論壇](https://azure.microsoft.com/support/forums/)上的 azure 專家。 或者，提出 Azure 支援事件。 移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [ **取得支援** ]。 如需使用 Azure 支援的資訊，請參閱 [Microsoft Azure 支援常見問題集](https://azure.microsoft.com/support/faq/)。
