---
title: PowerShell-在 Azure DevTest Labs 中將外部使用者新增至實驗室
description: 本文提供 Azure PowerShell 腳本，可將外部使用者新增至 Azure DevTest Labs 中的實驗室。
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: 76fbb5e4d7f5db39a0aadba9098ebf1064b3cda5
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/12/2020
ms.locfileid: "88136213"
---
# <a name="use-powershell-to-add-an-external-user-to-a-lab-in-azure-devtest-labs"></a>使用 PowerShell 將外部使用者新增至 Azure DevTest Labs 的實驗室中

這個範例 PowerShell 指令碼會將外部使用者新增至 Azure DevTest Labs 的實驗室中。 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>先決條件
* **實驗室**。 指令碼需要您擁有現有的實驗室。 

## <a name="sample-script"></a>範例指令碼

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-external-user-to-lab/add-external-user-to-custom-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令： 

| Command | 注意 |
|---|---|
| [AzADUser](/powershell/module/az.resources/get-azaduser) | 從 Azure Active Directory 擷取使用者物件。 |
| [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) | 在指定的範圍中，將指定的角色指派給指定的主體。 |

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/)。

您可以在 [Azure 實驗室服務 PowerShell 範例](../samples-powershell.md)中找到其他的 Azure 實驗室服務 PowerShell 指令碼範例。
