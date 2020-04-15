---
title: API 認證與授權 - Azure 時間序列的解解 。微軟文件
description: 本文說明如何為呼叫 Azure Time Series Insights API 的自訂應用程式設定驗證和授權。
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/14/2020
ms.custom: seodec18
ms.openlocfilehash: beefad41a270233336bb9134268c98341e81a7cd
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81380815"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Azure Time Series Insights API 的驗證和授權

本文件介紹如何使用新的 Azure 活動目錄邊欄選項卡在 Azure 活動目錄中註冊應用。 在 Azure 活動目錄中註冊的應用使用戶能夠對與時序見解環境關聯的 Azure 時間序列見解 API 進行身份驗證並授權其使用。

> [!IMPORTANT]
> Azure 時間序列的解支援以下兩個身份驗證函式庫:
> * 較新的[Microsofts 的驗證函式庫 (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview)
> * [Azure 的目錄身分認證函式庫 (ADAL)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)

## <a name="service-principal"></a>服務主體

以下各節介紹如何配置應用程式以代表應用訪問時序見解 API。 然後,應用程式可以使用其自己的應用程式憑據通過 Azure 活動目錄查詢或發佈時序見解環境中的參考數據。

## <a name="summary-and-best-practices"></a>摘要和最佳實務

Azure 活動目錄應用註冊流涉及三個主要步驟。

1. 在 Azure 的目錄中[註冊應用程式](#azure-active-directory-app-registration)。
1. 授權應用程式具有[對時序見解環境的資料存取權限](#granting-data-access)。
1. 使用**應用程式識別**碼和**客戶端金鑰**從`https://api.timeseries.azure.com/`[用戶端應用中](#client-app-initialization)獲取權杖。 此權杖可用來呼叫 Time Series Insights API。

每個**步驟 3**分隔應用程式的和使用者認證允許您:

* 向應用標識分配不同於您自己的許可權的許可權。 一般而言，這些權限只會限制為 App 所需的權限。 例如,您可以允許應用僅從特定的時序見解環境中讀取數據。
* 使用**用戶端密鑰**或安全證書將應用的安全性與創建使用者的身份驗證憑據隔離開來。 因此,應用程式的憑據不依賴於特定使用者的憑據。 如果使用者的角色發生更改,則應用程式不一定需要新的憑據或進一步配置。 如果使用者更改其密碼,則對應用程式的所有訪問不需要新的憑據或密鑰。
* 使用**用戶端機密**或安全證書(而不是特定用戶的認證(要求它們存在)運行無人參與的腳本。
* 使用安全憑證而不是密碼來保護對 Azure 時間序列見解 API 的訪問。

> [!IMPORTANT]
> 在配置 Azure 時序見解安全策略時,請遵循 **「關注點分離**」原則(以上對此方案進行了介紹)。

> [!NOTE]
> * 本文重點介紹一個單租戶應用程式,其中應用程式打算僅在一個組織中運行。
> * 通常,對於在組織中運行的業務線應用程式,通常使用單租戶應用程式。

## <a name="detailed-setup"></a>詳細設定

### <a name="azure-active-directory-app-registration"></a>Azure 活動目錄應用程式註冊

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

### <a name="granting-data-access"></a>授資料存取權限

1. 對於時間序列見解環境,選擇 **「資料存取策略」** 並選擇「**新增**」 。

   [![將新的資料存取原則新增至 Time Series Insights 環境](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. 在 **「選擇使用者」** 對話框中,從 Azure 活動目錄應用註冊部分貼上**應用程式名稱**或**應用程式 ID。**

   [![在 [選取使用者] 對話方塊中尋找應用程式](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. 選擇角色。 選擇 **「讀取器」** 以查詢資料或 **「參與者**」 以查詢資料並更改參考資料。 選取 [確定]  。

   [![選擇使用者角色對話框中選擇讀者或參與者](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. 通過選擇 **「確定」** 保存策略。

   > [!TIP]
   > 對進階資料存取選項,請閱讀[給資料存取](./time-series-insights-data-access.md)。

### <a name="client-app-initialization"></a>用戶端應用初始化

* 開發人員可以使用[Microsoft 驗證函式庫 (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview)或[Azure 活動目錄身分驗證庫 (ADAL)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)使用 Azure 時間序列見解進行身份驗證。

* 例如,要使用 ADAL 進行認證:

   1. 使用 Azure 活動目錄應用註冊部分**的應用程式 ID**和**用戶端密鑰**(應用程式金鑰)代表應用程式獲取權杖。

   1. 在 C# 中,以下代碼可以代表應用程式獲取權杖。 對於完整的範例,請使用[C# 讀取查詢資料](time-series-insights-query-data-csharp.md)。

        [!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs?range=170-199)]

   1. 當應用程式呼叫 Time Series Insights API 時，此權杖即可傳入 `Authorization` 標頭。

* 或者,開發人員可以選擇使用 MSAL 進行身份驗證。 閱讀有關[遷移到 MSAL](https://docs.microsoft.com/azure/active-directory/develop/msal-net-migration)的介紹,並查看 Azure[時間序列見解環境的管理 GA 參考數據,使用 C#](time-series-insights-manage-reference-data-csharp.md)文章瞭解更多資訊。 

## <a name="common-headers-and-parameters"></a>一般標頭和參數

本節介紹用於對時序見解 GA 和預覽 API 進行查詢的常見 HTTP 請求標頭和參數。 [時間序列見解 REST API 參考文檔中](https://docs.microsoft.com/rest/api/time-series-insights/)更詳細地介紹了特定於 API 的要求。

> [!TIP]
> 閱讀[Azure REST API 參考](https://docs.microsoft.com/rest/api/azure/),以瞭解有關如何使用 REST API、發出 HTTP 請求和處理 HTTP 回應的更多資訊。

### <a name="authentication"></a>驗證

要針對[時序見解 REST API](https://docs.microsoft.com/rest/api/time-series-insights/)執行經過身份驗證的查詢,必須在授權標頭中使用您選擇的 REST 用戶端(Postman、JAvaScript、C#)在[授權標頭](/rest/api/apimanagement/2019-12-01/authorizationserver/createorupdate)中傳遞有效的 OAuth 2.0 承載權杖。 

> [!TIP]
> 閱讀託管的 Azure 時間序列見解[用戶端 SDK 範例可視化效果](https://tsiclientsample.azurewebsites.net/),瞭解如何使用[JavaScript 用戶端 SDK](https://github.com/microsoft/tsiclient/blob/master/docs/API.md)以及圖表和圖表以程式設計方式使用時序見解 API 進行身份驗證。

### <a name="http-headers"></a>HTTP 標頭

所需的請求標頭如下所述。

| 需要的要求標頭 | 描述 |
| --- | --- |
| 授權 | 要使用時間序列見解進行身份驗證,必須在**授權**標頭中傳遞有效的 OAuth 2.0 承載權杖。 | 

> [!IMPORTANT]
> 令牌必須完全頒發給`https://api.timeseries.azure.com/`資源(也稱為權杖的"訪問者")。
> * 因此[,您的郵遞員](https://www.getpostman.com/) **AuthURL**將是:`https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?resource=https://api.timeseries.azure.com/`
> * `https://api.timeseries.azure.com/`有效`https://api.timeseries.azure.com`但無效。

下面將介紹可選的請求標頭。

| 選擇性的要求標頭 | 描述 |
| --- | --- |
| Content-Type | 僅`application/json`支援。 |
| x-ms-client-request-id | 用戶端請求 ID。 服務記錄此值。 允許服務跨服務跟蹤操作。 |
| x-ms-用戶端作業階段-id | 用戶端會話 ID。 服務記錄此值。 允許服務跨服務跟蹤一組相關操作。 |
| x-ms-用戶端-應用程式名稱 | 生成此請求的應用程式的名稱。 服務記錄此值。 |

下面將介紹可選但推薦的回應標頭。

| 回應標頭 | 描述 |
| --- | --- |
| Content-Type | 只支援 `application/json`。 |
| x-ms-request-id | 伺服器生成的請求 ID。 可用於聯繫 Microsoft 以調查請求。 |
| x-ms-屬性-找不到行為 | GA API 可選回應標頭。 可能的值是`ThrowError`(預設)`UseNull`或 。 |

### <a name="http-parameters"></a>HTTP 參數

> [!TIP]
> 在[參考文檔中](https://docs.microsoft.com/rest/api/time-series-insights/)尋找有關必需查詢資訊和可選查詢資訊的詳細資訊。

所需的網址 查詢字串參數取決於 API 版本。

| 版本 | 可能的 API 版本值 |
| --- |  --- |
| 正式運作 | `api-version=2016-12-12`|
| 預覽 | `api-version=2018-11-01-preview` |
| 預覽 | `api-version=2018-08-15-preview` |

選擇網址查詢字串參數包括為 HTTP 請求執行時間設定超時。

| 選擇查詢參數 | 描述 | 版本 |
| --- |  --- | --- |
| `timeout=<timeout>` | 用於 HTTP 請求執行的伺服器端超時。 僅適用於[獲取環境事件](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-api)和[獲取環境聚合](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-api)API。 超時值應為 ISO 8601 持續時間`"PT20S"`格式,例如 ,`1-30 s`並且應位於範圍內 。 預設值為 `30 s`。 | GA |
| `storeType=<storeType>` | 對於啟用了暖存儲的預覽環境,可以在`WarmStore`或`ColdStore`上執行查詢。 查詢中的此參數定義應在哪個存儲上執行查詢。 如果未定義,將在冷存儲上執行查詢。 要查詢記憶體儲存,需要儲存**型態**設定為`WarmStore`。 如果未定義,則查詢將針對冷存儲執行。 | 預覽 |

## <a name="next-steps"></a>後續步驟

- 對呼叫 GA 時序見解 API 的範例碼,[請使用 C# 讀取查詢資料](./time-series-insights-query-data-csharp.md)。

- 此預覽時間序列的字串檢視 API 程式,[請使用 C# 讀取查詢預覽資料](./time-series-insights-update-query-data-csharp.md)。

- 有關 API 參考資訊,請閱讀[查詢 API 參考](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api)文檔。

- 瞭解如何[建立服務主體](../active-directory/develop/howto-create-service-principal-portal.md)。
