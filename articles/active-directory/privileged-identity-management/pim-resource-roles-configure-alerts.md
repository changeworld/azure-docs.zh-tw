---
title: 在 Privileged Identity Management Azure Active Directory 中設定 Azure 資源角色的安全性警示 |Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中設定 Azure 資源角色的安全性警示。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0bfca096eb49ee9f1807935de1dac49151cc8ac3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84743757"
---
# <a name="configure-security-alerts-for-azure-resource-roles-in-privileged-identity-management"></a>在 Privileged Identity Management 中設定 Azure 資源角色的安全性警示

當您的 Azure Active Directory （Azure AD）組織中有可疑或不安全的活動時，Privileged Identity Management （PIM）會產生警示。 觸發後的警示會顯示在 [警示] 頁面上。

![Azure 資源-列出警示、風險層級和計數的警示頁面](media/pim-resource-roles-configure-alerts/rbac-alerts-page.png)

## <a name="review-alerts"></a>檢閱警示

選取警示以查看列出觸發警示之使用者或角色的報告，以及補救指導方針。

![警示報表，顯示上次掃描時間、描述、風險降低步驟、類型、嚴重性、安全性影響，以及如何預防下一次](media/pim-resource-roles-configure-alerts/rbac-alert-info.png)

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

![已反白顯示設定的警示頁面](media/pim-resource-roles-configure-alerts/rbac-navigate-settings.png)

自訂不同警示的設定，以便合您的環境和安全性目標。

![設定頁面中的警示以啟用和設定設定](media/pim-resource-roles-configure-alerts/rbac-alert-settings.png)

## <a name="next-steps"></a>後續步驟

- [在 Privileged Identity Management 中設定 Azure 資源角色設定](pim-resource-roles-configure-role-settings.md)
