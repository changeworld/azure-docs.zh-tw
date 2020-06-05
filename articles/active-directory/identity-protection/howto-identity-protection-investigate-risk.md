---
title: 調查 Azure Active Directory Identity Protection 風險
description: 了解如何在 Azure Active Directory Identity Protection 中調查有風險的使用者、偵測和登入
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
ms.openlocfilehash: 23105a78e2ba34cf6a45f749ebfdd6b034ebf0dc
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680154"
---
# <a name="how-to-investigate-risk"></a>作法：調查風險

Identity Protection 會提供組織三種報告，用於調查其環境中的身分識別風險。 分別為**風險性使用者**、**風險性登入**及**風險偵測**報告。 事件調查是深入了解並找出任何安全性策略弱點的關鍵。

這三種報告皆能以 .CSV 格式下載事件，以供在 Azure 入口網站外部進一步分析。 風險性使用者和風險性登入報告允許下載最近的 2500 個項目，而風險偵測報告則允許下載最近的 5000 筆記錄。

組織可以利用 Microsoft Graph 的 API 整合，將資料和組織具有存取權限的其他來源匯總在一起。

可在 [Azure 入口網站]  >  [Azure Active Directory]  >  [安全性] 取得這三種報告。

## <a name="navigating-the-reports"></a>瀏覽報告

每個報告啟動時，報告頂端都會顯示該段期間內所有偵測的清單。 每個報告都能依管理員偏好新增或移除資料行。 管理員可以選擇以 .CSV 或 .JSON 格式下載資料。 可使用橫跨報告頂端的篩選條件來篩選報告。

選取個別項目時，可能會啟用報告頂端的其他項目，例如確認登入是否遭到入侵抑或保持安全、確認使用者是否遭到入侵，或解除使用者風險的功能。

選取個別項目後，會展開偵測下方的詳細資料視窗。 管理員可透過詳細資料檢視，對每次偵測進行審查和執行動作。 

![顯示風險性登入與詳細資料的 Identity Protection 報告範例](./media/howto-identity-protection-investigate-risk/identity-protection-risky-sign-ins-report.png)

## <a name="risky-users"></a>具風險使用者

透過風險性使用者報告提供的資訊，管理員可了解以下項目：

- 哪些使用者有風險、其風險已補救，或其風險已解除？
- 偵測的詳細資料
- 所有風險性登入的歷程
- 風險記錄
 
接著，管理員可以選擇對這些事件採取動作。 管理員可以選擇：

- 重設使用者密碼
- 確認使用者遭入侵
- 解除使用者風險
- 阻止使用者登入
- 使用 Azure ATP 進一步調查

## <a name="risky-sign-ins"></a>有風險的登入

風險性登入報告包含最多過去 30 天 (1 個月) 的可篩選資料。

透過風險性登入報告提供的資訊，管理員可了解以下項目：

- 哪些登入被分類為有風險、確認遭到入侵、確認安全、已解除或已補救。
- 與登入嘗試相關聯的即時風險等級和彙總風險等級。
- 觸發的偵測類型
- 套用的條件式存取原則
- MFA 詳細資料
- 裝置資訊
- 應用程式資訊
- 位置資訊

接著，管理員可以選擇對這些事件採取動作。 管理員可以選擇：

- 確認登入遭入侵
- 確認登入安全

> [!NOTE] 
> Identity Protection 會評估所有驗證流程的風險，無論是互動式或非互動式。 不過，登入報告只會顯示互動式登入。您可能會看到非互動式登入時發生的風險性登入，但該次登入不會顯示在 Azure AD 登入報告中。

## <a name="risk-detections"></a>風險偵測

風險偵測報告包含最多過去 90 天 (3 個月) 的可篩選資料。

透過風險偵測報告提供的資訊，管理員可了解以下項目：

- 每項風險偵測的相關資訊，包括類型。
- 同時觸發的其他風險
- 登入嘗試位置
- 從 Microsoft Cloud App Security (MCAS) 取得更多詳細資料的連結。

然後，管理員可以選擇返回使用者的風險或登入報告，根據搜集到的資訊採取動作。

## <a name="next-steps"></a>後續步驟

- [可用來降低風險的原則](concept-identity-protection-policies.md)

- [啟用登入與使用者風險原則](howto-identity-protection-configure-risk-policies.md)
