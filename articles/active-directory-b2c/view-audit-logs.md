---
title: 存取和審查審核記錄
titleSuffix: Azure AD B2C
description: 如何以程式設計方式和 Azure 入口網站存取 Azure AD B2C audit 記錄檔。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 02/20/2020
ms.author: marsma
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 62a969519ebefaab919505d9c8faae830f55f4c6
ms.sourcegitcommit: 934776a860e4944f1a0e5e24763bfe3855bc6b60
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/20/2020
ms.locfileid: "77505619"
---
# <a name="accessing-azure-ad-b2c-audit-logs"></a>存取 Azure AD B2C 稽核記錄

Azure Active Directory B2C （Azure AD B2C）會發出 audit 記錄，其中包含 B2C 資源、發出的權杖和系統管理員存取的相關活動資訊。 本文提供 audit 記錄中可用資訊的簡要總覽，以及如何為您的 Azure AD B2C 租使用者存取此資料的指示。

Audit log 事件只會保留**七天**。 如果您需要更長的保留期，請使用以下所示的其中一個方法來規劃下載與儲存您的記錄。

> [!NOTE]
> 您無法在 Azure 入口網站的 [ **Azure Active Directory** ] 或 [ **Azure AD B2C** ] 頁面的 [**使用者**] 區段底下，看到個別 Azure AD B2C 應用程式的使用者登入。 登入事件顯示使用者活動，但無法與使用者已登入的 B2C 應用程式相互關聯。 您必須使用該的 audit 記錄檔，如本文中的進一步說明。

## <a name="overview-of-activities-available-in-the-b2c-category-of-audit-logs"></a>稽核記錄 B2C 類別中可取得活動的概觀

稽核記錄中的 **B2C** 類別包含以下類型的活動：

|活動類型 |描述  |
|---------|---------|
|授權 |有關授權使用者存取 B2C 資源的活動（例如，存取 B2C 原則清單的系統管理員）。         |
|目錄 |當系統管理員使用 Azure 入口網站登入時，所抓取之目錄屬性的相關活動。 |
|Application | 在 B2C 應用程式上建立、讀取、更新和刪除（CRUD）作業。 |
|Key |在 B2C 金鑰容器中儲存之金鑰的 CRUD 作業。 |
|資源 |B2C 資源的 CRUD 作業。 例如，原則和識別提供者。
|驗證 |驗證使用者認證和權杖發行。|

如需使用者物件 CRUD 活動，請參閱**核心目錄**類別。

## <a name="example-activity"></a>範例活動

此 Azure 入口網站中的範例影像會顯示當使用者以外部識別提供者登入時所捕捉到的資料（在此案例中為 Facebook）：

![Azure 入口網站中的 [Audit 記錄活動詳細資料] 頁面範例](./media/view-audit-logs/audit-logs-example.png)

[活動詳細資料] 面板包含下列相關資訊：

|區段|欄位|描述|
|-------|-----|-----------|
| 活動 | 名稱 | 發生了哪個活動。 例如，對*應用程式發出 id_token*，這會結束實際的使用者登入。 |
| 啟動者 (執行者) | ObjectId | 使用者所登入之 B2C 應用程式的**物件識別碼**。 此識別碼不會顯示在 Azure 入口網站中，但可透過 Microsoft Graph API 存取。 |
| 啟動者 (執行者) | Spn | 使用者所登入之 B2C 應用程式的**應用程式識別碼**。 |
| 目標 | ObjectId | 登入之使用者的**物件識別碼**。 |
| 其他詳細資料 | TenantId | Azure AD B2C 租使用者的**租使用者識別碼**。 |
| 其他詳細資料 | PolicyId | 用來將使用者登入之使用者流程（原則）的**原則識別碼**。 |
| 其他詳細資料 | ApplicationId | 使用者所登入之 B2C 應用程式的**應用程式識別碼**。 |

## <a name="view-audit-logs-in-the-azure-portal"></a>查看 Azure 入口網站中的 audit 記錄

Azure 入口網站提供 Azure AD B2C 租使用者中 audit 記錄事件的存取權。

1. 登入 [Azure 入口網站](https://portal.azure.com)
1. 切換至包含您 Azure AD B2C 租使用者的目錄，然後流覽至**Azure AD B2C**。
1. 在左側功能表中的 [**活動**] 底下，選取 [ **Audit logs**]。

在過去七天內記錄的活動事件清單隨即顯示。

![在 Azure 入口網站中使用兩個活動事件的範例篩選](./media/view-audit-logs/audit-logs-example-filter.png)

有數個篩選選項可供使用，包括：

* **活動資源類型**-依可用的活動[總覽](#overview-of-activities-available-in-the-b2c-category-of-audit-logs)一節中的表格所顯示的活動類型篩選。
* **日期**-篩選所顯示活動的日期範圍。

如果您選取清單中的資料列，則會顯示事件的活動詳細資料。

若要下載逗號分隔值（CSV）檔案中的活動事件清單，請選取 [**下載**]。

## <a name="get-audit-logs-with-the-azure-ad-reporting-api"></a>使用 Azure AD 報告 API 取得 audit 記錄

稽核記錄的發行管線與 Azure Active Directory 的其他活動相同，因此可以透過 [Azure Active Directory 報告 API](https://docs.microsoft.com/graph/api/directoryaudit-list) \(機器翻譯\) 來存取。 如需詳細資訊，請參閱[開始使用 Azure Active Directory 報告 API](../active-directory/reports-monitoring/concept-reporting-api.md)。

### <a name="enable-reporting-api-access"></a>啟用報告 API 存取

若要允許以腳本或應用程式為基礎的 Azure AD 報告 API 存取，您需要在 Azure AD B2C 租使用者中註冊的應用程式，並具備下列 API 許可權。 您可以在 B2C 租使用者中的現有應用程式註冊上啟用這些許可權，或建立一個專門用於 audit 記錄自動化的新帳戶。

* Microsoft Graph > 應用程式許可權 > AuditLog > AuditLog. 全部

請遵循下列文章中的步驟，向應用程式註冊所需的許可權：

[使用 Microsoft Graph 管理 Azure AD B2C](microsoft-graph-get-started.md)

使用適當的許可權註冊應用程式之後，請參閱本文稍後的 PowerShell 腳本一節，以取得如何使用腳本取得活動事件的範例。

### <a name="access-the-api"></a>存取 API

若要透過 API 下載 Azure AD B2C audit 記錄事件，請篩選 `B2C` 類別目錄上的記錄檔。 若要依類別篩選，請在呼叫 Azure AD 報告 API 端點時，使用 `filter` 查詢字串參數。

```HTTP
https://graph.microsoft.com/v1.0/auditLogs/directoryAudits?$filter=loggedByService eq 'B2C' and activityDateTime gt 2019-09-10T02:28:17Z
```

### <a name="powershell-script"></a>PowerShell 指令碼

下列 PowerShell 腳本顯示如何查詢 Azure AD 報告 API 的範例。 查詢 API 之後，它會將記錄的事件列印到標準輸出，然後將 JSON 輸出寫入檔案。

您可以在[Azure Cloud Shell](overview.md)中嘗試此腳本。 請務必使用您的應用程式識別碼、用戶端密碼和 Azure AD B2C 租使用者的名稱來更新它。

```powershell
# This script requires an application registration that's granted Microsoft Graph API permission
# https://docs.microsoft.com/azure/active-directory-b2c/microsoft-graph-get-started

# Constants
$ClientID       = "your-client-application-id-here"       # Insert your application's client ID, a GUID
$ClientSecret   = "your-client-application-secret-here"   # Insert your application's client secret
$tenantdomain   = "your-b2c-tenant.onmicrosoft.com"       # Insert your Azure AD B2C tenant domain name

$loginURL       = "https://login.microsoftonline.com"
$resource       = "https://graph.microsoft.com"           # Microsoft Graph API resource URI
$7daysago       = "{0:s}" -f (get-date).AddDays(-7) + "Z" # Use 'AddMinutes(-5)' to decrement minutes, for example
Write-Output "Searching for events starting $7daysago"

# Create HTTP header, get an OAuth2 access token based on client id, secret and tenant domain
$body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
$oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

# Parse audit report items, save output to file(s): auditX.json, where X = 0 through n for number of nextLink pages
if ($oauth.access_token -ne $null) {
    $i=0
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
    $url = "https://graph.microsoft.com/v1.0/auditLogs/directoryAudits?`$filter=loggedByService eq 'B2C' and activityDateTime gt  " + $7daysago

    # loop through each query page (1 through n)
    Do {
        # display each event on the console window
        Write-Output "Fetching data using Uri: $url"
        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
        foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
            Write-Output ($event | ConvertTo-Json)
        }

        # save the query page to an output file
        Write-Output "Save the output to a file audit$i.json"
        $myReport.Content | Out-File -FilePath audit$i.json -Force
        $url = ($myReport.Content | ConvertFrom-Json).'@odata.nextLink'
        $i = $i+1
    } while($url -ne $null)
} else {
    Write-Host "ERROR: No Access Token"
}
```

以下是本文稍早所示之範例活動事件的 JSON 標記法：

```JSON
{
    "id": "B2C_DQO3J_4984536",
    "category": "Authentication",
    "correlationId": "00000000-0000-0000-0000-000000000000",
    "result": "success",
    "resultReason": "N/A",
    "activityDisplayName": "Issue an id_token to the application",
    "activityDateTime": "2019-09-14T18:13:17.0618117Z",
    "loggedByService": "B2C",
    "operationType": "",
    "initiatedBy": {
        "user": null,
        "app": {
            "appId": "00000000-0000-0000-0000-000000000000",
            "displayName": null,
            "servicePrincipalId": null,
            "servicePrincipalName": "00000000-0000-0000-0000-000000000000"
        }
    },
    "targetResources": [
        {
            "id": "00000000-0000-0000-0000-000000000000",
            "displayName": null,
            "type": "User",
            "userPrincipalName": null,
            "groupType": null,
            "modifiedProperties": []
        }
    ],
    "additionalDetails": [
        {
            "key": "TenantId",
            "value": "test.onmicrosoft.com"
        },
        {
            "key": "PolicyId",
            "value": "B2C_1A_signup_signin"
        },
        {
            "key": "ApplicationId",
            "value": "00000000-0000-0000-0000-000000000000"
        },
        {
            "key": "Client",
            "value": "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.132 Safari/537.36"
        },
        {
            "key": "IdentityProviderName",
            "value": "facebook"
        },
        {
            "key": "IdentityProviderApplicationId",
            "value": "0000000000000000"
        },
        {
            "key": "ClientIpAddress",
            "value": "127.0.0.1"
        }
    ]
}
```

## <a name="next-steps"></a>後續步驟

您可以自動化其他管理工作，例如，[使用 Microsoft Graph 來管理 Azure AD B2C 的使用者帳戶](manage-user-accounts-graph-api.md)。
