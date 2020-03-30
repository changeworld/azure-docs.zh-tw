---
title: 條件訪問 - 阻止舊版身份驗證 - Azure 活動目錄
description: 創建自訂條件訪問策略以阻止遺留身份驗證協定
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a868c8199ac34a498a280e2522d6b1e4c7ec370
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295222"
---
# <a name="conditional-access-block-legacy-authentication"></a>條件訪問：阻止舊版身份驗證

由於與舊版身份驗證協定相關的風險增加，Microsoft 建議組織使用這些協定阻止身份驗證請求，並要求進行現代身份驗證。

## <a name="create-a-conditional-access-policy"></a>建立條件式存取原則

以下步驟將有助於創建條件訪問策略以阻止舊版身份驗證請求。 此策略將放入[僅報告模式](howto-conditional-access-report-only.md)以啟動，以便管理員可以確定它們對現有使用者的影響。 當管理員對策略應用預期感到滿意時，他們可以通過添加特定組並排除其他組來切換到**On**或暫行部署。

1. 以全域管理員、安全管理員或條件訪問管理員的身份登錄到**Azure 門戶**。
1. 流覽到**Azure 活動目錄** > **安全** > **條件訪問**。
1. 選取 [新增原則]****。
1. 為您的策略指定一個名稱。 我們建議組織為其策略的名稱創建有意義的標準。
1. 在 **"分配"** 下，選擇 **"使用者"和"組**"
   1. 在 **"包括**"下，選擇**所有使用者**。
   1. 在 **"排除"** 下，選擇 **"使用者"和"組"，** 然後選擇必須保持使用舊版身份驗證功能的任何帳戶。 至少排除一個帳戶，以防止自己被鎖定。如果不排除任何帳戶，您將無法創建此策略。
   1. 選擇 **"完成**"。
1. 在 **"雲應用"或"操作**"下，選擇 **"所有雲應用**"。
   1. 選擇 **"完成**"。
1. 在**條件** > **用戶端應用（預覽）** 下，將 **"配置"** 設置為 **"是**"。
   1. 只選中框**移動應用和桌面用戶端** > **其他用戶端**。
   1. 選擇 **"完成**"。
1. 在 **"存取控制** > **授予"** 下，選擇 **"阻止訪問**"。
   1. 選取 [選取] ****。
1. 確認設置並將 **"啟用策略"** 設置為 **"僅報告**"。
1. 選擇 **"創建**"以啟用策略。

## <a name="next-steps"></a>後續步驟

[條件訪問通用策略](concept-conditional-access-policy-common.md)

[使用僅條件訪問報告模式確定影響](howto-conditional-access-report-only.md)

[使用條件訪問"如果"工具類比登錄行為](troubleshoot-conditional-access-what-if.md)
