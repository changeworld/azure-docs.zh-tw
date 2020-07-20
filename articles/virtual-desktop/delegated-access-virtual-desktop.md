---
title: Windows 虛擬桌面中的委派存取-Azure
description: 如何在 Windows 虛擬桌面部署上委派系統管理功能，包括範例。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 16b4fca475f91a8cb5b7f9a20ea5aa74b6b674a3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82612855"
---
# <a name="delegated-access-in-windows-virtual-desktop"></a>Windows 虛擬桌面中委派的存取權

>[!IMPORTANT]
>此內容適用於具有 Azure Resource Manager Windows 虛擬桌面物件的 2020 年春季更新版。 如果您使用不含 Azure Resource Manager 物件的 Windows 虛擬桌面 2019 年秋季版，請參閱[這篇文章](./virtual-desktop-fall-2019/delegated-access-virtual-desktop-2019.md)。
>
> Windows 虛擬桌面 2020 年春季更新版目前為公開預覽狀態。 此預覽版本是在沒有服務等級協定的情況下提供，不建議您將其用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Windows 虛擬桌面具有委派的存取模型，可讓您藉由指派角色給使用者，來定義允許特定使用者擁有的存取權數量。 角色指派有三個元件：安全性主體、角色定義和範圍。 Windows 虛擬桌面委派的存取模型是以 Azure RBAC 模型為基礎。 若要深入瞭解特定角色指派及其元件，請參閱[Azure 角色型存取控制總覽](../role-based-access-control/built-in-roles.md)。

Windows 虛擬桌面委派存取針對角色指派的每個元素支援下列值：

* 安全性主體
    * 使用者
    * 使用者群組
    * 服務主體
* 角色定義
    * 內建角色
    * 自訂角色
* 影響範圍
    * 主機集區
    * 應用程式群組
    * 工作區

## <a name="powershell-cmdlets-for-role-assignments"></a>用於角色指派的 PowerShell Cmdlet

開始之前，請務必遵循[設定 powershell 模組](powershell-module.md)中的指示來設定 Windows 虛擬桌面 powershell 模組（如果尚未安裝的話）。

Windows 虛擬桌面會在將應用程式群組發佈至使用者或使用者群組時，使用 Azure 角色型存取控制（RBAC）。 「桌面虛擬化」使用者角色會指派給使用者或使用者群組，而範圍則是「應用程式群組」。 此角色可讓使用者在應用程式群組上進行特殊資料存取。  

執行下列 Cmdlet，將 Azure Active Directory 使用者新增至應用程式群組：

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <hostpoolname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'  
```

執行下列 Cmdlet，將 Azure Active Directory 使用者群組新增至應用程式群組：

```powershell
New-AzRoleAssignment -ObjectId <usergroupobjectid> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <hostpoolname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups' 
```

## <a name="next-steps"></a>後續步驟

如需每個角色可使用之 PowerShell Cmdlet 的更完整清單，請參閱[powershell 參考](/powershell/windows-virtual-desktop/overview)。

如需 Azure RBAC 中所支援角色的完整清單，請參閱[azure 內建角色](../role-based-access-control/built-in-roles.md)。

如需如何設定 Windows 虛擬桌面環境的指導方針，請參閱[Windows 虛擬桌面環境](environment-setup.md)。
