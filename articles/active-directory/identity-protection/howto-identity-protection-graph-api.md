---
title: 適用于 Azure Active Directory Identity Protection 的 Microsoft Graph API
description: 瞭解如何從 Azure Active Directory 查詢 Microsoft Graph 風險偵測和相關資訊
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 07/14/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81c3715995d0d53cd72acf7277f46a279fa4cfb2
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87019653"
---
# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>開始使用 Azure Active Directory Identity Protection 和 Microsoft Graph

Microsoft Graph 是 Microsoft 統一 API 端點，也是 [Azure Active Directory Identity Protection](../active-directory-identityprotection.md) API 的寄居地。 有三個 Api 會公開有風險的使用者和登入的相關資訊。第一個 API **riskDetection**可讓您查詢 Microsoft Graph，以取得使用者和登入連結的風險偵測和相關資訊的清單。 第二個 API **riskyUsers** 可讓您查詢 Microsoft Graph 中，Identity Protection 偵測為風險之使用者的相關資訊。 第三個 API **signIn** 可讓您查詢 Microsoft Graph 中，具有與風險狀態、詳細資料以及層級相關特定屬性之 Azure AD 登入的相關資訊。 

本文可讓您協助開始連線至 Microsoft Graph 並查詢這些 API。 如需深入的簡介、完整文件以及 Graph 總管的存取權，請參閱 [Microsoft Graph 網站](https://graph.microsoft.io/)或這些 API 的特定參考文件：

* [riskDetection API](/graph/api/resources/riskdetection?view=graph-rest-v1.0)
* [riskyUsers API](/graph/api/resources/riskyuser?view=graph-rest-v1.0)
* [signIn API](/graph/api/resources/signin?view=graph-rest-v1.0)

## <a name="connect-to-microsoft-graph"></a>連線至 Microsoft Graph

透過 Microsoft Graph 存取 Identity Protection 資料需要四個步驟︰

- [擷取網域名稱](#retrieve-your-domain-name)
- [建立新的應用程式註冊](#create-a-new-app-registration)
- [設定 API 許可權](#configure-api-permissions)
- [設定有效的認證](#configure-a-valid-credential)

### <a name="retrieve-your-domain-name"></a>擷取網域名稱 

1. 登入 [Azure 入口網站](https://portal.azure.com)。  
1. 流覽至**Azure Active Directory**  >  的**自訂功能變數名稱**。 
1. 記下 `.onmicrosoft.com` 網域，稍後的步驟將會需要這項資訊。

### <a name="create-a-new-app-registration"></a>建立新的應用程式註冊

1. 在 [Azure 入口網站中，流覽至**Azure Active Directory**  >  **應用程式註冊**。
1. 選取 [新增註冊]。
1. 在 [**建立**] 頁面上，執行下列步驟：
   1. 在 [**名稱**] 文字方塊中，輸入應用程式的名稱（例如： Azure AD 風險偵測 API）。
   1. 在 [**支援的帳戶類型**] 底下，選取將使用 api 的帳戶類型。
   1. 選取 [註冊]。
1. 複製 [**應用程式識別碼**]。

### <a name="configure-api-permissions"></a>設定 API 許可權

1. 從您建立的**應用程式**中，選取 [ **API 許可權**]。
1. 在 [**設定的許可權**] 頁面上，按一下頂端工具列中的 [**新增許可權**]。
1. 在 [新增 API 存取權]**** 頁面中，按一下 [選取 API]****。
1. 在 [選取 API]**** 頁面中，選取 [Microsoft Graph]****，然後按一下 [選取]****。
1. 在 [**要求 API 許可權**] 頁面上： 
   1. 選取 [應用程式權限]。
   1. 選取 [和] 旁的核取方塊 `IdentityRiskEvent.Read.All` `IdentityRiskyUser.Read.All` 。
   1. 選取 [新增權限]。
1. 選取 **[授與網域的系統管理員同意**] 

### <a name="configure-a-valid-credential"></a>設定有效的認證

1. 從您建立的**應用程式**中，選取 [**憑證 & 密碼**]。
1. 在 [用戶端密碼]**** 底下，選取 [新增用戶端密碼]****。
   1. 提供用戶端密碼的**描述**，並根據您的組織原則設定到期時間週期。
   1. 選取 [新增]。

   > [!NOTE]
   > 如果遺失此金鑰，則必須返回本區段並建立新的金鑰。 請勿將此金鑰告知他人︰任何人只要擁有此金鑰就可以存取您的資料。

## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-detections-api"></a>驗證以 Microsoft Graph 及查詢身分識別風險偵測 API

到目前為止，您應該已擁有︰

- 租用戶網域的名稱
- 應用程式（用戶端）識別碼 
- 用戶端密碼或憑證 

若要驗證，請傳送 post 要求至 `https://login.microsoft.com` ，並在主體中加入下列參數︰

- grant_type: “**client_credentials**”
- resource：`https://graph.microsoft.com`
- client_id: \<your client ID\>
- client_secret: \<your key\>

如果成功，此要求會傳回驗證權杖。  
若要呼叫 API，請建立具有下列參數的標頭︰

```
`Authorization`="<token_type> <access_token>"
```

驗證時，您可以在傳回的權杖中找到權杖類型和存取權杖。

將此標頭做為要求來傳送至下列 API URL： `https://graph.microsoft.com/v1.0/identityProtection/riskDetections`

如果成功，回應就是身分識別風險偵測的集合和 OData JSON 格式的相關資料，可依您的情況加以剖析和處理。

### <a name="sample"></a>範例

以下是使用 PowerShell 驗證並呼叫 API 的範例程式碼。  
您只需要新增用戶端識別碼、祕密金鑰和租用戶網域。

```PowerShell
    $ClientID       = "<your client ID here>"        # Should be a ~36 hex character string; insert your info here
    $ClientSecret   = "<your client secret here>"    # Should be a ~44 character string; insert your info here
    $tenantdomain   = "<your tenant domain here>"    # For example, contoso.onmicrosoft.com

    $loginURL       = "https://login.microsoft.com"
    $resource       = "https://graph.microsoft.com"

    $body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
    $oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    Write-Output $oauth

    if ($oauth.access_token -ne $null) {
        $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

        $url = "https://graph.microsoft.com/v1.0/identityProtection/riskDetections"
        Write-Output $url

        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)

        foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
            Write-Output $event
        }

    } else {
        Write-Host "ERROR: No Access Token"
    } 
```

## <a name="query-the-apis"></a>查詢 API

這三個 API 會提供許多機會，在您的組織中擷取有風險的使用者和登入的相關資訊。 以下是這些 API 和相關聯範例要求的一些常見使用案例。 您可以使用上述的範例程式碼，或使用 [Graph 總管](https://developer.microsoft.com/graph/graph-explorer)執行這些查詢。

### <a name="get-all-of-the-offline-risk-detections-riskdetection-api"></a>取得所有離線風險偵測（riskDetection API）

使用 Identity Protection 登入風險原則時，您可以在即時偵測到風險時套用條件。 但離線探索到的偵測會如何？ 若要瞭解哪些偵測離線發生，因而無法觸發登入風險原則，您可以查詢 riskDetection API。

```
GET https://graph.microsoft.com/v1.0/identityProtection/riskDetections?$filter=detectionTimingType eq 'offline'
```

### <a name="get-all-of-the-users-who-successfully-passed-an-mfa-challenge-triggered-by-risky-sign-ins-policy-riskyusers-api"></a>取得成功通過有危險的登入原則所觸發之 MFA 挑戰的所有使用者 (riskyUsers API)

若要瞭解以身分識別保護風險為基礎的原則對您的組織有何影響，您可以查詢成功通過具風險登入原則所觸發之 MFA 挑戰的所有使用者。 此資訊可協助您了解哪些使用者 Identity Protection 可能會被誤偵測為風險，以及哪些合法使用者可能正在執行 AI 認為有風險的動作。

```
GET https://graph.microsoft.com/v1.0/identityProtection/riskyUsers?$filter=riskDetail eq 'userPassedMFADrivenByRiskBasedPolicy'
```

## <a name="next-steps"></a>接下來的步驟

恭喜，您剛剛完成了對 Microsoft Graph 的第一次呼叫！  
現在您可以查詢身分識別風險偵測，並在您看到 [適合] 時使用資料。

若要深入了解 Microsoft Graph 以及如何使用 Graph API 來建置應用程式，請查看[說明文件](/graph/overview)，而在 [Microsoft Graph 網站](https://developer.microsoft.com/graph)上還能找到更多資訊。 

如需相關資訊，請參閱：

- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
- [Azure Active Directory Identity Protection 偵測到的風險偵測類型](../reports-monitoring/concept-risk-events.md)
- [Microsoft Graph](https://developer.microsoft.com/graph/)
- [Microsoft Graph 概觀](https://developer.microsoft.com/graph/docs)
- [Azure AD Identity Protection 服務根目錄](https://docs.microsoft.com/graph/api/resources/identityprotectionroot?view=graph-rest-1.0)
