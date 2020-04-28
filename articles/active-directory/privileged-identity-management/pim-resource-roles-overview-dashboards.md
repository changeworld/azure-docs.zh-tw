---
title: PIM 中用於存取審查的資源儀表板-Azure AD |Microsoft Docs
description: 說明如何使用資源儀表板在 Azure AD Privileged Identity Management (PIM) 中執行存取權檢閱。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6affa2ecc8919dabeb6173622b525280ce96bcfe
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "73847018"
---
# <a name="use-a-resource-dashboard-to-perform-an-access-review-in-privileged-identity-management"></a>使用資源儀表板在 Privileged Identity Management 中執行存取權審核

您可以使用資源儀表板，在 Privileged Identity Management （PIM）中執行存取權審查。 Azure Active Directory （Azure AD）中的 [管理檢視] 儀表板有三個主要元件：

- 資源角色啟用的圖形表示
- 依指派類型顯示角色指派分佈的圖表
- 包含新角色指派資訊的資料區域

![顯示圖表的系統管理員檢視儀表板螢幕擷取畫面](media/pim-resource-roles-overview-dashboards/rbac-overview-top.png)

![顯示資料清單的系統管理員檢視儀表板螢幕擷取畫面](media/pim-resource-roles-overview-dashboards/role-settings.png)

展示過去七天資源角色啟用的圖表。 此資料範圍限制在所選資源，並會顯示最常見角色的啟用 (擁有者、參與者和使用者存取系統管理員)，也會將所有角色的啟用結合顯示。

在啟用圖形的一側，兩個圖表會顯示使用者和群組的角色指派（依指派類型）分佈。 選取圖表的一部分，即可將值更改為特定百分比 (反之亦然)。

下圖列出過去30天內具有新角色指派的使用者和群組數目，以及依總指派的遞減順序排序的角色。

## <a name="next-steps"></a>後續步驟

- [在 Privileged Identity Management 中開始使用 Azure 資源角色的存取權審查](pim-resource-roles-start-access-review.md)
