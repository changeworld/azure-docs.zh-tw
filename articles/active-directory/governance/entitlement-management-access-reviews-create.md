---
title: 在 Azure AD 授權管理中創建訪問包的訪問審核
description: 瞭解如何在 Azure 活動目錄訪問審核（預覽）中為授權管理訪問包創建訪問稽核原則。
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
ms.openlocfilehash: a862bbb1f574e4adab2f7d8e59a1abe8e5a5fa2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73608835"
---
# <a name="create-an-access-review-of-an-access-package-in-azure-ad-entitlement-management"></a>在 Azure AD 授權管理中創建訪問包的訪問審核

為了降低過時訪問的風險，應啟用對 Azure AD 授權管理中具有活動分配到訪問包的使用者的定期檢查。 您可以在創建新訪問包或編輯現有訪問包時啟用審核。 本文介紹如何啟用訪問包的訪問審查。

## <a name="prerequisites"></a>Prerequisites

要啟用訪問包檢查，必須滿足創建訪問包的先決條件：
- Azure AD Premium P2
- 全域管理員、使用者系統管理員、目錄擁有者或存取套件管理員

如需詳細資訊，請參閱[授權需求](entitlement-management-overview.md#license-requirements)。


## <a name="create-an-access-review-of-an-access-package"></a>創建訪問包的訪問審核

您可以在[創建新訪問包](entitlement-management-access-package-create.md)或[編輯現有訪問包](entitlement-management-access-package-lifecycle-policy.md)策略時啟用訪問審核。 按照以下步驟啟用訪問包的訪問審查：

1. 打開訪問包的**生命週期**選項卡，向下滾動到**訪問審核**。

1. 將 **"需要訪問審核**"移到"**是**"。

    ![添加訪問審核](./media/entitlement-management-access-reviews/access-reviews-pane.png)

1. 指定評論將在 **"開始"旁邊開始**的日期。

1. 接下來，將**審核頻率**設置為**每年**、**每半年**、**每季度**或**每月**。
此設置確定訪問審核發生的頻率。

1. 設置 **"持續時間"** 以定義定期系列的每個審閱將打開多少天供檢閱者輸入。 例如，您可以安排從 1 月 1 日開始並開放審核 30 天的年度審核，以便檢閱者必須到月底才能回復。

1. **如果希望**使用者執行自己的訪問審閱，請選擇 **"自我審閱**"，或者，如果要指定檢閱者，請選擇 **"特定檢閱者"。**

    ![選擇"添加檢閱者"](./media/entitlement-management-access-reviews/access-reviews-add-reviewer.png)

1. 如果選擇 **"特定檢閱者"，** 請指定哪些使用者將執行訪問審核：
    1. 選擇 **"添加檢閱者**"。
    1. 在 **"選擇檢閱者"窗格中**，搜索並選擇要成為檢閱者的使用者。
    1. 選擇檢閱者後，按一下 **"選擇"** 按鈕。

    ![指定檢閱者](./media/entitlement-management-access-reviews/access-reviews-select-reviewer.png)

1. 如果要在頁面底部編輯訪問包，請按一下 **"查看 " 創建**，如果正在創建新的訪問包或**更新**。

## <a name="view-the-status-of-the-access-review"></a>查看訪問審核的狀態

開始日期後，訪問審核將在 **"訪問審核**"部分中列出。 按照以下步驟查看訪問審核的狀態：

1. 在 **"身份治理"中**，按一下 **"訪問包**"，然後選擇具有要檢查的訪問審閱狀態的訪問包。   

1. 訪問包概覽後，按一下左側功能表上的 **"訪問評論**"。
    
    ![選擇訪問審核](./media/entitlement-management-access-reviews/access-review-status-access-package-overview.png)

1. 將顯示一個清單，其中包含具有與其關聯的訪問審核的所有策略。 按一下審核以查看其報告。

    ![訪問審核清單](./media/entitlement-management-access-reviews/access-review-status-select-access-reviews.png)
   
1. 查看報表時，它會顯示已審核的使用者數以及檢閱者對報表執行的操作。

    ![查看審核狀態](./media/entitlement-management-access-reviews/access-review-status.png)
 

## <a name="access-reviews-email-notifications"></a>訪問評論電子郵件通知
您可以指定檢閱者，也可以使用者自行查看其存取權限。 預設情況下，Azure AD 將在審閱開始後不久向檢閱者或自我檢閱者發送電子郵件。

電子郵件將包括有關如何查看訪問包存取權限的說明。 如果審核是供使用者查看其存取權限，則向他們顯示有關如何對其訪問包進行自我審閱的說明。
  
如果您已指定來賓使用者為檢閱者，並且他們尚未接受 Azure AD 來賓邀請，則他們將不會收到來自 Azure AD 訪問審核的電子郵件。 必須先接受邀請，並在收到電子郵件之前使用 Azure AD 創建帳戶。 

## <a name="next-steps"></a>後續步驟

- [查看訪問包的訪問](entitlement-management-access-reviews-review-access.md)
- [訪問包的自我審查](entitlement-management-access-reviews-self-review.md)
