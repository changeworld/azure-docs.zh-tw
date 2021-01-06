---
title: 什麼是條件式存取僅限報表模式？ -Azure Active Directory
description: 如何使用條件式存取原則部署來說明僅限報表模式的說明
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/01/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: deced953089fcbb2640cbf3e402db24cb511e769
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97935881"
---
# <a name="what-is-conditional-access-report-only-mode"></a>什麼是條件式存取僅限報表模式？

客戶可以廣泛使用條件式存取，以在正確的情況下套用正確的存取控制，以保持安全。 不過，在您的組織中部署條件式存取原則的其中一項挑戰，就是決定對終端使用者的影響。 可能很難預期受一般部署計畫影響的使用者數目和名稱，例如封鎖舊版驗證、要求使用者擴展的多重要素驗證，或執行登入風險原則。 

僅限報表模式是新的條件式存取原則狀態，可讓系統管理員在環境中啟用條件式存取原則之前，先評估條件式存取原則的影響。  使用僅限報表模式的版本：

- 您可以在僅限報表模式中啟用條件式存取原則。
- 在登入期間，會評估僅限報表模式中的原則，但不會強制執行。
- 結果會記錄在登入記錄詳細資料的 [ **條件式存取** ] 和 [ **僅限報表** ] 索引標籤中。
- 具有 Azure 監視器訂用帳戶的客戶可以使用條件式存取深入解析活頁簿來監視其條件式存取原則的影響。

> [!WARNING]
> 在僅限報表模式中，需要符合規範之裝置的原則，可能會提示 Mac、iOS 和 Android 上的使用者在原則評估期間選取裝置憑證，即使未強制執行裝置合規性也是一樣。 這些提示可能會重複，直到裝置符合規範。 若要防止終端使用者在登入期間收到提示，請從執行裝置合規性檢查的僅限報表原則中排除裝置平臺 Mac、iOS 和 Android。 請注意，僅限報表模式不適用於具有「使用者動作」範圍的條件式存取原則。

![Azure AD 登入記錄檔中的 [僅限報表] 索引標籤](./media/concept-conditional-access-report-only/report-only-detail-in-sign-in-log.png)

## <a name="policy-results"></a>原則結果

針對特定登入評估僅限報告模式中的原則時，有四個新的可能結果值：

| 結果 | 描述 |
| --- | --- |
| 僅報告：成功 | 已滿足所有設定的原則條件、必要的非互動式 grant 控制項和會話控制項。 例如，多重要素驗證需求是由權杖中已存在的 MFA 宣告所滿足，或在符合規範的裝置上執行裝置檢查來滿足符合規範的裝置原則。 |
| 僅報表：失敗 | 已滿足所有設定的原則條件，但不符合所有必要的非互動式 grant 控制項或會話控制。 例如，原則會套用至已設定封鎖控制項的使用者，或裝置無法符合規範的裝置原則。 |
| 僅報告：需要使用者動作 | 已滿足所有設定的原則條件，但需要使用者動作才能滿足所需的 grant 控制項或會話控制項。 使用僅限報表模式時，不會提示使用者滿足所需的控制項。 例如，系統不會提示使用者進行多重要素驗證挑戰或使用規定。   |
| 僅報表：未套用 | 未滿足所有設定的原則條件。 例如，使用者會從原則中排除，否則原則只會套用至某些受信任的命名位置。 |

## <a name="conditional-access-insights-workbook"></a>條件式存取深入解析活頁簿

系統管理員可以在僅限報表模式中建立多個原則，因此必須同時瞭解每個原則的個別影響，以及多個原則的組合影響。 新的條件式存取深入解析活頁簿可讓系統管理員將條件式存取查詢視覺化，並監視指定時間範圍、一組應用程式和使用者的原則影響。 
 
## <a name="next-steps"></a>後續步驟

[在條件式存取原則上設定僅限報表模式](howto-conditional-access-insights-reporting.md)
