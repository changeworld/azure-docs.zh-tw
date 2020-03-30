---
title: 什麼是"僅條件訪問"報表模式？ - Azure 活動目錄
description: 如何使用條件訪問策略部署提供僅報告模式説明
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd41e79a1e08c57e806f6ada32faccfa5fdf5792
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295292"
---
# <a name="what-is-conditional-access-report-only-mode"></a>什麼是"僅條件訪問"報表模式？

我們的客戶廣泛使用條件訪問，通過在適當的環境中應用正確的存取控制來保持安全。 但是，在組織中部署條件訪問策略的挑戰之一是確定對最終使用者的影響。 可能很難預測受常見部署計畫（如阻止舊版身份驗證、需要對使用者群進行多重要素驗證）或實施登錄風險策略等常見部署計畫影響的使用者的數量和名稱。 

僅報告模式是一種新的條件訪問策略狀態，允許管理員在在其環境中啟用條件訪問策略之前評估其影響。  發佈僅報告模式時：

- 條件訪問策略可以在僅報告模式下啟用。
- 在登錄期間，將評估報表模式下的策略，但不會強制執行。
- 結果記錄在"登錄日誌詳細資訊**的條件訪問**"和 **"僅報告"（預覽）** 選項卡中。
- 具有 Azure 監視器訂閱的客戶可以使用條件訪問見解活頁簿監視其條件訪問策略的影響。

> [!WARNING]
> 需要相容設備的僅報告模式下的策略可能會提示 Mac、iOS 和 Android 上的使用者在策略評估期間選擇設備證書，即使未強制執行設備合規性。 這些提示可能會重複，直到設備符合要求。 為防止最終使用者在登錄期間收到提示，請從執行設備合規性檢查的僅報告策略中排除設備平臺 Mac、iOS 和 Android。

![Azure AD 登錄日誌中的僅報告選項卡](./media/concept-conditional-access-report-only/report-only-detail-in-sign-in-log.png)

## <a name="policy-results"></a>原則結果

當為給定登錄計算僅報告模式下的策略時，有四個新的可能結果值：

| 結果 | 描述 |
| --- | --- |
| 僅限報告：成功 | 滿足所有配置的策略條件、所需的非互動式授予控制項和會話控制項。 例如，權杖中已存在的 MFA 聲明滿足了多重要素驗證要求，或者通過在相容設備上執行設備檢查來滿足符合的設備策略。 |
| 僅報告：失敗 | 滿足所有配置的策略條件，但並非所有必需的非互動式授予控制項或會話控制項都得到滿足。 例如，策略應用於配置塊控制項或設備失敗符合的設備策略的使用者。 |
| 僅報告：需要使用者操作 | 滿足所有配置的策略條件，但需要使用者操作才能滿足所需的授予控制項或會話控制項。 使用僅報告模式時，不會提示使用者滿足所需的控制項。 例如，不會提示使用者出現多重要素驗證挑戰或使用條款。   |
| 僅報告：未應用 | 並非所有配置的策略條件都得到滿足。 例如，使用者從策略中排除，或者該策略僅適用于某些受信任的命名位置。 |

## <a name="conditional-access-insights-workbook"></a>條件訪問見解活頁簿

管理員能夠在僅報告模式下創建多個策略，因此必須瞭解每個策略的個體影響以及同時評估的多個策略的綜合影響。 新的條件訪問見解活頁簿使管理員能夠視覺化條件訪問查詢，並監視給定時間範圍、應用程式集和使用者的策略的影響。 
 
## <a name="next-steps"></a>後續步驟

[在條件訪問策略上配置僅報告模式](howto-conditional-access-report-only.md)
