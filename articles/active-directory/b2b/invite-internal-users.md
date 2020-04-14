---
title: 邀請內部使用者加入 B2B 協作 - Azure AD
description: 如果您有合作夥伴、分銷商、供應商、供應商和其他來賓的內部使用者帳戶,則可以通過邀請他們使用自己的外部憑據或登錄來更改為 Azure AD B2B 協作。 使用 PowerShell 或 Microsoft 圖形邀請 API。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/12/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 097de14a3451e8d352dceb17436ae8423aa06533
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "81265937"
---
# <a name="invite-internal-users-to-b2b-collaboration"></a>邀請內部使用者加入 B2B 協作

|     |
| --- |
| 邀請內部使用者使用 B2B 協作是 Azure 活動目錄的公共預覽功能。 如需有關預覽版的詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 |
|     |

在 Azure AD B2B 協作可用之前,組織可以通過為其設置內部認證與分銷商、供應商、供應商和其他來賓使用者協作。 如果您有這樣的內部來賓使用者,則可以邀請他們使用 B2B 協作,以便利用 Azure AD B2B 權益。 您的 B2B 來賓使用者將能夠使用自己的身份和認證登錄,您無需維護密碼或管理帳戶生命週期。

新增使用者帳戶傳送邀請允許您保留該使用者的物件 ID、UPN、組成員身份和應用分配。 您無需手動刪除和重新邀請使用者或重新分配資源。 要邀請使用者,您將使用邀請 API 傳遞內部使用者物件和來賓使用者的電子郵件位址以及邀請。 當使用者接受邀請時,B2B 服務將現有內部使用者物件更改為 B2B 使用者。 今後,用戶必須使用 B2B 認證登錄到雲端資源服務。 他們仍可以使用其內部認證到本地資源,但您可以透過重置或更改內部帳戶上的密碼來防止這種情況。

> [!NOTE]
> 邀請是單向的。 您可以邀請內部使用者使用 B2B 協作,但在添加 B2B 認證時無法刪除這些認證。 要將使用者更改回僅內部使用者,您需要刪除使用者物件並創建新物件。

在公共預覽版中,本文中描述的邀請內部使用者加入 B2B 協作的方法不能在這些情況下使用:

- 已為內部使用者分配了 Exchange 許可證。
- 用戶來自為目錄中的直接聯合設置的域。
- 內部使用者是僅雲帳戶,其主帳戶不在 Azure AD 中。

在這些情況下,如果必須將內部使用者更改為 B2B 使用者,則應刪除內部帳戶並向使用者發送 B2B 協作邀請。

**本地同步使用者**:對於在本地和雲端之間同步的使用者帳戶,在邀請他們使用 B2B 協作後,本地目錄仍然是許可權來源。 對本地帳戶所做的任何更改都將同步到雲帳戶,包括禁用或刪除該帳戶。 因此,您無法阻止使用者登錄其本地帳戶,同時通過刪除本地帳戶來保留其雲帳戶。 相反,您可以將本地帳戶密碼設定為隨機 GUID 或其他未知值。

## <a name="how-to-invite-internal-users-to-b2b-collaboration"></a>如何邀請內部使用者加入 B2B 協作

您可以使用 PowerShell 或邀請 API 向內部使用者發送 B2B 邀請。 確保要用於邀請的電子郵件地址設置為內部使用者物件上的外部電子郵件位址。

- 對於僅限雲的使用者,請使用「使用者.其他郵件」中針對邀請的電子郵寄地址。
- 對於本地同步用戶,必須使用邀請的 User.Mail 屬性中的值。
- 使用者郵件屬性中的域必須與用於登錄的帳戶匹配。 否則,某些服務(如 Teams)將無法對使用者進行身份驗證。

預設情況下,邀請會向使用者發送一封電子郵件,讓他們知道自己被邀請了,但您可以禁止此電子郵件併發送您自己的電子郵件。

> [!NOTE]
> 要發送您自己的電子郵件或其他通訊,可以使用 New-AzureADMS 邀請與 -SendInviteMessage:$false 以靜默方式邀請使用者,然後將您自己的電子郵件發送給轉換後的使用者。 請參考[Azure AD B2B 協作 API 與自訂](customize-invitation-api.md)。

## <a name="use-powershell-to-send-a-b2b-invitation"></a>使用 PowerShell 傳送 B2B 邀請

使用以下指令邀請使用者加入 B2B 協作:

```powershell
Uninstall-Module AzureADPreview
Install-Module AzureADPreview
$ADGraphUser = Get-AzureADUser -searchstring "<<external email>>"
$msGraphUser = New-Object Microsoft.Open.MSGraph.Model.User -ArgumentList $ADGraphUser.ObjectId
New-AzureADMSInvitation -InvitedUserEmailAddress <<external email>> -SendInvitationMessage $True -InviteRedirectUrl "http://myapps.microsoft.com" -InvitedUser $msGraphUser
```

## <a name="use-the-invitation-api-to-send-a-b2b-invitation"></a>使用邀請 API 傳送 B2B 邀請

下面的範例展示如何調用邀請 API 以邀請內部用戶作為 B2B 使用者。

```json
POST https://graph.microsoft.com/v1.0/invitations
Authorization: Bearer eyJ0eX...
ContentType: application/json
{
    "invitedUserEmailAddress": "<<external email>>"",
    "sendInvitationMessage": true,
    "invitedUserMessageInfo": {
        "messageLanguage": "en-US",
        "ccRecipients": [
            {
                "emailAddress": {
                    "name": null,
                    "address": "<<optional additional notification email>>""
                }
            }
        ],
        "customizedMessageBody": "<<custom message>>"
    },
    "inviteRedirectUrl": "https://myapps.microsoft.com?tenantId=",
    "invitedUser": {"id": "<<ID for the user you want to convert>>"}
}
```

對 API 的回應與您邀請新來賓使用者加入目錄時得到的回應相同。

## <a name="next-steps"></a>後續步驟

- [B2B 共同作業邀請兌換](redemption-experience.md)