---
title: 在 Windows 虛擬桌面中委派訪問 - Azure
description: 如何委派 Windows 虛擬桌面部署的管理功能，包括示例。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 3e27550ecc5b42c2bf0d947690da09e13d88ea4f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128026"
---
# <a name="delegated-access-in-windows-virtual-desktop"></a>Windows 虛擬桌面中委派的存取權

Windows 虛擬桌面具有委派的訪問模型，允許您通過為其分配角色來定義特定使用者允許其擁有的訪問量。 角色指派有三個元件：安全主體、角色定義和範圍。 Windows 虛擬桌面委派訪問模型基於 Azure RBAC 模型。 要瞭解有關特定角色指派及其元件的詳細資訊，請參閱[Azure 基於角色的存取控制概述](../role-based-access-control/built-in-roles.md)。

Windows 虛擬桌面委派訪問支援角色指派的每個元素的以下值：

* 安全性主體
    * 使用者
    * 服務主體
* 角色定義
    * 內建角色
* 影響範圍
    * 租戶組
    * 租用戶
    * 主機池
    * 應用程式群組

## <a name="built-in-roles"></a>內建角色

Windows 虛擬桌面中的委派存取權限具有多個內置角色定義，您可以將其分配給使用者和服務主體。

* RDS 擁有者可以管理所有內容，包括訪問資源。
* RDS 參與者可以管理一切，但訪問資源。
* RDS 閱讀器可以查看所有內容，但不能進行任何更改。
* RDS 操作員可以查看診斷活動。

## <a name="powershell-cmdlets-for-role-assignments"></a>用於角色指派的 PowerShell Cmdlet

您可以運行以下 Cmdlet 來創建、查看和刪除角色指派：

* **獲取 RdsRole 分配**顯示角色指派的清單。
* **新 RdsRole 分配**創建新的角色指派。
* **刪除 RdsRole 分配**將刪除角色指派。

### <a name="accepted-parameters"></a>接受的參數

您可以使用以下參數修改基本三個 Cmdlet：

* **AadTenantId**：指定服務主體是成員的 Azure 活動目錄租戶 ID。
* **AppGroup 名稱**：遠端桌面應用組的名稱。
* **診斷**：指示診斷範圍。 （必須與**基礎結構**或**租戶**參數配對。
* **主機池名稱**：遠端桌面主機池的名稱。
* **基礎結構**：指示基礎結構範圍。
* **角色定義名稱**：分配給使用者、組或應用的基於角色的遠端桌面服務存取控制角色的名稱。 （例如，遠端桌面服務擁有者、遠端桌面服務讀取器等。
* **伺服器原則名稱**：Azure 活動目錄應用程式的名稱。
* **簽名名稱**：使用者的電子郵件地址或使用者主體名稱。
* **租戶名稱**：遠端桌面租戶的名稱。

## <a name="next-steps"></a>後續步驟

有關每個角色可以使用的 PowerShell Cmdlet 的更完整清單，請參閱[PowerShell 引用](/powershell/windows-virtual-desktop/overview)。

有關如何設置 Windows 虛擬桌面環境的指南，請參閱[Windows 虛擬桌面環境](environment-setup.md)。
