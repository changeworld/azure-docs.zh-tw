---
title: 在 Azure AD 權利管理中建立存取套件的存取權審查
description: 瞭解如何在 Azure Active Directory 存取評論（預覽）中建立權利管理存取套件的存取權審查原則。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "73608835"
---
# <a name="create-an-access-review-of-an-access-package-in-azure-ad-entitlement-management"></a>在 Azure AD 權利管理中建立存取套件的存取權審查

若要降低過時存取的風險，您應該啟用 Azure AD 權利管理中，對存取套件具有作用中指派的使用者進行定期審查。 當您建立新的存取封裝或編輯現有的存取套件時，可以啟用審核。 本文說明如何啟用存取套件的存取權審查。

## <a name="prerequisites"></a>Prerequisites

若要啟用存取套件的審查，您必須符合建立存取套件的必要條件：
- Azure AD Premium P2
- 全域管理員、使用者系統管理員、目錄擁有者或存取套件管理員

如需詳細資訊，請參閱[授權需求](entitlement-management-overview.md#license-requirements)。


## <a name="create-an-access-review-of-an-access-package"></a>建立存取套件的存取權審查

您可以在[建立新的存取套件](entitlement-management-access-package-create.md)或[編輯現有的存取套件](entitlement-management-access-package-lifecycle-policy.md)原則時，啟用存取權審查。 請遵循下列步驟來啟用存取套件的存取權審查：

1. 開啟存取套件的 [**生命週期**] 索引標籤，並向下滾動以**存取評論**。

1. 將 [**需要存取審查**] 切換到 **[是]**。

    ![新增存取權審查](./media/entitlement-management-access-reviews/access-reviews-pane.png)

1. 指定在開始**時**，審查將開始的日期。

1. 接下來，將**審查頻率**設定為**每年** **、每年、每****季**或**每月**。
此設定會決定存取審查的頻率。

1. 設定 [**持續時間**]，以定義每次審查重複執行數列的天數，以供審核者輸入。 例如，您可以排程在1月1日開始的年度審核，並在30天內開啟以供審查，讓審核者能夠在該月結束後回應。

1. 在 [**審核者**] 旁，選取 [**自我審查**] （如果您想要讓使用者執行自己的存取權審查，或選取**特定的檢閱者**，如果您想要指定審核者的話）。

    ![選取 [新增審核者]](./media/entitlement-management-access-reviews/access-reviews-add-reviewer.png)

1. 如果您選取 **[特定檢閱者**]，請指定哪些使用者將進行存取權審查：
    1. 選取 [**新增審核者**]。
    1. 在 [**選取審核者**] 窗格中，搜尋並選取您想要成為審核者的使用者。
    1. 選取您的審核者之後，請按一下 [**選取**] 按鈕。

    ![指定審核者](./media/entitlement-management-access-reviews/access-reviews-select-reviewer.png)

1. 如果您要在頁面底部編輯存取套件，請按一下 [**檢查 + 建立**] （如果您要建立新的存取套件或**更新**）。

## <a name="view-the-status-of-the-access-review"></a>查看存取權審查的狀態

在開始日期之後，存取權審查會列在 [**存取評論**] 區段中。 請遵循下列步驟來查看存取權審查的狀態：

1. 在 [身分**識別管理**] 中，按一下 [**存取套件**]，然後選取具有您想要檢查之存取權審查狀態的存取封裝。   

1. 在存取套件總覽之後，按一下左側功能表上的 [**存取評論**]。
    
    ![選取存取權評論](./media/entitlement-management-access-reviews/access-review-status-access-package-overview.png)

1. 隨即會出現一個清單，其中包含所有具有相關存取權審查的原則。 按一下 [審查] 以查看其報表。

    ![存取審查清單](./media/entitlement-management-access-reviews/access-review-status-select-access-reviews.png)
   
1. 當您查看報表時，它會顯示審核的使用者數目，以及審核者對其採取的動作。

    ![查看審查狀態](./media/entitlement-management-access-reviews/access-review-status.png)
 

## <a name="access-reviews-email-notifications"></a>存取評論電子郵件通知
您可以指定審核者，或使用者可以自行檢查其存取權。 根據預設，Azure AD 會在評論開始之後，立即傳送電子郵件給審核者或自我審核者。

此電子郵件將包含如何審查存取封裝存取權的指示。 如果審查是讓使用者檢查其存取權，請向他們說明如何執行其存取套件的自我審查。
  
如果您已將來賓使用者指派為審核者，而且他們尚未接受其 Azure AD 來賓邀請，他們就不會收到 Azure AD 存取評論的電子郵件。 他們必須先接受邀請，並建立具有 Azure AD 的帳戶，才能接收電子郵件。 

## <a name="next-steps"></a>後續步驟

- [審查存取套件的存取權](entitlement-management-access-reviews-review-access.md)
- [自我審查存取套件](entitlement-management-access-reviews-self-review.md)
