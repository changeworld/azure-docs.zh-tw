---
title: 在 Azure AD 權利管理中審查存取套件的存取權
description: 瞭解如何在 Azure Active Directory 存取權審查 (預覽) 中完成權利管理存取套件的存取權審查。
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: d1115c08214db19227b4b8d7be671ce4da1cf2b1
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/05/2020
ms.locfileid: "87798592"
---
# <a name="review-access-of-an-access-package-in-azure-ad-entitlement-management"></a>在 Azure AD 權利管理中審查存取套件的存取權

Azure AD 權利管理可簡化企業管理群組、應用程式和 SharePoint 網站存取的方式。 本文說明如何針對指派給存取套件的其他使用者，以指定的審核者來執行存取權審查。

## <a name="prerequisites"></a>Prerequisites

若要檢查使用者的 active access 套件指派，您必須符合進行存取權審查的必要條件：
- Azure AD Premium P2
- 全域管理員
- 指定的使用者系統管理員、目錄擁有者或存取套件管理員

如需詳細資訊，請參閱[授權需求](entitlement-management-overview.md#license-requirements)。


## <a name="open-the-access-review"></a>開啟存取權審查

使用下列步驟來尋找並開啟存取權審查：

1. 您可能會收到來自 Microsoft 的電子郵件，要求您審查存取權。 找出電子郵件以開啟存取權審查。 以下是用來審查存取權的電子郵件範例：
    
    ![存取審查檢閱者電子郵件](./media/entitlement-management-access-reviews-review-access/review-access-reviewer-email.png)

1. 按一下 [**審查使用者存取權**] 連結，以開啟存取權審查。 

1. 如果您沒有電子郵件，可以直接流覽至來尋找您的暫止存取評論 https://myaccess.microsoft.com 。   (美國政府，請改用 `https://myaccess.microsoft.us` 。 ) 

1. 按一下左側導覽列上的 [**存取評論**]，以查看指派給您的暫止存取評論清單。
    
    ![選取我的存取權上的存取評論](./media/entitlement-management-access-reviews-review-access/review-access-myaccess-select-access-review.png)

1. 按一下您想要開始的審查。
    
    ![選取存取權審查](./media/entitlement-management-access-reviews-review-access/review-access-select-access-review.png)

## <a name="perform-the-access-review"></a>執行存取權審查

一旦您開啟存取權審查，您就會看到需要檢查的使用者名稱。 有兩種方式可讓您核准或拒絕存取：
- 您可以手動核准或拒絕一或多個使用者的存取權
- 您可以接受系統建議

### <a name="manually-approve-or-deny-access-for-one-or-more-users"></a>手動核准或拒絕一或多個使用者的存取權
1. 請檢查使用者清單，並判斷哪些使用者需要繼續擁有存取權。

    ![要審查的使用者清單](./media/entitlement-management-access-reviews-review-access/review-access-list-of-users.png)

1. 若要核准或拒絕存取，請選取使用者名稱左邊的選項按鈕。

1. 在使用者名稱上方的列中，選取 [**核准**] 或 [**拒絕**]。

    ![選取使用者](./media/entitlement-management-access-reviews-review-access/review-access-select-users.png)

1. 如果您不確定，可以按一下 [**不知道**] 按鈕。

    如果您選取此選項，使用者會維持存取權，而此選項會放在 audit 記錄中。 此記錄會顯示您仍完成審核的其他任何審核者。

1. 您可能需要為您的決策提供原因。 輸入原因，然後按一下 [**提交**]。

    ![核准或拒絕存取](./media/entitlement-management-access-reviews-review-access/review-access-decision-approve.png)

1. 您可以在審核結束之前隨時變更您的決策。 若要這麼做，請從清單中選取使用者並變更決策。 例如，您可以針對先前拒絕的使用者核准存取權。

如果有多個審核者，則會記錄最後提交的回應。 請考慮一個範例，其中系統管理員會指定兩個審核者– Alice 和 Bob。 Alice 會先開啟審查並核准存取權。 在審查結束之前，Bob 會開啟審查並拒絕存取。 在此情況下，會記錄最後的拒絕存取決策。

>[!NOTE]
>如果使用者遭到拒絕存取，他們就不會立即從存取套件中移除。 當審核結束時，系統會將使用者從存取套件中移除，或管理員結束審核。

### <a name="approve-or-deny-access-using-the-system-generated-recommendations"></a>使用系統產生的建議來核准或拒絕存取

若要更快速地檢查多位使用者的存取權，您可以使用系統產生的建議，只需按一下即可接受建議。 系統會根據使用者的登入活動來產生建議。

1.  在頁面頂端的列中，按一下 [**接受建議**]。
    
    ![選取 [接受建議]](./media/entitlement-management-access-reviews-review-access/review-access-use-recommendations.png)
    
    您會看到建議動作的摘要。

1.  按一下 [**提交**] 以接受建議。

## <a name="next-steps"></a>後續步驟

- [自我審查存取套件](entitlement-management-access-reviews-self-review.md)