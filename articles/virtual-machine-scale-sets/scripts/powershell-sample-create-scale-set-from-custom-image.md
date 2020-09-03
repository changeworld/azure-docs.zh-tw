---
title: Azure PowerShell 範例 - 使用自訂 VM 映像
description: 此指令碼會建立可使用自訂 VM 映像作為 VM 執行個體來源的虛擬機器擴展集。
author: axayjo
ms.author: akjosh
ms.topic: sample
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.date: 03/27/2018
ms.reviewer: cynthn
ms.custom: akjosh, devx-track-azurepowershell
ms.openlocfilehash: 34a786c133d38e40cfa614ddceed53a6cf52b617
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2020
ms.locfileid: "89078923"
---
# <a name="create-a-virtual-machine-scale-set-from-a-custom-vm-image-with-powershell"></a>使用 PowerShell 從自訂 VM 映像建立虛擬機器擴展集
此指令碼會建立可使用自訂 VM 映像作為 VM 執行個體來源的虛擬機器擴展集。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="sample-script"></a>範例指令碼

[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/use-custom-vm-image/use-custom-vm-image.ps1 "Create a virtual machine scale set with a custom VM image")]

## <a name="clean-up-deployment"></a>清除部署
執行下列命令來移除資源群組、擴展集和所有相關資源。

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>指令碼說明
此指令碼會使用下列命令來建立部署。 下表中的每個項目都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [New-AzVmss](/powershell/module/az.compute/new-azvmss) | 建立虛擬機器擴展集和所有支援的資源，包括虛擬網路、負載平衡器和 NAT 規則。 |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 移除資源群組及其內含的所有資源。 |

## <a name="next-steps"></a>後續步驟
如需有關 Azure PowerShell 模組的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/azure/)。
