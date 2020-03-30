---
title: 完成對應用程式&組的訪問審查 - Azure AD
description: 瞭解如何在 Azure 活動目錄訪問審核中完成組成員或應用程式訪問的訪問審核。
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 07/23/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 266234f2872cfe99509d564c9460bfba4a0e2bf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75932537"
---
# <a name="complete-an-access-review-of-groups-and-applications-in-azure-ad-access-reviews"></a>在 Azure AD 訪問審核中完成對組和應用程式的訪問審查

作為管理員，您可以[創建組或應用程式的訪問審閱](create-access-review.md)，檢閱者[執行訪問審閱](perform-access-review.md)。 本文介紹如何查看訪問審核的結果並應用結果。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="prerequisites"></a>Prerequisites

- Azure AD Premium P2
- 全域管理員、使用者管理員、安全管理員或安全讀取器

如需詳細資訊，請參閱[授權需求](access-reviews-overview.md#license-requirements)。

## <a name="view-an-access-review"></a>查看訪問審核

您可以在檢閱者完成審核時跟蹤進度。

1. 登錄到 Azure 門戶並打開[標識治理頁](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)。

1. 在左側功能表中，按一下"**訪問評論**"。

1. 在清單中，按一下訪問審閱。

    要查看一系列訪問審核，請查看訪問審核，您將在"計畫審核"中查找即將出現的事件。

    在 **"概述"** 頁上，您可以看到進度。 在完成檢閱之前，不會變更目錄中的任何存取權限。

    ![訪問審核進度](./media/complete-access-review/overview-progress.png)

1. 如果要在訪問審核到達計畫結束日期之前停止，請按一下"**停止"** 按鈕。

    停止審閱時，檢閱者將無法再給出回應。 檢閱停止後即無法重新開始。

1. 如果您不再對訪問審核感興趣，可以通過按一下"刪除"按鈕將其**刪除**。

## <a name="apply-the-changes"></a>套用變更

如果**啟用了"自動將結果應用於資源**"，並且基於您在**完成設置**中的選擇，則自動應用將在審核結束日期之後或手動停止審核時執行。

如果未為審核啟用 **"自動將結果應用於資源**"，請按一下"**應用"** 以手動應用更改。 如果在審核中拒絕使用者的存取權限，則當您按一下 **"應用"** 時，Azure AD 將刪除其成員資格或應用程式分配。

![應用訪問審核更改](./media/complete-access-review/apply-changes.png)

審核的狀態將從 **"已完成"** 更改為中間狀態，如 **"應用**"，最後更改為 **"應用結果**"。 您應該會看到遭到拒絕的使用者 (若有的話)，在幾分鐘內從群組成員資格或應用程式指派中移除。

已設定的自動套用檢閱，或選取 [套用]**** 並不會影響源自內部部署目錄中的群組，或動態群組。 如果您想要變更源自於內部部署的群組，請下載結果，並將那些變更套用至該目錄中的群組圖像。

## <a name="retrieve-the-results"></a>擷取結果

要查看一次性訪問審核的結果，請按一下"**結果**"頁。 要僅查看使用者的存取權限，在"搜索"框中，鍵入已查看其存取權限的使用者的顯示名稱或使用者主體名稱。

![檢索訪問審核的結果](./media/complete-access-review/retrieve-results.png)

要查看定期活動訪問審核的進度，請按一下"**結果**"頁。

要查看重複進行的訪問審核已完成實例的結果，請按一下 **"審閱歷史記錄**"，然後根據實例的開始日期和結束日期從已完成的訪問審閱實例清單中選擇特定實例。 可以從 **"結果"** 頁獲取此實例的結果。

要檢索訪問審核的所有結果，請按一下"**下載**"按鈕。 可在 Excel 中，或開啟 UTF-8 編碼 CSV 檔案的其他程式中檢視產生的 CSV 檔案。

## <a name="remove-users-from-an-access-review"></a>將使用者從訪問審核中刪除

 根據預設，已刪除的使用者會在 Azure AD 中以已刪除的狀態保留 30 天，在這段期間內，系統管理員可以視需要加以還原。  經過 30 天後，該使用者將永久刪除。  此外，全域管理員可以使用 Azure Active Directory 入口網站，在該時段結束之前明確地[永久刪除最近刪除的使用者](../fundamentals/active-directory-users-restore.md)。  使用者永久刪除後，關於該使用者的資料將會從作用中的存取權檢閱中移除。  與已刪除的使用者有關的稽核資訊仍會保留在稽核記錄中。

## <a name="next-steps"></a>後續步驟

- [使用 Azure AD 存取權檢閱來管理使用者存取權](manage-user-access-with-access-reviews.md)
- [使用 Azure AD 存取權檢閱來管理來賓存取權](manage-guest-access-with-access-reviews.md)
- [創建組或應用程式的訪問審核](create-access-review.md)
- [在 Azure AD 管理角色中建立使用者的存取權檢閱](../privileged-identity-management/pim-how-to-start-security-review.md)
