---
title: 在 Azure AD 權利管理中檢查存取套件的存取權
description: 瞭解如何在 Azure Active Directory 存取權評論 (Preview) 中完成權利管理存取套件的存取權檢查。
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87798592"
---
# <a name="review-access-of-an-access-package-in-azure-ad-entitlement-management"></a>在 Azure AD 權利管理中檢查存取套件的存取權

Azure AD 權利管理可簡化企業管理群組、應用程式和 SharePoint 網站存取的方式。 本文說明如何針對指派給存取套件的其他使用者，以指定的審核者來執行存取權審核。

## <a name="prerequisites"></a>必要條件

若要檢查使用者的作用中存取套件指派，您必須符合必要條件才能進行存取權審核：
- Azure AD Premium P2
- 全域管理員
- 指定的使用者管理員、目錄擁有者或存取套件管理員

如需詳細資訊，請參閱[授權需求](entitlement-management-overview.md#license-requirements)。


## <a name="open-the-access-review"></a>開啟存取權審核

使用下列步驟來尋找並開啟存取權審核：

1. 您可能會收到 Microsoft 的電子郵件，要求您複習存取權。 找出電子郵件以開啟存取權審核。 以下是可供審查存取的範例電子郵件：
    
    ![存取審查審核者電子郵件](./media/entitlement-management-access-reviews-review-access/review-access-reviewer-email.png)

1. 按一下 [ **審核使用者存取** ] 連結以開啟存取權審核。 

1. 如果您沒有電子郵件，可以直接流覽至來尋找您的暫止存取權評論 https://myaccess.microsoft.com 。   (適用于美國政府，請 `https://myaccess.microsoft.us` 改用。 ) 

1. 按一下左側導覽列上的 [ **存取審核** ]，以查看指派給您的暫止存取評論清單。
    
    ![選取我的存取權上的存取權審核](./media/entitlement-management-access-reviews-review-access/review-access-myaccess-select-access-review.png)

1. 按一下您想要開始的評論。
    
    ![選取存取權審核](./media/entitlement-management-access-reviews-review-access/review-access-select-access-review.png)

## <a name="perform-the-access-review"></a>執行存取權審核

開啟存取權審核之後，您會看到需要檢查的使用者名稱。 您可以使用兩種方式來核准或拒絕存取：
- 您可以手動核准或拒絕一或多個使用者的存取權
- 您可以接受系統建議

### <a name="manually-approve-or-deny-access-for-one-or-more-users"></a>手動核准或拒絕一或多個使用者的存取權
1. 請檢查使用者清單，並判斷哪些使用者需要繼續存取。

    ![要檢查的使用者清單](./media/entitlement-management-access-reviews-review-access/review-access-list-of-users.png)

1. 若要核准或拒絕存取，請選取使用者名稱左邊的選項按鈕。

1. 在使用者名稱上方的橫條中選取 [ **核准** ] 或 [ **拒絕** ]。

    ![選取使用者](./media/entitlement-management-access-reviews-review-access/review-access-select-users.png)

1. 如果您不確定，可以按一下 [ **不知道** ] 按鈕。

    如果您選取此選項，使用者會維持存取權，而此選項會在 audit 記錄檔中進行。 此記錄檔會顯示您仍完成審核的其他任何審核者。

1. 您可能需要提供決策的原因。 鍵入原因，然後按一下 [ **提交**]。

    ![核准或拒絕存取](./media/entitlement-management-access-reviews-review-access/review-access-decision-approve.png)

1. 您可以在審核結束之前隨時變更您的決定。 若要這樣做，請從清單中選取使用者，並變更決策。 例如，您可以為先前拒絕的使用者核准存取權。

如果有多個審核者，則會記錄最後提交的回應。 請考慮一個範例，其中系統管理員會指定兩個審核者– Alice 和 Bob。 Alice 先開啟審核，並核准存取權。 在審核結束之前，Bob 會開啟審核並拒絕存取。 在此情況下，會記錄上次拒絕存取決策。

>[!NOTE]
>如果使用者遭到拒絕存取，就不會立即從存取套件中移除。 當審核結束時，使用者會從存取套件中移除，或在系統管理員結束審核時移除。

### <a name="approve-or-deny-access-using-the-system-generated-recommendations"></a>使用系統產生的建議來核准或拒絕存取

若要更快速地檢查多位使用者的存取權，您可以使用系統產生的建議，只要按一下就能接受建議。 系統會根據使用者的登入活動來產生建議。

1.  在頁面頂端的列中，按一下 [ **接受建議**]。
    
    ![選取接受建議](./media/entitlement-management-access-reviews-review-access/review-access-use-recommendations.png)
    
    您將會看到建議的動作摘要。

1.  按一下 [ **提交** ] 接受建議。

## <a name="next-steps"></a>後續步驟

- [存取套件的自我評論](entitlement-management-access-reviews-self-review.md)