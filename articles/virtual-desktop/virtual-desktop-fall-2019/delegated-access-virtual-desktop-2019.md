---
title: Windows 虛擬桌面中的委派存取-Azure
description: 如何在 Windows 虛擬桌面部署上委派系統管理功能，包括範例。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 899e42e3b592ce1bc92d7d47578e10355b1c6de7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82614430"
---
# <a name="delegated-access-in-windows-virtual-desktop"></a>Windows 虛擬桌面中委派的存取權

>[!IMPORTANT]
>此內容適用於不支援 Azure Resource Manager Windows 虛擬桌面物件的 2019 年秋季版本。 如果您嘗試管理 2020 年春季版更新中引進的 Azure Resource Manager Windows 虛擬桌面物件，請參閱[這篇文章](../delegated-access-virtual-desktop.md)。

Windows 虛擬桌面具有委派的存取模型，可讓您藉由指派角色給使用者，來定義允許特定使用者擁有的存取權數量。 角色指派有三個元件：安全性主體、角色定義和範圍。 Windows 虛擬桌面委派的存取模型是以 Azure RBAC 模型為基礎。 若要深入瞭解特定角色指派及其元件，請參閱[Azure 角色型存取控制總覽](../../role-based-access-control/built-in-roles.md)。

Windows 虛擬桌面委派存取針對角色指派的每個元素支援下列值：

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

Windows 虛擬桌面中的委派存取有數個內建角色定義，您可以將其指派給使用者和服務主體。

* RDS 擁有者可以管理所有專案，包括對資源的存取。
* RDS 參與者可以管理所有專案，但無法存取資源。
* RDS 讀取器可以查看所有專案，但無法進行任何變更。
* RDS 運算子可以查看診斷活動。

## <a name="powershell-cmdlets-for-role-assignments"></a>用於角色指派的 PowerShell Cmdlet

您可以執行下列 Cmdlet 來建立、查看和移除角色指派：

* **RdsRoleAssignment**顯示角色指派清單。
* **RdsRoleAssignment**會建立新的角色指派。
* **RdsRoleAssignment**會刪除角色指派。

### <a name="accepted-parameters"></a>接受的參數

您可以使用下列參數來修改基本三個 Cmdlet：

* **AadTenantId**：指定服務主體為其成員的 Azure Active Directory 租使用者識別碼。
* **AppGroupName**：遠端桌面應用程式群組的名稱。
* **診斷**：表示診斷範圍。 （必須與**基礎結構**或**租**使用者參數配對）。
* **HostPoolName**：遠端桌面主機集區的名稱。
* **基礎結構**：指出基礎結構範圍。
* **RoleDefinitionName**：指派給使用者、群組或應用程式遠端桌面服務角色型存取控制角色的名稱。 （例如，遠端桌面服務擁有者、遠端桌面服務讀者等等）。
* **ServerPrincipleName**： Azure Active Directory 應用程式的名稱。
* **SignInName**：使用者的電子郵件地址或使用者主體名稱。
* **TenantName**：遠端桌面租使用者的名稱。

## <a name="next-steps"></a>後續步驟

如需每個角色可使用之 PowerShell Cmdlet 的更完整清單，請參閱[powershell 參考](/powershell/windows-virtual-desktop/overview)。

如需如何設定 Windows 虛擬桌面環境的指導方針，請參閱[Windows 虛擬桌面環境](environment-setup-2019.md)。
