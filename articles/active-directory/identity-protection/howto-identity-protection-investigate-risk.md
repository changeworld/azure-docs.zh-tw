---
title: 調查風險 Azure Active Directory Identity Protection
description: 瞭解如何在 Azure Active Directory Identity Protection 中調查有風險的使用者、偵測和登入
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79253477"
---
# <a name="how-to-investigate-risk"></a>如何：調查風險

Identity Protection 為組織提供三份報告，可用於調查其環境中的身分識別風險。 這些報告是有**風險的使用者**、有風險的登**入**，以及**風險**偵測。 調查事件是進一步瞭解和識別安全性策略中任何弱式要點的關鍵。

這三份報告都允許下載中的事件。CSV 格式，可進一步分析 Azure 入口網站外部。 有風險的使用者和有風險的登入報告可讓您下載最新的2500專案，而風險偵測報告則允許下載最新的5000筆記錄。

組織可以利用 Microsoft Graph 的 API 整合來匯總資料，與其他可作為組織存取權的來源。

您可以在**Azure 入口網站** > **Azure Active Directory** > **安全性**中找到這三份報告。

## <a name="navigating-the-reports"></a>導覽報表

每份報表都會以報表頂端顯示之期間的所有偵測清單來啟動。 每份報表都允許根據系統管理員喜好設定來新增或移除資料行。 系統管理員可以選擇下載中的資料。CSV 或。JSON 格式。 您可以使用報表頂端的篩選來篩選報表。

選取個別專案可能會在報表的頂端啟用其他專案，例如確認登入遭盜用或安全的功能、確認使用者是否遭到入侵，或解除使用者風險。

選取個別專案會展開偵測底下的 [詳細資料] 視窗。 詳細資料檢視可讓系統管理員在每次偵測時調查並執行動作。 

![顯示有風險登入和詳細資料的範例身分識別保護報表](./media/howto-identity-protection-investigate-risk/identity-protection-risky-sign-ins-report.png)

## <a name="risky-users"></a>具風險使用者

透過具風險使用者報告所提供的資訊，系統管理員可以找到：

- 哪些使用者有風險、已補救風險，或已解除風險？
- 偵測的詳細資料
- 所有具風險登入的歷程記錄
- 風險記錄
 
然後，系統管理員可以選擇對這些事件採取動作。 系統管理員可以選擇：

- 重設使用者密碼
- 確認使用者入侵
- 解除使用者風險
- 禁止使用者登入
- 使用 Azure ATP 進一步調查

## <a name="risky-sign-ins"></a>有風險的登入

有風險的登入報告包含最多過去30天（1個月）的可篩選資料。

利用具風險登入報告所提供的資訊，系統管理員可以找到：

- 哪些登入被分類為風險、已確認遭到入侵、已確認安全、已解除或已補救。
- 與登入嘗試相關聯的即時和匯總風險層級。
- 觸發的偵測類型
- 已套用條件式存取原則
- MFA 詳細資料
- 裝置資訊
- 應用程式資訊
- 位置資訊

然後，系統管理員可以選擇對這些事件採取動作。 系統管理員可以選擇：

- 確認登入遭入侵
- 確認登入安全

## <a name="risk-detections"></a>風險偵測

風險偵測報告包含最多過去90天（3個月）的可篩選資料。

透過風險偵測報告所提供的資訊，系統管理員可以找到：

- 每個風險偵測的相關資訊，包括類型。
- 同時觸發的其他風險
- 登入嘗試位置
- 從 Microsoft Cloud App Security （MCAS）連結至更多詳細資料。

然後，系統管理員可以選擇返回使用者的風險或登入報告，根據所搜集的資訊採取動作。

## <a name="next-steps"></a>後續步驟

- [可用來降低風險的原則](concept-identity-protection-policies.md)

- [啟用登入和使用者風險原則](howto-identity-protection-configure-risk-policies.md)
