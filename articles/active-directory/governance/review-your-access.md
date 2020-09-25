---
title: 查看存取權審核中對群組 & apps 的存取權-Azure AD
description: 瞭解如何在 Azure Active Directory 存取權評論中，查看您自己對群組或應用程式的存取權。
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
ms.openlocfilehash: 959837ff540fd95d186497858d7b9de1f8b1124d
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91274005"
---
# <a name="review-access-for-yourself-to-groups-or-applications-in-azure-ad-access-reviews"></a>查看 Azure AD 存取權評論中群組或應用程式的存取權

Azure Active Directory (Azure AD) 簡化了企業如何使用稱為 Azure AD 存取權評論的功能，來管理 Azure AD 和其他 Microsoft 線上服務中群組或應用程式的存取權。

本文說明如何查看您自己對群組或應用程式的存取權。

## <a name="review-your-access-using-my-apps"></a>使用我的應用程式審查您的存取權

執行存取權檢查的第一個步驟是尋找並開啟存取權審核。

>[!IMPORTANT]
> 接收電子郵件可能會有延遲，而且在某些情況下，可能需要24小時的時間。 新增 azure-noreply@microsoft.com 至您的安全收件者清單，以確定您收到的是所有電子郵件。

1. 尋找 Microsoft 的電子郵件，要求您複習存取權。 以下是用來檢查群組存取權的範例電子郵件。

    ![Microsoft 的電子郵件範例，可檢查您對群組的存取權](./media/review-your-access/access-review-email.png)

1. 按一下 [ **審核存取** ] 連結以開啟存取權審核。

如果您沒有電子郵件，可以遵循下列步驟來尋找您的暫止存取權評論。

1. 登入我的應用程式入口網站 [https://myapps.microsoft.com](https://myapps.microsoft.com) 。

    ![我的應用程式入口網站列出您具有許可權的應用程式](./media/review-your-access/myapps-access-panel.png)

1. 在頁面右上角按一下使用者符號，其中顯示您的名稱和預設組織。 如果列出多個組織，請選取已要求存取權檢閱的組織。

1. 在頁面的右側，按一下 [ **存取審核** ] 圖格，以查看暫止的存取權評論清單。

    如果看不到磚，則沒有針對該組織執行的存取權檢閱，此時也不需要採取任何動作。

    ![應用程式和群組的暫止存取權審核清單](./media/review-your-access/access-reviews-list.png)

1. 按一下您想要執行之存取權審核的 [ **開始審核** ] 連結。

### <a name="perform-the-access-review"></a>執行存取權審核

開啟存取權審核之後，您就可以看到您的存取權。

1. 檢查您的存取權，並決定您是否仍需要存取權。

    如果要求要檢查其他人的存取權，頁面看起來會不同。 如需詳細資訊，請參閱 [檢查群組或應用程式的存取權](perform-access-review.md)。

    ![顯示開啟存取權審查的螢幕擷取畫面，詢問您是否仍需要存取群組。](./media/review-your-access/perform-access-review.png)

1. 按一下 **[是]** 以保留您的存取權，或按一下 [ **否** ] 移除您的存取權。

1. 如果您按一下 **[是]**，可能需要在 [ **原因** ] 方塊中指定理由。

    ![顯示已完成的存取權檢查的螢幕擷取畫面，詢問您是否仍需要存取群組，並選取 [是]。](./media/review-your-access/perform-access-review-submit.png)

1. 按一下 [提交]  。

    已提交您的選取專案，並回到我的應用程式入口網站。

    如果您想要變更回應，請重新開啟存取權審核頁面，並更新您的回應。 您可以隨時變更回應，直到存取權審核結束為止。

    > [!NOTE]
    > 如果您指出不再需要存取權，就不會立即移除。 當審核結束或系統管理員停止審核時，系統就會將您移除。

## <a name="review-your-own-access-using-my-access-new"></a>使用我的存取權 (新) 來審查您自己的存取權

您可以透過下列幾種不同的方式，在我的存取權的更新使用者介面中試用新體驗：

### <a name="my-apps-portal"></a>我的應用程式入口網站

1. 登入我的應用程式入口網站 [https://myapps.microsoft.com](https://myapps.microsoft.com) 。

    ![我的應用程式入口網站列出您具有許可權的應用程式](./media/review-your-access/myapps-access-panel.png)

2. 按一下 [ **存取審核** ] 圖格，以查看暫止存取權評論的清單。

    > [!NOTE]
    > 如果看不到 [ **存取權審核** ] 磚，就不會對該組織執行任何存取權審核，此時不需要採取任何動作。

3. 按一下 [**試試看！** ] 在頁面頂端的橫幅中，移至新的我的存取權體驗。

    ![在預覽期間顯示具有新體驗可用橫幅的應用程式和群組暫止存取權審核清單](./media/review-your-access/banner-your-access.png)

4. 在 [**執行存取權檢查**] 區段中繼續進行

### <a name="email"></a>電子郵件

>[!IMPORTANT]
> 接收電子郵件可能會有延遲，而且在某些情況下，可能需要24小時的時間。 新增 azure-noreply@microsoft.com 至您的安全收件者清單，以確定您收到的是所有電子郵件。

1. 尋找 Microsoft 的電子郵件，要求您複習存取權。 您可以在下方看到範例電子郵件訊息：

 ![Microsoft 的電子郵件範例，可審查群組的存取權](./media/review-your-access/access-review-email-preview.png)

2. 按一下 [ **審核存取** ] 連結以開啟存取權審核。

3. 在 [**執行存取權檢查**] 區段中繼續進行

>[!NOTE]
>如果按一下 [開始檢查] 會將您帶到 **我的應用程式** 遵循前面的 **我的應用程式入口網站**中所列的步驟。

### <a name="directly-at-my-access"></a>直接在我的存取權

您也可以使用瀏覽器來開啟我的存取權，以查看您的暫止存取權評論。

1. 登入我的存取權位置 https://myaccess.microsoft.com/

2. 從左側工具列上的功能表選取 [ **存取審核** ]，以查看指派給您的暫止存取評論清單。

   ![存取功能表中的評論](./media/review-your-access/access-review-menu.png)

### <a name="perform-the-access-review"></a>執行存取權審核

1. 在 [群組和應用程式] 底下，您可以看到：
    
    - **名稱** 存取權檢查的名稱。
    - **到期** 審查的到期日。 拒絕此日期之後，使用者將可從正在審核的群組或應用程式中移除。
    - **資源** 正在審核之資源的名稱。
    - **進度** 此存取權評論中的使用者總數已審核的使用者數目。
    
2. 按一下存取權評論的名稱以開始使用。

   ![應用程式和群組的暫止存取權審核清單](./media/review-your-access/access-reviews-list-preview.png)

3. 檢查您的存取權，並決定您是否仍需要存取權。

    如果要求要檢查其他人的存取權，頁面看起來會不同。 如需詳細資訊，請參閱 [檢查群組或應用程式的存取權](perform-access-review.md)。

    ![開啟存取權審核，詢問您是否仍需要存取群組](./media/review-your-access/review-access-preview.png)

1. 選取 **[是]** 以保留您的存取權，或選取 [ **否** ] 以移除存取權。

1. 如果您按一下 **[是]**，可能需要在 [ **原因** ] 方塊中指定理由。

    ![完成的存取權檢查，詢問您是否仍需要存取群組](./media/review-your-access/review-access-yes-preview.png)

1. 按一下 [提交]  。

    已提交您的選取專案，並返回我的存取權頁面。

    如果您想要變更回應，請重新開啟存取權審核頁面，並更新您的回應。 您可以隨時變更回應，直到存取權審核結束為止。

    > [!NOTE]
    > 如果您指出不再需要存取權，就不會立即移除。 當審核結束或系統管理員停止審核時，系統就會將您移除。

## <a name="next-steps"></a>後續步驟

- [完成群組或應用程式的存取權檢閱](complete-access-review.md)
