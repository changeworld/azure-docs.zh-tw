---
title: B2B 邀請電子郵件的元素-Azure Active Directory |Microsoft Docs
description: Azure Active Directory B2B 共同作業邀請電子郵件範本
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 09/28/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: d03391ba5a82c128197c86ea6ed84389552fadb9
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91439839"
---
# <a name="the-elements-of-the-b2b-collaboration-invitation-email---azure-active-directory"></a>B2B 共同作業邀請電子郵件的元素 - Azure Active Directory

邀請電子郵件是一個可讓合作夥伴在 Azure AD 中以 B2B 共同作業使用者身分上線的重要元件。 雖然 [您不需要傳送電子郵件來邀請使用 B2B](add-user-without-invite.md)共同作業的人，但是這麼做會為使用者提供決定是否接受您的邀請所需的所有資訊。 它也提供他們在未來需要返回您的資源時，可以隨時參考的連結。

![顯示 B2B 邀請電子郵件的螢幕擷取畫面](media/invitation-email-elements/invitation-email.png)

> [!NOTE]
> 這個新的電子郵件範本仍會推出給所有租使用者，因此某些租使用者仍在使用較舊的設計。 在5月2020日結束時，所有租使用者的邀請都會使用此範本。

## <a name="explaining-the-email"></a>說明電子郵件

讓我們看看一些電子郵件項目，以便了解如何充分利用這些功能。

### <a name="subject"></a>主體

電子郵件的主旨遵循此模式：

&lt;&gt;使用者名稱邀請您存取其組織內的應用程式。

### <a name="from-address"></a>寄件者地址

針對「寄件者地址」，我們使用類似 LinkedIn 的模式。 這種模式應該清楚指出，雖然電子郵件來自 invites@microsoft.com ，但邀請來自另一個組織。 格式為： Microsoft Invitations  <invites@microsoft.com> 或代表 tenantname 的 Microsoft 邀請 &lt; &gt;  <invites@microsoft.com> 。 

### <a name="reply-to"></a>回覆地址

回覆電子郵件會設定為邀請者的電子郵件 (如果可用)，以便在回覆電子郵件時會將電子郵件傳回給邀請者。

### <a name="phishing-warning"></a>網路釣魚警告

電子郵件會從使用者的短暫警告開始，通知使用者網路釣魚，警示他們應該只接受所預期的邀請。 最好的作法是確定您邀請的夥伴不會因為您的邀請而感到驚訝。

![電子郵件中網路釣魚警告的影像](media/invitation-email-elements/phishing-warning.png)

### <a name="inviters-information"></a>邀請者的資訊

電子郵件包含邀請者的相關資訊，以及他們正在傳送邀請的組織。 這包括寄件者的名稱和電子郵件地址，以及與組織相關聯的名稱和主域。 所有這項資訊都應協助受邀者做出有關接受邀請的明智決策。

![電子郵件中邀請者資訊的影像](media/invitation-email-elements/inviters-information.png)

### <a name="invitation-message"></a>邀請訊息

如果邀請者在邀請 [來賓使用者加入目錄、群組或應用程式](add-users-administrator.md) 時，或在 [使用邀請 API](customize-invitation-api.md)時，在其邀請中包含訊息，則會在電子郵件的主要區段中反白顯示訊息。 此外也包含邀請者的名稱和設定檔影像（如果已設定的話）。 訊息本身是文字區域，因此基於安全性考慮，它不會處理 HTML 標籤。

![電子郵件中邀請訊息的影像](media/invitation-email-elements/invitation-message.png)

### <a name="accept-button-and-redirect-url"></a>接受按鈕和重新導向 URL

電子郵件的下一節包含在接受邀請之後，會在何處取得受邀者的相關資訊，以及執行此動作的按鈕。  未來，受邀者一律可以使用此連結來直接返回您的資源。

![電子郵件中接受按鈕和重新導向 URL 的影像](media/invitation-email-elements/accept-button.png)

### <a name="footer-section"></a>頁尾區段

頁尾包含有關正在傳送之邀請的詳細資訊。 有一個選項可讓受邀者封鎖未來的邀請。 如果組織已 [設定隱私權聲明](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-properties-area)，則會在此處顯示該語句的連結。  否則，附注指出組織未設定隱私權聲明。

![電子郵件中頁尾區段的影像](media/invitation-email-elements/footer-section.png)

### <a name="blocking-an-organization-unsubscribing"></a>封鎖組織 (取消訂閱) 

在組織的邀請中，頁尾包含 **封鎖未來邀請**的選項。 來賓使用者可以選取此連結，以封鎖任何未來來自組織的邀請。 此動作也會將組織新增至使用者的取消訂閱清單 [https://invitations.microsoft.com/unsubscribe/manage](https://invitations.microsoft.com/unsubscribe/manage) 。

### <a name="viewing-organizations-youve-blocked"></a>查看您已封鎖的組織

來賓使用者可以依照下列步驟來查看或匯出已封鎖的組織：

1. 前往 [https://invitations.microsoft.com/unsubscribe/manage](https://invitations.microsoft.com/unsubscribe/manage)。
2. 輸入您的電子郵件，並依照登入步驟進行電子郵件單次密碼驗證。
3. 查看您已封鎖的組織，或使用複製並貼上來匯出名稱。
   > [!NOTE]
   > 如果您想要允許組織您再次邀請您，可以選擇組織，然後選取 [ **下一步]**。

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
