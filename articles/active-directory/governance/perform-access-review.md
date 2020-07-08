---
title: 在存取權審查中審查群組 & 應用程式的存取權-Azure AD
description: 瞭解如何在 Azure Active Directory 存取評論中，檢查群組成員或應用程式存取的存取權。
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
ms.openlocfilehash: 5b672cca97e70c97a5dccb1ca54daccd6c171932
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85077981"
---
# <a name="review-access-to-groups-and-applications-in-azure-ad-access-reviews"></a>在 Azure AD 存取審查中審查群組和應用程式的存取權

Azure Active Directory （Azure AD）藉由稱為 Azure AD 存取評論的功能，簡化了企業管理 Azure AD 和其他 Microsoft 線上服務中群組和應用程式存取權的方式。 本文將說明指定的審查員如何針對群組成員或具有應用程式存取權的使用者，執行存取權審查。 如果您想要查看，請參閱[Azure AD 權利管理中存取套件](entitlement-management-access-reviews-review-access.md)的讀取審查存取權

## <a name="perform-access-review-using-my-apps"></a>使用我的應用程式執行存取權審查

您可以從通知電子郵件啟動存取權審查程式，或直接前往網站。

- **電子郵件**：

>[!IMPORTANT]
> 接收電子郵件時可能會有延遲，而在某些情況下，可能需要最多24小時的時間。 加入白名單，確保 azure-noreply@microsoft.com 您收到所有電子郵件。

1. 尋找來自 Microsoft 的電子郵件，要求您審查存取權。 以下是用來審查群組存取權的電子郵件範例。

    ![Microsoft 的電子郵件範例，用以審查群組的存取權](./media/perform-access-review/access-review-email.png)

1. 按一下 [**開始審核**] 連結，以開啟存取權審查。

- **如果您沒有電子郵件**，您可以遵循下列步驟來尋找您的暫止存取審查。

    1. 登入我的應用程式入口網站，網址為 [https://myapps.microsoft.com](https://myapps.microsoft.com) 。

        ![我的應用程式入口網站列出您有許可權的應用程式](./media/perform-access-review/myapps-access-panel.png)

    1. 在頁面的右上角，按一下 [名稱] 和 [預設組織] 旁的使用者。 如果列出多個組織，請選取已要求存取權檢閱的組織。

    1. 按一下 [**存取評論**] 磚，以查看暫止的存取評論清單。

        > [!NOTE]
        > 如果看不到 [**存取評論**] 磚，就不會對該組織執行任何存取權審查，此時不需要採取任何動作。

        ![應用程式和群組的暫止存取審查清單](./media/perform-access-review/access-reviews-list.png)

    1. 按一下 [**開始審查**] 連結，以取得您想要執行的存取權審查。

當您開啟存取權審查之後，就會看到需要檢查其存取權的使用者名稱。

如果要求要檢查您自己的存取權，頁面看起來會不同。 如需詳細資訊，請參閱對[群組或應用程式檢查自己的存取權](review-your-access.md)。

![開啟存取權審查列出要審查的使用者](./media/perform-access-review/perform-access-review.png)

有兩種方式可讓您核准或拒絕存取：

- 您可以為每個使用者要求選擇適當的動作，以核准或拒絕一或多個使用者的存取權。
- 您可以接受系統建議。

### <a name="approve-or-deny-access-for-one-or-more-users"></a>核准或拒絕一或多個使用者的存取權

1. 檢查使用者的清單，並決定要核准或拒絕他們繼續存取。

    - 若要核准或拒絕單一使用者的存取，請按一下該資料列以開啟視窗，以指定要採取的動作。 
    - 若要核准或拒絕多位使用者的存取權，請在使用者旁新增核取記號，然後按一下 [**審核 X 使用者**] 按鈕，以開啟視窗以指定要採取的動作。

1. 按一下 [**核准**] 或 [**拒絕**]。 

    ![包含「核准」、「拒絕」和「不知道」選項的 [動作] 視窗](./media/perform-access-review/approve-deny.png)
    >[!NOTE]
    > 如果您不確定，您可以按一下 [**不知道**]。 而且，使用者可以繼續存取，而且您的選擇會記錄在 audit 記錄中。

1. 存取權審查的系統管理員可能會要求您在 [**原因**] 方塊中提供您決定的原因。 即使不需要原因。 您仍然可以提供決策的原因，而您包含的資訊將可供其他審核者使用。

1. 一旦您指定要採取的動作，請按一下 [**儲存**]。

    >[!NOTE]
    > 您可以在存取權審查結束之前隨時變更回應。 如果您想要變更您的回應，請選取資料列並更新回應。 例如，您可以核准先前拒絕的使用者，或拒絕先前已核准的使用者。

    >[!IMPORTANT]
    > - 如果使用者遭到拒絕存取，他們就不會立即移除。 當審核期間結束時，或如果已啟用 [[自動套用](complete-access-review.md#apply-the-changes)]，則會在系統管理員停止審核時移除它們。
    > - 如果有多個審核者，則會記錄最後提交的回應。 請考慮一個範例，其中系統管理員會指定兩個審核者– Alice 和 Bob。 Alice 會先開啟存取權審查，並核准使用者的存取要求。 在審查期間結束之前，Bob 會開啟存取權審查，並拒絕 Alice 先前核准的相同要求的存取權。 拒絕存取的最後一個決策是所記錄的回應。

### <a name="approve-or-deny-access-based-on-recommendations"></a>根據建議來核准或拒絕存取

為了讓您更輕鬆且更快速地進行存取評論，我們也提供您只需按一下即可接受的建議。 系統會根據使用者的登入活動來產生建議。

1. 在頁面底部的藍色列中，按一下 [**接受建議**]。

    ![顯示 [接受建議] 按鈕的開啟存取權審查清單](./media/perform-access-review/accept-recommendations.png)

    您會看到建議動作的摘要。

    ![顯示建議動作摘要的視窗](./media/perform-access-review/accept-recommendations-summary.png)

1. 按一下 **[確定]** 以接受建議。

## <a name="perform-access-review-using-my-access-new"></a>使用我的存取權執行存取權審查（新）

您可以透過下列幾種不同的方式，使用我的存取權中更新的使用者介面來取得新的審查者體驗：

### <a name="my-apps-portal"></a>我的應用程式入口網站

1. 登入我的應用程式，網址為 [https://myapps.microsoft.com](https://myapps.microsoft.com) 。

    ![我的應用程式入口網站列出您有許可權的應用程式](./media/perform-access-review/myapps-access-panel.png)

2. 按一下 [**存取評論**] 磚，以查看暫止的存取評論清單。

    > [!NOTE]
    > 如果看不到 [**存取評論**] 磚，就不會對該組織執行任何存取權審查，此時不需要採取任何動作。

![應用程式和群組的暫止存取審查清單，其中包含預覽期間顯示的可用新體驗橫幅](./media/perform-access-review/banner.png)

3. 按一下 [**試用]！** 在頁面頂端的橫幅中。 這將帶您前往新的我的存取權體驗。
  
### <a name="email"></a>電子郵件

  >[!IMPORTANT]
> 接收電子郵件時可能會有延遲，而在某些情況下，可能需要最多24小時的時間。 加入白名單，確保 azure-noreply@microsoft.com 您收到所有電子郵件。

   1. 尋找來自 Microsoft 的電子郵件，要求您審查存取權。 您可以看到以下的範例電子郵件訊息：

   ![Microsoft 的電子郵件範例，用以審查群組的存取權](./media/perform-access-review/access-review-email-preview.png)

   2. 按一下 [**開始審核**] 連結，以開啟存取權審查。

>[!NOTE]
>如果按一下 [開始審核]，會將您帶到**我的應用程式**遵循上述**我的應用程式入口網站**一節中所列的步驟。

### <a name="navigate-to-my-access-directly"></a>直接流覽至我的存取權

您也可以使用瀏覽器開啟我的存取權，以查看您的暫止存取評論。

1. 登入我的存取權，網址為https://myaccess.microsoft.com/

2. 從左側列的功能表中選取 [**存取評論**]，以查看指派給您的暫止存取評論清單。

   ![存取功能表中的評論](./media/perform-access-review/access-review-menu.png)

### <a name="approve-or-deny-access-for-one-or-more-users"></a>核准或拒絕一或多個使用者的存取權

在您開啟 [群組和應用程式] 下的我的存取權之後，您可以看到：

- **名稱**存取權檢查的名稱。
- **逾期**審查的到期日。 拒絕此日期之後，就可以從所要審核的群組或應用程式中移除使用者。
- **資源**[審查] 下的資源名稱。
- **進度**在此存取權審查中，經過使用者總數審查的使用者人數。

按一下存取權審查的名稱即可開始使用。

![應用程式和群組的暫止存取審查清單](./media/perform-access-review/access-reviews-list-preview.png)

一旦開啟，您就會在存取權審查範圍中看到使用者清單。 如果要求要檢查您自己的存取權，頁面看起來會不同。 如需詳細資訊，請參閱對[群組或應用程式檢查自己的存取權](review-your-access.md)。

有兩種方式可讓您核准或拒絕存取：

- 您可以手動核准或拒絕一或多個使用者的存取權。
- 您可以接受系統建議。

#### <a name="manually-approve-or-deny-access-for-one-or-more-users"></a>手動核准或拒絕一或多個使用者的存取權

1. 檢查使用者的清單，並決定要核准或拒絕他們繼續存取。
2. 按一下一或多個使用者的名稱旁邊的圓圈，加以選取。
3. 選取上方列的 [**核准**] 或 [**拒絕**]。
    - 如果您不確定，您可以按一下 [**不知道**]。 使用者會取得存取權，並將您的選擇記錄在 audit 記錄中。 請務必記住，您所提供的任何資訊都可供其他審核者使用。 他們可以讀取您的意見，並在審查要求時將其納入考慮。

    ![開啟存取權審查列出需要審查的使用者](./media/perform-access-review/user-list-preview.png)

4. 存取權審查的系統管理員可能會要求您在 [**原因**] 方塊中提供您決定的原因。 即使不需要原因。 您仍然可以提供決策的原因，而包含的資訊將可供其他核准者進行審查。

5. 按一下 [提交] 。
    - 您可以隨時變更您的回應，直到存取權審查結束為止。 如果您想要變更您的回應，請選取資料列並更新回應。 例如，您可以核准先前拒絕的使用者，或拒絕先前已核准的使用者。

 >[!IMPORTANT]
 > - 如果使用者遭到拒絕存取，他們就不會立即移除。 當審核期間結束或系統管理員停止審核時，就會將它們移除。 
 > - 如果有多個審核者，則會記錄最後提交的回應。 請考慮一個範例，其中系統管理員會指定兩個審核者– Alice 和 Bob。 Alice 會先開啟存取權審查，並核准使用者的存取要求。 在審查期間結束之前，Bob 會開啟存取權審查，並拒絕 Alice 先前核准的相同要求的存取權。 拒絕存取的最後一個決策是所記錄的回應。

#### <a name="approve-or-deny-access-based-on-recommendations"></a>根據建議來核准或拒絕存取

為了讓您更輕鬆且更快速地進行存取評論，我們也提供您只需按一下即可接受的建議。 系統會根據使用者的登入活動來產生建議。

1. 選取一或多個使用者，然後按一下 [**接受建議**]。

    ![顯示 [接受建議] 按鈕的開啟存取權審查清單](./media/perform-access-review/accept-recommendations-preview.png)

1. 按一下 [**提交**] 以接受建議。

若要接受所有使用者的建議，請確定未選取任何一個，然後按一下頂端列的 [**接受建議**] 按鈕。

>[!NOTE]
>當您接受建議時，將不會變更先前的決策。

## <a name="next-steps"></a>後續步驟

- [完成群組或應用程式的存取權檢閱](complete-access-review.md)

