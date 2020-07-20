---
title: 針對發行者驗證進行疑難排解 - Microsoft 身分識別平台 | Azure
description: 說明如何藉由呼叫 Microsoft Graph API，針對 Microsoft 身分識別平台的發行者驗證 (預覽) 進行疑難排解。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 05/08/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jesakowi
ms.openlocfilehash: dcdce6ee6683c5770f97f5f3dc20e1c9b409ead0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85477034"
---
# <a name="troubleshoot-publisher-verification-preview"></a>針對發行者驗證進行疑難排解 (預覽)
如果您無法完成此程序，或遇到非預期的[發行者驗證 (預覽)](publisher-verification-overview.md) 行為，則當您收到錯誤或看到非預期的行為時，應該先執行下列動作： 

1. 檢閱[需求](publisher-verification-overview.md#requirements)並確保全都符合。

1. 檢閱[將應用程式標示為發行者已驗證](mark-app-as-publisher-verified.md)的指示，並確保已成功執行所有步驟。

1. 檢閱[常見問題](#common-issues)的清單。

1. 使用 [Graph 總管](#making-microsoft-graph-api-calls)來重現要求，以收集其他資訊並排除 UI 中的任何問題。

## <a name="common-issues"></a>常見的問題
以下是在此過程中可能發生的一些常見問題。 

- **我不知道我的 Microsoft 合作夥伴網路識別碼 (MPN ID)，或不知道誰是帳戶的主要連絡人** 
    1. 瀏覽至 [MPN 註冊頁面](https://partner.microsoft.com/dashboard/account/v3/enrollment/joinnow/basicpartnernetwork/new)
    1. 在組織的主要 Azure AD 租用戶中以使用者帳戶登入 
    1. 如果 MPN 帳戶已經存在，將會加以辨識，而且您會新增至該帳戶 
    1. 瀏覽至[合作夥伴設定檔頁面](https://partner.microsoft.com/en-us/pcv/accountsettings/connectedpartnerprofile)，其中會列出 MPN 識別碼和主要帳戶連絡人

- **我不知道誰是我的 Azure AD 全域管理員 (也稱為公司系統管理員或租用戶系統管理員)，如何找出他們？應用程式管理員或不同的管理員角色怎麼樣？**
    1. 使用貴組織的主要租用戶中的使用者帳戶登入 [Azure AD 入口網站](https://aad.portal.azure.com)
    1. 瀏覽至[角色管理](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators)
    1. 按一下 [全域管理員] 或所需的管理員角色
    1. 將會出現獲派該角色的使用者清單。

- **我不知道誰是我的 MPN 帳戶的管理員** 前往 [MPN 使用者管理頁面](https://partner.microsoft.com/en-us/pcv/users)並篩選使用者清單，以查看使用者屬於何種不同的管理員角色。

- **我收到錯誤，指出我的 MPN 識別碼無效或我沒有其存取權。**
    1. 移至您的[合作夥伴設定檔](https://partner.microsoft.com/en-us/pcv/accountsettings/connectedpartnerprofile)並確認： 
        - MPN 識別碼正確無誤。 
        - 未顯示任何錯誤或「擱置動作」，而且合法商務設定檔與合作夥伴資訊底下的驗證狀態都顯示「已授權」或「成功」。
    1. 移至 [MPN 租用戶管理頁面](https://partner.microsoft.com/en-us/dashboard/account/v3/tenantmanagement)，並確認應用程式註冊所在的租用戶和您以使用者帳戶登入的租用戶位於相關聯的租用戶清單上。
    1. 移至 [MPN 使用者管理頁面](https://partner.microsoft.com/en-us/pcv/users)，並確認您登入的使用者是全域管理員、MPN 管理員或帳戶管理員。

- **當我登入 Azure AD 入口網站時，我沒看到任何已註冊的應用程式。原因為何？** 
    您的應用程式註冊可能是使用不同的使用者帳戶建立或在不同的租用戶中建立的。 確保您在建立應用程式註冊的租用戶中使用正確的帳戶進行登入。

- **如何知道 Azure AD 中應用程式註冊的擁有者是誰？** 
    登入已註冊應用程式的租用戶時，瀏覽至 [應用程式註冊] 刀鋒視窗，按一下應用程式，然後按一下 [擁有者]。

## <a name="making-microsoft-graph-api-calls"></a>進行 Microsoft Graph API 呼叫 

如果您遇到問題，但無法根據您在 UI 中看到的內容來了解原因，建議使用 Microsoft Graph 呼叫來執行進一步的疑難排解，以執行您可在應用程式註冊入口網站中執行的相同作業。 在預覽階段，這些 API 僅適用於 Microsoft Graph 的 /beta 端點。  

提出這些要求的最簡單方式就是使用 [Graph 總管](https://developer.microsoft.com/graph/graph-explorer)。 您也可考慮使用 [Postman](https://www.postman.com/) 之類的其他選項，或使用 PowerShell 來[叫用 Web 要求](/powershell/module/microsoft.powershell.utility/invoke-webrequest?view=powershell-7)。  

您可以使用 Microsoft Graph 來設定和取消設定應用程式的已驗證發行者，並在執行其中一項作業後檢查結果。 在您的應用程式註冊所對應的[應用程式](/graph/api/resources/application?view=graph-rest-beta)物件，以及已從該應用程式具現化的任何[服務主體](/graph/api/resources/serviceprincipal?view=graph-rest-beta)上，都可以看到結果。 如需這些物件之間關聯性的詳細資訊，請參閱：[Azure Active Directory 中的應用程式和服務主體物件](app-objects-and-service-principals.md)。  

以下是一些實用的要求範例：  

### <a name="set-verified-publisher"></a>設定已驗證的發行者 

要求

```
POST /applications/0cd04273-0d11-4e62-9eb3-5c3971a7cbec/setVerifiedPublisher 

{ 

    "verifiedPublisherId": "12345678" 

} 
```
 
回應 
```
204 No Content 
```
> [!NOTE]
> *verifiedPublisherID* 是您的 MPN 識別碼。 

### <a name="unset-verified-publisher"></a>取消設定已驗證的發行者 

要求：  
```
POST /applications/0cd04273-0d11-4e62-9eb3-5c3971a7cbec/unsetVerifiedPublisher 
```
 
回應 
```
204 No Content 
```
### <a name="get-verified-publisher-info-from-application"></a>從應用程式取得已驗證的發行者資訊 
 
```
GET https://graph.microsoft.com/beta/applications/0cd04273-0d11-4e62-9eb3-5c3971a7cbec 

HTTP/1.1 200 OK 

{ 
    "id": "0cd04273-0d11-4e62-9eb3-5c3971a7cbec", 

    ... 

    "verifiedPublisher" : { 
        "displayName": "myexamplePublisher", 
        "verifiedPublisherId": "12345678", 
        "addedDateTime": "2019-12-10T00:00:00" 
    } 
} 
```

### <a name="get-verified-publisher-info-from-service-principal"></a>從服務主體取得已驗證的發行者資訊 
```
GET https://graph.microsoft.com/beta/servicePrincipals/010422a7-4d77-4f40-9335-b81ef5c23dd4 

HTTP/1.1 200 OK 

{ 
    "id": "010422a7-4d77-4f40-9335-b81ef5c22dd4", 

    ... 

    "verifiedPublisher" : { 
        "displayName": "myexamplePublisher", 
        "verifiedPublisherId": "12345678", 
        "addedDateTime": "2019-12-10T00:00:00" 
    } 
} 
```

## <a name="error-reference"></a>錯誤參考 

以下是使用 Microsoft Graph 進行疑難排解，或在應用程式註冊入口網站中進行處理時，可能會收到的錯誤碼清單。

### <a name="mpnaccountnotfoundornoaccess"></a>MPNAccountNotFoundOrNoAccess     

您提供的 MPN 識別碼 (<MPNID>) 不存在，或您沒有其存取權。 提供有效的 MPN 識別碼，然後再試一次。 

### <a name="mpnglobalaccountnotfound"></a>MPNGlobalAccountNotFound     

您提供的 MPN 識別碼 (<MPNID>) 無效。 提供有效的 MPN 識別碼，然後再試一次。 

### <a name="mpnaccountinvalid"></a>MPNAccountInvalid    

您提供的 MPN 識別碼 (<MPNID>) 無效。 提供有效的 MPN 識別碼，然後再試一次。 

### <a name="mpnaccountnotvetted"></a>MPNAccountNotVetted  

您提供的 MPN 識別碼 (<MPNID>) 尚未完成調查程序。 在合作夥伴中心完成此程序，然後再試一次。 

### <a name="nopublisheridonassociatedmpnaccount"></a>NoPublisherIdOnAssociatedMPNAccount  

您提供的 MPN 識別碼 (<MPNID>) 無效。 提供有效的 MPN 識別碼，然後再試一次。 

### <a name="mpniddoesnotmatchassociatedmpnaccount"></a>MPNIdDoesNotMatchAssociatedMPNAccount    

您提供的 MPN 識別碼 (<MPNID>) 無效。 提供有效的 MPN 識別碼，然後再試一次。 

### <a name="applicationnotfound"></a>ApplicationNotFound  

找不到目標應用程式 (<AppId>)。 提供有效的應用程式識別碼，然後再試一次。 

### <a name="b2ctenantnotallowed"></a>B2CTenantNotAllowed  

Azure AD B2C 租用戶不支援此功能。 

### <a name="emailverifiedtenantnotallowed"></a>EmailVerifiedTenantNotAllowed    

透過電子郵件驗證的租用戶不支援此功能。 

### <a name="nopublisherdomainonapplication"></a>NoPublisherDomainOnApplication   

目標應用程式 (<AppId>) 確實必須設定發行者網域。 設定發行者網域，然後再試一次。 

### <a name="publisherdomainisnotdnsverified"></a>PublisherDomainIsNotDNSVerified  

目標應用程式的發行者網域 (<publisherDomain>) 不是此租用戶中已驗證的網域。 使用 DNS 驗證來驗證租用戶網域，然後再試一次。 

### <a name="publisherdomainmismatch"></a>PublisherDomainMismatch  

目標應用程式的發行者網域 (<publisherDomain>) 與合作夥伴中心中用來執行電子郵件驗證的網域 (<pcDomain>) 不符。 請確認這些網域相符，然後再試一次。 

### <a name="notauthorizedtoverifypublisher"></a>NotAuthorizedToVerifyPublisher   

您無權在此應用程式上設定已驗證的發行者屬性 (<AppId>) 

### <a name="mpnidwasnotprovided"></a>MPNIdWasNotProvided  

未在要求本文中提供 MPN 識別碼，或要求內容類型不是 "application/json"。 

### <a name="msanotsupported"></a>MSANotSupported  

Microsoft 取用者帳戶不支援此功能。 僅支援 Azure AD 使用者在 Azure AD 中註冊的應用程式。 

## <a name="next-steps"></a>後續步驟

如果您已檢閱先前所有的資訊，但仍收到來自 Microsoft Graph 的錯誤，請盡可能收集與失敗要求相關的下列資訊，並[聯絡 Microsoft 支援服務](developer-support-help-options.md#open-a-support-request)。

- 時間戳記 
- CorrelationId 
- 已登入使用者的 ObjectID 或 UserPrincipalName 
- 目標應用程式的 ObjectId
- 目標應用程式的 AppId
- 應用程式註冊所在的 TenantId
- MPN 識別碼
- 正在進行 REST 要求 
- 正在傳回的錯誤碼和訊息 
