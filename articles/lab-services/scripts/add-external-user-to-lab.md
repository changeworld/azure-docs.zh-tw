---
title: PowerShell - 將外部使用者添加到 Azure 開發人員測試實驗室中的實驗室
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
ms.openlocfilehash: e9efe5476da051d905feff3d6803cb60ea0c4beb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760482"
---
# <a name="use-powershell-to-add-an-external-user-to-a-lab-in-azure-devtest-labs"></a>使用 PowerShell 將外部使用者新增至 Azure DevTest Labs 的實驗室中

這個範例 PowerShell 指令碼會將外部使用者新增至 Azure DevTest Labs 的實驗室中。 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Prerequisites
* **實驗室**。 指令碼需要您擁有現有的實驗室。 

## <a name="sample-script"></a>範例指令碼

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-external-user-to-lab/add-external-user-to-custom-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令： 

| Command | 注意 |
|---|---|
| [獲取阿達使用者](/powershell/module/az.resources/get-azaduser) | 從 Azure Active Directory 擷取使用者物件。 |
| [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) | 在指定的範圍中，將指定的角色指派給指定的主體。 |

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 的詳細資訊，請參閱 [Azure PowerShell 文件](https://docs.microsoft.com/powershell/)。

您可以在 [Azure 實驗室服務 PowerShell 範例](../samples-powershell.md)中找到其他的 Azure 實驗室服務 PowerShell 指令碼範例。
