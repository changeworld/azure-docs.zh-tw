---
title: 疑難排解發行者驗證 |蔚藍
titleSuffix: Microsoft identity platform
description: 說明如何藉由呼叫 Microsoft Graph Api，針對 Microsoft 身分識別平臺進行發行者驗證的疑難排解。
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
ms.openlocfilehash: 10609f2706d257dbe5d8f43b85da5f06cb986cae
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2021
ms.locfileid: "98756187"
---
# <a name="troubleshoot-publisher-verification"></a>針對發行者驗證進行疑難排解
如果您無法完成此程式，或在 [發行者驗證](publisher-verification-overview.md)中遇到非預期的行為，您應該先執行下列動作，如果您收到錯誤或看到非預期的行為： 

1. 檢閱[需求](publisher-verification-overview.md#requirements)並確保全都符合。

1. 檢閱[將應用程式標示為發行者已驗證](mark-app-as-publisher-verified.md)的指示，並確保已成功執行所有步驟。

1. 檢閱[常見問題](#common-issues)的清單。

1. 使用 [Graph 總管](#making-microsoft-graph-api-calls)來重現要求，以收集其他資訊並排除 UI 中的任何問題。

## <a name="common-issues"></a>常見的問題
以下是在此過程中可能發生的一些常見問題。 

- **我不知道我的 Microsoft 合作夥伴網路識別碼 (MPN 識別碼) 或我不知道該帳戶的主要連絡人是誰** 
    1. 瀏覽至 [MPN 註冊頁面](https://partner.microsoft.com/dashboard/account/v3/enrollment/joinnow/basicpartnernetwork/new)
    1. 在組織的主要 Azure AD 租用戶中以使用者帳戶登入 
    1. 如果 MPN 帳戶已經存在，將會加以辨識，而且您會新增至該帳戶 
    1. 瀏覽至[合作夥伴設定檔頁面](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile)，其中會列出 MPN 識別碼和主要帳戶連絡人

- **我不知道我的 Azure AD 全域管理員 (也稱為公司系統管理員或租使用者系統管理員) 是，如何找到它們？應用程式系統管理員或雲端應用程式系統管理員如何？**
    1. 使用貴組織的主要租用戶中的使用者帳戶登入 [Azure AD 入口網站](https://aad.portal.azure.com)
    1. 瀏覽至[角色管理](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators)
    1. 按一下所需的系統管理員角色
    1. 將會出現獲派該角色的使用者清單。

- **我不知道誰是我的 MPN 帳戶的管理員** 前往 [MPN 使用者管理頁面](https://partner.microsoft.com/pcv/users)並篩選使用者清單，以查看使用者屬於何種不同的管理員角色。

- **我收到錯誤，指出我的 MPN 識別碼無效或我沒有其存取權。**
    1. 移至您的[合作夥伴設定檔](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile)並確認： 
        - MPN 識別碼正確無誤。 
        - 未顯示任何錯誤或「擱置動作」，而且合法商務設定檔與合作夥伴資訊底下的驗證狀態都顯示「已授權」或「成功」。
    1. 移至 [MPN 租用戶管理頁面](https://partner.microsoft.com/dashboard/account/v3/tenantmanagement)，並確認應用程式註冊所在的租用戶和您以使用者帳戶登入的租用戶位於相關聯的租用戶清單上。 如果您需要新增額外的租使用者，請遵循 [此處](/partner-center/multi-tenant-account)的指示。 請注意，您新增的任何租使用者的全域管理員都會獲得合作夥伴中心帳戶的全域管理員許可權。
    1. 移至 [ [MPN 使用者管理] 頁面](https://partner.microsoft.com/pcv/users) ，並確認您用來登入的使用者為 [全域管理員]、[MPN 管理員] 或 [帳戶管理員]。如果您需要將使用者新增至合作夥伴中心中的角色，請遵循 [此處](/partner-center/create-user-accounts-and-set-permissions)的指示。

- **當我登入 Azure AD 入口網站時，我沒看到任何已註冊的應用程式。原因為何？** 
    您的應用程式註冊可能是使用此租使用者中不同的使用者帳戶、個人/取用者帳戶或不同的租使用者所建立。 確保您在建立應用程式註冊的租用戶中使用正確的帳戶進行登入。

- **我收到與多重要素驗證相關的錯誤。我該怎麼做？** 
    請確定已啟用 [多重要素驗證](../fundamentals/concept-fundamentals-mfa-get-started.md) ，且您在此案例中登入的使用者 **需要** 此驗證。 例如，MFA 可能是：
    - 您要用來登入的使用者一律需要
    - [Azure 管理的必要元件](../conditional-access/howto-conditional-access-policy-azure-management.md)。
    - 您用來登入的[系統管理員類型的必要](../conditional-access/howto-conditional-access-policy-admin-mfa.md)項。

## <a name="making-microsoft-graph-api-calls"></a>進行 Microsoft Graph API 呼叫 

如果您遇到問題，但無法根據您在 UI 中看到的內容來了解原因，建議使用 Microsoft Graph 呼叫來執行進一步的疑難排解，以執行您可在應用程式註冊入口網站中執行的相同作業。

提出這些要求的最簡單方式就是使用 [Graph 總管](https://developer.microsoft.com/graph/graph-explorer)。 您也可考慮使用 [Postman](https://www.postman.com/) 之類的其他選項，或使用 PowerShell 來[叫用 Web 要求](/powershell/module/microsoft.powershell.utility/invoke-webrequest)。  

您可以使用 Microsoft Graph 來設定和取消設定應用程式的已驗證發行者，並在執行其中一項作業後檢查結果。 在您的應用程式註冊所對應的[應用程式](/graph/api/resources/application)物件，以及已從該應用程式具現化的任何[服務主體](/graph/api/resources/serviceprincipal)上，都可以看到結果。 如需這些物件之間關聯性的詳細資訊，請參閱：[Azure Active Directory 中的應用程式和服務主體物件](app-objects-and-service-principals.md)。  

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
GET https://graph.microsoft.com/v1.0/applications/0cd04273-0d11-4e62-9eb3-5c3971a7cbec 

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
GET https://graph.microsoft.com/v1.0/servicePrincipals/010422a7-4d77-4f40-9335-b81ef5c23dd4 

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
    
最常見的原因是登入的使用者不是合作夥伴中心中 MPN 帳戶的適當角色成員，請參閱合格角色清單的 [需求](publisher-verification-overview.md#requirements) ，並查看 [常見問題](#common-issues) 以取得詳細資訊。 也可能是因為應用程式註冊的租使用者未新增至 MPN 帳戶，或 MPN 識別碼無效而造成。

### <a name="mpnglobalaccountnotfound"></a>MPNGlobalAccountNotFound     

您提供的 MPN 識別碼 (<MPNID>) 無效。 提供有效的 MPN 識別碼，然後再試一次。
    
最常見的原因是提供的 MPN 識別碼對應至夥伴位置帳戶 (PLA) 。 僅支援合作夥伴通用帳戶。 如需詳細資訊，請參閱 [合作夥伴中心帳戶結構](/partner-center/account-structure) 。

### <a name="mpnaccountinvalid"></a>MPNAccountInvalid    

您提供的 MPN 識別碼 (<MPNID>) 無效。 提供有效的 MPN 識別碼，然後再試一次。
    
最常見的原因是提供了錯誤的 MPN 識別碼。

### <a name="mpnaccountnotvetted"></a>MPNAccountNotVetted  

您提供的 MPN 識別碼 (<MPNID>) 尚未完成調查程序。 在合作夥伴中心完成此程序，然後再試一次。 
    
最常見的原因是 MPN 帳戶未完成 [驗證](/partner-center/verification-responses) 程式。

### <a name="nopublisheridonassociatedmpnaccount"></a>NoPublisherIdOnAssociatedMPNAccount  

您提供的 MPN 識別碼 (<MPNID>) 無效。 提供有效的 MPN 識別碼，然後再試一次。 
   
最常見的原因是提供了錯誤的 MPN 識別碼。

### <a name="mpniddoesnotmatchassociatedmpnaccount"></a>MPNIdDoesNotMatchAssociatedMPNAccount    

您提供的 MPN 識別碼 (<MPNID>) 無效。 提供有效的 MPN 識別碼，然後再試一次。
    
最常見的原因是提供了錯誤的 MPN 識別碼。

### <a name="applicationnotfound"></a>ApplicationNotFound  

找不到目標應用程式 (<AppId>)。 提供有效的應用程式識別碼，然後再試一次。
    
最常見的原因是透過圖形 API 執行驗證，而且提供的應用程式識別碼不正確。 注意-必須提供應用程式的識別碼，而不是 AppId/ClientId。

### <a name="b2ctenantnotallowed"></a>B2CTenantNotAllowed  

Azure AD B2C 租用戶不支援此功能。 

### <a name="emailverifiedtenantnotallowed"></a>EmailVerifiedTenantNotAllowed    

透過電子郵件驗證的租用戶不支援此功能。 

### <a name="nopublisherdomainonapplication"></a>NoPublisherDomainOnApplication   

目標應用程式 (\<AppId\>) 必須設定發行者網域。 設定發行者網域，然後再試一次。

當應用程式上未設定 [發行者網域](howto-configure-publisher-domain.md) 時發生。

### <a name="publisherdomainmismatch"></a>PublisherDomainMismatch  

目標應用程式的發行者網域 (<publisherDomain>) 與合作夥伴中心中用來執行電子郵件驗證的網域 (<pcDomain>) 不符。 請確認這些網域相符，然後再試一次。 
    
當應用程式的 [發行者網域](howto-configure-publisher-domain.md) 或其中一個 [自訂網域](../fundamentals/add-custom-domain.md) 都未新增至 Azure AD 租使用者時，就會符合合作夥伴中心中用來執行電子郵件驗證的網域。

### <a name="notauthorizedtoverifypublisher"></a>NotAuthorizedToVerifyPublisher   

您無權在此應用程式上設定已驗證的發行者屬性 (<AppId>) 
  
最常見的原因是登入的使用者不是 Azure AD 中 MPN 帳戶的適當角色成員，請參閱合格角色清單的 [需求](publisher-verification-overview.md#requirements) ，並查看 [常見問題](#common-issues) 以取得詳細資訊。

### <a name="mpnidwasnotprovided"></a>MPNIdWasNotProvided  

未在要求本文中提供 MPN 識別碼，或要求內容類型不是 "application/json"。 

### <a name="msanotsupported"></a>MSANotSupported  

Microsoft 取用者帳戶不支援此功能。 僅支援 Azure AD 使用者在 Azure AD 中註冊的應用程式。

### <a name="interactionrequired"></a>InteractionRequired

在嘗試將已驗證的發行者新增至應用程式之前，尚未執行多重要素驗證時發生。 如需詳細資訊，請參閱 [常見的問題](#common-issues) 。 注意：嘗試新增已驗證的發行者時，必須在相同的會話中執行 MFA。 如果已啟用 MFA，但不需要在會話中執行，則要求將會失敗。   

顯示的錯誤訊息為：「由於系統管理員所做的設定變更，或是因為您移至新的位置，您必須使用多重要素驗證才能繼續。」

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