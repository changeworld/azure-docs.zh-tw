---
title: 什麼是條件式存取報告專用模式？ -Azure Active Directory
description: 如何在條件式存取原則部署中使用僅報告模式的協助
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75880f4c533a503852d62ff940e53d4bcc30d218
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2020
ms.locfileid: "77186109"
---
# <a name="what-is-conditional-access-report-only-mode"></a>什麼是條件式存取報告專用模式？

我們的客戶廣泛使用條件式存取，在適當的情況下套用適當的存取控制，以保持安全。 不過，在您的組織中部署條件式存取原則的其中一項挑戰，就是要決定對使用者的影響。 可能會難以預期受一般部署計畫影響的使用者數目和名稱，例如封鎖舊版驗證、要求使用者擴展的多重要素驗證，或執行登入風險原則。 

僅限報告模式是新的條件式存取原則狀態，可讓系統管理員在其環境中啟用條件式存取原則之前，先評估其影響。  使用僅限報表模式的版本：

- 條件式存取原則可以在僅限報表模式中啟用。
- 在登入期間，會評估僅限報表模式中的原則，但不會強制執行。
- 結果會記錄在登入記錄詳細資料的 [**條件式存取**] 和 [**僅報告（預覽）** ] 索引標籤中。
- 具有 Azure 監視器訂用帳戶的客戶可以使用條件式存取深入解析活頁簿，來監視其條件式存取原則的影響。

> [!WARNING]
> 在僅限報表模式中，需要相容裝置的原則可能會在原則評估期間提示使用者在 Mac、iOS 和 Android 上選取裝置憑證，即使不強制執行裝置合規性也一樣。 這些提示可能會重複，直到裝置符合規範為止。 若要防止終端使用者在登入期間收到提示，請從執行裝置合規性檢查的僅限報告原則中排除裝置平臺 Mac、iOS 和 Android。

![Azure AD 登入記錄中的僅限報告索引標籤](./media/concept-conditional-access-report-only/report-only-detail-in-sign-in-log.png)

## <a name="policy-results"></a>原則結果

針對指定的登入評估僅限報告模式的原則時，有四個新的可能結果值：

| 結果 | 描述 |
| --- | --- |
| 僅限報告：成功 | 已滿足所有已設定的原則條件、必要的非互動式授與控制和會話控制。 例如，已存在於權杖中的 MFA 宣告會滿足多重要素驗證需求，或在符合規範的裝置上執行裝置檢查，滿足相容的裝置原則。 |
| 僅限報告：失敗 | 已滿足所有已設定的原則條件，但未滿足所有必要的非互動式授與控制或會話控制項。 例如，原則會套用至已設定封鎖控制項的使用者，或裝置未通過相容的裝置原則。 |
| 僅限報告：需要使用者動作 | 已滿足所有已設定的原則條件，但需要使用者動作以滿足必要的授與控制或會話控制項。 使用僅限報表模式時，系統不會提示使用者滿足所需的控制項。 例如，使用者不會收到多重要素驗證挑戰或使用規定的提示。   |
| 僅限報表：未套用 | 未滿足所有設定的原則條件。 例如，會從原則中排除使用者，否則原則只會套用至特定信任的命名位置。 |

## <a name="conditional-access-insights-workbook"></a>條件式存取深入解析活頁簿

系統管理員可以在僅限報表模式中建立多個原則，因此必須同時瞭解每個原則的個別影響，以及多個原則評估的組合影響。 新的條件式存取深入解析活頁簿可讓系統管理員將條件式存取查詢視覺化，並監視指定時間範圍、一組應用程式和使用者的原則影響。 
 
## <a name="next-steps"></a>後續步驟

[在條件式存取原則上設定僅限報告模式](howto-conditional-access-report-only.md)
