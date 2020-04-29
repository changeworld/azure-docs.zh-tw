---
title: B2B 共同作業 API 和自訂-Azure Active Directory
description: Azure Active Directory B2B 共同作業讓企業合作夥伴選擇性地存取您的公司應用程式，以支援公司間的關係
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: reference
ms.date: 04/11/2017
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a090ee3f9588ff6bff01e12db469bf04407a7fc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79263461"
---
# <a name="azure-active-directory-b2b-collaboration-api-and-customization"></a>Azure Active Directory B2B 共同作業 API 和自訂

有許多客戶告訴我們他們想要以最適合其組織的方式自訂邀請程序。 使用我們的 API，您可以進行自訂。 [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation)

## <a name="capabilities-of-the-invitation-api"></a>邀請 API 的功能

API 提供下列功能：

1. 使用*任何*電子郵件地址來邀請外部使用者。

    ```
    "invitedUserDisplayName": "Sam"
    "invitedUserEmailAddress": "gsamoogle@gmail.com"
    ```

2. 自訂使用者接受其邀請之後將會看到的內容。

    ```
    "inviteRedirectUrl": "https://myapps.microsoft.com/"
    ```

3. 選擇透過我們的服務 傳送標準邀請郵件

    ```
    "sendInvitationMessage": true
    ```

   使用您可以自訂的訊息傳送給收件者

    ```
    "customizedMessageBody": "Hello Sam, let's collaborate!"
    ```

4. 此外，您也可以選擇在邀請共同作業者時將相關人員新增到副本收件者。

5. 或者，透過選擇不以 Azure AD 傳送通知，以完全自訂您的邀請與上線工作流程。

    ```
    "sendInvitationMessage": false
    ```

   在此案例中，您會從 API 取得兌換 URL，您可以將它內嵌在電子郵件範本、IM 或其他發佈方法。

6. 最後，若您是系統管理員，您可以選擇邀請使用者做為成員。

    ```
    "invitedUserType": "Member"
    ```


## <a name="authorization-model"></a>授權模型

API 可以在下列授權模型中執行：

### <a name="app--user-mode"></a>應用程式 + 使用者模式

在此模式中，使用 API 的任何使用者必須有建立 B2B 邀請的權限。

### <a name="app-only-mode"></a>僅應用程式模式

在僅應用程式模式中，應用程式需要 User.Invite.All 範圍，邀請才能成功。

如需詳細資訊，請參閱：https://developer.microsoft.com/graph/docs/authorization/permission_scopes


## <a name="powershell"></a>PowerShell

您可以使用 PowerShell，輕鬆地新增並邀請外部使用者加入組織。 使用 Cmdlet 建立邀請：

```powershell
New-AzureADMSInvitation
```

您可以使用下列選項：

* -InvitedUserDisplayName
* -InvitedUserEmailAddress
* -SendInvitationMessage
* -InvitedUserMessageInfo

### <a name="invitation-status"></a>邀請狀態

將邀請傳送給外部使用者之後，您可以使用 **Get-AzureADUser** Cmdlet 來查看他們是否已接受要求。 將邀請傳送給外部使用者時，會填入 Get-AzureADUser 的下列屬性：

* **UserState** 指出邀請為 **PendingAcceptance** 或 **Accepted**。
* **UserStateChangedOn** 顯示最近 **UserState** 屬性變更的時間戳記。

您可以使用 [篩選]**** 選項，依 **UserState** 篩選結果。 以下範例示範如何篩選結果，只顯示具有待決邀請的使用者。 此範例也顯示 **Format-list** 選項，其可讓您指定要顯示的屬性。 
 

```powershell
Get-AzureADUser -Filter "UserState eq 'PendingAcceptance'" | Format-List -Property DisplayName,UserPrincipalName,UserState,UserStateChangedOn
```

> [!NOTE]
> 請確定您有最新版的 AzureAD PowerShell 模組或 AzureADPreview PowerShell 模組。 

## <a name="see-also"></a>請參閱

請參閱中[https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation)的邀請 API 參考。

## <a name="next-steps"></a>後續步驟

- [何謂 Azure AD B2B 共同作業？](what-is-b2b.md)
- [B2B 共同作業邀請電子郵件的元素](invitation-email-elements.md)
- [B2B 共同作業邀請兌換](redemption-experience.md)
- [在沒有邀請的情況下新增 B2B 共同作業使用者](add-user-without-invite.md)