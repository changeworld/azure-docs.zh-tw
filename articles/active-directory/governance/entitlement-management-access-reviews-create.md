---
title: 在 Azure AD 權利管理中建立存取套件的存取權審核
description: 瞭解如何在 Azure Active Directory 存取權審核 (預覽) 中，為權利管理存取套件建立存取權稽核原則。
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
ms.openlocfilehash: 012eefe9140703a62d7bb1074ab763191a0976cb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87798506"
---
# <a name="create-an-access-review-of-an-access-package-in-azure-ad-entitlement-management"></a>在 Azure AD 權利管理中建立存取套件的存取權審核

若要降低過時存取的風險，您應該啟用 Azure AD 權利管理中，對存取套件進行使用中指派的使用者定期評論。 當您建立新的存取套件或編輯現有的存取套件時，可以啟用評論。 本文說明如何啟用存取套件的存取權審核。

## <a name="prerequisites"></a>必要條件

若要啟用存取套件的評論，您必須符合建立存取套件的必要條件：
- Azure AD Premium P2
- 全域管理員、使用者系統管理員、目錄擁有者或存取套件管理員

如需詳細資訊，請參閱[授權需求](entitlement-management-overview.md#license-requirements)。


## <a name="create-an-access-review-of-an-access-package"></a>建立存取套件的存取權審核

您可以在 [建立新的存取套件](entitlement-management-access-package-create.md) 或 [編輯現有的存取套件](entitlement-management-access-package-lifecycle-policy.md) 原則時啟用存取權審核。 遵循下列步驟來啟用存取套件的存取權審核：

1. 開啟存取套件的 [ **生命週期** ] 索引標籤，並向下滾動以 **存取評論**。

1. 將 [ **需要存取權審核** ] 切換為 **[是]**。

    ![新增存取權審核](./media/entitlement-management-access-reviews/access-reviews-pane.png)

1. 指定 **開始進行審核**的日期。

1. 接下來，將 **審核頻率** 設定為 **每年**、 **每年、** 每季、每 **季** 或 **每月**。
此設定會決定存取權審查的頻率。

1. 設定 **持續時間** ，以定義將針對審核者的輸入開啟每個週期性系列評論的天數。 例如，您可能會排程在1月1日開始的年度評論，並在30天內開啟以供審查，讓審核者可以在該月結束之前回復。

1. 如果您想要指定審核者，請選取 [ **審核者**] 旁的 [ **自** 定義]，如果您想要讓使用者執行自己的存取權審核，或選取 **特定的審核者 (s) ** 。

    ![選取 [新增審核者]](./media/entitlement-management-access-reviews/access-reviews-add-reviewer.png)

1. 如果您選取了 **特定的審核者 (s) **，請指定哪些使用者將執行存取權審核：
    1. 選取 [ **新增審核者**]。
    1. 在 [ **選取審核者** ] 窗格中，搜尋並選取您想要成為審核者) 的使用者 (s。
    1. 當您選取了檢閱者 (s) ，請按一下 [ **選取** ] 按鈕。

    ![指定審核者](./media/entitlement-management-access-reviews/access-reviews-select-reviewer.png)

1. 如果您正在建立新的存取套件，請按一下 [ **審核 + 建立** ] **; 如果您** 正在編輯存取套件，請在頁面底部按一下。

## <a name="view-the-status-of-the-access-review"></a>查看存取權審核的狀態

開始日期之後，存取權審核將會列在 [ **存取審核** ] 區段中。 遵循下列步驟來查看存取權審核的狀態：

1. 在 [身分 **識別管理**] 中，按一下 [ **存取套件** ]，然後選取具有您想要檢查之存取權檢查狀態的存取套件。   

1. 當您在存取套件總覽之後，請按一下左側功能表上的 [ **存取審核** ]。
    
    ![選取存取權評論](./media/entitlement-management-access-reviews/access-review-status-access-package-overview.png)

1. 將會出現清單，其中包含所有具有相關存取權審核的原則。 按一下審核以查看其報表。

    ![存取權評論清單](./media/entitlement-management-access-reviews/access-review-status-select-access-reviews.png)
   
1. 當您查看報表時，它會顯示已審核的使用者數目，以及審核者所採取的動作。

    ![查看審核狀態](./media/entitlement-management-access-reviews/access-review-status.png)
 

## <a name="access-reviews-email-notifications"></a>存取評論電子郵件通知
您可以指定審核者，或使用者可以自行檢查其存取權。 依預設，Azure AD 會在評論開始之後，立即將電子郵件傳送給審核者或自我審核者。

此電子郵件將包含如何檢查存取套件存取權的指示。 如果審查是讓使用者檢查其存取權，請向他們說明如何執行其存取套件的自我檢查。
  
如果您已將來賓使用者指派為審核者，但他們尚未接受其 Azure AD 來賓邀請，他們將不會收到 Azure AD 存取評論的電子郵件。 他們必須先接受邀請並建立具有 Azure AD 的帳戶，才可以接收電子郵件。 

## <a name="next-steps"></a>後續步驟

- [檢查存取套件的存取權](entitlement-management-access-reviews-review-access.md)
- [存取套件的自我評論](entitlement-management-access-reviews-self-review.md)
