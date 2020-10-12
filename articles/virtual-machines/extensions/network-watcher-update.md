---
title: 將網路監看員延伸模組更新為最新版本
description: 瞭解如何將網路監看員延伸模組更新為最新版本
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
ms.openlocfilehash: c386685d63894472623ffc4392a529541a91391c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91410314"
---
# <a name="how-to-update-the-network-watcher-extension-to-the-latest-the-version"></a>如何將網路監看員延伸模組更新為最新版本 

## <a name="overview"></a>概觀

[Azure 網路監看員](../../network-watcher/network-watcher-monitoring-overview.md)是網路效能的監視、診斷和分析服務，可讓您監視 Azure 網路。 依需求擷取網路流量及 Azure 虛擬機器上的其他進階功能，都需要網路監看員代理程式虛擬機器擴充功能。 網路監看員延伸模組是由連線監視器、連線監視器 (預覽) 、連線疑難排解和封包捕獲等功能使用。   

## <a name="prerequisites"></a>必要條件
本檔假設您已在虛擬機器中安裝網路監看員延伸模組，並提供將其更新為最新版本的指示。 

## <a name="latest-version"></a>最新版本
目前的網路監看員延伸模組最新版本 `1.4.1654.1` 。

## <a name="updating-your-extension"></a>更新您的延伸模組 

### <a name="check-your-extension-version"></a>檢查您的延伸模組版本  

**使用 Azure 入口網站**

1. 在 Azure 入口網站中，移至 VM 的 [擴充功能] 分頁。   
2. 按一下 [AzureNetworkWatcher] 延伸模組，以查看 [詳細資料] 窗格。  
3. 在 [版本] 欄位中找出版本號碼。  

**使用 Azure CLI** 從 Azure CLI 提示字元執行下列命令。   

```azurecli
az vm extension list --resource-group  <ResourceGroupName> --vm-name <VMName>
```

在輸出中找出 AzureNetworkWatcher 延伸模組，並從輸出中的 "TypeHandlerVersion" 欄位識別版本號碼。  


**使用 PowerShell** 從 PowerShell 提示字元執行下列命令：   

```powershell
Get-AzVMExtension -ResourceGroupName <ResourceGroupName> -VMName <VMName>  
```

在輸出中找出 AzureNetworkWatcher 延伸模組，並從輸出中的 "TypeHandlerVersion" 欄位識別版本號碼。   


### <a name="update-your-extension"></a>更新您的延伸模組

如果您的版本低於 `1.4.1654.1` 目前最新版本)  (，請使用下列任何選項來更新您的延伸模組。 

**選項1：使用 PowerShell**

```powershell
#Linux command
Set-AzVMExtension `  -ResourceGroupName "myResourceGroup1" `  -Location "WestUS" `  -VMName "myVM1" `  -Name "AzureNetworkWatcherExtension" `  -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentLinux"   

#Windows command
Set-AzVMExtension `  -ResourceGroupName "myResourceGroup1" `  -Location "WestUS" `  -VMName "myVM1" `  -Name "AzureNetworkWatcherExtension" `  -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentWindows"   
```


**選項2：使用 Azure CLI**  

強制升級 

```azurecli
#Linux command
az vm extension set --resource-group "myResourceGroup1" --vm-name "myVM1" --name "NetworkWatcherAgentLinux" --publisher "Microsoft.Azure.NetworkWatcher" --force-update

#Windows command
az vm extension set --resource-group "myResourceGroup1" --vm-name "myVM1" --name "NetworkWatcherAgentWindows" --publisher "Microsoft.Azure.NetworkWatcher" --force-update
```

如果無法運作。 使用下列步驟，再次移除並安裝擴充功能。 這會自動新增最新版本。 

移除擴充功能 

```azurecli
#Same for Linux and Windows
az vm extension delete --resource-group "myResourceGroup1" --vm-name "myVM1" -n "AzureNetworkWatcherExtension"

```

再次安裝擴充功能

```azurecli
#Linux command
az vm extension set --resource-group "DALANDEMO" --vm-name "Linux-01" --name "NetworkWatcherAgentLinux" --publisher "Microsoft.Azure.NetworkWatcher"  

#Windows command
az vm extension set --resource-group "DALANDEMO" --vm-name "Linux-01" --name "NetworkWatcherAgentWindows" --publisher "Microsoft.Azure.NetworkWatcher" 

```

**選項3：重新開機 Vm**

如果您已將 NetworkWatcher 延伸模組的自動升級設定為 true。 重新開機您的 VM 會安裝最新的擴充功能。


## <a name="support"></a>支援

如果您在本文的任何時間點需要更多說明，您可以參閱網路監看員延伸模組檔 ([Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-linux)、 [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-windows)) 或與 [MSDN azure 和 Stack Overflow 論壇](https://azure.microsoft.com/support/forums/)上的 azure 專家聯繫。 或者，您可以提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]。 如需使用 Azure 支援的資訊，請參閱 [Microsoft Azure 支援常見問題集](https://azure.microsoft.com/support/faq/)。
