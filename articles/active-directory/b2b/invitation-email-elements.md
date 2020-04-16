---
title: B2B 邀請電子郵件的元素 - Azure 活動目錄 |微軟文件
description: Azure Active Directory B2B 共同作業邀請電子郵件範本
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0429cfb62c319675806d76b4759b776a7b32dbcb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81407181"
---
# <a name="the-elements-of-the-b2b-collaboration-invitation-email---azure-active-directory"></a>B2B 共同作業邀請電子郵件的元素 - Azure Active Directory

邀請電子郵件是一個可讓合作夥伴在 Azure AD 中以 B2B 共同作業使用者身分上線的重要元件。 雖然[不需要您發送電子郵件來邀請使用 B2B 協作的使用者](add-user-without-invite.md),但這樣做為使用者提供了決定是否接受您的邀請所需的所有資訊。 它還為他們提供了一個連結,當他們需要返回到您的資源時,他們將來可以隨時參考。

![顯示 B2B 邀請電子郵件的螢幕擷取畫面](media/invitation-email-elements/invitation-email.png)

> [!NOTE]
> 此新電子郵件範本仍在向所有租戶推出,因此某些租戶仍在使用較舊的設計。 到 2020 年 5 月底,來自所有租戶的邀請都將使用此範本。

## <a name="explaining-the-email"></a>說明電子郵件

讓我們看看一些電子郵件項目，以便了解如何充分利用這些功能。

### <a name="subject"></a>主體

電子郵件的主題遵循以下模式:

&lt;使用者名&gt;邀請您訪問其組織內的應用程式。

### <a name="from-address"></a>寄件者地址

針對「寄件者地址」，我們使用類似 LinkedIn 的模式。 此模式應明確說明,雖然電子郵件來自invites@microsoft.com,但邀請來自另一個組織。 格式是:微軟 <invites@microsoft.com>邀請或代表租戶&lt;&gt; <invites@microsoft.com>名稱 的微軟邀請。 

### <a name="reply-to"></a>回覆地址

回覆電子郵件會設定為邀請者的電子郵件 (如果可用)，以便在回覆電子郵件時會將電子郵件傳回給邀請者。

### <a name="phishing-warning"></a>網路釣魚警告

電子郵件首先向使用者發出有關網路釣魚的簡短警告,提醒用戶他們只接受他們期望的邀請。 最好能確保您邀請的合作夥伴不會因為您的邀請而感到驚訝,提前向他們提及邀請。

![電子郵件中網路釣魚警告的影像](media/invitation-email-elements/phishing-warning.png)

### <a name="inviters-information"></a>邀請者的資訊

電子郵件包括有關邀請者及其發送邀請的組織的資訊。 這包括寄件者的姓名和電子郵寄地址,以及與組織關聯的名稱和主要域。 所有這些資訊都應幫助被邀請者就接受邀請做出明智的決定。

![邀請者資訊在電子郵件中的影像](media/invitation-email-elements/inviters-information.png)

### <a name="invitation-message"></a>邀請訊息

如果邀請者在[邀請來賓使用者訪問目錄、組或應用](add-users-administrator.md)或使用[邀請 API](customize-invitation-api.md)時,將郵件作為邀請的一部分,則該郵件將突出顯示到電子郵件的主部分。 還包括被邀請者的姓名和個人資料圖像(如果他們設置了一個)。 郵件本身是一個文字區域,因此出於安全原因,它不處理 HTML 標記。

![電子郵件中邀請郵件的影像](media/invitation-email-elements/invitation-message.png)

### <a name="accept-button-and-redirect-url"></a>接受按鈕與重定到網址

電子郵件的下一部分包含有關被邀請者接受邀請後將採取哪些內容的資訊,以及執行此操作的按鈕。  將來,被邀請者始終可以使用此連結直接返回到您的資源。

![接受按鈕的影像與電子郵件中的重定向網址](media/invitation-email-elements/accept-button.png)

### <a name="footer-section"></a>頁尾區段

頁腳包含有關要發送的邀請的詳細資訊。 邀請者始終可以選擇阻止將來的邀請。 如果組織[設置了隱私聲明](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-properties-area),則指向該語句的連結將在此處顯示。  否則,註釋指示組織未設置隱私聲明。

![電子郵件中註腳部分的影像](media/invitation-email-elements/footer-section.png)
 
## <a name="how-the-language-is-determined"></a>如何決定語言

在邀請電子郵件中向來賓使用者顯示的語言取決於下列設定。 這些設定是依優先順序列出的。 如果未設定某個設定，就會由清單中的下一個設定決定語言。

- [invitedUserMessageInfo](https://docs.microsoft.com/graph/api/resources/invitedusermessageinfo?view=graph-rest-1.0) 物件的 **messageLanguage** 屬性 (如果使用「建立邀請」API)
-   來賓[使用者物件](https://docs.microsoft.com/graph/api/resources/user?view=graph-rest-1.0)中指定的 **preferredLanguage** 屬性
-   來賓使用者主租用戶的屬性中設定的**通知語言** (僅適用於 Azure AD 租用戶)
-   來源租用戶的屬性中設定的**通知語言**

如果未設定任何這些設定，語言就會預設為英文 (美國)。

## <a name="next-steps"></a>後續步驟

請參閱下列有關 Azure AD B2B 共同作業的文章：

- [什麼是 Azure AD B2B 協作](what-is-b2b.md)
- [Azure Active Directory 系統管理員如何新增 B2B 共同作業使用者？](add-users-administrator.md)
- [資訊工作者如何新增 B2B 共同作業使用者？](add-users-information-worker.md)
- [B2B 共同作業邀請兌換](redemption-experience.md)
- [在沒有邀請的情況下新增 B2B 共同作業使用者](add-user-without-invite.md)
