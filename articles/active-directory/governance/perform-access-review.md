---
title: 查看存取權審核中對群組 & 應用程式的存取權-Azure AD
description: 瞭解如何在 Azure Active Directory 存取權評論中，檢查群組成員或應用程式存取權的存取權。
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
ms.openlocfilehash: 7e03ba5e7a4a24ee3114946230f78a64ea3d42cf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91265676"
---
# <a name="review-access-to-groups-and-applications-in-azure-ad-access-reviews"></a>在 Azure AD 存取權評論中審查群組和應用程式的存取權

Azure Active Directory (Azure AD) 簡化了企業如何使用稱為 Azure AD 存取權評論的功能，來管理 Azure AD 和其他 Microsoft 線上服務中的群組和應用程式的存取權。 本文將探討指定的審核者如何針對群組成員或具有應用程式存取權的使用者，執行存取權審核。 如果您想要複習，請存取[Azure AD 權利管理中存取套件](entitlement-management-access-reviews-review-access.md)的套件讀取審核存取權

## <a name="perform-access-review-using-my-apps"></a>使用我的應用程式執行存取權審核

您可以從通知電子郵件或直接前往網站開始存取權審核程式。

- **電子郵件**：

>[!IMPORTANT]
> 接收電子郵件可能會有延遲，而且在某些情況下，可能需要24小時的時間。 新增 azure-noreply@microsoft.com 至您的安全收件者清單，以確定您收到的是所有電子郵件。

1. 尋找 Microsoft 的電子郵件，要求您複習存取權。 以下是用來檢查群組存取權的電子郵件範例。

    ![顯示 Microsoft 用來審查群組存取權的範例電子郵件的螢幕擷取畫面。](./media/perform-access-review/access-review-email.png)

1. 按一下 [ **開始審核** ] 連結以開啟存取權審核。

- **如果您沒有電子郵件**，可以遵循下列步驟來尋找您的暫止存取權評論。

    1. 登入我的應用程式入口網站 [https://myapps.microsoft.com](https://myapps.microsoft.com) 。

        ![我的應用程式入口網站列出您具有許可權的應用程式](./media/perform-access-review/myapps-access-panel.png)

    1. 在頁面的右上角，按一下您的名稱和預設組織旁的使用者。 如果列出多個組織，請選取已要求存取權檢閱的組織。

    1. 按一下 [ **存取審核** ] 圖格，以查看暫止存取權評論的清單。

        > [!NOTE]
        > 如果看不到 [ **存取權審核** ] 磚，就不會對該組織執行任何存取權審核，此時不需要採取任何動作。

        ![顯示應用程式和群組的暫止存取權審核清單的螢幕擷取畫面。](./media/perform-access-review/access-reviews-list.png)

    1. 按一下您想要執行之存取權審核的 [ **開始審核** ] 連結。

開啟存取權審核之後，您會看到需要檢查其存取權的使用者名稱。

如果要求要檢查您自己的存取權，頁面看起來會不同。 如需詳細資訊，請參閱 [將您自己的存取權提供給群組或應用程式](review-your-access.md)。

![開啟存取權檢查以列出要審核的使用者](./media/perform-access-review/perform-access-review.png)

您可以使用兩種方式來核准或拒絕存取：

- 您可以為每個使用者要求選擇適當的動作，以核准或拒絕一或多個使用者的存取權。
- 您可以接受系統建議。

### <a name="approve-or-deny-access-for-one-or-more-users"></a>核准或拒絕一或多個使用者的存取權

1. 檢查使用者清單，並決定是否要核准或拒絕他們的持續存取權。

    - 若要核准或拒絕單一使用者的存取權，請按一下資料列以開啟視窗，以指定要採取的動作。 
    - 若要核准或拒絕多位使用者的存取權，請在 [使用者] 旁新增核取記號，然後按一下 [ **評論 X 使用者 (s]) ** 按鈕來開啟視窗，以指定要採取的動作。

1. 按一下 [ **核准** ] 或 [ **拒絕**]。 

    ![包含「核准」、「拒絕」和「不知道」選項的動作視窗](./media/perform-access-review/approve-deny.png)
    >[!NOTE]
    > 如果您不確定，您可以按一下 [ **不知道**]。 使用者可以繼續存取，而您的選擇會記錄在 audit 記錄檔中。

1. 存取權審查的系統管理員可能會要求您在 [ **原因** ] 方塊中提供決策的原因。 即使不需要原因也是如此。 您仍然可以提供決策的原因，而您包含的資訊將可供其他審核者使用。

1. 當您指定要採取的動作之後，請按一下 [ **儲存**]。

    >[!NOTE]
    > 您可以在存取權審核結束之前隨時變更您的回應。 如果您想要變更回應，請選取資料列並更新回應。 例如，您可以核准先前拒絕的使用者，或拒絕先前核准的使用者。

    >[!IMPORTANT]
    > - 如果使用者遭到拒絕存取，就不會立即移除。 當審核期間結束時，或當系統管理員在啟用 [ [自動套用](complete-access-review.md#apply-the-changes) ] 時，就會移除它們。
    > - 如果有多個審核者，則會記錄最後提交的回應。 請考慮一個範例，其中系統管理員會指定兩個審核者– Alice 和 Bob。 Alice 先開啟存取權審核，並核准使用者的存取要求。 在審核期間結束之前，Bob 開啟了存取權審核，並拒絕了 Alice 先前核准之相同要求的存取權。 拒絕存取的最後一個決策是所記錄的回應。

### <a name="approve-or-deny-access-based-on-recommendations"></a>根據建議核准或拒絕存取

為了讓您更輕鬆且快速地查看存取權，我們也會提供您只要按一下即可接受的建議。 系統會根據使用者的登入活動來產生建議。

1. 在頁面底部的藍色列中，按一下 [ **接受建議**]。

    ![螢幕擷取畫面，顯示已選取 [接受建議] 按鈕的開啟存取權審核清單。](./media/perform-access-review/accept-recommendations.png)

    您會看到建議的動作摘要。

    ![顯示建議動作摘要的視窗](./media/perform-access-review/accept-recommendations-summary.png)

1. 按一下 **[確定]** 以接受建議。

## <a name="perform-access-review-using-my-access-new"></a>使用我的存取權 (新) 來執行存取權審核

您可以透過下列幾種不同的方式，使用更新的使用者我的存取權介面來取得新的審核者體驗：

### <a name="my-apps-portal"></a>我的應用程式入口網站

1. 登入我的應用程式 [https://myapps.microsoft.com](https://myapps.microsoft.com) 。

    ![我的應用程式入口網站列出您具有許可權的應用程式](./media/perform-access-review/myapps-access-panel.png)

2. 按一下 [ **存取審核** ] 圖格，以查看暫止存取權評論的清單。

    > [!NOTE]
    > 如果看不到 [ **存取權審核** ] 磚，就不會對該組織執行任何存取權審核，此時不需要採取任何動作。

![在預覽期間顯示具有新體驗可用橫幅的應用程式和群組暫止存取權審核清單](./media/perform-access-review/banner.png)

3. 按一下 [**試試看！** ] 在頁面頂端的橫幅中。 這會帶您前往新的我的存取權體驗。
  
### <a name="email"></a>電子郵件

  >[!IMPORTANT]
> 接收電子郵件可能會有延遲，而且在某些情況下，可能需要24小時的時間。 新增 azure-noreply@microsoft.com 至您的安全收件者清單，以確定您收到的是所有電子郵件。

   1. 尋找 Microsoft 的電子郵件，要求您複習存取權。 您可以在下方看到範例電子郵件訊息：

   ![Microsoft 的電子郵件範例，可審查群組的存取權](./media/perform-access-review/access-review-email-preview.png)

   2. 按一下 [ **開始審核** ] 連結以開啟存取權審核。

>[!NOTE]
>如果按一下 [開始檢查] 會將您帶到 **我的應用程式** 遵循前面的 **我的應用程式入口網站**中所列的步驟。

### <a name="navigate-to-my-access-directly"></a>直接流覽至我的存取權

您也可以使用瀏覽器來開啟我的存取權，以查看您的暫止存取權評論。

1. 登入我的存取權位置 https://myaccess.microsoft.com/

2. 從左側工具列上的功能表選取 [ **存取審核** ]，以查看指派給您的暫止存取評論清單。

   ![存取功能表中的評論](./media/perform-access-review/access-review-menu.png)

### <a name="approve-or-deny-access-for-one-or-more-users"></a>核准或拒絕一或多個使用者的存取權

開啟 [群組和應用程式] 底下的我的存取權之後，您可以看到：

- **名稱** 存取權檢查的名稱。
- **到期** 審查的到期日。 拒絕此日期之後，使用者將可從正在審核的群組或應用程式中移除。
- **資源** 正在審核之資源的名稱。
- **進度** 此存取權評論中的使用者總數已審核的使用者數目。

按一下存取權評論的名稱以開始使用。

![應用程式和群組的暫止存取權審核清單](./media/perform-access-review/access-reviews-list-preview.png)

一旦開啟，您就會在存取權審查的範圍內看到使用者清單。 如果要求要檢查您自己的存取權，頁面看起來會不同。 如需詳細資訊，請參閱 [將您自己的存取權提供給群組或應用程式](review-your-access.md)。

您可以使用兩種方式來核准或拒絕存取：

- 您可以手動核准或拒絕一或多個使用者的存取權。
- 您可以接受系統建議。

#### <a name="manually-approve-or-deny-access-for-one-or-more-users"></a>手動核准或拒絕一或多個使用者的存取權

1. 檢查使用者清單，並決定是否要核准或拒絕他們的持續存取權。
2. 選取一或多個使用者，方法是按一下其名稱旁邊的圓形。
3. 選取上方橫條圖上的 [ **核准** ] 或 [ **拒絕** ]。
    - 如果您不確定，您可以按一下 [ **不知道**]。 使用者可以繼續存取，而您的選擇會記錄在 audit 記錄檔中。 請務必記住，您提供的任何資訊都將可供其他審核者使用。 他們可以讀取您的批註，並在審核要求時將其納入考慮。

    ![開啟存取權審核清單需要審查的使用者](./media/perform-access-review/user-list-preview.png)

4. 存取權審查的系統管理員可能會要求您在 [ **原因** ] 方塊中提供決策的原因。 即使不需要原因也是如此。 您仍然可以提供決策的原因，並將您包含的資訊提供給其他核准者進行審查。

5. 按一下 [提交]  。
    - 您可以隨時變更回應，直到存取權審核結束為止。 如果您想要變更回應，請選取資料列並更新回應。 例如，您可以核准先前拒絕的使用者，或拒絕先前核准的使用者。

 >[!IMPORTANT]
 > - 如果使用者遭到拒絕存取，就不會立即移除。 當審核期間結束或系統管理員停止審核時，就會移除這些帳戶。 
 > - 如果有多個審核者，則會記錄最後提交的回應。 請考慮一個範例，其中系統管理員會指定兩個審核者– Alice 和 Bob。 Alice 先開啟存取權審核，並核准使用者的存取要求。 在審核期間結束之前，Bob 開啟了存取權審核，並拒絕了 Alice 先前核准之相同要求的存取權。 拒絕存取的最後一個決策是所記錄的回應。

#### <a name="approve-or-deny-access-based-on-recommendations"></a>根據建議核准或拒絕存取

為了讓您更輕鬆且快速地查看存取權，我們也會提供您只要按一下即可接受的建議。 系統會根據使用者的登入活動來產生建議。

1. 選取一或多個使用者，然後按一下 [ **接受建議**]。

    ![開啟顯示接受建議按鈕的存取權審核清單](./media/perform-access-review/accept-recommendations-preview.png)

1. 按一下 [ **提交** ] 接受建議。

若要接受所有使用者的建議，請確定未選取任何一個，並按一下頂端列上的 [ **接受建議** ] 按鈕。

>[!NOTE]
>當您接受建議時，將不會變更先前的決策。

## <a name="next-steps"></a>後續步驟

- [完成群組或應用程式的存取權檢閱](complete-access-review.md)
