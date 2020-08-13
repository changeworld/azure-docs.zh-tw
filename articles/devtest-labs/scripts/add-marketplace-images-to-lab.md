---
title: PowerShell-在 Azure DevTest Labs 中將 marketplace 映射新增至實驗室
description: 這個 PowerShell 指令碼會將市集映像新增至 Azure DevTest Labs 的實驗室中。
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: 543f20af270769dd16e4a1ecf6ee93e9259cdfd1
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/12/2020
ms.locfileid: "88136226"
---
# <a name="use-powershell-to-add-a-marketplace-image-to-a-lab-in-azure-devtest-labs"></a>使用 PowerShell 將市集映像新增至 Azure DevTest Labs 的實驗室中

這個範例 PowerShell 指令碼會將市集映像新增至 Azure DevTest Labs 的實驗室中。 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>先決條件
* **實驗室**。 指令碼需要您擁有現有的實驗室。 

## <a name="sample-script"></a>範例指令碼

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-marketplace-images-to-lab/add-marketplace-images-to-lab.ps1 "Add marketplace images to a lab")]

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
