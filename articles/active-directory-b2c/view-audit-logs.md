---
title: 訪問和查看稽核記錄
titleSuffix: Azure AD B2C
description: 如何以程式設計方式在 Azure 門戶中訪問 Azure AD B2C 稽核記錄。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 02/20/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 83086fa2cb96eba423b9111134a0406d7256821f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264215"
---
# <a name="accessing-azure-ad-b2c-audit-logs"></a>存取 Azure AD B2C 稽核記錄

Azure 活動目錄 B2C（Azure AD B2C）發出稽核記錄，其中包含有關 B2C 資源的活動資訊、頒發的權杖和管理員存取權限。 本文簡要概述了稽核記錄中提供的資訊，以及如何為 Azure AD B2C 租戶訪問此資料的說明。

稽核記錄事件僅保留**七天**。 如果您需要更長的保留期，請使用以下所示的其中一個方法來規劃下載與儲存您的記錄。

> [!NOTE]
> 在 Azure 門戶中的**Azure 活動目錄**或**Azure AD B2C**頁"**使用者"** 部分下，看不到單個 Azure AD B2C 應用程式的使用者登錄。 其中的登錄事件顯示使用者活動，但不能關聯回使用者登錄的 B2C 應用程式。 您必須為此使用稽核記錄，本文將進一步說明。

## <a name="overview-of-activities-available-in-the-b2c-category-of-audit-logs"></a>稽核記錄 B2C 類別中可取得活動的概觀

稽核記錄中的 **B2C** 類別包含以下類型的活動：

|活動類型 |描述  |
|---------|---------|
|授權 |有關使用者訪問 B2C 資源的授權的活動（例如，訪問 B2C 策略清單的管理員）。         |
|目錄 |管理員使用 Azure 門戶登錄時檢索與目錄屬性相關的活動。 |
|Application | 在 B2C 應用程式中創建、讀取、更新和刪除 （CRUD） 操作。 |
|Key |CRUD 對存儲在 B2C 金鑰容器中的金鑰執行操作。 |
|資源 |對 B2C 資源的 CRUD 操作。 例如，策略和標識提供程式。
|驗證 |驗證使用者憑據和權杖頒發。|

如需使用者物件 CRUD 活動，請參閱**核心目錄**類別。

## <a name="example-activity"></a>範例活動

Azure 門戶中的此示例圖像顯示使用者與外部標識提供程式登錄時捕獲的資料，在此示例中為 Facebook：

![Azure 門戶中的稽核記錄活動詳細資訊頁示例](./media/view-audit-logs/audit-logs-example.png)

活動詳細資訊面板包含以下相關資訊：

|區段|欄位|描述|
|-------|-----|-----------|
| 活動 | 名稱 | 發生了哪些活動。 例如，*向應用程式發出id_token，* 應用程式結束實際使用者登錄。 |
| 啟動者 (執行者) | ObjectId | 使用者登錄的 B2C 應用程式**的物件識別碼。** 此識別碼在 Azure 門戶中不可見，但可通過 Microsoft 圖形 API 訪問。 |
| 啟動者 (執行者) | Spn | 使用者登錄的 B2C**應用程式的應用程式 ID。** |
| 目標 | ObjectId | 正在登錄的使用者**的物件識別碼。** |
| 其他詳細資料 | TenantId | Azure AD B2C**租戶的租戶 ID。** |
| 其他詳細資料 | PolicyId | 用於登錄使用者流（策略）**的策略 ID。** |
| 其他詳細資料 | ApplicationId | 使用者登錄的 B2C**應用程式的應用程式 ID。** |

## <a name="view-audit-logs-in-the-azure-portal"></a>在 Azure 門戶中查看稽核記錄

Azure 門戶提供對 Azure AD B2C 租戶中的稽核記錄事件的訪問。

1. 登錄到 Azure[門戶](https://portal.azure.com)
1. 切換到包含 Azure AD B2C 租戶的目錄，然後流覽到**Azure AD B2C**。
1. 在左側功能表中的 **"活動"** 下，選擇 **"稽核記錄**"。

將顯示過去七天記錄的活動事件清單。

![Azure 門戶中具有兩個活動事件的示例篩選器](./media/view-audit-logs/audit-logs-example-filter.png)

提供多種篩選選項，包括：

* **活動資源類型**- 按"[可用活動概述](#overview-of-activities-available-in-the-b2c-category-of-audit-logs)"部分表中顯示的活動類型進行篩選。
* **日期**- 篩選顯示的活動的日期範圍。

如果在清單中選擇一行，將顯示事件的活動詳細資訊。

要下載逗號分隔值 （CSV） 檔中的活動事件清單，請選擇 **"下載**"。

## <a name="get-audit-logs-with-the-azure-ad-reporting-api"></a>使用 Azure AD 報告 API 獲取稽核記錄

稽核記錄的發行管線與 Azure Active Directory 的其他活動相同，因此可以透過 [Azure Active Directory 報告 API](https://docs.microsoft.com/graph/api/directoryaudit-list) \(機器翻譯\) 來存取。 有關詳細資訊，請參閱使用[Azure 活動目錄報告 API 入門](../active-directory/reports-monitoring/concept-reporting-api.md)。

### <a name="enable-reporting-api-access"></a>啟用報告 API 訪問

要允許基於腳本或應用程式的訪問 Azure AD 報告 API，您需要在 Azure AD B2C 租戶中註冊具有以下 API 許可權的應用程式。 您可以在 B2C 租戶中的現有應用程式註冊上啟用這些許可權，或者創建一個新許可權，專門用於稽核記錄自動化。

* Microsoft 圖形 >>稽核記錄>稽核記錄.閱讀.所有

按照以下文章中的步驟註冊具有所需許可權的應用程式：

[使用微軟圖形管理 Azure AD B2C](microsoft-graph-get-started.md)

註冊具有相應許可權的應用程式後，請參閱本文後面的 PowerShell 腳本部分，瞭解如何使用腳本獲取活動事件。

### <a name="access-the-api"></a>訪問 API

要通過 API 下載 Azure AD B2C 稽核記錄事件，`B2C`請篩選類別上的日誌。 要按類別篩選，在調用`filter`Azure AD 報告 API 終結點時，請使用查詢字串參數。

```HTTP
https://graph.microsoft.com/v1.0/auditLogs/directoryAudits?$filter=loggedByService eq 'B2C' and activityDateTime gt 2019-09-10T02:28:17Z
```

### <a name="powershell-script"></a>PowerShell 指令碼

以下 PowerShell 腳本顯示了如何查詢 Azure AD 報告 API 的示例。 查詢 API 後，它將記錄的事件列印到標準輸出，然後將 JSON 輸出寫入檔。

您可以在[Azure 雲外殼](overview.md)中嘗試此腳本。 請務必使用應用程式 ID、用戶端機密和 Azure AD B2C 租戶的名稱更新它。

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

以下是本文前面顯示的示例活動事件的 JSON 表示形式：

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

您可以自動執行其他管理工作，例如，[使用 Microsoft 圖形管理 Azure AD B2C 使用者帳戶](manage-user-accounts-graph-api.md)。
