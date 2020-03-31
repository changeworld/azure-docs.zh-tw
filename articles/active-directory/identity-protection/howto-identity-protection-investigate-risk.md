---
title: 調查 Azure 活動目錄標識保護的風險
description: 瞭解如何在 Azure 活動目錄標識保護中調查風險使用者、檢測和登錄
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5191f05752c177aa29d121abe9d1aa29fde265a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253477"
---
# <a name="how-to-investigate-risk"></a>如何：調查風險

身份保護為組織提供了三個報告，它們可用於調查其環境中的身份風險。 這些報告是**風險使用者**、**風險登錄**和**風險檢測**。 調查事件是更好地瞭解和識別安全性原則中的任何弱點的關鍵。

所有三個報告都允許在 中下載事件。用於 Azure 門戶外部進一步分析的 CSV 格式。 風險使用者和風險登錄報告允許下載最近的 2500 個條目，而風險檢測報告允許下載最近的 5000 條記錄。

組織可以利用 Microsoft Graph API 集成將資料與組織中可能有權訪問的其他源聚合。

這三個報告在**Azure 門戶** > **Azure 活動目錄** > **安全性**中找到。

## <a name="navigating-the-reports"></a>導航報表

每個報告都會在報告頂部顯示的期間的所有檢測的清單啟動。 每個報表都允許根據管理員首選項添加或刪除列。 管理員可以選擇在 中下載資料。CSV 或 。JSON 格式。 可以使用報表頂部的篩選器篩選報表。

選擇單個條目可能會啟用報表頂部的其他條目，例如確認登錄是否受損害或安全、確認使用者已受損或消除使用者風險的能力。

選擇單個條目會擴展檢測下方的詳細資訊視窗。 詳細資訊視圖允許管理員調查並執行每個檢測的操作。 

![顯示危險登錄和詳細資訊的標識保護報告示例](./media/howto-identity-protection-investigate-risk/identity-protection-risky-sign-ins-report.png)

## <a name="risky-users"></a>具風險使用者

通過風險使用者報告提供的資訊，管理員可以找到：

- 哪些使用者面臨風險、已補救風險或已消除風險？
- 有關檢測的詳細資訊
- 所有風險登錄的歷史
- 風險記錄
 
然後，管理員可以選擇對這些事件執行操作。 管理員可以選擇：

- 重置使用者密碼
- 確認使用者危害
- 解除使用者風險
- 阻止使用者登錄
- 使用 Azure ATP 進一步調查

## <a name="risky-sign-ins"></a>有風險的登入

風險登錄報告包含長達 30 天（1 個月）的可篩選資料。

通過風險登錄報告提供的資訊，管理員可以找到：

- 哪些登錄被歸類為有風險、已確認洩露、確認安全、已排除或修復。
- 與登錄嘗試關聯的即時和聚合風險級別。
- 觸發檢測類型
- 應用條件訪問策略
- MFA 詳細資訊
- 裝置資訊
- 應用程式資訊
- 位置資訊

然後，管理員可以選擇對這些事件執行操作。 管理員可以選擇：

- 確認登錄折中方案
- 確認登錄安全

## <a name="risk-detections"></a>風險偵測

風險檢測報告包含長達 90 天（3 個月）的可篩選資料。

通過風險檢測報告提供的資訊，管理員可以找到：

- 有關每個風險檢測（包括類型）的資訊。
- 同時觸發的其他風險
- 登錄嘗試位置
- 連結到微軟雲應用安全 （MCAS） 的更多詳細資訊。

然後，管理員可以選擇返回到使用者的風險或登錄報告，以便根據收集到的資訊執行操作。

## <a name="next-steps"></a>後續步驟

- [可用來降低風險的原則](concept-identity-protection-policies.md)

- [啟用登錄和使用者風險策略](howto-identity-protection-configure-risk-policies.md)
