---
title: B2B 邀請電子郵件的元素 - Azure 活動目錄 |微軟文檔
description: Azure Active Directory B2B 共同作業邀請電子郵件範本
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f93586d46aa01116990f8f02f344c6952d3c1b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "65768360"
---
# <a name="the-elements-of-the-b2b-collaboration-invitation-email---azure-active-directory"></a>B2B 共同作業邀請電子郵件的元素 - Azure Active Directory

邀請電子郵件是一個可讓合作夥伴在 Azure AD 中以 B2B 共同作業使用者身分上線的重要元件。 您可以用它們提高收件者的信任度。 您可以為電子郵件增添合法性和社交證明，以確保收件者在選取 [開始使用]**** 按鈕來接受邀請時沒有疑慮。 此信任是減少共用摩擦的重要手段。 而且您也希望電子郵件看起來很讚！

![顯示 B2B 邀請電子郵件的螢幕擷取畫面](media/invitation-email-elements/invitation-email.png)

## <a name="explaining-the-email"></a>說明電子郵件
讓我們看看一些電子郵件項目，以便了解如何充分利用這些功能。

### <a name="subject"></a>主體
電子郵件的主旨依循以下模式：您收到加入 &lt;tenantname&gt; 組織的邀請

### <a name="from-address"></a>寄件者地址
針對「寄件者地址」，我們使用類似 LinkedIn 的模式。  您應該清楚邀請者是誰及來自哪個公司，並且表明電子郵件是來自 Microsoft 電子郵件帳戶。 格式是：<invites@microsoft.com>微軟邀請或&lt;顯示邀請者&gt;的名字從&lt;租戶名稱&gt;（通過微軟）。 <invites@microsoft.com>

### <a name="reply-to"></a>回覆地址
回覆電子郵件會設定為邀請者的電子郵件 (如果可用)，以便在回覆電子郵件時會將電子郵件傳回給邀請者。

### <a name="branding"></a>商標
來自您租用戶的邀請電子郵件會使用您可能已為租用戶設定的公司商標。 如果您想要利用此功能，[這裡](https://docs.microsoft.com/azure/active-directory/active-directory-branding-custom-signon-azure-portal)提供有關如何設定的詳細資料。 電子郵件中會顯示橫幅標誌。 依照[這裡](https://docs.microsoft.com/azure/active-directory/active-directory-branding-custom-signon-azure-portal)的影像大小和品質指示，以獲得最佳效果。 此外，公司名稱也會顯示在對動作的呼叫中。

### <a name="call-to-action"></a>對動作的呼叫
對動作的呼叫是由兩個部分組成：說明收件者收到郵件的原因，以及要求收件者採取的動作。
- 「原因」區段可以使用以下模式來處理：您收到存取 &lt;tenantname&gt; 組織中應用程式的邀請

- 而「要求您採取的動作」區段是以 [開始使用]**** 按鈕的存在來表示。 在已新增使用者而不需要邀請的情況下，就不會顯示此按鈕。

### <a name="inviters-information"></a>邀請者的資訊
邀請者的顯示名稱會包含在電子郵件中。 此外，如果您已為 Azure AD 帳戶設定個人檔案圖片，則邀請電子郵件也會包含該圖片。 這兩項措施都是為了提升收件者對電子郵件的信賴感。

如果您尚未設定自己的個人檔案圖片，圖片位置就會顯示邀請者的姓名縮寫圖示：

  ![顯示邀請者首字母縮寫圖的螢幕截圖](media/invitation-email-elements/inviters-initials.png)

### <a name="body"></a>body
內文包含[邀請來賓使用者到目錄、群組或應用程式](add-users-administrator.md) 或[使用邀請 API](customize-invitation-api.md) 時，邀請者所撰寫的訊息。 因為它是文字區域，所以不會基於安全考量處理 HTML 標記。

  ![顯示邀請電子郵件正文的螢幕截圖](media/invitation-email-elements/invitation-email-body.png)

### <a name="footer-section"></a>頁尾區段
頁尾包含 Microsoft 公司品牌，並讓收件者知道電子郵件是否是由未受監視的別名所傳送。 

特殊案例：

- 邀請者在邀請方租用中沒有電子郵件地址

  ![邀請者在邀請租賃中沒有電子郵件時的螢幕截圖](media/invitation-email-elements/inviter-no-email.png)


- 收件者不需要兌換邀請函

  ![當收件者不需要兌換邀請時，螢幕截圖](media/invitation-email-elements/when-recipient-doesnt-redeem.png)

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
