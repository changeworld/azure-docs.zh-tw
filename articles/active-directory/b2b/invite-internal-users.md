---
title: 邀請內部使用者進行 B2B 共同作業-Azure AD
description: 如果您有合作夥伴、轉銷商、供應商、廠商和其他來賓的內部使用者帳戶，您可以藉由邀請他們使用自己的外部認證或登入，變更為 Azure AD B2B 共同作業。 請使用 PowerShell 或 Microsoft Graph 邀請 API。
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
ms.openlocfilehash: 783fc0fa6f6c4e6c918fa3ff5fe0b53a71fa0178
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81680166"
---
# <a name="invite-internal-users-to-b2b-collaboration"></a>邀請內部使用者進行 B2B 共同作業

|     |
| --- |
| 邀請內部使用者使用 B2B 共同作業是 Azure Active Directory 的公開預覽功能。 如需有關預覽版的詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 |
|     |

在 Azure AD B2B 共同作業的可用性之前，組織可以藉由為他們設定內部認證，與轉銷商、供應商、廠商和其他來賓使用者共同作業。 如果您有像這樣的內部來賓使用者，您可以邀請他們使用 B2B 共同作業，讓您可以利用 Azure AD 的 B2B 權益。 您的 B2B 來賓使用者將能夠使用自己的身分識別和認證來登入，而且您不需要維護密碼或管理帳戶生命週期。

將邀請傳送至現有的內部帳戶，可讓您保留該使用者的物件識別碼、UPN、群組成員資格和應用程式指派。 您不需要手動刪除並重新邀請使用者或重新指派資源。 若要邀請使用者，您將使用邀請 API 同時傳遞內部使用者物件和來賓使用者的電子郵件地址，以及邀請。 當使用者接受邀請時，B2B 服務會將現有的內部使用者物件變更為 B2B 使用者。 之後，使用者必須使用其 B2B 認證來登入雲端資源服務。 他們仍然可以使用其內部認證來存取內部部署資源，但您可以藉由重設或變更內部帳戶的密碼來避免這種情況。

> [!NOTE]
> 邀請是單向的。 您可以邀請內部使用者使用 B2B 共同作業，但是一旦新增 B2B 認證之後，就無法將其移除。 若要將使用者變更回僅限內部的使用者，您必須刪除使用者物件，並建立一個新的。

在公開預覽期間，本文中所述用於邀請內部使用者進行 B2B 共同作業的方法，無法在這些實例中使用：

- 已將 Exchange 授權指派給內部使用者。
- 使用者來自于目錄中設定為直接同盟的網域。
- 內部使用者是僅限雲端的帳戶，且其主要帳戶不在 Azure AD 中。

在這些情況下，如果內部使用者必須變更為 B2B 使用者，您應該刪除內部帳戶，並將 B2B 共同作業的邀請傳送給使用者。

內部**部署同步使用者**：對於在內部部署與雲端之間同步處理的使用者帳戶，內部部署目錄會在受邀使用 B2B 共同作業之後，保持授權來源。 您對內部部署帳戶所做的任何變更都會同步處理到雲端帳戶，包括停用或刪除帳戶。 因此，您無法防止使用者登入其內部部署帳戶，而只需要刪除內部部署帳戶，就能保留其雲端帳戶。 相反地，您可以將內部部署帳戶密碼設定為隨機 GUID 或其他未知值。

## <a name="how-to-invite-internal-users-to-b2b-collaboration"></a>如何邀請內部使用者進行 B2B 共同作業

您可以使用 PowerShell 或邀請 API，將 B2B 邀請傳送給內部使用者。 請確定您要用於邀請的電子郵件地址已設定為內部使用者物件的外部電子郵件地址。

- 若為僅限雲端的使用者，請使用 OtherMails 屬性中的電子郵件地址來取得邀請。
- 若為內部部署同步處理的使用者，您必須使用 [User. Mail] 屬性中的值來取得邀請。
- 使用者的 Mail 屬性中的網域必須與他們用來登入的帳戶相符。 否則，某些服務（例如小組）將無法驗證使用者。

根據預設，邀請會傳送電子郵件給使用者，讓他們知道他們已受邀，但是您可以隱藏這封電子郵件並改為傳送您自己的資訊。

> [!NOTE]
> 若要傳送您自己的電子郵件或其他通訊，您可以使用 AzureADMSInvitation 搭配-SendInvitationMessage： $false 以無訊息方式邀請使用者，然後將您自己的電子郵件訊息傳送給轉換後的使用者。 請參閱[AZURE AD B2B 共同作業 API 和自訂](customize-invitation-api.md)。

## <a name="use-powershell-to-send-a-b2b-invitation"></a>使用 PowerShell 傳送 B2B 邀請

使用下列命令將使用者邀請至 B2B 共同作業：

```powershell
Uninstall-Module AzureADPreview
Install-Module AzureADPreview
$ADGraphUser = Get-AzureADUser -searchstring "<<external email>>"
$msGraphUser = New-Object Microsoft.Open.MSGraph.Model.User -ArgumentList $ADGraphUser.ObjectId
New-AzureADMSInvitation -InvitedUserEmailAddress <<external email>> -SendInvitationMessage $True -InviteRedirectUrl "http://myapps.microsoft.com" -InvitedUser $msGraphUser
```

## <a name="use-the-invitation-api-to-send-a-b2b-invitation"></a>使用邀請 API 來傳送 B2B 邀請

下列範例說明如何呼叫邀請 API，以 B2B 使用者的身分邀請內部使用者。

```json
POST https://graph.microsoft.com/v1.0/invitations
Authorization: Bearer eyJ0eX...
ContentType: application/json
{
    "invitedUserEmailAddress": "<<external email>>",
    "sendInvitationMessage": true,
    "invitedUserMessageInfo": {
        "messageLanguage": "en-US",
        "ccRecipients": [
            {
                "emailAddress": {
                    "name": null,
                    "address": "<<optional additional notification email>>"
                }
            }
        ],
        "customizedMessageBody": "<<custom message>>"
    },
    "inviteRedirectUrl": "https://myapps.microsoft.com?tenantId=",
    "invitedUser": {
        "id": "<<ID for the user you want to convert>>"
    }
}
```

對 API 的回應，與您邀請新的來賓使用者至目錄時所得到的回應相同。

## <a name="next-steps"></a>後續步驟

- [B2B 共同作業邀請兌換](redemption-experience.md)
