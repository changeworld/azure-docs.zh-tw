---
title: Windows 虛擬桌面 (傳統) 中的委派存取-Azure
description: 如何將 Windows 虛擬桌面的管理功能委派 (傳統) 部署，包括範例。
author: Heidilohr
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 0152dc5102fd3f77418448234cab1234d25b97c6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88008146"
---
# <a name="delegated-access-in-windows-virtual-desktop-classic"></a>Windows 虛擬桌面中的委派存取 (傳統) 

>[!IMPORTANT]
>此內容適用於不支援 Azure Resource Manager Windows 虛擬桌面物件的 Windows 虛擬桌面 (傳統)。 如果您嘗試管理 Azure Resource Manager Windows 虛擬桌面物件，請參閱[這篇文章](../delegated-access-virtual-desktop.md)。

Windows 虛擬桌面具有委派的存取模型，可讓您藉由指派角色給使用者，來定義允許特定使用者擁有的存取量。 角色指派有三個元件：安全性主體、角色定義和範圍。 Windows 虛擬桌面委派的存取模型是以 Azure RBAC 模型為基礎。 若要深入瞭解特定角色指派及其元件，請參閱 [Azure 角色型存取控制的總覽](../../role-based-access-control/built-in-roles.md)。

Windows 虛擬桌面委派存取可針對角色指派的每個元素支援下列值：

* 安全性主體
    * 使用者
    * 服務主體
* 角色定義
    * 內建角色
* 影響範圍
    * 租使用者群組
    * 租用戶
    * 主機集區
    * 應用程式群組

## <a name="built-in-roles"></a>內建角色

Windows 虛擬桌面中的委派存取權有數個內建角色定義，可供您指派給使用者和服務主體。

* RDS 擁有者可以管理所有專案，包括對資源的存取。
* RDS 參與者可以管理所有專案，但無法存取資源。
* RDS 讀取器可以查看所有內容，但無法進行任何變更。
* RDS 操作員可以看到診斷活動。

## <a name="powershell-cmdlets-for-role-assignments"></a>角色指派的 PowerShell Cmdlet

您可以執行下列 Cmdlet 來建立、查看和移除角色指派：

* **RdsRoleAssignment** 會顯示角色指派清單。
* **RdsRoleAssignment** 會建立新的角色指派。
* **RdsRoleAssignment** 刪除角色指派。

### <a name="accepted-parameters"></a>接受的參數

您可以使用下列參數修改基本三個 Cmdlet：

* **AadTenantId**：指定服務主體為其成員的 Azure Active Directory 租使用者識別碼。
* **AppGroupName**：遠端桌面應用程式群組的名稱。
* **診斷**：指出診斷範圍。  (必須搭配 **基礎結構** 或 **租** 使用者參數。 ) 
* **HostPoolName**：遠端桌面主機集區的名稱。
* **基礎結構**：指出基礎結構的範圍。
* **RoleDefinitionName**：指派給使用者、群組或應用程式遠端桌面服務角色型存取控制角色的名稱。  (例如遠端桌面服務擁有者、遠端桌面服務讀取者等等。 ) 
* **ServerPrincipleName**： Azure Active Directory 應用程式的名稱。
* **SignInName**：使用者的電子郵件地址或使用者主體名稱。
* **TenantName**：遠端桌面租使用者的名稱。

## <a name="next-steps"></a>後續步驟

如需每個角色可以使用的 PowerShell Cmdlet 更完整清單，請參閱 [powershell 參考](/powershell/windows-virtual-desktop/overview)。

如需如何設定 Windows 虛擬桌面環境的指導方針，請參閱 [Windows 虛擬桌面環境](environment-setup-2019.md)。
