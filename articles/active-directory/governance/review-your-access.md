---
title: 在存取權審查中審查您對群組 & 應用程式的存取權-Azure AD
description: 瞭解如何在 Azure Active Directory 存取評論中，檢查您自己對群組或應用程式的存取權。
services: active-directory
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/17/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 33d946c47a17bb537c7644937547ad479b4637e5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85077927"
---
# <a name="review-access-for-yourself-to-groups-or-applications-in-azure-ad-access-reviews"></a>在 Azure AD 存取評論中，對群組或應用程式的存取權進行審核

Azure Active Directory （Azure AD）藉由稱為 Azure AD 存取評論的功能，簡化了企業管理 Azure AD 和其他 Microsoft 線上服務中群組或應用程式存取權的方式。

本文說明如何檢查您自己對群組或應用程式的存取權。

## <a name="review-your-access-using-my-apps"></a>使用我的應用程式檢查您的存取權

執行存取權審查的第一個步驟是尋找並開啟存取權審查。

>[!IMPORTANT]
> 接收電子郵件時可能會有延遲，而在某些情況下，可能需要最多24小時的時間。 加入白名單，確保 azure-noreply@microsoft.com 您收到所有電子郵件。

1. 尋找 Microsoft 的電子郵件，要求您審查存取權。 以下是用來審查群組存取權的電子郵件範例。

    ![Microsoft 提供的電子郵件範例，用以審查您對群組的存取權](./media/review-your-access/access-review-email.png)

1. 按一下 [**審查存取**] 連結以開啟存取權審查。

如果您沒有電子郵件，您可以遵循下列步驟來尋找您的暫止存取審查。

1. 登入我的應用程式入口網站，網址為 [https://myapps.microsoft.com](https://myapps.microsoft.com) 。

    ![我的應用程式入口網站列出您有許可權的應用程式](./media/review-your-access/myapps-access-panel.png)

1. 在頁面右上角按一下使用者符號，其中顯示您的名稱和預設組織。 如果列出多個組織，請選取已要求存取權檢閱的組織。

1. 在頁面右側，按一下 [**存取評論**] 磚，以查看暫止的存取評論清單。

    如果看不到磚，則沒有針對該組織執行的存取權檢閱，此時也不需要採取任何動作。

    ![應用程式和群組的暫止存取審查清單](./media/review-your-access/access-reviews-list.png)

1. 按一下 [**開始審查**] 連結，以取得您想要執行的存取權審查。

### <a name="perform-the-access-review"></a>執行存取權審查

開啟存取權審查之後，您就可以看到您的存取權。

1. 檢查您的存取權，並決定您是否仍需要存取權。

    如果要求要檢查其他人的存取權，頁面看起來會不同。 如需詳細資訊，請參閱[審查群組或應用程式的存取權](perform-access-review.md)。

    ![開啟存取權審查，詢問您是否仍需要存取群組](./media/review-your-access/perform-access-review.png)

1. 按一下 **[是]** 以保留您的存取權，或按一下 [**否**] 移除您的存取權。

1. 如果您按一下 [**是]**，您可能需要在 [**原因**] 方塊中指定理由。

    ![完整的存取權審查，詢問您是否仍需要群組的存取權](./media/review-your-access/perform-access-review-submit.png)

1. 按一下 [提交] 。

    您的選擇隨即提交，並返回我的應用程式入口網站。

    如果您想要變更您的回應，請重新開啟 [存取評論] 頁面，並更新您的回應。 您可以隨時變更您的回應，直到存取權審查結束為止。

    > [!NOTE]
    > 如果您指出不再需要存取權，就不會立即移除。 當審核結束或系統管理員停止審核時，會將您移除。

## <a name="review-your-own-access-using-my-access-new"></a>使用我的存取權檢查您自己的存取權（新的）

您可以透過以下幾種不同的方式，嘗試更新的使用者我的存取權介面的新體驗：

### <a name="my-apps-portal"></a>我的應用程式入口網站

1. 登入我的應用程式入口網站，網址為 [https://myapps.microsoft.com](https://myapps.microsoft.com) 。

    ![我的應用程式入口網站列出您有許可權的應用程式](./media/review-your-access/myapps-access-panel.png)

2. 按一下 [**存取評論**] 磚，以查看暫止的存取評論清單。

    > [!NOTE]
    > 如果看不到 [**存取評論**] 磚，就不會對該組織執行任何存取權審查，此時不需要採取任何動作。

3. 按一下 [**試用]！** 在頁面頂端的橫幅中，前往新的我的存取權體驗。

    ![應用程式和群組的暫止存取審查清單，其中包含預覽期間顯示的可用新體驗橫幅](./media/review-your-access/banner-your-access.png)

4. 在**執行存取權審查**一節中繼續進行

### <a name="email"></a>電子郵件

>[!IMPORTANT]
> 接收電子郵件時可能會有延遲，而在某些情況下，可能需要最多24小時的時間。 加入白名單，確保 azure-noreply@microsoft.com 您收到所有電子郵件。

1. 尋找來自 Microsoft 的電子郵件，要求您審查存取權。 您可以看到以下的範例電子郵件訊息：

 ![Microsoft 的電子郵件範例，用以審查群組的存取權](./media/review-your-access/access-review-email-preview.png)

2. 按一下 [**審查存取**] 連結以開啟存取權審查。

3. 在**執行存取權審查**一節中繼續進行

>[!NOTE]
>如果按一下 [開始審核]，會將您帶到**我的應用程式**遵循上述**我的應用程式入口網站**一節中所列的步驟。

### <a name="directly-at-my-access"></a>直接在我的存取權

您也可以使用瀏覽器開啟我的存取權，以查看您的暫止存取評論。

1. 登入我的存取權，網址為https://myaccess.microsoft.com/

2. 從左側列的功能表中選取 [**存取評論**]，以查看指派給您的暫止存取評論清單。

   ![存取功能表中的評論](./media/review-your-access/access-review-menu.png)

### <a name="perform-the-access-review"></a>執行存取權審查

1. 在 [群組和應用程式] 底下，您可以看到：
    
    - **名稱**存取權檢查的名稱。
    - **逾期**審查的到期日。 拒絕此日期之後，就可以從所要審核的群組或應用程式中移除使用者。
    - **資源**[審查] 下的資源名稱。
    - **進度**在此存取權審查中，經過使用者總數審查的使用者人數。
    
2. 按一下存取權審查的名稱即可開始使用。

   ![應用程式和群組的暫止存取審查清單](./media/review-your-access/access-reviews-list-preview.png)

3. 檢查您的存取權，並決定您是否仍需要存取權。

    如果要求要檢查其他人的存取權，頁面看起來會不同。 如需詳細資訊，請參閱[審查群組或應用程式的存取權](perform-access-review.md)。

    ![開啟存取權審查，詢問您是否仍需要存取群組](./media/review-your-access/review-access-preview.png)

1. 選取 **[是]** 以保留您的存取權，或選取 [**否**] 移除您的存取權。

1. 如果您按一下 [**是]**，您可能需要在 [**原因**] 方塊中指定理由。

    ![完整的存取權審查，詢問您是否仍需要群組的存取權](./media/review-your-access/review-access-yes-preview.png)

1. 按一下 [提交] 。

    您的選擇隨即提交，並返回 [我的存取權] 頁面。

    如果您想要變更您的回應，請重新開啟 [存取評論] 頁面，並更新您的回應。 您可以隨時變更您的回應，直到存取權審查結束為止。

    > [!NOTE]
    > 如果您指出不再需要存取權，就不會立即移除。 當審核結束或系統管理員停止審核時，會將您移除。

## <a name="next-steps"></a>後續步驟

- [完成群組或應用程式的存取權檢閱](complete-access-review.md)
