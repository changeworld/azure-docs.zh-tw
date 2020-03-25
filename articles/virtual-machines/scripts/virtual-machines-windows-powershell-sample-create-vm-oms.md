---
title: Azure PowerShell 指令碼範例 - Azure 監視器記錄
description: Azure PowerShell 指令碼範例 - Azure 監視器記錄
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/12/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: fdffb05ac7aff8aac2db9ac588c1dedf8f5c9ea5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "74038950"
---
# <a name="create-an-azure-monitor-vm-with-powershell"></a>使用 PowerShell 建立 Azure 監視器 VM 

此指令碼會建立 Azure 虛擬機器、安裝 Log Analytics 代理程式，並向 Log Analytics 工作區註冊系統。 執行此指令碼後，就能在 Azure 監視器中看到虛擬機器。 此外，您需要更新 Log Analytics 工作區識別碼和工作區金鑰。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>範例指令碼

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-monitor-oms/create-windows-vm-detailed-oms.ps1 "Create VM Log Analytics")]

## <a name="clean-up-deployment"></a>清除部署

執行下列命令來移除資源群組、VM 和所有相關資源。

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令來建立部署。 下表中的每個項目都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) | 建立用來存放所有資源的資源群組。 |
| [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) | 建立虛擬機器，並將它連線到網路卡、虛擬網路、子網路及網路安全性群組。 此命令也會開啟連接埠 80 並設定系統管理認證。 |
| [Set-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension) | 將 VM 擴充功能新增至虛擬機器。 |
|[Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | 移除資源群組及其內含的所有資源。 |

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 模組的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/azure/overview)。

您可以在 [Azure Windows VM 文件](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)中找到其他的虛擬機器 PowerShell 指令碼範例。
