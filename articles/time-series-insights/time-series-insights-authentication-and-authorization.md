---
title: API 驗證和授權 - Azure 時間序列見解 | Microsoft Docs
description: 本文說明如何為呼叫 Azure Time Series Insights API 的自訂應用程式設定驗證和授權。
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: shresha
manager: dpalled
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/02/2020
ms.custom: seodec18, has-adal-ref
ms.openlocfilehash: 7408e3fb279536f61dd2e5cf1858476da57219d4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91665800"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Azure Time Series Insights API 的驗證和授權

本文件描述如何使用新的 Azure Active Directory 刀鋒視窗，在 Azure Active Directory 中註冊應用程式。 在 Azure Active Directory 中註冊的應用程式可讓使用者進行驗證，並獲得使用與 Azure 時間序列深入解析環境相關聯之 Azure 時間序列深入解析 API 的授權。

## <a name="service-principal"></a>服務主體

下列各節說明如何設定應用程式，以代表應用程式存取 Azure 時間序列深入解析 API。 然後，應用程式可以透過 Azure Active Directory，使用自己的應用程式認證，在 Azure 時間序列深入解析環境中查詢或發佈參考資料。

## <a name="summary-and-best-practices"></a>總結和最佳做法

Azure Active Directory 應用程式註冊流程包含三個主要步驟。

1. 在 Azure Active Directory 中[註冊應用程式](#azure-active-directory-app-registration)。
1. 授權應用程式擁有 [Azure 時間序列深入解析環境的資料存取權](#granting-data-access)。
1. 使用 [應用程式識別碼] 和 [用戶端祕密]，從[用戶端應用程式](#client-app-initialization)中的 `https://api.timeseries.azure.com/` 取得權杖。 然後，權杖可以用來呼叫 Azure 時間序列深入解析 API。

根據**步驟 3**，將應用程式與使用者認證隔開，其可供：

* 將權限指派給不同於自有權限的應用程式身分識別。 一般而言，這些權限只會限制為 App 所需的權限。 例如，您可以允許應用程式只從特定的 Azure 時間序列深入解析環境中讀取資料。
* 使用**用戶端祕密**或安全性憑證，將應用程式的安全性與使用者的驗證認證建立作業隔離。 因此，應用程式其認證不會相依於特定使用者的認證。 如果使用者的角色變更，則應用程式不一定需要新認證或進一步的設定。 如果使用者變更其密碼，則對應用程式的所有存取都不需要新認證或金鑰。
* 使用**用戶端祕密**或安全性憑證而非特定使用者的認證 (需要有這些認證) 來執行自動指令碼。
* 使用安全性憑證而非密碼來保護 Azure 時間序列見解 API 的存取。

> [!IMPORTANT]
> 在設定 Azure 時間序列見解安全性原則時，請遵循**分開考量** (針對上述案例所述) 的準則。

> [!NOTE]

> * 本文著重在說明單一租用戶應用程式，此應用程式的目的是只在一個組織內執行。
> * 您通常會將單一租用戶應用程式用在組織內執行的企業營運應用程式。

## <a name="detailed-setup"></a>詳細設定

### <a name="azure-active-directory-app-registration"></a>Azure Active Directory 應用程式註冊

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

### <a name="granting-data-access"></a>授與資料存取權

1. 針對 Azure 時間序列深入解析環境，請選取 [ **資料存取原則** ]，然後選取 [ **新增**]。

   [![將新的資料存取原則新增至 Azure 時間序列深入解析環境](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. 在 [選取使用者] 對話方塊中，貼上 Azure Active Directory 應用程式註冊區段中的 [應用程式名稱] 或 [應用程式識別碼]。

   [![在 [選取使用者] 對話方塊中尋找應用程式](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. 選取角色。 選取 [讀者] 來查詢資料，或選取 [參與者] 來查詢資料及變更參考資料。 選取 [確定]。

   [![在 [選取使用者角色] 對話方塊中挑選 [讀者] 或 [參與者]](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. 選取 [確定] 來儲存原則。

   > [!TIP]
   > 如需進階的資料存取選項，請參閱[授與資料存取權](./time-series-insights-data-access.md)。

### <a name="client-app-initialization"></a>用戶端應用程式初始化

* 開發人員可以使用 [Microsoft 驗證程式庫 (MSAL) 向 Azure 時間序列深入解析進行驗證。

* 若要使用 MSAL 進行驗證：

   1. 使用 Azure Active Directory 應用程式註冊區段中的 [應用程式識別碼] 和 [用戶端祕密] (應用程式金鑰)，以代表應用程式取得權杖。

   1. 在 C# 中，下列程式碼可代表應用程式來取得權杖。 如需如何從 Gen1 環境查詢資料的完整範例，請參閱 [使用 c # 的查詢資料](time-series-insights-query-data-csharp.md)。

        請參閱 [Azure 時間序列深入解析](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen1-sample/csharp-tsi-gen1-sample/Program.cs)] 存放庫以存取 c # 程式碼。

   1. 然後， `Authorization` 當應用程式呼叫 AZURE 時間序列深入解析 API 時，就可以在標頭中傳遞權杖。

> [!IMPORTANT]
> 如果您使用 [Azure Active Directory Authentication Library (ADAL) ](https://docs.microsoft.com/azure/active-directory/azuread-dev/active-directory-authentication-libraries) 閱讀有關 [遷移至 MSAL](https://docs.microsoft.com/azure/active-directory/develop/msal-net-migration)的資訊。

## <a name="common-headers-and-parameters"></a>一般標頭和參數

本節說明用來對 Azure 時間序列深入解析 Gen1 和 Gen2 Api 進行查詢的一般 HTTP 要求標頭和參數。 API 特定的需求會在 [Azure 時間序列深入解析 REST API 參考檔](https://docs.microsoft.com/rest/api/time-series-insights/)中更詳細地討論。

> [!TIP]
> 請閱讀 [Azure REST API 參考](https://docs.microsoft.com/rest/api/azure/) (英文)，以深入了解如何使用 REST API、提出 HTTP 要求，以及處理 HTTP 回應。

### <a name="authentication"></a>驗證

若要對 [AZURE 時間序列深入解析 REST api](https://docs.microsoft.com/rest/api/time-series-insights/)執行已驗證的查詢，必須使用您選擇的 REST 用戶端，在 [授權標頭](/rest/api/apimanagement/2019-12-01/authorizationserver/createorupdate) 中傳遞有效的 OAuth 2.0 持有人權杖 (Postman、JavaScript、c # ) 。

> [!TIP]
> 閱讀 hosted Azure 時間序列深入解析 [用戶端 sdk 範例視覺效果](https://tsiclientsample.azurewebsites.net/) ，以瞭解如何使用 [JAVASCRIPT 用戶端 sdk](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) 以及圖表和圖形，以程式設計方式驗證 Azure 時間序列深入解析 api。

### <a name="http-headers"></a>HTTP 標頭

必要的要求標頭如下所述。

| 必要的要求標頭 | 描述 |
| --- | --- |
| 授權 | 若要使用 Azure 時間序列深入解析進行驗證，必須在 **授權** 標頭中傳遞有效的 OAuth 2.0 持有人權杖。 |

> [!IMPORTANT]
> 此權杖必須確切核發給 `https://api.timeseries.azure.com/` 資源 (也稱為權杖的「對象」)。

> * 因此， [Postman](https://www.getpostman.com/) **AuthURL** 將會是：`https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?scope=https://api.timeseries.azure.com//.default`
> * `https://api.timeseries.azure.com/` 有效，但 `https://api.timeseries.azure.com` 無效。

選擇性的要求標頭如下所述。

| 選擇性的要求標頭 | 描述 |
| --- | --- |
| Content-Type | 只支援 `application/json`。 |
| x-ms-client-request-id | 用戶端要求識別碼。 服務會記錄此值。 允許服務追蹤跨服務的作業。 |
| x-ms-client-session-id | 用戶端工作階段識別碼。 服務會記錄此值。 允許服務追蹤一組跨服務的相關作業。 |
| x-ms-client-application-name | 產生這個要求的應用程式名稱。 服務會記錄此值。 |

選擇性但建議的回應標頭如下所述。

| 回應標頭 | 描述 |
| --- | --- |
| Content-Type | 只支援 `application/json`。 |
| x-ms-request-id | 伺服器產生的要求識別碼。 可用來與 Microsoft 連絡以調查要求。 |
| x-ms-property-not-found-behavior | GA API 選擇性回應標頭。 可能的值為 `ThrowError` (預設) 或 `UseNull`。 |

### <a name="http-parameters"></a>HTTP 參數

> [!TIP]
> 請在[參考文件](https://docs.microsoft.com/rest/api/time-series-insights/)中尋找必要和選擇性查詢資訊的詳細資訊。

必要的 URL 查詢字串參數取決於 API 版本。

| 版本 | 可能的 API 版本值 |
| --- |  --- |
| Gen1 | `api-version=2016-12-12`|
| Gen2 | `api-version=2020-07-31` 和 `api-version=2018-11-01-preview`|

> [!IMPORTANT]
>
> `api-version=2018-11-01-preview`版本即將淘汰。 我們建議使用者切換至較新的版本。

選擇性 URL 查詢字串參數包括設定 HTTP 要求執行時間的逾時。

| 選擇性的查詢參數 | 描述 | 版本 |
| --- |  --- | --- |
| `timeout=<timeout>` | HTTP 要求執行的伺服器端逾時。 僅適用於[取得環境事件](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/getavailability)和[取得環境彙總](https://docs.microsoft.com/rest/api/time-series-insights/gen1-query-api#get-environment-aggregates-api) API。 逾時值應採用 ISO 8601 持續期間格式 (例如 `"PT20S"`)，且應在 `1-30 s`範圍內。 預設值為 `30 s`。 | Gen1 |
| `storeType=<storeType>` | 針對已啟用暖存放區的 Gen2 環境，可在或上執行查詢 `WarmStore` `ColdStore` 。 這個查詢中參數會定義應執行查詢的存放區。 如果未定義，則會在冷存放區上執行查詢。 若要查詢暖存放區，**storeType** 必須設定為 `WarmStore`。 如果未定義，則會針對冷存放區執行查詢。 | Gen2 |

## <a name="next-steps"></a>後續步驟

* 如需呼叫 Gen1 Azure 時間序列深入解析 API 的範例程式碼，請參閱 [使用 c # 來 Gen1 資料的查詢](./time-series-insights-query-data-csharp.md)。

* 如需呼叫 Gen2 Azure 時間序列深入解析 API 程式碼範例的範例程式碼，請參閱 [使用 c # 來 Gen2 資料的查詢](./time-series-insights-update-query-data-csharp.md)。

* 如需 API 參考資訊，請參閱[查詢 API 參考](https://docs.microsoft.com/rest/api/time-series-insights/gen1-query-api)。

* 了解如何[建立服務主體](../active-directory/develop/howto-create-service-principal-portal.md)。
