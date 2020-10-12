---
title: 邀請內部使用者進行 B2B 共同作業-Azure AD
description: 如果您有適用于夥伴、轉銷商、供應商、廠商和其他來賓的內部使用者帳戶，您可以邀請他們使用自己的外部認證或登入登入，以變更為 Azure AD B2B 協同作業。 請使用 PowerShell 或 Microsoft Graph 的邀請 API。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 04/12/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: c03c2c55988df04cc45ef4a1d66d959513c1626d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87908232"
---
# <a name="invite-internal-users-to-b2b-collaboration"></a>邀請內部使用者進行 B2B 共同作業

> [!NOTE]
> 邀請內部使用者使用 B2B 協同作業是 Azure Active Directory 的公開預覽功能。 如需有關預覽版的詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

在 Azure AD B2B 共同作業的可用性之前，組織可以藉由設定其內部認證，與轉銷商、供應商、廠商和其他來賓使用者共同作業。 如果您有像這樣的內部來賓使用者，您可以邀請他們使用 B2B 共同作業，以充分利用 Azure AD B2B 權益。 您的 B2B 來賓使用者將能夠使用自己的身分識別和認證登入，而且您不需要維護密碼或管理帳戶生命週期。

將邀請傳送至現有的內部帳戶，可讓您保留該使用者的物件識別碼、UPN、群組成員資格和應用程式指派。 您不需要手動刪除並重新邀請使用者或重新指派資源。 若要邀請使用者，您將使用邀請 API 來傳遞內部使用者物件和來賓使用者的電子郵件地址以及邀請。 當使用者接受邀請時，B2B 服務會將現有的內部使用者物件變更為 B2B 使用者。 之後，使用者必須使用其 B2B 認證登入雲端資源服務。 他們仍可使用其內部認證來存取內部部署資源，但您可以重設或變更內部帳戶的密碼，以避免發生這種情況。

> [!NOTE]
> 邀請是單向的。 您可以邀請內部使用者使用 B2B 共同作業，但在新增之後，您就無法移除 B2B 認證。 若要將使用者變更回僅供內部使用的使用者，您必須刪除該使用者物件並建立一個新的。

在公開預覽期間，這篇文章中所述用來邀請內部使用者進行 B2B 共同作業的方法，無法在這些情況下使用：

- 已將 Exchange 授權指派給內部使用者。
- 使用者來自于您目錄中設定為直接同盟的網域。
- 內部使用者是僅限雲端的帳戶，而其主要帳戶不在 Azure AD 中。

在這些情況下，如果內部使用者必須變更為 B2B 使用者，您應該刪除內部帳戶，並將 B2B 共同作業的邀請傳送給使用者。

內部**部署同步**處理的使用者：對於在內部部署與雲端之間同步處理的使用者帳戶，內部部署目錄會在其受邀使用 B2B 共同作業之後，維持授權來源。 您對內部部署帳戶所做的任何變更都會同步至雲端帳戶，包括停用或刪除帳戶。 因此，您無法防止使用者登入其內部部署帳戶，同時保留其雲端帳戶，只要刪除內部部署帳戶即可。 相反地，您可以將內部部署帳戶密碼設定為隨機 GUID 或其他未知值。

## <a name="how-to-invite-internal-users-to-b2b-collaboration"></a>如何邀請內部使用者進行 B2B 共同作業

您可以使用 PowerShell 或邀請 API 將 B2B 邀請傳送給內部使用者。 請確定您要用於邀請的電子郵件地址已設定為內部使用者物件的外部電子郵件地址。

- 針對僅限雲端的使用者，請使用 OtherMails 屬性中的電子郵件地址來取得邀請。
- 若為內部部署同步處理的使用者，您必須使用 [使用者] 的 [郵件] 屬性中的值來取得邀請。
- 使用者的 Mail 屬性中的網域必須與他們用來登入的帳戶相符。 否則，某些服務（例如小組）將無法驗證使用者。

根據預設，邀請會傳送電子郵件給使用者，告知他們已獲邀，但您可以隱藏這封電子郵件，並改為傳送您自己的電子郵件。

> [!NOTE]
> 若要傳送您自己的電子郵件或其他通訊，您可以使用 New-AzureADMSInvitation 與-SendInvitationMessage： $false 以無訊息方式邀請使用者，然後將您自己的電子郵件訊息傳送給已轉換的使用者。 請參閱 [AZURE AD B2B 共同作業 API 和自訂](customize-invitation-api.md)。

## <a name="use-powershell-to-send-a-b2b-invitation"></a>使用 PowerShell 傳送 B2B 邀請

使用下列命令，邀請使用者進行 B2B 共同作業：

```powershell
Uninstall-Module AzureADPreview
Install-Module AzureADPreview
$ADGraphUser = Get-AzureADUser -objectID "UPN of Internal User"
$msGraphUser = New-Object Microsoft.Open.MSGraph.Model.User -ArgumentList $ADGraphUser.ObjectId
New-AzureADMSInvitation -InvitedUserEmailAddress <<external email>> -SendInvitationMessage $True -InviteRedirectUrl "http://myapps.microsoft.com" -InvitedUser $msGraphUser
```

## <a name="use-the-invitation-api-to-send-a-b2b-invitation"></a>使用邀請 API 傳送 B2B 邀請

下列範例說明如何呼叫邀請 API，以將內部使用者邀請為 B2B 使用者。

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

對 API 的回應，與您邀請新的來賓使用者到目錄時所取得的回應相同。

## <a name="next-steps"></a>後續步驟

- [B2B 共同作業邀請兌換](redemption-experience.md)
