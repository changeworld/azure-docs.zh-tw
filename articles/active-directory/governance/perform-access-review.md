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
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee4125e82dd5176f01de294011e22a1d66005094
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80128460"
---
# <a name="review-access-to-groups-and-applications-in-azure-ad-access-reviews"></a>在 Azure AD 存取審查中審查群組和應用程式的存取權

Azure Active Directory （Azure AD）藉由稱為 Azure AD 存取評論的功能，簡化了企業管理 Azure AD 和其他 Microsoft 線上服務中群組和應用程式存取權的方式。

本文說明指定的審查員如何為群組成員或具有應用程式存取權的使用者，執行存取權審查。

## <a name="open-the-access-review"></a>開啟存取權審查

執行存取權審查的第一個步驟是尋找並開啟存取權審查。

1. 尋找 Microsoft 的電子郵件，要求您審查存取權。 以下是用來審查群組存取權的電子郵件範例。

    ![Microsoft 的電子郵件範例，用以審查群組的存取權](./media/perform-access-review/access-review-email.png)

1. 按一下 [**開始審核**] 連結，以開啟存取權審查。

如果您沒有電子郵件，您可以遵循下列步驟來尋找您的暫止存取審查。

1. 登入 MyApps 入口網站，網址[https://myapps.microsoft.com](https://myapps.microsoft.com)為。

    ![MyApps 入口網站列出您有許可權的應用程式](./media/perform-access-review/myapps-access-panel.png)

1. 在頁面右上角按一下使用者符號，其中顯示您的名稱和預設組織。 如果列出多個組織，請選取已要求存取權檢閱的組織。

1. 按一下 [**存取評論**] 磚，以查看暫止的存取評論清單。

    如果看不到磚，則沒有針對該組織執行的存取權檢閱，此時也不需要採取任何動作。

    ![應用程式和群組的暫止存取審查清單](./media/perform-access-review/access-reviews-list.png)

1. 按一下 [**開始審查**] 連結，以取得您想要執行的存取權審查。

## <a name="perform-the-access-review"></a>執行存取權審查

當您開啟存取權審查之後，就會看到需要審查的使用者名稱。

如果要求要檢查您自己的存取權，頁面看起來會不同。 如需詳細資訊，請參閱對[群組或應用程式檢查自己的存取權](review-your-access.md)。

![開啟存取權審查列出需要審查的使用者](./media/perform-access-review/perform-access-review.png)

有兩種方式可讓您核准或拒絕存取：

- 您可以核准或拒絕一或多個使用者的存取權，或
- 您可以接受系統建議，這是最簡單快速的方式。

### <a name="approve-or-deny-access-for-one-or-more-users"></a>核准或拒絕一或多個使用者的存取權

1. 請檢查使用者清單，以決定要核准或拒絕其繼續存取。

1. 若要核准或拒絕單一使用者的存取，請按一下該資料列以開啟視窗，以指定要採取的動作。 若要核准或拒絕多位使用者的存取權，請在使用者旁新增核取記號，然後按一下 [**審核 X 使用者**] 按鈕，以開啟視窗以指定要採取的動作。

1. 按一下 [**核准**] 或 [**拒絕**]。 如果您不確定，您可以按一下 [**不知道**]。 這麼做會導致使用者維護其存取權，但選取專案會反映在 audit 記錄中。

    ![包含「核准」、「拒絕」和「不知道」選項的 [動作] 視窗](./media/perform-access-review/approve-deny.png)

1. 如有需要，請在 [**原因**] 方塊中輸入原因。

    存取權審查的系統管理員可能會要求您提供核准繼續存取或群組成員資格的原因。

1. 一旦您指定要採取的動作，請按一下 [**儲存**]。

    如果您想要變更您的回應，請選取資料列並更新回應。 例如，您可以核准先前拒絕的使用者，或拒絕先前已核准的使用者。 您可以隨時變更您的回應，直到存取權審查結束為止。

    如果有多個審核者，則會記錄最後提交的回應。 請考慮一個範例，其中系統管理員會指定兩個審核者– Alice 和 Bob。 Alice 會先開啟存取權審查並核准存取權。 在審查結束之前，Bob 會開啟存取權審查並拒絕存取。 最後一個拒絕回應是記錄的內容。

    > [!NOTE]
    > 如果使用者遭到拒絕存取，他們就不會立即移除。 當審核結束或系統管理員停止審核時，就會移除它們。

### <a name="approve-or-deny-access-based-on-recommendations"></a>根據建議來核准或拒絕存取

為了讓您更輕鬆且更快速地進行存取評論，我們也提供您只需按一下即可接受的建議。 系統會根據使用者的登入活動來產生建議。

1. 在頁面底部的藍色列中，按一下 [**接受建議**]。

    ![顯示 [接受建議] 按鈕的開啟存取權審查清單](./media/perform-access-review/accept-recommendations.png)

    您會看到建議動作的摘要。

    ![顯示建議動作摘要的視窗](./media/perform-access-review/accept-recommendations-summary.png)

1. 按一下 **[確定]** 以接受建議。

## <a name="next-steps"></a>後續步驟

- [完成群組或應用程式的存取權審查](complete-access-review.md)
