---
title: B2B 邀請電子郵件的元素-Azure Active Directory |Microsoft Docs
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81407181"
---
# <a name="the-elements-of-the-b2b-collaboration-invitation-email---azure-active-directory"></a>B2B 共同作業邀請電子郵件的元素 - Azure Active Directory

邀請電子郵件是一個可讓合作夥伴在 Azure AD 中以 B2B 共同作業使用者身分上線的重要元件。 雖然[您不需要傳送電子郵件來邀請使用 B2B](add-user-without-invite.md)共同作業的人，但這麼做會為使用者提供所需的所有資訊，以決定是否接受您的邀請。 它也會提供連結，讓他們在未來需要返回您的資源時，可以隨時參考它們。

![顯示 B2B 邀請電子郵件的螢幕擷取畫面](media/invitation-email-elements/invitation-email.png)

> [!NOTE]
> 這個新的電子郵件範本仍會推出給所有租使用者，因此有些租使用者仍在使用較舊的設計。 從2020月底開始，所有租使用者的邀請都會使用此範本。

## <a name="explaining-the-email"></a>說明電子郵件

讓我們看看一些電子郵件項目，以便了解如何充分利用這些功能。

### <a name="subject"></a>主體

電子郵件的主旨會遵循此模式：

&lt;使用者&gt;名稱邀請您存取其組織內的應用程式。

### <a name="from-address"></a>寄件者地址

針對「寄件者地址」，我們使用類似 LinkedIn 的模式。 此模式應該可以清楚指出，雖然電子郵件來自于invites@microsoft.com另一個組織，但邀請來自其他組織。 格式 <invites@microsoft.com>為：代表&lt;tenantname&gt; <invites@microsoft.com>的 microsoft 邀請或 microsoft 邀請。 

### <a name="reply-to"></a>回覆地址

回覆電子郵件會設定為邀請者的電子郵件 (如果可用)，以便在回覆電子郵件時會將電子郵件傳回給邀請者。

### <a name="phishing-warning"></a>網路釣魚警告

此電子郵件會從使用者相關的網路釣魚簡短警告開始，警示他們應該只接受預期的邀請。 最好的作法是確保您邀請的合作夥伴不會因為您的邀請而感到驚訝。

![電子郵件中網路釣魚警告的影像](media/invitation-email-elements/phishing-warning.png)

### <a name="inviters-information"></a>邀請者的資訊

此電子郵件包含邀請者的相關資訊，以及他們正在傳送邀請的組織。 這包括寄件者的名稱和電子郵件地址，以及與組織相關聯的名稱和主要網域。 所有這項資訊都應該協助被邀請者做出有關接受邀請的明智決策。

![邀請者在電子郵件中的資訊影像](media/invitation-email-elements/inviters-information.png)

### <a name="invitation-message"></a>邀請訊息

如果邀請者在邀請[來賓使用者到目錄、群組或應用程式](add-users-administrator.md)，或當他們[使用邀請 API](customize-invitation-api.md)時，將訊息包含在其邀請中，則會在電子郵件的主要區段中反白顯示該訊息。 也包含邀請者的名稱和設定檔映射（如果已設定的話）。 訊息本身是一個文字區域，因此基於安全性考慮，它不會處理 HTML 標籤。

![電子郵件中的邀請訊息影像](media/invitation-email-elements/invitation-message.png)

### <a name="accept-button-and-redirect-url"></a>接受按鈕和重新導向 URL

電子郵件的下一節包含在接受邀請之後，將會在何處採取的相關資訊，以及用來執行這項操作的按鈕。  在未來，受邀者一律可以使用此連結直接回到您的資源。

![電子郵件中 [接受] 按鈕和 [重新導向 URL] 的影像](media/invitation-email-elements/accept-button.png)

### <a name="footer-section"></a>頁尾區段

頁尾包含所傳送邀請的詳細資訊。 受邀者一律可以選擇封鎖未來的邀請。 如果組織已[設定隱私權聲明](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-properties-area)，則會在這裡顯示語句的連結。  否則，附注表示組織尚未設定隱私權聲明。

![電子郵件中頁尾區段的影像](media/invitation-email-elements/footer-section.png)
 
## <a name="how-the-language-is-determined"></a>如何決定語言

在邀請電子郵件中向來賓使用者顯示的語言取決於下列設定。 這些設定是依優先順序列出的。 如果未設定某個設定，就會由清單中的下一個設定決定語言。

- [invitedUserMessageInfo](https://docs.microsoft.com/graph/api/resources/invitedusermessageinfo?view=graph-rest-1.0) 物件的 **messageLanguage** 屬性 (如果使用「建立邀請」API)
-   來賓[使用者物件](https://docs.microsoft.com/graph/api/resources/user?view=graph-rest-1.0)中指定的 **preferredLanguage** 屬性
-   來賓使用者主租用戶的屬性中設定的**通知語言** (僅適用於 Azure AD 租用戶)
-   來源租用戶的屬性中設定的**通知語言**

如果未設定任何這些設定，語言就會預設為英文 (美國)。

## <a name="next-steps"></a>後續步驟

請參閱下列有關 Azure AD B2B 共同作業的文章：

- [什麼是 B2B 共同作業 Azure AD](what-is-b2b.md)
- [Azure Active Directory 系統管理員如何新增 B2B 共同作業使用者？](add-users-administrator.md)
- [資訊工作者如何新增 B2B 共同作業使用者？](add-users-information-worker.md)
- [B2B 共同作業邀請兌換](redemption-experience.md)
- [在沒有邀請的情況下新增 B2B 共同作業使用者](add-user-without-invite.md)
