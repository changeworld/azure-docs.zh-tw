---
title: 完成群組 & 應用程式的存取權檢查-Azure AD
description: 瞭解如何在 Azure Active Directory 存取權評論中完成群組成員或應用程式存取的存取權審核。
services: active-directory
documentationcenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 23266043d5efd65a4e6e79dd515a54a2c321b077
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87798829"
---
# <a name="complete-an-access-review-of-groups-and-applications-in-azure-ad-access-reviews"></a>在 Azure AD 存取權評論中完成群組和應用程式的存取權審核

以系統管理員身分，您可以 [建立群組或應用程式的存取權評論](create-access-review.md) ，以及審核者 [執行存取權審核](perform-access-review.md)。 本文說明如何查看存取權檢查的結果，並套用結果。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="prerequisites"></a>必要條件

- Azure AD Premium P2
- 全域管理員、使用者系統管理員、安全性系統管理員或安全性讀取者

如需詳細資訊，請參閱[授權需求](access-reviews-overview.md#license-requirements)。

## <a name="view-an-access-review"></a>查看存取權審核

當審核者完成評論時，您可以追蹤進度。

1. 登入 Azure 入口網站，然後開啟 [身分 [識別管理] 頁面](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)。

1. 在左側功能表中，按一下 [ **存取審核**]。

1. 在清單中，按一下存取權審核。

    若要查看一系列的存取權評論，請流覽至存取權評論，您將會在已排程的評論中找到即將發生的專案。

    在 [ **總覽** ] 頁面上，您可以看到進度。 在完成檢閱之前，不會變更目錄中的任何存取權限。

    ![存取評論進度](./media/complete-access-review/overview-progress.png)

1. 如果您想要在到達排程結束日期之前停止存取權審核，請按一下 [ **停止** ] 按鈕。

    停止審查時，審核者將無法再提供回應。 檢閱停止後即無法重新開始。

1. 如果您對存取權檢查不再感興趣，可以按一下 [ **刪除** ] 按鈕將它刪除。

## <a name="apply-the-changes"></a>套用變更

如果已啟用 [ **自動將結果套用至資源** ]，並根據您在 **完成設定**時所做的選擇，自動套用將會在審核的結束日期之後或您手動停止審核之後執行。

如果審核未啟用 [ **自動將結果套用至資源** ]， **請按一下 [** 套用] 以手動套用變更。 如果審核中的使用者存取遭到 **拒絕，當您按一下 [** 套用] 時，Azure AD 會移除其成員資格或應用程式指派。

![套用存取權檢查變更](./media/complete-access-review/apply-changes.png)

審核的狀態將會從 [已完成]**變更為 [** **已完成**]，例如 [套用] 和 [最後] 以套用狀態**結果**。 您應該會看到遭到拒絕的使用者 (若有的話)，在幾分鐘內從群組成員資格或應用程式指派中移除。

已設定的自動套用檢閱，或選取 [套用]**** 並不會影響源自內部部署目錄中的群組，或動態群組。 如果您想要變更源自於內部部署的群組，請下載結果，並將那些變更套用至該目錄中的群組圖像。

## <a name="retrieve-the-results"></a>擷取結果

若要查看單次存取檢查的結果，請按一下 [ **結果** ] 頁面。 若要只查看使用者的存取權，請在 [搜尋] 方塊中，輸入已審核其存取權之使用者的顯示名稱或使用者主體名稱。

![取得存取權檢查的結果](./media/complete-access-review/retrieve-results.png)

若要查看重複使用中存取審核的進度，請按一下 [ **結果** ] 頁面。

若要查看已完成的存取權審核實例的結果，請按一下 [ **審核歷程記錄**]，然後根據實例的開始和結束日期，從完成的存取權審核實例清單中選取特定的實例。 您可以從 [ **結果** ] 頁面取得這個實例的結果。

若要取得存取權審核的所有結果，請按一下 [ **下載** ] 按鈕。 可在 Excel 中，或開啟 UTF-8 編碼 CSV 檔案的其他程式中檢視產生的 CSV 檔案。

## <a name="remove-users-from-an-access-review"></a>從存取權審核中移除使用者

 根據預設，已刪除的使用者會在 Azure AD 中以已刪除的狀態保留 30 天，在這段期間內，系統管理員可以視需要加以還原。  經過 30 天後，該使用者將永久刪除。  此外，全域管理員可以使用 Azure Active Directory 入口網站，在該時段結束之前明確地[永久刪除最近刪除的使用者](../fundamentals/active-directory-users-restore.md)。  使用者永久刪除後，關於該使用者的資料將會從作用中的存取權檢閱中移除。  與已刪除的使用者有關的稽核資訊仍會保留在稽核記錄中。

## <a name="next-steps"></a>後續步驟

- [使用 Azure AD 存取權檢閱來管理使用者存取權](manage-user-access-with-access-reviews.md)
- [使用 Azure AD 存取權檢閱來管理來賓存取權](manage-guest-access-with-access-reviews.md)
- [建立群組或應用程式的存取權檢閱](create-access-review.md)
- [在 Azure AD 管理角色中建立使用者的存取權檢閱](../privileged-identity-management/pim-how-to-start-security-review.md)
