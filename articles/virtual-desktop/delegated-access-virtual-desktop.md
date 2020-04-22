---
title: 在 Windows 虛擬桌面中委派存取 - Azure
description: 如何委派 Windows 虛擬桌面部署的管理功能,包括示例。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 91451ff3024a9a5019b3982b0e4471e2c4d80c74
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683906"
---
# <a name="delegated-access-in-windows-virtual-desktop"></a>Windows 虛擬桌面中委派的存取權

Windows 虛擬桌面具有委派的訪問模型,允許您通過為其分配角色來定義特定使用者允許其擁有的訪問量。 角色分配有三個元件:安全主體、角色定義和範圍。 Windows 虛擬桌面委派訪問模型基於 Azure RBAC 模型。 要瞭解有關特定角色分配及其元件的詳細資訊,請參閱[Azure 基於角色的存取控制概述](../role-based-access-control/built-in-roles.md)。

Windows 虛擬桌面委派存取支援角色分配的每個元素的以下值:

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

Windows 虛擬桌面中的委派訪問許可權具有多個內建角色定義,您可以將其分配給使用者和服務主體。

* RDS 擁有者可以管理所有內容,包括訪問資源。
* RDS 參與者可以管理所有內容,但不能訪問資源。
* RDS 閱讀器可以查看所有內容,但不能進行任何更改。
* RDS 操作員可以查看診斷活動。

## <a name="powershell-cmdlets-for-role-assignments"></a>在角色配置的 PowerShell cmdlet

您可以執行以下 cmdlet 來建立、檢視與移除角色配置:

* **獲取 RdsRole 分配**顯示角色分配的清單。
* **新 RdsRole 分配**創建新的角色分配。
* **刪除 RdsRole 分配**將刪除角色分配。

### <a name="accepted-parameters"></a>接受的參數

您可以使用以下參數修改基本三個 cmdlet:

* **AadTenantId**:指定服務主體是成員的 Azure 活動目錄租戶 ID。
* **AppGroup 名稱**:遠端桌面應用組的名稱。
* **診斷**:指示診斷範圍。 (必須與**基礎結構**或**租戶**參數配對。
* **主機池名稱**:遠端桌面主機池的名稱。
* **基礎結構**:指示基礎結構範圍。
* **角色定義名稱**:分配給使用者、組或應用的基於角色的遠端桌面服務存取控制角色的名稱。 (例如,遠端桌面服務擁有者、遠端桌面服務讀取器等。
* **伺服器原則名稱**:Azure 活動目錄應用程式的名稱。
* **簽名名稱**:使用者的電子郵件地址或用戶主體名稱。
* **租戶名稱**:遠端桌面租戶的名稱。

## <a name="next-steps"></a>後續步驟

有關每個角色可以使用的 PowerShell cmdlet 的更完整清單,請參閱[PowerShell 引用](/powershell/windows-virtual-desktop/overview)。

有關如何設定 Windows 虛擬桌面環境的指南,請參閱[Windows 虛擬桌面環境](environment-setup.md)。
