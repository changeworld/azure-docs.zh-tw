---
title: 查看 Azure AD 授權管理中訪問包的訪問
description: 瞭解如何在 Azure 活動目錄訪問審核（預覽）中完成對授權管理訪問包的訪問審查。
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 11/01/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 99de022b7259b33baab3aa825673a8f85e932bff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78968734"
---
# <a name="review-access-of-an-access-package-in-azure-ad-entitlement-management"></a>查看 Azure AD 授權管理中訪問包的訪問

Azure AD 授權管理簡化了企業管理對組、應用程式和 SharePoint 網站的訪問的方式。 本文介紹如何為作為指定檢閱者分配到訪問包的其他使用者執行訪問審核。

## <a name="prerequisites"></a>Prerequisites

要查看使用者的活動訪問包分配，必須滿足執行訪問審核的先決條件：
- Azure AD Premium P2
- 全域管理員
- 指定使用者管理員、目錄擁有者或訪問包管理器

如需詳細資訊，請參閱[授權需求](entitlement-management-overview.md#license-requirements)。


## <a name="open-the-access-review"></a>打開訪問審核

使用以下步驟查找和打開訪問審核：

1. 您可能會收到來自 Microsoft 的電子郵件，要求您查看存取權限。 找到電子郵件以打開訪問審核。 下面是一個用於查看存取權限的示例電子郵件：
    
    ![訪問審閱檢閱者電子郵件](./media/entitlement-management-access-reviews-review-access/review-access-reviewer-email.png)

1. 按一下 **"查看使用者訪問**"連結以打開訪問審核。 

1. 如果您沒有該電子郵件，則可以通過直接導航到https://myaccess.microsoft.com找到掛起的訪問評論。  （對於美國政府，`https://myaccess.microsoft.us`請使用。

1. 按一下左側巡覽列上的 **"訪問審閱**"以查看分配給您掛起的訪問審核的清單。
    
    ![在"我的訪問"上選擇訪問評論](./media/entitlement-management-access-reviews-review-access/review-access-myaccess-select-access-review.png)

1. 按一下要開始的評論。
    
    ![選擇訪問審核](./media/entitlement-management-access-reviews-review-access/review-access-select-access-review.png)

## <a name="perform-the-access-review"></a>執行訪問審核

打開訪問審核後，您將看到需要查看的使用者名稱。 有兩種方法可以批准或拒絕訪問：
- 您可以手動批准或拒絕一個或多個使用者的存取權限
- 您可以接受系統建議

### <a name="manually-approve-or-deny-access-for-one-or-more-users"></a>手動批准或拒絕一個或多個使用者的存取權限
1. 查看使用者清單並確定需要哪些使用者繼續具有存取權限。

    ![要查看的使用者清單](./media/entitlement-management-access-reviews-review-access/review-access-list-of-users.png)

1. 要批准或拒絕存取權限，請選擇使用者名左側的選項按鈕。

1. 在使用者名上方的欄中選擇 **"批准**"或 **"拒絕**"。

    ![選取使用者](./media/entitlement-management-access-reviews-review-access/review-access-select-users.png)

1. 如果您不確定，可以按一下"**不知道"** 按鈕。

    如果進行此選擇，則使用者維護存取權限，並且此選擇將位於稽核記錄中。 該日誌顯示您仍已完成審閱的任何其他檢閱者。

1. 您可能需要提供決策的理由。 鍵入原因並按一下"**提交**"。

    ![核准或拒絕存取](./media/entitlement-management-access-reviews-review-access/review-access-decision-approve.png)

1. 您可以在審核結束前的任何時間更改您的決定。 為此，請從清單中選擇使用者並更改決策。 例如，您可以批准以前被拒絕的使用者的存取權限。

如果有多個檢閱者，則記錄上次提交的回應。 考慮一個管理員指定兩個檢閱者的示例 - Alice 和 Bob。 Alice 首先打開審核並批准訪問。 在審核結束之前，Bob 將打開審核並拒絕訪問。 在這種情況下，將記錄最後一個拒絕訪問決定。

>[!NOTE]
>如果使用者被拒絕訪問，則不會立即從訪問包中刪除他們。 審核結束時，使用者將從訪問包中刪除，或者管理員結束審核。

### <a name="approve-or-deny-access-using-the-system-generated-recommendations"></a>使用系統生成的建議批准或拒絕訪問

要更快地查看多個使用者的存取權限，可以使用系統生成的建議，只需按一下一下即可接受建議。 建議基於使用者的登錄活動生成。

1.  在頁面頂部的欄中，按一下"**接受建議**"。
    
    ![選擇"接受建議"](./media/entitlement-management-access-reviews-review-access/review-access-use-recommendations.png)
    
    您將看到建議操作的摘要。

1.  按一下"**提交**"以接受建議。

## <a name="next-steps"></a>後續步驟

- [訪問包的自我審查](entitlement-management-access-reviews-self-review.md)