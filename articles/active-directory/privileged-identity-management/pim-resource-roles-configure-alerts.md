---
title: 在特權標識管理中為 Azure 資源角色配置安全警報 - Azure 活動目錄 |微軟文檔
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中設定 Azure 資源角色的安全性警示。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0b938dc808d9b02ad4105d85a5b3125135c51d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74023076"
---
# <a name="configure-security-alerts-for-azure-resource-roles-in-privileged-identity-management"></a>在特權標識管理中為 Azure 資源角色配置安全警報

特權標識管理 （PIM） 在 Azure 活動目錄 （Azure AD） 組織中存在可疑或不安全活動時生成警報。 觸發後的警示會顯示在 [警示] 頁面上。

![Azure 資源 - 警報頁面清單警報、風險級別和計數](media/pim-resource-roles-configure-alerts/rbac-alerts-page.png)

## <a name="review-alerts"></a>檢閱警示

選擇警報以查看列出觸發警報的使用者或角色的報告以及修正指南。

![顯示上次掃描時間、描述、緩解步驟、類型、嚴重性、安全影響以及如何防止下一次掃描的警報報告](media/pim-resource-roles-configure-alerts/rbac-alert-info.png)

## <a name="alerts"></a>警示

| 警示 | Severity | 觸發程序 | 建議 |
| --- | --- | --- | --- |
| **指派了太多的擁有者到資源** |中 |太多使用者具有擁有者角色。 |檢閱清單中的使用者，然後將部分使用者重新指派給較少特殊權限的角色。 |
| **指派太多永久擁有者過到資源** |中 |太多使用者永久指派給某個角色。 |檢閱清單中的使用者，然後重新指派部分使用者以要求啟用角色。 |
| **建立了重複的角色** |中 |多個角色具有相同的準則。 |僅使用其中一個角色。 |

### <a name="severity"></a>Severity

- **高**：因為發生原則違規而需要立即採取行動。 
- **中**：不需要立即採取行動，但表示可能發生原則違規。
- **低**：不需要立即採取行動，但建議進行慣用的原則變更。

## <a name="configure-security-alert-settings"></a>設定安全性警示設定

從 [警示] 頁面中移至 [設定]****。

![突出顯示"設置"的警報頁面](media/pim-resource-roles-configure-alerts/rbac-navigate-settings.png)

自訂不同警示的設定，以便合您的環境和安全性目標。

![設置警報頁面以啟用和配置設置](media/pim-resource-roles-configure-alerts/rbac-alert-settings.png)

## <a name="next-steps"></a>後續步驟

- [在特權標識管理中配置 Azure 資源角色設置](pim-resource-roles-configure-role-settings.md)
