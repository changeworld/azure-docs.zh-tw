---
title: PowerShell - 在 Azure 開發人員測試實驗室的實驗室中創建自訂角色
description: 本文提供了一個 Azure PowerShell 腳本，該腳本將外部使用者添加到 Azure DevTest 實驗室中的實驗室。
services: lab-services
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: 9605b3c63877335b8f180ae5fd2b06edc1601096
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760437"
---
# <a name="use-powershell-to-create-a-custom-role-in-a-lab-in-azure-devtest-labs"></a>使用 PowerShell 在 Azure DevTest Labs 的實驗室中建立自訂角色

此範例 PowerShell 指令碼會建立自訂角色，以在 Azure DevTest Labs 的實驗室中使用。 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Prerequisites
* **實驗室**。 指令碼需要您擁有現有的實驗室。 

## <a name="sample-script"></a>範例指令碼

[!code-powershell[main](../../../powershell_scripts/devtest-lab/create-custom-role-in-lab/create-custom-role-in-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令： 

| Command | 注意 |
|---|---|
| [獲取-阿茲供應商操作](/powershell/module/az.resources/get-azprovideroperation) | 取得使用 Azure RBAC 保護安全的 Azure 資源提供者作業。 |
| [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) | 列出可用來指派的所有 Azure RBAC 角色。 |
| [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) | 建立自訂角色。 |

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 的詳細資訊，請參閱 [Azure PowerShell 文件](https://docs.microsoft.com/powershell/)。

您可以在 [Azure 實驗室服務 PowerShell 範例](../samples-powershell.md)中找到其他的 Azure 實驗室服務 PowerShell 指令碼範例。
