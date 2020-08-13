---
title: PowerShell 指令碼：在 Azure 實驗室服務中設定允許的 VM 大小 | Microsoft Docs
description: 本文包含 PowerShell 腳本範例，可在 Azure 實驗室服務中設定允許的虛擬機器 (VM) 大小。
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: 476b86b7c577db17efc39dbac64a527432c916b6
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/12/2020
ms.locfileid: "88136145"
---
# <a name="use-powershell-to-set-allowed-vm-sizes-in-azure-lab-services"></a>使用 PowerShell 在 Azure 實驗室服務中設定允許的 VM 大小

此範例 PowerShell 指令碼會在 Azure 實驗室服務中設定允許的虛擬機器 (VM) 大小。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>先決條件
* **實驗室**。 指令碼需要您擁有現有的實驗室。 

## <a name="sample-script"></a>範例指令碼

[!code-powershell[main](../../../powershell_scripts/devtest-lab/set-allowed-vm-sizes-in-lab/set-allowed-vm-sizes-in-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令： 

| Command | 注意 |
|---|---|
| 尋找-Get-azresource | 根據指定的參數搜尋資源。 |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | 取得資源。 |
| [Set-AzResource](/powershell/module/az.resources/set-azresource) | 修改資源。 |
| [New-AzResource](/powershell/module/az.resources/new-azresource) | 建立資源。 |

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/)。

您可以在 [Azure 實驗室服務 PowerShell 範例](../samples-powershell.md)中找到其他的 Azure 實驗室服務 PowerShell 指令碼範例。
