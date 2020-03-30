---
title: 用於 PIM 中訪問審核的資源儀表板 - Azure AD |微軟文檔
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847018"
---
# <a name="use-a-resource-dashboard-to-perform-an-access-review-in-privileged-identity-management"></a>使用資源儀表板在特權標識管理中執行訪問審核

您可以使用資源儀表板在特權標識管理 （PIM） 中執行訪問審核。 Azure 活動目錄 （Azure AD） 中的管理檢視儀表板有三個主要元件：

- 資源角色啟動的圖形表示
- 按分配類型顯示角色指派分佈的圖表
- 包含新角色指派資訊的資料區域

![顯示圖表的系統管理員檢視儀表板螢幕擷取畫面](media/pim-resource-roles-overview-dashboards/rbac-overview-top.png)

![顯示資料清單的系統管理員檢視儀表板螢幕擷取畫面](media/pim-resource-roles-overview-dashboards/role-settings.png)

展示過去七天資源角色啟用的圖表。 此資料範圍限制在所選資源，並會顯示最常見角色的啟用 (擁有者、參與者和使用者存取系統管理員)，也會將所有角色的啟用結合顯示。

在啟動圖的一側，兩個圖表顯示按分配類型（使用者和組）的角色指派分佈。 選取圖表的一部分，即可將值更改為特定百分比 (反之亦然)。

圖表下方列出了過去 30 天內具有新角色指派的使用者和組數，以及按總分配按降冪排序的角色數。

## <a name="next-steps"></a>後續步驟

- [在特權標識管理中啟動 Azure 資源角色的訪問審查](pim-resource-roles-start-access-review.md)
